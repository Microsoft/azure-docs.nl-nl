---
title: Video toezicht met het hulp programma beoordeling-Content Moderator
titleSuffix: Azure Cognitive Services
description: Gebruik machine-ondersteunde video toezicht en het beoordelings programma voor gepaste inhoud
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: pafarley
ms.openlocfilehash: 05ece4d8ea24fb9da106ec3daa2cc1a63706dab9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "96853495"
---
# <a name="video-moderation-with-the-review-tool"></a>Video toezicht met het hulp programma beoordeling

Content Moderator gebruik de functie voor door de machine ondersteunde video-en [beoordelings hulpmiddelen](Review-Tool-User-Guide/human-in-the-loop.md) voor [gelaagde](video-moderation-api.md) Video's en geautomatiseerde transcripten voor volwassene (expliciete) en ongepaste (suggestief) om de beste resultaten voor uw bedrijf te krijgen.

## <a name="view-videos-under-review"></a>Bekijk Video's onder beoordeling

Selecteer op het dash board een van de controle wachtrijen in het inhouds type video. Hiermee wordt een controle gestart en wordt de pagina toezicht op video-inhoud geopend.

> [!div class="mx-imgBorder"]
> ![Op Content Moderator (preview) is de schuif regelaar gemarkeerd en ingesteld op 4 Beoordelingen. De Schakel opties vervagen en zwart en wit zijn gemarkeerd en beide zijn ingesteld.](./Review-Tool-User-Guide/images/video-moderation-detailed.png)

### <a name="review-count"></a>Aantal beoordelingen

Gebruik de schuif regelaar in de rechter bovenhoek om het aantal beoordelingen in te stellen dat u op de pagina wilt weer geven.

### <a name="view-type"></a>Weergave type

U kunt de verschillende inhouds items weer geven als tegels of in een gedetailleerde weer gave. In de **detail** weergave kunt u belang rijke frames en andere informatie over de geselecteerde video bekijken. 

> [!NOTE]
> In plaats van frames met regel matige tussen pozen te laten uitvoeren, identificeert de video moderator-service alleen mogelijke voltooide (goede) frames. Met deze functie kunt u efficiënte frames genereren voor de analyse op frame niveau voor volwassenen en ongepaste.

In de **tegel** weergave wordt elke video als één tegel weer gegeven. Selecteer de knop Uitvouwen boven een video frame om die video te verg Roten en de andere te verbergen.

### <a name="content-obscuring-effects"></a>Inhoud-onduidelijke effecten

Gebruik de wissel knop **Alles vervagen** en **zwart-wit** om deze Onzichtbaare effecten in te stellen. Ze zijn standaard ingeschakeld. In de weer gave **naast elkaar** kunt u de effecten voor elke video afzonderlijk in-of uitschakelen.

## <a name="check-video-details"></a>Video details controleren

In de **detail** weergave ziet u in het rechterdeel venster verschillende tabbladen met details over de video.

* Selecteer het tabblad **notities** om aangepaste notities aan Video's toe te voegen.
* Selecteer het tabblad **transcript** voor het weer geven van de video-transcripten &mdash; die de service automatisch een transcript van een spraak in de video uitpakt. Wanneer u een sectie met tekst selecteert, gaat de video speler naar het deel van de video.
* Selecteer het tabblad **meta gegevens** om meta gegevens van video bestanden weer te geven.
* Selecteer het tabblad **geschiedenis** om de geschiedenis van de beoordeling weer te geven, zoals wanneer deze is gemaakt en hoe deze is gewijzigd.

> [!div class="mx-imgBorder"]
> ![Het rechterdeel venster is gemarkeerd en het tabblad notities is geselecteerd. Er is een test gebied met de label een notitie toevoegen.](./Review-Tool-User-Guide/images/video-moderation-video-details.png)

## <a name="apply-moderation-tags"></a>Moderator Tags Toep assen

De belangrijkste taak van een video controle is het Toep assen of verwijderen van moderator Tags op Video's of delen van Video's.

