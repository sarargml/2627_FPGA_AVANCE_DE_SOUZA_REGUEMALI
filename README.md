# 2627_FPGA_AVANCE_DE_SOUZA_REGUEMALI

# TP FPGA avancé — Nios V et écran magique

Ce dépôt rassemble le tutoriel Nios V et le projet d’écran magique réalisés
dans le cadre du TP de FPGA avancé.

Le tutoriel consiste à intégrer un processeur Nios V/m dans un FPGA
Cyclone V, puis à exécuter deux programmes C pour tester la communication
avec le PC et piloter les LED de la carte.

Le projet reprend cette architecture et y ajoute l’accéléromètre ADXL345,
accessible par I²C. Le capteur sert d’abord à réaliser un niveau à bulles
avec les LED, puis à détecter le retournement de la carte pour effacer
le dessin de l’écran magique.

## Rapports

**Chaque dossier contient son propre `README.md`, qui sert de rapport.**
Les rapports présentent les étapes de réalisation, les choix techniques
et les essais sur carte.

- [Rapport du tutoriel Nios V](tuto/README_TUTO.md)
- [Rapport du projet d’écran magique](projet/README_PROJET.md)

## Structure du dépôt

| Chemin | Contenu |
| --- | --- |
| `README.md` | Présentation générale du TP et accès aux deux rapports |
| `tuto/README_TUTO.md` | Rapport du tutoriel Nios V, du premier `Hello, world!` au chenillard |
| `tuto/rtl/` | Sources VHDL du tutoriel |
| `tuto/synt/` | Projet Quartus et affectations de broches |
| `tuto/sopc/` | Configuration du système Nios V dans Platform Designer |
| `tuto/soft/` | Application C du tutoriel |
| `tuto/images/` | Captures et photos du tutoriel |
| `projet/` | Sources et illustrations du niveau à bulles et de l’écran magique |
| `projet/README_PROJET.md` | Rapport du projet, de la lecture de l’accéléromètre à l’effacement de l’écran |

## Outils

- **Quartus 24.1** pour la synthèse et la programmation du FPGA Cyclone V.
- **Platform Designer** pour configurer le processeur Nios V/m, sa mémoire
  et ses périphériques.
- **Ashling RiscFree** pour compiler, charger et déboguer les applications C.

[Énoncé du TP FPGA avancé](https://github.com/lfiack/ENSEA_2A_FPGA_Public/blob/main/majeure/3-tp/fpga_adv_tp.md)
