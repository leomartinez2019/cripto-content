---
layout: post
title: Documentación Técnica de Ethereum
categories: criptomonedas
---

La intención de Ethereum es crear un protocolo alternativo para desarrollar aplicaciones descentralizadas
introduciendo un conjunto nuevo de aspectos que creemos serán de gran uso en una clase amplia de aplicaciones descentralizadas, sobretodo en situaciones
donde son importantes el tiempo de desarrollo, la seguridad para aplicaciones pequeñas y poco usadas,
y la capacidad de interacción eficiente de diferentes aplicaciones. Ethereum logra esto construyendo lo que esencialmente es la mayor capa fundamental abstracta: un blockchain con un lenguaje Turing completo incorporado que le permite a cualquiera escribir contratos inteligentes y aplicaciones
descentralizadas donde se pueden crear reglas propias y arbitrarias de propiedad, formatos de transacción y funciones de transición de estado.
Se puede escribir una versión esencial de Namecoin en solo dos líneas de código y otros protocolos tales como divisas y sistemas de reputación se pueden escribir con menos de veinte líneas.
También se pueden crear sobre la plataforma contratos inteligentes que son especies de  "cajas" criptográficas que contienen un valor y lo revelan solo si se cumplen ciertas condiciones.
Éstas tienen mucho más poder que las que ofrece el código de Bitcoin debido a las capacidades agregadas de la completitud de Turing, el conocimiento del valor y del blockchain y el estado.

### Filosofía

El diseño detrás de Ethereum debe seguir los siguientes principios:

1. **Simplicidad**: el protocolo Ethereum debe ser tan simple como se pueda, incluso sacrificando el amacenamiento de datos y la eficiencia de tiempo. Un programador promedio
debería ser capaz de seguir e implementar una especificación completa, dándose cuenta del potencial sin precedentes de democratización que las criptomonedas traen y también para extender la visión
de Ethereum como un protocolo abierto a todos. Ninguna optimización que agregue complejidad se debería incluir a menos que esa optimización brinde beneficios considerables.

2. **Universalidad**: una parte fundamental de la filosofía de diseño es que Ethereum no tiene "características".
En vez de eso, Ethereum trae un lenguaje interno de codificación Turing completo que un programador pude usar
para crear cualquier contrato inteligente o tipo de transacción que puede ser definido matemáticamente.
Quiere crear su propio producto financiero? Con Ethereum se puede. Quiere crear su propia moneda? Desarróllelo como un contrato en Ethereum. Quiere montar un script de ejecución constante o Skynet completo?
Podría necesitar tener varios miles de contratos interconectados y asegurarse de llenarlos de información, pero también se puede en Ethereum.

3. **Modularidad**: las partes del protocolo Ethereum deben ser diseñados para ser tan modulares y separables como sea posible.
Durante la fase de desarrollo, nuestro objetivo es crear un programa en donde si se necesita hacer una pequeña modificación del protocolo en un lugar, el stack de la aplicación
seguiría funcionando sin más modificaciones. Las innovaciones como Ethash, los árboles modificados de Patricia y RPL
deberían y son implementados como bibliotecas separadas y completas. Esto es así de tal modo que aunque se usen en Ethereum, incluso si Ethereum no necesita ciertas características,
éstas se pueden usar en otros protocolos. El desarrollo en Ethereum debería ser para beneficiar lo más posible al ecosistema de criptomonedas en general, no solo a si misma.
