## Capítulo 2. Dashboard Torre de Control, Caseta de Seguridad y Portal de Choferes

Este capítulo detalla el funcionamiento integral de los tres pilares de visibilidad y control de acceso del ecosistema **4GUARD WMS**: el **Dashboard Torre de Control** (supervisión gerencial en tiempo real), el **Centro de Control de Vigilancia y Caseta de Seguridad** (control vehicular, formato oficial F01, pases QR, patio, salidas y exportación F02) y el **Portal Público Móvil de Auto-Registro de Choferes** (captura anticipada en smartphone).

---

### 2.1 Dashboard Torre de Control

#### 2.1.1 Propósito, ruta y roles autorizados

| Atributo | Detalle |
| :--- | :--- |
| **Propósito operativo** | Centralizar el monitoreo en tiempo real de la operación del centro de distribución: saturación de andenes, capacidad volumétrica de las naves, estado del inventario y contingencias críticas con acceso directo a su resolución. |
| **Ruta de acceso** | `/dashboard` (pantalla de inicio predeterminada tras iniciar sesión para perfiles administrativos y operativos). |
| **Frecuencia de actualización** | Sincronización automática en vivo cada **15 segundos** para bloqueos de calidad y actualización reactiva de inventario, bahías y andenes. |
| **Roles con acceso** | Director General, Administrador, Gerente de Operaciones, Gerente de Almacén, Coordinador de Operaciones, Supervisor de Operaciones, Mesa de Control, Líder de Turno y Operario de Almacén. |
| **Roles restringidos** | Los perfiles exclusivos de **Guardia de Seguridad / Vigilancia** son redirigidos automáticamente a `/security`, y los perfiles exclusivos de **Inspector de Calidad** a `/quality`, cumpliendo el principio de segregación de funciones. |

![Dashboard Principal - Torre de Control](screenshots/04-dashboard.png)

#### 2.1.2 Estructura visual y barra superior de indicadores

En la cabecera de la Torre de Control se muestran los siguientes indicadores dinámicos de estado:

1. **Indicador condicional "Riesgo Maniobras (>100%)"**: Píldora interactiva con pulso de advertencia y contador numérico. Aparece automáticamente solo cuando uno o más almacenes superan el umbral crítico de saturación (>95% de ocupación ajustada). Al hacer clic abre de inmediato el modal de **Desglose por Almacén y Parámetros de Empaque**.
2. **Insignia de Alertas Críticas**: Muestra el conteo total de alertas activas con severidad crítica (`Crítica` / `Críticas`) junto a un punto indicador rojo pulsante.
3. **Indicador de sincronización en vivo**: Muestra la etiqueta `En vivo · actualización automática` confirmando el enlace continuo con la base de datos operativa.

#### 2.1.3 Tabla completa de indicadores (KPIs) y tarjetas interactivas

| Tarjeta / Indicador | Qué mide exactamente | Estados / Umbrales visuales | Acción interactiva al hacer clic |
| :--- | :--- | :--- | :--- |
| **Ocupación de Andenes** (Medidor circular) | Porcentaje de rampas activas en maniobra respecto al total de andenes configurados (base de 12 rampas si no se cargan adicionales). Muestra en el pie el conteo exacto: *X rampas libres · Y en maniobra activa*. | **Flujo Normal** (< 60%), **Atención** (60% a 84%), **Saturado** (≥ 85%). El color del arco refleja el índice de riesgo operativo global: verde (< 30 pts), ámbar (30 a 59 pts) y rojo (≥ 60 pts). | Navega directamente a la pantalla de gestión de recepción y andenes. |
| **Saturación del Almacén** (Anillo de capacidad) | Porcentaje global de bahías ocupadas o bloqueadas sobre el total de posiciones del plano maestro. Debajo del anillo desglosa tres cifras: **Bahías ocupadas**, **Disponibles** y **Total bahías**. | Escala de 0% a 100% con barra circular animada. | Al hacer clic en la tarjeta abre el mapa 2D de ubicaciones; al pulsar el botón **Desglose por Bodega & Empaque** abre el simulador volumétrico modal. |
| **Disponibles** (Tarjeta KPI) | Cantidad de unidades o tarimas en estado disponible para asignación y despacho, acompañada de su etiqueta de estado y tendencia porcentual frente al día anterior. | Estado: *Disponible*. Tendencia positiva (`↑`), negativa (`↓`) o estable (`— Sin cambio`). | Visualización ejecutiva instantánea del stock libre. |
| **En Cuarentena** (Tarjeta KPI) | Cantidad de lotes o tarimas retenidas preventivamente en cuarentena pendientes de dictamen o inspección física. | Estado: *Cuarentena*. | Permite identificar rápidamente stock inmovilizado preventivamente. |
| **Bloqueados QM** (Tarjeta KPI) | Total de bloqueos activos registrados por Control de Calidad (Producto No Conforme / QM) obtenidos en tiempo real desde la base de datos. | Estado: *Bloqueado QM*. Flecha ascendente si existen bloqueos activos (> 0). | Refleja la carga real de incidencias de calidad abiertas. |
| **En Picking** (Tarjeta KPI) | Cantidad de tarimas o partidas actualmente en proceso de surtido y preparación para embarque. | Estado: *En Picking*. | Mide el dinamismo de las órdenes de salida en curso. |

> **Cálculo del Índice de Riesgo Operativo Global:** Para colorear el arco del medidor de andenes, el sistema evalúa de forma ponderada (en una escala de 5 a 100 puntos) cuatro factores simultáneos: ocupación de nave (hasta 35 puntos), proporción de andenes ocupados (hasta 25 puntos), número de bloqueos activos de calidad QM (6 puntos por bloqueo, hasta 30 puntos) y transportes en cola esperando andén en caseta (4 puntos por unidad, hasta 20 puntos).

#### 2.1.4 Panel de Alertas del Sistema en vivo

El panel **Alertas del Sistema** consolida automáticamente cuatro categorías de contingencias operativas. Cada tarjeta de alerta incluye título, etiqueta del módulo afectado, descripción puntual, antigüedad relativa (`hace un momento`, `hace X min`, `hace Xh`), botón de acción directa y botón de descarte temporal (`×`) para la sesión en curso.

| Tipo de Alerta | Condición que la dispara | Severidad | Módulo etiquetado | Botón de acción directa | Destino al pulsar |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Bloqueo Calidad (QM)** | Existencia de bloqueos activos de calidad (se muestran hasta los 3 más recientes con número de lote/SSCC/folio, SKU y motivo o defectos reportados). | **Crítica** | Calidad & PNC | *Inspeccionar en Calidad* | Módulo de Control de Calidad (`/quality`). |
| **Congestión en Caseta de Seguridad** | Una o más unidades arribadas en espera de asignación de andén (desglosando cuántas son descargas y cuántos embarques). | **Advertencia** (1 a 2 unidades) / **Crítica** (3 o más unidades) | Patios & Caseta | *Asignar Andenes* | Módulo de Movimientos de Almacén (`/warehouse-movements`). |
| **Capacidad de Andenes al Límite** | Quedan **2 o menos andenes libres** en patio. | **Advertencia** (1 o 2 libres) / **Crítica** (0 libres / 100% ocupados) | Andenes & Despacho | *Ver Monitor de Patios* | Módulo de Movimientos de Almacén (`/warehouse-movements`). |
| **Riesgo de Saturación en Pasillos** | Uno o más almacenes superan el **95% de ocupación ajustada** según el tipo de empaque seleccionado. | **Crítica** | Capacidad de Nave | *Ver Desglose de Almacenes* | Abre el modal **Desglose por Almacén & Parámetros de Empaque**. |

