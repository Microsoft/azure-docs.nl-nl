---
title: 'Quickstart: Azure Management-clientbibliotheek voor .NET'
description: Ga in deze quickstart aan de slag met de Azure Management-clientbibliotheek voor .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 3/22/2021
ms.author: pafarley
ms.openlocfilehash: 1ecfd58e961e70182bfc2e0cc8eaf77f5053eb77
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879787"
---
[Referentiedocumentatie](/dotnet/api/overview/azure/cognitiveservices/management) | [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices) | [Pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Management.CognitiveServices/) | [Voorbeelden](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Microsoft.Azure.Management.CognitiveServices/tests)

## <a name="c-prerequisites"></a>C#-vereisten

* Een geldig Azure-abonnement - [Maak een gratis abonnement](https://azure.microsoft.com/free/).
* De huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).

[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

Maak een nieuwe .NET Core-toepassing. Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `azure-management-quickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: *program.cs*. 

```console
dotnet new console -n azure-management-quickstart
```

Wijzig uw map in de zojuist gemaakte app-map. U kunt de toepassing maken met:

```console
dotnet build
```

De build-uitvoer mag geen waarschuwingen of fouten bevatten. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>De clientbibliotheek installeren

Installeer in de toepassingsmap de Azure Management-clientbibliotheek voor .NET met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.Management.CognitiveServices
dotnet add package Microsoft.Azure.Management.Fluent
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

Als u de Visual Studio-IDE, gebruikt, is de clientbibliotheek beschikbaar als downloadbaar NuGet-pakket.

### <a name="import-libraries"></a>Bibliotheken importeren

Open *program.cs* en voeg de volgende `using`-instructies toe aan het begin van het bestand:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_using)]

## <a name="authenticate-the-client"></a>De client verifiëren

Voeg de volgende velden toe aan de hoofdmap van *program.cs* en vul hun waarden in met behulp van de service-principal die u hebt gemaakt en uw Azure-accountgegevens.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_constants)]

Gebruik deze waarden vervolgens in uw **Main**-methode om een **CognitiveServicesManagementClient**-object te maken. Dit object is nodig voor al uw Azure Management-bewerkingen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_assigns)]

## <a name="call-management-methods"></a>Methoden voor aanroepbeheer

Voeg de volgende code toe aan uw **Main**-methode om beschikbare resources weer te geven, maak een voorbeeldresource, vermeld uw eigen resources en verwijder vervolgens de voorbeeldresource. U gaat deze methoden in de volgende stappen definiëren.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource-c"></a>Een Cognitive Services-resource (C#) maken

Als u een nieuwe Cognitive Services-resource wilt maken en zich hierop wilt abonneren, gebruikt u de methode **maken**. Deze methode voegt een nieuwe factureerbare resource toe aan de resourcegroep die u doorgeeft. Wanneer u uw nieuwe resource maakt, moet u weten welk soort service u wilt gebruiken, samen met de prijscategorie (of SKU) en een Azure-locatie. Met de volgende methode worden al deze argumenten gebruikt en wordt een resource gemaakt.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Een service en prijscategorie kiezen

Wanneer u een nieuwe resource maakt, moet u weten welk soort service u wilt gebruiken, samen met de [prijscategorie](https://azure.microsoft.com/pricing/details/cognitive-services/) (of SKU) die u wilt. U gebruikt deze en andere informatie als parameters bij het maken van de resource. U kunt een lijst met beschikbare soorten Cognitive Services vinden door de volgende methode in uw script aan te roepen:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Uw resources weergeven

Als u alle resources onder uw Azure-account (voor alle resourcegroepen) wilt weergeven, gebruikt u de volgende methode:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_list)]

## <a name="delete-a-resource"></a>Een resource verwijderen

Met de volgende methode verwijdert u de opgegeven resource uit de opgegeven resourcegroep.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/azure_management_service/create_delete_resource.cs?name=snippet_delete)]

## <a name="run-the-application"></a>De toepassing uitvoeren

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `dotnet run`.

```dotnet
dotnet run
```

## <a name="see-also"></a>Zie ook

* Zie **[aanvragen verifiëren voor Azure Cognitive Services](../../authentication.md)** over het veilig werken met Cognitive Services.
* Zie **[Wat is Azure Cognitive Services?](../../what-are-cognitive-services.md)** voor een lijst met verschillende categorieën in cognitive Services.
* Zie **[ondersteuning voor natuurlijke](../../language-support.md)** talen voor een overzicht van de natuurlijke talen die Cognitive Services ondersteunt.
* Zie **[Cognitive Services als containers gebruiken](../../cognitive-services-container-support.md)** voor meer informatie over het gebruik van Cognitive Services on-premises.
* Zie **[kosten plannen en beheren voor Cognitive Services](../../plan-manage-costs.md)** voor het schatten van de kosten van het gebruik van Cognitive Services.
* Raadpleeg de **[naslag documentatie over Cognitive Services Management SDK](/dotnet/api/overview/azure/cognitiveservices/management)** voor meer informatie over de Management SDK.
