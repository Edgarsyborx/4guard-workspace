## Capítulo 4. Consulta de Inventarios, Topología 2D, Layout y Secciones

Este capítulo documenta de forma integral los cuatro módulos de gobierno de existencias e infraestructura física de **4GUARD WMS**:

1. **Consulta de Inventarios y Exportación Excel**: visibilidad en tiempo real de todas las tarimas y piezas en stock, aplicación de filtros multicriterio, control de caducidades bajo la *Regla de Pablo*, analítica histórica anual y generación de reportes oficiales en formato `.xlsx`.
2. **Catálogo por Almacén / Topología 2D**: plano espacial interactivo de las 12 naves oficiales del almacén, mapa térmico de ocupación, consulta tabular de bahías, Inspector de Posición FSM, Bloqueo QM, alta individual y generación paramétrica de racks.
3. **Gestión de Layout y Ubicaciones Físicas**: explorador jerárquico maestro, administración detallada de coordenadas físicas, tipos estructurales, capacidades, máquina de estados con motivo obligatorio y auditoría histórica.
4. **Secciones de Almacén**: administración de la zonificación lógica por sucursal, activación de naves en el plano 2D, control de vigencia y trazabilidad de cambios.

---

### 4.1 Consulta de Inventarios y Exportación a Excel

El módulo **Consulta de Inventarios y Exportación Excel** (ruta operativa `/inventory-query`) constituye la fuente oficial de verdad para auditar existencias físicas, rastrear lotes y remisiones, prevenir mermas por caducidad y extraer reportes gerenciales compatibles con Excel. Su información se sincroniza de manera automática con las recepciones físicas registradas, los lotes consolidados en almacén y los traspasos o cambios de ubicación confirmados.

![Consulta de Inventarios](screenshots/10-inventory-query.png)

> [!TIP]
> Desde la cabecera superior del módulo puede regresar en cualquier momento al tablero principal haciendo clic en el enlace **Dashboard**, o sincronizar existencias frescas directamente desde la base de datos mediante el botón **ACTUALIZAR STOCK**.

#### 4.1.1 Panel de Indicadores Clave (4 Tarjetas KPI)

En la parte superior de la pantalla se despliegan cuatro tarjetas de resumen que se recalculan en tiempo real cada vez que usted aplica o limpia un filtro avanzado:

| # | Tarjeta KPI | Unidad / Etiqueta | Qué mide y cómo interpretarlo |
| :-: | :--- | :--- | :--- |
| 1 | **Total Palets Registrados** | `Tarimas` | Sumatoria total de tarimas (con precisión de hasta dos decimales) correspondientes al conjunto de registros filtrados actualmente. |
| 2 | **Total Piezas en Stock** | `Piezas (PZ)` | Volumen acumulado de piezas medidas en todas las tarimas visibles bajo los criterios de consulta vigentes. |
| 3 | **Caducidad <30 Días** | `Alerta Pablo` | Conteo inmediato de tarimas cuya fecha de caducidad vence dentro de los próximos 30 días naturales. Se resalta en color ámbar preventivo para priorizar su despacho FEFO (*First Expired, First Out*). |
| 4 | **Promedio de Estadía** | `Días Prom.` | Promedio redondeado de días naturales que las tarimas mostradas han permanecido almacenadas desde su fecha de ingreso físico. |

#### 4.1.2 Barra de Herramientas y Buscador Predictivo Inteligente

Debajo de las tarjetas KPI se ubica la barra operativa principal, compuesta por el **buscador predictivo** a la izquierda y cinco botones de acción a la derecha:

1. **Buscador predictivo multi-palabra**:
   - Permite escribir uno o varios términos separados por espacio (por ejemplo, `frasco nescafé` o `Almacén A 89201`). El sistema exige que cada registro coincida con **todas** las palabras escritas, buscando simultáneamente en: número de tarima (`NO.`), SKU, descripción del producto, remisión, ubicación física, almacén/bodega, proveedor y cliente.
   - Al escribir **2 o más caracteres**, se despliega automáticamente el panel **Sugerencias Predictivas ("Comienza con" / Palabras clave)** con hasta **10 recomendaciones** clasificadas por tipo e ícono, indicando cuántas tarimas coinciden con cada una:
     - **SKU**: coincidencias exactas o parciales de código de producto y su descripción.
     - **Palabra clave (`KEYWORD`)**: términos frecuentes presentes en descripciones, bodegas o códigos.
     - **Bodega (`WAREHOUSE`)**: naves o almacenes con stock disponible.
     - **Remisión (`REVISION`)**: folios de factura o remisión registrados.
   - Al hacer clic en una sugerencia, el término se aplica de inmediato sobre la tabla y restablece la paginación a la página 1. Puede limpiar la búsqueda en un clic con el botón **×**.

2. **Botones de acción de la barra**:
   - **ACTUALIZAR STOCK**: solicita una recarga fresca al servidor de recepciones, lotes de inventario y transferencias confirmadas.
   - **FILTROS AVANZADOS**: abre el modal de Filtros Multicriterio. Cuando existe alguna regla activa (Regla de Pablo distinta de General, SKUs o bodegas seleccionadas), muestra un punto ámbar parpadeante como recordatorio visual.
   - **VER ANALYTICS INGRESOS**: abre el modal de Analítica Histórica posicionado en la pestaña **Tabla de Trazabilidad**.
   - **GENERAR GRÁFICA DE AÑOS**: abre el modal de Analítica Histórica directamente en la pestaña **Gráficas Anuales Comparativas**.
   - **EXPORTAR A EXCEL (21 COLS)**: descarga el archivo `.xlsx` oficial. Si usted tiene filas marcadas mediante casillas de verificación, la etiqueta cambia dinámicamente a **EXPORTAR SELECCIÓN (N)**.

3. **Barra de Chips de Filtros Activos**:
   - Siempre que haya una búsqueda rápida, filtros multi-SKU, multi-bodega, estados de caducidad o una Regla de Pablo activa, aparece una franja con etiquetas removibles de forma individual (botón **×** en cada chip) y un botón global **Limpiar Todo** que restablece filtros, búsqueda y selección múltiple en un solo paso.

#### 4.1.3 Tabla Interactiva, Selección Múltiple y Barra Flotante

La tabla principal muestra los registros paginados de **10 en 10 tarimas** (con botones **Anterior** y **Siguiente**, más indicador de página actual y total de registros).

