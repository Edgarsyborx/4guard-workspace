## Capítulo 5. Control de Calidad (QM)

El módulo de **Control de Calidad (QM — Quality Management)** de **4GUARD WMS** centraliza el sistema de aseguramiento, inocuidad y cumplimiento normativo del almacén. Sustituye bitácoras físicas, hojas de cálculo dispersas y formularios externos por un entorno trazable de extremo a extremo que gobierna cinco procesos críticos: la retención de Producto No Conforme (PNC), el dictamen formal de liberaciones y destinos finales, la verificación obligatoria de carga y transporte bajo el formato **F01-PO-GC-8.6-03 Rev. 03**, el control financiero de reclamos e incidencias, y el monitoreo ejecutivo mensual mediante el **Tablero de 10 KPIs y Desviaciones**.

Todas las acciones ejecutadas en este módulo impactan en tiempo real el inventario físico del almacén (máquina de estados FSM), asientan movimientos inmutables en el Kardex y en la bitácora de auditoría, y transmiten alertas instantáneas a las terminales de radiofrecuencia (RF) para evitar maniobras sobre mercancía retenida o rechazada.

---

### 5.1. Arquitectura del Shell de Calidad y Tarjetas KPI Navegables

Al ingresar a **Control de Calidad** desde el menú lateral o desde el tablero principal, el sistema despliega el **Shell de Calidad**, una cabecera unificada que permanece visible en todo momento mientras el usuario cambia entre submódulos.

La parte superior presenta:
- **Ruta de navegación (miga de pan):** Enlace directo de retorno al **Dashboard** acompañado de la etiqueta institucional *Aseguramiento y Control de Calidad*.
- **Encabezado del módulo:** Título **Control de Calidad** y subtítulo operativo (*Gestión integral de producto no conforme, dictamen formal de liberaciones y verificación de carga F01-PO-GC-8.6-03*).

Inmediatamente debajo se encuentra la **rejilla de 5 Tarjetas KPI Navegables**. Cada tarjeta cumple una doble función: muestra un totalizador operativo en tiempo real y actúa como pestaña interactiva de acceso directo al submódulo correspondiente (la tarjeta activa se resalta visualmente).

| # | Etiqueta de Tarjeta | Métrica Mostrada en Tiempo Real | Submódulo Destino | Ruta de Navegación |
| :-: | :--- | :--- | :--- | :--- |
| **1** | **TOTAL BLOQUEADOS** | Cantidad de lotes en estatus *Bloqueado* (cuarentena activa) | **1. Bloqueos / PNC** | `/quality/blocks` |
| **2** | **LIBERACIONES** | Total histórico de dictámenes de liberación emitidos | **2. Dictamen Liberaciones** | `/quality/releases` |
| **3** | **VERIFICACIONES F01** | Total de formatos F01-PO-GC-8.6-03 registrados | **3. Verificación F01** | `/quality/load-verifications` |
| **4** | **RECLAMOS E INCIDENCIAS** | Total de expedientes de reclamos operativos registrados | **4. Reclamos (F01)** | `/quality/claims` |
| **5** | **TABLERO DE 10 KPIS** | Contador de desviaciones registradas (`Dev.`) | **5. KPIs & Desviaciones** | `/quality/kpi-dashboard` |

---

### 5.2. Submódulo 1: Bloqueos y Producto No Conforme (`/quality/blocks`)

Este submódulo concentra el universo de lotes que presentan alguna desviación de calidad, daño físico, anomalía documental o retención preventiva para pruebas de laboratorio. Únicamente muestra registros activos en estatus **Bloqueado** o **En Inspección**; en cuanto un lote recibe su dictamen formal de liberación, abandona esta bandeja y se traslada automáticamente al historial del Submódulo 2 (*Liberaciones*).

![Control de Calidad - Bloqueos PNC](screenshots/13-quality-blocks.png)

#### 5.2.1. Barra Superior de Búsqueda, Filtro y Acción

En la parte superior de la pantalla se disponen tres controles principales:
1. **Buscador Integrado:** Filtra en tiempo real la tabla por **Folio** (ej. `BLQ-2026-0001`), **SKU**, **Lote**, **Descripción**, **Cliente** o cualquiera de los **Criterios de Defecto** registrados. Incluye botón de limpieza rápida (`×`).
2. **Filtro Rápido por Cliente:** Lista desplegable dinámica poblada con los clientes que mantienen bloqueos activos, más la opción *Todos los Clientes*.
3. **Botón Principal *Registrar No Conformidad* (Dorado):** Abre el asistente de 3 pasos para inmovilizar un lote en el inventario y generar un nuevo folio de bloqueo.

#### 5.2.2. Estructura de la Tabla de Bloqueos Activos

La tabla principal presenta los lotes retenidos en cinco columnas con información consolidada:

| Columna | Contenido Mostrado | Propósito Operativo |
| :--- | :--- | :--- |
| **ID / Folio** | Folio consecutivo (`BLQ-2026-XXXX`) y fecha de registro (`AAAA-MM-DD`). | Identificación única del expediente de No Conformidad. |
| **Producto / Lote** | Clave **SKU** y descripción del producto, acompañados de etiquetas de **Lote**, código **SSCC** de tarima y **Cantidad con Unidad de Medida** (Pzas / Cajas). | Trazabilidad exacta del inventario retenido en piso. |
| **Cliente / Ubicación** | Razón social del propietario de la mercancía y código de ubicación física actual (ej. `LOC-QM-DOCK-02`, `A-01-N1`). | Localización inmediata de la tarima para traslado a bahía QM. |
| **Etapa** | Insignia de color correspondiente a una de las 4 etapas normativas de detección. | Clasificación del punto del flujo logístico donde ocurrió el hallazgo. |
| **Acciones** | Botones rápidos **Estatus** (solo en bloqueos activos), **Inspeccionar** y **Liberar**. | Ejecución directa del ciclo de vida QM sin cambiar de vista. |

En el pie de la tabla se muestra el contador dinámico (*Mostrando X de Y bloqueos activos*) junto con la leyenda de protocolo *4GUARD WMS · Protocolo QM*.

#### 5.2.3. Las 4 Etapas Normativas de Detección y Código de Colores

Todo bloqueo de Producto No Conforme se clasifica obligatoriamente en una de cuatro etapas normativas, cada una identificada con un color semántico consistente tanto en la tabla como en los modales de edición:

