# Composbot Fijo 
## Sensores de Monoxido de Carbono y Metano con RPI5, ADS1115 y TCA9548A
Este repositorio documenta el proceso de conexión e implementación de un sistema de adquisición de datos utilizando una **Raspberry Pi 5 con Ubuntu 24.04**, un multiplexor I2C **TCA9548A**, un convertidor analogico digital **ADS1115**, dos sensores de CO₂ **MG811** y dos sensores de CH₄ **MQ4**

- [Conexion sensores MG811 y MQ4 al ADS1115](#Conexion sensores)
- [Instalacion librerias](#Instalacion librerias)
- [Conexion ADS1115 al TC9548A]()
- [Prueba de funcionamiento del TCA9548A]()
---
### Conexion sensores
#### TCA9548A
El **TCA9548A** es un extensor I2C de 8 canales, La función de un extensor I2C es conectar varios buses a un único bus. Podría entenderse como un tipo particular de multiplexor, pero especialmente diseñado para comunicación I2C. También pueden ser útiles para comunicar buses I2C con tensiones diferentes, sin necesidad de emplear un adaptador de nivel lógico. Así es posible comunicar buses a tensiones de 1.8V, 2.5V, 3.3V y 5V.

<img src = "img.png" alt="img" width="100"/>

#### ADS1115
El ADS1115 es un convertidor analógico–digital (ADC) de 16 bits. El chip se puede configurar como 4 canales de entrada de un solo extremo o dos canales diferenciales. Como beneficio adicional, incluso incluye un amplificador de ganancia programable, de hasta x16, para ayudar a aumentar señales simples/diferenciales más pequeñas a todo el rango

![img_1.png](img_1.png)

#### MG811
El módulo tiene un sensor MG-811 que es altamente sensible al CO2 Rango de medición 0～10000 ppm

![img_2.png](img_2.png)

#### MQ4
El MQ-4 es un sensor electrónico diseñado para detectar gases combustibles como el metano (CH₄) y el gas natural, con una sensibilidad que abarca concentraciones entre 300 y 10,000 partes por millón (ppm).

![img_3.png](img_3.png)
#### Diagrama de conexión 
En este diagrama se muestra las conexion entre cada uno de los sensores y modulo utilizados. 

![img_4.png](img_4.png)

>[!WARNING] 
>
>No conecte el TCA9548A a 5V ya que podria dañar el puerto I2C de la raspberry pi que funcionan a 3.3V

| Raspberry pi 5 | TCA9548A |  
|----------------|----------|
| PIN3/GPIO2/SDA | SDA      |
| PIN5/GPIO3/SCL | SCL      |
| PIN1/3.3V      | VCC      |
| PIN6/GND       | GND      |

El voltaje de alimentación para el módulo ADS1115 es de 5V de una fuente externa, estos debido a que los sensores MG811 y MQ4 requieren 5V, evitando sobrecargar los pines de 5V de la raspberry pi y dañarla. 

| TCA9548A | ADS1115 |
|----------|---------|
| SD0      | SDA     |
| SC0      | SCL     |
| 5V       | VCC     |
| GND      | GND     |

| ADS1115 | MG811 (1) | MG811 (2) | MQ4 (1) | MQ4 (2) |
|---------|-----------|-----------|---------|---------|
| A0      |           |           |         | A-MQ2   |
| A1      |           |           | A-MQ1   |         |
| A2      |           | A-MG2     |         |         |
| A3      | A-MG1     |           |         |         |
| 5V      | VCC       | VCC       | VCC     | VCC     |
| GND     | GND       | GND       | GND     | GND     |

> [!IMPORTANT]  
> Para que los datos leidos por los diferentes modulos puedan ser leidos por la raspberry pi debe existir un GND comun para todo el circuito. 
> 
Se decicio utilizar el módulo TCA9548A, debido a que permite municar con tesion de 1.8V a 5V en sus entradas I2C, esto debido a que los sensores MG811 y MQ4 trabajan con tensiones de 5V y sus salidas analogicas pueden llegar a los 4V o superior, si se conecta el ADS1115 a 3.3V para conectarlo directamente a la raspberry pi se podria dañar el modulo al ingresar señales analogicas que superen los 3.3V. 
Existen modulos que podrian evitarnos todo este trabajo de conseguir multiples modulo para adecuar la comunicacion. El modulo **[Gravity: I2C ADS1115 16-Bit ADC Module (Arduino & Raspberry Pi Compatible)](https://www.dfrobot.com/product-1730.html)** ya cuenta con señal de nivel digital I2C de 3,3 V, optimizada para Raspberry Pi

### Instalacion librerias 
En una terminal Ejecuta 
~~~~
sudo apt-get update & sudo apt-get full-upgrade -y
sudo apt install i2c-tools
~~~~
Esto actulizara paquetes, y el i2c-tools no servira para saber su el puerto i2c detecta algun dispositivo conectado. Para ello ejecuta el siguiente comando

~~~~
sudo i2cdetect -y 1
~~~~
Se mostrará una matriz, en donde se podra visualizar los dipositivos conectados al puerto I2C, en este caso el TCA9548A viene por defecto con el 0x70, entonces en el terminal debe poder visualizarse el numero 70 en la matriz indicando que el dispositvo esta conectado 

~~~~

~~~~

~~~~
sudo pip3 install adafruit-circuitpython-tca9548a
sudo pip3 install adafruit-circuitpython-ads1x15
~~~~
