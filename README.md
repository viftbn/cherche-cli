# CHERCHE

CHERCHE est une commande Bash de recherche de fichiers et dossiers en français, conçue pour être plus simple, intuitive, et plus pédagogique qu’un usage brut de `find`.

Le projet repose sur une logique simple :

**chercher quoi → où → comment (selon quels critère)s → pour faire quoi**

CHERCHE n’a pas la prétention ni la vocation à remplacer `find` sur tous les plans.  
C’est un outil volontairement structuré, humainement simple et lisible, conçu comme un projet autour de la conception d’une CLI.

---

## Philosophie

CHERCHE est conçu pour être :

- intuitif ;
- lisible ;
- pédagogique ;
- structuré autour d’une logique humaine ;
- en français.

Le projet s’appuie sur un moteur de parcours écrit en Bash, sans utiliser `find` comme moteur principal.

---

## Syntaxe

```bash
cherche <fichier|dossier> [options] --dans <chemin> [action]
```

Le premier argument indique ce que l’on cherche :

```bash
cherche fichier ...
cherche dossier ...
```

L’option `--dans` est obligatoire afin de borner explicitement la recherche.

---

## Utilisation

Particularité volontaire de CHERCHE : lancer `cherche` tout seul affiche directement l’aide d’utilisation.

```bash
cherche
```

---

## Fonctionnalités disponibles

### Type recherché

```text
fichier
dossier
```

### Portée

```text
--dans <chemin>     obligatoire
--non-recursif      limite la recherche au premier niveau
```

Par défaut, la recherche est récursive.

### Filtres

```text
--nom <nom_exact>
--contient <texte>
--extension <ext>
--vide
--user <utilisateur>
--perm <mode>
--caches
```

Détails :

- `--nom` filtre par nom exact ;
- `--contient` filtre les résultats dont le nom contient un texte donné ;
- `--extension` filtre les fichiers selon leur extension ;
- `--vide` recherche les fichiers ou dossiers vides ;
- `--user` filtre selon le propriétaire ;
- `--perm` filtre selon les permissions exactes en notation octale.
- `--caches` Inclut les fichiers et dossiers cachés (noms commençant par .)

L’option `--extension` est utilisable uniquement avec `fichier`.

L’option `--perm` fonctionne en mode exact uniquement.

Exemples de modes valides :

```text
644
600
755
700
1777
```

Les notations symboliques comme `rw-r--r--`, `u=rw` ou les recherches avancées de type `-perm /222` ne sont pas prises en charge.

### Actions

```text
--afficher
--compter
--supprimer
```

Si aucune action n’est indiquée, l’action par défaut est :

```text
--afficher
```

---

## Exemples

Rechercher un fichier nommé `hosts` dans `/etc` :

```bash
cherche fichier --nom hosts --dans /etc
```

Rechercher les fichiers contenant `log` dans leur nom :

```bash
cherche fichier --contient log --dans /var/log
```

Rechercher les fichiers `.conf` :

```bash
cherche fichier --extension conf --dans /etc
```

Limiter la recherche au premier niveau :

```bash
cherche fichier --dans /etc --non-recursif
```

Rechercher les fichiers vides :

```bash
cherche fichier --dans /tmp --vide
```

Rechercher les dossiers vides :

```bash
cherche dossier --dans /tmp --vide
```

Rechercher les fichiers appartenant à `root` :

```bash
cherche fichier --dans /etc --user root
```

Rechercher les fichiers avec des permissions exactes en `644` :

```bash
cherche fichier --dans /tmp --perm 644
```

Combiner plusieurs filtres :

```bash
cherche fichier --dans /tmp --user virgil --perm 644
```

Compter les résultats :

```bash
cherche fichier --extension log --dans /var/log --compter
```

Supprimer les résultats trouvés :

```bash
cherche dossier --dans /tmp --contient cache --supprimer
```

---

## Chemins spéciaux

CHERCHE accepte les chemins fournis par le shell.

Exemples :

```bash
cherche fichier --dans .
cherche fichier --dans ..
cherche fichier --dans ~
cherche fichier --dans ~/Documents
```

