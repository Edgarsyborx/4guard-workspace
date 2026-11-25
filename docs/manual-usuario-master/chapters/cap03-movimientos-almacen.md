## Capítulo 3. Movimientos de Almacén (Recepción, Traspasos y Salidas)

El módulo de **Movimientos de Almacén** constituye el corazón operativo de **4GUARD WMS**. Centraliza el ciclo de vida físico y documental de la mercancía desde que una unidad de transporte cruza la caseta de vigilancia hasta que sus tarimas son descargadas, almacenadas en bahías, reubicadas internamente entre las seis bodegas reales (`A`, `APC`, `AT`, `B`, `BPC`, `BT`) y finalmente embarcadas hacia las plantas destino bajo estricto control **FEFO (Primero en Caducar, Primero en Salir)**.

El módulo está estructurado en tres submódulos interconectados mediante una **Barra de Pestañas Flotante (Segmented Control)** ubicada en la parte superior de la pantalla, lo que permite alternar entre ellos sin perder visibilidad del contexto operativo ni del turno vigente:

1. **1. Recepción** (`/warehouse-movements/receiving`) — Control de arribo, descarga en andén, gestión de lotes, vida útil, logística inversa e ingreso formal a inventario (Formato F01).
2. **2. Cambio de Almacén** (`/warehouse-movements/transfers`) — Reubicación interna, *putaway* desde andén o posición temporal a bahía fija, consolidación de stock y validación de capacidad dinámica.
3. **3. Salidas** (`/warehouse-movements/outbound`) — Despacho Outbound, selección de inventario FEFO con gobernanza de calidad y caducidad, carga RF en andén, sellado y emisión de remisión de salida (Formato F03).

---

### 3.0 Arquitectura Compartida del Módulo: Navegación, Turno Vigente, Permisos y Plano de Andenes

Antes de abordar cada submódulo, es fundamental conocer los elementos transversales que operan de manera homologada en las tres pantallas.

#### Rutas de Acceso y Redirecciones Directas

| Ruta Principal | Redirección Legacy / Alias | Submódulo Destino | Título en Navegador |
| :--- | :--- | :--- | :--- |
| `/warehouse-movements` | — | Redirige automáticamente a `/warehouse-movements/receiving` | 4GUARD WMS — Movimientos de Almacén |
| `/warehouse-movements/receiving` | `/receiving` | Recepción de Mercancía | 4GUARD WMS — Recepción de Mercancía |
| `/warehouse-movements/transfers` | — | Cambio de Almacén (Traspasos) | 4GUARD WMS — Cambio de Almacén |
| `/warehouse-movements/outbound` | `/shipping` | Salidas de Almacén (Outbound) | 4GUARD WMS — Salidas de Almacén |

#### Matriz de Roles, Acceso y Restricciones (RBAC)

El acceso al módulo está protegido por control de roles y segregación de funciones (SoD). Cualquier usuario sin permiso es redirigido automáticamente a su módulo autorizado (`/security`, `/quality` o `/dashboard`).

| Rol en 4GUARD WMS | Acceso al Módulo | Recepción | Cambio de Almacén | Salidas | Acciones de Autorización Crítica |
| :--- | :---: | :---: | :---: | :---: | :--- |
| **Director General / Administrador** | Total | Completo | Completo | Completo | Autoriza cancelaciones extraordinarias, cambios de remisión, reaperturas, edición de tarimas y depuración de entorno. |
| **Gerente de Operaciones / Gerente de Almacén** | Total | Completo | Completo | Completo | Autoriza cierres, excepciones de vida útil corta (MRP), cancelaciones, cambios de remisión y reaperturas. |
| **Coordinador de Operaciones** | Total | Completo | Completo | Completo | Gestión táctica de órdenes, seguimiento de andenes, asignación y consulta de auditoría. |
| **Supervisor de Operaciones / Supervisor de Almacén** | Total | Completo | Completo | Completo | Autoriza cambios de remisión, autorizaciones operativas integrales (retarimado, faltantes, ficha, UAs) y vistos buenos en andén. |
| **Mesa de Control** | Total | Completo | Completo | Completo | Planeación F01/F03, asignación de andenes 1–12 y montacarguistas, captura de lotes, auditoría de UAs y emisión de comprobantes. |
| **Líder de Turno** | Total | Completo | Completo | Completo | Autoriza cierres de recepción y salida mediante el Modal de Autorización de Líder, supervisa cuadrillas, pausas y prioridades urgentes. |
| **Operario de Almacén / Montacarguista** | Parcial | Operativo (RF / Andén) | Restringido a ejecución RF | Restringido a ejecución RF | Escaneo de tarimas/UAs en andén, inicio/cierre de descarga y carga. Sin facultad para cancelar folios ni modificar remisiones. |
| **Guardia de Seguridad / Vigilancia** | Sin acceso | No | No | No | Operación exclusiva en módulo **Caseta de Seguridad** (`/security`). Alimenta automáticamente las pre-recepciones y pre-salidas en estatus *En Caseta*. |
| **Inspector / Ayudante de Calidad** | Sin acceso | No | No | No | Operación exclusiva en módulo **Control de Calidad** (`/quality`). Sus bloqueos PNC impiden el despacho en Salidas y bloquean bahías en Traspasos. |

> **Nota sobre Turno Vigente y Órdenes de Arrastre:** En la esquina superior derecha del encabezado de los tres submódulos se muestra el **Chip de Turno Vigente** (indicando el nombre del turno y horario de la sucursal activa, por ejemplo *Turno Matutino · 06:00–14:00*). Los indicadores KPI se acotan automáticamente a las operaciones del turno vigente más todas las operaciones que permanezcan activas provenientes de turnos anteriores; estas últimas se identifican mediante la etiqueta **Arrastre** para garantizar continuidad operativa en los cambios de turno.

#### Plano Interactivo de Andenes y Rampas (Rampas 1 a 12)

En la barra de herramientas superior de **Recepción** y **Salidas** se ubica la píldora inteligente **Andenes** (que indica en vivo cuántos andenes están ocupados o si *Todos Libres (12)*). Al hacer clic se abre el **Plano de Andenes y Rampas de Descarga / Carga**:

- **Cobertura:** Representa visualmente las **12 rampas físicas** del centro de distribución (`R-01` a `R-12`, códigos `LOC-RAMP-01` a `LOC-RAMP-12`).
- **Estados visuales en tiempo real:**
  - **Disponible (Verde):** Rampa libre y lista para recibir o despachar una unidad.
  - **Ocupada por Recepción Inbound (Ámbar / Azul):** Muestra la ilustración del tráiler acoplado, el folio de recepción, número de remisión, chofer, línea transportista, montacarguista asignado y etapa exacta (*Pre-registro Caseta*, *Andén Asignado*, *En Descarga Inbound*, *Descarga Concluida por Auditar* o *Espera Salida Caseta*).
  - **Ocupada por Carga Outbound (Púrpura / Cian):** Muestra el folio de salida, remisión, chofer, transportista, montacarguista y etapa (*Andén Asignado*, *En Carga Outbound*, *Carga Concluida por Auditar*).
- **Regla de bloqueo físico:** Una rampa permanece ocupada y bloqueada contra doble asignación desde que Mesa de Control o Caseta la asigna a una unidad hasta que la orden se cancela o completa su salida física (Check-Out) en Caseta de Seguridad.
- **Interacción directa:** Hacer clic sobre una rampa ocupada permite abrir directamente el expediente de la operación en curso; hacer clic sobre una rampa libre permite seleccionarla rápidamente durante una asignación.

---

### 3.1 Submódulo 1: Recepción de Mercancía (`/warehouse-movements/receiving`)

![Recepción de Mercancía](screenshots/07-receiving.png)

#### 3.1.1 Propósito y Alcance Operativo

El submódulo de **Recepción de Mercancía** gobierna el flujo integral de entrada de producto terminado, materias primas, empaques y retornos de clientes (Logística Inversa). Conecta el registro inicial de **Caseta de Seguridad** (Formato `F01-PO-CP-7.1.3-03`) con la asignación de andén y montacarguista, el control riguroso de lotes y vida útil remanente, la captura de Unidades de Almacenamiento (UAs / SSCC) por escáner RF o importación Excel, la auditoría administrativa y el ingreso oficial a inventario físico mediante el **Comprobante Oficial de Recepción Formato F01**.

#### 3.1.2 Panel de Métricas Operativas (KPIs)

El panel superior dispone de un botón **Compactar Vista / Expandir Métricas** (su preferencia se guarda por estación de trabajo). En modo expandido muestra **5 tarjetas KPI**; en modo compacto las resume en una barra horizontal de una sola línea:

