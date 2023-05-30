---
title: Marche à suivre pour créer un nouvel article
author: Panchevre
contributors: Jean Luc Pinchèvre
tested on: 8.8
tags: 
  - i18n
  - français
  - rocky
  - linux
  - contribution
  - documentation
---

# Marche à suivre
Le but de cet article est de proposer un modèle pour écrire de nouveaux articles ou bien restructurer des articles déjà existants.

## Marche à suivre pour créer un nouvel Article

1. Choisir votre langue préférée et créer un fichier dans la rubrique New Articles (par exemple french_articles/antoine/01_lab1.md).
2. Une fois l'article prêt à être traduit, créez un PR et proposez votre PR à l'équipe de Rocky Linux.
3. Si votre article est accepté, vous pourrez avec l'aide des traducteurs de Rocky Linux traduire l'article vers en-US dans crowdin.
4. La traduction terminée, créez un issue pour proposer votre article à l'équipe de Rocky Linux, qui prendra la décision de placer la version en-US et votre version
   dans une des rubriques books, guides, labs, gems,...
   
Exemple:

1. antoine/01_lab1.md sera copié vers docs/labs/01_lab1.fr.md
2. antoine/01_lab1.en-US.md sera copié vers docs/labs/01_lab1.en-US.md et docs/labs/01_lab1.md
3. Eventuellement le fichier sera renommé si besoin est.
