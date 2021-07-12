<div style="text-align:center;">
  <h3>Bitcoin: Un Sistema Distribuido de Dinero Electrónico</h3>
  <div style="margin: 32px 0 32px 0;line-height: 2.0px">
  <p>Satoshi Nakamoto</p>
  <p>satoshin@gmx.com</p>
  <p>www.bitcoin.org</p>
  </div>
</div>

<div style="font-size: 90%; text-align: justify; width: 80%; margin: auto;">
<b>Resumen</b>. Una versión de dinero electrónico basada totalmente en la cooperación entre participantes permitiría que los pagos en linea se
envíen directamente de un participante a otro sin pasar por una institución financiera.
Las firmas digitales representan parte de la solución, pero los beneficios principales
se pierden si se require una tercera persona para evitar el doble gasto. Proponemos una solución
al problema del doble gasto usando una red entre pares. La red marca cronológicamente las transacciones por medio de un hash que se incluye en una cadena
sucesiva de prueba de trabajo basado en hashes, creando un registro que no se puede modificar
sin rehacer la prueba de trabajo. La cadena más larga no solo sirve como prueba de la secuencia de los eventos ocurridos, sino también como prueba de su proveniencia del grupo con mayor poder de CPU.
Siempre que la mayoría del poder de CPU sea controlada por nodos que no estén cooperando para atacar la red, éstos generarán la cadena más larga y sobrepasarán a los atacantes.
La red misma requiere una estructuración mínima. Los mensajes se transmiten con base en el mejor esfuerzo y los nodos pueden abandonar y luego regresar a la red
a voluntad aceptando la cadena de prueba de trabajo más larga como prueba de lo sucedido
mientras estaban ausentes.
</div>

## 1. Introducción

El comercio en linea ha llegado a depender casi exclusivamente de las instituciones financieras que funcionan como intermediarios de confianza para
procesar pagos electrónicos. Si bien el sistema funciona lo suficientemente bien para la mayoría de las transacciones, todavía sufre de las debilidades
inherentes del modelo basado en la confianza. Las transacciones completamente irreversibles no son realmente posibles ya que las instituciones financieras no pueden
evitar inmiscuirse en la resolución de las disputas. El costo de la concialiación aumenta los costos de la transacción, lo que limita el tamaño práctico mínimo de la transacción, eliminando la posibilidad de transacciones pequeñas ocasionales y pagos irreversibles para servicios irreversibles.
Al ser posible revertir una transacción, se aumenta la necesidad de mutua confianza. Los comerciantes deben desconfiar de sus clientes exigiéndoles más información de lo que realmente se necesita. Cierto porcentaje de fraude es aceptado como algo inevitable. Estas incertidumbres de costos y pagos
se pueden evitar personalmente usando dinero en efectivo; sin embargo no hay un mecanismo para hacer pagos a través de un canal de comunicación sin un tercero de confianza.
Lo que se necesita es un sistema de pago electrónico basado en una prueba criptográfica en vez de una fiducia, lo que permitiría a dos partes cualesquieras hacer una transacción directamente entre sí sin la necesidad de una fiducia.
Las transacciones que son computacionalmente imprácticas de revertir protegerían a los vendedores del fraude y se podrían implementar mecanismos rutinarios de garantía para proteger a los compradores.
En este documento se propone una solución al problema del gasto doble usando un servidor distribuido que registra el tiempo preciso para generar una prueba computacional del orden cronológico de las transacciones. El sistema es seguro siempre y cuando
los nodos controlen colectivamente más poder de CPU que cualquier grupo de atacantes que cooperen juntos.

## 2. Las Transacciones

Definimos una moneda electrónica como una cadena de firmas electrónicas.
Cada poseedor transfiere la moneda a la siguiente persona firmando digitalmente un hash de la transacción previa y la clave pública del siguiente dueño y agregando éstos al final de la moneda.
El destinatario puede verificar las firmas para comprobar la cadena de propiedad.

![](/images/transactions.svg)

El problema por supuesto es que el destinatario no puede verificar si uno de los dueños usó la moneda dos veces.
Una solución común es incluir una autoridad central confiable, o emisor de monedas, que revisa si hubo doble gasto en cada transacción.
Luego de cada transacción, la moneda debe ser regresada al emisor de monedas para emitir una nueva moneda y solo las monedas creadas directamente por el emisor se consideran que no fueron gastadas doblemente.
El problema con esta solución es que el destino de todo el sistema monetario depende de la compañía emisora y cada transacción debe pasar por allí tal como un banco.

