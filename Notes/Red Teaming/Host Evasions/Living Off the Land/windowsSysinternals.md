# Windows Sysinternals

## Qu'est-ce que Windows Sysinternals ?

Windows Sysinternals est un ensemble d'outils et d'utilitaires système avancés développés pour aider les professionnels IT à gérer, dépanner et diagnostiquer le système d'exploitation Windows. La suite Sysinternals est divisée en plusieurs catégories, notamment :

- Gestion des disques
- Gestion des processus
- Outils réseau
- Informations système
- Outils de sécurité

Pour utiliser les outils Windows Sysinternals, il faut accepter le contrat de licence Microsoft, soit en passant l'argument `-accepteula` en ligne de commande, soit via l'interface graphique lors de l'exécution de l'outil.

### Outils populaires de Windows Sysinternals

- **AccessChk** : Vérifie les accès spécifiés pour les fichiers, dossiers, clés de registre, objets globaux et services Windows.
- **PsExec** : Exécute des programmes sur un système distant.
- **ADExplorer** : Outil avancé pour visualiser et gérer la base de données Active Directory.
- **ProcDump** : Surveille les processus pour les pics CPU et permet de générer des dumps mémoire pour analyse.
- **ProcMon** : Outil essentiel pour la surveillance des processus.
- **TCPView** : Liste toutes les connexions TCP et UDP.
- **PsTools** : Fournit des informations détaillées sur les processus.
- **Portmon** : Surveille toute l'activité des ports série et parallèle.
- **Whois** : Fournit des informations sur un nom de domaine ou une adresse IP.

Pour plus d'informations, consultez la [page officielle Microsoft Docs](https://docs.microsoft.com/en-us/sysinternals/).

---

## Sysinternals Live

L'un des avantages de Windows Sysinternals est qu'aucune installation n'est requise. Microsoft propose le service **Sysinternals Live**, permettant d'utiliser les outils de plusieurs façons :

- Navigateur web ([lien](https://live.sysinternals.com/))
- Partage Windows
- Invite de commandes

Pour accéder aux outils, vous pouvez les télécharger ou entrer le chemin `\\live.sysinternals.com\tools` dans l'explorateur Windows.

> **Note :** Si la machine virtuelle n'a pas accès à Internet, les outils Sysinternals sont pré-téléchargés dans `C:\Tools\`.

### Exemple d'utilisation

```cmd
C:\Users\thm> C:\Tools\SysinternalsSuite\PsExec64.exe
```

### Ressources complémentaires

- [TryHackMe room : Sysinternals](https://tryhackme.com/room/sysinternals)
- [Microsoft Sysinternals Resources](https://docs.microsoft.com/en-us/sysinternals/)

---

## Utilisation et bénéfices pour le Red Team

Bien que ces outils soient conçus pour les administrateurs système, ils sont également utilisés par les hackers, malwares et pentesters en raison de la confiance inhérente qu'ils possèdent dans Windows. Cette confiance est bénéfique pour les Red Teamers souhaitant éviter la détection par les contrôles de sécurité. Ainsi, ces outils sont souvent employés pour échapper à la détection et contourner les défenses.

> **Attention :** L'utilisation malveillante de ces outils étant connue, les équipes Blue Team ont mis en place des contrôles défensifs pour détecter leur usage abusif.
