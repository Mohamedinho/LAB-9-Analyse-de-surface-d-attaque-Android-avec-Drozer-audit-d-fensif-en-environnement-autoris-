# Risques liés au Content Provider

Le Content Provider est exporté et ne possède pas de permission de lecture ou d’écriture.

Risque :
- Accès non autorisé aux données du provider.
- Possibilité de lecture ou modification si les URI sont exploitables.

Impact :
- Fuite de données.
- Manipulation non autorisée des données.