| Etapa Normativa | Color Identificador | Alcance Operativo |
| :--- | :--- | :--- |
| **1. Detección en Descarga (Inbound)** | **Azul** | Hallazgo identificado en andén de recepción durante la apertura de unidad o maniobra de descarga. |
| **2. Detección en Almacenamiento** | **Morado** | Hallazgo detectado en racks de almacenamiento, pasillos, bahías o durante traspasos internos. |
| **3. Detección en Carga (Outbound)** | **Amarillo / Dorado** | Hallazgo identificado en la zona de staging de despacho o en la caja de transporte previo a la salida. |
| **4. Material de Prueba / QA** | **Verde** | Muestras retenidas para análisis de laboratorio, pruebas técnicas o tratamiento especial autorizado. |

#### 5.2.4. Procedimiento de 3 Pasos para Registrar una No Conformidad

Para inmovilizar un lote nuevo, haga clic en el botón dorado **Registrar No Conformidad** y complete las tres secciones del modal:

**Paso 1 — Buscar Producto / Remisión / Lote / SKU (Detección Automática de Etapa):**
- Opcionalmente, seleccione uno de los **chips de filtro rápido por Proveedor / Cliente** (*Todos los Proveedores* o el cliente específico) para acotar la búsqueda.
- Escriba en el campo de búsqueda reactivo el **SKU**, **Lote / Remisión**, **SSCC**, descripción o ubicación. El sistema consulta en tiempo real el inventario global no bloqueado del WMS (excluyendo automáticamente tarimas que ya cuentan con un bloqueo activo para evitar duplicidades).
- Al seleccionar un resultado de la lista desplegable, se despliega la **Tarjeta Resumen del Lote Seleccionado**, mostrando Cliente, Ubicación, SKU, Descripción, Lote/Remisión, SSCC y Stock Disponible.
- El sistema asigna automáticamente la **Etapa de Detección** sugerida según el origen físico del lote, pero el inspector puede modificarla manualmente haciendo clic en las tarjetas del selector (*1. Descarga — Inbound*, *2. Racks — Storage* o *3. Carga — Outbound*).

**Paso 2 — Tipificación de Defecto según la Etapa Normativa:**
- Las categorías de defecto disponibles se ajustan dinámicamente a las reglas de calidad de la etapa seleccionada en el Paso 1. Si la etapa admite más de una categoría, se muestran como pestañas seleccionables; si solo admite una (como en *Almacenamiento* o *Material de Prueba*), el sistema la fija automáticamente.
- Marque una o más casillas en la cuadrícula de **Criterios Específicos Detectados**. Es obligatorio seleccionar al menos un criterio para poder registrar el bloqueo.

**Paso 3 — Cantidad a Bloquear y Observaciones:**
- Confirme o ajuste la **Cantidad a Bloquear** (prellenada con el stock disponible del lote).
- Capture las **Observaciones del Inspector** detallando el hallazgo físico, la causa probable o la acción inmediata requerida.
- Haga clic en **Bloquear Lote en FSM**.

> **Impacto automático al confirmar el bloqueo:** El sistema genera el folio consecutivo `BLQ-2026-XXXX`, conmuta el estado del inventario a **Cuarentena / En Calidad (`IN_QUALITY`)**, impide su asignación en picking, asienta el movimiento en el Kardex y en el historial de auditoría, asigna severidad **Crítica** de forma automática si se marcaron hallazgos de *plaga*, *humedad* o *caducidad* (y **Advertencia** en los demás casos), y transmite una alerta en tiempo real a los roles de Calidad, Operaciones y Terminales RF con la instrucción de no mover ni despachar la tarima.

#### 5.2.5. Matriz Normativa de Categorías y Criterios de Defecto

La siguiente tabla detalla la totalidad de categorías de defecto, las etapas en las que aplican y todos los criterios oficiales seleccionables en el sistema:

| Categoría de Defecto | Etapas Donde Aplica | Criterios Específicos Oficiales (Checkboxes) |
| :--- | :--- | :--- |
| **Defecto de Transporte** | • Detección en Descarga (Inbound)<br>• Detección en Carga (Outbound) | 1. Camión abierto o con lona en malas condiciones.<br>2. Paredes sucias.<br>3. Puertas sucias.<br>4. Presencia de indicios de plagas.<br>5. Presencia de aromas extraños.<br>6. Piso sucio, con obstáculos o en malas condiciones.<br>7. Presencia de perforaciones.<br>8. Llantas en mal estado. |
| **Documentación Incorrecta** | • Detección en Descarga (Inbound) | 1. Ausencia de certificado de calidad o es incorrecto.<br>2. Ausencia de certificado de fumigación o es incorrecto.<br>3. Sin remisión o factura. |
| **Defecto de Material** | • Detección en Descarga (Inbound)<br>• Detección en Almacenamiento<br>• Detección en Carga (Outbound) | 1. Caduco.<br>2. No cumple con especificación.<br>3. Embalaje en malas condiciones.<br>4. Material sin identificar.<br>5. Material con humedad.<br>6. Material visualmente inestable.<br>7. Material con plaga.<br>8. Material colapsado. |
| **Tratamiento Especial / Prueba QA** | • Material de Prueba / QA | 1. Prueba QA.<br>2. Material para tratamiento especial. |

#### 5.2.6. Modal de Inspección Técnica de Lote

Al hacer clic en el botón **Inspeccionar** de cualquier fila de la tabla, se abre el **Modal de Inspección Técnica de Lote**, diseñado en dos columnas con una barra fija de progreso en la parte superior.

**Encabezado y Barra de Progreso:**
- Muestra el cliente propietario, el chip de estatus actual del inventario, el número de **Lote** y la insignia del código **SSCC / Código de barras**.
- La barra superior calcula en vivo el porcentaje de avance (*X de 5 criterios validados*) y el contador de criterios **Obligatorios** (*X/5*).

**Columna Izquierda — Ficha Técnica del Lote:**
Presenta cuatro tarjetas de trazabilidad de solo lectura:
- **SKU / Producto:** Clave y descripción completa.
- **Cantidad Recibida:** Cantidad, unidad de medida, peso estimado (kg) y volumen ($m^3$).
- **Ubicación Actual:** Código de ubicación física en la *Zona de Control Cuarentena QM*.
- **Fecha de Caducidad:** Fecha límite de vida útil y fecha/hora de ingreso al almacén.
- Si existen **Observaciones de Recepción en Andén**, se resaltan en un recuadro dedicado al final de la columna.

**Columna Derecha — Checklist de Calidad Crítico (5 Criterios Obligatorios):**
El auditor debe verificar físicamente la mercancía y marcar cada uno de los cinco criterios normativos (también dispone de los atajos *Seleccionar Todos* y *Limpiar*):

