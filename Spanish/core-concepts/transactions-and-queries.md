---
description: Un resumen de las transacciones y consultas de la API de Hedera
---

# Transacciones y consultas

## Transacciones

Las **transacciones** son peticiones enviadas por un cliente a un nodo con la expectativa de que se sometan a la red para procesarlas al orden de consenso y a la aplicación posterior al estado. Cada transacción (por ejemplo, `TokenCreateTransaction()`) tiene una cuota de transacción asociada que compensa a la red Hedera por ese procesamiento y posterior mantenimiento en un estado de consenso. Los usuarios pueden establecer una cuota máxima de transacción por la cantidad que el usuario está dispuesto a gastar. Al usuario sólo se le cobra la cuota real de la transacción.

**ID de la transacción**

Cada transacción tiene un ID de transacción único. El ID de transacción se utiliza para lo siguiente:

- Obteniendo recibos, registros, pruebas de estado
- Internamente por la red para detectar cuando se envían transacciones duplicadas

El ID de la transacción se compone de usar la hora de inicio válida de la transacción y el ID de cuenta de la cuenta que está pagando la transacción. La hora de inicio válida de la transacción es la hora en que la transacción comienza a ser procesada en la red. La hora de inicio válida de la transacción puede establecerse a una fecha/hora futura. Un ID de transacción se ve algo como `0.0.9401@1598924675.82525000`donde `0.0.9401` es el ID de la cuenta de pagador de transacción y `1598924675.82525000` es el timestamp en `seconds.nanoseconds`.

Las transacciones tienen una duración válida de hasta 180 segundos y comienzan a la hora de inicio válida de la transacción. Esto significa que la transacción tiene hasta 180 segundos para ser aceptada por uno de los nodos de la red. Si la transacción no es aceptada en este intervalo de tiempo, la transacción caducará. La transacción tendrá que ser creada, firmada y enviada de nuevo.

Una **transacción** generalmente incluye lo siguiente:

- **Cuenta del nodo**: la cuenta del nodo al que se está enviando la transacción (por ejemplo, `0.0.3`)
- **ID de la transacción**: el identificador de una transacción tiene dos componentes, el ID de la cuenta de pago más la hora de inicio válida de la transacción
- **Tarifa de transacción**: la cuota máxima que la cuenta de pago está dispuesta a pagar por la transacción
- **Duración válida**: el número de segundos para los que el cliente desea que la transacción se considere válida, comenzando por la hora de inicio válida de la transacción
- **Memo**: una cadena de texto de hasta 100 bytes de datos (opcional)
- **Transacción**: tipo de solicitud, por ejemplo, una transferencia HBAR o una llamada inteligente al contrato
- **Firma**: como mínimo, la cuenta de pago firmará la transacción como autorización. Otras firmas también pueden estar presentes.

El ciclo de vida de una transacción en el ecosistema de Hedera comienza cuando un cliente crea una transacción. Una vez creada la transacción es firmada criptográficamente como mínimo por la cuenta que paga las comisiones asociadas a la transacción. Las firmas adicionales pueden ser requeridas dependiendo de las propiedades establecidas para la cuenta, el tema o el token. El cliente es capaz de sofocar la cuota máxima que está dispuesto a pagar por el procesamiento de la transacción y, para una operación de contrato inteligente, la cantidad máxima de gas. Una vez que las firmas requeridas se aplican a la transacción, el cliente envía la transacción a cualquier nodo de la red Hedera.

