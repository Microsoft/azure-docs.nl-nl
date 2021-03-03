---
title: Prijsscenario's voor oproepen (spraak/video) en chatten
titleSuffix: An Azure Communication Services concept document
description: Meer informatie over het prijsmodel voor Communication Services.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/29/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: bf255837a08a4810321f2f03cb97838e31a7cddc
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657754"
---
# <a name="pricing-scenarios"></a>Prijsscenario's

Prijzen voor Azure Communication Services zijn doorgaans gebaseerd op een model voor betalen per gebruik. De prijzen in de volgende voor beelden zijn bedoeld ter illustratie en komen mogelijk niet overeen met de nieuwste prijzen voor Azure.

## <a name="voicevideo-calling-and-screen-sharing"></a>Spraak-/video-oproepen en scherm delen

Met Azure Communication Services kunt u functionaliteit voor spraak-/video-oproepen en scherm delen aan uw toepassingen toevoegen. U kunt de ervaring insluiten in uw toepassingen met behulp van JavaScript-, Objective-C- (Apple), Java- (Android) of .NET-clientbibliotheken. Raadpleeg onze [volledige lijst met beschikbare clientbibliotheken](./sdk-options.md).

### <a name="pricing"></a>Prijzen

De kosten voor de services voor oproepen en scherm delen bedragen $0,004 per minuut per deelnemer voor groepsoproepen. Raadpleeg [deze pagina](./call-flows.md) voor uitleg over de verschillende mogelijke oproepstromen.

Voor elke deelnemer van de oproep worden kosten in rekening gebracht voor elke minuut die men verbonden is met de oproep. Dit is altijd zo, ongeacht of de gebruiker deelneemt aan een video- of spraakoproep of het scherm deelt.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>Prijsvoorbeeld: Audio/video-groepsoproep met JS- en iOS-clientbibliotheken

Alice heeft een groepsoproep gemaakt met haar collega's, Bob en Charlie. Alice en Bob hebben JS-clientbibliotheken gebruikt, en Charlie iOS-clientbibliotheken.

- De oproep duurt in totaal 60 minuten.
- Alice en Bob hebben aan de hele oproep deelgenomen. Alice heeft haar video 5 minuten aan gehad en haar scherm 23 minuten gedeeld. Bob heeft zijn video gedurende de hele oproep (60 minuten) aan gehad en zijn scherm 12 minuten gedeeld.
- Charlie heeft de oproep na 43 minuten verlaten. Charlie heeft audio en video gebruikt gedurende de hele deelname (43 minuten).

**Kostenberekeningen**

- 2 deelnemers x 60 minuten x $0,004 per deelnemer per minuut = $0,48 [voor video en audio geldt hetzelfde tarief]
- 1 deelnemers x 43 minuten x $0,004 per deelnemer per minuut = $0,172 [voor video en audio geldt hetzelfde tarief]

**Totale kosten voor de groepsoproep**: $0,48 + $0,172 = $0,652

### <a name="pricing-example-a-user-of-the-communication-services-js-client-library-joins-a-scheduled-microsoft-teams-meeting"></a>Prijs voorbeeld: een gebruiker van de JS-client bibliotheek van Communication Services maakt deel uit van een geplande micro soft teams-vergadering

Anja is een dokters bijeenkomst met haar patiënt, Bob. Anja wordt toegevoegd aan het bezoek vanuit de team bureau blad-toepassing. Bob ontvangt een koppeling met de website van de gezondheids zorg die verbinding maakt met de vergadering via de client bibliotheek van de Communication Services JS. Bob gebruikt zijn mobiele telefoon om de vergadering in te voeren met een webbrowser (iPhone met Safari). Chatten is beschikbaar op het virtuele bezoek.

- De aanroep duurt een totaal van 30 minuten.
- Anne en Robert nemen deel aan de volledige oproep. Anne draait haar video vijf minuten nadat het gesprek is gestart en deelt het scherm gedurende 13 minuten. Bob heeft zijn video aan voor de hele oproep.
- Anja stuurt vijf berichten, Bob beantwoordt met drie berichten.


**Kostenberekeningen**

- 1 deel nemer (Bob) x 30 minuten x $0,004 per deel nemer per minuut = $0,12 [zowel video als audio worden in rekening gebracht tegen hetzelfde tarief]
- 1 deel nemer (Alice) x 30 minuten x $0,000 per deel nemer per minuut = $0,0 *.
- 1 deel nemer (Bob) x 3 chat berichten x $0,0008 = $0,0024.
- 1 deel nemer (Alice) x 5 chat berichten x $0,000 = $0,0 *.

* De deelname van Alice valt onder de licentie van haar teams. In uw Azure-factuur worden de notulen en chat berichten weer gegeven die gebruikers van teams hebben met communicatie Services-gebruikers voor uw gemak, maar die minuten en berichten die afkomstig zijn van de teams-client, worden niet op prijs.

**Totale kosten voor het bezoek**:
- Gebruiker die lid is van de communicatie Services JS-client bibliotheek: $0,12 + $0,0024 = $0,1224
- Gebruiker die deelneemt aan teams bureaublad toepassing: $0 (gedekt door teams licentie)


