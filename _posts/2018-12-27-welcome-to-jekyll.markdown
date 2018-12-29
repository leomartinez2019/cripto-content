---
layout: post
title:  "Documentación Técnica de Bitcoin"
date:   2018-12-27 11:56:53 -0500
categories: bitcoin
mathjax: true
---


<div style="text-align:center; line-height: 0.5">
  <h3>Bitcoin: Un Sistema Distribuido de Dinero Electrónico</h3>
  <div style="margin: 32px 0 32px 0;">
  <p>Satoshi Nakamoto</p>
  <p>satoshin@gmx.com</p>
  <p>www.bitcoin.org</p>
  </div>
</div>

<div style="font-size: 90%; text-align: justify; width: 80%; margin: auto;">
<b>Abstract</b>. Una versión de dinero electrónico basada totalmente en la cooperación entre pares, permitiría que los pagos en linea se
envíen directamente de un par a otro sin pasar por una institución financiera.
Las firmas digitales representan parte de la solución, pero los beneficios principales
se pierden si se require una tercera persona para evitar el doble gasto. Proponemos una solución
al problema del doble gasto usando una red entre pares. La red marca la fecha de las transacciones por medio de un hash que se incluye en una cadena
sucesiva de prueba de trabajo basado en hashes, creando un registro que no se puede modificar
sin rehacer la prueba de trabajo. La cadena más larga no solo sirve como prueba de la secuencia de los eventos ocurridos, sino también como prueba de su proveniencia del grupo con mayor poder de CPU.
Mientras la mayoría del poder de CPU se controle por nodos que no estén cooperando para atacar la red, éstos generarán la cadena más larga y sobrepasarán a los atacantes.
La red misma requiere una estructuración mínima. Los mensajes se transmiten con base en el mejor esfuerzo y los nodos pueden abandonar y luego regresar a la red
a voluntad aceptando la cadena de prueba de trabajo más larga como prueba de lo sucedido
mientras estaban ausentes.
</div>

## 1. Introducción

El comercio en linea ha llegado a depender casi exclusivamente de las instituciones financieras que funcionan como intermediarios de confianza para
procesar pagos electrónicos. Si bien el sistema funciona lo suficientemente bien para la mayoría de las transacciones, todavía sufre de las debilidades
inherentes del modelo basado en la fiducia. Las transacciones completamente irreversibles no son realmente posibles ya que las instituciones financieras no pueden
evitar inmiscuirse en la resolución de las disputas. El costo de la concialiación aumenta los costos de la transacción, lo que limita el tamaño práctico mínimo de la transacción, eliminando la posibilidad de transacciones pequeñas ocasionales y pagos irreversibles para servicios irreversibles.
Al ser posible revertir una transacción, se aumenta la necesidad de mutua confianza. Los comerciantes deben desconfiar de sus clientes exigiéndoles más información de lo que realmente se necesita. Aquí se acepta cierto porcentaje de fraude como algo inevitable. Estas incertidumbres de costos y pagos
se pueden evitar personalmente usando moneda física, sin embargo no hay un mecanismo para hacer pagos a través de un canal de comunicación sin un tercero de confianza.
Lo que se necesita es un sistema de pago electrónico basado en una prueba criptográfica en vez de una fiducia, lo que permitiría a dos partes cualquieras hacer una transacción directamente entre sí sin la necesidad de una fiducia.
Las transacciones que son computacionalmente imprácticas de revertir protegerían a los vendedores del fraude y se podrían implementar mecanismos rutinarios de garantía para proteger a los compradores.
En este documento se propone una solución al problema del gasto doble usando un servidor distribuido que registra el tiempo preciso para generar una prueba computacional del orden cronológico de las transacciones. El sistema es seguro siempre y cuando
los nodos controlen colectivamente más poder de CPU que cualquier grupo de atacantes que cooperen juntos.

## 2. Las Transacciones

