# 2026-2027 - TP Bachelor 3 : Programmation embarquée
## Acquis d'Apprentissage visés

Ce TP a pour objectif d'introduire aux étudiants les bases de la robotique embarquée en utilisant une Raspberry Pi comme plateforme de contrôle. A travers les différentes étapes, configuration du système, lecture des capteurs, traitement des données et pilotage d'un moteur, les étudiants découvrent concrètement comment un robot perçoit, interprète et réagit à son environnement.

Les manipulations réalisées permettent de comprendre des notions essentielles : communication I2C/SPI, interprétation de valeurs brutes, conversion vers des grandeurs physiques, gestion d'un capteur multi-axes et intégration d'un comportement autonome comme le suiveur de ligne.

A l'issue de ce TP, les étudiants sont capables d'interfacer du matériel réel, d'exploiter des données capteurs et de programmer des actions correspondant à un comportement robotique simple. 

## Introduction
Une RPI (Raspberry Pi) est un petit ordinateur conçu pour l’apprentissage de la programmation et des bases informatiques. Elle dispose d’un processeur, d’une mémoire vive (RAM = Random Access Memory), d’un espace de stockage (via une carte micro SD), ainsi que de ports USB, HDMI et d’une connectivité réseau sans fil (wifi).  

La Raspberry Pi peut interagir avec des capteurs, des moteurs ou d’autres composants grâce aux broches GPIO (General Purpose Input/output). Cela en fait d’elle un outil très pratique et adapté pour les projets IoT (Internet of Things) et les systèmes embarqués. 

Pour pouvoir établir une connexion avec la RPI, on utilise le connexion SSH (Secure Shell), c’est un protocole de communication qui permet de se connecter à un autre ordinateur via un réseau IP. Il nous fournit une interface en ligne de commande à travers laquelle on peut exécuter des instructions, transférer des fichiers ou administrer un système, comme si on travaillait dessus directement. Donc nous l’utiliserons pour pouvoir se connecter à notre Raspberry via notre ordinateur. 

Pour cela, nous allons utiliser l’extension Remote-ssh de VSCode qui nous permettra d’établir cette connexion et ouvrira, donc, directement un espace de développement sur la Raspberry. Le code python qui sera écrit dans cet espace sera automatiquement enregistré et exécuté sur celle-ci tout en profitant de l'ergonomie d'un PC complet avec écran, souris et clavier.

### Objectifs

A la fin de ce TP, les étudiants doivent être capables de :
* Configurer une Raspberry Pi. 
* Comprendre et utiliser les bus de communication. 
* Lire et interpréter des capteurs.
* Analyser un code python existant.
* Obtenir des résultats concrets.

Configurer une Raspberry Pi Zero 2W en utilisant la liaison UART. 

* Une Raspberry Pi Zero possède un port 40 pins appelés GPIO, donc nous utiliserons les pins 14 et 15 pour y connecter un bridge USB-UART pour communiquer avec notre pc en UART.

![](img/image_00.png)

Ecrire un driver proprement

* Un driver sert d'interface entre le logiciel et le matériel, traduit les instructions du programme (python par exemple) en signaux compréhensible par le capteur. Dans notre contexte le driver sera un programme dans le langage python.
* Développement d'un programme d'un suiveur de ligne sur VS code.
* Développement d'un programme de commande d'un moteur pas-à-pas (stepper)
* Développement d'un programme d'un accéléromètre.

### Livrables

Les étudiants doivent fournir comme résultats :
* Capteurs IR + MCP3208 :
  * Lecture des 8 valeurs de tension.
  * Détection fonctionelle de la position de la ligne.
  * Compréhension et justification de l'usage de seuil de détection.
* Driver moteur :
  * Initialisation correcte du driver moteur.
  * Utilisation des fonctions fournies (set_speed(), set_direction(), rotate(), cleanup()).
  * Démonstration qu'il peut avancer en ligne droite, ajuster la vitesse des moteurs en fonction de la position de la ligne, s'arrêter proprement.
  * Compréhension du rôle de la PWM et de la différence de vitesse entre moteur gauche/droite.
* Accéléromètre et gyroscope :
  * Lecture correcte des valeurs en brut.
  * Conversion en unité physiques (g et dps).
  * Calculer les angles d'inclinaison X et Y.
* Compréhension des programmes : 
  * La structure et le rôle du fichier setting.py.
  * Comment communiquent SPI et i2C.
  * Comment les différents modules interagissement dans le code principale (main.py).