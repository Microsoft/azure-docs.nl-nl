---
title: Beperkings problemen of ' 429-te veel aanvragen ' oplossen
description: Het tijdelijk beperken van problemen of HTTP 429 te veel aanvragen fouten in Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: deli, logicappspm
ms.topic: conceptual
ms.date: 04/13/2020
ms.openlocfilehash: ea153b1927a337be29c2eb69e2417cc250abf5e8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94366047"
---
# <a name="handle-throttling-problems-429---too-many-requests-errors-in-azure-logic-apps"></a>Omgaan met beperkings problemen (429-"te veel aanvragen") in Azure Logic Apps

In [Azure Logic apps](../logic-apps/logic-apps-overview.md)retourneert uw logische app de [fout ' http 429 te veel aanvragen '](https://developer.mozilla.org/docs/Web/HTTP/Status/429) wanneer er sprake is van beperking. dit gebeurt wanneer het aantal aanvragen de snelheid overschrijdt waarmee het doel gedurende een bepaalde tijd kan worden verwerkt. Beperking kan problemen veroorzaken, zoals vertraagde gegevens verwerking, verminderde prestatie snelheid en fouten, zoals het overschrijden van het opgegeven beleid voor opnieuw proberen.

![Beperking in SQL Server-connector](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-error.png)

Hier volgen enkele veelvoorkomende typen beperking die uw logische app kan ervaren:

* [Logische app](#logic-app-throttling)
* [Connector](#connector-throttling)
* [Doel service of systeem](#destination-throttling)

<a name="logic-app-throttling"></a>

## <a name="logic-app-throttling"></a>Beperking van logische apps

De Azure Logic Apps-service heeft zijn eigen [doorvoer limieten](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Als uw logische app deze limieten overschrijdt, wordt uw logische app-resource dus beperkt, niet alleen een specifiek exemplaar of worden uitgevoerd.

Als u de beperkings gebeurtenissen op dit niveau wilt vinden, controleert u het deel venster **metrische gegevens** van de logische app in de Azure Portal.

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Selecteer **metrische gegevens** onder **bewaking** in het menu van de logische app.

1. Onder **grafiek titel** selecteert u **metrische gegevens toevoegen** zodat u een andere metriek toevoegt aan de bestaande.

1. Selecteer in de eerste meet balk, in de lijst **metrische gegevens** , de **actie vertraagde gebeurtenissen**. Selecteer in de tweede meet balk de optie **trigger vertraagde gebeurtenissen** in de lijst **metriek** .

U hebt de volgende opties om bandbreedte beperking op dit niveau te verwerken:

* Beperk het aantal logische app-exemplaren dat tegelijkertijd kan worden uitgevoerd.

  Als de trigger voorwaarde van uw logische app meer dan één keer wordt voldaan, worden meerdere trigger exemplaren voor uw logische app gelijktijdig of *parallel* uitgevoerd. Dit gedrag houdt in dat elke trigger instantie wordt gestart voordat het vorige werk stroom exemplaar wordt uitgevoerd.

  Hoewel het standaard aantal trigger instanties dat gelijktijdig kan worden uitgevoerd, [onbeperkt](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits)is, kunt u dit aantal beperken door [de gelijktijdigheids instelling voor de trigger in te scha kelen](../logic-apps/logic-apps-workflow-actions-triggers.md#change-trigger-concurrency)en, indien nodig, een andere limiet te selecteren dan de standaard waarde.

* Modus voor hoge door Voer inschakelen.

  Een logische app heeft een [standaard limiet voor het aantal acties dat kan worden uitgevoerd gedurende een interval van 5 minuten](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Als u deze limiet wilt verhogen voor het maximum aantal acties, schakelt u de [modus voor hoge door Voer](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode) in voor uw logische app.

* Schakel het gedrag voor het debatcheren van matrices (' splitsen op ') in triggers uit.

  Als met een trigger een matrix wordt geretourneerd voor de resterende werk stroom acties die moeten worden verwerkt, splitst de trigger de instelling voor het [  instellen](../logic-apps/logic-apps-workflow-actions-triggers.md#split-on-debatch) van de matrix items en wordt er een werk stroom exemplaar voor elk matrix item gestart, waardoor meerdere gelijktijdige uitvoeringen effectief worden geactiveerd tot de [ **splitsing van** de limiet](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Als u beperking wilt beheren, schakelt u de functie **splitsen bij** gedrag uit en laat u de gehele matrix met één aanroep verwerken in plaats van één item per oproep te verwerken.

* Verdeel acties in kleinere logische apps.

  Zoals eerder vermeld, is een logische app beperkt tot een [standaard aantal acties dat gedurende een periode van vijf minuten kan worden uitgevoerd](../logic-apps/logic-apps-limits-and-config.md#throughput-limits). Hoewel u deze limiet kunt verhogen door de [modus voor hoge door Voer](../logic-apps/logic-apps-limits-and-config.md#run-high-throughput-mode)in te scha kelen, kunt u ook overwegen of u de acties van uw logische app wilt opsplitsen in kleinere logische apps zodat het aantal acties dat in elke logische app wordt uitgevoerd, onder de limiet blijft. Op die manier vermindert u de belasting van één logische app-resource en distribueert u de belasting over meerdere logische apps. Deze oplossing werkt beter voor acties waarmee grote gegevens sets worden verwerkt of waarmee zo veel gelijktijdig uitgevoerde acties, herhalingen of acties binnen elke lus worden herhaald die ze overschrijden.

  Met deze logische app kunnen bijvoorbeeld alle tabellen uit een SQL Server Data Base worden opgehaald en worden de rijen uit elke tabel opgehaald. Met de **voor elke** lus wordt elke tabel gelijktijdig herhaald, zodat de actie **rijen ophalen** de rijen voor elke tabel retourneert. Op basis van de hoeveel heden gegevens in deze tabellen, kunnen deze acties de limiet voor het uitvoeren van acties overschrijden.

  ![Logische app voor het herstructureren](./media/handle-throttling-problems-429-errors/refactor-logic-app-before-version.png)

  Na het herstructureren is de logische app nu een bovenliggende en onderliggende logische app. Het bovenliggende knoop punt haalt de tabellen op uit SQL Server en roept vervolgens een onderliggende logische app aan voor elke tabel om de rijen op te halen:

  ![Een logische app maken voor één actie](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-1.png)

  Dit is de onderliggende logische app die wordt aangeroepen door de bovenliggende logische app om de rijen voor elke tabel op te halen:

  ![Een andere logische app maken voor een tweede actie](./media/handle-throttling-problems-429-errors/refactor-logic-app-single-connection-2.png)

<a name="connector-throttling"></a>

## <a name="connector-throttling"></a>Beperking van de connector

Elke connector heeft zijn eigen beperkings limieten, die u kunt vinden op de pagina met technische Naslag informatie over de connector. De [Azure service bus-connector](/connectors/servicebus/) heeft bijvoorbeeld een beperkings limiet die maxi maal 6.000 aanroepen per minuut toestaat, terwijl de SQL Server-connector [beperkingen heeft die variëren op basis van het bewerkings type](/connectors/sql/).

Sommige triggers en acties, zoals HTTP, hebben een [beleid voor opnieuw proberen](../logic-apps/logic-apps-exception-handling.md#retry-policies) die u kunt aanpassen op basis van de [limieten voor het beleid voor opnieuw proberen](../logic-apps/logic-apps-limits-and-config.md#retry-policy-limits) voor het implementeren van uitzonderings verwerking. Dit beleid bepaalt of en hoe vaak een trigger of actie een aanvraag opnieuw probeert wanneer de oorspronkelijke aanvraag is mislukt of een time-out optreedt, en resulteert in een 408, 429 of 5xx respons. Als de beperking wordt gestart en er wordt een 429-fout geretourneerd, Logic Apps het beleid voor opnieuw proberen op te starten wanneer dit wordt ondersteund.

Als u wilt weten of een trigger of actie beleid voor opnieuw proberen ondersteunt, controleert u de instellingen van de trigger of actie. Als u de nieuwe pogingen van een trigger of actie wilt weer geven, gaat u naar de uitvoerings geschiedenis van de logische app, selecteert u de uitvoering die u wilt controleren en vouwt u deze trigger of actie uit om details over invoer, uitvoer en nieuwe pogingen weer te geven, bijvoorbeeld:

![Uitvoerings geschiedenis, nieuwe pogingen, invoer en uitvoer van de actie weer geven](./media/handle-throttling-problems-429-errors/example-429-too-many-requests-retries.png)

Hoewel de geschiedenis van nieuwe pogingen fout gegevens bevat, is het mogelijk dat er geen onderscheid kan worden tussen de beperking van de connector en de [doel beperking](#destination-throttling). In dit geval moet u mogelijk de details van het antwoord controleren of enkele beperkingen voor interval berekening uitvoeren om de bron te identificeren.

Voor logische apps in de wereld wijde multi tenant-Azure Logic Apps service, wordt er een beperking toegepast op het *verbindings* niveau. Voor logische apps die worden uitgevoerd in een [Integration service Environment (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), is er nog steeds een beperking voor niet-ISE, omdat deze worden uitgevoerd in de globale, multi tenant-Logic apps service. ISE-verbindingen, die worden gemaakt door ISE-connectors, worden echter niet beperkt omdat ze worden uitgevoerd in uw ISE.

U hebt de volgende opties om bandbreedte beperking op dit niveau te verwerken:

* Stel meerdere verbindingen voor één actie in, zodat de logische app de gegevens partitioneert voor verwerking.

  Voor deze optie kunt u overwegen of u de werk belasting wilt distribueren door de aanvragen van een actie te delen tussen meerdere verbindingen met dezelfde bestemming met dezelfde referenties.

  Stel bijvoorbeeld dat uw logische app tabellen uit een SQL Server Data Base haalt en vervolgens de rijen uit elke tabel ophaalt. Op basis van het aantal rijen dat u moet verwerken, kunt u meerdere verbindingen en meerdere **voor elke** lussen gebruiken om het totale aantal rijen te verdelen in kleinere sets voor verwerking. In dit scenario worden twee **voor elke** lussen gebruikt om het totale aantal rijen in de helft te splitsen. De eerste **voor elke** lus gebruikt een expressie die de eerste helft ophaalt. De andere **voor elke** lus gebruikt een andere expressie die de tweede helft ophaalt, bijvoorbeeld:<p>

    * Expressie 1: de `take()` functie haalt de front van een verzameling op. Zie de [ **`take()`** functie](workflow-definition-language-functions-reference.md#take)voor meer informatie.

      `@take(collection-or-array-name, div(length(collection-or-array-name), 2))`

    * Expressie 2: `skip()` met de functie wordt de front van een verzameling verwijderd en worden alle andere items geretourneerd. Zie de [ **`skip()`** functie](workflow-definition-language-functions-reference.md#skip)voor meer informatie.

      `@skip(collection-or-array-name, div(length(collection-or-array-name), 2))`

    Hier volgt een visueel voor beeld dat laat zien hoe u deze expressies kunt gebruiken:

    ![Meerdere verbindingen voor één actie maken en gebruiken](./media/handle-throttling-problems-429-errors/create-multiple-connections-per-action.png)

* Stel een andere verbinding in voor elke actie.

  Voor deze optie kunt u overwegen of u de werk belasting wilt distribueren door de aanvragen van elke actie te spreiden over hun eigen verbinding, zelfs wanneer acties verbinding maken met dezelfde service of hetzelfde systeem en dezelfde referenties gebruiken.

  Stel bijvoorbeeld dat uw logische app de tabellen uit een SQL Server Data Base haalt en elke rij in elke tabel ophaalt. U kunt afzonderlijke verbindingen gebruiken zodat de tabellen die gebruikmaken van één verbinding worden gebruikt, terwijl elke rij wordt gebruikt voor het ophalen van een andere verbinding.

  ![Voor elke actie een andere verbinding maken en gebruiken](./media/handle-throttling-problems-429-errors/create-connection-per-action.png)

* Wijzig de gelijktijdigheid of parallellisme op een [lus voor elke](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

  Standaard worden voor elke herhalings herhalingen uitgevoerd op hetzelfde tijdstip tot aan de [gelijktijdigheids limiet](../logic-apps/logic-apps-limits-and-config.md#looping-debatching-limits). Als u een connector hebt die wordt beperkt binnen een lus voor elke, kunt u het aantal herhalings iteraties verminderen die parallel worden uitgevoerd. Raadpleeg de volgende onderwerpen voor meer informatie:
  
  * [' Voor elke ' lussen: gelijktijdige uitvoering wijzigen of opeenvolgend uitvoeren](../logic-apps/logic-apps-control-flow-loops.md#sequential-foreach-loop)

  * [Schema voor werk stroom definitie taal-voor elke lussen](../logic-apps/logic-apps-workflow-actions-triggers.md#foreach-action)

  * [Schema voor werk stroom definitie taal-wijzigen voor elke lus-gelijktijdigheid](../logic-apps/logic-apps-workflow-actions-triggers.md#change-for-each-concurrency)

  * [Schema voor werk stroom definitie taal-elke wordt sequentieel uitgevoerd](../logic-apps/logic-apps-workflow-actions-triggers.md#sequential-for-each)

<a name="destination-throttling"></a>

## <a name="destination-service-or-system-throttling"></a>Doel service of systeem beperking

Hoewel een connector zijn eigen beperkings limieten heeft, hebben de doel service of het systeem dat wordt aangeroepen door de connector mogelijk ook beperkings limieten. Sommige Api's in micro soft Exchange server hebben bijvoorbeeld strikte beperkings limieten dan de Office 365 Outlook-Connector.

Standaard worden de instanties van een logische app en alle lussen of vertakkingen binnen die instanties *parallel* uitgevoerd. Dit gedrag houdt in dat meerdere exemplaren tegelijkertijd hetzelfde eind punt kunnen aanroepen. Elk exemplaar weet niet over het andere bestaan, dus pogingen om mislukte acties opnieuw uit te voeren, kunnen [race omstandigheden](https://en.wikipedia.org/wiki/Race_condition) maken waarbij meerdere aanroepen gelijktijdig proberen te worden uitgevoerd, maar dat de aanroepen bij de doel service of het systeem moeten arriveren voordat de beperking wordt gestart.

Stel bijvoorbeeld dat u een matrix hebt met 100 items. U gebruikt een lus ' voor elke ' om de matrix door te lopen en het gelijktijdigheids beheer van de lus in te scha kelen, zodat u het aantal parallelle iteraties kunt beperken tot 20 of de [huidige standaard limiet](../logic-apps/logic-apps-limits-and-config.md#concurrency-looping-and-debatching-limits). Binnen die lus voegt een actie een item uit de matrix toe aan een SQL Server-Data Base, waardoor slechts 15 aanroepen per seconde worden toegestaan. Dit scenario resulteert in een beperkings probleem, omdat er een nieuwe poging wordt gedaan om het programma te bouwen en nooit uit te voeren.

In deze tabel wordt de tijd lijn beschreven voor wat er gebeurt in de lus wanneer het interval voor nieuwe pogingen voor de actie 1 seconde is:

| Tijdstip | Aantal acties dat wordt uitgevoerd | Aantal mislukte acties | Aantal nieuwe pogingen dat wacht |
|---------------|----------------------------|-----------------------------|---------------------------|
| T + 0 seconden | 20 invoeg bladen | 5 mislukt vanwege SQL-limiet | 5 nieuwe pogingen |
| T + 0,5 seconden | 15 invoeg bladen vanwege vorige 5 nieuwe pogingen in de wacht stand | De 15 mislukt, omdat de vorige SQL-limiet nog gedurende een andere 0,5 seconden van kracht is | 20 nieuwe pogingen <br>(vorige 5 + 15 nieuwe) |
| T + 1 seconde | 20 invoeg bladen | 5 fouten plus vorige 20 nieuwe pogingen vanwege SQL-limiet | 25 nieuwe pogingen (vorige 20 + 5 nieuw)
|||||

U hebt de volgende opties om bandbreedte beperking op dit niveau te verwerken:

* Maak Logic apps zodat elk één bewerking wordt uitgevoerd.

  * Als u doorgaat met het voor beeld SQL Server scenario in deze sectie, kunt u een logische app maken die matrix items in een wachtrij plaatst, zoals een [Azure service bus wachtrij](../connectors/connectors-create-api-servicebus.md). Vervolgens maakt u een nieuwe logische app die alleen de invoeg bewerking voor elk item in die wachtrij uitvoert. Op die manier wordt slechts één exemplaar van een logische app op een wille keurige tijd uitgevoerd, waardoor de invoeg bewerking wordt voltooid en wordt verplaatst naar het volgende item in de wachtrij, of als het exemplaar 429 fouten ontvangt, maar geen productspecifieke pogingen doet.

  * Maak een bovenliggende logische app die een onderliggende of geneste logische app aanroept voor elke actie. Als de bovenliggende apps moet worden aangeroepen op basis van het resultaat van de bovenliggende toepassing, kunt u een voorwaarde actie of een switch-actie gebruiken die bepaalt welke onderliggende app moet worden aangeroepen. Dit patroon kan u helpen het aantal aanroepen of bewerkingen te verminderen.

    Stel dat u twee Logic apps hebt, elk met een polling trigger waarmee uw e-mail account elke minuut wordt gecontroleerd op een specifiek onderwerp, zoals "geslaagd" of "mislukt". Deze installatie resulteert in 120 aanroepen per uur. Als u in plaats daarvan een enkele bovenliggende logische app maakt die elke minuut pollt, maar een onderliggende logische app aanroept die wordt uitgevoerd op basis van het feit of het onderwerp ' geslaagd ' of ' fout ' is, knipt u het aantal polling controles in een halve of 60 in dit geval.

* Batch verwerking instellen.

  Als de doel service batch bewerkingen ondersteunt, kunt u de verwerkings snelheid verpakken door items in groepen of batches te verwerken in plaats van afzonderlijk. Als u de oplossing voor batch verwerking wilt implementeren, maakt u een logische app-ontvanger en een logische app voor het verzenden van batches. De batch-afzender verzamelt berichten of items totdat aan de opgegeven criteria wordt voldaan en verzendt vervolgens de berichten of items in één groep. De batch-ontvanger accepteert die groep en verwerkt die berichten of items. Zie [Batch Process-berichten in groepen](../logic-apps/logic-apps-batch-process-send-receive-messages.md)voor meer informatie.

* Gebruik de webhook-versies voor triggers en acties in plaats van de polling versies.

  Hoe komt dat? Een polling trigger blijft de doel service of het systeem op specifieke intervallen controleren. Een regel matig interval, zoals elke seconde, kan beperkings problemen veroorzaken. Een webhook-trigger of-actie, zoals [http-webhook](../connectors/connectors-native-webhook.md), maakt echter slechts één aanroep van de doel service of het systeem, dat gebeurt op het tijdstip van het abonnement en de aanvragen die het doel krijgt de trigger of actie, alleen wanneer er een gebeurtenis optreedt. Op die manier hoeft de trigger of actie de bestemming niet continu te controleren.
  
  Als de doel service of het systeem webhooks ondersteunt of een connector biedt met een webhook-versie, is deze optie beter dan het gebruik van de polling-versie. Als u de triggers en acties van webhooks wilt identificeren, moet u controleren of ze het `ApiConnectionWebhook` type hebben of dat u geen terugkeer patroon opgeeft. Zie [APIConnectionWebhook trigger](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) and [APIConnectionWebhook Action](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-action)(Engelstalig) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Logic apps limieten en configuratie](../logic-apps/logic-apps-limits-and-config.md)
