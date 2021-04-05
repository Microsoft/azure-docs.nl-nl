---
title: 'Quickstart: Een circuit maken en wijzigen met ExpressRoute - Azure PowerShell'
description: In deze quickstart leert u hoe u een ExpressRoute-circuit kunt maken, inrichten, controleren, verwijderen en de inrichting ervan ongedaan kunt maken.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: quickstart
ms.date: 10/12/2020
ms.author: duau
ms.openlocfilehash: 5397dd2745a0d4e61804cf631014846ae15ec4e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91971537"
---
# <a name="quickstart-create-and-modify-an-expressroute-circuit-using-azure-powershell"></a>Quickstart: Een ExpressRoute-circuit maken en wijzigen met behulp van de Azure PowerShell

Deze quickstart laat zien hoe u een ExpressRoute-circuit maakt met behulp van PowerShell-cmdlets en het Azure Resource Manager-implementatiemodel. U kunt ook de status van een circuit controleren, een circuit bijwerken of verwijderen, of de inrichting ervan ongedaan maken.

## <a name="prerequisites"></a>Vereisten

* Bekijk de [vereisten](expressroute-prerequisites.md) en [werkstromen](expressroute-workflows.md) voordat u begint met de configuratie.
* Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure PowerShell lokaal geïnstalleerd of Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-and-provision-an-expressroute-circuit"></a><a name="create"></a>Een ExpressRoute-circuit maken en inrichten
### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Aanmelden bij uw Azure-account en uw abonnement selecteren

