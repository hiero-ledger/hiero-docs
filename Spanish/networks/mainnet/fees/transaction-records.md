# Registros de transacción

## Registros de Transacción Enderstanding – Remesas & Tarifas

Una vez que una transacción ha sido procesada con éxito por la red Hedera a un estado de consenso o no, los nodos de red crean un "registro" o un "recibo", respectivamente, para esa transacción, indicando su estado e impacto.

Un componente clave de la información dentro de un registro de una transacción es cómo la transacción cambió los balances de aquellas cuentas de Hedera que estaban involucradas. El saldo de una cuenta puede cambiar debido a una transacción ya sea por una comisión (pagada o recibida) o por otra transferencia intencional – a la que nos referimos aquí como una “remitencia”.

### Saldos de la cuenta

Cada transacción enviada a Hedera hará que los balances de un conjunto de cuentas cambien, ya sea porque

1. La transacción directamente dirigió saldos específicos a cambiar, por ejemplo, Alice envió 1000 hbars a Bob con una CryptoTransfer.
2. La transacción indirectamente causó que los balances cambiaran, por ejemplo, la ejecución de una ContractCall causó que HBARs se enviara desde la cuenta del Contrato Inteligente a otros.
3. Las tasas se pagan por el procesamiento de la transacción en un estado de consenso y la persistencia de ese estado cambiado.
4. Se paga una cuota por la persistencia de un “registro” para esa transacción por un período de tiempo más largo que el predeterminado.

Una transacción puede hacer que el saldo de una cuenta determinada cambie por cualquiera de las razones anteriores o, de forma más general, por una combinación de las razones anteriores.

En general, hay 5 tipos de cuentas asociadas a una transacción:

1. Remitentes - las cuentas desde las que se envían HBARs
2. Destinatarios - las cuentas a las que se envían los HBARs
3. Pagador – la cuenta que paga las comisiones asociadas a la transacción.
4. Red – la cuenta de Hedera que recibe el componente de las comisiones que compensan la red por procesar la transacción.
5. Nodo – la cuenta de cualquier nodo que envíe la transacción a la red para el consenso

**Notas**

- Para cualquier transacción, la suma de transferencias fuera de todas las cuentas será siempre igual a la suma de todas las transferencias en todas las cuentas.
- El pagador, en general, es diferente del remitente o del receptor. Sin embargo, un caso típico es que el remitente también será el pagador.
- No todas las transacciones tienen un remitente o receptor ya que no hay ningún aspecto de remisión a la transacción, e. . una transacción FileCreate o ConsensusSubmitMessage tendrá una comisión pero no la remittance asociada.
- Un solo CryptoTransfer puede tener múltiples emisores y múltiples receptores.
- Una remesa puede ser el número de HBARs que un CryptoTransfer dirige a mover, la cantidad de HBARs que un CryptoCreate dirige se financia en la nueva cuenta, o la cantidad de HBARs en una cuenta a eliminar, con esos fondos movidos a otra cuenta.
- Una remisión tendrá que ser autorizada por el propietario de esos HBAR.
- El propietario de una cuenta puede especificar umbrales para transferencias dentro y fuera de esa cuenta. Si una transacción provoca que se active el umbral de una cuenta, entonces el registro de esa transacción persistirá durante 25 horas y no los 3 minutos por defecto.
- El propietario de la cuenta que especificó el umbral pagará una tasa de umbral – distinta de la comisión por la transacción misma – por ese tiempo adicional de almacenamiento.
- Es la cuenta 0.0. 8 que recibe el componente de la cuota de transacción que compensa a todos los nodos por su trabajo en la transformación de la transacción en consenso
- 0.0.98 también cobra cualquier tasa de umbral
- A principios de febrero de 2024, hay 31 nodos con números de cuenta en el rango de 0.0.3-0.0.4698971.
- Mientras las cuentas 0.0. 8 y las cuentas del nodo son especiales con respecto a las comisiones de recepción, también pueden enviar y recibir HBARs y, como tal, podrían ser el remitente o receptor de una transacción.

### Scenarios

Exploramos los escenarios a continuación y cómo fluyen los HBARs entre las cuentas de cada uno.

