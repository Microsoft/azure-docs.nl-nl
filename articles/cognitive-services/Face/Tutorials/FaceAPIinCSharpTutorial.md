---
title: 'Zelfstudie: Gegevens van gezichten in een afbeelding detecteren en weergeven met behulp van de .NET SDK'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie maakt u een Windows-app die gebruikmaakt van de Face-service om gezichten in een afbeelding te herkennen en omlijsten.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: tutorial
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 7a9caf1c1785055cbc81ef56958fe8ce2aca229c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102428300"
---
# <a name="tutorial-create-a-windows-presentation-framework-wpf-app-to-display-face-data-in-an-image"></a>Zelfstudie: Een WPF-app (Windows Presentation Framework) maken om gezichtsgegevens in een afbeelding weer te geven

In deze zelfstudie leert u hoe u de Azure Face-service via de .NET-client-SDK gebruikt om gezichten in een afbeelding te detecteren en die gegevens vervolgens in de gebruikersinterface te presenteren. U maakt een WPF-toepassing die gezichten detecteert, een kader rond elk gezicht tekent en een beschrijving van het gezicht in de statusbalk weergeeft. 

In deze zelfstudie ontdekt u hoe u:

> [!div class="checklist"]
> - Een WPF-toepassing maken
> - De Face-clientbibliotheek installeren
> - De clientbibliotheek gebruiken om gezichten in een afbeelding te detecteren
> - Een kader rond elk gedetecteerd gezicht tekenen
> - Een beschrijving van het gemarkeerde gezicht op de statusbalk weergeven

![Schermafbeelding met gedetecteerde gezichten omlijst door rechthoeken](../Images/getting-started-cs-detected.png)

De volledige voorbeeldcode is beschikbaar in de opslagplaats [Cognitive Face CSharp sample](https://github.com/Azure-Samples/Cognitive-Face-CSharp-sample) (Cognitive Face CSharp-voorbeeld) op GitHub.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint. 


## <a name="prerequisites"></a>Vereisten

* Azure-abonnement: [Krijg een gratis abonnement](https://azure.microsoft.com/free/cognitive-services/)
* Zodra u een Azure-abonnement hebt, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Een Face-resource maken"  target="_blank">maakt u een Face-resource </a> in Azure Portal om uw sleutel en eindpunt op te halen. Nadat de app is geïmplementeerd, klikt u op **Ga naar resource**.
    * U hebt de sleutel en het eindpunt nodig van de resource die u maakt, om de toepassing te verbinden met de Face-API. Later in de quickstart plakt u uw sleutel en eindpunt in de onderstaande code.
    * U kunt de gratis prijscategorie (`F0`) gebruiken om de service uit te proberen, en later upgraden naar een betaalde laag voor productie.
* [Maak omgevingsvariabelen](../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) voor de sleutel- en service-eindpunttekenreeks, met respectievelijk de namen `FACE_SUBSCRIPTION_KEY` en `FACE_ENDPOINT`.
- Een versie van [Visual Studio](https://www.visualstudio.com/downloads/).

## <a name="create-the-visual-studio-project"></a>Het Visual Studio-project maken

Volg deze stappen voor het maken van een nieuw WPF-toepassingsproject.

1. Open het dialoogvenster Nieuw project in Visual Studio. Vouw **Geïnstalleerd** en vervolgens **Visual C#** uit en selecteer **WPF App (.NET Framework)**.
1. Noem de toepassing **FaceTutorial** en klik vervolgens op **OK**.
1. Download de vereiste NuGet-pakketten. Klik met de rechtermuisknop op uw project in Solution Explorer en selecteer **NuGet-pakketten beheren**. Zoek en installeer vervolgens het volgende pakket:
    - [Microsoft.Azure.CognitiveServices.Vision.Face 2.6.0-preview.1](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Vision.Face/2.6.0-preview.1)

## <a name="add-the-initial-code"></a>De initiële code toevoegen

In deze sectie voegt u het basisframework van de app toe zonder de gezichtsbepalende kenmerken.

### <a name="create-the-ui"></a>Gebruikersinterface maken

Open *MainWindow.xaml* en vervang de inhoud door de volgende code. Met deze code wordt het venster voor de gebruikersinterface gemaakt. De methoden `FacePhoto_MouseMove` en `BrowseButton_Click` zijn gebeurtenis-handlers die u later gaat definiëren.

[!code-xaml[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml?name=snippet_xaml)]

### <a name="create-the-main-class"></a>Hoofdklasse maken

Open *MainWindow.xaml.cs* en voeg de naamruimten van de clientbibliotheek te, samen met de overige noodzakelijke naamruimten. 

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_using)]

Voeg vervolgens de volgende code in de klasse **MainWindow** in. Met deze code wordt een **FaceClient**-exemplaar gemaakt met behulp van de abonnementssleutel en het eindpunt.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_fields)]

