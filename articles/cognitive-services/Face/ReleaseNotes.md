---
title: Wat is er nieuw in de Face-service?
titleSuffix: Azure Cognitive Services
description: Release opmerkingen voor de face-service bevatten een geschiedenis van release wijzigingen voor verschillende versies.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 03/30/2021
ms.author: pafarley
ms.custom: contperf-fy21q3
ms.openlocfilehash: c580828d29e92ecef7ecc73b8f3e5843c3ecd23d
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078877"
---
# <a name="whats-new-in-face-service"></a>Wat is er nieuw in de Face-service?

De Azure face-service wordt doorlopend bijgewerkt. Gebruik dit artikel om op de hoogte te blijven van de functie verbeteringen, oplossingen en documentatie-updates.

## <a name="february-2021"></a>Februari 2021

### <a name="new-face-api-detection-model"></a>Nieuw detectie model voor Face-API
* Het nieuwe detectie 03-model is het meest nauw keurige detectie model dat momenteel beschikbaar is. Als u een nieuwe klant bent, raden we u aan dit model te gebruiken. Detectie 03 verbetert zowel inkomend als nauw keurigheid van kleinere gezichten gevonden binnen afbeeldingen (64x64 pixels). Aanvullende verbeteringen zijn onder andere een algehele verlaging van de fout-positieven en verbeterde detectie van geroteerde gezichts standen. Door detectie 03 te combi neren met het nieuwe model van de herkenning 04, wordt ook de nauw keurigheid van de herkenning verbeterd. Zie [een gezichts detectie model opgeven](./face-api-how-to-topics/specify-detection-model.md) voor meer informatie.
### <a name="new-detectable-face-attributes"></a>Nieuwe waarneem bare gezichts kenmerken
* Het `faceMask` kenmerk is beschikbaar in het meest recente model van de detectie 03, samen met het extra kenmerk `"noseAndMouthCovered"` dat detecteert of het gezichts masker is verzorgd als bedoeld, waarbij zowel de neus als de mond worden belicht. Gebruikers moeten het detectie model opgeven in de API-aanvraag om de meest recente mogelijkheid voor masker detectie te gebruiken: wijs de model versie met de para meter _detectionModel_ toe aan `detection_03` . Zie [een gezichts detectie model opgeven](./face-api-how-to-topics/specify-detection-model.md) voor meer informatie.
### <a name="new-face-api-recognition-model"></a>Nieuw herkennings model voor Face-API
* Het nieuwe model van de herkenning 04 is het meest nauw keurige herkennings model dat momenteel beschikbaar is. Als u een nieuwe klant bent, kunt u dit model het beste gebruiken voor verificatie en identificatie. Het verbetert de nauw keurigheid van de erkenning 03, met inbegrip van verbeterde herkenning van geregistreerde gebruikers (chirurgische maskers, N95 Maskers, doek maskers). Klanten kunnen nu een veilige en naadloze gebruikers ervaring bouwen die detecteert of een geregistreerde gebruiker een gezicht bedekt met het meest recente model van de detectie 03 en herkennen wie ze zijn met het laatste model van de opname 04. Zie [een gezichts herkennings model opgeven](./face-api-how-to-topics/specify-recognition-model.md) voor meer informatie.


## <a name="january-2021"></a>Januari 2021
### <a name="mitigate-latency"></a>Latentie beperken
* Het gezichts team heeft een nieuw artikel gepubliceerd met een gedetailleerde beschrijving van potentiële oorzaken van latentie bij het gebruik van de service en mogelijke beperkende strategieën. Zie [latentie beperken bij het gebruik van de face-service](./face-api-how-to-topics/how-to-mitigate-latency.md).

## <a name="december-2020"></a>December 2020
### <a name="customer-configuration-for-face-id-storage"></a>Klant configuratie voor opslag met een face-ID
* Hoewel de face-service geen afbeeldingen van klanten opslaat, worden de geëxtraheerde gezichts functie (s) opgeslagen op de server. De face-ID is een id van het gezichts onderdeel en wordt gebruikt bij [gezicht identificeren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face-verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)en [Face-vind vergelijkbaar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237). De opgeslagen gezichts functies verlopen en worden 24 uur na de oorspronkelijke detectie oproep verwijderd. Klanten kunnen nu bepalen hoe lang deze gezichts-Id's in de cache worden opgeslagen. De maximum waarde is nog Maxi maal 24 uur, maar een minimum waarde van 60 seconden kan nu worden ingesteld. De nieuwe Peri Oden voor gezichts-Id's die in de cache worden opgeslagen, zijn een waarde tussen 60 seconden en 24 uur. Meer informatie vindt u in de naslag informatie [over API (de para](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) meter *faceIdTimeToLive* ).

