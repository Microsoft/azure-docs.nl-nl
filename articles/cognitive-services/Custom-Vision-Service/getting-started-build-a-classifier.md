---
title: 'Snelstart: Een classificatie bouwen met de Custom Vision-website'
titleSuffix: Azure Cognitive Services
description: In deze quickstart leert u hoe u met de Custom Vision-website een classificatiemodel voor afbeeldingen kunt maken, trainen en testen.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 01/29/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: afbeeldingsherkenning, app voor afbeeldingsherkenning, Custom Vision
ms.openlocfilehash: d25b226f5dfff34aa572b40d1e4de142cf8e0b7b
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221241"
---
# <a name="quickstart-build-a-classifier-with-the-custom-vision-website"></a>Snelstart: Een classificatie bouwen met de Custom Vision-website

In deze quickstart leert u hoe u met de Custom Vision-website een classificatiemodel voor afbeeldingen kunt maken. Wanneer u een model bouwt, kunt u het testen met nieuwe afbeeldingen en het ten slotte integreren in uw eigen app voor het herkennen van afbeeldingen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/cognitive-services/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

- Een reeks afbeeldingen waarmee u de classificatie kunt trainen. Hieronder vindt u tips voor het kiezen van afbeeldingen.

## <a name="create-custom-vision-resources"></a>Custom Vision-resources maken

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>Een nieuw project maken

