# Passerelle ESPHome pour Aldes T.One® AIR

## **Attention :** 
cette page indépendente du fabricant présente des idées dont la mise en pratique nécessite des connaissances approfondies en chauffage, climatisation, électricité, électronique et informatique. Les risques sont nombreux et des erreurs graves figurent très probablement dans cette page. L'auteur se dégage de toute responsabilité liée à la mise en oeuvre de ce projet. N'utilisez pas ce projet, utilisez la passerelle officielle [AldesConnect® Box](https://www.aldes.fr/produits/mesure-regulation-et-connectivite/capteurs-et-connectivite/autres-capteurs/aldesconnect-box).

Documentation [esphome](https://esphome.io/)

**Note :** un énorme merci à la [communauté d'HACF](https://forum.hacf.fr/t/aldes-t-one-air-aquaair/42974) dans laquelle nous travaillons sur le sujet depuis plus de 2 ans !

## Objectif
**Ce projet expérimental permet de piloter un système Aldes T.One® AIR via le réseau local et propose en particulier une intégration poussée dans HomeAssistant sous la forme d'entités Climate pour chaque thermostat.**
### fonctionnalité principales:
+  Afficher et modifier les températures de consignes de l'ensemble des thermostats
+  Afficher les tempérratures des piéces
+  Connaitre l'état (ouvert/fermé) des bouches de diffusion ( 6 max )
+  Création d'entité Climate dans Home assistant

![climate1](/V2/src/images/ha/thermostat%20séjour%20k1a-1.png)

![climate2](/V2/src/images/ha/thermostat%20séjour%20k1a-2.png)

![climate3](/V2/src/images/ha/thermostat%20séjour%20k1a-3.png)

## PCB
+ PCB (récupérer le [fichier Gerber Switch](/V2/src/PCB/EasyEAD/Gerber_ESPHome-Aldes-T.One-advanced_switch.zip) ou [fichier Gerber Jumper](/V2/src/PCB/EasyEAD/Gerber_ESPHome-Aldes-T.One-advanced_jumper.zip) et le faire fabriquer sur un site comme JLCPCB.com)
+ Pour le PCB sans les bouches, récupérer le [fichier Gerber Basic](Gerber_ESPHome-Aldes-T.One-Basic-v1.0_PCB_ESPHome-Aldes_2024-12-31.zip)  ou le [projet EasyEAD](SCH_ESPHome-Aldes-T.One-Basic-v1.0_2024-12-31.json)
+ Kit soudure [Amazon](https://amzn.eu/d/3sIApjg) ou autre

## Composants électroniques
+ D1 mini ESP32 ([AliExpress](https://fr.aliexpress.com/item/1005005972627549.html)) ou ([Amazon](https://amzn.eu/d/dTeepAy))
+ 2 x Convertisseur RS485/TTL ([AliExpress](https://fr.aliexpress.com/item/1005006340391490.html))
+ Convertisseur de niveau logique bidirectionnel 5V <=> 3.3V ([AliExpress](https://fr.aliexpress.com/item/1005006068381598.html)) ou ([Amazon](https://amzn.eu/d/aiCa1Ww)
+ Convertisseur 12V => 5V ([AliExpress](https://fr.aliexpress.com/item/1005006486270630.html)) ou ([Amazon](https://amzn.eu/d/aN7AZQ7)) : penser à régler la sortie sur 5V avant de le brancher au reste ! (multimétre)
+ Convertisseur numérique de niveaux logiques 12V => 3.3V à 4 voies ([AliExpress](https://fr.aliexpress.com/item/1005006419972546.html)) : Pour les bouches de diffusion
+ 3 x Mini interrupteur 1P2T SPDT (> 2A) [AliExpress](https://a.aliexpress.com/_EyLZ1bw) ou [Amazon](https://amzn.eu/d/39LWGet)
+ 3 x Jumper de connexion [AliExpress](https://a.aliexpress.com/_EQpgm7m)
  
## Connectique :
+ Câbles de connexion [Amazon](https://amzn.eu/d/2ajQLdu)
+ 3 x Terminal 4 bornes ([AliExpress](https://fr.aliexpress.com/item/32828459901.html))
+ 6 x Terminal 2 bornes ([AliExpress](https://fr.aliexpress.com/item/32828459901.html))
+ Headers mâles ([AliExpress](https://fr.aliexpress.com/item/32973181162.html))
+ Pince à sertir et embouts ([AliExpress](https://fr.aliexpress.com/item/32831768783.html)) ou [Amazon](https://amzn.eu/d/3J0eNm9)
+ Facultatif : headers femelle doubles (2) pour l'ESP32 ([AliExpress](https://fr.aliexpress.com/item/32747224548.html))
+ Falcutatif : headers femelle simples pour les autres circuits ([AliExpress](https://fr.aliexpress.com/item/32854239374.html))
+ Embouts doubles ([AliExpress](https://fr.aliexpress.com/item/1005004846852618.html))


Les terminaux "K" sont à relier aux terminaux correspondants sur la carte mère de la PAC, où sont déjà branchés les câbles menant aux bouches. Attention, la polarité n'est pas importante pour les vérins des bouches selon la documentation officielle, mais elle est importante ici. Assurez-vous d'obtenir +12V au niveau du terminal quand la bouche est ouverte, et pas -12V !

## Schéma EasyEAD

![schema](/V2/src/images/EasyEAD/schéma_elec.png)

## PCB
Coté composant:
![Coté composant](/V2/src/images/EasyEAD/PCB_composant.png)

Coté cuivre:
![Coté cuivre](/V2/src/images/EasyEAD/PCB_cuivre.png)


## ESPHome
configuration yaml [ICI](/V2/src/yaml/ESPHome/esphome_v1.yaml)

**Note :** [Pour l'AquaAir, Modbus permet d'autres choses](https://forum.hacf.fr/t/aldes-t-one-air-aquaair/42974/171)

## Home Assistant
Configuration [ICI](/V2/src/yaml/Home assistant/climate_v1.yaml)

Installer [hass-template-climate](https://github.com/jcwillox/hass-template-climate)