| # | Criterio Normativo Obligatorio | Parámetro de Verificación | Carácter |
| :-: | :--- | :--- | :-: |
| **1** | **Empaque Secundario y Tarima Intactos** | Sin rasgaduras, abolladuras, parches húmedos ni tarimas rotas. | Obligatorio |
| **2** | **Vida Útil Mínima y Caducidad Vigente** | Cumple con margen mínimo de anaquel (> 90 días). | Obligatorio |
| **3** | **Control de Cadena de Frío (2°C - 6°C)** | Termómetro de recepción dentro de parámetros normativos. | Obligatorio |
| **4** | **Ausencia de Plagas o Contaminación Cruzada** | Libre de indicios de plagas, humedad excesiva o fauna nociva. | Obligatorio |
| **5** | **Etiquetado NOM / Código SSCC y Certificado Legible** | Código de barras escaneable y certificado de origen adjunto. | Obligatorio |

Cada criterio cambia dinámicamente su etiqueta de `PENDIENTE` a `CONFORME` al ser validado.

**Carga de Evidencias Fotográficas y Certificados (hasta 15 MB):**
- El inspector puede arrastrar archivos a la zona de carga o hacer clic en **Adjuntar Evidencia** para seleccionar uno o múltiples archivos desde su equipo.
- **Formatos permitidos:** `JPG`, `JPEG`, `PNG`, `WEBP` y `PDF`.
- **Límite de peso:** **15 MB máximo por archivo** (el sistema rechaza con alerta archivos de formato distinto o peso superior).
- Las imágenes muestran vista previa en miniatura junto con su nombre y tamaño; los archivos PDF muestran su ícono identificador. Es posible eliminar archivos individualmente o usar **Limpiar todo**.

**Dictamen / Observaciones del Inspector QM y Botones de Resolución:**
- El campo de texto libre permite asentar el dictamen técnico o motivo de rechazo. Incluye un contador de caracteres con validación de **mínimo 5 caracteres obligatorios** para retener/bloquear un lote.
- **Bloquear Lote (No Conforme):** Se habilita cuando las observaciones tienen al menos 5 caracteres. Mantiene el lote retenido en cuarentena QM, guarda las notas y fusiona las nuevas evidencias al expediente del bloqueo.
- **Aprobar y Liberar Lote:** Permanece deshabilitado hasta que se completen **los 5 criterios obligatorios (5/5)**. Al presionarlo, cierra la inspección y abre de inmediato el **Modal de Dictamen Formal de Liberación** prellenado con las observaciones y evidencias adjuntadas para elegir el destino final.

#### 5.2.7. Actualización de Etapa y Estatus FSM

El botón **Estatus** en la tabla de bloqueos abre el modal **Actualizar Etapa y Estatus**, que permite reclasificar un folio activo sin recrearlo:
1. **Etapa de Detección Normativa:** Permite reasignar el folio entre las **4 etapas normativas** (*Descarga Inbound* en azul, *Almacenamiento* en morado, *Carga Outbound* en amarillo/dorado, o *Material de Prueba / QA* en verde).
2. **Estatus del Folio (FSM):** Permite conmutar entre los tres estados operativos:
   - **🔴 Bloqueado:** Mercancía inmovilizada en cuarentena total.
   - **🟡 En Inspección:** Lote asignado a revisión técnica, muestreo o espera de respuesta del cliente.
   - **🟢 Liberado:** Cierra el bloqueo activo y reincorpora la mercancía al inventario global disponible.

Al hacer clic en **Guardar Cambios**, la tabla y los contadores del Shell se actualizan al instante.

---

### 5.3. Submódulo 2: Liberaciones y Destinos Finales (`/quality/releases`)

El submódulo de **Liberaciones** gobierna la resolución definitiva de todo Producto No Conforme. Garantiza que ningún lote abandone la cuarentena sin un autorizador identificado, un soporte documental auditable y un destino operativo explícito.

![Control de Calidad - Liberaciones](screenshots/14-quality-releases.png)

#### 5.3.1. Navegación por Pestañas y Tarjetas de Destino Final

La parte superior integra un selector de dos vistas con contadores dinámicos:
- **Pestaña 1 — Liberaciones Emitidas:** Muestra el historial completo de dictámenes emitidos con buscador por Folio, Lote, Cliente, Autorizador o Asunto del soporte, y una tabla de 7 columnas (*Folio & Fecha*, *SKU / Lote*, *Cliente*, *Autorizado Por*, *Soporte / Evidencia*, *Destino Final* y *Dictamen Técnico*).
- **Pestaña 2 — Por Dictaminar (Retenidos):** Despliega la bandeja de lotes actualmente en estatus *Bloqueado* o *En Inspección* (con insignia roja indicando pendientes) en una tabla de 6 columnas (*Folio Bloqueo*, *SKU & Descripción*, *Lote / SSCC*, *Cliente*, *Motivo de Retención* y botón **Dictaminar Liberación**).

Debajo de las pestañas se exhiben **3 tarjetas resumen de Destinos Finales** con sus respectivos contadores de lotes. Hacer clic sobre cualquiera de ellas filtra automáticamente la tabla de *Liberaciones Emitidas* por ese destino (mostrando un botón de limpieza de filtro activo en la barra de búsqueda).

#### 5.3.2. Los 3 Destinos Finales y su Impacto Operativo Exacto

Cada dictamen de liberación exige seleccionar uno de los tres destinos normativos, cuyo efecto en el sistema WMS es inmediato e irreversible:

| Destino Final | Etiqueta Visual | Estado Resultante en Inventario (FSM) | Movimiento en Kardex | Impacto Operativo Exacto |
| :--- | :--- | :--- | :--- | :--- |
| **Destino 1: Distribución** | **Liberado para Distribución** *(Verde)* | Disponible (`AVAILABLE`) | Liberación (`RELEASE`) | El lote es declarado apto. Se retira la restricción de cuarentena, se suma nuevamente al inventario global disponible y queda habilitado de inmediato para **picking, surtido y embarque** a clientes. |
| **Destino 2: Destrucción** | **Liberado para Destrucción** *(Rojo)* | Dañado / Merma (`DAMAGED`) | Ajuste de Baja (`ADJUSTMENT`) | Producto no conforme sin posibilidad de rescate comercial. Se canaliza a la zona de desecho/merma con baja definitiva respaldada por acta y evidencia documental. |
| **Destino 3: Devolución** | **Liberación para Devolución** *(Ámbar)* | Devuelto (`RETURNED`) | Devolución (`RETURN`) | Rechazo comercial o logístico. El inventario queda segregado para retorno formal a la planta matriz o al proveedor de origen, preparando su documentación de salida inversa. |

