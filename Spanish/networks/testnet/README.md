---
description: Únete a una red de pruebas de Hedera
---

# Testnets

## Resumen

Las redes de pruebas de Hedera proporcionan a los desarrolladores acceso a un entorno de pruebas gratuito para los servicios de red de Hedera. Las redes de prueba simulan el entorno de desarrollo exacto como esperarías para mainnet. Esto incluye tasas de transacción, limites, servicios disponibles, etc. Para crear una cuenta de Hedera Testnet o Previewnet, puedes visitar el [Portal de Desarrollador de Hedera](https://portal.hedera.com/login).

Una vez que su aplicación ha sido construida y probada en este entorno de prueba, puede esperar migrar su aplicación descentralizada (dApp) a mainnet sin ningún cambio.

<table><thead><tr><th width="324">Probar redes</th><th>Descripción</th></tr></thead><tbody><tr><td><strong>Testnet</strong></td><td>Testnet ejecuta el mismo código que el Hedera Mainnet, diseñado para proporcionar un entorno de preproducción para desarrolladores que están a punto de mudarse a mainnet. Puedes encontrar SDKs compatibles <a href="../../sdks-and-apis/sdks/#hedera-supported-sdks">aquí</a>.</td></tr><tr><td><strong>Vista previa</strong></td><td><p>Código que está siendo desarrollado por el equipo de Hedera y que probablemente será utilizado en una próxima versión diseñada para dar a los desarrolladores una exposición temprana a las características que bajan la tubería. Las actualizaciones a la red se realizan con frecuencia. No hay garantía de que un SDK soporte fácilmente las características de arriba y venir.</p><p><strong>Note:</strong> Updates to this network are triggered by a new release and are frequent. Estas actualizaciones no se reflejarán en la página de estado.</p></td></tr></tbody></table>

<table><thead><tr><th width="325">Servicio de red</th><th>Disponibilidad</th></tr></thead><tbody><tr><td><strong>Criptomoneda</strong></td><td>Limitado</td></tr><tr><td><strong>Servicio de consenso</strong></td><td>Limitado</td></tr><tr><td><strong>Servicio de archivos</strong></td><td>Limitado</td></tr><tr><td><strong>Servicio de Contrato Inteligente</strong></td><td>Limitado</td></tr><tr><td><strong>Token Service</strong></td><td>Limitado</td></tr></tbody></table>

### Probar reinicios de red

El nodo espejo y la red de pruebas de nodos consensuados están programados para reiniciarse una vez que el cuadrilátero. Cuando se produce un reinicio de red de pruebas, todos los datos de cuenta, token, contrato, tópico, horario y archivo se borran.

Los desarrolladores ya no tendrán acceso a los datos de estado de los nodos de prueba de consenso de red. Por ejemplo, no podrá realizar transacciones o consultas en una cuenta que existía antes de reiniciar.&#x20

El nodo espejo testnet estará disponible para que los desarrolladores almacenen cualquier dato antes de que se elimine completamente el acceso durante dos semanas después de la fecha del reinicio. Podrá consultar la información antigua de la red de pruebas durante el período de dos semanas si está disponible.

**Lo que deberías hacer:**

- Tome nota de las próximas fechas de reinicio.
- Tener la capacidad de recrear los datos de prueba para su aplicación para minimizar las interrupciones.
- Después de reiniciar, tendrás que visitar el [Portal de Desarrolladores de Hedera](https://portal.hedera.com/register) para obtener el ID de tu nueva cuenta de testnet.
  - El par de claves públicas y privadas seguirá siendo el mismo después de los reinicios.
- Suscríbete a la [página de estado de Hedera](https://status.hedera.com/) para recibir notificaciones de reinicio.
- Los operadores de nodo de réplica pueden hacer referencia a las instrucciones [here](https://github.com/hashgraph/hedera-mirror-node/blob/main/docs/database.md#reset) para configurar su nodo espejo
  - Buckets GCP GCS y AWS S3: `hedera-testnet-streams-2023-01`

Si tienes alguna pregunta o preocupación, por favor conecta con nosotros a través de [Discord](https://hedera.com/discord).

**Restablecer fechas:**

**2024**

- 1 de febrero de 2024 - Completado
- 25 de abril de 2024
- 25 de julio de 2024
- 31 de octubre de 2024

**2023**

- 26 de enero de 2023 - Completado&#x20
- 27 de abril de 2023 - Saltado&#x20
- 27 de julio de 2023 - Completado
- 26 de octubre de 2023 - Omitido

### Probar lanzamientos de red

{% hint style="warning" %}
**Soporte Limitado**\
Las transacciones están actualmente atascadas para redes de prueba. Usted recibirá una respuesta **`BUSY`** si el número de transacciones enviadas a la red excede el valor umbral.
{% endhint %}

<table><thead><tr><th width="322">Tipo de solicitud de red</th><th>Impulso (tps)</th></tr></thead><tbody><tr><td><strong>Transacciones de criptomonedas</strong></td><td><p><code>AccountCreateTransaction</code>: 2 tps</p><p><code>AccountBalanceQuery</code>: ilimitado<br><code>Transferencia</code> (inc. tokens): 10,000 tps</p><p><code>Otra</code>: 10.000 tps</p></td></tr><tr><td><strong>Consenso de transacciones</strong></td><td><p><code>TopicCreateTransaction</code>: 5 tps</p><p><code>Otra</code>: 10.000 tps</p></td></tr><tr><td><strong>Transacciones de Token</strong></td><td><p><code>TokenMintTransaction</code>:</p><ul><li>125 TPS para menta fungible</li><li>50 TPS para la menta NFT</li></ul><p><code>TokenAssociateTransaction</code>: 100 tps<br><code>TransferTransaction</code> (inc. tokens): 10,000 tps</p><p><code>Otra</code>: 3.000 tps</p></td></tr><tr><td><strong>Programar transacciones</strong></td><td><code>ScheduleSignTransaction</code>: 100 tps<br><code>ScheduleCreateTransaction</code>: 100 tps</td></tr><tr><td><strong>Transacciones de archivo</strong></td><td>10 tps</td></tr><tr><td><strong>Transacciones inteligentes de contrato</strong></td><td><code>ContractExecuteTransaction</code>: 350 tps<br><code>ContractCreateTransaction</code>: 350 tps</td></tr><tr><td><strong>Consultas</strong></td><td><code>ContractGetInfo</code>: 700 tps<br><code>ContractGetBytecode</code>: 700 tps<br><code>ContractCallLocal</code>: 700 tps<br><br><code>FileGetInfo</code>: 700 tps<br><code>FileGetContents</code>: 700 tps<br><br><code>Other</code>: 10, 10, 10, 00 tps</td></tr><tr><td><strong>recibos</strong></td><td>ilimitado (sin limitador)</td></tr></tbody></table>
