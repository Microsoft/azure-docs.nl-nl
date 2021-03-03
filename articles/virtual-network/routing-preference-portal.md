---
title: Routerings voorkeur configureren voor een openbaar IP-adres-Azure Portal
description: Meer informatie over het maken van een openbaar IP-adres met een routerings voorkeur voor Internet verkeer
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: f445eab65e8d2448e57bad19c52a4b72732016bb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672926"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Routerings voorkeur configureren voor een openbaar IP-adres met behulp van de Azure Portal

In dit artikel wordt beschreven hoe u [routerings voorkeur](./routing-preference-overview.md) CONFIGUREERT via ISP Network (**Internet** optie) voor een openbaar IP-adres. Nadat u het open bare IP-adres hebt gemaakt, kunt u dit koppelen aan de volgende Azure-resources voor binnenkomend en uitgaand verkeer naar Internet:

* Virtuele machine
* Schaalset voor virtuele machines
* Azure Kubernetes Service (AKS)
* Internet gerichte load balancer
* Application Gateway
* Azure Firewall

De routerings voorkeur voor openbaar IP-adres is standaard ingesteld op het micro soft Global Network voor alle Azure-Services en kan worden gekoppeld aan elke Azure-service.

Als u nog geen abonnement op Azure hebt, maak dan nu een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>Een openbaar IP-adres maken met een routerings voorkeur
1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Een resource maken**.
3. Typ *openbaar IP-adres* in het zoekvak.
3. Selecteer **openbaar IP-adres** in de zoek resultaten. Selecteer vervolgens op de pagina **openbaar IP-adres** **maken**.
1. Selecteer voor SKU de optie **standaard**.
1. Selecteer voor **routerings voorkeur** **Internet**.

      ![Een openbaar IP-adres maken](./media/routing-preference-portal/public-ip-new.png)
1. Voer in het gedeelte **IP-adres configuratie van IPv4** de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Abonnement | Selecteer uw abonnement.|
    | Resourcegroep | Selecteer **nieuwe maken**, Voer *RoutingPreferenceResourceGroup* in en selecteer **OK**. |
    | Locatie | Selecteer **VS - oost**.|
    | Beschikbaarheidszone | Behoud de standaard waarde- **zone-redundant**. |
1. Selecteer **Maken**.

    > [!NOTE]
    > Openbare IP-adressen worden gemaakt met een IPv4- of IPv6-adres. Routerings voorkeur ondersteunt momenteel echter alleen IPV4.

U kunt het hierboven gemaakte open bare IP-adres koppelen aan een virtuele [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -of [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -machine. Gebruik de CLI-sectie op de pagina zelf studie: [een openbaar IP-adres koppelen aan een virtuele machine](associate-public-ip-address-vm.md#azure-cli) om de open bare IP aan uw VM te koppelen. U kunt ook het eerder gemaakte open bare IP-adres koppelen aan een [Azure Load Balancer](../load-balancer/load-balancer-overview.md)door het toe te wijzen aan de Load Balancer front- **End** -configuratie. Het openbare IP-adres doet dienst als een virtueel IP-adres (VIP) met taakverdeling.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [open bare IP met routerings voorkeur](routing-preference-overview.md).
- [Routerings voorkeur configureren voor een virtuele machine](tutorial-routing-preference-virtual-machine-portal.md).
- [Configureer routerings voorkeur voor een openbaar IP-adres met behulp van de Power shell](routing-preference-powershell.md).
- Meer informatie over [open bare IP-adressen](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Meer informatie over alle [open bare IP-adres instellingen](virtual-network-public-ip-address.md#create-a-public-ip-address).