Necesitamos una manera que el destinatario sepa que los dueños anteriores no firmaron transacciones previas. Para nuestros propósitos,
la transacción previa es la que cuenta, de modo que no nos preocupamos por intentos posteriores de gasto doble.
La única forma de confirmar la ausencia de una transacción es estar al tanto de todas las transacciones. En el modelo del emisor de moneda, el emisor estaba al tanto de todas las transacciones y decidía cual llegaba primero.
Para lograr esto sin un intermediario fiable, las transacciones deben ser anunciadas públicamente [1], y necesitamos un sistema de participantes para que esten de acuerdo en un solo historial
del orden en las que fueron recibidas. El beneficiario necesita la prueba de que en el momento de cada transacción, la mayoría de los nodos acordaron que esa fue la primera recibida.

## 3. El Servidor de Registro Cronológico

La solución que proponemos inicia con un servidor de registro cronológico. Un servidor de este tipo toma un hash de un bloque de
objetos para ser registrados cronológicamente y divulga el hash en medios como un periódico o una publicación de Usenet [2-5].
El registro cronológico prueba que los datos debieron existir en el momento, obviamente, para poder ser incluidos en el hash. Cada registro cronológico incluye el registro anterior en su propio hash formando así una cadena. Esto permite a cada registro adicional reforzar los anteriores.

![](/images/timestamp-server.svg)

## 4. La Prueba de Trabajo

Para implementar un servidor distribuido de registro cronológico  entre pares, necesitaremos usar un sistema de prueba de trabajo similar al HashCash de Adam Back [6] en vez de publicaciones en un periódico o Usenet.
La prueba de trabajo involucra la lectura de un valor que cuando se le aplica hash, por ejemplo, por medio de SHA-256, éste comienza con una serie de bits cero.
El promedio de trabajo necesario es exponencial a la cantidad de bits cero necesarias y se puede verificar ejecutando un hash simple.

Para nuestro servidor de registro cronológico, implementamos la prueba de trabajo aumentando el valor único (nonce) en el bloque hasta que se encuentra un valor que le da al hash del bloque los bits cero requeridos.
Una vez el esfuerzo CPU se ha utilizado por completo para satisfacer la prueba de trabajo, el bloque no se puede modificar sin rehacer el trabajo. A medida que bloques posteriores se encadenan, el trabajo para modificar el bloque incluiría rehacer todos los bloques siguientes.

![](/images/proof-of-work.svg)

La prueba de trabajo también soluciona el problema de determinar la representación en la toma de decisiones por la mayoría. Si la mayoría se basara en una dirección IP - un voto, podría corromperse por cualquiera capaz de asignarse muchas direcciones IPs.
La prueba de trabajo es básicamente un CPU - un voto. La decisión mayoritaria se representa con la cadena más larga que a su vez tiene la mayor prueba de trabajo aplicada. Si una mayoría de poder CPU es controlada por nodos honestos, la cadena honesta va a crecer más rápidamente y va a sobrepasar cualquier cadena rival.
Para modificar un bloque previo, un atacante tendría que rehacer la prueba de trabajo del bloque y de los bloques posteriores y luego alcanzar y superar el trabajo de los nodos honestos.
Mostraremos más adelante que la probabilidad de que un atacante más lento  se ponga a la par disminuye exponencialmente a medida que se agregan bloques adicionales.

Para compensar el incremento de la velocidad del hardware y el interés variable de ejecutar nodos a lo largo del tiempo, la dificultad de la prueba de trabajo se determina por un promedio variable
que apunta a un número promedio de bloques por hora. Si se generan muy rápido, la dificultad se incrementa.

## 5. La Red

Los pasos para iniciar la red son los siguientes:

1. Se transmiten las nuevas transacciones a todos los nodos
2. Cada nodo alamacena las nuevas transacciones en un bloque.
3. Cada nodo trabaja en hallar una prueba de trabajo difícil para su bloque.
4. Cuando un nodo halla una prueba de trabajo, transmite el bloque a todos los nodos.
5. Los nodos aceptan el bloque sólo si todas las transacciones en él son válidas o no han sido gastadas.
6. Los nodos expresan su asentimiento del bloque comenzando a trabajar en la creación del siguiente bloque en la cadena, usando el hash del bloque aceptado como el hash previo.


