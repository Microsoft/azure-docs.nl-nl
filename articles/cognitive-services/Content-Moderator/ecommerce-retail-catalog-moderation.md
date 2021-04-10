---
title: 'Zelfstudie: E-commerce-productafbeeldingen beheren - Content Moderator'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie ziet u hoe u een toepassing instelt voor het analyseren en classificeren van productafbeeldingen met opgegeven labels (met behulp van Azure Computer Vision en Custom Vision). Tag ongewenste afbeeldingen voor verdere controle (met behulp van Azure Content Moderator).
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: tutorial
ms.date: 01/29/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: e67a9b35291615b87edf918b4146ab8d77a0ae7f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065139"
---
# <a name="tutorial-moderate-e-commerce-product-images-with-azure-content-moderator"></a>Zelfstudie: E-commerce-productafbeeldingen beheren met Azure Content Moderator

In deze zelfstudie leert u hoe u Azure Cognitive Services, waaronder Content Moderator, gebruikt om productafbeeldingen voor een e-commerce-scenario te classificeren en te beheren. U gebruikt Computer Vision en Custom Vision voor het Toep assen van tags (labels) op afbeeldingen en vervolgens maakt u een team beoordeling, waarin Content Moderator de op machine learning gebaseerde technologieën worden gecombineerd met mensen beoordelings teams om een intelligent toezicht systeem te bieden.

In deze zelfstudie ontdekt u hoe u:

> [!div class="checklist"]
> * Aanmelden voor Content Moderator en een beoordelingsteam maken.
> * De Image-API van Content Moderator gebruiken om te scannen op mogelijke ongepaste inhoud of inhoud voor volwassenen.
> * De Computer Vision-service gebruiken om te scannen op beroemdheden (of andere tags die Computer Vision kan detecteren).
> * De Custom Vision service gebruiken om te scannen op de aanwezigheid van vlaggen, speelgoed en pennen (of andere aangepaste tags).
> * De gecombineerde scanresultaten presenteren voor menselijke beoordeling en uiteindelijke besluitvorming.

