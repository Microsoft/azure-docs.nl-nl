---
title: Voor voegsel van openbaar IP-adres voor Azure
description: Meer informatie over wat een voor voegsel van een openbaar IP-adres voor Azure is en hoe u het kunt gebruiken om voorspel bare, openbaar IP-adressen aan uw resources toe te wijzen.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/29/2020
ms.author: allensu
ms.openlocfilehash: 3d98107075c215c0a00bc2035325a6ad8bf5bfc5
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98222425"
---
# <a name="public-ip-address-prefix"></a>Voorvoegsel van openbaar IP-adres

Een voor voegsel van een openbaar IP-adres is een gereserveerd bereik van IP-adressen in Azure. Azure biedt een aaneengesloten reeks adressen voor uw abonnement op basis van het aantal dat u opgeeft. 

Zie [open bare IP-adressen](./public-ip-addresses.md#public-ip-addresses) als u niet bekend bent met open bare adressen.

Open bare IP-adressen worden toegewezen uit een groep adressen in elke Azure-regio. U kunt de lijst met bereiken [downloaden](https://www.microsoft.com/download/details.aspx?id=56519) die Azure gebruikt voor elke regio. 40.121.0.0/16 is bijvoorbeeld een van meer dan 100 bereiken die Azure gebruikt in de regio VS-Oost. Het bereik bevat de bruikbare adressen van 40.121.0.1-40.121.255.254.

U maakt een voor voegsel van een openbaar IP-adres in een Azure-regio en-abonnement door een naam op te geven en te bepalen hoeveel adressen het voor voegsel moet bevatten. 

Open bare IP-adresbereiken worden toegewezen met een voor voegsel van uw keuze. Als u een voor voegsel van/28 maakt, geeft Azure 16 IP-adressen van een van de bereiken.

U weet niet welk bereik Azure er moet worden toegewezen totdat u het bereik maakt, maar de adressen aaneengesloten zijn. 

Voor voegsels voor open bare IP-adressen gelden kosten voor [openbaar IP-adres](https://azure.microsoft.com/pricing/details/ip-addresses).

## <a name="why-create-a-public-ip-address-prefix"></a>Waarom een openbaar IP-adres voorvoegsel maken?

Wanneer u resources met een openbaar IP-adres maakt, wijst Azure een beschikbaar openbaar IP-adres toe uit een van de bereiken die in die regio worden gebruikt. 

Totdat het IP-adres is toegewezen aan Azure, weet u niet precies wat het IP-adressen is. Dit proces kan problematisch zijn wanneer u firewall regels maakt waarmee specifieke IP-adressen zijn toegestaan. Voor elk toegevoegd IP-adres moet een bijbehorende firewall regel worden toegevoegd.

Wanneer u adressen aan uw resources toewijst vanuit een openbaar IP-adres voorvoegsel, zijn updates van de firewall regel niet vereist. Het hele bereik wordt toegevoegd aan de regel.

## <a name="benefits"></a>Voordelen

- Het maken van open bare IP-adres bronnen van een bekend bereik.
- Configuratie van de firewall regel met bereiken met een openbaar IP-adres dat u momenteel hebt toegewezen en de adressen die u nog niet hebt toegewezen. Deze configuratie elimineert de nood zaak om firewall regels te wijzigen wanneer u IP-adressen aan nieuwe resources toewijst.
- De standaard grootte van een bereik dat u kunt maken, is/28 of 16 IP-adressen.
- Er zijn geen beperkingen voor het aantal bereiken dat u kunt maken. Er gelden limieten voor het maximum aantal statische open bare IP-adressen dat u kunt hebben in een Azure-abonnement. Het aantal bereiken dat u maakt, mag niet meer statische open bare IP-adressen bevatten dan u in uw abonnement kunt hebben. Zie [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)voor meer informatie.
- De adressen die u maakt met behulp van adressen uit het voor voegsel, kunnen worden toegewezen aan elke Azure-resource waaraan u een openbaar IP-adres kunt toewijzen.
- U kunt eenvoudig zien welke IP-adressen worden vermeld en niet binnen het bereik vallen.

## <a name="scenarios"></a>Scenario's
U kunt de volgende resources koppelen aan een statisch openbaar IP-adres van een voor voegsel:

|Resource|Scenario|Stappen|
|---|---|---|
|Virtuele machines| Het koppelen van open bare Ip's van een voor voegsel aan uw virtuele machines in azure reduceert de overhead van beheer bij het toevoegen van IP-adressen aan een lijst met toegestane apparaten in de firewall. U kunt een volledig voor voegsel toevoegen met één firewall regel. Wanneer u schaalt met virtuele machines in azure, kunt u IP-adressen koppelen aan hetzelfde voor voegsel kosten, tijd en beheer overhead besparen.| IP-adressen van een voor voegsel aan uw virtuele machine koppelen: </br> 1. [Maak een voor voegsel.](manage-public-ip-address-prefix.md) </br> 2. [Maak een IP-adres van het voor voegsel.](manage-public-ip-address-prefix.md) </br> 3. [Koppel het IP-adres aan de netwerk interface van de virtuele machine.](virtual-network-network-interface-addresses.md#add-ip-addresses) </br> U kunt [de IP-adressen ook koppelen aan een schaalset voor virtuele machines](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/).
| Standaard load balancers | Het koppelen van open bare Ip's van een voor voegsel aan uw frontend-IP-configuratie of de regel voor uitgaande verbindingen van een load balancer zorgt voor vereenvoudiging van uw open bare IP-adres ruimte van Azure. Vereenvoudig uw scenario door uitgaande verbindingen van een bereik van aaneengesloten IP-adressen op te schonen. | IP-adressen van een voor voegsel aan uw load balancer koppelen: </br> 1. [Maak een voor voegsel.](manage-public-ip-address-prefix.md) </br> 2. [Maak een IP-adres van het voor voegsel.](manage-public-ip-address-prefix.md) </br> 3. Wanneer u de load balancer maakt, moet u het IP-adres dat u in stap 2 hierboven hebt gemaakt, selecteren of bijwerken als het frontend-IP-adres van uw load balancer. |
| Azure Firewall | U kunt een openbaar IP-adres gebruiken uit een voor voegsel voor uitgaande SNAT. Al het uitgaande virtuele netwerk verkeer wordt omgezet naar het [Azure firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) open bare IP-adres. | Een IP-adres uit een voor voegsel aan uw firewall koppelen: </br> 1. [Maak een voor voegsel.](manage-public-ip-address-prefix.md) </br> 2. [Maak een IP-adres van het voor voegsel.](manage-public-ip-address-prefix.md) </br> 3. Wanneer u [de Azure-firewall implementeert](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall), moet u ervoor zorgen dat u het IP-adres selecteert dat u eerder van het voor voegsel hebt gekregen.|
| Toepassingsgateway v2 | U kunt een openbaar IP-adres gebruiken uit een voor voegsel voor uw automatisch schalen en een zone-redundante toepassings gateway v2. | Een IP-adres uit een voor voegsel aan uw gateway koppelen: </br> 1. [Maak een voor voegsel.](manage-public-ip-address-prefix.md) </br> 2. [Maak een IP-adres van het voor voegsel.](manage-public-ip-address-prefix.md) </br> 3. Wanneer u [de Application Gateway implementeert](../application-gateway/quick-create-portal.md#create-an-application-gateway), moet u ervoor zorgen dat u het IP-adres selecteert dat u eerder van het voor voegsel hebt gekregen.|

## <a name="constraints"></a>Beperkingen

- U kunt de IP-adressen voor het voor voegsel niet opgeven. Azure geeft de IP-adressen voor het voor voegsel, op basis van de grootte die u opgeeft.
- U kunt standaard een voor voegsel van Maxi maal 16 IP-adressen of een/28 maken. Bekijk de [netwerk limieten verhogen aanvragen](../azure-portal/supportability/networking-quota-requests.md) en [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) voor meer informatie.
- U kunt het bereik niet meer wijzigen als u het voor voegsel hebt gemaakt.
- Alleen statische open bare IP-adressen die zijn gemaakt met de standaard-SKU kunnen worden toegewezen vanuit het bereik van het voor voegsel. Zie [openbaar IP-adres](./public-ip-addresses.md#public-ip-addresses)voor meer informatie over open bare IP-adres-sku's.
- Adressen van het bereik kunnen alleen worden toegewezen aan Azure Resource Manager resources. Adressen kunnen niet worden toegewezen aan resources in het klassieke implementatie model.
- Alle open bare IP-adressen die zijn gemaakt op basis van het voor voegsel, moeten zich in dezelfde Azure-regio en hetzelfde abonnement bevinden als het voor voegsel. Adressen moeten worden toegewezen aan resources in dezelfde regio en hetzelfde abonnement.
- U kunt een voor voegsel niet verwijderen als er adressen in de service zijn toegewezen aan open bare IP-adres resources die zijn gekoppeld aan een resource. Koppel alle open bare IP-adres bronnen die IP-adressen aan het voor voegsel zijn toegewezen eerst.


## <a name="next-steps"></a>Volgende stappen

- Een voor voegsel voor een openbaar IP-adres [maken](manage-public-ip-address-prefix.md)