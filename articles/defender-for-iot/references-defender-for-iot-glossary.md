---
title: Defender voor IoT-woorden lijst
description: Deze verklarende woorden lijst bevat een korte beschrijving van belang rijke voor waarden en concepten van Defender voor IoT-platform.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/09/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 7896237b1f9e9e66659532422efb3449c41ede11
ms.sourcegitcommit: 5ef018fdadd854c8a3c360743245c44d306e470d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/01/2021
ms.locfileid: "97845293"
---
# <a name="defender-for-iot-glossary"></a>Defender voor IoT-woorden lijst

Deze verklarende woorden lijst bevat een korte beschrijving van belang rijke termen en concepten voor het Azure Defender voor IoT-platform. Selecteer de koppelingen **meer informatie** om naar gerelateerde voor waarden in de woorden lijst te gaan. Dit helpt u sneller meer te weten te komen en de product hulpprogramma's te gebruiken.

## <a name="a"></a>A

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **toegangs groep** | Ondersteuning voor gebruikers toegangs vereisten voor grote organisaties door regels voor toegangs groepen te maken.<br /><br />Met regels kunt u de weer gave-en configuratie toegang tot de Defender voor IoT on-premises beheer console voor specifieke gebruikers rollen in relevante bedrijfs eenheden, regio's, sites en zones beheren.<br /><br />U kunt bijvoorbeeld beveiligings analisten van een Active Directory groep toestaan om toegang te krijgen tot de gegevens van de West-Europese auto, maar geen toegang tot gegevens in Afrika te voor komen. | **on-premises beheer console <br /> <br /> bedrijfs eenheid** |
| **toegangs tokens** | Genereer toegangs tokens om toegang te krijgen tot de Defender voor IoT-REST API. | **API** |
| **Waarschuwings gebeurtenis bevestigen** | Geef Defender voor IoT de waarschuwing voor de gedetecteerde gebeurtenis één keer weer. De waarschuwing wordt opnieuw geactiveerd als de gebeurtenis opnieuw wordt gedetecteerd. | **waarschuwing voor waarschuwings <br /> <br /> gebeurtenis <br /> <br /> Dempen waarschuwing gebeurtenis** |
| **waarschuwing** | Een bericht dat een Defender voor IoT-engine wordt geactiveerd met betrekking tot afwijkingen van geautoriseerd netwerk gedrag, netwerk afwijkingen of verdachte netwerk activiteit en verkeer. | **<br /> <br /> uitsluitings regel <br /> <br /> systeem meldingen voor doorstuur regel** |
| **waarschuwings Opmerking** | Opmerkingen die beveiligings analisten en beheerders in waarschuwings berichten maken. Een waarschuwings opmerking kan bijvoorbeeld instructies geven over de maat regelen die moeten worden ondernomen of namen van personen om contact op te nemen met de gebeurtenis.<br /><br />Gebruikers die waarschuwingen controleren, kunnen de opmerkingen of de opmerkingen kiezen die het beste overeenkomen met de status van de gebeurtenis of stappen die zijn ondernomen om de waarschuwing te onderzoeken. | **waarschuwing** |
| **afwijkende engine** | Een Defender voor IoT-engine die ongebruikelijke communicatie en gedrag van de machine naar de machine (M2M) detecteert. De engine kan bijvoorbeeld buitensporige SMB-aanmeldings pogingen detecteren. Er worden anomalie waarschuwingen geactiveerd wanneer deze gebeurtenissen worden gedetecteerd. | **Defender voor IoT-engines** |
| **API** | Hiermee kunnen externe systemen toegang krijgen tot gegevens die door Defender voor IoT zijn gedetecteerd en acties worden uitgevoerd met behulp van de externe REST API via SSL-verbindingen. | **toegangs tokens** |
| **rapport over aanvals vector** | Een real-time grafische weer gave van de beveiligings ketens van exploitable-eind punten.<br /><br />Met rapporten kunt u het effect van de oplossings activiteiten in de aanvals volgorde evalueren om te bepalen. U kunt bijvoorbeeld evalueren of een systeem upgrade het pad van de aanvaller verstoort door de aanvals keten te verbreken of een alternatief pad naar een aanval te behouden. Hiermee worden de prioriteiten voor herstel en het beperken van problemen beschreven. | **rapport risico beoordeling** |

