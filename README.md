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


## Resultados de Simulaciones de CPU e I/O

### CPU Utilization with `-l 5:100,5:100`

**Comando ejecutado:**
```bash
python process-run.py -l 5:100,5:100 -c -p
```

**Resultados:**

| Tiempo | PID: 0       | PID: 1       | CPU | IOs |
|--------|--------------|--------------|-----|-----|
| 1      | RUN:cpu      | READY        | 1   | 0   |
| 2      | RUN:cpu      | READY        | 1   | 0   |
| 3      | RUN:cpu      | READY        | 1   | 0   |
| 4      | RUN:cpu      | READY        | 1   | 0   |
| 5      | RUN:cpu      | READY        | 1   | 0   |
| 6      | DONE         | RUN:cpu      | 1   | 0   |
| 7      | DONE         | RUN:cpu      | 1   | 0   |
| 8      | DONE         | RUN:cpu      | 1   | 0   |
| 9      | DONE         | RUN:cpu      | 1   | 0   |
| 10     | DONE         | RUN:cpu      | 1   | 0   |

**Estadísticas finales:**
- **Tiempo total de ejecución:** 10 ticks
- **CPU ocupado:** 10 ticks (100% de utilización)
- **I/O ocupado:** 0 ticks (0% de utilización)

**Explicación:**
- **Procesos puramente CPU-bound:**
   - Ambos procesos (PID: 0 y PID: 1) tienen 5 instrucciones y ninguna operación de I/O (100% CPU).
   - No hay tiempos de espera ni alternancia forzada por E/S.
   
- **Secuencia de ejecución:**
   - PID: 0 ocupa el CPU los primeros 5 ticks.
   - PID: 1 ocupa el CPU los siguientes 5 ticks (ticks 6-10).
   - Ningún tick queda inactivo: El CPU siempre está procesando.

- **Eficiencia máxima:**
   - Al no haber overhead por cambios de contexto ni esperas, la CPU alcanza un 100% de uso.

**Conclusión:**
La utilización del CPU es del 100%, como era previsible teóricamente para cargas de trabajo puramente computacionales sin interrupciones.

---

### 2. CPU vs I/O: `-l 4:100,1:0`

**Comando ejecutado:**
```bash
python process-run.py -l 4:100,1:0 -c -p
```

**Resultados:**

| Tiempo | PID: 0       | PID: 1       | CPU | IOs |
|--------|--------------|--------------|-----|-----|
| 1      | RUN:cpu      | READY        | 1   | 0   |
| 2      | RUN:cpu      | READY        | 1   | 0   |
| 3      | RUN:cpu      | READY        | 1   | 0   |
| 4      | RUN:cpu      | READY        | 1   | 0   |
| 5      | DONE         | RUN:io       | 1   | 0   |
| 6      | DONE         | WAITING      | 0   | 1   |
| 7      | DONE         | WAITING      | 0   | 1   |
| 8      | DONE         | WAITING      | 0   | 1   |
| 9      | DONE         | WAITING      | 0   | 1   |
| 10     | DONE         | WAITING      | 0   | 1   |
| 11     | DONE         | RUN:io_done  | 1   | 0   |

**Estadísticas finales:**
- **Tiempo total de ejecución:** 11 ticks
- **CPU ocupado:** 6 ticks (54.55% de utilización)
- **I/O ocupado:** 5 ticks (45.45% de utilización)

**Explicación:**
- **Proceso 0 (CPU-bound):**
   - Ejecuta 4 instrucciones de CPU seguidas (ticks 1-4).
   - Termina en el tick 5 (marca DONE).

- **Proceso 1 (I/O-bound):**
   - En el tick 5, inicia una operación de I/O (`RUN:io`), que tarda 5 ticks en completarse (simulando una espera).
   - Durante los ticks 6-10, el proceso está en estado `WAITING` (I/O ocupado).
   - En el tick 11, se completa la E/S (`RUN:io_done`).

