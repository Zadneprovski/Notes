
```cpp
#include "FreeRTOS.h"
#include "task.h"
#include <iostream>

class MyClass {
public:
    MyClass() {
        // Создаем задачу
        createTask();
    }

    void run() {
        // Код задачи
    }

private:
    static void taskWrapper(void* param) {
        // Приводим указатель к типу MyClass
        MyClass* instance = static_cast<MyClass*>(param);
        instance->run(); // Вызываем нестатическую функцию
    }

    void createTask() {
        TaskHandle_t xHandle = nullptr;

        // Создание задачи
        xTaskCreate(
            taskWrapper,                 // Функция-обертка
            "MyTask",                   // Имя задачи
            configMINIMAL_STACK_SIZE,   // Размер стека
            this,                        // Передаем указатель на текущий объект
            tskIDLE_PRIORITY,           // Приоритет задачи
            &xHandle                     // Хендл задачи
        );
    }
};
```