Voeg vervolgens de constructor **MainWindow** toe. Hiermee wordt uw eindpunt-URL-tekenreeks gecontroleerd en vervolgens aan het clientobject gekoppeld.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mainwindow_constructor)]

Voeg tot slot de methoden **BrowseButton_Click** en **FacePhoto_MouseMove** aan de klasse toe. Deze methoden corresponderen met de gebeurtenis-handlers die in *MainWindow.xaml* zijn gedeclareerd. Met de methode **BrowseButton_Click** wordt het dialoogvenster **OpenFileDialog** gemaakt, waarmee de gebruiker een JPG-afbeelding kan selecteren. Daarna wordt de afbeelding in het hoofdvenster weergegeven. U voegt de resterende code voor **BrowseButton_Click** en **FacePhoto_MouseMove** later toe. Kijk ook naar de verwijzing `faceList`, een lijst met **DetectedFace**-objecten. Deze verwijzing is waar uw app de feitelijke gezichtsgegevens opslaat en aanroept.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_end)] -->

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_start)]

<!-- [!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_end)] -->

### <a name="try-the-app"></a>De app uitproberen

Druk op **Start** in het menu om uw app te testen. Als het app-venster wordt geopend, klikt u in de linkerbenedenhoek op **Bladeren**. Het dialoogvenster **Bestand openen** verschijnt. Selecteer een afbeelding in uw bestandssysteem en controleer of deze in het venster wordt weergegeven. Sluit vervolgens de app en ga door naar de volgende stap.

![Schermafbeelding met ongewijzigde afbeelding van gezichten](../Images/getting-started-cs-ui.png)

## <a name="upload-image-and-detect-faces"></a>Afbeelding uploaden en gezichten detecteren

Uw app detecteert gezichten door de methode **FaceClient.Face.DetectWithStreamAsync** aan te roepen. Deze verpakt de [Detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)-REST API voor het uploaden van een lokale afbeelding.

Voeg de volgende methode in de klasse **MainWindow** in, onder de methode **FacePhoto_MouseMove**. Met deze methode wordt een lijst met gezichtskenmerken gedefinieerd en wordt het ingediende afbeeldingsbestand in een **Stream** ingelezen. Vervolgens worden beide objecten doorgegeven aan de methodeaanroep **DetectWithStreamAsync**.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_uploaddetect)]

## <a name="draw-rectangles-around-faces"></a>Rechthoeken rond gezichten tekenen

Vervolgens voegt u de code toe om een rechthoek rond elk gedetecteerd gezicht in de afbeelding te tekenen. Voeg in de klasse **MainWindow** de volgende code toe aan het eind van de methode **BrowseButton_Click**, na de regel `FacePhoto.Source = bitmapSource`. Met deze code wordt een lijst met gedetecteerde gezichten gevuld door **UploadAndDetectFaces** aan te roepen. Vervolgens wordt een rechthoek rond elk gezicht getekend en de aangepaste afbeelding weergegeven in het hoofdvenster.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_browsebuttonclick_mid)]

## <a name="describe-the-faces"></a>De gezichten beschrijven

Voeg de volgende methode toe aan de klasse **MainWindow**, onder de methode **UploadAndDetectFaces**. Met deze methode worden de opgehaalde gezichtskenmerken geconverteerd naar een tekenreeks die het gezicht beschrijft.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_facedesc)]

## <a name="display-the-face-description"></a>De beschrijving van het gezicht weergeven

Voeg de volgende code toe aan de methode **FacePhoto_MouseMove**. Deze gebeurtenis-handler geeft de tekenreeks met de beschrijving van het gezicht in `faceDescriptionStatusBar` weer als de cursor boven een gedetecteerd vierkant rond het gezicht wordt geplaatst.

[!code-csharp[](~/Cognitive-Face-CSharp-sample/FaceTutorialCS/FaceTutorialCS/MainWindow.xaml.cs?name=snippet_mousemove_mid)]

## <a name="run-the-app"></a>De app uitvoeren

Voer de toepassing uit en blader naar een afbeelding met een gezicht. Wacht enkele seconden, zodat de Face-service kan reageren. U zou nu een rode rechthoek rond de gezichten in de afbeelding moeten zien. Als u de muisaanwijzer boven een gezichtsrechthoek beweegt, moet de beschrijving van dat gezicht op de statusbalk worden weergegeven.

![Schermafbeelding met gedetecteerde gezichten omlijst door rechthoeken](../Images/getting-started-cs-detected.png)


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het basisproces geleerd voor het gebruik van de .NET-SDK van de Face-service en een toepassing gemaakt om gezichten in een afbeelding te detecteren en omlijsten. Meer informatie over de details van gezichtsdetectie.

> [!div class="nextstepaction"]
> [Gezichten in afbeeldingen detecteren](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md)