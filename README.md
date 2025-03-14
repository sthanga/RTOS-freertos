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
xTaskCreate()	            Creates a task
vTaskDelete()	            Deletes a task
vTaskDelay()	            Delays a task for a specified time
xQueueCreate()	          Creates a queue
xQueueSend()	            Sends data to a queue
xQueueReceive()	          Reads data from a queue
xSemaphoreCreateBinary()	Creates a semaphore
xSemaphoreTake()	        Acquires a semaphore
xSemaphoreGive()	        Releases a semaphore
vPortMalloc()	            Allocates dynamic memory
vPortFree()	              Frees allocated memory
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
