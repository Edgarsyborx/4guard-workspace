# SDD: Módulo de Calidad QM (Quality Management) — Backend & Frontend Bridge

> **Módulo:** Calidad & Inocuidad → Control de Calidad (QM)  
> **HUs:** HU-QM-01 (Bloqueos PNC), HU-QM-02 (Dictamen de Liberaciones), HU-QM-03 (Verificación de Carga F01-PO-GC-8.6-03), HU-QM-04 (Reclamos & Costos F01)  
> **Versión:** 1.0.0 (Homologación Hexagonal SDOP — Spec-Driven Oracle-Bridge)  
> **Estado:** 📋 ESPECIFICACIÓN APROBADA (LISTO PARA FASE ORÁCULO E IMPLEMENTACIÓN)  
> **Fecha:** 2026-09-29  
> **Autor:** Lead Architect — SyborX Engineering  
> **Referencia:** [`/docs/gap-analysis/gap-report-quality.md`](file:///c:/Users/kike2/OneDrive/Escritorio/Syborx/4ward/workspace/docs/gap-analysis/gap-report-quality.md) | [ADR-021](file:///c:/Users/kike2/OneDrive/Escritorio/Syborx/4ward/workspace/docs/adr/ADR-021-gestion-calidad-qm-bloqueos-liberaciones-y-f01.md)  

---

## 1. Resumen y Objetivos Arquitectónicos

El módulo de **Calidad (QM)** es el árbitro normativo y sanitario de **4GUARD WMS**. Su propósito es gobernar el ciclo de vida de Producto No Conforme (PNC), autorizar formalmente dictámenes de liberación con trazabilidad documental, digitalizar el protocolo oficial de Verificación de Carga (`F01-PO-GC-8.6-03 Rev. 03`) y cuantificar el impacto operativo/financiero de reclamos e incidencias.

Este documento de diseño (**SDD**) establece la arquitectura técnica bajo el estándar **SDOP (Spec-Driven Oracle-Bridge)**, resolviendo la desconexión total identificada en el Gap Report (donde el Frontend operaba 100% sobre signals en memoria sin persistencia ni endpoints en Spring Boot).

### Principios Rectores SDOP Aplicados:
1. **Spec-Driven (Pilar 1):** Contratos inmutables de API REST, DTOs fuertemente tipados con validaciones Jakarta Bean Validation, identificadores UUID RFC 4122 v4 y marcas temporales UTC (ISO-8601).
2. **Oracle (Pilar 2):** Criterios de aceptación automatizados para pruebas E2E en Playwright y tests de integración en backend antes de desplegar código a producción.
3. **Bridge (Pilar 3):** Desacoplamiento estricto entre UI y fuentes de datos mediante puertos abstractos (`QualityRepository`) y adaptadores concretos (`HttpQualityAdapter.ts`), permitiendo alternar fuentes de datos con cero dependencias duras en componentes.
4. **Reutilización Máxima (Zero-Duplication):** Explotación integral de puertos de salida, servicios y entidades JPA preexistentes en `4guard_be` (`IncidenceEntity`, `InventoryItemEntity`, `InventoryMovementEntity`, `CatBlockReasonEntity`, etc.).

---

## 2. Plan Exhaustivo de Reutilización (Reuse Architecture)

Para evitar redundancias y preservar la integridad transaccional del WMS, el módulo QM **no creará silos paralelos de inventario ni de movimientos**. Se acopla a las estructuras existentes mediante el siguiente plan de reutilización:

```mermaid
graph TD
    subgraph Frontend [4Guard_FE_UI - Angular 19]
        QC[Quality Components] --> QS[QualityStateService]
        QS --> QP[<< Port >> QualityRepository]
        QP --> HQA[HttpQualityAdapter.ts]
    end

    subgraph Backend [4guard_be - Spring Boot Hexagonal]
        HQA -->|REST /api/v1/quality| REST[QualityController]
        REST --> QUC[QualityUseCase / QualityService]
        
        QUC --> QRP[QualityRepositoryPort]
        QUC --> IIRP[InventoryItemRepositoryPort (REUTILIZADO)]
        QUC --> IMRP[InventoryMovementRepositoryPort (REUTILIZADO)]
        QUC --> INRP[IncidenceRepositoryPort (REUTILIZADO)]
        QUC --> IARP[InventoryAuditLogRepositoryPort (REUTILIZADO)]
        QUC --> WORP[WarehouseOutboundRepositoryPort (REUTILIZADO)]
        QUC --> LRP[LocationRepositoryPort (REUTILIZADO)]
    end

    subgraph Persistence [PostgreSQL - Schema wms]
        QRP --> T_REL[wms.quality_releases (NUEVA V35)]
        QRP --> T_VER[wms.load_verifications (NUEVA V35)]
        INRP --> T_INC[wms.incidences (REUTILIZADA)]
        IIRP --> T_ITEM[wms.inventory_items (REUTILIZADA)]
        IMRP --> T_MOV[wms.inventory_movements (REUTILIZADA)]
        IARP --> T_AUD[wms.inventory_audit_log (REUTILIZADA)]
        WORP --> T_OUT[wms.warehouse_outbounds (REUTILIZADA)]
        LRP --> T_LOC[wms.locations (REUTILIZADA)]
    end
```

### 2.1 Entidades JPA a Reutilizar
1. **[`IncidenceEntity`](file:///c:/Users/kike2/OneDrive/Escritorio/Syborx/4ward/workspace/4guard_be/src/main/java/com/fourguard/wms/infrastructure/persistence/entity/IncidenceEntity.java) (`wms.incidences`):**
   - **Rol:** Almacén principal de eventos de PNC y reclamos.
   - **Campos nativos utilizados:** `id` (UUID), `folio` (SERIAL), `item` (FK `InventoryItemEntity`), `type` (`IncidenceType`), `severity` (`IncidenceSeverity`), `reportedBy` (FK `UserEntity`), `status` (`IncidenceStatus`), `createdAt`.
   - **Extensión V35:** Incorporación de columnas para `stage` (etapa operativa), `damaged_qty`, `lost_qty`, `associated_cost` y `currency`.
2. **[`InventoryItemEntity`](file:///c:/Users/kike2/OneDrive/Escritorio/Syborx/4ward/workspace/4guard_be/src/main/java/com/fourguard/wms/infrastructure/persistence/entity/InventoryItemEntity.java) (`wms.inventory_items`):**
   - **Rol:** Representación física y lógica de la tarima o bulto.
   - **Uso:** Al bloquear, conmuta su estado a `InventoryState.IN_QUALITY (20)`. Registra la causa en `quarantine_reason` y criterios específicos en `metadata` (JSONB). Al liberarse, conmuta a `AVAILABLE (30)`, `DAMAGED (60)` o `RETURNED (80)`.
3. **[`InventoryMovementEntity`](file:///c:/Users/kike2/OneDrive/Escritorio/Syborx/4ward/workspace/4guard_be/src/main/java/com/fourguard/wms/infrastructure/persistence/entity/InventoryMovementEntity.java) (`wms.inventory_movements`):**
   - **Rol:** Registro inmutable (Kardex) de movimientos.
   - **Uso:** Asienta formalmente las transacciones utilizando sus enums nativos preexistentes: `MovementType.QUARANTINE` (al retener) y `MovementType.RELEASE` (al dictaminar distribución).
4. **[`CatBlockReasonEntity`](file:///c:/Users/kike2/OneDrive/Escritorio/Syborx/4ward/workspace/4guard_be/src/main/java/com/fourguard/wms/infrastructure/persistence/entity/CatBlockReasonEntity.java) (`wms.cat_block_reasons`):**
   - **Rol:** Catálogo estándar sembrado en Flyway V26 (`BLOQ_CALIDAD`, `BLOQ_CUARENTENA`, `BLOQ_DANIO`, etc.).
   - **Uso:** Suministra las opciones para selectores y tipificación de causas en Angular.
5. **[`InventoryAuditLogEntity`](file:///c:/Users/kike2/OneDrive/Escritorio/Syborx/4ward/workspace/4guard_be/src/main/java/com/fourguard/wms/infrastructure/persistence/entity/InventoryAuditLogEntity.java) (`wms.inventory_audit_log`):**
   - **Rol:** Árbol de la Vida del pallet.
   - **Uso:** Registra hitos forenses (`event_type: 'QM_BLOCK_CREATED'`, `'QM_RELEASE_AUTHORIZED'`).
6. **[`WarehouseOutboundEntity`](file:///c:/Users/kike2/OneDrive/Escritorio/Syborx/4ward/workspace/4guard_be/src/main/java/com/fourguard/wms/infrastructure/persistence/entity/WarehouseOutboundEntity.java) (`wms.warehouse_outbounds`):**
   - **Rol:** Encabezado de transporte y despacho.
   - **Uso:** Fuente de precarga de remisión, rampa, transportista, placas y chofer para el formulario de Verificación de Carga F01.
7. **[`LocationEntity`](file:///c:/Users/kike2/OneDrive/Escritorio/Syborx/4ward/workspace/4guard_be/src/main/java/com/fourguard/wms/infrastructure/persistence/entity/LocationEntity.java) (`wms.locations`):**
   - **Rol:** Gestión de capacidad de bahías de cuarentena (`zone: 'QM'`) e inhabilitación operativa de posiciones bloqueadas.

---

### 2.2 Puertos de Salida (Outbound Ports) a Reutilizar

Ubicados en `com.fourguard.wms.domain.ports.out`, serán inyectados directamente en `QualityService`:

| Puerto Existente | Interfaz / Clase | Métodos Utilizados |
|:---|:---|:---|
| **`IncidenceRepositoryPort`** | [`IncidenceRepositoryPort.java`](file:///c:/Users/kike2/OneDrive/Escritorio/Syborx/4ward/workspace/4guard_be/src/main/java/com/fourguard/wms/domain/ports/out/IncidenceRepositoryPort.java) | `save()`, `findById()`, `findByFolio()`, `findByItemId()` |
| **`InventoryItemRepositoryPort`**| [`InventoryItemRepositoryPort.java`](file:///c:/Users/kike2/OneDrive/Escritorio/Syborx/4ward/workspace/4guard_be/src/main/java/com/fourguard/wms/domain/ports/out/InventoryItemRepositoryPort.java) | `findBySsccOrExternalUa()`, `findByLocationId()`, `save()` |
| **`InventoryMovementRepositoryPort`**| [`InventoryMovementRepositoryPort.java`](file:///c:/Users/kike2/OneDrive/Escritorio/Syborx/4ward/workspace/4guard_be/src/main/java/com/fourguard/wms/domain/ports/out/InventoryMovementRepositoryPort.java) | `save(InventoryMovementEntity)`, `findByItemId()` |
| **`InventoryAuditLogRepositoryPort`**| [`InventoryAuditLogRepositoryPort.java`](file:///c:/Users/kike2/OneDrive/Escritorio/Syborx/4ward/workspace/4guard_be/src/main/java/com/fourguard/wms/domain/ports/out/InventoryAuditLogRepositoryPort.java) | `save()`, `findByPalletCode()`, `findByRemisionFolio()` |
| **`WarehouseOutboundRepositoryPort`**| [`WarehouseOutboundRepositoryPort.java`](file:///c:/Users/kike2/OneDrive/Escritorio/Syborx/4ward/workspace/4guard_be/src/main/java/com/fourguard/wms/domain/ports/out/WarehouseOutboundRepositoryPort.java) | `findById()`, `findByFolio()`, `findByRemisionNo()` |
| **`LocationRepositoryPort`** | [`LocationRepositoryPort.java`](file:///c:/Users/kike2/OneDrive/Escritorio/Syborx/4ward/workspace/4guard_be/src/main/java/com/fourguard/wms/domain/ports/out/LocationRepositoryPort.java) | `decrementOccupancy()`, `incrementOccupancy()`, `findByBranchIdAndCode()` |
| **`NotificationRepositoryPort`** | [`NotificationRepositoryPort.java`](file:///c:/Users/kike2/OneDrive/Escritorio/Syborx/4ward/workspace/4guard_be/src/main/java/com/fourguard/wms/domain/ports/out/NotificationRepositoryPort.java) | `save(NotificationEntity)` para alertas ante bloqueos críticos |

---

### 2.3 Servicios y Reglas Existentes a Reutilizar
1. **`PerformanceAnalyticsService`:** Reutilización del DTO `QualityLocksStatusDto` (`f01ChecklistApprovedCount`, `f01PendingCount`, `allLocksEnforced`) para el cuadro de mando de control de calidad.
2. **Regla de Negocio `RN-OUT-02`:** Las tarimas en estado `IN_QUALITY` causan de inmediato un rechazo `422 Unprocessable Entity` si un operador intenta asociarlas a un despacho outbound.
3. **Seguridad RBAC:** Permisos nativos:
   - `QUALITY_READ`: Consulta de bloqueos, dictámenes y verificaciones.
   - `QUALITY_UPDATE`: Registro de notas técnicas, inspección y checklist.
   - `QUALITY_AUTHORIZE`: Emisión de dictamen de liberación de lote.
   - `QUALITY_CONFIRM`: Firma y cierre formal de Verificación F01.

---

### 2.4 Esquema de Base de Datos y Migración Flyway V35

Se requiere la creación del script Flyway `V35__create_quality_management_schema.sql` con las siguientes definiciones:

```sql
-- Flyway V35: Quality Management Schema
SET search_path TO wms, public;

-- 1. Tabla de Dictámenes de Liberación
CREATE TABLE IF NOT EXISTS wms.quality_releases (
    id                      UUID PRIMARY KEY DEFAULT wms.uuid_generate_v4(),
    organization_id         UUID NOT NULL REFERENCES wms.organizations(id),
    branch_id               UUID NOT NULL REFERENCES wms.branches(id),
    folio                   VARCHAR(30) NOT NULL UNIQUE, -- Ej. LIB-2026-0001
    incidence_id            UUID NOT NULL REFERENCES wms.incidences(id),
    item_id                 UUID NOT NULL REFERENCES wms.inventory_items(id),
    authorizer_type         VARCHAR(30) NOT NULL, -- CLIENT, QUALITY_4GUARD
    support_type            VARCHAR(30) NOT NULL, -- EMAIL, ELECTRONIC_MEDIA, FORMAL_ACT, OTHER
    support_custom_type     VARCHAR(100),
    support_subject         VARCHAR(255) NOT NULL,
    support_file_name       VARCHAR(255),
    authorized_by_name      VARCHAR(150) NOT NULL,
    authorized_by_position  VARCHAR(150) NOT NULL,
    destination             VARCHAR(30) NOT NULL, -- DISTRIBUTION, DESTRUCTION, RETURN
    decision_notes          TEXT NOT NULL,
    released_by_user_id     UUID NOT NULL REFERENCES wms.users(id),
    evidence_metadata       JSONB DEFAULT '[]'::jsonb,
    created_at              TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_qm_releases_org ON wms.quality_releases(organization_id, branch_id);
CREATE INDEX idx_qm_releases_item ON wms.quality_releases(item_id);

-- 2. Tabla de Verificaciones de Carga (F01-PO-GC-8.6-03 Rev. 03)
CREATE TABLE IF NOT EXISTS wms.load_verifications (
    id                      UUID PRIMARY KEY DEFAULT wms.uuid_generate_v4(),
    organization_id         UUID NOT NULL REFERENCES wms.organizations(id),
    branch_id               UUID NOT NULL REFERENCES wms.branches(id),
    folio                   VARCHAR(30) NOT NULL UNIQUE, -- Ej. VER-2026-0001
    control_number          VARCHAR(50) NOT NULL DEFAULT 'F01-PO-GC-8.6-03',
    revision_number         VARCHAR(10) NOT NULL DEFAULT '03',
    outbound_id             UUID REFERENCES wms.warehouse_outbounds(id),
    reception_id            UUID REFERENCES wms.warehouse_receptions(id),
    remision_number         VARCHAR(60) NOT NULL,
    product_description     VARCHAR(255) NOT NULL,
    client_name             VARCHAR(150) NOT NULL,
    verification_date       DATE NOT NULL,
    verification_time       TIME NOT NULL,
    ramp_code               VARCHAR(30) NOT NULL,
    status                  VARCHAR(30) NOT NULL, -- APROBADO, RECHAZADO, ACONDICIONAMIENTO_PENDIENTE, LIMPIEZA_PENDIENTE, EN_PROCESO
    product_criteria        JSONB NOT NULL,       -- Array con 9 criterios IT01 / IT02
    transport_criteria      JSONB NOT NULL,       -- Array con 9 criterios de transporte
    signatures              JSONB NOT NULL,       -- Elaboró, Revisó, Aprobó, Limpieza, Liberación
    general_observations    TEXT,
    evidence_metadata       JSONB DEFAULT '[]'::jsonb,
    created_at              TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP,
    updated_at              TIMESTAMPTZ NOT NULL DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_qm_verif_remision ON wms.load_verifications(remision_number);

-- 3. Extensión de wms.incidences para Soporte Integral de Reclamos F01
ALTER TABLE wms.incidences
    ADD COLUMN IF NOT EXISTS stage VARCHAR(30) DEFAULT 'STORAGE',
    ADD COLUMN IF NOT EXISTS defect_category VARCHAR(40) DEFAULT 'MATERIAL',
    ADD COLUMN IF NOT EXISTS damaged_qty NUMERIC(12,2) DEFAULT 0,
    ADD COLUMN IF NOT EXISTS lost_qty NUMERIC(12,2) DEFAULT 0,
    ADD COLUMN IF NOT EXISTS associated_cost NUMERIC(12,2) DEFAULT 0,
    ADD COLUMN IF NOT EXISTS currency VARCHAR(10) DEFAULT 'MXN',
    ADD COLUMN IF NOT EXISTS observations TEXT,
    ADD COLUMN IF NOT EXISTS criteria_metadata JSONB DEFAULT '[]'::jsonb,
    ADD COLUMN IF NOT EXISTS evidence_metadata JSONB DEFAULT '[]'::jsonb;
```

---

## 3. Modelo de Dominio y Contratos de Puertos Backend

### 3.1 Enums de Dominio Homologados

Para subsanar las brechas de severidad y estado identificadas en el Gap Report:

```java
package com.fourguard.wms.domain.enums;

public enum DetectionStage {
    INBOUND_UNLOAD,
    STORAGE,
    OUTBOUND_LOAD,
    TEST_MATERIAL
}

public enum DefectCategory {
    TRANSPORT,
    DOCUMENTATION,
    MATERIAL,
    SPECIAL_TREATMENT
}

public enum QualitySeverity {
    CRITICAL("RED"),
    WARNING("YELLOW"),
    INFO("BLUE");

    private final String dbValue;
    QualitySeverity(String dbValue) { this.dbValue = dbValue; }
    public String getDbValue() { return dbValue; }
    
    public static QualitySeverity fromDb(String db) {
        if ("RED".equalsIgnoreCase(db)) return CRITICAL;
        if ("YELLOW".equalsIgnoreCase(db)) return WARNING;
        return INFO;
    }
}

public enum ReleaseDestination {
    DISTRIBUTION, // Retorna a AVAILABLE (30)
    DESTRUCTION,  // Pasa a DAMAGED (60)
    RETURN        // Pasa a RETURNED (80)
}

public enum ReleaseAuthorizerType {
    CLIENT,
    QUALITY_4GUARD
}

public enum ReleaseSupportType {
    EMAIL,
    ELECTRONIC_MEDIA,
    FORMAL_ACT,
    OTHER
}

public enum LoadVerificationStatus {
    APROBADO,
    RECHAZADO,
    ACONDICIONAMIENTO_PENDIENTE,
    LIMPIEZA_PENDIENTE,
    EN_PROCESO
}
```

---

### 3.2 Puerto de Salida: `QualityRepositoryPort.java`

Ubicación: `com.fourguard.wms.domain.ports.out.QualityRepositoryPort`

```java
package com.fourguard.wms.domain.ports.out;

import com.fourguard.wms.domain.enums.DetectionStage;
import com.fourguard.wms.domain.enums.IncidenceStatus;
import com.fourguard.wms.domain.enums.LoadVerificationStatus;
import com.fourguard.wms.domain.enums.ReleaseDestination;
import com.fourguard.wms.infrastructure.persistence.entity.IncidenceEntity;
import com.fourguard.wms.infrastructure.persistence.entity.QualityReleaseEntity;
import com.fourguard.wms.infrastructure.persistence.entity.LoadVerificationEntity;

import java.time.LocalDate;
import java.util.List;
import java.util.Optional;
import java.util.UUID;

/**
 * Port OUT — Contrato de persistencia para el módulo de Calidad QM.
 */
public interface QualityRepositoryPort {

    // ── 1. Bloqueos y PNC (Incidences) ─────────────────────────────────────────
    IncidenceEntity saveIncidence(IncidenceEntity entity);
    Optional<IncidenceEntity> findIncidenceById(UUID id);
    Optional<IncidenceEntity> findIncidenceByFolio(Integer folio);
    List<IncidenceEntity> findIncidencesByBranchAndStatus(UUID branchId, IncidenceStatus status);
    List<IncidenceEntity> findIncidencesWithFilters(UUID branchId, DetectionStage stage, IncidenceStatus status);

    // ── 2. Dictámenes de Liberación ───────────────────────────────────────────
    QualityReleaseEntity saveRelease(QualityReleaseEntity entity);
    Optional<QualityReleaseEntity> findReleaseById(UUID id);
    Optional<QualityReleaseEntity> findReleaseByFolio(String folio);
    Optional<QualityReleaseEntity> findReleaseByIncidenceId(UUID incidenceId);
    List<QualityReleaseEntity> findReleasesByBranch(UUID branchId, ReleaseDestination destination);
    String generateNextReleaseFolio(UUID organizationId);

    // ── 3. Verificación de Carga F01 ───────────────────────────────────────────
    LoadVerificationEntity saveVerification(LoadVerificationEntity entity);
    Optional<LoadVerificationEntity> findVerificationById(UUID id);
    Optional<LoadVerificationEntity> findVerificationByFolio(String folio);
    Optional<LoadVerificationEntity> findVerificationByRemision(UUID branchId, String remisionNumber);
    List<LoadVerificationEntity> findVerificationsByBranch(UUID branchId, LoadVerificationStatus status, LocalDate date);
    String generateNextVerificationFolio(UUID organizationId);

    // ── 4. Reclamos e Incidencias ─────────────────────────────────────────────
    List<IncidenceEntity> findClaimsByBranch(UUID branchId, DetectionStage stage, LocalDate startDate, LocalDate endDate);
}
```

---

### 3.3 Puerto de Entrada: `QualityUseCase.java`

Ubicación: `com.fourguard.wms.domain.ports.in.QualityUseCase`

```java
package com.fourguard.wms.domain.ports.in;

import com.fourguard.wms.application.dto.request.quality.*;
import com.fourguard.wms.application.dto.response.quality.*;

import java.util.List;
import java.util.UUID;

public interface QualityUseCase {

    // ── Submódulo 1: Bloqueos y PNC ──
    QualityBlockResponse createBlock(UUID organizationId, UUID branchId, UUID userId, CreateQualityBlockRequest request);
    List<QualityBlockResponse> getActiveBlocks(UUID organizationId, UUID branchId);
    QualityBlockResponse getBlockById(UUID blockId);

    // ── Submódulo 2: Liberaciones y Destinos ──
    QualityReleaseResponse releaseBlock(UUID organizationId, UUID branchId, UUID userId, CreateQualityReleaseRequest request);
    List<QualityReleaseResponse> getReleasesHistory(UUID organizationId, UUID branchId);
    QualityReleaseResponse getReleaseById(UUID releaseId);

    // ── Submódulo 3: Verificación de Carga F01 ──
    LoadVerificationResponse createOrUpdateVerification(UUID organizationId, UUID branchId, UUID userId, SaveLoadVerificationRequest request);
    LoadVerificationResponse getVerificationById(UUID verificationId);
    List<LoadVerificationResponse> getVerifications(UUID organizationId, UUID branchId, String status);

    // ── Submódulo 4: Reclamos y KPIs ──
    QualityClaimResponse createClaim(UUID organizationId, UUID branchId, UUID userId, CreateQualityClaimRequest request);
    List<QualityClaimResponse> getClaims(UUID organizationId, UUID branchId);
    QualityDashboardKpisResponse getDashboardKpis(UUID organizationId, UUID branchId);
}
```

---

## 4. Contratos de la API REST (DTOs y Endpoints)

**Base Path:** `/api/v1/quality`

### 4.1 Catálogo de Endpoints REST

| Método | Endpoint | Permiso RBAC | Descripción Operativa |
|:---|:---|:---|:---|
| `POST` | `/blocks` | `QUALITY_UPDATE` | Bloquear tarima/lote: coloca `IN_QUALITY` y registra movimiento `QUARANTINE`. |
| `GET` | `/blocks` | `QUALITY_READ` | Listar bloqueos activos con soporte de filtros (etapa, cliente, severidad). |
| `GET` | `/blocks/{id}` | `QUALITY_READ` | Consultar detalle de bloqueo, criterios de no conformidad y evidencias. |
| `POST` | `/releases` | `QUALITY_AUTHORIZE` | Dictaminar liberación formal: conmuta inventario según destino y genera Kardex. |
| `GET` | `/releases` | `QUALITY_READ` | Historial de dictámenes emitidos con filtros por destino final. |
| `POST` | `/load-verifications` | `QUALITY_UPDATE` | Guardar o actualizar registro de verificación F01 con 18 criterios normativos. |
| `GET` | `/load-verifications` | `QUALITY_READ` | Listar verificaciones de carga por fecha, estatus o remisión. |
| `GET` | `/load-verifications/{id}`| `QUALITY_READ` | Obtener formato F01 completo para vista o impresión PDF. |
| `POST` | `/claims` | `QUALITY_UPDATE` | Registrar reclamo / merma cuantificada con cálculo financiero. |
| `GET` | `/claims` | `QUALITY_READ` | Listado histórico de reclamos e incidencias de calidad. |
| `GET` | `/dashboard/kpis` | `QUALITY_READ` | KPIs consolidados: bloques activos, liberaciones por destino, costo reclamos. |

---

### 4.2 Definición Exhaustiva de DTOs REST

#### A. Submódulo Bloqueos y PNC

```java
// Request: CreateQualityBlockRequest.java
package com.fourguard.wms.application.dto.request.quality;

import com.fourguard.wms.domain.enums.DefectCategory;
import com.fourguard.wms.domain.enums.DetectionStage;
import com.fourguard.wms.domain.enums.QualitySeverity;
import jakarta.validation.constraints.*;
import lombok.*;

import java.math.BigDecimal;
import java.util.List;
import java.util.UUID;

@Getter @Setter @Builder @NoArgsConstructor @AllArgsConstructor
public class CreateQualityBlockRequest {

    @NotNull(message = "El item_id (tarima) es obligatorio")
    private UUID itemId;

    @NotNull(message = "La etapa de detección es obligatoria")
    private DetectionStage stage;

    @NotNull(message = "La categoría de defecto es obligatoria")
    private DefectCategory defectCategory;

    @NotEmpty(message = "Debe especificar al menos un criterio de defecto")
    private List<String> defectCriteria;

    @NotNull(message = "La severidad es obligatoria")
    private QualitySeverity severity;

    @NotNull(message = "La cantidad retenida es obligatoria")
    @DecimalMin(value = "0.001", message = "La cantidad debe ser mayor a 0")
    private BigDecimal quantity;

    @NotBlank(message = "Las notas/observaciones son obligatorias (mínimo 5 caracteres)")
    @Size(min = 5, max = 1000)
    private String notes;

    private UUID targetLocationId; // Opcional: Reubicación física a bahía QM
    private List<EvidenceFileDto> evidenceFiles;
}
```

```java
// Response: QualityBlockResponse.java
package com.fourguard.wms.application.dto.response.quality;

import com.fourguard.wms.domain.enums.*;
import lombok.*;

import java.math.BigDecimal;
import java.time.OffsetDateTime;
import java.util.List;
import java.util.UUID;

@Getter @Setter @Builder @NoArgsConstructor @AllArgsConstructor
public class QualityBlockResponse {
    private UUID id;
    private String folio; // Ej. BLQ-2026-0012
    private UUID itemId;
    private String sscc;
    private String sku;
    private String skuDescription;
    private String clientName;
    private String batchNumber;
    private BigDecimal quantity;
    private String unitOfMeasure;
    private String locationCode;
    private DetectionStage stage;
    private DefectCategory defectCategory;
    private List<String> defectCriteria;
    private QualitySeverity severity;
    private String status; // BLOCKED, UNDER_INSPECTION, RELEASED
    private String reportedByName;
    private OffsetDateTime reportedAt;
    private String notes;
    private List<EvidenceFileDto> evidenceFiles;
}
```

#### B. Submódulo Liberaciones y Destinos

```java
// Request: CreateQualityReleaseRequest.java
package com.fourguard.wms.application.dto.request.quality;

import com.fourguard.wms.domain.enums.*;
import jakarta.validation.constraints.*;
import lombok.*;

import java.util.List;
import java.util.UUID;

@Getter @Setter @Builder @NoArgsConstructor @AllArgsConstructor
public class CreateQualityReleaseRequest {

    @NotNull(message = "El ID del bloqueo es obligatorio")
    private UUID blockId;

    @NotNull(message = "El tipo de autorizador es obligatorio")
    private ReleaseAuthorizerType authorizerType;

    @NotNull(message = "El tipo de soporte documental es obligatorio")
    private ReleaseSupportType supportType;

    private String supportCustomType;

    @NotBlank(message = "El asunto o referencia de soporte es obligatorio")
    @Size(max = 255)
    private String supportSubject;

    private String supportFileName;

    @NotBlank(message = "El nombre de quien autoriza es obligatorio")
    @Size(max = 150)
    private String authorizedByName;

    @NotBlank(message = "El puesto del autorizador es obligatorio")
    @Size(max = 150)
    private String authorizedByPosition;

    @NotNull(message = "El destino final es obligatorio (DISTRIBUTION, DESTRUCTION, RETURN)")
    private ReleaseDestination destination;

    @NotBlank(message = "El dictamen / notas de decisión son obligatorios")
    @Size(min = 10, max = 2000)
    private String decisionNotes;

    private List<EvidenceFileDto> evidenceFiles;
}
```

```java
// Response: QualityReleaseResponse.java
package com.fourguard.wms.application.dto.response.quality;

import com.fourguard.wms.domain.enums.*;
import lombok.*;

import java.math.BigDecimal;
import java.time.OffsetDateTime;
import java.util.List;
import java.util.UUID;

@Getter @Setter @Builder @NoArgsConstructor @AllArgsConstructor
public class QualityReleaseResponse {
    private UUID id;
    private String folio; // Ej. LIB-2026-0081
    private UUID blockId;
    private String blockFolio;
    private String sku;
    private String description;
    private String batchNumber;
    private String clientName;
    private BigDecimal quantity;
    private String unitOfMeasure;
    private ReleaseAuthorizerType authorizerType;
    private ReleaseSupportType supportType;
    private String supportCustomType;
    private String supportSubject;
    private String supportFileName;
    private String authorizedByName;
    private String authorizedByPosition;
    private ReleaseDestination destination;
    private String decisionNotes;
    private String releasedByUserName;
    private OffsetDateTime releasedAt;
    private List<EvidenceFileDto> evidenceFiles;
}
```

#### C. Submódulo Verificación de Carga F01

```java
// Request: SaveLoadVerificationRequest.java
package com.fourguard.wms.application.dto.request.quality;

import com.fourguard.wms.domain.enums.LoadVerificationStatus;
import jakarta.validation.constraints.*;
import lombok.*;

import java.time.LocalDate;
import java.time.LocalTime;
import java.util.List;
import java.util.UUID;

@Getter @Setter @Builder @NoArgsConstructor @AllArgsConstructor
public class SaveLoadVerificationRequest {

    private UUID id; // Null si es nueva creación
    private UUID outboundId;
    private UUID receptionId;

    @NotBlank(message = "El número de remisión es obligatorio")
    private String remisionNumber;

    @NotBlank(message = "La descripción del producto es obligatoria")
    private String productDescription;

    @NotBlank(message = "El nombre del cliente es obligatorio")
    private String clientName;

    @NotNull(message = "La fecha de verificación es obligatoria")
    private LocalDate date;

    @NotNull(message = "La hora de verificación es obligatoria")
    private LocalTime time;

    @NotBlank(message = "La rampa es obligatoria")
    private String ramp;

    @NotNull(message = "El estatus de verificación es obligatorio")
    private LoadVerificationStatus status;

    @NotEmpty(message = "Los 9 criterios de producto son obligatorios")
    private List<VerificationCriterionDto> productCriteria;

    @NotEmpty(message = "Los 9 criterios de transporte son obligatorios")
    private List<VerificationCriterionDto> transportCriteria;

    @NotNull(message = "El bloque de firmas es obligatorio")
    private VerificationSignaturesDto signatures;

    private String generalObservations;
    private List<EvidenceFileDto> evidencePhotos;
}
```

#### D. Submódulo Reclamos y KPIs

```java
// Request: CreateQualityClaimRequest.java
package com.fourguard.wms.application.dto.request.quality;

import com.fourguard.wms.domain.enums.DetectionStage;
import jakarta.validation.constraints.*;
import lombok.*;

import java.math.BigDecimal;
import java.time.LocalDate;
import java.time.LocalTime;
import java.util.List;
import java.util.UUID;

@Getter @Setter @Builder @NoArgsConstructor @AllArgsConstructor
public class CreateQualityClaimRequest {

    @NotNull(message = "La fecha es obligatoria")
    private LocalDate date;

    @NotNull(message = "La hora es obligatoria")
    private LocalTime time;

    @NotNull(message = "La etapa es obligatoria")
    private DetectionStage stage;

    @NotBlank(message = "El SKU es obligatorio")
    private String sku;

    @NotBlank(message = "La descripción del producto es obligatoria")
    private String productDescription;

    @NotBlank(message = "El cliente es obligatorio")
    private String clientName;

    private String batchNumber;
    private String remisionNumber;

    @NotBlank(message = "El tipo de defecto es obligatorio")
    private String defectType;

    private String defectCustomType;

    @Min(value = 0, message = "Las piezas dañadas no pueden ser negativas")
    private BigDecimal damagedQty;

    @Min(value = 0, message = "Las piezas perdidas no pueden ser negativas")
    private BigDecimal lostQty;

    @DecimalMin(value = "0.0", message = "El costo asociado no puede ser negativo")
    private BigDecimal associatedCost;

    @NotBlank(message = "La moneda es obligatoria (MXN / USD)")
    private String currency;

    @NotBlank(message = "El nombre de quien autoriza es obligatorio")
    private String authorizedByName;

    private String authorizedByPosition;
    private String observations;
    private List<EvidenceFileDto> evidenceFiles;
}
```

---

## 5. Especificación del Puente Frontend: `HttpQualityAdapter.ts`

Conforme al **Pilar 3 de SDOP (Bridge)**, se define el contrato abstracto en TypeScript (`QualityRepository`) y su implementación concreta (`HttpQualityAdapter`), la cual será inyectada en `QualityStateService`:

### 5.1 Puerto Abstracto: `quality.repository.ts`

```typescript
import { InjectionToken } from '@angular/core';
import { Observable } from 'rxjs';
import {
  QualityBlockItem,
  QualityRelease,
  LoadVerification,
  QualityClaim,
  QualityDashboardKpis
} from '../models/quality.models';

export interface CreateBlockPayload {
  itemId: string;
  stage: string;
  defectCategory: string;
  defectCriteria: string[];
  severity: string;
  quantity: number;
  notes: string;
  targetLocationId?: string;
  evidenceFiles?: any[];
}

export interface ReleaseBlockPayload {
  blockId: string;
  authorizerType: string;
  supportType: string;
  supportCustomType?: string;
  supportSubject: string;
  supportFileName?: string;
  authorizedByName: string;
  authorizedByPosition: string;
  destination: string;
  decisionNotes: string;
  evidenceFiles?: any[];
}

export interface QualityRepository {
  // Bloqueos
  getBlocks(stage?: string, status?: string): Observable<QualityBlockItem[]>;
  getBlockById(id: string): Observable<QualityBlockItem>;
  createBlock(payload: CreateBlockPayload): Observable<QualityBlockItem>;

  // Liberaciones
  getReleases(destination?: string): Observable<QualityRelease[]>;
  getReleaseById(id: string): Observable<QualityRelease>;
  releaseBlock(payload: ReleaseBlockPayload): Observable<QualityRelease>;

  // Verificaciones F01
  getVerifications(status?: string, date?: string): Observable<LoadVerification[]>;
  getVerificationById(id: string): Observable<LoadVerification>;
  saveVerification(verification: LoadVerification): Observable<LoadVerification>;

  // Reclamos y KPIs
  getClaims(stage?: string): Observable<QualityClaim[]>;
  createClaim(claim: Partial<QualityClaim>): Observable<QualityClaim>;
  getDashboardKpis(): Observable<QualityDashboardKpis>;
}

export const QUALITY_REPOSITORY_TOKEN = new InjectionToken<QualityRepository>('QUALITY_REPOSITORY_TOKEN');
```

---

### 5.2 Implementación Concreta: `HttpQualityAdapter.ts`

Ubicación proyectada: `/4Guard_FE_UI/apps/admin-console/src/app/features/quality/services/http-quality.adapter.ts`

```typescript
import { Injectable, inject } from '@angular/core';
import { HttpClient, HttpParams } from '@angular/common/http';
import { Observable, catchError, map, throwError } from 'rxjs';
import { environment } from '../../../../../environments/environment';
import {
  QualityRepository,
  CreateBlockPayload,
  ReleaseBlockPayload
} from './quality.repository';
import {
  QualityBlockItem,
  QualityRelease,
  LoadVerification,
  QualityClaim,
  QualityDashboardKpis
} from '../models/quality.models';

@Injectable({
  providedIn: 'root'
})
export class HttpQualityAdapter implements QualityRepository {
  private readonly http = inject(HttpClient);
  private readonly baseUrl = `${environment.apiUrl}/api/v1/quality`;

  // ══════════════════════════════════════════════════════════════════════════
  // 1. BLOQUEOS Y PRODUCTO NO CONFORME (PNC)
  // ══════════════════════════════════════════════════════════════════════════

  getBlocks(stage?: string, status?: string): Observable<QualityBlockItem[]> {
    let params = new HttpParams();
    if (stage && stage !== 'ALL') params = params.set('stage', stage);
    if (status && status !== 'ALL') params = params.set('status', status);

    return this.http.get<QualityBlockItem[]>(`${this.baseUrl}/blocks`, { params }).pipe(
      catchError(this.handleError)
    );
  }

  getBlockById(id: string): Observable<QualityBlockItem> {
    return this.http.get<QualityBlockItem>(`${this.baseUrl}/blocks/${id}`).pipe(
      catchError(this.handleError)
    );
  }

  createBlock(payload: CreateBlockPayload): Observable<QualityBlockItem> {
    return this.http.post<QualityBlockItem>(`${this.baseUrl}/blocks`, payload).pipe(
      catchError(this.handleError)
    );
  }

  // ══════════════════════════════════════════════════════════════════════════
  // 2. DICTAMEN DE LIBERACIONES Y DESTINOS
  // ══════════════════════════════════════════════════════════════════════════

  getReleases(destination?: string): Observable<QualityRelease[]> {
    let params = new HttpParams();
    if (destination && destination !== 'ALL') params = params.set('destination', destination);

    return this.http.get<QualityRelease[]>(`${this.baseUrl}/releases`, { params }).pipe(
      catchError(this.handleError)
    );
  }

  getReleaseById(id: string): Observable<QualityRelease> {
    return this.http.get<QualityRelease>(`${this.baseUrl}/releases/${id}`).pipe(
      catchError(this.handleError)
    );
  }

  releaseBlock(payload: ReleaseBlockPayload): Observable<QualityRelease> {
    return this.http.post<QualityRelease>(`${this.baseUrl}/releases`, payload).pipe(
      catchError(this.handleError)
    );
  }

  // ══════════════════════════════════════════════════════════════════════════
  // 3. VERIFICACIÓN DE CARGA F01-PO-GC-8.6-03
  // ══════════════════════════════════════════════════════════════════════════

  getVerifications(status?: string, date?: string): Observable<LoadVerification[]> {
    let params = new HttpParams();
    if (status && status !== 'ALL') params = params.set('status', status);
    if (date) params = params.set('date', date);

    return this.http.get<LoadVerification[]>(`${this.baseUrl}/load-verifications`, { params }).pipe(
      catchError(this.handleError)
    );
  }

  getVerificationById(id: string): Observable<LoadVerification> {
    return this.http.get<LoadVerification>(`${this.baseUrl}/load-verifications/${id}`).pipe(
      catchError(this.handleError)
    );
  }

  saveVerification(verification: LoadVerification): Observable<LoadVerification> {
    return this.http.post<LoadVerification>(`${this.baseUrl}/load-verifications`, verification).pipe(
      catchError(this.handleError)
    );
  }

  // ══════════════════════════════════════════════════════════════════════════
  // 4. RECLAMOS Y DASHBOARD KPIS
  // ══════════════════════════════════════════════════════════════════════════

  getClaims(stage?: string): Observable<QualityClaim[]> {
    let params = new HttpParams();
    if (stage && stage !== 'ALL') params = params.set('stage', stage);

    return this.http.get<QualityClaim[]>(`${this.baseUrl}/claims`, { params }).pipe(
      catchError(this.handleError)
    );
  }

  createClaim(claim: Partial<QualityClaim>): Observable<QualityClaim> {
    return this.http.post<QualityClaim>(`${this.baseUrl}/claims`, claim).pipe(
      catchError(this.handleError)
    );
  }

  getDashboardKpis(): Observable<QualityDashboardKpis> {
    return this.http.get<QualityDashboardKpis>(`${this.baseUrl}/dashboard/kpis`).pipe(
      catchError(this.handleError)
    );
  }

  // ══════════════════════════════════════════════════════════════════════════
  // MANEJO CENTRALIZADO DE ERRORES HTTP
  // ══════════════════════════════════════════════════════════════════════════

  private handleError(error: any): Observable<never> {
    let errorMessage = 'Ocurrió un error en el servicio de Calidad';
    if (error.error?.message) {
      errorMessage = error.error.message;
    } else if (error.status === 403) {
      errorMessage = 'No tiene permisos suficientes para ejecutar esta acción de calidad (RBAC)';
    } else if (error.status === 422) {
      errorMessage = 'Error de validación o transición inválida de estado en inventario';
    }
    console.error('[HttpQualityAdapter Error]:', error);
    return throwError(() => new Error(errorMessage));
  }
}
```

---

## 6. Máquina de Estados Finita (FSM) y Reglas de Negocio

### 6.1 Matriz de Estados y Transiciones

```
                                  ┌───────────────────────────────┐
                                  │      Lote en Almacén          │
                                  │ (AVAILABLE 30 / RECEIVED 10)  │
                                  └───────────────┬───────────────┘
                                                  │
                                                  │ [RN-QM-01] Bloqueo por Defecto
                                                  ▼
                                  ┌───────────────────────────────┐
                                  │     IN_QUALITY (20)           │
                                  │ (Kardex: QUARANTINE Movement) │
                                  └───────────────┬───────────────┘
                                                  │
                                                  │ Inicio Muestreo / Análisis
                                                  ▼
                                  ┌───────────────────────────────┐
                                  │      UNDER_INSPECTION         │
                                  │ (Inspección QM / Lab Sample)  │
                                  └───────────────┬───────────────┘
                                                  │
                                                  │ [RN-QM-03] Dictamen Formal
                         ┌────────────────────────┼────────────────────────┐
                         │                        │                        │
                         ▼                        ▼                        ▼
               [ Destino: DISTRIBUCIÓN ] [ Destino: DESTRUCCIÓN ] [ Destino: DEVOLUCIÓN ]
                         │                        │                        │
                         ▼                        ▼                        ▼
                  AVAILABLE (30)             DAMAGED (60)            RETURNED (80)
               (Kardex: RELEASE)          (Kardex: ADJUSTMENT)     (Kardex: RETURN)
```

### 6.2 Reglas de Negocio Inmutables (RN)

- **RN-QM-01 (Bloqueo Atómico):** Al emitir un bloqueo en `POST /api/v1/quality/blocks`, la transacción debe ejecutar atómicamente:
  1. Inserción del registro en `wms.incidences` con severidad mapeada al color semafórico.
  2. Actualización de `wms.inventory_items`: `state = 20 (IN_QUALITY)` y `quarantine_reason = request.notes`.
  3. Inserción en `wms.inventory_movements`: `type = 'QUARANTINE'`, registrando usuario y motivo.
  4. Inserción en `wms.inventory_audit_log`: `event_type = 'QM_BLOCK_PLACED'`.
- **RN-QM-02 (Blindaje de Despacho Outbound):** Ningún item en estado `IN_QUALITY` puede ser seleccionado para surtido o despacho. Toda consulta outbound filtra `state = 30 (AVAILABLE)`.
- **RN-QM-03 (Dictamen Formal Obligatorio):** Ninguna tarima puede salir del estado `IN_QUALITY` sin un registro previo en `wms.quality_releases` que contenga: autorizador, tipo de soporte documental y motivo.
- **RN-QM-04 (Conmutación según Destino):**
  - Si `destination == 'DISTRIBUTION'`: `state = 30 (AVAILABLE)`, movimiento `MovementType.RELEASE`.
  - Si `destination == 'DESTRUCTION'`: `state = 60 (DAMAGED)`, movimiento `MovementType.ADJUSTMENT` con baja de stock.
  - Si `destination == 'RETURN'`: `state = 80 (RETURNED)`, movimiento `MovementType.RETURN`.
- **RN-QM-05 (Cumplimiento Instructivos IT01 e IT02 en Carga F01):**
  - Si el criterio `crit-prod-4` (pallets limpios) es marcado como `'NO'`, el estatus de la verificación se fuerza a `'LIMPIEZA_PENDIENTE'` y se exige la firma del responsable de limpieza bajo el instructivo `IT01-PO-GC-8.6-01`.
  - Si cualquiera de los criterios de tarima física (`crit-prod-1`, `2`, `3`, `5`) es `'NO'`, se fuerza a `'ACONDICIONAMIENTO_PENDIENTE'` bajo el instructivo `IT02-PO-GC-8.6-02`.
- **RN-QM-06 (Homologación de Severidades):** La API traduce automáticamente:
  - `CRITICAL` ↔ `RED`
  - `WARNING` ↔ `YELLOW`
  - `INFO` ↔ `BLUE`
- **RN-QM-07 (Inmutabilidad de Folios):** Todos los folios generados (`BLQ-...`, `LIB-...`, `VER-...`, `REC-...`) son únicos por organización y estrictamente ascendentes.

---

## 7. Criterios de Aceptación del Oráculo (Playwright & Integration Tests)

Conforme al **Pilar 2 de SDOP (Oracle)**, la tarea solo se considerará completa cuando pasen al 100% en verde los siguientes oráculos:

### 7.1 Oráculo E2E en Playwright (`/e2e/quality-flow.spec.ts`)
1. **Escenario 1 (Bloqueo y Blindaje):**
   - El usuario inicia sesión como Auditor QM y bloquea la tarima `SSCC-375010203040500018`.
   - Se valida que en la UI la tarjeta aparezca como `BLOCKED` con badge rojo.
   - Se navega al módulo de Despachos Outbound y se verifica que dicha tarima sea rechazada con error de validación `RN-OUT-02`.
2. **Escenario 2 (Dictamen de Liberación):**
   - Desde la pestaña de Liberaciones, se selecciona el bloqueo generado.
   - Se completa el formulario indicando autorizador Cliente, soporte por correo y destino `DISTRIBUTION`.
   - Se confirma el dictamen. Se valida que la tarima pase a `RELEASED` y su stock retorne a estado Disponible en el Kardex.
3. **Escenario 3 (Pauta de Carga F01):**
   - Se captura una verificación de carga marcando pallet sucio (`crit-prod-4 = NO`).
   - Se valida que el sistema impida el dictamen `APROBADO` hasta que se capture la firma del responsable de limpieza bajo `IT01`.
   - Se genera la vista de impresión oficial de la pauta.

### 7.2 Oráculo de Integración Backend (`QualityControllerIntegrationTest.java`)
1. Inserción atómica verificando que `wms.inventory_items` y `wms.inventory_movements` reflejen los cambios sin discrepancias.
2. Rechazo con `403 Forbidden` si un usuario sin el rol `QUALITY_AUTHORIZE` intenta dictaminar una liberación.
3. Rechazo con `422 Unprocessable Entity` si se intenta liberar un lote inexistente o ya liberado.

---

*Especificación viva y vinculante aprobada para el proyecto 4GUARD WMS.*  
*Fin del documento SDD-quality.md.*