Definimos una moneda electrónica como una cadena de firmas electrónicas.
Cada poseedor transfiere la moneda a la siguiente persona por medio de una firma digital de un hash de la transacción previa y la clave pública del siguiente poseedor y agregando éstos al final de la moneda.
Un beneficiario puede verificar las firmas para comprobar la cadena de propiedad.

![](/images/transactions.svg)

El problema por supuesto es que el beneficiario no puede verificar que uno de los poseedores no gastó doblemente la moneda.
Una solución común es incluir una autoridad central confiable, o casa de moneda, que revisa el gasto duplicado en cada transacción.
Luego de cada transacción, la moneda debe ser regresada a la casa de la moneda para emitir una nueva moneda y solo las monedas emitidas directamente por la casa de moneda se confia que no son gastadas doblemente.
El problema con esta solución es que el destino de todo el sistema monetario dependa de la compañía emisora y cada transacción debe pasar por allí tal como un banco.

Necesitamos una forma que el beneficiario sepa que los dueños anteriores no firmaron transacciones anteriores. Para nuestros propósitos,
la transacción previa es la que cuenta, de modo que no nos preocupemos por intentos posteriores de gasto doble.
La única forma de confirmar la ausencia de una transacción es estar al tanto de todas las transacciones. En el modelo del emisor de moneda, el emisor estaba al tanto de todas las transacciones y decidía cual llegaba primero.
Para lograr esto sin un intermediario fiable, las transacciones deben ser anunciadas públicamente [1], y necesitamos un sistema de participantes para que esten de acuerdo en una sola historia
del orden en las que fueron recibidas. El beneficiario necesita la prueba de que en el momento de cada transacción, la mayoría de los nodos acordaron que esa fue la primera que recibieron.

## 3. El Servidor de Registro Cronológico

La solución que proponemos inicia con un servidor de registro cronológico. Un servidor de este tipo toma un hash de un bloque de
objetos para ser registrados cronológicamente y publica el hash de forma amplia, como en un periódico o una publicación de Usenet [2-5].
El registro cronológico prueba que los datos debieron existir en el momento, obviamente, para poder ser incluidos en el hash. Cada registro cronológico incluye el registro anterior en su propio hash formando así una cadena, lo que permite a cada registro adicional reforzar los anteriores.

![](/images/timestamp-server.svg)

## 4. La Prueba de Trabajo

Para implementar un servidor distribuido de registro cronológico  entre pares, necesitaremos usar un siatema de prueba de trabajo similar al HashCash de Adam Back [] en vez de publicaciones en un periódico o Usenet.
La prueba de trabajo involucra la lectura de un valor que cuando se hashea, por ejemplo, por medio de SHA-256, el hash comienza con una serie de bits cero.
El promedio de trabajo necesario es exponencial a la cantidad de bits cero necesarias y se puede verificar ejecutando un hash simple.

Para nuestro servidor de registro cronológico, implementamos la prueba de trabajo aumentando el valor único (nonce) en el bloque hasta que se encuentra un valor que le da al hash del bloque los bits cero requeridos.
Una vez el esfuerzo CPU se ha utilizado por completo para satisfacer la prueba de trabajo, el bloque no se puede modificar sin rehacer el trabajo. A medida que bloques posteriores se encadenan, el trabajo para modificar el bloque incluiría rehacer todos los bloques siguientes.

![](/images/proof-of-work.svg)

La prueba de trabajo también soluciona el problema de determinar la representación en la toma de decisiones por la mayoría. Si la mayoría se basara en una dirección IP - un voto, podría corromperse por cualquiera capaz de asignarse muchas IPs.
La prueba de trabajo es básicamente un CPU - un voto. La decisión mayoritaria se representa con la cadena más larga que a su vez tiene la mayor prueba de trabajo aplicada. Si una mayoría de poder CPU es controlada por nodos honestos, la cadena honesta va a crecer más rápidamente y va a sobrepasar cualquier cadena rival.
Para modificar un bloque previo, un atacante tendría que rehacer la prueba de trabajo del bloque y de los bloques posteriores y luego alcanzar y superar el trabajo de los nodos honestos.
Mostraremos más adelante que la probabilidad de que un atacante más lento  se ponga a la par disminuye exponencialmente a medida que se agregan bloques adicionales.

