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
- [Krabice E113 85x85x40mm IP54](https://www.e1.cz/produkt/1230649-krabice-e113-85x85x40mm-ip54?t=popis "Rozbočovací krabička") - umístění desky a step downu - 1 ks
- [Dupont propojovací kabely samice - samice 20 cm](https://www.aliexpress.com/item/32825558073.html "Dupont propojovací kabely samice - samice 20 cm")
- Držák na HC-RS04 - [vytištěn na 3D tiskárně](https://www.thingiverse.com/thing:189585 "vytištěn na 3D tiskárně")
- a běžný spotřební materiál (smršťovací bužírka, izolačka, kabelové držáky, stahovací pásky, pájecí materiál)
