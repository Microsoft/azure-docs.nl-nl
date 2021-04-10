---
title: Hand leiding voor de beveiliging van Azure Stack Edge-mini-R | Microsoft Docs
description: Hierin worden de veiligheids conventies, richt lijnen en overwegingen beschreven en wordt uitgelegd hoe u uw Azure Stack Edge mini-R-apparaat veilig kunt installeren en bedienen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: alkohli
ms.openlocfilehash: eb42a9a77927d8577dfec3c9167294eb8f809fec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100382618"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Beveiligings instructies voor de Azure Stack Edge mini-R

![Waarschuwings pictogram 1 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ pictogram Lees beveiligings melding ](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
 **Lees veiligheid en status informatie**

Lees de informatie over de veiligheid in dit artikel voordat u uw Azure Stack Edge mini-R-apparaat gebruikt, een samen stelling van één batterij pakket, een wisselstroom voeding van AC/DC, één module stroom adapter en één server module. Het volgen van instructies kan leiden tot brand, elektrische schokken, verwondingen of beschadiging van uw eigenschappen. Lees alle onderstaande veiligheids informatie voordat u Azure Stack Edge mini-R gebruikt.

## <a name="safety-icon-conventions"></a>Conventies voor veiligheids pictogrammen

De volgende signaal woorden voor waarschuwings signalen voor waarschuwingen zijn:

| Pictogram | Description |
|:--- |:--- |
| ![Waarschuwings symbool](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **Gevaar:** Duidt op een gevaarlijke situatie die, indien dat niet wordt vermeden, leidt tot dood of ernstige schade. <br> **Waarschuwing:** Duidt op een gevaarlijke situatie die, indien dat niet wordt vermeden, kan leiden tot overlijden of ernstige schade. <br> **Let op:** Duidt op een gevaarlijke situatie die, indien dat niet wordt vermeden, kan leiden tot kleine of gemiddelde schade.|
|

De volgende gevaren pictogrammen moeten worden waargenomen bij het instellen en uitvoeren van uw Azure Stack Edge mini-R-apparaat:

| Pictogram | Description |
|:--- |:--- |
| ![Lees eerst alle instructies](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | Lees eerst alle instructies |
| ![Meldings pictogram ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **:** | Geeft aan dat de informatie die belang rijk is, maar niet met een risico is betrokken. |
| ![Waarschuwings symbool](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) | Waarschuwings symbool |
| ![Pictogram elektrische schok](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) | Elektrisch schok gevaar |
| ![Alleen binnen gebruik](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) | Alleen binnen gebruik |
| ![Er is geen pictogram voor de gebruikers-service onderdelen](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) | Er zijn geen onderdelen die door de gebruiker kan worden onderhouden. Doe geen toegang tenzij het goed is getraind. |
|

## <a name="handling-precautions-and-site-selection"></a>Voorzorgsmaatregelen en site selectie verwerken

Het Azure Stack Edge mini-R-apparaat heeft de volgende afhandelings maatregelen en criteria voor het selecteren van de site:

![Waarschuwings pictogram 2 pictogram voor ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ elektrisch schokken, ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **Waarschuwing:**

* Inspecteer het apparaat voor *ontvangen* van schade. Als de behuizing van het apparaat is beschadigd, [neemt u contact op met Microsoft ondersteuning](azure-stack-edge-placeholder.md) om een vervanging te verkrijgen. Probeer het apparaat niet te bedienen.
* Als u vermoedt dat het apparaat defect is, [neemt u contact op met Microsoft ondersteuning](azure-stack-edge-placeholder.md) om een vervanging te verkrijgen. Probeer het apparaat niet te onderhouden.
* Het apparaat bevat geen onderdelen die door de gebruiker kan worden onderhouden. Er zijn schadelijke spannings-, stroom-en energie niveaus aanwezig in. Open niet. Het apparaat terug naar micro soft voor onderhoud.

![Waarschuwings pictogram 3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Let op:**

Het is raadzaam om het systeem uit te voeren:

* Weg van bronnen van warmte, waaronder direct zonlicht en radiatoren.
* Op locaties die niet blootgesteld zijn aan vocht of regen.
* Deze bevindt zich in een ruimte die trillingen en fysieke schokken minimaliseert.  Het systeem is ontworpen voor schokken en trillingen op basis van het 810G.
* Geïsoleerd van krachtige elektromagnetische velden die door elektrische apparaten worden geproduceerd.
* Geen Liquid of een afwijkend object toestaan om het systeem in te voeren. Plaats geen dranken of andere liquide containers op of in de buurt van het systeem.

![Waarschuwings pictogram 4 waarschuwing voor onderdelen die niet van een ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ gebruiker te onderhouden zijn ](./media/azure-stack-edge-mini-r-safety/icon-safety-do-not-access.png) **:**

* Deze apparatuur bevat een Lithium batterij. Probeer niet om het accu pack te onderhouden. Accu's in deze apparatuur zijn niet in gebruik. Risico op explosie als de accu wordt vervangen door een onjuist type.

![Waarschuwings pictogram 5 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Let op:**

Het accu pack wordt alleen in rekening gebracht wanneer het deel uitmaakt van het Azure Stack Edge mini-R-apparaat. dit wordt niet als een afzonderlijk apparaat in rekening gebracht.

![Waarschuwings pictogram 6 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Let op:**

* De aan/uit-schakelaar van het accu pack is alleen voor het opladen van de accu naar de server module. Als de stroom adapter is aangesloten op het accu pack, wordt de stroom aan de server module door gegeven, zelfs als de switch zich in de buiten stand bevindt.

![Waarschuwings pictogram 7 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Let op:**

* Brand het accu pack niet of verkort het circuit. Het moet correct worden gerecycled of verwijderd.

![Waarschuwings pictogram 8 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Let op:**

* In plaats van gebruik te maken van de geleverde AC/gelijkstroom voeding, heeft dit systeem ook de optie om een veld van het type 2590-batterij te gebruiken. In dit geval moet de eind gebruiker controleren of het voldoet aan alle toepasselijke veiligheids-, transport-, milieu-en andere nationale/plaatselijke regelgeving.
* Wanneer u het systeem met de batterij van het type 2590 gebruikt, moet u de batterij in de gebruiks voorwaarden van de accu van de fabrikant.

![Waarschuwings pictogram 9 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Let op:**

Dit apparaat heeft twee SFP +-poorten, die kunnen worden gebruikt met optische transceivers. Gebruik alleen met klasse 1-transceivers om gevaarlijke laser straling te voor komen.

## <a name="electrical-precautions"></a>Elektrische voorzorgsmaatregelen

Het Azure Stack Edge mini-R-apparaat heeft de volgende elektrische voorzorgsmaatregelen:

![Waarschuwings pictogram 10 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) ![ waarschuwing voor elektrisch schok pictogram ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **:**

Bij gebruik in combi natie met de adapter voor energie toevoer:

* Geef een veilige verbinding voor de elektrische voeding met de stroom kabel op. De afwisselende stroom kabel (AC) heeft een stekker met drie draden (een stekker met een massa pincode). Deze stekker past alleen op een geaard AC-uitgang. Verslaan niet het doel van de massa pincode.
* Gezien de stekker van de stroom kabel van de voeding is het belangrijkste apparaat voor het verbreken van de verbinding, zorg ervoor dat de sockets zich in de buurt van het apparaat bevinden en gemakkelijk toegankelijk zijn.
* Ontkoppel de stroom kabel (s) (door de stekker te halen en niet de kabel) en Verbreek de verbinding met alle kabels als aan een van de volgende voor waarden wordt voldaan:

  * De stroom kabel of de stekker wordt verzonken of anderszins beschadigd.
  * Het apparaat wordt blootgesteld aan regen, overmaat vocht of andere vloei stoffen.
  * Het apparaat is verwijderd en de behuizing van het apparaat is beschadigd.
  * U vermoedt dat het apparaat service of reparatie nodig heeft.
* Ontkoppel de eenheid permanent voordat u deze verplaatst of als u denkt dat deze op enigerlei wijze beschadigd is geraakt.

* Geef een geschikte voedings bron met elektrische overbelasting beveiliging op om te voldoen aan de volgende energie specificaties:

* Spanning: 100-240 v wissel spanning
* Huidige: 1,7 amperes
* Frequentie: 50 tot 60 Hz

![Waarschuwing voor waarschuwing 11 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ elektrisch schok pictogram ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png) **:**

* U hoeft geen netstroom kabel (s) te wijzigen of gebruiken, anders dan die van de apparatuur.

![Waarschuwings pictogram 12 het ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ elektrische schok pictogram ](./media/azure-stack-edge-mini-r-safety/icon-safety-electric-shock.png)
 ![ alleen een ](./media/azure-stack-edge-mini-r-safety/icon-safety-indoor-use-only.png) **waarschuwing gebruiken:**

* De voeding die is gelabeld met dit symbool, wordt alleen geclassificeerd voor gebruik in de binnenste.

## <a name="regulatory-information"></a>Reglementaire informatie

Hieronder vindt u informatie over de regelgeving voor Azure Stack Edge mini-R-apparaat, regelgevings model nummer: TMA01.

Het Azure Stack Edge mini-R-apparaat is ontworpen voor gebruik met NRTL in de lijst (UL, CSA, ETL, enzovoort), en IEC/EN 60950-1 of IEC/EN 62368-1 compliant (CE gemarkeerd) informatie technologie apparatuur.

In andere landen dan de Verenigde Staten en Canada mag netwerk kabels (niet meegeleverd met de apparatuur) niet worden geïnstalleerd met deze apparatuur als hun lengte groter is dan 3 meters.

De apparatuur is ontworpen om te worden gebruikt in de volgende omgevingen:

| Omgeving | Specificaties |
|:---  |:--- |
| Specificaties systeem temperatuur | <ul><li>Opslag temperatuur: – 20 &deg; c – 50 &deg; c (– 4 &deg; f-122 &deg; f)</li><li>Doorlopende bewerking: 0 &deg; C: 40 &deg; C (32 &deg; f – 104 &deg; f)</li></ul> |
| Specificaties van relatieve lucht vochtigheid (RH) | <ul><li>Opslag: 5% tot 95% relatieve vochtigheid</li><li>Actief: 10% tot 90% relatieve vochtigheid</li></ul>|
| Specificaties maximum hoogte | <ul><li>Werk: 15.000 meter (4.572 meters)</li><li>Niet-actief: 40.000 meter (12.192 meters)</li></ul>|

> ![Meldings pictogram-2 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **kennisgeving:** &nbsp; wijzigingen of wijzigingen aan de apparatuur die niet expliciet door micro soft zijn goedgekeurd, kunnen de bevoegdheid van de gebruiker voor het uitvoeren van de apparatuur ongeldig maken.

#### <a name="canada-and-usa"></a>CANADA en USA:

> ![Meldings pictogram-3 ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **kennisgeving:** &nbsp; Dit apparaat is getest en er is vastgesteld dat het voldoet aan de limieten voor een digitaal apparaat van klasse a, overeenkomstig deel 15 van de FCC-regels. Deze limieten zijn ontworpen om redelijke bescherming te bieden tegen schadelijke interferentie wanneer de apparatuur wordt geëxploiteerd in een commerciële omgeving. Met deze apparatuur worden radio frequentie-energie gegenereerd, gebruikt en gestroomd en, indien niet geïnstalleerd en gebruikt in overeenstemming met de instructie handleiding, kan schadelijk interferentie voor radio communicatie veroorzaken. Het functioneren van deze apparatuur in een woon gebied veroorzaakt waarschijnlijk schadelijke interferentie, in welk geval de gebruiker de interferentie op eigen kosten moet corrigeren.

De Netgear A6150 WiFi USB-adapter die aan deze apparatuur wordt verstrekt, is bedoeld om te worden gebruikt voor de beschik bare specifieke absorptie frequentie (SAR-naleving). De door de FCC ingestelde SAR-limiet is 1,6 W/kg, wanneer het gemiddelde wordt berekend over 1 g weefsel. Houd bij het uitvoeren van het product of het gebruik ervan te maken met uw eigen lichaam, een afstand van 10 mm van de hoofd tekst om ervoor te zorgen dat aan de vereisten voor de bloot stelling aan RF wordt voldaan.

De Netgear A6150 WiFi USB-adapter voldoet aan ANSI/IEEE C 95.1-1999 en is getest volgens de meet methoden en procedures die zijn opgegeven in OET Bulletin 65 supplement C.

Netgear A6150 specifiek absorptie tempo (SAR): 1,18 W/kg gemiddeld meer dan 1 g weefsel

De Netgear A6150 WiFi USB-adapter moet alleen worden gebruikt met goedgekeurde antennes. Dit apparaat en de antenne (s) mogen zich niet in combi natie met een andere antenne of zender bevinden, behalve in overeenstemming met de product procedures van FCC multimitter. Voor producten die beschikbaar zijn in de USA-markt, kan alleen kanaal 1 ~ 11 worden gebruikt. Het is niet mogelijk om andere kanalen te selecteren.

De bewerking in de band 5150 – 5250 MHz is alleen voor gebruik voor de buiten zijde voor het beperken van de mogelijkheden voor schadelijke interferentie van systemen voor mobiele satellieten op co-Channel.

![Waarschuwing over reglementaire informatie-gebruik in de binnenshuis](./media/azure-stack-edge-mini-r-safety/regulatory-information-indoor-use-only.png)

Gebruikers worden geadviseerd dat hoogwaardige radar diagrammen worden toegewezen als primaire gebruikers (prioriteits gebruikers) van de banden 5250 – 5350 MHz en 5650-5850 MHz, en deze radar diagrammen kunnen storingen en/of schade veroorzaken op LE-LAN-apparaten.

Met deze apparatuur worden radio frequentie-energie gegenereerd, gebruikt en gestroomd en, indien niet geïnstalleerd en gebruikt in overeenstemming met de instructies, kan schadelijk storingen voor radio communicatie veroorzaken. Er is echter geen garantie dat storingen in een bepaalde installatie optreden.

Als deze apparatuur schadelijke interferentie veroorzaakt op radio-of televisie-ontvangst, wat kan worden bepaald door de apparatuur uit te scha kelen en aan, wordt de gebruiker aangemoedigd om de interferentie te corrigeren met een of meer van de volgende maat regelen:

- De ontvangende antenne opnieuw te richten of te verplaatsen.
- Verg root de schei ding tussen de apparatuur en de ontvanger.
- Verbind het apparaat met een stop op een ander circuit dan dat waarmee de ontvanger is verbonden.
- Raadpleeg de dealer of een ervaren radio/TV-technicus voor hulp.

Ga voor meer informatie over interferentie problemen naar de FCC-website op [fcc.gov/cgb/consumerfacts/interference.html](https://www.fcc.gov/consumers/guides/interference-radio-tv-and-telephone-signals). U kunt de FCC ook aanroepen op 1-888-CALL FCC om interferentie-en telefoon storingen op te vragen.

Meer informatie over radiofrequency-beveiliging vindt u op de website van de FCC op [https://www.fcc.gov/general/radio-frequency-safety-0](https://www.fcc.gov/general/radio-frequency-safety-0) en op de website van Canada op [http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html](http://www.ic.gc.ca/eic/site/smt-gst.nsf/eng/sf01904.html) .

Dit product heeft EMC-naleving bewezen onder voor waarden die het gebruik van compatibele rand apparaten en afgeschermde kabels tussen systeem onderdelen bevatten. Het is belang rijk dat u voldoet aan de rand apparaten en de afgeschermde kabels tussen systeem onderdelen om te voor komen dat er storingen worden veroorzaakt door radio's, televisie sets en andere elektronische apparaten.

Dit apparaat voldoet aan deel 15 van de FCC-regels en de bedrijfstak van de branche Canada-belaste RSS-norm (en). De bewerking is onderhevig aan de volgende twee voor waarden: (1) dit apparaat veroorzaakt mogelijk geen schadelijk interferentie, en (2) dit apparaat moet alle interferenties ontvangen, inclusief interferentie die een ongewenste werking van het apparaat kan veroorzaken.

![Waarschuwing voor reglementaire informatie 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

KAN ICES-3 (A)/NMB-3 (A)

Micro soft Corporation, One micro soft Way, Redmond, WA 98052, USA

Verenigde Staten: (800) 426-9400

Canada: (800) 933-4750

Netgear A6150 WiFi USB-adapter FCC-ID: PY318300429
 
IC-ID Netgear A6150 WiFi USB-adapter: 4054A-18300429

De Netgear A6150 WiFi USB-adapter die wordt meegeleverd met deze apparatuur is compatibel met SAR voor algemene blootstellings limieten van populatie/onbeheerd in IC RSS-102 en is getest volgens de meet methoden en procedures die zijn opgegeven in IEEE 1528. Onderhoud ten minste 10 mm afstand voor voor waarden die in de hoofd tekst worden gedragen.

De Netgear A6150 WiFi USB-adapter voldoet aan de voor een onbeheerde omgeving ingestelde blootstellings limiet voor Canada-draag bare RF en is veilig voor de beoogde werking, zoals beschreven in de hand leiding. Verdere RF-belichtings reductie kan worden bereikt door het product zoveel mogelijk van de hoofd tekst te bewaren of door het apparaat in te stellen op een lagere uitvoer kracht als een dergelijke functie beschikbaar is.

Een tabel met het specifieke absorptie aantal (SAR), gemiddeld meer dan 1 g voor elk product, kan worden weer gegeven in de sectie USA hierboven.

![Waarschuwing voor reglementaire informatie 2](./media/azure-stack-edge-mini-r-safety/regulatory-information-2.png)

#### <a name="european-union"></a>EUROPESE UNIE:

Vraag een kopie aan van de EU-verklaring van overeenstemming voor deze apparatuur. E-mail verzenden naar [CSI_Compliance@microsoft.com](mailto:CSI_Compliance@microsoft.com).

De Netgear A6150 WiFi USB-adapter die wordt meegeleverd met deze apparatuur is in overeenstemming met richt lijn 2014/53/EU en kan ook op aanvraag worden gegeven.

![Waarschuwings pictogram 13 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) **Waarschuwing:**  

Dit is een product klasse. In een binnenlandse omgeving kan dit product radio storingen veroorzaken in welk geval de gebruiker kan worden verplicht om passende maat regelen te nemen.

Verwijdering van afval accu's en elektrische en elektronische apparatuur:

![Waarschuwings pictogram 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Dit symbool op het product of de batterijen of het verpakkings materiaal houdt in dat dit product en eventuele accu's daarin niet mogen worden verwijderd met uw huishoudelijke afval. In plaats daarvan is het uw verantwoordelijkheid om deze over te dragen aan een toepasselijk verzamelings punt voor het recyclen van batterijen en elektrische en elektronische apparatuur. Met deze afzonderlijke verzameling en recycling kunnen natuurlijke bronnen worden bespaard en mogelijke negatieve gevolgen voor de gezondheid van de mens en het milieu worden voor komen als gevolg van de mogelijke aanwezigheid van gevaarlijke stoffen in accu's en elektrische en elektronische apparatuur. Dit kan worden veroorzaakt door een onjuiste verwijdering. Voor meer informatie over waar u uw batterijen en elektrische en elektronische afval stoffen weghaalt, neemt u contact op met uw lokale Office-woon plaats/gemeente, uw huisraad dienst voor onderhoud of de winkel waar u dit product hebt aangeschaft. Neem contact erecycle@microsoft.com op met aanvullende informatie over AEEA.

Dit product bevat een munt batterij (s).

De Netgear A6150 WiFi USB-adapter die bij deze apparatuur wordt verstrekt, is bedoeld om te worden gebruikt in de buurt van de menselijke hoofd tekst en wordt getest op basis van de naleving van het hoofd met een specifieke absorptie frequentie (SAR) (zie onder waarden). Houd bij het uitvoeren van het product of het gebruik ervan te maken met uw eigen lichaam, een afstand van 10mm uit de hoofd tekst om te zorgen voor compatibiliteit met RF-blootstellings vereisten.

**Netgear A6150 specifiek absorptie tempo (SAR):** 0,54 W/kg gemiddeld op 10g of weefsel

 
Dit apparaat kan worden gebruikt in alle lidstaten van de EU. Bekijk de nationale en lokale voor Schriften waar het apparaat wordt gebruikt. Dit apparaat is beperkt tot gebruik binnen het bereik van 5150-5350 MHz in de volgende landen:  

![EU-landen waarvoor alleen gebruik binnen de lands is vereist](./media/azure-stack-edge-mini-r-safety/mini-r-safety-eu-indoor-use-only.png)

Overeenkomstig artikel 10,8, punt a en 10,8 (b) van de rood, bevat de volgende tabel informatie over de gebruikte frequentie banden en de maximale RF-verzend kracht van Netgear draadloze producten voor verkoop in de EU:

**WiFi**

| Frequentie bereik (MHz) | Gebruikte kanalen | Maximale verzend kracht (dBm/mW) |
| --------------------- | ------------- | --------------------------- |
| 2400-2483.5 | 1-13    | ODFM: 19,9 dBm (97,7 mW) <br> CCK: 17,9 dBm (61,7 mW) |
| 5150-5320   | 36-48   | 22,9 dBm (195 mW) |
| 5250-5350   | 52-64   | 22,9 dBm (195 mW) met TPC <br> 19,9 dBm (97,7 mW) niet-TPC |
| 5470-5725   | 100-140 | 29,9 dBm (977 mW) met TPC <br> 29,6 dBm (490 mW) niet-TPC |

Micro soft Ireland Sandyford ind est Dublin D18 KX32 IRL

Telefoon nummer: + 353 1 295 3826

Faxnummer: + 353 1 706 4110

#### <a name="singapore"></a>Singapore:

De Netgear A6150 WiFi USB-adapter die wordt meegeleverd met deze apparatuur voldoet aan de IMDA-normen.


## <a name="next-steps"></a>Volgende stappen

- [Voorbereiden voor de implementatie van Azure Stack Edge mini maal R](azure-stack-edge-mini-r-deploy-prep.md)