### <a name="bulk-tagging"></a>Bulk tagging

Met de werk balk **massa Tags** kunt u tags aan meerdere geselecteerde Video's tegelijk toevoegen. Selecteer een of meer Video's en selecteer vervolgens de tags die u wilt Toep assen en klik op **verzenden**. 

> [!div class="mx-imgBorder"]
> ![De knop + wordt gemarkeerd in het deel venster bulk Tags.](./Review-Tool-User-Guide/images/video-moderation-bulk-tags.png)


### <a name="key-frame-tagging"></a>Belangrijkste frame Tags

U kunt ook moderator Tags toevoegen aan specifieke keyframes. Selecteer de frames in het tegel venster van het hoofd frame en selecteer vervolgens **keyframes +** om de gewenste tags toe te passen.

> [!NOTE]
> Als de service geen keyframes kan extra heren, worden **er geen frames** weer gegeven in het tegel deel venster van het hoofd frame en wordt de optie voor het selecteren van keyframes lichter gekleurd. In dit geval kunt u alleen Tags Toep assen op de video als geheel (met behulp van de **video Tags +** knop).

> [!div class="mx-imgBorder"]
> ![De deel Vensters tegel, video speler, keyframe en video Tags worden weer gegeven. De keyframe Tags + en video Tags + knoppen zijn gemarkeerd.](./Review-Tool-User-Guide/images/video-moderation-tagging-options.png)

## <a name="put-a-review-on-hold"></a>Een beoordeling in de wacht zetten

Met de knop **vasthouden** aan de onderkant van het deel venster video kunt u een beoordeling in de wacht stand plaatsen, zodat u deze kunt ophalen om deze later te volt ooien. U kunt dit doen voor een beoordeling waarvoor een raadpleging van een andere teamlid of manager is vereist die momenteel niet beschikbaar is. 

U kunt de Video's in de wacht stand weer geven door te klikken op de knop **vasthouden** boven aan het scherm. Het deel venster vasthouden wordt aan de rechter kant weer gegeven. Hier kunt u meerdere beoordelingen in de wacht selecteren en deze weer vrijgeven in de wachtrij of de verloop tijd instellen. Na de vooraf geconfigureerde tijd worden beoordelingen in de wacht stand weer gegeven. Selecteer **Opslaan** om te beginnen met tellen vanaf de huidige geselecteerde verval tijd.

> [!div class="mx-imgBorder"]
> ![In het deel venster video is de knop vasthouden gemarkeerd. Onder aan het deel venster wordt een keuze lijst met invoervak met een wacht tijd gemarkeerd, samen met de knoppen vrijgeven en opslaan.](./Review-Tool-User-Guide/images/video-moderation-hold.png)

## <a name="submit-a-review"></a>Een beoordeling verzenden

Nadat u uw tags hebt toegepast, selecteert u de knop **verzenden** onder in het deel venster video. Als u meerdere Video's hebt gelabeld, kunt u deze onder één beoordeling of als afzonderlijke beoordelingen verzenden.

## <a name="limbo-state"></a>Limbo-status

Nadat u een beoordeling hebt verzonden, wordt de video verplaatst naar de status **Limbo** , die u kunt bekijken door de knop **Limbo** boven aan het scherm te selecteren. Video's blijven in de Limbo-status voor een vooraf geconfigureerde hoeveelheid tijd (die u in het menu onderaan kunt wijzigen) of totdat ze opnieuw worden gecontroleerd of hand matig zijn verzonden.

Zodra de Video's van Limbo verlopen, worden hun beoordelingen als voltooid gemarkeerd.

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met de [Snelstartgids voor video toezicht](video-moderation-api.md).
- Meer informatie over het genereren van [video beoordelingen](video-reviews-quickstart-dotnet.md) voor uw menselijke revisoren vanuit uw gecontroleerde uitvoer.
- Voeg [Video-Transcript beoordelingen](video-transcript-reviews-quickstart-dotnet.md) toe aan uw video Beoordelingen.
