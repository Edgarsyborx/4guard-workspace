# MANUAL DE USUARIO MAESTRO — 4GUARD WMS (EDICIÓN ENCICLOPÉDICA v2.0)

**Versión:** 2.0 — Edición Completa Quirúrgica (Rama `develop`)  
**Fecha de emisión:** Noviembre 2026  
**Alcance:** Consola Administrativa Web (`admin-console`), Portal Público Móvil de Caseta (`carrier-checkin`) y Terminal RF Operativa en Piso (`rf-terminal`).

---

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

---

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

---

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

---

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

---

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

---

## Capítulo 6. Catálogos Maestros y Entidades Operativas

Los catálogos maestros constituyen la columna vertebral de información de **4GUARD WMS**. Antes de recibir mercancía en andén, asignar ubicaciones de almacenamiento, programar citas de patio o despachar órdenes de salida, el sistema requiere contar con definiciones precisas y estandarizadas de los artículos manejados, las empresas propietarias de la mercancía, los proveedores de bienes y servicios, las líneas transportistas y el personal operativo certificado.

Todos los módulos descritos en este capítulo comparten una **arquitectura visual homologada** que reduce la curva de aprendizaje del personal administrativo:

* **Panel de Métricas Operativas Colapsable:** Ubicado en la cabecera de cada módulo. Por defecto inicia en **Vista Compacta** (una barra horizontal delgada con indicadores numéricos y puntos de color) para maximizar el espacio de trabajo. Al hacer clic en **Expandir Métricas** se despliega la cuadrícula completa de tarjetas KPI; al pulsar **Compactar Vista** regresa al formato resumido.
* **Patrón Master-Detail con Barra de Selector Desplegable:** En lugar de dividir permanentemente la pantalla, la parte superior del área de trabajo presenta una barra de selección que muestra el registro activo (o el modo de alta) junto con el botón de creación (**+ Nuevo…**). Al hacer clic en la barra se despliega un panel flotante (directorio) con buscador instantáneo, filtros combinados y lista interactiva. Debajo de la barra, el formulario de detalle ocupa el ancho completo de la pantalla para facilitar la captura y lectura de información extensa.
* **Trazabilidad y Auditoría Campo por Campo:** Todo registro existente incorpora en su parte inferior una sección de solo lectura denominada **Información de Control y Auditoría**, acompañada de una **Línea de Tiempo de Auditoría (Trazabilidad WMS)**. Esta bitácora registra cada alta, modificación, cambio de estatus o baja lógica indicando la acción realizada, el usuario responsable (`@usuario`), la fecha y hora exacta (`dd/MM/aaaa, hh:mm:ss a. m./p. m.`) y el comparativo visual **Valor anterior ➔ Valor nuevo** de cada atributo modificado.
* **Integridad Histórica (Sin Borrado Físico):** Para preservar la trazabilidad fiscal, de inventario y de movimientos históricos, 4GUARD WMS protege la información mediante **cambios de estatus** y **borrado lógico / archivado**, evitando que la desactivación de un catálogo corrompa remisiones, recibos o embarques pasados.

---

### 6.1 Catálogo de Productos / SKUs

![Catálogo de Productos y SKUs](screenshots/15-skus.png)

#### 6.1.1 Propósito Operativo, Ruta y Roles de Acceso

El **Catálogo de Productos / SKUs** centraliza el maestro de artículos (unidades de mantenimiento de stock) que ingresan, se resguardan y se embarcan en las instalaciones de la operación 3PL. Cada SKU queda estrictamente vinculado a un **Cliente Depositante (Owner)**, garantizando la segregación de inventario por propietario y proporcionando la unidad de medida y el peso unitario necesarios para cálculos de cubicaje, tarimas, recepciones, traspasos, salidas y consultas de inventario.

* **Ruta directa:** `/skus` (también accesible mediante `/admin/skus`).
* **Acceso desde el menú:** En la barra lateral izquierda seleccione **Administrar** (`/admin`) y, dentro de la categoría **Mercancía y Operatividad Logística**, haga clic en la tarjeta **PRODUCTOS**. Desde el encabezado del módulo puede regresar al panel principal mediante el enlace **← Administración WMS**.
* **Roles autorizados:**
  * **Acceso completo:** Director General (`ADMIN` / `CEO` / `SUPER_ADMIN`), Gerente de Operaciones (`OPERATIONS_MANAGER`) y Gerente de Almacén (`WAREHOUSE_MANAGER`).
  * **Acceso por permisos delegados:** Usuarios cuyo perfil incluya permisos administrativos sobre el módulo de administración (`USERS_READ` / `USERS_CREATE` y permisos específicos de catálogo).

#### 6.1.2 Interfaz Master-Detail, Tarjetas KPI y Buscador

1. **Métricas de Catálogo de SKUs (Colapsable):**
   * **Total Registrados / Total SKUs:** Cantidad total de artículos vigentes en el catálogo (excluye artículos con borrado lógico).
   * **SKUs Activos:** Artículos habilitados para operar en recepciones, inventario y embarques.
   * **SKUs Inactivos:** Artículos suspendidos temporalmente que no deben utilizarse en nuevas transacciones.
2. **Barra de Selección y Directorio Flotante:**
   * Muestra el avatar con las tres primeras letras/dígitos del código SKU (o el ícono de paquete en modo alta), el nombre comercial y el código SKU seleccionado.
   * Al desplegar el directorio se muestran los siguientes controles:
     * **Buscador global:** Filtra en tiempo real por código de SKU (ej. `43211385`), nombre comercial, descripción, nombre del cliente depositante o ID corto de sistema de 8 dígitos (`#XXXXXXXX`).
     * **Filtro por Cliente Depositante:** Lista desplegable con *Todos los clientes* o un propietario específico.
     * **Filtro por Estatus Operativo:** *Todos los estatus*, *Activos* o *Inactivos*.
     * **Botón Limpiar filtros:** Aparece automáticamente cuando existe al menos un criterio activo.
   * **Tarjetas del directorio:** Cada elemento exhibe avatar de iniciales, nombre comercial, etiqueta `SKU: <código>`, ID corto de sistema (`#XXXXXXXX`), unidad de medida, insignia de estatus (*Activo* / *Inactivo*), cliente depositante titular y peso unitario en kilogramos (`kg`).
   * **Paginación reactiva:** Ubicada al pie del directorio flotante. Permite elegir el tamaño de página (**10 / pág**, **30 / pág** o **50 / pág**), visualiza el rango actual (ej. `1-10 de 45`) y ofrece botones de página anterior y siguiente. Cualquier cambio en el buscador o los filtros reinicia la vista a la página 1.

#### 6.1.3 Diccionario Completo de Campos

| Campo | Obligatoriedad | Formato / Longitud máxima | Valores permitidos | Reglas de negocio |
| :--- | :--- | :--- | :--- | :--- |
| **Cliente Depositante (Owner)** | Obligatorio | Lista desplegable (catálogo de clientes) | Clientes registrados en la organización activa | Al iniciar un nuevo SKU se preselecciona automáticamente el primer cliente disponible. Determina la propiedad 3PL del artículo y el ámbito de unicidad del código SKU. |
| **Código Único de SKU** | Obligatorio | Texto alfanumérico en fuente monoespaciada / Máx. 50 caracteres | Caracteres alfanuméricos y guiones (ej. `SKU-NES-001`) | No se permiten solo espacios en blanco. Al guardar se recorta y convierte automáticamente a **MAYÚSCULAS**. Debe ser único para el cliente depositante; si ya existe, el sistema rechaza la operación con alerta de conflicto. |
| **Nombre Comercial del Producto** | Obligatorio | Texto libre / Máx. 200 caracteres | Texto descriptivo (ej. `Leche Nido 1kg Lata`) | No se permiten solo espacios en blanco. Es la denominación principal visible en directorios, remisiones y reportes. |
| **Unidad de Medida** | Obligatorio | Lista desplegable estandarizada | `Caja (BOX)`, `Pieza (PZA)`, `Piezas (PCS)`, `Kilogramos (KG)`, `Tarima / Pallet (PALLET)`, `Litros (LITRO)`, `Bolsa / Saco (BAG)` | Valor por defecto: `Caja (BOX)`. Estandariza la unidad base de conteo en piso. |
| **Peso Unitario (kg)** | Obligatorio | Numérico decimal (paso `0.001`) | Mayor o igual a `0` | Valor por defecto: `1.000`. No admite valores negativos. Soporta hasta tres decimales para precisión en báscula y cubicaje. |
| **Descripción Detallada / Observaciones** | Opcional | Área de texto multilínea / Máx. 500 caracteres | Texto libre (especificaciones de empaque, manejo, etc.) | Se recorta de espacios sobrantes al guardar. |
| **ID de Sistema (8 dígitos)** | Solo lectura (edición) | Código compacto `#XXXXXXXX` | Generado automáticamente por el servidor | Representación abreviada del identificador único interno (al pasar el cursor muestra el identificador completo). |
| **Estatus Actual en BD** | Solo lectura (edición) | Insignia de estado | `ACTIVE` / `INACTIVE` | Todo SKU nuevo se crea con estatus `ACTIVE`. Se modifica mediante el botón **Suspender / Activar**. |
| **Versión de Registro** | Solo lectura (edición) | Numérico (`v1`, `v2`, …) | Entero incremental | Control de versiones interno para auditoría y concurrencia. |
| **Fecha de creación / Última actualización** | Solo lectura (edición) | Fecha y hora (`dd/MM/aaaa HH:mm`) | Timestamp del servidor | Generadas automáticamente en cada alta y actualización. |

#### 6.1.4 Procedimientos Paso a Paso (SOPs)

##### SOP 6.1.A — Alta de un Nuevo Producto / SKU
1. Ingrese a **Administrar → PRODUCTOS** (`/skus`). Por defecto el formulario inicia listo para un alta nueva; si está consultando un SKU previo, haga clic en el botón **+ Nuevo SKU** en la barra superior.
2. Seleccione el **Cliente Depositante (Owner)** propietario de la mercancía.
3. Capture el **Código Único de SKU** (el sistema lo convertirá a mayúsculas automáticamente al guardar) y el **Nombre Comercial del Producto**.
4. Seleccione la **Unidad de Medida** correspondiente e ingrese el **Peso Unitario (kg)** (hasta 3 decimales).
5. De manera opcional, agregue una **Descripción Detallada / Observaciones** con notas de empaque o manejo.
6. Haga clic en **Crear SKU**.
7. **Resultado esperado:** El sistema valida que los campos obligatorios estén completos y que el código no exista duplicado para ese cliente. Al confirmarse, se muestra el banner verde *"Producto / SKU guardado correctamente"*, el formulario pasa a modo edición con el nuevo registro seleccionado y se registra el primer evento en la **Línea de Tiempo de Auditoría**.

##### SOP 6.1.B — Edición de un Producto / SKU
1. Haga clic en la barra superior del selector de SKU para abrir el directorio flotante.
2. Localice el artículo mediante el buscador o los filtros de cliente/estatus y haga clic sobre él.
3. Modifique los atributos necesarios en la sección **Información del Producto / SKU**.
4. Haga clic en **Guardar cambios** (o en **Cancelar** para descartar cambios y reiniciar el formulario).
5. **Resultado esperado:** El sistema actualiza la ficha, incrementa la versión del registro, muestra confirmación visual y recarga la bitácora de auditoría con el detalle exacto de cada campo modificado.

##### SOP 6.1.C — Cambio de Estatus (Suspender / Activar) y Borrado Lógico (Desactivar)
El módulo distingue entre la **suspensión reversible** y la **desactivación por borrado lógico**:

* **Para suspender o reactivar temporalmente:**
  1. Seleccione el SKU deseado en el directorio.
  2. En la barra inferior izquierda del formulario, haga clic en **Suspender** (si el SKU está `Activo`) o en **Activar** (si está `Inactivo`).
  3. **Resultado esperado:** El estatus operativo cambia de inmediato entre `ACTIVE` e `INACTIVE`, actualizando los contadores KPI y generando un evento `Cambio de estatus operativo (ACTIVE / INACTIVE)` en la línea de tiempo de auditoría. El SKU permanece visible en el directorio al filtrar por *Inactivos*.

* **Para realizar el borrado lógico (retiro definitivo del catálogo operativo):**
  1. Con el SKU seleccionado en modo edición, haga clic en el botón rojo **Desactivar**.
  2. Se abrirá el diálogo de confirmación **"Desactivar Producto del Catálogo"**, advirtiendo que el artículo se retirará de las listas activas de operación en el almacén mientras su historial de movimientos e inventario histórico permanecen resguardados.
  3. Haga clic en **Confirmar** (o **Cancelar** para abortar).
  4. **Resultado esperado:** El registro queda marcado con borrado lógico, desaparece de la lista operativa del catálogo y se cierra la vista de detalle.

##### SOP 6.1.D — Consulta de Auditoría Campo por Campo
1. Abra cualquier SKU existente en modo edición y desplácese a la sección **Información de Control y Auditoría BE**.
2. Revise los metadatos de control (ID de sistema, código SKU, estatus actual, versión, fecha de creación y última actualización).
3. En el bloque **Línea de Tiempo de Auditoría (Trazabilidad WMS)** revise cada nodo cronológico codificado por color:
   * **Verde:** Alta de producto (`Producto / SKU registrado en el catálogo`).
   * **Ámbar:** Modificación de atributos (`Modificación de atributos de producto`).
   * **Morado:** Cambio de estatus (`Cambio de estatus operativo`).
   * **Rojo:** Baja lógica / eliminación.
4. Dentro de cada evento, verifique la etiqueta `@usuario`, la fecha y hora exacta y el bloque de diferencias campo por campo (ej. `Peso Unitario (kg): 1.000 ➔ 1.250`, `Unidad de Medida: BOX ➔ PALLET`, `Estado Operativo: Activo ➔ Inactivo`).
5. Si requiere recargar eventos recientes sin salir de la pantalla, pulse el botón circular de actualización (**Actualizar bitácora**).

---

### 6.2 Gestión de Clientes (Depositantes / Owners 3PL)

![Gestión de Clientes](screenshots/16-clients.png)

#### 6.2.1 Propósito Operativo, Ruta y Roles de Acceso

El módulo de **Gestión de Clientes** administra el catálogo maestro de empresas depositantes (propietarios de mercancía en el esquema 3PL). Además de los datos fiscales y corporativos, permite gestionar en una sola ficha la **matriz dinámica de contactos corporativos**, las **credenciales de acceso al portal web de autoservicio** y el catálogo de **direcciones físicas de destino** (bodegas, plantas y CEDIS *ship-to*) hacia donde se despachan las órdenes de salida.

* **Ruta directa:** `/clients` (también accesible mediante `/admin/clients`).
* **Acceso desde el menú:** En la barra lateral izquierda seleccione **Administrar** (`/admin`) y, dentro de la categoría **Mercancía y Operatividad Logística**, haga clic en la tarjeta **Clientes / Owners**.
* **Roles autorizados:** Director General (`ADMIN` / `CEO` / `SUPER_ADMIN`), Gerente de Operaciones (`OPERATIONS_MANAGER`), Gerente de Almacén (`WAREHOUSE_MANAGER`) y perfiles con permisos administrativos delegados.

#### 6.2.2 Interfaz Master-Detail, Tarjetas KPI y Buscador

1. **Métricas de Cartera de Clientes (Colapsable):**
   * **Empresas Registradas / Empresas:** Total de clientes depositantes dados de alta en la organización activa.
   * **Clientes Activos:** Empresas habilitadas para recibir y despachar mercancía.
   * **Clientes Inactivos:** Empresas suspendidas operativamente.
   * **Destinos / Bodegas:** Sumatoria total de destinos físicos (plantas, bodegas y CEDIS) registrados entre todos los clientes.
2. **Barra de Selección y Directorio Flotante:**
   * Muestra las iniciales de la razón social, el nombre del cliente y su código externo/RFC entre paréntesis.
   * **Buscador inteligente:** Filtra simultáneamente por razón social, código externo/RFC, nombre de organización, dirección fiscal y **también por el nombre de planta o dirección de cualquiera de sus destinos físicos**.
   * **Filtro por Estado:** *Todos los estados*, *Activos* o *Inactivos*, con botón **Limpiar filtros** cuando hay criterios activos.
   * **Tarjetas del directorio:** Muestran avatar de iniciales, razón social, código externo/RFC, contador de destinos físicos (`X dest.`), dirección fiscal, teléfono principal e insignia de estatus (`ACTIVA` / `INACTIVA`).

#### 6.2.3 Diccionario Completo de Campos

##### Sección 1 — Información General y Domicilio Fiscal

| Campo | Obligatoriedad | Formato / Longitud máxima | Valores permitidos | Reglas de negocio |
| :--- | :--- | :--- | :--- | :--- |
| **Razón Social / Nombre Comercial** | Obligatorio | Texto libre / Máx. 150 caracteres | Texto (ej. `NESTLE MEXICO S.A. DE C.V.`) | No permite solo espacios en blanco. Identifica al cliente en todo el WMS. |
| **Código Cliente / RFC Fiscal (External ID)** | Obligatorio | Texto alfanumérico monoespaciado / Máx. 50 caracteres | RFC o clave ERP (ej. `NME850101K99` o `CLI-1002`) | No permite solo espacios en blanco. Al guardar se convierte automáticamente a **MAYÚSCULAS**. Debe ser único en el sistema; si ya existe se devuelve error de duplicidad. |
| **Dirección Fiscal / Corporativa** | Obligatorio | Texto libre | Domicilio completo (ej. `Av. Ejército Nacional 453, Granada, CDMX`) | No permite solo espacios en blanco. Sirve además como dirección de respaldo si el cliente no cuenta con destinos físicos adicionales. |
| **Teléfono Corporativo Principal** | Opcional | Texto / Numérico | Ej. `55 5268 2000` | Se recorta de espacios sobrantes al guardar. |
| **Correo Electrónico General** | Opcional | Formato de correo electrónico | Correo válido (ej. `contacto@empresa.com`) | Si se captura, debe cumplir con formato estándar de email. |
| **Organización Perteneciente** | Obligatorio | Lista desplegable | Organizaciones activas de la arquitectura multi-tenant | Preseleccionada automáticamente con la organización activa de la sesión. |
| **Contraseña Web Autoservicio** | Opcional | Texto en fuente monoespaciada | Contraseña de acceso al Portal de Clientes | Por defecto se inicializa con una contraseña temporal (`4GuardTemp#2026`), modificable por el administrador. |

