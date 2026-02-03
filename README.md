# PPS – Control de Prensa Lateral en Paletizadora Industrial

## Práctica Profesional Supervisada – Ingeniería Mecatrónica  
**Universidad Nacional de Lomas de Zamora – Facultad de Ingeniería**

---

## Descripción general

Este repositorio documenta el desarrollo de una Práctica Profesional Supervisada (PPS) realizada en el marco de la carrera de Ingeniería Mecatrónica. El proyecto consistió en la mejora del sistema de control de una prensa lateral neumática integrada a una paletizadora industrial automática, mediante la incorporación de un modo de prensado limitado configurable desde HMI.

La intervención se realizó sobre un equipo industrial real, modificando exclusivamente la lógica de control y la interfaz de operación, sin rediseñar el equipo ni alterar la secuencia general de funcionamiento validada en producción.

---

## Objetivo del proyecto

Optimizar el control del recorrido del cilindro neumático de la prensa lateral, permitiendo adaptar el prensado a distintos productos sin modificar:
- La secuencia automática del sistema.
- Las temporizaciones originales del ciclo.
- El comportamiento manual existente.

Para ello se implementó un modo de prensado LIMITADO, seleccionable desde HMI, que detiene el avance del cilindro en una posición intermedia definida por sensor, manteniendo la presión durante el tiempo original de prensado.

---

## Alcance de la PPS

Las actividades desarrolladas incluyeron:

- Análisis del programa Ladder existente.
- Estudio del sistema neumático de la prensa lateral.
- Selección e integración de un sensor magnético ON/OFF.
- Modificación de la lógica de control en PLC Omron.
- Adecuación de la interfaz HMI para selección de modo FULL / LIMITADO.
- Cálculo de la fuerza del cilindro neumático.
- Modelado mecánico y verificación estructural mediante simulación estática.
- Documentación técnica del funcionamiento y de las decisiones adoptadas.

La solución quedó disponible para su futura implementación en planta, sujeta a la planificación y aprobación interna de la empresa.

---

## Arquitectura del sistema

- PLC: Omron CJ2M-CPU34  
- HMI: Omron NS5  
- Software PLC: CX-Programmer  
- Software HMI: CX-Designer  
- Actuador: Cilindro neumático Micro 0.049.260.300  
- Electroválvula: 5/3 centro cerrado  
- Sensor incorporado: IFM MR0120 (magnético ON/OFF)

---

## Modos de funcionamiento

### Modo FULL
- Funcionamiento idéntico al sistema original.
- El cilindro avanza a carrera completa.
- El sensor es ignorado por la lógica de control.

### Modo LIMITADO
- El avance del cilindro se detiene al activarse el sensor magnético.
- Se mantiene la presión durante el tiempo original del ciclo.
- El retroceso ocurre según la lógica global existente.
- No se modifican temporizadores ni pasos de secuencia.

---

## Organización del repositorio

```text
docs/        → Informe final de la PPS  
plc/         → Programa PLC y backups  
hmi/         → Proyecto HMI  
mecanica/    → Cálculos y simulaciones estructurales  
imagenes/    → Capturas y esquemas  
anexos/      → Documentación técnica complementaria  
