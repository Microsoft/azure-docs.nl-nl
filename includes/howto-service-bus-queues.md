---
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/25/2018
ms.author: spelluru
ms.openlocfilehash: d3d33c87dc1adf65a53b71cc4c833e7f4a191670
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67176275"
---
## <a name="what-are-service-bus-queues"></a>Wat zijn Service Bus-wachtrijen?
Service Bus-wachtrijen ondersteunen een **Brokered Messaging**-communicatiemodel. Wanneer u gebruikmaakt van wachtrijen, communiceren onderdelen van een gedistribueerde toepassing niet direct met elkaar. In plaats daarvan wisselen ze berichten uit via een wachtrij die als intermediaire service (broker) fungeert. De maker van een bericht (afzender) draagt een bericht over aan de wachtrij en gaat vervolgens door met de verwerking ervan. Een gebruiker van een bericht (ontvanger) haalt het bericht asynchroon op uit de wachtrij en verwerkt het. De maker hoeft niet te wachten op een reactie van de gebruiker om door te kunnen gaan met het verwerken en verzenden van verdere berichten. Wachtrijen maken gebruik van het **FIFO-principe (first in, first out)** voor de berichtbezorging naar een of meer concurrerende gebruikers. Dat wil zeggen dat berichten doorgaans door de ontvangers worden ontvangen en verwerkt in de volgorde waarin ze zijn toegevoegd aan de wachtrij, en elk bericht wordt slechts door één berichtengebruiker ontvangen en verwerkt.

![QueueConcepts](./media/howto-service-bus-queues/sb-queues-08.png)

Service Bus-wachtrijen is een technologie voor algemeen gebruik die voor een groot aantal verschillende scenario's kan worden gebruikt:

* Communicatie tussen web- en werkrollen in een meerlaagse Azure-toepassing.
* Communicatie tussen on-premises apps en in Azure gehoste apps in een hybride oplossing.
* Communicatie tussen onderdelen van een gedistribueerde toepassing die on-premises wordt uitgevoerd in verschillende organisaties of afdelingen binnen een organisatie.

Door wachtrijen te gebruiken, kunt u uw toepassingen eenvoudiger schalen en kunt u meer tolerantie aan uw architectuur toevoegen.


