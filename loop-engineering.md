# Loop Engineering — Document stratégique de référence

> **Usage :** ce document sert de base reproductible pour tout projet d'agent IA autonome.
> La **Partie A** est le socle stratégique : elle ne change pas d'un projet à l'autre.
> La **Partie B** est le template opérationnel : on en fait une copie par projet et on remplit les champs.
> Version : 1.0 — 2026-07-04

---

# PARTIE A — Socle stratégique (commun à tous les projets)

## A1. Principe fondateur

Le loop engineering transforme un LLM générateur de réponses en **système d'exécution** : l'agent ne livre pas une réponse, il livre un **travail fini et vérifié**. Pour cela, il boucle — essai, diagnostic, correction — jusqu'à atteindre une **preuve de succès** définie à l'avance, ou jusqu'à toucher une **limite** définie à l'avance.

La règle d'or : **pas de boucle sans critère de sortie mesurable ET sans limite de ressources.** Une boucle qui n'a que l'un des deux est soit inutile (elle ne sait pas quand elle a fini), soit dangereuse (elle ne sait pas quand s'arrêter).

## A2. Les trois blocs de l'architecture

| Bloc | Rôle | Question à laquelle il répond |
|---|---|---|
| **Déclencheur (Trigger)** | Sort l'agent de sa passivité : prompt utilisateur, cron, hook/webhook, heartbeat | *Quand l'agent démarre-t-il ?* |
| **Boucle de travail** | Plan → sélection d'outils → exécution → diagnostic → ajustement, en itérations | *Comment l'agent progresse-t-il ?* |
| **Vérification** | Compare le résultat aux seuils de preuve avant livraison | *Comment sait-on que c'est fini ?* |

Le bloc **vérification** est le plus fragile et le plus important. Un projet de loop engineering se conçoit **en partant de la vérification** : si on ne sait pas écrire le test de succès, on n'écrit pas la boucle.

## A3. Les seuils de preuve

Deux natures de tâches, deux méthodes :

- **Tâches à vérification objective** (code, comptabilité, calcul, tri de flux) : le critère existe déjà — tests automatisés, exactitude des formules, règles de classification. Le seuil de preuve = le test passe.
- **Tâches à vérification subjective** (rapports, analyses, stratégie) : le critère n'existe pas nativement. **L'agent doit s'arrêter et demander** : *« Quels tests dois-je mener pour valider ce travail ? »* L'utilisateur transforme alors une attente floue (« un bon rapport ») en points de contrôle vérifiables (« comparatif de 3 concurrents, calcul de ROI sur 5 ans, analyse de risque »).

Un seuil de preuve complet comporte **cinq éléments** :

1. **Objectif** — ce qui doit être accompli
2. **Outil** — ce qui sert à tester
3. **Test** — l'action de vérification
4. **Diagnostic** — l'analyse du pourquoi (échec ou réussite)
5. **Preuve de succès** — le marqueur final, binaire, qui autorise la livraison

**Interdiction de boucle floue :** si l'un des cinq éléments manque, la boucle ne démarre pas — l'agent bascule en mode question (bifurcation interactive).

## A4. Les trois sorties de boucle (toujours définies à l'avance)

Toute boucle a exactement trois sorties possibles, et chacune doit être spécifiée **avant** le lancement :

1. **Succès** — la preuve de succès est atteinte → livraison.
2. **Échec assumé** — limite d'itérations/temps/budget atteinte → l'agent s'arrête, **documente** où il en est, ce qu'il a essayé, pourquoi ça bloque, et restitue un état propre (rollback si nécessaire).
3. **Escalade humaine (HITL)** — l'agent détecte une zone d'inflexion (il consomme sans progresser, ou il manque un critère, ou l'action est risquée) → il stoppe et sollicite l'utilisateur. La réponse de l'utilisateur **enrichit la base de connaissances** pour les sessions futures.

## A5. Garde-fous obligatoires

Aucun agent en autonomie sans les quatre :

- **Cadrage d'itération** : nombre maximal de tentatives (typiquement 4 à 50 selon le coût unitaire d'un essai).
- **Boucle temporelle** : durée maximale de travail autonome (ex. 30 min).
- **Budget** : plafond de tokens/coût par session.
- **Rollback** : procédure de retour arrière si l'agent stagne ou casse quelque chose. L'agent ne doit jamais laisser un état intermédiaire cassé.