Los nodos siempren asumen que la cadena más larga es la correcta y se van a segurir trabajando para extenderlo.
Si dos nodos transmiten diferentes versiones de bloque siguiente simultáneamente, algunos nodos pueden recibir una u otra primero. En ese caso, ellos trabajan en la primera versión que recibieron, pero guardan la otra ramificación
en el caso de que ésta se convierta en la más larga. El empate se resuelve cuando se encuentre la siguiente prueba de trabajo y una de las ramificaciones sea más larga; los nodos que estaban trabajando en la otra ramificación ahora se pasan a la más larga.

Las nuevas transmisiones de transacciones no tienen que llegar necesariamente a todos los nodos. Siempre que alcancen muchos nodos ellos van a ser incluidos en un bloque tarde o temprano. Las transmisiones de los bloques también son tolerantes a los mensajes no recibidos. Si un nodo no recibe un bloque, lo va a solicitar cuando reciba el siguiente bloque y se de cuenta que le faltaba uno.

## 6. Los Incentivos

Por convención, la primera transacción es una transacción especial que genera una moneda que le pertenece al creador del bloque. Esto crea un incentivo para que los nodos respalden la red y establece una manera de poner monedas inicialmente en circulación ya que no hay una autoridad central que las emita.
La adición continua de una cantidad constante de monedas es análogo a la minería de oro en la que gastan recursos para poner el oro en circulación. En nuestro caso es el tiempo de CPU y la electricidad lo que se gasta.

El incentivo también pude ser financiado a través de comisiones de transacción. Si el valor de salida de una transacción es menor que su valor de entrada, la diferencia es una comisión de transacción que se agrega al valor de incentivo del bloque que contiene la transacción.
Una vez que un número predeterminado de monedas han entrado en circulación, el sistema de incentivos puede entrar en una etapa de transición basado en comisiones de transacción siendo completamente libre de inflación.  

El incentivo puede ayudar a estimular a los nodos para que permanezcan honestos. Si un atacante codicioso es capaz de juntar más poder de CPU que todo el resto
de los nodos, le tocará escoger entre usar este poder para defraudar a la gente robándole sus pagos o usar este poder para generar nuevas monedas. Él debería encontrar
más provechoso seguir las reglas que lo favorecen con más nuevas monedas que todos los demás en la red, que subvertir el sistema y la validez de su propia fortuna.

## 7. Reducción de Espacio de Memoria

Una vez la más reciente transacción de una moneda se engrana bajo suficientes bloques, las transacciones completadas anteriormente se pueden descartar para ahorrar espacio de disco.
Para facilitar esto sin romper el hash del bloque, se aplica un hash a las transacciones en un Árbol de Merkle [7][2][5], incluyendo sólo la raíz en el hash del bloque.
Los bloques anteriores pueden luego ser compactados podando las ramificacións del árbol. Los hash interiores no tienen que ser almacenados.

![](/images/reclaiming-disk-space.svg)

Una cabecera de bloque sin transacciones tendría alrededor de 80 bytes. Si suponemos que los bloques se generan cada 10 minutos, 80 bytes * 6 * 24 * 365 = 4.2MB por año.
Con sistemas de computación que se venden con 2GB de RAM en 2008 y la ley de Moore que predice un crecimiento actual de 1.2GB por año, el almacenamiento no debería ser un problema incluso si las cabeceras de los bloques deben permanecer en la memoria.

## 8. Verificación Simplificada de Pago

Es posible verificar los pagos sin tener que correr un nodo de red completo. Un usuario solo necesita mantener una copia de las cabeceras del bloque de la cadena de prueba de trabajo más larga, que puede obtener
consultando los nodos de la red hasta convencerse de tener la cadena más extensa y obtener la ramificación de Merkle que relaciona la transacción con el bloque donde está registrada cronológicamente. No puede revisar la transacción él mismo sino que al relacionarla con un lugar en la cadena pude ver que un nodo de la red la ha aceptado y los bloques agregadoe posteriormente sólo confirman esta aceptación por parte de la red.

![](/images/simplified-payment-verification.svg)

La verificación como tal es confiable siempre y cuando los nodos honestos controlen la red pero es más vulnerable si la red es sobrepasada por un atacante.
Si bien los nodos de red pueden verificar las transacciones por si solos, el método simplificado puede ser burlado por transacciones falsas de un atacante mientras éste continue dominando la red. Una estrategia para protegerse de este caso sería aceptar alertas de los nodos cuando detecten un bloque inválido anunciando al software del usuario para que descargue el bloque completo con las transacciones sospechosas para confirmar la inconsistencia.
Los negocios que reciben pagos frecuentes probablemente querrán correr sus propios nodos para una mayor seguridad y una rápida verificación.