##### Sección 2 — Panel Dinámico de Contactos Corporativos (Sub-entidad 1:N)

| Campo | Obligatoriedad | Formato / Longitud máxima | Valores permitidos | Reglas de negocio |
| :--- | :--- | :--- | :--- | :--- |
| **Nombre Contacto** | Obligatorio | Texto libre | Ej. `Ing. Carlos Fuentes` | No permite solo espacios en blanco. Todo cliente debe conservar al menos **1 contacto** en el formulario. |
| **Departamento** | Obligatorio | Texto libre | Ej. `Logística y Abasto` | En el alta de un cliente nuevo se precarga el valor sugerido `Logística y Abasto`. |
| **Teléfono Directo** | Opcional | Texto / Numérico | Ej. `55 1234 5678` | Teléfono directo o móvil del ejecutivo de cuenta. |
| **Correo Electrónico** | Obligatorio | Formato de correo electrónico | Correo válido (ej. `cfuentes@empresa.com`) | Validación estricta de formato email. |

##### Sección 3 — Direcciones Físicas de Destino (Bodegas, Plantas, Cedis — Sub-entidad 1:N)

| Campo | Obligatoriedad | Formato / Longitud máxima | Valores permitidos | Reglas de negocio |
| :--- | :--- | :--- | :--- | :--- |
| **Código de Destino** | Automático (solo lectura en tarjeta) | Prefijo `DEST-XXX` | Ej. `DEST-104` | Generado automáticamente al agregar una nueva tarjeta de destino. |
| **Nombre de Planta / Bodega** | Obligatorio (si existe el destino) | Texto libre | Ej. `Planta Toluca (Café y Cacao)` | No permite solo espacios en blanco. En un cliente nuevo se sugiere inicialmente `Planta Principal / Almacén Central`. |
| **Persona de Contacto en Sitio** | Obligatorio (si existe el destino) | Texto libre | Ej. `Ing. Fernando Ruiz` | Responsable que recibe la mercancía en la planta o bodega destino. |
| **Teléfono de Planta / Bodega** | Obligatorio (si existe el destino) | Texto / Numérico | Ej. `722 279 1000` | Teléfono directo de caseta o almacén en el sitio de entrega. |
| **Dirección Física Completa de Entrega** | Obligatorio (si existe el destino) | Texto libre | Ej. `Km 62.5 Carretera México-Toluca, Zona Industrial Toluca, EdoMex` | No permite solo espacios en blanco. Si el cliente no tiene destinos registrados, el sistema muestra el aviso *"Sin destinos físicos registrados. Se utilizará la dirección fiscal por defecto."* |

#### 6.2.4 Procedimientos Paso a Paso (SOPs)

##### SOP 6.2.A — Alta de Cliente con Contactos y Destinos Físicos
1. Ingrese a **Administrar → Clientes / Owners** (`/clients`) y asegúrese de estar en modo alta (haga clic en **+ Nuevo cliente** si había un registro abierto).
2. Complete la **Sección 1 (Información General y Domicilio Fiscal)**: Razón Social, Código Cliente / RFC Fiscal, Dirección Fiscal, Organización y, de forma opcional, teléfono, correo y contraseña del portal autoservicio.
3. En la **Sección 2 (Panel Dinámico de Contactos Corporativos)** capture los datos del contacto inicial obligatorio (Nombre, Departamento, Correo y opcionalmente Teléfono Directo).
4. En la **Sección 3 (Direcciones Físicas de Destino)** complete el destino base precargado o elimínelo si el cliente operará únicamente con su dirección fiscal.
5. Haga clic en **Crear cliente y destinos**.
6. **Resultado esperado:** El sistema valida todas las secciones (incluidos todos los contactos y destinos agregados). Si falta algún dato obligatorio se notificará en rojo y mediante mensaje de alerta. Al guardarse con éxito, el cliente queda en estatus `ACTIVA`, pasa a modo edición y se actualiza el KPI de **Empresas Registradas** y **Destinos / Bodegas**.

##### SOP 6.2.B — Gestión de Sub-entidades (Contactos Corporativos y Destinos Físicos)
* **Agregar o quitar contactos corporativos:**
  1. En la **Sección 2**, haga clic en **+ Agregar Contacto** para insertar una nueva tarjeta de contacto.
  2. Complete Nombre, Departamento, Correo Electrónico y (opcional) Teléfono Directo.
  3. Para eliminar un contacto sobrante, pulse el ícono de papelera rojo en el extremo derecho de la tarjeta. *Regla de negocio:* El botón de eliminar solo está disponible cuando existen **2 o más contactos**, garantizando que el cliente siempre conserve al menos un contacto principal.
* **Agregar o quitar direcciones físicas de destino:**
  1. En la **Sección 3**, haga clic en **+ Agregar Destino Físico**. El sistema asignará automáticamente una etiqueta de código (`DEST-XXX`).
  2. Capture el Nombre de Planta / Bodega, Persona de Contacto en Sitio, Teléfono de Planta / Bodega y Dirección Física Completa de Entrega.
  3. Para retirar un destino, haga clic en el ícono de papelera rojo en la esquina superior derecha de la tarjeta del destino.
  4. Al finalizar los cambios en contactos o destinos, haga clic en **Guardar cambios** para persistir la matriz completa.

##### SOP 6.2.C — Edición y Cambio de Estatus Operativo (Suspender / Activar)
1. Seleccione el cliente desde el directorio desplegable.
2. Para modificar datos generales, contactos o destinos, edite los campos necesarios y haga clic en **Guardar cambios**.
3. Para cambiar el estatus operativo del cliente:
   * Si el cliente está **ACTIVO**, haga clic en **Suspender** en la barra inferior izquierda.
   * Si el cliente está **INACTIVO**, haga clic en **Activar**.
4. En el diálogo modal **"Cambiar Estado de Cliente"**, verifique el nombre del cliente y el nuevo estado objetivo (*INACTIVO* o *ACTIVO*) y pulse **Confirmar**.
5. **Resultado esperado:** El estatus cambia inmediatamente, la insignia del encabezado se actualiza (`ACTIVA` ↔ `INACTIVA`), los KPIs se recalculan y se registra el evento `Cambio de estado del cliente` en la línea de tiempo de auditoría.

##### SOP 6.2.D — Consulta de Auditoría Campo por Campo
1. En modo edición, desplácese a la **Sección 4 — Información de Control y Auditoría BE**.
2. Consulte los metadatos de solo lectura: ID de Sistema de 8 dígitos (`#XXXXXXXX`), Código Externo / ERP, Fecha de creación, Última actualización y Versión de Registro (`v1`, `v2`, …).
3. En la **Línea de Tiempo de Auditoría (Trazabilidad WMS)** inspeccione los eventos registrados, que incluyen tanto cambios en la ficha principal (`Cliente registrado`, `Modificación de atributos`, `Cambio de estado`) como eventos sobre sub-entidades (`Nuevo destino físico agregado`, `Destino físico actualizado`, `Destino físico eliminado`).
4. Cada nodo muestra el usuario `@responsable`, la fecha/hora exacta y el detalle campo por campo (Razón Social, Código/RFC, Dirección Fiscal, Total de Contactos, Total de Destinos, Estado Operativo, etc.). Pulse **Actualizar bitácora** para refrescar en cualquier momento.

---

### 6.3 Catálogo de Proveedores

![Catálogo de Proveedores](screenshots/17-suppliers.png)

#### 6.3.1 Propósito Operativo, Ruta y Roles de Acceso

El **Catálogo de Proveedores** administra el padrón oficial de empresas proveedoras de **bienes** (mercancías, materias primas, material de empaque, tarimas, refacciones) y **servicios** (mantenimiento, limpieza industrial, seguridad, control de plagas, tecnología, servicios generales y transporte comercial). Su diseño está preparado para entornos 3PL multi-cliente y multi-almacén mediante **Alcance 3PL (Row-Level Security)**, además de gestionar condiciones comerciales, tiempos de respuesta/entrega, banderas de inspección de calidad (QM) y estatus con motivo obligatorio.

> **Nota operativa importante:** La categoría *Servicios de transporte (comercial)* dentro de Proveedores representa al proveedor comercial o facturador de fletes y **no sustituye** al catálogo operativo de **Gestión de Transportistas** (Sección 6.4), el cual administra líneas de transporte, tipos de unidad y operación en caseta y patio.

* **Ruta directa:** `/suppliers` (también accesible mediante `/admin/suppliers`).
* **Acceso desde el menú:** En la barra lateral izquierda seleccione **Administrar** (`/admin`) y, en la categoría **Mercancía y Operatividad Logística**, haga clic en la tarjeta **Proveedores**.
* **Roles autorizados:** Director General (`ADMIN` / `CEO` / `SUPER_ADMIN`), Gerente de Operaciones (`OPERATIONS_MANAGER`), Gerente de Almacén (`WAREHOUSE_MANAGER`) y perfiles con permisos explícitos de proveedores (`SUPPLIERS_READ` / `SUPPLIERS_CREATE`).

#### 6.3.2 Interfaz Master-Detail, Tarjetas KPI y Buscador

1. **Métricas del Catálogo de Proveedores (Colapsable):**
   * **Total:** Total de proveedores activos en el catálogo (excluye archivados).
   * **Activos:** Proveedores disponibles para compras, recepciones y órdenes de servicio.
   * **No Disponibles:** Suma de proveedores en estatus *Inactivo* o *Bloqueado*.
   * **Preferentes:** Proveedores marcados con estrella dorada como prioritarios en sugerencias de compras y citas.
2. **Barra de Selección y Directorio Flotante:**
   * Muestra avatar de iniciales, nombre comercial (o razón social) y código único (`PRV-XXXX`).
   * **Buscador avanzado:** Filtra en tiempo real por código de proveedor, RFC / Tax ID (ignorando espacios y guiones), razón social, nombre comercial, nombre del contacto, correo electrónico o ciudad.
   * **Cuadrícula de 4 filtros combinables:**
     1. **Estado:** *Todos los estados*, *Activos*, *Inactivos* o *Bloqueados*.
     2. **Categoría:** Las 13 categorías de suministro o servicio.
     3. **Cliente 3PL:** Filtra proveedores asignados a un cliente específico.
     4. **Almacén:** Filtra proveedores asignados a una sucursal/almacén específico.
   * Incluye botón **Limpiar filtros** cuando existe algún filtro aplicado.
   * **Tarjetas del directorio:** Muestran iniciales, nombre comercial, estrella dorada si es preferente, razón social (si difiere del nombre comercial), código `PRV-XXXX`, insignia de estatus (*Activo* / *Inactivo* / *Bloqueado*), categoría y ciudad.
3. **Protección contra pérdida de datos:** Si el usuario modifica campos del formulario e intenta seleccionar otro proveedor, crear uno nuevo o cancelar sin guardar, el sistema despliega el diálogo **"Cambios sin guardar"** con las opciones *Volver al formulario* o *Descartar cambios*.

#### 6.3.3 Diccionario Completo de Campos

##### Sección 1 — Información General y Sección 2 — Clasificación

| Campo | Obligatoriedad | Formato / Longitud máxima | Valores permitidos | Reglas de negocio |
| :--- | :--- | :--- | :--- | :--- |
| **Código de Proveedor** | Obligatorio (auto-sugerido) | Texto alfanumérico / Máx. 30 caracteres | Formato sugerido `PRV-0001` (consecutivo automático) | Al guardar se eliminan espacios y guiones y se convierte a **MAYÚSCULAS**. Debe ser único; el sistema valida duplicidad localmente y en el servidor. |
| **RFC / Tax ID Fiscal** | Obligatorio | Texto alfanumérico / Máx. 20 caracteres | RFC mexicano o Tax ID fiscal (ej. `ENN980415HG8`) | No permite solo espacios. Se normaliza sin espacios/guiones y en **MAYÚSCULAS**. Validación estricta de unicidad entre proveedores. |
| **Razón Social (Fiscal)** | Obligatorio | Texto libre / Máx. 200 caracteres | Razón social oficial | No permite solo espacios en blanco. |
| **Nombre Comercial / Marca** | Opcional | Texto libre / Máx. 150 caracteres | Alias o marca comercial | Si se captura, se muestra como título principal en el directorio y encabezado. |
| **Proveedor Preferente** | Opcional | Interruptor (Sí / No) | Activado / Desactivado (default: Desactivado) | Prioriza al proveedor en sugerencias de compras y asignación de citas; agrega insignia de estrella dorada en el directorio. |
| **Observaciones Generales** | Opcional | Área de texto / Máx. 1000 caracteres | Notas sobre convenios, certificaciones o vigencias | Texto libre. |
| **Categoría de Suministro / Servicio** | Obligatorio | Lista desplegable (13 opciones) | **Bienes:** `Bienes y mercancías`, `Materia prima`, `Material de empaque`, `Tarimas y estibas`, `Refacciones`, `Otros suministros`.<br>**Servicios:** `Servicios de transporte (comercial)`, `Mantenimiento`, `Limpieza industrial`, `Seguridad y vigilancia`, `Control de plagas / Fumigación`, `Tecnología y sistemas`, `Servicios generales`. | Valor por defecto: `Bienes y mercancías`. **Regla adaptativa:** Al seleccionar una categoría de *Servicios*, el banner informativo y las etiquetas de la Sección 5 cambian automáticamente a modo Servicios (*Tiempo de respuesta* y *Validación y Cierre Operativo de Servicio*); en categorías de *Bienes* operan en modo Bienes (*Tiempo de entrega* e *Inspección de Calidad Obligatoria QM*). |

##### Sección 3 — Contacto Principal y Sección 4 — Dirección y Ubicación

| Campo | Obligatoriedad | Formato / Longitud máxima | Valores permitidos | Reglas de negocio |
| :--- | :--- | :--- | :--- | :--- |
| **Nombre Completo del Contacto** | Obligatorio | Texto libre / Máx. 150 caracteres | Nombre y apellidos | No permite solo espacios en blanco. |
| **Puesto / Cargo** | Opcional | Texto libre / Máx. 100 caracteres | Ej. `Gerente de Cuentas Clave` | Cargo del ejecutivo de contacto. |
| **Correo Electrónico** | Obligatorio | Formato email / Máx. 150 caracteres | Correo electrónico válido | Se almacena automáticamente en minúsculas. |
| **Teléfono Principal** | Opcional | Teléfono (7 a 15 dígitos) | Dígitos, espacios, `+`, `-` y paréntesis | Si se ingresa, debe contener entre 7 y 15 dígitos numéricos reales. |
| **Teléfono Alternativo / Móvil** | Opcional | Teléfono (7 a 15 dígitos) | Dígitos, espacios, `+`, `-` y paréntesis | Misma validación de 7 a 15 dígitos si se captura. |
| **País** | Obligatorio | Texto libre | Valor por defecto: `México` | País del domicilio fiscal/operativo. |
| **Estado / Entidad** | Obligatorio | Texto libre / Máx. 100 caracteres | Ej. `Nuevo León` | No permite solo espacios en blanco. |
| **Ciudad** | Obligatorio | Texto libre / Máx. 100 caracteres | Ej. `Monterrey` | No permite solo espacios en blanco. Se muestra en el directorio como referencia geográfica. |
| **Código Postal** | Opcional | Texto / Máx. 10 caracteres | Ej. `66600` | Código postal de ubicación. |
| **Calle y Colonia** | Opcional | Texto libre / Máx. 200 caracteres | Nombre de vialidad y colonia | Dirección física detallada. |
| **Número Exterior / Número Interior** | Opcional | Texto / Máx. 20 caracteres c/u | Ej. `450` / `Bodega 3` | Complemento del domicilio. |

##### Sección 5 — Condiciones Operativas y Sección 6 — Alcance 3PL (RLS)

| Campo | Obligatoriedad | Formato / Longitud máxima | Valores permitidos | Reglas de negocio |
| :--- | :--- | :--- | :--- | :--- |
| **Tiempo de entrega (días) / Tiempo de respuesta (días)** | Obligatorio | Numérico entero | Mayor o igual a `0` (default: `1`) | La etiqueta se adapta dinámicamente según si el proveedor es de Bienes o de Servicios. |
| **Monto Mínimo de Pedido** | Obligatorio | Numérico | Mayor o igual a `0` (default: `0`) | Expresado en la moneda comercial seleccionada. |
| **Días de Crédito** | Obligatorio | Numérico entero | Mayor o igual a `0` (default: `0`) | Plazo pactado de pago. |
| **Moneda Comercial** | Obligatorio | Lista desplegable | `Pesos Mexicanos (MXN)`, `Dólares US (USD)`, `Euros (EUR)` | Valor por defecto: `MXN`. |
| **Requiere Inspección de Calidad (QM) / Validación de Servicio** | Opcional | Interruptor (Sí / No) | Activado / Desactivado (default: Desactivado) | Si está activo, exige control de calidad al ingreso de mercancía o validación de cierre al concluir servicios. |
| **Nivel de Alcance 3PL** | Obligatorio | Lista desplegable | `Global (Toda la org)`, `Específico de Cliente`, `Específico de Almacén` | Valor por defecto: `Global`. Define la visibilidad RLS dentro de la operación 3PL. |
| **Cliente Propietario Asignado** | Condicional | Lista desplegable de clientes | Clientes registrados | **Obligatorio** únicamente cuando el Alcance 3PL es `Específico de Cliente`. Al cambiar a otro alcance se limpia automáticamente. |
| **Almacén / Sede Físico Asignado** | Condicional | Lista desplegable de sucursales | Sucursales/almacenes registrados | **Obligatorio** únicamente cuando el Alcance 3PL es `Específico de Almacén`. Al cambiar a otro alcance se limpia automáticamente. |

#### 6.3.4 Procedimientos Paso a Paso (SOPs)