| Tarjeta KPI | Etiqueta Compacta | Fórmula / Alcance | Interpretación Operativa |
| :--- | :--- | :--- | :--- |
| **TOTAL RECEPCIONES** | `Total` | Total de recepciones del turno vigente + recepciones activas de arrastre | Volumen global bajo control en la consola. |
| **1. REGISTRADAS CASETA** | `1. Caseta` | Recepciones en estatus *Registrada* (`REGISTERED`) | Unidades que ya ingresaron por caseta y esperan asignación de andén (Rampa 1–12). |
| **2-4. EN ANDÉN / DESCARGA** | `2-4. En Andén` | Suma de estatus *Asignada* (`ASSIGNED`), *En Descarga* (`IN_PROGRESS`) y *Por Auditar* (`DISCHARGED`) | Unidades con rampa físicamente ocupada y maniobra activa o pendiente de cierre. |
| **5. COMPLETADAS** | `5. Completadas` | Recepciones en estatus *Completada* (`COMPLETED`) | Mercancía ingresada oficialmente a inventario con Formato F01 cerrado. |
| **CANCELADAS** | `Canceladas` | Recepciones en estatus *Cancelada* (`CANCELLED`) | Unidades rechazadas o revocadas con acta `F01-CANC` y stock en cero. |

#### 3.1.3 Barra de Búsqueda, Filtros y Directorio Ejecutivo

Debajo de los KPIs se despliega la tarjeta **Recepciones Generales** con el contador de órdenes visibles y los controles de filtrado:

- **Buscador Predictivo (`⌘K`):** Permite filtrar en tiempo real por número de folio, nombre del cliente, número de rampa, nombre del chofer, remisión o número de sello de seguridad.
- **Píldoras de Filtro Combinables:**
  - `Todos`: Muestra todas las recepciones del tablero.
  - `En Caseta`: Filtra unidades recién registradas por vigilancia pendientes de rampa.
  - `En Andén`: Filtra unidades asignadas, en descarga física o descargadas por auditar.
  - `Verificado`: Filtra recepciones completadas e ingresadas a stock.
  - `Retornos`: Filtra exclusivamente devoluciones y reingresos de Logística Inversa (`REENTRY`).
  - `Pausadas`: Filtra maniobras detenidas temporalmente por corte de turno o incidencia.
  - `Canceladas`: Filtra expedientes revocados.
  - `Urgentes` (icono de rayo): Filtra órdenes marcadas con prioridad urgente.
  - `Arrastre` (icono de reloj): Filtra operaciones abiertas iniciadas en turnos anteriores.
- **Tarjetas del Directorio Ejecutivo:** Cada tarjeta exhibe el nombre del cliente, folio, fecha/hora de arribo, estatus, línea transportista, chofer, rampa asignada, resumen de tarimas/piezas y una **Barra de Progreso de 5 Fases** (Fase 1 a Fase 5). Cuando la unidad se encuentra en Caseta sin andén asignado, la tarjeta incluye el botón de acceso rápido **Asignar Andén 1–12** para despacharla sin abrir el expediente completo.

#### 3.1.4 Monitor de Rampas y Permanencia en Patio

El submódulo incorpora una vista dedicada al **Monitor de Rampas y Permanencia de Vehículos** con **actualización automática cada 60 segundos**, diseñada para prevenir demoras, cobros por estadía y cuellos de botella en patio:

- **4 Tarjetas de Métricas de Patio:**
  1. **Unidades en Patio:** Total de transportes actualmente dentro de las instalaciones.
  2. **Retraso Espera (>8h):** Conteo de unidades cuyo tiempo acumulado desde el registro en caseta supera **8 horas (480 minutos)**. Se resalta en alerta ámbar/crítica.
  3. **Retraso Descarga (>2.5h):** Conteo de unidades cuya descarga física activa excede **2.5 horas (150 minutos)**. Se resalta en alerta roja crítica.
  4. **Rampas Habilitadas:** Total de andenes configurados en la sucursal (12 rampas).
- **Tabla de Monitor de Patio:** Muestra por cada unidad: *Folio / Chofer*, *Línea y Placas (Tracto y Caja)*, *Estado Patio*, *Rampa*, *Montacarguista / Tipo de Tarima*, *Tiempo de Espera*, *Tiempo de Descarga* y *Acciones directas*.

#### 3.1.5 Expediente Completo (Workbench) Sección por Sección

Al hacer clic en cualquier tarjeta del directorio se abre el **Workbench de Recepción** a ancho completo, organizado en 7 bloques secuenciales:

1. **Cabecera Breadcrumb y Acciones Superiores:**
   - Botón para regresar al directorio, nombre del cliente, folio oficial, etiqueta de estatus, indicador de retorno (`REENTRY`) cuando aplica, conmutador de prioridad **Urgente**, botón de **Árbol de la Vida** (trazabilidad multiciclo), impresión de **Check List de Transporte** (`F01-PO-CP-7.1.3-03`), **Formato F01**, **Exportar Excel**, **Cambio de Remisión**, **Autorización Operativa Integral**, **Pausar/Reanudar** y **Cancelar Recepción**.
2. **Check List de Transporte Unificado (Caseta y Ubicación Actual):**
   - **Fila 1 (5 columnas):** *Proveedor*, *No. Remisión*, *Hora de Llegada*, *Línea Transportadora* y *Chofer / Operador*.
   - **Fila 2 (3 columnas amplias):** *No. Económico y Placas de Tracto y Caja*, *Sellos / Cinchos de Seguridad* y *Ubicación Actual de Almacenaje*.
   - **Gobernanza de Ubicación Actual:**
     - *En proceso:* Permite abrir el **Selector Visual de Bahías** para definir la bahía destino.
     - *Completado en posición temporal (ej. Staging):* Habilita el botón para **Reubicar a Bahía Fija Definitiva**.
     - *Completado en bahía fija:* Se muestra bloqueada como inmutable (cualquier movimiento posterior se realiza en *Cambio de Almacén*).
     - *Cancelado:* Totalmente inactivo sin botones de edición.
3. **Bloque de Asignación de Andén en Recepción (Fase 1 → Fase 2):**
   - Muestra el grid interactivo de **Rampas 1–12** homologado con Caseta, diferenciando rampas libres (verde), ocupadas (con candado y folio) y la rampa seleccionada (dorado). Al seleccionar una rampa libre se bloquea el andén y se notifica a Caseta y al montacarguista.
4. **Sección 2 — Parámetros de Recepción y Hub de Control de Lotes:**
   - **Hub de Control de Lotes en Sesión:** Permite gestionar uno o múltiples lotes dentro de una misma remisión. Muestra un deck de tarjetas por lote (código de lote, fecha de elaboración, fecha de caducidad, semáforo de vida útil remanente y sello de autorización MRP si aplica) y una franja inferior con el lote activo seleccionado para escaneo.
   - **Parámetros Operativos de Descarga (Grid de 12 columnas):**
     - *Fila 1:* **Montacarguista** (autocompletado predictivo con puesto y turno), **Rampa de Recepción** (autocompletado con badge de disponibilidad), **Proveedor** (catálogo predictivo + alta rápida) y **Tipo de Tarima** (7 tipos oficiales).
     - *Fila 2:* **Producto / SKU** (autocompletado con descripción y piezas estándar), **Piezas o Kilos por Tarima** (cantidad base) y **Observaciones**.
5. **Consola Contextual por Fase Operativa:**
   - **Fase 2 (Andén Asignado):** Permite validar parámetros reales y pulsar **Iniciar Descarga Física**.
   - **Fase 3 (Descarga Física en Curso — Terminal Montacarguista):** Habilita la captura de tarimas/UAs por pistola RF o captura manual, selección del lote activo, botón **Guardar Avance de Descarga** (contingencia), botón **Concluir Descarga** (envía a auditoría) y zona de pruebas/simulación de tarima estándar o tarima pico (cantidad parcial).
   - **Fase 4 (Descarga Concluida — Auditoría Administrativa):** Muestra un resumen de alto contraste en 4 chips (*Montacarguista*, *Total Tarimas*, *Total Piezas*, *Destino Inicial Staging*), botón **Previsualizar Borrador** y botón principal **Aprobar e Ingresar a Stock** (dispara la Autorización de Líder y cierra el F01).
   - **Fase 5 (Completada) / Cancelada:** Presenta los parámetros en modo solo lectura; si fue cancelada, despliega el **Banner de Auditoría de Cancelación** con fecha, responsable, autorizador y motivo obligatorio.
