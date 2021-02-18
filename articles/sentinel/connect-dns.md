---
title: DNS-gegevens in azure-Sentinel koppelen | Microsoft Docs
description: Meer informatie over het verbinden van een domeinnaam server (DNS) die wordt uitgevoerd op Windows naar Azure Sentinel door een agent te installeren op de DNS-computer.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: abecddb6f5469cb4ef463e65d6c74149bf34dca9
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590233"
---
# <a name="connect-your-domain-name-server"></a>De domein naam server verbinden

> [!IMPORTANT]
> De DNS-gegevens connector in azure Sentinel is momenteel beschikbaar als open bare preview.
> Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

U kunt elke domein naam server (DNS) die wordt uitgevoerd op Windows verbinden met Azure Sentinel. Dit doet u door een agent op de DNS-computer te installeren. Met behulp van DNS-Logboeken kunt u inzicht krijgen in beveiliging, prestaties en bewerkingen met betrekking tot de DNS-infra structuur van uw organisatie door analyse-en audit logboeken en andere gerelateerde gegevens van de DNS-servers te verzamelen, te analyseren en te correleren.

Wanneer u de DNS-logboek verbinding inschakelt, kunt u het volgende doen:
- Clients identificeren die kwaad aardige domein namen proberen op te lossen
- Verouderde bron records identificeren
- Veelgebruikte domein namen en DNS-clients van dremepelwaarde identificeren
- Aanvraag belasting voor DNS-servers weer geven
- Dynamische DNS-registratie fouten weer geven

## <a name="connected-sources"></a>Verbonden bronnen

De volgende tabel beschrijft de verbonden bronnen die worden ondersteund door deze oplossing:

| **Verbonden bron** | **Ondersteuning** | **Beschrijving** |
| --- | --- | --- |
| [Windows-agents](../azure-monitor/agents/agent-windows.md) | Yes | De oplossing verzamelt DNS-gegevens van Windows-agents. |
| [Linux-agents](../azure-monitor/vm/quick-collect-linux-computer.md) | No | De oplossing verzamelt geen DNS-gegevens van direct Linux-agents. |
| [Beheergroep System Center Operations Manager](../azure-monitor/agents/om-agents.md) | Yes | De oplossing verzamelt DNS-gegevens van agents in een verbonden Operations Manager-beheer groep. Een directe verbinding van de Operations Manager agent naar Azure Monitor is niet vereist. Gegevens worden doorgestuurd van de beheer groep naar de Log Analytics-werk ruimte. |
| [Azure Storage-account](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) | No | Azure Storage wordt niet gebruikt door de oplossing. |

### <a name="data-collection-details"></a>Details van gegevens verzameling

De oplossing verzamelt DNS-inventarisatie-en DNS-gegevens over gebeurtenissen van de DNS-servers waarop een Log Analytics-agent is geïnstalleerd. Inventaris-gerelateerde gegevens, zoals het aantal DNS-servers, zones en bron records, worden verzameld door de DNS Power shell-cmdlets uit te voeren. De gegevens worden elke twee dagen bijgewerkt. De gebeurtenis gegevens worden in realtime verzameld van de [analyse-en audit logboeken](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)#enhanc) die zijn opgenomen in de verbeterde DNS-logboek registratie en diagnoses in Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Uw DNS-apparaat koppelen

1. Selecteer in de Azure Sentinel-Portal **Data connectors** en kies de tegel **DNS (preview)** .
1. Als uw DNS-machines zich in azure bevinden:
    1. Klik op **agent installeren op virtuele machine van Azure Windows**.
    1. Selecteer in de lijst **virtuele machines** de DNS-computer die u wilt streamen naar Azure Sentinel. Zorg ervoor dat dit een Windows-VM is.
    1. Klik in het venster dat wordt geopend voor die VM op **verbinden**.  
    1. Klik op **inschakelen** in het venster **DNS-connector** . 

2. Als uw DNS-computer geen Azure-VM is:
    1. Klik op **agent installeren op niet-Azure-machines**.
    1. Selecteer in het venster **direct agent** de optie **Windows-agent (64 bits) downloaden** of **Windows-agent (32 bits) downloaden**.
    1. Installeer de agent op uw DNS-computer. Kopieer de **werk ruimte-id**, **primaire sleutel** en **secundaire sleutel** en gebruik deze wanneer u hierom wordt gevraagd tijdens de installatie.

3. Als u het relevante schema in Log Analytics voor de DNS-logboeken wilt gebruiken, zoekt u naar **DnsEvents**.

## <a name="validate"></a>Valideren 

Zoek in Log Analytics naar het schema **DnsEvents** en controleer of er gebeurtenissen zijn.

## <a name="troubleshooting"></a>Problemen oplossen

Als opzoek Query's niet worden weer gegeven in azure Sentinel, volgt u deze stappen zodat de query's correct worden weer gegeven:
1. Schakel de [DNS-analyse-Logboeken in op uw servers](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)).
2. Zorg ervoor dat DNSEvents worden weer gegeven in de lijst Log Analytics verzameling.
3. Schakel [Azure DNS Analytics](../azure-monitor/insights/dns-analytics.md)in.
4. Wijzig in Azure DNS Analytics, onder **configuratie**, een van de instellingen, sla het bestand op en wijzig het vervolgens opnieuw als dat nodig is en sla het vervolgens opnieuw op.
5. Controleer Azure DNS Analytics om er zeker van te zijn dat de query's nu worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u DNS on-premises apparaten verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).