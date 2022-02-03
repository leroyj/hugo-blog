---
title: "📊 Essayez de faire ça avec Excel :)"
date: 2022-02-02T22:59:43+01:00
draft: false
tags: ["FR","howto"]
description: "Gérer un fichier csv de 5Go avec ripgrep et xsv - La ligne de commande est toujours pertinent pour la data"
---
_Comment gérer un fichier csv de 5Go pour extraire quelques informations simples ?
La CLI va vous sauver le coup._

## Oui, la ligne de commande est toujours en vie

Non seulement la ligne de commande (CLI) représente toujours quelque chose aujourd'hui pour la partie Ops mais on peut même observer une certaine renaissance.

### Automatisation

Les activités de DevOps et de déploiement continue encouragent un déploiement très fréquent (plusieurs fois par jour) par les développeurs . Cela n'est possible qu'avec une automatisation complète de toute la chaîne de déploiement. Cette automatisation doublée d'un haut niveau de personnalisation est rendu possible par des scripts et un outillage performant.

Et qui dit script, dit CLI...

#### Rust 🦀

![rust](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d5/Rust_programming_language_black_logo.svg/144px-Rust_programming_language_black_logo.svg.png)

Rust devient de plus en plus populaire pour les fonctions système en ce moment.
Ce succès s'explique principalement en raison de la modernité de ce langage de programmation système offrant un haut niveau de sécurité **_et_** de performance.
Avec ce succès grandissant, on voit émerger de plus en plus de réécriture d'outil classique en ligne de commande.

Et cela va bien plus loin que qu'une simple réécriture.

### Des outils sous stéroïdes

[`ripgrep`](https://github.com/BurntSushi/ripgrep) (aka `rg`) est un des exemples de réécriture les plus célèbres. Il s'agit d'une réimplémentation plus rapide que l'éclair du non moins célèbre `grep`, un outil de recherche de motif de phrases dans des fichiers.
Ses performances incroyables en font un outil de choix pour être intégré dans des projets majeurs tels que Microsoft Visual Studio Code.

Essayez ces autres outils:

* `exa` en remplacement de `ls`
* `fd` en remplacement de `find`  
* `dust` en remplacement de `du`

## C'est bien tout ça, mais pourquoi m'y interesser?

### Mon cas d'usage

Pour certaines raisons, J'ai eu à trouver l'établissement d'un certaine entreprise dans une certaine ville. Bien entendu, il existe quelques services en ligne qui procurent ce type de fonctionnalité.
Toutefois, la compagnie en question (Orange) a plus de 4000 établissements et les services en ligne n'offrent pas la possibilité de filtrer par ville, ni exclure les établissements fermés.

### Open Data

Grâce à la communauté militant pour l'ouverture des données (mouvement Open Data), La plupart des administrations ouvrent désormais leur données publiques.
Il y a même un site officiel recensant les entreprises françaises appelé [Annuaire Entreprise](https://annuaire-entreprises.data.gouv.fr/).
Malheureusement, encore une fois, cet annuaire en ligne ne permet pas de filtrer les établissements de cette liste et pas moyen de l'extraire.
Alors, allons à la source des données sur la plateforme [data.gouv.fr](https://www.data.gouv.fr/en/).
Vous pouvez trouver facilement la base des entreprise française appelée [Sirene](https://www.data.gouv.fr/fr/datasets/base-sirene-des-entreprises-et-de-leurs-etablissements-siren-siret/).

Seulement voilà, un fois récupéré et décompressé, le fichier fait tout de même 5,4 Giga octets !

### Now, what to do of a 5,3GB csv file?

Ripgrep aka `rg` est là pour vous aider à cibler votre donnée.
Une fois fait, c'est un jeu d'enfant d'extraire proprement les données utile en utilisant xsv.

Voici comment :

```bash
# Get the French company file from the open data platform
curl -LO https://www.data.gouv.fr/fr/datasets/r/0651fb76-bcf3-4f6a-a38d-bc04fa708576
# unzip the 1.38GB file to a 5.3GB csv file
# Search for Orange (unique id 380129866) and extract the Orange's offices in the city of Guyancourt (Postal Code 78280) that are still active
time (\
xsv search -s siren '380129866' StockEtablissement_utf8.csv \
| xsv search -s codePostalEtablissement '78280' \
| xsv search -s etatAdministratifEtablissement 'A' \
| xsv select siren,siret,enseigne1Etablissement,typeVoieEtablissement,libelleVoieEtablissement,libelleCommuneEtablissement \
| xsv table )
```

[![asciicast](https://asciinema.org/a/cyNEJbjD7qWv2cW93ma3dp4FH.svg)](https://asciinema.org/a/cyNEJbjD7qWv2cW93ma3dp4FH)

La ligne de commande reste toujours pertinente pour gérer les données.
Essayez de faire ça avec votre outil favori en interface graphique :P
