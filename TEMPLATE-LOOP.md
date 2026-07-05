# LOOP — <nom de la tâche>

> Template opérationnel du loop engineering (Partie B de [loop-engineering.md](loop-engineering.md)).
> Copier ce fichier en `LOOP-<tâche>.md`, une copie par projet.
> **Règle d'engagement : tout champ laissé vide = boucle non autorisée à démarrer.**

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

## Checklist de lancement (5 minutes avant d'autonomiser)

- [ ] Chaque seuil de preuve a ses 5 éléments (B3)
- [ ] Les 3 sorties de boucle sont définies (B6)
- [ ] Itérations max + temps max + budget max renseignés (B4)
- [ ] Rollback testé au moins une fois manuellement
- [ ] Permissions explicites, actions irréversibles verrouillées (B5)
- [ ] Journal de boucle opérationnel (B8)
- [ ] Le projet a tourné en mode supervisé avant tout passage en autonomie
