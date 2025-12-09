# Documentation des Registres Modbus Télécommande Aldes T.One AquaAIR/T.One Air

Ce document est basé sur du reverse engineering de la liaison entre la télécommande et le T.One, il ne s'agit pas d'une documentation officielle.
Il est incomplet et peut contenir des erreurs.

La télécommande agit comme un client Modbus (Master) et la pompe à chaleur est le serveur (Slave). Dans cette intégration, l'ESP32 agit comme un nouveau client (Master) en remplaçant le rôle de la télécommande pour lire et écrire les registres sur le bus

Configuration Modbus:
- Vitesse: `19200 bauds`
- Partité : `Paire`
- Bits de stop: `1`
- Adresse de l'esclave T.One :  `1`
- Type de registres : tous les registres documentés ici sont des `Holding Registers`

***

## 1. Registres de Statut (Lecture seule / Read-Only)

Ces registres fournissent des informations sur l'état actuel, les mesures de sondes et les données de diagnostic de la pompe à chaleur.


| Adresse (Dec) | Adresse (Hex) | Description                                   | Unité | Mise à l'échelle                  | Format     | Notes                                                                 |
|---------------|---------------|-----------------------------------------------|-------|-----------------------------------|------------|-----------------------------------------------------------------------|
| 1             | 0x0001        | Version du registre TOP                       | -     | -                                 | 16 bits   |                                                                      |
| 14-15         | 0x000E-0x000F | ID IHM                                        | -     | -                                 | 32 bits   | Affichée en hexa                                                      |
| 16            | 0x0010        | Date                                          | -     | -                                 | 16 bits   | Voir formattage                                                       |
| 17            | 0x0011        | Heure                                         | -     | -                                 | 16 bits   | Voir formattage                                                       |
| 39            | 0x0027        | Température extérieure / ThoA                 | °C    | Valeur / 100                      | 16 bits   | Température ambiante extérieure.                                      |
| 42            | 0x002A        | Température Échangeur extérieur ThoR1         | °C    | Valeur / 100                      | 16 bits   |                                                                       |
| 44            | 0x002C        | Température Sortie compresseur                | °C    | Valeur / 100                      | 16 bits   |                                                                       |
| 49            | 0x0031        | Courant compresseur                           | A     | Valeur / 100                      | 16 bits   |                                                                       |
| 51            | 0x0033        | Protection compresseur                        | -     | -                                 | 16 bits   | Code d'état/protection.                                               |
| 61            | 0x003D        | Vitesse ventilateur UI (mesurée)              | RPM   | Valeur / 10                       | 16 bits   | Vitesse réelle du ventilateur de l'unité intérieure.                  |
| 66            | 0x0042        | Fréquence compresseur (mesurée)               | Hz    | Valeur / 10                       | 16 bits   | Fréquence réelle du compresseur.                                      |
| 72-73         | 0x0048-0x0049 | Temps compresseur ON                          | s     | -                                 | 32 bits   | Temps total de fonctionnement du compresseur.                         |
| 90            | 0x005A        | Défaut unité extérieure                       | -     | -                                 | 16 bits   | Code d'état/défaut de l'UE.                                           |
| 91            | 0x005B        | Position détendeur EEV1                       | Pls   | -                                 | 16 bits   | Impulsions (Pulse) du détendeur électronique.                         |
| 93            | 0x005D        | Niveau ventilateur extérieur                  | -     | -                                 | 16 bits   | Vitesse/niveau du ventilateur extérieur.                              |
| 94            | 0x005E        | Angle vanne 3 voies 1/2''                     | °     | -                                 | 16 bits   | Angle de la vanne (position).                                         |
| 95            | 0x005F        | Angle vanne 3 voies 1/4''                     | °     | -                                 | 16 bits   | Angle de la vanne (position).                                         |
| 106           | 0x006A        | Échangeur eau chaude (Pression)               | bar   | Valeur / 100                      | 16 bits   | Pression dans l'échangeur ECS (pour T.One AquaAIR).                   |
| 111           | 0x006F        | Sonde ECS haut                                | °C    | Valeur / 100                      | 16 bits   | Température sonde ECS partie haute.                                   |
| 112           | 0x0070        | Sonde ECS bas                                 | °C    | Valeur / 100                      | 16 bits   | Température sonde ECS partie basse.                                   |
| 113           | 0x0071        | ECS ligne liquide Th4                         | °C    | Valeur / 100                      | 16 bits   | Température ligne liquide ECS.                                        |
| 115           | 0x0073        | Ligne gaz Th3                                 | °C    | Valeur / 100                      | 16 bits   |                                                                       |
| 116           | 0x0074        | Échangeur air crosse Th5                      | °C    | Valeur / 100                      | 16 bits   |                                                                       |
| 117           | 0x0075        | Échangeur air capillaire Th6                  | °C    | Valeur / 100                      | 16 bits   |                                                                       |
| 131           | 0x0083        | État dégivrage                                | -     | -                                 | 16 bits   | 0: Dégivrage ON, 1: Dégivrage OFF                                     |
| 150-151       | 0x0096-0x0097 | Temps compresseur OFF                         | s     | -                                 | 32 bits   | Temps total d'arrêt du compresseur.                                   |
| 190           | 0x00BE        | Température Ambiance zone K1a                 | °C    | Valeur / 100                      | 16 bits   |                                                                       |
| 191           | 0x00BF        | Température Ambiance zone K1b                 | °C    | Valeur / 100                      | 16 bits   |                                                                       |
| 192           | 0x00C0        | Température Ambiance zone K2                  | °C    | Valeur / 100                      | 16 bits   |                                                                       |
| 193           | 0x00C1        | Température Ambiance zone K3                  | °C    | Valeur / 100                      | 16 bits   |                                                                       |
| 194           | 0x00C2        | Température Ambiance zone K4                  | °C    | Valeur / 100                      | 16 bits   |                                                                       |
| 5029          | 0x13A5        | Canaux actifs                                 | -     | -                                 | N BOOL     | 1 booléen par canal actif ( bit 0: K1a, bit 1 K1b, bit 3 K2...)       |
| 6021          | 0x1785        | État du circuit frigorifique                  | -     | -                                 | 16 bits   | 0: Initialisation, 1: Échangeur ECS, 2: Coil AIR, 3: Standby, 4: Sécurité, 5: Wait. |
| 6080          | 0x17C0        | Pourcentage ECS                               | %     | -                                 | 16 bits   | Pourcentage de charge/niveau de l'ECS.                                |
| 20047-20048   | 0x4E4F-0x4E50 | Temps ventilateur ON                          | s     | -                                 | 32 bits   | Temps total de fonctionnement du ventilateur.                         |
| 20049-20050   | 0x4E51-0x4E52 | Temps compresseur ON                          | s     | -                                 | 32 bits   | Temps total de fonctionnement du compresseur.                         |
| 20063         | 0x4E5F        | État Filtre                                   | -     | -                                 | 16 bits   | 0-2900: Bon, 2901-4394: Moyen, >4394: Mauvais.                        |
| 20162         | 0x4EBE        | Défauts #1 (Bitmask)                          | -     | -                                 | 16 BOOL   | 1 booléen par défaut (voir [correspondances](#registres-de-défauts)). |
| 20163         | 0x4EBF        | Défauts #2 (Bitmask)                          | -     | -                                 | 16 BOOL   | 1 booléen par défaut(voir [correspondances](#registres-de-défauts)).  |
| 20164         | 0x4EC0        | Défauts #3 (Bitmask)                          | -     | -                                 | 16 BOOL   | 1 booléen par défaut (voir [correspondances](#registres-de-défauts)). |
| 20165         | 0x4EC1        | Défauts #4 (Bitmask)                          | -     | -                                 | 16 BOOL   | 1 booléen par défaut (voir [correspondances](#registres-de-défauts)). |
| 20272         | 0x4F20        | ECS tout électrique                           | -     | -                                 | BOOL      | État du mode ECS tout électrique.                                     |
| 30026         | 0x754A        | Nombre de canaux réglés                       | -     | Valeur -1                          | 16 bits  | Nombre de thermostats                                                 |         


