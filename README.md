# Actividad de seguimiento - Simulación 1

|Integrante|correo|usuario github|
|---|---|---|
|Jonathan Mazo González|jonathan.mazog@udea.edu.co|Jonathanmg1415|


## Instrucciones

Antes de empezar a realizar esta actividad haga un **fork** de este repositorio y sobre este trabaje en la solución de las preguntas planteadas en la actividad de simulación. Las respuestas deben ser respondidas en español o si lo prefiere en ingles en el lugar señalado para ello (La palabra **answer** muestra donde).

**Importante**:
* Como la actividad es en las parejas del laboratorio, solo uno de los integrantes tiene que hacer el fork; y sobre repositorio bifurcado que se genera, la modificación se realiza en equipo.
* Como la entrega se debe hacer modificando el archivo READNE, se recomienda que consulte mas sobre el lenguaje **Markdown**. En el repo adjuntan dos cheatsheet ([cheat sheet 1](Markdown_Cheat_Sheet.pdf), [cheatsheet 2](markdown-cheatsheet.pdf)) para consulta rapida.
* Entre mas creativo mejor.

## Homework (Simulation)

This program, [`process-run.py`](process-run.py), allows you to see how process states change as programs run and either use the CPU (e.g., perform an add instruction) or do I/O (e.g., send a request to a disk and wait for it to complete). See the [README](https://github.com/remzi-arpacidusseau/ostep-homework/blob/master/cpu-intro/README.md) for details.

### Questions

### CPU Utilization with `-l 5:100,5:100`

# Resultados de Simulaciones de CPU e I/O

---
# Análisis de Simulaciones de Planificación de Procesos

Este documento contiene los resultados y explicaciones de diferentes simulaciones realizadas con el script `process-run.py` para analizar la utilización del CPU, el comportamiento de E/S, y diferentes políticas de cambio de contexto.

---

## 1. Simulación: `-l 5:100,5:100`

**Comando:**
```bash
python process-run.py -l 5:100,5:100 -c -p
```

**Tabla de Ejecución:**

| Tiempo | PID: 0    | PID: 1  | CPU | IOs |
|--------|-----------|---------|-----|-----|
| 1      | RUN:cpu   | READY   | 1   |     |
| 2      | RUN:cpu   | READY   | 1   |     |
| 3      | RUN:cpu   | READY   | 1   |     |
| 4      | RUN:cpu   | READY   | 1   |     |
| 5      | RUN:cpu   | READY   | 1   |     |
| 6-10   | DONE      | RUN:cpu | 1   |     |

**Estadísticas:**
- Tiempo total: **10 ticks**
- CPU ocupado: **10 ticks (100%)**
- I/O ocupado: **0 ticks (0%)**

**Explicación:** Ambos procesos son intensivos en CPU. La CPU está totalmente utilizada.

---

## 2. Simulación: `-l 4:100,1:0`

**Comando:**
```bash
python process-run.py -l 4:100,1:0 -c -p
```

| Tiempo | PID: 0     | PID: 1    | CPU | IOs |
|--------|------------|-----------|-----|-----|
| 1-4    | RUN:cpu    | READY     | 1   |     |
| 5      | DONE       | RUN:io    | 1   |     |
| 6-10   | DONE       | WAITING   |     | 1   |
| 11     | DONE       | RUN:io_done | 1 |     |

**Estadísticas:**
- Tiempo total: **11 ticks**
- CPU ocupado: **6 ticks (54.55%)**
- I/O ocupado: **5 ticks (45.45%)**

**Explicación:** El CPU queda inactivo durante la espera de E/S.

---

## 3. Simulación: `-l 1:0,4:100`

**Comando:**
```bash
python process-run.py -l 1:0,4:100 -c -p
```

| Tiempo | PID: 0    | PID: 1  | CPU | IOs |
|--------|-----------|---------|-----|-----|
| 1      | RUN:io    | READY   | 1   |     |
| 2-5    | BLOCKED   | RUN:cpu | 1   | 1   |
| 6      | BLOCKED   | DONE    |     | 1   |
| 7*     | RUN:io_done | DONE  | 1   |     |

**Estadísticas:**
- Tiempo total: **7 ticks**
- CPU ocupado: **6 ticks (85.71%)**
- I/O ocupado: **5 ticks (71.43%)**

**Explicación:** El orden importa. Ejecutar primero I/O permite solapar ejecución de CPU.

---

## 4. Simulación con `SWITCH_ON_END`

**Comando:**
```bash
python process-run.py -l 1:0,4:100 -c -p -S SWITCH_ON_END
```

| Tiempo | PID: 0     | PID: 1   | CPU | IOs |
|--------|------------|----------|-----|-----|
| 1      | RUN:io     | READY    | 1   |     |
| 2-6    | BLOCKED    | READY    |     | 1   |
| 7*     | RUN:io_done| READY    | 1   |     |
| 8-11   | DONE       | RUN:cpu  | 1   |     |

**Estadísticas:**
- Tiempo total: **11 ticks**
- CPU ocupado: **6 ticks (54.55%)**
- I/O ocupado: **5 ticks (45.45%)**

**Explicación:** No hay cambio de proceso durante E/S. Menor eficiencia.

---

## 5. Simulación con `SWITCH_ON_IO`

**Comando:**
```bash
python process-run.py -l 1:0,4:100 -c -p -S SWITCH_ON_IO
```

| Tiempo | PID: 0    | PID: 1  | CPU | IOs |
|--------|-----------|---------|-----|-----|
| 1      | RUN:io    | READY   | 1   |     |
| 2-5    | BLOCKED   | RUN:cpu | 1   | 1   |
| 6      | BLOCKED   | DONE    |     | 1   |
| 7*     | RUN:io_done | DONE  | 1   |     |

**Estadísticas:**
- Tiempo total: **7 ticks**
- CPU ocupado: **6 ticks (85.71%)**
- I/O ocupado: **5 ticks (71.43%)**

**Explicación:** Cambio inmediato permite mejor aprovechamiento de CPU.

---

## 6. `IO_RUN_LATER` con `SWITCH_ON_IO`

**Comando:**
```bash
python process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH_ON_IO -c -p -I IO_RUN_LATER
```

**Estadísticas:**
- Tiempo total: **31 ticks**
- CPU ocupado: **21 ticks (67.74%)**
- I/O ocupado: **15 ticks (48.39%)**

**Explicación:** IO_RUN_LATER no prioriza procesos que completan E/S. Ineficiencia.

---

## 7. `IO_RUN_IMMEDIATE` con `SWITCH_ON_IO`

**Comando:**
```bash
python process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH_ON_IO -c -p -I IO_RUN_IMMEDIATE
```

**Estadísticas:**
- Tiempo total: **21 ticks**
- CPU ocupado: **21 ticks (100%)**
- I/O ocupado: **15 ticks (71.43%)**

**Explicación:** Priorizar procesos que completan E/S mejora eficiencia y reduce tiempo total en un **32%** comparado con IO_RUN_LATER.

---

> ⚡ **Conclusión general:** Las políticas de planificación y el comportamiento ante operaciones de E/S impactan significativamente en la eficiencia de la CPU y el tiempo total de ejecución.






### Criterios de evaluación
- [x] Despligue de los resultados y analisis claro de los resultados respecto a lo visto en la teoria.
- [x] Creatividad y orden.