---
title: Dubbele bericht detectie Azure Service Bus | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u dubbele items kunt detecteren in Azure Service Bus berichten. Het duplicaat bericht kan worden genegeerd en verwijderd.
ms.topic: article
ms.date: 01/13/2021
ms.openlocfilehash: 527c2dea34b02733907372b6e75a40a5ef5fc289
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711922"
---
# <a name="duplicate-detection"></a>Detectie van duplicaten

Als een toepassing mislukt als gevolg van een onherstelbare fout onmiddellijk nadat een bericht is verzonden en het opnieuw opstarten van de toepassing ten onrechte verstrijkt dat de voorafgaande bericht bezorging niet is uitgevoerd, zorgt een volgende verzen ding ertoe dat hetzelfde bericht twee keer wordt weer gegeven in het systeem.

Het is ook mogelijk dat er een ogen blik geduld op client-of netwerk niveau en dat een verzonden bericht in de wachtrij moet worden doorgevoerd, waarbij de bevestiging niet naar de client wordt geretourneerd. Dit scenario laat de client onzeker weten wat het resultaat van de verzend bewerking is.

Duplicaten detectie neemt de twijfel uit deze situaties door het inschakelen van de afzender om hetzelfde bericht opnieuw te verzenden. de wachtrij of het onderwerp verwijdert dubbele kopieën.

> [!NOTE]
> De laag basis van Service Bus biedt geen ondersteuning voor duplicaten detectie. De laag Standard en Premium ondersteunen duplicaten detectie. Zie [Service Bus prijzen](https://azure.microsoft.com/pricing/details/service-bus/)voor verschillen tussen deze lagen.

## <a name="how-it-works"></a>Hoe werkt het? 
Het inschakelen van duplicaten detectie helpt bij het bijhouden van de door de toepassing beheerde *MessageId* van alle berichten die worden verzonden naar een wachtrij of onderwerp tijdens een opgegeven periode. Als er een nieuw bericht wordt verzonden met een *MessageId* die tijdens het tijd venster is geregistreerd, wordt het bericht gerapporteerd als geaccepteerd (de verzend bewerking slaagt), maar wordt het zojuist verzonden bericht direct genegeerd en verwijderd. Er wordt geen rekening gehouden met andere onderdelen van het bericht dan de *MessageId* .

Toepassings beheer van de id is essentieel, omdat alleen de toepassing de *MessageId* kan koppelen aan een bedrijfsproces context van waaruit het zoals verwacht kan worden geconstrueerd wanneer er een fout optreedt.

Voor een bedrijfs proces waarbij tijdens de verwerking van bepaalde toepassings context meerdere berichten worden verzonden, kan de *MessageId* een combi natie zijn van de context-id op toepassings niveau, zoals een aankoop ordernummer, en het onderwerp van het bericht, bijvoorbeeld **12345.2017/Payment**.

De *MessageId* kan altijd een bepaalde GUID zijn, maar het verankeren van de id naar het bedrijfs proces levert een voorspel bare Herhaal baarheid, die gewenst is voor het effectief gebruiken van de duplicaten detectie functie.

> [!IMPORTANT]
>- Wanneer **partitioneren** is **ingeschakeld**, `MessageId+PartitionKey` wordt gebruikt om de uniekheid te bepalen. Wanneer sessies zijn ingeschakeld, moeten de partitie sleutel en de sessie-ID hetzelfde zijn. 
>- Wanneer **partitioneren** is **uitgeschakeld** (standaard), `MessageId` wordt alleen gebruikt om de uniekheid te bepalen.
>- Zie voor meer informatie over SessionId, PartitionKey en MessageId het [gebruik van partitie sleutels](service-bus-partitioning.md#use-of-partition-keys).
>- De [premier-laag](service-bus-premium-messaging.md) biedt geen ondersteuning voor partitionering, daarom raden we u aan om unieke bericht-id's in uw toepassingen te gebruiken en niet te vertrouwen op partitie sleutels voor duplicaten detectie. 


## <a name="enable-duplicate-detection"></a>Duplicaten detectie inschakelen

In de portal is de functie ingeschakeld tijdens het maken van de entiteit met het selectie vakje **Duplicaten detectie inschakelen** , die standaard uitgeschakeld is. De instelling voor het maken van nieuwe onderwerpen is gelijkwaardig.

![Scherm afbeelding van het dialoog venster wachtrij maken met de optie Duplicaten detectie inschakelen geselecteerd en in rood beschreven.][1]

> [!IMPORTANT]
> U kunt duplicaten detectie niet in-of uitschakelen nadat de wachtrij is gemaakt. U kunt dit alleen doen op het moment van het maken van de wachtrij. 

Via een programma kunt u de markering instellen met de eigenschap [QueueDescription. requiresDuplicateDetection](/dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection#Microsoft_ServiceBus_Messaging_QueueDescription_RequiresDuplicateDetection) in de volledige Framework .net API. Met de Azure Resource Manager-API wordt de waarde ingesteld met de eigenschap [queueProperties. requiresDuplicateDetection](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) .

De geschiedenis van de duplicaten detectie tijd wordt standaard ingesteld op 10 minuten voor wacht rijen en onderwerpen, met een minimum waarde van 20 seconden tot de maximale waarde van 7 dagen. U kunt deze instelling wijzigen in het venster Eigenschappen van de wachtrij en het onderwerp in de Azure Portal.

![Scherm afbeelding van de functie Service Bus met de instelling eigenschappen gemarkeerd en de optie geschiedenis van duplicaten detectie wordt rood beschreven.][2]

Via een programma kunt u de grootte van het duplicaten detectie venster configureren waarin bericht-id's worden bewaard, met behulp van de eigenschap [QueueDescription. DuplicateDetectionHistoryTimeWindow](/dotnet/api/microsoft.servicebus.messaging.queuedescription.duplicatedetectionhistorytimewindow#Microsoft_ServiceBus_Messaging_QueueDescription_DuplicateDetectionHistoryTimeWindow) met de volledige .NET Framework-API. Met de Azure Resource Manager-API wordt de waarde ingesteld met de eigenschap [queueProperties. duplicateDetectionHistoryTimeWindow](/azure/templates/microsoft.servicebus/namespaces/queues#property-values) .

Het inschakelen van duplicaten detectie en de grootte van het venster zijn direct van invloed op de door Voer van de wachtrij (en het onderwerp), omdat alle opgenomen bericht-id's moeten overeenkomen met de nieuw ingediende bericht-id.

Als u het venster klein houdt, betekent dit dat er minder bericht-id's moeten worden bewaard en gevonden en dat de door Voer minder wordt beïnvloed. Voor entiteiten met een hoge door Voer waarvoor duplicaten detectie is vereist, moet u het venster zo klein mogelijk laten.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over Service Bus Messa ging:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)

In scenario's waarin client code een bericht niet opnieuw kan verzenden met dezelfde *MessageId* als voorheen, is het belang rijk om berichten te ontwerpen die veilig kunnen worden verwerkt. In dit [blog bericht over Idempotence](https://particular.net/blog/what-does-idempotent-mean) worden verschillende technieken beschreven voor het uitvoeren van deze procedure.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
