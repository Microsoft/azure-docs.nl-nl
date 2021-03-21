---
title: Proofpoint on demand e-mail beveiligings gegevens aan Azure-Sentinel koppelen | Microsoft Docs
description: Meer informatie over het gebruik van de Proofpoint on demand E-mail Security Data Connector om POD e-mail beveiligings logboeken te verzamelen in azure Sentinel. Bekijk POD-e-mail beveiligings gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 73a9d9c7ab321aebd615922e5d4395c0318e809c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100580452"
---
# <a name="connect-your-proofpoint-on-demand-email-security-pod-solution-to-azure-sentinel"></a>Verbind uw Proofpoint-oplossing voor e-mail beveiliging (POD) op aanvraag met Azure Sentinel

> [!IMPORTANT]
> De Proofpoint on demand E-mail Security connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

In dit artikel wordt uitgelegd hoe u uw Proofpoint-e-mail beveiligings apparaat op aanvraag verbindt met Azure Sentinel. Met de POD-gegevens connector kunt u eenvoudig uw POD-logboeken verbinden met Azure-Sentinel, zodat u de gegevens in werkmappen kunt bekijken, er aangepaste waarschuwingen voor maakt en hoe u deze integreert om het onderzoek te verbeteren.  Integratie tussen Proofpoint on demand e-mail beveiliging en Azure Sentinel maakt gebruik van de WebSocket-API.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor de Azure Sentinel-werk ruimte.

- U moet Lees machtigingen hebben voor gedeelde sleutels voor de werk ruimte. [Meer informatie over werkruimte sleutels](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

- U moet lees-en schrijf machtigingen hebben voor Azure Functions om een functie-app te kunnen maken. Meer [informatie over Azure functions](../azure-functions/index.yml).

- U moet de volgende WebSocket-API-referenties hebben: ProofpointClusterID, ProofpointToken. Meer [informatie over de WebSocket-API](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API).

## <a name="configure-and-connect-proofpoint-on-demand-email-security"></a>E-mail beveiliging op aanvraag Proofpoint configureren en verbinden

Proofpoint on demand e-mail beveiliging kunnen Logboeken rechtstreeks integreren en exporteren naar Azure Sentinel.

1. Selecteer in het navigatie menu van de Azure-Sentinel de optie **Data connectors**.

1. Selecteer in de galerie met **gegevens connectors** de optie **Proofpoint on demand E-mail Security (preview)** en open vervolgens de **pagina connector**.

1. Volg de stappen die worden beschreven in de sectie **configuratie** van de pagina connector.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken**, onder *aangepaste logboeken*, in de volgende tabellen:
- `ProofpointPOD_message_CL`
- `ProofpointPOD_maillog_CL`

Zie het tabblad **volgende stappen** op de pagina connector voor een aantal nuttige voorbeeld query's.

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan tot 60 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Proofpoint op aanvraag-e-mail beveiliging verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.