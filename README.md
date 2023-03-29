# Procesadores digitales - Práctica 3

## Objetivo
<div align="justify">

El objetivo de esta prática consiste en comprender el funiconamiento de un sistema operativo en tiempo real. Lo haremos generando tareas, sincronizándolas y dando prioridad a unas o a otras.

## Multitarea

Lo primero que tenemos que saber es que para crear una tarea debemos seguir dos pasos: primero crear una función que contenga el código que más adelante querremos ejecutar y a continucación, crear una tarea que la llame. En nuestro caso, llamamos a la función `anotherTask`. Esta tendrá un bucle infinito con `for(;;)` que le hará imprimir por pantalla `this is another task` cada 1 segundo de reloj. Obtenemos este resultado con `delay(1000)`. Es una tarea que no se detiene a menos que lo paremos manualmente ya que con `vTaskDelete(NULL)` conseguimos que nunca llegue al final. Como no asignamos ningún período en el cual no deba ejecutarse el programa, simplemente este continúa ejecutándose. 

```
void anotherTask(void * parameter){
for(;;){
    Serial.println("this is another Task");
    delay(1000);
    }
vTaskDelete(NULL);
}
```

Seguidamente, avisamos al planificador que deberá realizar la tarea. Para hacerlo, llamamos `xTaskCreate()`, una función de FreeRTOS que permite poner a la cola la tarea deseada después de las diferentes tareas que ya están preparadas para llevarse a cabo. Dentro de esta, llamamos a nuestra función `anotherTask()` creada anteriormente, le ponemos un nombre para facilitar el trabajo, le asignamos el tamaño de la pila de tareas especificado como el número de variables que puede contener la pila. No debe cofundirse con el número de bytes, en este caso tenemos una medida de pila de `10000`. Además, para ejecutar la tarea no necesitamos pasarle ningun parámetro por lo que ponemos a `NULL` su valor, igual que en el último campo, que también lo establecemos en `NULL` al ser opcional ya que no creamos un identificador a la tarea creada fuera de la función `xTaskCreate()`. El `1` entre esos dos campos que declaramos nulos, se refiere a la prioridad que le damos a la tarea. En el caso que nos ocupa, no tiene un gran valor ya que tan solo ejecutamos una sola tarea pero se tiene que tener en cuenta que no siempre, en realidad en la mayoría de los casos, tendremos más de una tarea a ejecutar y el planificador necesitará saber en qué orden deberá realizar cada una de ellas. Curiosamente, la prioridad que le damos va a ser mayor como más grande sea el número, es decir, una prioridad de 2 está por encima que una de 1. El código a implementar es el siguiente, dentro del `setup()`:

```
void setup(){
xTaskCreate(
    anotherTask,
    "another Task",
    10000, 
    NULL, 
    1, 
    NULL);
}
```
Finalmente, en el `loop()` tan solo imprimos por pantalla `this is ESP32 Task` y lo hacemos con un retraso de `delay(1000)`, es decir, lo sacamos por pantalla cada segundo. Juntando todo lo explicado, nos queda un código resultante como el siguiente:

```
#include <Arduino.h>

void anotherTask(void * parameter);

void setup(){
Serial.begin(112500);
xTaskCreate(
    anotherTask, 
    "another Task",
    10000, 
    NULL,
    1, 
    NULL); 
}

void loop(){
    Serial.println("this is ESP32 Task");
    delay(1000);
}

void anotherTask( void * parameter ){
for(;;){
    Serial.println("this is another Task");
    delay(1000);
    }

vTaskDelete( NULL );
}
```
Con un resultado que va alternando ambos mensajes ya que se ejecuta el bucle, la tarea y vuelve a empezar dado que programamos un bucle infinito.
```
this is ESP32 Task
this is another Task
this is ESP32 Task
this is another Task
this is ESP32 Task
...
```
