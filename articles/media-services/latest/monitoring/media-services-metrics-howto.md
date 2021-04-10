---
title: Metrische gegevens weer geven met Azure Monitor
description: In dit artikel wordt uitgelegd hoe u metrische gegevens kunt controleren met behulp van de Azure Portal grafieken en Azure CLI.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/17/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 306381465919d3fde7135315b69d450a496c2898
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104609314"
---
# <a name="monitor-media-services-metrics"></a>Metrische gegevens voor Media Services controleren

[!INCLUDE [media services api v3 logo](../includes/v3-hr.md)]

Met [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview.md) kunt u metrische gegevens en Diagnostische logboeken bewaken die u helpen begrijpen hoe uw toepassingen worden uitgevoerd. Zie [Media Services metrische gegevens en Diagnostische logboeken controleren](monitor-media-services-data-reference.md)voor een gedetailleerde beschrijving van deze functie en om te begrijpen waarom u Azure Media Services metrische gegevens en Diagnostische logboeken moet gebruiken.

Azure Monitor biedt verschillende manieren om te communiceren met metrische gegevens, zoals het maken van grafieken in de portal, het openen ervan via de REST API of het opvragen van query's met behulp van Azure CLI. In dit artikel wordt uitgelegd hoe u metrische gegevens kunt controleren met behulp van de Azure Portal grafieken en Azure CLI.

## <a name="prerequisites"></a>Vereisten

- [Een Azure Media Services-account maken](../create-account-howto.md)
- Controleren  [Media Services metrische gegevens en Diagnostische logboeken](monitor-media-services-data-reference.md)

## <a name="view-metrics-in-azure-portal"></a>Metrische gegevens weer geven in Azure Portal

1. Meld u aan bij Azure Portal op https://portal.azure.com.
1. Navigeer naar uw Azure Media Services-account en selecteer **metrische gegevens**.
1. Klik op het vak **bereik** en selecteer de resource die u wilt bewaken.

    Het venster **een bereik selecteren** wordt weer gegeven aan de rechter kant met de lijst met beschik bare resources. In dit geval ziet u:

    * &lt;Media Services-account naam&gt;
    * &lt;Naam van Media Services account naam van &gt; / &lt; streaming-eind punt&gt;
    * &lt;naam van opslag account&gt;

    Filter vervolgens de resource en druk op **Toep assen**. Zie [Media Services metrische gegevens controleren](monitor-media-services-data-reference.md)voor meer informatie over ondersteunde bronnen en metrische gegevens.

    > [!NOTE]
    > Als u wilt scha kelen tussen resources die u wilt bewaken, klikt u nogmaals op het vak **bron** en herhaalt u deze stap.

1. Optioneel: Geef een naam op voor de grafiek (Bewerk de naam door bovenaan op het potlood te drukken).
1. Voeg de metrische gegevens toe die u wilt weer geven.
1. U kunt uw grafiek vastmaken aan uw dash board.

## <a name="view-metrics-with-azure-cli"></a>Metrische gegevens weer geven met Azure CLI

Voer de volgende opdracht uit om de metrische gegevens van ' uitgang ' met Azure CLI te verkrijgen `az monitor metrics` :

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Als u andere metrische gegevens wilt ophalen, vervangt u ' uitgang ' door de naam van de metrische gegevens die u wilt gebruiken.

## <a name="see-also"></a>Zie ook

- [Metrische gegevens van Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/data-platform.md)
- [Metrische waarschuwingen maken, weer geven en beheren met behulp van Azure monitor](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-metric.md).

## <a name="next-steps"></a>Volgende stappen

[Diagnostische logboeken](../media-services-diagnostic-logs-howto.md)
