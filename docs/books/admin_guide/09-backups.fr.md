---
title: Sauvegarde et Restauration
---

# Sauvegarde et Restauration

Dans ce chapitre, vous apprendrez comment sauvegarder et restaurer vos données avec Linux.

****

**Objectifs** : Dans ce chapitre, les futurs administrateurs Linux vont apprendre comment :

:heavy_check_mark: utiliser les commandes `tar` et `cpio` pour faire une sauvegarde ;  
:heavy_check_mark: vérifier les sauvegardes et restaurer des données ;  
:heavy_check_mark: comprimer ou décomprimer les sauvegardes.

:checkered_flag: **sauvegarde**, **restauration**, **compression**

**Connaissances** : :star: :star: :star:  
**Complexité** : :star: :star:

**Temps de lecture** : 40 minutes

****

!!! note "Remarque"

    Tout au long de ce chapitre les commandes utilisent le terme `device` – unité péripherique – pour spécifier à la fois la cible pour la sauvegarde et la source lors de la restauration. Le périphérique - device - peut indiquer soit un média externe, soit un fichier local. Vous pouvez toujours vous référer à cette note pour plus de précisions si besoin est.

La sauvegarde répond à la nécessité de conserver et de restaurer les données de manière sûre et efficace.

La sauvegarde vous permet de vous protéger des événements suivants :

* **Destruction**: volontaire ou involontaire. Humain ou technique. Logiciels malveillants (virus, etc.)
* **Suppression** : volontaire ou involontaire. Humain ou technique. Logiciels malveillants, ...
* **Atteinte à l'intégrité** : les données deviennent inutilisables.

Aucun système n'est infaillible, aucun humain n'est infaillible, donc pour éviter de perdre des données, il doit être sauvegardé pour pouvoir le restaurer après un problème.

Le support de sauvegarde devrait être conservé dans une autre pièce (ou bâtiment) que le serveur afin qu'une catastrophe ne détruise pas le serveur et les sauvegardes.

En outre, l'administrateur doit vérifier régulièrement que les médias sont toujours lisibles.

## Généralités

Il existe deux principes, la **sauvegarde** et l'**archivage**.

* L'archivage détruit la source d'information après l'opération.
* La sauvegarde conserve la source des informations après l'opération.

Ces opérations consistent à enregistrer des informations dans un fichier, sur un périphérique ou sur un support physique (bandes magnétiques, disques, ...).

### Le processus

Les sauvegardes nécessitent beaucoup de discipline et de rigueur de la part de l'administrateur système. Les administrateurs système doivent prendre en compte les points suivants avant d’effectuer des opérations de sauvegarde :

* Quel est le média approprié ?
* Qu'est-ce qui doit être sauvegardé ?
* Combien de copies ?
* Combien de temps prendra la sauvegarde ?
* Quelle méthode ?
* À quelle fréquence ?
* Processus automatique ou manuel ?
* Où stocker la sauvegarde ?
* Combien de temps sera-t-elle conservée ?
* Quelles sont les considérations de coût à prendre en compte ?

Outre ces considérations, les administrateurs système doivent également prendre en compte des facteurs tels que le coût, les performances, l’importance des données, la consommation de bande passante et la complexité de la maintenance en fonction des situations réelles.

### Méthodes de sauvegarde

* **Sauvegarde complète** : fait référence à une copie unique de tous les fichiers, dossiers ou données du disque dur ou de la base de données.
* **Sauvegarde incrémentielle** : fait référence à la sauvegarde des données mises à jour après la dernière sauvegarde complète ou incrémentielle.
* **Sauvegarde différentielle** : fait référence à la sauvegarde des fichiers modifiés après la sauvegarde complète.
* **Sauvegarde sélective (sauvegarde partielle)** : fait référence à la sauvegarde d'une partie du système.
* **Sauvegarde à froid** : fait référence à la sauvegarde lorsque le système est en état d'arrêt ou de maintenance.  Les données sauvegardées sont exactement les mêmes que celles du système durant cette période.
* **Sauvegarde à chaud** : fait référence à la sauvegarde lorsque le système est en fonctionnement normal.  Comme les données du système sont mises à jour à tout moment, les données sauvegardées ont un certain retard par rapport aux données réelles du système.
* **Sauvegarde à distance** : fait référence à la sauvegarde des données dans un autre emplacement géographique pour éviter la perte de données et l'interruption de service causée par un incendie, une catastrophe naturelle, un vol, etc.