Et un cinquième, absent des présentations classiques mais indispensable :

- **Périmètre de permissions** : liste explicite de ce que l'agent peut faire **sans** validation (lire, écrire dans tel dossier, exécuter tel script) et de ce qui exige **toujours** une validation humaine (suppression, envoi externe — mail, publication —, dépense, action irréversible).

## A6. Observabilité

On ne pilote pas ce qu'on ne voit pas. Chaque projet doit produire :

- **Journal de boucle** : pour chaque itération — tentative, résultat du test, diagnostic, décision suivante. Format simple (fichier log ou markdown), relisible par un humain.
- **Heartbeat** : signal régulier prouvant que l'agent est actif ; sert aussi de déclencheur de relance en cas d'anomalie ou de déconnexion d'outil.
- **Suivi des coûts** : consommation réelle par session, comparée au budget. C'est la donnée qui décide du passage à un modèle moins cher (voir A8).

## A7. Mémoire et contexte

Principe : **le contexte chargé est l'ennemi de la performance et du coût.** Compartimenter :

| Fichier / mécanisme | Rôle | Cycle de vie |
|---|---|---|
| `AGENT.md` | Identité, mission, permissions, garde-fous | Permanent, versionné |
| `MEMORY.md` | Mémoire persistante inter-sessions (faits durables) | Permanent, curé régulièrement |
| `skills/*.md` | Compétences spécialisées, injectées **uniquement** quand la tâche le requiert (routing) | Permanent, versionné |
| Mémoire temporaire | Notes de travail d'un workflow multi-étapes | **Effacée en fin de tâche** |
| Fenêtres séparées | Sous-tâches lourdes déléguées dans un contexte isolé | Éphémère |

**Pérennisation des apprentissages :** quand une interaction avec l'utilisateur produit un nouveau critère ou une nouvelle procédure, elle est écrite dans un skill ou un fichier d'assets — jamais laissée dans la conversation. C'est ainsi que l'agent « progresse ». Chaque skill créé ou modifié par l'agent est **daté et relu par un humain** avant d'entrer en production : pas de boîte noire auto-apprise.

## A8. Stratégie de modèles : conception vs exécution

- **Phase de conception** : utiliser un modèle de pointe pour construire le cœur — orchestrateur, scripts, seuils de preuve, skills.
- **Phase d'exécution** : une fois le système stabilisé (taux de succès mesuré satisfaisant sur N sessions), basculer les tâches exécutives vers des modèles 10 à 20× moins chers. La rigueur de la structure compense la puissance moindre du modèle.
- **Portabilité** : le système repose sur des fichiers (`AGENT.md`, `MEMORY.md`, skills, scripts), pas sur une interface. Il doit rester transportable d'une machine ou d'un abonnement à l'autre.

**Critère de bascule** : ne pas changer de modèle « au feeling ». Définir un seuil (ex. ≥ 90 % de sessions terminées en « succès » sur les 20 dernières), tester le modèle économique sur un échantillon, comparer les taux, puis basculer.

## A9. Cycle de vie d'un projet de loop engineering

1. **Définir la vérification** (seuils de preuve) — si impossible, la tâche n'est pas prête pour l'autonomie.
2. **Remplir le template** (Partie B).
3. **Faire tourner en mode supervisé** : l'humain valide chaque livraison, on ajuste les seuils.
4. **Mesurer** : taux de succès, coût moyen, causes d'échec.
5. **Autonomiser progressivement** : élargir les permissions seulement sur ce qui est fiable.
6. **Optimiser les coûts** : bascule de modèle (A8) quand le critère est atteint.
7. **Capitaliser** : les procédures validées deviennent des skills réutilisables sur d'autres projets.

---

# PARTIE B — Template opérationnel (une copie par projet)

> Copier cette partie dans un fichier `LOOP-<nom-du-projet>.md`. Tout champ laissé vide = boucle non autorisée à démarrer.

