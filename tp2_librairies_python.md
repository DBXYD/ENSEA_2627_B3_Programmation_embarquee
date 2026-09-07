## TP 2 - Bibliothèques Python et interfaces matérielles

### Objectifs

À la fin de ce TP, vous devez être capables de :

* identifier l'interface matérielle adaptée à un composant ;
* installer et importer les bibliothèques Python utilisées dans les TP suivants ;
* distinguer GPIO, UART, I2C et SPI ;
* tester chaque interface avec un programme court ;
* expliquer le rôle d'un driver entre un programme Python et le matériel.

#### Les interfaces de la Raspberry Pi

**📖 Lecture** Les broches GPIO de la Raspberry Pi peuvent être utilisées de plusieurs manières :

| Interface | Usage principal | Bibliothèque Python utilisée ensuite |
| --- | --- | --- |
| GPIO | Entrée/sortie numérique, impulsions et direction moteur | `RPi.GPIO` |
| UART | Console série et communication point à point | `pyserial` côté Python, `minicom` pour une console |
| I2C | Communication avec plusieurs capteurs adressés sur deux fils | `smbus2` |
| SPI | Communication rapide avec un convertisseur ou un périphérique | `spidev` |

Les interfaces ne sont pas interchangeables : le composant doit être conçu pour le protocole utilisé. Consultez le brochage de la Raspberry Pi avant tout câblage et vérifiez les niveaux logiques. Les GPIO de la Raspberry Pi fonctionnent en 3,3 V et ne doivent pas recevoir directement 5 V.

Un driver est un module qui masque les détails de la communication matérielle. Le programme principal appelle par exemple `read_accel()` ou `rotate()`, tandis que le driver s'occupe des registres, des octets, des timings et des broches.

#### Installer les bibliothèques

**🛠️ Manipulation / code** Sur la Raspberry Pi, installez les paquets système nécessaires :

```bash
sudo apt update
sudo apt install -y python3-rpi.gpio python3-smbus2 python3-spidev python3-serial minicom
```

Les rôles des paquets sont les suivants :

* `python3-rpi.gpio` fournit le module `RPi.GPIO` pour piloter les GPIO ;
* `python3-smbus2` fournit le module `smbus2` pour le bus I2C ;
* `python3-spidev` fournit le module `spidev` pour le bus SPI ;
* `python3-serial` fournit le module `serial` de PySerial pour l'UART ;
* `minicom` est un programme de terminal série, ce n'est pas une bibliothèque Python.

Vérifiez que Python peut importer les modules :

```bash
python3 - <<'PY'
import RPi.GPIO
import serial
import smbus2
import spidev
print("Bibliothèques disponibles")
PY
```

Si un import échoue, notez le nom du module et le paquet manquant avant de poursuivre.

#### Configurer les interfaces

**🛠️ Manipulation / code** Activez les interfaces nécessaires avec :

```bash
sudo raspi-config
```

Dans `Interface Options`, activez `I2C` et `SPI`. L'UART de la console a été activé dans le TP1 avec `enable_uart=1`. Redémarrez si le configurateur le demande :

```bash
sudo reboot
```

Après redémarrage, vérifiez les périphériques détectés :

```bash
ls -l /dev/i2c-* /dev/spidev* /dev/ttyAMA* /dev/ttyS* 2>/dev/null
```

Les noms peuvent varier selon le modèle et la configuration. Sur une Raspberry Pi, le bus I2C est généralement `/dev/i2c-1` et le bus SPI principal `/dev/spidev0.0`.

#### Tester un GPIO

**🛠️ Manipulation / code** Le programme suivant configure une broche en sortie et la fait commuter. Ne branchez pas directement un moteur sur un GPIO : utilisez un driver adapté.

```python
import time
import RPi.GPIO as GPIO

LED_PIN = 18

GPIO.setmode(GPIO.BCM)
GPIO.setup(LED_PIN, GPIO.OUT, initial=GPIO.LOW)

try:
    for _ in range(5):
        GPIO.output(LED_PIN, GPIO.HIGH)
        time.sleep(0.5)
        GPIO.output(LED_PIN, GPIO.LOW)
        time.sleep(0.5)
finally:
    GPIO.cleanup()
```

Le bloc `finally` garantit la libération des GPIO, même si le programme est interrompu.

#### Tester l'UART avec PySerial

**🛠️ Manipulation / code** La console UART du TP1 utilise `minicom`. Pour communiquer avec un périphérique série depuis Python, utilisez PySerial :

```python
import serial

with serial.Serial("/dev/ttyUSB0", baudrate=115200, timeout=1) as port:
    port.write(b"message\n")
    response = port.readline()
    print(response.decode(errors="replace").rstrip())
```

Remplacez `/dev/ttyUSB0` par le port réellement détecté. Ne lancez pas `minicom` et ce programme en même temps sur le même port.

#### Observer I2C et SPI

**🛠️ Manipulation / code** Pour rechercher les périphériques I2C, installez puis lancez l'outil de détection :

```bash
sudo apt install -y i2c-tools
sudo i2cdetect -y 1
```

L'adresse `0x6A` du LSM6DSOX doit apparaître si le capteur est correctement alimenté et câblé. Pour le SPI, vérifiez que le périphérique est présent :

```bash
ls /dev/spidev*
```

Le TP4 utilisera ensuite `spidev` pour communiquer avec le MCP3208.

#### Choisir une bibliothèque

**📖 Lecture**
* Utilisez `RPi.GPIO` lorsqu'il faut lire un état logique, produire une impulsion ou fixer une direction.
* Utilisez `smbus2` lorsqu'un composant est identifié par une adresse I2C et possède des registres.
* Utilisez `spidev` lorsqu'un composant échange des octets sur SPI et que la sélection du périphérique est gérée par le bus.
* Utilisez `serial` lorsque le programme doit échanger des octets avec un port UART.
* Utilisez `time` pour les temporisations et `math` pour les conversions et les angles ; ce sont des modules standards de Python.

**💭 Réflexion** Répondez aux questions suivantes :

1. Pourquoi un moteur ne doit-il pas être branché directement sur une broche GPIO ?
2. Quelle différence y a-t-il entre une adresse I2C et une sélection de périphérique SPI ?
3. Pourquoi une communication UART nécessite-t-elle d'accorder le débit entre les deux appareils ?
4. Pourquoi les programmes des TP doivent-ils toujours fermer ou nettoyer les interfaces matérielles ?
5. Quel est le rôle d'un driver et que gagne-t-on à séparer `main.py`, `config.py` et le driver ?
6. Pour chaque TP suivant, indiquez l'interface et la bibliothèque principalement utilisées : LSM6DSOX, MCP3208 et TMC2225.

### Livrable

Ajoutez au compte rendu :

* le résultat de l'installation et du test des imports ;
* le résultat de la détection I2C et de la présence du périphérique SPI ;
* le schéma des interfaces utilisées dans les TP suivants ;
* les réponses aux questions de réflexion.
