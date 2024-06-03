# EEMI-LINUX-SYSADMIN


----
# Cours Complet : Gestion Avancée des Utilisateurs et Permissions en Unix/Linux

## Introduction

Ce cours aborde les concepts avancés de gestion des utilisateurs et des permissions en Unix/Linux. Nous couvrirons la création et la gestion des utilisateurs et groupes, l'utilisation des ACL (Access Control Lists) pour une gestion fine des permissions, et des cas d'utilisation courants.

## 1. Gestion Avancée des Utilisateurs et Groupes

### Création de Groupes avec des GID Spécifiques

Pour créer un groupe avec un GID (Group ID) spécifique, utilisez l'option `-g` :
```bash
sudo addgroup --gid 1001 nom_groupe
```

### Création d'Utilisateurs avec des UID et GID Spécifiques

Pour créer un utilisateur avec des UID (User ID) et GID spécifiques :
```bash
sudo adduser --uid 1001 --gid 1001 nom_utilisateur
```

### Utilisation de `/etc/sudoers` pour Accorder des Droits Superutilisateurs

Pour donner des droits sudo à un utilisateur, éditez le fichier `/etc/sudoers` avec `visudo` :
```bash
sudo visudo
```
Ajoutez la ligne suivante pour accorder des droits sudo à un utilisateur spécifique :
```plaintext
nom_utilisateur ALL=(ALL) NOPASSWD:ALL
```
Ou pour un groupe :
```plaintext
%nom_groupe ALL=(ALL) NOPASSWD:ALL
```

## 2. Gestion Avancée des Permissions avec ACL

### Activer les ACL

Assurez-vous que les ACL sont activées sur le système de fichiers :
```bash
sudo mount -o remount,acl /dev/sdX /mnt
```

### Utilisation de `setfacl` pour Définir des Permissions

- **Ajouter des permissions pour un utilisateur** :
  ```bash
  setfacl -m u:alice:rwx fichier.txt
  ```

- **Ajouter des permissions pour un groupe** :
  ```bash
  setfacl -m g:devs:rwx fichier.txt
  ```

- **Définir des permissions par défaut pour un répertoire** :
  ```bash
  setfacl -d -m g:devs:rwx projet_dossier
  ```

### Utilisation de `getfacl` pour Vérifier les Permissions

- **Vérifier les ACL d'un fichier ou répertoire** :
  ```bash
  getfacl fichier.txt
  getfacl projet_dossier
  ```

### Supprimer des ACL

- **Supprimer une ACL pour un utilisateur** :
  ```bash
  setfacl -x u:alice fichier.txt
  ```

- **Supprimer une ACL pour un groupe** :
  ```bash
  setfacl -x g:devs fichier.txt
  ```

## 3. Cas d'Utilisation Courants

### Partager des Répertoires entre Groupes

1. **Créer un répertoire partagé** :
   ```bash
   mkdir /srv/projets
   ```

2. **Changer le groupe propriétaire** :
   ```bash
   sudo chown :devs /srv/projets
   ```

3. **Définir les permissions du groupe** :
   ```bash
   chmod 770 /srv/projets
   ```

4. **Définir les permissions par défaut avec ACL** :
   ```bash
   setfacl -d -m g:devs:rwx /srv/projets
   ```

### Restreindre l'Accès à un Répertoire Sensible

1. **Créer un répertoire** :
   ```bash
   mkdir /srv/secret
   ```

2. **Changer le propriétaire et le groupe** :
   ```bash
   sudo chown root:root /srv/secret
   ```

3. **Définir des permissions strictes** :
   ```bash
   chmod 700 /srv/secret
   ```

## 4. Gestion des Scripts pour Automatiser les Tâches

### Exemple de Script pour Créer des Utilisateurs et Configurer des Permissions

Voici un exemple de script Bash pour automatiser la création d'utilisateurs et la configuration des permissions :

```bash
#!/bin/bash

# Vérifie si l'utilisateur est root
if [ "$(id -u)" -ne 0 ]; then
  echo "Ce script doit être exécuté en tant que root." >&2
  exit 1
fi

# Crée des utilisateurs
for user in alice bob charlie; do
  adduser --disabled-password --gecos "" $user
  echo "$user:password" | chpasswd
done

# Crée un groupe
groupadd devs

# Ajoute des utilisateurs au groupe
for user in alice bob; do
  usermod -aG devs $user
done

# Crée un répertoire partagé
mkdir /srv/projets
chown :devs /srv/projets
chmod 770 /srv/projets
setfacl -d -m g:devs:rwx /srv/projets

echo "Configuration terminée avec succès."
```

