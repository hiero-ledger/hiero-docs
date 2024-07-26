# Renta de Contratos Inteligentes

{% hint style="danger" %}
🚨 **HEDERA COUNCIL NO HA ENABLADO RENTES EN CONTRACTOS SMART AÑO. RENTES PAY PARA EL USO ONGOING DE RESOURCAS USADAS POR EL CONTRACTO SMART. HEDERA INTENDOS PARA ENCUENTRAR LOS RENTES EN LA FUTURA, COMO DESCRIBIDOS EN ESTA SECCIÓN. MÁS DETAILES COMING SOON... 🚨**
{% endhint %}

Smart contract rent es un mecanismo de pago recurrente diseñado para mantener la asignación de recursos y es necesario para que los contratos permanezcan activos en la red. Para los contratos, el alquiler se compone de dos componentes principales:

**➡️** [**Renovación automática**](smart-contract-rent.md#contract-auto-renewal)

**➡️** [**Pagos de almacenamiento**](smart-contract-rent.md#storage-payment)

***

## Contrato Auto-Renovación

La renovación automática es una característica que renueva automáticamente la vida de los contratos inteligentes no eliminados en un mínimo de 90 días. Se anima a los autores de contratos a establecer una cuenta de renovación automática específicamente para este propósito.&#x20

La red intentará cargar automáticamente el **pago de renovación** a la cuenta de renovación automática del contrato caducado. La red intentará cobrar el contrato si una cuenta de renovación automática tiene saldo cero. &#x20

Si la cuenta carece de fondos suficientes para la renovación, el contrato entra en un período de gracia de una semana. Durante este tiempo, el contrato es inoperable a menos que se añadan fondos, su caducidad se extiende (a través de `ContractUpdate`), o recibe HBAR. Si no se renueva, se eliminará el contrato del estado.

***

## Almacenamiento de pagos

Los pagos de almacenamiento de contratos en Hedera se activarán una vez que **100 millones de pares clave-valor** se almacenen acumulativamente en toda la red. Se espera que el Comité de Economía Moneda de Hedera fije una tasa de **0,02 dólares por par de valor clave por año**. Esto se aplica a todos los contratos en Hedera, independientemente del contrato que se esté creando antes o después de que los pagos de alquiler se realicen en vivo.

Una vez habilitados los pagos de almacenamiento en Hedera, cada contrato tiene **100 pares de clave-valor gratis** de almacenamiento disponibles. Luego, una vez que un contrato excede los primeros 100 pares de clave-valor, debe pagar comisiones de almacenamiento.&#x20

> _Los cargos de almacenamiento formarán parte del pago de alquiler recogido cuando un contrato se renueve automáticamente. Las ventanas de renovación válidas están entre \~30 y \~92 días (ver_ [_HIP-372_](https://hips.hedera.com/hip/hip-372)_)._

Si se alcanza un umbral de utilización lo suficientemente alto, se aplica \*\*el precio de congestión. \* En este caso, los precios cargados serán inversamente proporcionales a la capacidad restante del sistema de la red (menor capacidad restante significa precios más altos). Esto se aplica a todas las transacciones.

***

## Renta de Contratos Inteligentes - Preguntas Frecuentes (FAQ)

<details>

<summary>Why do contracts have to pay rent on Hedera?</summary>

Redes distribuidas como Hedera tienen una cantidad limitada de recursos computacionales. Cuando entidades como los contratos inteligentes se implementan en una red descentralizada, una parte de esos recursos se consumen. Por lo tanto, es imfactible mantener un número ilimitado de entidades por una cantidad infinita de tiempo en recursos finitos. La solución de este problema es necesaria y es un tema clave de discusión por Leemon y [others](https://www.coindesk.com/markets/2018/03/27/vitalik-wants-you-to-pay-slow-ethereums-growth/) en el espacio de red capa 1.

El alquiler de contratos es un enfoque económicamente y técnicamente viable para administrar las entidades contractuales inteligentes y el almacenamiento estatal.

</details>

<details>

<summary>Do all entities on Hedera have to pay rent or just contracts?</summary>

Todas las demás entidades de red (por ejemplo, tokens, cuentas, temas y archivos) también pagarán alquiler. Sin embargo, la línea de tiempo para el alquiler aún no está definida. Se proporcionará tiempo y aviso suficientes a la comunidad antes de habilitar el alquiler para otras entidades.

</details>

<details>

<summary>What charges are included in contract rent?</summary>

El alquiler se define como el pago recurrente requerido para que los contratos (y, eventualmente, todas las demás entidades Hedera) permanezcan activas en la red. Para los contratos, el alquiler se compone de **auto-renovación** y **almacenamiento**:

- **Pagos de renovación automática** La tarifa de renovación automática de un contrato es de $0.026 USD por 90 días.
- Los **pagos de almacenamiento** comenzarán una vez que un total de **100 millones de pares de valor clave** se almacenen acumulativamente en toda la red. Estos gastos de almacenamiento formarán parte del pago de alquiler recogido cuando un contrato se renueve automáticamente. La tasa de almacenamiento es de 0,02 dólares por par clave-valor al año.

<img src="../../.gitbook/assets/smart-contracts-rent-storage-payments.png" alt="" data-size="original">

</details>

<details>

<summary>What are the steps in the renewal process? ¿Y qué sucede si un contrato no paga renta?</summary>

Cada entidad en Hedera tiene los campos `expirationTime`, `autorenewPeriod`, y `autorenewAccount`.

1. Cuando se alcance el `expirationTime` para un contrato, la red primero intentará cobrar el alquiler del contrato a la `autoRenewAccount` del contrato
   - Si la renovación tiene éxito, el contrato permanece activo en la red
   - Si la renovación falla, entonces el contrato se marca como 'caducado'
2. A una entidad `caducada` se le da un período de gracia antes de que se retire de la red. Durante el período de gracia, la entidad (contrato) está inactiva, y todas las transacciones que lo involucren fallarán, excepto una transacción de actualización para extender el `expirationTime`
   - Un contrato en el periodo de gracia puede ser inmediatamente "reactivado" ya sea enviándolo algo de HBAR o extendiendo manualmente su `expirationTime` a través de una transacción de actualización del contrato
3. Al final del período de gracia, el contrato se elimina permanentemente del libro de beneficios:
   - El contrato y su 'cuenta de autorRenovación' todavía tienen un saldo HBAR cero al final del período de gracia, O
   - El contrato no se prolonga manualmente durante el período de gracia

Tenga en cuenta que el número de ID de una entidad eliminada no se reutiliza para avanzar. Además, si una entidad fue marcada como `deleted`, entonces no puede que se extienda su `expirationTime`. Ni una transacción de actualización ni una renovación automática serán capaces de ampliarla.

Vea el diagrama a continuación y [HIP-16](https://hips.hedera.com/hip/hip-16) para más detalles.

<img src="../../.gitbook/assets/Untitled.png" alt="" data-size="original">

</details>

<details>

<summary>How long is the grace period for expired contracts?</summary>

El período de gracia entre la caducidad de la entidad y la eliminación es de 30 días.

</details>

<details>

<summary>¿Quién paga las tasas de renovación y almacenamiento del contrato?</summary>

Los contratos Smart sobre Hedera pueden pagar el alquiler de dos maneras: fondos externos o fondos de contrato.

Cuando se alcance el `expirationTime` para un contrato, la red primero intentará cobrar el alquiler al `autoRenewAccount` del contrato\`:

- Si el `autoRenewAccount` tiene suficiente HBAR para pagar el `autoRenewPeriod`, entonces el contrato se renueva con éxito
- Si el `autoRenewAccount` tiene algo de HBAR pero no lo suficiente para permitirse el completo `autoRenewPeriod`, entonces el contrato se amplía tanto como sea posible (digamos, una semana en lugar de 90 días). Una vez transcurrida esa extensión (1 semana), si el `autoRenewAccount` no ha sido refinanciado para cubrir el `autoRenewPeriod`, entonces se cobrará la cuenta del contrato en sí misma por el alquiler
- Si el `autoRenewAccount` tiene un saldo HBAR cero, entonces el contrato en sí mismo es cobrado
- Si el `Auto RenewAccount` y el contrato ambos tienen un saldo HBAR cero en el momento en que las tasas de renovación son vencidas, el contrato está marcado como 'caducado'

</details>

<details>

<summary>What happens if I call a contract that is expired?</summary>

Llamar a un contrato `expired` resolverá a `CONTRACT_EXPIRED_AND_AWAITING_REMOVAL`.

</details>

<details>

<summary>When a contract is expired and deleted from the network, what happens to its account and assets?</summary>

Si un contrato caducado que contiene tokens nativos del Servicio de Token de Hedera (HTS) alcanza la etapa de eliminación, entonces los activos en posesión de ese contrato son devueltos a sus respectivas cuentas de tesoros.

Si el contrato eliminado está siendo usado como una clave específica para un token HTS, entonces ese campo clave se referirá a un contrato que ya no existe. Esa clave específica se puede cambiar, siempre y cuando se haya especificado una clave de administración durante la creación de token. Si el token es inmutable (sin clave de administración), la clave específica no se puede cambiar.

Los contratos que son el tesoro de los tokens HTS no expiran en este momento (sujeto a cambios en el futuro).

</details>

<details>

<summary>For how long can I renew my contract?</summary>

El período mínimo de renovación posible es de 2.592.000 segundos (\~30 días) y el máximo es de 8.001 segundos (\~92 días).

Ver detalles en [HIP-372: Entity Auto-Renewals and Expiry Window](https://hips.hedera.com/hip/hip-372).

</details>

<details>

<summary>Si cambio la <code>Auto-RenewPeriod</code> de mi contrato de 30 a 90 días, ¿cuál será el costo de mi transacción?</summary>

El costo de las escalas de alquiler es casi lineal con la duración del período de renovación. Así que una renovación que pague por 90 días costará \~3 veces más que una renovación que paga por 30 días.

</details>

<details>

<summary>Where can I seen when a contract will expire?</summary>

Los nodos de giro proporcionan el tiempo de caducidad de los contratos. Puede obtener esta información usando la API REST (mostrarla como `expiration_time`) y exploradores de red como HashScan (muestra como `Expires en`).

</details>

<details>

<summary>Where do the auto-renewal transactions appear? ¿Se pueden ver en exploradores de red como HashScan?</summary>

De acuerdo con [HIP-16: Entity Auto-Renewal](https://hips.hedera.com/hip/hip-16), los registros de cargos auto-renovados aparecerán como `actions` en el flujo de registro, y estarán disponibles a través de nodos espejos. Además, el desglose de tasas se proporciona en exploradores de red como HashScan para la transacción de actualización del contrato. No habrá recibos ni registros para acciones de renovación automática a través de consultas HAPI.

[HIP-449](https://hips.hedera.com/hip/hip-449) proporciona detalles técnicos sobre cómo se incluye la información para contratos de vencimiento en el flujo de registros.

</details>

<details>

<summary>¿Puede la <code>cuenta de renovación automática</code> para un contrato establecerse a otro ID del contrato?</summary>

Sí, eso es posible para los contratos.

</details>

<details>

<summary>What are the key-value pair thresholds that I should be aware of that impact the size of the storage payment?</summary>

- Los pagos de almacenamiento por contratos solo comenzarán a cobrarse una vez que se alcancen **100 millones de pares de valor clave** acumulados en toda la red
- Después de eso, cada contrato tiene **100 pares de clave-valor libre** de almacenamiento disponibles. Una vez que un contrato excede los primeros 100 pares de clave-valor, debe pagar comisiones de almacenamiento

</details>

<details>

<summary>For smart contracts created via <code>CREATE2</code>, how can I specify rent-related properties like<code>autorenewAccount</code> and <code>autorenewPeriod</code>?</summary>

Los contratos creados a través de `CREATE2` dentro de la EVM heredarán la `autorenewaccount` y `autorenewPeriod`de la dirección `sender`.

Por ejemplo, si llama al contrato `0xab...cd` que tiene `autorenewAccount` `0.0.X` y `autorenewPeriod` de 45 días, y este contrato despliegue un nuevo contrato `0xcd. .ef`, entonces el nuevo contrato también tendrá `autorenewAccount` `0.0.X`y `autorenewPeriod` de 45 días.

Además, recuerde que el alquiler puede ser cubierto por el saldo de HBAR de un contrato. Así, los desarrolladores pueden enviar HBAR al contrato o configurar el contrato para cobrar a los usuarios una cantidad específica de HBAR al ejecutar operaciones.

</details>
