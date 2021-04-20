---
title: Azure Service Bus detectie van dubbele berichten | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u dubbele waarden kunt detecteren in Azure Service Bus berichten. Het dubbele bericht kan worden genegeerd en worden genegeerd.
ms.topic: article
ms.date: 04/19/2021
ms.openlocfilehash: baeda3509cb5646c658f79fb11610ecfdd1ffd3d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751270"
---
# <a name="duplicate-detection"></a>Detectie van duplicaten

Als een toepassing mislukt vanwege een onoorbare fout direct na het verzenden van een bericht en het opnieuw gestarte exemplaar van de toepassing ten onrechte denkt dat de eerdere levering van het bericht niet heeft plaatsgevonden, zorgt een volgende verzending ervoor dat hetzelfde bericht twee keer in het systeem wordt weergegeven.

Het is ook mogelijk dat een fout op client- of netwerkniveau even eerder optreedt en dat een verzonden bericht wordt vastgelegd in de wachtrij, zonder dat de bevestiging naar de client is geretourneerd. In dit scenario heeft de client geen twijfel over het resultaat van de verzendbewerking.

Duplicaatdetectie neemt de twijfel weg uit deze situaties door in te stellen dat de afzender hetzelfde bericht opnieuw moet verzenden en eventuele dubbele kopieën worden verwijderd uit de wachtrij of het onderwerp.

> [!NOTE]
> De basislaag van Service Bus biedt geen ondersteuning voor duplicaatdetectie. De lagen Standard en Premium bieden ondersteuning voor duplicaatdetectie. Zie Prijzen voor Service Bus verschillen [tussen deze lagen.](https://azure.microsoft.com/pricing/details/service-bus/)

## <a name="how-it-works"></a>Hoe werkt het? 
Door detectie van duplicaten in te stellen, kunt u de door de toepassing beheerde *MessageId* bijhouden van alle berichten die tijdens een opgegeven periode naar een wachtrij of onderwerp worden verzonden. Als er een nieuw bericht wordt verzonden met *een MessageId* die is geregistreerd tijdens het tijdvenster, wordt het bericht gerapporteerd als geaccepteerd (de verzendbewerking slaagt), maar het zojuist verzonden bericht wordt onmiddellijk genegeerd en verwijderd. Andere onderdelen van het bericht dan de *MessageId* worden niet in aanmerking genomen.

Toepassingsbeheer van de id is essentieel, omdat de toepassing alleen op die manier de *MessageId* kan binden aan een context van het bedrijfsproces van waaruit deze kan worden gereconstrueerd wanneer er een fout optreedt.

Voor een bedrijfsproces waarin meerdere berichten worden verzonden tijdens het verwerken van een toepassingscontext, kan de *MessageId* een samengestelde context-id op toepassingsniveau zijn, zoals een inkoopordernummer en het onderwerp van het bericht, bijvoorbeeld **12345.2017/payment.**

De *MessageId* kan altijd een GUID zijn, maar het veankeren van de id voor het bedrijfsproces levert voorspelbare herhaalbaarheid op, wat gewenst is voor het effectief gebruik van de functie voor duplicaatdetectie.

> [!IMPORTANT]
>- Wanneer **partitioneren** is **ingeschakeld,** wordt `MessageId+PartitionKey` gebruikt om de uniekheid te bepalen. Wanneer sessies zijn ingeschakeld, moeten de partitiesleutel en sessie-id hetzelfde zijn. 
>- Wanneer **partitioneren** is **uitgeschakeld** (standaard), wordt alleen `MessageId` gebruikt om de uniekheid te bepalen.
>- Zie Use of partition keys (Gebruik van partitiesleutels) voor meer informatie over SessionId, PartitionKey [en MessageId.](service-bus-partitioning.md#use-of-partition-keys)
>- De [premier-laag](service-bus-premium-messaging.md) biedt geen ondersteuning voor partitionering. Daarom raden we u aan unieke bericht-ID's in uw toepassingen te gebruiken en niet afhankelijk te zijn van partitiesleutels voor duplicaatdetectie. 


## <a name="duplicate-detection-window-size"></a>Grootte van venster voor duplicaatdetectie

Naast het alleen inschakelen van duplicaatdetectie kunt u ook de grootte configureren van het tijdvenster voor de detectiegeschiedenis van duplicaten waarin bericht-id's worden bewaard.
Deze waarde wordt standaard ingesteld op 10 minuten voor wachtrijen en onderwerpen, met een minimumwaarde van 20 seconden tot de maximumwaarde van 7 dagen.

Het inschakelen van duplicaatdetectie en de grootte van het venster is rechtstreeks van invloed op de doorvoer van de wachtrij (en het onderwerp), omdat alle vastgelegde bericht-id's moeten worden afgestemd op de zojuist verzonden bericht-id.

Als u het venster klein houdt, betekent dit dat er minder bericht-id's moeten worden bewaard en gematcht en dat de doorvoer minder wordt beïnvloed. Voor entiteiten met een hoge doorvoer waarvoor duplicaatdetectie is vereist, moet u het venster zo klein mogelijk houden.

## <a name="next-steps"></a>Volgende stappen
U kunt detectie van dubbele berichten inschakelen Azure Portal, PowerShell, CLI, Resource Manager sjabloon, .NET, Java, Python en JavaScript. Zie Detectie van dubbele berichten [inschakelen voor meer informatie.](enable-duplicate-detection.md) 

In scenario's waarin clientcode een bericht niet opnieuw kan verzenden met dezelfde *MessageId* als voorheen, is het belangrijk om berichten te ontwerpen die veilig opnieuw kunnen worden verwerkt. In [dit blogbericht over idemence worden](https://particular.net/blog/what-does-idempotent-mean) verschillende technieken beschreven om dat te doen.

Probeer de voorbeelden in de taal van uw keuze om de Azure Service Bus verkennen. 

- [Azure Service Bus-clientbibliotheekvoorbeelden voor Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus-clientbibliotheekvoorbeelden voor Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)
- [Azure Service Bus-clientbibliotheekvoorbeelden voor JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)
- [Azure Service Bus clientbibliotheekvoorbeelden voor TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)
- [Voorbeelden van Azure.Messaging.ServiceBus voor .NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)

Zoek voorbeelden voor de oudere .NET- en Java-clientbibliotheken hieronder:
- [Voorbeelden van Microsoft.Azure.ServiceBus voor .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)
- [azure-servicebus-voorbeelden voor Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)

