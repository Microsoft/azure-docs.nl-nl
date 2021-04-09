---
title: Uw Imperva WAF-gateway apparaat verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de Imperva WAF gateway connector om Imperva WAF-logboeken te verzamelen in azure Sentinel. Bekijk Imperva WAF-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
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
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: a37abf369d1f34dc8f4a27802dfad88dab79be44
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101698430"
---
# <a name="connect-your-imperva-waf-gateway-appliance-to-azure-sentinel"></a>Uw Imperva WAF-gateway apparaat verbinden met Azure Sentinel

> [!IMPORTANT]
> De Imperva WAF-gateway connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

In dit artikel wordt uitgelegd hoe u uw Imperva WAF-gateway apparaat verbindt met Azure Sentinel. Met de Imperva WAF gateway-gegevens connector kunt u eenvoudig uw Imperva WAF gateway-logboeken verbinden met Azure Sentinel, zodat u de gegevens in werkmappen kunt bekijken, er aangepaste waarschuwingen voor moet gebruiken en deze op te nemen om het onderzoek te verbeteren. Integratie tussen Imperva WAF gateway en Azure Sentinel maakt gebruik van CEF-indeling syslog, een op Linux gebaseerde logboek doorstuur server en de Log Analytics-agent.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor uw Azure-Sentinel-werk ruimte.

- U moet Lees machtigingen hebben voor gedeelde sleutels voor de werk ruimte. [Meer informatie over werkruimte sleutels](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-imperva-waf-gateway-logs-to-azure-sentinel"></a>Imperva WAF-gateway logboeken naar Azure Sentinel verzenden

Als u de logboeken van Azure Sentinel wilt ontvangen, configureert u uw Imperva WAF-gateway apparaat om syslog-berichten te verzenden in CEF-indeling naar een op Linux gebaseerde logboek server voor door sturen (met rsyslog of syslog-aardgas). Op deze server wordt de Log Analytics-agent geïnstalleerd en de agent stuurt de logboeken door naar uw Azure Sentinel-werk ruimte.

1. Selecteer in het navigatie menu van de Azure-Sentinel de optie **Data connectors**.

1. Selecteer in de galerie **Data connectors** de optie **Imperva WAF gateway (preview)** en **Open vervolgens de pagina connector**.

1. Volg de instructies op het tabblad **instructies** onder **configuratie**:

    1. Onder **1. Configuratie van Linux syslog-agent** : Voer deze stap uit als u nog geen logboek doorstuur server hebt of als u er een hebt. Zie [stap 1: de doorstuur server voor logboeken implementeren](connect-cef-agent.md) in de Azure-Sentinel-documentatie voor meer gedetailleerde instructies en uitleg.

    1. Onder **2. CEF-Logboeken (common Event Format) door sturen naar syslog-agent** : voor deze connector zijn een **actie-interface** en een **actie ingesteld** die moeten worden gemaakt in de **Imperva SecureSphere MX** -beheer console. Volg de instructies van Imperva om [logboek registratie van IMPERVA WAF-gateway in te scha kelen voor Azure Sentinel](https://community.imperva.com/blogs/craig-burlingame1/2020/11/13/steps-for-enabling-imperva-waf-gateway-alert). Deze configuratie moet de volgende elementen bevatten:
        - Logboek bestemming: de hostnaam en/of het IP-adres van de server voor het door sturen van Logboeken
        - Protocol en poort: TCP 514
        - Logboek indeling – CEF
        - Logboek typen: alle beschikbaar

    1. Minder dan **3. Verbinding valideren** : Controleer de gegevens opname door de opdracht op de pagina connector te kopiëren en op de logboek-doorstuur server uit te voeren. Zie [stap 3: connectiviteit valideren](connect-cef-verify.md) in de informatie over de Azure-Sentinel voor meer gedetailleerde instructies en uitleg.

        Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken**, onder de sectie voor het **Sentinel van Azure** in de tabel *CommonSecurityLog* .

Als u een query wilt uitvoeren op Imperva WAF-gateway gegevens in Log Analytics, kopieert u het volgende in het query venster en past u andere filters toe wanneer u kiest:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Imperva Inc." 
| where DeviceProduct == "WAF Gateway" 
| where TimeGenerated == ago(5m)
```

Zie het tabblad **volgende stappen** op de pagina connector voor meer nuttige query voorbeelden.

## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Imperva WAF-gateway verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.