Enregistrez ce script sous `setup.sh`, rendez-le exécutable et exécutez-le :
```bash
chmod +x setup.sh
sudo ./setup.sh
```

----
### Partage de Fichiers entre Utilisateurs de Différents Groupes en Unix/Linux

Pour partager un fichier entre plusieurs utilisateurs appartenant à différents groupes, vous pouvez utiliser plusieurs méthodes. Voici les options les plus courantes et efficaces :

#### 1. Utilisation des Listes de Contrôle d'Accès (ACL)

Les ACL permettent de définir des permissions très précises pour plusieurs utilisateurs et groupes sur un même fichier ou répertoire.

##### Activer les ACL

Assurez-vous que les ACL sont activées sur le système de fichiers :
```bash
sudo mount -o remount,acl /dev/sdX /mnt
```

##### Définir des Permissions avec ACL

1. **Créer le fichier ou répertoire à partager** :
   ```bash
   touch /srv/partage/fichier_partage.txt
   mkdir /srv/partage
   ```

2. **Changer le propriétaire et le groupe du fichier/répertoire** :
   ```bash
   sudo chown root:root /srv/partage/fichier_partage.txt
   sudo chown root:root /srv/partage
   ```

3. **Définir les permissions de base** :
   ```bash
   chmod 770 /srv/partage/fichier_partage.txt
   chmod 770 /srv/partage
   ```

4. **Ajouter des permissions ACL pour des utilisateurs spécifiques** :
   ```bash
   setfacl -m u:alice:rwx /srv/partage/fichier_partage.txt
   setfacl -m u:bob:rwx /srv/partage/fichier_partage.txt
   setfacl -m u:charlie:rw /srv/partage/fichier_partage.txt
   ```

5. **Ajouter des permissions ACL pour des groupes spécifiques** :
   ```bash
   setfacl -m g:devs:rwx /srv/partage/fichier_partage.txt
   setfacl -m g:managers:rwx /srv/partage/fichier_partage.txt
   ```

6. **Définir des permissions par défaut pour le répertoire** (pour les nouveaux fichiers créés dans ce répertoire) :
   ```bash
   setfacl -d -m u:alice:rwx /srv/partage
   setfacl -d -m u:bob:rwx /srv/partage
   setfacl -d -m u:charlie:rw /srv/partage
   setfacl -d -m g:devs:rwx /srv/partage
   setfacl -d -m g:managers:rwx /srv/partage
   ```

7. **Vérifier les ACL** :
   ```bash
   getfacl /srv/partage/fichier_partage.txt
   getfacl /srv/partage
   ```

#### 2. Utilisation d'un Groupe Commun

Créer un groupe commun pour les utilisateurs qui doivent partager le fichier peut simplifier la gestion des permissions.

1. **Créer un groupe commun** :
   ```bash
   sudo addgroup partage
   ```

2. **Ajouter des utilisateurs au groupe** :
   ```bash
   sudo usermod -aG partage alice
   sudo usermod -aG partage bob
   sudo usermod -aG partage charlie
   ```

3. **Créer le fichier ou répertoire à partager** :
   ```bash
   touch /srv/partage/fichier_partage.txt
   mkdir /srv/partage
   ```

4. **Changer le groupe propriétaire** :
   ```bash
   sudo chown :partage /srv/partage/fichier_partage.txt
   sudo chown :partage /srv/partage
   ```

5. **Définir les permissions** :
   ```bash
   chmod 770 /srv/partage/fichier_partage.txt
   chmod 770 /srv/partage
   ```

#### 3. Utilisation des Répertoires Partagés avec des Groupes Secondaires

Les utilisateurs peuvent appartenir à plusieurs groupes secondaires, ce qui permet de gérer des permissions complexes.

1. **Ajouter des utilisateurs à plusieurs groupes** :
   ```bash
   sudo usermod -aG devs alice
   sudo usermod -aG managers bob
   ```

2. **Créer le fichier ou répertoire à partager** :
   ```bash
   touch /srv/partage/fichier_partage.txt
   mkdir /srv/partage
   ```

3. **Changer le propriétaire et le groupe du fichier/répertoire** :
   ```bash
   sudo chown root:root /srv/partage/fichier_partage.txt
   sudo chown root:root /srv/partage
   ```

