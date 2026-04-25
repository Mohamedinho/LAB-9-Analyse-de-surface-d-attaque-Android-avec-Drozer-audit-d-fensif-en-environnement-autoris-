# Analyse du manifeste Android

Package analysé :
jakhar.aseem.diva

Permissions déclarées :
- android.permission.WRITE_EXTERNAL_STORAGE
- android.permission.READ_EXTERNAL_STORAGE
- android.permission.INTERNET

Configurations observées :
- debuggable="true"
- allowBackup="true"

Composants exposés :
- MainActivity
- APICredsActivity
- APICreds2Activity
- NotesProvider

Faiblesses identifiées :
- Mode debug activé.
- Backup autorisé.
- Activités exposées sans permission.
- Content Provider exporté sans permission visible.