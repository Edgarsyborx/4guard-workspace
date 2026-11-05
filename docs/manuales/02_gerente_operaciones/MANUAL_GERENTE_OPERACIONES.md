# Manual de Usuario Oficial: Gerente de Operaciones
**Sistema:** 4GUARD WMS  
**Perfil Asignado:** `ROLE_OPERATIONS_MANAGER` / `WAREHOUSE_MANAGER`  
**Módulos Autorizados:** Dashboard (`/dashboard`), Movimientos de Almacén (`/warehouse-movements`), Topología 2D (`/catalogs/warehouse`), Rendimiento & Horas (`/performance`), Administración WMS (`/admin`)  
**Versión del Manual:** 2.0  

---

## 1. Perfil del Puesto y Matriz de Gobernanza (Segregación de Funciones)

El **Gerente de Operaciones** es el líder táctico y estratégico de la cadena logística interna. Su responsabilidad principal es garantizar el cumplimiento de los acuerdos de nivel de servicio (SLAs) de clientes depositantes, maximizar la ocupación cúbica del almacén, supervisar el throughput diario y auditar la productividad del personal operativo.

> [!IMPORTANT]
> **Segregación de Funciones Corporativa (Auditoría Ciberseguridad & Compliance)**:  
> En cumplimiento estricto con la política de control interno, el Gerente de Operaciones tiene facultades totales sobre la planeación, supervisión, topología, métricas y administración, pero **tiene restringido el acceso directo a la operación de Caseta de Seguridad (`/security`) y a los dictámenes técnicos de Control de Calidad (`/quality`)**. Esto evita conflictos de interés entre la velocidad operativa y el control perimetral o de inocuidad.

---

## 2. Acceso y Navegación General en la Consola

1. Ingrese a la consola administrativa de 4GUARD con sus credenciales gerenciales.
2. En la barra superior visualizará:
   - Nombre de la organización y selector de **Sucursal Activa** (ej. *BR-TOL-01 Toluca*).
   - Reloj de planta con estatus de conexión en vivo.
   - Indicador del **Turno Vigente** (ej. *Turno Matutino CDMX · 08:00–16:00*).
   - Conmutador de modo visual (Claro / Oscuro) y menú de perfil.
3. En el menú lateral izquierdo accederá a los 5 ejes de su gestión: **Dashboard**, **Movimientos de Almacén**, **Almacén / Topología**, **Rendimiento** y **Administrar**.

---

## 3. Módulo 1: Dashboard Ejecutivo y KPIs de Throughput

Al ingresar a `/dashboard`, el sistema presenta el cuadro de mando integral de la planta:

![Dashboard Ejecutivo y KPIs de Planta](imagenes/01_dashboard_kpis_globales.png)
*Figura 1: Tablero ejecutivo con indicadores de Throughput, ocupación y tiempos ciclo.*

### Indicadores Clave en Pantalla:
* **Throughput Diario de Planta**: Comparativa de tarimas recibidas (Inbound) vs. tarimas despachadas (Outbound).
* **Ocupación Espacial Global**: Porcentaje de bahías ocupadas frente a la capacidad instalada total de la sucursal.
* **Tiempo Promedio de Maniobra**: Minutos promedio transcurridos desde que una unidad ingresa por caseta hasta que se libera su salida.
* **Productividad por Cuadrilla**: Tarimas movidas por hora/hombre en el turno activo.
* **Filtros Dinámicos**: Puede segmentar los datos por día de la semana, turno específico o rango de fechas para análisis de tendencias.

---

## 4. Módulo 2: Torre de Control de Movimientos de Almacén

Ubicado en `/warehouse-movements`, este módulo homologa el control de los tres flujos logísticos del almacén mediante tarjetas ejecutivas con acabado satinado y cabecera dorada:

![Torre de Control de Movimientos de Almacén](imagenes/02_torre_control_movimientos.png)
*Figura 2: Tablero homologado de Recepción, Cambio de Almacén y Salidas.*

### Pestañas de Supervisión:
1. **1. Recepción (Inbound)**: Supervisión de entradas de mercancía desde el arribo en caseta hasta la estiba y autorización.
2. **2. Cambio de Almacén (Traspasos / Putaway)**: Supervisión de reubicaciones internas entre bahías para optimizar slots de almacenamiento.
3. **3. Salidas (Outbound)**: Supervisión de órdenes de despacho, asignación de andenes de carga y embarque a transporte.

