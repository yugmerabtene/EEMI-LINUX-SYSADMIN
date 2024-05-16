# EEMI-LINUX-SYSADMIN

**Titre du TP : Création de groupes et d'utilisateurs**

**Objectif :**  
L'objectif de ce TP est de créer un script Shell permettant de créer un groupe et des utilisateurs sur un système Linux.

**Tâches à réaliser :**

1. Création du script Shell :
   - Créez un nouveau fichier nommé `create_users.sh`.
   - Écrivez un script Shell dans ce fichier en utilisant l'exemple fourni précédemment comme base.

2. Exécution du script :
   - Assurez-vous que le script est exécutable en utilisant la commande `chmod +x create_users.sh`.
   - Exécutez le script en utilisant `./create_users.sh`.

3. Observation des résultats :
   - Observez les messages de sortie pour vérifier si le groupe et les utilisateurs ont été créés avec succès.
   - Vérifiez la création des utilisateurs en utilisant la commande `cat /etc/passwd`.
   - Vérifiez l'appartenance au groupe en utilisant la commande `getent group mon_groupe`.

4. Exercice supplémentaire (optionnel) :
   - Modifier le script pour permettre à l'utilisateur de spécifier le nom du groupe et la liste des utilisateurs à créer en tant qu'arguments en ligne de commande.

**Questions de discussion :**

1. Pourquoi est-il important d'organiser les utilisateurs en groupes sur un système Linux ?
2. Quelles autres fonctionnalités pourraient être ajoutées à ce script pour l'améliorer ou l'adapter à des cas d'utilisation spécifiques ?

**Remarques :**

- Assurez-vous d'exécuter ce script avec des privilèges suffisants (par exemple, en tant qu'administrateur).
- Ce TP peut être réalisé sur une machine virtuelle ou un environnement de développement Linux.
