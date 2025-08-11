# Yara Tools

Créer des règles Yara personnalisées est utile, mais il existe de nombreuses ressources sur GitHub et des outils open-source (ainsi que des produits commerciaux) pour utiliser Yara dans la chasse aux menaces et la réponse aux incidents.

---

## LOKI

**LOKI** est un scanner IOC (Indicator of Compromise) open-source, créé par Florian Roth.

**Méthodes de détection :**
- Vérification du nom de fichier (File Name IOC Check)
- Vérification par règle Yara
- Vérification de hash
- Vérification de connexion C2 Back Connect

LOKI fonctionne sous Windows et Linux. [Lien de téléchargement](https://github.com/Neo23x0/Loki).

> **Note :** Vous n'êtes pas censé utiliser cet outil dans cette room.

**Afficher l'aide de LOKI :**
```bash
python3 loki.py -h
```
Extrait :
```
usage: loki.py [-h] [-p path] ...
optional arguments:
    -h, --help            show this help message and exit
```

---

## THOR Lite

**THOR Lite** est un scanner IOC et YARA multiplateforme, également développé par Florian Roth. Il existe des versions précompilées pour Windows, Linux et macOS. THOR Lite propose un contrôle de la charge CPU.

Pour télécharger le binaire, [voir ici](https://www.nextron-systems.com/thor-lite/). Inscription à la mailing list requise. THOR est destiné aux entreprises, THOR Lite est la version gratuite.

> **Note :** Vous n'êtes pas censé utiliser cet outil dans cette room.

**Afficher l'aide de THOR Lite :**
```bash
./thor-lite-linux-64 -h
```
Extrait :
```
Thor Lite
APT Scanner
Version 10.7.3
> Scan Options
    -t, --template string      ...
    -p, --path strings         ...
            --allhds               ...
            --max_file_size uint   ...
> Scan Modes
            --quick     ...
```

---

## FENRIR

**FENRIR** est le troisième outil de Florian Roth. Contrairement à ses prédécesseurs, Fenrir est un script bash qui fonctionne sur tout système supportant bash (y compris Windows).

> **Note :** Vous n'êtes pas censé utiliser cet outil dans cette room.

**Exécution de Fenrir :**
```bash
./fenrir.sh
```
Extrait :
```
##############################################################
        ____             _
     / __/__ ___  ____(_)___
    / _// -_) _ \/ __/ / __/
 /_/  \__/_//_/_/ /_/_/
 v0.9.0-log4shell

 Simple Bash IOC Checker
 Florian Roth, Dec 2021
##############################################################
```

---

## YAYA (Yet Another Yara Automaton)

**YAYA** est un outil open-source créé par l'EFF (Electronic Frontier Foundation) en septembre 2020. Il permet de gérer plusieurs dépôts de règles YARA, d'ajouter ses propres règles, de désactiver certains ensembles et de lancer des scans.

> **Note :** YAYA fonctionne uniquement sous Linux.

**Exécution de YAYA :**
```bash
yaya
```
Extrait :
```
YAYA - Yet Another Yara Automaton
Usage:
yaya [-h]  
        -h print this help screen
Commands:
     update - update rulesets
     edit - ban or remove rulesets
     add - add a custom ruleset
     scan - perform a yara scan on the directory
```

---

Dans la section suivante, nous examinerons LOKI plus en détail...