---
title: Opties voor integratie- en automatiseringsplatforms in Azure
description: 'Vergelijk Microsoft cloud-services die zijn geoptimaliseerd voor integratietaken: Power Automate, Logic Apps, Functions en WebJobs.'
ms.topic: overview
ms.date: 04/09/2018
ms.custom: mvc
ms.openlocfilehash: e6bd97469a6b7b6c42427e130ac103954377c652
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105643363"
---
# <a name="choose-the-right-integration-and-automation-services-in-azure"></a>Kies de juiste integratie- en automatiseringsservices in Azure

In dit artikel worden de volgende Microsoft-cloudservices vergeleken:

* [Microsoft Power Automate](https://flow.microsoft.com/) (vroeger Microsoft Flow)
* [Azure Logic Apps](https://azure.microsoft.com/services/logic-apps/)
* [Azure Functions](https://azure.microsoft.com/services/functions/)
* [Azure App Service WebJobs](../app-service/webjobs-create.md)

Al deze services kunnen integratieproblemen oplossen en bedrijfsprocessen automatiseren. Ze kunnen allemaal input, acties, voorwaarden en output definiëren. U kunt ze allemaal uitvoeren volgens een planning of na een trigger. Elke service biedt unieke voordelen en in dit artikel worden de verschillen uitgelegd. 

Zie [Criteria voor het kiezen van een Azure-rekenservice](/azure/architecture/guide/technology-choices/compute-comparison) en [Een Azure-rekenoptie kiezen voor microservices](/azure/architecture/microservices/design/compute-options) als u een meer algemene vergelijking wilt tussen Azure Functions en Azure-rekenopties.

## <a name="compare-microsoft-power-automate-and-azure-logic-apps"></a>Microsoft Power Automate en Azure Logic Apps vergelijken

Power Automate en Azure Logic Apps zijn beide *designer-first* integratieservices waarmee werkstromen kunnen worden gemaakt. Beide services integreren met verschillende SaaS- en bedrijfstoepassingen. 

Power Automate is gebaseerd op Logic Apps. Ze hebben dezelfde ontwerpfunctie voor werkstromen en dezelfde [connectors](../connectors/apis-list.md). 

Power Automate stelt kantoormedewerkers in staat om eenvoudige integraties tot stand te brengen (bijvoorbeeld een goedkeuringsproces voor een documentbibliotheek in SharePoint) zonder tussenkomst van ontwikkelaars of IT. Logic Apps maakt het ook mogelijk om geavanceerde integraties te realiseren (zoals B2B-processen) waarvoor Azure DevOps-beveiligingsprocessen op ondernemingsniveau zijn vereist. Het is normaal dat een werkstroom voor een bedrijfsproces na verloop van tijd toeneemt in complexiteit. U kunt daarom eerst met een stroom beginnen en deze later omzetten in een logische app als dat nodig is.

Raadpleeg de volgende tabel om te bepalen of Power Automate of Logic Apps het meest geschikt is voor een bepaalde integratie:

|  | Power Automate | Logic Apps |
| --- | --- | --- |
| **Gebruikers** |Kantoorpersoneel, zakelijke gebruikers, SharePoint-beheerders |Professionele integrators en ontwikkelaars, IT-professionals |
| **Scenario's** |Selfservice |Geavanceerde integraties |
| **Ontwerpomgeving** |In browser en mobiele app, alleen UI |In browser en [Visual Studio](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md), [codeweergave](../logic-apps/logic-apps-author-definitions.md) beschikbaar |
| **Application lifecycle management (ALM)** |Ontwerpen en testen in niet-productieomgevingen, niveau verhogen naar productie wanneer u klaar bent |Azure DevOps: broncodebeheer, testen, ondersteuning, automatisering en beheersbaarheid in [Azure Resource Manager](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) |
| **Beheerervaring** |Beleidsregels voor Power Automate-omgevingen en preventie van gegevensverlies beheren, licenties bijhouden: [Beheercentrum](https://admin.flow.microsoft.com) |Resourcegroepen, verbindingen, toegangsbeheer en logboekregistratie beheren: [Azure-portal](https://portal.azure.com) |
| **Beveiliging** |Microsoft 365-logboeken voor beveiligingscontrole, preventie van gegevensverlies, [inactieve versleuteling](https://wikipedia.org/wiki/Data_at_rest#Encryption) voor gevoelige gegevens |Beveiligingscontrole van Azure: [Azure-beveiliging](https://www.microsoft.com/en-us/trustcenter/Security/AzureSecurity), [Azure Security Center](https://azure.microsoft.com/services/security-center/), [auditlogboeken](https://azure.microsoft.com/blog/azure-audit-logs-ux-refresh/) |

## <a name="compare-azure-functions-and-azure-logic-apps"></a>Azure Functions en Azure Logic Apps vergelijken

Functions en Logic Apps zijn Azure-services waarmee serverloze werkbelastingen mogelijk worden gemaakt. Azure Functions is een serverloze rekenservice, terwijl Azure Logic Apps serverloze werkstromen biedt. Met beide kunnen complexe *indelingen* worden gemaakt. Een indeling bestaat uit een verzameling functies of stappen, *acties* genoemd, in Logic Apps, die worden uitgevoerd om een complexe taak te volbrengen. Als u bijvoorbeeld een reeks orders wilt verwerken, kunt u een aantal instanties van een functie parallel uitvoeren, wachten tot alle instanties zijn voltooid en vervolgens een functie uitvoeren die het resultaat voor de combinatie berekent.

Met Azure Functions ontwikkelt u indelingen door code te schrijven en de [extensie Durable Functions](durable/durable-functions-overview.md) te gebruiken. Met Logic Apps maakt u indelingen door gebruik te maken van een GUI of door configuratiebestanden te bewerken.

U kunt services combineren en matchen als u een indeling bouwt, waarbij u functies aanroept vanuit logische apps en logische apps aanroept vanuit functies. U kunt kiezen hoe u elke indeling bouwt op basis van de mogelijkheden van de services of van uw persoonlijke voorkeur. De volgende tabel vermeldt de belangrijkste verschillen tussen:

|  | Durable Functions | Logic Apps |
| --- | --- | --- |
| **Ontwikkeling** | Code-first (imperatief) | Designer-first (declaratief) |
| **Connectiviteit** | [Circa 15 bindingstypen](functions-triggers-bindings.md#supported-bindings); code schrijven voor aangepaste bindingen | [Grote verzameling connectors](../connectors/apis-list.md), [Enterprise Library Integration Pack voor B2B-scenario's](../logic-apps/logic-apps-enterprise-integration-overview.md); [aangepaste bindingen ontwikkelen](../logic-apps/custom-connector-overview.md) |
| **Acties** | Elke activiteit is een Azure-functie; code schrijven voor de activiteitsfuncties |[Grote verzameling kant-en-klare acties](../logic-apps/logic-apps-workflow-actions-triggers.md)|
| **Controle** | [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [Azure Monitor-Logboeken](../logic-apps/monitor-logic-apps.md)|
| **Beheer** | [REST API](durable/durable-functions-http-api.md), [Visual Studio](/visualstudio/azure/vs-azure-tools-resources-managing-with-cloud-explorer) | [Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md), [REST API](/rest/api/logic/), [PowerShell](/powershell/module/az.logicapp), [Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md) |
| **Context voor uitvoering** | Kan [lokaal](functions-runtime-overview.md) of in de cloud worden uitgevoerd | Wordt alleen in de cloud uitgevoerd|

<a name="function"></a>

## <a name="compare-functions-and-webjobs"></a>Functions en WebJobs vergelijken

Azure App Service WebJobs met de WebJobs SDK is net als Azure Functions een integratieservice *waarbij code op de eerste plaats komt* en die is ontworpen voor ontwikkelaars. Beide zijn gebaseerd op [Azure App Service](../app-service/overview.md) en ondersteunen functies zoals [integratie van broncodebeheer](../app-service/deploy-continuous-deployment.md), [verificatie](../app-service/overview-authentication-authorization.md) en [bewaking met Application Insights-integratie](functions-monitoring.md).

### <a name="webjobs-and-the-webjobs-sdk"></a>WebJobs en de WebJobs-SDK

U kunt de *WebJobs*-functie van App Service gebruiken om een script of code uit te voeren in de context van een App Service-web-app. De *WebJobs SDK* is een framework dat is ontworpen voor WebJobs dat de code vereenvoudigt die u schrijft om te reageren op gebeurtenissen in Azure-services. U kunt bijvoorbeeld reageren op een afbeeldingsblob die in Azure Storage is gemaakt door een miniatuurafbeelding te maken. De WebJobs SDK wordt uitgevoerd als een .NET-consoletoepassing die u op een WebJob kunt implementeren. 

WebJobs en de WebJobs SDK werken samen het beste, maar u kunt WebJobs ook zonder de WebJobs SDK gebruiken en vice versa. Een WebJob kan elk programma of script uitvoeren dat in de App Service-sandbox wordt uitgevoerd. Een WebJobs SDK-consoletoepassing kan overal worden uitgevoerd waar consoletoepassingen worden uitgevoerd, zoals op on-premises servers.

### <a name="comparison-table"></a>Vergelijkingstabel

Azure Functions is gebaseerd op de WebJobs SDK en deelt daarom veel triggers en verbindingen met andere Azure-services. Hier volgt een aantal factoren om te overwegen als u kiest tussen Azure Functions en WebJobs met de WebJobs SDK:

|  | Functions | WebJobs met WebJobs SDK |
| --- | --- | --- |
|**[App-model zonder server](https://azure.microsoft.com/solutions/serverless/) met [automatisch schalen](event-driven-scaling.md)**|✔||
|**[Ontwikkelen en testen in de browser](./functions-get-started.md)** |✔||
|**[Betalen per gebruik](consumption-plan.md)**|✔||
|**[Integratie met Logic Apps](functions-twitter-email.md)**|✔||
| **Trigger-gebeurtenissen** |[Timer](functions-bindings-timer.md)<br>[Azure Storage-wachtrijen en -blobs](functions-bindings-storage-blob.md)<br>[Azure Service Bus-wachtrijen en -onderwerpen](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[HTTP/WebHook (GitHub, Slack)](functions-bindings-http-webhook.md)<br>[Azure Event Grid](functions-bindings-event-grid.md)|[Timer](functions-bindings-timer.md)<br>[Azure Storage-wachtrijen en -blobs](functions-bindings-storage-blob.md)<br>[Azure Service Bus-wachtrijen en -onderwerpen](functions-bindings-service-bus.md)<br>[Azure Cosmos DB](functions-bindings-cosmosdb.md)<br>[Azure Event Hubs](functions-bindings-event-hubs.md)<br>[Bestandssysteem](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Files/FileTriggerAttribute.cs)|
| **Ondersteunde talen**  |C#<br>F#<br>Javascript<br>Java<br>Python<br>PowerShell |C#<sup>1</sup>|
|**Pakketbeheer**|NPM en NuGet|NuGet<sup>2</sup>|

<sup>1</sup> WebJobs (zonder de WebJobs SDK) biedt ondersteuning voor C#, Java, JavaScript, Bash, .cmd, .bat, PowerShell, PHP, TypeScript, Python en meer. Dit is geen allesomvattende lijst. Een WebJob kan elk programma of script uitvoeren dat in de App Service-sandbox kan worden uitgevoerd.

<sup>2</sup> WebJobs (zonder de WebJobs SDK) ondersteunt NPM en NuGet.

### <a name="summary"></a>Samenvatting

Azure Functions biedt ontwikkelaars meer productiviteit dan Azure App Service WebJobs. Het biedt tevens meer opties voor programmeertalen, ontwikkelomgevingen, Azure-service-integratie en prijzen. Deze oplossing is voor de meeste scenario's de beste keuze.

Hier volgen twee scenario's waarvoor WebJobs de beste keuze kan zijn:

* U hebt meer controle nodig over de code die naar gebeurtenissen luistert, het `JobHost`-object. Functions biedt een beperkt aantal manieren om `JobHost`-gedrag aan te passen in het [host.json](functions-host-json.md)-bestand. Soms moet u acties uitvoeren die niet met een tekenreeks in een JSON-bestand kunnen worden opgegeven. U kunt bijvoorbeeld alleen met de WebJobs SDK een aangepast beleid voor opnieuw proberen voor Azure Storage configureren.
* U hebt een App Service-app waarvoor u codefragmenten wilt uitvoeren en u wilt deze gezamenlijk beheren in dezelfde Azure DevOps-omgeving.

Voor andere scenario's waarin u codefragmenten wilt uitvoeren voor de integratie van Azure-services of services van derden, kiest u Azure Functions in plaats van WebJobs en de WebJobs SDK.

<a name="together"></a>

## <a name="power-automate-logic-apps-functions-and-webjobs-together"></a>Power Automate, Logic Apps, Functions en WebJobs samen

U hoeft niet slechts een van deze services te kiezen. Ze integreren net zo goed met elkaar als met externe services.

Een stroom kan een logische app aanroepen. Een logische app kan een functie aanroepen en een functie kan een logische app aanroepen. Zie bijvoorbeeld [Een functie maken die kan worden geïntegreerd met Azure Logic Apps](functions-twitter-email.md).

De integratie tussen Power Automate, Logic Apps en Functions wordt alleen maar beter. U kunt iets bouwen in de ene service en gebruiken in de andere services.

Gebruik deze koppelingen voor meer informatie over integratieservices:

* [Leveraging Azure Functions & Azure App Service for integration scenarios door Christopher Anderson](https://www.biztalk360.com/integrate-2016-resources/leveraging-azure-functions-azure-app-service-integration-scenarios/)
* [Integrations Made Simple door Charles Lamanna](https://www.biztalk360.com/integrate-2016-resources/integrations-made-simple/)
* [Logic Apps Live webcast](https://aka.ms/logicappslive) (Live webcast van Logic Apps)
* [Veelgestelde vragen over Power Automate-rapporten](/power-automate/frequently-asked-questions)

## <a name="next-steps"></a>Volgende stappen

Begin met het maken van uw eerste stroom, logische app of functie-app. Selecteer een van de volgende koppelingen:

* [Aan de slag met Power Automate](/power-automate/getting-started)
* [Een logische app maken](../logic-apps/quickstart-create-first-logic-app-workflow.md)
* [Uw eerste Azure-functie maken](./functions-get-started.md)