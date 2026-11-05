# Manual de Usuario Oficial: Guardia de Seguridad (Caseta de Vigilancia)
**Sistema:** 4GUARD WMS  
**Perfil Asignado:** `ROLE_SECURITY_GUARD` / `VIGILANCIA`  
**Módulos Autorizados:** Caseta & Seguridad (`/security`), Dashboard Básico (`/dashboard`)  
**Versión del Manual:** 2.0  

---

## 1. Introducción y Responsabilidades del Puesto

El **Guardia de Seguridad** es el custodio del perímetro y el responsable del control de accesos de vehículos de transporte pesado y ligero a la planta. 

### Objetivos Principales:
1. Validar la identidad de los operadores de transporte y sus credenciales de circulación.
2. Emitir pases digitales con código QR para que los choferes completen su formato de auto-registro **F01-PO-CP-7.1.3-03**.
3. Realizar la inspección física vehicular (integridad de caja, neumáticos, sellos/marchamos y temperatura de frío).
4. Asignar el andén o rampa de descarga/carga, o en su defecto la posición de espera en patio.
5. Controlar el flujo en patio y registrar el **Check-Out (Salida Oficial)** sellando la boleta final en PDF.

---

## 2. Acceso al Sistema e Inicio de Turno

1. Ingrese a la URL de acceso del sistema 4GUARD en su navegador web.
2. Digite su usuario asignado (ej. `guardia`) y su contraseña de seguridad.
3. Al pulsar **"Iniciar Sesión"**, el sistema detecta automáticamente su rol operativo y lo redirige de forma directa al módulo autónomo de **Caseta de Seguridad** (`/security`).

![Inicio de Sesión en Caseta de Seguridad](imagenes/01_login_caseta.png)
*Figura 1: Pantalla de inicio de sesión y redirección automática al módulo de Caseta.*

> [!NOTE]
> El guardia de seguridad tiene una sesión protegida que solo le permite interactuar con Caseta y Dashboard. Cualquier intento de entrar a rutas internas del almacén está bloqueado por el cortafuegos RBAC del sistema.

---

## 3. Entorno de Trabajo de Caseta de Seguridad

La pantalla de Caseta de Seguridad está organizada en 4 pestañas de control operativo en tiempo real:

1. **Generar Pase QR**: Creación de tokens digitales para transportistas en fila de acceso.
2. **Pases Activos / En Espera**: Choferes que han escaneado su QR y están llenando o enviando su checklist.
3. **En Planta / Patio**: Unidades autorizadas que se encuentran actualmente en andén de descarga, carga o patio de maniobras.
4. **Historial de Caseta**: Bitácora inmutable con filtros por turno, fecha o placa para auditoría.

---

## 4. Procedimiento 1: Generación de Pase Digital QR para Chofer

Cuando un camión arriba a la pluma de entrada:

1. En la pestaña **"Generar Pase QR"**, pulse el botón dorado **`+ Nuevo Pase de Acceso`**.
2. Complete los datos preliminares en el formulario:
   - **Tipo de Operación**:
     - Seleccione **DESCARGA** (Mercancía que ingresa para recepción en almacén).
     - Seleccione **CARGA** (Unidad que viene a recolectar producto para embarque/despacho).
   - **Cliente / Propietario**: Seleccione el cliente depositante en la lista desplegable.
   - **Línea Transportista**: Seleccione o capture la empresa de flete.
   - **Nombre del Chofer**: Nombre completo del operador del transporte.
   - **Placas**: Placas del tractocamión y de la caja/remolque.
3. Pulse **`Generar Pase Digital QR`**.
4. La pantalla mostrará una credencial digital en pantalla con un **Código QR de alta resolución** y un **Token de 6 caracteres**.

![Generador de Pase Digital QR y Credencial de Acceso](imagenes/02_generador_pase_qr.png)
*Figura 2: Generación del código QR y credencial de acceso para el transportista.*

5. **Entrega al chofer**: Indique al chofer que escanee el código con la cámara de su smartphone. Si el chofer no cuenta con cámara, proporciónele el token de 6 caracteres para ingresar en el portal móvil.

---

## 5. Procedimiento 2: Auto-Registro del Chofer (Portal Móvil)

El transportista, desde la comodidad de su cabina:

1. Escanea el QR y accede automáticamente a la interfaz web móvil de 4GUARD.
2. Llena sus datos obligatorios:
   - Número de Licencia de Conducir y fecha de vencimiento.
   - Número de Seguro Social o identificación oficial.
   - Números de sello fiscal / marchamos colocados en las puertas de la caja.
   - Si transporta producto refrigerado: lectura de temperatura actual del termógrafo (°C).
3. Responde el checklist sanitario y de seguridad preliminar (ausencia de humedad, olores, plagas o daños estructurales).
4. Firma en el recuadro digital táctil y pulsa **`Enviar Registro a Caseta`**.

![Portal Móvil del Chofer - Formato F01](imagenes/03_portal_chofer_f01.png)
*Figura 3: Interfaz móvil de captura de datos y firma digital del transportista.*

