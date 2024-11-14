# DNSRecon

**Description**: DNSRecon est un outil puissant pour effectuer des reconnaissances DNS, notamment pour l'extraction de sous-domaines, la découverte de serveurs DNS autoritaires, et l'analyse de zones DNS.

## Commandes de base

### Requête sur un domaine

```bash
dnsrecon -d example.com
```
Cela va récupérer tous les enregistrements DNS pour example.com (A, MX, NS, TXT, etc.).

### Brute force pour sous-domaines

```bash
dnsrecon -d example.com -t brt
```
Recherche des sous-domaines par brute force.

### Vérification des transferts de zone DNS

```bash
dnsrecon -d example.com -t axfr
```
Essaie de récupérer une copie de la zone DNS (attention aux mauvaises configurations qui peuvent permettre un transfert de zone non sécurisé).

### Ciblage spécifique des enregistrements

```bash
dnsrecon -d example.com -t soa
```
Récupère uniquement l'enregistrement SOA (Start of Authority).

# Sublist3r

**Description**: Sublist3r est un outil de reconnaissance de sous-domaines qui interroge plusieurs sources publiques pour identifier les sous-domaines d'un domaine.

## Commandes de base

### Recherche des sous-domaines d'un domaine

```bash
sublist3r -d example.com
```
Cela va interroger plusieurs moteurs de recherche et services publics pour trouver des sous-domaines de example.com.

### Recherche en utilisant un fichier de mots pour brute force

```bash
sublist3r -d example.com -w /path/to/wordlist.txt
```
Utilise un fichier de mots pour faire une recherche de sous-domaines via brute force.

### Lister les sous-domaines avec une sortie JSON

```bash
sublist3r -d example.com -o result.json
```
Sauvegarde les sous-domaines trouvés dans un fichier result.json.

### Utiliser des options de recherche supplémentaires (Google, Bing, etc.)

```bash
sublist3r -d example.com -b
```
Active la recherche sur plusieurs moteurs de recherche en parallèle (Google, Bing, etc.).

# Résumé des outils

- **DNSRecon** : Plus orienté vers une reconnaissance approfondie des serveurs DNS et la configuration de la zone DNS. Utilisation de brute force, analyse de zone et recherche d'enregistrements DNS.
- **Sublist3r** : Plus simple et rapide pour la découverte de sous-domaines via des sources publiques comme les moteurs de recherche. Recherche rapide avec des options de brute force via des dictionnaires et des résultats en JSON.

Ces deux outils sont complémentaires : DNSRecon pour des analyses DNS détaillées et Sublist3r pour une découverte rapide des sous-domaines.

## ffuf tool

Certaines applications ou sous-domaines ne sont pas exposés publiquement dans les résultats DNS classiques (par exemple, des environnements de développement ou des portails d'administration). Ces sous-domaines peuvent être stockés dans des fichiers locaux comme `/etc/hosts` (Linux) ou `c:\windows\system32\drivers\etc\hosts` (Windows), ou sur un serveur DNS privé.

Lorsqu'un serveur web héberge plusieurs sites, il peut savoir quel site afficher en fonction du Host header dans la requête HTTP. En manipulant ce header, on peut tester différents sous-domaines pour découvrir de nouveaux sites ou services cachés.

## Commande de base pour tester un sous-domaine

On utilise ffuf, un outil de fuzzing de requêtes HTTP, pour tester les sous-domaines. Voici la commande de base :

```bash
ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeitsupport.thm" -u http://10.10.173.24
```
- `-w` : Spécifie la liste de mots (ici namelist.txt).
- `-H "Host: FUZZ.acmeitsupport.thm"` : Modifie le header HTTP "Host" pour tester les sous-domaines en remplaçant "FUZZ" par chaque entrée du wordlist.
- `-u` : L'URL cible à tester.

## Filtrage des résultats avec -fs

Étant donné que la commande ci-dessus renverra toujours une réponse valide, il est important de filtrer les résultats pour identifier les nouveaux sous-domaines. Cela peut être fait en filtrant par la taille de la page (`-fs`).

### Commande pour filtrer les résultats

```bash
ffuf -w /usr/share/wordlists/SecLists/Discovery/DNS/namelist.txt -H "Host: FUZZ.acmeitsupport.thm" -u http://10.10.173.24 -fs {size}
```
- `-fs {size}` : Ignore les résultats ayant une taille de réponse identique à `{size}`, que vous obtenez en analysant la sortie de la commande précédente.

## But

L'utilisation de cette technique permet de découvrir de nouveaux sous-domaines non répertoriés publiquement, en simulant une requête HTTP avec un header modifié, ce qui peut révéler des sites cachés ou des ressources internes.