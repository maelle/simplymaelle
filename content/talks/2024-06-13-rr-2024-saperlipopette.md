---
title: '{saperlipopette}, un paquet R pour progresser en Git en toute sérénité'
date: '2024-06-13'
event: "Rencontres R"
event_url: "https://rr2024.sciencesconf.org/536842"
event_type: "conference talk"
slides: https://rr2024-maelle.netlify.app/#/
---

Une pratique de Git confiante peut changer la vie de tout développeur·se de paquets R : un historique utile, une capacité à travailler en parallèle sur différents aspects dans différentes branches, etc.
Cependant, il faut en arriver là à la sueur de son front, car Git, ce n'est pas simple !
Notamment, un obstacle peut être de ne pas savoir où et comment s'entraîner à utiliser les commandes moins basiques telles que `git commit --amend` pour changer son dernier commit, `git rebase -i` pour retravailler l'historique d'une branche, `git bisect` pour trouver quel commit a introduit ce fichu bug...
Le paquet R saperlipopette est là pour vous aider !
Il offre pour le moment 12 exercices, inspirés du célèbre site "Dangit, Git!?!", ou de la pratique Git de l'autrice.
Chaque exercice est commencé en appelant une fonction telle que `saperlipopette::exo_committed_to_wrong()` qui concerne le scénario "zut, j'ai fait mon commit sur la mauvaise branche". 
La fonction crée l'exercice dans un dossier qu'on lui indique, temporaire par exemple.
L'utilisateur·rice ouvre R dans ce dossier, et lit les instructions qui apparaissent.
Si cela ne suffit pas, il·elle peut appeler la fonction `tip()` qui lui donne des indices en plus.
Ainsi, on s'entraîne à Git sur un scénario utile et réaliste, avec ses outils habituels, depuis notre chère console R, dans un dossier où il n'y a rien à casser.
Dans cette présentation, je vous expliquerai pourquoi j'ai créé ce paquet, comment il s'utilise, et pourquoi les fichiers `.Rprofile` sont les rois de son implémentation.
