---
title: Aruba ClearPass-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de Aruba ClearPass-connector om Aruba ClearPass-logboeken te verzamelen in azure Sentinel. Bekijk Aruba ClearPass-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 8050b4f173476d7af66cb858ff5f785e5a12af43
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105046569"
---
# <a name="connect-your-aruba-clearpass-to-azure-sentinel"></a>Uw Aruba ClearPass verbinden met Azure Sentinel

> [!IMPORTANT]
> De Aruba ClearPass-connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

In dit artikel wordt uitgelegd hoe u uw Aruba ClearPass-apparaat verbindt met Azure Sentinel. Met de Aruba ClearPass Data Connector kunt u eenvoudig uw Aruba ClearPass-logboeken verbinden met Azure Sentinel, zodat u de gegevens in werkmappen kunt bekijken, een query voor het maken van aangepaste waarschuwingen en het opnemen ervan om het onderzoek te verbeteren. Integratie tussen Aruba ClearPass en Azure Sentinel maakt gebruik van CEF-indeling syslog, een op Linux gebaseerde logboek doorstuur server en de Log Analytics-agent. Er wordt ook een aangepaste, gegenereerde logboek-parser gebruikt op basis van een Kusto-functie.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor uw Azure-Sentinel-werk ruimte.

- U moet Lees machtigingen hebben voor gedeelde sleutels voor de werk ruimte. [Meer informatie over werkruimte sleutels](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-aruba-clearpass-logs-to-azure-sentinel"></a>Aruba ClearPass-logboeken naar Azure-Sentinel verzenden

Als u de logboeken van Azure Sentinel wilt ontvangen, configureert u uw Aruba ClearPass-apparaat om syslog-berichten te verzenden in CEF-indeling naar een op Linux gebaseerde logboek server voor door sturen (met rsyslog of syslog-aardgas). Op deze server wordt de Log Analytics-agent geïnstalleerd en de agent stuurt de logboeken door naar uw Azure Sentinel-werk ruimte.

1. Selecteer in het navigatie menu van de Azure-Sentinel de optie **Data connectors**.

1. Selecteer in de galerie **gegevens connectors** de optie **Aruba ClearPass (preview)** en **Open vervolgens de pagina connector**.

1. Volg de instructies op het tabblad **instructies** onder **configuratie**:

    1. Onder **1. Configuratie van Linux syslog-agent** : Voer deze stap uit als u nog geen logboek doorstuur server hebt of als u er een hebt. Zie [stap 1: de doorstuur server voor logboeken implementeren](connect-cef-agent.md) in de Azure-Sentinel-documentatie voor meer gedetailleerde instructies en uitleg.

    1. Onder **2. Aruba ClearPass-logboeken door sturen naar een syslog-agent** : Volg de instructies van Aruba voor het [configureren van ClearPass](https://www.arubanetworks.com/techdocs/ClearPass/6.7/PolicyManager/Content/CPPM_UserGuide/Admin/syslogExportFilters_add_syslog_filter_general.htm). Deze configuratie moet de volgende elementen bevatten:
        - Logboek bestemming: de hostnaam en/of het IP-adres van de server voor het door sturen van Logboeken
        - Protocol en poort – TCP 514 (als u dit niet doet, moet u ervoor zorgen dat u de parallelle wijziging aanbrengt in de syslog-daemon op de server voor het door sturen van Logboeken)
        - Logboek indeling – CEF
        - Logboek typen: alle beschik bare of alle geschikte

    1. Minder dan **3. Verbinding valideren** : Controleer de gegevens opname door de opdracht op de pagina connector te kopiëren en op de logboek-doorstuur server uit te voeren. Zie [stap 3: connectiviteit valideren](connect-cef-verify.md) in de informatie over de Azure-Sentinel voor meer gedetailleerde instructies en uitleg.

        Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken**, onder de sectie voor het **Sentinel van Azure** in de tabel *CommonSecurityLog* .

Deze gegevens connector is afhankelijk van een parser op basis van een Kusto-functie die naar verwachting werkt. [Volg deze stappen](https://aka.ms/sentinel-arubaclearpass-parser) om de alias voor de functie **ArubaClearPass** Kusto te maken.

Voer `ArubaClearPass` boven in het query venster de Aruba ClearPass-gegevens in log Analytics in.

Zie het tabblad **volgende stappen** op de pagina connector voor een aantal nuttige query voorbeelden.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Aruba ClearPass verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.