Adicionalmente, el sistema emite el folio formal `LIB-2026-XXXX`, cierra la incidencia de bloqueo origen, asienta el evento `QM_RELEASE_APPROVED` en la bitácora forense y difunde una notificación a las terminales RF informando el destino autorizado de la tarima.

#### 5.3.3. Origen de Autorización, Soportes Documentales y Evidencias

El **Modal de Dictamen Formal de Liberación** (accesible tanto desde la pestaña *Por Dictaminar* como desde los botones *Liberar* o *Aprobar y Liberar* del Submódulo 1) muestra en la parte superior la ficha resumen del lote (Cliente, SKU, Cantidad y Folio de Bloqueo Origen) y guía al usuario por cuatro pasos obligatorios:

1. **Origen de la Autorización:**
   - **Liberado por el Cliente:** Autorización formal emitida directamente por el cliente propietario de la mercancía.
   - **Liberado por Calidad 4GUARD:** Dictamen técnico interno emitido por la Superintendencia de Aseguramiento de Calidad (QM).

2. **Soporte Documental y Datos del Autorizador:**
   - **Tipo de Soporte Documental:** Seleccione entre cuatro opciones oficiales:
     - *Correo Electrónico Oficial*
     - *Medios Electrónicos (Ticket / Sistema)*
     - *Acta / Oficio Formal Firmado*
     - *Otro (Especificar)* — al elegir esta opción se habilita un campo obligatorio adicional para describir el medio (ej. *Minuta técnica firmada*, *Circular extraordinaria*, *WhatsApp gerencial*).
   - **Asunto / Folio del Correo o Ticket:** Referencia rastreable de la autorización.
   - **Nombre de Quién Autoriza** y **Puesto / Cargo del Autorizador:** Campos obligatorios para la trazabilidad legal del dictamen.
   - **Evidencia Documental / Fotográfica de la Autorización:** Zona interactiva de carga (*arrastrar y soltar* o *Examinar Archivo*) compatible con **JPG, PNG, WEBP y PDF** hasta **15 MB por archivo**. Las evidencias previamente adjuntadas durante el bloqueo o inspección técnica se heredan automáticamente en este expediente y pueden complementarse con el correo o acta de autorización.

3. **Condición y Destino Final del Producto:** Selección visual entre **Distribución**, **Destrucción** o **Devolución**.

4. **Justificación Técnica y Observaciones del Dictamen:** Campo obligatorio (mínimo **5 caracteres**) para documentar la resolución técnica, parámetros validados o acuerdos establecidos con el cliente.

#### 5.3.4. Procedimiento Operativo Estándar (SOP) para Emitir una Liberación

1. Ingrese a **Control de Calidad → Liberaciones** y seleccione la pestaña **Por Dictaminar (Retenidos)** (o haga clic en **Liberar** directamente desde un bloqueo activo en el Submódulo 1).
2. Localice el folio de bloqueo y presione **Dictaminar Liberación**.
3. Verifique en la ficha superior que el **Cliente**, **SKU**, **Lote** y **Cantidad** coincidan con la autorización recibida.
4. En el **Paso 1**, indique si la liberación proviene del **Cliente** o de **Calidad 4GUARD**.
5. En el **Paso 2**, seleccione el **Tipo de Soporte Documental**, capture el **Asunto / Folio**, ingrese el **Nombre** y **Puesto** de la persona que autoriza, y adjunte la evidencia en PDF o imagen (máx. 15 MB).
6. En el **Paso 3**, seleccione el **Destino Final** (*Distribución*, *Destrucción* o *Devolución*).
7. En el **Paso 4**, redacte la **Justificación Técnica** (mínimo 5 caracteres) y haga clic en **Emitir Dictamen de Liberación**.
8. Confirme el folio generado (`LIB-2026-XXXX`). El sistema cambiará automáticamente a la pestaña **Liberaciones Emitidas**, donde el registro quedará disponible para consulta y auditoría.

---

### 5.4. Submódulo 3: Verificación de Carga F01-PO-GC-8.6-03 (`/quality/load-verifications`)

Este submódulo digitaliza 1:1 el formato normativo oficial **F01-PO-GC-8.6-03 Rev. 03** (*Liberación de Carga — Seguridad e Inocuidad / Calidad*), garantizando la inspección sistemática tanto del producto/tarimas como de las condiciones higiénicas y estructurales de la unidad de transporte antes de autorizar cualquier despacho.

![Control de Calidad - Verificación de Carga F01](screenshots/13b-quality-verifications.png)

#### 5.4.1. Interfaz Maestro-Detalle (Directorio y Área de Captura)

La pantalla se divide en dos paneles sincronizados:
- **Panel Izquierdo — Directorio de Verificaciones F01:** Contiene el botón **Nueva** para crear un formato en blanco, un buscador instantáneo (por Folio `VER-2026-XXXX`, Remisión, Cliente, Producto o Rampa), un filtro desplegable por los 5 estatus oficiales (*Todos los estatus*, *Aprobados*, *Limpieza IT01*, *Acondicionamiento IT02*, *Rechazados*, *En Proceso*) y la lista de tarjetas de verificación con iniciales del cliente, folio, estatus, remisión, descripción, rampa y fecha.
- **Panel Derecho — Formulario Digital Interactivo:** Muestra en la barra superior el Cliente, Folio, insignia de estatus en tiempo real y los botones **Imprimir / PDF** y **Guardar Cambios**. Debajo se organizan las alertas normativas automáticas y los cinco bloques de captura:
  1. **Datos Generales de la Carga:** Descripción del producto, No. de Remisión, Cliente, Fecha de inspección, Hora y Rampa de andén asignada.
  2. **Personal y Responsables de la Inspección:** *Elaboró (Montacarguista / Andén)*, *Revisó (Auditor de Calidad QM)*, *Aprobó (Superintendencia / Jefe QM)* y *Responsable de Limpieza (IT01-PO-GC-8.6-01)*.
  3. **Tabla 1 — Condiciones Generales Visuales (Criterios de Producto).**
  4. **Tabla 2 — Criterios de Transporte (Unidad / Caja / Tracto).**
  5. **Observaciones Generales y Dictamen Final de Carga.**

#### 5.4.2. Alertas Normativas Automáticas y Recálculo de Estatus

Cada vez que el inspector marca **NO (`✕`)** en un criterio de producto o transporte, el motor normativo recalcula automáticamente el estatus del formato y despliega banners de alerta de acción obligatoria (en orden estricto de prioridad):

