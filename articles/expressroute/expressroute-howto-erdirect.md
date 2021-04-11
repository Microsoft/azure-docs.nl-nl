---
title: 'Azure ExpressRoute: ExpressRoute direct configureren'
description: Meer informatie over het gebruik van Azure PowerShell voor het configureren van Azure ExpressRoute direct om rechtstreeks verbinding te maken met het wereld wijde micro soft-netwerk.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/14/2020
ms.author: duau
ms.openlocfilehash: 2c49fe65cf97207a495f6c0cc78585489a0db6ff
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107228245"
---
# <a name="how-to-configure-expressroute-direct"></a>ExpressRoute direct configureren

ExpressRoute direct biedt u de mogelijkheid om rechtstreeks verbinding te maken met het wereld wijde netwerk van micro soft via peering-locaties strategisch gedistribueerd over de hele wereld. Zie [About ExpressRoute Direct](expressroute-erdirect-about.md) (Over ExpressRoute Direct) voor meer informatie.

## <a name="before-you-begin"></a>Voordat u begint

Voordat u ExpressRoute direct gebruikt, moet u uw abonnement eerst registreren. Voordat u ExpressRoute direct gebruikt, moet u uw abonnement eerst registreren. Ga als volgt te werk om de registratie uit te voeren via Azure PowerShell:
1.  Meld u aan bij Azure en selecteer het abonnement dat u wilt inschrijven.

    ```azurepowershell-interactive
    Connect-AzAccount 

    Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
    ```

2. Registreer uw abonnement voor open bare Preview met de volgende opdracht:
    ```azurepowershell-interactive
    Register-AzProviderFeature -FeatureName AllowExpressRoutePorts -ProviderNamespace Microsoft.Network
    ```

Nadat u bent Inge schreven, controleert u of de resource provider **micro soft. Network** is geregistreerd voor uw abonnement. Als u een resourceprovider registreert, wordt uw abonnement zo geconfigureerd dat dit kan worden gebruikt met de resourceprovider.

## <a name="create-the-resource"></a><a name="resources"></a>De resource maken

1. Meld u aan bij Azure en selecteer het abonnement. De ExpressRoute direct resource-en ExpressRoute-circuits moeten zich in hetzelfde abonnement benemen.

   ```powershell
   Connect-AzAccount 

   Select-AzSubscription -Subscription "<SubscriptionID or SubscriptionName>"
   ```
   
2. Registreer uw abonnement opnieuw bij het micro soft. Network om toegang te krijgen tot de expressrouteportslocation-en expressrouteport-Api's.

   ```powershell
   Register-AzResourceProvider -ProviderNameSpace "Microsoft.Network"
   ```   
3. Alle locaties weer geven waar ExpressRoute direct wordt ondersteund.
  
   ```powershell
   Get-AzExpressRoutePortsLocation
   ```

   **Voorbeelduitvoer**
  
   ```powershell
   Name                : Equinix-Ashburn-DC2
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Ashburn-D
                        C2
   ProvisioningState   : Succeeded
   Address             : 21715 Filigree Court, DC2, Building F, Ashburn, VA 20147
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-Dallas-DA3
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-Dallas-DA
                        3
   ProvisioningState   : Succeeded
   Address             : 1950 N. Stemmons Freeway, Suite 1039A, DA3, Dallas, TX 75207
   Contact             : support@equinix.com
   AvailableBandwidths : []

   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : []
   ```
4. Bepalen of een locatie die hierboven wordt vermeld, beschik bare band breedte heeft

   ```powershell
   Get-AzExpressRoutePortsLocation -LocationName "Equinix-San-Jose-SV1"
   ```

   **Voorbeelduitvoer**

   ```powershell
   Name                : Equinix-San-Jose-SV1
   Id                  : /subscriptions/<subscriptionID>/providers/Microsoft.Network/expressRoutePortsLocations/Equinix-San-Jose-
                        SV1
   ProvisioningState   : Succeeded
   Address             : 11 Great Oaks Blvd, SV1, San Jose, CA 95119
   Contact             : support@equinix.com
   AvailableBandwidths : [
                          {
                            "OfferName": "100 Gbps",
                            "ValueInGbps": 100
                          }
                        ]
   ```
