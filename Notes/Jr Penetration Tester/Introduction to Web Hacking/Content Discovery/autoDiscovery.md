# Automated Discovery

**Automated Discovery** est le processus de découverte automatique de contenus en ligne via des outils spécialisés, permettant d'identifier des fichiers ou répertoires sur des serveurs web à l'aide de requêtes massives. Cette approche aide à trouver des ressources auparavant inconnues en utilisant des **wordlists**.

## Wordlists

Les **wordlists** sont des fichiers texte contenant des listes de mots communément utilisés dans différents contextes. En recherche de contenu, une wordlist peut inclure des noms de fichiers et de répertoires les plus courants. Une ressource populaire de wordlists est le [SecLists de Daniel Miessler](https://github.com/danielmiessler/SecLists).

## Outils d'Automatisation

Parmi les outils de découverte de contenu, trois sont utilisés ici sur l'AttackBox :

1. **ffuf**
    ```bash
    ffuf -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt -u http://10.10.97.138/FUZZ
    ```

2. **dirb**
    ```bash
    dirb http://10.10.97.138/ /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
    ```

3. **gobuster**
    ```bash
    gobuster dir --url http://10.10.97.138/ -w /usr/share/wordlists/SecLists/Discovery/Web-Content/common.txt
    ```

Ces commandes ciblent le site Acme IT Support et utilisent une wordlist pour explorer le contenu potentiellement caché.