## Capítulo 1. Autenticación, Seguridad de Acceso, Navegación y Perfil

Este capítulo constituye la guía operativa oficial para el acceso seguro a **4GUARD WMS**, el dominio de la barra lateral de navegación, el aprovechamiento de las herramientas de la barra superior, el relevo ágil de operadores en estación compartida, el uso del asistente inteligente **ForBot AI** y la gestión de la cuenta personal del usuario.

---

### 1.1 Inicio de Sesión y Control de Bloqueos de Cuenta

#### Propósito operativo, ruta y roles con acceso
- **Propósito operativo:** Validar la identidad del colaborador antes de permitir el acceso a los módulos operativos y administrativos de 4GUARD WMS, aplicando protección contra intentos de intrusión por fuerza bruta y preservando el contexto de trabajo en caso de pausas o expiración de sesión.
- **Ruta de acceso:** `/login`
- **Roles con acceso:** Pantalla pública previa a la autenticación (todos los perfiles institucionales de escritorio).
  - *Nota sobre dispositivos móviles:* Si un usuario sin sesión activa intenta acceder al portal desde un teléfono inteligente o tableta (por ejemplo, un operador transportista en caseta), el sistema lo redirige automáticamente al portal público de **Auto-Registro de Transporte** (`/carrier-checkin`) para evitar que visualice la pantalla de acceso institucional.

![Pantalla de Inicio de Sesión](screenshots/01-login.png)

#### Diccionario de Elementos de Interfaz

| Elemento / Etiqueta | Tipo | Obligatorio | Formato / Reglas | Comportamiento exacto |
| :--- | :--- | :---: | :--- | :--- |
| **Panel Izquierdo de Marca** (*"Torre de Control para operar."*) | Panel informativo | No aplica | Logotipo institucional 4GUARD WMS y mensaje de bienvenida | Identifica el entorno oficial del sistema. En pantallas estrechas se adapta para priorizar el formulario. |
| **Banner de Sesión Pausada por Inactividad** | Alerta informativa (ámbar) | Condicional | Visible solo cuando la sesión previa se pausó o expiró | Muestra el texto *"Sesión Pausada por Inactividad"* e indica el nombre del proceso preservado (ej. *Recepción de Mercancía*). Al iniciar sesión, el usuario vuelve automáticamente a esa pantalla. |
| **Banner de Error de Acceso** | Alerta de error (rojo) | Condicional | Mensaje descriptivo + insignia `Intentos: N` | Aparece tras un intento fallido. Muestra cuántos intentos restantes quedan antes del bloqueo temporal (de un máximo de 3). |
| **USUARIO O CORREO ELECTRÓNICO** | Campo de texto | Sí | Mínimo 2 caracteres sin espacios (se bloquea la tecla espacio) | Acepta nombre de usuario o correo electrónico corporativo. Normaliza automáticamente espacios laterales y mayúsculas/minúsculas. Si la cuenta tiene intentos fallidos previos, actualiza la insignia de intentos restantes al escribir. |
| **CONTRASEÑA DE SEGURIDAD** | Campo de contraseña | Sí | Mínimo 6 caracteres (oculto por defecto con `••••••••`) | Admite la clave de acceso del usuario. Ante un fallo de autenticación, ejecuta una vibración visual breve para alertar del error. |
| **Ícono de Visibilidad (Ojo)** | Botón de alternancia | No | Ícono `visibility` / `visibility_off` | Alterna entre ocultar y mostrar los caracteres escritos en la contraseña sin perder el foco del formulario. |
| **¿Olvidó su clave?** | Enlace / botón | No | Texto interactivo sobre el campo de contraseña | Limpia cualquier mensaje de error visible y abre el **Modal de Recuperación de Acceso** con verificación por código de 6 dígitos (OTP). |
| **ACCEDER AL SISTEMA** | Botón primario | Sí | Habilitado solo cuando ambos campos son válidos | Envía las credenciales. Durante la verificación cambia a estado de carga con indicador giratorio y texto **"INICIANDO SESIÓN..."** evitando envíos duplicados. |
| **Pie de página** | Etiqueta informativa | No aplica | `© 2026 4GUARD WMS • SyborX • v1.0.1` | Muestra la versión activa de la consola de autenticación. |

#### Vistas de Bloqueo de Seguridad en `/login`

Cuando se alcanzan los umbrales de seguridad, la tarjeta de inicio de sesión reemplaza automáticamente el formulario por una de las dos vistas de protección:

| Vista de Bloqueo | Elementos visibles | Comportamiento exacto |
| :--- | :--- | :--- |
| **Acceso Pausado (Bloqueo Temporal — 15 minutos)** | Ícono de candado con reloj, título **"Acceso Pausado"**, explicación de límite de intentos superado, recuadro **TIEMPO RESTANTE** (`MM:SS`), botón **Reintentar Inicio de Sesión** y botón **Contactar Soporte**. | Inicia una cuenta regresiva en tiempo real de **15:00 minutos (900 segundos)**. El bloqueo persiste incluso si el usuario recarga la página (`F5`) o cierra y vuelve a abrir la pestaña. Al llegar a `00:00`, desbloquea automáticamente la pantalla, limpia la contraseña y devuelve al formulario. El botón *Contactar Soporte* abre el cliente de correo dirigido a `soporte@4guard.com` con el asunto *"Desbloqueo de Cuenta 4GUARD WMS"*. |
| **Acceso Restringido (Bloqueo Definitivo)** | Ícono de mazo en rojo, título **"Acceso Restringido"**, texto *"Cuenta inhabilitada permanentemente. Se han enviado reportes de auditoría al administrador."*, alerta *"Notificación enviada al correo del operador."*, botón **Reintentar Inicio de Sesión** y botón **Contactar Soporte**. | Se muestra cuando el servidor determina que la cuenta ha sido inhabilitada permanentemente por políticas de seguridad. Requiere intervención del administrador o de soporte técnico para reactivar la cuenta. |

#### Procedimientos Operativos Paso a Paso (SOPs)

##### SOP 1.1.1 — Inicio de sesión estándar en estación de trabajo
1. Abra el navegador en la dirección corporativa de 4GUARD WMS; el sistema lo dirigirá a `/login`.
2. En el campo **USUARIO O CORREO ELECTRÓNICO**, escriba su identificador institucional (sin espacios).
3. En el campo **CONTRASEÑA DE SEGURIDAD**, ingrese su clave. Si desea verificar lo escrito, haga clic en el ícono del ojo a la derecha del campo.
4. Verifique que el botón **ACCEDER AL SISTEMA** se encuentre habilitado y haga clic en él (o presione `Enter`).
5. El botón cambiará a **"INICIANDO SESIÓN..."**.
6. **Resultado según perfil y estado de cuenta:**
   - **Cuenta con contraseña temporal:** El sistema lo redirige obligatoriamente a `/change-password` para definir su clave definitiva (ver Sección 1.3).
   - **Perfil Guardia de Seguridad / Caseta:** El sistema lo dirige directamente al módulo de **Caseta de Seguridad** (`/security`).
   - **Sesión reanudada tras pausa o inactividad:** El sistema lo lleva exactamente a la pantalla donde estaba trabajando y muestra un banner superior verde **"Proceso Reanudado con Éxito"** durante 7 segundos.
   - **Resto de perfiles operativos y directivos:** El sistema carga el **Dashboard** principal (`/dashboard`).