## B0. Identité

- **Projet :**
- **Date / version :**
- **Responsable humain (HITL) :**
- **Modèle(s) utilisé(s) :** conception : ______ / exécution : ______

## B1. Mission

- **Objectif en une phrase :**
- **Livrable attendu (forme exacte : fichier, mail, rapport, code…) :**
- **Ce qui est HORS périmètre :**

## B2. Déclencheur

- **Type :** ☐ prompt manuel ☐ cron (planning : ______) ☐ hook/webhook (événement : ______) ☐ heartbeat
- **Contexte injecté au démarrage :** (quels fichiers, quelles données)

## B3. Seuils de preuve

> Un tableau par critère. La boucle ne démarre que si chaque critère a ses 5 éléments.

| # | Objectif | Outil de test | Test | Diagnostic en cas d'échec | Preuve de succès (binaire) |
|---|---|---|---|---|---|
| 1 | | | | | |
| 2 | | | | | |
| 3 | | | | | |

- **Nature de la vérification :** ☐ objective (tests existants) ☐ subjective (critères co-définis ci-dessus avec l'utilisateur)
- **Critères encore flous → questions à poser à l'utilisateur avant lancement :**

## B4. Garde-fous

- **Itérations max :** ______
- **Temps max de session :** ______
- **Budget max (tokens ou €) :** ______
- **Procédure de rollback :** (comment revenir à l'état initial si blocage)
- **Définition de « stagnation »** (déclenche l'arrêt anticipé) : ex. 3 itérations sans progression du diagnostic

## B5. Permissions

| Action | Autonome | Validation humaine requise |
|---|---|---|
| Lecture (préciser périmètre) | ☐ | ☐ |
| Écriture/création de fichiers (préciser dossiers) | ☐ | ☐ |
| Modification/suppression | ☐ | ☐ |
| Exécution de scripts/commandes | ☐ | ☐ |
| Envoi externe (mail, publication, API) | ☐ | ☐ |
| Dépense / transaction | jamais | toujours |

## B6. Sorties de boucle

- **Succès →** livraison sous la forme : ______ ; notification : ______
- **Échec assumé →** l'agent produit un rapport d'échec contenant : état atteint, tentatives menées, diagnostic du blocage, recommandation. Rollback : ☐ oui ☐ non
- **Escalade HITL →** conditions d'escalade : ______ ; canal de sollicitation : ______ ; la réponse est enregistrée dans : ______

## B7. Mémoire et skills

- **Skills chargés pour ce projet :** (liste des `skills/*.md` pertinents — et uniquement eux)
- **Ce que la session doit écrire en mémoire persistante :**
- **Ce qui reste en mémoire temporaire (effacé en fin de tâche) :**
- **Nouveaux apprentissages → fichier de destination :** ______ ; relu/validé par : ______

## B8. Observabilité

- **Journal de boucle :** emplacement : ______ ; contenu minimal : n° itération, action, résultat du test, diagnostic, décision
- **Heartbeat :** ☐ oui (fréquence : ______) ☐ non (justification : ______)
- **Suivi du coût :** où et comment il est relevé : ______

## B9. Indicateurs de pilotage (à relever après N sessions)

| Indicateur | Cible | Mesuré |
|---|---|---|
| Taux de sessions en « succès » | ex. ≥ 90 % | |
| Coût moyen / session | | |
| Itérations moyennes / session | | |
| Taux d'escalade HITL | (doit baisser dans le temps) | |

- **Critère de bascule vers un modèle économique :** ______
- **Date de la prochaine revue :** ______

---

## Annexe — Checklist de lancement (5 minutes avant d'autonomiser)

- [ ] Chaque seuil de preuve a ses 5 éléments (B3)
- [ ] Les 3 sorties de boucle sont définies (B6)
- [ ] Itérations max + temps max + budget max renseignés (B4)
- [ ] Rollback testé au moins une fois manuellement
- [ ] Permissions explicites, actions irréversibles verrouillées (B5)
- [ ] Journal de boucle opérationnel (B8)
- [ ] Le projet a tourné en mode supervisé avant tout passage en autonomie
