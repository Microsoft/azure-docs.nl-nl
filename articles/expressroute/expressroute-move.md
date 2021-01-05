---
title: 'ExpressRoute: circuits van het klassieke naar het Azure Resource Manager verplaatsen'
description: Meer informatie over wat er gebeurt wanneer u een Azure ExpressRoute-circuit verplaatst van het klassieke naar het Azure Resource Manager-implementatie model.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/15/2020
ms.author: duau
ms.openlocfilehash: dcba2e9de2b37e8c432f94781b3c4c369ad52395
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/29/2020
ms.locfileid: "97807938"
---
# <a name="moving-expressroute-circuits-from-the-classic-to-the-resource-manager-deployment-model"></a>ExpressRoute-circuits verplaatsen van het klassieke naar het Resource Manager-implementatiemodel
Dit artikel bevat een overzicht van wat er gebeurt wanneer u een Azure ExpressRoute-circuit van het klassieke naar het implementatie model van Azure Resource Manager verplaatst.

U kunt één ExpressRoute-circuit gebruiken om virtuele netwerken te verbinden die zijn geïmplementeerd in het klassieke en het Resource Manager-implementatie model.

![Een ExpressRoute-circuit dat via beide implementatiemodellen wordt gekoppeld aan virtuele netwerken](./media/expressroute-move/expressroute-move-1.png)

## <a name="expressroute-circuits-that-are-created-in-the-classic-deployment-model"></a>ExpressRoute-circuits die zijn gemaakt in het klassieke implementatiemodel
ExpressRoute-circuits die zijn gemaakt in het klassieke implementatie model moeten eerst worden gemigreerd naar het Resource Manager-implementatie model. Alleen vervolgens kan de verbinding met zowel het klassieke als het Resource Manager-implementatie model worden ingeschakeld. Connectiviteit wordt niet onderbroken of verstoord wanneer een verbinding wordt verplaatst. Alle verbindingen van circuit naar virtueel netwerk in het klassieke implementatie model binnen hetzelfde abonnement en voor meerdere abonnementen blijven behouden.

Nadat de verplaatsing is voltooid, werkt het ExpressRoute-circuit precies hetzelfde als een ExpressRoute-circuit dat is gemaakt in het Resource Manager-implementatie model. Nu kunt u verbinding maken met virtuele netwerken in het Resource Manager- implementatiemodel.

Nadat u het ExpressRoute-circuit hebt verplaatst naar het Resource Manager-implementatie model, kunt u het niet beheren in het implementatie model van het bron beheer. Bewerkingen voor het beheren van peerings, het bijwerken van eigenschappen van circuits en het verwijderen van circuits zijn alleen beschikbaar via het Resource Manager-implementatie model. Raadpleeg de volgende sectie voor meer informatie over hoe u de toegang tot beide implementatie modellen kunt beheren.

U hoeft uw connectiviteits provider niet te betrekken om uw circuit naar het Resource Manager-implementatie model te verplaatsen.

## <a name="expressroute-circuits-that-are-created-in-the-resource-manager-deployment-model"></a>ExpressRoute-circuits die zijn gemaakt in het Resource Manager-implementatiemodel
U kunt ExpressRoute-circuits die zijn gemaakt in het Resource Manager-implementatiemodel inschakelen voor toegang vanuit beide implementatiemodellen. Elk ExpressRoute-circuit in uw abonnement kan zodanig worden geconfigureerd dat het toegang heeft vanuit beide implementatie modellen.

* ExpressRoute-circuits die zijn gemaakt in het Resource Manager-implementatie model hebben standaard geen toegang tot het klassieke implementatie model.
* ExpressRoute-circuits die zijn verplaatst van het klassieke implementatie model naar het Resource Manager-implementatie model, zijn standaard toegankelijk vanuit beide implementatie modellen.
* Een ExpressRoute-circuit heeft altijd toegang tot het Resource Manager-implementatie model, ongeacht of het is gemaakt in het Resource Manager-of klassieke implementatie model. U kunt verbindingen met virtuele netwerken maken door de instructies te volgen voor [het koppelen van virtuele netwerken](expressroute-howto-linkvnet-arm.md).
* De toegang tot het klassieke implementatiemodel wordt gecontroleerd met de parameter **allowClassicOperations** in het ExpressRoute-circuit.