Para compensar el incremento de la velocidad del hardware y el interés variable de ejecutar nodos a lo largo del tiempo, la dificultad de la prueba de trabajo se determina por un promedio variable
que apunta a un número promedio de bloques por hora. Si se generan muy rápido, la dificultad se incrementa.

## 5. La Red

The steps to run the network are as follows:

1. New transactions are broadcast to all nodes.
2. Each node collects new transactions into a block.
3. Each node works on finding a difficult proof-of-work for its block.
4. When a node finds a proof-of-work, it broadcasts the block to all nodes.
5. Nodes accept the block only if all transactions in it are valid and not already spent.
6. Nodes express their acceptance of the block by working on creating the next block in the chain, using the hash of the accepted block as the previous hash.

Nodes always consider the longest chain to be the correct one and will keep working on extending it.
If two nodes broadcast different versions of the next block simultaneously, some nodes may receive one or the other first.
In that case, they work on the first one they received, but save the other branch in case it becomes longer.
The tie will be broken when the next proof-of-work is found and one branch becomes longer; the nodes that were working on the other branch will then switch to the longer one.

New transaction broadcasts do not necessarily need to reach all nodes.
As long as they reach many nodes, they will get into a block before long.
Block broadcasts are also tolerant of dropped messages.
If a node does not receive a block, it will request it when it receives the next block and realizes it missed one.

## 6. Los Incentivos

By convention, the first transaction in a block is a special transaction that starts a new coin owned by the creator of the block.
This adds an incentive for nodes to support the network, and provides a way to initially distribute coins into circulation, since there is no central authority to issue them.
The steady addition of a constant of amount of new coins is analogous to gold miners expending resources to add gold to circulation.
In our case, it is CPU time and electricity that is expended.

The incentive can also be funded with transaction fees.
If the output value of a transaction is less than its input value, the difference is a transaction fee that is added to the incentive value of the block containing the transaction.
Once a predetermined number of coins have entered circulation, the incentive can transition entirely to transaction fees and be completely inflation free.

The incentive may help encourage nodes to stay honest.
If a greedy attacker is able to assemble more CPU power than all the honest nodes, he would have to choose between using it to defraud people by stealing back his payments, or using it to generate new coins.
He ought to find it more profitable to play by the rules, such rules that favour him with more new coins than everyone else combined, than to undermine the system and the validity of his own wealth.

## 7. Reducción de Espacio de Memoria

Once the latest transaction in a coin is buried under enough blocks, the spent transactions before it can be discarded to save disk space.
To facilitate this without breaking the block's hash, transactions are hashed in a Merkle Tree [7][2][5], with only the root included in the block's hash.
Old blocks can then be compacted by stubbing off branches of the tree.
The interior hashes do not need to be stored.

![](/images/reclaiming-disk-space.svg)

A block header with no transactions would be about 80 bytes.
If we suppose blocks are generated every 10 minutes, 80 bytes * 6 * 24 * 365 = 4.2MB per year.
With computer systems typically selling with 2GB of RAM as of 2008, and Moore's Law predicting current growth of 1.2GB per year, storage should not be a problem even if the block headers must be kept in memory.

## 8. Verificación Simplificada de Pago

It is possible to verify payments without running a full network node.
A user only needs to keep a copy of the block headers of the longest proof-of-work chain, which he can get by querying network nodes until he's convinced he has the longest chain, and obtain the Merkle branch linking the transaction to the block it's timestamped in.
He can't check the transaction for himself, but by linking it to a place in the chain, he can see that a network node has accepted it, and blocks added after it further confirm the network has accepted it.

