---
title: 'Java script-zelf studie: een website met zoek functionaliteit implementeren'
titleSuffix: Azure Cognitive Search
description: Een website met zoek functionaliteit implementeren naar een statische web-app van Azure.
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: a49ede283899cec42898672f5a376221265dea10
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726843"
---
# <a name="3---deploy-the-search-enabled-website"></a>3-de website met zoek functionaliteit implementeren

Implementeer de website met zoek functionaliteit als een statische Azure-web-app. Deze implementatie omvat zowel de reagerende app als de functie-app.  

De statische web-app haalt de informatie en bestanden op voor implementatie van GitHub met behulp van uw Fork van de opslag plaats voor beelden.  

## <a name="create-a-static-web-app-in-visual-studio-code"></a>Een statische web-app maken in Visual Studio code

1. Selecteer **Azure** op de activiteiten balk en selecteer vervolgens **statische web apps** in de balk aan de zijkant. 
1. Klik met de rechter muisknop op de naam van het abonnement en selecteer vervolgens **statische web-app maken (Geavanceerd)**.    

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-create-static-web-app-resource-advanced.png" alt-text="Klik met de rechter muisknop op de naam van het abonnement en selecteer vervolgens * een statische web-app maken (Geavanceerd) * *.":::

1. Volg de aanwijzingen om de volgende informatie op te geven:

    |Prompt|Enter|
    |--|--|
    |Hoe wilt u een statische web-app maken?|Bestaande GitHub-opslag plaats gebruiken|
    |Organisatie kiezen|Selecteer uw _eigen_ github-alias als de organisatie.|
    |Opslag plaats kiezen|Selecteer **Azure-Search-java script-voor beelden** uit de lijst. |
    |Vertakking van opslag plaats kiezen|Selecteer **model** in de lijst. |
    |Voer de naam in voor de nieuwe statische web-app.|Maak een unieke naam voor de resource. U kunt bijvoorbeeld uw naam laten voorafgaan door aan de naam van de opslag plaats, zoals, `joansmith-azure-search-javascript-samples` . |
    |Selecteer een resource groep voor nieuwe resources.|Gebruik de resource groep die u hebt gemaakt voor deze zelf studie.|
    |Kies Build-voor instelling om standaard project structuur te configureren.|**Aangepaste** selecteren|
    |Selecteer de locatie van de toepassings code|`search-website`|
    |Selecteer de locatie van uw Azure-functie code|`search-website/api`|
    |Voer het pad van uw build-uitvoer in...|bouwen|
    |Selecteer een locatie voor nieuwe resources.|Selecteer een regio bij u in de buurt.|

1. De resource is gemaakt, selecteert u in de meldingen **Open acties in github** . Hiermee opent u een browser venster naar uw gevorkte opslag plaats. 

    De lijst met acties geeft aan dat uw web-app, zowel de client als de functies, naar uw statische Azure-web-app is gepusht. 

    Wacht tot de build en implementatie zijn voltooid voordat u doorgaat. Dit kan een paar minuten duren voordat de bewerking is voltooid.

## <a name="get-cognitive-search-query-key-in-visual-studio-code"></a>Cognitive Search query sleutel ophalen in Visual Studio code

1. Open in Visual Studio code de [activiteiten balk](https://code.visualstudio.com/docs/getstarted/userinterface)en selecteer het pictogram Azure. 

1. Selecteer uw Azure-abonnement in het gebied **Azure: Cognitive Search** in de zijbalk, klik met de rechter muisknop op uw zoek resource en selecteer **query sleutel kopiëren**. 

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-query-key.png" alt-text="Selecteer uw Azure-abonnement in het gebied * * Azure: Cognitive Search * * in de zijbalk, klik met de rechter muisknop op uw zoek resource en selecteer * * query sleutel kopiëren * *.":::

1. Bewaar deze query sleutel, u moet deze gebruiken in de volgende sectie. De query sleutel kan een query uitvoeren op uw index. 

## <a name="add-configuration-settings-in-visual-studio-code"></a>Configuratie-instellingen toevoegen in Visual Studio code

De functie-app van Azure retourneert geen Zoek gegevens totdat de zoek geheimen in instellingen zijn. 

1. Selecteer **Azure** op de activiteiten balk en selecteer vervolgens **statische web apps** in de balk aan de zijkant. 
1. Breid uw nieuwe statische web-app uit totdat de **Toepassings instellingen** worden weer gegeven.
1. Klik met de rechter muisknop op **Toepassings instellingen** en selecteer **nieuwe instelling toevoegen**.

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-static-web-app-configure-settings.png" alt-text="Klik met de rechter muisknop op * * toepassings instellingen * * en selecteer vervolgens * * nieuwe instelling toevoegen * *.":::

1. Voeg de volgende instellingen toe:

    |Instelling|De waarde van de zoek resource|
    |--|--|
    |SearchApiKey|De zoek query sleutel|
    |SearchServiceName|De naam van de zoek resource|
    |SearchIndexName|`good-books`|
    |SearchFacets|`authors*,language_code`|

## <a name="use-search-in-your-static-web-app"></a>Zoeken gebruiken in uw statische web-app

1. Open in Visual Studio code de [activiteiten balk](https://code.visualstudio.com/docs/getstarted/userinterface)en selecteer het pictogram Azure.
1. Klik in de zijbalk met de **rechter muisknop op uw Azure-abonnement** onder het `Static web apps` gebied en zoek de statische web-app die u hebt gemaakt voor deze zelf studie.
1. Klik met de rechter muisknop op de naam van de statische web-app en selecteer **Bladeren naar site**.
    
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-browse-static-web-app.png" alt-text="Klik met de rechter muisknop op de naam van de statische web-app en selecteer * * Bladeren naar site * *.":::    

1. Selecteer **openen** in het pop-updialoogvenster.
1. Voer in de zoek balk van de website een zoek opdracht in `code` , bijvoorbeeld _langzaam_ , zodat de functie Voorst Ellen suggesties voor Boek titels voordoet. Selecteer een suggestie of ga door met het invoeren van uw eigen query. Druk op ENTER wanneer u uw zoek query hebt voltooid. 
1. Bekijk de resultaten en selecteer vervolgens een van de boeken om meer details weer te geven. 

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resources die in deze zelf studie zijn gemaakt wilt opschonen, verwijdert u de resource groep.

1. Open in Visual Studio code de [activiteiten balk](https://code.visualstudio.com/docs/getstarted/userinterface)en selecteer het pictogram Azure. 

1. Klik in de zijbalk met de **rechter muisknop op uw Azure-abonnement** onder het `Resource Groups` gebied en zoek de resource groep die u hebt gemaakt voor deze zelf studie.
1. Klik met de rechter muisknop op de naam van de resource groep en selecteer vervolgens **verwijderen**.
    Hiermee worden zowel de zoek-als statische web-app-resources verwijderd.
1. Als u de GitHub Fork van het voor beeld niet meer wilt, moet u deze verwijderen op GitHub. Ga naar de **instellingen** van uw Fork en verwijder vervolgens de fork. 


## <a name="next-steps"></a>Volgende stappen

* [Zoek integratie begrijpen voor de website met zoek functionaliteit](tutorial-javascript-search-query-integration.md)