**Impacto en la CPU:**
- **Tiempo inactivo:** Durante los ticks 6-10, el CPU no está en uso porque el único proceso activo está esperando por I/O.
- **Eficiencia reducida:** La CPU solo trabaja el 54.55% del tiempo total (6/11 ticks).

**Conclusión:**
- **Tiempo total:** 11 ticks (más lento que un caso puramente CPU-bound debido a la espera de E/S).
- **Subutilización del CPU:** La operación de I/O bloqueante introduce tiempos muertos, reduciendo la eficiencia.

---
### 3.Cambiando el orden: `-l 1:0,4:100`

**Comando ejecutado:**
```bash
python process-run.py -l 1:0,4:100 -c -p
```

**Resultados:**

| Tiempo | PID: 0 (I/O) | PID: 1 (CPU) | CPU | IOs |
|--------|--------------|--------------|-----|-----|
| 1      | RUN:io       | READY        | 1   | 0   |
| 2      | BLOCKED      | RUN:cpu      | 1   | 1   |
| 3      | BLOCKED      | RUN:cpu      | 1   | 1   |
| 4      | BLOCKED      | RUN:cpu      | 1   | 1   |
| 5      | BLOCKED      | RUN:cpu      | 1   | 1   |
| 6      | BLOCKED      | DONE         | 0   | 1   |
| 7      | RUN:io_done  | DONE         | 1   | 0   |

**Estadísticas finales:**
- **Tiempo total de ejecución:** 7 ticks (vs. 11 en el orden inverso).
- **CPU ocupado:** 6 ticks (85.71% de utilización).
- **I/O ocupado:** 5 ticks (71.43% de utilización).

**Explicación:**
- **Proceso 0 (I/O-bound) primero:**
   - **Tick 1:** Inicia una operación de I/O (`RUN:io`), que se bloquea inmediatamente.
   - **Ticks 2-6:** Mientras el I/O está en progreso (`BLOCKED`), el Proceso 1 (CPU-bound) aprovecha para ejecutar sus 4 instrucciones (ticks 2-5).

- **Solapamiento CPU/I/O:**
   - La CPU nunca está inactiva: Mientras el I/O espera (ticks 2-6), el Proceso 1 usa el CPU.
   - **Tick 7:** El I/O termina (`RUN:io_done`), finalizando la ejecución.

**¿Por qué mejora el rendimiento?**
- **Orden anterior (`-l 4:100,1:0`):**
   - CPU ejecuta primero, luego I/O espera sin solapamiento → CPU inactiva 5 ticks.
- **Nuevo orden (`-l 1:0,4:100`):**
   - I/O se inicia primero, permitiendo que el CPU-bound se ejecute en paralelo con la espera de E/S.

**Conclusión:**
- **El orden importa:** Ejecutar el proceso I/O primero permite solapar computación y E/S, reduciendo el tiempo total de 11 a 7 ticks.
- **Eficiencia mejorada:** La CPU trabaja el 85.71% del tiempo (vs. 54.55% en el orden inverso).

---

### 4.Análisis con Política SWITCH_ON_END (`-l 1:0,4:100 -S SWITCH_ON_END`)

**Comando ejecutado:**
```bash
python process-run.py -l 1:0,4:100 -c -p -S SWITCH_ON_END
```

**Resultados:**

| Tiempo | PID: 0 (I/O) | PID: 1 (CPU) | CPU | IOs |
|--------|--------------|--------------|-----|-----|
| 1      | RUN:io       | READY        | 1   | 0   |
| 2      | BLOCKED      | READY        | 0   | 1   |
| 3      | BLOCKED      | READY        | 0   | 1   |
| 4      | BLOCKED      | READY        | 0   | 1   |
| 5      | BLOCKED      | READY        | 0   | 1   |
| 6      | BLOCKED      | READY        | 0   | 1   |
| 7      | RUN:io_done  | READY        | 1   | 0   |
| 8      | DONE         | RUN:cpu      | 1   | 0   |
| 9      | DONE         | RUN:cpu      | 1   | 0   |
| 10     | DONE         | RUN:cpu      | 1   | 0   |
| 11     | DONE         | RUN:cpu      | 1   | 0   |