| Alerta Automática | Condición de Disparo | Estatus Asignado | Protocolo Obligatorio |
| :--- | :--- | :--- | :--- |
| **¡ALERTA CRÍTICA DE INOCUIDAD! PRESENCIA DE PLAGA DETECTADA** *(Rojo)* | Criterio de Producto #8 (*Producto libre de algún tipo de plaga*) = **NO** **o** Criterio de Transporte #4 (*Libre de indicios de plagas evidentes*) = **NO**. | **Rechazado** | Bloquea el despacho de inmediato. Impide firmar la liberación. Exige reporte formal vía correo al cliente y Dirección General con evidencia fotográfica, y transmite alerta crítica a terminales RF. |
| **INSTRUCTIVO IT02-PO-GC-8.6-02: ACONDICIONAMIENTO DE CARGA EN CURSO** *(Azul)* | Cualquiera de los Criterios de Producto #1, #2, #3 o #5 = **NO**, **o** Criterios de Transporte #1, #7, #8 o #9 = **NO**. | **Acondicionamiento Pendiente (IT02)** | Se detectaron daños, aberturas, inestabilidad en tarima/embalaje o deficiencias estructurales en la unidad. Requiere acondicionamiento físico antes de poder liberar la carga. |
| **INSTRUCTIVO IT01-PO-GC-8.6-01: LIMPIEZA DE PALLET REQUERIDA** *(Ámbar)* | Criterio de Producto #4 (*Pallets limpios*) = **NO** **o** Criterios de Transporte #2, #3 o #6 (paredes, puertas o piso sucio) = **NO**, sin firma de limpieza registrada. | **Limpieza Pendiente (IT01)** | Exige realizar limpieza física y asentar el nombre y firma del **Responsable de Limpieza (IT01)** para poder liberar la carga y plasmarlo en el acta impresa. |

Si todos los criterios aplicables son evaluados como **SI (`✓`)** o **N/A (`–`)**, o tras completar y firmar la limpieza requerida, el estatus evoluciona automáticamente a **Aprobado**.

#### 5.4.3. Catálogo Completo de Criterios de Producto (Tabla 1 — F01-PO-GC-8.6-03)

Todos los criterios cuentan con botones interactivos **SI (`✓`)**, **NO (`✕`)** y **N/A (`–`)**, además de un campo individual de **Observaciones**:

| # | Criterio de Producto | Acción Normativa si la Evaluación es NO | Instructivo Aplicable | Responsable |
| :-: | :--- | :--- | :---: | :--- |
| **1** | **Tarima en buen estado** *(Habilitada para manipularse con montacargas).* | Se informa a las áreas correspondientes para su seguimiento y se realiza acondicionamiento de acuerdo al instructivo IT02-PO-GC-8.6-02. | `IT02-PO-GC-8.6-02` | Mantenimiento, Calidad, Operaciones. |
| **2** | **Pallet sin daños** *(Sin el producto expuesto y dañado).* | Se realiza acondicionamiento de acuerdo al instructivo IT02-PO-GC-8.6-02. | `IT02-PO-GC-8.6-02` | Calidad. |
| **3** | **Embalaje en buen estado** *(Sin aberturas o rasgaduras que afecte la inocuidad).* | Se realiza acondicionamiento de acuerdo al instructivo IT02-PO-GC-8.6-02. | `IT02-PO-GC-8.6-02` | Calidad. |
| **4** | **Pallets limpios** *(Sin manchas en general, sustancia ajena al pallet, exceso de polvo, etc.).* | Se realiza limpieza a pallet, en caso de exceso de polvo aplicando el instructivo IT01-PO-GC-8.6-01. | `IT01-PO-GC-8.6-01` | Mantenimiento, Calidad. |
| **5** | **Pallet visualmente estable o alineado.** | Se realiza acondicionamiento de acuerdo al instructivo IT02-PO-GC-8.6-02. | `IT02-PO-GC-8.6-02` | Calidad. |
| **6** | **Producto coincide con lo solicitado por el cliente.** | Se reporta al líder en turno. | — | Calidad, Operaciones. |
| **7** | **Material identificado con UA de cliente.** | Se reporta al líder en turno. | — | Calidad, Operaciones. |
| **8** | **Producto libre de algún tipo de plaga.** *(Crítico)* | Se realiza un reporte, vía correo al cliente y dirección general, informando con evidencia fotográfica como respaldo del suceso, esperando respuesta por parte del cliente, para el seguimiento. | Protocolo Inocuidad | Calidad. |
| **9** | **Otros:** *(Criterio abierto para observaciones especiales).* | Según hallazgo asentado en observaciones. | — | — |

#### 5.4.4. Catálogo Completo de Criterios de Transporte (Tabla 2 — F01-PO-GC-8.6-03)

La segunda matriz inspecciona las condiciones físicas, higiénicas y de aseguramiento de carga del tractocamión o caja:

| # | Criterio de Transporte | Acción Normativa si la Evaluación es NO | Responsable |
| :-: | :--- | :--- | :--- |
| **1** | **Camión cerrado o con lona en buenas condiciones.** | Se toma evidencia de los criterios detectados y se comparte al cliente. | Calidad. |
| **2** | **Paredes limpias.** | Se toma evidencia de los criterios detectados y se comparte al cliente. | Calidad. |
| **3** | **Puertas limpias.** | Se toma evidencia de los criterios detectados y se comparte al cliente. | Calidad. |
| **4** | **Libre de indicios de plagas evidentes.** *(Crítico)* | Se toma evidencia de los criterios detectados y se comparte al cliente. *(Dispara alerta crítica de inocuidad y estatus Rechazado).* | Calidad. |
| **5** | **Libre de aromas extraños.** | Se toma evidencia de los criterios detectados y se comparte al cliente. | Calidad. |
| **6** | **Piso limpio / Libre de obstáculos y en un buen estado.** | Se toma evidencia de los criterios detectados y se comparte al cliente. | Calidad. |
| **7** | **Libre de perforaciones.** | Se toma evidencia de los criterios detectados y se comparte al cliente. | Calidad. |
| **8** | **Llantas en buen estado.** | Se toma evidencia de los criterios detectados y se comparte al cliente. | Calidad. |
| **9** | **Bloqueo de Seguridad** *(Uso de cartón, bolsa de aire, gatas, eslingas) Cuando la carga es foránea.* | Se toma evidencia de los criterios detectados y se comparte al cliente. | Calidad / Operaciones. |

#### 5.4.5. Guardado, Vista Previa Oficial e Impresión PDF

