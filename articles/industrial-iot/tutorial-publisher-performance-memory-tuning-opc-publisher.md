---
title: Micro soft OPC Publisher prestaties en geheugen afstemmen
description: In deze zelf studie leert u hoe u de prestaties en het geheugen van de OPC-Publisher afstemt.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: tutorial
ms.date: 3/22/2021
ms.openlocfilehash: 89e288d1186efd405019d6474dcbd332e7925d67
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104787731"
---
# <a name="tutorial-tune-the-opc-publisher-performance-and-memory"></a>Zelf studie: de OPC-prestaties en het geheugen van de Publisher afstemmen

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De prestaties aanpassen
> * De berichten stroom aanpassen aan de geheugen resources

Wanneer de OPC-Publisher wordt uitgevoerd in productie-instellingen, moeten netwerk prestatie vereisten (door Voer en latentie) en geheugen bronnen worden overwogen. OPC Publisher stelt de volgende opdracht regel parameters beschikbaar om aan deze vereisten te voldoen:

* Capaciteit van berichten wachtrij ( `mq` voor versie 2,5 en lager, niet beschikbaar in versie 2,6, `om` voor versie 2,7)
* IoT Hub verzend interval ( `si` )
* IoT Hub bericht grootte ( `ms` )

## <a name="adjusting-iot-hub-send-interval-and-iot-hub-message-size"></a>IoT Hub verzend interval en IoT Hub bericht grootte aanpassen

De `mq/om` para meter bepaalt de bovengrens van de capaciteit van de interne berichten wachtrij. Met deze wachtrij worden alle berichten gebufferd voordat ze naar IoT Hub worden verzonden. De standaard grootte van de wachtrij is Maxi maal 2 MB voor OPC Publisher-versie 2,5 en lager en 4000 IoT Hub berichten voor versie 2,7 (dat wil zeggen, als de instelling voor de bericht grootte van de IoT Hub 256 KB is, is de grootte van de wachtrij Maxi maal 1 GB). Als de OPC-uitgever niet in staat is om berichten te verzenden naar IoT Hub snel genoeg, neemt het aantal items in deze wachtrij toe. Als dit tijdens de test uitvoering gebeurt, kan een van de volgende handelingen worden uitgevoerd om het probleem te verhelpen:

* IoT Hub verzend interval verlagen ( `si` )

* Verg root de IoT Hub bericht grootte ( `ms` , het maximum dat kan worden ingesteld op is 256 KB)

Als de wachtrij groeit, zelfs als de `si` `ms` para meters en zijn aangepast, wordt de maximale wachtrij capaciteit bereikt, waarna de berichten verloren gaan. Dit wordt veroorzaakt door het feit dat zowel de `si` `ms` para meter als de Internet verbinding tussen de OPC-Uitgever en de IOT hub niet snel genoeg is voor het aantal berichten dat in een bepaald scenario moet worden verzonden. In dat geval kunnen er alleen meerdere parallelle OPC-uitgevers worden ingesteld. De `mq/om` para meter heeft ook de grootste invloed op het geheugen verbruik door OPC Publisher. 

Met de `si` para meter wordt de OPC-Uitgever gedwongen berichten te verzenden naar IOT hub met het opgegeven interval. Er wordt een bericht verzonden wanneer de maximale IoT Hub bericht grootte van 256 KB aan gegevens beschikbaar is (de verzend interval wordt geactiveerd om opnieuw in te stellen) of wanneer de opgegeven interval tijd is verstreken.

`ms`Met de para meter kunnen berichten in batches worden verzonden naar IOT hub. In de meeste netwerk instellingen is de latentie van het verzenden van één bericht naar IoT Hub hoog, vergeleken met de tijd die nodig is om de payload te verzenden. Dit is vooral het gevolg van Quality of Service (QoS) vereisten, omdat berichten alleen worden bevestigd zodra ze door IoT Hub zijn verwerkt). Als er daarom een vertraging voor de gegevens op IoT Hub acceptabel is, moet OPC Publisher worden geconfigureerd voor gebruik van de maximale bericht grootte van 256 KB door de `ms` para meter in te stellen op 0. Het is ook de meest rendabele manier om OPC Publisher te gebruiken.

Met de standaard configuratie worden gegevens verzonden naar IoT Hub elke tien seconden ( `si=10` ) of wanneer 256 kB aan IOT hub bericht gegevens beschikbaar is ( `ms=0` ). Hiermee voegt u een maximum vertraging toe van 10 seconden, maar heeft dit weinig waarschijnlijkheid bij het verlies van gegevens vanwege de omvang van grote berichten. De metrische gegevens `monitored item notifications enqueue failure`  in OPC Publisher-versie 2,5 en lager en `messages lost` in OPC publisher versie 2,7 laat zien hoeveel berichten verloren zijn gegaan.

Als beide `si` en- `ms` para meters zijn ingesteld op 0, stuurt OPC Publisher een bericht naar IOT hub zodra er gegevens beschikbaar zijn. Dit resulteert in een gemiddelde IoT Hub bericht grootte van slechts meer dan 200 bytes. Het voor deel van deze configuratie is echter dat OPC Publisher de gegevens van het verbonden activum zonder vertraging verzendt. Het aantal verloren berichten is hoog voor gebruik wanneer een grote hoeveelheid gegevens moet worden gepubliceerd en daarom wordt dit niet aanbevolen voor deze scenario's.

Als u de prestaties van de OPC-uitgever wilt meten, kunt u de `di` para meter gebruiken om de metrische gegevens voor prestaties naar het traceer logboek af te drukken in het opgegeven interval (in seconden).

## <a name="next-steps"></a>Volgende stappen
Nu u hebt geleerd hoe u de prestaties en het geheugen van de OPC-Publisher afstemt, kunt u de OPC Publisher GitHub-opslag plaats bekijken voor meer bronnen:

> [!div class="nextstepaction"]
> [OPC Publisher GitHub-opslag plaats](https://github.com/Azure/Industrial-IoT)