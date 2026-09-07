## TP 3
### Objectifs

* Rédigez le code pour l'accéléromètre en i2C.

#### Structure du code

**🛠️ Manipulation / code** Créez trois fichiers Python : `main.py`, `setting.py` et `drv_lsm6dsox.py`.

Chacun a un rôle bien défini :
* main.py -> programme principal, utilise le driver pour afficher les valeurs physiques du capteur.
* setting.py -> définit toutes les constantes nécessaires pour configurer et comprendre le capteur LSM6DSOX.
* drv_lsm6dsox.py -> gère la communication i2C et fournit des fonctions permettant de lire le capteur.

#### setting.py

**📖 Lecture** Le fichier `setting.py` regroupe les constantes qui décrivent le capteur et évite de disperser les valeurs numériques dans le programme.

**🛠️ Manipulation / code** Déclarez l'adresse i2C du capteur LSM6DSOX avec une valeur hex pour pouvoir parler au capteur sur le bus i2C.
```python
LSM6DSOX_ADDR = 0x6A
```

Indiquez le numéro du bus i2C à utiliser (1 pour la Raspberry Pi) mais aussi le nombre d'octets à lire pour chaque bloc de données.

```python
I2C_BUS = 1
BLOCK_SIZE = 6  # 6 octets = 2 octets par axe (X, Y, Z)
```

Mettez des constantes pour configurer la fréquence d'échantillonnage de l'accéléromètre dans les registres du capteur.
```python
FQ_POWER_DOWN = 0x00   # gyroscope disabled
FQ12_5HZ      = 0x10
FQ26HZ        = 0x20
FQ52HZ        = 0x30
FQ104HZ       = 0x40
FQ208HZ       = 0x50
FQ416HZ       = 0x60
FQ833HZ       = 0x70
FQ1660HZ      = 0x80  # 1.66 kHz
FQ3330HZ      = 0x90  # 3.33 kHz
FQ6660HZ      = 0xA0  # 6.66 kHz
```

Configurez la plage de l'accéléromètre avec des constantes qui se combineront avec le ODR (=fréquence d'échantillonnage).
```python
FS_2G  = 0x00
FS_16G = 0x04
FS_4G  = 0x08
FS_8G  = 0x0C
```

Faites la même chose pour le gyroscope.

```python
FQ_G_POWER_DOWN = 0x00  # gyroscope disabled
FQ_G_12_5HZ     = 0x10
FQ_G_26HZ       = 0x20
FQ_G_52HZ       = 0x30
FQ_G_104HZ      = 0x40
FQ_G_208HZ      = 0x50
FQ_G_416HZ      = 0x60
FQ_G_833HZ      = 0x70
FQ_G_1660HZ     = 0x80
FQ_G_3330HZ     = 0x90
FQ_G_6660HZ     = 0xA0
```

Configurez la plage angulaire du gyroscope pour définir la sensibilité et affecter le facteur de conversion des valeurs brutes.

```python
FS_G_125DPS   = 0x02
FS_G_245DPS   = 0x00
FS_G_500DPS   = 0x04
FS_G_1000DPS  = 0x08
FS_G_2000DPS  = 0x0C
```
Mettez des constantes pour représenter, chacune, un bit pour configurer des fonctions globales : reboot, BDU (block data update), auto-incrément d'adresse, reset logiciel, mode SPI, type d'IRQ, etc.

```python
CTRL3_C_BOOT      = 0x80  # Bit 7: Reboot memory content
CTRL3_C_BDU       = 0x40  # Bit 6: Block Data Update
CTRL3_C_H_LACTIVE = 0x20  # Bit 5: Interrupt active level (0: high, 1: low)
CTRL3_C_PP_OD     = 0x10  # Bit 4: Push-pull / Open-drain selection for INT1/INT2
CTRL3_C_SIM       = 0x08  # Bit 3: SPI Serial Interface Mode selection (0: 4-wire, 1: 3-wire)
CTRL3_C_IF_INC    = 0x04  # Bit 2: Register address auto-increment enable (1: enabled)
# Bit 1 is reserved and should be kept at 0
CTRL3_C_SW_RESET  = 0x01  # Bit 0: Software Reset
```

Mettez un délai (en secondes) entre les lectures de capteur.

```python
READ_DELAY = 0.1
```

Configurez des facteurs d'échelle pour convertir les valeurs brutes (LSB) de l'accéléromètre en g (accélération gravitationnelle).

```python
SF_2G   = 0.000061
SF_4G   = 0.000122
SF_8G   = 0.000244
SF_16G  = 0.000488
```

Faites la même chose pour le gyroscope mais convertissez les valeurs en degrés par seconde (dps). Nommez ces constantes en cohérence avec les plages "FS_G_*DPS" définies plus haut.

```python
SF_G_125DPS   = 0.004375
SF_G_245DPS   = 0.00875
SF_G_500DPS   = 0.0175
SF_G_1000DPS  = 0.035
SF_G_2000DPS  = 0.07
```

#### drv_lsm6dsox.py

**🛠️ Manipulation / code** Implémentez le driver en vous appuyant sur les registres et les constantes définis précédemment.

D'abord importez smbus2, time et setting.

