---
title: Een persoonlijk model aanpassen met Video Indexer website
titleSuffix: Azure Media Services
description: Meer informatie over het aanpassen van een persoons model met de Video Indexer-website.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 12/16/2020
ms.author: juliako
ms.openlocfilehash: 890f8c159c3e8e8d0b4164cf218f320551ea63ec
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97628928"
---
# <a name="customize-a-person-model-with-the-video-indexer-website"></a>Een persoonlijk model aanpassen met de Video Indexer website

Video Indexer ondersteunt beroemdheden herkenning voor video-inhoud. De functie voor beroemdheden-herkenning gaat over ongeveer 1.000.000 gezichten op basis van een gevraagde gegevens bron, zoals IMDB, Wikipedia en belangrijkste LinkedIn-invloeds. Zie [een persoonlijk model aanpassen in video indexer](customize-person-model-overview.md)voor een gedetailleerd overzicht.

U kunt de Video Indexer-website gebruiken om gezichten te bewerken die in een video zijn gedetecteerd, zoals beschreven in dit onderwerp. U kunt ook de API gebruiken, zoals beschreven in [een persoons model aanpassen met behulp van api's](customize-person-model-with-api.md).

## <a name="central-management-of-person-models-in-your-account"></a>Centraal beheer van persoons modellen in uw account

1. Als u de persoons modellen in uw account wilt weer geven, bewerken en verwijderen, bladert u naar de Video Indexer-website en meldt u zich aan.
1. Selecteer de knop aanpassing van inhouds model aan de linkerkant van de pagina.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/content-model-customization/content-model-customization.png" alt-text="Inhouds model aanpassen":::
1. Selecteer het tabblad personen.

    U ziet het standaard persoons model in uw account. Het model met standaard personen bevat alle gezichten die u mogelijk hebt bewerkt of gewijzigd in de inzichten van uw Video's waarvoor u tijdens het indexeren geen aangepast persoons model hebt opgegeven.

    Als u andere persoons modellen hebt gemaakt, worden deze ook weer gegeven op deze pagina.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-face-model/content-model-customization-people-tab.png" alt-text="Personen aanpassen":::

## <a name="create-a-new-person-model"></a>Een nieuw persoons model maken

1. Selecteer de **plus knop model toevoegen** aan de rechter kant.
1. Voer de naam van het model in. U kunt nu nieuwe personen en gezichten toevoegen aan het nieuwe persoons model.
1. Selecteer de knop lijst menu en kies **+ persoon toevoegen**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/customize-face-model/add-new-person.png" alt-text="Een Peron toevoegen":::

## <a name="add-a-new-person-to-a-person-model"></a>Een nieuwe persoon toevoegen aan een persoons model

> [!NOTE]
> Met Video Indexer kunt u meerdere personen met dezelfde naam toevoegen aan een persoons model. Het is echter raadzaam dat elke persoon in uw model unieke namen krijgt voor bruikbaarheid en duidelijkheid.

1. Als u een nieuw gezicht wilt toevoegen aan een persoons model, selecteert u de menu knop lijst naast het persoons model waaraan u het gezicht wilt toevoegen.
1. Selecteer **+ persoon toevoegen** in het menu.

    In een pop-upvenster wordt u gevraagd om de details van de persoon in te vullen. Typ de naam van de persoon en selecteer de knop controleren.

    Vervolgens kunt u kiezen uit uw Verkenner of de gezichts afbeeldingen van het gezicht slepen en neerzetten. Video Indexer worden alle standaard afbeeldings bestands typen (bijvoorbeeld: JPG, PNG en meer).

    Video Indexer kunt instanties van deze persoon detecteren in de toekomstige Video's die u indexeert en de huidige Video's die u al hebt geïndexeerd, met behulp van het persoons model waaraan u dit nieuwe gezicht hebt toegevoegd. Het kan enige tijd duren voordat de herkenning van de persoon in uw huidige Video's van kracht wordt, omdat dit een batch proces is.

## <a name="rename-a-person-model"></a>De naam van een persoons model wijzigen

U kunt de naam van elk persoons model in uw account wijzigen, inclusief het standaard persoons model. Zelfs als u de naam van het standaard model van de persoon wijzigt, blijft deze toch hetzelfde als het standaard persoons model in uw account.

1. Selecteer de lijst menu knop naast het model van de persoon waarvan u de naam wilt wijzigen.
1. Selecteer **naam wijzigen** in het menu.
1. Selecteer de huidige naam van het model en typ de nieuwe naam.
1. Selecteer de knop met het selectie vakje voor uw model waarvan u de naam wilt wijzigen.

## <a name="delete-a-person-model"></a>Een persoons model verwijderen

U kunt alle persoons modellen verwijderen die u in uw account hebt gemaakt. U kunt het standaard model van de persoon echter niet verwijderen.

1. Selecteer **verwijderen** in het menu.

    Er wordt een pop-upvenster weer gegeven waarin u wordt gewaarschuwd dat met deze actie het persoons model en alle personen en bestanden erin worden verwijderd. Deze actie kan niet ongedaan worden gemaakt.
1. Als u zeker weet, selecteert u opnieuw verwijderen.

> [!NOTE]
> De bestaande Video's die zijn geïndexeerd met dit persoons model (nu verwijderd), bieden geen ondersteuning voor de mogelijkheid om de namen van de gezichten die in de video worden weer gegeven bij te werken. U kunt de namen van de gezichten in deze Video's pas bewerken nadat u ze opnieuw hebt geïndexeerd met een ander persoons model. Als u opnieuw indexeert zonder een persoons model op te geven, wordt het standaard model gebruikt.

## <a name="manage-existing-people-in-a-person-model"></a>Bestaande personen in een persoons model beheren

Als u de inhoud van een van uw persoons modellen wilt bekijken, selecteert u de pijl naast de naam van het persoons model. In de vervolg keuzelijst ziet u alle personen in het betreffende persoons model. Als u de lijst menu knop naast elk van de personen selecteert, ziet u de opties beheren, naam wijzigen en verwijderen.  

![Scherm afbeelding toont een context menu met opties voor beheren, naam wijzigen en verwijderen.](./media/customize-face-model/manage-people.png)

### <a name="rename-a-person"></a>De naam van een persoon wijzigen

1. Als u de naam van een persoon in uw persoons model wilt wijzigen, selecteert u de menu knop lijst en kiest u **naam wijzigen** in het menu lijst.
1. Selecteer de huidige naam van de persoon en typ de nieuwe naam.
1. Selecteer de knop controleren en de naam van de persoon wordt gewijzigd.

### <a name="delete-a-person"></a>Een persoon verwijderen

1. Als u een persoon uit uw persoonlijke model wilt verwijderen, selecteert u de menu knop lijst en kiest u **verwijderen** in het menu lijst.
1. Een pop-upvenster vertelt u dat deze actie de persoon zal verwijderen en dat deze actie niet ongedaan kan worden gemaakt.
1. Selecteer opnieuw **verwijderen** en Hiermee wordt de persoon uit het persoonlijke model verwijderd.

### <a name="manage-a-person"></a>Een persoon beheren

Als u **beheren** selecteert, wordt het venster **Details** van de persoon weer geven met alle gezichten waaruit dit persoons model wordt getraind. Deze gezichten zijn afkomstig van instanties van deze persoon in Video's die gebruikmaken van dit persoons model of van installatie kopieën die u hand matig hebt geüpload.

> [!TIP]
> U kunt het detail venster **van de persoon** openen door te klikken op de naam van de persoon of door te klikken op **beheren**, zoals hierboven wordt weer gegeven.

#### <a name="add-a-face"></a>Een gezicht toevoegen

U kunt meer gezichten toevoegen aan de persoon door **installatie kopieën toevoegen** te selecteren.

#### <a name="delete-a-face"></a>Een gezicht verwijderen

Selecteer de installatie kopie die u wilt verwijderen en klik op **verwijderen**.

#### <a name="rename-and-delete-the-person"></a>De persoon een andere naam geven en verwijderen 

U kunt het deel venster beheren gebruiken om de naam van de persoon te wijzigen en de persoon uit het persoons model te verwijderen.

## <a name="use-a-person-model-to-index-a-video"></a>Een persoons model gebruiken om een video te indexeren

U kunt een persoons model gebruiken om uw nieuwe video te indexeren door het persoons model toe te wijzen tijdens het uploaden van de video.

Voer de volgende stappen uit om uw persoons model te gebruiken in een nieuwe video:

1. Selecteer de knop **uploaden** aan de rechter kant van de pagina.
1. Verwijder uw video bestand of blader naar het bestand.
1. Selecteer de pijl **Geavanceerde opties** .
1. Selecteer de vervolg keuzelijst en selecteer het persoons model dat u hebt gemaakt.
1. Selecteer de optie **uploaden** onder aan de pagina en uw nieuwe video wordt geïndexeerd met uw persoons model.

Als u tijdens het uploaden geen persoonlijk model opgeeft, indexeert Video Indexer de video met het standaard persoons model in uw account.

## <a name="use-a-person-model-to-reindex-a-video"></a>Een persoons model gebruiken om een video opnieuw te indexeren

Als u een persoons model wilt gebruiken om een video in uw verzameling opnieuw te indexeren, gaat u naar uw account Video's op de start pagina van Video Indexer en houdt u de muis aanwijzer over de naam van de video die u opnieuw wilt indexeren.

U ziet opties om uw video te bewerken, te verwijderen en opnieuw te indexeren.

1. Selecteer de optie voor het opnieuw indexeren van uw video.

    ![Scherm opname toont de Video's van uw account en de optie om uw video opnieuw te indexeren.](./media/customize-face-model/reindex.png)

    U kunt nu het persoons model selecteren waarmee u uw video opnieuw wilt indexeren.
1. Selecteer de vervolg keuzelijst en selecteer het persoons model dat u wilt gebruiken.
1. Selecteer de knop **index** opnieuw maken en uw video wordt opnieuw geïndexeerd met uw persoons model.

Nieuwe wijzigingen die u aanbrengt aan de gezichten die u hebt gedetecteerd en herkend in de video die u zojuist hebt geïndexeerd, worden opgeslagen in het persoons model dat u hebt gebruikt om de video opnieuw te indexeren.

## <a name="managing-people-in-your-videos"></a>Personen in uw Video's beheren

U kunt de gevonden gezichten en mensen die worden herkend in de Video's die u indexeert, beheren door gezichten te bewerken en te verwijderen.

Als u een gezicht verwijdert, wordt een specifiek gezicht verwijderd uit de inzichten van de video.

Als u een gezicht bewerkt, wordt de naam van een gezicht dat wordt gedetecteerd en mogelijk herkend in uw video, gewijzigd. Wanneer u een gezicht in uw video bewerkt, wordt die naam opgeslagen als persoons vermelding in het persoons model dat is toegewezen aan de video tijdens het uploaden en indexeren.

Als u tijdens het uploaden geen persoonlijk model aan de video toewijst, wordt uw Bewerk bewerking opgeslagen in het standaard persoons model van uw account.

### <a name="edit-a-face"></a>Een gezicht bewerken

> [!NOTE]
> Als een persoons model twee of meer verschillende personen met dezelfde naam heeft, kunt u die naam niet meer labelen binnen de Video's die gebruikmaken van dat persoons model. U kunt alleen wijzigingen aanbrengen aan personen die deze naam delen op het tabblad personen van de pagina voor het aanpassen van het inhouds model in Video Indexer. Daarom is het raadzaam om unieke namen te geven aan elke persoon in uw persoons model.

1. Ga naar de Video Indexer-website en meld u aan.
1. Zoek naar een video die u wilt weer geven en bewerken in uw account.
1. Als u een gezicht in uw video wilt bewerken, gaat u naar het tabblad inzichten en selecteert u het potlood pictogram in de rechter bovenhoek van het venster.

    ![Scherm afbeelding toont een video met een onbekend gezicht om te selecteren.](./media/customize-face-model/edit-face.png)

1. Selecteer een van de gedetecteerde gezichten en wijzig hun namen van ' onbekend #X ' (of de naam die eerder aan het gezicht is toegewezen).
1. Nadat u de nieuwe naam hebt getypt, selecteert u het selectie pictogram naast de nieuwe naam. Met deze actie wordt de nieuwe naam opgeslagen en worden alle exemplaren van dit gezicht herkend en genoteerd in uw andere huidige Video's en in de toekomstige Video's die u uploadt. De herkenning van het gezicht in uw andere huidige Video's kan enige tijd in beslag nemen omdat dit een batch proces is.

Als u een gezicht een naam geeft met de naam van een bestaande persoon in het persoons model dat de video gebruikt, worden de gedetecteerde gezichts afbeeldingen uit deze video van die persoon samengevoegd met wat al in het model voor komt. Als u een gezicht met een nieuwe naam een naam krijgt, wordt er een nieuwe persoons vermelding gemaakt in het persoons model dat de video gebruikt.

### <a name="delete-a-face"></a>Een gezicht verwijderen

Als u een gedetecteerd gezicht in uw video wilt verwijderen, gaat u naar het deel venster inzichten en selecteert u het potlood pictogram in de rechter bovenhoek van het deel venster. Selecteer de optie **verwijderen** onder de naam van het gezicht. Met deze actie wordt het gedetecteerde gezicht verwijderd uit de video. Het gezicht van de gebruiker wordt nog steeds gedetecteerd in de andere Video's waarin het wordt weer gegeven, maar u kunt het gezicht van die Video's ook verwijderen nadat ze zijn geïndexeerd.

De persoon, als deze is benoemd, blijft ook bestaan in het persoons model dat is gebruikt voor het indexeren van de video van waaruit u het gezicht hebt verwijderd, tenzij u de persoon expliciet uit het persoons model verwijdert.

## <a name="next-steps"></a>Volgende stappen

[Persoonlijk model aanpassen met behulp van Api's](customize-person-model-with-api.md)