***

## 2. Registres de Commande et de Consigne (Lecture-Écriture / Read-Write)

Ces registres sont utilisés pour contrôler le comportement de la pompe à chaleur et ajuster les consignes
| Adresse (Dec) | Adresse (Hex) | Description                         | Unité | Mise à l'échelle                  | Format     | Plage de Valeurs / Mapping                                                                                                                                     |
|---------------|---------------|-------------------------------------|-------|-----------------------------------|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 7             | 0x0007        | Consigne ventilateur UI            | RPM   | Valeur / 10                       | 16 bits   | Vitesse cible du ventilateur.                                                                                                                          |
| 8             | 0x0008        | Consigne compresseur               | Hz    | Valeur / 10                       | 16 bits   | Fréquence cible du compresseur.                                                                                                                        |
| 9             | 0x0009        | Mode ECS                           | -     | -                                 | 8 bits   | MSB (Bits 8-15) Mode ECS 0: Off, 1 ON, 2: BOOST                                                                       |
| 9             | 0x0009        | Mode AIR & Mode ECS (Bitmask)      | -     | -                                 | 8 bits   | LSB (Bits 0-7)  Mode Air  : 0: Off, 1: Confort Chauffage, 2: Eco Chauffage, 3: Prog A Chauffage, 4: Prog B Chauffage, 5: Confort Clim, 6: Boost Clim, 7: Prog C Clim, 8: Prog D Clim, 9: Ventilation. |
| 64            | 0x0040        | Débit air cible                    | m³/h  | -                                 | 16 bits   | Débit volumique d'air cible.                                                                                                                                   |
| 152           | 0x0098        | Vitesse air cible                  | m/s   | Valeur / 10                       | 16 bits   | Vitesse surfacique de l'air.                                                                                                                                   |
| 157           | 0x009D        | Pression statique cible            | Pa    | -                                 | 16 bits   | Pression statique cible pour la ventilation.                                                                                                                   |
| 20279         | 0x4F27        | LED Bleue Télécommande             | -     | -                                 | BOOL      | 0: OFF, 1: ON.                                                                                                                                                 |
| 31002         | 0x7982        | Consigne ECS                       | °C    | -                                 | 16 bits   | Température de consigne ECS.                                                                                                                                   |
| 31007         | 0x7987        | Composition foyer                  | -     | Valeur +2                         | 16 bits   | Composition du foyer [2-5].                                                                                                                          |
| 31012         | 0x798C        | Jour cycle antilégionnelle         | -     | -                                 | 3 bits   | 0: OFF, 1: Lundi, ..., 7: Dimanche.                                                                                                                            |
| 31013         | 0x798D        | Tarif Kwh                          | €     | Valeur / 1000                     | 16 bits   | Prix du kWh.                                                                                                                                                   |
| 31015         | 0x798F        | Devise                             | -     | -                                 | BOOL      | 0: Euros, 1: Dollars.                                                                                                                                          |
| 31100         | 0x79E4        | Consigne zone K1a                  | °C    | Valeur / 100                      | 16 bits   | Consigne de température pour la zone 1a.                                                                                                                       |
| 31101         | 0x79E5        | Consigne zone K1b                  | °C    | Valeur / 100                      | 16 bits   | Consigne de température pour la zone 1b.                                                                                                                       |
| 31102         | 0x79E6        | Consigne zone K2                   | °C    | Valeur / 100                      | 16 bits   | Consigne de température pour la zone 2.                                                                                                                        |
| 31103         | 0x79E7        | Consigne zone K3                   | °C    | Valeur / 100                      | 16 bits   | Consigne de température pour la zone 3.                                                                                                                        |
| 31104         | 0x79E8        | Consigne zone K4                   | °C    | Valeur / 100                      | 16 bits   | Consigne de température pour la zone 4.                                                                                                                        |
                                                                                                  

