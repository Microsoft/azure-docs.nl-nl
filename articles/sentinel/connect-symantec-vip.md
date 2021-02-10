---
title: Symantec-VIP-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Symantec VIP-gegevens met Azure Sentinel.
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
ms.openlocfilehash: c7429108f70d735cb6e314a0d4daa27ba0d31637
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100090408"
---
# <a name="connect-your-symantec-vip-to-azure-sentinel"></a>Uw Symantec VIP verbinden met Azure Sentinel

> [!IMPORTANT]
> De Symantec VIP Data Connector in azure Sentinel is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel wordt uitgelegd hoe u uw [Symantec-VIP](https://vip.symantec.com/) -apparaat verbindt met Azure Sentinel. Met de Symantec VIP Data Connector kunt u eenvoudig uw Symantec-VIP-logboeken verbinden met Azure Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van het onderzoek. Integratie tussen Symantec VIP en Azure Sentinel maakt gebruik van syslog.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="forward-symantec-vip-logs-to-the-syslog-agent"></a>Symantec VIP-logboeken door sturen naar de syslog-agent  

Symantec VIP configureren voor het door sturen van syslog-berichten naar uw Azure-werk ruimte via de syslog-agent.

1. Klik in de Azure-Sentinel-Portal op **gegevens connectors** en selecteer **Symantec VIP** connector.

1. Selecteer de **pagina connector openen**.

1. Volg de instructies op de pagina **Symantec VIP** .

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in Log Analytics onder syslog.

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Symantec-VIP-logboeken verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.