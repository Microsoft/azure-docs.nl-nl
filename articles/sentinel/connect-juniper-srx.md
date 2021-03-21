---
title: Juniper-netwerken verbinden met SRX-gegevens naar Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de Juniper SRX Data Connector om Juniper SRX-logboeken te verzamelen in azure Sentinel. Bekijk Juniper SRX-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
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
ms.openlocfilehash: b10c47a31bf1be10c278d4d9e0dce633bc7bff6c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100530634"
---
# <a name="connect-your-juniper-srx-firewall-to-azure-sentinel"></a>Uw Juniper SRX-firewall verbinden met Azure Sentinel

> [!IMPORTANT]
> De Juniper SRX-connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

In dit artikel wordt uitgelegd hoe u uw Juniper SRX-firewall apparaat verbindt met Azure Sentinel. Met de Juniper SRX-gegevens connector kunt u eenvoudig uw SRX-logboeken verbinden met Azure Sentinel, zodat u de gegevens in werkmappen kunt bekijken, er aangepaste waarschuwingen voor moet gebruiken en deze op te nemen om het onderzoek te verbeteren. Integratie tussen Juniper SRX en Azure Sentinel maakt gebruik van syslog.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor de Azure Sentinel-werk ruimte.

- Uw Juniper SRX-oplossing moet worden geconfigureerd voor het exporteren van Logboeken via syslog.

## <a name="forward-juniper-srx-logs-to-the-syslog-agent"></a>Juniper SRX-logboeken door sturen naar de syslog-agent  

Configureer Juniper SRX om syslog-berichten door te sturen naar uw Azure Sentinel-werk ruimte via de syslog-agent.

1. Selecteer in het navigatie menu van de Azure-Sentinel de optie **Data connectors**.

1. Selecteer in de galerie **gegevens connectors** de **Juniper SRX-connector (preview-versie)** en open vervolgens de **pagina connector**.

1. Volg de instructies op de pagina **JUNIPER SRX** -connector:

    1. De agent voor Linux installeren en vrijgeven

        - Kies een Azure Linux-VM of een niet-Azure Linux-machine (fysiek of virtueel).

    1. De logboeken configureren die moeten worden verzameld

        - Selecteer de faciliteiten en ernst in de configuratie van de werkruimte agent.

    1. De Juniper-SRX configureren en verbinden

        - Volg deze instructies om de Juniper SRX te configureren voor het door sturen van syslog.
            - [Verkeers Logboeken (logboeken voor beveiligings beleid)](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)
            - [Systeem logboeken](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)
        - Voor de externe server gebruikt u het IP-adres van de Linux-computer waarop u de Linux-agent hebt geïnstalleerd.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in Log Analytics onder syslog.

Zie het tabblad **volgende stappen** op de pagina connector voor een aantal nuttige voorbeeld query's.

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Juniper SRX verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