**Estadísticas finales:**
- **Tiempo total:** 11 ticks
- **CPU ocupado:** 6 ticks (54.55% de utilización)
- **I/O ocupado:** 5 ticks (45.45% de utilización)

---

**Explicación detallada:**

**Política SWITCH_ON_END:**
- No permite cambios de contexto durante E/S: El sistema espera a que el proceso en E/S termine completamente antes de asignar la CPU a otro proceso.
- **Consecuencia:** El proceso CPU-bound (PID: 1) no puede ejecutarse mientras PID: 0 está bloqueado en E/S.

**Secuencia de eventos:**
1. **Tick 1:** PID: 0 inicia E/S (`RUN:io`), luego se bloquea (`BLOCKED`).
2. **Ticks 2-6:** PID: 0 espera la E/S (CPU inactiva).
3. **Tick 7:** PID: 0 completa la E/S (`RUN:io_done`).
4. **Ticks 8-11:** PID: 1 ejecuta sus 4 instrucciones de CPU secuencialmente.

---

**Comparación con SWITCH_ON_IO (caso anterior):**

| Métrica          | SWITCH_ON_IO (7 ticks) | SWITCH_ON_END (11 ticks) |
|-------------------|------------------------|--------------------------|
| **Tiempo total**  | Más rápido (solapamiento) | Más lento (secuencial)  |
| **CPU útil**      | 85.71%                | 54.55%                  |
| **Eficiencia**    | Óptima                | Subóptima               |

**Problema clave:**
- **Inactividad forzada:** La CPU permanece ociosa durante los 5 ticks de E/S porque la política no permite asignarla a PID: 1.

---

**Conclusión:**
- **Impacto del orden de políticas:**
   - `SWITCH_ON_IO` (por defecto) maximiza la utilización al permitir solapamiento.
   - `SWITCH_ON_END` degrada el rendimiento al serializar las operaciones.
- **Uso en sistemas reales:**
   - Políticas como `SWITCH_ON_END` son raras, ya que desperdician recursos. Se usan en casos específicos (ej.: depuración o sistemas sin multitarea).
---

### 5. Análisis con Política SWITCH_ON_IO (`-l 1:0,4:100 -S SWITCH_ON_IO`)

**Comando ejecutado:**
```bash
python process-run.py -l 1:0,4:100 -c -p -S SWITCH_ON_IO
```

**Resultados:**

| Tiempo | PID: 0 (I/O) | PID: 1 (CPU) | CPU | IOs |
|--------|--------------|--------------|-----|-----|
| 1      | RUN:io       | READY        | 1   | 0   |
| 2      | BLOCKED      | RUN:cpu      | 1   | 1   |
| 3      | BLOCKED      | RUN:cpu      | 1   | 1   |
| 4      | BLOCKED      | RUN:cpu      | 1   | 1   |
| 5      | BLOCKED      | RUN:cpu      | 1   | 1   |
| 6      | BLOCKED      | DONE         | 0   | 1   |
| 7      | RUN:io_done  | DONE         | 1   | 0   |

**Estadísticas finales:**
- **Tiempo total:** 7 ticks (óptimo).
- **CPU ocupado:** 6 ticks (85.71% de utilización).
- **I/O ocupado:** 5 ticks (71.43% de utilización).

---

**Explicación detallada:**

**Política SWITCH_ON_IO:**
- **Cambio de contexto durante E/S:** Cuando un proceso se bloquea en E/S (`BLOCKED`), el scheduler inmediatamente asigna la CPU a otro proceso listo.
- **Objetivo:** Evitar tiempos muertos en la CPU.

