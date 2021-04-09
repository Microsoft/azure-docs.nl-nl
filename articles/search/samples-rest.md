---
title: REST-voorbeelden
titleSuffix: Azure Cognitive Search
description: Zoek de REST code voorbeelden van Azure Cognitive Search demo die gebruikmaken van de zoek-of beheer REST-Api's.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/27/2021
ms.openlocfilehash: a7ab48759ac775c1195dedb4143d895bdcdec937
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98956486"
---
# <a name="rest-code-samples-for-azure-cognitive-search"></a>Voor beelden van REST code voor Azure Cognitive Search

Meer informatie over de REST API-voor beelden die de functionaliteit en werk stroom van een Azure Cognitive Search-oplossing demonstreren. In deze voor beelden worden de [**Zoek rest-api's**](/rest/api/searchservice)gebruikt.

REST is de definitieve programmeer interface voor Azure Cognitive Search en alle bewerkingen die programmatisch kunnen worden aangeroepen, zijn eerst in de REST beschikbaar en vervolgens in Sdk's. Daarom maken de meeste voor beelden in de documentatie gebruik van de REST-Api's om belang rijke concepten te demonstreren of uit te leggen.

REST-voor beelden zijn doorgaans ontwikkeld en getest op Postman, maar u kunt elke client gebruiken die HTTP-aanroepen ondersteunt:

+ Begin met [Quick Start: Maak een Azure Cognitive search-index met rest api's](search-get-started-rest.md) voor hulp bij het formuleren van http-aanroepen.
+ Probeer [Visual Studio code Extension voor Azure Cognitive Search](search-get-started-vs-code.md), die momenteel als preview-versie beschikbaar is, als u werkt met Visual Studio code.

## <a name="doc-samples"></a>Doc-voor beelden

Code voorbeelden van het Cognitive Search team illustreren de functies en werk stromen. In veel van deze voor beelden wordt verwezen naar zelf studies, Quick starts en artikelen met procedures. U kunt deze voor beelden vinden in [**Azure-samples/Azure-Search-postman-samples**](https://github.com/Azure-Samples/azure-search-postman-samples) op github.

| Voorbeelden | Artikel |
|---------|---------|
| [Snelstartgids](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Quickstart) | Bron code voor [Quick Start: Maak een zoek index met behulp van rest api's](search-get-started-rest.md). In dit artikel wordt de basis werk stroom beschreven voor het maken, laden en doorzoeken van een zoek index met voorbeeld gegevens. |
| [Zelfstudie](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) | Bron code voor [zelf studie: gebruik rest en AI om Doorzoek bare inhoud te genereren op basis van Azure-blobs](cognitive-search-tutorial-blob.md). In dit artikel wordt beschreven hoe u een vaardig heden maakt die over Azure-blobs lopen om informatie en Afleidings structuur te extra heren.|
| [Fout opsporing-sessies](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Debug-sessions) | Bron code voor [zelf studie: wijzigingen in uw vaardig heden diagnosticeren, herstellen en door voeren](cognitive-search-tutorial-debug-sessions.md). In dit artikel leest u hoe u een toepassing voor het opsporen van vaardig heden kunt gebruiken in de Azure Portal. REST wordt gebruikt om de objecten te maken die worden gebruikt tijdens de fout opsporing.|
| [aangepaste analyse functies](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/custom-analyzers) | Bron code voor [zelf studie: een aangepaste analyse functie maken voor telefoon nummers](tutorial-create-custom-analyzer.md). In dit artikel wordt uitgelegd hoe u analyse functies gebruikt om patronen en speciale tekens in Doorzoek bare inhoud te bewaren.|
| [kennis archief](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) | Bron code voor [het maken van een kennis archief met behulp van rest en postman](knowledge-store-create-rest.md). In dit artikel worden de stappen beschreven die nodig zijn voor het invullen van een kennis archief dat wordt gebruikt voor Knowledge Mining-werk stromen. |
| [projecties](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/projections) | Bron code voor [het vorm geven en exporteren van verrijkingen](knowledge-store-projections-examples.md). In dit artikel wordt uitgelegd hoe u de fysieke gegevens structuren in een kennis archief kunt opgeven.|
| [index-versleutelde blobs](https://github.com/Azure-Samples/azure-search-postman-samples/commit/f5ebb141f1ff98f571ab84ac59dcd6fd06a46718) | Bron code voor [het indexeren van versleutelde blobs met behulp van BLOB-Indexeer functies en vaardig heden](search-howto-index-encrypted-blobs.md). In dit artikel wordt beschreven hoe u documenten in Azure Blob-opslag indexeert die eerder zijn versleuteld met Azure Key Vault. |

> [!Tip]
> Gebruik de voor beelden van de [browser](/samples/browse/?expanded=azure&languages=http&products=azure-cognitive-search) om te zoeken naar micro soft-code voorbeelden in github, gefilterd op product, service en taal.

## <a name="other-samples"></a>Andere voor beelden

De volgende voor beelden worden ook gepubliceerd door het Cognitive Search-team, maar er wordt niet naar verwezen in de documentatie. Gekoppelde Leesmij-bestanden bieden gebruiks instructies.

| Voorbeelden | Description |
|---------|-------------|
| [Query-voor beelden](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Query-examples) | Postman-verzamelingen waarbij de verschillende query technieken worden gedemonstreerd, waaronder fuzzy zoeken, RegEx en Joker tekens zoeken, automatisch aanvullen, enzovoort. |