## <a name="november-2020"></a>November 2020
### <a name="sample-face-enrollment-app"></a>Voor beeld van een app voor het inschrijven van gezicht
* Het team heeft een voor beeld van een app voor het inschrijven van een gezicht gepubliceerd om aanbevolen procedures te demonstreren voor het vaststellen van zinvolle toestemming en het maken van herkennings systemen met een hoge nauw keurigheid via inschrijvingen van hoge kwaliteit Het open source-voor beeld is te vinden in de hand leiding [een inschrijvings-app bouwen](build-enrollment-app.md) en op [github](https://github.com/Azure-Samples/cognitive-services-FaceAPIEnrollmentSample), klaar voor ontwikkel aars om te implementeren of aan te passen. 

## <a name="august-2020"></a>Augustus 2020
### <a name="customer-managed-encryption-of-data-at-rest"></a>Door de klant beheerde versleuteling van gegevens in rust
* Met de service Face worden uw gegevens automatisch versleuteld wanneer deze persistent worden gemaakt in de Cloud. Met de face service-versleuteling worden uw gegevens beschermd, zodat u kunt voldoen aan de verplichtingen voor beveiliging en naleving van uw organisatie. Uw abonnement maakt standaard gebruik van door Microsoft beheerde versleutelingssleutels. Er is ook een nieuwe optie voor het beheren van uw abonnement met uw eigen sleutels, genaamd door de klant beheerde sleutels (CMK). Meer informatie vindt u op door de [klant beheerde sleutels](./encrypt-data-at-rest.md).

## <a name="april-2020"></a>April 2020
### <a name="new-face-api-recognition-model"></a>Nieuw herkennings model voor Face-API
* Het nieuwe model van de erkenning 03 is het meest nauw keurige model dat momenteel beschikbaar is. Als u een nieuwe klant bent, raden we u aan dit model te gebruiken. De erkenning 03 biedt een verbeterde nauw keurigheid voor de vergelijking van overeenkomsten en de matching van personen. Meer informatie vindt u in [een gezichts herkennings model opgeven](./face-api-how-to-topics/specify-recognition-model.md).

## <a name="june-2019"></a>Juni 2019

### <a name="new-face-api-detection-model"></a>Nieuw detectie model voor Face-API
* De nieuwe detectie 02-model functies verbeteren de nauw keurigheid van kleine, weer gave-, occluded-en wazige gezichten. Gebruik deze methode via [gezichts detectie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList-add](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)gezicht, [PersonGroup persoon-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) gezicht toevoegen en [LargePersonGroup persoon-gezicht](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) toevoegen door de naam van het nieuwe gezichts detectie model `detection_02` op te geven in de `detectionModel` para meter. Meer informatie [over het opgeven van een detectie model](Face-API-How-to-Topics/specify-detection-model.md).

## <a name="april-2019"></a>April 2019

### <a name="improved-attribute-accuracy"></a>Verbeterde kenmerk nauwkeurigheid
* Verbeterde algehele nauw keurigheid van de `age` `headPose` kenmerken en. Het `headPose` kenmerk wordt ook bijgewerkt met de `pitch` waarde nu ingeschakeld. Gebruik deze kenmerken door ze op te geven in de `returnFaceAttributes` para meter van de para meter [Face-detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` . 
### <a name="improved-processing-speeds"></a>Verbeterde verwerkings snelheden
* Verbeterde snelheid van [gezichts detectie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList:](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250)LargeFaceList toevoegen, [gezicht](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)toevoegen, [PersonGroup persoon-gezicht toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) en [LargePersonGroup persoon-vlak](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42) bewerkingen toevoegen.

## <a name="march-2019"></a>Maart 2019

### <a name="new-face-api-recognition-model"></a>Nieuw herkennings model voor Face-API
* De herkenning 02 model heeft een verbeterde nauw keurigheid. Gebruik deze methode via [gezichts detectie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList maken](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b), [LargeFaceList-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc), [PersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) en [LargePersonGroup-Create](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) door de nieuwe naam van het gezichts erkennings model op te geven `recognition_02` in de `recognitionModel` para meter. Meer informatie [over het opgeven van een herkennings model](Face-API-How-to-Topics/specify-recognition-model.md).

## <a name="january-2019"></a>Januari 2019

### <a name="face-snapshot-feature"></a>Functie voor gezichts momentopname
* Met deze functie kan de service gegevens migratie tussen abonnementen ondersteunen: [moment opname](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-get). Meer informatie [over het migreren van uw gezichts gegevens naar een ander gezichts abonnement](Face-API-How-to-Topics/how-to-migrate-face-data.md).

## <a name="october-2018"></a>Oktober 2018

### <a name="api-messages"></a>API-berichten
* Verfijnde beschrijving voor `status` , `createdDateTime` ,, `lastActionDateTime` en `lastSuccessfulTrainingDateTime` in [PersonGroup: trainings status ophalen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395247), [LargePersonGroup-trainings status ophalen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599ae32c6ac60f11b48b5aa5)en [LargeFaceList-trainings status](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a1582f8d2de3616c086f2cf)ophalen.

## <a name="may-2018"></a>Mei 2018

### <a name="improved-attribute-accuracy"></a>Verbeterde kenmerk nauwkeurigheid
* Verbeterd `gender` kenmerk aanzienlijk en ook verbeterd `age` , `glasses` , `facialHair` , `hair` , `makeup` kenmerken. U kunt ze gebruiken via de para meter voor de [detectie van het gezicht](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` .
### <a name="increased-file-size-limit"></a>Maximale bestands grootte
* De maximale grootte van het invoer afbeeldings bestand is van 4 MB tot 6 MB in een [gezichts detectie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [FaceList: add face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250), [LargeFaceList-add](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a158c10d2de3616c086f2d3)face, [PersonGroup persoon-](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) add gezicht en [LargePersonGroup person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adf2a3a7b9412a4d53f42).

## <a name="march-2018"></a>Maart 2018

### <a name="new-data-structure"></a>Nieuwe gegevens structuur
* [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc) en [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d). Meer informatie [over het gebruik van de grootschalige functie](Face-API-How-to-Topics/how-to-use-large-scale.md).
* Verhoogde ' [Face-identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) ' `maxNumOfCandidatesReturned` para meter van [1, 5] tot [1, 100] en standaard ingesteld op 10.

## <a name="may-2017"></a>Mei 2017

### <a name="new-detectable-face-attributes"></a>Nieuwe waarneem bare gezichts kenmerken
* Toegevoegde,,,,, en `hair` `makeup` `accessory` `occlusion` `blur` `exposure` `noise` kenmerken in de para meter voor de detectie van het [gezichts](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) kenmerk `returnFaceAttributes` .
* Ondersteunde 10.000 personen in een PersonGroup en [Face-identificeren](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
* Ondersteunde paginering in [PersonGroup person-List](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395241) met optionele para meters: `start` en `top` .
* Ondersteunde gelijktijdigheid bij het toevoegen/verwijderen van gezichten voor verschillende FaceLists en verschillende personen in PersonGroup.

## <a name="march-2017"></a>Maart 2017

### <a name="new-detectable-face-attribute"></a>Nieuw waarneembaar gezichts kenmerk
* Het `emotion` kenmerk is toegevoegd in de para meter [Face-detect](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) `returnFaceAttributes` .
### <a name="fixed-issues"></a>Opgeloste problemen
* Gezicht kan niet opnieuw worden gedetecteerd met een rechthoek die is geretourneerd door een [gezichts detectie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236) , zoals `targetFace` in [FaceList-add face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) en [PersonGroup persoon](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)toevoegen.
* De detectie grootte is ingesteld om ervoor te zorgen dat deze zich tussen 36x36 en 4096x4096 pixels bevindt.

## <a name="november-2016"></a>November 2016
### <a name="new-subscription-tier"></a>Nieuwe Subscription-laag
* Gezichtsopslag standaard abonnement toegevoegd voor het opslaan van extra persistente gezichten bij het gebruik van [PersonGroup person-add face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) of [FaceList-add gezicht](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) voor identificatie of gelijkenis met overeenkomst. De opgeslagen afbeeldingen worden als volgt in rekening gebracht: $0,5 per 1000 gezichten. Dit tarief is per dag pro rato. Abonnementen met een gratis laag blijven beperkt tot 1.000 totaal aantal personen.

## <a name="october-2016"></a>Oktober 2016
### <a name="api-messages"></a>API-berichten
* Het fout bericht van meer dan één gezicht in de van wordt gewijzigd, omdat er meer dan één gezicht in de afbeelding ' `targetFace` to ' is, is er meer dan één gezicht in de afbeelding ' in [FaceList: Face toevoegen](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250) en [PersonGroup persoon](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b)toevoegen.

## <a name="july-2016"></a>Juli 2016
### <a name="new-features"></a>Nieuwe functies
* Ondersteuning van het gezichts object voor persoons objecten in [Face-verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).
* Optionele `mode` para meter voor het inschakelen van de selectie van twee werk modi: `matchPerson` en `matchFace` in [gezicht-Find lijkt op soort gelijke](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) en standaard waarde `matchPerson` .
* De optionele `confidenceThreshold` para meter voor de gebruiker is toegevoegd om de drempel waarde in te stellen van het feit of een gezicht deel uitmaakt van een persoons object in [Face-identify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239).
* Optionele `start` en `top` para meters toegevoegd aan de [PersonGroup-lijst](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395248) om de gebruiker in staat te stellen het begin punt en het totale PersonGroups-nummer op te geven.

## <a name="v10-changes-from-v0"></a>V 1.0 wijzigingen van v0

* Het service basis eindpunt is bijgewerkt van ```https://westus.api.cognitive.microsoft.com/face/v0/``` naar ```https://westus.api.cognitive.microsoft.com/face/v1.0/``` . Wijzigingen toegepast op: [gezichts detectie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236), [gezichts identificatie](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239), [Face-Zoek vergelijk bare](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237) en [gezichts groep](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).
* De minimale Detecteer bare face-grootte is bijgewerkt naar 36x36 pixels. Vlakken die kleiner zijn dan 36x36 pixels, worden niet gedetecteerd.
* De PersonGroup en persoons gegevens in gezicht v0 zijn afgeschaft. Deze gegevens zijn niet toegankelijk met de face V 1.0-service.
* Het eind punt v0 van Face-API is afgeschaft op 30 juni 2016.