## <a name="b"></a>B

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **bedrijfs eenheid** | Een logische organisatie van uw bedrijf volgens specifieke branches.<br /><br />Zo kan een wereld wijd bedrijf dat glazen fabrieken en plastic fabrieken bevat, worden beheerd als twee verschillende bedrijfs eenheden. U kunt de toegang van Defender voor IoT-gebruikers beheren voor bepaalde bedrijfs eenheden. | **on-premises beheer console <br /> <br /> <br /> <br /> site <br /> <br /> zone toegangs groep** |
| **gebonden** | Goedgekeurd netwerk verkeer, protocollen, opdrachten en apparaten. Defender voor IoT identificeert afwijkingen van de netwerk basislijn. Goedgekeurd basislijn verkeer weer geven door analyse rapporten van gegevens te genereren. | **<br /> <br /> leer modus voor gegevens analyse** |

## <a name="c"></a>C

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **on-premises beheer console** | De on-premises beheer console biedt een gecentraliseerde weer gave en beheer van apparaten en bedreigingen die worden gedetecteerd door de implementatie van Defender voor IoT-sensors in uw organisatie. | **Defender voor IoT-platform <br /> <br /> sensor** |
| **CLI-opdrachten** | Opties voor de opdracht regel interface (CLI) voor Defender voor IoT-beheerders gebruikers. CLI-opdrachten zijn beschikbaar voor functies die niet toegankelijk zijn vanuit de Defender voor IoT-consoles. | - |


## <a name="d"></a>D

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **gegevens analyse** | Uitgebreide en gedetailleerde rapporten over uw netwerk apparaten genereren:<br /><br />- **Soc-incident respons**: rapporten in realtime om te helpen bij het direct reageren op incidenten. Een rapport kan bijvoorbeeld apparaten weer geven die mogelijk patches nodig hebben.<br /><br />- **forensische**: rapporten op basis van historische gegevens voor onderzoek rapporten.<br /><br />- **IT-netwerk integriteit**: rapporten die helpen de algehele beveiliging van het netwerk te verbeteren. Een rapport kan bijvoorbeeld apparaten met zwakke verificatie referenties weer geven.<br /><br />- **zicht baarheid**: rapporten die alle query-items dekken om alle basislijn parameters van uw netwerk weer te geven.<br /><br />Sla rapporten voor gegevens analyse op voor weer gave met alleen-lezen-gebruikers. | **basislijn <br /> <br /> rapporten** |
| **Defender voor IoT-engines** | Met de zelf studie analyse-engines in Defender voor IoT hoeft u geen hand tekeningen te hoeven bijwerken of regels te definiëren. De engines gebruiken ICS-specifieke gedrags analyse en data wetenschappen om voortdurend het netwerk verkeer te analyseren voor afwijkingen, malware, operationele problemen, schendingen van het protocol en afwijkingen van de activiteit van het basislijn netwerk.<br /><br />Wanneer een engine een afwijking detecteert, wordt er een waarschuwing geactiveerd. Waarschuwingen kunnen worden weer gegeven en beheerd vanuit het scherm **waarschuwingen** of vanuit een Siem. | **waarschuwing** |
| **Defender voor IoT-platform** | De Defender voor IoT-oplossing die is geïnstalleerd op Defender voor IoT Sens oren en de on-premises beheer console. | **<br /> <br /> on-premises beheer console sensor** |
| **apparaattoewijzing** | Een grafische weer gave van netwerk apparaten die Defender voor IoT detecteert. Hierin worden de verbindingen tussen apparaten en informatie over elk apparaat weer gegeven. Gebruik de kaart voor het volgende:<br /><br />-Essentiële apparaatgegevens ophalen en beheren.<br /><br />-Netwerk segmenten analyseren.<br /><br />-Details en samen vattingen van het apparaat exporteren. | **Laag groep Purdue** |
| **inventaris van apparaten-sensor** | De apparaat-inventaris toont een uitgebreid scala aan kenmerken van apparaten, gedetecteerd door Defender voor IoT. Opties zijn beschikbaar voor:<br /><br />-Weer gegeven informatie filteren.<br /><br />-Deze gegevens exporteren naar een CSV-bestand.<br /><br />-Details van het Windows-REGI ster importeren. | **inventaris van apparaten groeperen-on-premises beheer console** |
| **inventaris van apparaten: on-premises beheer console** | Apparaatgegevens van verbonden Sens oren kunnen worden bekeken vanuit de on-premises beheer console in de inventaris van het apparaat. Dit geeft gebruikers van de on-premises beheer console een uitgebreid overzicht van alle netwerk gegevens. | **inventaris van apparaat-sensor <br /> <br /> apparaat-data integrator** |
| **apparaat-inventaris-gegevens integrator** | Met de mogelijkheden voor gegevens integratie van de on-premises beheer console kunt u de gegevens in de inventaris van het apparaat verbeteren met informatie van andere bedrijfs resources. Voor beelden van resources zijn CMDB, DNS, firewalls en Web-Api's. | **inventaris van apparaten: on-premises beheer console** |