Si no existe ninguna contingencia activa, el panel muestra el mensaje de confirmación: *«Sin alertas críticas activas. Operación dentro de parámetros nominales.»*

#### 2.1.5 Modal: Desglose por Almacén y Parámetros de Empaque

* **Propósito:** Analizar la capacidad nominal frente a la ocupación real de cada bodega o nave del centro de distribución, ajustando dinámicamente la densidad volumétrica según el tipo de empaque predominante en el turno.
* **Cómo abrirlo:** Pulsar **Desglose por Bodega & Empaque** en la tarjeta de Saturación del Almacén, hacer clic en la píldora superior **Riesgo Maniobras (>100%)** o pulsar **Ver Desglose de Almacenes** en la alerta de saturación.

**Sección 1 — Selector de Tipo de Empaque Predominante en Turno:**

| Opción de Empaque | Unidad de medida mostrada | Factor de densidad | Efecto en el cálculo |
| :--- | :--- | :--- | :--- |
| **Producto Terminado (PT)** *(Predeterminado)* | Palets Estándar | **1.0x** | Refleja la ocupación base nominal sin ajuste volumétrico. |
| **Frascos / Vidrio** | Cajas / Tarimas | **1.15x** | Incrementa en un 15% el volumen ocupado equivalente, evidenciando saturación anticipada por mayor requerimiento de espacio y menor estiba. |
| **Barriles / Contenedores** | Tambores | **0.85x** | Reduce en un 15% el factor de ocupación equivalente por mayor compactación en piso. |

**Sección 2 — Tarjetas de Capacidad Nominal vs. Ocupación Real por Almacén:**
Cada almacén o sección registrada en la topología del centro de distribución (por ejemplo, *Almacén H — Conservación & Químicos*, *Almacén G — Zona Oeste*, *Almacén B — Almacén Central*, *Almacén C — Refrigerados / QM*) muestra:
* **Código y nombre de la nave**.
* **Porcentaje de ocupación ajustada** y barra de progreso visual.
* **Etiqueta de estado**: `NORMAL` (≤ 95%) o `⚠️ ALERTA RIESGO MANIOBRAS` (> 95%).
* **Detalle numérico**: *Bahías nominales* y *Ocupación ajustada* en la unidad del empaque elegido.
* **Aviso de desbordamiento en pasillos**: Si la ocupación ajustada supera el 100%, calcula y muestra automáticamente cuántos palets están invadiendo pasillos con la advertencia operativa: *«🚨 X palet(s) en pasillos (Retraso en rampas ~25 min)»*.

#### 2.1.6 Procedimiento Paso a Paso (SOP): Consultar el desglose volumétrico y atender alertas

1. Ingrese al módulo **Torre de Control** (`/dashboard`) desde el menú lateral.
2. Verifique en la cabecera si existen alertas críticas activas o la píldora **Riesgo Maniobras (>100%)**.
3. En la tarjeta **Saturación del Almacén**, haga clic en el botón **Desglose por Bodega & Empaque**.
4. En el modal emergente, seleccione el **Tipo de Empaque Predominante en Turno** (*Producto Terminado*, *Frascos / Vidrio* o *Barriles / Contenedores*) y observe el recálculo inmediato del porcentaje y las barras de cada bodega.
5. Identifique las naves marcadas con **ALERTA RIESGO MANIOBRAS** o con palets en pasillo para coordinar reacomodos con el Líder de Turno antes de asignar nuevas descargas.
6. Pulse **Entendido** (o el botón `×`) para cerrar el modal.
7. En el panel **Alertas del Sistema**, haga clic en el botón de acción de cada contingencia (por ejemplo, *Asignar Andenes* o *Inspeccionar en Calidad*) para navegar directamente a resolverla. Si una alerta ya está siendo atendida y desea despejar su vista temporalmente, pulse el botón `×` en la esquina superior derecha de la alerta.

#### 2.1.7 Reglas de negocio y solución de problemas del Dashboard

* **Descarte de alertas no elimina el problema:** Descartar una alerta con `×` solo la oculta durante la sesión actual del navegador; si la causa raíz persiste (por ejemplo, un bloqueo QM abierto o andenes saturados), reaparecerá al recargar el sistema.
* **Si el medidor de andenes aparece en 0% pero hay unidades en patio:** Verifique en Caseta o Movimientos de Almacén que las unidades ya tengan una rampa física asignada y no se encuentren únicamente en estatus *En Espera en Patio*.
* **Si no se visualizan las naves actualizadas:** Pulse actualizar en el navegador o verifique con Administración que las secciones del almacén estén cargadas en el catálogo de Topología.

---

### 2.2 Caseta de Seguridad (Centro de Control de Vigilancia)

#### 2.2.1 Propósito, ruta y roles autorizados

| Atributo | Detalle |
| :--- | :--- |
| **Propósito operativo** | Gestionar el ciclo completo de seguridad patrimonial de vehículos de carga: recepción de auto-registros QR desde el celular del chofer, captura e inspección física del formato oficial **Check List de Transporte (F01-PO-CP-7.1.3-03)**, coordinación en vivo con Operaciones para ingreso a rampas, monitoreo de unidades en planta, reasignación auditada de andenes, control de salidas (Check-Out), historial por turno y exportación del reporte oficial **F02-PO-CP-7.1.3-03** en Excel. |
| **Ruta de acceso** | `/security` (pantalla principal automática para Guardias de Seguridad y Vigilancia). |
| **Sincronización en vivo** | Actualización silenciosa cada **2 segundos** para choferes en espera, unidades en planta e historial, complementada con alertas acústicas (timbre de caseta) y notificaciones en tiempo real. |
| **Roles con acceso** | Guardia de Seguridad, Ayudante de Seguridad / Vigilancia, Supervisor de Operaciones, Gerente de Almacén, Gerente de Operaciones, Administrador y Director General. |

![Caseta de Seguridad - Registro y Pases QR](screenshots/06-security-gate.png)

#### 2.2.2 Barra superior de acciones, alertas de rampa y métricas del turno

**Botones de la cabecera principal:**
* **Imprimir Cartel QR**: Abre el visor de impresión del cartel oficial tamaño Carta Horizontal (`F01-PO-CP-7.1.3-03-QR`) para colocar en la entrada de caseta.
* **Ver QR / Configurar Túnel**: Abre el modal de Pase Digital QR rápido para escaneo directo en ventanilla y configuración de URL pública o túnel móvil.
* **Actualizar**: Fuerza la recarga inmediata de pases activos, unidades en patio, recepciones e historial.
* **Exportar F02 Excel**: Abre el asistente de exportación del formato oficial `F02-PO-CP-7.1.3-03` en archivo `.xlsx`.
* **Insignia de estado**: `CASETA EN SERVICIO` (indicador verde de operación continua).

**Banner en vivo de Rampas Asignadas por Almacén:**
Cuando Operaciones/Almacén asigna una rampa a una unidad que esperaba en patio, aparece automáticamente un banner verde de alta prioridad en la parte superior de Caseta indicando: **DAR ACCESO A LA UNIDAD · PASAR A RAMPA [N]**, mostrando folio, hora, cliente/transportista, número económico, placas y nombre del chofer.
* **Botón "Dar Acceso (Levantar Pluma)"**: Confirma el acceso en pantalla, descarta el aviso y lleva automáticamente al guardia a la pestaña *Unidades en Planta* filtrando esa unidad.
* **Botón `×`**: Descarta el aviso manualmente.