### Fréquence des sauvegardes

* **Périodique** : sauvegarde dans un délai spécifique avant une mise à jour majeure du système (généralement pendant les heures creuses)
* **Cyclique** : quotidien, hebdomadaire, mensuel, ...

!!! tip "Astuce"

    Avant la modification d'un système, il est souvent utile de lancer une sauvegarde. Cependant, il est inutile de sauvegarder quotidiennement des données qui ne changent que tous les mois.

### Méthodes de Restauration

Selon les utilitaires disponibles, il sera possible de réaliser plusieurs types de restaurations.

Dans certains systèmes de gestion de bases de données relationnelles, les opérations correspondantes de `recover` (parfois `recovery` est utilisée dans la documentation) et de `restore` sont différentes, ce qui nécessite de consulter la documentation officielle pour plus d'informations. Ce document de base n'entrera pas trop dans les détails sur cette partie du `SGBDR`.

* **Récupération complète** : Récupération de données basée sur une sauvegarde complète ou « Sauvegarde complète + Sauvegarde incrémentielle » ou « Sauvegarde complète + Sauvegarde différentielle ».
* **Récupération sélective** : Récupération de données basée sur une sauvegarde sélective (sauvegarde partielle).

Il n'est pas recommandé de supprimer directement les répertoires ou les fichiers du système d'exploitation actuellement actif avant d'effectuer une opération de récupération (à moins que vous ne sachiez ce qui se passera après la suppression). Si vous ne savez pas ce qui va se passer, vous pouvez effectuer une opération « snapshot » sur le système d'exploitation actuel.

!!! tip "Astuce"

    Pour des raisons de sécurité, il est recommandé de stocker le répertoire ou le fichier restauré dans le répertoire /tmp avant d'effectuer l'opération de récupération afin d'éviter les situations où les anciens fichiers (ancien répertoire) écrasent les nouveaux fichiers (nouveau répertoire).

### Les outils et technologies associées

Il existe de nombreux utilitaires pour faire des sauvegardes.

* **outils d'édition** ;
* **outils graphiques** ;
* **outils en ligne de commande** : `tar`, `cpio`, `pax`, `dd`, `dump`, ...

Les commandes que nous utiliserons ici sont `tar` et `cpio`. Si vous souhaitez en savoir plus sur l'outil `dump`, veuillez vous référer à [ce document](../../guides/backup/dump_restore.md).

* `tar` :

  1. facile à utiliser ;
  2. permet d'ajouter des fichiers à une sauvegarde existante.

* `cpio` :

  1. conserve les propriétaires - owner - ;
  2. préserve les groupes, dates et privilèges ;
  3. ignore les fichiers endommagés ;
  4. peut être utilisé pour l'ensemble du système de fichiers.

!!! note "Remarque"

    Ces commandes enregistrent les données en utilisant un format propriétaire et standardisé.

**Réplication** : une technologie de sauvegarde qui copie un ensemble de données d'une source de données vers une autre ou plusieurs sources de données, principalement divisée en **Réplication synchrone** et **Réplication asynchrone**. Pour les administrateurs système novices, il s'agit d'une partie avancée de la sauvegarde, donc ce document de base ne développera pas ce thème.

### Convention de nommage

L'utilisation d'une convention de nommage permet de cibler rapidement le contenu d'un fichier de sauvegarde et ainsi d'éviter des restaurations risquées.

* nom du répertoire ;
* utilitaire utilisé ;
* options utilisées ;
* date.

!!! tip "Astuce"

    Le nom de la sauvegarde doit être explicite.

!!! note "Note"

    Dans le monde Linux, hormis quelques exceptions dans les environnements GUI (telles que .jpg, .mp4, .gif), la plupart des fichiers n'ont pas le concept d'extension. En d’autres termes, la plupart des extensions de fichiers ne sont pas nécessaires. La raison de l'ajout artificiel de suffixes est de faciliter la reconnaissance par les utilisateurs humains. Si l'administrateur système voit une extension de fichier `.tar.gz` ou `.tgz`, par exemple, il sait alors comment traiter le fichier.