- **Selección individual**: haga clic en la casilla de verificación o en cualquier parte de la fila para marcar o desmarcar una tarima.
- **Casilla maestra (encabezado)**: permite seleccionar o deseleccionar todas las tarimas filtradas de una sola vez; muestra un estado intermedio (guion) cuando la selección es parcial.
- **Barra Flotante de Selección Múltiple**: al seleccionar al menos una tarima, emerge en la parte inferior una barra fija que muestra en tiempo real:
  - Cantidad de **Tarima(s) Seleccionada(s)**.
  - Sumatoria de **Piezas (`PZ`)**, cantidad de **SKU(s) distintos** y total de **Pallets** seleccionados.
  - Acciones rápidas: **Seleccionar Todo (N)** (marca todos los registros del filtro actual), **Invertir** (intercambia seleccionadas por no seleccionadas), **Limpiar Selección** y **Exportar Selección (N)**.

#### 4.1.4 Diccionario Completo de las 21 Columnas Oficiales

Aunque la vista en pantalla condensa campos relacionados para facilitar la lectura sin desplazamiento horizontal excesivo (por ejemplo, agrupando `NO.` con `ETIQUETA UA`, `SKU` con `DESCRIPCIÓN PRODUCTO`, `ALMACÉN` con `UBICACIÓN`, y `PROVEEDOR` con `CLIENTE`), cada registro de inventario en **4GUARD WMS** mantiene intactas las **21 columnas operativas oficiales**, las cuales se incluyen íntegras en la exportación a Excel:

| # | Nombre Oficial de Columna | Formato / Ejemplo | Significado Operativo, Origen y Badges |
| :-: | :--- | :--- | :--- |
| 1 | **NO.** | `#563633` | Identificador consecutivo histórico del palet dentro del WMS. En pantalla se muestra en dorado resaltado. |
| 2 | **FECHA INGRESO** | `AAAA-MM-DD` (ej. `2026-04-12`) | Fecha física en que la mercancía ingresó al almacén (derivada de la recepción o alta del lote). |
| 3 | **FECHA ELABORACIÓN** | `AAAA-MM-DD` (ej. `2026-02-01`) | Fecha de fabricación del lote declarada por el proveedor o fabricante. |
| 4 | **FECHA CADUCIDAD** | `AAAA-MM-DD` o `N/A` | Fecha límite de consumo o vigencia del producto. Es la base de cálculo para la Regla de Pablo. |
| 5 | **DÍAS VIDA ÚTIL** | Número entero (ej. `365`) | Total de días naturales de vida útil del producto calculados entre `FECHA CADUCIDAD` y `FECHA ELABORACIÓN`. |
| 6 | **REMISIÓN** | Alfanumérico (ej. `REM-89201`) | Número de factura o remisión documental con la que arribó la mercancía. |
| 7 | **LOTE FABRICANTE** | Alfanumérico (ej. `LOT-2026-A04`) | Clave de trazabilidad asignada por la planta productora. Muestra `S/L` si no aplica lote. |
| 8 | **ETIQUETA UA** | Código SSCC (ej. `001750105860012345`) | Código de barras único de la Unidad de Almacenamiento (tarima) usado para escaneo en terminal móvil y montacargas. En pantalla se visualiza debajo del `NO.` cuando corresponde a un SSCC real. |
| 9 | **SKU** | Código único (ej. `SKU-NEST-001`) | Clave maestra del artículo en el catálogo de productos. |
| 10 | **DESCRIPCIÓN PRODUCTO** | Texto descriptivo | Nombre comercial y presentación del artículo (ej. *NESCAFÉ CLÁSICO FRASCO 48x200G*). |
| 11 | **FOLIO RECEPCIÓN** | Folio WMS (ej. `REC-2026-000142`) | Folio interno de la orden de recepción o traspaso que originó la entrada del palet. |
| 12 | **CANTIDAD MEDIDA** | Entero con separador (ej. `1,200 PZ`) | Número de piezas físicas contenidas en la tarima o registro. |
| 13 | **PALLETS** | Decimal a 2 dígitos (ej. `1.00`) | Equivalente en tarimas del registro (normalmente `1.00` por tarima individualizada). |
| 14 | **ESTADÍA** | `N días` (ej. `18 días`) | Días naturales transcurridos desde `FECHA INGRESO` hasta la fecha actual. |
| 15 | **ALMACÉN** | Nombre de nave (ej. `Almacén A`, `Andén / Tránsito`, `Bodega APC`) | Nave o bodega resuelta automáticamente a partir del prefijo de la ubicación física (`POS-A-...` → `Almacén A`, `STAGING`/`RAMP` → `Andén / Tránsito`, etc.). |
| 16 | **NÚMERO / UBICACIÓN** | Código físico (ej. `POS-A-012`, `A-02-14`, `STAGING-01`) | Posición exacta donde descansa actualmente la tarima. Se actualiza en vivo cuando se confirma un Cambio de Almacén. |
| 17 | **NO. ENTRADA** | Prefijo + folio (ej. `ENT-REC-042`, `STK-563633`, `TR-CAM-09`) | Referencia de control físico de entrada según el origen del registro: lote consolidado (`STK-`), recepción directa (`ENT-`) o reubicación/traspaso (`TR-`). |
| 18 | **TIPO TARIMA** | Etiqueta catálogo oficial | Clasificación física de la base de estiba. Acepta los **7 tipos oficiales de 4GUARD WMS**: `MADERA OWENS`, `MADERA ESTANDAR`, `PLASTICO NEGRO OWENS`, `PLASTICO AZUL`, `TARIMA CHEP NACIONAL`, `TARIMA CHEP EXPORTACION` y `TARIMA PLASTICO NEGRO ESTANDAR`. |
| 19 | **ETIQUETADA** | `ETIQUETADO` / `PENDIENTE` / `OBSOLETO` | Estado de identificación física de la tarima con etiqueta UA/SSCC adherida y verificada. |
| 20 | **PROVEEDOR** | Razón social / nombre | Nombre del proveedor o línea transportista asociada al ingreso. |
| 21 | **VIDA RESTANTE Y CLIENTE** | `Nd \| Nombre Cliente` (ej. `45d \| Nestlé México`) | En la exportación oficial reúne los días faltantes para caducar (`VIDA RESTANTE`) y el propietario comercial (`CLIENTE`). En la tabla web, la vida restante se presenta dentro del **Badge de Estado de Caducidad** y el cliente aparece junto al proveedor (`Cli: ...`). |

