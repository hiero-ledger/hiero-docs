---
description: Exploradores de red alojados por la comunidad
cover: ../.gitbook/assets/Hero-Desktop-NetworkExplorers_2022-12-020704_ehza (1).webp
coverY: -209.48275862068965
---

# Exploradores y Herramientas de Red

Los exploradores de redes de Hedera son herramientas para rastrear la actividad en la red de Hedera. Los nodos de espejo proporcionan datos en tiempo real sobre las transacciones, mientras que los exploradores de red ofrecen una interfaz fácil de usar para ver y buscar esas transacciones.

Echa un vistazo a algunos de los servicios de explorador de redes alojados en la comunidad listados a continuación. Cada explorador de red alojado en la comunidad puede tener sus propias características y experiencia únicas.

<table data-view="cards"><thead><tr><th align="center"></th><th data-hidden></th><th data-hidden></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden></th><th data-hidden></th></tr></thead><tbody><tr><td align="center"><a href="https://explorer.arkhia.io/#/mainnet/dashboard"><mark style="color:purple;"><strong>EXPLORER DE RED</strong></mark></a></td><td></td><td></td><td><a href="../.gitbook/assets/4 (1).png">4 (1).png</a></td><td><a href="https://explorer.arkhia.io/">https://explorer.arkhia.io/</a></td><td></td><td></td></tr><tr><td align="center"><a href="https://app.dragonglass.me/hedera/home"><mark style="color:purple;"><strong>EXPLORER DE RED</strong></mark></a></td><td></td><td></td><td><a href="../.gitbook/assets/3 (1).png">3 (1).png</a></td><td><a href="https://app.dragonglass.me/hedera/home">https://app.dragonglass.me/hedera/home</a></td><td></td><td></td></tr><tr><td align="center"><mark style="color:purple;"><strong>EXPLORER DE RED</strong></mark></td><td></td><td></td><td><a href="../.gitbook/assets/22.png">22.png</a></td><td><a href="https://hashscan.io/">https://hashscan.io/</a></td><td></td><td></td></tr><tr><td align="center"><a href="https://hederaexplorer.io/"><mark style="color:purple;"><strong>EXPLORER DE RED</strong></mark></a></td><td></td><td></td><td><a href="../.gitbook/assets/23.png">23.png</a></td><td><a href="https://hederaexplorer.io/">https://hederaexplorer.io/</a></td><td></td><td></td></tr><tr><td align="center"><p><a href="https://explore.lworks.io/"><mark style="color:purple;"><strong>EXPLORER DE RED</strong></mark></a></p><p><a href="https://www.lworks.io/"><mark style="color:purple;"><strong>EXPLORER TOOL</strong></mark></a></p></td><td></td><td></td><td><a href="../.gitbook/assets/7.png">7.png</a></td><td><a href="https://explore.lworks.io/">https://explore.lworks.io/</a></td><td></td><td></td></tr></tbody></table>

## FAQ

<details>

<summary><mark style="color:blue;"><strong>¿Cómo busco una transacción?</strong></mark></summary>

Para buscar una transacción específica, puede utilizar el ID de transacción único.

El ID de transacción debería verse algo así: `0.0.48750443@1671560120.085845879`

</details>

<details>

<summary><mark style="color:blue;"><strong>¿Cómo obtengo el ID de la transacción?</strong></mark></summary>

El ID de transacción puede ser generado automáticamente por el SDK, creado manualmente y asociado con una transacción, o obtenido del recibo o registro después de que la transacción haya sido procesada. Sirve como un identificador único para la transacción y se puede utilizar para buscar y ver sus detalles.

</details>

<details>

<summary><mark style="color:blue;"><strong>How do I search for an entity (account, topic, tokens, smart contracts)?</strong></mark></summary>

Puede buscar por el ID único de la entidad que está buscando. El formato ID de la entidad es `0.0.entityNumber`.

Por ejemplo, `0.0.2` es un ID de cuenta y buscas esa cuenta usando ese ID.

</details>

<details>

<summary><mark style="color:blue;"><strong>¿Cómo obtengo el ID de la entidad?</strong></mark></summary>

Los identificadores de entidad son devueltos en el recibo de la transacción que los creó. Entities include accounts, topics, smart contracts, schedules, and tokens.\
\
For example, if you create a new account using the `AccountCreateTransaction` in the SDK, you can get the new account ID from the transaction receipt.

</details>

<details>

<summary><mark style="color:blue;"><strong>¿Puedo alojar a mi propio explorador de red Hedera?</strong></mark></summary>

¡Sí, puedes! Puedes crear tu propio explorador de red Hedera personalizado usando la [API REST del nodo espejo](../sdks-and-apis/rest-api. d) o echa un vistazo al proyecto open-source [Hedera Mirror Node Explorer](https://github.com/hashgraph/hedera-mirror-node-explorer) .

</details>

<details>

<summary><mark style="color:blue;"><strong>¿Cómo puedo añadir un explorador de red a esta página?</strong></mark></summary>

Para añadir un explorador de red a esta página, consulte la [guía de contribución](../support-and-community/contributing-guide.md) y abra un problema en `hedera-docs` [repository](https://github.com/hashgraph/hedera-docs). Por favor, incluya la siguiente información en el problema:

- Nombre del explorador de red
- Enlace al explorador de red
- Logo de alta resolución

</details>
