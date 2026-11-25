## Capítulo 8. Terminal RF — Operación Móvil en Piso (PWA)

La **Terminal RF de 4GUARD WMS** es la aplicación operativa de piso diseñada específicamente para tabletas industriales, lectores portátiles (Zebra, Honeywell) y dispositivos móviles (Android / iOS) que operan en andenes, pasillos de racks, cámaras, zona de cuarentena y caseta de vigilancia. Su interfaz táctil de alto contraste (con modo claro y modo oscuro) está construida bajo el principio de **"una acción prioritaria por pantalla"** y arquitectura **Offline-First** (resiliente ante cortes de señal Wi-Fi en zonas ciegas del almacén).

Este capítulo constituye el **Manual Oficial de Operación en Piso** y documenta los 9 módulos operativos completos, sus matrices de permisos y redirecciones por rol, las señales visuales, sonoras y hápticas, todos los candados de seguridad y los Procedimientos Estándar de Operación (SOP) paso a paso.

---

### 8.1 Acceso a la Terminal RF, Shell Industrial y Menú Principal Dinámico

#### 8.1.1 Métodos de Autenticación en Piso

La pantalla de acceso de la Terminal RF ofrece tres mecanismos de identificación diseñados para operar con guantes, en cabina de montacargas o en tabletas compartidas entre turnos:

| Método de Acceso | Cuándo Utilizarlo | Flujo y Reglas de Seguridad |
| :--- | :--- | :--- |
| **1. Código de Operador + PIN Numérico (4 dígitos)** | Acceso estándar en cualquier terminal, especialmente cuando el operador utiliza careta, cubrebocas o lentes de protección industrial. | El operador ingresa su **Código de Operador / Gafete** (ejemplo: `MC-001`, se convierte automáticamente a mayúsculas) y teclea su **PIN de 4 dígitos** en el teclado numérico industrial (`0-9`, `C` para limpiar todo, `⌫` para borrar el último dígito). Al pulsar **Confirmar Acceso PIN**, el sistema autentica contra el servidor central o, si la terminal está sin red, contra el caché seguro local (mostrando el aviso *"Modo Sin Conexión — Autenticando con caché seguro local"*). |
| **2. Face ID / Biometría de Plataforma (W3C FIDO2)** | Dispositivos personales o dedicados con sensor biométrico nativo (iPad, iPhone, tablet Android con reconocimiento facial/huella del sistema). | Si el dispositivo cuenta con sensor biométrico y el operador ya vinculó su cuenta, la terminal selecciona automáticamente esta pestaña al abrirse para ingresar en **menos de 1 segundo**. No almacena fotografías en servidores (*Zero Biometric Knowledge*). Tras un inicio de sesión por PIN en un dispositivo compatible no vinculado, aparece la tarjeta **¿Activar Face ID?** con las opciones **Vincular Face ID Ahora** o **En otro momento**. Incluye el botón de respaldo *"¿Llevas careta o lentes de protección? Ingresar con PIN"*. |
| **3. Reconocimiento Facial Multi-Operador (Cámara Frontal)** | **Tabletas compartidas** por varios montacarguistas en distintos turnos (ejemplo: cambio de turno en cabina). | Al pulsar **📷 IDENTIFICAR ROSTRO** (o el botón **📷 IDENTIFICARME CON FACE ID (CÁMARA)**), se abre el visor de cámara frontal. Todo el análisis biométrico se procesa localmente en la tableta (ningún video sale del dispositivo) y funciona sin conexión a internet. Compara el rostro contra el padrón local de operadores con un umbral de seguridad estricto (distancia máxima `0.5`) y muestra el porcentaje de coincidencia antes de iniciar sesión automáticamente. |

##### Cambio Obligatorio de PIN (Primer Acceso o Restablecimiento)
Cuando un administrador o supervisor restablece el PIN de un operador, al iniciar sesión el sistema bloquea el acceso operativo y despliega la pantalla **Configura tu PIN Personal**:
1. **Paso 1 de 2:** Ingresar el nuevo PIN personal de **exactamente 4 dígitos** usando el teclado numérico en pantalla y pulsar **Continuar a Confirmación**.
2. **Paso 2 de 2:** Volver a ingresar los mismos 4 dígitos y pulsar **Guardar PIN y Entrar**.
3. **Validaciones:** Si los dos ingresos no coinciden, la terminal limpia el campo de confirmación y solicita reintentar. El operador puede pulsar **Cambiar PIN** para regresar al Paso 1 o **Cancelar** (lo que cierra la sesión temporal por seguridad).

##### SOP — Enrolamiento e Identificación Facial Multi-Operador (3 Muestras)
1. En la pantalla de inicio de sesión, pulsar **📷 IDENTIFICAR ROSTRO**.
2. Si la tableta aún no tiene rostros registrados (o si se pulsa **➕ Enrolar mi cara** desde el visor), se muestra el padrón de operadores de la terminal con foto en miniatura (si ya están enrolados) o ícono genérico.
3. Localizar el nombre del operador y pulsar **Enrolar** (o **Re-enrolar** para actualizar sus rasgos; también existe el botón de papelera para borrar el rostro de la tableta).
4. Mirar de frente a la cámara frontal dentro del óvalo guía y mover ligeramente la cabeza mientras el sistema captura **3 muestras faciales consecutivas** (la barra de progreso avanza en pasos de `33% → 67% → 100%` con una breve vibración de confirmación en cada muestra y toma una miniatura de avatar).
5. **Tiempo límite de captura:** El visor otorga hasta **25 segundos** por intento. Si no detecta un rostro claro por falta de iluminación o distancia excesiva, emite alarma de error y permite pulsar **Reintentar**.
6. Una vez registrado, en turnos futuros basta pulsar **📷 IDENTIFICAR ROSTRO**, mirar a la cámara y en menos de 2 segundos el visor muestra el mensaje de bienvenida (*"¡Hola [Nombre]!"*, código de empleado y porcentaje de coincidencia), emite un bip agudo + doble vibración y abre la sesión del operador.

---

#### 8.1.2 Shell Industrial (Barra Superior, Navegación Inferior y Herramientas de Hardware)

Una vez autenticado, el operador trabaja dentro del **Shell Industrial** de la Terminal RF, compuesto por cuatro zonas permanentes:

1. **Encabezado Industrial (Barra Superior):**
   - **Logo 4GUARD Terminal RF:** Toque directo para regresar al Menú Principal (`/menu`).
   - **Reloj Oficial en Vivo:** Muestra la hora exacta en formato 24 horas (`HH:MM:SS`) y la fecha (`DÍA DD MES AAAA`) actualizadas cada segundo.
   - **Insignia de Módulo / Zona Activa:** Cambia dinámicamente según la pantalla en curso (*Caseta de Vigilancia*, *Control de Calidad*, *Descarga en Andén*, *Carga en Andén*, *Acomodo en Racks*, *Mis Maniobras*, *Revisión de Inventario*, *Sincronización*).
   - **Semáforo de Red y Cola Offline:**
     - **En Línea (`XX%`):** Muestra la fuerza de señal Wi-Fi y latencia en milisegundos.
     - **`N pend.` (Ámbar pulsante):** Indica operaciones guardadas localmente en IndexedDB pendientes de subir; tocarlo abre directamente el Monitor de Sincronización (`/sync`).
     - **Sin Red (Rojo):** Indica desconexión total; la terminal sigue operando en modo offline.
   - **Botones de Ciclo de Vida PWA:**
     - **Actualizar (Ámbar pulsante):** Aparece automáticamente cuando existe una nueva versión lista (verificación automática cada 5 minutos sin interrumpir escaneos activos).
     - **Instalar App / Instalar (iOS):** Permite instalar la Terminal RF en pantalla completa sin barras de navegador. En iPad/iPhone abre una guía visual de 3 pasos (*Compartir → Agregar a pantalla de inicio → Agregar*).
   - **Centro de Notificaciones Operativas:** Campana en vivo con alertas de pre-recepciones, asignaciones de rampa y pases QR de choferes.
   - **Asistente ForBot AI:** Acceso táctil para consultas operativas y asistencia por voz en andén.
   - **Selector de Tema (Claro / Oscuro):** Cambia instantáneamente entre modo claro (alta legibilidad bajo luz solar en patio/caseta) y modo oscuro (reducción de fatiga visual en nave/cámara).
   - **Menú de Perfil de Operador (Iniciales):** Al tocarlo despliega:
     - Nombre completo, usuario (`@usuario`), rol certificado, CEDIS/Planta, tipo de dispositivo detectado, módulo activo y modo de ejecución (*PWA Standalone* vs *Navegador Web*).
     - **Interruptor de Pantalla Activa (Wake Lock):** Activa o desactiva el bloqueo de suspensión de pantalla (*"Activa (Wake Lock ON)"* vs *"Inactiva (Auto-sleep)"*) para mantener la tableta siempre encendida durante el escaneo continuo. Si el operador cambia de aplicación y regresa, el sistema re-adquiere el Wake Lock automáticamente.
     - **Cerrar Sesión del Terminal:** Abre un modal de confirmación (*"¿Cerrar Sesión del Turno?"*) para evitar cierres accidentales antes de regresar a la pantalla de acceso.