## <a name="e"></a>E

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **bedrijfs weergave** | Een globale kaart die business units, sites en zones weergeeft waarin Defender voor IoT Sens oren is geïnstalleerd. Bekijk geografische locaties van schadelijke waarschuwingen, operationele waarschuwingen en meer. | **<br /> <br /> site <br /> <br /> zone bedrijfs eenheid** |
| **tijd lijn van gebeurtenis** | Er is een tijd lijn van de activiteit gedetecteerd in uw netwerk, met inbegrip van:<br /><br />-Waarschuwingen geactiveerd.<br /><br />-Netwerk gebeurtenissen (informatief).<br /><br />-Gebruikers bewerkingen, zoals aanmelding, het verwijderen van gebruikers en het maken van gebruikers, en het beheer van waarschuwingen, zoals dempen, leren en bevestigen. Beschikbaar in de sensor consoles. | - |
| **uitsluitings regel** | Zorg ervoor dat Defender voor IoT waarschuwings triggers negeert op basis van de tijds periode, het adres van het apparaat en de naam van de waarschuwing, of door een specifieke sensor.<br /><br />Als u bijvoorbeeld weet dat alle binnenkomende apparaten die door een specifieke sensor worden bewaakt, een onderhouds procedure tussen 6:30 en 10:15 in de ochtend zullen door lopen, kunt u een uitsluitings regel instellen die aangeeft dat deze sensor geen waarschuwingen in de vooraf gedefinieerde periode moet verzenden. | **waarschuwings gebeurtenis voor waarschuwing <br /> <br /> Dempen** |

## <a name="f"></a>F

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **doorstuur regel** | Regels voor door sturen geven Defender voor IoT informatie over het verzenden van waarschuwings gegevens naar leveranciers of systemen van partners.<br /><br />U kunt bijvoorbeeld waarschuwings gegevens verzenden naar een Splunk-server of een syslog-server. | **waarschuwing** |

## <a name="g"></a>G

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **groep** | Vooraf gedefinieerde of aangepaste groepen apparaten met specifieke kenmerken, zoals apparaten die programmeer activiteiten of apparaten die zich op een specifiek subnet bevinden, bevatten. Gebruik groepen om apparaten te bekijken en apparaten te analyseren in Defender voor IoT.<br /><br />Groepen kunnen worden weer gegeven in en gemaakt op basis van de apparaattoewijzing en de inventaris van apparaten. | **apparaat- <br /> <br /> inventaris voor apparaten toewijzen** |

