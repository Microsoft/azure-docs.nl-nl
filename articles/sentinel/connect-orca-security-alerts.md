---
title: Beveiligings waarschuwingen van Orca verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van de Orca-beveiligings waarschuwings gegevens met Azure Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van het onderzoek.
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 18a50e3d06135f2c17b7e7c5b2969be33f408c62
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100093077"
---
# <a name="connect-your-orca-security-alerts-to-azure-sentinel"></a>Uw Orca-beveiligings waarschuwingen verbinden met Azure Sentinel 

> [!IMPORTANT]
> De Orca Security Alerts-connector in azure Sentinel is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met de Orca Security Alerts-connector kunt u eenvoudig waarschuwingen voor beveiligings oplossingen van uw [Orca](https://orca.security/) naar Azure-Sentinel brengen, zodat u ze in werkmappen kunt bekijken, ze met behulp van een aangepaste waarschuwing en deze op te nemen om het onderzoek te verbeteren. Integratie tussen Orca-beveiligings waarschuwingen en Azure-Sentinel maakt gebruik van REST API.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-orca-security-alerts"></a>Beveiligings waarschuwingen van Orca configureren en verbinden

Orca-beveiligings waarschuwingen kunnen Logboeken rechtstreeks integreren en exporteren naar Azure Sentinel.

1. Klik in de Azure-Sentinel Portal op **Data connectors** en selecteer **Orca Security Alerts** en **Open vervolgens connector-pagina**.

2. Raadpleeg https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integration voor het volt ooien van de integratie van het Orca-platform.

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in Log Analytics onder **CustomLogs** in de tabel **OrcaAlerts_CL** .
Als u het relevante schema in Log Analytics voor de Orca-waarschuwingen wilt gebruiken, zoekt u naar `OrcaAlerts_CL` .

## <a name="validate-connectivity"></a>Connectiviteit valideren
Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u de beveiligings waarschuwingen van Orca kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.

