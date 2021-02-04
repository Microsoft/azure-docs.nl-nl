---
title: Een open bare IP-Azure Portal maken
description: Meer informatie over het maken van een openbaar IP-adres in de Azure Portal
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: 7d0c83f1ae18d36557a7a5b0222aee2905e05cb7
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550232"
---
# <a name="quickstart-create-a-public-ip-address-using-the-azure-portal"></a>Snelstartgids: een openbaar IP-adres maken met behulp van de Azure Portal

In dit artikel wordt beschreven hoe u een open bare IP-adres bron maakt met behulp van de Azure Portal. Zie [open bare IP-adressen](./public-ip-addresses.md)voor meer informatie over de resources waaraan dit kan worden gekoppeld, het verschil tussen de basis-en standaard-SKU en andere gerelateerde informatie.  In dit voor beeld richten we zich alleen op IPv4-adressen. Zie voor meer informatie over IPv6-adressen [IPv6 voor Azure VNet](./ipv6-overview.md).

# <a name="standard-sku"></a>[**Standaard SKU**](#tab/option-create-public-ip-standard-zones)

Voer de volgende stappen uit om een standaard zone-redundant openbaar IP-adres te maken met de naam **myStandardZRPublicIP**.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Een resource maken**. 
3. Typ *openbaar IP-adres* in het zoekvak.
4. Selecteer **openbaar IP-adres** in de zoek resultaten. Selecteer vervolgens op de pagina **openbaar IP-adres** **maken**.
5. Voer op de pagina **openbaar IP-adres maken** de volgende gegevens in of Selecteer deze: 

    | Instelling                 | Waarde                       |
    | ---                     | ---                         |
    | IP-versie              | IPv4 selecteren                 |    
    | SKU                     | selecteer **Standard**         |
    | Laag (indien weer gegeven *)                  | **Regionaal** selecteren         |
    | Name                    | *MyStandardZRPublicIP* invoeren          |
    | IP-adrestoewijzing   | Opmerking: dit wordt vergrendeld als ' statisch '                                        |
    | Time-out voor inactiviteit (minuten)  | Wijzig de waarde in 4        |
    | DNS-naamlabel          | Laat de waarde leeg    |
    | Abonnement            | Selecteer uw abonnement.   |
    | Resourcegroep          | Selecteer **nieuwe maken** , voer myResourceGroup in en selecteer **OK** . |
    | Locatie                | Selecteer **US - oost 2**      |
    | Beschikbaarheidszone       | Selecteer **zone-redundant**, geen zone of specifieke zone kiezen (zie opmerking hieronder) |

Houd er rekening mee dat dit alleen geldige selecties zijn in regio's met [Beschikbaarheidszones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).  (U kunt ook een specifieke zone in deze regio's selecteren, maar het is niet mogelijk om een zonegebonden fout te maken.)  Zie [overzicht van beschikbaarheids zones](https://docs.microsoft.com/azure/availability-zones/az-overview)voor meer informatie over beschikbaarheids zones.

\* = De laag maakt deel uit van de functionaliteit voor [Load Balancer van meerdere regio's](../load-balancer/cross-region-overview.md) , momenteel als preview-versie beschikbaar.

# <a name="basic-sku"></a>[**Basis-SKU**](#tab/option-create-public-ip-basic)

Voer de volgende stappen uit om een eenvoudig, statisch openbaar IP-adres te maken met de naam **myBasicPublicIP**.  Algemene open bare Ip's hebben niet het concept van beschikbaarheids zones.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Een resource maken**. 
3. Typ *openbaar IP-adres* in het zoekvak.
4. Selecteer **openbaar IP-adres** in de zoek resultaten. Selecteer vervolgens op de pagina **openbaar IP-adres** **maken**.
5. Voer op de pagina **openbaar IP-adres maken** de volgende gegevens in of Selecteer deze: 

    | Instelling                 | Waarde                       |
    | ---                     | ---                         |
    | IP-versie              | IPv4 selecteren                 |    
    | SKU                     | selecteer **Standard**         |
    | Name                    | *MyBasicPublicIP* invoeren          |
    | IP-adrestoewijzing   | **Statisch** kiezen (zie opmerking hieronder)                                     |
    | Time-out voor inactiviteit (minuten)  | Wijzig de waarde in 4        |
    | DNS-naamlabel          | Laat de waarde leeg    |
    | Abonnement            | Selecteer uw abonnement.   |
    | Resourcegroep          | Selecteer **nieuwe maken** , voer myResourceGroup in en selecteer **OK** . |
    | Locatie                | Selecteer **US - oost 2**      |

Als het IP-adres acceptabel is om na verloop van tijd te wijzigen, kan **dynamische** IP-toewijzing worden geselecteerd.

---

## <a name="additional-information"></a>Aanvullende informatie 

Zie [open bare IP-adressen beheren](./virtual-network-public-ip-address.md#create-a-public-ip-address)voor meer informatie over de afzonderlijke velden die hierboven worden vermeld.

## <a name="next-steps"></a>Volgende stappen
- Een [openbaar IP-adres koppelen aan een virtuele machine](./associate-public-ip-address-vm.md#azure-portal)
- Meer informatie over [open bare IP-adressen](./public-ip-addresses.md#public-ip-addresses) in Azure.
- Meer informatie over alle [open bare IP-adres instellingen](virtual-network-public-ip-address.md#create-a-public-ip-address).