## 9. Combinando y Dividiendo el Valor

Aunque sería posible manejar monedas en forma individual, sería insostenible hacer una transacción separada por cada centavo en una transferencia. Para permitir la división y combinación del valor, las transacciones contienen múltiples entradas y salidas.
Normalmente habrá o bien una sola entrada de una transacción previa grande o múltiples entradas que combinan montos más pequeños y además como mucho dos salidas: una para el pago y una devolviendo el cambio, si lo hay, de nuevo al remitente.

![](/images/combining-splitting-value.svg)

Debe tenerse en cuenta que la expansión, es decir, donde una transacción depende de varias transacciones y éstas dependen de muchas más, no es un problema aquí.
No existe nunca la necesidad de extraer la copia completa del historial de una transacción individual. 

## 10. Privacidad

El modelo tradicional de banca alcanza un nivel de privacidad al limitar el acceso a la información a las partes involucradas y al intermediario de confianza.
La necesidad de anunciar todas las tranzacciones al público imposibilita este método, pero la privacidad aún se puede mantener bloquenado el flujo de información en otro lugar: manteniendo las claves privadas anónimas.
El público puede ver que alguien envia una cantidad a alguien más pero sin relacionar la transacción con nadie.
Esto es similar al nivel de información divulgado por las bolsas de valores donde se anuncia la hora y el tamaño de los movimientos individuales, el "registro",  pero sin decir quienes fueron las partes.

![](/images/privacy.svg)

Como cortafuegos adicional, debe usarse un nuevo par de claves para cada transacción para evitar que sean relacionadas con un dueño común. Alguna forma de vinculación es todavía inevitable con las transacciones de entradas múltiples que necesariamente revelan que las entradas eran poseidas por el mismo dueño. El riesgo es que si el dueño de una clave es revelado, el vínculo puede revelar otras transacciones que pertenecieron al mismo dueño.

## 11. Cálculos

Consideramos el escenario de un atacante tratando de generar una cadena alterna más rápido que la cadena honesta. Incluso si esto se logra alcanzar, no implica que el sistema sea propenso a cambios arbitrarios tales como la creación de valor a partir de nada o la posesión de dinero que nunca perteneció al atacante. Los nodos no van a aceptar una transacción inválida como pago y los nodos honestos nunca aceptarán un bloque que los contiene.
Un atacante solo puede tratar de cambiar una de sus propias transacciones para recuperar dinero que gastó antes.

La competencia entre la cadena honesta y la cadena del atacante se puede caracterizar como un Camino Aleatorio Binomial. El evento éxito es que la cadena honesta se extienda un bloque aumentando su ventaja +1, y el evento fracaso es la cadena del atacante extendiéndose un bloque reduciendo la brecha por -1.

La probabilidad de que un atacante se ponga al día a partir de un deficit dado es análogo al problema de la Ruina del Apostador.
Suponga que un apostador con crédito ilimitado comienza con un deficit y juega potencialmente un número infinito de intentos para tratar de alcanzar una paridad. podemos calcular la probabilidad de que alguna vez alcance la paridad
o que en nuestro caso el atacante alcance la cadena honesta de la siguiente forma[8]:

                p =  probabilidad de que un nodo honesto encuentre el próximo bloque
                q = probabilidad de que el atacante encuentre el próximo bloque
                qz = probabilidad de que el atacante alguna vez alcance a los demás a partir de z bloques atrás
                
![probability](/images/equation1.png)

Dada la suposición de que  p > q, la probabilidad disminuye exponencialmente a medida que el número de bloques que el atacante debe alcanzar aumenta.

Ahora consideramos cuanto tiempo el destinatario de una nueva transacción debe esperar antes de estar lo suficientemente seguro que el remitente no pueda cambiar la transacción. Asumimos que el remitente es un atacante
que quiere hacer que el destinatario esté convencido por un tiempo de que se le hizo el pago, luego hacer el cambio para pagarse a sí mismo más tarde. El destinatario será alertado cuando eso pase, pero el remitente va a tener la esperanza de que sea demasiado tarde.

El destinatario genera un nuevo par de claves y da la clave pública al remitente justo antes de firmar. Esto evita que el remitente prepare una cadena de bloques con anticipación y procesándolo continuamente hasta que es lo bastante afortunado para avanzar lo suficiente y realizar la transacción en ese momento.
Una vez la transacción es enviada, el remitente deshonesto comienza a trabajar en secreto en una cadena paralela que contiene una versión alterna de la transacción.