**Panel de Métricas Operativas del Turno (Colapsable):**
El usuario puede alternar entre vista expandida (4 tarjetas) y vista compacta (barra resumida) mediante el botón **Compactar Vista / Expandir Métricas** (la preferencia se recuerda automáticamente).

| Métrica KPI | Descripción operativa |
| :--- | :--- |
| **Unidades en Planta** | Total de vehículos actualmente dentro de patios y andenes que aún no completan su Check-Out. |
| **QR Pendientes / Espera** | Choferes con pase QR activo capturando datos o con formulario ya enviado esperando validación del guardia. |
| **Listos para Salida** | Unidades cuya maniobra de descarga o carga ya fue autorizada y cerrada por Almacén (habilitadas para Check-Out). |
| **Salidas del Turno** | Total de inspecciones y salidas vehiculares concluidas durante el turno operativo en curso. |

---

#### 2.2.3 Pestaña 1: Nuevo Registro & Pases QR

Esta pestaña se divide en dos áreas: el **Panel Lateral "Choferes en Espera"** (colapsable mediante el botón **Ocultar Panel Lateral / Mostrar Choferes** para aprovechar el 100% del ancho) y el **Formulario Oficial F01-PO-CP-7.1.3-03**.

##### A. Panel Lateral: Choferes en Espera (Cola de Pases QR)

Muestra en tiempo real las solicitudes provenientes del Portal Móvil de Choferes y los pases QR generados en caseta. Cuando un chofer envía su formulario desde su smartphone, el sistema reproduce un **timbre sonoro de caseta**, muestra una notificación visual y, si el formulario principal está vacío, **precarga automáticamente los datos del chofer** para agilizar el despacho.

Cada tarjeta de pase muestra:
* **Tipo de operación** (`DESCARGA` o `CARGA`) y **folio del pase** (`#PASS-4G-...`).
* **Estado del pase**: `QR Activo` (el chofer está llenando el formulario en su celular) o `✓ Enviado` (formulario F01 completado por el transportista).
* **Identidad del chofer y teléfono interactivo**: Al tocar el número telefónico se abre la llamada directa desde dispositivos compatibles.
* **Placas de la unidad y línea transportista**.
* **Botones de acción**:
  * **QR**: Abre el código QR individual de ese pase.
  * **Cargar en Caseta / Cargar / Usar**: Transfiere el 100% de los datos y firma digital del chofer al formulario oficial F01.
  * **Papelera (Descartar)**: Elimina el pase de la cola de espera (si estaba cargado en el formulario, lo limpia automáticamente).

##### B. Barra de navegación rápida del formulario (4 Pasos del F01)

Una barra superior fija permite saltar directamente a cada sección del formato oficial:
1. **1. Operación**
2. **2. Transporte & Unidad**
3. **3. Check List (F01)**
4. **4. Firmas & Rampa**

Cuando se está trabajando sobre un pase móvil cargado, la barra muestra a la derecha el identificador del pase vinculado (`#PASS-4G-...`).

##### C. Paso 1 — Datos Generales de la Operación

| Campo | Obligatoriedad | Valores / Formato | Reglas de negocio y comportamiento |
| :--- | :--- | :--- | :--- |
| **Fecha** | Obligatorio (`*`) | Selector de fecha (`AAAA-MM-DD`). | Se autocompleta con la fecha actual del sistema. |
| **Operación** | Obligatorio (`*`) | Selector bimodal: **Descarga** (Recepción) o **Carga** (Embarque). | Al cambiar entre Descarga y Carga, el sistema ajusta automáticamente la obligatoriedad de Carta Porte vs. Remisión, los criterios documentales del Check List y el bloqueo/habilitación de sellos de seguridad. |
| **No. Carta Porte** | Obligatorio solo en **Carga** | Texto alfanumérico (se convierte a MAYÚSCULAS automáticamente). | En *Descarga* es opcional y el criterio documental se marca como N/A. |
| **No. Remisión / Factura** | Obligatorio solo en **Descarga** | Texto alfanumérico (MAYÚSCULAS). | Al capturar al menos 3 caracteres, el sistema activa la **Auto-Detección Inteligente de Retornos**: si el número coincide con una salida previa registrada en el WMS, muestra un banner morado **«⚡ Retorno Detectado Automáticamente»** con el folio de salida origen y el botón **Precargar Datos**, que rellena automáticamente cliente, transportista, chofer y placas para agilizar la logística inversa. |
| **Cliente** | Obligatorio (`*`) | Lista desplegable del catálogo de clientes + opción **Otro (Especificar)**. | Si se elige *Otro*, aparece un campo adicional obligatorio para capturar manualmente la razón social. |
| **Hora Entrada** | Obligatorio (`*`) | Selector de hora (`HH:MM`). | Se autocompleta con la hora actual al iniciar el registro. |
| **Destino / Posicionamiento** | Informativo (Solo lectura) | *En Espera en Patio* o *Rampa R-XX (Andén Directo)*. | Resume el estado de posicionamiento de la unidad. |
| **Monitor de Andenes en Vivo** | Solo lectura | Cuadrícula de todas las rampas estándar del almacén. | Muestra en tiempo real qué rampas están **Libres** (verde) y cuáles **Ocupadas** (con su número de folio activo). Si Operaciones ya asignó una rampa pendiente de acceso, esa celda se resalta con la etiqueta **METER AQUÍ**. La asignación de rampa corresponde a Operaciones; Caseta visualiza el estado. |

##### D. Paso 2 — Datos del Transporte y Unidad

| Campo | Obligatoriedad | Valores / Formato | Reglas de negocio y comportamiento |
| :--- | :--- | :--- | :--- |
| **Línea Transportista** | Obligatorio (`*`) | Lista desplegable del catálogo de fleteras + opción **Otro (Especificar)**. | Si se selecciona *Otro*, habilita el campo manual de nombre de transportista. |
| **Nombre Operador** | Obligatorio (`*`) | Texto (MAYÚSCULAS). | Nombre completo del chofer. |
| **Teléfono Chofer** | Opcional en registro manual (Uso Caseta) | Numérico de hasta 10 dígitos (`5512345678`). | Se precarga automáticamente cuando el chofer realizó su auto-registro móvil. |
| **Tipo de Transporte** | Obligatorio (`*`) | Lista desplegable: *Camioneta*, *Tortón*, *Tráiler*, *Otro (Especificar)* (y valores del catálogo en BD). | Si se elige *Otro (Especificar)*, muestra un campo de texto libre. |
| **Placas Tracto** | Obligatorio (`*`) | Texto alfanumérico (MAYÚSCULAS). | Identificación vehicular del tractocamión. |
| **No. Eco Tracto** | Obligatorio (`*`) | Texto alfanumérico (MAYÚSCULAS). | Número económico de la unidad motriz. |
| **Placas Caja** | Obligatorio (`*`) | Texto alfanumérico (MAYÚSCULAS). | Placas del remolque o caja. |
| **No. Eco Caja** | Opcional | Texto alfanumérico (MAYÚSCULAS). | Número económico del remolque. |
| **Medidas Caja / Capacidad** | Obligatorio (`*`) | *53 Pies*, *48 Pies*, *40 Pies*, *20 Pies*, *Tortón*, *Rabón*, *3.5 Toneladas*, *N/A - Plataforma*, *Otra Medida*. | Si se elige *Otra Medida*, despliega campo manual. |
| **Sellos / Cinchos de Seguridad** | **Obligatorio en Descarga** (mínimo 1 sello). **Bloqueado en Carga**. | Captura múltiple con botón **Agregar** o tecla `Enter`; cada sello se agrega como chip removible (`✕`). | En operaciones de **CARGA**, la unidad ingresa vacía, por lo que el campo se bloquea automáticamente con la etiqueta `PENDIENTE_ANDEN` (los sellos oficiales se colocan y registran en el andén de embarque al concluir la estiba). En **DESCARGA**, si el guardia escribe un sello y pulsa enviar sin dar clic en *Agregar*, el sistema lo incorpora automáticamente antes de validar. |

