# EEMI-LINUX-SYSADMIN

**Titre du TP : Création de groupes et d'utilisateurs**

https://www.w3schools.io/terminal/bash-tutorials/ 

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

  **Correction du TP : Création de groupes et d'utilisateurs**

**1. Création du script Shell :**

Créez un nouveau fichier nommé `create_users.sh` et écrivez le script suivant :

```bash
#!/bin/bash

# Vérification des arguments en ligne de commande
if [ $# -lt 2 ]; then
    echo "Usage: $0 <nom_du_groupe> <utilisateur1> [<utilisateur2> ...]"
    exit 1
fi

# Nom du groupe
group_name=$1

# Création du groupe
echo "Création du groupe $group_name..."
if grep -q "^$group_name:" /etc/group; then
    echo "Le groupe $group_name existe déjà, en sautant..."
else
    groupadd "$group_name"
    echo "Groupe $group_name créé avec succès."
fi

# Création des utilisateurs
shift
users=("$@")

for user in "${users[@]}"
do
    # Vérification si l'utilisateur existe déjà
    if id "$user" &>/dev/null; then
        echo "L'utilisateur $user existe déjà, en sautant..."
    else
        # Création de l'utilisateur
        echo "Création de l'utilisateur $user..."
        useradd -m -s /bin/bash -G "$group_name" "$user"
        echo "Utilisateur $user créé avec succès."
        echo "Définir le mot de passe pour $user :"
        passwd "$user"
    fi
done

echo "Tous les utilisateurs ont été créés avec succès."
```

**2. Exécution du script :**

Assurez-vous que le script est exécutable en utilisant la commande :

```bash
chmod +x create_users.sh
```

Ensuite, exécutez le script avec les arguments nécessaires :

```bash
./create_users.sh mon_groupe utilisateur1 utilisateur2 utilisateur3
```

**3. Observation des résultats :**

- Vérifiez les messages de sortie pour confirmer que le groupe et les utilisateurs ont été créés avec succès.
- Utilisez les commandes `cat /etc/passwd` et `getent group mon_groupe` pour vérifier la création des utilisateurs et leur appartenance au groupe.

**4. Exercice supplémentaire (optionnel) :**

Vous pouvez améliorer ce script en ajoutant une vérification pour s'assurer que l'utilisateur exécutant le script a les privilèges nécessaires pour créer des groupes et des utilisateurs.

**Questions de discussion :**

1. Pourquoi est-il important d'organiser les utilisateurs en groupes sur un système Linux ?
   - Réponse : L'organisation des utilisateurs en groupes permet de simplifier la gestion des autorisations et des permissions, en permettant d'attribuer des permissions de manière cohérente à un groupe d'utilisateurs plutôt qu'à chaque utilisateur individuellement.

2. Quelles autres fonctionnalités pourraient être ajoutées à ce script pour l'améliorer ou l'adapter à des cas d'utilisation spécifiques ?
   - Réponse : Le script pourrait être étendu pour inclure la gestion des mots de passe, la configuration de répertoires personnels, la définition de shells par défaut, etc.

**Remarques :**

- Veillez à exécuter ce script avec des privilèges suffisants (par exemple, en tant qu'administrateur).
- Ce TP peut être réalisé sur une machine virtuelle ou un environnement de développement Linux.
