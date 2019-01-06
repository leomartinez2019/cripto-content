---
layout: post
title: Documentación Técnica de Ethereum
categories: criptomonedas
---

La intención de Ethereum es crear un protocolo alternativo para desarrollar aplicaciones descentralizadas
al introducir un conjunto nuevo de aspectos que creemos serán de gran uso en una clase extensa de aplicaciones descentralizadas, sobretodo en situaciones
donde importan el tiempo de desarrollo, la seguridad para aplicaciones pequeñas y poco usadas,
y la capacidad de interacción eficiente de diferentes aplicaciones. Ethereum logra esto construyendo lo que esencialmente es la mayor capa fundamental abstracta: un blockchain con un lenguaje Turing completo incorporado que le permite a cualquiera escribir contratos inteligentes y aplicaciones
descentralizadas donde se pueden crear reglas propias y arbitrarias de propiedad, formatos de transacción y funciones de transición de estado.
Se puede escribir una versión esencial de Namecoin en solo dos líneas de código y otros protocolos tales como divisas y sistemas de reputación en menos de veinte líneas.
También se pueden crear sobre la plataforma contratos inteligentes que son especies de  "cajas" criptográficas que contienen un valor y lo revelan solo si se cumplen ciertas condiciones.
Éstas tienen mucho más poder que las que ofrece el código de Bitcoin debido a las capacidades agregadas de la completitud de Turing, el reconocimiento del valor, del blockchain y del estado.

### Filosofía

El diseño detrás de Ethereum debe seguir los siguientes principios:

1. **Simplicidad**: el protocolo Ethereum debe ser tan simple como sea posible, incluso sacrificando el amacenamiento de datos y la eficiencia de tiempo. Un programador promedio
debería ser capaz de seguir e implementar una especificación completa, dándose cuenta del potencial sin precedentes de democratización que las criptomonedas traen y extendiendo la visión
de Ethereum como un protocolo abierto a todos. Ninguna optimización que agregue complejidad se debería incluir a menos que esa optimización brinde beneficios considerables.

2. **Universalidad**: una parte fundamental de la filosofía de diseño es que Ethereum no tiene "características".
En vez de eso, Ethereum trae un lenguaje interno de codificación Turing completo que un programador puede usar
para crear cualquier contrato inteligente o tipo de transacción que puede ser definido matemáticamente.
¿Quiere crear su propio producto financiero? Con Ethereum se puede. ¿Quiere crear su propia moneda? Desarróllelo como un contrato en Ethereum. ¿Quiere montar un script de ejecución constante o Skynet completo?
Podría necesitar tener varios miles de contratos interconectados y asegurarse de llenarlos de información, pero también se puede en Ethereum.

3. **Modularidad**: las partes del protocolo Ethereum deben ser diseñados para ser tan modulares y separables como sea posible.
Durante la fase de desarrollo, nuestro objetivo es crear un programa en donde si se necesita hacer una pequeña modificación del protocolo en un lugar, el stack de la aplicación
seguirá funcionando sin más cambios. Las innovaciones como Ethash, los árboles modificados de Patricia y RLP (Recursive Length Prefix)
deberían y son implementados como bibliotecas separadas y completas. Este es el procedimiento para que ciertas características disponibles en Ethereum se puedan transferir a otros protocolos, incluso si Ethereum no las aplica. El desarrollo en Ethereum debería ser para beneficiar lo más posible al ecosistema de criptomonedas en general y no solo a Ethereum.

4. **Agilidad**: los detalles del protocolo Ethereum no son inflexibles. Aunque seremos extremadamente cuidadosos al modificar estructuras de alto nivel.
Un ejemplo es la hoja de ruta de partición (sharding roadmap) donde se abstrae la ejecución dependiendo de la disponibilidad de datos que cumplan con el consenso.
Los tests computacionales posteriores en el proceso de desarrollo nos pueden llevar a descubrir que ciertas modificaciones, por ejemplo, al protocolo de arquitectura de la Máquina Virtual de Ethereum (EVM), van a mejorar la escalabilidad o la seguridad. Si esas ocasiones se presentan, las vamos a aprovechar.