#### 4.1.5 Regla Crítica de Caducidad (Regla de Pablo)

Para eliminar el riesgo de embarcar producto vencido o con vida de anaquel insuficiente, **4GUARD WMS** calcula automáticamente los días restantes de vigencia (`Fecha de Caducidad − Fecha Actual`) y asigna a cada tarima un semáforo operativo conocido internamente como **Regla de Pablo**:

| Semáforo / Badge en Pantalla | Condición Matemática | Significado Operativo | Acción Recomendada |
| :--- | :--- | :--- | :--- |
| 🟢 **En Tiempo (`Nd`)** | Más de 30 días restantes (`> 30 días`) | Stock sano con margen comercial suficiente. | Almacenamiento y rotación estándar FEFO. |
| 🟡 **Próximo <30 Días (`Nd`)** | Entre 1 y 30 días restantes (`1 a 30 días`) | Alerta preventiva (**Alerta Pablo**). Incrementa el KPI superior *Caducidad <30 Días*. | Priorizar asignación a embarques inmediatos o notificar al área comercial/cliente. |
| 🔴 **Caduco (`≤0d`)** | Cero o menos días restantes (`≤ 0 días`) | Producto vencido o que caduca el día de hoy. | Aislar físicamente, aplicar **Bloqueo QM** en Topología 2D y gestionar dictamen de destrucción o devolución. |

> [!IMPORTANT]
> La Regla de Pablo funciona de forma acumulativa con el resto de los filtros. Por ejemplo, puede combinar **Próximo 30 Días** con una **Bodega** específica y un **Cliente** para emitir en segundos un reporte de riesgo de caducidad para ese cliente.

#### 4.1.6 Modal de Filtros Multicriterio (Campo por Campo)

Al presionar **FILTROS AVANZADOS** se abre el modal **Filtros Multicriterio de Consulta de Inventario**, organizado en cinco secciones:

1. **Sección 1 — Regla Crítica de Estado de Caducidad (Pablo)** *(selección única por botones de radio)*:
   - **⚪ Caducidad General**: muestra la totalidad del stock activo (opción predeterminada).
   - **🔴 Producto Caduco**: filtra exclusivamente tarimas con fecha de caducidad menor o igual a hoy.
   - **🟡 Próximo 30 Días**: filtra exclusivamente tarimas en ventana de alerta de los próximos 30 días.

2. **Sección 2 — Rangos de Fechas (Desde / Hasta)** *(selectores de calendario)*:
   - **Fecha de Ingreso**: campos `Desde` y `Hasta`.
   - **Fecha de Elaboración**: campos `Desde` y `Hasta`.
   - **Fecha de Caducidad**: campos `Desde` y `Hasta`.
   - *Nota*: puede llenar solo `Desde`, solo `Hasta`, o ambos extremos en cualquiera de los tres pares de fechas.

3. **Sección 3 — Selección Múltiple Avanzada (Múltiples SKUs y Bodegas)**:
   - **Filtrar por Uno o Más SKUs**: lista interactiva de chips con casilla de verificación y conteo de tarimas por SKU, acompañada de un buscador interno (`Buscar SKU...`) y un contador en vivo de SKUs marcados.
   - **Filtrar por Una o Más Bodegas / Almacenes**: lista de chips seleccionables con todas las bodegas que tienen stock y su cantidad de tarimas.

4. **Sección 4 — Textos y Buscadores Operativos** *(coincidencia parcial sin distinción de mayúsculas/minúsculas)*:
   - **Factura / Remisión** (ej. `REM-89201`).
   - **SKU Específico** (ej. `SKU-NEST-001`).
   - **Producto / Descripción**: cuenta con lista desplegable de **autocompletado** alimentada con las descripciones reales en inventario.
   - **Ubicación (Letra / Número)**: convierte automáticamente el texto a mayúsculas (ej. `A-02-14` o `POS-A-012`).
   - **Proveedor** (ej. `Nestlé México`).
   - **Cliente** (ej. `Comercializadora Alpura`).

5. **Sección 5 — Clasificación de Tarimas y Etiquetado** *(listas desplegables)*:
   - **Tipo Tarima (7 Oficiales 4Guard)**: `-- Todos los Tipos de Tarima --` o cualquiera de los 7 tipos maestros oficiales.
   - **Estado de Etiquetado**: `-- Todos los Estados --`, `ETIQUETADO`, `PENDIENTE` u `OBSOLETO`.

**Botones del pie del modal**:
- **LIMPIAR FILTROS**: borra todos los campos, desmarca todos los SKUs y bodegas, regresa la Regla de Pablo a `Caducidad General` y restaura el inventario completo.
- **Cancelar**: cierra el modal sin aplicar modificaciones pendientes.
- **GENERAR CONSULTA**: aplica inmediatamente todos los criterios seleccionados sobre la tabla y los KPIs, y cierra el modal.

#### 4.1.7 Modal de Analítica Histórica y Gráficas Anuales

Accesible desde **VER ANALYTICS INGRESOS** o **GENERAR GRÁFICA DE AÑOS**, este modal agrupa todo el stock activo según el año de `FECHA INGRESO` y el rango consecutivo de folios de palet (`#563633` y posteriores). Cuenta con un indicador de **Años Analizados** y dos pestañas:

1. **Pestaña 1 — Tabla de Trazabilidad**:
   - Presenta una tabla resumida por año (ordenado del más reciente al más antiguo) con cinco columnas: **Año Ingreso**, **Rango de Folios (`#`)**, **Total Palets**, **Total Piezas (`PZ`)** y **Porcentaje Stock (`%`)**.
2. **Pestaña 2 — Gráficas Anuales Comparativas**:
   - Incluye un **Selector de Métrica** para alternar instantáneamente la escala de las barras entre **Palets Registrados** (color dorado) y **Volumen en Piezas (PZ)** (color verde esmeralda).
   - Cada barra muestra el año, el rango de folios, la cantidad de palets, las piezas totales, el porcentaje de participación sobre el stock global y una barra horizontal proporcional animada.
   - En la parte inferior presenta **tres tarjetas de resumen ejecutivo**:
     - **Año Dominante**: identifica el año que concentra el mayor volumen de palets y su porcentaje del stock activo.
     - **Secuencia de Folios**: destaca el rango consecutivo histórico validado.
     - **Volumen Total Acumulado**: consolida el total global de palets y piezas activas.
   - El botón **GENERAR GRÁFICA DE AÑOS** (en cabecera y pie) recalcula y actualiza la visualización con animación de refresco; **Cerrar Analítica** retorna a la consulta principal.

