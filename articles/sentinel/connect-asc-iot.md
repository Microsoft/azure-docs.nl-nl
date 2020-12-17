---
title: Verbinding maken tussen Azure Defender voor IoT en Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Azure Defender (voorheen Azure Security Center) voor IoT-gegevens naar Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/16/2020
ms.author: yelevin
ms.openlocfilehash: 95e78c7557092a4d1203a8df3a107fe7b63eac9b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631438"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Uw gegevens verbinden met Azure Defender (voorheen Azure Security Center) voor een IoT-naar-Azure-Sentinel 


> [!IMPORTANT]
> De Defender voor IoT-gegevens connector bevindt zich momenteel in de open bare preview. Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Gebruik de Defender voor IoT-connector om al uw Defender voor IoT-gebeurtenissen in azure Sentinel te streamen. 

Met deze integratie kunnen organisaties snel multi-fase-aanvallen detecteren die vaak de grenzen intervallen. Daarnaast maakt Defender voor IoT-integratie met de mogelijkheden van Security Orchestration, Automation en Response (via) van Azure Sentinel automatische reactie en voor komen met behulp van ingebouwde, voor OT geoptimaliseerde playbooks. 
## <a name="prerequisites"></a>Vereisten

- **Lees** -en **Schrijf** machtigingen voor de werk ruimte waarop Azure Sentinel wordt geïmplementeerd
- **Defender voor IOT** moet zijn **ingeschakeld** op uw relevante IOT hub (s)
- U moet **Inzender** machtigingen hebben voor het **abonnement** dat u wilt verbinden

## <a name="connect-to-defender-for-iot"></a>Verbinding maken met Defender voor IoT

1. Selecteer in azure Sentinel **Data connectors** en selecteer vervolgens **Defender voor IOT** (kan nog steeds Azure Security Center voor IOT worden genoemd) in de galerie.

1. Klik onder aan het rechterdeel venster op **connector pagina openen**. 

1. Klik op **verbinding maken** naast elk IOT hub abonnement waarvan u de waarschuwingen en de waarschuwingen van apparaten wilt streamen naar Azure Sentinel. 
    - Er wordt een fout bericht weer gegeven als Defender voor IoT niet is ingeschakeld op ten minste één IoT Hub binnen een abonnement. Schakel Defender voor IoT in het IoT Hub in om de fout te verwijderen.

1. U kunt beslissen of u wilt dat de waarschuwingen van Defender voor IoT automatisch incidenten genereren in azure Sentinel. Selecteer onder **incidenten maken** de optie **inschakelen** om de standaard analyse regel in te scha kelen om automatisch incidenten te maken op basis van de gegenereerde waarschuwingen. Deze regel kan worden gewijzigd of bewerkt onder   >  **actieve regels** voor analyse.

> [!NOTE]
> Het kan 10 seconden of langer duren voordat de lijst met **abonnementen** is vernieuwd na het maken van de verbinding. 

## <a name="log-analytics-alert-view"></a>Waarschuwings weergave Log Analytics

Het relevante schema in Log Analytics gebruiken om de Defender voor IoT-waarschuwingen weer te geven:

1. Open **Logboeken**  >  **SecurityInsights**  >  **SecurityAlert** of zoek naar **SecurityAlert**. 

2. Filter om alleen Defender voor IoT gegenereerde waarschuwingen weer te geven met behulp van het volgende kql-filter:

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>Opmerkingen bij de service

Nadat u verbinding hebt gemaakt met een **abonnement**, zijn de hub-gegevens ongeveer 15 minuten later beschikbaar in azure Sentinel.


## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Defender voor IoT verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