##### E. Paso 3 — Revisión del Transporte (Tabla Completa del Check List F01-PO-CP-7.1.3-03)

Todos los criterios cuentan con botones de selección **SÍ / NO** y una columna **Observaciones (Manual)** vacía por defecto para documentar hallazgos específicos (el texto de observaciones se convierte automáticamente a MAYÚSCULAS).

| Grupo | Criterio de Inspección | Obligatoriedad | Valor por defecto | Valores posibles | Efecto operativo y significado de cumplimiento |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **EPP (Equipo de Protección Personal)** | **Zapatos de seguridad** | Obligatorio | `SÍ` | `SÍ` / `NO` | Verifica que el chofer porte calzado industrial con casquillo. Marcar `NO` queda asentado en el expediente F01 y observaciones de auditoría. |
| **EPP** | **Cofia** | Obligatorio | `SÍ` | `SÍ` / `NO` | Verifica cumplimiento de inocuidad/higiene requerido en planta. |
| **EPP** | **Cubrebocas** | Obligatorio | `SÍ` | `SÍ` / `NO` | Verifica cumplimiento sanitario en área de patios y andenes. |
| **EPP** | **Chaleco con reflejante** | Obligatorio | `SÍ` | `SÍ` / `NO` | Verifica visibilidad obligatoria del operador en zona de maniobras de montacargas. |
| **Documentos Obligatorios** | **Carta porte** | Obligatorio en **Carga** (`N/A` en Descarga) | `NO` (Descarga) / `SÍ` (Carga) | `SÍ` / `NO` | En Descarga se auto-llena observación *«N/A - Operación de Descarga/Recepción»*. Al cambiar a Carga pasa automáticamente a `SÍ` y limpia la observación. |
| **Documentos Obligatorios** | **Remisión** | Obligatorio en **Descarga** (`N/A` en Carga) | `SÍ` (Descarga) / `NO` (Carga) | `SÍ` / `NO` | En Carga se auto-llena observación *«N/A - Operación de Carga/Embarque»*. Al cambiar a Descarga pasa automáticamente a `SÍ`. |
| **Revisión Física de la Unidad** | **Interior de Caja** | Obligatorio | `SÍ` | `SÍ` / `NO` | Evalúa limpieza y condiciones sanitarias del interior del remolque (`SÍ` = en buenas condiciones/cumple). |
| **Revisión Física de la Unidad** | **Daños a la caja** | Obligatorio | `NO` | `SÍ` / `NO` | Evalúa integridad estructural de paredes, piso y techo (`NO` = sin daños; `SÍ` = presenta daños que deben detallarse en observaciones). |
| **Revisión Física de la Unidad** | **Daños en puertas** | Obligatorio | `NO` | `SÍ` / `NO` | Evalúa bisagras, empaques y mecanismos de cierre/sellado (`NO` = sin daños). |
| **Revisión Física de la Unidad** | **Olores extraños** | Obligatorio | `NO` | `SÍ` / `NO` | Detecta contaminación cruzada o aromas ajenos a la carga (`NO` = sin olores extraños). |
| **Revisión Física de la Unidad** | **Indicios de plagas** | Obligatorio | `NO` | `SÍ` / `NO` | Verifica ausencia de insectos, roedores o rastros biológicos conforme a protocolos de inocuidad y C-TPAT (`NO` = sin indicios). |

##### F. Paso 4 — Responsables, Firmas de Conformidad y Envío a Operaciones

| Elemento | Obligatoriedad | Comportamiento |
| :--- | :--- | :--- |
| **Responsable Vigilancia** | Obligatorio (`*`) | Se autocompleta con el nombre completo del guardia autenticado en la sesión activa. Incluye sello visual *«Firma Autorizada de Caseta»*. |
| **Transportista (Chofer)** | Obligatorio (`*`) | Nombre de quien entrega o recibe la unidad. Se precarga automáticamente al cargar un pase móvil. |
| **Casilla "Transportista Acepta y Firma de Conformidad"** | Obligatorio (`*`) | Debe estar marcada para permitir el envío del Check-In. Cuando el chofer envió su auto-registro móvil con firma táctil, se marca automáticamente y despliega la tarjeta **Firma Electrónica y Sello Digital QR** con el folio del pase, la insignia `Verificado` y la vista previa del trazo manuscrito capturado. |
| **Barra inferior fija (Acciones)** | — | **Cancelar / Limpiar**: Restablece el formulario a valores iniciales y desvincula cualquier pase activo. **Mandar datos a Operaciones para asignación de rampa**: Valida todos los campos obligatorios, crea el folio oficial de Recepción (Descarga) o Salida (Carga), notifica en tiempo real a Operaciones/Almacén, actualiza el pase del chofer a *Autorizado* y cambia automáticamente a la pestaña *Unidades en Planta*. |

---

#### 2.2.4 Pestaña 2: Unidades en Planta & Control de Salidas (Check-Out)

![Caseta - Unidades en Planta y Salidas](screenshots/06b-security-planta.png)

* **Propósito:** Monitorear en tiempo real todos los vehículos que se encuentran dentro de las instalaciones (en patio de espera, en maniobra de descarga/carga o en auditoría) y ejecutar el proceso de salida física (**Check-Out**) una vez que Almacén autoriza el cierre.

**Controles superiores y filtros segmentados:**
* **Buscador rápido**: Filtra en tiempo real por folio, token, transportista, nombre de chofer, placas de tracto/caja o código de rampa.
* **Botón Refrescar**: Sincroniza manualmente el estatus de todas las unidades en patio.
* **Botón Depurar Patio**: Herramienta de mantenimiento/pruebas protegida por modal de confirmación (*«¿Depurar todo el patio y entorno?»*) que limpia unidades activas, pases de choferes y caché local dejando el patio en ceros. *Precaución: no utilizar en operación productiva activa.*
* **Filtros segmentados**:
  * **Todas en Planta**: Muestra el universo completo de vehículos dentro de las instalaciones.
  * **En Descarga / Maniobra**: Muestra únicamente unidades cuya operación aún está en curso o pendiente de auditoría.
  * **Listas para Salida**: Muestra únicamente unidades ya autorizadas por Almacén para retirarse (resaltadas visualmente).

**Tabla de columnas de Unidades en Planta:**

| Columna | Contenido mostrado |
| :--- | :--- |
| **Folio / Pase** | Folio oficial generado por el WMS y token del pase digital vinculado. |
| **Operación** | Etiqueta de color diferenciada: `DESCARGA #...` o `CARGA #...`. |
| **Rampa** | Si aún no tiene andén: `🅿️ En Espera de Andén`. Si ya tiene rampa asignada: insignia `DAR ACCESO · R-XX`. Si cumple los candados de seguridad, muestra además el botón **Cambiar** (reasignación auditada de rampa). |
| **Transporte & Placas** | Línea transportista, placas del tracto y placas de la caja. |
| **Operador** | Nombre completo del chofer. |
| **Hora Entrada** | Hora de ingreso registrada en formato `HH:MM`. |
| **Estatus en Almacén** | Estado sincronizado en vivo: *En Patio / Espera*, *Asignado a Rampa*, *Descargando en Andén*, *Cargando en Andén*, *Descarga Concluida / Auditoría*, *Carga Concluida / Auditoría*, *Cancelado (Salida Autorizada)* o *Autorizado / Listo Salida*. |
| **Acciones** | Botón **F01** (abre el visor e impresión del formato oficial), botón **Check-Out** (habilitado cuando la unidad está lista para salida; en caso contrario se muestra candado bloqueado indicando *En Descarga*, *En Carga* o *En Auditoría* con explicación emergente) y botón **Papelera** (descarte individual de unidad de prueba con confirmación). |