![](/images/simplified-payment-verification.svg)

As such, the verification is reliable as long as honest nodes control the network, but is more vulnerable if the network is overpowered by an attacker.
While network nodes can verify transactions for themselves, the simplified method can be fooled by an attacker's fabricated transactions for as long as the attacker can continue to overpower the network.
One strategy to protect against this would be to accept alerts from network nodes when they detect an invalid block, prompting the user's software to download the full block and alerted transactions to confirm the inconsistency.
Businesses that receive frequent payments will probably still want to run their own nodes for more independent security and quicker verification.

## 9. Combinando y Dividiendo el Valor

Although it would be possible to handle coins individually, it would be unwieldy to make a separate transaction for every cent in a transfer.
To allow value to be split and combined, transactions contain multiple inputs and outputs.
Normally there will be either a single input from a larger previous transaction or multiple inputs combining smaller amounts, and at most two outputs: one for the payment, and one returning the change, if any, back to the sender.

![](/images/combining-splitting-value.svg)

It should be noted that fan-out, where a transaction depends on several transactions, and those transactions depend on many more, is not a problem here.
There is never the need to extract a complete standalone copy of a transaction's history.

## 10. Privacidad

The traditional banking model achieves a level of privacy by limiting access to information to the parties involved and the trusted third party.
The necessity to announce all transactions publicly precludes this method, but privacy can still be maintained by breaking the flow of information in another place: by keeping public keys anonymous.
The public can see that someone is sending an amount to someone else, but without information linking the transaction to anyone.
This is similar to the level of information released by stock exchanges, where the time and size of individual trades, the "tape", is made public, but without telling who the parties were.

![](/images/privacy.svg)

As an additional firewall, a new key pair should be used for each transaction to keep them from being linked to a common owner.
Some linking is still unavoidable with multi-input transactions, which necessarily reveal that their inputs were owned by the same owner.
The risk is that if the owner of a key is revealed, linking could reveal other transactions that belonged to the same owner.

## 11. Cálculos

We consider the scenario of an attacker trying to generate an alternate chain faster than the honest chain.
Even if this is accomplished, it does not throw the system open to arbitrary changes, such as creating value out of thin air or taking money that never belonged to the attacker.
Nodes are not going to accept an invalid transaction as payment, and honest nodes will never accept a block containing them.
An attacker can only try to change one of his own transactions to take back money he recently spent.

The race between the honest chain and an attacker chain can be characterized as a Binomial Random Walk.
The success event is the honest chain being extended by one block, increasing its lead by +1, and the failure event is the attacker's chain being extended by one block, reducing the gap by -1.

The probability of an attacker catching up from a given deficit is analogous to a Gambler's Ruin problem.
Suppose a gambler with unlimited credit starts at a deficit and plays potentially an infinite number of trials to try to reach breakeven.
We can calculate the probability he ever reaches breakeven, or that an attacker ever catches up with the honest chain, as follows [8]:

$$
\begin{eqnarray*}
                \large p &=& \text{ probability an honest node finds the next block}\\
                \large q &=& \text{ probability the attacker finds the next block}\\
                \large q_z &=& \text{ probability the attacker will ever catch up from $z$ blocks behind}
                \end{eqnarray*}
$$

$$
\large q_z = \begin{Bmatrix}
				1 & \textit{if}\; p \leq q\\
				(q/p)^z & \textit{if}\; p > q
				\end{Bmatrix}
$$

Given our assumption that $p \gt q$, the probability drops exponentially as the number of blocks the attacker has to catch up with increases.
With the odds against him, if he doesn't make a lucky lunge forward early on, his chances become vanishingly small as he falls further behind.

We now consider how long the recipient of a new transaction needs to wait before being sufficiently certain the sender can't change the transaction.
We assume the sender is an attacker who wants to make the recipient believe he paid him for a while, then switch it to pay back to himself after some time has passed.
The receiver will be alerted when that happens, but the sender hopes it will be too late.

