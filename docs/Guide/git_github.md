---
title: "Git / Github"
---
# Guide Technique Git & GitHub

---

## **1. Concepts Fondamentaux**

### **Git vs GitHub**
| Élément       | Description                                                                                     |
|---------------|-------------------------------------------------------------------------------------------------|
| **Git**       | Système de contrôle de version (VCS) **local** : suit les modifications de ton code, permet de revenir en arrière, de créer des branches, etc. Fonctionne en ligne de commande. |
| **GitHub**    | Plateforme **en ligne** qui héberge tes dépôts Git. Ajoute des fonctionnalités collaboratives : Pull Requests, Issues, Projects, Actions, etc. |

---

### **Terminologie Clé**
| Terme               | Description                                                                                     | Exemple d’Usage                                                                 |
|---------------------|-------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| **Dépôt (Repo)**    | Dossier contenant ton projet et son historique de versions.                                    | `git clone https://github.com/utilisateur/depot.git`                           |
| **Commit**          | "Instantané" de ton code à un moment donné. Contient un message descriptif.                   | `git commit -m "Ajout de la fonction de login"`                                |
| **Branche (Branch)**| Version parallèle du projet. Permet de travailler sur des fonctionnalités sans affecter la version principale (`main` ou `master`). | `git checkout -b feature/nouvelle-fonction`                                  |
| **Pull Request (PR)** | Proposition de fusionner une branche dans une autre (ex: `feature` → `main`). Permet la revue de code. | Créée via l’interface GitHub après un `git push`.                              |
| **Fork**            | Copie d’un dépôt tiers dans ton compte GitHub. Permet de contribuer à des projets open-source. | Forker un dépôt depuis l’interface GitHub.                                    |
| **Merge**           | Fusionner deux branches (ex: une `feature` dans `main`).                                       | `git merge feature/nouvelle-fonction` (ou via une PR sur GitHub).              |
| **Clone**           | Télécharger un dépôt distant sur ta machine locale.                                            | `git clone git@github.com:utilisateur/depot.git`                              |
| **Remote**          | Lien vers un dépôt distant (ex: `origin` pour ton dépôt GitHub).                              | `git remote add origin git@github.com:utilisateur/depot.git`                  |

---

## **2. Configuration de Git et GitHub**

