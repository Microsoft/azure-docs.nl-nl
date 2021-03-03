---
title: Werken met Vm's en Nsg's in azure Bastion
description: U kunt netwerk beveiligings groepen gebruiken met Azure Bastion. Meer informatie over de subnetten die zijn vereist voor deze configuratie.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: b6a0dee4c3fef1be4f4b9f910b4c6256b4924a2d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700215"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Werken met NSG-toegang en Azure Bastion

Wanneer u met Azure Bastion werkt, kunt u netwerk beveiligings groepen (Nsg's) gebruiken. Zie [beveiligings groepen](../virtual-network/network-security-groups-overview.md)voor meer informatie.

:::image type="content" source="./media/bastion-nsg/figure-1.png" alt-text="NSG":::

In dit diagram:

* De bastion-host wordt geïmplementeerd in het virtuele netwerk.
* Maakt de gebruiker verbinding met Azure Portal met behulp van een HTML5-browser.
* De gebruiker navigeert naar de virtuele machine van Azure naar RDP/SSH.
* Connect Integration-een RDP/SSH-sessie met één klik binnen de browser
* Is er geen openbaar IP-adres vereist voor de virtuele Azure-machine.

## <a name="network-security-groups"></a><a name="nsg"></a>Netwerk beveiligings groepen

In deze sectie ziet u het netwerk verkeer tussen de gebruiker en Azure Bastion, en tot doel-Vm's in uw virtuele netwerk:

> [!IMPORTANT]
> Als u ervoor kiest om een NSG te gebruiken met uw Azure Bastion-resource, **moet** u alle volgende regels voor binnenkomend en uitgaand verkeer maken. Als u een van de volgende regels in uw NSG weglaat, zal uw Azure Bastion-resource de vereiste updates in de toekomst niet meer kunnen ontvangen en kan de bron daarom worden geopend voor toekomstige beveiligings problemen.
> 

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

Azure Bastion wordt specifiek geïmplementeerd voor ***AzureBastionSubnet***.

* **Binnenkomend verkeer:**

   * **Binnenkomend verkeer via het open bare Internet:** De Azure-Bastion maakt een openbaar IP-adres waarvoor poort 443 is ingeschakeld op het open bare IP-adres voor binnenkomend verkeer. Poort 3389/22 hoeft niet te worden geopend op de AzureBastionSubnet.
   * **Binnenkomend verkeer vanuit het Azure Bastion-besturings vlak:** Schakel voor de connectiviteit van het besturings element het argument poort 443 binnenkomend van de **GatewayManager** -service in. Hiermee wordt het besturings vlak ingeschakeld, dat wil zeggen dat gateway beheer met Azure Bastion kan communiceren.
   * **Binnenkomend verkeer vanuit Azure Bastion data-vlak:** Schakel voor de communicatie tussen de onderliggende onderdelen van Azure Bastion poort 8080, 5701 van de **VirtualNetwork** -servicetag in op de servicetag **VirtualNetwork** . Hierdoor kunnen de onderdelen van Azure Bastion met elkaar communiceren.
   * **Binnenkomend verkeer van Azure Load Balancer:** Schakel voor status tests het poort 443 in dat binnenkomend van het **AzureLoadBalancer** -service label. Hiermee kan Azure Load Balancer connectiviteit detecteren


   :::image type="content" source="./media/bastion-nsg/inbound.png" alt-text="Scherm opname bevat binnenkomende beveiligings regels voor Azure Bastion-connectiviteit.":::

* **Uitgaand verkeer:**

   * Uitgaand **verkeer naar doel-vm's:** Azure Bastion bereikt de doel-Vm's via privé-IP. De Nsg's moet uitgaand verkeer naar andere doel-VM-subnetten toestaan voor poort 3389 en 22.
   * Uitgaand **verkeer naar Azure Bastion data-vlak:** Schakel voor de communicatie tussen de onderliggende onderdelen van Azure Bastion poort 8080, 5701 van de **VirtualNetwork** -servicetag naar het label **VirtualNetwork** -service in. Hierdoor kunnen de onderdelen van Azure Bastion met elkaar communiceren.
   * Uitgaand **verkeer naar andere open bare eind punten in Azure:** Azure Bastion moet verbinding kunnen maken met verschillende open bare eind punten in azure (bijvoorbeeld voor het opslaan van Diagnostische logboeken en logboeken voor meters). Daarom heeft Azure Bastion een uitgaand verkeer naar 443 naar **Cloud** service-tag nodig.
   * Uitgaand **verkeer naar Internet:** Azure Bastion moet kunnen communiceren met internet voor het valideren van sessies en certificaten. Daarom is het raadzaam poort 80 uitgaand verkeer naar Internet in te scha kelen **.**


   :::image type="content" source="./media/bastion-nsg/outbound.png" alt-text="Scherm opname toont de uitgaande beveiligings regels voor Azure Bastion-connectiviteit.":::

### <a name="target-vm-subnet"></a>Doel-VM-subnet
Dit is het subnet dat de virtuele doel machine bevat waarnaar u RDP/SSH wilt.

   * **Binnenkomend verkeer van Azure Bastion:** Azure Bastion is bereikbaar voor de doel-VM via een particulier IP-adres. RDP/SSH-poorten (poorten 3389/22 respectievelijk) moeten worden geopend op de doel-VM naast het particuliere IP-adres. Als best practice kunt u het IP-adres bereik van het Azure Bastion-subnet in deze regel toevoegen zodat alleen Bastion deze poorten op de doel-Vm's in het subnet van de doel-VM kunnen openen.


## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [Veelgestelde vragen](bastion-faq.md)voor meer informatie over Azure Bastion.
