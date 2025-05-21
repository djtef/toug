# T.One Ultimate Gateway (TOUG) : Passerelle ESPHome pour Aldes T.One® AIR/AquaAIR

**Attention :** cette page indépendente du fabricant présente des idées dont la mise en pratique nécessite des connaissances approfondies en chauffage, climatisation, électricité, électronique et informatique. Les risques sont nombreux et des erreurs graves figurent très probablement dans cette page. L'auteur se dégage de toute responsabilité liée à la mise en oeuvre de ce projet. N'utilisez pas ce projet, utilisez la passerelle officielle [AldesConnect® Box](https://www.aldes.fr/produits/mesure-regulation-et-connectivite/capteurs-et-connectivite/autres-capteurs/aldesconnect-box).

**Note :** un énorme merci à la [communauté d'HACF](https://forum.hacf.fr/t/aldes-t-one-air-aquaair/42974) dans laquelle nous travaillons sur le sujet depuis plus de 2 ans !

# Pilotage local d'une PAC Aldes T.One Aqua Air avec ESPHome + Home Assistant (sans cloud)

&#x20;&#x20;

> Contrôlez votre pompe à chaleur *Aldes T.One® AIR/AquaAIR** entièrement en local via un **WT32‑ETH01** (ESP32 + Ethernet) et profitez d’une intégration immédiate dans **Home Assistant** – sans cloud ni passerelle propriétaire.

---

## Table des matières

1. [Fonctionnalités](#fonctionnalités)
2. [Matériel requis](#matériel-requis)
3. [Schéma de principe](#schéma-de-principe)
4. [Installation](#installation)
5. [Intégration Home Assistant](#intégration-home-assistant)
6. [Roadmap](#roadmap)
7. [License](#license)

---

## Fonctionnalités

### Communications supportées

* **Modbus utilisateur** (UART) : lecture des registres usuels (modes chauffage/ECS, thermostats, filtres, etc.).
* **Modbus écran central** (UART secondaire) : accès à des registres avancés normalement invisibles (températures des pièces, pressions du gaz, débits, etc.)
* **Interface série passerelle Aldes** (USB) : reverse‑engineering en cours pour remplacer la passerelle officielle AldesConnect® Box via l'USB
* **Extension I2C** : permet l'ajout de fonctionnalités via le bus I2C

### Surveillance & commandes

* Suivi de l’**ouverture jusqu'à 6 bouches de ventilation**.
* Détection de la **demande de résistance d’appoint** (AquaAIR seulement)
* Lecture des sondes **ECS haut & bas** (NTC 10 kΩ) (AquaAIR seulement)
* **Simulation** d’une température ECS de **60 °C** pour éviter l’erreur température trop haute lorsque l’eau est chauffée par un routeur solaire DIY (AquaAIR seulement) /!\ Voir ci-après

### Alimentation

* **Port USB** de la passerelle AldesConnect® Box
* **Modbus utilisateur** : conversion du 12V du connecteur modbus utilisateur
* **Modbus écran central** (télécommande) : conversion du 12V du connecteur modbus télécommande
* **5V externe** : via une alimentation séparée

### Configurations

* **Switch ON/OFF** en façade
* **Switch Flash Mode** en façade avec connecteur FTDI
* **Choix de l'UART** 1 à 3 ou désactivation du port USB via interrupteur à glissière 4 positions
* **Choix de l'alimentation** via jumpers
* **Activation/Désactivation** de la lecture de l'écran central via interrupteur à glissière
* **Activation/Désactivation** de la simulation des températures ECS haut et bas via interrupteur à glissière (AquaAIR seulement)
* **Détection résistance d'appoint** avec pullup ou pulldown via jumper

### Connectique

* **Borniers à vis débrochables 2.54mm** pour les bouches de ventilation et résistance d'apoint
* **Borniers à vis débrochables 5.08mm** pour alimentations externes 5V et 3.3V
* **Connecteur femelle JST XAP-04V** pour brancher directement la télécommande
* **Connecteur femelle JST XAP-06V** pour brancher directement les sondes ECS haut et bas (AquaAIR seulement)
* **Connecteurs femelles JST XH 2.54** pour liaisons vers carte mère (sondes et modbus) et bus I2C
* **Port Ethernet** RJ45

### Boitier

* Boitier générique au format **rail DIN**
* **Ouverture et fermeture** via clips
* **Fixation** de la carte par vis
---

## Matériel requis

| Quantité | Composant                  | Rôle                                                      |
| -------- | -------------------------- | --------------------------------------------------------- |
| 1        | **WT32‑ETH01**             | ESP32 + Ethernet                                          |
| 2        | **TTL to RS485**           | Convertisseur RS485/TTL                                   |
| 1        | **Level shifter**          |Convertisseur de niveau logique bidirectionnel 5V <=> 3.3V |
| 1        | **Step-Down Power Module** | Convertisseur 12V => 5V (régler la sortie au multimètre sur 5V avant de le brancher au reste)|
| 2        | **Logic Level Converter PNP Output**  | Convertisseur numérique de niveaux logiques 12V => 3.3V à 4 voies|
| 1        | **ADS1115**                | Convertisseur Analogique-Numérique 4 voies                |
| 1        | **MCP2221-I/SL**           | Convertisseur USB/TTL CDC                                 |
| 1        | **4.7 µF**                 | Condensateur VDD MCP2221                                  |
| 1        | **0.47 µF**                | Condensateur VUSB MCP2221                                 |
| 1        | **10 µF**                  | Condensateur alimentation 5V                              |
| 1        | **100 nF**                 | Condensateur découplage 5V                                |
| 3        | **4.7 kΩ**                 | Résistances pull up                                       |
| 2        | **SK-12D02-VG7**           | Interrupteurs à glissière en façade                       |
| 2        | **KH-SS42D02-G3**          | Interrupteurs à glissière 4PDT                            |
| 1        | **SS-24H03-G070**          | Interrupteurs à glissière DP4T                            |
| 1        | **Port USB**               | Port USB 4 pins                                           |
| 1        | **KF2EDGR-2.54-8P**        | Bornier à vis débrochable 8 pins 2.54mm                   |
| 1        | **KF2EDGR-2.54-2P**        | Bornier à vis débrochable 2 pins 2.54mm                   |
| 2        | **KF2EDGR-5.08-2P**        | Bornier à vis débrochable 2 pins 5.08mm                   |
| 1        | **S06B-XASK-1(LF)(SN)**    | Connecteur JST XASK femelle 6 pins sondes température     |
| 1        | **S04B-XASK-1(LF)(SN)**    | Connecteur JST XASK femelle 4 pins télécommande           |
| 1        | **KF2EDGR-2.54-8P**        | Bornier à vis débrochable 8 pins 2.54mm                   |
| 4        | **JST XH2.54 4P**          | Connecteur JST XH 4 pins 2.54mm                           |
| 5        | **Pin header 3P**          | Pin Header 3 pins 2.54mm                                  |
| 5        | **Jumper**                 | Jumpers                                                   |

> 📋 **Astuce** : Avec un WT32‑ETH01, vous bénéficiez d’un port RJ45 natif – idéal pour une communication fiable dans une chaufferie blindée.

---

## Schéma de principe

```
WT32‑ETH01
├── UART1 ──────► Modbus utilisateur (PAC)
├── UART2 ──────► Modbus écran central (PAC)
├── USB‑Serial ─► USB passerelle Aldes (en analyse)
├── Sortie GPIO ─► Simulation 60°
├── Entrées GPIO ◄─ 6 bouches de ventilation
├── Entrée GPIO ◄── Demande résistance d’appoint  
├── ADC1 ◄───────── Sonde ECS haut (NTC)
├── ADC1 ◄───────── Sonde ECS bas (NTC)

```

*(Vous pouvez ajouter un vrai schéma KiCad ou Fritzing dans le dossier **`/doc`** puis l’intégrer ici avec une image.)*

---

## Installation

### 1. Flasher ESPHome

```bash
esphome run toug.yaml
```

* Le fichier **toug.yaml** est fourni dans */esphome*.
* Renseignez vos paramètres réseau (IP statique conseillée).

### 2. Câblage

1. **Modbus** :Connecteur femelle « Modbus » de la carte mère
2. **Télécommande** : Connecteur mâle de la télécommande (écran central).
3. **Remote** : Connecteur « Remote » de la carte mère
4. **Entrée sondes** : Connecteur mâle des sondes de température ECS
5. **Tank** : Connecteur femelle « Tank » de la carte mère
6. **USB** : Branchez au port USB de la passerelle Aldes
7. **I²C** : Extension I2C (Optionnel)
8. **K1+/K1- à K5+/K5-** : Borniers bouches
9. **RA/K6** : Résistance Appoint ou Bornier bouche K6 selon jumper

> ⚠️ **Sécurité** : Coupez l’alimentation de la PAC avant toute intervention. Vérifiez la continuité des sondes après câblage.

![image](https://github.com/user-attachments/assets/04e49a60-2b5f-459d-9bbd-449cbecf594d)


### 3. Explication Routeur Solaire

> **Prérequis** : Le routeur doit être activable et désactivable à distance par ESPhome de la passerelle ou Home Assistant.

Il est possible de piloter la résistance d'appoint ECS par un routeur solaire (Aldes T.One® AquaAIR uniquement)

Dans ce cas, ce n'est plus la carte mère qui pilote directement la résistance d'appoint mais le routeur.
La résistance d'appoint sert normalement à monter la température de 50° à 60° pour le cycle anti légionellose ou lorsque la PAC ne suffit pas lors de grand froid.
Il est donc important de garder ce mode de fonctionnement en plus du routeur solaire.

Avec le routeur solaire, on peut autoriser le ballon à monter plus haut en température (max 80°, au delà la sécurité thermique à réarmement manuel Kt°1 se déclenche).

**Fonctionnement** : Le routeur chauffe l'eau en fonction du surplus non consommé par la maison.
>Si la température dépasse 80° (sonde haut, bas, ou température ECS du modbus), alors on désactive le routeur jusqu'à redescendre sous 79°.
> > Quand la température dépasse 60°, la PAC se met en erreur "Température ECS trop haute", ainsi il faut lui faire croire que la température est à 60¨° 
> > Pour celà on bascule la carte mère vers des "fausses" sondes NTC : des résistances de 2.3 kΩ représentant 60°

>Si la carte mère demande à activer la résistance d'appoint, alors on force le routeur à 100%, jusqu'à que la carte mère arrête.
> > Pour détecter la demande, on remplace la phase de la résistance d'appoint sur le connecteur "AUX Heater" de carte mère par 3.3V (ou GND) et on lit la valeur du connecteur de la résistance d'appoint ("TANK HW") : si le relais s'active on reçoit 3.3V (ou GND)

>Si en fin de journée la température ECS dépasse la consigne, la PAC ne se mettra pas en route pour chauffer l'eau, sinon elle complètera le routeur.

---

## Intégration Home Assistant

Une fois flashé, l’ESP32 apparaît automatiquement via l’intégration **ESPHome**.
Toutes les entités (températures, modes, états, relais, etc.) sont créées.
Exemples d’automatisations :

```yaml
alias: Activer simulation ECS si dépasse 58 °C
trigger:
  - platform: numeric_state
    entity_id: sensor.ecs_haut
    above: 58
action:
  - service: switch.turn_on
    target:
      entity_id: switch.simulation_ecs
```

---

## Roadmap

*

---

## License

Distributed under the **MIT License**. See `LICENSE` for more information.

---

🛠️ **Contributions bienvenues !**
*Forkez, étoilez, proposez vos idées – la communauté fera le reste.*
