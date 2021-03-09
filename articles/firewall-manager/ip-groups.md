---
title: IP-groepen in het Azure Firewall-beleid
description: Met IP-groepen kunt u IP-adressen voor Azure Firewall-beleids regels groeperen en beheren.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: conceptual
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 54faa0bc7d414eb15a866b8e1e6d09e15b22b071
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500849"
---
# <a name="ip-groups-in-azure-firewall-policy"></a>IP-groepen in het Azure Firewall-beleid

Met IP-groepen kunt u IP-adressen voor Azure Firewall beleid op de volgende manieren groeperen en beheren:

- Als bron type in DNAT-regels
- Als bron-of doel type in netwerk regels
- Als bron type in toepassings regels


Een IP-groep kan één IP-adres, meerdere IP-adressen of een of meer IP-adresbereiken hebben.

IP-groepen kunnen opnieuw worden gebruikt in Azure Firewall DNAT-, netwerk-en toepassings regels voor meerdere firewalls voor verschillende regio's en abonnementen in Azure. Groeps namen moeten uniek zijn. U kunt een IP-groep configureren in de Azure Portal, Azure CLI of REST API. Er wordt een voorbeeld sjabloon gegeven om u te helpen aan de slag te gaan.

## <a name="sample-format"></a>Sample-indeling

De volgende IPv4-adres notatie voorbeelden zijn geldig voor gebruik in IP-groepen:

- Eén adres: 10.0.0.0
- CIDR-notatie: 10.1.0.0/32
- Adres bereik: 10.2.0.0-10.2.0.31

## <a name="create-an-ip-group"></a>Een IP-groep maken

Een IP-groep kan worden gemaakt met behulp van de Azure Portal, Azure CLI of REST API. Zie [een IP-groep maken](../firewall/create-ip-group.md)voor meer informatie.

## <a name="browse-ip-groups"></a>Bladeren door IP-groepen
1. In de Azure Portal zoek balk typt u **IP-groepen** en selecteert u deze. U kunt de lijst met IP-groepen zien of u kunt **toevoegen** selecteren om een nieuwe IP-groep te maken.
2. Selecteer een IP-groep om de pagina overzicht te openen. U kunt IP-adressen of IP-groepen bewerken, toevoegen of verwijderen.

   ![Overzicht van IP-groepen](media/ip-groups/overview.png)

## <a name="manage-an-ip-group"></a>Een IP-groep beheren

U kunt alle IP-adressen in de IP-groep en de bijbehorende regels of bronnen weer geven. Als u een IP-groep wilt verwijderen, moet u eerst de IP-groep ontkoppelen van de resource die deze gebruikt.

1. Als u de IP-adressen wilt weer geven of bewerken, selecteert u **IP-adressen** onder **instellingen** in het linkerdeel venster.
2. Als u één of meerdere IP-adressen wilt toevoegen, selecteert u **IP-adressen toevoegen**. Hiermee opent u de pagina **slepen of bladeren** voor een upload, of u kunt het adres hand matig invoeren.
3.    Selecteer de beletsel tekens (**...**) aan de rechter kant om IP-adressen te bewerken of te verwijderen. Als u meerdere IP-adressen wilt bewerken of verwijderen, selecteert u de vakken en selecteert u bovenaan **bewerken** of **verwijderen** .
4. Ten slotte kan het bestand in de CSV-bestands indeling worden geëxporteerd.

> [!NOTE]
> Als u alle IP-adressen in een IP-groep verwijdert terwijl deze nog steeds in een regel wordt gebruikt, wordt die regel overgeslagen.


## <a name="use-an-ip-group"></a>Een IP-groep gebruiken

U kunt nu **IP-groep** selecteren als **bron type** of **doel type** voor IP-adres (sen) wanneer u een beleid maakt met DNAT-, toepassings-of netwerk regels.

:::image type="content" source="media/ip-groups/firewall-ip-group.png" alt-text="IP-groepen in de firewall":::

## <a name="ip-address-limits"></a>IP-adres limieten

U kunt Maxi maal 100 IP-groepen per firewall hebben met een maximum van 5000 afzonderlijke IP-adressen of IP-voor voegsels per IP-groep.

## <a name="related-azure-powershell-cmdlets"></a>Gerelateerde Azure PowerShell-cmdlets

De volgende Azure PowerShell-cmdlets kunnen worden gebruikt voor het maken en beheren van IP-groepen:

- [New-AzIpGroup](/powershell/module/az.network/new-azipgroup)
- [Remove-AzIPGroup](/powershell/module/az.network/remove-azipgroup)
- [Get-AzIpGroup](/powershell/module/az.network/get-azipgroup)
- [Set-AzIpGroup](/powershell/module/az.network/set-azipgroup)
- [New-AzFirewallPolicyNetworkRule](/powershell/module/az.network/new-azfirewallpolicynetworkrule)
- [New-AzFirewallPolicyApplicationRule](/powershell/module/az.network/new-azfirewallpolicyapplicationrule)
- [New-AzFirewallPolicyNatRule](/powershell/module/az.network/new-azfirewallpolicynatrule)

## <a name="next-steps"></a>Volgende stappen

- [Zelfstudie: Uw virtuele WAN beveiligen met Azure Firewall Manager](secure-cloud-network.md)