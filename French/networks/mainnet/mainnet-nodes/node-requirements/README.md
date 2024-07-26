---
description: Le réseau principal d'Hedera est actuellement constitué de nœuds de consensus autorisés gérés par le Conseil des gouverneurs d'Hedera
---

# Exigences de nœud

Ce qui suit est fourni pour aider les membres [_Hedera Governing Council_](https://hedera.com/council) à déployer leur nœud de consensus principal autorisé. Veuillez noter que ces informations ne sont pas destinées à s'appliquer à la transition de Hedera vers un réseau sans permission.

## Exigences minimales de la plateforme de nœuds

Actuellement, le réseau principal de Hedera fonctionnera à un rythme déterminé par le nœud le plus performant. Pour assurer un niveau commun de performance matériel minimum, la connectivité et les exigences d'hébergement ont été définies pour la permission initiale, nœuds du Conseil des gouverneurs.

{% hint style="warning" %}
Pour assurer une conformité précise avec les exigences minimales, veuillez fournir le matériel de nœud, la connectivité et les détails d'hébergement à Hedera avant l'achat (devops@swirldslabs.com).
{% endhint %}

- CPU : compatible X86/X64 (Intel Xeon ou AMD EPYC) ; 24 coeurs / 48 threads satisfaisant ou dépassant les benchmarks suivants :
  - format@@0 Geekbench 6 single-core score
    - Minimum : 1000 ou plus
    - Recommandé : 1500 ou plus
  - Évaluation d'un seul fil de passe :
    - Minimum pour rester sur le réseau principal : 2300 ou plus
    - Recommandé : 2800 ou plus
- Connexion au réseau : une bande passante Internet de 1Gb/s soutenue via une seule interface Ethernet de 1 Gigabit / 10 Gigabit
- Mémoire : 256 Go PC4-21300 2666MHz DDR4 ECC enregistré DIMM ou plus rapide (minimum), 320 Go ou supérieur PC4-25600 3200MHz (recommandé)
- Stockage : Il est recommandé de monter 240 Go de SSD avec Raid 1 comme volume racine `/` puis de fournir un stockage utilisable via différents périphériques montés plus tard pendant l'installation. Cela peut ne pas être possible sur votre matériel, donc tout le stockage nécessaire peut être alloué au volume racine.
  - Minimum : 5To du stockage SSD NVMe utilisable
  - Recommandé :
    - 2 x 240 Go SSD avec RAID 1 pour le stockage système
    - 2 x Périphériques NVMe sous la forme d'un RAID 0 de 7,5 TB (ou 4x comme tableau RAID 10)
- Performances de stockage : Si le volume est monté à la racine, le volume racine doit répondre à ces exigences. Si le RAID est fourni via RAID, le tableau RAID doit répondre à ces exigences :
  - Écriture séquentielle maintenue :
    - Minimum : 2 000 Go/s
    - Recommandé : 3 000Go/s
  - Lecture séquentielle maintenue :
    - Minimum : 3 000 Go/s
    - Recommandé : 4 500 Go/s
  - Lecture aléatoire, synchronisée :
    - Minimum : 250,000 IOPS
    - Recommandé : 375.000 IOPS
  - Lecture aléatoire, AIO:
    - Minimum : 500,000 IOPS
    - Recommandé : 750 000 IOPS
  - Écriture aléatoire, synchronisée:
    - Minimum 100 000 IOPS
    - Recommandé : 150 000 IOPS
  - latence de lecture aléatoire inférieure à 200μs, moyenne
- Les nœuds doivent passer la suite de tests de performance Hedera effectuée au moment de l'installation

### **Système d'exploitation des nœuds :**

- Linux
  - Version minimale du noyau 3.10+
  - Prise en charge active de la version de support long terme
    - Ubuntu LTS 22.04
    - RHEL 8

### **Logiciel Node :**

- Docker Engine (`docker-ce` version 20.10.6)
  - Déployé avec les privilèges root
  - Support du conteneur privilégié activé (facultatif)
    - Si le support du conteneur privilégié est désactivé, alors la machine hôte doit exécuter le démon Havege
- Docker Compose (`docker-compose` version 1.29.2)
- Support IPTables (`linux-kernel` version 3.10+)
- Havege Daemon (`haveged` version 1.9.14)
  - Si le support du conteneur privilégié est activé, cette exigence est facultative
- Utilitaires HashDeep (`hashdeep` version 4.4)
  - Requis pour la validation de l'intégrité de la mise à jour
- Collecteur de Bindplane (`bindplane-collector` version 4+)
  - Requis pour la surveillance du journal des nœuds
- CLI JQ (`jq` version 1.5+)
  - Dépendance requise pour les outils de gestion des nœuds
- GNU CoreUtils (`coreutils` version 8.00+)
  - Dépendance requise pour les outils de gestion des nœuds
- cURL CLI (`curl` version 7.58.0+)
  - Dépendance requise pour les outils de gestion des nœuds
- InCron Daemon (`incron` version 0.5.12+)
  - Dépendance requise pour les outils de gestion des nœuds
  - Requis pour la mise à jour automatique du réseau
- Rsync CLI (`rsync` version 3.0.0+)
  - Dépendance requise pour les outils de gestion des nœuds
  - Requis pour la mise à jour automatique du réseau
- Outils de gestion des nœuds (version 0.1.0+ `node-mgmt-tools`
  - Mises à jour déployées via le processus de mise à jour du nœud
  - Doit être installé sur le chemin suivant : `/opt/hgcapp/node-mgmt-tools`
    - Le chemin doit être accessible en écriture et exécutable par le compte utilisateur `hgcadmin`

### **Comptes utilisateur système:**

- _**Compte logiciel de Node (obligatoire)**_
  - Caractéristiques de l'utilisateur
    - Nom: `hedera`
    - UID Unix : `2000`
    - Adhésion au groupe
      - Primaire: `hedera`
      - Secondaire : `admin` ou `wheel` _(selon la distribution Linux)_
    - Autorisations :
      - Lisez, écrivez et exécutez l'accès à l'arborescence du dossier `/opt/hgcapp`
  - Spécification du groupe
    - Nom: `hedera`
    - Unix GID: `2000`

{% hint style="info" %}
**Note :** Configurations de référence disponibles dans les annexes B, C, D
{% endhint %}

### Proxy

L'accès au noeud via des API publiques doit être médié par un proxy en ligne. Vous trouverez ci-dessous les spécifications de l'établissement de ce mandataire.

- 2- CPU core-x86/x64
- 2 Go de RAM
- 100 Go de stockage SSD
- Connectivité de réseau internet soutenue de 200 Mo/s avec adresse IP statique publique
- Docker supporté (Hedera pour fournir une image Docker avec HAProxy)

### Connectivité réseau

Connectivité des nœuds

- Connexion Internet 1Gbps – soutenue (non explosible)
  - Préférences illimitées
  - Déployé avec un accès coupe-feu à d'autres nœuds de consensus du réseau principal
- Noeud déployé dans un réseau DMZ dédié (isolé)
  - IP statique (FQDN n'est pas supporté)
  - Port TCP 50111 ouvert à 0.0.0.0/0
  - Port TCP 50211 ouvert à 0.0.0.0/0
  - Port TCP 50212 ouvert à 0.0.0.0/0
  - Port TCP 80 ouverture sortie à 0.0.0.0/0 (pour la connectivité du dépôt des paquets du système d'exploitation)
  - Port TCP 443 ouverture à 0.0.0.0/0 (pour la connectivité du dépôt des paquets d'OS)
  - Port UDP 123 Ouverture et sortie à 0.0.0.0/0 (pour la synchronisation du pool NTP en temps système)

Connectivité du proxy

- Adresse IP statique (FQDN non prise en charge)
- Connectivité internet 200Mo/s
- Port TCP 80 ouverture sortie à 0.0.0.0/0 (pour la connectivité du dépôt des paquets du système d'exploitation)
- Port TCP 443 ouverture à 0.0.0.0/0 (pour la connectivité du dépôt des paquets d'OS)
- Port TCP 50211 ouvert à 0.0.0.0/0
- Port TCP 50212 ouvert à 0.0.0.0/0

Lien d'interface (optionnel)

- Si vous utilisez un lien d'interface, notez que le TLS mutuel est utilisé, et que le routage basé sur la politique de Layer 3 (PBR) avec des double voies n'est pas pris en charge. Seul le lien de l'interface Layer 2 en utilisant le mode 1 (ports autonomes utilisant la sauvegarde active) ou le mode 4 (LACP 802.3ad active/active) est pris en charge.

### Hébergement

- Exigences d'hébergement standard pour la sécurité et la disponibilité
  - Centre de données de Tier 1
  - Conformité SSAE 16 /18, SOC 2 Type 2
- Hedera cherchera à éviter la duplication des fournisseurs d'hébergement entre les membres du Conseil

### Logiciel & Installation

- N'importe quelle distribution Linux de support à long terme 64 bits (LTS)
  - Distributions approuvées :
    - Ubuntu
    - Red Hat Entreprise
    - Oracle Linux
    - CentOS (uniquement jusqu'en 2023)

## Topologie du réseau /(Configuration du datacenter d'entreprise typique/)

![](../../../../.gitbook/assets/Network-topology.jpg)

## Étapes de déploiement

Les étapes suivantes décrivent le processus permettant aux membres du Conseil d'ajouter leur point de consensus au réseau principal.

1. Contact initial avec le membre du Conseil et l'entité d'hébergement de nœuds
   1. Identifier les personnes clés et les chefs de projet
   2. Établir une cadence régulière de réunions d'équipe de déploiement
2. Convélation des exigences techniques et discussion des options de déploiement
3. Acquisition de la plateforme de nœud
   1. Instance matérielle ou virtuelle
   2. Connectivité réseau
   3. Hébergement
4. Configuration du système d'exploitation sur la plateforme
   1. Mise à disposition des comptes comme indiqué
   2. Mise à disposition des accès réseau (listes de contrôle d'accès au pare-feu/règles de pare-feu)
5. Convélation des identifiants vers Hedera
   1. Comprend toutes les instructions spéciales pour l'accès autorisé, comme les VPNs
   2. Discussion sur le soutien et l'escalade entre les organisations
6. Hedera entreprend un examen de la configuration
   1. Plateforme
   2. Connectivité
7. Déploiement du logiciel de nœud de consensus Hedera et des bibliothèques de support nécessaires
8. Ajouter une configuration de connexion pour un réseau de test de performance Hedera
   1. Hedera exécute des tests fonctionnels, de stabilité et de performance pour tous les services réseau
9. Révision des résultats des tests et détermination de la préparation à la connectivité du réseau principal
   1. Examiner les documents clés de gestion relatifs aux comptes des membres du Conseil, y compris : compte de frais, compte de mise en valeur de procuration, et autres.
   2. Mettre à jour les clés privées à l'aide des outils fournis
10. Planifier la connexion au réseau principal
11. Réseau principal en direct
