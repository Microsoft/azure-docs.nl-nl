---
title: 'Quickstart: Een objectdetector bouwen met de Custom Vision-website'
titleSuffix: Azure Cognitive Services
description: In deze quickstart leert u hoe u met de Custom Vision-website een detectormodel voor objecten kunt maken, trainen en testen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 09/30/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: afbeeldingsherkenning, app voor afbeeldingsherkenning, Custom Vision
ms.openlocfilehash: b27864fc1fd1f94f99fccacd90a66120e6d044c4
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132576"
---
# <a name="quickstart-build-an-object-detector-with-the-custom-vision-website"></a>Quickstart: Een objectdetector bouwen met de Custom Vision-website

In deze quickstart leert u hoe u met de Custom Vision-website een detectormodel voor objecten kunt maken. Wanneer u een model bouwt, kunt u het testen met nieuwe afbeeldingen en het ten slotte integreren in uw eigen app voor het herkennen van afbeeldingen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- Een reeks afbeeldingen waarmee u het detectormodel kunt trainen. U kunt de reeks [voorbeeldafbeeldingen](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images) op GitHub gebruiken. U kunt ook uw eigen afbeeldingen kiezen met behulp van de onderstaande tips.

## <a name="create-custom-vision-resources"></a>Custom Vision-resources maken

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Een nieuw project maken

