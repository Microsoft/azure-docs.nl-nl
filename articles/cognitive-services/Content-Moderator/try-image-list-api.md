---
title: Gemiddelde afbeeldingen met aangepaste lijsten en de API-console-Content Moderator
titleSuffix: Azure Content Moderator
description: U kunt de lijst beheer-API in azure Content Moderator gebruiken om aangepaste lijsten met installatie kopieën te maken.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 0035d367017c92bd151c27e14d744ef41eace069
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85800145"
---
# <a name="moderate-with-custom-image-lists-in-the-api-console"></a>Gemiddeld met aangepaste installatie kopie lijsten in de API-console

U kunt de [lijst beheer-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672) in azure content moderator gebruiken om aangepaste lijsten met installatie kopieën te maken. Gebruik de aangepaste lijsten met installatie kopieën met de API voor afbeeldings toezicht. Met de bewerking afbeeldings toezicht wordt de afbeelding geëvalueerd. Als u aangepaste lijsten maakt, vergelijkt de bewerking deze ook met de afbeeldingen in uw aangepaste lijsten. U kunt aangepaste lijsten gebruiken om de installatie kopie te blok keren of toe te staan.

> [!NOTE]
> Er is een maximumlimiet van **5 afbeeldingslijsten** waarbij elke lijst **niet meer dan 10.000 afbeeldingen mag bevatten**.
>

U kunt de lijst beheer-API gebruiken om de volgende taken uit te voeren:

- Een lijst maken.
- Voeg afbeeldingen toe aan een lijst.
- Scherm afbeeldingen voor de afbeeldingen in een lijst.
- Afbeeldingen uit een lijst verwijderen.
- Een lijst verwijderen.
- Lijstgegevens bewerken.
- De index vernieuwen zodat wijzigingen in de lijst worden gebruikt in een nieuwe scan.

## <a name="use-the-api-console"></a>De API-console gebruiken
Voordat u de API in de online console kunt testen, moet u uw abonnements sleutel hebben. Dit bevindt zich op het tabblad **instellingen** in het vak **OCP-APIM-Subscription-Key** . Zie [Overzicht](overview.md) voor meer informatie.

## <a name="refresh-search-index"></a>Zoek index vernieuwen

Nadat u wijzigingen in een lijst met installatie kopieën hebt aangebracht, moet u de index vernieuwen zodat wijzigingen worden opgenomen in toekomstige scans. Deze stap is vergelijkbaar met de manier waarop een zoek machine op het bureau blad (indien ingeschakeld) of een zoek machine op Internet de index doorlopend vernieuwt om nieuwe bestanden of pagina's te bevatten.

1. Selecteer in de [installatie kopie lijst beheer-API-verwijzing](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)in het menu links de optie **afbeeldings lijsten** en selecteer vervolgens **zoek index vernieuwen**.

   De **lijst met installatie kopieën-zoek index pagina vernieuwen** wordt geopend.

2. Voor **open API-test console** selecteert u de regio die uw locatie het meest beschrijft. 
 
    ![Lijst met installatie kopieën-pagina regio selectie voor zoek index vernieuwen](images/test-drive-region.png)

    De **lijst met installatie kopieën-de API-console zoek index vernieuwen** wordt geopend.

3. Voer in het vak **listId** de lijst-ID in. Voer uw abonnements sleutel in en selecteer vervolgens **verzenden**.

   ![Lijst met installatie kopieën-inhoud van het selectie vakje zoek index-console reactie vernieuwen](images/try-image-list-refresh-1.png)


## <a name="create-an-image-list"></a>Een afbeeldingslijst maken

1. Ga naar de [Image List Management API-referentie](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672).

   De **lijst met installatie kopieën-pagina maken** wordt geopend. 

3. Voor **open API-test console** selecteert u de regio die uw locatie het meest beschrijft.

   ![Lijst met installatie kopieën-pagina regio selectie maken](images/test-drive-region.png)

   De **lijst met installatie kopieën-API-console maken** wordt geopend.
 
4. Voer in het vak **OCP-APIM-Subscription-Key** uw abonnements sleutel in.

5. Voer in het vak **hoofd tekst van aanvraag** waarden in voor **naam** (bijvoorbeeld myList) en **Beschrijving**.

   ![Lijst met installatie kopieën-naam en beschrijving van de hoofd tekst van de console maken](images/try-terms-list-create-1.png)

6. Gebruik tijdelijke aanduidingen voor sleutel waarden paar om beschrijvende meta gegevens aan uw lijst toe te wijzen.

    ```json
    {
        "Name": "MyExclusionList",
        "Description": "MyListDescription",
        "Metadata": 
        {
            "Category": "Competitors",
            "Type": "Exclude"
        }
    }
    ```

   Voeg de meta gegevens van de lijst toe als sleutel-waardeparen en niet de daad werkelijke installatie kopieën.
 
