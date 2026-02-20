

## Checklist de préparation pour la présentation de la journée

### 1. Finalisation du fichier RBAC
- [x] Vérifier que tous les secrets ont le champ `kubernetes_secret_key` (ex: pour `twitter_api_key`, `linkedin_api_key`, `news_api_key`).
- [x] Remplacer les noms de secrets invalides (ex: `openclaw/twitter/api-key` → `twitter-api-key`).
- [x] S'assurer que les références aux ressources dans les permissions des rôles existent bien dans la section `resources`.
- [x] Valider la syntaxe JSON (utiliser un validateur en ligne).

### 2. Configuration de l'agent Authority
- [ ] Déployer le fichier `rbac-policy.json` dans un volume accessible par l'agent (ConfigMap Kubernetes). - Equipe CI/CD
- [ ] Créer les secrets Kubernetes correspondants (ex: `openai-secret`, `twitter-api-key`) avec les vraies clés API. - Equipe CI/CD et Infra
- [x] Configurer l'authentification pour Microsoft Graph (Azure AD app) et Google Drive (compte de service) – obtenir les tokens/credentials. - voir avec Entreprise 
- [x] Implémenter dans l'agent Authority la logique de récupération de la classification via les API (SharePoint/Google) en suivant l'ordre défini dans `data_classification.methods_order`.
- [x] Tester la connexion aux API avec des appels simulés.

### 3. Source des utilisateurs
- [x] Créer le fichier `users.json` avec quelques utilisateurs de test et leurs rôles (ex: `alice@example.com` → `PRESIDENT`, `bob@example.com` → `ANALYST`).
- [x] Monter ce fichier dans l'agent ou configurer l'accès à l'annuaire (si LDAP).

### 4. Préparation de la démonstration
- [x] Préparer des scénarios de test concrets :
  - Accès autorisé (ex: un `ANALYST` lit un fichier C1).
  - Accès refusé pour cause de classification trop élevée.
  - Action nécessitant une supervision (ex: `CHARGE_AFFAIRES` propose un post sur les réseaux).
  - Vérification de la remontée de classification par héritage ou pattern de chemin.
- [x] Simuler les appels à l'agent Authority via une interface ou des scripts.
- [x] Afficher les logs de décision pour illustrer la traçabilité.

### 5. Éléments de gouvernance à présenter
- [x] Expliquer la structure du RBAC (rôles, ressources, classification).
- [x] Montrer comment la politique de supervision est appliquée.
- [ ] Décrire le processus de mise à jour du RBAC (revue, validation).
- [x] Présenter les mesures de sécurité Zero Trust : vérification systématique, moindre privilège.

### 6. Infrastructure et déploiement
- [x] S'assurer que Kubernetes est opérationnel et que les pods des agents (Authority, métiers) peuvent communiquer.
- [x] Vérifier que l'agent Authority a les droits nécessaires pour lire les secrets (Role-Based Access Control dans Kubernetes).
- [x] Tester la journalisation : les décisions doivent être écrites dans un système de logs (ELK, stdout, etc.).

### 7. Documentation pour la présentation
- [x] Préparer un slide récapitulatif du RBAC et de son rôle.
- [x] Avoir le README_RBAC.md sous la main pour répondre aux questions.
- [x] Lister les prochaines étapes (phase 2/3) pour montrer la vision à long terme.

### 8. Derniers ajustements
- [x] Relire le fichier RBAC pour s'assurer qu'aucune information sensible (ex: clés en dur) n'y figure.
- [x] Vérifier que le champ `secrets_management.type` correspond à votre environnement (`kubernetes_secrets`).
- [x] Prévoir un fallback si les API SharePoint/Google ne répondent pas (ex: utiliser la classification par défaut de la ressource).