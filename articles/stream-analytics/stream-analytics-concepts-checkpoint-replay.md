---
title: Het controle punt en de concepten van herstel na opnieuw afspelen in Azure Stream Analytics
description: In dit artikel wordt het controle punt beschreven en worden de concepten voor taak herstel opnieuw afgespeeld in Azure Stream Analytics.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: df31f8538bb9eabeca37fe4c52c4443fd447e415
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98015315"
---
# <a name="checkpoint-and-replay-concepts-in-azure-stream-analytics-jobs"></a>Controle punten en concepten voor opnieuw afspelen in Azure Stream Analytics taken
In dit artikel worden het interne controle punt en de concepten voor het opnieuw afspelen van Azure Stream Analytics beschreven en de invloed hiervan op de taak herstel. Telkens wanneer een Stream Analytics taak wordt uitgevoerd, wordt de status informatie intern onderhouden. Deze status informatie wordt periodiek opgeslagen in een controle punt. In sommige gevallen wordt de controlepunt informatie gebruikt voor taak herstel als een taak fout of een upgrade optreedt. In andere gevallen kan het controle punt niet worden gebruikt voor herstel, en moet er een herhaling worden uitgevoerd.

## <a name="stateful-query-logic-in-temporal-elements"></a>Stateful query logica in tijdelijke elementen
Een van de unieke mogelijkheden van Azure Stream Analytics taak is het uitvoeren van stateful verwerking, zoals venster aggregaties, tijdelijke samen voegingen en tijdelijke analytische functies. Elk van deze opera tors houdt status informatie bij het uitvoeren van de taak. De maximale venster grootte voor deze query-elementen is zeven dagen. 

Het tijdelijke venster-concept wordt in verschillende Stream Analytics query-elementen weer gegeven:
1. Statistische functies in Vensters (groeperen op basis van Tumblingvenstertriggers, verspringen en schuivende Vensters)

2. Tijdelijke samen voegingen (samen voegen met DATEDIFF)

3. Tijdelijke analytische functies (ISFIRST, LAST en LAG met de limiet duur)


## <a name="job-recovery-from-node-failure-including-os-upgrade"></a>Taak herstel van knooppunt fout, inclusief upgrade van het besturings systeem
Telkens wanneer een Stream Analytics taak wordt uitgevoerd, wordt intern deze geschaald naar werk op meerdere werk knooppunten. De status van elke worker-knoop punt is elke paar minuten, waardoor het systeem kan worden hersteld als er een fout optreedt.

Het kan gebeuren dat een gegeven werk knooppunt mislukt of dat er een upgrade van het besturings systeem voor dat werk knooppunt kan worden uitgevoerd. Om automatisch te herstellen, verkrijgt Stream Analytics een nieuw gezonden knoop punt en wordt de status van het vorige werk knooppunt hersteld vanuit het meest recente beschik bare controle punt. Als u het werk wilt hervatten, is een kleine hoeveelheid herhaling nodig om de status te herstellen vanaf het moment dat het controle punt wordt gemaakt. Normaal gesp roken is de herstel hiaat slechts een paar minuten. Wanneer er voldoende streaming-eenheden zijn geselecteerd voor de taak, moet de herhaling snel worden voltooid. 

In een volledig parallelle query is de tijd die nodig is om na een storing van een werk knooppunt te worden opgedeeld in verhouding te staan tot:

[de invoer gebeurtenis frequentie] x [de lengte van de tussen ruimte]/[aantal verwerkings partities]

Als u ooit aanzienlijke verwerkings vertraging onderneemt vanwege een storing in het knoop punt en de upgrade van het besturings systeem, kunt u overwegen de query volledig parallel te maken en de taak te schalen om meer streaming-eenheden toe te wijzen Zie [een Azure stream Analytics taak schalen om de door voer te verg Roten](stream-analytics-scale-jobs.md)voor meer informatie.

Met de huidige Stream Analytics wordt geen rapport weer gegeven wanneer dit soort herstel proces plaatsvindt.

## <a name="job-recovery-from-a-service-upgrade"></a>Taak herstel van een service-upgrade 
Micro soft werkt af en toe de binaire bestanden die de Stream Analytics-taken uitvoeren in de Azure-service. Op dat moment worden de taken van gebruikers die worden uitgevoerd naar een nieuwere versie bijgewerkt en wordt de taak automatisch opnieuw gestart. 

