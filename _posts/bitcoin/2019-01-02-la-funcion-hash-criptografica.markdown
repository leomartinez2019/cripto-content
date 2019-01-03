---
layout: post
title: "Las funciones hash criptográficas"
categories: bitcoin
mathjax: true
---

(Basado en el curso "Bitcoin y tecnología de criptomonedas" de Princeton. Youtube: [Princeton Bitcoin Course](https://www.youtube.com/channel/UCNcSSleedtfyDuhBvOQzFzQ/videos))

Una función hash asigna cadenas de texto arbitrarias a una salida de longitud fija de una manera determinística, pública y aleatoria.
Las funciones hash tienen tres atributos principales:

* Toman un texto como entrada

* Producen una salida de tamaño fijo (256 bits)

* Se calculan de forma eficiente, en un tiempo razonable

A su vez tienen tres propiedades de seguridad:

* Son libres de colisiones

* Ocultan información

* Son casi imposibles de desencriptar

### 1. Libres de colisiones

![](/images/libre-colisiones.png)

Las colisiones sí existen pero son muy difíciles de encontrar. Para hallar una habría que
probar 2^130 entradas escogidas al azar. En ese caso hay un 99.8% de posibilidades de que dos de ellas colisionen.
El punto a destacar aquí es que tomaría tanto tiempo y poder de cómputo que se considera prácticamente inviable.

Si asumimos que la función hash es libre de colisiones, entonces la podemos usar como un compresor de mensajes.
Si sabemos que $$ H(x)=H(y) $$, entonces es seguro asumir que $$ x = y $$.

Un ejemplo de aplicación de esta idea sería la comprobación de que dos archivos son iguales. Si los archivos son muy grandes podría ser difícil comprobar si son iguales.
Sin embargo, si se aplica un hash a cada archivo y se obtiene el mismo valor, podemos asegurar que se trata del mismo archivo.

### 2. Ocultamiento

La propiedad de ocultamiento se puede plantear así:

Dado $$H(x)$$, es inviable encontrar $$x$$

Para que esta propiedad se cumpla, necesitamos lo siguiente: si $$r$$ se escoge de una distribución de probabilidad que tiene alta entropía de Renyi, entonces dado $$H(r|x)$$ (dos entradas concatenads), es inviable encontrar $$x$$.
La alta entropía de Renyi (entropía min) indica que la distribución está muy dispersa, de modo que ningún valor en particular puede ser escogido con más que una probabilidad despreciable.
Entonces podemos decir que $$r$$ oculta a $$x$$.


### 3. Dificultad para descencriptar

Para cada posible valor de salida $$y$$, si $$k$$ se escoge de una distribución con alta entropía de Renyi, entonces es inviable encontrar $$x$$ tal que 
$$H(k|x)=y$$. Lo que esto quiere decir es que la única forma de encontrar $$x$$ es recorrer un espacio muy grande de valores que aparecen de forma aleatoria.


## Punteros hash

![](/images/puntero-hash.png)

Un puntero hash es donde se almacena cierta información además del hash de esa información. Si tenemos un puntero hash, podemos
indagar por la información y verificar que no se ha alterado.

La idea es crear estructuras de datos con estos punteros, como listas enlazadasy y árboles binarios.

Un ejemplo es una lista enlazada:

![](/images/linked-hash.png)

Si un atacante cambia los datos de un bloque, el hash de ese bloque cambia. Esta inconsistencia es fácilmente detectable con el hash principal. Un atacante incuso puede alterar todos los bloques pero no el hash raíz que tenemos almacenado.

![](/images/tamper-proof.png)

Otra estructura de datos que se puede crear con punteros es un árbol binario también llamado árbol de Merkle.

![](/images/merkle-tree.png)

![](/images/merkle2.png)