**Secuencia de eventos:**
1. **Tick 1:** PID: 0 inicia E/S (`RUN:io`) y se bloquea.
2. **Ticks 2-5:** Mientras PID: 0 espera E/S, la CPU ejecuta todas las instrucciones de PID: 1 (proceso CPU-bound).
3. **Tick 6:** PID: 1 termina (`DONE`), y PID: 0 sigue en E/S.
4. **Tick 7:** PID: 0 completa la E/S (`RUN:io_done`).

---

**Eficiencia mejorada vs SWITCH_ON_END:**
- **Tiempo total reducido:** De 11 ticks a 7 ticks (ganancia del 36%).
- **CPU activa:** 85.71% del tiempo (vs. 54.55% en SWITCH_ON_END).

**Solapamiento CPU/E/S:**
- La clave del rendimiento es que la CPU nunca está inactiva:
   - Mientras PID: 0 espera E/S (ticks 2-6), PID: 1 usa la CPU.

---

**Comparación de políticas:**

| Política         | Tiempo Total | CPU Utilizado | Comportamiento clave                  |
|-------------------|--------------|---------------|---------------------------------------|
| **SWITCH_ON_IO**  | 7 ticks      | 85.71%        | Solapamiento de CPU y E/S.            |
| **SWITCH_ON_END** | 11 ticks     | 54.55%        | Espera pasiva durante E/S.            |

---

**Conclusión:**
- **SWITCH_ON_IO** es superior en sistemas con operaciones de E/S bloqueantes, ya que maximiza la utilización de recursos.
- **Caso de uso típico:** Sistemas operativos modernos (Linux, Windows) usan políticas similares para evitar inactividad innecesaria.

---

### 6.Análisis con Política IO_RUN_LATER (`-l 3:0,5:100,5:100,5:100 -S SWITCH_ON_IO -I IO_RUN_LATER`)

**Comando ejecutado:**
```bash
python process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH_ON_IO -c -p -I IO_RUN_LATER
```

**Resultados clave:**
- **Tiempo total:** 31 ticks
- **CPU ocupado:** 21 ticks (67.74% de utilización).
- **I/O ocupado:** 15 ticks (48.39% de utilización).

---

**Explicación detallada:**

**Configuración del escenario:**
- **Proceso 0 (I/O-bound):** 3 instrucciones de E/S (`3:0`).
- **Procesos 1, 2, 3 (CPU-bound):** 5 instrucciones de CPU cada uno (`5:100`).

**Políticas activas:**
- **SWITCH_ON_IO:** Cambia a otro proceso cuando uno se bloquea en E/S.
- **IO_RUN_LATER:** No reanuda inmediatamente los procesos que completan E/S.

**Comportamiento observado:**
- Cuando el Proceso 0 completa una E/S, **no se reanuda de inmediato**. En su lugar, los procesos CPU-bound siguen ejecutándose hasta terminar.
- Esto genera **retrasos acumulativos**:
   - El Proceso 0 debe esperar a que los procesos CPU-bound liberen la CPU, incluso después de que sus E/S hayan terminado.

**Ineficiencias críticas:**
- **Subutilización de CPU:** Aunque hay solapamiento inicial entre E/S y CPU, la política IO_RUN_LATER evita que el proceso I/O-bound use la CPU rápidamente después de cada E/S.
- **Tiempo muerto adicional:** La CPU permanece inactiva en momentos donde el Proceso 0 podría estar ejecutando nuevas E/S.

---

**Comparación con IO_RUN_IMMEDIATE (óptimo):**
- Con **IO_RUN_IMMEDIATE**, el Proceso 0 se reanudaría tan pronto como completara una E/S, reduciendo el tiempo total.
- En este caso, el tiempo aumentó un **~24%** respecto a un escenario optimizado (estimado en ~25 ticks).