6. **Sección 4 — Mapa de Descarga y Tabla de Tarimas Descargadas:**
   - Barra de herramientas con **Totalizadores en Vivo** (tarimas y piezas), botones para **Auditar UAs**, **Re-etiquetar UAs (Nestlé GS1-128)**, **Cargar / Importar Excel**, **Exportar Excel (`INVENTARIOS 4GUARD .XLSX`)** y **Eliminar Seleccionadas / Vaciar UAs**.
   - Tabla detallada con separadores visuales automáticos cuando una misma remisión contiene múltiples productos (SKUs). Muestra: `# Tarima`, `Código UA / SSCC`, `SKU y Descripción`, `Lote`, `Caducidad`, `Remisión`, `Tipo de Tarima`, `Cantidad`, `Ubicación` y acciones individuales (editar o eliminar tarima).
7. **Sección de Control y Auditoría (Colapsable):**
   - Muestra metadatos de captura (quién capturó, líder que autorizó, fecha de creación y cierre) y una **Línea de Tiempo de Auditoría** inmutable y cronológica con todos los eventos del folio (creación, asignación, escaneos, edición de tarimas, cambios de remisión, reingresos, aprobaciones o cancelaciones).

#### 3.1.6 Catálogo Completo de Modales Operativos y de Seguridad en Recepción

| Modal | Cuándo se Usa | Campos, Controles y Reglas de Seguridad |
| :--- | :--- | :--- |
| **Asignación Rápida de Rampa (1–12)** | Desde el Directorio Ejecutivo para unidades *En Caseta*. | Muestra datos de la unidad (folio, cliente, chofer, placas) y la matriz visual `R-01` a `R-12`. Impide elegir rampas ocupadas (muestran candado y folio). Al confirmar, notifica a Caseta y Montacarguista. |
| **Asignación Rápida de Montacarguista** | Para asignar o reasignar el operador responsable de la descarga. | Selector predictivo del catálogo de montacarguistas certificados con clave (`MC-001`, etc.), puesto y turno. |
| **Dar de Alta / Modificar Lote (Hub de Vida Útil)** | Antes o durante la descarga para registrar lotes y validar vigencia. | Campos obligatorios: *Número de Lote*, *Fecha de Caducidad* y opcional *Fecha de Elaboración*. Evalúa automáticamente la vida útil remanente: si la vida total es **< 24 meses**, exige **≥ 75%** remanente; si es **≥ 24 meses**, exige **≥ 50%**. Si incumple el umbral, despliega el panel **Autorización Excepcional MRP / Planeación de la Demanda**, exigiendo: *Usuario/Responsable Autorizador*, *Contraseña/PIN de Autorización*, *Motivo Rápido* (catálogo de 7 motivos oficiales) y *Justificación Formal*. |
| **Selector Visual de Bahías** | Al asignar bahía de almacenaje o al reubicar de posición temporal a fija. | Mapa visual de ocupación en tiempo real por bodega y bahía. Muestra capacidad dinámica (por defecto 22 tarimas), espacios libres y remisiones almacenadas en co-storage. |
| **Reemplazo / Re-etiquetado de UAs (GS1-128)** | Cuando se requiere sustituir etiquetas de proveedor por etiquetas físicas oficiales Nestlé/Cliente. | Soporta dos modos: **1) Escaneo de Etiqueta Física (Nestlé / GS1-128)** con campo de disparo rápido para pistola que parsea automáticamente identificadores `(00)` SSCC, `(10)` Lote y `(15)` Caducidad; y **2) Generar SSCC 4Guard Automático**. Permite seleccionar todas o tarimas específicas y exige registrar el *Motivo del Re-etiquetado en Bitácora*. |
| **Auditoría y Verificación de UAs Escaneadas** | Antes del cierre definitivo para validar integridad de códigos escaneados. | Presenta barra de métricas de coincidencia/discrepancias y tabla desplazable con cada UA, estado de verificación, lote, piezas y anomalías detectadas. |
| **Edición de Tarima Individual** | Para corregir una tarima puntual (solo Administrador / Supervisor). | Permite ajustar cantidad de piezas/kilos, código UA, lote, fecha de caducidad, tipo de tarima y observaciones dejando rastro en auditoría. |
| **Modificar Ficha Operativa de Caseta** | Para corregir datos vehiculares o documentales recibidos de Caseta. | Permite editar *No. Remisión*, *Proveedor*, *Línea Transportista*, *Hora de Llegada*, *Chofer*, *Placas y No. Económico de Tracto y Caja*, y agregar/eliminar *Sellos de Seguridad*. Si la recepción ya avanzó de fase, exige escalamiento mediante autorización de supervisor. |
| **Autorización de Líder** | Obligatorio para aprobar el ingreso a stock (cierre F01) y operaciones protegidas. | Solicita **Usuario del Líder** y **Contraseña** autorizada. Registra el nombre oficial del líder validador en el expediente y en el comprobante impreso. |
| **Autorización Operativa Integral (4 Modalidades)** | Para intervenciones extraordinarias sobre recepciones en curso o cerradas. | Incluye selector de 4 modalidades, resumen del folio, *Justificación de Auditoría Obligatoria* y **Credenciales de Supervisor / Administrador**:<br>• **Opción A — Retarimado Físico (Pallet Swap):** Compara tipo de tarima origen vs. destino, permite aplicar a *Todo el embarque* o *Tarimas seleccionadas* y registrar motivo rápido.<br>• **Opción B — Tarimas Faltantes (Descarga Complementaria):** Muestra tarjeta de cupo en tiempo real de la bahía asignada para validar espacio antes de reabrir descarga complementaria.<br>• **Opción C — Modificar Ficha de Caseta:** Habilita la corrección controlada de datos de transporte/chofer/sellos.<br>• **Opción D — Cambio / Homologación de UAs del Cliente (Pistola RF):** Tabla ergonómica de escaneo fila por fila con salto automático de foco y bloque colapsable de carga masiva. |
| **Cambio de No. de Remisión** | Cuando el documento físico presenta error de captura en el número de remisión. | Muestra resumen actual, campo *Nuevo No. de Remisión* (validado diferente al actual), *Motivo Obligatorio* y **Credenciales de Supervisor / Administrador**. |
| **Cancelación Extraordinaria** | Para rechazar una unidad o revocar una recepción completa. | Muestra resumen de tarimas/piezas a revocar, exige *Motivo de Cancelación Obligatorio* y **Credenciales de Administrador**. Revierte el stock a cero, libera la rampa y habilita la impresión del acta `F01-CANC`. |
| **Árbol de la Vida (Trazabilidad Multiciclo)** | En cualquier recepción (especialmente relevante en Retornos / Logística Inversa). | Despliega una línea de tiempo inmutable con todos los eventos históricos de la remisión a través de sus ciclos (`Despacho ➔ Retorno ➔ Recepción ➔ Calidad`), mostrando fecha/hora, tipo de evento, descripción, UA, ubicación y operador. |
| **Alta Rápida de Catálogo** | Cuando un proveedor, línea transportista o entidad no existe en el desplegable. | Permite dar de alta rápida la entidad sin abandonar el expediente de recepción. |
| **Depurar Entorno de Pruebas** | Exclusivo para ambientes de capacitación o pruebas controladas. | Solicita confirmación explícita para limpiar recepciones, salidas, traspasos y patio de prueba, restableciendo contadores a cero. |

#### 3.1.7 Logística Inversa y Detección Automática de Retornos (F01-R)

Cuando una remisión o UA que anteriormente fue despachada en **Salidas** regresa al centro de distribución por rechazo del cliente o devolución, el sistema activa automáticamente el flujo de **Logística Inversa (Reingreso)**:

1. Al capturar el documento o escanear una UA previamente despachada, el sistema detecta la coincidencia y muestra la notificación de retorno vinculado al folio de salida original.
2. El expediente se etiqueta como **Retorno (`REENTRY`)** y carga la lista de **Tarimas Esperadas de Retorno** (UA, lote, SKU, piezas, caducidad y tipo de tarima).
3. Durante la descarga en andén, cada escaneo valida la UA contra el manifiesto esperado, clasificando cada lectura como **Verificada**, **Ya Verificada** o **Discrepancia**, actualizando contadores de *Verificadas*, *Esperadas* y *Restantes*.
4. Al concluir y autorizar, se genera el cierre de **Reingreso Formal / Devolución Concluida (F01-R)** y se enlaza permanentemente al **Árbol de la Vida** de la remisión.

#### 3.1.8 Impresión de Comprobantes y Exportación Excel en Recepción