---

## 6. Procedimiento 3: Revisión de Checklist F01 y Asignación de Andén

Una vez que el chofer pulsa "Enviar", la consola del guardia emite una **alerta sonora inmediata** y una notificación emergente.

1. Vaya a la pestaña **"Pases Activos / En Espera"**.
2. Localice la tarjeta del pase que ahora muestra el estatus **`SUBMITTED`** (Registro Enviado).
3. Haga clic sobre la tarjeta para abrir el formulario integral de inspección en pantalla completa.

![Revisión de Checklist F01 en Consola de Caseta](imagenes/04_revision_checklist_caseta.png)
*Figura 4: Formulario de revisión presencial y checklist vehicular F01.*

4. **Inspección Física Presencial**:
   - Acuda a la unidad y verifique que los sellos físicos coincidan exactamente con lo registrado en el sistema.
   - Inspeccione llantas, espejos, cabina y caja.
   - Marque las casillas de verificación técnica obligatorias (C-Cumple, NC-No Cumple).
5. **Asignación de Ubicación**:
   - Si Mesa de Control ya programó el andén: seleccione la **Rampa (ej. Rampa 01, Rampa 03)**.
   - Si el andén está ocupado: seleccione **Patio de Maniobras** para ordenar que el vehículo espere estacionado.

![Asignación de Andén o Posición en Patio](imagenes/05_asignacion_anden_patio.png)
*Figura 5: Asignación de rampa o patio y confirmación de ingreso oficial.*

6. Pulse **`Autorizar Ingreso Oficial a Planta`**.
7. **Acción del Sistema**: 
   - El sistema registra la hora exacta de entrada.
   - Genera automáticamente en el WMS el folio oficial de **Recepción (`REC-XXXXXX`)** o de **Salida (`OUT-XXXXXX`)** en estado **`REGISTERED`**.
   - Notifica a Mesa de Control y a las Terminales RF que la unidad ha ingresado al perímetro.

---

## 7. Procedimiento 4: Monitoreo de Unidades en Planta (Patio de Maniobras)

1. Diríjase a la pestaña **"En Planta / Patio"**.
2. En esta vista observará todas las unidades que están físicamente dentro de la instalación.

![Monitoreo de Unidades en Planta y Semáforo de Tiempo](imagenes/06_monitoreo_unidades_planta.png)
*Figura 6: Tablero de unidades en planta con estatus de operación y cronómetro de permanencia.*

3. **Interpretación de Estatus de la Unidad**:
   - 🔵 **EN ESPERA**: La unidad está en patio esperando rampa libre.
   - 🟡 **EN MANIOBRA (EN PROCESO)**: El montacarguista está descargando o cargando activamente en el andén.
   - 🟢 **LISTO PARA SALIDA**: El almacén y Mesa de Control concluyeron la maniobra, autorizaron el folio y liberaron la unidad.

---

## 8. Procedimiento 5: Registro de Salida (Check-Out) e Impresión de Boleta F01

Cuando la unidad concluye su operación en andén y se presenta en la caseta de salida:

1. En la pestaña **"En Planta"**, la unidad se mostrará con distintivo verde **`LISTO PARA SALIDA`**.
2. Pulse el botón **`Registrar Salida (Check-Out)`**.
3. Inspeccione los sellos finales de salida colocados en las puertas de la caja (en caso de salidas de producto terminado) y verifique que el chofer porte su remisión o carta porte firmada.
4. Capture el número de sello de salida y notas finales si existió alguna incidencia.
5. Pulse **`Confirmar Salida Definitiva`**.
6. El sistema cerrará el ciclo de la unidad, liberará el andén/estacionamiento y abrirá la ventana para **Imprimir o Descargar la Boleta F01 en PDF**.

![Check-Out Oficial y Emisión de Boleta F01 en PDF](imagenes/07_checkout_boleta_pdf.png)
*Figura 7: Cierre de pase de acceso y boleta F01 sellada digitalmente lista para impresión.*

7. Entregue una copia firmada al chofer y permita el levantamiento de la pluma de salida.

---

## 9. Tratamiento de Incidencias y Preguntas Frecuentes

* **¿Qué hacer si el chofer no tiene smartphone o no tiene datos móviles?**  
  El guardia puede pulsar sobre el pase generado y realizar la captura manual de los datos del chofer directamente desde la consola de caseta pulsando *"Llenar en Sitio por Guardia"*.

* **¿Qué hacer si un sello viene roto o violado al arribo?**  
  No autorice el ingreso. Marque la casilla de no conformidad en el checklist, capture la observación en el campo de notas y dé aviso inmediato por radio a la Mesa de Control y al Supervisor de Calidad.

* **¿Cómo cancelar un pase generado por error?**  
  En la pestaña "Pases Activos", ubique el pase no utilizado y pulse el icono de papelera **"Descartar Pase"**. El sistema solicitará confirmación y liberará el token.