##### SOP 6.3.A — Alta y Edición de un Proveedor
1. Ingrese a **Administrar → Proveedores** (`/suppliers`). Para un registro nuevo pulse **+ Nuevo proveedor** (el sistema precargará el siguiente código consecutivo `PRV-XXXX`). Para editar, abra el selector superior y elija el proveedor deseado.
2. Complete la **Sección 1 (Información General)**: verifique el código, ingrese el **RFC / Tax ID**, **Razón Social**, opcionalmente **Nombre Comercial**, active **Proveedor Preferente** si aplica y capture observaciones.
3. En la **Sección 2 (Clasificación)** elija la categoría. Observe el banner de confirmación (*Proveedor de Bienes y Materiales* o *Proveedor de Servicios*).
4. Complete la **Sección 3 (Contacto Principal)** y la **Sección 4 (Dirección y Ubicación)**.
5. Configure la **Sección 5 (Condiciones Operativas y Comerciales)**: días de entrega/respuesta, monto mínimo, días de crédito, moneda y bandera de inspección QM / validación de servicio.
6. Configure la **Sección 6 (Alcance 3PL)** siguiendo el SOP 6.3.B.
7. Haga clic en **Guardar proveedor**.
8. **Resultado esperado:** El sistema valida formato, obligatoriedad y no duplicidad de RFC y código. Al confirmarse, el proveedor se guarda con estatus `Activo` (en alta), aparece la notificación de éxito y se habilita la tarjeta y línea de tiempo de auditoría.

##### SOP 6.3.B — Configuración de Alcance 3PL (Row-Level Security)
1. En la **Sección 6 — Alcance 3PL**, despliegue el campo **Nivel de Alcance 3PL**:
   * Seleccione **Global (Toda la org)** si el proveedor atiende a todos los clientes y almacenes de la corporación (no requiere selección adicional).
   * Seleccione **Específico de Cliente** si el proveedor suministra exclusivamente para un cliente depositante; aparecerá el selector obligatorio **Cliente Propietario Asignado**.
   * Seleccione **Específico de Almacén** si el proveedor opera únicamente en una sede física (ej. limpieza, vigilancia o mantenimiento local); aparecerá el selector obligatorio **Almacén / Sede Físico Asignado**.
2. Guarde los cambios para aplicar la regla de visibilidad.

##### SOP 6.3.C — Cambio de Estatus (con Motivo Obligatorio) y Archivo Lógico
* **Cambiar estado (Activo / Inactivo / Bloqueado):**
  1. Con el proveedor seleccionado en modo edición, haga clic en **Cambiar estado** en la barra inferior izquierda.
  2. En el diálogo modal **"Cambiar estado del proveedor"**, seleccione el **Nuevo Estado Operativo**: *Activo*, *Inactivo* o *Bloqueado (Temporal)* (utilizado por incumplimientos, auditorías o revisiones de calidad).
  3. Capture obligatoriamente el **Motivo del Cambio** (máximo 400 caracteres, no puede quedar vacío).
  4. Haga clic en **Confirmar cambio**.
  5. **Resultado esperado:** El estado del proveedor se actualiza, los KPIs de *Activos* y *No Disponibles* se recalculan y el cambio queda documentado en la auditoría.

* **Archivar proveedor (Borrado Lógico):**
  1. En modo edición, haga clic en **Archivar proveedor** en la barra inferior izquierda.
  2. Lea la advertencia del diálogo **"Archivar proveedor del catálogo"**, que confirma que el registro quedará archivado lógicamente preservando todo su historial fiscal y operativo, pero dejará de estar disponible para nuevas recepciones, compras u órdenes.
  3. Haga clic en **Archivar proveedor** para confirmar.
  4. **Resultado esperado:** El proveedor sale del directorio activo y el formulario se limpia.

##### SOP 6.3.D — Consulta de Auditoría Campo por Campo
1. Al abrir cualquier proveedor existente, desplácese al final del formulario para consultar primero la **Tarjeta de Registro de Auditoría (Audit Log)** con: *Creado por*, *Fecha creación*, *Modificado por*, *Última actualización* y *Última acción*.
2. Justo debajo, inspeccione la **Línea de Tiempo de Auditoría (Trazabilidad WMS)**. Cada nodo muestra el resumen del evento, el `@usuario`, la marca de tiempo y el comparativo **Valor anterior ➔ Valor nuevo** de los atributos modificados (Razón Social, Nombre Comercial, RFC, Código, Tipo de Proveedor, Estado, Tiempo de Entrega, Pedido Mínimo, Días de Crédito, Alcance 3PL).
3. Utilice el botón **Actualizar bitácora** para recargar eventos directamente desde el servidor.

---

### 6.4 Gestión de Transportistas

![Gestión de Transportistas](screenshots/18b-carriers-real.png)

#### 6.4.1 Propósito Operativo, Ruta y Roles de Acceso

El módulo de **Gestión de Transportistas** centraliza el catálogo maestro oficial de las empresas responsables del traslado físico de mercancía hacia y desde el centro de distribución. Este catálogo es consumido directamente por los módulos operativos de **Caseta de Seguridad (Smart Gate)**, **Control de Patio**, **Recepción**, **Salidas de Almacén** y **Programación de Ventanas**. A diferencia del control de unidades individuales (placas y choferes registrados al momento del arribo en caseta), aquí se gobierna la **línea transportista**, sus **capacidades generales de vehículos**, su clasificación, cobertura, permiso SCT y estatus operativo.

* **Ruta directa:** `/carriers` (también accesible mediante `/admin/carriers`).
* **Acceso desde el menú:** En la barra lateral izquierda seleccione **Administrar** (`/admin`) y, dentro de la categoría **Mercancía y Operatividad Logística**, haga clic en la tarjeta **Transportistas**.
* **Roles autorizados:** Director General (`ADMIN` / `CEO` / `SUPER_ADMIN`), Gerente de Operaciones (`OPERATIONS_MANAGER`), Gerente de Almacén (`WAREHOUSE_MANAGER`) y perfiles con permisos explícitos de transportistas (`CARRIERS_READ` / `CARRIERS_CREATE`).

#### 6.4.2 Interfaz Master-Detail, Tarjetas KPI y Buscador

1. **Métricas de Líneas de Transporte (Colapsable):**
   * **Total:** Total de líneas transportistas registradas.
   * **Activos:** Líneas habilitadas para programación de citas, recepción y despacho.
   * **Suspendidos:** Líneas pausadas temporalmente (no aparecen disponibles en nuevas programaciones).
   * **Inactivos:** Líneas dadas de baja lógica (se conserva el historial para trazabilidad).
2. **Barra de Selección y Directorio Flotante:**
   * Muestra avatar circular con color distintivo según el tipo de transportista, el **Sobre Nombre** (nombre comercial) y el RFC (`S/RFC` si no se capturó).
   * **Buscador extendido:** Filtra en tiempo real por sobre nombre, razón social, RFC, nombre del contacto, teléfono (comparando dígitos), correo electrónico y observaciones.
   * **Filtros combinados:**
     * **Estado:** *Todos los estados*, *Activos*, *Suspendidos* o *Inactivos*.
     * **Tipo de transportista:** *Todos los tipos*, *Externo*, *Del cliente*, *Propio*, *3PL* o *Paquetería*.
     * Botón **Limpiar filtros** cuando hay criterios activos.
   * **Tarjetas del directorio:** Muestran avatar, sobre nombre, razón social, contador de capacidades vehiculares (`X unid.`), RFC, insignia de estatus y fecha de última actualización (`dd/MM/aa`).

#### 6.4.3 Diccionario Completo de Campos

##### Sección 1 — Información de la Línea Transportista y Sección 2 — Tipo de Unidad y Operación

| Campo | Obligatoriedad | Formato / Longitud máxima | Valores permitidos | Reglas de negocio |
| :--- | :--- | :--- | :--- | :--- |
| **Razón Social de la Línea Transportista** | Obligatorio | Texto libre / Máx. 200 caracteres | Razón social oficial (ej. `Transportes del Noreste S.A. de C.V.`) | No permite solo espacios en blanco. Debe ser única en el sistema. |
| **Sobre Nombre** | Obligatorio | Texto libre / Máx. 150 caracteres | Alias o nombre comercial (ej. `TransNoreste`) | No permite solo espacios en blanco. Es el nombre visible principal en caseta, patio y listas de selección. |
| **Tipo de Unidad (Capacidades de Vehículos)** | Recomendado / Operativo | Casillas de selección múltiple (8 opciones) | `Caja seca`, `Caja refrigerada`, `Plataforma`, `Tortón`, `Rabón`, `Tractocamión`, `Camioneta`, `Motocicleta` | Representa las **capacidades generales de flota** que maneja la línea transportista (no unidades individuales con placas). Permite seleccionar una o múltiples combinaciones. |
| **Observaciones** | Opcional | Área de texto / Máx. 1000 caracteres | Acuerdos operativos, condiciones de servicio o notas | Incluye contador de caracteres en vivo (`0 / 1000`). |

##### Sección 3 — Datos Complementarios (Opcionales)

| Campo | Obligatoriedad | Formato / Longitud máxima | Valores permitidos | Reglas de negocio |
| :--- | :--- | :--- | :--- | :--- |
| **RFC / Tax ID** | Opcional | Texto alfanumérico monoespaciado / Máx. 13 caracteres | RFC mexicano válido: 12 caracteres (persona moral) o 13 caracteres (persona física) | **Validación asíncrona en vivo:** Al salir del campo, el sistema convierte el RFC a mayúsculas, valida su sintaxis oficial y consulta al servidor en tiempo real si el RFC está disponible (mostrando ícono verde *El RFC está disponible* o alerta roja si ya pertenece a otro transportista). |
| **Clasificación de Transportista** | Opcional | Lista desplegable | `Transportista externo` (default), `Transporte del cliente`, `Transporte propio`, `Proveedor logístico 3PL`, `Paquetería` | Define el color del avatar en el directorio y permite segmentar el catálogo por tipo de operación. |
| **Nombre del Contacto** | Opcional | Texto libre / Máx. 100 caracteres | Ej. `Roberto Garza` | Ejecutivo operativo o coordinador de tráfico de la línea. |
| **Teléfono de Contacto** | Opcional | Teléfono (7 a 15 dígitos) | Dígitos, `+`, `-`, paréntesis y espacios | Validación de 7 a 15 dígitos numéricos si se captura. |
| **Correo Electrónico** | Opcional | Formato email / Máx. 150 caracteres | Correo válido | Se normaliza en minúsculas al guardar. |
| **Tipo de Servicio** | Opcional | Lista desplegable | `Carga completa (FTL)` (default), `Carga parcial (LTL)`, `Paquetería`, `Multimodal`, `Última milla`, `Servicio dedicado` | Clasifica la modalidad logística ofrecida. |
| **No. de Permiso / Registro** | Opcional | Texto monoespaciado / Máx. 80 caracteres | Ej. `SCT-NL-00234-2022` | Número de permiso SCT u otro registro oficial de autotransporte federal. |
| **Cobertura Geográfica** | Opcional | Texto libre / Máx. 300 caracteres | Ej. `Noreste y Bajío (NL, CDMX, QRO, GTO)` | Rutas y regiones atendidas por la línea. |
| **Clientes Preferentes** | Solo lectura (edición) | Etiquetas informativas | Lista de clientes vinculados | Visible cuando el transportista tiene clientes preferentes asignados; consumido por programación de ventanas. |

#### 6.4.4 Procedimientos Paso a Paso (SOPs)

##### SOP 6.4.A — Alta y Edición de un Transportista
1. Ingrese a **Administrar → Transportistas** (`/carriers`). Para registrar una línea nueva haga clic en **+ Nuevo transportista**; para editar, selecciónela desde el directorio desplegable.
2. En la **Sección 1 (Información de la Línea Transportista)** capture la **Razón Social** y el **Sobre Nombre**.
3. En la **Sección 2 (Tipo de Unidad y Operación)** marque las casillas correspondientes a las **Capacidades de Vehículos** que opera la línea (ver SOP 6.4.B) y agregue **Observaciones** si se requiere.
4. De manera opcional, complete la **Sección 3 (Datos Complementarios)**:
   * Si ingresa el **RFC / Tax ID**, espere un segundo al cambiar de campo para verificar la confirmación verde de disponibilidad en el servidor.
   * Seleccione la **Clasificación de Transportista**, el **Tipo de Servicio**, datos de contacto, **No. de Permiso SCT** y **Cobertura Geográfica**.
5. Haga clic en **Registrar transportista** (o **Guardar cambios** en modo edición).
6. **Resultado esperado:** El sistema guarda la ficha con estatus `Activo` (en alta), valida concurrencia optimista en edición (si otro usuario modificó el registro previamente, solicita recargar), muestra confirmación visual y actualiza la línea de tiempo de auditoría.

##### SOP 6.4.B — Gestión de Capacidades de Vehículos (Tipos de Unidad)
1. En la **Sección 2 — Tipo de Unidad y Operación**, ubique la cuadrícula de casillas **Tipo de Unidad**.
2. Haga clic sobre cada tipo de vehículo que la empresa transportista tiene capacidad de operar (*Caja seca*, *Caja refrigerada*, *Plataforma*, *Tortón*, *Rabón*, *Tractocamión*, *Camioneta*, *Motocicleta*). Las opciones seleccionadas se resaltan visualmente.
3. Para retirar una capacidad, vuelva a hacer clic sobre la casilla marcada.
4. Haga clic en **Guardar cambios**. El contador `X unid.` en el directorio reflejará automáticamente la cantidad de capacidades habilitadas.

##### SOP 6.4.C — Cambio de Estatus (Suspender, Desactivar con Motivo Obligatorio y Reactivar)
El catálogo de transportistas nunca realiza borrado físico; su ciclo de vida se controla mediante tres acciones en la barra inferior izquierda del formulario:

* **Suspender temporalmente (`ACTIVE` ➔ `SUSPENDED`):**
  1. Con un transportista activo seleccionado, haga clic en el botón ámbar **Suspender**.
  2. En el diálogo **"Suspender transportista"**, capture obligatoriamente el **Motivo** (máx. 500 caracteres) y, opcionalmente, **Observaciones adicionales** (máx. 500 caracteres).
  3. Pulse **Confirmar**. El transportista pasa a estatus `Suspendido` y deja de aparecer en nuevas programaciones.

* **Desactivar / Baja Lógica (`ACTIVE` o `SUSPENDED` ➔ `INACTIVE`):**
  1. Haga clic en el botón rojo **Desactivar**.
  2. En el diálogo **"Desactivar transportista"**, capture obligatoriamente el **Motivo** y opcionalmente observaciones adicionales.
  3. Pulse **Confirmar**. El transportista pasa a estatus `Inactivo` conservando toda su trazabilidad histórica.

* **Reactivar (`SUSPENDED` o `INACTIVE` ➔ `ACTIVE`):**
  1. Seleccione un transportista suspendido o inactivo y haga clic en el botón verde **Activar**.
  2. El sistema reactiva inmediatamente a la línea transportista, registra automáticamente el motivo *"Reactivación manual por operador"* y actualiza la auditoría.

##### SOP 6.4.D — Consulta de Auditoría Campo por Campo
1. Seleccione cualquier transportista existente y desplácese a la **Sección 4 — Información de Control**.
2. Consulte los metadatos de solo lectura: *Fecha de creación*, *Última actualización*, *Creado por*, *Modificado por* y, cuando exista, el **Motivo del último cambio de estado**.
3. En la **Línea de Tiempo de Auditoría (Trazabilidad WMS)** revise cada evento con su ícono y color semántico (alta, actualización, cambio de estado o baja), el usuario `@responsable`, la fecha/hora exacta y las diferencias campo por campo (Razón Social, Nombre Comercial, RFC, Tipo de Transportista, Estado Operativo, Contacto, Teléfono, Correo, Notas y Motivo de Estado).
4. Pulse el botón **Actualizar bitácora** si desea refrescar el historial desde el servidor.

---

### 6.5 Gestión de Montacarguistas

#### 6.5.1 Propósito Operativo, Ruta y Roles de Acceso

El módulo de **Gestión de Montacarguistas** administra el padrón oficial de operadores certificados responsables de la carga, descarga, estiba y movimiento físico de mercancía en andenes y pasillos del almacén. Este catálogo cumple una doble función operativa crítica:

1. **Cumplimiento normativo y asignación operativa:** Registra la sucursal asignada, el turno de trabajo y el control de vigencia de la **Certificación STPS / DC-3** mediante un semáforo automático calculado por el servidor (*Vigente*, *Por vencer* en 30 días o menos, y *Vencida*).
2. **Aprovisionamiento de acceso a Terminal RF / Tableta:** Permite crear automáticamente las credenciales operativas del montacarguista (`@usuario` y **PIN temporal**) y restablecer su PIN cuando sea necesario, forzando el cambio de contraseña/PIN confidencial en el primer inicio de sesión del operador.

* **Ruta directa:** `/admin/forklift-operators`.
* **Acceso desde el menú:** En la barra lateral izquierda seleccione **Administrar** (`/admin`) y, dentro de la categoría **Mercancía y Operatividad Logística**, haga clic en la tarjeta **Montacarguistas**.
* **Roles autorizados:** Director General (`ADMIN` / `CEO` / `SUPER_ADMIN`), Gerente de Operaciones (`OPERATIONS_MANAGER`), Gerente de Almacén (`WAREHOUSE_MANAGER`) y perfiles con acceso administrativo al panel `/admin`.

#### 6.5.2 Interfaz Master-Detail, Tarjetas KPI y Buscador

1. **Métricas de Operadores WMS (Colapsable):**
   * **Total:** Total de montacarguistas registrados en la organización.
   * **Activos:** Operadores habilitados para ser asignados a tareas en andén y patio.
   * **DC-3 Vigentes:** Operadores cuya certificación STPS / DC-3 tiene más de 30 días de vigencia restante.
   * **Licencias en Alerta:** Suma de operadores con certificación **Por Vencer (≤ 30 días)** o **Vencida**, facilitando la prevención de incumplimientos de seguridad industrial.
2. **Barra de Selección y Directorio Flotante:**
   * Muestra avatar con las iniciales del nombre y apellido paterno, nombre completo, código operativo autogenerado (`MC-XXX`) y usuario de terminal RF (`@usuario` o `Sin RF`).
   * **Buscador rápido:** Filtra en tiempo real por nombre completo, código operativo (`MC-XXX`), puesto/cargo o número de licencia DC-3.
   * **Filtro por Estatus de Licencia DC-3:** *Todas las licencias*, *Licencia Vigente*, *Por Vencer (< 30 días)* o *Licencia Vencida*, acompañado del botón **Limpiar filtros**.
   * **Tarjetas del directorio:** Muestran iniciales, nombre completo, insignia semáforo de DC-3 (*DC-3 Vigente* en verde, *DC-3 Por vencer* en ámbar, *DC-3 Vencida* en rojo/gris), usuario RF (`@usuario`), código operativo y sucursal asignada (o *Corporativo* si tiene acceso multi-sucursal).

#### 6.5.3 Diccionario Completo de Campos

##### Sección 1 — Información Personal y Sección 2 — Asignación Operativa

