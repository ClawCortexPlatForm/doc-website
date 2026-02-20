# **Checklist Skills - Plateforme Multi-Agents OpenClaw**

Ce document répertorie les mesures mises en place concernant la gestion des skills sur la plateforme Investec Advisory, ainsi que les prochaines étapes pour sécuriser et optimiser leur usage.

# 🛠️** 1. Ce qui a été réalisé (Fondations)**

## **Sélection & Installation**

- **Inventaire des besoins par agent** : Identification des skills nécessaires pour chaque agent métier.
- **Google Sheet de référence** : Création d'un tableau partagé listant l'ensemble des skills nécessaires pour chaque agent.
- **Documentation d'installation** : Rédaction d'un guide pas-à-pas expliquant comment installer un skill sur OpenClaw, à destination des membres de l'équipe.

## **Skills Déployés**

- **Gog (Google Workspace)** : Installation sur Windows (binaires + config OpenClaw) et authentification via OAuth Desktop Client — déployé pour les agents Origination et Delivery.
- **Realtime Web Search** : Skill de recherche web en temps réel, alimentant les agents Media Intelligence et Fact-Checking.
- **LinkedIn Skill** : Intégration aux réseaux sociaux professionnels pour la veille et l'origination.
- **Twitter Skill** : Intégration pour la veille média et le suivi des signaux marché.
- **Document Generation** : Skill de génération de livrables (pitchs, CIM, PowerPoint) pour les agents Origination et Delivery.
- **Summarize** Sert à résumer rapidement des contenus (URL, PDF, docs, etc.) pour extraire l’essentiel sans tout lire.
- **Ontology** Sert à structurer la connaissance en graphe (entités, liens, dépendances). Très utile pour suivre projets, documents, tâches, relations entre infos.
- **Proactive Agent** Sert à rendre l’agent plus proactif : suivi continu, routines/cron, anticipation, amélioration continue.
- **Tavily Web Search** Sert à faire de la recherche web orientée IA, avec résultats pertinents et exploitables pour veille, fact-check et analyse.
- **Find Skills** Sert à découvrir des skills adaptés à un besoin précis (“je veux faire X, quel skill installer ?”).
- **API Gateway** Sert à connecter l’agent à des APIs tierces via une couche centralisée (auth/OAuth et appels API gérés).
- **Nano Pdf** Sert à manipuler/éditer des PDF avec des instructions en langage naturel.
- **Quality Documentation Manager** Sert à gérer la gouvernance documentaire (numérotation, versions, changements, conformité doc), pratique pour versioning/qualité.
- **Lawyer** Sert à aider sur l’analyse/rédaction/revue juridique (contrats, conformité, points légaux).
- **Notion** Sert à lire/écrire dans Notion (pages, blocs, bases de données), utile pour centraliser notes, pipeline, suivi d’actions.
- **Gog** Sert à interagir avec Google Workspace (Gmail, Calendar, Drive, Sheets, Docs, etc.).
- **Compliance Officer** Sert à vérifier des contenus/process contre des cadres réglementaires (compliance marketing/réglementaire).
- **Compliance Audit Generator** Sert à générer des audits de conformité structurés (constats, risques, plan de remédiation).
- **Praesidia** Sert à la sécurité/confiance agentique : vérification d’agents, trust score, guardrails de sécurité/compliance.

## **Architecture & Interopérabilité**

- **Protocole MCP** : Standardisation des skills via le protocole MCP pour garantir l'interopérabilité entre tous les agents et sources de données.
- **Credentials isolés par skill** : Authentification OAuth 2.0 avec isolation des credentials pour chaque skill, évitant toute fuite de droits entre agents.
- **Environnement isolé par agent/skill** : Chaque agent dispose d'un contexte d'exécution cloisonné, limitant la surface d'exposition.
- **Anti-doublons Media Intelligence** : Mécanisme de déduplication basé sur Google Sheets pour la veille automatique (CRON), évitant la remontée d'informations déjà traitées.
- **Gestion des rate limits API Google** : Mise en place de stratégies de throttling et de file d'attente pour respecter les quotas de l'API Google Workspace.

## **Validation & Contrôle Qualité**

- **Tests fonctionnels** : Vérification du bon fonctionnement de chaque skill dans le contexte Investec Advisory.
- **Évaluation de la pertinence** : Validation que chaque skill répond au besoin métier identifié et apporte une valeur ajoutée mesurable.
- **Détection des conflits** : Analyse des interactions entre skills pour identifier et résoudre les éventuels conflits ou doublons fonctionnels.

# 🚀** 2. Prochaines Étapes (Fiabiliser & Optimiser)**

## **Sécurité & Gouvernance des Skills**

- **Audit de provenance** : Vérifier l'origine et la fiabilité de chaque skill installé (auteur, réputation, code source auditable).
- **Contrôle des permissions** : S'assurer que chaque skill n'accède qu'aux ressources strictement nécessaires (principe du moindre privilège).
- **Isolation par rôle** : Restreindre les skills disponibles selon le rôle de l'agent — un agent Compliance ne doit pas avoir accès aux skills de génération de livrables clients.
- **Politique de mise à jour** : Définir un processus de validation avant toute mise à jour d'un skill (test en staging avant production).
- **Rotation des credentials** : Aligner la rotation des secrets OAuth et clés API des skills sur la politique globale à 90 jours définie dans la checklist de gouvernance.

## **Fiabilité & Maintenance Continue**

- **Monitoring actif** : Surveillance continue des skills (taux d'échec, latence, comportements anormaux) intégrée au tableau de bord de gouvernance.
- **Tests de régression** : Après chaque modification de l'environnement, rejouer les scénarios de test pour détecter toute régression.
- **Gestion des dépendances** : Documenter les dépendances entre skills et s'assurer qu'une défaillance (ex: rate limit Google) ne crée pas d'effet cascade sur les agents alimentés.

## **Optimisation & Veille**

- **Veille sur les nouveaux skills** : Surveillance régulière de ClawHub pour identifier de nouveaux skills pertinents pour les cas d'usage Investec Advisory.
- **Décommissionnement** : Définir un processus de suppression propre pour les skills obsolètes afin de réduire la surface d'attaque.

## **Documentation & Traçabilité**

- **Registre des skills** : Maintenir un inventaire à jour listant pour chaque skill : version, agent(s) alimenté(s), date d'installation, responsable et statut de validation.
- **Journalisation de l'usage** : Activer les logs d'utilisation des skills et les intégrer au fichier audit.log existant pour une traçabilité complète.

# Comment installer un skill

[https://clawhub.ai](https://clawhub.ai)

## Installation

```
npx clawhub@latest install <nom-du-skill>

```

Ça créé ==~/.openclaw/workspace/skills/<nom-du-skill>==

## Configuration

On peut activer le skill soit manuellement soit via le dashboard

**Manuellement**

- Ajouter dans ==~/.openclaw/openclaw.json==

```
"skills": {
    "entries": {
      "<nom-du-skill>": {
        "enabled": true
      },
    },
  },

```

**Via le dashboard**

- Aller dans le dashboard > skills
- Cliquer sur le bouton « Enable »  
  ![Pasted Graphic 2.png](Attachments/Pasted%20Graphic%202.png)
- Redémarrer openclaw

```
openclaw gateway stop
openclaw gateway

```
