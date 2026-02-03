# PPS – Control Mecatrónico de Prensa Lateral en Paletizadora Industrial

**Práctica Profesional Supervisada – Ingeniería Mecatrónica**  
**Universidad Nacional de Lomas de Zamora – Facultad de Ingeniería**

---

## 📌 Índice
1. [Introducción](#-introducción)
2. [Objetivo](#-objetivo)
3. [Sistema intervenido](#-sistema-intervenido)
4. [Solución implementada](#-solución-implementada)
   - [Modos FULL / LIMITADO](#-modos-full--limitado)
   - [PLC – Señales y lógica](#-plc--señales-y-lógica)
   - [HMI – Selección de modo](#-hmi--selección-de-modo)
   - [Sensor IFM MR0120 – Montaje y conexión](#-sensor-ifm-mr0120--montaje-y-conexión)
   - [Consideración neumática](#-consideración-neumática)
5. [Análisis mecánico](#-análisis-mecánico)
6. [Estructura del repositorio](#-estructura-del-repositorio)
7. [Cómo abrir y utilizar los proyectos](#-cómo-abrir-y-utilizar-los-proyectos)
8. [Archivos incluidos](#-archivos-incluidos)
9. [Autor](#-autor)

---

## 📌 Introducción

Este repositorio documenta el desarrollo de una PPS realizada sobre un equipo industrial real: una **paletizadora automática** equipada con una **prensa lateral neumática**.  
La mejora consistió en incorporar un **modo de prensado limitado** configurable desde HMI, manteniendo la secuencia y temporizaciones originales del ciclo.


---

## 🎯 Objetivo

Implementé una mejora de control para **limitar el recorrido del cilindro neumático** en función del producto, sin modificar:
- la secuencia automática existente,
- las temporizaciones originales del ciclo,
- el comportamiento manual ya validado.

La solución permite detener el avance del cilindro en una posición intermedia definida por sensor, **manteniendo presión durante el tiempo original de prensado**.

---

## 🏭 Sistema intervenido

- **PLC:** Omron CJ2M-CPU34  
- **HMI:** Omron NS5  
- **Software PLC:** CX-Programmer  
- **Software HMI:** CX-Designer  
- **Cilindro neumático:** Micro 0.049.260.300 (doble efecto, con imán, carrera 300 mm)  
- **Electroválvula:** 5/3 centro cerrado  
- **Sensor agregado:** IFM MR0120 (magnético ON/OFF)

📷 **[Croquis/visión superior de la capa y zona de contacto]**

---

## ⚙️ Solución implementada

### 🔹 Modos FULL / LIMITADO

**Modo FULL**
- Comportamiento idéntico al original.
- El cilindro avanza a carrera completa.
- El sensor es ignorado por lógica.

**Modo LIMITADO**
- El avance se detiene cuando el sensor detecta el imán del pistón.
- Se conserva el tiempo de prensado del ciclo.
- El retroceso ocurre por la lógica global existente, sin agregar pasos nuevos.

---

### 🧠 PLC – Señales y lógica

**Entrada digital (sensor):**
- `S_PRENSA_LIMIT` → **CIO 3.14**

**Bit de modo (desde HMI):**
- `PRENSA_MODO_LIMITADO` → **CIO 610.09**
  - `0` = FULL (recorrido completo)
  - `1` = LIMITADO (recorrido limitado por sensor)

**Bit interno (bloqueo):**
- `BLOQUEO_PRENSA_LIMIT` → **W500.00**

**Cálculo del bloqueo:**
- `BLOQUEO_PRENSA_LIMIT = PRENSA_MODO_LIMITADO AND S_PRENSA_LIMIT`

**Acción sobre el avance:**
- Se agregó `NOT BLOQUEO_PRENSA_LIMIT` en los rungs que energizan el avance de la prensa (salida Y12).
- En FULL, la lógica queda equivalente al programa original.
- En LIMITADO, al activarse el sensor se impide continuar el avance.

📷 **[Rung de Y12 con el contacto NOT BLOQUEO_PRENSA_LIMIT]**

**Retroceso (Y34):**
- Se mantuvo el criterio del ciclo existente, evitando dobles mandos y conservando la secuencia validada.

📷 **[Rung de Y34 / lógica de retorno]**

---

### 🖥️ HMI – Selección de modo

La pantalla modificada fue **0011 – Programa**.  
Los Programas 1 y 2 escriben en **DM200** (selección de receta/ciclo), por lo que el modo FULL/LIMITADO se definió como **configuración global** independiente del programa activo.

Se agregaron dos botones con indicación por lámpara:

- **Prensa FULL:** escribe `0` en `HOST3:00610.09`
- **Prensa LIMITADA:** escribe `1` en `HOST3:00610.09`

📷 **[Pantalla HMI con botones FULL/LIMITADO]**

---

### 🔌 Sensor IFM MR0120 – Montaje y conexión

El sensor IFM MR0120 se monta sobre el cuerpo del cilindro y detecta el imán del pistón en una posición intermedia definida en la puesta a punto.

**Conexión eléctrica (lógica PLC):**
- El sensor se cableó a la **entrada digital CIO 3.14** (`S_PRENSA_LIMIT`).

**Aclaración importante (conexión física):**
- CIO 3.14 es una **dirección lógica**.  
- El borne físico exacto depende del módulo de entradas instalado y su dirección base.  
- Para identificar el borne:
  1. Abrir **I/O Table and Unit Setup** en CX-Programmer.
  2. Ubicar el módulo de entradas y su rango CIO asignado.
  3. Identificar el canal que mapea a **CIO 3.14**.

📷 **[Tabla de E/S (I/O Table) o bornera del módulo de entradas]**

**Recomendaciones de instalación:**
- Ajustar la posición del sensor según el recorrido máximo deseado.
- Verificar repetibilidad de la señal antes de habilitar el modo LIMITADO.
- Asegurar fijación mecánica firme para evitar desplazamientos por vibración.

---

### ⚠️ Consideración neumática

Se recomienda instalar una **válvula estranguladora antirretorno** para regular la velocidad del cilindro durante el avance.

**Motivo:**
- Evitar que el avance sea tan rápido que supere el punto del sensor antes de ser leído por el PLC.
- Mejorar confiabilidad de detección sin afectar el prensado.

**Ajuste recomendado:**
- Evitar estrangular en exceso (riesgo de no alcanzar el prensado a tiempo).
- Evitar estrangular de menos (riesgo de sobrepaso del punto de detección).

---

## 🧮 Análisis mecánico

Se calculó la fuerza de avance del cilindro a 6,5 bar y se verificó el conjunto mediante simulación estática (tensiones y desplazamientos).  
El análisis permitió concluir que la estructura trabaja con margen de seguridad y que el fenómeno de sobreprensado se relaciona principalmente con el recorrido impuesto y la compresibilidad del producto.

📷 **[Capturas de von Mises / desplazamiento]**

---

## 📁 Estructura del repositorio

```text
docs/        → Informe final (PPS)  
plc/         → Proyecto PLC (.cxp) + backups (.bak) + config (.opt) + PDFs  
hmi/         → Proyecto HMI (.ipp) + capturas  
mecanica/    → Modelos CAD y simulaciones (PDF + imágenes)  
imagenes/    → Imágenes utilizadas en este README  
anexos/      → Catálogos, planos eléctricos y documentación complementaria  
