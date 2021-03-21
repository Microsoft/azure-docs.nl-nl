---
title: De Azure-portal gebruiken om Service Bus-onderwerpen en -abonnementen te maken
description: 'Quickstart: In deze quickstart leert u hoe u een Service Bus-onderwerp en abonnementen op dat onderwerp kunt maken met behulp van de Azure-portal.'
author: spelluru
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: 749ba3d2947dfd7defdf2549305f2a61c16ba713
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95803292"
---
# <a name="use-the-azure-portal-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>De Azure-portal gebruiken om een Service Bus-onderwerp en abonnementen te maken voor het onderwerp
In deze quickstart gebruikt u de Azure-portal om een Service Bus-onderwerp te maken en vervolgens abonnementen op dat onderwerp te maken. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Wat zijn Service Bus-onderwerpen en -abonnementen?
Service Bus-onderwerpen en -abonnementen bieden ondersteuning voor een berichtencommunicatiemodel op basis van *publiceren/abonneren*. Wanneer u gebruikmaakt van onderwerpen en abonnementen, communiceren onderdelen van een gedistribueerde toepassing niet direct met elkaar. In plaats daarvan wisselen ze berichten uit via een onderwerp dat als intermediair fungeert.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Anders dan bij Service Bus-wachtrijen, waarin elk bericht door een enkele gebruiker wordt verwerkt, bieden onderwerpen en abonnementen een één-naar-veel-communicatiewijze, waarbij een patroon voor publiceren/abonneren wordt gebruikt. Het is mogelijk meerdere abonnementen voor een onderwerp te registreren. Wanneer een bericht naar een onderwerp wordt verzonden, wordt dit vervolgens beschikbaar gemaakt voor elk abonnement, waar het vervolgens zelfstandig wordt afgehandeld/verwerkt. Een abonnement op een onderwerp lijkt op een virtuele wachtrij die kopieën van de berichten ontvangen die naar het onderwerp zijn verzonden. U kunt eventueel per abonnement filterregels voor een onderwerp registreren, waardoor u met behulp van een filter of een beperking kunt bepalen welke berichten naar een onderwerp door welke onderwerpabonnementen worden ontvangen.

Met Service Bus-onderwerpen en -abonnementen kunt u opschalen naar het verwerken van grote aantallen berichten voor grote aantallen gebruikers en toepassingen.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topics-three-subscriptions-portal](../../includes/service-bus-create-topics-three-subscriptions-portal.md)]

> [!NOTE]
> U kunt resources van Service Bus beheren met [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Met Service Bus Explorer kunnen gebruikers verbinding maken met een Service Bus-naamruimte en berichtenentiteiten op een eenvoudige manier beheren. Het hulpprogramma biedt geavanceerde functies zoals functionaliteit voor importeren/exporteren of de mogelijkheid van het testen van onderwerpen, wachtrijen, abonnementen, relay-services, Notification Hubs en Event Hubs. 

## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een Service Bus-naamruimte, een onderwerp in de naamruimte en drie abonnementen voor het onderwerp. Zie een van de volgende quickstarts in de sectie **Publiceren van en abonneren op berichten** voor meer informatie over het publiceren van berichten naar het onderwerp en het abonneren op berichten vanuit een abonnement. 

- [.NET](service-bus-dotnet-how-to-use-topics-subscriptions.md)
- [Java](service-bus-java-how-to-use-topics-subscriptions.md)
- [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md)
- [Python](service-bus-python-how-to-use-topics-subscriptions.md)
- [PHP](service-bus-php-how-to-use-topics-subscriptions.md)
- [Ruby](service-bus-ruby-how-to-use-topics-subscriptions.md)