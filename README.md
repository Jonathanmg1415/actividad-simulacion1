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

1. Run `process-run.py` with the following flags: `-l 5:100,5:100`. What should the CPU utilization be (e.g., the percent of time the CPU is in use?) Why do you know this? Use the `-c` and `-p` flags to see if you were right.
   
   <details>
   <summary>Answer</summary>
   2 procesos (PID 0 y PID 1)

   Cada proceso tiene exactamente 5 instrucciones

   El 100% de las instrucciones son de CPU (no hay operaciones de I/O)

   Al ejecutar el comando completo: `python process-run.py -l 5:100,5:100 -c -p` se obtuvo

      ```
      Time        PID: 0        PID: 1           CPU           IOs
         1        RUN:cpu         READY             1
         2        RUN:cpu         READY             1
         3        RUN:cpu         READY             1
         4        RUN:cpu         READY             1
         5        RUN:cpu         READY             1
         6           DONE       RUN:cpu             1
         7           DONE       RUN:cpu             1
         8           DONE       RUN:cpu             1
         9           DONE       RUN:cpu             1
      10           DONE       RUN:cpu             1

      Stats: Total Time 10
      Stats: CPU Busy 10 (100.00%)
      Stats: IO Busy  0 (0.00%)
      ```
   </details>
   <br>

2. Now run with these flags: `./process-run.py -l 4:100,1:0`. These flags specify one process with 4 instructions (all to use the CPU), and one that simply issues an I/O and waits for it to be done. How long does it take to complete both processes? Use `-c` and `-p` to find out if you were right. 
   
   <details>
   <summary>Answer</summary>

   **Proceso 0**: 4 instrucciones (100% CPU - solo operaciones de cálculo).<br>  
  
   **Proceso 1**: 1 instrucción (0% CPU - 100% I/O, es decir, solo hace una operación de E/S).  

   **Verificación con `-c` y `-p`**  
   Ejecutando: `python process-run.py -l 4:100,1:0 -c -p`  

   **Resultado obtenido**:  
   - **Tiempos 1-4**: El proceso 0 ejecuta sus 4 instrucciones de CPU.  
   - **Tiempo 5**: El proceso 1 inicia su operación I/O y se bloquea.  
   - **Tiempos 6-10**: El sistema espera a que la E/S termine.  
   - **Tiempo 11**: La E/S termina y el proceso 1 completa su instrucción `io_done`.  

   **Total**: 11 unidades de tiempo.  

   **Salida esperada**:  

   ```
   Time  PID: 0        PID: 1           CPU           IOs
      1  RUN:cpu       READY               1              
      2  RUN:cpu       READY               1              
      3  RUN:cpu       READY               1              
      4  RUN:cpu       READY               1              
      5  DONE         RUN:io              1              
      6  DONE         WAITING                            1
      7  DONE         WAITING                            1
      8  DONE         WAITING                            1
      9  DONE         WAITING                            1
     10  DONE         WAITING                            1
     11  DONE         RUN:io_done         1              

   Stats: Total Time 11
   Stats: CPU Busy 6 (54.55%)
   Stats: IO Busy  5 (45.45%)
   ```
 </details>
   <br>

1. Switch the order of the processes: `-l 1:0,4:100`. What happens now? Does switching the order matter? Why? (As always, use `-c` and `-p` to see if you were right)
   
   <details>
   <summary>Answer</summary>
   Coloque aqui su respuerta
   </details>
   <br>

2. We'll now explore some of the other flags. One important flag is `-S`, which determines how the system reacts when a process issues an I/O. With the flag set to SWITCH ON END, the system will NOT switch to another process while one is doing I/O, instead waiting until the process is completely finished. What happens when you run the following two processes (`-l 1:0,4:100 -c -S SWITCH ON END`), one doing I/O and the other doing CPU work?
   
   <details>
   <summary>Answer</summary>
   Coloque aqui su respuerta
   </details>
   <br>

3. Now, run the same processes, but with the switching behavior set to switch to another process whenever one is WAITING for I/O (`-l 1:0,4:100 -c -S SWITCH ON IO`). What happens now? Use `-c` and `-p` to confirm that you are right.
   
   <details>
   <summary>Answer</summary>
   Coloque aqui su respuerta
   </details>
   <br>

4. One other important behavior is what to do when an I/O completes. With `-I IO RUN LATER`, when an I/O completes, the process that issued it is not necessarily run right away; rather, whatever was running at the time keeps running. What happens when you run this combination of processes? (`./process-run.py -l 3:0,5:100,5:100,5:100 -S SWITCH ON IO -c -p -I IO RUN LATER`) Are system resources being effectively utilized?
   
   <details>
   <summary>Answer</summary>
   Coloque aqui su respuerta
   </details>
   <br>

5. Now run the same processes, but with `-I IO RUN IMMEDIATE` set, which immediately runs the process that issued the I/O. How does this behavior differ? Why might running a process that just completed an I/O again be a good idea?
   
   <details>
   <summary>Answer</summary>
   Coloque aqui su respuerta
   </details>
   <br>


### Criterios de evaluación
- [x] Despligue de los resultados y analisis claro de los resultados respecto a lo visto en la teoria.
- [x] Creatividad y orden.