| Campo | Obligatoriedad | Formato / Longitud máxima | Valores permitidos | Reglas de negocio |
| :--- | :--- | :--- | :--- | :--- |
| **Nombre(s)** | Obligatorio | Texto libre / Mín. 2, Máx. 100 caracteres | Nombre de pila (ej. `Juan Manuel`) | Se concatena automáticamente en el servidor junto con ambos apellidos para formar el Nombre Completo. |
| **Apellido Paterno** | Obligatorio | Texto libre / Mín. 2, Máx. 100 caracteres | Ej. `Pérez` | Requerido para identificación oficial y trazabilidad de firmas/movimientos. |
| **Apellido Materno** | Obligatorio | Texto libre / Mín. 2, Máx. 100 caracteres | Ej. `Hernández` | Requerido para evitar homonimias en piso. |
| **Sucursal Asignada** | Opcional | Lista desplegable | `Acceso Corporativo / Todas las sucursales` (default) o una sucursal/almacén específico (`Código — Nombre`) | Limita o habilita la disponibilidad del montacarguista en la sede seleccionada. |
| **Turno Operativo de Almacén** | Opcional | Lista desplegable | `Sin turno fijo / Horario flexible` (default) o cualquier turno activo del catálogo de Turnos (`Código — Nombre (Inicio - Fin)`) | Solo muestra turnos cuyo estatus sea `Activo`. |
| **Código Operativo** | Automático (solo lectura) | Formato `MC-XXX` | Ej. `MC-001`, `MC-002` | Generado automáticamente por el servidor al crear el registro. |

##### Sección 3 — Acceso a Terminal RF / Tableta Operativa (Sub-entidad de Credenciales)

| Campo | Obligatoriedad | Formato / Longitud máxima | Valores permitidos | Reglas de negocio |
| :--- | :--- | :--- | :--- | :--- |
| **Habilitar cuenta de acceso para Terminal RF / Tableta** | Opcional (solo en alta) | Casilla de verificación | Activada (default) / Desactivada | Si se deja marcada durante el alta, el sistema crea automáticamente la cuenta de usuario con rol operativo para tableta/RF. |
| **Usuario para Tableta** | Opcional (solo en alta con acceso habilitado) | Texto monoespaciado / Máx. 50 caracteres | Ej. `MC-001` | Si se omite, el sistema asigna automáticamente el código operativo generado (`MC-XXX`). |
| **PIN / Contraseña Temporal** | Obligatorio (en alta con acceso habilitado y al restablecer) | Texto monoespaciado / Mín. 4, Máx. 10 caracteres | Numérico o alfanumérico (default sugerido: `1234`) | Configurado por el supervisor. Activa automáticamente la bandera de **cambio de contraseña obligatorio** para que el operador defina su PIN confidencial en su primer ingreso. |
| **Estado de Acceso RF (Modo Edición)** | Solo lectura | Panel informativo | `Cuenta Activa` / `Sin Acceso RF` + estado del PIN (`Pendiente de cambio` o `PIN personalizado y activo`) | Incluye el botón directo **Restablecer PIN** para emitir una nueva clave temporal. |

##### Sección 4 — Certificación STPS / DC-3 (Opcional)

| Campo | Obligatoriedad | Formato / Longitud máxima | Valores permitidos | Reglas de negocio |
| :--- | :--- | :--- | :--- | :--- |
| **Número de Licencia DC-3** | Opcional | Texto monoespaciado / Máx. 50 caracteres | Ej. `LIC-MC-9921` | Número de constancia de competencias o habilidades laborales ante la STPS. |
| **Vencimiento de Certificación** | Opcional | Selector de fecha (`AAAA-MM-DD`) | Fecha calendario | El servidor calcula automáticamente el estatus semáforo: **VIGENTE** (> 30 días), **POR_VENCER** (entre 0 y 30 días) o **VENCIDA** (fecha pasada). |

#### 6.5.4 Procedimientos Paso a Paso (SOPs)

##### SOP 6.5.A — Alta de un Nuevo Montacarguista (con Acceso RF y Certificación DC-3)
1. Ingrese a **Administrar → Montacarguistas** (`/admin/forklift-operators`) y haga clic en **Nuevo montacarguista** (en el encabezado o en la barra de selección).
2. En **Información Personal**, capture **Nombre(s)**, **Apellido Paterno** y **Apellido Materno** (mínimo 2 caracteres en cada campo).
3. En **Asignación Operativa**, elija la **Sucursal Asignada** y el **Turno Operativo de Almacén** (o deje las opciones flexibles por defecto).
4. En **Acceso a Terminal RF / Tableta Operativa**:
   * Mantenga marcada la casilla **Habilitar cuenta de acceso para Terminal RF / Tableta** si el operador utilizará dispositivo móvil en piso.
   * Opcionalmente ingrese un **Usuario para Tableta** personalizado (si lo deja vacío, se usará su código `MC-XXX`).
   * Capture el **PIN / Contraseña Temporal** inicial (mínimo 4 caracteres; por defecto `1234`).
5. En **Certificación STPS / DC-3 (Opcional)** capture el **Número de Licencia DC-3** y la fecha de **Vencimiento de Certificación**.
6. Haga clic en **Guardar Montacarguista**.
7. **Resultado esperado:** El sistema genera automáticamente el código `MC-XXX`, crea la cuenta de acceso RF (si se habilitó), calcula el estatus de la licencia DC-3, muestra la confirmación con el código asignado y abre el registro en modo edición.

##### SOP 6.5.B — Edición y Renovación de Certificación DC-3
1. Despliegue el directorio superior, utilice el filtro **Por Vencer (< 30 días)** o **Licencia Vencida** para localizar a los operadores que requieren actualización y seleccione al montacarguista.
2. Actualice los datos personales, cambie de sucursal o turno si hubo rotación, o ingrese el nuevo **Número de Licencia DC-3** y la nueva fecha de **Vencimiento de Certificación**.
3. Haga clic en **Actualizar Montacarguista**.
4. **Resultado esperado:** El servidor recalcula de inmediato el semáforo de vigencia (`VIGENTE`), actualiza los KPIs de **DC-3 Vigentes** y **Licencias en Alerta**, y registra la modificación en la línea de tiempo de auditoría.

##### SOP 6.5.C — Restablecimiento de PIN Temporal de Tableta RF
1. Seleccione al montacarguista en modo edición.
2. Haga clic en **Restablecer PIN** (disponible tanto en la barra superior del formulario como dentro de la tarjeta **Acceso a Terminal RF / Tableta Operativa**).
3. En el diálogo modal **"Restablecer PIN de Tableta"**, capture el **Nuevo PIN Temporal** (entre 4 y 10 caracteres numéricos o alfanuméricos).
4. Haga clic en **Confirmar y Asignar PIN**.
5. **Resultado esperado:** El PIN temporal queda activo de inmediato y el sistema enciende la bandera de **cambio de contraseña obligatorio**, mostrando en la ficha del operador el aviso amarillo *"Pendiente: El montacarguista debe cambiar su PIN temporal en su primer ingreso."*

##### SOP 6.5.D — Cambio de Estatus Operativo y Baja Lógica
* **Alternar estatus (`ACTIVO` ↔ `INACTIVO`):**
  1. Con el montacarguista abierto en modo edición, haga clic en el botón **Marcar como INACTIVO** (o **Marcar como ACTIVO**) en la esquina inferior izquierda del formulario.
  2. El cambio se aplica de forma inmediata, actualizando la insignia superior, el KPI de *Activos* y la línea de tiempo de auditoría.

* **Confirmar Baja Lógica (Eliminar del catálogo operativo):**
  1. En la parte superior derecha del formulario en modo edición, haga clic en el botón **Eliminar**.
  2. En el diálogo **"Confirmar Baja"**, verifique el nombre y código del montacarguista y lea la advertencia: el registro se marcará como inactivo y dejará de aparecer en las listas de selección en andenes de carga/descarga.
  3. Haga clic en **Sí, Confirmar Baja**.
  4. **Resultado esperado:** El operador se retira del listado activo preservando su historial operativo en el servidor.

##### SOP 6.5.E — Consulta de Auditoría Campo por Campo
1. En modo edición, desplácese a la sección **Información de Control & Auditoría**.
2. Verifique el **Código Operativo**, la **Organización**, el usuario **Registrado Por** y la **Fecha de Registro**.
3. En la **Línea de Tiempo de Auditoría (Trazabilidad WMS)** revise los eventos registrados (`Montacarguista Registrado en Catálogo`, `Actualización de Datos Operativos`, `Cambio de Estatus Operativo`, `Baja Lógica del Operador`) con su usuario `@responsable`, fecha/hora y comparativo campo por campo (`Valor anterior ➔ Valor nuevo`).
4. Utilice el botón **Actualizar bitácora** para recargar el historial directamente desde el servidor.

---

## Capítulo 7. Administración, Gobernanza, Rendimiento, Reglas y Alertas

El presente capítulo documenta la totalidad de los módulos de gobierno corporativo, administración de identidad, control de acceso, supervisión operativa, analítica de rendimiento 3PL, trazabilidad forense, motor de reglas de negocio y configuración de alertas de **4GUARD WMS**. Estas pantallas constituyen el centro de mando estratégico desde el cual la Dirección General, la Gerencia de Operaciones, la Gerencia de Almacén y los Supervisores gobiernan la operación diaria de todos los centros de distribución.

---

### 7.1 Consola de Administración WMS (Admin Hub)

#### Propósito del módulo
La **Consola de Administración WMS** (Admin Hub) es el punto de entrada centralizado que organiza en un tablero visual de tarjetas todos los catálogos maestros, controles de seguridad, herramientas de soporte y consolas de auditoría del ecosistema 4GUARD WMS. Permite navegar de forma ordenada entre las cuatro grandes dimensiones administrativas del sistema sin perder visibilidad del estado de conexión y licencia de la plataforma.

#### Ruta de acceso y roles autorizados
| Atributo | Detalle |
| :--- | :--- |
| **Ruta principal** | `/admin` |
| **Acceso desde menú** | Menú lateral **Administración** |
| **Roles autorizados** | Director General (`ADMIN` / `CEO`), Gerente de Operaciones (`OPERATIONS_MANAGER`), Gerente de Almacén (`WAREHOUSE_MANAGER`) y perfiles con permiso `USERS_READ` o `USERS_CREATE`. |

![Consola de Administración WMS](screenshots/19b-admin-hub.png)

#### Estructura de categorías y tarjetas disponibles
El tablero se divide en **cuatro secciones ejecutivas**, cada una con tarjetas numeradas interactivas que redirigen directamente al submódulo correspondiente (mostrando insignias numéricas cuando existen incidencias activas pendientes):

| Categoría | Tarjeta / Submódulo | Propósito resumido | Ruta destino |
| :--- | :--- | :--- | :--- |
| **1. Estructura de Almacén** (`WMS Catalogs`) | **Organizaciones** | Empresas matrices y holdings raíz (Multi-Tenancy). | `/organizations` |
| **1. Estructura de Almacén** | **Sucursales / Almacenes** | Sedes físicas, zonas horarias y direcciones. | `/branches` |
| **1. Estructura de Almacén** | **ALMACÉN** (Secciones) | Áreas lógicas del almacén (recibo, frío, pasillos). | `/sections` |
| **1. Estructura de Almacén** | **Ubicaciones físicas** | Coordenadas 3D, capacidades y bloqueos de posiciones. | `/layout` |
| **2. Catálogos de Mercancía** (`Stock & Owners`) | **Clientes / Owners** | Dueños de mercancía y stock depositado (3PL). | `/clients` |
| **2. Catálogos de Mercancía** | **PRODUCTOS** (SKUs) | Unidades de medida, pesos y descripciones de stock. | `/skus` |
| **2. Catálogos de Mercancía** | **Transportistas** | Empresas transportistas, capacidades de vehículos y licencias. | `/carriers` |
| **2. Catálogos de Mercancía** | **Proveedores** | Catálogo maestro de proveedores, condiciones operativas y alcance WMS. | `/suppliers` |
| **2. Catálogos de Mercancía** | **Montacarguistas** | Alta, licenciamiento DC-3, asignación de turnos y disponibilidad en andén. | `/admin/forklift-operators` |
| **3. Seguridad y Control de Acceso** (`Identity & Permissions`) | **USUARIOS** | Cuentas de operadores, intentos de acceso y bloqueos. | `/admin/users` |
| **3. Seguridad y Control de Acceso** | **Roles y Matriz de Permisos** | Nivel de jerarquía y matriz de accesos y llamadas a API. | `/roles` |
| **3. Seguridad y Control de Acceso** | **Turnos y Horarios** | Configuración de jornadas operativas, horarios y disponibilidad de almacén. | `/shifts` |
| **3. Seguridad y Control de Acceso** | **Sesiones Activas** | Monitoreo en tiempo real de conexiones de usuario activas. | `/sessions` |
| **4. Monitoreo Técnico y Auditoría** (`Operations & Support`) | **Monitor de Inventario** | Saldos activos por SSCC, cuarentenas preventivas y lotes. | `/inventory-query` |
| **4. Monitoreo Técnico y Auditoría** | **Movimientos de Stock** | Historial inmutable de traslados, recibos y despachos. | Vista interna en `/admin` |
| **4. Monitoreo Técnico y Auditoría** | **Incidencias de Calidad** | Seguimiento a anomalías, mermas y bloqueos (muestra contador de abiertas). | `/quality` |
| **4. Monitoreo Técnico y Auditoría** | **Consola de Auditoría** | Bitácora forense de red y comparador de cambios campo a campo. | `/admin/audit` |
| **4. Monitoreo Técnico y Auditoría** | **Actividad por Usuario** | Consulta, análisis y trazabilidad de acciones realizadas por los usuarios. | `/admin/user-activity` |
| **4. Monitoreo Técnico y Auditoría** | **Historial de Remisiones** | Consulta ejecutiva de recepciones, traspasos y salidas por fecha, cliente y turno. | `/admin/remissions-history` |
| **4. Monitoreo Técnico y Auditoría** | **Alertas y Notificaciones** | Configuración de reglas, umbrales y notificaciones operativas. | `/alerts-config` |
| **4. Monitoreo Técnico y Auditoría** | **Reglas de Negocio** | Motor de reglas de negocio enterprise, políticas operativas y disparadores. | `/business-rules` |

#### Barra técnica de estado (pie de página)
En la parte inferior del Admin Hub se despliega una barra fija de telemetría rápida que muestra:
- **Conexión API**: Indicador de pulso en verde (`Conexión API: Estable`).
- **Licencia**: Estado de suscripción corporativa (`Licencia: Active 3PL Corp`).
- **Versión**: Identificador de plataforma (`4GUARD WMS v4.5.0-GOLDEN • Premium Hub`).

---

### 7.2 Gestión de Usuarios WMS

#### Propósito del módulo
Permite administrar el ciclo de vida completo de las cuentas del personal administrativo, gerencial, de supervisión, calidad y caseta de seguridad: alta de nuevos colaboradores, edición de perfil y sucursal asignada, administración de contraseñas (manual o clave temporal), desbloqueo inmediato de cuentas bloqueadas por intentos fallidos, baja lógica y consulta de la línea de tiempo de auditoría individual.

> **Nota operativa sobre Montacarguistas:** Los operadores de montacargas (`WAREHOUSE_OPERATOR` / `FORKLIFT_OPERATOR` o códigos `@MC-...`) se gestionan en su módulo dedicado **Montacarguistas** (`/admin/forklift-operators`), donde se administra su PIN de terminal RF y certificación STPS DC-3 sin requerir correo ni contraseña web. Si selecciona un usuario vinculado a terminal RF, el sistema mostrará un banner informativo con acceso directo a dicho catálogo.

#### Ruta de acceso y roles autorizados
| Atributo | Detalle |
| :--- | :--- |
| **Rutas de acceso** | `/admin/users` y `/users` |
| **Roles autorizados** | Director General (`ADMIN` / `CEO`), Gerente de Operaciones (`OPERATIONS_MANAGER`), Gerente de Almacén (`WAREHOUSE_MANAGER`) y perfiles con permisos `USERS_READ` / `USERS_CREATE`. |

![Gestión de Usuarios](screenshots/20-users.png)

#### Panel de Métricas (KPIs colapsables)
El encabezado incluye la sección **Métricas de Personal WMS**, que puede alternarse entre vista compacta (barra horizontal) y vista expandida (tarjetas) mediante el botón **Expandir Métricas / Compactar Vista**:

| Indicador | Descripción |
| :--- | :--- |
| **Total Usuarios** | Cantidad total de colaboradores de sistema registrados (excluyendo operadores exclusivos de terminal RF). |
| **Activos** | Cuentas habilitadas con acceso operativo vigente. |
| **Inactivos / Suspendidos** | Cuentas con baja lógica o acceso deshabilitado. |
| **Administradores y Supervisores** | Total de usuarios con roles de mando (Director General, Gerentes, Supervisores, Líderes de Turno y Mesa de Control). |

#### Barra de selección flotante y filtros del directorio
Al hacer clic en la barra **Directorio de Personal WMS — Clic para seleccionar...**, se despliega un panel flotante con los siguientes filtros:

| Filtro | Tipo | Comportamiento |
| :--- | :--- | :--- |
| **Buscador** | Texto libre | Filtra en tiempo real por nombre completo, username (`@usuario`), correo electrónico, rol o sucursal. |
| **Estado** | Selector | `Todos los estados`, `Activos`, `Inactivos`, `Suspendidos`. |
| **Rol** | Selector dinámico | `Todos los roles` o cualquiera de los roles registrados en la base de datos (Director General, Gerente de Operaciones, Coordinador de Operaciones, Supervisor de Operaciones, Mesa de Control, Líder de Turno, Inspector de Calidad, Ayudante de Calidad, Guardia de Seguridad, Ayudante de Seguridad). |
| **Limpiar filtros** | Botón de icono | Restablece los tres filtros simultáneamente cuando alguno está activo. |

#### Campos del formulario y validaciones
El formulario cuenta con una **Tarjeta de Vista Previa en Vivo** en modo alta (que muestra iniciales, nombre, `@username`, correo, rol y sucursal a medida que escribe) y cuatro secciones:

| Sección | Campo | Obligatorio | Validaciones y reglas de negocio |
| :--- | :--- | :---: | :--- |
| **1. Datos de Identidad y Acceso** | **Nombre(s)** | Sí | Máximo 100 caracteres; no admite solo espacios en blanco. |
| **1. Datos de Identidad y Acceso** | **Apellidos** | Sí | Máximo 100 caracteres; no admite solo espacios en blanco. |
| **1. Datos de Identidad y Acceso** | **Nombre de Usuario (Username)** | Sí | Máximo 50 caracteres; formato monoespaciado; sin espacios vacíos. |
| **1. Datos de Identidad y Acceso** | **Correo Electrónico** | Sí | Formato de correo válido; máximo 150 caracteres; se normaliza a minúsculas. |
| **2. Perfil Operativo y Sucursal** | **Rol del Sistema** | Sí | Selección obligatoria del catálogo oficial de roles. |
| **2. Perfil Operativo y Sucursal** | **Sucursal Asignada** | No | Permite elegir una sucursal específica o **Acceso Corporativo (Todas)**. |
| **3. Credenciales Provisionales (Alta)** | **Contraseña Temporal de Primer Acceso** | Automático | El sistema asigna automáticamente `admin123` y exige cambio obligatorio al primer ingreso. |
| **3. Seguridad & Credenciales (Edición)** | **Establecer Contraseña / Generar Clave Temporal / Desbloquear Cuenta** | Acciones | Permite fijar contraseña manual, emitir clave temporal aleatoria o limpiar bloqueos por intentos fallidos. |
| **4. Información de Control & Auditoría (Edición)** | **Metadatos y Línea de Tiempo** | Solo lectura | Muestra `@username` + UUID, Organización, Rol Asignado, Último Inicio de Sesión y cronología inmutable de eventos del usuario con botón **Actualizar Auditoría**. |

#### SOP 7.2.1 — Cómo dar de alta un nuevo usuario
1. Ingrese a **Administración** (`/admin`) y haga clic en la tarjeta **USUARIOS** (o navegue directamente a `/admin/users`).
2. Haga clic en el botón **+ Alta de Usuario** ubicado en la parte derecha de la barra selectora.
3. En la sección **1. Datos de Identidad y Acceso**, capture **Nombre(s)**, **Apellidos**, **Nombre de Usuario (Username)** y **Correo Electrónico**. Verifique en la tarjeta superior de vista previa que los datos sean correctos.
4. En la sección **2. Perfil Operativo y Sucursal**, seleccione el **Rol del Sistema** y la **Sucursal Asignada** (o deje *Acceso Corporativo (Todas)* para perfiles gerenciales).
5. Revise el recuadro informativo de la sección **3. Credenciales Provisionales de Primer Ingreso**, donde se confirma que la contraseña inicial será `admin123`.
6. Haga clic en el botón de guardado al final del formulario.
7. El sistema mostrará una tarjeta verde de confirmación con el botón **Copiar Contraseña** para entregar las credenciales al colaborador. En su primer acceso, el colaborador deberá definir su contraseña definitiva de manera obligatoria.

#### SOP 7.2.2 — Cómo desbloquear una cuenta y restablecer credenciales
Cuando un usuario supera el límite de intentos fallidos de inicio de sesión, su cuenta puede quedar bloqueada por 15 minutos o con contador de intentos fallidos acumulado:
1. Abra la barra **Directorio de Personal WMS**, busque al colaborador por nombre o `@username` y selecciónelo para cargar su ficha en modo edición.
2. Desplácese a la sección **Seguridad & Credenciales WMS**.
3. **Para desbloquear la cuenta**: Verifique si aparece la etiqueta roja con el número de intentos fallidos y haga clic en el botón verde **🔓 Desbloquear Cuenta**. El sistema restablecerá el contador a `0` y levantará inmediatamente cualquier bloqueo activo en el servidor.
4. **Para asignar una contraseña manual definitiva**: Haga clic en **🔑 Establecer Contraseña**, capture y confirme la nueva clave en la ventana modal y confirme. Esta acción también desbloquea la cuenta y la deja en estado `ACTIVE`.
5. **Para emitir una contraseña temporal aleatoria**: Haga clic en **⚡ Generar Clave Temporal**, confirme en el cuadro de diálogo y copie la clave mostrada en la ventana emergente para compartirla con el usuario.
6. Para verificar que la acción quedó auditada, haga clic en **Actualizar Auditoría** en la esquina superior derecha de la ficha.

---

### 7.3 Roles y Matriz de Permisos (RBAC)

#### Propósito del módulo
Permite diseñar y gobernar el modelo de **Control de Acceso Basado en Roles (RBAC)** de 4GUARD WMS. Desde esta consola el administrador define perfiles de autoridad con niveles jerárquicos del 1 al 7, asigna o revoca permisos granulares agrupados por módulo operativo y consulta el historial de cambios de cada rol.

#### Ruta de acceso y roles autorizados
| Atributo | Detalle |
| :--- | :--- |
| **Rutas de acceso** | `/roles` y `/admin/roles` |
| **Roles autorizados** | Director General (`ADMIN` / `CEO`), Gerente de Operaciones (`OPERATIONS_MANAGER`) y Gerente de Almacén (`WAREHOUSE_MANAGER`). |

![Roles y Matriz de Permisos](screenshots/21-roles.png)

#### Panel de Métricas (KPIs colapsables)
| Indicador | Descripción |
| :--- | :--- |
| **Total Roles** | Número total de perfiles configurados en la matriz RBAC. |
| **Roles de Sistema** | Perfiles protegidos nativos de la plataforma (identificados con candado; no pueden eliminarse). |
| **Roles Personalizados** | Perfiles creados a la medida por la organización (editables y eliminables). |

#### Directorio de roles, filtros y paginación
Al abrir el selector **Directorio de Roles — Clic para seleccionar...**, dispone de:
- **Buscador**: Filtra por nombre de rol o número de nivel jerárquico.
- **Filtro de Tipo de Rol**: `Todos los roles`, `Roles de Sistema (Protegidos)`, `Roles Personalizados`.
- **Paginación**: Selector de tamaño de página (`10`, `30`, `50` registros por página) y controles **Página anterior / Página siguiente**.
- Cada elemento de la lista muestra iniciales, nombre del rol, etiqueta de nivel (`Nivel 1` a `Nivel 7`), insignia (`Sistema` o `Personalizado`) y contador de permisos asignados.

#### Campos del formulario, niveles jerárquicos y matriz de permisos
| Sección | Campo / Control | Obligatorio | Descripción y reglas |
| :--- | :--- | :---: | :--- |
| **Información del Rol** | **Nombre del Rol** | Sí | Máximo 100 caracteres; se almacena en mayúsculas (ej. `OPERATIONS_MANAGER`); no admite solo espacios. |
| **Información del Rol** | **Nivel Jerárquico de Autorización** | Sí | Escala del **1 (Máximo)** al **7 (Mínimo)**:<br>• **Nivel 1**: Administrador General (Acceso Total)<br>• **Nivel 2**: Gerencia / Operaciones<br>• **Nivel 3**: Supervisión de Almacén<br>• **Nivel 4**: Operador de Almacén / Montacargas<br>• **Nivel 5**: Control de Calidad / Auditoría<br>• **Nivel 6**: Cliente Depositante (Solo lectura)<br>• **Nivel 7**: Consulta Básica |
| **Matriz de Permisos del Sistema** | **Acciones globales** | — | Botones **Seleccionar todos** y **Desmarcar todos**, más contador en vivo de permisos seleccionados. |
| **Matriz de Permisos del Sistema** | **Tarjetas por Módulo WMS** | — | Casilla maestra por grupo de módulo (selecciona o desmarca todos los permisos del bloque) y casillas individuales por permiso mostrando código técnico y descripción funcional. |
| **Información de Control y Auditoría BE** | **Metadatos y Línea de Tiempo** | Solo lectura (edición) | ID de Sistema (8 dígitos con UUID completo en tooltip), Código de Rol, Fecha de creación, Última actualización, Versión de Registro (`v1`, `v2`...) y Línea de Tiempo de Auditoría con comparador de cambios (`Valor anterior ➔ Valor nuevo`). |

#### SOP 7.3.1 — Cómo configurar un rol y su matriz de permisos RBAC
1. Acceda a **Roles y Matriz de Permisos (RBAC)** desde el Admin Hub o navegando a `/roles`.
2. Para crear un perfil nuevo, haga clic en **+ Nuevo Rol**. Para ajustar uno existente, abra el **Directorio de Roles** y seleccione el rol deseado.
3. En la sección **Información del Rol**, escriba el **Nombre del Rol** y elija su **Nivel Jerárquico de Autorización (1 a 7)**.
4. En la sección **Matriz de Permisos del Sistema**, active los permisos necesarios:
   - Use **Seleccionar todos** o **Desmarcar todos** para partir de una base limpia.
   - Marque la casilla en el encabezado de una tarjeta de módulo para otorgar acceso completo a ese módulo, o marque permisos individuales según el principio de mínimo privilegio.
5. Haga clic en **Crear Rol** (en alta) o **Guardar cambios** (en edición).
6. Si necesita eliminar un rol personalizado, selecciónelo, haga clic en **Desactivar / Eliminar** y confirme en el cuadro de diálogo **Eliminar Rol de la Matriz RBAC** (*nota: los usuarios que tenían dicho rol deberán ser reasignados a otro perfil*). Los **Roles de Sistema** tienen este botón deshabilitado por seguridad.

---

### 7.4 Gestión de Organizaciones (Multi-Tenancy)

#### Propósito del módulo
Administra el nivel jerárquico superior de la arquitectura **Multi-Tenancy 3PL** de 4GUARD WMS: empresas matrices, holdings y razones sociales bajo las cuales operan las sucursales, clientes depositantes y usuarios. Garantiza el aislamiento lógico de datos entre distintas organizaciones.

#### Ruta de acceso y roles autorizados
| Atributo | Detalle |
| :--- | :--- |
| **Rutas de acceso** | `/organizations` y `/admin/organizations` |
| **Roles autorizados** | Director General (`ADMIN` / `CEO`), Gerente de Operaciones (`OPERATIONS_MANAGER`) y Gerente de Almacén (`WAREHOUSE_MANAGER`). |

![Gestión de Organizaciones](screenshots/22-organizations.png)

#### Panel de Métricas (KPIs colapsables)
| Indicador | Descripción |
| :--- | :--- |
| **Total** | Total de organizaciones registradas en la plataforma. |
| **Activos** | Organizaciones operando normalmente (`ACTIVE`). |
| **Suspendidos** | Organizaciones suspendidas o inactivas (`SUSPENDED` / `INACTIVE`). |
| **Modalidades** | Cantidad de tipos de operación distintos registrados. |

#### Filtros del directorio y campos del formulario
- **Filtros del directorio desplegable**: Buscador libre (razón social, RFC o código), selector de **Estado** (`Activos`, `Suspendidos`, `Inactivos`) y selector de **Tipo de Organización**.
- **Campos del formulario**:

| Sección | Campo | Obligatorio | Validaciones y opciones |
| :--- | :--- | :---: | :--- |
| **Identificación & Configuración Fiscal** | **Razón Social / Nombre** | Sí | Máximo 100 caracteres; sin espacios vacíos. Ej. `4GUARD LOGISTICS CORP`. |
| **Identificación & Configuración Fiscal** | **Código Identificador** | Sí | Máximo 50 caracteres; se guarda en mayúsculas (ej. `4GUARD-HQ`). **Es inmutable en modo edición.** |
| **Identificación & Configuración Fiscal** | **Tax ID / RFC Fiscal** | No | Máximo 30 caracteres; se guarda en mayúsculas (ej. `MX-99887766-A`). |
| **Modalidad de Operación** | **Tipo de Organización** | Sí | • `Operador Logístico (3PL)`<br>• `Almacén / Depósito`<br>• `Centro de Distribución`<br>• `Planta de Manufactura`<br>• `Comercio Retail`<br>• `Terceros (External Owner)` |

#### Acciones de gestión y auditoría
En modo edición, la barra de acciones permite **Activar** o **Suspender** la organización (con cambio inmediato de estado operativo), **Eliminar** (previa confirmación en modal) y consultar la **Línea de Tiempo de Auditoría** con el detalle de cada campo modificado (`Nombre`, `Código`, `RFC`, `Tipo`, `Estado Operativo`).

#### SOP 7.4.1 — Cómo registrar o suspender una organización
1. Ingrese a **Gestión de Organizaciones** (`/organizations`).
2. Para crear una nueva, haga clic en **+ Nueva organización**, capture la **Razón Social**, el **Código Identificador** (recuerde que no podrá modificarse después), el **Tax ID / RFC Fiscal** y la **Modalidad de Operación**, y presione **Crear organización**.
3. Para suspender temporalmente una organización existente, selecciónela desde el **Directorio de Organizaciones** y haga clic en **Suspender** (o en **Activar** para rehabilitarla).

---

### 7.5 Gestión de Sucursales (Branches)

#### Propósito del módulo
Permite administrar la red física de centros de distribución (CEDIS), almacenes centrales y bodegas regionales vinculadas a una organización, estableciendo su código operativo, dirección física y **zona horaria oficial** (fundamental para la precisión de marcas de tiempo en caseta, turnos, auditoría y reportes).

#### Ruta de acceso y roles autorizados
| Atributo | Detalle |
| :--- | :--- |
| **Rutas de acceso** | `/branches` y `/admin/branches` |
| **Roles autorizados** | Director General (`ADMIN` / `CEO`), Gerente de Operaciones (`OPERATIONS_MANAGER`) y Gerente de Almacén (`WAREHOUSE_MANAGER`). |

![Gestión de Sucursales](screenshots/23b-branches-real.png)

#### Panel de Métricas (KPIs colapsables)
| Indicador | Descripción |
| :--- | :--- |
| **Total Registradas** | Total de sucursales/CEDIS dados de alta. |
| **Sucursales Activas** | Centros logísticos habilitados para operación. |
| **Sucursales Inactivas** | Sedes suspendidas o fuera de operación. |

#### Filtros y campos del formulario
- **Filtros del directorio desplegable**: Buscador libre (nombre, código, organización, dirección o zona horaria) y selector de **Estado** (`Todos los estados`, `Activas`, `Inactivas`).
- **Campos del formulario**:

| Sección | Campo | Obligatorio | Validaciones y opciones |
| :--- | :--- | :---: | :--- |
| **Información General** | **Nombre de Sucursal** | Sí | Máximo 150 caracteres. Ej. `Sucursal Norte Querétaro`. |
| **Información General** | **Código de Sucursal** | Sí | Máximo 50 caracteres; único en el sistema; se guarda en mayúsculas (ej. `SUC-NORTE`). |
| **Información General** | **Organización Perteneciente** | Sí | Selector vinculado al catálogo de Organizaciones. |
| **Ubicación y Configuración** | **Dirección Principal** | Sí | Máximo 300 caracteres. Ej. `Av. de la Industria 500, Parque Industrial Querétaro`. |
| **Ubicación y Configuración** | **Zona Horaria** | Sí | • `Centro (CDMX / MTY / GDL)` (`America/Mexico_City`)<br>• `Noreste (Monterrey)` (`America/Monterrey`)<br>• `Pacífico (Tijuana)` (`America/Tijuana`)<br>• `Sonora (Hermosillo)` (`America/Hermosillo`)<br>• `Sureste (Cancún)` (`America/Cancun`)<br>• `Tiempo Universal (UTC)` (`UTC`) |
| **Información de Control y Auditoría BE** | **Metadatos y Línea de Tiempo** | Solo lectura (edición) | ID de Sistema (8 dígitos), Código, Fechas de creación/actualización, Versión y bitácora detallada de cambios. |

#### Acciones disponibles en edición
En modo edición, puede **Suspender** / **Activar** la sucursal o hacer clic en **Desactivar** para eliminarla permanentemente, ambos procesos protegidos por un cuadro de diálogo de confirmación.

---

### 7.6 Configuración de Turnos y Horarios

#### Propósito del módulo
Permite definir las jornadas operativas del centro de distribución, controlar la cobertura continua de 24 horas mediante un **Mapa Visual tipo Gantt**, detectar automáticamente conflictos de solapamiento de horarios, configurar esquemas de horario unificado o rotativo por día, y establecer ventanas de **descanso, tolerancia de entrada y horario de comida con anticipación de pausa en terminales RF**.

#### Ruta de acceso y roles autorizados
| Atributo | Detalle |
| :--- | :--- |
| **Rutas de acceso** | `/shifts` (y redirección desde `/admin/shifts`) |
| **Roles autorizados (Edición)** | Director General (`ADMIN` / `CEO`), Gerente de Operaciones (`OPERATIONS_MANAGER`), Gerente de Almacén (`WAREHOUSE_MANAGER`), Supervisor de Operaciones (`OPERATIONS_SUPERVISOR`) y perfiles con permiso `SHIFTS_WRITE`. |
| **Roles autorizados (Solo lectura)** | **Líder de Turno (`SHIFT_LEADER`)**: visualiza el banner informativo *Modo Solo Lectura* y puede consultar turnos, horarios y Gantt 24h sin modificar parámetros. |

![Turnos y Horarios](screenshots/24-shifts.png)

#### Panel de Métricas (KPIs colapsables)
| Indicador | Descripción |
| :--- | :--- |
| **Total Turnos** | Cantidad de turnos configurados para la sucursal activa. |
| **Turnos Activos** | Jornadas vigentes consideradas en el cronograma de 24 horas. |
| **Sucursal Activa** | Nombre del CEDIS al que pertenecen los turnos visualizados. |
| **Conflictos de Horario** | Contador automático de solapamientos detectados entre turnos activos. |

#### Mapa Visual de Cobertura 24 Horas (Gantt Operativo)
Ubicado encima del formulario (plegable con el botón **Ocultar Cronograma / Ver Cronograma**), grafica en una regla horaria de `00:00` a `24:00` (con marcas cada 4 horas) todos los turnos activos de la sucursal:
- **Turnos diurnos**: Se muestran como una barra continua proporcional a su horario.
- **Turnos nocturnos (cruzan medianoche)**: Se dividen automáticamente en dos segmentos (ej. `22:00–24:00` y `00:00–06:00`) marcados con el ícono de luna (`Nocturno`).
- **Alertas visuales**: Si dos turnos se solapan, las barras exhiben el indicador de advertencia (`Conflicto`).
- **Navegación directa**: Hacer clic sobre cualquier barra del Gantt carga inmediatamente ese turno en el editor inferior.

