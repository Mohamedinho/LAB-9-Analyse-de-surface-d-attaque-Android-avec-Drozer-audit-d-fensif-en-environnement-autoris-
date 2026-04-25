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
#### Figure 1 : Agent Drozer activé

<img width="377" height="828" alt="01_drozer_agent_on" src="https://github.com/user-attachments/assets/cf6c6392-3a5e-4768-9d01-901462630a22" />

#### Figure 2 : Connexion à la console Drozer

<img width="769" height="391" alt="02_drozer_console_connect" src="https://github.com/user-attachments/assets/0398761e-24c0-47ee-bfcd-eee9cf259a02" />

### Figure 3 : Identification du package DIVA

<img width="1094" height="68" alt="03_package_list_diva" src="https://github.com/user-attachments/assets/42a7f155-2e32-4d1e-b30c-928af4bc9ddc" />

### Figure 4 : Informations générales de l’application

<img width="1095" height="381" alt="04_package_info_diva" src="https://github.com/user-attachments/assets/33c609d5-00ca-46f3-9aba-3e141337f593" />

### Figure 5 : Activités exportées

<img width="1092" height="193" alt="05_activity_info" src="https://github.com/user-attachments/assets/b166cec5-9941-4430-9ff3-d2d1b6acc206" />

### Figure 6 : Services exportés

<img width="1109" height="146" alt="06_service_info" src="https://github.com/user-attachments/assets/8696b3a6-a818-4521-be0b-75e2c66182aa" />

### Figure 7 : Broadcast receivers

<img width="436" height="86" alt="07_broadcast_info" src="https://github.com/user-attachments/assets/9131e38b-c2a5-4c33-b169-28795bdc1596" />

### Figure 8 : Content Provider exposé

<img width="570" height="220" alt="08_provider_info" src="https://github.com/user-attachments/assets/c2ebbb67-c0b1-462c-95e3-6da410a67cd7" />

### Figure 9 : Analyse du manifeste Android

<img width="708" height="723" alt="09_manifest_analysis" src="https://github.com/user-attachments/assets/8edf1182-6993-4608-b28c-fea1e20b0b6c" />

### Figure 10 : URI accessibles du Content Provider

<img width="850" height="240" alt="10_provider_uris" src="https://github.com/user-attachments/assets/b6292a70-3891-490b-969a-4432c0e7318d" />

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