#### 4.1.8 Procedimiento Operativo Estándar (SOP): Exportación a Excel (`.xlsx`)

Siga este procedimiento para generar reportes oficiales auditables en Excel:

1. **Defina el alcance del reporte**:
   - *Opción A — Exportación Total Filtrada*: aplique los filtros deseados (o deje la vista completa sin filtros) y asegúrese de no tener filas marcadas con casilla de verificación. El botón verde mostrará **EXPORTAR A EXCEL (21 COLS)**.
   - *Opción B — Exportación Parcial por Selección*: marque con la casilla de verificación únicamente las tarimas que desea incluir (puede combinar búsquedas, filtros y botones de la barra flotante como *Invertir* o *Seleccionar Todo*). El botón cambiará a **EXPORTAR SELECCIÓN (N)**.
2. **Ejecute la descarga**: haga clic en el botón verde de exportación (ya sea en la barra superior o en la barra flotante inferior).
   - *Validación de seguridad*: si el filtro actual arroja cero registros, el sistema le avisará con el mensaje *"No hay datos en pantalla para exportar a Excel"* y evitará descargar archivos vacíos.
3. **Verifique la estructura del archivo generado**:
   - **Nombre del archivo**: `4GUARD_WMS_Consulta_Inventarios_AAAA-MM-DD.xlsx` (con la fecha del día).
   - **Nombre de la hoja**: `Consulta_Inventario_4GUARD`.
   - **Columnas**: contiene exactamente las **21 columnas oficiales** con encabezados en mayúsculas y anchos de columna preajustados para lectura inmediata sin truncamiento.
   - **Fila final de totales (`TOTALES 4GUARD`)**: se añade automáticamente al cierre de la hoja con la leyenda `SUMATORIA GLOBAL DE STOCK` en la columna de descripción, la suma exacta de `CANTIDAD MEDIDA` (piezas), la suma exacta de `PALLETS` (redondeada a 2 decimales) y el conteo total de registros exportados (`N Regs`) en la columna final.

---

### 4.2 Catálogo por Almacén y Topología 2D

El módulo **Catálogo por Almacén / Topología** (ruta `/catalogs/warehouse`) ofrece una representación fiel del plano arquitectónico real de la planta y permite auditar, inspeccionar, bloquear, dar de alta o generar posiciones físicas en tiempo real.

La cabecera superior exhibe el indicador en vivo **TOPOLOGÍA SINCRONIZADA**, la referencia **12 NAVES REGISTRADAS** y dos pestañas de trabajo: **1. Mapa Visual de Topología** y **2. Consulta de Bahías**.

![Topología de Almacén - Mapa 2D](screenshots/19-catalogs-warehouse.png)

#### 4.2.1 Las 12 Naves Oficiales y Zona de Andenes

La topología maestra contempla **12 naves productivas** identificadas por letra: **A, B, C, D, E, F, G, H, I, K, L y M** (nótese que no existe nave J en la nomenclatura operativa de las 12 naves activas). Adicionalmente, el plano arquitectónico integra:

- **Rampas 1 y 2 (Andén de Maniobras)**: área demarcada con franjas industriales amarillo-negro en el patio noreste para referencia operativa (sin inventario permanente).
- **12 Bahías de Staging Temporal en Cortinas de Rampa (`STAGING-R01` a `STAGING-R12`)**: zonas de piso vinculadas a las recepciones en curso. Cuando una rampa tiene descarga activa, la bahía se ilumina con un indicador morado y el número de tarimas en andén (`NT`), emitiendo una alerta preventiva para recordar su traslado a nave fija.

#### 4.2.2 Las 5 Métricas KPI Operativas (Vista Expandida y Compacta)

Justo debajo de la cabecera se muestran cinco indicadores globales de capacidad y estado del almacén. Mediante el botón **Compactar Vista / Expandir Métricas** puede alternar entre tarjetas detalladas y una barra horizontal compacta para maximizar el área visible del plano 2D:

| # | Métrica KPI | Referencia / Valor Base | Interpretación Operativa |
| :-: | :--- | :--- | :--- |
| 1 | **Total Posiciones** | `1,702` (`1,180 Fijas · 474 Temp · 48 Pre`) | Universo total de bahías físicas registradas, desglosado en posiciones fijas nominales, temporales de sobrecupo y precarga. |
| 2 | **Naves Activas** | `12/12` (`A, B, C, D, E, F, G, H, I, K, L, M`) | Cantidad de naves inicializadas y operativas en el plano 2D. |
| 3 | **Ocupación Actual** | Conteo en vivo (`Base Nominal: 25,960 Tarimas`) | Número de posiciones físicas que actualmente contienen mercancía. |
| 4 | **Bloqueadas QM** | Conteo en vivo (`Cuarentena / Inspección`) | Posiciones inhabilitadas por Control de Calidad (QM) o cuarentena. |
| 5 | **Buffer Extra** | `474` (`+10,428 T en Temporales`) | Capacidad elástica disponible en ubicaciones temporales para absorber picos estacionales. |

#### 4.2.3 Pestaña 1: Mapa Visual de Topología (Plano Interactivo 2D)

##### Barra de Secciones, Capas y Mapa Térmico

Encima del lienzo interactivo se encuentra la barra **SECCIONES DEL ALMACÉN**:

- **Chips de Naves (`A`–`M`)**: muestran el código de cada nave y su total de posiciones fijas (o etiqueta `Pendiente` si aún no ha sido inicializada). Al hacer clic en cualquier chip, la cámara hace auto-zoom sobre esa nave y abre su panel lateral.
- **Control de Capas**:
  - **Bahías Visibles / Ocultas**: muestra u oculta los rectángulos individuales de cada bahía dentro de los racks.
  - **Rutas Conectadas**: activa o desactiva los ejes centrales de pasillos (`P-01`, `P-02`...) y sus enlaces hacia los corredores principales de tránsito de montacargas.
  - **+ Generar Bahías**: abre el asistente del Generador Paramétrico de Bahías.
