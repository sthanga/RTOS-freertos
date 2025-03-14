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



Here are some commonly asked **FreeRTOS interview questions**, categorized by difficulty level:

---

## **Basic FreeRTOS Interview Questions**
### **1. What is FreeRTOS, and why is it used?**
**Answer:**  
FreeRTOS is an open-source, real-time operating system (RTOS) designed for embedded systems. It provides task scheduling, inter-task communication, and resource management, ensuring **deterministic behavior** for time-sensitive applications.

---

### **2. What are the key components of FreeRTOS?**
**Answer:**  
- **Task Scheduler** – Manages task execution based on priority.  
- **Tasks** – Independent units of execution.  
- **Queues** – Used for inter-task communication.  
- **Semaphores & Mutexes** – Synchronization mechanisms.  
- **Timers** – Software-based timing events.  
- **Interrupt Service Routines (ISR)** – Handles external events.  

---

### **3. How does FreeRTOS schedule tasks?**
**Answer:**  
FreeRTOS uses **preemptive, cooperative, and time-slicing scheduling** based on task priorities. The highest-priority **ready state** task is always executed first.

---

### **4. What are the different task states in FreeRTOS?**
**Answer:**  
1. **Running** – Task currently executing.  
2. **Ready** – Task ready to execute but waiting for CPU.  
3. **Blocked** – Task waiting for an event (e.g., semaphore, queue).  
4. **Suspended** – Task not in scheduling queue, waiting to be resumed.  
5. **Deleted** – Task has been removed from the system.  

---

## **Intermediate FreeRTOS Interview Questions**
### **5. How do you create a task in FreeRTOS?**
```c
xTaskCreate(vTaskFunction, "TaskName", 1000, NULL, 1, NULL);
```
Where:  
- `vTaskFunction` – Function executed by the task.  
- `"TaskName"` – Name of the task.  
- `1000` – Stack size in words.  
- `NULL` – Task parameters.  
- `1` – Priority.  
- `NULL` – Task handle (optional).  

---

### **6. How do you delete a task in FreeRTOS?**
```c
vTaskDelete(NULL); // Deletes the currently running task
```

---

### **7. What is a task handle, and why is it needed?**
**Answer:**  
A **task handle** (`TaskHandle_t`) is used to manage a task (suspend, resume, delete). Example:
```c
TaskHandle_t myTask;
xTaskCreate(vTaskFunction, "TaskName", 1000, NULL, 1, &myTask);
vTaskDelete(myTask); // Deletes a specific task
```

---

### **8. How do you handle task synchronization in FreeRTOS?**
**Answer:**  
- **Semaphores** (Binary, Counting) – Used to signal task execution.  
- **Mutexes** – Used for mutual exclusion (prevent race conditions).  
- **Event Groups** – Used for multiple event synchronization.  

Example of Binary Semaphore:
```c
SemaphoreHandle_t xSemaphore;
xSemaphore = xSemaphoreCreateBinary();
xSemaphoreGive(xSemaphore); // Unlock
xSemaphoreTake(xSemaphore, portMAX_DELAY); // Lock
```

---

### **9. How do you implement inter-task communication?**
**Answer:**  
Using **Queues**:
```c
QueueHandle_t xQueue = xQueueCreate(5, sizeof(int)); 
int data = 10;
xQueueSend(xQueue, &data, portMAX_DELAY); // Send data
xQueueReceive(xQueue, &data, portMAX_DELAY); // Receive data
```

---

## **Advanced FreeRTOS Interview Questions**
### **10. What is the difference between a binary semaphore and a mutex?**
| Feature | Binary Semaphore | Mutex |
|---------|----------------|------|
| Purpose | Synchronization (signal between tasks) | Mutual exclusion (resource locking) |
| Ownership | No ownership | Owned by the task that locks it |
| Recursive | No | Yes |
| Priority Inversion Handling | No | Yes (Priority Inheritance) |

---

### **11. How does FreeRTOS handle priority inversion?**
**Answer:**  
- Uses **priority inheritance** in **mutexes** to temporarily boost a lower-priority task’s priority if a higher-priority task is waiting on it.

---

### **12. What happens if a task runs indefinitely in FreeRTOS?**
**Answer:**  
If a task does **not yield** (`vTaskDelay()`, `vTaskDelayUntil()`, `xQueueReceive()` with timeout), **lower-priority tasks may starve**, blocking the RTOS scheduler.

