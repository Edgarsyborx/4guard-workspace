# Manual de Usuario Oficial: Mesa de Control
**Sistema:** 4GUARD WMS  
**Perfil Asignado:** `ROLE_CONTROL_DESK` / `CONTROL_DESK`  
**Módulos Autorizados:** Dashboard (`/dashboard`), Movimientos de Almacén (`/warehouse-movements`: Recepción, Cambio de Almacén, Salidas), Almacén / Topología 2D (`/catalogs/warehouse`, `/layout`), Consulta de Inventario (`/inventory-query`)  
**Módulos Excluidos por Perfil:** Rendimiento (`/performance`), Administrar (`/admin`)  
**Versión del Manual:** 2.0  

---

## 1. Definición del Puesto y Alcance de Mesa de Control

La **Mesa de Control** es la torre de control operativa y el puente neurálgico entre la Caseta de Vigilancia (ingreso de transporte) y el piso de bodega (cuadrillas de montacargas). Su misión es garantizar que ninguna mercancía sea descargada, reubicada o despachada sin una validación documental, asignación táctica de recursos y autorización estricta en el sistema.

> [!IMPORTANT]
> **Alcance de Módulos para Mesa de Control**:  
> Mesa de Control cuenta con **exactamente los mismos accesos y facultades operativas que la Gerencia de Operaciones**, con la única excepción de los módulos directivos de **Rendimiento** (`/performance`) y **Administración de Usuarios/Roles** (`/admin`). Esto le otorga control total sobre la orquestación física y documental del almacén.

---

## 2. Entorno de Trabajo y Menú de Navegación

Al iniciar sesión con el perfil de Mesa de Control:
1. En la barra superior, seleccione la sucursal activa correspondiente.
2. El menú lateral presenta las opciones operativas autorizadas:
   - **Dashboard**: Vista de flujo de planta en tiempo real.
   - **Recepción**: Submódulos de Recepción de Mercancía, Cambio de Almacén y Salidas.
   - **Almacén / Topología**: Consulta del plano 2D y disponibilidad de bahías.
   - **Consulta Inventario**: Rastreo inmutable de SKUs, lotes y remisiones.

---

## 3. Procedimiento 1: Recepción de Mercancía (Inbound) y Control Multi-Lote

Cuando el Guardia de Seguridad autoriza el ingreso de un camión en Caseta, se genera automáticamente una recepción en estatus **`REGISTERED`**.

1. Diríjase a `/warehouse-movements/receiving`.
2. Ubique la tarjeta de recepción en la columna o filtro de **"En Espera"** (o presione `⌘K` para buscarla directamente por el número de remisión del proveedor).
3. Haga clic sobre la tarjeta ejecutiva para abrir el panel detallado.

![Recepción Inbound y Validación de Lotes con Vida Útil](imagenes/01_recepcion_validacion_lotes_365.png)
*Figura 1: Panel de gestión de recepción, captura de remisión y validación multi-lote.*

4. **Validación Documental**:
   - Coteje la remisión física entregada por el chofer contra los datos registrados en el sistema.
   - Si requiere corregir el número de remisión o documento, pulse **`Modificar Remisión`** ingresando el motivo de cambio.
5. **Captura Multi-Lote y Candado de Calidad (ADR-019)**:
   - En la sección **"Lotes de la Recepción"**, pulse **`+ Agregar Lote`**.
   - Capture el número de lote, fecha de elaboración y fecha de caducidad.
   > [!WARNING]
   > **Regla de Negocio ADR-019 (Vida Útil Mínima de 365 Días)**:  
   > El sistema calcula automáticamente los días remanentes de vida útil desde la fecha actual hasta la caducidad. Si el producto tiene menos de 365 días de vida útil, el sistema bloqueará la captura impidiendo el ingreso de mercancía próxima a vencer sin una excepción autorizada.
6. Capture la cantidad de tarimas estimadas a recibir por cada lote.

---

## 4. Procedimiento 2: Asignación Táctica de Andenes y Montacarguistas

1. Con la recepción abierta y los lotes validados:
2. En la sección **"Asignación de Recursos"**:
   - Seleccione la **Rampa / Andén de Descarga** definitivo (ej. *Rampa 02*).
   - Seleccione el **Montacarguista Certificado** que ejecutará la maniobra en piso.

![Asignación de Andén y Montacarguista para Terminal RF](imagenes/02_asignacion_montacarguista_anden.png)
*Figura 2: Asignación de rampa y despacho de misión a la Terminal RF del montacarguista.*

3. Pulse **`Asignar a Montacargas`**.
4. **Acción del Sistema**:
   - La recepción pasa al estatus **`ASSIGNED`**.
   - Se despacha una señal en tiempo real por Server-Sent Events (SSE) a la Terminal RF de la tablet del montacarguista seleccionado, emitiendo una alerta sonora en su cabina.
5. **Supervisión de la Descarga Física**:
   - Conforme el montacarguista va bajando tarimas y escaneando códigos de barras, el contador de tarimas en la consola de Mesa de Control se actualiza en vivo.
   - Cuando el operador baja la última tarima, la recepción cambia a estatus **`DISCHARGED`** (Descarga física concluida).
