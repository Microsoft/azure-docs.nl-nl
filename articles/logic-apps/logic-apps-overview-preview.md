---
title: Overzicht voor Azure Logic Apps-preview
description: Azure Logic Apps Preview is een Cloud oplossing voor het bouwen van geautomatiseerde, single tenant-, stateful en stateless werk stromen die apps, gegevens, services en systemen integreren met minimale code voor scenario's op ondernemings niveau.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, az-logic-apps-dev
ms.topic: conceptual
ms.date: 03/10/2021
ms.openlocfilehash: 7120b6ff17657232c0e614f49b75bb24263712b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102636332"
---
# <a name="overview-azure-logic-apps-preview"></a>Overzicht: Azure Logic Apps Preview

> [!IMPORTANT]
> Deze functie is in openbare preview en wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met Azure Logic Apps Preview kunt u automatiserings-en integratie oplossingen bouwen in apps, gegevens, Cloud Services en systemen door logische apps met één Tenant te maken en uit te voeren met het nieuwe resource type **Logic app (preview)** . Met dit type logische app voor één Tenant kunt u meerdere [ *stateful* en *stateless* werk stromen](#stateful-stateless) bouwen die worden aangedreven door de opnieuw ontworpen Azure Logic Apps Preview-runtime, die draag baarheid, betere prestaties en flexibiliteit biedt voor het implementeren en uitvoeren in verschillende hosting omgevingen, waaronder niet alleen Azure, maar ook docker-containers.

Hoe is dat mogelijk? De opnieuw ontworpen runtime maakt gebruik van het [Azure functions-uitbreidings model](../azure-functions/functions-bindings-register.md) en wordt gehost als een uitbrei ding van de Azure functions runtime. Deze architectuur houdt in dat u het type logische app met één Tenant kunt uitvoeren waar Azure Functions wordt uitgevoerd. U kunt de opnieuw ontworpen runtime hosten op bijna elke netwerk topologie en een beschik bare reken grootte kiezen voor het verwerken van de benodigde werk belasting die wordt vereist door uw werk stromen. Zie [Inleiding tot Azure functions](../azure-functions/functions-overview.md) en [Azure functions triggers en bindingen](../azure-functions/functions-triggers-bindings.md)voor meer informatie.

U kunt de **logische app (preview)** -resource maken door te [beginnen in de Azure Portal](create-stateful-stateless-workflows-azure-portal.md) of door [een project te maken in Visual Studio code met de extensie Azure Logic apps (preview)](create-stateful-stateless-workflows-visual-studio-code.md). Daarnaast kunt u in Visual Studio code uw werk stromen bouwen *en lokaal uitvoeren* in uw ontwikkel omgeving. Of u de portal of Visual Studio code gebruikt, u kunt het type van de logische app voor één Tenant implementeren en uitvoeren in hetzelfde soort hosting omgevingen.

In dit overzicht worden de volgende gebieden beschreven:

* [Verschillen tussen Azure Logic Apps Preview, de Azure Logic apps multi tenant-omgeving en de integratie service omgeving](#preview-differences).

* [Verschillen tussen stateful en stateless werk stromen](#stateful-stateless), met inbegrip van gedrags verschillen tussen [geneste stateful en stateless werk stromen](#nested-behavior).

* [Mogelijkheden in deze open bare preview](#public-preview-contents).

* [Hoe het prijs model werkt](#pricing-model).

* [Gewijzigde, beperkte, niet-beschik bare en niet-ondersteunde mogelijkheden](#limited-unavailable-unsupported).

* [Limieten in azure Logic Apps Preview](#limits).

Raadpleeg de volgende onderwerpen voor meer informatie:

* [Azure Logic Apps uitvoeren van Anywhere-runtime dieper](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)

* [Bekende problemen met Logic Apps open bare Preview (GitHub)](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

<a name="preview-differences"></a>

## <a name="how-does-azure-logic-apps-preview-differ"></a>Hoe verschilt Azure Logic Apps Preview?

De Azure Logic Apps Preview-runtime gebruikt [Azure functions](../azure-functions/functions-overview.md) uitbreid baarheid en wordt gehost als een uitbrei ding van de Azure functions runtime. Met deze architectuur kunt u het type van de logische app met één Tenant uitvoeren, waar Azure Functions uitgevoerd. U kunt de runtime van Azure Logic Apps Preview hosten op bijna elke gewenste netwerk topologie en een beschik bare reken grootte kiezen voor het afhandelen van de benodigde werk belasting die uw werk stroom nodig heeft. Zie [Webjobs SDK: aangepaste invoer-en uitvoer bindingen maken](https://github.com/Azure/azure-webjobs-sdk/wiki/Creating-custom-input-and-output-bindings)voor meer informatie over Azure functions uitbreid baarheid.

Met deze nieuwe benadering zijn de runtime van Azure Logic Apps Preview en uw werk stromen beide deel uit van uw app die u kunt inpakken. Met deze mogelijkheid kunt u uw werk stromen implementeren en uitvoeren door simpelweg artefacten naar de hostomgeving te kopiëren en de app te starten. Deze benadering biedt ook een meer gestandaardiseerde ervaring voor het bouwen van implementatie pijplijnen rond de werk stroom projecten voor het uitvoeren van de vereiste tests en validaties voordat u wijzigingen in productie omgevingen implementeert. Zie Azure Logic Apps de uitvoering van [Anywhere-runtime grondig](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-runtime-deep-dive/ba-p/1835564)uit voor meer informatie.

De volgende tabel geeft een overzicht van de verschillen in de manier waarop werk stromen resources delen, op basis van de omgeving waar ze worden uitgevoerd. Zie [limieten in azure Logic Apps Preview](#limits)voor verschillen in limieten.

| Omgeving | Resources delen en verbruiken |
|-------------|----------------------------------|
| Azure Logic Apps (multi tenant) | Werk stromen *van klanten over meerdere tenants* delen dezelfde verwerking (Compute), opslag, netwerk, enzovoort. |
| Azure Logic Apps (preview, single-tenant) | Werk stromen *in dezelfde logische app en één Tenant* delen dezelfde verwerking (Compute), opslag, netwerk, enzovoort. |
| Integratie service omgeving (niet beschikbaar in de preview-versie) | Werk stromen in *dezelfde omgeving* delen dezelfde verwerking (Compute), opslag, netwerk, enzovoort. |
|||

Ondertussen kunt u nog steeds het type logische app voor meerdere tenants maken in de Azure Portal en in Visual Studio code met behulp van de Azure Logic Apps extensie voor meerdere tenants. Hoewel de ontwikkelings ervaring verschilt tussen de typen van de logische app met meerdere tenants en single tenants, kan uw Azure-abonnement beide typen bevatten. U kunt alle geïmplementeerde Logic apps in uw Azure-abonnement weer geven en openen, maar de apps zijn ingedeeld in hun eigen categorieën en secties.

<a name="stateful-stateless"></a>

## <a name="stateful-and-stateless-workflows"></a>Stateful en stateless werk stromen

Met het type logische app voor één Tenant kunt u deze werk stroom typen maken binnen dezelfde logische app:

* *Stateful*

  Maak stateful werk stromen wanneer u gegevens uit eerdere gebeurtenissen wilt houden, controleren of ernaar wilt verwijzen. Met deze werk stromen worden de invoer en uitvoer voor elke actie en hun status in externe opslag opgeslagen, waardoor de details van de uitvoering en de geschiedenis mogelijk worden gecontroleerd nadat elke uitvoering is voltooid. Stateful werk stromen bieden hoge tolerantie als er storingen optreden. Nadat de services en systemen zijn hersteld, kunt u onderbroken uitvoeringen reconstrueren van de opgeslagen status en de werk stromen opnieuw uitvoeren om deze te volt ooien. Stateful werk stromen kunnen Maxi maal een jaar worden uitgevoerd.

* *Stateless*

  Maak stateless werk stromen wanneer u geen gegevens hoeft op te slaan, te controleren of ernaar te verwijzen vanuit eerdere gebeurtenissen in de externe opslag om deze later te controleren. Met deze werk stromen worden de invoer en uitvoer voor elke actie en hun status *alleen in het geheugen* opgeslagen, in plaats van deze gegevens naar externe opslag te verplaatsen. Als gevolg hiervan hebben stateless werk stromen kortere uitvoeringen die doorgaans niet langer zijn dan vijf minuten, snellere prestaties met snellere reactie tijden, een hogere door Voer en lagere kosten, omdat de details van de uitvoering en de geschiedenis niet in de externe opslag worden bewaard. Als er echter storingen optreden, worden onderbroken uitvoeringen niet automatisch hersteld. de oproepende functie moet de onderbroken uitvoeringen hand matig opnieuw verzenden. Deze werk stromen kunnen alleen synchroon worden uitgevoerd.

  Voor eenvoudiger fout opsporing kunt u de uitvoerings geschiedenis voor een staatloze werk stroom inschakelen, wat invloed heeft op de prestaties en vervolgens de uitvoerings geschiedenis uitschakelen wanneer u klaar bent. Zie [stateful en stateless werk stromen maken in Visual Studio code](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless) of [stateful en stateless werk stromen maken in de Azure Portal](create-stateful-stateless-workflows-visual-studio-code.md#enable-run-history-stateless)voor meer informatie.

  > [!NOTE]
  > Stateless werk stromen ondersteunen momenteel alleen *acties* voor [beheerde connectors](../connectors/apis-list.md#managed-api-connectors), die worden geïmplementeerd in azure, en niet voor triggers. Als u uw werk stroom wilt starten, selecteert u de [ingebouwde aanvraag, Event hubs of service bus trigger](../connectors/apis-list.md#built-ins). Deze triggers worden standaard uitgevoerd in de runtime van Azure Logic Apps Preview. Zie voor meer informatie over beperkte, niet-beschik bare of niet-ondersteunde triggers, acties en connectors [gewijzigde, beperkte, niet-beschik bare en niet-ondersteunde mogelijkheden](#limited-unavailable-unsupported).

<a name="nested-behavior"></a>

### <a name="nested-behavior-differences-between-stateful-and-stateless-workflows"></a>Verschillen in genest gedrag tussen stateful en stateless werk stromen

U kunt [een werk stroom aanroepen](../logic-apps/logic-apps-http-endpoint.md) van andere werk stromen die zich in dezelfde **logische app (preview)** bevinden door gebruik te maken van de trigger voor de [aanvraag](../connectors/connectors-native-reqres.md), [http-webhook-trigger](../connectors/connectors-native-webhook.md)of beheerde connector triggers die het [ApiConnectionWebhook-type](../logic-apps/logic-apps-workflow-actions-triggers.md#apiconnectionwebhook-trigger) hebben en HTTPS-aanvragen kunnen ontvangen.

Dit zijn de gedrags patronen die geneste werk stromen kunnen volgen nadat een bovenliggende werk stroom een onderliggende werk stroom aanroept:

* Asynchroon polling-patroon

  Het bovenliggende item wordt niet gewacht op een antwoord op de eerste aanroep, maar controleert doorlopend de uitvoerings geschiedenis van het kind totdat het onderliggende item is voltooid. Stateful werk stromen volgen standaard dit patroon. Dit is ideaal voor langlopende onderliggende werk stromen die mogelijk de [time-outlimieten voor aanvragen](../logic-apps/logic-apps-limits-and-config.md)overschrijden.

* Synchroon patroon ("vuur en verg eten")

  Het onderliggend item erkent de oproep door onmiddellijk een `202 ACCEPTED` reactie te retour neren en de bovenliggende actie wordt voortgezet zonder te hoeven wachten op de resultaten van het onderliggende item. In plaats daarvan ontvangt het bovenliggende item de resultaten wanneer het onderliggende element is voltooid. Onderliggende stateful werk stromen die geen reactie actie bevatten, volgen altijd het synchrone patroon. Voor onderliggende stateful werk stromen is de uitvoerings geschiedenis beschikbaar die u kunt controleren.

  Als u dit gedrag wilt inschakelen, stelt u in de JSON-definitie van de werk stroom de `operationOptions` eigenschap in op `DisableAsyncPattern` . Zie [trigger-en actie typen-bewerkings opties](../logic-apps/logic-apps-workflow-actions-triggers.md#operation-options)voor meer informatie.

* Activeren en wachten

  Voor een onderliggend werk stroom voor een onderliggend niveau wordt gewacht op een reactie die de resultaten van het onderliggende item retourneert. Dit patroon werkt op vergelijk bare wijze als het gebruik van de ingebouwde [http-trigger of actie](../connectors/connectors-native-http.md) voor het aanroepen van een onderliggende werk stroom. Onderliggende stateless werk stromen die geen antwoord actie bevatten, retour neren onmiddellijk een `202 ACCEPTED` antwoord, maar het bovenliggende item wacht tot het onderliggende item is voltooid voordat de volgende actie kan worden voortgezet. Deze gedragingen zijn alleen van toepassing op werk stromen met een onderliggend niveau.

In deze tabel wordt het gedrag van de onderliggende werk stroom opgegeven op basis van het feit of het bovenliggende en het onderliggende niveau stateful, stateless of gemengde werk stroom typen zijn:

| Bovenliggende werk stroom | Onderliggende werk stroom | Onderliggend gedrag |
|-----------------|----------------|----------------|
| Stateful | Stateful | Asynchroon of synchroon met `"operationOptions": "DisableAsyncPattern"` instelling |
| Stateful | Stateless | Activeren en wachten |
| Stateless | Stateful | Synchroon |
| Stateless | Stateless | Activeren en wachten |
||||

<a name="public-preview-contents"></a>

## <a name="capabilities"></a>Functies

Azure Logic Apps Preview bevat een groot aantal huidige en aanvullende mogelijkheden, bijvoorbeeld:

* Maak Logic apps en hun werk stromen van [400 en connectors](/connectors/connector-reference/connector-reference-logicapps-connectors) voor software-as-a-Service (SaaS) en platform-as-a-Service (PaaS)-apps en-services plus connectors voor on-premises systemen.

  * Sommige beheerde connectors zijn nu beschikbaar als ingebouwde versies, die op dezelfde manier worden uitgevoerd als de ingebouwde triggers en acties, zoals de trigger voor aanvragen en de HTTP-actie, die systeem eigen worden uitgevoerd op de Azure Logic Apps Preview-runtime. Deze nieuwe ingebouwde connectors bevatten bijvoorbeeld Azure Service Bus, Azure Event Hubs, SQL Server en MQ.

    > [!NOTE]
    > Voor de ingebouwde SQL Server-connector kan alleen de actie **query uitvoeren** rechtstreeks verbinding maken met virtuele Azure-netwerken zonder dat de [on-premises gegevens gateway](logic-apps-gateway-connection.md)is vereist.

  * Maak uw eigen ingebouwde Connect oren voor elke service die u nodig hebt met behulp [van het uitbreidings raamwerk van de preview-versie](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272). Net als bij ingebouwde connectors, zoals Azure Service Bus en SQL Server, maar in tegens telling tot [aangepaste connectors](../connectors/apis-list.md#custom-apis-and-connectors) die momenteel niet worden ondersteund voor preview, bieden deze connectors een hogere door Voer, lage latentie en lokale connectiviteit, en worden ze systeem eigen uitgevoerd in hetzelfde proces als de runtime van de preview-versie.

    De ontwerp functie is momenteel alleen beschikbaar in Visual Studio code, maar is niet standaard ingeschakeld. Als u deze connectors wilt maken, moet u [uw project overschakelen van een op een NuGet (Node.js op basis van een op extensie gebaseerd) naar een op het pakket gebaseerde (.net)](create-stateful-stateless-workflows-visual-studio-code.md#enable-built-in-connector-authoring). Zie Azure Logic Apps voor het [uitvoeren van Anywhere-ingebouwde connector Extensibility](https://techcommunity.microsoft.com/t5/integrations-on-azure/azure-logic-apps-running-anywhere-built-in-connector/ba-p/1921272)voor meer informatie.

  * U kunt de B2B-acties voor liquide bewerkingen en XML-bewerkingen zonder een integratie account gebruiken. Als u deze acties wilt gebruiken, moet u beschikken over liquide kaarten, XML-kaarten of XML-schema's die u kunt uploaden via de respectieve acties in de Azure Portal, of toevoegen aan de map **artefacten** van Visual Studio code-project met behulp van de respectieve **kaarten** en **schema's** mappen.

  * Maak en implementeer logische apps die overal kunnen worden uitgevoerd, omdat de Azure Logic Apps service de verbindings reeksen voor Shared Access Signature (SAS) genereert die door deze Logic apps kunnen worden gebruikt voor het verzenden van aanvragen naar het runtime-eind punt voor de Cloud verbinding. De Logic Apps-service slaat deze verbindings reeksen op met andere toepassings instellingen, zodat u deze waarden eenvoudig kunt opslaan in Azure Key Vault wanneer u in azure implementeert.

    > [!NOTE]
    > Standaard heeft een resource- **app (preview)** een door het [systeem toegewezen beheerde identiteit](../logic-apps/create-managed-service-identity.md) automatisch ingeschakeld voor het verifiëren van verbindingen tijdens runtime. Deze identiteit wijkt af van de verificatie referenties of connection string die u gebruikt bij het maken van een verbinding. Als u deze identiteit uitschakelt, werken verbindingen niet tijdens runtime. Als u deze instelling wilt weer geven, selecteert u in het menu van de logische app onder **instellingen** de optie **identiteit**.

* Maak logische apps met stateless werk stromen die alleen in het geheugen worden uitgevoerd, zodat ze sneller worden voltooid, snellere reageert, een hogere door voer hebben en de kost prijs minder hebben om te worden uitgevoerd omdat de run-geschiedenis en gegevens tussen acties niet behouden blijven in de externe opslag. Optioneel kunt u de uitvoerings geschiedenis inschakelen voor eenvoudiger fout opsporing. Zie [stateful versus stateless Logic apps](#stateful-stateless)voor meer informatie.

* U kunt uw logische apps en hun werk stromen lokaal uitvoeren, testen en fouten opsporen in de ontwikkel omgeving van Visual Studio code.

  Voordat u uw logische app uitvoert en test, kunt u de fout opsporing vereenvoudigen door onderbrekings punten toe te voegen en te gebruiken in het **workflow.js** bestand voor een werk stroom. Onderbrekings punten worden op dit moment echter alleen ondersteund voor acties, niet voor triggers. Zie [stateful en stateless werk stromen maken in Visual Studio code](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)voor meer informatie.

* Publiceer of implementeer logische apps en hun werk stromen rechtstreeks vanuit Visual Studio code naar verschillende hosting omgevingen, zoals Azure en [docker-containers](/dotnet/core/docker/introduction).

* Schakel diagnostische logboek registratie en tracerings mogelijkheden in voor uw logische app door gebruik te maken van [Application Insights](../azure-monitor/app/app-insights-overview.md) als dit wordt ondersteund door uw Azure-abonnement en de instellingen van de logische app.

* Toegang tot netwerk mogelijkheden, zoals verbinding maken en integreren met Azure Virtual Networks, vergelijkbaar met Azure Functions wanneer u uw Logic apps maakt en implementeert met behulp van het [Premium-abonnement van Azure functions](../azure-functions/functions-premium-plan.md). Raadpleeg de volgende onderwerpen voor meer informatie:

  * [Netwerkopties van Azure Functions](../azure-functions/functions-networking-options.md)

  * [Azure Logic Apps Anywhere-netwerk mogelijkheden uitvoeren met Azure Logic Apps Preview](https://techcommunity.microsoft.com/t5/integrations-on-azure/logic-apps-anywhere-networking-possibilities-with-logic-app/ba-p/2105047)

* Opnieuw genereren van toegangs sleutels voor beheerde verbindingen die door afzonderlijke werk stromen worden gebruikt in de resource voor een logische app met één Tenant **(preview-versie)** . Volg voor deze taak [dezelfde stappen voor de multi tenant- **Logic apps** resource, maar op het individuele werk stroom niveau](logic-apps-securing-a-logic-app.md#regenerate-access-keys), niet op het resource niveau van de logische app.

* Voeg parallelle vertakkingen toe aan de single tenant Designer door dezelfde stappen te volgen als de multi tenant-ontwerp functie.

Zie voor meer informatie [gewijzigde, beperkte, niet-beschik bare en niet-ondersteunde mogelijkheden](#limited-unavailable-unsupported) en de [pagina met bekende problemen met de Logic apps open bare preview in github](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md).

<a name="pricing-model"></a>

## <a name="pricing-model"></a>Prijsmodel

Wanneer u het type logische app voor één Tenant maakt in de Azure Portal of implementeren vanuit Visual Studio code, moet u een hosting abonnement kiezen, hetzij [app service of Premium](../azure-functions/functions-scale.md), voor uw logische app. Dit abonnement bepaalt het prijs model dat van toepassing is op het uitvoeren van uw logische app. Als u het App Service plan selecteert, moet u ook een [prijs categorie](../app-service/overview-hosting-plans.md)kiezen.

*Stateful* werk stromen gebruiken [externe opslag](../azure-functions/storage-considerations.md#storage-account-requirements), zodat de [Azure Storage prijzen](https://azure.microsoft.com/pricing/details/storage/) van toepassing zijn op opslag transacties die de Azure Logic Apps Preview-runtime uitvoert. Wacht rijen worden bijvoorbeeld gebruikt voor de planning, terwijl tabellen en blobs worden gebruikt voor het opslaan van werk stroom statussen.

> [!NOTE]
> Tijdens de open bare preview worden met Logic apps op App Service geen *extra* kosten boven op het geselecteerde abonnement gemaakt.

Lees de volgende onderwerpen voor meer informatie over de prijs modellen die van toepassing zijn op het resource type met één Tenant:

* [Schaal en hosting van Azure Functions](../azure-functions/functions-scale.md)
* [Een app omhoog schalen in Azure App Service](../app-service/manage-scale-up.md)
* [Prijs informatie voor Azure Functions](https://azure.microsoft.com/pricing/details/functions/)
* [Prijs informatie voor App Service](https://azure.microsoft.com/pricing/details/app-service/)
* [Prijs informatie voor Azure Storage](https://azure.microsoft.com/pricing/details/storage/)

<a name="limited-unavailable-unsupported"></a>

## <a name="changed-limited-unavailable-or-unsupported-capabilities"></a>Gewijzigde, beperkte, niet-beschik bare en niet-ondersteunde mogelijkheden

In Azure Logic Apps Preview zijn deze mogelijkheden gewijzigd, of ze zijn momenteel beperkt, niet beschikbaar of niet-ondersteund:

* **Ondersteuning voor besturings systeem**: momenteel werkt de ontwerp functie in Visual Studio code niet in Linux-besturings systeem, maar u kunt wel logische Apps implementeren die gebruikmaken van de Logic Apps Preview-runtime voor op Linux gebaseerde virtuele machines. Voor Taan kunt u uw Logic apps in Visual Studio code op Windows of macOS bouwen en vervolgens implementeren op een virtuele machine op basis van Linux.

* **Triggers en acties**: ingebouwde triggers en acties worden standaard uitgevoerd in de runtime van Azure Logic Apps Preview, terwijl beheerde connectors worden geïmplementeerd in Azure. Sommige ingebouwde triggers zijn niet beschikbaar, zoals verschuivend venster en batch.

  Als u uw werk stroom wilt starten, gebruikt u de trigger voor het [ingebouwde terugkeer patroon, aanvraag, http, HTTP-webhook, Event hubs of service bus](../connectors/apis-list.md). In de ontwerp functie worden ingebouwde triggers en acties weer gegeven onder het **ingebouwde** tabblad, terwijl beheerde connector triggers en acties worden weer gegeven op het tabblad **Azure** .

  > [!NOTE]
  > Als u lokaal wilt uitvoeren in Visual Studio code, moeten triggers en acties op basis van webhooks extra worden ingesteld. Zie [stateful en stateless werk stromen maken in Visual Studio code](create-stateful-stateless-workflows-visual-studio-code.md#webhook-setup)voor meer informatie.

  * Voor *stateless werk stromen* wordt het tabblad **Azure** niet weer gegeven wanneer u een trigger selecteert, omdat u alleen [beheerde connector *acties* kunt selecteren, niet triggers](../connectors/apis-list.md#managed-api-connectors). Hoewel u door Azure geïmplementeerde beheerde connectors kunt inschakelen voor stateless werk stromen, worden in de ontwerp functie geen beheerde connector triggers weer gegeven die u wilt toevoegen.

  * Voor *stateful werk stromen*, met uitzonde ring van de triggers en acties die hieronder niet beschikbaar zijn, zijn zowel [beheerde connector triggers als acties](../connectors/apis-list.md#managed-api-connectors) beschikbaar die u kunt gebruiken.

  * Deze triggers en acties zijn gewijzigd of zijn momenteel beperkt, worden niet ondersteund of zijn niet beschikbaar:

    * [On-premises gegevens gateway- *Triggers*](../connectors/apis-list.md#on-premises-connectors) zijn niet beschikbaar, maar gateway acties *zijn* beschikbaar.

    * De ingebouwde actie, [Azure functions-een Azure-functie kiezen](logic-apps-azure-functions.md) is nu **Azure function Operations-een Azure-functie aanroepen**. Deze actie werkt momenteel alleen voor functies die zijn gemaakt op basis van de **http-trigger** sjabloon.

      In de Azure Portal kunt u een HTTP-trigger functie selecteren, waar u toegang hebt door een verbinding te maken via de gebruikers ervaring. Als u de JSON-definitie van de functie actie in de code weergave of de **workflow.jsin** het bestand inspecteert, verwijst de actie naar de functie met behulp van een `connectionName` verwijzing. Deze versie bevat een samen vatting van de gegevens van de functie als een verbinding, die u kunt vinden in deconnections.jsvan het project **in** het bestand, wat beschikbaar is nadat u een verbinding hebt gemaakt.

      > [!NOTE]
      > In de versie met één Tenant ondersteunt de functie actie alleen verificatie van de query-teken reeks. Azure Logic Apps Preview haalt de standaard sleutel van de functie op bij het maken van de verbinding, slaat die sleutel op in de instellingen van uw app en gebruikt de sleutel voor verificatie bij het aanroepen van de functie.
      >
      > Net als bij de multi tenant versie, als u deze sleutel verlengt, bijvoorbeeld door de Azure Functions ervaring in de portal, werkt de functie actie niet meer vanwege de ongeldige sleutel. U kunt dit probleem oplossen door de verbinding opnieuw tot stand te brengen met de functie die u wilt aanroepen of de instellingen van uw app bij te werken met de nieuwe sleutel.

    * De ingebouwde actie, de [inline code-execute Java script-code](logic-apps-add-run-inline-code.md) is nu **inline code bewerkingen-online java script uitvoeren**.

      * Acties voor inline code bewerkingen hebben geen integratie account meer nodig.

      * Voor macOS en Linux worden **inline code bewerkingen** nu ondersteund wanneer u de extensie Azure Logic apps (preview) in Visual Studio code gebruikt.

      * U hoeft de logische app niet meer opnieuw op te starten als u wijzigingen aanbrengt in een actie voor **inline code bewerkingen** .

      * Acties voor **inline code bewerkingen** hebben [bijgewerkte limieten](logic-apps-overview-preview.md#inline-code-limits).

    * Sommige [ingebouwde B2B-triggers en-acties voor integratie accounts](../connectors/apis-list.md#integration-account-connectors) zijn niet beschikbaar, bijvoorbeeld de acties voor **platte bestands** codering en decoderen.

    * De ingebouwde actie, [Azure Logic apps: Kies een logische app-werk](logic-apps-http-endpoint.md) stroom is nu **werk stroom bewerkingen: een werk stroom in deze werk stroom-app aanroepen**.

* [Aangepaste connectors](../connectors/apis-list.md#custom-apis-and-connectors) worden momenteel niet ondersteund voor preview.

* **Beschik baarheid hosting abonnement**: ongeacht of u het resource type voor de logische app met één Tenant **(preview)** in de Azure portal of implementeren vanuit Visual Studio code maakt, kunt u alleen het Premium-of app service-hosting abonnement in azure gebruiken. Abonnementen voor het hosten van verbruik zijn niet beschikbaar en worden niet ondersteund voor de implementatie van dit bron type. U kunt implementeren vanuit Visual Studio code naar een docker-container, maar niet naar een [integratie service omgeving (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

* **Fout opsporing in Visual Studio code voor onderbrekings** punten: Hoewel u onderbrekings punten kunt toevoegen en gebruiken in het **workflow.js** bestand voor een werk stroom, worden onderbrekings punten alleen ondersteund voor acties die op dit moment niet worden uitgevoerd. Zie [stateful en stateless werk stromen maken in Visual Studio code](create-stateful-stateless-workflows-visual-studio-code.md#manage-breakpoints)voor meer informatie.

* **Zoom besturings element**: het zoom besturings element is momenteel niet beschikbaar in de ontwerp functie.

* **Trigger geschiedenis en uitvoerings geschiedenis**: voor het resource type **logische app (preview-versie)** , trigger geschiedenis en uitvoerings geschiedenis in de Azure portal wordt weer gegeven op werk stroom niveau, niet op het niveau van de logische app. Voer de volgende stappen uit om deze historische gegevens te vinden:

   * Als u de uitvoerings geschiedenis wilt weer geven, opent u de werk stroom in uw logische app. Selecteer in het menu werk stroom onder **ontwikkelaar** de optie **monitor**.

   * Als u de trigger geschiedenis wilt bekijken, opent u de werk stroom in uw logische app. Selecteer in het menu werk stroom onder **ontwikkelaar** **trigger GESCHIEDENISS**.

<a name="firewall-permissions"></a>

## <a name="permit-traffic-in-strict-network-and-firewall-scenarios"></a>Verkeer in strikte netwerk-en firewall scenario's toestaan

Als uw omgeving strikte netwerk vereisten of firewalls heeft die verkeer beperken, moet u toegang toestaan voor trigger-of actie verbindingen in uw logische app-werk stromen.

Als u de FQDN-namen (Fully Qualified Domain names) voor deze verbindingen wilt vinden, raadpleegt u de bijbehorende secties in deze onderwerpen:

* [Firewall machtigingen voor single tenant Logic apps-Visual Studio code](create-stateful-stateless-workflows-visual-studio-code.md#firewall-setup)
* [Firewall machtigingen voor logische apps met één Tenant-Azure Portal](create-stateful-stateless-workflows-azure-portal.md#firewall-setup)

<a name="limits"></a>

## <a name="updated-limits"></a>Bijgewerkte limieten

Hoewel veel limieten voor Azure Logic Apps Preview hetzelfde blijven als de [limieten voor multi tenant Azure Logic apps](logic-apps-limits-and-config.md), zijn deze limieten gewijzigd voor Azure Logic Apps Preview.

<a name="http-timeout-limits"></a>

### <a name="http-timeout-limits"></a>HTTP-time-outlimieten

Voor een enkele inkomende oproep of een uitgaande oproep is de time-outlimiet 230 seconden (3,9 minuten) voor deze triggers en acties:

* Uitgaande aanvraag: HTTP-trigger, HTTP-actie
* Binnenkomende aanvraag: aanvraag trigger, HTTP-webhook-trigger, HTTP-webhook-actie

In vergelijking zijn dit de time-outlimieten voor deze triggers en acties in andere omgevingen waarin Logic apps en de werk stromen worden uitgevoerd:

* Multi tenant-Azure Logic Apps: 120 seconden (2 minuten)
* Integratie service omgeving: 240 seconden (4 minuten)

Zie [http-limieten](logic-apps-limits-and-config.md#http-limits)voor meer informatie.

<a name="managed-connector-limits"></a>

### <a name="managed-connectors"></a>Beheerde connectors

Beheerde connectors zijn beperkt tot 50 aanvragen per minuut per verbinding. Zie voor het oplossen van problemen met de verbindings beperking [afhandelings problemen (429-"te veel aanvragen") in azure Logic apps](handle-throttling-problems-429-errors.md#connector-throttling).

<a name="inline-code-limits"></a>

### <a name="inline-code-operations-execute-javascript-code"></a>Inline code bewerkingen (Java script-code uitvoeren)

Voor de definitie van een enkele logische app, de actie voor inline code bewerkingen, [**Java script-code uitvoeren**](logic-apps-add-run-inline-code.md), zijn de volgende bijgewerkte limieten:

* Het maximum aantal code tekens neemt toe van 1.024 tekens tot 100.000 tekens.

* De maximale duur voor het uitvoeren van code neemt toe van vijf seconden tot 15 seconden.

Zie [definitie limieten van Logic apps](logic-apps-limits-and-config.md#definition-limits)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Stateful en stateless werk stromen maken in de Azure Portal](create-stateful-stateless-workflows-azure-portal.md)
* [Stateful en stateless werk stromen maken in Visual Studio code](create-stateful-stateless-workflows-visual-studio-code.md)
* [Logic Apps pagina met bekende problemen met de open bare preview-versie in GitHub](https://github.com/Azure/logicapps/blob/master/articles/logic-apps-public-preview-known-issues.md)

Daarnaast willen we u graag over uw ervaringen horen met Azure Logic Apps Preview.

* Voor fouten of problemen [maakt u uw problemen in github](https://github.com/Azure/logicapps/issues).
* Voor vragen, aanvragen, opmerkingen en andere feedback [gebruikt u dit feedback formulier](https://aka.ms/lafeedback).
