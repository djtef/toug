# T.One Ultimate Gateway (TOUG) : Passerelle ESPHome pour Aldes T.One® AIR/AquaAIR

**Attention :** cette page indépendente du fabricant présente des idées dont la mise en pratique nécessite des connaissances approfondies en chauffage, climatisation, électricité, électronique et informatique. Les risques sont nombreux et des erreurs graves figurent très probablement dans cette page. L'auteur se dégage de toute responsabilité liée à la mise en oeuvre de ce projet. N'utilisez pas ce projet, utilisez la passerelle officielle [AldesConnect® Box](https://www.aldes.fr/produits/mesure-regulation-et-connectivite/capteurs-et-connectivite/autres-capteurs/aldesconnect-box).

**Note :** un énorme merci à la [communauté d'HACF](https://forum.hacf.fr/t/aldes-t-one-air-aquaair/42974) dans laquelle nous travaillons sur le sujet depuis plus de 2 ans !

# Pilotage local d'une PAC Aldes T.One Aqua Air avec ESPHome + Home Assistant (sans cloud)

&#x20;&#x20;

![touga2](https://github.com/user-attachments/assets/59892eb9-fa53-43a9-8cca-c5437c7e87dd)

![touga](https://github.com/user-attachments/assets/6002d573-57a5-4d28-bc2e-dffc2f2c387c)


> Contrôlez votre pompe à chaleur *Aldes T.One® AIR/AquaAIR** entièrement en local via un **WT32‑ETH01** (ESP32 + Ethernet) et profitez d’une intégration immédiate dans **Home Assistant** – sans cloud ni passerelle propriétaire.

---

## Table des matières

1. [Fonctionnalités](#fonctionnalités)
2. [Matériel requis](#matériel-requis)
3. [Schéma de principe](#schéma-de-principe)
4. [Installation](#installation)
5. [Intégration Home Assistant](#intégration-home-assistant)
6. [Limitations](#limitations)
7. [License](#license)



---

## Fonctionnalités

### Communications supportées

* **Modbus utilisateur** (UART) : lecture des registres usuels (modes chauffage/ECS, thermostats, filtres, etc.).
* **Modbus écran central** (UART secondaire) : accès à des registres avancés normalement invisibles (températures des pièces, pressions du gaz, débits, etc.)
* **Interface série passerelle Aldes** (USB) : reverse‑engineering en cours pour remplacer la passerelle officielle AldesConnect® Box via l'USB
* **Extension I2C** : permet l'ajout de fonctionnalités via le bus I2C
* **Réseau** : Ethernet ou Wifi

### Surveillance & commandes

* Suivi de l’**ouverture jusqu'à 6 bouches de ventilation**.
* Détection de la **demande de résistance d’appoint** (AquaAIR seulement)
* Lecture des sondes **ECS haut & bas** (NTC 10 kΩ) (AquaAIR seulement)
* **Simulation** d’une température ECS de **60 °C** pour éviter l’erreur température trop haute lorsque l’eau est chauffée par un routeur solaire DIY (AquaAIR seulement) ⚠️ Voir [Simulation sondes](#5-simulation-sondes-60)

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

![image](https://github.com/user-attachments/assets/21f1d627-101c-4075-81c3-fc9126faa32d)


### 3. Jumpers

1. **Sélection 12V** : Sélectionne la source de l'alimentation 12V (Télécommande ou Modbus)
2. **VCC I2C** : Sélectionne la tension à envoyer sur le connecteur I2C (5V ou 3.3V).
3. **Sélection PullUp/PullDown** : Connecte une résistance en PullUp ou PullDown sur les entrées GPIO35, 36 et 39
4. **Ref Résistance d'appoint** : Envoie 3.3V ou GND en tant que référence d'entrée pour la détection de la résistance d'appoint (connecteur 9)
5. **Sélection 5V** : Sélectionne la source de l'alimentation 5V (USB ou conversion 12V)

### 4. Explication Routeur Solaire

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


### 5. Simulation sondes 60°

> Pour éviter que la PAC se mettre en erreur lorsque la température ECS dépasse 60°, il faut lui faire croire qu'elle ne dépasse pas 60° en simulant de fausses sondes de température NTC 10K.

Les sondes de température NTC sont des résistances variables en fonction de la température, ainsi, à chaque température correspond une résistance. Grâce à un diviseur de tension interne, la carte mère mesure la tension aux bornes des sondes, proportionnelles à la résistance et donc à la température qu'elle arrive à déduire.
La résistance qui correspond à 60° est  2.3 kΩ, il faut donc déconnecter les sondes de la carte mère et lui connecter une résistance de  2.3 kΩ. Mais il faut toujours connaitre les températures Haut et Bas ECS sur notre carte pour ne pas dépasser 80°. Ainsi il nous faut connecter les sondes sur notre passerelle avec notre propre diviseur de tension.
Pour résumer :
> En dessous de 60°, on peut se connecter en parallèle de la carte mère pour mesurer la tension aux bornes des sondes en partageant le diviseur de tension de la carte mère.
> AU dessus de 60°, on remplace les sondes par des résistance de 2.3 kΩ côté carte mère, et on continue à lire les sondes sur la passerelle avec un diviseur de tension propre à la passerelle.

Plusieurs solutions possibles :
* Remplacer les sondes côté carte mère par un potentiomètre numérique (par exemple un MCP4461 qui est disponible pour esphome)  piloté par la passerelle qui lit en permanence les sondes avec son porpre diviseur de tension.
* Utiliser un relais qui bascule les entrées de la carte mère soit vers les vraies sondes, soit vers des résistances de 2.3 kΩ en activant un diviseur de tension côté passerelle pour continuer à lire les valeurs des sondes.

Sur la V1.0 de la passerelle, **cette fonctionnalité n'est pas disponible** suite à une erreur de design, il faut réaliser donc une carte additionnelle en I2C (en utilisant les connecteurs MCP4728 et ADS1115).

De mon côté j'ai choisi la 2e solution pour corriger le problème.

Je fournirai un schéma de ma solution.

Une fois tout branché :

![touga3](https://github.com/user-attachments/assets/4aec945a-05a0-4683-b4c4-530ffbb46372)

---

## Intégration Home Assistant

Installer hass-template-climate dans HACS et ajouter la configuration Yaml

Exemple:
```yaml
climate:
  - platform: climate_template
    name: Séjour k1a
    unique_id: sejour_k1a_template
    icon_template: mdi:home-thermometer
    availability_template: "{{ is_state('sensor.esphome_aldes_aiguillage_vanne', 'Air') }}"
    modes:
      - "heat"
      - "cool"                              # Si la climatisation est activée
      - "off"
    hvac_mode_template: "{{ states('select.esphome_aldes_mode_air') }}"
    set_hvac_mode:
      - action: select.select_option
        data:
          entity_id: select.esphome_aldes_mode_air
          option: "{{ hvac_mode }}"
    # hvac_action_template: IDLE # Sans amélioration 2
    hvac_action_template: >- # Avec amélioration 2 # à adapter
      {% if (is_state('binary_sensor.esphome_aldes_sejour_bouche_k1a', 'on')) %}             
        {% if ( is_state('select.esphome_aldes_mode_air', 'heat')  
             or is_state('select.esphome_aldes_mode_air', 'Chauffage économique')
             or is_state('select.esphome_aldes_mode_air', 'Programme chauffage A')
             or is_state('select.esphome_aldes_mode_air', 'Programme chauffage B')) %} 
            heating
        {% elif (is_state('select.esphome_aldes_mode_air', 'cool')
              or is_state('select.esphome_aldes_mode_air', 'Climatisation boost')
              or is_state('select.esphome_aldes_mode_air', 'Programme climatisation C')
              or is_state('select.esphome_aldes_mode_air', 'Programme climatisation D')) %} 
            cooling
        {% else %} 
            idle
        {% endif %}
      {% else %} 
         off
      {% endif %}
    #current_humidity_template: "{{ states('sensor.aqara_temperature_et_humidite_thermostat_k1a_humidity') }}" # Avec amélioration 3
    current_temperature_template: "{{ states('sensor.esphome_aldes_sejour_temperature_k1a') }}"      # à adapter
    min_temp_template: >-
      {## 0°C est là pour pouvoir eteindre les thermostats muraux   ##} 
      {% if (   is_state('select.esphome_aldes_mode_air', 'heat')  
             or is_state('select.esphome_aldes_mode_air', 'Chauffage économique')
             or is_state('select.esphome_aldes_mode_air', 'Programme chauffage A')
             or is_state('select.esphome_aldes_mode_air', 'Programme chauffage B')) %} 
         0              {##  16°C réél mini pour le mode chauffage  ##} 
      {% else %} 
         0              {## 22°C réél mini pour le mode climatisation ##} 
      {% endif %}
    max_temp_template: >-
      {% if ( is_state('select.esphome_aldes_mode_air', 'heat')  
           or is_state('select.esphome_aldes_mode_air', 'Chauffage économique')
           or is_state('select.esphome_aldes_mode_air', 'Programme chauffage A')
           or is_state('select.esphome_aldes_mode_air', 'Programme chauffage B')) %} 
          24
       {% elif  (is_state('select.esphome_aldes_mode_air', 'cool')
              or is_state('select.esphome_aldes_mode_air', 'Climatisation boost')
              or is_state('select.esphome_aldes_mode_air', 'Programme climatisation C')
              or is_state('select.esphome_aldes_mode_air', 'Programme climatisation D')) %}
           31
      {% else %} 
          24        {##  pas vraiment besoin  ##} 
      {% endif %}
    target_temperature_template: "{{ states('number.esphome_aldes_sejour_thermostat_k1a') }}"        # à adapter
    set_temperature:
      - action: number.set_value
        data:
          entity_id: number.esphome_aldes_sejour_thermostat_k1a                                      # à adapter
          value: "{{ temperature }}"
```
Voici le résultat :

![climate1](/doc/images/climate1.png)

![climate2](/doc/images/climate2.png)

![climate3](/doc/images/climate3.png)

---

## Limitations

* La simulation des sondes à 60° n'est pas fonctionnelle Voir [Simulation sondes](#5-simulation-sondes-60)
* Le connecteur femelle de la télécommande (2)  est inversé, on ne peut pas y brancher directement le connecteur mâle de la télécommande.
* Bien que théoriquement on puisse connecter directement les connecteurs mâles des sondes et de la télécommande sur les connecteurs femelles de la passerelle, en réalité ce n'est pas forcément pratique car les câbles d'origine sont courts et ça empêche de mettre la passerelle où on veut. Il est plus jusdicieux de connecter un cable avec un connecteur femelle en guise de ralonge jusqu'à la passerelle.

---

## License

Distributed under the **MIT License**. See `LICENSE` for more information.

---

🛠️ **Contributions bienvenues !**
*Forkez, étoilez, proposez vos idées – la communauté fera le reste.*
