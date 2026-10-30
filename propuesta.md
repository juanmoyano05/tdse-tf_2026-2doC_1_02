# Sistema de Monitoreo Ambiental y Seguridad para Smart House

**Proyecto:** Sistema de Monitoreo Ambiental y Seguridad para Smart House  
**Materia:** Taller de Sistemas Embebidos  
**Integrantes:** Jonathan Ruben Babi-Garcia | 113704, Tamerlan Baskaev | 113165, Juan Manuel Moyano | 113613  
**Fecha:** 2do cuatrimestre 2026  

---

## 1. Selección del proyecto a implementar

### 1.1 Objetivo del proyecto y resultados esperados
El objetivo del proyecto es diseñar e implementar un prototipo embebido de bajo costo para el monitoreo ambiental y seguridad en el hogar (*Smart House*), utilizando el microcontrolador STM32 como unidad central de procesamiento. 

El sistema adquiere señales de sensores comerciales de fácil acceso local: presencia de Gas, Dióxido de Carbono ($\text{CO}_2$), Temperatura y Humedad ambiente. Dispone de dos modos de operación definidos por la arquitectura del firmware:
1. **Modo NORMAL:** Muestreo continuo de sensores, envío de datos a una **aplicación móvil propia** vía Bluetooth (módulo **HM-10**) y supervisión de umbrales para activación de alertas.
2. **Modo SET_UP:** Modo de configuración a través de la app donde se permite ajustar:
   - La sensibilidad del Buzzer y LED para alertar (umbrales de disparo). El LED va a brillar y apagarse con PWM.
   - La variable asignada al Buzzer y LED (selección de cuál parámetro detona la alarma auditiva: Gas, $\text{CO}_2$, Temperatura, Humedad).
3. **Modo FALLA**: Al detectarse algún problema en la etapa de medición, sea ausencia de cambios o valores muy elevados, el sistema notificará al usuario vía la app y medios físicos.

Los parámetros de configuración (`SET_UP`) se persisten en una **memoria EEPROM externa** conectada por bus **I2C / SPI**, garantizando la recuperación de datos ante pérdidas de alimentación. 

---

### 1.2 Proyectos similares
Los proyectos similares considerados son:
1. **Opción 1 (Base Estándar):** Monitoreo analógico básico de Gas y Temperatura con alertas por LEDs/Buzzer. Sin pantalla LCD, sin EEPROM externa y con configuración rígida.
2. **Opción 2 (Propuesta SmartHouse STM32):** Monitoreo de Gas, $\text{CO}_2$, Temp y Humedad con memoria EEPROM externa (I2C/SPI) para almacenamiento de `SET_UP`, modulación PWM para intensidad de LED, gestión de modos `NORMAL` / `SET_UP` / 'FALLA' y comunicación BLE (HM-10) con App móvil propia.
3. **Opción 3 (Compleja / Alto Costo):** Monitoreo ambiental con pantalla gráfica OLED, sensores industriales NDIR de $\text{CO}_2$, módulo Wi-Fi e integración a plataformas cloud comerciales.

Para la elección, se ponderan los criterios del 1 al 10 según la posibilidad de implementación:

#### Tabla 1.2.1: Comparación de alternativas de proyecto

| Criterio | Peso | Opción 1: Base Estándar (Puntuación \| Ponderado) | Opción 2: Propuesta SmartHouse STM32 (Puntuación \| Ponderado) | Opción 3: Compleja / Alto Costo (Puntuación \| Ponderado) |
| :--- | :---: | :---: | :---: | :---: |
| **Disponibilidad de HW** | 9 | 10 \| 90 | 10 \| 90 | 4 \| 36 |
| **Costo** | 8 | 9 \| 72 | 9 \| 72 | 3 \| 24 |
| **Dificultad Técnica** | 10 | 9 \| 90 | 8 \| 80 | 4 \| 40 |
| **Interés Personal** | 7 | 6 \| 42 | 9 \| 63 | 8 \| 56 |
| **Puntaje Total** | **-** | **294** | **305** | **156** |

