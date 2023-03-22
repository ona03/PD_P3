# Procesadores digitales - Práctica 3

## Objetivo
<div align="justify">

El objetivo de esta prática consiste en comprender el funiconamiento de un sistema operativo en tiempo real. Lo haremos generando tareas, sincronizándolas y dando prioridad a unas o a otras.

## Multitarea

Lo primero que tenemos que saber es que para crear una tarea debemos seguir dos pasos: primero crear una función que contenga el código que más adelante querremos ejecutar y a continucación, crear una tarea que la llame. En nuestro caso, llamamos a la función `anotherTask`. Esta tendrá un bucle infinito con `for(;;)` que le hará imprimir por pantalla `this is another task` cada 1 segundo de reloj. Obtenemos este resultado con `delay(1000)`. Es una tarea que no se detiene a menos que lo paremos manualmente ya que con `vTaskDelete(NULL)` conseguimos que nunca llegue al final. Como no asignamos ningún período en el cual no deba ejecutarse el programa, simplemente este continúa ejecutándose. 

El código de la función es el siguiente:
 después de programar una velocidad de 115200 con `Serial.begin(115200)`, 

```
void anotherTask(void * parameter){
for(;;){
    Serial.println("this is another Task");
    delay(1000);
    }
vTaskDelete(NULL);
}
```
Con el resultado siguiente:
```
resultat
```
Seguidamente, avisamos al planificador que deberá realizar la tarea. Para hacerlo, llamamos `xTaskCreate()`, una función de FreeRTOS que permite poner a la cola la tarea deseada después de las diferentes tareas que ya están preparadas para llevarse a cabo. Dentro de esta, llamamos a nuestra función `anotherTask()` creada anteriormente, le ponemos un nombre para facilitar el trabajo, le asignamos el tamaño de la pila de tareas especificado como el número de variables que puede contener la pila, no el número de bytes.

```
xTaskCreate(
    anotherTask, /* Task function. */
    "another Task", /* name of task. */
    10000, /* Stack size of task */
    NULL, /* parameter of the task */
    1, /* priority of the task */
    NULL); /* Task handle to keep track of created task */
```