| **Métrica**       | **IO_RUN_LATER (31 ticks)** | **IO_RUN_IMMEDIATE (estimado)** |
|--------------------|-----------------------------|----------------------------------|
| **Tiempo total**   | 31 ticks                   | ~25 ticks                       |
| **CPU útil**       | 67.74%                     | ~84%                            |
| **E/S útil**       | 48.39%                     | ~60%                            |

---

**Conclusión:**
- **IO_RUN_LATER** degrada el rendimiento al introducir retardos innecesarios en la reanudación de procesos I/O-bound.
- **Uso típico:** Esta política podría simular sistemas con prioridades estáticas (donde los procesos CPU-bound tienen mayor prioridad), pero no es eficiente en cargas de trabajo mixtas (CPU + E/S).
- **Recomendación:** En sistemas reales, se prefieren políticas que reanuden procesos I/O-bound rápidamente (ej.: **IO_RUN_IMMEDIATE**) para equilibrar el uso de recursos.

---

### 7.Análisis con Política IO_RUN_IMMEDIATE (`-l 3:0,5:100,5:100,5:100 -S SWITCH_ON_IO -I IO_RUN_IMMEDIATE`)

**Comando ejecutado:**
```bash
python process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH_ON_IO -c -p -I IO_RUN_IMMEDIATE
```

**Resultados clave:**
- **Tiempo total:** 21 ticks (vs. 31 en IO_RUN_LATER).
- **CPU ocupado:** 21 ticks (100% de utilización).
- **I/O ocupado:** 15 ticks (71.43% de utilización).

---

**Explicación detallada:**

1. **Comportamiento de IO_RUN_IMMEDIATE:**
   - **Reanudación inmediata:** Cuando un proceso completa una operación de E/S, el scheduler lo ejecuta inmediatamente, interrumpiendo cualquier proceso CPU-bound en curso.
   - **Objetivo:** Minimizar el tiempo de respuesta de procesos I/O-bound y maximizar el solapamiento CPU/E/S.

2. **Secuencia de eventos clave:**
   - **Proceso 0 (I/O-bound):**
     - Ejecuta 3 operaciones de E/S, cada una con 5 ticks de espera.
     - Tras cada E/S, recupera la CPU de inmediato para iniciar la siguiente.
   - **Procesos 1-3 (CPU-bound):**
     - Se ejecutan solo cuando el Proceso 0 está bloqueado en E/S.
     - Sus instrucciones se intercalan eficientemente con las esperas de E/S.

3. **Eficiencia vs IO_RUN_LATER:**

| **Métrica**       | **IO_RUN_IMMEDIATE (21 ticks)** | **IO_RUN_LATER (31 ticks)** | **Mejora** |
|--------------------|----------------------------------|-----------------------------|------------|
| **Tiempo total**   | 21 ticks                       | 31 ticks                    | ✅ 32% más rápido |
| **CPU útil**       | 100%                           | 67.74%                      | ✅ |
| **I/O útil**       | 71.43%                         | 48.39%                      | ✅ |

4. **¿Por qué es óptimo?**
   - **Evita inactividad:** La CPU nunca espera innecesariamente.
   - **Prioriza interactividad:** Los procesos I/O-bound (ej.: servidores, interfaces de usuario) responden más rápido.
   - **Equilibrio CPU/E/S:** El solapamiento reduce el tiempo total.

---

**Conclusión:**
- **IO_RUN_IMMEDIATE** es ideal para cargas de trabajo mixtas (CPU + E/S), ya que:
  - **Maximiza la utilización del CPU:** 100%.
  - **Reduce el tiempo total:** 21 ticks (vs. 31 en IO_RUN_LATER).
- **Caso de uso típico:** Sistemas operativos generales (Linux, Windows) usan políticas similares para garantizar equidad y rendimiento.


### Criterios de evaluación
- [x] Despligue de los resultados y analisis claro de los resultados respecto a lo visto en la teoria.
- [x] Creatividad y orden.