##### SOP 1.1.2 — Actuación ante bloqueo temporal de 15 minutos
1. Si cometió 3 errores consecutivos de contraseña, observará la pantalla **Acceso Pausado** con el temporizador **TIEMPO RESTANTE** (iniciando en `15:00`).
2. **Opción A (Espera estándar):** Espere a que el reloj llegue a `00:00`. La pantalla regresará automáticamente al formulario de acceso limpio para que vuelva a ingresar sus credenciales correctas.
3. **Opción B (Olvido de clave):** Una vez habilitado el formulario (o si solicita asistencia), utilice el enlace **¿Olvidó su clave?** para restablecer su contraseña mediante código de verificación.
4. **Opción C (Asistencia inmediata):** Haga clic en **Contactar Soporte** para redactar un correo al equipo de soporte técnico solicitando revisión del bloqueo.

#### Estados, Validaciones, Mensajes de Error y Reglas de Negocio
- **Espacios en el identificador:** El campo de usuario/correo bloquea la pulsación de la barra espaciadora para prevenir errores tipográficos invisibles.
- **Validaciones en tiempo real:**
  - Usuario vacío o menor a 2 caracteres: *"Ingresa un usuario o correo electrónico válido"*.
  - Contraseña vacía: *"La contraseña es requerida"*.
  - Botón deshabilitado si la contraseña tiene menos de 6 caracteres.
- **Regla de los 3 intentos:** El sistema permite un máximo de **3 intentos fallidos** por cuenta.
  - Intento 1 fallido: Muestra error de credenciales, vibra el campo de contraseña y despliega la insignia `Intentos: 2`.
  - Intento 2 fallido: Muestra error, vibra el campo y despliega la insignia `Intentos: 1`.
  - Intento 3 fallido: Muestra *"Has agotado tus intentos. Por seguridad, tu acceso ha sido bloqueado temporalmente."* y transiciona de inmediato a la vista **Acceso Pausado** por **15 minutos (900 segundos)**.
- **Persistencia ante recargas:** Si el usuario presiona `F5` durante un bloqueo temporal, el sistema intercepta la carga y restaura de inmediato el temporizador exacto sin permitir evadir el bloqueo.
- **Limpieza automática al tener éxito:** Un inicio de sesión válido elimina cualquier contador de intentos fallidos acumulado para esa cuenta.

#### Preguntas Frecuentes / Solución de Problemas Rápida
- **¿Por qué el botón "ACCEDER AL SISTEMA" permanece inactivo?**
  Verifique que el usuario tenga al menos 2 caracteres y la contraseña al menos 6 caracteres.
- **Recargué la página después de bloquear mi cuenta, pero el cronómetro sigue corriendo. ¿Es un error?**
  No. Es una medida de seguridad deliberada para evitar la evasión de bloqueos. El tiempo restante se calcula contra la hora real hasta completar los 15 minutos.
- **Al entrar desde mi celular no veo el login de oficina.**
  Los dispositivos móviles sin sesión se redirigen al portal de Auto-Registro de Choferes en Caseta. Para usar la consola administrativa, ingrese desde una estación de escritorio.

---

### 1.2 Recuperación de Contraseña (Modal en Login y Página Dedicada)

#### Propósito operativo, ruta y roles con acceso
- **Propósito operativo:** Permitir al usuario recuperar el acceso a su cuenta de forma autónoma y segura mediante un flujo de verificación en tres etapas: correo registrado, código de verificación de 6 dígitos (OTP) y definición de una nueva contraseña robusta.
- **Rutas de acceso:**
  - **Modal integrado:** Desde `/login` haciendo clic en **¿Olvidó su clave?**.
  - **Página dedicada:** `/forgot-password` (cuenta con barra visual de progreso de 3 pasos: *1. Correo → 2. Código OTP → 3. Contraseña*, enlace superior `← Volver al inicio de sesión` y nota de registro en auditoría al finalizar).
- **Roles con acceso:** Pantalla pública disponible para todos los usuarios registrados con correo electrónico activo.

![Recuperación de Contraseña](screenshots/02-forgot-password.png)

#### Diccionario de Elementos de Interfaz

| Paso / Vista | Elemento / Etiqueta | Tipo | Obligatorio | Formato / Reglas | Comportamiento exacto |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **Paso 1: Correo** | **Correo electrónico** | Campo de correo | Sí | Formato válido `usuario@dominio.ext` | Solicita el correo registrado en la plataforma. Muestra *"El correo es requerido"* o *"Formato de correo inválido"* si no cumple. |
| **Paso 1: Correo** | **Enviar código de 6 dígitos** | Botón primario | Sí | Habilitado solo con correo válido | Envía la solicitud de código OTP. Cambia a *"Enviando..."* / *"Enviando código..."* durante el proceso y avanza automáticamente al Paso 2. |
| **Paso 1: Correo** | **Cancelar** *(solo en Modal)* | Botón secundario | No | Acción de cierre | Cierra el modal y reinicia todos los campos. También se puede cerrar presionando `Esc` o haciendo clic fuera de la tarjeta. |
| **Paso 2: Código OTP** | **6 Casillas de Código OTP** | Campos numéricos individuales | Sí | 1 dígito (`0–9`) por casilla | Al escribir un dígito, el cursor salta automáticamente a la siguiente casilla. Al presionar `Backspace` en una casilla vacía, regresa a la anterior. Permite **pegar (`Ctrl+V`)** el código completo de 6 dígitos de una sola vez. Al completarse los 6 dígitos, dispara la verificación automáticamente. |
| **Paso 2: Código OTP** | **Temporizador / Reenviar código** | Indicador dinámico / botón | No | Cuenta regresiva de **60 segundos** (`00:59` a `00:00`) | Mientras corre el tiempo muestra *"⏱ Reenviar código en 00:XX"*. Al llegar a cero se habilita el botón **"🔄 Reenviar código ahora"**, que limpia las casillas, solicita un nuevo código y reinicia los 60 segundos. |
| **Paso 2: Código OTP** | **Verificar código** | Botón primario | Sí | Habilitado al completar los 6 dígitos | Valida el código ingresado (cambia a *"Verificando..."*). Si es correcto, detiene el temporizador y avanza al Paso 3. |
| **Paso 2: Código OTP** | **← Cambiar correo** | Botón secundario / enlace | No | Navegación hacia atrás | Regresa al Paso 1 para corregir la dirección de correo electrónico. |
| **Paso 3: Nueva Contraseña** | **Nueva contraseña** | Campo de contraseña + ojo | Sí | Mín. 8 caracteres, al menos 1 minúscula, 1 mayúscula, 1 número y 1 símbolo (`@$!%*?&._-`) | Define la nueva clave de acceso. Incluye botón de visibilidad (mostrar/ocultar). |
| **Paso 3: Nueva Contraseña** | **Confirmar contraseña** | Campo de contraseña + ojo | Sí | Debe coincidir exactamente con *Nueva contraseña* | Si difiere, muestra *"Las contraseñas no coinciden"*. |
| **Paso 3: Nueva Contraseña** | **Guardar nueva contraseña** | Botón primario | Sí | Habilitado solo cuando la contraseña cumple la política y coincide | Guarda el cambio (muestra *"Guardando..."*) y avanza a la vista de confirmación. |
| **Paso 4: Éxito** | **¡Contraseña restablecida!** | Pantalla de confirmación | No aplica | Ícono de verificación verde + mensaje de éxito | En la página dedicada añade la nota *"Evento de recuperación asentado en el log de auditoría."* Incluye el botón **"Entendido, ir al login"** (en modal) o **"Ir al inicio de sesión"** (en página). |