5. Een ExpressRoute-directe resource maken op basis van de hierboven gekozen locatie

   ExpressRoute direct ondersteunt zowel QinQ-als Dot1Q-inkapseling. Als QinQ is geselecteerd, wordt elk ExpressRoute-circuit dynamisch toegewezen aan een S-tag en is deze uniek in de directe resource van ExpressRoute. Elk C-tag op het circuit moet uniek zijn op het circuit, maar niet via de ExpressRoute direct.  

   Als Dot1Q-inkapseling is geselecteerd, moet u de uniekheid van de C-tag (VLAN) beheren voor de volledige ExpressRoute direct-resource.  

   > [!IMPORTANT]
   > ExpressRoute direct kan slechts één encapsulation-type zijn. Inkapseling kan niet worden gewijzigd nadat ExpressRoute direct is gemaakt.
   > 
 
   ```powershell 
   $ERDirect = New-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName -PeeringLocation $PeeringLocationName -BandwidthInGbps 100.0 -Encapsulation QinQ | Dot1Q -Location $AzureRegion
   ```

   > [!NOTE]
   > Het kenmerk encapsulation kan ook worden ingesteld op Dot1Q. 
   >

   **Voorbeeld uitvoer:**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                               ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                                 {
                                   "Name": "link1",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link1",
                                   "RouterName": "tst-09xgmr-cis-1",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "link2",
                                   "Etag": "W/\"<etagnumber>\"",
                                   "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                               Network/expressRoutePorts/Contoso-Direct/links/link2",
                                   "RouterName": "tst-09xgmr-cis-2",
                                   "InterfaceName": "HundredGigE2/2/2",
                                   "PatchPanelId": "PPID",
                                   "RackId": "RackID",
                                   "ConnectorType": "SC",
                                   "AdminState": "Disabled",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]
   Circuits                   : []
   ```

## <a name="generate-the-letter-of-authorization-loa"></a><a name="authorization"></a>De autorisatie brief (LOA) genereren

Raadpleeg de zojuist gemaakte ExpressRoute direct-resource, voer een klant naam in om de LOA naar te schrijven en (optioneel) een bestands locatie te definiëren om het document op te slaan. Als er niet naar een bestandspad wordt verwezen, wordt het document gedownload naar de huidige map.

  ```powershell 
   New-AzExpressRoutePortLOA -ExpressRoutePort $ERDirect -CustomerName TestCustomerName -Destination "C:\Users\SampleUser\Downloads" 
   ```
 **Voorbeelduitvoer**

   ```powershell
   Written Letter of Authorization To: C:\Users\SampleUser\Downloads\LOA.pdf
   ```

## <a name="change-admin-state-of-links"></a><a name="state"></a>De beheer status van koppelingen wijzigen
   
Dit proces moet worden gebruikt om een laag 1-test uit te voeren, zodat elke Kruis verbinding op de juiste wijze wordt gerepareerd in elke router voor primair en secundair.
1. ExpressRoute direct-Details ophalen.

   ```powershell
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   ```
2. Stel de koppeling in op ingeschakeld. Herhaal deze stap om elke koppeling in te stellen op ingeschakeld.

   Koppelingen [0] is de primaire poort en koppelingen [1] is de secundaire poort.

   ```powershell
   $ERDirect.Links[0].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   $ERDirect = Get-AzExpressRoutePort -Name $Name -ResourceGroupName $ResourceGroupName
   $ERDirect.Links[1].AdminState = "Enabled"
   Set-AzExpressRoutePort -ExpressRoutePort $ERDirect
   ```
   **Voorbeeld uitvoer:**

   ```powershell
   Name                       : Contoso-Direct
   ResourceGroupName          : Contoso-Direct-rg
   Location                   : westcentralus
   Id                         : /subscriptions/<number>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Network/exp
                             ressRoutePorts/Contoso-Direct
   Etag                       : W/"<etagnumber> "
   ResourceGuid               : <number>
   ProvisioningState          : Succeeded
   PeeringLocation            : Equinix-Seattle-SE2
   BandwidthInGbps            : 100
   ProvisionedBandwidthInGbps : 0
   Encapsulation              : QinQ
   Mtu                        : 1500
   EtherType                  : 0x8100
   AllocationDate             : Saturday, September 1, 2018
   Links                      : [
                               {
                                 "Name": "link1",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link1",
                                 "RouterName": "tst-09xgmr-cis-1",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               },
                               {
                                 "Name": "link2",
                                 "Etag": "W/\"<etagnumber>\"",
                                 "Id": "/subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.
                             Network/expressRoutePorts/Contoso-Direct/links/link2",
                                 "RouterName": "tst-09xgmr-cis-2",
                                 "InterfaceName": "HundredGigE2/2/2",
                                 "PatchPanelId": "PPID",
                                 "RackId": "RackID",
                                 "ConnectorType": "SC",
                                 "AdminState": "Enabled",
                                 "ProvisioningState": "Succeeded"
                               }
                             ]
   Circuits                   : []
   ```

   Gebruik dezelfde procedure `AdminState = "Disabled"` om de poorten in te scha kelen.

## <a name="create-a-circuit"></a><a name="circuit"></a>Een circuit maken

Standaard kunt u 10 circuits maken in het abonnement waarin de ExpressRoute direct-resource zich bevindt. Deze limiet kan worden verhoogd met ondersteuning. U bent zelf verantwoordelijk voor het bijhouden van zowel ingerichte als gebruikte band breedte. Ingerichte band breedte is de som van de band breedte van alle circuits op de ExpressRoute direct-resource en gebruikte band breedte is het fysieke gebruik van de onderliggende fysieke interfaces.

Er zijn extra circuit bandbreedten die op ExpressRoute direct kunnen worden gebruikt om alleen de hierboven beschreven scenario's te ondersteunen. Deze band breedten zijn 40 Gbps en 100 Gbps.

**SkuTier** kan lokaal, standaard of Premium zijn.

**SkuFamily** kan alleen MeteredData zijn. Onbeperkt wordt niet ondersteund op ExpressRoute direct.

Maak een circuit op de ExpressRoute direct-resource.

  ```powershell
  New-AzExpressRouteCircuit -Name $Name -ResourceGroupName $ResourceGroupName -ExpressRoutePort $ERDirect -BandwidthinGbps 100.0  -Location $AzureRegion -SkuTier Premium -SkuFamily MeteredData 
  ```

  Andere band breedten zijn onder andere: 5,0, 10,0 en 40,0

  **Voorbeeld uitvoer:**

  ```powershell
  Name                             : ExpressRoute-Direct-ckt
  ResourceGroupName                : Contoso-Direct-rg
  Location                         : westcentralus
  Id                               : /subscriptions/<subscriptionID>/resourceGroups/Contoso-Direct-rg/providers/Microsoft.Netwo
                                   rk/expressRouteCircuits/ExpressRoute-Direct-ckt
  Etag                             : W/"<etagnumber>"
  ProvisioningState                : Succeeded
  Sku                              : {
                                     "Name": "Premium_MeteredData",
                                     "Tier": "Premium",
                                     "Family": "MeteredData"
                                   }
  CircuitProvisioningState         : Enabled
  ServiceProviderProvisioningState : Provisioned
  ServiceProviderNotes             : 
    ServiceProviderProperties        : null
  ExpressRoutePort                 : {
                                     "Id": "/subscriptions/<subscriptionID>n/resourceGroups/Contoso-Direct-rg/providers/Micros
                                   oft.Network/expressRoutePorts/Contoso-Direct"
                                   }
  BandwidthInGbps                  : 10
  Stag                             : 2
  ServiceKey                       : <number>
  Peerings                         : []
  Authorizations                   : []
  AllowClassicOperations           : False
  GatewayManagerEtag     
  ```
## <a name="delete-the-resource"></a>De resource verwijderen
Voordat u de ExpressRoute-directe resource verwijdert, moet u eerst de ExpressRoute-circuits verwijderen die zijn gemaakt in het directe poort paar ExpressRoute.
U kunt de ExpressRoute direct-resource verwijderen door de volgende opdracht uit te voeren:
 ```powershell
   Remove-azexpressrouteport -Name $Name -Resourcegroupname -$ResourceGroupName
   ```
## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht](expressroute-erdirect-about.md)voor meer informatie over ExpressRoute direct.