> **Aviso sonoro automático de Salida Lista:** Mientras el guardia permanece en Caseta, en el instante en que Almacén concluye y autoriza la maniobra de una unidad, el sistema emite automáticamente el timbre de caseta y una notificación emergente indicando que la unidad concluyó en su rampa y está lista para Check-Out.

---

#### 2.2.5 Pestaña 3: Historial del Turno y Consulta Histórica General

![Caseta - Historial del Turno](screenshots/06c-security-historial.png)

* **Propósito:** Auditar las salidas vehiculares concluidas y reimprimir en cualquier momento la boleta oficial **Formato F01-PO-CP-7.1.3-03** con horas de entrada, salida y duración de estancia.

**Dos modos de consulta (selector de píldoras superior):**
1. **Turno Activo** *(predeterminado)*: Muestra exclusivamente las salidas concluidas durante la jornada operativa en curso. Despliega un banner informativo con el nombre del turno activo y su hora de apertura, además de un buscador en vivo por placas, folio o chofer.
2. **Consulta Histórica General**: Permite auditar salidas de cualquier fecha mediante filtros de rango temporal:
   * **Hoy**
   * **Últimos 7 días**
   * **Últimos 30 días**
   * **Todo el Historial**
   * **Personalizado** (despliega selectores de fecha *Desde* y *Hasta*)
   * Más buscador en vivo por placas, folio, chofer o cliente.

**Tabla de columnas del Historial:**

| Columna | Contenido mostrado |
| :--- | :--- |
| **Fecha & Folio** | Folio oficial de la operación y fecha del documento. |
| **Operación** | `DESCARGA` o `CARGA`. |
| **Cliente** | Razón social del cliente atendido. |
| **Transportista & Placas** | Línea transportista, placas de tracto (`T:`) y placas de caja (`C:`). |
| **Operador Chofer** | Nombre completo del conductor. |
| **Entrada / Salida** | Hora de entrada (`In: HH:MM`) y hora de salida (`Out: HH:MM`). |
| **Estancia** | Duración total calculada automáticamente (`X min` o `Xh Ym`), contemplando incluso cruces de medianoche. |
| **Estado** | Insignia `✓ Salida Concluida`. |
| **Boleta F01** | Botón **Imprimir F01** que abre el visor oficial para imprimir o descargar en PDF. |

---

#### 2.2.6 Modales especializados de Caseta de Seguridad

##### Modal A: Pase Digital QR y Configuración de Túnel Móvil

* **Cómo abrirlo:** Botón **Ver QR / Configurar Túnel** en la cabecera (muestra el QR permanente `PORTAL-DIRECTO` vinculado a la organización y sucursal) o botón **QR** dentro de la tarjeta de un chofer específico.
* **Contenido y acciones:**
  * Código QR de alta legibilidad con corrección de errores nivel H (30%) y avatar central de 4GUARD, acompañado del folio del pase.
  * **Abrir Portal**: Abre el enlace del portal de choferes en una nueva pestaña del navegador.
  * **Copiar Enlace**: Copia la URL pública al portapapeles (muestra confirmación *«¡Enlace copiado al portapapeles!»*).
  * **Imprimir Cartel en Hoja Completa (Carta)**: Acceso directo al visor del cartel oficial de entrada.
  * **Nuevo QR**: Genera un nuevo pase individual único en el sistema vinculado a los datos pre-capturados en el formulario.
  * **Descartar**: Elimina el pase QR mostrado actualmente.
  * **Sección URL Pública para Móviles (ngrok)**: En entornos locales de desarrollo o red interna, permite pegar y guardar la URL pública del túnel móvil (con botones **Guardar** y restablecer `×`), mostrando una insignia de estado (`Túnel Activo` o `Localhost`) y la URL base resultante. En servidores productivos en la nube utiliza automáticamente el dominio oficial del sistema.

##### Modal B: Cartel Oficial QR de Entrada (`F01-PO-CP-7.1.3-03-QR`)

* **Cómo abrirlo:** Botón **Imprimir Cartel QR** en la cabecera o desde el Modal A.
* **Especificaciones del cartel:** Hoja tamaño **Carta Horizontal (11" × 8.5" / Paisaje)** con marco doble de seguridad y código QR central ampliado (65 mm × 65 mm) escaneable hasta a **3 metros de distancia**. Incluye normas de EPP en patio (*chaleco reflejante, botas con casquillo, velocidad máxima 10 km/h, prohibido descender sin permiso*), instrucciones de escaneo en 3 pasos, vigencia mensual automática (mes y año en curso), datos de sucursal y teléfono de atención en ventanilla.
* **Controles del visor:**
  * **Zoom interactivo**: Alejar (`-`), porcentaje actual, Acercar (`+`), **Ajustar** (escala automática a pantalla) y **100%** (tamaño físico real).
  * **Barra de túnel QR**: Permite ajustar la URL pública o pulsar **Detectar ngrok** antes de imprimir.
  * **Imprimir Cartel**: Lanza la impresión directa en orientación horizontal (paisaje).
  * **Descargar PDF**: Genera y descarga el archivo `Cartel_Acceso_Caseta_AAAA-MM.pdf` en alta resolución.
  * Se cierra haciendo clic fuera del cuadro o presionando la tecla `Esc`.

##### Modal C: Reasignación de Rampa Operativa con Re-Autenticación y Auditoría

* **Propósito:** Permitir a Caseta o Supervisión cambiar la rampa asignada a una unidad en planta por una contingencia física (rampa niveladora averiada, saturación de pasillo, indicación de patio) dejando trazabilidad criptográfica y auditoría inmutable.
* **Candados de seguridad (cuándo aparece el botón "Cambiar"):**
  1. La unidad debe tener ya una rampa asignada.
  2. La unidad **NO** debe haber iniciado descarga/carga ni estar finalizada, lista para salida o cancelada.
  3. La unidad **NO** debe tener todavía un montacarguista asignado.
  4. El usuario debe contar con rol autorizado (Seguridad/Vigilancia, Operaciones, Supervisor, Mesa de Control o Administrador).
* **Campos del modal:**
  1. **Resumen superior (4 chips)**: Chofer, Operación, Hora Entrada y Rampa Actual.
  2. **Seleccionar Nuevo Andén Destino (`*`)**: Lista desplegable de todas las rampas; las rampas ocupadas y la rampa actual aparecen deshabilitadas, permitiendo elegir únicamente andenes con etiqueta `✓ Disponible`.
  3. **Motivo / Justificación de la Reasignación (`*`)**: Campo obligatorio con un **mínimo de 8 caracteres**. Queda guardado permanentemente en la bitácora de auditoría y se sincroniza con Almacén.
  4. **Firma y Credenciales de Seguridad (`*`)**: Solicita **Usuario / Correo** (precargado con el usuario actual) y **Contraseña de Acceso** (con botón de ojo para mostrar/ocultar). El sistema valida las credenciales contra el servidor de autenticación; si son incorrectas, bloquea el cambio.