El nodo receptor valida (por ejemplo, confirma que la cuenta de pago tiene saldo suficiente para pagar la cuota) la transacción y, si la validación es exitosa, envía la transacción a la red Hedera para obtener consenso añadiendo la transacción a un evento y chispeando ese evento a otro nodo. Rápido, ese evento fluye a todos los demás nodos. La red recibe esta transacción de forma exponencialmente rápida a través del [chismes sobre el protocolo de chismes](hashgraph-consens→ algorithms/gossip-about-gossip.md). La marca de tiempo de consenso para un evento (y así las transacciones dentro) es calculada por cada nodo de forma independiente calculando el promedio de las veces que los nodos de la red recibieron ese evento. Puede encontrar más información sobre cómo se calcula el timestamp de consenso [here](https://docs.hedera.com/docs/hashgraph-overview#section-f.Utimestamps). El algoritmo hashgraph proporciona la finalidad del consenso. Una vez asignado una marca de tiempo de consenso, la transacción se aplica al estado de consenso en el orden determinado por la marca de tiempo de cada transacción. En ese punto, las comisiones para la transacción también se procesan. De esta manera, cada nodo de la red mantiene un estado de consenso porque todos aplican las mismas transacciones en el mismo orden. Cada nodo también crea y almacena temporalmente recibos y registros en soporte del cliente consultando posteriormente para el estado de una transacción.

### Transacciones anidadas

Una **transacción anidada** activa las transacciones posteriores después de ejecutar una transacción de alto nivel. La transacción de nivel superior que envía un usuario es una **transacción padre**. Cada transacción subsiguiente que la transacción padre activa como resultado de la ejecución de la transacción padre es una **transacción hijo**. Un ejemplo de una transacción anidada es cuando un usuario envía la transacción de alto nivel a un alias de cuenta que activa una cuenta para crear una transacción detrás de escenas. Esta relación de transacción padre/hijo también se observa con los contratos de Hedera interactuando con HTS precompile. Una transacción padre soporta hasta 999 transacciones hijas, ya que la plataforma reserva 1000 nanosegundos por cada transacción enviada por el usuario.

**ID de transacción**

Las transacciones padre e hijo comparten el ID de la cuenta de pagador y la transacción de fecha de inicio válida. Los IDs de transacción hijo tienen un valor **nunca** adicional que representa el orden en el que las transacciones hijas fueron ejecutadas. La transacción padre tiene un valor nonce de 0. El valor nonce de las transacciones hijas incrementa 1 por cada transacción hija ejecutada como resultado de la transacción padre.

ID de transacción padre: <mark style="color:red;">payerAccountId</mark>@<mark style="color:blue;">transactionValidStart</mark>

Child Transaction ID: <mark style="color:red;">payerAccountId</mark>@<mark style="color:blue;">transactionValidStart</mark>/<mark style="color:green;">nonce</mark>

Ejemplo:

- ID de transacción padre: <mark style="color:red;">0.0.2252</mark>@<mark style="color:blue;">1640119571.329880313</mark>
- Niño 1 ID de transacción: <mark style="color:red;">0.0.2252</mark>@<mark style="color:blue;">1640119571.329880313</mark>/<mark style="color:green;">1</mark>
- Niño 2 ID de transacción: <mark style="color:red;">0.0.2252</mark>@<mark style="color:blue;">1640119571.329880313</mark>/<mark style="color:green;">2</mark>

**Registros de transacción**

Los registros de transacción anidados se devuelven al solicitar el registro para la transacción padre y establecer el `setIncludeChildren(<value>)` como verdadero. Esto devuelve registros para todas las transacciones hijas asociadas con la transacción padre. Los registros de transacciones hijas incluyen la marca de tiempo del consenso padre y el ID de transacción hija.

El campo de la marca de tiempo del consenso padre en un registro de transacción hijo no se llena en casos cuando la transacción hijo se activó **antes** de la transacción padre. Un ejemplo de este caso es crear una cuenta usando un alias de cuenta. El usuario envía la transacción de transferencia para crear y financiar la nueva cuenta usando el alias de la cuenta. La transacción de transferencia (padre) activa la cuenta crear transacción (hijo). Sin embargo, la transacción hijo se produce antes de la transacción padre, por lo que la nueva cuenta se crea antes de completar la transferencia. La marca de tiempo del consenso de los padres no está poblada en este caso.

**Recibos de transacción**

Los recibos de transacción anidados pueden ser devueltos solicitando el recibo de la transacción padre y estableciendo el valor booleano igual a verdadero para devolver todos los recibos de transacción hija.

**Tarifas de Transacción Hijo**

La tarifa de transacción para la transacción secundaria se incluye en el registro de la transacción padre. El cargo de transacción devolverá cero en la transacción hija.

## Consultas

Las **consultas** son procesadas sólo por el nodo único al que son enviadas. Los clientes envían consultas para recuperar algún aspecto del estado de consenso actual como el saldo de una cuenta. Algunas consultas son gratuitas, pero en general, las consultas están sujetas a tasas. La lista completa de consultas se puede encontrar [here](../sdks-and-apis/sdks/queries.md).

Una consulta incluye un encabezado que incluye una transacción normal de transferencia HBAR que servirá como el medio por el cual el cliente paga el nodo la comisión apropiada. No hay forma de dar un pago parcial a un nodo para procesar la consulta significando si un usuario sobrepagado por la consulta el usuario no recibirá un reembolso. El nodo que procesa la consulta enviará esa transacción de pago a la red para procesarla en una declaración de consenso con el fin de recibir su honorario.

Un cliente puede determinar la tarifa apropiada para una consulta solicitando primero un nodo por el costo y no por los datos reales. Tal consulta COST\_ANSWER es libre para el cliente.

Para obtener más información sobre las comisiones por consulta, por favor visite las comisiones de la API de Hedera [overview](https://www.hedera.com/fees).

### Recordar:

{% hint style="info" %}
Retirada

Hedera no tiene **mineros** o un grupo especial de nodos que sean responsables de agregar transacciones al libro de contabilidad como soluciones alternativas distribuidas de contadores. La influencia de cada nodo a la determinación de la marca de tiempo de consenso para un evento es proporcional a su participación en HBAR.
{% endhint %}

![](../.gitbook/assets/transaction-flow.png)

Una vez que una transacción ha sido enviada a la red, los clientes pueden buscar confirmación se procesó correctamente. Hay varios métodos de confirmación - variando en el nivel de información proporcionada, la duración durante la cual la confirmación está disponible, el grado de confianza y el coste correspondiente.

![](../.gitbook/assets/query-confirmation.png)

## Confirmaciones

- **Recibidos:** Los recibos proporcionan información mínima - simplemente si la transacción se procesó o no con éxito en un estado de consenso. Los recibos se generan por defecto y persisten durante 3 minutos. Los recibos son gratuitos.
- **Grabaciones:** Los registros proporcionan más detalles sobre la transacción que los recibos — tales como la marca de tiempo de consenso que recibió o los resultados de una llamada a la función de contrato inteligente. Los registros se generan por defecto, pero persisten durante 3 minutos.
- **Pruebas de estado (próximamente):** Al consultar un registro, un cliente puede indicar opcionalmente que desea que la red devuelva una prueba de estado además del registro. Una prueba de estado, documenta el consenso de la red sobre el contenido de ese registro en el estado de consenso — esta afirmación colectiva incluye firmas de la mayoría de los nodos de red. Dado que las pruebas estatales son criptográficamente firmadas por una gran mayoría de la red, son seguras y potencialmente admisibles en un tribunal de justicia.

{% hint style="info" %}
Una versión temprana de una prueba de estado alfa, prueba de estado, ya está disponible. Por favor, echa un vistazo a la sección de API REST de Mirror Node para empezar.
{% endhint %}

{% content-ref url="../sdks-and-apis/rest-api.md" %}
[rest-api.md](../sdks-and-apis/rest-api.md)
{% endcontent-ref %}

Para una revisión más detallada de los métodos de confirmación, por favor echa un vistazo a esta [publicación del blog](https://www.hedera.com/blog/transaction-confirmation-methods-in-hedera).

## FAQ

<details>

<summary>What are the transaction and query fees associated with using Hedera?</summary>

Puede consultar la página de tarifas en el sitio web de Hedera para obtener un desglose detallado de los costes de transacción y consulta. Si estás buscando una herramienta de estimación, puedes usar [Hedera fee estimator](https://hedera.com/fees).

</details>

<details>

<summary>What are transactions?</summary>

Las transacciones son peticiones enviadas por un cliente a un nodo con la expectativa de que sean enviadas a la red para procesarlas en orden de consenso y la posterior aplicación al estado. Cada transacción tiene un ID de transacción único compuesto por la hora de inicio válida de la transacción y el ID de cuenta de la cuenta que está pagando por la transacción. Este ID se utiliza para obtener recibos, registros y pruebas de estado y para detectar cuando se envían transacciones duplicadas.

</details>

<details>

<summary>¿Qué son las consultas?</summary>

Las consultas son peticiones procesadas sólo por el nodo único al que son enviadas. [Clients](../support-and-community/glossary.md#client) envía consultas para recuperar algún aspecto del estado de consenso actual, como el saldo de una cuenta. Algunas consultas son gratis, pero en general, las consultas están sujetas a tasas.

</details>

<details>

<summary>What is the difference between receipts, records, and state proofs?</summary>

Los recibos proporcionan información mínima - si la transacción fue o no procesada con éxito en un estado de consenso. Los registros proporcionan más detalles sobre la transacción que los recibos, como la marca de tiempo de consenso que recibió o los resultados de una llamada a la función de contrato inteligente. Las pruebas de estado son una característica que permitirá a un cliente indicar opcionalmente que desea que la red devuelva una prueba de estado además del registro.

</details>
