---
title: 'Een VNet verbinden met een VNet met behulp van een VNet-naar-VNet-verbinding: Azure CLI'
description: Virtuele netwerken met elkaar verbinden met behulp van een VNet-naar-VNet-verbinding en Azure CLI.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.custom: devx-track-azurecli
ms.openlocfilehash: b9502f3fbd50aad756e15daa4db1badda2abf9ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94660063"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-using-azure-cli"></a>Een VPN-gatewayverbinding tussen VNets configureren met behulp van Azure CLI

Dit artikel helpt u om virtuele netwerken te verbinden met behulp van het verbindingstype VNet-naar-VNet. De virtuele netwerken kunnen zich in dezelfde of verschillende regio's bevinden en tot dezelfde of verschillende abonnementen behoren. Wanneer u VNets uit verschillende abonnementen koppelt, hoeven de abonnementen niet aan dezelfde Active Directory-tenant gekoppeld te zijn.

De stappen in dit artikel zijn van toepassing op het Resource Manager-implementatiemodel en gebruiken Azure CLI. U kunt deze configuratie ook maken met een ander implementatiehulpprogramma of een ander implementatiemodel door in de volgende lijst een andere optie te selecteren:

> [!div class="op_single_selector"]
> * [Azure-portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Azure-CLI](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Azure Portal (klassiek)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Verbinding maken tussen verschillende implementatiemodellen - Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Verbinding maken tussen verschillende implementatiemodellen - PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a><a name="about"></a>Over het verbinden van VNet's

Er zijn meerdere manieren om VNet's te koppelen. In de onderstaande secties worden verschillende manieren beschreven voor het koppelen van virtuele netwerken.

### <a name="vnet-to-vnet"></a>VNet-naar-VNet

Het configureren van een VNet-naar-VNet-verbinding is een goede manier om VNet's te koppelen. Het verbinden van een virtueel netwerk met een ander virtueel netwerk met behulp van het VNet-naar-VNet-verbindingstype is vergelijkbaar met het maken van een site-naar-site-IPsec-verbinding naar een on-premises locatie. Voor beide verbindingstypen wordt een VPN-gateway gebruikt om een beveiligde tunnel met IPsec/IKE te bieden en beide typen werken tijdens het communiceren op dezelfde manier. Het verschil tussen de verbindingstypen is de manier waarop de lokale netwerkgateway is geconfigureerd. Als u een VNet-naar-VNet-verbinding maakt, ziet u de adresruimte voor het lokale netwerkgateway niet. Deze wordt automatisch gemaakt en ingevuld. Wanneer u de adresruimte voor een VNet bijwerkt, is de route naar de bijgewerkte adresruimte automatisch bekend voor het andere VNet. Als u een VNet-naar-VNet-verbinding maakt, is dit gewoonlijk sneller en makkelijker dan een site-naar-site-verbinding tussen VNet's.

### <a name="connecting-vnets-using-site-to-site-ipsec-steps"></a>Stappen voor het koppelen van VNet's via site-naar-site (IPsec)

Als u met een gecompliceerde netwerkconfiguratie werkt, kunt u de VNet's wellicht beter koppelen met behulp van de stappen voor [site-naar-site](vpn-gateway-howto-site-to-site-resource-manager-cli.md) dan met de stappen voor VNet-naar-VNet. Als u de stappen voor site-naar-site gebruikt, maakt en configureert u de lokale netwerkgateways handmatig. De lokale netwerkgateway voor elke VNet behandelt de andere VNet als een lokale site. Hiermee kunt u extra adresruimte voor de lokale netwerkgateway opgeven om het verkeer te routeren. Als de adresruimte voor een VNet wordt gewijzigd, moet u de bijbehorende lokale netwerkgateway handmatig bijwerken om de wijziging door te voeren. Er vindt niet automatisch een update plaats.

### <a name="vnet-peering"></a>VNet-peering

U kunt overwegen uw VNet's te koppelen door middel van VNET-peering. Bij VNET-peering wordt geen VPN-gateway gebruikt en er gelden diverse beperkingen voor. Bovendien worden de [prijzen voor VNET-peering](https://azure.microsoft.com/pricing/details/virtual-network) anders berekend dan voor [VNet-naar-VNet-VPN Gateway](https://azure.microsoft.com/pricing/details/vpn-gateway). Zie het artikel [VNet-peering](../virtual-network/virtual-network-peering-overview.md) voor meer informatie.

## <a name="why-create-a-vnet-to-vnet-connection"></a><a name="why"></a>Waarom een VNet-met-VNet-verbinding maken?

U kunt omwille van de volgende redenen virtuele netwerken koppelen met een VNet-naar-VNet-verbinding:

* **Geografische redundantie en aanwezigheid tussen regio's**

  * U kunt uw eigen geo-replicatie of synchronisatie met beveiligde connectiviteit instellen zonder gebruik te maken van internetgerichte eindpunten.
  * Met Azure Traffic Manager en Load Balancer kunt u workloads met maximale beschikbaarheid instellen met behulp van geografische redundantie over meerdere Azure-regio's. Een belangrijk voorbeeld hiervan is het instellen van SQL Always On met beschikbaarheidsgroepen verspreid over meerdere Azure-regio's.
* **Regionale toepassingen met meerdere lagen met isolatie- of beheergrenzen**

  * Binnen dezelfde regio kunt u vanwege isolatie- of beheervereisten toepassingen met meerdere lagen instellen met meerdere virtuele netwerken die met elkaar zijn verbonden.

VNet-naar-VNet-communicatie kan worden gecombineerd met configuraties voor meerdere locaties. Zo kunt u netwerktopologieën maken waarin cross-premises connectiviteit is gecombineerd met connectiviteit tussen virtuele netwerken.

## <a name="which-vnet-to-vnet-steps-should-i-use"></a><a name="steps"></a>Welke stappen voor VNet-naar-VNet moet ik gebruiken?

In dit artikel ziet u twee verschillende reeksen stappen voor een VNet-naar-VNet-verbinding. Eén reeks stappen voor [VNet's die onderdeel uitmaken van hetzelfde abonnement](#samesub) en één reeks voor [VNet's die onderdeel uitmaken van verschillende abonnementen](#difsub). 

Voor deze oefening kunt u configuraties combineren of alleen de configuratie kiezen waarmee u wilt werken. Alle configuraties maken gebruik van het verbindingstype VNet-naar-VNet. Netwerkverkeer verloopt tussen de VNet's die rechtstreeks met elkaar zijn verbonden. In deze oefening wordt verkeer van TestVNet4 niet gerouteerd naar TestVNet5.

* [VNet's die onderdeel uitmaken van hetzelfde abonnement:](#samesub) in de stappen voor deze configuratie wordt gebruikgemaakt van TestVNet1 en TestVNet4.

  ![Diagram met de V net-naar-V-net-stappen voor V-netten die zich in hetzelfde abonnement bevinden.](./media/vpn-gateway-howto-vnet-vnet-cli/v2vrmps.png)

* [VNet's die onderdeel uitmaken van verschillende abonnementen:](#difsub) in de stappen voor deze configuratie wordt gebruikgemaakt van TestVNet1 en TestVNet5.

  ![v2v-diagram](./media/vpn-gateway-howto-vnet-vnet-cli/v2vdiffsub.png)


## <a name="connect-vnets-that-are-in-the-same-subscription"></a><a name="samesub"></a>VNets verbinden die tot hetzelfde abonnement behoren

### <a name="before-you-begin"></a>Voordat u begint

Installeer eerst de meest recente versie van de CLI-opdrachten (2.0 of hoger). Zie [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli) voor meer informatie over het installeren van de CLI-opdrachten.

### <a name="plan-your-ip-address-ranges"></a><a name="Plan"></a>De IP-adresbereiken plannen

In de volgende stappen maakt u twee virtuele netwerken en de bijbehorende gatewaysubnetten en configuraties. Vervolgens maakt u een VPN-verbinding tussen de twee VNet's. Het is belangrijk dat u de IP-adresbereiken voor uw netwerkconfiguratie plant. De VNet-bereiken of de bereiken van het lokale netwerk mogen elkaar niet overlappen. In deze voorbeelden behandelen we geen DNS-server. Zie [Naamomzetting](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) als u naamomzetting voor uw virtuele netwerken wilt.

In de voorbeelden worden de volgende waarden gebruikt:

**Waarden voor TestVNet1:**

* VNet-naam: TestVNet1
* Resourcegroep: TestRG1
* Locatie: VS - oost
* TestVNet1: 10.11.0.0/16 en 10.12.0.0/16
* FrontEnd: 10.11.0.0/24
* BackEnd: 10.12.0.0/24
* GatewaySubnet: 10.12.255.0/27
* GatewayName: VNet1GW
* Openbare IP: VNet1GWIP
* VPNType: op route gebaseerd
* Connection(1to4): VNet1toVNet4
* Connection(1to5): VNet1toVNet5 (voor VNets in verschillende abonnementen)

**Waarden voor TestVNet4:**

* VNet-naam: TestVNet4
* TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
* FrontEnd: 10.41.0.0/24
* BackEnd: 10.42.0.0/24
* GatewaySubnet: 10.42.255.0/27
* Resourcegroep: TestRG4
* Locatie: VS - west
* GatewayName: VNet4GW
* Openbare IP: VNet4GWIP
* VPNType: op route gebaseerd
* Verbinding: VNet4toVNet1

### <a name="step-1---connect-to-your-subscription"></a><a name="Connect"></a>Stap 1: verbinding maken met uw abonnement

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-numbers-include.md)]

### <a name="step-2---create-and-configure-testvnet1"></a><a name="TestVNet1"></a>Stap 2: TestVNet1 maken en configureren

1. Een resourcegroep maken.

   ```azurecli
   az group create -n TestRG1  -l eastus
   ```
2. Maak TestVNet1 en de subnetten voor TestVNet1. In dit voorbeeld worden een virtueel netwerk met de naam TestVNet1 en een subnet met de naam FrontEnd gemaakt.

   ```azurecli
   az network vnet create -n TestVNet1 -g TestRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24
   ```
3. Maak een extra adresruimte voor het back-endsubnet. In deze stap specificeren we zowel de adresruimte die we eerder hebben gemaakt en de extra adresruimte die we willen toevoegen. Dit komt doordat de opdracht [az network vnet update](/cli/azure/network/vnet) de vorige instellingen overschrijft. Zorg ervoor dat u alle adresvoorvoegsels opgeeft wanneer u deze opdracht gebruikt.

   ```azurecli
   az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestRG1
   ```
4. Maak het back-endsubnet.
  
   ```azurecli
   az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestRG1 --address-prefix 10.12.0.0/24 
   ```
5. Maak het gatewaysubnet. Het gatewaysubnet heet GatewaySubnet. Deze naam is verplicht. In dit voorbeeld maakt het gatewaysubnet gebruik van een /27. Het is mogelijk om een klein gatewaysubnet van /29 te maken, maar we raden u aan een groter subnet met meer adressen te maken door ten minste /28 of /27 te selecteren. Hierdoor hebt u genoeg adressen voor mogelijke aanvullende toekomstige configuraties.

   ```azurecli 
   az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestRG1 --address-prefix 10.12.255.0/27
   ```
6. Vraag om een openbaar IP-adres toe te wijzen aan de gateway die u voor uw VNet gaat maken. De toewijzingsmethode is Dynamisch. U kunt het IP-adres dat u wilt gebruiken niet zelf opgeven. Het wordt dynamisch toegewezen aan uw gateway.

   ```azurecli
   az network public-ip create -n VNet1GWIP -g TestRG1 --allocation-method Dynamic
   ```
7. Maak de gateway van het virtuele netwerk voor TestVNet1. VNet-naar-VNet-configuraties vereisen een op route gebaseerd VpnType. Als u deze opdracht uitvoert met de parameter --no-wait, ziet u geen feedback of uitvoer. De parameter "--no-wait" zorgt ervoor dat de gateway op de achtergrond wordt gemaakt. Dit betekent niet dat de VPN-gateway onmiddellijk klaar is. Het maken van een gateway duurt vaak 45 minuten of langer, afhankelijk van de gateway-SKU die u gebruikt.

   ```azurecli
   az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address VNet1GWIP -g TestRG1 --vnet TestVNet1 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="step-3---create-and-configure-testvnet4"></a><a name="TestVNet4"></a>Stap 3: TestVNet4 maken en configureren

1. Een resourcegroep maken.

   ```azurecli
   az group create -n TestRG4  -l westus
   ```
2. Maak TestVNet4.

   ```azurecli
   az network vnet create -n TestVNet4 -g TestRG4 --address-prefix 10.41.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.41.0.0/24
   ```

3. Extra subnetten voor TestVNet4 maken.

   ```azurecli
   az network vnet update -n TestVNet4 --address-prefixes 10.41.0.0/16 10.42.0.0/16 -g TestRG4 
   az network vnet subnet create --vnet-name TestVNet4 -n BackEnd -g TestRG4 --address-prefix 10.42.0.0/24 
   ```
4. Maak het gatewaysubnet.

   ```azurecli
   az network vnet subnet create --vnet-name TestVNet4 -n GatewaySubnet -g TestRG4 --address-prefix 10.42.255.0/27
   ```
5. Vraag een openbaar IP-adres aan.

   ```azurecli
   az network public-ip create -n VNet4GWIP -g TestRG4 --allocation-method Dynamic
   ```
6. Maak de gateway van het virtuele TestVNet4-netwerk.

   ```azurecli
   az network vnet-gateway create -n VNet4GW -l westus --public-ip-address VNet4GWIP -g TestRG4 --vnet TestVNet4 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="step-4---create-the-connections"></a><a name="createconnect"></a>Stap 4: de verbindingen maken

U hebt nu twee VNets met VPN-gateways. De volgende stap is het maken van VPN-gatewayverbindingen tussen de gateways voor de virtuele netwerken. Als u de bovenstaande voorbeelden hebt gebruikt, bevinden uw VNet-gateways zich in verschillende resourcegroepen. Wanneer de gateways zich in verschillende resourcegroepen bevinden, moet u de resource-id's voor elke gateway bepalen en opgeven wanneer u een verbinding maakt. Als uw VNets zich in dezelfde resourcegroep bevinden, kunt u de [tweede set instructies](#samerg) gebruiken omdat u de resource-id's niet hoeft op te geven.

### <a name="to-connect-vnets-that-reside-in-different-resource-groups"></a><a name="diffrg"></a>VNets verbinden die zich in verschillende resourcegroepen bevinden

1. U kunt de resource-id van VNet1GW in de uitvoer van de volgende opdracht vinden:

   ```azurecli
   az network vnet-gateway show -n VNet1GW -g TestRG1
   ```

   Zoek de regel "id:" in de uitvoer. De waarden binnen de aanhalingstekens zijn nodig voor het maken van de verbinding in de volgende sectie. Kopieer deze waarden naar een teksteditor zoals Kladblok, zodat u ze gemakkelijk kunt plakken wanneer u verbinding maakt.

   Voorbeelduitvoer:

   ```
   "activeActive": false, 
   "bgpSettings": { 
    "asn": 65515, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
   }, 
   "enableBgp": false, 
   "etag": "W/\"ecb42bc5-c176-44e1-802f-b0ce2962ac04\"", 
   "gatewayDefaultSite": null, 
   "gatewayType": "Vpn", 
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW", 
   "ipConfigurations":
   ```

   Kopieer de waarden na **"id:"** binnen de aanhalingstekens.

   ```
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
   ```

2. Haal de resource-id van VNet4GW op en kopieer de waarden naar een teksteditor.

   ```azurecli
   az network vnet-gateway show -n VNet4GW -g TestRG4
   ```

3. Maak de verbinding tussen TestVNet1 en TestVNet4. In deze stap maakt u de verbinding van TestVNet1 naar TestVNet4. Er wordt naar een gedeelde sleutel verwezen in de voorbeelden. U kunt uw eigen waarden voor de gedeelde sleutel gebruiken. Het belangrijkste is dat de gedeelde sleutel voor beide verbindingen moet overeenkomen. Het kan even duren voordat de verbinding tot stand is gebracht.

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW 
   ```
4. Maak de verbinding tussen TestVNet4 en TestVNet1. Deze stap is vergelijkbaar met die hierboven, alleen maakt u de verbinding nu vanuit TestVNet4 naar TestVNet1. Zorg dat de gedeelde sleutels overeenkomen. Het duurt enkele minuten om de verbinding tot stand te brengen.

   ```azurecli
   az network vpn-connection create -n VNet4ToVNet1 -g TestRG4 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW -l westus --shared-key "aabbcc" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1G
   ```
5. Controleer uw verbindingen. Zie [De verbinding controleren](#verify).

### <a name="to-connect-vnets-that-reside-in-the-same-resource-group"></a><a name="samerg"></a>VNets verbinden die zich in dezelfde resourcegroep bevinden

1. Maak de verbinding tussen TestVNet1 en TestVNet4. In deze stap maakt u de verbinding van TestVNet1 naar TestVNet4. De resourcegroepen in de voorbeelden zijn hetzelfde. Er wordt in de voorbeelden naar een gedeelde sleutel verwezen. U kunt uw eigen waarden voor de gedeelde sleutel gebruiken, maar de gedeelde sleutel moet voor beide verbindingen hetzelfde zijn. Het kan even duren voordat de verbinding tot stand is gebracht.

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet4 -g TestRG1 --vnet-gateway1 VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet4GW
   ```
2. Maak de verbinding tussen TestVNet4 en TestVNet1. Deze stap is vergelijkbaar met die hierboven, alleen maakt u de verbinding nu vanuit TestVNet4 naar TestVNet1. Zorg dat de gedeelde sleutels overeenkomen. Het duurt enkele minuten om de verbinding tot stand te brengen.

   ```azurecli
   az network vpn-connection create -n VNet4ToVNet1 -g TestRG1 --vnet-gateway1 VNet4GW -l eastus --shared-key "eeffgg" --vnet-gateway2 VNet1GW
   ```
3. Controleer uw verbindingen. Zie [De verbinding controleren](#verify).

## <a name="connect-vnets-that-are-in-different-subscriptions"></a><a name="difsub"></a>VNets verbinden die tot verschillende abonnement behoren

In dit scenario verbindt u TestVNet1 met TestVNet5. De VNets behoren tot verschillende abonnementen. De abonnementen hoeven niet aan dezelfde Active Directory-tenant gekoppeld te zijn. Met de stappen voor deze configuratie voegt u nog een VNet-naar-VNet-verbinding toe om TestVNet1 te verbinden met TestVNet5.

### <a name="step-5---create-and-configure-testvnet1"></a><a name="TestVNet1diff"></a>Stap 5: TestVNet1 maken en configureren

Deze instructies gaan verder na de stappen in de voorgaande secties. U moet [stap 1](#Connect) en [stap 2](#TestVNet1) uitvoeren om TestVNet1 en de VPN gateway voor TestVNet1 te maken en te configureren. Voor deze configuratie is het niet vereist TestVNet4 uit de vorige sectie te maken. Als u deze wel maakt, levert dit geen problemen op met de volgende stappen. Nadat u stap 1 en stap 2 hebt voltooid, gaat u verder met stap 6 hieronder.

### <a name="step-6---verify-the-ip-address-ranges"></a><a name="verifyranges"></a>Stap 6: de IP-adresbereiken controleren

Als u extra verbindingen maakt, is het belangrijk dat u controleert of de IP-adresruimte van het nieuwe virtuele netwerk niet overlapt met een van de VNet-bereiken of de gatewaybereiken van het lokale netwerk. Voor deze oefening kunt u de volgende waarden voor TestVNet5 gebruiken:

**Waarden voor TestVNet5:**

* VNet-naam: TestVNet5
* Resourcegroep: TestRG5
* Locatie: Japan - oost
* TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
* FrontEnd: 10.51.0.0/24
* BackEnd: 10.52.0.0/24
* GatewaySubnet: 10.52.255.0.0/27
* GatewayName: VNet5GW
* Openbare IP: VNet5GWIP
* VPNType: op route gebaseerd
* Verbinding: VNet5toVNet1
* ConnectionType: VNet2VNet

### <a name="step-7---create-and-configure-testvnet5"></a><a name="TestVNet5"></a>Stap 7: TestVNet5 maken en configureren

Deze stap moet worden uitgevoerd in de context van het nieuwe abonnement, abonnement 5. Dit deel kan worden uitgevoerd door de beheerder in een andere organisatie die eigenaar is van het abonnement. Als u wilt scha kelen tussen abonnementen gebruiken `az account list --all` om de abonnementen weer te geven die beschikbaar zijn voor uw account, kunt u gebruiken `az account set --subscription <subscriptionID>` om over te scha kelen naar het abonnement dat u wilt gebruiken.

1. Zorg ervoor dat u bent verbonden met abonnement 5 en maak vervolgens een resourcegroep.

   ```azurecli
   az group create -n TestRG5  -l japaneast
   ```
2. Maak TestVNet5.

   ```azurecli
   az network vnet create -n TestVNet5 -g TestRG5 --address-prefix 10.51.0.0/16 -l japaneast --subnet-name FrontEnd --subnet-prefix 10.51.0.0/24
   ```

3. Voeg subnetten toe.

   ```azurecli
   az network vnet update -n TestVNet5 --address-prefixes 10.51.0.0/16 10.52.0.0/16 -g TestRG5
   az network vnet subnet create --vnet-name TestVNet5 -n BackEnd -g TestRG5 --address-prefix 10.52.0.0/24
   ```

4. Voeg het gatewaysubnet toe.

   ```azurecli
   az network vnet subnet create --vnet-name TestVNet5 -n GatewaySubnet -g TestRG5 --address-prefix 10.52.255.0/27
   ```

5. Vraag een openbaar IP-adres aan.

   ```azurecli
   az network public-ip create -n VNet5GWIP -g TestRG5 --allocation-method Dynamic
   ```
6. De TestVNet5-gateway maken

   ```azurecli
   az network vnet-gateway create -n VNet5GW -l japaneast --public-ip-address VNet5GWIP -g TestRG5 --vnet TestVNet5 --gateway-type Vpn --sku VpnGw1 --vpn-type RouteBased --no-wait
   ```

### <a name="step-8---create-the-connections"></a><a name="connections5"></a>Stap 8: de verbindingen maken

Deze stap is opgesplitst in twee CLI-sessies, aangeduid als **[Abonnement 1]** en **[Abonnement 5]** omdat de gateways onderdeel uitmaken van verschillende abonnementen. Als u wilt scha kelen tussen abonnementen gebruiken `az account list --all` om de abonnementen weer te geven die beschikbaar zijn voor uw account, kunt u gebruiken `az account set --subscription <subscriptionID>` om over te scha kelen naar het abonnement dat u wilt gebruiken.

1. **[Abonnement 1]** Meld u aan bij en maak verbinding met Abonnement 1. Voer de volgende opdracht uit om de naam en id van de gateway op te halen in de uitvoer:

   ```azurecli
   az network vnet-gateway show -n VNet1GW -g TestRG1
   ```

   Kopieer de uitvoer voor "id:". Verstuur de id en naam van de VNet-gateway (VNet1GW) via e-mail of een andere methode naar de beheerder van abonnement 5.

   Voorbeelduitvoer:

   ```
   "id": "/subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
   ```

2. **[Abonnement 5]** Meld u aan bij en maak verbinding met Abonnement 5. Voer de volgende opdracht uit om de naam en id van de gateway op te halen in de uitvoer:

   ```azurecli
   az network vnet-gateway show -n VNet5GW -g TestRG5
   ```

   Kopieer de uitvoer voor "id:". Verstuur de id en naam van de VNet-gateway (VNet5GW) via e-mail of een andere methode naar de beheerder van abonnement 1.

3. **[Abonnement 1]** In deze stap maakt u de verbinding van TestVNet1 naar TestVNet5. U kunt uw eigen waarden voor de gedeelde sleutel gebruiken, maar de gedeelde sleutel moet voor beide verbindingen hetzelfde zijn. Het kan even duren voordat de verbinding is gemaakt. Zorg dat u verbinding maakt met Abonnement 1.

   ```azurecli
   az network vpn-connection create -n VNet1ToVNet5 -g TestRG1 --vnet-gateway1 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW -l eastus --shared-key "eeffgg" --vnet-gateway2 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW
   ```

4. **[Abonnement 5]** Deze stap is vergelijkbaar met die hierboven, alleen maakt u de verbinding nu vanuit TestVNet5 naar TestVNet1. Zorg ervoor dat de gedeelde sleutels overeenkomen en dat u verbinding met abonnement 5 maakt.

   ```azurecli
   az network vpn-connection create -n VNet5ToVNet1 -g TestRG5 --vnet-gateway1 /subscriptions/e7e33b39-fe28-4822-b65c-a4db8bbff7cb/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW -l japaneast --shared-key "eeffgg" --vnet-gateway2 /subscriptions/d6ff83d6-713d-41f6-a025-5eb76334fda9/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
   ```

## <a name="verify-the-connections"></a><a name="verify"></a>De verbindingen controleren
[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

[!INCLUDE [verify connections](../../includes/vpn-gateway-verify-connection-cli-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Veelgestelde vragen over VNet-naar-VNet
[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Volgende stappen

* Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Raadpleeg de [Documentatie voor Virtual Machines](../index.yml) voor meer informatie.
* Voor meer informatie over BGP raadpleegt u [BGP Overview](vpn-gateway-bgp-overview.md) (BGP-overzicht) en [How to configure BGP](vpn-gateway-bgp-resource-manager-ps.md) (BGP configureren).