- **Comprobante Oficial de Recepción de Mercancía — Formato F01:** Documento institucional con encabezado 4GUARD WMS, folio, fecha/hora, datos completos de transporte (línea, chofer, placas, números económicos de tracto y caja, sellos, rampa, montacarguista), cliente, desglose tabular de todas las tarimas/UAs con remisión, SKU, descripción, lote, caducidad y piezas, totalizadores y **3 columnas de firmas oficiales** (*Entregó: Chofer / Transportista*, *Descargó: Montacarguista* y *Validó: Mesa de Control / Líder*). Ajusta automáticamente su densidad tipográfica (*normal* o *ultra-compacta*) según el volumen de tarimas.
- **Hoja de Cancelación y Revocación de Recepción — Formato F01-CANC:** Acta oficial de revocación con motivo de cancelación, listado de UAs anuladas y **2 bloques de firmas** (*Mesa de Control / Seguridad Validada* y *Notificación de Rechazo / Chofer Transportista*).
- **Formato Check List de Transporte — `F01-PO-CP-7.1.3-03`:** Replicación fiel del formato de Seguridad Patrimonial con datos vehiculares, checklist de EPP, checklist de documentos, revisión física de la unidad y **2 firmas** (*Vigilante / Caseta* y *Firma Digital Autorizada o Autógrafa del Chofer*).
- **Exportación e Importación Excel (`INVENTARIOS 4GUARD .XLSX`):** Genera un libro corporativo con tres pestañas (`ALMACEN A`, `ALMACEN B` y `Hoja 2`) y 10 columnas estandarizadas (`#`, `ALMACEN`, `UBICACION`, `UA`, `SKU`, `DESCRIPCION`, `REMISIÓN`, `FECHA DE CADUCIDAD`, `Ubicacion sys`, `NIVEL DE ESTIBA`). También permite importar masivamente tarimas desde un archivo Excel hacia el Mapa de Descarga.

#### 3.1.9 Diccionario de Estados de Recepción (Máquina de Estados)

| Estado | Etiqueta en UI | Transiciones Permitidas | Quién Autoriza / Ejecuta | Efecto en Inventario y Andenes |
| :--- | :--- | :--- | :--- | :--- |
| `REGISTERED` | **En Caseta / Registrada** | ➔ `ASSIGNED`<br>➔ `CANCELLED` | Caseta de Seguridad (crea)<br>Mesa de Control / Supervisor (asigna) | Sin afectación a inventario. Unidad en patio; inicia conteo de alerta de espera (> 8h). Sin rampa bloqueada. |
| `ASSIGNED` | **Andén Asignado** | ➔ `IN_PROGRESS`<br>➔ `PAUSED`<br>➔ `CANCELLED` | Mesa de Control / Líder / Montacarguista | Sin afectación a inventario. **Bloquea físicamente la rampa asignada (1–12)** contra otras operaciones. |
| `IN_PROGRESS` | **En Descarga** | ➔ `DISCHARGED`<br>➔ `PAUSED`<br>➔ `CANCELLED` | Montacarguista / Mesa de Control | Tarimas escaneadas registradas en sesión de descarga (aún no disponibles para surtido). Inicia conteo de alerta de descarga (> 2.5h). Rampa bloqueada. |
| `PAUSED` | **Pausada** | ➔ `IN_PROGRESS`<br>➔ `CANCELLED` | Líder de Turno / Supervisor / Mesa de Control | Preserva avance de tarimas escaneadas y motivo de pausa (corte de turno o incidencia). Rampa permanece reservada. |
| `DISCHARGED` | **Por Auditar (Descargada)** | ➔ `COMPLETED`<br>➔ `IN_PROGRESS` (reapertura)<br>➔ `CANCELLED` | Montacarguista (concluye)<br>Líder de Almacén (aprueba cierre) | Descarga física concluida; detenida en espera de auditoría de UAs y visto bueno administrativo. Rampa sigue ocupada. |
| `COMPLETED` | **Completada / Verificado** | ➔ Intervención vía *Autorización Operativa Integral* o *Cambio de Remisión*<br>➔ `CANCELLED` (extraordinaria) | Líder de Almacén (credenciales obligatorias) | **Ingresa formalmente todas las UAs al inventario disponible** en la bahía asignada (o staging). Emite Formato F01. La rampa se libera al Check-Out en Caseta. |
| `CANCELLED` | **Cancelada** | Estado terminal (inmutable) | Administrador / Gerente (credenciales + motivo obligatorio) | **Revierte inventario a cero** (anula UAs registradas), libera inmediatamente la rampa asignada y genera acta `F01-CANC`. |

#### 3.1.10 Procedimientos Paso a Paso (SOPs) de Recepción

##### SOP-REC-01: Recepción Estándar de Mercancía (De Caseta a Stock F01)
1. **Localizar la unidad:** Ingrese a **1. Recepción** y ubique en el Directorio la tarjeta con estado *En Caseta* (puede filtrar con la píldora `En Caseta` o buscar por folio/placas con `⌘K`).
2. **Asignar andén (Rampa 1–12):** Haga clic en **Asignar Andén 1–12** en la tarjeta (o abra el expediente y seleccione una rampa libre en el grid). Verifique que el estado cambie a *Andén Asignado* y el indicador superior de Andenes refleje la ocupación.
3. **Configurar parámetros de descarga:** Dentro del expediente, en la **Sección 2**, seleccione el **Montacarguista**, verifique **Proveedor**, **Tipo de Tarima**, **Producto (SKU)** y **Piezas por Tarima**.
4. **Registrar lote y validar vida útil:** En el **Hub de Control de Lotes**, haga clic en agregar lote, capture el *Código de Lote*, *Fecha de Elaboración* y *Fecha de Caducidad*.
   - Si el porcentaje de vida útil cumple el umbral (≥ 75% para productos < 24 meses; ≥ 50% para ≥ 24 meses), guarde directamente.
   - Si aparece la alerta de **Vida Útil Remanente Corta**, solicite al responsable de MRP / Planeación / Gerencia que ingrese sus credenciales, seleccione el motivo oficial y capture la justificación para habilitar el lote.
5. **Asignar bahía de almacenaje:** En el bloque de *Ubicación Actual*, abra el **Selector Visual de Bahías** y elija la bahía destino con capacidad disponible.
6. **Iniciar descarga y capturar UAs:** Pulse **Iniciar Descarga Física**. Escanee cada tarima con la pistola RF (o importe el archivo Excel desde el Mapa de Descarga). Si el producto requiere etiquetas físicas oficiales, ejecute el modal **Re-etiquetado de UAs (GS1-128)**.
7. **Concluir descarga y auditar:** Al terminar la última tarima, pulse **Concluir Descarga**. El expediente pasará a fase *Por Auditar*. Revise los totalizadores en los 4 chips de auditoría y ejecute **Auditar UAs** para confirmar cero discrepancias.
8. **Aprobar ingreso a stock e imprimir F01:** Haga clic en **Aprobar e Ingresar a Stock**. El **Líder de Almacén** deberá ingresar su usuario y contraseña en el modal de autorización. Al validar, las tarimas ingresan al inventario disponible y se habilita la impresión del **Formato F01**.

##### SOP-REC-02: Recepción de Retorno / Logística Inversa (F01-R)
1. Ingrese al expediente del transporte de retorno (identificado con la insignia **Retorno / REENTRY** o detectado automáticamente al capturar la remisión original).
2. Verifique la vinculación con el folio de salida origen y consulte las **Tarimas Esperadas de Retorno**.
3. Asigne rampa y montacarguista, inicie la descarga y escanee cada UA devuelta para validar coincidencia exacta contra el despacho previo.
4. Si existe alguna discrepancia, documéntela en las observaciones antes de cerrar.
5. Solicite la autorización del Líder para emitir el cierre **F01-R** y verifique en el modal **Árbol de la Vida** que el ciclo `Despacho ➔ Retorno ➔ Recepción` haya quedado registrado.

---

### 3.2 Submódulo 2: Cambio de Almacén / Traspasos (`/warehouse-movements/transfers`)

![Cambio de Almacén y Traspasos](screenshots/08-transfers.png)

#### 3.2.1 Propósito y Alcance Operativo

El submódulo de **Cambio de Almacén (Traspasos)** gestiona la reubicación interna controlada de tarimas/UAs entre las **6 bodegas reales** del almacén (`A`, `APC`, `AT`, `B`, `BPC`, `BT`), incluyendo el *putaway* desde zonas temporales de andén (`STAGING-R01` a `STAGING-R12`) hacia racks definitivos, la liberación de bahías y la **consolidación inteligente de inventario**. Garantiza trazabilidad completa de origen a destino y valida en vivo la capacidad física de cada posición (capacidad global por defecto de **22 tarimas por bahía**, ajustable dinámicamente por el Administrador en el layout).