> [!IMPORTANT]
> Alle quota die zijn beschreven op de pagina [service limits](../azure-resource-manager/management/azure-subscription-service-limits.md) (Servicelimieten) zijn van toepassing. Zo kan een standaardcircuit maximaal 10 virtuele netwerkkoppelingen/-verbindingen tussen zowel het klassieke als het Resource Manager-implementatiemodel hebben.
> 
> 

## <a name="controlling-access-to-the-classic-deployment-model"></a>Toegang tot het klassieke implementatiemodel beheren
U kunt een ExpressRoute-circuit inschakelen om te koppelen aan virtuele netwerken in beide implementatie modellen. Als u dit wilt doen, stelt u de para meter **allowClassicOperations** in op het ExpressRoute-circuit.

Wanneer u **allowClassicOperations** instelt op TRUE, kunt u virtuele netwerken vanuit beide implementatiemodellen koppelen aan het ExpressRoute-circuit. 
* Zie [virtuele netwerken voor het klassieke implementatie model koppelen voor](expressroute-howto-linkvnet-classic.md)het koppelen van virtuele netwerken in het klassieke implementatie model.
* Zie [virtuele netwerken koppelen in het Resource Manager-implementatie model](expressroute-howto-linkvnet-arm.md)om virtuele netwerken in het Resource Manager-implementatie model te koppelen.

Wanneer u **allowClassicOperations** instelt op FALSE, wordt de toegang tot het circuit vanuit het klassieke implementatiemodel geblokkeerd. Alle virtuele netwerken die zijn gekoppeld aan het klassieke implementatie model blijven echter behouden. Het ExpressRoute-circuit is niet zichtbaar in het klassieke implementatie model.

## <a name="supported-operations-in-the-classic-deployment-model"></a>Ondersteunde bewerkingen in het klassieke implementatiemodel
De volgende klassieke bewerkingen worden ondersteund in een ExpressRoute-circuit als **allowClassicOperations** is ingesteld op TRUE:

* ExpressRoute-circuitgegevens verkrijgen
* Koppelingen tussen virtuele netwerken en klassieke virtuele netwerken maken, bijwerken, verkrijgen en verwijderen
* Autorisaties voor koppelingen met virtuele netwerken voor abonnementoverschrijdende connectiviteit maken, bijwerken, verkrijgen en verwijderen

Als **allowClassicOperations** echter is ingesteld op True, kunt u de volgende klassieke bewerkingen niet uitvoeren:

* BGP-peerings (Border Gateway Protocol) maken, bijwerken, verkrijgen of verwijderen voor persoonlijke of openbare Azure-peerings en Microsoft-peerings
* ExpressRoute-circuits verwijderen

## <a name="communication-between-the-classic-and-the-resource-manager-deployment-models"></a>Communicatie tussen het klassieke en het Resource Manager-implementatiemodel
Het ExpressRoute-circuit fungeert als een brug tussen het klassieke en het Resource Manager-implementatiemodel. Verkeer tussen virtuele netwerken voor beide implementatie modellen kan het ExpressRoute-circuit passeren als beide virtuele netwerken zijn gekoppeld aan hetzelfde circuit.

Cumulatieve doorvoer wordt beperkt door de doorvoercapaciteit van de gateway van het virtueel netwerk. Verkeer voert de netwerken van de connectiviteits provider of uw netwerken in dergelijke gevallen niet in. De verkeersstroom tussen de virtuele netwerken vindt volledig plaats in het Microsoft-netwerk.

## <a name="access-to-azure-public-and-microsoft-peering-resources"></a>Toegang tot resources in openbare Azure-peering en Microsoft-peering
U blijft zonder onderbreking toegang houden tot resources die doorgaans toegankelijk zijn via openbare Azure-peering en Microsoft-peering.  