---

### **13. How does FreeRTOS handle memory allocation?**
**Answer:**  
FreeRTOS provides three **memory management strategies**:
- **heap_1.c** – Fixed-size allocation, no `free()`.
- **heap_2.c** – Supports `malloc()` and `free()`.
- **heap_3.c** – Uses `malloc()` and `free()` from standard C library.
- **heap_4.c** – Best-fit memory allocation.

Example:
```c
void *ptr = pvPortMalloc(100);  // Allocate 100 bytes
vPortFree(ptr);  // Free allocated memory
```

---

### **14. How does FreeRTOS handle software timers?**
**Answer:**  
Software timers allow delayed execution of functions.
```c
TimerHandle_t xTimer = xTimerCreate("Timer", pdMS_TO_TICKS(1000), pdTRUE, NULL, vTimerCallback);
xTimerStart(xTimer, 0);
```

---

### **15. How can you optimize power consumption in FreeRTOS?**
**Answer:**  
- **Use tickless idle mode (`configUSE_TICKLESS_IDLE`)** to stop the system tick in idle state.  
- **Suspend unnecessary tasks** to reduce CPU usage.  
- **Use event-driven programming** instead of polling loops.  

---

## **16. How does FreeRTOS support multi-core processors (SMP)?**
**Answer:**  
- FreeRTOS **Symmetric Multi-Processing (SMP)** allows tasks to run on multiple cores.
- Tasks can be **pinned** to specific cores.

Example:
```c
xTaskCreatePinnedToCore(vTaskFunction, "Task", 1000, NULL, 1, NULL, 1);
```

---

## **Real-World Scenario Questions**
### **17. What will happen if a FreeRTOS task calls `vTaskDelete(NULL);`?**
**Answer:**  
The current task will be deleted, but its stack memory will **not** be freed unless dynamically allocated.

---

### **18. What is a race condition, and how do you prevent it in FreeRTOS?**
**Answer:**  
A **race condition** occurs when multiple tasks access a shared resource without proper synchronization.  
**Solution:** Use **mutexes, semaphores, or critical sections**:
```c
taskENTER_CRITICAL();
shared_resource++;
taskEXIT_CRITICAL();
```

---

### **19. What is configTICK_RATE_HZ, and how does it affect FreeRTOS?**
**Answer:**  
Defines **the system tick frequency** (e.g., `1000 Hz = 1ms tick`).  
- **Higher values** → More precise delays, but higher CPU overhead.  
- **Lower values** → Less CPU overhead, but reduced timer precision.  

---

### **20. How do you debug FreeRTOS applications?**
**Answer:**  
- **Use `uxTaskGetSystemState()`** to inspect task states.  
- **Enable stack overflow detection** (`configCHECK_FOR_STACK_OVERFLOW`).  
- **Use trace tools** like FreeRTOS+Trace.

---

## **Conclusion**
Mastering **task scheduling, inter-task communication, memory management, and debugging** is key for FreeRTOS-based development.

Here’s a **FreeRTOS-based embedded application** that reads temperature data from an **I2C temperature sensor**, controls an **LED status indicator**, and handles **a button press** for interface interaction.  

### **Features:**
✅ Reads temperature from an I2C sensor (e.g., **LM75, TMP102**).  
✅ Blinks an **LED** based on temperature threshold.  
✅ Handles a **button press** (debounced) to toggle between **Celsius & Fahrenheit**.  
✅ Uses **FreeRTOS tasks, queues, and semaphores** for efficient multitasking.  

---

### **🛠 FreeRTOS Concepts Used:**
1. **Task Scheduling** – Separate tasks for temperature reading, LED control, and button handling.  
2. **Queues** – Inter-task communication for passing temperature values.  
3. **Semaphores** – Used for button press event handling.  
4. **I2C Interface** – Reads temperature sensor data.  

---