Ga in de webbrowser naar de [Custom Vision-webpagina](https://customvision.ai) en selecteer __Aanmelden__. Meld u aan met hetzelfde account dat u hebt gebruikt om u aan te melden bij Azure Portal.

![Afbeelding van de aanmeldingspagina](./media/browser-home.png)


1. Selecteer **Nieuw project** om uw eerste project te maken. Het dialoogvenster **Nieuw project maken** wordt weergegeven.

    ![Het dialoogvenster Nieuw project bevat velden voor naam, beschrijving en domeinen.](./media/getting-started-build-a-classifier/new-project.png)

1. Voer een naam en een beschrijving voor het project in. Selecteer vervolgens een resourcegroep. Als uw aangemelde account aan een Azure-account is gekoppeld, worden in de vervolgkeuzelijst Resourcegroep al uw Azure-resourcegroepen weergegeven die een Custom Vision-serviceresource bevatten. 

   > [!NOTE]
   > Als er geen resourcegroep beschikbaar is, controleert u of u bent aangemeld bij [customvision.ai](https://customvision.ai) met hetzelfde account dat u hebt gebruikt om u aan te melden bij [Azure Portal](https://portal.azure.com/). Controleer ook of u dezelfde 'map' hebt geselecteerd in de Custom Vision-website als de map in Azure Portal waar zich uw Custom Vision-resources bevinden. Op beide sites kunt u uw map selecteren in het vervolgkeuzeaccountmenu in de rechterbovenhoek van het scherm. 

1. Selecteer __Classificatie__ onder __Projecttypen__. Kies onder __Classificatietypen__ de optie **Meerdere labels** of **Meerdere klassen**, afhankelijk van uw gebruikstoepassing. Bij classificatie met meerdere labels wordt een willekeurig aantal tags toegepast op een afbeelding (nul of meer), terwijl bij classificatie met meerdere klassen afbeeldingen in één categorie worden ingedeeld (elke afbeelding die u verzendt, wordt voorzien van de meest logische tag). Desgewenst kunt u het classificatietype later wijzigen.

1. Selecteer vervolgens een van de beschikbare domeinen. Elk domein optimaliseert de classificatie voor specifieke typen afbeeldingen, zoals beschreven in de volgende tabel. U kunt het domein later desgewenst wijzigen.

    |Domain|Doel|
    |---|---|
    |__Algemeen__| Geoptimaliseerd voor een breed scala aan afbeeldingsclassificatietaken. Als geen van de andere domeinen geschikt is, of als u niet zeker weet welk domein u moet kiezen, selecteert u het domein Algemeen. |
    |__Voedsel__|Geoptimaliseerd voor foto's van gerechten zoals op de menukaart van een restaurant. Gebruik het domein Voedsel als u foto's van afzonderlijke soorten fruit of groenten wilt classificeren.|
    |__Oriëntatiepunten__|Geoptimaliseerd voor herkenbare oriëntatiepunten, zowel natuurlijke als kunstmatige. Dit domein werkt het beste wanneer het oriëntatiepunt duidelijk te zien is in de foto. Dit domein werkt ook als het oriëntatiepunt slechts gedeeltelijk zichtbaar is omdat er mensen voor staan.|
    |__Retail__|Geoptimaliseerd voor afbeeldingen zoals die te vinden zijn in de catalogus of op de website van een winkel. Gebruik dit domein voor een nauwkeurige classificatie van kleding, zoals jurken, broeken en shirts.|
    |__Compacte domeinen__| Geoptimaliseerd voor de beperkingen van classificatie in realtime op mobiele apparaten. De modellen die door compacte domeinen worden gegenereerd, kunnen worden geëxporteerd om lokaal te worden uitgevoerd.|

1. Selecteer tot slot __Project maken__.

## <a name="choose-training-images"></a>Trainingsafbeeldingen kiezen

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>Afbeeldingen uploaden en labelen

In deze sectie gaat u afbeeldingen uploaden en handmatig taggen om de classificatie te helpen trainen. 

1. Als u installatie kopieën wilt toevoegen, selecteert u __installatie kopieën toevoegen__ en selecteert u vervolgens __lokale bestanden bladeren__. Selecteer __Openen__ om naar tags te gaan. De label selectie wordt toegepast op de hele groep installatie kopieën die u hebt geselecteerd om te uploaden. het is dus eenvoudiger om afbeeldingen in afzonderlijke groepen te uploaden op basis van de toegepaste Tags. U kunt ook de labels voor afzonderlijke afbeeldingen wijzigen nadat ze zijn geüpload.

    ![Het besturingselement om afbeeldingen toe te voegen wordt linksboven weergegeven, en als een knop onderaan in het midden.](./media/getting-started-build-a-classifier/add-images01.png)


1. Als u een tag wilt maken, voert u tekst in het veld __Mijn tags__ in en drukt u op Enter. Als de tag al bestaat, wordt deze weergegeven in een vervolgkeuzemenu. In een project met meerdere labels kunt u meer dan één tag toevoegen aan uw afbeeldingen, maar in een project met meerdere klassen kunt u slechts één tag toevoegen. Als u het uploaden van de afbeeldingen wilt voltooien, gebruikt u de knop __[aantal] bestanden uploaden__. 

    ![Afbeelding van de tag- en uploadpagina](./media/getting-started-build-a-classifier/add-images03.png)

1. Selecteer __Gereed__ zodra de afbeeldingen zijn geüpload.

    ![Op de voortgangsbalk worden alle voltooide taken weergegeven.](./media/getting-started-build-a-classifier/add-images04.png)

Als u nog een andere reeks afbeeldingen wilt uploaden, gaat u terug naar het begin van deze sectie en voert u de stappen nogmaals uit.

## <a name="train-the-classifier"></a>De classificatie trainen

Als u de classificatie wilt trainen, selecteert u de knop **Trainen**. De classificatie maakt gebruik van alle huidige afbeeldingen om een model te maken waarmee de visuele kenmerken van elke tag worden geïdentificeerd.

![De knop Trainen in de rechterbovenhoek van de kopwerkbalk op de webpagina](./media/getting-started-build-a-classifier/train01.png)

Het trainingsproces duurt normaliter slechts enkele minuten. Gedurende deze periode wordt informatie over het trainingsproces weergegeven op het tabblad **Prestaties**.

![Het browservenster met een dialoogvenster Training in de hoofdsectie](./media/getting-started-build-a-classifier/train02.png)

## <a name="evaluate-the-classifier"></a>De classificatie evalueren

Nadat de training is voltooid, worden de prestaties van het model geschat en weergegeven. De Custom Vision-service gebruikt de afbeeldingen die u voor de training hebt ingediend om de precisie en relevante overeenkomsten te berekenen met een proces dat [k-voudige kruisvalidatie](https://en.wikipedia.org/wiki/Cross-validation_(statistics)) wordt genoemd. Precisie en relevante overeenkomsten zijn twee verschillende metingen van de effectiviteit van een classificatie:

- **Precisie**: hiermee wordt de fractie correct geïdentificeerde classificaties aangegeven. Als het model bijvoorbeeld 100 afbeeldingen heeft geïdentificeerd als honden en 99 van de afbeeldingen zijn daadwerkelijk van honden, dan is de precisie 99%.
- **Relevante overeenkomsten**: hiermee wordt de fractie feitelijke classificaties aangegeven dat correct is geïdentificeerd. Als er bijvoorbeeld daadwerkelijk 100 afbeeldingen van appels waren en het model er 80 als appels heeft geïdentificeerd, is de waarde voor relevante overeenkomsten 80%.

![De resultaten van de training tonen de algehele precisie en relevante overeenkomsten, en de precisie en relevante overeenkomsten voor elke tag in de classificatie.](./media/getting-started-build-a-classifier/train03.png)

### <a name="probability-threshold"></a>Drempelwaarde waarschijnlijkheid

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

## <a name="manage-training-iterations"></a>Trainingsiteraties beheren

Telkens wanneer u de classificatie traint, maakt u een nieuwe _iteratie_ met de bijbehorende bijgewerkte metrische prestatiegegevens. U kunt alle iteraties bekijken in het linkerdeelvenster van het tabblad **Prestaties**. U vindt er ook de knop **Verwijderen**, waarmee u een iteratie kunt verwijderen als deze verouderd is. Wanneer u een iteratie verwijdert, verwijdert u alle afbeeldingen die er uniek aan zijn gekoppeld.

Zie [Uw model gebruiken met de voorspellings-API](./use-prediction-api.md) om te leren hoe u programmatisch toegang hebt tot uw getrainde modellen.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een afbeeldingsclassificatiemodel maakt en traint met behulp van de Custom Vision-website. U kunt nog meer informatie downloaden over het iteratieve proces om uw model te verbeteren.

> [!div class="nextstepaction"]
> [Een model testen en opnieuw trainen](test-your-model.md)

* [Wat is Custom Vision?](./overview.md)