- **Simbología del Mapa Térmico**:
  - **Vacío (`0 T`)**: blanco técnico translúcido — bahía completamente libre.
  - **Baja (`1–30%`)**: verde esmeralda — amplia capacidad disponible.
  - **Medio (`31–80%`)**: amarillo / ámbar — ocupación operativa media o picking activo.
  - **Lleno (`81–100%`)**: azul zafiro — bahía saturada o de alta densidad.
  - **Bloqueado QM**: rojo — posición retenida por Control de Calidad, cuarentena o alerta crítica por sobrecupo.
  - **Buffer**: morado — posición temporal de piso/descarga con mercancía activa.
  - **Precarga / Staging**: dorado — bahía de precarga de salida con tarimas asignadas.

##### Navegación Espacial y Paneles Flotantes (HUD)

El lienzo 2D incorpora herramientas de precisión estilo plano técnico:

- **Movimiento y Zoom**: arrastre con el ratón para desplazar el plano (*pan*), use la rueda del ratón para acercar o alejar (desde **40%** hasta **500%**), o utilice la barra flotante de controles (`+`, indicador `%`, `-` y botón **Centrar vista**). El plano incluye una **Rosa de los Vientos** (N/S/E/O) y la cartela técnica **PLANO DE PLANTA**.
- **HUD Izquierdo — Radar de Naves**: panel colapsable que lista las 12 naves con su porcentaje de ocupación en tiempo real, barra de progreso (que cambia a color de alerta al superar el **85%**) y conteo de posiciones. En el pie informa el total de posiciones activas y ocupadas.
- **HUD Derecho — Capas CAD & Telemetría** *(visible en la vista general sin nave seleccionada)*:
  - **Presets de Zoom**: accesos de un clic a `85%`, `100%`, `150%`, `220%` y `Ajustar`.
  - **Capas del Blueprint**: interruptores rápidos para `Bahías & Racks` y `Rutas de Tráfico`.
  - **Ficha Técnica de Planta**: muestra los datos arquitectónicos de referencia — Superficie Total **28,450 m²**, Altura Libre **12.50 m**, Andenes/Docks **14 Cortinas** y Protección Incendio **ESFR K-25**.
  - Botón directo **Generar Bahías Paramétricas**.
- **Tooltips Reactivos**: al pasar el cursor sobre una nave se muestra su nombre, estatus (`ACTIVO`/`PENDIENTE`), posiciones, capacidad en tarimas, factor de estiba y material principal. Al pasar el cursor sobre una bahía específica se muestra código, pasillo/rack, ocupación (`actual/capacidad T`), material, lote y, si existe, el banner de **Alerta Crítica** o **Alerta Preventiva**.

##### Panel Lateral de Detalle de Nave (Drill-Down)

Al hacer clic en cualquier polígono de nave o en su chip superior, aparece un **HUD superior de control** (con botones **Centrar** y **Ver Almacén Completo**) y se despliega a la derecha el panel de detalle de la nave:

- **Acciones de cabecera**: botón **+ Nueva Posición**, ícono de engranaje **Configurar Parámetros de Nave** y botón cerrar (`×`).
- **Cinta de 4 métricas de nave**: `100% FIJAS` (bahías y capacidad), `TEMPORALES` (bahías y capacidad), `OCUPACIÓN` (porcentaje nominal y ocupadas/totales) y `PRECARGA` (cantidad de bahías).
- **Búsqueda y filtros segmentados**:
  - Buscador rápido por código (`POS-A-001`, `POS-A-T01`), SKU o lote.
  - **Chips de Pasillos**: `Todos`, `P-01`, `P-02`, etc., mostrando cuántas bahías pertenecen a cada pasillo.
  - **Chips de Categoría**: `Todas`, `Fijas`, `Temporales` (`TEMP`), `Precarga` (`PRE`).
  - **Chips de Estado**: `Todos`, `Libres`, `Ocupadas`, `Bloqueadas`.
- **Cuadrícula de tarjetas con resaltado bidireccional**: pasar el cursor sobre una tarjeta ilumina su bahía en el mapa 2D y viceversa. Cada tarjeta exhibe código, badge de categoría y estado, material o estado libre/bloqueado, lote, ocupación (`actual/capacidad T`) y banners automáticos de alerta:
  - **Alerta Crítica (Rojo)**: cuando la posición está `Bloqueada QM` o presenta **Sobrecupo Crítico** (tarimas actuales mayores a la capacidad máxima).
  - **Alerta Preventiva (Amarillo)**: cuando la ocupación alcanza o supera el **85%**, o cuando un **Buffer Temporal** contiene mercancía pendiente de traslado a nave fija.

#### 4.2.4 Pestaña 2: Consulta de Bahías (Auditoría Tabular)

Al seleccionar la pestaña **2. Consulta de Bahías** se despliega un directorio tabular de alto volumen diseñado para auditorías rápidas y búsquedas globales en todo el almacén.

![Topología de Almacén - Consulta de Bahías](screenshots/19c-catalogs-bahias.png)

- **Barra de Filtros Combinables**:
  1. **Buscar Código de Bahía, SKU o Lote** (ej. `POS-020`, `43211385`, `LOTE-2026`).
  2. **Filtrar por Almacén Real**: `Todos los Almacenes` o cualquiera de las 12 naves.
  3. **Tipo de Bahía / Categoría**: `Todas las Categorías`, `Fijas Nominales (100% Base)`, `Temporales (Buffer Extra / Sobrecupo)` o `Precarga / Rampa / Staging`.
  4. **Estado de Disponibilidad**: `Todos los Estados`, `Disponibles (Libres)`, `Ocupadas` o `Bloqueadas QM (Cuarentena)`.
  5. Botón directo **+ Nueva Posición**.
- **Columnas de la tabla**:
  - **Código / ID Bahía** (con distintivo `TEMP` o `PRE` cuando aplica).
  - **Almacén / Nave**.
  - **Capacidad / Estiba** (`ocupadas / capacidad Tarimas`).
  - **Material / SKU** (o `— Vacante —` si está libre).
  - **Lote Asignado**.
  - **Estado Operativo**: `Disponible` (verde), `Parcial` (ámbar, ocupación > 0% y < 100%), `Ocupada` (azul, saturada al 100%) o `Bloqueada QM` (rojo).
  - **Último Movimiento**.
  - **Acción**: botón **Inspeccionar** que abre el Inspector de Posición FSM.