## <a name="chat"></a>Chat

Met communicatie Services kunt u uw toepassing uitbreiden met de mogelijkheid om chat berichten tussen twee of meer gebruikers te verzenden en te ontvangen. Chat-clientbibliotheken zijn beschikbaar voor JavaScript, .NET, Python en Java. Raadpleeg [deze pagina voor meer informatie over clientbibliotheken](./sdk-options.md)

### <a name="price"></a>Prijs

Er wordt $0,0008 in rekening gebracht voor elk verzonden chatbericht.

### <a name="pricing-example-chat-between-two-users"></a>Prijsvoorbeeld: Chat tussen twee gebruikers

Geeta start een chatthread met Emily om een update te delen en verzendt 5 berichten. De chat sessie duurt 10 minuten. Geeta en Elsje verzenden elk nog 15 berichten.

**Kostenberekeningen**
- Aantal verzonden berichten (5 + 15 + 15) x $0,0008 = $0,028

### <a name="pricing-example-group-chat-with-multiple-users"></a>Prijsvoorbeeld: Groepschat met meerdere gebruikers

Charlie start een chatthread met zijn vrienden Casey en Jasmine om een vakantie te plannen. Ze chatten een tijdje, waarbij Charlie, Casey en Jasmine respectievelijk 20, 30 en 18 berichten verzenden. Ze realiseren zich dat hun vriendin Rose wellicht ook mee op vakantie zou willen, dus ze voegen haar toe aan de chatthread en delen de berichtgeschiedenis met haar.

Rose ziet de berichten en mengt zich in de chat. Casey ontvangt ondertussen een oproep en besluit de conversatie te verlaten om later weer aan te haken. Charlie, Jasmine en Rose overleggen over de reisdatums en verzenden nog eens respectievelijk 30, 25 en 35 berichten.

**Kostenberekeningen**

- Aantal verzonden berichten (20 + 30 + 18 + 30 + 25 + 35) x $0,0008 = $0,1264


## <a name="telephony-and-sms"></a>Telefonie en sms

## <a name="price"></a>Prijs

Telefoonservices worden per minuut berekend, en sms-berichten worden per bericht berekend. De prijzen zijn afhankelijk van het type en de locatie van het nummer dat u gebruikt, evenals de bestemming van uw oproepen en sms-berichten.

### <a name="telephone-number-leasing"></a>Een telefoonnummer leasen

Kosten voor het leasen van een telefoonnummer worden vooraf in rekening gebracht en vervolgens herhaald op maandbasis:

|Type getal   |Maandelijkse kosten   |
|--------------|-----------|
|Lokaal (Verenigde Staten)     |$1/maand        |
|Gratis (Verenigde Staten) |$2/maand |


### <a name="telephone-calling"></a>Telefoonoproepen

Traditionele telefonische oproep (oproep die plaatsvindt via het openbare telefoonnetwerk) is beschikbaar met betalen per gebruik voor telefoonnummers in de Verenigde Staten. De prijs wordt per minuut berekend op basis van het type nummer dat wordt gebruikt en de bestemming van de oproep. De prijsinformatie voor de meest populaire belbestemmingen vindt u in de volgende tabel. Raadpleeg de [gedetailleerde prijslijst](https://github.com/Azure/Communication/blob/master/pricing/communication-services-pstn-rates.csv) voor een volledige lijst met bestemmingen.


#### <a name="united-states-calling-prices"></a>Prijzen voor bellen in de Verenigde Staten

De volgende prijzen zijn inclusief de vereiste communicatiebelastingen en -kosten tot en met 30 juni 2021:

|Type getal   |Om oproepen te plaatsen   |Om oproepen te ontvangen|
|--------------|-----------|------------|
|Lokaal     |Vanaf $0,013/min       |$0,0085/min        |
|Gratis |$0,013/min   |$0,0220/min |

#### <a name="other-calling-destinations"></a>Overige belbestemmingen

De volgende prijzen zijn inclusief de vereiste communicatiebelastingen en -kosten tot en met 30 juni 2021:

|Bellen naar   |Prijs per minuut|
|-----------|------------|
|Canada     |Vanaf $0,013/min   |
|Verenigd Koninkrijk     |Vanaf $0,015/min   |
|Duitsland     |Vanaf $0,015/min   |
|Frankrijk     |Vanaf $0,016/min   |


### <a name="sms"></a>Sms

Sms biedt betalen per gebruik. De prijs wordt per bericht in rekening gebracht op basis van de bestemming van het bericht. Berichten kunnen worden verzonden door gratis telefoonnummers naar telefoonnummers die zich binnen de Verenigde Staten bevinden. Houd er rekening mee dat lokale (geografische) telefoonnummers niet kunnen worden gebruikt voor het verzenden van sms-berichten.

De volgende prijzen zijn inclusief de vereiste communicatiebelastingen en -kosten tot en met 30 juni 2021:

|Land   |Berichten verzenden|Berichten ontvangen|
|-----------|------------|------------|
|Verenigde Staten (gratis)    |$0,0075/bericht   | $0,0075/bericht |