El destinatario espera hasta que la transacción ha sido añadida a un bloque y $$ z $$ bloques han sido enlazados luego de ese. Él no sabe la cantidad exacta de progreso que el atacante ha logrado, pero asumiendo que los nodos honestos invirtieron el promedio esperado de tiempo
por bloque, el progreso potencial del atacante será una distribución de Poisson con el valor esperado:

$$
\large \lambda = z \frac qp
$$

Para obtener la probabilidad de un que un atacante pueda ponerse a la par de los demás, multiplicamos la densidad de Poisson para cada nivel de progreso que haya podido alcanzar por la probabilidad de que pueda ponerse al día a partir de ese punto:

$$
\large \sum_{k=0}^{\infty} \frac{\lambda^k e^{-\lambda}}{k!} \cdot
				\begin{Bmatrix}
				(q/p)^{(z-k)} & \textit{if}\;k\leq z\\
				1 & \textit{if} \; k > z
				\end{Bmatrix}
$$

Reordenando para evitar sumar la cola infinita de la distribución...



Convirtiendo a código C...

```
#include
double ProbabilidadDeExitoDeUnAtacante(double q, int z)
{
	double p = 1.0 - q;
	double lambda = z * (q / p);
	double sum = 1.0;
	int i, k;
	for (k = 0; k <= z; k++)
	{
		double poisson = exp(-lambda);
		for (i = 1; i <= k; i++)
			poisson *= lambda / i;
		sum -= poisson * (1 - pow(q / p, z - k));
	}
	return sum;
}
```

Al ejecutar algunos resultados podemos ver como la probabilidad disminuye exponencialmente con z.

```
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
```

Resolviendo para P menor que 0.1%...

```
P < 0.001
q=0.10   z=5
q=0.15   z=8
q=0.20   z=11
q=0.25   z=15
q=0.30   z=24
q=0.35   z=41
q=0.40   z=89
q=0.45   z=340
```

## 12. Conclusión

Hemos propuesto un sistema de transacciones electrónicas que no dependen de la buena fe. Comenzamos con el marco usual de monedas hechas a partir de firmas digitales que brindan un fuerte control de propiedad pero es incompleto sin una forma de prevenir el doble gasto.
Para resolver esto, propusimos una red entre pares que usa la prueba de trabajo para guardar un historial público de transacciones que rápidamente terminan siendo computacionalmente imprácticas de cambiar por parte de un atacante si los nodos honestos controlan la mayoría de poder de CPU. La red es robusta en su simplicidad no estructurada.
Los nodos trabajan todos a la vez con poca coordinación. No necesitan ser identificados ya que los mensajes no son enrutados a un lugar particular y solo deben ser transmitidos con base en el mejor esfuerzo. Los nodos pueden abandonar y retomar la red a voluntad aceptando la cadena de prueba de trabajo como prueba de lo que pasó cuando estaban ausentes. Ellos votan con su poder de CPU
expresando su aprobación de los bloques válidos al ponerse a trabajar en extenderlos y rechazando a su vez los bloques inválidos al negarse a procesarlos. Cualquier regla o incentivo puede se puede hacer cumplir con este mecanismo de consenso.

## Referencias

[1] W. Dai, "b-money," http://www.weidai.com/bmoney.txt, 1998.  
[2] H. Massias, X.S. Avila, and J.-J. Quisquater, "Design of a secure timestamping service with minimal trust requirements," In *20th Symposium on Information Theory in the Benelux*, May 1999.  
[3] S. Haber, W.S. Stornetta, "How to time-stamp a digital document," In *Journal of Cryptology*, vol 3, no 2, pages 99-111, 1991.  
[4] D. Bayer, S. Haber, W.S. Stornetta, "Improving the efficiency and reliability of digital time-stamping," In *Sequences II: Methods in Communication, Security and Computer Science*, pages 329-334, 1993.  
[5] S. Haber, W.S. Stornetta, "Secure names for bit-strings," In *Proceedings of the 4th ACM Conference on Computer and Communications Security*, pages 28-35, April 1997.  
[6] A. Back, "Hashcash - a denial of service counter-measure,"  
http://www.hashcash.org/papers/hashcash.pdf, 2002.  
[7] R.C. Merkle, "Protocols for public key cryptosystems," In *Proc. 1980 Symposium on Security and Privacy*, IEEE Computer Society, pages 122-133, April 1980.  
[8] W. Feller, "An introduction to probability theory and its applications," 1957.
