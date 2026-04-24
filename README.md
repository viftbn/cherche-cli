# CHERCHE

CHERCHE est une commande Bash de recherche de fichiers et dossiers, conçue pour être plus lisible, plus intuitive et plus pédagogique qu’un usage brut de `find`.

Le projet repose sur une logique simple :

**chercher quoi → où → selon quels critères → pour faire quoi**

CHERCHE n’a pas la prétention ni la vocation à remplacer `find` sur tous les plans.
C’est un outil volontairement structuré, humainement simple et lisible, et conçu comme un projet autour de la conception d’une CLI.

---

## Philosophie

CHERCHE est conçu pour être :

- intuitif
- lisible
- pédagogique
- structuré autour d’une logique humaine
- en français

Le projet s’appuie sur un moteur de parcours écrit en Bash, sans utiliser `find` comme moteur principal.

---

## Syntaxe

```bash
cherche <fichier|dossier> [options] --dans <chemin> [action]

````
---

## Utilisation

Particularité volontaire de CHERCHE : 'cherche' tout seul affiche directement l’aide d’utilisation.

---

## Fonctionnalités disponibles

Type recherché:
<fichier>
<dossier>

Portée:
--dans <chemin> (obligatoire)
--non-recursif

Filtres:
--nom <nom_exact>
--contient <texte>
--extension <ext>
--vide

Actions:
--afficher (par défaut)
--compter
--supprimer

---

## Exemples
```bash

Rechercher un fichier nommé hosts dans /etc : cherche fichier --nom hosts --dans /etc
Rechercher les fichiers contenant log dans leur nom : cherche fichier --contient log --dans /var/log
Rechercher les fichiers .conf : cherche fichier --extension conf --dans /etc
Limiter la recherche au premier niveau : cherche fichier --dans /etc --non-recursif
Rechercher les fichiers vides : cherche fichier --dans /tmp --vide
Compter les résultats : cherche fichier --extension log --dans /var/log --compter
Supprimer les résultats trouvés : cherche dossier --dans /tmp --contient cache --supprimer
```

---

## Comportement de suppression

L’action --supprimer :

fonctionne sur les résultats trouvés
demande une confirmation globale avant suppression
supprime les dossiers de manière récursive avec leur contenu
filtre les doublons logiques parent/enfant avant suppression

---

## État du projet

v1
recherche de fichier ou dossier
--nom
--contient
--extension
--dans
--afficher
--compter

v2
--non-recursif
--vide
--supprimer

---

## Limites connues

En v2, CHERCHE ne parcourt pas encore les éléments cachés lors de la recherche générale.

En revanche, pour l’option --vide, un contenu caché est bien pris en compte pour déterminer si un dossier est vide ou non.

---

## Installation

Pour le moment, CHERCHE s’installe manuellement depuis le dépôt.

Cloner le dépôt :
```bash
git clone <git clone https://github.com/viftbn/cherche-cli.git>
cd cherche-cli
chmod +x cherche
```
Pour l’utiliser comme une vraie commande : 
```bash
ln -sf "$(pwd)/cherche" ~/.local/bin/cherche
```

Vérifier ensuite que ~/.local/bin est bien dans le PATH.

=> Une distribution future sous forme de paquet .deb est envisagée pour simplifier l’installation.

---

## Objectif du projet

CHERCHE est à la fois :

un outil en ligne de commande utilisable
un projet pédagogique Bash
une démonstration de conception CLI évolutive

---

## Roadmap

v3
--modifie
--user
--perm
--exec

v4
logique AND / OR avancée
exclusions
performance
amélioration UX

## Licence

CHERCHE est distribué sous licence GNU General Public License v3 (GPLv3).
Consulter le fichier `LICENSE` du dépôt pour le texte complet.

