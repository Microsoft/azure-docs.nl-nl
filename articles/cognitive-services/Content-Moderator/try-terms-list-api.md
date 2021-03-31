---
title: Gemiddelde tekst met aangepaste termen lijsten-Content Moderator
titleSuffix: Azure Cognitive Services
description: Gebruik de lijst beheer-API om aangepaste lijsten met termen te maken voor gebruik met de API voor tekst toezicht.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: f1dfe88741a304da92901d3997c746654336ef54
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "85800037"
---
# <a name="moderate-with-custom-term-lists-in-the-api-console"></a>Gemiddeld met aangepaste termen lijsten in de API-console

De standaardlijst met algemene termen van Azure Content Moderator is voldoende voor de meeste moderatietaken voor inhoudsbeheer. Het is echter mogelijk dat u inhoud moet controleren op termen die specifiek zijn voor uw organisatie. Zo is het bijvoorbeeld mogelijk dat u namen van concurrenten wilt taggen voor nader onderzoek. 

Gebruik de [lijst beheer-API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f) om aangepaste lijsten met termen te maken voor gebruik met de API voor tekst toezicht. De tekst **scherm** bewerking scant uw tekst voor scheld woorden en vergelijkt ook tekst met aangepaste en gedeelde ip's.

> [!NOTE]
> Er is een maximumlimiet van **5 terminologielijsten** waarbij elke lijst **niet meer dan 10.000 termen mag bevatten**.
>

U kunt de lijst beheer-API gebruiken om de volgende taken uit te voeren:
- Een lijst maken.
- Termen toevoegen aan een lijst.
- Termen vergelijken met de termen in een lijst.
- Termen verwijderen uit een lijst.
- Een lijst verwijderen.
- Lijstgegevens bewerken.
- De index vernieuwen zodat wijzigingen in de lijst worden gebruikt in een nieuwe scan.

## <a name="use-the-api-console"></a>De API-console gebruiken

Voordat u de API in de online console kunt testen, moet u uw abonnements sleutel hebben. Deze sleutel bevindt zich op het tabblad **instellingen** in het vak **OCP-APIM-Subscription-Key** . Zie [Overzicht](overview.md) voor meer informatie.

## <a name="refresh-search-index"></a>Zoek index vernieuwen

Nadat u wijzigingen in een lijst met termen hebt aangebracht, moet u de index vernieuwen zodat wijzigingen worden opgenomen in toekomstige scans. Deze stap is vergelijkbaar met de manier waarop een zoek machine op het bureau blad (indien ingeschakeld) of een zoek machine op Internet de index doorlopend vernieuwt om nieuwe bestanden of pagina's te bevatten.

1. Selecteer in de [termen lijst beheer API-verwijzing](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)in het menu links de optie **termen lijsten** en selecteer vervolgens **zoek index vernieuwen**. 

   De **lijst met termen-pagina zoek index vernieuwen** wordt geopend.

2. Voor **open API-test console** selecteert u de regio die uw locatie het meest beschrijft. 

   ![Termen lijsten-zoek index pagina regio selectie vernieuwen](images/test-drive-region.png)

   De **lijst met termen-de API-console zoek index vernieuwen** wordt geopend.

3. Voer in het vak **listId** de lijst-ID in. Voer uw abonnements sleutel in en selecteer vervolgens **verzenden**.

   ![Term lists-API-inhoud van zoek index-console antwoord vernieuwen](images/try-terms-list-refresh-1.png)

## <a name="create-a-term-list"></a>Een termenlijst maken
1. Ga naar de API-verwijzing voor het beheer van de [termen lijst](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f). 

   De pagina **termen lijst-maken** wordt geopend.

2. Voor **open API-test console** selecteert u de regio die uw locatie het meest beschrijft. 

   ![Termen lijsten-selectie van pagina regio maken](images/test-drive-region.png)

   De **lijst met termen-API-console maken** wordt geopend.
 
3. Voer in het vak **OCP-APIM-Subscription-Key** uw abonnements sleutel in.

4. Voer in het vak **hoofd tekst van aanvraag** waarden in voor **naam** (bijvoorbeeld myList) en **Beschrijving**.

   ![Termen lijsten-naam en beschrijving van de hoofd tekst van de console maken](images/try-terms-list-create-1.png)

5. Gebruik tijdelijke aanduidingen voor sleutel waarden paar om beschrijvende meta gegevens aan uw lijst toe te wijzen.

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

   Voeg Meta gegevens van lijsten toe als sleutel-waardeparen en niet de werkelijke voor waarden.
 