### Propriétés du fichier de sauvegarde

Un seul fichier de sauvegarde peut inclure les propriétés suivantes :

* nom de fichier (y compris les suffixes ajoutés manuellement) ;
* sauvegarder l'`atime`, le `ctime`, le `mtime`, le `btime` (`crtime`) du fichier lui-même ;
* taille du fichier de sauvegarde lui-même ;
* les propriétés ou caractéristiques des fichiers ou répertoires dans le fichier de sauvegarde seront partiellement préservées. Par exemple, le `mtime` des fichiers ou des répertoires sera conservé, mais le numéro `inode` ne sera pas retenu.

### Modes de Stockage

Il existe deux modes de stockage différents :

* Interne : stocker les fichiers de sauvegarde sur le disque actuel.
* Externe : stocker les fichiers de sauvegarde sur des périphériques externes. Les périphériques externes en question peuvent être des clés USB, des CD, des disques, des serveurs ou des NAS, etc.

## Tape ArchiveR - `tar`

La commande `tar` permet d'enregistrer sur plusieurs supports successifs (options multi-volumes).

Il est possible d'extraire tout ou une partie d'une sauvegarde.

`tar` sauvegarde implicitement en mode relatif même si le chemin des informations à sauvegarder est mentionné en mode absolu. Cependant, les sauvegardes et les restaurations en mode absolu sont également possibles. Si vous souhaitez voir un exemple distinct de l'utilisation de `tar`, veuillez vous référer à [ce document](../../guides/backup/tar.md).

### Règles de restauration

Les bonnes questions à poser sont donc les suivantes :

* quoi ? partiel ou complet ;
* où ? l'endroit où les données seront restaurées ;
* Comment ? absolu ou relatif ?

!!! warning "Avertissement"

    Avant une restauration, il est important de prendre le temps de réfléchir et de déterminer la méthode la plus appropriée pour éviter les erreurs.

Les restaurations sont généralement effectuées après un problème qui doit être résolu rapidement. Dans certains cas, une mauvaise restauration peut aggraver la situation.

### Sauvegarde avec `tar`

L'utilitaire par défaut pour créer des sauvegardes sur les systèmes UNIX est la commande `tar`. Ces sauvegardes peuvent être compressées par `bzip2`, `xz`, `lzip`, `lzma`, `lzop`, `gzip`, `compress` ou `zstd`.

`tar` vous permet d'extraire un seul fichier ou un répertoire d'une sauvegarde, d'afficher son contenu ou de valider son intégrité.

#### Estimer la taille d'une sauvegarde

La commande suivante estime la taille possible en kilo-octets d'un fichier *tar* :

```bash
$ tar cf - /directory/to/backup/ | wc -c
20480
$ tar czf - /directory/to/backup/ | wc -c
508
$ tar cjf - /directory/to/backup/ | wc -c
428
```

!!! warning "Avertissement"

    Attention, la présence de "-" dans la ligne de commande perturbe `zsh`. Passer à `bash` !

#### Convention de nommage pour une sauvegarde avec `tar`

Voici un exemple de convention de nommage pour une sauvegarde `tar`, sachant que la date sera ajoutée au nom.

| option  | Fichiers | Suffixe          | Fonctionnalité                                |
| ------- | -------- | ---------------- | --------------------------------------------- |
| `cvf`   | `home`   | `home.tar`       | `/home` en mode relatif, forme non compressée |
| `cvfP`  | `/etc`   | `etc.A.tar`      | `/etc` en mode absolu, pas de compression     |
| `cvfz`  | `usr`    | `usr.tar.gz`     | `/usr` en mode relatif, compression *gzip*    |
| `cvfj`  | `usr`    | `fr.tar.bz2`     | `/usr` en mode relatif, compression *bzip2*   |
| `cvfPz` | `/home`  | `home.A.tar.gz`  | `/home` en mode absolu, compression *gzip*    |
| `cvfPj` | `/home`  | `home.A.tar.bz2` | `/home` en mode absolu, compression *bzip2*   |
| …       |          |                  |                                               |

#### Créer une sauvegarde

##### Créer une sauvegarde en mode relatif

La création d'une sauvegarde non compressée en mode relatif est faite avec les options `cvf` :

