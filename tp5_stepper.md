## TP 5

### Objectifs

* Rédiger le code pour le moteur

#### Structure du code
**🛠️ Manipulation / code** Créez trois fichiers Python : `main.py`, `config.py` et `driver.py`, dans une structure de paquet permettant les imports `motor.driver` et `motor.config`. Chacun a un rôle bien défini :
* main.py : programme principal, initialise et teste le moteur.
* config.py : contient les constantes tels que les pins, direction, vitesse...
* driver.py : contient la logique pour faire bouger le moteur

#### Driver.py

**📖 Lecture** Le driver TMC2225 reçoit des impulsions `STEP` et un niveau logique `DIR`. La vitesse dépend de la fréquence des impulsions, tandis que le nombre d'impulsions détermine le déplacement.
Ce fichier est le cœur du code, contrôle le moteur via les GPIO.

**🛠️ Manipulation / code** Pour commencer, importez RPI.GPIO et time :

* RPI.GPIO : bibliothèque pour contrôler les broches GPIO de la Raspberry Pi.
* time : permet de faire des pauses entre les impulsions envoyées au moteur.

Importez les constantes de config.py.

```python
import RPi.GPIO as GPIO
import time
from .config import (DEFAULT_SPEED_RPM, DEFAULT_STEPS_PER_REV, DEFAULT_MICROSTEP,
                     DIRECTION_FORWARD, DIRECTION_BACKWARD)
```

Créez une classe TMC2225 puis vous initialisez les broches et configurez les comme sorties, puis configurez la vitesse et la direction initiale.

```python
class TMC2225:

    def __init__(self, step_pin, dir_pin, 
                 speed_rpm=DEFAULT_SPEED_RPM, 
                 direction=DIRECTION_FORWARD, 
                 steps_per_rev=DEFAULT_STEPS_PER_REV, 
                 microstep=DEFAULT_MICROSTEP):
        
        self.step_pin = step_pin
        self.dir_pin = dir_pin
        self.steps_per_rev = steps_per_rev
        self.microstep = microstep

        GPIO.setup(self.step_pin, GPIO.OUT)
        GPIO.setup(self.dir_pin, GPIO.OUT)

        self.set_speed(speed_rpm)
        self.set_direction(direction)
```

Définissez un "set_speed" pour convertir la vitesse en fréquence d'impulsions (Hz) (plus la fréquence est élevée, plus le moteur tourne vite) 

```python
def set_speed(self, speed_rpm):
```

Définissez un "set_direction" pour définir le sens de rotation du moteur (avant/arrière)

```python
def set_direction(self, direction):
```

Définissez un "step" pour effectuer un nombre de pas donné, il permet d'envoyer des impulsions carrées au driver du moteur.

```python
def step(self, steps=1):
```

Définissez "rotate" pour faire tourner le moteur d'un angle en degrés, cette fonction permet de calculer combien de pas correspondent à un angle donné (en degrés)

```python
def rotate(self, angle):
```
définissez "info" pour afficher les informations du moteur, la fonction peut servir à afficher les valeurs actuelles des paramètres du moteurs.

```python
def info(self):
```

Enfin, définissez un "cleanup" pour nettoyer les GPIO du moteur, il permet de les libérer pour éviter les problèmes.

```python
def cleanup(self):
```

**💭 Réflexion** Répondez aux questions suivantes :

1. Pourquoi une impulsion `STEP` ne doit-elle pas être remplacée par un simple changement permanent de niveau ?
2. Quelle est la relation entre `speed_rpm`, `steps_per_rev` et la fréquence des impulsions ?
3. Comment le micro-pas modifie-t-il le nombre d'impulsions nécessaires pour un tour ?
4. Pourquoi faut-il appeler `cleanup()` même si le programme s'arrête à cause d'une erreur ?
5. Quelles précautions faut-il prendre avant de brancher ou de déplacer un moteur piloté par le driver ?

#### main.py

**🛠️ Manipulation / code** Écrivez le programme de test, faites d'abord tourner le moteur à faible vitesse et vérifiez le sens de rotation avant d'utiliser des angles importants.
Ce fichier initialise, affiche les informations et teste la rotation du moteur. C'est le programme d'exécution.

Pour commencer, importez TMC2225 et time ainsi que les constantes de motor.config.

* TMC2225 : Contient les fonctions pour piloter le moteur.
* motor.config : Variables qui définissent les numéros des pins GPIO, la vitesse en RPM et la direction initiale (avant/arrière)

```python
from motor.driver import TMC2225
import time

from motor.config import (MOTOR1_STEP_PIN, MOTOR1_DIR_PIN, MOTOR1_SPEED_RPM, MOTOR1_DIRECTION, MOTOR2_STEP_PIN, MOTOR2_DIR_PIN, MOTOR2_SPEED_RPM, MOTOR2_DIRECTION)
```

Exécutez le code seulement si le fichier est lancé directement, pas importé par un autre fichier.

```python
if __name__ == "__main__":
```

Définissez les paramètres du test pour modifier le test facilement sans toucher au code du moteur.

```python
    TEST_ANGLE_MOTOR1 = 5000 
    TEST_ANGLE_MOTOR2 = 180 
    WAIT_DURATION = 1  
```

Créez 2 objets moteur pour initialiser des moteurs à partir de la configuration, et les afficher.

```python
    motor1 = TMC2225(...)
    motor2 = TMC2225(...)

    motor1.info()
    motor2.info()
```

Exécutez le test avec la variables, utilisez la méthode rotate(angle) pour convertir l'angle en un nombre de pas moteurs et les exécuter ensuite.

```python
    motor1.rotate(TEST_ANGLE_MOTOR1)
    motor2.rotate(TEST_ANGLE_MOTOR2)
```
Mettez une pause avec "time.sleep"

Nettoyez les GPIO avec la fonction "cleanup"

**💭 Réflexion** Vérifiez les points suivants dans votre compte rendu :

1. Combien de pas sont nécessaires pour les angles demandés par `TEST_ANGLE_MOTOR1` et `TEST_ANGLE_MOTOR2` ?
2. Que se passe-t-il si l'angle demandé n'est pas un multiple du pas moteur ?
3. Comment détecteriez-vous qu'un moteur a perdu des pas ?
4. Pourquoi un test de `5000` degrés peut-il être inadapté pour une première mise sous tension ?

#### config.py

**🛠️ Manipulation / code** Regroupez ici les broches, la vitesse par défaut, le nombre de pas par tour, le micro-pas et les constantes de direction. Vérifiez que les numéros de broches correspondent au câblage réel.

**💭 Réflexion** Expliquez pourquoi les paramètres matériels doivent être séparés du code du driver. Quelle modification doit être possible sans réécrire la classe `TMC2225` ?