5. **No discriminación** y **no censura**: el protocolo no debería tratar de restringir o prevenir categorías específicas de uso. Todos los mecanismos regulatorios en el protocolo deberían ser diseñados para regular directamente el daño y no tratar de oponerse a aplicaciones específicas no deseadas. Un programador puede incluso ejecutar un bucle infinito encima de Ethereum mientras esté dispuesto a seguir pagando el costo de la transacción de cada instrucción de cómputo.

### Las cuentas de Ethereum

En Ethereum el estado se compone de objetos llamados "cuentas" cada una con una dirección de 20 bytes y con estados de transición que representan transferencias directas de valor e información entre cuentas. Una cuenta de Ethereum contiene cuatro campos:

* El número único (nonce) que se usa para asegurarse de que cada transacción se pueda procesar solo una vez.
* El balance actual de ether de la cuenta
* El código de contrato de la cuenta si existe
* El almacenamiento de la cuenta (vacío por defecto)

"Ether" es el principal criptocombustible de Ethereum y se usa para pagar costos de transacción. En general, hay dos tipos de cuentas: **cuentas de propiedad externa**, controladas por claves privadas y **cuentas de contrato** controladas por código de contrato. Una cuenta de propiedad externa no tiene código y se pueden enviar mensajes desde una de estas cuentas creando y firmando una transacción; en una cuenta de contrato, cada vez que se recibe un mensaje, el código se activa, lo que permite leer y escribir al almacenamiento interno y enviar otros mensajes o crear contratos a su vez.

Nótese que los "contratos" en Ethereum no deben verse como algo que debe ser "ejecutado" o "cumplido"; son más bien como "agentes autónomos" que viven dentro del ambiente de ejecución de Ethereum y siempre están ejecutando un segmento específico de código cuando son "despertados" por un mensaje o una transacción y además tienen control directo sobre su propio balance de ether y tienen su propio almacenamiento tipo clave/valor para registrar variables persistentes.

### Mensajes y Transacciones

El término "transacción" se usa en Ethereum para referirse a un paquede de datos firmado que guarda un mensaje que se va a enviar a una cuenta de propiedad externa. Las transacciones contienen:

* El destinatario del mensaje
* Una firma que identifica al remitente
* La cantidad de ether que se transfiere de remitente a destinatario
* Un campo de datos opcional
* un valor STARTGAS que representa el número máximo de instrucciones computacionales que la ejecución de la transacción tiene permitido hacer
* Un valor GASPRICE que representa pago que el remitente hace por cada instrucción computacional

Los primeros tres son campos estándar que se esperan en cualquier criptomoneda. El campo de datos no tiene una función por defecto, pero la máquina virtual tiene un código de operación que un contrato puede usar para acceder a los datos. Como un ejemplo de caso de uso, si un contrato está funcionando como servicio de registro de dominios basado en blockchain, entonces podría interpretar los datos que está recibiendo como compuestos de dos campos: el primer campo es el dominio que hay que registrar y el segundo campo es la dirección IP a la que hay que asociar. El contrato leería estos valores desde los datos del mensaje y los guardaría apropiadamente en el almacenamiento.

Los campos STARTGAS y GASPRICE son cruciales para el modelo antinegación de servicio (DDoS). de Ethereum. Para evitar bucles infinitos accidentales u hostiles o cualquier otro  desperdicio computacional en código, cada transacción necesita establecer un límite acerca de cuantas instrucciones computacionales de ejecución de código puede utilizar. La unidad fundamental de computación es el "gas"; generalmente una instrucción computacional cuasta 1 gas aunque ciertas operaciones tiene un mayor costo por ser más intensas computacionalmente o porque aumentan la cantidad de datos que debe ser almacenado como parte del estado. También hay un pago de 5 gas para cada byte en los datos de transacción. La intención del sistema de pagos es para exigir a un atacante que pague proporcionalmente a cada recurso que consume incuido computacón, ancho de banda y almacenamiento. De esta forma, cualquier transacción que lleve al consumo de una mayor cantidad de cualquiera de estos recursos debe pagar una comisión en gas justamente proporcional al ese incremento

### Mensajes

Los contratos tienen la capacidad de enviar "mensajes" a otros contratos. Los mensajes son objetos virtuales que nunca se serializan y solo existen en el ambiente de ejecución de Ethereum. Un mensaje contiene:

*  El remitente del mensaje (implícito)
*  El destinatario del mensaje
*  La cantidad de ether para transferir junto con el mensaje
*  Un campo de datos opcional
*  Un valor de STARTGAS