[!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

### <a name="get-the-list-of-supported-providers-locations-and-bandwidths"></a>De lijst met ondersteunde providers, locaties en bandbreedten ophalen
U kunt pas een ExpressRoute-circuit maken als u beschikt over de lijst met ondersteunde connectiviteitsproviders, locaties en bandbreedte-opties.

De PowerShell-cmdlet **Get-AzExpressRouteServiceProvider** retourneert deze informatie, die u in latere stappen gaat gebruiken:

```azurepowershell-interactive
Get-AzExpressRouteServiceProvider
```

Controleer deze om te zien of uw connectiviteitsprovider wordt vermeld. Noteer de volgende gegevens, aangezien u die later nodig hebt bij het maken van een circuit:

* Naam
* PeeringLocations
* BandwidthsOffered

U bent nu klaar om een ExpressRoute-circuit te maken.

### <a name="create-an-expressroute-circuit"></a>Een ExpressRoute-circuit maken
Als u nog geen resourcegroep hebt, moet u er een maken voordat u het ExpressRoute-circuit maakt. U doet dit door de volgende opdracht uit te voeren:

```azurepowershell-interactive
New-AzResourceGroup -Name "ExpressRouteResourceGroup" -Location "West US"
```

In het volgende voorbeeld ziet u hoe u een ExpressRoute-circuit van 200 Mbps maakt met behulp van Equinix in Silicon Valley. Als u een andere provider en andere instellingen gebruikt, vervangt u de gegevens uit het voorbeeld door uw gegevens wanneer u uw aanvraag verstuurt. Gebruik het volgende voorbeeld om een nieuwe servicesleutel aan te vragen:

```azurepowershell-interactive
New-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup" -Location "West US" -SkuTier Standard -SkuFamily MeteredData -ServiceProviderName "Equinix" -PeeringLocation "Silicon Valley" -BandwidthInMbps 200
```

Zorg ervoor dat u de juiste SKU-categorie en SKU-familie opgeeft:

* De SKU-categorie bepaalt of een ExpressRoute-circuit [Local](expressroute-faqs.md#expressroute-local), Standard of [Premium](expressroute-faqs.md#expressroute-premium) is. U kunt *Local*, *Standard of *Premium* opgeven. U kunt de SKU niet wijzigen van *Standard/Premium* in *Local*.
* De SKU-familie bepaalt het type facturering. U kunt *MeteredData* opgeven voor een data-abonnement met een datalimiet en *UnlimitedData* voor een onbeperkt data-abonnement. U kunt het type facturering wijzigen van *MeteredData* in *UnlimitedData*, maar niet van *UnlimitedData* in *MeteredData*. Een *Local* circuit kan alleen *UnlimitedData* zijn.

> [!IMPORTANT]
> Vanaf het moment dat een servicesleutel is uitgegeven, worden er kosten voor een ExpressRoute-circuit in rekening gebracht. Zorg ervoor dat u deze bewerking uitvoert wanneer de connectiviteitsprovider gereed is om het circuit in te richten.
>

Het antwoord bevat de servicesleutel. U kunt gedetailleerde beschrijvingen van alle parameters opvragen door de volgende opdracht uit te voeren:

```azurepowershell-interactive
get-help New-AzExpressRouteCircuit -detailed
```


### <a name="list-all-expressroute-circuits"></a>Alle ExpressRoute-circuits opvragen
Als u een lijst wilt weergeven met alle ExpressRoute-circuits die u hebt gemaakt, voert u de opdracht **Get-AzExpressRouteCircuit** uit:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Het antwoord ziet er ongeveer uit als in het volgende voorbeeld:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState          : Enabled
ServiceProviderProvisioningState  : NotProvisioned
ServiceProviderNotes              :
ServiceProviderProperties         : {
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                        : **************************************
Peerings                          : []
```

U kunt deze informatie op elk gewenst moment opvragen met behulp van de cmdlet `Get-AzExpressRouteCircuit`. Als u de aanroep gebruikt zonder parameters, worden alle circuits weergegeven. Uw servicesleutel wordt vermeld in het veld *ServiceKey*:

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```

Het antwoord ziet er ongeveer uit als in het volgende voorbeeld:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : NotProvisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

### <a name="send-the-service-key-to-your-connectivity-provider-for-provisioning"></a>De servicesleutel voor inrichting verzenden naar de connectiviteitsprovider
*ServiceProviderProvisioningState* bevat informatie over de huidige status van de inrichting aan de zijde van de serviceprovider. De status duidt de status aan de zijde van Microsoft aan. Zie [Werkstromen](expressroute-workflows.md#expressroute-circuit-provisioning-states) voor meer informatie over de inrichtingsstatussen van circuits.

Wanneer u een nieuw ExpressRoute-circuit maakt, heeft het circuit de volgende status:

```azurepowershell
ServiceProviderProvisioningState : NotProvisioned
CircuitProvisioningState         : Enabled
```

Wanneer de connectiviteitsprovider bezig is met het inschakelen van uw circuit, verandert de status van het circuit in:

```azurepowershell
ServiceProviderProvisioningState : Provisioning
Status                           : Enabled
```

Als u het ExpressRoute-circuit wilt gebruiken, moet het de volgende status hebben:

```azurepowershell
ServiceProviderProvisioningState : Provisioned
CircuitProvisioningState         : Enabled
```

### <a name="periodically-check-the-status-and-the-state-of-the-circuit-key"></a>Controleer regelmatig de circuitstatus en de status van de circuitsleutel
Als u de circuitstatus en de status van de servicesleutel controleert, kunt u zien wanneer uw provider uw circuit heeft ingericht. Nadat het circuit is geconfigureerd, wordt *ServiceProviderProvisioningState* weergegeven als *Provisioned*, zoals wordt weergegeven in het volgende voorbeeld:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Het antwoord ziet er ongeveer uit als in het volgende voorbeeld:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                    "ServiceProviderName": "Equinix",
                                    "PeeringLocation": "Silicon Valley",
                                    "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

### <a name="create-your-routing-configuration"></a>De routeringsconfiguratie maken
Raadpleeg het artikel over [de routeringsconfiguratie voor ExpressRoute-circuits](expressroute-howto-routing-arm.md) voor stapsgewijze instructies over het maken en wijzigen van circuitpeerings.

> [!IMPORTANT]
> Deze instructies zijn alleen van toepassing op circuits die zijn gemaakt met serviceproviders die services met laag-2-connectiviteit aanbieden. Als u gebruikmaakt van een serviceprovider die beheerde laag-3-services aanbiedt (meestal een IP-adres voor VPN, zoals MPLS), wordt de routering voor u geconfigureerd en beheerd via de connectiviteitsprovider.
>

### <a name="link-a-virtual-network-to-an-expressroute-circuit"></a>Een virtueel netwerk koppelen aan een ExpressRoute-circuit
Koppel vervolgens een virtueel netwerk aan een ExpressRoute-circuit. Gebruik het artikel [Virtuele netwerken koppelen aan ExpressRoute-circuits](expressroute-howto-linkvnet-arm.md) wanneer u werkt met het Resource Manager-implementatiemodel.

## <a name="getting-the-status-of-an-expressroute-circuit"></a>De status van een ExpressRoute-circuit ophalen
U kunt deze informatie op elk gewenst moment opvragen met behulp van de cmdlet **Get-AzExpressRouteCircuit**. Als u de aanroep gebruikt zonder parameters, worden alle circuits weergegeven.

```azurepowershell-interactive
Get-AzExpressRouteCircuit
```

Het antwoord is vergelijkbaar met het volgende voorbeeld:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                    "Name": "Standard_MeteredData",
                                    "Tier": "Standard",
                                    "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                        "ServiceProviderName": "Equinix",
                                        "PeeringLocation": "Silicon Valley",
                                        "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

U kunt informatie over een specifiek ExpressRoute-circuit verkrijgen door de naam van de resourcegroep en de naam van het circuit als een parameter aan de aanroep door te geven:

```azurepowershell-interactive
Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"
```

Het antwoord ziet er ongeveer uit als in het volgende voorbeeld:

```azurepowershell
Name                             : ExpressRouteARMCircuit
ResourceGroupName                : ExpressRouteResourceGroup
Location                         : westus
Id                               : /subscriptions/***************************/resourceGroups/ExpressRouteResourceGroup/providers/Microsoft.Network/expressRouteCircuits/ExpressRouteARMCircuit
Etag                             : W/"################################"
ProvisioningState                : Succeeded
Sku                              : {
                                        "Name": "Standard_MeteredData",
                                        "Tier": "Standard",
                                        "Family": "MeteredData"
                                    }
CircuitProvisioningState         : Enabled
ServiceProviderProvisioningState : Provisioned
ServiceProviderNotes             :
ServiceProviderProperties        : {
                                        "ServiceProviderName": "Equinix",
                                        "PeeringLocation": "Silicon Valley",
                                        "BandwidthInMbps": 200
                                    }
ServiceKey                       : **************************************
Peerings                         : []
```

U kunt gedetailleerde beschrijvingen van alle parameters opvragen door de volgende opdracht uit te voeren:

```azurepowershell-interactive
get-help Get-AzExpressRouteCircuit -detailed
```

## <a name="modifying-an-expressroute-circuit"></a><a name="modify"></a>Een ExpressRoute-circuit wijzigen
U kunt bepaalde eigenschappen van een ExpressRoute-circuit wijzigen zonder dat dit van invloed is op de connectiviteit.

U kunt de volgende taken uitvoeren zonder dat dit resulteert in downtime:

* Een ExpressRoute Premium-invoegtoepassing in- of uitschakelen voor uw ExpressRoute-circuit. Het is niet mogelijk om de SKU te wijzigen van *Standard/Premium* in *Local*.
* De bandbreedte van uw ExpressRoute-circuit verhogen, mits er capaciteit beschikbaar is op de poort. Het verminderen van de bandbreedte van een circuit wordt niet ondersteund.
* Het verbruiksabonnement wijzigen van Datalimiet in Onbeperkte data. Het verbruiksabonnement wijzigen van Onbeperkte data in Datalimiet wordt niet ondersteund.
* U kunt *klassieke bewerkingen toestaan* in- en uitschakelen.

Zie de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md) voor meer informatie over limieten en beperkingen.

### <a name="to-enable-the-expressroute-premium-add-on"></a>Premium-invoegtoepassing voor ExpressRoute inschakelen
U kunt de ExpressRoute Premium-invoegtoepassing inschakelen voor uw bestaande circuit met behulp van het volgende PowerShell-fragment:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Premium"
$ckt.sku.Name = "Premium_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

De functies van de ExpressRoute Premium-invoegtoepassing zijn nu ingeschakeld voor het circuit. U wordt gefactureerd voor de mogelijkheden van de Premium-invoegtoepassing zodra de opdracht is uitgevoerd.

### <a name="to-disable-the-expressroute-premium-add-on"></a>Premium-invoegtoepassing voor ExpressRoute uitschakelen
> [!IMPORTANT]
> Deze bewerking kan mislukken als u resources gebruikt die groter zijn dan wat is toegestaan voor het Standard-circuit.
>

Let op de volgende informatie:

* Voordat u een downgrade uitvoert van Premium naar Standard, moet u ervoor zorgen dat het aantal virtuele netwerken dat is gekoppeld aan het circuit, kleiner is dan 10. Als u dat niet doet, mislukt de aanvraag en wordt u gefactureerd tegen Premium-tarieven.
* Alle virtuele netwerken in andere geopolitieke regio's moeten eerst worden ontkoppeld. Als u dat niet doet, mislukt de aanvraag en blijven we u factureren tegen Premium-tarieven.
* De routetabel moet kleiner zijn dan 4.000 routes voor persoonlijke peering. Als uw routetabel groter is dan 4.000 routes, wordt de BGP-sessie beëindigd. De BGP-sessie wordt pas weer ingeschakeld als het aantal geadverteerde voorvoegsels minder is dan 4.000.

U kunt de ExpressRoute Premium-invoegtoepassing uitschakelen voor het bestaande circuit met behulp van het volgende PowerShell-cmdlet:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Tier = "Standard"
$ckt.sku.Name = "Standard_MeteredData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-update-the-expressroute-circuit-bandwidth"></a>De bandbreedte van het ExpressRoute-circuit bijwerken
Informatie over ondersteunde bandbreedte-opties voor uw provider vindt u in de [Veelgestelde vragen over ExpressRoute](expressroute-faqs.md). U kunt elke grootte kiezen die groter is dan de grootte van uw bestaande circuit.

> [!IMPORTANT]
> Mogelijk moet u het ExpressRoute-circuit opnieuw maken als er onvoldoende capaciteit is op de bestaande poort. U kunt het circuit niet upgraden als er geen extra capaciteit beschikbaar is op de betreffende locatie.
>
> Het is niet mogelijk om de bandbreedte van een ExpressRoute-circuit zonder onderbreking te verminderen. Voor het verminderen van de bandbreedte moet u de inrichting van het ExpressRoute-circuit ongedaan maken, en vervolgens een nieuw ExpressRoute-circuit inrichten.
>

Nadat u hebt bepaald welke grootte u nodig hebt, gebruikt u de volgende opdracht om de grootte van het circuit te wijzigen:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.ServiceProviderProperties.BandwidthInMbps = 1000

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

Uw circuit wordt bijgewerkt aan de kant van Microsoft. Vervolgens moet u contact opnemen met uw connectiviteitsprovider om configuraties aan hun zijde bij te werken met deze wijziging. Nadat u deze melding hebt gedaan, wordt u gefactureerd voor de bijgewerkte bandbreedte-optie.

### <a name="to-move-the-sku-from-metered-to-unlimited"></a>De SKU wijzigen van Datalimiet in Onbeperkte data
U kunt de SKU van een ExpressRoute-circuit wijzigen met behulp van het volgende PowerShell-fragment:

```azurepowershell-interactive
$ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

$ckt.Sku.Family = "UnlimitedData"
$ckt.sku.Name = "Premium_UnlimitedData"

Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
```

### <a name="to-control-access-to-the-classic-and-resource-manager-environments"></a>De toegang tot de klassieke omgeving en de Resource Manager-omgeving beheren
Lees de instructies in het artikel over het [verplaatsen van ExpressRoute-circuits van het klassieke naar het Resource Manager-implementatiemodel](expressroute-howto-move-arm.md).

## <a name="deprovisioning-an-expressroute-circuit"></a><a name="delete"></a>De inrichting van een ExpressRoute-circuit ongedaan maken
Let op de volgende informatie:

* Alle virtuele netwerken moeten worden losgekoppeld van het ExpressRoute-circuit. Als deze bewerking mislukt, controleert u of er virtuele netwerken zijn gekoppeld aan het circuit.
* Als de serviceprovider van het circuit de inrichtingsstatus **Inrichten** of **Ingericht** heeft, moet u contact opnemen met de serviceprovider om de inrichting van het circuit aan hun zijde ongedaan te maken. We blijven resources reserveren en kosten in rekening brengen, totdat de serviceprovider de inrichting van het circuit helemaal ongedaan heeft gemaakt en ons op de hoogte heeft gesteld.
* Als de serviceprovider dit heeft gedaan (de inrichtingsstatus bij de serviceprovider is ingesteld op **NotProvisioned**), kunt u het circuit verwijderen. De facturering voor het circuit wordt dan gestopt.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt uw ExpressRoute-circuit verwijderen door de volgende opdracht uit te voeren:

```azurepowershell-interactive
Remove-AzExpressRouteCircuit -ResourceGroupName "ExpressRouteResourceGroup" -Name "ExpressRouteARMCircuit"
```

## <a name="next-steps"></a>Volgende stappen

Nadat u het circuit hebt gemaakt en het circuit is ingericht bij uw provider, gaat u verder met de volgende stap om de peering te configureren:

> [!div class="nextstepaction"]
> [Routering voor uw ExpressRoute-circuit maken en wijzigen](expressroute-howto-routing-arm.md)
