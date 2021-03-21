---
title: 'Zelf studie java script: zoek opdracht toevoegen aan Web-apps'
titleSuffix: Azure Cognitive Search
description: Index maken en CSV-gegevens importeren in zoek index met Java script met behulp van de NPM-SDK @azure/search-documents .
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 0fd28262f4a4b852386fa354037e69c5097109c5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726858"
---
# <a name="2---create-and-load-search-index-with-javascript"></a>2-zoek index maken en laden met Java script

Ga door met het bouwen van uw website met zoek functionaliteit:
* Een zoek resource maken met de VS code-extensie
* Het maken van een nieuwe index en het importeren van gegevens met Java script met behulp van het voorbeeld script en de Azure SDK [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) .

## <a name="create-an-azure-search-resource"></a>Een Azure Search resource maken 

Maak een nieuwe zoek resource met de [Azure Cognitive Search](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch) -extensie voor Visual Studio code.

1. Open in Visual Studio code de [activiteiten balk](https://code.visualstudio.com/docs/getstarted/userinterface)en selecteer het pictogram Azure. 

1. Klik in de zijbalk met de **rechter muisknop op uw Azure-abonnement** onder het `Azure: Cognitive Search` gebied en selecteer **nieuwe zoek service maken**.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-create-search-resource.png" alt-text="Klik in de zijbalk met de rechter muisknop op uw Azure-abonnement onder het gedeelte * * Azure: Cognitive Search * * en selecteer * * nieuwe zoek service maken * *.":::

1. Volg de aanwijzingen om de volgende informatie op te geven:

    |Prompt|Enter|
    |--|--|
    |Voer een globaal unieke naam in voor de nieuwe Search Service.|**Onthoud deze naam**. Deze resource naam wordt onderdeel van het resource-eind punt.|
    |Een resource groep selecteren voor nieuwe resources|Gebruik de resource groep die u hebt gemaakt voor deze zelf studie.|
    |Selecteer de SKU voor uw zoek service.|Selecteer **gratis** voor deze zelf studie. U kunt een SKU-prijs categorie niet wijzigen nadat de service is gemaakt.|
    |Selecteer een locatie voor nieuwe resources.|Selecteer een regio bij u in de buurt.|

1. Nadat u de prompts hebt voltooid, wordt de nieuwe zoek resource gemaakt. 

## <a name="get-your-search-resource-admin-key"></a>De beheerders sleutel van de zoek resource ophalen

Down load de beheerder van de zoek resource met de Visual Studio code-extensie. 

1. Klik in Visual Studio code in de zijbalk met de rechter muisknop op uw zoek resource en selecteer **beheerder sleutel kopiëren**.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-admin-key.png" alt-text="Klik in de zijbalk met de rechter muisknop op uw zoek resource en selecteer * * beheerders sleutel kopiëren * *.":::

1. Behoud deze beheerders sleutel, u moet deze in [een latere sectie](#prepare-the-bulk-import-script-for-search)gebruiken. 

## <a name="prepare-the-bulk-import-script-for-search"></a>Het bulk import-script voorbereiden voor zoeken

Het script maakt gebruik van de Azure SDK voor Cognitive Search:

* [NPM-pakket @azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* [Referentie documentatie](/javascript/api/overview/azure/search-documents-readme)

1. Open in Visual Studio code het `bulk_insert_books.js` bestand in de submap,  `search-web/bulk-insert` Vervang de volgende variabelen door uw eigen waarden om te verifiëren met de Azure Search SDK:

    * UW-ZOEK NAAM VOOR DE RESOURCE
    * UW-ZOEKEN-BEHEERDER SLEUTEL

    :::code language="javascript" source="~/azure-search-javascript-samples/search-website/bulk-insert/bulk_insert_books.js" highlight="16,17" :::

1. Open een geïntegreerde terminal in Visual Studio voor de submap van de projectmap `search-web/bulk-insert` en voer de volgende opdracht uit om de afhankelijkheden te installeren. 

    ```bash
    npm install 
    ```

## <a name="run-the-bulk-import-script-for-search"></a>Het bulk import-script uitvoeren voor zoeken

1. Ga door met het gebruik van de geïntegreerde terminal in Visual Studio voor de submap van de projectmap, `search-web/bulk-insert` , om de volgende bash-opdracht uit te voeren om het script uit te voeren `bulk_insert_books.js` :

    ```javascript
    npm start
    ```

1. Wanneer de code wordt uitgevoerd, wordt de voortgang van de console weer gegeven. 
1. Wanneer het uploaden is voltooid, wordt de laatste instructie afgedrukt op de console.

## <a name="review-the-new-search-index"></a>De nieuwe zoek index controleren

Zodra het uploaden is voltooid, is de zoek index klaar voor gebruik. Controleer de nieuwe index.

1. Open in Visual Studio code de extensie Azure Cognitive Search en selecteer uw zoek resource.  

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-resource.png" alt-text="Open de Azure Cognitive Search-extensie in Visual Studio code en open uw zoek resource.":::

1. Vouw indexen uit, then documenten en `good-books` Selecteer vervolgens een document om alle documentspecifieke gegevens weer te geven.
 
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" lightbox="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" alt-text="Vouw indices en vervolgens ' good-books ' uit en selecteer een document.":::

## <a name="copy-your-search-resource-name"></a>De naam van de zoek resource kopiëren

Noteer de **naam van uw zoek resource**. U hebt dit nodig om de Azure function-app te verbinden met uw zoek resource. 

> [!CAUTION]
> Hoewel u mogelijk geneigd bent om uw zoek beheerder sleutel te gebruiken in de Azure function, die niet voldoet aan het principe van minimale bevoegdheden. De Azure-functie gebruikt de sleutel van de query om te voldoen aan de minimale bevoegdheden. 

## <a name="next-steps"></a>Volgende stappen

[Uw statische web-app implementeren](tutorial-javascript-deploy-static-web-app.md)