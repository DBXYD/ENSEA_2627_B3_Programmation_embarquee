## TP 1
### objectif
* Configurer la Raspberry Pi Zero
* Un tutoriel pas a pas est mis en place pour vous aider à installer les fichiers de l'OS qui sont nécessaires au bon fonctionnement de la Raspberry Pi : 
  
   1. Flasher la carte SD 
   2. Activer la liaison UART
   3. Configurer la connexion 
   4. Se connecter en ssh via le Wifi
   5. Sécuriser la connexion ssh avec une clé 

#### Flasher la carte SD
Allez sur https://www.raspberrypi.com/software/ et installez la Raspberry selon votre type de PC (Winodws, MacOS).

![](img/image_01.png)

Sélectionnez le bon modèle de RPI : Raspberry Pi Zero 2W

![](img/image_02.png)

Sélectionnez l'OS adapté à notre utilisation : Raspberry Pi (other) -> L'OS Lite (32-bit)

![](img/image_03.png)
![](img/image_04.png)

Sélectionnez le support d'installation de l'OS, sélectionnez la carte SD 

![](img/image_05.png)

Validez la configuration, la carte SD est prête à être flachée. Cliquez sur Next et choisissez "Edit settings" 

![](img/image_06.png)

Modifiez quelques paramètres :
* Le hostname -> Choisir la forme "nom de la salle-tes initiales-initiales du binôme"
* nom d'utilisateur et un mot de passe (le mot de passe sera nécessaire à chaque fois donc à retenir)

![](img/image_07.png)

Validez les modifications apportées et les appliquer a l'installation. 
Validez la copie de l'image de l'OS Linux sur la carte SD.

![](img/image_08.png)

Le processus dure quelques minutes, la fichiers sont copiés et une vérifications de la copie a lieu.

![](img/image_9.png)
![](img/image_10.png)

une fois fini, retirez la carte SD et réinsérez la carte SD flachée. Ensuite, éjectez la carte SD en toute sécrité puis insérez la dans la Raspberry Pi.

Assurez vous que la Raspberry est bien allumée et connectée au réseau WIfi. 

Appuyez sur "Windows + R" et tapez "cmd" puis "Entrée"

![](img/image_11.png)

Tapez, ensuite, "ipconfig"

![](img/image_12.png)

cherchez la section correspendant au réseau Wifi appelée "carte réseau sans fil Wifi" 

![](img/image_13.png)

Regardez la ligne "Passerelle par défaut..... : 'adresse ip'"
c'est l'adresse IP du routeur.

Allez sur internet et tapez sur la barre de recherche "http://192.168.65.1/#IP:DHCP_Server.Leases" en remplacant"192.168.65.1" par l'adresse IP de votre Wifi.

Regardez l'adresse IP de la Raspberry et la copier.

Ouvrez le terminal et tapez "ping 'adresse IP' "

![](img/image_14.png)

si vous avez les mêmes réponses affichées à l'écran, ça veut dire que votre PC voit votre Raspberry sur le réseau donc la connection réseau fonctionne. (pour l'arrêter, tapez : "ctrl + C")

tapez "sudo systemctl status ssh"

![](img/image_15.png)

Si vous voyez "active (running)" alors le SSH est déjà activé. 

Tapez "ssh user@adresse IP". Il vous demandera votre mot de passe (rien ne sera aficher pendant que vous écrirez, c'est normal)

![](img/image_16.png)

S'il y a bien écrit "Linux rpi-test-B3 ...
Last login: Wed Nov  5 ..." alors vous êtes bien connecté à votre Rasberry via le SSH.

Allez sur VS code et cliquez sur le petit symbole en bleu en bas à gauche de l'écran.

![](img/image_17.png)

Cliquez sur "Connect to host"

![](img/image_18.png)

Mettez "votre nom d'utilisateur@l'adresse IP"

![](img/image_19.png)

Il vous demandera sur quelle platefrome vous êtes (Linux, Windows ou MacOS), vous mettez "Linux"

Entrez votre mot de passe.
Revenez sur votre terminal et marquez "mkdir 'nom de votre dossier'" ensuite "cd 'nom de votre dossier'". Le dossier est creer, puis vous tapez "ls" puis "cd..".

![](img/image_20.png)

Revenez sur VS code et ouvrez le dossier, créez, ensuite, 3 fichier "main.py" "MCP3208.py" et "votre composant.py" 

![](img/image_21.png) 

Maintenant il faudra rédiger un code dans les 3 onglets et les reliés ensemble pour que le code puisse fonctionner. 