2. **Banner Global de Conectividad, Cola IndexedDB y Zone Lease:**
   Se despliega automáticamente debajo del encabezado únicamente cuando existe alguna condición crítica: modo offline activo, operaciones pendientes en cola, sincronización en curso, advertencia o expiración de Zone Lease (30 min), o cola llena (`500/500`). Incluye barra de ocupación porcentual, botón **Sincronizar**, botón urgente **RENOVAR LEASE** (si expiró) y un cajón desplegable con el listado secuencial FIFO de operaciones pendientes.

3. **Detección Automática de Escáner Láser vs. Captura Manual:**
   El componente de entrada industrial de la terminal distingue en tiempo real cómo se ingresó un código:
   - **Lectura por Pistola Láser 2D / DataWedge (Zebra / Honeywell):** Cuando los caracteres llegan en ráfaga rápida (menos de **100 ms** entre teclas) y cierran con `Enter`, el campo enciende el indicador **"Escaneando..."**, emite una vibración corta de confirmación (`50 ms`) y procesa el código automáticamente. También detecta disparadores físicos (`F9`, `F10` o `Ctrl+B`).
   - **Captura Manual por Teclado:** Si el operador teclea lentamente el código y presiona `Enter`, el sistema lo marca como captura manual, emite un pulso háptico triple (`30-30-30 ms`) y procesa la lectura.
   - **Simulador de Escáner Láser (Soporte / Capacitación):** Permite disparar códigos predefinidos (*SSCC Tarima*, *Ubicación `A-01-01`*, *Andén `BAHIA-ANDEN-02`*, *EAN SKU*) o un código personalizado, inyectándolo como si proviniera del láser físico.

4. **Catálogo Maestro de Retroalimentación Auditiva y Háptica (Entornos Ruidosos):**
   La terminal utiliza un motor acústico con amplificación de ganancia (`+60%` y compresor dinámico) y patrones de vibración diferenciados para que el operador distinga resultados sin apartar la vista del montacargas:

| Evento Operativo | Señal Visual | Señal Sonora (Frecuencia / Tipo) | Patrón Háptico (Vibración) |
| :--- | :--- | :--- | :--- |
| **Escaneo válido / Acción exitosa** | Destello verde / Toast verde | Bip agudo senoidal limpio (`880 Hz`, `160 ms`) | 1 pulso corto (`50 ms`) |
| **Nueva recepción / Arribo asignado** | Toast verde + Tarjeta en cola | Campana armónica amigable (triada mayor D5–F#5–A5–D6) | Triple pulso suave (`40-60-40 ms` o `150-80-150-80-250 ms`) |
| **Notificación general operativa** | Insignia en campana superior | Audio de notificación amplificado (`+60%`) | 1 pulso medio (`60 ms`) |
| **Advertencia / Zone Lease por vencer / Cambio de turno** | Banner o Toast ámbar | Tres bips cortos triangulares (`580 Hz`) | Doble pulso largo (`150-100-150 ms`) |
| **Error de escaneo / Rack equivocado / Bloqueo QM** | Banner o Toast rojo | Buzzer grave diente de sierra (`220 Hz → 160 Hz`, `400 ms`) | Doble pulso firme (`100-50-100 ms`) |
| **Violación FEFO en Carga de Camión** | **Pantalla Roja completa (3 s)** | Buzzer cuadrado pulsante de emergencia (`320 → 200 → 320 Hz`, `600 ms`) | Bloqueo visual + sonoro durante 3 segundos |

---

#### 8.1.3 Menú Principal Dinámico y Matriz de Roles / Redirecciones

Al iniciar sesión, la Terminal RF adapta automáticamente **el destino de aterrizaje**, **la barra de navegación inferior**, **el Banner Hero de Tarea Activa** y **las tarjetas de módulos visibles** según el rol del usuario. Adicionalmente, un candado de seguridad por rol impide ingresar a rutas no autorizadas (por ejemplo, un Guardia no puede entrar al Cockpit ni a Calidad, y un Montacarguista no puede entrar a Caseta).

| Rol en 4GUARD WMS | Pantalla de Aterrizaje al Entrar | Barra de Navegación Inferior | Módulos Visibles en Menú Principal | Redirección Automática si Intenta Ruta No Autorizada |
| :--- | :--- | :--- | :--- | :--- |
| **Montacarguista** (`WAREHOUSE_OPERATOR`) | Menú (`/menu`) | Inicio · Descargar · Cargar · Acomodar | Descargar Camión (Entrada), Cargar Camión (Salida), Acomodar en Racks *(+ acceso directo a Cockpit, Conteo, Sync y Anomalías)* | Redirige automáticamente a `/cockpit` |
| **Guardia de Seguridad** (`SECURITY_GUARD`) | Caseta (`/security`) | Inicio · Caseta | Registrar Entrada F01, Patio & Andenes Activos, Salida & Check-Out, Control de Caseta & Registro | Redirige automáticamente a `/security` |
| **Ayudante de Seguridad** (`SECURITY_ASSISTANT`) | Menú (`/menu`) | Inicio · Caseta | Registrar Entrada F01, Control de Caseta & Registro | Redirige automáticamente a `/security` |
| **Inspector de Calidad QM** (`QM_INSPECTOR`) | Menú (`/menu`) | Inicio · Calidad | Calidad & Cuarentena QM, Conteo Cíclico e Inventario | Redirige automáticamente a `/quality` |
| **Ayudante de Calidad** (`QUALITY_ASSISTANT`) | Menú (`/menu`) | Inicio · Calidad | Calidad & Cuarentena QM, Conteo Cíclico e Inventario | Redirige automáticamente a `/quality` |
| **Líder de Turno** (`SHIFT_LEADER`) | Menú (`/menu`) | Inicio · Descargar · Cargar · Acomodar | Descargar Camión, Mis Maniobras (Cockpit), Seguridad & Caseta, Conteo Cíclico, Impresión Zebra SSCC *(Supervisión completa habilitada)* | Acceso completo de supervisión |
| **Mesa de Control** (`CONTROL_DESK`) | Menú (`/menu`) | Barra completa (9 accesos) | Descargar Camión, Mis Maniobras, Seguridad & Caseta, Conteo Cíclico, Impresión Zebra SSCC | Acceso completo de supervisión |
| **Supervisor / Coordinador / Gerente de Operaciones** | Menú (`/menu`) | Barra completa (9 accesos) | Descargar Camión, Mis Maniobras, Seguridad & Caseta, Conteo Cíclico, Impresión Zebra SSCC | Acceso completo de supervisión |
| **Director General / Admin** (`ADMIN`) | Menú (`/menu`) | Barra completa (9 accesos) | Descargar Camión, Mis Maniobras, Conteo Cíclico, Impresión Zebra SSCC *(acceso total a todos los módulos)* | Acceso total sin restricciones |

##### Barra de Telemetría de Hardware y Acciones Rápidas (Pie del Menú Principal)
En la parte inferior del Menú Principal, todo operador dispone de:
- **Chip Láser 2D Activo:** Muestra si el escáner industrial está listo; al tocarlo dispara un pulso de prueba visual (*"¡Láser Disparado!"*).
- **Chip Impresora Zebra BT:** Indica estado en tiempo real (`Zebra BT Conectada` en verde con modelo `Zebra ZT411` y nivel de batería, o `Zebra Desconectada` en rojo).
- **Chip Wi-Fi Industrial:** Muestra banda (`5GHz` / `2.4GHz`), porcentaje de señal y latencia (`ms`), con semáforo de color (`≥80%` verde, `55–79%` azul, `30–54%` ámbar, `<30%` o sin red en rojo).
- **Botón Sincronizar:** Acceso directo al Monitor de Sincronización (`/sync`).
- **Botón Reportar Incidencia:** Abre un modal de confirmación rápida (*"Reporte de Siniestro / Anomalía — Apertura de expediente con foto obligatoria"*) que redirige a `/anomaly`.

---

### 8.2 Cockpit del Montacarguista (`/cockpit`)

El **Cockpit Unificado del Montacarguista** concentra en una sola consola las tres familias de maniobras asignadas al operador en orden de llegada (**FIFO**): **Descargas de Tráiler (Entrada)**, **Acomodo en Racks / Reubicación Interna** y **Cargas de Salida (Embarques)**. El operador únicamente visualiza las misiones asignadas a su identidad (o abiertas en andén), mientras que los supervisores visualizan toda la operación del turno.

#### 8.2.1 Arquitectura Operativa del Cockpit

- **Barra de Filtros Tácticos:** `Todas en Cola`, `Descargas (Entrada)`, `Acomodo en Racks`, `Cargas (Salida)` y `Urgentes`, cada una con su contador dinámico en vivo.
- **Tarjeta Hero de Misión Activa:** Muestra folio, número de remisión, hora de llegada, cuenta regresiva SLA en minutos, corredor visual **1. Origen ➔ 2. Destino** (con códigos de barras esperados), producto/SKU, peso estimado, placas y chofer.
- **Cola Dinámica (Flujo FIFO):** Lista inferior de misiones pendientes ordenadas por arribo. Al tocar cualquier tarjeta se abre el **Plan Detallado de Misión** (paso 1 recogida, paso 2 depósito, lotes autorizados y tabla de tarimas ya descargadas) con el botón para iniciarla de inmediato.
- **Sincronización Continua y Alertas QM en Tiempo Real:** Cada **5 segundos** sincroniza la cola y la cola offline local con la base de datos central. Además, mantiene un canal en vivo para recibir alertas inmediatas de Calidad: si Calidad bloquea una tarima que el operador está manipulando, el sistema dispara alarma grave + vibración larga y congela la misión en estado **`⚠️ DETENIDA POR CALIDAD (QM)`**, prohibiendo su traslado a rack estándar (solo permite llevarla a Bahía de Cuarentena / QM).

#### 8.2.2 Interrupción por Prioridad Urgente (Preemption) y Candado `ACTIVE_DROP`

Cuando llega una misión con prioridad **URGENTE** mientras el montacarguista atiende una misión normal, aparece el banner rojo parpadeante **`🚨 PRIORIDAD URGENTE DETECTADA`** con el botón **PAUSAR ACTUAL Y ATENDER URGENTE**.

> [!IMPORTANT]
> **Candado de Seguridad Física `ACTIVE_DROP` (Carga sobre Horquillas):**
> Si el montacarguista ya escaneó y levantó una tarima (estado **Paso 2: Depósito en Destino / `ACTIVE_DROP`**), el sistema **BLOQUEA** la interrupción urgente y muestra el aviso: *"Tienes una tarima sobre las horquillas. Concluye el depósito asignado o déjala en bahía de buffer antes de tomar la urgencia"*. Solo cuando las horquillas están libres (`ACTIVE_PICK` o `QUEUED`) permite pausar la misión actual, conservando intactas todas las tarimas ya descargadas, el lote activo y el contador correlativo.

Al concluir la misión urgente, la terminal muestra el banner verde **`🔄 MISIÓN PREVIA EN PAUSA`** (o la reanuda automáticamente) con el botón **REANUDAR MISIÓN (TARIMA #N)** para continuar exactamente donde se interrumpió, sin perder ningún avance.

#### 8.2.3 Cámara HD, Anti-Rebote de 3 Segundos y Conmutación Rápida de Lotes

Al iniciar la maniobra, se abre el **Visor de Lectura Láser y Cámara HD**:
- **Cámara Full HD adaptativa (`1080p → 720p`)** con autoenfoque continuo y compensación de exposición anti-deslumbramiento para etiquetas blancas brillantes. Permite alternar entre cámara trasera y frontal (**Girar**), expandir la pantalla o pausar la cámara.
- **Escudo Anti-Rebote de 3 segundos:** Si el mismo código de barras permanece frente a la lente, la terminal ignora lecturas duplicadas durante **3 segundos** sin pitar ni arrojar falsos errores de duplicado.
- **Atajos de Teclado `1` a `9` (Cambio de Lote al Vuelo):** Durante una descarga inbound, presionar las teclas numéricas `1` al `9` (cuando no se está escribiendo en un campo de texto) selecciona instantáneamente el lote correspondiente de la lista de lotes autorizados con confirmación sonora.
- **Conmutación Zero-Touch por Láser:** Si el operador escanea directamente el código de barras de un lote autorizado (o una etiqueta GS1 con identificador `(10)` de lote), la terminal cambia el lote activo automáticamente sin tocar la pantalla.
- **Botón `+ Lote` / Intercambio de Lote:** Permite registrar un lote físico adicional encontrado en el tráiler (embarque mixto) o corregir el lote/piezas de una tarima ya escaneada, registrando el motivo (por defecto *"Embarque Mixto / Multi-Lote de Proveedor"*).

#### 8.2.4 Regla de Evaluación Automática de Vida Útil (Política MRP 75% / 50%)

Al registrar o editar un lote con fecha de caducidad y fecha de elaboración en el Cockpit, la terminal calcula automáticamente la vida útil restante y aplica los candados MRP:

| Escenario de Vida Útil Calculada | Umbral Requerido | Comportamiento del Sistema en Piso |
| :--- | :---: | :--- |
| **Producto caducado** (días restantes `< 0`) | Rechazo absoluto | **BLOQUEO DURO:** Emite buzzer de error y mensaje *"🛑 Fecha de caducidad inválida: El lote está vencido (hace X días). No se permite el recibo"*. Impide guardar el lote. |
| **Vida útil total menor a 24 meses** (`< 24m`) | **Mínimo 75% restante** | Si cumple (`≥ 75%`): Insignia verde conforme. Si está debajo (`< 75%`): Advertencia ámbar *"⚠️ Excepción MRP"* y exige registrar el motivo/autorización antes de continuar. |
| **Vida útil total de 24 meses o más** (`≥ 24m`) | **Mínimo 50% restante** | Si cumple (`≥ 50%`): Insignia verde conforme. Si está debajo (`< 50%`): Advertencia ámbar *"⚠️ Excepción MRP"* con registro de motivo. |
| **Sin fecha de elaboración informada** | **Estimado sobre 730 días (mín. 75%)** | Calcula el porcentaje estimado contra 24 meses estándar y aplica la alerta de excepción si queda debajo del 75%. |

#### 8.2.5 Cierre Total (`DISCHARGED`), Corte Parcial por Fin de Turno, Pausa de Comida y Anomalía

| Acción de Cierre / Contingencia | Cuándo Aplicarla | Candados y Efecto Operativo |
| :--- | :--- | :--- |
| **Concluir Descarga (`DISCHARGED`)** | Al bajar la última tarima del tráiler. | **Candado Cero Invenciones:** Exige haber escaneado y depositado **al menos 1 tarima**. Abre modal de confirmación con el desglose por lote, cambia el estatus a `DISCHARGED`, actualiza la ocupación de la bahía en el mapa y turna el manifiesto a Mesa de Control para emitir el Formato F01. Muestra modal de celebración. |
| **Corte Parcial por Fin de Turno (`PARTIAL_DISCHARGED`)** | Cuando faltan **15 minutos o menos** para terminar el turno (la terminal despliega automáticamente el **Banner Preventivo de Aviso de Relevo**) y el tráiler aún no termina de descargarse. | Permite pulsar **Descarga Parcial**, genera una nota de relevo automática (*"Corte por cambio de turno (HH:MM). N tarimas descargadas en buffer. Tráiler acoplado para relevo"*), guarda las tarimas ya bajadas en la base de datos y deja la misión lista en estatus `PARTIAL_DISCHARGED` para que el montacarguista del siguiente turno pulse **REANUDAR (T#N+1)** con prioridad automática. |
| **Pausa por Horario de Comida** | Durante la ventana autorizada de colación configurada para el turno. | **Candado de Ventana de Horario:** Solo permite pausar dentro del horario de comida o con la anticipación máxima autorizada (por defecto **10 minutos antes** del inicio). Fuera de esa ventana, rechaza la pausa indicando la hora programada. Al pausar, guarda el avance intacto y recuerda: *"Resguarda tu unidad con uñas a piso"*. |
| **Posponer Misión** | Cuando la rampa o el material presentan obstrucción temporal. | Envía la misión activa al final de la cola FIFO y promueve la siguiente. |
| **Reportar Daño / Anomalía** | Ante tarima colapsada, inclinada o producto roto en maniobra. | Abre el modal de reporte rápido (tipo de incidencia, notas y evidencia fotográfica), detiene la maniobra en estado de retención y notifica a Calidad. |

#### 8.2.6 SOP — Operación Estándar en el Cockpit del Montacarguista
1. Ingresar a **Mis Maniobras (`/cockpit`)** y verificar el indicador **BD WMS: En Línea** (o **Buffer Local** si está sin red).
2. Revisar la **Tarjeta Hero** de la misión activa (o tocar una tarjeta de la cola FIFO y pulsar **VER & INICIAR**).
3. Verificar el **Lote Activo** en los chips de lotes habilitados (usar toque, teclas `1–9` o escanear el código del lote). Si llega un lote no listado, pulsar **`+ Lote`**, capturar nomenclatura y fechas para validación MRP.
4. Pulsar el botón principal **ESCANEAR TARIMA #N** para abrir el visor láser/cámara.
5. **Paso 1 (`ACTIVE_PICK`):** Escanear la etiqueta SSCC/UA de la tarima al retirarla del tráiler o rack origen. Confirmar el bip agudo y el cambio visual a **Paso 2: Depósito en Destino (`ACTIVE_DROP`)**.
6. **Paso 2 (`ACTIVE_DROP`):** Trasladar la tarima a la bahía o buffer indicado y escanear la etiqueta de la ubicación destino. El sistema registra la tarima en el manifiesto y regresa automáticamente a `ACTIVE_PICK` para la siguiente tarima.
7. Al finalizar la última tarima del tráiler, pulsar **Concluir Descarga (N T)** y confirmar el cierre a estatus **`DISCHARGED`** (o **Descarga Parcial** si concluye el turno).

---

### 8.3 Recepción y Descarga en Andén (`/receiving`)

El módulo de **Recepción en Andén** está diseñado para la descarga intensiva y continua en rampa con tableta montada o en mano, permitiendo ajustar parámetros de producto, piezas, tipo de tarima y lote al vuelo sin detener el ritmo de escaneo.

#### 8.3.1 Sincronización en Vivo y Selección de Unidad
- **Polling automático cada 4 segundos:** Consulta continuamente las recepciones activas en andén (excluye completadas y canceladas) y actualiza las tarimas registradas en tiempo real.
- **Ficha de Recepción Activa:** Muestra Folio/Remisión, Cliente, Transportista y Chofer, Placas de Tractor y Caja, Rampa asignada y Bahía Destino Staging (`STAGING-RXX`). Si hay varias unidades en andén, el botón **Cambiar Unidad (N)** abre el selector rápido por número de rampa.

#### 8.3.2 Barra de Parámetros Activos al Vuelo
Cada parámetro seleccionado en esta barra se aplica automáticamente a todas las tarimas escaneadas a partir de ese momento:
1. **Producto (SKU) Activo:** Selector sincronizado con el catálogo maestro de productos; al cambiar de SKU actualiza automáticamente la cantidad estándar de piezas por tarima.
2. **Piezas por Tarima:** Control ergonómico con botones paso a paso (`-10`, `-1`, campo numérico directo, `+1`, `+10`) y **4 presets de un toque**: `135 (Cama)`, `300`, `324` y `48 (Pico)`.
3. **Tipo de Tarima:** Selector de embase físico (`Madera Estándar`, `CHEP`, `Plástico`, `Disin Tarima` / granel), mapeado automáticamente a los tipos oficiales del servidor.
4. **Lote Activo y Botón `+ Lote`:** Selector de lotes con fecha de caducidad visible. Al pulsar **`+ Lote`**, se abre el modal rápido para dar de alta un nuevo lote con su **Número de Lote**, **Fecha de Caducidad** y **Fecha de Elaboración**, el cual se registra en el servidor y queda seleccionado de inmediato como lote activo.

#### 8.3.3 Cámara Dual (BarcodeDetector / ZXing) y Orientación 90°
El visor táctico de Recepción integra doble motor óptico y control de orientación:
- **Motor Primario:** `BarcodeDetector` nativo acelerado por hardware (escaneo cada `280 ms`, soporta `Code-128`, `Code-39`, `EAN-13`, `QR`, `Data Matrix`, `ITF`, `UPC-A`, `UPC-E`).
- **Motor de Respaldo Automático:** Si el dispositivo no cuenta con `BarcodeDetector`, activa automáticamente **ZXing** en modo de alta sensibilidad (`TRY_HARDER`).
- **Botón Láser Horizontal / Láser Vertical (90°):** Cuando una etiqueta GS1/SSCC viene pegada en orientación vertical sobre el emplaye, pulsar **Láser Vertical** rota internamente la imagen de captura **90 grados** y alinea la retícula vertical para decodificar códigos 1D verticales sin tener que girar la tableta.
- **Anti-Rebote de 3 segundos + Validación de Duplicidad:** Evita lecturas repetidas accidentales y bloquea cualquier SSCC que ya haya sido registrado en la misma recepción (buzzer de error + vibración larga + alerta *"⚠ Tarima duplicada"*).
- **Pistola Láser / Captura Manual:** Campo dedicado debajo del visor siempre listo para recibir lecturas de escáner físico o captura manual con `Enter`.

#### 8.3.4 Edición de Tarima / Pico, Eliminación y Cierre `DISCHARGED`
- **Barra de Progreso y Métricas en Vivo:** Muestra tarimas descargadas vs. esperadas (`%`), piezas totales acumuladas, lote en proceso y rampa activa.
- **Edición Táctil de Tarima ya Escaneada:** Al tocar cualquier tarima del listado (o su botón de lápiz **Editar**), se abre el modal **Modificar Tarima #N** que permite ajustar:
  - Cantidad exacta de piezas (`-10`, `-1`, `+1`, `+10` o presets `135`, `300`, `48 Pico`).
  - Lote asignado y Tipo de tarima.
  - Casilla **Marcar como Tarima Pico / Incompleta** (coloca la etiqueta visible `PICO` en la tarjeta).
- **Eliminación de Tarima:** El botón de papelera elimina una lectura errónea tanto del buffer local como de la base de datos.
- **Conclusión Física de Descarga (F01):** El botón fijo inferior **CONCLUIR DESCARGA FÍSICA (F01)** (habilitado únicamente con `≥ 1` tarima escaneada) abre un modal de confirmación con el resumen de tarimas, piezas totales, rampa y staging destino. Al confirmar **Sí, Concluir Descarga**, sincroniza cualquier tarima pendiente, cambia la recepción a estatus **`DISCHARGED`**, notifica a Mesa de Control/Oficina y despliega la pantalla de confirmación final.

#### 8.3.5 SOP — Descarga Física en Andén
1. Abrir **Descargar Camión (`/receiving`)** y verificar que la unidad y rampa en pantalla coincidan con el tráiler acoplado (o usar **Cambiar Unidad**).
2. Ajustar en la **Barra de Parámetros Activos** el **SKU**, las **Piezas por Tarima**, el **Tipo de Tarima** y el **Lote Activo** (usar **`+ Lote`** si aparece un lote adicional).
3. Encender la **Cámara HD** (o usar la pistola láser). Si la etiqueta está vertical, activar **Láser Vertical (90°)**.
4. Escanear el código SSCC/UA de cada tarima descargada. Verificar el bip de confirmación, la aparición en el listado y el avance de la barra porcentual.
5. Si una tarima llega incompleta (pico) o requiere cambio de lote, tocar su tarjeta en la lista, editar piezas/lote, marcar la casilla **Tarima Pico** si aplica y guardar.
6. Al vaciar el tráiler, pulsar **CONCLUIR DESCARGA FÍSICA (F01)** en la barra inferior y confirmar para liberar la remisión a Mesa de Control.

---

### 8.4 Putaway — Ubicación y Acomodo en Rack (`/putaway`)

El módulo de **Acomodo en Racks y Cambios de Almacén** guía al montacarguista para mover tarimas desde el andén/staging hacia su posición exacta en rack, garantizando cero errores de ubicación mediante un flujo estricto de 2 pasos sincronizado cada **5 segundos** con las órdenes de traspaso de la base de datos.

#### 8.4.1 Flujo Guiado de 2 Pasos y Candados Anti-Error

| Paso | Estado del Flujo | Acción Requerida del Operador | Validación y Candado Anti-Error |
| :--- | :--- | :--- | :--- |
| **Paso 1** | **`SCAN_PALLET`** (*Recoger UA en Origen*) | Escanear (por cámara HD con anti-rebote 3 s, orientación 90° o pistola láser) el código SSCC/UA de una tarima pendiente en la orden activa. | • **Candado de Duplicidad:** Rechaza con buzzer de error si la UA ya fue ubicada en el lote (*"Tarima Duplicada"*).<br>• **Candado de Pertenencia:** Rechaza si la UA no pertenece al folio de acomodo activo.<br>• Al validar, marca la orden en progreso en el servidor y pone la tarima en estado **En Tránsito**. |
| **Paso 2** | **`SCAN_RACK`** (*Confirmar Rack Destino*) | Trasladar la tarima al rack indicado en pantalla y escanear el código de barras de la ubicación destino (o pulsar el botón de confirmación cuando aplique). | • **Candado Anti-Error de Rack:** Compara el código escaneado contra el rack asignado por el sistema. Si no coincide, bloquea la operación con alerta roja y buzzer de error: *"❌ UBICACIÓN INCORRECTA: Escaneaste 'X'. La orden exige colocar la tarima en: Y"*.<br>• **Cancelación de Tránsito:** Si el pasillo está obstruido o el operador tomó otra tarima, el botón **Cancelar** (ícono deshacer) devuelve la tarima a estado pendiente y regresa a `SCAN_PALLET` con sonido de advertencia. |
| **Cierre** | **`BATCH_COMPLETED`** (*Acomodo Completado*) | Al depositar la última tarima pendiente de la orden. | Completa automáticamente el traspaso en la base de datos y actualiza el Kardex de inventario, emitiendo confirmación visual (*"¡Acomodo Completado en PostgreSQL y Kardex!"*) y habilitando el botón **ACOMODO COMPLETADO • BUSCAR SIGUIENTE**. |

#### 8.4.2 SOP — Acomodo Guiado en Rack
1. Entrar a **Acomodar en Racks (`/putaway`)** y confirmar el folio, origen y rack destino asignado (si existen varias órdenes, pulsar **Cambiar (N)**).
2. En **Paso 1 (`SCAN_PALLET`)**, escanear el SSCC de la tarima en el andén/staging. Verificar que aparezca el banner **Tarima en Tránsito** con el código gigante del rack destino.
3. Conducir hasta el rack asignado. Si por fuerza mayor no es posible depositarla, pulsar **Cancelar** para liberar el tránsito.
4. En **Paso 2 (`SCAN_RACK`)**, escanear la etiqueta del rack destino. Verificar el bip de éxito y el paso de la tarima a la lista de ubicadas con hora registrada.
5. Repetir hasta completar todas las tarimas de la orden; al ubicar la última, el sistema cierra el lote automáticamente y actualiza el Kardex.

---

### 8.5 Picking y Carga de Camión (`/picking`)

El módulo de **Carga de Camión (Salidas / Embarque F03)** gobierna la estiba física de embarques a clientes bajo secuencia **FEFO estricta** (*First Expired, First Out* — lo primero que caduca es lo primero que sale), sincronizado cada **5 segundos** con las órdenes de salida.

#### 8.5.1 Reglas de Despacho: FEFO Estricto vs. Acuerdo Comercial (`MANUAL_OVERRIDE`)

Al cargar una orden de salida, el sistema ordena automáticamente todas las tarimas del manifiesto de la fecha de caducidad más próxima a la más lejana y resalta en la tarjeta superior la **SIGUIENTE TARIMA REQUERIDA (FEFO)** (ubicación origen, SSCC esperado, SKU, lote, caducidad y cantidad).

| Modo de Despacho | Regla de Validación al Escanear | Comportamiento ante Desviación |
| :--- | :--- | :--- |
| **FEFO Estricto (`FEFO_AUTO`)** *(Modo por defecto)* | El operador **debe** escanear exactamente el SSCC de la tarima en turno (la más próxima a caducar). | **ALERTA ROJA DE VIOLACIÓN FEFO:** Si escanea cualquier otra tarima, la pantalla completa se tiñe de rojo durante **3 segundos**, suena el **buzzer de emergencia**, muestra el SSCC escaneado frente al SSCC requerido por FEFO y bloquea nuevos escaneos hasta que transcurre la cuenta regresiva (*"Desbloqueando en 3 segundos…"*). |
| **Acuerdo Comercial (`MANUAL_OVERRIDE`)** *(Excepción autorizada)* | Al pulsar el botón **FEFO Estricto / Acuerdo Comercial** en la ficha del embarque, permite escanear **cualquier tarima pendiente perteneciente al manifiesto** sin seguir el orden estricto de caducidad, dejando rastro de auditoría con folio de convenio (`AC-COMERCIAL`). | Si el SSCC escaneado no pertenece a ninguna línea pendiente del embarque, dispara igualmente la **Alerta Roja + Buzzer de 3 segundos**. |

#### 8.5.2 Pre-Revisión Física de Estiba y Cierre `LOADED` (Formato F03)
- **Visor de Cámara HD + Pistola Láser:** Incluye motor dual (`BarcodeDetector` + `ZXing`), rotación `90°` para etiquetas verticales y anti-rebote de 3 segundos.
- **Confirmación de Avance:** Cada escaneo válido genera un destello verde de `800 ms`, sonido de éxito y avanza automáticamente a la siguiente línea FEFO pendiente. Al primer escaneo, cambia el estatus del embarque en el servidor a `IN_PROGRESS`.
- **Checklist de Staging / Estiba Física:** Cuando el manifiesto llega al **100%** de tarimas verificadas, aparece el recuadro de confirmación con el botón **Confirmar Pre-Revisión Física en Rampa N** (casilla verificable de estiba física).
- **Cierre Oficial (`LOADED`):** El botón fijo inferior **CONCLUIR CARGA DE CAMIÓN (F03)** se habilita al completar el 100% de las tarimas. Al pulsarlo, reporta el embarque en estatus **`LOADED`** a Mesa de Control con el nombre del montacarguista, total de tarimas y unidades cargadas, emite la campana de confirmación y prepara el embarque para despacho F03.

#### 8.5.3 SOP — Carga de Camión con Validación FEFO
1. Abrir **Cargar Camión (`/picking`)** y confirmar la orden de salida, cliente, placas y rampa de carga.
2. Verificar la tarjeta **SIGUIENTE TARIMA REQUERIDA (FEFO)** (ubicación, SSCC, lote y caducidad). *(Solo si existe autorización formal de cliente, cambiar el botón superior a **Acuerdo Comercial**).*
3. Recoger la tarima y escanear su etiqueta SSCC con la cámara HD o pistola láser antes de subirla a la caja del tráiler.
4. Si salta la **Pantalla Roja de Violación FEFO**, detener la maniobra, esperar 3 segundos al desbloqueo y localizar la tarima exacta indicada como **Tarima Requerida (FEFO)**.
5. Al llegar al 100% de las tarimas, marcar la casilla **Confirmar Pre-Revisión Física en Rampa N** y pulsar **CONCLUIR CARGA DE CAMIÓN (F03)**.

---

### 8.6 Conteo Físico Ciego (`/counting`)

El módulo de **Conteo Cíclico Ciego** ejecuta auditorías de inventario bajo el **Principio Inmutable de Conteo Ciego Puro**: durante la captura física, el operador o auditor **NUNCA ve las existencias teóricas del WMS**, eliminando cualquier sesgo de confirmación y alimentando con exactitud el indicador **IRA %** (*Inventory Record Accuracy*).

#### 8.6.1 Candados de Seguridad e Integridad de Auditoría

| Candado / Regla | Funcionamiento Técnico en Piso | Mensaje / Acción si se Activa |
| :--- | :--- | :--- |
| **1. Conteo Ciego Puro** | Oculta por completo las piezas y tarimas teóricas mientras el rack está en captura. El comparativo contra teórico solo se revela al concluir la bahía. | El operador únicamente ve cuántas tarimas y piezas físicas ha capturado. |
| **2. Candado de Presencia Física en Rack** | Cada ubicación de la ruta inicia **BLOQUEADA** (`🔒 PASO 1: CONFIRMAR RACK`). Impide escanear tarimas hasta que el operador se posiciona físicamente frente al rack y escanea la etiqueta de la bahía (ej. `LOC-P04-R02-B`). | Si escanea un código distinto al rack asignado, emite buzzer de error: *"Ubicación Incorrecta — Candado Activo"*. Al validar, cambia a `✓ PRESENCIA CONFIRMADA`. |
| **3. Protocolo Zone Lease (30 min)** | Al entrar al conteo, reserva exclusivamente el pasillo asignado durante **30 minutos** (temporizador regresivo visible en cabecera `MM:SS`) para evitar colisiones con otros montacargas u operadores. | Si el arrendamiento expira (`00:00`), **bloquea todo escaneo** hasta renovar el Zone Lease o recuperar cobertura de red. |
| **4. Cola Local IndexedDB FIFO (Límite 500 ops)** | Cada tarima capturada se encola en IndexedDB en estricto orden FIFO para soportar zonas ciegas sin Wi-Fi. | Al llegar a **500/500 operaciones**, bloquea nuevas capturas (*"Tope de 500 Operaciones Offline Alcanzado"*) hasta acercarse a cobertura Wi-Fi y vaciar la cola. |
| **5. Detección de Tarima Duplicada** | Impide capturar dos veces el mismo SSCC dentro de la misma bahía. | Alerta ámbar + tres bips: *"Tarima Ya Registrada en este Rack"*. |

#### 8.6.2 Presets de Piezas, Navegación y Matriz de Discrepancias (3 Acciones de Supervisor)
- **Anillo de Progreso Circular (Header):** Muestra visualmente las ubicaciones auditadas vs. totales (`X/Y`) y el porcentaje de avance del pasillo.
- **Presets Rápidos de Piezas:** En Paso 2 (rack desbloqueado), botones táctiles para fijar rápidamente la cantidad física: **`12 Caja`**, **`48 Pallet (Std)`** y **`60 Pzas`** (además de ajustes manuales y visor de cámara HUD bajo demanda).
- **Pestañas Segmentadas:** **Ruta en Pasillo** (lista de ubicaciones con estado `AUDITADO`, `EN CONTEO`, `RECONTEO` o `PENDIENTE`) y **Tarimas en Bahía** (buffer de tarimas capturadas con opción de eliminar una captura errónea antes de cerrar).
- **Modal de Conciliación y Resolución de Discrepancias:** Al pulsar **CONCLUIR CONTEO Y REVISAR CONCILIACIÓN**, el sistema calcula y muestra: **Físico Contado**, **Teórico WMS**, **Delta Físico (`±N pzas`)** e **IRA Resultante (`%`)**.
  - Si es **Conteo Exacto (`Delta = 0`, `IRA = 100%`)**: Queda aprobado automáticamente y listo para avanzar a la siguiente bahía.
  - Si existe **Discrepancia**: Despliega la botonera de **Resolución de Supervisor de Turno** con **3 acciones**:
    1. **Aprobar Ajuste:** Acepta el conteo físico, ajusta el inventario en el WMS y marca la ubicación como `AUDITADO`.
    2. **Solicitar Reconteo:** Deja la ubicación en estado **`RECONTEO`** (`DISCREPANCY_PENDING`) para una segunda verificación en cuarentena.
    3. **Rechazar:** Rechaza el conteo registrado y cierra el dictamen como rechazado.

#### 8.6.3 SOP — Ejecución de Conteo Físico Ciego
1. Ingresar a **Conteo Cíclico (`/counting`)** y verificar que el temporizador **Zone Lease (30:00)** esté activo.
2. En **Paso 1 (`🔒 CONFIRMAR RACK`)**, trasladarse físicamente a la ubicación mostrada en gigante y escanear su etiqueta de rack (con pistola láser o abriendo el **📷 Visor HUD**).
3. En **Paso 2 (`🎯 CONTEO FÍSICO EN BAHÍA`)**, seleccionar el preset de piezas correspondiente (`12`, `48` o `60`) o ingresar la cantidad física exacta, y escanear cada tarima SSCC presente en la bahía.
4. Revisar las tarimas capturadas en la pestaña **Tarimas en Bahía** (eliminar cualquier lectura errónea con el ícono de papelera).
5. Pulsar **CONCLUIR CONTEO Y REVISAR CONCILIACIÓN**. Si hay discrepancia, el supervisor aplica una de las 3 acciones (**Aprobar Ajuste**, **Solicitar Reconteo** o **Rechazar**).
6. Pulsar **Avanzar a Siguiente Bahía** (el candado de presencia física se vuelve a activar automáticamente para el siguiente rack).

---

### 8.7 Inspección de Calidad QM en Piso (`/quality`)

El **Centro Operativo de Calidad QM** está alineado a **ISO 9001** y al **Formato Oficial F01-PO-GC-8.6-03 Rev. 03**. Ofrece una cabecera con 4 tarjetas KPI en vivo (*Bloqueos PNC*, *Liberaciones*, *Verif. F01 Andén*, *Reclamos F01*) y **5 submódulos táctiles** accesibles mediante pestañas:

#### 8.7.1 Submódulo 1: Bloqueos / Producto No Conforme (`1. Bloqueos / PNC`)
Permite retener tarimas o lotes dañados o sospechosos en tiempo real desde cualquier punto del almacén:
- **Filtros:** Buscador por Folio, SSCC, Lote o SKU + filtro por etapa de detección (`Descarga Inbound`, `Almacenamiento en Racks`, `Carga Outbound`, `Material de Prueba`).
- **Botón `+ Bloquear Tarima`:** Registra un nuevo bloqueo capturando SSCC, SKU, descripción, lote, cantidad afectada, ubicación Hold (ej. `LOC-QM-HOLD-01`), etapa de detección, **severidad** (`🛑 Crítica`, `⚠️ Advertencia`, `ℹ️ Informativa`), observaciones y selección múltiple de **7 criterios de no conformidad**:
  1. *Material con humedad*
  2. *Embalaje en malas condiciones*
  3. *Caducidad vencida o menor a 90 días*
  4. *Discrepancia en certificado COA*
  5. *Tarima rota o desnivelada*
  6. *Fuga o derrame visible*
  7. *Código GS1 no legible*
- **Acciones Directas por Tarjeta:** **Liberar** (salta al submódulo 2), **Inspección** (salta al submódulo 5) y **Marbete** (envía el **Marbete Hold QR** directamente a la impresora Zebra).

#### 8.7.2 Submódulo 2: Liberaciones y Destinos (`2. Liberaciones`)
Permite emitir el dictamen formal de salida de cuarentena sobre cualquier bloqueo activo (`BLOCKED` o `UNDER_INSPECTION`), registrando:
- **Autorizador:** `Calidad 4GUARD` o `Cliente`.
- **Soporte Documental:** `Acta o dictamen formal`, `Correo electrónico`, `Medios electrónicos` u `Otro`, con asunto/folio de respaldo, nombre y puesto del autorizador y notas técnicas.
- **3 Destinos Oficiales de Liberación:**
  1. **Liberado para Distribución:** Retorna el lote a stock disponible para picking y surtido.
  2. **Liberado para Destrucción:** Canaliza producto sin rescate a zona de desecho/merma con folio de baja.
  3. **Liberación para Devolución:** Prepara documentación de retorno a planta o proveedor (RTV).

#### 8.7.3 Submódulo 3: Verificación de Carga en Andén F01 (`3. Verif. F01 Andén`)
Digitaliza en piso el **Formato Oficial F01-PO-GC-8.6-03 Rev. 03** con **cálculo automático de dictamen en vivo**:
- **Datos del encabezado:** No. Remisión/Folio, Andén/Rampa, Producto Transportado, Cliente/Proveedor y Observaciones Generales.
- **Checklist Táctil (`SI` / `NO` / `N/A`) dividido en 2 secciones:**
  - **1. Criterios de Transporte & Caja (5 puntos):**
    - *Caja limpia, seca y libre de olores extraños* (**Crítico**)
    - *Ausencia total de plagas, insectos o roedores* (**Crítico**)
    - *Temperatura de termoking dentro de rango (2°C a 6°C)* (**Crítico**)
    - *Sellos fiscales y marchamos coinciden con remisión* (**Crítico**)
    - *Paredes y piso sin clavos salientes o astillas* (No crítico)
  - **2. Criterios de Producto & Embalaje (4 puntos):**
    - *Tarimas estandarizadas y libres de humedad* (No crítico)
    - *Empaque primario y secundario sellado sin fugas* (**Crítico**)
    - *Código GS1 / EAN legible por escáner láser* (No crítico)
    - *Fecha de caducidad con vida útil mayor a 90 días* (**Crítico**)
- **Regla de Dictamen Automático:**
  - Si **cualquier criterio marcado como Crítico** recibe `NO` ➔ **`🛑 TRANSPORTE RECHAZADO`**.
  - Si no hay fallas críticas pero **algún criterio no crítico** recibe `NO` ➔ **`⚠️ ACONDICIONAMIENTO REQUERIDO`** (`ACONDICIONAMIENTO_PENDIENTE`).
  - Si todos los criterios están en `SI` o `N/A` ➔ **`✅ TRANSPORTE APROBADO`**.

#### 8.7.4 Submódulo 4: Reclamos e Incidencias F01 (`4. Reclamos (F01)`)
Registra reclamaciones formales con impacto físico y financiero:
- Captura etapa (`Descarga`, `Almacenamiento`, `Carga`), Remisión, SKU, Producto, Cliente, Lote, **Tipo de Defecto** (*No cumple con especificación*, *Discrepancia en cantidad*, *Malas condiciones*, *Otro*), **Cantidad Dañada**, **Cantidad Perdida**, **Costo Asociado**, autorizador y observaciones.

#### 8.7.5 Submódulo 5: Inspección QM y Acta Oficial con Cámara (`5. Inspección QM`)
Consola de auditoría detallada para tarimas en cuarentena (siniestros de piso y muestreos inbound):
- **Checklist Técnico Obligatorio de 4 Puntos:**
  1. *Integridad de Empaque y Tarima*
  2. *Sellos y Hermeticidad*
  3. *Coincidencia Lote & Caducidad*
  4. *Legibilidad Código GS1 / SSCC*
- **Cámara de Evidencia Fotográfica:** Permite abrir la cámara trasera o frontal, encuadrar con guías y capturar la fotografía del empaque o daño para anexarla al expediente.
- **Matriz de 4 Disposiciones Finales:**
  1. **✅ LIBERAR AL ALMACÉN** — **Candado estricto:** Este botón permanece **bloqueado** (`🔒 Requiere checklist 4/4 conforme`) hasta que los 4 puntos del checklist estén en estado `CONFORME`. Al firmarse, genera automáticamente una misión de Putaway en el Cockpit del montacarguista.
  2. **🔄 REEMPAQUE / RETRABAJO** — Envía la tarima a Acondicionamiento para cambio de tarima, re-emplaye y nuevo SSCC.
  3. **🔥 MERMA / DESTRUCCIÓN (80)** — Da de baja definitiva el inventario en **Estado 80** y lo canaliza a confinamiento.
  4. **🚚 DEVOLUCIÓN PROVEEDOR (RTV)** — Rechaza el lote y genera acta para devolución y nota de crédito.
- **Acta Oficial QM / Certificado de Disposición:** Tras confirmar el dictamen, el sistema emite el certificado oficial con folio único, tabla completa de trazabilidad, sello hash digital (`4G-SHA256-...-VERIFIED`), firma del auditor y botones para **Copiar Folio**, **Imprimir Acta** o **Ir al Cockpit (Ver Putaway)** cuando la tarima fue liberada.

#### 8.7.6 SOP — Auditoría Técnica y Emisión de Acta QM en Piso
1. Acceder a **Calidad & Cuarentena QM (`/quality`)** y seleccionar el submódulo requerido (Bloqueos, Liberaciones, Verif. F01, Reclamos o Inspección QM).
2. Para auditar una tarima retenida, abrir **5. Inspección QM** y seleccionar el folio desde la **Cola de Cuarentena**.
3. Evaluar y marcar uno a uno los **4 puntos del Checklist Técnico de Inocuidad & Empaque**.
4. Pulsar **Tomar Foto con Cámara**, encuadrar la evidencia física y capturar; redactar la justificación técnica si es necesario.
5. Elegir la disposición correspondiente en la **Matriz de 4 Disposiciones** y confirmar en el modal **Firmar y Emitir Dictamen**.
6. Revisar el **Acta Oficial QM** generada, imprimir o copiar el folio y avanzar a la siguiente tarima.

---

### 8.8 Seguridad y Caseta en Terminal RF (`/security`)

El **Centro de Control de Vigilancia & Caseta** digitaliza en tableta el control perimetral, el **Formato Oficial F01-PO-CP-7.1.3-03 (Rev. 01)**, la bandeja de auto-registro QR de choferes, el monitoreo de patio con semáforo SLA y el Check-Out con cotejo de sellos. Sincroniza automáticamente con el servidor y la Consola Administrativa cada **4 segundos**.

Las **Métricas Operativas del Turno** (En Patio Total, Solicitudes en Espera, En Andén/Rampa, Listos para Salida) cuentan con un botón **Compactar Vista / Expandir Métricas** para maximizar el espacio de trabajo en pantallas pequeñas.

#### 8.8.1 Pestaña 1: Nuevo Registro F01 (1-Tap QR + Captura Bimodal)
- **Bandeja de Solicitudes Entrantes de Choferes (1-Tap Load):** Muestra en vivo las tarjetas de choferes que enviaron su auto-registro móvil desde el portal QR de caseta (`/carrier-checkin`).
  - Al tocar una tarjeta, **en 1 toque** se precargan todos los datos en el Formato Oficial F01 (operación, línea transportista, chofer, teléfono, placas de tractor y caja, número económico, medidas, cliente, remisión/carta porte, sellos y EPP) y aparece el banner **SOLICITUD MÓVIL VINCULADA**.
  - También permite **Desvincular** para captura manual o **Rechazar** una solicitud errónea con modal de confirmación.
- **Reglas Bimodales del Formulario F01-PO-CP-7.1.3-03:**

| Regla / Campo | Operación `DESCARGA` (Entrada / Recepción) | Operación `CARGA` (Salida / Embarque) |
| :--- | :--- | :--- |
| **Documento Obligatorio** | **No. Remisión / Factura** (obligatorio). Carta Porte se marca automáticamente `NO` (*N/A en Descarga*). | **No. Carta Porte** (obligatorio). Remisión se marca automáticamente `NO` (*N/A en Carga*). |
| **Sellos / Cinchos de Seguridad al Ingreso** | **Obligatorio capturar al menos 1 sello físico** de llegada (se agregan como chips). | **Bloqueado al ingreso:** La unidad entra vacía; el sistema asigna automáticamente `PENDIENTE_ANDEN` porque los sellos se colocan y registran al concluir la carga en andén. |
| **Asignación de Rampa / Andén** | Bloqueada en caseta (*"Pendiente de asignación por Almacén"*): la rampa la asigna exclusivamente Almacén / Mesa de Control. | Bloqueada en caseta (*"Pendiente de asignación por Almacén"*). |

- **Checklist Oficial de 11 Criterios (`SÍ` / `NO` + Observaciones manuales):**
  - **EPP (4 puntos):** Zapatos de seguridad, Cofia, Cubrebocas, Chaleco con reflejante.
  - **Documentos (2 puntos):** Carta porte, Remisión.
  - **Revisión Física de la Unidad (5 puntos):** Interior de Caja, Daños a la caja, Daños en puertas, Olores extraños, Indicios de plagas.
- **Responsables y Firmas:** Auto-asigna el nombre del guardia autenticado en la sesión y solicita el nombre/conformidad del transportista antes de pulsar **AUTORIZAR ENTRADA Y ABRIR PLUMA**.

#### 8.8.2 Pestaña 2: En Patio y Andenes (`IN_YARD`) — Semáforo SLA
Muestra todas las unidades dentro de las instalaciones con filtros (`Todos`, `En Espera`, `En Rampa`, `Listos Salida`), buscador por placa/chofer/folio/sello y alternador de vista **Tarjetas / Compacto**.

##### Semáforo SLA de Permanencia en Patio
Cada unidad calcula en tiempo real su tiempo de estancia desde la hora de entrada y colorea el indicador automáticamente:

| Tiempo de Estancia en Patio | Color del Semáforo SLA | Interpretación Operativa |
| :---: | :---: | :--- |
| **Menor a 45 minutos** (`< 45 min`) | **Verde** (`Normal`) | Estancia óptima dentro de estándar operativo. |
| **Entre 45 y 90 minutos** (`45–90 min`) | **Ámbar** (`Alerta`) | Permanencia prolongada; vigilar avance en andén. |
| **Mayor a 90 minutos** (`> 90 min`) | **Rojo** (`Crítico`) | Demora crítica de SLA; escalar con Mesa de Control / Supervisión. |

> [!CAUTION]
> **Candado de Salida Vinculado a Almacén:**
> Un guardia **NO puede dar Check-Out** a una unidad mientras se encuentre en estado `En Espera de Rampa` o `En Andén / Maniobra`. El botón aparece bloqueado (`🔒 En Descarga` / `🔒 En Carga`). La salida solo se habilita cuando Almacén concluye y autoriza la maniobra (`Descarga / Carga Finalizada` o `Salida Autorizada`).

#### 8.8.3 Pestaña 3: Salida y Cotejo de Sellos (`CHECKOUT`)
Lista exclusivamente las unidades autorizadas por almacén para egresar. Al pulsar **Cotejar y Salir**:
1. Se abre el modal **COTEJO DE SALIDA** con resumen de folio, operador, transportista, placas y sellos de entrada.
2. **Validación de Sello de Salida:**
   - En operaciones de **CARGA**, es **obligatorio** capturar el número de **Sello Fiscal Oficial** colocado en el andén de salida (no acepta campo vacío ni `PENDIENTE_ANDEN`).
   - En operaciones de **DESCARGA**, verifica caja vacía o sello de salida y permite agregar observaciones de salida y notas del guardia.
3. Al confirmar, estampa la hora exacta de salida, libera la rampa ocupada, guarda en base de datos y **abre automáticamente la Vista Previa Oficial del Formato F01-PO-CP-7.1.3-03** con botones para **Imprimir Boleta** o **Descargar PDF Oficial** (`F01_<Folio>_<Placas>.pdf`).

#### 8.8.4 Pestaña 4: Historial y Auditoría (`HISTORY`)
Bitácora oficial de unidades egresadas durante el turno con:
- **4 Tarjetas KPI de Auditoría:** *Total Egresos*, *Descargas (Inbound)*, *Cargas (Outbound)* y *Estancia Promedio* (con indicador de meta `SLA ≤ 90m`).
- **Filtros segmentados** (`Todas`, `Descargas`, `Cargas`) y buscador en vivo.
- **Línea de tiempo por unidad** (`Entrada ➔ Salida` con chip de duración coloreado por semáforo SLA) y botón **Reimprimir Formato F01** en cada tarjeta.

#### 8.8.5 SOP — Ciclo Completo de Caseta en Terminal RF
1. En **1. Nuevo Registro F01**, tocar la solicitud QR del chofer en la bandeja superior (o capturar manualmente si no trae pase).
2. Seleccionar **Descarga** o **Carga**, verificar documento obligatorio (Remisión o Carta Porte) y capturar sellos físicos si es Descarga.
3. Inspeccionar físicamente al chofer y la caja marcando los **11 criterios del Checklist F01** (`SÍ`/`NO`) y pulsar **AUTORIZAR ENTRADA Y ABRIR PLUMA**.
4. Monitorear la unidad en **2. En Patio & Andenes**, vigilando el semáforo SLA (`<45` / `45-90` / `>90 min`) y la asignación de rampa por almacén.
5. Cuando el vehículo aparezca en **3. Salida / Check-Out** como **Listo para Salida**, pulsar **Cotejar y Salir**, capturar/cotejar el sello de salida y confirmar para generar e imprimir o descargar el **Formato F01 oficial**.

---

### 8.9 Sincronización Offline (`/sync`) y Reporte de Anomalías (`/anomaly`)

#### 8.9.1 Monitor de Sincronización Offline y Zone Lease (`/sync`)

El **Monitor de Sincronización** se adapta automáticamente al rol activo (*Caseta de Seguridad*, *Control de Calidad QM*, *Montacarguista / Terminal RF* o *Mesa de Control*) y permite gestionar las transacciones locales almacenadas en **IndexedDB** cuando la terminal trabaja en zonas ciegas sin Wi-Fi.

##### Panel de Control y Capacidades Clave
- **Telemetría de Red en Cabecera:** Muestra `EN LÍNEA (XX ms)` con latencia de ping en milisegundos o `SIN CONEXIÓN`, junto al chip de área asignada (*Pasillo 04*, *Caseta Norte*, *Lab QM 01*, *Mesa Control*).
- **4 Indicadores KPI:**
  1. **Transacciones Pendientes:** Contador `N / 500 máx` con barra de ocupación (`>50%` ámbar, `>85%` rojo).
  2. **Concesión de Área / Zone Lease (30 min):** Muestra estado (`ACTIVE`, `WARNING`, `EXPIRED`), tiempo restante `MM:SS` y el botón **Renovar** (o **Renovar Turno**) que extiende la concesión otros 30 minutos.
  3. **Sincronizadas Hoy:** Total de transacciones confirmadas por el servidor central.
  4. **Motor Local IndexedDB:** Muestra `OPERATIVO` (en línea) o `OFFLINE-SAFE` (protegiendo capturas localmente).
- **Vaciado Manual FIFO (`SINCRONIZAR AHORA`):** Botón táctil principal (habilitado cuando hay conexión y `≥ 1` operación pendiente) que transmite secuencialmente todas las operaciones de la cola en estricto orden **FIFO** (`#1, #2, #3...`), mostrando barra de progreso en porcentaje y moviendo cada registro confirmado al **Historial Reciente (Transmitido OK)**.
- **Descarte de Operación en Conflicto / Error:** Cada tarjeta de la cola muestra su índice FIFO, tipo de operación, folio de referencia, tiempo transcurrido y contador de reintentos fallidos (`↻ N reintento`). Si una transacción quedó bloqueada por un conflicto irrecuperable, el operador o supervisor puede pulsar el botón de papelera **Descartar operación** para retirarla de la cola local y destrabar el vaciado FIFO.

##### SOP — Recuperación y Sincronización Tras Zona Ciega
1. Al recuperar cobertura Wi-Fi (o si la cola se acerca al límite de **500 operaciones**), tocar la insignia ámbar `N pend.` del encabezado o el botón **Sincronizar** del Menú Principal.
2. Verificar que la insignia superior indique **EN LÍNEA** y revisar la lista secuencial de **Operaciones en Cola**.
3. Si el Zone Lease está en `WARNING` o `EXPIRED`, pulsar **Renovar** para restablecer 30 minutos de operación en el pasillo.
4. Pulsar **SINCRONIZAR AHORA** y esperar a que la barra llegue al `100%` y aparezca la confirmación *"Todo sincronizado • Base de datos al día"*.
5. Si alguna operación permanece atascada con reintentos fallidos tras validar con Mesa de Control, pulsar el ícono de papelera para **Descartar operación** y volver a sincronizar.

---

#### 8.9.2 Reporte de Siniestros y Anomalías (`/anomaly`) — Regla de Pablo (Estado 80)

El módulo de **Reporte de Siniestros y Disposición Final** permite documentar en piso cualquier pallet colapsado, mercancía perforada por uñas de montacargas, derrame o aplastamiento, aplicando dos reglas de negocio inmutables:

1. **Regla de Pablo (Transición Automática a Estado 80 — Dado de Baja):** Toda tarima siniestrada transiciona de forma inmediata e irreversible a **`ESTADO 80: DADO DE BAJA`**, sacándola del inventario disponible para surtido.
2. **Regla de Inmutabilidad de Ubicación:** El sistema **conserva la posición física original del rack** donde ocurrió el siniestro (mostrada en el banner de candado *"Regla de Inmutabilidad de Ubicación"*) para garantizar trazabilidad histórica y conciliación con Calidad.

##### Compresión Fotográfica Automática (~150 KB) y Validaciones
- **Evidencia Fotográfica Obligatoria:** El formulario **bloquea el envío** si no se ha adjuntado una fotografía del daño (*"⚠️ Es obligatorio adjuntar la fotografía de evidencia del siniestro"*).
- **Compresión en Dispositivo:** Al capturar la foto con la cámara trasera (`Abrir Cámara / Adjuntar Foto`), la terminal la redimensiona y comprime localmente a un máximo de `1024 px` de ancho en formato JPEG (~**150 KB**), mostrando en pantalla la vista previa y el peso exacto en KB para garantizar transmisión rápida incluso con baja señal o en cola offline.
- **Catálogo de 6 Causas de Siniestro:**
  - *Pallet colapsado / Tarima caída (HU-164)*
  - *Mercancía perforada por uñas de montacargas*
  - *Embalaje roto / Derrame de líquido*
  - *Desplome por estiba vencida*
  - *Caja aplastada en maniobra*
  - *Otro siniestro físico*
- **Botones de Acción Dual y Ticket `SIN-XXXXXX`:**
  - **🚨 Registrar Siniestro (Estado 80):** Da de baja la tarima y la deja con dictamen **`⚖️ Pendiente de Conciliación de Calidad`**.
  - **⚡ Liberado para Destrucción (Destrucción Rápida):** Da de baja la tarima en Estado 80 y autoriza la destrucción física inmediata y retiro del pasillo sin esperar checklist de calidad (`⚡ Liberado para Destrucción Inmediata`).
  - En ambos casos, genera un **Folio de Incidente único con formato `SIN-XXXXXX`** (6 dígitos), guarda el registro local de auditoría y presenta la pantalla de confirmación con botones para **Reportar Otro Siniestro** o **Volver al Menú Principal**.

##### SOP — Levantamiento de Reporte de Siniestro en Piso
1. Desde el Menú Principal pulsar **Reportar Incidencia** y confirmar **ABRIR REPORTE CON FOTO** (o navegar a `/anomaly`).
2. **Paso 1 (SSCC):** Escanear el código SSCC de la tarima afectada (mínimo 6 caracteres). El sistema autocompleta SKU, descripción, piezas y **Ubicación Actual en Rack**.
3. **Paso 2 y 3 (Ubicación y Causa):** Confirmar la posición física del incidente y seleccionar la **Causa del Siniestro Físico** de la lista desplegable.
4. **Paso 4 (Observaciones):** Redactar el detalle de la maniobra (mínimo **10 caracteres** explicativos).
5. **Paso 5 (Foto Obligatoria):** Pulsar **Abrir Cámara / Adjuntar Foto**, fotografiar el daño y verificar la insignia *"Evidencia Comprimida Lista (`~150 KB`)"*.
6. **Paso 6 (Envío):** Pulsar **🚨 Registrar Siniestro (Estado 80)** para conciliación con Calidad, o **⚡ Liberado para Destrucción** si aplica retiro y destrucción inmediata. Anotar o comunicar el ticket **`SIN-XXXXXX`** generado.
