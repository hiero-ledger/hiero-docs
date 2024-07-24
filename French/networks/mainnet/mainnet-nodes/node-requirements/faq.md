---
description: Questions courantes pour les nœuds de consensus du réseau principal autorisés de Hedera actuel
---

# Foire Aux Questions

## Quels sont les protocoles de sécurité et les tailles de clés utilisés par le hashgraphe ?

Les nœuds utilisent les clés TLS 1.2 DH RSA 3k et SHA384 pour sécuriser les communications entre les nœuds. Notre objectif est de satisfaire la CNSA suite1 telle que spécifiée par le gouvernement américain. TLS 1.2 utilise AES 256 éphémère pour un secret parfait vers l'avant. Différentes clés sont utilisées pour l'échange de clés TLS et une autre pour les signatures. Les clients utilisent ED25519 pour signer des transactions.

## Un nœud de consensus supporte-t-il la liaison ou le fractionnement de la circulation et de la circulation ?

Hashgraph ne prend pas en charge la liaison ou le fractionnement du trafic d'entrées et d'évacuation.

## Le nœud de consensus a-t-il besoin d'accéder à notre réseau interne ?

Le nœud de consensus Hedera n'a pas besoin d'accéder à des ressources internes et doit être séparé du reste du réseau d'entreprise, idéalement dans sa propre DMZ (Zone Demilitarisée).

## Le nœud de consensus doit-il être sauvegardé ?

Les sauvegardes spécifiques à l'application ne sont pas nécessaires. Puisque le réseau Hedera continue à traiter les transactions lorsque le noeud échoué est en panne, les sauvegardes restaurées seront obsolètes au moment de leur récupération. La redondance vient des autres noeuds et le noeud récupéré sera resynchronisé par le logiciel hashgraph.

On s'attend à ce que des procédures normales de sauvegarde soient en place pour le niveau du système d'exploitation afin de permettre une reprise rapide et cohérente en cas de catastrophe, y compris des pannes matérielles et des situations similaires.

## Quelles sont les exigences de SLA et opérationnelles du point de consensus ?

L'entente de LLC précise que « alors que l'objectif initial souhaité devrait être une disponibilité d'au moins 99,5 %, y compris des fenêtres de maintenance technique/opérationnelle planifiées ».

Une surveillance externe sera disponible depuis Hedera pour notification de panne.
