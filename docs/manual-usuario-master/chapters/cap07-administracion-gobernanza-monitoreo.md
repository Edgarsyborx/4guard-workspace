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
