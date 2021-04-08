---
title: 'Klassieke virtuele netwerken verbinden met Azure Resource Manager VNets: Power shell'
description: Maak een VPN-verbinding tussen de klassieke VNets-en Resource Manager-VNets met behulp van VPN Gateway en Power shell.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: 7012a696684a4e7bc1d3b52943b11ba413c43037
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100376485"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-powershell"></a>Virtuele netwerken van verschillende implementatiemodellen verbinden met PowerShell

Dit artikel helpt u om de klassieke VNets te verbinden met Resource Manager VNets, zodat de resources in de afzonderlijke implementatie modellen kunnen communiceren met elkaar. In de stappen in dit artikel wordt Power shell gebruikt, maar u kunt deze configuratie ook maken met behulp van de Azure Portal door het artikel in deze lijst te selecteren.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Het verbinden van een klassiek VNet met een resource manager VNet is vergelijkbaar met het verbinden van een VNet met een on-premises site locatie. Voor beide connectiviteitstypen wordt een VPN-gateway gebruikt om een beveiligde tunnel met IPsec/IKE te bieden. U kunt een verbinding maken tussen VNets die zich in verschillende abonnementen en in verschillende regio's bevinden. U kunt ook verbinding maken met VNets die al verbindingen met on-premises netwerken hebben, zolang de gateway die ze hebben geconfigureerd, dynamisch is of op route gebaseerd is. Zie voor meer informatie over verbindingen tussen VNets de [Veelgestelde vragen over VNet-naar-VNet](#faq) aan het einde van dit artikel. 

Als u nog geen virtuele netwerk gateway hebt en u deze niet wilt maken, kunt u in plaats daarvan overwegen uw VNets te koppelen met behulp van VNet-peering. Bij VNet-peering wordt geen VPN-gateway gebruikt. Zie het artikel [VNet-peering](../virtual-network/virtual-network-peering-overview.md) voor meer informatie.

## <a name="before-you-begin"></a><a name="before"></a>Voordat u begint

De volgende stappen leiden u door de instellingen die nodig zijn voor het configureren van een dynamische of route-gebaseerde gateway voor elk VNet en het maken van een VPN-verbinding tussen de gateways. Deze configuratie biedt geen ondersteuning voor statische of op beleid gebaseerde gateways.

### <a name="prerequisites"></a><a name="pre"></a>Vereisten

