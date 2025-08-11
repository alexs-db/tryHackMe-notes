# Using LOKI

En tant qu'analyste sécurité, il est essentiel de rechercher des rapports de threat intelligence, des articles de blog, etc., afin de recueillir des informations sur les dernières tactiques et techniques utilisées dans la nature, passées ou présentes. Généralement, ces lectures partagent des IOC (hashes, adresses IP, noms de domaine, etc.) pour créer des règles permettant de détecter ces menaces dans votre environnement, ainsi que des règles Yara. À l'inverse, vous pouvez être confronté à une situation où vous avez rencontré quelque chose d'inconnu, que votre stack de sécurité n'a pas détecté. Avec des outils comme Loki, il est possible d'ajouter vos propres règles basées sur vos recherches ou sur les résultats d'une investigation (forensics).

Loki dispose déjà d'un ensemble de règles Yara que l'on peut utiliser pour scanner immédiatement les endpoints.

## Accéder à Loki

Loki se trouve dans le dossier `tools` :

```bash
cmnatic@thm-yara:~/tools$ ls
Loki  yarGen
```

Naviguez dans le dossier Loki et affichez les options disponibles :

```bash
cd Loki
python loki.py -h
```

Si vous exécutez Loki sur votre propre système, commencez par la commande `--update` pour ajouter le dossier `signature-base`, utilisé pour scanner les menaces connues. Cette commande a déjà été exécutée dans la VM fournie.

## Explorer le dossier signature-base

```bash
cmnatic@thm-yara:~/tools/Loki/signature-base$ ls
iocs  misc  yara
```

Naviguez dans le dossier `yara` pour examiner les différents fichiers Yara utilisés par Loki et comprendre ce que ces règles détectent.

## Lancer un scan avec Loki

Pour scanner un fichier suspect, utilisez la commande suivante (depuis le dossier du fichier) :

```bash
cmnatic@thm-yara:~/suspicious-files/file1$ python ../../tools/Loki/loki.py -p .
```

---

### Scénario

Vous êtes analyste sécurité dans un cabinet d'avocats de taille moyenne. Un collègue a découvert des fichiers suspects sur un serveur web de l'organisation lors de la mise à jour du site corporate. Ces fichiers ont été copiés sur votre machine pour analyse, dans le dossier `suspicious-files`. Utilisez Loki pour répondre aux questions ci-dessous.