Al concluir la captura:
1. Presione **Guardar Cambios** para persistir la verificación (el sistema muestra un aviso de confirmación con el folio `VER-2026-XXXX`). Si el estatus es *Limpieza Pendiente*, *Acondicionamiento Pendiente* o *Rechazado*, el sistema notifica automáticamente a las terminales RF en la rampa asignada.
2. Presione **Imprimir / PDF** para abrir el modal oficial de vista previa del documento **Formato F01-PO-GC-8.6-03 · Verificación de Carga**, el cual reproduce fielmente el formato de planta en hoja tamaño Carta con:
   - Bloque de control documental (*Dueño*, *Proceso*, *Emisión*) y los tres sellos digitales con código QR de **Elaboró**, **Revisó** y **Aprobó**.
   - Metadatos de producto, remisión, fecha, hora y rampa.
   - Tablas completas de Criterios de Producto y Criterios de Transporte con marcas visuales (`✔` / `✕`) y celdas agrupadas.
   - Cajas inferiores de firmas con sellos QR para **Nombre y firma de quien realizó la limpieza** y **Nombre y firma de quien realizó la liberación**.
3. Desde la barra del modal puede elegir **Descargar PDF** (genera el archivo `<FOLIO>_F01-PO-GC-8.6-03.pdf` en alta resolución) o **Imprimir** para enviarlo directamente a impresora física.

---

### 5.5. Submódulo 4: Reclamos e Incidencias F01 (`/quality/claims`)

Este submódulo centraliza el registro, seguimiento e impacto financiero de los reclamos e incidencias de calidad originados en descarga, almacenamiento o embarque, reemplazando las hojas de cálculo manuales por un concentrado auditable con emisión de dictamen oficial en PDF.

![Control de Calidad - Reclamos F01](screenshots/13c-quality-claims.png)

#### 5.5.1. Tablero Ejecutivo de 4 KPIs de Reclamos

La franja superior resume en tiempo real el impacto operativo y económico acumulado del período:

| # | Tarjeta KPI | Qué Mide | Subtítulo / Unidad |
| :-: | :--- | :--- | :--- |
| **1** | **TOTAL RECLAMOS (MES)** | Conteo total de expedientes de reclamo registrados. | Incidencias registradas |
| **2** | **MATERIAL DAÑADO** | Sumatoria de cajas o piezas reportadas con daño físico. | Cajas / Pzas afectadas |
| **3** | **MATERIAL PERDIDO / MERMA** | Sumatoria de unidades extraviadas o dadas de baja definitiva. | Bajas definitivas |
| **4** | **COSTO TOTAL POR RECLAMOS** | Sumatoria consolidada del impacto económico en pesos mexicanos (`$ MXN`). | Impacto económico consolidado |

#### 5.5.2. Barra de Filtros y Tabla Concentradora de Reclamos

Debajo de los KPIs se ubica la barra de herramientas con:
- **Buscador Global:** Filtra por Folio (`REC-2026-XXXX`), Lote, Remisión, Cliente, SKU o descripción.
- **Pills de Filtro por Etapa Operativa:** *Todas las Etapas*, *1. Descarga (Inbound)*, *2. Almacenamiento (Racks)* y *3. Carga (Outbound)*.
- **Selector de Tipo de Defecto:** *Todos los Defectos*, *No cumple especificación*, *Discrepancia en cantidad*, *Malas condiciones* y *Otro*.
- **Botón Principal *+ Registrar Nuevo Reclamo*:** Abre el modal integral de captura.

La tabla **Concentrado de Reclamos e Incidencias de Calidad** organiza cada expediente en 9 columnas:
1. **Folio / Fecha:** Folio `REC-2026-XXXX`, fecha y hora de detección.
2. **Etapa Operativa:** Insignia visual (*Descarga*, *Racks* o *Carga*).
3. **Producto & Cliente:** SKU, descripción y cliente/proveedor.
4. **Lote & Remisión:** Dos campos independientes explícitamente etiquetados (`LOTE:` y `REM:`) para trazabilidad documental sin ambigüedades.
5. **Tipo de Defecto:** Tipificación oficial o descripción personalizada cuando corresponde a *Otro*.
6. **Impacto Físico:** Etiquetas separadas para piezas **Dañadas** (ámbar) y **Merma** (rojo), o leyenda *Sin merma física*.
7. **Costo Asociado:** Monto monetario formateado en MXN.
8. **Soporte / Evidencia:** Contador de archivos adjuntos disponibles.
9. **Acción:** Botones **Imprimir** (vista previa del dictamen PDF) y **Ver** (modal de detalle del expediente).

#### 5.5.3. Formulario de Registro de Nuevo Reclamo (4 Pasos)

Al hacer clic en **+ Registrar Nuevo Reclamo**, el formulario guía la captura en cuatro bloques:

1. **Paso 1 — Información General, Etapa y Lote/Remisión:**
   - Seleccione la **Etapa Operativa** mediante tarjetas interactivas (*1. Descarga*, *2. Almacenamiento* o *3. Carga*).
   - Ajuste la **Fecha de Detección** y **Hora**.
   - Seleccione el **Producto Afectado** del catálogo sugerido (autocompleta SKU, descripción y cliente).
   - Capture los campos obligatorios independientes: **Número de Lote (Batch)** y **Folio de Remisión / Factura**.

2. **Paso 2 — Tipología del Defecto o Discrepancia:**
   - Elija la causal del reclamo entre cuatro categorías oficiales:
     - *Malas condiciones (Empaque roto / Tarima dañada / Humedad)*
     - *No cumple con especificación (Código / Rotulado / Caducidad)*
     - *Discrepancia en cantidad (Faltante / Sobrante físico)*
     - *Otro (Especificar)* — despliega un campo de texto obligatorio para describir la causal no catalogada.

3. **Paso 3 — Impacto Físico y Financiero:**
   - **Material Dañado (Cajas/Pzas):** Cantidad numérica afectada.
   - **Material Perdido / Merma:** Cantidad numérica en baja definitiva.
   - **Costo Asociado ($ MXN):** Impacto económico estimado o liquidado.

4. **Paso 4 — Soporte Documental, Autorizador y Evidencias:**
   - **Nombre de Quién Autoriza / Reporta** (obligatorio) y **Puesto / Cargo**.
   - **Observaciones y Justificación Técnica:** Dictamen preliminar, acuerdos con cliente o acciones inmediatas.
   - **Evidencias Fotográficas y Actas PDF:** Carga múltiple de archivos **JPG, PNG, WEBP y PDF** (hasta **15 MB** por archivo) con vista previa.

Al pulsar **Guardar y Registrar Reclamo**, se genera el folio consecutivo `REC-2026-XXXX` en estatus inicial `OPEN` y se recalculan de inmediato los 4 KPIs superiores.

