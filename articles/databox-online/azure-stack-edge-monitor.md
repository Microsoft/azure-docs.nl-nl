---
title: Uw Azure Stack Edge Pro-apparaat bewaken | Microsoft Docs
description: Hierin wordt beschreven hoe u de Azure Portal en de lokale web-UI gebruikt om uw Azure Stack Edge Pro te bewaken.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/04/2021
ms.author: alkohli
ms.openlocfilehash: aae64cad3603725a4062d5afb42df974bbf8ac40
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438780"
---
# <a name="monitor-your-azure-stack-edge-pro"></a>Uw Azure Stack Edge Pro bewaken

[!INCLUDE [applies-to-GPU-and-pro-r-mini-r-and-fpga-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-fpga-sku.md)]

In dit artikel wordt beschreven hoe u uw Azure Stack Edge Pro kunt bewaken. Als u uw apparaat wilt bewaken, kunt u Azure Portal of de lokale web-UI gebruiken. Gebruik de Azure Portal voor het weer geven van faxgebeurtenissen, het configureren en beheren van waarschuwingen en het weer geven van metrische gegevens. Gebruik de lokale webgebruikersinterface op het fysieke apparaat om de hardware-status van de verschillende onderdelen van het apparaat weer te geven.

In dit artikel leert u het volgende:

> [!div class="checklist"]
>
> * De gebeurtenissen van het apparaat en de bijbehorende waarschuwingen weer geven
> * De apparaatstatus van de onderdelen van apparaten weer geven
> * Metrische gegevens over capaciteit en trans acties voor uw apparaat weer geven

## <a name="view-device-events"></a>Faxgebeurtenissen weer geven

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>Hardware-status weer geven

Voer de volgende stappen uit in de lokale web-UI om de hardwarestatus van de onderdelen van uw apparaat weer te geven.

1. Maak verbinding met de lokale gebruikersinterface van uw apparaat.
2. Ga naar **onderhoud > hardware-status**. U kunt de status van de verschillende onderdelen van het apparaat weergeven.

    ![Hardware-status weer geven](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>Metrische gegevens bekijken

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

### <a name="metrics-on-your-device"></a>Metrische gegevens op uw apparaat

In deze sectie worden de metrische gegevens over bewaking op het apparaat beschreven. De metrische gegevens kunnen zijn:

* Metrische gegevens over capaciteit. De metrische gegevens over de capaciteit zijn gerelateerd aan de capaciteit van het apparaat.

* Metrische gegevens van trans actie. De metrische gegevens van de trans actie zijn gerelateerd aan de lees-en schrijf bewerkingen die worden Azure Storage.

* Metrische reken gegevens. De metrische reken gegevens zijn gerelateerd aan het gebruik van de Edge Compute op het apparaat.

In de volgende tabel ziet u een volledige lijst met metrische gegevens:

|Metrische gegevens over capaciteit                     |Description  |
|-------------------------------------|-------------|
|**Beschik bare capaciteit**               | Verwijst naar de grootte van de gegevens die naar het apparaat kunnen worden geschreven. Met andere woorden, deze metriek is de capaciteit die beschikbaar kan worden gemaakt op het apparaat. <br></br>U kunt de capaciteit van het apparaat vrijmaken door de lokale kopie van bestanden te verwijderen die een kopie hebben op het apparaat en in de Cloud.        |
|**Totale capaciteit**                   | Verwijst naar het totale aantal bytes op het apparaat waarnaar gegevens worden geschreven. dit wordt ook wel de totale grootte van de lokale cache genoemd. <br></br> U kunt nu de capaciteit van een bestaand virtueel apparaat verg Roten door een gegevens schijf toe te voegen. Voeg een gegevens schijf toe via het Hyper Visor beheer voor de virtuele machine en start vervolgens de virtuele machine opnieuw op. De lokale opslag groep van het gateway-apparaat wordt uitgebreid met de nieuwe toegevoegde gegevens schijf. <br></br>Ga voor meer informatie naar [een harde schijf toevoegen voor Hyper-V virtuele machine](https://www.youtube.com/watch?v=EWdqUw9tTe4). |

|Metrische gegevens voor transacties              | Description         |
|-------------------------------------|---------|
|**Geüploade Cloud bytes (apparaat)**    | Som van alle bytes die zijn geüpload over alle shares op uw apparaat        |
|**Geüploade Cloud bytes (delen)**     | Geüploade bytes per share. Deze waarde kan zijn: <br></br> Gem. Dit is het (de som van alle geüploade bytes per share/aantal shares),  <br></br>Max. Dit is het maximum aantal bytes dat van een share is geüpload <br></br>Min. Dit is het minimum aantal bytes dat van een share is geüpload      |
|**Door Voer van Cloud downloaden (delen)**| Geladen bytes per share. Deze waarde kan zijn: <br></br> Gem. Dit is de (som van alle bytes die worden gelezen of gedownload naar een share/aantal shares) <br></br> Max. Dit is het maximum aantal bytes dat is gedownload van een share<br></br> en min. Dit is het minimum aantal bytes dat is gedownload van een share  |
|**Lees doorvoer voor Cloud**            | Som van alle bytes die in de Cloud zijn gelezen over alle shares op uw apparaat     |
|**Upload doorvoer van Cloud**          | Som van alle bytes die in de Cloud zijn geschreven over alle shares op uw apparaat     |
|**Upload doorvoer van Cloud (delen)**  | De som van alle bytes die naar de cloud worden geschreven vanuit een share/aantal shares is gemiddeld, Max en min per aandeel      |
|**Lees doorvoer (netwerk)**           | Bevat de systeem netwerk doorvoer voor alle bytes die in de Cloud zijn gelezen. Deze weer gave kan gegevens bevatten die niet zijn beperkt tot shares. <br></br>Bij het splitsen wordt het verkeer voor alle netwerk adapters op het apparaat weer gegeven, met inbegrip van adapters die niet zijn verbonden of ingeschakeld.      |
|**Schrijf doorvoer (netwerk)**       | Bevat de systeem netwerk doorvoer voor alle bytes die worden geschreven naar de Cloud. Deze weer gave kan gegevens bevatten die niet zijn beperkt tot shares. <br></br>Bij het splitsen wordt het verkeer voor alle netwerk adapters op het apparaat weer gegeven, met inbegrip van adapters die niet zijn verbonden of ingeschakeld.          |

| Metrische reken gegevens              | Description         |
|-------------------------------------|---------|
|**Edge Compute-geheugen gebruik**      |           |
|**Edge Compute-percentage CPU**    |         |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Bandbreedte beheren](azure-stack-edge-manage-bandwidth-schedules.md).
Meer informatie over het [beheren van waarschuwings meldingen voor gebeurtenis apparaten](azure-stack-edge-gpu-manage-device-event-alert-notifications.md).
