# Hedera inferior para desarrolladores de EVM

## Hedera vs Ethereum

Mientras que Hedera lucha por la equivalencia de EVM, es importante reconocer ciertos aspectos únicos y diferencias fundamentales en su arquitectura y operaciones de red, como el manejo de estructuras de datos estatales, algoritmos de hashing y la gestión de cuentas y transacciones. Estas distinciones en los comportamientos de la red son opciones de diseño intencionales hechas para alinearse con los estándares EVM, logrando así la compatibilidad con EVM. Este enfoque asegura que, si bien Hedera está muy cerca de Ethereum, también mantiene sus características distintivas y su optimización.

### Diferencias de red y seguridad

<table><thead><tr><th width="211">Función</th><th>Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Estructura de datos del estado de red</strong></td><td>Árbol de Merkle Virtual</td><td>Intento de Merkle Patricia</td></tr><tr><td><strong>Algoritmo de Hash</strong></td><td>SHA-384</td><td>Keccak-256</td></tr><tr><td><strong>Seguridad</strong></td><td>Alta seguridad con aBFT</td><td>Asegurado con la red descentralizada de PoS</td></tr></tbody></table>

### Diferencias de cuenta y autorización

<table><thead><tr><th width="202.33333333333331">Función</th><th width="296">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Firmas de Autorización</strong></td><td>Utilizado para la autorización de transacciones fuera de contratos inteligentes</td><td>Normalmente usado dentro de contratos inteligentes</td></tr><tr><td><strong>Cuentas Especiales del Sistema</strong></td><td>Disponible con propiedades únicas</td><td>No disponible</td></tr><tr><td><strong>Cuentas no ECDSA</strong></td><td><p>Las cuentas que no son ECDSA (como ED o multi-clave) están soportadas por Hedera y</p><p>Las cuentas ECDSA son totalmente compatibles</p></td><td>Las cuentas ECDSA están soportadas por Ethereum y las cuentas no ECDSA no son soportadas/compatibles</td></tr><tr><td><strong>Account Deletion</strong></td><td>Posible</td><td>No es posible</td></tr></tbody></table>

### Diferencias de Contrato y Gas

<table><thead><tr><th width="210.33333333333331">Función</th><th width="252">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Data Return on Static Calls</strong></td><td>La recuperación de datos debe hacerse a través del repetidor</td><td>Datos devueltos directamente</td></tr><tr><td><strong>tarifas de gas</strong></td><td>Carga al menos el 80% de las comisiones de gas sin importar el resultado de la transacción</td><td>Las comisiones de gas dependen del resultado de la transacción pero normalmente se cobran el 100% de las comisiones de gas y la porción no utilizada se acredita de vuelta</td></tr><tr><td><strong>ciclo de vida del contrato</strong></td><td>Las entidades del contrato pueden expirar, las tarifas de alquiler pueden aplicarse</td><td>Sin vencimiento ni cargos de alquiler</td></tr></tbody></table>

### Diferencias de transacciones y consultas

<table><thead><tr><th width="212.33333333333331">Función</th><th width="252">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Transaction Size Limit</strong></td><td>6kb</td><td>Sin límite</td></tr><tr><td><strong>Transacción lanzando</strong></td><td><a href="deploying-smart-contracts/#gas-limit">Las transacciones pueden ser atascadas por límites de gas</a></td><td>Transacciones pendientes hasta el envío futuro</td></tr><tr><td><strong>Costes de consulta</strong></td><td>No gratis, puede usar el nodo réplica para consultas gratuitas</td><td>Llamadas de solo lectura gratuitas</td></tr><tr><td><strong>Mempools</strong></td><td>No <a href="../../support-and-community/glossary.md#mempool">mempools</a></td><td>Memorias disponibles</td></tr><tr><td><strong>Coste</strong></td><td>Tarifas bajas y predecibles (fracción de centavo)</td><td>Variable, a menudo altas tarifas de gas</td></tr></tbody></table>

### Diferencias de comunicación y punto final RPC

<table><thead><tr><th width="259">Función</th><th width="244">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>RPC Bloque Solicita</strong> (por ejemplo, <code>eth_getBlockByHash*</code> & <code>eth_getBlockByNumber</code>)</td><td>Devolver cero 32bytes valor hexadecimal para el <code>stateRoot</code></td><td>Devuelve el valor hexadecimal <code>stateRoot</code> del estado final del bloque</td></tr><tr><td><strong>Comunicación</strong></td><td>Requiere comunicación con los nodos de consenso y espejo</td><td>Comunicación directa con nodos</td></tr></tbody></table>

{% hint style="info" %}
**Nota**: Consenso de Hedera y nodos espejo no proporcionan puntos finales de API RPC de Ethereum.
{% endhint %}

### Diferencias de tarifas y token

<table><thead><tr><th width="232.33333333333331">Función</th><th>Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td> <strong>Tokens nativos</strong></td><td>Soporta tokens nativos además de los estándares <a href="tokens-managed-by-smart-contracts/">ERC y ERC-721 tokens</a></td><td>Todas las normas ERC token pero principalmente las tokens ERC y ERC-721.</td></tr><tr><td><strong>Cuota de estructura</strong></td><td><a href="deploying-smart-contracts/#gas-schedule-and-fees">Complejo con dos precios diferentes de gas</a></td><td>Precio de gas único</td></tr><tr><td><strong>Asociación de Token **</strong></td><td><a href="../../sdks-and-apis/sdks/token-service/associate-tokens-to-an-account.md">Concepto de asociación de tokens </a></td><td>Ningún concepto de asociación de tokens</td></tr><tr><td><strong>Llaves para Funcionalidad de Token</strong></td><td>Las claves controlan el acceso a la funcionalidad del token (<code>KYC</code>, <code>FREEZE</code>, <code>WIPE</code>, suministro, comisión y <code>PAUSE</code>)</td><td>No equivalent <em>native</em> functionality</td></tr></tbody></table>

{% hint style="info" %}
**\*\*Nota:** La asociación de token sólo aplica a tokens _nativos_ HTS y no afecta tokens ERC-20/721.
{% endhint %}

### Otras Diferencias

<table><thead><tr><th width="238">Función</th><th width="274.3333333333333">Hedera</th><th>Ethereum</th></tr></thead><tbody><tr><td><strong>Precheck Failures</strong></td><td><a href="../../sdks-and-apis/hedera-api/miscellaneous/responsecode.md">Múltiples razones de fallo precomprobadas</a></td><td>Razón típica de un fallo</td></tr><tr><td><strong>Precisión decimal HBAR</strong></td><td>8 o 18<a href="../../sdks-and-apis/sdks/hbars.md#hbar-decimal-places"> (varía entre APIs de Hedera)</a></td><td>Precisión decimal de 18 puntos consistente</td></tr></tbody></table>
