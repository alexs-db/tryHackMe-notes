# Intégration avec d'autres bibliothèques

Des frameworks tels que **Cuckoo Sandbox** ou le module **PE de Python** permettent d'améliorer considérablement la technicité de vos règles Yara.

---

## Cuckoo Sandbox

Cuckoo Sandbox est un environnement automatisé d'analyse de malwares. Ce module permet de générer des règles Yara basées sur les comportements observés lors de l'exécution de malwares dans Cuckoo Sandbox. Vous pouvez ainsi créer des règles ciblant des comportements spécifiques, comme des chaînes de caractères en mémoire ou d'autres indicateurs runtime.

---

## Module PE de Python

Le module PE de Python permet de créer des règles Yara à partir des différentes sections et éléments de la structure Windows Portable Executable (PE).

L'explication détaillée de cette structure dépasse le cadre de ce document, car elle est abordée dans mon room d'introduction au malware. Sachez simplement que cette structure est le format standard de tous les exécutables et fichiers DLL sous Windows, y compris les bibliothèques de programmation utilisées.

L'examen du contenu d'un fichier PE est une technique essentielle en analyse de malwares, car certains comportements (cryptographie, propagation de vers, etc.) peuvent être identifiés sans ingénierie inverse ni exécution de l'échantillon.