De volledige voorbeeldcode is beschikbaar in de opslagplaats [Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) op GitHub.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- Een abonnementssleutel voor Content Moderator. Volg de instructies in [Een Cognitive Services-account maken](../cognitive-services-apis-create-account.md) om u te abonneren op Content Moderator en een sleutel op te halen.
- Een abonnementssleutel voor Computer Vision (dezelfde instructies als hierboven).
- Een versie van [Visual Studio 2015 of 2017](https://www.visualstudio.com/downloads/).
- Een reeks afbeeldingen voor elk label dat door de Custom Vision-classificatie wordt gebruikt (in dit geval van speelgoed, pennen en vlaggen).

## <a name="create-a-review-team"></a>Een beoordelingsteam maken

Zie de quickstart [Content Moderator proberen op internet](quick-start.md) om te lezen hoe u zich kunt aanmelden voor het beoordelingsprogramma [Content Moderator](https://contentmoderator.cognitive.microsoft.com/) en een beoordelingsteam kunt samenstellen. Noteer de waarde voor **Team Id** op de pagina **Create review team**.

## <a name="create-custom-moderation-tags"></a>Aangepaste controletags maken

Maak vervolgens aangepaste tags in het controlehulpprogramma (zie het artikel [Tags](./review-tool-user-guide/configure.md#tags) als u hierbij hulp nodig hebt). In dit geval voegen we deze tags toe: **celebrity**, **USA**, **flag**, **toy** en **pen**. Niet alle tags hoeven categorieën te zijn die kunnen worden gedetecteerd in Computer Vision (zoals **celebrity**). U kunt uw eigen aangepaste tags toevoegen, op voorwaarde dat u Custom Vision traint voor het detecteren van deze tags.

![Aangepaste tags configureren](images/tutorial-ecommerce-tags2.PNG)

## <a name="create-visual-studio-project"></a>Een Visual Studio-project maken

1. Open het dialoogvenster Nieuw project in Visual Studio. Vouw **Geïnstalleerd** en vervolgens **Visual C#** uit en selecteer **Console App (.NET Framework)**.
1. Noem de toepassing **EcommerceModeration** en selecteer **OK**.
1. Als u dit project aan een bestaande oplossing toevoegt, selecteert u dit project als het enige opstartproject.

In deze zelfstudie wordt aandacht besteed aan de code die essentieel is voor het project, maar niet elke regel met code wordt besproken. Kopieer de volledige inhoud van _Program.cs_ uit het voorbeeldproject ([Samples eCommerce Catalog Moderation](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration)) naar het bestand _Program.cs_ van het nieuwe project. Neem vervolgens de onderstaande gedeelten door voor meer informatie over de werking van het project en hoe u dit zelf kunt gebruiken.

## <a name="define-api-keys-and-endpoints"></a>API-sleutels en -eindpunten definiëren

In deze zelfstudie worden drie cognitieve services gebruikt. Daarom zijn er drie bijbehorende sleutels en API-eindpunten vereist. Bekijk de volgende velden in de klasse **Program**:

[!code-csharp[define API keys and endpoint URIs](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=21-29)]

U moet de velden `___Key` bijwerken met de waarde van de abonnementssleutels. en u moet de velden `___Uri` wijzigen in de juiste eindpunt-URL’s. (U ontvangt de sleutel en het eindpunt voor Custom Vision later.) U vindt deze waarden op de tabbladen in de **quickstart** voor elke Azure-resource. Voer in het gedeelte `YOURTEAMID` van het veld `ReviewUri` de id in van het beoordelingsteam dat u eerder hebt gemaakt. Het laatste gedeelte van het veld `CustomVisionUri` vult u later in.

[!INCLUDE [subdomains note](../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="primary-method-calls"></a>Aanroepen van primaire methode

Bekijk de volgende code in de methode **Main**, waarmee een lijst met afbeeldings-URL's wordt doorlopen. De methode analyseert elke afbeelding met behulp van de drie verschillende services, registreert de toegepaste labels in de matrix **ReviewTags** en maakt vervolgens een beoordeling voor menselijke moderators door de afbeeldingen naar het Content Moderator-controlehulpprogramma te verzenden. Deze methoden gaan we in de volgende gedeelten bespreken. Als u wilt, kunt u hier aangeven welke afbeeldingen ter beoordeling worden verzonden. Hiervoor gebruikt u de matrix **ReviewTags** in een voorwaardelijke instructie om te controleren welke tags zijn toegepast.

[!code-csharp[Main: evaluate each image and create review](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=53-70)]

## <a name="evaluateadultracy-method"></a>EvaluateAdultRacy-methode

Bekijk de methode **EvaluateAdultRacy** in de klasse **Program**. Deze methode accepteert een afbeeldings-URL en een matrix van sleutel-waardeparen als parameters. De methode roept de Image API van Content Moderator aan (met behulp van REST) om de waarden voor AdultScore en RacyScore op te vragen voor de afbeelding. Als de score voor een van de twee hoger is dan 0,4 (het bereik is tussen 0 en 1), wordt de bijbehorende waarde in de matrix **ReviewTags** ingesteld op **True**.

[!code-csharp[define EvaluateAdultRacy method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=73-113)]

## <a name="evaluatecomputervisiontags-method"></a>Methode EvaluateComputerVisionTags

De volgende methode verwerkt een afbeeldings-URL en de gegevens van uw Computer Vision-abonnement en analyseert de afbeelding op de aanwezigheid van beroemdheden. Als er een of meer beroemdheden worden gevonden in de afbeelding, wordt de bijbehorende waarde in de matrix **ReviewTags** ingesteld op **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=115-146)]

## <a name="evaluatecustomvisiontags-method"></a>EvaluateComputerVisionTags-methode

Bekijk nu de methode **EvaluateCustomVisionTags**, waarmee de daadwerkelijke producten worden geclassificeerd&mdash;in dit geval vlaggen, speelgoed en pennen. Volg de instructies in [Een classificatie bouwen](../custom-vision-service/getting-started-build-a-classifier.md) om uw eigen aangepaste afbeeldingsclassificatie te bouwen en vlaggen, speelgoed en pennen (of wat u ook maar hebt gekozen als aangepaste tags) te detecteren in afbeeldingen. U kunt de afbeeldingen in de map **sample-images** van de [GitHub-opslagplaats](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration) gebruiken om snel enkele categorieën in dit voorbeeld te trainen.

![Webpagina van Custom Vision met trainingsafbeeldingen van pennen, speelgoed en vlaggen](images/tutorial-ecommerce-custom-vision.PNG)

Als u de classificatie hebt getraind, haalt u de sleutel en de eindpunt-URL van de voorspelling op en wijst u deze toe aan respectievelijk de velden `CustomVisionKey` en `CustomVisionUri`. Zie eventueel [De URL en voorspellingssleutel ophalen](../custom-vision-service/use-prediction-api.md#get-the-url-and-prediction-key) voor instructies. De methode gebruikt deze waarden om query's uit te voeren op de classificatie. Als de classificatie een of meer van de aangepaste tags vindt in de afbeelding, worden de bijbehorende waarden in de matrix **ReviewTags** ingesteld op **True**.

[!code-csharp[define EvaluateCustomVisionTags method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=148-171)]

## <a name="create-reviews-for-review-tool"></a>Beoordelingen maken voor het beoordelingsprogramma

In de vorige secties hebt u verkend hoe met de app binnenkomende afbeeldingen worden gescand op inhoud voor volwassen en ongepaste inhoud (Content Moderator), beroemdheden (Computer Vision) en verschillende andere objecten (Custom Vision). Hieronder besteden we aandacht aan de methode **CreateReview**. Hiermee worden de afbeeldingen, samen met alle toegepaste tags (doorgegeven als _Metagegevens_), geüpload naar het Content Moderator-controlehulpprogramma.

[!code-csharp[define CreateReview method](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=173-196)]

De afbeeldingen worden weergegeven op het tabblad Review van het [beoordelingsprogramma Content Moderator](https://contentmoderator.cognitive.microsoft.com/).

![Schermafbeelding van het beoordelingsprogramma Content Moderator met verschillende afbeeldingen en de bijbehorende gemarkeerde tags](images/tutorial-ecommerce-content-moderator.PNG)

## <a name="submit-a-list-of-test-images"></a>Een lijst met testafbeeldingen aanbieden

Zoals u kunt zien in de methode **Main**, zoekt dit programma naar een map 'C:Test' met daarin een bestand _Urls.txt_ dat een lijst met afbeeldings-URL's bevat. Maak dit bestand en deze map, of wijzig het pad zodat het verwijst naar uw tekstbestand. Vul dit bestand vervolgens met de URL's van de afbeeldingen die u wilt testen.

[!code-csharp[Main: set up test directory, read lines](~/samples-eCommerceCatalogModeration/Fusion/Program.cs?range=38-51)]

## <a name="run-the-program"></a>Het programma uitvoeren

Als u alle bovenstaande stappen hebt gevolgd, worden de verschillende afbeeldingen in het programma verwerkt (waarbij in alle drie de services wordt gezocht naar de relevante tags), en worden de afbeeldingen vervolgens met de taggegevens geüpload naar het Content Moderator-controlehulpprogramma.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een programma gemaakt voor het analyseren van productafbeeldingen, deze te taggen op producttype, en een beoordelingsteam te laten beslissen of de afbeeldingen al dan niet geschikt zijn. In de volgende zelfstudie wordt meer aandacht besteed aan het beoordelen van gecontroleerde afbeeldingen.

> [!div class="nextstepaction"]
> [Gecontroleerde afbeeldingen beoordelen](./review-tool-user-guide/review-moderated-images.md)