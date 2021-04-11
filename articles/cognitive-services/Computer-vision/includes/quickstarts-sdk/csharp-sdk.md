---
title: 'Quick Start: client bibliotheek voor optische teken herkenning voor .NET'
description: In deze Snelstartgids gaat u aan de slag met de optische teken herkenning-client bibliotheek voor .NET.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 410ced99b1c5053c084921edf4d9bbde1a9443c4
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107073557"
---
<a name="HOLTop"></a>

Gebruik de OCR-client bibliotheek om gedrukte en handgeschreven tekst uit een afbeelding te lezen.

[Referentiedocumentatie](/dotnet/api/overview/azure/cognitiveservices/client/computervision) | [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ComputerVision) | [Pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/) | [Voorbeelden](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement - [Een gratis abonnement maken](https://azure.microsoft.com/free/cognitive-services/)
* De [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) of de huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Een Computer Vision-resource maken"  target="_blank">maakt u een Computer Vision-resource </a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Computer Vision-service. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Maak met behulp van Visual Studio een nieuwe .NET Core-toepassing. 

### <a name="install-the-client-library"></a>De clientbibliotheek installeren 

Nadat u een nieuw project hebt gemaakt, installeert u de clientbibliotheek door in **Solution Explorer** met de rechtermuisknop op de projectoplossing te klikken en **NuGet-pakketten beheren** te selecteren. Selecteer in de package manager die wordt geopend de optie **Bladeren**, schakel **Prerelease opnemen** in en zoek naar `Microsoft.Azure.CognitiveServices.Vision.ComputerVision`. Selecteer versie `6.0.0-preview.1` en vervolgens **Installeren**. 

#### <a name="cli"></a>[CLI](#tab/cli)

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `computer-vision-quickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: *Program.cs*.

```console
dotnet new console -n computer-vision-quickstart
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

Installeer in de toepassingsmap de Computer Vision-clientbibliotheek voor .NET met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

---

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs), waar de codevoorbeelden uit deze quickstart zich bevinden.

Open vanuit de projectmap het bestand *Program.cs* in uw favoriete editor of IDE.

### <a name="find-the-subscription-key-and-endpoint"></a>De abonnements sleutel en het eind punt zoeken

Ga naar Azure Portal. Als de Computer Vision-resource die u in de sectie **Vereisten** hebt gemaakt, succesvol is geïmplementeerd, klikt u op de knop **Ga naar resource** onder **Volgende stappen**. U vindt de abonnements sleutel en het eind punt op de pagina **sleutel en eind punt** van de resource, onder **resource beheer**. 

Maak in de **programma** klasse van de toepassing variabelen voor de sleutel en het eind punt van uw computer vision abonnement. Plak uw abonnements sleutel en het eind punt in de volgende code, indien aangegeven. Het Computer Vision-eind punt heeft het formulier `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_using_and_vars)]

> [!IMPORTANT]
> Vergeet niet om de abonnements sleutel uit uw code te verwijderen wanneer u klaar bent en deze nooit openbaar te plaatsen. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Bijvoorbeeld [Azure Key Vault](../../../../key-vault/general/overview.md).

Voeg in de methode `Main` van de toepassing aanroepen toe voor de methoden die in deze quickstart worden gebruikt. U gaat deze later maken.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_extracttextinmain)]

> [!div class="nextstepaction"]
> [Ik heb de client ingesteld](?success=set-up-client#object-model) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=set-up-client)

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de OCR .NET SDK.

|Naam|Beschrijving|
|---|---|
| [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Deze klasse is nodig voor alle Computer Vision-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om de meeste afbeeldingsbewerkingen uit te voeren.|
|[ComputerVisionClientExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclientextensions)| Deze klasse bevat aanvullende methoden voor de **ComputerVisionClient**.|

## <a name="code-examples"></a>Codevoorbeelden

Deze code fragmenten laten zien hoe u de volgende taken kunt uitvoeren met de OCR-client bibliotheek voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Afgedrukte en handgeschreven tekst lezen](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>De client verifiëren

In een nieuwe methode in de klasse **Program** kunt u een client instantiëren met uw eind punt en abonnements sleutel. Maak een **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.apikeyserviceclientcredentials)** -object met uw abonnements sleutel en gebruik het met uw eind punt om een **[ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)** -object te maken.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Ik heb de client geverifieerd](?success=authenticate-client#read-printed-and-handwritten-text) [Er is een probleem opgetreden](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Csharp&Section=authenticate-client)

## <a name="read-printed-and-handwritten-text"></a>Afgedrukte en handgeschreven tekst lezen

De OCR-service kan zicht bare tekst in een afbeelding lezen en deze converteren naar een teken stroom. Zie het overzicht van [optische teken herkenning (OCR)](../../overview-ocr.md) voor meer informatie over het herkennen van tekst. De code in deze sectie maakt gebruik van de nieuwste [Computer Vision SDK-versie voor Read 3,0](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/6.0.0-preview.1) en definieert een methode, `BatchReadFileUrl` die het client object gebruikt om tekst in de afbeelding te detecteren en uit te pakken.

> [!TIP]
> U kunt ook tekst extraheren uit een lokale afbeelding. Zie de [ComputerVisionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.computervision.computervisionclient)-methoden, zoals **ReadInStreamAsync**. Of zie de voorbeeldcode op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs) voor scenario's met betrekking tot lokale afbeeldingen.

### <a name="set-up-test-image"></a>Testafbeelding instellen

Sla in uw klasse **Programma** een verwijzing op naar de URL van de afbeelding waaruit u tekst wilt extraheren. Dit codefragment bevat voorbeeldafbeeldingen van gedrukte en handgeschreven tekst.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readtext_url)]

### <a name="call-the-read-api"></a>De Read-API aanroepen

Definieer de nieuwe methode voor het lezen van tekst. Voeg de onderstaande code toe, die de methode **ReadAsync** aanroept voor de gegeven afbeelding. Hiermee wordt een bewerkings-id geretourneerd en wordt een asynchroon proces gestart om de inhoud van de afbeelding te lezen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_1)]

### <a name="get-read-results"></a>Leesresultaten ophalen

Vervolgens haalt u de bewerkings-id op die wordt geretourneerd vanaf de **ReadAsync**-aanroep en gebruikt u deze om de service te doorzoeken op de resultaten van de bewerking. Met de volgende code wordt de bewerking gecontroleerd totdat de resultaten worden geretourneerd. Vervolgens worden de geëxtraheerde tekstgegevens afgedrukt naar de console.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_2)]

### <a name="display-read-results"></a>Leesresultaten weergeven

Voeg de volgende code toe om de opgehaalde tekstgegevens te parseren en weer te geven, en voltooi de methodedefinitie.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ComputerVision/ComputerVisionQuickstart.cs?name=snippet_readfileurl_3)]

## <a name="run-the-application"></a>De toepassing uitvoeren

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Voer de toepassing uit door boven in het IDE-venster op de knop **Fouten opsporen** te klikken.

#### <a name="cli"></a>[CLI](#tab/cli)

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `dotnet run`.

```dotnet
dotnet run
```

---

## <a name="clean-up-resources"></a>Resources opschonen

Als u een Cognitive Services-abonnement wilt opschonen en verwijderen, kunt u de resource of resourcegroep verwijderen. Als u de resourcegroep verwijdert, worden ook alle bijbehorende resources verwijderd.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure-CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u de OCR-client bibliotheek installeert en de Lees-API gebruikt. Vervolgens leest u meer over de API-functies voor lezen.

> [!div class="nextstepaction"]
>[De Read-API aanroepen](../../Vision-API-How-to-Topics/call-read-api.md)

* [Overzicht van OCR](../../overview-ocr.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ComputerVision/ComputerVisionQuickstart.cs).