* smbus2 : fournit les fonctions i2C pour Raspberry Pi.
* time : Introduire des pauses.
* from setting import * : importe toutes les constantes définies dans le setting

Commencez par mettre les constantes importantes qui représentent les adresses des registres du LSM6DSOX.

```python
CTRL1_XL    = 0x10  # Accelerometer configuration (ODR, range, etc.)
CTRL2_G     = 0x11  # Gyroscope configuration (ODR, range, etc.)
CTRL3_C     = 0x12  # General configuration (BDU, auto-increment...)
OUTX_L_G    = 0x22  # Start of gyroscope data registers (6 bytes)
OUTX_L_XL   = 0x28  # Start of accelerometer data registers (6 bytes)
```
Définissez une class drv_lsm6dsox pour initialiser les paramètres "bus=I2C_BUS" et "adresse=LSM6DSOX_ADDR"
* bus=I2C_BUS : pour ouvrir le bus i2C
* adresse=LSM6DSOX_ADDR : mémoriser l'adresse i2C du capteur (0x6A ou 0x6B).

```python
class drv_lsm6dsox:
    def __init__(self, bus=I2C_BUS, adresse=LSM6DSOX_ADDR):
        # Open the I2C bus (bus=1 for Raspberry Pi)
        self.bus = smbus2.SMBus(bus)
        self.adresse = adresse
        self.init_lsm6dsox()
```

Initialisez le capteur lsm6dsox pour le configurer en écrivant dans ses registres via i2C, écrire dans CTRL1_XL un octet combinant la fréquence et la plage pour l'accéléromètre mais aussi faire la même chose pour le gyroscope, activer BDU et IF_INC pour permettre la lecture séquentielle de plusieurs registres en un seul bloc.

```python
    def init_lsm6dsox(self):
```


Définissez une fonction lire l'accéléromètre pour lire les octets à partir d"un registre définit, lire un bloc d'octets, combiner l'octet de poids fort et l'octet de poids faible pour reconstituer chaque axe et enfin pour renvoyer les valeurs brutes. 

```python
    def read_accel(self):
```

Définissez une fonction pour lire le gyroscope pour faire les mêmes choses que pour l'accéléromètre.

```python
    def read_gyro(self):
```

Définissez une fonction pour convertir un entier non signé en entier signé selon la représentation en complément à deux.

```python
    def _twos_complement(self, val, bits):
```

**💭 Réflexion** Répondez aux questions suivantes :

1. Pourquoi une mesure sur deux octets doit-elle être interprétée en complément à deux ?
2. Comment reconnaît-on qu'une valeur signée est négative ?
3. Pourquoi faut-il activer `BDU` avant de lire les six octets d'un bloc de mesures ?
4. Quel est le rôle de l'auto-incrément d'adresse `IF_INC` ?
5. Que peut-on déduire si le capteur répond à l'adresse `0x6B` mais pas à `0x6A` ?

#### main.py

**🛠️ Manipulation / code** Écrivez le programme principal, lancez-le sur la Raspberry Pi et observez les mesures lorsque le capteur est immobile puis incliné.

Commencez par importer drv_lsm6dsox, setting et math
* from drv_lsm6dsox import * : importe tout le driver étudié juste avant.
* from setting import * : importer toutes les constantes.
* math : nécessaire pour utiliser "atan2()" et "degrees()"

Mettez un bloc principale qui s'exécute uniquement si le fichier est lancé directement et non lorsqu'il est importé dans un autre module.

```python
    if __name__ == "__main__":
```

Créez un objet driver qui va communiquer avec le capteur LSM6DSOX via i2C, le constructeur appellera automatiquement init_lsm6dox. Puis affichez un message indiquant que les mesures vont démarrer.

```python
    driver = drv_lsm6dsox(bus=I2C_BUS)
```

Configurez une boucle principale pour lire en continue.
```python
    while True:
```

Lisez les données du capteur pour renvoyer les valeurs brutes de l'accéléromètre et du gyroscope.
```python
        x_a, y_a, z_a = driver.read_accel()
        x_g, y_g, z_g = driver.read_gyro()
```

Calculez les angles sur X et Y. 
* Convertir les valeurs brutes en g.
* Calculer l'angle entre 2 axes stables.
* Convertir en degrés.

Affichez les données converties, accéléromètre en g et gyroscope en dps.
Affichez aussi les angles calculés en format 2 décimales + symbole °.

```python
        angle_x = math.degrees(math.atan2(y_a * SF_2G,z_a * SF_2G))
        angle_y = math.degrees(math.atan2(x_a * SF_2G, z_a * SF_2G))
```

Marquez une pause pour laisser le capteur générer de nouvelles mesures.
```python
        time.sleep(READ_DELAY)
```

**💭 Réflexion** Dans votre compte rendu :

1. Quelle valeur d'accélération doit-on observer approximativement sur un axe aligné avec la gravité lorsque le capteur est immobile ?
2. Pourquoi les angles calculés avec `atan2` sont-ils plus robustes qu'un simple rapport entre deux axes ?
3. Quelle est l'influence de `READ_DELAY` sur la fréquence d'affichage et la charge du processeur ?
4. Que se passe-t-il si la plage `FS_2G` est dépassée ? Proposez une configuration adaptée à des mouvements plus brusques.