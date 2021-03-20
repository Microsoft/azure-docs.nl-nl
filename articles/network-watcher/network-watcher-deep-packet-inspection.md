---
title: Pakket inspectie met Azure Network Watcher | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Network Watcher kunt gebruiken om grondige pakket inspecties uit te voeren die zijn verzameld van een virtuele machine
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 7b907d00-9c35-40f5-a61e-beb7b782276f
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.openlocfilehash: 3bd85d6faf05fcf659e9335ee9de3d64198dfa08
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98011166"
---
# <a name="packet-inspection-with-azure-network-watcher"></a>Pakket inspectie met Azure Network Watcher

Met de functie voor het vastleggen van pakketten van Network Watcher kunt u in de portal, Power shell, CLI en programmatisch met de SDK en REST API Capture-sessies initiëren en beheren op uw Azure-Vm's. Met pakket opname kunt u scenario's aanpakken waarvoor gegevens op pakket niveau zijn vereist door de informatie in een gemakkelijk bruikbare indeling op te geven. Gebruik gratis hulpprogram ma's voor het inspecteren van de gegevens, u kunt de communicatie onderzoeken die naar en van uw Vm's worden verzonden en inzicht krijgen in uw netwerk verkeer. Een voor beeld van het gebruik van gegevens voor pakket opname is: het onderzoeken van problemen met het netwerk of de toepassing, het detecteren van misbruik van het netwerk en pogingen tot inbraak of het behouden van naleving van regelgeving. In dit artikel laten we zien hoe u een pakket opname bestand opent dat is verschaft door Network Watcher met behulp van een populaire open source-hulp programma. Er worden ook voor beelden gegeven van het berekenen van de latentie van een verbinding, het identificeren van abnormaal verkeer en het onderzoeken van netwerk statistieken.

## <a name="before-you-begin"></a>Voordat u begint

