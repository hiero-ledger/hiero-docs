# Configuration du nœud unique

## Utilisation de la configuration d'un nœud unique

La configuration à un seul nœud simule les fonctions du réseau à plus petite échelle (nœud unique), idéale pour le débogage, le test et le développement de prototypes.

<details>

<summary><strong>Exigences de mode à un nœud simple</strong></summary>

Assurez-vous que l'implémentation **`VirtioFS`** de partage de fichiers est activée dans les paramètres du docker.

![](<../../.gitbook/assets/docker-compose-settings (1).png>)

Assurez-vous que les configurations suivantes sont définies au minimum dans Docker **Paramètres** -> **Ressources** et sont disponibles pour utilisation :

**CPUS :** 6

**Mémoire :** 8Go

**Échange :** 1 Go

**Taille de l'image du disque :** 64 Go

![](../../.gitbook/assets/docker-settings.png)

Assurez-vous que le **`Autoriser l'utilisation des sockets Docker par défaut (nécessite un mot de passe)`** est activé dans Docker **Paramètres -> Avancé**.

![](../../.gitbook/assets/docker-socket-setting.png)

**Note:** L'image peut sembler différente si vous êtes sur une version différente

</details>

#### **Démarrage et arrêt du nœud**

Avant de lancer les commandes réseau, confirmez que Docker est installé et ouvert sur votre machine. Pour arrêter votre nœud local, utilisez les commandes suivantes `npm` ou `docker`. Avant de procéder à cette opération, assurez-vous de sauvegarder tous les fichiers créés manuellement dans le répertoire de travail.

<details>

<summary><strong>commandes npm</strong></summary>

{% code overflow="wrap" %}

```bash
# npm commande pour démarrer le réseau local et générer des comptes en mode détaché
npm run start -- -d

# npm commande pour arrêter
npm run stop

# npm commande npm pour redémarrer le noeud
npm run redémarrer
```

{% endcode %}

</details>

<details>

Les commandes <summary><strong>docker</strong></summary>

```bash
# Commande Docker pour démarrer le réseau local. Ne génère pas les comptes
docker compose up -d

# Commande Docker pour arrêter les services
docker compose stop

# Commande Docker pour redémarrer le réseau local
docker compose restart

# Commande Docker pour arrêter le réseau local et supprimer les conteneurs
docker composer vers le bas
```

</details>

Alternativement, exécutez `docker compose down -v; git clean -xfd; git reset --hard` pour arrêter le noeud local et le réinitialiser à son état d'origine. La liste complète des commandes disponibles peut être trouvée [here](https://github.com/hashgraph/hedera-local-node?tab=readme-ov-file#commands).

#### Schéma du mode à un nœud

Le diagramme suivant illustre l'architecture et le flux des données en mode noeud unique.

<figure><img src="../../.gitbook/assets/localnet-single-node-diagram.png" alt="" width="563"><figcaption><p>Mode nœud unique schéma</p></figcaption></figure>
