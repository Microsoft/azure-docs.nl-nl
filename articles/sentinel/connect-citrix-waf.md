---
title: Citrix WAF-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de Citrix WAF Data Connector om de logboeken in azure Sentinel te halen. Bekijk Citrix WAF-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/25/2020
ms.author: yelevin
ms.openlocfilehash: 475e04ad3dd8c7cc51d6345b4b51dd68cff5b597
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93102977"
---
# <a name="connect-your-citrix-waf-to-azure-sentinel"></a>Verbind uw Citrix WAF met Azure Sentinel

> [!IMPORTANT]
> De Citrix Web Application firewall-gegevens connector (WAF) in azure Sentinel is momenteel beschikbaar als open bare preview. Deze functie wordt zonder service level agreement gegeven. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

In dit artikel wordt uitgelegd hoe u uw Citrix Web Application firewall (WAF)-apparaat verbindt met Azure Sentinel. Met de Citrix WAF Data Connector kunt u eenvoudig uw Citrix WAF-logboeken verbinden met Azure Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van onderzoek. Door Citrix WAF CEF-logboeken te verbinden met Azure Sentinel, kunt u profiteren van zoek-en correlatie-, waarschuwings-en bedreigings informatie verrijken voor elk logboek.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="forward-citrix-waf-logs-to-the-syslog-agent"></a>Citrix WAF-logboeken door sturen naar de syslog-agent  

Citrix WAF verzendt syslog-berichten in CEF-indeling naar een op Linux gebaseerde logboek server (met rsyslog of syslog-ng) waarop de Log Analytics-agent is geïnstalleerd, waardoor de logboeken worden doorgestuurd naar uw Azure Sentinel-werk ruimte.

1. Als u een dergelijke server voor het door sturen van logboeken niet hebt, raadpleegt u [deze instructies](connect-cef-agent.md) om er een te maken en uit te voeren.

1. Volg de instructies van Citrix voor [het configureren van de WAF, het](https://support.citrix.com/article/CTX234174) [configureren van CEF-logboek registratie](https://support.citrix.com/article/CTX136146)en [het configureren van de logboeken naar uw logboek-doorstuur server](https://docs.citrix.com/en-us/citrix-adc/13/system/audit-logging/configuring-audit-logging.html). Zorg ervoor dat u de logboeken verzendt naar TCP-poort 514 op het IP-adres van de logboek doorstuur machine.

1. Valideer uw verbinding en controleer de gegevens opname met behulp van [deze instructies](connect-cef-verify.md). Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken**, onder de sectie voor het **Sentinel van Azure** in de tabel *CommonSecurityLog* .

Als u een query wilt uitvoeren op de Citrix WAF-Logboeken in Log Analytics, voert u `CommonSecurityLog` boven in het query venster.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Citrix WAF verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.