---

### 1.3 Selección de proyecto
Considerando los puntajes asignados a las opciones, se seleccionó la **Opción 2 (Propuesta SmartHouse STM32)**. Utiliza periféricos de comunicación digital (I2C/SPI para EEPROM, UART para BLE, ADC para sensores analógicos, Timers para PWM), mantiene un presupuesto bajo y dificultad técnica accesible. 

---

### 1.3.1 Diagrama en bloques
<img width="1161" height="465" alt="tdsefinal_Export" src="https://github.com/user-attachments/assets/80c66b99-1259-4694-a4b5-085f9ed3b467" />

---

## 2. Elicitación de requisitos y casos de uso

En el mercado existen varios productos con la funcionalidad de detección de cambios ambientales, sea de temperatura, humedad, presencia de gases naturales, etc. La gran mayoría de estos productos económicos miden únicamente la temperatura y/o la humedad, con la excepción de estaciones meteorológicas con un costo muy elevado. Nuestro proyecto tiene como objetivo unificar la medición de distintos factores de la naturaleza a un costo accesible.

Con la llegada del siglo XXI se llevó a cabo una revolución digital donde todo se conecta al Internet vía IoT y, de a poco, todo se reimplementa usando sistemas embebidos. Es por esto que surgen productos "Smart". Nuestro producto se ubica en un punto medio que, por más que no utilizamos el Internet, incorporamos Bluetooth para la transmición de información del ambiente a un celular.

### 2.1 Requisitos del proyecto

| Grupo | ID | Descripción |
| :--- | :---: | :--- |
| **Sensores** | 1.1 | El sistema medirá la concentración de Gas mediante un sensor analógico. |
| | 1.2 | El sistema medirá la presencia de $\text{CO}_2$ mediante un sensor analógico. |
| | 1.3 | El sistema medirá Temperatura del ambiente mediante un sensor analógico. |
| | 1.4 | El sistema medirá Humedad del ambiente mediante un sensor analógico. |
| **Periféricos** | 2.1 | Un LED indicador debe prenderse cuando el sistema funciona correctamente (modo NORMAL). |
| | 2.2 | El sistema utilizará el bus **I2C o SPI** para comunicarse con una memoria **EEPROM externa**. |
| | 2.3 | Un LED indicador se debe prender y apagar via PWM cuando una variable configurable llegue a un umbral configurable. |
| | 2.4 | Un buzzer debe sonar cuando una variable configurable llegue a un umbral configurable. |
| | 2.5 | Módulo bluetooth para conectarse a una aplicación móvil en un dispositivo. |
| | 2.6 | Un LED indicador debe prenderse en caso de entrar al modo FALLA. |
| **Sistema** | 3.1 | El firmware implementará tres modos principales de funcionamiento: `NORMAL`, `SET_UP` y `FALLA`. |
| | 3.2 | En modo `SET_UP`, el usuario podrá ajustar la sensibilidad (umbral) del Buzzer y LED. |
| | 3.3 | En modo `SET_UP`, el usuario podrá seleccionar la variable específica que activa el Buzzer y LED (Gas, $\text{CO}_2$, Temp, Humedad). |
| | 3.4 | En modo `NORMAL`, el sistema estará funcionando con las configuraciones elegidas. |
| | 3.5 | En modo `NORMAL`, el sistema medirá todas las variables y transmitirá la información al usuario en un intervalo de tiempo si detecta un dispositivo conectado. |
| | 3.6 | En modo `NORMAL`, el sistema medirá todas las variables y observará si existe alguna falla en los sensores. En tal caso, entra en modo `FALLA`. |
| | 3.7 | En modo `FALLA`, el sistema enviará una notificación al usuario de qué sensor falló. |
| **Almacenamiento Persistente** | 4.1 | Toda la estructura `SET_UP` (umbrales de sensibilidad, variable de asignación de Buzzer) se guardará en la EEPROM externa. |
| | 4.2 | Al iniciar, el STM32 leerá el `SET_UP` desde la EEPROM. Si la memoria no está inicializada, cargará valores por defecto en RAM. |
| **Aplicación** | 5.1 | La aplicación debe poder detectar y conectarse al STM32 en la inicializacion. |
| | 5.2 | La aplicación debe tener texto que muestre la información recibida del STM32 |
| | 5.3 | La aplicación debe tener un botón que hará entrar el sistema en modo `SET_UP` y mostrar las opciones de configuración.  |

