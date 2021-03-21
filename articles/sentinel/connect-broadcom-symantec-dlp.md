---
title: Verbinding maken met Broadcom Symantec-gegevens verlies voor komen (DLP) met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de Broadcom Symantec DLP Data Connector om Symantec DLP-logboeken te halen bij Azure Sentinel. Bekijk de DLP-gegevens van Symantec in werkmappen, maak waarschuwingen en verbeter het onderzoek.
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 7f89780f2ed440898f5a28d78ec541c48a958b90
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700884"
---
# <a name="connect-your-broadcom-symantec-data-loss-prevention-dlp-to-azure-sentinel"></a>Uw Broadcom Symantec-preventie van gegevens verlies (DLP) verbinden met Azure Sentinel

> [!IMPORTANT]
> De Broadcom Symantec DLP connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

In dit artikel wordt uitgelegd hoe u uw Broadcom Symantec DLP-apparaat verbindt met Azure Sentinel. Met de Broadcom Symantec DLP Data Connector kunt u eenvoudig uw Symantec DLP-logboeken verbinden met Azure Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van het onderzoek. Deze functie geeft u meer inzicht in de gegevens van uw organisatie, waar deze zich ook bevindt, en verbetert de mogelijkheden van beveiligings bewerkingen. Integratie tussen Broadcom Symantec DLP en Azure Sentinel maakt gebruik van CEF-indeling syslog, een op Linux gebaseerde logboek doorstuur server en de Log Analytics-agent. Er wordt ook een aangepaste, gegenereerde logboek-parser gebruikt op basis van een Kusto-functie.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor uw Azure-Sentinel-werk ruimte.

- U moet Lees machtigingen hebben voor gedeelde sleutels voor de werk ruimte. [Meer informatie over werkruimte sleutels](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key).

## <a name="send-broadcom-symantec-dlp-logs-to-azure-sentinel"></a>Broadcom Symantec DLP-logboeken verzenden naar Azure Sentinel

Als u de logboeken van Azure Sentinel wilt ontvangen, moet u uw Broadcom Symantec DLP-apparaat configureren voor het verzenden van syslog-berichten in de CEF-indeling naar een op Linux gebaseerde logboek server voor het door sturen van gebeurtenissen (waarop rsyslog of syslog-aardgas wordt uitgevoerd). Op deze server wordt de Log Analytics-agent geïnstalleerd en de agent stuurt de logboeken door naar uw Azure Sentinel-werk ruimte.

1. Selecteer in het navigatie menu van de Azure-Sentinel de optie **Data connectors**.

1. Selecteer in de galerie **Data connectors** de **Broadcom Symantec DLP (preview)** -connector en open vervolgens de **pagina connector**.

1. Volg de instructies op het tabblad **instructies** onder **configuratie**:

    1. Onder **1. Configuratie van Linux syslog-agent** : Voer deze stap uit als u nog geen logboek doorstuur server hebt of als u er een hebt. Zie [stap 1: de doorstuur server voor logboeken implementeren](connect-cef-agent.md) in de Azure-Sentinel-documentatie voor meer gedetailleerde instructies en uitleg.

    1. Onder **2. Symantec DLP-logboeken door sturen naar een syslog-agent** : Volg de instructies van de Broadcom om [Symantec DLP te configureren](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US). Deze configuratie moet de volgende elementen bevatten:
        - Logboek bestemming: de hostnaam en/of het IP-adres van de server voor het door sturen van Logboeken
        - Protocol en poort – TCP 514 (als u dit niet doet, moet u ervoor zorgen dat u de parallelle wijziging aanbrengt in de syslog-daemon op de server voor het door sturen van Logboeken)
        - Logboek indeling – CEF
        - Logboek typen: alle beschik bare of alle geschikte

    1. Minder dan **3. Verbinding valideren** : Controleer de gegevens opname door de opdracht op de pagina connector te kopiëren en op de logboek-doorstuur server uit te voeren. Zie [stap 3: connectiviteit valideren](connect-cef-verify.md) in de informatie over de Azure-Sentinel voor meer gedetailleerde instructies en uitleg.

        Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken**, onder de sectie voor het **Sentinel van Azure** in de tabel *CommonSecurityLog* .

Deze gegevens connector is afhankelijk van een parser op basis van een Kusto-functie die naar verwachting werkt. [Volg deze stappen](https://aka.ms/sentinel-symantecdlp-parser) om de alias voor de functie **SymantecDLP** Kusto te maken.

Voer `SymantecDLP` boven in het query venster de gegevens van Broadcom Symantec dlpgegevens in log Analytics in.

Zie het tabblad **volgende stappen** op de pagina connector voor een aantal nuttige query voorbeelden.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Symantec DLP kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
