## TP 1
### Objectif
* Configurer la Raspberry Pi Zero
* Un tutoriel pas a pas est mis en place pour vous aider à installer les fichiers de l'OS qui sont nécessaires au bon fonctionnement de la Raspberry Pi : 
  
   1. Flasher la carte SD 
   2. Activer la liaison UART
   3. Configurer la connexion 
   4. Se connecter en ssh via le Wifi
   5. Sécuriser la connexion ssh avec une clé 

#### Flasher la carte SD
Allez sur https://www.raspberrypi.com/software/ et installez Raspberry Pi Imager selon votre type de PC (Linux).

![](img/image_01.png)

Sélectionnez le bon modèle de RPI : Raspberry Pi Zero 2W

![](img/image_02.png)

Sélectionnez l'OS adapté à notre utilisation : Raspberry Pi (other) -> L'OS Lite (64-bit)

![](img/image_03.png)

![](img/image_04.png)

Sélectionnez le support d'installation de l'OS, sélectionnez la carte SD 

![](img/image_05.png)

Validez la configuration, la carte SD est prête à être flashée. Cliquez sur Next et choisissez "Edit settings" 

![](img/image_06.png)

Modifiez quelques paramètres :
* Le hostname -> Choisir la forme "nom de la salle-vos initiales-initiales du binôme"
* nom d'utilisateur et un mot de passe (le mot de passe sera nécessaire à chaque fois donc à retenir)
* Dans l'onglet "Services", activez "Enable SSH" et l'authentification par mot de passe (nécessaire pour se connecter en headless, sans écran ni clavier branchés sur la Raspberry)

![](img/image_07.png)

Validez les modifications apportées et les appliquer a l'installation. 
Validez la copie de l'image de l'OS Linux sur la carte SD.

![](img/image_08.png)

Le processus dure quelques minutes, la fichiers sont copiés et une vérifications de la copie a lieu.

![](img/image_09.png)

![](img/image_10.png)

une fois fini, éjectez la carte SD en toute sécurité puis insérez-la dans la Raspberry Pi.

#### Première connexion par UART
Assurez-vous que la Raspberry est bien allumée et connectée au réseau Wifi de la salle. 

Le réseau de la salle est géré par un routeur Mikrotik administré par le professeur : vous n'avez pas la main sur son interface d'administration, donc pas moyen d'aller y consulter la liste des baux DHCP vous-même. On utilise à la place le hostname mDNS que vous avez défini dans Raspberry Pi Imager, qui permet de joindre la Raspberry par son nom directement, sans connaître son adresse IP.

Ouvrez un terminal sur votre PC Linux et tapez "ping nom_de_votre_hostname.local" (remplacez "nom_de_votre_hostname" par le hostname choisi à l'étape de flash).

![](img/image_14.png)

si vous avez des réponses affichées à l'écran, ça veut dire que votre PC voit votre Raspberry sur le réseau donc la connexion réseau fonctionne. (pour l'arrêter, tapez : "ctrl + C")

Si le ping ne répond pas (résolution mDNS parfois indisponible selon le PC), deux solutions de secours :
* scannez le sous-réseau avec nmap : "nmap -sn 192.168.X.0/24" (remplacez X par le sous-réseau de la salle) et repérez l'IP dont le nom d'hôte correspond au hostname choisi.
* demandez au professeur l'adresse IP attribuée à votre Raspberry sur le Mikrotik.

Tapez "ssh utilisateur@nom_de_votre_hostname.local" (ou "ssh utilisateur@adresse_ip" si vous êtes passés par la solution de secours). Il vous demandera votre mot de passe (rien ne sera affiché pendant que vous écrirez, c'est normal).

![](img/image_16.png)

S'il y a bien écrit "Linux rpi-test-B3 ...
Last login: Wed Nov  5 ..." alors vous êtes bien connectés à votre Raspberry via SSH.

Allez sur VS code et cliquez sur le petit symbole en bleu en bas à gauche de l'écran.

![](img/image_17.png)

Cliquez sur "Connect to host"

![](img/image_18.png)

Mettez "votre nom d'utilisateur@nom_de_votre_hostname.local"

![](img/image_19.png)

Il vous demandera sur quelle plateforme vous êtes (Linux, Windows ou MacOS), vous mettez "Linux"

Entrez votre mot de passe.
Revenez sur votre terminal et marquez "mkdir 'nom de votre dossier'" ensuite "cd 'nom de votre dossier'". Le dossier est creer, puis vous tapez "ls" puis "cd..".

![](img/image_20.png)

Revenez sur VS code et ouvrez le dossier, créez, ensuite, 3 fichiers "main.py" "MCP3208.py" et "votre_composant.py" 

![](img/image_21.png) 

Maintenant il faudra rédiger un code dans les 3 onglets et les reliés ensemble pour que le code puisse fonctionner. 