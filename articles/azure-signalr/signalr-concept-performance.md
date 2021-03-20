---
title: Prestatierichtlijnen voor Azure SignalR Service
description: Een overzicht van de prestaties en Bench Mark van de Azure signalerings service. Belang rijke metrische gegevens waarmee u rekening moet houden bij het plannen van de capaciteit.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 68cad32be177fa20794399157fca89e87c2f8f59
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "74157663"
---
# <a name="performance-guide-for-azure-signalr-service"></a>Prestatierichtlijnen voor Azure SignalR Service

Een van de belangrijkste voor delen van het gebruik van de Azure signalerings service is het gemak van het schalen van signaal toepassingen. In een grootschalig scenario is de prestaties een belang rijke factor. 

In deze hand leiding worden de factoren die van invloed zijn op de prestaties van de signaal toepassing geïntroduceerd. We beschrijven typische prestaties in verschillende gebruiks scenario's. In het eind worden de omgeving en hulpprogram ma's geïntroduceerd die u kunt gebruiken om een prestatie rapport te genereren.

## <a name="term-definitions"></a>Term definities

*Inkomend*: het binnenkomende bericht naar de Azure signalerings service.

*Uitgaand*: het uitgaande bericht van de Azure signalerings service.

*Band breedte*: de totale grootte van alle berichten in 1 seconde.

*Standaard modus*: de standaardwerk modus wanneer een exemplaar van de Azure signalerings service is gemaakt. De Azure signalerings service verwacht dat de app server een verbinding tot stand brengt voordat client verbindingen worden geaccepteerd.

*Serverloze modus*: een modus waarin de Azure signalerings service alleen client verbindingen accepteert. Er is geen server verbinding toegestaan.

## <a name="overview"></a>Overzicht

De Azure signalerings service definieert zeven standaard lagen voor verschillende prestatie capaciteiten. In deze hand leiding worden de volgende vragen beantwoord:

-   Wat zijn de typische prestaties van de Azure signalerings service voor elke laag?

-   Voldoet de Azure signalerings service aan mijn vereisten voor bericht doorvoer (bijvoorbeeld 100.000 berichten per seconde verzenden)?

-   Welke laag is geschikt voor mijn specifieke scenario? Of hoe kan ik de juiste laag selecteren?

-   Welk type app-server (VM-grootte) is geschikt voor mij? Hoeveel ervan moet ik implementeren?

Deze hand leiding geeft eerst uitleg over de factoren die van invloed zijn op de prestaties om deze vragen te beantwoorden. Vervolgens ziet u het maximum aantal binnenkomende en uitgaande berichten voor elke laag voor typische gebruiks voorbeelden: **echo**, **broadcast**, **verzenden naar groep** en **verzenden naar verbinding** (peer-to-peer-chat).

Deze hand leiding heeft geen betrekking op alle scenario's (en verschillende gebruiks voorbeelden, bericht grootten, bericht verzendings patronen, enzovoort). Maar het biedt een aantal methoden om u te helpen:

- Evalueer uw vereiste bij benadering voor de inkomende of uitgaande berichten.
- Zoek de juiste lagen door de prestatie tabel te controleren.

## <a name="performance-insight"></a>Prestatie inzicht

In deze sectie worden de evaluatie methoden voor prestaties beschreven en worden vervolgens alle factoren vermeld die van invloed zijn op de prestaties. In het eind biedt het een methode om u te helpen bij het evalueren van prestatie vereisten.

### <a name="methodology"></a>Methodologie

De *door Voer* en *latentie* zijn twee typische aspecten van het controleren van de prestaties. Voor de Azure signalerings service heeft elke SKU-laag een eigen doorvoer beperkings beleid. Het beleid definieert *de Maxi maal toegestane door Voer (binnenkomende en uitgaande band breedte)* als de Maxi maal behaalde door Voer als maxi maal 99 procent van de berichten een latentie van minder dan 1 seconde heeft.

Latentie is de tijds duur van de verbinding die het bericht verzendt om het antwoord bericht van de Azure signalerings service te ontvangen. Laten we een voor beeld van **echo's** maken. Elke client verbinding voegt een tijds tempel in het bericht toe. De hub van de app-server stuurt het oorspronkelijke bericht terug naar de client. De vertraging van de doorgifte kan dus eenvoudig worden berekend door elke client verbinding. De tijds tempel wordt gekoppeld voor elk bericht in **broadcasten**, **verzenden naar groep** en **verzenden naar verbinding**.

