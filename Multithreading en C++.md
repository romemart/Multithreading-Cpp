# Multithreading en C++

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [Multithreading en C++](#multithreading-en-c)
    - [1. Introducción](#1-introducción)
    - [2. Usando header \<thread>](#2-usando-header-thread)
    - [3. Sincronización Básica](#3-sincronización-básica)
    - [4. Usando header \<mutex>](#4-usando-header-mutex)
    - [5. Usando header <shared_mutex>](#5-usando-header-shared_mutex)
    - [6. Usando header \<atomic>](#6-usando-header-atomic)
    - [7. Usando header \<semaphore>](#7-usando-header-semaphore)
    - [8. Usando header <condition_variable>](#8-usando-header-condition_variable)
    - [9. Usando header \<future>](#9-usando-header-future)
    - [10. Comunicación entre Hilos](#10-comunicación-entre-hilos)
    - [11. Sincronización Avanzada](#11-sincronización-avanzada)
    - [12. Patrones de diseño](#12-patrones-de-diseño)
    - [13. Conclusiones](#13-conclusiones)

<!-- /code_chunk_output -->

### 1. Introducción

**Introducción a Tareas en Paralelo:**
En informática, las tareas en paralelo son aquellas que se ejecutan simultáneamente, ya sea en múltiples núcleos de procesador o mediante la subdivisión de una tarea en partes que pueden ejecutarse al mismo tiempo. Este enfoque se utiliza para mejorar el rendimiento y la eficiencia de los sistemas informáticos al permitir que múltiples tareas se realicen concurrentemente.

**Evolución de Multiprocesamiento a Multithreading:**
- **Multiprocesamiento:** El multiprocesamiento se refiere al uso de múltiples CPUs para ejecutar tareas de forma concurrente.
Históricamente, el multiprocesamiento ha sido la primera estrategia para lograr el paralelismo en sistemas informáticos.
Se desarrolló en las décadas de 1970 y 1980 como una solución para mejorar el rendimiento de los sistemas informáticos mediante la ejecución simultánea de múltiples procesos independientes.
- **Multithreading:** El multithreading implica dividir una tarea en subprocesos independientes que pueden ejecutarse de manera concurrente dentro de un solo proceso. Gracias a la implementación de procesadores multinucleos.
Aunque el concepto de multithreading ha existido desde las primeras etapas de la informática, su implementación práctica se popularizó con la introducción en la decada de los 90's en bibliotecas estándar como `<pthread.h>` en lenguaje C y en el año 2011 se introdujo `<thread>` en el estandar C++11.
La implementación de multithreading marcó un hito significativo al proporcionar una interfaz estándar y portátil para trabajar con threads de ejecución en programas C++.

    <p align="center">
        <img src="/threadvsprocess.png" alt="image">
    </p>

- **Recursos Compartidos (shared resources):**

    - Variables globales o estáticas compartidas entre múltiples hilos.
    - Estructuras de datos compartidas, como colas, pilas, listas enlazadas, etc.
    - Recursos de red, como sockets TCP/IP o UDP.
    - Memoria compartida entre procesos.
    - Bases de datos compartidas.
    - Recursos de hardware, como dispositivos periféricos.
    - Variables de estado de la aplicación que pueden ser modificadas por múltiples hilos.

    Al comprender la importancia de proteger los recursos compartidos, podemos abordar los desafíos del multithreading de manera más efectiva y desarrollar aplicaciones robustas y seguras en entornos concurrentes.

**Comparación Multiprocesamiento vs. Multithreading:**

| Aspecto                | Multiprocessing                                     | Multithreading                                       |
|------------------------|-----------------------------------------------------|-------------------------------------------------------|
| **Ventajas**           | - Aislamiento completo entre procesos.             | - Menor consumo de recursos en términos de memoria.  |
|                        | - Mayor estabilidad: un proceso puede fallar sin afectar a otros. | - Mayor eficiencia en la comunicación entre hilos, al compartir memoria. |
|                        | - Escalabilidad en sistemas con múltiples CPUs/núcleos. | - Menor tiempo de inicio de un nuevo hilo en comparación con un nuevo proceso. |
|                        | - Mayor seguridad, ya que los errores de un proceso no afectan a otros. |                                                       |
| **Desventajas**        | - Mayor consumo de recursos (memoria, tiempo de inicio). | - Mayor complejidad en la gestión de la concurrencia y la sincronización. |
|                        | - Mayor complejidad en la comunicación entre procesos (IPC). | - Posible aumento de la complejidad del código y la depuración debido a la concurrencia. |
|                        | - Menor eficiencia en la comunicación entre procesos debido al IPC. | - Vulnerabilidad a condiciones de carrera y problemas de sincronización. |
|                        | - Menor eficiencia en sistemas de un solo núcleo debido a la alternancia de contextos. | - Riesgo de bloqueo y problemas de rendimiento si la sincronización no se maneja adecuadamente. |

- **Diferencias entre concurrencia y paralelismo**
    La concurrencia se refiere a la ejecución simultánea de múltiples tareas, mientras que el paralelismo implica la ejecución real simultánea de estas tareas en diferentes núcleos de procesador.

- **Ejemplos de aplicaciones donde el multithreading es útil**

    - Una aplicación de reproducción de video puede utilizar hilos separados para decodificar el video, cargar los fotogramas en la memoria y mostrarlos en pantalla simultáneamente.
    - Un servidor web puede utilizar hilos para manejar múltiples solicitudes de clientes al mismo tiempo, permitiendo que el servidor atienda a varios clientes de manera concurrente.

- **Consideraciones de diseño y desafíos asociados con el multithreading**
    Es importante diseñar cuidadosamente la estructura del programa para evitar problemas de concurrencia, como condiciones de carrera y deadlocks. Además, la sincronización adecuada entre hilos y la gestión de recursos compartidos son aspectos críticos del diseño de programas multithreaded.

- **Estructura y clasificacion de la tematica Multithreading**
En este material, abordaremos los principales headers de la biblioteca estándar de C++ (Thread support library) para programación multihilo. Estos headers se pueden clasificar en tres categorías principales según su uso:

    <p align="center">
        <img src="/clasificacion.png" alt="image">
    </p>

    Este conjunto de headers cubre las principales necesidades para programar con multihilo en C++, incluyendo la creación y gestión de hilos, la sincronización de acceso a datos compartidos y la sincronización de eventos entre hilos. Sin embargo, dependiendo de tus requisitos específicos, es posible que necesites utilizar otras partes de la biblioteca estándar o incluso bibliotecas externas.

### 2. Usando header \<thread>
El header <font color="red">\<thread></font> en C++11 proporciona funcionalidades para trabajar con hilos de ejecución en programas C++. Aquí tienes una descripción general del header `<thread>` y un cuadro destacando algunas de sus clases y funciones principales:

|Contenido|Descripción|
|---------|-----------|
|thread <span style="color:green;font-size:12px">(C++11)</span>|administra un hilo separado <span style="color:green;font-size:12px">(clase)</span>|
|this_thread|proporciona funciones que acceden al hilo de ejecución actual|
|jthread <span style="color:green;font-size:12px">(C++20)</span>| `std::thread` con soporte para unión automática y cancelación <span style="color:green;font-size:12px">(clase)</span>|
|yield <span style="color:green;font-size:12px">(C++11)</span>|sugiere que la implementación reprograma la ejecución de hilos <span style="color:green;font-size:12px">(Función)</span>|
|get_id <span style="color:green;font-size:12px">(C++11)</span>|devuelve el ID del hilo del hilo actual <span style="color:green;font-size:12px">(Función)</span>|
|sleep_for <span style="color:green;font-size:12px">(C++11)</span>|detiene la ejecución del hilo actual durante una duración de tiempo especificada <span style="color:green;font-size:12px">(Función)</span>|
|sleep_until <span style="color:green;font-size:12px">(C++11)</span>|detiene la ejecución del hilo actual hasta un punto de tiempo especificado <span style="color:green;font-size:12px">(Función)</span>|

- **Usando la clase `std::thread`**
std::thread es una clase en la biblioteca estándar de C++ que se utiliza para crear y controlar hilos de ejecución en un programa. Proporciona una interfaz para crear y administrar hilos de forma eficiente.

    | Members         | Descripción                                       |
    |-----------------|---------------------------------------------------|
    |joinable|verifica si el hilo es unible, es decir, potencialmente se está ejecutando en un contexto paralelo <span style="color:green;font-size:12px">(función miembro pública)</span>|
    |join|espera a que el hilo termine su ejecución <span style="color:green;font-size:12px">(función miembro pública)</span>|
    |detach|permite que el hilo se ejecute de forma independiente del identificador de hilo <span style="color:green;font-size:12px">(función miembro pública)</span>|
    |get_id|devuelve el ID del hilo <span style="color:green;font-size:12px">(función miembro pública)</span>|
    |hardware_concurrency[static]|devuelve el número de hilos concurrentes admitidos por la implementación <span style="color:green;font-size:12px">(función miembro pública)</span>|

- **Uso de std::thread para crear hilos:**
    Para crear un hilo, se puede instanciar un objeto `std::thread` proporcionando una función o un objeto funcional como argumento. Cuando se crea un objeto `std::thread`, se inicia un nuevo hilo de ejecución y se ejecuta la función especificada.

    Ejemplo de código en C++ utilizando <thread>:
    ```cpp
    #include <iostream>
    #include <thread>

    void tarea() {
        std::cout << "¡Hola desde el hilo!\n";
    }
        
    int main() {
        // Crear un nuevo hilo y ejecutar la función tarea
        std::thread t(tarea);

        // Esperar a que el hilo termine su ejecución
        t.join();

        return 0;
    }
    ```
    Imprime:
    ```cpp
    ¡Hola desde el hilo!
    ```
    En este ejemplo, se crea un nuevo hilo utilizando std::thread y se ejecuta la función tarea en ese hilo. Luego, el programa principal espera a que el hilo termine su ejecución utilizando `t.join()`.

- **Problemas en creación y desvinculación de threads:**

    ```cpp
    std::thread t; // No joinable, sin inicializar
    t.detach(); // Separa el hilo sin tarea asociada
    t = std::thread([] { /* código del hilo */ }); // Operación inválida, causará una excepción o un comportamiento indefinido
    ```
    `std::thread t;`: Se crea un objeto `std::thread` sin inicializar, lo que lo deja en un estado no joinable (no se puede unir al hilo principal).
    `t.detach();`: Se llama al método `detach()` sobre el objeto `t` que no está inicializado. Esto separa el objeto `t` del hilo principal, pero como no tiene ninguna tarea asociada, no hay nada que ejecutar realmente. El objeto `t` entra en un estado especial después de esta operación.
    `t = std::thread([] { /* código del hilo */ });`: Intentar asignar un nuevo hilo inicializado a un objeto std::thread que se encuentra en el estado especial después de `detach()` es una operación inválida y está prohibida por el estándar C++.

    Cuando se intenta esta asignación, ocurrirá uno de los siguientes escenarios:

    - **Excepción:** Es muy probable que se lance una excepción del tipo `std::system_error` o alguna excepción relacionada, dependiendo de la implementación de la biblioteca estándar C++ que se esté utilizando.
    - **Comportamiento indefinido:** Si la implementación de la biblioteca estándar C++ no lanza una excepción, el programa entrará en un estado de comportamiento indefinido, lo que puede causar errores impredecibles o incluso un fallo en el programa.

### 3. Sincronización Básica

La sincronización básica en programación multihilo implica técnicas simples para controlar la ejecución de hilos y garantizar la coherencia en el acceso a los recursos compartidos.
- **Bloqueo de hilos con `std::this_thread::sleep_for()` y `std::this_thread::sleep_until()`:**
Estas funciones se utilizan para suspender la ejecución de un hilo durante un período de tiempo específico. `sleep_for()` suspende el hilo durante una cantidad de tiempo determinada, mientras que `sleep_until()` suspende el hilo hasta un momento específico en el tiempo.
- **Identificación de hilos con `std::this_thread::get_id()`:**
Esta función devuelve un identificador único para el hilo en el que se llama. Es útil para identificar hilos específicos y realizar acciones basadas en el hilo actual.
- **Uso de variables `thread-local` y variables globales:**
    Las variables thread-local son variables que tienen un alcance local al hilo en el que se definen, lo que significa que cada hilo tiene su propia instancia de la variable. Esto se logra utilizando el modificador thread_local. Las variables globales, por otro lado, son variables que se comparten entre todos los hilos y pueden causar problemas de concurrencia si se accede a ellas concurrentemente sin sincronización adecuada.
    Ejemplo de uso de `std::this_thread::sleep_for()` y `std::this_thread::get_id()`:
    ```cpp
    #include <iostream>
    #include <thread>
    #include <chrono>

    void tarea() {
        std::cout << "Hilo en ejecución. ID: " << std::this_thread::get_id() << "\n";
        std::this_thread::sleep_for(std::chrono::seconds(2)); // Suspender el hilo durante 2 segundos
        std::cout << "Hilo reanudado después de dormir\n";
    }

    int main() {
        std::thread t(tarea);
        std::cout << "Hilo principal. ID: " << std::this_thread::get_id() << "\n";
        t.join(); // Esperar a que el hilo termine
        return 0;
    }
    ```
    En este ejemplo, se muestra cómo obtener el ID del hilo utilizando `std::this_thread::get_id()` y cómo suspender la ejecución del hilo durante un cierto período de tiempo utilizando `std::this_thread::sleep_for()`. Esto ilustra la identificación y el bloqueo básico de hilos en C++.

### 4. Usando header \<mutex>
Definida como header <font color="red">\<mutex></font> de la librería Estándar desde C++11 que proporciona las facilidades para trabajar con mecanismos de exclusión mutua, es decir, mutexes. Este encabezado define varias clases y funciones que se utilizan para crear, bloquear y desbloquear mutexes en un entorno multihilo. 

| Clases                       | Descripción                       |
| -----------                 | -----------                       |
|mutex <span style="color:green;font-size:12px">(C++11)</span>                |Proporciona una facilidad básica de exclusión mutua <span style="color:green;font-size:12px">(clase)</span>|
|timed_mutex <span style="color:green;font-size:12px">(C++11)</span>          |Proporciona una facilidad de exclusión mutua que implementa el bloqueo con un tiempo de espera <span style="color:green;font-size:12px">(clase)</span>|
|recursive_mutex <span style="color:green;font-size:12px">(C++11)</span>      |Proporciona una facilidad de exclusión mutua que puede ser bloqueada de forma recursiva por el mismo thread <span style="color:green;font-size:12px">(clase)</span>|
|recursive_timed_mutex <span style="color:green;font-size:12px">(C++11)|Proporciona una facilidad de exclusión mutua que puede ser bloqueada de forma recursiva por el mismo thread e implementa el bloqueo con un tiempo de espera <span style="color:green;font-size:12px">(clase)</span>|

| Locks                                                     | Descripción                     |
| --------                                                         | -----------                     |
|lock_guard <span style="color:green;font-size:12px">(C++11)</span>|Implementa un contenedor de propiedad de mutex estrictamente basado en ámbito <span style="color:green;font-size:12px">(clase plantilla)</span>|
|unique_lock <span style="color:green;font-size:12px">(C++11)</span>|Implementa un contenedor de propiedad de mutex móvil <span style="color:green;font-size:12px">(clase plantilla)</span>|
|shared_lock <span style="color:green;font-size:12px">(C++11)</span>|Implementa un contenedor de propiedad de mutex compartido móvil <span style="color:green;font-size:12px">(clase plantilla)</span>|
|scoped_lock <span style="color:green;font-size:12px">(C++17)</span>|Envoltorio RAII para evitar deadlocks para múltiples mutex <span style="color:green;font-size:12px">(clase plantilla)</span> |

|Tag arguments                                                     | Descripción                     |
| --------                                                         | -----------                     |
|defer_lock <span style="color:green;font-size:12px">(C++11)</span>|No adquirir inmediatamente la propiedad del mutex <span style="color:green;font-size:12px">defer_lock_t</span>|
|try_to_lock <span style="color:green;font-size:12px">(C++11)</span>|Intentar adquirir la propiedad del mutex sin bloquear <span style="color:green;font-size:12px">try_to_lock_t</span>|
|adopt_lock <span style="color:green;font-size:12px">(C++11)</span>|Suponer que el hilo que llama ya tiene la propiedad del mutex <span style="color:green;font-size:12px">adopt_lock_t</span>|

Estos tres tags se aplican a `std::unique_lock` y `std::shared_lock`. Por otro lado, `std::lock_guard` solo acepta el tag `std::adopt_lock`. En ausencia de cualquiera de estos tags, `std::unique_lock`, `std::shared_lock`, y `std::lock_guard` adquirirán la propiedad del mutex de inmediato. Esta precisión es importante para entender las opciones de control de mutex en C++.

| Funciones   | Descripción                     |
| --------   |  --------                       |
|try_lock <span style="color:green;font-size:12px">(C++11)</span>|Intenta obtener la propiedad de los mutex mediante llamadas repetidas a try_lock <span style="color:green;font-size:12px">(función plantilla)</span>|
|lock <span style="color:green;font-size:12px">(C++11)</span>|Bloquea los mutex especificados, bloquea si alguno no está disponible <span style="color:green;font-size:12px">(función plantilla)</span>|
|call_once <span style="color:green;font-size:12px">(C++11)</span>|invoca una función solo una vez incluso si se llama desde varios threads <span style="color:green;font-size:12px">(función plantilla)</span>|

- **Usando la clase `std::mutex`**
Un tipo básico de mutex que proporciona bloqueo mutuo. Se utiliza para proteger secciones críticas del código.

| Miembros                       | Descripción                       |
| -----------                 | -----------                       |
|lock     |Bloquea el mutex, bloquea si el mutex no está disponible <span style="color:green;font-size:12px">(función miembro pública)</span>         |
|try_lock |Intenta bloquear el mutex, retorna si el mutex no está disponible <span style="color:green;font-size:12px">(función miembro pública)</span>|
|unlock   |Desbloquea el mutex <span style="color:green;font-size:12px">(función miembro pública)</span>                                       |

Ejemplo de instanciacion:
```cpp
std::mutex m;

m.lock();
m.unlock();
m.try_lock();
```
Ejemplo usando la clase  `std::mutex`:
```cpp
#include <iostream>     //std::cout std::endl
#include <thread>       //std::thread
#include <mutex>        //std::mutex  lock()  unlock()

int globalVar = 5;
std::mutex m;

void task() {
    //algunas instrucciones
    m.lock();
        globalVar += 2;
        std::cout << globalVar << '\n';
    m.unlock();
}

int main(int argc, char const *argv[])
{
    std::thread t1(task);
    thr.join();

    return 0;
}
```
Imprime:
```cpp
7
```
Ejamplo con `try_lock()`:

```cpp
#include <iostream>     //std::cout std::endl;
#include <thread>       //std::thread
#include <mutex>        //std::mutex

std::mutex m;

void task(const std::string& str) {
    if (m.try_lock()){
        std::cout << str << " Bloqueo el mutex" << "\n";
        m.unlock();
    }
    else{
        std::cout << str << " Fallo al bloquear el mutex" << "\n";
    }
}  

int main(int argc, char const *argv[])
{
    std::thread t1(task,"t1");
    std::thread t2(task,"t2");

    t1.join();
    t2.join();

    return 0;
}
```
- **Usando la clase `std::timed_mutex`**
 Similar a `std::mutex`, pero permite la adquisición de bloqueo con temporización.


| Miembros                       | Descripción                       |
| -----------                 | -----------                       |
|lock     |Bloquea el mutex, bloquea si el mutex no está disponible <span style="color:green;font-size:12px">(función miembro pública)</span>         |
|try_lock |Intenta bloquear el mutex, retorna si el mutex no está disponible <span style="color:green;font-size:12px">(función miembro pública)</span>|
|try_lock_for|Intenta bloquear el mutex, retorna si el mutex ha estado no disponible durante la duración de tiempo relativo especificado <span style="color:green;font-size:12px">(función miembro pública)</span>         |
|try_lock_until|Intenta bloquear el mutex, retorna si el punto de tiempo absoluto especificado se ha alcanzado y el mutex no ha estado disponible <span style="color:green;font-size:12px">(función miembro pública)</span>         |
|unlock   |Desbloquea el mutex <span style="color:green;font-size:12px">(función miembro pública)</span>    |

Ejemplo de instanciacion:
```cpp
std::timed_mutex m;

m.lock();
m.unlock();
m.try_lock();
m.try_lock_for(rel_time);
m.try_lock_until(abs_time);
```
Ejemplo con `try_lock_for(rel_time)`:

Este método intenta bloquear un objeto `std::timed_mutex` durante un tiempo máximo especificado `rel_time`, o hasta que se desbloquee el objeto mediante `unlock()`:
- Si el mutex no está bloqueado por ningún hilo, se bloquea inmediatamente y devuelve true.
- Si el mutex está bloqueado por otro hilo, el hilo actual espera hasta que se desbloquee el mutex o hasta que haya transcurrido `rel_time`, lo que ocurra primero. Si se desbloquea antes de que transcurra `rel_time`, el hilo bloqueado lo bloqueará y devolverá true. Si `rel_time` expira antes de que se desbloquee el mutex, la función devuelve false.
- Si el hilo actual ya tiene bloqueado el mutex, se produce un bloqueo con un comportamiento no definido.

    ```cpp
    #include <iostream>       // std::cout
    #include <chrono>         // std::chrono::milliseconds
    #include <thread>         // std::thread
    #include <mutex>          // std::timed_mutex

    std::timed_mutex mtx;

    void task(int id, std::chrono::seconds timeout) {
        if (mtx.try_lock_for(timeout)) {
            std::cout << "Thread " << id << " adquirió el bloqueo\n";
            // Simular trabajo dentro de la sección crítica
            std::this_thread::sleep_for(std::chrono::seconds(20));
            mtx.unlock();
            std::cout << "Thread " << id << " liberó el bloqueo\n";
        } else {
            std::cout << "Thread " << id << " falló al adquirir el bloqueo\n";
        }
    }

    int main(int argc, char const *argv[]) {
        std::thread t1(task, 1, std::chrono::seconds(10));
        std::thread t2(task, 2, std::chrono::seconds(10));

        t1.join();
        t2.join();

        return 0;
    }
    ```
    Imprime: 
    ```cpp
    Thread 1 adquirió el bloqueo
    Thread 2 falló al adquirir el bloqueo
    Thread 1 liberó el bloqueo
    ```

Se crean dos hilos t1 y t2.
t1 llama a `try_lock_for(std::chrono::seconds(10))`. Como el `std::timed_mutex` no está bloqueado, t1 adquiere el bloqueo y se imprime "Thread 1 acquired the lock".
t1 simula trabajo dentro de la sección crítica durante 20 segundos.
Mientras t1 está dentro de la sección crítica, t2 llama a `try_lock_for(std::chrono::seconds(10))`. Como t1 ya tiene el bloqueo, t2 intentará adquirir el bloqueo durante un máximo de 10 segundos.
Después de 10 segundos, como t1 aún no ha liberado el bloqueo, `try_lock_for` en t2 devuelve false, por lo que se imprime "Thread 2 failed to acquire the lock".
Después de 20 segundos, t1 libera el bloqueo llamando a `unlock()` y se imprime "Thread 1 released the lock".

Ejemplo con `try_lock_until(abs_time)`:

Este método intenta bloquear un objeto `std::timed_mutex` hasta un tiempo absoluto especificado `abs_time`, o hasta que se desbloquee el objeto mediante `unlock()`:

Si el mutex no está bloqueado por ningún hilo, se bloquea inmediatamente y devuelve true.
Si el mutex está bloqueado por otro hilo, el hilo actual espera hasta que se desbloquee el mutex o hasta que se alcance el tiempo `abs_time`, lo que ocurra primero. Si se desbloquea antes de abs_time, el hilo bloqueado lo bloqueará y devolverá true. Si se alcanza `abs_time` antes de que se desbloquee el mutex, la función devuelve false.
Si el hilo actual ya tiene bloqueado el mutex, se produce un bloqueo con un comportamiento no definido.

```cpp
#include <iostream>       // std::cout
#include <chrono>         // std::chrono::milliseconds, std::chrono::system_clock
#include <thread>         // std::thread
#include <mutex>          // std::timed_mutex

std::timed_mutex mtx;

void task(int id, std::chrono::seconds timeout) {
    auto abs_time = std::chrono::system_clock::now() + timeout;
    if (mtx.try_lock_until(abs_time)) {
        std::cout << "Thread " << id << " adquirió el bloqueo\n";
        // Simular trabajo dentro de la sección crítica
        std::this_thread::sleep_for(std::chrono::seconds(20));
        mtx.unlock();
        std::cout << "Thread " << id << " liberó el bloqueo\n";
    } else {
        std::cout << "Thread " << id << " falló al adquirir el bloqueo\n";
    }
}

int main(int argc, char const *argv[]) {
    std::thread t1(task, 1, std::chrono::seconds(10));
    std::thread t2(task, 2, std::chrono::seconds(10));

    t1.join();
    t2.join();

    return 0;
}
```
Imprime: 
```cpp
Thread 1 adquirió el bloqueo
Thread 2 falló al adquirir el bloqueo
Thread 1 liberó el bloqueo
```

t1 adquiere el bloqueo del std::timed_mutex porque no está bloqueado.
Cuando t2 llama a `try_lock_until(abs_time)`, como t1 ya tiene el bloqueo, t2 intentará adquirir el bloqueo hasta que se alcance `abs_time` (10 segundos después del tiempo actual) o hasta que t1 libere el bloqueo, lo que ocurra primero.
Como t1 no libera el bloqueo antes de que se alcance `abs_time`, `try_lock_until` en t2 devolverá false.
Después de 20 segundos (simulando trabajo), t1 libera el bloqueo llamando a unlock().
- **Usando la clase `std::recursive_mutex`:** Proporciona una facilidad de exclusión mutua que puede ser bloqueada de forma recursiva por el mismo thread
```cpp
#include <iostream>
#include <thread>
#include <mutex>

std::recursive_mutex rmutex;

void recursive_function(int count) {
    if (count < 0) return;
    
    // Bloqueo del mutex recursivo
    rmutex.lock();
    
    std::cout << "Count: " << count << " - Thread ID: " << std::this_thread::get_id() << std::endl;
    
    // Llamada recursiva
    recursive_function(count - 1);
    
    // Desbloqueo del mutex recursivo
    rmutex.unlock();
}

int main() {
    std::thread t1(recursive_function, 3);
    std::thread t2(recursive_function, 3);
    
    t1.join();
    t2.join();
    
    return 0;
}
```
Imprime:
```
Count: 3 - Thread ID: 140737345746496
Count: 2 - Thread ID: 140737345746496
Count: 1 - Thread ID: 140737345746496
Count: 0 - Thread ID: 140737345746496
Count: 3 - Thread ID: 140737337353792
Count: 2 - Thread ID: 140737337353792
Count: 1 - Thread ID: 140737337353792
Count: 0 - Thread ID: 140737337353792
```
Usando esta clase nos asegura que el mismo thread llamando de forma recursiva a la funcion no se este bloqueando como parte de la misma operacion.
- **Usando la clase `std::lock_guard`:** sigue el principio de RAII para garantizar la adquisición y liberación segura de un mutex, lo que simplifica el código y mejora la robustez y la seguridad en entornos multihilo.

    ```cpp
    #include <iostream>
    #include <thread>
    #include <mutex>

    std::mutex mtx;
    int shared_data = 0;

    void threadFunc() {
        std::lock_guard<std::mutex> lock(mtx); // Adquirir el mutex de forma segura
        shared_data++; // Acceso seguro al recurso compartido
    }

    int main() {
        std::thread t1(threadFunc);
        std::thread t2(threadFunc);

        t1.join();
        t2.join();

        std::cout << "Valor final del dato compartido: " << shared_data << std::endl;
        return 0;
    }
    ```
    Imprime: 
    ```cpp
    Valor final del dato compartido: 2
    ```
- **Usando la clase `std::unique_lock`:**


    ```cpp
    #include <iostream>
    #include <thread>
    #include <mutex>

    std::mutex mtx;
    int shared_data = 0;

    void threadFunc1() {
        std::unique_lock<std::mutex> lock(mtx, std::defer_lock); // Utilización de std::defer_lock
        // El mutex no se bloquea automáticamente
        lock.lock(); // Bloquear explícitamente el mutex
        shared_data++;
    }

    void threadFunc2() {
        std::unique_lock<std::mutex> lock(mtx, std::try_to_lock); // Utilización de std::try_to_lock
        if (lock.try_lock()) { // Intentar bloquear el mutex (no esperar si ya está bloqueado)
            shared_data++;
        }
    }

    void threadFunc3() {
        mtx.lock(); // Bloquea el mutex manualmente
        std::unique_lock<std::mutex> lock(mtx, std::adopt_lock); // Adopcion del bloqueo por la linea anterior 
        // unique_lock asume la liberacion de recursos
        shared_data++;
    }

    int main() {
        std::thread t1(threadFunc1);
        std::thread t2(threadFunc2);
        std::thread t3(threadFunc3);

        t1.join();
        t2.join();
        t3.join();

        std::cout << "Valor final del dato compartido: " << shared_data << std::endl;
        return 0;
    }
    ```
    Imprime:
    ```cpp
    Valor final del dato compartido: 3
    ```
- **Usando la clase `std::scoped_lock`:**
    La clase `std::scoped_lock` es una versión moderna y más segura de las funciones anteriores. Es posible utilizarla para bloquear múltiples mutexes
    ```cpp
    #include <iostream>
    #include <thread>
    #include <mutex>

    std::mutex m1,m2;

    void funcion1(std::string s) {
        std::scoped_lock lock(m1,m2);
        std::cout << "recurso1 protegido con m1 utilizado por: "<< s <<'\n';
        std::cout << "recurso2 protegido con m2 utilizado por: "<< s <<'\n';
    }

    void funcion2(std::string s) {
        std::scoped_lock lock(m1,m2);
        std::cout << "recurso2 protegido con m2 utilizado por: "<< s <<'\n';
        std::cout << "recurso1 protegido con m1 utilizado por: "<< s <<'\n';
    }

    int main(int argc, char const *argv[]) 
    {
        std::thread t1(funcion1,"t1");
        std::thread t2(funcion2,"t2");

        t1.join();
        t2.join();
        return 0;
    }
    ```
    imprime:
    ```cpp
    recurso1 protegido con m1 utilizado por: t1
    recurso2 protegido con m2 utilizado por: t1
    recurso2 protegido con m2 utilizado por: t2
    recurso1 protegido con m1 utilizado por: t2
    ```

### 5. Usando header <shared_mutex>

| Clases                       | Descripción                       |
| -----------                 | -----------                       |
|shared_mutex <span style="color:green;font-size:12px">(C++17)</span>                |provee una facilidad de exclusión mutua compartida <span style="color:green;font-size:12px">(clase)</span>|
|shared_timed_mutex <span style="color:green;font-size:12px">(C++14)</span>          |provee una facilidad de exclusión mutua compartida e implementa bloqueos con un tiempo límite <span style="color:green;font-size:12px">(clase)</span>|
|shared_lock <span style="color:green;font-size:12px">(C++14)</span>          |implementa un envoltorio de propiedad compartida de mutex movible <span style="color:green;font-size:12px">(clase plantilla)</span>|

|Bloqueo exclusivo       |   Descripción                            |
| --------       | -----------                                                                                                |
|lock |bloquea el mutex, bloquea si el mutex no está disponible <span style="color:green;font-size:12px">(función miembro pública)</span>|
|try_lock     |intentos de bloquear el mutex, retorna si el mutex no está disponible <span style="color:green;font-size:12px">(función miembro pública)</span>         |
|unlock   |Desbloquea el mutex (propiedad compartida) <span style="color:green;font-size:12px">(función miembro pública)</span>                                                |

|Bloqueo compartido       |   Descripción                            |
| --------       | -----------                                                                                                |
|lock_shared |bloquea el mutex para la propiedad compartida, bloquea si el mutex no está disponible <span style="color:green;font-size:12px">(función miembro pública)</span>|
|try_lock_shared     |intentos de bloquear el mutex para la propiedad compartida, retorna si el mutex no está disponible <span style="color:green;font-size:12px">(función miembro pública)</span>         |
|unlock_shared   |Desbloquea el mutex (propiedad compartida) <span style="color:green;font-size:12px">(función miembro pública)</span>                                                |

Ejemplo usando shared_mutex
```cpp
#include <iostream>
#include <thread>
#include <vector>
#include <shared_mutex>

std::shared_mutex mutex;
std::vector<int> data;

void writer() { //exclusive write access
    for (int i = 0; i < 5; ++i) {
        std::unique_lock<std::shared_mutex> lock(mutex);
        data.push_back(i);
        std::cout << "Writer: Added " << i << std::endl;
        std::this_thread::sleep_for(std::chrono::milliseconds(100));
    }
}

void reader(std::string n) { //read-only access simultaneously
    std::shared_lock<std::shared_mutex> lock(mutex);
    std::cout << "Reader: thread " << n << " = ";
    for (int value : data) {
        std::cout << value << " ";
    }
    std::cout << std::endl;
    std::this_thread::sleep_for(std::chrono::milliseconds(200));
}

int main(int argc, char const *argv[])
{
    std::thread w(writer);
    std::thread r1(reader,"r1");
    std::thread r2(reader,"r2");

    w.join(); 
    r1.join();
    r2.join();
    return 0;
}
```
Impirme:
```cpp
Writer: Added 0
Writer: Added 1
Writer: Added 2
Writer: Added 3
Writer: Added 4
Reader: thread r2 = 0 1 2 3 4
Reader: thread r1 = 0 1 2 3 4
```

### 6. Usando header \<atomic>
La clase `std::atomic` es una herramienta para manipular valores atómicos en un programa multithread. Esto permite operar sobre variables 

| Miembros                       | Descripción                       |
| -----------                 | -----------                       |
|is_lock_free|verifica si el objeto atómico está libre de bloqueo <span style="color:green;font-size:12px">(función miembro pública)</span>|
|store|reemplaza atómicamente el valor del objeto atómico con un argumento no atómico <span style="color:green;font-size:12px">(función miembro pública)</span>|
|load|obtiene atómicamente el valor del objeto atómico <span style="color:green;font-size:12px">(función miembro pública)</span>|
|operator T|carga un valor desde un objeto atómico <span style="color:green;font-size:12px">(función miembro pública)</span>|
|exchange|reemplaza atómicamente el valor del objeto atómico y obtiene el valor previamente almacenado <span style="color:green;font-size:12px">(función miembro pública)</span>|
|compare_exchange_weak compare_exchange_strong|compara atómicamente el valor del objeto atómico con un argumento no atómico y realiza un intercambio atómico si son iguales o una carga atómica si no lo son <span style="color:green;font-size:12px">(función miembro pública)</span>|
|wait <span style="color:green;font-size:12px">(C++20)</span>|bloquea el hilo hasta que se notifique y cambie el valor atómico <span style="color:green;font-size:12px">(función miembro pública)</span>|
|notify_one <span style="color:green;font-size:12px">(C++20)</span>|notifica al menos a un hilo que está esperando en el objeto atómico <span style="color:green;font-size:12px">(función miembro pública)</span>|
|notify_all <span style="color:green;font-size:12px">(C++20)</span>|notifica a todos los hilos bloqueados esperando en el objeto atómico <span style="color:green;font-size:12px">(función miembro pública)</span>|

| Constantes                       | Descripción                       |
| -----------                 | -----------                        |
|is_always_lock_free `[static]`<span style="color:green;font-size:12px">(C++17)</span> | indica que el tipo siempre está libre de bloqueo <span style="color:green;font-size:12px">(función miembro pública)</span>|

Repasando:
```cpp
#include <atomic>
#include <thread>
#include <chrono>

int main() {
    // 1. Constructor y asignación
    {
        std::atomic<int> atom(10);
        atom = 20;
        
        // Resultado:
        // atom.load() = 20
    }

    // 2. load() y store()
    {
        std::atomic<int> atom(5);

        int valor = atom.load();
        // Resultado:
        // valor = 5

        atom.store(10);
        // Resultado:
        // atom.load() = 10
    }

    // 3. exchange()
    {
        std::atomic<int> atom(5);

        int valor_anterior = atom.exchange(10);
        // Resultado:
        // valor_anterior = 5
        // atom.load() = 10
    }

    // 4. compare_exchange_strong()
    {
        std::atomic<int> atom(5);
        int esperado = 5;
        bool exito = atom.compare_exchange_strong(esperado, 10);

        // Resultado:
        // exito = true  (valor esperaro es igual al valor anterior)
        // atom.load() = 10
    }

    // 5. fetch_add() y fetch_sub()
    {
        std::atomic<int> atom(5);

        int valor_previo = atom.fetch_add(3);
        // Resultado:
        // valor_previo = 5
        // atom.load() = 8

        valor_previo = atom.fetch_sub(2);
        // Resultado:
        // valor_previo = 8
        // atom.load() = 6
    }

    // 6. Operadores aritméticos atómicos
    {
        std::atomic<int> atom(5);

        atom += 3;
        // Resultado:
        // atom.load() = 8

        atom -= 2;
        // Resultado:
        // atom.load() = 6

        ++atom;
        // Resultado:
        // atom.load() = 7

        --atom;
        // Resultado:
        // atom.load() = 6
    }

    // 7. is_lock_free()
    {
        std::atomic<int> atom;

        bool es_lock_free = atom.is_lock_free();
        // Resultado:
        // es_lock_free es generalmente true en la mayoría de las arquitecturas modernas
    }

    // 8. compare_exchange_weak()
    {
        std::atomic<int> atom(5);
        int esperado = 5;
        bool exito = atom.compare_exchange_weak(esperado, 10);

        // Resultado:
        // exito = true (valor esperado es igual al valor anterior)
        // atom.load() = 10
        // esperado = 5

        // Intentar de nuevo, esta vez debería fallar
        esperado = 5;
        exito = atom.compare_exchange_weak(esperado, 15);

        // Resultado:
        // exito = false (valor esperado no es igual al valor actual)
        // atom.load() = 10 (sin cambios)
        // esperado = 10 (actualizado al valor actual de atom)

        // Uso típico en un bucle de espera
        atom.store(0);
        int valor_deseado = 1;

        std::thread t([&atom]() {
            std::this_thread::sleep_for(std::chrono::milliseconds(100));
            atom.store(1);
        });

        while (!atom.compare_exchange_weak(valor_deseado, 2)) {
            valor_deseado = 1; // Reiniciar el valor esperado en cada iteración
        }

        t.join();

        // Resultado:
        // atom.load() = 2
    }

    return 0;
}
```
Un ejemplo sencillo donde dos threads incrementan una variable atómica global
```cpp
#include <iostream>
#include <atomic>
#include <thread>

std::atomic<int> counter(0);

void increment() {
    for (int i = 0; i < 100000; ++i) {
        ++counter;
    }
}

int main(int argc, char const *argv[]) {
    std::thread t1(increment);
    std::thread t2(increment);

    t1.join();
    t2.join();

    std::cout << "Counter: " << counter << '\n';

    return 0;
}
```
Imprime:
```cpp
Counter: 200000
```
En este código, counter es una variable atómica. Esto significa que las operaciones en counter son atómicas, es decir, se completan en un solo paso. Por lo tanto, aunque increment es llamado por dos hilos diferentes, el resultado final de counter será correcto.

Para acceder al valor de counter se puede por ejemplo

```cpp
std::cout << "Counter: " << counter << '\n';
std::cout << "Counter: " << counter.load() << '\n';
```
Para modificar el valor de counter se puede también
```cpp
counter = 100;
counter.store(100);
```
Operaciones de modificación atómica: Estas operaciones modifican el valor de la variable atómica y devuelven su valor original en una sola operación atómica. Incluyen `fetch_add`, `fetch_sub`, `fetch_and`, `fetch_or`, y `fetch_xor`

Operaciones de intercambio atómico: Estas operaciones establecen el valor de la variable atómica y devuelven su valor original en una sola operación atómica. Incluyen exchange y `compare_exchange_strong`

```cpp
#include <atomic>
#include <iostream>

int main() {
    std::atomic<int> counter(0);

    int old_value = counter.fetch_add(1);  // Operación de modificación atómica
    std::cout << "Old value: " << old_value << ", New value: " << counter << '\n';

    old_value = counter.exchange(100);  // Operación de intercambio atómico
    std::cout << "Old value: " << old_value << ", New value: " << counter << '\n';

    return 0;
}
```
Imprime:
```cpp
Old value: 0, New value: 1
Old value: 1, New value: 100
```
Las operaciones atómicas como `fetch_add`, `fetch_sub`, `exchange`, y otras similares, modifican el valor de la variable atómica y devuelven su valor original en una sola operación atómica. Por lo tanto, cuando haces algo como int `old_value = counter.fetch_add(1);`, estás asignando a `old_value` el valor original de `counter` antes de que se incremente.

- **Orden y Modelo de memoria:**

    | Valor                      | Explicación                       |
    | -----------                 | -----------                      |
    |memory_order_relaxed|proporciona el ordenamiento mínimo de memoria posible, lo que significa que las operaciones atómicas se realizan sin ninguna garantía de ordenamiento con otras operaciones atómicas o no atómicas.|
    |memory_order_consume|garantiza que las operaciones previas realizadas en la misma variable atómica sean visibles para el hilo actual, pero no garantiza ningún ordenamiento de memoria con otras variables atómicas o no atómicas.|
    |memory_order_acquire|garantiza que las operaciones previas realizadas en la misma variable atómica sean visibles para el hilo actual y que ninguna operación posterior se realice antes de que se complete la operación atómica actual.|
    |memory_order_release|garantiza que las operaciones previas realizadas en la misma variable atómica sean visibles para otros hilos y que ninguna operación anterior se realice después de que se complete la operación atómica actual.|
    |memory_order_acq_rel|combina los efectos de memory_order_acquire y memory_order_release, lo que significa que la operación atómica actual actúa como una barrera de adquisición y liberación.|
    |memory_order_seq_cst|proporciona el máximo ordenamiento de memoria posible, lo que significa que todas las operaciones atómicas se realizan en un orden secuencial y consistente en todos los hilos. El orden de memoria predeterminado para las operaciones atómicas es memory_order_seq_cst.|

    - **Operaciones de carga y almacenamiento:** Los métodos load y store de las variables atómicas pueden tomar un modelo de ordenamiento de memoria como argumento. Por ejemplo, puedes hacer:
        ```cpp
        counter.load(std::memory_order_acquire)
        counter.store(100, std::memory_order_release)
        ```
    - **Operaciones de modificación atómica:** Los métodos como `fetch_add`, `fetch_sub`, `fetch_and`, `fetch_or`, y `fetch_xor` también pueden tomar un modelo de ordenamiento de memoria como argumento. Por ejemplo, puedes hacer:
        ```cpp
        counter.fetch_add(1, std::memory_order_relaxed).
        ```
    - **Operaciones de intercambio atómico:** Los métodos exchange y `compare_exchange_strong` pueden tomar un modelo de ordenamiento de memoria como argumento. Por ejemplo, puedes hacer: 
        ```cpp
        counter.exchange(100, std::memory_order_acq_rel).
        ```
- **Fences o barreras de memoria:** Las barreras de memoria, también conocidas como “memory fences” o “memory barriers”,son mecanismos que previenen que ciertas optimizaciones reorganicen las operaciones de lectura y escritura de manera que puedan causar problemas en un entorno multihilo.
    Un ejemplo de como usar `std::atomic_thread_fence` y `std::atomic_signal_fence` para crear barreras de memoria.
    ```cpp
    #include <atomic>
    #include <thread>

    std::atomic<bool> data_ready(false);
    int data;

    void producer() {
        data = 42;
        std::atomic_thread_fence(std::memory_order_release);
        data_ready.store(true, std::memory_order_relaxed);
    }

    void consumer() {
        while (!data_ready.load(std::memory_order_relaxed)) {
            // Esperar
        }
        std::atomic_thread_fence(std::memory_order_acquire);
        if (data != 42) {
            std::cout << "La barrera de memoria no funcionó!\n";
        }
    }
    ```
    En este código, `std::atomic_thread_fence(std::memory_order_release)` en producer asegura que la escritura en data se complete antes de que data_ready se establezca en true. De manera similar, `std::atomic_thread_fence(std::memory_order_acquire)` en consumer asegura que el hilo consumidor vea la escritura en data después de ver que `data_ready` es true.

- **Consistencia secuencial:** La consistencia secuencial es un tipo de ordenamiento de memoria que garantiza que todas las operaciones de memoria se perciben en el mismo orden en todos los hilos. En otras palabras, si tienes dos operaciones A y B que se ejecutan en diferentes hilos, y A sucede antes que B en el código del programa, entonces todos los hilos verán que A sucede antes que B.

    En C++, std::memory_order_seq_cst es el modelo de ordenamiento de memoria que proporciona consistencia secuencial. Es el modelo de ordenamiento predeterminado para las operaciones atómicas y es el más fuerte en términos de restricciones de ordenamiento.

    Un ejemplo de cómo usar std::memory_order_seq_cst en C++:
    ```cpp
    #include <atomic>
    #include <thread>

    std::atomic<bool> ready(false);
    std::atomic<int> data;

    void producer() {
        data.store(42, std::memory_order_relaxed);
        ready.store(true, std::memory_order_seq_cst);
    }

    void consumer() {
        while (!ready.load(std::memory_order_seq_cst)) {
            // Esperar
        }
        if (data.load(std::memory_order_relaxed) != 42) {
            std::cout << "La consistencia secuencial no funcionó!\n";
        }
    }
    ```
- **Relajación y Sincronización:** Son dos conceptos fundamentales en la programación multihilo que están estrechamente relacionados con el modelo de memoria y el ordenamiento de memoria.
    - Relajación: La relajación se refiere a permitir cierto grado de reordenamiento de las operaciones de memoria para mejorar el rendimiento. Por ejemplo, el compilador o la arquitectura del hardware pueden decidir reordenar las operaciones de lectura y escritura para optimizar el uso de la caché o para aprovechar las instrucciones de ejecución fuera de orden. Sin embargo, este reordenamiento solo se puede hacer si no afecta la correctitud del programa.
    - Sincronización: La sincronización se refiere a restringir el reordenamiento de las operaciones de memoria para garantizar la correctitud del programa. Por ejemplo, si tienes un hilo que está escribiendo en una variable y otro hilo que está leyendo de esa variable, es posible que necesites sincronizar los hilos para asegurarte de que la escritura se complete antes de la lectura. Esto se puede hacer utilizando operaciones atómicas con los modelos de ordenamiento de memoria adecuados, o utilizando mecanismos de sincronización como mutexes, semáforos o barreras de memoria.

        ```cpp
        #include <atomic>
        #include <thread>
        #include <iostream>

        std::atomic<int> data(0);
        std::atomic<bool> ready(false);

        void producer() {
            data.store(42, std::memory_order_relaxed);  // Relajación
            ready.store(true, std::memory_order_release);  // Sincronización
        }

        void consumer() {
            while (!ready.load(std::memory_order_acquire)) {  // Sincronización
                std::this_thread::yield();  // Esperar
            }
            std::cout << "Data: " << data.load(std::memory_order_relaxed) << '\n';  // Relajación
        }

        int main() {
            std::thread t1(producer);
            std::thread t2(consumer);

            t1.join();
            t2.join();

            return 0;
        }
        ```
        En este código, el hilo productor escribe en data y luego establece ready en true. El hilo consumidor espera hasta que ready sea true y luego lee data.

        La escritura en data se realiza con std::memory_order_relaxed, lo que significa que esta operación puede ser reordenada con respecto a otras operaciones en el mismo hilo. Esto es un ejemplo de relajación.

        La escritura en ready se realiza con std::memory_order_release, y la lectura correspondiente en el hilo consumidor se realiza con std::memory_order_acquire. Esto asegura que todas las operaciones de memoria (incluyendo la escritura en data) que suceden antes de ready.store(true) en el hilo productor sean visibles para el hilo consumidor después de ready.load(). Esto es un ejemplo de sincronización.

### 7. Usando header \<semaphore>

| Clases                       | Descripción                       |
| -----------                 | -----------                       |
|counting_semaphore <span style="color:green;font-size:12px">(C++20)</span>                |semáforo que modela un recuento de recursos no negativo <span style="color:green;font-size:12px">(clase plantilla)</span>|
|binary_semaphore <span style="color:green;font-size:12px">(C++20)</span>          |semáforo que tiene solo dos estados <span style="color:green;font-size:12px">(clase plantilla)</span>|

**Member functions of both `std::counting_semaphore` and `std::binary_semaphore`**

| Miembros                       | Descripción                       |
| -----------                 | -----------                       |
|release     |incrementa el contador interno y desbloquea a los adquirentes <span style="color:green;font-size:12px">(función miembro pública)</span>         |
|acquire |decrementa el contador interno o bloquea hasta que pueda hacerlo <span style="color:green;font-size:12px">(función miembro pública)</span>|
|try_acquire|intenta decrementar el contador interno sin bloquear <span style="color:green;font-size:12px">(función miembro pública)</span>         |
|try_acquire_for|intenta decrementar el contador interno, bloqueándose durante un tiempo de duración específico <span style="color:green;font-size:12px">(función miembro pública)</span>         |
|try_acquire_until   |intenta decrementar el contador interno, bloqueándose hasta un punto en el tiempo <span style="color:green;font-size:12px">(función miembro pública)</span>    |

| Constantes                       | Descripción                       |
| -----------                 | -----------                       |
|max`[static]`     |devuelve el valor máximo posible del contador interno <span style="color:green;font-size:12px">(función miembro estática pública)</span>         |

Los semáforos son una parte importante del multithreading que aún no has cubierto. Los semáforos son variables especiales que se utilizan para señalizar entre hilos y evitar conflictos.

```cpp
#include <mutex>
#include <condition_variable>

class Semaphore {
public:
    Semaphore (int count_ = 0) : count(count_) { }

    inline void notify(int tid) {
        std::unique_lock<std::mutex> lock(mtx);
        count++;
        cv.notify_one();
    }

    inline void wait(int tid) {
        std::unique_lock<std::mutex> lock(mtx);
        while(count == 0) {
            cv.wait(lock);
        }
        count--;
    }

private:
    std::mutex mtx;
    std::condition_variable cv;
    int count;
};
```
Un ejemplo de cómo podrías usar un semáforo en C++20:

```cpp
#include <semaphore>
#include <thread>

std::counting_semaphore<10> semaphore(3);

void worker(int id) {
    semaphore.acquire();
    std::cout << "Thread " << id << " acquired the semaphore.\n";
    // Aquí va el código de la sección crítica
    semaphore.release();
}
```
En este código, `std::counting_semaphore<10> semaphore(3);` crea un semáforo con un máximo de 10 permisos y se inicializa con 3 permisos disponibles.

El primer parámetro del template, 10, es el valor máximo que puede alcanzar el semáforo. Esto significa que a lo sumo, 10 hilos pueden adquirir el semáforo al mismo tiempo.
El segundo parámetro, 3, es el valor inicial del semáforo. Esto significa que al inicio, 3 hilos pueden adquirir el semáforo sin bloquearse.
Cuando un hilo llama a semaphore.acquire(), intenta adquirir un permiso. Si hay permisos disponibles (es decir, el valor del semáforo es mayor que 0), el hilo adquiere un permiso y continúa ejecutándose. Si no hay permisos disponibles (el valor del semáforo es 0), el hilo se bloquea hasta que otro hilo libere un permiso llamando a semaphore.release().

```cpp
#include <semaphore>
#include <thread>
#include <iostream>

std::counting_semaphore<5> semaphore(2);

void worker(int id) {
    semaphore.acquire();
    std::cout << "Thread " << id << " acquired the semaphore.\n";
    // Aquí va el código de la sección crítica
    semaphore.release();
}

int main() {
    std::thread t1(worker, 1);
    std::thread t2(worker, 2);
    std::thread t3(worker, 3);

    t1.join();
    t2.join();
    t3.join();

    return 0;
}
```

Ejemplo de std::binary_semaphore

```cpp
#include <semaphore>
#include <thread>
#include <iostream>

std::binary_semaphore semaphore(1);

void worker(int id) {
    semaphore.acquire();
    std::cout << "Thread " << id << " acquired the semaphore.\n";
    // Aquí va el código de la sección crítica
    semaphore.release();
}

int main() {
    std::thread t1(worker, 1);
    std::thread t2(worker, 2);

    t1.join();
    t2.join();

    return 0;
}
```

### 8. Usando header <condition_variable>

| Clases | Descripción |
| --------    | -----------|
|condition_variable <span style="color:green;font-size:12px">(C++11)</span> |provee una variable de condición asociada con un `std::unique_lock` <span style="color:green;font-size:12px">(clase)</span>|
|condition_variable_any <span style="color:green;font-size:12px">(C++11)</span> |provee una variable de condición asociada con cualquier tipo de bloqueo <span style="color:green;font-size:12px">(clase)</span>|
|cv_status <span style="color:green;font-size:12px">(C++11)</span> |enumera los posibles resultados de esperas temporizadas en variables de condición <span style="color:green;font-size:12px">(enumeración)</span>|

|Espera | Descripción |
| --------    | -----------|
|wait| Bloquea el hilo actual hasta que la variable de condición se despierte <span style="color:green;font-size:12px">(función miembro pública)</span>|
|wait_for| Bloquea el hilo actual hasta que la variable de condición se despierte o después de la duración de tiempo especificada <span style="color:green;font-size:12px">(función miembro pública)</span>|
|wait_until| Bloquea el hilo actual hasta que la variable de condición se despierte o hasta que se alcance el momento de tiempo especificado <span style="color:green;font-size:12px">(función miembro pública)</span>|

|Notificación | Descripción |
| --------    | -----------|
|notify_one| Notifica a un hilo en espera <span style="color:green;font-size:12px">(función miembro pública)</span>|
|notify_all| Notifica a todos los hilos en espera <span style="color:green;font-size:12px">(función miembro pública)</span>|

- **Usando la clase `std::condition_variable`:** Es una herramienta de sincronización que permite a los hilos esperar a que se cumpla una condición antes de continuar su ejecución. Esta clase se utiliza en combinación con un mutex y proporciona una forma de notificar a los hilos que están esperando en la condición variable que la condición se ha cumplido, lo que les permite continuar su ejecución. 

    La clase `std::condition_variable` se utiliza a menudo en patrones de productor-consumidor, donde un productor produce datos y un consumidor los consume. Cuando el productor produce datos, puede notificar a la condición variable para despertar al consumidor y hacer que continúe su ejecución.

    ```cpp
    #include <condition_variable>
    #include <mutex>
    #include <queue>
    #include <thread>
    #include <chrono>
    #include <iostream>

    std::mutex mtx;
    std::condition_variable cv;
    std::queue<int> data_queue;

    const int buffer_size = 5;
    int next_produced = 0;

    void producer() {
        while (true) {
            std::unique_lock<std::mutex> lck(mtx);
            cv.wait(lck, [] { return data_queue.size() < buffer_size; });
            data_queue.push(next_produced);
            std::cout << "Producer: produced " << next_produced << "\n";
            next_produced++;
            lck.unlock();
            cv.notify_one();
            std::this_thread::sleep_for(std::chrono::milliseconds(100));
        }
    }

    void consumer() {
        while (true) {
            std::unique_lock<std::mutex> lck(mtx);
            cv.wait(lck, [] { return !data_queue.empty(); });
            int data = data_queue.front();
            data_queue.pop();
            std::cout << "Consumer: consumed " << data << "\n";
            lck.unlock();
            cv.notify_one();
            std::this_thread::sleep_for(std::chrono::milliseconds(100));
        }
    }

    int main(int argc, char* argv[]) {
        std::thread producer_thread(producer);
        std::thread consumer_thread(consumer);
        producer_thread.join();
        consumer_thread.join();
        return 0;
    }
    ```
    Imprime:
    ```cpp
    Producer: produced 0
    Consumer: consumed 0
    Producer: produced 1
    Consumer: consumed 1
    Producer: produced 2
    Consumer: consumed 2
    Producer: produced 3
    Consumer: consumed 3
    Producer: produced 4
    Consumer: consumed 4
    Producer: produced 5
    Consumer: consumed 5
    ...
    ```

    En este ejemplo, el productor produce datos y los coloca en una cola concurrente (data_queue). Cuando la cola está llena, el productor se bloquea y espera a que haya espacio disponible. El consumidor consume datos de la cola y, cuando la cola está vacía, se bloquea y espera a que haya datos disponibles.

    La std::condition_variable se utiliza para notificar a los hilos que están esperando en la condición variable que la condición se ha cumplido, lo que les permite continuar su ejecución. En este caso, el productor notifica a la condición variable cuando hay espacio disponible en la cola y el consumidor notifica a la condición variable cuando hay datos disponibles en la cola.

    Recuerda que la clase std::condition_variable debe utilizarse en combinación con un mutex, ya que el hilo que notifica a la condición variable debe desbloquear el mutex para que el hilo que está esperando pueda continuar su ejecución. En este ejemplo, se utiliza un std::unique_lock para bloquear y desbloquear el mutex.

### 9. Usando header \<future>

Las clases y funciones de la libreria estándar de C++ en el header \<future> proporcionan una interfaz para la programación concurrente y la gestión de tareas asíncronas. En particular, permiten crear y manipular objetos future y promise, que se utilizan para transferir datos y resultados entre diferentes hilos y contextos de ejecución.

|Contenido      |Descripción |
| --------    | -----------|
|future <span style="color:green;font-size:12px">(C++11)</span>|Espera un valor que se establece de forma asíncrona <span style="color:green;font-size:12px">(clase plantilla)</span>|
|promise <span style="color:green;font-size:12px">(C++11)</span>|Almacena un valor para su recuperación asíncrona <span style="color:green;font-size:12px">(clase plantilla)</span>|
|shared_future <span style="color:green;font-size:12px">(C++11)</span>|Espera un valor (posiblemente referenciado por otros futures) que se establece de forma asíncrona <span style="color:green;font-size:12px">(clase plantilla)</span>|
|packaged_task <span style="color:green;font-size:12px">(C++11)</span>|Empaqueta una función para almacenar su valor de retorno para recuperación asíncrona <span style="color:green;font-size:12px">(clase plantilla)</span>|
|async <span style="color:green;font-size:12px">(C++11)</span>|Ejecuta una función de forma asíncrona (potencialmente en un nuevo hilo) y devuelve un std::future que contendrá el resultado <span style="color:green;font-size:12px">(plantilla de función)</span>|
|launch <span style="color:green;font-size:12px">(C++11)</span>|Especifica la política de lanzamiento para std::async <span style="color:green;font-size:12px">(enumeración)</span>|
|future_status <span style="color:green;font-size:12px">(C++11)</span>|Define los estados posibles de un objeto future. <span style="color:green;font-size:12px">(enumeración)</span>|

|Política de lanzamiento| Descripción|
| --------    | -----------|
|launch::async	|Asincrónico: Lanza un nuevo hilo para ejecutar la función de forma asíncrona, separado del hilo principal.|
|launch::deferred	|Diferido: No se lanza un nuevo hilo. La función se ejecuta solo cuando el resultado del futuro se solicita explícitamente usando wait() o get(). Se ejecuta en el mismo hilo que llama a wait() o get().|
|No especificado |Automático: La implementación elige la política de lanzamiento más apropiada.|

- **Usando la clase `std::future` y `std::shared_future`:**

| Miembros | Descripción |
| -------- | -----------|
|get|devuelve el resultado <span style="color:green;font-size:12px">(función miembro pública)</span>|
|wait|espera a que el resultado esté disponible <span style="color:green;font-size:12px">(función miembro pública)</span>|
|share <span style="color:green;font-size:12px">(std::future)</span>|transfiere el estado compartido desde `*this` a un shared_future y lo devuelve <span style="color:green;font-size:12px">(función miembro pública)</span>|
|valid|verifica si el future tiene un estado compartido <span style="color:green;font-size:12px">(función miembro pública)</span>|

- **Usando la clase `std::promise`:**

| Miembros      | Descripción |
| --------    | -----------|
|get_future|devuelve un future asociado con el resultado prometido <span style="color:green;font-size:12px">(función miembro pública)</span>|
|set_value|establece el resultado en un valor específico <span style="color:green;font-size:12px">(función miembro pública)</span>|
|set_exception|establece el resultado para indicar una excepción <span style="color:green;font-size:12px">(función miembro pública)</span>|
|set_value_at_thread_exit|establece el resultado en un valor específico mientras entrega la notificación solo al salir del hilo <span style="color:green;font-size:12px">(función miembro pública)</span>|
|set_exception_at_thread_exit|establece el resultado para indicar una excepción mientras entrega la notificación solo al salir del hilo <span style="color:green;font-size:12px">(función miembro pública)</span>|

- **Usando la clase `std::packaged_task`:**

| Miembros      | Descripción |
| --------    | -----------|
|get_future|devuelve un future asociado con el resultado prometido <span style="color:green;font-size:12px">(función miembro pública)</span>|
|valid|verifica si el objeto de tarea tiene una función válida <span style="color:green;font-size:12px">(función miembro pública)</span>|
|operator()|ejecuta la función <span style="color:green;font-size:12px">(función miembro pública)</span>|
|make_ready_at_thread_exit|ejecuta la función asegurando que el resultado esté listo solo una vez que el hilo actual salga <span style="color:green;font-size:12px">(función miembro pública)</span>|

- **Ejemplos**

Tanto el objeto future como promise pueden ser pasados como argumento  a una función que se ejecutará en segundo plano.

Pasando como atributo referencia a rvalue:
```cpp
void funcion(std::promise && p2)
//asumiento declarado en main() objeto  std::promise<int> p1;
std::thread t1(funcion, std::move(p1)); // pasamos una referencia a un rvalue
```
Pasando como atributo referencia a lvalue:
```cpp
void funcion(std::promise & p2)
//asumiento declarado en main() objeto  std::promise<int> p1;
std::thread t1(funcion, std::ref(p1)); // pasamos una referencia a un lvalue
```
Ejemplo seteando promise desde la funcion:
```cpp
#include <iostream>
#include <thread>
#include <future>

void funcion(std::promise<std::string>&& p2 ){
    p2.set_value("Valor establecido en la funcion\n");
}

int main(int argc, char const *argv[])
{
    //declaro objeto promise
    std::promise<std::string> p1;
    //vinculo el future con el promise
    std::future<std::string>  f1 = p1.get_future();
    //creo un thread pasando referencia rvalue de p1
    std::thread t1(funcion, std::move(p1));

    std::string valorDevuelto=f1.get();
    t1.join();//Espero a que se complete el hilo
    std::cout << "Imprimo: " << valorDevuelto << std::endl;
    return 0;
}
```
Imprime:
```cpp
Imprimo: Valor establecido en la funcion
```
De la misma manera podria haberse resuelto cambiando:
```cpp
void funcion(std::promise<std::string>& p2 )

std::thread t1(funcion, std::ref(p1));
```

Ejemplo seteando promise desde otro ambito

```cpp
#include <iostream>
#include <thread>
#include <future>

void funcion(std::future<std::string>&& f2 ){
    std::string valorDevuelto=f2.get();
    std::cout << "Imprimo: " << valorDevuelto << std::endl;
}

int main(int argc, char const *argv[])
{
    //declaro objeto promise
    std::promise<std::string> p1;
    //vinculo el future con el promise
    std::future<std::string>  f1 = p1.get_future();
    //creo un thread pasando referencia rvalue de f1
    std::thread t1(funcion, std::move(f1));
    
    p1.set_value("Valor establecido en main\n");
    
    t1.join();//Espero a que se complete el hilo
    
    return 0;
}
```
Imprime:
```cpp
Imprimo: Valor establecido en main
```

De la misma manera podria haberse resuelto cambiando:
```cpp
void funcion(std::future<std::string>& f2 )

std::thread t1(funcion, std::ref(f1));
```
Ejemplo usando std::async() y future
```cpp
#include <iostream> // std::cout  std::endl
#include <future>   // std::future  std::async()

int fn(int num){
    int result = num + 4;
    return result;
}

int main(int argc, char const *argv[]){

    std::future<int> output = std::async(fn, 6);
    
    //main() thread waits here the result
    int res = output.get();
    
    std::cout << res << std::endl;  

    return 0;  
}
```
Imprime:
```cpp
10
```

Ejemplo usando con multiples objetos promise y future
```cpp
#include <iostream> // std::cout
#include <thread>   // std::thread
#include <future>   // std::promise  std::future  std::move

void setDataReady(std::promise<int>&& ret, int num) {
    int result = num + 4;
    ret.set_value(result);
}

int main(int argc, char const* argv[]) {

    std::promise<int> pro[5];                   // create promise
    std::future<int> fut[5];                    // create future
    std::thread thr[5];                         // create child threads

    // engagement future with promise
    for (int i = 0; i < 5; i++) {
        fut[i] = pro[i].get_future();          
    }

    //launching threads
    for (int i = 0; i < 5; i++) {
        thr[i] = std::thread(setDataReady, std::move(pro[i]), i);   
    }

    // waiting and printing the return from future
    for (int i = 0; i < 5; i++) {
        std::cout << "thr[" << i << "] = " << fut[i].get() << std::endl;   
    }
    
    // waiting child threads are finished
    for (int i = 0; i < 5; i++) {
        thr[i].join();                           
    }

    return 0;
}
```
The output is:
```cpp
thr[0] = 4
thr[1] = 5
thr[2] = 6
thr[3] = 7
thr[4] = 8
```

Ejemplo que usa std::async(), future y promise
```cpp
#include <iostream> // std::cout  std::endl
#include <future>   // std::future std::promise std::async()

int funcion(std::future<int>& num) {
    int result = num.get() + 4;
    return result;
}

int main(int argc, char const* argv[]) {

    std::promise<int> pro;
    std::future<int> fut = pro.get_future();
    
    //usamos politica std::launch::async crear nuevo thread separado de main 
    std::future<int> ret = std::async(std::launch::async,funcion, std::ref(fut));
    //setamos promise desbloqueamos num.get() y esperamos el retorno
    pro.set_value(6);
    
    //main() thread waits here the result
    int res = ret.get();

    std::cout << res << std::endl;

    return 0;
}
```
Imprime:
```cpp
10
```
Ejemplo usando shared_furure
```cpp
#include <iostream> // std::cout  std::endl
#include <chrono>   // std::chrono
#include <future>   // std::shared_future  std::async()

int work(std::shared_future<float> fu)
{
    std::this_thread::sleep_for(std::chrono::milliseconds(1000));
    double Result1 = fu.get();
    std::cout << "Child::Result1 : " << Result1 << std::endl;


    double Result2 = fu.get();
    std::cout << "Child::Result2 : " << Result2 << std::endl;

    return 10;
}

int main(int argc, char const* argv[]) {
    // template typename <float> is the type of parameter
    std::promise<float> p;
    std::shared_future<float> fu = p.get_future();
    
    // template typename <int> must match with function return
    std::shared_future<int> fr = std::async(std::launch::async, work, fu);
    p.set_value(20.5);

    // fr has "10" is the return value of the work() function. 
    int result1 = fr.get();
    std::cout << "Parent::Result1 : " << result1 << std::endl;


    int result2 = fr.get();
    std::cout << "Parent::Result2 : " << result2 << std::endl;
    return 0;
}
```
The output is:
```cpp
Child::Result1 : 20.5
Child::Result2 : 20.5
Parent::Result1 : 10
Parent::Result2 : 10
```
Ejemplo usando packaged_task
```cpp
#include <iostream>
#include <cmath>
#include <thread>
#include <future>
#include <functional>

// unique function to avoid disambiguating the std::pow overload set
double f(int x, int y) { return std::pow(x, y); }

class Functor{
    public:
    double operator() (int x, int y){
        return f(x,y); 
    }
};

void task_function(){
    std::packaged_task<double(int,int)>task(f);
    std::future<double> ret = task.get_future();

    task(2,8);
    int result = static_cast<int>(ret.get());
    std::cout << "task_function:\t" << result << '\n';
}

void task_functor(){
    Functor functor;
    std::packaged_task<double(int, int)> task(functor);
    std::future<double> ret = task.get_future();

    task(2,13); 
    int result = static_cast<int>(ret.get());
    std::cout << "task_functor:\t" << result << '\n';
}

void task_lambda()
{
    std::packaged_task<double(int, int)> task([](int a, int b) {
            return std::pow(a, b);
        });
    std::future<double> ret = task.get_future();

    task(2, 9);

    int result = static_cast<int>(ret.get());
    std::cout << "task_lambda:\t" << result << '\n';
}

void task_bind()
{
    std::packaged_task<double()> task(std::bind(f, 2, 11));
    std::future<double> ret = task.get_future();

    task();

    int result = static_cast<int>(ret.get());
    std::cout << "task_bind:\t" << result << '\n';
}

void task_thread()
{
    std::packaged_task<double(int, int)> task(f);
    std::future<double> ret = task.get_future();

    std::thread task_td(std::move(task), 2, 10);
    task_td.join();

    int result = static_cast<int>(ret.get());
    std::cout << "task_thread:\t" << result << '\n';
}

void task_stdfun(){
    std::function<double(int,int)>stdfun = f;
    std::packaged_task<double(int,int)> task(stdfun);
    std::future<double> ret = task.get_future();

    task(2,12);

    int result = static_cast<int>(ret.get());
    std::cout << "task_stdfun:\t" << result << '\n';
}

int main(int argc, char const *argv[])
{
    task_function();
    task_lambda();
    task_thread();
    task_bind();
    task_stdfun();
    task_functor();
    
    return 0;
}
```
The output is:
```cpp
task_function:  256
task_lambda:    512
task_thread:    1024
task_bind:      2048
task_stdfun:    4096
task_functor:   8192
```

### 10. Comunicación entre Hilos
- **variables compartidas**
    - **std::mutex:**  
        Un mutex es un objeto que permite a múltiples hilos de programas acceder a un 
        único recurso compartido, pero uno a la vez. Aquí tienes un ejemplo de cómo se usa un mutex en C++:
        ```cpp
        std::mutex mtx;
        mtx.lock();
        // Acceso a la variable compartida
        mtx.unlock();    
        ```
        En este caso, `mtx.lock()` adquiere el mutex y `mtx.unlock()` lo libera.
    - **std::atomic:** 
        Las operaciones atómicas aprovechan el soporte del procesador (instrucciones         de          comparar e intercambiar) y no usan bloqueos en absoluto. Aquí tienes un ejemplo de cómo se      usa una  variable atómica en C++:
        ```cpp
        std::atomic<int> atomicVar;
        atomicVar.store(1);
        // o
        atomicVar = 1;
        ```
        Aquí, atomicVar.store(1) o atomicVar = 1 son operaciones atómicas.
    - **semáforo:** 
        Un semáforo es típicamente una variable entera. Aquí tienes un ejemplo de cómo se usa un semáforo en C++:
        ```cpp
        std::counting_semaphore<1> sema;
        sema.acquire();
        // Acceso a la variable compartida
        sema.release();
        ```
        En este ejemplo, sema.acquire() adquiere el semáforo y sema.release() lo libera.

- **Colas concurrentes**
    - **std::mutex:** 
        Un mutex es una primitiva de sincronización que se puede utilizar para proteger los datos compartidos del acceso simultáneo de varios hilos. En una cola concurrente, puedes usar std::mutex para asegurarte de que solo un hilo pueda modificar la cola a la vez. Aquí tienes un ejemplo de cómo se podría usar un mutex en una operación de encolado y desencolado:
        ```cpp
        std::queue<int> myQueue;
        std::mutex mtx;

        // Thread 1: Agregar un elemento a la cola
        mtx.lock();
        myQueue.push(1);
        mtx.unlock();

        // Thread 2: Eliminar un elemento de la cola
        mtx.lock();
        if (!myQueue.empty()) {
            myQueue.pop();
        }
        mtx.unlock();
        ```
        En este ejemplo, `mtx.lock()` adquiere el mutex antes de acceder a la cola, y `mtx.unlock()` lo libera después. Esto asegura que solo un thread puede modificar la cola a la vez, evitando así las condiciones de carrera.
    - **std::atomic:** 
        En una cola concurrente, puedes usar `std::atomic` para implementar una cola sin bloqueo. Esto significa que los threads no necesitan bloquearse y esperar si la cola está vacía o llena. En su lugar, pueden continuar ejecutándose y volver a intentarlo más tarde. Aquí tienes un ejemplo de cómo se podría usar `std::atomic` en una operación de encolado:
        ```cpp
        std::atomic<int> tail;
        // ...
        tail.store(tail.load() + 1);
        ```
        En este ejemplo, `tail.store(tail.load() + 1)` incrementa de forma atómica el índice de la cola, lo que significa que este incremento es una operación indivisible y segura en un contexto multithread.
    - **semáforo:** 
        Los semáforos pueden ser útiles en una cola concurrente para controlar el acceso a la cola. Por ejemplo, puedes usar un semáforo para asegurarte de que solo un número específico de threads puede acceder a la cola al mismo tiempo. Aquí tienes un ejemplo de cómo se podría usar un semáforo en una operación de encolado:
        ```cpp
        std::counting_semaphore<1> sema;
        // ...
        sema.acquire();
        // Acceso a la cola
        sema.release();
        ```
        En este ejemplo, `sema.acquire()` adquiere el semáforo antes de acceder a la cola, y `sema.release()` lo libera después. Esto asegura que solo un thread puede modificar la cola a la vez, evitando así las condiciones de carrera.
- **std::condition_variable:**
    se utiliza para sincronizar la ejecución de varios hilos basados en ciertas condiciones. Permite a un hilo esperar hasta que una condición se cumpla antes de continuar su ejecución.
    Aquí hay un ejemplo que ilustra cómo usar std::condition_variable para esperar hasta que un hilo notifique que se ha producido un evento:
    ```cpp
    std::mutex mtx;
    std::condition_variable cv;
    bool evento_ocurrido = false;

    // Hilo 1: Espera a que ocurra el evento
    {
        std::unique_lock<std::mutex> lck(mtx);
        cv.wait(lck, [&evento_ocurrido]{ return evento_ocurrido; });
        // Realizar acciones después de que el evento ocurra
    }

    // Hilo 2: Notifica que el evento ha ocurrido
    {
        std::lock_guard<std::mutex> lck(mtx);
        evento_ocurrido = true;
        cv.notify_one();
    }
    ```
    En este ejemplo, el "Hilo 1" espera a que evento_ocurrido sea true utilizando `cv.wait()`. Mientras tanto, el "Hilo 2" notifica a cv cuando evento_ocurrido se establece en true, permitiendo que el "Hilo 1" continúe su ejecución.
- **std::shared_mutex:**
    proporciona un mecanismo de bloqueo que permite el acceso simultáneo de múltiples hilos para lectura y un único acceso para escritura.
    Aquí hay un ejemplo que ilustra cómo usar std::shared_mutex para permitir el acceso concurrente de lectura y escritura a una estructura de datos compartida:
    ```cpp
    std::shared_mutex smtx;
    std::map<int, std::string> data;

    // Hilo de lectura: Lee los datos
    {
        std::shared_lock<std::shared_mutex> lck(smtx);
        auto it = data.find(42);
        if (it != data.end()) {
            std::cout << "Valor encontrado: " << it->second << std::endl;
        }
    }

    // Hilo de escritura: Escribe datos
    {
        std::unique_lock<std::shared_mutex> lck(smtx);
        data[42] = "Nuevo valor";
    }
    ```
    En este ejemplo, el "Hilo de lectura" bloquea smtx para acceder a los datos en modo de solo lectura, permitiendo que otros hilos también accedan para lectura simultáneamente. Mientras tanto, el "Hilo de escritura" bloquea smtx en modo de escritura exclusiva para modificar los datos, evitando que otros hilos lean o escriban al mismo tiempo.
### 11. Sincronización Avanzada
- **std::lock\:**
    Es una función incluida en header `<mutex>` que adquiere varios mutex de forma segura y sin riesgo de deadlock.
    Ejemplo mínimo:
    Supongamos que tenemos dos mutex mutex1 y mutex2. Para adquirir ambos mutex de forma segura, podemos usar std::lock de la siguiente manera:
    ```cpp
    std::mutex mutex1, mutex2;
    std::lock(mutex1, mutex2);
    // Operaciones seguras con mutex1 y mutex2
    ```
- **std::try_lock:**
    Es una función incluida en header `<mutex>` que intenta adquirir varios mutex sin bloquear el hilo si no puede adquirir todos los mutex de inmediato.
    Ejemplo mínimo:
    Supongamos que tenemos dos mutex mutex1 y mutex2. Podemos intentar adquirir ambos mutex sin bloquear el hilo usando std::try_lock de la siguiente manera:
    ```cpp
    std::mutex mutex1, mutex2;
    if (std::try_lock(mutex1, mutex2) == -1) {
        // No se pudieron adquirir ambos mutex de inmediato
    } else {
        // Operaciones seguras con mutex1 y mutex2
    }
    ```
- **std::call_once:**
    Es una función incluida en header `<mutex>` que garantiza que una función se llame solo una vez por varios hilos, incluso si la función se llama desde varios hilos simultáneamente.
    ```cpp
    #include <iostream>
    #include <thread>
    #include <mutex>

    std::once_flag flag;

    void initialize_resource() {
        std::cout << "Initializing resource..." << std::endl;
    }

    void do_work() {
        std::call_once(flag, initialize_resource);

        std::cout << "Doing some work..." << std::endl;
    }

    int main(int argc, char const *argv[])
    {
        std::thread t1(do_work);
        std::thread t2(do_work);
        std::thread t3(do_work);

        t1.join();
        t2.join();
        t3.join();

        return 0;
    }
    ```
    The output is: 
    ```cpp
    Initializing resource...
    Doing some work...
    Doing some work...
    Doing some work...
    ```

### 12. Patrones de diseño
- **Producer-Consumer:**
    Es una forma efectiva de manejar la comunicación entre procesos o threads donde un conjunto de productores generan datos y los colocan en una cola compartida, y un conjunto de consumidores retiran esos datos de la cola y los procesan. Es fundamental comprender y aplicar este patrón correctamente para desarrollar sistemas concurrentes robustos y eficientes.
    - **Usando Mutex:**
        ```cpp
        #include <iostream>
        #include <thread>
        #include <queue>
        #include <mutex>
        #include <condition_variable>

        std::queue<int> buffer;
        std::mutex mtx;
        std::condition_variable cv;

        void producer() {
            for (int i = 0; i < 10; ++i) {
                {
                    std::unique_lock<std::mutex> lck(mtx);
                    buffer.push(i);
                    std::cout << "Produced: " << i << std::endl;
                }
                cv.notify_one(); // Notificar a los consumidores que hay datos disponibles
                std::this_thread::sleep_for(std::chrono::milliseconds(100));
            }
        }

        void consumer() {
            for (int i = 0; i < 10; ++i) {
                int data;
                {
                    std::unique_lock<std::mutex> lck(mtx);
                    cv.wait(lck, [] { return !buffer.empty(); }); // Esperar a que haya datos disponibles
                    data = buffer.front();
                    buffer.pop();
                }
                std::cout << "Consumed: " << data << std::endl;
                std::this_thread::sleep_for(std::chrono::milliseconds(200));
            }
        }

        int main() {
            std::thread producerThread(producer);
            std::thread consumerThread(consumer);

            producerThread.join();
            consumerThread.join();

            return 0;
        }
        ```
    - **Usando Atomic:**
        ```cpp
        #include <iostream>
        #include <thread>
        #include <queue>
        #include <atomic>
        #include <condition_variable>

        std::queue<int> buffer;
        std::atomic<bool> dataAvailable(false);
        std::mutex mtx;
        std::condition_variable cv;

        void producer() {
            for (int i = 0; i < 10; ++i) {
                buffer.push(i);
                std::cout << "Produced: " << i << std::endl;
                dataAvailable.store(true);
                cv.notify_one(); // Notificar a los consumidores que hay datos disponibles
                std::this_thread::sleep_for(std::chrono::milliseconds(100));
            }
        }

        void consumer() {
            for (int i = 0; i < 10; ++i) {
                int data;
                {
                    std::unique_lock<std::mutex> lck(mtx);
                    cv.wait(lck, [] { return dataAvailable.load(); }); // Esperar a que haya datos disponibles
                    data = buffer.front();
                    buffer.pop();
                    dataAvailable.store(false);
                }
                std::cout << "Consumed: " << data << std::endl;
                std::this_thread::sleep_for(std::chrono::milliseconds(200));
            }
        }

        int main() {
            std::thread producerThread(producer);
            std::thread consumerThread(consumer);

            producerThread.join();
            consumerThread.join();

            return 0;
        }
        ```
    - **Usando Semaphore:**
        Desafortunadamente, la biblioteca estándar de C++ hasta C++20 no proporciona una implementación nativa de semáforos.
- **Readers-Writers:**
    - **Usando Mutex:**
        ```cpp
        #include <iostream>
        #include <thread>
        #include <shared_mutex>

        std::shared_mutex mutex;
        // declaracion del recurso donde se lee y escribe

        void writer() { //exclusive write access
            std::unique_lock<std::shared_mutex> lock(mutex);
            // escritura segura del recurso exclusiva para un trhead x vez
        }

        void reader(std::string n) { //read-only access simultaneously
            std::shared_lock<std::shared_mutex> lock(mutex);
            // lectura segura del recurso, para varios threads simultaneos
        }

        int main(int argc, char const *argv[])
        {
            std::thread w(writer);
            std::thread r1(reader,"r1");
            std::thread r2(reader,"r2");

            w.join(); 
            r1.join();
            r2.join();
            return 0;
        }
        ```
    - **Usando Atomic:**
        ```cpp
        #include <iostream>
        #include <thread>
        #include <atomic>

        std::atomic<int> sharedResource(0); // Variable compartida

        void writer() {
            // Operaciones de escritura
            sharedResource.store(1, std::memory_order_relaxed);
        }

        void reader(std::string name) {
            // Operaciones de lectura
            int value = sharedResource.load(std::memory_order_relaxed);
            std::cout << "Reader " << name << " read: " << value << std::endl;
        }

        int main(int argc, char const *argv[]) {
            std::thread w(writer);
            std::thread r1(reader, "r1");
            std::thread r2(reader, "r2");

            w.join();
            r1.join();
            r2.join();

            return 0;
        }
        ```
    - **Usando Semaphore:**
        Desafortunadamente, la biblioteca estándar de C++ hasta C++20 no proporciona una implementación nativa de semáforos.
- **Thread Pool:**
    Un ThreadPool es un conjunto de hilos previamente inicializados que están listos para procesar tareas de forma concurrente. En lugar de crear y destruir hilos repetidamente, se reutilizan estos hilos, reduciendo la sobrecarga y mejorando la eficiencia del sistema. Esto facilita el procesamiento rápido de tareas, adaptándose dinámicamente a la carga de trabajo y optimizando el uso de recursos del sistema. En esencia, el ThreadPool simplifica la gestión de hilos y la ejecución concurrente de tareas en programas informáticos.
    ```cpp
    #include <iostream>
    #include <vector>
    #include <queue>
    #include <thread>
    #include <functional>
    #include <mutex>
    #include <condition_variable>

    class ThreadPool {
    public:
        ThreadPool(size_t num_threads = std::thread::hardware_concurrency()) {
            for (size_t i = 0; i < num_threads; ++i) {
                threads_.emplace_back([this] {
                    while (true) {
                        std::function<void()> task;
                        {
                            std::unique_lock<std::mutex> lock(queue_mutex_);
                            cv_.wait(lock, [this] { return !tasks_.empty() || stop_; });
                            if (stop_ && tasks_.empty()) {
                                return;
                            }
                            task = std::move(tasks_.front());
                            tasks_.pop();
                        }
                        task();
                    }
                });
            }
        }

        ~ThreadPool() {
            {
                std::unique_lock<std::mutex> lock(queue_mutex_);
                stop_ = true;
            }
            cv_.notify_all();
            for (auto& thread : threads_) {
                thread.join();
            }
        }

        void enqueue(std::function<void()> task) {
            {
                std::unique_lock<std::mutex> lock(queue_mutex_);
                tasks_.emplace(std::move(task));
            }
            cv_.notify_one();
        }

    private:
        std::vector<std::thread> threads_;
        std::queue<std::function<void()>> tasks_;
        std::mutex queue_mutex_;
        std::condition_variable cv_;
        bool stop_ = false;
    };

    int main(int argc, char const *argv[]) 
    {
        ThreadPool pool(4);
        for (int i = 0; i < 5; ++i) {
            pool.enqueue([i] {
                std::cout << "Tarea " << i << " ejecutada por hilo " << std::this_thread::get_id() << std::endl;
                std::this_thread::sleep_for(std::chrono::milliseconds(100));
            });
        }
        return 0;
    }
    ```
- **Future-Promise:**
- **Monitor:**
- **Dead Lock avoiding:**
    Un deadlock es una situación en la que un conjunto de procesos quedan bloqueados porque cada proceso está sosteniendo un recurso y esperando otro recurso adquirido por algún otro proceso.
    Considera un ejemplo en el que dos trenes vienen uno hacia el otro en la misma vía y solo hay una vía, ninguno de los trenes puede moverse una vez que están frente a frente. Una situación similar ocurre en los sistemas operativos cuando hay dos o más procesos que tienen algunos recursos y esperan recursos mantenidos por otros. Por ejemplo, en el siguiente diagrama, el Hilo 1 está sosteniendo el Recurso 1 y esperando el recurso 2 que está adquirido por el Hilo 2, y el Hilo 2 está esperando el recurso 1.

    <p align="center">
        <img src="/deadlock.png" alt="image">
    </p>

    - **Solucion usando std::lock\:**
    La funcion std::lock() forma parte del header \<mutex>
    ```cpp
    #include <iostream>     //std::cout std::endl;
    #include <thread>       //std::thread
    #include <mutex>        //std::mutex std::lock std::lock_guard std::adopt_lock

    std::mutex m1, m2;      //assigned for resource 1 and 2 respectively

    void Resource1(std::string str) {
        std::cout << "Resource 1 owned by " << str << std::endl;
    }

    void Resource2(std::string str) {
        std::cout << "Resource 2 owned by " << str << std::endl;
    }

    void Thread1(std::string str) {
        std::lock(m1, m2);
        std::lock_guard<std::mutex> l1(m1, std::adopt_lock);
        Resource1(str);

        std::lock_guard<std::mutex> l2(m2, std::adopt_lock);
        Resource2(str);
    }

    void Thread2(std::string str) {
        std::lock(m1, m2);
        std::lock_guard<std::mutex> l2(m2, std::adopt_lock);
        Resource2(str);

        std::lock_guard<std::mutex> l1(m1, std::adopt_lock);
        Resource1(str);
    }

    int main(int argc, char const* argv[])
    {
        std::cout << "running process" << "\n";

        std::thread t1(Thread1, "Thread 1");
        std::thread t2(Thread2, "Thread 2");

        t1.join();
        t2.join();

        std::cout << "finished process" << "\n";
        return 0;
    }
    ```
    The output is:
    ```cpp
    running process
    Resource 1 owned by Thread 1
    Resource 2 owned by Thread 1
    Resource 2 owned by Thread 2
    Resource 1 owned by Thread 2
    finished process
    ```
    - **Solucion usando std::atomic:**    
    ```cpp
    #include <iostream>
    #include <thread>
    #include <atomic>

    std::atomic<bool> flag1(false);
    std::atomic<bool> flag2(false);

    void Resource1(std::string str) {
        std::cout << "Resource 1 owned by " << str << std::endl;
    }

    void Resource2(std::string str) {
        std::cout << "Resource 2 owned by " << str << std::endl;
    }

    void Thread1(std::string str) {
        while (!flag2.load(std::memory_order_acquire)); // Espera a que flag2 sea verdadero
        Resource1(str);
    }

    void Thread2(std::string str) {
        while (!flag1.load(std::memory_order_acquire)); // Espera a que flag1 sea verdadero
        Resource2(str);
    }

    void SetFlag1() {
        flag1.store(true, std::memory_order_release); // Establece flag1 en verdadero
    }

    void SetFlag2() {
        flag2.store(true, std::memory_order_release); // Establece flag2 en verdadero
    }

    int main() {
        std::cout << "running process" << "\n";

        std::thread t1(Thread1, "Thread 1");
        std::thread t2(Thread2, "Thread 2");
        std::thread t3(SetFlag1);
        std::thread t4(SetFlag2);

        t1.join();
        t2.join();
        t3.join();
        t4.join();

        std::cout << "finished process" << "\n";
        return 0;
    }
    ```
    - **Solucion usando std::semaphore:**
    ```cpp
    #include <iostream>
    #include <thread>
    #include <semaphore>

    std::semaphore semaphore1(0);
    std::semaphore semaphore2(0);

    void Resource1(std::string str) {
        std::cout << "Resource 1 owned by " << str << std::endl;
    }

    void Resource2(std::string str) {
        std::cout << "Resource 2 owned by " << str << std::endl;
    }

    void Thread1(std::string str) {
        semaphore2.acquire(); // Espera a que semaphore2 esté disponible
        Resource1(str);
        semaphore1.release(); // Libera semaphore1
    }

    void Thread2(std::string str) {
        semaphore1.acquire(); // Espera a que semaphore1 esté disponible
        Resource2(str);
        semaphore2.release(); // Libera semaphore2
    }

    int main() {
        std::cout << "running process" << "\n";

        std::thread t1(Thread1, "Thread 1");
        std::thread t2(Thread2, "Thread 2");

        semaphore1.release(); // Inicia el proceso liberando semaphore1

        t1.join();
        t2.join();

        std::cout << "finished process" << "\n";
        return 0;
    }
    ```
- **Thread Safety:**
    se refiere a la capacidad de un programa para funcionar correctamente cuando múltiples hilos están ejecutando concurrentemente, accediendo y modificando los mismos recursos compartidos. En un entorno multi-hilo, si no se manejan adecuadamente los accesos a los datos compartidos, pueden ocurrir condiciones de carrera y otros problemas que llevan a resultados inesperados y posiblemente incorrectos.

    ```cpp
    #include <atomic>

    class ThreadSafeCounter {
    public:
        ThreadSafeCounter() : count_(0) {}

        void increment() {
            // Atomically increment the count by 1
            count_.fetch_add(1, std::memory_order_relaxed);
        }

        int get() const {
            // Atomically load the current value of the count
            return count_.load(std::memory_order_relaxed);
        }

    private:
        std::atomic<int> count_;
    };
    ```
### 13. Conclusiones

- La programación multihilo en C++ es una herramienta poderosa pero compleja que permite aprovechar al máximo los recursos de la CPU y mejorar la eficiencia de los programas.
- La sincronización adecuada entre hilos es crucial para evitar condiciones de carrera, deadlocks y otros problemas comunes en entornos multihilo.
- Existen varias técnicas y herramientas en C++ para lograr la sincronización entre hilos, como mutex, variables atómicas, semáforos, future y condition_variable.
- Cada técnica tiene sus propias ventajas, desventajas y casos de uso específicos, por lo que es importante comprender cuándo y cómo aplicar cada una de ellas en diferentes situaciones.

- **Estrategia para Elegir entre Mutex, Shared_mutex, Atomic, Semaphore, Future y Condition_variable:**
    - **mutex:**
    Usar mutex cuando se necesita garantizar que solo un hilo acceda a un recurso compartido a la vez.
    Es adecuado para situaciones en las que se requiere exclusión mutua, como la modificación de estructuras de datos compartidas.
    - **shared_Mutex:**
    Usar shared_mutex cuando se necesita permitir que múltiples hilos accedan a un recurso compartido para lectura simultáneamente, pero solo un hilo pueda acceder para escritura en un momento dado.
    Es adecuado para situaciones en las que se tienen operaciones de lectura frecuentes pero operaciones de escritura menos frecuentes en una estructura de datos compartida.
    - **atomic:**
    Usar variables atómicas cuando se necesita realizar operaciones en una variable compartida de manera segura y sin bloqueos.
    Es adecuado para operaciones simples como incrementar o decrementar un contador compartido.
    - **semaphore:**
    Usar semáforos cuando se necesita controlar el acceso concurrente a un recurso compartido mediante un contador que puede ser incrementado y decrementado de manera segura por múltiples hilos.
    Es adecuado para situaciones en las que se necesita limitar la cantidad de hilos que pueden acceder a un recurso compartido simultáneamente.
    - **future:** 
    Se utiliza para representar el resultado de una tarea que se ejecutará en un hilo y que puede ser recuperado en otro hilo en el futuro. Permite a un hilo esperar hasta que el resultado esté disponible sin necesidad de bloquear el hilo activo. Esto es útil cuando se necesitan resultados de operaciones asincrónicas en un contexto multihilo.

    - **Condition_variable:** 
    Se utiliza para esperar a que se cumpla una condición específica antes de continuar la ejecución de un hilo. Permite a un hilo esperar hasta que una condición se cumpla, lo que generalmente implica que otro hilo notificará cuando se cumpla la condición. Esto es útil para sincronizar la ejecución de varios hilos basándose en ciertas condiciones.
