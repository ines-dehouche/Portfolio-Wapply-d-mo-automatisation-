# Démonstration — Automatisation web avec Playwright et intégration IA

## Contexte

Ce projet est une **reconstitution pédagogique**, entièrement réécrite, illustrant des concepts techniques explorés lors d'un stage en développement Python orienté automatisation web. Le code présenté ici cible un site de démonstration générique et ne reprend aucune ligne de code, aucun nom de plateforme réelle, ni aucune information propre à l'entreprise d'accueil.

## Objectif du projet

Illustrer une architecture d'automatisation de formulaires web modulaire et réutilisable, capable de :

- se connecter à un site et **conserver une session** entre deux exécutions ;
- **détecter dynamiquement** des éléments d'interface (boutons, menus déroulants, formulaires) ;
- remplir et soumettre un formulaire de façon fiable, y compris sur des composants web modernes (menus personnalisés, fenêtres popup) ;
- générer un **contenu textuel personnalisé** via un modèle de langage (LLM), à partir du contexte de la page ;
- **suivre les actions déjà effectuées**, pour éviter les répétitions inutiles.

## Architecture

Le projet est découpé en modules indépendants, chacun avec une responsabilité claire :

| Fichier | Rôle |
|---|---|
| `settings.py` | Configuration générale (profil utilisateur fictif, options du navigateur) |
| `session_manager.py` | Connexion et persistance de session |
| `page_parser.py` | Détection des éléments cibles sur la page |
| `form_filler.py` | Remplissage et soumission d'un formulaire |
| `orchestrator.py` | Coordination du processus global |
| `activity_tracker.py` | Suivi des actions déjà réalisées |
| `main.py` | Point d'entrée (mode test / mode réel) |

Ce découpage facilite la maintenance : si l'interface d'un site change, seul le module concerné (`form_filler.py` par exemple) doit être adapté, sans toucher au reste du système.

## Exemple — Persistance de session avec Playwright

```python
from pathlib import Path
import json

# Sauvegarde de la session après une connexion réussie
storage = context.storage_state()
Path("session.json").write_text(json.dumps(storage))

# Réutilisation de la session lors du lancement suivant
session_file = Path("session.json")
if session_file.exists():
    context = browser.new_context(storage_state=str(session_file))
else:
    context = browser.new_context()
```

Cette approche évite de se reconnecter à chaque exécution, ce qui réduit le temps de traitement et le risque d'être identifié comme un comportement automatisé.

## Exemple — Génération de contenu personnalisé via un LLM

```python
def build_prompt(profile, context_title, context_description):
    return (
        f"Rédige un texte court et personnalisé pour :\n"
        f"Profil : {profile.summary}\n"
        f"Sujet : {context_title}\n"
        f"Contexte : {context_description}\n\n"
        "Réponds en 150 à 250 mots, dans un ton professionnel."
    )

prompt = build_prompt(profile, titre, description)
generated_text = call_llm_api(prompt, max_tokens=300, temperature=0.6)
```

Si l'appel à l'API échoue (absence de connexion, quota dépassé), le système peut revenir automatiquement sur un contenu par défaut, afin de ne jamais bloquer le processus.

## Compétences illustrées

- **Langage & outils** : Python, Playwright
- **Automatisation web** : gestion de sessions, détection dynamique d'éléments, interaction avec des composants JS modernes (menus personnalisés, fenêtres popup)
- **Intégration IA** : appel à une API de LLM pour générer du contenu contextualisé
- **Architecture logicielle** : séparation des responsabilités, configuration externalisée, code réutilisable et testable

## Note importante

Ce dépôt a une vocation strictement pédagogique et démonstrative. Il ne contient :
- aucun code source réel d'une entreprise,
- aucune donnée personnelle ou identifiant,
- aucune référence à une plateforme web réelle ciblée par un projet professionnel.
