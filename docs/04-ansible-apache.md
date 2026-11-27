# Automatisation d'Apache avec Ansible

## Objectif
Automatiser l'installation d'Apache, l'ouverture du port 80 et le
déploiement d'une page web sur RHEL 10.3, depuis mon poste Debian.

## Architecture
- Machine de contrôle : poste Debian (Ansible 2.19)
- Machine gérée : VM RHEL 10.3, accessible par SSH avec une clé
- Inventaire : ansible/inventory.ini
- Playbook : ansible/playbooks/apache.yml

## Ce que fait le playbook
- Installe httpd (module dnf)
- Démarre et active le service (module service)
- Ouvre le service http dans firewalld (module ansible.posix.firewalld)
- Déploie une page d'accueil (module copy)

## Commandes utilisées
- ansible -i ansible/inventory.ini rhel -m ping
- ansible-playbook ... --syntax-check
- ansible-playbook ... --ask-become-pass

## Résultats
- Première exécution : ok=5 changed=1 (seule la page était différente)
- Seconde exécution : ok=5 changed=0 (docs/ansible-run-2.txt)
- Test depuis le poste hôte : la page affiche « déployé avec Ansible »

## Ce que j'ai appris
- L'idempotence : relancer un playbook ne modifie pas un système
  déjà conforme
- Un playbook décrit l'état voulu, pas les commandes à taper
- become: true donne les droits administrateur, avec --ask-become-pass
  pour saisir le mot de passe sudo de la machine gérée
- Le mot de passe de become est celui de la machine gérée, pas celui
  du poste de contrôle
