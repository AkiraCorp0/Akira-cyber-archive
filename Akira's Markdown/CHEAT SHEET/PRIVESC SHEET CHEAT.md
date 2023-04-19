
## FIND SUID BIN

**SUID** est une autorisation de fichier spéciale pour les fichiers exécutables qui permet à d'autres utilisateurs d'exécuter le fichier avec les autorisations effectives du propriétaire du fichier. Au lieu du `x` qui représente les autorisations d'exécution, vous verrez une autorisation spéciale `s` (pour indiquer **SUID** ) à l'utilisateur.

> find / -perm -4000 2>/dev/null

## FIND SGID BIN

Contrairement au **_SUID_**, le **_SGID_**,  s’applique aussi bien aux fichiers qu’aux répertoires. En ce qui concerne les fichiers, rien ne change.

> find / -perm -2000 2>/dev/null

![](https://hackingeek.com/wp-content/uploads/2021/09/Image5-1.png)

