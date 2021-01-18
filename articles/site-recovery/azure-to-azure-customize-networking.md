---
title: Netwerk configuraties aanpassen voor een failover-VM | Microsoft Docs
description: Biedt een overzicht van het aanpassen van netwerk configuraties voor een failover-VM in de replicatie van virtuele Azure-machines met behulp van Azure Site Recovery.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: sideeksh
ms.openlocfilehash: f63021275574e294fa372357d6e62724f5efe0f7
ms.sourcegitcommit: 949c0a2b832d55491e03531f4ced15405a7e92e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98541187"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Netwerkconfiguraties van de beoogde Azure-VM aanpassen

Dit artikel bevat richt lijnen voor het aanpassen van netwerk configuraties op de doel-VM van Azure, wanneer u virtuele Azure-machines van de ene naar de andere regio repliceert en herstelt met behulp van [Azure site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Voordat u begint

Meer informatie over hoe Site Recovery herstel na nood gevallen biedt voor [dit scenario](azure-to-azure-architecture.md).

## <a name="supported-networking-resources"></a>Ondersteunde netwerk bronnen

U kunt de volgende belang rijke bron configuraties voor de failover-VM opgeven tijdens het repliceren van virtuele Azure-machines:

- [Interne load balancer](../load-balancer/load-balancer-overview.md)
- [Openbare IP](../virtual-network/public-ip-addresses.md)
- [Netwerk beveiligings groep](../virtual-network/manage-network-security-group.md) zowel voor het subnet als voor de NIC

## <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat u de configuraties van de herstel kant van tevoren plant.
- Maak vooraf de netwerk resources. Geef het op als invoer zodat Azure Site Recovery service deze instellingen kan naleven en ervoor moet zorgen dat de failover-VM aan deze instellingen voldoet.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>Failover-en test failover-netwerk configuraties aanpassen

1. Ga naar **gerepliceerde items**. 
2. Selecteer de gewenste Azure-VM.
3. Selecteer **Compute en netwerk** en selecteer **bewerken**. U ziet dat de configuratie-instellingen van de NIC de bijbehorende bronnen in de bron bevatten. 

     ![De failover-netwerk configuraties aanpassen](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Selecteer een virtueel netwerk voor testfailover.
5. Failover-netwerk Selecteer **bewerken** in de buurt van de NIC die u wilt configureren. Selecteer op de volgende Blade die wordt geopend de bijbehorende vooraf gemaakte resources in de testfailover en de failover-locatie.

    ![De NIC-configuratie bewerken](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. Selecteer **OK**.

Site Recovery gaat nu door met deze instellingen en zorg ervoor dat de virtuele machine op failover is verbonden met de geselecteerde resource via de bijbehorende NIC.

Wanneer u de testfailover via het herstel plan triggert, wordt het virtuele Azure-netwerk altijd gevraagd. Dit virtuele netwerk wordt gebruikt voor een testfailover voor de computers waarvoor de instellingen voor de testfailover niet vooraf zijn geconfigureerd.

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="unable-to-view-or-select-a-resource"></a>Een resource kan niet worden weer gegeven of geselecteerd

Als u een netwerk bron niet kunt selecteren of weer geven, gaat u naar de volgende controles en voor waarden:

- Het doel veld voor een netwerk bron is alleen ingeschakeld als de bron-VM een overeenkomende invoer heeft. Dit is gebaseerd op het principe dat voor een nood herstel scenario zou moeten overeenkomen met de exacte of een geschaalde versie van de bron.
- Voor elke netwerk bron worden sommige filters toegepast in de vervolg keuzelijst om ervoor te zorgen dat de virtuele machine voor failover kan worden gekoppeld aan de geselecteerde resource en dat de betrouw baarheid van de failover wordt gehandhaafd. Deze filters zijn gebaseerd op de netwerk omstandigheden die tijdens de configuratie van de bron-VM zouden zijn gecontroleerd.

Interne load balancer validaties:

- Het abonnement en de regio van de load balancer en de doel-VM moeten hetzelfde zijn.
- Het virtuele netwerk dat is gekoppeld aan de interne load balancer en die van de doel-VM moeten hetzelfde zijn.
- De open bare IP-SKU van de doel-VM en de SKU van de interne load balancer moeten hetzelfde zijn.
- Als de doel-VM is geconfigureerd om in een beschikbaarheids zone te worden geplaatst, controleert u of de load balancer zone redundant is of deel uitmaakt van een beschikbaarheids zone. (Basic SKU load balancers bieden geen ondersteuning voor zones en worden in dit geval niet weer gegeven in de vervolg keuzelijst.)
- Zorg ervoor dat de interne load balancer een vooraf gemaakte back-end-pool en front-end-configuratie heeft.

Openbaar IP-adres:

- Het abonnement en de regio van het open bare IP-adres en de doel-VM moeten hetzelfde zijn.
- De open bare IP-SKU van de doel-VM en de SKU van de interne load balancer moeten hetzelfde zijn.

Netwerk beveiligings groep:
- Het abonnement en de regio van de netwerk beveiligings groep en de doel-VM moeten hetzelfde zijn.


> [!WARNING]
> Als de doel-VM is gekoppeld aan een beschikbaarheidsset, moet u het open bare IP-adres en de interne load balancer koppelen van dezelfde SKU met die van het open bare IP-adres van de andere VM en interne load balancer in de beschikbaarheidsset. Als dat niet het geval is, mislukt de failover mogelijk niet.
