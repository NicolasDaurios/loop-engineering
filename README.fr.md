# Loop engineering — un framework reproductible pour agents IA autonomes

Le loop engineering transforme un LLM générateur de réponses en **système d'exécution** : l'agent ne livre pas une réponse, il livre un **travail fini et vérifié**. Il boucle — essai, diagnostic, correction — jusqu'à atteindre une **preuve de succès** définie à l'avance, ou jusqu'à toucher une **limite de ressources** définie à l'avance.

**La règle d'or : pas de boucle sans critère de sortie mesurable ET sans limite de ressources.** Une boucle qui n'a que l'un des deux est soit inutile (elle ne sait pas quand elle a fini), soit dangereuse (elle ne sait pas quand s'arrêter).

## Contenu du repo

| Fichier | Rôle |
|---|---|
| [loop-engineering.md](loop-engineering.md) | Le document de référence. Partie A = socle stratégique, commun à tous les projets. Partie B = template opérationnel. |
| [TEMPLATE-LOOP.md](TEMPLATE-LOOP.md) | La Partie B extraite en template autonome — à copier pour chaque nouvelle boucle. |
| [workflow-agentique.md](workflow-agentique.md) | La boucle **macro** : le workflow d'orchestration en 7 étapes qui entoure le loop engineering. Le loop engineering est l'étage **micro** (le moteur d'une tâche) ; ce document est l'étage **macro** (piloter un projet entier avec des agents). |
| [README.md](README.md) | Ce README en anglais. |

## Comment l'utiliser

1. **Copier le template** dans un fichier `LOOP-<votre-tâche>.md`.
2. **Commencer par les seuils de preuve** (section B3) : objectif, outil de test, test, diagnostic, preuve de succès. Si les cinq éléments ne se remplissent pas, la tâche n'est pas mûre pour une boucle autonome.
3. **Remplir le reste** : déclencheur, garde-fous (itérations max, temps max, budget, rollback), permissions, les trois sorties de boucle (succès / échec assumé / escalade humaine), mémoire, observabilité.
4. **La règle d'engagement : tout champ vide = boucle non autorisée à démarrer.** Un agent qui respecte cette règle vous posera les bonnes questions au lieu d'improviser.
5. **Faire tourner en supervisé d'abord**, mesurer (taux de succès, coût par session, taux d'escalade), puis autonomiser progressivement — et basculer l'exécution vers un modèle moins cher quand les chiffres le justifient.

Le framework est indépendant du modèle et repose sur des fichiers (markdown + scripts) : une boucle conçue avec un modèle de pointe peut être exécutée par un modèle bien moins coûteux, et le paquet reste portable d'une machine ou d'un fournisseur à l'autre.

## Licence

[CC BY 4.0](LICENSE) — réutilisation et adaptation libres, avec attribution.