6. Selecteer **Verzenden**. De lijst wordt gemaakt. Noteer de **id-** waarde die is gekoppeld aan de nieuwe lijst. U hebt deze ID nodig voor andere beheer functies voor termen lijsten.

   ![Termen lijsten-inhouds vakje voor het maken van een console-antwoord bevat de lijst-ID](images/try-terms-list-create-2.png)
 
7. Termen toevoegen aan MyList. Selecteer in het linkermenu onder **term** de optie **term toevoegen**. 

   De pagina term **-toevoegen** wordt geopend. 

8. Voor **open API-test console** selecteert u de regio die uw locatie het meest beschrijft. 

   ![Term-selectie voor term pagina regio toevoegen](images/test-drive-region.png)

   De **term-API-** console voor toevoegen wordt geopend.
 
9. Voer in het vak **listId** de lijst-ID in die u hebt gegenereerd en selecteer een waarde voor de **taal**. Voer uw abonnements sleutel in en selecteer vervolgens **verzenden**.

   ![Term-query parameters voor term console toevoegen](images/try-terms-list-create-3.png)
 
10. Als u wilt controleren of de term is toegevoegd aan de lijst, selecteert u in het linkermenu **term** en selecteert u **alle voor waarden ophalen**. 

    De **term-API-console alle termen ophalen** wordt geopend.

11. Voer in het vak **listId** de lijst-ID in en voer vervolgens uw abonnements sleutel in. Selecteer **Verzenden**.

12. Controleer in het vak **reactie inhoud** de voor waarden die u hebt ingevoerd.

    ![Term: alle termen ophalen console antwoord inhoud bevat een lijst met de voor waarden die u hebt ingevoerd](images/try-terms-list-create-4.png)
 
13. Voeg nog een aantal voor waarden toe. Nu u een aangepaste lijst met voor waarden hebt gemaakt, kunt u [tekst scannen](try-text-api.md) met behulp van de lijst met aangepaste termen. 

## <a name="delete-terms-and-lists"></a>Termen en lijsten verwijderen

U kunt eenvoudig term of lijsten verwijderen. U gebruikt de API om de volgende taken uit te voeren:

- Een term verwijderen. (**Term-verwijderen**)
- Alle termen in een lijst verwijderen zonder de lijst te verwijderen. (**Term-alle voor waarden verwijderen**)
- Een lijst en alle inhoud verwijderen. (**Termen lijsten-verwijderen**)

In dit voor beeld wordt één term verwijderd.

1. Selecteer in de [termen lijst beheer-API-verwijzing](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)in het linkermenu **term** en selecteer vervolgens **verwijderen**. 

   De **term-verwijderen** wordt geopend.

2. Voor **open API-test console** selecteert u de regio die uw locatie het meest beschrijft. 

   ![Term-pagina regio selectie verwijderen](images/test-drive-region.png)

   De **term-delete API-** console wordt geopend.
  
3. Voer in het vak **listId** de id in van de lijst waaruit u een term wilt verwijderen. Deze ID is het aantal (in het voor beeld **122**) dat wordt geretourneerd in de **lijst met termen-Details** van de console ophalen voor myList. Voer de term in en selecteer een taal.
 
   ![Term-console query parameters verwijderen](images/try-terms-list-delete-1.png)

4. Voer uw abonnements sleutel in en selecteer vervolgens **verzenden**.

5. Als u wilt controleren of de term is verwijderd, gebruikt u de **lijst met termen-alle console ophalen** .

   ![Lijst met termen: alle inhoud van het console-antwoord ophalen geeft aan dat de term is verwijderd](images/try-terms-list-delete-2.png)
 
## <a name="change-list-information"></a>Lijst gegevens wijzigen

U kunt de naam en beschrijving van een lijst bewerken en meta gegevens items toevoegen.

1. Selecteer in de [termen lijst beheer API-verwijzing](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f67f)in het menu links de optie **termen lijsten** en selecteer vervolgens **Details bijwerken**. 

   De **lijst met termen-pagina Details bijwerken** wordt geopend.

2. Voor **open API-test console** selecteert u de regio die uw locatie het meest beschrijft. 

   ![Termen lijsten-pagina regio Details bijwerken](images/test-drive-region.png)

   De **lijst met termen-update Details** API-console wordt geopend.

3. Voer in het vak **listId** de lijst-ID in en voer vervolgens uw abonnements sleutel in.

4. Breng in het vak **hoofd tekst van aanvraag** de gewenste wijzigingen aan en selecteer vervolgens **verzenden**.

   ![Termen lijsten-aanvraag tekst bewerkingen update Details](images/try-terms-list-change-1.png)
 

## <a name="next-steps"></a>Volgende stappen

Gebruik de REST API in uw code of begin met de [term lijsten .net Quick](term-lists-quickstart-dotnet.md) start om te integreren met uw toepassing.
