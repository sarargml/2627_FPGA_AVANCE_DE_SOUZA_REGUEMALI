# TP FPGA avancé — Tutoriel Nios V

**Auteurs** — Sara REGUEMALI, Luis Henrique DE SOUZA

**FPGA cible** — Intel Cyclone V (`5CSEBA6U23I7`)  

**Outils** — Quartus 24.1, Platform Designer et Ashling RiscFree

[Énoncé du TP FPGA avancé](https://github.com/lfiack/ENSEA_2A_FPGA_Public/blob/main/majeure/3-tp/fpga_adv_tp.md)

## Objectif

L’objectif de ce tutoriel est d’intégrer un processeur Nios V/m dans un FPGA et d’exécuter un programme C sur ce processeur. Le Nios V/m est un soft-processeur RISC-V, c’est-à-dire un processeur réalisé à partir des
ressources logiques programmables du FPGA.

Le système associe le processeur à une mémoire interne, à une interface JTAG UART et à un port de sortie parallèle relié aux dix LED de la carte. Deux essais permettent de vérifier son fonctionnement. Le premier affiche `Hello, world!` dans un terminal. Le second commande les LED pour former un chenillard.

## 1. Création du projet Quartus

Le projet `tp_nios_v` cible le FPGA Cyclone V `5CSEBA6U23I7`. Le fichier `tp_nios_v.qpf` identifie le projet et sa révision, tandis que `tp_nios_v.qsf` définit le composant cible, les fichiers sources, l’entité de niveau supérieur et les affectations de broches.

L’entité VHDL `tp_nios_v` relie le système aux entrées et sorties physiques de la carte. Elle reçoit l’horloge `i_clk` et le signal de reset actif à l’état bas `i_rst_n`. Le vecteur `o_led(9 downto 0)` commande les dix LED. Les broches correspondantes sont définies dans le fichier `.qsf`.

| Dossier | Contenu |
| --- | --- |
| `rtl/` | Sources VHDL, dont l’entité de niveau supérieur |
| `synt/` | Fichiers du projet Quartus et affectations de broches |
| `sopc/` | Configuration du système Nios V dans Platform Designer |
| `soft/` | Application C et configuration du BSP |
| `images/` | Captures et photos utilisées dans ce compte rendu |


## 2. Architecture du système dans Platform Designer

Le système `nios.qsys` rassemble les blocs nécessaires à l’exécution du programme et à la commande des LED.

| Bloc | Rôle |
| --- | --- |
| Nios V/m | Exécute les instructions du programme C |
| Mémoire On-Chip de 128 Kio | Stocke les instructions et les données de l’application |
| JTAG UART | Transmet les messages du programme au terminal du PC |
| PIO de 10 bits | Met à disposition dix sorties pilotables par le programme C |

L’interface d’instructions du processeur est reliée à la mémoire. Son interface de données permet d’accéder à cette mémoire et aux
périphériques. Une adresse de base est attribuée à chaque périphérique afin que le logiciel puisse y accéder.

La mémoire interne est choisie comme cible du vecteur de reset. Le processeur peut ainsi y récupérer sa première instruction au démarrage. La sortie du PIO est exportée pour être raccordée aux LED dans le VHDL.

<img width="608" height="650" alt="image" src="https://github.com/user-attachments/assets/773aab7e-c065-48f8-9d30-f9fc07541764" />

**Figure 1 — Le processeur Nios V/m accède à la mémoire interne et aux
périphériques. La sortie `external_connection` du PIO est exportée pour
être reliée aux dix LED dans le module VHDL de niveau supérieur.**

## 3. Intégration du système dans le VHDL

Une fois le système configuré, Platform Designer génère les fichiers HDL. Le fichier `nios.qip` référence les fichiers nécessaires à la synthèse du système dans Quartus.

Le bloc généré est instancié dans `tp_nios_v.vhd`. Son horloge et son reset sont raccordés à `i_clk` et `i_rst_n`. La sortie exportée du PIO est reliée à `o_led`. Une écriture dans le registre de données du PIO peut ainsi changer l’état des LED physiques.

La compilation Quartus produit le fichier de configuration utilisé pour programmer le FPGA. Cette étape met en place le processeur et ses périphériques sur la carte. L’application C est chargée ensuite.

**<!-- Ajouter une capture du résultat de compilation ou de la programmation
dans Quartus. Indiquer sous la capture le résultat réellement observé.
![Programmation du FPGA dans Quartus](images/programmation-quartus.png)
-->**


## 4. Création et chargement de l’application C

Le **BSP** (*Board Support Package*) est généré à partir de la description matérielle du système. Il fournit à l’application C les pilotes et les constantes correspondant aux périphériques présents dans le FPGA.
Par exemple, `PIO_0_BASE` désigne l’adresse de base du PIO.

Le BSP et l’application sont importés dans RiscFree. La compilation produit le fichier `app.elf`, qui est chargé sur le système Nios V
à l’aide du débogueur matériel RISC-V.

### Premier essai — `Hello, world!`

Le premier programme appelle `printf` pour envoyer le message `Hello, world!`. Après le lancement du programme et la déconnexion du
débogueur, `juart-terminal` permet de lire la sortie du JTAG UART.

L’affichage du message permet de vérifier que le processeur a exécuté l’application et que la communication avec le PC fonctionne.

**<!-- Ajouter la capture du terminal. Sous l’image, noter le message
réellement affiché et, si nécessaire, la manipulation qui a permis de le voir.
![Message reçu dans juart-terminal](images/hello-world.png)
-->**

## 5. Commande des LED — Chenillard

Le chenillard consiste à déplacer une LED allumée le long de la rangée.
Pour cela, le programme écrit successivement des mots de dix bits dans
le registre du PIO. Chaque mot contient un seul bit à `1`. En décalant
ce bit à chaque itération, le programme change la LED sélectionnée.

L’écriture se fait avec `IOWR_ALTERA_AVALON_PIO_DATA`, en utilisant
`PIO_0_BASE` comme adresse de base. Un appel à `usleep` introduit
une temporisation entre deux écritures pour rendre le déplacement visible.

**<!-- Ajouter deux photos successives, un GIF ou un lien vidéo montrant
le déplacement. Indiquer les LED de départ et d’arrivée, le délai choisi
et ce qui se passe en fin de séquence d’après votre code et votre essai.
![Démonstration du chenillard](images/chenillard.gif)
-->**

## Conclusion

Le tutoriel illustre la chaîne complète, de la configuration d’un processeur dans le FPGA jusqu’à l’exécution d’un programme C. L’essai
`Hello, world!` vérifie la communication avec le PC. Le chenillard vérifie que le logiciel peut commander des sorties physiques en écrivant dans un périphérique du système.

Cette architecture sert de point de départ pour le projet suivant, dans lequel le Nios V devra notamment communiquer avec l’accéléromètre ADXL345 via une interface I²C.

[Accéder au rapport du projet](../projet/README.md)