* **Efecto al confirmar:** Libera la rampa anterior, ocupa la nueva rampa en tiempo real en todos los monitores de Caseta y Almacén, y registra el evento de auditoría `RAMPA_REASIGNADA_CASETA` con usuario firmante, rampa previa, nueva rampa, motivo y fecha/hora.

##### Modal D: Salida de Planta (Check-Out)

* **Cómo abrirlo:** Botón **Check-Out** en la fila de una unidad dentro de la pestaña *Unidades en Planta*.
* **Protección preventiva:** Si se intenta dar salida a una unidad que todavía figura en maniobra o pendiente de visto bueno en Almacén, el sistema despliega una alerta de confirmación explícita antes de abrir el modal. Si la unidad proviene de una operación cancelada, precarga automáticamente el motivo de cancelación en las observaciones de salida.
* **Campos del modal:**
  * **Resumen de la unidad**: Folio, placas de tracto, número económico, chofer, línea transportista, hora de entrada y rampa/bahía.
  * **Hora de Salida (`*`)**: Obligatorio (`HH:MM`), precargado con la hora actual.
  * **Sellos de Salida (Opcional)**: Permite ingresar uno o varios números de sello separados por comas (o indicar `Vacío`).
  * **Observaciones de Salida (Opcional)**: Condiciones físicas de la unidad al retirarse de planta.
* **Efecto al confirmar:** Registra la salida definitiva, libera inmediatamente la rampa y el patio, mueve el registro a la pestaña **Historial del Turno** y abre de forma automática el **Visor e Impresión del Formato F01** listo para imprimir o archivar en PDF.

##### Modal E: Visor e Impresión Oficial del Formato F01-PO-CP-7.1.3-03

* **Propósito:** Previsualizar, imprimir o descargar en PDF la boleta oficial tamaño Carta (`8.5" × 11"`) del **Check List de Transporte** con encabezado SGC, datos de entrada y salida, resultados de los 11 criterios de inspección, sellos, firma de Vigilancia y firma digital manuscrita del chofer.
* **Acciones:** **Imprimir Hoja**, **Descargar PDF** (`Formato_F01_CheckList_[Folio].pdf`) y cerrar (`✕` o tecla `Esc`).

##### Modal F: Exportación Oficial F02-PO-CP-7.1.3-03 (Excel `.xlsx`)

* **Cómo abrirlo:** Botón **Exportar F02 Excel** en la cabecera de Caseta.
* **Selección de período:**
  * **Día en Curso** (`Hoy`, 00:00 a 23:59 hrs) → Genera `F02_Tracking_Unidades_AAAA-MM-DD.xlsx`.
  * **Día de Ayer** (día anterior completo) → Genera `F02_Tracking_Unidades_Ayer_AAAA-MM-DD.xlsx`.
  * **Mes Completo** (mes en curso acumulado) → Genera `F02_Tracking_Unidades_Mes_AAAA-MM.xlsx`.
  * **Historial Total** (todas las unidades registradas) → Genera `F02_Tracking_Unidades_Historico_AAAA-MM-DD.xlsx`.
* **Las 18 columnas oficiales exportadas en el archivo Excel:**
  1. `Fecha`
  2. `Turno` (`D` = Diurno 06:00–17:59 / `N` = Nocturno)
  3. `Grupo`
  4. `Rampa` (número de andén o `PATIO`)
  5. `No. Economico`
  6. `Carga o Descarga (C y D)` (`C` o `D`)
  7. `Linea de Transporte`
  8. `Entrega de papeles (hora)`
  9. `Asignación de rampa (hora)`
  10. `Tiempo en carga o descarga` *(calculado automáticamente)*
  11. `Hora de Salida` *(o `EN PLANTA` si aún no sale)*
  12. `Tiempo de Actividad` *(estancia total calculada)*
  13. `Documento de Entrada`
  14. `Documento de Salida`
  15. `Guardia que Realiza Registro`
  16. `Hora de reporte a Operaciones`
  17. `Firma del personal de Operaciones`
  18. `Observaciones`

---

#### 2.2.7 Procedimientos Paso a Paso (SOPs) de Caseta de Seguridad

##### SOP 1: Recibir y validar a un chofer que se auto-registró con QR

1. Al escuchar el timbre de caseta o ver el contador en el panel **Choferes en Espera**, localice la tarjeta del transportista con estado **`✓ Enviado`**.
2. Si el formulario no se precargó automáticamente, haga clic en **Cargar en Caseta** dentro de la tarjeta del pase.
3. Revise en el **Paso 1** y **Paso 2** que el tipo de operación (*Descarga/Carga*), documento (*Remisión/Carta Porte*), cliente, línea transportista, placas y sellos coincidan físicamente con la documentación entregada por el chofer en ventanilla.
4. En el **Paso 3 (Check List F01)**, realice la inspección física del chofer (EPP: zapatos, cofia, cubrebocas, chaleco), verifique la documentación y revise los 5 puntos físicos de la caja. Ajuste a `NO` o `SÍ` cualquier hallazgo y anote detalles en **Observaciones (Manual)**.
5. En el **Paso 4**, confirme su nombre en **Responsable Vigilancia** y verifique que aparezca la tarjeta verde **Verificado** con la firma digital del chofer y la casilla de conformidad marcada.
6. Haga clic en **Mandar datos a Operaciones para asignación de rampa**. El sistema generará el folio oficial, notificará a Almacén y lo llevará a la pestaña *Unidades en Planta*.
7. Cuando aparezca el banner verde **DAR ACCESO A LA UNIDAD · PASAR A RAMPA [N]**, pulse **Dar Acceso (Levantar Pluma)** e indique al chofer dirigirse a la rampa asignada.

##### SOP 2: Registrar una entrada vehicular manual (sin QR previo)

1. En la pestaña **1. Nuevo Registro & Pases QR**, asegúrese de tener el formulario limpio (si hay datos previos, pulse **Cancelar / Limpiar**).
2. En **1. Datos Generales**, verifique la **Fecha**, seleccione **Descarga** o **Carga**, capture el **No. Remisión / Factura** (en Descarga) o **No. Carta Porte** (en Carga), seleccione el **Cliente** y confirme la **Hora Entrada**.
   * *Si aparece el banner morado de Retorno Detectado*, pulse **Precargar Datos** para autocompletar la ficha de logística inversa.
3. En **2. Datos del Transporte & Unidad**, seleccione **Línea Transportista**, escriba **Nombre Operador**, **Teléfono Chofer** (opcional), **Tipo de Transporte**, **Placas Tracto**, **No. Eco Tracto**, **Placas Caja**, **No. Eco Caja** y **Medidas Caja**.
4. Si la operación es **DESCARGA**, ingrese cada número de sello de seguridad en el campo de cinchos y presione `Enter` o **Agregar** (mínimo 1 sello). Si es **CARGA**, omita este campo (permanece bloqueado como `PENDIENTE_ANDEN`).
5. Complete los 11 criterios del **Paso 3 (Check List F01)** y registre cualquier observación relevante.
6. En **Paso 4**, capture el **Nombre del Transportista**, solicite su conformidad física y marque la casilla **Transportista Acepta y Firma de Conformidad**.
7. Pulse **Mandar datos a Operaciones para asignación de rampa**.

##### SOP 3: Reasignar una rampa con auditoría de seguridad

