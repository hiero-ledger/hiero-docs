# Configuration multi-nœuds

## Utiliser la configuration multi-nœuds

La configuration multi-nœuds est une fonctionnalité avancée conçue pour des scénarios spécifiques qui nécessitent plusieurs nœuds de consensus. Cette configuration requiert des ressources plus élevées et implique plus de complexité, ce qui la rend idéale pour les environnements de test et de développement. Avant d'essayer d'utiliser la configuration du multinoeud, il est crucial de s'assurer que le noeud local fonctionne correctement en mode mono-noeud par défaut.

<details>

<summary><strong>Exigences de mode multinoeud</strong></summary>

Pour exécuter le mode multinoeud, assurez-vous que les configurations suivantes sont définies au minimum dans Docker **Paramètres** -> **Ressources** et qu'au moins 14 Go de mémoire sont disponibles pour Docker:

- **CPUS :** 6
- **Mémoire :** 14 Go
- **Échange :** 1 Go
- **Taille de l'image du disque :** 64 Go

<img src="../../.gitbook/assets/localnode-multinode-requirements.png" alt="" data-size="original">

</details>

{% hint style="info" %}
_**📣 Note** : Créer un réseau décentralisé où chaque nœud fonctionne indépendamment sur sa propre machine n'est pas pris en charge. Néanmoins, des capacités avancées de réseautage et de configuration sont disponibles, permettant aux nœuds de communiquer les uns avec les autres similaires à leurs interactions sur le réseau principal d'Hedera._
{% endhint %}

#### **Démarrage du mode multi-nodes**

Pour démarrer Hedera Local Node en mode multinode, ajoutez le drapeau `--multinode` à votre [commande de démarrage](single-node-configuration.md#npm). Par exemple :

```bash
# npm commande pour démarrer le réseau local en mode multinode
npm run start -- -d --multinode

# docker commande pour démarrer le réseau local en mode multinode
docker compose up -d --multinode
```

Vérifiez le lancement réussi du mode multi-nœuds en inspectant la sortie Docker de `docker ps --format "table {{.Names}}" | réseau grep` ou le tableau de bord Docker Desktop. Vous devriez identifier quatre nœuds en cours d'exécution :

```bash
nœud réseau
network-node-1
network-node-2
network-node-3
```

_📣 **Note**: En mode multinode, vous avez besoin d'au moins trois nœuds sains pour le réseau opérationnel._

#### **Démarrage et arrêt des nœuds**

Les nœuds individuels peuvent être démarrés ou arrêtés pour tester les processus de consensus, de synchronisation et de sélection de nœuds en utilisant les commandes de gestion `npm` ou `docker`: &#x20

<details>

<summary><strong>commandes npm</strong></summary>

```bash
# npm commande pour démarrer un noeud individuel
npm run start network-node-3

# npm commande pour arrêter un noeud individuel
npm run stop network-node-3

# npm commande pour redémarrer un noeud individuel
npm run restart network-node-3
```

</details>

<details>

Les commandes <summary><strong>docker</strong></summary>

```bash
# Commande Docker pour démarrer un noeud individuel
docker compose start network-node-3

# Commande Docker pour arrêter un noeud individuel
docker compose stop network-node-3

# Commande Docker pour redémarrer un noeud individuel
docker compose restart network-node-3

# Commande Docker pour vérifier les logs de chaque noeud
docker compose logs network-node-3 -f

# Commande Docker pour arrêter le réseau local et supprimer les conteneurs
docker compose vers le bas
```

</details>

Alternativement, exécutez `docker compose down -v; git clean -xfd; git reset --hard` pour arrêter le noeud local et le réinitialiser à son état d'origine.

#### Schéma du mode multi-nœud

Le diagramme suivant illustre l'architecture et le flux des données en mode multinoeuds.

<figure><img src="../../.gitbook/assets/multinode-diagram.jpeg" alt="" width="535"><figcaption><p>Schéma en mode multinœud</p></figcaption></figure>
