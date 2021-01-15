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
ms.date: 12/02/2020
ms.author: mnayak
ms.openlocfilehash: 2c6295db96f951abd1fd069535b98639e723d93a
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98217563"
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

> [!IMPORTANT]
> De voor keuren voor route ring is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Als u nog geen abonnement op Azure hebt, maak dan nu een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>Een openbaar IP-adres maken met een routerings voorkeur
1. Meld u aan bij de [Azure-portal](https://preview.portal.azure.com/).
2. Selecteer **Een resource maken**. 
3. Typ *openbaar IP-adres* in het zoekvak.
3. Selecteer **openbaar IP-adres** in de zoek resultaten. Selecteer vervolgens op de pagina **openbaar IP-adres** **maken**.
3. Selecteer **Internet** in de **Voorkeurs instellingen voor route ring** .

      ![Een openbaar IP-adres maken](./media/routing-preference-portal/pip-new.png)

    > [!NOTE]
    > Openbare IP-adressen worden gemaakt met een IPv4- of IPv6-adres. Routerings voorkeur ondersteunt momenteel echter alleen IPV4.

U kunt het hierboven gemaakte open bare IP-adres koppelen aan een virtuele [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -of [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -machine. Gebruik de CLI-sectie op de pagina zelf studie: [een openbaar IP-adres koppelen aan een virtuele machine](associate-public-ip-address-vm.md#azure-cli) om de open bare IP aan uw VM te koppelen. U kunt ook het eerder gemaakte open bare IP-adres koppelen aan een [Azure Load Balancer](../load-balancer/load-balancer-overview.md)door het toe te wijzen aan de Load Balancer front- **End** -configuratie. Het openbare IP-adres doet dienst als een virtueel IP-adres (VIP) met taakverdeling.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [open bare IP met routerings voorkeur](routing-preference-overview.md).
- [Routerings voorkeur configureren voor een virtuele machine](tutorial-routing-preference-virtual-machine-portal.md).
- [Configureer routerings voorkeur voor een openbaar IP-adres met behulp van de Power shell](routing-preference-powershell.md).
- Meer informatie over [open bare IP-adressen](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Meer informatie over alle [open bare IP-adres instellingen](virtual-network-public-ip-address.md#create-a-public-ip-address).