1. Abra la pestaña **2. Unidades en Planta & Salidas** y ubique la unidad correspondiente.
2. En la columna **Rampa**, haga clic en el botón **Cambiar** (disponible solo si la maniobra aún no inicia y no hay montacarguista asignado).
3. En el modal **Reasignación de Rampa Operativa**, despliegue **Seleccionar Nuevo Andén Destino** y elija una rampa marcada como `✓ Disponible`.
4. Escriba en **Motivo / Justificación de la Reasignación** la causa operativa detallada (mínimo 8 caracteres).
5. Confirme su **Usuario / Correo** e ingrese su **Contraseña de Acceso** en la sección de credenciales de seguridad.
6. Pulse **Confirmar y Reasignar Rampa**. El sistema validará su identidad, liberará la rampa anterior, asignará la nueva y notificará a Almacén en vivo.

##### SOP 4: Dar Check-Out (Salida de Planta) e imprimir boleta F01

1. En la pestaña **2. Unidades en Planta & Salidas**, seleccione el filtro **Listas para Salida** (o ubique las filas con estado verde pulsante **`Autorizado / Listo Salida`**).
2. Haga clic en el botón dorado **Check-Out** de la unidad.
3. Verifique el resumen de la unidad, confirme la **Hora de Salida**, capture los **Sellos de Salida** (si aplica) y anote cualquier **Observación de Salida**.
4. Pulse **Confirmar Salida y Boleta F01**.
5. En el visor del Formato F01 que se abre automáticamente, pulse **Imprimir Hoja** para entregar copia física o **Descargar PDF** para respaldo digital.

##### SOP 5: Exportar el reporte oficial F02 en Excel

1. Haga clic en **Exportar F02 Excel** en la barra superior de Caseta.
2. Seleccione el rango requerido: **Día en Curso**, **Día de Ayer**, **Mes Completo** o **Historial Total**.
3. Pulse **Descargar Archivo Excel (.xlsx)** y guarde el documento generado con las 18 columnas institucionales.

#### 2.2.8 Reglas de negocio, validaciones y solución de problemas de Caseta

* **El sistema impide registrar el Check-In:** Aparecerá un banner rojo en la parte superior detallando exactamente qué campos obligatorios faltan (por ejemplo, falta de Remisión en Descarga, falta de Carta Porte en Carga, ausencia de al menos 1 sello en Descarga o falta de marcar la casilla de conformidad del transportista). Complete todos los campos señalados y reintente.
* **No aparece el botón "Cambiar" junto a la rampa:** Verifique los candados de seguridad: si Almacén ya inició la maniobra (*Descargando/Cargando*), si ya se asignó un montacarguista o si la unidad ya está lista para salida, el cambio de rampa queda bloqueado por integridad operativa.
* **El botón "Check-Out" aparece con candado gris (*En Descarga*, *En Carga* o *En Auditoría*):** Significa que el Líder de Recepción o Salidas aún no cierra ni autoriza la maniobra en Almacén. Comuníquese con Operaciones para confirmar el cierre antes de autorizar la salida física.
* **El chofer escanea el QR en entorno local y no abre la página:** Abra **Ver QR / Configurar Túnel**, verifique que la URL pública de túnel (`ngrok`) esté activa y guardada, y vuelva a mostrar el código QR (o reimprima el cartel).

---

### 2.3 Portal Público Móvil de Auto-Registro de Choferes

#### 2.3.1 Propósito, rutas y características de acceso

| Atributo | Detalle |
| :--- | :--- |
| **Propósito operativo** | Permitir a cualquier operador de transporte (chofer) capturar desde su propio teléfono celular los datos del Formato **F01-PO-CP-7.1.3-03** y firmar digitalmente antes de llegar a la ventanilla de caseta, eliminando tiempos muertos y errores de transcripción. |
| **Acceso y autenticación** | **Portal 100% público sin usuario ni contraseña.** El chofer accede escaneando el **Cartel QR de Entrada** o un **Pase QR individual** generado por Caseta. |
| **Rutas habilitadas** | `/carrier-checkin` y `/carrier-checkin/:token` (con redirecciones automáticas desde `/public/checkin`, `/driver-checkin` y `/security/check-in`). |
| **Generación automática de folio** | Si el chofer ingresa mediante el cartel general sin token previo, el portal genera automáticamente un folio único (`PASS-4G-XXXXXX`) y notifica de inmediato a Caseta en estado *«QR esperando captura»*. Si ingresa desde un pase pre-generado, precarga los datos asociados. |
| **Modo Oscuro / Modo Sol (Alto Contraste)** | Incluye un botón en la esquina superior derecha (**☀️ Claro** / **🌙 Oscuro**) diseñado específicamente para operación en exteriores bajo luz solar directa. La preferencia se guarda automáticamente en el dispositivo del chofer y ajusta también el color del trazo en el recuadro de firma. |

![Portal Móvil de Auto-Registro de Chofer](screenshots/03-carrier-checkin.png)

#### 2.3.2 Asistente de 3 pasos y tabla completa de campos y validaciones

El portal guía al chofer mediante una barra superior de progreso de 3 pasos (*1. Operación → 2. Transporte → 3. Firma*). No permite avanzar al siguiente paso si existen campos obligatorios incompletos; en tal caso, muestra un banner de advertencia en la parte superior con la lista puntual de datos faltantes. Todos los campos de texto se convierten automáticamente a **MAYÚSCULAS**.

##### Paso 1 de 3: Tipo de Operación y Documento

| Campo | Obligatoriedad | Opciones / Formato | Validaciones y comportamiento |
| :--- | :--- | :--- | :--- |
| **Tipo de Operación** | Obligatorio (`*`) | Botones táctiles grandes: **📥 DESCARGA** (*Recepción / Entrega*) o **📤 CARGA** (*Embarque / Recolección*). | Predeterminado en `DESCARGA`. Cambiar a `CARGA` deshabilita la captura de sellos en el Paso 2 y cambia el documento requerido a Carta Porte. |
| **Empresa / Cliente Destinatario** | Obligatorio (`*`) | Lista desplegable conectada al catálogo de clientes + opción **➕ Escribir otro cliente...**. | Si elige *Escribir otro cliente*, despliega un campo de texto libre con botón para regresar a la lista. |
| **Tipo de Documento de Descarga** *(Solo en Descarga)* | Obligatorio en Descarga (`*`) | Selector táctil: **📄 Remisión** o **🧾 Factura**. | Adapta la etiqueta y el ejemplo del campo de número de documento. |
| **Número de Remisión / Factura** *(Solo en Descarga)* | Obligatorio en Descarga (`*`) | Texto alfanumérico (MAYÚSCULAS). | No puede quedar vacío para avanzar al Paso 2. |
| **Número de Carta Porte** *(Solo en Carga)* | Obligatorio en Carga (`*`) | Texto alfanumérico (MAYÚSCULAS). | No puede quedar vacío para avanzar al Paso 2. |

##### Paso 2 de 3: Datos del Chofer, Unidad y Sellos

