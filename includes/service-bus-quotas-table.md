---
title: bestand opnemen
description: bestand opnemen
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 02/17/2021
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: ee066ff46f319749469a41e6decf12b35c0ee27e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100651937"
---
De volgende tabel vermeldt quotuminformatie die specifiek is voor Azure Service Bus-berichten. Voor informatie over prijzen en andere quota voor Service Bus raadpleegt u [Service Bus-prijzen](https://azure.microsoft.com/pricing/details/service-bus/).

| Quotumnaam | Bereik | Notities | Waarde |
| --- | --- | --- | --- |
| Maximumaantal Basic- of Standard-naamruimten per Azure-abonnement |Naamruimte |Verdere aanvragen voor aanvullende Basic of Standard-naamruimten worden geweigerd door de Azure Portal. |100|
| Maximumaantal Premium-naamruimten per Azure-abonnement |Naamruimte |Verdere aanvragen voor aanvullende Premium-naamruimten worden geweigerd door de Portal. |100 |
| Wachtrij- of onderwerpgrootte |Entiteit |Gedefinieerd bij het maken van de wachtrij of het onderwerp. <br/><br/> Verdere binnenkomende berichten worden geweigerd en er wordt een uitzondering ontvangen door de aanroepende code. |1, 2, 3, 4 GB of 5 GB.<br /><br />In de Premium-SKU en de Standard-SKU waar [partitioneren](../articles/service-bus-messaging/service-bus-partitioning.md) is ingeschakeld is de maximumgrootte van de wachtrij of het onderwerp 80 GB. |
| Aantal gelijktijdige verbindingen in een naamruimte |Naamruimte |Volgende aanvragen voor extra verbindingen worden geweigerd en er wordt een uitzondering ontvangen door de aanroepende code. REST-bewerkingen tellen niet mee voor gelijktijdige TCP-verbindingen. |Netberichten: 1000.<br /><br />AMQP: 5000. |
| Aantal gelijktijdige ontvangen verzoeken in een wachtrij, onderwerp of abonnementsentiteit |Entiteit |Verdere ontvangstverzoeken worden geweigerd en er wordt een uitzondering ontvangen door de aanroepende code. Dit quotum is van toepassing op het gecombineerde aantal gelijktijdige ontvangstbewerkingen in alle abonnementen op een onderwerp. |5.000 |
| Aantal onderwerpen of wachtrijen per naamruimte |Naamruimte |Verdere verzoeken om nieuwe onderwerpen of wachtrijen in de naamruimte te maken, worden geweigerd. Als dit wordt geconfigureerd via de [Azure Portal][Azure portal], wordt er een foutbericht gegenereerd. Als deze wordt aangeroepen vanuit de beheer-API, wordt een uitzondering ontvangen door de aanroepende code. |10.000 voor de Basic- of Standard-laag. Het totale aantal onderwerpen en wachtrijen in een naamruimte moet minder zijn dan of gelijk zijn aan 10.000. <br/><br/>Voor de Premium-laag 1.000 per Messaging-eenheid. |
| Aantal [gepartitioneerde onderwerpen](../articles/service-bus-messaging/service-bus-partitioning.md) of wachtrijen per naamruimte |Naamruimte |Verdere verzoeken om nieuwe gepartitioneerde onderwerpen of wachtrijen in de naamruimte te maken, worden geweigerd. Als dit wordt geconfigureerd via de [Azure Portal][Azure portal], wordt er een foutbericht gegenereerd. Indien aangeroepen vanuit de beheer-API, wordt de uitzondering **QuotaExceededException** ontvangen door de aanroepende code. |Basic- en Standard-lagen: 100.<br/><br/>Gepartitioneerde entiteiten worden niet ondersteund in de [Premium-laag](../articles/service-bus-messaging/service-bus-premium-messaging.md).<br/><br />Elke gepartitioneerde wachtrij of onderwerp telt voor het quotum van 1000 entiteiten per naamruimte. |
| Maximale grootte van ieder berichtentiteitspad: wachtrij of onderwerp |Entiteit |- |260 tekens. |
| Maximale grootte van berichtentiteitsnaam: naamruimte, abonnement of abonnementsregel |Entiteit |- |50 tekens. |
| Maximale berichtgrootte van een bericht-ID | Entiteit |- | 128 |
| Maximale berichtgrootte van een sessie-ID | Entiteit |- | 128 |
| Berichtgrootte voor een wachtrij, onderwerp of abonnementsentiteit |Entiteit |Binnenkomende berichten die deze quota overschrijden, worden geweigerd en er wordt een uitzondering ontvangen door de aanroepende code. |Maximale berichtgrootte: 256 KB voor [Standard-laag](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB voor [Premium-laag](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />Door oververhitting van het systeem is deze limiet minder dan deze waarden.<br /><br />Maximale header-grootte: 64 KB.<br /><br />Maximumaantal headereigenschappen in eigenschapverzameling: **byte/int.MaxValue**.<br /><br />Maximale grootte van eigenschap in eigenschapverzameling: Geen expliciete limiet. Beperkt door maximale header-grootte. |
| Berichteigenschapsgrootte voor een wachtrij, onderwerp of abonnementsentiteit |Entiteit | De uitzondering `SerializationException` wordt gegenereerd. |De maximale grootte van de berichteigenschap voor elke eigenschap is 32.000. De cumulatieve grootte van alle eigenschappen mag niet groter zijn dan 64.000. Deze limiet is van toepassing op de volledige header van het brokered-bericht, met zowel gebruikers eigenschappen als systeem eigenschappen, zoals Volg nummer, label en bericht-ID. |
| Aantal abonnementen per onderwerp |Entiteit |Volgende aanvragen om aanvullende abonnementen te maken voor het onderwerp worden geweigerd. Als resultaat wordt er een foutbericht weergegeven als de entiteit wordt geconfigureerd via de portal. Als deze wordt aangeroepen vanuit de beheer-API, wordt een uitzondering ontvangen door de aanroepende code. |2\.000 per onderwerp voor de laag Standard en Premium. |
| Aantal SQL-filters per onderwerp |Entiteit |Volgende aanvragen om aanvullende filters te maken voor het onderwerp worden geweigerd en er wordt een uitzondering ontvangen door de aanroepende code. |2.000 |
| Aantal correlatiefilters per onderwerp |Entiteit |Volgende aanvragen om aanvullende filters te maken voor het onderwerp worden geweigerd en er wordt een uitzondering ontvangen door de aanroepende code. |100.000 |
| Grootte van SQL-filters of acties |Naamruimte |Volgende aanvragen om aanvullende filters te maken, worden geweigerd en er wordt een uitzondering ontvangen door de aanroepende code. |Maximale lengte van de filtervoorwaarde: 1024 (1k).<br /><br />Maximale lengte van de regelactie: 1024 (1k).<br /><br />Maximumaantal expressies per regelactie: 32. |
| Aantal Shared Access Authorization-regels per naam ruimte, wachtrij of onderwerp |Entiteit, naamruimte |Volgende aanvragen om aanvullende regels te maken, worden geweigerd en er wordt een uitzondering ontvangen door de aanroepende code. |Maximumaantal regels per entiteitstype: 12. <br /><br /> Regels die zijn geconfigureerd in een Service Bus-naamruimte zijn van toepassing op alle typen: wachtrijen, onderwerpen. |
| Aantal berichten per transactie | Transactie | Extra binnenkomende berichten worden geweigerd en een uitzondering met de melding 'Kan niet meer dan 100 berichten in een transactie verzenden' wordt ontvangen door de aanroepende code. | 100 <br /><br /> Voor zowel de bewerking **Send()** als **SendAsync()** . |
| Aantal filterregels voor virtuele netwerken en IP's | Naamruimte | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com
