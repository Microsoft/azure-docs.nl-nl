---
title: Een virtuele machine maken met een statisch openbaar IP-adres-Azure Portal
description: Meer informatie over het maken van een virtuele machine met een statisch openbaar IP-adres met behulp van de Azure Portal.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/12/2020
ms.author: allensu
ms.openlocfilehash: d416af3d3a8eb8ab8057f13cc0d9a133adcb849a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98221153"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Maak een virtuele machine met een statisch openbaar IP-adres met behulp van de Azure Portal

Met een openbaar IP-adres kunt u via internet communiceren met een virtuele machine. 

Wijs een statisch openbaar IP-adres in plaats van een dynamisch adres toe om ervoor te zorgen dat het adres nooit wordt gewijzigd.   

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

1. Selecteer in de linkerbovenhoek van het portaal de optie **Een resource maken** > **Compute** > **Virtuele machine** of zoek naar **Virtuele machine** via het zoekvak.
   
2. In **Een virtuele machine maken** typt of selecteert u de waarden op het tabblad **Basisinformatie**:

    | Instelling | Waarde                                          |
    |-----------------------|----------------------------------|
    | **Projectgegevens** |  |
    | Abonnement | Selecteer uw Azure-abonnement |
    | Resourcegroep | Selecteer **Nieuw maken**. </br> Voer bij **naam** **myResourceGroup** in. </br> Selecteer **OK**. |
    | **Exemplaardetails** |  |
    | Naam van de virtuele machine | Open **myVM** |
    | Region | Selecteer **VS - oost** |
    | Beschikbaarheidsopties | Selecteer **Geen infrastructuurredundantie vereist** |
    | Installatiekopie | Selecteer **Windows Server 2019 Datacenter - Gen1** |
    | Azure Spot-exemplaar | Selecteer **Nee** |
    | Grootte | Kies een VM-grootte of kies de standaardinstelling |
    | **Beheerdersaccount** |  |
    | Gebruikersnaam | Voer een gebruikersnaam in |
    | Wachtwoord | Voer een wachtwoord in |
    | Wachtwoord bevestigen | Voer het wachtwoord opnieuw in |

3. Selecteer het tabblad **Netwerken** of selecteer **Volgende: Schijven** en vervolgens **Volgende: Netwerken**.
  
4. Op het tabblad Netwerken selecteert u of voert u het volgende in:

    | Instelling | Waarde |
    |-|-|
    | **Netwerkinterface** |  |
    | Virtueel netwerk | Accepteer de standaard naam van het netwerk. |
    | Subnet | Accepteer de standaard-subnet-configuratie. |
    | Openbare IP | Selecteer **Nieuw maken**. </br> In **openbaar IP-adres maken** typt u in de naam **myPublicIP**. </br> Selecteer voor **SKU** de optie **standaard**. </br> **Toewijzing**, selecteer **statisch**. </br> Selecteer **OK**.  |
    | NIC-netwerkbeveiligingsgroep | Selecteer **Basic**|
    | Openbare poorten voor inkomend verkeer | Selecteer **Geselecteerde poorten toestaan**. |
    | Binnenkomende poorten selecteren | Selecteer **RDP (3389)** |

    > [!WARNING]
    > Portal 3389 is geselecteerd om externe toegang tot de virtuele Windows Server-machine via internet in te scha kelen. Het openen van poort 3389 voor Internet wordt niet aanbevolen voor het beheren van productie werkbelastingen. </br> Zie **[Wat is Azure Bastion?](../bastion/bastion-overview.md) voor beveiligde toegang tot Azure virtual machines.**
   
5. Selecteer **Controleren + maken**. 
  
6. Controleer de instellingen en selecteer vervolgens **Maken**.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de resourcegroep en alle gerelateerde resources die deze bevat verwijderen wanneer u deze niet meer nodig hebt:

1. Voer **myResourceGroup** in het vak **Zoeken** bovenaan de portal in. Wanneer u **myResourceGroup** ziet in de zoekresultaten, selecteert u deze.
2. Selecteer **Resourcegroep verwijderen**.
3. Voer **myResourceGroup** in voor **TYP DE RESOURCEGROEPNAAM:** en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Zie [IP-adressen toevoegen, wijzigen of verwijderen](virtual-network-network-interface-addresses.md):

* Een openbaar IP-adres wijzigen van dynamisch in statisch.
* Werken met privé-IP-adressen.

Open bare IP-adressen hebben een [nominale belasting](https://azure.microsoft.com/pricing/details/ip-addresses). Er is een [limiet](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor het aantal open bare IP-adressen dat u per abonnement kunt gebruiken.

De SKU van het open bare IP-adres van de virtuele machine moet overeenkomen met de open bare IP-SKU van Azure Load Balancer wanneer deze wordt toegevoegd aan een back-end-groep. Zie [Azure Load Balancer](../load-balancer/skus.md)voor meer informatie.

U kunt de lijst met bereiken (voorvoegsels) downloaden voor de Azure-clouds [Openbaar](https://www.microsoft.com/download/details.aspx?id=56519), [US government](https://www.microsoft.com/download/details.aspx?id=57063), [China](https://www.microsoft.com/download/details.aspx?id=57062) en [Duitsland](https://www.microsoft.com/download/details.aspx?id=57064).

- Meer informatie over [statische open bare IP-adressen](./public-ip-addresses.md#allocation-method).
- Meer informatie over [open bare IP-adressen](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Meer informatie over alle [open bare IP-adres instellingen](virtual-network-public-ip-address.md#create-a-public-ip-address).
- Meer informatie over [privé-IP-adressen](./private-ip-addresses.md) en het toewijzen van een [statisch privé-IP-adres](virtual-network-network-interface-addresses.md#add-ip-addresses) aan een virtuele Azure-machine.
