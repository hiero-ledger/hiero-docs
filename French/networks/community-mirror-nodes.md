---
description: Explorateurs de réseau hébergés par la communauté
cover: ../.gitbook/assets/Hero-Desktop-NetworkExplorers_2022-12-07-020704_ehza (1).webp
coverY: -209.48275862068965
---

# Explorateurs de réseau et outils

Les explorateurs de réseau Hedera sont des outils pour le suivi des activités sur le réseau Hedera. Les nœuds miroir fournissent des données en temps réel sur les transactions, tandis que les explorateurs de réseau offrent une interface conviviale pour visualiser et rechercher ces transactions.

Consultez quelques-uns des services d'explorateur de réseau hébergés par la communauté listés ci-dessous. Chaque explorateur de réseau hébergé par la communauté peut avoir ses propres caractéristiques et expériences.

<table data-view="cards"><thead><tr><th align="center"></th><th data-hidden></th><th data-hidden></th><th data-hidden data-card-cover data-type="files"></th><th data-hidden data-card-target data-type="content-ref"></th><th data-hidden></th><th data-hidden></th></tr></thead><tbody><tr><td align="center"><a href="https://explorer.arkhia.io/#/mainnet/dashboard"><mark style="color:purple;"><strong>RESEAU EXPLORER</strong></mark></a></td><td></td><td></td><td><a href="../.gitbook/assets/4 (1).png">4 (1).png</a></td><td><a href="https://explorer.arkhia.io/">https://explorer.arkhia.io/</a></td><td></td><td></td></tr><tr><td align="center"><a href="https://app.dragonglass.me/hedera/home"><mark style="color:purple;"><strong>RESEAU EXPLORER</strong></mark></a></td><td></td><td></td><td><a href="../.gitbook/assets/3 (1).png">3 (1).png</a></td><td><a href="https://app.dragonglass.me/hedera/home">https://app.dragonglass.me/hedera/home</a></td><td></td><td></td></tr><tr><td align="center"><mark style="color:purple;"><strong>RESEAU EXPLORER</strong></mark></td><td></td><td></td><td><a href="../.gitbook/assets/22.png">22.png</a></td><td><a href="https://hashscan.io/">https://hashscan.io/</a></td><td></td><td></td></tr><tr><td align="center"><a href="https://hederaexplorer.io/"><mark style="color:purple;"><strong>RESEAU EXPLORER</strong></mark></a></td><td></td><td></td><td><a href="../.gitbook/assets/23.png">23.png</a></td><td><a href="https://hederaexplorer.io/">https://hederaexplorer.io/</a></td><td></td><td></td></tr><tr><td align="center"><p><a href="https://explore.lworks.io/"><mark style="color:purple;"><strong>RESEAU EXPLORER</strong></mark></a></p><p><a href="https://www.lworks.io/"><mark style="color:purple;"><strong>EXPLORER TOOL</strong></mark></a></p></td><td></td><td></td><td><a href="../.gitbook/assets/7.png">7.png</a></td><td><a href="https://explore.lworks.io/">https://explore.lworks.io/</a></td><td></td><td></td></tr></tbody></table>

## Foire Aux Questions

<details>

<summary><mark style="color:blue;"><strong>Comment rechercher une transaction ?</strong></mark></summary>

Pour rechercher une transaction spécifique, vous pouvez utiliser l'ID unique de la transaction.

L'ID de la transaction devrait ressembler à ceci : `0.0.48750443@1671560120.085845879`

</details>

<details>

<summary><mark style="color:blue;"><strong>Comment puis-je obtenir l'ID de la transaction ?</strong></mark></summary>

L'ID de la transaction peut être généré automatiquement par le SDK, créé manuellement et associé à une transaction, ou obtenu à partir de la réception ou du dossier après le traitement de la transaction. Il sert d'identifiant unique pour la transaction et peut être utilisé pour rechercher et voir ses détails.

</details>

<details>

<summary><mark style="color:blue;"><strong>Comment rechercher une entité (compte, sujet, jetons, contrats intelligents)?</strong></mark></summary>

Vous pouvez effectuer une recherche par l'ID unique de l'entité que vous recherchez. Le format de l'ID de l'entité est `0.0.entityNumber`.

Par exemple, `0.2` est un ID de compte et vous recherchez ce compte en utilisant cet ID.

</details>

<details>

<summary><mark style="color:blue;"><strong>Comment puis-je obtenir l'ID de l'entité ?</strong></mark></summary>

Les identifiants des entités sont retournés à la réception de la transaction qui les a créés. Les entités comprennent des comptes, des sujets, des contrats intelligents, des calendriers et des jetons.
\
Par exemple, si vous créez un nouveau compte en utilisant `AccountCreateTransaction` dans le SDK, vous pouvez obtenir le nouvel ID de compte à partir du reçu de la transaction.

</details>

<details>

<summary><mark style="color:blue;"><strong>Puis-je héberger mon propre explorateur réseau Hedera ?</strong></mark></summary>

Oui, vous pouvez! Vous pouvez créer votre propre explorateur de réseau Hedera personnalisé en utilisant les [API REST de Node miroir](../sdks-and-apis/rest-api. d) ou jetez un coup d'oeil au projet open-source [Hedera Mirror Node Explorer](https://github.com/hashgraph/hedera-mirror-node-explorer).

</details>

<details>

<summary><mark style="color:blue;"><strong>Comment puis-je ajouter un explorateur de réseau à cette page ?</strong></mark></summary>

Pour ajouter un explorateur de réseau à cette page, reportez-vous au [guide de contribution](../support-and-community/contributing-guide.md) et ouvrez un problème dans le `hedera-docs` [repository](https://github.com/hashgraph/hedera-docs). Veuillez inclure les informations suivantes dans le problème :

- Nom de l'explorateur de réseau
- Lien vers l'explorateur de réseau
- Logo haute résolution

</details>