```bash
tar c[vf] [device] [file(s)]
```

Exemple :

```bash
[root]# tar cvf /backups/home.133.tar /home/
```

| Option | Observation                                                |
| ------ | ---------------------------------------------------------- |
| `c`    | Crée une sauvegarde.                                       |
| `v`    | Affiche le nom des fichiers traités.                       |
| `f`    | Vous permet de spécifier le nom de la sauvegarde (médium). |

!!! tip "Astuce"

    Le tiret (`-`) devant les options de `tar` n'est pas nécessaire !

##### Créer une sauvegarde en mode absolu

La création d'une sauvegarde non compressée, explicitement en mode absolu, est faite avec les paramètres `cvfP` :

```bash
tar c[vf]P [device] [file(s)]
```

Exemple :

```bash
[root]# tar cvfP /backups/home.133.P.tar /home/
```

| Clé | Observation                          |
| --- | ------------------------------------ |
| `P` | Créer une sauvegarde en mode absolu. |

!!! warning "Avertissement"

    Avec l'option `P`, le chemin des fichiers à archiver doit être indiqué comme **absolut**. Si aucune des deux conditions (option `P` et chemin **absolut**) n'est indiquée, l'archivage est effectué en mode relatif.

##### Création d'une sauvegarde compressée avec `gzip`

La création d'une sauvegarde compressée avec `gzip` est faite avec les paramètres `cvfz` :

```bash
tar cvzf backup.tar.gz dirname/
```

| Option | Observation                          |
| ------ | ------------------------------------ |
| `z`    | Compresse la sauvegarde vers *gzip*. |

!!! note "Remarque"

    L'extension `.tgz` est equivalente à `.tar.gz`.

!!! note "Remarque"

    Conserver les options `cvf` (`tvf` ou `xvf`) inchangées pour toutes les opérations de sauvegarde et simplement ajouter l'option de compression à la fin des options rend la commande plus facile à comprendre (par exemple : `cvfz` ou `cvfj`, et autres).

##### Création d'une sauvegarde compressée avec `bzip2`

La création d'une sauvegarde compressée avec `bzip2` est faite avec les paramètres `cvfj` :

```bash
tar cvfj backup.tar.bz2 dirname/
```

| Option | Observation                         |
| ------ | ----------------------------------- |
| `j`    | Compresse la sauvegarde en *bzip2*. |

!!! note "Remarque"

    Les extensions `.tbz` and `.tb2` sont equivalentes aux extensions `.tar.bz2`.

##### Comparaison de l'efficacité de la compression

La compression, et par conséquent la décompression, auront un impact sur la consommation des ressources (temps et utilisation du processeur).

Voici un classement de la compression d'un ensemble de fichiers texte du moins au plus efficace :

* compress (`.tar.Z`)
* gzip (`.tar.gz`)
* bzip2 (`.tar.bz2`)
* lzip (`.tar.lz`)
* xz (`.tar.xz`)

#### Ajouter un fichier ou un répertoire à une sauvegarde existante

Il est possible d'ajouter un ou plusieurs éléments à une sauvegarde existante.

```bash
tar {r|A}[key(s)] [device] [file(s)]
```

Pour ajouter `/etc/passwd` à la sauvegarde `/backups/home.133.tar` :

```bash
[root]# tar rvf /backups/home.133.tar /etc/passwd
```

L'ajout d'un répertoire est similaire. Par exemple ajouter `dirtoadd` au fichier `backup_name.tar` :

```bash
tar rvf backup_name.tar dirtoadd
```

| Option | Observation                                                           |
| ------ | --------------------------------------------------------------------- |
| `r`    | Ajouter les fichiers ou les répertoires à la fin de l’archive.        |
| `A`    | Ajouter tous les fichiers d’une archive à la fin d’une autre archive. |

