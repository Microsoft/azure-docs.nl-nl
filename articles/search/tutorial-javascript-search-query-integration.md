---
title: 'Java script-zelf studie: aandachtspunten bij integratie zoeken'
titleSuffix: Azure Cognitive Search
description: Meer informatie over de Java script SDK-Zoek query's die worden gebruikt op de website met zoek functionaliteit
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/09/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: cf4e1b1ecf209b587a45ca4c43607bfa95155aee
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726835"
---
# <a name="4---search-integration-highlights"></a>4-integratie markeringen zoeken

In de vorige lessen hebt u een zoek opdracht toegevoegd aan een statische web-app. In deze les worden de essentiële stappen beschreven die integratie tot stand brengen. Als u op zoek bent naar een cheat-blad over het integreren van de zoek functie in uw Java script-app, wordt in dit artikel uitgelegd wat u moet weten.

## <a name="azure-sdk-azuresearch-documents"></a>Azure SDK @azure/search-documents 

De functie-app maakt gebruik van de Azure SDK voor Cognitive Search:

* NPM [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* Referentie documentatie: [client bibliotheek](/javascript/api/overview/azure/search-documents-readme)

De functie-app verifieert via de SDK naar de Cloud-Cognitive Search-API met behulp van de resource naam, resource sleutel en index naam. De geheimen worden opgeslagen in de instellingen van de statische web-app en worden als omgevings variabelen in de functie opgehaald. 

## <a name="configure-secrets-in-a-configuration-file"></a>Geheimen in een configuratie bestand configureren

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/config.js" highlight="3,4" :::

## <a name="azure-function-search-the-catalog"></a>Azure function: zoeken in de catalogus

De `Search` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/index.js) neemt een zoek term en zoek opdrachten in de documenten in de zoek index op en retourneert een lijst met overeenkomsten. 

Route ring voor de zoek-API bevindt zich in de [function.jsop](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Search/function.json) bindingen.

De Azure-functie haalt de informatie over de zoek configuratie op en voldoet aan de query.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Search/index.js" highlight="4-9, 75" :::

## <a name="client-search-from-the-catalog"></a>Client: zoeken in de catalogus

Roep de Azure-functie aan in de reagerende client met de volgende code. 

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Search/Search.js" highlight="40-51" :::

## <a name="azure-function-suggestions-from-the-catalog"></a>Azure function: suggesties uit de catalogus

De `Suggest` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/index.js) haalt een zoek term op terwijl een gebruiker typt en suggereert zoek termen zoals Boek titels en auteurs in de documenten in de zoek index om een kleine lijst met overeenkomsten te retour neren. 

De zoek suggesties, `sg` ,, worden gedefinieerd in het [schema bestand](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/bulk-insert/good-books-index.json) dat wordt gebruikt tijdens bulk upload.

Route ring voor de API Voorst Ellen bevindt zich in de [function.jsop](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Suggest/function.json) bindingen.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Suggest/index.js" highlight="4-9, 21" :::

## <a name="client-suggestions-from-the-catalog"></a>Client: suggesties uit de catalogus

De functie-API Voorst Ellen wordt aangeroepen in de app reageren `\src\components\SearchBar\SearchBar.js` als onderdeel van initialisatie van onderdelen:

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/components/SearchBar/SearchBar.js" highlight="52-60" :::

## <a name="azure-function-get-specific-document"></a>Azure function: specifiek document ophalen 

De `Lookup` [API](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/index.js) gebruikt een id en retourneert het document object uit de zoek index. 

Route ring voor de lookup-API bevindt zich in de [function.jsop](https://github.com/Azure-Samples/azure-search-javascript-samples/blob/master/search-website/api/Lookup/function.json) bindingen.

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/api/Lookup/index.js" highlight="4-9, 17" :::

## <a name="client-get-specific-document"></a>Client: specifiek document ophalen 

Deze functie-API wordt aangeroepen in de reagerende app `\src\pages\Details\Detail.js` als onderdeel van de initialisatie van het onderdeel:

:::code language="javascript" source="~/azure-search-javascript-samples/search-website/src/pages/Details/Details.js" highlight="19-29" :::

## <a name="next-steps"></a>Volgende stappen

* [Azure SQL-gegevens indexeren](search-indexer-tutorial.md)
