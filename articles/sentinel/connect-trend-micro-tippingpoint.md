---
title: Trend Micro TippingPoint verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de trend micro TippingPoint Data Connector om TippingPoint SMS-logboeken te halen in azure Sentinel. Bekijk de TippingPoint-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
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
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 549b4e1e5e1aef3f6957fa52d69d252c55934286
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541550"
---
# <a name="connect-your-trend-micro-tippingpoint-solution-to-azure-sentinel"></a>Uw Trend Micro TippingPoint-oplossing verbinden met Azure Sentinel

> [!IMPORTANT]
> De trend micro TippingPoint-connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

In dit artikel wordt uitgelegd hoe u de oplossing van uw Trend Micro TippingPoint Threat Protection-systeem kunt verbinden met Azure Sentinel. Met de trend micro TippingPoint Data Connector kunt u eenvoudig uw TippingPoint Security Management System-Logboeken (SMS) verbinden met Azure Sentinel, zodat u de gegevens in werkmappen kunt bekijken, er aangepaste waarschuwingen voor moet maken en deze moet opnemen om het onderzoek te verbeteren. 

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor uw Azure-Sentinel-werk ruimte.

- U moet Lees machtigingen hebben voor gedeelde sleutels voor de werk ruimte.

## <a name="send-trend-micro-tippingpoint-logs-to-azure-sentinel"></a>Trend Micro TippingPoint-logboeken naar Azure Sentinel verzenden

Als u de logboeken van Azure Sentinel wilt ontvangen, moet u uw TippingPoint TPS-oplossing configureren voor het verzenden van syslog-berichten in de CEF-indeling naar een op Linux gebaseerde logboek server voor door sturen (met rsyslog of syslog-aardgas). Op deze server wordt de Log Analytics-agent geïnstalleerd en de agent stuurt de logboeken door naar uw Azure Sentinel-werk ruimte.

1. Selecteer in het navigatie menu van de Azure-Sentinel de optie **Data connectors**.

1. Selecteer in de galerie **gegevens connectors** de optie **Trend Micro-TippingPoint (preview)** en open vervolgens de **pagina connector**.

1. Volg de instructies op het tabblad **instructies** onder **configuratie**:

    1. **1. configuratie van Linux syslog-agent** : Voer deze stap uit als u nog geen logboek doorstuur server hebt of als u er een hebt. Zie [stap 1: de doorstuur server voor logboeken implementeren](connect-cef-agent.md) in de Azure-Sentinel-documentatie voor meer gedetailleerde instructies en uitleg.

    1. **2. forward Trend Micro TIPPINGPOINT SMS-logboeken naar syslog-agent** : deze configuratie moet de volgende elementen bevatten:
        - Logboek bestemming: de hostnaam en/of het IP-adres van de server voor het door sturen van Logboeken
        - Protocol en poort – **TCP 514** (als u dit niet doet, moet u ervoor zorgen dat u de parallelle wijziging aanbrengt in de syslog-daemon op de server voor het door sturen van Logboeken)
        - Logboek indeling – **ARCSIGHT CEF Format v 4.2**
        - Logboek typen: alle beschikbaar

    1. **3. Valideer de verbinding** -Controleer de gegevens opname door de opdracht op de pagina connector te kopiëren en op de logboek-doorstuur server uit te voeren. Zie [stap 3: connectiviteit valideren](connect-cef-verify.md) in de informatie over de Azure-Sentinel voor meer gedetailleerde instructies en uitleg.

        Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken**, onder de sectie voor het **Sentinel van Azure** in de tabel *CommonSecurityLog* .

Als u een query wilt uitvoeren op Trend Micro TippingPoint-gegevens in Log Analytics, kopieert u het volgende in het query venster en past u andere filters toe als u dat hebt gekozen:

```kusto
CommonSecurityLog 
| where DeviceVendor == "TrendMicroTippingPoint"
```

Zie het tabblad **volgende stappen** op de pagina connector voor meer query voorbeelden.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Trend Micro TippingPoint verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.