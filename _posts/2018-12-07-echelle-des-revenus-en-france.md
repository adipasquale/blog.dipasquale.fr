---
layout: post
title: Échelle des revenus en France
date: 2018-12-07
categories: fr
tags: opendata
---

Je me suis intéressé à tracer le graphique de l'échelle des revenus en France.
L'idée est de pouvoir dire "si vous vivez avec tant d'argent, vous êtes mieux loti que X% des français".

C'est une statistique qui m'intéresse particulièrement car elle est simple à comprendre et permet de prendre du recul.
En l'occurence, cela peut permettre de mieux comprendre la contestation des "gilets jaunes".

# Résultats

Avant toute chose, voici le résultat de mes analyses :

![Revenus disponibles mensuels par unité de consommation](/images/fr-revenues-months.png)

De manière simplificatrice, on peut lire ce graphique comme cela : **"si vous vivez avec 2500 euros par mois, vous êtes mieux loti que 80% des français"**.
*Note : c'est un langage approximatif*.

Pour êtrer précis, il faut bien préciser les termes employés ici. Voici les définitions de l'INSEE :

> Le [revenu disponible](https://www.insee.fr/fr/metadonnees/definition/c1458) d'un ménage comprend les revenus d'activité (nets des cotisations sociales), les revenus du patrimoine, les transferts en provenance d'autres ménages et les prestations sociales (y compris les pensions de retraite et les indemnités de chômage), nets des impôts directs.

> [L'unité de consommation](https://www.insee.fr/fr/metadonnees/definition/c1802) est un Système de pondération attribuant un coefficient à chaque membre du ménage et permettant de comparer les niveaux de vie de ménages de tailles ou de compositions différentes. Avec cette pondération, le nombre de personnes est ramené à un nombre d'unités de consommation (UC).
> Pour comparer le niveau de vie des ménages, on ne peut s'en tenir à la consommation par personne. En effet, les besoins d'un ménage ne s'accroissent pas en stricte proportion de sa taille. Lorsque plusieurs personnes vivent ensemble, il n'est pas nécessaire de multiplier tous les biens de consommation (en particulier, les biens de consommation durables) par le nombre de personnes pour garder le même niveau de vie.
> Aussi, pour comparer les niveaux de vie de ménages de taille ou de composition différente, on utilise une mesure du revenu corrigé par unité de consommation à l'aide d'une échelle d'équivalence. L'échelle actuellement la plus utilisée (dite de l'OCDE) retient la pondération suivante :
>  - 1 UC pour le premier adulte du ménage ;
>  - 0,5 UC pour les autres personnes de 14 ans ou plus ;
>  - 0,3 UC pour les enfants de moins de 14 ans.

Donc, *en gros*, on parle ici de **l'argent effectivement disponible dans chaque famille divisé par le nombre de personnes de manière dégressive** (toutes sources confondues et après impôts).

Les données utilisées proviennent de [ce dossier](https://www.insee.fr/fr/statistiques/3560118) de l'INSEE paru le 19/06/2018 analysant des données de 2015 (plus de détails ci-dessous).

*Vous trouverez des graphiques à d'autres échelles géographiques à la [fin de l'article](#regions).*

# Construction de l'analyse

## Recherche des données

Je me suis d'abord rendu sur [Data Gouv](https://www.data.gouv.fr/) et j'ai cherché avec des mots-clés comme "revenu médian".
J'ai vite constaté que les résultats sont bien moins évidents que ce que j'espérais, avec parfois un langage abscons.
De plus, les liens pointant vers des données de l'INSEE sont en partie cassés et redirigent vers la page d'accueil.

Je me suis donc déplacé sur le site de l'INSEE pour mes recherches.
C'est très compliqué de comprendre où chercher les informations sur ce grand site au vocabulaire nouveau pour moi.
Il y a pléthore de documents, de fiches, de données, regroupées sous des noms d'enquêtes très larges.
Très dur de savoir vers quoi chercher.
J'ai alors utilisé mon joker à un ami et [@AntoineAugusti](https://twitter.com/AntoineAugusti) m'a conseillé de ne pas hésiter à écrire à l'INSEE pour qu'ils m'aiguillent.

J'ai donc envoyé un message depuis le formulaire de contact et j'ai effectivement reçu une réponse très efficace sous quelques jours :

> Bonjour,
>
> Vous trouverez la ventilation des revenus disponibles par commune,sous la rubrique Statistique, en sélectionnant les critères suivants :
thèmes : Revenus – Pouvoir d'achat – Consommation > Revenus – Niveaux de vie – Pouvoir d'achat
catégories : Données > Bases de données
>
> Les données sont accessibles sur la page [Structure et distribution des revenus, inégalité des niveaux de vie en 2015](https://www.insee.fr/fr/statistiques/3560118), puis dans le fichier "Base niveau communes en 2015 - y compris arrondissements municipaux". Vous devez ensuite choisir le fichier "FILO_DISP_COM.xls". La distribution par décile se situe dans l'onglet "ensemble".

Cette réponse a été quasiment parfaite pour mon usage et m'a débloqué pour la suite !

## Retour sur la recherche de données

Mon sentiment sur cette recherche de données est partagé.

D'un côté je suis ébahi par la quantité et la qualité des données accessibles librement.
Et aussi par la réponse parfaite dans un temps raisonnable de l'INSEE à une demande d'un particulier.

D'un autre côté, je trouve ça dommage d'avoir à faire une demande de support pour une recherche de statistique qui me paraît relativement "basique".
L'expérience aurait été bien meilleure si j'avais réussi à trouver moi-même ce que je cherchais.
C'est dommage que l'intégration de Data Gouv avec l'INSEE ne fonctionne pas, et je pense que beaucoup de choses pourraient être faites pour améliorer la recherche et l'exploration au sein du site de l'INSEE. Ça m'a d'ailleurs donné l'idée d'ouvrir un site mirroir ... plus dans un autre post !

Surtout, je pense que je n'aurais jamais envoyé un message de support à l'INSEE si mon ami qui connaît le milieu ne me l'avait suggéré.
Je ne me serais pas senti légitime, et/ou je n'aurais jamais pensé recevoir une réponse aussi efficace rapidement.

## Construction du graphique

Ça a été l'occasion pour moi de re-découvrir pandas, que je n'avais pas utilisé depuis longtemps.
J'ai aussi pu utiliser Jupyter, le successeur de iPython Notebook, que je ne connaissais pas.

La bonne surprise a été de voir que l'installation de cet environnement complet de stats (numpy, pandas, Jupyter ...) sur mon OS X s'est fait avec 3 commandes `pip3 install`, sans aucun problème. J'ai des souvenirs de cauchemards de dépendances ininstallables il y a quelques années.

Dans un premier temps, j'ai travaillé sur le fichier indiqué par le support de l'INSEE qui contient les données à l'échelle communale. J'ai voulu faire une moyenne des déciles, avant de me rendre compte que c'était probablement une erreur statistique. J'ai alors pensé pondérer les moyennes avec la population de chaque commune. Intuitivement, impossible de me décider sur la validité d'une telle opération : **est-ce qu'on peut découper un ensemble en sous-ensembles, calculer les déciles sur ces sous-ensembles, et en faire la moyenne pondérée pour retomber sur les déciles de l'ensemble global ?**. Je n'ai pas trouvé la réponse sur internet, donc j'ai fait des simulations dans un autre notebook pour me faire une idée. Et ça n'a pas l'air valide, on ne retombe pas sur les mêmes valeurs ! vous pouvez jouer avec ce notebook de tests [sur Binder](https://mybinder.org/v2/gh/adipasquale/france-income-disparities-2015/master?filepath=check%20weighted%20percentiles.ipynb) si ça vous intéresse.

J'ai alors sorti le nez du guidon, pour me rendre compte qu'il y avait un jeu de données voisin à échelle plus haute, notamment à l'échelle nationale `FILO_DISP_METROPOLE.xls`. C'est beaucoup plus facile comme ça !

Le reste est un "jeu d'enfants" qui m'a quand même pris quelques heures pour me rappeler comment manipuler les DataFrame et le plotting.

Vous pouvez trouver le code source du Notebook Jupyter sur [GitHub](https://github.com/adipasquale/france-income-disparities-2015), et aussi en lancer une version interactive avec Binder [en suivant ce lien](https://mybinder.org/v2/gh/adipasquale/france-income-disparities-2015/master?filepath=revenus%20disponibles%20france%20metropolitaine%20-%20deciles.ipynb).

# <a name="regions"></a>Par région et par commune

Dans les données disponibles, on a le détail à différentes échelles, dont l'échelle régionale et l'échelle communale.

## Par région

Voici ce que ça donne région par région :

![Revenus disponibles mensuels par unité de consommation par région](/images/fr-revenues-regions-all.png)


C'est un peu illisible, j'ai isolé de manière un peu arbitraire les extrêmes  :

![Revenus disponibles mensuels par unité de consommation par région, seulement les extrêmes](/images/fr-revenues-regions-filtered.png)

On voit des différences de répartition importantes et surtout des inégalités de niveaux de vie très marquées.

## Au sein de Paris

Ensuite, j'ai regardé à l'échelle des communes. On ne peut évidemment pas envisager de tracer le graphique pour les 36000 communes françaises et quelques, il faut donc faire des choix.

J'ai d'abord regardé les différences entre les différents arrondissements de Paris. Avec 20 arrondissements, le graphique est illisible, le voici avec les 4 arrondissements ayant respectivement les 2 taux d'inégalités les plus forts et les plus faibles (mesurés par [l'indice de Gini](https://www.insee.fr/fr/metadonnees/definition/c1551)).

![Revenus disponibles mensuels par unité de consommation par région](/images/fr-revenues-paris.png)

Et non, ce n'est pas le 16ème qui a le plus haut taux d'inégalité, mais bien le 7ème ! (le 16ème est 3ème). Les différences sont extrèmement marquées avec les deux arrondissements à l'extrême opposé. On note que le 1er décile est quasiment identique : les ménages ayant le moins de revenu en ont à peu près autant dans le 7ème que dans le 20ème. Et on voit aussi de manière flagrante que **les ménages ayant le plus de revenus en ont environ 3 fois plus à Paris 7ème qu'à Paris 20ème**.

## Par ville

Enfin j'ai regardé les communes à l'échelle nationale. J'ai filtré les données pour voir les grandes villes uniquement, en utilisant cette règle : "nombre de personnes dans les ménages fiscaux supérieur à 100000".

Voici un graphique représentant 5 villes choisies arbitrairement pour représenter la "palette" de répartition des revenus : Paris (taux d'inégalité le plus fort), Marseille, Brest (taux d'inégalité le plus faible), Toulouse et Lyon.

![Revenus disponibles mensuels par unité de consommation par communes](/images/fr-revenues-cities.png)

Là encore, on voit un schéma similaire, c'est-à-dire que les écarts se produisent principalement dans les déciles les plus hauts. Je pense que l'on peut lire cela comme ça : **"Les écarts de répartitions des richesses dans les grandes villes françaises se concentrent dans les classes les plus aisées".**

# Conclusion

J'espère que comme moi vous avez appris des choses, et surtout que ça vous a donné envie d'en savoir plus. Je vous invite à visiter le site de [l'INSEE](https://www.insee.fr/) et celui de [Data Gouv](https://www.data.gouv.fr/) pour chercher des données intéressantes. J'ai plein d'idées pour d'autres analyses, notamment avec l'indice de Gini, si ça vous intéresse suivez-moi sur Twitter : [@hypertextadrien](https://twitter.com/hypertextadrien).

Et surtout n'hésitez pas à me contacter si vous avez des questions, ou si vous avez repéré des erreurs dans les manipulations de données ou dans mes interprétations !