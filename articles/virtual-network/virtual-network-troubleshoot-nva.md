---
title: Problemen met het virtuele netwerk apparaat oplossen in azure | Microsoft Docs
description: Los problemen met het netwerk virtuele apparaat (NVA) op in Azure en valideer basis vereisten voor het Azure-platform voor NVA-configuraties.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2018
ms.author: genli
ms.openlocfilehash: fe4c17b74cd786d03bd19257dea190a21ecaa9f5
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99095638"
---
# <a name="network-virtual-appliance-issues-in-azure"></a>Problemen met het virtuele netwerk apparaat in azure

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt problemen met de virtuele machine of VPN-verbinding en-fouten ondervinden bij het gebruik van een virtueel netwerk apparaat van derden (NVA) in Microsoft Azure. Dit artikel bevat de basis stappen om u te helpen bij het valideren van basis vereisten voor Azure-platforms voor NVA-configuraties.

Technische ondersteuning voor Nva's van derden en hun integratie met het Azure-platform wordt geboden door de leverancier van NVA.

> [!NOTE]
> Als u een connectiviteits-of routerings probleem hebt met een NVA, [neemt u rechtstreeks contact op met de leverancier van de NVA](https://mskb.pkisolutions.com/kb/2984655) .

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="checklist-for-troubleshooting-with-nva-vendor"></a>Controle lijst voor probleem oplossing met NVA-leverancier

- Software-updates voor NVA VM-software
- Installatie en functionaliteit van het service account
- Door de gebruiker gedefinieerde routes (Udr's) op subnetten van het virtuele netwerk die verkeer naar NVA sturen
- Udr's in subnetten van het virtuele netwerk die verkeer van NVA omleiden
- Routerings tabellen en-regels binnen de NVA (bijvoorbeeld van NIC1 naar NIC2)
- Tracering op NVA Nic's om het ontvangen en verzenden van netwerk verkeer te controleren
- Bij het gebruik van een standaard-SKU en open bare Ip's moet er een NSG zijn gemaakt en een expliciete regel waarmee het verkeer kan worden doorgestuurd naar de NVA.

## <a name="basic-troubleshooting-steps"></a>Eenvoudige stappen voor probleem oplossing

- De basis configuratie controleren
- Prestaties van NVA controleren
- Geavanceerde netwerk problemen oplossen

## <a name="check-the-minimum-configuration-requirements-for-nvas-on-azure"></a>Controleer de minimale configuratie vereisten voor Nva's op Azure

Elke NVA heeft basis vereisten voor de configuratie om goed te kunnen werken in Azure. De volgende sectie bevat de stappen om deze basis configuraties te controleren. [Neem contact op met de leverancier van de NVA](https://mskb.pkisolutions.com/kb/2984655)voor meer informatie.

**Controleer of door sturen via IP is ingeschakeld op NVA**

Azure Portal gebruiken

1. Ga naar de NVA-resource in het [Azure Portal](https://portal.azure.com), selecteer netwerken en selecteer vervolgens de netwerk interface.
2. Selecteer op de pagina netwerk interface de optie IP-configuratie.
3. Zorg ervoor dat door sturen via IP is ingeschakeld.

PowerShell gebruiken

1. Open Power shell en meld u aan bij uw Azure-account.
2. Voer de volgende opdracht uit (Vervang de waarden met tussen haakjes door uw gegevens):

   ```powershell
   Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   ```

3. Controleer de eigenschap **EnableIPForwarding** .
4. Als door sturen via IP niet is ingeschakeld, voert u de volgende opdrachten uit om deze in te scha kelen:

   ```powershell
   $nic2 = Get-AzNetworkInterface -ResourceGroupName <ResourceGroupName> -Name <NicName>
   $nic2.EnableIPForwarding = 1
   Set-AzNetworkInterface -NetworkInterface $nic2
   Execute: $nic2 #and check for an expected output:
   EnableIPForwarding   : True
   NetworkSecurityGroup : null
   ```

**Controleren op NSG bij gebruik van standaard-SKU Pubilc IP** Bij het gebruik van een standaard-SKU en open bare Ip's moet er een NSG zijn gemaakt en een expliciete regel om het verkeer naar de NVA toe te staan.

**Controleer of het verkeer kan worden doorgestuurd naar de NVA**

1. Open **Network Watcher** op [Azure Portal](https://portal.azure.com), selecteer **volgende hop**.
2. Geef een virtuele machine op die is geconfigureerd om het verkeer om te leiden naar de NVA en een doel-IP-adres waarop de volgende hop wordt weer gegeven. 
3. Als de NVA niet als de **volgende hop** wordt weer gegeven, controleert u de Azure-route tabellen en werkt u deze bij.

**Controleer of het verkeer de NVA kan bereiken**

1. Open in [Azure Portal](https://portal.azure.com) **Network Watcher** en selecteer vervolgens **IP-stroom controleren**. 
2. Geef de virtuele machine en het IP-adres van de NVA op en controleer of het verkeer wordt geblokkeerd door netwerk beveiligings groepen (NSG).
3. Als er een NSG-regel is die het verkeer blokkeert, zoekt u de NSG in de **juiste beveiligings** regels en werkt u deze bij zodat verkeer kan worden door gegeven. Voer vervolgens de **IP-stroom** opnieuw uit en gebruik **verbindings problemen oplossen** om TCP-communicatie van de virtuele machine naar uw interne of externe IP-adres te testen.

**Controleer of NVA en virtuele machines Luis teren naar het verwachte verkeer**

1. Maak verbinding met de NVA met behulp van RDP of SSH en voer vervolgens de volgende opdracht uit:

    Voor Windows:

    ```console
   netstat -an
    ```

    Voor Linux:

    ```console
   netstat -an | grep -i listen
    ```
2. Als u de TCP-poort die wordt gebruikt door de NVA-software die wordt vermeld in de resultaten niet ziet, moet u de toepassing op de NVA en VM configureren om te Luis teren en te reageren op verkeer dat deze poorten bereikt. [Neem zo nodig contact op met de NVA-leverancier](https://mskb.pkisolutions.com/kb/2984655).

## <a name="check-nva-performance"></a>Prestaties van NVA controleren

### <a name="validate-vm-cpu"></a>VM-CPU valideren

Als het CPU-gebruik bijna 100 procent wordt, kunt u problemen ondervinden die van invloed zijn op het netwerk pakket. Uw VM rapporteert gemiddeld CPU-verbruik voor een specifieke tijds Panne in het Azure Portal. Onderzoek tijdens een CPU-piek welk proces op de gast-VM de hoge CPU veroorzaakt en verminder, indien mogelijk. U moet mogelijk ook de grootte van de virtuele machine wijzigen in een grotere SKU-grootte of, voor schaal sets voor virtuele machines, het aantal instanties verhogen of instellen op automatisch schalen op het CPU-gebruik. Neem voor beide problemen [contact op met de NVA-leverancier voor hulp](https://mskb.pkisolutions.com/kb/2984655), indien nodig.

### <a name="validate-vm-network-statistics"></a>VM-netwerk statistieken valideren

Als het VM-netwerk pieken gebruikt of peri Oden met een hoog gebruik laat zien, moet u mogelijk ook de SKU-grootte van de virtuele machine verhogen om hogere doorvoer mogelijkheden te verkrijgen. U kunt de virtuele machine ook opnieuw implementeren door versneld netwerken in te scha kelen. Als u wilt controleren of de NVA de functie voor versneld netwerken ondersteunt, neemt u, indien nodig, [contact op met de leverancier van de NVA](https://mskb.pkisolutions.com/kb/2984655).

## <a name="advanced-network-administrator-troubleshooting"></a>Problemen oplossen met geavanceerde netwerk beheerders

### <a name="capture-network-trace"></a>Netwerk tracering vastleggen
Leg een gelijktijdige netwerk tracering op de bron-VM, de NVA en de doel-VM vast terwijl u **[PsPing](/sysinternals/downloads/psping)** of **nmap** uitvoert, en Stop vervolgens de tracering.

1. Als u een gelijktijdige netwerk tracering wilt vastleggen, voert u de volgende opdracht uit:

   **Voor Windows**

   Netsh Trace Start Capture = ja tracefile = c:\ server_IP. etl scenario = NetConnection

   **Voor Linux**

   sudo tcpdump-S0-i eth0-X-w vmtrace. Cap

2. Gebruik **PsPing** of **nmap** van de bron-VM naar de doel-VM (bijvoorbeeld: `PsPing 10.0.0.4:80` of `Nmap -p 80 10.0.0.4` ).
3. Open de netwerk tracering van de doel-VM met behulp van [Network Monitor](https://download.cnet.com/s/network-monitor) of tcpdump. Pas een weergave filter toe voor het IP-adres van de bron-VM waarop u **PsPing** of **nmap** hebt uitgevoerd, zoals `IPv4.address==10.0.0.4 (Windows netmon)` of `tcpdump -nn -r vmtrace.cap src or dst host 10.0.0.4` (Linux).

### <a name="analyze-traces"></a>Traceringen analyseren

Als u de pakketten die binnenkomen aan de back-end-VM-tracering niet ziet, zijn er waarschijnlijk een NSG-of UDR-storing of zijn de NVA-routerings tabellen onjuist.

Als de inkomende pakketten wel worden weergegeven, maar er geen antwoord is, moet u mogelijk een probleem met een VM-toepassing of firewall oplossen. Neem voor een van deze problemen [contact op met de NVA-leverancier voor hulp, indien nodig](https://mskb.pkisolutions.com/kb/2984655).