Op dit moment wordt de indeling van het herstel controlepunt niet bewaard tussen upgrades. Als gevolg hiervan moet de status van de streaming-query volledig worden hersteld met behulp van de replay-techniek. Als u wilt toestaan dat Stream Analytics-taken exact dezelfde invoer van tevoren herhalen, is het belang rijk om het Bewaar beleid voor de bron gegevens in te stellen op ten minste de venster grootten in uw query. Als u dit niet doet, kan dit leiden tot onjuiste of gedeeltelijke resultaten tijdens de upgrade van de service, omdat de bron gegevens mogelijk niet voldoende lang genoeg zijn om de volledige venster grootte toe te voegen.

Over het algemeen geldt dat de hoeveelheid opnieuw afspelen evenredig is met de grootte van het venster vermenigvuldigd met het gemiddelde gebeurtenis percentage. Een voor beeld: voor een taak met een invoer frequentie van 1000 gebeurtenissen per seconde, wordt een venster grootte van meer dan één uur beschouwd als een grote replay grootte. Het kan Maxi maal één uur duren voordat de gegevens opnieuw worden verwerkt om de status te initialiseren, zodat deze volledige en juiste resultaten kan veroorzaken. Dit kan leiden tot een vertraagde uitvoer (geen uitvoer) gedurende een langere periode. Query's zonder Windows of andere tijdelijke Opera Tors, zoals `JOIN` of `LAG` , zouden geen herhaling hebben.

## <a name="estimate-replay-catch-up-time"></a>Indruk tijd van raming voor replay
Als u de lengte van de vertraging wilt schatten als gevolg van een service-upgrade, kunt u deze techniek volgen:

1. Laad de invoer Event hub met voldoende gegevens om de grootste venster grootte in uw query te bedekken, op basis van het verwachte aantal gebeurtenissen. De tijds tempel van de gebeurtenissen moet dicht bij de klok tijd van de wand binnen die periode liggen, alsof het een live invoer feed is. Als u bijvoorbeeld een venster van drie dagen hebt in uw query, verzendt u gebeurtenissen drie dagen naar Event hub en gaat u door met het verzenden van gebeurtenissen. 

2. Start de taak **nu** op het moment van de begin tijd. 

3. De tijd meten tussen de begin tijd en de eerste uitvoer die wordt gegenereerd. De tijd is de hoeveelheid vertraging die de taak zou opleveren tijdens een service-upgrade.

4. Als de vertraging te lang is, probeert u de taak te partitioneren en het aantal SUs te verhogen. de belasting wordt dus naar meer knoop punten verdeeld. U kunt ook de venster grootten in uw query verkleinen en verdere aggregatie of andere stateful verwerking uitvoeren op de uitvoer die wordt geproduceerd door de Stream Analytics-taak in de stroomafwaartse Sink (bijvoorbeeld met behulp van Azure SQL Database).

Voor algemene service stabiliteits bezorgdheid tijdens de upgrade van essentiële taken kunt u overwegen dubbele taken uit te voeren in gekoppelde Azure-regio's. Zie voor meer informatie [garantie stream Analytics taak betrouw baarheid tijdens service-updates](stream-analytics-job-reliability.md).

## <a name="job-recovery-from-a-user-initiated-stop-and-start"></a>Taak herstel van een door de gebruiker geïnitieerde stoppen en starten
Als u de query syntaxis wilt bewerken voor een streaming-taak of als u invoer en uitvoer wilt aanpassen, moet de taak worden gestopt om de wijzigingen aan te brengen en het taak ontwerp bij te werken. Wanneer een gebruiker de streaming-taak stopt en opnieuw start, is het herstel scenario vergelijkbaar met de service-upgrade. 

Controlepunt gegevens kunnen niet worden gebruikt voor een door de gebruiker geïnitieerde taak opnieuw starten. Als u de uitvoer vertraging tijdens een dergelijke herstart wilt schatten, gebruikt u dezelfde procedure zoals beschreven in de vorige sectie en past u vergelijk bare beperkingen toe als de vertraging te lang is.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over betrouw baarheid en schaal baarheid:
- [Zelf studie: waarschuwingen instellen voor Azure Stream Analytics taken](stream-analytics-set-up-alerts.md)
- [Een Azure Stream Analytics taak schalen om de door voer te verhogen](stream-analytics-scale-jobs.md)
- [Betrouw baarheid Stream Analytics-taak garanderen tijdens service-updates](stream-analytics-job-reliability.md)
