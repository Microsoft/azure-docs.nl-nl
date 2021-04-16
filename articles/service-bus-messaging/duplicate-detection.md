---
title: Azure Service Bus detectie van dubbele berichten | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u dubbele waarden kunt detecteren in Azure Service Bus berichten. Het dubbele bericht kan worden genegeerd en worden genegeerd.
ms.topic: article
ms.date: 04/14/2021
ms.openlocfilehash: a9ca9de988f5a3db15da773a870e2d929ab938c8
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107499475"
---
# <a name="duplicate-detection"></a>Detectie van duplicaten

Als een toepassing mislukt vanwege een onoorbare fout direct na het verzenden van een bericht en het opnieuw gestarte exemplaar van de toepassing ten onrechte denkt dat de eerdere levering van het bericht niet heeft plaatsgevonden, zorgt een volgende verzending ervoor dat hetzelfde bericht twee keer in het systeem wordt weergegeven.

Het is ook mogelijk dat er een fout optreedt op client- of netwerkniveau en dat een verzonden bericht wordt vastgelegd in de wachtrij, zonder dat de bevestiging naar de client is geretourneerd. In dit scenario heeft de client geen twijfel over het resultaat van de verzendbewerking.

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


## <a name="enable-duplicate-detection"></a>Duplicaatdetectie inschakelen

Naast het alleen inschakelen van duplicaatdetectie kunt u ook de grootte configureren van het tijdvenster voor de detectiegeschiedenis van duplicaten waarin bericht-id's worden bewaard.
Deze waarde wordt standaard ingesteld op 10 minuten voor wachtrijen en onderwerpen, met een minimumwaarde van 20 seconden tot de maximumwaarde van 7 dagen.

Het inschakelen van duplicaatdetectie en de grootte van het venster hebben rechtstreeks invloed op de doorvoer van de wachtrij (en het onderwerp), omdat alle vastgelegde bericht-id's moeten worden afgestemd op de zojuist verzonden bericht-id.

Als u het venster klein houdt, betekent dit dat er minder bericht-id's moeten worden bewaard en gematcht en dat de doorvoer minder wordt beïnvloed. Voor entiteiten met een hoge doorvoer waarvoor duplicaatdetectie is vereist, moet u het venster zo klein mogelijk houden.

### <a name="using-the-portal"></a>De portal gebruiken

In de portal wordt de functie voor duplicaatdetectie ingeschakeld tijdens het maken van entiteiten met het selectievakje **Duplicaatdetectie** inschakelen. Deze functie is standaard uitgeschakeld. De instelling voor het maken van nieuwe onderwerpen is gelijk.

![Schermopname van het dialoogvenster Wachtrij maken met de optie Duplicaatdetectie inschakelen geselecteerd en rood omschreven.][1]

> [!IMPORTANT]
> U kunt duplicaatdetectie niet in- of uitschakelen nadat de wachtrij is gemaakt. U kunt dit alleen doen op het moment dat u de wachtrij maakt. 

Het tijdvenster voor de duplicaatdetectiegeschiedenis kan worden gewijzigd in het venster met wachtrij- en onderwerpeigenschappen in de Azure Portal.

![Schermopname van Service Bus functie met de instelling Eigenschappen gemarkeerd en de optie Detectiegeschiedenis dupliceren rood gemarkeerd.][2]

### <a name="using-sdks"></a>Met behulp van SDK's

U kunt een van onze SDK's in .NET, Java, JavaScript, Python en Go gebruiken om de functie voor duplicaatdetectie in te schakelen bij het maken van wachtrijen en onderwerpen. U kunt ook het tijdvenster voor de duplicaatdetectiegeschiedenis wijzigen.
De eigenschappen die moeten worden bijgewerkt bij het maken van wachtrijen en onderwerpen om dit te bereiken zijn:
- `RequiresDuplicateDetection`
- `DuplicateDetectionHistoryTimeWindow`

Houd er rekening mee dat terwijl de namen van eigenschappen hier worden opgegeven in een trapsgetje, JavaScript- en Python-SDK's respectievelijk gebruik maken van een camel-casing en een casing van een casing voor een python.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen Service Bus meer informatie over het verzenden van berichten:

* [Service Bus-wachtrijen, -onderwerpen en -abonnementen](service-bus-queues-topics-subscriptions.md)
* [Aan de slag met Service Bus-wachtrijen](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus-onderwerpen en -abonnementen gebruiken](service-bus-dotnet-how-to-use-topics-subscriptions.md)

In scenario's waarin clientcode een bericht niet opnieuw kan verzenden met dezelfde *MessageId* als voorheen, is het belangrijk om berichten te ontwerpen die veilig opnieuw kunnen worden verwerkt. In [dit blogbericht over idemence worden](https://particular.net/blog/what-does-idempotent-mean) verschillende technieken beschreven om dat te doen.

[1]: ./media/duplicate-detection/create-queue.png
[2]: ./media/duplicate-detection/queue-prop.png
