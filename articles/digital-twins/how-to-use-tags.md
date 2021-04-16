---
title: Tags toevoegen aan digitale tweelingen
titleSuffix: Azure Digital Twins
description: Meer informatie over het implementeren van tags op digitale tweelingen
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 0d45ef8c32e61b5567798b7c42af28badb222601
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376712"
---
# <a name="add-tags-to-digital-twins"></a>Tags toevoegen aan digitale tweelingen 

U kunt het concept tags gebruiken om uw digitale tweelingen verder te identificeren en categoriseren. In het bijzonder willen gebruikers mogelijk tags van bestaande systemen repliceren, zoals [Labelstack Tags,](https://project-haystack.org/doc/TagModel)binnen hun Azure Digital Twins exemplaren. 

In dit document worden patronen beschreven die kunnen worden gebruikt voor het implementeren van tags op digitale tweelingen.

Tags worden eerst toegevoegd als eigenschappen in het [model die](concepts-models.md) een digitale tweeling beschrijven. Deze eigenschap wordt vervolgens ingesteld op de tweeling wanneer deze wordt gemaakt op basis van het model. Daarna kunnen de tags worden gebruikt in [query's om](concepts-query-language.md) uw tweelingen te identificeren en te filteren.

## <a name="marker-tags"></a>Markeringstags 

Een **markeringstag** is een eenvoudige tekenreeks die wordt gebruikt om een digitale tweeling te markeren of te categoriseren, zoals 'blauw' of 'rood'. Deze tekenreeks is de naam van de tag en markeringstags hebben geen betekenisvolle waarde. De tag is alleen belangrijk vanwege de aanwezigheid (of afwezigheid). 

### <a name="add-marker-tags-to-model"></a>Markeringstags toevoegen aan het model 

Markeringstags worden [gemodelleerd als een DTDL-kaart](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) van `string` naar `boolean` . De Booleaanse code wordt genegeerd, omdat de aanwezigheid van `mapValue` de tag alles is wat belangrijk is. 

Hier is een fragment van een dubbel model dat een markeringstag implementeert als een eigenschap:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="2-16":::

### <a name="add-marker-tags-to-digital-twins"></a>Markeringstags toevoegen aan digitale tweelingen

Zodra de eigenschap deel uitmaakt van het model van een digitale tweeling, kunt u de markeringstag instellen in de digitale tweeling door de waarde van `tags` deze eigenschap in te stellen. 

Hier is een voorbeeld van het vullen van de markering `tags` voor drie tweelingen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesMarker":::

Hier is een codevoorbeeld voor het instellen van de markering `tags` voor een tweeling met behulp van de [.NET SDK](/dotnet/api/overview/azure/digitaltwins/client):

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesCsharp":::

### <a name="query-with-marker-tags"></a>Query uitvoeren met markeringstags

Zodra tags zijn toegevoegd aan digitale tweelingen, kunnen de tags worden gebruikt om de tweelingen in query's te filteren. 

Hier is een query om alle tweelingen op te halen die zijn getagd als 'rood': 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

U kunt ook tags combineren voor complexere query's. Hier is een query om alle tweelingen op te halen die rond en niet rood zijn: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags2":::

## <a name="value-tags"></a>Waardetags 

Een **waardetag** is een sleutel-waardepaar dat wordt gebruikt om elke tag een waarde te geven, zoals `"color": "blue"` of `"color": "red"` . Zodra een waardetag is gemaakt, kan deze ook worden gebruikt als markeringstag door de waarde van de tag te negeren. 

### <a name="add-value-tags-to-model"></a>Waardetags toevoegen aan model 

Waardetags worden gemodelleerd als [een DTDL-kaart](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) van `string` naar `string` . Zowel de `mapKey` als de `mapValue` zijn significant. 

Hier is een fragment van een dubbelmodel waarin een waardetag als eigenschap wordt geïmplementeerd:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="17-31":::

### <a name="add-value-tags-to-digital-twins"></a>Waardetags toevoegen aan digitale tweelingen

Net als bij markeringstags kunt u de waardetag instellen in een digitale tweeling door de waarde van deze eigenschap `tags` van het model in te stellen. Als u een waardetag als markeringstag wilt gebruiken, kunt u het `tagValue` veld instellen op de lege tekenreekswaarde ( `""` ). 

Hier is een voorbeeld van het vullen van de waarde `tags` voor drie tweelingen:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesValue":::

Houd er rekening `red` mee dat en worden gebruikt als `purple` markeringstags in dit voorbeeld.

### <a name="query-with-value-tags"></a>Query uitvoeren met waardetags

Net als bij markeringstags kunt u waardetags gebruiken om de tweelingen in query's te filteren. U kunt ook waardetags en markeringstags samen gebruiken.

In het bovenstaande voorbeeld `red` wordt gebruikt als markeringstag. Denk eraan dat dit een query is om alle tweelingen op te halen die zijn getagd als 'rood': 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Hier is een query om alle entiteiten op te halen die klein zijn (waardetag) en niet rood: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerValueTags":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het ontwerpen en beheren van digital twin-modellen:
* [*How-to: Manage DTDL models (DTDL-modellen beheren)*](how-to-manage-model.md)

Meer informatie over het uitvoeren van query's op de tweelinggrafiek:
* [*How-to: Query's uitvoeren op de tweelinggrafiek*](how-to-query-graph.md)