| Campo | Obligatoriedad | Opciones / Formato | Validaciones y comportamiento |
| :--- | :--- | :--- | :--- |
| **Línea Transportista / Fletera** | Obligatorio (`*`) | Botones táctiles rápidos con las líneas registradas + botón **➕ Otra Línea**. | Si selecciona *Otra Línea*, habilita campo manual con opción de volver a la lista. |
| **Nombre Completo del Operador / Chofer** | Obligatorio (`*`) | Texto libre (MAYÚSCULAS). | Mínimo **3 caracteres**. |
| **Teléfono Celular del Operador** | Obligatorio (`*`) | Numérico exacto de **10 dígitos** (etiquetado `📱 WhatsApp / SMS`). | Validación estricta de 10 dígitos numéricos para contacto directo de vigilancia y avisos de rampa. |
| **Tipo de Transporte** | Obligatorio (`*`) | Chips táctiles: *Tortón*, *Tráiler*, *Otro (Especificar)*. | Si elige *Otro (Especificar)*, muestra campo de texto libre. |
| **Medidas Caja** | Obligatorio (`*`) | Chips táctiles: *53 Pies*, *48 Pies*, *40 Pies*, *20 Pies*, *Tortón*, *Rabón*, *3.5 Toneladas*, *N/A - Plataforma*, *Otra Medida*. | Si elige *Otra Medida*, muestra campo de texto libre. |
| **Placas Tracto** | Obligatorio (`*`) | Alfanumérico (MAYÚSCULAS). | Mínimo **3 caracteres**. |
| **No. Eco Tracto** | Opcional | Alfanumérico (MAYÚSCULAS). | Número económico del tractocamión. |
| **Placas Caja** | Obligatorio (`*`) | Alfanumérico (MAYÚSCULAS). | Mínimo **3 caracteres**. |
| **No. Eco Caja** | Opcional | Alfanumérico (MAYÚSCULAS). | Número económico del remolque. |
| **Sellos de Seguridad (Cinchos)** | **Obligatorio en Descarga** (mínimo 1). **No aplica en Carga**. | Campo de texto + botón **+ Agregar** (o tecla `Enter`). Se muestran como etiquetas con botón `×` para eliminar. | **Regla de Carga:** Bloqueado con aviso informativo indicando que los sellos se colocan en el andén al concluir el embarque (envía automáticamente `N/A - Sellos asignados en andén al concluir carga`). **Regla de Descarga:** Exige al menos un número de sello; si el chofer escribió uno sin pulsar *+ Agregar*, el portal lo agrega automáticamente al avanzar. |
| **Observaciones Adicionales** | Opcional | Área de texto (MAYÚSCULAS). | Si se deja vacía, se registra automáticamente la nota *«REGISTRO COMPLETADO POR CHOFER VÍA SMARTPHONE»*. |

##### Paso 3 de 3: Firma Digital Táctil y Declaración Legal

| Elemento | Obligatoriedad | Comportamiento y validación |
| :--- | :--- | :--- |
| **Recuadro de Firma Digital del Chofer** | Obligatorio (`*`) | Lienzo táctil compatible con dedo, lápiz óptico o ratón. Muestra el estado `⏳ Pendiente` y cambia a `✓ Firma Registrada` al realizar el trazo. Incluye botón **🔄 Limpiar Firma** para repetirla si es necesario. |
| **Declaración bajo protesta de decir verdad** | Obligatorio (`*`) | Casilla de verificación legal obligatoria: *«Declaro bajo protesta de decir verdad que los datos ingresados, sellos y la condición física del transporte corresponden a la realidad de la unidad y acepto las normas de seguridad de planta.»* |
| **Botón "Enviar Registro a Caseta"** | — | Permanecerá deshabilitado hasta que la firma esté trazada, la declaración marcada y todos los pasos válidos. Al enviarse, transmite los datos y la imagen de la firma directamente a Caseta. |

#### 2.3.3 Estados en vivo posteriores al envío y función WhatsApp

Una vez enviado el registro, el portal cambia a modo de seguimiento en tiempo real consultando automáticamente al servidor cada **2.5 segundos**:

1. **Estado 1 — Registro Recibido en Caseta (En Espera de Asignación de Rampa):**
   * Muestra un reloj de arena animado, el número de pase (`#PASS-4G-...`), el resumen completo de los datos capturados y la instrucción de permanecer en la unidad en el carril de acceso.
   * **Botón "💬 Guardar Pase por WhatsApp"**: Abre WhatsApp con un mensaje pre-formateado que incluye folio, operación, chofer, transportista, placas y documento para guardarlo o enviarlo a su base logística.
   * **Botón "✏️ Modificar Datos / Volver al Formulario"**: Permite corregir algún dato capturado erróneamente antes de que Caseta valide el acceso.

2. **Estado 2 — ¡Acceso a Planta Autorizado! (Actualización automática en vivo):**
   * En cuanto el guardia de Caseta valida y autoriza el ingreso, la pantalla del celular cambia automáticamente a color **verde esmeralda** y hace **vibrar el teléfono** (en dispositivos compatibles) para alertar al chofer.
   * Despliega una tarjeta destacada gigante con el destino asignado: **🚛 ANDÉN / RAMPA ASIGNADA (`Rampa R-XX`)** o **📍 ZONA DE ESPERA ASIGNADA (`En Espera en Patio`)**, junto con las instrucciones de maniobra.
   * Muestra el **Código QR del Pase de Validación** con el folio oficial para presentar en andén, el botón **💬 Compartir Pase por WhatsApp** y el resumen validado de la unidad y sellos.

#### 2.3.4 Procedimiento Paso a Paso (SOP): Auto-Registro del Chofer desde Smartphone

1. Al llegar al carril de acceso, abra la cámara de su teléfono celular y apunte al **Cartel Oficial QR** ubicado en caseta; toque el enlace emergente para abrir el portal **4GUARD WMS**.
2. Si se encuentra bajo luz solar intensa y requiere mayor contraste, toque el botón **☀️ Claro** en la esquina superior derecha para activar el **Modo Sol**.
3. **Paso 1:** Toque **DESCARGA** o **CARGA**, seleccione el **Cliente Destinatario** (o elija *Escribir otro cliente*), capture el número de **Remisión / Factura** o **Carta Porte** y pulse **Continuar a Datos del Transporte**.
4. **Paso 2:** Seleccione su **Línea Transportista**, escriba su **Nombre Completo** y **Teléfono Celular a 10 dígitos**, seleccione con un toque el **Tipo de Transporte** y **Medidas de Caja**, y capture **Placas Tracto**, **Placas Caja** y números económicos.
5. Si su visita es de **DESCARGA**, escriba el número de sello o cincho de seguridad y pulse **+ Agregar** (repita si trae más de un sello). Pulse **Continuar a Firma Digital**.
6. **Paso 3:** Dibuje su firma con el dedo dentro del recuadro táctil, marque la casilla de **Declaración bajo protesta de decir verdad** y toque **Enviar Registro a Caseta**.
7. Permanezca en su unidad atento a la pantalla: si desea conservar comprobante, toque **Guardar Pase por WhatsApp**. Cuando su teléfono vibre y muestre la pantalla verde con su **Rampa Asignada** o **Patio**, avance con precaución siguiendo las normas de seguridad (velocidad máxima 10 km/h y EPP completo).

#### 2.3.5 Solución de problemas del Portal de Choferes

* **No puedo avanzar del Paso 2 y me marca error en el teléfono:** Asegúrese de ingresar exactamente **10 dígitos numéricos**, sin espacios, guiones ni prefijo de país (`+52`).
* **El botón "Enviar Registro a Caseta" no se activa en el Paso 3:** Verifique que haya dibujado su firma dentro del recuadro (debe mostrar la etiqueta verde `✓ Firma Registrada`) y que la casilla de declaración legal esté marcada.
* **Capturé mal una placa o remisión después de enviar:** Mientras la pantalla siga en estado ámbar (*En Espera de Asignación de Rampa*), pulse **✏️ Modificar Datos / Volver al Formulario**, corrija el dato y vuelva a enviar. Si ya fue autorizado, notifíquelo de inmediato al guardia en ventanilla.
* **Error de conexión al enviar el registro:** Verifique tener datos móviles activos o señal Wi-Fi de visitantes de caseta y presione nuevamente **Enviar Registro a Caseta**; el formulario conserva todos los datos capturados para evitar re-escribirlos.
