---
title: 'Comment traduire vos documents efficacement dans R'
date: '2025-05-20'
event: "Rencontres R 2025 à Mons (Belgique)"
event_url: "https://rr2025.sciencesconf.org/program"
event_type: "conference talk"
slides: https://rr2025.netlify.app/#/
---

**Avec Yanina Bellini Saibene, Paola Corrales, Elio Campitelli, Pascal Burkhard**

La communauté des utilisateur·rice·s de R est mondiale et donc multilingue. 
Les personnes qui produisent du contenu sur R, qu'il s'agisse de sites de documentation, de livres ou d'articles de blog, adaptent fréquemment leurs documents à des publics multilingues. 
À rOpenSci, nous avons un projet de publication multilingue actif, pour lequel nous avons traduit notre guide de développement de paquets et d'autres documents de l'anglais vers l'espagnol. 
Une traduction vers le portugais est également en cours. 
La réalisation de ce travail a nécessité le développement non seulement de processus humains, mais aussi de deux paquets R : babelquarto pour construire des sites et des livres Quarto multilingues, et babeldown pour utiliser l'API de traduction automatique DeepL afin d'accélérer le processus de traduction des documents Markdown. 
Les paquets babelquarto et babeldown se sont révélés utiles au-delà de notre projet. 
Dans cette communication, nous présenterons les deux paquets, leurs fonctionnalités et leurs limites.

## Créer une documentation Quarto mutilingue avec babelquarto

Notre guide de développement est publié avec Quarto, car il s'agit d'un outil moderne qui, contrairement à Hugo par exemple, est assez facile à apprendre et également utile pour d'autres applications telles que les rapports et les présentations.
Malheureusement, Quarto ne permet pas de créer des sites et des livres multilingues, c'est-à-dire des sites et des livres où chaque page a un lien vers sa version dans d'autres langues. 
C'est pourquoi nous avons décidé de créer un paquet qui permet de construire de tels livres et sites avec Quarto. 
Basé sur des documents avec différentes extensions en fonction de la langue, et sur des champs de configuration spécifiques, babelquarto construit les différentes versions d'un livre ou d'un site, et ajoute les liens vers d'autres langues. 
Le paquet fonctionne parfaitement pour notre guide de développement de paquets. 
Il est également utilisé par d'autres communautés et projets, ce qui donne lieu à des contributions qui rendent le paquet encore plus collaboratif et adapté à d'autres utilisations.

## Utiliser un outil de traduction avancée avec babeldown

Nous nous appliquons à réduire les efforts humains grâce à l'automatisation. 
Tout d'abord, au lieu de traduire des documents à partir de zéro, nous nous appuyons sur une traduction automatique réalisée par DeepL, ce qui nous permet de concentrer notre travail de traduction manuelle sur des aspects tels que le choix des mots les plus appropriés pour notre communauté ou l'utilisation de formulations non genrées.
Deuxièmement, afin d'automatiser le processus autant que possible, nous ne voulons pas faire de copier-coller à partir d'un outil externe. 
Troisièmement, nos documents sont préparés en Markdown et la traduction automatique doit donc respecter cette syntaxe. 
Sur la base de ces trois critères, nous avons créé le paquet R babeldown, qui envoie le contenu des documents Markdown à l'API de traduction DeepL et écrit le résultat dans des documents Markdown corrects. 
En outre, babeldown permet de mettre à jour les traductions en ne traduisant que les parties qui ont été modifiées, ce qui fait gagner du temps aux traducteurs et de l'argent si on paie pour l'utilisation de l'API.

## Leçons apprises

L'infrastructure créée s'intègre bien aux flux de travail R, Git et GitHub et a été utilisée avec succès par rOpenSci pour traduire du contenu en anglais, portugais, espagnol et français. 
Elle est également utilisée par d'autres projets tels que R4Epi,
et le développement reste réactif aux demandes des utilisateur·rice·s.

## Références

- https://docs.ropensci.org/babelquarto/
- https://docs.ropensci.org/babeldown/
- https://ropensci.org/commcalls/nov2023-multilingual/