The receiver generates a new key pair and gives the public key to the sender shortly before signing.
This prevents the sender from preparing a chain of blocks ahead of time by working on it continuously until he is lucky enough to get far enough ahead, then executing the transaction at that moment.
Once the transaction is sent, the dishonest sender starts working in secret on a parallel chain containing an alternate version of his transaction.

The recipient waits until the transaction has been added to a block and $$ z $$ blocks have been linked after it.
He doesn't know the exact amount of progress the attacker has made, but assuming the honest blocks took the average expected time per block, the attacker's potential progress will be a Poisson distribution with expected value:

$$
\large \lambda = z \frac qp
$$

To get the probability the attacker could still catch up now, we multiply the Poisson density for each amount of progress he could have made by the probability he could catch up from that point:

$$
\large \sum_{k=0}^{\infty} \frac{\lambda^k e^{-\lambda}}{k!} \cdot
				\begin{Bmatrix}
				(q/p)^{(z-k)} & \textit{if}\;k\leq z\\
				1 & \textit{if} \; k > z
				\end{Bmatrix}
$$

Rearranging to avoid summing the infinite tail of the distribution...

$$
\large 1 - \sum_{k=0}^{z} \frac{\lambda^k e^{-\lambda}}{k!}
				\left ( 1-(q/p)^{(z-k)} \right )
$$

Converting to C code...

{% highlight c %}
#include
double AttackerSuccessProbability(double q, int z)
{
	double p = 1.0 - q;
	double lambda = z * (q / p);
	double sum = 1.0;
	int i, k;
v	for (k = 0; k <= z; k++)
	{
		double poisson = exp(-lambda);
		for (i = 1; i <= k; i++)
			poisson *= lambda / i;
		sum -= poisson * (1 - pow(q / p, z - k));
	}
	return sum;
}
{% endhighlight %}

Running some results, we can see the probability drop off exponentially with $$ z $$.

{% highlight c %}
q=0.1
z=0    P=1.0000000
z=1    P=0.2045873
z=2    P=0.0509779
z=3    P=0.0131722
z=4    P=0.0034552  
z=5    P=0.0009137
z=6    P=0.0002428
z=7    P=0.0000647
z=8    P=0.0000173
z=9    P=0.0000046
z=10   P=0.0000012

q=0.3
z=0    P=1.0000000
z=5    P=0.1773523
z=10   P=0.0416605
z=15   P=0.0101008
z=20   P=0.0024804
z=25   P=0.0006132
z=30   P=0.0001522
z=35   P=0.0000379
z=40   P=0.0000095
z=45   P=0.0000024
z=50   P=0.0000006
{% endhighlight %}

Solving for P less than 0.1%...

{% highlight c %}
P < 0.001
q=0.10   z=5
q=0.15   z=8
q=0.20   z=11
q=0.25   z=15
q=0.30   z=24
q=0.35   z=41
q=0.40   z=89
q=0.45   z=340
{% endhighlight %}

## 12. Conclusión

We have proposed a system for electronic transactions without relying on trust.
We started with the usual framework of coins made from digital signatures, which provides strong control of ownership, but is incomplete without a way to prevent double-spending.
To solve this, we proposed a peer-to-peer network using proof-of-work to record a public history of transactions that quickly becomes computationally impractical for an attacker to change if honest nodes control a majority of CPU power.
The network is robust in its unstructured simplicity.
Nodes work all at once with little coordination.
They do not need to be identified, since messages are not routed to any particular place and only need to be delivered on a best effort basis.
Nodes can leave and rejoin the network at will, accepting the proof-of-work chain as proof of what happened while they were gone.
They vote with their CPU power, expressing their acceptance of valid blocks by working on extending them and rejecting invalid blocks by refusing to work on them.
Any needed rules and incentives can be enforced with this consensus mechanism.
