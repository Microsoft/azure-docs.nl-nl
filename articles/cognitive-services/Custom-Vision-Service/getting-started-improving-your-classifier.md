---
title: Uw model Custom Vision Service verbeteren
titleSuffix: Azure Cognitive Services
description: In dit artikel leert u hoe het aantal, de kwaliteit en de verscheidenheid van de gegevens de kwaliteit van uw model in de Custom Vision-service kunnen verbeteren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 328bfe57c675d49aa951388e2808fcecfe8da8b5
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100096528"
---
# <a name="how-to-improve-your-custom-vision-model"></a>Uw Custom Vision model verbeteren

In deze hand leiding leert u hoe u de kwaliteit van uw Custom Vision Service model kunt verbeteren. De kwaliteit van uw [classificatie](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) of [object detectie](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/get-started-build-detector) is afhankelijk van het aantal, de kwaliteit en de verscheidenheid van de gelabelde gegevens die u opgeeft en de verdeling van de algemene gegevensset. Een goed model heeft een gebalanceerde trainings gegevensset die representatief is voor wat er wordt verzonden. Het proces van het bouwen van een dergelijk model is een terugkerende methode; het is gebruikelijk om enkele ronden te volgen om de verwachte resultaten te bereiken.

Hier volgt een algemeen patroon waarmee u een nauw keuriger model kunt trainen:

1. First-Round training
1. Meer installatie kopieën en balans gegevens toevoegen; opnieuw trainen
1. Voeg afbeeldingen met verschillende achtergrond, belichting, object grootte, camera hoek en stijl toe. opnieuw trainen
1. Nieuwe afbeelding (en) gebruiken om voor spelling te testen
1. Bestaande trainings gegevens aanpassen op basis van voorspellings resultaten

## <a name="prevent-overfitting"></a>Overmontage voor komen

Soms leert een model om voor spellingen te maken op basis van wille keurige kenmerken die uw afbeeldingen gemeen hebben. Als u bijvoorbeeld een classificatie maakt voor Apples vs. citrus en u afbeeldingen van appels in handen hebt en van citrus op witte platen hebt gebruikt, kan de classificatie onterecht belang rijk zijn voor handen van de hand, in plaats van met Apples vs. Citrus.

![Afbeelding van onverwachte classificatie](./media/getting-started-improving-your-classifier/unexpected.png)

Als u dit probleem wilt corrigeren, geeft u installatie kopieën op met verschillende hoeken, achtergronden, object grootte, groepen en andere variaties. De volgende secties worden op deze concepten uitgebreid.

## <a name="data-quantity"></a>Hoeveelheid gegevens

Het aantal trainings afbeeldingen is de belangrijkste factor voor uw gegevensset. U kunt het beste ten minste 50 installatie kopieën per label gebruiken als uitgangs punt. Als er minder afbeeldingen zijn, is er een hoger risico op de overmontage en terwijl uw prestatie cijfers een goede kwaliteit kunnen Voorst Ellen, kan uw model moeilijk met echte gegevens worden gerealiseerd. 

## <a name="data-balance"></a>Gegevens saldo

Het is ook belang rijk dat u rekening houdt met de relatieve hoeveel heden van uw trainings gegevens. Bijvoorbeeld: met 500 installatie kopieën voor één label en 50 installatie kopieën voor een ander label wordt een niet-sluitende trainings gegevensset gemaakt. Dit zorgt ervoor dat het model nauw keuriger is bij het voors pellen van één label dan het andere. U ziet waarschijnlijk betere resultaten als u ten minste een 1:2-verhouding tussen het label en de minste afbeeldingen en het label met de meeste afbeeldingen wilt behouden. Als het label met de meeste installatie kopieën 500 installatie kopieën heeft, moet het label met de minste installatie kopieën ten minste 250 installatie kopieën voor de training hebben.

## <a name="data-variety"></a>Gegevens soort

Zorg ervoor dat u installatie kopieën gebruikt die representatief zijn voor wat wordt verzonden naar de classificatie tijdens normaal gebruik. Anders kan uw model leren om voor spellingen te maken op basis van wille keurige kenmerken die uw installatie kopieën gemeen hebben. Als u bijvoorbeeld een classificatie maakt voor Apples vs. citrus en u afbeeldingen van appels in handen hebt en van citrus op witte platen hebt gebruikt, kan de classificatie onterecht belang rijk zijn voor handen van de hand, in plaats van met Apples vs. Citrus.