### Facultades de Intervención Gerencial:
* **Conmutación de Prioridad Urgente**: El Gerente puede pulsar el botón de prioridad en cualquier movimiento para cambiarlo de `NORMAL` a `URGENTE`. Esto despacha inmediatamente una alerta roja a la Terminal RF de los montacarguistas, reorganizando su cola de trabajo de forma preemptiva.
* **Pausa Operativa de Maniobra**: En caso de emergencia, corte de energía o cambio de turno imprevisto, puede pausar una maniobra conservando íntegro el progreso escaneado.
* **Reasignación Dinámica**: Capacidad de cambiar el operador de montacargas asignado a una tarea si se requiere balancear la carga de trabajo.

---

## 5. Módulo 3: Topología 2D y Monitoreo de Ocupación de Bahías

Ubicado en `/catalogs/warehouse` o `/layout`:

![Topología 2D SVG y Blueprint de Naves](imagenes/03_topologia_mapa_2d.png)
*Figura 3: Blueprint 2D del almacén con coordenadas físicas de naves y pasillos.*

1. **Visualización Espacial 2D**:
   - Representación en plano vectorial SVG de las naves, pasillos, racks y andenes de la planta.
   - Posibilidad de hacer zoom, desplazarse y filtrar por nave o área funcional.
2. **Semáforo y Matriz de Bahías**:
   - Cada bahía refleja su estatus operativo mediante un código de color normalizado:

![Semáforo y Ocupación Detallada de Bahías](imagenes/04_semaforo_ocupacion_bahias.png)
*Figura 4: Matriz de ocupación con semáforo verde/ámbar/rojo y detalle de inventario por posición.*

   - 🟢 **Verde (Disponible)**: Bahía vacía lista para recibir tarimas de putaway.
   - 🟡 **Ámbar (Ocupación Parcial)**: Bahía con capacidad remanente de estiba.
   - 🔴 **Rojo (Saturada)**: Bahía al 100% de su capacidad nominal.
   - 🟣 **Morado (Bloqueo QM)**: Bahía restringida por orden de Control de Calidad debido a merma o cuarentena.
3. **Inspección de Posición**: Al hacer clic en cualquier celda de bahía, el panel lateral expone el detalle exacto: SKU, descripción, cliente depositante, lote, caducidad, cantidad de bultos/piezas y fecha del último movimiento.

---

## 6. Módulo 4: Rendimiento Operativo y Reporte de Horas Trabajadas

Ubicado en `/performance`:

![Módulo de Rendimiento y Horas Trabajadas de Usuarios](imagenes/05_monitor_horas_trabajadas.png)
*Figura 5: Monitor de sesiones activas, auditoría de conexiones y reporte de horas efectivas.*

### Pestaña 1: Productividad y Rendimiento
- Métricas comparativas de rendimiento por turno (horas pico de carga vs. valles).
- Tiempos de respuesta de montacarguistas entre asignación y primer escaneo.

### Pestaña 2: Conexiones y Horas Trabajadas de Usuarios
- **Monitor de Actividad en Tiempo Real**: Lista de usuarios autenticados en el sistema con su IP, dispositivo, sucursal y última interacción registrada.
- **Reporte Consolidado de Horas**:
  - Filtros por rol (Supervisores, Mesa de Control, Montacarguistas, etc.) y por rango de fechas.
  - Registro de hora de inicio de jornada y hora de desconexión.
  - **Regla de Inactividad y Descuento por Timeout**: Si un usuario deja su ordenador inactivo y el sistema cierra su sesión por timeout de seguridad, la plataforma desglosa los 15 minutos de inactividad para calcular el tiempo real de labor productiva.
  - **Exportación**: Botón directo para descargar el reporte consolidado en archivo **CSV / Excel** para soporte administrativo y nómina.

---

## 7. Módulo 5: Administración WMS y Gobernanza de Catálogos

Ubicado en `/admin`:

![Panel de Administración WMS y Catálogos Maestros](imagenes/06_administracion_catalogos.png)
*Figura 6: Panel de administración de usuarios, clientes 3PL y catálogo de montacarguistas.*

### Catálogos Gestionados:
1. **Usuarios y Accesos**:
   - Alta de personal administrativo, asignación de rol y vinculación a turnos de trabajo.
   - Restablecimiento de contraseñas y desbloqueo de cuentas.
2. **Clientes Depositantes (3PL)**:
   - Registro de empresas dueñas de mercancía, razón social, RFC y domicilios de entrega (Ship-to destinations).
3. **Líneas Transportistas y Proveedores**:
   - Maestro de empresas fleteras autorizadas para ingresar a planta con validación de RFC contra el SAT.
4. **Operadores de Montacargas**:
   - Padrón de montacarguistas certificados con emisión de código operativo `MC-XXX`, número de licencia DC-3 y asignación de PIN de 4 dígitos para Terminal RF.
5. **Configuración de Turnos Operativos**:
   - Parametrización de los horarios oficiales de planta (Matutino, Vespertino, Nocturno, Mixto) y días operativos.

