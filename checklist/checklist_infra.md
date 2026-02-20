# Checklist Pôle INFRASTRUCTURE & CI/CD – Plateforme Multi-Agents OpenClaw

Ce document détaille les réalisations et les prochaines étapes de l’équipe Infrastructure, en charge de l’hébergement, de la conteneurisation, de l’orchestration et du déploiement continu de la plateforme OpenClaw. L’infrastructure cible est désormais hébergée chez **Hostinger**, avec une architecture basée sur **Docker** et **Kubernetes** pour garantir l’isolation et la scalabilité des agents.

---

## 🏗️ 1. Ce qui a été réalisé (Fondations)

### 🌐 Infrastructure cible – Hostinger
- [x] **Choix du fournisseur** : migration vers Hostinger (VPS) pour un meilleur rapport qualité/prix et une latence adaptée.
- [x] **Provisionnement du VPS** : instance Ubuntu 22.04 LTS, ressources adaptées à la charge (CPU, RAM, stockage SSD).
- [x] **Configuration réseau** : groupe de sécurité dédié, ouverture des ports strictement nécessaires (SSH, HTTP/HTTPS pour la Gateway, éventuellement ports Kubernetes).
- [x] **Accès sécurisé** : génération d’une paire de clés SSH dédiée pour l’administration et pour le pipeline CI/CD.

### 🔧 Conteneurisation des composants
- [x] **Gateway OpenClaw** : empaquetée dans une image Docker, exposée sur le VPS.
- [x] **Agents M&A** : 11 agents (Authority + 10 métiers) chacun isolé dans son propre conteneur Docker basé sur l’image `openclaw-sandbox:bookworm-slim`.
- [x] **Workspace et configuration** : inclus dans les images ou montés via volumes.
- [x] **Orchestration initiale avec Docker Compose** : fichier `docker-compose.yml` définissant l’ensemble des services (agents, Gateway) pour un démarrage simplifié.

### ☸️ Orchestration Kubernetes
- [x] **Installation de Kubernetes** : cluster mono‑nœud (ou multi‑nœuds si besoin) déployé sur le VPS Hostinger (kubeadm, k3s, ou microk8s selon choix).
- [x] **Migration des workloads** : définition des manifests Kubernetes (Deployments, Services, ConfigMaps, Secrets) pour chaque agent.
- [x] **Isolation renforcée** : chaque agent tourne dans un pod distinct, avec ses propres limites de ressources.
- [x] **Stockage des secrets** : utilisation de Kubernetes Secrets pour les clés API (OpenAI, Twitter, LinkedIn, News API), montés dans les pods.

### 🔄 Automatisation & CI/CD
- [x] **Pipeline GitHub Actions** : déclenché à chaque push sur la branche `main`.
- [x] **Étapes automatisées** :
    - Connexion SSH au VPS Hostinger (via clé dédiée).
    - Vérification de l’état de Docker et Kubernetes.
    - Copie des fichiers de configuration (manifests, Dockerfiles, .env).
    - Application des manifests Kubernetes (`kubectl apply -f ...`) ou reconstruction des images.
- [x] **Génération automatique du token Gateway** : créé au premier déploiement et stocké dans un Secret Kubernetes.
- [x] **Tests de base** : vérification que les pods sont bien en état `Running` après déploiement.

### 🔒 Sécurité & Hardening
- [x] **Hardening du VPS** : désactivation des services inutiles, configuration de `iptables`/firewalld, politique de mots de passe renforcée.
- [x] **Accès SSH restreint** : authentification par clé uniquement, pas de root direct.
- [x] **Moindre privilège** : rôles IAM (ou équivalent) limités aux seules actions nécessaires.
- [x] **Scan des images Docker** : intégration d’un outil de scan (Trivy) dans le pipeline pour détecter les vulnérabilités avant déploiement.
- [x] **Isolation des agents** : garantie par conteneurs séparés et politique réseau Kubernetes (Network Policies) si activée.

### 📊 Monitoring & Observabilité
- [x] **Mise en place de CloudWatch / Prometheus** : collecte des métriques de base (CPU, mémoire, disque) sur le VPS.
- [x] **Logs centralisés** : les logs des agents sont envoyés vers un système de collecte (ELK, Loki, ou simplement stdout récupéré par `kubectl logs`).
- [x] **Alertes de base** : seuils de charge, indisponibilité de pods.

### 📦 Gestion des coûts
- [x] **Estimation initiale des coûts** : calcul des ressources nécessaires, alerte budgétaire configurée.
- [x] **Optimisation** : adaptation des ressources aux besoins réels (ressizing si nécessaire).

---

## 🚀 2. Prochaines Étapes (Renforcer & Fiabiliser)

### 🔧 Amélioration de l’orchestration Kubernetes
- [ ] **Passer à un cluster multi‑nœuds** pour la haute disponibilité (si nécessaire).
- [ ] **Implémenter des Network Policies** pour isoler les communications entre agents (seuls les flux autorisés sont permis).
- [ ] **Mettre en place un Ingress Controller** (nginx, traefik) pour exposer la Gateway de façon sécurisée avec TLS automatique (Let's Encrypt).
- [ ] **Automatiser les sauvegardes** des volumes persistants (snapshots EBS-like chez Hostinger, ou sauvegarde des bases de données).

### 🔄 CI/CD avancé
- [ ] **Ajouter des tests d’intégration complets** dans le pipeline : après déploiement, exécuter une batterie de scénarios pour valider le bon fonctionnement des agents.
- [ ] **Mettre en place un environnement de préproduction** (staging) pour valider les modifications avant déploiement en production.
- [ ] **Automatiser la rotation des secrets** : renouvellement des clés API tous les 90 jours via un job Kubernetes CronJob.
- [ ] **Utiliser des outils de templating** (Helm) pour simplifier la gestion des manifests Kubernetes.

### 🔒 Sécurité renforcée
- [ ] **Tests de pénétration réguliers** sur l’infrastructure et les agents (simulation d’attaques, tentatives de contournement RBAC).
- [ ] **Mise en place de la signature et vérification des images** (Cosign, Notary) pour garantir l’intégrité des conteneurs déployés.
- [ ] **Audit de sécurité** des configurations Kubernetes (kube-bench, kube-hunter).
- [ ] **Chiffrement des données sensibles** au repos (si utilisation de volumes persistants).

### 📈 Observabilité & Alerting
- [ ] **Mettre en place un tableau de bord centralisé** (Grafana) pour visualiser l’état de santé de tous les agents et de l’infrastructure.
- [ ] **Définir des alertes plus fines** : temps de réponse des agents, taux d’erreur, saturation mémoire.
- [ ] **Collecte et analyse des logs** avec recherche full-text et corrélation (ELK Stack complet).

### 💰 Optimisation des coûts
- [ ] **Révision périodique des ressources allouées** (rightsizing) en fonction de la charge réelle.
- [ ] **Mise en place de quotas** par namespace Kubernetes pour éviter qu’un agent ne consomme trop de ressources.
- [ ] **Explorer l’utilisation de spot/preemptible instances** si la charge le permet (Hostinger ne propose pas ce type d’instances, mais à surveiller).

### 📄 Documentation & Formation
- [ ] **Rédiger un runbook d’incident** pour les pannes courantes (panne d’un agent, indisponibilité de la Gateway).
- [ ] **Former les équipes** à l’utilisation de Kubernetes et des outils de debugging (kubectl, logs, exec).

---
*Dernière mise à jour : 20 Février 2026*