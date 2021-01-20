---
title: De Video Indexer editor gebruiken om projecten te maken en video clips toe te voegen
titleSuffix: Azure Media Services
description: In dit onderwerp ziet u hoe u de Video Indexer editor kunt gebruiken om projecten te maken en video clips toe te voegen.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 11/28/2020
ms.author: juliako
ms.openlocfilehash: b25341fb58c1e758d807e3c7b4345fd0ab1baa53
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610417"
---
# <a name="add-video-clips-to-your-projects"></a>Video clips toevoegen aan uw projecten

Met de [video indexer](https://www.videoindexer.ai/) -website kunt u de geavanceerde inzichten van uw Video's gebruiken om de juiste media-inhoud te zoeken, de onderdelen te zoeken waarin u geïnteresseerd bent en de resultaten te gebruiken om een volledig nieuw project te maken. 

Nadat het project is gemaakt, kan het worden gerenderd en gedownload van Video Indexer en worden gebruikt in uw eigen bewerkings toepassingen of downstream-werk stromen.

Enkele scenario's waarin deze functie nuttig is, zijn: 

* Film hooglichten voor aanhang wagens maken.
* Het gebruik van oude video clips in Nieuws casts.
* Kortere inhoud maken voor sociale media.

In dit artikel wordt beschreven hoe u een project maakt en geselecteerde clips uit de Video's toevoegt aan het project. 

## <a name="create-new-project-and-manage-videos"></a>Nieuw project maken en Video's beheren

1. Ga naar de [Video Indexer](https://www.videoindexer.ai/)-website en meld u aan.
1. Selecteer het tabblad **projecten** . Als u eerder projecten hebt gemaakt, worden hier al uw andere projecten weer gegeven.
1. Klik op **Nieuw project maken**.  

    :::image type="content" source="./media/video-indexer-view-edit/new-project.png" alt-text="Een nieuw project maken":::
1. Geef uw project een naam door te klikken op het potlood pictogram. Vervang de tekst "naamloos project" door de naam van uw project en klik op de controle.

    :::image type="content" source="./media/video-indexer-view-edit/new-project-edit-name.png" alt-text="Een nieuw project":::
    
### <a name="add-videos-to-the-project"></a>Video's toevoegen aan het project

> [!NOTE]
> Op dit moment kunnen projecten alleen Video's bevatten die in dezelfde taal zijn geïndexeerd. </br>Zodra u een video in de ene taal selecteert, kunt u de Video's in uw account in een andere taal toevoegen. de Video's met andere talen worden grijs weer gegeven/uitgeschakeld.

1. Voeg Video's toe waarmee u in dit project wilt werken door **Video's toevoegen** te selecteren.

    U ziet alle Video's in uw account en een zoekvak dat "zoeken naar tekst, tref woorden of visuele inhoud" bevat. U kunt zoeken naar Video's met een opgegeven persoon, label, merk, tref woord of voorval in de transcripten en OCR.
    
    In de onderstaande afbeelding kijken we bijvoorbeeld naar Video's waarin ' aangepaste visie ' alleen in Transcripten wordt vermeld ( **filter** gebruiken als u de zoek resultaten wilt filteren).
    
    :::image type="content" source="./media/video-indexer-view-edit/custom-vision.png" alt-text="Scherm afbeelding toont het zoeken naar Video's waarin aangepaste visie wordt vermeld":::
1. Klik op **toevoegen** om Video's aan het project toe te voegen.
1. U ziet nu alle Video's die u hebt gekozen. Dit zijn de Video's waaruit u clips gaat selecteren voor uw project.

    U kunt de volg orde van de Video's wijzigen door te slepen en neer te zetten of door de menu knop lijst te selecteren en **omlaag** of omhoog te **gaan**. In het menu lijst kunt u ook de video uit dit project verwijderen. 
    
    U kunt op elk gewenst moment meer Video's toevoegen aan dit project door **Video's toevoegen** te selecteren. U kunt ook meerdere exemplaren van dezelfde video toevoegen aan uw project. U kunt dit doen als u een clip van de ene video wilt weer geven en vervolgens een clip van een andere clip en vervolgens in de eerste video wilt maken. 

### <a name="select-clips-to-use-in-your-project"></a>Clips selecteren die u in uw project wilt gebruiken

Als u op de pijl-omlaag aan de rechter kant van elke video klikt, wordt de inzichten in de video geopend op basis van tijds tempels (clips van de video). 

1. Als u query's voor specifieke clips wilt maken, gebruikt u het zoekvak ' zoeken in transcriptie, visuele tekst, personen en labels '.
1. Selecteer **inzichten weer geven** om aan te passen welke inzichten u wilt zien en welke u niet wilt zien. 

    :::image type="content" source="./media/video-indexer-view-edit/search-try-cognitive-services.png" alt-text="Scherm afbeelding toont het zoeken naar Video's die gepaard gaat met cognitieve Services":::
1. Voeg filters toe om details op te geven van de scènes die u zoekt door **filter opties** te selecteren.

    U kunt meerdere filters toevoegen. 
1. Wanneer u tevreden bent met de resultaten, selecteert u de clips die u aan dit project wilt toevoegen door het segment te selecteren dat u wilt toevoegen. U kunt de selectie van deze clip opheffen door opnieuw op het segment te klikken.
    
    Voeg alle segmenten van een video toe (of, alle die zijn geretourneerd na uw zoek actie) door te klikken op de menu optie lijst naast de video en **Selecteer Alles selecteren**. 

Wanneer u uw clips selecteert en ordent, kunt u een voor beeld van de video in de speler aan de rechter kant van de pagina bekijken. 

> [!IMPORTANT]
> Vergeet niet om uw project op te slaan wanneer u wijzigingen aanbrengt door **project opslaan** boven aan de pagina te selecteren. 

### <a name="render-and-download-the-project"></a>Het project weer geven en downloaden

> [!NOTE]
> Voor Video Indexer betaalde accounts bevat de rendering van het project coderings kosten. Video Indexer proef accounts zijn beperkt tot vijf uur aan de weer gave.

1. Wanneer u klaar bent, controleert u of uw project is opgeslagen. U kunt dit project nu weer geven. Klik op **render**, er wordt een pop-upvenster weer gegeven waarin wordt uitgelegd dat video indexer een bestand weergeeft en de download koppeling naar uw e-mail bericht wordt verzonden. Selecteer Doorgaan. 

    :::image type="content" source="./media/video-indexer-view-edit/render-download.png" alt-text="Scherm afbeelding toont Video Indexer met de optie om uw project weer te geven en te downloaden":::
    
    U ziet ook een melding dat het project boven op de pagina wordt weer gegeven. Zodra de bewerking is uitgevoerd, wordt er een nieuwe melding weer gegeven dat het project is gegenereerd. Klik op de melding om het project te downloaden. Het project wordt gedownload in de indeling MP4.
1. U kunt opgeslagen projecten openen via het tabblad **projecten** . 

    Als u dit project selecteert, worden alle inzichten en de tijd lijn van dit project weer geven. Als u **video-editor** selecteert, kunt u door gaan met het maken van wijzigingen aan dit project. Bewerkingen zijn onder andere het toevoegen of verwijderen van Video's en clips of het wijzigen van de naam van het project.
    
## <a name="create-a-project-from-your-video"></a>Een project maken op basis van uw video

U kunt rechtstreeks een nieuw project maken op basis van een video in uw account. 

1. Ga naar het tabblad **bibliotheek** van de video indexer-website.
1. Open de video die u wilt gebruiken om uw project te maken. Selecteer op de pagina inzichten en tijd lijn de knop **video-editor** .

    Hiermee gaat u naar de pagina die u hebt gebruikt om een nieuw project te maken. In tegens telling tot het nieuwe project ziet u de getimede Insights-segmenten van de video, die u eerder hebt bewerkt.

## <a name="see-also"></a>Zie tevens

[Overzicht van Video Indexer](video-indexer-overview.md)

