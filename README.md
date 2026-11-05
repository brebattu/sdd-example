# Workflow de développement d'une feature : Spec Kit + IBM Bob

Cas de base : une feature de taille moyenne, un dev, quelques jours de travail.

## 1. Le process

```mermaid
flowchart TD
    A["1. Ticket prêt<br/>(BA)"] --> B["2. Spec<br/>/speckit-specify + /speckit-clarify<br/>(dev)"]
    B --> C{"Validation spec<br/>(BA)"}
    C -- ajustements --> B
    C -- OK --> D["3. Plan technique<br/>/speckit-plan<br/>(dev)"]
    D --> E{"Validation plan<br/>(Techlead)"}
    E -- ajustements --> D
    E -- OK --> F["4. Tâches + PR spec<br/>/speckit-tasks<br/>(dev)"]
    F --> G{"Revue PR spec<br/>(BA + Techlead)"}
    G -- corrections --> F
    G -- merge --> H["5. Implémentation<br/>/speckit-implement<br/>(dev + Bob)"]
    H --> I["6. Convergence<br/>/speckit-converge<br/>(dev + Bob)"]
    I -- écarts avec la spec --> H
    I -- Converged --> J["7. PR code"]
    J --> K{"CI + ArchUnit<br/>verts ?"}
    K -- non --> H
    K -- oui --> L{"Revue humaine<br/>(autre dev)"}
    L -- corrections --> H
    L -- merge --> M["8. Recette<br/>(testeur)"]
    M --> N["Déploiement<br/>(pipeline du client)"]
    N --> O["9. Rétro<br/>(équipe)"]
    O -. nouvelle règle .-> P["AGENTS.md / ArchUnit / ADR"]
```

## 2. Ce qui alimente le workflow

Trois familles, chacune avec un rôle différent. La base projet est posée une fois, puis enrichie à chaque rétro.

```mermaid
flowchart LR
    subgraph G["Guides : Bob sait quoi faire"]
        G1["AGENTS.md"]
        G2["Constitution Spec Kit"]
        G3["ADR"]
        G4["Module de référence"]
    end
    subgraph S["Capteurs : le résultat est bon ?"]
        S1["CI : build, tests, lint, scan"]
        S2["ArchUnit"]
        S3["Revue humaine"]
        S4["Recette du testeur"]
    end
    subgraph R["Garde-fous : Bob ne fait pas de dégâts"]
        R1["Politique d'auto-approve"]
        R2[".bobignore"]
        R3["Accord client sur les données"]
    end
    G --> W["Étapes 2 à 5 :<br/>spec, plan, tâches, implémentation"]
    S --> V["Étapes 5 à 8 :<br/>implémentation, convergence, revue, recette"]
    R --> W2["Étape 5 :<br/>implémentation"]
    O["9. Rétro"] -. enrichit .-> G
    O -. enrichit .-> S
```

## 3. Ce qui intervient à chaque étape

1. **Ticket prêt** : Definition of Ready (besoin clair, critères d'acceptation présents).
2. **Spec** : constitution Spec Kit, AGENTS.md, ADR (contraintes déjà décidées).
3. **Plan technique** : ADR et constitution (validation du techlead contre ces références), module de référence comme modèle.
4. **Tâches + PR spec** : règles de PR (une PR spec, une PR code), revue BA + techlead.
5. **Implémentation** : AGENTS.md (stack, commandes, interdits), module de référence, politique d'auto-approve, `.bobignore`, CI et ArchUnit lancés en local et en continu.
6. **Convergence** : critères d'acceptation de la spec comme référence.
7. **PR code** : CI (build, tests, lint, scan des dépendances), ArchUnit, revue humaine, Definition of Done.
8. **Recette** : critères d'acceptation de la spec, cas de test du testeur.
9. **Rétro** : toute dérive de Bob devient une ligne dans AGENTS.md, une règle ArchUnit ou une ADR.

## 4. Qui valide quoi

- **BA** : la spec (étape 2) et le fonctionnel de la PR spec (étape 4).
- **Techlead** : le plan (étape 3) et le technique de la PR spec (étape 4). En filet à l'étape 7 seulement si la PR touche à l'architecture.
- **Autre dev** : la PR code (étape 7).
- **Testeur** : la recette (étape 8).
