---
title: Taal model aanpassen met Video Indexer website
titleSuffix: Azure Media Services
description: Meer informatie over het aanpassen van een taal model met de Video Indexer-website.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 08/10/2020
ms.author: kumud
ms.openlocfilehash: dd8b36340deb6c785989107461dd420e7fc0d985
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97722569"
---
# <a name="customize-a-language-model-with-the-video-indexer-website"></a>Een taal model aanpassen met de Video Indexer-website

Met Video Indexer kunt u aangepaste taal modellen maken om spraak herkenning aan te passen door de aanpassings tekst te uploaden, namelijk de tekst van het domein waarvan u wilt dat de engine aan de woorden lijst voldoet. Zodra u het model hebt getraind, worden nieuwe woorden in de aanpassings tekst herkend.

Zie [een taal model aanpassen met video indexer](customize-language-model-overview.md)voor een gedetailleerd overzicht en aanbevolen procedures voor aangepaste taal modellen.

U kunt de Video Indexer-website gebruiken om aangepaste taal modellen in uw account te maken en te bewerken, zoals wordt beschreven in dit onderwerp. U kunt ook de API gebruiken, zoals beschreven in [taal model aanpassen met behulp van api's](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Een taal model maken

1. Ga naar de [video indexer](https://www.videoindexer.ai/) -website en meld u aan.
1. Als u een model in uw account wilt aanpassen, selecteert u de knop **aanpassing van inhouds model** aan de linkerkant van de pagina.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-language-model/model-customization.png" alt-text="Het inhouds model in Video Indexer aanpassen":::
1. Selecteer het tabblad **taal** .

    U ziet een lijst met ondersteunde talen.
1. Selecteer **model toevoegen** onder de gewenste taal.
1. Typ de naam voor het taal model en druk op ENTER.

    Met deze stap maakt u het model en biedt de optie om tekst bestanden naar het model te uploaden.
1. Selecteer **bestand toevoegen** om een tekst bestand toe te voegen. De bestanden Verkenner wordt geopend.
1. Navigeer naar en selecteer het tekst bestand. U kunt meerdere tekst bestanden toevoegen aan een taal model.

    U kunt ook een tekst bestand toevoegen door de knop **...** te selecteren aan de rechter kant van het taal model en vervolgens **bestand toevoegen** te selecteren.
1. Wanneer u klaar bent met het uploaden van de tekst bestanden, selecteert u de optie voor de groene **trein** .

Het trainings proces kan een paar minuten duren. Zodra de training is voltooid, ziet u **getraind** naast het model. U kunt het bestand bekijken, downloaden en verwijderen uit het model.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/customize-language-model/customize-language-model.png" alt-text="Het model trainen":::

### <a name="using-a-language-model-on-a-new-video"></a>Een taal model gebruiken in een nieuwe video

Voer een van de volgende acties uit om uw taal model te gebruiken in een nieuwe video:

* Selecteer de knop **uploaden** boven aan de pagina.

    ![Knop Uploaden Video Indexer](./media/customize-language-model/upload.png)
* Verwijder uw audio-of video bestand of blader naar het bestand.

U krijgt de mogelijkheid om de taal van de **video bron** te selecteren. Selecteer de vervolg keuzelijst en selecteer een taal model dat u hebt gemaakt in de lijst. U moet de taal van uw taal model en de naam die u hebt opgegeven tussen haakjes zeggen. Bijvoorbeeld:

![De taal van de video bron kiezen: een video opnieuw indexeren met Video Indexer](./media/customize-language-model/reindex.png)

Selecteer de optie **uploaden** onder aan de pagina en uw nieuwe video wordt geïndexeerd met uw taal model.

### <a name="using-a-language-model-to-reindex"></a>Een taal model gebruiken om opnieuw te indexeren

Als u uw taal model wilt gebruiken om een video in uw verzameling opnieuw te indexeren, voert u de volgende stappen uit:

1. Meld u aan bij de start pagina van [video indexer](https://www.videoindexer.ai/) .
1. Klik op **de knop op** de video en selecteer **opnieuw indexeren**.
1. U krijgt de mogelijkheid om de taal van de **video bron** te selecteren waarmee u uw video opnieuw wilt indexeren. Selecteer de vervolg keuzelijst en selecteer een taal model dat u hebt gemaakt in de lijst. U moet de taal van uw taal model en de naam die u hebt opgegeven tussen haakjes zeggen.
1. Selecteer de knop **opnieuw indexeren** en uw video wordt opnieuw geïndexeerd met uw taal model.

## <a name="edit-a-language-model"></a>Een taal model bewerken

U kunt een taal model bewerken door de naam ervan te wijzigen, bestanden toe te voegen en er bestanden van te verwijderen.

Als u bestanden toevoegt aan of verwijdert uit het taal model, moet u het model opnieuw trainen door de optie voor de groene **trein** te selecteren.

### <a name="rename-the-language-model"></a>De naam van het taal model wijzigen

U kunt de naam van het taal model wijzigen door de knop met het weglatings teken (**...**) aan de rechter kant van het taal model te selecteren en **naam wijzigen** te selecteren.

Typ de nieuwe naam en druk op ENTER.

### <a name="add-files"></a>Bestanden toevoegen

Selecteer **bestand toevoegen** om een tekst bestand toe te voegen. De bestanden Verkenner wordt geopend.

Navigeer naar en selecteer het tekst bestand. U kunt meerdere tekst bestanden toevoegen aan een taal model.

U kunt ook een tekst bestand toevoegen door de knop met het weglatings teken (**...**) aan de rechter kant van het taal model te selecteren en vervolgens **bestand toevoegen** te selecteren.

### <a name="delete-files"></a>Bestanden verwijderen

Als u een bestand uit het taal model wilt verwijderen, selecteert u de knop met het weglatings teken (**...**) aan de rechter kant van het tekst bestand en selecteert u **verwijderen**. Er verschijnt een nieuw venster met de melding dat de verwijdering niet ongedaan kan worden gemaakt. Selecteer de optie **verwijderen** in het nieuwe venster.

Met deze actie verwijdert u het bestand volledig uit het taal model.

## <a name="delete-a-language-model"></a>Een taal model verwijderen

Als u een taal model uit uw account wilt verwijderen, selecteert u de knop met het weglatings teken (**...**) aan de rechter kant van het taal model en selecteert u **verwijderen**.

Er verschijnt een nieuw venster met de melding dat de verwijdering niet ongedaan kan worden gemaakt. Selecteer de optie **verwijderen** in het nieuwe venster.

Met deze actie wordt het taal model volledig verwijderd uit uw account. Alle Video's die het verwijderde taal model gebruiken, blijven dezelfde index totdat u de video opnieuw indexeert. Als u de video opnieuw indexeert, kunt u een nieuw taal model toewijzen aan de video. Anders wordt het standaard model van Video Indexer gebruikt voor het opnieuw indexeren van de video.

## <a name="customize-language-models-by-correcting-transcripts"></a>Taal modellen aanpassen door transcripten te corrigeren

Video Indexer ondersteunt automatische aanpassing van taal modellen op basis van de daad werkelijke correcties die gebruikers aanbrengen in de transcripties van hun Video's.

1. Als u correcties wilt aanbrengen in een transcript, opent u de video die u wilt bewerken vanuit uw account Video's. Selecteer het tabblad **tijd lijn** .

    ![Tabblad tijd lijn van taal model aanpassen, Video Indexer](./media/customize-language-model/timeline.png)

1. Selecteer het potlood pictogram om de transcriptie van uw transcriptie te bewerken.

    ![Transcriptie voor het bewerken van taal modellen aanpassen: Video Indexer](./media/customize-language-model/edits.png)

    Video Indexer alle regels die door u zijn gecorrigeerd, worden vastgelegd in de transcriptie van uw video en worden ze automatisch toegevoegd aan een tekst bestand met de naam ' van transcripten bewerken '. Deze bewerkingen worden gebruikt voor het opnieuw trainen van het specifieke taal model dat is gebruikt voor het indexeren van deze video. 
    
    De bewerkingen die in de tijd lijn [van de widget](video-indexer-embed-widgets.md) zijn uitgevoerd, zijn ook opgenomen.
    
    Als u geen taal model hebt opgegeven bij het indexeren van deze video, worden alle bewerkingen voor deze video opgeslagen in een standaard taal model met de naam ' account aanpassingen ' in de gedetecteerde taal van de video.
    
    Als er meerdere bewerkingen op dezelfde regel zijn aangebracht, wordt alleen de laatste versie van de gecorrigeerde regel gebruikt voor het bijwerken van het taal model.  
    
    > [!NOTE]
    > Voor de aanpassing worden alleen tekstuele correcties gebruikt. Correcties die geen werkelijke woorden (bijvoorbeeld lees tekens of spaties) bevatten, worden niet opgenomen.
    
1. U ziet afschrift correcties worden weer gegeven op het tabblad taal van de pagina aanpassing van het inhouds model.

   Als u wilt kijken naar het bestand ' van transcripten bewerken ' voor elk van uw taal modellen, selecteert u dit om het te openen.

    ![Van transcript bewerkingen — Video Indexer](./media/customize-language-model/from-transcript-edits.png)

## <a name="next-steps"></a>Volgende stappen

[Taal model aanpassen met behulp van Api's](customize-language-model-with-api.md)
