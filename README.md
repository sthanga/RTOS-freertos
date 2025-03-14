# RTOS-freertos
#### FreeRTOS is a real-time operating system (RTOS) for embedded systems that provides task scheduling, synchronization, inter-task communication, and memory management. It is lightweight, open-source, and widely used in microcontrollers and embedded applications.

### FREE RTOS:
FreeRTOS allows multiple tasks (threads) to run concurrently. This helps:

Improve responsiveness of time-critical applications. 
Handle multiple real-time events efficiently.
Manage CPU resources better.
 
#### Example Use Case
Imagine a smart home device where:

Task 1 reads temperature from a sensor.
Task 2 sends data to a cloud server.
Task 3 controls a display.

With FreeRTOS, all these tasks run concurrently without blocking each other.

### Concepts in FreeRTOS
#### (a) Tasks
#### (b) Task Scheduling
#### (c) Inter-Task Communication
#### (d) Memory Management

## Key FreeRTOS API Functions
``` bash
### Function	Description
xTaskCreate()	               Creates a task
vTaskDelete()	               Deletes a task
vTaskDelay()	                Delays a task for a specified time
xQueueCreate()	              Creates a queue
xQueueSend()	                Sends data to a queue
xQueueReceive()	             Reads data from a queue
xSemaphoreCreateBinary()	    Creates a semaphore
xSemaphoreTake()	            Acquires a semaphore
xSemaphoreGive()	            Releases a semaphore
vPortMalloc()	               Allocates dynamic memory
vPortFree()	                 Frees allocated memory
```
### (a) Tasks
      Tasks are like independent programs (threads) running concurrently.
      Each task has its own stack and priority.
      FreeRTOS schedules tasks based on priority.
#### Creating task
```c
#include "FreeRTOS.h"
#include "task.h"
#include <stdio.h>

void Task1(void *pvParameters) {
    while (1) {
        printf("Task 1 Running\n");
        vTaskDelay(1000 / portTICK_PERIOD_MS);  // Delay for 1 second
    }
}

void Task2(void *pvParameters) {
    while (1) {
        printf("Task 2 Running\n");
        vTaskDelay(500 / portTICK_PERIOD_MS);  // Delay for 500 ms
    }
}

int main() {
    xTaskCreate(Task1, "Task1", 1000, NULL, 1, NULL);
    xTaskCreate(Task2, "Task2", 1000, NULL, 2, NULL);

    vTaskStartScheduler();  // Start FreeRTOS scheduler

    while (1);  // Should never reach here
}

output:
Task 1 Running
Task 2 Running
Task 2 Running
Task 1 Running
Task 2 Running
```
#### xTaskCreate → Creates tasks.
#### vTaskDelay → Puts task to sleep for a specified time.
#### vTaskStartScheduler → Starts the FreeRTOS scheduler.

### (b) Task Scheduling
        FreeRTOS uses preemptive scheduling based on task priorities.
        Higher priority tasks preempt lower ones.
        If multiple tasks have the same priority, they run in a round-robin manner.

#### Priority	    Task     Name	Status
#### High (2)	    Task2	     Running
#### Low (1)	     Task1	     Waiting
Task2 runs first since it has a higher priority.
Task1 runs only when Task2 is sleeping or blocked.
### (c) Inter-Task Communication
        Tasks communicate using:
        
        Queues → Pass messages between tasks.
        Semaphores → Synchronize access to shared resources.
        Mutexes → Prevent data corruption (mutual exclusion).
``` c
QueueHandle_t queue;

void SenderTask(void *pvParameters) {
    int value = 10;
    while (1) {
        xQueueSend(queue, &value, portMAX_DELAY);
        vTaskDelay(1000 / portTICK_PERIOD_MS);
    }
}

void ReceiverTask(void *pvParameters) {
    int receivedValue;
    while (1) {
        xQueueReceive(queue, &receivedValue, portMAX_DELAY);
        printf("Received: %d\n", receivedValue);
    }
}

int main() {
    queue = xQueueCreate(5, sizeof(int));

    xTaskCreate(SenderTask, "Sender", 1000, NULL, 1, NULL);
    xTaskCreate(ReceiverTask, "Receiver", 1000, NULL, 1, NULL);

    vTaskStartScheduler();
}
output :
Received: 10
Received: 10
...
```
The sender sends data to the queue.
The receiver reads data when available.

### (d) Memory Management
     FreeRTOS provides multiple heap management options to handle memory dynamically.
     
     Heap 1: Fixed-size memory allocation.
     Heap 2: Simple malloc/free.
     Heap 4: Best-fit memory allocation (recommended).
```c
void Task(void *pvParameters) {
    int *data = pvPortMalloc(sizeof(int));  // FreeRTOS equivalent of malloc
    if (data != NULL) {
        *data = 50;
        printf("Allocated Data: %d\n", *data);
        vPortFree(data);  // Free allocated memory
    }
    vTaskDelete(NULL);
}

```
### **Function Pointer in C**
A **function pointer** is a pointer that stores the address of a function. It allows calling functions dynamically, enabling flexibility and modularity in programming.

