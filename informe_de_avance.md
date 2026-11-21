 # **Sistema de Monitoreo Ambiental y Seguridad para Smart House**
 ## **Informe de Avances**

**Materia:** Taller de Sistemas Embebidos

**Autores:**

**Jonathan Ruben Babi-Garcia \- Padrón 113704**  
**Tamerlan Baskaev \- Padrón 113165**  
**Juan Manuel Moyano \- Padrón 113613**  

**Fecha: 20/11/2026**  
**2do cuatrimestre**

A continuación se detalla el informe de avances del trabajo final a partir de los requerimientos:

| Estado | Descripción      |
|-----|---------------------|
| 🟢 | Ya implementado |
| 🟡 | En proceso de implementarse |
| 🔴 | No se implementará |

#### **Sensores**  

| Req ID | Descripción | Estado | 
|--------|-------------|--------|  
| 1.1    |  El sistema medirá la concentración de Gas mediante un sensor analógico. | 🟡 |  
| 1.2    | El sistema medirá la presencia de $\text{CO}_2$ mediante un sensor analógico.| 🟡 |  
| 1.3    | El sistema medirá Temperatura del ambiente mediante un sensor analógico. | 🟡 | 
| 1.4    | El sistema medirá Humedad del ambiente mediante un sensor analógico. | 🟡 | 

#### **Periféricos**  

| Req ID | Descripción | Estado |  
|--------|-------------|--------|  
| 2.1 | Un LED indicador debe prenderse cuando el sistema funciona correctamente (modo NORMAL). | 🟡 |
| 2.2 | El sistema utilizará el bus **I2C o SPI** para comunicarse con una memoria **EEPROM externa**. | 🟡 |
| 2.3 | Un LED indicador se debe prender y apagar via PWM cuando una variable configurable llegue a un umbral configurable. | 🟡 |
| 2.4 | Un buzzer debe sonar cuando una variable configurable llegue a un umbral configurable. | 🟡 |
| 2.5 | Módulo bluetooth para conectarse a una aplicación móvil en un dispositivo. | 🟢 |
| 2.6 | Un LED indicador debe prenderse en caso de entrar al modo FALLA. |🟡 |

#### **Sistema**  

| Req ID | Descripción | Estado |  
|--------|-------------|--------|  
| 3.1 | El firmware implementará tres modos principales de funcionamiento: `NORMAL`, `SET_UP` y `FALLA`. | 🟡 |
| 3.2 | En modo `SET_UP`, el usuario podrá ajustar la sensibilidad (umbral) del Buzzer y LED. | 🟡 |
| 3.3 | En modo `SET_UP`, el usuario podrá seleccionar la variable específica que activa el Buzzer y LED (Gas, $\text{CO}_2$, Temp, Humedad). | 🟡 |
| 3.4 | En modo `NORMAL`, el sistema estará funcionando con las configuraciones elegidas. | 🟡 |
| 3.5 | En modo `NORMAL`, el sistema medirá todas las variables y transmitirá la información al usuario en un intervalo de tiempo si detecta un dispositivo conectado. | 🟡 |
| 3.6 | En modo `NORMAL`, el sistema medirá todas las variables y observará si existe alguna falla en los sensores. En tal caso, entra en modo `FALLA`. | 🟡 |
| 3.7 | En modo `FALLA`, el sistema enviará una notificación al usuario de qué sensor falló. | 🟡 |

#### **Almacenamiento Persistente**  

| Req ID | Descripción | Estado |  
|--------|-------------|--------|  
| 4.1 | Toda la estructura `SET_UP` (umbrales de sensibilidad, variable de asignación de Buzzer) se guardará en la EEPROM externa. | 🟡 |
| 4.2 | Al iniciar, el STM32 leerá el `SET_UP` desde la EEPROM. Si la memoria no está inicializada, cargará valores por defecto en RAM. | 🟡 |

#### **Aplicación**
| Req ID | Descripción | Estado |  
|--------|-------------|--------|  
| 5.1 | La aplicación debe poder detectar y conectarse al STM32 en la inicializacion. | 🟢 |
| 5.2 | La aplicación debe tener texto que muestre la información recibida del STM32 | 🟢 |
| 5.3 | La aplicación debe tener un botón que hará entrar el sistema en modo `SET_UP` y mostrar las opciones de configuración.  | 🟡 |
