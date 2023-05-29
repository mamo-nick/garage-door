# Ovládání garážových vrat z Home Assistant
Implementace wi-fi relé pro ovládání garážových vrat do stávajícího řešení a jeho integrace do Home assistant.

## Úvod
### Stávající řešení
Rolovací garážová vrata s ovládací jednotkou SIMU RSA Hz. 
Ovladací jednoka je nastavena na 4-krokový režim. Jedná se o nejčastější řešení, kdy je ovládání zajištěno jedním tlačítkem.
Otevírání vrat je tedy krokové: NAHORU - STOP - DOLŮ - STOP atd. Při stisku tlačítka během otevírání nebo zavírání se vrata zastaví. 
Vlastní ovládání je možno
- dvěma dálkovými ovladači
- tlačítkem uvnitř garáže
- číselnou klávesnici na garáži

### Cílové řešení
Cílem je ovládat vrata z prostředí Home Assistant a to ručně nebo pomocí automatizace.
Dále pomocí magnetického senzoru získat informaci o poloze vrat (otevřeno - zavřeno) a pomocí ultrazvukového senzoru na měření vzdálenosti získat informaci o obsazenosti garáže (auto je doma - pryč).
## Použitý materiál
### Hardware
- [Sonoff SV - bezdrátový wi-fi spínač](https://www.aliexpress.com/item/4000898615200.html "Sonoff SV - bezdrátový wi-fi spínač")
- [Step down DC DC - nastavitelný měnič](https://www.aliexpress.com/item/4000064597454.html "Step down DC DC")
- [HC-SR04 - ultrazvukový senzor pro měření vzdálenosti](https://www.aliexpress.com/item/32713522570.html "HC-SR04 - ultrazvukový senzor pro měření vzdálenosti")
- [Jazýčkový magnetický senzor](https://www.aliexpress.com/item/457511345.html "Jazýčkový magnetický senzor")

### Instalační materiál 
- Kabel 4 žilový - k senzoru HC-SR04 - 4 m
- Dvoulinka (původně kabel k reproduktoru) - k magnetickému senzoru - 2 m
- UTP kabel (použil jsem starší) - propojení SIMU jednotky a Sonoff SV - 0,6 m
- [Svorka na DPS 2 piny 2.54mm](https://www.aliexpress.com/item/32867190033.html "Svorka na DPS 2 piny 2.54mm") - na desku Sonoff SV - 2ks
- [Krabice E113 85x85x40mm IP54](https://www.e1.cz/produkt/1230649-krabice-e113-85x85x40mm-ip54?t=popis "Rozbočovací krabička") - umístění desky a step-downu - 1 ks
- [Dupont propojovací kabely samice - samice 20 cm](https://www.aliexpress.com/item/32825558073.html "Dupont propojovací kabely samice - samice 20 cm")
- Držák na HC-RS04 - [vytištěn na 3D tiskárně](https://www.thingiverse.com/thing:189585 "vytištěn na 3D tiskárně")
- a běžný spotřební materiál (smršťovací bužírka, izolačka, kabelové držáky, stahovací pásky, pájecí materiál)

### Příprava
1. **Odstranit na desce Sonoff SV dva rezistory v blizkosti napájení** - tím dojde k přerušení +5V na vstup relé
2. Připájení konektorů (z Dupont kabelů) na kabel pro HC-SR04 a na dvoulinku pro magnetický senzor

### Zapojení
#### Napájení
Napájení je realizováno z jednotky SIMU, kde je ze svorek 10 (+) a 12 (-) bráno 24 V. To je následně pomocí step-down sraženo na 5 V. Tím odpadá nutnost externího napájení z adaptéru. Použit UTP kabel (2x dvě žíly).
#### Senzory
|  Sonoff SV | Senzor  |   |
| ------------ | ------------ | ------------ |
|  GPIO5 | Trigger  |HC-SR04   |
|GPIO4   |Echo   | HC-SR04  |
| GPIO14  |  |  magnet. senzor  |

#### Schéma zapojení
![](https://github.com/mamo-nick/garage-door/blob/main/Sch%C3%A9ma%20zapojen%C3%AD%20II.png?raw=true)

Relé desky je na vstupu propojeno s SIMU jednotkou, svorka 14 (+) a na výstupu svorka 18 (-). Použit UTP kabel (zbývající 2x dvě žíly).
Magnetický senzor je připojen dvoulinkou k GPIO14 a Ground. Samotné umístění senzoru je u podlahy, tedy jeho sepnutí detekuje stav Zavřeno. U sekvenčních vrat bývá tento senzor umísťován na horní lištu a k sepnutí dochází při stavu Otevřeno. Při tomto řešení senzor hlásí zavřeno hned při zajájení zavírání. Pokud z nějakého důvodu nedojde k plnému zavření, i tak je hlášeno Zavřeno, což není šťastné řešení. Toto řešení ale také vyžaduje ve FW nastavit u binárního senzoru `inverted: True` 

```
binary_sensor:
  - platform: gpio
    pin:
      number: GPIO14
      mode: INPUT_PULLUP
      
# Použít v případě, že magnetický senzor sepnutím detekuje Otevřeno
#     inverted: true 
#
    name: "Garážová vrata"
    device_class: garage_door
```


Ultrazvukový senzor HC-SR14 je spojen s deskou dle tabulky a schéma výše. Signalizace přítomnosti vozidla je realizována nastavením senzoru jako binární. 
Stav true/false je nastaven na podmínku: je-li naměřená vzdálenost menší než 1,6 m, pak je auto přítomno. Tuto hodnotu je třeba nastavit podle vzdálenosti umístění senzoru od auta. 

```
   
  - platform: template
    name: "Fiesta je..."
    device_class: presence
    lambda: |-
      if (id(ultrasonic_sensor1).state < 1.6) {
        // car is in the garage
        return true;
      } else {
        // no car
        return false;
      }   

```
Samotný senzor je umístěn na stropě nad autem.

## Firmware
Na desku je nahrán firmware ESPhome. Vedle základních senzorů jsem nahrál další senzory, které posílají různé informace o zařízení

![](https://github.com/mamo-nick/garage-door/blob/main/Karta_garaz.png?raw=true)

Soubor ke stažení: [vrata.yaml](https://github.com/mamo-nick/garage-door/blob/main/vrata.yaml)

## Automatizace
Otevírání vrat je triggerem pro rozsvícení světla v garáži, když je tma. 

## Zdroje informací
- Hlavním zdrojem informací pro tuto realizaci je [DrZzs a jeho video](https://www.youtube.com/watch?v=AU1KD_aJSMY).
- Dalším a neméně důležitý zdroj je [blog Jirky Vorálka](https://blog.vyoralek.cz/iot/centrum-chytre-domacnosti-homeassistant-hass-io-6-cast-sonoff-esphome/), kde jsem se seznamoval s flashováním.

A dále 
- [homeassistant.io](https://www.home-assistant.io/docs/)
- [esphome.io](https://esphome.io)