#### Caso 1 - Genérico

En el caso más genérico un remitente está haciendo una devolución a un receptor, y una cuenta separada paga la cuota asociada. El tamaño de la comisión dependerá de la naturaleza de la transacción. La carga de un archivo grande costará más que una simple transferencia de cifrado.

La cantidad de esa cuota se divide entre la cuenta 0.0. 8 (una cuenta especial de Hedera que representa la red) y el nodo específico que presentó la transacción.

![](../../../.gitbook/assets/transaction\_records\_1.png)

#### Caso 2 - Cuotas sólo

Muchas transacciones no permiten una remisión explícita, por ejemplo, un FileCreate o un ConsensusSubmitMessage. Para estas transacciones, los únicos cambios en los balances de la cuenta se deben a la comisión de esa transacción.

Como antes, la comisión para la transacción se divide en 0.0.98 y un nodo.

![](../../../.gitbook/assets/transaction\_records\_2.png)

#### Caso 3 - La cuenta del remitente paga cuotas

A menudo se da el caso de que la tarifa por un envío de CryptoTransfer de un remitente a un receptor es pagada por el remitente. En este caso, el saldo del remitente disminuirá por la suma de la devolución y la tasa.

En principio, el receptor también podría pagar la tasa.

![](../../../.gitbook/assets/transaction\_records\_3.png)

#### Caso 4 - La cuenta del remitente tiene un umbral que se cruza

Los propietarios de la cuenta pueden especificar umbrales para sus cuentas de modo que cualquier transferencia dentro o fuera de la cuenta que exceda estos umbrales causará que el registro creado persista durante 25 horas y no los 3 minutos por defecto. La cuenta que asfixió el umbral pagará una tasa de umbral por este almacenamiento prolongado del registro.

En este ejemplo, el umbral se especifica en la cuenta de envío, por lo que será esa cuenta la que pague esta cuota de umbral. En consecuencia, el saldo de esa cuenta disminuye por la suma de la devolución y la tasa de umbral.

La cuenta 98 recibe la suma de la red, el servicio, y también esta cuota de umbral.

No se muestra aquí, pero si fuera también el caso de que el remitente estaba pagando la cuota de transacción (como arriba), entonces el saldo de la cuenta del remitente disminuiría por la suma de la remitencia, la cuota de transacción, y esta cuota de umbral.

![](../../../.gitbook/assets/transaction\_records\_4.png)

#### Caso 5 - La cuenta receptora tiene un umbral que se cruza

La cuenta receptora también puede tener un umbral establecido eso, si se supera por la cantidad de HBARs transferidos a la cuenta, hará que el registro se almacene durante 25 horas.

Los umbrales pueden ser particularmente útiles para los receptores ya que un receptor puede no ser consciente de las remitencias enviadas a ellos (ya que no están necesariamente involucrados en la firma de la transacción), como es el caso del remitente). Los umbrales de larga duración permiten a un propietario de una cuenta consultar diariamente los registros de todas y cada una de las devoluciones que ha recibido en las últimas 24 horas de las que de otro modo podrían no ser conscientes.

La cuenta receptora pagará la cuota de umbral asociada para este período de almacenamiento más largo del registro.

El cambio de saldo neto de la cuenta receptora será, por lo tanto, la devolución menos la tasa de umbral.

La cuenta 98 recibe la suma de la red, el servicio y los umbrales.

Las tasas de transacción no se ven afectadas por la tasa umbral que se está pagando.

Si el valor de la remisión es menor que la tasa umbral, la transacción fallará.

![](../../../.gitbook/assets/transaction\_records\_5.png)

#### Caso 6 - La cuenta del nodo es receptora

Los nodos pueden recibir remesas como cualquier otra cuenta de Hedera.

Como un ejemplo específico, los clientes compensan los nodos por responder a una consulta incluyendo dentro de la consulta una CryptoTransfer que, cuando se envíe a la red, compensará ese nodo en particular con una remisión adecuada.

En este escenario, el saldo de la cuenta del nodo aumentará por la suma de la cuota de nodo que recibe por procesar el CryptoTransfer más el valor de la remitencia real que paga el nodo por la respuesta a la consulta.