4. **Définir les permissions de base** :
   ```bash
   chmod 770 /srv/partage/fichier_partage.txt
   chmod 770 /srv/partage
   ```

5. **Ajouter des permissions ACL pour des groupes spécifiques** :
   ```bash
   setfacl -m g:devs:rwx /srv/partage/fichier_partage.txt
   setfacl -m g:managers:rwx /srv/partage/fichier_partage.txt
   ```

6. **Définir des permissions par défaut pour le répertoire** (pour les nouveaux fichiers créés dans ce répertoire) :
   ```bash
   setfacl -d -m g:devs:rwx /srv/partage
   setfacl -d -m g:managers:rwx /srv/partage
   ```


### Exercice Pratique : Gestion Avancée des Utilisateurs et Permissions avec ACL en Unix/Linux

#### Objectif

Dans cet exercice, vous allez créer et configurer un environnement de partage de fichiers où plusieurs utilisateurs appartenant à différents groupes peuvent accéder et modifier un fichier commun. Vous allez utiliser les ACL pour définir des permissions précises.

#### Scénario

1. Vous avez trois utilisateurs : Alice, Bob et Charlie.
2. Vous avez deux groupes : `devs` et `managers`.
3. Alice et Bob font partie du groupe `devs`.
4. Charlie fait partie du groupe `managers`.
5. Vous devez créer un répertoire de projet où tous ces utilisateurs peuvent collaborer, avec des permissions spécifiques définies par ACL.

#### Étapes

1. **Création des utilisateurs et des groupes**
2. **Ajout des utilisateurs aux groupes**
3. **Création d'un répertoire de projet**
4. **Configuration des permissions de base et des ACL**
5. **Vérification des permissions**

#### Étape 1 : Création des Utilisateurs et des Groupes

Créez les utilisateurs Alice, Bob et Charlie, et les groupes `devs` et `managers`.

```bash
# Créer les groupes
sudo addgroup devs
sudo addgroup managers

# Créer les utilisateurs
sudo adduser alice
sudo adduser bob
sudo adduser charlie
```

#### Étape 2 : Ajout des Utilisateurs aux Groupes

Ajoutez Alice et Bob au groupe `devs`, et Charlie au groupe `managers`.

```bash
# Ajouter Alice et Bob au groupe devs
sudo usermod -aG devs alice
sudo usermod -aG devs bob

# Ajouter Charlie au groupe managers
sudo usermod -aG managers charlie
```

#### Étape 3 : Création d'un Répertoire de Projet

Créez un répertoire de projet à partager entre les utilisateurs.

```bash
# Créer le répertoire
sudo mkdir /srv/projet_partage

# Changer le propriétaire et le groupe du répertoire
sudo chown root:root /srv/projet_partage
```

#### Étape 4 : Configuration des Permissions de Base et des ACL

Définissez les permissions de base et configurez les ACL pour le répertoire de projet.

```bash
# Définir les permissions de base
sudo chmod 770 /srv/projet_partage

# Ajouter des permissions ACL pour les utilisateurs
sudo setfacl -m u:alice:rwx /srv/projet_partage
sudo setfacl -m u:bob:rwx /srv/projet_partage
sudo setfacl -m u:charlie:r-x /srv/projet_partage

# Ajouter des permissions ACL pour les groupes
sudo setfacl -m g:devs:rwx /srv/projet_partage
sudo setfacl -m g:managers:r-x /srv/projet_partage

# Définir des permissions par défaut pour le répertoire
sudo setfacl -d -m u:alice:rwx /srv/projet_partage
sudo setfacl -d -m u:bob:rwx /srv/projet_partage
sudo setfacl -d -m u:charlie:r-x /srv/projet_partage
sudo setfacl -d -m g:devs:rwx /srv/projet_partage
sudo setfacl -d -m g:managers:r-x /srv/projet_partage
```

#### Étape 5 : Vérification des Permissions

Vérifiez les permissions et les ACL pour vous assurer que tout est configuré correctement.

```bash
# Vérifier les permissions et les ACL
ls -ld /srv/projet_partage
getfacl /srv/projet_partage
```

### Résumé des Commandes

1. Création des groupes :
   ```bash
   sudo addgroup devs
   sudo addgroup managers
   ```

2. Création des utilisateurs :
   ```bash
   sudo adduser alice
   sudo adduser bob
   sudo adduser charlie
   ```

