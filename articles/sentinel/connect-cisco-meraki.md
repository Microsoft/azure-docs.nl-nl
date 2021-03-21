---
title: Cisco Meraki-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over hoe u de Cisco Meraki-connector kunt gebruiken om Cisco Meraki-logboeken te verzamelen in azure Sentinel. Bekijk Cisco Meraki-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
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
ms.openlocfilehash: dc2d2a0724f18a02a5b667eb1004963a326ec360
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101745991"
---
# <a name="connect-your-cisco-meraki-to-azure-sentinel"></a>Uw Cisco Meraki koppelen aan de Azure-Sentinel

> [!IMPORTANT]
> De Cisco Meraki-connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

In dit artikel wordt uitgelegd hoe u uw Cisco Meraki (MX/MS/MR)-apparaat (en) verbindt met Azure Sentinel. Met de Cisco Meraki Data Connector kunt u eenvoudig uw Cisco Meraki-logboeken verbinden met Azure Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van onderzoek. Integratie tussen Cisco Meraki en Azure Sentinel maakt gebruik van een syslog-server waarop de Log Analytics-agent is geïnstalleerd. Er wordt ook een aangepaste, gegenereerde logboek-parser gebruikt op basis van een Kusto-functie.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="prerequisites"></a>Vereisten

- U moet lees-en schrijf machtigingen hebben voor de Azure Sentinel-werk ruimte.

- Uw Cisco Meraki-oplossing moet worden geconfigureerd voor het exporteren van Logboeken via syslog.

## <a name="send-cisco-meraki-logs-to-azure-sentinel-via-the-syslog-agent"></a>Cisco Meraki-logboeken verzenden naar Azure Sentinel via de syslog-agent  

Cisco Meraki configureren voor het door sturen van syslog-berichten naar uw Azure Sentinel-werk ruimte via de syslog-agent.

1. Selecteer in het navigatie menu van de Azure-Sentinel de optie **Data connectors**.

1. Selecteer de connector **Cisco Meraki (preview)** in de galerie met **gegevens connectors** en open vervolgens de **pagina connector**.

1. Volg de instructies op de pagina **Cisco Meraki** -connector:

    1. De agent voor Linux installeren en vrijgeven

        - Kies een Azure Linux-VM of een niet-Azure Linux-machine (fysiek of virtueel).

    1. De logboeken configureren die moeten worden verzameld

        - Selecteer de faciliteiten en ernst in de configuratie van de **werkruimte agent**.

    1. De Cisco Meraki-apparaten configureren en verbinden

        - Volg [deze instructies](https://documentation.meraki.com/General_Administration/Monitoring_and_Reporting/Meraki_Device_Reporting_-_Syslog%2C_SNMP_and_API) om de Cisco Meraki-apparaten te configureren voor het door sturen van syslog. Voor de externe server gebruikt u het IP-adres van de Linux-computer waarop u de Linux-agent hebt geïnstalleerd.

## <a name="validate-connectivity-and-find-your-data"></a>Connectiviteit valideren en uw gegevens zoeken

Het kan Maxi maal 20 minuten duren voordat uw logboeken in azure Sentinel worden weer gegeven. 

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken**, onder de sectie *logboek beheer* in de *syslog* -tabel.

Deze gegevens connector is afhankelijk van een parser op basis van een Kusto-functie die naar verwachting werkt. [Volg deze stappen](https://aka.ms/sentinel-ciscomeraki-parser) om de alias voor de functie **CiscoMeraki** Kusto te maken. Vervolgens typt u `CiscoMeraki` in het query venster om een query uit te voeren op de gegevens.

Zie het tabblad **volgende stappen** op de pagina connector voor een aantal nuttige query voorbeelden.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Cisco Meraki verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
