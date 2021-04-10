---
author: baanders
description: include-bestand voor Azure Digital Apparaatdubbels-model instructies voor de opdracht regel zelf studie
ms.topic: include
ms.date: 3/5/2021
ms.author: baanders
ms.openlocfilehash: a94b9304ecd6c6630f6ad45652e76d2879bbc1b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103463782"
---
1. **Werk het versienummer bij** om aan te geven dat u een meer up-to-date versie van dit model maakt. U doet dit door de *1* aan het einde van de `@id`-waarde rte veranderen in een *2*. Elk nummer dat hoger is dan het huidige versienummer werkt ook.
1. **Wijzig een eigenschap**. Wijzig de naam van eigenschap `Humidity` in *HumidityLevel* (of iets anders als u dat wilt. Als u iets anders gebruikt dan *HumidityLevel*, onthoud dan wat u hebt gebruikt en gebruik het overal in de zelfstudie in plaats van *HumidityLevel*).
1. **Voeg een eigenschap toe**. Plak onder de eigenschap `HumidityLevel` die eindigt op regel 15 de volgende code om de eigenschap `RoomName` toe te voegen aan de ruimte:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="16-20":::

1. **Voeg een relatie toe**. Plak onder de eigenschap `RoomName` die u zojuist hebt toegevoegd de volgende code om de mogelijkheid toe te voegen dat deze tweeling *contains*-relaties kan maken met andere tweelingen:

    :::code language="json" source="~/digital-twins-docs-samples/models/Room.json" range="21-24":::

Wanneer u klaar bent, zou het bijgewerkte model moeten overeenkomen met:

:::code language="json" source="~/digital-twins-docs-samples/models/Room.json":::

Vergeet niet het bestand op te slaan voordat u verdergaat.