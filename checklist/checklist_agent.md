# Checklist Pôle AGENTS – Plateforme Multi-Agents OpenClaw

Ce document répertorie les mesures de gouvernance, les réalisations et l’intégration infrastructure du Pôle Agents, dans le cadre de la plateforme Investec Advisory. Il inclut les avancées en matière de conteneurisation, de déploiement continu et d’isolation sur Kubernetes.

---

## 🛡️ 1. Ce qui a été réalisé (Fondations)

### 🧠 Architecture Multi-Agents Déployée
- [x] **Agent Authority centralisé** : point de contrôle unique interceptant toutes les requêtes avant exécution.
- [x] **11 agents spécialisés déployés** (10 métiers + 1 Authority) avec missions et périmètres clairement définis.
- [x] **Séparation stricte des responsabilités** : aucun agent ne cumule analyse, validation et exécution sans supervision.
- [x] **Orchestration hiérarchique** : Agent Authority valide → agents métiers exécutent → Fact Checking & Compliance supervisent.
- [x] **Agent Authority intégré aux sources de données** : implémentation de la logique de récupération de la classification via API SharePoint et Google Drive (avec fallback), testée avec des appels simulés.

### 🧭 Rôles des Agents Métiers
- [x] **Activity Monitor** : surveillance continue des événements internes, détection d’anomalies.
- [x] **Document Intelligence** : extraction des données clés, résumé de documents complexes.
- [x] **Version Resolver** : identification de la version officielle d’un document.
- [x] **Fact Checking** : application stricte de la règle *Evidence First*, validation des sources.
- [x] **Compliance Agent** : vérification des règles Zero Trust, respect des permissions RBAC et classifications C0–C3.
- [x] **Operational Intelligence** : production d’analyses stratégiques synthétiques.
- [x] **Media Intelligence** : veille externe sectorielle, anticipation des tendances.
- [x] **Social Presence** : assistance à la communication externe, protection de l’image institutionnelle.
- [x] **Origination Agent** : préparation des dossiers en amont des missions.
- [x] **Delivery Agent** : génération des livrables finaux, standardisation documentaire.

### 📂 Standardisation des Agents
- [x] **Chaque agent dispose de fichiers de gouvernance** :  
  `IDENTITY.md` (mission), `AGENTS.md` (règles), `TOOLS.md` (skills), `BOOTSTRAP.md` (démarrage), `SOUL.md` (valeurs), `MEMORY.md` (contexte), `HEARTBEAT.md` (monitoring).
- [x] **Garanties** : cohérence entre agents, traçabilité, auditabilité, maintenabilité.

