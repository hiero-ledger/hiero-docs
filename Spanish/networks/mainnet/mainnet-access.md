---
cover: ../../.gitbook/assets/HH-Eco-Cat-Hero-Desktop-R1 (1).webp
coverY: -625.8620689655172
---

# Cuentas Mainnet

Para interactuar con y acceder a los diversos servicios de Hedera Mainnet, tales como cuentas, temas, tokens, archivos y contratos inteligentes, necesitará una cuenta Hedera. Su cuenta Hedera también tiene un saldo de HBAR, que puede ser utilizado para hacer pagos de comisiones de transacción o transferencias a otras cuentas.

Crear cuentas gratuitas de red principal visitando cualquiera de estos proveedores de cartera:

<table data-card-size="large" data-view="cards"><thead><tr><th align="center"></th><th></th><th></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td align="center"><a href="https://atomicwallet.io/"><mark style="color:purple;"><strong>ATÓMICA</strong></mark></a></td><td>✅ Clave privada visible</td><td></td><td><a href="../../.gitbook/assets/Screenshot 2022-12-20 at 2.39.29 PM (1).png">Captura de pantalla de 2022-12 a las 2.39.29 PM (1).png</a></td><td><a href="https://atomicwallet.io/">https://atomicwallet.io/</a></td></tr><tr><td align="center"><a href="https://www.bladewallet.io/"><mark style="color:purple;"><strong>BLADE</strong></mark></a></td><td><p>✅ Clave privada visible</p><p>✅ Contraseña compatible con SDK</p></td><td></td><td><a href="../../.gitbook/assets/blade-wallet-logo.png">blade-wallet-logo.png</a></td><td><a href="https://www.bladewallet.io/">https://www.bladewallet.io/</a></td></tr><tr><td align="center"><a href="https://www.coinomi.com/en/"><mark style="color:purple;"><strong>COINOMI</strong></mark></a></td><td>✅ Contraseña compatible con SDK</td><td></td><td><a href="../../.gitbook/assets/coinomi-logo.png">coinomi-logo.png</a></td><td><a href="https://www.coinomi.com/en/">https://www.coinomi.com/es/</a></td></tr><tr><td align="center"><a href="https://www.exodus.com/hedera-wallet-hbar"><mark style="color:purple;"><strong>EXODUS</strong></mark></a></td><td>✅ Clave privada visible</td><td></td><td><a href="../../.gitbook/assets/Screenshot 2022-12-20 at 3.11.05 PM.png">Captura de pantalla de 2022-12 a 3.11.05 PM.png</a></td><td><a href="https://www.exodus.com/hedera-wallet-hbar">https://www.exodus.com/hedera-wallet-hbar</a></td></tr><tr><td align="center"><a href="https://guarda.com/"><mark style="color:purple;"><strong>GUARDA</strong></mark></a></td><td>✅ Clave privada visible</td><td></td><td><a href="../../.gitbook/assets/GUARDA.png">GUARDA.png</a></td><td><a href="https://guarda.com/">https://guarda.com/</a></td></tr><tr><td align="center"><a href="https://www.hashpack.app/"><mark style="color:purple;"><strong>HASHPACK</strong></mark></a></td><td><p>✅ Clave privada visible</p><p>✅ Contraseña compatible con SDK</p></td><td></td><td><a href="../../.gitbook/assets/HASHPACK.png">HASHPACK.png</a></td><td><a href="https://www.hashpack.app/">https://www.hashpack.app/</a></td></tr><tr><td align="center"><a href="https://www.kabila.app/"><mark style="color:purple;"><strong>KABILA</strong></mark></a></td><td><p>✅ Clave privada visible</p><p>✅ Contraseña compatible con SDK</p></td><td></td><td><a href="../../.gitbook/assets/kabila-docs-logo.png">kabila-docs-logo.png</a></td><td><a href="https://www.kabila.app/">https://www.kabila.app/</a></td></tr><tr><td align="center"><a href="https://wallawallet.com/"><mark style="color:purple;"><strong>WALLAWALLET</strong></mark></a></td><td><p>✅ Clave privada visible</p><p>✅ Contraseña compatible con SDK</p></td><td></td><td><a href="../../.gitbook/assets/WALLA (1).png">WALLA (1).png</a></td><td><a href="https://wallawallet.com/">https://wallawallet.com/</a></td></tr></tbody></table>

| Característica                  | Descripción                                                                                                                                                           |
| ------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ✅ Clave privada visible         | Tiene acceso a la clave privada asociada con la cuenta principal de la cartera creada para usted                                                                      |
| ✅ Contraseña compatible con SDK | La contraseña creada por el monedero es compatible con los SDKs y se puede utilizar para recuperar las claves privadas de la cuenta que el monedero creado para usted |

### Crear nuevas cuentas de red principal

Una vez que haya obtenido su cuenta de red principal de una cartera soportada, puede utilizar los SDKs para crear cuentas adicionales de red principal.

Para hacer esto, necesitarás apuntar tu cliente Hedera a mainnet (`Client.forMainnet()`)y usar la API `AccountCreateTransaction` para crear una nueva cuenta. El pagador de comisión de transacción (denominado `operator` en los SDKs) la información debe establecerse en la cuenta de red principal que creaste a partir de una de las billeteras anteriores (`setOperator(<mainnetAccountId, mainnetAccountPrivateKey)`).

{% content-ref url="../../sdks-and-apis/sdks/accounts-and-hbar/create-an-account.md" %}
[create-an-account.md](../../sdks-and-apis/sdks/accounts-and-hbar/create-an-account.md)
{% endcontent-ref %}
