---
title: Azure Service Bus sequencing van berichten en tijdstempels | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u sequencing en volgorde (met tijdstempels) van Azure Service Bus behouden.
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: 3d5300568232afae1238445113d60eda8cdb2f1b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107497094"
---
# <a name="message-sequencing-and-timestamps"></a>Berichtvolgorde en -timestamps

Sequencing en timestamping zijn twee functies die altijd zijn ingeschakeld voor alle Service Bus-entiteiten en die worden weergeven via de eigenschappen en van ontvangen `SequenceNumber` `EnqueuedTimeUtc` of bladerende berichten.

In die gevallen waarin de absolute volgorde van berichten aanzienlijk is en/of waarin een consument een betrouwbare unieke id voor berichten nodig heeft, stempelt de broker berichten met een hiaatvrij, toenemend volgnummer ten opzichte van de wachtrij of het onderwerp. Voor gepartitiestiteiten wordt het volgnummer ten opzichte van de partitie uitgegeven.

De **waarde SequenceNumber** is een uniek 64-bits geheel getal dat is toegewezen aan een bericht wanneer het wordt geaccepteerd en opgeslagen door de broker en als interne id fungeert. Voor gepartitiestiteiten weerspiegelen de bovenste 16 bits de partitie-id. Sequentienummers worden overgenomen naar nul wanneer het 48/64-bits bereik is uitgeput.

Het volgnummer kan worden vertrouwd als een unieke id omdat het wordt toegewezen door een centrale en neutrale instantie en niet door clients. Het vertegenwoordigt ook de werkelijke volgorde van aankomst en is nauwkeuriger dan een tijdstempel als criterium voor de volgorde, omdat tijdstempels mogelijk niet hoog genoeg resolutie tegen extreme berichtsnelheden en kan worden onderworpen aan (maar minimale) klok scheef in situaties waarin de broker eigendom overgangen tussen knooppunten.

De absolute aankomstorder is bijvoorbeeld van belang in bedrijfsscenario's waarin een beperkt aantal aangeboden goederen wordt aangeboden op basis van 'first-come-first-served' terwijl de leveringen duren; de verkoop van concerttickets is een voorbeeld.

De tijdstempelfunctie fungeert als een neutrale en betrouwbare instantie die de UTC-tijd van aankomst van een bericht nauwkeurig vastlegt, zoals wordt weergegeven in de eigenschap **EnqueuedTimeUtc.** De waarde is nuttig als een bedrijfsscenario afhankelijk is van deadlines, zoals of een werkitem op een bepaalde datum vóór middernacht is ingediend, maar de verwerking ver achter ligt op de wachtrijachterstand.

## <a name="scheduled-messages"></a>Geplande berichten

U kunt berichten verzenden naar een wachtrij of onderwerp voor vertraagde verwerking, bijvoorbeeld om te plannen dat een taak op een bepaald moment beschikbaar komt voor verwerking door een systeem. Deze mogelijkheid realiseert een betrouwbare, op tijd gebaseerde scheduler op basis van gedistribueerde tijd.

Geplande berichten worden pas in de wachtrij na de gedefinieerde inschrijvingstijd ge materialiseren. Vóór die tijd kunnen geplande berichten worden geannuleerd. Met Annulering wordt het bericht verwijderd.

U kunt op twee manieren berichten plannen met behulp van een van onze clients:
- Gebruik de standaard-API voor verzenden, maar stel de eigenschap `ScheduledEnqueueTimeUtc` voor het bericht in voordat u het verzendt.
- Gebruik de API voor het planningsbericht en geef zowel het normale bericht als de geplande tijd door. Hiermee wordt het **SequenceNumber** van het geplande bericht retourneert, dat u later kunt gebruiken om het geplande bericht zo nodig te annuleren. 

Geplande berichten en hun reeksnummers kunnen ook worden ontdekt met behulp van [berichten bladeren.](message-browsing.md)

Het **SequenceNumber** voor een gepland bericht is alleen geldig wanneer het bericht deze status heeft. Wanneer het bericht overstroomt naar de actieve status, wordt het bericht aan de wachtrij toegevoegd alsof het op het huidige moment in de wachtrij is geplaatst. Dit omvat het toewijzen van een nieuwe **SequenceNumber**.

Omdat de functie is verankerd in afzonderlijke berichten en berichten slechts één keer kunnen worden in de enqueu, Service Bus geen ondersteuning voor terugkerende planningen voor berichten.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen Service Bus meer informatie over Service Bus messaging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)
