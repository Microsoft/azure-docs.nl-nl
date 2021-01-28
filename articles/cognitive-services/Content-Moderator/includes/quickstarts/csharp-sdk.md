---
title: 'Quickstart: Content Moderator .NET-clientbibliotheek'
titleSuffix: Azure Cognitive Services
description: In deze quickstart leert u hoe u aan de slag gaat met de Azure Content Moderator-clientbibliotheek voor .NET. Voeg software voor het filteren van inhoud toe aan uw app om te voldoen aan de regelgeving of om de beoogde omgeving voor gebruikers te behouden.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 09/15/2020
ms.author: pafarley
ms.custom: devx-track-dotnet, cog-serv-seo-aug-2020
ms.openlocfilehash: ed9d7aee5f677a5c278a985c3f8102364e94ab7e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947634"
---
Ga aan de slag met de Azure Content Moderator-clientbibliotheek voor .NET. Voer deze stappen uit om het NuGet-pakket te installeren en de voorbeeldcode voor basistaken uit te proberen. 

Content Moderator is een AI-service waarmee u inhoud kunt afhandelen die mogelijk aanstootgevend, riskant of anderszins ongewenst is. Gebruik de service voor inhoudsbeheer op basis van AI, waarmee tekst, afbeeldingen en video's worden gescand en automatisch inhoudsmarkeringen worden toegepast. Integreer vervolgens uw app met het controleprogramma, een onlinebeheeromgeving voor een team van menselijke revisoren. Voeg software voor het filteren van inhoud toe aan uw app om te voldoen aan de regelgeving of om de beoogde omgeving voor gebruikers te behouden.

Gebruik de Content Moderator-clientbibliotheek voor .NET om:

* Tekst modereren
* Afbeeldingen modereren
* Een beoordeling maken