* Beide VNets zijn al gemaakt. Als u een virtueel netwerk van Resource Manager wilt maken, raadpleegt u [een resource groep en een virtueel netwerk maken](../virtual-network/quick-create-powershell.md#create-a-resource-group-and-a-virtual-network). Zie [een klassiek VNet maken](/previous-versions/azure/virtual-network/create-virtual-network-classic)voor het maken van een klassiek virtueel netwerk.
* De adresbereiken voor de VNets overlappen elkaar niet of overlappen een van de bereiken voor andere verbindingen waarmee de gateways kunnen worden verbonden.
* U hebt de meest recente Power shell-cmdlets geïnstalleerd. Zie [Azure PowerShell installeren en configureren](/powershell/azure/) voor meer informatie. Zorg ervoor dat u de Service Management (SM) en de RM-cmdlets (Resource Manager) installeert. 

### <a name="example-settings"></a><a name="exampleref"></a>Voorbeeldinstellingen

U kunt deze waarden gebruiken om een testomgeving te maken of ze raadplegen om meer inzicht te krijgen in de voorbeelden in dit artikel.

**Klassieke VNet-instellingen**

VNet-naam = ClassicVNet <br>
Locatie = vs-West <br>
Virtual Network adres ruimten = 10.0.0.0/24 <br>
Subnet-1 = 10.0.0.0/27 <br>
GatewaySubnet = 10.0.0.32/29 <br>
Lokale netwerk naam = RMVNetLocal <br>
Gateway type = DynamicRouting

**VNet-instellingen van Resource Manager**

VNet-naam = RMVNet <br>
Resource groep = RG1 <br>
Virtual Network IP-adres ruimten = 192.168.0.0/16 <br>
Subnet-1 = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Locatie = VS-Oost <br>
Open bare IP-naam van Gateway = gwpip <br>
Lokale netwerk gateway = ClassicVNetLocal <br>
Virtual Network gateway naam = RMGateway <br>
IP-adres Sering configuratie van Gateway = gwipconfig

## <a name="section-1---configure-the-classic-vnet"></a><a name="createsmgw"></a>Sectie 1: het klassieke VNet configureren
### <a name="1-download-your-network-configuration-file"></a>1. down load het netwerk configuratie bestand
1. Meld u aan bij uw Azure-account in de Power shell-console met verhoogde bevoegdheden. Met de volgende cmdlet wordt u gevraagd om de aanmeldings referenties voor uw Azure-account. Na het aanmelden, worden de instellingen van uw account gedownload zodat ze beschikbaar zijn voor Azure PowerShell. De cmdlets van het klassieke Service beheer (SM) Azure PowerShell worden gebruikt in deze sectie.

   ```azurepowershell
   Add-AzureAccount
   ```

   Down load uw Azure-abonnement.

   ```azurepowershell
   Get-AzureSubscription
   ```

   Als u meerdere abonnementen hebt, selecteert u het abonnement dat u wilt gebruiken.

   ```azurepowershell
   Select-AzureSubscription -SubscriptionName "Name of subscription"
   ```
2. Exporteer uw Azure-netwerk configuratie bestand door de volgende opdracht uit te voeren. Als dat nodig is, kunt u de locatie van het bestand wijzigen zodat het naar een andere locatie kan worden geëxporteerd.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
3. Open het. XML-bestand dat u hebt gedownload om het te bewerken. Zie het [netwerk configuratie schema](/previous-versions/azure/reference/jj157100(v=azure.100))voor een voor beeld van het netwerk configuratie bestand.

### <a name="2-verify-the-gateway-subnet"></a>2. Controleer het gateway-subnet
Voeg in het element **VirtualNetworkSites** een gateway-subnet toe aan uw VNet als er nog geen is gemaakt. Wanneer u werkt met het netwerk configuratie bestand, moet het gateway-subnet de naam ' GatewaySubnet ' hebben of kan Azure niet worden herkend en als gateway-subnet worden gebruikt.

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

**Voorbeeld:**

```xml
<VirtualNetworkSites>
  <VirtualNetworkSite name="ClassicVNet" Location="West US">
    <AddressSpace>
      <AddressPrefix>10.0.0.0/24</AddressPrefix>
    </AddressSpace>
    <Subnets>
      <Subnet name="Subnet-1">
        <AddressPrefix>10.0.0.0/27</AddressPrefix>
      </Subnet>
      <Subnet name="GatewaySubnet">
        <AddressPrefix>10.0.0.32/29</AddressPrefix>
      </Subnet>
    </Subnets>
  </VirtualNetworkSite>
</VirtualNetworkSites>
```

### <a name="3-add-the-local-network-site"></a>3. de lokale netwerk site toevoegen
De lokale netwerk site die u toevoegt, vertegenwoordigt de RM-VNet waarmee u verbinding wilt maken. Voeg een **LocalNetworkSites** -element toe aan het bestand als er nog geen bestaat. Op dit punt in de configuratie kan het VPNGatewayAddress een geldig openbaar IP-adres zijn, omdat de gateway nog niet is gemaakt voor het Resource Manager-VNet. Zodra de gateway is gemaakt, vervangen we het IP-adres van de tijdelijke aanduiding door het juiste open bare IP-adres dat is toegewezen aan de RM-gateway.

```xml
<LocalNetworkSites>
  <LocalNetworkSite name="RMVNetLocal">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
  </LocalNetworkSite>
</LocalNetworkSites>
```

### <a name="4-associate-the-vnet-with-the-local-network-site"></a>4. het VNet koppelen aan de lokale netwerk site
In deze sectie geven we de lokale netwerk site op waarmee u het VNet wilt verbinden. In dit geval is het de Resource Manager VNet waarnaar u eerder verwijst. Zorg ervoor dat de namen overeenkomen. Met deze stap maakt u geen gateway. Hiermee geeft u het lokale netwerk op waarmee de gateway verbinding maakt.

```xml
<Gateway>
  <ConnectionsToLocalNetwork>
    <LocalNetworkSiteRef name="RMVNetLocal">
      <Connection type="IPsec" />
    </LocalNetworkSiteRef>
  </ConnectionsToLocalNetwork>
</Gateway>
```

### <a name="5-save-the-file-and-upload"></a>5. Sla het bestand op en upload het
Sla het bestand op en importeer het naar Azure door de volgende opdracht uit te voeren. Zorg ervoor dat u het bestandspad zo nodig wijzigt voor uw omgeving.

```azurepowershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Er wordt een vergelijkbaar resultaat weer gegeven waarin wordt aangegeven dat het importeren is geslaagd.

```output
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                                
Set-AzureVNetConfig        e0ee6e66-9167-cfa7-a746-7casb9    Succeeded 
```

### <a name="6-create-the-gateway"></a>6. de gateway maken

Voordat u dit voor beeld uitvoert, raadpleegt u het netwerk configuratie bestand dat u hebt gedownload voor de exacte namen die Azure verwacht te zien. Het netwerk configuratie bestand bevat de waarden voor uw klassieke virtuele netwerken. Soms worden de namen voor klassieke VNets gewijzigd in het netwerk configuratie bestand bij het maken van klassieke VNet-instellingen in de Azure Portal vanwege de verschillen in de implementatie modellen. Als u bijvoorbeeld de Azure Portal hebt gebruikt voor het maken van een klassiek VNet met de naam klassiek VNet en dit hebt gemaakt in een resource groep met de naam ClassicRG, wordt de naam die is opgenomen in het netwerk configuratie bestand, geconverteerd naar groep ClassicRG klassiek VNet. Gebruik aanhalings tekens rond de waarde wanneer u de naam opgeeft van een VNet dat spaties bevat.


Gebruik het volgende voor beeld om een dynamische routerings gateway te maken:

```azurepowershell
New-AzureVNetGateway -VNetName ClassicVNet -GatewayType DynamicRouting
```

U kunt de status van de gateway controleren met behulp van de cmdlet **Get-azurevnetgateway gebruikt** .

## <a name="section-2---configure-the-rm-vnet-gateway"></a><a name="creatermgw"></a>Sectie 2: de RM VNet-gateway configureren



Aan de vereisten wordt ervan uitgegaan dat u al een RM VNet hebt gemaakt. In deze stap maakt u een VPN-gateway voor het RM-VNet. Start deze stappen pas nadat u het open bare IP-adres voor de klassieke VNet-gateway hebt opgehaald. 

1. Meld u aan bij uw Azure-account in de Power shell-console. Met de volgende cmdlet wordt u gevraagd om de aanmeldings referenties voor uw Azure-account. Nadat u zich hebt aangemeld, worden uw account instellingen gedownload zodat ze beschikbaar zijn voor Azure PowerShell. U kunt eventueel de functie ' try it ' gebruiken om Azure Cloud Shell in de browser te starten.

   Als u Azure Cloud Shell gebruikt, slaat u de volgende cmdlet over:

   ```azurepowershell
   Connect-AzAccount
   ``` 
   Als u wilt controleren of u het juiste abonnement gebruikt, voert u de volgende cmdlet uit:  

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
   
   Als u meer dan één abonnement hebt, geeft u het abonnement op dat u wilt gebruiken.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
2. Maak een lokale netwerkgateway. In een virtueel netwerk verwijst de lokale netwerkgateway doorgaans naar uw on-premises locatie. In dit geval verwijst de lokale netwerk gateway naar uw klassieke VNet. Geef een naam op waarmee Azure ernaar kan verwijzen en geef ook het adres ruimte voorvoegsel op. Azure gebruikt het IP-adresvoorvoegsel dat u opgeeft om te bepalen welk verkeer naar uw on-premises locatie moet worden verzonden. Als u de gegevens later opnieuw moet aanpassen voordat u uw gateway maakt, kunt u de waarden wijzigen en het voor beeld opnieuw uitvoeren.
   
   **-Naam** is de naam die u wilt toewijzen om te verwijzen naar de lokale netwerk gateway.<br>
   **-AddressPrefix** is de adres ruimte voor uw klassieke VNet.<br>
   **-GatewayIpAddress** is het open bare IP-adres van de klassieke VNet-gateway. Zorg ervoor dat u de volgende voorbeeld tekst ' n. n. n. n ' wijzigt, zodat het juiste IP-adres wordt weer gegeven.<br>

   ```azurepowershell-interactive
   New-AzLocalNetworkGateway -Name ClassicVNetLocal `
   -Location "West US" -AddressPrefix "10.0.0.0/24" `
   -GatewayIpAddress "n.n.n.n" -ResourceGroupName RG1
   ```
3. Vraag een openbaar IP-adres aan dat moet worden toegewezen aan de virtuele netwerk gateway voor het Resource Manager-VNet. U kunt het IP-adres dat u wilt gebruiken niet zelf opgeven. Het IP-adres wordt dynamisch toegewezen aan de gateway van het virtuele netwerk. Dit betekent echter niet dat het IP-adres verandert. De enige keer dat het IP-adres van de gateway van het virtuele netwerk verandert wanneer de gateway wordt verwijderd en opnieuw wordt gemaakt. Het wijzigen van het formaat, het opnieuw instellen of andere interne onderhouds-en-upgrades van de gateway wordt niet gewijzigd.

   In deze stap stellen we ook een variabele in die wordt gebruikt in een latere stap.

   ```azurepowershell-interactive
   $ipaddress = New-AzPublicIpAddress -Name gwpip `
   -ResourceGroupName RG1 -Location 'EastUS' `
   -AllocationMethod Dynamic
   ```

4. Controleer of het virtuele netwerk een gateway-subnet heeft. Als er geen gateway-subnet bestaat, voegt u er een toe. Controleer of het gateway-subnet de naam *GatewaySubnet* heeft.
5. Haal het subnet op dat voor de gateway wordt gebruikt door de volgende opdracht uit te voeren. In deze stap stellen we ook een variabele in die in de volgende stap moet worden gebruikt.
   
   **-Naam** is de naam van uw Resource Manager-VNet.<br>
   **-ResourceGroupName** is de resource groep waaraan het VNet is gekoppeld. Het gateway-subnet moet al bestaan voor dit VNet en moet de naam *GatewaySubnet* hebben om goed te kunnen werken.<br>

   ```azurepowershell-interactive
   $subnet = Get-AzVirtualNetworkSubnetConfig -Name GatewaySubnet `
   -VirtualNetwork (Get-AzVirtualNetwork -Name RMVNet -ResourceGroupName RG1)
   ``` 

6. De IP-adres configuratie van de gateway maken. De gatewayconfiguratie bepaalt welk subnet en openbaar IP-adres moeten worden gebruikt. Gebruik het volgende voorbeeld om de gatewayconfiguratie te maken.

   In deze stap moeten de para meters **-SubnetId** en **-PublicIpAddressId** worden door gegeven aan respectievelijk de id-eigenschap van het subnet en de IP-adres objecten. U kunt geen eenvoudige teken reeks gebruiken. Deze variabelen worden ingesteld in de stap voor het aanvragen van een openbaar IP-adres en de stap voor het ophalen van het subnet.

   ```azurepowershell-interactive
   $gwipconfig = New-AzVirtualNetworkGatewayIpConfig `
   -Name gwipconfig -SubnetId $subnet.id `
   -PublicIpAddressId $ipaddress.id
   ```
7. Maak de gateway van het virtuele netwerk van Resource Manager door de volgende opdracht uit te voeren. De `-VpnType` moet *RouteBased* zijn. Het kan 45 minuten of langer duren voordat de gateway is gemaakt.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1 `
   -Location "EastUS" -GatewaySKU Standard -GatewayType Vpn `
   -IpConfigurations $gwipconfig `
   -EnableBgp $false -VpnType RouteBased
   ```
8. Kopieer het open bare IP-adres zodra de VPN-gateway is gemaakt. U gebruikt deze bij het configureren van de instellingen van het lokale netwerk voor uw klassieke VNet. U kunt de volgende cmdlet gebruiken om het open bare IP-adres op te halen. Het open bare IP-adres wordt weer gegeven in het resultaat als *IpAddress*.

   ```azurepowershell-interactive
   Get-AzPublicIpAddress -Name gwpip -ResourceGroupName RG1
   ```

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="localsite"></a>Sectie 3: de instellingen van de klassieke VNet-lokale site wijzigen

In deze sectie werkt u met het klassieke VNet. U vervangt het tijdelijke IP-adres dat u hebt gebruikt bij het opgeven van de lokale site-instellingen die worden gebruikt om verbinding te maken met de Resource Manager VNet-gateway. Omdat u met het klassieke VNet werkt, gebruikt u Power shell lokaal geïnstalleerd op uw computer, niet de Azure Cloud Shell TryIt.

1. Exporteer het netwerk configuratie bestand.

   ```azurepowershell
   Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
   ```
2. Wijzig de waarde voor VPNGatewayAddress met behulp van een tekst editor. Vervang het tijdelijke IP-adres door het open bare IP-adres van de Resource Manager-gateway en sla de wijzigingen op.

   ```
   <VPNGatewayAddress>13.68.210.16</VPNGatewayAddress>
   ```
3. Importeer het gewijzigde netwerk configuratie bestand in Azure.

   ```azurepowershell
   Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
   ```

## <a name="section-4---create-a-connection-between-the-gateways"></a><a name="connect"></a>Sectie 4: een verbinding maken tussen de gateways
Voor het maken van een verbinding tussen de gateways is Power shell vereist. Mogelijk moet u uw Azure-account toevoegen voor gebruik van de klassieke versie van de Power shell-cmdlets. Gebruik hiervoor **add-AzureAccount**.

1. Stel uw gedeelde sleutel in de Power shell-console in. Voordat u de cmdlets uitvoert, raadpleegt u het netwerk configuratie bestand dat u hebt gedownload voor de exacte namen die Azure verwacht te zien. Wanneer u de naam van een VNet opgeeft dat spaties bevat, gebruikt u enkele aanhalings tekens rond de waarde.<br><br>In het volgende voor beeld is **-VNetName** de naam van het klassieke VNet en **-LocalNetworkSiteName** is de naam die u hebt opgegeven voor de lokale netwerk site. De **-SharedKey** is een waarde die u genereert en opgeeft. In het voor beeld hebben we ' abc123 ' gebruikt, maar u kunt een complexere waarde genereren en gebruiken. Het belangrijkste is dat de waarde die u hier opgeeft, dezelfde waarde moet zijn die u in de volgende stap opgeeft wanneer u de verbinding maakt. De retour moet status weer geven **: geslaagd**.

   ```azurepowershell
   Set-AzureVNetGatewayKey -VNetName ClassicVNet `
   -LocalNetworkSiteName RMVNetLocal -SharedKey abc123
   ```
2. Maak de VPN-verbinding door de volgende opdrachten uit te voeren:
   
   Stel de variabelen in.

   ```azurepowershell-interactive
   $vnet01gateway = Get-AzLocalNetworkGateway -Name ClassicVNetLocal -ResourceGroupName RG1
   $vnet02gateway = Get-AzVirtualNetworkGateway -Name RMGateway -ResourceGroupName RG1
   ```
   
   Maak de verbinding. U ziet dat de **-Connection type** IPSec is, niet Vnet2Vnet.

   ```azurepowershell-interactive
   New-AzVirtualNetworkGatewayConnection -Name RM-Classic -ResourceGroupName RG1 `
   -Location "East US" -VirtualNetworkGateway1 `
   $vnet02gateway -LocalNetworkGateway2 `
   $vnet01gateway -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
   ```

## <a name="section-5---verify-your-connections"></a><a name="verify"></a>Sectie 5: de verbindingen controleren

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>De verbinding van uw klassieke VNet naar uw Resource Manager-VNet controleren

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-connection-ps-classic](../../includes/vpn-gateway-verify-connection-ps-classic-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]


### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>De verbinding van uw Resource Manager VNet naar uw klassieke VNet controleren

#### <a name="powershell"></a>PowerShell

[!INCLUDE [vpn-gateway-verify-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

#### <a name="azure-portal"></a>Azure Portal

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Veelgestelde vragen over VNet-naar-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