#### Campos del formulario y validaciones
| Sección | Campo | Obligatorio | Validaciones y comportamiento |
| :--- | :--- | :---: | :--- |
| **1. Identificación del Turno** | **Código de Turno** | Sí | Máximo 20 caracteres; solo letras, números, guion y guion bajo (ej. `TRN-MAT-01`). |
| **1. Identificación del Turno** | **Nombre Oficial del Turno** | Sí | Máximo 100 caracteres (ej. `Turno Matutino Principal`). |
| **1. Identificación del Turno** | **Descripción Operativa** | No | Máximo 250 caracteres. |
| **2. Horario y Días de Operación** | **Esquema de Horarios** | Sí | Conmutador 50/50 entre **Horario Unificado** (mismo horario todos los días) y **Horarios por Día** (días intercalados / rotativos). |
| **2. Horario Unificado** | **Hora de Inicio (24H)** y **Hora de Término (24H)** | Sí | Formato estricto `HH:MM` (00:00–23:59). Incluye selector visual 24H con accesos rápidos (`06:00`, `08:00`, `14:00`, `17:00`, `19:00`, `20:00`, `22:00`, `03:00`). No pueden ser idénticas. Calcula automáticamente la **Duración Neta** y detecta **Cruza medianoche**. |
| **2. Horario Unificado** | **Días Operativos de la Semana** | Sí | Botones interactivos para los 7 días (`L` a `D`); exige al menos 1 día activo. |
| **2. Horarios por Día** | **Interruptor, Inicio y Término por día** | Sí | Permite habilitar/deshabilitar cada día individualmente, definir horarios distintos por día, visualizar la duración diaria y el **Total Semanal**, y usar **Copiar a activos** para replicar el horario de un día a los demás días activos. |
| **3. Parámetros Operativos y Tolerancia** | **Minutos de Descanso** | Sí | Valor numérico de `0` a `300` min (predeterminado: `60`). |
| **3. Parámetros Operativos y Tolerancia** | **Tolerancia de Entrada (min)** | Sí | Valor numérico de `0` a `120` min (predeterminado: `10`). |
| **4. Horario de Comida / Colación Programada** | **Habilitar Horario de Comida** | No | Casilla de activación. Si se habilita, exige **Inicio de Comida**, **Término de Comida** (formato 24H) y **Anticipación de Pausa RF** (`5`, `10` [Recomendado], `15`, `20` o hasta `60` minutos antes para pausar la asignación de nuevas tareas en terminal RF). |

#### SOP 7.6.1 — Cómo configurar un turno (Horario Unificado vs. Horarios por Día) y validar cobertura en el Gantt 24h
1. Ingrese a **Configuración de Turnos y Horarios** (`/shifts`).
2. Haga clic en **+ Nuevo Turno** en la barra selectora (si tiene cambios sin guardar en otro turno, el sistema mostrará un diálogo de confirmación para evitar pérdida de datos).
3. En **Identificación del Turno**, capture el **Código de Turno**, el **Nombre Oficial** y una **Descripción Operativa**.
4. En **Horario y Días de Operación**, elija el esquema requerido:
   - **Opción A — Horario Unificado**: Mantenga seleccionada la pestaña **Horario Unificado**, capture o elija mediante el ícono de reloj (`Selector 24H`) la **Hora de Inicio** y la **Hora de Término** (por ejemplo `22:00` a `06:00`; el sistema marcará automáticamente *Cruza medianoche* y calculará la duración neta), y active los **Días Operativos** deseados.
   - **Opción B — Horarios por Día (Rotativo / Intercalado)**: Seleccione la pestaña **Horarios por Día**, active el interruptor de cada día laborable y defina la hora de entrada y salida de cada jornada (por ejemplo, Lunes a Miércoles de `08:00` a `20:00` y Jueves nocturno de `22:00` a `06:00`). Si desea homologar varios días, use el botón **Copiar a activos** en la fila de referencia y verifique el **Total Semanal**.
5. Ajuste los **Minutos de Descanso** y la **Tolerancia de Entrada** en la sección 3.
6. En la sección 4, si el turno contempla comida programada, marque **Habilitar Horario de Comida Programado**, establezca la ventana horaria y seleccione la **Anticipación de Pausa RF** (recomendado: `10 minutos antes`).
7. Guarde el turno y verifique en el **Mapa Visual de Cobertura 24 Horas** superior que la nueva barra aparezca sin el indicador de **Conflicto**.

---

### 7.7 Sesiones Activas y Control de Horas Trabajadas

#### Propósito del módulo
Proporciona visibilidad en tiempo real sobre quién está conectado actualmente a 4GUARD WMS —con capacidad de revocación inmediata de sesiones ante incidentes de seguridad— y, de manera exclusiva para la **Dirección General**, ofrece el tablero **Control de Jornadas & Horas Trabajadas** con cálculo exacto de tiempo efectivo laborado y deducción automática de **-15 minutos por cada desconexión por inactividad**.

#### Ruta de acceso y roles autorizados
| Pestaña | Ruta | Roles autorizados |
| :--- | :--- | :--- |
| **Conexiones en Vivo** | `/sessions` y `/admin/sessions` | Director General (`ADMIN` / `CEO`), Gerente de Operaciones (`OPERATIONS_MANAGER`) y perfiles autorizados por el backend según su alcance. |
| **Horas Trabajadas por Usuario** | Pestaña 2 en `/sessions` | **Exclusivo Dirección General (`ADMIN` / `CEO`)**. La pestaña permanece oculta y bloqueada para cualquier otro rol. |

![Sesiones Activas](screenshots/25-sessions.png)

#### Pestaña 1: Conexiones en Vivo
Muestra el contador total de conexiones activas, el botón **Actualizar** y una tarjeta por cada usuario autenticado con la siguiente información:
- **Identidad**: Avatar con iniciales, nombre completo, insignia verde pulsante `Online`, rol oficial traducido y correo electrónico.
- **Ubicación**: Nombre de la sucursal/CEDIS y organización.
- **IP / Nodo**: Dirección IP de origen de la conexión autenticada.
- **Dispositivo**: Navegador y sistema operativo detectados (ej. *Chrome en Windows*, *Edge en Windows*, *Safari en iOS*) con etiqueta *Sesión segura JWT*.
- **Última conexión y acción**: Fecha/hora de inicio de sesión y botón rojo **Revocar**.

#### Pestaña 2: Horas Trabajadas por Usuario (Exclusivo Dirección General)
##### KPIs ejecutivos de jornada
| Tarjeta KPI | Descripción |
| :--- | :--- |
| **TOTAL HORAS HOMBRE** | Suma acumulada de tiempo neto efectivo laborado en la fecha consultada (`Xh YYm`). |
| **PERSONAL CON ASISTENCIA** | Relación de usuarios con registro de inicio de sesión frente al total filtrado (`Activos / Total`). |
| **PROMEDIO POR JORNADA** | Tiempo efectivo promedio por colaborador activo (referencia: *Meta estándar: 8h 00m*). |
| **EXPULSIONES POR INACTIVIDAD** | Total de cierres automáticos por timeout (`-15 min` deducidos por cada evento). |

##### Filtros y controles
- **Selector de Jornada**: Presets rápidos **Hoy** y **Ayer**, más selector de fecha de calendario.
- **Buscador con atajo `Ctrl+K` / `⌘K`**: Filtra por colaborador, correo, rol o sucursal.
- **Pills de filtro por área/rol**: `Todos los roles`, `Operaciones / Gerencia`, `Mesa de Control`, `Operadores / Montacargas`, `Calidad (QM)`, `Caseta & Vigilancia`.
- **Acciones de cabecera**: Botón dorado **Exportar a Excel (CSV)** y botón **Recalcular**.

##### Desglose expandible de intervalos por colaborador
Cada tarjeta de usuario muestra **Primer Ingreso**, **Última Salida** (o *Activo ahora*), **Sesiones**, **Horas Efectivas** (con ícono de paloma si cumple la meta de 8 horas) y alerta de salidas por inactividad. Al hacer clic sobre la fila, se despliega la tabla **Bitácora de Sesiones e Intervalos de Conexión** con las columnas: `#`, `Hora Inicio`, `Hora Término`, `Tiempo Bruto`, `Deducción Inactividad`, `Tiempo Neto Computado`, `Motivo de Cierre` (`EN LÍNEA`, `LOGOUT MANUAL`, `INACTIVIDAD (-15m)`, `REVOCADO POR ADMIN`) y `Nodo / IP`.

#### SOP 7.7.1 — Cómo revocar una sesión activa
1. Navegue a **Sesiones Activas** (`/sessions`) y asegúrese de estar en la pestaña **Conexiones en Vivo**.
2. Localice la tarjeta del colaborador cuya sesión desea terminar (puede verificar su IP, dispositivo y última conexión).
3. Haga clic en el botón **Revocar** situado en el extremo derecho de la tarjeta.
4. En la ventana de confirmación **¿Revocar sesión de usuario?**, verifique el nombre y correo del usuario y haga clic en **Sí, revocar sesión**.
5. El sistema invalidará el token de inmediato, expulsará al usuario a la pantalla de inicio de sesión y lo retirará del monitor en vivo.

#### SOP 7.7.2 — Cómo auditar y exportar el reporte de Horas Trabajadas (Dirección General)
1. Inicie sesión con una cuenta de **Dirección General (`ADMIN` / `CEO`)** e ingrese a `/sessions`.
2. Haga clic en la pestaña **Horas Trabajadas por Usuario** (etiqueta `DIRECCIÓN GENERAL`).
3. Seleccione la fecha deseada usando **Hoy**, **Ayer** o el selector de calendario.
4. Opcionalmente, filtre por área usando los pills de rol o presione `Ctrl+K` (`⌘K`) para buscar a un colaborador específico.
5. Para auditar deducciones de inactividad de un colaborador, haga clic en su fila para desplegar la tabla de intervalos y revisar los cierres `INACTIVIDAD (-15m)`.
6. Haga clic en **Exportar a Excel (CSV)** en la esquina superior derecha. Se descargará el archivo `Reporte_Horas_Trabajadas_4GUARD_AAAA-MM-DD.csv` (codificado en UTF-8 con BOM para abrir correctamente acentos en Excel) con las 14 columnas oficiales: *Usuario, Nombre Completo, Correo, Rol Oficial, Sucursal, Fecha, Estatus Actual, Primer Ingreso, Última Salida, Minutos Netos, Horas Trabajadas, Sesiones Iniciadas, Expulsiones por Inactividad (-15m c/u)* y *Meta Jornada (>=8h)*.

---

### 7.8 Consola de Auditoría

#### Propósito del módulo
Es la bitácora forense e inmutable de 4GUARD WMS. Registra cada creación, modificación, eliminación, acceso y llamada crítica ejecutada en el sistema, permitiendo rastrear al operador responsable, la entidad afectada, la dirección IP, el navegador (User Agent) y un **Comparador de Cambios** campo por campo (`Antes` vs. `Después`).

#### Ruta de acceso y roles autorizados
| Atributo | Detalle |
| :--- | :--- |
| **Ruta de acceso** | `/admin/audit` |
| **Roles autorizados** | Director General (`ADMIN` / `CEO`), Gerente de Operaciones (`OPERATIONS_MANAGER`), Gerente de Almacén (`WAREHOUSE_MANAGER`) y perfiles con acceso a `admin`. |

![Consola de Auditoría](screenshots/25b-admin-audit.png)

#### Panel de Métricas (6 KPIs)
| Indicador | Descripción |
| :--- | :--- |
| **EVENTOS** | Total de registros de auditoría que coinciden con los filtros activos. |
| **USUARIOS ÚNICOS** | Cantidad de operadores distintos presentes en el rango consultado. |
| **CREACIONES** | Eventos de alta o registro nuevo. |
| **MODIFICACIONES** | Eventos de actualización de parámetros o estatus. |
| **ELIMINACIONES** | Eventos de baja o eliminación. |
| **ERRORES / RECHAZOS** | Eventos cuyo detalle registra resultado `FAILED` o `REJECTED`. |

#### Filtros, tabla de resultados y Drawer comparador
- **Filtros**: Presets temporales (**Hoy**, **Este mes** [predeterminado], **Este año**, **Personalizado** con campos `Desde` y `Hasta`), selector de **Acción**, selector de **Entidad**, **Buscador** (operador, acción, entidad, ID o IP) y botones **Filtrar**, **Limpiar** y **Actualizar**.
- **Columnas de la tabla**: `Fecha / Hora`, `Operador`, `Entidad`, `ID Entidad`, `Acción` (chip de color semántico), `Dirección IP` y `Detalle` (botón de comparador). Incluye paginación de `20`, `30` o `50` registros por página.
- **Panel lateral de detalle (Drawer)**: Al hacer clic en cualquier fila se despliega el panel **Detalle de evento**, que muestra la etiqueta `REVISAR` si hubo fallo/rechazo, la ficha de metadatos (`Operador`, `Entidad`, `ID Entidad`, `Fecha`, `Dirección IP`, `User Agent`) y la tabla **Comparador de cambios** con tres columnas (`Campo`, `Antes` en rojo y `Después` en verde).

---

### 7.9 Historial de Remisiones y Turnos

#### Propósito del módulo
Proporciona una vista ejecutiva multi-entidad e inmutable de todas las **Recepciones (Inbound)**, **Salidas (Despacho)** y **Traspasos entre Almacenes**, consolidados por cliente, estatus y turno operativo. Permite inspeccionar el expediente completo de cada movimiento en un modal emergente de tres pestañas y descargar el **Formato Oficial F01 en PDF**.

#### Ruta de acceso y roles autorizados
| Atributo | Detalle |
| :--- | :--- |
| **Ruta de acceso** | `/admin/remissions-history` |
| **Roles autorizados** | Director General (`ADMIN` / `CEO`), Gerente de Operaciones (`OPERATIONS_MANAGER`) y Gerente de Almacén (`WAREHOUSE_MANAGER`). |

#### Panel de Métricas (5 KPIs)
| Indicador | Descripción |
| :--- | :--- |
| **TOTAL REMISIONES** | Registros consolidados en el periodo consultado. |
| **VERIFICADAS (COMPLETADAS)** | Movimientos aprobados con descarga o despacho conforme. |
| **CANCELADAS / ANULADAS** | Movimientos anulados con justificación en bitácora de auditoría. |
| **PENDIENTES EN CURSO** | Unidades aún en caseta, patio o proceso de andén. |
| **CARGA EN VISTA** | Suma de **tarimas** y **piezas totales** de los registros mostrados en la página actual. |

#### Filtros y columnas de la tabla
- **Filtros**: Presets de fecha (**Hoy**, **Este mes**, **Este año**, **Personalizado** con `Desde` y `Hasta`), **Cliente / Depositante**, **Estatus histórico** (`Verificado (Completado)`, `Cancelado`), **Tipo** (`Recepción (Inbound)`, `Salida (Despacho)`, `Traspaso (Entre Almacenes)`), **Búsqueda libre** (folio, remisión, cliente u operador) y botones **Filtrar**, **Limpiar** y **Actualizar Datos**.
- **Columnas**: `Fecha y Hora`, `Tipo`, `Folio` (con botón rápido de copiado), `No. Remisión` (con botón rápido de copiado), `Cliente / Depositante`, `Turno Operativo`, `Carga Auditada` (tarimas / piezas), `Operador`, `Ciclo` (duración calculada), `Estatus` y botón **Ver Expediente**. Paginación de `20`, `30` o `50` registros por página.

#### Expediente Oficial en Modal Pop-up (3 pestañas y Formato F01)
Al hacer clic en cualquier fila o en **Ver Expediente**, se abre la ventana centrada con tres pestañas y acciones de exportación:
1. **Línea de Tiempo & Auditoría**: Tarjetas resumen (*Remisión/Factura*, *Carga Total*, *Cliente*, *Tiempo de Ciclo*), banner rojo de motivo de cancelación (si aplica) y línea de tiempo de 4 hitos (*1. Registro de Ingreso en Caseta*, *2. Consolidación de Jornada y Turno*, *3. Verificación de Carga y Conteo Físico*, *4. Resolución Final del Ciclo*).
2. **Tabla de Partidas & Tarimas**: Desglose granular por tarima con `#`, `Código UA (SSCC)`, `Descripción / SKU`, `No. Lote`, `Caducidad`, `Piezas`, `Ubicación` y `Estatus`, con fila de totales consolidados.
3. **Metadatos & Seguridad WMS**: Ficha inmutable con UUID, Folio, Remisión, Cliente, Sucursal, Operador, Fechas de registro y cierre, ID de Sesión de Turno y Tipo de Movimiento.
4. **Acciones del expediente**: Botón **Descargar Formato F01** (genera el archivo `Formato_F01_[FOLIO].pdf`) y botón **Copiar Resumen para Auditoría**.

---

### 7.10 Monitoreo de Rendimiento (Inteligencia Operativa 3PL)

#### Propósito del módulo
El **Módulo de Rendimiento** es la suite analítica integral de 4GUARD WMS para supervisar en tiempo real la productividad del almacén, el cumplimiento de acuerdos de nivel de servicio (SLAs / OTIF), el desempeño de montacarguistas por turno, la rotación de la flota propia (**Circuito Delicado**), el catálogo formal de reglas de KPIs y la descarga de los **4 libros oficiales de Excel 2026**.

#### Ruta de acceso, barra global y roles autorizados
| Atributo | Detalle |
| :--- | :--- |
| **Ruta base** | `/performance` (redirige por defecto a `/performance/dashboard`) |
| **Roles autorizados** | Director General (`ADMIN` / `CEO`), Gerente de Operaciones (`OPERATIONS_MANAGER`), Gerente de Almacén (`WAREHOUSE_MANAGER`), Coordinador de Operaciones (`OPERATIONS_COORDINATOR`) y perfiles con permiso `PERFORMANCE_READ` o `REPORTS_READ`. |

La cabecera unificada del módulo está presente en todas sus pestañas y ofrece:
- **Selector de Sucursal**: `Todas las Sucursales`, `CEDIS Central — Cuautitlán`, `Almacén Regional Guadalajara`, `Almacén Monterrey Apodaca`.
- **Selector de Rango Temporal**: `Hoy (Tiempo Real)`, `Últimos 7 días`, `Mes Actual`, `Rango Personalizado`.
- **Auto-actualización en vivo**: Insignia con cuenta regresiva (`Auto: 60s`) y botón de refresco manual.
- **Botón Ajustar Metas**: Abre el modal de configuración dinámica de SLAs y objetivos.
- **Botón Exportar Excel**: Encola y descarga el reporte general de rendimiento.
- **Barra de 5 pestañas**: *Control General & Procesos*, *Productividad & Turnos*, *Circuito Delicado (Flota Propia)*, *Catálogo & Reglas de KPIs* y *Reportes Oficiales & KPIs 2026*.

