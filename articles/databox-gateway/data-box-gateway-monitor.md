---
title: Uw Azure Data Box Gateway apparaat controleren | Microsoft Docs
description: Hierin wordt beschreven hoe u de Azure Portal en de lokale web-UI gebruikt om uw Azure Data Box Gateway te bewaken.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: how-to
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: ed05f3d60f8ba4fbb06327136c7a117ae1d1d2db
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581963"
---
# <a name="monitor-your-azure-data-box-gateway"></a>Uw Azure Data Box Gateway bewaken

In dit artikel wordt beschreven hoe u uw Azure Data Box Gateway bewaakt. Als u uw apparaat wilt bewaken, kunt u Azure Portal of de lokale web-UI gebruiken. Gebruik de Azure Portal voor het weer geven van faxgebeurtenissen, het configureren en beheren van waarschuwingen en het weer geven van metrische gegevens.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * De gebeurtenissen van het apparaat en de bijbehorende waarschuwingen weer geven
> * Metrische gegevens over capaciteit en trans acties voor uw apparaat weer geven
> * Waarschuwingen configureren en beheren

## <a name="view-device-events"></a>Faxgebeurtenissen weer geven

[!INCLUDE [data-box-gateway-view-device-events](../../includes/data-box-gateway-view-device-events.md)]

## <a name="view-metrics"></a>Metrische gegevens bekijken

[!INCLUDE [data-box-gateway-view-metrics](../../includes/data-box-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>Metrische gegevens op uw apparaat

In deze sectie worden de metrische gegevens over bewaking op het apparaat beschreven. De metrische gegevens kunnen zijn:

* Metrische gegevens over capaciteit. De metrische gegevens over de capaciteit zijn gerelateerd aan de capaciteit van het apparaat.

* Metrische gegevens van trans actie. De metrische gegevens van de trans actie zijn gerelateerd aan de lees-en schrijf bewerkingen die worden Azure Storage.

In de volgende tabel ziet u een volledige lijst met metrische gegevens:

|Metrische gegevens over capaciteit                     |Beschrijving  |
|-------------------------------------|-------------|
|**Beschik bare capaciteit**               | Verwijst naar de grootte van de gegevens die naar het apparaat kunnen worden geschreven. Met andere woorden, dit is de capaciteit die beschikbaar kan worden gemaakt op het apparaat. <br></br>U kunt de capaciteit van het apparaat vrijmaken door de lokale kopie van bestanden te verwijderen die zowel op het apparaat als in de Cloud zijn gekopieerd.        |
|**Totale capaciteit**                   | Verwijst naar het totale aantal bytes op het apparaat om gegevens naar te schrijven. Dit wordt ook wel de totale grootte van de lokale cache genoemd. <br></br> U kunt nu de capaciteit van een bestaand virtueel apparaat verg Roten door een gegevens schijf toe te voegen. Voeg een gegevens schijf toe via het Hyper Visor beheer voor de virtuele machine en start vervolgens de virtuele machine opnieuw op. De lokale opslag groep van het gateway-apparaat wordt uitgebreid met de nieuwe toegevoegde gegevens schijf. <br></br>Ga voor meer informatie naar [een harde schijf toevoegen voor Hyper-V virtuele machine](https://www.youtube.com/watch?v=EWdqUw9tTe4). |

|Metrische gegevens voor transacties              | Beschrijving         |
|-------------------------------------|---------|
|**Geüploade Cloud bytes (apparaat)**    | Som van alle bytes die zijn geüpload over alle shares op uw apparaat        |
|**Geüploade Cloud bytes (delen)**     | Geüploade bytes per share. Dit kan zijn: <br></br> Gem. Dit is het (de som van alle geüploade bytes per share/aantal shares),  <br></br>Max. Dit is het maximum aantal bytes dat van een share is geüpload <br></br>Min. Dit is het minimum aantal bytes dat van een share is geüpload      |
|**Door Voer van Cloud downloaden (delen)**| Geladen bytes per share. Dit kan zijn: <br></br> Gem. Dit is de (som van alle bytes die worden gelezen of gedownload naar een share/aantal shares) <br></br> Max. Dit is het maximum aantal bytes dat is gedownload van een share<br></br> en min. Dit is het minimum aantal bytes dat is gedownload van een share  |
|**Lees doorvoer voor Cloud**            | Som van alle bytes die in de Cloud zijn gelezen over alle shares op uw apparaat     |
|**Upload doorvoer van Cloud**          | Som van alle bytes die in de Cloud zijn geschreven over alle shares op uw apparaat     |
|**Upload doorvoer van Cloud (delen)**  | De som van alle bytes die naar de cloud worden geschreven vanuit een share/aantal shares is gemiddeld, Max en min per aandeel      |
|**Lees doorvoer (netwerk)**           | Bevat de systeem netwerk doorvoer voor alle bytes die in de Cloud zijn gelezen. Deze weer gave kan gegevens bevatten die niet zijn beperkt tot shares. <br></br>Bij het splitsen wordt het verkeer voor alle netwerk adapters op het apparaat weer gegeven. Dit omvat adapters die niet zijn verbonden of ingeschakeld.      |
|**Schrijf doorvoer (netwerk)**       | Bevat de systeem netwerk doorvoer voor alle bytes die worden geschreven naar de Cloud. Deze weer gave kan gegevens bevatten die niet zijn beperkt tot shares. <br></br>Bij het splitsen wordt het verkeer voor alle netwerk adapters op het apparaat weer gegeven. Dit omvat adapters die niet zijn verbonden of ingeschakeld.          |

## <a name="manage-alerts"></a>Waarschuwingen beheren

[!INCLUDE [data-box-gateway-manage-alerts](../../includes/data-box-gateway-manage-alerts.md)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Bandbreedte beheren](data-box-gateway-manage-bandwidth-schedules.md).
