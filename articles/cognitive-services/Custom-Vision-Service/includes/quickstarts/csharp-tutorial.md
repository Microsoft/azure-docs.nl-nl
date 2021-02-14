---
author: PatrickFarley
ms.author: pafarley
ms.service: cognitive-services
ms.date: 09/15/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f29bacb60ce71257e8ace266df0a524125e457f8
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100106093"
---
Aan de slag met de clientbibliotheek van Custom Vision voor .NET. Volg deze stappen om het pakket te installeren en de voorbeeldcode voor het bouwen van een model voor de classificatie van afbeeldingen uit te proberen. U maakt een project, voegt tags toe, traint het project en gebruikt de voorspellingseindpunt-URL van het project om het programmatisch te testen. Gebruik dit voorbeeld als een sjabloon om uw eigen beeldherkennings-app te maken.

> [!NOTE]
> Als u een classificatiemodel wilt bouwen en trainen _zonder_ code te schrijven, raadpleegt u de [handleiding voor browsers](../../getting-started-build-a-classifier.md).

Gebruik de Custom Vision-clientbibliotheek voor .NET voor het volgende:

* Een nieuw project maken in de Custom Vision-service
* Label aan het project toevoegen
* Afbeeldingen uploaden en labelen
* Het project trainen
* De huidige herhaling publiceren
* Voorspellingseindpunt testen

[Referentiedocumentatie](/dotnet/api/overview/azure/cognitiveservices/client/customvision) | Bibliotheekbroncode [(training)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Training) [(voorspelling)](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.CustomVision.Prediction) | Pakket (NuGet) [(training)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training/) [(voorspelling)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction/) | [Voorbeelden](/samples/browse/?products=azure&term=vision&terms=vision)


## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* De [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) of de huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Zodra u uw Azure-abonnement hebt, <a href="https://portal.azure.com/?microsoft_azure_marketplace_ItemHideKey=microsoft_azure_cognitiveservices_customvision#create/Microsoft.CognitiveServicesCustomVision"  title="een Custom Vision-resource maken"  target="_blank"> maakt u een Custom Vision-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om een trainings- en voorspellingsresource te maken en uw sleutels en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met Custom Vision. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Maak met behulp van Visual Studio een nieuwe .NET Core-toepassing. 

### <a name="install-the-client-library"></a>De clientbibliotheek installeren 

Nadat u een nieuw project hebt gemaakt, installeert u de clientbibliotheek door in **Solution Explorer** met de rechtermuisknop op de projectoplossing te klikken en **NuGet-pakketten beheren** te selecteren. Selecteer in de pakketbeheerder die wordt geopend de optie **Bladeren**, schakel **Prerelease opnemen** in en zoek naar `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training` en `Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction`. Selecteer de nieuwste versie en vervolgens **Installeren**. 

#### <a name="cli"></a>[CLI](#tab/cli)

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `custom-vision-quickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: *program.cs*. 

```console
dotnet new console -n custom-vision-quickstart
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

