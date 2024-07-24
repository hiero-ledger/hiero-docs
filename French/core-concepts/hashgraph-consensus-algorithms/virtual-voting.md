# Vote virtuel

Il ne suffit pas de faire en sorte que chaque membre connaisse chaque événement. Il est également\
nécessaire de convenir d'un ordre linéaire des événements, et donc des transactions\
enregistrées à l'intérieur des événements. La plupart des protocoles Byzantins de tolérance de défaut sans un leader\
dépendent des membres qui s'envoient mutuellement des votes. .Certains de ces protocoles\
nécessitent des reçus sur les votes envoyés à tout le monde. .Et ils\
peuvent nécessiter plusieurs tours de vote, ce qui augmente encore le nombre de messages\
de vote envoyés.

Cette approche pure du vote devient prohibitive et peu pratique dans un réseau de toute taille mais a les propriétés d'être la méthode la plus juste et la plus sûre pour parvenir à un consensus. L'algorithme de hashgraph implémente le vote qui obtient les mêmes propriétés équitables et sûres, mais est aussi très rapide et pratique. Il accomplit cela grâce au **vote virtuel**.

L'algorithme de hashgraph ne nécessite aucun vote à envoyer à travers le réseau pour calculer les votes de chaque membre. Les membres peuvent calculer les votes de chaque autre membre en regardant en interne chacune de leurs copies du hashgraphe et en appliquant l'algorithme de vote virtuel. Les votes sont calculés localement en fonction des ancêtres d'un événement donné.

Ce vote virtuel a plusieurs avantages. En plus d'économiser la bande passante, il garantit que les membres calculent toujours leurs votes selon les règles. Si Alice est honnête, elle calculera les votes virtuels pour le Bob virtuel qui sont honnêtes. Même si le vrai Bob est un tricheur, il ne peut pas attaquer Alice en faisant voter le Bob virtuel de manière incorrecte.

Avec cet algorithme de vote virtuel, l'accord byzantin est garanti.

Le vote virtuel se déroule en 3 étapes:

1. Diviser les manches
2. Décider de la renommée
3. Trouver une commande

## Diviser les manches

Pour commencer le processus de vote virtuel, nous devons d'abord définir des tours et des témoins. Dans l’histoire du hashgraphe, le premier événement pour le nœud d’un membre est le premier **témoin**. Le premier témoin est le début du premier tour \\(r\\) pour ce noeud. Tous les événements suivants font partie de ce premier tour jusqu'à ce qu'un nouveau témoin soit découvert. Un témoin est découvert lorsqu'un nœud crée un nouvel événement qui peut **voir fortement** 2<unk> 3 des témoins dans la ronde actuelle. Par exemple, événement w peut fortement voir l'événement x quand w peut tracer ses ancêtres à travers les relations parentales qui passent par d'autres événements qui résident sur au moins 2<unk> 3 des nœuds membres. Lorsqu'un événement est déterminé à voir fortement 2<unk> 3 du témoin de la ronde actuelle, cet événement est considéré comme le prochain témoin de ce noeud. Ce nouveau témoin est le premier événement de la ronde suivante \\(r+1\\) pour ce noeud. Chaque événement est assigné à une ronde car l'événement est ajouté au hashgraph.

```text
procédure divideRounds

pour chaque événement x
    r <- round max de parents de x (ou 1 si aucun n'existe)
    si x peut fortement voir plus de 2n/3 rond r témoins
        x. ound <- r+1
    else
      x.round <- r
    x. itness <- (x n'a pas d'auto-parent)
                ou (x. ound > x.selfParent.round)
```

## Décider de la renommée

La prochaine étape consiste à décider si un témoin est un témoin célèbre ou non. Un témoin est célèbre si de nombreux témoins du prochain tour peuvent le voir, et il n’est pas célèbre si beaucoup ne le peuvent pas. L'événement A peut **voir** l'événement B si l'événement B est un ancêtre de l'événement A. Lorsque nous décidons de la renommée du témoin A, nous devons regarder les témoins du tour suivant. Si les témoins de la série suivante peuvent voir le témoin A, ils sont considérés comme un vote en faveur de la renommée du témoin A. De même, si un témoin du prochain tour ne peut pas voir le témoin A, alors le vote de ce témoin est que le témoin A n’est pas célèbre. Pour que le témoin A soit considéré comme célèbre, un témoin futur doit être en mesure de voir avec force qu'au moins 2<unk> 3 des témoins votants ont voté en faveur du témoin A être célèbre. Si 2<unk> 3 des témoins de vote ont voté que le témoin A n'est pas célèbre, alors le témoin A sera décidé de ne pas être célèbre.

## Trouver une commande

Maintenant que nous avons calculé tous les témoins d'une ronde pour être célèbre ou pas célèbre, Nous pouvons déterminer l'ordre des événements qui ont eu lieu avant les fameux événements de témoin. Cela se fait en calculant :

1. La **ronde reçue** pour tous les événements qui n'ont pas encore été ordonnés et qui ont eu lieu avant une ronde où la renommée de tous les témoins a été décidée. La ronde de l'événement reçue est la première ronde où tous les témoins célèbres de cette ronde peuvent voir \\(ou sont descendants de\\) l'événement en question.
2. Le **timestamp** pour chaque événement. Cela se fait en rassemblant les ancêtres les plus anciens des célèbres témoins de la ronde reçue, qui sont également descendants de l'événement en question, et en prenant l'horodatage médian de ces événements collectés.
3. Le **tri des événements** d'abord par : arrondi reçu, horodatage du consensus, puis la signature.
