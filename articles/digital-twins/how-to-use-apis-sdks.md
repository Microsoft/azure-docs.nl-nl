---
title: De Azure Digital Twins-API's en -SDK's gebruiken
titleSuffix: Azure Digital Twins
description: Zie hoe u werkt met de Azure Digital Twins API's, inclusief via SDK.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: efa5061a49978ed5e7766c0e7bf9b56a1e73cf5d
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389754"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>De Azure Digital Twins-API's en -SDK's gebruiken

Azure Digital Twins is uitgerust met api's voor besturingsvlakken en **API's** voor gegevensvlak voor het beheren van uw exemplaar en de elementen ervan.  
* De API's van het [besturingsvlak zijn Azure Resource Manager (ARM) API's](../azure-resource-manager/management/overview.md) en omvatten bewerkingen voor resourcebeheer, zoals het maken en verwijderen van uw exemplaar. 
* De gegevensvlak-API's zijn Azure Digital Twins API's en worden gebruikt voor gegevensbeheerbewerkingen zoals het beheren van modellen, tweelingen en de grafiek.

In dit artikel vindt u een overzicht van de beschikbare API's en de methoden voor interactie met de API's. U kunt de REST API's rechtstreeks gebruiken met hun bijbehorende S associatedgers (via een hulpprogramma zoals [Postman)](how-to-use-postman.md)of via een SDK.

## <a name="overview-control-plane-apis"></a>Overzicht: API's voor besturingsvlak

De besturingsvlak-API's zijn ARM-API's die worden gebruikt voor het beheren van uw Azure Digital Twins-exemplaar als geheel, zodat ze bewerkingen behandelen zoals het maken of verwijderen van uw hele exemplaar. [](../azure-resource-manager/management/overview.md) U gebruikt deze ook om eindpunten te maken en te verwijderen.

De meest recente versie van de besturingsvlak-API is _**2020-12-01.**_

