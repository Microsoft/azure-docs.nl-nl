---
title: NXLog LinuxAudit-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het gebruik van de NXLog LinuxAudit Data Connector om LinuxAudit-logboeken te verzamelen in azure Sentinel. Bekijk LinuxAudit-gegevens in werkmappen, maak waarschuwingen en verbeter het onderzoek.
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
ms.openlocfilehash: 2010b21a3cdb81f2b2aa4180f87857862cd02bf5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101700876"
---
# <a name="connect-your-nxlog-linuxaudit-to-azure-sentinel"></a>Uw NXLog LinuxAudit verbinden met Azure Sentinel

> [!IMPORTANT]
> De NXLog LinuxAudit-connector is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

Met de [NXLog LinuxAudit](https://nxlog.co/documentation/nxlog-user-guide/im_linuxaudit.html) -connector kunt u eenvoudig Linux-beveiligings gebeurtenissen exporteren naar Azure Sentinel in realtime, zodat u inzicht krijgt in de activiteiten van de domein naam server in uw hele organisatie. De NXLog LinuxAudit-module ondersteunt aangepaste controle regels en verzamelt logboeken zonder controle of andere gebruikers ruimte-software. IP-adressen en groeps-en gebruikers-Id's worden omgezet naar hun eigen namen, waardoor [Linux-audit](https://nxlog.co/documentation/nxlog-user-guide/linux-audit.html) logboeken betrouwbaarder worden voor beveiligings analisten. Integratie tussen NXLog LinuxAudit en Azure Sentinel wordt vereenvoudigd via REST API.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-nxlog-linuxaudit"></a>NXLog LinuxAudit configureren en verbinden

NXLog kan worden geconfigureerd voor het rechtstreeks verzenden van gebeurtenissen in JSON-indeling naar Azure Sentinel.

1. Klik in de Azure-Sentinel-Portal op **Data connectors** en selecteer **NXLog LinuxAudit** -connector.

1. Selecteer de **pagina connector openen**.

1. Volg de stapsgewijze instructies in het NXLog-onderhouds onderwerp van de *Gebruikers handleiding* [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html) voor het configureren van door sturen via rest API.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken** onder de sectie  **aangepaste logboeken** in de tabel *LinuxAudit_CL* .

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u NXLog LinuxAudit kunt gebruiken om Linux-beveiligings gebeurtenissen op te nemen in azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over hoe u [inzicht krijgt in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