![Afbeelding van onverwachte classificatie](./media/getting-started-improving-your-classifier/unexpected.png)

U kunt dit probleem oplossen door diverse installatie kopieën te gebruiken om ervoor te zorgen dat uw model goed kan worden gegeneraliseerd. Hieronder vindt u enkele manieren waarop u uw training kunt instellen:

* __Achtergrond:__ Geef afbeeldingen van uw object voor de verschillende achtergronden op. Foto's in natuurlijke contexten zijn beter dan Foto's voor neutrale achtergronden, aangezien ze meer informatie over de classificatie bieden.

    ![Afbeelding van achtergrond voorbeelden](./media/getting-started-improving-your-classifier/background.png)

* __Verlichting:__ Bied installatie kopieën met een variabele verlichting (die wordt gemaakt met Flash, hoge bloot stelling enzovoort), met name als de installatie kopieën die worden gebruikt voor de voor spelling, een andere belichting hebben. Het is ook handig om installatie kopieën te gebruiken met een variërende intensiteit, kleur Toon en helderheid.

    ![Afbeelding van belichtings voorbeelden](./media/getting-started-improving-your-classifier/lighting.png)

* __Object grootte:__ Geef installatie kopieën op waarin de objecten qua grootte en cijfer variëren (bijvoorbeeld een foto van een aantal bananen en een close van één bananen). Met een andere grootte kunt u de classificatie generalize verbeteren.

    ![Afbeelding van grootte voorbeelden](./media/getting-started-improving-your-classifier/size.png)

