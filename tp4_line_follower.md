## TP 4
### Objectifs

* Rédigez le code pour le suiveur de ligne.

#### Structure du code

**🛠️ Manipulation / code** Créez trois fichiers Python : `main.py`, `line_detector.py` et `MCP3208.py`.

Chacun a un rôle bien défini :

main.py -> programme principal, fait tourner le capteur de suivi de ligne.
line_detector.py -> Lit les capteurs et détecte la ligne.
MCP3208.py -> contient les informations pour lire les capteurs IR, analyser les valeurs, afficher les valeurs des capteurs.

#### MCP3208.py

**📖 Lecture** Le MCP3208 est un convertisseur analogique-numérique 12 bits. Il transforme une tension comprise entre 0 V et `vref` en une valeur entière comprise entre 0 et 4095, transmise à la Raspberry Pi par SPI.

**🛠️ Manipulation / code** Pour commencer, importez "spidev" pour communiquer avec les périphériques SPI sur la Raspberry Pi.

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

Définissez une méthode pour lire la valeur brute et vérifiez si le numéro du canal est valide sinon elle soulève l'erreur.

```python
    def read_channel(self, channel):
```

Construisez une commande SPI pour le MCP3208. Ensuite envoyez la commande pour recevoir la réponse de 3 octets de l'ADC. Convertir le résultat ADC 12 bits à partir des octets de réponse et enfin retournez la valeur.

```python
    command = [BIT_START | BIT_CONV_SINGLE | (channel >> 2), (channel & 3) << 6, 0]
    result = self.spi.xfer2(command)
    value = ((result[1] & 15) << 8 ) | result[2]
    return value
```

Définissez une méthode pour lire la tension réelle d'un canal.

```python
    def read_voltage(self, channel):
```

Définissez une méthode pour lire tous les 8 canaux en tension et retourner une liste de 8 valeurs.

```python
    def read_all_channels(self):
```

Fermez proprement la communication SPI pour libérer le périphérique et éviter les erreurs.

```python
    def close(self):
```

**💭 Réflexion** Répondez aux questions suivantes :

1. Pourquoi la valeur maximale d'un ADC 12 bits est-elle 4095 et non 4096 ?
2. Quelle tension correspond à une mesure brute de 2048 avec `vref = 3.3 V` ?
3. Quel serait l'effet d'une mauvaise valeur de `vref` sur les tensions calculées ?
4. Pourquoi faut-il vérifier le numéro du canal avant de construire la commande SPI ?

#### line_detector.py

**🛠️ Manipulation / code** Implémentez la lecture des huit capteurs et la logique qui transforme leurs tensions en une position de ligne.

Importez MCP3208 et time.

* MCP3208 : Communiquer avec l'ADC.
* time : marquer des pauses de lectures.

Mettre un seuil de tension pour déterminer si le capteur détecte la ligne.

```python
THRESHOLD = 1.5
```

Définissez une fonction pour lire les 8 capteurs et détecter la position de la ligne.

```python
    def detect_line(IFR, threshold=THRESHOLD):
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

Enfin affichez la position détectée à côté des tensions des capteurs, marquez une pause pour éviter de saturer le CPU (=microprocesseur) et la console et renvoyez la position détectée afin que le programme principal "main.py" puisse l'utiliser.

```python
    print(position)
    time.sleep(0.2)
    return position
```

**💭 Réflexion** Le seuil `THRESHOLD = 1.5` est un choix expérimental. Expliquez comment le mesurer ou le régler à partir des valeurs observées sur le sol clair et sur la ligne. Indiquez aussi ce que doit faire le programme si aucun capteur ou plusieurs capteurs détectent la ligne.

#### main.py

**🛠️ Manipulation / code** Assemblez l'ADC et le détecteur dans `main.py`, puis testez l'arrêt par `Ctrl+C`.

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
try:
    print("Line follower started.")
    while True:
        position = detect_line(IFR, threshold=THRESHOLD)
        time.sleep(DELAY)
```
Mettez une gestion de l'interruption clavier, si l'utilisateur appuie sur "ctrl+C" le programme ferme correctement la communication SPI.

```python
except KeyboardInterrupt:
    # Handles manual interruption (Ctrl+C)
    IFR.close()
    print("\nProgram stopped.")
```

Mettez une gestion pour les autres erreurs, si une erreur inattendue survient alors la communication SPI se ferme pour ne pas bloquer le périphérique et affiche l'erreur pour le debug.

```python
except Exception as e:
    IFR.close()
    print(f"Error detected: {e}")
```
Enfin, mettez une exécution conditionnelle pour vérifier que le fichier est exécuté directement et si oui, alors il appelle "main()" pour démarrer le programme.

```python
if __name__ == "__main__":
    main()
```

**💭 Réflexion** Pourquoi le bloc `try`/`except` doit-il fermer SPI dans les cas `KeyboardInterrupt` et `Exception` ? Quelle différence y a-t-il entre une interruption utilisateur et une erreur inattendue ?

