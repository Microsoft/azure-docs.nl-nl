---
title: Overzicht van IPv6-Azure Load Balancer
description: Met dit leer traject kunt u aan de slag met IPv6-ondersteuning voor Azure Load Balancer en virtuele machines met taak verdeling.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: IPv6, Azure load balancer, dual stack, openbaar IP, systeem eigen IPv6, mobiel, IOT
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2018
ms.author: allensu
ms.openlocfilehash: 4061a3dbf4dc92d6d412528115d46edc36d20d5e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94700679"
---
# <a name="overview-of-ipv6-for-azure-load-balancer"></a>Overzicht van IPv6 voor Azure Load Balancer


>[!NOTE] 
>Deze inhoud is vervangen door [IPv6 voor Azure VNet-overzicht](../virtual-network/ipv6-overview.md). Azure raadt nieuwe IPv6-implementaties aan om de nieuwe functies van IPv6 voor virtuele netwerken van Azure te gebruiken.

>[!NOTE]
>Azure Load Balancer ondersteunt twee verschillende typen: Basic en Standard. In dit artikel wordt Basic Load Balancer beschreven. Zie [Standard Load Balancer Overview](./load-balancer-overview.md)voor meer informatie over Standard Load Balancer.

Basis-SKU Internet gerichte load balancers kan worden geïmplementeerd met een IPv6-adres. Naast de IPv4-connectiviteit, kunnen de volgende mogelijkheden worden toegepast:

* Systeem eigen end-to-end IPv6-connectiviteit tussen open bare internetclients en Azure Virtual Machines (Vm's) via de load balancer.
* Systeem eigen end-to-end IPv6-verbinding met uitgaand verkeer tussen Vm's en open bare Internet-clients met IPv6-functionaliteit.

In de volgende afbeelding ziet u de IPv6-functionaliteit voor Azure Load Balancer.

![Azure Load Balancer met IPv6](./media/load-balancer-ipv6-overview/load-balancer-ipv6.png)

Na de implementatie kan een IPv4-of IPv6-Internet-client communiceren met de open bare IPv4-of IPv6-adressen (of hostnamen) van de Azure Internet gerichte Load Balancer. De load balancer stuurt de IPv6-pakketten naar de privé-IPv6-adressen van de virtuele machines met behulp van Network Address Translation (NAT). De IPv6-Internet-client kan niet rechtstreeks communiceren met het IPv6-adres van de Vm's.

## <a name="features"></a>Functies

Systeem eigen IPv6-ondersteuning voor virtuele machines die via Azure Resource Manager worden geïmplementeerd biedt het volgende:

1. IPv6-Services met gelijke taak verdeling voor IPv6-clients op het Internet
2. Systeem eigen IPv6-en IPv4-eind punten op Vm's ("dubbele gestapeld")
3. Binnenkomende en uitgaande systeem eigen IPv6-verbindingen
4. Ondersteunde protocollen, zoals TCP, UDP en HTTP (S), bieden een volledige reeks service architecturen

## <a name="benefits"></a>Voordelen

Deze functionaliteit maakt de volgende belang rijke voor delen mogelijk:

* Voldoen aan wettelijke voor Schriften die vereisen dat nieuwe toepassingen toegankelijk zijn voor alleen IPv6-clients
* Mobiele en Internet der dingen (IOT)-ontwikkel aars in staat stellen om met dubbele gestapelde (IPv4 + IPv6) Azure Virtual Machines de groeiende mobiele & IOT-markten te verhelpen

## <a name="details-and-limitations"></a>Details en beperkingen

Details

* De Azure DNS-service bevat zowel IPv4 A-als IPv6 AAAA-naam records en reageert beide records voor de load balancer. De client kiest voor welk adres (IPv4 of IPv6) moet worden gecommuniceerd.
* Wanneer een virtuele machine verbinding initieert met een IPv6-apparaat dat is verbonden met een openbaar netwerk, is het IPv6-bron adres van de virtuele machine NAT (Network Address Translation) naar het open bare IPv6-adres van de load balancer.
* Vm's met het Linux-besturings systeem moeten worden geconfigureerd voor het ontvangen van een IP-adres via DHCP. Veel Linux-installatie kopieën in de Azure-galerie zijn al geconfigureerd voor de ondersteuning van IPv6 zonder aanpassing. Zie [Configure DHCPv6 for Linux vm's](load-balancer-ipv6-for-linux.md) voor meer informatie.
* Als u een status test met uw load balancer wilt gebruiken, maakt u een IPv4-test en gebruikt u deze met zowel IPv4-als IPv6-eind punten. Als de service op uw virtuele machine uitvalt, worden de IPv4-en IPv6-eind punten uit de rotatie gehaald.

