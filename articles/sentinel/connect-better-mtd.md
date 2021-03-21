---
title: Verbind betere Mobile Threat verdediging (MTD) met Azure Sentinel | Microsoft Docs
description: Informatie over het gebruik van de betere MTD-gegevens connector (Mobile Threat verdediging) voor het ophalen van MTD-Logboeken in azure Sentinel. Bekijk MTD-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
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
ms.openlocfilehash: 4828e31b9d15f101740c158ee62c90c95673c9a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98541544"
---
# <a name="connect-your-better-mobile-threat-defense-mtd-to-azure-sentinel"></a>Verbind uw betere Mobile Threat verdediging (MTD) met Azure Sentinel

> [!IMPORTANT]
> De verbeterde MTD-connector (Mobile Threat verdediging) is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

Dankzij de verbeterde MTD-connector (Mobile Threat verdediging) kunt u eenvoudig al uw betere MTD-beveiligings oplossingen met uw Azure-Sentinel aansluiten om Dash boards weer te geven, aangepaste waarschuwingen te maken en het onderzoek te verbeteren. Integratie tussen betere beveiliging tegen mobiele bedreigingen en Azure-Sentinel maakt gebruik van REST API.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-better-mobile-threat-defense"></a>BETERE beveiliging tegen mobiele bedreigingen configureren en verbinden

BETERE MTD kan Logboeken rechtstreeks integreren en exporteren naar Azure Sentinel.

1. Selecteer in het navigatie menu van de Azure-Sentinel de optie **Data connectors**.

1. Selecteer in de galerie met **gegevens connectors** **betere Mobile Threat verdediging (MTD) (preview)** en **Open vervolgens de pagina connector**.

1. Volg de stappen op de pagina connector en op [Deze pagina van de betere MTD-documentatie](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration#mtd-integration-configuration) om de integratie van de betere MTD-console te volt ooien.

    Wanneer u wordt gevraagd om de **werk ruimte-id** en **primaire sleutel** waarden in te voeren, kopieert u deze van de Azure Sentinel connector-pagina en plakt u deze in de betere MTD-configuratie.

    :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{Werk ruimte-ID en primaire sleutel}":::

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken**, onder de sectie **CustomLogs** , in een of meer van de volgende tabellen:
- `BetterMTDDeviceLog_CL`
- `BetterMTDIncidentLog_CL`
- `BetterMTDAppLog_CL`
- `BetterMTDNetflowLog_CL`

Als u een query wilt uitvoeren voor de betere MTD-Logboeken in Analytics-regels, query's voor de jacht of ergens anders in azure Sentinel, voert u een van de bovenstaande tabel namen in boven aan het query venster.

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u betere Mobile Threat verdediging (MTD) kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
