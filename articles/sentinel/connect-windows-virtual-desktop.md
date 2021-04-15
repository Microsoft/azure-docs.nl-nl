---
title: Verbinding Windows Virtual Desktop met Azure Sentinel | Microsoft Docs
description: Leer hoe u uw Windows Virtual Desktop kunt verbinden met Azure Sentinel.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2021
ms.author: bagol
ms.openlocfilehash: a835ea7b5e79ecc9b2d26dc6955984d0d0ff2906
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107380291"
---
# <a name="connect-windows-virtual-desktop-data-to-azure-sentinel"></a>Verbinding Windows Virtual Desktop gegevens maken met Azure Sentinel

In dit artikel wordt beschreven hoe u uw Windows Virtual Desktop (WVD)-omgevingen kunt bewaken met behulp van Azure Sentinel.

Als u bijvoorbeeld uw Windows Virtual Desktop kunt u meer extern werk bieden met behulp van gevirtualiseerde bureaubladen, terwijl de beveiligingsstatus van uw organisatie behouden blijft.

## <a name="windows-virtual-desktop-data-in-azure-sentinel"></a>Windows Virtual Desktop data in Azure Sentinel

Windows Virtual Desktop gegevens in Azure Sentinel omvat de volgende typen:


|Gegevens  |Description  |
|---------|---------|
|**Windows-gebeurtenislogboeken**     |  Windows-gebeurtenislogboeken van de WVD-omgeving worden op dezelfde manier naar een Log Analytics-werkruimte met Azure Sentinel-ondersteuning gestreamd als Windows-gebeurtenislogboeken van andere Windows-computers, buiten de WVD-omgeving. <br><br>Installeer de Log Analytics-agent op uw Windows-computer en configureer de Windows-gebeurtenislogboeken die naar de Log Analytics-werkruimte moeten worden verzonden.<br><br>Zie voor meer informatie:<br>- [Log Analytics-agent installeren op Windows-computers](/azure/azure-monitor/agents/agent-windows)<br>- [Gegevensbronnen voor Windows-gebeurtenislogboek verzamelen met Log Analytics-agent](/azure/azure-monitor/agents/data-sources-windows-events)<br>- [Verbinding maken met Windows-beveiligingsgebeurtenissen](connect-windows-security-events.md)       |
|**MDE-waarschuwingen (Microsoft Defender for Endpoint)**     |  Als u MDE wilt configureren Windows Virtual Desktop, gebruikt u dezelfde procedure als voor elk ander Windows-eindpunt. <br><br>Zie voor meer informatie: <br>- [Implementatie van Microsoft Defender voor eindpunten instellen](/windows/security/threat-protection/microsoft-defender-atp/production-deployment)<br>- [Gegevens van Microsoft 365 Defender verbinden met Azure Sentinel](connect-microsoft-365-defender.md)       |
|**Windows Virtual Desktop diagnostische gegevens**     | Windows Virtual Desktop diagnostics is een functie van de Windows Virtual Desktop PaaS-service, die informatie registreert wanneer iemand die is toegewezen aan Windows Virtual Desktop de service gebruikt. <br><br>Elk logboek bevat informatie over welke Windows Virtual Desktop betrokken was bij de activiteit, eventuele foutberichten die worden weergegeven tijdens de sessie, tenantgegevens en gebruikersgegevens. <br><br>De diagnostische functie maakt activiteitenlogboeken voor gebruikers- en beheeracties. <br><br>Zie Log Analytics gebruiken voor [de diagnostische functie in](/azure/virtual-desktop/virtual-desktop-fall-2019/diagnostics-log-analytics-2019)Windows Virtual Desktop voor meer Windows Virtual Desktop.        |
|     |         |

## <a name="connect-windows-virtual-desktop-data"></a>Verbinding maken Windows Virtual Desktop gegevens

Als u gegevens wilt opnemen Windows Virtual Desktop in Azure Sentinel, gebruikt u de instructies in de Windows Virtual Desktop documentatie.

Zie Gegevens pushen naar [Windows Virtual Desktop Log Analytics-werkruimte voor meer informatie.](/azure/virtual-desktop/diagnostics-log-analytics)

## <a name="find-your-data"></a>Uw gegevens zoeken

Nadat de verbinding tot stand is gebracht, kunt u query's uitvoeren in Azure Sentinel log analytics-gegevens.

Zie bijvoorbeeld voorbeeldquery's uit de [Windows Virtual Desktop documentatie](/azure/virtual-desktop/diagnostics-log-analytics).


Azure Sentinel biedt ook ingebouwde query's in het **gebied Algemene** logboeken WINDOWS  >    >  **VIRTUAL DESKTOP:**

[![Windows Virtual Desktop ingebouwde query's in Azure Sentinel. ](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png)](media/connect-windows-virtual-desktop/windows-virtual-desktop-queries.png#lightbox)

## <a name="next-steps"></a>Volgende stappen


Zie voor meer informatie de [Azure Monitor voor Windows Virtual Desktop woordenlijst](/azure/virtual-desktop/azure-monitor-glossary).
