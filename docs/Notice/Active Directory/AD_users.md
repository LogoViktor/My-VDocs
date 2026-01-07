# Création des Utilisateurs, Groupes et OU dans Active Directory

Ce guide vous explique comment créer des **utilisateurs**, des **groupes** et des **Organizational Units (OU)** dans **Active Directory**, ainsi que la procédure pour intégrer un poste au domaine.

---

## 👥 Création des Utilisateurs

### 1. Ajouter un utilisateur

- Dans **Active Directory Users and Computers** :
  - Allez dans **Outils > Utilisateurs et ordinateurs Active Directory**.
  - Cliquez sur le **nom de votre domaine**.
  - Faites un **clic droit sur Users > Nouveau > Utilisateur**.
  - Remplissez les informations nécessaires pour créer l'utilisateur.

- **Activer et configurer le compte** :
  - Faites un **clic droit sur l'utilisateur > Onglet Compte**.
  - Décochez **"Le compte est verrouillé"** pour déverrouiller le compte.
  - Configurez les **propriétés du mot de passe** (par exemple, "Le mot de passe n'expire jamais").

---

## 👥 Types et Étendues des Groupes

### 1. Types de groupes

- **Groupes de sécurité** :
  - Utilisent un **jeton d'accès** pour contrôler les autorisations et les accès aux ressources.

- **Groupes de distribution** :
  - Utilisés principalement pour la **messagerie** et la **diffusion**.

---

### 2. Étendues des groupes

- **Domaine local** :
  - Contiennent des **utilisateurs, ordinateurs ou groupes** pour gérer les autorisations d'accès au sein d'un même domaine.

- **Globale** :
  - Contiennent des **utilisateurs et ordinateurs** du même domaine.
  - Peuvent être positionnés sur n'importe quelle ressource de la forêt.

- **Universelle** :
  - Peuvent être positionnés sur les **ACL de toutes les ressources de la forêt**.

---

## 👥 Création et Configuration des Groupes

### 1. Ajouter un groupe

- Dans **Users**, faites un **clic droit > Nouveau > Groupe**.

---

### 2. Configurer le groupe

- **Nom** : Entrez le nom du groupe.
- **Étendue** : Sélectionnez l'étendue (par défaut : **Globale**).
- **Type** : Sélectionnez le type (par défaut : **Groupes de sécurité**).

---

### 3. Ajouter un utilisateur à un groupe

- Faites un **clic droit sur le groupe sélectionné > Propriétés > Onglet Membre**.
- Cliquez sur **Ajouter** et sélectionnez l'utilisateur.
- Cliquez sur **Appliquer** pour enregistrer.

---

## 🌐 Intégration d'un Poste au Domaine

### 1. Paramétrer le DNS du poste

- Configurez le **DNS du poste** avec l'**adresse IP du serveur ADDS**.

---

### 2. Rejoindre le domaine

- Allez dans **Panneau de configuration > Système et sécurité > Système**.
- Cliquez sur **Modifier les paramètres** (à côté du nom de l'ordinateur).
- Allez dans **Nom de l'ordinateur > Modifier**.
- Sélectionnez **Membre d'un domaine** et entrez le **nom du domaine**.

---

### 3. S'authentifier avec un compte administrateur

- Entrez les **identifiants d'un compte administrateur du domaine**.

---

### 4. Redémarrer le poste

- Redémarrez le poste pour appliquer les modifications.

---

### 5. Vérifier la connexion au domaine

- Ouvrez un **terminal** et testez la connectivité :
  ```bash
  ping <nom_NetBIOS_du_serveur_AD>
  ping <nom_du_domaine>
  ```

---

## 📌 Notes supplémentaires

- **Permissions** : Assurez-vous que les utilisateurs et groupes ont les bonnes permissions pour accéder aux ressources.
- **Sécurité** : Utilisez des mots de passe robustes pour les comptes administrateurs.
- **Tests** : Après chaque étape, testez la connectivité et la configuration pour vérifier que tout fonctionne correctement.