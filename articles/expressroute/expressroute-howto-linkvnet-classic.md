---
title: 'Azure-ExpressRoute: een VNet koppelen aan een circuit: klassiek'
description: Dit document bevat een overzicht van hoe u virtuele netwerken (VNets) koppelt aan ExpressRoute-circuits met behulp van het klassieke implementatie model en Power shell.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2019
ms.author: duau
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 0e8a1d48934d73f1035c6f2c4c76e56d9a0e4052
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92206967"
---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit-using-powershell-classic"></a>Een virtueel netwerk verbinden met een ExpressRoute-circuit met behulp van Power shell (klassiek)
> [!div class="op_single_selector"]
> * [Azure-portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Azure-CLI](howto-linkvnet-cli.md)
> * [Video: Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klassiek)](expressroute-howto-linkvnet-classic.md)
>

In dit artikel vindt u informatie over het koppelen van virtuele netwerken (VNets) aan Azure ExpressRoute-circuits met behulp van Power shell. Eén VNet kan aan maximaal vier ExpressRoute-circuits worden gekoppeld. Volg de stappen in dit artikel om een nieuwe koppeling te maken naar elk ExpressRoute-circuit waarmee u verbinding maakt. De ExpressRoute-circuits kunnen zich in hetzelfde abonnement, in verschillende abonnementen of in een combinatie van beide bevinden. Dit artikel is van toepassing op virtuele netwerken die zijn gemaakt met het klassieke implementatie model.

U kunt Maxi maal 10 virtuele netwerken koppelen aan een ExpressRoute-circuit. Alle virtuele netwerken moeten zich in dezelfde geopolitieke regio bevinden. U kunt een groter aantal virtuele netwerken koppelen aan uw ExpressRoute-circuit of virtuele netwerken koppelen die zich in andere geopolitieke regio's bevinden als u de ExpressRoute Premium-invoeg toepassing inschakelt. Raadpleeg de [Veelgestelde vragen](expressroute-faqs.md) voor meer informatie over de Premium-invoeg toepassing.

[!INCLUDE [expressroute-classic-end-include](../../includes/expressroute-classic-end-include.md)]

**Over Azure-implementatiemodellen**

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## <a name="configuration-prerequisites"></a>Configuratievereisten

* Bekijk de [vereisten](expressroute-prerequisites.md), de [routeringsvereisten](expressroute-routing.md) en de [werkstromen](expressroute-workflows.md) voordat u begint met de configuratie.
* U moet een actief ExpressRoute-circuit hebben.
   * Volg de instructies voor het [maken van een ExpressRoute-circuit](expressroute-howto-circuit-classic.md) en laat uw connectiviteits provider het circuit inschakelen.
   * Zorg ervoor dat u persoonlijke Azure-peering voor uw circuit hebt geconfigureerd. Zie het artikel [route ring configureren](expressroute-howto-routing-classic.md) voor instructies voor route ring.
   * Zorg ervoor dat persoonlijke Azure-peering is geconfigureerd en dat de BGP-peering tussen uw netwerk en micro soft actief is zodat u end-to-end connectiviteit kunt inschakelen.
   * U moet een virtueel netwerk en een gateway voor het virtuele netwerk hebben gemaakt en volledig zijn ingericht. Volg de instructies voor het [configureren van een virtueel netwerk voor ExpressRoute](./expressroute-howto-add-gateway-portal-resource-manager.md).

### <a name="download-the-latest-powershell-cmdlets"></a>De meest recente Power shell-cmdlets downloaden

