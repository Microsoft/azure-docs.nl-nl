---
title: Schaal baarheid-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat informatie over het schalen van Azure Event Hubs met behulp van partities en doorvoer eenheden.
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: f258ee2a3b4162dabf7a8e615db82b9b889d628b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103601272"
---
# <a name="scaling-with-event-hubs"></a>Schalen met Event Hubs

Er zijn twee factoren die van invloed zijn op schalen met Event Hubs.
*   Doorvoereenheden
*   Partities

## <a name="throughput-units"></a>Doorvoereenheden

De doorvoer capaciteit van Event Hubs wordt bepaald door *doorvoer eenheden*. Doorvoereenheden zijn vooraf aangeschafte capaciteitseenheden. Met één door Voer kunt u:

* Binnenkomend: Maxi maal 1 MB per seconde of 1000 gebeurtenissen per seconde (afhankelijk van wat het eerste komt).
* Uitgang: Maxi maal 2 MB per seconde of 4096 gebeurtenissen per seconde.

Wanneer de capaciteit van de aangekochte doorvoereenheden wordt overschreven, wordt de invoer vertraagd en een [ServerBusyException](/dotnet/api/microsoft.azure.eventhubs.serverbusyexception) afgegeven. De uitvoer geeft geen vertragingsuitzonderingen af, maar is nog steeds beperkt tot de capaciteit van de aangekochte doorvoereenheden. Als zich uitzonderingen met betrekking tot de publicatiesnelheid voordoen of als u meer uitgaande gegevens verwacht, controleert u hoeveel doorvoereenheden u hebt aangeschaft voor de naamruimte. U kunt doorvoereenheden beheren op de blade **Schaal** van de naamruimten in [Azure Portal](https://portal.azure.com). U kunt doorvoer eenheden ook programmatisch beheren met behulp van de [Event hubs-api's](./event-hubs-samples.md).

Doorvoer eenheden zijn vooraf aangeschaft en worden per uur gefactureerd. Nadat u doorvoereenheden hebt aangeschaft, worden deze voor minimaal één uur in rekening gebracht. Er kunnen Maxi maal 20 doorvoer eenheden worden aangeschaft voor een Event Hubs naam ruimte en worden gedeeld door alle Event hubs in die naam ruimte.

De functie **automatisch verg Roten** van Event hubs wordt automatisch geschaald door het aantal doorvoer eenheden te verhogen om te voldoen aan de behoeften van het gebruik. Het verhogen van doorvoer eenheden voor komt het beperken van scenario's, waarbij:

- Tarieven voor gegevens ingang overschrijden de ingestelde doorvoer eenheden.
- De tarieven voor gegevens uitwissel aanvragen overschrijden de ingestelde doorvoer eenheden.

De Event Hubs-service verhoogt de door Voer wanneer de belasting groter wordt dan de minimale drempel waarde, zonder dat er aanvragen worden uitgevoerd met ServerBusy-fouten. 

Zie [automatisch schalen door Voer eenheden](event-hubs-auto-inflate.md)voor meer informatie over de functie voor automatisch verg Roten.

## <a name="partitions"></a>Partities
[!INCLUDE [event-hubs-partitions](../../includes/event-hubs-partitions.md)]




## <a name="next-steps"></a>Volgende stappen
U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

- [Doorvoereenheden automatisch schalen](event-hubs-auto-inflate.md)
- [Overzicht van Event Hubs-service](./event-hubs-about.md)
