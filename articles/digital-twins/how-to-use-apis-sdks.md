---
title: De Azure Digital Twins-API's en -SDK's gebruiken
titleSuffix: Azure Digital Twins
description: Zie werken met de Azure Digital Apparaatdubbels Api's, inclusief via SDK.
author: baanders
ms.author: baanders
ms.date: 06/04/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 4a2667e4876682a6b0aa6d6b7a8cf67eaee376cc
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683664"
---
# <a name="use-the-azure-digital-twins-apis-and-sdks"></a>De Azure Digital Twins-API's en -SDK's gebruiken

Azure Digital Apparaatdubbels wordt geleverd met beide **Control-api's** en **datacenter api's** voor het beheren van uw exemplaar en de bijbehorende elementen. 
* De Api's van het besturings element zijn [Azure Resource Manager (arm)](../azure-resource-manager/management/overview.md) -api's en bedekken bron beheer bewerkingen, zoals het maken en verwijderen van uw exemplaar. 
* De data vlak-Api's zijn Azure Digital Apparaatdubbels Api's en worden gebruikt voor gegevens beheer bewerkingen, zoals het beheren van modellen, apparaatdubbels en de grafiek.

Dit artikel geeft een overzicht van de beschik bare Api's en de methoden om ermee te werken. U kunt de REST-Api's rechtstreeks met de bijbehorende Swaggers gebruiken (via een hulp programma zoals [postman](how-to-use-postman.md)) of via een SDK.

## <a name="overview-control-plane-apis"></a>Overzicht: Controling-Api's

De besturings vlak-Api's zijn [arm](../azure-resource-manager/management/overview.md) -api's die worden gebruikt voor het beheren van uw Azure Digital apparaatdubbels-exemplaar als geheel, zodat deze bewerkingen kunnen uitvoeren zoals het maken of verwijderen van uw hele exemplaar. U kunt deze ook gebruiken om eind punten te maken en te verwijderen.

De meest recente versie van de Control vlak-API is _**2020-12-01**_.

