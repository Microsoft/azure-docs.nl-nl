---
title: Gegevens van Cisco Unified computing System (UCS) verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de Cisco UCS Data Connector voor het ophalen van Cisco UCS-Logboeken in azure Sentinel. Bekijk Cisco UCS-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
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
ms.openlocfilehash: caa83b9149f39f69d0cbf44a2d6cb01fdaf29721
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567886"
---
# <a name="connect-your-cisco-unified-computing-system-ucs-to-azure-sentinel"></a>Verbind uw Cisco Unified computing System (UCS) met Azure Sentinel

> [!IMPORTANT]
> De Cisco UCS-connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

In dit artikel wordt uitgelegd hoe u uw Cisco Unified computing System (UCS)-apparaat verbindt met Azure Sentinel. Met de Cisco UCS Data Connector kunt u eenvoudig uw UCS-logboeken met Azure-Sentinel verbinden, zodat u de gegevens in werkmappen kunt bekijken, er aangepaste waarschuwingen voor moet maken en deze moet opnemen om het onderzoek te verbeteren. Integratie tussen Cisco UCS en Azure Sentinel maakt gebruik van syslog.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor de Azure Sentinel-werk ruimte.

- Uw Cisco UCS-oplossing moet worden geconfigureerd voor het exporteren van Logboeken via syslog.

## <a name="forward-cisco-ucs-logs-to-the-syslog-agent"></a>Cisco UCS-logboeken door sturen naar de syslog-agent  

Configureer Cisco UCS om syslog-berichten door te sturen naar uw Azure Sentinel-werk ruimte via de syslog-agent.

1. Selecteer in het navigatie menu van de Azure-Sentinel de optie **Data connectors**.

1. Selecteer in de galerie **gegevens connectors** de **Cisco UCS-connector (preview-versie)** en open vervolgens de **pagina connector**.

1. Volg de instructies op de pagina **Cisco UCS** connector:

    1. De agent voor Linux installeren en vrijgeven

        - Kies een Azure Linux-VM of een niet-Azure Linux-machine (fysiek of virtueel).

    1. De logboeken configureren die moeten worden verzameld

        - Selecteer de faciliteiten en ernst in de configuratie van de geavanceerde instellingen voor de werk ruimte

    1. De Cisco UCS configureren en verbinden

        - Volg [deze instructies](https://www.cisco.com/c/en/us/support/docs/servers-unified-computing/ucs-manager/110265-setup-syslog-for-ucs.html#configsremotesyslog) om de Cisco UCS te configureren voor het door sturen van syslog. Voor de externe server gebruikt u het IP-adres van de Linux-computer waarop u de Linux-agent hebt geïnstalleerd.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in Log Analytics onder syslog.

Zie het tabblad **volgende stappen** op de pagina connector voor een aantal nuttige voorbeeld query's.

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Cisco UCS kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
