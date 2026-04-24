<div align="center" class="hero-header" markdown="1">

![OKDP Logo](logo/inverted/okdp-inverted.png)

# Open Kubernetes Data Platform

Une plateforme de données cloud-native, libre et 100% open source.<br/>
Modulaire, souveraine et propulsée par la communauté.

<div class="hero-nav" markdown="1">

[Consulter la Roadmap](https://okdp.io/roadmap/)
[Architecture & Modules](#architecture)
[Site Officiel](https://okdp.io)

</div>

</div>

---

## 🏗️ Architecture

L'architecture d'OKDP est articulée autour de deux couches majeures : un écosystème de **Modules Data & IA** de référence et un plan de contrôle (Control Plane) unifié.

### Modules Data & IA
*Un catalogue d'outils open source de référence (liste non exhaustive). Utilisables unitairement ou combinés, sans dépendre du Control Plane OKDP (UI/Server).*

<div class="grid cards data-stack-grid" markdown>

-   **⚡ Ingestion & Streaming**
    - Apache NiFi
    - Apache Kafka
    - Apache Flink

-   **🚀 Lakehouse & Analytics**
    - Apache Spark
    - Trino
    - Polaris Catalog (Apache Iceberg)

-   **🧪 Data Science**
    - JupyterLab

-   **🤖 IA & MLOps**
    - Kubeflow
    - MLflow
    - LLM Serving (v1.2.0+)

-   **📊 Visualization & BI**
    - Apache Superset

-   **⚙️ Orchestration & Governance**
    - Apache Airflow
    - OpenMetadata

</div>

### OKDP Control Plane
*La couche d'automatisation d'OKDP : orchestration, isolation multi-tenant et gouvernance pour une utilisation clé en main de toute la stack.*

<div class="grid cards control-plane-grid" markdown>

-   **🖥️ Server / UI / CLI**

    Portail Web et interfaces unifiées pour les administrateurs et les utilisateurs.

-   **🗂️ Project & Quota Management**

    Isolation multi-tenant sécurisée et gestion des limites de ressources par projet.

-   **🔒 Auth & Secrets Management**

    Authentification OIDC de bout en bout et gestion sécurisée des secrets et du RBAC.

-   **📈 Observability**

    Collecte centralisée des métriques, logs et traces pour l'ensemble de la plateforme.

</div>

## 🛠️ Prérequis Infrastructure

OKDP nécessite un cluster **Kubernetes** et un **stockage objet (S3)** pour fonctionner. L'infrastructure est hors-scope du projet, mais une **Sandbox d'Intégration** (sur Kind ou Minikube) est disponible pour tester la plateforme avec tout l'environnement nécessaire.

---

## 🤝 Communauté & TOSIT

OKDP est un projet soutenu par l'association **TOSIT** (The Open Source I Trust), initié par la **DGFiP**, **Orange** et bien d'autres entreprises. L'objectif est de garantir une stack technologique data souveraine, puissante, accessible à tous et sous licence libre.

- **Découvrir le projet** : [okdp.io](https://okdp.io)
- **Échanges & Discussions** : Rejoignez le canal [Mattermost OKDP (TOSIT)](https://framateam.org/tosit/channels/okdp)
- **Réunion Technique Hebdo.** : Tous les mercredis à 10h00 (CET)
- **Artefacts Java** : [Maven Central (io.okdp)](https://central.sonatype.com/namespace/io.okdp)
- **Images Docker** : [Quay.io (okdp)](https://quay.io/organization/okdp)
- **Contribuer** : Découvrez notre [Appel à contribution](https://okdp.io#community)

---

## 📋 Produit & Licence

- **Roadmap** : Consultez la [Roadmap officielle](https://okdp.io/roadmap/) sur notre site web (release v1.0.0 prévue en Juin 2026).
- **Licence** : [Apache License 2.0](./LICENSE)
