# Passerelle ESPHome pour Aldes T.One® AIR

## **Disclaimer** 
Cette page indépendente du fabricant présente des idées dont la mise en pratique nécessite des connaissances approfondies en chauffage, climatisation, électricité, électronique et informatique. Les risques sont nombreux et des erreurs graves figurent très probablement dans cette page. L'auteur se dégage de toute responsabilité liée à la mise en oeuvre de ce projet. N'utilisez pas ce projet, utilisez la passerelle officielle [AldesConnect® Box](https://www.aldes.fr/produits/mesure-regulation-et-connectivite/capteurs-et-connectivite/autres-capteurs/aldesconnect-box). Documentation [esphome](https://esphome.io/)


**Il est fortement conseillé de travailler hors tension.**

![danger](https://github.com/user-attachments/assets/dec92670-5e23-4b2b-b3bc-45dc3fbba967)

## Objectif
**Ce projet expérimental permet de piloter un système Aldes T.One® AIR via le réseau local et propose en particulier une intégration poussée dans Home Assistant sous la forme d'entités Climate pour chaque thermostat.**

### Fonctionnalité principales:
+  Afficher et modifier les consignes de l'ensemble des thermostats
+  Afficher les tempérratures des piéces
+  Connaitre l'état (ouvert/fermé) des bouches de diffusion ( 6 max )
+  Création d'entités Climate dans Home Assistant regroupant l'ensemble des entités ESPHome créées

![climate1](/V2/src/images/ha/thermostat%20séjour%20k1a-1.png)

![climate2](/V2/src/images/ha/thermostat%20séjour%20k1a-2.png)

![climate3](/V2/src/images/ha/thermostat%20séjour%20k1a-3.png)


## PCB
+ Récupérer le [fichier Gerber Switch](/V2/src/PCB/EasyEAD/Gerber_ESPHome-Aldes-T.One-advanced_switch.zip) ou [fichier Gerber Jumper](/V2/src/PCB/EasyEAD/Gerber_ESPHome-Aldes-T.One-advanced_jumper.zip) et le faire fabriquer sur un site comme [JLCPCB.com](https://jlcpcb.com/)
+ Souder l'ensemble des composants en surface

Projet réalisé avec EasyEAD ([fichier source](/V2/src/PCB/EasyEAD/SCH_ESPHome-Aldes-T.One-V2.json))


## Composants électroniques
+ D1 mini ESP32 ([AliExpress](https://fr.aliexpress.com/item/1005005972627549.html)) ou ([Amazon](https://amzn.eu/d/dTeepAy))
+ 2 x Convertisseur RS485/TTL ([AliExpress](https://fr.aliexpress.com/item/1005006340391490.html)) ([Amazon](https://amzn.eu/d/6Duje2j)) 
+ Convertisseur de niveau logique bidirectionnel 5V <=> 3.3V ([AliExpress](https://fr.aliexpress.com/item/1005006068381598.html)) ou ([Amazon](https://amzn.eu/d/aiCa1Ww))
+ Convertisseur 12V => 5V ([AliExpress](https://fr.aliexpress.com/item/1005006486270630.html)) ou ([Amazon](https://amzn.eu/d/aN7AZQ7)) : penser à régler la sortie sur 5V avant de le brancher au reste ! (multimétre)
+ Convertisseur numérique de niveaux logiques 12V => 3.3V à 4 voies ([AliExpress](https://fr.aliexpress.com/item/1005006419972546.html)) : Pour les bouches de diffusion
+ 3 x Mini interrupteur 1P2T SPDT (> 2A) [AliExpress](https://a.aliexpress.com/_EyLZ1bw) ou ([Amazon](https://amzn.eu/d/39LWGet))
+ 3 x Jumper de connexion ([AliExpress](https://a.aliexpress.com/_EQpgm7m)) ou ([Amazon](https://amzn.eu/d/83UZLdL))
  
## Connectique :
+ Câbles de connexion ([Amazon](https://amzn.eu/d/2ajQLdu))
+ 3 x Terminal 4 bornes ([AliExpress](https://fr.aliexpress.com/item/32828459901.html)) ou [Amazon](https://amzn.eu/d/fVjQnfr)
+ 6 x Terminal 2 bornes ([AliExpress](https://fr.aliexpress.com/item/32828459901.html)) ou [Amazon](https://amzn.eu/d/fVjQnfr)
+ Headers mâles ([AliExpress](https://fr.aliexpress.com/item/32973181162.html)) ou [Amazon](https://amzn.eu/d/hxtYfb3)
+ Headers femelle doubles (2) pour l'ESP32 ([AliExpress](https://fr.aliexpress.com/item/32747224548.html)) ou [Amazon](https://amzn.eu/d/7DIb0WQ) 
+ Headers femelle simples pour les autres circuits ([AliExpress](https://fr.aliexpress.com/item/32854239374.html)) ou [Amazon](https://amzn.eu/d/gex7kYw)
+ Headers mâle et femelle pour les autres circuits ([AliExpress](https://fr.aliexpress.com/item/1005007235591794.html)
+ Embouts doubles ([AliExpress](https://fr.aliexpress.com/item/1005004846852618.html))
+ Pince à sertir et embouts ([AliExpress](https://fr.aliexpress.com/item/32831768783.html)) ou [Amazon](https://amzn.eu/d/3J0eNm9)

## Autre
+ Kit fer à souder
+ Multimétre

## Explication
+  Le terminal à 4 bornes ("Modbus 1 IN" du PCB) doit être connecté au port modbus de la carte mère

![carte mère](/V2/src/images/PAC/modbus_carte_mere.png).

+  Le terminal à 4 bornes ("Modbus Remote IN (option)" du PCB) doit être connecté au port "Remote" de la carte mère via le câble de la commande centrale, et donc déconnecté de la commande centrale.

![commande centrale](/V2/src/images/PAC/remote.png).

+  Le terminal à 4 bornes ("Remote OUT" du PCB) doit être connecté à la commande centrale pour permettre son allumage simplement à l'aide des switches ou des jumpers, sans nécessiter de démontage. Par défaut, la commande centrale reste éteinte.

+ Si les switches sont en position basse (ou les jumpers), les températures des pièces ne seront plus remontées dans ESPHome et Home Assistant. Il ne peut y avoir qu’un seul maître en Modbus, soit l’ESP, soit la commande centrale, sinon cela entraîne un conflit.

Les terminaux à 2 bornes "K*" sont à relier aux terminaux correspondants sur la carte mère de la PAC, où sont déjà branchés les câbles menant aux bouches. Attention, la polarité n'est pas importante pour les vérins des bouches selon la documentation officielle, mais elle est importante ici. Assurez-vous d'obtenir +12V au niveau du terminal quand la bouche est ouverte, et pas -12V !

## Schéma EasyEAD
![schema](/V2/src/images/EasyEAD/schéma_elec.png)

## PCB
Coté composant:

![Coté composant](/V2/src/images/EasyEAD/PCB_composant.png)

Coté cuivre:

![Coté cuivre](/V2/src/images/EasyEAD/PCB_cuivre.png)

3D:

![3D](/V2/src/images/EasyEAD/PCB_3D.png)

## ESPHome
Installer ESPHome sur votre ESP32 et ajouter la [configuration Yaml](/V2/src/yaml/ESPHome/esphome_v1.yaml)

[![Watch the video](https://img.youtube.com/vi/3GbyYQHQvV8/maxresdefault.jpg)](https://youtu.be/3GbyYQHQvV8)

Exemple:
```yaml
number:
  - platform: modbus_controller
    modbus_controller_id: motherboard
    name: "sejour Thermostat K1a"
    icon: "mdi:thermostat"
    unit_of_measurement: "°C"
    address: 0x96
    value_type: U_WORD
    min_value: 0
    max_value: 31
    use_write_multiple: true
    multiply: 100
sensor:
  - platform: modbus_controller
    modbus_controller_id: central_command
    name: "sejour temperature K1a"              # à adapter
    disabled_by_default: false                  # à adapter
    device_class: temperature
    state_class: measurement
    register_type: holding
    address: 190
    value_type: S_WORD
    unit_of_measurement: "°C"
    accuracy_decimals: 1
    filters:
      - multiply: 0.01
```
**Note :** [Pour l'AquaAir, Modbus permet d'autres choses](https://forum.hacf.fr/t/aldes-t-one-air-aquaair/42974/171)

## Home Assistant
Installer [hass-template-climate](https://github.com/jcwillox/hass-template-climate) dans HACS et ajouter la [configuration Yaml](/V2/src/yaml/Home%20assistant/climate_v1.yaml)

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

## Impression 3D
TODO

**Note :** un énorme merci à la [communauté d'HACF](https://forum.hacf.fr/t/aldes-t-one-air-aquaair/42974) dans laquelle nous travaillons sur le sujet depuis plus de 2 ans !