#### 5.5.4. Estados del Reclamo y Emisión de Dictamen Oficial PDF

Cada expediente de reclamo transita por cuatro estados formales que se reflejan en el sistema y en el dictamen impreso:

| Estado del Reclamo | Significado Operativo | Indicador Visual en Dictamen |
| :--- | :--- | :--- |
| **`OPEN` (Abierto)** | Reclamo recién registrado y pendiente de análisis o dictamen con el cliente/proveedor. | Ámbar |
| **`IN_REVIEW` (En Revisión)** | Expediente bajo investigación de causa raíz, validación de evidencias o conciliación de costos. | Ámbar |
| **`CLOSED` (Cerrado)** | Investigación concluida y medidas correctivas documentadas. | Verde |
| **`SETTLED` (Liquidado)** | Reclamo cerrado con conciliación económica, nota de crédito o finiquito formal completado. | Verde |

Para consultar un caso, haga clic en **Ver** (abre el **Modal de Detalle del Reclamo** con producto, cliente, lote, remisión, impacto físico/financiero y dictamen). Desde el detalle o directamente desde el botón **Imprimir** de la tabla se despliega el **Dictamen Oficial de Reclamo e Incidencia de Calidad (Norma `F02-PO-GC-8.6-04`)**, que incluye encabezado corporativo, tabla de trazabilidad, liquidación del costo de no calidad, observaciones técnicas y sellos digitales QR del **Auditor de Calidad (QM)** y **Superintendencia / Autorizó**. Permite **Descargar PDF** (`<FOLIO>_F02-PO-GC-8.6-04.pdf`) o **Imprimir** en papel.

---

### 5.6. Submódulo 5: Tablero de 10 KPIs & Desviaciones (`/quality/kpi-dashboard`)

El quinto submódulo constituye el centro de inteligencia gerencial de Calidad. Consolida mensualmente los **10 Indicadores Normativos de Desempeño QM** en un tablero visual tipo Bento Grid, proporciona un formulario nativo para el registro de desviaciones en almacén y descarga (eliminando el uso de Google Forms y Google Drive), y genera el reporte oficial multi-hoja en Excel (`.xlsx`) o CSV.

![Control de Calidad - Tablero 10 KPIs](screenshots/13d-quality-kpis.png)

#### 5.6.1. Barra de Control Mensual

En la cabecera del submódulo el usuario controla el periodo de evaluación y las acciones globales:
- **Período de Análisis:** Selector mensual histórico (desde noviembre 2025 hasta el mes operativo en curso). Al cambiar de mes, los 10 KPIs y la tabla de desviaciones se recalculan automáticamente.
- **Identificador de Almacén:** Muestra la nave activa (*Almacén Central Toluca — Nave M1*).
- **Exportar Reporte Mensual:** Descarga el libro Excel corporativo `.xlsx` (o CSV en modo respaldo) correspondiente al periodo seleccionado.
- **+ Registrar Desviación:** Abre el modal nativo para asentar una nueva desviación de calidad.

#### 5.6.2. Matriz Detallada de los 10 Indicadores Clave de Calidad (KPIs)

El tablero Bento Grid despliega los diez indicadores oficiales calculados automáticamente a partir de las verificaciones F01, liberaciones, reclamos y desviaciones del mes seleccionado:

| # | Indicador / KPI | Categoría | Qué Mide y Fuente de Cálculo | Meta / Umbral Oficial | Reglas de Semáforo |
| :-: | :--- | :--- | :--- | :---: | :--- |
| **1** | **% Liberaciones sin Desviación** | Liberación | Porcentaje de lotes conformes respecto al total de lotes inspeccionados en F01 (o liberados a distribución si no hay F01). Incluye barra de cumplimiento. | **≥ 95.0%** | • **Verde (`SUCCESS`):** ≥ 95.0% (*✓ Cumple Meta*)<br>• **Ámbar (`WARNING`):** 90.0% a 94.9% (*⚠ En Riesgo*)<br>• **Rojo (`DANGER`):** < 90.0% (*❌ No Cumple*) |
| **2** | **% Transporte Óptimo** | Transporte | Porcentaje de unidades de transporte verificadas en F01 que cumplen condiciones óptimas de limpieza, ausencia de olores y ausencia de plagas (sin rechazo ni limpieza pendiente). | **≥ 95.0%** | • **Verde (`SUCCESS`):** ≥ 95.0%<br>• **Ámbar (`WARNING`):** < 95.0% |
| **3** | **Inspector Top del Mes** | Productividad | Colaborador o auditor QM con mayor número de liberaciones dictaminadas durante el mes, acompañado del total de lotes liberados. | **Desempeño QM** *(Informativo)* | • **Azul / Dorado (`INFO`):** Indicador de reconocimiento y productividad individual. |
| **4** | **Daños en Almacenamiento** | Almacén | Conteo mensual de eventos de desviación detectados en racks, almacenamiento, goteras, tarimas o derivados de manejo inadecuado. | **≤ 5 eventos** | • **Verde (`SUCCESS`):** ≤ 5 eventos<br>• **Rojo (`DANGER`):** > 5 eventos |
| **5** | **Causa Raíz Predominante** | Análisis Causa Raíz | Motivo de daño con mayor frecuencia acumulada en las desviaciones del mes (análisis de Pareto para enfocar planes de mitigación). | **Mitigación** | • **Ámbar (`WARNING`):** Alerta preventiva permanente para acción correctiva sobre la causa principal. |
| **6** | **Desviaciones en Descarga** | Recepción Inbound | Cantidad de fallas detectadas en el área de recepción/descarga (tarima rota, plagas, ausencia de certificado COA, etc.). | **≤ 5 al mes** | • **Verde (`SUCCESS`):** ≤ 5 fallas<br>• **Ámbar (`WARNING`):** > 5 fallas |
| **7** | **Reclamos de Cliente** | Satisfacción | Conteo de quejas o reclamos externos registrados formalmente durante el periodo. | **0 Críticos** | • **Verde (`SUCCESS`):** 0 reclamos<br>• **Ámbar (`WARNING`):** 1 a 2 reclamos<br>• **Rojo (`DANGER`):** > 2 reclamos |
| **8** | **Costo de la No Calidad** | Finanzas QM | Impacto económico total en pesos mexicanos (`$ MXN`), resultante de sumar el costo de material de todas las desviaciones del mes más el costo asociado de los reclamos. | **< \$10,000 MXN** | • **Verde (`SUCCESS`):** ≤ \$10,000 MXN<br>• **Rojo (`DANGER`):** > \$10,000 MXN |
| **9** | **Total Piezas Físicas Dañadas** | Inventario Físico | Sumatoria de unidades físicas dañadas en el mes, con desglose visible por tipo de material: **PT** (Producto Terminado), **Emb** (Embalajes) y **Café** (Café Verde). | **≤ 70 U máx** | • **Verde (`SUCCESS`):** ≤ 70 piezas<br>• **Rojo (`DANGER`):** > 70 piezas |
| **10** | **Acciones & Resoluciones** | Disposición FSM | Volumen de desviaciones atendidas y resueltas mediante acciones formales en el flujo FSM (bloqueos, rechazos, acondicionamientos, devoluciones o destrucciones). | **100% Cerradas** | • **Verde (`SUCCESS`):** Seguimiento de cierre efectivo de disposiciones QM. |

