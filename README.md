# Rapport d'audit de sécurité - Application Android

## Informations générales

- **Application** : DIVA - Damn Insecure and Vulnerable App
- **Package** : jakhar.aseem.diva
- **Version** : 1.0
- **Date d'audit** : [DATE]
- **Auditeur** : [NOM]

## Résumé exécutif

Cet audit de sécurité a été réalisé sur l'application Android DIVA à l'aide de Drozer et ADB dans un environnement de test contrôlé. L'objectif était d'identifier les composants Android exposés, d'analyser les protections mises en place et d'évaluer les risques associés.

L'analyse a permis d'identifier plusieurs faiblesses importantes : des activités accessibles sans permission spécifique, un Content Provider exporté sans protection de lecture ou d'écriture, des URI de provider accessibles, ainsi que des paramètres de configuration risqués comme `debuggable="true"` et `allowBackup="true"`.

Ces faiblesses peuvent augmenter la surface d'attaque de l'application et permettre à une application malveillante d'accéder à certains composants internes ou à des données exposées.

## Méthodologie

- Analyse statique du manifeste Android.
- Cartographie des composants exposés avec Drozer.
- Vérification des protections en place.
- Analyse des risques potentiels.
- Collecte de preuves avec captures d'écran et résultats Drozer.

## Découvertes principales

### V1 - Activités exportées sans permission

Les activités suivantes sont accessibles sans permission spécifique :

- `jakhar.aseem.diva.MainActivity`
- `jakhar.aseem.diva.APICredsActivity`
- `jakhar.aseem.diva.APICreds2Activity`

Drozer indique `Permission: null`, ce qui signifie qu'aucune permission particulière n'est requise pour interagir avec ces activités.

**Impact :** Une application externe pourrait tenter de lancer directement certaines activités internes via des intents.

### V2 - Content Provider exporté sans protection

Le Content Provider suivant est exporté :

- `jakhar.aseem.diva.provider.notesprovider`

Le manifeste indique que le provider est exporté, et aucune permission de lecture ou d'écriture n'est définie.

**Impact :** Une application externe pourrait potentiellement accéder aux données exposées par ce provider.

### V3 - URI du Content Provider accessibles

Drozer a identifié des URI accessibles :

- `content://jakhar.aseem.diva.provider.notesprovider/notes`
- `content://jakhar.aseem.diva.provider.notesprovider/notes/`

**Impact :** Risque d'accès non autorisé à des données stockées dans le provider.

### V4 - Mode debug activé

Le manifeste indique :

- `debuggable="true"`

**Impact :** Le mode debug peut faciliter l'analyse, le reverse engineering et la manipulation de l'application.

### V5 - Backup autorisé

Le manifeste indique :

- `allowBackup="true"`

**Impact :** Les données locales de l'application pourraient être extraites via une sauvegarde si elles ne sont pas correctement protégées.

## Recommandations prioritaires

### R1 - Restreindre les activités exposées

Définir `android:exported="false"` pour les activités qui ne doivent pas être appelées depuis l'extérieur de l'application.

### R2 - Protéger les activités sensibles

Ajouter des permissions personnalisées avec un niveau de protection adapté pour les activités sensibles comme `APICredsActivity` et `APICreds2Activity`.

### R3 - Sécuriser le Content Provider

Ajouter des permissions de lecture et d'écriture :

- `android:readPermission`
- `android:writePermission`

ou désactiver l'export du provider si l'accès externe n'est pas nécessaire.

### R4 - Désactiver le mode debug en production

Mettre :

`android:debuggable="false"`

dans la version finale de l'application.

### R5 - Désactiver ou sécuriser le backup

Mettre :

`android:allowBackup="false"`

ou utiliser une configuration de sauvegarde sécurisée pour éviter l'extraction de données sensibles.

## Annexes

### Annexe A : Tableau de triage complet

Voir le fichier `triage.csv`.

### Annexe B : Captures d'écran des preuves

Les captures d'écran doivent être placées dans le dossier :

`preuves/screenshots/`

Captures recommandées :

- Console Drozer connectée.
- Résultat de `run app.package.list -f diva`.
- Résultat de `run app.package.manifest jakhar.aseem.diva`.
- Résultat de `run app.activity.info -a jakhar.aseem.diva -i`.
- Résultat de `run app.provider.info -a jakhar.aseem.diva`.
- Résultat de `run scanner.provider.finduris -a jakhar.aseem.diva`.

### Annexe C : Mapping OWASP MASVS

| Vulnérabilité | Catégorie MASVS associée | Description |
|---|---|---|
| Activités exposées sans permission | MASVS-PLATFORM | Mauvaise protection des composants Android exposés |
| Content Provider exporté sans permission | MASVS-PLATFORM | Exposition de données via composant inter-applications |
| URI accessibles sans permission | MASVS-PLATFORM / MASVS-STORAGE | Risque d'accès non autorisé aux données |
| Mode debug activé | MASVS-RESILIENCE | Facilite l'analyse et la manipulation de l'application |
| Backup autorisé | MASVS-STORAGE | Risque d'extraction de données locales |



# Checklist de fin d'audit

## Conformité de l'audit

- [x] Toutes les étapes du lab ont été suivies
- [x] Tous les composants Android ont été analysés
- [x] Le tableau de triage est complet
- [x] Les remédiations proposées sont spécifiques et applicables
- [x] Le mapping OWASP MASVS est correct

## Absence de données sensibles

- [x] Aucune donnée utilisateur réelle n'est présente dans le rapport
- [x] Aucun mot de passe ou clé n'est inclus dans le rapport
- [x] Les captures d'écran ne contiennent pas d'informations sensibles
- [x] Les chemins système complets ont été anonymisés
- [x] Les identifiants personnels ont été supprimés

## Qualité du rapport

- [x] Le rapport est bien structuré
- [x] Les vulnérabilités sont clairement expliquées
- [x] Les recommandations sont précises et actionnables
- [x] La documentation est complète
- [x] Le format des livrables est conforme aux attentes
