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

## 1. Run process-run.py with the following flags: -l 5:100,5:100. What should the CPU utilization be (e.g., the percent of time the CPU is in use?) Why do you know this? Use the -c and -p flags to see if you were right.

**Comando:**
```bash
python process-run.py -l 5:100,5:100 -c -p
```

**Salida:**
```
Tiempo  PID: 0        PID: 1        CPU        IOs
  1     RUN:cpu       READY          1
  2     RUN:cpu       READY          1
  3     RUN:cpu       READY          1
  4     RUN:cpu       READY          1
  5     RUN:cpu       READY          1
  6     DONE          RUN:cpu        1
  7     DONE          RUN:cpu        1
  8     DONE          RUN:cpu        1
  9     DONE          RUN:cpu        1
 10     DONE          RUN:cpu        1
```

**Estadísticas:**
- Tiempo total: 10 ticks
- CPU ocupado: 10 ticks (100%)
- I/O ocupado: 0 ticks (0%)

**Explicación:**
Ambos procesos son intensivos en CPU con 5 instrucciones cada uno. Se ejecutan secuencialmente sin esperas de I/O, logrando una utilización del CPU del 100%.

---

## 2. Now run with these flags: ./process-run.py -l 4:100,1:0. These flags specify one process with 4 instructions (all to use the CPU), and one that simply issues an I/O and waits for it to be done. How long does it take to complete both processes? Use -c and -p to find out if you were right.

**Comando:**
```bash
python process-run.py -l 4:100,1:0 -c -p
```

**Salida:**
```
Tiempo  PID: 0        PID: 1        CPU        IOs
  1     RUN:cpu       READY          1
  2     RUN:cpu       READY          1
  3     RUN:cpu       READY          1
  4     RUN:cpu       READY          1
  5     DONE          RUN:io         1
  6     DONE          WAITING                   1
  7     DONE          WAITING                   1
  8     DONE          WAITING                   1
  9     DONE          WAITING                   1
 10     DONE          WAITING                   1
 11     DONE          RUN:io_done     1
```

**Estadísticas:**
- Tiempo total: 11 ticks
- CPU ocupado: 6 ticks (54.55%)
- I/O ocupado: 5 ticks (45.45%)

**Explicación:**
El proceso intensivo en CPU se ejecuta primero. Luego se realiza la operación de I/O que bloquea durante 5 ticks. Esto genera tiempo inactivo del CPU durante la espera.

---

## 3. Switch the order of the processes: -l 1:0,4:100. What happens now? Does switching the order matter? Why? (As always, use -c and -p to see if you were right)

**Comando:**
```bash
python process-run.py -l 1:0,4:100 -c -p
```

**Salida:**
```
Tiempo  PID: 0        PID: 1        CPU        IOs
  1     RUN:io        READY          1
  2     BLOCKED       RUN:cpu        1          1
  3     BLOCKED       RUN:cpu        1          1
  4     BLOCKED       RUN:cpu        1          1
  5     BLOCKED       RUN:cpu        1          1
  6     BLOCKED       DONE                       1
  7*    RUN:io_done   DONE           1
```

**Estadísticas:**
- Tiempo total: 7 ticks
- CPU ocupado: 6 ticks (85.71%)
- I/O ocupado: 5 ticks (71.43%)

**Explicación:**
Ejecutar la operación de I/O primero permite que el proceso de CPU se ejecute durante la espera de I/O, mejorando la utilización del CPU y reduciendo el tiempo total.

---

## 4. We'll now explore some of the other flags. One important flag is -S, which determines how the system reacts when a process issues an I/O. With the flag set to SWITCH ON END, the system will NOT switch to another process while one is doing I/O, instead waiting until the process is completely finished. What happens when you run the following two processes (-l 1:0,4:100 -c -S SWITCH ON END), one doing I/O and the other doing CPU work?

**Comando:**
```bash
python process-run.py -l 1:0,4:100 -c -p -S SWITCH_ON_END
```

**Salida:**
```
Tiempo  PID: 0        PID: 1        CPU        IOs
  1     RUN:io        READY          1
  2     BLOCKED       READY                    1
  3     BLOCKED       READY                    1
  4     BLOCKED       READY                    1
  5     BLOCKED       READY                    1
  6     BLOCKED       READY                    1
  7*    RUN:io_done   READY          1
  8     DONE          RUN:cpu        1
  9     DONE          RUN:cpu        1
 10     DONE          RUN:cpu        1
 11     DONE          RUN:cpu        1
```

**Estadísticas:**
- Tiempo total: 11 ticks
- CPU ocupado: 6 ticks (54.55%)
- I/O ocupado: 5 ticks (45.45%)

**Explicación:**
La política SWITCH_ON_END evita el cambio de proceso durante I/O, resultando en ejecución secuencial y menor utilización del CPU comparado con SWITCH_ON_IO.

---

## 5. Now, run the same processes, but with the switching behavior set to switch to another process whenever one is WAITING for I/O (-l 1:0,4:100 -c -S SWITCH ON IO). What happens now? Use -c and -p to confirm that you are right.

**Comando:**
```bash
python process-run.py -l 1:0,4:100 -c -p -S SWITCH_ON_IO
```

**Salida:**
```
Tiempo  PID: 0        PID: 1        CPU        IOs
  1     RUN:io        READY          1
  2     BLOCKED       RUN:cpu        1          1
  3     BLOCKED       RUN:cpu        1          1
  4     BLOCKED       RUN:cpu        1          1
  5     BLOCKED       RUN:cpu        1          1
  6     BLOCKED       DONE                     1
  7*    RUN:io_done   DONE           1
```

**Estadísticas:**
- Tiempo total: 7 ticks
- CPU ocupado: 6 ticks (85.71%)
- I/O ocupado: 5 ticks (71.43%)

**Explicación:**
SWITCH_ON_IO permite cambio de proceso durante espera de I/O, maximizando la utilización del CPU.

---

## 6. One other important behavior is what to do when an I/O completes. With -I IO RUN LATER, when an I/O completes, the process that issued it is not necessarily run right away; rather, whatever was running at the time keeps running. What happens when you run this combination of processes? (./process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH ON IO -c -p -I IO RUN LATER) Are system resources being effectively utilized?

**Comando:**
```bash
python process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH_ON_IO -c -p -I IO_RUN_LATER
```

**Estadísticas:**
- Tiempo total: 31 ticks
- CPU ocupado: 21 ticks (67.74%)
- I/O ocupado: 15 ticks (48.39%)

**Explicación:**
Con IO_RUN_LATER, los procesos que completan I/O no se reanudan inmediatamente, generando tiempo inactivo y menor eficiencia.

---

## 7. Now run the same processes, but with -I IO RUN IMMEDIATE set, which immediately runs the process that issued the I/O. How does this behavior differ? Why might running a process that just completed an I/O again be a good idea?

**Comando:**
```bash
python process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH_ON_IO -c -p -I IO_RUN_IMMEDIATE
```

**Estadísticas:**
- Tiempo total: 21 ticks
- CPU ocupado: 21 ticks (100%)
- I/O ocupado: 15 ticks (71.43%)

**Explicación:**
IO_RUN_IMMEDIATE permite reanudar el proceso inmediatamente después de completar I/O, maximizando la utilización del CPU y reduciendo el tiempo total de ejecución en un 32% respecto a IO_RUN_LATER.




### Criterios de evaluación
- [x] Despligue de los resultados y analisis claro de los resultados respecto a lo visto en la teoria.
- [x] Creatividad y orden.