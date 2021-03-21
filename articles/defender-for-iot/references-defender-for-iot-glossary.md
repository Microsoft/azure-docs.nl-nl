---
title: Defender for IoT-woordenlijst
description: Deze verklarende woorden lijst bevat een korte beschrijving van belang rijke voor waarden en concepten van Defender voor IoT-platform.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/09/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: f26cea9442aa3fbbe7f475cc5d16bea792b83fb3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493982"
---
# <a name="defender-for-iot-glossary"></a>Defender for IoT-woordenlijst

Deze verklarende woorden lijst bevat een korte beschrijving van belang rijke termen en concepten voor het Azure Defender voor IoT-platform. Selecteer de koppelingen **meer informatie** om naar gerelateerde voor waarden in de woorden lijst te gaan. Dit helpt u sneller meer te weten te komen en de product hulpprogramma's te gebruiken.

> [!Note]
> Elke term met een `(DB)` vermeld in de naam is een op agent gebaseerde Device Builder-term. 

<a name="glossary-a"></a>

## <a name="a"></a>A

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **Toegangs groep** | Ondersteuning voor gebruikers toegangs vereisten voor grote organisaties door regels voor toegangs groepen te maken.<br /><br />Met regels kunt u de weer gave-en configuratie toegang tot de Defender voor IoT on-premises beheer console voor specifieke gebruikers rollen in relevante bedrijfs eenheden, regio's, sites en zones beheren.<br /><br />U kunt bijvoorbeeld beveiligings analisten van een Active Directory groep toestaan om toegang te krijgen tot de gegevens van de West-Europese auto, maar geen toegang tot gegevens in Afrika te voor komen. | **[On-premises beheer console](#o)** <br /><br />**[Bedrijfseenheid](#b)** |
| **Toegangstokens** | Genereer toegangs tokens om toegang te krijgen tot de Defender voor IoT-REST API. | **[API](#glossary-a)** |
| **Waarschuwings gebeurtenis bevestigen** | Geef Defender voor IoT de waarschuwing voor de gedetecteerde gebeurtenis één keer weer. De waarschuwing wordt opnieuw geactiveerd als de gebeurtenis opnieuw wordt gedetecteerd. | **[](#glossary-a) <br /> Waarschuwing <br /> [Waarschuwings gebeurtenis leren](#l) <br /> <br /> [Waarschuwings gebeurtenis dempen](#m)** |
| **Waarschuwing** | Een bericht dat een Defender voor IoT-engine wordt geactiveerd met betrekking tot afwijkingen van geautoriseerd netwerk gedrag, netwerk afwijkingen of verdachte netwerk activiteit en verkeer. | **[Doorstuur regel](#f) <br /> <br /> [Uitsluitings regel](#e) <br /> <br /> [Systeem meldingen](#s)** |
| **Waarschuwings Opmerking** | Opmerkingen die beveiligings analisten en beheerders in waarschuwings berichten maken. Een waarschuwings opmerking kan bijvoorbeeld instructies geven over de maat regelen die moeten worden ondernomen of namen van personen om contact op te nemen met de gebeurtenis.<br /><br />Gebruikers die waarschuwingen controleren, kunnen de opmerkingen of de opmerkingen kiezen die het beste overeenkomen met de status van de gebeurtenis of stappen die zijn ondernomen om de waarschuwing te onderzoeken. | **[Waarschuwing](#glossary-a)** |
| **Afwijkende engine** | Een Defender voor IoT-engine die ongebruikelijke communicatie en gedrag van de machine naar de machine (M2M) detecteert. De engine kan bijvoorbeeld buitensporige SMB-aanmeldings pogingen detecteren. Er worden anomalie waarschuwingen geactiveerd wanneer deze gebeurtenissen worden gedetecteerd. | **[Defender voor IoT-engines](#d)** |
| **API** | Hiermee kunnen externe systemen toegang krijgen tot gegevens die door Defender voor IoT zijn gedetecteerd en acties worden uitgevoerd met behulp van de externe REST API via SSL-verbindingen. | **[Toegangstokens](#glossary-a)** |
| **Rapport over aanvals vector** | Een real-time grafische weer gave van de beveiligings ketens van exploitable-eind punten.<br /><br />Met rapporten kunt u het effect van de oplossings activiteiten in de aanvals volgorde evalueren om te bepalen. U kunt bijvoorbeeld evalueren of een systeem upgrade het pad van de aanvaller verstoort door de aanvals keten te verbreken of een alternatief pad naar een aanval te behouden. Hiermee worden de prioriteiten voor herstel en het beperken van problemen beschreven. | **[Rapport risico beoordeling](#r)** |

## <a name="b"></a>B

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **Bedrijfseenheid** | Een logische organisatie van uw bedrijf volgens specifieke branches.<br /><br />Zo kan een wereld wijd bedrijf dat glazen fabrieken en plastic fabrieken bevat, worden beheerd als twee verschillende bedrijfs eenheden. U kunt de toegang van Defender voor IoT-gebruikers beheren voor bepaalde bedrijfs eenheden. | **[On-premises beheer console](#o) <br /> <br /> [Toegangs groep](#glossary-a) <br /> <br /> [](#s) <br /> Site <br /> [Zone](#z)** |
| **Basislijn** | Goedgekeurd netwerk verkeer, protocollen, opdrachten en apparaten. Defender voor IoT identificeert afwijkingen van de netwerk basislijn. Goedgekeurd basislijn verkeer weer geven door analyse rapporten van gegevens te genereren. | **[Gegevens analyse](#d) <br /> <br /> [Leer modus](#l)** |

## <a name="c"></a>C

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **CLI-opdrachten** | Opties voor de opdracht regel interface (CLI) voor Defender voor IoT-beheerders gebruikers. CLI-opdrachten zijn beschikbaar voor functies die niet toegankelijk zijn vanuit de Defender voor IoT-consoles. | - |


## <a name="d"></a>D

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **Gegevens analyse** | Uitgebreide en gedetailleerde rapporten over uw netwerk apparaten genereren:<br /><br />- **Soc-incident respons**: rapporten in realtime om te helpen bij het direct reageren op incidenten. Een rapport kan bijvoorbeeld apparaten weer geven die mogelijk patches nodig hebben.<br /><br />- **Forensische**: rapporten op basis van historische gegevens voor onderzoek rapporten.<br /><br />- **IT-netwerk integriteit**: rapporten die helpen de algehele beveiliging van het netwerk te verbeteren. Een rapport kan bijvoorbeeld apparaten met zwakke verificatie referenties weer geven.<br /><br />- **zicht baarheid**: rapporten die alle query-items dekken om alle basislijn parameters van uw netwerk weer te geven.<br /><br />Sla rapporten voor gegevens analyse op voor weer gave met alleen-lezen-gebruikers. | **[Basis lijn](#b) <br /> <br /> [Rapporten](#r)** |
| **Defender voor IoT-engines** | Met de zelf studie analyse-engines in Defender voor IoT hoeft u geen hand tekeningen te hoeven bijwerken of regels te definiëren. De engines gebruiken ICS-specifieke gedrags analyse en data wetenschappen om voortdurend het netwerk verkeer te analyseren voor afwijkingen, malware, operationele problemen, schendingen van het protocol en afwijkingen van de activiteit van het basislijn netwerk.<br /><br />Wanneer een engine een afwijking detecteert, wordt er een waarschuwing geactiveerd. Waarschuwingen kunnen worden weer gegeven en beheerd vanuit het scherm **waarschuwingen** of vanuit een Siem. | **[Waarschuwing](#glossary-a)** |
| **Defender voor IoT-platform** | De Defender voor IoT-oplossing die is geïnstalleerd op Defender voor IoT Sens oren en de on-premises beheer console. | **[](#s) <br /> Sensor <br /> [On-premises beheer console](#o)** |
| **Apparaattoewijzing** | Een grafische weer gave van netwerk apparaten die Defender voor IoT detecteert. Hierin worden de verbindingen tussen apparaten en informatie over elk apparaat weer gegeven. Gebruik de kaart voor het volgende:<br /><br />-Essentiële apparaatgegevens ophalen en beheren.<br /><br />-Netwerk segmenten analyseren.<br /><br />-Details en samen vattingen van het apparaat exporteren. | **[Laag groep Purdue](#p)** |
| **Inventaris van apparaten-sensor** | De apparaat-inventaris toont een uitgebreid scala aan kenmerken van apparaten, gedetecteerd door Defender voor IoT. Opties zijn beschikbaar voor:<br /><br />-Weer gegeven informatie filteren.<br /><br />-Deze gegevens exporteren naar een CSV-bestand.<br /><br />-Details van het Windows-REGI ster importeren. | **[Groep](#g)** <br /><br />**[Inventaris van apparaten: on-premises beheer console](#d)** |
| **Inventaris van apparaten: on-premises beheer console** | Apparaatgegevens van verbonden Sens oren kunnen worden bekeken vanuit de on-premises beheer console in de inventaris van het apparaat. Dit geeft gebruikers van de on-premises beheer console een uitgebreid overzicht van alle netwerk gegevens. | **[Inventaris van apparaten-sensor](#d) <br /> <br /> [Apparaat-inventaris-gegevens integrator](#d)** |
| **Apparaat-inventaris-gegevens integrator** | Met de mogelijkheden voor gegevens integratie van de on-premises beheer console kunt u de gegevens in de inventaris van het apparaat verbeteren met informatie van andere bedrijfs resources. Voor beelden van resources zijn CMDB, DNS, firewalls en Web-Api's. | **[Inventaris van apparaten: on-premises beheer console](#d)** |
| **Apparaat apparaatdubbels**`(DB)` | Apparaatdubbels zijn JSON-documenten die status informatie van een apparaat opslaan, inclusief meta gegevens, configuraties en voor waarden. | [Module dubbele](#m) <br /> <br />[Defender-IoT-micro agent dubbele](#s) |

## <a name="e"></a>E

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **Bedrijfs weergave** | Een wereld wijde kaart die business units, sites en zones presenteert waarbij verdedigings nemers voor IoT Sens oren zijn geïnstalleerd. Bekijk geografische locaties van schadelijke waarschuwingen, operationele waarschuwingen en meer. | **[Bedrijfs eenheid](#b) <br /> <br /> [](#s) <br /> Site <br /> [Zone](#z)** |
| **Tijd lijn van gebeurtenis** | Er is een tijd lijn van de activiteit gedetecteerd in uw netwerk, met inbegrip van:<br /><br />-Waarschuwingen geactiveerd.<br /><br />-Netwerk gebeurtenissen (informatief).<br /><br />-Gebruikers bewerkingen, zoals aanmelden, verwijderen van gebruikers en het maken van gebruikers, en waarschuwingen voor waarschuwings beheer zoals dempen, leren en bevestigen. Beschikbaar in de sensor consoles. | - |
| **Uitsluitings regel** | Zorg ervoor dat Defender voor IoT waarschuwings triggers negeert op basis van de tijds periode, het adres van het apparaat en de naam van de waarschuwing, of door een specifieke sensor.<br /><br />Als u bijvoorbeeld weet dat alle binnenkomende apparaten die door een specifieke sensor worden bewaakt, een onderhouds procedure tussen 6:30 en 10:15 in de ochtend zullen door lopen, kunt u een uitsluitings regel instellen die aangeeft dat deze sensor geen waarschuwingen in de vooraf gedefinieerde periode moet verzenden. | **[](#glossary-a) <br /> Waarschuwing <br /> [Waarschuwings gebeurtenis dempen](#m)** |

## <a name="f"></a>F

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **Doorstuur regel** | Regels voor door sturen geven Defender voor IoT informatie over het verzenden van waarschuwings gegevens naar leveranciers of systemen van partners.<br /><br />U kunt bijvoorbeeld waarschuwings gegevens verzenden naar een Splunk-server of een syslog-server. | **[Waarschuwing](#glossary-a)** |

## <a name="g"></a>G

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **Groep** | Vooraf gedefinieerde of aangepaste groepen apparaten met specifieke kenmerken, zoals apparaten die programmeer activiteiten of apparaten die zich op een specifiek subnet bevinden, bevatten. Gebruik groepen om apparaten te bekijken en apparaten te analyseren in Defender voor IoT.<br /><br />Groepen kunnen worden weer gegeven in en gemaakt op basis van de apparaattoewijzing en de inventaris van apparaten. | **[](#d) <br /> Apparaattoewijzing <br /> [Inventaris van apparaten](#d)** |

## <a name="h"></a>H

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **Ontwikkelings omgeving horizon geopend** | Beveilig IoT-en ICS-apparaten met eigen en aangepaste protocollen of protocollen die afwijken van de standaard. Gebruik de node-SDK (Open Development Environment) voor het ontwikkelen van invoeg toepassingen die netwerk verkeer decoderen op basis van gedefinieerde protocollen. Verdedigings functies voor IoT-Services analyseren verkeer om te zorgen voor volledige bewaking, waarschuwingen en rapportage.<br /><br />Gebruik de Horizony tot:<br /><br />- **Breid** zicht baarheid en beheer uit zonder de nood zaak om Defender voor IOT-platform versies te upgraden.<br /><br />- **Beveilig** eigendoms gegevens door on-site te ontwikkelen als een externe invoeg toepassing.<br /><br />- **Lokalisatie** van tekst voor waarschuwingen, gebeurtenissen en protocol parameters.<br /><br />Neem contact op met het succes van de klant voor meer informatie. | **[Protocol ondersteuning](#p) <br /> <br /> [Lokalisatie](#l)** |
| **Aangepaste waarschuwing van Horizon** | Verbeter het beheer van waarschuwingen in uw onderneming door aangepaste waarschuwingen te activeren voor elk protocol (op basis van het niveau van Horizon-Framework-verkeer).<br /><br />Deze waarschuwingen kunnen worden gebruikt om informatie te communiceren:<br /><br />-Over verkeers detecties op basis van protocollen en onderliggende protocollen in een eigen sluit-invoeg toepassing.<br /><br />-Over een combi natie van protocol velden van alle protocol lagen. | **[Protocol ondersteuning](#p)** |

## <a name="i"></a>I

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **IoT Hub** `(DB)` | Beheerde service, gehost in de Cloud, die fungeert als een centrale Message hub voor bidirectionele communicatie tussen uw IoT-toepassing en de apparaten die worden beheerd.  |   |
| **Integraties** | Vouw Defender voor IoT-mogelijkheden uit door de apparaatgegevens te delen met partner systemen. Organisaties kunnen voorheen gesilote beveiliging, NAC, incident beheer en oplossingen voor Apparaatbeheer voor komen om de systeem brede reacties te versnellen en zo snel mogelijk Risico's te verminderen. | **[Doorstuur regel](#f)** |
| **Intern subnet** | Subnet-configuraties die zijn gedefinieerd door Defender voor IoT. In sommige gevallen, zoals omgevingen die gebruikmaken van open bare bereiken als interne bereiken, kunt u ervoor zorgen dat Defender voor IoT alle subnetten kan omzetten als interne subnetten. Subnetten worden weer gegeven in de kaart en in verschillende Defender voor IoT-rapporten. | **[Subnetten](#s)** |

## <a name="l"></a>L

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **Waarschuwings gebeurtenis leren** | Geef Defender voor IoT opdracht om het verkeer dat in een waarschuwings gebeurtenis wordt gedetecteerd, te autoriseren. | **[](#glossary-a) <br /> Waarschuwing <br /> [Waarschuwings gebeurtenis bevestigen](#glossary-a) <br /> <br /> [Waarschuwings gebeurtenis dempen](#m)** |
| **Leer modus** | De modus die wordt gebruikt wanneer Defender voor IoT uw netwerk activiteit leert. Deze activiteit wordt uw netwerk basislijn. Defender voor IoT blijft in de modus voor een vooraf gedefinieerde periode na de installatie. Activiteit die van de geleerde activiteit na deze periode verschilt, wordt Defender geactiveerd voor IoT-waarschuwingen. | **[Slim it-Learning](#s) <br /> <br /> [Basis lijn](#b)** |
| **Lokalisatie** | Lokalisatie van de tekst voor waarschuwingen, gebeurtenissen en protocol parameters voor invoeg toepassingen die zijn ontwikkeld door horizon. | **[Ontwikkelings omgeving horizon geopend](#h)** |

## <a name="m"></a>M


| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **Micro agent**`(DB)` | Biedt uitgebreide beveiligings mogelijkheden voor IoT-apparaten, waaronder beveiligings-postuur en detectie van bedreigingen. | |
| **Module dubbele**`(DB)` | Moduledubbels zijn JSON-documenten waarin statusinformatie van een module wordt opgeslagen, zoals metagegevens, configuraties en voorwaarden. | [Dubbel apparaat](#d) <br /> <br />[Defender-IoT-micro agent dubbele](#s) |
| **Waarschuwings gebeurtenis dempen** | Geef Defender voor IoT de activiteit continu negeren met identieke apparaten en vergelijkbaar verkeer. | **[](#glossary-a) <br /> Waarschuwing <br /> [Uitsluitings regel](#e) <br /> <br /> [Waarschuwings gebeurtenis bevestigen](#glossary-a) <br /> <br /> [Waarschuwings gebeurtenis leren](#l)** |

## <a name="n"></a>N

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **Meldingen** | Informatie over netwerk wijzigingen of onopgeloste Apparaateigenschappen. Er zijn opties beschikbaar voor het bijwerken van apparaat-en netwerk gegevens met nieuwe gegevens gedetecteerd. Reageren op meldingen verrijkt de inventarisatie van het apparaat, de kaart en verschillende rapporten. Beschikbaar op sensor consoles. | **[](#glossary-a) <br /> Waarschuwing <br /> [Systeem meldingen](#s)** |

## <a name="o"></a>O

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **On-premises beheer console** | De on-premises beheer console biedt een gecentraliseerde weer gave en beheer van apparaten en bedreigingen die voor de implementatie van een IoT-sensor worden gedetecteerd in uw organisatie. | **[Defender voor IOT-platform](#d) <br /> <br /> [Sensor](#s)** |
| **Operationele waarschuwing** | Waarschuwingen met betrekking tot operationele netwerk problemen, zoals een apparaat waarvan wordt vermoed dat ze van het netwerk worden losgekoppeld. | **[](#glossary-a) <br /> Waarschuwing <br /> [Beveiligings waarschuwing](#s)** |

## <a name="p"></a>P

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **Purdue-laag** | Toont de onderlinge verbindingen en onderlinge afhankelijkheden van de belangrijkste onderdelen van een standaard ICS op de kaart. |  |
| **Protocol ondersteuning** | Naast ondersteuning voor Inge sloten protocol kunt u IoT-en ICS-apparaten beveiligen die gebruikmaken van eigen en aangepaste protocollen, of protocollen die afwijken van een van de standaarden, met behulp van de Horizony Open Development Environment SDK. | **[Ontwikkelings omgeving horizon geopend](#h)** |

## <a name="r"></a>R

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **Regio** | Een logische divisie van een wereld wijde organisatie in geografische regio's. Voor beelden zijn Noord-Amerika, West-Europa en Oost-Europa.<br /><br />Noord-Amerika kunnen fabrieken van verschillende bedrijfs eenheden bevatten. | **[Toegangs groep](#glossary-a) <br /> <br /> [Bedrijfs eenheid](#b) <br /> <br /> [On-premises beheer console](#o) <br /> <br /> [](#s) <br /> Site <br /> [Zone](#z)** |
| **Rapporten** | Rapporten geven informatie weer die is gegenereerd door query resultaten van de gegevens analyse. Dit omvat standaard resultaten voor gegevens analyse, die beschikbaar zijn in de weer gave **rapporten** . Beheerders en beveiligings analisten kunnen ook aangepaste query's voor gegevens analyse genereren en deze opslaan als rapporten. Deze rapporten zijn ook beschikbaar voor gebruikers met het kenmerk alleen-lezen. | **[Gegevens analyse](#d)** |
| **Rapport risico beoordeling** | Met rapportage voor risico analyse kunt u een beveiligings Score genereren voor elk netwerk apparaat, samen met een algehele netwerk beveiligings Score. De totale score geeft het percentage van 100 procent beveiliging. Het rapport bevat aanbevelingen voor het beperken van beperkingen waarmee u uw huidige beveiligings Score kunt verbeteren. | - |

## <a name="s"></a>S

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **Beveiligings waarschuwing** | Waarschuwingen die betrekking hebben op beveiligings problemen, zoals buitensporige SMB-aanmeldingen in pogingen of detecties van malware. | **[](#glossary-a) <br /> Waarschuwing <br /> [Operationele waarschuwing](#o)** |
| **Defender-IOT-micro agent dubbele**`(DB)` | De Defender-IoT-micro agent is van toepassing op alle informatie die relevant is voor de beveiliging van apparaten, voor elk specifiek apparaat in uw oplossing. | [Dubbel apparaat](#d) <br /> <br />[Module dubbele](#m)  |
| **Selectief zoeken** | Defender voor IoT bewaakt passief inspecteert dit en verkeer en detecteert relevante informatie over apparaten, hun kenmerken, hun gedrag en meer. In bepaalde gevallen is bepaalde informatie mogelijk niet zichtbaar in passieve netwerk analyses.<br /><br />Als dit gebeurt, kunt u de veilige, granulaire probing-hulpprogram ma's in Defender voor IoT gebruiken om belang rijke informatie te ontdekken over voorheen onbereikbare apparaten. | - |
| **Sensoren** | De fysieke of virtuele machine waarop het Defender voor IoT-platform is geïnstalleerd. | **[On-premises beheer console](#o)** |
| **Site** | Een locatie die een Factory of andere entiteit is. De site moet een zone of meerdere zones bevatten waarin een sensor is geïnstalleerd. | **[Zone](#z)** |
| **Site beheer** | De optie on-premises beheer console waarmee u Enter prise Sens oren kunt beheren. | - |
| **Slim IT-Learning** | Nadat de leer periode is voltooid en de leer modus is uitgeschakeld, kan Defender voor IoT een ongebruikelijk hoog niveau van basislijn wijzigingen detecteren die het resultaat zijn van de normale IT-activiteit, zoals DNS-en HTTP-aanvragen. Dit verkeer kan onnodige waarschuwingen voor beleids schendingen en systeem meldingen activeren. Als u deze waarschuwingen en meldingen wilt beperken, kunt u slim IT-Learning inschakelen. | **[Leer modus](#l) <br /> <br /> [Basis lijn](#b)** |
| **Subnetten** | Voor het inschakelen van focus op het OT, worden de IT-apparaten automatisch geaggregeerd door het subnet in de apparaattoewijzing. Elk subnet wordt weer gegeven als één entiteit op de kaart, met inbegrip van een interactieve samengevouwen of uitbreid bare mogelijkheid om naar een IT-subnet en terug te richten. | **[Apparaattoewijzing](#d)** |
| **Systeem meldingen** | Meldingen van de on-premises beheer console herindeling:<br /><br />-Verbindings status van de sensor.<br /><br />-Externe back-upfouten. | **[](#n) <br /> Meldingen <br /> [Waarschuwing](#glossary-a)** |

## <a name="z"></a>Z

| Termijn | Beschrijving | Meer informatie |
|--|--|--|
| **Zone** | Een gebied binnen een site waarin een sensor of Sens oren zijn geïnstalleerd. | **[](#s) <br /> Site <br /> [Bedrijfs eenheid](#b) <br /> <br /> [Regio](#r)** |
