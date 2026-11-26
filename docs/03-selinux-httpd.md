# SELinux et Apache sur RHEL 10.3

## Objectif
Comprendre un blocage SELinux sur un fichier web et le corriger
sans désactiver SELinux.

## Scénario
Une page créée dans le dossier personnel a été déplacée avec mv
dans /var/www/html. Elle a conservé l'étiquette user_home_t,
alors qu'Apache (httpd_t) n'a le droit de lire que httpd_sys_content_t.

## Commandes utilisées
- getenforce : mode SELinux (Enforcing)
- ls -Z : afficher les étiquettes SELinux
- curl -I : voir le code HTTP (403 puis 200)
- ausearch -m AVC -ts today : lire les refus
- restorecon -v : remettre l'étiquette par défaut

## Constats
- Avant la correction : 403 Forbidden, bien que les permissions
  classiques soient correctes (-rw-r--r--)
- Le journal d'audit contient : avc: denied { read },
  scontext=httpd_t, tcontext=user_home_t
- Après restorecon : étiquette httpd_sys_content_t et 200 OK

## Fichiers
- Refus SELinux : docs/selinux-refus.txt
- Étiquettes après correction : docs/selinux-apres.txt
- Test depuis le poste hôte : docs/selinux-test-apres.txt

## Ce que j'ai appris
- Un 403 avec des permissions correctes peut venir de SELinux
- mv conserve l'étiquette d'origine, cp prend celle du dossier de destination
- On corrige l'étiquette avec restorecon, on ne désactive pas SELinux
- Le message d'Apache « file permissions deny server access » peut
  masquer une cause SELinux : l'audit donne la vraie raison
