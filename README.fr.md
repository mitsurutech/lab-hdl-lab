# Lab de conception numérique pour la classe : Verilog, VHDL et yosys dans une VM Ubuntu

[English](README.md) | [Français](README.fr.md) | [日本語](README.ja.md) | [Español](README.es.md) | [Deutsch](README.de.md)

[![check](https://github.com/mitsurutech/lab-hdl-lab/actions/workflows/check.yml/badge.svg)](https://github.com/mitsurutech/lab-hdl-lab/actions/workflows/check.yml)

Un **lab de conception numérique** prêt à l’emploi pour enseigner Verilog et VHDL, issu de la [plateforme d’apprentissage MitsuruTech](https://mitsurutech.co.uk/fr/learning), où les établissements scolaires donnent à chaque élève ses propres machines et son terminal dans le navigateur. Chaque élève reçoit une VM Ubuntu avec **Icarus Verilog**, **GHDL** et **yosys**, installés depuis l’archive d’Ubuntu. Il simule des circuits avec un banc de test, lit une table de vérité et un **chronogramme** sous forme de texte, termine un additionneur complet en Verilog et en VHDL ainsi qu’un compteur 4 bits avec validation et remise à zéro synchrone, puis compte les portes que yosys en tire. Rien n’est installé sur l’ordinateur de l’élève et aucune carte n’est nécessaire.

## Ce que fait l’élève

Le lab laisse un dossier `~/hdl` de fichiers à travailler et un `README.md` dans le dossier personnel qui explique quoi faire sans donner aucune réponse. Il comporte cinq parties :

1. Un circuit combinatoire en Verilog : lire la table de vérité d’un demi-additionneur fourni, puis écrire `full_adder.v`.
2. Une horloge et un chronogramme : un compteur fourni enregistre ses signaux dans un fichier VCD, lu en lignes de texte avec `vcd2txt` ou dessiné en image avec `vcd2svg` et ouvert sur le port 8000 du lab.
3. Le même additionneur complet en VHDL, avec les trois étapes de GHDL, analyser, élaborer et exécuter : `full_adder.vhd`.
4. Un compteur avec validation et remise à zéro synchrone, à terminer par l’élève : `counter4.v`.
5. Les portes : `hdl gates counter4.v counter4` fait compter par yosys les cellules dont un circuit a besoin, ce qui montre qu’une bascule retient un bit et qu’un verrou (latch) est une erreur.

`hdl reset FICHIER` remet un fichier tel qu’il a été donné. Une leçon des bases de la plateforme se travaille dans ce lab et laisse l’additionneur complet et le compteur à l’élève.

## Comment il est noté

Le lab comporte une **vérification**, un script qui note une VM rendue sans que l’enseignant ait à la lire. Elle s’exécute sur une copie de la machine enregistrée, après un démarrage à neuf. Elle exige `full_adder.v`, `full_adder.vhd` et `counter4.v` dans `~/hdl`, et refuse un fichier Verilog qui appelle une tâche système mettant fin à la simulation ou en sortant (`$finish`, `$stop`, `$fatal`, `$fopen`, `$system` et d’autres, même en commentaire) ainsi qu’un fichier VHDL qui utilise `textio`, `std.env` ou `severity failure`. Elle compile ensuite chaque circuit avec des **bancs de test que l’élève ne voit jamais**. Chacun contient un modèle de référence et le compare au circuit, le compteur avant et après chaque front d’horloge, de sorte qu’une remise à zéro non synchrone est repérée. Un banc de test affiche une ligne de résultat avec un nombre que la vérification tire à neuf à chaque exécution : un circuit ne peut donc pas afficher lui-même la bonne ligne. Enfin, yosys doit lire les circuits **sans aucun verrou**, et le compteur doit avoir des bascules et pas plus de cellules qu’un circuit simple. La première ligne affichée dit ce qui ne va pas.

Ce qu’elle ne fait pas : elle ne lit ni les chronogrammes ni les réponses aux questions du README, elle ne place ni ne route rien pour une carte, et un élève qui a les droits root sur sa propre VM pourrait encore trafiquer les simulateurs. C’est une vérification pour la classe, pas une preuve.

## Ce que contient le fichier

`hdl-lab.lab.yaml` est une **définition de lab VM** complète, exactement ce que la plateforme exécute :

| Clé | Ce que c’est |
| --- | --- |
| `cpus`, `memory`, `disk` | la taille de la VM de chaque élève : 1 CPU, 1024 Mo et 10 Go |
| `open` | les ports qui s’ouvrent dans le navigateur : 8000, qui montre les images de chronogrammes dessinées par le lab ; tout le reste se fait dans le terminal |
| `setup` | un script exécuté une fois, en tant que root, au premier démarrage de la VM de chaque élève : il installe `iverilog`, `ghdl` et `yosys` depuis l’archive d’Ubuntu (quelques minutes), écrit les commandes d’aide `hdl`, `vcd2txt` et `vcd2svg`, les fichiers à travailler, le README et une unité systemd qui sert les images |
| `check` | le script de notation, exécuté en tant que root sur une copie du disque enregistré après un démarrage à neuf ; son code de sortie est le résultat et `0` signifie réussi |

## L’utiliser sur la plateforme

**Pour le donner comme travail à une classe :**

1. Dans votre module, ajoutez une tâche et choisissez la carte **Lab VM Ubuntu**.
2. Appuyez sur l’exemple Conception numérique, ou collez le contenu de `hdl-lab.lab.yaml`. Utiliser un exemple remplace le texte, et Annuler rétablit le vôtre.
3. Publiez et essayez d’abord votre propre copie. Chaque élève a sa propre VM, conservée d’une session à l’autre.
4. Distribuez-le comme n’importe quelle tâche. Une fois rendu, lancez la vérification sur la ligne d’un élève dans la liste de la classe : elle démarre une copie de sa machine enregistrée, qui n’est pas sauvegardée en retour, et indique si elle a réussi et ce qu’elle a affiché. L’élève n’est pas informé du résultat.

La [page de ce lab](https://mitsurutech.co.uk/fr/help/lab-hdl-lab) dans le manuel, son [guide des labs VM Ubuntu](https://mitsurutech.co.uk/fr/help/labs) et sa [page des exemples de labs](https://mitsurutech.co.uk/fr/help/sample-labs) disent le reste.

Pas encore de compte pour votre établissement ? [Votre première année est gratuite](https://mitsurutech.co.uk/fr/pricing) jusqu’à 50 élèves, et [l’inscription](https://mitsurutech.co.uk/fr/signup) ne demande qu’une adresse e-mail.

## Vérifier le fichier

À chaque push, `.github/workflows/check.yml` vérifie que le fichier est un YAML valide avec les clés `cpus`, `memory`, `disk`, `setup` et `check`, et que `setup` et `check` tiennent chacun dans la limite de 16 Ko de la plateforme. Il ne démarre pas de VM : cela se fait sur les machines de la plateforme. L’installation, les commandes de la leçon des bases et la vérification ont été exécutées dans un conteneur Ubuntu 26.04 tenant lieu de VM : un essai dans une VM d’élève réellement démarrée avant un cours vaut donc la peine.

## Ce qui ne fonctionne pas dans un lab

La VM atteint l’archive de paquets d’Ubuntu, PyPI, npm et GitHub, et rien d’autre. Tout ce dont ce lab a besoin vient de l’archive d’Ubuntu. Une VM en terminal n’a pas de bureau, donc pas de visionneuse graphique de chronogrammes : on les lit en texte ou en image sur le port 8000. Ubuntu 26.04 fournit GHDL avec le backend GCC, si bien que `ghdl -e` écrit un exécutable à côté des fichiers.

## À propos

Réalisé par [Mitsuru Technologies](https://mitsurutech.co.uk/fr) pour la [plateforme d’apprentissage MitsuruTech](https://mitsurutech.co.uk/fr/learning) : Python, Java, Go, Node, SQL et Linux dans le navigateur pour les écoles et les établissements, avec des labs de conteneurs, des labs VM, des applications web déployées depuis GitHub et des quiz qui se corrigent tout seuls. Essayez du code sans compte dans le [bac à sable](https://mitsurutech.co.uk/fr/playground).

D’autres labs du même éditeur : [lab-netlab](https://github.com/mitsurutech/lab-netlab), [lab-juice-shop](https://github.com/mitsurutech/lab-juice-shop), [lab-dvwa](https://github.com/mitsurutech/lab-dvwa), [lab-webgoat](https://github.com/mitsurutech/lab-webgoat), [lab-mysql-phpmyadmin](https://github.com/mitsurutech/lab-mysql-phpmyadmin), [lab-git-gitea](https://github.com/mitsurutech/lab-git-gitea), [lab-sample](https://github.com/mitsurutech/lab-sample), [lab-sdn-lab](https://github.com/mitsurutech/lab-sdn-lab), [lab-reverse-lab](https://github.com/mitsurutech/lab-reverse-lab), [lab-formal-lab](https://github.com/mitsurutech/lab-formal-lab), [lab-k3s-lab](https://github.com/mitsurutech/lab-k3s-lab), [lab-tofu-lab](https://github.com/mitsurutech/lab-tofu-lab), [lab-ansible-roles](https://github.com/mitsurutech/lab-ansible-roles) et [lab-ansible-basics](https://github.com/mitsurutech/lab-ansible-basics).

## Licence

Cette œuvre est sous licence [CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/). Vous pouvez la partager et l’adapter pour apprendre et enseigner, à condition de citer Mitsuru Technologies Ltd, de ne pas l’utiliser commercialement et de partager tout ce que vous en tirez sous la même licence. Le texte complet est dans le fichier `LICENSE`.

[Icarus Verilog](https://steveicarus.github.io/iverilog/) (GPL-2.0 ou ultérieure), [GHDL](https://github.com/ghdl/ghdl) (GPL-2.0 ou ultérieure) et [yosys](https://yosyshq.net/yosys/) (ISC) sont installés depuis l’archive d’Ubuntu au démarrage d’une VM ; rien n’en est distribué ici.
