# Taller de Sistemas Embebidos

```console
.
|-- app
|   |-- inc
|   |   |-- app.h
|   |   |-- board.h
|   |   |-- dwt.h
|   |   |-- logger.h
|   |   |-- systick.h
|   |   |-- task_actuator_attribute.h
|   |   |-- task_actuator.h
|   |   |-- task_actuator_interface.h
|   |   |-- task_sensor_attribute.h
|   |   |-- task_sensor.h
|   |   |-- task_system_attribute.h
|   |   |-- task_system.h
|   |   `-- task_system_interface.h
|   |-- src
|   |   |-- app.c
|   |   |-- logger.c
|   |   |-- systick.c
|   |   |-- task_actuator.c
|   |   |-- task_actuator_interface.c
|   |   |-- task_sensor.c
|   |   |-- task_system.c
|   |   `-- task_system_interface.c
|   |-- app.txt
|   |-- readme.txt
|   |-- task_actuator.png
|   |-- task_actuator.txt
|   |-- task_sensor.png
|   |-- task_sensor.txt
|   |-- task_system.png
|   `-- task_system.txt
|-- Core
|   |-- Inc
|   |   |-- main.h
|   |   |-- stm32f1xx_hal_conf.h
|   |   `-- stm32f1xx_it.h
|   |-- Src
|   |   |-- main.c
|   |   |-- stm32f1xx_hal_msp.c
|   |   |-- stm32f1xx_it.c
|   |   |-- syscalls.c
|   |   |-- sysmem.c
|   |   `-- system_stm32f1xx.c
|   `-- Startup
|       `-- startup_stm32f103rbtx.s
|-- Drivers
|   |-- CMSIS
|   |   |-- Device
|   |   |-- Include
|   |   `-- LICENSE.txt
|   `-- STM32F1xx_HAL_Driver
|       |-- Inc
|       |-- Src
|       `-- LICENSE.txt
|-- README.md
|-- STM32F103RBTX_FLASH.ld
`-- tdse-tp2_01-model_integration.ioc

15 directories, 44 files
```

## Archivo `Core/Startup/startup_stm32f103rbtx.s`

Esta escrito en arm assembly y se ejecuta al resetear el microcontrolador, el
propósito es inicializar los registros principales (como el stack pointer o el
program counter) la memoria y periféricos, para finalmente transferir el
control a la aplicación principal, la cual el punto de entrada es la función
`main()`, esto se puede ver en la linea 100 del archivo, en la cual se ejecuta
una instrucción `bl` (branch with link) y luego el símbolo `main`. El único tipo
de dato utilizado es `word` que representa una variable de 4 bytes (o 32 bits).

## Archivo `Core/Src/main.c`

El archivo `main.c` es un archivo escrito en C que contiene el punto de entrada
del programa principal, este punto de entrada es invocado al momento de
inicializar el microcontrolador, como se mencionó previamente. El patrón de
diseño de este archivo es procedural, ya que la ejecución del programa se lleva
a cabo mediante la invocación de diferentes funciones o subrutinas.

Los métodos que se encuentran en este archivo además de la (función principal
`main`) son: `initialise_monitor_handles`, `HAL_Init`, `SystemClock_Config`,
`MX_GPIO_Init`, `MX_USART2_UART_Init`, `app_init`, `app_update` y
`Error_Handler`.

La funcion o metodo `initialise_monitor_handles` se encarga de inicializar o
configurar como se manipula la entrada y salida, por ejemplo con `printf` al
momento de depurar el codigo, en particular se utiliza UART para comunicarse con
la computadora, es por esto que se inicializa tambien con `MX_USART2_UART_Init`.
La funcion `HAL_Init` iniciliza los estados internos de la libreria HAL
(Hardware Abstraction Layer). `SystemClock_Config` como se indica en el nombre
configura los relojes internos del microprocesador, como ser las fuentes y los
preescaladores. La funcion `Error_Handler` se ejecuta en caso de error pero no
tiene una funcionalidad definida. Por ultimo en la funcion `main` se
inicializa la aplicacion con `app_init` y en cada ciclo se actualiza el estado
de la aplicación con `app_update`.

## Archivo `Core/app/app.c`

En este archivo se definen las funciones `app_init`,`app_update` que se invocan
en la función `main`, y el callback `HAL_SYSTICK_Callback`, este ultimo es
una funcion definida de manera debil en la libreria HAL, y se inicializa al
invocar `HAL_Init` en la función `main`, este callback se ejecuta cada 1 ms, y
al estar definido de manera debil se puede sobreescribir, que es lo que se hace
en el archivo. Se puede ver que el callback se sobreescribe para que en cada
milisegundo incremente el contador global de la aplicacion y el interno de cada
tarea.