---

### 2.2 Casos de Uso

#### Tabla 2.2.1: Caso de uso 1: Monitoreo continuo y disparo de alarma (Modo NORMAL)

| Elemento | Definición |
| :--- | :--- |
| **Disparador** | El nivel medido de la variable asignada al Buzzer y LED (ej. Gas o $\text{CO}_2$) supera el umbral de sensibilidad configurado en el `SET_UP`. |
| **Precondiciones** | El STM32 ha cargado los datos del `SET_UP` desde la EEPROM externa y los sensores están activos. El STM32 está en modo NORMAL |
| **Flujo principal** | 1. El STM32 muestrea los sensores y actualiza los datos vía la app a través del módulo HM-10.<br>3. El firmware compara la lectura de la variable seleccionada en el `SET_UP` contra el umbral de sensibilidad asignado.<br>4. Al superar el umbral, se activa el Buzzer y el LED de alarma correspondiente.<br>5. El LED controlado por PWM conmuta a su patrón de advertencia. |
| **Flujos alternativos** | **a) La variable retorna a valores seguros:** El Buzzer se apaga automáticamente y vuelve a indicar estado normal.<br>**b) Se pierde la conectividad Bluetooth** La app notifica al usuario avisando que se desconectó del sistema.  |

---

#### Tabla 2.2.2: Caso de uso 2: Configuración del sistema (`SET_UP`) y grabado en EEPROM externa

| Elemento | Definición |
| :--- | :--- |
| **Disparador** | El usuario elige desde la app al modo `SET_UP`. |
| **Precondiciones** | El sistema está encendido y la EEPROM externa está operativa en el bus I2C/SPI. |
| **Flujo principal** | 1. La FSM del STM32 conmuta al estado `SET_UP`.<br>2. La app muestra el menú de configuración: "1. Variable de control | 2. Sensibilidad".<br>3. El usuario selecciona la variable a asociar al Buzzer y LED (ej. cambiar de "Gas" a "$\text{CO}_2$") y ajusta la sensibilidad.<br>4. Al presionar el Button "Guardar" desde la App, el STM32 escribe los nuevos datos en la memoria EEPROM externa.<br>5. La app confirma "Config Guardada" y emite un todo corto en el Buzzer. |
| **Flujos alternativos** | **a) Pérdida de alimentación o conexión Bluetooth durante el ajuste:** Al reiniciar, el STM32 recupera el último `SET_UP` guardado válidamente en la EEPROM externa.<br>**b) Valores fuera de rango recibidos por BLE:** El STM32 descarta el comando remoto. |

---

#### Tabla 2.2.3: Caso de uso 3: Falla en los sensores (modo FALLA)

| Elemento | Definición |
| :--- | :--- |
| **Disparador** | Algún sensor deja de funcionar correctamente. |
| **Precondiciones** | Está en modo NORMAL. |
| **Flujo principal** | 1. Se enciende un LED indicador de falla.<br>2. Se envía una notificación de falla al usuario vía la app.<br>3. El STM32 entra en modo `FALLA`.  |
| **Flujos alternativos** | **a) Desconexión Bluetooth:** El STM32 continúa en modo `FALLA`.<br>**b) Sensor vuelve a funcionar correctamente:** El STM32 sale de modo `FALLA` y vuelve al modo `NORMAL`. |
```