#### 4.2.5 Inspector de Posición FSM y Procedimiento de Bloqueo QM

Al hacer clic en cualquier bahía del mapa 2D, en una tarjeta del panel lateral o en el botón **Inspeccionar** de la tabla de bahías, se abre el **Inspector de Posición FSM**:

1. **Información visible**: código y nave, identificador interno, categoría (`Fija Nominal (100%)`, `Temporal Buffer` o `Precarga Staging`), estado actual, material asignado, código SKU, lote asignado, capacidad/estiba, último movimiento y, en caso de estar retenida, el recuadro de advertencia con el motivo del bloqueo.
2. **Acciones de Estado Operativo (FSM)**:
   - **Liberar**: cambia la posición a `Disponible` (`0` tarimas) y elimina cualquier bloqueo QM previo. Se deshabilita si ya está disponible.
   - **Ocupar**: marca la posición como `Ocupada` a su capacidad nominal. Se deshabilita si ya está ocupada.
   - **Bloquear QM**: abre el formulario **Registrar Bloqueo QM**.
3. **SOP de Bloqueo QM (Control de Calidad / Cuarentena)**:
   - Haga clic en **Bloquear QM**. El sistema muestra el aviso: *"Esta posición quedará inhabilitada hasta que sea liberada por un supervisor."*
   - Seleccione un **Motivo de Bloqueo** obligatorio del catálogo oficial del almacén.
   - *(Opcional)* Capture **Observaciones del Supervisor** (folio de inspección, nombre del inspector, hallazgo de calidad).
   - Presione **Confirmar Bloqueo QM**. La bahía cambia inmediatamente a rojo en el mapa 2D, incrementa el KPI `Bloqueadas QM` y queda protegida contra asignaciones operativas.
4. **Modificación y Baja Individual**:
   - **Modificar Capacidad / Datos**: permite ajustar la **Capacidad en Tarimas** (`1` a `100`), cambiar la **Categoría Operativa** (`Fija Nominal`, `Temporal` o `Precarga`) y actualizar el **Material / SKU Predeterminado**.
   - **Dar de Baja Posición**: inhabilita/elimina la bahía previa confirmación del usuario.

> [!CAUTION]
> **Regla de protección de inventario**: El sistema impide dar de baja una posición que tenga **1 o más tarimas almacenadas**. Debe desocupar o transferir primero la mercancía antes de proceder con la baja.

#### 4.2.6 Alta de Posición Individual

Desde el botón **+ Nueva Posición** (disponible en el panel lateral de una nave o en Consulta de Bahías) se abre el modal **Registrar Nueva Posición de Almacén**:

| Campo | Obligatorio | Comportamiento y Reglas |
| :--- | :---: | :--- |
| **Almacén / Nave Destino** | Sí | Selecciona la nave donde se dará de alta la bahía. |
| **Categoría de Posición** | Sí | `Fija Nominal (100% Base)`, `Temporal (Buffer Extra / Sobrecupo)` o `Precarga / Rampa / Staging`. |
| **Código de Posición (Bahía)** | Sí | Se **sugiere automáticamente** en mayúsculas siguiendo el consecutivo real de la nave: `POS-X-001` (fijas), `POS-X-T01` (temporales) o `POS-X-PRE01` (precarga). |
| **Capacidad en Tarimas** | Sí | Valor numérico entre `1` y `100`. Hereda automáticamente el factor de estiba configurado en la nave (por defecto `22`). |
| **Pasillo / Rack / Nivel** | No | Coordenadas sugeridas automáticamente (`01`/`01`/`1` o `TMP`/`PRE` según categoría; nivel de `1` a `10`). |
| **Notas / Observaciones** | No | Texto libre para documentar contingencias o usos especiales. |

#### 4.2.7 Generador Paramétrico de Bahías y Conexión Topológica

Accesible desde **+ Generar Bahías** (barra superior del mapa) o **Generar Bahías Paramétricas** (HUD derecho), este asistente crea baterías completas de racks conectadas automáticamente a los carriles centrales de tránsito y a los muelles de carga `R1–R8`:

1. Seleccione la **Nave / Zona de Destino** (`A`–`M`) y el **Tipo de Ubicación** (`100% Fija Nominal`, `Buffer Temporal` o `Precarga / Docks de Salida`).
2. Defina los cuatro parámetros geométricos:
   - **Baterías / Pasillos**: de `1` a `8` pasillos de montacargas.
   - **Bahías por Pasillo**: de `2` a `50` columnas de rack por pasillo.
   - **Niveles de Estiba**: de `1` a `5` alturas (`N1` a `N5`).
   - **Capacidad por Bahía (Tarimas)**: de `1` a `60` tarimas.
3. Revise el cuadro **Total Posiciones Proyectadas**, que calcula al vuelo `Pasillos × Bahías × Niveles` posiciones y la capacidad máxima acumulada en tarimas.
4. Haga clic en **Generar Bahías y Conectar al Croquis**. Al finalizar, el sistema confirma la creación y abre automáticamente el detalle de la nave actualizada.

#### 4.2.8 Configuración y Parámetros de Nave

Al presionar el ícono **Configurar Parámetros de Nave** (engranaje) dentro de una nave, se abre el modal **Configurar y Activar Nave** (compartido con el módulo *Secciones de Almacén*):

- Muestra el aviso de **Geometría y Topología 2D fijas** (las coordenadas civiles del plano ya están pre-mapeadas).
- **Giro Operativo / Categoría** *(obligatorio)*: selector con 11 categorías estándar (*Secos & Producto Terminado*, *Culinarios & Salsas*, *Envase & Vidrio*, *Materia Prima & Insumos*, *Empaque & Vidrio Industrial*, *General Central & Palletizado*, *Insumos Especiales*, *Granel & Tambores*, *Cuarentena & Retenidos*, *Almacén Anexo Exterior* y *Área Técnica*).
- **Posiciones Fijas** *(obligatorio, 1 a 1,000)*: contador con botones `-` y `+` para definir el total de bahías/cajones.
- **Factor de Estiba** *(obligatorio)*: selector con estándares operativos (`40`, `30`, `22`, `20`, `15` o `10 tarimas/pos`).
- **Indicador de Capacidad Total en Vivo**: calcula automáticamente `Posiciones Fijas × Factor de Estiba = Capacidad Total en Tarimas`.
- **SKUs y Materiales Autorizados**: buscador y lista de chips seleccionables para restringir o autorizar qué productos pueden almacenarse en la nave.
- **Generar cuadrícula de ubicaciones físicas automáticamente**: casilla marcada por defecto que crea los registros consecutivos con nomenclatura estándar (`POS-X-001` hasta `POS-X-NNN`).
- **Notas u Observaciones Operativas**: instrucciones especiales, tipo de racks o restricciones.
- Presione **Guardar y Activar Nave** para confirmar y sincronizar la topología.

