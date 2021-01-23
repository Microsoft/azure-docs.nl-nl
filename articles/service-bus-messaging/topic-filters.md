---
title: Filters voor Azure Service Bus-onderwerp | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe abonnees kunnen bepalen welke berichten ze willen ontvangen van een onderwerp door filters op te geven.
ms.topic: conceptual
ms.date: 01/22/2021
ms.openlocfilehash: 63cf6e67d4fa32c5c7f52f569094e1165554108c
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98742961"
---
# <a name="topic-filters-and-actions"></a>Onderwerpfilters en acties

Abonnees kunnen definiëren welke berichten ze willen ontvangen van een onderwerp. Deze berichten worden opgegeven in de vorm van een of meer benoemde abonnementsregels. Elke regel bestaat uit een **filter** voorwaarde waarmee bepaalde berichten worden geselecteerd en die **optioneel** een **actie** bevatten die het geselecteerde bericht aantekent. 

Alle regels **zonder acties** worden gecombineerd met een `OR` voor waarde en resulteren in **één bericht** op het abonnement, zelfs als u meerdere overeenkomende regels hebt. 

Elke regel **met een actie** produceert een kopie van het bericht. Dit bericht bevat een eigenschap die de `RuleName` naam van de overeenkomende regel bevat. De actie kan eigenschappen toevoegen of bijwerken, of eigenschappen uit het oorspronkelijke bericht verwijderen om een bericht in het abonnement te maken. 

Denkt u zich het volgende scenario eens in:

- Het abonnement heeft vijf regels.
- Twee regels bevatten acties.
- Drie regels bevatten geen acties.

Als u in dit voor beeld één bericht verzendt dat overeenkomt met alle vijf regels, krijgt u drie berichten over het abonnement. Dit zijn twee berichten voor twee regels met acties en één bericht voor drie regels zonder acties. 

Elk nieuw gemaakt onderwerp-abonnement heeft een eerste standaard abonnements regel. Als u geen expliciete filter voorwaarde opgeeft voor de regel, is het toegepaste filter het **ware** filter waarmee alle berichten in het abonnement kunnen worden geselecteerd. De standaard regel heeft geen gekoppelde actie voor aantekening.

## <a name="filters"></a>Filters
Service Bus ondersteunt drie filter voorwaarden:

-   *SQL-filters* -een **SQLFILTER** bevat een SQL-achtige voorwaardelijke expressie die in de Broker wordt geëvalueerd op basis van de door de gebruiker gedefinieerde eigenschappen en systeem eigenschappen van de binnenkomende berichten. Alle systeem eigenschappen moeten worden voorafgegaan door `sys.` in de voorwaardelijke expressie. De [SQL-taal deel verzameling voor filter voorwaarden](service-bus-messaging-sql-filter.md) test op het bestaan van eigenschappen ( `EXISTS` ), null-waarden ( `IS NULL` ), logische not/en/of relationele Opera Tors, eenvoudig numeriek reken kundig en eenvoudig tekst patroon dat overeenkomt met `LIKE` .

-   *Booleaanse filters* : de **TrueFilter** en **FalseFilter** leiden ertoe dat alle berichten (**waar**) of geen van de binnenkomende berichten (**False**) worden geselecteerd voor het abonnement. Deze twee filters worden afgeleid van het SQL-filter. 

-   *Correlatie filters* : een **CorrelationFilter** bevat een set voor waarden die overeenkomen met een of meer van de gebruikers-en systeem eigenschappen van een inkomend bericht. Een veelgebruikte toepassing is om te vergelijken met de eigenschap **CorrelationId** , maar deze kan er ook voor kiezen om te zoeken op basis van de volgende eigenschappen:

    - **Invoer**
     - **Label**
     - **MessageId**
     - **ReplyTo**
     - **ReplyToSessionId**
     - **SessionId** 
     - **Aan**
     - alle door de gebruiker gedefinieerde eigenschappen. 
     
     Er bestaat een overeenkomst wanneer de waarde van een binnenkomend bericht voor een eigenschap gelijk is aan de waarde die is opgegeven in het correlatie filter. Voor teken reeks expressies is de vergelijking hoofdletter gevoelig. Wanneer u meerdere match-eigenschappen opgeeft, combineert het filter ze als een logische en-voor waarde, wat betekent dat het filter overeenkomt met alle voor waarden.

