# Création de GPO et Tests

Ce guide vous explique comment créer et configurer des **Stratégies de Groupe (GPO)** dans **Active Directory**, puis comment les tester.

---

## 📝 Création d'un Objet de Stratégie de Groupe (GPO)

### 1. Créer un nouvel objet de stratégie de groupe

- Ouvrez **Gestion des stratégies de groupe** :
  - Allez dans **Outils > Gestion des stratégies de groupe**.
  - Développez votre **forêt/domaine**.
  - Faites un **clic droit sur Objets de stratégie de groupe > Nouveau**.
  - Saisissez un **nom** pour votre GPO (par exemple : `PC Libre Service`).

---

### 2. Configurer l'objet de stratégie de groupe

- Faites un **clic droit sur le GPO nouvellement créé > Modifier**.
- Accédez à **Configuration ordinateur > Stratégies > Modèles d'administration** pour configurer les paramètres machine.
- Accédez à **Configuration utilisateur > Stratégies > Modèles d'administration** pour configurer les paramètres utilisateur.

#### Exemple de configuration :
- **Interdire l'accès au panneau de configuration** :
  - Allez dans **Configuration utilisateur > Modèles d'administration > Panneau de configuration**.
  - Sélectionnez **Interdire l'accès au panneau de configuration**.
  - Cochez **Activé** et cliquez sur **Appliquer**.

---

### 3. Lier l'objet de stratégie de groupe

- Faites un **clic droit sur le domaine** (ou l'OU concernée).
- Sélectionnez **Lier un objet de stratégie de groupe existant**.
- Choisissez le GPO que vous venez de créer (`PC Libre Service`).

---

## 🔄 Propagation des Mises à Jour

Pour forcer la mise à jour des stratégies de groupe sur les postes clients, exécutez la commande suivante dans une **invite de commandes** (en tant qu'administrateur) :

```bash
gpupdate /force
```

---

## 🧪 Tests des Stratégies de Groupe

### 1. Vérification de l'application des GPO

- Connectez-vous à un poste client avec un utilisateur concerné par la GPO.
- Vérifiez que les paramètres configurés (comme l'interdiction d'accès au panneau de configuration) sont bien appliqués.

---

### 2. Résolution des problèmes

Si une stratégie ne s'applique pas correctement :

- Vérifiez que le poste client est bien **intégré au domaine**.
- Assurez-vous que le **GPO est bien lié** à l'OU ou au domaine concerné.
- Exécutez `gpupdate /force` sur le poste client.
- Consultez les **journaux d'événements** pour identifier d'éventuelles erreurs.

---

## 📌 Notes supplémentaires

- **Permissions** : Assurez-vous que les utilisateurs et ordinateurs ont les bonnes permissions pour appliquer les GPO.
- **Sécurité** : Testez toujours les GPO dans un environnement contrôlé avant de les déployer en production.
- **Documentation** : Documentez chaque GPO pour faciliter la gestion et le dépannage.