---

### 4.3 Gestión de Layout y Ubicaciones Físicas

El módulo **Gestión de Layout y Ubicaciones Físicas** (ruta `/layout`) es la consola administrativa maestra donde se configuran las ubicaciones individuales que utilizan todas las operaciones logísticas del WMS (recepción, acomodo, traspasos, surtido y embarque).

![Gestión de Layout y Ubicaciones](screenshots/11-layout.png)

La cabecera incluye acceso de retorno a **Administración WMS**, el botón principal **Nueva ubicación** y **4 tarjetas KPI** en tiempo real: **Total**, **Activas**, **Bloqueadas** y **Mantenimiento**.

#### 4.3.1 Explorador Jerárquico (Panel Izquierdo)

El panel izquierdo organiza todas las ubicaciones en un **árbol desplegable de hasta 4 niveles**:

`Zona / Sección` ➔ `Pasillo` ➔ `Rack` ➔ `Ubicación (Hoja)`

- **Buscador rápido**: filtra al instante por código, nombre descriptivo o nombre de zona.
- **Filtros combinables**: desplegable **Todas las zonas** y desplegable **Todos los estados** (`Activa`, `Bloqueada`, `Mantenimiento`, `Inactiva`).
- **Indicadores en nodos**: cada grupo (Zona, Pasillo, Rack) muestra su contador de ubicaciones hijas y **puntos de alerta de color** cuando contiene al menos una ubicación en estado *Bloqueada* o *Mantenimiento*.
- **Nodos hoja**: muestran el punto de color de estado, el código en formato monoespaciado y la etiqueta compacta de estado. Al hacer clic en una hoja se carga su ficha completa en el editor derecho. Si tenía cambios sin guardar en la ubicación anterior, el sistema muestra una advertencia preventiva.

#### 4.3.2 Editor de Ubicación: Diccionario de Campos y Validaciones

El panel derecho opera en modo **NUEVA UBICACIÓN** o **UBICACIÓN FÍSICA** (edición), mostrando la etiqueta `Cambios sin guardar` mientras existan modificaciones pendientes:

| Sección | Campo | Obligatorio | Formato, Validación y Comportamiento |
| :--- | :--- | :---: | :--- |
| **Identificación** | **Código** | Sí | Único en el almacén. Solo letras mayúsculas (`A–Z`), números (`0–9`) y guiones (`-`). En creación se **autogenera** al capturar zona y coordenadas (ej. `ALMC-A-R1-N1`) mientras no se edite manualmente. |
| **Identificación** | **Nombre** | Sí | Máximo `150` caracteres. En creación se **autogenera** de forma legible (ej. `Almacén A – Pasillo A – Rack R1 – Nivel 1`). |
| **Identificación** | **Almacén / Sucursal** | Sí | Lista de sucursales activas configuradas en el WMS. |
| **Identificación** | **Zona / Sección** | Sí | Lista filtrada automáticamente a las **secciones ACTIVAS** pertenecientes a la sucursal seleccionada (en edición conserva visible la sección asignada aunque haya sido desactivada). |
| **Coordenadas Físicas** | **Pasillo** | No | Identificador de pasillo (ej. `A`, `B`, `01`), convertido a mayúsculas. |
| **Coordenadas Físicas** | **Rack** | No | Identificador de estantería o batería (ej. `R1`, `R2`). |
| **Coordenadas Físicas** | **Nivel** | No | Altura o nivel de estiba (ej. `1`, `2`, `3`). |
| **Coordenadas Físicas** | **Posición** | No | Sub-posición o hueco dentro del nivel (ej. `P1`, `P2`). |
| **Clasificación y Capacidad** | **Tipo de Ubicación** | Sí | Define la estructura física: **`PALLET`** (*Pallet / Tarima*), **`BIN`** (*Bin / Gaveta*), **`SHELF`** (*Shelf / Estantería*) o **`RAMP`** (*Rampa*). |
| **Clasificación y Capacidad** | **Capacidad Máxima (unidades)** | Sí | Número entero mayor o igual a `1`. |
| **Ocupación (Solo Lectura)** | **Ocupadas / Uso % / Disponibles** | Solo lectura | Visible únicamente en modo edición. Muestra barra de progreso con semáforo: verde (`< 70%`), ámbar (`70%–89%`) y rojo crítico (`≥ 90%`). |
| **Observaciones** | **Observaciones / Notas de Operación** | No | Campo multilínea para restricciones especiales o instrucciones operativas. |

#### 4.3.3 Matriz de Estados FSM, Motivo Obligatorio y Eliminación

En modo edición, la sección **Cambiar Estado** gobierna el ciclo de vida de cada ubicación mediante una **Máquina de Estados Finitos (FSM)** estricta. Solo aparecen habilitados los botones correspondientes a las transiciones válidas desde el estado actual:

| Estado Actual | Etiqueta UI | Transiciones Permitidas | Botones Visibles | ¿Exige Motivo Obligatorio? |
| :--- | :--- | :--- | :--- | :---: |
| **`ACTIVE`** | Activa | ➔ `BLOCKED`<br>➔ `MAINTENANCE`<br>➔ `INACTIVE` | **Bloquear**, **Mantenimiento**, **Desactivar** | Sí (para `BLOCKED` y `MAINTENANCE`)<br>No (para `INACTIVE`) |
| **`BLOCKED`** | Bloqueada | ➔ `ACTIVE` | **Reactivar** | No |
| **`MAINTENANCE`** | Mantenimiento | ➔ `ACTIVE` | **Reactivar** | No |
| **`INACTIVE`** | Inactiva | ➔ `ACTIVE` | **Reactivar** *(y **Eliminar** si ocupación = 0)* | No |

##### Modal de Motivo Obligatorio (`BLOCKED` y `MAINTENANCE`)

