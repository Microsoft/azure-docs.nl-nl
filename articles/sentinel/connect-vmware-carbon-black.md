---
title: Standaard gegevens van VMware Carbon Black Cloud endpoint verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van de standaard gegevens van VMware Carbon Black Cloud endpoint met Azure Sentinel.
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
ms.openlocfilehash: e13b251c9bd95a5b52d63d8ea1bbf265c9c46fd8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023871"
---
# <a name="connect-your-vmware-carbon-black-cloud-endpoint-standard-to-azure-sentinel-with-azure-function"></a>Uw VMware Carbon Black-Cloud eindpunt standaard koppelen aan Azure Sentinel met Azure function

> [!IMPORTANT]
> De standaard gegevens connector voor het VMware-eind punt voor de zwarte Cloud endpoint in Azure is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met de standaard connector voor het maken van een zwart-Cloud eindpunt met VMware-eind punten kunt u eenvoudig al uw standaard logboeken voor de beveiliging van uw [VMware](https://www.carbonblack.com/products/endpoint-standard/) -basis met Azure-Sentinel aansluiten om Dash boards weer te geven, aangepaste waarschuwingen te maken en het onderzoek te verbeteren. Integratie tussen VMware Carbon Black en Azure Sentinel maakt gebruik van Azure Functions om logboek gegevens te verzamelen met behulp van REST API.


> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-vmware-carbon-black-cloud-endpoint-standard"></a>VMware Carbon Black Cloud endpoint Standard configureren en verbinden

Azure Functions kunnen gebeurtenissen en logboeken rechtstreeks integreren en ophalen uit de zwarte Cloud endpoint-standaard van VMware carbon en deze door sturen naar Azure Sentinel.

1. Klik in de Azure-Sentinel Portal op **Data connectors** en selecteer **VMware Carbon Black** connector.
2. Selecteer de **pagina connector openen**.
3. Volg de instructies op de pagina **VMware-zwarte** .


## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in Log Analytics onder de tabellen **CarbonBlackAuditLogs_CL**, **CarbonBlackNotifications_CL** en * * * * CarbonBlackEvents_CL * * * * *.

## <a name="validate-connectivity"></a>Connectiviteit valideren
Het kan 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 


## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u met Azure function-apps verbinding kunt maken met de Black-Cloud eindpunt standaard van VMware. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.

