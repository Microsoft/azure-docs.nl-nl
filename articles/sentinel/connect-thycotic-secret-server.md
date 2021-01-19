---
title: Thycotic Secret Server verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de Thycotic Secret Server Data Connector om Thycotic Secret Server-logboeken te verzamelen in azure Sentinel. Bekijk Thycotic Secret Server-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
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
ms.date: 12/13/2020
ms.author: yelevin
ms.openlocfilehash: a0056391dddec25511deb7033d37f59db3d835c8
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567893"
---
# <a name="connect-your-thycotic-secret-server-to-azure-sentinel"></a>Uw Thycotic-geheim server verbinden met Azure Sentinel

> [!IMPORTANT]
> De Thycotic Secret Server-connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

In dit artikel wordt uitgelegd hoe u uw Thycotic Secret Server-apparaat verbindt met Azure Sentinel. Met de Thycotic Secret Server Data Connector kunt u eenvoudig uw Thycotic Secret-Server logboeken verbinden met Azure Sentinel, zodat u de gegevens in werkmappen kunt bekijken, er aangepaste waarschuwingen voor moet maken en deze moet opnemen om het onderzoek te verbeteren. Integratie tussen Thycotic en Azure Sentinel maakt gebruik van de CEF-gegevens connector voor het correct parseren en weer geven van berichten van een geheime server.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor uw Azure-Sentinel-werk ruimte.

- U moet Lees machtigingen hebben voor gedeelde sleutels voor de werk ruimte.

- Uw Thycotic-geheim server moet worden geconfigureerd voor het exporteren van Logboeken via syslog.

## <a name="send-thycotic-secret-server-logs-to-azure-sentinel"></a>Thycotic Secret-Server logboeken naar Azure Sentinel verzenden

Als u de logboeken van Azure Sentinel wilt ontvangen, configureert u uw Thycotic Secret-Server om syslog-berichten te verzenden in de CEF-indeling naar uw op Linux gebaseerde logboek server voor door sturen (met rsyslog of syslog-aardgas). Op deze server wordt de Log Analytics-agent geïnstalleerd en de agent stuurt de logboeken door naar uw Azure Sentinel-werk ruimte.

1. Selecteer in het navigatie menu van de Azure-Sentinel de optie **Data connectors**.

1. Selecteer in de galerie **gegevens connectors** de optie **Thycotic Secret Server (preview)** en **Open vervolgens de pagina connector**.

1. Volg de instructies op het tabblad **instructies** onder **configuratie**:

    1. Onder **1. Configuratie van Linux syslog-agent** : Voer deze stap uit als u nog geen logboek doorstuur server hebt of als u er een hebt. Zie [stap 1: de doorstuur server voor logboeken implementeren](connect-cef-agent.md) in de Azure-Sentinel-documentatie voor meer gedetailleerde instructies en uitleg.

    1. Onder **2. CEF-Logboeken (common Event Format) door sturen naar syslog-agent** -Volg de instructies van Thycotic om de [geheime server te configureren](https://thy.center/ss/link/syslog). Deze configuratie moet de volgende elementen bevatten:
        - Logboek bestemming: de hostnaam en/of het IP-adres van de server voor het door sturen van Logboeken
        - Protocol en poort – **TCP 514** (als u dit niet doet, moet u ervoor zorgen dat u de parallelle wijziging aanbrengt in de syslog-daemon op de server voor het door sturen van Logboeken)
        - Logboek indeling – CEF
        - Logboek typen: alle beschikbaar

    1. Minder dan **3. Verbinding valideren** : Controleer de gegevens opname door de opdracht op de pagina connector te kopiëren en op de logboek-doorstuur server uit te voeren. Zie [stap 3: connectiviteit valideren](connect-cef-verify.md) in de informatie over de Azure-Sentinel voor meer gedetailleerde instructies en uitleg.

        Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken**, onder de sectie voor het **Sentinel van Azure** in de tabel *CommonSecurityLog* .

Als u een query wilt uitvoeren op Thycotic Secret Server-gegevens in Log Analytics, kopieert u het volgende in het query venster en past u andere filters toe wanneer u kiest:

```kusto
CommonSecurityLog 
| where DeviceVendor == "Thycotic Software"
```

Zie het tabblad **volgende stappen** op de pagina connector voor een aantal nuttige werkmappen en query voorbeelden.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Thycotic Secret Server verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.