---

#### 7.10.1 Control General & Procesos (`/performance/dashboard`)

![Monitoreo de Rendimiento](screenshots/27-performance.png)

Presenta cuatro bloques analíticos en tiempo real:
1. **5 Macro KPIs Ejecutivos con semáforo dinámico**:
   - **Ocupación Almacén (%)**: Porcentaje ocupado, barra visual con línea de meta y espacio disponible restante.
   - **Precisión Inventario (IRA %)**: Confiabilidad en conteos cíclicos frente a meta mínima.
   - **Nivel de Servicio (OTIF %)**: Órdenes entregadas a tiempo y completas.
   - **Dock-to-Stock (hrs)**: Tiempo promedio desde el andén de descarga hasta ubicación en rack.
   - **Ciclo de Orden (hrs)**: Tiempo promedio desde liberación de pedido hasta despacho final.
   *(Cada tarjeta incluye un botón de acceso directo `Meta: ...` para abrir el modal de ajuste de objetivos).*
2. **Evolución de Throughput Operativo (Jornada de 24 Horas)**: Gráfica interactiva en vivo con conmutador entre modo **Tendencia** (curva suave con área degradada) y modo **Desglose** (barras apiladas de *Inbound* y *Outbound*), línea punteada de meta horaria, tooltip interactivo al pasar el cursor (mostrando tarimas inbound, órdenes outbound, throughput total en `mov/h` y desviación porcentual vs. meta) y 4 contadores inferiores (*Recepciones Inbound*, *Despachos Outbound*, *Movimientos Hoy* e *Incidencias Activas*).
3. **Tiempos de Procesos Nodales (`T₀ ➔ T₁`) y Candados de Calidad**: Comparativa en barras de tiempo real vs. estándar por proceso, anillo de **Ciclo Puerta a Puerta (Caseta QR ➔ Salida)** con desglose de etapas (`QR`, `Rampa`, `Salida`) y estado de los candados obligatorios (*Checklist F01 de 17 Criterios* y *Tolerancia de Peso Packing ±2%*).
4. **Monitoreo de Andenes y Rampas de Descarga**: Tarjetas compactas por rampa mostrando estatus (`ACTIVA`), cantidad de descargas y estadía promedio en minutos contra la meta de descarga inbound.

#### SOP 7.10.1 — Cómo ajustar las metas y objetivos de rendimiento
1. Desde cualquier pestaña del Módulo de Rendimiento, haga clic en el botón **Ajustar Metas** en la barra superior (o en la etiqueta `Meta:` de cualquier tarjeta KPI).
2. Se abrirá el modal **Configuración Dinámica de Metas y Objetivos Operativos**, dividido en dos secciones con **7 controles deslizantes**:

| Sección | Parámetro de Meta | Rango permitido | Paso | Referencia recomendada |
| :--- | :--- | :---: | :---: | :--- |
| **1. SLAs y Capacidad de Almacén** | **Meta Ocupación Máxima (%)** | 50% – 95% | 1% | 60% – 85% para evitar saturación |
| **1. SLAs y Capacidad de Almacén** | **Meta Exactitud de Inventario IRA (%)** | 90% – 100% | 0.1% | ≥ 99.5% (Estándar 3PL) |
| **1. SLAs y Capacidad de Almacén** | **Meta Cumplimiento a Tiempo (OTIF %)** | 85% – 100% | 0.5% | ≥ 98.0% |
| **2. Tiempos de Ciclo & Productividad** | **Meta Dock-to-Stock (Horas)** | 0.5 – 6.0 hrs | 0.1 h | ≤ 2.0 hrs |
| **2. Tiempos de Ciclo & Productividad** | **Meta Ciclo de Orden (Horas)** | 1.0 – 10.0 hrs | 0.2 h | ≤ 4.0 hrs |
| **2. Tiempos de Ciclo & Productividad** | **Meta Montacargas (PPH / Tarimas/Hr)** | 3.0 – 12.0 /h | 0.5 | Mínimo por operador/turno |
| **2. Tiempos de Ciclo & Productividad** | **Meta Tiempo de Descarga (Minutos)** | 15 – 90 min | 5 min | ≤ 45 min por tráiler |

3. Deslice los controles hasta fijar los umbrales deseados (o haga clic en **Valores Recomendados** para restaurar los estándares de fábrica).
4. Haga clic en **Guardar y Aplicar Metas**. Todos los semáforos, líneas de referencia y porcentajes de cumplimiento del módulo se recalcularán inmediatamente.

---

#### 7.10.2 Productividad & Turnos (`/performance/operators`)

![Rendimiento - Productividad y Turnos](screenshots/27b-performance-operators.png)

Evalúa el rendimiento individual y grupal de los montacarguistas:
- **4 KPIs superiores**: *Personal Activo*, *Productividad Promedio (PPH)* (con botón de ajuste de meta PPH), *Cumplimiento Global (%)* y *Certificación DC-3 (% de personal acreditado STPS vigente)*.
- **Eficiencia Operativa por Turno de Trabajo**: Tarjetas interactivas por turno mostrando horario, estatus (`En Meta`, `Atención`, `Crítico`), operadores activos, movimientos totales, PPH promedio y barra de cumplimiento. **Hacer clic sobre un turno filtra automáticamente el ranking inferior** (use **Ver Todos los Turnos** para quitar el filtro).
- **Ranking Individual & Gráfica de Rendimiento (PPH)**: Buscador por nombre, código `MC` o licencia `DC-3`; tarjetas con barras comparativas frente a la línea de meta PPH; y tabla detallada con 10 columnas (`Operador / Código`, `Puesto & Turno`, `Certificación DC-3`, `Recepción`, `Acomodo`, `Salidas`, `Total Movs`, `PPH (Tarimas/Hr)`, `Cumplimiento Meta`, `Desempeño`).

---

#### 7.10.3 Circuito Delicado — Flota Propia (`/performance/circuito-delicado`)

![Rendimiento - Circuito Delicado](screenshots/27c-performance-circuito.png)

Monitorea la operación logística de la flota propia dedicada (10 choferes y 7 unidades):
- **4 KPIs superiores**: *Viajes Completados (Mes)*, *Tiempo Promedio de Vuelta (hrs)*, *Choferes Dedicados* y *Unidades de Flota*.
- **Controles de vista y filtros**: Buscador por chofer, placas o número económico; conmutador entre vista **Choferes (10)** y **Unidades (7)**; y pills de estatus (`Todos los Registros`, `En Ruta`, `Disponible` / `En Patio / Taller`).
- **Tabla de Choferes**: `Chofer / Licencia`, `Placas Asignadas`, `Viajes Mes`, `Tarimas Movidas`, `Piezas Movidas`, `Rotación de Cajas`, `Tiempo Vuelta (Hrs)` y `Estatus`.
- **Tabla de Unidades**: `Unidad / Económico`, `Tipo de Unidad`, `Chofer Habitual`, `Viajes Asignados`, `Cajas Remolcadas`, `Piezas Transportadas`, `Horas en Ruta` y `Estatus`.

---

#### 7.10.4 Catálogo & Reglas de KPIs (`/performance/kpis`)

Consola Master-Detail (35% catálogo izquierdo / 65% ficha de definición y umbrales derecha) para administrar los indicadores operativos del motor de evaluación:
- **Barra de pestañas por módulo** (`Todos` y cada módulo operativo) y botón **+ Nuevo Indicador**.
- **4 KPIs del catálogo**: *Total Indicadores*, *Óptimos*, *En Atención* y *Críticos*.
- **Filtros del directorio**: Buscador libre, selector de **Proceso / módulo** y selector de **Estado de control** (`Óptimo`, `Atención`, `Crítico`, `Sin datos`).
- **Panel de detalle**: Muestra el resumen ejecutivo del indicador, tarjetas de *Meta objetivo*, *Umbral de atención*, *Umbral crítico* y *Frecuencia*, el diagrama de **Flujo de cálculo del indicador** (`01 Evento inicial ➔ 02 Proceso monitoreado ➔ 03 Evento final`), el **Mapa de umbrales** según tipo de evaluación (`Mayor es mejor`, `Menor es mejor` o `Dentro de rango`) y el formulario para crear o editar reglas de cálculo.

---

#### 7.10.5 Reportes Oficiales & KPIs 2026 (`/performance/reports-2026`)

![Rendimiento - Reportes 2026](screenshots/27d-performance-reports.png)

Consola diseñada para la auditoría ejecutiva y la descarga inmediata de los **4 libros oficiales en formato Excel (`.xlsx`)** con múltiples hojas, estilos corporativos, fórmulas y semáforos integrados. El nombre de cada archivo incorpora automáticamente la fecha real de descarga en formato `DD-MM-AAAA`.

| Cuadrante | Nombre del archivo generado | Estructura de hojas | Contenido y métricas clave |
| :--- | :--- | :--- | :--- |
| **1. KPIs de Descarga** | `KPI´S DESCARGA - DD-MM-AAAA.xlsx` | **2 hojas** (`Datos descarga `, `KPi´s`) · 2 gráficas | 13 columnas auditadas (`FECHA`, `NO.`, `PRODUCTO / DESCRIPCIÓN`, `LÍNEA TRANSPORTISTA`, `NO. ENTRADA (E)`, `REMISIÓN`, `HORA REPORTE`, `HORA SALIDA`, `TIEMPO DESCARGA`, `ESTATUS DESCARGA`, `FÍSICO`, `RECEPCIÓN`, `ESTATUS SISTEMA (SAP)`). Promedio mensual (meta ≤ 01:20–01:30 hrs) y ranking por transportista (*Óptimo < 01:30*, *Regular 01:30–02:00*, *Crítico > 02:00*). |
| **2. KPIs de Carga** | `KPI´S DE CARGA - DD-MM-AAAA.xlsx` | **4 hojas** (`Base de datos Embalajas`, `Kpi´s Embalajes`, `Base de datos culinarios`, `Kps´s Culinarios`) · 4 gráficas | Sub-pestañas en pantalla para **Línea Embalajes** y **Línea Culinarios**. 11 columnas oficiales (`AÑO/SEM`, `NO.`, `DESCRIPCIÓN / PRODUCTO`, `IMPORTANCIA / TURNO`, `UNIDAD/TRANSPORTE`, `REMISIÓN`, `HORA REPORTE`, `HORA SALIDA`, `TIEMPO CARGA`, `PROCESO CARGA`, `DESPACHADO`). Meta de cumplimiento ≥ 95% (≤ 01:45 hrs). |
| **3. Movimiento Montacarguistas** | `KPI´S MOVIMIENTO DE MONTACARGISTAS - DD-MM-AAAA.xlsx` | **2 hojas** (`Base de datos`, `KPi´s `) · 1 gráfica | Matriz diaria del **Día 1 al Día 31** por montacarguista, `TOTAL DÍAS`, `TOTAL TARIMAS`, `PROMEDIO/DÍA`, `EFICIENCIA` y **Semáforo oficial**: Verde *Alta Eficiencia* (`211–233+ pallets/turno`), Amarillo *Meta Estándar* (`100–200 pallets/turno`) y Rojo *Zona Crítica* (`< 100 pallets/turno`). |
| **4. Tendencia de Inventarios** | `KPI´S TENDENCIA DE INVENTARIOS MENSUAL - DD-MM-AAAA.xlsx` | **6 hojas** de periodos quincenales/mensuales · 6 gráficas | Control diario contra capacidad fija contratada (`18,000` o `13,500` pallets) con 8 columnas (`FECHA DE INVENTARIO`, `# PALLETS`, `PALLETS FIJOS`, `SUMATORIA DE EXCEDENTES`, `COMENTARIO`, `EXCEDENTE`, `COMPROBACIÓN`, `% CAPACIDAD DE ALMACENAJE`) y límite crítico de saturación al `95%`. |

#### SOP 7.10.2 — Cómo consultar y exportar los 4 libros oficiales Excel 2026
1. Ingrese al Módulo de Rendimiento (`/performance`) y seleccione la pestaña **Reportes Oficiales & KPIs 2026**.
2. Para visualizar previamente los datos y gráficas de un reporte, haga clic en cualquiera de los **4 cuadrantes superiores** (*KPIs de Descarga*, *KPIs de Carga*, *Movimiento Montacarguistas* o *Tendencia de Inventarios*).
   - En **KPIs de Carga**, alterne entre **Línea Embalajes (2 Hojas)** y **Línea Culinarios (2 Hojas)** para revisar cada base.
   - En **Tendencia de Inventarios**, seleccione cualquiera de los 6 botones de período para inspeccionar su ocupación y tabla de excedentes.
3. **Para descargar el libro activo**: Haga clic en el botón principal **Descargar Libro Activo (.xlsx)** en la cabecera o en **Descargar Excel Oficial (.xlsx)** dentro del panel analítico.
4. **Para descargar directamente cualquiera de los 4 libros sin abrir su detalle**: Haga clic en el botón de descarga rápida (ícono de flecha hacia abajo) ubicado en la esquina inferior derecha de cada cuadrante.
5. Verifique la notificación de confirmación que indica el nombre exacto del archivo `.xlsx` descargado con la fecha del día.

---

### 7.11 Reporte de Actividad por Usuario

#### Propósito del módulo
Permite a los mandos operativos consultar, filtrar con precisión forense, analizar cronológicamente por colaborador y exportar (a Excel o PDF) todas las acciones ejecutadas por los usuarios dentro del WMS, destacando automáticamente las operaciones críticas, los errores/rechazos y la **actividad realizada fuera de horario** (antes de las `06:00` o después de las `22:00`).

#### Ruta de acceso y roles autorizados
| Atributo | Detalle |
| :--- | :--- |
| **Rutas de acceso** | `/user-activity` y `/admin/user-activity` |
| **Roles autorizados** | Director General (`ADMIN` / `CEO`), Gerente de Operaciones (`OPERATIONS_MANAGER`), Gerente de Almacén (`WAREHOUSE_MANAGER`), Supervisor de Operaciones (`OPERATIONS_SUPERVISOR`) y Líder de Turno (`SHIFT_LEADER`). *Todos los resultados respetan aislamiento RLS por organización y almacén.* |

![Actividad por Usuario](screenshots/26-user-activity.png)

#### Panel de Métricas (5 KPIs colapsables con estado persistente)
| Indicador | Descripción |
| :--- | :--- |
| **Usuarios activos** | Cantidad de colaboradores con al menos un evento en el rango consultado. |
| **Eventos totales** | Número total de acciones registradas bajo los filtros aplicados. |
| **Op. críticas** | Eventos con severidad `HIGH` o `CRITICAL`. |
| **Errores / rechazos** | Eventos con resultado `ERROR` o `REJECTED`. |
| **Fuera de horario** | Acciones ejecutadas antes de las `06:00` o después de las `22:00`. |

#### Filtros de consulta (Básicos y Avanzados)
El panel de filtros funciona como un acordeón plegable e incluye validación automática de rango de fechas (la fecha final no puede ser anterior a la inicial; por defecto precarga los últimos 7 días):

| Nivel | Campo | Tipo / Opciones |
| :--- | :--- | :--- |
| **Básico (Obligatorio)** | **Fecha inicial** y **Fecha final** | Selectores de fecha (`AAAA-MM-DD`). |
| **Básico** | **Usuario** | Búsqueda por nombre de colaborador. |
| **Básico** | **Rol** | `Todos los roles`, `OPERATIONS_SUPERVISOR`, `SHIFT_LEADER`, `OPERATIONS_MANAGER`. |
| **Básico** | **Almacén** | `Todos los almacenes`, `Toluca`, `Monterrey`, `Guadalajara`. |
| **Básico** | **Módulo** | Catálogo de módulos WMS (`Autenticación`, `Recepción`, `Calidad`, `Inventario`, `Picking`, `Embarques`, `Proveedores`, `Usuarios`, `Layout`, `Reportes`). |
| **Básico** | **Resultado** | `SUCCESS`, `WARNING`, `REJECTED`, `ERROR`. |
| **Básico** | **Criticidad** | `INFO`, `MEDIUM`, `HIGH`, `CRITICAL`. |
| **Básico** | **Búsqueda libre** | Búsqueda reactiva en vivo sobre usuario, módulo, acción o entidad. |
| **Básico** | **Solo actividad fuera de horario** | Casilla de verificación. |
| **Avanzado** *(clic en Filtros avanzados)* | **SKU, Lote, Ubicación, Orden, Embarque, Dirección IP, Tipo de dispositivo, ID de sesión** | 8 campos especializados para trazabilidad puntual de mercancía o sesiones. |

#### Vistas de consulta, Drawer de detalle y Exportación
- **Vista Tabla**: Presenta 10 columnas (`Fecha / hora` con ícono nocturno si es fuera de horario, `Usuario`, `Rol`, `Almacén`, `Módulo`, `Acción`, `Entidad`, `Resultado`, `Criticidad`, `Detalle`), con paginación de 10 eventos por página.
- **Vista Línea de tiempo**: Solicita seleccionar un usuario específico (nunca mezcla actividades de distintos usuarios) y muestra todas sus acciones en secuencia cronológica vertical con opción **Cambiar usuario**.
- **Drawer de Detalle de Actividad**: Al hacer clic en cualquier evento (desde Tabla o Línea de tiempo) se abre el panel lateral con 4 secciones: *A. Información general*, *B. Acción ejecutada*, *C. Cambios realizados (Antes ➔ Después)* y *D. Información técnica* (colapsable: IP, Dispositivo, Navegador, Session ID, Correlation ID y Duración en ms).
- **Exportación**: El menú **Exportar** permite descargar la vista filtrada en **Excel (.xlsx)** (14 columnas auditables) o **PDF (.pdf)** (documento formateado listo para impresión/archivo).

