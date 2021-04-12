---
title: Aanbevolen procedures voor Azure Functions
description: Lees de aanbevolen procedures en patronen voor Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5783f8092a6435b43ab8720df18cc5200e390d46
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100378244"
---
# <a name="best-practices-for-performance-and-reliability-of-azure-functions"></a>Aanbevolen procedures voor de prestaties en betrouw baarheid van Azure Functions

Dit artikel bevat richt lijnen voor het verbeteren van de prestaties en betrouw baarheid van uw [serverloze](https://azure.microsoft.com/solutions/serverless/) functie-apps.  

Hieronder vindt u de aanbevolen procedures voor het bouwen en ontwerpen van uw serverloze oplossingen met behulp van Azure Functions.

## <a name="avoid-long-running-functions"></a>Langdurige functies voor komen

Grote, langlopende functies kunnen onverwachte time-outproblemen veroorzaken. Zie de time-outperiode van de [functie-app](functions-scale.md#timeout)voor meer informatie over de time-outs voor een bepaald hosting plan.

Een functie kan groot worden vanwege veel Node.js afhankelijkheden. Het importeren van afhankelijkheden kan ook leiden tot grotere laad tijden die leiden tot onverwachte time-outs. Afhankelijkheden worden zowel expliciet als impliciet geladen. Eén module die door uw code is geladen, kan zijn eigen extra modules laden.

Als dat mogelijk is, kunnen er in kleinere functie sets grote functies worden gebruikt die samen werken en snel antwoorden retour neren. Zo kan een webhook of HTTP-activerings functie een bevestigings antwoord vereisen binnen een bepaalde tijds limiet; het is gebruikelijk dat webhooks een onmiddellijke reactie vereisen. U kunt de nettolading van de HTTP-trigger door geven aan een wachtrij die moet worden verwerkt door een functie voor wachtrij activering. Met deze benadering kunt u de werkelijke hoeveelheid werk uitstellen en een onmiddellijke reactie retour neren.

## <a name="cross-function-communication"></a>Communicatie tussen meerdere functies

[Durable functions](durable/durable-functions-overview.md) en [Azure Logic apps](../logic-apps/logic-apps-overview.md) zijn gebouwd om status overgangen en communicatie tussen meerdere functies te beheren.

Als u Durable Functions of Logic Apps niet gebruikt om te integreren met meerdere functies, is het het beste om opslag wachtrijen te gebruiken voor communicatie tussen functies. De belangrijkste reden is dat opslag wachtrijen goed koper zijn en veel eenvoudiger zijn in te richten dan andere opslag opties.

Afzonderlijke berichten in een opslag wachtrij zijn beperkt tot 64 KB. Als u grotere berichten tussen functies wilt door geven, kan een Azure Service Bus wachtrij worden gebruikt ter ondersteuning van bericht grootten van Maxi maal 256 KB in de laag standaard en Maxi maal 1 MB in de Premium-laag.

Service Bus onderwerpen zijn nuttig als u berichten filtering nodig hebt vóór de verwerking.

Event hubs zijn handig voor het ondersteunen van de communicatie van grote volumes.

## <a name="write-functions-to-be-stateless"></a>Schrijf functies die stateless zijn

Functies moeten stateless en idempotent, indien mogelijk, zijn. Koppel alle eventueel vereiste statusgegevens aan uw gegevens. Een order die wordt verwerkt, zou waarschijnlijk een gekoppeld lid hebben `state` . Een functie kan een volg orde op basis van die status verwerken terwijl de functie zelf staat.

Idempotent-functies worden met name aanbevolen met timer-triggers. Als u bijvoorbeeld iets hebt dat absoluut eenmaal per dag moet worden uitgevoerd, schrijft u dit zodat het op elk gewenst moment kan worden uitgevoerd op dezelfde dag met dezelfde resultaten. De functie kan worden afgesloten als er geen werk voor een bepaalde dag is. Ook als een vorige uitvoering niet kon worden voltooid, moet de volgende uitvoering worden opgehaald waar deze is gebleven.

## <a name="write-defensive-functions"></a>Verdedigings functies schrijven

Stel dat uw functie op elk moment een uitzonde ring kan ondervinden. Ontwerp uw functies met de mogelijkheid om vanaf een eerder uitgaand fout punt te blijven tijdens de volgende uitvoering. Overweeg een scenario dat de volgende acties vereist:

1. Query voor 10.000 rijen in een Data Base.
2. Maak een wachtrij bericht voor elk van deze rijen om de regel verder omlaag te verwerken.

Afhankelijk van hoe complex uw systeem is, hebt u mogelijk de volgende mogelijkheden: de betrokken downstream-services functioneren niet goed, netwerk storingen of quotum limieten, enzovoort. Al deze kunnen van invloed zijn op uw functie op elk gewenst moment. U moet uw functies ontwerpen om hiervoor voor te bereiden.

Hoe reageert uw code als er een fout optreedt nadat u 5.000 van deze items in een wachtrij voor verwerking hebt ingevoegd? Items bijhouden in een set die u hebt voltooid. Als dat niet het geval is, kunt u ze de volgende keer opnieuw invoegen. Deze dubbele invoeging kan een ernstige invloed hebben op uw werk stroom, dus [Zorg ervoor dat uw functies idempotent](functions-idempotent.md)zijn. 

Als een wachtrij-item al is verwerkt, mag de functie niet worden uitgevoerd.

Profiteer van de beschik bare verdedigings maatregelen voor onderdelen die u in het Azure Functions platform gebruikt. Zie bijvoorbeeld het **verwerken van verontreinigde wachtrij berichten** in de documentatie voor [Azure Storage wachtrij Triggers en bindingen](functions-bindings-storage-queue-trigger.md#poison-messages).

## <a name="function-organization-best-practices"></a>Best practices voor functie organisatie

Als onderdeel van uw oplossing kunt u meerdere functies ontwikkelen en publiceren. Deze functies worden vaak gecombineerd tot één functie-app, maar ze kunnen ook worden uitgevoerd in afzonderlijke functie-apps. In Premium-en toegewezen (App Service) hosting abonnementen kunnen meerdere functie-apps ook dezelfde resources delen door in hetzelfde abonnement te worden uitgevoerd. Hoe u de functies en functie-apps groepeert, kunnen invloed hebben op de prestaties, schaal baarheid, configuratie, implementatie en beveiliging van uw algemene oplossing. Er zijn geen regels die van toepassing zijn op elk scenario. Houd daarom rekening met de informatie in deze sectie bij het plannen en ontwikkelen van uw functies.

### <a name="organize-functions-for-performance-and-scaling"></a>Functies voor prestaties en schalen ordenen

Elke functie die u maakt, heeft een geheugen footprint. Hoewel deze footprint meestal klein is, kan het starten van de app op nieuwe instanties worden vertraagd door te veel functies in een functie-app. Dit betekent ook dat het totale geheugen gebruik van uw functie-app mogelijk hoger is. Het is moeilijk om te zeggen hoeveel functies er in één app moeten zijn, wat afhankelijk is van uw specifieke werk belasting. Als uw functie echter veel gegevens in het geheugen opslaat, kunt u overwegen minder functies in één app te gebruiken.

Als u meerdere functie-apps uitvoert in één Premium-abonnement of een toegewezen (App Service)-abonnement, worden deze apps allemaal tegelijk geschaald. Als u een functie-app hebt die een veel hoger geheugen vereiste heeft dan de andere, wordt er een onevenredige hoeveelheid geheugen bronnen gebruikt voor elk exemplaar waarop de app is geïmplementeerd. Omdat hierdoor minder geheugen beschikbaar zou kunnen zijn voor de andere apps op elk exemplaar, kunt u een functie-app met veel geheugen gebruiken zoals deze in een eigen afzonderlijk hosting abonnement.

> [!NOTE]
> Wanneer u het [verbruiks abonnement](./functions-scale.md)gebruikt, raden we u aan elke app altijd in een eigen abonnement te plaatsen, omdat apps toch onafhankelijk van elkaar worden geschaald.

Overweeg of u functies met verschillende laad profielen wilt groeperen. Als u bijvoorbeeld een functie hebt die veel duizenden wachtrij berichten verwerkt en een andere die slechts af en toe maar hoge geheugen vereisten heeft, wilt u deze mogelijk implementeren in afzonderlijke functie-apps, zodat ze hun eigen bronnen sets krijgen en ze onafhankelijk van elkaar kunnen worden geschaald.

### <a name="organize-functions-for-configuration-and-deployment"></a>Functies ordenen voor configuratie en implementatie

Functie-apps hebben een `host.json` bestand, dat wordt gebruikt voor het configureren van Geavanceerd gedrag van functie Triggers en de Azure functions runtime. Wijzigingen in het `host.json` bestand zijn van toepassing op alle functies in de app. Als u bepaalde functies hebt die aangepaste configuraties nodig hebben, kunt u overwegen deze te verplaatsen naar hun eigen functie-app.

Alle functies in uw lokale project worden samen met een set bestanden in uw functie-app in azure geïmplementeerd. Mogelijk moet u afzonderlijke functies afzonderlijk implementeren of functies gebruiken, zoals [implementatie sleuven](./functions-deployment-slots.md) voor sommige functies en andere niet. In dergelijke gevallen moet u deze functies (in afzonderlijke code projecten) implementeren in verschillende functie-apps.

### <a name="organize-functions-by-privilege"></a>Functies ordenen op bevoegdheid

Verbindings reeksen en andere referenties die zijn opgeslagen in toepassings instellingen, bieden alle functies in de functie-app dezelfde set machtigingen in de bijbehorende resource. Overweeg het aantal functies met toegang tot specifieke referenties te minimaliseren door functies te verplaatsen die deze referenties niet naar een afzonderlijke functie-app gebruiken. U kunt altijd gebruikmaken van technieken als [functie](/learn/modules/chain-azure-functions-data-using-bindings/) koppeling voor het door geven van gegevens tussen functies in verschillende functie-apps.  

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
