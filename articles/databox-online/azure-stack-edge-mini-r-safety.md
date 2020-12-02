---
title: Hand leiding voor de beveiliging van Azure Stack Edge-mini-R | Microsoft Docs
description: Hierin worden de veiligheids conventies, richt lijnen en overwegingen beschreven en wordt uitgelegd hoe u uw Azure Stack Edge mini-R-apparaat veilig kunt installeren en bedienen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: 507ceef0f13951eafdcb02d586f35c1d61764c4e
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466688"
---
# <a name="azure-stack-edge-mini-r-safety-instructions"></a>Beveiligings instructies voor de Azure Stack Edge mini-R

![Waarschuwings pictogram 1 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)
 ![ pictogram Lees beveiligings melding ](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png)
 **Lees veiligheid en status informatie**

Lees de informatie over de veiligheid in dit artikel voordat u uw Azure Stack Edge mini-R-apparaat gebruikt, een samen stelling van één batterij pakket, een wisselstroom voeding van AC/DC, één module stroom adapter en één server module. Het volgen van instructies kan leiden tot brand, elektrische schokken, verwondingen of beschadiging van uw eigenschappen. Lees alle onderstaande veiligheids informatie voordat u Azure Stack Edge mini-R gebruikt.

## <a name="safety-icon-conventions"></a>Conventies voor veiligheids pictogrammen

De volgende signaal woorden voor waarschuwings signalen voor waarschuwingen zijn:

| Pictogram | Beschrijving |
|:--- |:--- |
| ![Waarschuwings symbool](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png)| **Gevaar:** Duidt op een gevaarlijke situatie die, indien dat niet wordt vermeden, leidt tot dood of ernstige schade. <br> **Waarschuwing:** Duidt op een gevaarlijke situatie die, indien dat niet wordt vermeden, kan leiden tot overlijden of ernstige schade. <br> **Let op:** Duidt op een gevaarlijke situatie die, indien dat niet wordt vermeden, kan leiden tot kleine of gemiddelde schade.|
|

De volgende gevaren pictogrammen moeten worden waargenomen bij het instellen en uitvoeren van uw Azure Stack Edge mini-R-apparaat:

| Pictogram | Beschrijving |
|:--- |:--- |
| ![Lees eerst alle instructies](./media/azure-stack-edge-mini-r-safety/icon-safety-read-all-instructions.png) | Lees eerst alle instructies |
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

* Deze apparatuur bevat een Lithium batterij. Probeer het accu pack niet te onderhouden. Accu's in deze apparatuur zijn niet in gebruik. Risico op explosie als de accu wordt vervangen door een onjuist type.

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

Dit apparaat heeft twee SFP +-poorten die kunnen worden gebruikt met optische transceivers. Gebruik alleen met klasse 1-transceivers om gevaarlijke laser straling te voor komen.

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

> ![Opmerking ](./media/azure-stack-edge-mini-r-safety/icon-safety-notice.png) **:** &nbsp; wijzigingen of wijzigingen in het apparaat dat niet uitdrukkelijk door micro soft is goedgekeurd, kunnen de bevoegdheid van de gebruiker voor het uitvoeren van de apparatuur ongeldig maken.

CANADA en USA:

Opmerking: dit apparaat is getest en er is vastgesteld dat het voldoet aan de limieten voor een digitaal apparaat van klasse A, conform deel 15 van de FCC-regels. Deze limieten zijn ontworpen om redelijke bescherming te bieden tegen schadelijke interferentie wanneer de apparatuur wordt geëxploiteerd in een commerciële omgeving. Met deze apparatuur worden radio frequentie-energie gegenereerd, gebruikt en gestroomd en, indien niet geïnstalleerd en gebruikt in overeenstemming met de instructie handleiding, kan schadelijk interferentie voor radio communicatie veroorzaken. Het functioneren van deze apparatuur in een woon gebied veroorzaakt waarschijnlijk schadelijke interferentie, in welk geval de gebruiker de interferentie op eigen kosten moet corrigeren.

Dit apparaat voldoet aan deel 15 van de FCC-regels en de bedrijfstak van de branche Canada-belaste RSS-norm (en). De bewerking is onderhevig aan de volgende twee voor waarden: (1) dit apparaat veroorzaakt mogelijk geen schadelijk interferentie, en (2) dit apparaat moet alle interferenties ontvangen, inclusief interferentie die een ongewenste werking van het apparaat kan veroorzaken.

![Waarschuwing voor reglementaire informatie 1](./media/azure-stack-edge-mini-r-safety/regulatory-information-1.png)

KAN ICES-3 (A)/NMB-3 (A) micro soft Corporation, One micro soft Way, Redmond, WA 98052, Verenigde Staten.
Verenigde Staten: (800) 426-9400 Canada: (800) 933-4750

Europese Unie: vraag een kopie aan van de EU-verklaring van overeenstemming.

> ![Waarschuwings pictogram 13 ](./media/azure-stack-edge-mini-r-safety/icon-safety-warning.png) Dit is een klasse van een product. In een binnenlandse omgeving kan dit product radio storingen veroorzaken in welk geval de gebruiker kan worden verplicht om passende maat regelen te nemen.

Verwijdering van afval accu's en elektrische en elektronische apparatuur:

![Waarschuwings pictogram 14](./media/azure-stack-edge-mini-r-safety/icon-ewaste-disposal.png)

Dit symbool op het product of de batterijen of het verpakkings materiaal houdt in dat dit product en eventuele accu's daarin niet mogen worden verwijderd met uw huishoudelijke afval. In plaats daarvan is het uw verantwoordelijkheid om deze over te dragen aan een toepasselijk verzamelings punt voor het recyclen van batterijen en elektrische en elektronische apparatuur. Met deze afzonderlijke verzameling en recycling kunnen natuurlijke bronnen worden bespaard en mogelijke negatieve gevolgen voor de gezondheid van de mens en het milieu worden voor komen als gevolg van de mogelijke aanwezigheid van gevaarlijke stoffen in accu's en elektrische en elektronische apparatuur. Dit kan worden veroorzaakt door een onjuiste verwijdering. Voor meer informatie over waar u uw batterijen en elektrische en elektronische afval stoffen weghaalt, neemt u contact op met uw lokale Office-woon plaats/gemeente, uw huisraad dienst voor onderhoud of de winkel waar u dit product hebt aangeschaft. Neem contact erecycle@microsoft.com op met aanvullende informatie over AEEA.

Dit product bevat een munt batterij (s).
Micro soft Ireland Sandyford ind est Dublin D18 KX32 IRL telefoon nummer: + 353 1 295 3826 faxnummer: + 353 1 706 4110

## <a name="next-steps"></a>Volgende stappen

- [Voorbereiden voor de implementatie van Azure Stack Edge mini maal R](azure-stack-edge-mini-r-deploy-prep.md)