Voor het simuleren van duizenden gelijktijdige client verbindingen worden meerdere Vm's gemaakt in een virtueel particulier netwerk in Azure. Al deze Vm's maken verbinding met hetzelfde exemplaar van de Azure signalerings service.

In de standaard modus van de Azure signalerings service worden app server-Vm's geïmplementeerd in hetzelfde virtuele particuliere netwerk als client-Vm's. Alle client-Vm's en app server-Vm's worden geïmplementeerd in hetzelfde netwerk van dezelfde regio om te voor komen dat de latentie tussen regio's wordt overschreden.

### <a name="performance-factors"></a>Prestatie factoren

Theoretisch is de capaciteit van de Azure signalerings service beperkt door de reken resources: CPU, geheugen en netwerk. Meer verbindingen met de Azure signalerings service zorgen er bijvoorbeeld voor dat de service meer geheugen gebruikt. Voor groter bericht verkeer (bijvoorbeeld elk bericht groter is dan 2.048 bytes), moet de Azure signalerings service meer CPU-cycli best Eden om verkeer te verwerken. Ondertussen legt Azure-netwerk bandbreedte ook een limiet voor het maximale verkeer.

Het transport type is een andere factor die van invloed is op de prestaties. De drie typen zijn [Websockets](https://en.wikipedia.org/wiki/WebSocket), door de [server verzonden gebeurtenissen](https://en.wikipedia.org/wiki/Server-sent_events)en [lange polling](https://en.wikipedia.org/wiki/Push_technology). 

WebSocket is een bidirectionele en full-duplex communicatie protocol via één TCP-verbinding. Server-sent-event is een onwaar-protocol om berichten van de server naar de client te pushen. Long-Polling moeten clients periodiek gegevens van de server laten pollen via een HTTP-aanvraag. Voor dezelfde API onder dezelfde voor waarden biedt WebSocket de beste prestaties, de door de server verzonden gebeurtenis is langzamer en Long-Polling het langzaamst. De Azure signalerings service beveelt standaard WebSocket aan.

De kosten voor bericht routering beperken ook de prestaties. De Azure signalerings service speelt een rol af als een Message router, waarmee het bericht wordt gerouteerd van een set clients of servers naar andere clients of servers. Een ander scenario of API vereist een ander routerings beleid. 

Bij **echo** verzendt de client een bericht naar zichzelf en is het routerings doel ook zelf. Dit patroon heeft de laagste routerings kosten. Maar voor **broadcasten**, **verzenden naar groep** en **verzenden naar verbinding**, moet de Azure signalerings service de doel verbindingen opzoeken via de interne gedistribueerde gegevens structuur. Deze extra verwerking maakt gebruik van meer CPU-, geheugen-en netwerk bandbreedte. Als gevolg hiervan zijn de prestaties langzamer.

In de standaard modus kan de app-server ook een knel punt vormen voor bepaalde scenario's. De Azure Signalr SDK moet de hub aanroepen, terwijl een live-verbinding wordt bijgehouden met elke client via heartbeat-signalen.

In de serverloze modus verzendt de client een bericht via HTTP post, dat niet zo efficiënt is als WebSocket.

Een andere factor is Protocol: JSON en [Message Pack](https://msgpack.org/index.html). Message Pack is kleiner en wordt sneller geleverd dan JSON. De prestaties worden echter door Message Pack mogelijk niet verbeterd. De prestaties van de Azure Signalr-service zijn niet gevoelig voor protocollen omdat deze de bericht lading niet ontsleutelt tijdens het door sturen van berichten van clients naar servers of andersom.

In samen vatting zijn de volgende factoren van invloed op de inkomende en uitgaande capaciteit:

-   SKU-laag (CPU/geheugen)

-   Aantal verbindingen

-   Berichtgrootte

-   Verzend frequentie van berichten

-   Transport type (WebSocket, server-verzonden-event of Long-Polling)

-   Use-case-scenario (routerings kosten)

-   App-server-en service verbindingen (in server modus)


### <a name="finding-a-proper-sku"></a>Een juiste SKU zoeken

Hoe kunt u de inkomende/uitgaande capaciteit evalueren of bepalen welke laag geschikt is voor een specifieke use case?

Stel dat de app-server krachtig genoeg is en niet het knel punt van de prestaties is. Controleer vervolgens de maximale binnenkomende en uitgaande band breedte voor elke laag.

#### <a name="quick-evaluation"></a>Snelle evaluatie

We gaan de evaluatie eerst vereenvoudigen door een aantal standaard instellingen op te nemen: 

- Het transport type is WebSocket.
- De bericht grootte is 2.048 bytes.
- Er wordt elke 1 seconde een bericht verzonden.
- De Azure signalerings service bevindt zich in de standaard modus.

Elke laag heeft zijn eigen maximale inkomende band breedte en uitgaande band breedte. Een soepele gebruikers ervaring is niet gegarandeerd nadat de binnenkomende of uitgaande verbinding de limiet overschrijdt.

**Echo** geeft de maximale binnenkomende band breedte, omdat deze de laagste routerings kosten heeft. **Broadcast** Hiermee wordt de maximale band breedte voor uitgaande berichten gedefinieerd.

De gemarkeerde waarden in de volgende twee tabellen mogen *niet* worden overschreden.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen                       | 1000 | 2.000 | 5\.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| **Inkomende band breedte** | **2 Mbps**    | **4 Mbps**    | **10 Mbps**   | **20 MBps**    | **40 MBps**    | **100 MBps**   | **200 MBps**    |
| Uitgaande band breedte | 2 Mbps   | 4 Mbps   | 10 Mbps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |


|     Uitzenden             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Verbindingen               | 1000 | 2.000 | 5\.000  | 10.000 | 20.000 | 50,000  | 100.000 |
| Inkomende band breedte  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps    |
| **Uitgaande band breedte** | **4 Mbps**    | **8 MBps**    | **20 MBps**    | **40 MBps**    | **80 MBps**    | **200 MBps**    | **400 MBps**   |

*Inkomende band breedte* en *uitgaande band breedte* zijn de totale bericht grootte per seconde.  Hier volgen de formules:
```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: het aantal verbindingen dat het bericht verzendt.

- *outboundConnections*: het aantal verbindingen dat het bericht ontvangt.

- *messageSize*: de grootte van één bericht (gemiddelde waarde). Een klein bericht van minder dan 1.024 bytes heeft een invloed op de prestaties die vergelijkbaar is met een bericht van 1.024 bytes.

- *sendInterval*: de tijd voor het verzenden van één bericht. Normaal gesp roken is het 1 seconde per bericht, wat betekent dat u elke seconde één bericht verzendt. Een kleiner interval betekent dat er meer berichten worden verzonden in een tijds periode. Zo betekent 0,5 seconden per bericht dat elke seconde twee berichten verzendt.

- *Verbindingen*: de toegewezen maximale drempel waarde voor de Azure signalerings service voor elke laag. Als het verbindings nummer verder toeneemt, wordt de verbindings beperking in rekening gebracht.

#### <a name="evaluation-for-complex-use-cases"></a>Evaluatie voor complexe use cases

##### <a name="bigger-message-size-or-different-sending-rate"></a>Grotere bericht grootte of ander verzend tempo

De echte use-case is ingewik kelder. Er kan een bericht worden verzonden dat groter is dan 2.048 bytes, of het verzend bericht is niet één bericht per seconde. We gaan Unit100's broadcast als voor beeld nemen om te ontdekken hoe u de prestaties ervan kunt evalueren.

In de volgende tabel ziet u een real use-case van **broadcast**. Maar de bericht grootte, het aantal verbindingen en de verzend frequentie van berichten verschillen van wat we in de vorige sectie hebben aangenomen. De vraag is hoe we een van deze items (bericht grootte, aantal verbindingen of verzend frequentie) kunnen afleiden als we er slechts twee van hebben.

| Uitzenden  | Berichtgrootte | Inkomende berichten per seconde | Verbindingen | Intervallen verzenden |
|---|---------------------|--------------------------|-------------|-------------------------|
| 1 | 20 KB                | 1                        | 100.000     | 5 sec                      |
| 2 | 256 kB               | 1                        | 8,000       | 5 sec                      |

De volgende formule is gemakkelijk te afleiden op basis van de vorige formule:

```
outboundConnections = outboundBandwidth * sendInterval / messageSize
```

Voor Unit100 is de maximale uitgaande band breedte 400 MB van de vorige tabel. Voor een bericht grootte van 20 KB moet het maximum aantal uitgaande verbindingen 400 MB \* 5/20 KB = 100.000 zijn, wat overeenkomt met de werkelijke waarde.

##### <a name="mixed-use-cases"></a>Gemengde use cases

In het praktijk voorbeeld worden de vier basis cases samen met elkaar gecombineerd: **echo**, **broadcast**, **verzenden naar groep** en **verzenden naar verbinding**. De methode die u gebruikt om de capaciteit te evalueren, is:

1. Deel de cases voor gemengde gebruik in vier elementaire use cases.
1. De maximale band breedte voor inkomend en uitgaand verkeer berekenen door de voor gaande formules afzonderlijk te gebruiken.
1. Som van de bandbreedte berekeningen om de totale maximale inkomende/uitgaande band breedte te verkrijgen. 

Vervolgens neemt u de juiste laag op in de tabellen maximum aantal inkomende/uitgaande band breedte.

> [!NOTE]
> Voor het verzenden van een bericht naar honderden of duizenden kleine groepen, of voor duizenden clients die een bericht naar elkaar verzenden, worden de kosten van het bewerkings plan dominant. Houd rekening met dit voor deel.

Zorg ervoor dat de app-server *niet* het knel punt is voor het gebruik van het verzenden van een bericht naar clients. De sectie ' casestudy ' bevat richt lijnen voor het aantal app-servers dat u nodig hebt en het aantal server verbindingen dat u moet configureren.

## <a name="case-study"></a>Casestudy

In de volgende secties worden vier typische gebruiks voorbeelden voor WebSocket-Trans Port beschreven: **echo**, **broadcast**, **verzenden naar groep** en **verzenden naar verbinding**. Voor elk scenario bevat de sectie de huidige binnenkomende en uitgaande capaciteit voor de Azure signalerings service. Ook worden de belangrijkste factoren beschreven die van invloed zijn op de prestaties.

In de standaard modus maakt de app server vijf server verbindingen met de Azure signalerings service. De app server maakt standaard gebruik van de Azure signalerings Service-SDK. In de volgende resultaten van de prestatie test worden server verbindingen verhoogd tot 15 (of meer voor het uitzenden en verzenden van een bericht naar een grote groep).

Verschillende use cases hebben verschillende vereisten voor app-servers. **Broadcast** vereist een klein aantal app-servers. **Echo** of **verzenden naar verbinding** vereist veel app-servers.

In alle use-cases is de standaard bericht grootte 2.048 bytes en het verzend interval van het bericht 1 seconde.

### <a name="default-mode"></a>Standaard modus

Clients, Web-app-servers en de Azure signalerings service zijn betrokken bij de standaard modus. Elke client staat voor één verbinding.

#### <a name="echo"></a>Echo

De eerste keer dat een web-app verbinding maakt met de Azure signalerings service. Ten tweede, veel clients maken verbinding met de web-app, waarmee de clients worden omgeleid naar de Azure signalerings service met het toegangs token en-eind punt. Vervolgens maken de clients WebSocket-verbindingen met de Azure signalerings service.

Nadat alle clients verbindingen tot stand hebben gebracht, gaan ze een bericht verzenden dat elke seconde een tijds tempel voor de specifieke hub bevat. De hub ECHO het bericht terug naar de oorspronkelijke client. Elke client berekent de latentie wanneer het ECHO bericht weer wordt ontvangen.

In het volgende diagram bevinden zich in een lus 5 t/m 8 (rood gemarkeerd verkeer). De lus wordt uitgevoerd voor een standaard duur (5 minuten) en de statistieken van alle bericht latentie worden opgehaald.

![Verkeer voor de echo use-case](./media/signalr-concept-performance/echo.png)

Het gedrag van **echo** bepaalt dat de maximale binnenkomende band breedte gelijk is aan de maximale uitgaande band breedte. Zie de volgende tabel voor details.

|       Echo                        | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen                       | 1000 | 2.000 | 5\.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Inkomende/uitgaande berichten per seconde | 1000 | 2.000 | 5\.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Inkomende/uitgaande band breedte | 2 Mbps   | 4 Mbps   | 10 Mbps  | 20 MBps   | 40 MBps   | 100 MBps  | 200 MBps   |

In dit geval roept elke client de hub aan die is gedefinieerd in de app-server. De hub roept alleen de methode aan die is gedefinieerd in de oorspronkelijke client zijde. Deze hub is de meest licht gewicht hub voor **echo's**.

```
        public void Echo(IDictionary<string, object> data)
        {
            Clients.Client(Context.ConnectionId).SendAsync("RecordLatency", data);
        }
```

Zelfs voor deze eenvoudige hub is de verkeers druk op de app-server prominent, omdat de inkomend bericht over het laden van de **echo** toeneemt. Deze verkeers druk vereist veel app-servers voor grote SKU-lagen. De volgende tabel geeft een overzicht van het aantal app-servers voor elke laag.


|    Echo          | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen      | 1000 | 2.000 | 5\.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Aantal app-servers | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Het client verbindings nummer, de bericht grootte, de verzend snelheid van berichten, de SKU-laag en de CPU/het geheugen van de app-server beïnvloeden de algehele prestaties van **echo's**.

#### <a name="broadcast"></a>Uitzenden

Wanneer de **Web-app** het bericht ontvangt, wordt deze verzonden naar alle clients. Hoe meer clients er worden uitgezonden, des te meer berichten verkeer naar alle clients. Zie het volgende diagram.

![Verkeer voor de use-case van de uitzending](./media/signalr-concept-performance/broadcast.png)

Een klein aantal clients wordt uitgezonden. De band breedte van het inkomende bericht is klein, maar de uitgaande band breedte is enorm. De uitgaande bericht bandbreedte neemt toe als de client verbinding of de broadcast frequentie toeneemt.

De volgende tabel bevat een overzicht van de maximale client verbindingen, het inkomende/uitgaande aantal berichten en de band breedte.

|     Uitzenden             | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Verbindingen               | 1000 | 2.000 | 5\.000  | 10.000 | 20.000 | 50,000  | 100.000 |
| Inkomende berichten per seconde  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Uitgaande berichten per seconde | 2.000 | 4000 | 10.000 | 20.000 | 40,000 | 100.000 | 200.000 |
| Inkomende band breedte  | 4 KBps   | 4 KBps   | 4 KBps    | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     |
| Uitgaande band breedte | 4 Mbps   | 8 MBps   | 20 MBps   | 40 MBps   | 80 MBps   | 200 MBps   | 400 MBps   |

De omroep clients die berichten posten, zijn niet meer dan vier. Ze hebben minder toepassings servers nodig vergeleken met **echo's** , omdat de inkomende bericht grootte klein is. Er zijn twee app-servers voldoende voor zowel SLA-als prestatie overwegingen. Maar u moet de standaard server verbindingen verhogen om te voor komen dat er geen evenwicht meer is, met name voor Unit50 en Unit100.

|   Uitzenden      | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen      | 1000 | 2.000 | 5\.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Aantal app-servers | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Verhoog de standaard server verbindingen van 5 tot 40 op elke app-server om mogelijke niet-sluitende server verbindingen met de Azure signalerings service te voor komen.
>
> Het aantal client verbindingen, de bericht grootte, de verzend snelheid van berichten en de SKU-laag zijn van invloed op de algehele prestaties van de **uitzending**.

#### <a name="send-to-group"></a>Verzenden naar groep

De use-case **voor verzenden naar groep** heeft een vergelijkbaar verkeers patroon om te **broadcasten**. Het verschil is dat nadat clients WebSocket-verbindingen met de Azure signalerings service hebben tot stand zijn gebracht, ze lid moeten worden van groepen voordat ze een bericht kunnen verzenden naar een specifieke groep. In het volgende diagram ziet u de verkeers stroom.

![Verkeer voor de gebruiks Case voor verzenden naar groep](./media/signalr-concept-performance/sendtogroup.png)

Groepslid en aantal groepen zijn twee factoren die van invloed zijn op de prestaties. Voor het vereenvoudigen van de analyse definieert u twee soorten groepen:

- **Kleine groep**: elke groep heeft 10 verbindingen. Het groeps nummer is gelijk aan (maximum aantal verbindingen)/10. Bijvoorbeeld: voor Unit1, als er 1.000-verbindings aantallen zijn, hebben we 1000/10 = 100 groepen.

- **Grote groep**: het groeps nummer is altijd 10. Het aantal leden van de groep is gelijk aan (maximum aantal verbindingen)/10. Bijvoorbeeld: voor Unit1, als er 1.000-verbindings aantallen zijn, heeft elke groep 1000/10 = 100 leden.

De **groep verzenden naar** levert de bewerkings kosten van de Azure signalerings service, omdat deze de doel verbindingen via een gedistribueerde gegevens structuur moet vinden. Naarmate de verzend verbindingen toenemen, nemen de kosten toe.

##### <a name="small-group"></a>Kleine groep

De bewerkings kosten zijn belang rijk voor het verzenden van berichten naar een groot aantal kleine groepen. Op dit moment komt de implementatie van de Azure signalerings service voor de kosten limiet van het routerings percentage bij Unit50. Het toevoegen van meer CPU en geheugen helpt niet, zodat Unit100 niet meer kan worden verbeterd door het ontwerp. Neem contact op met de klant ondersteuning als u meer inkomende band breedte nodig hebt.

|   Naar kleine groep verzenden     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50 | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Verbindingen               | 1000 | 2.000 | 5\.000  | 10.000 | 20.000 | 50,000 | 100.000
| Groepslid aantal leden        | 10    | 10    | 10     | 10     | 10     | 10     | 10 
| Aantal groepen               | 100   | 200   | 500    | 1000  | 2.000  | 5\.000  | 10.000 
| Inkomende berichten per seconde  | 200   | 400   | 1000  | 2500  | 4000  | 7\.000  | 7\.000   |
| Inkomende band breedte  | 400 KBps  | 800 KBps  | 2 Mbps     | 5 Mbps     | 8 MBps     | 14 MBps    | 14 MBps     |
| Uitgaande berichten per seconde | 2.000 | 4000 | 10.000 | 25,000 | 40,000 | 70.000 | 70.000  |
| Uitgaande band breedte | 4 Mbps    | 8 MBps    | 20 MBps    | 50 MBps     | 80 MBps    | 140 MBps   | 140 MBps    |

Veel client verbindingen bellen de hub, waardoor het nummer van de app-server ook essentieel is voor de prestaties. De volgende tabel geeft een lijst van de voorgestelde app server-aantallen.

|  Naar kleine groep verzenden   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen      | 1000 | 2.000 | 5\.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Aantal app-servers | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Het client verbindingsnummer, de bericht grootte, de verzend snelheid van berichten, de route kosten, de SKU-laag en de CPU/het geheugen van de app-server zijn van invloed op de algehele prestaties van **verzenden naar kleine groep**.

##### <a name="big-group"></a>Big-groep

Voor de **groep verzenden naar Big** wordt de uitgaande band breedte het knel punt voordat de kosten limiet van de route ring wordt bereikt. De volgende tabel bevat de maximale uitgaande band breedte, die bijna hetzelfde is als voor **broadcasten**.

|    Naar grote groep verzenden      | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Verbindingen               | 1000 | 2.000 | 5\.000  | 10.000 | 20.000 | 50,000  | 100.000
| Groepslid aantal leden        | 100   | 200   | 500    | 1000  | 2.000  | 5\.000   | 10.000 
| Aantal groepen               | 10    | 10    | 10     | 10     | 10     | 10      | 10
| Inkomende berichten per seconde  | 20    | 20    | 20     | 20     | 20     | 20      | 20      |
| Inkomende band breedte  | 80 KBps   | 40 KBps   | 40 KBps    | 20 KBps    | 40 KBps    | 40 KBps     | 40 KBps     |
| Uitgaande berichten per seconde | 2.000 | 4000 | 10.000 | 20.000 | 40,000 | 100.000 | 200.000 |
| Uitgaande band breedte | 8 MBps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

Het aantal verzonden verbindingen is niet groter dan 40. De belasting op de app-server is klein, dus het voorgestelde aantal web-apps is klein.

|  Naar grote groep verzenden  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen      | 1000 | 2.000 | 5\.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Aantal app-servers | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

> [!NOTE]
> Verhoog de standaard server verbindingen van 5 tot 40 op elke app-server om mogelijke niet-sluitende server verbindingen met de Azure signalerings service te voor komen.
> 
> Het aantal client verbindingen, de bericht grootte, de verzend snelheid van berichten, de routerings kosten en de SKU-laag zijn van invloed op de algehele prestaties van de **verzenden naar een grote groep**.

#### <a name="send-to-connection"></a>Verzenden naar verbinding

Wanneer clients de verbindingen **met de Azure** signalerings service tot stand brengen, roept elke client een speciale hub aan om hun eigen verbindings-id op te halen. Met de Bench Mark voor prestaties worden alle verbindings-Id's verzameld, worden deze in wille keurige volg orde en opnieuw toegewezen aan alle clients als een verzend doel. De clients verzenden het bericht naar de doel verbinding totdat de prestatie test is voltooid.

![Verkeer voor de gebruiks case van send-to-client](./media/signalr-concept-performance/sendtoclient.png)

De routerings kosten voor **verzenden naar verbinding** zijn vergelijkbaar met de kosten voor **verzenden naar een kleine groep**.

Naarmate het aantal verbindingen toeneemt, worden de kosten voor de route ring van de totale prestaties beperkt. Unit50 heeft de limiet bereikt. Als gevolg hiervan kunnen Unit100 niet verder worden verbeterd.

De volgende tabel is een statistische samen vatting na veel rondingen van het uitvoeren van de Bench Mark **voor verzenden naar verbinding** .

|   Verzenden naar verbinding   | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50          | Unit100         |
|------------------------------------|-------|-------|-------|--------|--------|-----------------|-----------------|
| Verbindingen                        | 1000 | 2.000 | 5\.000 | 10.000 | 20.000 | 50,000          | 100.000         |
| Inkomende/uitgaande berichten per seconde | 1000 | 2.000 | 5\.000 | 8,000  | 9000  | 20.000 | 20.000 |
| Inkomende/uitgaande band breedte | 2 Mbps    | 4 Mbps    | 10 Mbps   | 16 MBps    | 18 MBps    | 40 MBps       | 40 MBps       |

Voor deze use-case moet hoge belasting aan de kant van de app-server worden gebruikt. Zie het aantal voorgestelde app-servers in de volgende tabel.

|  Verzenden naar verbinding  | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen      | 1000 | 2.000 | 5\.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Aantal app-servers | 2     | 2     | 2     | 3      | 3      | 10     | 20      |

> [!NOTE]
> Het client verbindingsnummer, de bericht grootte, de verzend snelheid van berichten, de route kosten, de SKU-laag en de CPU/het geheugen voor de app-server zijn van invloed op de algehele prestaties van **verzenden naar verbinding**.

#### <a name="aspnet-signalr-echo-broadcast-and-send-to-small-group"></a>ASP.NET signalerings ECHO, broadcasten en verzenden naar kleine groep

De Azure signalerings service biedt dezelfde prestatie capaciteit voor ASP.NET-Signa lering. 

De prestatie test maakt gebruik van Azure Web Apps van het [standaard service plan S3](https://azure.microsoft.com/pricing/details/app-service/windows/) voor ASP.net signalering.

In de volgende tabel ziet u het aantal voorgestelde web-apps voor ASP.NET signalerings **echo**.

|   Echo           | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen      | 1000 | 2.000 | 5\.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Aantal app-servers | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

In de volgende tabel ziet u het aantal voorgestelde web-apps voor ASP.NET signalerings **broadcast**.

|  Uitzenden       | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen      | 1000 | 2.000 | 5\.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Aantal app-servers | 2     | 2     | 2     | 2      | 2      | 2      | 2       |

In de volgende tabel ziet u het aantal voorgestelde web-apps voor ASP.NET signalering **verzenden naar een kleine groep**.

|  Naar kleine groep verzenden     | Unit1 | Unit2 | Unit5 | Unit10 | Unit20 | Unit50 | Unit100 |
|------------------|-------|-------|-------|--------|--------|--------|---------|
| Verbindingen      | 1000 | 2.000 | 5\.000 | 10.000 | 20.000 | 50,000 | 100.000 |
| Aantal app-servers | 2     | 2     | 4     | 4      | 8      | 32      | 40       |

### <a name="serverless-mode"></a>Serverloze modus

Clients en de Azure signalerings service zijn betrokken bij de serverloze modus. Elke client staat voor één verbinding. De client verzendt berichten via de REST API naar een andere client of broadcast berichten.

Het verzenden van berichten met hoge dichtheid via de REST API is niet zo efficiënt als het gebruik van WebSocket. U moet elke keer een nieuwe HTTP-verbinding maken en dat is een extra kosten in de serverloze modus.

#### <a name="broadcast-through-rest-api"></a>Uitzenden via REST API
Alle clients maken WebSocket-verbindingen met de Azure signalerings service. Vervolgens starten sommige clients via de REST API. Het bericht dat wordt verzonden (inkomend) is alle via HTTP post, wat niet efficiënt is vergeleken met WebSocket.

|   Uitzenden via REST API     | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Verbindingen               | 1000 | 2.000 | 5\.000  | 10.000 | 20.000 | 50,000  | 100.000 |
| Inkomende berichten per seconde  | 2     | 2     | 2      | 2      | 2      | 2       | 2       |
| Uitgaande berichten per seconde | 2.000 | 4000 | 10.000 | 20.000 | 40,000 | 100.000 | 200.000 |
| Inkomende band breedte  | 4 KBps    | 4 KBps    | 4 KBps     | 4 KBps     | 4 KBps     | 4 KBps      | 4 KBps      |
| Uitgaande band breedte | 4 Mbps    | 8 MBps    | 20 MBps    | 40 MBps    | 80 MBps    | 200 MBps    | 400 MBps    |

#### <a name="send-to-user-through-rest-api"></a>Naar gebruiker verzenden via REST API
De Bench Mark wijst gebruikers namen toe aan alle clients voordat ze verbinding maken met de Azure signalerings service. Nadat de clients WebSocket-verbindingen met de Azure signalerings service hebben tot stand zijn gebracht, sturen ze via HTTP post berichten naar anderen.

|   Naar gebruiker verzenden via REST API | Unit1 | Unit2 | Unit5  | Unit10 | Unit20 | Unit50  | Unit100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Verbindingen               | 1000 | 2.000 | 5\.000  | 10.000 | 20.000 | 50,000  | 100.000 |
| Inkomende berichten per seconde  | 300   | 600   | 900    | 1.300  | 2.000  | 10.000  | 18.000  |
| Uitgaande berichten per seconde | 300   | 600   | 900    | 1.300  | 2.000  | 10.000  | 18.000 |
| Inkomende band breedte  | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 Mbps     | 10 Mbps     | 36 MBps    |
| Uitgaande band breedte | 600 KBps  | 1,2 MBps  | 1,8 MBps   | 2,6 MBps   | 4 Mbps     | 10 Mbps     | 36 MBps    |

## <a name="performance-test-environments"></a>Prestatie test omgevingen

Voor alle use-cases die eerder zijn vermeld, hebben we de prestatie tests uitgevoerd in een Azure-omgeving. We hebben meestal 50-client-Vm's en 20 app server-Vm's gebruikt. Hier volgt enige informatie:

- VM-grootte van client: StandardDS2V2 (2 vCPU, 7G geheugen)

- VM-grootte van de app-server: StandardF4sV2 (4 vCPU, 8G geheugen)

- Azure signalering SDK-server verbindingen: 15

## <a name="performance-tools"></a>Prestatie hulpprogramma's

U kunt prestatie hulpprogramma's voor Azure signalerings service vinden op [github](https://github.com/Azure/azure-signalr-bench/).

## <a name="next-steps"></a>Volgende stappen

In dit artikel krijgt u een overzicht van de prestaties van de Azure signalerings service in typische gebruiks scenario's.

Lees de volgende hand leidingen om meer te weten te komen over de interne service en om deze te schalen:

* [Interne werking van Azure SignalR Service](signalr-concept-internals.md)
* [Azure signalerings service schalen](signalr-howto-scale-multi-instances.md)