Installeer in de toepassingsmap de Custom Vision-clientbibliotheek voor .NET met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
```

---

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs), waar de codevoorbeelden uit deze quickstart zich bevinden.

Open vanuit de projectmap het bestand *program.cs* en voeg de volgende `using`-instructies toe:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_imports)]


Maak in de **Hoofdmethode** van de toepassing variabelen voor de sleutel en het eindpunt van uw resource. U declareert ook enkele basisobjecten die u later kunt gebruiken.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_creds)]

> [!IMPORTANT]
> Ga naar Azure Portal. Als de Custom Vision-resources die u in de sectie **Vereisten** hebt gemaakt, zijn geïmplementeerd, klikt u onder **Volgende stappen** op de knop **Ga naar resource**. U vindt de sleutels en het eindpunt op de pagina's over **sleutel en eindpunt** van de resources, onder **Resourcebeheer**. U moet uw trainings- en voorspellingssleutel samen met het eindpunt van de trainingsresources ophalen.
>
> Vergeet niet de sleutels uit uw code te verwijderen wanneer u klaar bent, en maak deze sleutels nooit openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Zie het artikel Cognitive Services [Beveiliging](../../../cognitive-services-security.md) voor meer informatie.

Voeg in de **Hoofdmethode** van de toepassing aanroepen toe voor de methoden die in deze quickstart worden gebruikt. U gaat deze later implementeren.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Objectmodel

|Naam|Beschrijving|
|---|---|
|[CustomVisionTrainingClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclient) | Deze klasse behandelt het maken, trainen en publiceren van uw modellen. |
|[CustomVisionPredictionClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient)| Deze klasse verwerkt de query op uw modellen voor voorspellingen met betrekking tot de classificatie van afbeeldingen.|
|[PredictionModel](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel)| Deze klasse definieert één voorspelling van één afbeelding. De klasse bevat eigenschappen voor de id en de naam van het object en een betrouwbaarheidsscore.|

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Custom Vision-clientbibliotheek voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Een nieuw project maken in de Custom Vision-service](#create-a-new-custom-vision-project)
* [Label aan het project toevoegen](#add-tags-to-the-project)
* [Afbeeldingen uploaden en labelen](#upload-and-tag-images)
* [Het project trainen](#train-the-project)
* [De huidige herhaling publiceren](#publish-the-current-iteration)
* [Voorspellingseindpunt testen](#test-the-prediction-endpoint)


## <a name="authenticate-the-client"></a>De client verifiëren

In een nieuwe methode instantieert u trainings- en voorspellingsclients met behulp van uw eindpunt en sleutels.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_auth)]

## <a name="create-a-new-custom-vision-project"></a>Een nieuw project maken in de Custom Vision-service

Met het volgende deel van de code wordt een afbeeldingsclassificatieproject gemaakt. Het project wordt weergegeven op de [Custom Vision-website](https://customvision.ai/). Raadpleeg de [CreateProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.createproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_CreateProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_String_System_String_System_Nullable_System_Guid__System_String_System_Collections_Generic_IList_System_String__&preserve-view=true)-methode om andere opties op te geven wanneer u uw project maakt (uitgelegd in de webportalgids [Een classificatie maken](../../getting-started-build-a-classifier.md)).  

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_create)]


## <a name="add-tags-to-the-project"></a>Label aan het project toevoegen

Met deze methode worden de tags gedefinieerd waarmee u het model gaat trainen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_addtags)]

## <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en labelen

Download eerst de voorbeeldafbeeldingen voor dit project. Sla de inhoud van de [map Voorbeeldafbeeldingen](https://github.com/Azure-Samples/cognitive-services-sample-data-files/tree/master/CustomVision/ImageClassification/Images) op uw lokale apparaat op.

> [!NOTE]
> Hebt u een grotere set installatie kopieën nodig om uw training te volt ooien? Met Trove, een Microsoft Garage-project, kunt u sets afbeeldingen verzamelen en aanschaffen voor trainingsdoeleinden. Wanneer u uw afbeeldingen hebt verzameld kunt u ze downloaden en importeren naar uw Custom Vision-project. Ga naar de pagina [Trove](https://www.microsoft.com/ai/trove?activetab=pivot1:primaryr3) voor meer informatie.

Definieer vervolgens een helpermethode voor het uploaden van de afbeeldingen in deze map. Mogelijk moet u het argument **GetFiles** bewerken om te verwijzen naar de locatie waar de afbeeldingen worden opgeslagen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_loadimages)]

Definieer vervolgens een methode voor het uploaden van de afbeeldingen, waarbij tags worden toegepast op basis van de locatie van de map (de afbeeldingen zijn al gesorteerd). U kunt afbeeldingen een voor een uploaden en taggen of in een batch (maximaal 64 per batch). Dit codefragment bevat voorbeelden van beide. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_upload)]


## <a name="train-the-project"></a>Het project trainen

Met deze methode wordt de eerste trainingsiteratie in het project gemaakt. Er wordt een query uitgevoerd op de service totdat de training is voltooid.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_train)]

> [!TIP]
> Trainen met geselecteerde tags
>
> Indien gewenst kun u alleen trainen op een subset van de toegepaste tags. U kunt dit doen als u bepaalde tags nog niet vaak genoeg hebt toegepast, maar u wel voldoende andere codes hebt toegepast. Gebruik in de [TrainProject](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.customvisiontrainingclientextensions.trainproject#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_CustomVisionTrainingClientExtensions_TrainProject_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_ICustomVisionTrainingClient_System_Guid_System_String_System_Nullable_System_Int32__System_Nullable_System_Boolean__System_String_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Training_Models_TrainingParameters_&preserve-view=true)-aanroep de parameter *trainingParameters*. Maak een [TrainingParameters](/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.training.models.trainingparameters) en stel de eigenschap **SelectedTags** in voor een lijst met id's van de tags die u wilt gebruiken. Het model wordt getraind om alleen de tags in de lijst te herkennen.

## <a name="publish-the-current-iteration"></a>De huidige herhaling publiceren

Met deze methode wordt de huidige iteratie van het model beschikbaar voor het uitvoeren van query's. U kunt de naam van het model gebruiken als referentie voor het verzenden van voorspellingsaanvragen. U moet uw eigen waarde invoeren voor `predictionResourceId`. U kunt de resource-id van de voorspelling vinden op het tabblad **Overzicht** van de resource in de Azure-portal, vermeld als **Abonnements-id**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_publish)]


## <a name="test-the-prediction-endpoint"></a>Voorspellingseindpunt testen

In dit deel van het script wordt de testafbeelding geladen, wordt een query op het eindpunt van het model uitgevoerd en worden de gegevens van de voorspelling op de console weergegeven.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/CustomVision/ImageClassification/Program.cs?name=snippet_test)]


## <a name="run-the-application"></a>De toepassing uitvoeren

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Voer de toepassing uit door boven in het IDE-venster op de knop **Fouten opsporen** te klikken.

#### <a name="cli"></a>[CLI](#tab/cli)

Voer de toepassing op vanuit uw toepassingsmap met de opdracht `dotnet run`.

```dotnet
dotnet run
```

---

Terwijl de toepassing wordt uitgevoerd, wordt een consolevenster geopend en wordt de volgende uitvoer geschreven:

```console
Creating new project:
        Uploading images
        Training
Done!

Making a prediction:
        Hemlock: 95.0%
        Japanese Cherry: 0.0%
```

U kunt vervolgens controleren of de testafbeelding (in **Images/Test/**) correct is gelabeld. Druk op een willekeurige toets om de toepassing af te sluiten. U kunt altijd teruggaan naar de [Custom Vision-website](https://customvision.ai) en de huidige status bekijken van het nieuwe project dat u hebt gemaakt.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [clean-ic-project](../../includes/clean-ic-project.md)]

## <a name="next-steps"></a>Volgende stappen

Nu heeft u elke stap van het proces voor afbeeldingsclassificatie in code uitgevoerd. Met dit voorbeeld wordt één trainingsiteratie uitgevoerd, maar vaak zult u uw model meerdere keren willen trainen en testen om het nauwkeuriger te maken.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](../../test-your-model.md)

* Wat is Custom Vision?
* De broncode voor dit voorbeeld is te vinden [op GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/CustomVision/ObjectDetection/Program.cs)
* [SDK-naslagdocumentatie](/dotnet/api/overview/azure/cognitiveservices/client/customvision)
