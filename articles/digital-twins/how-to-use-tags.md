---
title: Tags toevoegen aan digitale apparaatdubbels
titleSuffix: Azure Digital Twins
description: Meer informatie over het implementeren van tags op digitale apparaatdubbels
author: baanders
ms.author: baanders
ms.date: 7/22/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 70bf46de072a97eca810dda60a5331df14172ed6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "100555159"
---
# <a name="add-tags-to-digital-twins"></a>Tags toevoegen aan digitale apparaatdubbels 

U kunt het concept Tags gebruiken om uw digitale apparaatdubbels verder te identificeren en te categoriseren. Het is met name mogelijk dat gebruikers labels willen repliceren van bestaande systemen, zoals [haystack-Tags](https://project-haystack.org/doc/TagModel), binnen hun digitale apparaatdubbels-instanties van Azure. 

In dit document worden patronen beschreven die kunnen worden gebruikt voor het implementeren van tags op digitale apparaatdubbels.

Labels worden eerst toegevoegd als eigenschappen in het [model](concepts-models.md) waarin een digitale dubbele. Deze eigenschap wordt vervolgens ingesteld op de dubbele waarde wanneer deze wordt gemaakt op basis van het model. Daarna kunnen de tags worden gebruikt in [query's](concepts-query-language.md) om uw apparaatdubbels te identificeren en te filteren.

## <a name="marker-tags"></a>Markerings Tags 

Een **markerings code** is een eenvoudige teken reeks die wordt gebruikt om een digitale dubbele waarde te markeren of categoriseren, zoals ' blauw ' of ' rood '. Deze teken reeks is de naam van de tag en markeringen Tags hebben geen zinvolle waarde: het label is alleen van belang voor de aanwezigheid (of afwezigheid). 

### <a name="add-marker-tags-to-model"></a>Markerings Tags toevoegen aan model 

Markerings Tags worden gemodelleerd als een [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) -toewijzing van `string` naar `boolean` . De Booleaanse waarde `mapValue` wordt genegeerd als de aanwezigheid van de tag belang rijk is. 

Hier volgt een uittreksel van een dubbel model waarmee een markerings code wordt geïmplementeerd als een eigenschap:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="2-16":::

### <a name="add-marker-tags-to-digital-twins"></a>Markerings Tags toevoegen aan digitale apparaatdubbels

Zodra de `tags` eigenschap deel uitmaakt van een digitaal en dubbel model, kunt u de markerings code instellen in het digitale dubbele door de waarde van deze eigenschap in te stellen. 

Hier volgt een voor beeld waarin de markering `tags` voor drie apparaatdubbels wordt ingevuld:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesMarker":::

### <a name="query-with-marker-tags"></a>Query met markerings Tags

Zodra Tags zijn toegevoegd aan digitale apparaatdubbels, kunnen de tags worden gebruikt voor het filteren van de apparaatdubbels in query's. 

Hier volgt een query om alle apparaatdubbels op te halen die zijn gelabeld als ' Red ': 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

U kunt ook labels combi neren voor complexere query's. Hier volgt een query voor het ophalen van alle apparaatdubbels die worden afgerond en niet rood: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags2":::

## <a name="value-tags"></a>Waarde-labels 

Een **waardeveld** is een sleutel/waarde-paar dat wordt gebruikt om elk label een waarde te geven, zoals `"color": "blue"` of `"color": "red"` . Zodra een waarde-tag is gemaakt, kan deze ook als markerings code worden gebruikt door de waarde van de tag te negeren. 

### <a name="add-value-tags-to-model"></a>Waarde-Tags aan model toevoegen 

Waarde-labels worden gemodelleerd als een [DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) -toewijzing van `string` naar `string` . Beide `mapKey` en `mapValue` zijn belang rijk. 

Hier volgt een uittreksel van een dubbel model waarmee een waarde-tag wordt geïmplementeerd als een eigenschap:

:::code language="json" source="~/digital-twins-docs-samples/models/tags.json" range="17-31":::

### <a name="add-value-tags-to-digital-twins"></a>Waarde-Tags toevoegen aan digitale apparaatdubbels

Net als bij markeringen Tags kunt u de waardecode instellen in een digitale dubbele instelling door de waarde van deze `tags` eigenschap vanuit het model in te stellen. Als u een waarde-tag wilt gebruiken als een markerings code, kunt u het `tagValue` veld instellen op de lege teken reeks waarde ( `""` ). 

Hier volgt een voor beeld waarin de waarde `tags` voor drie apparaatdubbels wordt ingevuld:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="TagPropertiesValue":::

Houd er rekening mee dat `red` `purple` in dit voor beeld als markeringen Tags worden gebruikt.

### <a name="query-with-value-tags"></a>Query met waarde-labels

Net als bij markeringen Tags kunt u de apparaatdubbels gebruiken om de waarde in query's te filteren. U kunt ook de labels Tags en markeringen samen gebruiken.

In het bovenstaande voor beeld `red` wordt gebruikt als markerings code. Houd er rekening mee dat dit een query is voor het ophalen van alle apparaatdubbels die zijn gelabeld als ' Red ': 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Hier volgt een query om alle entiteiten te verkrijgen die klein zijn (waarde-tag) en niet rood: 

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerValueTags":::

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het ontwerpen en beheren van digitale twee modellen:
* [*Instructies: DTDL-modellen beheren*](how-to-manage-model.md)

Meer informatie over het uitvoeren van query's in het dubbele diagram:
* [*Instructies: een query uitvoeren op de dubbele grafiek*](how-to-query-graph.md)