Ga in de webbrowser naar de [Custom Vision-webpagina](https://customvision.ai) en selecteer __Aanmelden__. Meld u aan met hetzelfde account dat u hebt gebruikt om u aan te melden bij Azure Portal.

![Afbeelding van de aanmeldingspagina](./media/browser-home.png)


1. Selecteer **Nieuw project** om uw eerste project te maken. Het dialoogvenster **Nieuw project maken** wordt weergegeven.

    ![Het dialoogvenster Nieuw project bevat velden voor naam, beschrijving en domeinen.](./media/get-started-build-detector/new-project.png)

1. Voer een naam en een beschrijving voor het project in. Selecteer vervolgens een resourcegroep. Als uw aangemelde account aan een Azure-account is gekoppeld, worden in de vervolgkeuzelijst Resourcegroep al uw Azure-resourcegroepen weergegeven die een Custom Vision-serviceresource bevatten. 

   > [!NOTE]
   > Als er geen resourcegroep beschikbaar is, controleert u of u bent aangemeld bij [customvision.ai](https://customvision.ai) met hetzelfde account dat u hebt gebruikt om u aan te melden bij [Azure Portal](https://portal.azure.com/). Controleer ook of u dezelfde 'map' hebt geselecteerd in de Custom Vision-website als de map in Azure Portal waar zich uw Custom Vision-resources bevinden. Op beide sites kunt u uw map selecteren in het vervolgkeuzeaccountmenu in de rechterbovenhoek van het scherm. 

1. Selecteer __Objectdetectie__ onder __Projecttypen__.

1. Selecteer vervolgens een van de beschikbare domeinen. Elk domein optimaliseert de detector voor specifieke typen afbeeldingen, zoals beschreven in de volgende tabel. U kunt het domein later desgewenst wijzigen.

    |Domain|Doel|
    |---|---|
    |__Algemeen__| Geoptimaliseerd voor een breed scala aan objectdetectietaken. Als geen van de andere domeinen geschikt is of als u niet zeker weet welk domein u moet kiezen, selecteert u het domein Algemeen. |
    |__Logo__|Geoptimaliseerd voor het vinden van merklogo's in afbeeldingen.|
    |__Producten op schappen__|Geoptimaliseerd voor het detecteren en classificeren van producten op schappen.|
    |__Compacte domeinen__| Geoptimaliseerd voor de beperkingen van objectdetectie in realtime op mobiele apparaten. De modellen die door compacte domeinen worden gegenereerd, kunnen worden geëxporteerd om lokaal te worden uitgevoerd.|

1. Selecteer tot slot __Project maken__.

## <a name="choose-training-images"></a>Trainingsafbeeldingen kiezen

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en labelen

In deze sectie gaat u afbeeldingen uploaden en handmatig taggen om de detector te helpen trainen. 

1. Als u afbeeldingen wilt toevoegen, klikt u op de knop __Afbeeldingen toevoegen__ en selecteert u __In lokale bestanden bladeren__. Selecteer __Openen__ om de afbeeldingen te uploaden.

    ![Het besturingselement om afbeeldingen toe te voegen wordt linksboven weergegeven, en als een knop onderaan in het midden.](./media/get-started-build-detector/add-images.png)

1. U ziet de geüploade afbeeldingen in de sectie **Zonder label** van de gebruikersinterface. In de volgende stap gaat u de objecten die u door de detector wilt laten herkennen, handmatig taggen. Klik op de eerste afbeelding om het dialoogvenster Taggen te openen. 

    ![Geüploade afbeeldingen in de sectie Zonder label](./media/get-started-build-detector/images-untagged.png)

1. Klik en sleep een rechthoek rond het object in uw afbeelding. Voer vervolgens een nieuwe naam voor de tag in met de knop **+** of selecteer een bestaande tag in de vervolgkeuzelijst. Het is belangrijk dat u alle exemplaren van de objecten die u wilt detecteren van een tag voorziet, omdat de detector het achtergrondgebied zonder label als een negatief voorbeeld in de training gebruikt. Wanneer u klaar bent met taggen, klikt u op de pijl aan de rechterkant om uw tags op te slaan en naar de volgende afbeelding te gaan.

    ![Een object met een rechthoekige selectie taggen](./media/get-started-build-detector/image-tagging.png)

Als u nog een andere reeks afbeeldingen wilt uploaden, gaat u terug naar het begin van deze sectie en voert u de stappen nogmaals uit.

## <a name="train-the-detector"></a>De detector trainen

Als u het detectormodel wilt trainen, selecteert u de knop **Trainen**. De detector maakt gebruik van alle huidige afbeeldingen en de bijbehorende tags om een model te maken waarmee elk getagd object wordt geïdentificeerd.

![De knop Trainen in de rechterbovenhoek van de kopwerkbalk op de webpagina](./media/getting-started-build-a-classifier/train01.png)

Het trainingsproces duurt normaliter slechts enkele minuten. Gedurende deze periode wordt informatie over het trainingsproces weergegeven op het tabblad **Prestaties**.

![Het browservenster met een dialoogvenster Training in de hoofdsectie](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>De detector evalueren

Nadat de training is voltooid, worden de prestaties van het model berekend en weergegeven. De Custom Vision-service gebruikt de afbeeldingen die u voor de training hebt ingediend om de precisie, de relevante overeenkomsten en de gemiddelde precisie te berekenen. Precisie en relevante overeenkomsten zijn twee verschillende metingen van de effectiviteit van een detector:

- **Precisie**: hiermee wordt de fractie correct geïdentificeerde classificaties aangegeven. Als het model bijvoorbeeld 100 afbeeldingen heeft geïdentificeerd als honden en 99 van de afbeeldingen zijn daadwerkelijk van honden, dan is de precisie 99%.
- **Relevante overeenkomsten**: hiermee wordt de fractie feitelijke classificaties aangegeven dat correct is geïdentificeerd. Als er bijvoorbeeld daadwerkelijk 100 afbeeldingen van appels waren en het model er 80 als appels heeft geïdentificeerd, is de waarde voor relevante overeenkomsten 80%.
- **Gemiddelde precisie** is de gemiddelde waarde van de gemiddelde precisie (AP). AP is het gebied onder de curve van precisie/relevante overeenkomsten (precisie die is uitgezet tegen relevante overeenkomsten voor elke gemaakte voorspelling).

![De trainingsresultaten tonen de algehele precisie en relevante overeenkomsten, en de gemiddelde precisie.](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>Drempelwaarde waarschijnlijkheid

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

### <a name="overlap-threshold"></a>Overlappingsdrempelwaarde

Met de slider **Overlappingsdrempelwaarde** wordt ingesteld hoe correct een objectvoorspelling moet zijn om in training 'correct' te zijn. Hiermee wordt de minimale toegestane overlap ingesteld tussen het voorspelde begrenzingsvak van het object en het werkelijke begrenzingsvak dat door de gebruiker is in gevoerd. Als de begrenzingsvakken niet in deze mate overlappen, wordt de voorspelling niet als 'correct' beschouwd.

## <a name="manage-training-iterations"></a>Trainingsiteraties beheren

Telkens wanneer u de detector traint, maakt u een nieuwe _iteratie_ met de bijbehorende bijgewerkte metrische prestatiegegevens. U kunt alle iteraties bekijken in het linkerdeelvenster van het tabblad **Prestaties**. In het linkerdeelvenster vindt u ook de knop **Verwijderen**, waarmee u een iteratie kunt verwijderen als deze verouderd is. Wanneer u een iteratie verwijdert, verwijdert u alle afbeeldingen die er uniek aan zijn gekoppeld.

Zie [Uw model gebruiken met de voorspellings-API](./use-prediction-api.md) om te leren hoe u programmatisch toegang hebt tot uw getrainde modellen.

## <a name="next-steps"></a>Volgende stappen

In deze quicktart hebt u geleerd hoe u een objectdetectiemodel maakt en traint met behulp van de Custom Vision-website. U kunt nog meer informatie downloaden over het iteratieve proces om uw model te verbeteren.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](test-your-model.md)

* [Wat is Custom Vision?](./overview.md)
