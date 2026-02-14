# Tâches proposées après parcours de la base de code

## 1) Coquille typographique / cohérence de nommage
- **Constat**: Le fichier et la classe `GoNativeWebviewClient` utilisent `Webview` (v minuscule), alors que le reste du code utilise majoritairement `WebView` (`PoolWebViewClient`, `WebViewSetup`, etc.).
- **Risque**: dette de lisibilité et confusion lors des recherches/refactors automatiques.
- **Tâche proposée**:
  1. Renommer `GoNativeWebviewClient` en `GoNativeWebViewClient` (fichier + classe).
  2. Mettre à jour toutes les références/imports.
  3. Vérifier la compilation Android (`assemble`/`test`).

## 2) Correction de bug (robustesse runtime)
- **Constat**: Dans `Installation.getInfo`, l'appel à `SubscriptionManager.from(context)` puis l'itération sur `getActiveSubscriptionInfoList()` n'a pas de garde contre les cas `null`.
- **Risque**: `NullPointerException` sur des appareils sans téléphonie active, eSIM non provisionnée ou selon constructeur/ROM.
- **Tâche proposée**:
  1. Ajouter des vérifications `subscriptionManager != null` et `activeSubscriptions != null`.
  2. En cas d'absence d'abonnements, journaliser proprement et ne pas remplir `carrierName(s)`.
  3. Ajouter un test unitaire/instrumenté qui couvre la branche sans abonnements.

## 3) Commentaire / documentation à corriger
- **Constat**: `CHANGELOG.md` n'est pas trié chronologiquement (ex: entrée `2015-01-02` placée avant des entrées de `2014-12-xx`).
- **Risque**: lecture ambiguë de l'historique et maintenance documentaire plus difficile.
- **Tâche proposée**:
  1. Réordonner les entrées du changelog (ordre décroissant conseillé).
  2. Ajouter une courte convention en tête de fichier (format de date + ordre attendu).
  3. Vérifier la cohérence des futures entrées dans la CI (lint markdown simple ou checklist PR).

## 4) Amélioration d'un test (fiabilité)
- **Constat**: Les tests instrumentés (`FirstTestClass`/`TestMethods`) utilisent de nombreux `Thread.sleep(...)` et dépendent d'URLs externes (`https://gonative-test-web.web.app/`), ce qui les rend fragiles et lents.
- **Risque**: flakiness CI (faux négatifs), temps d'exécution élevé, dépendance réseau non maîtrisée.
- **Tâche proposée**:
  1. Remplacer les attentes fixes par des mécanismes synchronisés (IdlingResource/condition polling borné).
  2. Éviter les dépendances réseau externes via serveur mock/local (MockWebServer).
  3. Scinder les assertions UI et navigation en cas de panne pour diagnostics plus précis.