## <a name="whats-supported"></a>Wat wordt er ondersteund
In deze sectie wordt beschreven wat er wordt ondersteund voor ExpressRoute-circuits:

* U kunt één ExpressRoute-circuit gebruiken voor toegang tot virtuele netwerken die zijn geïmplementeerd in het klassieke en het Resource Manager-implementatiemodel.
* U kunt een ExpressRoute-circuit verplaatsen van het klassieke naar het Resource Manager-implementatiemodel Nadat het ExpressRoute-circuit is verplaatst, blijft het werken zoals elk ander ExpressRoute-circuit dat is gemaakt in het Resource Manager-implementatie model.
* U kunt alleen het ExpressRoute-circuit verplaatsen. Met deze bewerking kunt u geen circuitkoppelingen, virtuele netwerken of VPN-gateways verplaatsen.
* Wanneer een ExpressRoute-circuit is verplaatst naar het Resource Manager-implementatiemodel, kunt u de levenscyclus van het ExpressRoute-circuit alleen beheren met het Resource Manager-implementatiemodel. Dit betekent dat u bewerkingen kunt uitvoeren zoals het toevoegen/bijwerken/verwijderen van peerings, het bijwerken van eigenschappen van circuits (zoals band breedte, SKU en facturerings type) en het verwijderen van circuits alleen in het Resource Manager-implementatie model.
* Het ExpressRoute-circuit fungeert als een brug tussen het klassieke en het Resource Manager-implementatiemodel. Verkeer tussen virtuele machines in klassieke virtuele netwerken en virtuele machines in virtuele netwerken van Resource Manager kan communiceren via ExpressRoute als beide virtuele netwerken zijn gekoppeld aan hetzelfde ExpressRoute-circuit.
* Abonnementoverschrijdende connectiviteit wordt ondersteund in zowel het klassieke als het Resource Manager-implementatiemodel.
* Nadat u een ExpressRoute-circuit uit het klassieke model naar het model van Azure Resource Manager hebt verplaatst, kunt u [de virtuele netwerken die zijn gekoppeld aan het ExpressRoute-circuit migreren](expressroute-migration-classic-resource-manager.md).

## <a name="whats-not-supported"></a>Wat wordt er niet ondersteund
In deze sectie wordt beschreven wat er niet wordt ondersteund voor ExpressRoute-circuits:

* Het beheer van de levenscyclus van een ExpressRoute-circuit vanuit het klassieke implementatiemodel.
* Azure RBAC-ondersteuning (op rollen gebaseerd toegangs beheer) voor het klassieke implementatie model. U kunt geen Azure RBAC-besturings elementen uitvoeren op een circuit in het klassieke implementatie model. Een beheerder/co-beheerder van het abonnement kan virtuele netwerken koppelen aan of loskoppelen van het circuit.

## <a name="configuration"></a>Configuratie
Volg de instructies in [Een ExpressRoute-circuit verplaatsen van het klassieke naar het Resource Manager-implementatiemodel](expressroute-howto-move-arm.md).

## <a name="next-steps"></a>Volgende stappen
* [De virtuele netwerken die zijn gekoppeld aan het ExpressRoute-circuit uit het klassieke model migreren naar het model van Azure Resource Manager](expressroute-migration-classic-resource-manager.md)
* Voor informatie over werkstromen raadpleegt u [ExpressRoute circuit provisioning workflows and circuit states](expressroute-workflows.md) (Werkstromen voor de inrichting van ExpressRoute-circuits en circuittoestanden).
* Ga als volgt te werk om uw ExpressRoute-verbinding te configureren:
  
  * [Een ExpressRoute-circuit maken](expressroute-howto-circuit-arm.md)
  * [Routering configureren](expressroute-howto-routing-arm.md)
  * [Een virtueel netwerk koppelen aan een ExpressRoute-circuit](expressroute-howto-linkvnet-arm.md)

