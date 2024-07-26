---
description: Unirse a la red principal de Hedera
---

# Mainnet

## Resumen

El mainnet de Hedera (abreviatura de la red principal) es donde las aplicaciones se ejecutan en producción, con tarifas de transacción pagadas en [HBAR](https://www.hedera.com/hbar). Las transacciones son enviadas a la red principal de Hedera por cualquier aplicación o usuario minorista; están automáticamente marcadas de tiempo de consenso y ordenadas de forma justa.&#x20

Los datos asociados con los servicios de Hedera y almacenados en cadena pueden ser consultados por cualquier cuenta de Hedera. Cada transacción requiere el pago en forma de una **comisión de transacción** denominada en _**tinybars**_ (100.000.000 túrpuras = 1 céntimo). Puede obtener más información sobre las comisiones de transacción y estimar los costos de su solicitud [here](https://www.hedera.com/fees).&#x20

Si estás buscando probar tu aplicación (o simplemente experimento), por favor visita [Testnet Access](../testnet/testnet-access.md). La red de pruebas de Hedera permite a los desarrolladores generar prototipos y probar aplicaciones en un entorno de red principal simulado que utiliza _HBAR_ de prueba para pagar comisiones de transacción.

{% hint style="warning" %}
**Trampas de Transacción**\
Las transacciones en la red principal de Hedera están actualmente atascadas. Usted recibirá una respuesta `"BUSY"` si el número de transacciones enviadas a la red excede el valor umbral.
{% endhint %}

#### Lanzamientos de red

| Tipos de solicitud de red      | Impulso (tps)                                                                                                                                                                        |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Transacciones de criptomonedas | <p>AccountCreateTransaction: 2 tps</p><p>AccountBalanceQuery: ilimitado</p><p>Transferencia (inc. tokens): 10,000 tps<br>Otro: 10,000 tps</p>                                                           |
| Transacciones de consenso      | <p>TopicCreateTransaction: 5 tps</p><p>Otro: 10,000 tps</p>                                                                                                                                             |
| Transacciones de Token         | <p>TokenMint:</p><ul><li>125 TPS para fungible minería</li><li>50 TPS para NFT mint</li></ul><p>TokenAssociateTransaction: 100 tps<br>Transferencia (inc. tokens): 10,000 tps</p><p>Otro: 3,000 tps</p> |
| Programar transacciones        | <p>ScheduleSignTransaction: 100 tps<br>ScheduleCreateTransaction: 100 tps</p>                                                                                                                           |
| Transacciones de archivo       | 10 tps                                                                                                                                                                                                  |
| Transacciones inteligentes     | <p>ContractExecuteTransaction: 350 tps<br>ContractCreateTransaction: 350 tps</p>                                                                                                                        |
| Consultas                      | <p>ContractGetInfo: 700 tps<br>ContractGetBytecode: 700 tps<br>ContractCallLocal: 700 tps<br><br>FileGetInfo: 700 tps<br>FileGetContents: 700 tps<br><br>Otro: 10, 00 tps</p>                           |
| Recibos                        | ilimitado (sin limitador)                                                                                                                                                            |
