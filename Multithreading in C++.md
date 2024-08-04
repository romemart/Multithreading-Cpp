# Multithreading in C++

<!-- @import "[TOC]" {cmd="toc" depthFrom=1 depthTo=6 orderedList=false} -->

<!-- code_chunk_output -->

- [Multithreading in C++](#multithreading-in-c)
    - [1. Introduction](#1-introduction)
    - [2. Using the \<thread> header](#2-using-the-thread-header)
    - [3. Basic Synchronization](#3-basic-synchronization)
    - [4. Using the \<mutex> header](#4-using-the-mutex-header)
    - [5. Using the <shared_mutex> header](#5-using-the-shared_mutex-header)
    - [6. Using the \<atomic> header](#6-using-the-atomic-header)
    - [7. Using the \<semaphore> header](#7-using-the-semaphore-header)
    - [8. Using the <condition_variable> header](#8-using-the-condition_variable-header)
    - [9. Using the \<future> header](#9-using-the-future-header)
    - [10. Communication Between Threads](#10-communication-between-threads)
    - [11. Advanced Synchronization](#11-advanced-synchronization)
    - [12. Design Patterns](#12-design-patterns)
    - [13. Conclusions](#13-conclusions)

<!-- /code_chunk_output -->

### 1. Introduction

**Introduction to Parallel Tasks:**
In computer science, parallel tasks are those that run simultaneously, either across multiple processor cores or by subdividing a task into parts that can be executed at the same time. This approach is used to improve the performance and efficiency of computer systems by allowing multiple tasks to be performed concurrently.

**Evolution from Multiprocessing to Multithreading:**
- **Multiprocessing:** Multiprocessing refers to using multiple CPUs to execute tasks concurrently. Historically, multiprocessing was the first strategy to achieve parallelism in computer systems. It was developed in the 1970s and 1980s as a solution to improve system performance by executing multiple independent processes simultaneously.
- **Multithreading:** Multithreading involves dividing a task into independent subthreads that can execute concurrently within a single process. Though the concept of multithreading has existed since the early days of computing, its practical implementation was popularized with the introduction of standard libraries like `<pthread.h>` in the 1990s and `<thread>` in C++11 in 2011. The implementation of multithreading marked a significant milestone by providing a standard and portable interface for working with execution threads in C++.

    <p align="center">
        <img src="/threadvsprocess.png" alt="image">
    </p>

- **Shared Resources:**

    - Global or static variables shared among multiple threads.
    - Shared data structures, such as queues, stacks, linked lists, etc.
    - Network resources, such as TCP/IP or UDP sockets.
    - Shared memory between processes.
    - Shared databases.
    - Hardware resources, such as peripheral devices.
    - Application state variables that may be modified by multiple threads.

    Understanding the importance of protecting shared resources allows us to address multithreading challenges more effectively and develop robust and secure applications in concurrent environments.

**Comparison of Multiprocessing vs. Multithreading:**

| Aspect                | Multiprocessing                                     | Multithreading                                       |
|------------------------|-----------------------------------------------------|-------------------------------------------------------|
| **Advantages**           | - Complete isolation between processes.             | - Lower resource consumption in terms of memory.  |
|                        | - Higher stability: one process can fail without affecting others. | - Higher efficiency in thread communication by sharing memory. |
|                        | - Scalability in systems with multiple CPUs/cores. | - Shorter startup time for a new thread compared to a new process. |
|                        | - Higher security, as errors in one process do not affect others. |                                                       |
| **Disadvantages**        | - Higher resource consumption (memory, startup time). | - Higher complexity in managing concurrency and synchronization. |
|                        | - Higher complexity in inter-process communication (IPC). | - Possible increase in code complexity and debugging due to concurrency. |
|                        | - Lower efficiency in inter-process communication due to IPC. | - Vulnerability to race conditions and synchronization problems. |
|                        | - Lower efficiency in single-core systems due to context switching. | - Risk of deadlock and performance issues if synchronization is not handled properly. |

- **Differences between Concurrency and Parallelism:**
    Concurrency refers to the simultaneous execution of multiple tasks, while parallelism involves the actual simultaneous execution of these tasks on different processor cores.

- **Examples of Applications Where Multithreading Is Useful:**

    - A video playback application may use separate threads to decode the video, load frames into memory, and display them on screen simultaneously.
    - A web server can use threads to handle multiple client requests concurrently, allowing the server to serve several clients at the same time.

- **Design Considerations and Challenges Associated with Multithreading:**
    It's important to carefully design the program structure to avoid concurrency issues such as race conditions and deadlocks. Additionally, proper synchronization between threads and management of shared resources are critical aspects of designing multithreaded programs.

- **Structure and Classification of Multithreading Topics:**
In this material, we will cover the main headers of the C++ Standard Library (Thread support library) for multithreaded programming. These headers can be classified into three main categories based on their usage:

    <p align="center">
        <img src="/classification.png" alt="image">
    </p>

    This set of headers covers the main needs for multithreading in C++, including thread creation and management, synchronization of access to shared data, and synchronization of events between threads. However, depending on your specific requirements, you may need to use other parts of the standard library or even external libraries.

### 2. Using the \<thread> header

The <font color="red">\<thread></font> header in C++11 provides functionalities to work with execution threads in C++ programs. Here's an overview of the `<thread>` header along with a table highlighting some of its key classes and functions:

| Content          | Description                                         |
|------------------|-----------------------------------------------------|
| thread <span style="color:green;font-size:12px">(C++11)</span> | Manages a separate thread <span style="color:green;font-size:12px">(class)</span> |
| this_thread | Provides functions that access the current thread of execution |
| jthread <span style="color:green;font-size:12px">(C++20)</span> | `std::thread` with support for auto-joining and cancellation <span style="color:green;font-size:12px">(class)</span> |
| yield <span style="color:green;font-size:12px">(C++11)</span> | Suggests that the implementation reschedule execution of threads <span style="color:green;font-size:12px">(Function)</span> |
| get_id <span style="color:green;font-size:12px">(C++11)</span> | Returns the thread id of the current thread <span style="color:green;font-size:12px">(Function)</span> |
| sleep_for <span style="color:green;font-size:12px">(C++11)</span> | Stops the execution of the current thread for a specified time duration <span style="color:green;font-size:12px">(Function)</span> |
| sleep_until <span style="color:green;font-size:12px">(C++11)</span> | Stops the execution of the current thread until a specified time point <span style="color:green;font-size:12px">(Function)</span> |

- **Using `std::thread` Class**
  `std::thread` is a class in the C++ standard library used to create and control execution threads in a program. It provides an interface for creating and managing threads efficiently.

    | Members       | Description                                        |
    |---------------|----------------------------------------------------|
    | joinable      | Checks whether the thread is joinable, i.e., potentially running in parallel context <span style="color:green;font-size:12px">(public member function)</span> |
    | join          | Waits for the thread to finish its execution <span style="color:green;font-size:12px">(public member function)</span> |
    | detach        | Permits the thread to execute independently from the thread handle <span style="color:green;font-size:12px">(public member function)</span> |
    | get_id        | Returns the id of the thread <span style="color:green;font-size:12px">(public member function)</span> |
    | hardware_concurrency[static] | Returns the number of concurrent threads supported by the implementation <span style="color:green;font-size:12px">(public member function)</span> |

- **Using `std::thread` to Create Threads:**
  To create a thread, you can instantiate a `std::thread` object by providing a function or a functor object as an argument. When a `std::thread` object is created, a new execution thread is started, and the specified function is executed.

  C++ code example using `<thread>`:
  ```cpp
  #include <iostream>
  #include <thread>

  void task() {
      std::cout << "Hello from the thread!\n";
  }
      
  int main() {
      // Create a new thread and execute the task function
      std::thread t(task);

      // Wait for the thread to finish its execution
      t.join();

      return 0;
  }
  ```
  Output:
  ```cpp
  Hello from the thread!
  ```
  In this example, a new thread is created using `std::thread`, and the `task` function is executed on that thread. Then, the main program waits for the thread to finish its execution using `t.join()`.

- **Issues in Thread Creation and Detachment:**

  ```cpp
  std::thread t; // Not joinable, uninitialized
  t.detach(); // Detaches the thread without associated task
  t = std::thread([] { /* thread code */ }); // Invalid operation, will cause an exception or undefined behavior
  ```
  `std::thread t;`: Creates a `std::thread` object without initialization, leaving it in a non-joinable state (cannot be joined to the main thread).
  `t.detach();`: Calls the `detach()` method on the uninitialized `t` object. This detaches the `t` object from the main thread, but since it has no associated task, there's nothing actually to execute. The `t` object enters a special state after this operation.
  `t = std::thread([] { /* thread code */ });`: Trying to assign a new initialized thread to a `std::thread` object that is in the special state after `detach()` is an invalid operation and prohibited by the C++ standard.

  When attempting this assignment, one of the following scenarios will occur:
  - **Exception:** It's highly likely that a `std::system_error` or related exception will be thrown, depending on the implementation of the C++ standard library being used.
  - **Undefined Behavior:** If the C++ standard library implementation does not throw an exception, the program enters a state of undefined behavior, which can cause unpredictable errors or even program failure.

### 3. Basic Synchronization
Basic synchronization in multithreaded programming involves simple techniques to control thread execution and ensure consistency in accessing shared resources.
- **Thread Blocking with `std::this_thread::sleep_for()` and `std::this_thread::sleep_until()`:**
  These functions are used to suspend the execution of a thread for a specific period of time. `sleep_for()` suspends the thread for a determined amount of time, while `sleep_until()` suspends the thread until a specific moment in time.
- **Thread Identification with `std::this_thread::get_id()`:**
  This function returns a unique identifier for the thread it's called in. It's useful for identifying specific threads and performing actions based on the current thread.
- **Usage of Thread-Local Variables and Global Variables:**
  Thread-local variables are variables with a thread-local scope, meaning each thread has its own instance of the variable. This is achieved using the `thread_local` modifier. Global variables, on the other hand, are variables shared across all threads and can cause concurrency issues if accessed concurrently without proper synchronization.
  Example usage of `std::this_thread::sleep_for()` and `std::this_thread::get_id()`:
  ```cpp
  #include <iostream>
  #include <thread>
  #include <chrono>

  void task() {
      std::cout << "Thread running. ID: " << std::this_thread::get_id() << "\n";
      std::this_thread::sleep_for(std::chrono::seconds(2)); // Suspend thread for 2 seconds
      std::cout << "Thread resumed after sleeping\n";
  }

  int main() {
      std::thread t(task);
      std::cout << "Main thread. ID: " << std::this_thread::get_id() << "\n";
      t.join(); // Wait for thread to finish
      return 0;
  }
  ```
  In this example, it demonstrates how to obtain the thread ID using `std::this_thread::get_id()` and how to suspend the thread execution for a certain period of time using `std::this_thread::sleep_for()`. This illustrates basic thread identification and blocking in C++.

### 4. Using the \<mutex> header
Defined as <font color="red">\<mutex></font> header in the Standard Library since C++11, provides facilities to work with mutual exclusion mechanisms, namely mutexes. This header defines several classes and functions that are used to create, lock, and unlock mutexes in a multithreaded environment.

| Classes                       | Description                       |
| -----------                 | -----------                       |
|mutex <span style="color:green;font-size:12px">(C++11)</span>                |Provides basic mutual exclusion facility <span style="color:green;font-size:12px">(class)</span>|
|timed_mutex <span style="color:green;font-size:12px">(C++11)</span>          |Provides mutual exclusion facility which implements locking with a timeout <span style="color:green;font-size:12px">(class)</span>|
|recursive_mutex <span style="color:green;font-size:12px">(C++11)</span>      |Provides mutual exclusion facility which can be locked recursively by the same thread <span style="color:green;font-size:12px">(class)</span>|
|recursive_timed_mutex <span style="color:green;font-size:12px">(C++11)|Provides mutual exclusion facility which can be locked recursively by the same thread and implements locking with a timeout <span style="color:green;font-size:12px">(class)</span>|

|Locks                                                         | Description                     |
| --------                                                         | -----------                     |
|lock_guard <span style="color:green;font-size:12px">(C++11)</span>|Implements a strictly scope-based mutex ownership wrapper <span style="color:green;font-size:12px">(class template)</span>|
|unique_lock <span style="color:green;font-size:12px">(C++11)</span>|Implements movable mutex ownership wrapper <span style="color:green;font-size:12px">(class template)</span>|
|shared_lock <span style="color:green;font-size:12px">(C++11)</span>|Implements movable shared mutex ownership wrapper <span style="color:green;font-size:12px">(class template)</span>|
|scoped_lock <span style="color:green;font-size:12px">(C++17)</span>|Deadlock-avoiding RAII wrapper for multiple mutexes <span style="color:green;font-size:12px">(class template)</span> |

|Tag arguments                                                     | Description                     |
| --------                                                         | -----------                     |
|defer_lock <span style="color:green;font-size:12px">(C++11)</span>|Do not immediately acquire  ownership of the mutex <span style="color:green;font-size:12px">defer_lock_t</span>|
|try_to_lock <span style="color:green;font-size:12px">(C++11)</span>|Try to acquire ownership of the mutex without blocking <span style="color:green;font-size:12px">try_to_lock_t</span>|
|adopt_lock <span style="color:green;font-size:12px">(C++11)</span>|Assume the calling thread already has ownership of the mutex <span style="color:green;font-size:12px">adopt_lock_t</span>|

These three tags apply to `std::unique_lock` and `std::shared_lock`. On the other hand, `std::lock_guard` only accepts the `std::adopt_lock` tag. In the absence of any of these tags, `std::unique_lock`, `std::shared_lock`, and `std::lock_guard` will acquire ownership of the mutex immediately. This distinction is important for understanding mutex control options in C++.

|Functions   | Description                     |
| --------   |  --------                       |
|try_lock <span style="color:green;font-size:12px">(C++11)</span>|Attempts to obtain ownership of mutexes via repeated calls to try_lock <span style="color:green;font-size:12px">(function template)</span>|
|lock <span style="color:green;font-size:12px">(C++11)</span>|Locks specified mutexes, blocks if any are unavailable <span style="color:green;font-size:12px">(function template)</span>|
|call_once|invokes a function only once even if called from multiple threads <span style="color:green;font-size:12px">(function template)</span>|

- **Using the `std::mutex` class:**
A fundamental type of mutex that provides mutual exclusion locking. It is used to protect critical sections of code.

| Members                       | Description                       |
| -----------                 | -----------                       |
|lock     |Locks the mutex, blocks if the mutex is not available <span style="color:green;font-size:12px">(public member function)</span>         |
|try_lock |Tries to lock the mutex, returns if the mutex is not available <span style="color:green;font-size:12px">(public member function)</span>|
|unlock   |Unlocks the mutex <span style="color:green;font-size:12px">(public member function)</span>                                       |

Instantiation example:
```cpp
std::mutex m;

m.lock();
m.unlock();
m.try_lock();
```
Example using the `std::mutex` class:
```cpp
#include <iostream>  // std::cout, std::endl
#include <thread>    // std::thread
#include <mutex>     // std::mutex, lock(), unlock()

int globalVar = 5;
std::mutex m;

void task() {
    // Some statements
    m.lock();
        globalVar += 2;
        std::cout << globalVar << '\n';
    m.unlock();
}

int main(int argc, char* argv[]) {
    std::thread t1(task);
    t1.join();

    return 0;
}
```
Output:
```cpp
7
```
Example with `try_lock()`:

```cpp
#include <iostream>  // std::cout, std::endl
#include <thread>    // std::thread
#include <mutex>     // std::mutex

std::mutex m;

void task(const std::string& str) {
    if (m.try_lock()) {
        std::cout << str << " locked the mutex" << "\n";
        m.unlock();
    }
    else {
        std::cout << str << " failed to lock the mutex" << "\n";
    }
}

int main(int argc, char* argv[]) {
    std::thread t1(task, "t1");
    std::thread t2(task, "t2");

    t1.join();
    t2.join();

    return 0;
}
```
- **Using the `std::timed_mutex` class:**
    Similar to `std::mutex`, but allows lock acquisition with timing.

| Members                       | Description                       |
| -----------                 | -----------                       |
|lock     |Locks the mutex, blocks if the mutex is not available <span style="color:green;font-size:12px">(public member function)</span>         |
|try_lock |Tries to lock the mutex, returns if the mutex is not available <span style="color:green;font-size:12px">(public member function)</span>|
|try_lock_for|Tries to lock the mutex, returns if the mutex has been unavailable for the specified relative time duration <span style="color:green;font-size:12px">(public member function)</span>         |
|try_lock_until|Tries to lock the mutex, returns if the mutex has been unavailable for the specified absolute time point has been reached <span style="color:green;font-size:12px">(public member function)</span>         |
|unlock   |Unlocks timed mutex <span style="color:green;font-size:12px">(public member function)</span>    |

Instantiation example:
```cpp
std::timed_mutex m;

m.lock();
m.unlock();
m.try_lock();
m.try_lock_for(rel_time);
m.try_lock_until(abs_time);
```
Example with `try_lock_for(rel_time)`:

This method attempts to lock a `std::timed_mutex` object for a maximum specified time `rel_time`, or until the object is unlocked using `unlock()`:
- If the mutex is not locked by any thread, it locks immediately and returns true.
- If the mutex is locked by another thread, the current thread waits until the mutex is unlocked or until   `rel_time` has elapsed, whichever happens first. If the mutex is unlocked before `rel_time` elapses, the blocking thread locks it and returns true. If `rel_time` expires before the mutex is unlocked, the function returns false.
- If the current thread already holds the mutex locked, undefined behavior occurs.

    ```cpp
    #include <iostream>       // std::cout
    #include <chrono>         // std::chrono::milliseconds
    #include <thread>         // std::thread
    #include <mutex>          // std::timed_mutex

    std::timed_mutex mtx;

    void task(int id, std::chrono::seconds timeout) {
        if (mtx.try_lock_for(timeout)) {
            std::cout << "Thread " << id << " acquired the lock\n";
            // Simular trabajo dentro de la sección crítica
            std::this_thread::sleep_for(std::chrono::seconds(20));
            mtx.unlock();
            std::cout << "Thread " << id << " released the lock\n";
        } else {
            std::cout << "Thread " << id << " failed to acquire the lock\n";
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
    Output: 
    ```cpp
    Thread 1 acquired the lock
    Thread 2 failed to acquire the lock
    Thread 1 released the lock
    ```

Creating two threads, t1 and t2.
t1 calls `try_lock_for(std::chrono::seconds(10))`. Since the `std::timed_mutex` is not locked, t1 acquires the lock, and "Thread 1 acquired the lock" is printed.
t1 simulates work inside the critical section for 20 seconds.
While t1 is inside the critical section, t2 calls `try_lock_for(std::chrono::seconds(10))`. Since t1 already holds the lock, t2 attempts to acquire the lock for a maximum of 10 seconds.
After 10 seconds, as t1 has not released the lock yet, `try_lock_for` on t2 returns false, so "Thread 2 failed to acquire the lock" is printed.
After 20 seconds, t1 releases the lock by calling `unlock()`, and "Thread 1 released the lock" is printed.

Example with `try_lock_until(abs_time)`:

This method attempts to lock a `std::timed_mutex` object until a specified absolute time `abs_time`, or until the object is unlocked using `unlock()`:

If the mutex is not locked by any thread, it locks immediately and returns true.
If the mutex is locked by another thread, the current thread waits until the mutex is unlocked or until `abs_time` is reached, whichever happens first. If the mutex is unlocked before `abs_time` is reached, the blocking thread locks it and returns true. If `abs_time` is reached before the mutex is unlocked, the function returns false.
If the current thread already holds the mutex locked, undefined behavior occurs.

```cpp
#include <iostream>       // std::cout
#include <chrono>         // std::chrono::milliseconds, std::chrono::system_clock
#include <thread>         // std::thread
#include <mutex>          // std::timed_mutex

std::timed_mutex mtx;

void task(int id, std::chrono::seconds timeout) {
    auto abs_time = std::chrono::system_clock::now() + timeout;
    if (mtx.try_lock_until(abs_time)) {
        std::cout << "Thread " << id << " acquired the lock\n";
        // Simulate work inside the critical section
        std::this_thread::sleep_for(std::chrono::seconds(20));
        mtx.unlock();
        std::cout << "Thread " << id << " released the lock\n";
    } else {
        std::cout << "Thread " << id << " failed to acquire the lock\n";
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
Output: 
```cpp
Thread 1 acquired the lock
Thread 2 failed to acquire the lock
Thread 1 released the lock
```

t1 acquires the lock of the `std::timed_mutex` because it is not locked initially.
When t2 calls `try_lock_until(abs_time)`, since t1 already holds the lock, t2 attempts to acquire the lock until `abs_time` is reached (10 seconds after the current time) or until t1 releases the lock, whichever happens first.
Since t1 does not release the lock before `abs_time` is reached, `try_lock_until` in t2 returns false.
After 20 seconds (simulating work), t1 releases the lock by calling `unlock()`.
- **Using `std::recursive_mutex` class:** Provides mutual exclusion facility which can be locked recursively by the same thread
```cpp
#include <iostream>
#include <thread>
#include <mutex>

std::recursive_mutex rmutex;

void recursive_function(int count) {
    if (count < 0) return;
    
    // Lock the recursive mutex
    rmutex.lock();
    
    std::cout << "Count: " << count << " - Thread ID: " << std::this_thread::get_id() << std::endl;
    
    // call recursive
    recursive_function(count - 1);
    
    // Unlock the recursive mutex
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
Output:
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
Using this class ensures that the same thread recursively calling the function is not blocking as part of the same operation.
- **Using the `std::lock_guard` class:** follows the RAII principle to ensure safe acquisition and release of a mutex, simplifying the code and improving robustness and security in multithreaded environments.

    ```cpp
    #include <iostream>
    #include <thread>
    #include <mutex>

    std::mutex mtx;
    int shared_data = 0;

    void threadFunc() {
        std::lock_guard<std::mutex> lock(mtx); // Safely acquire the mutex
        shared_data++; // Safely access the shared resource
    }

    int main(int argc, char* argv[]) {
        std::thread t1(threadFunc);
        std::thread t2(threadFunc);

        t1.join();
        t2.join();

        std::cout << "Final value of the shared data: " << shared_data << std::endl;
        return 0;
    }
    ```
    Output: 
    ```cpp
    Final value of the shared data: 2
    ```
- **Using the `std::unique_lock` class:**


    ```cpp
    #include <iostream>
    #include <thread>
    #include <mutex>

    std::mutex mtx;
    int shared_data = 0;

    void threadFunc1() {
        std::unique_lock<std::mutex> lock(mtx, std::defer_lock); // Using std::defer_lock
        // The mutex is not automatically locked
        lock.lock(); // Explicitly lock the mutex
        shared_data++;
    }

    void threadFunc2() {
        std::unique_lock<std::mutex> lock(mtx, std::try_to_lock); // Using std::try_to_lock
        if (lock.try_lock()) { // Try to lock the mutex (don't wait if it's already locked)
            shared_data++;
        }
    }

    void threadFunc3() {
        mtx.lock(); // Lock the mutex manualy
        std::unique_lock<std::mutex> lock(mtx, std::adopt_lock); // Adoption of the lock by the previous line
        // unique_lock assumes the release of resources 
        shared_data++;
    }

    int main(int argc, char* argv[]) {
        std::thread t1(threadFunc1);
        std::thread t2(threadFunc2);
        std::thread t3(threadFunc3);

        t1.join();
        t2.join();
        t3.join();

        std::cout << "Final value of the shared data: " << shared_data << std::endl;
        return 0;
    }
    ```
    Output:
    ```cpp
    Final value of the shared data: 3
    ```
- **Using the `std::scoped_lock` class:**
    La clase `std::scoped_lock` es una versión moderna y más segura de las funciones anteriores. Es posible utilizarla para bloquear múltiples mutexes
    ```cpp
    #include <iostream>
    #include <thread>
    #include <mutex>

    std::mutex m1, m2;

    void function1(std::string s) {
        std::scoped_lock lock(m1, m2);
        std::cout << "Resource 1 protected with m1 used by: " << s << '\n';
        std::cout << "Resource 2 protected with m2 used by: " << s << '\n';
    }

    void function2(std::string s) {
        std::scoped_lock lock(m1, m2);
        std::cout << "Resource 2 protected with m2 used by: " << s << '\n';
        std::cout << "Resource 1 protected with m1 used by: " << s << '\n';
    }

    int main(int argc, char* argv[]) {
        std::thread t1(function1, "t1");
        std::thread t2(function2, "t2");

        t1.join();
        t2.join();
        return 0;
    }
    ```
    Output:
    ```cpp
    Resource 1 protected with m1 used by: t1
    Resource 2 protected with m2 used by: t1
    Resource 2 protected with m2 used by: t2
    Resource 1 protected with m1 used by: t2
    ```

### 5. Using the <shared_mutex> header

| Classes                       | Description                       |
| -----------                 | -----------                       |
|shared_mutex <span style="color:green;font-size:12px">(C++17)</span>                |provides shared mutual exclusion facility <span style="color:green;font-size:12px">(class)</span>|
|shared_timed_mutex <span style="color:green;font-size:12px">(C++14)</span>          |provides shared mutual exclusion facility and implements locking with a timeout <span style="color:green;font-size:12px">(class)</span>|
|shared_lock <span style="color:green;font-size:12px">(C++14)</span>          |implements movable shared mutex ownership wrapper <span style="color:green;font-size:12px">(class template)</span>|

|Exclusive locking       |   Desscription                            |
| --------       | -----------                                                                                                |
|lock |locks the mutex, blocks if the mutex is not available <span style="color:green;font-size:12px">(public member function)</span>|
|try_lock     |tries to lock the mutex, returns if the mutex is not available <span style="color:green;font-size:12px">(public member function)</span>         |
|unlock   |Unlock the mutex (shared ownership) <span style="color:green;font-size:12px">(public member function)</span>                                                |

|Shared locking       |   Desscription                            |
| --------       | -----------                                                                                                |
|lock_shared |locks the mutex for shared ownership, blocks if the mutex is not available <span style="color:green;font-size:12px">(public member function)</span>|
|try_lock_shared     |tries to lock the mutex for shared ownership, returns if the mutex is not available <span style="color:green;font-size:12px">(public member function)</span>         |
|unlock_shared   |Unlock the mutex (shared ownership) <span style="color:green;font-size:12px">(public member function)</span>                                                |

Example using shared_mutex:
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
Output:
```cpp
Writer: Added 0
Writer: Added 1
Writer: Added 2
Writer: Added 3
Writer: Added 4
Reader: thread r2 = 0 1 2 3 4
Reader: thread r1 = 0 1 2 3 4
```

### 6. Using the \<atomic> header 
The `std::atomic` class is a tool for manipulating atomic values in a multithreaded program. This allows operations on variables to be atomic, ensuring thread safety.

| Members                       | Description                       |
| -----------                 | -----------                       |
|is_lock_free|checks if the atomic object is lock-free <span style="color:green;font-size:12px">(public member function)</span>|
|store|atomically replaces the value of the atomic object with a non-atomic argument <span style="color:green;font-size:12px">(public member function)</span>|
|load|atomically obtains the value of the atomic object <span style="color:green;font-size:12px">(public member function)</span>|
|operator T|loads a value from an atomic object <span style="color:green;font-size:12px">(public member function)</span>|
|exchange|atomically replaces the value of the atomic object and obtains the value held previously <span style="color:green;font-size:12px">(public member function)</span>|
|compare_exchange_weak compare_exchange_strong|atomically compares the value of the atomic object with non-atomic argument and performs atomic exchange if equal or atomic load if not <span style="color:green;font-size:12px">(public member function)</span>|
|wait <span style="color:green;font-size:12px">(C++20)</span>|blocks the thread until notified and the atomic value changes <span style="color:green;font-size:12px">(public member function)</span>|
|notify_one <span style="color:green;font-size:12px">(C++20)</span>|notifies at least one thread waiting on the atomic object <span style="color:green;font-size:12px">(public member function)</span>|
|notify_all <span style="color:green;font-size:12px">(C++20)</span>|notifies all threads blocked waiting on the atomic object <span style="color:green;font-size:12px">(public member function)</span>|

| Contants                       | Description                       |
| -----------                 | -----------                        |
|is_always_lock_free `[static]`<span style="color:green;font-size:12px">(C++17)</span> | indicates that the type is always lock-free<span style="color:green;font-size:12px">(public member function)</span>|

Reviewing:
```cpp
#include <atomic>
#include <thread>
#include <chrono>

int main() {
    // 1. Constructor and assignment
    {
        std::atomic<int> atom(10);
        atom = 20;
        
        // Result:
        // atom.load() = 20
    }

    // 2. load() and store()
    {
        std::atomic<int> atom(5);

        int value = atom.load();
        // Result:
        // value = 5

        atom.store(10);
        // Result:
        // atom.load() = 10
    }

    // 3. exchange()
    {
        std::atomic<int> atom(5);

        int previous_value = atom.exchange(10);
        // Result:
        // previous_value = 5
        // atom.load() = 10
    }

    // 4. compare_exchange_strong()
    {
        std::atomic<int> atom(5);
        int expected = 5;
        bool success = atom.compare_exchange_strong(expected, 10);

        // Result:
        // success = true (expected value is equal to previous value)
        // atom.load() = 10
    }

    // 5. fetch_add() and fetch_sub()
    {
        std::atomic<int> atom(5);

        int previous_value = atom.fetch_add(3);
        // Result:
        // previous_value = 5
        // atom.load() = 8

        previous_value = atom.fetch_sub(2);
        // Result:
        // previous_value = 8
        // atom.load() = 6
    }

    // 6. Atomic arithmetic operators
    {
        std::atomic<int> atom(5);

        atom += 3;
        // Result:
        // atom.load() = 8

        atom -= 2;
        // Result:
        // atom.load() = 6

        ++atom;
        // Result:
        // atom.load() = 7

        --atom;
        // Result:
        // atom.load() = 6
    }

    // 7. is_lock_free()
    {
        std::atomic<int> atom;

        bool is_lock_free = atom.is_lock_free();
        // Result:
        // is_lock_free is generally true on most modern architectures
    }

    // 8. compare_exchange_weak()
    {
        std::atomic<int> atom(5);
        int expected = 5;
        bool success = atom.compare_exchange_weak(expected, 10);

        // Result:
        // success = true (expected value is equal to previous value)
        // atom.load() = 10
        // expected = 5

        // Try again, this time it should fail
        expected = 5;
        success = atom.compare_exchange_weak(expected, 15);

        // Result:
        // success = false (expected value is not equal to current value)
        // atom.load() = 10 (unchanged)
        // expected = 10 (updated to the current value of atom)

        // Typical usage in a spin-wait loop
        atom.store(0);
        int desired_value = 1;

        std::thread t([&atom]() {
            std::this_thread::sleep_for(std::chrono::milliseconds(100));
            atom.store(1);
        });

        while (!atom.compare_exchange_weak(desired_value, 2)) {
            desired_value = 1; // Reset expected value in each iteration
        }

        t.join();

        // Result:
        // atom.load() = 2
    }

    return 0;
}
```
An esasy example where two threads increase a atomic global variable
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
Output:
```cpp
Counter: 200000
```
In this code, counter is an atomic variable. This means that operations on counter are atomic, i.e., they complete in a single step. Therefore, even though increment is called by two different threads, the final result of counter will be correct.

To access the value of counter, you can use:
```cpp
std::cout << "Counter: " << counter << '\n';
std::cout << "Counter: " << counter.load() << '\n';
```
To modify the value of counter, you can also:
```cpp
counter = 100;
counter.store(100);
```
Atomic modification operations: These operations modify the value of the atomic variable and return its original value in a single atomic operation. They include `fetch_add`, `fetch_sub`, `fetch_and`, `fetch_or`, and `fetch_xor`.

Atomic exchange operations: These operations set the value of the atomic variable and return its original value in a single atomic operation. They include exchange and `compare_exchange_strong`.

```cpp
#include <atomic>
#include <iostream>

int main() {
    std::atomic<int> counter(0);

    int old_value = counter.fetch_add(1);  // Atomic modification operation
    std::cout << "Old value: " << old_value << ", New value: " << counter << '\n';

    old_value = counter.exchange(100);  // Atomic exchange operation
    std::cout << "Old value: " << old_value << ", New value: " << counter << '\n';

    return 0;
}
```
Output:
```cpp
Old value: 0, New value: 1
Old value: 1, New value: 100
```
Atomic operations like `fetch_add`, `fetch_sub`, `exchange`, and others modify the value of the atomic variable and return its original value in a single atomic operation. Therefore, when you do something like `int old_value = counter.fetch_add(1);`, you are assigning old_value the original value of counter before it is incremented.

- **Memory Order and Model:**

    | Value                      | Explanation                       |
    | -----------                 | -----------                      |
    |memory_order_relaxed|Provides the minimum possible memory ordering, meaning atomic operations are performed without any guarantee of ordering with other atomic or non-atomic operations.|
    |memory_order_consume|Ensures that previous operations performed on the same atomic variable are visible to the current thread, but it doesn't guarantee any memory ordering with other atomic or non-atomic variables.|
    |memory_order_acquire|Guarantees that previous operations performed on the same atomic variable are visible to other threads and that no preceding operation is performed after the current atomic operation completes.|
    |memory_order_release|Guarantees that previous operations performed on the same atomic variable are visible to other threads and that no preceding operation is performed after the current atomic operation completes.|
    |memory_order_acq_rel|Combines the effects of memory_order_acquire and memory_order_release, meaning the current atomic operation acts as an acquire-release barrier.|
    |memory_order_seq_cst|Offers the maximum possible memory ordering, meaning all atomic operations are performed in a sequential and consistent order across all threads. The default memory order for atomic operations is memory_order_seq_cst.|

    - **Load and Store Operations:** The load and store methods of atomic variables can take a memory   ordering model as an argument. For example, you can do:
        ```cpp
        counter.load(std::memory_order_acquire)
        counter.store(100, std::memory_order_release)
        ```
    - **Atomic Modification Operations:**  Methods like `fetch_add`, `fetch_sub`, `fetch_and`, `fetch_or`, and `fetch_xor` can also take a memory ordering model as an argument. For example, you can do:
        ```cpp
        counter.fetch_add(1, std::memory_order_relaxed).
        ```
    - **Atomic Exchange Operations:** The exchange and `compare_exchange_strong` methods can take a memory ordering model as an argument. For example, you can do: 
        ```cpp
        counter.exchange(100, std::memory_order_acq_rel).
        ```
- **Memory Fences or Barriers:** Memory fences, also known as "memory fences" or "memory barriers," are     mechanisms that prevent certain optimizations from reordering read and write operations in ways that could  cause issues in a multithreaded environment.

    Here's an example of how to use `std::atomic_thread_fence` and `std::atomic_signal_fence` to create memory barriers:
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
            // Wait
        }
        std::atomic_thread_fence(std::memory_order_acquire);
        if (data != 42) {
            std::cout << "Memory barrier failed!\n";
        }
    }
    ```
    In this code, `std::atomic_thread_fence(std::memory_order_release)` in the producer ensures that the write to data completes before data_ready is set to true. Similarly, `std::atomic_thread_fence(std::memory_order_acquire)` in the consumer ensures that the consumer thread sees the write to data after seeing data_ready as true.

- **Sequential Consistency:** Sequential consistency is a type of memory ordering that ensures all memory operations are perceived in the same order across all threads. In other words, if you have two operations A and B that run in different threads, and A happens before B in the program's code, then all threads will see A happening before B.

    In C++, std::memory_order_seq_cst is the memory ordering model that provides sequential consistency. It's the default memory order for atomic operations and is the strongest in terms of ordering constraints.

    Here's an example of how to use std::memory_order_seq_cst in C++:
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
            // Wait
        }
        if (data.load(std::memory_order_relaxed) != 42) {
            std::cout << "Sequential consistency failed!\n";
        }
    }
    ```
- **Relaxation and Synchronization:** These are two fundamental concepts in multithreaded programming that are closely related to memory model and memory ordering.
    - Relaxation: Relaxation refers to allowing some degree of reordering of memory operations to improve performance. For example, the compiler or hardware architecture may decide to reorder read and write operations to optimize cache usage or to take advantage of out-of-order execution instructions. However, this reordering can only be done if it doesn't affect the correctness of the program.
    - Synchronization: Synchronization refers to restricting the reordering of memory operations to ensure program correctness. For example, if you have one thread writing to a variable and another thread reading from that variable, you may need to synchronize the threads to ensure that the write completes before the read. This can be done using atomic operations with appropriate memory ordering models, or using synchronization mechanisms like mutexes, semaphores, or memory barriers.

        ```cpp
        #include <atomic>
        #include <thread>
        #include <iostream>

        std::atomic<int> data(0);
        std::atomic<bool> ready(false);

        void producer() {
            data.store(42, std::memory_order_relaxed);  // Relaxation
            ready.store(true, std::memory_order_release);  // Synchronization
        }

        void consumer() {
            while (!ready.load(std::memory_order_acquire)) {  // Synchronization
                std::this_thread::yield();  // Wait
            }
            std::cout << "Data: " << data.load(std::memory_order_relaxed) << '\n';  // Relaxation
        }

        int main(int argc, char* argv[]) {
            std::thread t1(producer);
            std::thread t2(consumer);

            t1.join();
            t2.join();

            return 0;
        }
        ```
        In this code, the producer thread writes to data and then sets ready to true. The consumer thread waits until ready is true and then reads data.

        Writing to data is done with std::memory_order_relaxed, meaning this operation can be reordered with respect to other operations in the same thread. This is an example of relaxation.

        Writing to ready is done with std::memory_order_release, and the corresponding read in the consumer thread is done with std::memory_order_acquire. This ensures that all memory operations (including writing to data) that happen before ready.store(true) in the producer thread are visible to the consumer thread after ready.load(). This is an example of synchronization.

### 7. Using the \<semaphore> header
| Classes                       | Description                       |
| -----------                 | -----------                       |
|counting_semaphore <span style="color:green;font-size:12px">(C++20)</span>                |semaphore that models a non-negative resource count <span style="color:green;font-size:12px">(class template)</span>|
|binary_semaphore <span style="color:green;font-size:12px">(C++20)</span>          |semaphore that has only two states <span style="color:green;font-size:12px">(class template)</span>|

**Member functions of both `std::counting_semaphore` and `std::binary_semaphore`**

| Members                       | Description                       |
| -----------                 | -----------                       |
|release     |increments the internal counter and unblocks acquirers <span style="color:green;font-size:12px">(public member function)</span>         |
|acquire |decrements the internal counter or blocks until it can <span style="color:green;font-size:12px">(public member function)</span>|
|try_acquire|tries to decrement the internal counter without blocking <span style="color:green;font-size:12px">(public member function)</span>         |
|try_acquire_for|tries to decrement the internal counter, blocking for up to a duration time <span style="color:green;font-size:12px">(public member function)</span>         |
|try_acquire_until   |tries to decrement the internal counter, blocking until a point in time <span style="color:green;font-size:12px">(public member function)</span>    |

| Constants                       | Description                       |
| -----------                 | -----------                       |
|max`[static]`     |returns the maximum possible value of the internal counter <span style="color:green;font-size:12px">(public static member function)</span>         |

Semaphores are an important part of multithreading that hasn't been covered yet. Semaphores are special variables used for signaling between threads and avoiding conflicts.

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
An example of how you could use a semaphore in C++20:

```cpp
#include <semaphore>
#include <thread>

std::counting_semaphore<10> semaphore(3);

void worker(int id) {
    semaphore.acquire();
    std::cout << "Thread " << id << " acquired the semaphore.\n";
    // Critical section code goes here
    semaphore.release();
}
```
In this code, `std::counting_semaphore<10> semaphore(3);` creates a semaphore with a maximum of 10 permits and initializes it with 3 available permits.

The first template parameter, 10, is the maximum value the semaphore can reach. This means that at most, 10 threads can acquire the semaphore simultaneously.
The second parameter, 3, is the initial value of the semaphore. This means that initially, 3 threads can acquire the semaphore without blocking.
When a thread calls `semaphore.acquire()`, it attempts to acquire a permit. If permits are available (i.e., the semaphore value is greater than 0), the thread acquires a permit and continues execution. If no permits are available (the semaphore value is 0), the thread blocks until another thread releases a permit by calling `semaphore.release()`.

```cpp
#include <semaphore>
#include <thread>
#include <iostream>

std::counting_semaphore<5> semaphore(2);

void worker(int id) {
    semaphore.acquire();
    std::cout << "Thread " << id << " acquired the semaphore.\n";
    // Critical section code goes here
    semaphore.release();
}

int main(int argc, char* argv[]) {
    std::thread t1(worker, 1);
    std::thread t2(worker, 2);
    std::thread t3(worker, 3);

    t1.join();
    t2.join();
    t3.join();

    return 0;
}
```

Example with `std::binary_semaphore`

```cpp
#include <semaphore>
#include <thread>
#include <iostream>

std::binary_semaphore semaphore(1);

void worker(int id) {
    semaphore.acquire();
    std::cout << "Thread " << id << " acquired the semaphore.\n";
    // Critical section code goes here
    semaphore.release();
}

int main(int argc, char* argv[]) {
    std::thread t1(worker, 1);
    std::thread t2(worker, 2);

    t1.join();
    t2.join();

    return 0;
}
```

### 8. Using the <condition_variable> header

|Classes |Description |
| --------    | -----------|
|condition_variable <span style="color:green;font-size:12px">(C++11)</span> |provides a condition variable associated with a `std::unique_lock` <span style="color:green;font-size:12px">(class)</span>|
|condition_variable_any <span style="color:green;font-size:12px">(C++11)</span> |provides a condition variable associated with any lock type <span style="color:green;font-size:12px">(class)</span>|
|cv_status <span style="color:green;font-size:12px">(C++11)</span> |lists the possible results of timed waits on condition variables <span style="color:green;font-size:12px">(enum)</span>|

|Waiting |Description |
| --------    | -----------|
|wait| Blocks the current thread until the condition variable is awakened <span style="color:green;font-size:12px">(public member function)</span>|
|wait_for| Blocks the current thread until the condition variable is awakened or after the specified timeout duration <span style="color:green;font-size:12px">(public member function)</span>|
|wait_until| Blocks the current thread until the condition variable is awakened or until specified time point has been reached <span style="color:green;font-size:12px">(public member function)</span>|

|Notification |Description |
| --------    | -----------|
|notify_one| Notifies one waiting thread <span style="color:green;font-size:12px">(public member function)</span>|
|notify_all| Notifies all waiting thread <span style="color:green;font-size:12px">(public member function)</span>|

- **Using the `std::condition_variable` class:** It's a synchronization tool that allows threads to wait until a condition is met before continuing their execution. This class is used in combination with a mutex and provides a way to notify threads waiting on the condition variable that the condition has been met, allowing them to continue their execution.

    The `std::condition_variable` class is often used in producer-consumer patterns, where a producer produces data and a consumer consumes it. When the producer produces data, it can notify the condition variable to wake up the consumer and let it continue its execution.

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
    Output:
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

    In this example, the producer produces data and places it into a concurrent queue `(data_queue)`. When the queue is full, the producer blocks and waits for space to become available. The consumer consumes data from the queue, and when the queue is empty, it blocks and waits for data to become available.

    The `std::condition_variable` is used to notify threads waiting on the condition variable that the condition has been met, allowing them to continue their execution. In this case, the producer notifies the condition variable when there is space available in the queue, and the consumer notifies the condition variable when there is data available in the queue.

    Remember that the `std::condition_variable` class should be used in combination with a mutex, as the thread that notifies the condition variable must unlock the mutex to allow the waiting thread to continue its execution. In this example, a std::unique_lock is used to lock and unlock the mutex.

### 9. Using the \<future> header

The classes and functions in the C++ standard library header \<future> provide an interface for concurrent programming and managing asynchronous tasks. In particular, they allow creating and manipulating future and promise objects, which are used to transfer data and results between different threads and execution contexts.

|Content      |Description |
| --------    | -----------|
|future <span style="color:green;font-size:12px">(C++11)</span>|Waits for a value that is set asynchronously <span style="color:green;font-size:12px">(class template)</span>|
|promise <span style="color:green;font-size:12px">(C++11)</span>|Stores a value for asynchronous retrieval <span style="color:green;font-size:12px">(class template)</span>|
|shared_future <span style="color:green;font-size:12px">(C++11)</span>|Waits for a value (possibly referenced by other futures) that is set asynchronously <span style="color:green;font-size:12px">(class template)</span>|
|packaged_task <span style="color:green;font-size:12px">(C++11)</span>|Packages a function to store its return value for asynchronous retrieval <span style="color:green;font-size:12px">(class template)</span>|
|async <span style="color:green;font-size:12px">(C++11)</span>|Runs a function asynchronously (potentially in a new thread) and returns a std::future that will hold the result <span style="color:green;font-size:12px">(function template)</span>|
|launch <span style="color:green;font-size:12px">(C++11)</span>|Specifies the launch policy for std::async <span style="color:green;font-size:12px">(enum)</span>|
|future_status <span style="color:green;font-size:12px">(C++11)</span>|Defines the possible states of a future object. <span style="color:green;font-size:12px">(enum)</span>|

|launch policy| 	Description|
| --------    | -----------|
|launch::async	|Asynchronous: Launches a new thread to execute the function asynchronously, separate from the main thread.
|launch::deferred	|Deferred:  No new thread is launched. The function is executed only when the future result is explicitly requested using wait() or get(). It runs on the same thread that calls wait() or get().
|Unspecified |Automatic: The implementation chooses the most appropriate launch policy.|



- **Using the `std::future` and `std::shared_future` classes:**

|Members      |Description |
| --------    | -----------|
|get|returns the result <span style="color:green;font-size:12px">(public member function)</span>|
|wait|waits for the result to become available <span style="color:green;font-size:12px">(public member function)</span>|
|share <span style="color:green;font-size:12px">(std::future)</span>|transfers the shared state from `*this` to a shared_future and returns it <span style="color:green;font-size:12px">(public member function)</span>|
|valid|checks if the future has a shared state <span style="color:green;font-size:12px">(public member function)</span>|

- **Using the `std::promise` class:**

|Members      |Description |
| --------    | -----------|
|get_future|returns a future associated with the promised result <span style="color:green;font-size:12px">(public member function)</span>|
|set_value|sets the result to specific value <span style="color:green;font-size:12px">(public member function)</span>|
|set_exception|sets the result to indicate an exception <span style="color:green;font-size:12px">(public member function)</span>|
|set_value_at_thread_exit|sets the result to specific value while delivering the notification only at thread exit <span style="color:green;font-size:12px">(public member function)</span>|
|set_exception_at_thread_exit|sets the result to indicate an exception while delivering the notification only at thread exit <span style="color:green;font-size:12px">(public member function)</span>|

- **Using the `std::packaged_task` class:**

|Members      |Description |
| --------    | -----------|
|get_future|returns a future associated with the promised result <span style="color:green;font-size:12px">(public member function)</span>|
|valid|checks if the task object has a valid function <span style="color:green;font-size:12px">(public member function)</span>|
|operator()|executes the function <span style="color:green;font-size:12px">(public member function)</span>|
|make_ready_at_thread_exit|executes the function ensuring that the result is ready only once the current thread exits <span style="color:green;font-size:12px">(public member function)</span>|

- **Examples**

Both the future and promise objects can be passed as arguments to a function that will run in the background.

Passing as an rvalue reference attribute:
```cpp
void function(std::promise && p2)
// assuming std::promise<int> p1; declared in main()
std::thread t1(function, std::move(p1)); // passing a reference to an rvalue
```
Passing as an lvalue reference attribute:
```cpp
void function(std::promise & p2)
// assuming std::promise<int> p1; declared in main()
std::thread t1(function, std::ref(p1)); // passing a reference to an lvalue
```
Example setting promise from the function:
```cpp
#include <iostream>
#include <thread>
#include <future>

void function(std::promise<std::string>&& p2 ){
    p2.set_value("Value set in the function\n");
}

int main(int argc, char const *argv[])
{
    // declare promise object
    std::promise<std::string> p1;
    // link the future with the promise
    std::future<std::string>  f1 = p1.get_future();
    // create a thread passing rvalue reference of p1
    std::thread t1(function, std::move(p1));

    std::string returnedValue = f1.get();
    t1.join(); // Wait for the thread to complete
    std::cout << "Print: " << returnedValue << std::endl;
    return 0;
}
```
Output:
```cpp
Print: Value set in the function
```
Similarly, it could have been resolved by changing:
```cpp
void funcion(std::promise<std::string>& p2 )

std::thread t1(funcion, std::ref(p1));
```

Example setting promise from another scope:

```cpp
#include <iostream>
#include <thread>
#include <future>

void function(std::future<std::string>&& f2 ){
    std::string returnedValue = f2.get();
    std::cout << "Print: " << returnedValue << std::endl;
}

int main(int argc, char const *argv[])
{
    // declare promise object
    std::promise<std::string> p1;
    // link the future with the promise
    std::future<std::string>  f1 = p1.get_future();
    // create a thread passing rvalue reference of f1
    std::thread t1(function, std::move(f1));
    
    p1.set_value("Value set in main\n");
    
    t1.join(); // Wait for the thread to complete
    
    return 0;
}
```
Output:
```cpp
Print: Value set in main
```

Similarly, it could have been resolved by changing:
```cpp
void funcion(std::future<std::string>& f2 )

std::thread t1(funcion, std::ref(f1));
```
Example using std::async() and future
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
Output:
```cpp
10
```

Example using multiple promise and future objects:
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
Output:
```cpp
thr[0] = 4
thr[1] = 5
thr[2] = 6
thr[3] = 7
thr[4] = 8
```

Example using std::async(), future, and promise:
```cpp
#include <iostream>
#include <future>

int function(std::future<int>& num) {
    int result = num.get() + 4;
    return result;
}

int main(int argc, char* argv[]) {
    std::promise<int> pro;
    std::future<int> fut = pro.get_future();
    
    // Using std::launch::async policy to create a new thread separate from main
    std::future<int> ret = std::async(std::launch::async, function, std::ref(fut));
    
    // Setting promise value to unblock num.get() and waiting for the return
    pro.set_value(6);
    
    // Main thread waits here for the result
    int res = ret.get();

    std::cout << res << std::endl;

    return 0;
}
```
Output:
```cpp
10
```
Example using shared_future:
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
Output:
```cpp
Child::Result1 : 20.5
Child::Result2 : 20.5
Parent::Result1 : 10
Parent::Result2 : 10
```
Example using packaged_task
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
Output:
```cpp
task_function:  256
task_lambda:    512
task_thread:    1024
task_bind:      2048
task_stdfun:    4096
task_functor:   8192
```

### 10. Communication Between Threads
- **Shared Variables**
    - **std::mutex:**  
        A mutex is an object that allows multiple threads of a program to access a single shared resource, but one at a time. Here's an example of how a mutex is used in C++:
        ```cpp
        std::mutex mtx;
        mtx.lock();
        // Access the shared variable
        mtx.unlock();    
        ```
        In this case, `mtx.lock()` acquires the mutex, and `mtx.unlock()` releases it.
    - **std::atomic:** 
        Atomic operations leverage processor support (compare and swap instructions) and do not use locks at all. Here's an example of how an atomic variable is used in C++:
        ```cpp
        std::atomic<int> atomicVar;
        atomicVar.store(1);
        // or
        atomicVar = 1;
        ```
        Here, atomicVar.store(1) or atomicVar = 1 are atomic operations.
    - **semaphore:** 
        A semaphore is typically an integer variable. Here's an example of how a semaphore is used in C++:
        ```cpp
        std::counting_semaphore<1> sema;
        sema.acquire();
        // Access the shared variable
        sema.release();
        ```
        In this example, sema.acquire() acquires the semaphore, and sema.release() releases it.

- **Concurrent Queues**
    - **std::mutex:** 
        A mutex is a synchronization primitive that can be used to protect shared data from simultaneous access by multiple threads. In a concurrent queue, you can use std::mutex to ensure that only one thread can modify the queue at a time. Here's an example of how a mutex could be used in an enqueue and dequeue operation:
        ```cpp
        std::queue<int> myQueue;
        std::mutex mtx;

        // Thread 1: Adding an element to the queue
        mtx.lock();
        myQueue.push(1);
        mtx.unlock();

        // Thread 2: Removing an element from the queue
        mtx.lock();
        if (!myQueue.empty()) {
            myQueue.pop();
        }
        mtx.unlock();
        ```
        In this example, `mtx.lock()` acquires the mutex before accessing the queue, and `mtx.unlock()` releases it afterward. This ensures that only one thread can modify the queue at a time, thus avoiding race conditions.
    - **std::atomic:** 
        In a concurrent queue, you can use `std::atomic` to implement a lock-free queue. This means that threads don't need to block and wait if the queue is empty or full. Instead, they can continue running and retry later. Here's an example of how `std::atomic` could be used in an enqueue operation:
        ```cpp
        std::atomic<int> tail;
        // ...
        tail.store(tail.load() + 1);
        ```
        In this example, `tail.store(tail.load() + 1)` atomically increments the queue index, meaning that this increment is an indivisible and safe operation in a multithreaded context.
    - **semaphore:** 
        Semaphores can be useful in a concurrent queue to control access to the queue. For example, you can use a semaphore to ensure that only a specific number of threads can access the queue at the same time. Here's an example of how a semaphore could be used in an enqueue operation:
        ```cpp
        std::counting_semaphore<1> sema;
        // ...
        sema.acquire();
        // Access the queue
        sema.release();
        ```
        In this example, `sema.acquire()` acquires the semaphore before accessing the queue, and sema.`release()` releases it afterward. This ensures that only one thread can modify the queue at a time, thus avoiding race conditions.
- **std::condition_variable:**
    It is used to synchronize the execution of multiple threads based on certain conditions. It allows a thread to wait until a condition is met before continuing its execution.
    Here's an example that illustrates how to use std::condition_variable to wait until a thread notifies that an event has occurred:
    ```cpp
    std::mutex mtx;
    std::condition_variable cv;
    bool event_occurred = false;

    // Thread 1: Waits for the event to occur
    {
        std::unique_lock<std::mutex> lck(mtx);
        cv.wait(lck, [&event_occurred]{ return event_occurred; });
        // Perform actions after the event occurs
    }

    // Thread 2: Notifies that the event has occurred
    {
        std::lock_guard<std::mutex> lck(mtx);
        event_occurred = true;
        cv.notify_one();
    }
    ```
    In this example, "Thread 1" waits for event_occurred to be true using `cv.wait()`. Meanwhile, "Thread 2" notifies `cv` when `event_occurred` is set to true, allowing "Thread 1" to continue its execution.
- **std::shared_mutex:**
    It provides a locking mechanism that allows simultaneous access by multiple threads for reading and single access for writing.
    Here's an example that illustrates how to use std::shared_mutex to enable concurrent read and write access to a shared data structure:
    ```cpp
    std::shared_mutex smtx;
    std::map<int, std::string> data;

    // Read Thread: Reads the data
    {
        std::shared_lock<std::shared_mutex> lck(smtx);
        auto it = data.find(42);
        if (it != data.end()) {
            std::cout << "Value found: " << it->second << std::endl;
        }
    }

    // Write Thread: Writes data
    {
        std::unique_lock<std::shared_mutex> lck(smtx);
        data[42] = "New value";
    }
    ```
    In this example, the "Read Thread" locks `smtx` to access the data in read-only mode, allowing other threads to also access for reading simultaneously. Meanwhile, the "Write Thread" locks `smtx` in exclusive write mode to modify the data, preventing other threads from reading or writing at the same time.
### 11. Advanced Synchronization
- **std::lock\:**
    It's a function included in the <mutex> header that safely acquires multiple mutexes without risking deadlock.
    Minimal Example:
    Suppose we have two mutexes mutex1 and mutex2. To safely acquire both mutexes, we can use std::lock as follows:
    ```cpp
    std::mutex mutex1, mutex2;
    std::lock(mutex1, mutex2);
    // Safe operations with mutex1 and mutex2
    ```
- **std::try_lock:**
    It's a function included in the <mutex> header that attempts to acquire multiple mutexes without blocking the thread if it can't acquire all mutexes immediately.
    Minimal Example:
    Suppose we have two mutexes mutex1 and mutex2. We can try to acquire both mutexes without blocking the thread using std::try_lock as follows:
    ```cpp
    std::mutex mutex1, mutex2;
    if (std::try_lock(mutex1, mutex2) == -1) {
        // Both mutexes could not be acquired immediately
    } else {
        // Safe operations with mutex1 and mutex2
    }
    ```
- **std::call_once:**
    It's a function included in the `<mutex>` header that ensures a function is called only once across multiple threads, even if the function is called from multiple threads simultaneously.
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
    Output: 
    ```cpp
    Initializing resource...
    Doing some work...
    Doing some work...
    Doing some work...
    ```

### 12. Design Patterns
- **Producer-Consumer:**
    It's an effective way to handle communication between processes or threads where a set of producers generate data and place it into a shared queue, and a set of consumers retrieve that data from the queue and process it. It's crucial to understand and apply this pattern correctly to develop robust and efficient concurrent systems.
    - **Using Mutex:**
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
                cv.notify_one(); // Notify consumers that data is available
                std::this_thread::sleep_for(std::chrono::milliseconds(100));
            }
        }

        void consumer() {
            for (int i = 0; i < 10; ++i) {
                int data;
                {
                    std::unique_lock<std::mutex> lck(mtx);
                    cv.wait(lck, [] { return !buffer.empty(); }); // Wait until data is available
                    data = buffer.front();
                    buffer.pop();
                }
                std::cout << "Consumed: " << data << std::endl;
                std::this_thread::sleep_for(std::chrono::milliseconds(200));
            }
        }

        int main(int argc, char* argv[]) {
            std::thread producerThread(producer);
            std::thread consumerThread(consumer);

            producerThread.join();
            consumerThread.join();

            return 0;
        }
        ```
    - **Using Atomic:**
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
                cv.notify_one(); // Notify consumers that data is available
                std::this_thread::sleep_for(std::chrono::milliseconds(100));
            }
        }

        void consumer() {
            for (int i = 0; i < 10; ++i) {
                int data;
                {
                    std::unique_lock<std::mutex> lck(mtx);
                    cv.wait(lck, [] { return dataAvailable.load(); }); // Wait until data is available
                    data = buffer.front();
                    buffer.pop();
                    dataAvailable.store(false);
                }
                std::cout << "Consumed: " << data << std::endl;
                std::this_thread::sleep_for(std::chrono::milliseconds(200));
            }
        }

        int main(int argc, char* argv[]) {
            std::thread producerThread(producer);
            std::thread consumerThread(consumer);

            producerThread.join();
            consumerThread.join();

            return 0;
        }
        ```
    - **Using Semaphore:**
        Unfortunately, the C++ standard library up to C++20 does not provide a native implementation of semaphores.
- **Readers-Writers:**
    - **Using Mutex:**
        ```cpp
        #include <iostream>
        #include <thread>
        #include <shared_mutex>

        std::shared_mutex mutex;
        // Declaration of the resource where both reading and writing occur

        void writer() { // exclusive write access
            std::unique_lock<std::shared_mutex> lock(mutex);
            // Safe writing to the resource exclusive to one thread at a time
        }

        void reader(std::string n) { // read-only access simultaneously
            std::shared_lock<std::shared_mutex> lock(mutex);
            // Safe reading from the resource, for multiple threads simultaneously
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
    - **Using Atomic:**
        ```cpp
        #include <iostream>
        #include <thread>
        #include <atomic>

        std::atomic<int> sharedResource(0); // Shared variable

        void writer() {
            // Write operations
            sharedResource.store(1, std::memory_order_relaxed);
        }

        void reader(std::string name) {
            // Read operations
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
    - **Using Semaphore:**
        Unfortunately, the C++ standard library up to C++20 does not provide a native implementation of semaphores.
- **Thread Pool:**
    A ThreadPool is a set of pre-initialized threads that are ready to process tasks concurrently. Instead of creating and destroying threads repeatedly, these threads are reused, reducing overhead and improving system efficiency. This facilitates the rapid processing of tasks, dynamically adapting to the workload and optimizing the use of system resources. Essentially, the ThreadPool simplifies thread management and concurrent task execution in computer programs.
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

    int main(int argc, char const *argv[]) {
        ThreadPool pool(4);
        for (int i = 0; i < 5; ++i) {
            pool.enqueue([i] {
                std::cout << "Task " << i << " executed by thread " << std::this_thread::get_id() << std::endl;
                std::this_thread::sleep_for(std::chrono::milliseconds(100));
            });
        }
        return 0;
    }
    ```
- **Future-Promise:**
- **Monitor:**
- **Dead Lock avoiding:**
    A deadlock is a situation in which a set of processes become blocked because each process is holding a resource and waiting for another resource acquired by some other process.

    Consider an example where two trains are coming towards each other on the same track, and there is only one track. Neither of the trains can move once they are face-to-face. A similar situation occurs in operating systems when there are two or more processes that hold some resources and wait for resources held by others. For example, in the following diagram, Thread 1 is holding Resource 1 and waiting for Resource 2 held by Thread 2, and Thread 2 is waiting for Resource 1.

    <p align="center">
        <img src="/deadlock.png" alt="image">
    </p>

    - **Solution using std::lock\:**
        The std::lock() function is part of the \<mutex> header.
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

        int main(int argc, char const* argv[]) {
            std::cout << "running process" << "\n";

            std::thread t1(Thread1, "Thread 1");
            std::thread t2(Thread2, "Thread 2");

            t1.join();
            t2.join();

            std::cout << "finished process" << "\n";
            return 0;
        }
        ```
        Output:
        ```cpp
        running process
        Resource 1 owned by Thread 1
        Resource 2 owned by Thread 1
        Resource 2 owned by Thread 2
        Resource 1 owned by Thread 2
        finished process
        ```
    - **Solution using std::atomic:**    
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
            while (!flag2.load(std::memory_order_acquire)); // Wait for flag2 to be true
            Resource1(str);
        }

        void Thread2(std::string str) {
            while (!flag1.load(std::memory_order_acquire)); // Wait for flag1 to be true
            Resource2(str);
        }

        void SetFlag1() {
            flag1.store(true, std::memory_order_release); // Set flag1 to true
        }

        void SetFlag2() {
            flag2.store(true, std::memory_order_release); // Set flag2 to true
        }

        int main(int argc, char* argv[]) {
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
    - **Solution using std::semaphore:**
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
            semaphore2.acquire(); // Wait for semaphore2 to be available
            Resource1(str);
            semaphore1.release(); // Release semaphore1
        }

        void Thread2(std::string str) {
            semaphore1.acquire(); // Wait for semaphore1 to be available
            Resource2(str);
            semaphore2.release(); // Release semaphore2
        }

        int main(int argc, char* argv[]) {
            std::cout << "running process" << "\n";

            std::thread t1(Thread1, "Thread 1");
            std::thread t2(Thread2, "Thread 2");

            semaphore1.release(); // Start the process by releasing semaphore1

            t1.join();
            t2.join();

            std::cout << "finished process" << "\n";
            return 0;
        }
        ```
- **Thread Safety:**
    Refers to a program's ability to function correctly when multiple threads are executing concurrently, accessing, and modifying the same shared resources. In a multi-threaded environment, if accesses to shared data are not properly handled, race conditions and other issues leading to unexpected and potentially incorrect results may occur.

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
### 13. Conclusions

- Multithreading in C++ is a powerful but complex tool that allows maximizing CPU resources and improving program efficiency.

- Proper synchronization between threads is crucial to avoid race conditions, deadlocks, and other common issues in multithreaded environments.

- There are various techniques and tools in C++ for achieving synchronization between threads, such as mutexes, atomic variables, semaphores, futures, and condition_variables.

- Each technique has its own advantages, disadvantages, and specific use cases, so it's important to understand when and how to apply each of them in different situations.

- **Strategy for Choosing between Mutex, Shared_mutex, Atomic, Semaphore, Future, and Condition_variable:**
    - **mutex:**
    Use mutex when you need to ensure that only one thread accesses a shared resource at a time.
    It is suitable for situations requiring mutual exclusion, such as modifying shared data structures.
    - **shared_Mutex:**
    Use shared_mutex when you need to allow multiple threads to access a shared resource for reading simultaneously, but only one thread can access for writing at any given time.
    It is suitable for situations with frequent read operations but less frequent write operations on a shared data structure.
    - **atomic:**
    Use atomic variables when you need to perform operations on a shared variable safely and without locking.
    It is suitable for simple operations such as incrementing or decrementing a shared counter.
    - **semaphore:**
    Use semaphores when you need to control concurrent access to a shared resource using a counter that can be safely incremented and decremented by multiple threads.
    It is suitable for situations where you need to limit the number of threads that can access a shared resource simultaneously.
    - **future:** 
    It is used to represent the result of a task that will be executed in one thread and can be retrieved in another thread in the future. It allows a thread to wait until the result is available without blocking the active thread. This is useful when asynchronous operation results are needed in a multithreaded context.

    - **Condition_variable:** 
    It is used to wait for a specific condition to be met before continuing thread execution. It allows a thread to wait until a condition is met, which usually involves another thread notifying when the condition is met. This is useful for synchronizing the execution of multiple threads based on certain conditions.