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
