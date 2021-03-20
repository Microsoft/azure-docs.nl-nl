---
title: Positie transformatie in gegevens stroom toewijzen
description: Toewijzings gegevens stroom transformatie van Azure Data Factory gebruiken rang Transformation een classificatie kolom genereren
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/05/2020
ms.openlocfilehash: b7adb6bf13cba5f886b442515e8ba5661cfeb8ef
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96490921"
---
# <a name="rank-transformation-in-mapping-data-flow"></a>Positie transformatie in gegevens stroom toewijzen 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Gebruik de positie transformatie om een geordende classificatie te genereren op basis van de door de gebruiker opgegeven sorteer voorwaarden. 

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GGJo]

## <a name="configuration"></a>Configuratie

![Rang instellingen](media/data-flow/rank-configuration.png "Rang instellingen")

**Hoofdletter gevoelig:** Als een sorteer kolom van het type teken reeks is, wordt de waarde van de aanvraag in de rang orde gefactord. 

**Dichtheid:** Als deze functie is ingeschakeld, is de rang orde van de kolom in hoge volg orde. Elk aantal posities is een opeenvolgend getal en de rang waarden worden niet overgeslagen na een gelijke positie.

**Kolom positie:** De naam van de kolom positie die is gegenereerd. Deze kolom is van het type Long.

**Sorteer voorwaarden:** Kies op welke kolommen u wilt sorteren en in welke volg orde de sorteer volgorde plaatsvindt. De volg orde bepaalt de sorteer prioriteit.

De bovenstaande configuratie neemt binnenkomende basketbal gegevens en maakt een rang kolom met de naam ' pointsRanking '. De rij met de hoogste waarde van de kolom *pts* heeft een *pointsRanking* -waarde van 1.

## <a name="data-flow-script"></a>Script voor gegevensstroom

### <a name="syntax"></a>Syntax

```
<incomingStream>
    rank(
        desc(<sortColumn1>),
        asc(<sortColumn2>),
        ...,
        caseInsensitive: { true | false }
        dense: { true | false }
        output(<rankColumn> as long)
    ) ~> <sortTransformationName<>
```

### <a name="example"></a>Voorbeeld

![Rang instellingen](media/data-flow/rank-configuration.png "Rang instellingen")

Het gegevensstroom script voor de bovenstaande positie configuratie bevindt zich in het volgende code fragment.

```
PruneColumns
    rank(
        desc(PTS, true),
        caseInsensitive: false,
        output(pointsRanking as long),
        dense: false
    ) ~> RankByPoints
```

## <a name="next-steps"></a>Volgende stappen

Rijen filteren op basis van de rang waarden met behulp van de [filter transformatie](data-flow-filter.md).
