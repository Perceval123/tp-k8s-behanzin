# TP Kubernetes — Cluster bac à sable et déploiement d'application

> **Auteur :** Perceval BEHANZIN
> **Formation :** TP Kubernetes Niveau 1 — AUKFOOD
> **Date de rendu :** vendredi 30 octobre 2026

## Résumé

| Partie                 | Réalisation                                                                                                                      |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------------|
| 1 — Cluster bac à sable | Cluster **`k8s-behanzin`** créé avec **kind** dans Docker Desktop (Windows 10) : 1 control plane + 3 workers, Kubernetes v1.37.0 |
| 2 — Application         | Deux blogs **Ghost** (`ghost:6-alpine`, base SQLite) isolés dans les namespaces **`behanzin-blog1`** et **`behanzin-blog2`**     |
| Bonus                   | Persistance des articles avec un volume (PersistentVolumeClaim) : l'article survit à la suppression du Pod                       |

| Blog   | Namespace        | URL                    | Titre              |
|--------|------------------|------------------------|--------------------|
| Blog 1 | `behanzin-blog1` | http://localhost:30081 | Blog 1 de behanzin |
| Blog 2 | `behanzin-blog2` | http://localhost:30082 | Blog 2 de behanzin |

## Sommaire

### [Partie 1 — Cluster bac à sable](1-cluster/cluster.md)

1. [Choix de la solution](1-cluster/cluster.md#1-choix-de-la-solution)
2. [Prérequis](1-cluster/cluster.md#2-prérequis)
3. [Procédure d'installation](1-cluster/cluster.md#3-procédure-dinstallation)
4. [Schéma d'architecture](1-cluster/cluster.md#4-schéma-darchitecture)
5. [Vérifications](1-cluster/cluster.md#5-vérifications)
6. [Difficultés rencontrées](1-cluster/cluster.md#6-difficultés-rencontrées)

### [Partie 2 — Déploiement de deux blogs Ghost isolés](2-application/application.md)

1. [Vue d'ensemble](2-application/application.md#1-vue-densemble)
2. [Manifestes](2-application/application.md#2-manifestes)
3. [Procédure de déploiement](2-application/application.md#3-procédure-de-déploiement)
4. [Configuration des blogs](2-application/application.md#4-configuration-des-blogs)
5. [Preuves de fonctionnement en console](2-application/application.md#5-preuves-de-fonctionnement-en-console)
6. [Preuve de fonctionnement web](2-application/application.md#6-preuve-de-fonctionnement-web)
7. [Isolation des namespaces](2-application/application.md#7-isolation-des-namespaces)
8. [Questions](2-application/application.md#8-questions)
9. [Suppression](2-application/application.md#9-suppression)
10. [Bonus — Persistance des articles avec un volume](2-application/application.md#10-bonus--persistance-des-articles-avec-un-volume)
11. [Difficultés rencontrées](2-application/application.md#11-difficultés-rencontrées)

## Arborescence du rendu

```
tp-k8s-behanzin/
├── README.md                     # ce fichier : sommaire, auteur, liens
├── 1-cluster/
│   ├── cluster.md                # documentation partie 1
│   ├── config/
│   │   └── kind-config.yaml      # configuration du cluster kind
│   └── captures/
└── 2-application/
    ├── application.md            # documentation partie 2
    ├── manifests/
    │   ├── blog1/                # namespace, deployment, service, pvc
    │   │   ├── 01-namespace.yaml
    │   │   ├── 02-deployment.yaml
    │   │   ├── 03-service.yaml
    │   │   └── 04-pvc.yaml
    │   └── blog2/                # idem pour le blog 2
    └── captures/
```

## Démarrage rapide

Prérequis : Docker Desktop démarré, kind et kubectl installés (détails dans la [Partie 1](1-cluster/cluster.md#2-prérequis)). Commandes à lancer depuis la racine du dépôt :

```
kind create cluster --config 1-cluster\config\kind-config.yaml
kubectl apply -f 2-application\manifests\blog1
kubectl apply -f 2-application\manifests\blog2
kubectl get pods -A -l owner=behanzin -o wide
```

Les blogs sont ensuite accessibles sur http://localhost:30081 et http://localhost:30082 (administration : `/ghost`).

Tout supprimer :

```
kind delete cluster --name k8s-behanzin
```

## Règles de personnalisation respectées

| Règle                                   | Mise en œuvre                                                                 |
|-----------------------------------------|-------------------------------------------------------------------------------|
| Cluster `k8s-<nom>`                     | `k8s-behanzin`                                                                |
| Namespaces `<nom>-blog1`, `<nom>-blog2` | `behanzin-blog1`, `behanzin-blog2`                                            |
| Label `owner: <nom>` sur les ressources | `owner: behanzin` sur Namespace, Deployment, Pods, Service et PVC             |
| Titre des blogs contenant le nom        | « Blog 1 de behanzin », « Blog 2 de behanzin »                                |
| Invite de commande ou `date` visible    | Invite `C:\Users\DUP\Documents\tp-k8s-behanzin>` sur chaque sortie et capture |
