# Harvest World — Manuel du joueur (Membre 1 : Gameplay & Joueur)

## Ce qui a été fait

- Scène `Player` (`res://scenes/player/Player.tscn`) avec :
  - `CharacterBody2D` (le personnage)
  - `AnimatedSprite2D` (les animations idle / run / jump)
  - `CollisionShape2D` (la zone de collision physique)
  - `Camera2D` (suit le joueur avec un lissage de mouvement)
- Script `res://scenes/player/player.gd` qui gère :
  - Le déplacement gauche/droite
  - Le saut
  - La gravité
  - Le changement automatique d'animation selon l'état (idle/run/jump)
  - Le retournement du sprite selon la direction (flip_h)

## Points sensibles à connaître absolument

### 1. Les sprites sont des "sprite sheets" (planches), pas des images uniques
Chaque fichier dans `assets/player/idle/`, `run/`, `jump/` contient **plusieurs personnages en grille** sur une seule image. Il a fallu utiliser l'outil **"Add frames from Sprite Sheet"** dans le panneau SpriteFrames de Godot (PAS l'outil "ajouter depuis un fichier" classique) pour découper chaque image en frames individuelles.

➡️ Si vous ajoutez de nouveaux sprites (ennemis, items...), vérifiez d'abord s'ils sont aussi en planche, et utilisez le même outil de découpage.

### 2. Le Scale des nœuds peut s'écraser accidentellement
En manipulant les poignées dans l'éditeur 2D, il est facile de redimensionner un nœud par erreur (Scale qui passe à des valeurs comme 0.16 au lieu de 1.0). Si un objet semble visuellement déformé ou que les collisions ne correspondent pas à ce qu'on voit, **vérifiez en premier le Scale dans Transform** (doit être 1.0 / 1.0 sauf volonté contraire).

### 3. Input Map : les actions doivent avoir des touches assignées
Dans **Projet → Paramètres du projet → Input Map**, les actions suivantes existent et sont utilisées par le script :
- `move_left`
- `move_right`
- `jump`

Si vous créez une nouvelle action, n'oubliez pas de lui assigner une touche réelle (sinon elle existe mais ne réagit à rien).

### 5. Touches de contrôle du jeu
- **Flèche gauche** ou **L** → diriger le joueur à gauche
- **Flèche droite** ou **R** → diriger le joueur à droite
- **Ctrl** ou **X** → faire sauter le joueur

Ces touches doivent être assignées aux actions `move_left`, `move_right` et `jump` dans l'Input Map (chaque action peut avoir plusieurs touches associées).

### 4. Layers / Mask de collision
Le `Player` (CharacterBody2D) et tout objet solide (sol, plateforme...) doivent partager au moins un calque de collision en commun (`Layer`/`Mask`), sinon ils se traversent sans interagir. Actuellement tout est sur le calque `1` par défaut.

## Comment exploiter ce travail (pour les autres membres)

### Pour les niveaux (Membre 2)
- Réutilisez `Player.tscn` en le glissant dans vos scènes de niveau, ne le recréez pas
- Vos plateformes/sol doivent avoir un `CollisionShape2D` sur le calque `1` (ou ajustez le `Mask` du Player si vous changez de calque)
- La `Camera2D` est déjà incluse dans `Player.tscn` — pas besoin d'en ajouter une dans vos niveaux

### Pour les ennemis (Membre 3)
- Le `Player` a un `velocity` géré par `move_and_slide()` — pour les dégâts, on utilisera probablement une `Area2D` détectant le `body_entered` avec le groupe ou la classe du Player
- Les sprites d'ennemis sont probablement aussi des sprite sheets : utilisez la même méthode de découpage (voir point 1)

### Pour le score/items/HUD (Membre 4)
- Le nœud `Player` peut être étendu avec des signaux (ex: `signal item_collected`) si besoin de communiquer avec le HUD — à voir ensemble selon votre architecture finale (singleton/autoload recommandé pour le score global)

## Structure des dossiers

```
res://
├── assets/
│   ├── player/ (idle/, run/, jump/)
│   ├── enemies/
│   ├── environment/
│   └── items/
├── scenes/
│   ├── player/ (Player.tscn, player.gd)
│   ├── levels/
│   ├── enemies/
│   └── ui/
├── scripts/
└── resources/
```

## Workflow Git du groupe

- Branche `main` : version stable uniquement
- Branche `game` : intégration du travail de tous, avant fusion finale
- Chacun travaille sur sa propre branche (ex: `game-niveaux`, `game-ennemis`) créée à partir de `game`
- Pull Request vers `game` pour relecture avant fusion
- Fusion vers `main` seulement une fois tous les conflits réglés et le jeu testé ensemble

### Pour rejoindre le projet (chaque membre, sur sa machine)

```bash
git clone https://github.com/TONPSEUDO/HarvestWorld.git
cd HarvestWorld
git checkout game
```

Vous pouvez alors créer votre propre branche à partir de `game` (ex: `git checkout -b game-niveaux`) pour éviter de tout casser en travaillant en même temps, puis faire une **Pull Request** vers `game` une fois votre partie prête.
