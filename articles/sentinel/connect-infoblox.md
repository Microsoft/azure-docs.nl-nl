---
title: NIOS-gegevens (Infoblox Network Identity Operating System) verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Infoblox-gegevens (NIOS) voor netwerk identiteiten aan Azure Sentinel.
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 28abb9f09e3bca2522b959c6a9b890de5320b17a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98567444"
---
# <a name="connect-your-infoblox-nios-to-azure-sentinel"></a>Uw Infoblox NIOS verbinden met Azure Sentinel

> [!IMPORTANT]
> De Infoblox NIOS-gegevens connector in azure Sentinel is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel wordt uitgelegd hoe u uw [Nios-apparaat (Infoblox Network Identity Operating System)](https://www.infoblox.com/glossary/network-identity-operating-system-nios/) verbindt met Azure Sentinel. Met de Infoblox NIOS Data Connector kunt u eenvoudig uw Infoblox-logboeken verbinden met Azure Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van het onderzoek. Integratie tussen Infoblox NIOS en Azure Sentinel maakt gebruik van syslog.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="forward-infoblox-logs-to-the-syslog-agent"></a>Infoblox-logboeken door sturen naar de syslog-agent  

Configureer Infoblox voor het door sturen van syslog-berichten naar uw Azure Sentinel-werk ruimte via de syslog-agent.

1. Klik in de Azure-Sentinel-Portal op **Data connectors** en selecteer **Infoblox Nios** -connector.

1. Selecteer de **pagina connector openen**.

1. Volg de instructies op de pagina **INFOBLOX Nios** .

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in Log Analytics onder syslog.

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Infoblox NIOS verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.