### **📌 FreeRTOS Application Code**
```c
#include "FreeRTOS.h"
#include "task.h"
#include "queue.h"
#include "semphr.h"
#include "stdio.h"
#include "i2c_driver.h"   // Assume you have an I2C driver
#include "gpio_driver.h"  // Assume you have GPIO functions

// Constants
#define TEMP_SENSOR_ADDR  0x48  // I2C Address (TMP102 or LM75)
#define TEMP_THRESHOLD    30.0  // Temperature limit for LED alert

// Global Variables
QueueHandle_t tempQueue;
SemaphoreHandle_t buttonSemaphore;
volatile uint8_t displayFahrenheit = 0;  // Toggle between °C and °F

// Function to Read Temperature (I2C)
float readTemperature() {
    uint8_t tempData[2];
    i2c_read(TEMP_SENSOR_ADDR, tempData, 2);
    int16_t rawTemp = (tempData[0] << 8) | tempData[1];
    float temperature = rawTemp / 256.0;  // Convert to Celsius
    return displayFahrenheit ? (temperature * 9/5) + 32 : temperature;
}

// Temperature Task
void vTemperatureTask(void *pvParameters) {
    float temperature;
    while (1) {
        temperature = readTemperature();
        xQueueSend(tempQueue, &temperature, portMAX_DELAY);
        vTaskDelay(pdMS_TO_TICKS(1000));  // Read every second
    }
}

// LED Control Task
void vLedTask(void *pvParameters) {
    float temperature;
    while (1) {
        if (xQueueReceive(tempQueue, &temperature, portMAX_DELAY)) {
            if (temperature >= TEMP_THRESHOLD) {
                gpio_set(LED_PIN, HIGH);
            } else {
                gpio_set(LED_PIN, LOW);
            }
        }
    }
}

// Button Interrupt Handler
void vButtonISR() {
    BaseType_t xHigherPriorityTaskWoken = pdFALSE;
    xSemaphoreGiveFromISR(buttonSemaphore, &xHigherPriorityTaskWoken);
    portYIELD_FROM_ISR(xHigherPriorityTaskWoken);
}

// Button Task
void vButtonTask(void *pvParameters) {
    while (1) {
        if (xSemaphoreTake(buttonSemaphore, portMAX_DELAY)) {
            displayFahrenheit = !displayFahrenheit;
            printf("Display Mode: %s\n", displayFahrenheit ? "Fahrenheit" : "Celsius");
        }
    }
}

// Main Function
int main(void) {
    // Initialize Peripherals
    i2c_init();
    gpio_init();
    gpio_set_interrupt(BUTTON_PIN, vButtonISR);

    // Create Queue & Semaphore
    tempQueue = xQueueCreate(5, sizeof(float));
    buttonSemaphore = xSemaphoreCreateBinary();

    // Create Tasks
    xTaskCreate(vTemperatureTask, "TempTask", 1000, NULL, 2, NULL);
    xTaskCreate(vLedTask, "LedTask", 1000, NULL, 2, NULL);
    xTaskCreate(vButtonTask, "ButtonTask", 500, NULL, 1, NULL);

    // Start Scheduler
    vTaskStartScheduler();

    while (1);  // Should never reach here
}
```

---

### **📌 Explanation:**
1. **`vTemperatureTask`**  
   - Reads temperature over **I2C** every second.  
   - Sends data to the **Queue** for processing by the LED task.  

2. **`vLedTask`**  
   - Waits for temperature data from **Queue**.  
   - Turns **LED ON** if temp **≥ 30°C**, otherwise **OFF**.  

3. **`vButtonTask`**  
   - Waits for **button press (Semaphore)** event.  
   - Toggles between **Celsius & Fahrenheit** display.  

4. **`vButtonISR`**  
   - Interrupt handler for **button press** (uses **semaphore**).  

---

### **🔹 Key FreeRTOS Features Demonstrated**
✔ **Task Scheduling** – Three independent tasks run in parallel.  
✔ **Queue Usage** – Transfers temperature data between tasks.  
✔ **Semaphore Usage** – Handles button press event efficiently.  
✔ **I2C Communication** – Reads temperature sensor values.  
✔ **GPIO Control** – Manages LED based on temperature.  
✔ **ISR Handling** – Efficient button interrupt processing.  

---

### **🛠 Modifications You Can Try**
🔹 Add **OLED/LCD Display** to show temperature.  
🔹 Implement **Low Power Mode** using **Tickless Idle**.  
🔹 Add **Logging via UART** for debugging.  
🔹 Use **Multiple Sensors** (Humidity, Pressure).  
