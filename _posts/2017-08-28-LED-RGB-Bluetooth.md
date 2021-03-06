---
layout: post
title: Lampara RGB controlada desde aplicación Andoid
published: true
---

El objetivo de este post es implementar una lampara RGB controlada desde una aplicación Android mediante Bluetooth a la vez que se repasan conceptos importantes para lograr una implementación exitosa y bien entendida.

- [Consideraciones a tener en cuenta a la hora de seleccionar un LED - 1](#consideraciones-a-tener-en-cuenta-a-la-hora-de-seleccionar-un-led---1)
  * [El brillo](#el-brillo)
  * [Tipo de brillo](#tipo-de-brillo)
  * [El color](#el-color)
  * [Package dimensions](#package-dimensions)
  * [Angulo de vision de la luz](#angulo-de-vision-de-la-luz)
  * [Forward voltage](#forward-voltage)
- [Posibles usos de los LED en aplicaciones interactivas - 2](#posibles-usos-de-los-led-en-aplicaciones-interactivas---2)
- [Circuito de acondicionamiento de un LED - 3](#circuito-de-acondicionamiento-de-un-led---3)
  * [Riesgos de conectar un LED directamente a un microcontrolador sin un circuito de acondicionamiento - 4](#riesgos-de-conectar-un-led-directamente-a-un-microcontrolador-sin-un-circuito-de-acondicionamiento---4)
- [Variando el brillo de un LED utilizando una resistencia variable - 5](#variando-el-brillo-de-un-led-utilizando-una-resistencia-variable---5)
- [Variando el brillo de un LED utilizando una señal PWM - 6](#variando-el-brillo-de-un-led-utilizando-una-se-al-pwm---6)
- [Generando distintos colores utilizando señales de PWM - 7](#generando-distintos-colores-utilizando-se-ales-de-pwm---7)
- [Implicacion de los diodos RGB de anodo comun y catodo comun en el programa del microcontrolador - 8](#implicacion-de-los-diodos-rgb-de-anodo-comun-y-catodo-comun-en-el-programa-del-microcontrolador---8)
- [El duty cycle - 9](#el-duty-cycle---9)
- [Calculando el periodo y la frecuencia de una señal de PWM en el Arduino - 10](#calculando-el-periodo-y-la-frecuencia-de-una-se-al-de-pwm-en-el-arduino---10)
- [Implicaciones de frecuencias altas o bajas en la señal PWM que controla un LED - 11](#implicaciones-de-frecuencias-altas-o-bajas-en-la-se-al-pwm-que-controla-un-led---11)
- [Diferencia entre una interfaz paralela y una interfaz serial - 12](#diferencia-entre-una-interfaz-paralela-y-una-interfaz-serial---12)
- [Diferencia entre una comunicación serial sincrona y una asincrona - 13](#diferencia-entre-una-comunicaci-n-serial-sincrona-y-una-asincrona---13)
- [Niveles logicos del microcontrolador ATmega328P - 14](#niveles-logicos-del-microcontrolador-atmega328p---14)
- [Consideraciones a tener en cuenta al conectar por medio de una interfaz serial un microcontrolador que opera a 5v con un sensor o actuador que opera a 3v o 3.3v - 15](#consideraciones-a-tener-en-cuenta-al-conectar-por-medio-de-una-interfaz-serial-un-microcontrolador-que-opera-a-5v-con-un-sensor-o-actuador-que-opera-a-3v-o-33v---15)
- [Bits de sincronizacion, datos y paridad en una comincacion serial - 16](#bits-de-sincronizacion--datos-y-paridad-en-una-comincacion-serial---16)
- [El baud rate - 17](#el-baud-rate---17)
- [El concepto de endian en comunicaciones seriales - 18](#el-concepto-de-endian-en-comunicaciones-seriales---18)
- [Enviando caracteres ASCII por el serial del arudino - 19](#enviando-caracteres-ascii-por-el-serial-del-arudino---19)
- [Conexion para comunicación serial entre dos Arduino UNO - 20](#conexion-para-comunicaci-n-serial-entre-dos-arduino-uno---20)
- [Como se conecta un Arduino UNO al PC - 21](#como-se-conecta-un-arduino-uno-al-pc---21)
- [Diferencias entre comunicaciónes seriales TTL y comunicaciones seriales RS232 - 22](#diferencias-entre-comunicaci-nes-seriales-ttl-y-comunicaciones-seriales-rs232---22)
- [La UART - 23](#la-uart---23)
- [Errores comunes al conectar dispositivos por el puerto serial - 24](#errores-comunes-al-conectar-dispositivos-por-el-puerto-serial---24)
- [Sensores utilizados en el proyecto 2 del texto guia - Making Things Talk - 25](#sensores-utilizados-en-el-proyecto-2-del-texto-guia---making-things-talk---25)
- [Funcionamiento de los sensores mencionados - 26](#funcionamiento-de-los-sensores-mencionados---26)
- [Algunos usos de los sensores mencionados - 27](#algunos-usos-de-los-sensores-mencionados---27)
- [Diferencia entre el metodo println y el write del objeto Serial de Arduino - 28](#diferencia-entre-el-metodo-println-y-el-write-del-objeto-serial-de-arduino---28)
- [Protocolo de comunicacion usado en el proyecto 2 del texto guia - 29](#protocolo-de-comunicacion-usado-en-el-proyecto-2-del-texto-guia---29)
- [Implementando una Lampara RGB a control remoto](#implementando-una-lampara-rgb-a-control-remoto)
  * [Circuito](#circuito)
  * [Protocolos de comunicacion](#protocolos-de-comunicacion)
    + [Comando RGB](#comando-rgb)
    + [Comando C](#comando-c)
    + [Comando Cresponse](#comando-cresponse)
  * [Software](#software)
    + [Software para el arduino](#software-para-el-arduino)
    + [Software para la aplicación](#software-para-la-aplicaci-n)
- [Referencias](#referencias)

<small><i><a href='http://ecotrust-canada.github.io/markdown-toc/'>Table of contents generated with markdown-toc</a></i></small>


# Consideraciones a tener en cuenta a la hora de seleccionar un LED - 1
A primera vista puede parecer que un LED es el dispositivo mas facíl de comprar, solo es cuestion de ir a la tienda y pedir un LED, el vendedor escojera un LED entre los miles de LEDs "iguales" que tiene en la bodega y nos lo entregara.

Pero esto no es correcto, un LED puede brillar mas que otro, su package puede ser mas grande o pequeño en incluso el angulo de vision de la luz que emite puede variar, a continuación se explicaran con un poco mas de detalle algunos factores a tener en cuenta en un LED para seleccionarlo.

NOTA: Todas las propiedades de un LED especifico estan detalladas en su respectivo *datasheet*

## El brillo
¿Que tan brillante es un LED? esto generalmente esta en el *datasheet* y se expresa en candelas (cd), la cual es en terminos generales una unidad para medir la intensidad luminica de una fuente de luz.
![](http://imgur.com/0Ylb6mV.gif)

Por ejemplo, con el LED que estamos usando de ejemplo, se ve en su datasheet, que a 20mA tiene una intensidad luminica de 250 mcd (mili candelas), aunque debido a que nada es perfecto en el mundo real, el fabricante nos advierte que es posible que el LED que le compremos tenga una intensidad luminica de entre 180 cd a 250 cd.

## Tipo de brillo
Puede ser *diffuse* o *clear*, si tenemos un Diffused LED tendremos un LED que emite luz de una forma suave, uniforme y se puede ver desde casi cualquier angulo estos son generalmente buenos para labores de indicacion, en cambio con un Clear LED la luz es directa y muy fuerte, por lo tanto su luz solo se ve desde unos angulos muy especificos, en Colombia tambien son llamados *"LED de chorro"* porque se asemejan a una mangera a la que le tapamos casi todo el orificio de salida, haciendo que el agua salga con mucha presion y muy direccionada, estos LEDs son buenos para labores de iluminación.

Si hacemos una analogia con el agua los Diffused LEDs son como aspersores (de agua) mientras que los clear LED se asemejan mas a un limpiador (de agua) a alta presion.

## El color
Este factor es obvio y es quizas uno de los que intuitivamente tenemos en cuenta a la hora de seleccionar LEDs en la tienda de electronicos, lo unico complicado es que en el datasheet de un LED el color esta dado en una <a href="http://lsrtools.1apps.com/wavetorgb/index.asp?wavelength=640" target="_blank">longitud de onda</a> electromagnetica, por ejemplo la del LED que estamos analizando es de 640 la cual nuestro ojo percibe como un color rojo.

![](http://imgur.com/SWFC4nH.gif)


## Package dimensions
![](http://imgur.com/m3pfTqq.gif)

NOTA: Unidades estan en milimetros, en parentecis esta la misma unidad pero en pulgadas.

Este se refiere al tamaño de la capsula que protege el interior del LED, generalmente hay 3 tamaños muy usados, el de 3mm, el de 5mm y el de 10mm, generalmente el de 10mm es un diodo de 5mm con una capsula mas grande, por tanto se puede llegar a ser incluso menos brillante que un led de 5mm, esto deja en evidencia que no necesariamente es cierto que a mas grande la capsulta protectora del LED mas brillo obtendremos.


## Angulo de vision de la luz
![](http://imgur.com/om9jSid.gif)

No todos los LEDs se ven igual de brillantes desde todos los angulos, esto es especialmente notorio en los **clear LED** que solo se ven super brillantes si los miramos desde unos puntos, y se ven mas opacos si los vemos desde otros puntos, es decir el brillo percibido depende del angulo de vision desde el que se ve el LED.

## Forward voltage
Este se refiere a la caida de voltaje que produce el LED, en la datasheet que estamos viendo cuando la corriente es de 20 mA.

# Posibles usos de los LED en aplicaciones interactivas - 2
Existen muchos (infinitos), pero mencionare solo algunos:
1. Como ojos en un mecatronico.
2. Como indicador de fuerza en un juego de fuerza (poniendo muchos leds en linea y prendiendo solo una cantidad relativa a la fuerza del golpe)
3. Para indicar que un boton fue precioniado.

# Circuito de acondicionamiento de un LED - 3
Es un circuito basico que consiste en conectar en serie una resistencia a un LED para garantizar una corriente (I) maxima en el LED.

Pero como podemos calcular una resistencia que permita que nuestro LED brille lo suficiente.

![](http://imgur.com/rbaRV5e.gif)

Mirando el datasheet del LED que estamos analizando vemos que soporta hasta 30mA y en esa corriente su voltaje es de 1.9v aproximadamente, asi podemos proceder a calcular una resistencia que permita el maximo paso de corriente posible a travez del LED suponiendo que alimentamos el circuito con una fuente de 5v.

![](https://latex.codecogs.com/gif.latex?R=\frac{V_r}{I}=\frac{3.1v}{0.03A}\approx104\Omega)

## Riesgos de conectar un LED directamente a un microcontrolador sin un circuito de acondicionamiento - 4
La resistencia en un circuito de acondicionamiento de un LED nos garantiza una corriente maxima que no queme el le LED.

# Variando el brillo de un LED utilizando una resistencia variable - 5
Mediante un circuito simple, como por ejemplo conectar en serie al (catodo o anodo) LED una resistencia variable, y luego conectandolos a los pines +5v y GND obtendriamos un brillo en el LED que depende de la resistencia variable.

# Variando el brillo de un LED utilizando una señal PWM - 6
Para variar el brillo de un LED usando señales PWM, el truco consiste en cambiar el duty cycle en la señal PWM.
![](https://en.wikipedia.org/wiki/Duty_cycle#/media/File:PWM_duty_cycle_with_label.gif)
(Imagen de Eighthave, modified by Teslaton, wikimedia commons)

NOTA: Mas adelante se explicara en detalle como calcular el duty cycle.

Si el duty cycle es de 50%, entonces al LED le llegara el 10% del voltaje maximo que puede suministrar el PIN, en nuestro caso 5v, entonces el voltaje que llegaria al LED seria de: 2.5v

# Generando distintos colores utilizando señales de PWM - 7
Para lograr esto, primero necesitamos un LED RGB (en nuestro caso un catodo comun), luego procedemos a conectar cada anodo a un pin PWM distinto del Arduino, asi cambiamos la intensidad de cada color separadamente y mediante la combinacioón de dichos brillos se pueden producir diferentes brillos. 

# Implicacion de los diodos RGB de anodo comun y catodo comun en el programa del microcontrolador - 8
El programa que escribamos para el microcontrolador cambiara dependiendo de si el LED RGB es de cátodo o ánodo común, por un lado el programa para un LED de cátodo comun es muy intuitivo ya que hay una relación directamente proporcional entre el brillo del LED y el valor del duty cycle, a mayor sea el duty cycle mayor sera el brillo y a menor duty cycle menor brillo.

Por otra parte en el LED de ánodo comun el programa puede llegar a ser un poco anti-intuitivo, porque la relación entre el duty cycle y el brillo del LED es inversamente proporcional, pero esto tiene una explicación que hace que este comportamiento sea intuitivo:

Hay que decir que lo que importa en el circuito es la caida de voltaje entre los dos terminales (de la fuente) y no los valores de voltaje que hay en cada terminal especifico, por ejemplo es lo mismo para el voltaje (y la corriente) del circuito que un terminal de la bateria este a +5v y el otro a 0v, a tener, un terminal a +10v y el otro a +5v, en ambos casos la diferencia de voltaje entre ambos terminales es de 5v, donde el terminal con el valor de voltaje mas pequeño se comporta como el lado negativo de la fuente.

Dicho lo anterior se puede explicar la relación inversamente proporcional entre el brillo del LED RGB de ánodo común y el duty cycle, el anodo comun del LED RGB (el mismo anodo para los tres LEDs internos) esta siempre a +5v mientras el cátodo de cada led esta conectado a un pin PWM distinto del Arduino, este actuara como el terminal negativo de la bateria, por tanto, cuando un duty cycle sea del 0% la diferencia de voltaje entre los pines del Arduino sera de: 5v-dutyCyle*5v = 5v-0=5v, lo cual nos indica que hay una alimentación de 5v en el circuito.

Si cambiamos el duty cycle a 50%, 5v-0.5*5v=2.5v, es decir, ahora la alimentacion del circuito es de 2.5v lo cual producirá menos corriente y por consiguiente el LED brillará menos.

En este punto ya debe ser trivial ver que cuando el duty cycle sea del 100% el LED no prendera en absoluto, y debe ser fácil ver y comprender la relación inversamente proporcional entre el duty cycle y el brillo del LED RGB.

Es por tanto trivial darse cuenta que el programa del microcontrolador que escribamos cambiara dependiendo del tipo de LED RGB que queramos controlar.

# El duty cycle - 9
Es el ratio entre el pulse width y el periodo de la señal PWM, el pulse width es el tiempo que mantiene la señal en el estado logico true durante un ciclo, en tanto el periodo de la señal es el tiempo que dura un ciclo.

![](http://imgur.com/p67BpIW.gif)

La forma de calcularlo es la siguiente:

![](https://latex.codecogs.com/gif.latex?DutyCycle=\frac{AnchoDePulso}{Periodo})

# Calculando el periodo y la frecuencia de una señal de PWM en el Arduino - 10
Para calcular esto primero debemos tener en cuenta que el Arduino utiliza unos "dispositivos internos" llamados timers, estos determinan la frecuencia de la señal PWM, en total el Arduino UNO (ATmega328P) tiene 3 timers:

1. timer0 hace PWM para pines 5,6 a una frecuencia base de 62500Hz
2. timer1 hace PWM para pines 9,10 a una frecuencia base de 31250Hz
3. timer2 hace PWM para pines 11,3 a una frecuencia base de 31250Hz

Cada una de las frecuencias bases de estos timers se puede dividir por un **registro de preescalado** para obtener una frecuencia determinada, los posibles divisores para cada timer estan detallados a continuación.

1. timer0 se puede dividir frecuencia base por: 1, 8, 64, 256 y 1024.
2. timer1 se puede dividir frecuencia base por: 1, 8, 64, 256 y 1024.
3. timer2 se puede dividir frecuencia base por: 1, 8, 32, 64, 256 y 1024.

Por defecto el registro de preescalado para el timer0 es de 64:
![](https://latex.codecogs.com/gif.latex?\frac{62500Hz}{64}\approx&space;980Hz)

Por tanto la frecuencia del PWM en los pines 5 y 6 es por defecto de 980Hz.

Asi, la frecuencia por defecto en los pines 9, 10, 11 y 3 se puede calcular de forma similar:
![](https://latex.codecogs.com/gif.latex?\frac{31250}{64}\approx490Hz)

(Teniendo en cuenta que el registro de preescalado por defecto es de 490Hz)

Conociendo la frecuencia podemos calcular el periodo, es decir el tiempo que tarda un ciclo en completarse, usando una sencilla formula:
![](https://latex.codecogs.com/gif.latex?Periodo=\frac{1}{Frecuencia})

Usando la formula anterior, deducimos que el periodo en los pines 9, 10, 11 y 3 por defecto es de:
![](https://latex.codecogs.com/gif.latex?Periodo=\frac{1}{980Hz}\approx0.001020s\approx1020&space;us)


# Implicaciones de frecuencias altas o bajas en la señal PWM que controla un LED - 11
Si usamos los timers la frecuencia mas alta que podriamos lograr con el Arduino UNO seria una de 62500Hz, esto quiere decir que cada ciclo dura 0.000016s, lo cual quiere decir que estamos prendiendo y apagando el LED cada 0.00016s, pero en la practica, al menos con el LED que estamos usando, esto no es posible, porque el LED tarda un tiempo en encenderse, y lo estamos apagando antes de que logre encenderse completamente, por lo tanto lo que obtendriamos seria un led en dos estados, medio brillante y apagado.

En el caso contrario, es decir si le enviamos una señal de PWM con una frecuencia muy baja, lo que obtendremos será un LED parpadiando posiblemente, puesto que tendriamos un periodo muy largo, que nuestro ojo podria percibir, en este punto es dificil decir a partir de que frecuencia el parpadeo del LED es visible por el ojo humano al natural debido a que en los humanos hay ojos que funcionan mejor que otros.

# Diferencia entre una interfaz paralela y una interfaz serial - 12
En una interfaz paralela, varios BITS de informacion pasan al mismo tiempo (paralelamente) por la interfaz de comunicación en cambio en una interfaz serial, en un momento de tiempo dado solo pasa un unico bit de información, obligando asi a enviar la información en una especie de cadena (chain).

# Diferencia entre una comunicación serial sincrona y una asincrona - 13
En una comunicación serial sincrona los dispositivos que se estan comunicando comparten la misma señal de reloj, esto quiere decir que se necesita de un segundo cable a parte del cable de datos para poder trasmitir esta información, en cambio, en una comunicación ásincrona los dispositivos que se estan comunicando tienen cada uno se propio reloj, pero deben antes de iniciar la comunicación acordar un tiempo de reloj común entre ellos, para evitar perdida de información o resultados inesperados.

# Niveles logicos del microcontrolador ATmega328P - 14
En el microcontrolador del Arduino un +5v es considerado un true y un 0v es considerado un false.

> leer lectura complementaria sparkfun logic-levels

# Consideraciones a tener en cuenta al conectar por medio de una interfaz serial un microcontrolador que opera a 5v con un sensor o actuador que opera a 3v o 3.3v - 15
Si vamos a conectar por medio de una interfaz serial un sensor al microcontrolador que opera a 5V es altamente recomendable hacer primero un circuito acondicionador que evite que el sensor se queme por un sobrevoltaje y al mismo tiempo otro circuito acondicionador que transforme los valores de voltaje para que sea el apropiado para la comunicacion entre los dispositivos.

Es decir, si el sensor envia una señal de 3.3v ¿como deberia ser interpretada por el arduino? ¿como un true o como un false? para solucionar este problema se podria usar algun tipo de circuito acondicionador que mapee el voltaje de salida del sensor al voltaje de entrada del arduino.

# Bits de sincronizacion, datos y paridad en una comincacion serial - 16
Los bits de sincronización en un mensaje serial son generalmente los que indican donde empieza y donde terminan los bits que representan la información que se quiere transmitir, por otra parte, los datos son los bits que representan la información que se desea trasmitir, generalmente y el bit de paridad es un bit que sirve para deteccion de errores en la transmición del mensaje, generalmente esta ubicado entre el ultimo bit de datos y el primer bit indicador de finalización.

![](http://imgur.com/YgQzQMK.gif)

1. El start bit indica que los siguientes bits hacen parte de un mensaje.
2. Los 8 siguientes bits contienen la información que se quiere enviar, podria ser un caracter ASCII o simplemente un numero representado en binario.
3. En este caso se define una paridad par, por tanto el parity bit sera true solo cuando en los 8 bits que contienen la información haya una cantidad inpar de true, de esta forma se garantiza que siempre deberia haber una cantidad par de true, si habiendo definido una paridad par, no llega una cantidad par de true podemos concluir que la información fue corrompida o modificada en la comunicación, esta no es una tecnica muy efectiva para la detección de errores, pero mejor algo que nada.
4. El end bit indica que el mensaje que se queria enviar ya se envio.


# El baud rate - 17
Segun la wikipedia, es el número de unidades de señal trasmitidos por segundo, en nuestro caso el baud rate es el igual al bit per second (bps) pero estos dos valores no son siempre iguales.

Por ejemplo cuando implementemos mas adelante la lampara RGB a control remoto veremos que se define un baud rate de 9600, esto es, que en un segundo se van a enviar 9600 bits del control remoto a la lampara.

# El concepto de endian en comunicaciones seriales - 18
El concepto de endian es importante para saber si el primer bit del dato es el bit mas significativo o el menos significativo, por ejemplo vamos a un numero mediante una comunicación serial entre un arduino hipotetico A y otro arduino hipotetico B.

Por defecto se envia el bit menos significativo primero (Little-Endian).

# Enviando caracteres ASCII por el serial del arudino - 19
Imaginemos que tenemos un Arduino UNO A y otro Arduino UNO B

¿Que viaja por el cable si escribimos desde el arduino A Serial.println(“Hola Mundo”)?
Se debe tener en cuenta que cuando se envia un array de chars lo que ocurre es que se envia cada char en un packet diferente, es decir, si tenemos un array de 10 chars, se enviarian 10 packets cada uno conteniendo cada char del array cada packet detras del otro.

Tambien debes tener en cuenta que el hecho de que una comunicación sea litle endian no significa que al enviar "Hola Mundo" se va a enviar primero la 'o' de mundo.

La función print/println en Arduino envia el valor ASCII que representa la letra/palabra/numero escrita en su argumento. 

![](http://imgur.com/b8OEgaC.gif)

NOTA: Imagina que el end de cada letra esta conectado al start de la siguiente.

NOTA2: Mira que el primer bit de todos los packets es false siempre y el ultimo es true siempre, estos son el bit de inicio y el bit de fin respectivamente, los 8 bits intermedios tienen el numero en binario que representa cada una de las respectivas letras en la codificación ASCII.

NOTA3: El numero se envia litte-endian, es decir si se quiere enviar 2 (0b10), por el puerto serial se envia el numero "al revez", es decir: (0b01)

# Conexion para comunicación serial entre dos Arduino UNO - 20
![](http://imgur.com/eLpgASm.gif)

Se conecnta el pin de trasmición del Arduino UNO A al pin de recepción del Arduino UNO B, y se ponen los dos arduinos a una tierra comun para evitar accidentes inesperados.

# Como se conecta un Arduino UNO al PC - 21
![](http://imgur.com/a0K4ngB.gif)

El puerto USB tipo B tiene tres calbes, 2 de alimentación, y 2 de datos, +D y -D, donde D es el dato a trasmitir y -D debe ser el valor tal que +D-D=0.

Vemos que el +D y el -D del USB tipo B entra en el microncontrolador ATmega16U2-MU, el cual es un microcontrolador al que alguien programo para que convierta la señal de la USB a una señal serial que sea entendible por el 328P, luego esta señal se envia al 328P.

Notemos que el TX del 16u2 se conecta al RX del 328p y el RX del 16u2 se conecta al TX del 328p.

Descargue el diagrama esquemático de un arduino UNO R3. Identifique: cómo se conecta al computador, cómo se conectan el puerto serial a los conectores externos de la tarjeta.

Al mismo tiempo se observa que los pines I/O 0 y 1 del Arduino estan conectados directamente a los mismos pines RX y TX del 328p a los que esta conectado el RX y TX del 16u2.

Asi, podemos por ejemplo leer desde otro Arduino B un dato que llegue al 328p desde la USB de Arduino A.

# Diferencias entre comunicaciónes seriales TTL y comunicaciones seriales RS232 - 22
Una de las principales diferencias son los niveles logicos de ambos tipos de comunicación, idealmente, en una comunicación TTL el valor logico true es representado con un voltaje de 5v, y el valor logico falso es representado con un 0v.

Por otro lado una comuniación RS232 los niveles son como los del TTL pero volteados (fliped) y escalados, es decir, una señal alta representa un valor logico falso, y una señal baja representa un valor logico true, en este tipo de comunicación los voltajes van entre -13v y 13v.

# La UART - 23

Investigar que es la UART
La UART es un microchip que se ha programado para que se encarge de controlar las transmiciones entre el puerto USB y el puesto Serial, entre las funciones de la UART estan:

1. Convierte los Bytes que recibe del computador a un stream serial que pueda ser interpretado por los otros chips del microcontrolador.
2. Para hacer transmisiones al PC convierte los bytes que vienen del microcontrolador a unos Bytes que puedan ser leidos por el PC.
3. Agrega los bits de start y end en las transmisiones y los quita en los packets entrantes para que solo quede el dato.
4. Si se desea agrega el bit de parity y realiza la verificación.
6. Se encarga de trasmitir datos en el baud rate especificado, es decir si el baud rate es de 9600Hz, entonces la UART transmitirá una señal cada 1042 micro segundos (1/9600Hz).

# Errores comunes al conectar dispositivos por el puerto serial - 24
A conrinuación se enumeraran algunos errores comunes a la hora de hacer comunicaciones por puerto serial:
1. No conectar RX a TX y TX a RX a la hora de hacer las conexiones seriales entre dos dispositivos seriales.
2. Baud rate incompatible, cuando el baud rate de los dos dispositivos seriales no es el mismo, provocando errores inesperados.
3. Conectar el TX de dos dispositivos seriales al mismo RX del receptor, cuando conectamos dos dispositivos al mismo RX, por ejemplo si conectamos el TX de un GPS al pin 1 (RX) de un Arduino UNO  y luego intentamos hacer un Serial.read() muy posiblemente obtendremos un comportamiento erratico por parte del Arduino ya que estamos intentando escuchando por un mismo RX dos dispositivos distintos, es como si muchas personas te hablaran al mismo tiempo, se hace mucho mas dificil entender sus mensajes, lo contrario es posible sin obtener errores, es decir desde un ArduinoUNO hablarle a varios dispositivos, siguiendo con la analogia anterior, tu puedes hablarle a varias personas al mismo tiempo y lograr que todas te entiendan, sin embargo esto puede tener implicaciones en seguridad a tener en cuenta en algunos escenarios.

# Sensores utilizados en el proyecto 2 del texto guia - Making Things Talk - 25
1. Flex sensor resistor (Sensor de flexion), 
2. Momentary switches (Un boton), es un simple boton.

# Funcionamiento de los sensores mencionados - 26
El flex sensor resistor es una resistencia que cambia en función de que tan flexionada este, generalmente si dejamos el sensor totalmente recto la resistencia es de 30KOhm y a medida que la vayamos flexionando se va incrementando la resistencia hata los 70KOhm, esta resistencia se puede conectar con una resistencia fija para hacer un divisor de voltaje y enviarle esta señal a un convertidor analogo-digital como el del Arduino por ejemplo.

Por otra parte el Momentary switch es un boton, en este caso, normalmente abierto, que se cierra mientras se mantiene pulsado y se vuelve a abrir cuando se suelta.

# Algunos usos de los sensores mencionados - 27
Podemos usar un Flex Resistor como sensor en una puerta para saber ¿que tan abierta o cerrada se encuentra? o quizas como un sensor para crear animales de peluche sensibles a ciertos movimientos.

¿Y que usos podriamos darle a unos botones? Los botones estan en todos lados, teclados de computadora, ascensores, puertas, cajas, buses, automoviles, semaforos, entre otros.

# Diferencia entre el metodo println y el write del objeto Serial de Arduino - 28
Serial.println() envia por el serial el codigo ASCII que representa lo que recibe el parametro s, que puede ser un char, string o incluso un numero, y agrega al final el caracter de salto de linea.

```c++
Serial.println("Hola Mundo");
/* ENVIA los siguientes datos uno tras otro.
'H' (0x48)
'o' (0x6F)
'l' (0x6C)
'a' (0x61)
' ' (0x20)
'M' (0x4D)
'u' (0x75)
'n' (0x6E)
'd' (0x64)
'o' (0x6F)
'\n' (0xA)
*/
```

NOTA: En parentesis se pone el ASCII code en hexagesimal que representa el char correspondiente.

En cambio con el metodo write, se envia el dato que se recibe por el argumento, tal cual, es decir, el metodo no se encarga de hacer un procesamiento para hallar el caracter ASCII que representa el argumento, en su lugar simplemente envia lo que recibe, la diferencia entre el metodo write y el print se hace notoria cuando queremos enviar numeros, veamos el siguiente ejemplo:
```c++
Serial.print(45);
/* ENVIA
'4' (0x34)
'5' (0x35)
*/

Serial.write(45);
/* ENVIA
45
*/
```

NOTA: La diferencia entre print y println es que esta ultima agrega un caracter de salto de linea al final del mensaje.

Como se puede ver en el ejemplo anterior el write envio el valor 45, el cual en un simulador de terminal como CoolTerm sera interpretado como un caracter ASCII, como el numero 45 (decimal) representa la letra 'E' en el codigo ASCII, lo que veremos en el CoolTerm sera una letra E.

Si al metodo write pasamos como argumento un array de chars como "Hola Mundo", el metodo procedara enviando el valor de cada char uno tras otro y no agregara el caracter de salto de linea al final del array de chars, en este caso veremos en CooLTerm HolaMundo.

Para concluir el metodo print envia el codigo ASCII que representa lo que sea que se le pase como argumento, mientras el metodo write enviará exactamente el dato que reciba como argumento, esta diferencia se hace notoria cuando queremos imprimir numeros en la terminal serial del computador.

# Protocolo de comunicacion usado en el proyecto 2 del texto guia - 29
El Arduino envia al computador el valor recibido de los sensores que tiene conectados mediante un protocolo ASCII, a continuación se muestra la estructura del mensaje:

"leftArmFlexValue,rightArmFlexValue,resetButtonState,serveButtonState\n"

En otras palabras el Arduino envia al programa de processing un string que contiene el valor de cada uno de los sensores en el momento actual, tengase en cuenta, que el Arduino envia esto usando el metodo print, por lo tanto lo que en realidad envia es la representación en ASCII de los valores de los sensores, como se explico anteriormente, ejemplo de envio:

"284,284,1,1\n"
"285,283,1,1\n"
"286,284,1,1\n"
"289,283,1,1\n"

Luego en Java (o cualquier lenguaje que estes usando para construir el server) es elemental procesar el string que se va recibiendo y convertir los valores ASCII que representan numeros a un int.

# Implementando una Lampara RGB a control remoto
Vamos ahora a poner en practica algunos conceptos mencionados anteriormente, para ello vamos a implementar una lampara RGB cuyo color se pueda controlar desde un celular mediante Bluetooth.

A continuación un diagrama en el que se muestra por encima como encajara todo.
![](https://imgur.com/HkO7hlh.gif)

A continuación muestro en un video el resultado final. (Disculpen la mala calidad)

<iframe width="560" height="315" src="https://www.youtube.com/embed/xHAPReRyei8?rel=0" frameborder="0" allowfullscreen></iframe>


Se explicara el proyecto empezando por la capa del hardware y finalizando con los protocolos de comunicación y capa de software diseñados.
![](https://imgur.com/e72lTZu.gif)

## Circuito
![](https://imgur.com/07e4lAG.gif)
El circuito completo esta dividido en 2 partes, un encargada de gestionar el LED RGB y otra encargada de gestionar el modulo Bluetooth, empecemos analizando la que se encarga del RGB.

En nuestro casos usaremos un led RGB de catodo comun, es decir los tres led del RGB estan conectados a la misma tierra y a fuentes de voltaje "separadas", cuanto mas voltaje reciba un LED mas brillante sera su color, la suma de los tres niveles de brillo de los LEDs del RGB producen el efecto de LED multicolor.

La fuente de los leds R, G y B son los pines PWM 5, 6 y 9 respectivamente, los cuales se conectan con una resistencia de 250Ohm para garantizar que el LED no se quemara, ademas cabe mencionar, que se conectan a pines PWM para poder variar "programatisticamente" desde el Arduino el voltaje que recibe cada LED usando el metodo analogWrite(pin, intensity).

Por otra parte si analizamos el circuito encargado de gestionar el modulo Bluetooth HC-05 vemos un divisor de voltaje conectado al pin RX del HC-05, esto debido a que las señales que envia el TX del ArduinoLeonardo van de 0 a 5v, mientras que el RX del HC-05 requiere niveles de voltaje de 0v a 3.3v

![](https://imgur.com/sxTrkdR.gif)
(Fuente: wikipedia)

Si hacemos:

![](https://latex.codecogs.com/gif.latex?V_{in}=5v)

![](https://latex.codecogs.com/gif.latex?R_{2}=2k\Omega)

Y suponemos que queremos un voltaje de salida de:

![](https://latex.codecogs.com/gif.latex?V_{out}=3.3v)

Despejando R1 en:

![](https://latex.codecogs.com/gif.latex?V_{out}=V_{in}*\frac{R_2}{R_1&plus;R_2})

Obtenemos la ecuación:

![](https://latex.codecogs.com/gif.latex?R_1=\frac{V_{in}*R_2}{V_{out}}&space;-&space;R_2)

En la cual, si reemplazamos obtenemos el valor que deberia tener R1

![](https://latex.codecogs.com/gif.latex?R_{1}=1k\Omega)

Con lo cual se explica el valor de la resistencias en el divisor de voltaje.

## Protocolos de comunicacion
Ahora vamos a definir unas reglas de comunicación entre el Arduino y la Aplicación Android para que se puedan entender.

Haremos que se comuniquen mediante caracteres ASCII serialmente, asincronamente y a una velocidad de 9600 Bauds

La estructura de los mensajes que podra escuchar el Arduino son:

### Comando RGB
> "RGB=rrr,ggg,bbb;"

¿Que hace este comando?
- Asigna la intensidad luminica indicada por rrr al LED R.
- Asigna la intensidad luminica indicada por ggg al LED G.
- Asigna la intensidad luminica indicada por bbb al led B.

NOTA: Los valor rrr, ggg y bbb son un numero de 0 a 255.

### Comando C
> "C"

¿Que hace este comando?
Trasmite desde el modulo Bluetooth a la aplicación Android el ultimo color guardado en la EEPROM del arduino, el mensaje trasmitido tiene la siguiente estructura: "rrr,ggg,bbb\n"

Mensajes que podra escuchar la aplicación Android:
### Comando Cresponse
> "rrr,ggg,bbb\n"

¿Que hace este comando?
"Setea" los sliders r, g, b de la interfaz grafica al valor correspondiente indicado por rrr, ggg y bbb respectivamente.

Tengase en cuenta que el mensaje "C" se envia de la Aplicación al Arduino, para de alguna forma decirle al Arduino: "Oye, dime cual fue el ultimo color que guardaste en tu EEPROM para yo poner ese color en mi interfaz de usuario" a lo que el arduino responde algo como: "Claro, fue rrr,ggg,bbb\n gracias por preguntarme".

## Software
En este punto ya tenemos el hardware preparado y hemos definido un protocolo a seguir por el Arduino y la aplicación para comunicarse, pero el sistema no tomara vida hasta que escribamos el software que controle Al Arduino y la Aplicación.

### Software para el arduino
El programa que escribamos para el Arduino debe realizar las siguientes tareas:
1. Recibir los mensajes entrantes por el pin RX.
2. Interpretar los mensajes, es decir "RGB=55,20,100;" de un mensaje como el anterior almacenar los valores r, g y b en variables separadas como enteros y no como strings.
3. Usar el mensaje interpretado para controlar el LED RGB.
4. Responder a las solicitudes de información por parte de la Aplicación (Por ejemplo el comando "C").
5. Guardar persistentemente (En la EEPROM) los colores resultantes del mensaje interpretado, para que al apagar y volver a prender el circuito, no se pierda el ultimo color asignado.

Para recibir y procesar los mensajes entrantes se usó un enfoque de maquina de estados, muy parecido al explicado [en este foro](https://www.gammon.com.au/serial)

```c++

void processIncomingnByte(const char character) {
    /*
     * Envia el ultimo color almacenado en la EEPROM
    */
    if (character == 'C') {
      EEPROM.get(rEEDir, r);
      EEPROM.get(gEEDir, g);
      EEPROM.get(bEEDir, b);
      Serial1.print(r);
      Serial1.write(',');
      Serial1.print(g);
      Serial1.write(',');
      Serial1.print(b);
      Serial1.write('\n');
    }
}

```

La parte del codigo mostrada arriba se encarga de escuchar y responder al comando "C".


```c++
void processR (const unsigned int val) {
  if (val > 255) {
    Serial.println("R value max val is 255");
    actualValue = 0;
    return;
  }
  r = val;
  /*
   * The purpose of this example is to show the EEPROM.update() method that writes data only if it is different from the previous content of the locations to be written. This solution may save execution time because every write operation takes 3.3 ms; the EEPROM has also a limit of 100,000 write cycles per single location, therefore avoiding rewriting the same value in any location will increase the EEPROM overall life.
  */
  EEPROM.update(rEEDir, r);
  Serial.print("R= ");
  Serial.println(val);
  actualValue = 0;
}
```

Cada canal de color tiene una funcion similar a la anterior, que se encarga de tomar el valor del canal de color correspondiente y guardarlo en una variable que luego sera utilizada para enviar mediante PWM un voltaje al LED, la función EEPROM.update(dir, val) se encarga de escribir en la direccion *dir* de la EEPROM el valor *val*, si y solo si *val* es diferente al valor que hay actualmente en dicha dirección, esto sirve como medida de protección a la EEPROM, ya cada registro de la misma se dañara luego de 100000 operaciones de escritura, de esta forma se puede aumentar la vida util de los registros de la EEPROM.

```c++
void showRGBLed() {
  analogWrite(rLedPin, r);
  analogWrite(gLedPin, g);
  analogWrite(bLedPin, b);
}
```

Por ultimo esta función se encarga enviar un voltaje al led correspondiente del RGB.

NOTA: Las funciones mencionadas anteriormente se ejecutan en cada ciclo de la funcion loop()

```c++
void loop() {
  // Protocolo
  // RGB=val0,val1,val2\n
  if (Serial1.available()) {    
    processIncomingnByte(Serial1.read());    
  }
  showRGBLed();
}
```

### Software para la aplicación
La aplicación que envia mensajes al Arduino se escribió en app inventor, y consiste basicamente de dos pantallas, una para conectarse a un dispositivo bluetooth y la otra para enviar un color RGB al Arduino mediante una interfaz de usuario comoda.
![](https://imgur.com/wO68yrZ.gif)

El resultado final de la pantalla de envio de color fue el siguiente:
![](https://imgur.com/ybitFnH.gif)
El "255,63,0" es la respuesta que se recibio del Arduino luego de enviarle el comando "C", a continuación se muestra el bloque de codigo en la aplicación que se encarga de hacer la solicitud y procesar la respuesta.

![](https://imgur.com/RCGpnQJ.gif)
En resumidas palabras, la funcion getLatestColor() se encarga de enviar el mensaje "C" al arduino, procesarlo para guardar cada componente de color como una lista de integers que almacena el color, pone la cabeza de los sliders en el valor indicado por dicho mensaje y setea el color del cuadro verde y por ultimo hace true una variable global tipo bandera que permite que el codigo dentro de los eventos onSliderPositionChanged() de los sliders se pueda ejecutar, esto es necesario porque cambiar la cabeza del slider desde el codigo hara que dichos eventos se ejecuten produciendo como resultados dos piezas de codiga que intentan modificar la lista actualColor al mismo tiempo.

NOTA: La funcion getLatestColor() es llamada tan pronto la aplicación se conecta al HC-05. 

![](https://imgur.com/Vjz3G1G.gif)
Cuando cambia la posicion de la cabeza de un slider, se actualiza la variable que almacena el colorActual (que se enviara como mensaje ASCII al arduino) y se actualiza el color del cuadro verde al nuevo color, por ultimo se procede a empaquetar la información del nuevo color y enviarsela siguiendo el protocolo establecido al Arduino.

![](https://imgur.com/RyeM33w.gif)
Se usa el metodo join(str1, str2, str3, ...), que es una especie de concatenador de strings en appinventor, con el objetivo de crear un string que siga la sintaxis establecida por el protocolo discutido previamente que trasmita al Arduino el color seleccionado actualmente en la interfaz de usuario para que este se encargue de ponerle dicho color al led RGB, este string cambia cada vez que se mueve un slider, asi que se esta trasmitiendo una cantidad razonable de datos.

Aqui seria bueno decir que se puede usar una aplicacion como Bluetooth Terminal para enviar los comandos RGB al arduino, sin embargo tocaria escirbir manualmente todo el comando.
![](https://imgur.com/JrlDubV.gif)

Mientras que la aplicación desarrollada "escribe" automaticamente el comando RGB y lo envia al arduino a la velocidad a la que movemos los sliders de la interfaz de usuario, sin necesidad de que tengamos que escribir en el teclado del Arduino el comando manualmente.

Dicho lo anterior, se puede resumir la funcion de la Aplicacion Android desarrollada a:

> Una aplicación que escribe y envia comandos RGB muy rapidamente sin necesidad de escribirlos manualmente con el teclado.

Hasta pronto.

# Referencias
<a href="https://en.wikipedia.org/wiki/Voltage_divider" target="_blank">Voltage divider</a>

<a href="https://learn.sparkfun.com/tutorials/flex-sensor-hookup-guide" target="_blank">Flex Sensor Resistor</a>

<a href="http://whatis.techtarget.com/definition/UART-Universal-Asynchronous-Receiver-Transmitter" target="_blank">What is the UART</a>

<a href="https://es.wikipedia.org/wiki/Universal_Asynchronous_Receiver-Transmitter" target="_blank">UART, Wikipedia</a>

<a href="http://www.asciitable.com/" target="_blank">Ascii table</a>

<a href="https://es.wikipedia.org/wiki/Tasa_de_baudios" target="_blank">Baud rate</a>

<a href="https://electronics.stackexchange.com/questions/79373/how-to-choose-right-pwm-frequency-for-led" target="_blank">How to choose right PWM frequency for a LED</a>

<a href="https://forum.arduino.cc/index.php?topic=341196.0" target="_blank">Understanding timers Arduino UNO</a>

<a href="https://playground.arduino.cc/Code/PwmFrequency" target="_blank">Setting PWM Frequency</a>

<a href="https://playground.arduino.cc/Main/TimerPWMCheatsheet" target="_blank">Timer PWM Cheatsheet</a>

<a href="https://www.luisllamas.es/salidas-analogicas-pwm-en-arduino/" target="_blank">Salidas analogicas PWM</a>

<a href="https://en.wikipedia.org/wiki/Duty_cycle" target="_blank">Duty cycle</a>

<a href="https://learn.adafruit.com/all-about-leds/" target="_blank">All about LEDs</a>

<a href="https://electronics.stackexchange.com/questions/10962/what-is-forward-and-reverse-voltage-when-working-with-diodes" target="_blank">What is “forward” and “reverse” voltage when working with diodes?</a>

<a href="https://en.wikipedia.org/wiki/Light-emitting_diode#Colors_and_materials" target="_blank">Color de los LED</a>

<a href="https://cdn-shop.adafruit.com/datasheets/WP7113SRD-D.pdf" target="_blank">LED datasheet example</a>

<a href="https://es.wikipedia.org/wiki/Candela" target="_blank">Candela, wikipedia</a>