* __Camera hoek:__ Geef installatie kopieën op die zijn gemaakt met verschillende camera hoeken. Als al uw Foto's moeten worden uitgevoerd met vaste camera's (zoals surveillance camera's), moet u er ook voor zorgen dat u een ander label toewijst aan elk regel matig object, om te voor komen dat niet- &mdash; gerelateerde objecten (zoals lampposts) worden geïnterpreteerd als de belangrijkste functie.

    ![Afbeelding van hoek voorbeelden](./media/getting-started-improving-your-classifier/angle.png)

* __Stijl:__ Geef afbeeldingen van verschillende stijlen van dezelfde klasse op (bijvoorbeeld verschillende variëteiten van hetzelfde fruit). Als u echter objecten van zeer verschillende stijlen hebt (zoals Mickey Mouse versus een Real-Life muis), raden we u aan ze als afzonderlijke klassen aan te duiden, zodat ze beter hun afzonderlijke functies vertegenwoordigen.

    ![Afbeelding van stijl voorbeelden](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images-classifiers-only"></a>Negatieve afbeeldingen (alleen classificaties)

Als u een afbeeldings classificatie gebruikt, moet u mogelijk negatieve voor _beelden_ toevoegen om uw classificatie nauw keuriger te maken. Negatieve voor beelden zijn afbeeldingen die niet overeenkomen met een van de andere tags. Wanneer u deze installatie kopieën uploadt, moet u de speciale **negatieve** label hierop Toep assen.

Object detecties verwerken negatieve voor beelden automatisch, omdat alle afbeeldings gebieden buiten de getekende grenzen als negatief worden beschouwd.

> [!NOTE]
> Het Custom Vision Service ondersteunt een automatische negatieve verwerking van de afbeelding. Als u bijvoorbeeld een wijn van druiven versus bananen bouwt en een afbeelding van een schoenen voor voor spelling verzendt, moet de classificatie die afbeelding even nauw keurig afzetten tot 0% voor zowel druiven als bananen.
> 
> Aan de andere kant, in gevallen waarin de negatieve installatie kopieën slechts een variant zijn van de afbeeldingen die in de training worden gebruikt, is het waarschijnlijk dat het model de negatieve afbeeldingen als een klasse met labels geclassificeerd als gevolg van de fantastische overeenkomsten. Als u bijvoorbeeld een oranje-en grapefruit-classificatie hebt en u een afbeelding van een Clementine invoert, kan de Clementine als oranje worden gezien, omdat veel kenmerken van de Clementine lijken op die van sinaasappels. Als uw negatieve installatie kopieën van deze aard zijn, raden we u aan om een of meer extra tags (zoals **andere**) te maken en de negatieve afbeeldingen met deze tag te labelen tijdens de training, zodat het model beter kan worden gedifferentieerd tussen deze klassen.

## <a name="consider-occlusion-and-truncation-object-detectors-only"></a>Bedekking en afkap ping overwegen (alleen object detecties)

Als u wilt dat de object detectie afgekapte objecten detecteert (het object is gedeeltelijk uitgesneden van de afbeelding) of occluded-objecten (het object wordt gedeeltelijk geblokkeerd door een ander object in de afbeelding), moet u trainings afbeeldingen voor die gevallen toevoegen.

> [!NOTE]
> Het probleem van objecten die door andere objecten worden occluded, kan niet worden verward met een **overlappings drempel**, een para meter voor prestatie classificatie model. De schuif regelaar voor **overlappings drempelwaarde** op de [Custom Vision-website](https://customvision.ai) behandelt hoeveel een voorspeld begrenzingsvak moet overlappen met het vak waar wordt aangegeven dat het als correct wordt beschouwd.

## <a name="use-prediction-images-for-further-training"></a>Voorspellings afbeeldingen gebruiken voor verdere training

Wanneer u het model gebruikt of test door installatie kopieën naar het Voorspellings eindpunt te verzenden, slaat de Custom Vision-service deze installatie kopieën op. U kunt ze vervolgens gebruiken om het model te verbeteren.

1. Als u afbeeldingen wilt bekijken die naar het model zijn verzonden, opent u de [Custom Vision-webpagina](https://customvision.ai), gaat u naar uw project en selecteert u het tabblad voor __spellingen__ . In de standaard weergave worden afbeeldingen van de huidige herhaling weer gegeven. U kunt de vervolg keuzelijst __iteratie__ gebruiken om afbeeldingen weer te geven die zijn verzonden tijdens vorige iteraties.

    ![scherm afbeelding van het tabblad voor spellingen met afbeeldingen in de weer gave](./media/getting-started-improving-your-classifier/predictions.png)

2. Beweeg de muis aanwijzer over een afbeelding om de labels te zien die voor het model zijn voor speld. Afbeeldingen worden gesorteerd, zodat de meeste verbeteringen van het model worden weer gegeven bovenaan. Als u een andere sorteer methode wilt gebruiken, maakt u een selectie in het gedeelte __sorteren__ . 

    Als u een afbeelding wilt toevoegen aan uw bestaande trainings gegevens, selecteert u de installatie kopie, stelt u de juiste code (s) in en klikt u op __opslaan en sluiten__. De afbeelding wordt verwijderd uit de voor __spellingen__ en toegevoegd aan de set trainings afbeeldingen. U kunt deze weer geven door het tabblad __trainings afbeeldingen__ te selecteren.

    ![Afbeelding van de pagina labelen](./media/getting-started-improving-your-classifier/tag.png)

3. Gebruik vervolgens de __trein__ knop om het model opnieuw te trainen.

## <a name="visually-inspect-predictions"></a>Voor spellingen visueel controleren

Als u afbeeldings voorspellingen wilt controleren, gaat u naar het tabblad __trainings afbeeldingen__ , selecteert u uw vorige trainingens iteratie in de vervolg keuzelijst **iteratie** en controleert u een of meer tags onder de sectie **Tags** . In de weer gave wordt nu een rood vak weer gegeven rond elk van de afbeeldingen waarvoor het model de opgegeven tag niet correct kan voors pellen.

![Afbeelding van de iteratie geschiedenis](./media/getting-started-improving-your-classifier/iteration.png)

Soms kan een visuele inspectie patronen identificeren die u vervolgens kunt corrigeren door meer trainings gegevens toe te voegen of bestaande trainings gegevens te wijzigen. Bijvoorbeeld: een classificatie voor Apples vergeleken met kalkten kan alle groene appels onjuist labelen als kalkten. U kunt dit probleem vervolgens verhelpen door trainings gegevens toe te voegen en te voorzien die gelabelde afbeeldingen van groene Apple bevatten.

## <a name="next-steps"></a>Volgende stappen

In deze hand leiding hebt u verschillende technieken geleerd om uw aangepaste afbeeldings classificatie model of object detector model nauw keuriger te maken. Vervolgens leert u hoe u afbeeldingen programmatisch kunt testen door ze naar de Voorspellings-API te verzenden.

> [!div class="nextstepaction"]
> [De voorspellings-API gebruiken](use-prediction-api.md)
