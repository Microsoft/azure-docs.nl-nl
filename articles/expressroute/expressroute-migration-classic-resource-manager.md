---
title: 'Azure ExpressRoute: klassieke VNets migreren naar Resource Manager'
description: Op deze pagina wordt beschreven hoe u ExpressRoute-gekoppelde virtuele netwerken naar Resource Manager migreert nadat u het circuit hebt verplaatst.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 02/06/2020
ms.author: duau
ms.openlocfilehash: 57c439cf8ac52d93d231d6ff33f72a5a942dec6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96351602"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>ExpressRoute-gekoppelde virtuele netwerken migreren van klassiek naar Resource Manager

In dit artikel wordt uitgelegd hoe u ExpressRoute-gekoppelde virtuele netwerken vanuit het klassieke implementatie model naar het Azure Resource Manager-implementatie model migreert nadat u het ExpressRoute-circuit hebt verplaatst. 

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Controleer of u de nieuwste versies van de Azure PowerShell-modules hebt. Zie [Azure PowerShell installeren en configureren](/powershell/azure/) voor meer informatie. Zie [de Azure PowerShell service management-module installeren](/powershell/azure/servicemanagement/install-azure-ps)voor het installeren van de Power shell-module voor Service beheer (die nodig is voor het klassieke implementatie model).
* Zorg ervoor dat [u de vereisten](expressroute-prerequisites.md), [routerings behoeften](expressroute-routing.md)en [werk stromen](expressroute-workflows.md) hebt gecontroleerd voordat u begint met de configuratie.
* Lees de informatie die wordt weer gegeven bij het [verplaatsen van een ExpressRoute-circuit van klassiek naar Resource Manager](expressroute-move.md). Zorg ervoor dat u de limieten en beperkingen volledig begrijpt.
* Controleer of het circuit volledig operationeel is in het klassieke implementatie model.
* Zorg ervoor dat u een resource groep hebt die is gemaakt in het Resource Manager-implementatie model.
* Raadpleeg de volgende documentatie over resource migratie:

    * [Platformondersteunde migratie van IaaS-resources van het klassieke implementatiemodel naar Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
    * [Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-deep-dive.md)
    * [Veelgestelde vragen: door het platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-faq.md)
    * [Bekijk de meeste veelvoorkomende migratie fouten en-oplossingen](../virtual-machines/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Ondersteunde en niet-ondersteunde scenario's

* Een ExpressRoute-circuit kan worden verplaatst van het klassieke naar de Resource Manager-omgeving zonder uitval tijd. U kunt elk ExpressRoute-circuit verplaatsen van het klassieke naar de Resource Manager-omgeving zonder uitval tijd. Volg de instructies in [ExpressRoute-circuits verplaatsen van het klassieke naar het Resource Manager-implementatie model met behulp van Power shell](expressroute-howto-move-arm.md). Dit is een vereiste voor het verplaatsen van resources die zijn verbonden met het virtuele netwerk.
* Virtuele netwerken, gateways en gekoppelde implementaties binnen het virtuele netwerk die zijn gekoppeld aan een ExpressRoute-circuit in hetzelfde abonnement, kunnen worden gemigreerd naar de Resource Manager-omgeving zonder uitval tijd. U kunt de stappen die u later hebt beschreven volgen om bronnen te migreren, zoals virtuele netwerken, gateways en virtuele machines die in het virtuele netwerk zijn geïmplementeerd. U moet ervoor zorgen dat de virtuele netwerken correct zijn geconfigureerd voordat ze worden gemigreerd. 
* Virtuele netwerken, gateways en gekoppelde implementaties in het virtuele netwerk die zich niet in hetzelfde abonnement bevinden als het ExpressRoute-circuit, vereisen enige downtime om de migratie te volt ooien. In de laatste sectie van het document worden de stappen beschreven die moeten worden gevolgd om resources te migreren.
* Een virtueel netwerk met zowel de ExpressRoute-gateway als de VPN Gateway kan niet worden gemigreerd.
* Migratie van ExpressRoute-circuit voor meerdere abonnementen wordt niet ondersteund. Zie [ondersteuning voor micro soft. netwerk verplaatsing](../azure-resource-manager/management/move-support-resources.md#microsoftnetwork)voor meer informatie.

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Een ExpressRoute-circuit verplaatsen van klassiek naar Resource Manager
U moet een ExpressRoute-circuit verplaatsen van het klassieke naar de Resource Manager-omgeving voordat u probeert om resources te migreren die zijn gekoppeld aan het ExpressRoute-circuit. Als u deze taak wilt uitvoeren, raadpleegt u de volgende artikelen:

* Lees de informatie die wordt weer gegeven bij het [verplaatsen van een ExpressRoute-circuit van klassiek naar Resource Manager](expressroute-move.md).
* [Verplaats een circuit van klassiek naar Resource Manager met behulp van Azure PowerShell](expressroute-howto-move-arm.md).
* Gebruik de Azure-Service beheer Portal. U kunt de werk stroom volgen om [een nieuw ExpressRoute-circuit te maken](expressroute-howto-circuit-portal-resource-manager.md) en de optie importeren te selecteren. 

Voor deze bewerking is geen downtime vereist. U kunt door gaan met het overdragen van gegevens tussen uw locatie en micro soft terwijl de migratie wordt uitgevoerd.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Virtuele netwerken, gateways en gekoppelde implementaties migreren

De stappen die u moet volgen, zijn afhankelijk van het feit of uw resources zich in hetzelfde abonnement, verschillende abonnementen of beide bevinden.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migreer virtuele netwerken, gateways en gekoppelde implementaties in hetzelfde abonnement als het ExpressRoute-circuit
In deze sectie worden de stappen beschreven voor het migreren van een virtueel netwerk, een gateway en gekoppelde implementaties in hetzelfde abonnement als het ExpressRoute-circuit. Er is geen downtime gekoppeld aan deze migratie. U kunt alle bronnen blijven gebruiken via het migratie proces. Het beheer vlak wordt vergrendeld terwijl de migratie wordt uitgevoerd. 

1. Zorg ervoor dat het ExpressRoute-circuit is verplaatst van het klassieke naar de Resource Manager-omgeving.
2. Zorg ervoor dat het virtuele netwerk op de juiste wijze is voor bereid voor de migratie.
3. Registreer uw abonnement voor resource migratie. Als u uw abonnement voor resource migratie wilt registreren, gebruikt u het volgende Power shell-fragment:

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Valideren, voorbereiden en migreren. Als u het virtuele netwerk wilt verplaatsen, gebruikt u het volgende Power shell-fragment:

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   U kunt de migratie ook afbreken door de volgende Power shell-cmdlet uit te voeren:

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>Volgende stappen
* [Platformondersteunde migratie van IaaS-resources van het klassieke implementatiemodel naar Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-overview.md)
* [Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-deep-dive.md)
* [Veelgestelde vragen: door het platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](../virtual-machines/migration-classic-resource-manager-faq.md)
* [Bekijk de meeste veelvoorkomende migratie fouten en-oplossingen](../virtual-machines/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)