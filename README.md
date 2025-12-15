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
* **Modbus écran central** (UART secondaire) : accès à des registres avancés normalement invisibles (températures des pièces, pressions du gaz, débits, etc.) [Voir documentation modbus télécommande](https://github.com/djtef/toug/blob/main/doc/telecommande.md)
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

### 1. Câblage

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


### 2. Jumpers

1. **Sélection 12V** : Sélectionne la source de l'alimentation 12V (Télécommande ou Modbus)
2. **VCC I2C** : Sélectionne la tension à envoyer sur le connecteur I2C (5V ou 3.3V).
3. **Sélection PullUp/PullDown** : Connecte une résistance en PullUp ou PullDown sur les entrées GPIO35, 36 et 39
4. **Ref Résistance d'appoint** : Envoie 3.3V ou GND en tant que référence d'entrée pour la détection de la résistance d'appoint (connecteur 9)
5. **Sélection 5V** : Sélectionne la source de l'alimentation 5V (USB ou conversion 12V)

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


### 4. Simulation sondes 60°

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

## Installer ESPHome

La méthode la plus simple c'est de passer par Home Assistant avec l'addon [ESPhome Device Builder](https://esphome.io/guides/getting_started_hassio/) :
* Cliquer sur NEW DEVICE, puis Continue et New Device Setup
* Entrer un nom, par exemple TOUG AIR
* Choisir ESP32 et faire SKIP
* Cliquer sur EDIT sur le composant créé
* Effacer le contenu et le remplacer par le contenu du fichier [template.yaml](esphome/template.yaml) 
* Suivre les 5 étapes :
1. `TOne:` `air` pour un T.ONE AIR ou `aquaair` pour un T.ONE AquaAIR
2. `nombre_thermostats:` Définir le nombre de thermostats associés au T.One (entre 1 et 9)
3. (optionnel) Décommenter `name:` et/ou `description:` pour remplacer les valeurs par défaut
4. (optionnel) Décommenter les cannaux `K1a` à `K8` pour personnaliser leur nom (par pièces par exemple)
5. Décommenter la configuration à télécharger
* Cliquer sur INSTALL
  ![toug](https://github.com/user-attachments/assets/33ecf527-f942-4d90-9b1f-deb57fe7c4df)

La deuxième méthode consiste à utiliser esphome depuis un terminal et suivre les mêmes étapes à partir du [template.yaml](esphome/template.yaml) 

---

## Intégration Home Assistant

### Ajouter un thermostat
Installer hass-template-climate dans HACS et ajouter la configuration Yaml

Exemple:
```yaml
climate:
  - platform: climate_template
    name: Séjour
    unique_id: sejour_k1a_climate_template
    icon_template: mdi:home-thermometer
    availability_template: >- 
        {{ is_state('sensor.aiguillage_vanne', 'Air') 
        or  is_state('sensor.aiguillage_vanne', 'Standby')
        or  is_state('sensor.aiguillage_vanne', 'ECS') }}
    modes:
      - "heat"
      - "cool"  # Si la climatisation est activée
      - "off"
    preset_modes:
      - comfort
      - eco
      - away
      - home
      - boost
      - none
    hvac_mode_template: >-
      {% if 'Chauffage' in states('select.mode_air') %} 
          heat
      {% elif 'Clim' in states('select.mode_air') %} 
          cool
      {% else %} 
          off      
      {% endif %}
    preset_mode_template: >-
      {% if is_state('select.mode_air', 'Off') %}
        none
      {% elif 'Confort' in states('select.mode_air') %}
        comfort
      {% elif 'Prog A' in states('select.mode_air') or 'Prog C' in states('select.mode_air') %}
        away
      {% elif 'Prog B' in states('select.mode_air') or 'Prog D' in states('select.mode_air') %}
        home
      {% elif 'Eco' in states('select.mode_air') %}
        eco
      {% elif 'Boost' in states('select.mode_air') %}
        boost
      {% else %}
        none
      {% endif %}
    set_preset_mode:
      - action: select.select_option
        data:
          entity_id: select.mode_air
          option: >-
            {% if 'Chauffage' in states('select.mode_air') %} 
                {% if preset_mode == 'none' %}
                    Off
                {% elif preset_mode == 'comfort' or preset_mode == 'boost' %}
                    Confort Chauffage
                {% elif preset_mode == 'away' %}
                   Prog A Chauffage
                {% elif preset_mode == 'home' %}
                   Prog B Chauffage
                {% elif preset_mode == 'eco' %}
                   Eco Chauffage
                {% endif %}
            {% elif 'Clim' in states('select.mode_air') %} 
              {% if preset_mode == 'none' %}
                    Off
                {% elif preset_mode == 'comfort' or preset_mode == 'eco' %}
                    Confort Clim
                {% elif preset_mode == 'away' %}
                   Prog C Clim
                {% elif preset_mode == 'home' %}
                   Prog D Clim
                {% elif preset_mode == 'boost' %}
                   Boost Clim
                {% endif %}
            {% else %}
              Off
            {% endif %}
    set_hvac_mode:
      - action: select.select_option
        data:
          entity_id: select.mode_air
          option: >-
            {% if hvac_mode == 'heat' %}
              Confort Chauffage
            {% elif hvac_mode == 'cool' %}
              Confort Clim
            {% elif hvac_mode == 'off' %}
              Off
            {% else %}
              Off
            {% endif %}
    hvac_action_template: >-
      {% if is_state('binary_sensor.canal_k1a', 'off') %}
        off
      {% elif 'Chauffage' in states('select.mode_air') %}
        heating
      {% elif 'Clim' in states('select.mode_air') %}
        cooling
      {% else %}
        idle
      {% endif %}

    #current_humidity_template: "{{ states('sensor.aqara_temperature_et_humidite_thermostat_k1a_humidity') }}" # Avec capteur additionnel
    temp_step: 1
    current_temperature_template: "{{ states('sensor.temperature_piece_principale') }}"     
    min_temp: 0
    max_temp_template: >-
      {% if 'Chauffage' in states('select.mode_air') %} 
          24
      {% elif 'Clim' in states('select.mode_air') %} 
           31
      {% else %} 
          24        {##  pas vraiment besoin  ##} 
      {% endif %}
    target_temperature_template: "{{ states('number.thermostat_k1a') }}" 
    set_temperature:
      - action: number.set_value
        data:
          entity_id: number.thermostat_k1a  
          value: "{{ temperature }}"
```
Voici le résultat :

![climate1](/doc/images/climate1.png)

![climate2](/doc/images/climate2.png)

![climate3](/doc/images/climate3.png)

### Ajouter la programmation horaire

Avec la télécommande du T.One il est possible de définir heure par heure si la clim ou le chauffage doit être activé ou en éco (off pour la clim). Il existe 2 programmes pour le chauffage (A et B), et 2 pour la clim (C et D)
<img width="652" height="516" alt="image" src="https://github.com/user-attachments/assets/4d1751d2-d0e8-4494-b2b6-2d8e93c67d29" />


#### Integration dans ESPHome
La programmation horaire a été intégrée dans la TOUG :
- En lecture
  - Au format texte
  ```
  Lundi : 7h-9h, 17h-22h
  Mardi : 7h-9h, 17h-22h
  Mercredi : 7h-9h, 17h-22h
  Jeudi : 7h-9h, 17h-22h
  Vendredi : 7h-9h, 17h-22h
  Samedi : 8h-22h
  Dimanche : 8h-22h
  ```
  - En représentation hexa du contenu des registres (voir [explications](doc/telecommande.md#21-registres-de-programmation-horaire-prog-a-b-c-d))
    
  `003e0180003e0180003e0180003e0180003e0180003fff00003fff00`
- En écriture via l'api
  ```yaml
      - action: change_prog
        variables:
          prog: string
          dataset: string
  ```
  - prog : A, B, C ou D
  - dataset : représentation hexa du contenu des registres

#### Integration dans Home Assistant 
Côté Home Assistant, une implémentation possible est d'utiliser la custom card [button-card](https://github.com/custom-cards/button-card)

**Note :** dans cet exemple le nom esphome de la TOUG est `toug_aquaair`, les entités commencent donc par `toug_aquaair_`, à remplacer selon les cas.

Tout le code yaml présenté ici est dans le dossier [ha](ha).

1. Créer un script [envoie_prog](ha/scripts.yaml) pour synchroniser la programmation horaire entre Home Assistant et Esphome (dans les deux sens)
```yaml
description: Envoie la programmation horaire au T.One
fields:
  prog:
    description: Nom de la programmation (A,B,C ou D)
    example: A
    selector:
      select:
        options:
          - A
          - B
          - C
          - D
    required: true
  direction:
    description: Direction de la synchro
    example: esphome_vers_ha
    default: esphome_vers_ha
    required: true
    selector:
      select:
        options:
          - esphome_vers_ha
          - ha_vers_esphome
sequence:
  - variables:
      buffer_entity: "{{ 'input_text.prog' ~ prog | lower ~ '_buffer' }}"
      dataset_entity: "{{ 'sensor.toug_aquaair_progr_' ~ prog | lower ~ '_dataset' }}"
      buffer: "{{ states(buffer_entity) }}"
      dataset: "{{ states(dataset_entity) }}"
  - if:
      - condition: template
        value_template: "{{ direction == 'ha_vers_esphome' }}"
    then:
      - action: esphome.toug_aquaair_change_prog
        data:
          prog: "{{ prog }}"
          dataset: "{{ states(buffer_entity) }}"
    else:
      - action: input_text.set_value
        target:
          entity_id: "{{ buffer_entity }}"
        data:
          value: "{{ states(dataset_entity) }}"
alias: envoie_prog
```

2. Créer 4 entrées "saisie de texte" via l'IHM ou en [yaml](ha/helpers.yaml) (helpers input_text) qui seront la représentation de l'IHM en hexa pour chaque programme
```yaml
input_text:
  progA_buffer:
    name: progA_buffer
    min: 56
    max: 56
    pattern: "[0-9A-Fa-f]+"
    mode: text
  progB_buffer:
    name: progB_buffer
    min: 56
    max: 56
    pattern: "[0-9A-Fa-f]+"
    mode: text
  progC_buffer:
    name: progC_buffer
    min: 56
    max: 56
    pattern: "[0-9A-Fa-f]+"
    mode: text
  progD_buffer:
    name: progD_buffer
    min: 56
    max: 56
    pattern: "[0-9A-Fa-f]+"
    mode: text
```
3. Créer 4 entrées Template Capteur via l'IHM ou en [yaml](ha/helpers.yaml) (sensor template) qui représenteront l'état de la synchro entre ESPHome et Home Assistant :
   - synchro : Home Assistant et ESPhome sont synchronisés
   - maj : Mise à jour en cours, attente retour ESPHome
   - dataset : ESPhome a été mis à jour
   - buffer : Home Assistant est en train de changer la valeur via l'IHM
```yaml
template:
  - sensor:
      - name: "proga_synchro"
        state: >
          {% if states('input_text.proga_buffer') == states('sensor.toug_aquaair_progr_a_dataset') %}
          synchro
          {% elif  states.input_text.proga_buffer.last_changed > states.sensor.toug_aquaair_progr_a_dataset.last_changed %}
          {% if  states.input_text.proga_buffer.last_changed > state_attr('script.envoie_prog','last_triggered') %}
          buffer
          {% else %}
          maj
          {% endif %}
          {% else %}
          dataset
          {% endif %}
      - name: "progb_synchro"
        state: >
          {% if states('input_text.progb_buffer') == states('sensor.toug_aquaair_progr_b_dataset') %}
          synchro
          {% elif  states.input_text.progb_buffer.last_changed > states.sensor.toug_aquaair_progr_b_dataset.last_changed %}
          {% if  states.input_text.progb_buffer.last_changed > state_attr('script.envoie_prog','last_triggered') %}
          buffer
          {% else %}
          maj
          {% endif %}
          {% else %}
          dataset
          {% endif %}
      - name: "progc_synchro"
        state: >
          {% if states('input_text.progc_buffer') == states('sensor.toug_aquaair_progr_c_dataset') %}
          synchro
          {% elif  states.input_text.progc_buffer.last_changed > states.sensor.toug_aquaair_progr_c_dataset.last_changed %}
          {% if  states.input_text.progc_buffer.last_changed > state_attr('script.envoie_prog','last_triggered') %}
          buffer
          {% else %}
          maj
          {% endif %}
          {% else %}
          dataset
          {% endif %}   
      - name: "progd_synchro"
        state: >
          {% if states('input_text.progd_buffer') == states('sensor.toug_aquaair_progr_d_dataset') %}
          synchro
          {% elif  states.input_text.progd_buffer.last_changed > states.sensor.toug_aquaair_progr_d_dataset.last_changed %}
          {% if  states.input_text.progd_buffer.last_changed > state_attr('script.envoie_prog','last_triggered') %}
          buffer
          {% else %}
          maj
          {% endif %}
          {% else %}
          dataset
          {% endif %}      
```
4. Créer une entrée liste déroulante (via IHM) ou un input_select par yaml:
```yaml
input_select:
  programmation_horaire:
    name: Programmation horaire
    options:
      - Chauffage Progr. A
      - Chauffage Progr. B
      - Rafraîchissement Progr. C
      - Rafraîchissement Progr. C
    initial: Chauffage Progr. A
    icon: mdi:calendar-blank-multiple
```
5. Créer une automatisation [Synchro prog T.One](ha/automations.yaml)
```yaml
  alias: Synchro prog T.One
  description: Met à jour la programmation horaire du T.One vers Home Assitant
  triggers:
  - trigger: state
    entity_id:
    - sensor.proga_synchro
    to: dataset
    id: A
  - trigger: state
    entity_id:
    - sensor.progb_synchro
    to: dataset
    id: B
  - trigger: state
    entity_id:
    - sensor.progc_synchro
    to: dataset
    id: C
  - trigger: state
    entity_id:
    - sensor.progd_synchro
    to: dataset
    id: D
  actions:
  - action: script.envoie_prog
    metadata: {}
    data:
      prog: '{{ trigger.id }}'
      direction: esphome_vers_ha
    alias: Copie dataset ESPHome vers buffer HA
  mode: single
```

6. Ajouter une vue au dashboard en copiant le contenu de [dashboard_prog.yaml](ha/dashboard_prog.yaml) dans l'éditeur de configuration du dashboard de Home Assistant:
<img width="265" height="238" alt="image" src="https://github.com/user-attachments/assets/fd5d49ec-d8bd-496c-b97f-57ceab0bc328" />

On obtient ainsi un équivalent de la programmation horaire de la télécommande directement dans Home Assistant qu'on peut synchroniser avec ESPHome et donc le T.One
![proga_fin](https://github.com/user-attachments/assets/20f86a33-8761-4ac3-a50d-f0f8f6cab2fa)


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
