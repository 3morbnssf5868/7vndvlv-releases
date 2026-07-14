# Market Screener — Releases

Ce dépôt ne contient **aucun code source**. Il ne sert qu'à héberger les
installeurs de Market Screener et le manifeste `latest.json` que l'application
interroge pour se mettre à jour.

Le code source est privé.

## Installation

Télécharge le `-setup.exe` de la [dernière release](../../releases/latest) et lance-le.
Les mises à jour suivantes se font automatiquement depuis l'application.

## Intégrité

Chaque installeur est signé. L'application vérifie la signature avec la clé
publique qu'elle embarque, et refuse toute mise à jour qui ne correspond pas.
