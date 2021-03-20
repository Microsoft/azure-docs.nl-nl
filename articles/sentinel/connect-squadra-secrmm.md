---
title: Squadra Technologies secRMM-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Squadra Technologies secRMM-gegevens met Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: a00b4b1e81c0d644cf1475aa46dda3848fda1365
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98632898"
---
# <a name="connect-your-squadra-technologies-secrmm-data-to-azure-sentinel"></a>Verbind uw Squadra Technologies secRMM-gegevens met de Azure-Sentinel 

Met de Squadra Technologies secRMM-connector kunt u uw Squadra Technologies secRMM-logboeken voor beveiligings oplossingen eenvoudig verbinden met Azure Sentinel. U kunt Dash boards weer geven, aangepaste waarschuwingen maken en het onderzoek verbeteren. Met deze connector beschikt u over inzicht in USB-Verwissel bare opslag gebeurtenissen. Integratie tussen Squadra Technologies secRMM en Azure Sentinel maakt gebruik van REST API.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-squadra-technologies-secrmm"></a>Squadra Technologies configureren en verbinden secRMM 

Squadra Technologies secRMM kunnen Logboeken rechtstreeks integreren en exporteren naar Azure Sentinel.
1. Klik in de Azure-Sentinel Portal op Data connectors en selecteer Squadra Technologies secRMM en open vervolgens de pagina connector.

2. Volg de stappen die worden beschreven in de [hand leiding voor de onboarding van Squadra Technologies voor Azure Sentinel](http://www.squadratechnologies.com/StaticContent/ProductDownload/secRMM/9.9.0.0/secRMMAzureSentinelAdministratorGuide.pdf) om Squadra secRMM-gegevens op te halen in azure Sentinel.   

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, worden de gegevens weer gegeven in **Logboeken**, onder de sectie **CustomLogs** in de `secRMM_CL` tabel.

Als u een query wilt uitvoeren voor de logboeken van de Squadra Technologies secRMM, voert u de tabel naam boven in het query venster in.

## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Squadra Technologies secRMM kunt verbinden met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.