***

### 2.1 Registres de Programmation Horaire (Prog A, B, C, D)

Ces registres utilisent des masques de bits pour définir les plages horaires actives sur une journée, avec deux registres par jour (MSB+LSB)

- Registre LSB (pair) : 00h–15h (16 heures)  
- Registre MSB (impair) : 16h–23h (8 heures)
#### Structure LSB (00h-15h) - 16 bits

| Bits        | Bit 15 | Bit 14 | Bit 13 | Bit 12 | Bit 11 | Bit 10 | Bit 9 | Bit 8 | Bit 7 | Bit 6 | Bit 5 | Bit 4 | Bit 3 | Bit 2 | Bit 1 | Bit 0 |
|-------------|--------|--------|--------|--------|--------|--------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
| **Heure**   | 15-16h | 14-15h | 13-14h | 12-13h | 11-12h | 10-11h | 09-10h| 08-09h| 07-08h| 06-07h| 05-06h| 04-05h| 03-04h| 02-03h| 01-02h| 00-01h|

#### Structure MSB (16h-23h) - 8 bits

| Bits     | Bit 7 | Bit 6 | Bit 5 | Bit 4 | Bit 3 | Bit 2 | Bit 1 | Bit 0 |
|----------|-------|-------|-------|-------|-------|-------|-------|-------|
| **Heure**| 23-00h| 22-23h| 21-22h| 20-21h| 19-20h| 18-19h| 17-18h| 16-17h|

