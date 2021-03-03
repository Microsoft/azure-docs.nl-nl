---
title: Verbinding maken met NXLog Windows DNS logboek gegevens naar Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de NXLog DNS logs Data Connector om Windows DNS-gebeurtenissen op te halen in azure Sentinel. Bekijk Windows DNS-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
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
ms.openlocfilehash: 880aad438d98605d11e5a2a7c314d89bd8beb5c5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101745986"
---
# <a name="connect-your-nxlog-windows-dns-logs-to-azure-sentinel"></a>Uw NXLog Windows DNS-logboeken verbinden met Azure Sentinel

> [!IMPORTANT]
> De NXLog DNS logs connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

Met de [NXLOG DNS logs](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html) connector kunt u eenvoudig al uw Windows DNS server-gebeurtenissen exporteren naar Azure Sentinel in realtime, waardoor u inzicht hebt in de activiteiten van de domein naam server in uw hele organisatie. Er wordt gebruikgemaakt van high performance [Event Tracing for Windows (etw)](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html#dns_windows_etw) voor het verzamelen van zowel controle-als analyse-DNS-server gebeurtenissen in realtime en het ondersteunen van gebeurtenis verrijking met aangepaste velden. Integratie tussen NXLog DNS-logboeken en Azure Sentinel wordt vereenvoudigd via REST API.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-nxlog-dns-logs"></a>NXLog DNS-logboeken configureren en verbinden

NXLog kan worden geconfigureerd voor het rechtstreeks verzenden van gebeurtenissen in JSON-indeling naar Azure Sentinel.

1. Klik in de Azure-Sentinel-Portal op **Data connectors** en selecteer **NXLog DNS logs** connector.

1. Selecteer de **pagina connector openen**.

1. Volg de stapsgewijze instructies in het NXLog-onderhouds onderwerp van de *Gebruikers handleiding* [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) voor het configureren van door sturen via rest API.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken** onder de sectie  **aangepaste logboeken** in de tabel *DNS_Logs_CL* .

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u NXLog kunt gebruiken om Windows DNS-logboeken op te nemen in azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
