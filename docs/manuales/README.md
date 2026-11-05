# Suite de Manuales de Usuario — 4GUARD WMS

Bienvenido a la documentación operativa oficial de **4GUARD WMS (Warehouse Management System)**. Esta suite de manuales está estructurada de forma modular, independiente y detallada para cada uno de los perfiles clave de la operación logística.

Cada manual contiene flujos de trabajo paso a paso, reglas de negocio, criterios de validación, tratamiento de excepciones y marcadores directos para insertar las capturas de pantalla de la interfaz de usuario.

---

## 📚 Índice de Manuales por Perfil

| Perfil Oficial | Rol del Sistema | Entorno de Uso | Manual Dedicado |
| :--- | :--- | :--- | :--- |
| **1. Guardia de Seguridad** | `SECURITY_GUARD` | Consola Web (`/security`) | [Manual de Guardia de Seguridad](01_guardia_seguridad/MANUAL_GUARDIA_SEGURIDAD.md) |
| **2. Gerente de Operaciones** | `OPERATIONS_MANAGER` | Consola Web (`admin-console`) | [Manual de Gerente de Operaciones](02_gerente_operaciones/MANUAL_GERENTE_OPERACIONES.md) |
| **3. Mesa de Control** | `CONTROL_DESK` | Consola Web (`admin-console`) | [Manual de Mesa de Control](03_mesa_control/MANUAL_MESA_CONTROL.md) |
| **4. Montacarguista / Operador RF** | `WAREHOUSE_OPERATOR` | Terminal RF PWA (`rf-terminal`) | [Manual de Montacarguista RF](04_montacarguista_rf/MANUAL_MONTACARGUISTA_RF.md) |

---

## 📂 Estructura de Archivos e Imágenes

```text
docs/manuales/
├── README.md                                  <-- Índice general
├── 01_guardia_seguridad/
│   ├── MANUAL_GUARDIA_SEGURIDAD.md
│   └── imagenes/                              <-- Guardar aquí las capturas de Caseta
├── 02_gerente_operaciones/
│   ├── MANUAL_GERENTE_OPERACIONES.md
│   └── imagenes/                              <-- Guardar aquí las capturas Gerenciales
├── 03_mesa_control/
│   ├── MANUAL_MESA_CONTROL.md
│   └── imagenes/                              <-- Guardar aquí las capturas de Mesa de Control
└── 04_montacarguista_rf/
    ├── MANUAL_MONTACARGUISTA_RF.md
    └── imagenes/                              <-- Guardar aquí las capturas de Terminal RF
```

---

## 📷 Instrucciones para Insertar Capturas de Pantalla

1. Cada manual cuenta con marcadores visuales predefinidos:
   ```markdown
   ![Descripción](imagenes/nombre_archivo.png)
   *Figura X: Descripción técnica del elemento.*
   ```
2. Realice la captura de pantalla de la sección correspondiente en la aplicación.
3. Guarde la imagen con el nombre exacto especificado en la subcarpeta `imagenes/` de cada manual.
4. Las imágenes se visualizarán automáticamente tanto en visualizadores de Markdown como en GitHub, GitLab o al exportar a PDF corporativo.

