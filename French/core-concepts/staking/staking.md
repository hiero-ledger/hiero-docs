# Programme de Staking

La fonction de mise en jeu sera déployée en quatre phases. Les deux premières phases sont décrites ci-dessous, et les deux dernières phases seront disponibles au début de la phase I.

## **Phase I : Disponibilité technique \[Complete]**

La fonctionnalité de staking est maintenant disponible et en direct sur le réseau Hedera Testnet et le réseau principal depuis le 21 juillet 2022. En phase I, les utilisateurs seront techniquement en mesure de mettre leur compte sur les nœuds du réseau principal, mais cela ne contribuera pas au poids du consensus d'un nœud (pouvoir de vote). Cette mise à disposition technique initiale ne récompense pas les participants pour leur mise en jeu, mais permet des conditions de jeu équitables grâce auxquelles tous les acteurs du marché ont la possibilité de rejoindre le programme de mise en jeu et évite de donner un avantage injuste aux premiers participants.

## **Phase II : Développement des écosystèmes \[Complete]**

Pendant cette phase, les échanges et les portefeuilles pris en charge seront en mesure d'intégrer la fonctionnalité de staking pour fournir aux détenteurs d'un compte un moyen facile de mettre leur HBAR, mais ne distribuera pas de récompenses. En outre, des applications web pour déléguer des participations seront probablement construites pour être utilisées par l'écosystème de détail. Pendant cette phase, il y aura une visibilité de la mise en jeu par nœud, et le stating vers un noeud affectera son poids de consensus (pouvoir de vote) avec des mises à jour mensuelles.

## **Phase III: lancement du programme de récompenses \[Complete]**