#### 3.2.2 Panel de Métricas Operativas (KPIs)

Cuenta con botón **Compactar Vista / Expandir Métricas** (persistente por estación) y **4 tarjetas KPI centradas**:

| Tarjeta KPI | Etiqueta Compacta | Fórmula / Alcance | Interpretación Operativa |
| :--- | :--- | :--- | :--- |
| **TOTAL TRASPASOS** | `Total Traspasos` | Total de movimientos de traspaso del turno vigente + activos de arrastre | Volumen total de reubicaciones registradas. |
| **BAHÍAS CON STOCK** | `Bahías con Stock` | Posiciones físicas y zonas temporales con ≥ 1 tarima almacenada | Cantidad de ubicaciones disponibles como *Origen* de traspaso. |
| **BAHÍAS EN CEROS** | `Bahías en Ceros` | Posiciones con 0 tarimas y sin bloqueo de Calidad/Mantenimiento | Bahías completamente vacías y listas para *putaway* inmediato. |
| **UAS REUBICADAS** | `UAs Reubicadas` | Suma de tarimas transferidas en los movimientos del tablero | Productividad física del equipo de montacargas en reubicaciones. |

#### 3.2.3 Barra de Búsqueda, Filtros y Directorio Ejecutivo

La tarjeta **Consultas de Cambio de Almacén & Traspasos** muestra el contador de movimientos y los siguientes controles:

- **Botón Principal (`+ Nuevo Traspaso`):** Ubicado en el extremo derecho de la barra de pestañas superior; abre el formulario de creación de traspaso.
- **Buscador Predictivo (`⌘K`):** Filtra por folio (`CAM-YYYY-XXXXXX`), bahía origen, bahía destino, montacarguista asignado o motivo de traspaso.
- **Píldoras de Filtro Combinables:**
  - `Todos`: Todos los traspasos del turno y arrastre.
  - `Asignados RF`: Traspasos asignados a terminal de montacargas pendientes de iniciar.
  - `En Maniobra`: Traspasos en ejecución activa (`IN_PROGRESS` / `ASSIGNED`).
  - `Completados`: Reubicaciones finalizadas y reflejadas en inventario.
  - `Pausados`: Movimientos detenidos temporalmente.
  - `Cancelados`: Traspasos revocados.
  - `Urgentes` (icono de rayo): Prioridad urgente.
  - `Arrastre` (icono de reloj): Movimientos abiertos heredados de turnos previos.
- **Tarjetas Ejecutivas de Traspaso:** Cada tarjeta presenta la ruta visual `Origen ➔ Destino`, folio, hora, estatus, montacarguista, total de tarimas y piezas, y el motivo de reubicación.

#### 3.2.4 Formulario de Nuevo Traspaso (Workbench de Creación en 5 Secciones)

Al hacer clic en **Nuevo Traspaso**, el área de trabajo despliega un flujo guiado en **5 secciones secuenciales** con validación en vivo:

1. **Sección 1 — Asignación de Montacarguista Certificado y Estatus:**
   - Buscador predictivo de montacarguistas con indicador automático de disponibilidad operativa en tiempo real:
     - **Disponible:** Libre para recibir la maniobra.
     - **En Recepción:** Actualmente asignado a una descarga activa.
     - **En Despacho:** Actualmente asignado a una carga Outbound.
     - **Inactivo:** Operador fuera de servicio.
   - Tarjeta de metadatos del operador seleccionado (clave, puesto y turno).
2. **Sección 2 — Bahía Origen y Selección de Tarimas:**
   - Selector predictivo de **Bahías con Stock** (incluye botón para abrir el **Selector Visual de Bahías Origen**).
   - Tarjeta de metadatos de la bahía origen (bodega/zona, ocupación actual y total de piezas).
   - **Controles de Selección de Tarimas:**
     - *Selector rápido numérico:* Permite indicar cuántas tarimas mover y seleccionarlas automáticamente.
     - Botones **Seleccionar Todas** y **Deseleccionar Todas**.
     - **Tabla interactiva con checkboxes** mostrando: *ID Tarima*, *Código UA*, *Lote*, *Caducidad*, *Remisión*, *Producto / SKU* y *Cantidad*.
3. **Sección 3 — Bahía Destino (Validación En Ceros y Consolidación Dinámica):**
   - Selector predictivo de **Bahía Destino** (incluye botón para abrir el **Selector Visual de Bahías Destino** con capacidad dinámica).
   - **Banner Inteligente de Validación de Destino (5 escenarios automáticos):**
     - **Bahía Bloqueada (Rojo):** Ubicación retenida por Calidad o Mantenimiento; bloquea la operación.
     - **Bahía Destino Disponible — En Ceros (Verde):** Posición vacía (`0 UAs`) con toda su capacidad libre para *putaway*.
     - **Consolidación de Bahía Autorizada (Turquesa):** La bahía ya tiene inventario pero cuenta con espacio suficiente (`Stock Actual + Tarimas a Mover ≤ Capacidad Máxima`). Indica si se trata del **mismo SKU** (consolidación homogénea) o **consolidación multi-producto autorizada por capacidad**.
     - **Capacidad Excedida (Rojo):** Bloquea el traspaso indicando exactamente por cuántas tarimas se rebasa la capacidad física de la bahía.
     - **Bahía con Stock Previo (Ámbar):** Solicita seleccionar primero las tarimas en el Paso 2 para recalcular si caben en los espacios libres.
4. **Sección 4 — Motivo de Traspaso y Observaciones:**
   - Desplegable obligatorio con el **Catálogo Oficial de 7 Motivos de Traspaso** (detallado en la sección 3.2.7) y campo de *Observaciones* (obligatorio cuando se elige *Otro motivo*).
5. **Sección 5 — Revise que la Operación Sea Correcta (Checklist de Pre-Validación en Vivo):**
   - Valida con indicadores verdes/pendientes 5 requisitos obligatorios antes de habilitar el botón de confirmación:
     1. *Montacarguista asignado*
     2. *Bahía Origen seleccionada*
     3. *Cantidad de tarimas seleccionada (≥ 1 UA)*
     4. *Bahía Destino válida (En Ceros o Consolidación dentro de capacidad y no bloqueada)*
     5. *Motivo de Traspaso especificado*
   - Muestra los **Totalizadores en Vivo** (tarimas, piezas y SKUs distintos) y el botón **Confirmar Traspaso**.

#### 3.2.5 Consulta Detallada de Traspaso (Workbench de Lectura y Auditoría)

Al seleccionar una tarjeta del directorio se abre la vista detallada con:
- **Cabecera Breadcrumb** (`Origen ➔ Destino`), folio, estatus y acciones superiores (**Imprimir Comprobante**, **Cancelar Traspaso** si aplica).
- **Sección 1 — Información General del Movimiento:** Matriz visual `Origen ➔ Destino`, montacarguista, fecha/hora, motivo, observaciones y, en caso de revocación, la **Alerta de Cancelación** con responsable y justificación.
- **Tabla de Tarimas Reubicadas:** Detalle completo de cada UA transferida.
- **Metadatos de Auditoría y Trazabilidad:** Línea de tiempo cronológica colapsable con cada evento del folio.

#### 3.2.6 Modales Operativos, de Seguridad e Impresión en Traspasos

| Modal | Propósito y Validaciones |
| :--- | :--- |
| **Confirmar Cambio de Almacén** | Resumen final de ruta (`Origen ➔ Destino`), montacarguista, motivo, total de tarimas y piezas antes de ejecutar el movimiento en inventario. |
| **Confirmación de Éxito y Pregunta de Impresión** | Tras completar el traspaso, informa el folio generado y pregunta si se desea imprimir inmediatamente el comprobante oficial (*Sí / No*). |
| **Selector Visual de Bahías Origen (Con Stock)** | Mapa interactivo filtrado para seleccionar ubicaciones con inventario físico disponible. |
| **Selector Visual de Bahías Destino (Capacidad Dinámica)** | Mapa interactivo que muestra ocupación actual vs. capacidad máxima de cada bahía en las 6 bodegas para elegir bahía en ceros o consolidación. |
| **Vista Previa e Impresión de Comprobante de Cambio de Almacén** | Genera el **Comprobante Oficial de Cambio de Almacén** (o la **Hoja de Cancelación de Cambio de Almacén** si el folio fue cancelado), con tabla de UAs, ruta, motivo y bloque de firmas de responsables. |
| **Cancelar Traspaso** | Modal de seguridad con resumen del folio, **Motivo de Cancelación Obligatorio** y **Credenciales de Administrador** para revocar el movimiento y restablecer las tarimas a su ubicación origen. |

