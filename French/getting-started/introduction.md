# Introduction

Grâce à cette courte série _Getting Started_, vous apprendrez les bases de la création d'un compte, du transfert de HBAR, de la signature et de l'envoi des transactions vers le Testnet Hedera. Le réseau de test Hedera vous permet de jouer avec les API Hedera dans un environnement de non-production. Vous verrez à quel point il est facile de commencer avec l'un de nos [SDK Hedera](../sdks-and-apis/) dans le langage de programmation de votre choix. Voici deux chemins vers la création de comptes testnet et la réception de HBAR pour construire, tester et déployer des dApplications Hedera :

**➡️** [**Hedera Faucet**](introduction.md#hedera-faucet)

**➡️** [**Hedera Developer Portal**](introduction.md#hedera-developer-portal-profile)

***

### Hedera Faucet

Le robinet Hedera vous permet de recevoir de façon anonyme le HBAR testnet sans les tracas de la création d'un compte portail développeur. Pour utiliser le robinet anonyme, visitez le [robinet Hedera](https://portal.hedera. om/rocet) et connectez votre portefeuille, ou entrez une adresse de portefeuille EVM ou un identifiant de compte Hedera pour lancer le processus.

<figure><img src="../.gitbook/assets/faucet-receive-hbar.png" alt=""><figcaption></figcaption></figure>

La saisie d'une adresse EVM facilitera la création d'un compte en utilisant le [flux de création de compte automatique](../core-concepts/accounts/auto-account-creation.md#auto-account-creation-evm-addresss-alias). Copiez et enregistrez le nouvel ID de compte Hedera et la clé privée que vous gérez pour la configuration de votre environnement de codage sur la page suivante.

<figure><img src="../.gitbook/assets/faucet-success-account-id.png" alt=""><figcaption></figcaption></figure>

Le robinet a une limite de distribution maximale de **100 HBAR** toutes les 24 heures.

<figure><img src="../.gitbook/assets/faucet-wallet-timer.png" alt=""><figcaption></figcaption></figure>

Vous recevrez un message d'erreur et vous serez invité à le retourner lorsque votre compte sera admissible à une recharge si vous le faites plus d'une fois dans un délai de 24 heures.

<figure><img src="../.gitbook/assets/faucet-receive-error.png" alt=""><figcaption></figcaption></figure>

***

### Profil du portail développeur Hedera

Le portail de développement Hedera vous permet de créer un compte testnet pour recevoir HBAR lors de la création. Visitez le [portail développeur Heder](https://portal.hedera.com/register) et suivez les instructions pour créer un compte.

<figure><img src="../.gitbook/assets/portal testnet account.png" alt="Screenshot of the Hedera Developer portal (portal.hedera.com/register) account creation page."><figcaption></figcaption></figure>

Après la création du compte, votre compte portail testnet recevra automatiquement \*\*1000 HBAR, \* et vous verrez votre identifiant de compte et votre paire de clés à partir du tableau de bord du portail (voir l'image ci-dessous). Copiez l'ID de votre compte et la clé privée encodée par DER pour l'étape de configuration de l'environnement de codage.

<figure><img src="../.gitbook/assets/faucet-der-account-id.png" alt="" width="563"><figcaption></figcaption></figure>

{% hint style="info" %}
**Note** : Les comptes Testnet sur le portail développeur sont soumis à une limite quotidienne de 1000 HBAR. Les comptes _**ne sont pas**_ automatiquement rechargés. Pour maintenir votre solde, vous devez demander manuellement une recharge via le tableau de bord du portail toutes les 24 heures.

Pour plus de clarté, le rechargement n'ajoute pas 1000 HBAR au solde de votre compte chaque fois que vous rechargez. Au lieu de cela, le solde de votre compte est réapprovisionné jusqu'à 1000 HBAR s'il tombe en dessous de ce seuil. Par exemple, si votre solde de compte est de 500 HBAR, Le remplissage ne fera qu'ajouter suffisamment de HBAR pour ramener votre solde à 1000 HBAR, pas un 1000 HBAR supplémentaire.&#x20;
{% endhint %}
