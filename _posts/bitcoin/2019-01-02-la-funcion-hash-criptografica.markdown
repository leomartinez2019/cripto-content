---
layout: post
title: "Las funciones hash criptográficas"
categories: bitcoin
mathjax: true
---

Las funciones hash tienen tres atributos principales:

* toman una texto como entrada

* producen una salida de tamaño fijo (256 bits)

* son computadas de forma eficiente

A su vez tienen tres propiedades relacionadas con la seguridad:

* son libres de colisiones

* funcionan para ocultar

* son difíciles de desencriptar

### 1. Libres de colisiones

No se pueden encontrar $$x$$ e $$y$$ tales que

$$ x!=y $$ y 
$$ H(x)=H(x) $$

Las colisiones sí existen pero son muy difíciles de encontrar. Para hallar una habría que
probar 2^130 entradas escogidas al azar. En ese caso hay un 99.8% de posibilidades de que dos de ellas colisionen.
El problema aquí es que tomaría tanto tiempo y poder de cómputo que se considera en la práctiva inviable.

Si asumimos que la función hash es libre de colisiones, entonces la podemos usar como un compresor de mensajes.
Si sabemos que $$ H(x)=H(y) $$, entonces es seguro asumir que $$ x = y $$.

Un ejemplo de aplicación de esta idea sería la comprobación de que dos archivos son iguales. Si los archivos son muy grandes podría ser difícil comprobar si son iguales.
Sin embargo, si se aplica un hash a cada archivo y se obtiene el mismo valor, podemos asegurar que los dos archivos son iguales.

### 2. Ocultamiento

Queremos obtener algo así:

Dado $$H(x)$$, es inviable encontrar $$x$$

Para que esta propiedad se cumpla, necesitamos lo siguiente:

Si r se escoge de una distribución de probabilidad que tiene alta entropía de Renyi, entonces dado $$H(r|x)$$, es inviable encontrar $$x$$.
La alta entropía de Renyi indica que la distribución está muy dispersa, de modo que ningún valor en particular puede ser escogido con más que una probabilidad despreciable.


### 3. Dificultad para descencriptar

Para cada posible valor de salida $$y$$, si $$k$$ se escoge de una distribución con alta entropía de Renyi, entonces es inviable encontrar $$x$$ tal que 
$$H(k|x)=y$$.


## Punteros hash

Un puntero hash es donde se almacena cierta información además del hash de esa información. Si tenemos un puntero hash, podemos
indagar por la información y verificar que no se ha alterado.

La idea es crear estructuras de datos con estos punteros, como listas enlazadasy y árboles binarios.

Un ejemplo es una lista enlazada:

Si un atacante cambia los datos de un bloque, el hash de ese bloque cambia. Esta inconsistencia es fácilmente detectable con el hash principal. Un atacante incuso puede alterar todos los bloques pero no el hash raíz que tenemos almacenado.

otra estructura de datos que se puede crear con punteros es un árbol binario también llamado árbol de Merkle. 
