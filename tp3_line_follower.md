
## TP 3
### Objectifs

* Rédige le code pour le suiveur de ligne.

#### Structure du code

créez trois fichiers python "main.py", et par exemple "line_detector.py" et "MCP3208.py".

Chacun a un rôle bien défini :

main.py -> programme principale, fait tourner le capteur de suivi de ligne.
line_detector.py -> Lit les capteurs et détecte la ligne.
MCP3208.py -> contient les informations pour lire les capteurs IR, analyser les valeurs, afficher les valeurs des capteurs.

#### MCP3208.py

Pour commencer, importez "spidev" pour communiquer avec les périphériques SPI sur la Rasberry Pi.

Utilisez des constantes pour construire la commande à envoyer à l'ADC.
```python
BIT_START = 4
BIT_CONV_SINGLE = 2
```

Créez une classe MCP3208 pour initialiser l'ADC avec les paramétres "spi_bus", "spi_device", "clock_speed" et "vref".
* spi_bus, spi_device : numéro du bus et du périphérique SPI -> ouvre la communication SPI avec le bus et le périphérique spécifiés.
* clock_speed : vitesse SPI -> définit la vitesse max de la communication SPI.
* vref : tension de référence -> sauvegarde la tension de référence dans l'objet, pour convertir les valeurs ADC en tension.

```python
class MCP3208:
    def __init__(self, spi_bus=0, spi_device=0, clock_speed=1e6, vref=3.3):
        self.spi = spidev.SpiDev()
        self.spi.open(spi_bus, spi_device)
        self.spi.max_speed_hz = clock_speed
        self.vref = vref
```

Définissez une méthode pour lire la valeur brute et vérifiez si le numéro du canal est visible sinon il souléve l'erreur.

```python
    def reac_channel(self, channel);
```

Construisez une commande SPI pour le MCP3208. Ensuite Envoyez la commande pour reçevoir la réponse de 3octets de l'ADC. Convertir le résultat ADC 12 bits à partir des octets de réponse et enfin retournez la valeur.

```python
    command = [BIT_START | BIT_CONV_SINGLE | (channel >> 2), (channel & 3) << 6, 0]
    result = self.spi.xfer2(command)
    value = ((result[1] & 15) << 8 ) | result[2]
    return value
```

Définissez une méthode pour lire la tension réelle d'un canal.

```python
    def read_voltage(self, channel)
```

Définissez une méthode pour lire tous les 8 canaux en tension et retourner une liste de 8 valeurs.

```python
    def read_all_channels(self);
```

Fermez proprement la communication SPI pour libérer le périphérique et éviter les erreurs.

```python
    def close(self):
```

#### line_detector.py

Importez MCP3208 et time.

* MCP3208 : Communiquer avec l'ADC.
* time : marquer des pauses de lectures.

Mettre un seuil de tension pour déterminer si le capteur détecte la ligne.

```python
THRESHOLD = 1.5
```

Définissez une fonction pour lire les 8 capteurs et détecter la position de la ligne.

```python
    def detect_line(IFR, threshold=THRESHOLD)
```

Lisez les tensions de chaque capteurs connecté aux 8 canaux de l'ADC, et affichez les valeurs en 2 décimales pour monitoring (=surveillance).

```python
    sensor_0 = IFR.read_voltage(0)
    sensor_1 = IFR.read_voltage(1)
    sensor_2 = IFR.read_voltage(2)
    sensor_3 = IFR.read_voltage(3)
    sensor_4 = IFR.read_voltage(4)
    sensor_5 = IFR.read_voltage(5)
    sensor_6 = IFR.read_voltage(6)
    sensor_7 = IFR.read_voltage(7)

```

Mettez une logique de base pour suivre la ligne, c'est à dire comparez chaque tension à "threshold" (=seuil pour détecter la ligne).

Enfin affichez la position détectée à côté des tension des capteurs, marquez une pause pour éviter de saturer le CPU (=microprocesseur) et la console et renvoyez la position détectée afin que le programme principal "main.py" puisse l'utliser.

```python
    print(position)
    time.sleep(0.2)
    return position
```

#### main.py

Importez MCP3208, detect_line et time.

* MCP3208 : Communiquer avec l'ADC.
* detect_line : lire les capteurs et détecter la position de la ligne.
* time : Introduire des délais

Mettez les constantes"THRESHOLD" et "DELAY".

Définissez la fonction principale qui contient la boucle principale du suiveur de ligne.

```python
def main():
```

Initialisez l'ADC MCP3208 avec la tension de référence et utilisez "IFR" pour lire les capteurs via SPI.

```python
IFR = MCP3208(vref=3.3)
```

Affichez un message indiquant que le suiveur de ligne commence à suivre la ligne en l'incluant dans un bloc "try" qui permet de gérer les interruptions et les erreurs proprement.

mettez une boucle principale pour lire les capteurs en continue.

```python
while True:
    position = detect_line(IFR, threshold=THRESHOLD)
    time.sleep(DELAY)
```
Mettez une gestion de lt'interrupteur clavier, si l'utilisateur appuie sur "ctrl+C" le programme se ferme correctement la communication SPI.

```python
except KeyboardInterrupt:
    # Handles manuel interruption (Ctrl+C)
    IFR.close()
    print("\nPProgram stopped.")
```

Mettez une gestion pour les autres erreurs, si une erreur inattendue survient alors la communication SPI se ferme pour ne pas bloquer le périphérique et affiche l'erreur pour le debug.

```python
except Exception as e:
    IFR.close()
    printf("Error detected: {e}")
```
Enfin, mettez une exécution conditionnelle pour vérifier que le fichier est exécuté directement et si oui, alors il appelle "main()" pour démarrer le programme.

```python
__if __name__ == "__main__":
```

