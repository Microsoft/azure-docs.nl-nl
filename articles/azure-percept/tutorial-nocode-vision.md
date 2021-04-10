---
title: Een oplossing voor No-code Vision maken in azure percept Studio
description: Meer informatie over het maken van een oplossing voor No-code Vision in azure percept Studio en het implementeren ervan in uw Azure percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: 49e5db729dab7abaa440b1adf6a61e9e52a1efbc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023126"
---
# <a name="create-a-no-code-vision-solution-in-azure-percept-studio"></a>Een oplossing voor No-code Vision maken in azure percept Studio

Met Azure percept Studio kunt u aangepaste computer Vision-oplossingen bouwen en implementeren, maar geen code ring vereist. In dit artikel gaat u het volgende doen:

- Een Vision-project maken in [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)
- Trainings afbeeldingen verzamelen met uw Devkit
- Voorzie uw trainings afbeeldingen in [Custom Vision](https://www.customvision.ai/)
- Uw aangepaste object detectie of classificatie model trainen
- Implementeer uw model naar uw Devkit
- Verbeter uw model door trainingen in te stellen

Deze zelf studie is geschikt voor ontwikkel aars met weinig of geen AI-ervaring en die alleen aan de slag met Azure percept.

## <a name="prerequisites"></a>Vereisten

- Azure percept DK (Devkit)
- [Azure-abonnement](https://azure.microsoft.com/free/)
- Setup van Azure percept DK: u hebt uw Devkit met een Wi-Fi-netwerk verbonden, een IoT Hub gemaakt en uw Devkit verbonden met de IoT Hub

## <a name="create-a-vision-prototype"></a>Een visueel model maken

1. Start uw browser en ga naar [Azure percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).

1. Klik op de pagina overzicht op het tabblad **demo's & zelf studies** .  :::image type="content" source="./media/tutorial-nocode-vision/percept-studio-overview-inline.png" alt-text="Overzichts scherm van Azure percept Studio." lightbox="./media/tutorial-nocode-vision/percept-studio-overview.png":::

1. Klik onder **visuele zelf studies en demo's** op **een visie prototype maken**.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-tutorials-and-demos-inline.png" alt-text="Het scherm demo's en zelf studies van Azure percept Studio." lightbox="./media/tutorial-nocode-vision/vision-tutorials-and-demos.png":::

1. Ga als volgt te werk op de pagina **nieuwe Azure Percept Custom Vision-prototype** :

    1. Voer in het vak **project naam** een naam in voor uw Vision-prototype.

    1. Voer een beschrijving in van het Vision prototype in het vak Beschrijving van het **project** .

    1. Selecteer **Azure PERCEPT DK** in het vervolg keuzemenu **Apparaattype** .

    1. Selecteer een resource in de vervolg keuzelijst **resource** of klik op **een nieuwe resource maken**. Als u ervoor kiest om een nieuwe resource te maken, gaat u als volgt te werk in het venster **maken** :
        1. Voer een naam in voor de nieuwe resource.
        1. Selecteer uw Azure-abonnement.
        1. Selecteer een resource groep of maak een nieuwe.
        1. Selecteer uw voorkeursregio.
        1. Selecteer uw prijs categorie (we raden S0 aan).
        1. Klik onder aan het venster op **maken** .

        :::image type="content" source="./media/tutorial-nocode-vision/create-resource.png" alt-text="Resource venster maken.":::

    1. Kies bij **project type** of uw Vision-project object detectie of afbeeldings classificatie moet uitvoeren. Klik voor meer informatie over de project typen op **Help mij kiezen**.

    1. Voor **Optima Lise ring** selecteert u of u uw project wilt optimaliseren voor nauw keurigheid, lage netwerk latentie of een saldo van beide.

    1. Klik op de knop **Maken**.

        :::image type="content" source="./media/tutorial-nocode-vision/create-prototype.png" alt-text="Een aangepast gezichts prototype-scherm maken.":::

## <a name="connect-a-device-to-your-project-and-capture-images"></a>Een apparaat verbinden met uw project en installatie kopieën vastleggen

Nadat u een Vision-oplossing hebt gemaakt, moet u uw Devkit en de bijbehorende IoT Hub eraan toevoegen.

1. Schakel uw Devkit uit.

1. Selecteer in het vervolg keuzemenu **IOT hub** de IOT-hub waarmee uw Devkit was verbonden tijdens het OOBE.

1. Selecteer uw Devkit in het vervolg keuzemenu **apparaten** .

Vervolgens moet u installatie kopieën laden of installatie kopieën vastleggen voor het trainen van uw AI-model. We raden ten minste 30 afbeeldingen per label type te uploaden. Als u bijvoorbeeld een hond en kat detector wilt maken, moet u ten minste 30 installatie kopieën van honden en 30 kopieën van katten uploaden. Ga als volgt te werk om installatie kopieën vast te leggen met het gezichts vermogen van uw Devkit:

1. Selecteer in het venster **installatie kopie vastleggen** de optie **apparaatgegevens weer geven** om de zichte SoM-video stroom weer te geven.

1. Controleer de video stroom om er zeker van te zijn dat uw Vision-SoM-camera goed is afgestemd op het nemen van de trainings afbeeldingen. Breng de gewenste wijzigingen aan.

1. Klik in het venster **installatie kopie vastleggen** op **foto nemen**.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture.png" alt-text="Scherm voor het vastleggen van installatie kopieën.":::

1. U kunt ook een automatische installatie kopie van een afbeelding instellen om een grote hoeveelheid installatie kopieën tegelijk te verzamelen door het selectie vakje voor het **automatisch vastleggen van installatie kopieën** in te scha kelen. Selecteer de gewenste Imaging-rate onder de **opname frequentie** en het totale aantal installatie kopieën dat u wilt verzamelen onder het **doel**. Klik op **automatisch vastleggen instellen** om te beginnen met het automatisch vastleggen van de installatie kopie.

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture-auto.png" alt-text="Automatisch vervolg keuzemenu voor het vastleggen van afbeeldingen.":::

Wanneer u voldoende Foto's hebt, klikt u op **volgende: Codeer afbeeldingen en model training** onder aan het scherm. Alle installatie kopieën worden opgeslagen in [Custom Vision](https://www.customvision.ai/).

> [!NOTE]
> Als u ervoor kiest om trainings afbeeldingen rechtstreeks naar Custom Vision te uploaden, moet u er rekening mee houden dat afbeeldings bestand niet groter mag zijn dan 6 MB.

## <a name="tag-images-and-train-your-model"></a>Codeer afbeeldingen en Train uw model

Voordat u uw model hebt getraind, voegt u labels toe aan uw installatie kopieën.

1. Klik op de pagina **code-installatie kopieën en model trainingen** op **project openen in Custom Vision**.

1. Klik aan de linkerkant van de pagina **Custom Vision** op niet- **gelabeld** onder **labels** om de afbeeldingen weer te geven die u in de vorige stap hebt verzameld. Selecteer een of meer van uw niet-gecodeerde installatie kopieën.

1. Klik in het venster **afbeeldings Details** op de afbeelding om te beginnen met labelen. Als u object detectie als uw project type hebt geselecteerd, moet u ook een [omsluitend kader](../cognitive-services/custom-vision-service/get-started-build-detector.md#upload-and-tag-images) tekenen rond specifieke objecten die u wilt labelen. Pas het selectie kader naar wens aan. Typ uw object code en klik **+** om de tag toe te passen. Als u bijvoorbeeld een Vision-oplossing maakt waarmee u op de hoogte zou worden gesteld wanneer een Store-plank opnieuw moet worden besteld, voegt u het label ' empty schap ' toe aan afbeeldingen van lege schappen en voegt u het label ' volledig schap ' toe aan afbeeldingen van de archiefen met volledige aandelen. Herhaal deze stap voor alle niet-gelabelde afbeeldingen.

    :::image type="content" source="./media/tutorial-nocode-vision/image-tagging.png" alt-text="Het scherm voor het coderen van afbeeldingen in Custom Vision.":::

1. Nadat u de afbeeldingen hebt gelabeld, klikt u op het pictogram **X** in de rechter bovenhoek van het venster. Klik op **gelabeld** onder **labels** om al uw nieuw gelabelde afbeeldingen weer te geven.

1. Nadat uw installatie kopieën zijn gelabeld, bent u klaar om uw AI-model te trainen. Hiertoe klikt u op **Train** aan de bovenkant van de pagina. U moet ten minste 15 installatie kopieën per label type hebben om uw model te trainen (we raden u aan om ten minste 30 te gebruiken). De training duurt meestal ongeveer 30 minuten, maar het kan langer duren als uw installatie kopie is ingesteld op extreem grote.

    :::image type="content" source="./media/tutorial-nocode-vision/train-model.png" alt-text="Keuze van de trainings afbeelding met de knop trainen gemarkeerd.":::

1. Wanneer de training is voltooid, worden in uw scherm de prestaties van uw model weer gegeven. Raadpleeg de [documentatie voor model-evaluatie](../cognitive-services/custom-vision-service/get-started-build-detector.md#evaluate-the-detector)voor meer informatie over het evalueren van deze resultaten. Na de training wilt u mogelijk ook [uw model](../cognitive-services/custom-vision-service/test-your-model.md) op extra installatie kopieën testen en indien nodig opnieuw trainen. Telkens wanneer u uw model traint, wordt het opgeslagen als een nieuwe iteratie. Raadpleeg de [documentatie van Custom Vision](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md) voor meer informatie over het verbeteren van de prestaties van modellen.

    :::image type="content" source="./media/tutorial-nocode-vision/iteration.png" alt-text="Model trainings resultaten.":::

    > [!NOTE]
    > Als u ervoor kiest om uw model op extra installatie kopieën in Custom Vision te testen, moet u er rekening mee houden dat de grootte van de test afbeelding niet groter mag zijn dan 4 MB.

Wanneer u tevreden bent met de prestaties van uw model, sluit u Custom Vision door het tabblad browser te sluiten.

## <a name="deploy-your-ai-model"></a>Uw AI-model implementeren

1. Ga terug naar het tabblad Azure percept Studio en klik op **volgende: evalueren en implementeren** onder aan het scherm.

1. In het venster **evalueren en implementeren** worden de prestaties van de geselecteerde model herhaling weer gegeven. Selecteer de herhaling die u wilt implementeren naar uw Devkit in het vervolg keuzemenu **model iteratie** en klik onder aan het scherm op **model implementeren** .

    :::image type="content" source="./media/tutorial-nocode-vision/deploy-model-inline.png" alt-text="Model implementatie scherm." lightbox="./media/tutorial-nocode-vision/deploy-model.png":::

1. Nadat u het model hebt geïmplementeerd, bekijkt u de video stroom van uw apparaat om te zien hoe het model in actie wordt afgedragen.

    :::image type="content" source="./media/tutorial-nocode-vision/view-device-stream.png" alt-text="De stroom van het apparaat met de hoofdtelefoon detector in actie.":::

Na het sluiten van dit venster kunt u op elk gewenst moment teruggaan en uw gezichts project bewerken door te klikken op **Vision** onder **AI-projecten** op de Azure percept Studio-start pagina en de naam van uw Vision-project te selecteren.

:::image type="content" source="./media/tutorial-nocode-vision/vision-project-inline.png" alt-text="Vision project-pagina." lightbox="./media/tutorial-nocode-vision/vision-project.png":::

## <a name="improve-your-model-by-setting-up-retraining"></a>Verbeter uw model door trainingen in te stellen

Nadat u uw model hebt getraind en op het apparaat hebt geïmplementeerd, kunt u de model prestaties verbeteren door para meters voor opnieuw trainen in te stellen voor het vastleggen van meer trainings gegevens. Deze functie wordt gebruikt voor het verbeteren van de prestaties van een getraind model door u de mogelijkheid te bieden installatie kopieën op te nemen op basis van een waarschijnlijkheids bereik. U kunt bijvoorbeeld instellen dat uw apparaat alleen trainings afbeeldingen vastlegt wanneer de kans laag is. Hier vindt u een aantal [aanvullende richt lijnen](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md) voor het toevoegen van meer afbeeldingen en het verdelen van trainings gegevens.

1. Als u trainingen wilt instellen, gaat u terug naar uw **project** en vervolgens naar **project Summary**
1. Selecteer in het tabblad **vastleggen van installatie kopie** de optie **automatische installatie kopie vastleggen** en **Stel opnieuw training** in.
1. Stel het vastleggen van automatische installatie kopieën in om een grote hoeveelheid installatie kopieën tegelijk te verzamelen door het selectie vakje voor **automatische installatie kopieën** in te scha kelen.
1. Selecteer de gewenste Imaging-rate onder de **opname frequentie** en het totale aantal installatie kopieën dat u wilt verzamelen onder het **doel**.
1. Selecteer in de sectie **retraining instellen** de herhaling waarvoor u meer trainings gegevens wilt vastleggen en selecteer vervolgens het waarschijnlijkheids bereik. Alleen installatie kopieën die voldoen aan het waarschijnlijkheids tempo, worden geüpload naar uw project.

    :::image type="content" source="./media/tutorial-nocode-vision/vision-image-capture.png" alt-text="vastleggen van installatie kopie.":::

## <a name="clean-up-resources"></a>Resources opschonen

Als u een nieuwe Azure-resource hebt gemaakt voor deze zelf studie en u uw Vision-oplossing niet meer wilt ontwikkelen of gebruiken, voert u de volgende stappen uit om de resource te verwijderen:

1. Ga naar de [Azure Portal](https://ms.portal.azure.com/).
1. Klik op **alle resources**.
1. Klik op het selectie vakje naast de resource die u tijdens deze zelf studie hebt gemaakt. Het resource type wordt vermeld als **Cognitive Services**.
1. Klik op het pictogram **verwijderen** aan de bovenkant van het scherm.

## <a name="video-walkthrough"></a>Video-overzicht

Raadpleeg de volgende video voor een visueel overzicht van de stappen die hierboven worden beschreven:

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

</br>

## <a name="next-steps"></a>Volgende stappen

Bekijk vervolgens de visioning-artikelen voor meer informatie over aanvullende functies van de oplossing in azure percept Studio.

<!--
Add links to how-to articles and oobe article.
-->