Le Conseil des gouverneurs d’Hedera déterminera quand l’écosystème Hedera aura atteint un ensemble minimum d’intégrations viables pour permettre des récompenses de mise en jeu. Une fois que cela sera déterminé, le Conseil (par l'intermédiaire de CoinCom) votera pour mettre à jour le taux de récompense, et, par la suite, le réseau principal sera mis à jour avec le taux de récompense convenu. Le dernier taux de récompense du staking voté par CoinComm peut être trouvé [here](https://hedera.com/blog/hedera-governing-council-votes-to-approve-changes-to-staking-algorithm).
\
Une fois mis à jour, le compte de récompense de staking (0.0. 00) sera éligible à distribuer les récompenses gagnées par les stakers, une fois que le seuil de récompense de 250M de HBAR total aura été atteint. Les récompenses continueront d'être distribuées même si, après ce délai, le solde du compte 0,0.800 est inférieur à 250M.

## Phase IV : Implémentation de la prise complète

Dans cette phase, les mises à jour de 24 heures seront publiées pour la visibilité dans le jeu par noeud et la fonction de disponibilité des nœuds sera publiée. Cela signifie qu'au lieu de mettre à jour mensuellement la visibilité des mises en jeu des noeuds, la visibilité des mises en jeu des noeuds sera mise à jour sur un intervalle de 24 heures. Lorsque la fonction de disponibilité prend effet, les comptes misés ne gagneront pas de récompenses lorsque les nœuds ne peuvent pas participer au consensus (indisponibles ou déconnectés).

## **Noeuds de prise**

{% hint style="info" %}
Le Conseil de Gouvernement d'Hedera a voté pour changer la valeur minimale de mise en jeu de la moitié de la valeur de mise en jeu de nœud max à 1/4 de la valeur de mise en jeu de nœud maximum.
{% endhint %}

Tous les nœuds de consensus gérés par le Conseil de Gouvernement d'Hedera distribuent des récompenses aux comptes qui leur ont été attribués. Vous pouvez trouver des informations sur chaque nœud du réseau en visitant l'un des explorateurs du réseau Hedera ou en obtenant le [carnet d'adresses](../../sdks-and-apis/rest-api.md#api-v1-network-nodes). À l’avenir, la participation au réseau s’ouvrira aux nœuds de la communauté et, à terme, au public dans le cadre des efforts de décentralisation de Hedera.

Les nœuds ont un **minimum de jeu** et un **maximum de parties**. La mise en jeu minimale du nœud doit être remplie pour que les comptes misés sur ce nœud soient éligibles pour gagner des récompenses de mise en jeu. Les jetons absorbés qui dépassent la mise en jeu maximale n'affecteront plus la proportion de récompenses retournées. Le seuil de mise en jeu maximum pour chaque noeud sera le nombre total de HBAR divisé par le nombre total de nœuds dans le réseau. La valeur minimale du seuil de mise en jeu du noeud sera 1/4 de la valeur maximale de mise en jeu du noeud. Ces valeurs changeront au fur et à mesure que de nouveaux nœuds seront ajoutés au réseau ou pourront être modifiés par vote du Conseil de Gouvernement de Hedera.

#### Exemple:

Stade minimum : 50 000 000 000 barres de haut\*(1/26nodes)\*(1/4)

Stake Maximum : 50 000 000 000 barres de haut\*(1/26nodes)

## **Période de verrouillage**

Il n'y a **pas de période de verrouillage** lorsque les comptes sont mis sur un nœud. Les Stakers n'ont pas besoin de choisir un montant de HBAR pour miser à partir de leur compte. Le solde entier du compte est automatiquement misé sur le noeud ou le compte sélectionné. Il n'y a pas de concept de "liaison" ou de "découper" de vos jetons. Le solde du compte misé est liquide en tout temps.

## **Compte de Récompense de Staking**

Le compte de récompense de staking distribue des récompenses aux comptes misés éligibles. L'ID du compte de récompense de staking est [0.0.800](https://hashscan.io/#/mainnet/account/0.0.800?type=) sur le réseau principal. N'importe qui dans la communauté peut contribuer au pool de récompenses en transférant le HBAR sur ce compte. Ce compte n'a pas de clés, et par conséquent, tout HBAR transféré sur ce compte ne peut pas être retourné au propriétaire. Si vous choisissez de contribuer au pool de récompenses, assurez-vous de vérifier les détails de votre transaction de transfert.

Le compte de récompense de mise en jeu doit atteindre un solde minimum avant que les récompenses puissent commencer à distribuer les récompenses gagnées sur les comptes misés éligibles. Le seuil minimal de solde du HBAR pour le compte de récompense est de 250 millions de HBAR votés par le Conseil des gouverneurs de Hedera. Si ce solde n'est pas atteint, les récompenses de staking ne seront pas distribuées. Vous pouvez voir le solde de ce compte en visitant l'un des explorateurs du réseau Hedera.

Une fois le seuil minimum atteint, les récompenses continueront d'être distribuées sur les comptes misés tant qu'il y aura un solde dans le compte de récompense même si elle tombe en dessous du seuil minimal initial. Le taux de récompense sera initialement fixé à zéro. Le Conseil de Gouvernement d'Hedera votera et mettra à jour le taux de récompense lorsque le programme de récompense de Hedera sera en ligne. Le dernier taux de récompense peut être trouvé [here](https://hedera.com/blog/hedera-governing-council-votes-to-approve-changes-to-staking-algorithm).&#x20

## \*\*Récompenses de Staking \*\*

Dans la phase I, le taux de récompense de staking sera initialement nul. Le Conseil des gouverneurs d’Hedera déterminera quand l’écosystème Hedera aura atteint un ensemble minimum d’intégrations viables pour permettre des récompenses de mise en jeu. Une fois que cela sera déterminé, le Conseil (par l'intermédiaire de CoinCom) votera pour mettre à jour le taux de récompense, et, par la suite, le réseau principal sera mis à jour avec le taux de récompense convenu.

Tout compte peut choisir de miser sur un noeud ou un autre compte. La **période minimale de mise en jeu** est le minimum de temps qu'un compte doit être misé sur un nœud de consensus avant que le compte ne soit éligible pour gagner des récompenses. La période minimale de mise en jeu est **un jour (24 heures).** La période de mise en jeu commence à minuit UTC et se termine à minuit UTC. La période de mise en valeur est définie par le Conseil d’administration de Hedera. Les récompenses gagnées ne sont pas transférées sur le compte misé immédiatement après qu'un compte a été misé pour une période de mise complète. Veuillez consulter la section Distribution des récompenses de prise pour connaître les scénarios qui déclenchent le paiement d'une récompense.

Les comptes misés pour une période inférieure à la période minimale de mise en jeu définie ne sont pas éligibles pour gagner des récompenses pour cette période. Les nœuds et les comptes accumulent des enjeux et des récompenses pour tout le RAPB. Les fractions sont arrondies vers le bas.

Pour qu'un compte misé soit éligible pour gagner des récompenses, ce qui suit doit être vrai :

- Le compte de récompense de staking doit avoir atteint le solde de seuil initial de HBAR
  - Une fois la valeur seuil minimale atteinte, le compte de récompenses continuera à récompenser les comptes misés même si le solde tombe en dessous du seuil initial
- Le compte pour lequel le noeud est misé correspond au seuil de mise en jeu minimum
- Le compte doit être misé pour la période de mise minimum
- Le taux de récompense est voté par le Conseil des gouverneurs d'Hedera et mis à jour sur le réseau principal

Les récompenses continueront d'être gagnées lorsqu'un nœud est à la baisse ou inactif dans la première phase. Le Conseil (par l'intermédiaire de CoinCom) a voté pour mettre en œuvre un plafond maximum de [taux de récompense annuel de 2,5%](https://hedera.com/blog/hedera-governing-council-votes-to-approve-changes-to-staking-algorithm). Le taux de récompense réel varie selon le nombre de BAR mises pour les récompenses, mais le taux ne dépassera pas le plafond. Dans le futur, lorsque les nœuds sont en baisse ou inactifs, le compte misé ne sera pas éligible pour gagner des récompenses.

Ce système de positionnement offre une fonctionnalité unique supplémentaire: **mise indirecte**. Si le compte A est misé sur le nœud N, la mise en jeu augmente le poids de consensus de N, et le compte A est récompensé pour chaque période de 24 heures qu'il met en jeu. Si le compte A est misé sur le compte B et compte B misé sur le nœud N, alors l'enjeu de A et B augmentera le poids consensuel de N, mais les récompenses pour A et B seront reçues par B.

Un compte peut éventuellement refuser de gagner des récompenses lorsqu'il est misé. Le compte sera toujours comptabilisé pour atteindre la valeur minimale de mise en jeu du noeud.

**📣 Si vous êtes intéressé à consulter les portefeuilles et les échanges supportant le staking HBAR, allez sur la** [**Stake HBAR**](stake-hbar.md) **page.**
