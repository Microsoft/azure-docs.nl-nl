---
title: IPsec/IKE-beleid voor S2S VPN-& VNet-naar-VNet-verbindingen
titleSuffix: Azure VPN Gateway
description: Configureer IPsec/IKE-beleid voor S2S-of VNet-naar-VNet-verbindingen met Azure VPN-gateways met behulp van Azure Resource Manager en Power shell.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: yushwang
ms.openlocfilehash: 96931d2dd94a8a31021ebe62caaefc54f643b007
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94649259"
---
# <a name="configure-ipsecike-policy-for-s2s-vpn-or-vnet-to-vnet-connections"></a>IPsec/IKE-beleid configureren voor S2S VPN-verbindingen of VNet-naar-VNet-verbindingen

In dit artikel worden de stappen beschreven voor het configureren van IPsec/IKE-beleid voor site-naar-site VPN-of VNet-naar-VNet-verbindingen met behulp van het Resource Manager-implementatie model en Power shell.



## <a name="about-ipsec-and-ike-policy-parameters-for-azure-vpn-gateways"></a><a name="about"></a>IPsec-en IKE-beleids parameters voor Azure VPN-gateways
IPsec en IKE-protocol standaard bieden ondersteuning voor een breed scala van cryptografische algoritmen in verschillende combi Naties. Raadpleeg [over cryptografische vereisten en Azure VPN-gateways](vpn-gateway-about-compliance-crypto.md) om te zien hoe dit kan helpen om te zorgen voor cross-premises en vnet-naar-vnet-connectiviteit die voldoet aan uw nalevings-en beveiligings vereisten.

Dit artikel bevat instructies voor het maken en configureren van een IPsec/IKE-beleid en voor het Toep assen van een nieuwe of bestaande verbinding:

* [Deel 1: werk stroom om IPsec/IKE-beleid te maken en in te stellen](#workflow)
* [Deel 2: ondersteunde cryptografische algoritmen en sleutel sterkten](#params)
* [Deel 3: een nieuwe S2S-VPN-verbinding maken met het IPsec/IKE-beleid](#crossprem)
* [Deel 4: een nieuwe VNet-naar-VNet-verbinding maken met het IPsec/IKE-beleid](#vnet2vnet)
* [Deel 5: het IPsec/IKE-beleid voor een verbinding beheren (maken, toevoegen, verwijderen)](#managepolicy)

> [!IMPORTANT]
> 1. Houd er rekening mee dat IPsec/IKE-beleid alleen werkt op de volgende gateway-Sku's:
>    * ***VpnGw1, VpnGw2, VpnGw3*** (op route gebaseerd)
>    * ***Standard** _ en _ *_High Performance_** (op route gebaseerd)
> 2. U kunt maar ***één*** beleidscombinatie opgeven voor een bepaalde verbinding.
> 3. U moet alle algoritmen en para meters opgeven voor zowel IKE (hoofd modus) als IPsec (snelle modus). Gedeeltelijke beleidsspecificatie is niet toegestaan.
> 4. Neem contact op met de specificaties van de leverancier van uw VPN-apparaat om ervoor te zorgen dat het beleid wordt ondersteund op uw on-premises VPN-apparaten. S2S-of VNet-naar-VNet-verbindingen kunnen niet bepalen of het beleid niet compatibel is.

## <a name="part-1---workflow-to-create-and-set-ipsecike-policy"></a><a name ="workflow"></a>Deel 1: werk stroom om IPsec/IKE-beleid te maken en in te stellen
In deze sectie wordt de werk stroom beschreven om IPsec/IKE-beleid te maken en bij te werken op een S2S VPN-of VNet-naar-VNet-verbinding:
1. Een virtueel netwerk en een VPN-gateway maken
2. Een lokale netwerk gateway maken voor cross-premises verbinding of een ander virtueel netwerk en een andere gateway voor VNet-naar-VNet-verbinding
3. Een IPsec/IKE-beleid met geselecteerde algoritmen en para meters maken
4. Een verbinding (IPsec of VNet2VNet) maken met het IPsec/IKE-beleid
5. Een IPsec/IKE-beleid voor een bestaande verbinding toevoegen/bijwerken/verwijderen

De instructies in dit artikel helpen u bij het instellen en configureren van IPsec/IKE-beleid, zoals weer gegeven in het diagram:

![IPSec-IKE-beleid](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)

## <a name="part-2---supported-cryptographic-algorithms--key-strengths"></a><a name ="params"></a>Deel 2: ondersteunde cryptografische algoritmen & sleutel sterktes

De volgende tabel bevat de ondersteunde cryptografische algoritmen en sleutel sterktes die door de klanten kunnen worden geconfigureerd:

| **IPsec/IKEv2**  | **Opties**    |
| ---  | --- 
| IKEv2-versleuteling | AES256, AES192, AES128, DES3, DES  
| IKEv2-integriteit  | SHA384, SHA256, SHA1, MD5  |
| DH-groep         | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, geen |
| IPsec-versleuteling | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, geen    |
| IPsec-integriteit  | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| PFS-groep        | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, geen 
| QM SA-levensduur   | (**Optioneel**: de standaard waarden worden gebruikt als er geen waarde is opgegeven)<br>Seconden (geheel getal; **min. 300**/standaard 27000 seconden)<br>KB (geheel getal; **min. 1024**/standaard 102400000 KB)   |
| Verkeersselector | UsePolicyBasedTrafficSelectors * * ($True/$False; **Optioneel**, standaard $false indien niet opgegeven)    |
|  |  |

> [!IMPORTANT]
> 1. **De configuratie van uw on-premises VPN-apparaat moet overeenkomen met of de volgende algoritmen en parameters bevatten die u in het Azure IPsec/IKE-beleid opgeeft:**
>    * IKE-versleutelings algoritme (hoofd modus/fase 1)
>    * IKE-integriteits algoritme (hoofd modus/fase 1)
>    * DH-groep (hoofd modus/fase 1)
>    * IPsec-versleutelings algoritme (snelle modus/fase 2)
>    * IPsec-integriteits algoritme (snelle modus/fase 2)
>    * PFS-groep (snelle modus/fase 2)
>    * Verkeers selectie (als UsePolicyBasedTrafficSelectors wordt gebruikt)
>    * De SA-levensduren zijn alleen lokale specificaties en hoeven niet overeen te komen.
>
> 2. **Als GCMAES wordt gebruikt als voor IPsec-versleutelings algoritme, moet u hetzelfde GCMAES-algoritme en dezelfde sleutel lengte voor IPsec-integriteit selecteren. Als u bijvoorbeeld GCMAES128 gebruikt voor beide**
> 3. In de bovenstaande tabel:
>    * IKEv2 komt overeen met de hoofd modus of fase 1
>    * IPsec komt overeen met snelle modus of fase 2
>    * DH-groep Hiermee geeft u de Diffie-Hellmen groep op die in de hoofd modus of fase 1 wordt gebruikt
>    * PFS-groep opgegeven de Diffie-Hellmen groep die wordt gebruikt in de snelle modus of fase 2
> 4. SA-levensduur voor IKEv2 Main Mode staat vastgesteld op 28.800 seconden op de Azure VPN-gateways
> 5. Als u ' UsePolicyBasedTrafficSelectors ' instelt op $True voor een verbinding, wordt de Azure VPN-gateway geconfigureerd om verbinding te maken met op beleid gebaseerde VPN-Firewall on-premises. Als u PolicyBasedTrafficSelectors inschakelt, moet u ervoor zorgen dat op uw VPN-apparaat de overeenkomende verkeers selectie is gedefinieerd met alle combi Naties van de voor voegsels van uw on-premises netwerk (lokale netwerk gateway) naar/van de voor voegsels van het virtuele Azure-netwerk (in plaats van any). Als uw lokale netwerkvoorvoegsels bijvoorbeeld 10.1.0.0/16 en 10.2.0.0/16 zijn, en de voorvoegsels van uw virtuele netwerk 192.168.0.0/16 en 172.16.0.0/16, moet u de volgende verkeersselectoren opgeven:
>    * 10.1.0.0/16 <====> 192.168.0.0/16
>    * 10.1.0.0/16 <====> 172.16.0.0/16
>    * 10.2.0.0/16 <====> 192.168.0.0/16
>    * 10.2.0.0/16 <====> 172.16.0.0/16

Zie voor meer informatie over op beleid gebaseerde verkeers selecties [verbinding maken met meerdere on-premises op beleid gebaseerde VPN-apparaten](vpn-gateway-connect-multiple-policybased-rm-ps.md).

De volgende tabel bevat de overeenkomende Diffie-Hellman groepen die worden ondersteund door het aangepaste beleid:

| **Diffie-Hellman-groep**  | **DHGroup**              | **PFSGroup** | **Sleutellengte** |
| --- | --- | --- | --- |
| 1                         | DHGroup1                 | PFS1         | 768-bits MODP   |
| 2                         | DHGroup2                 | PFS2         | 1024-bits MODP  |
| 14                        | DHGroup14<br>DHGroup2048 | PFS2048      | 2048-bits MODP  |
| 19                        | ECP256                   | ECP256       | 256-bits ECP    |
| 20                        | ECP384                   | ECP384       | 384-bits ECP    |
| 24                        | DHGroup24                | PFS24        | 2048-bits MODP  |

Raadpleeg [RFC3526](https://tools.ietf.org/html/rfc3526) en [RFC5114](https://tools.ietf.org/html/rfc5114) voor meer informatie.

## <a name="part-3---create-a-new-s2s-vpn-connection-with-ipsecike-policy"></a><a name ="crossprem"></a>Deel 3: een nieuwe S2S-VPN-verbinding maken met het IPsec/IKE-beleid

In deze sectie worden de stappen beschreven voor het maken van een S2S-VPN-verbinding met een IPsec/IKE-beleid. Met de volgende stappen maakt u de verbinding, zoals weer gegeven in het diagram:

![S2S-beleid](./media/vpn-gateway-ipsecikepolicy-rm-powershell/s2spolicy.png)

Zie [een S2S-VPN-verbinding maken](vpn-gateway-create-site-to-site-rm-powershell.md) voor meer gedetailleerde stapsgewijze instructies voor het maken van een S2S-VPN-verbinding.

### <a name="before-you-begin"></a><a name="before"></a>Voordat u begint

* Controleer of u een Azure-abonnement hebt. Als u nog geen Azure-abonnement hebt, kunt u [uw voordelen als MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of [u aanmelden voor een gratis account](https://azure.microsoft.com/pricing/free-trial/).
* Installeer de Azure Resource Manager Power shell-cmdlets. Zie [overzicht van Azure PowerShell](/powershell/azure/) voor meer informatie over het installeren van de Power shell-cmdlets.

### <a name="step-1---create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a><a name="createvnet1"></a>Stap 1: het virtuele netwerk, de VPN-gateway en de lokale netwerk gateway maken

#### <a name="1-declare-your-variables"></a>1. Declareer de variabelen

Voor deze oefening beginnen we met het declareren van onze variabelen. Zorg dat u de waarden door uw eigen waarden vervangt wanneer u configureert voor productie.

```powershell
$Sub1          = "<YourSubscriptionName>"
$RG1           = "TestPolicyRG1"
$Location1     = "East US 2"
$VNetName1     = "TestVNet1"
$FESubName1    = "FrontEnd"
$BESubName1    = "Backend"
$GWSubName1    = "GatewaySubnet"
$VNetPrefix11  = "10.11.0.0/16"
$VNetPrefix12  = "10.12.0.0/16"
$FESubPrefix1  = "10.11.0.0/24"
$BESubPrefix1  = "10.12.0.0/24"
$GWSubPrefix1  = "10.12.255.0/27"
$DNS1          = "8.8.8.8"
$GWName1       = "VNet1GW"
$GW1IPName1    = "VNet1GWIP1"
$GW1IPconf1    = "gw1ipconf1"
$Connection16  = "VNet1toSite6"

$LNGName6      = "Site6"
$LNGPrefix61   = "10.61.0.0/16"
$LNGPrefix62   = "10.62.0.0/16"
$LNGIP6        = "131.107.72.22"
```

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. Maak verbinding met uw abonnement en maak een nieuwe resource groep

Zorg ervoor dat u overschakelt naar de PowerShell-modus als u de Resource Manager-cmdlets wilt gebruiken. Zie [Using Windows PowerShell with Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md) (Windows PowerShell gebruiken met Resource Manager) voor meer informatie.

Open de PowerShell-console en maak verbinding met uw account. Gebruik het volgende voorbeeld als hulp bij het maken van de verbinding:

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $Sub1
New-AzResourceGroup -Name $RG1 -Location $Location1
```

#### <a name="3-create-the-virtual-network-vpn-gateway-and-local-network-gateway"></a>3. het virtuele netwerk, de VPN-gateway en de lokale netwerk gateway maken

In het volgende voor beeld wordt het virtuele netwerk, TestVNet1, met drie subnetten en de VPN-gateway gemaakt. Wanneer u de waarden vervangt, is het belangrijk dat u de juiste namen voor de gatewaysubnets gebruikt, in het bijzonder GatewaySubnet. Als u een andere naam kiest, mislukt het maken van de gateway.

```powershell
$fesub1 = New-AzVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
$besub1 = New-AzVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
$gwsub1 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

New-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

$gw1pip1    = New-AzPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
$vnet1      = Get-AzVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
$subnet1    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
$gw1ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1

New-AzVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku VpnGw1

New-AzLocalNetworkGateway -Name $LNGName6 -ResourceGroupName $RG1 -Location $Location1 -GatewayIpAddress $LNGIP6 -AddressPrefix $LNGPrefix61,$LNGPrefix62
```

### <a name="step-2---create-a-s2s-vpn-connection-with-an-ipsecike-policy"></a><a name="s2sconnection"></a>Stap 2: een S2S-VPN-verbinding maken met een IPsec/IKE-beleid

#### <a name="1-create-an-ipsecike-policy"></a>1. een IPsec/IKE-beleid maken

Met het volgende voorbeeld script maakt u een IPsec/IKE-beleid met de volgende algoritmen en para meters:

* IKEv2: AES256, SHA384, DHGroup24
* IPsec: AES256, SHA256, PFS geen, SA-levens duur van 14400 seconden & 102400000KB

```powershell
$ipsecpolicy6 = New-AzIpsecPolicy -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup24 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

Als u GCMAES voor IPsec gebruikt, moet u hetzelfde GCMAES-algoritme en dezelfde sleutel lengte gebruiken voor IPsec-versleuteling en-integriteit. De overeenkomstige para meters zijn bijvoorbeeld '-IpsecEncryption GCMAES256-IpsecIntegrity GCMAES256 ' bij gebruik van GCMAES256.

#### <a name="2-create-the-s2s-vpn-connection-with-the-ipsecike-policy"></a>2. de S2S-VPN-verbinding met het IPsec/IKE-beleid maken

Maak een S2S-VPN-verbinding en pas het IPsec/IKE-beleid toe dat u eerder hebt gemaakt.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$lng6 = Get-AzLocalNetworkGateway  -Name $LNGName6 -ResourceGroupName $RG1

New-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng6 -Location $Location1 -ConnectionType IPsec -IpsecPolicies $ipsecpolicy6 -SharedKey 'AzureA1b2C3'
```

U kunt eventueel '-UsePolicyBasedTrafficSelectors $True ' toevoegen aan de cmdlet Connection maken om Azure VPN gateway in te scha kelen om verbinding te maken met op beleid gebaseerde VPN-apparaten op locatie, zoals hierboven wordt beschreven.

> [!IMPORTANT]
> Zodra een IPsec/IKE-beleid is opgegeven voor een verbinding, verzendt of accepteert de Azure VPN-gateway alleen het IPsec/IKE-voor stel met de opgegeven cryptografische algoritmen en sleutel sterkten op die specifieke verbinding. Zorg ervoor dat uw on-premises VPN-apparaat voor de verbinding de nauw keurige combi natie van het beleid gebruikt of accepteert, anders zal de S2S VPN-tunnel niet tot stand worden gebracht.


## <a name="part-4---create-a-new-vnet-to-vnet-connection-with-ipsecike-policy"></a><a name ="vnet2vnet"></a>Deel 4: een nieuwe VNet-naar-VNet-verbinding maken met het IPsec/IKE-beleid

De stappen voor het maken van een VNet-naar-VNet-verbinding met een IPsec/IKE-beleid zijn vergelijkbaar met die van een S2S-VPN-verbinding. De volgende voorbeeld scripts maken de verbinding, zoals weer gegeven in het diagram:

![V2V-beleid](./media/vpn-gateway-ipsecikepolicy-rm-powershell/v2vpolicy.png)

Zie [een vnet-naar-vnet-verbinding maken](vpn-gateway-vnet-vnet-rm-ps.md) voor meer gedetailleerde stappen voor het maken van een vnet-naar-vnet-verbinding. U moet [deel 3](#crossprem) volt ooien om TestVNet1 en de VPN gateway te maken en te configureren.

### <a name="step-1---create-the-second-virtual-network-and-vpn-gateway"></a><a name="createvnet2"></a>Stap 1: het tweede virtuele netwerk en de VPN-gateway maken

#### <a name="1-declare-your-variables"></a>1. Declareer de variabelen

Zorg ervoor dat u de waarden vervangt door de waarden die u voor uw configuratie wilt gebruiken.

```powershell
$RG2          = "TestPolicyRG2"
$Location2    = "East US 2"
$VNetName2    = "TestVNet2"
$FESubName2   = "FrontEnd"
$BESubName2   = "Backend"
$GWSubName2   = "GatewaySubnet"
$VNetPrefix21 = "10.21.0.0/16"
$VNetPrefix22 = "10.22.0.0/16"
$FESubPrefix2 = "10.21.0.0/24"
$BESubPrefix2 = "10.22.0.0/24"
$GWSubPrefix2 = "10.22.255.0/27"
$DNS2         = "8.8.8.8"
$GWName2      = "VNet2GW"
$GW2IPName1   = "VNet2GWIP1"
$GW2IPconf1   = "gw2ipconf1"
$Connection21 = "VNet2toVNet1"
$Connection12 = "VNet1toVNet2"
```

#### <a name="2-create-the-second-virtual-network-and-vpn-gateway-in-the-new-resource-group"></a>2. Maak het tweede virtuele netwerk en de VPN-gateway in de nieuwe resource groep

```powershell
New-AzResourceGroup -Name $RG2 -Location $Location2

$fesub2 = New-AzVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
$besub2 = New-AzVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
$gwsub2 = New-AzVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

New-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

$gw2pip1    = New-AzPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
$vnet2      = Get-AzVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
$subnet2    = Get-AzVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
$gw2ipconf1 = New-AzVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1

New-AzVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance
```

### <a name="step-2---create-a-vnet-tovnet-connection-with-the-ipsecike-policy"></a>Stap 2: een VNet-toVNet-verbinding maken met het IPsec/IKE-beleid

Net als bij de S2S-VPN-verbinding maakt u een IPsec/IKE-beleid en vervolgens op beleid Toep assen op de nieuwe verbinding.

#### <a name="1-create-an-ipsecike-policy"></a>1. een IPsec/IKE-beleid maken

Het volgende voorbeeldscript maakt een ander beleid voor IPsec/IKE met de volgende algoritmen en parameters:
* IKEv2: AES128, SHA1, DHGroup14
* IPsec: GCMAES128, GCMAES128, PFS14, levens duur van 14400 seconden & 102400000KB

```powershell
$ipsecpolicy2 = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption GCMAES128 -IpsecIntegrity GCMAES128 -PfsGroup PFS14 -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000
```

#### <a name="2-create-vnet-to-vnet-connections-with-the-ipsecike-policy"></a>2. VNet-naar-VNet-verbindingen maken met het IPsec/IKE-beleid

Maak een VNet-naar-VNet-verbinding en pas het IPsec/IKE-beleid toe dat u hebt gemaakt. In dit voor beeld bevinden beide gateways zich in hetzelfde abonnement. Het is dus mogelijk beide verbindingen met hetzelfde IPsec/IKE-beleid in dezelfde Power shell-sessie te maken en configureren.

```powershell
$vnet1gw = Get-AzVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
$vnet2gw = Get-AzVirtualNetworkGateway -Name $GWName2  -ResourceGroupName $RG2

New-AzVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'

New-AzVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -IpsecPolicies $ipsecpolicy2 -SharedKey 'AzureA1b2C3'
```

> [!IMPORTANT]
> Zodra een IPsec/IKE-beleid is opgegeven voor een verbinding, verzendt of accepteert de Azure VPN-gateway alleen het IPsec/IKE-voor stel met de opgegeven cryptografische algoritmen en sleutel sterkten op die specifieke verbinding. Zorg ervoor dat het IPsec-beleid voor beide verbindingen hetzelfde is, anders zal de VNet-naar-VNet-verbinding niet tot stand worden gebracht.

Nadat u deze stappen hebt voltooid, wordt de verbinding in een paar minuten tot stand gebracht en hebt u de volgende netwerk topologie, zoals in het begin:

![IPSec-IKE-beleid](./media/vpn-gateway-ipsecikepolicy-rm-powershell/ipsecikepolicy.png)


## <a name="part-5---update-ipsecike-policy-for-a-connection"></a><a name ="managepolicy"></a>Deel 5: het IPsec/IKE-beleid voor een verbinding bijwerken

In de laatste sectie ziet u hoe u IPsec/IKE-beleid beheert voor een bestaande S2S-of VNet-naar-VNet-verbinding. De onderstaande oefening begeleidt u bij de volgende bewerkingen op een verbinding:

1. Het IPsec/IKE-beleid van een verbinding weer geven
2. Het IPsec/IKE-beleid toevoegen of bijwerken aan een verbinding
3. Het IPsec/IKE-beleid uit een verbinding verwijderen

Dezelfde stappen zijn van toepassing op S2S-en VNet-naar-VNet-verbindingen.

> [!IMPORTANT]
> IPsec/IKE-beleid wordt alleen ondersteund op *standaard* en *High Performance* VPN-gateways op basis van route ring. Het werkt niet op de Basic gateway-SKU of de op beleid gebaseerde VPN-gateway.

#### <a name="1-show-the-ipsecike-policy-of-a-connection"></a>1. het IPsec/IKE-beleid van een verbinding weer geven

In het volgende voor beeld ziet u hoe u het IPsec/IKE-beleid kunt ophalen dat is geconfigureerd voor een verbinding. De scripts worden ook door lopen van de bovenstaande oefeningen.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

Met de laatste opdracht wordt een lijst weer gegeven met het huidige IPsec/IKE-beleid dat op de verbinding is geconfigureerd, indien aanwezig. Hier volgt een voor beeld van de uitvoer voor de verbinding:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES256
IkeIntegrity        : SHA384
DhGroup             : DHGroup24
PfsGroup            : PFS24
```

Als er geen IPsec/IKE-beleid is geconfigureerd, wordt de opdracht (PS> $connection 6. IpsecPolicies) haalt een lege retour waarde op. Dit betekent niet dat IPsec/IKE niet is geconfigureerd voor de verbinding, maar dat er geen aangepast IPsec/IKE-beleid is. De werkelijke verbinding maakt gebruik van het standaard beleid dat wordt onderhandeld tussen uw on-premises VPN-apparaat en de Azure VPN-gateway.

#### <a name="2-add-or-update-an-ipsecike-policy-for-a-connection"></a>2. een IPsec/IKE-beleid voor een verbinding toevoegen of bijwerken

De stappen voor het toevoegen van een nieuw beleid of het bijwerken van een bestaand beleid voor een verbinding zijn hetzelfde: Maak een nieuw beleid en pas het nieuwe beleid toe op de verbinding.

```powershell
$RG1          = "TestPolicyRG1"
$Connection16 = "VNet1toSite6"
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$newpolicy6   = New-AzIpsecPolicy -IkeEncryption AES128 -IkeIntegrity SHA1 -DhGroup DHGroup14 -IpsecEncryption AES256 -IpsecIntegrity SHA256 -PfsGroup None -SALifeTimeSeconds 14400 -SADataSizeKilobytes 102400000

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6
```

Als u ' UsePolicyBasedTrafficSelectors ' wilt inschakelen wanneer u verbinding maakt met een on-premises VPN-apparaat op basis van beleid, voegt u de para meter '-UsePolicyBaseTrafficSelectors ' toe aan de cmdlet of stelt u deze in op $False om de optie uit te scha kelen:

```powershell
Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6 -IpsecPolicies $newpolicy6 -UsePolicyBasedTrafficSelectors $True
```

U kunt de verbinding opnieuw verkrijgen om te controleren of het beleid is bijgewerkt.

```powershell
$connection6  = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1
$connection6.IpsecPolicies
```

U ziet de uitvoer van de laatste regel, zoals wordt weer gegeven in het volgende voor beeld:

```powershell
SALifeTimeSeconds   : 14400
SADataSizeKilobytes : 102400000
IpsecEncryption     : AES256
IpsecIntegrity      : SHA256
IkeEncryption       : AES128
IkeIntegrity        : SHA1
DhGroup             : DHGroup14
PfsGroup            : None
```

#### <a name="3-remove-an-ipsecike-policy-from-a-connection"></a>3. een IPsec/IKE-beleid uit een verbinding verwijderen

Wanneer u het aangepaste beleid van een verbinding verwijdert, wordt de Azure VPN-gateway teruggezet naar de [standaard lijst met IPSec/IKE-Voorst Ellen](vpn-gateway-about-vpn-devices.md) en opnieuw onderhandeld met uw on-PREMISES VPN-apparaat.

```powershell
$RG1           = "TestPolicyRG1"
$Connection16  = "VNet1toSite6"
$connection6   = Get-AzVirtualNetworkGatewayConnection -Name $Connection16 -ResourceGroupName $RG1

$currentpolicy = $connection6.IpsecPolicies[0]
$connection6.IpsecPolicies.Remove($currentpolicy)

Set-AzVirtualNetworkGatewayConnection -VirtualNetworkGatewayConnection $connection6
```

U kunt hetzelfde script gebruiken om te controleren of het beleid is verwijderd uit de verbinding.

## <a name="next-steps"></a>Volgende stappen

Zie [verbinding maken met meerdere on-premises op beleid gebaseerde VPN-apparaten](vpn-gateway-connect-multiple-policybased-rm-ps.md) voor meer informatie over op beleid gebaseerde verkeers selecties.

Wanneer de verbinding is voltooid, kunt u virtuele machines aan uw virtuele netwerken toevoegen. Zie [Een virtuele machine maken](../virtual-machines/windows/quick-create-portal.md) voor de stappen.