### **Installation et Configuration de Git**
1. **Installer Git** :
   - Télécharge depuis [git-scm.com](https://git-scm.com/).
   - Vérifie l’installation :
     ```bash
     git --version
     ```

2. **Configurer ton identité** (liée à tes commits) :
   ```bash
   git config --global user.name "Ton Nom"
   git config --global user.email "ton.email@example.com"
   ```

3. **Configurer l’éditeur par défaut** (ex: VS Code) :
   ```bash
   git config --global core.editor "code --wait"
   ```

4. **Vérifier ta configuration** :
   ```bash
   git config --list
   ```

---

### **Créer un Compte GitHub**
1. Inscris-toi sur [github.com](https://github.com/).
2. **Configurer une clé SSH** (recommandé pour une connexion sécurisée) :
   - Génère une clé SSH :
     ```bash
     ssh-keygen -t ed25519 -C "ton.email@example.com"
     ```
   - Ajoute la clé publique à ton compte GitHub :
     - Copie la clé avec `cat ~/.ssh/id_ed25519.pub`.
     - Colle-la dans `Settings > SSH and GPG keys > New SSH Key`.

---

## **3. Commandes Git Essentielles**

### **Initialiser et Cloner un Dépôt**
| Commande                                      | Description                                                                                     |
|-----------------------------------------------|-------------------------------------------------------------------------------------------------|
| `git init`                                    | Initialise un nouveau dépôt Git **local**.                                                     |
| `git clone https://github.com/utilisateur/depot.git` | Clone un dépôt distant sur ta machine.                                         |
| `git clone git@github.com:utilisateur/depot.git`     | Clone avec SSH (plus sécurisé).                                                |

---

### **Travailler avec les Fichiers et Commits**
| Commande                                      | Description                                                                                     |
|-----------------------------------------------|-------------------------------------------------------------------------------------------------|
| `git status`                                  | Affiche l’état des fichiers (modifiés, non suivis, etc.).                                      |
| `git add [fichier]`                           | Ajoute un fichier à la "staging area" (prêt pour le commit).                                   |
| `git add .`                                   | Ajoute **tous** les fichiers modifiés.                                                          |
| `git commit -m "message"`                     | Crée un commit avec les fichiers de la staging area.                                           |
| `git log`                                     | Affiche l’historique des commits.                                                              |
| `git log --oneline`                           | Affiche l’historique en format compact.                                                       |
| `git diff`                                    | Affiche les modifications non commitées.                                                      |
| `git diff --staged`                           | Affiche les modifications dans la staging area.                                               |

---

### **Gérer les Branches**
| Commande                                      | Description                                                                                     |
|-----------------------------------------------|-------------------------------------------------------------------------------------------------|
| `git branch`                                  | Liste les branches locales.                                                                    |
| `git branch [nom]`                            | Crée une nouvelle branche.                                                                     |
| `git checkout [nom]`                          | Bascule vers une branche existante.                                                            |
| `git checkout -b [nom]`                       | Crée **et** bascule vers une nouvelle branche.                                                 |
| `git merge [branche]`                         | Fusionne une branche dans la branche courante.                                                 |
| `git branch -d [nom]`                         | Supprime une branche **locale**.                                                               |
| `git push origin --delete [nom]`              | Supprime une branche **distante**.                                                             |

---

### **Synchroniser avec GitHub**
| Commande                                      | Description                                                                                     |
|-----------------------------------------------|-------------------------------------------------------------------------------------------------|
| `git push origin [branche]`                   | Envoie tes commits locaux vers GitHub.                                                          |
| `git pull`                                    | Récupère les modifications distantes **et** fusionne avec ta branche locale.                  |
| `git fetch`                                   | Récupère les modifications distantes **sans** fusionner.                                       |
| `git remote -v`                               | Affiche les dépôts distants configurés.                                                        |
| `git remote add [nom] [URL]`                  | Ajoute un dépôt distant (ex: `origin`).                                                        |

---

## **4. Collaborer avec GitHub**

### **Créer une Pull Request (PR)**
1. **Pousse ta branche** sur GitHub :
   ```bash
   git push origin feature/nouvelle-fonction
   ```
2. Sur GitHub :
   - Va dans ton dépôt > onglet **"Pull Requests"** > **"New Pull Request"**.
   - Sélectionne ta branche (`feature/nouvelle-fonction`) et la branche cible (`main`).
   - Ajoute une description claire et crée la PR.

3. **Revoir une PR** :
   - Discute des modifications via les commentaires.
   - Utilise les **"Requested Changes"** si des corrections sont nécessaires.
   - Fusionne avec **"Merge Pull Request"** une fois validée.

---

### **Gérer les Conflits**
Si Git signale un conflit lors d’un `git merge` ou `git pull` :
1. Ouvre les fichiers en conflit (marqués par `<<<<<<<`, `=======`, `>>>>>>>`).
2. Modifie le code pour résoudre le conflit.
3. Ajoute les fichiers résolus et fais un commit :
   ```bash
   git add [fichier]
   git commit -m "Résolution du conflit"
   ```

---

### **Utiliser les Issues et Projects**
| Élément               | Description                                                                                     |
|-----------------------|-------------------------------------------------------------------------------------------------|
| **Issue**             | Outil pour suivre les bugs ou les demandes de fonctionnalités.                                |
| **Project**           | Tableau Kanban pour organiser les tâches (ex: To Do, In Progress, Done).                      |
| **Milestone**         | Regroupe des Issues/PRs pour une version ou un objectif spécifique.                           |

**Exemple** :
- Crée une **Issue** pour un bug : `Bug: Le bouton de login ne répond pas`.
- Lie l’Issue à une **PR** pour suivre sa résolution.

---

## **5. Automatiser avec GitHub Actions**
- **Workflows** : Automatise des tâches (tests, déploiement, etc.) via des fichiers YAML dans `.github/workflows/`.
- **Exemple** : Lancer des tests à chaque push sur `main` :
  ```yaml
  name: Tests
  on:
    push:
      branches: [ "main" ]
  jobs:
    test:
      runs-on: ubuntu-latest
      steps:
        - uses: actions/checkout@v4
        - run: npm test
  ```

---

## **6. Bonnes Pratiques**
| Pratique                              | Pourquoi ?                                                                                     |
|---------------------------------------|-------------------------------------------------------------------------------------------------|
| **Commits atomiques**                 | Un commit = une modification logique. Évite les commits géants.                                |
| **Messages de commit clairs**         | Utilise le format : `type(scope): description` (ex: `feat(auth): ajout du login`).            |
| **Branches dédiées**                  | Une branche par fonctionnalité/bug (ex: `feature/login`, `bugfix/header`).                     |
| **Revue de code**                     | Toujours faire relire les PRs avant de merger.                                                |
| **Documentation**                     | Mets à jour le `README.md` et commente ton code.                                               |

---

## **7. Commandes Avancées**
| Commande                                      | Description                                                                                     |
|-----------------------------------------------|-------------------------------------------------------------------------------------------------|
| `git rebase [branche]`                        | Réapplique tes commits sur une autre branche (pour un historique linéaire).                     |
| `git cherry-pick [commit]`                    | Applique un commit spécifique à ta branche courante.                                           |
| `git stash`                                   | Sauvegarde temporairement tes modifications non commitées.                                     |
| `git stash pop`                               | Restaure les modifications sauvegardées.                                                       |
| `git reset --hard [commit]`                   | **Attention** : Réinitialise ton dépôt à un commit spécifique (perd les modifications non commitées). |

---

## **8. Ressources Utiles**
- [Documentation GitHub](https://docs.github.com/fr)
- [Git Cheat Sheet](https://education.github.com/git-cheat-sheet-education.pdf)
- [Tutoriel interactif Git](https://learngitbranching.js.org/)
- [Conventions de commits](https://www.conventionalcommits.org/)

---