# Programar transacción

## Resumen

Una **transacción programada** es una transacción con la capacidad de recoger las firmas requeridas en una red Hedera como preparación para su ejecución. A diferencia de otras transacciones de Hedera, esto le permite poner en cola una transacción para su ejecución en el caso de que no tenga todas las firmas requeridas para que la red procese inmediatamente la transacción. Una transacción programada se utiliza para crear una transacción programada. Esta característica es ideal para transacciones que requieren múltiples firmas.

Cuando un usuario crea una transacción programada, la red crea una entidad programada. La entidad programada recibe un ID de entidad al igual que las cuentas, tokens, etc llamado un ID de programación. El ID del programa se utiliza para hacer referencia a la transacción programada que fue creada. La transacción que está siendo programada es referenciada por un ID de transacción programada.&#x20

Las firmas se añaden a la transacción programada enviando una transacción `ScheduleSign`. La transacción `ScheduleSign` requiere el ID de horario de la transacción programada a la que se añadirán las firmas. En su diseño actual, una transacción programada tiene 30 minutos para recoger todas las firmas requeridas antes de que la transacción programada pueda ser ejecutada o será borrada de la red. Puede eliminar una transacción programada estableciendo una clave de administración para eliminar una transacción programada antes de que sea ejecutada o eliminada por la red.

Puede solicitar el estado actual de una transacción programada consultando la red para `ScheduleGetInfo`. La solicitud devolverá la siguiente información:

- Programar ID
- ID de cuenta que creó la transacción programada
- ID de cuenta que pagó por la creación de la transacción programada
- Cuerpo de la transacción interna
- ID de la transacción interna
- Lista actual de firmas
- Clave de administrador (si existe)
- Tiempo de caducidad
- La marca de tiempo de cuando la transacción fue eliminada, si es verdadera

El documento de diseño para esta característica puede referenciarse [here](https://github.com/hashgraph/hedera-services/blob/master/docs/scheduled-transactions/revised-spec.md).

**Programar ID de la transacción**

Los ID de la transacción Hedera Transaction son compuestos por el ID de la cuenta que envía la transacción y la hora de inicio válida en seconds.nanoseconds (`0.0.1234@1615422161.673238162`). El ID de transacción para una transacción programada incluirá "`? chedule`" al final del ID de la transacción que identifica la transacción como una transacción programada i. . `0.0.1234@1615422161.673238162`. El ID de transacción de la transacción programada (interior) hereda la hora de inicio y el ID de la cuenta de la transacción programada (exterior).

**Programar recibos de transacción**

El recibo de la transacción de un programa que fue creado contiene el nuevo ID de entidad de programación y el ID de transacción programada. El ID de transacción programada se utiliza para solicitar registros de la transacción interna cuando se ejecutó con éxito.

**Programar registros de transacción**

Los registros de transacción se crean cuando se crea la transacción programada, para cada firma que fue añadida, cuando se ejecuta la transacción programada, y si la transacción programada fue borrada por un usuario. El registro de una transacción programada incluye una propiedad de referencia de horario, que es el ID del programa con el que el registro está asociado. Para obtener el registro de transacción para la transacción interna después de la ejecución exitosa, puede hacer lo siguiente:

1. Encuentre la red para el ID de transacción programada especificada. Una vez que la transacción programada ejecuta la transacción programada con éxito, solicite el registro de la transacción programada usando el ID de transacción programada.
2. Consulta un nodo espejo de Hedera para el ID de transacción programada.
3. Ejecute su propio nodo espejo y consulta para el ID de transacción programada.

## FAQ

<details>

<summary>What is the difference between a schedule transaction and scheduled transaction?</summary>

Una _**transacción de programación**_ es una transacción que puede programar cualquier transacción de Hedera con la capacidad de recoger las firmas requeridas en la red Hedera para preparar su ejecución.

Una _**transacción programada**_ es una transacción que ya ha sido programada.

</details>

<details>

<summary>Is there an entity ID assigned to a schedule transaction?</summary>

Sí, el ID de la entidad se refiere como el ID de schedule que se devuelve en el recibo de la transacción ScheduleCreate .

</details>

<details>

<summary>What transactions can be scheduled?</summary>

En su primera iteración, un pequeño subconjunto de transacciones será programable. Echa un vistazo a la página [this](../sdks-and-apis/sdks/schedule-transaction/create-a-schedule-transaction.md) para una lista de tipos de transacciones soportadas hoy. Todos los demás tipos de transacciones estarán disponibles para programar en futuras versiones. La lista completa de transacciones que los usuarios pueden programar en el futuro se puede encontrar aquí.

</details>

<details>

<summary>How can I find a schedule transaction that requires my signature?</summary>

- El creador de la transacción programada puede proporcionarte un ID de programación que especifiques en la transacción de ScheduleSign para enviar tu firma.

<!---->

- Puede consultar un nodo réplica para devolver todas las transacciones programadas que tengan su clave pública asociada a él. Esta opción no está disponible hoy, pero está prevista para el futuro.

</details>

<details>

<summary>What happens if the scheduled transaction does not have sufficient balance?</summary>

Si el pagador de la transacción programada (transacción interna) no tiene saldo suficiente, la transacción interna fallará mientras que la transacción programada (transacción externa) será exitosa.

</details>

<details>

<summary>Can you delay a transaction once it has been scheduled?</summary>

No, no puede retrasar o modificar una transacción programada una vez que ha sido enviada a una red. Necesitaría eliminar la transacción de programación y crear una nueva con las modificaciones.

</details>

<details>

<summary>What happens if multiple users create the same schedule transaction?</summary>

- La primera transacción en alcanzar un consenso creará la transacción de programación y proporcionará el ID de entidad de programación
- Los otros usuarios obtendrán el ID del horario en el recibo de la transacción que fue enviada. El estado de recibo resultará en `IDENTICAL_SCHEDULE_ALREADY_CREATED`. Estos usuarios necesitarían enviar una transacción de ScheduleSign para añadir sus firmas a la transacción de programación.

</details>

<details>

<summary>When does the scheduled transaction execute?</summary>

La transacción programada se ejecuta cuando se recibe la última firma.

</details>

<details>

<summary>How often does the network check to see if the scheduled transaction (inner transaction) has met the signature requirement?</summary>

Cada vez que se firma la transacción de programación.

</details>

<details>

<summary>¿Cómo obtener información sobre una transacción de programación?</summary>

Puedes enviar una [consulta de información de programa](../sdks-and-apis/sdks/schedule-transaction/get-schedule-info.md) a la red.

</details>

<details>

<summary>When does a scheduled transaction expire?</summary>

Una transacción programada expira en 30 minutos. En futuras implementaciones, permitiremos al usuario establecer la hora en la que la transacción programada debe ejecutarse, y la transacción caducará en ese momento.

</details>

<details>

<summary>What does a schedule transaction receipt contain?</summary>

El recibo de la transacción de un programa que fue creado contiene el nuevo ID de entidad de programación y el ID de transacción programada.

</details>
