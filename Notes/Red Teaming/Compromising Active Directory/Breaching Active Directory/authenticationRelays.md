# Attaques sur les protocoles d’authentification réseau

Dans les réseaux Windows, de nombreux services communiquent entre eux, nécessitant des méthodes d’authentification intégrées pour vérifier l’identité des connexions entrantes. Après avoir exploré l’authentification NTLM sur une application web (voir Tâche 2), nous allons approfondir le fonctionnement de cette authentification au niveau réseau, en nous concentrant sur NetNTLM utilisé par SMB.

---

## Server Message Block (SMB)

Le protocole SMB permet aux clients (stations de travail) de communiquer avec des serveurs (partages de fichiers, administration distante, etc.). Dans les environnements Active Directory, SMB gère le partage de fichiers, l’administration distante, et même des alertes comme « plus de papier » lors de l’impression.

Les premières versions de SMB présentaient des failles de sécurité, permettant la récupération de mots de passe ou l’exécution de code à distance. Bien que corrigées dans les versions récentes, de nombreuses organisations utilisent encore d’anciennes versions pour des raisons de compatibilité.

Nous allons examiner deux types d’exploits NetNTLM avec SMB :

1. **Crackage hors ligne** : Intercepter les challenges NTLM pour tenter de retrouver le mot de passe associé (processus lent).
2. **Relais d’authentification** : Utiliser un appareil malveillant pour relayer l’authentification SMB entre client et serveur, obtenant ainsi une session authentifiée.

---

## LLMNR, NBT-NS et WPAD

Pour intercepter l’authentification SMB, nous utiliserons **Responder** afin de capturer le challenge NetNTLM. Sur un réseau local, de nombreux challenges circulent, parfois redirigés vers un appareil malveillant à cause de vieux enregistrements DNS.

Responder permet des attaques de type Man-in-the-Middle en empoisonnant les réponses lors de l’authentification NetNTLM, trompant le client pour qu’il communique avec l’attaquant. Il cible les protocoles :

- **LLMNR** (Link-Local Multicast Name Resolution)
- **NBT-NS** (NetBIOS Name Service)
- **WPAD** (Web Proxy Auto-Discovery)

Ces protocoles servent à la résolution locale de noms sur le réseau. Responder écoute ces requêtes et envoie des réponses empoisonnées, forçant le client à se connecter à l’AttackBox, qui héberge alors des services (SMB, HTTP, SQL, etc.) pour capturer les authentifications.

---

## Interception du challenge NetNTLM

Responder tente de gagner la course en empoisonnant les connexions pour intercepter l’authentification. Sur un VPN, seules les requêtes du réseau VPN peuvent être empoisonnées. Pour cette raison, une requête d’authentification simulée est lancée toutes les 30 minutes.

> **Attention :** L’utilisation de Responder peut perturber le réseau et être détectée, car elle provoque l’échec des authentifications légitimes.

**Installation et lancement :**

```bash
sudo responder -I breachad
```

Sur l’AttackBox, certains services de Responder peuvent ne pas démarrer (ports déjà utilisés), mais cela n’impacte pas la tâche.

Après quelques minutes, vous devriez voir une connexion SMBv2 et obtenir une réponse NTLMv2-SSP :

```
[SMBv2] NTLMv2-SSP Client   : <Client IP>
[SMBv2] NTLMv2-SSP Username : ZA\<Service Account Username>
[SMBv2] NTLMv2-SSP Hash     : <Service Account Username>::ZA:<NTLMv2-SSP Hash>
```

Copiez le hash NTLMv2-SSP dans un fichier texte. Utilisez ensuite Hashcat et le fichier de mots de passe fourni pour tenter de le cracker :

```bash
hashcat -m 5600 <hash file> <password file> --force
```

Le type de hash 5600 correspond à NTLMv2-SSP pour Hashcat.

---

## Relais du challenge

Au lieu de simplement capturer le challenge, il est parfois possible de le relayer pour obtenir une session authentifiée. Cette attaque dépend :

- De la configuration de la signature SMB (doit être désactivée ou non imposée).
- Des permissions du compte utilisé (idéalement un compte admin sur la cible).

Les relais « à l’aveugle » sont rares, car il faut connaître les permissions des comptes. Après une compromission initiale, l’énumération permet de cibler les comptes à privilèges pour l’escalade latérale.

---

## Schéma d’une attaque de relais

![Schéma d’un relais d’authentification SMB](#)

---

Pour tester ce type d’attaque, rendez-vous sur le Holo Network. Ce scénario sera approfondi dans d’autres modules Active Directory.