Alle filters evalueren bericht eigenschappen. Filters kunnen de bericht tekst niet evalueren.

Complexe filter regels vereisen verwerkings capaciteit. Met name het gebruik van SQL-filter regels resulteert in een lagere algehele bericht doorvoer op het niveau van de abonnementen, het onderwerp en de naam ruimte. Indien mogelijk moeten toepassingen correlatie filters kiezen ten opzichte van SQL-achtige filters, omdat ze veel efficiënter worden verwerkt en minder invloed hebben op de door voer.

## <a name="actions"></a>Acties

Met SQL-filter voorwaarden kunt u een actie definiëren die het bericht kan annoteren door de eigenschappen en de waarden ervan toe te voegen, te verwijderen of te vervangen. De actie [maakt gebruik van een SQL-achtige expressie](service-bus-messaging-sql-filter.md) die los van de syntaxis van de SQL UPDATE-instructie. De actie wordt uitgevoerd op het bericht nadat het is gevonden en voordat het bericht is geselecteerd in het abonnement. De wijzigingen in de bericht eigenschappen zijn privé in het bericht dat naar het abonnement is gekopieerd.

## <a name="usage-patterns"></a>Gebruikspatronen

Het eenvoudigste gebruiks scenario voor een onderwerp is dat elk abonnement een kopie krijgt van elk bericht dat naar een onderwerp wordt verzonden, waardoor een broadcast patroon mogelijk is.

Met filters en acties worden twee verdere groepen patronen ingeschakeld: partitioneren en routeren.

Partitioneren gebruikt filters voor het distribueren van berichten over verschillende bestaande onderwerps abonnementen op een voorspel bare en wederzijds exclusieve manier. Het patroon voor partitionering wordt gebruikt wanneer een systeem wordt uitgeschaald voor het afhandelen van veel verschillende contexten in functiond identieke compartimenten die elk een subset van de algemene gegevens bevatten; bijvoorbeeld klant profiel gegevens. Bij partitioneren verzendt een uitgever het bericht naar een onderwerp zonder kennis van het partitie model. Het bericht wordt vervolgens verplaatst naar het juiste abonnement, dat vervolgens kan worden opgehaald door de Message Handler van de partitie.

Route ring gebruikt filters voor het distribueren van berichten over topic-abonnementen op een voorspel bare manier, maar niet noodzakelijkerwijs exclusief. In combi natie met de functie voor [automatisch door sturen](service-bus-auto-forwarding.md) kunnen onderwerps filters worden gebruikt voor het maken van complexe routerings grafieken binnen een service bus naam ruimte voor bericht distributie binnen een Azure-regio. Met Azure Functions of Azure Logic Apps fungeren als een brug tussen Azure Service Bus naam ruimten, kunt u complexe globale topologieën maken met directe integratie in line-of-business-toepassingen.

[!INCLUDE [service-bus-filter-examples](../../includes/service-bus-filter-examples.md)]



> [!NOTE]
> Omdat de Azure Portal nu ondersteuning biedt voor Service Bus Explorer-functionaliteit, kunnen abonnements filters vanuit de portal worden gemaakt of bewerkt. 

## <a name="next-steps"></a>Volgende stappen
Bekijk de volgende voorbeelden: 

- [.NET-eenvoudige zelf studie verzenden en ontvangen met filters](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveTutorialwithFilters/BasicSendReceiveTutorialWithFilters)
- [.NET-onderwerp filters](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters)
- [Azure Resource Manager-sjabloon](/azure/templates/microsoft.servicebus/2017-04-01/namespaces/topics/subscriptions/rules)