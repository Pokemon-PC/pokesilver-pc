# Pokémon Or / Argent — port PC natif par recompilation statique

Même méthode et mêmes outils que le port de Cristal
(`../pokecrystal/pc/PORTING_ROADMAP.md`). Rien n'est modifié hors de `pc/`
(sauf `/build/` et `/dist/` dans `.gitignore`) ; `make compare` reste OK.

Un exécutable par version : `pokegold.exe`, `pokesilver.exe`. MBC3 + horloge
(comme Cristal), mode couleur.

## Jalons

| Jalon | Contenu | État |
|---|---|---|
| Outils | ROM Or et Argent (USA) reconstruites identiques (sha1 de `roms.sha1`) | ✅ |
| Traducteur | Or 99 912 / Argent 99 871 instructions, 32 banques | ✅ |
| Compilation | Windows | ✅ |
| Démarrage | intro, titre « Gold Version » (nuages en effet de trame), nouvelle partie | ✅ (ROM reconstruite) |
| Contrôle | `GB_DIFF=1` : 417 743 blocs, 0 écart | ✅ |
| ROM du joueur | **l'utilisateur n'a pas encore de ROM Or/Argent** : le jeu se lance avec la sienne (US = identique, FR = `match_roots.py`) | — |
| Jeu | Or : chambre, maman (jour de la semaine), labo d'Orme, Héricendre, Route 29, combat sauvage gagné, sauvegarde, CONTINUE au même endroit ; Argent : jusqu'à la chambre | ✅ (ROM reconstruite) |
| Français | ROM Or/Argent (France) du joueur traduites directement (11 318 fonctions chacune) ; exécutables anglais + français. Or FR : maman, labo, Héricendre, « Un ROUCOOL sauvage apparaît ! » ✅ ; Argent FR : jusqu'à la chambre ✅ | ✅ |
| Paquet, Linux | comme Cristal | — |

## Commandes

```
PATH=/d/LUC/pokemonall/tools/rgbds-1.0.4:$PATH mingw32-make -j8 gold silver
python pc/tools/recomp/recomp.py --rom pokegold.gbc --sym pokegold.sym --src . \
       --out build/rc_gold --name gold --prefix gold --lang en
cmake -S pc -B build/pc_gold -G Ninja -DCMAKE_C_COMPILER=clang -DRC_DIRS=build/rc_gold \
      -DGAME_NAME=pokegold -DSDL2_PREBUILT_DIR=<SDL2 statique>
cmake --build build/pc_gold
```
(idem `silver`.)

## Journal

- 2026-11-25 : parcours scripté d'Or jusqu'à la sauvegarde/rechargement ; Argent jusqu'à la chambre.
- 2026-11-25 : ROM françaises Or/Argent fournies par l'utilisateur ; versions françaises ajoutées.
- 2026-11-25 (suite) : les ROM de l'utilisateur sont toutes dans un seul dossier
  (`D:/LUC/pokemonall/roms`, à plat). Le jeu les trouve seul : chemin explicite
  (argument, `rom_en=`/`rom_fr=`), sinon dossier du programme, sinon `rom_dir`
  du `.ini` (`--rom-dir`, `GB_ROM_DIR`) ; le fichier est choisi à son contenu
  (CRC de la ROM entière = version + langue), jamais à son nom. Testé : 10 cas
  EN/FR trouvés depuis `rom_dir` seul. Paquets Windows (.zip) et Linux
  (.tar.gz, glibc >= 2.34 grâce à `pc/cmake/glibc_compat.h` et
  `-DGB_PORTABLE_GLIBC=ON`) faits avec `pc/tools/package.py`.