#### Registres

| Adresse (Dec) | Adresse (Hex) | Description                 | Type   | Plages Horaires Représentées |
|---------------|---------------|-----------------------------|--------|------------------------------|
| 31200         | 0x7A40        | Prog A Lundi (16h-23h)     | 16 BOOL | MSB                          |
| 31201         | 0x7A41        | Prog A Lundi (00h-15h)     | 16 BOOL | LSB                          |
| 31202         | 0x7A42        | Prog A Mardi (16h-23h)     | 16 BOOL | MSB                          |
| 31203         | 0x7A43        | Prog A Mardi (00h-15h)     | 16 BOOL | LSB                          |
| ...           | ...           | ...                         |        |                              |
| 31254         | 0x7A7E        | Prog D Dimanche (16h-23h)  | 16 BOOL | MSB                          |
| 31255         | 0x7A7F        | Prog D Dimanche (00h-15h)  | 16 BOOL | LSB                          |

- Chauffage : Prog A et B
- Clim : Prog C et D

Les adresses couvrent 31200 à 31255, soit 4 programmes (A, B, C, D) × 7 jours × 2 registres = 56 registres au total


***

## 3. Registres de Consommation (Lecture Seule / Read-Only)

Ces registres donnent les consommations et coûts en 32 bits (deux registres, MSB puis LSB)

| Adresse (Dec) | Adresse (Hex) | Description                             | Unité | Mise à l'échelle                  | Format     | Notes                                           |
|---------------|---------------|-----------------------------------------|-------|-----------------------------------|------------|-------------------------------------------------|
| 21668-21669   | 0x54A4-0x54A5 | Conso Chauffage                         | kWh   | -                                 | 32 bits   |                                                 |
| 21670-21671   | 0x54A6-0x54A7 | Conso Chauffage Appoint                 | kWh   | -                                 | 32 bits   |                                                 |
| 21672-21673   | 0x54A8-0x54A9 | Conso Eau Chaude                        | kWh   | -                                 | 32 bits   |                                                 |
| 21674-21675   | 0x54A8-0x54A9 | Conso Eau Chaude Appoint                | kWh   | -                                 | 32 bits   |                                                 |
| 21676-21677   | 0x54AC-0x54AD | Conso Rafraîchissement                  | kWh   | -                                 | 32 bits   |                                                 |
| 21678-21679   | 0x54AE-0x54AF | Coût Chauffage                          | €     | Valeur / 100                      | 32 bits   | Coût total estimé                               |
| 21680-21681   | 0x54B0-0x54B1 | Coût Rafraîchissement                   | €     | Valeur / 100                      | 32 bits   | Coût total estimé                               |
| 21682-21683   | 0x54B2-0x54B3 | Coût Eau Chaude                         | €     | Valeur / 100                      | 32 bits   | Coût total estimé                               |


***

## 4. Registres particuliers

### Horloge (Date/heure)

Il est possible de définir la date et l'heure du T.one

| Adresse (Dec) | Adresse (Hex) | Description                                   | Format     | 
|---------------|---------------|-----------------------------------------------|------------|
| 16            | 0x0010        | Date                                          |16 bits   |
| 17            | 0x0011        | Heure                                         |16 bits   | 

#### Décodage Registre 16 - Date (16 bits)