#### SOP 7.11.1 — Cómo crear, guardar y aplicar perfiles de filtros de actividad
1. En la pantalla **Reporte de actividad por usuario** (`/user-activity`), haga clic en el botón **Perfiles** de la barra superior derecha para abrir el panel **Perfiles guardados**.
2. Haga clic en **+ Nuevo perfil**.
3. Complete el formulario de configuración del perfil:
   - **Nombre** *(obligatorio, mínimo 3 caracteres)*: Ej. *Actividad crítica diaria*.
   - **Código** *(obligatorio, único)*: Ej. `RPT-USR-001`.
   - **Descripción** *(opcional)*: Propósito operativo del perfil.
   - **Visibilidad**: Elija `Privado — Solo para mí` (solo usted puede eliminarlo) o `Compartido — Visible para el equipo` (protegido contra eliminación por otros usuarios).
   - **Módulos** *(obligatorio, al menos 1)*: Marque las casillas de los módulos WMS a monitorear.
   - **Columnas visibles** *(obligatorio, al menos 1)*: Seleccione qué columnas desea incluir de las 11 disponibles (*Fecha y hora, Usuario, Rol, Almacén, Módulo, Acción, Entidad, ID entidad, Descripción, Resultado, Criticidad*).
   - **Vista predeterminada** (`Tabla` o `Línea de tiempo`), **Rango de fecha** (`Hoy`, `Turno actual`, `Últimos 7 días`, `Personalizado`) y **Formato de exportación** (`Excel (.xlsx)`, `CSV (.csv)`, `PDF (.pdf)`).
4. Haga clic en **Guardar perfil**.
5. Desde la lista de perfiles guardados podrá **Aplicar perfil** (carga instantáneamente su configuración en la consola), **Editar**, **Duplicar**, **Activar / Desactivar** o **Eliminar** (solo perfiles privados propios).

---

### 7.12 Motor de Reglas de Negocio

#### Propósito del módulo
Permite parametrizar sin intervención de programación las estrategias, tolerancias, restricciones de seguridad, tiempos límite y automatizaciones que gobiernan el comportamiento del motor de 4GUARD WMS (por ejemplo: estrategia de rotación `FEFO/FIFO`, bloqueo de entradas ciegas sin ASN, tolerancia de pesaje, cuarentena automática en calidad o tiempo de inactividad de sesión).

#### Ruta de acceso y roles autorizados
| Atributo | Detalle |
| :--- | :--- |
| **Ruta de acceso** | `/business-rules` |
| **Roles autorizados** | Director General (`ADMIN` / `CEO`), Gerente de Operaciones (`OPERATIONS_MANAGER`), Gerente de Almacén (`WAREHOUSE_MANAGER`) y perfiles con permiso `BUSINESS_RULES_READ` / `BUSINESS_RULES_WRITE`. |

![Motor de Reglas de Negocio](screenshots/28b-business-rules-real.png)

#### Panel de Métricas (5 KPIs colapsables)
| Indicador | Descripción |
| :--- | :--- |
| **Reglas configuradas** | Catálogo total de parámetros registrados en el motor. |
| **Reglas activas** | Reglas en ejecución en tiempo real. |
| **Reglas inactivas** | Reglas pausadas, en borrador u obsoletas. |
| **Reglas críticas** | Políticas de impacto crítico/bloqueante. |
| **Modificadas recientemente** | Reglas actualizadas en los últimos 14 días. |

#### Barra de filtros y Directorio de Reglas
- **6 filtros reactivos**: Buscador (código, nombre o descripción), **Módulo** (`Recepción`, `Inventario`, `Despacho`, `Calidad`, `Layout & Ubicaciones`, `Sistema Base`, `Seguridad y Accesos`), **Categoría** (`Estrategia Operativa`, `Tolerancia de Operación`, `Alerta Preventiva`, `Automatización`, `Restricción de Seguridad`, `Tiempos y Límites`), **Tipo de dato**, **Alcance** y **Estado**, más botón **Limpiar**.
- **Directorio desplegable**: Muestra cada regla con su código técnico, insignias de criticidad y estado, **Valor actual**, **Procesos impactados**, etiquetas de módulo/tipo/alcance y una tarjeta inferior de **Estado del Motor** (*Auditoría activa, Configuración consistente, Sin conflictos detectados, Backend sincronizado*).

#### Formulario Enterprise de 4 secciones y reglas de validación
En la parte superior del editor se muestra la **Tarjeta de Impacto Operativo** (procesos afectados, aplicación inmediata en motor WMS y alcance RLS) y la etiqueta **Regla de Sistema** cuando se trata de una regla protegida (cuyo código es de solo lectura).

| Sección | Campo | Obligatorio | Opciones y validaciones |
| :--- | :--- | :---: | :--- |
| **1. Identidad de la Regla** | **Código Parámetro** | Sí | Solo mayúsculas, números y guion bajo (ej. `PICKING_STRATEGY`). Solo lectura en reglas de sistema. |
| **1. Identidad de la Regla** | **Nombre Descriptivo** | Sí | Mínimo 5 caracteres. |
| **1. Identidad de la Regla** | **Módulo WMS** y **Categoría Operativa** | Sí | Selectores de los 7 módulos y 6 categorías. |
| **1. Identidad de la Regla** | **Descripción Funcional Detallada** | Sí | Mínimo 10 caracteres. |
| **2. Configuración y Parámetros** | **Tipo Funcional** | Sí | `Determinística`, `Umbral / Límite`, `Política Corporativa`, `Validación de Seguridad`. |
| **2. Configuración y Parámetros** | **Tipo de Dato** | Sí | `Texto libre (String)`, `Numérico`, `Verdadero / Falso (Boolean)`, `Porcentaje (%)`, `Lista de opciones (Enum)`, `Duración en minutos`, `Días calendario`. |
| **2. Configuración y Parámetros** | **Valor Actual** | Sí | Valor operativo que evaluará el motor WMS. |
| **2. Configuración y Parámetros** | **Unidad de Medida**, **Límite Mínimo** y **Límite Máximo** | No | Unidad (ej. `%`, `días`, `minutos`) y rango numérico permitido. |
| **3. Alcance de Aplicación (RLS)** | **Selector visual de Scope** | Sí | • **Global WMS**: Aplica a todo el sistema 4GUARD.<br>• **Organización**: Toda la empresa corporativa.<br>• **Almacén / Sucursal**: Específico por centro de distribución.<br>• **Cliente 3PL**: Exclusivo para una cuenta cliente.<br>• **Cliente × Almacén**: Combinación específica de Cliente en un Almacén. |
| **4. Control y Gobernanza** | **Estado Operativo** | Sí | `Activa`, `Inactiva`, `Borrador`, `Obsoleta`. |
| **4. Control y Gobernanza** | **Nivel de Criticidad** | Sí | `Informativa (Info)`, `Criticidad Media`, `Advertencia (Warning)`, `Alta Criticidad`, `Crítica Bloqueante`. |
| **4. Control y Gobernanza** | **Fecha de Efectividad** | Sí | Fecha (`AAAA-MM-DD`) a partir de la cual entra en vigor la regla. |
| **4. Control y Gobernanza** | **Motivo de la Modificación / Justificación Auditada** | Sí | Mínimo 8 caracteres; **obligatorio en cada guardado** para la bitácora de auditoría Enterprise. |

#### Catálogo de referencia de reglas base del sistema
| Código | Módulo | Tipo | Valor base | Alcance |
| :--- | :--- | :---: | :---: | :--- |
| `PICKING_STRATEGY` | Despacho | Enum | `FEFO` (`FEFO/FIFO/LIFO/LEFO`) | Almacén |
| `RECEIVING_WEIGHT_TOLERANCE` | Recepción | Porcentaje | `2.5 %` (rango 0–10%) | Organización |
| `ALLOW_RECEIPT_WITHOUT_ASN` | Recepción | Boolean | `false` (Bloqueado) | Global |
| `EXPIRATION_WARNING_DAYS` | Inventario | Días | `30 días` (rango 1–180) | Almacén |
| `SESSION_IDLE_MINUTES` | Seguridad | Minutos | `15 minutos` (rango 5–120) | Global |
| `TRUCK_WAIT_ALERT` | Recepción | Minutos | `45 minutos` (rango 10–240) | Almacén |
| `AUTO_QUARANTINE` | Calidad | Boolean | `true` (Habilitado) | Cliente × Almacén |
| `MAX_PALLET_STACK_HEIGHT` | Layout | Numérico | `3 niveles` (rango 1–5) | Almacén |
| `CYCLE_COUNT_DISCREPANCY_THRESHOLD` | Inventario | Porcentaje | `1.0 %` (rango 0.1–5%) | Organización |
| `STAGING_BAY_MAX_DWELL_HOURS` | Despacho | Numérico | `4 horas` (rango 1–24) | Almacén |

#### SOP 7.12.1 — Cómo crear o modificar una regla de negocio
1. Navegue al **Motor de Reglas de Negocio** (`/business-rules`).
2. Para crear una regla nueva, haga clic en **Nueva Configuración**. Para ajustar una existente, abra el **Directorio de Reglas** y seleccione la regla (puede filtrarla primero por módulo o alcance).
3. Revise la **Tarjeta de Impacto Operativo** para confirmar los procesos afectados.
4. Edite los campos necesarios en las secciones **1. Identidad**, **2. Configuración y Parámetros** y **3. Alcance de Aplicación (RLS)**.
5. En la sección **4. Control y Gobernanza**, verifique el **Estado Operativo**, la **Criticidad**, la **Fecha de Efectividad** y redacte obligatoriamente el **Motivo de la Modificación** (mínimo 8 caracteres).
6. En la barra inferior fija (*sticky*), confirme que el indicador muestre *Cambios pendientes por guardar* y haga clic en el botón de guardado. La regla quedará aplicada inmediatamente en el motor WMS.

---

### 7.13 Configuración de Alertas y Notificaciones

#### Propósito del módulo
Permite definir reglas automáticas de monitoreo y notificación operativa para que 4GUARD WMS avise en tiempo real a los roles responsables cuando ocurra una anomalía (espera excesiva en patio, stock mínimo crítico, lotes próximos a vencer FEFO, bloqueos de usuario o errores de integración ERP/SAP), incluyendo **plantillas de mensajes con variables dinámicas**, **vista previa en tiempo real (System Toast)**, botón de prueba en vivo y bitácora de auditoría por regla.

#### Ruta de acceso y roles autorizados
| Atributo | Detalle |
| :--- | :--- |
| **Ruta de acceso** | `/alerts-config` (y redirección desde `/admin/alerts-config`) |
| **Roles autorizados** | Director General (`ADMIN` / `CEO`), Gerente de Operaciones (`OPERATIONS_MANAGER`), Gerente de Almacén (`WAREHOUSE_MANAGER`) y perfiles con permiso `ALERTS_READ` / `ALERTS_WRITE`. |

![Configuración de Alertas y Notificaciones](screenshots/29-alerts-config.png)

#### Panel de Métricas (4 KPIs colapsables)
| Indicador | Descripción |
| :--- | :--- |
| **Alertas Activas** | Reglas de notificación habilitadas actualmente en el motor. |
| **Alertas Críticas** | Reglas activas clasificadas con prioridad `CRITICAL`. |
| **Canales Configurados** | Cantidad de canales de despacho operativos en la plataforma. |
| **Escalamientos** | Reglas activas que cuentan con política de escalamiento automático (`15`, `30` o `60` min). |

#### Directorio de Alertas y filtros
Al abrir el selector **Directorio de Alertas — Clic para seleccionar...**, puede filtrar por:
- **Buscador**: Nombre de la regla, evento o descripción.
- **Chips rápidos de estado**: `Todas`, `Activas`, `Inactivas`, `Críticas`.
- **Selector de Categoría**: `Todas las Categorías`, `Recepción`, `Inventario`, `Calidad`, `Picking`, `Despacho`, `Usuarios`, `Sistema`.

#### Campos del editor de reglas de alerta y validaciones
| Campo | Obligatorio | Validaciones y opciones |
| :--- | :---: | :--- |
| **Nombre de la Alerta** | Sí | Máximo 80 caracteres; único en todo el catálogo (no permite duplicados ni solo espacios). |
| **Categoría** | Sí | `Recepción`, `Inventario`, `Calidad`, `Picking`, `Despacho`, `Usuarios`, `Sistema`. |
| **Evento del WMS** | Sí | Ver tabla completa de eventos del motor a continuación. |
| **Prioridad** | Sí | `Información`, `Baja`, `Media`, `Alta`, `Crítica`. |
| **Estado** | Sí | `Activo` o `Inactivo`. |
| **Canales de Notificación** | Sí | **Notificación del sistema** (habilitado) y **Notificación Push** (habilitado). |
| **Destinatarios de la Notificación** | Sí (mín. 1 rol) | `Supervisor de Operaciones`, `Gerente de Almacén`, `Administrador del Sistema`, `Operador de Montacargas / Mesa`, `Cliente 3PL / Cuenta`. |
| **Condición** | Sí | `Mayor que ( > )`, `Menor que ( < )`, `Igual a ( = )`, `Tiempo excedido ( > t )`. |
| **Valor** | Sí | Número estrictamente positivo (`> 0`). |
| **Unidad** | Sí | `Minutos`, `Horas`, `Piezas`, `Porcentaje` *(el modelo soporta además `Días`, `Unidades` y `Pallets`)*. |
| **Repetición** | Sí | `Nunca`, `Cada 15 minutos`, `Cada 30 minutos`, `Cada hora` *(modelo incluye `Diariamente`)*. |
| **Escalamiento** | Sí | `Sin escalamiento`, `Después de 15 minutos`, `Después de 30 minutos`, `Después de 60 minutos`. |
| **Mensaje mostrado en el sistema** | Sí | Máximo 250 caracteres; admite inserción de variables dinámicas `{{...}}` con un clic. |

#### Catálogo completo de Eventos del WMS soportados
| Código de Evento | Descripción funcional | Categoría habitual |
| :--- | :--- | :--- |
| `WAIT_TIME_EXCEEDED` | Tiempo de espera de camión excedido en patio o andén. | Recepción |
| `PENDING_ASN` | Recepción sin ASN o documento previo pendiente. | Recepción |
| `LOW_INVENTORY` | Inventario por debajo del nivel de stock mínimo configurado. | Inventario |
| `INVENTORY_DISCREPANCY` | Discrepancia detectada en conteo cíclico (físico vs. sistema). | Inventario |
| `LOT_EXPIRATION` | Lote próximo a vencer según política de caducidad FEFO. | Calidad |
| `HALTED_PICKING` | Ola de picking detenida o bloqueada operativamente. | Picking |
| `ORDER_DELAYED` | Orden o embarque retrasado respecto a su compromiso. | Despacho |
| `SHIPMENT_DELAYED` | Embarque retrasado sobre su ventana horaria de salida. | Despacho |
| `USER_LOCKED` | Cuenta de usuario bloqueada por exceso de intentos fallidos. | Usuarios |
| `UNAUTHORIZED_ACCESS` | Intento de acceso no autorizado a recurso protegido. | Usuarios / Seguridad |
| `INTEGRATION_ERROR` | Error en interfaz de integración ERP / SAP. | Sistema |
| `SYSTEM_ERROR` | Error crítico interno del sistema WMS. | Sistema |

#### Tabla completa de Variables Dinámicas `{{...}}` para plantillas de mensaje
Al redactar el campo **Mensaje mostrado en el sistema**, puede hacer clic en los chips interactivos (`+ variable`) o escribir las etiquetas entre dobles llaves para que el motor sustituya automáticamente los datos reales del evento y los previsualice en el **System Toast**:

| Variable dinámica | Significado operativo | Ejemplo en Vista Previa (System Toast) |
| :--- | :--- | :--- |
| `{{truck}}` | Identificador o económico de la unidad transportista | `TRK-9042` |
| `{{ramp}}` | Código de rampa o andén asignado | `Rampa-04` |
| `{{supplier}}` | Nombre del proveedor o línea transportista | `Logística Bimbo S.A.` |
| `{{sku}}` | Código o descripción del producto (SKU) | `NESCAFÉ-001-Caja` |
| `{{zone}}` | Zona, pasillo o ubicación de almacén | `Aisle-03-Bay-12` |
| `{{qty}}` | Cantidad física detectada en el evento | `100` (toma el valor configurado) |
| `{{lot}}` | Número de lote de mercancía | `LOT-202607-X` |
| `{{date}}` | Fecha relevante del evento (ej. fecha de caducidad) | `15/08/2026` |
| `{{waveId}}` | Identificador de la ola de picking | `WAVE-1084` |
| `{{aisle}}` | Pasillo específico donde ocurre la incidencia | `Pasillo 08` |
| `{{username}}` | Nombre de usuario involucrado en el evento | `carlos.lopez` |
| `{{interfaceId}}` | Identificador de interfaz ERP / SAP | `SAP-WMS-ORDERS` |
| `{{code}}` | Código de error o respuesta de integración | `504 Gateway Timeout` |
| `{{routeId}}` | Identificador de ruta de embarque | `RT-TOL-042` |
| `{{value}}` | Valor umbral numérico definido en la regla | `30` (según campo *Valor*) |

#### SOP 7.13.1 — Cómo configurar, probar y activar una regla de alerta
1. Ingrese a **Configuración de Alertas y Notificaciones** (`/alerts-config`).
2. Haga clic en el botón **Nueva alerta** (o seleccione una regla existente desde el **Directorio de Alertas** para editarla).
3. Capture un **Nombre de la Alerta** único y descriptivo (ej. *Unidad con tiempo de espera excedido en patio*).
4. Seleccione la **Categoría**, el **Evento del WMS**, la **Prioridad** y el **Estado** (`Activo`).
5. En **Canales de Notificación**, confirme que **Notificación del sistema** (y opcionalmente **Notificación Push**) esté seleccionado.
6. En **Destinatarios de la Notificación**, marque al menos un rol responsable (por ejemplo, *Supervisor de Operaciones* y *Gerente de Almacén*).
7. Configure la lógica de disparo: seleccione **Condición** (ej. `Tiempo excedido ( > t )`), capture un **Valor** positivo (ej. `30`), elija la **Unidad** (`Minutos`), la **Repetición** (`Cada 15 minutos`) y el **Escalamiento** (`Después de 30 minutos`).
8. Redacte el **Mensaje mostrado en el sistema** e inserte las variables necesarias haciendo clic en los chips inferiores (ej. `+ truck`, `+ ramp`, `+ value`). Observe cómo se actualiza instantáneamente el recuadro **Vista Previa en Tiempo Real (System Toast)**.
9. Haga clic en **Probar alerta** para disparar una notificación real en pantalla con el estilo de color e ícono correspondiente a la prioridad seleccionada (sin necesidad de guardar aún).
10. Haga clic en **Guardar cambios**. Si más adelante desea pausar la regla sin eliminarla, selecciónela y utilice el botón **Desactivar** (o **Activar**) en el extremo inferior izquierdo, confirmando en el diálogo modal. Todo cambio quedará trazado en la tabla inferior **Historial de Auditoría y Trazabilidad**.

---

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