#### 3.2.7 Catálogo Oficial de Motivos de Traspaso

| Clave | Motivo de Traspaso | Descripción Operativa | ¿Requiere Observación Obligatoria? |
| :--- | :--- | :--- | :---: |
| `OPT_ESPACIO` | **Optimización de espacio** | Reorganización de ubicaciones para mejorar el aprovechamiento volumétrico. | No |
| `REUB_OPERATIVA` | **Reubicación operativa** | Movimiento preventivo o preparativo para surtido y despacho. | No |
| `LIB_BAHIA` | **Liberación de bahía** | Desocupación de bahía para recepción, auditoría o mantenimiento. | No |
| `CONSOLIDACION` | **Consolidación de inventario** | Agrupación de lotes y UAs compatibles en una sola posición. | No |
| `SOL_CLIENTE` | **Solicitud del cliente** | Instrucción directa del cliente para segregar o trasladar mercancía. | No |
| `INCIDENCIA` | **Incidencia / Desvío de calidad** | Aislamiento temporal de tarimas por inspección de calidad QM. | No |
| `OTRO` | **Otro motivo (especificar)** | Motivo extraordinario no catalogado. | **Sí** |

#### 3.2.8 Diccionario de Estados de Cambio de Almacén (Máquina de Estados)

| Estado | Etiqueta en UI | Transiciones Permitidas | Quién Autoriza / Ejecuta | Efecto en Inventario y Capacidad de Bahías |
| :--- | :--- | :--- | :--- | :--- |
| `DRAFT` | **Borrador** | ➔ `ASSIGNED` / `CONFIRMED` / `COMPLETED`<br>➔ `CANCELLED` | Mesa de Control / Supervisor | Sin afectación a inventario. |
| `ASSIGNED` | **Asignado RF** | ➔ `IN_PROGRESS`<br>➔ `PAUSED`<br>➔ `CANCELLED` | Mesa de Control / Montacarguista | Orden enviada a la cola de trabajo del montacarguista asignado. |
| `IN_PROGRESS` | **En Maniobra** | ➔ `COMPLETED`<br>➔ `PAUSED`<br>➔ `CANCELLED` | Montacarguista / Líder de Turno | Tarimas en tránsito físico entre bahía origen y destino. |
| `PAUSED` | **Pausado** | ➔ `IN_PROGRESS`<br>➔ `CANCELLED` | Líder de Turno / Supervisor | Maniobra detenida temporalmente con registro de motivo; conserva la asignación. |
| `CONFIRMED` / `COMPLETED` | **Completado** | ➔ `CANCELLED` (extraordinaria) | Mesa de Control / Montacarguista | **Descuenta las UAs de la Bahía Origen y las acredita en la Bahía Destino.** Actualiza automáticamente el conteo de *Bahías en Ceros* y *Bahías con Stock*. |
| `CANCELLED` | **Cancelado** | Estado terminal (inmutable) | Administrador (credenciales + motivo obligatorio) | **Revierte el movimiento** devolviendo las UAs a su ubicación origen y habilita la **Hoja de Cancelación de Cambio de Almacén**. |

#### 3.2.9 Procedimiento Paso a Paso (SOP-TRF-01): Ejecutar un Cambio de Almacén o Consolidación

1. En **2. Cambio de Almacén**, haga clic en **+ Nuevo Traspaso**.
2. **Paso 1 (Montacarguista):** Seleccione un operador certificado con estatus *Disponible*.
3. **Paso 2 (Origen y UAs):** Elija la **Bahía Origen** (por buscador o *Selector Visual*). Seleccione las tarimas específicas mediante checkboxes o con el selector numérico rápido.
4. **Paso 3 (Destino):** Elija la **Bahía Destino** y verifique el banner de validación:
   - Confirme **Bahía Destino Disponible (En Ceros)** para un *putaway* limpio, o **Consolidación de Bahía Autorizada** si está agrupando inventario dentro de la capacidad máxima.
   - Si aparece *Bahía Bloqueada* o *Capacidad Excedida*, seleccione otra ubicación.
5. **Paso 4 (Motivo):** Seleccione el motivo oficial del catálogo y agregue observaciones (obligatorias si elige *Otro motivo*).
6. **Paso 5 (Pre-Validación y Confirmación):** Verifique que los 5 puntos del checklist estén en verde y pulse **Confirmar Traspaso**.
7. Valide el resumen en el modal de confirmación, ejecute el movimiento y, cuando el sistema pregunte, imprima el **Comprobante Oficial de Cambio de Almacén** para archivo o entrega al montacarguista.

---

### 3.3 Submódulo 3: Salidas de Almacén / Outbound (`/warehouse-movements/outbound`)

![Salidas de Almacén - Outbound](screenshots/09-outbound.png)

#### 3.3.1 Propósito y Alcance Operativo

El submódulo de **Salidas de Almacén (Outbound)** controla el despacho de mercancía hacia las plantas y centros de negocio del cliente. Integra el arribo de unidades vacías desde Caseta de Seguridad con la asignación de rampas de embarque (1–12), el surtido inteligente multi-producto bajo regla estricta **FEFO** (con bloqueo automático de inventario vencido o retenido por Calidad PNC y alertas de caducidad próxima **Alerta Pablo**), la validación por lectura RF en andén, el registro de sellos/cinchos oficiales, la autorización de líder y la emisión de la **Remisión / Comprobante Oficial de Salida Formato F03**.

#### 3.3.2 Panel de Métricas Operativas (KPIs)

Dispone de vista expandida (**5 tarjetas KPI**) y vista compacta colapsable (**Compactar Vista / Expandir Métricas**):

| Tarjeta KPI | Etiqueta Compacta | Fórmula / Alcance | Interpretación Operativa |
| :--- | :--- | :--- | :--- |
| **TOTAL SALIDAS** | `Total` | Total de órdenes de salida del turno vigente + activas de arrastre | Volumen general de embarques gestionados. |
| **1. REGISTRADAS CASETA** | `1. Caseta` | Salidas en estatus *Registrada* (`REGISTERED`) | Unidades vacías en patio esperando asignación de andén, tarimas y montacarguista. |
| **2-4. EN ANDÉN / CARGA** | `2-4. En Andén` | Suma de *Asignada* (`ASSIGNED`), *En Carga* (`IN_PROGRESS`) y *Por Auditar* (`LOADED`) | Unidades acopladas a rampa en proceso de carga o auditoría previa al despacho. |
| **5. COMPLETADAS** | `5. Completadas` | Salidas en estatus *Completada* (`COMPLETED`) | Embarques cerrados, descontados de stock y con Formato F03 emitido. |
| **CANCELADAS** | `Canceladas` | Salidas en estatus *Cancelada* (`CANCELLED`) | Despachos anulados con tarimas reintegradas al inventario. |

#### 3.3.3 Barra de Búsqueda, Filtros y Directorio General de Salidas

La tarjeta **Directorio General de Salidas** incluye:

- **Buscador Predictivo (`⌘K`):** Busca en vivo por folio (`SAL-YYYY-XXXXXX`), cliente, rampa, chofer o sello de seguridad.
- **Píldoras de Filtro Combinables:** `Todos`, `En Caseta`, `En Andén`, `Despachado`, `Pausadas`, `Canceladas`, `Urgentes` (prioridad alta) y `Arrastre` (turnos anteriores).
- **Tarjetas Ejecutivas de Salida:** Muestran cliente, destino/planta, folio, hora, estatus, línea transportista, chofer, números económicos y placas de tracto y caja, rampa asignada, resumen de tarimas/piezas y una **Barra de Progreso de 5 Fases Operativas** (Fase 1 a Fase 5).

#### 3.3.4 Flujo de Creación de Nueva Salida (Asistente en 2 Pasos)

Cuando Mesa de Control genera una orden de salida directamente desde consola, el asistente guía la captura en dos pasos:

- **Paso 1 — Transporte, Destino y Sello:**
  - *Cliente y Destino:* Selección de **Cliente** y **Destino Físico / Planta** (con dirección de solo lectura cargada automáticamente del catálogo de Centros de Negocio).
  - *Transportista y Vehículo:* **Línea Transportista**, **Nombre del Operador (Chofer)**, **Tipo de Camión** (`Camión`, `Tórtón` o `Tráiler`), **Eco de Tracto**, **Placas de Tracto**, **Eco Caja** y **Placas Caja**.
  - *Seguridad:* **Número de Sello o Cincho Oficial** (obligatorio para cierre).
