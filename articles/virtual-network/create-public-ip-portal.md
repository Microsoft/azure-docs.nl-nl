---
title: Een open bare IP-Azure Portal maken
description: Meer informatie over het maken van een openbaar IP-adres in de Azure Portal
services: virtual-network
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: how-to
ms.date: 02/22/2021
ms.author: allensu
ms.openlocfilehash: e6b7648188e2307da4ef40e0ab3daf6201f9d89d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694866"
---
# <a name="create-a-public-ip-address-using-the-azure-portal"></a>Een openbaar IP-adres maken met behulp van de Azure Portal

In dit artikel wordt beschreven hoe u een open bare IP-adres bron maakt met behulp van de Azure Portal. 

Zie [open bare IP-adressen](./public-ip-addresses.md)voor meer informatie over de resources waaraan dit open bare IP-adres kan worden gekoppeld en het verschil tussen de Basic-en Standard-sku's. 

Dit artikel richt zich op IPv4-adressen. Zie voor meer informatie over IPv6-adressen [IPv6 voor Azure VNet](./ipv6-overview.md).

# <a name="standard-sku"></a>[**Standaard SKU**](#tab/option-create-public-ip-standard-zones)

Voer de volgende stappen uit om een standaard zone-redundant openbaar IP-adres te maken met de naam **myStandardZRPublicIP**.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Een resource maken**. 
3. Voer in het zoekvak het **open bare IP-adres** in. Selecteer **openbaar IP-adres** in de zoek resultaten.
4. Selecteer op de pagina **openbaar IP-adres** **maken**.
5. Op de pagina **openbaar IP-adres maken** voert u de volgende gegevens in: 

    | Instelling                 | Waarde                       |
    | ---                     | ---                         |
    | IP-versie              | IPv4 selecteren                 |    
    | SKU                     | selecteer **Standard**         |
    | Primair                   | **Regionaal** selecteren         |
    | Name                    | **MyStandardZRPublicIP** invoeren          |
    | IP-adrestoewijzing   | Opmerking: deze selectie is vergrendeld als ' statisch '                                        |
    | Routerings voorkeur      | De standaard instelling van het **micro soft-netwerk** behouden. </br> Zie [Wat is routerings voorkeur (preview)?](./routing-preference-overview.md)voor meer informatie over routerings voorkeur. |
    | Time-out voor inactiviteit (minuten)  | De standaard waarde van **4** behouden.        |
    | DNS-naamlabel          | Laat de waarde leeg.    |
    | Abonnement            | Selecteer uw abonnement.   |
    | Resourcegroep          | Selecteer **nieuwe maken** en voer **myResourceGroup** in. </br> Selecteer **OK**. |
    | Locatie                | Selecteer **US - oost 2**      |
    | Beschikbaarheidszone       | Selecteer **zone-redundant**, geen zone of specifieke zone kiezen (zie opmerking hieronder) |

:::image type="content" source="./media/create-public-ip-portal/create-standard-ip.png" alt-text="Standaard-IP-adres maken in Azure Portal" border="false":::

> [!NOTE]
> Deze selecties zijn geldig in regio's met [Beschikbaarheidszones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones). </br>
U kunt een specifieke zone selecteren in deze regio's, hoewel het niet mogelijk is om zonegebonden fout te voor komen. </br> Zie [overzicht van beschikbaarheids zones](https://docs.microsoft.com/azure/availability-zones/az-overview)voor meer informatie over beschikbaarheids zones.

\* = De laag maakt deel uit van de functionaliteit voor [Load Balancer van meerdere regio's](../load-balancer/cross-region-overview.md) , momenteel als preview-versie beschikbaar.

# <a name="basic-sku"></a>[**Basis-SKU**](#tab/option-create-public-ip-basic)

In deze sectie maakt u een open bare basis-IP-adres met de naam **myBasicPublicIP**. 

> [!NOTE]
> Algemene open bare Ip's bieden geen ondersteuning voor beschikbaarheids zones.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Een resource maken**. 
3. Voer in het zoekvak het **open bare IP-adres** in. Selecteer **openbaar IP-adres** in de zoek resultaten.
4. Selecteer op de pagina **openbaar IP-adres** **maken**.
5. Op de pagina **openbaar IP-adres maken** voert u de volgende gegevens in: 

    | Instelling                 | Waarde                       |
    | ---                     | ---                         |
    | IP-versie              | IPv4 selecteren                 |    
    | SKU                     | Selecteer **Basic**         |
    | Name                    | *MyBasicPublicIP* invoeren          |
    | IP-adrestoewijzing   | **Statisch** selecteren (zie opmerking hieronder)                                     |
    | Time-out voor inactiviteit (minuten)  | De standaard waarde van **4** behouden.       |
    | DNS-naamlabel          | Laat de waarde leeg    |
    | Abonnement            | Selecteer uw abonnement.   |
    | Resourcegroep          | Selecteer **nieuwe maken** en voer **myResourceGroup** in. </br> Selecteer **OK**. |
    | Locatie                | Selecteer **US - oost 2**      |

:::image type="content" source="./media/create-public-ip-portal/create-basic-ip.png" alt-text="Standaard-IP-adres maken in Azure Portal" border="false":::

Als het IP-adres acceptabel is om na verloop van tijd te wijzigen, kan **dynamische** IP-toewijzing worden geselecteerd.

---

## <a name="additional-information"></a>Aanvullende informatie 

Zie [open bare IP-adressen beheren](./virtual-network-public-ip-address.md#create-a-public-ip-address)voor meer informatie over de afzonderlijke velden die hierboven worden vermeld.

## <a name="next-steps"></a>Volgende stappen
- Een [openbaar IP-adres koppelen aan een virtuele machine](./associate-public-ip-address-vm.md#azure-portal)
- Meer informatie over [open bare IP-adressen](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Meer informatie over alle [open bare IP-adres instellingen](virtual-network-public-ip-address.md#create-a-public-ip-address).