## <a name="h"></a>H

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **Ontwikkelings omgeving horizon geopend** | Beveilig IoT-en ICS-apparaten met eigen en aangepaste protocollen of protocollen die afwijken van de standaard. Gebruik de node-SDK (Open Development Environment) voor het ontwikkelen van invoeg toepassingen die netwerk verkeer decoderen op basis van gedefinieerde protocollen. Defender voor IoT-Services analyseren verkeer om te zorgen voor volledige bewaking, waarschuwingen en rapportage.<br /><br />Gebruik de Horizony tot:<br /><br />- **Breid** zicht baarheid en beheer uit zonder de nood zaak om Defender voor IOT-platform versies te upgraden.<br /><br />- **Beveilig** eigendoms gegevens door on-site te ontwikkelen als een externe invoeg toepassing.<br /><br />- **Lokalisatie** van tekst voor waarschuwingen, gebeurtenissen en protocol parameters.<br /><br />Neem contact op met het succes van de klant voor meer informatie. | **lokalisatie van Protocol ondersteuning <br /> <br />** |
| **Aangepaste waarschuwing van Horizon** | Verbeter het beheer van waarschuwingen in uw onderneming door aangepaste waarschuwingen te activeren voor elk protocol (op basis van het niveau van Horizon-Framework-verkeer).<br /><br />Deze waarschuwingen kunnen worden gebruikt om informatie te communiceren:<br /><br />-Over verkeers detecties op basis van protocollen en onderliggende protocollen in een eigen sluit-invoeg toepassing.<br /><br />-Over een combi natie van protocol velden van alle protocol lagen. | **Protocol ondersteuning** |

## <a name="i"></a>I

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **integraties** | Vouw Defender voor IoT-mogelijkheden uit door de apparaatgegevens te delen met partner systemen. Organisaties kunnen voorheen gesilote beveiliging, NAC, incident beheer en oplossingen voor Apparaatbeheer voor komen om de systeem brede reacties te versnellen en zo snel mogelijk Risico's te verminderen. | **doorstuur regel** |
| **intern subnet** | Subnet-configuraties die zijn gedefinieerd door Defender voor IoT. In sommige gevallen, zoals omgevingen die gebruikmaken van open bare bereiken als interne bereiken, kunt u ervoor zorgen dat Defender voor IoT alle subnetten kan omzetten als interne subnetten. Subnetten worden weer gegeven in de kaart en in verschillende Defender voor IoT-rapporten. | **subnetten** |

## <a name="l"></a>L

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **Waarschuwings gebeurtenis leren** | Geef Defender voor IoT opdracht om het verkeer dat in een waarschuwings gebeurtenis wordt gedetecteerd, te autoriseren. | **waarschuwings gebeurtenis voor waarschuwing bij waarschuwing <br /> <br /> bevestigen <br /> <br />** |
| **Leer modus** | De modus die wordt gebruikt wanneer Defender voor IoT uw netwerk activiteit leert. Deze activiteit wordt uw netwerk basislijn. Defender voor IoT blijft in de modus voor een vooraf gedefinieerde periode na de installatie. Activiteit die van de geleerde activiteit na deze periode verschilt, wordt Defender geactiveerd voor IoT-waarschuwingen. | **<br />Basis lijn voor slim it-Learning <br />** |
| **localisatie** | Lokalisatie van de tekst voor waarschuwingen, gebeurtenissen en protocol parameters voor invoeg toepassingen die zijn ontwikkeld door horizon. | **Ontwikkelings omgeving horizon geopend** |

## <a name="m"></a>M

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **Waarschuwings gebeurtenis dempen** | Geef Defender voor IoT de activiteit continu negeren met identieke apparaten en vergelijkbaar verkeer. | **waarschuwing voor de regel voor het uitsluiten van waarschuwingen <br /> <br /> <br /> <br /> gebeurtenis <br /> <br /> ontdekken waarschuwing gebeurtenis** |

## <a name="n"></a>N

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **meldingen** | Informatie over netwerk wijzigingen of onopgeloste Apparaateigenschappen. Er zijn opties beschikbaar voor het bijwerken van apparaat-en netwerk gegevens met nieuwe gegevens gedetecteerd. Reageren op meldingen verrijkt de inventarisatie van het apparaat, de kaart en verschillende rapporten. Beschikbaar op sensor consoles. | **waarschuwingen <br /> <br /> systeem meldingen** |

## <a name="o"></a>O

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **operationele waarschuwing** | Waarschuwingen met betrekking tot operationele netwerk problemen, zoals een apparaat waarvan wordt vermoed dat ze van het netwerk worden losgekoppeld. | **waarschuwing <br /> <br /> beveiligings waarschuwing** |

## <a name="p"></a>P

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **Purdue-laag** | Toont de onderlinge verbindingen en onderlinge afhankelijkheden van de belangrijkste onderdelen van een standaard ICS op de kaart. |  |
| **Protocol ondersteuning** | Naast ondersteuning voor Inge sloten protocol kunt u IoT-en ICS-apparaten beveiligen die gebruikmaken van eigen en aangepaste protocollen, of protocollen die afwijken van een van de standaarden, met behulp van de Horizony Open Development Environment SDK. | **Ontwikkelings omgeving horizon geopend** |