- **Paso 2 — Selección de Mercancía y Tarimas (Arquitectura Inteligente FEFO):**
  - **Sección 1 — Asignación de Montacarguista:** Buscador predictivo de operador certificado.
  - **Sección 2 — Buscador Omnicanal de Producto y Escáner de UA:** Compatible con pistola RF y tablet; al escribir o escanear muestra en un desplegable dos grupos: *Sección A: Tarimas / UAs Directas Coincidentes* y *Sección B: Catálogo de Productos y SKUs*.
  - **Sección 3 — Tarimas Disponibles en Inventario con Motor FEFO:**
    - Barra compacta con buscador interno por UA/lote/bahía, contador de inventario local y **Controles del Motor FEFO**: *Stepper Numérico FEFO* (indica cuántas tarimas requiere), botón **Agregar FEFO** (selecciona automáticamente las más próximas a caducar que estén disponibles) y botón **Todas**.
    - Tabla de tarimas disponibles con semáforo de vigencia y estatus de calidad.
  - **Sección 4 — Manifiesto Consolidado de Despacho (Carro Multi-Producto):** Tarjetas resumen, desglose por SKU en chips y tabla consolidada de todas las UAs a embarcar.
  - **Sección 5 — Pre-Validación en Vivo (Checklist de 6 Puntos):** Verifica: 1) *Cliente y Destino*, 2) *Transporte y Chofer*, 3) *Unidades y Sello*, 4) *Tarimas y Piezas del Manifiesto*, 5) *Montacarguista* y 6) *Criterio FEFO*.

#### 3.3.5 Expediente Operativo de Salida (Workbench y Stepper de 5 Fases)

Al abrir una salida existente desde el directorio se presenta el expediente completo:

1. **Cabecera Breadcrumb y Controles Rápidos:** Botón regresar, cliente/folio, conmutador en caliente de **Prioridad Urgente**, botones **Pausar Maniobra** / **Reanudar Maniobra**, **Editar Ficha de Transporte**, **Cambio de Remisión / Carta Porte**, **Imprimir Comprobante F03** y **Cancelar Salida**.
2. **Stepper Horizontal Visual de 5 Fases:**
   - `Paso 1: Caseta` ➔ `Paso 2: Asignación` ➔ `Paso 3: En Carga` ➔ `Paso 4: Por Auditar` ➔ `Paso 5: Cerrada`.
3. **Ficha Operativa Unificada (Caseta y Transporte):**
   - *Fila 1 (4 columnas):* Cliente, Destino / Planta, Línea Transportista y Chofer / Operador.
   - *Fila 2 (3 columnas):* Placas y Económicos (Tracto y Caja), Sellos / Cinchos Oficiales y Andén de Salida + Montacarguista.
4. **Cajas de Acción Operativa Contextual por Fase:**
   - **Fase 1 (`REGISTERED` — Registrado en Caseta):** Muestra alertas si falta definir Planta/Destino o Sello. Permite asignar **Rampa de Salida (1–12)** y **Montacarguista** mediante selectores predictivos con badges de ocupación, y abrir el **Selector de Tarimas FEFO** si aún no hay mercancía asignada (resaltando el botón principal para asignar tarimas antes de notificar al montacarguista).
   - **Fase 2 (`ASSIGNED` — Andén Asignado):** Confirma la notificación en terminal de montacargas y habilita el inicio de carga física en andén.
   - **Fase 3 (`IN_PROGRESS` — Carga Física en Curso / Terminal RF):** Despliega la **Barra de Progreso de Escaneo de UAs en Andén**, el campo de entrada de **Lector / Pistola RF** para validar físicamente cada tarima subida al tráiler y el **Feedback Visual del Último Escaneo**.
   - **Fase 4 (`LOADED` — Carga Concluida / Por Auditar):** Presenta el resumen para auditoría administrativa y el botón principal para **Aprobar y Autorizar Salida** (requiere credenciales de Líder/Supervisor).
   - **Fase 5 (`COMPLETED` — Despacho Completado) / Cancelado:** Muestra confirmación de cierre F03 e impresión, o el banner de revocación si fue cancelada.
5. **Detalle de Tarimas Asignadas / Despachadas:** Tabla de UAs del manifiesto (o estado vacío guiado cuando aún no se seleccionan tarimas).
6. **Sección de Control y Auditoría (Colapsable):** Metadatos de despacho y línea de tiempo cronológica de eventos del folio.

#### 3.3.6 Motor de Gobernanza FEFO, Calidad (PNC) y "Alerta Pablo"

En el **Selector de Tarimas FEFO** (tanto en el Paso 2 de creación como en el modal de asignación de mercancía), cada tarima del inventario es evaluada automáticamente por el motor de gobernanza según dos ejes:

1. **Semáforo de Caducidad (Criterio de Días Remanentes):**

| Estatus | Condición (Días a Caducidad) | Color | Etiqueta en UI | ¿Seleccionable para Despacho? |
| :--- | :--- | :---: | :--- | :---: |
| `OPTIMAL` | Más de 30 días (`> 30 días`) | Verde | **Óptimo (`Xd`)** | **Sí** (Priorizado por FEFO según fecha más cercana) |
| `PABLO_ALERT` | Entre 1 y 30 días (`1 a 30 días`) | Ámbar | **Alerta Pablo (`Xd`)** | **Sí, con alerta crítica visual** para supervisión prioritaria de desplazamiento FEFO |
| `EXPIRED` | 0 días o vencido (`≤ 0 días`) | Rojo | **Caduco / Bloqueado** | **No** (Bloqueo estricto de sistema) |

2. **Gobernanza de Estatus de Inventario y Calidad (Pestañas de Filtro en Modal FEFO):**
   El modal incorpora 4 pestañas de filtro (`Todas`, `Disponibles`, `Bloqueados`, `Caducos`). Toda tarima retenida por **Control de Calidad (PNC / Cuarentena)** muestra el estatus **Bloqueado (Calidad)** con su motivo de retención y folio `PNC-QM`, quedando **estrictamente inhabilitada para selección y embarque** hasta su liberación formal en el módulo de Calidad.

#### 3.3.7 Catálogo Completo de Modales Operativos y de Seguridad en Salidas

| Modal | Propósito y Controles |
| :--- | :--- |
| **Selector de Tarimas FEFO y Manifiesto Acumulativo** | Combina: 1) Buscador predictivo de SKU y escáner de UA, 2) Detalle del SKU con **Stepper FEFO**, pestañas de estatus (`Todas / Disponibles / Bloqueados / Caducos`), buscador interno (UA/Lote/Bahía) y tabla de tarimas con semáforo *Alerta Pablo*, y 3) **Manifiesto Acumulativo de Despacho (Dispatch Basket)** agrupado por SKU con detalle de UAs seleccionadas. |
| **Confirmar Salida de Almacén** | Resumen estructurado con total de tarimas y piezas, datos logísticos/transporte, desglose multi-producto y advertencia de seguridad antes de confirmar. |
| **Aprobar y Autorizar Salida (Líder / Supervisor)** | Solicita **Credenciales de Líder o Supervisor** sobre el resumen del despacho para transicionar de `LOADED` a `COMPLETED`, descontar definitivamente el inventario y emitir el Formato F03. |
| **Modificar Ficha de Transporte / Destino** | Permite actualizar *Línea Transportista*, *Planta / Destino*, *Chofer*, *Sello Oficial*, *Placas* y *Números Económicos* de tracto y caja. |
| **Cambio de Remisión / Carta Porte** | Muestra resumen de la orden y exige *Nuevo No. de Remisión / Carta Porte* (diferente al actual), *Motivo Obligatorio* y **Credenciales de Supervisor o Administrador**. |
| **Cancelar Salida** | Muestra el resumen de tarimas a reintegrar al stock, exige *Motivo Obligatorio* y **Credenciales de Administrador**. Reintegra todas las UAs a sus bahías, libera la rampa y habilita la **Hoja de Cancelación de Salida de Almacén**. |
| **Plano de Andenes & Rampas de Carga (1–12)** | Vista en vivo de ocupación de las 12 rampas (Recepción Inbound y Carga Outbound). |
| **Impresión de Comprobantes (F03 / Cancelación)** | **Comprobante Oficial de Salida de Mercancía / Remisión WMS (F03)** homologado con datos de transporte, sellos, desglose multi-producto y firmas; o **Hoja de Cancelación de Salida de Almacén** con listado de UAs reintegradas a stock y 2 bloques de firmas (*Supervisor / Admin*). |

