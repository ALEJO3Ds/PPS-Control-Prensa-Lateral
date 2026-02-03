# Control de Prensa Lateral en Paletizado Industrial

## Práctica Profesional Supervisada – Ingeniería Mecatrónica  
**Universidad Nacional de Lomas de Zamora – Facultad de Ingeniería**

---

## 📌 Introducción

Este repositorio documenta el desarrollo de una **Práctica Profesional Supervisada (PPS)** realizada en el marco de la carrera de Ingeniería Mecatrónica.  
El proyecto consistió en la **mejora del sistema de control de una prensa lateral neumática** integrada a una **paletizadora industrial automática**, mediante la incorporación de un **modo de prensado limitado** configurable desde HMI.

La intervención se realizó sobre un **equipo industrial real**, modificando exclusivamente la **lógica de control en PLC** y la **interfaz HMI**, sin rediseñar el equipo ni alterar la secuencia de funcionamiento validada en producción.

📷 **[Paletizadora y prensa lateral en planta]**

---

## 🎯 Objetivo del proyecto

Optimizar el control del recorrido del cilindro neumático de la prensa lateral, permitiendo adaptar el prensado sin modificar:

- La secuencia automática del sistema.
- Las temporizaciones originales del ciclo.
- El comportamiento manual existente.

La solución implementada permite **detener el avance del cilindro en una posición intermedia**, definida por sensor, **manteniendo la presión durante el tiempo original de prensado**.

---

## 🏭 Contexto industrial

El sistema intervenido corresponde a una paletizadora automática ubicada en una planta industrial dedicada a la fabricación de productos para la construcción.  
La prensa lateral cumple la función de **compactar las bolsas durante el armado del pallet**, asegurando su estabilidad antes del transporte y almacenamiento.

La paletizadora es uno de los equipos más antiguos de la línea, por lo que la mejora debía integrarse **respetando la lógica original**, sin afectar la confiabilidad ni la seguridad del proceso.

---

## 🧩 Arquitectura del sistema

- **PLC:** Omron CJ2M-CPU34  
- **HMI:** Omron NS5  
- **Software PLC:** CX-Programmer  
- **Software HMI:** CX-Designer  
- **Actuador:** Cilindro neumático Micro 0.049.260.300  
- **Electroválvula:** 5/3 centro cerrado  
- **Sensor agregado:** IFM MR0120 (magnético ON/OFF)


---

## ⚙️ Descripción de la solución

Se incorporó un **sensor magnético ON/OFF** sobre el cilindro neumático para detectar una posición intermedia antes del fin de carrera mecánico.  
A partir de esta señal se implementó un **modo de prensado LIMITADO**, seleccionable desde HMI.

### Modos de funcionamiento

#### 🔹 Modo FULL
- Funcionamiento idéntico al sistema original.
- El cilindro avanza a carrera completa.
- El sensor es ignorado por la lógica de control.

#### 🔹 Modo LIMITADO
- El avance del cilindro se detiene al activarse el sensor.
- Se mantiene la presión durante el tiempo original del ciclo.
- El retroceso ocurre según la lógica global existente.
- No se modifican temporizadores ni pasos de secuencia.


---

## 🖥️ Configuración PLC

El programa PLC fue desarrollado en **CX-Programmer**.  
Los archivos incluidos en este repositorio permiten analizar y reproducir la lógica implementada.

### Señales utilizadas

- **Entrada digital:**  
  - Sensor IFM MR0120 → **CIO 3.14**

- **Bit de modo (HMI):**  
  - Selección FULL / LIMITADO

- **Bit interno:**  
  - Bloqueo lógico de avance del cilindro

🔧 **Nota técnica:**  
La entrada **CIO 3.14** fue verificada previamente como libre y no asociada a funciones críticas del equipo, garantizando que la incorporación del sensor no interfiera con señales existentes.

📷 **[AQUÍ INSERTAR IMAGEN – Rungs modificados en el programa PLC]**

---

## 🖱️ Configuración HMI

La interfaz de operación fue modificada utilizando **CX-Designer**.  
El proyecto HMI se encuentra en formato:

- `.ipp`

Se agregó una sección específica en la pantalla de selección de programa para definir el modo de prensado:

- **Prensa FULL:** recorrido completo.
- **Prensa LIMITADA:** recorrido limitado por sensor.

La selección se realiza mediante botones ON/OFF con indicación por lámpara, manteniendo la estética y estructura original de la pantalla.

📷 **[Pantalla HMI con selección de modo]**

---

## 🔌 Integración del sensor IFM MR0120

El sensor IFM MR0120 se monta directamente sobre el cuerpo del cilindro neumático, detectando el imán del pistón en una posición intermedia definida durante la puesta a punto.

**Conexión eléctrica:**
- El sensor se cablea a la **entrada digital CIO 3.14** del PLC.

**Recomendaciones de instalación:**
- Ajustar la posición del sensor según el recorrido máximo deseado.
- Verificar la repetibilidad de la señal antes de habilitar el modo LIMITADO.
- Asegurar una fijación mecánica firme para evitar desplazamientos por vibración.

📷 **[Ubicación del sensor sobre el cilindro]**

---

## 🛠️ Consideración neumática adicional

Se recomienda la utilización de una **válvula estranguladora antirretorno** en la línea de salida del cilindro neumático.

### Motivo:
- Evitar que el avance del cilindro sea excesivamente rápido y supere el punto de detección del sensor antes de ser leído por el PLC.
- Garantizar una lectura confiable del sensor magnético.
- Mantener un movimiento fluido que asegure el prensado correcto.

⚠️ **Ajuste recomendado:**
- Demasiado abierta → riesgo de sobrepasar el sensor.
- Demasiado cerrada → riesgo de no alcanzar la presión efectiva de prensado.


---

## 🧮 Análisis mecánico

Se realizó el cálculo de la fuerza disponible del cilindro neumático y una verificación estructural del conjunto de prensado mediante **simulación estática**.

Los archivos asociados incluyen:
- Informes en formato `.pdf`
- Modelos CAD y resultados de simulación (SolidWorks)

El análisis permitió verificar que la estructura trabaja con **amplio margen de seguridad** y que el fenómeno de sobreprensado está asociado al **recorrido impuesto** y no a una insuficiencia de fuerza.

📷 **[Resultados de simulación / tensiones]**

---

## 📁 Organización del repositorio

```text
docs/        → Informe final de la PPS  
plc/         → Programa PLC y backups  
hmi/         → Proyecto HMI  
mecanica/    → Modelos CAD y simulaciones estructurales  
imagenes/    → Imágenes utilizadas en el README  
anexos/      → Documentación técnica complementaria  