## <a name="r"></a>R

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **regio** | Een logische divisie van een wereld wijde organisatie in geografische regio's. Voor beelden zijn Noord-Amerika, West-Europa en Oost-Europa.<br /><br />Noord-Amerika kunnen fabrieken van verschillende bedrijfs eenheden bevatten. | **<br /> <br /> <br /> <br /> <br /> <br /> site <br /> <br /> zone van de on-premises beheer console van de Business Unit-toegangs groep** |
| **rapporten** | Rapporten geven informatie weer die is gegenereerd door query resultaten van de gegevens analyse. Dit omvat standaard resultaten voor gegevens analyse, die beschikbaar zijn in de weer gave **rapporten** . Beheerders en beveiligings analisten kunnen ook aangepaste query's voor gegevens analyse genereren en deze opslaan als rapporten. Deze rapporten zijn ook beschikbaar voor gebruikers met het kenmerk alleen-lezen. | **gegevens analyse** |
| **rapport risico beoordeling** | Met rapportage voor risico analyse kunt u een beveiligings Score genereren voor elk netwerk apparaat, samen met een algehele netwerk beveiligings Score. De totale score geeft het percentage van 100 procent beveiliging. Het rapport bevat aanbevelingen voor het beperken van beperkingen waarmee u uw huidige beveiligings Score kunt verbeteren. | - |

## <a name="s"></a>S

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **beveiligings waarschuwing** | Waarschuwingen die betrekking hebben op beveiligings problemen, zoals buitensporige pogingen voor SMB-aanmelding of detectie van malware. | **<br /> <br /> operationele waarschuwing voor waarschuwing** |
| **selectief zoeken** | Defender voor IoT bewaakt passief inspecteert dit en verkeer en detecteert relevante informatie over apparaten, hun kenmerken, hun gedrag en meer. In bepaalde gevallen is bepaalde informatie mogelijk niet zichtbaar in passieve netwerk analyses.<br /><br />Als dit gebeurt, kunt u de veilige, granulaire probing-hulpprogram ma's in Defender voor IoT gebruiken om belang rijke informatie te ontdekken over voorheen onbereikbare apparaten. | - |
| **sensor** | De fysieke of virtuele machine waarop het Defender voor IoT-platform is geïnstalleerd. | **on-premises beheer console** |
| **site** | Een locatie die een Factory of andere entiteit is. De site moet een zone of meerdere zones bevatten waarin een sensor is geïnstalleerd. | **gebied** |
| **Site beheer** | De optie on-premises beheer console waarmee u Enter prise Sens oren kunt beheren. | - |
| **Slim IT-Learning** | Nadat de leer periode is voltooid en de leer modus is uitgeschakeld, kan Defender voor IoT een ongebruikelijk hoog niveau van basislijn wijzigingen detecteren die het resultaat zijn van de normale IT-activiteit, zoals DNS-en HTTP-aanvragen. Dit verkeer kan onnodige waarschuwingen voor beleids schendingen en systeem meldingen activeren. Als u deze waarschuwingen en meldingen wilt beperken, kunt u slim IT-Learning inschakelen. | **<br />basis lijn van de leer modus <br />** |
| **subnetten** | Voor het inschakelen van focus op het OT, worden de IT-apparaten automatisch geaggregeerd door het subnet in de apparaattoewijzing. Elk subnet wordt weer gegeven als één entiteit op de kaart, met inbegrip van een interactieve samengevouwen of uitbreid bare mogelijkheid om naar een IT-subnet en terug te richten. | **apparaattoewijzing** |
| **systeem meldingen** | Meldingen van de on-premises beheer console herindeling:<br /><br />-Verbindings status van de sensor.<br /><br />-Externe back-upfouten. | **<br /> <br /> meldings waarschuwing** |

## <a name="z"></a>Z

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **gebied** | Een gebied binnen een site waarin een sensor of Sens oren zijn geïnstalleerd. | **<br /> <br /> regio bedrijfs eenheid <br /> <br /> site** |