[Referentiedocumentatie](/dotnet/api/overview/azure/cognitiveservices/client/contentmoderator) | [Broncode van bibliotheek](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Vision.ContentModerator) | [Pakket (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/) | [Voorbeelden](../../samples-dotnet.md)

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* De [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) of de huidige versie van [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Zodra u een Azure-abonnement hebt, kunt u <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="Een Content Moderator-resource maken"  target="_blank">een Content Moderator-resource maken <span class="docon docon-navigate-external x-hidden-focus"></span></a> in Azure Portal om uw sleutel en eindpunt op te halen. Wacht tot deze is geïmplementeerd en klik op de knop **Naar de resource gaan**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met Content Moderator. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="setting-up"></a>Instellen

### <a name="create-a-new-c-application"></a>Een nieuwe C#-toepassing maken

#### <a name="visual-studio-ide"></a>[Visual Studio IDE](#tab/visual-studio)

Maak met behulp van Visual Studio een nieuwe .NET Core-toepassing. 

### <a name="install-the-client-library"></a>De clientbibliotheek installeren 

Nadat u een nieuw project hebt gemaakt, installeert u de clientbibliotheek door in **Solution Explorer** met de rechtermuisknop op de projectoplossing te klikken en **NuGet-pakketten beheren** te selecteren. Selecteer in de package manager die wordt geopend de optie **Bladeren**, schakel **Prerelease opnemen** in en zoek naar `Microsoft.Azure.CognitiveServices.ContentModerator`. Selecteer versie `2.0.0` en vervolgens **Installeren**. 

#### <a name="cli"></a>[CLI](#tab/cli)

Gebruik in een consolevenster (zoals cmd, PowerShell of Bash) de opdracht `dotnet new` om een nieuwe console-app te maken met de naam `content-moderator-quickstart`. Met deze opdracht maakt u een eenvoudig Hallo wereld-C#-project met één bronbestand: *Program.cs*.

```console
dotnet new console -n content-moderator-quickstart
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

Installeer in de toepassingsmap de Content Moderator-clientbibliotheek voor .NET met de volgende opdracht:

```console
dotnet add package Microsoft.Azure.CognitiveServices.ContentModerator --version 2.0.0
```

---

> [!TIP]
> Wilt u het codebestand voor de quickstart in één keer weergeven? Die is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs), waar de codevoorbeelden uit deze quickstart zich bevinden.

Open vanuit de projectmap het bestand *Program.cs* in uw favoriete editor of IDE. Voeg de volgende `using` instructies toe:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_using)]

Maak in de klasse **Programma** variabelen voor de sleutel en het eindpunt van uw resource.

> [!IMPORTANT]
> Ga naar Azure Portal. Als de Content Moderator-resource die u in de sectie **Vereisten** hebt gemaakt, is geïmplementeerd, klikt u op de knop **Naar de resource gaan** onder **Volgende stappen**. U vindt uw sleutel en eindpunt op de pagina **Sleutel en eindpunt** van de resource, onder **Resourcebeheer**. 
>
> Vergeet niet de sleutel uit uw code te verwijderen wanneer u klaar bent, en plaats deze sleutel nooit in het openbaar. Overweeg om voor productie een veilige manier te gebruiken voor het opslaan en openen van uw referenties. Zie het artikel Cognitive Services [Beveiliging](../../../cognitive-services-security.md) voor meer informatie.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_creds)]


Voeg in de methode `main()` van de toepassing aanroepen toe voor de methoden die in deze quickstart worden gebruikt. U gaat deze later maken.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_client)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_call)]

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_call)]


## <a name="object-model"></a>Objectmodel

De volgende klassen worden gebruikt voor enkele van de belangrijkste functies van de Content Moderator .NET-clientbibliotheek.

|Naam|Beschrijving|
|---|---|
|[ContentModeratorClient](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.contentmoderatorclient)|Deze klasse is nodig voor alle Content Moderator-functionaliteit. U instantieert deze klasse met uw abonnementsgegevens en gebruikt deze om instanties van andere klassen te maken.|
|[ImageModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation)|Deze klasse biedt de functionaliteit voor het analyseren van afbeeldingen op inhoud voor volwassenen, persoonlijke gegevens of menselijke gezichten.|
|[TextModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.textmoderation)|Deze klasse biedt de functionaliteit voor het analyseren van tekst op taal, grove taal, fouten en persoonlijke gegevens.|
|[Reviews](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews)|Deze klasse biedt de functionaliteit van de Review-API's, waaronder de methoden voor het maken van taken, aangepaste werkstromen en menselijke beoordelingen.|

## <a name="code-examples"></a>Codevoorbeelden

Deze codefragmenten laten zien hoe u de volgende taken kunt uitvoeren met de Content Moderator-clientbibliotheek voor .NET:

* [De client verifiëren](#authenticate-the-client)
* [Tekst modereren](#moderate-text)
* [Afbeeldingen modereren](#moderate-images)
* [Een beoordeling maken](#create-a-review)

## <a name="authenticate-the-client"></a>De client verifiëren

Instantieer in een nieuwe methode clientobjecten met uw eindpunt en sleutel.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_auth)]

## <a name="moderate-text"></a>Tekst modereren

In de volgende code wordt een Content Moderator-client gebruikt om een stuk tekst te analyseren en de resultaten af te drukken naar de console. Definieer in- en uitvoerbestanden in de hoofdsectie van uw **Program**-klasse:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_text_vars)]

Voeg vervolgens een *TextFile.txt*-bestand toe aan de hoofdmap van uw project. Voeg uw eigen tekst toe aan dit bestand, of gebruik de volgende voorbeeldtekst:

```
Is this a grabage email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
Crap is the profanity here. Is this information PII? phone 4255550111
```


Definieer vervolgens de tekstmoderatiemethode ergens in uw **Program**-klasse:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_textmod)]

## <a name="moderate-images"></a>Afbeeldingen modereren

In de volgende code wordt een Content Moderator-client samen met een [ImageModeration](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation)-object gebruikt om externe afbeeldingen te analyseren op inhoud voor volwassenen.

> [!NOTE]
> U kunt ook de inhoud van een lokale afbeelding analyseren. Zie de [naslagdocumentatie](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.imagemoderation.evaluatefileinputwithhttpmessagesasync#Microsoft_Azure_CognitiveServices_ContentModerator_ImageModeration_EvaluateFileInputWithHttpMessagesAsync_System_IO_Stream_System_Nullable_System_Boolean__System_Collections_Generic_Dictionary_System_String_System_Collections_Generic_List_System_String___System_Threading_CancellationToken_) voor methoden en bewerkingen die werken met lokale afbeeldingen.

### <a name="get-sample-images"></a>Voorbeeldafbeeldingen ophalen

Definieer uw in- en uitvoerbestanden in de hoofdmap van de klasse **Programma**:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_image_vars)]

Maak vervolgens het invoerbestand, *ImageFiles.txt*, in de hoofdmap van uw project. In dit bestand voegt u de URL's van te analyseren afbeeldingen toe, met&mdash;één URL per regel. U kunt de volgende voorbeeldafbeeldingen gebruiken:

```
https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg
https://moderatorsampleimages.blob.core.windows.net/samples/sample5.png
```

### <a name="define-helper-class"></a>Helperklasse definiëren

Voeg de volgende klassedefinitie toe in de **Program**-klasse. Deze binnenklasse handelt afbeeldingsmoderatieresultaten af.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_dataclass)]

### <a name="define-the-image-moderation-method"></a>De methode voor het modereren van afbeeldingen definiëren

De volgende methode doorloopt de afbeeldings-URL's in een tekstbestand, maakt een **EvaluationData**-instantie en analyseert de afbeelding op inhoud voor volwassenen, tekst en menselijke gezichten. Vervolgens wordt de **EvaluationData** instantie toegevoegd aan een lijst en wordt de volledige lijst met geretourneerde gegevens naar de console geschreven.

#### <a name="iterate-through-images"></a>Afbeeldingen doorlopen

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_iterate)]

#### <a name="analyze-content"></a>Inhoud analyseren

Zie de handleiding [Image moderation concepts](../../image-moderation-api.md) (concepten voor afbeeldingsmoderatie) voor informatie over de afbeeldingskenmerken waarop Content Moderator let.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_analyze)]

#### <a name="write-moderation-results-to-file"></a>Moderatieresultaten naar bestand schrijven

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_imagemod_save)]

## <a name="create-a-review"></a>Een beoordeling maken

U kunt de Content Moderator .NET-clientbibliotheek gebruiken om inhoud naar het [beoordelingsprogramma](https://contentmoderator.cognitive.microsoft.com) te sturen zodat menselijke moderators die kunnen beoordelen. Zie de [conceptgids over het beoordelingsprogramma](../../review-tool-user-guide/human-in-the-loop.md) voor meer informatie over het beoordelingsprogramma.

De methode in deze sectie gebruikt de [Reviews](/dotnet/api/microsoft.azure.cognitiveservices.contentmoderator.reviews)-klasse om een beoordeling te maken, de id ervan op te halen en de gegevens ervan te controleren na menselijke invoer te hebben gekregen via de webportal van het beoordelingsprogramma. Al deze gegevens worden vastgelegd in een uitvoertekstbestand. 

### <a name="get-sample-images"></a>Voorbeeldafbeeldingen ophalen

Declareer de volgende matrix in de hoofdsectie van uw **Program**-klasse. Deze variabele verwijst naar een voorbeeldafbeelding die moet worden gebruikt om de beoordeling te maken.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_urls)]

### <a name="get-review-credentials"></a>Beoordelingsreferentie ophalen

Meld u aan bij het [beoordelingsprogramma](https://contentmoderator.cognitive.microsoft.com) en haal uw teamnaam op. Wijs deze toe aan de juiste variabele in de **Program**-klasse. U kunt desgewenst een callback-eindpunt instellen om updates te ontvangen over de activiteit van de beoordeling.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_vars)]

### <a name="define-helper-class"></a>Helperklasse definiëren

Voeg de volgende klassedefinitie toe in uw **Program**-klasse. Deze klasse wordt gebruikt om één beoordelingsinstantie te vertegenwoordigen die naar het beoordelingsprogramma wordt verzonden.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_review_item)]

### <a name="define-helper-method"></a>Helpermethode definiëren

Voeg de volgende methode toe aan de klasse **Program**. Deze methode schrijft de resultaten van beoordelingsquery's naar het uitvoertekstbestand.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_writeline)]

### <a name="define-the-review-creation-method"></a>De methode voor het maken van beoordelingen definiëren

Nu bent u klaar om de methode te definiëren waarmee het maken van beoordelingen en het opvragen ervan wordt afgehandeld. Voeg een nieuwe methode toe, **CreateReviews**, definieer de volgende lokale variabelen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_fields)]

#### <a name="post-reviews-to-the-review-tool"></a>Beoordelingen posten naar het beoordelingsprogramma

Voeg nu de volgende code toe om de gegeven voorbeeldafbeeldingen te doorlopen, metagegevens toe te voegen en ze in één batch naar het beoordelingsprogramma te sturen. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_create)]

Het object dat door de API-aanroep wordt geretourneerd, bevat unieke id-waarde voor elke geüploade afbeelding. De volgende code parseert deze id's en gebruikt deze om de status van elke afbeelding in de batch op te vragen bij Content Moderator.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_ids)]

### <a name="get-review-details"></a>Beoordelingsdetails ophalen

De volgende code zorgt ervoor dat het programma wacht op gebruikersinvoer. Wanneer u tijdens de uitvoering bij deze stap komt, kunt u zelf naar het [beoordelingsprogramma](https://contentmoderator.cognitive.microsoft.com) gaan, controleren dat de voorbeeldafbeelding is geüpload en daarmee werken. Zie de [instructiegids voor Reviews](../../review-tool-user-guide/review-moderated-images.md) voor informatie over het werken met een beoordeling. Wanneer u klaar bent, kunt u op een willekeurige toets drukken om door te gaan met het programma en de resultaten van het beoordelingsproces op te halen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/ContentModerator/Program.cs?name=snippet_createreview_results)]

Als u in dit scenario een callback-eindpunt hebt gebruikt, zou het een gebeurtenis in deze indeling moeten ontvangen:

```console
{'callback_endpoint': 'https://requestb.in/qmsakwqm',
 'content': '',
 'content_id': '3ebe16cb-31ed-4292-8b71-1dfe9b0e821f',
 'created_by': 'cspythonsdk',
 'metadata': [{'key': 'sc', 'value': 'True'}],
 'review_id': '201901i14682e2afe624fee95ebb248643139e7',
 'reviewer_result_tags': [{'key': 'a', 'value': 'True'},
                          {'key': 'r', 'value': 'True'}],
 'status': 'Complete',
 'sub_team': 'public',
 'type': 'Image'}
```

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

In deze quickstart hebt u geleerd hoe u de Content Moderator .NET-bibliotheek kunt gebruiken om moderatietaken uit te voeren. Nu kunt u doorgaan en in conceptgids meer lezen over het modereren van afbeeldingen en andere media.

> [!div class="nextstepaction"]
> [Concepten voor afbeeldingsmoderatie](../../image-moderation-api.md)

* [Wat is Azure Content Moderator?](../../overview.md)
* De broncode voor dit voorbeeld is te vinden op [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/ContentModerator/Program.cs).
