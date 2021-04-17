---
title: Transformatie sorteren in toewijzingsgegevensstroom
description: Azure Data Factory Transformatie van toewijzingsgegevens sorteren
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 4a6567f8576e2507704956233bc593b203b48239
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588731"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Transformatie sorteren in toewijzingsgegevensstroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met de sorteertransformatie kunt u de binnenkomende rijen in de huidige gegevensstroom sorteren. U kunt afzonderlijke kolommen kiezen en deze sorteren in oplopende of aflopende volgorde.

> [!NOTE]
> Toewijzingsgegevensstromen worden uitgevoerd op Spark-clusters die gegevens verdelen over meerdere knooppunten en partities. Als u ervoor kiest om uw gegevens opnieuw tepartitioneren in een volgende transformatie, kunt u uw sortering verliezen als gevolg van het opnieuw indelen van gegevens. De beste manier om de sorteervolgorde in uw gegevensstroom te behouden, is door één partitie in te stellen op het tabblad Optimaliseren van de transformatie en de sorteertransformatie zo dicht mogelijk bij de sink te houden.

## <a name="configuration"></a>Configuratie

![Sorteerinstellingen](media/data-flow/sort.png "Sorteren")

**Niet-casegevoelig:** Of u een case wilt negeren bij het sorteren van tekenreeks- of tekstvelden

**Alleen sorteren binnen partities:** Wanneer gegevensstromen worden uitgevoerd op Spark, wordt elke gegevensstroom onderverdeeld in partities. Met deze instelling worden alleen gegevens binnen de binnenkomende partities gesorteerd in plaats van de hele gegevensstroom te sorteren. 

**Voorwaarden sorteren:** Kies op welke kolommen u wilt sorteren en in welke volgorde de sortering wordt gemaakt. De volgorde bepaalt de sorteerprioriteit. Kies of null-waarden aan het begin of einde van de gegevensstroom worden weergegeven.

### <a name="computed-columns"></a>Berekende kolommen

Als u een kolomwaarde wilt wijzigen of extraheren voordat u de sortering gaat toepassen, beweegt u de muisaanwijzer over de kolom en selecteert u 'berekende kolom'. Hiermee opent u de opbouwer van expressies om een expressie voor de sorteerbewerking te maken in plaats van een kolomwaarde te gebruiken.

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntax

```
<incomingStream>
    sort(
        desc(<sortColumn1>, { true | false }),
        asc(<sortColumn2>, { true | false }),
        ...
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Voorbeeld

![Sorteerinstellingen](media/data-flow/sort.png "Sorteren")

Het gegevensstroomscript voor de bovenstaande sorteerconfiguratie staat in het onderstaande codefragment.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Volgende stappen

Na het sorteren kunt u de [aggregatietransformatie gebruiken](data-flow-aggregate.md)