7. Selecteer **Verzenden**. De lijst wordt gemaakt. Noteer de **id-** waarde die is gekoppeld aan de nieuwe lijst. U hebt deze ID nodig voor andere beheer functies voor lijst met installatie kopieën.

   ![Lijst met installatie kopieën-inhouds vakje voor het maken van een console-antwoord](images/try-terms-list-create-2.png)
 
8. Voeg vervolgens installatie kopieën toe aan MyList. Selecteer in het menu links de optie **afbeelding** en selecteer vervolgens **afbeelding toevoegen**.

   De pagina installatie kopie **toevoegen** wordt geopend. 

9. Voor **open API-test console** selecteert u de regio die uw locatie het meest beschrijft.

   ![Afbeelding: selectie van pagina regio voor installatie kopie toevoegen](images/test-drive-region.png)

   De **installatie kopie-API-** console voor installatie kopie toevoegen wordt geopend.
 
10. Voer in het vak **listId** de lijst-ID in die u hebt gegenereerd en voer de URL in van de afbeelding die u wilt toevoegen. Voer uw abonnements sleutel in en selecteer vervolgens **verzenden**.

11. Als u wilt controleren of de afbeelding is toegevoegd aan de lijst, selecteert u in het menu links de optie **afbeelding** en selecteert u vervolgens **alle afbeeldings-id's ophalen**.

    De **afbeelding: alle afbeeldings-Id's ophalen API-** console wordt geopend.
  
12. Voer in het vak **listId** de lijst-ID in en voer vervolgens uw abonnements sleutel in. Selecteer **Verzenden**.

    ![Afbeelding: alle afbeeldings-Id's ophalen in het vak console reactie inhoud bevat de installatie kopieën die u hebt ingevoerd](images/try-image-list-create-11.png)
 
10. Voeg nog enkele afbeeldingen toe. Nu u een aangepaste lijst met installatie kopieën hebt gemaakt, kunt u de installatie [kopieën evalueren](try-image-api.md) met behulp van de aangepaste lijst met installatie kopieën. 

## <a name="delete-images-and-lists"></a>Afbeeldingen en lijsten verwijderen

Het is eenvoudig om een afbeelding of lijst te verwijderen. U kunt de API gebruiken om de volgende taken uit te voeren:

- Een installatiekopie verwijderen. (**Installatie kopie verwijderen**)
- Alle installatie kopieën in een lijst verwijderen zonder de lijst te verwijderen. (**Installatie kopie-alle installatie kopieën verwijderen**)
- Een lijst en alle inhoud verwijderen. (**Lijsten met installatie kopieën-verwijderen**)

In dit voor beeld wordt één installatie kopie verwijderd:

1. Selecteer in de [installatie kopie lijst beheer-API-verwijzing](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)in het menu links de optie **Afbeelding** en selecteer vervolgens **verwijderen**. 

   De pagina **afbeelding verwijderen** wordt geopend.

2. Voor **open API-test console** selecteert u de regio die uw locatie het meest beschrijft. 

   ![Afbeelding: selectie van pagina regio verwijderen](images/test-drive-region.png)
 
   De **installatie kopie-API-console verwijderen** wordt geopend.
 
3. Voer in het vak **listId** de id in van de lijst waaruit u een afbeelding wilt verwijderen.  Dit is het getal dat is geretourneerd in de **installatie kopie-alle afbeeldings-ID-console ophalen** voor myList. Voer vervolgens de **ImageId** in van de afbeelding die u wilt verwijderen. 

In ons voor beeld is de lijst-ID **58953**, de waarde voor **ContentSource**. De afbeeldings-ID is **59021**, de waarde voor **ContentIds**.

1. Voer uw abonnements sleutel in en selecteer vervolgens **verzenden**.

1. Als u wilt controleren of de installatie kopie is verwijderd, gebruikt u de **installatie kopie-alle afbeeldings-ID-console ophalen** .
 
## <a name="change-list-information"></a>Lijst gegevens wijzigen

U kunt de naam en beschrijving van een lijst bewerken en meta gegevens items toevoegen.

1. Selecteer in de [installatie kopie lijst beheer-API-verwijzing](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f672)in het menu links de optie **afbeeldings lijsten** en selecteer vervolgens **Details bijwerken**. 

   De **lijst met installatie kopieën-pagina Details bijwerken** wordt geopend.

2. Voor **open API-test console** selecteert u de regio die uw locatie het meest beschrijft.  

    ![Lijst met installatie kopieën-pagina regio voor update Details](images/test-drive-region.png)

    De **lijst met installatie kopieën: update Details API-** console wordt geopend.
 
3. Voer in het vak **listId** de lijst-ID in en voer vervolgens uw abonnements sleutel in.

4. Maak in het vak **hoofd tekst** van de aanvraag uw wijzigingen en selecteer vervolgens de knop **verzenden** op de pagina.

   ![Lijst met installatie kopieën-aanvraag tekst bewerkingen update Details](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Volgende stappen

Gebruik de REST API in uw code of begin met de [installatie kopie lijsten .net Quick](image-lists-quickstart-dotnet.md) start om te integreren met uw toepassing.