[!INCLUDE [classic powershell install instructions](../../includes/expressroute-poweshell-classic-install-include.md)]

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Een virtueel netwerk in hetzelfde abonnement verbinden met een circuit
U kunt een virtueel netwerk koppelen aan een ExpressRoute-circuit met behulp van de volgende cmdlet. Zorg ervoor dat de virtuele netwerk gateway is gemaakt en gereed is voor koppeling voordat u de cmdlet uitvoert.

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
Provisioned
```
    
## <a name="remove-a-virtual-network-link-to-a-circuit"></a>Een koppeling met een virtueel netwerk naar een circuit verwijderen
U kunt een koppeling van een virtueel netwerk met een ExpressRoute-circuit verwijderen met de volgende cmdlet. Zorg ervoor dat het huidige abonnement is geselecteerd voor het opgegeven virtuele netwerk. 

```powershell
Remove-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```
 

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Een virtueel netwerk in een ander abonnement verbinden met een circuit
U kunt een ExpressRoute-circuit delen tussen meerdere abonnementen. De volgende afbeelding is een schematische weergave van hoe delen tussen meerdere abonnementen werkt voor ExpressRoute-circuits.

Elk van de kleinere clouds in de grote cloud staan voor abonnementen die tot verschillende afdelingen binnen een organisatie behoren. Elk van de afdelingen in de organisatie kan hun eigen abonnement gebruiken voor het implementeren van hun services, maar de afdelingen kunnen een enkel ExpressRoute-circuit delen om verbinding te maken met uw on-premises netwerk. Eén afdeling (in dit voorbeeld: IT) kan eigenaar zijn van het ExpressRoute-circuit. Andere abonnementen binnen de organisatie kunnen het ExpressRoute-circuit gebruiken.

> [!NOTE]
> Kosten voor connectiviteit- en bandbreedte voor het toegewezen circuit zijn in rekening gebracht bij de eigenaar van het ExpressRoute-circuit. Alle virtuele netwerken delen dezelfde bandbreedte.
> 
> 

![Connectiviteit tussen abonnementen](./media/expressroute-howto-linkvnet-classic/cross-subscription.png)

### <a name="administration"></a>Beheer
De *eigenaar* van het circuit is de beheerder/beheerder van het abonnement waarin het ExpressRoute-circuit is gemaakt. De eigenaar van het circuit kan beheerders/mede beheerders van andere abonnementen, zoals *circuit gebruikers* genoemd, autoriseren voor het gebruik van het toegewezen circuit waarvan ze eigenaar zijn. Circuit gebruikers die gemachtigd zijn om het ExpressRoute-circuit van de organisatie te gebruiken, kunnen het virtuele netwerk in hun abonnement koppelen aan het ExpressRoute-circuit nadat ze zijn geautoriseerd.

De circuiteigenaar heeft de bevoegdheid om autorisaties op elk gewenst moment te wijzigen en in te trekken. Als u een autorisatie intrekt, worden alle koppelingen verwijderd uit het abonnement waarvan de toegang is ingetrokken.

### <a name="circuit-owner-operations"></a>Bewerkingen door circuiteigenaars

**Een autorisatie maken**

De eigenaar van het circuit geeft de beheerders van andere abonnementen toestemming om het opgegeven circuit te gebruiken. In het volgende voor beeld is de beheerder van het circuit (Contoso IT) de beheerder van een ander abonnement (dev-test) in staat om Maxi maal twee virtuele netwerken aan het circuit te koppelen. De contoso IT-beheerder kan dit doen door de Dev-Test micro soft-ID op te geven. De cmdlet verzendt geen e-mail bericht naar de opgegeven micro soft-ID. De eigenaar van het circuit moet expliciet de andere eigenaar van het abonnement melden dat de autorisatie is voltooid.

```powershell
New-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -Description "Dev-Test Links" -Limit 2 -MicrosoftIds 'devtest@contoso.com'
```

  retourneren:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : **********************************
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Autorisaties controleren**

De circuiteigenaar kan alle autorisaties controleren die voor een bepaald circuit worden uitgegeven, door de volgende cmdlet uit te voeren:

```powershell
Get-AzureDedicatedCircuitLinkAuthorization -ServiceKey: "**************************"
```
  retourneren:

  ```powershell
  Description         : EngineeringTeam
  Limit               : 3
  LinkAuthorizationId : ####################################
  MicrosoftIds        : engadmin@contoso.com
  Used                : 1

  Description         : MarketingTeam
  Limit               : 1
  LinkAuthorizationId : @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
  MicrosoftIds        : marketingadmin@contoso.com
  Used                : 0

  Description         : Dev-Test Links
  Limit               : 2
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : salesadmin@contoso.com
  Used                : 2
  ```

**Autorisaties bijwerken**

De eigenaar van het circuit kan autorisaties wijzigen met behulp van de volgende cmdlet:

```powershell
Set-AzureDedicatedCircuitLinkAuthorization -ServiceKey "**************************" -AuthorizationId "&&&&&&&&&&&&&&&&&&&&&&&&&&&&"-Limit 5
```

  retourneren:

  ```powershell
  Description         : Dev-Test Links
  Limit               : 5
  LinkAuthorizationId : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  MicrosoftIds        : devtest@contoso.com
  Used                : 0
  ```

**Autorisaties verwijderen**

De circuiteigenaar kan autorisaties intrekken/verwijderen door de volgende cmdlet uit te voeren:

```powershell
Remove-AzureDedicatedCircuitLinkAuthorization -ServiceKey "*****************************" -AuthorizationId "###############################"
```

### <a name="circuit-user-operations"></a>Bewerkingen door circuitgebruikers

**Autorisaties controleren**

De circuit gebruiker kan autorisaties controleren met behulp van de volgende cmdlet:

```powershell
Get-AzureAuthorizedDedicatedCircuit
```

  retourneren:

  ```powershell
  Bandwidth                        : 200
  CircuitName                      : ContosoIT
  Location                         : Washington DC
  MaximumAllowedLinks              : 2
  ServiceKey                       : &&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&&
  ServiceProviderName              : equinix
  ServiceProviderProvisioningState : Provisioned
  Status                           : Enabled
  UsedLinks                        : 0
  ```

**Koppelings autorisaties worden ingewisseld**

De circuitgebruiker kan de volgende cmdlet uitvoeren om een autorisatie voor een koppeling in te wisselen:

```powershell
New-AzureDedicatedCircuitLink –servicekey "&&&&&&&&&&&&&&&&&&&&&&&&&&" –VnetName 'SalesVNET1'
```

  retourneren:

  ```powershell
  State VnetName
  ----- --------
  Provisioned SalesVNET1
  ```

Voer deze opdracht uit in het zojuist gekoppelde abonnement voor het virtuele netwerk:

```powershell
New-AzureDedicatedCircuitLink -ServiceKey "*****************************" -VNetName "MyVNet"
```

## <a name="next-steps"></a>Volgende stappen

Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md)voor meer informatie over ExpressRoute.