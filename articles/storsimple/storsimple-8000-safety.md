---
title: Veilig installeren & StorSimple 8000 Series-apparaat
description: Hierin worden beveiligings conventies, richt lijnen en overwegingen beschreven en wordt uitgelegd hoe u uw StorSimple-apparaat veilig kunt installeren en bedienen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/04/2017
ms.author: alkohli
ms.openlocfilehash: 3adbd292d42fd6fd2a0e149e103c17968a6f084b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94950751"
---
# <a name="safely-install-and-operate-your-storsimple-device"></a>Uw StorSimple-apparaat veilig installeren en uitvoeren
![Pictogram waarschuwings pictogram ](./media/storsimple-safety/IC740879.png)
 ![ Lees beveiliging Lees de ](./media/storsimple-safety/IC740885.png) **informatie over de veiligheid en de status**

Lees alle informatie over de veiligheid en status in dit artikel die van toepassing is op uw Microsoft Azure StorSimple-apparaat. Bewaar alle gedrukte hulp lijnen die worden geleverd bij uw StorSimple-apparaat voor toekomstig Naslag informatie. Het is niet mogelijk om instructies te volgen en op de juiste wijze in te stellen, te gebruiken en te zorgen voor dit product kan het risico op ernstige schade of dood of beschadiging van het apparaat of de apparaten verg Roten. Er is ook een [Download bare versie van deze hand leiding](https://www.microsoft.com/download/details.aspx?id=44233) beschikbaar.

## <a name="safety-icon-conventions"></a>Conventies voor veiligheids pictogrammen
Hier vindt u de pictogrammen die u zult vinden wanneer u de veiligheids maatregelen controleert die moeten worden waargenomen bij het instellen en uitvoeren van uw Microsoft Azure StorSimple-apparaat.

| Pictogram | Description |
|:--- |:--- |
| ![Gevaar voor risico pictogram ](./media/storsimple-safety/IC740879.png) **!** |Duidt op een gevaarlijke situatie die, indien dat niet wordt vermeden, leidt tot dood of ernstige schade. Dit signaal Word is beperkt tot de meest extreme situaties. |
| ![Waarschuwing voor waarschuwings pictogram ](./media/storsimple-safety/IC740879.png) **.** |Duidt op een gevaarlijke situatie die, indien dat niet wordt vermeden, kan leiden tot overlijden of ernstige schade. |
| ![Waarschuwing voor waarschuwings pictogram ](./media/storsimple-safety/IC740879.png) **!** |Duidt op een gevaarlijke situatie die, indien dat niet wordt vermeden, kan leiden tot kleine of gemiddelde schade. |
| ![Meldings pictogram ](./media/storsimple-safety/IC740881.png) **:** |Geeft aan dat de informatie die belang rijk is, maar niet met een risico is betrokken. |
| ![Elektrisch schok-pictogram ](./media/storsimple-safety/IC740882.png) **risico op elektrische schokken** |Hoge spanning |
| ![Pictogram voor dik gewicht ](./media/storsimple-safety/IC740883.png) **dik gewicht** | |
| ![Er zijn geen onderdelen die door de gebruiker kan ](./media/storsimple-safety/IC740879.png)  worden onderhouden |Doe geen toegang tenzij het goed is getraind. |
| ![Lees het pictogram beveiligings melding](./media/storsimple-safety/IC740885.png)**eerst alle instructies lezen** | |
| ![Waarschuwings pictogram tip ](./media/storsimple-safety/IC740886.png) **-risico** | |

## <a name="handling-precautions"></a>Voorzorgsmaatregelen verwerken
![Waarschuwing pictogram pictogram voor ](./media/storsimple-safety/IC740879.png) ![ zwaar gewicht ](./media/storsimple-safety/IC740883.png) **.** 

Om het risico van schade te verminderen:

* Een volledig geconfigureerde behuizing kan tot 32 kg (70 lbs) wegen. Probeer het niet zelf door te tillen.
* Voordat u de behuizing verplaatst, moet u er altijd voor zorgen dat er twee personen beschikbaar zijn voor het afhandelen van het gewicht. Houd er rekening mee dat één persoon die dit gewicht probeert te halen, schade kan behalen.
* Til de behuizing niet door de ingangen op de PCMs (Power and koeling modules) aan de achterzijde van de eenheid. Deze zijn niet ontworpen om het gewicht te nemen.

## <a name="connection-precautions"></a>Verbindings maatregelen
![Waarschuwing pictogram Waarschuwing voor ](./media/storsimple-safety/IC740879.png) ![ elektrisch schokken ](./media/storsimple-safety/IC740882.png) **!**

Om de kans op schade, elektrische schokken of dood te verminderen:

* Wanneer er meerdere AC-bronnen worden ingeschakeld, verbreekt u de verbinding met alle voedings kracht voor een volledige isolatie.
* Ontkoppel de eenheid permanent voordat u deze verplaatst of als u denkt dat deze op enigerlei wijze beschadigd is geraakt.
* Geef een veilige verbinding voor elektrische aard op met de stroom kabels. Controleer of de aarding van de behuizing voldoet aan de nationale en lokale vereisten alvorens energie toe te passen.
* Zorg ervoor dat de stroom verbinding altijd wordt verbroken voordat een PCM uit de behuizing wordt verwijderd.
* Gezien de stekker van de stroom kabel van de voeding is de belangrijkste verbrekings apparaat, zorg ervoor dat de sockets in de buurt van de apparatuur worden bevinden en gemakkelijk toegankelijk zijn.

![Waarschuwing pictogram Waarschuwing voor ](./media/storsimple-safety/IC740879.png) ![ elektrisch schokken ](./media/storsimple-safety/IC740882.png) **!**

Om de kans op oververwarming of het weglaten van de elektrische verbindingen te verminderen:

* Geef een geschikte voedings bron met elektrische overbelasting beveiliging op om te voldoen aan de vereisten die worden beschreven in de technische specificatie.
* Gebruik geen bifurcated-stroom kabels (' Y ' leads).
* Om te voldoen aan de toepasselijke veiligheids-, emissie-en thermische vereisten, moeten geen voor vallen worden verwijderd en moeten alle bays worden gevuld met invoeg modules of lege stations.
* Zorg ervoor dat het apparaat wordt gebruikt op een manier die is opgegeven door de fabrikant. Als deze apparatuur wordt gebruikt op een manier die niet door de fabrikant is opgegeven, kan de beveiliging van de apparatuur worden aangetast.

![Meldings pictogram ](./media/storsimple-safety/IC740881.png) **:**

Voor de juiste werking van uw apparatuur en om te voor komen dat het product wordt beschadigd:

* De RJ45-poorten op de achterkant van het apparaat zijn alleen voor een Ethernet-verbinding. Deze moeten niet zijn verbonden met een telecommunicatie netwerk.
* Zorg ervoor dat u het apparaat in een rek installeert dat geschikt is voor een ontwerp van front-to-back-koeling.
* Alle invoeg modules en lege platen maken deel uit van de systeem behuizing. Deze moeten alleen worden verwijderd wanneer een vervanging direct kan worden toegevoegd. Het systeem mag niet worden uitgevoerd zonder alle modules of lege locaties.

## <a name="rack-system-precautions"></a>Voorzorgsmaatregelen voor het rack systeem
U moet rekening houden met de volgende veiligheids vereisten wanneer u het apparaat koppelt aan een rack behuizing.

![Waarschuwing waarschuwings pictogram ](./media/storsimple-safety/IC740879.png) ![ Tip ](./media/storsimple-safety/IC740886.png) **waarschuwings pictogram.**

Om de kans op schade van een tip te verminderen:

* Het rack ontwerp moet het totale gewicht van de geïnstalleerde behuizingen ondersteunen en gestabiliseerde functies omvatten die geschikt zijn om te voor komen dat het rek wordt gekanteld of gepusht tijdens de installatie of normaal gebruik.
* Wanneer u een rek laadt, vult u het rek vanuit de onderkant naar boven en maakt u het leeg.
* Schuif niet meer dan één behuizing tegelijk uit het rek om het risico van het toppling te voor komen.

![Waarschuwing pictogram Waarschuwing voor ](./media/storsimple-safety/IC740879.png) ![ elektrisch schokken ](./media/storsimple-safety/IC740882.png) **!**

Om de kans op schade, elektrische schokken of dood te verminderen:

* Het rek moet een kluis systeem voor elektrische distributie hebben. Het moet over de actuele beveiliging beschikken voor de behuizing en mag niet worden overbelast door het totale aantal geïnstalleerde behuizingen. De classificatie voor het energie verbruik die op het webplaatje wordt weer gegeven, moet in acht worden genomen.
* Het elektriciteits distributiesysteem moet een betrouw bare massa bieden voor elke behuizing in het rek.
* Het ontwerp van het systeem voor elektrische distributie moet rekening houden met de totale massa lekkage van alle voedings eenheden in alle behuizingen. Houd er rekening mee dat elke voeding in elke behuizing een massa lekkage heeft van 1,0 mA maximum bij 60 Hz, 264 volt. Het rek vereist mogelijk labels met ' hoge lekkage stroom '. Massa verbinding (aarde) is essentieel voordat u een aanbod verbindt. '
* Het rek, wanneer het is geconfigureerd met de behuizingen, moet voldoen aan de veiligheids vereisten van UL 60950-1 en IEC 60950-1/EN 60950-1.

![Meldings pictogram ](./media/storsimple-safety/IC740881.png) **:**

Voor de juiste koeling van uw rack systeem:

* Zorg ervoor dat het rack ontwerp rekening houdt met de Maxi maal toegestane Tempe ratuur van 35 graden Celsius van de behuizing (95 graden Fahrenheit). Bewaar de ruimte waar het rack systeem cool is en controleer of er voldoende lucht stroom is van de AC-ventilator in het Data Center.
* Het systeem wordt uitgevoerd met een lage druk, een installatie met een achterlopende uitlaat (back-up die is gemaakt door rack deuren en obstakels die niet groter zijn dan 5 Pascal [0,5 mm water meter]).

## <a name="power-cooling-module-pcm-precautions"></a>Voorzorgsmaatregelen voor energie koeling module (PCM)

Het apparaat is ontworpen om te worden gebruikt met twee PCMs. Elk van de PCMs heeft een voeding en een ventilator op twee assen. Tijdens een kritieke toestand kan het systeem een storing van één voeding veroorzaken terwijl de normale bewerkingen worden voortgezet. Twee PCMs-eenheden (en dus voedingen) moeten altijd worden geïnstalleerd. Eén PCM biedt geen redundante voeding. De uitval van zelfs één PCM kan er daarom toe leiden dat er downtime of mogelijk gegevens verloren gaan.

![Waarschuwing pictogram Waarschuwing voor ](./media/storsimple-safety/IC740879.png) ![ elektrisch schokken ](./media/storsimple-safety/IC740882.png) **!**

Om de kans op schade, elektrische schokken of dood te verminderen:

* Verwijder de deksels niet uit de PCM. Er is sprake van een bedreiging van elektrische schokken in. [Neem contact op met Microsoft ondersteuning](./storsimple-8000-contact-microsoft-support.md)om de PCM te retour neren en een vervanging te verkrijgen.

![Meldings pictogram ](./media/storsimple-safety/IC740881.png) **:**

Voor de juiste werking van uw apparatuur en om te voor komen dat het product wordt beschadigd:

* U moet de defecte PCM binnen 24 uur vervangen. Nadat een PCM is verwijderd voor vervanging, moet de vervanging binnen 10 minuten na verwijdering worden voltooid.
* Verwijder geen PCM tenzij een vervanging direct kan worden geïnstalleerd. De behuizing mag niet worden gebruikt zonder alle modules op locatie.

## <a name="electrostatic-discharge-esd-precautions"></a>Voorzorgsmaatregelen voor elektrostatische lozing (ESD)

![Meldings pictogram ](./media/storsimple-safety/IC740881.png) **:**

Bekijk de volgende ESD-gerelateerde voorzorgsmaatregelen.

* Zorg ervoor dat u een geschikte antistatic-pols of RFID riem hebt geïnstalleerd en gecontroleerd.
* Bekijk alle conventionele ESD-voorzorgsmaatregelen bij het verwerken van modules en onderdelen.
* Vermijd contact met backplane-onderdelen en module connectors.
* ESD-schade wordt niet gedekt door de garantie.

## <a name="battery-disposal-precautions"></a>Voorzorgsmaatregelen voor het verwijderen van de batterij

De voeding gebruikt een speciale batterij om de inhoud van het geheugen te beschermen tijdens tijdelijke, langdurige stroom uitval. De accu bevindt zich in de PCM. Houd de volgende informatie in acht over de accu.

![Waarschuwing voor waarschuwings pictogram ](./media/storsimple-safety/IC740879.png) **.**

Om het risico van korte, brand, ontploffing, verwonding of dood te verminderen:

* De gebruikte batterijen worden afgestoten volgens nationale/regionale voor Schriften.
* U mag niet meer dan 60 graden Celsius (140 graden Fahrenheit) of verbranding maken. Vervang de PCM-batterij alleen door een geleverde batterij. Het gebruik van een andere accu kan een risico op brand of ontploffing vormen.
* Gebruik de beschermende eind letters op de accu's als deze worden verwijderd uit de voeding.

![Meldings pictogram ](./media/storsimple-safety/IC740881.png) **:**

Wanneer u de batterijen verzendt of anderszins transporteert door de lucht, volgt u het IATA lithium battery-richt lijn document dat beschikbaar is op [https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx](https://www.iata.org/whatwedo/cargo/dgr/Pages/lithium-batteries.aspx)

Nadat u deze veiligheids kennisgevingen hebt gecontroleerd, moet u de volgende stappen uitvoeren om uw apparaat uit te pakken, te rek en te bekabelen.

## <a name="next-steps"></a>Volgende stappen

* Voor een 8100-apparaat gaat u naar [uw StorSimple 8100-apparaat installeren](storsimple-8100-hardware-installation.md).
* Voor een 8600-apparaat gaat u naar [uw StorSimple 8600-apparaat installeren](storsimple-8600-hardware-installation.md).