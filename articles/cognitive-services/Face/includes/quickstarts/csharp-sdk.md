---
title: Quickstart voor Face .NET-clientbibliotheek voor .NET
description: Gebruik de Face-clientbibliotheek voor .NET om gezichten te detecteren, gelijksoortige gezichten te zoeken (gezichten zoeken op afbeelding), gezichten te identificeren (zoeken met gezichtsherkenning) en uw gezichtsgegevens te migreren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: b06a35274082e09ec0973c5123b39935a93e354d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102444137"
---
Ga aan de slag met gezichtsherkenning met behulp van de Face-clientbibliotheek voor .NET. Volg deze stappen om het pakket te installeren en de voorbeeldcode voor basistaken uit te proberen. De Face-service biedt u toegang tot geavanceerde algoritmen voor het detecteren en herkennen van menselijke gezichten in afbeeldingen.

Gebruik de Face-clientbibliotheek voor .NET voor het volgende:

* [Gezichten in een afbeelding detecteren](#detect-faces-in-an-image)
* [Vergelijkbare gezichten zoeken](#find-similar-faces)
* [Een groep personen (PersonGroup) maken](#create-a-person-group)
* [Een gezicht identificeren](#identify-a-face)

[Referentiedocumentatie](/dotnet/api/overview/azure/cognitiveservices/client/faceapi) | [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.Face) | [Pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1) | [Voorbeelden](/samples/browse/?products=azure&term=face)

## <a name="prerequisites"></a>Vereisten


* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* De [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) of de huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Een Face-resource maken"  target="_blank">maakt u een Face-resource </a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Face-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Maak met behulp van Visual Studio een nieuwe .NET Core-toepassing. 

### <a name="install-the-client-library"></a>De clientbibliotheek installeren 

Nadat u een nieuw project hebt gemaakt, installeert u de clientbibliotheek door in **Solution Explorer** met de rechtermuisknop op de projectoplossing te klikken en **NuGet-pakketten beheren** te selecteren. Selecteer in de package manager die wordt geopend de optie **Bladeren**, schakel **Prerelease opnemen** in en zoek naar `Microsoft.Azure.CognitiveServices.Vision.Face`. Selecteer versie `2.6.0-preview.1` en vervolgens **Installeren**. 

#### <a name="cli"></a>[CLI](#tab/cli)

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `face-quickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: *program.cs*. 

```console
dotnet new console -n face-quickstart
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

Installeer in de toepassingsmap de Face-clientbibliotheek voor .NET met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
```

---

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs), waar de codevoorbeelden uit deze quickstart zich bevinden.


Open vanuit de projectmap het bestand *program.cs* en voeg de volgende `using`-instructies toe:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_using)]

Maak in de klasse **Programma** van de toepassing variabelen voor de sleutel en het eindpunt van uw resource.


> [!IMPORTANT]
> Ga naar Azure Portal. Als de bron van het gezicht die u hebt gemaakt in de sectie **vereisten** is geïmplementeerd, klikt u op de knop **Ga naar resource** onder **volgende stappen**. U vindt uw sleutel en eindpunt op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**. 
>
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Zie het artikel Cognitive Services [Beveiliging](../../../cognitive-services-security.md) voor meer informatie.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_creds)]

Voeg in de **Hoofdmethode** van de toepassing aanroepen toe voor de methoden die in deze quickstart worden gebruikt. U gaat deze later implementeren.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_maincalls)]

## <a name="object-model"></a>Objectmodel

De volgende klassen en interfaces verwerken enkele van de belangrijkste functies van de Face .NET-clientbibliotheek:

|Naam|Beschrijving|
|---|---|
|[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) | Deze klasse vertegenwoordigt uw autorisatie voor het gebruik van de Face-service. U hebt deze nodig voor alle Face-functies. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om instanties van andere klassen te maken. |
|[FaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperations)|Deze klasse verwerkt de basistaken voor detectie en herkenning die u met menselijke gezichten kunt uitvoeren. |
|[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)|Deze klasse vertegenwoordigt alle gegevens die zijn gedetecteerd van één gezicht in een afbeelding. U kunt deze gebruiken om gedetailleerde informatie over het gezicht op te halen.|
|[FaceListOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.facelistoperations)|Deze klasse beheert de in de cloud opgeslagen **FaceList**-constructies, waarin een geassorteerde set gezichten wordt opgeslagen. |
|[PersonGroupPersonExtensions](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongrouppersonextensions)| Deze klasse beheert de in de cloud opgeslagen **Person**-constructies, die een set gezichten opslaan die tot één persoon behoren.|
|[PersonGroupOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.persongroupoperations)| Deze klasse beheert de in de cloud opgeslagen **PersonGroup**-constructies, die een set van verschillende **Person**-objecten opslaan. |

## <a name="code-examples"></a>Codevoorbeelden

De onderstaande codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Face-clientbibliotheek voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Gezichten in een afbeelding detecteren](#detect-faces-in-an-image)
* [Vergelijkbare gezichten zoeken](#find-similar-faces)
* [Een groep personen (PersonGroup) maken](#create-a-person-group)
* [Een gezicht identificeren](#identify-a-face)

## <a name="authenticate-the-client"></a>De client verifiëren

Instantieer in een nieuwe methode een client met uw eindpunt en sleutel. Maak een **[ApiKeyServiceClientCredentials](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.apikeyserviceclientcredentials)** -object met uw sleutel en maak hiermee en met uw eindpunt een **[FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient)** -object.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_auth)]

### <a name="declare-helper-fields"></a>Helper-velden declareren

De volgende velden zijn vereist voor verschillende gezichtsbewerkingen die u later toevoegt. Definieer de volgende URL-tekenreeks in de hoofdmap van de klasse **Programma**. Deze URL verwijst naar een map met voorbeelden.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_image_url)]

Definieer in de **Main**-methode tekenreeksen die verwijzen naar de verschillende typen herkenningsmodellen. Later kunt u opgeven welk herkenningsmodel u wilt gebruiken voor gezichtsdetectie. Zie [Een herkenningsmodel opgeven](../../Face-API-How-to-Topics/specify-recognition-model.md) voor meer informatie over deze opties.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_models)]

## <a name="detect-faces-in-an-image"></a>Gezichten in een afbeelding detecteren

### <a name="get-detected-face-objects"></a>Gedetecteerde face-objecten ophalen

Maak een nieuwe methode voor het detecteren van gezichten. Met de `DetectFaceExtract`-methode worden drie van de afbeeldingen bij de opgegeven URL verwerkt en wordt een lijst met **[DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface)** -objecten in het programmageheugen gemaakt. In de lijst met **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** -waarden wordt aangegeven welke functies moeten worden geëxtraheerd. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect)]

> [!TIP]
> U kunt ook gezichten detecteren in een lokale afbeelding. Zie de [IFaceOperations](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ifaceoperations)-methoden, bijvoorbeeld **DetectWithStreamAsync**.

### <a name="display-detected-face-data"></a>Gedetecteerde gezichtsgegevens weergeven

De rest van de methode `DetectFaceExtract` parseert en drukt de kenmerkgegevens af voor elk gedetecteerd gezicht. Elk kenmerk moet afzonderlijk worden opgegeven in de oorspronkelijke gezichtsdetectie-API-aanroep (in de lijst **[FaceAttributeType](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype)** ). De volgende code verwerkt elk kenmerk, maar u zult waarschijnlijk slechts één of enkele gebruiken.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_detect_parse)]

## <a name="find-similar-faces"></a>Vergelijkbare gezichten zoeken

Met de volgende code wordt op basis van één gedetecteerd gezicht (bron) een reeks andere gezichten (doel) doorzocht om een overeenkomstig gezicht te vinden (gezichten zoeken op afbeelding). Wanneer er een overeenkomst wordt gevonden, wordt de id van het overeenkomende gezicht afgedrukt op de console.

### <a name="detect-faces-for-comparison"></a>Gezichten detecteren voor vergelijking

Definieer eerst een tweede gezichtsdetectiemethode. U moet gezichten in afbeeldingen detecteren voordat u ze kunt vergelijken en deze detectiemethode is geoptimaliseerd voor vergelijkingsbewerkingen. Er worden geen gedetailleerde gezichtskenmerken geëxtraheerd, zoals in de bovenstaande sectie, en er wordt een ander herkenningsmodel gebruikt.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_face_detect_recognize)]

### <a name="find-matches"></a>Overeenkomsten zoeken

De volgende methode detecteert gezichten in een set doelafbeeldingen en in één bronafbeelding. Vervolgens worden deze vergeleken en worden alle doelafbeeldingen gevonden die vergelijkbaar zijn met de bronafbeelding.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar)]

### <a name="print-matches"></a>Overeenkomsten afdrukken

Met de volgende code worden de overeenkomende resultaten op de console weergegeven:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_find_similar_print)]

## <a name="identify-a-face"></a>Een gezicht identificeren

Bij de bewerking Identificeren wordt op basis van een afbeelding van een persoon (of meerdere personen) gezocht naar de identiteit van elk gezicht in de afbeelding (zoeken met gezichtsherkenning). Elk gedetecteerd gezicht wordt vergeleken met een **PersonGroup**, een database van verschillende **Person**-objecten waarvan de gezichtskenmerken bekend zijn. Als u de bewerking Identificeren wilt uitvoeren, moet u eerst een **PersonGroup** maken en trainen

### <a name="create-a-person-group"></a>Een groep personen (PersonGroup) maken

Met de volgende code wordt een **PersonGroup** gemaakt met zes verschillende **Person**-objecten. Het koppelt elke **persoon** aan een reeks voorbeeldafbeeldingen en wordt vervolgens getraind om elke persoon te herkennen aan de gezichtskenmerken. **Person**- en **PersonGroup**-objecten worden gebruikt bij de bewerkingen Verifiëren, Identificeren of Groeperen.

Declareer een tekenreeksvariabele in de hoofdmap van uw klasse om de id weer te geven van de **PersonGroup** die u maakt.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_declare)]

Voeg in een nieuwe methode de volgende code toe. Met deze methode wordt de bewerking Identificeren uitgevoerd. Het eerste codeblok koppelt de namen van personen aan hun voorbeeldafbeeldingen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_files)]

Met deze code wordt de variabele `sourceImageFileName` gedefinieerd. Deze variabele komt overeen met de bronafbeelding&mdash;de afbeelding die de personen bevat die moeten worden geïdentificeerd.

Voeg vervolgens de volgende code toe om een **Person**-object te maken voor elke persoon in de woordenlijst en voeg de gezichtsgegevens toe van de juiste afbeeldingen. Elk **Person**-object is gekoppeld aan dezelfde **PersonGroup** via de unieke id-reeks. Vergeet niet om de variabelen `client`, `url`en `RECOGNITION_MODEL1` in deze methode op te geven.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_create)]

> [!TIP]
> U kunt ook een **PersonGroup** maken op basis van lokale afbeeldingen. Zie de [IPersonGroupPerson](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.ipersongroupperson)-methoden, bijvoorbeeld **AddFaceFromStreamAsync**.

### <a name="train-the-persongroup"></a>De PersonGroup trainen

Zodra u de gezichtsgegevens van uw afbeeldingen hebt geëxtraheerd en deze in verschillende **Person**-objecten hebt gesorteerd, moet u de **PersonGroup** trainen om de visuele functies te identificeren die zijn gekoppeld aan elk van de **Person**-objecten. Met de volgende code wordt de asynchrone **Train**-methode aangeroepen en worden de resultaten gecontroleerd, en wordt de status naar de console afgedrukt.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_train)]

> [!TIP]
> De Face-API wordt uitgevoerd op een set vooraf gebouwde modellen die van nature statisch zijn (de prestaties van het model worden niet slechter of beter tijdens het uitvoeren van de service). De resultaten die het model produceert, kunnen veranderen als de back-end van het model door Microsoft wordt gewijzigd zonder migratie naar een heel nieuwe modelversie. Als u wilt profiteren van een nieuwere versie van het model, kunt u **PersonGroup** opnieuw trainen, waarbij u het nieuwere model opgeeft als een parameter bij dezelfde inschrijvingsafbeeldingen.

Deze **Person**-groep en de bijbehorende **Person**-objecten zijn nu klaar om te worden gebruikt in de bewerkingen Verifiëren, Identificeren of Groeperen.

### <a name="identify-faces"></a>Gezichten identificeren

De volgende code neemt de bronafbeelding en maakt een lijst van alle gezichten die in de afbeelding zijn gedetecteerd. Dit zijn de gezichten die worden geïdentificeerd op basis van de **PersonGroup**.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify_sources)]

In het volgende codefragment wordt de bewerking **IdentifyAsync** aangeroepen en worden de resultaten naar de console afgedrukt. Hier probeert de service elk gezicht van de bronafbeelding te koppelen aan een **Person** in de opgegeven **PersonGroup**. Hiermee wordt uw methode Identificeren uitgesloten.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_identify)]

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

Als u in deze quickstart een **PersonGroup** hebt gemaakt en u deze wilt verwijderen, voert u de volgende code uit in uw programma:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_persongroup_delete)]

Definieer de verwijderingsmethode met de volgende code:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/Face/FaceQuickstart.cs?name=snippet_deletepersongroup)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u de Face-clientbibliotheek voor .NET gebruikt om eenvoudige gezichtsherkenningstaken uit te voeren. Bestudeer daarna het naslagmateriaal bij de Face-API voor meer informatie.

> [!div class="nextstepaction"]
> [Verwijzing naar de Face-API (.NET)](/dotnet/api/overview/azure/cognitiveservices/client/faceapi)

* [Wat is de Face-service?](../../overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/Face/FaceQuickstart.cs).