6. **Autorización y Cierre Oficial (F01)**:
   - Mesa de Control coteja el conteo final escaneado contra la factura o remisión.
   - Si todo es conforme, pulsa **`Autorizar y Concluir Recepción (COMPLETED)`**.
   - El inventario se da de alta automáticamente en el Kardex y la unidad en Caseta queda lista para su Check-Out.

---

## 5. Procedimiento 3: Reubicaciones y Cambio de Almacén (Putaway)

Para mover mercancía desde las bahías de rampa/buffer hacia las posiciones fijas de estiba en racks:

1. Diríjase a `/warehouse-movements/transfers`.
2. Pulse el botón **`+ Nuevo Traspaso`**.

![Creación de Traspaso y Asignación de Prioridad](imagenes/03_traspasos_putaway_prioridad.png)
*Figura 3: Formulario de cambio de almacén entre bahías con conmutador de prioridad URGENTE.*

3. **Captura del Movimiento**:
   - **Bahía Origen**: Seleccione la bahía donde se encuentra la tarima (ej. *Rampa 02 - Buffer*). El sistema desplegará las tarimas presentes con su código SSCC/UA.
   - **Bahía Destino**: Seleccione la bahía final en racks (ej. *Nave A - Pasillo 02 - Posición 12*). El sistema comprueba en tiempo real que la posición tenga capacidad disponible.
   - **Operador**: Seleccione el montacarguista responsable.
4. **Gestión de Prioridad**:
   - Si la reubicación es rutinaria: mantenga prioridad `NORMAL`.
   - Si el producto se requiere de inmediato para surtir un camión en salida: cambie a **`URGENTE`**. La Terminal RF del operador emitirá una alarma y colocará la tarea al inicio de su cola.
5. Pulse **`Registrar Traspaso`**. El operador ejecuta la maniobra física y al confirmar el depósito, el sistema marca el traspaso como **`COMPLETED`** en sub-segundos.

---

## 6. Procedimiento 4: Despachos y Salidas (Outbound FEFO / Carta Porte)

1. Diríjase a `/warehouse-movements/outbound`.
2. Localice la salida pre-registrada por Caseta o pulse **`+ Nueva Salida`**.

![Selección de Lotes FEFO y Asignación de Embarque](imagenes/04_salidas_algoritmo_fefo.png)
*Figura 4: Despacho de mercancía con sugerencia automática por algoritmo FEFO.*

3. **Cálculo Inteligente de Inventario (Algoritmo FEFO)**:
   - Al seleccionar el cliente y el SKU solicitado, el sistema ejecuta una búsqueda indexada ordenando las existencias por **FEFO (First Expired, First Out)**.
   - Mesa de Control valida que se seleccionen las tarimas con caducidad más cercana para garantizar frescura de inventario.
4. Asigne la rampa de embarque y el montacarguista asignado para la carga.
5. Pulse **`Asignar Maniobra de Carga`**.
6. El montacarguista estiba las tarimas dentro de la caja del tráiler y pulsa `Finalizar Carga Física` (**`LOADED`**).
7. Mesa de Control revisa la lista de bultos y emite la **Carta Porte / Remisión Oficial F03**, pulsando **`Autorizar y Concluir Despacho (COMPLETED)`**.

---

## 7. Procedimiento 5: Consulta de Inventario y "Árbol de Vida" de Remisiones

Ubicado en `/inventory-query`:

![Consulta de Inventario y Árbol de Vida de Remisión](imagenes/05_arbol_vida_trazabilidad.png)
*Figura 5: Trazabilidad inmutable de remisión y árbol genealógico de tarimas.*

- **Buscador Universal `⌘K`**: Permite localizar inventario por código de tarima SSCC, lote, SKU o remisión.
- **Árbol de Vida de la Remisión**: Al capturar un número de remisión, el sistema despliega la línea de tiempo inmutable:
  1. Cuándo arribó la unidad a Caseta y qué guardia la atendió.
  2. Qué operador descargó cada pallet y en qué andén.
  3. Cada reubicación interna que haya tenido la mercancía en el almacén.
  4. En qué fecha y en qué remisión de salida se despachó el producto final.

---

## 8. Procedimiento 6: Gestión de Excepciones y Acciones Extraordinarias

![Modal de Autorización para Cambios, Pausas o Cancelaciones](imagenes/06_modal_pausa_cancelacion.png)
*Figura 6: Ventana de autorización y registro justificado de excepciones operativas.*

* **Pausar Maniobra (`PAUSED`)**: Si ocurre una interrupción urgente o cambio de turno, Mesa de Control puede pausar la recepción o salida. Las tarimas ya descargadas permanecen seguras en el buffer.
* **Modificar Remisión o Carta Porte**: Si el proveedor o transportista entrega documentación corregida, se pulsa `Modificar Remisión` ingresando motivo y justificación que se estampa en la bitácora de auditoría.
* **Reapertura de Recepción**: En caso extraordinario de requerir ingresar tarimas faltantes de un embarque concluido, Mesa de Control puede reabrir el folio con justificación obligatoria.
* **Cancelación de Maniobra**: Anula una operación no iniciada o errónea liberando los andenes y restaurando el inventario al instante.