#### Procedimientos Operativos Paso a Paso (SOPs)

##### SOP 1.2.1 — Restablecimiento de contraseña mediante código OTP
1. En la pantalla de inicio de sesión, haga clic en **¿Olvidó su clave?** (o navegue directamente a `/forgot-password`).
2. Escriba su dirección de correo electrónico corporativo en el campo **Correo electrónico** y pulse **Enviar código de 6 dígitos**.
3. Revise su bandeja de entrada (o carpeta de correo no deseado) y ubique el código numérico de 6 dígitos.
4. Ingrese el código en las 6 casillas (puede digitarlo número por número o copiarlo y pegarlo directamente en la primera casilla).
5. El sistema verificará automáticamente el código al completarse el sexto dígito (también puede hacer clic en **Verificar código**).
   - *Si no recibió el código:* Espere a que el temporizador de **60 segundos** finalice y haga clic en **Reenviar código ahora**.
6. En la pantalla **Nueva contraseña**, capture una clave que contenga al menos **8 caracteres, una letra minúscula, una letra mayúscula, un número y un símbolo especial** (`@ $ ! % * ? & . _ -`).
7. Repita exactamente la misma clave en **Confirmar contraseña** y haga clic en **Guardar nueva contraseña**.
8. Al visualizar el mensaje **¡Contraseña restablecida!**, haga clic en **Entendido, ir al login** (o **Ir al inicio de sesión**) y acceda con sus nuevas credenciales.

#### Estados, Validaciones, Mensajes de Error y Reglas de Negocio
- **Caracteres permitidos en OTP:** Las casillas rechazan automáticamente letras o símbolos; solo aceptan dígitos del `0` al `9`.
- **Pegado inteligente:** Si pega un texto con espacios o guiones (ej. `123-456`), el sistema extrae únicamente los primeros 6 dígitos numéricos y rellena las casillas en orden.
- **Política estricta de contraseña en recuperación:** Se exigen simultáneamente los 4 grupos de caracteres (minúscula, mayúscula, número y símbolo especial) más longitud mínima de 8 caracteres. Si falta alguno, se muestra *"Mínimo 8 caracteres, mayúscula, número y símbolo"*.
- **Bloqueo de reenvío rápido:** No es posible solicitar otro código OTP hasta que transcurran los 60 segundos reglamentarios.
- **Trazabilidad:** En la página `/forgot-password`, cada hito del proceso (solicitud de OTP, verificación exitosa, reenvío y restablecimiento completado) queda asentado en el registro de auditoría.

#### Preguntas Frecuentes / Solución de Problemas Rápida
- **No me llega el código de 6 dígitos. ¿Qué hago?**
  Verifique que haya escrito correctamente su correo corporativo (puede usar **← Cambiar correo**), revise la carpeta de Spam y espere los 60 segundos para pulsar **Reenviar código ahora**.
- **El botón "Guardar nueva contraseña" no se activa aunque escribí 8 caracteres.**
  Asegúrese de incluir una letra mayúscula, una minúscula, un número y al menos uno de los símbolos permitidos (`@ $ ! % * ? & . _ -`), y que ambos campos sean idénticos.

---

### 1.3 Cambio Obligatorio de Contraseña (Primer Acceso o Clave Temporal)

#### Propósito operativo, ruta y roles con acceso
- **Propósito operativo:** Garantizar que cualquier usuario al que se le haya asignado una contraseña temporal (por alta de cuenta o restablecimiento administrativo) establezca una contraseña personal y permanente antes de poder acceder a los módulos operativos del almacén.
- **Ruta de acceso:** `/change-password` (pantalla independiente sin barra lateral ni menú superior).
- **Roles con acceso:** Solo accesible para usuarios con sesión activa cuya cuenta tenga activada la bandera de cambio de contraseña obligatorio. Si un usuario sin sesión intenta entrar, es redirigido a `/login`; si un usuario ya cumplió el cambio, es redirigido automáticamente a `/dashboard`.

#### Diccionario de Elementos de Interfaz