Signification :

- `.` désigne le répertoire courant ;
- `..` désigne le répertoire parent ;
- `~` désigne le répertoire personnel de l’utilisateur courant.

Ces notations ne sont pas propres à CHERCHE.  
Elles font partie de l’usage normal du shell et du système de fichiers.

Par exemple :

```bash
cherche fichier --dans ~
```

est généralement interprété par le shell comme une recherche dans le répertoire personnel de l’utilisateur courant.

---

## Répertoires spéciaux exclus par défaut

Lors d’une recherche large, CHERCHE exclut par défaut certains pseudo-filesystems :

```text
/proc
/sys
/dev
/run
```

Ces répertoires contiennent des fichiers virtuels, dynamiques ou directement liés à l’état du noyau et du système. Les parcourir automatiquement peut produire des résultats incohérents, très lents, ou inutiles dans le cadre d’une recherche classique.

Exemple :

```bash
cherche fichier --dans /
```

Dans ce cas, CHERCHE indique que ces répertoires font partie du périmètre demandé mais sont exclus par défaut.

Pour rechercher spécifiquement dans l’un de ces répertoires, il faut l’indiquer explicitement :

```bash
cherche fichier --dans /proc
cherche fichier --dans /sys
cherche fichier --dans /dev
cherche fichier --dans /run
```

---

## Liens symboliques

Les liens symboliques peuvent apparaître dans les résultats lorsqu’ils correspondent à la recherche.

Exemples :

- un lien symbolique vers un dossier peut apparaître lors d’une recherche de dossiers ;
- un lien symbolique vers un fichier peut apparaître lors d’une recherche de fichiers.

En revanche, CHERCHE ne suit pas les liens symboliques par défaut.

Ce comportement évite les boucles récursives et les parcours absurdes, par exemple :

```text
/bin/X11/X11/X11/...
```

Ce choix est volontaire.

---

## Comportement de suppression

L’action `--supprimer` :

- fonctionne sur les résultats trouvés ;
- demande une confirmation globale avant suppression ;
- supprime les dossiers de manière récursive avec leur contenu ;
- filtre les doublons logiques parent/enfant avant suppression.

Le filtrage parent/enfant évite de tenter de supprimer inutilement un élément déjà contenu dans un dossier parent supprimé.

---

## Limites connues

CHERCHE ne parcourt pas encore les éléments cachés lors d’une recherche générale.

En revanche, pour l’option `--vide`, un contenu caché est bien pris en compte pour déterminer si un dossier est vide ou non.

CHERCHE ne gère pas encore :

- les recherches par date de modification ;
- l’exécution de commandes sur les résultats ;
- les expressions logiques avancées de type AND / OR ;
- les permissions symboliques ;
- le suivi explicite des liens symboliques.

Ces choix sont volontaires afin de conserver un outil simple, lisible et maîtrisable.

---

## Installation

Pour le moment, CHERCHE s’installe manuellement depuis le dépôt.

Cloner le dépôt :

```bash
git clone https://github.com/viftbn/cherche-cli.git
cd cherche-cli
chmod +x cherche
```

Pour l’utiliser comme une vraie commande utilisateur :

```bash
ln -sf "$(pwd)/cherche" ~/.local/bin/cherche
```

Vérifier ensuite que `~/.local/bin` est bien présent dans le `PATH` :

```bash
echo "$PATH"
```

Puis tester :

```bash
cherche
```

Une distribution future sous forme de paquet `.deb` est envisagée pour simplifier l’installation.

---

## Objectif du projet

CHERCHE est à la fois :

- un outil en ligne de commande utilisable en français ;
- un projet pédagogique Bash ;
- une démonstration de conception CLI évolutive.

L’objectif n’est pas de faire plus puissant que `find`.

L’objectif est de construire une commande claire, lisible, cohérente et compréhensible.

---

## Licence

CHERCHE est distribué sous licence GNU General Public License v3.

Consulter le fichier `LICENSE` du dépôt pour le texte complet.
