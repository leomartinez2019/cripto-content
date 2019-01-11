---
layout: post
title: Documentación Técnica de 0x
description: "Traducción del whitepaper de 0x"
categories: criptomonedas
---

<div style="text-align:center;">
  <h3>0x: Un protocolo para el intercambio descentralizado en la blockchain de Ethereum</h3>
  <div style="margin: 32px 0 32px 0; line-height: 2.0px">
  <p>Will Warren, Amir Bandealli</p>
  <p>0xProject.com</p>
  <p>Febrero 21, 2017</p>
  </div>
</div>

<div style="font-size: 90%; text-align: justify; width: 80%; margin: auto;">
<b>Resumen</b>. Describimos un protocolo que facilita un intercambio de baja fricción entre pares de tokens ERC20 en el blockchain de Ethereum. El protocolo tiene la intención de servir como un bloque de estándar abierto y de estructuración común que impulsa la interoperatividad entre las aplicaciones descentralizadas (dApps) que incorporan la funcionalidad del intercambio. Los intercambios se ejecutan a través de un sistema de contratos inteligentes de Ethereum que son accesibles públicamente, gratis de usar y a la que cualquier dApp se puede conectar. Las dApps creadas encima del protocolo pueden acceder a fondos públicos de liquidez y crear su propia liquidez y cobrar comisiones de transacción sobre los volúmenes resultantes. El protocolo es abierto: no impone costos a sus usuarios o extrae valor arbitrariamente de un grupo de usuarios para beneficiar a otro grupo. La gestión descentralizada se usa para integrar actualizaciones de forma continua y segura en el protocolo base sin perturbar las dApps  o los usuarios finales.
</div>

### 1. Introducción

Los blockchain han sido revolucionarios al permitir a cualquiera poseer y transferir activos a través de una red financiera abierta sin la necesidad de un tercero garante. Ahora que hay cientos de activos basados en blockchain, y cada mes se agregan más, la necesidad de intercambiar estos activos es creciente. Con la llegada de los contratos inteligentes, es posible para dos o más partes intercambiar activos blockchain sin la necesidad de un intermediario garante.

Un intercambio descentralizado es un avance importante en el ecosistema de intercambios centralizados por varias razones claves: los intercambios descentralizados puden brindar garantías más fuertes de seguridad a los usuarios finales ya que no existe un tercero que pueda ser hackeado, los fondeos de sus clientes robados, o ser sujetos de regulaciones gubernamentales. Los hacks de MT. Gox, Shapeshift y Bitfinex [2,3] han demostrado que estos tipos de riesgos sistemáticos son palpables. El intercambio descentralizado va a eliminar estos riesgos al permitir a los usuarios hacer transacciones sin intermediarios y al imponer la carga de la seguridad en los usuarios individuales en vez de un guardian solitario.

En los dos años que han pasado desde el bloque génesis de la blockchain de Ethereum, numerosas aplicaciones descentralizadas (dApps) han creado contratos inteligentes Ethereum para el intercambio entre pares. El desarrollo intempestivo y la falta de buenas prácticas han dejado a la blockchain dispersa con implementaciones patentadas y centradas en un solo aspecto. Como resultado, los usuarios finales están expuestos a numerosos contratos inteligentes de calidad y seguridad variable con procesos de configuración y curvas de aprendizaje únicos, todos aplicando la misma funcionalidad. Este enfoque impone costos innecesarios en la red al fragmentar a los usuarios finales de acuerdo a la dApp particular que cada usuario está usando, evitando aspectos valiosos de la red relacionados con la liquidez.