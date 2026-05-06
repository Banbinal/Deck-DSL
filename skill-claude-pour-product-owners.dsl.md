---
title: Créer un skill Claude Code
subtitle: Codifier votre expertise de Product Owner en un assistant réutilisable
eyebrow: Guide pratique · Product Management
author: Atelier Skills
date: 2026-05-06
theme:
  accent: "#1F4E79"
---

# En une phrase

Un **skill Claude Code** est un dossier de quelques fichiers qui apprend à Claude *quand* intervenir et *comment* livrer un résultat conforme à votre standard — PRD, user stories, revue de sprint, OKR. Vous le créez une fois, vous le déclenchez à vie.

> [!tip]
> Le vrai levier
> Le skill ne remplace pas le PO. Il **encode** sa méthode pour que chaque livrable suive le même format, le même ton, les mêmes critères d'acceptation — sans avoir à les redicter à chaque conversation.

## Ce qu'un skill change pour un PO

```kpi
Temps par livrable | -60% | écrit en 1 prompt
Cohérence de format | 100% | template figé
Onboarding équipe | -2 sem | méthode partageable
Coût d'apprentissage | nul | usage en langage naturel
```

---

# 1 · Comprendre l'anatomie

Un skill est un dossier. Trois couches qui se chargent **progressivement** — Claude n'avale pas tout, il pioche au besoin.

## Progressive disclosure : ce que Claude voit, et quand

```pyramid
Métadonnées: name + description, toujours en mémoire (~100 mots)
Corps SKILL.md: chargé quand le skill se déclenche (<500 lignes)
Ressources bundle: scripts, templates, références, à la demande
Données de la conversation: ce que vous fournissez en direct
```

> [!info]
> Pourquoi cette structure
> Les métadonnées doivent tenir dans le contexte permanent de Claude. Le corps n'est lu qu'au déclenchement. Les ressources lourdes (templates Word, exemples PDF) ne sont touchées que si la tâche les demande.

## Structure de fichiers type

```org
mon-skill-pr-d
- SKILL.md
- scripts
  - genere_user_story.py
- references
  - definition_of_done.md
  - exemples_prd.md
- assets
  - template_prd.docx
```

---

# 2 · Quatre cas d'usage qui valent le coup pour un PO

```cards
Rédaction de PRD | Standardise sections, critères, métriques de succès | ◆
User stories | Format Gherkin, AC, estimation, priorisation | ✦
Revue de sprint | Synthèse démo, blockers, vélocité, next steps | ⚡
Préparation MBR | Compile KPIs, narratif, décisions à valider | ▲
```

> [!note]
> Choisissez votre **premier** skill autour d'un livrable que vous produisez **chaque semaine**. Les ROI explosent sur la répétition.

---

# 3 · Le processus de création

Cinq étapes. Comptez une demi-journée pour le premier, une heure pour les suivants.

```timeline
Étape 1 | Capturer l'intention | Quoi, quand, pour qui
Étape 2 | Rédiger SKILL.md | Frontmatter + corps
Étape 3 | Bundler les ressources | Scripts, templates
Étape 4 | Écrire 2-3 tests | Prompts réalistes
Étape 5 | Itérer | Lire, ajuster, redéployer
```

## Le flux décisionnel

```flow
[Idée de skill] -> {Tâche répétitive ?}
{Tâche répétitive ?} -non-> (Pas de skill, juste un prompt)
{Tâche répétitive ?} -oui-> [Capturer 2-3 prompts réels]
[Capturer 2-3 prompts réels] -> [Rédiger SKILL.md]
[Rédiger SKILL.md] -> {Output mesurable ?}
{Output mesurable ?} -oui-> [Écrire des assertions]
{Output mesurable ?} -non-> [Feedback humain qualitatif]
[Écrire des assertions] -> [Itérer]
[Feedback humain qualitatif] -> [Itérer]
[Itérer] -> (Skill stable)
```