### 🔐 Configuration RBAC et Gestion des Accès
- [x] **Définition complète des rôles métiers** : hiérarchie stricte (PRESIDENT, ASSOCIATE, MANAGER, ANALYST, STAGIAIRE, etc.) avec matrice de permissions détaillée.
- [x] **Granularité des ressources** : identification des ressources critiques (CRM, Data Rooms, Business Plans, Outils d'automatisation).
- [x] **Niveaux de classification** : mise en œuvre des tags de sensibilité (C0: Public à C3: Très Confidentiel).
- [x] **Validation du fichier RBAC** : syntaxe JSON vérifiée, références croisées entre permissions et ressources, absence d’informations sensibles.
- [x] **Gestion des secrets via Kubernetes** : alignement des références de secrets (OpenAI, Twitter, LinkedIn, News API) avec le type `kubernetes_secrets`.
- [x] **Authentification configurée** : accès aux API Microsoft Graph (Azure AD) et Google Drive (compte de service) pour la classification dynamique.
- [x] **Fallback prévu** : classification par défaut en cas d’indisponibilité des API.

### 🛡️ Mesures de Gouvernance Appliquées
- [x] **Interception obligatoire par Authority** : validation préalable de toute action, vérification des permissions (RBAC + C0–C3), blocage automatique en cas d’ambiguïté.
- [x] **Evidence First** : aucun fait retourné sans preuve source, retour `insufficient_evidence` si nécessaire, traçabilité des affirmations.
- [x] **Cloisonnement des Connaissances** : accès limité aux données nécessaires, isolation logique des contextes métiers.
- [x] **Supervision Croisée** : Fact Checking obligatoire pour analyses critiques, Compliance requis pour contenus réglementaires, pas d’auto‑validation.
- [x] **Politique de supervision explicite** : identification des actions nécessitant validation humaine (ex: publication sur réseaux sociaux).
- [x] **Traçabilité renforcée** : journalisation des décisions d’accès dans `audit.log` (ALLOWED, DENIED, PENDING) et démonstration des logs.

### 🧪 Tests et Validation
- [x] **Création d’utilisateurs de test** : fichier `users.json` avec rôles associés (ex: `alice@example.com` → PRESIDENT, `bob@example.com` → ANALYST).
- [x] **Scénarios de test concrets préparés** :
  - Accès autorisé (lecture fichier C1 par un ANALYST).
  - Accès refusé pour cause de classification trop élevée.
  - Action nécessitant supervision (CHARGE_AFFAIRES propose un post réseau).
  - Vérification de la classification par héritage / pattern de chemin.
- [x] **Simulations d’appels à l’Agent Authority** réalisées via interface ou scripts.
- [x] **Journalisation opérationnelle** : les décisions sont écrites dans un système de logs (ELK / stdout) et accessibles pour audit.

### 🏗️ Infrastructure & Déploiement (intégration avec l’équipe Infrastructure)
- [x] **Conteneurisation des agents** : chaque agent (Authority, métiers) est empaqueté dans une image Docker basée sur `openclaw-sandbox:bookworm-slim`, garantissant isolation et reproductibilité.
- [x] **Orchestration Kubernetes** : déploiement des pods agents sur un cluster Kubernetes hébergé chez Hostinger, avec isolation renforcée par conteneurs séparés.
- [x] **CI/CD automatisé** : pipeline GitHub Actions déclenché à chaque push sur `main` :
  - Connexion SSH au VPS Hostinger,
  - Copie des fichiers (Dockerfile, docker-compose, configurations),
  - Reconstruction et redéploiement via `docker compose up -d --build` (ou équivalent Kubernetes).
- [x] **Gestion des secrets** : stockage des clés API dans Kubernetes Secrets, montés dans les pods des agents.
- [x] **Gateway OpenClaw** : conteneurisée et exposée sur le VPS, servant de point d’entrée unique.
- [x] **Environnements isolés** : chaque agent tourne dans son propre conteneur, limitant les risques de propagation en cas d’incident.
- [x] **Token Gateway** : généré automatiquement au premier déploiement et stocké dans `.env` sur le serveur.

---

## 🚀 2. Prochaines Étapes (Renforcer & Fiabiliser)

### Robustesse Agentique
- [ ] Formalisation d’un protocole d’escalade automatique en cas de conflit entre agents.
- [ ] Ajout d’un scoring de confiance par agent.
- [ ] Mise en place d’un mode “safe execution” par défaut pour les nouveaux agents.

### Gouvernance Humaine (HITL)
- [ ] Intégration systématique du principe *Human‑in‑the‑loop* pour actions sensibles.
- [ ] Double validation obligatoire (4‑eyes) pour actions irréversibles.
- [ ] Tableau de bord de supervision dédié au Compliance Officer (visualisation des alertes en temps réel).

### Knowledge Guarding
- [ ] Restriction dynamique de l’accès aux dossiers clients (isolation par contexte).
- [ ] Filtrage automatique des informations sensibles (PII, comptes) dans les réponses externes.
- [ ] Audit périodique des périmètres d’accès de chaque agent.

### Performance & Fiabilité
- [ ] Monitoring continu des performances agentiques (métriques CPU/RAM, latence).
- [ ] Détection des dérives comportementales (anomalies dans les patterns d’appels).
- [ ] Tests réguliers de résistance aux tentatives de contournement (tests de pénétration “agentiques”).

### Infrastructure & CI/CD – Améliorations continues
- [ ] **Passer à un déploiement Kubernetes complet** : remplacer `docker-compose` par des manifests Kubernetes (déploiements, services, ingress) pour une meilleure orchestration.
- [ ] **Mise en place de quotas de ressources** (requests/limits) par pod agent pour éviter la saturation.
- [ ] **Automatisation de la rotation des secrets** (renouvellement des clés API tous les 90 jours).
- [ ] **Ajout de tests d’intégration post‑déploiement** dans le pipeline CI/CD (vérification que les agents répondent correctement).
- [ ] **Mise en place d’un système de logs centralisé** (ELK ou Loki) pour faciliter le débogage et l’audit.
- [ ] **Configuration d’alertes CloudWatch / Prometheus** sur la santé des pods et les décisions de l’Authority.

---
*Dernière mise à jour : 20 Février 2026*