| Bits  | 15-9     | 8-5     | 4-0    |
|-------|----------|---------|--------|
| **Champs** | Année   | Mois    | Jour   |
| **Masque** | `& 0x7F`| `>>5 & 0x0F` | `& 0x1F` |
| **Calcul** | `1980 + valeur` | - | - |

**Exemple :**

Registre 16 = `0x1A0C`
| Bits  | 15-9     | 8-5     | 4-0    |
|-------|----------|---------|--------|
| **Champs** | Année   | Mois    | Jour   |
| **Masque** | `& 0x7F`| `>>5 & 0x0F` | `& 0x1F` |
| **Binaire**| `0100110`| `1010`  | `01100`|
| **Hexa**   | `0x26`   | `0x0A`  | `0x0C` |
| **Décodé** | 1980+26=2006 | 10 (Oct) | 12 |



#### Décodage Registre 17 - Heure (16 bits)

| Bits     | 15-11   | 10-0              |
|----------|---------|-------------------|
| **Champs** | Heures | Secondes  |
| **Masque** | `>>11 & 0x1F` | `& 0x7FF`     |
| **Calcul** | -      | `valeur * 1.875` |

**Conversion valeur → minutes/secondes :**
- Total secondes = valeur * 1.875
- minutes = (total secondes / 60) % 60
- secondes = total secondes % 60
  
**Exemple :** 

Registre 17 = `0x1123` 
| Bits     | 15-11   | 10-0              |
|----------|---------|-------------------|
| **Champs** | Heures | Unités de temps  |
| **Masque** | `>>11 & 0x1F` | `& 0x7FF`     |
| **Binaire**| `00001` | `100100011`     |
| **Hexa**   | `0x01`  | `0x123`          |
| **Décodé** | 1h    | 291 × 1.875 = 546s (09min 06s) |

### Registres de Défauts

Ces registres contiennent des masques de bits où chaque bit actif correspond à un code défaut spécifique (format hexadécimal).

| Adresse (Dec) | Adresse (Hex) | Description      | Format | Notes |
|---------------|---------------|------------------|--------|-------|
| 20162         | 0x4EBE        | Défauts #1       | 16 BOOL| Bit 0-15 → codes défauts |
| 20163         | 0x4EBF        | Défauts #2       | 16 BOOL| Bit 0-15 → codes défauts |
| 20164         | 0x4EC0        | Défauts #3       | 16 BOOL| Bit 0-15 → codes défauts |
| 20165         | 0x4EC1        | Défauts #4       | 16 BOOL| Bit 0-15 → codes défauts |

#### Codes défaut

| Bits     | 15    | 14    | 13    | 12    | 11    | 10    | 9     | 8     | 7     | 6     | 5     | 4     | 3     | 2     | 1     | 0     |
|----------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|-------|
| **#1 (20162)** | 30    | 0     | 64    | 32    | 25    | 00    | 2D    | 2D    | 20    | 20    | 00    | B0    | 64    | 31     | 30    | 25     |
| **#2 (20163)** | 00    | 3F    | A3    | A3    | 00    | 24    | 00    | 73    | 6F    | 72    | 75    | 45    | 00    | 2F    | 33    | F1    |
| **#3 (20164)** | F0    | 7D    | 32    | 31    | 30    | 2E    | 2F    | 34    | 32    | 31    | 30    | 2E    | 2D    | 2C    | 2B    | 2A    |
| **#4 (20165)** | 29    | 28    | 27    | 26    | 25    | 24    | 23    | 22    | 21    | 20    | 16    | 08    | 76    | 06    | 05    | 75    |

**Utilisation :**
- Bit `n` = 1 → défaut actif avec code hexadécimal correspondant
- Chaque registre couvre 16 défauts différents


### Réinitialisation de l'Anode

Un registre permet de réinitialiser le compteur d'anode

| Adresse (Dec) | Adresse (Hex) | Description              | Action       | Notes                                                                 |
|---------------|---------------|--------------------------|--------------|-----------------------------------------------------------------------|
| 20279         | 0x4F27        | Commande de Reset Anode | Écrire 0x0003| Le registre 20279 doit être écrit avec la valeur 3 (0x0003) pour déclencher la réinitialisation de l'anode. |