---

# 4 · Le fichier SKILL.md, pièce maîtresse

Tout skill commence par un fichier `SKILL.md` avec un **frontmatter YAML** et un **corps en markdown impératif**.

## Les deux champs qui décident de tout

```table
| Champ | Rôle | Erreur fréquente |
| name | Identifiant du skill | Trop générique (`pr-d` au lieu de `prd-produit-saas`) |
| description | Quand déclencher + ce que ça fait | Trop modeste — Claude **sous-déclenche** |
| corps | Instructions, exemples, format de sortie | Trop de "TOUJOURS" en majuscules, pas assez de "pourquoi" |
```

> [!warning]
> Le piège n°1 : la description timide
> Claude a tendance à **ne pas** déclencher un skill quand il en a un. Soyez assertif : listez les synonymes, les contextes voisins, les façons dont un PO formulerait sa demande sans utiliser le mot exact du skill.

## Description : faible vs forte

```table
| Version | Texte |
| Faible | "Génère un PRD." |
| Forte | "Utilise ce skill dès qu'il s'agit de rédiger une spec produit, un brief feature, une note de cadrage, un PRD, un MRD, un *one-pager produit*, ou toute formalisation d'un besoin avant build — même si l'utilisateur ne dit pas explicitement *PRD*." |
```

---

# 5 · Tester et itérer

Un skill se valide comme un produit : avec des cas réels, pas avec votre intuition.

## Boucle d'itération

```sequence
PO -> Claude: Lance le skill sur 3 prompts réels
Claude --> PO: Produit 3 livrables
PO -> Reviewer: Annote ce qui cloche (ton, format, manque)
Reviewer --> PO: Liste de feedbacks
PO -> SKILL.md: Ajuste instructions, ajoute exemple
PO -> Claude: Rejoue les 3 mêmes prompts
note over PO, Claude: 2 à 4 itérations suffisent généralement
```

## Où concentrer l'effort

```matrix
x: Effort | low | high
y: Impact | low | high
Soigner la description | low | high
Bundler un template | low | high
Ajouter 2-3 exemples | low | high
Écrire un script Python | high | high
Renommer le skill | low | low
Documenter chaque cas exotique | high | low
Réécrire le corps à chaque feedback | high | low
```

---

# 6 · Pièges classiques

```cards
Sous-déclenchement | Description trop modeste : Claude n'invoque pas le skill | ⚠
Sur-spécification | Trop de règles rigides — le skill ne généralise plus | ◆
Sur-ajustement aux tests | Marche sur 3 prompts, échoue sur le 4ᵉ | ✦
Tout en majuscules | "TOUJOURS / JAMAIS" sans le pourquoi → moins efficace | ▲
```

> [!danger]
> À éviter absolument
> Mettre des secrets, des données client, ou des credentials dans le skill. Tout ce qui est dans le dossier est lisible par toute personne ayant accès au skill.

---

# 7 · Plan d'action sur 2 semaines

```funnel
Idées de livrables récurrents | 10
Skills priorisés (semaine 1) | 3
Skill MVP rédigé | 1
Skill testé sur 3 cas réels | 1
Skill partagé à l'équipe | 1
```

## Prochains pas

```timeline
J+0 | Lister vos 5 livrables hebdo
J+2 | Choisir le 1er skill candidat
J+4 | Rédiger SKILL.md v1
J+7 | Tester sur 3 prompts réels
J+10 | Itération v2 + partage équipe
J+14 | Rétro & choisir le skill n°2
```

> [!tip]
> Le réflexe à installer
> Chaque fois que vous écrivez le **même type de document pour la troisième fois** dans le mois, demandez-vous : *est-ce que ça mérite un skill ?* Souvent, oui.

---

*Source : [skill-creator SKILL.md](https://github.com/anthropics/skills/blob/main/skills/skill-creator/SKILL.md) — Anthropic, 2026.*