---

## **1. Why Use Function Pointers?**
Function pointers are useful when:
✅ **Dynamic Function Calls** → Select a function at runtime instead of compile time.  
✅ **Callback Mechanism** → Pass a function as an argument to another function (e.g., signal handling, sorting with custom comparison).  
✅ **Polymorphism in C** → Implement **object-oriented behavior** (like virtual functions in C++).  
✅ **Driver Development & HAL (Hardware Abstraction Layer)** → Use function pointers in **Linux kernel, device drivers, and embedded systems** to handle multiple hardware implementations.  

---

## **2. Declaring & Using Function Pointers**
### **Basic Function Pointer Example**
```c
#include <stdio.h>

// Function to be pointed
void greet() {
    printf("Hello, Function Pointer!\n");
}

int main() {
    void (*func_ptr)();  // Declare a function pointer
    func_ptr = greet;    // Assign address of function
    func_ptr();          // Call function using pointer
    return 0;
}
```
**Output:**
```
Hello, Function Pointer!
```

---

## **3. Function Pointer with Parameters & Return Value**
```c
#include <stdio.h>

int add(int a, int b) {
    return a + b;
}

int main() {
    int (*func_ptr)(int, int);  // Function pointer declaration
    func_ptr = add;             // Assign function address
    printf("Sum: %d\n", func_ptr(5, 10)); // Call function via pointer
    return 0;
}
```
**Output:**
```
Sum: 15
```

---

## **4. Function Pointer in Callbacks (Real-World Example)**
Function pointers are commonly used in **callbacks**.  

### **Example: Using Function Pointer in `qsort()`**
The C standard library function `qsort()` uses a function pointer to allow custom sorting.
```c
#include <stdio.h>
#include <stdlib.h>

int compare(const void *a, const void *b) {
    return (*(int*)a - *(int*)b); // Ascending order
}

int main() {
    int arr[] = { 5, 3, 7, 2, 8 };
    int n = sizeof(arr) / sizeof(arr[0]);

    qsort(arr, n, sizeof(int), compare); // qsort uses a function pointer

    printf("Sorted array: ");
    for (int i = 0; i < n; i++) {
        printf("%d ", arr[i]);
    }
    return 0;
}
```
**Output:**
```
Sorted array: 2 3 5 7 8
```

---

## **5. Function Pointers in Structures (Used in Device Drivers)**
Function pointers are often used in **structs** for abstraction.

### **Example: Using Function Pointers in Structs**
```c
#include <stdio.h>

typedef struct {
    void (*start)(void);
    void (*stop)(void);
} DeviceDriver;

void start_device() { printf("Device started\n"); }
void stop_device() { printf("Device stopped\n"); }

int main() {
    DeviceDriver driver = { start_device, stop_device };
    driver.start();
    driver.stop();
    return 0;
}
```
**Output:**
```
Device started
Device stopped
```

This is **widely used in Linux drivers** to define function interfaces.

---

## **6. Function Pointer Arrays**
Instead of multiple `if-else` or `switch` statements, **function pointer arrays** simplify function selection.

```c
#include <stdio.h>

void fun1() { printf("Function 1\n"); }
void fun2() { printf("Function 2\n"); }

int main() {
    void (*func_arr[])() = { fun1, fun2 }; // Array of function pointers
    func_arr[0](); // Call fun1
    func_arr[1](); // Call fun2
    return 0;
}
```
**Output:**
```
Function 1
Function 2
```

---

## **7. Function Pointers in Linux Kernel & Embedded Systems**
- **Device Drivers:** Function pointers are used to implement **hardware abstraction layers (HAL)**.
- **Interrupt Handlers:** Function pointers are registered for **ISR (Interrupt Service Routines)**.
- **Syscalls:** System calls in the Linux kernel use function pointers to **redirect requests dynamically**.

### **Example: Using Function Pointer in a Driver**
```c
struct file_operations {
    int (*open)(struct inode *, struct file *);
    int (*read)(struct file *, char *, size_t, loff_t *);
    int (*write)(struct file *, const char *, size_t, loff_t *);
};
```
This structure is used in **Linux kernel drivers** to handle different file operations dynamically.

---

## **8. When to Use Function Pointers?**
✅ **When you need a callback mechanism (e.g., `qsort()`, signal handling).**  
✅ **When you need dynamic function selection (e.g., device drivers, HAL, function tables).**  
✅ **When you want to replace `switch-case` with a more efficient method (function pointer arrays).**  
✅ **When implementing polymorphism in C (e.g., defining behavior in structs for OOP-like design).**  

Would you like an example specific to **FreeRTOS, Linux drivers, or embedded systems**? 🚀