Beperkingen

* U kunt geen IPv6-taakverdelings regels toevoegen in de Azure Portal. De regels kunnen alleen worden gemaakt via de sjabloon, CLI, Power shell.
* U mag bestaande Vm's niet upgraden voor het gebruik van IPv6-adressen. U moet nieuwe Vm's implementeren.
* Er kan één IPv6-adres worden toegewezen aan één netwerk interface in elke VM.
* De open bare IPv6-adressen kunnen niet worden toegewezen aan een virtuele machine. Ze kunnen alleen worden toegewezen aan een load balancer.
* U kunt de achterwaartse DNS-zoek opdracht voor uw open bare IPv6-adressen niet configureren.
* De virtuele machines met de IPv6-adressen kunnen geen deel uitmaken van een Azure-Cloud service. Ze kunnen worden verbonden met een Azure-Virtual Network (VNet) en met elkaar communiceren via hun IPv4-adressen.
* Privé-IPv6-adressen kunnen worden geïmplementeerd op afzonderlijke Vm's in een resource groep, maar kunnen niet worden geïmplementeerd in een resource groep via schaal sets.
* Virtuele Azure-machines kunnen geen verbinding maken via IPv6 naar andere Vm's, andere Azure-Services of on-premises apparaten. Ze kunnen alleen communiceren met de Azure-load balancer via IPv6. Ze kunnen echter met behulp van IPv4 communiceren met deze andere resources.
* De beveiliging van de netwerk beveiligings groep (NSG) voor IPv4 wordt ondersteund in implementaties met dubbele stack (IPv4 + IPv6). Nsg's zijn niet van toepassing op de IPv6-eind punten.
* Het IPv6-eind punt op de virtuele machine wordt niet rechtstreeks aan Internet blootgesteld. Het bevindt zich achter een load balancer. Alleen de poorten die zijn opgegeven in de load balancer regels, zijn toegankelijk via IPv6.
* Het wijzigen van de IdleTimeout-para meter voor IPv6 wordt **momenteel niet ondersteund**. De standaard waarde is vier minuten.
* Het wijzigen van de loadDistributionMethod-para meter voor IPv6 wordt **momenteel niet ondersteund**.
* IPv6 voor Basic Load Balancer is vergrendeld op een **dynamische** SKU.  IPv6 voor een Standard Load Balancer is vergrendeld op een **statische** SKU.
* NAT64 (omzetting van IPv6 naar IPv4) wordt niet ondersteund.
* Het koppelen van een secundaire NIC die verwijst naar een IPv6-subnet naar een back-end-groep wordt **momenteel niet ondersteund**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het implementeren van een load balancer met IPv6.

* [Beschik baarheid van IPv6 per regio](https://go.microsoft.com/fwlink/?linkid=828357)
* [Een load balancer met IPv6 implementeren met behulp van een sjabloon](load-balancer-ipv6-internet-template.md)
* [Een load balancer met IPv6 implementeren met behulp van Azure PowerShell](load-balancer-ipv6-internet-ps.md)
* [Een load balancer met IPv6 implementeren met behulp van Azure CLI](load-balancer-ipv6-internet-cli.md)