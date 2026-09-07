# 2026-2027 - TP Bachelor 3 : Programmation embarquée
## Acquis d'Apprentissage visés

Ce TP a pour objectif d'introduire aux étudiants les bases de la robotique embarquée en utilisant une Raspberry Pi comme plateforme de contrôle. A travers les différentes étapes, configuration du système, lecture des capteurs, traitement des données et pilotage d'un moteur, les étudiants découvrent concrètement comment un robot perçoit, interprète et réagit à son environnement.

Les manipulations réalisées permettent de comprendre des notions essentielles : communication I2C/SPI, interprétation de valeurs brutes, conversion vers des grandeurs physiques, gestion d'un capteur multi-axes et intégration d'un comportement autonome comme le suiveur de ligne.

A l'issue de ce TP, les étudiants sont capables d'interfacer du matériel réel, d'exploiter des données capteurs et de programmer des actions correspondant à un comportement robotique simple. 

## Introduction
Une RPI (Raspberry Pi) est un petit ordinateur conçu pour l’apprentissage de la programmation et des bases informatiques. Elle dispose d’un processeur, d’une mémoire vive (RAM), d’un espace de stockage (via une carte micro SD), ainsi que de ports USB, HDMI et d’une connectivité réseau sans fil (wifi).  

La Raspberry Pi peut interagir avec des capteurs, des moteurs ou d’autres composants grâce aux broches GPIO (General Purpose Input/output). Cela en fait d’elle un outil très pratique et adapté pour les projets IoT (Internet of Things) et les systèmes embarqués. 

Pour pouvoir établir une connexion avec la RPI, on utilise le connexion SSH (Secure Shell), c’est un protocole de communication qui permet de se connecter à un autre ordinateur via un réseau IP. Il nous fournit une interface en ligne de commande à travers laquelle on peut exécuter des instructions, transférer des fichiers ou administrer un système, comme si on travaillait dessus directement. Donc nous l’utiliserons pour pouvoir se connecter à notre Raspberry via notre ordinateur. 

Pour cela, nous allons utiliser l’extension Remote-ssh de VSCode qui nous permettra d’établir cette connexion et ouvrira, donc, directement un espace de développement sur la Raspberry. Le code python qui sera écrit dans cet espace sera automatiquement enregistré et exécuté sur celle-ci tout en profitant de l'ergonomie d'un PC complet avec écran, souris et clavier.

## Parcours des TP

1. [TP1 - Installation et configuration de la Raspberry Pi](tp1_install_rpi.md)
2. [TP2 - Bibliothèques Python et interfaces matérielles](tp2_librairies_python.md)
3. [TP3 - Accéléromètre LSM6DSOX](tp3_accelerometre_lsm6dsox.md)
4. [TP4 - Suiveur de ligne](tp4_line_follower.md)
5. [TP5 - Moteur pas à pas](tp5_stepper.md)

## Organisation des consignes

Les activités sont balisées dans tous les TP avec les mêmes icônes :

* **📖 Lecture** : lire une explication ou un document et relever les informations importantes ;
* **🛠️ Manipulation / code** : réaliser une configuration, écrire du code ou exécuter des commandes ;
* **💭 Réflexion** : répondre aux questions et justifier les choix effectués.

Les réponses aux activités **💭 Réflexion** doivent être ajoutées au compte rendu. Les commandes et extraits de code des activités **🛠️ Manipulation / code** doivent être testés sur la Raspberry Pi lorsque le matériel est disponible.

### Objectifs

A la fin de ce TP, les étudiants doivent être capables de :
* Configurer une Raspberry Pi. 
* Comprendre et utiliser les bus de communication. 
* Lire et interpréter des capteurs.
* Analyser un code python existant.
* Obtenir des résultats concrets.

### Livrables

Les étudiants doivent fournir comme résultats :
* Accéléromètre et gyroscope :
  * Lecture correcte des valeurs en brut.
  * Conversion en unité physiques (g et dps).
  * Calculer les angles d'inclinaison X et Y.
* Driver moteur :
  * Initialisation correcte du driver moteur.
  * Utilisation des fonctions fournies (set_speed(), set_direction(), rotate(), cleanup()).
  * Démonstration qu'il peut avancer en ligne droite, ajuster la vitesse des moteurs en fonction de la position de la ligne, s'arrêter proprement.
  * Compréhension du rôle de la PWM et de la différence de vitesse entre moteur gauche/droite.
* Capteurs IR + MCP3208 :
  * Lecture des 8 valeurs de tension.
  * Détection fonctionelle de la position de la ligne.
  * Compréhension et justification de l'usage de seuil de détection.
* Compréhension des programmes : 
  * La structure et le rôle du fichier setting.py.
  * Comment communiquent SPI et i2C.
  * Comment les différents modules interagissement dans le code principale (main.py).
* Dépôt GitHub :
  * Déposer l'ensemble du code sur GitHub, dans un dépôt nommé `2627_B3_ProgEmb_<nom1>_<nom2>`.
  * Partager ce dépôt avec l'enseignant (nicolas.papazoglou@ensea.fr).

Rappel des commandes git de base pour versionner et partager votre code :

```bash
git add .
git commit -m "message décrivant les changements"
git pull
git push
```