#### 3.3.8 Diccionario de Estados de Salidas de Almacén (Máquina de Estados)

| Estado | Etiqueta en UI | Transiciones Permitidas | Quién Autoriza / Ejecuta | Efecto en Inventario y Andenes |
| :--- | :--- | :--- | :--- | :--- |
| `DRAFT` | **Borrador** | ➔ `REGISTERED` / `ASSIGNED`<br>➔ `CANCELLED` | Mesa de Control | Sin descuento de stock ni ocupación de rampa. |
| `REGISTERED` | **En Caseta / Registrada** | ➔ `ASSIGNED`<br>➔ `CANCELLED` | Caseta de Seguridad (registra)<br>Mesa de Control (asigna andén/tarimas) | Unidad en patio. Sin rampa ocupada hasta su asignación. |
| `ASSIGNED` | **Andén Asignado** | ➔ `IN_PROGRESS`<br>➔ `PAUSED`<br>➔ `CANCELLED` | Mesa de Control / Líder / Montacarguista | **Bloquea físicamente la rampa asignada (1–12)** y reserva el manifiesto de tarimas para el montacarguista. |
| `IN_PROGRESS` | **En Carga** | ➔ `LOADED`<br>➔ `PAUSED`<br>➔ `CANCELLED` | Montacarguista (RF) / Mesa de Control | Validación por pistola RF de cada UA subida al transporte. Rampa ocupada. |
| `PAUSED` | **Pausada** | ➔ `IN_PROGRESS`<br>➔ `CANCELLED` | Líder de Turno / Supervisor | **Preserva las tarimas ya cargadas en el tráiler** y registra motivo de pausa (corte de turno o incidencia). Rampa reservada. |
| `LOADED` | **Por Auditar (Cargada)** | ➔ `COMPLETED`<br>➔ `CANCELLED` | Montacarguista (concluye carga)<br>Líder / Supervisor (aprueba) | Carga física completada; pendiente de auditoría final y autorización de líder. Rampa ocupada. |
| `COMPLETED` | **Despachado / Completada** | ➔ `CANCELLED` (extraordinaria) | Líder / Supervisor (credenciales obligatorias) | **Descuenta definitivamente las UAs del inventario físico**, emite **Formato F03** y habilita la liberación de rampa al Check-Out en Caseta. |
| `CANCELLED` | **Cancelada** | Estado terminal (inmutable) | Administrador (credenciales + motivo obligatorio) | **Reintegra las UAs al inventario disponible**, libera inmediatamente la rampa asignada y genera la **Hoja de Cancelación de Salida**. |

#### 3.3.9 Procedimiento Paso a Paso (SOP-OUT-01): Despacho Outbound con Surtido FEFO y Carga RF

1. **Ubicar la unidad en patio:** En **3. Salidas**, seleccione la tarjeta en estado *En Caseta* proveniente de Seguridad Patrimonial (o cree una orden desde el asistente de 2 pasos).
2. **Verificar destino y sello:** En el expediente, confirme que la **Planta / Destino** y el **Número de Sello / Cincho** estén capturados (si falta alguno, use el botón de edición de ficha).
3. **Asignar mercancía con Motor FEFO:** Haga clic en el botón para asignar tarimas. En el **Selector FEFO**, busque el SKU, verifique que no haya bloqueos de Calidad ni productos caducos, revise cualquier **Alerta Pablo (≤ 30 días)**, indique la cantidad en el *Stepper FEFO* y pulse **Agregar FEFO**. Repita para todos los SKUs requeridos y confirme el **Manifiesto Consolidado**.
4. **Asignar andén y montacarguista:** Seleccione una **Rampa disponible (1–12)** y un **Montacarguista certificado** y notifique la orden. El expediente avanzará a *Paso 2: Asignación*.
5. **Ejecutar carga física y escaneo RF:** Al iniciar la carga (*Paso 3: En Carga*), escanee con la pistola RF cada UA conforme ingresa al tráiler hasta que la **Barra de Progreso de Escaneo** alcance el 100%.
   - *Contingencia:* Si ocurre un cambio de turno a mitad de la carga, utilice **Pausar Maniobra** para preservar las tarimas ya escaneadas y reanúdela en el siguiente turno con **Reanudar Maniobra**.
6. **Auditar y autorizar cierre F03:** Concluida la carga (*Paso 4: Por Auditar*), haga clic en **Aprobar y Autorizar Salida**. El **Líder / Supervisor** ingresa sus credenciales para validar el despacho.
7. **Imprimir Remisión F03:** El estatus cambia a *Paso 5: Cerrada*, el inventario se descuenta definitivamente y se imprime el **Comprobante Oficial de Salida (Formato F03)** para entrega al chofer y salida en Caseta de Seguridad.

---

### 3.4 Catálogos Internos y Parámetros de Referencia del Módulo

Para consulta rápida de operadores, Mesa de Control y supervisores, se consolidan los catálogos maestros activos dentro de los tres submódulos de Movimientos de Almacén:

#### 3.4.1 Catálogo Oficial de Tipos de Tarima (7 Tipos)

| Clave Interna | Nombre Oficial en Consola | Uso Habitual |
| :--- | :--- | :--- |
| `MADERA_OWENS` | **Madera Owens** | Recepción y almacenaje de insumos/envases Owens. |
| `MADERA_ESTANDAR` | **Madera Estándar** | Operación general y rotación estándar. |
| `PLASTICO_NEGRO_OWENS` | **Plástico Negro Owens** | Tarima plástica retornable de especificación Owens. |
| `PLASTICO_AZUL` | **Plástico Azul** | Tarima plástica higienizada de grado alimenticio. |
| `TARIMA_CHEP_NACIONAL` | **Tarima CHEP Nacional** | Circuito de renta CHEP para distribución nacional. |
| `TARIMA_CHEP_EXPORTACION` | **Tarima CHEP Exportación** | Circuito CHEP certificado para exportación. |
| `TARIMA_PLASTICO_NEGRO_ESTANDAR` | **Tarima Plástico Negro Estándar** | Tarima plástica multiuso estándar. |

#### 3.4.2 Catálogo de Motivos Estándar de Autorización Excepcional MRP (Vida Útil Corta)

1. *Autorizado por MRP previo a embarque (Evaluación de riesgo aprobada)*
2. *Acuerdo con Planeación de la Demanda y Fábrica Receptora (Absorción de costo convenida)*
3. *Uso y consumo inmediato en producción / Maquila rápida*
4. *Rotación rápida acordada con cliente / Salida programada*
5. *Desabasto crítico de insumos / Urgencia operativa*
6. *Convenio comercial especial con proveedor por caducidad corta*
7. *Otro motivo operativo (especificar en justificación)*

#### 3.4.3 Catálogos Logísticos Base (Transporte, Rampas, Bodegas y Destinos)

- **Tipos de Transporte Outbound:** `Camión`, `Tórtón` y `Tráiler`.
- **Rampas / Andenes Habilitados:** 12 andenes físicos numerados de `R-01` a `R-12` (`LOC-RAMP-01` a `LOC-RAMP-12`), con zonas temporales asociadas `STAGING-R01` a `STAGING-R12`.
- **Bodegas Reales de Almacenaje:** 6 bodegas (`A`, `APC`, `AT`, `B`, `BPC`, `BT`) con capacidad dinámica por defecto de **22 tarimas por bahía** (configurable individualmente por el Administrador).
- **Destinos / Centros de Negocio Base:** *Centro de Negocio PAC*, *Centro de Negocio Culinarios*, *Centro de Negocio Cafés*, *Centro de Negocio CAF*, *Centro de Negocio Chocolates* y *Centro de Negocio Café Verde* (más los destinos adicionales registrados por cliente en el catálogo maestro).
- **Umbrales de Alerta de Patio y Caducidad:**
  - **Espera en Patio (Recepción):** Alerta automática al superar **8 horas (480 minutos)** desde el check-in en caseta.
  - **Tiempo de Descarga (Recepción):** Alerta crítica al superar **2.5 horas (150 minutos)** en descarga activa.
  - **Vida Útil en Recepción:** Mínimo **≥ 75%** remanente si la vida total es **< 24 meses**, o **≥ 50%** remanente si la vida total es **≥ 24 meses** (de lo contrario requiere autorización formal MRP / Planeación).
  - **Semáforo FEFO en Salidas:** **Óptimo** (`> 30 días`), **Alerta Pablo** (`1 a 30 días`) y **Caduco / Bloqueado** (`≤ 0 días`).
