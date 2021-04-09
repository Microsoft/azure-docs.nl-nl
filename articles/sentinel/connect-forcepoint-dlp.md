---
title: Force Point DLP verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Force Point DLP met Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: 62ed3915dcaf596d144a2f59817626cdf8ec47e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100092771"
---
# <a name="connect-your-forcepoint-dlp-to-azure-sentinel"></a>Uw Force Point DLP verbinden met Azure Sentinel

> [!IMPORTANT]
> De DLP-gegevens connector (Force Point data verlies preventie) in azure Sentinel is momenteel beschikbaar als open bare preview. Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.



Met de Force Point DLP-connector kunt u automatisch DLP-incident gegevens exporteren naar Azure Sentinel. U kunt deze gebruiken om inzicht te krijgen in de gebruikers activiteiten en incidenten voor gegevens verlies. Het schakelt ook correlaties uit met gegevens van Azure-workloads en andere feeds, en verbetert de bewakings mogelijkheden met werkmappen binnen Azure Sentinel.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-forcepoint-dlp"></a>Force Point DLP configureren en verbinden

Configureer Force Point DLP om incident gegevens in JSON-indeling door te sturen naar uw Azure-werk ruimte via REST API zoals wordt uitgelegd in de [Force Point DLP-integratie handleiding](https://frcpnt.com/dlp-sentinel).


## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de Force Point DLP-connector is ingesteld, worden de gegevens weer gegeven in Log Analytics onder CustomLogs **ForcepointDLPEvents_CL**.


Als u het relevante schema in Log Analytics voor Force Point DLP wilt gebruiken, zoekt u naar **ForcepointDLPEvents_CL**.


## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Force Point DLP kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.