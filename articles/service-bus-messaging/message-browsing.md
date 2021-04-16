---
title: Azure Service Bus - bladeren door berichten
description: Door de berichten Service Bus bekijken, kan Azure Service Bus client alle berichten in een wachtrij of abonnement opsnoemen.
ms.topic: article
ms.date: 03/29/2021
ms.openlocfilehash: deafe9e6ddeeebf233922aade36823ddaaede864
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520119"
---
# <a name="message-browsing"></a>Berichten doorzoeken
Door berichten te bladeren of te bekijken, kan een Service Bus-client alle berichten in een wachtrij of een abonnement opsnoemen voor diagnostische en debuggingsdoeleinden.

De bewerking Peek voor een wachtrij of een abonnement retourneert het aangevraagde aantal berichten. In de volgende tabel ziet u de typen berichten die worden geretourneerd door de bewerking Peek. 

| Type berichten | Opgenomen? | 
| ---------------- | ----- | 
| Actieve berichten | Ja |
| Berichten met een bericht in een dead-letter | Nee | 
| Vergrendelde berichten | Ja |
| Verlopen berichten |  Dit kan zijn (voordat ze in een dead-letter zijn) |
| Geplande berichten | Ja voor wachtrijen. Nee voor abonnementen |

## <a name="dead-lettered-messages"></a>Berichten met een bericht in een dead-letter
Als u berichten met in wachtrijen geplaatste berichten van een wachtrij of abonnement wilt bekijken, moet de **peek-bewerking** worden uitgevoerd op de wachtrij voor inlopende letters die is gekoppeld aan de wachtrij of het abonnement. Zie Toegang tot wachtrijen voor [in wachtrijen voor inlopende berichten voor meer informatie.](service-bus-dead-letter-queues.md#path-to-the-dead-letter-queue)

## <a name="expired-messages"></a>Verlopen berichten
Verlopen berichten kunnen worden opgenomen in de resultaten die worden geretourneerd door de bewerking Peek. Verbruikte en verlopen berichten worden opgeschoond door een asynchrone 'garbage collection'-run. Deze stap kan niet noodzakelijkerwijs onmiddellijk plaatsvinden nadat berichten zijn verlopen. Daarom kan een peek-bewerking berichten retourneren die al zijn verlopen. Deze berichten worden verwijderd of in een impasse geplaatst wanneer een ontvangstbewerking de volgende keer wordt aangeroepen in de wachtrij of het abonnement. Houd rekening met dit gedrag wanneer u uitgestelde berichten uit de wachtrij probeert te herstellen. 

Een verlopen bericht komt niet meer in aanmerking voor het regelmatig ophalen op een andere manier, zelfs niet wanneer het wordt geretourneerd door Peek. Het retourneren van deze berichten is een ontwerp, omdat Peek een hulpprogramma voor diagnostische gegevens is dat de huidige status van het logboek weerspiegelt.

## <a name="locked-messages"></a>Vergrendelde berichten
Peek retourneert ook berichten die **zijn vergrendeld** en momenteel worden verwerkt door andere ontvangers. Omdat Peek echter een niet-verbonden momentopname retourneert, kan de vergrendelingstoestand van een bericht niet worden waargenomen bij binnengekeken berichten.

## <a name="peek-apis"></a>API's bekijken
Peek werkt met wachtrijen, abonnementen en hun wachtrijen voor in wachtrijen zonder wachtrijen. 

Wanneer deze herhaaldelijk wordt aangeroepen, worden met de peek-bewerking alle berichten in de wachtrij of het abonnement op volgorde van het laagste beschikbare volgnummer naar het hoogste opsnoemd. Het is de volgorde waarin berichten in de enqueu zijn opgeslagen, niet de volgorde waarin berichten uiteindelijk kunnen worden opgehaald.

U kunt ook een SequenceNumber doorgeven aan een peek-bewerking. Deze wordt gebruikt om te bepalen waar u wilt beginnen met het bekijken van de locatie. U kunt volgende aanroepen naar de peek-bewerking uitvoeren zonder de parameter op te geven die u verder wilt opsnoemen.

## <a name="next-steps"></a>Volgende stappen
Probeer de voorbeelden in de taal van uw keuze om de functie voor het bekijken of bladeren van berichten te verkennen:

- [Azure Service Bus-clientbibliotheekvoorbeelden voor Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)  -  **Een voorbeeld van een bericht** bekijken
- [Azure Service Bus-clientbibliotheekvoorbeelden voor Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)  -  **receive_peek.py-voorbeeld**
- [Azure Service Bus-clientbibliotheekvoorbeelden voor JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)  -  **browseMessages.js** voorbeeld
- [Azure Service Bus clientbibliotheekvoorbeelden voor TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)  -  **voorbeeld browseMessages.ts**
- [Voorbeelden van Azure.Messaging.ServiceBus voor .NET:](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) bekijk de methoden voor ontvangstklassen in de [referentiedocumentatie](/dotnet/api/azure.messaging.servicebus).

Zoek voorbeelden voor de oudere .NET- en Java-clientbibliotheken hieronder:
- [Voorbeelden van Microsoft.Azure.ServiceBus voor .NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/)  -  Voorbeeld van bladeren door berichten **(peek)** 
- [azure-servicebus-voorbeelden voor Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)  -  **Voorbeeld van bladeren door** berichten. 
