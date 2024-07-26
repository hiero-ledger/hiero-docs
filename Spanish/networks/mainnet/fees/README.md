---
description: Cargos de red de hedera
---

# Cuotas

Las tablas de comisiones de prueba de Hedera que se encuentran a continuación ofrecen una estimación más baja de las tarifas de transacción y consulta para todos los servicios de red. Las tablas de abajo contienen valores USD, HBAR y Tinybar (t�) por cada llamada API. Todas las cuotas de operación en la red de pruebas de Hedera se pagan en la prueba HBAR, que está disponible libremente y sólo es útil para fines de desarrollo.

Las estimaciones de las comisiones se basan en supuestos sobre los detalles de una llamada API específica. Por ejemplo, la comisión por una transferencia de criptomonedas HBAR (CryptoTransfer) asume una sola firma en la transacción y la comisión por almacenar un archivo asume un archivo de tamaño de 48 bytes almacenado durante 30 días. Las transacciones que superen estos supuestos básicos serán más costosas; recomendamos aumentar su cuota máxima permitida para acomodar la complejidad adicional.

### Tarifas de Mainnet

Las comisiones de transacción y consulta Mainnet pueden ser estimadas usando el [Estimador de cuota de Hedera](https://hedera.com/fees). El Estimador de Comisión le permite determinar las comisiones (en USD y HBAR, utilizando el tipo de cambio actual en directo) para transacciones individuales y consultas basadas en sus características, así como los costos proyectados basados en el volumen esperado para esas transacciones. Las estimaciones no pueden ser 100% exactas y los precios subyacentes están sujetos a cambios sin previo aviso.

## Abreviaturas y nombres de HBAR

| Denominaciones | Abreviaturas                                             | Cantidad de criptomoneda HBAR                                   |
| -------------- | -------------------------------------------------------- | --------------------------------------------------------------- |
| gigabar        | 1 Għ                                                     | = 1.000.000.000 |
| megabar        | 1 M                                                      | = 1.000.000                     |
| kilobar        | 1 Ko                                                     | = 1.000 °                                       |
| hbar           | 1 vez                                                    | = 1                                                             |
| milibar        | 1.000 metros                             | = 1                                                             |
| microbarra     | 1,000,000 μħ                                             | = 1                                                             |
| tinybar        | 100.000.000 de tonterías | = 1                                                             |

## Tarifas de transacción y consultas

Todas las tarifas están sujetas a cambios. Las comisiones siguientes reflejan un precio base para la transacción o consulta. Las características de la transacción pueden aumentar el precio a partir del precio base que se muestra a continuación. Las características de la transacción incluyen tener más de una firma, un campo de memos, etc. Por favor refiérase al [estimador de la cuota de Hedera](https://hedera.com/fees) para estimar la cuota de transacción o consulta.

### Servicio de criptomonedas

<table><thead><tr><th width="482">Operaciones</th><th>USD ($)</th></tr></thead><tbody><tr><td>CriptoCrear</td><td>$0.05</td></tr><tr><td>CryptoAccountAutoRenew</td><td>$0.00022</td></tr><tr><td>CryptoDeleteAllowance</td><td>$0.05</td></tr><tr><td>CryptoApproveAllowance</td><td>$0.05</td></tr><tr><td>CryptoUpdate</td><td>$0.00022</td></tr><tr><td>CryptoTransfer</td><td>$0.0001</td></tr><tr><td>CryptoTransfer (comisiones personalizadas)</td><td>$0.002</td></tr><tr><td>CriptoEliminar</td><td>$0.005</td></tr><tr><td>CryptoGetAccountRecords</td><td>$0.0001</td></tr><tr><td>CryptoGetAccountBalance</td><td>$0.00</td></tr><tr><td>CryptoGetInfo</td><td>$0.0001</td></tr><tr><td>CriptoGetStakers</td><td>$0.0001</td></tr></tbody></table>

### Servicio de consenso

<table><thead><tr><th width="484">Operaciones</th><th>USD ($)</th></tr></thead><tbody><tr><td>Crear tema</td><td>$0.01</td></tr><tr><td>Actualización del tema</td><td>$0.00022</td></tr><tr><td>Eliminar tema</td><td>$0.005</td></tr><tr><td>Enviar mensaje</td><td>$0.0001</td></tr><tr><td>Información del tema</td><td>$0.0001</td></tr></tbody></table>

### Servicio de Token

<table><thead><tr><th width="486">Operaciones</th><th>USD ($)</th></tr></thead><tbody><tr><td>TokenCreate</td><td>$1.00</td></tr><tr><td>Crear TokenCreate (tarifas personalizadas)</td><td>$2.00</td></tr><tr><td>TokenUpdate</td><td>$0.001</td></tr><tr><td>Actualizar programa</td><td>$0.001</td></tr><tr><td>TokenDelete</td><td>$0.001</td></tr><tr><td>Asociado de fichas</td><td>$0.05</td></tr><tr><td>TokenDissociate</td><td>$0.05</td></tr><tr><td>TokenMint (fungible)</td><td>$0.001</td></tr><tr><td>TokenMint (no fungible)</td><td>$0.02</td></tr><tr><td>TokenMint (menta a granel 10k NFTs)</td><td>$200</td></tr><tr><td>Quemado</td><td>$0.001</td></tr><tr><td>TokenGrantKyc</td><td>$0.001</td></tr><tr><td>Revocar TokenKyc</td><td>$0.001</td></tr><tr><td>TokenFreeze</td><td>$0.001</td></tr><tr><td>TokenUnfreeze</td><td>$0.001</td></tr><tr><td>Pausa</td><td>$0.001</td></tr><tr><td>Reanudar token</td><td>$0.001</td></tr><tr><td>TokenWipe</td><td>$0.001</td></tr><tr><td>TokenGetInfo</td><td>$0.0001</td></tr><tr><td>Obtener información</td><td>$0.0001</td></tr><tr><td>Nft</td><td>$0.0001</td></tr><tr><td>Gestionar cuenta</td><td>$0.0001</td></tr><tr><td>TokenUpdateNfts (metadatos de actualizaciones de 1 NFT)</td><td>$0.001</td></tr><tr><td>TokenUpdateNfts (actualizar múltiples NFTs en una sola llamada)</td><td>N * $0.001</td></tr></tbody></table>

### Programar transacción

<table><thead><tr><th width="491">Operaciones</th><th>USD ($)</th></tr></thead><tbody><tr><td>Crear calendario</td><td>$0.01</td></tr><tr><td>Señal del programa</td><td>$0.001</td></tr><tr><td>ScheduleDelete</td><td>$0.001</td></tr><tr><td>Información del programa</td><td>$0.0001</td></tr></tbody></table>

### Servicio de archivos

<table><thead><tr><th width="495">Operaciones</th><th>USD ($)</th></tr></thead><tbody><tr><td>Crear archivo</td><td>$0.05</td></tr><tr><td>Actualizar archivo</td><td>$0.05</td></tr><tr><td>Eliminar archivo</td><td>$0.007</td></tr><tr><td>Añadir archivo</td><td>$0.05</td></tr><tr><td>Obtener contenido</td><td>$0.0001</td></tr><tr><td>Archivo GetInfo</td><td>$0.0001</td></tr></tbody></table>

### Servicio de Contratos Inteligentes

<table><thead><tr><th width="501">Operaciones</th><th>USD ($)</th></tr></thead><tbody><tr><td>Crear contrato</td><td>$1.0</td></tr><tr><td>Contrato actualizado</td><td>$0.026</td></tr><tr><td>Eliminar contrato</td><td>$0.007</td></tr><tr><td>Contrato</td><td>$0.05</td></tr><tr><td>Contrato local</td><td>$0.001</td></tr><tr><td>ContractGetByteCode</td><td>$0.05</td></tr><tr><td>GetBySolidityID</td><td>$0.0001</td></tr><tr><td>ContractGetInfo</td><td>$0.0001</td></tr><tr><td>ContraGetRecords</td><td>$0.0001</td></tr><tr><td>Renovación automática</td><td>$0.026</td></tr></tbody></table>

### Miscellano

<table><thead><tr><th width="508">Operaciones</th><th>USD ($)</th></tr></thead><tbody><tr><td>Transacción Ethereum</td><td>$0.0001</td></tr><tr><td>PrngTransacción</td><td>$0.001</td></tr><tr><td>Obtener versión</td><td>$0.001</td></tr><tr><td>Obtener Llave</td><td>$0.0001</td></tr><tr><td>Recibo de transacción</td><td>$0.0000</td></tr><tr><td>TransacciónGetRecord</td><td>$0.0001</td></tr></tbody></table>