![](../../../.gitbook/assets/transaction\_records\_6.png)

### Registros de transacción

Después de que los nodos Hedera Mainnet procesen una transacción en estado de consenso, los detalles se publican al mundo exterior en un “registro de transacciones” que los nodos crean y ponen a su disposición. Los clientes recuperan registros y analizan los datos de dentro para verificar las consecuencias de las transacciones, por ejemplo la marca de tiempo de consenso que fue asignada y cómo los balances de cuenta asociados cambiaron como resultado de la transacción.

Cuando se recupera de un nodo espejo y no del mainnet, la transacción que resultó en un registro dado también estará disponible. Por lo tanto, es esta estructura de datos combinada la que proporciona el conjunto más rico de información para analizar y diferenciar entre reembolsos y tasas.

El flujo de información se muestra a continuación:

![](../../../.gitbook/assets/transaction\_records\_7.png)

Un cliente que recupera el par de una transacción y su registro asociado puede querer distinguir entre remesas y componentes de comisión para la transacción - es decir, que parte del cambio de saldo de una cuenta fue debido a las comisiones de la transacción, qué parte se debe a una tasa de umbral, y qué parte se debe a una devolución.

Hay suficiente información en la combinación de la transacción y el registro correspondiente para permitir que un cliente haga esta distinción sin ambigüedades para cada cuenta.

Un registro de transacción tiene una estructura de datos de lista de transferencias que describe cómo se movió HBARs entre cuentas como resultado de la transacción.

En la versión R3 (la versión anterior a la actualización del 10 de febrero de 2020) del software del nodo, puede haber múltiples transferencias para cada cuenta involucrada en la transacción. Por ejemplo, podría haber transferencias separadas indicando las tasas de recepción de la cuenta 0.0.98, que se sumaron al total correcto de la comisión.

Adicionalmente, en R3

1. La determinación de si se superó un umbral para cada cuenta se realizó para cada transferencia. En consecuencia, una cuenta única que pague tanto una devolución como tasas podría pagar por múltiples registros de umbrales si el umbral se fijara muy bajo.
2. El orden de las transferencias en formato R3 no era predecible.

Hemos cambiado el formato de la lista de transferencia de registros en el R4 (la liberación del 10 de febrero 2020) software de nodo para abordar los problemas anteriores y ser más consistente y conciso.

En la versión R4, la lista de transferencia de registros muestra, para todos los tipos de transacciones, sólo una sola transferencia neta dentro o fuera de cada cuenta relevante.

La comparación de las transferencias dentro o fuera de los umbrales de una cuenta se hace ahora en esa transferencia neta, y no en las transferencias constituyentes que resumieron a la red. En consecuencia, cualquier cuenta pagará una vez por una única tasa de umbral y no varias veces. Esto es más barato para el usuario.

El cambio entre R3 y R4 se muestra a continuación, para una transacción representativa en la que la cuenta 0.0. 002 está enviando una remesa de 10.000 tinybars a la cuenta 0.1001 y tanto el remitente y el receptor tienen umbrales de 1.000 tinybars fijados en sus cuentas.

Dado que el valor de remisión excede estos umbrales, tanto el remitente como el receptor pagarán una cuota de umbral.

**R3**

![](../../../.gitbook/assets/r3.jpeg)

**R4**

![](../../../.gitbook/assets/r4.jpg)

En el formato R4, la lista de transferencia de registros ya no tiene múltiples transferencias para las diferentes cuentas – cada cuenta tiene sólo una sola transferencia con un valor que refleja la suma de las distintas transferencias que afectaron a cada cuenta.

Mientras que el formato R4 es más conciso que el formato R3, algunos clientes pueden querer determinar las transferencias de componentes - que es romper las remitencias, cargos por node, umbrales y otras comisiones por transacción. Para facilitar este análisis, Hedera planea añadir soporte a la API REST del nodo espejo para permitir que un cliente solicite la lista de transferencias agregadas por defecto, o en su lugar una lista detallada de transferencias (similar al formato R3).