!!! note "Remarque"

    Il n'est pas possible d'ajouter des fichiers ou des répertoires à une sauvegarde comprimée.

    ```
    $ tar rvfz backup.tgz filetoadd
    tar: Cannot update compressed archives
    Try `tar --help' or `tar --usage' for more information.
    ```

!!! note "Remarque "

    Si la sauvegarde a été créée en mode relatif, il faut ajouter des fichiers aussi en mode relatif. Si la sauvegarde a été effectuée en mode absolu, ajoutez des fichiers en mode absolu.
    
    Le mélange de modes (relatif, absolu) peuvent causer des problèmes lors de la restauration.

#### Lister le contenu d'une sauvegarde

Visualiser le contenu d'une sauvegarde sans extraction est possible.

```bash
tar t[key(s)] [device]
```

| Option | Observation                                              |
| ------ | -------------------------------------------------------- |
| `t`    | Affiche le contenu d'une sauvegarde (compressée ou non). |

Exemples :

```bash
tar tvf backup.tar
tar tvfz backup.tar.gz
tar tvfj backup.tar.bz2
```

Lorsque le nombre de fichiers dans la sauvegarde augmente, vous pouvez utiliser des caractères de barre verticale (`|`) et certaines commandes (`less`, `more`, `most` et autres) pour obtenir l'effet de l'affichage par pagination :

```bash
tar tvf backup.tar | less
```

!!! tip "Astuce"

    Pour lister ou récupérer le contenu d'une sauvegarde, il n'est pas nécessaire de mentionner l'algorithme de compression utilisé lors de la création de la sauvegarde. Autrement dit, un `tar tvf` est équivalent à `tar tvfj`, pour lire le contenu. Évidemment le type ou l'algorithme de compression **doit** être sélectionné uniquement lors de la création d'une sauvegarde compressée.

!!! tip "Astuce"

    Vous devez toujours vérifier et afficher le contenu du fichier de sauvegarde avant d’effectuer une opération de restauration.

#### Contrôler l'intégrité d'une sauvegarde

L'intégrité d'une sauvegarde peut être testée avec le paramètre `W` au moment de sa création :

```bash
tar cvfW file_name.tar dir/
```

L'intégrité d'une sauvegarde peut être testée avec le paramètre `d` après sa création :

```bash
tar vfd file_name.tar dir/
```

!!! tip "Astuce"

    En ajoutant un deuxième `v` à l'option précédente, vous obtiendrez la liste des fichiers archivés ainsi que les différences entre les fichiers archivés et ceux présents dans le système de fichiers.

    ```
    $ tar vvfd  /tmp/quodlibet.tar .quodlibet/
    drwxr-x--- rockstar/rockstar     0 2021-05-21 00:11 .quodlibet/
    -rw-r--r-- rockstar/rockstar     0 2021-05-19 00:59 .quodlibet/queue
    […]
    -rw------- rockstar/rockstar  3323 2021-05-21 00:11 .quodlibet/config
    .quodlibet/config: Mod time differs
    .quodlibet/config: Size differs
    […]
    ```

L'option `W` est également utilisée pour comparer le contenu d'une archive avec le système de fichiers :

```bash
$ tar tvfW file_name.tar
Verify 1/file1
1/file1: Mod time differs
1/file1: Size differs
Verify 1/file2
Verify 1/file3
```

La vérification avec l'option `W` ne peut pas être effectuée avec une archive compressée. Vous devez utiliser l'option `d` à la place.

```bash
tar dfz file_name.tgz
tar dfj file_name.tar.bz2
```

#### Extraire (*untar*) une sauvegarde

Extraire (*untar*) une sauvegarde `*.tar` se fait avec les paramètres `xvf` :

Extraire le fichier `etc/export` depuis la sauvegarde `/savings/etc.133.tar` vers le répertoire `etc` du répertoire actif :

```bash
tar xvf /backups/etc.133.tar etc/exports
```

Extraire tous les fichiers de la sauvegarde compressée `/backups/home.133.tar.bz2` vers le répertoire actif :

```bash
[root]# tar xvfj /backups/home.133.tar.bz2
```

Extraire tous les fichiers de la sauvegarde `/backups/etc.133.P.tar` vers leur répertoire d'origine :

```bash
tar xvfP /backups/etc.133.P.tar
```

!!! warning "Avertissement"

    Pour des raisons de sécurité, vous devez être prudent lors de l'extraction de fichiers de sauvegarde enregistrés en mode absolu.
    
    Encore une fois, avant d'effectuer des opérations d'extraction, vous devez toujours vérifier le contenu des fichiers de sauvegarde (en particulier ceux enregistrés en mode absolu).

| Option | Observation                                                                        |
| ------ | ---------------------------------------------------------------------------------- |
| `x`    | Extraire des fichiers à partir de sauvegardes (qu'elles soient compressées ou non) |

L'extraction d'une sauvegarde *tar-gzipped* (`*.tar.gz`) se fait avec les options `xvfz` :

```bash
tar xvfz backup.tar.gz
```

L'extraction d'une sauvegarde *tar-bzipped* (`*.tar.bz2`) se fait avec les options `xvfj` :

```bash
tar xvfj backup.tar.bz2
```

!!! tip "Astuce"

    Pour extraire ou lister le contenu d'une sauvegarde, il n'est pas nécessaire de mentionner l'algorithme de compression qui a été utilisé pour créer la sauvegarde. Autrement dit, la commande `tar xvf` est équivalente à `tar xvfj`, pour extraire le contenu, et `tar tvf` est équivalente à `tar tvfj`, pour lister.

!!! warning "Avertissement"

    Afin de restaurer les fichiers dans leur dossier d'origine (option `P` de `tar xvf`), vous devez avoir généré la sauvegarde avec le chemin absolu. C'est-à-dire l'option <code>P de la commande tar cvf.
    </code>

##### Extraire seulement un fichier d'une sauvegarde *tar*

Pour extraire un fichier spécifique d'une sauvegarde *tar* indiquez le nom de ce fichier à la fin de la commande `tar xvf`.

```bash
tar xvf backup.tar /path/to/file
```

La commande précédente extrait uniquement le fichier `/path/to/file` de la sauvegarde `backup.tar`. Ce fichier sera restauré dans le répertoire `/path/to/` créé pour l'occasion ou déjà présent dans le répertoire actif.

```bash
tar xvfz backup.tar.gz /path/to/file
tar xvfj backup.tar.bz2 /path/to/file
```

##### Extraire un dossier à partir d'une sauvegarde *tar*

Pour extraire d'une sauvegarde un seul répertoire (y compris ses sous-répertoires et fichiers), indiquez le nom du répertoire à la fin de la commande `tar xvf`.

```bash
tar xvf backup.tar /path/to/dir/
```

Pour extraire plusieurs dossiers, spécifiez chacun des noms l'un après l'autre :

```bash
tar xvf backup.tar /path/to/dir1/ /path/to/dir2/
tar xvfz backup.tar.gz /path/to/dir1/ /path/to/dir2/
tar xvfj backup.tar.bz2 /path/to/dir1/ /path/to/dir2/
```

##### Extraire un groupe de fichiers d'une sauvegarde *tar* en utilisant des expressions régulières (*regex*)

Spécifiez une expression régulière (*regex*) pour extraire les fichiers correspondant au modèle de sélection spécifié.

Par exemple, pour extraire tous les fichiers avec l'extension `.conf` veuillez procéder comme suit :

```bash
tar xvf backup.tar --wildcards '*.conf'
```

Options :

* **--wildcards *.conf** correspond aux fichiers avec l'extension `.conf`.

## *CoPy Input Output* - `cpio`

La commande `cpio` permet d'enregistrer sur plusieurs supports successifs sans spécifier aucune option.

Il est possible d'extraire tout ou une partie d'une sauvegarde.

Il n'y a pas d'option, contrairement à la commande `tar`, pour sauvegarder et compresser en même temps. Donc, cela est effectué en deux étapes: la sauvegarde puis la compression.

Pour effectuer une sauvegarde avec `cpio`, vous devez spécifier la liste de fichiers à sauvegarder.

Cette liste peut être fournie avec les commandes `find`, `ls` ou `cat`.

* `find` : parcourir un arbre, récursivement ou non ;
* `ls` : liste un répertoire, récursivement ou non ;
* `cat` : lit un fichier contenant les arbres ou les fichiers à sauvegarder.

!!! note "Remarque"

    `ls` ne peut pas être utilisé avec `-l` (détails) ni avec `-R` (récursive).
    
    Il faut une simple liste de noms.

### Créer une sauvegarde avec la commande `cpio`

Syntaxe de la commande `cpio` :

```bash
[files command |] cpio {-o| --create} [-options] [<file-list] [>device]
```

Exemple :

Avec une redirection de la sortie de `cpio` :

```bash
find /etc | cpio -ov > /backups/etc.cpio
```

Utilisation du nom d'un support de sauvegarde :

```bash
find /etc | cpio -ovF /backups/etc.cpio
```

Le résultat de la commande `find` est envoyé en entrée à la commande `cpio` via un *pipe* (le caractère `|`, ++alt-graph+6++).

Ici, la commande `find /etc` renvoie une liste de fichiers correspondant au contenu du répertoire `/etc` (de manière récursive) à la commande `cpio`, qui effectue la sauvegarde.

N'oubliez pas le signe `>` lors de la sauvegarde ou le `F save_name_cpio`.

| Options | Observation                                |
| ------- | ------------------------------------------ |
| `-o`    | Crée une sauvegarde (*output*).            |
| `-v`    | Affiche le nom des fichiers traités.       |
| `-F`    | Indique la sauvegarde à modifier (médium). |

Sauvegarde vers un média :

```bash
find /etc | cpio -ov > /dev/rmt0
```

Le support peut être de plusieurs types :

* tape drive: `/dev/rmt0`;
* une partition : `/dev/sda5`, `/dev/hda5`, etc.

### Type de sauvegarde

#### Sauvegarde avec chemin relatif

```bash
cd /
find etc | cpio -o > /backups/etc.cpio
```

#### Sauvegarde avec chemin absolu

```bash
find /etc | cpio -o > /backups/etc.A.cpio
```

!!! warning "Avertissement"

    Si le chemin spécifié dans la commande `find` est **absolu**, alors la sauvegarde sera effectuée en mode **absolu**.
    
    Si le chemin indiqué dans la commande `find` est **relatif**, alors la sauvegarde sera faite en mode **relatif**.

### Ajouter à une sauvegarde

```bash
[files command |] cpio {-o| --create} -A [-options] [<fic-list] {F|>device}
```

Exemple :

```bash
find /etc/shadow | cpio -o -AF SystemFiles.A.cpio
```

L'ajout de fichiers n'est possible que sur les supports à accès direct.

| Option | Observation                                                     |
| ------ | --------------------------------------------------------------- |
| `-A`   | Ajoute un ou plusieurs fichiers à une sauvegarde sur le disque. |
| `-F`   | Indique la sauvegarde à modifier.                               |

### Compression d'une sauvegarde

* Enregistrer la sauvegarde**puis** comprimer

```bash
$ find /etc | cpio  –o > etc.A.cpio
$ gzip /backups/etc.A.cpio
$ ls /backups/etc.A.cpio*
/backups/etc.A.cpio.gz
```

* Enregistrer **et** comprimer

```bash
find /etc | cpio –o | gzip > /backups/etc.A.cpio.gz
```

Il n'y a pas d'option, contrairement à la commande `tar`, pour sauvegarder et compresser simultanément. La sauvegarde se fait donc en deux étapes : enregistrer et comprimer.

La syntaxe de la première méthode est plus facile à comprendre et à retenir, car elle se fait en deux étapes.

Pour la première méthode, le fichier de sauvegarde est automatiquement renommé par l'utilitaire `gzip` qui ajoute automatiquement `.gz` à la fin du nom du fichier. De même, l'utilitaire `bzip2` ajoute automatiquement le suffixe `.bz2`.

### Lire le contenu d'une sauvegarde

Syntaxe de la commande `cpio` pour lire le contenu d'une sauvegarde *cpio* :

```bash
cpio -t [-options] [<fic-list]
```

Exemple :

```bash
cpio -tv </backups/etc.152.cpio | less
```

| Options | Observation                       |
| ------- | --------------------------------- |
| `-t`    | Lit une sauvegarde.               |
| `-v`    | Affiche les attributs du fichier. |

Après avoir fait une sauvegarde, vous devez lire son contenu pour vous assurer qu'il n'y a pas eu d'erreurs.

De la même manière, avant d'effectuer une restauration, vous devez lire le contenu de la sauvegarde qui sera utilisée.

### Restaurer une sauvegarde

Syntaxe de la commande `cpio` pour restaurer une sauvegarde :

```bash
cpio {-i| --extract} [-E file] [-options] [<device]
```

Exemple :

```bash
cpio -iv </backups/etc.152.cpio | less
```

| Options                      | Observation                                                                  |
| ---------------------------- | ---------------------------------------------------------------------------- |
| `-i`                         | Restaurer une sauvegarde complète.                                           |
| `-E file`                    | Restaure uniquement les fichiers dont le nom est contenu dans le fichier.    |
| `--make-directories` or `-d` | Reconstruit l'arborescence manquante.                                        |
| `- u`                        | Remplace tous les fichiers même s'ils existent.                              |
| `--no-absolute-filenames`    | Permet de restaurer une sauvegarde faite en mode absolu de manière relative. |

!!! warning "Avertissement"

    Par défaut, au moment de la restauration, les fichiers du disque dont la date de dernière modification est plus récente ou égale à la date de la sauvegarde ne sont pas restaurés (afin d'éviter d'écraser des informations récentes par des informations plus anciennes).
    
    L'option `u` vous permet par contre de restaurer les anciennes versions des fichiers.

Exemples :

* Restauration absolue d'une sauvegarde absolue

```bash
cpio –ivF home.A.cpio
```

* Restauration absolue sur une arborescence existante

L'option `u` vous permet de remplacer les fichiers existants à l'endroit où la restauration a lieu.

```bash
cpio –iuvF home.A.cpio
```

* Restaurer une sauvegarde absolue en mode relatif

L'option longue `no-absolute-filenames` permet une restauration en mode relatif. En effet, la barre oblique `/` au début du chemin sera supprimée.

```bash
cpio --no-absolute-filenames -divuF home.A.cpio
```

!!! tip "Astuce"

    La création de répertoires est peut-être nécessaire, d'où l'utilisation de l'option `d`

* Restaurer une sauvegarde relative

```bash
cpio –iv <etc.cpio
```

* Restauration absolue d'un fichier ou d'un répertoire

La restauration d'un fichier ou d'un répertoire particulier nécessite la création d'un fichier contenant une liste qui doit ensuite être supprimé.

```bash
echo "/etc/passwd" > tmp
cpio –iuE tmp -F etc.A.cpio
rm -f tmp
```

## Utilitaires de compression - décompression

L'utilisation de la compression au moment d'une sauvegarde peut avoir un certain nombre d'inconvénients :

* Augmente le temps de sauvegarde ainsi que le temps de restauration.
* Il rend impossible d'ajouter des fichiers à la sauvegarde.

!!! note "Remarque"

    Il est donc préférable de faire une sauvegarde et de la compresser plutôt que de comprimer lors de la sauvegarde.

### Compression avec `gzip`

La commande `gzip` comprime des données.

Syntaxe de la commande `gzip` :

```bash
gzip [options] [file ...]
```

Exemple :

```bash
$ gzip usr.tar
$ ls
usr.tar.gz
```

Le fichier obtient l'extension `.gz`.

Il conserve les mêmes droits et les mêmes dates d'accès et de modification.

### Compression avec `bunzip2`

La commande `bunzip2` compresse également des données.

Syntaxe de la commande `bzip2` :

```bash
bzip2 [options] [file ...]
```

Exemple :

```bash
$ bzip2 usr.cpio
$ ls
usr.cpio.bz2
```

Le nom du fichier est complété par l'extension `.bz2`.

La compression par `bzip2` est meilleure que la compression par `gzip` mais elle met plus de temps à être exécutée.

### Décompression avec `gunzip`

La commande `gunzip` décompresse des données compressées.

Syntaxe de la commande `gunzip` :

```bash
gunzip [options] [file ...]
```

Exemple :

```bash
$ gunzip usr.tar.gz
$ ls
usr.tar
```

Le nom du fichier est tronqué par `gunzip` et l'extension `.gz` est supprimée.

`gunzip` décompresse également les fichiers ayant les extensions suivantes :

* `.z`;
* `-z` ;
* `_z`.

### Décompression avec `bunzip2`

La commande `bunzip2` décompresse des données compressées.

Syntaxe de la commande `bzip2` :

```bash
bzip2 [options] [fichier ...]
```

Exemple :

```bash
$ bunzip2 usr.cpio.bz2
$ ls
usr.cpio
```

Le nom du fichier est tronqué par `bunzip2` et l'extension `.bz2` est supprimée.

`bunzip2` décompresse également un fichier avec les extensions suivantes :

* `-bz` ;
* `.tbz2` ;
* `tbz`.
