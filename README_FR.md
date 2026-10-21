# Urban Open World PSP — prototype 0.1

Ce projet est un point de départ original pour un jeu homebrew PSP, jouable avec PPSSPP après compilation. Il ne contient aucun fichier de GTA V.

## Contrôles prévus dans ce prototype
- Flèches directionnelles : déplacer la position de démonstration.
- Triangle : changer entre 3 cadrages de caméra de démonstration.
- Rond : avancer le compteur de mission de démonstration.
- Croix : modifier le niveau de recherche de démonstration.

Important : dans cette version, la scène est une maquette 2D stylisée dessinée avec les primitives du GPU PSP, et non encore un monde 3D complet. Le compteur de mission et le niveau de recherche sont des démonstrations, pas des systèmes finalisés. Les prochaines étapes seront la vraie caméra 3D, le personnage animé, la conduite, les sauvegardes et les missions.

## Compilation
Il faut un environnement avec le PSPSDK installé. Sur iPhone, le moyen réaliste est d'utiliser un service de compilation à distance, par exemple un dépôt Git avec une action CI configurée pour PSPSDK. L'iPhone seul ne peut pas directement produire EBOOT.PBP avec ce projet sans environnement de compilation.

Quand compilé, le fichier `EBOOT.PBP` se place normalement dans :
`/PSP/GAME/UrbanOpenWorld/EBOOT.PBP`

Puis ouvrir PPSSPP et lancer le jeu depuis Games.

## Limites
- Prototype de départ, non compilé ni testé dans PPSSPP dans cette livraison.
- Les ressources graphiques sont provisoires et dessinées par code.
- Les personnages, marques, cartes et missions de GTA V ne sont pas inclus.
- Ce projet ne promet pas le niveau graphique de GTA V : la PSP a des limites matérielles importantes.