3. Ajout des utilisateurs aux groupes :
   ```bash
   sudo usermod -aG devs alice
   sudo usermod -aG devs bob
   sudo usermod -aG managers charlie
   ```

4. Création et configuration du répertoire de projet :
   ```bash
   sudo mkdir /srv/projet_partage
   sudo chown root:root /srv/projet_partage
   sudo chmod 770 /srv/projet_partage
   sudo setfacl -m u:alice:rwx /srv/projet_partage
   sudo setfacl -m u:bob:rwx /srv/projet_partage
   sudo setfacl -m u:charlie:r-x /srv/projet_partage
   sudo setfacl -m g:devs:rwx /srv/projet_partage
   sudo setfacl -m g:managers:r-x /srv/projet_partage
   sudo setfacl -d -m u:alice:rwx /srv/projet_partage
   sudo setfacl -d -m u:bob:rwx /srv/projet_partage
   sudo setfacl -d -m u:charlie:r-x /srv/projet_partage
   sudo setfacl -d -m g:devs:rwx /srv/projet_partage
   sudo setfacl -d -m g:managers:r-x /srv/projet_partage
   ```

5. Vérification des permissions :
   ```bash
   ls -ld /srv/projet_partage
   getfacl /srv/projet_partage
   ```


----


### Grep :

**Qu'est-ce que Grep ?**
- Grep est une commande utilisée dans les systèmes d'exploitation de type UNIX et Linux pour rechercher des chaînes de texte dans des fichiers ou des flux de données.

**Syntaxe de base :**
```
grep [options] pattern [file...]
```

**Options courantes de Grep :**
- **-i** : Ignore la casse lors de la recherche.
- **-v** : Inverse la recherche, affichant les lignes qui ne correspondent pas au motif spécifié.
- **-r** : Recherche récursive dans les répertoires.
- **-n** : Affiche les numéros de ligne correspondant au motif.
- **-c** : Affiche le nombre total de lignes correspondant au motif.
- **-l** : Affiche uniquement les noms des fichiers contenant le motif.
- **-e pattern** : Permet de spécifier plusieurs motifs de recherche.

**Exemples d'utilisation :**
1. Rechercher une chaîne de texte dans un fichier :
   ```
   grep "pattern" file.txt
   ```

2. Rechercher une chaîne de texte de manière insensible à la casse :
   ```
   grep -i "pattern" file.txt
   ```

3. Rechercher une chaîne de texte dans tous les fichiers d'un répertoire de manière récursive :
   ```
   grep -r "pattern" directory/
   ```

4. Afficher le nombre total de lignes correspondant au motif dans un fichier :
   ```
   grep -c "pattern" file.txt
   ```

### Sed :

**Qu'est-ce que Sed ?**
- Sed (Stream Editor) est un éditeur de flux de texte en ligne de commande. Il est utilisé pour filtrer et transformer du texte à l'intérieur d'un flux ou d'un fichier.

**Syntaxe de base :**
```
sed [options] 'command' file
```

**Options courantes de Sed :**
- **-e** : Permet de spécifier plusieurs commandes à exécuter.
- **-i** : Modifie le fichier d'entrée directement.
- **-n** : Supprime la sortie automatique de chaque ligne.
- **-r** : Utilise des expressions rationnelles étendues.
- **-f** : Lit les commandes depuis un fichier.

**Commandes Sed courantes :**
- **s/pattern/replacement/g** : Remplace toutes les occurrences de 'pattern' par 'replacement'.
- **/pattern/d** : Supprime les lignes contenant 'pattern'.
- **/pattern/p** : Affiche les lignes contenant 'pattern'.
- **1,10d** : Supprime les lignes de 1 à 10.
- **1,10p** : Affiche les lignes de 1 à 10.

**Exemples d'utilisation :**
1. Remplacer toutes les occurrences d'un mot dans un fichier :
   ```
   sed 's/old_word/new_word/g' file.txt
   ```

2. Supprimer les lignes contenant un motif spécifique :
   ```
   sed '/pattern/d' file.txt
   ```

3. Afficher uniquement les lignes contenant un motif spécifique :
   ```
   sed -n '/pattern/p' file.txt
   ```

4. Exécuter plusieurs commandes avec Sed :
   ```
   sed -e 's/old_word/new_word/g' -e '/pattern/d' file.txt
   ```



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


