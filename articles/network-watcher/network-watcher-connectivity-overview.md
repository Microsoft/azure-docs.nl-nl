---
title: Inleiding tot Azure Network Watcher verbinding oplossen | Microsoft Docs
description: Op deze pagina vindt u een overzicht van de mogelijkheden voor het oplossen van problemen met de Network Watcher-verbinding
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: damendo
ms.openlocfilehash: 047da0224cdbdb534ad9080faee4fd48da3840c5
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492451"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Inleiding tot verbindings problemen in azure Network Watcher

De functie verbinding oplossen van Network Watcher biedt de mogelijkheid om een directe TCP-verbinding van een virtuele machine naar een virtuele machine (VM), Fully Qualified Domain Name (FQDN), URI of IPv4-adres te controleren. Netwerk scenario's zijn complex, ze worden geïmplementeerd met behulp van netwerk beveiligings groepen, firewalls, door de gebruiker gedefinieerde routes en resources die worden geboden door Azure. Complexe configuraties maken probleem oplossing van connectiviteits problemen lastig. Network Watcher helpt de hoeveelheid tijd te beperken om verbindings problemen op te sporen en op te sporen. De geretourneerde resultaten kunnen inzicht geven in of een connectiviteits probleem wordt veroorzaakt door een platform of een probleem met de gebruikers configuratie. Connectiviteit kan worden gecontroleerd met [Power shell](network-watcher-connectivity-powershell.md), [Azure cli](network-watcher-connectivity-cli.md)en [rest API](network-watcher-connectivity-rest.md).

> [!IMPORTANT]
> Verbindings problemen oplossen vereist dat de VM-extensie is geïnstalleerd op de VM die u wilt oplossen `AzureNetworkWatcherExtension` . Voor het installeren van de uitbrei ding op een Windows-VM gaat u naar [azure Network Watcher agent-extensie voor virtuele machines voor Windows](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) en voor Linux VM gaat u naar de [Azure Network Watcher agent-extensie voor virtuele machines voor Linux](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json). De uitbrei ding is niet vereist voor het eind punt van de bestemming.

## <a name="response"></a>Antwoord

In de volgende tabel ziet u de eigenschappen die worden geretourneerd wanneer het oplossen van verbindings problemen is voltooid.

|Eigenschap  |Beschrijving  |
|---------|---------|
|Connection Status     | De status van de connectiviteits controle. Mogelijke resultaten zijn **bereikbaar** en **onbereikbaar**.        |
|AvgLatencyInMs     | Gemiddelde latentie tijdens de connectiviteits controle in milliseconden. (Wordt alleen weer gegeven als de status controleren bereikbaar is)        |
|MinLatencyInMs     | Minimale latentie tijdens de connectiviteits controle in milliseconden. (Wordt alleen weer gegeven als de status controleren bereikbaar is)        |
|MaxLatencyInMs     | Maximale latentie tijdens de connectiviteits controle in milliseconden. (Wordt alleen weer gegeven als de status controleren bereikbaar is)        |
|ProbesSent     | Het aantal tests dat is verzonden tijdens de controle. De maximale waarde is 100.        |
|ProbesFailed     | Het aantal tests dat tijdens de controle is mislukt. De maximale waarde is 100.        |
|Hops     | Hop per hop-pad van bron naar doel.        |
|Hops []. Voert     | Type resource. Mogelijke waarden zijn **Source**, **VirtualAppliance**, **VnetLocal** en **Internet**.        |
|Hops []. Id | De unieke id van de hop.|
|Hops []. Help | Het IP-adres van de hop.|
|Hops []. ResourceId | ResourceID van de hop als de hop een Azure-resource is. Als het een Internet bron is, is ResourceID **Internet**. |
|Hops []. NextHopIds | De unieke id van de volgende hop die is gemaakt.|
|Hops []. Taken | Een verzameling van problemen die zijn opgetreden tijdens de controle bij die hop. Als er geen problemen zijn, is de waarde leeg.|
|Hops []. Problemen []. Oorsprong | Bij de huidige hop, waar het probleem zich voordeed. Mogelijke waarden zijn:<br/> **Inkomend** -probleem bevindt zich op de koppeling van de vorige hop naar de huidige hop<br/>**Uitgaand** -probleem bevindt zich op de koppeling van de huidige hop naar de volgende hop<br/>**Lokaal** -probleem bevindt zich op de huidige hop.|
|Hops []. Problemen []. Ernst | De ernst van het probleem is gedetecteerd. Mogelijke waarden zijn **fout** en **waarschuwing**. |
|Hops []. Problemen []. Voert |Het type probleem dat is gevonden. Mogelijke waarden zijn: <br/>**CPU**<br/>**Geheugen**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Hops []. Problemen []. Context |Details over het gevonden probleem.|
|Hops []. Problemen []. Context []. key |De sleutel van het paar sleutel waarden dat is geretourneerd.|
|Hops []. Problemen []. Context []. waarde |De waarde van het sleutel waarde-paar dat wordt geretourneerd.|

Hier volgt een voor beeld van een probleem dat op een hop is gevonden.

```json
"Issues": [
    {
        "Origin": "Outbound",
        "Severity": "Error",
        "Type": "NetworkSecurityRule",
        "Context": [
            {
                "key": "RuleName",
                "value": "UserRule_Port80"
            }
        ]
    }
]
```
## <a name="fault-types"></a>Fout typen

Verbindings problemen oplossen retourneert fout typen over de verbinding. De volgende tabel bevat een lijst met de huidige gegenereerde fout typen.

|Type  |Beschrijving  |
|---------|---------|
|CPU     | Hoog CPU-gebruik.       |
|Geheugen     | Hoog geheugen gebruik.       |
|GuestFirewall     | Verkeer wordt geblokkeerd vanwege een firewall configuratie van een virtuele machine.        |
|DNSResolution     | De DNS-omzetting voor het doel adres is mislukt.        |
|NetworkSecurityRule    | Verkeer wordt geblokkeerd door een NSG-regel (regel wordt geretourneerd)        |
|UserDefinedRoute|Verkeer wordt verwijderd als gevolg van een door de gebruiker gedefinieerde of systeem route. |

### <a name="next-steps"></a>Volgende stappen

Meer informatie over het oplossen van verbindingen met behulp van de [Azure Portal](network-watcher-connectivity-portal.md), [Power shell](network-watcher-connectivity-powershell.md), de [Azure cli](network-watcher-connectivity-cli.md)of [rest API](network-watcher-connectivity-rest.md).