---
title: Azure Service Bus - uitstellen van berichten
description: In dit artikel wordt uitgelegd hoe u de levering van Azure Service Bus uitstellen. Het bericht blijft in de wachtrij of het abonnement staan, maar wordt nog niet verwerkt.
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: 171fc6e1a3c779802747d34ac631d265e8c8926f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107869487"
---
# <a name="message-deferral"></a>Berichten uitstellen
Wanneer een wachtrij of abonnementsclient een bericht ontvangt dat hij bereid is te verwerken, maar de verwerking momenteel niet mogelijk is vanwege speciale omstandigheden, kan het bericht worden opgehaald naar een later punt. Het bericht blijft in de wachtrij of het abonnement staan, maar wordt nog niet verwerkt.

> [!NOTE]
> Uitgestelde berichten worden na het verlopen niet automatisch verplaatst naar de wachtrij voor inlopende [berichten.](./service-bus-dead-letter-queues.md#time-to-live) Dit gedrag is van ontwerp.

## <a name="sample-scenarios"></a>Voorbeeldscenario's
Uitstel is een functie die speciaal is gemaakt voor scenario's voor werkstroomverwerking. Werkstroom-frameworks vereisen mogelijk dat bepaalde bewerkingen in een bepaalde volgorde worden verwerkt. Ze moeten de verwerking van sommige ontvangen berichten mogelijk uitstellen totdat de voorgeschreven werkzaamheden die door andere berichten zijn gedaan, zijn voltooid.

Een eenvoudig illustratief voorbeeld is een bestellingsverwerkingsreeks waarin een betalingsmelding van een externe betalingsprovider in een systeem wordt weergegeven voordat de overeenkomende aankooporder is doorgegeven van het winkelfront naar het uitvoeringssysteem. In dat geval kan het fulfillmentsysteem de verwerking van de betalingsmelding uitstellen totdat er een order is waarmee deze kan worden verbonden. In scenario's waarin berichten van verschillende bronnen een werkstroom vooruitsturen, kan de realtime uitvoeringsorder inderdaad juist zijn, maar de berichten die de resultaten weerspiegelen, kunnen niet in de juiste volgorde binnenkomen.

Uiteindelijk helpt uitstel bij het opnieuw rangschikken van berichten van de aankomstorder in een volgorde waarin ze kunnen worden verwerkt, terwijl deze berichten veilig in het berichtenopslag worden bewaard waarvoor verwerking moet worden uitgesteld.

Als een bericht niet kan worden verwerkt omdat een bepaalde resource voor het verwerken van dat bericht tijdelijk niet beschikbaar is, maar de berichtverwerking niet per [](message-sequencing.md) se moet worden uitgesteld, is een manier om dat bericht een paar minuten aan de zijkant te zetten, het volgnummer in een gepland bericht te onthouden dat over enkele minuten moet worden geplaatst en het uitgestelde bericht opnieuw op te halen wanneer het geplande bericht binnenkomt. Als een berichten-handler voor alle bewerkingen afhankelijk is van een database en die database tijdelijk niet beschikbaar is, mag deze geen uitstel gebruiken, maar de ontvangst van berichten geheel opschorten totdat de database weer beschikbaar is. 

## <a name="retrieving-deferred-messages"></a>Uitgestelde berichten ophalen
Uitgestelde berichten blijven in de hoofdwachtrij staan, samen met alle andere actieve berichten (in tegenstelling tot berichten die in een subqueue staan), maar ze kunnen niet meer worden ontvangen met behulp van de normale ontvangstbewerkingen. Uitgestelde berichten kunnen worden gedetecteerd via [berichten browsen](message-browsing.md) als een toepassing deze niet meer kan volgen.

Als u een uitgesteld bericht wilt ophalen, is de eigenaar verantwoordelijk voor het onthouden van het volgnummer wanneer dit wordt uitgesteld. Elke ontvanger die het volgnummer van een uitgesteld bericht kent, kan het bericht later ontvangen met behulp van ontvangstmethoden die het volgnummer als parameter gebruiken. Zie Message [sequencing and timestamps (Berichtvolgorde en tijdstempels)](message-sequencing.md)voor meer informatie over reeksnummers.

## <a name="next-steps"></a>Volgende stappen
Probeer de voorbeelden in de taal van uw keuze om de Azure Service Bus verkennen. 

- [Azure Service Bus-clientbibliotheekvoorbeelden voor Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Azure Service Bus voorbeelden van clientbibliotheek voor Python](/samples/azure/azure-sdk-for-python/servicebus-samples/) : zie **receive_deferred_message_queue.py-voorbeeld.** 
- [Azure Service Bus voorbeelden van clientbibliotheek voor JavaScript:](/samples/azure/azure-sdk-for-js/service-bus-javascript/) zie het voorbeeld **met geavanceerde/deferral.js-bestanden.** 
- [Azure Service Bus clientbibliotheekvoorbeelden voor TypeScript:](/samples/azure/azure-sdk-for-js/service-bus-typescript/) zie het **voorbeeld advanced/deferral.ts.** 
- [Azure.Messaging.ServiceBus-voorbeelden voor .NET:](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/) zie het **settling messages-voorbeeld.** 

Zoek voorbeelden voor de oudere .NET- en Java-clientbibliotheken hieronder:
- [Voorbeelden van Microsoft.Azure.ServiceBus voor .NET:](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/) zie **het voorbeeld voor uitstel.** 
- [azure-servicebus-voorbeelden voor Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/azure-servicebus/MessageBrowse)
