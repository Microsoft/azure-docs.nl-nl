---
title: Trans formatie sorteren in toewijzing van gegevens stroom
description: Trans formatie van gegevens Sorteer Azure Data Factory toewijzing
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2020
ms.openlocfilehash: 26852ec77194714c8236856b7cb496170bf0d777
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81606322"
---
# <a name="sort-transformation-in-mapping-data-flow"></a>Trans formatie sorteren in toewijzing van gegevens stroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met de sorteer transformatie kunt u de binnenkomende rijen op de huidige gegevens stroom sorteren. U kunt afzonderlijke kolommen kiezen en deze in oplopende of aflopende volg orde sorteren.

> [!NOTE]
> Toewijzing van gegevens stromen worden uitgevoerd op Spark-clusters waarmee gegevens over meerdere knoop punten en partities worden gedistribueerd. Als u ervoor kiest om uw gegevens in een volgende trans formatie opnieuw te partitioneren, kunt u uw Sorteer bewerking verliezen door de volg orde van de gegevens opnieuw in te delen.

## <a name="configuration"></a>Configuratie

![Instellingen sorteren](media/data-flow/sort.png "Sorteren")

**Hoofdletter gevoelig:** Hiermee wordt aangegeven of u al dan niet wilt negeren bij het sorteren van teken reeks-of tekst velden

**Alleen sorteren in partities:** Naarmate gegevens stromen worden uitgevoerd op Spark, wordt elke gegevens stroom onderverdeeld in partities. Met deze instelling worden alleen gegevens in de binnenkomende partities gesorteerd, in plaats van de hele gegevens stroom te sorteren. 

**Sorteer voorwaarden:** Kies op welke kolommen u wilt sorteren en in welke volg orde de sorteer bewerking plaatsvindt. De volg orde bepaalt de sorteer prioriteit. Kies of nullen worden weer gegeven aan het begin of einde van de gegevens stroom.

### <a name="computed-columns"></a>Berekende kolommen

Als u een kolom waarde wilt wijzigen of ophalen voordat u de sortering toepast, houdt u de muis aanwijzer boven de kolom en selecteert u berekende kolom. Hiermee opent u de opbouw functie voor expressies om een expressie voor de sorteer bewerking te maken, in plaats van een kolom waarde te gebruiken.

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

![Instellingen sorteren](media/data-flow/sort.png "Sorteren")

Het gegevens stroom script voor de bovenstaande Sorteer configuratie vindt u in het code fragment hieronder.

```
BasketballStats sort(desc(PTS, true),
    asc(Age, true)) ~> Sort1
```

## <a name="next-steps"></a>Volgende stappen

Na het sorteren kunt u de [geaggregeerde trans formatie](data-flow-aggregate.md) gebruiken