De besturingsvlak-API's gebruiken:
* U kunt de API's rechtstreeks aanroepen door te verwijzen naar de meest recente Swagger-map in de [Swagger-repo](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable)van het besturingsvlak. Deze map bevat ook een map met voorbeelden waarin het gebruik wordt weergeven.
* U hebt momenteel toegang tot SDK's voor het beheer van API's in...
  - [**.NET (C#)**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([verwijzing [automatisch gegenereerd]](/dotnet/api/overview/azure/digitaltwins/management)) ([bron](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [**Java**](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins) ([verwijzing [automatisch gegenereerd]](/java/api/overview/azure/digitaltwins)) ([bron](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins))
  - [**JavaScript**](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([bron](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) ([bron](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [**Go**](https://pkg.go.dev/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt) ([bron](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt))

U kunt ook de API's van het besturingsvlak oefenen door te communiceren met Azure Digital Twins via [de Azure Portal](https://portal.azure.com) en [CLI.](how-to-use-cli.md)

## <a name="overview-data-plane-apis"></a>Overzicht: API's voor gegevensvlak

De gegevensvlak-API's zijn de Azure Digital Twins API's die worden gebruikt om de elementen in uw Azure Digital Twins beheren. Ze omvatten bewerkingen zoals het maken van routes, het uploaden van modellen, het maken van relaties en het beheren van tweelingen. Ze kunnen breed worden onderverdeeld in de volgende categorieën:
* **DigitalTwinModels:** de categorie DigitalTwinModels bevat API's voor het beheren van de modellen [in](concepts-models.md) een Azure Digital Twins exemplaar. Beheeractiviteiten omvatten het uploaden, valideren, ophalen en verwijderen van modellen die zijn geschreven in DTDL.
* **DigitalTwins:** de categorie DigitalTwins bevat de API's die [](concepts-twins-graph.md) ontwikkelaars digitale tweelingen en hun relaties in een Azure Digital Twins maken, wijzigen en verwijderen.
* **Query:** met de categorie Query kunnen ontwikkelaars [sets van digitale tweelingen vinden in de tweelinggrafiek tussen](how-to-query-graph.md) relaties.
* **Gebeurtenisroutes:** de categorie Gebeurtenisroutes bevat API's om [gegevens](concepts-route-events.md)door te geven, via het systeem en naar downstreamservices.

De meest recente API-versie van het gegevensvlak is _**2020-10-31.**_

De gegevensvlak-API's gebruiken:
* U kunt de API's rechtstreeks aanroepen, door...
   - verwijst naar de meest recente Swagger-map in de [Swagger-gegevensvlak-repo.](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins) Deze map bevat ook een map met voorbeelden waarin het gebruik wordt weergeven. 
   - bekijk de [API-referentiedocumentatie](/rest/api/azure-digitaltwins/).
* U kunt de **.NET SDK (C#) gebruiken.** De .NET SDK gebruiken...
   - U kunt het pakket weergeven en toevoegen vanuit NuGet: [Azure.DigitalTwins.Core.](https://www.nuget.org/packages/Azure.DigitalTwins.Core) 
   - U kunt de [SDK-referentiedocumentatie bekijken.](/dotnet/api/overview/azure/digitaltwins/client)
   - U vindt de SDK-bron, inclusief een map met voorbeelden, in GitHub: [Azure IoT Digital Twins clientbibliotheek voor .NET.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core) 
   - U kunt gedetailleerde informatie en gebruiksvoorbeelden bekijken door verder te gaan naar de [*sectie .NET (C#) SDK (gegevensvlak)*](#net-c-sdk-data-plane) van dit artikel.
* U kunt de **Java SDK gebruiken.** De Java SDK gebruiken...
   - U kunt het pakket bekijken en installeren vanuit Maven: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - U kunt de [SDK-referentiedocumentatie bekijken](/java/api/overview/azure/digitaltwins/client)
   - U vindt de SDK-bron in GitHub: [Azure IoT Digital Twins clientbibliotheek voor Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* U kunt de **JavaScript SDK gebruiken.** De JavaScript SDK gebruiken...
   - U kunt het pakket bekijken en installeren vanuit npm: [Azure Azure Digital Twins Core-clientbibliotheek voor JavaScript.](https://www.npmjs.com/package/@azure/digital-twins-core)
   - U kunt de [SDK-referentiedocumentatie bekijken.](/javascript/api/@azure/digital-twins-core/)
   - U vindt de SDK-bron in GitHub: [Azure Azure Digital Twins Core-clientbibliotheek voor JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* U kunt de **Python SDK gebruiken.** De Python-SDK gebruiken...
   - U kunt het pakket weergeven en installeren vanuit PyPi: [Azure Azure Digital Twins Core-clientbibliotheek voor Python.](https://pypi.org/project/azure-digitaltwins-core/)
   - U kunt de [SDK-referentiedocumentatie bekijken.](/python/api/azure-digitaltwins-core/azure.digitaltwins.core)
   - U vindt de SDK-bron in GitHub: [Azure Azure Digital Twins Core-clientbibliotheek voor Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* U kunt een SDK genereren voor een andere taal met behulp van AutoRest. Volg de instructies in [*Instructies: Aangepaste SDK's maken voor Azure Digital Twins met AutoRest.*](how-to-create-custom-sdks.md)

U kunt ook oefenen met API's voor datumvlakken door te communiceren met Azure Digital Twins via de [CLI](how-to-use-cli.md).

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (gegevensvlak)

De Azure Digital Twins .NET (C#) SDK maakt deel uit van de Azure SDK voor .NET. Het is open source en is gebaseerd op de api'Azure Digital Twins gegevensvlak.

> [!NOTE]
> Zie de algemene ontwerpprincipes voor Azure SDK's en de specifieke [.NET-ontwerprichtlijnen](https://azure.github.io/azure-sdk/dotnet_introduction.html)voor meer informatie over [SDK-ontwerp.](https://azure.github.io/azure-sdk/general_introduction.html)

Als u de SDK wilt gebruiken, moet u het NuGet-pakket **Azure.DigitalTwins.Core opnemen** in uw project. U hebt ook de nieuwste versie van het **Azure.Identity-pakket** nodig. In Visual Studio kunt u deze pakketten toevoegen met behulp van de NuGet-Pakketbeheer (toegankelijk via Hulpprogramma's *> NuGet Pakketbeheer > NuGet-pakketten* voor oplossing beheren). U kunt ook het .NET-opdrachtregelprogramma gebruiken met de opdrachten in de onderstaande NuGet-pakketkoppelingen om deze toe te voegen aan uw project:
* [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). Dit is het pakket voor de [Azure Digital Twins SDK voor .NET](/dotnet/api/overview/azure/digitaltwins/client). 
* [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity). Deze bibliotheek biedt hulpprogramma's voor de verificatie bij Azure.

Zie [*Zelfstudie: Een client-app*](tutorial-code.md)codeer voor een gedetailleerd overzicht van het gebruik van de API's in de praktijk. 

### <a name="net-sdk-usage-examples"></a>.NET SDK-gebruiksvoorbeelden

Hier zijn enkele codevoorbeelden die het gebruik van de .NET SDK illustreren.

Verifiëren bij de service:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_basic":::

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

Een model uploaden:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Modellen op een lijst zetten:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

Tweelingen maken:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

Query-tweelingen en doorloop de resultaten:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

Zie de [*Zelfstudie: Een client-app coden*](tutorial-code.md) voor een walk-through van deze voorbeeld-app-code. 

U kunt ook aanvullende voorbeelden vinden in de [GitHub-opslagplaats voor de .NET -SDK (C#).](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples)

#### <a name="serialization-helpers"></a>Serialisatie helpers

Serialisatiehulpfuncties zijn helperfuncties die beschikbaar zijn in de SDK voor het snel maken of deserialiseren van tweelinggegevens voor toegang tot basisinformatie. Omdat de belangrijkste SDK-methoden standaard dubbele gegevens retourneren als JSON, kan het handig zijn om deze helperklassen te gebruiken om de dubbele gegevens verder op te delen.

De beschikbare helperklassen zijn:
* `BasicDigitalTwin`: Geeft de kerngegevens van een digitale tweeling weer in het algemeen
* `BasicDigitalTwinComponent`: Geeft een onderdeel aan in de `Contents` eigenschappen van een `BasicDigitalTwin`
* `BasicRelationship`: Geeft de kerngegevens van een relatie algemeen weer
* `DigitalTwinsJsonPropertyName`: Bevat de tekenreeksconstanten voor gebruik in JSON-serialisatie en -deserialisatie voor aangepaste digital twin-typen

##### <a name="deserialize-a-digital-twin"></a>Een digitale tweeling deserialiseren

U kunt tweelinggegevens altijd deserialiseren met behulp van de JSON-bibliotheek van uw keuze, zoals `System.Text.Json` of `Newtonsoft.Json` . Voor eenvoudige toegang tot een tweeling kunnen de helperklassen dit handiger maken.

De `BasicDigitalTwin` helperklasse biedt u ook toegang tot eigenschappen die zijn gedefinieerd op de tweeling, via een `Dictionary<string, object>` . Als u de eigenschappen van de tweeling wilt weergeven, kunt u het volgende gebruiken:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin":::

> [!NOTE]
> `BasicDigitalTwin` gebruikt `System.Text.Json` kenmerken. Als u wilt gebruiken met uw DigitalTwinsClient, moet u de client initialiseren met de standaard constructor of, als u de serializer-optie wilt `BasicDigitalTwin` aanpassen, [de JsonObjectSerializer gebruiken.](/dotnet/api/azure.core.serialization.jsonobjectserializer?view=azure-dotnet&preserve-view=true) [](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient?view=azure-dotnet&preserve-view=true)

##### <a name="create-a-digital-twin"></a>Een digitale tweeling maken

Met behulp `BasicDigitalTwin` van de klasse kunt u gegevens voorbereiden voor het maken van een tweeling-exemplaar:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

De bovenstaande code is gelijk aan de volgende handmatige variant:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

##### <a name="deserialize-a-relationship"></a>Een relatie deserialiseren

U kunt relatiegegevens altijd deserialiseren naar een type van uw keuze. Gebruik het type voor basistoegang tot een `BasicRelationship` relatie.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

De `BasicRelationship` helperklasse biedt u ook toegang tot eigenschappen die zijn gedefinieerd in de relatie, via een `IDictionary<string, object>` . Als u eigenschappen wilt weergeven, kunt u het volgende gebruiken:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

##### <a name="create-a-relationship"></a>Relatie maken

Met behulp `BasicRelationship` van de klasse kunt u ook gegevens voorbereiden voor het maken van relaties op een dubbel-exemplaar:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

##### <a name="create-a-patch-for-twin-update"></a>Een patch maken voor een tweelingupdate

Update-aanroepen voor tweelingen en relaties maken gebruik [van JSON](http://jsonpatch.com/) Patch-structuur. Als u lijsten met JSON Patch-bewerkingen wilt maken, kunt u de `JsonPatchDocument` gebruiken zoals hieronder wordt weergegeven.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

## <a name="general-apisdk-usage-notes"></a>Algemene gebruiksnotities voor API/SDK

> [!NOTE]
> Houd er rekening mee Azure Digital Twins ondersteuning biedt voor **Cross-Origin Resource Sharing (CORS)**. Zie de sectie [*Cross-Origin Resource Sharing (CORS)*](concepts-security.md#cross-origin-resource-sharing-cors) van Concepten: Beveiliging voor Azure Digital Twins oplossingen voor meer informatie over de *impact- en oplossingsstrategieën.*

De volgende lijst bevat aanvullende details en algemene richtlijnen voor het gebruik van de API's en SDK's.

* U kunt een HTTP REST-testprogramma zoals Postman gebruiken om directe aanroepen uit te voeren naar Azure Digital Twins API's. Zie Procedure: Aanvragen maken met Postman voor meer informatie [*over dit proces.*](how-to-use-postman.md)
* Als u de SDK wilt gebruiken, maakt u een instantie van de `DigitalTwinsClient` klasse . De constructor vereist referenties die kunnen worden verkregen met verschillende verificatiemethoden in het `Azure.Identity` pakket. Zie de documentatie `Azure.Identity` van de [naamruimte voor meer informatie](/dotnet/api/azure.identity)over . 
* Het kan handig zijn om aan de slag te gaan, maar er zijn verschillende andere opties, waaronder referenties voor beheerde identiteit, die u waarschijnlijk gebruikt voor het verifiëren van Azure-functies die zijn ingesteld met `InteractiveBrowserCredential` [MSI](../app-service/overview-managed-identity.md?tabs=dotnet) op Azure Digital Twins. [](/dotnet/api/azure.identity.interactivebrowsercredential) Zie de `InteractiveBrowserCredential` klassedocumentatie [voor meer informatie over](/dotnet/api/azure.identity.interactivebrowsercredential).
* Voor aanvragen naar Azure Digital Twins API's is een gebruiker of service-principal vereist die deel uitmaakt van dezelfde [Azure Active Directory-tenant](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) waarin de Azure Digital Twins zich bevindt. Aanvragen met toegangstokens van buiten de oorspronkelijke tenant krijgen het foutbericht '404 Sub-Domain not found' (404 Sub-Domain niet gevonden) als u wilt voorkomen dat kwaadde actoren URL's scannen om te ontdekken waar Azure Digital Twins-exemplaren zich kunnen vinden. Deze fout wordt  geretourneerd, zelfs als de gebruiker of service-principal de rol Azure Digital Twins gegevenseigenaar of Azure Digital Twins gegevenslezer heeft gekregen via [Azure AD B2B-samenwerking.](../active-directory/external-identities/what-is-b2b.md)
* Alle service-API-aanroepen worden beschikbaar gemaakt als lidfuncties in de `DigitalTwinsClient` klasse .
* Alle servicefuncties bestaan in synchrone en asynchrone versies.
* Alle servicefuncties geven een uitzondering voor een retourstatus van 400 of hoger. Zorg ervoor dat u aanroepen in een `try` sectie verpakt en ten minste vangt. `RequestFailedExceptions` Zie hier voor meer informatie over dit type [uitzondering.](/dotnet/api/azure.requestfailedexception)
* De meeste servicemethoden retourneren of ( voor `Response<T>` `Task<Response<T>>` de asynchrone aanroepen), waarbij de klasse van `T` het retourobject is voor de service-aanroep. De [`Response`](/dotnet/api/azure.response-1) klasse kapselt het retour van de service in en presenteert retourwaarden in het `Value` veld.  
* Servicemethoden met resultaten met pagina's `Pageable<T>` retourneren of `AsyncPageable<T>` als resultaten. Zie hier voor meer informatie over de klasse . Zie hier voor meer `Pageable<T>` informatie [](/dotnet/api/azure.pageable-1) `AsyncPageable<T>` [over](/dotnet/api/azure.asyncpageable-1).
* U kunt paginade resultaten herhalen met behulp van een `await foreach` lus. Zie hier voor meer informatie over [dit proces.](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8)
* De onderliggende SDK is `Azure.Core` . Zie de [documentatie voor Azure-naamruimte](/dotnet/api/azure) voor naslag over de infrastructuur en typen van de SDK.


Servicemethoden retourneren waar mogelijk sterk getypeerd objecten. Omdat Azure Digital Twins echter is gebaseerd op modellen die tijdens runtime door de gebruiker zijn geconfigureerd (via DTDL-modellen die naar de service zijn geüpload), nemen en retourneren veel service-API's dubbele gegevens in JSON-indeling.

## <a name="monitor-api-metrics"></a>Metrische API-gegevens bewaken

Metrische API-gegevens, zoals aanvragen, latentie en foutpercentage, kunnen worden bekeken in [de Azure Portal.](https://portal.azure.com/) 

Zoek op de startpagina van de portal naar uw Azure Digital Twins om de details ervan op te halen. Selecteer de **optie Metrische** gegevens in Azure Digital Twins menu van het exemplaar om de pagina Metrische gegevens *weer te* geven.

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Schermopname van de pagina met metrische gegevens voor Azure Digital Twins":::

Hier kunt u de metrische gegevens voor uw exemplaar bekijken en aangepaste weergaven maken.

## <a name="next-steps"></a>Volgende stappen

Zie hoe u directe aanvragen naar de API's kunt maken met behulp van Postman:
* [*Hoe kan ik aanvragen doen met Postman?*](how-to-use-postman.md)

U kunt ook oefenen met het gebruik van de .NET SDK door een client-app te maken met deze zelfstudie:
* [*Zelfstudie: Een client-app coderen*](tutorial-code.md)
