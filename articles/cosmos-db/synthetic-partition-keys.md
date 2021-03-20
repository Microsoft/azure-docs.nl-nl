---
title: Een synthetische partitie sleutel maken in Azure Cosmos DB
description: Meer informatie over het gebruik van synthetische partitie sleutels in uw Azure Cosmos-containers om de gegevens en werk belasting gelijkmatig te verdelen over de partitie sleutels
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/03/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 6b8bc44f1ba5624c37620205aaa574e618ef395f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340648"
---
# <a name="create-a-synthetic-partition-key"></a>Een synthetische partitiesleutel maken
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Het is de best practice om een partitie sleutel te hebben met veel verschillende waarden, zoals honderden of duizend tallen. Het doel is om uw gegevens en werk belasting gelijkmatig te verdelen over de items die zijn gekoppeld aan deze partitie sleutel waarden. Als een dergelijke eigenschap niet in uw gegevens bestaat, kunt u een *synthetische partitie sleutel* maken. In dit document worden verschillende basis technieken voor het genereren van een synthetische partitie sleutel voor uw Cosmos-container beschreven.

## <a name="concatenate-multiple-properties-of-an-item"></a>Meerdere eigenschappen van een item samen voegen

U kunt een partitie sleutel maken door meerdere eigenschaps waarden samen te voegen tot één kunst matige `partitionKey` eigenschap. Deze sleutels worden synthetische sleutels genoemd. Bekijk bijvoorbeeld het volgende voorbeeld document:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Voor het vorige document is één optie om/deviceId of/date in te stellen als de partitie sleutel. Gebruik deze optie als u uw container wilt partitioneren op basis van de apparaat-ID of-datum. Een andere optie is om deze twee waarden samen te voegen in een synthetische `partitionKey` eigenschap die wordt gebruikt als de partitie sleutel.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

In realtime scenario's kunt u duizenden items in een Data Base hebben. In plaats van de synthetische sleutel hand matig toe te voegen, definieert u logica aan client zijde om waarden samen te voegen en de synthetische sleutel in te voegen in de items in uw Cosmos-containers.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Een partitie sleutel met een wille keurig achtervoegsel gebruiken

Een andere mogelijke strategie om de werk belasting gelijkmatiger te verdelen is om een wille keurig getal toe te voegen aan het einde van de waarde van de partitie sleutel. Wanneer u items op deze manier distribueert, kunt u parallelle schrijf bewerkingen uitvoeren op meerdere partities.

Een voor beeld is een partitie sleutel die een datum voor stelt. U kunt een wille keurig getal tussen 1 en 400 kiezen en dit samen voegen als een achtervoegsel voor de datum. Deze methode resulteert in de partitie sleutel waarden zoals  `2018-08-09.1` , `2018-08-09.2` , enzovoort, tot en met  `2018-08-09.400` . Omdat u de partitie sleutel wille keurig maakt, worden de schrijf bewerkingen op de container op elke dag gelijkmatig verdeeld over meerdere partities. Deze methode resulteert in een betere parallellisme en een hogere door voer.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Een partitie sleutel met vooraf berekende achtervoegsels gebruiken 

De strategie voor wille keurige achtervoegsels kan de schrijf doorvoer aanzienlijk verbeteren, maar het is moeilijk om een specifiek item te lezen. U weet niet welke achtervoegsel waarde is gebruikt toen u het item schreef. Gebruik de vooraf berekende achtervoegsels strategie om het gemakkelijker te maken om afzonderlijke items te lezen. In plaats van een wille keurig getal te gebruiken om de items te verdelen over de partities, gebruikt u een getal dat wordt berekend op basis van iets dat u wilt opvragen.

Bekijk het vorige voor beeld, waarbij een container een datum als de partitie sleutel gebruikt. Stel nu dat elk item een  `Vehicle-Identification-Number` kenmerk ( `VIN` ) heeft dat we willen gebruiken. Stel dat u vaak query's uitvoert om items te vinden op de `VIN` , naast datum. Voordat uw toepassing het item naar de container schrijft, kan het een hash-achtervoegsel berekenen op basis van het chassis nummer en dit toevoegen aan de datum van de partitie sleutel. Tijdens de berekening wordt mogelijk een getal tussen 1 en 400 gegenereerd dat gelijkmatig is gedistribueerd. Dit resultaat is vergelijkbaar met de resultaten die door de strategie methode wille keurig achtervoegsel worden geproduceerd. De waarde van de partitie sleutel is vervolgens de datum die wordt samengevoegd met het berekende resultaat.

Met deze strategie worden de schrijf bewerkingen gelijkmatig verdeeld over de waarden van de partitie sleutel en over de partities. U kunt eenvoudig een bepaald item en datum lezen, omdat u de partitie sleutel waarde voor een specifieke kunt berekenen `Vehicle-Identification-Number` . Het voor deel van deze methode is dat u voor komt dat u een enkele Hot-partitie sleutel maakt, dat wil zeggen een partitie sleutel die alle werk belasting neemt. 

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het partitioneren concept vindt u in de volgende artikelen:

* Meer informatie over [logische partities](partitioning-overview.md).
* Meer informatie over het [inrichten van de door Voer voor Azure Cosmos-containers en-data bases](set-throughput.md).
* Meer informatie over het [inrichten van de door Voer voor een Azure Cosmos-container](how-to-provision-container-throughput.md).
* Meer informatie over het [inrichten van de door Voer voor een Azure Cosmos-data base](how-to-provision-database-throughput.md).
