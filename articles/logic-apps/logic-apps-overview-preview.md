---
title: Overzicht voor Azure Logic Apps-preview
description: Azure Logic Apps Preview is een cloudoplossing voor het bouwen van geautomatiseerde, enkelvoudige, stateful en stateless werkstromen die apps, gegevens, services en systemen integreren met minimale code voor scenario's op ondernemingsniveau.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/24/2021
ms.openlocfilehash: 27889e8309c0efaf1e2869fc39d099f38f64f7c4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764815"
---
# <a name="overview-azure-logic-apps-preview"></a>Overzicht: Azure Logic Apps preview

> [!IMPORTANT]
> Deze functie is in openbare preview en wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met Azure Logic Apps Preview kunt u automatiserings- en integratieoplossingen bouwen voor apps, gegevens, cloudservices en systemen door logische apps met één tenant te maken en uit te werken met het nieuwe resourcetype Logische app **(preview).** Met behulp van dit type logische app met één tenant kunt u meerdere [ *stateful*](#stateful-stateless) en staatloze werkstromen bouwen die powered by de opnieuw ontworpen Azure Logic Apps Preview-runtime. Dit biedt draagbaarheid, betere prestaties en flexibiliteit voor het implementeren en uitvoeren in verschillende hostingomgevingen, waaronder niet alleen Azure, maar ook Docker-containers.

Hoe is dit mogelijk? De opnieuw ontworpen runtime maakt gebruik [van Azure Functions](../azure-functions/functions-bindings-register.md) model voor uitbreiding en wordt gehost als een extensie op Azure Functions runtime. Deze architectuur betekent dat u het type logische app met één tenant overal kunt uitvoeren Azure Functions wordt uitgevoerd. U kunt de opnieuw ontworpen runtime hosten op vrijwel elke netwerktopologie en een beschikbare rekenkracht kiezen voor het afhandelen van de benodigde werkbelasting die vereist is voor uw werkstromen. Zie Introduction to Azure Functions and Azure Functions triggers and bindings [(Inleiding](../azure-functions/functions-overview.md) tot triggers Azure Functions en [Azure Functions bindingen) voor meer informatie.](../azure-functions/functions-triggers-bindings.md)

U kunt de resource van de logische **app (preview)** maken door te beginnen in de [Azure Portal of](create-stateful-stateless-workflows-azure-portal.md) door een project te maken in Visual Studio Code met de extensie [Azure Logic Apps (preview).](create-stateful-stateless-workflows-visual-studio-code.md) Bovendien kunt u in Visual Studio Code uw werkstromen bouwen en *lokaal* uitvoeren in uw ontwikkelomgeving. Of u nu de portal of Visual Studio Code gebruikt, u kunt het type logische app met één tenant implementeren en uitvoeren in dezelfde soorten hostingomgevingen.

Dit overzicht heeft betrekking op de volgende gebieden:

* [Verschillen tussen Azure Logic Apps Preview, de Azure Logic Apps omgeving met meerdere tenants en de integratieserviceomgeving](#preview-differences).

* [Verschillen tussen stateful en staatloze werkstromen,](#stateful-stateless)waaronder gedragsverschillen tussen [geneste stateful en staatloze werkstromen.](#nested-behavior)

* [Mogelijkheden in deze openbare preview.](#public-preview-contents)

* [Hoe het prijsmodel werkt.](#pricing-model)

* [Gewijzigde, beperkte, niet-beschikbare of niet-ondersteunde mogelijkheden](#limited-unavailable-unsupported).

* [Limieten in Azure Logic Apps Preview.](#limits)

Bekijk de volgende andere onderwerpen voor meer informatie:

* [Azure Logic Apps Running Anywhere - Runtime Deep Dive](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [Logic Apps public preview Known Issues (GitHub) (Bekende problemen met openbare preview (GitHub)](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Wat is de Azure Logic Apps Preview?

De Azure Logic Apps Preview-runtime maakt gebruik [Azure Functions](../azure-functions/functions-overview.md) uitbreidingsmogelijkheden en wordt gehost als een extensie op Azure Functions runtime. Deze architectuur betekent dat u het type logische app met één tenant overal kunt uitvoeren Azure Functions wordt uitgevoerd. U kunt de Azure Logic Apps Preview-runtime hosten op vrijwel elke netwerktopologie die u wilt en een beschikbare rekenkracht kiezen om de benodigde werkbelasting te verwerken die uw werkstroom nodig heeft. Zie [WebJobs SDK: Creating custom input and output bindings (WebJobs SDK: aangepaste](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)invoer- en uitvoerbindingen maken) voor meer informatie over Azure Functions-extensibility.

Met deze nieuwe benadering maken de Azure Logic Apps Preview-runtime en uw werkstromen beide deel uit van uw app die u samen kunt verpakken. Met deze mogelijkheid kunt u uw werkstromen implementeren en uitvoeren door artefacten naar de hostingomgeving te kopiëren en uw app te starten. Deze aanpak biedt ook een meer gestandaardiseerde ervaring voor het bouwen van implementatiepijplijnen rond de werkstroomprojecten voor het uitvoeren van de vereiste tests en validaties voordat u wijzigingen in productieomgevingen implementeert. Zie Running [Anywhere Azure Logic Apps Runtime Deep Dive voor meer informatie.](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

De volgende tabel bevat een korte samenvatting van de verschillen in de manier waarop werkstromen resources delen, op basis van de omgeving waarin ze worden uitgevoerd. Zie Limieten in Azure Logic Apps [Preview voor verschillen in limieten.](#limits)

| Omgeving | Delen en verbruik van resources |
|-------------|----------------------------------|
| Azure Logic Apps (meerdere tenants) | Werkstromen *van klanten in meerdere tenants* delen dezelfde verwerking (compute), opslag, netwerk, en meer. |
| Azure Logic Apps (preview, één tenant) | Werkstromen *in dezelfde logische app en één tenant* delen dezelfde verwerking (compute), opslag, netwerk, en meer. |
| Integratieserviceomgeving (niet beschikbaar in preview) | Werkstromen in *dezelfde omgeving delen* dezelfde verwerking (compute), opslag, netwerk, en meer. |
|||

Ondertussen kunt u nog steeds het type logische app met meerdere tenants maken in de Azure Portal en in Visual Studio Code met behulp van de multiten tenant Azure Logic Apps-extensie. Hoewel de ontwikkelingservaringen verschillen tussen de typen logische apps met meerdere tenants en één tenant, kan uw Azure-abonnement beide typen bevatten. U kunt alle geïmplementeerde logische apps in uw Azure-abonnement bekijken en openen, maar de apps zijn ingedeeld in hun eigen categorieën en secties.

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>Stateful en stateless werkstromen

Met het type logische app met één tenant kunt u deze werkstroomtypen binnen dezelfde logische app maken:

* *Stateful*

  Stateful werkstromen maken wanneer u gegevens uit eerdere gebeurtenissen wilt bewaren, controleren of er naar wilt verwijzen. Met deze werkstromen worden de invoer en uitvoer voor elke actie en hun staat opgeslagen in de externe opslag, waardoor het controleren van de details en geschiedenis van de run mogelijk is nadat elke run is uitgevoerd. Stateful werkstromen bieden hoge tolerantie als er uitval is. Nadat services en systemen zijn hersteld, kunt u onderbroken uitvoeringen reconstrueren vanuit de opgeslagen status en de werkstromen opnieuw tot voltooiing voltooien. Stateful werkstromen kunnen maximaal een jaar actief blijven.

* *Stateless*

  Maak staatloze werkstromen wanneer u geen gegevens van eerdere gebeurtenissen in externe opslag hoeft op te slaan, te controleren of hier naar te verwijzen voor latere beoordeling. Met deze werkstromen worden de invoer en uitvoer voor elke actie en hun staat alleen in het geheugen opgeslagen, *in* plaats van deze gegevens over te dragen naar externe opslag. Als gevolg hiervan hebben staatloze werkstromen kortere uitvoeringen die doorgaans niet langer zijn dan vijf minuten, snellere prestaties met snellere reactietijden, hogere doorvoer en lagere uitvoeringskosten omdat de details en geschiedenis van de uitvoering niet in externe opslag worden bewaard. Als er echter uitval is, worden onderbroken runs niet automatisch hersteld, zodat de aanroeper onderbroken runs handmatig opnieuw moet doen. Deze werkstromen kunnen alleen synchroon worden uitgevoerd.

  Voor eenvoudigere debugging kunt u de uitvoeringsgeschiedenis inschakelen voor een staatloze werkstroom, die enige invloed heeft op de prestaties, en vervolgens de uitvoeringsgeschiedenis uitschakelen wanneer u klaar bent. Zie [Stateful](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless) en staatloze werkstromen maken in Visual Studio Code of Stateful en [stateless werkstromen](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless)maken in de Azure Portal.

  > [!NOTE]
  > Staatloze werkstromen ondersteunen momenteel alleen *acties* voor [beheerde connectors](../connectors/managed.md), die zijn geïmplementeerd in Azure, en niet voor triggers. Als u de werkstroom wilt starten, selecteert u de [ingebouwde Event Hubs, of Service Bus trigger](../connectors/built-in.md). Deze triggers worden standaard uitgevoerd in de Azure Logic Apps Preview-runtime. Zie Gewijzigde, beperkte, niet-beschikbare of niet-ondersteunde mogelijkheden voor meer informatie over beperkte, niet-beschikbare of niet-ondersteunde triggers, [acties en connectors.](#limited-unavailable-unsupported)

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>Geneste gedragsverschillen tussen stateful en staatloze werkstromen

U [](../logic-apps/logic-apps-http-endpoint.md) kunt een werkstroom aanroepbaar maken vanuit andere werkstromen die bestaan in dezelfde **Logic App-resource (preview)** met behulp van de trigger [Aanvraag,](../connectors/connectors-native-reqres.md) [HTTP Webhook-trigger](../connectors/connectors-native-webhook.md)of beheerde connectortriggers die het [type ApiConnectionWebhook](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) hebben en HTTPS-aanvragen kunnen ontvangen.

Dit zijn de gedragspatronen die geneste werkstromen kunnen volgen nadat een bovenliggende werkstroom een onderliggende werkstroom aanroept:

* Asynchrone polling-patroon

  Het bovenliggende antwoord wacht niet op een reactie op de initiële aanroep, maar controleert voortdurend de geschiedenis van de onderliggende run totdat de onderliggende wordt uitgevoerd. Stateful werkstromen volgen standaard dit patroon, wat ideaal is voor langlopende onderliggende werkstromen die de [time-outlimieten voor aanvragen kunnen overschrijden.](../logic-apps/logic-apps-limits-and-config.md)

* Synchroon patroon ('fire and forget')

  Het onderliggende antwoord bevestigt de aanroep door onmiddellijk een antwoord te retourneren en de bovenliggende actie gaat door met de volgende actie zonder te wachten op `202 ACCEPTED` de resultaten van het onderliggende antwoord. In plaats daarvan ontvangt de bovenliggende gebruiker de resultaten wanneer de onderliggende is uitgevoerd. Onderliggende stateful werkstromen die geen antwoordactie bevatten, volgen altijd het synchrone patroon. Voor onderliggende stateful werkstromen is de run history beschikbaar die u kunt bekijken.

  Als u dit gedrag wilt inschakelen, stelt u in de JSON-definitie van de werkstroom de `operationOptions` eigenschap in op `DisableAsyncPattern` . Zie Trigger- en actietypen [- Bewerkingsopties voor meer informatie.](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)

* Activeren en wachten

  Voor een onderliggende staatloze werkstroom wacht de bovenliggende werkstroom op een antwoord dat de resultaten van het onderliggende resultaat retourneert. Dit patroon werkt vergelijkbaar met het gebruik van de ingebouwde [HTTP-trigger of -actie om](../connectors/connectors-native-http.md) een onderliggende werkstroom aan te roepen. Onderliggende staatloze werkstromen die geen antwoordactie bevatten, retourneren onmiddellijk een antwoord, maar het bovenliggende antwoord wacht tot het onderliggende proces is voltooien voordat de volgende `202 ACCEPTED` actie wordt voortgezet. Dit gedrag is alleen van toepassing op onderliggende staatloze werkstromen.

Deze tabel geeft het gedrag van de onderliggende werkstroom aan op basis van of het bovenliggende en onderliggende stateful, staatloze of gemengde werkstroomtypen zijn:

| Bovenliggende werkstroom | Onderliggende werkstroom | Onderliggend gedrag |
|-----------------|----------------|----------------|
| Stateful | Stateful | Asynchroon of synchroon met `"operationOptions": "DisableAsyncPattern"` instelling |
| Stateful | Stateless | Activeren en wachten |
| Stateless | Stateful | Synchroon |
| Stateless | Stateless | Activeren en wachten |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>Functies

Azure Logic Apps Preview bevat veel huidige en aanvullende mogelijkheden, bijvoorbeeld:

* Logische apps en hun werkstromen maken op basis van meer dan [400 connectors](/connectors/connector-reference/connector-reference-logicapps-connectors) voor SaaS-apps (Software-as-a-Service) en PaaS-apps en -services (Platform-as-a-Service), plus connectors voor on-premises systemen.

  * Sommige beheerde connectors zijn nu beschikbaar als ingebouwde versies, die op dezelfde manier worden uitgevoerd als de ingebouwde triggers en acties, zoals de aanvraagtrigger en HTTP-actie, die op een native manier worden uitgevoerd op de Azure Logic Apps Preview-runtime. Deze nieuwe ingebouwde connectors omvatten bijvoorbeeld Azure Service Bus, Azure Event Hubs, SQL Server en MQ.

    > [!NOTE]
    > Voor de ingebouwde SQL Server-connector kan alleen de actie **Query** uitvoeren rechtstreeks verbinding maken met virtuele Azure-netwerken zonder dat de [on-premises gegevensgateway is vereist.](logic-apps-gateway-connection.md)

  * Maak uw eigen ingebouwde connectors voor elke service die u nodig hebt met behulp van het framework voor extensibility van de [preview-versie.](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272) Net als bij ingebouwde connectors zoals Azure Service Bus en SQL Server, maar in tegenstelling tot aangepaste [connectors](../connectors/apis-list.md#custom-apis-and-connectors) die momenteel niet worden ondersteund voor preview, bieden deze connectors een hogere doorvoer, lage latentie, lokale connectiviteit en worden ze ingebouwde uitgevoerd in hetzelfde proces als de preview-runtime.

    De ontwerpmogelijkheid is momenteel alleen beschikbaar in Visual Studio Code, maar is niet standaard ingeschakeld. Als u deze connectors wilt maken, schakelt u uw project over van [extensiebundels (Node.js) naar NuGet-pakketten (.NET).](create-stateful-stateless-workflows-visual-studio-code.md#enable-built-in-connector-authoring) Zie Running [Anywhere Azure Logic Apps - Ingebouwde connector-extensibility voor meer informatie.](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)

  * U kunt de B2B-acties voor Liquid-bewerkingen en XML-bewerkingen gebruiken zonder een integratieaccount. Als u deze acties wilt gebruiken, moet u Liquid-kaarten, XML-kaarten of XML-schema's hebben die u kunt uploaden via de respectieve acties in de Azure Portal of toevoegen aan de map **Artifacts** van uw Visual Studio Code-project met behulp van de respectieve mappen **Kaarten** en **Schema's.**

  * Maak en implementeer logische apps die overal kunnen worden uitgevoerd omdat de Azure Logic Apps-service Shared Access Signature-verbindingsreeksen (SAS) genereert die deze logische apps kunnen gebruiken voor het verzenden van aanvragen naar het runtime-eindpunt van de cloudverbinding. De Logic Apps-service slaat deze verbindingsreeksen op met andere toepassingsinstellingen, zodat u deze waarden eenvoudig in een Azure Key Vault kunt opslaan wanneer u in Azure implementeert.

    > [!NOTE]
    > Standaard is voor een resource van [](../logic-apps/create-managed-service-identity.md) een logische app **(preview)** de door het systeem toegewezen beheerde identiteit automatisch ingeschakeld voor het verifiëren van verbindingen tijdens runtime. Deze identiteit wijkt af van de verificatiereferenties of connection string die u gebruikt wanneer u een verbinding maakt. Als u deze identiteit uit schakelen, werken verbindingen niet tijdens runtime. Als u deze instelling wilt weergeven, selecteert u identiteit in het menu van uw logische app onder **Instellingen.** 

* Maak logische apps met staatloze werkstromen die alleen in het geheugen worden uitgevoerd, zodat ze sneller worden uitgevoerd, sneller reageren, een hogere doorvoer hebben en minder kosten om uit te voeren, omdat de rungeschiedenissen en gegevens tussen acties niet blijven bestaan in externe opslag. U kunt ook de run history inschakelen voor eenvoudigere debugging. Zie Stateful versus staatloze logische [apps voor meer informatie.](#stateful-stateless)

* Uw logische apps en hun werkstromen lokaal uitvoeren, testen en er fouten in opsporen in de Visual Studio codeontwikkelingsomgeving.

  Voordat u uw logische app gaat uitvoeren en testen, kunt u debuggen eenvoudiger maken door onderbrekingspunten toe te voegen en te gebruiken in deworkflow.jsvoor een werkstroom.  Onderbrekingspunten worden op dit moment echter alleen ondersteund voor acties, niet voor triggers. Zie Stateful en stateless werkstromen maken in Visual Studio [Code voor meer informatie.](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)

* U kunt logische apps en hun werkstromen rechtstreeks publiceren of implementeren vanuit Visual Studio Code naar verschillende hostingomgevingen, zoals Azure- en [Docker-containers.](/dotnet/core/docker/introduction)

* Schakel diagnostische logboekregistratie en traceringsmogelijkheden in voor uw logische app door gebruik te [Application Insights](../azure-monitor/app/app-insights-overview.md) wanneer dit wordt ondersteund door uw Azure-abonnement en instellingen voor logische apps.

* Toegang tot netwerkmogelijkheden, zoals verbinding maken en privé integreren met virtuele Azure-netwerken, vergelijkbaar met Azure Functions wanneer u logische apps maakt en implementeert met behulp van het [Azure Functions Premium-abonnement](../azure-functions/functions-premium-plan.md). Lees de volgende onderwerpen voor meer informatie:

  * [Netwerkopties van Azure Functions](../azure-functions/functions-networking-options.md)

  * [Azure Logic Apps Running Anywhere - Netwerkmogelijkheden met Azure Logic Apps Preview](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* Toegangssleutels opnieuw maken voor beheerde verbindingen die worden gebruikt door afzonderlijke werkstromen in de logische app met één tenant **(preview).** Voor deze taak volgt u dezelfde stappen voor de [multi-tenant **Logic Apps** resource,](logic-apps-securing-a-logic-app.md#regenerate-access-keys)maar op het niveau van de afzonderlijke werkstroom, niet op het resourceniveau van de logische app.

* Voeg parallelle vertakkingen toe in de ontwerpfunctie voor één tenant door dezelfde stappen te volgen als de ontwerpfunctie voor meerdere tenants.

Zie Voor meer informatie [Gewijzigde, beperkte,](#limited-unavailable-unsupported) niet-beschikbare en niet-ondersteunde mogelijkheden en de pagina Logic Apps [Public Preview Known Issues in GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Prijsmodel

Wanneer u het type logische app met één tenant maakt in de Azure Portal of implementeert vanuit Visual Studio Code, moet u een hostingplan [kiezen, App Service of Premium,](../azure-functions/functions-scale.md)dat door uw logische app kan worden gebruikt. Dit abonnement bepaalt het prijsmodel dat van toepassing is op het uitvoeren van uw logische app. Als u het App Service selecteert, moet u ook een [prijscategorie kiezen.](../app-service/overview-hosting-plans.md)

*Stateful werkstromen* maken gebruik [](https://azure.microsoft.com/pricing/details/storage/) van externe [opslag,](../azure-functions/storage-considerations.md#storage-account-requirements)zodat de Azure Storage van toepassing zijn op opslagtransacties die door Azure Logic Apps Preview-runtime worden uitgevoerd. Wachtrijen worden bijvoorbeeld gebruikt voor planning, terwijl tabellen en blobs worden gebruikt voor het opslaan van werkstroomstatussen.

> [!NOTE]
> Tijdens de openbare preview worden voor het uitvoeren  van logische apps App Service extra kosten in rekening gebracht boven op het geselecteerde abonnement.

Lees de volgende onderwerpen voor meer informatie over de prijsmodellen die van toepassing zijn op het resourcetype voor één tenant:

* [Schaal en hosting van Azure Functions](../azure-functions/functions-scale.md)
* [Een app omhoog schalen in Azure App Service](../app-service/manage-scale-up.md)
* [Azure Functions prijsinformatie](https://azure.microsoft.com/pricing/details/functions/)
* [App Service prijsinformatie](https://azure.microsoft.com/pricing/details/app-service/)
* [Azure Storage prijsinformatie](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>Gewijzigde, beperkte, niet-beschikbare of niet-ondersteunde mogelijkheden

In Azure Logic Apps Preview zijn deze mogelijkheden gewijzigd of zijn ze momenteel beperkt, niet beschikbaar of niet ondersteund:

* **Ondersteuning voor** het besturingssysteem: momenteel werkt de ontwerpfunctie in Visual Studio Code niet in het Linux-besturingssysteem, maar u kunt wel logische apps die gebruikmaken van de Logic Apps Preview-runtime implementeren op virtuele Linux-machines. Op dit moment kunt u uw logische apps bouwen in Visual Studio Code in Windows of macOS en vervolgens implementeren op een virtuele Linux-machine.

* **Triggers en acties:** ingebouwde triggers en acties worden standaard uitgevoerd in de Azure Logic Apps Preview-runtime, terwijl beheerde connectors worden geïmplementeerd in Azure. Sommige ingebouwde triggers zijn niet beschikbaar, zoals Sliding Window en Batch.

  Gebruik de ingebouwde trigger [Recurrence, Request, HTTP, HTTP Webhook, Event Hubs of Service Bus](../connectors/apis-list.md)om uw werkstroom te starten. In de ontwerpfunctie worden ingebouwde triggers en acties weergegeven op het tabblad **Ingebouwd,** terwijl triggers en acties van beheerde connectors worden weergegeven op het **tabblad Azure.**

  > [!NOTE]
  > Als u lokaal wilt Visual Studio code, moeten op webhook gebaseerde triggers en acties extra worden ingesteld. Zie Stateful en stateless werkstromen maken in Visual Studio [Code voor meer informatie.](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup)

  * Voor *staatloze werkstromen* wordt het **tabblad Azure** niet weergegeven wanneer u een trigger selecteert, omdat u alleen acties voor beheerde connectors kunt [selecteren, niet activeert.](../connectors/managed.md) Hoewel u door Azure geïmplementeerde beheerde connectors kunt inschakelen voor staatloze werkstromen, toont de ontwerpfunctie geen triggers voor beheerde connectors die u kunt toevoegen.

  * Voor *stateful werkstromen* zijn naast de triggers en acties die hieronder worden vermeld als niet-beschikbaar, zowel triggers als acties van beheerde [connectors](../connectors/managed.md) beschikbaar die u kunt gebruiken.

  * Deze triggers en acties zijn gewijzigd of zijn momenteel beperkt, niet-ondersteund of niet beschikbaar:

    * [On-premises *gegevensgatewaytriggers*](../connectors/managed.md#on-premises-connectors) zijn niet beschikbaar, maar gatewayacties *zijn* wel beschikbaar.

    * De ingebouwde actie, [Azure Functions-](logic-apps-azure-functions.md) Een Azure-functie kiezen is nu **Azure-functiebewerkingen - Een Azure-functie aanroepen.** Deze actie werkt momenteel alleen voor functies die zijn gemaakt op basis van de **HTTP-triggersjabloon.**

      In de Azure Portal kunt u een HTTP-triggerfunctie selecteren waar u toegang toe hebt door een verbinding te maken via de gebruikerservaring. Als u de JSON-definitie van de functieactie inspecteert in de codeweergave of de **workflow.jsin** het bestand, verwijst de actie met behulp van een verwijzing naar de `connectionName` functie. In deze versie worden de gegevens van de functie geabstraheerd als een verbinding, die u kunt vinden in de **connections.jsvan** uw project op bestand, dat beschikbaar is nadat u een verbinding hebt gemaakt.

      > [!NOTE]
      > In de versie met één tenant ondersteunt de functieactie alleen verificatie van queryreeksen. Azure Logic Apps Preview haalt de standaardsleutel van de functie op bij het maken van de verbinding, slaat die sleutel op in de instellingen van uw app en gebruikt de sleutel voor verificatie bij het aanroepen van de functie.
      >
      > Net als bij de versie met meerdere tenants werkt de functieactie niet meer als u deze sleutel vernieuwt, bijvoorbeeld via de Azure Functions-ervaring in de portal. Om dit probleem op te lossen, moet u de verbinding opnieuw maken met de functie die u wilt aanroepen of de instellingen van uw app bijwerken met de nieuwe sleutel.

    * De ingebouwde actie [Inline Code - JavaScript-code](logic-apps-add-run-inline-code.md) uitvoeren is nu **Inline-codebewerkingen - Inline JavaScript uitvoeren.**

      * Voor inline-codebewerkingen is geen integratieaccount meer vereist.

      * Voor macOS en Linux wordt **Inline Code Operations** nu ondersteund wanneer u de extensie Azure Logic Apps (preview) in Visual Studio Code.

      * U hoeft uw logische app niet meer opnieuw op te starten als u wijzigingen aanneemt in een **inline-codebewerkingen-actie.**

      * **Inline Code Operations-acties** hebben [bijgewerkte limieten.](logic-apps-overview-preview.md#inline-code-limits)

    * Sommige [ingebouwde B2B-triggers](../connectors/managed.md#integration-account-connectors) en -acties voor integratieaccounts  zijn niet beschikbaar, bijvoorbeeld de coderings- en decodeeracties voor plat bestand.

    * De ingebouwde actie, Azure Logic Apps: een logic [app-werkstroom](logic-apps-http-endpoint.md) kiezen is nu Werkstroombewerkingen - Een werkstroom **aanroepen in deze werkstroom-app.**

* [Aangepaste connectors](../connectors/apis-list.md#custom-apis-and-connectors) worden momenteel niet ondersteund voor preview.

* Beschikbaarheid **van** hostingplannen: of u nu het resourcetype logische app met één tenant **(preview)** in de Azure Portal maakt of implementeert vanuit Visual Studio Code, u kunt alleen het Premium- of App Service-hostingplan in Azure gebruiken. Hostingplannen voor verbruik zijn niet beschikbaar en worden niet ondersteund voor het implementeren van dit resourcetype. U kunt vanuit Visual Studio Code implementeren in een Docker-container, maar niet in een [ISE (Integration Service Environment).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)

* Onderbrekingspuntopsporing **in Visual Studio-code:** hoewel u onderbrekingspunten in het **workflow.js-bestand** voor een werkstroom kunt toevoegen en gebruiken, worden onderbrekingspunten momenteel alleen ondersteund voor acties, niet voor triggers. Zie Stateful en stateless werkstromen maken in Visual Studio [Code voor meer informatie.](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)

* **Besturingselement voor** zoomen: het zoombesturingselement is momenteel niet beschikbaar in de ontwerpfunctie.

* **Triggergeschiedenis en**-run history: voor het resourcetype Logische **app (preview)** worden de triggergeschiedenis en de run history in de Azure Portal weergegeven op werkstroomniveau, niet op het niveau van de logische app. Volg deze stappen om deze historische gegevens te vinden:

   * Als u de geschiedenis van de run wilt weergeven, opent u de werkstroom in uw logische app. Selecteer in het werkstroommenu onder **Ontwikkelaar** de optie **Controleren.**

   * Als u de triggergeschiedenis wilt bekijken, opent u de werkstroom in uw logische app. Selecteer in het werkstroommenu onder **Ontwikkelaar** de optie **TriggerGeschiedeniss.**

<a name="firewall-permissions"></a>

## <a name="permit-traffic-in-strict-network-and-firewall-scenarios"></a>Verkeer toestaan in strikte netwerk- en firewallscenario's

Als uw omgeving strikte netwerkvereisten of firewalls heeft die het verkeer beperken, moet u toegang toestaan voor trigger- of actieverbindingen in de werkstromen van uw logische app.

Als u de FQDN's (Fully Qualified Domain Names) voor deze verbindingen wilt vinden, bekijkt u de bijbehorende secties in deze onderwerpen:

* [Firewallmachtigingen voor logische apps met één tenant - Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md#firewall-setup)
* [Firewallmachtigingen voor logische apps met één tenant - Azure Portal](create-stateful-stateless-workflows-azure-portal.md#firewall-setup)

<a name="limits"></a>

## <a name="updated-limits"></a>Bijgewerkte limieten

Hoewel veel limieten voor Azure Logic Apps Preview hetzelfde blijven als de limieten voor [multi-tenant Azure Logic Apps,](logic-apps-limits-and-config.md)zijn deze limieten gewijzigd voor Azure Logic Apps Preview.

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>Http-time-outlimieten

Voor één binnenkomende aanroep of uitgaande aanroep is de time-outlimiet 230 seconden (3,9 minuten) voor deze triggers en acties:

* Uitgaande aanvraag: HTTP-trigger, HTTP-actie
* Inkomende aanvraag: aanvraagtrigger, HTTP-webhooktrigger, HTTP-webhookactie

Ter vergelijking: hier volgen de time-outlimieten voor deze triggers en acties in andere omgevingen waar logische apps en hun werkstromen worden uitgevoerd:

* Multi-tenant Azure Logic Apps: 120 seconden (2 minuten)
* Integratieserviceomgeving: 240 seconden (4 minuten)

Zie HTTP-limieten [voor meer informatie.](logic-apps-limits-and-config.md#http-limits)

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>Beheerde connectors

Beheerde connectors zijn beperkt tot 50 aanvragen per minuut per verbinding. Zie Beperkingsproblemen [(429 - 'Te](handle-throttling-problems-429-errors.md#connector-throttling)veel aanvragen' afhandelen) in Azure Logic Apps om te werken met problemen met connectorbeperking.

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>Inline codebewerkingen (JavaScript-code uitvoeren)

Voor de definitie van één logische app heeft de actie Inline Code Operations, [**JavaScript-code**](logic-apps-add-run-inline-code.md)uitvoeren, de volgende bijgewerkte limieten:

* Het maximum aantal codetekens neemt toe van 1024 tekens tot 100.000 tekens.

* De maximale duur voor het uitvoeren van code wordt verhoogd van vijf seconden naar 15 seconden.

Zie Definitielimieten voor logische [apps voor meer informatie.](logic-apps-limits-and-config.md#definition-limits)

## <a name="next-steps"></a>Volgende stappen

* [Stateful en stateless werkstromen maken in de Azure Portal](create-stateful-stateless-workflows-azure-portal.md)
* [Stateful en stateless werkstromen maken in Visual Studio Code](create-stateful-stateless-workflows-visual-studio-code.md)
* [Logic Apps Public Preview Known Issues page in GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

We horen graag van u over uw ervaringen met Azure Logic Apps Preview.

* Maak voor fouten of problemen [uw problemen in GitHub](https://github.com/Azure/logicapps/issues).
* Gebruik dit feedbackformulier voor vragen, aanvragen, opmerkingen en [andere feedback.](https://aka.ms/lafeedback)