De Control-Api's gebruiken:
* U kunt de Api's rechtstreeks aanroepen door te verwijzen naar de meest recente Swagger-map in het [besturings vlak Swagger opslag plaats](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/resource-manager/Microsoft.DigitalTwins/stable). Deze map bevat ook een map met voor beelden waarin het gebruik wordt weer gegeven.
* U kunt momenteel toegang krijgen tot Sdk's voor besturings-Api's in...
  - [**.Net (C#)**](https://www.nuget.org/packages/Microsoft.Azure.Management.DigitalTwins/) ([referentie [automatisch gegenereerd]](/dotnet/api/overview/azure/digitaltwins/management?view=azure-dotnet&preserve-view=true)) ([bron](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Microsoft.Azure.Management.DigitalTwins))
  - [**Java**](https://search.maven.org/search?q=a:azure-mgmt-digitaltwins) ([referentie [automatisch gegenereerd]](/java/api/overview/azure/digitaltwins?view=azure-java-stable&preserve-view=true)) ([bron](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins))
  - [**Java script**](https://www.npmjs.com/package/@azure/arm-digitaltwins) ([bron](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/arm-digitaltwins))
  - [**Python**](https://pypi.org/project/azure-mgmt-digitaltwins/) ([bron](https://github.com/Azure/azure-sdk-for-python/tree/release/v3/sdk/digitaltwins/azure-mgmt-digitaltwins))
  - [**Go**](https://godoc.org/github.com/Azure/azure-sdk-for-go/services/digitaltwins/mgmt/2020-10-31/digitaltwins) ([bron](https://github.com/Azure/azure-sdk-for-go/tree/master/services/digitaltwins/mgmt/2020-10-31/digitaltwins))

U kunt ook beheer vlak-Api's uitoefenen door interactie met Azure Digital Apparaatdubbels via de [Azure Portal](https://portal.azure.com) en [cli](how-to-use-cli.md).

## <a name="overview-data-plane-apis"></a>Overzicht: gegevens vlak-Api's

De data vlak-Api's zijn de Azure Digital Apparaatdubbels-Api's die worden gebruikt voor het beheren van de elementen in uw Azure Digital Apparaatdubbels-exemplaar. Ze omvatten bewerkingen zoals het maken van routes, het uploaden van modellen, het maken van relaties en het beheren van apparaatdubbels. Ze kunnen breed worden onderverdeeld in de volgende categorieën:
* **DigitalTwinModels** : de categorie DigitalTwinModels bevat api's voor het beheren van de [modellen](concepts-models.md) in een Azure Digital apparaatdubbels-exemplaar. Beheer activiteiten zijn onder andere het uploaden, valideren, ophalen en verwijderen van modellen die zijn gemaakt in DTDL.
* **DigitalTwins** : de categorie DigitalTwins bevat de api's waarmee ontwikkel aars [digitale apparaatdubbels](concepts-twins-graph.md) en hun relaties kunnen maken, wijzigen en verwijderen in een Azure Digital apparaatdubbels-exemplaar.
* **Query** : de query categorie stelt ontwikkel aars in staat om [sets van digitale apparaatdubbels te vinden in de dubbele grafiek over de](how-to-query-graph.md) relaties.
* **Gebeurtenis routes** : de categorie gebeurtenis routes bevat api's voor het door [sturen van gegevens](concepts-route-events.md), via het systeem en naar downstream-Services.

De meest recente versie van het data vlak API is _**2020-10-31**_.

De data-vlak-Api's gebruiken:
* U kunt de Api's rechtstreeks aanroepen, door...
   - verwijzen naar de meest recente Swagger-map in het [Data vlak Swagger opslag plaats](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/digitaltwins/data-plane/Microsoft.DigitalTwins). Deze map bevat ook een map met voor beelden waarin het gebruik wordt weer gegeven. 
   - de [API-referentie documentatie](/rest/api/azure-digitaltwins/)weer geven.
* U kunt de **.NET-SDK (C#)** gebruiken. De .NET SDK gebruiken...
   - u kunt het pakket bekijken en toevoegen vanuit NuGet: [Azure. DigitalTwins. core](https://www.nuget.org/packages/Azure.DigitalTwins.Core). 
   - u kunt de [SDK-referentie documentatie](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true)raadplegen.
   - u vindt de SDK-bron, met inbegrip van een map met voor beelden, in GitHub: [Azure IOT Digital apparaatdubbels-client bibliotheek voor .net](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core). 
   - u kunt gedetailleerde informatie en voor beelden van gebruik bekijken door door te gaan naar de sectie [*.net (C#) SDK (Data-vlieg tuig)*](#net-c-sdk-data-plane) van dit artikel.
* U kunt de **Java-SDK** gebruiken. De Java-SDK gebruiken...
   - u kunt het pakket weer geven en installeren vanuit maven: [`com.azure:azure-digitaltwins-core`](https://search.maven.org/artifact/com.azure/azure-digitaltwins-core/1.0.0/jar)
   - u kunt de [SDK-referentie documentatie](/java/api/overview/azure/digitaltwins/client?preserve-view=true&view=azure-java-stable) bekijken
   - de SDK-bron is te vinden in GitHub: [Azure IOT Digital apparaatdubbels-client bibliotheek voor Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* U kunt de **Java script-SDK** gebruiken. De Java script-SDK gebruiken...
   - u kunt het pakket weer geven en installeren vanuit NPM: [Azure Azure Digital Apparaatdubbels core client library voor Java script](https://www.npmjs.com/package/@azure/digital-twins-core).
   - u kunt de [SDK-referentie documentatie](/javascript/api/@azure/digital-twins-core/?branch=master&view=azure-node-latest&preserve-view=true)raadplegen.
   - de SDK-bron is te vinden in GitHub: [Azure Azure Digital Apparaatdubbels core client library voor Java script](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/digitaltwins/digital-twins-core)
* U kunt de **python-SDK** gebruiken. De python-SDK gebruiken...
   - u kunt het pakket weer geven en installeren vanuit PyPi: [Azure Azure Digital Apparaatdubbels core client library voor python](https://pypi.org/project/azure-digitaltwins-core/).
   - u kunt de [SDK-referentie documentatie](/python/api/azure-digitaltwins-core/azure.digitaltwins.core?view=azure-python&preserve-view=true)raadplegen.
   - de SDK-bron is te vinden in GitHub: [Azure Azure Digital Apparaatdubbels core client library voor python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/digitaltwins/azure-digitaltwins-core)
* U kunt een SDK voor een andere taal genereren met behulp van auto rest. Volg de instructies in [*How to: aangepaste sdk's voor Azure Digital Apparaatdubbels maken met auto rest*](how-to-create-custom-sdks.md).

U kunt ook datum vlak-Api's uitoefenen door interactie met Azure Digital Apparaatdubbels via de [cli](how-to-use-cli.md).

## <a name="net-c-sdk-data-plane"></a>.NET (C#) SDK (Data-vlak)

De Azure Digital Apparaatdubbels .NET (C#) SDK maakt deel uit van de Azure SDK voor .NET. Het is open source en is gebaseerd op de Azure Digital Apparaatdubbels data vlak-Api's.

> [!NOTE]
> Zie algemene [ontwerp principes voor Azure sdk's](https://azure.github.io/azure-sdk/general_introduction.html) en de specifieke [.net-ontwerp richtlijnen](https://azure.github.io/azure-sdk/dotnet_introduction.html)voor meer informatie over het ontwerp van de SDK.

Als u de SDK wilt gebruiken, voegt u het NuGet-pakket **Azure. DigitalTwins. core** toe aan uw project. U hebt ook de nieuwste versie van het **Azure. Identity** -pakket nodig. In Visual Studio kunt u deze pakketten toevoegen met behulp van de NuGet-pakket Manager (toegankelijk via *Hulpprogram ma's > NuGet package manager > NuGet-pakketten voor oplossing beheren*). U kunt ook het .NET-opdracht regel programma gebruiken met de opdrachten in de NuGet-pakket koppelingen hieronder om deze toe te voegen aan uw project:
* [**Azure.DigitalTwins.Core**](https://www.nuget.org/packages/Azure.DigitalTwins.Core). Dit is het pakket voor de [Azure Digital Twins SDK voor .NET](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true). 
* [**Azure.Identity**](https://www.nuget.org/packages/Azure.Identity). Deze bibliotheek biedt hulpprogramma's voor de verificatie bij Azure.

Zie de [*zelf studie: een client-app coderen*](tutorial-code.md)voor een gedetailleerde procedure voor het gebruik van de api's in de praktijk. 

### <a name="net-sdk-usage-examples"></a>Gebruiks voorbeelden van .NET SDK

Hier volgen enkele code voorbeelden die het gebruik van de .NET SDK illustreren.

Verificatie voor de service:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_basic":::

[!INCLUDE [Azure Digital Twins: local credentials note](../../includes/digital-twins-local-credentials-note.md)] 

Een model uploaden:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="CreateModel":::

Lijst met modellen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/model_operations.cs" id="GetModels":::

Apparaatdubbels maken:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

Query's uitvoeren op apparaatdubbels en de resultaten door lopen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

Raadpleeg de [*zelf studie: een client-app coderen*](tutorial-code.md) voor een overzicht van deze app-voorbeeld code. 

U kunt ook aanvullende voor beelden vinden in de [github-opslag plaats voor de .net (C#) SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/digitaltwins/Azure.DigitalTwins.Core/samples).

#### <a name="serialization-helpers"></a>Helpers voor serialisatie

Helpers voor serialisatie zijn hulp functies die beschikbaar zijn in de SDK voor het snel maken of deserialiseren van dubbele gegevens voor toegang tot basis informatie. Aangezien de kern-SDK-methoden dubbele gegevens standaard als JSON retour neren, kan het handig zijn om deze hulp klassen te gebruiken om de dubbele gegevens verder te verbreken.

De beschik bare Help klassen zijn:
* `BasicDigitalTwin`: Hiermee worden de belangrijkste gegevens van een digitale dubbele
* `BasicRelationship`: Hiermee worden de kern gegevens van een relatie aangegeven
* `UpdateOperationUtility`: Hiermee wordt informatie over de JSON-patch gebruikt in Update aanroepen
* `WriteableProperty`: Vertegenwoordigt meta gegevens van eigenschap

##### <a name="deserialize-a-digital-twin"></a>Een digitale dubbele

U kunt dubbele gegevens altijd deserialiseren met de JSON-bibliotheek van uw keuze, zoals `System.Test.Json` of `Newtonsoft.Json` . Voor eenvoudige toegang tot een dubbele manier is de Help-klasse zo wat handiger.

De `BasicDigitalTwin` helperklasse biedt u ook toegang tot de eigenschappen die zijn gedefinieerd op de dubbele, via een `Dictionary<string, object>` . Als u de eigenschappen van de dubbele wilt weer geven, kunt u het volgende gebruiken:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="GetTwin":::

##### <a name="create-a-digital-twin"></a>Een digitale dubbele

U kunt met behulp `BasicDigitalTwin` van de-klasse gegevens voorbereiden voor het maken van een dubbele instantie:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_sample.cs" id="CreateTwin_withHelper":::

De bovenstaande code is gelijk aan de volgende ' hand matig ' variant:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

##### <a name="deserialize-a-relationship"></a>Een relatie deserialiseren

U kunt relatie gegevens altijd deserialiseren naar het type van uw keuze. Gebruik het type voor eenvoudige toegang tot een relatie `BasicRelationship` .

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_sample.cs" id="GetRelationshipsCall":::

De `BasicRelationship` helperklasse biedt u ook toegang tot eigenschappen die zijn gedefinieerd voor de relatie, via een `IDictionary<string, object>` . Als u eigenschappen wilt weer geven, kunt u het volgende gebruiken:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="ListRelationshipProperties":::

##### <a name="create-a-relationship"></a>Relatie maken

Met behulp van de `BasicRelationship` -klasse kunt u ook gegevens voorbereiden voor het maken van relaties op een dubbele instantie:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_short":::

##### <a name="create-a-patch-for-twin-update"></a>Een patch maken voor dubbele update

Update-aanroepen voor apparaatdubbels en relaties gebruiken [JSON-patch](http://jsonpatch.com/) structuur. Voor het maken van lijsten met JSON-patch bewerkingen kunt u de `JsonPatchDocument` zoals hieronder wordt weer gegeven.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="UpdateTwin":::

## <a name="general-apisdk-usage-notes"></a>Algemene opmerkingen over API/SDK-gebruik

> [!NOTE]
> Azure Digital Apparaatdubbels ondersteunt momenteel geen **CORS (cross-Origin Resource Sharing)**. Zie de sectie [*Cross-Origin Resource Sharing (CORS)*](concepts-security.md#cross-origin-resource-sharing-cors) van *concepten: beveiliging voor Azure Digital apparaatdubbels-oplossingen* voor meer informatie over de impact-en oplossings strategieën.

De volgende lijst bevat aanvullende details en algemene richt lijnen voor het gebruik van de Api's en Sdk's.

* U kunt een HTTP REST-test programma zoals postman gebruiken om directe aanroepen naar de Azure Digital Apparaatdubbels-Api's uit te voeren. Zie [*How-to: Requests with postman*](how-to-use-postman.md)(Engelstalig) voor meer informatie over dit proces.
* Als u de SDK wilt gebruiken, maakt u een instantie van de `DigitalTwinsClient` klasse. De constructor vereist referenties die kunnen worden verkregen met diverse verificatie methoden in het `Azure.Identity` pakket. `Azure.Identity`Zie de documentatie van de [naam ruimte](/dotnet/api/azure.identity?preserve-view=true&view=azure-dotnet)voor meer informatie. 
* Het is `InteractiveBrowserCredential` handig om aan de slag te gaan, maar er zijn verschillende andere opties, waaronder referenties voor [beheerde identiteit](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet), die u waarschijnlijk gebruikt voor het verifiëren van [Azure functions die zijn ingesteld met MSI](../app-service/overview-managed-identity.md?tabs=dotnet) op Azure Digital apparaatdubbels. `InteractiveBrowserCredential`Zie de documentatie van de [klasse](/dotnet/api/azure.identity.interactivebrowsercredential?preserve-view=true&view=azure-dotnet)voor meer informatie.
* Alle service-API-aanroepen worden weer gegeven als lidfuncties op de `DigitalTwinsClient` klasse.
* Alle service functies bestaan in synchrone en asynchrone versies.
* Alle service functies genereren een uitzonde ring voor elke retour status van 400 of hoger. Zorg ervoor dat u aanroepen in een sectie inpakt `try` en ten minste hebt ondervangen `RequestFailedExceptions` . Zie [hier](/dotnet/api/azure.requestfailedexception?preserve-view=true&view=azure-dotnet)voor meer informatie over dit type uitzonde ring.
* De meeste service methoden retour neren `Response<T>` of ( `Task<Response<T>>` voor de asynchrone aanroepen), waarbij `T` de klasse van het retour object voor de service aanroep is. De [`Response`](/dotnet/api/azure.response-1?preserve-view=true&view=azure-dotnet) klasse bevat de retour waarde van de service en geeft retour waarden in het `Value` veld weer.  
* Service methoden met geretourneerde resultaten `Pageable<T>` of `AsyncPageable<T>` als resultaat. Zie hier voor meer informatie over de `Pageable<T>` - [](/dotnet/api/azure.pageable-1?preserve-view=true&view=azure-dotnet)klasse `AsyncPageable<T>` . Zie [hier](/dotnet/api/azure.asyncpageable-1?preserve-view=true&view=azure-dotnet)voor meer informatie.
* U kunt met behulp van een lus de resultaten van de pagina door lopen `await foreach` . Zie [hier](/archive/msdn-magazine/2019/november/csharp-iterating-with-async-enumerables-in-csharp-8)voor meer informatie over dit proces.
* De onderliggende SDK is `Azure.Core` . Raadpleeg de [documentatie van Azure namespace](/dotnet/api/azure?preserve-view=true&view=azure-dotnet) voor naslag informatie over de SDK-infra structuur en typen.

Service methoden retour neren waar mogelijk sterk getypeerde objecten. Omdat Azure Digital Apparaatdubbels echter is gebaseerd op modellen die zijn geconfigureerd door de gebruiker tijdens runtime (via DTDL-modellen die zijn geüpload naar de service), nemen veel service-Api's dubbele gegevens in JSON-indeling en retour neer.

## <a name="monitor-api-metrics"></a>Metrische gegevens van de API bewaken

API-metrische gegevens, zoals aanvragen, latentie en fout frequentie, kunnen worden weer gegeven in de [Azure Portal](https://portal.azure.com/). 

Zoek op de start pagina van de portal naar uw Azure Digital Apparaatdubbels-exemplaar om de details ervan op te halen. Selecteer de optie **metrische gegevens** in het menu van het Azure Digital apparaatdubbels-exemplaar om de pagina *metrische gegevens* weer te geven.

:::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Scherm afbeelding van de pagina met metrische gegevens voor Azure Digital Apparaatdubbels":::

Hier kunt u de metrische gegevens voor uw exemplaar bekijken en aangepaste weer gaven maken.

## <a name="next-steps"></a>Volgende stappen

Zie direct aanvragen maken voor de Api's met behulp van Postman:
* [*Instructies: aanvragen indienen met de Postman*](how-to-use-postman.md)

Of oefen met het gebruik van de .NET-SDK door een client-app te maken met deze zelf studie:
* [*Zelfstudie: Een client-app coderen*](tutorial-code.md)