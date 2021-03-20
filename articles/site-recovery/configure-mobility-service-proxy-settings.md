---
title: Proxy-instellingen voor de Mobility-service configureren voor Azure voor herstel na nood gevallen | Microsoft Docs
description: Bevat informatie over het configureren van de Mobility-service wanneer klanten een proxy in hun bron omgeving gebruiken.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/18/2020
ms.author: sideeksh
ms.openlocfilehash: 429ffcab147142ae2e96de13b7c9e1e5ee1ac7ba
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86133219"
---
# <a name="configure-mobility-service-proxy-settings-for-azure-to-azure-disaster-recovery"></a>Proxy-instellingen voor de Mobility-service configureren voor Azure voor herstel na nood gevallen

Dit artikel bevat richt lijnen voor het aanpassen van netwerk configuraties op de doel-VM van Azure, wanneer u virtuele Azure-machines van de ene naar de andere regio repliceert en herstelt met behulp van [Azure site Recovery](site-recovery-overview.md).

Het doel van dit document is de stappen te geven voor het configureren van proxy-instellingen voor Azure Site Recovery Mobility-service in azure tot Azure nood herstel scenario. 

Proxy's zijn netwerk gateways die netwerk verbindingen met eind punten toestaan of weigeren. Doorgaans is een proxy een computer die zich buiten de client computer bevindt die probeert toegang te krijgen tot netwerk eindpunten. Met een bypass-lijst kan de client verbinding maken met de eind punten zonder de proxy te passeren. Een gebruikers naam en wacht woord kunnen eventueel worden ingesteld voor een proxy door netwerk beheerders, zodat alleen geverifieerde clients proxy kunnen gebruiken. 

## <a name="before-you-start"></a>Voordat u begint

Meer informatie over hoe Site Recovery herstel na nood gevallen biedt voor [dit scenario](azure-to-azure-architecture.md).
Meer informatie over de [netwerk richtlijnen](azure-to-azure-about-networking.md) wanneer u virtuele machines van Azure repliceert en herstelt vanuit de ene regio naar een andere, met behulp van [Azure site Recovery](site-recovery-overview.md).
Zorg ervoor dat uw proxy op de juiste wijze is ingesteld op basis van de behoeften van uw organisatie.

## <a name="configure-the-mobility-service"></a>De Mobility-service configureren

Mobility service ondersteunt alleen niet-geverifieerde proxy's. Het biedt twee manieren om proxy gegevens in te voeren voor communicatie met Site Recovery-eind punten. 

### <a name="method-1-auto-detection"></a>Methode 1: automatische detectie

Mobility service detecteert automatisch de proxy instellingen van omgevings instellingen of Internet Explorer-instellingen (alleen Windows) tijdens het inschakelen van de replicatie. 

- Windows-besturings systeem: tijdens het inschakelen van replicatie, detecteert Mobility service de proxy-instellingen zoals geconfigureerd in Internet Explorer voor lokale systeem gebruiker. Voor het instellen van een proxy voor een lokaal systeem account, kan een beheerder PsExec gebruiken om een opdracht prompt en vervolgens Internet Explorer te starten. 
- Windows-besturings systeem: proxy-instellingen zijn geconfigureerd als omgevings variabelen http_proxy en no_proxy. 
- Linux-besturings systeem: proxy-instellingen worden geconfigureerd in/etc/profile of/etc/Environment als omgevings variabelen http_proxy, no_proxy. 
- De automatisch gedetecteerde proxy-instellingen worden opgeslagen in het configuratie bestand ProxyInfo. conf van de Mobility-Service proxy 
- Standaard locatie van ProxyInfo. conf 
    - Windows: C:\ProgramData\Microsoft Azure site Recovery\Config\ProxyInfo.conf 
    - Linux:/usr/local/InMage/config/ProxyInfo.conf


### <a name="method-2-provide-custom-application-proxy-settings"></a>Methode 2: instellingen voor aangepaste toepassings proxy opgeven

In dit geval biedt de klant aangepaste instellingen voor toepassings proxy in het configuratie bestand ProxyInfo. conf van de Mobility-service. Met deze methode kunnen klanten alleen proxy bieden voor de Mobility-service of een andere proxy voor Azure Site Recovery Mobility-service dan een proxy (of geen proxy) voor de rest van de toepassingen op de computer.

## <a name="proxy-template"></a>Proxy sjabloon
ProxyInfo. conf bevat de volgende sjabloon [proxy] address = http://1.2.3.4 Port = 5678 BypassList = hypervrecoverymanager. windowsazure. com, login. microsoftonline. com, blob. core. Windows. net. De BypassList biedt geen ondersteuning voor joker tekens zoals *. windows.net, maar het geven van windows.net is goed genoeg om over te slaan. 

## <a name="next-steps"></a>Volgende stappen:
- Lees de [netwerk richtlijnen](./azure-to-azure-about-networking.md)  voor het repliceren van virtuele Azure-machines.
- Implementeer herstel na nood gevallen door [Azure vm's te repliceren](./azure-to-azure-quickstart.md).