| Elemento / Etiqueta | Tipo | Obligatorio | Formato / Reglas | Comportamiento exacto |
| :--- | :--- | :---: | :--- | :--- |
| **Indicador de Progreso (Panel Izquierdo)** | Lista de 3 pasos | No aplica | 1. *Contraseña temporal solicitada ✓*, 2. *Sesión iniciada ✓*, 3. *Establecer contraseña permanente* (activo) | Informa al usuario que está en el paso final para habilitar su acceso completo. |
| **Barra de Estado Superior** | Encabezado de página | No aplica | Texto `admin-console · Cambio de contraseña requerido` + botón **Salir** | Permite cerrar la sesión en cualquier momento si el usuario no desea completar el cambio ahora. |
| **Banner de Error** | Alerta roja | Condicional | Visible ante validaciones o errores de servidor | Indica requisitos faltantes, discrepancia entre contraseñas, sesión expirada o pérdida de conexión. |
| **Nueva contraseña** | Campo de contraseña + botón ojo | Sí | Mínimo 8 caracteres, combinación de letras (`a-z`/`A-Z`) y al menos un número (`0-9`) o símbolo (`!@#$%^&*`) | Evalúa la fortaleza en tiempo real mientras se escribe. |
| **Medidor de Fortaleza** | Barra de 3 segmentos + etiqueta | Dinámico | Niveles: **Débil** (1/3), **Moderada / Segura** (2/3), **Fuerte** (3/3) | Suma puntos al cumplir: (1) ≥ 8 caracteres, (2) letras + número/símbolo, (3) al menos una mayúscula o longitud ≥ 12 caracteres. |
| **Lista Interactiva de Requisitos** | Checklist en tiempo real | Informativo | 3 ítems con ícono de círculo vacío / palomita verde | Cambia a verde cada requisito conforme se cumple: *Mínimo 8 caracteres*, *Contener letras (a-z o A-Z)* y *Al menos un número (0-9) o símbolo (!@#$%^&*)*. |
| **Aviso de Requisito Faltante** | Recuadro ámbar | Condicional | Visible mientras escribe y falta algún requisito | Indica de forma puntual qué falta agregar (ej. *"Falta: un número o símbolo (0-9 o !@#$%^&*)."*). |
| **Confirmar contraseña** | Campo de contraseña + botón ojo | Sí | Debe ser idéntico a *Nueva contraseña* | Muestra *"Confirma la contraseña."* si está vacío o *"Las contraseñas no coinciden."* si difiere. |
| **Establecer contraseña permanente** | Botón primario | Sí | Deshabilitado únicamente durante el envío | Envía la nueva contraseña al servidor (muestra *"Guardando..."*). Si el cambio es exitoso, actualiza el estado de la cuenta y redirige automáticamente a `/dashboard`. |

#### Procedimientos Operativos Paso a Paso (SOPs)

##### SOP 1.3.1 — Establecimiento de contraseña permanente
1. Inicie sesión en `/login` con la contraseña temporal proporcionada por su administrador. El sistema lo llevará automáticamente a `/change-password`.
2. Escriba su nueva contraseña personal en el campo **Nueva contraseña** observando que las tres casillas de **Requisitos de seguridad** se marquen en verde.
3. Confirme la nueva clave escribiéndola nuevamente en **Confirmar contraseña**.
4. Haga clic en **Establecer contraseña permanente**.
5. Una vez confirmado el guardado, el sistema lo conducirá automáticamente a su consola operativa (`/dashboard`). No podrá volver a utilizar la contraseña temporal.

#### Estados, Validaciones, Mensajes de Error y Reglas de Negocio
- **No reutilización de clave temporal:** El sistema advierte que la contraseña temporal queda invalidada de forma definitiva.
- **Manejo de errores de red o servidor:**
  - *Error de validación del servidor:* Muestra el detalle devuelto o *"La contraseña no cumple los requisitos mínimos."*
  - *Sesión expirada:* Muestra *"Tu sesión ha expirado o no es válida. Por favor inicia sesión nuevamente."*
  - *Sin conexión:* Muestra *"Sin conexión al servidor. Verifica la red e inténtalo de nuevo."*
- **Cierre voluntario:** Al pulsar **Salir** en la esquina superior derecha, se cierra la sesión y el usuario regresa a `/login`. En el próximo ingreso con la contraseña temporal, volverá a solicitársele el cambio.

---

### 1.4 Entorno Principal de Navegación (Barra Lateral y Barra Superior)

Una vez autenticado, todos los módulos protegidos de 4GUARD WMS operan dentro del entorno unificado (**Shell**), compuesto por la **Barra Lateral Izquierda (Sidebar)**, la **Barra Superior (Header)** y el **Área Principal de Trabajo**.

#### 1.4.1 Barra Lateral Izquierda (Menú Principal y Consola de Usuario)

##### Diccionario de Elementos de la Barra Lateral

| Elemento / Sección | Tipo | Comportamiento exacto |
| :--- | :--- | :--- |
| **Bloque de Logotipo** (`4GUARD WMS · SyborX Console`) | Encabezado fijo | Identifica la aplicación. Al colapsar la barra lateral, mantiene únicamente el logotipo compacto. |
| **Botón Colapsar / Expandir Menú** (`chevron_left` / `chevron_right`) | Botón de alternancia | Contrae la barra lateral a una columna de íconos para maximizar el espacio horizontal de tablas y mapas; al pasar el cursor sobre cada ícono colapsado se muestra su nombre en una etiqueta emergente (*tooltip*). |
| **Menú de Navegación por Módulos** | Lista de enlaces filtrada por rol | Muestra únicamente los módulos autorizados para el rol activo. Al cambiar de módulo, el desplazamiento vertical del área principal se reinicia automáticamente al inicio de la página. |
| **Módulo con Submenú (`Recepción`)** | Menú desplegable | Al hacer clic en el título o en la flecha desplegable (`expand_more`), expande tres subrutas: **Recepción de Mercancía** (`/warehouse-movements/receiving`), **Cambio de Almacén** (`/warehouse-movements/transfers`) y **Salidas de Almacén** (`/warehouse-movements/outbound`). Si la barra estaba colapsada, hacer clic la expande automáticamente. |
| **Tarjeta de Usuario (Pie de Barra Lateral)** | Botón desplegable | Muestra el avatar con las **iniciales del usuario**, su **nombre completo** y la **etiqueta legible de su rol**. Al hacer clic, despliega el **Menú de Consola Operativa de Andén** (disponible cuando la barra está expandida). |
| **Botón Rápido de Salida** (`logout`) | Botón de acción | Ubicado junto a la tarjeta de usuario cuando la barra está expandida. Ejecuta el cierre de sesión definitivo inmediato. |

##### Matriz de Módulos Visibles en la Barra Lateral por Rol

| Módulo en Barra Lateral | Ruta | Director General / Admin / Gerentes | Coord. Operaciones | Sup. Operaciones | Mesa de Control | Líder de Turno | Guardia Seguridad | Inspector Calidad | Operario / Montacarguista |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Dashboard** | `/dashboard` | Sí | Sí | Sí | Sí | Sí | No | No | Sí |
| **Seguridad** | `/security` | Sí | No | Sí | No | No | **Exclusivo** | No | No |
| **Recepción** *(Recepción, Cambio de Almacén, Salidas)* | `/warehouse-movements/*` | Sí | Sí | Sí | Sí | Sí | No | No | Sí |
| **Calidad** | `/quality` | Sí | No | No | No | No | No | **Exclusivo** | No |
| **Rendimiento** | `/performance` | Sí | Sí | No | No | No | No | No | No |
| **Administrar** | `/admin` | Sí | No | No | No | No | No | No | No |
| **Almacén / Topología** | `/catalogs/warehouse` | Sí | Sí | Sí | Sí | Sí | No | No | No |
| **Consulta Inventario** | `/inventory-query` | Sí | Sí | Sí | Sí | Sí | No | No | Sí |

##### Menú Desplegable de Consola Operativa de Andén (Perfil en Barra Lateral)

Al hacer clic sobre su nombre en la parte inferior izquierda, se despliega un menú flotante con su encabezado (**Avatar, Nombre, Rol e Insignia de Turno**, ej. `TURNO 1`) dividido en tres secciones:

1. **Operación en Andén:**
   - **Pausar / Bloquear Estación:** Bloquea la terminal de forma inmediata preservando la pantalla actual (ver Sección 1.5).
   - **Cambio Rápido de Operador (PIN):** Abre el modal de relevo de turno por PIN de 4 dígitos sin cerrar la aplicación (ver Sección 1.6).
2. **Configuración:**
   - **Ver mi perfil:** Abre el modal **Ficha de Perfil** (consulta integral de 4 bloques: *Identidad y Contacto*, *Posición en la Empresa*, *Seguridad y Accesos*, *Preferencias y Sistema*).
   - **Cambiar contraseña:** Abre el modal **Cambiar contraseña** (ver tabla y procedimiento más abajo).
3. **Salida Definitiva:**
   - **Cerrar Sesión Definitiva:** Cierra la sesión en el servidor y en el navegador, redirigiendo a `/login`.

---

#### 1.4.2 Barra Superior (Header) y sus Herramientas

| Elemento (de izquierda a derecha) | Visibilidad | Comportamiento exacto |
| :--- | :--- | :--- |
| **Indicador de Sucursal** (`SUCURSAL: BR-TOL-01 - Toluca`) | Siempre visible | Muestra el código y nombre de la sucursal operativa activa. |
| **Notificador Inteligente de Patio** (`N UNIDAD(ES) EN PATIO · POR ASIGNAR ANDÉN`) | Condicional (cuando hay unidades registradas en caseta sin rampa asignada) | Píldora pulsante en color ámbar. Al hacer clic, navega directamente al módulo de **Recepción de Mercancía** con el filtro de unidades en espera (`REGISTERED`) aplicado. |
| **Indicador de Sincronización Offline** | Condicional (si hay pérdida de red u operaciones en cola) | Muestra un punto de estado y el mensaje de sincronización pendiente o modo sin conexión. |
| **Notificaciones Operativas por Rol y Módulo** | Condicional (cuando existen eventos activos en cola para el contexto del usuario) | Píldora interactiva con sonido de alerta, pulso visual y panel desplegable de tarjetas accionables (detallado en Sección 1.4.3). |
| **Badge de Alertas Críticas** (`N ALERTA(S) CRÍTICA(S)`) | Condicional (roles de Calidad, Gerencia y Dirección cuando hay alertas críticas activas) | Despliega el panel **Alertas Críticas del Sistema** (ej. excesos de tiempo en andén o caducidades próximas menores a 30 días) con botones de atención directa hacia **Recepción** o **Consulta de Inventario**. |
| **Reloj y Estado En Vivo** | Siempre visible | Muestra la hora local en formato 24 horas (`HH:MM:SS`), la fecha completa en español y el indicador verde **`En vivo`**. Cada **30 segundos** realiza una animación breve (`Actualizando...`) para confirmar la sincronización en tiempo real. |
| **Botón Menú Waffle (Ecosistema de Aplicaciones)** | Siempre visible | Abre el lanzador flotante **TUS FAVORITOS (GOOGLE & WMS)** con buscador, pestañas de filtro y cuadrícula de accesos directos (detallado en Sección 1.4.4). |
| **Botón Modo Claro / Oscuro** (Sol / Luna) | Siempre visible | Alterna inmediatamente entre tema claro y tema oscuro en toda la aplicación, sincroniza el aspecto de **ForBot AI** y guarda la preferencia en el navegador para futuras sesiones. |
| **Botón Cerrar Sesión** (`logout`) | Siempre visible | Ejecuta el cierre de sesión definitivo desde la esquina superior derecha. |

---

#### 1.4.3 Centro de Notificaciones Operativas en Tiempo Real

El sistema adapta automáticamente las notificaciones de la barra superior según el **rol del usuario** y el **módulo donde se encuentre trabajando**:

| Contexto Operativo | Qué notifica | Acciones rápidas en cada tarjeta |
| :--- | :--- | :--- |
| **Caseta / Seguridad** | Auto-registros móviles de choferes (F01 vía QR), rampas recién asignadas por almacén y unidades con maniobra concluida listas para salida. | **Cargar en Caseta** (precarga datos del chofer), **Dar Acceso** (rampa asignada) o **Check-Out** (registrar salida y sellos). |
| **Recepción (Inbound)** | Pre-recepciones generadas en caseta listas para descarga. | **Atender Folio** (abre directamente la recepción correspondiente). |
| **Salidas (Outbound)** | Pre-salidas y unidades listas para carga/embarque. | **Atender Salida** (abre el folio de salida). |
| **Calidad (QM)** | Lotes en cuarentena e inspecciones pendientes (`BLOQUEO QM`). | **Inspeccionar Lote** (navega al detalle del bloqueo en Calidad). |
| **Vista General (Dashboard / Traspasos)** | Combina Pre-Recepciones y Pre-Salidas con pestañas de filtro: **Todas**, **📥 Recepciones** y **📤 Salidas**. | Permite navegar a cada folio o ir al módulo completo desde los botones del pie del panel. |

**Comportamiento inteligente de apertura y cierre:**
- Cuando llega un evento nuevo en vivo, el sistema emite una señal acústica suave (tono breve), enciende el indicador pulsante y despliega automáticamente el panel durante **3 segundos** para informar sin interrumpir el trabajo.
- Si el usuario coloca el cursor sobre el panel desplegado, el cierre automático se **pausa** para permitir leer y actuar con calma; al retirar el cursor, el panel se cierra tras **2 segundos**.
- Al navegar a otra pantalla o hacer clic fuera del panel, este se cierra automáticamente.

---

#### 1.4.4 Menú Waffle (Ecosistema de Aplicaciones 4GUARD WMS)

Al presionar el ícono de cuadrícula (`apps`) en la barra superior, se abre el lanzador rápido con las siguientes funciones:
- **Buscador instantáneo:** Campo *"Buscar aplicación o servicio..."* con botón `×` para limpiar la búsqueda.
- **Pestañas de filtrado:** **Todos**, **Google Suite** y **Módulos WMS**.
- **Accesos directos disponibles:**
  - *Google Suite (abren en pestaña nueva):* **GMAIL**, **DRIVE**, **CALENDAR** (insignia `HOY`), **GEMINI IA** (insignia `IA`), **MEET**, **WHATSAPP**.
  - *Módulos WMS (navegación interna filtrada por permisos del rol):* **MAPS WMS** (`/layout`), **EVIDENCIA** (`/receiving`), **TUTORIALES** (`/dashboard`), **REGLAS** (`/business-rules`, insignia `MOTOR`), **CASETA & SEGURIDAD** (`/security`, insignia `CASETA`).
- **Pie del lanzador:** Muestra `ECOSISTEMA 4GUARD WMS · V2.8.14`.

---

#### 1.4.5 Modales Rápidos desde el Menú del Usuario

##### Modal: Cambiar Contraseña (desde la Barra Lateral)
- **Acceso:** Tarjeta de usuario (barra lateral) → **Cambiar contraseña**.
- **Campos:** *Nueva contraseña* (con botón ojo) y *Confirmar contraseña* (con botón ojo).
- **Requisitos obligatorios (3 de 3 para habilitar el botón `Guardar contraseña`):**
  1. Mínimo 8 caracteres.
  2. Una letra mayúscula (`A–Z`).
  3. Un número (`0–9`) o símbolo (`!@#$%^&*`).
- **Indicador de fortaleza:** Barra visual con etiquetas **Débil**, **Moderada** y **Fuerte**.
- **Comportamiento al guardar:** Muestra el mensaje **¡Contraseña actualizada!** y cierra el modal automáticamente después de **2 segundos**.

##### Modal: Ficha de Perfil (Consulta Rápida de Solo Lectura)
- **Acceso:** Tarjeta de usuario (barra lateral) → **Ver mi perfil**.
- **Contenido en 4 secciones:**
  1. **Identidad y Contacto:** Avatar con iniciales, nombre completo, `@usuario`, ID de Empleado, Correo electrónico y Teléfono.
  2. **Posición en la Empresa:** Organización, Sucursal asignada, Departamento, Cargo / Puesto y Reporta a (supervisor).
  3. **Seguridad y Accesos:** Rol en el sistema, Nivel de seguridad, Estado de cuenta (*Activo*) y Última conexión.
  4. **Preferencias y Sistema:** Zona horaria (por defecto `America/Mexico_City`), Idioma (por defecto `Español (México)`), Miembro desde y Última actualización (fecha, hora y usuario).
- **Resiliencia:** Si falla la conexión al cargar, ofrece el botón **Reintentar**.

---

### 1.5 Bloqueo de Estación y Protección por Inactividad

#### Propósito operativo
Proteger la información sensible del inventario y evitar operaciones no autorizadas cuando un operador se aleja temporalmente de una computadora o terminal en piso, garantizando al mismo tiempo que no pierda el contexto de su proceso en curso.

#### Modalidad A: Bloqueo Manual de Estación (*Pausar / Bloquear Estación*)
##### SOP 1.5.1 — Pausar y reanudar una estación de trabajo
1. Haga clic sobre su tarjeta de usuario en la parte inferior izquierda de la barra lateral.
2. En la sección **Operación en Andén**, seleccione **Pausar / Bloquear Estación**.
3. El sistema guarda automáticamente la ruta exacta en la que estaba trabajando, cierra la sesión y muestra la pantalla `/login` con el aviso ámbar **"Sesión Pausada por Inactividad"**.
4. Al regresar a la estación, ingrese nuevamente su usuario y contraseña.
5. El sistema lo regresará automáticamente a la pantalla exacta donde pausó su trabajo y desplegará el banner de confirmación **"Proceso Reanudado con Éxito"**.

#### Modalidad B: Detección Automática de Inactividad (15 minutos + 60 segundos de aviso)

##### Diccionario de Elementos del Modal de Inactividad

| Elemento / Etiqueta | Tipo | Comportamiento exacto |
| :--- | :--- | :--- |
| **Fondo Desenfocado Oscuro** | Capa de privacidad | Oculta visualmente los datos del inventario en segundo plano mientras el modal está activo. |
| **Título e Ícono** (`Inactividad Detectada`) | Encabezado de alerta | Explica que la sesión está a punto de cerrarse por motivos de seguridad. |
| **TIEMPO RESTANTE** (`60s` → `0s`) | Cuenta regresiva en vivo | Disminuye segundo a segundo. Al llegar a `0s`, cierra la sesión automáticamente. |
| **Mantener sesión activa** | Botón primario | Renueva el acceso con el servidor (cambia a *"Actualizando..."*), cierra el modal y reinicia el temporizador de **15 minutos** desde cero. |
| **Cerrar sesión ahora** | Botón secundario | Cierra la sesión de forma inmediata, guarda el nombre del proceso activo y redirige a `/login`. |
| **Nota de seguridad** | Pie informativo | *"Al finalizar el tiempo, se ocultarán todos los datos sensibles del inventario."* |

##### Reglas de Negocio de Inactividad
- **Eventos que reinician el reloj de 15 minutos:** Cualquier movimiento de ratón, pulsación de teclado, clic, desplazamiento de página o toque en pantalla reinicia automáticamente el conteo de 15 minutos continuos.
- **Preservación de proceso al cierre automático:** Si el contador de 60 segundos expira sin respuesta, el sistema guarda la ruta y el nombre legible del proceso (ej. *Recepción de Mercancía*, *Control de Calidad*, *Salidas de Almacén*), limpia los tokens de sesión y redirige a `/login` mostrando el aviso de sesión pausada.
- **Excepción de renovación fallida:** Si al pulsar *Mantener sesión activa* la sesión ya fue revocada en el servidor o el token expiró definitivamente, el sistema ejecuta el cierre seguro de inmediato.

---

### 1.6 Cambio Rápido de Operador por PIN (Relevo en Estación Compartida)

#### Propósito operativo y roles con acceso
- **Propósito operativo:** Permitir la conmutación ágil de turno y la firma de capturas en andén o caseta cuando dos o más colaboradores comparten la misma estación de trabajo, sin necesidad de cerrar sesión completa ni perder el contexto de la consola.
- **Acceso:** Tarjeta de usuario (barra lateral) → **Cambio Rápido de Operador (PIN)**.
- **Roles con acceso:** Usuarios con sesión autenticada activa en estaciones compartidas que cuenten con PIN operativo asignado.

#### Diccionario de Elementos del Modal

| Elemento / Etiqueta | Tipo | Obligatorio | Formato / Reglas | Comportamiento exacto |
| :--- | :--- | :---: | :--- | :--- |
| **Encabezado** (*Cambio Rápido de Operador (PIN)*) | Título + botón cerrar (`×`) | No aplica | Subtítulo: *"Conmutación ágil de turno y firma de capturas en andén"* | El botón `×` cierra el modal, limpia el PIN capturado y descarta mensajes previos. |
| **Banner de Éxito / Error** | Alertas dinámicas | Condicional | Verde (éxito) o rojo (error) | Confirma la conmutación o advierte si el PIN es incorrecto. |
| **Seleccionar Operador del Turno** | Lista desplegable | Sí | Muestra `Nombre Completo — Rol (Turno)` | Preselecciona al operador actual y permite elegir al colaborador entrante habilitado para relevo. |
| **PIN de Seguridad (4 Dígitos)** | Campo de contraseña numérica | Sí | Exactamente **4 dígitos** (ocultos) | Valida la identidad del operador seleccionado. |
| **Cancelar** | Botón secundario | No | Cierre sin cambios | Limpia el campo PIN y cierra el modal manteniendo al operador anterior. |
| **CONMUTAR OPERADOR** | Botón primario (dorado) | Sí | Habilitado solo al seleccionar operador e ingresar 4 dígitos | Verifica el PIN contra el operador elegido y aplica el relevo de identidad en vivo. |

#### Procedimientos Operativos Paso a Paso (SOPs)

##### SOP 1.6.1 — Ejecutar relevo de operador por PIN
1. Desde la barra lateral, haga clic en el bloque del usuario actual y seleccione **Cambio Rápido de Operador (PIN)**.
2. En el desplegable **Seleccionar Operador del Turno**, elija el nombre del operador que tomará el control de la estación.
3. En **PIN de Seguridad (4 Dígitos)**, ingrese el PIN personal de 4 dígitos del operador entrante.
4. Haga clic en **CONMUTAR OPERADOR**.
5. **Resultado:**
   - **PIN válido:** Aparece el mensaje verde *"¡Operador conmutado exitosamente a [Nombre del Operador]!"*. Tras 1.2 segundos el modal se cierra automáticamente y toda la interfaz (nombre, iniciales, rol, turno y módulos visibles) se actualiza al nuevo operador. Si el nuevo operador tiene acceso exclusivo a **Seguridad (Caseta)** o **Calidad**, el sistema lo redirige automáticamente a su módulo correspondiente.
   - **PIN incorrecto:** Aparece la alerta roja *"PIN incorrecto. Verifica la clave de 4 dígitos."* sin modificar al operador activo.

---

### 1.7 Asistente Inteligente ForBot AI (Modos Operativo y Tutor)

#### Propósito operativo y acceso
- **Propósito operativo:** Brindar un copiloto conversacional en tiempo real integrado en toda la consola de 4GUARD WMS, capaz de responder consultas sobre caducidades, ocupación de bodegas, estado de rampas, reglas de negocio, procedimientos de calidad e inducción interactiva guiada por pantalla.
- **Formas de apertura:**
  1. Clic en el **botón flotante circular (FAB)** ubicado en la esquina inferior derecha de la pantalla.
  2. Atajo de teclado global: **`Ctrl + K`** (en Windows/Linux) o **`Cmd + K`** (en macOS) desde cualquier módulo.
- **Roles con acceso:** Todos los usuarios autenticados (las sugerencias rápidas y el contenido de inducción se adaptan automáticamente al rol y turno activos).

#### Diccionario de Elementos del Panel ForBot AI

| Elemento / Sección | Tipo | Comportamiento exacto |
| :--- | :--- | :--- |
| **Botón Flotante (FAB)** | Disparador fijo inferior derecho | Muestra el avatar oficial de ForBot AI. Al pasar el cursor, despliega la etiqueta `🤖 ForBot AI · Ctrl + K`. Se oculta suavemente mientras el panel lateral está abierto. |
| **Encabezado del Drawer** | Barra superior del panel | Muestra el avatar, nombre **ForBot AI**, etiqueta **`LIVE`**, subtítulo *"Copiloto WMS 4GUARD"*, selector de modo y botón cerrar (`×`). |
| **Selector de Modos (`🛠️ Operativo` / `🎓 Tutor`)** | Interruptor de 2 estados | **Operativo (predeterminado):** Consultas en vivo sobre inventario, rampas, mermas, calidad y métricas. **Tutor:** Inducción guiada, explicación de reglas operativas y lanzamiento de tours interactivos de pantalla. |
| **Sub-barra de Contexto** | Banda informativa | Muestra el indicador verde de sesión activa, el **nombre del usuario**, su **rol** y su **turno** actual. |
| **Píldoras de Acción Rápida** | Carrusel horizontal de botones | Muestra atajos de consulta adaptados al rol y al modo (por ejemplo: *Mermas <30 días*, *Estado de Rampas*, *Inspección F01*, *Regla Diurex en PT*, *Tour Caseta & Patio*, *Tour Ejecutivo*). Al hacer clic en una píldora, envía la consulta o inicia el tour de inmediato. |
| **Historial Conversacional y Tarjetas en Vivo** | Área de mensajes | Presenta los mensajes del usuario (derecha) y las respuestas de ForBot (izquierda). Las respuestas pueden incluir **tarjetas con métricas `🟢 LIVE`**, **alertas proactivas (`💡`)**, botones de acceso directo al módulo (ej. *Ver Consulta de Inventario*) y el botón **`🚀 Iniciar Tour`**. |
| **Campo de Entrada de Texto** | Cuadro de consulta | Permite escribir preguntas en lenguaje natural. El texto de ayuda cambia según el modo (*"Pregunta sobre mermas, rampas, bodegas o folios..."* en Operativo; *"Pregunta sobre inducción, FSM o reglas..."* en Tutor). |
| **Botón de Micrófono (Dictado por Voz)** | Botón de entrada de voz | Activa el reconocimiento de voz del navegador (se ilumina en rojo pulsante mientras escucha). Al finalizar la frase, transcribe y envía la consulta automáticamente. |
| **Botón Enviar** (`send`) | Botón de envío | Envía la pregunta escrita (deshabilitado si el campo está vacío). |

#### Procedimientos Operativos Paso a Paso (SOPs)

##### SOP 1.7.1 — Realizar una consulta operativa o por voz
1. Presione **`Ctrl + K`** o haga clic en el botón flotante de **ForBot AI** en la esquina inferior derecha.
2. Verifique que el selector superior se encuentre en **`🛠️ Operativo`**.
3. Elija una de las **píldoras de acción rápida**, escriba su pregunta en el campo inferior y presione `Enter`, o haga clic en el **ícono de micrófono** para dictar su consulta por voz.
4. Revise la respuesta y la tarjeta de indicadores en vivo. Si la tarjeta incluye un botón de acción (por ejemplo, **Ver Consulta de Inventario**), haga clic en él para cerrar el panel y navegar directamente al módulo correspondiente.

##### SOP 1.7.2 — Iniciar un tour interactivo de inducción (Modo Tutor)
1. Abra **ForBot AI** y cambie el selector superior a **`🎓 Tutor`**.
2. Haga clic en la píldora de tour correspondiente a su perfil (ej. **🚀 Iniciar Tour**, **🚀 Tour Caseta & Patio**, **🚀 Tour Multi-Módulo** o **🚀 Tour Ejecutivo**), o bien haga clic en el botón **`🚀 Iniciar Tour`** dentro de la tarjeta de bienvenida.
3. Siga paso a paso las explicaciones resaltadas sobre los elementos reales de la pantalla.
4. Para cerrar el panel de ForBot AI en cualquier momento, haga clic en la `×`, presione **`Ctrl + K`** o haga clic en el fondo difuminado exterior.

---

### 1.8 Mi Perfil (`/profile`)

#### Propósito operativo, ruta y roles con acceso
- **Propósito operativo:** Ofrecer al usuario una vista dedicada de diseño modular (*Bento*) para consultar su identidad institucional, actualizar sus datos de contacto (correo electrónico institucional y teléfono), revisar los datos de seguridad de su última sesión y cerrar sesión de forma segura.
- **Ruta de acceso:** `/profile`
- **Roles con acceso:** Todos los usuarios autenticados en 4GUARD WMS.

> **Diferencia con el modal "Ficha de Perfil":** Mientras que el modal *Ficha de Perfil* del menú lateral ofrece una consulta rápida de solo lectura con 4 bloques organizacionales (incluyendo puesto, supervisor, zona horaria e idioma), la pantalla `/profile` permite **editar y guardar** la información de contacto del usuario y consultar la IP de red de la sesión.

![Mi Perfil](screenshots/05-profile.png)

#### Diccionario de Elementos de Interfaz

| Tarjeta | Elemento / Etiqueta | Tipo | Obligatorio | Formato / Reglas | Comportamiento exacto |
| :--- | :--- | :--- | :---: | :--- | :--- |
| **Tarjeta 1: Identidad** | **Avatar Circular con Iniciales** | Indicador visual | No aplica | 2 iniciales del nombre completo | Representa visualmente al usuario en formato destacado. |
| **Tarjeta 1: Identidad** | **Botón de Cámara** (`Cambiar foto`) | Botón circular | No | Ícono `photo_camera` | Ubicado en la esquina inferior derecha del avatar para la gestión de fotografía de perfil. |
| **Tarjeta 1: Identidad** | **Nombre, Usuario, Rol y Antigüedad** | Etiquetas de solo lectura | No aplica | Nombre completo, nombre de usuario, insignia dorada de rol y fecha **MIEMBRO DESDE** | Muestra la identidad corporativa vinculada a la cuenta (no editable por el propio usuario). |
| **Tarjeta 2: Contacto** | **Botón Editar** | Botón de modo edición | No | Visible solo en modo lectura | Habilita los campos *Correo Institucional* y *Teléfono* para su modificación y muestra los botones *Cancelar* y *Guardar Cambios*. |
| **Tarjeta 2: Contacto** | **CORREO INSTITUCIONAL** | Texto / Campo de correo | Sí (en edición) | Formato válido de correo electrónico | En modo lectura muestra el correo registrado (o *"No especificado"*). En modo edición marca el borde en rojo si está vacío o tiene formato inválido. |
| **Tarjeta 2: Contacto** | **TELÉFONO** | Texto / Campo telefónico | No | Teléfono con lada (ej. `+1 234 567 8900`) | Muestra o permite actualizar el número de contacto del colaborador. |
| **Tarjeta 2: Contacto** | **Cancelar** | Botón secundario | No | Visible solo en edición | Sale del modo edición sin enviar cambios al servidor. |
| **Tarjeta 2: Contacto** | **Guardar Cambios** | Botón primario (dorado) | Sí (en edición) | Habilitado solo si el formulario es válido | Envía los nuevos datos de contacto al servidor (muestra indicador giratorio y texto *"Guardando..."*) y regresa a modo lectura al finalizar. |
| **Tarjeta 3: Seguridad de la Cuenta** | **Última sesión activa** e **IP de red** | Indicadores de auditoría | No aplica | Tiempo transcurrido e IP local/red | Informa al usuario sobre la actividad reciente de su cuenta para detectar accesos no reconocidos. |
| **Tarjeta 3: Seguridad de la Cuenta** | **Cerrar Sesión** | Botón de acción (rojo) | No | Ícono `logout` | Finaliza la sesión activa en la estación y redirige a `/login`. |

#### Procedimientos Operativos Paso a Paso (SOPs)

##### SOP 1.8.1 — Actualizar correo institucional o teléfono en Mi Perfil
1. Ingrese a la ruta `/profile` en el navegador.
2. En la tarjeta **Contacto**, haga clic en el botón **Editar** ubicado en la esquina superior derecha de la tarjeta.
3. Modifique el campo **CORREO INSTITUCIONAL** (asegurándose de conservar un formato de correo válido) y/o el campo **TELÉFONO**.
4. Haga clic en **Guardar Cambios**. El botón mostrará **"Guardando..."** mientras se confirma la actualización.
5. Una vez completado el guardado, la tarjeta volverá automáticamente al modo de lectura mostrando los datos actualizados.

#### Estados, Validaciones, Mensajes de Error y Reglas de Negocio
- **Campos protegidos:** El nombre completo, el nombre de usuario, el rol asignado y la fecha de ingreso son controlados exclusivamente por el módulo de **Administración de Usuarios**; el usuario no puede alterarlos desde su perfil.
- **Validación de correo:** Si se deja vacío el correo institucional o se escribe un formato incorrecto, el borde del campo se resalta en rojo y el botón **Guardar Cambios** permanece deshabilitado.
- **Auditoría de visualización:** Cada consulta a la pantalla **Mi Perfil** genera un evento de trazabilidad.

#### Preguntas Frecuentes / Solución de Problemas Rápida
- **Necesito cambiar mi rol o corregir un error en mi nombre completo, pero no veo el botón para editarlo.**
  Por seguridad operativa y segregación de funciones (RBAC), los cambios de nombre, rol o sucursal solo pueden ser realizados por un **Administrador** o **Director General** desde el módulo **Administrar → Usuarios**.
- **¿Cómo cambio mi contraseña si no estoy en el primer acceso?**
  Haga clic en su tarjeta de usuario en la parte inferior izquierda de la barra lateral y seleccione **Cambiar contraseña**.
- **¿Cuál es la diferencia entre "Pausar / Bloquear Estación" y "Cerrar Sesión Definitiva"?**
  *Pausar / Bloquear Estación* resguarda la pantalla en la que estaba trabajando para que al volver a ingresar retome exactamente ese proceso. *Cerrar Sesión Definitiva* finaliza completamente la jornada en la terminal y limpia el contexto pendiente.
