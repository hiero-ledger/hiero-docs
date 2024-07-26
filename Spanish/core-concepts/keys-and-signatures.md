# Llaves y firmas

## Tipos de clave: ECDSA vs Ed25519

Una clave puede ser una [clave pública](../support-and-community/glossary.md#public-key) de un sistema soportado [Ed25519](../support-and-community/glossary.md#ed25519), [ECDSA secp256k1](../support-and-community/glossary.md#ecdsa-secp256k1), o un ID de un [smart contract](../support-and-community/glossary.md#smart-contract). El algoritmo correspondiente genera claves públicas y privadas únicas entre sí. La clave pública puede ser compartida y visible para otros usuarios de red en [Network Explorer](../support-and-community/glossary.md#network-explorer) o [REST APIs](../support-and-community/glossary.md#rest-api). La [clave privada](../support-and-community/glossary.md#private-key) se mantiene en secreto del propietario y concede acceso al propietario para modificar entidades (cuentas, tokens, etc.).&#x20

Las claves privadas _sólo pueden recuperarse una vez perdidas si se crean con una frase de recuperación asociada a la que puede acceder. Las claves son mutables y se pueden actualizar una vez definidas para una entidad. Generalmente, necesitará la clave actual para firmar la transacción para actualizar las claves.&#x20

### Elegir entre ECDSA y Ed25519 Keys

La elección entre las claves ECDSA y ED25519 depende principalmente de su caso de uso específico:

<table data-header-hidden><thead><tr><th width="193.33333333333331"></th><th></th><th></th></tr></thead><tbody><tr><td></td><td><strong>ECDSA</strong></td><td><strong>Ed25519</strong></td></tr><tr><td><strong>Usar Casos</strong></td><td>Ideal si quieres usar <a href="../support-and-community/glossary.md#metamask">MetaMask</a> o necesitas soporte para más herramientas de desarrollador EVM. Apropiado para aplicaciones que interactúan con redes compatibles con Ethereum o EVM debido a la dirección EVM asociada.</td><td>Se prefiere si la longitud clave, la seguridad y el rendimiento son importantes. Las claves públicas ECDSA son el doble de largas para el mismo nivel de seguridad.</td></tr></tbody></table>

{% hint style="info" %}
**Nota**: Monederos Hedera como [HashPack](https://www.hashpack.app/) soportan ambos tipos de claves.
{% endhint %}

## Estructuras clave

Hedera soporta los siguientes tipos de estructura de clave:

<table data-header-hidden><thead><tr><th width="193.33333333333331"></th><th width="240"></th><th></th></tr></thead><tbody><tr><td></td><td><strong>Descripción</strong></td><td><strong>Example</strong></td></tr><tr><td><strong>Tecla simple</strong></td><td>Una única clave en una cuenta.</td><td><strong>Cuenta</strong> <strong>Clave</strong> <br>       { <br>           Clave 1 <br>        }<br>Solo se requiere una clave para firmar en la cuenta.</td></tr><tr><td><strong>lista de teclas</strong></td><td>Todas las claves de la lista de claves son necesarias para firmar las transacciones que involucran la cuenta.</td><td><strong>Account Key</strong><br>     <strong>KeyList (3/3)</strong> <br>          { <br>               Key 1 <br>               Key 2 <br>               Key 3 <br>          }<br>All three keys in the list are required to sign for the account.</td></tr><tr><td><strong>Umbral de tecla</strong></td><td>Se requiere un subconjunto de claves definidas como el umbral para firmar la transacción que involucra la cuenta fuera del número total de claves.</td><td><strong>Clave de cuenta</strong><br>      <strong>ThresholdKey (1/3)</strong> <br>          { <br>              Clave 1 <br>              Clave 2 <br>              Clave 3 <br>          }<br>Una de las tres claves de la lista de claves es necesaria para registrarse en la cuenta.</td></tr></tbody></table>

{% hint style="info" %}
🔔 Las estructuras clave pueden ser anidadas. Esto significa que puede tener un sistema de claves más complejo con listas de claves dentro de teclas umbrales, teclas dentro de listas de claves, etc. Un ejemplo de una lista de claves anidada puede ser visto [here](https://hashscan.io/mainnet/adminKey/0.0.2).
{% endhint %}

Todos los tipos de transacción soportan las estructuras de clave anteriores que especifican un campo clave. Para que una transacción tenga éxito, las firmas proporcionadas deben coincidir con los requisitos de estructura de clave definida.

## FAQ

<details>

<summary>¿Qué es una clave en Hedera?</summary>

Una clave en Hedera puede ser una [clave pública](../support-and-community/glossary.md#public-key) de un sistema soportado como [ED25519](../support-and-community/glossary. d#ed25519), [ECDSA secp256k1](../support-and-community/glossary.md#ecdsa-secp256k), o un ID de un [contrato inteligente](../support-and-community/glossary.md#smart-contract). El algoritmo correspondiente genera claves públicas y privadas únicas entre sí. La clave pública puede ser compartida y visible para otros usuarios de la red en un [Explorador de red](../support-and-community/glossary.md#network-explorer) o API REST. La [clave privada](../support-and-community/glossary.md#private-key) se mantiene en secreto y otorga acceso al propietario para modificar entidades (cuentas, tokens, etc.).

</details>

<details>

<summary>What happens if I lose my private key?</summary>

Las claves privadas sólo se pueden recuperar una vez perdidas si se crean con una frase de recuperación asociada a la que se puede acceder. Es crucial mantener sus claves privadas seguras y seguras ya que permiten el acceso a modificar sus entidades Hedera, como cuentas y tokens.

</details>