Dit artikel doorloopt enkele vooraf geconfigureerde scenario's voor een pakket opname die eerder is uitgevoerd. Deze scenario's illustreren de mogelijkheden die toegankelijk zijn door een pakket opname te bekijken. In dit scenario wordt [WireShark](https://www.wireshark.org/) gebruikt om de pakket opname te controleren.

In dit scenario wordt ervan uitgegaan dat u een pakket opname al hebt uitgevoerd op een virtuele machine. Ga voor meer informatie over het maken van een pakket opname met behulp van [de portal](network-watcher-packet-capture-manage-portal.md) of met de rest door [pakket opnames beheren met rest API](network-watcher-packet-capture-manage-rest.md)te bezoeken.

## <a name="scenario"></a>Scenario

In dit scenario kunt u het volgende doen:

* Een pakket opname controleren

## <a name="calculate-network-latency"></a>Netwerk latentie berekenen

In dit scenario laten we zien hoe u de eerste round trip-tijd (RTT) kunt weer geven van een Transmission Control Protocol (TCP)-conversatie tussen twee eind punten.

Wanneer een TCP-verbinding tot stand is gebracht, volgen de eerste drie pakketten die in de verbinding worden verzonden, een patroon dat doorgaans de drieweg-handshake wordt genoemd. Door de eerste twee pakketten te controleren die in deze Handshake worden verzonden, een eerste aanvraag van de client en een reactie van de server, kunnen we de latentie berekenen wanneer deze verbinding tot stand is gebracht. Deze latentie wordt de round-trip tijd (RTT) genoemd. Raadpleeg de volgende resource voor meer informatie over het TCP-protocol en de drieweg-handshake. [https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip](https://support.microsoft.com/en-us/help/172983/explanation-of-the-three-way-handshake-via-tcp-ip)

### <a name="step-1"></a>Stap 1

WireShark starten

### <a name="step-2"></a>Stap 2

Laad het **Cap** -bestand van de pakket opname. Dit bestand bevindt zich in de blob die lokaal op de virtuele machine is opgeslagen, afhankelijk van hoe u het hebt geconfigureerd.

### <a name="step-3"></a>Stap 3

Voor het weer geven van de eerste round trip-tijd (RTT) in TCP-conversaties gaan we alleen naar de eerste twee pakketten die bij de TCP-handshake betrokken zijn. We gebruiken de eerste twee pakketten in de drieweg-handshake, die de pakketten [SYN], [SYN, ACK] zijn. Deze worden benoemd voor vlaggen die in de TCP-header zijn ingesteld. Het laatste pakket in de handshake, het pakket [ACK], wordt niet gebruikt in dit scenario. Het [SYN]-pakket wordt verzonden door de client. Zodra deze is ontvangen, verzendt de server het pakket [ACK] als bevestiging van het ontvangen van de SYN van de client. Het feit dat de reactie van de server weinig overhead vergt, we berekenen de RTT door de tijd af te trekken van het pakket [SYN, ACK] dat door de client is ontvangen door de client.

Met WireShark wordt deze waarde voor ons berekend.

Om de eerste twee pakketten in de TCP-richtings-Handshake gemakkelijker te kunnen bekijken, zullen we de filter functionaliteit gebruiken die wordt geleverd door WireShark.

Als u het filter in WireShark wilt Toep assen, vouwt u het segment ' Transmission Control Protocol ' van een [SYN]-pakket in uw Capture uit en bekijkt u de vlaggen die zijn ingesteld in de TCP-header.

Omdat we op zoek zijn naar alle [SYN]-en [SYN, ACK]-pakketten, onder vlaggen bevestigen dat de SYN-bit is ingesteld op 1, klikt u met de rechter muisknop op de SYN-bit-> als filter-> geselecteerd.

![afbeelding 7][7]

### <a name="step-4"></a>Stap 4

Nu u het venster hebt gefilterd, zodat alleen pakketten met de bit [SYN] worden weer gegeven, kunt u eenvoudig gesp rekken selecteren waarin u geïnteresseerd bent om de eerste RTT weer te geven. Een eenvoudige manier om de RTT in WireShark weer te geven, klikt u gewoon op de vervolg keuzelijst ' SEQ/ACK '. Vervolgens ziet u dat de RTT wordt weer gegeven. In dit geval is de RTT 0,0022114 seconden of 2,211 MS.

![afbeelding 8][8]

## <a name="unwanted-protocols"></a>Ongewenste protocollen

U kunt veel toepassingen uitvoeren op een exemplaar van een virtuele machine die u in azure hebt geïmplementeerd. Veel van deze toepassingen communiceren via het netwerk, mogelijk zonder uw uitdrukkelijke toestemming. Door gebruik te maken van pakket opname om netwerk communicatie op te slaan, kunnen we onderzoeken hoe de toepassing op het netwerk communiceert en wat er problemen zijn.

In dit voor beeld bekijken we een eerder uitgevoerde pakket opname voor ongewenste protocollen die kunnen wijzen op niet-geautoriseerde communicatie van een toepassing die op de computer wordt uitgevoerd.

### <a name="step-1"></a>Stap 1

Als u dezelfde vastleg ging in het vorige scenario wilt gebruiken, klikt u op **Statistieken**  >  **protocol hiërarchie**

![menu Protocol hiërarchie][2]

Het venster protocol hiërarchie wordt weer gegeven. Deze weer gave bevat een lijst met alle protocollen die tijdens de opname sessie werden gebruikt en het aantal pakketten dat is verzonden en ontvangen met behulp van de protocollen. Deze weer gave kan handig zijn voor het vinden van ongewenst netwerk verkeer op uw virtuele machines of netwerk.

![de protocol hiërarchie is geopend][3]

Zoals u kunt zien in de volgende scherm opname, was er verkeer via het BitTorrent-protocol, dat wordt gebruikt voor peer-to-peer bestanden delen. Als beheerder kunt u geen BitTorrent-verkeer op deze specifieke virtuele machines zien. Nu u op de hoogte bent van dit verkeer, kunt u de peer-to-peer-software die op deze virtuele machine is geïnstalleerd, verwijderen of het verkeer blok keren met behulp van netwerk beveiligings groepen of een firewall. Daarnaast kunt u ervoor kiezen om pakket opnames volgens een schema uit te voeren, zodat u het protocol gebruik regel matig op uw virtuele machines controleert. Zie [netwerk bronnen bewaken met Azure Automation](network-watcher-monitor-with-azure-automation.md) voor een voor beeld van het automatiseren van netwerk taken in Azure.

## <a name="finding-top-destinations-and-ports"></a>De belangrijkste bestemmingen en poorten zoeken

Meer informatie over de typen verkeer, de eind punten en de door gegeven poorten zijn belang rijk bij het bewaken of oplossen van problemen met toepassingen en bronnen in uw netwerk. Het gebruik van een pakket opname bestand van boven kan er snel worden gecommuniceerd met de belangrijkste bestemmingen die onze VM verkrijgt en de poorten die worden gebruikt.

### <a name="step-1"></a>Stap 1

Klik op **Statistieken**  >  **IPv4-statistieken**  >  **doelen en poorten** met dezelfde vastleg ging in het vorige scenario

![pakket opname venster][4]

### <a name="step-2"></a>Stap 2

Als we de resultaten van een regel bekijken, zijn er meerdere verbindingen op poort 111. De poort die het meest wordt gebruikt, is 3389, dat wil zeggen: extern bureau blad, en het resterende aantal RPC dynamische poorten.

Hoewel dit verkeer kan betekenen dat er niets is, is het een poort die voor veel verbindingen is gebruikt en is deze niet bekend bij de beheerder.

![afbeelding 5][5]

### <a name="step-3"></a>Stap 3

Nu we een poort voor buiten het bereik hebben bepaald, kunnen we de vastleg ging filteren op basis van de poort.

Het filter in dit scenario is:

```
tcp.port == 111
```

We voeren de filter tekst van boven in het tekstvak Filter en druk op ENTER.

![afbeelding 6][6]

Vanuit de resultaten ziet u dat al het verkeer afkomstig is van een lokale virtuele machine in hetzelfde subnet. Als we nog steeds niet begrijpen waarom dit verkeer plaatsvindt, kunnen we de pakketten verder controleren om te bepalen waarom deze aanroepen worden gedaan op poort 111. Met deze informatie kunnen we de juiste actie ondernemen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de andere diagnostische functies van Network Watcher vindt u in [Azure Network bewaking Overview](network-watcher-monitoring-overview.md) (Engelstalig)

[1]: ./media/network-watcher-deep-packet-inspection/figure1.png
[2]: ./media/network-watcher-deep-packet-inspection/figure2.png
[3]: ./media/network-watcher-deep-packet-inspection/figure3.png
[4]: ./media/network-watcher-deep-packet-inspection/figure4.png
[5]: ./media/network-watcher-deep-packet-inspection/figure5.png
[6]: ./media/network-watcher-deep-packet-inspection/figure6.png
[7]: ./media/network-watcher-deep-packet-inspection/figure7.png
[8]: ./media/network-watcher-deep-packet-inspection/figure8.png













