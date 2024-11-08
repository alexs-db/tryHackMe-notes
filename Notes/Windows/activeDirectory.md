# Active Directory 

Active Directory (AD) est un service clé dans un domaine Windows, responsable de la gestion des objets sur le réseau.

## Objets principaux

### Utilisateurs
Ils représentent des personnes (comme les employés) ou des services (comme IIS ou MSSQL). Ils peuvent être authentifiés et avoir des droits sur des ressources comme les fichiers ou les imprimantes.

### Machines
Chaque ordinateur qui rejoint le domaine AD a un compte machine (comme "PC01$"). Ces comptes permettent à la machine de se connecter au réseau avec des droits spécifiques.

### Groupes de sécurité
Ils permettent de gérer les droits d’accès. Par exemple, ajouter un utilisateur à un groupe lui donne automatiquement les privilèges du groupe sur certaines ressources.

## Groupes par défaut

- **Domain Admins** : Administrateurs du domaine entier.
- **Server Operators** : Gèrent les contrôleurs de domaine (DC).
- **Backup Operators** : Accès à tous les fichiers pour les sauvegardes.
- **Account Operators** : Gèrent les comptes utilisateurs.
- **Domain Users/Computers** : Comptes utilisateurs et ordinateurs du domaine.

## OUs (Unités Organisationnelles)
Ce sont des conteneurs utilisés pour organiser et appliquer des politiques spécifiques à des groupes de personnes ou d’ordinateurs (par exemple, une OU pour le département IT). Contrairement aux groupes, un utilisateur ne peut appartenir qu’à une seule OU.

## Différence Groupes/OUs

- **OUs** : Pour organiser et appliquer des politiques (par exemple, des configurations spécifiques pour chaque département).
- **Groupes** : Pour gérer les permissions sur les ressources (par exemple, accès à un dossier partagé).

Cela permet de gérer efficacement les droits et les configurations dans une entreprise.