---
title: Inleiding tot pakket opname in azure Network Watcher | Microsoft Docs
description: Op deze pagina vindt u een overzicht van de functionaliteit voor het vastleggen van pakketten in Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 1c458508dbf8d98349ec8549af32e3dd48bbd09b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94966425"
---
# <a name="introduction-to-variable-packet-capture-in-azure-network-watcher"></a>Inleiding tot het vastleggen van variabele pakketten in azure Network Watcher

Met Network Watcher-functie voor het vastleggen van pakketten kunt u pakket opname sessies maken om het verkeer van en naar een virtuele machine bij te houden. Met pakket opname kunt u netwerk afwijkingen zowel reactief als proactief vaststellen. Andere gebruiken zijn onder andere het verzamelen van netwerk statistieken, het verkrijgen van informatie over inbreuken op het netwerk, het opsporen van fouten in client-server communicatie en nog veel meer.

Pakket opname is een extensie voor virtuele machines die extern wordt gestart via Network Watcher. Deze mogelijkheid vereenvoudigt de belasting van het hand matig uitvoeren van een pakket opname op de gewenste virtuele machine, waardoor kost bare tijd wordt bespaard. Pakket opname kan worden geactiveerd via de portal, Power shell, CLI of REST API. Een voor beeld van hoe pakket opname kan worden geactiveerd, is met waarschuwingen voor virtuele machines. Er worden filters voor de opname sessie gegeven om ervoor te zorgen dat u verkeer vastlegt dat u wilt bewaken. Filters zijn gebaseerd op 5-tuple (protocol, lokaal IP-adres, extern IP-adres, lokale poort en externe poort). De vastgelegde gegevens worden opgeslagen op de lokale schijf of een opslag-blob. Er geldt een limiet van 10 pakket opname sessies per regio per abonnement. Deze limiet geldt alleen voor de sessies en is niet van toepassing op de opgeslagen pakket opname bestanden lokaal op de virtuele machine of in een opslag account.

> [!IMPORTANT]
> Voor pakket opname is een extensie van een virtuele machine vereist `AzureNetworkWatcherExtension` . Voor het installeren van de uitbrei ding op een Windows-VM gaat u naar [azure Network Watcher agent-extensie voor virtuele machines voor Windows](../virtual-machines/extensions/network-watcher-windows.md) en voor Linux VM gaat u naar de [Azure Network Watcher agent-extensie voor virtuele machines voor Linux](../virtual-machines/extensions/network-watcher-linux.md).

De volgende opties zijn beschikbaar voor een sessie voor het vastleggen van pakketten, zodat u de informatie die u alleen naar de gewenste informatie vastlegt, kunt verminderen:

**Configuratie vastleggen**

|Eigenschap|Beschrijving|
|---|---|
|**Maximum aantal bytes per pakket (bytes)** | Het aantal bytes van elk pakket dat wordt vastgelegd, alle bytes worden vastgelegd als dit veld leeg blijft. Het aantal bytes van elk pakket dat wordt vastgelegd, alle bytes worden vastgelegd als dit veld leeg blijft. Als u alleen de IPv4-header nodig hebt, geeft u 34 hier op |
|**Maximum aantal bytes per sessie (bytes)** | Het totale aantal bytes in dat wordt vastgelegd, zodra de waarde is bereikt, wordt de sessie beëindigd.|
|**Tijds limiet (seconden)** | Hiermee stelt u een tijds beperking in voor de sessie voor het vastleggen van pakketten. De standaard waarde is 18000 seconden of 5 uur.|

**Filteren (optioneel)**

|Eigenschap|Beschrijving|
|---|---|
|**Protocol** | Het protocol dat moet worden gefilterd voor de pakket opname. De beschik bare waarden zijn TCP, UDP en alle.|
|**Lokaal IP-adres** | Met deze waarde wordt de pakket opname gefilterd op pakketten waarbij het lokale IP-adres overeenkomt met deze filter waarde.|
|**Lokale poort** | Met deze waarde wordt de pakket opname gefilterd op pakketten waarbij de lokale poort overeenkomt met deze filter waarde.|
|**Extern IP-adres** | Met deze waarde wordt de pakket opname gefilterd op pakketten waarbij het externe IP-adres overeenkomt met deze filter waarde.|
|**Externe poort** | Met deze waarde wordt de pakket opname gefilterd op pakketten waarbij de externe poort overeenkomt met deze filter waarde.|

### <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u pakket opnames kunt beheren via de portal door op pakket opname beheren te gaan [in de Azure Portal](network-watcher-packet-capture-manage-portal.md) of met Power shell door te bezoeken [pakket vastleggen met Power shell](network-watcher-packet-capture-manage-powershell.md).

Meer informatie over het maken van proactieve pakket opnames op basis van waarschuwingen voor virtuele machines. Ga naar [een waarschuwing maken geactiveerd pakket vastleggen](network-watcher-alert-triggered-packet-capture.md)

<!--Image references-->
[1]: ./media/network-watcher-packet-capture-overview/figure1.png