#### 5.6.3. Registro Nativo de Desviaciones y Cédula Imprimible (`DEV-PO-QM-01`)

Debajo del tablero de 10 KPIs se encuentra la **Tabla Concentradora de Desviaciones**, equipada con buscador (Folio `DEV-2026-XXXX`, Remisión, SKU, UA/SSCC o Colaborador), filtro por **Tipo de Material** (*Producto Terminado*, *Embalajes*, *Café Verde*, *Otro Material*) y filtro por **Causa Raíz** (*Manejo Inadecuado*, *Infraestructura*, *Plagas*, *Limpieza*, *Transporte Interno*, *Empaque Original*).

Al hacer clic en **+ Registrar Desviación**, se abre el modal de captura en **3 secciones**:

1. **Sección 1 — Identificación de Material, Lote y Ubicación:**
   - **Tipo de Material:** Selección rápida por chips (*Producto Terminado (PT)*, *Embalajes / Empaque*, *Café Verde*, *Otro Material*).
   - **Catálogo Sugerido de Materiales:** Autocompleta SKU, descripción, tipo de material y costo base.
   - **Campos obligatorios de trazabilidad:** *No. Remisión / Factura*, *SKU del Producto* y *Código UA / SSCC (18 dígitos)*.
   - **Fecha del Hallazgo**, **Hora** y **Bahía / Ubicación de Origen** (ej. `F18`, `K-36`, `M1-01`).

2. **Sección 2 — Condición Detectada y Análisis de Causa Raíz:**
   - **Condición Física / Tipo de Falla (7 opciones):** *Pallet con daños (producto expuesto / escurrimiento)*, *Pallet visualmente inestable*, *Producto con plaga evidente*, *Frasco roto (culinarios / envases)*, *Humedad / Producto mojado*, *Tarima en mal estado* u *Otra desviación de condición*.
   - **Motivo del Daño / Causa Raíz (7 opciones):** *Manejo inadecuado (Mala maniobra montacargas/patín)*, *Condiciones de infraestructura (Desnivel / Gotera)*, *Plagas o fauna nociva*, *Limpieza y mantenimiento insuficientes*, *Transporte interno defectuoso (Patín/Montacargas)*, *Problemas en el empaque original del proveedor* u *Otro motivo de causa raíz*.
   - **Área de Detección / Origen:** *Operaciones / Almacén*, *Aseguramiento de Calidad*, *Supply Chain / Logística* o *Seguridad e Higiene (SHE)*.
   - **Colaborador Involucrado / Operador.**

3. **Sección 3 — Impacto en Piezas, Costo Monetario y Acción FSM:**
   - **Piezas Físicas Dañadas (No Pallets)** y **Costo No Calidad ($ MXN)**.
   - **Acción Inmediata / Resolución FSM (6 opciones):** *Rechazo de producto (No ingreso)*, *Bloqueo preventivo (Cuarentena QM)*, *Acondicionamiento / Reempacado*, *Devolución formal al proveedor*, *Destrucción / Merma final* u *Otra acción correctiva*.
   - **Descripción Detallada del Incidente** y carga directa de **Evidencia Fotográfica** (múltiples imágenes desde cámara móvil o equipo).

Cada registro en la tabla ofrece dos acciones:
- **Ver:** Abre el **Expediente Técnico de Calidad** completo con trazabilidad, diagnóstico, métricas de impacto, observaciones y galería fotográfica digitalizada.
- **Imprimir:** Genera la **Cédula Oficial de Desviación de Calidad (`DEV-PO-QM-01`)** con sellos QR del *Auditor de Calidad (QM)* y el *Responsable de Operaciones / Almacén*, permitiendo **Descargar PDF** (`<FOLIO>_DEV-PO-QM-01.pdf`) o **Imprimir**.

#### 5.6.4. Exportación Mensual a Excel Corporativo (`.xlsx`) y CSV

Al hacer clic en **Exportar Reporte Mensual**, 4GUARD WMS genera y descarga automáticamente el libro de trabajo corporativo **`4GUARD_WMS_Reporte_Calidad_KPIs_AAAA-MM.xlsx`** estructurado en **4 hojas temáticas** con formato ejecutivo, códigos de color semafóricos y filas de totales:

| Hoja del Libro Excel | Contenido Incluido |
| :--- | :--- |
| **1. Tablero 10 KPIs** | Encabezado institucional del periodo, 8 tarjetas de resumen ejecutivo (Costo Total No Calidad, Total Piezas Dañadas, Lotes Inspeccionados F01, PT, Embalajes, Café Verde, Desviaciones y Resolución), matriz oficial de los **10 KPIs** con estatus semafórico y barras gráficas de cumplimiento, más tablas analíticas de **Distribución por Causa Raíz (Pareto)** y **Desglose por Material**. |
| **2. Cédula Desviaciones** | Listado completo de desviaciones del mes (`DEV-PO-QM-01`) en 18 columnas detalladas (Folio, Fecha, Hora, Remisión, SKU, Descripción, UA/SSCC, Tipo Material, Bahía, Unidades Dañadas, Costo MXN, Condición Física, Causa Raíz, Área, Colaborador, Acción Correctiva, Observaciones y Estatus) con fila final de sumatorias. |
| **3. Reclamos e Incidencias** | Expedientes completos de reclamos (`F02-PO-GC-8.6-04`) en 16 columnas con totales consolidados de piezas dañadas, merma y costo asociado en MXN. |
| **4. Verificaciones F01** | Registro consolidado de las verificaciones de carga `F01-PO-GC-8.6-03` emitidas durante el periodo. |

En caso de consulta directa al servidor sin tablero cargado en memoria, el sistema dispone de una exportación de respaldo en formato **CSV UTF-8 con BOM** (`reporte-kpis-calidad-AAAA-MM.csv`), compatible nativamente con Microsoft Excel en Windows y sanitizada contra inyección de fórmulas.
