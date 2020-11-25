---
title: 'Voorbeeld: Een app voor afbeeldingsverwerking ontdekken in C#'
titleSuffix: Azure Cognitive Services
description: Ontdek een eenvoudige Windows-app die gebruikmaakt van de Computer Vision-API in Azure Cognitive Services. Voer OCR uit, maak miniaturen en werk met visuele kenmerken in een afbeelding.
services: cognitive-services
author: PatrickFarley
manager: nolachar
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 04/17/2019
ms.author: pafarley
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 522cf801e7c1bfdd3ed1f452c123a2db701c0c42
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96008800"
---
# <a name="sample-explore-an-image-processing-app-with-c"></a>Voorbeeld: Een app voor afbeeldingsverwerking ontdekken met C#

Een eenvoudige Windows-toepassing verkennen die Computer Vision gebruikt om optische tekenherkenning (OCR) uit te voeren, slim bijgesneden miniaturen maken, plus visuele kenmerken, inclusief gezichten, in een afbeelding detecteren, categoriseren, taggen en beschrijven. Met het onderstaande voorbeeld kunt u een afbeeldings-URL of een lokaal opgeslagen bestand verzenden voor analyse. U kunt dit open-sourcevoorbeeld gebruiken als een sjabloon voor het bouwen van uw eigen app voor Windows met behulp van de Computer Vision-API en Windows Presentation Foundation (WPF), een onderdeel van .NET Framework.

> [!div class="checklist"]
> * De voorbeeld-app downloaden vanuit GitHub
> * De voorbeeld-app openen en bouwen in Visual Studio
> * De voorbeeld-app uitvoeren en ermee werken om verschillende scenario's uit te voeren
> * De verschillende bij de voorbeeld-app meegeleverde scenario's ontdekken

## <a name="prerequisites"></a>Vereisten

Voordat u met de voorbeeld-app gaat werken, moet zijn voldaan aan de volgende vereisten:

* U hebt [Visual Studio 2015](https://visualstudio.microsoft.com/downloads/) of hoger nodig.
* Een Azure-abonnement - [Maak een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Een Computer Vision-resource maken"  target="_blank">maakt u een Computer Vision-resource <span class="docon docon-navigate-external x-hidden-focus"></span></a> in de Azure-portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Computer Vision-service. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.

## <a name="get-the-sample-app"></a>De voorbeeld-app downloaden

De Computer Vision-voorbeeld-app is beschikbaar op GitHub in de opslagplaats `Microsoft/Cognitive-Vision-Windows`. Deze opslagplaats bevat ook de opslagplaats `Microsoft/Cognitive-Common-Windows` als een Git-submodule. U kunt deze opslagplaats recursief klonen, inclusief de submodule, met behulp van de opdracht `git clone --recurse-submodules` vanaf de opdrachtregel of met behulp van GitHub Desktop.

Als u de opslagplaats voor de Computer Vision-voorbeeld-app bijvoorbeeld wilt klonen vanaf een opdrachtprompt, voert u de volgende opdracht uit:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Vision-Windows.git
```

> [!IMPORTANT]
> Download deze opslagplaats niet als een ZIP-bestand. Als u de opslagplaats downloadt als een ZIP-bestand, worden de Git-submodules niet opgenomen.

### <a name="get-optional-sample-images"></a>Optionele voorbeeldafbeeldingen downloaden

Desgewenst kunt u de voorbeeldafbeeldingen gebruiken die bij de voorbeeld-app [Face](../../Face/Overview.md) worden meegeleverd. U vindt deze op GitHub in de opslagplaats `Microsoft/Cognitive-Face-Windows`. Deze voorbeeld-app bevat de map `/Data`, die meerdere afbeeldingen van personen bevat. U kunt deze opslagplaats ook recursief klonen met behulp van de beschreven methoden voor de Computer Vision-voorbeeld-app.

Als u de opslagplaats voor de voorbeeld-app Face bijvoorbeeld wilt klonen vanaf een opdrachtprompt, voert u de volgende opdracht uit:

```Console
git clone --recurse-submodules https://github.com/Microsoft/Cognitive-Face-Windows.git
```

## <a name="open-and-build-the-sample-app-in-visual-studio"></a>De voorbeeld-app openen en bouwen in Visual Studio

Voordat u de voorbeeld-app kunt uitvoeren of verkennen, moet u eerst de voorbeeld-app bouwen zodat Visual Studio afhankelijkheden kan oplossen. Voer de volgende stappen uit om de voorbeeld-app te openen en te bouwen:

1. Open het Visual Studio-oplossingenbestand `/Sample-WPF/VisionAPI-WPF-Samples.sln` in Visual Studio.
1. Controleer of de Visual Studio-oplossing twee projecten bevat:  

   * SampleUserControlLibrary
   * VisionAPI-WPF-Samples  

   Als het project SampleUserControlLibrary niet beschikbaar is, controleert u of de opslagplaats `Microsoft/Cognitive-Vision-Windows` recursief gekloond is.
1. Druk in Visual Studio op Ctrl+Shift+B of kies **Build** in het lintmenu en kies vervolgens **Build Solution** om de oplossing te bouwen.

## <a name="run-and-interact-with-the-sample-app"></a>De voorbeeld-app uitvoeren en ermee werken

U kunt de voorbeeld-app uitvoeren om te zien hoe deze samenwerkt met u en de Computer Vision-clientbibliotheek bij het uitvoeren van verschillende taken, zoals het genereren van miniaturen of het taggen van afbeeldingen. Als u de voorbeeld-app wilt uitvoeren en ermee wilt werken, gaat u als volgt te werk:

1. Druk, nadat het bouwen voltooid is, op **F5** of kies **Debug** in het lintmenu en kies vervolgens **Start debugging** om de voorbeeld-app uit te voeren.
1. Wanneer de voorbeeld-app wordt weergegeven, kiest u **Subscription Key Management** in het navigatiedeelvenster om de pagina voor het beheren van abonnementssleutels weer te geven.
   ![Pagina Subscription Key Management](../Images/Vision_UI_Subscription.PNG)  
1. Geef uw abonnementssleutel op in **Subscription Key**.
1. Voer de eindpunt-URL in bij **Eindpunt**.  
   [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Als u uw abonnementssleutel en eindpunt-URL niet opnieuw wilt invoeren wanneer u de voorbeeld-app later nogmaals uitvoert, kiest u **Save Setting** om de abonnementssleutel en de eindpunt-URL op te slaan op uw computer. Als u de eerder opgeslagen abonnementssleutel en eindpunt-URL wilt verwijderen, kiest u **Delete Setting**.

   > [!NOTE]
   > De voorbeeld-app maakt gebruik van geïsoleerde opslag en `System.IO.IsolatedStorage` voor het opslaan van uw abonnementssleutel en eindpunt-URL.

1. Selecteer onder **Select a scenario** in het navigatiedeelvenster een van de scenario's die momenteel zijn opgenomen in de voorbeeld-app:  

   | Scenario | Beschrijving |
   |----------|-------------|
   |Analyze Image | Maakt gebruik van de bewerking [Analyze Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) voor het analyseren van een lokale of externe afbeelding. U kunt de visuele kenmerken en de taal voor de analyse kiezen en zowel de afbeelding als de resultaten bekijken.  |
   |Analyze Image with Domain Model | Maakt gebruik van de bewerking [List Domain Specific Models](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fd) voor het weergeven van een lijst met de domeinmodellen waaruit u kunt kiezen, en de bewerking [Recognize Domain Specific Content](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e200) voor het analyseren van een lokale of externe afbeelding met behulp van het geselecteerde domeinmodel. U kunt ook de taal voor de analyse kiezen. |
   |Describe Image | Maakt gebruik van de bewerking [Describe Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fe) voor het maken van een leesbare beschrijving van de lokale of externe afbeelding. U kunt ook de taal voor de beschrijving kiezen. |
   |Generate Tags | Maakt gebruik van de bewerking [Tag Image](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1ff) voor het taggen van de visuele kenmerken van een lokale of externe afbeelding. U kunt ook de taal voor de tags kiezen. |
   |Recognize Text (OCR) | Maakt gebruik van de bewerking [OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc) voor het herkennen en extraheren van gedrukte tekst in een afbeelding. U kunt de taal handmatig kiezen of automatisch door Computer Vision laten detecteren. |
   |Recognize Text V2 (Engels) | Maakt gebruik van de bewerkingen [Recognize Text](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2c6a154055056008f200) en [Get Recognize Text Operation Result](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/587f2cf1154055056008f201) voor het asynchroon herkennen en extraheren van gedrukte of handgeschreven tekst in een afbeelding. |
   |Get Thumbnail | Maakt gebruik van de bewerking [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) voor het genereren van een miniatuur voor een lokale of externe afbeelding. |

   In de volgende schermafbeelding ziet u de pagina die is opgegeven voor het scenario Analyze Image, na het analyseren van een voorbeeldafbeelding.
   ![Schermopname van de pagina Analyze Image](../Images/Analyze_Image_Example.PNG)

## <a name="explore-the-sample-app"></a>De voorbeeld-app verkennen

De Visual Studio-oplossing voor de Computer Vision-voorbeeld-app bevat twee projecten:

* SampleUserControlLibrary  
  Het project SampleUserControlLibrary biedt functionaliteit die wordt gebruikt in meerdere Cognitive Services-voorbeelden. Het project bevat het volgende:
  * SampleScenarios  
    Een UserControl (besturingselement voor de gebruiker) voor een gestandaardiseerde weergave (zoals een titelbalk, navigatievenster en inhoudsvenster) voor voorbeelden. De Computer Vision-voorbeeld-app maakt gebruik van dit besturingselement in het venster MainWindow.xaml voor het weergeven van scenariopagina's en het verkrijgen van toegang tot gedeelde gegevens, zoals de abonnementssleutel en de eindpunt-URL.
  * SubscriptionKeyPage  
    Een pagina met een gestandaardiseerde indeling voor het invoeren van een abonnementssleutel en eindpunt-URL voor de voorbeeld-app. De Computer Vision-voorbeeld-app maakt gebruik van deze pagina voor het beheren van de abonnementssleutel en eindpunt-URL voor de scenariopagina's.
  * VideoResultControl  
    Een UserControl voor een gestandaardiseerde weergave voor videogegevens. De Computer Vision-voorbeeld-app maakt geen gebruik van dit besturingselement.
* VisionAPI-WPF-Samples  
  Dit is het belangrijkste project voor de Computer Vision-voorbeeld-app. Het bevat alle functionaliteit die van belang is voor Computer Vision. Het project bevat het volgende:
  * AnalyzeInDomainPage.xaml  
    De scenariopagina voor het scenario Analyze Image with Domain Model.
  * AnalyzeImage.xaml  
    De scenariopagina voor het scenario Analyze Image.
  * DescribePage.xaml  
    De scenariopagina voor het scenario Describe Image.
  * ImageScenarioPage.cs  
    De klasse ImageScenarioPage, waarvan alle scenario's in de voorbeeld-app zijn afgeleid. Deze klasse beheert functionaliteit, zoals het opgeven van referenties en het opmaken van uitvoer, die wordt gebruikt in alle scenario's.
  * MainWindow.xaml  
    Dit is het hoofdvenster van de voorbeeld-app. Het maakt gebruik van het besturingselement SampleScenarios voor het weergeven van de SubscriptionKeyPage- en scenariopagina's.
  * OCRPage.xaml  
    De scenariopagina voor het scenario Recognize Text (OCR).
  * RecognizeLanguage.cs  
    De klasse RecognizeLanguage, die informatie verschaft over de talen die worden ondersteund door de verschillende methoden in de voorbeeld-app.
  * TagsPage.xaml  
    De scenariopagina voor het scenario Generate Tags.
  * TextRecognitionPage.xaml  
    De scenariopagina voor het scenario Recognize Text V2 (Engels).
  * ThumbnailPage.xaml  
    De scenariopagina voor het scenario Get Thumbnail.

### <a name="explore-the-sample-code"></a>De voorbeeldcode verkennen

Belangrijke gedeelten van de voorbeeldcode zijn omlijst met commentaarblokken die beginnen met `KEY SAMPLE CODE STARTS HERE` en eindigen met `KEY SAMPLE CODE ENDS HERE`, zodat u de voorbeeld-app gemakkelijk kunt verkennen. Deze gedeelten bevatten de code die het meest relevant is om te leren hoe u verschillende taken kunt uitvoeren met de Computer Vision-API-clientbibliotheek. U kunt zoeken op `KEY SAMPLE CODE STARTS HERE` in Visual Studio om door de meest relevante secties code in de Computer Vision-voorbeeld-app te bladeren. 

Zo laat de methode `UploadAndAnalyzeImageAsync`, die hieronder wordt weergegeven en is opgenomen in AnalyzePage.xaml, bijvoorbeeld zien hoe u de clientbibliotheek gebruikt voor het analyseren van een lokale afbeelding met behulp van de methode `ComputerVisionClient.AnalyzeImageInStreamAsync`.

```csharp
private async Task<ImageAnalysis> UploadAndAnalyzeImageAsync(string imageFilePath)
{
    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE STARTS HERE
    // -----------------------------------------------------------------------

    //
    // Create Cognitive Services Vision API Service client.
    //
    using (var client = new ComputerVisionClient(Credentials) { Endpoint = Endpoint })
    {
        Log("ComputerVisionClient is created");

        using (Stream imageFileStream = File.OpenRead(imageFilePath))
        {
            //
            // Analyze the image for all visual features.
            //
            Log("Calling ComputerVisionClient.AnalyzeImageInStreamAsync()...");
            VisualFeatureTypes[] visualFeatures = GetSelectedVisualFeatures();
            string language = (_language.SelectedItem as RecognizeLanguage).ShortCode;
            ImageAnalysis analysisResult = await client.AnalyzeImageInStreamAsync(imageFileStream, visualFeatures, null, language);
            return analysisResult;
        }
    }

    // -----------------------------------------------------------------------
    // KEY SAMPLE CODE ENDS HERE
    // -----------------------------------------------------------------------
}
```

### <a name="explore-the-client-library"></a>De clientbibliotheek verkennen

Deze voorbeeld-app maakt gebruik van de Computer Vision-API-clientbibliotheek, een thin C#-clientwrapper voor de Computer Vision-API in Azure Cognitive Services. De clientbibliotheek is beschikbaar via het NuGet-pakket [Microsoft.Azure.CognitiveServices.Vision.ComputerVision](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.ComputerVision/). Bij het bouwen van de Visual Studio-toepassing hebt u de clientbibliotheek opgehaald uit het bijbehorende NuGet-pakket. U kunt de broncode voor de clientbibliotheek ook bekijken in de map `/ClientLibrary` van de opslagplaats `Microsoft/Cognitive-Vision-Windows`.

De functionaliteit van de clientbibliotheek wordt geleverd door de klasse `ComputerVisionClient` in de naamruimte `Microsoft.Azure.CognitiveServices.Vision.ComputerVision`. De modellen die door de klasse `ComputerVisionClient` worden gebruikt tijdens interactie met Computer Vision, vindt u in de naamruimte `Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models`. In de verschillende XAML-scenariopagina's van de voorbeeld-app vindt u de volgende `using`-richtlijnen voor het gebruik van die naamruimten:

```csharp
// -----------------------------------------------------------------------
// KEY SAMPLE CODE STARTS HERE
// Use the following namespace for ComputerVisionClient.
// -----------------------------------------------------------------------
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
// -----------------------------------------------------------------------
// KEY SAMPLE CODE ENDS HERE
// -----------------------------------------------------------------------
```

U leert meer over de verschillende methoden die zijn opgenomen in de klasse `ComputerVisionClient` bij het verkennen van de scenario's die deel uitmaken van de Computer Vision-voorbeeld-app.

## <a name="explore-the-analyze-image-scenario"></a>Het scenario Analyze Image verkennen

Dit scenario wordt beheerd door de pagina AnalyzePage.xaml. U kunt de visuele kenmerken en de taal voor de analyse kiezen en zowel de afbeelding als de resultaten bekijken. De scenariopagina doet dit met een van de volgende methoden, afhankelijk van de bron van de afbeelding:

* UploadAndAnalyzeImageAsync  
  Deze methode wordt gebruikt voor lokale afbeeldingen, waarbij de afbeelding moet worden gecodeerd als een `Stream` en moet worden verzonden naar Computer Vision door het aanroepen van de methode `ComputerVisionClient.AnalyzeImageInStreamAsync`.
* AnalyzeUrlAsync  
  Deze methode wordt gebruikt voor externe afbeeldingen, waarbij de URL voor de afbeelding naar Computer Vision wordt verzonden door het aanroepen van de methode `ComputerVisionClient.AnalyzeImageAsync`.

Met de methode `UploadAndAnalyzeImageAsync` wordt een nieuwe instantie van `ComputerVisionClient` met de opgegeven abonnementssleutel en eindpunt-URL gemaakt. Omdat u de voorbeeld-app gebruikt voor het analyseren van een lokale afbeelding, moet de inhoud van die afbeelding worden verzonden naar Computer Vision. Hiermee wordt het lokale bestand dat is opgegeven in `imageFilePath` geopend en gelezen als een `Stream`. Vervolgens worden de visuele kenmerken en de taal opgehaald die in de scenariopagina zijn geselecteerd. Hiermee wordt de methode `ComputerVisionClient.AnalyzeImageInStreamAsync` aangeroepen, waarbij de `Stream` voor het bestand, de visuele kenmerken en de taal worden doorgegeven. Het resultaat wordt vervolgens geretourneerd als een instantie van `ImageAnalysis`. Met de overgenomen methoden uit de klasse `ImageScenarioPage` worden de geretourneerde resultaten weergegeven in de scenariopagina.

Met de methode `AnalyzeUrlAsync` wordt een nieuwe instantie van `ComputerVisionClient` met de opgegeven abonnementssleutel en eindpunt-URL gemaakt. Hierbij worden de visuele kenmerken en taal gebruikt die in de scenariopagina zijn geselecteerd. Hiermee wordt de methode `ComputerVisionClient.AnalyzeImageInStreamAsync` aangeroepen, waarbij de URL van de afbeelding, de visuele kenmerken en de taal worden doorgegeven. Het resultaat wordt vervolgens geretourneerd als een instantie van `ImageAnalysis`. Met de overgenomen methoden uit de klasse `ImageScenarioPage` worden de geretourneerde resultaten weergegeven in de scenariopagina.

## <a name="explore-the-analyze-image-with-domain-model-scenario"></a>Het scenario Analyze Image with Domain Model verkennen

Dit scenario wordt beheerd door de pagina AnalyzeInDomainPage.xaml. U kunt een taal en een domeinmodel zoals `celebrities` of `landmarks` kiezen om een afbeelding in een specifiek domein te analyseren en de afbeelding en de resultaten te bekijken. De scenariopagina gebruikt hiervoor de volgende methoden, afhankelijk van de bron van de afbeelding:

* GetAvailableDomainModelsAsync  
  Met deze methode wordt de lijst met beschikbare domeinmodellen opgehaald uit Computer Vision en wordt het besturingselement `_domainModelComboBox` op de pagina ingevuld met behulp van de methode `ComputerVisionClient.ListModelsAsync`.
* UploadAndAnalyzeInDomainImageAsync  
  Deze methode wordt gebruikt voor lokale afbeeldingen, waarbij de afbeelding moet worden gecodeerd als een `Stream` en moet worden verzonden naar Computer Vision door het aanroepen van de methode `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync`.
* AnalyzeInDomainUrlAsync  
  Deze methode wordt gebruikt voor externe afbeeldingen, waarbij de URL voor de afbeelding naar Computer Vision wordt verzonden door het aanroepen van de methode `ComputerVisionClient.AnalyzeImageByDomainAsync`.

Met de methode `UploadAndAnalyzeInDomainImageAsync` wordt een nieuwe instantie van `ComputerVisionClient` met de opgegeven abonnementssleutel en eindpunt-URL gemaakt. Omdat u de voorbeeld-app gebruikt voor het analyseren van een lokale afbeelding, moet de inhoud van die afbeelding worden verzonden naar Computer Vision. Hiermee wordt het lokale bestand dat is opgegeven in `imageFilePath` geopend en gelezen als een `Stream`. Vervolgens wordt de taal opgehaald die in de scenariopagina is geselecteerd. Hiermee wordt de methode `ComputerVisionClient.AnalyzeImageByDomainInStreamAsync` aangeroepen, waarbij de `Stream` voor het bestand, de naam van het domeinmodel en de taal worden doorgegeven. Het resultaat wordt vervolgens geretourneerd als een instantie van `DomainModelResults`. Met de overgenomen methoden uit de klasse `ImageScenarioPage` worden de geretourneerde resultaten weergegeven in de scenariopagina.

Met de methode `AnalyzeInDomainUrlAsync` wordt een nieuwe instantie van `ComputerVisionClient` met de opgegeven abonnementssleutel en eindpunt-URL gemaakt. Hierbij wordt de taal gebruikt die in de scenariopagina is geselecteerd. Hiermee wordt de methode `ComputerVisionClient.AnalyzeImageByDomainAsync` aangeroepen, waarbij de URL van de afbeelding, de visuele kenmerken en de taal worden doorgegeven. Het resultaat wordt vervolgens geretourneerd als een instantie van `DomainModelResults`. Met de overgenomen methoden uit de klasse `ImageScenarioPage` worden de geretourneerde resultaten weergegeven in de scenariopagina.

## <a name="explore-the-describe-image-scenario"></a>Het scenario Describe Image verkennen

Dit scenario wordt beheerd door de pagina DescribePage.xaml. U kunt een taal kiezen voor het maken van een leesbare beschrijving van de afbeelding, en de afbeelding en de resultaten bekijken. De scenariopagina gebruikt hiervoor de volgende methoden, afhankelijk van de bron van de afbeelding:

* UploadAndDescribeImageAsync  
  Deze methode wordt gebruikt voor lokale afbeeldingen, waarbij de afbeelding moet worden gecodeerd als een `Stream` en moet worden verzonden naar Computer Vision door het aanroepen van de methode `ComputerVisionClient.DescribeImageInStreamAsync`.
* DescribeUrlAsync  
  Deze methode wordt gebruikt voor externe afbeeldingen, waarbij de URL voor de afbeelding naar Computer Vision wordt verzonden door het aanroepen van de methode `ComputerVisionClient.DescribeImageAsync`.

Met de methode `UploadAndDescribeImageAsync` wordt een nieuwe instantie van `ComputerVisionClient` met de opgegeven abonnementssleutel en eindpunt-URL gemaakt. Omdat u de voorbeeld-app gebruikt voor het analyseren van een lokale afbeelding, moet de inhoud van die afbeelding worden verzonden naar Computer Vision. Hiermee wordt het lokale bestand dat is opgegeven in `imageFilePath` geopend en gelezen als een `Stream`. Vervolgens wordt de taal opgehaald die in de scenariopagina is geselecteerd. Hiermee wordt de methode `ComputerVisionClient.DescribeImageInStreamAsync` aangeroepen, waarbij de `Stream` voor het bestand, het maximum aantal kandidaten (in dit geval 3) en de taal worden doorgegeven. Het resultaat wordt vervolgens geretourneerd als een instantie van `ImageDescription`. Met de overgenomen methoden uit de klasse `ImageScenarioPage` worden de geretourneerde resultaten weergegeven in de scenariopagina.

Met de methode `DescribeUrlAsync` wordt een nieuwe instantie van `ComputerVisionClient` met de opgegeven abonnementssleutel en eindpunt-URL gemaakt. Hierbij wordt de taal gebruikt die in de scenariopagina is geselecteerd. Hiermee wordt de methode `ComputerVisionClient.DescribeImageAsync` aangeroepen, waarbij de URL van de afbeelding, het maximum aantal kandidaten (in dit geval 3) en de taal worden doorgegeven. Het resultaat wordt vervolgens geretourneerd als een instantie van `ImageDescription`. Met de overgenomen methoden uit de klasse `ImageScenarioPage` worden de geretourneerde resultaten weergegeven in de scenariopagina.

## <a name="explore-the-generate-tags-scenario"></a>Het scenario Generate Tags verkennen

Dit scenario wordt beheerd door de pagina TagsPage.xaml. U kunt een taal kiezen voor het taggen van de visuele kenmerken van een afbeelding en zowel de afbeelding als de resultaten bekijken. De scenariopagina gebruikt hiervoor de volgende methoden, afhankelijk van de bron van de afbeelding:

* UploadAndGetTagsForImageAsync  
  Deze methode wordt gebruikt voor lokale afbeeldingen, waarbij de afbeelding moet worden gecodeerd als een `Stream` en moet worden verzonden naar Computer Vision door het aanroepen van de methode `ComputerVisionClient.TagImageInStreamAsync`.
* GenerateTagsForUrlAsync  
  Deze methode wordt gebruikt voor externe afbeeldingen, waarbij de URL voor de afbeelding naar Computer Vision wordt verzonden door het aanroepen van de methode `ComputerVisionClient.TagImageAsync`.

Met de methode `UploadAndGetTagsForImageAsync` wordt een nieuwe instantie van `ComputerVisionClient` met de opgegeven abonnementssleutel en eindpunt-URL gemaakt. Omdat u de voorbeeld-app gebruikt voor het analyseren van een lokale afbeelding, moet de inhoud van die afbeelding worden verzonden naar Computer Vision. Hiermee wordt het lokale bestand dat is opgegeven in `imageFilePath` geopend en gelezen als een `Stream`. Vervolgens wordt de taal opgehaald die in de scenariopagina is geselecteerd. Hiermee wordt de methode `ComputerVisionClient.TagImageInStreamAsync` aangeroepen, waarbij de `Stream` voor het bestand en de taal worden doorgegeven. Het resultaat wordt vervolgens geretourneerd als een instantie van `TagResult`. Met de overgenomen methoden uit de klasse `ImageScenarioPage` worden de geretourneerde resultaten weergegeven in de scenariopagina.

Met de methode `GenerateTagsForUrlAsync` wordt een nieuwe instantie van `ComputerVisionClient` met de opgegeven abonnementssleutel en eindpunt-URL gemaakt. Hierbij wordt de taal gebruikt die in de scenariopagina is geselecteerd. Hiermee wordt de methode `ComputerVisionClient.TagImageAsync` aangeroepen, waarbij de URL van de afbeelding en de taal worden doorgegeven. Het resultaat wordt vervolgens geretourneerd als een instantie van `TagResult`. Met de overgenomen methoden uit de klasse `ImageScenarioPage` worden de geretourneerde resultaten weergegeven in de scenariopagina.

## <a name="explore-the-recognize-text-ocr-scenario"></a>Het scenario Recognize Text (OCR) verkennen

Dit scenario wordt beheerd door de pagina OCRPage.xaml. U kunt een taal kiezen voor het herkennen en extraheren van gedrukte tekst in een afbeelding en zowel de afbeelding als de resultaten bekijken. De scenariopagina gebruikt hiervoor de volgende methoden, afhankelijk van de bron van de afbeelding:

* UploadAndRecognizeImageAsync  
  Deze methode wordt gebruikt voor lokale afbeeldingen, waarbij de afbeelding moet worden gecodeerd als een `Stream` en moet worden verzonden naar Computer Vision door het aanroepen van de methode `ComputerVisionClient.RecognizePrintedTextInStreamAsync`.
* RecognizeUrlAsync  
  Deze methode wordt gebruikt voor externe afbeeldingen, waarbij de URL voor de afbeelding naar Computer Vision wordt verzonden door het aanroepen van de methode `ComputerVisionClient.RecognizePrintedTextAsync`.

Met de methode `UploadAndRecognizeImageAsync` wordt een nieuwe instantie van `ComputerVisionClient` met de opgegeven abonnementssleutel en eindpunt-URL gemaakt. Omdat u de voorbeeld-app gebruikt voor het analyseren van een lokale afbeelding, moet de inhoud van die afbeelding worden verzonden naar Computer Vision. Hiermee wordt het lokale bestand dat is opgegeven in `imageFilePath` geopend en gelezen als een `Stream`. Vervolgens wordt de taal opgehaald die in de scenariopagina is geselecteerd. Hiermee wordt de methode `ComputerVisionClient.RecognizePrintedTextInStreamAsync` aangeroepen, wat aangeeft dat de tekstrichting niet is gedetecteerd, waarbij de `Stream` voor het bestand en de taal worden doorgegeven. Het resultaat wordt vervolgens geretourneerd als een instantie van `OcrResult`. Met de overgenomen methoden uit de klasse `ImageScenarioPage` worden de geretourneerde resultaten weergegeven in de scenariopagina.

Met de methode `RecognizeUrlAsync` wordt een nieuwe instantie van `ComputerVisionClient` met de opgegeven abonnementssleutel en eindpunt-URL gemaakt. Hierbij wordt de taal gebruikt die in de scenariopagina is geselecteerd. Hiermee wordt de methode `ComputerVisionClient.RecognizePrintedTextAsync` aangeroepen, wat aangeeft dat de tekstrichting niet is gedetecteerd, waarbij de URL van de afbeelding en de taal worden doorgegeven. Het resultaat wordt vervolgens geretourneerd als een instantie van `OcrResult`. Met de overgenomen methoden uit de klasse `ImageScenarioPage` worden de geretourneerde resultaten weergegeven in de scenariopagina.

## <a name="explore-the-recognize-text-v2-english-scenario"></a>Het scenario Recognize Text V2 (Engels) verkennen

Dit scenario wordt beheerd door de pagina TextRecognitionPage.xaml. U kunt de herkenningsmodus en een taal kiezen voor het asynchroon herkennen en extraheren van gedrukte of handgeschreven tekst in een afbeelding, en zowel de afbeelding als de resultaten bekijken. De scenariopagina gebruikt hiervoor de volgende methoden, afhankelijk van de bron van de afbeelding:

* UploadAndRecognizeImageAsync  
  Deze methode wordt gebruikt voor lokale afbeeldingen, waarbij de afbeelding moet worden gecodeerd als een `Stream` en moet worden verzonden naar Computer Vision door het aanroepen van de methode `RecognizeAsync` en het doorgeven van een geparametriseerde gemachtigde voor de methode `ComputerVisionClient.RecognizeTextInStreamAsync`.
* RecognizeUrlAsync  
  Deze methode wordt gebruikt voor externe afbeeldingen, waarbij de URL voor de afbeelding wordt verzonden naar Computer Vision door het aanroepen van de methode `RecognizeAsync` en het doorgeven van een geparametriseerde gemachtigde voor de methode `ComputerVisionClient.RecognizeTextAsync`.
* RecognizeAsync: met deze methode wordt de asynchrone aanroep voor de methoden `UploadAndRecognizeImageAsync` en `RecognizeUrlAsync` verwerkt en gecontroleerd of er resultaten zijn door het aanroepen van de methode `ComputerVisionClient.GetTextOperationResultAsync`.

In tegenstelling tot de andere scenario's in de Computer Vision-voorbeeld-app, is dit een asynchroon scenario omdat er een methode wordt aangeroepen om het proces te starten en een andere methode wordt aangeroepen om de status te controleren en de resultaten van dat proces te retourneren. De logische stroom bij dit scenario is ietwat anders dan bij de overige scenario's.

Met de methode `UploadAndRecognizeImageAsync` wordt het lokale bestand dat is opgegeven in `imageFilePath` geopend en gelezen als een `Stream`. Vervolgens wordt de methode `RecognizeAsync` aangeroepen en het volgende doorgegeven:

* Een lambda-expressie voor een geparametriseerde asynchrone gemachtigde van de methode `ComputerVisionClient.RecognizeTextInStreamAsync`, waarbij de `Stream` voor het bestand en de herkenningsmodus worden doorgegeven als parameters in `GetHeadersAsyncFunc`.
* Een lambda-expressie voor een gemachtigde om de waarde van de antwoordheader `Operation-Location` in `GetOperationUrlFunc` op te halen.

Met de methode `RecognizeUrlAsync` wordt de methode `RecognizeAsync` aangeroepen, waarbij het volgende wordt doorgegeven:

* Een lambda-expressie voor een geparametriseerde asynchrone gemachtigde van de methode `ComputerVisionClient.RecognizeTextAsync`, waarbij de URL van de externe afbeelding en de herkenningsmodus worden doorgegeven als parameters in `GetHeadersAsyncFunc`.
* Een lambda-expressie voor een gemachtigde om de waarde van de antwoordheader `Operation-Location` in `GetOperationUrlFunc` op te halen.

Wanneer de methode `RecognizeAsync` is voltooid, wordt het resultaat door de methoden `UploadAndRecognizeImageAsync` en `RecognizeUrlAsync` geretourneerd als een instantie van `TextOperationResult`. Met de overgenomen methoden uit de klasse `ImageScenarioPage` worden de geretourneerde resultaten weergegeven in de scenariopagina.

Met de methode `RecognizeAsync` wordt de geparameteriseerde gemachtigde voor de methode `ComputerVisionClient.RecognizeTextInStreamAsync` of `ComputerVisionClient.RecognizeTextAsync` aangeroepen, afhankelijk van welke methode is doorgegeven in `GetHeadersAsyncFunc`, en wordt er gewacht op het antwoord. Vervolgens wordt de gemachtigde die is doorgegeven in `GetOperationUrlFunc` aangeroepen om de waarde van de antwoordheader `Operation-Location` op te halen uit het antwoord. Deze waarde is de URL die wordt gebruikt voor het ophalen van de resultaten van de methode die is doorgegeven in `GetHeadersAsyncFunc` van Computer Vision.

De methode `RecognizeAsync` roept vervolgens de methode `ComputerVisionClient.GetTextOperationResultAsync` aan, waarbij de opgehaalde URL uit de antwoordheader `Operation-Location` wordt doorgegeven om de status en het resultaat van de doorgegeven methode in `GetHeadersAsyncFunc` op te halen. Als de status niet aangeeft dat de methode al dan niet met succes is voltooid, probeert `RecognizeAsync` nog 3 keer `ComputerVisionClient.GetTextOperationResultAsync` aan te roepen, met tussenpozen van 3 seconden. De methode `RecognizeAsync` retourneert de resultaten naar de methode waarmee deze is aangeroepen.

## <a name="explore-the-get-thumbnail-scenario"></a>Het scenario Get Thumbnail verkennen

Dit scenario wordt beheerd door de pagina ThumbnailPage.xaml. U kunt aangeven of u slim bijsnijden wilt gebruiken en de gewenste hoogte en breedte voor het genereren van een miniatuur van een afbeelding opgeven, en de afbeelding en resultaten zien. De scenariopagina gebruikt hiervoor de volgende methoden, afhankelijk van de bron van de afbeelding:

* UploadAndThumbnailImageAsync  
  Deze methode wordt gebruikt voor lokale afbeeldingen, waarbij de afbeelding moet worden gecodeerd als een `Stream` en moet worden verzonden naar Computer Vision door het aanroepen van de methode `ComputerVisionClient.GenerateThumbnailInStreamAsync`.
* ThumbnailUrlAsync  
  Deze methode wordt gebruikt voor externe afbeeldingen, waarbij de URL voor de afbeelding naar Computer Vision wordt verzonden door het aanroepen van de methode `ComputerVisionClient.GenerateThumbnailAsync`.

Met de methode `UploadAndThumbnailImageAsync` wordt een nieuwe instantie van `ComputerVisionClient` met de opgegeven abonnementssleutel en eindpunt-URL gemaakt. Omdat u de voorbeeld-app gebruikt voor het analyseren van een lokale afbeelding, moet de inhoud van die afbeelding worden verzonden naar Computer Vision. Hiermee wordt het lokale bestand dat is opgegeven in `imageFilePath` geopend en gelezen als een `Stream`. Hiermee wordt de methode `ComputerVisionClient.GenerateThumbnailInStreamAsync` aangeroepen, waarbij de breedte, hoogte en `Stream` voor het bestand worden doorgegeven, en of u slim bijsnijden wilt gebruiken. Het resultaat wordt geretourneerd als een `Stream`. Met de overgenomen methoden uit de klasse `ImageScenarioPage` worden de geretourneerde resultaten weergegeven in de scenariopagina.

Met de methode `RecognizeUrlAsync` wordt een nieuwe instantie van `ComputerVisionClient` met de opgegeven abonnementssleutel en eindpunt-URL gemaakt. Hiermee wordt de methode `ComputerVisionClient.GenerateThumbnailAsync` aangeroepen, waarbij de breedte, hoogte en URL voor de afbeelding worden doorgegeven, en of u slim bijsnijden wilt gebruiken. Het resultaat wordt geretourneerd als een `Stream`. Met de overgenomen methoden uit de klasse `ImageScenarioPage` worden de geretourneerde resultaten weergegeven in de scenariopagina.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze resources niet meer nodig hebt, verwijdert u de map waarin u de opslagplaats `Microsoft/Cognitive-Vision-Windows` hebt gekloond. Als u ervoor hebt gekozen de voorbeeldafbeeldingen te gebruiken, verwijdert u ook de map waarin u de opslagplaats `Microsoft/Cognitive-Face-Windows` hebt gekloond.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met Face service](../../Face/Tutorials/FaceAPIinCSharpTutorial.md)