### Mode Test

Il est possible de lancer/arrêter les modes de test du T.One à partir des registres 21000 et 21001.

| Adresse (Dec) | Adresse (Hex) | Description             |  Notes                                                                |
|---------------|---------------|-------------------------|-----------------------------------------------------------------------|
| 21000         | 0x5220        | Identifiant du test     | Voir le tableau ci-dessous                                            |
| 21001         | 0x5221        | Démarrage/Arret du test | 0 : lancer,  32753 : arrêt                                            |

#### Options de test disponibles

| Identifiant | Description         |
|-------------|---------------------|
| 0           | Aucun (arrêt)       |
| 2           | Tirage au vide      |
| 3           | Pump Down           |
| 4           | Chauffage           |
| 5           | Clim                |
| 6           | Eau chaude          |
| 8           | Appoint Air 1       |
| 9           | Appoint Air 2       |
| 10          | Appoint ECS         |
| 11          | Aéraulique          |

#### Test Chauffage/Clim

Pour lancer le test du chauffage ou clim, il faut définir des consignes de ventilateur et du compresseur.

D'après les tests, il faut attendre plusieurs conditions pour écrire les consignes:
- Registre 142 > 0 (4 constaté)
- Registre 30 et 35 = `2^(nb_canaux+1)-1` (Masque binaire "tous les bits à 1" pour nb_canaux+1 bits)

avec nb_canaux = nombre de thermostats (valeur registre 30026 : Nombre de canaux réglés)


| Adresse (Dec) | Adresse (Hex) | Description                         | Unité | Mise à l'échelle                  | Format     | Plage de Valeurs / Mapping                                                                                                                                     |
|---------------|---------------|-------------------------------------|-------|-----------------------------------|------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 21011             | 0x5213        | Consigne ventilateur UI TEST           | RPM   | Valeur / 10                       | 16 bits   | Vitesse cible du ventilateur [150-750].                                                                                                                          |
| 21013             | 0x5215        | Consigne compresseur TEST               | Hz    | Valeur / 10                       | 16 bits   | Fréquence cible du compresseur [30-83].         

### Vacances/Hors Gel

Il est possible d'activer le mode Vacances et Hors-gel à l'aide des registres 31008 à 31012.

La date et l'heure sont codées comme [l'horloge](#horloge-dateheure)

#### Mode Vacances

| Adresse (Dec) | Adresse (Hex) | Description                                   | Format     | 
|---------------|---------------|-----------------------------------------------|------------|
| 31008            | 0x7920     | Date début vacances                           |16 bits   |
| 31009            | 0x7921     | Heure début vacances                          |16 bits   | 
| 31010            | 0x7922     | Date fin vacances                             |16 bits   |
| 31011            | 0x7923     | Heure fin vacances                            |16 bits   | 

#### Mode Hors-gel

| Adresse (Dec) | Adresse (Hex) | Description                                   | Format     | 
|---------------|---------------|-----------------------------------------------|------------|
| 31008            | 0x7920     | Date courante                           |16 bits   |
| 31009            | 0x7921     | Heure courante                          |16 bits   | 
| 31010            | 0x7922     | 0                                       |16 bits   |
| 31011            | 0x7923     | 0                                       |16 bits   | 

#### Désactivation

| Adresse (Dec) | Adresse (Hex) | Description                                   | Format     | 
|---------------|---------------|-----------------------------------------------|------------|
| 31008            | 0x7920     | 0                                       |16 bits   |
| 31009            | 0x7921     | 0                                       |16 bits   | 
| 31010            | 0x7922     | 0                                       |16 bits   |
| 31011            | 0x7923     | 0                                       |16 bits   | 


### Réinitialisation des Consommations

Un registre permet de réinitialiser les compteurs de consommation

| Adresse (Dec) | Adresse (Hex) | Description                    | Action         | Notes                                                                 |
|---------------|---------------|--------------------------------|----------------|-----------------------------------------------------------------------|
| 31017         | 0x7991        | Commande de Reset Consommation | Écrire 0x0001  | Le registre 31017 doit être écrit avec la valeur 1 (0x0001) pour déclencher la réinitialisation des compteurs. |