Cuando usted presiona **Bloquear** o **Mantenimiento**, se abre el modal **Motivo del cambio de estado**:
- Exige describir obligatoriamente la razón del cambio (por ejemplo: *daño estructural en viga*, *cuarentena sanitaria*, *reparación de piso*).
- Si intenta presionar **Confirmar** dejando el campo vacío, el sistema bloquea la operación con una alerta.
- El motivo capturado queda grabado de manera permanente en la bitácora de auditoría.

##### Eliminación Permanente

El botón rojo **Eliminar** únicamente se habilita cuando la ubicación cumple **ambas condiciones**:
1. Su estado actual es **`INACTIVE`** (desactivada).
2. Su ocupación actual es **`0` unidades**.

Al presionarlo se despliega el modal de confirmación **¿Eliminar esta ubicación?** advirtiendo que la acción es irreversible.

#### 4.3.4 Panel de Auditoría y Línea de Tiempo

Al final de cada ubicación en modo edición se encuentra el **Panel de Auditoría**, que muestra de forma fija la **Última modificación**, el **Usuario** y la **Acción** ejecutada.

Al hacer clic en **Ver historial**, se expande la línea de tiempo cronológica completa con íconos diferenciados por evento (**Ubicación registrada**, **Información actualizada**, **Cambio de estatus operativo** y **Ubicación eliminada**), detallando el usuario responsable, la fecha/hora exacta y el comparativo de cada campo modificado (`valor anterior ➔ valor nuevo`).

---

### 4.4 Secciones de Almacén

El módulo **Secciones de Almacén** (ruta `/sections`) administra la zonificación lógica de cada sucursal —como áreas de recibo, naves de almacenamiento general, cámaras de refrigeración o zonas especiales— y actúa como puente de activación hacia el plano 2D y el módulo de Layout.

![Secciones de Almacén](screenshots/12-sections.png)

#### 4.4.1 Panel de Métricas KPI Colapsables

En la cabecera se muestran cuatro indicadores globales del catálogo de secciones: **Total Secciones**, **Activas**, **Inactivas** y **Almacenes** (cantidad de sucursales con secciones registradas). El panel inicia en su vista compacta horizontal para priorizar el espacio de trabajo y puede expandirse a tarjetas completas en cualquier momento con el botón **Expandir Métricas / Compactar Vista**.

#### 4.4.2 Directorio Desplegable y Filtros

En la barra de selección superior encontrará:
- **Botón selector de sección**: muestra las iniciales y nombre de la sección seleccionada (o *Alta de Nueva Sección*). Al hacer clic abre el panel flotante **Directorio de Secciones** con:
  - Buscador instantáneo por **nombre**, **código** o **almacén**.
  - Filtro desplegable por **Almacén** y por **Estatus** (`Activas` / `Inactivas`), más botón de limpiar filtros.
  - Lista interactiva donde cada sección muestra su avatar de iniciales, nombre, sucursal, código y etiqueta `ACTIVA` o `INACTIVA`.
- Botón principal **+ Nueva sección** para iniciar un alta limpia.

#### 4.4.3 Formulario de Registro, Edición y Validaciones

| Campo | Obligatorio | Reglas de Validación |
| :--- | :---: | :--- |
| **Almacén / Sucursal** | Sí | Sucursal a la que pertenece la sección. |
| **Código de Sección** | Sí | Máximo `10` caracteres. Solo letras, números, guiones (`-`) y guion bajo (`_`), sin espacios; se guarda automáticamente en mayúsculas (ej. `ALMC`, `ZONA-A`, `FRIO`). Debe ser único por sucursal. |
| **Nombre Descriptivo** | Sí | Máximo `100` caracteres. No puede quedar vacío ni contener únicamente espacios en blanco. |

> [!NOTE]
> **Control de duplicidad y concurrencia**: Si intenta guardar un código que ya existe en la misma sucursal, o si otro administrador modificó la sección simultáneamente, el sistema detiene el guardado y muestra un banner de error claro para evitar sobrescrituras accidentales.

#### 4.4.4 Activación y Reconfiguración de Nave en Topología 2D

Al seleccionar una sección existente en modo edición, aparece la tarjeta intermedia **Capacidad de Almacenamiento & Topología 2D**:

- **Indicador de estado topológico**: muestra **`ACTIVA EN PLANO 2D`** (si cuenta con bahías configuradas) o **`PENDIENTE DE ACTIVACIÓN`**.
- **Resumen operativo**: despliega el número de bahías fijas, tarimas totales, factor de estiba (`22 tarimas/pos` por defecto) y categoría operativa asignada.
- **Botón Configurar y Activar Nave / Reconfigurar Nave**: abre el asistente de inicialización de nave (descrito en la sección *4.2.8*), permitiendo asignar giro operativo, posiciones fijas, factor de estiba, SKUs autorizados y generar automáticamente la cuadrícula de ubicaciones físicas en el plano 2D sin salir del módulo.

#### 4.4.5 Control de Vigencia (Activar / Desactivar Sección)

En el pie izquierdo del formulario de edición dispone del botón de cambio de estatus:
- **Desactivar Sección**: cambia la sección a `INACTIVE`. Una sección inactiva conserva su historial pero **deja de estar disponible** para registrar nuevas ubicaciones en el módulo *Gestión de Layout y Ubicaciones Físicas*.
- **Activar Sección**: restablece la sección al estado `ACTIVE` para uso operativo inmediato.

El botón **Guardar Cambios** permanece deshabilitado en modo edición mientras no realice modificaciones en el formulario; al detectar cambios muestra la etiqueta `Sin guardar` en la cabecera del panel.

#### 4.4.6 Información de Control y Línea de Tiempo de Auditoría

Toda sección existente exhibe el bloque de solo lectura **Información de Control & Auditoría**:
- **Metadatos**: Fecha de creación, Última actualización, **Versión** (`v1`, `v2`...) e **ID Interno**.
- **Línea de Tiempo de Auditoría (Trazabilidad WMS)**: muestra el contador de eventos, un botón de refresco manual y la bitácora cronológica detallada con código de color por acción (alta en verde, edición en ámbar, cambio de estatus en morado, baja en rojo), usuario responsable (`@usuario`), marca de fecha/hora exacta y diferencias campo por campo (`valor anterior ➔ valor nuevo`) con nombres legibles (*Nombre de Nave / Sección*, *Código de Sección*, *Estado Operativo*, *Capacidad de Tarimas*, *Bahías / Posiciones Fijas*, *Factor de Estiba* y *Categoría Operativa*).
