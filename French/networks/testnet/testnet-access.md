# Comptes Testnet

Vous aurez besoin d'un compte Hedera **Testnet** ou **Previewnet** pour interagir avec tous les services réseau (cryptomonnaie, consensus, jetons, fichiers et contrats intelligents). Votre compte Hedera Testnet détient un solde de HBAR pour les transferts vers d'autres comptes ou les paiements pour les services de réseau.

### Étape 1 : Créer un profil de Portail Hedera

Pour créer votre profil du portail Hedera, inscrivez [here](https://portal.hedera.com/register) et complétez votre profil. Une fois que vous avez terminé la configuration de votre profil, sélectionnez le réseau de test (Testnet ou Previewnet) dans le menu déroulant du réseau et créez un compte. Après la création de votre compte, votre compte portail recevra automatiquement 1000 HBAR.&#x20

Vous pouvez facilement copier vos informations `accountId`, `public key`, et `private key` dans votre presse-papiers pour les utiliser lors de la configuration de votre environnement SDK pour testnet.&#x20

{% hint style="info" %}
_**Note :** Lorsque previewnet ou testnet est réinitialisé, les nouveaux identifiants de compte seront générés. La paire de clés publique et privée reste cohérente lors des réinitialisations de prévisualisation et de testnet. Si vous recevez une réponse d'identifiant de compte invalide de la part du réseau, il est probable que vous deviez mettre à jour votre identifiant de compte previewnet ou testnet. [Créer un jeton d'accès personnel/clé API_](../.. tutoriels/more-tutorials/how-to-create-a-personal-access-token-api-key-on-the-hedera-portal.md) _pour rationaliser le processus de récréation et de gestion de compte lorsqu'il y a une réinitialisation du réseau._&#x20;
{% endhint %}

![](../../.gitbook/assets/portal-testnet-dashboard.png)

Vous êtes maintenant prêt à construire votre application sur testnet !
