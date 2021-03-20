---
title: Filteren op taal in een zoek index
titleSuffix: Azure Cognitive Search
description: Filter criteria voor het ondersteunen van zoeken in meerdere talen, het bereik van query's uitvoeren op taalspecifieke velden.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: 49a12203c833fc817b1898e6179d7f812d0a994e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89002518"
---
# <a name="how-to-filter-by-language-in-azure-cognitive-search"></a>Filteren op taal in azure Cognitive Search 

Een belang rijke vereiste in een meertalige Zoek toepassing is de mogelijkheid om de resultaten in de eigen taal van de gebruiker te doorzoeken en op te halen. In azure Cognitive Search, een manier om te voldoen aan de taal vereisten van een meertalige app, is het maken van een reeks velden die zijn toegewezen aan het opslaan van teken reeksen in een specifieke taal, en vervolgens de zoek opdracht voor volledige tekst beperken tot deze velden op de query tijd.

Query parameters op de aanvraag worden gebruikt om de zoek bewerking te bereiken en de resultaten te verkorten van alle velden die geen inhoud bieden die compatibel is met de zoek ervaring die u wilt leveren.

| Parameters | Doel |
|-----------|--------------|
| **searchFields** | Hiermee beperkt u het zoeken in volledige tekst in de lijst met benoemde velden. |
| **$select** | Hiermee verkleint u het antwoord op alleen de velden die u opgeeft. Standaard worden alle ophaalbaar velden geretourneerd. Met de para meter **$Select** kunt u kiezen welke items u wilt retour neren. |

Het succes van deze techniek scharniert op de integriteit van de veld inhoud. Azure Cognitive Search converteert geen teken reeksen of voert geen taal detectie uit. U moet ervoor zorgen dat velden de door u verwachte teken reeksen bevatten.

## <a name="define-fields-for-content-in-different-languages"></a>Velden definiëren voor inhoud in verschillende talen

In azure Cognitive Search worden query's gericht op één index. Ontwikkel aars die taalspecifieke teken reeksen willen opgeven in één zoek opdracht, definiëren meestal specifieke velden voor het opslaan van de waarden: Eén veld voor Engelse teken reeksen, een voor Frans, enzovoort. 

Het volgende voor beeld is afkomstig [uit het voor beeld van](search-get-started-portal.md) een onroerend goed met verschillende teken reeks velden die inhoud in verschillende talen bevatten. Let op de taal analyse toewijzingen voor de velden in deze index. Velden met teken reeksen worden beter in Zoek opdrachten in volledige tekst uitgevoerd wanneer ze zijn gekoppeld aan een Analyzer die is ontworpen om de taal kundige regels van de doel taal te verwerken.

  ![Scherm opname van het venster velden van het voor beeld van het onroerend goed. Een groep velden wordt gemarkeerd om erachter te komen hoe de taal analyse toewijzingen overeenkomen met de talen voor de gemarkeerde velden.](./media/search-filters-language/lang-fields.png)

> [!Note]
> Zie [een index definiëren (.net)](./search-get-started-dotnet.md) en [een index definiëren (rest)](./search-get-started-powershell.md)voor code voorbeelden van veld definities met taal analysen.

## <a name="build-and-load-an-index"></a>Een index maken en laden

Een tussenliggende (en mogelijk duidelijke) stap is dat u [de index moet bouwen en vullen](./search-get-started-dotnet.md) voordat u een query kunt formuleren. Deze stap wordt hier vermeld voor volledigheid. Een manier om te bepalen of de index beschikbaar is, is door de lijst indexen te controleren in de [Portal](https://portal.azure.com).

## <a name="constrain-the-query-and-trim-results"></a>De resultaten van de query en het knippen beperken

De para meters voor de query worden gebruikt om de zoek opdracht te beperken tot specifieke velden en vervolgens de resultaten van velden die niet nuttig zijn voor uw scenario, te verkorten. Gezien het doel van het beperken van een zoek opdracht naar velden met Franse teken reeksen, gebruikt u **searchFields** om de query te richten op velden met teken reeksen in die taal. 

Een zoek opdracht retourneert standaard alle velden die zijn gemarkeerd als ophalen. Daarom wilt u mogelijk velden uitsluiten die niet voldoen aan de taalspecifieke Zoek ervaring die u wilt bieden. Als u de zoek opdracht beperkt tot een veld met Franse teken reeksen, wilt u waarschijnlijk velden met Engelse teken reeksen uitsluiten van uw resultaten. Met behulp van de para meter **$Select** query kunt u bepalen welke velden worden geretourneerd naar de aanroepende toepassing.

```csharp
parameters =
    new SearchParameters()
    {
        searchFields = "description_fr" 
        Select = new[] { "description_fr"  }
    };
```
> [!Note]
> Hoewel er geen $filter argument is voor de query, wordt deze use-case sterk gekoppeld aan filter concepten, zodat deze wordt weer gegeven als een filter scenario.

## <a name="see-also"></a>Zie ook

+ [Filters in azure Cognitive Search](search-filters.md)
+ [Taalanalyse](/rest/api/searchservice/language-support)
+ [Hoe zoeken in de volledige tekst werkt in Azure Cognitive Search](search-lucene-query-architecture.md)
+ [REST API voor documenten zoeken](/rest/api/searchservice/search-documents)