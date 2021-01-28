---
title: Aanbevolen procedures voor Azure Functions
description: Lees de aanbevolen procedures en patronen voor Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f05afb3c23fc720bb0100a751a6943d7bb03453f
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954780"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>De prestaties en betrouwbaarheid van Azure Functions verbeteren

Dit artikel bevat richt lijnen voor het verbeteren van de prestaties en betrouw baarheid van uw [serverloze](https://azure.microsoft.com/solutions/serverless/) functie-apps.  

## <a name="general-best-practices"></a>Algemene best practices

Hieronder vindt u de aanbevolen procedures voor het bouwen en ontwerpen van uw serverloze oplossingen met behulp van Azure Functions.

### <a name="avoid-long-running-functions"></a>Langdurige functies voor komen

Grote, langlopende functies kunnen onverwachte time-outproblemen veroorzaken. Zie de time-outperiode van de [functie-app](functions-scale.md#timeout)voor meer informatie over de time-outs voor een bepaald hosting plan. 

Een functie kan groot worden vanwege veel Node.js afhankelijkheden. Het importeren van afhankelijkheden kan ook leiden tot grotere laad tijden die leiden tot onverwachte time-outs. Afhankelijkheden worden zowel expliciet als impliciet geladen. Eén module die door uw code is geladen, kan zijn eigen extra modules laden. 

Als dat mogelijk is, kunnen er in kleinere functie sets grote functies worden gebruikt die samen werken en snel antwoorden retour neren. Zo kan een webhook of HTTP-activerings functie een bevestigings antwoord vereisen binnen een bepaalde tijds limiet; het is gebruikelijk dat webhooks een onmiddellijke reactie vereisen. U kunt de nettolading van de HTTP-trigger door geven aan een wachtrij die moet worden verwerkt door een functie voor wachtrij activering. Met deze benadering kunt u de werkelijke hoeveelheid werk uitstellen en een onmiddellijke reactie retour neren.


### <a name="cross-function-communication"></a>Communicatie tussen meerdere functies

[Durable functions](durable/durable-functions-overview.md) en [Azure Logic apps](../logic-apps/logic-apps-overview.md) zijn gebouwd om status overgangen en communicatie tussen meerdere functies te beheren.

Als u Durable Functions of Logic Apps niet gebruikt om te integreren met meerdere functies, is het het beste om opslag wachtrijen te gebruiken voor communicatie tussen functies. De belangrijkste reden is dat opslag wachtrijen goed koper zijn en veel eenvoudiger zijn in te richten dan andere opslag opties. 

Afzonderlijke berichten in een opslag wachtrij zijn beperkt tot 64 KB. Als u grotere berichten tussen functies wilt door geven, kan een Azure Service Bus wachtrij worden gebruikt ter ondersteuning van bericht grootten van Maxi maal 256 KB in de laag standaard en Maxi maal 1 MB in de Premium-laag.

Service Bus onderwerpen zijn nuttig als u berichten filtering nodig hebt vóór de verwerking.

Event hubs zijn handig voor het ondersteunen van de communicatie van grote volumes.


### <a name="write-functions-to-be-stateless"></a>Schrijf functies die stateless zijn 

Functies moeten stateless en idempotent, indien mogelijk, zijn. Koppel alle eventueel vereiste statusgegevens aan uw gegevens. Een order die wordt verwerkt, zou waarschijnlijk een gekoppeld lid hebben `state` . Een functie kan een volg orde op basis van die status verwerken terwijl de functie zelf staat. 

Idempotent-functies worden met name aanbevolen met timer-triggers. Als u bijvoorbeeld iets hebt dat absoluut eenmaal per dag moet worden uitgevoerd, schrijft u dit zodat het op elk gewenst moment kan worden uitgevoerd op dezelfde dag met dezelfde resultaten. De functie kan worden afgesloten als er geen werk voor een bepaalde dag is. Ook als een vorige uitvoering niet kon worden voltooid, moet de volgende uitvoering worden opgehaald waar deze is gebleven.


### <a name="write-defensive-functions"></a>Verdedigings functies schrijven

Stel dat uw functie op elk moment een uitzonde ring kan ondervinden. Ontwerp uw functies met de mogelijkheid om vanaf een eerder uitgaand fout punt te blijven tijdens de volgende uitvoering. Overweeg een scenario dat de volgende acties vereist:

1. Query voor 10.000 rijen in een Data Base.
2. Maak een wachtrij bericht voor elk van deze rijen om de regel verder omlaag te verwerken.
 
Afhankelijk van hoe complex uw systeem is, hebt u mogelijk de volgende mogelijkheden: de betrokken downstream-services functioneren niet goed, netwerk storingen of quotum limieten, enzovoort. Al deze kunnen van invloed zijn op uw functie op elk gewenst moment. U moet uw functies ontwerpen om hiervoor voor te bereiden.

Hoe reageert uw code als er een fout optreedt nadat u 5.000 van deze items in een wachtrij voor verwerking hebt ingevoegd? Items bijhouden in een set die u hebt voltooid. Als dat niet het geval is, kunt u ze de volgende keer opnieuw invoegen. Deze dubbele invoeging kan een ernstige invloed hebben op uw werk stroom, dus [Zorg ervoor dat uw functies idempotent](functions-idempotent.md)zijn. 

Als een wachtrij-item al is verwerkt, mag de functie niet worden uitgevoerd.

Profiteer van de beschik bare verdedigings maatregelen voor onderdelen die u in het Azure Functions platform gebruikt. Zie bijvoorbeeld het **verwerken van verontreinigde wachtrij berichten** in de documentatie voor [Azure Storage wachtrij Triggers en bindingen](functions-bindings-storage-queue-trigger.md#poison-messages). 

## <a name="scalability-best-practices"></a>Best practices voor schaal baarheid

Er zijn een aantal factoren die van invloed zijn op de schaal van exemplaren van uw functie-app. De details vindt u in de documentatie over [functie schalen](functions-scale.md).  Hier volgen enkele aanbevolen procedures voor een optimale schaal baarheid van een functie-app.

### <a name="share-and-manage-connections"></a>Verbindingen delen en beheren

Gebruik waar mogelijk verbindingen met externe resources. Zie [verbindingen beheren in azure functions](./manage-connections.md).

### <a name="avoid-sharing-storage-accounts"></a>Vermijd het delen van opslag accounts

Wanneer u een functie-app maakt, moet u deze koppelen aan een opslag account. De verbinding van het opslag account wordt onderhouden in de [toepassings instelling AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Test-en productie code niet combi neren in dezelfde functie-app

Functies binnen een functie-app delen resources. Zo wordt geheugen gedeeld. Als u een functie-app in productie gebruikt, voegt u geen aan de test gerelateerde functies en resources toe. Dit kan leiden tot onverwachte overhead tijdens de uitvoering van productie code.

Wees voorzichtig met wat u laadt in uw productie functie-apps. Het geheugen wordt gemiddeld verdeeld over elke functie in de app.

Als u een gedeelde assembly hebt waarnaar in meerdere .NET-functies wordt verwezen, plaatst u deze in een gemeen schappelijke gedeelde map. Anders zou u per ongeluk meerdere versies van dezelfde binaire elementen implementeren die zich op verschillende functies gedragen.

Gebruik geen uitgebreide logboek registratie in productie code, die een negatieve invloed heeft op de prestaties.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Gebruik async code, maar voorkom het blok keren van aanroepen

Asynchrone programmering is een aanbevolen best practice, met name bij het blok keren van I/O-bewerkingen.

In C# vermijdt u altijd het verwijzen naar de `Result` eigenschap of aanroep `Wait` methode voor een `Task` exemplaar. Deze benadering kan leiden tot uitputting van de thread.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>Meerdere werk processen gebruiken

Elk exemplaar van een host voor functions maakt standaard gebruik van één werk proces. Om de prestaties te verbeteren, met name met single-threaded Runtimes zoals python, gebruikt u de [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) om het aantal werk processen per host (Maxi maal 10) te verhogen. Azure Functions probeert vervolgens gelijktijdige functie aanroepen voor deze werk nemers gelijkmatig te verdelen. 

De FUNCTIONS_WORKER_PROCESS_COUNT is van toepassing op elke host die functies maakt wanneer uw toepassing wordt geschaald om aan de vraag te voldoen. 

### <a name="receive-messages-in-batch-whenever-possible"></a>Indien mogelijk berichten in batch ontvangen

Sommige triggers, zoals Event hub, kunnen een batch berichten ontvangen met één aanroep.  Batch berichten hebben veel betere prestaties.  U kunt de maximale Batch grootte in het `host.json` bestand configureren, zoals wordt beschreven in de [host.jsop referentie documentatie](functions-host-json.md)

Voor C#-functies kunt u het type wijzigen in een sterk getypeerde matrix.  In plaats van `EventData sensorEvent` de hand tekening van de methode kan bijvoorbeeld niet worden gebruikt `EventData[] sensorEvent` .  Voor andere talen moet u de eigenschap kardinaliteit expliciet instellen in uw `function.json` to om `many` batch verwerking in te scha kelen [, zoals hier wordt weer gegeven](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Gedrag van hosts configureren voor betere verwerking van gelijktijdigheid

Met het `host.json` bestand in de functie-app kunt u de runtime van de host en trigger gedrag configureren.  Naast het uitvoeren van batch verwerking, kunt u gelijktijdigheid voor een aantal triggers beheren. Het aanpassen van de waarden in deze opties kan er vaak toe leiden dat elke instantie op de juiste wijze wordt geschaald voor de vereisten van de aangeroepen functies.

De instellingen in de host.jsvoor het bestand zijn van toepassing op alle functies in de app, binnen *één exemplaar* van de functie. Als u bijvoorbeeld een functie-app met twee HTTP-functies en- [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) aanvragen hebt ingesteld op 25, telt een aanvraag voor een HTTP-trigger naar de gedeelde 25 gelijktijdige aanvragen.  Wanneer deze functie-app wordt geschaald naar 10 instanties, staan de tien functies het effectief toestaan van 250 gelijktijdige aanvragen (10 exemplaren * 25 gelijktijdige aanvragen per instantie). 

Andere configuratie opties voor de host vindt u in het [ artikelhost.jsop configuratie](functions-host-json.md).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende resources voor meer informatie:

* [Verbindingen beheren in Azure Functions](manage-connections.md)
* [Aanbevolen procedures Azure App Service](../app-service/app-service-best-practices.md)
