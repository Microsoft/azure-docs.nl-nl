---
title: 'Klassieke virtuele netwerken verbinden met Azure Resource Manager VNets: Portal | Microsoft Docs'
description: Stappen om de klassieke VNets te verbinden met Resource Manager VNets met behulp van VPN Gateway en de portal
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/10/2021
ms.author: cherylmc
ms.openlocfilehash: 9d31bcaad01b9b762e57bd619d45c1f53ffb201e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100376799"
---
# <a name="connect-virtual-networks-from-different-deployment-models-using-the-portal"></a>Virtuele netwerken van verschillende implementatie modellen verbinden met behulp van de portal

In dit artikel wordt beschreven hoe u de klassieke VNets verbindt met Resource Manager VNets, zodat de resources in de afzonderlijke implementatie modellen kunnen communiceren met elkaar. De stappen in dit artikel gebruiken voornamelijk de Azure Portal, maar u kunt deze configuratie ook maken met behulp van de Power shell door het artikel in deze lijst te selecteren.

> [!div class="op_single_selector"]
> * [Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
> 
> 

Het verbinden van een klassiek VNet met een resource manager VNet is vergelijkbaar met het verbinden van een VNet met een on-premises site locatie. Voor beide connectiviteitstypen wordt een VPN-gateway gebruikt om een beveiligde tunnel met IPsec/IKE te bieden. U kunt een verbinding maken tussen VNets die zich in verschillende abonnementen en in verschillende regio's bevinden. U kunt ook verbinding maken met VNets die al verbindingen met on-premises netwerken hebben, zolang de gateway die ze hebben geconfigureerd, dynamisch is of op route gebaseerd is. Zie voor meer informatie over verbindingen tussen VNets de [Veelgestelde vragen over VNet-naar-VNet](#faq) aan het einde van dit artikel. 

Als u nog geen virtuele netwerk gateway hebt en u deze niet wilt maken, kunt u in plaats daarvan overwegen uw VNets te koppelen met behulp van VNet-peering. Bij VNet-peering wordt geen VPN-gateway gebruikt. Zie het artikel [VNet-peering](../virtual-network/virtual-network-peering-overview.md) voor meer informatie.

### <a name="before-you-begin"></a><a name="before"></a>Voordat u begint



* Bij deze stappen wordt ervan uitgegaan dat beide VNets al zijn gemaakt. Als u dit artikel als een oefening gebruikt en niet over VNets beschikt, zijn er koppelingen in de stappen die u kunt gebruiken om ze te maken.
* Controleer of de adresbereiken voor de VNets elkaar niet overlappen of elkaar overlappen met een van de bereiken voor andere verbindingen waarmee de gateways kunnen worden verbonden.
* Installeer de meest recente Power shell-cmdlets voor Resource Manager en Service Management (klassiek). In dit artikel gebruiken we de Azure Portal en Power shell. Power shell is vereist voor het maken van de verbinding van het klassieke VNet met het Resource Manager-VNet. Zie [Azure PowerShell installeren en configureren](/powershell/azure/) voor meer informatie. 

### <a name="example-settings"></a><a name="values"></a>Voorbeeldinstellingen

U kunt deze waarden gebruiken om een testomgeving te maken of ze raadplegen om meer inzicht te krijgen in de voorbeelden in dit artikel.

**Klassiek VNet**

VNet-naam = ClassicVNet <br>
Adres ruimte = 10.0.0.0/24 <br>
Subnetnaam = subnet-1 <br>
Adres bereik van subnet = 10.0.0.0/27 <br>
Abonnement = het abonnement dat u wilt gebruiken <br>
Resource groep = ClassicRG <br>
Locatie = vs-West <br>
GatewaySubnet = 10.0.0.32/28 <br>
Lokale site = RMVNetLocal <br>

**Resource Manager VNet**

VNet-naam = RMVNet <br>
Adres ruimte = 192.168.0.0/16 <br>
Resource groep = RG1 <br>
Locatie = VS-Oost <br>
Subnetnaam = subnet-1 <br>
Adres bereik = 192.168.1.0/24 <br>
GatewaySubnet = 192.168.0.0/26 <br>
Naam van de virtuele netwerk gateway = RMGateway <br>
Gateway type = VPN <br>
VPN-type = op route gebaseerd <br>
SKU = VpnGw1 <br>
Locatie = VS-Oost <br>
Virtueel netwerk = RMVNet <br> (koppel de VPN-gateway aan dit VNet) Eerste IP-configuratie = rmgwpip <br> (openbaar IP-adres van Gateway) Lokale netwerk gateway = ClassicVNetLocal <br>
Verbindings naam = RMtoClassic

### <a name="connection-overview"></a><a name="connectoverview"></a>Overzicht van verbindingen

Voor deze configuratie maakt u een VPN-gateway verbinding via een IPsec/IKE VPN-tunnel tussen de virtuele netwerken. Zorg ervoor dat geen van uw VNet-bereiken met elkaar overlappen, of met een van de lokale netwerken waarmee ze verbinding maken.

In de volgende tabel ziet u een voor beeld van hoe het voor beeld-VNets en de lokale sites worden gedefinieerd:

| Virtual Network | Adresruimte | Region | Maakt verbinding met de lokale netwerk site |
|:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |VS - west | RMVNetLocal (192.168.0.0/16) |
| RMVNet | (192.168.0.0/16) |VS - oost |ClassicVNetLocal (10.0.0.0/24) |

## <a name="section-1---configure-the-classic-vnet-settings"></a><a name="classicvnet"></a>Sectie 1: de klassieke VNet-instellingen configureren

In deze sectie maakt u het klassieke VNet, het lokale netwerk (lokale site) en de gateway van het virtuele netwerk. De schermafbeeldingen dienen alleen als voorbeeld. Zorg ervoor dat u de waarden vervangt door uw eigen waarde of gebruik de [voorbeeld](#values) waarden.

### <a name="1-create-a-classic-vnet"></a>1. <a name="classicvnet"></a> een klassiek VNet maken

Als u geen klassiek VNet hebt en u deze stappen uitvoert als oefening, kunt u een VNet maken met behulp van [dit artikel](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal) en de [voor beeld](#values) -instellingen van bovenstaande waarden.

Als u al een VNet met een VPN-gateway hebt, controleert u of de gateway dynamisch is. Als het statisch is, moet u eerst de VPN-gateway verwijderen voordat u doorgaat met [het configureren van de lokale site](#local).

1. Open de [Azure-portal](https://ms.portal.azure.com) en meld u aan met uw Azure-account.
2. Klik op **+ een resource maken** om de pagina nieuw te openen.
3. Typ ' Virtual Network ' in het veld Marketplace doorzoeken. Als u in plaats daarvan netwerk-> Virtual Network selecteert, krijgt u niet de mogelijkheid om een klassiek VNet te maken.
4. Zoek naar ' Virtual Network ' in de lijst met resultaten en klik erop om de pagina Virtual Network te openen. 
5. Selecteer op de pagina virtueel netwerk de optie klassiek om een klassiek VNet te maken. Als u hier de standaard waarde haalt, gaat u in plaats daarvan een resource manager-VNet.

### <a name="2-configure-the-local-site"></a>2. <a name="local"></a> de lokale site configureren

1. Ga naar **alle resources** en zoek de **ClassicVNet** in de lijst.
2. Klik in de sectie **instellingen** van het menu op **Gateway** en klik vervolgens op de banner om een gateway te maken.
  ![Een VPN-gateway configureren](./media/vpn-gateway-connect-different-deployment-models-portal/gatewaygraphic.png "Een VPN-gateway configureren")
3. Selecteer op de pagina **nieuwe VPN-verbinding** bij **verbindings type** de optie **site-naar-site**.
4. Klik voor **lokale site** op **vereiste instellingen configureren**. Hiermee opent u de pagina **lokale site** .
5. Maak op de pagina **lokale site** een naam om te verwijzen naar het Resource Manager VNet. Bijvoorbeeld ' RMVNetLocal '.
6. Als de VPN-gateway voor de Resource Manager VNet al een openbaar IP-adres heeft, gebruikt u de waarde voor het veld IP-adres van de **VPN-gateway** . Als u deze stappen uitvoert als oefening of als u nog geen virtuele netwerk gateway voor uw Resource Manager VNet hebt, kunt u een IP-adres voor tijdelijke aanduidingen maken. Zorg ervoor dat het IP-adres van de tijdelijke aanduiding een geldige indeling gebruikt. Later vervangt u het IP-adres van de tijdelijke aanduiding door het open bare IP-adres van de virtuele netwerk gateway van Resource Manager.
7. Gebruik voor de **adres ruimte** van de client de [waarden](#connectoverview) voor de IP-adres ruimten van het virtuele netwerk voor het Resource Manager-VNet. Deze instelling wordt gebruikt om de adres ruimten op te geven die moeten worden doorgestuurd naar het virtuele netwerk van Resource Manager. In het voor beeld gebruiken we 192.168.0.0/16, het adres bereik voor de RMVNet.
8. Klik op **OK** om de waarden op te slaan en terug te keren naar de pagina **nieuwe VPN-verbinding** .

### <a name="3-create-the-virtual-network-gateway"></a><a name="classicgw"></a>3. de gateway van het virtuele netwerk maken

1. Schakel op de pagina **nieuwe VPN-verbinding** het selectie vakje **Gateway onmiddellijk maken** in.
2. Klik op **Optionele gatewayconfiguratie** om de pagina **Gatewayconfiguratie** te openen.

   ![Pagina gateway configuratie openen](./media/vpn-gateway-connect-different-deployment-models-portal/optionalgatewayconfiguration.png "Pagina gateway configuratie openen")
3. Klik op **subnet-vereiste instellingen configureren** om de pagina **subnet toevoegen** te openen. De **naam** is al geconfigureerd met de vereiste waarde: **GatewaySubnet**.
4. Het **adres bereik** verwijst naar het bereik voor het gateway-subnet. Hoewel u een gateway-subnet kunt maken met een/29 adres bereik (3 adressen), raden we u aan een gateway-subnet te maken dat meer IP-adressen bevat. Dit geldt voor toekomstige configuraties waarvoor mogelijk meer beschik bare IP-adressen nodig zijn. Gebruik, indien mogelijk,/27 of/28. Als u deze stappen uitvoert als oefening, kunt u de [voorbeeld waarden](#values)raadplegen. Voor dit voor beeld gebruiken we ' 10.0.0.32/28 '. Klik op **OK** om het gateway-subnet te maken.
5. Op de pagina **Gateway configuratie** verwijst **grootte** naar de gateway-SKU. Selecteer de gateway-SKU voor uw VPN-gateway.
6. Controleer of het **routerings type** **dynamisch** is en klik vervolgens op **OK** om terug te keren naar de pagina **nieuwe VPN-verbinding** .
7. Klik op de pagina **nieuwe VPN-verbinding** op **OK** om te beginnen met het maken van uw VPN-gateway. Het maken van een VPN-gateway kan tot 45 minuten duren.

### <a name="4-copy-the-virtual-network-gateway-public-ip-address"></a><a name="ip"></a>4. het open bare IP-adres van de gateway van het virtuele netwerk kopiëren

Nadat de virtuele netwerk gateway is gemaakt, kunt u het IP-adres van de gateway weer geven. 

1. Navigeer naar uw klassieke VNet en klik op **overzicht**.
2. Klik op **VPN-verbindingen** om de pagina VPN-verbindingen te openen. Op de pagina VPN-verbindingen kunt u het open bare IP-adres weer geven. Dit is het open bare IP-adres dat is toegewezen aan uw virtuele netwerk gateway. Noteer het IP-adres. U gebruikt deze in latere stappen wanneer u werkt met de configuratie-instellingen van de lokale netwerk gateway van Resource Manager. 
3. U kunt de status van uw gateway verbindingen weer geven. U ziet dat de lokale netwerk site die u hebt gemaakt, wordt weer gegeven als ' verbinding maken '. De status wordt gewijzigd nadat u uw verbindingen hebt gemaakt. U kunt deze pagina sluiten wanneer u klaar bent met het weer geven van de status.

## <a name="section-2---configure-the-resource-manager-vnet-settings"></a><a name="rmvnet"></a>Sectie 2: de VNet-instellingen van Resource Manager configureren

In deze sectie maakt u de gateway van het virtuele netwerk en de lokale netwerk gateway voor uw Resource Manager-VNet. De schermafbeeldingen dienen alleen als voorbeeld. Zorg ervoor dat u de waarden vervangt door uw eigen waarde of gebruik de [voorbeeld](#values) waarden.

### <a name="1-create-a-virtual-network"></a>1. een virtueel netwerk maken

**Voorbeeld waarden:**

* VNet-naam = RMVNet <br>
* Adres ruimte = 192.168.0.0/16 <br>
* Resource groep = RG1 <br>
* Locatie = VS-Oost <br>
* Subnetnaam = subnet-1 <br>
* Adres bereik = 192.168.1.0/24 <br>

Als u geen Resource Manager VNet hebt en u deze stappen uitvoert als oefening, maakt u een virtueel netwerk met de stappen in [een virtueel netwerk maken](../virtual-network/quick-create-portal.md)met behulp van de voorbeeld waarden.

### <a name="2-create-a-virtual-network-gateway"></a><a name="creategw"></a>2. een virtuele netwerk gateway maken

In deze stap maakt u de virtuele netwerkgateway VNet. Het maken van een gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde gateway-SKU.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

**Voorbeeld waarden:**

* Naam van de virtuele netwerk gateway = RMGateway <br>
* Gateway type = VPN <br>
* VPN-type = op route gebaseerd <br>
* SKU = VpnGw1 <br>
* Locatie = VS-Oost <br>
* Virtueel netwerk = RMVNet <br>
* GatewaySubnet = 192.168.0.0/26 <br>
* Eerste IP-configuratie = rmgwpip <br>

[!INCLUDE [Add gateway](../../includes/vpn-gateway-add-gw-rm-portal-empty.md)]

[!INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]

### <a name="3-create-a-local-network-gateway"></a><a name="createlng"></a>3. een lokale netwerk gateway maken

**Voorbeeld waarden:** Lokale netwerk gateway = ClassicVNetLocal

| Virtual Network | Adresruimte | Region | Maakt verbinding met de lokale netwerk site |Openbaar IP-adres van Gateway|
|:--- |:--- |:--- |:--- |:--- |
| ClassicVNet |(10.0.0.0/24) |VS - west | RMVNetLocal (192.168.0.0/16) |Het open bare IP-adres dat is toegewezen aan de ClassicVNet-gateway|
| RMVNet | (192.168.0.0/16) |VS - oost |ClassicVNetLocal (10.0.0.0/24) |Het open bare IP-adres dat is toegewezen aan de RMVNet-gateway.|

De lokale netwerk gateway geeft het adres bereik en het open bare IP-adres aan dat is gekoppeld aan uw klassieke VNet en de gateway van het virtuele netwerk. Als u deze stappen uitvoert als oefening, raadpleegt u de voorbeeld waarden.

[!INCLUDE [Add local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-ip-empty.md)]

## <a name="section-3---modify-the-classic-vnet-local-site-settings"></a><a name="modifylng"></a>Sectie 3: de instellingen van de klassieke VNet-lokale site wijzigen

In deze sectie vervangt u het IP-adres van de tijdelijke aanduiding dat u hebt gebruikt bij het opgeven van de lokale site-instellingen, met het IP-adres van de Resource Manager VPN-gateway. In deze sectie worden de klassieke Power shell-cmdlets (SM) gebruikt.

1. Ga in het Azure Portal naar het klassieke virtuele netwerk.
2. Klik op de pagina voor uw virtuele netwerk op **overzicht**.
3. Klik in de sectie **VPN-verbindingen** op de naam van uw lokale site in de afbeelding.

   ![VPN-verbindingen](./media/vpn-gateway-connect-different-deployment-models-portal/vpnconnections.png "VPN-verbindingen")
4. Klik op de pagina **site-naar-site-VPN-verbindingen** op de naam van de site.

   ![Site naam](./media/vpn-gateway-connect-different-deployment-models-portal/sitetosite3.png "Naam van lokale site")
5. Klik op de pagina verbinding voor uw lokale site op de naam van de lokale site om de pagina **lokale site** te openen.

   ![Open-local-site](./media/vpn-gateway-connect-different-deployment-models-portal/openlocal.png "Open lokale site")
6. Vervang het IP-adres van de **VPN-gateway** op de pagina **lokale site** door het IP-adres van de Resource Manager-gateway.

   ![Gateway-IP-adres](./media/vpn-gateway-connect-different-deployment-models-portal/gwipaddress.png "IP-adres van de gateway")
7. Klik op **OK** om het IP-adres bij te werken.

## <a name="section-4---create-resource-manager-to-classic-connection"></a><a name="RMtoclassic"></a>Sectie 4: Resource Manager maken voor klassieke verbinding

In deze stappen configureert u de verbinding van de Resource Manager VNet met het klassieke VNet met behulp van de Azure Portal.

1. Zoek de lokale netwerk gateway in **alle resources**. In ons voor beeld is de lokale netwerk gateway **ClassicVNetLocal**.
2. Klik op **configuratie** en controleer of de IP-adres waarde de VPN-gateway voor het klassieke VNet is. Update, indien nodig, en klik vervolgens op **Opslaan**. Sluit de pagina.
3. Klik in **alle resources** op de lokale netwerk gateway.
4. Klik op **verbindingen** om de pagina verbindingen te openen.
5. Klik op de pagina **verbindingen** op **+** om een verbinding toe te voegen.
6. Geef op de pagina **verbinding toevoegen** een naam op voor de verbinding. Bijvoorbeeld ' RMtoClassic '.
7. **Site-naar-site** is al geselecteerd op deze pagina.
8. Selecteer de virtuele netwerk gateway die u wilt koppelen aan deze site.
9. Maak een **gedeelde sleutel**. Deze sleutel wordt ook gebruikt in de verbinding die u maakt op basis van het klassieke VNet en het Resource Manager-VNet. U kunt de sleutel genereren of er een maken. In ons voor beeld gebruiken we ' abc123 ', maar u kunt (en) een complexere waarde gebruiken.
10. Klik op **OK** om de verbinding te maken.

## <a name="section-5---create-classic-to-resource-manager-connection"></a><a name="classictoRM"></a>Sectie 5: een klassieke maken voor de Resource Manager-verbinding

In deze stappen configureert u de verbinding van het klassieke VNet met het Resource Manager-VNet. Voor deze stappen is Power shell vereist. U kunt deze verbinding niet maken in de portal. Zorg ervoor dat u de klassieke Power shell-cmdlets (SM) en Resource Manager (RM) hebt gedownload en geïnstalleerd.

### <a name="1-connect-to-your-azure-account"></a>1. Maak verbinding met uw Azure-account

Open de Power shell-console met verhoogde rechten en meld u aan bij uw Azure-account. Nadat u zich hebt aangemeld, worden uw account instellingen gedownload zodat ze beschikbaar zijn voor Azure PowerShell. De volgende cmdlet vraagt u om de aanmeldings referenties voor uw Azure-account voor het Resource Manager-implementatie model:

```powershell
Connect-AzAccount
```

Haal een lijst met uw Azure-abonnementen op.

```powershell
Get-AzSubscription
```

Als u meer dan één abonnement hebt, geeft u het abonnement op dat u wilt gebruiken.

```powershell
Select-AzSubscription -SubscriptionName "Name of subscription"
```

Vervolgens meldt u zich aan voor het gebruik van de klassieke Power shell-cmdlets (Service Management). Gebruik de volgende opdracht om uw Azure-account toe te voegen voor het klassieke implementatie model:

```powershell
Add-AzureAccount
```

Een lijst met uw abonnementen ophalen. Deze stap kan nodig zijn bij het toevoegen van de service management-cmdlets, afhankelijk van de installatie van uw Azure-module.

```powershell
Get-AzureSubscription
```

Als u meer dan één abonnement hebt, geeft u het abonnement op dat u wilt gebruiken.

```powershell
Select-AzureSubscription -SubscriptionName "Name of subscription"
```

### <a name="2-view-the-network-configuration-file-values"></a>2. de waarden van het netwerk configuratie bestand weer geven

Wanneer u een VNet maakt in de Azure Portal, is de volledige naam die door Azure wordt gebruikt, niet zichtbaar in de Azure Portal. Een VNet dat in het Azure Portal met de naam ' ClassicVNet ' wordt weer gegeven, kan in het netwerk configuratie bestand bijvoorbeeld een veel langere naam hebben. De naam kan er ongeveer als volgt uitzien: ' Group ClassicRG ClassicVNet '. In deze stappen downloadt u het netwerk configuratie bestand en bekijkt u de waarden.

Maak een map op de computer en exporteer vervolgens het netwerkconfiguratiebestand naar de map. In dit voorbeeld wordt het netwerkconfiguratiebestand geëxporteerd naar C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Open het bestand met een tekst editor en Bekijk de naam voor uw klassieke VNet. Gebruik de namen in het netwerk configuratie bestand bij het uitvoeren van uw Power shell-cmdlets.

- VNet-namen worden vermeld als **VirtualNetworkSite name =**
- Site namen worden vermeld als **LocalNetworkSite name =**

### <a name="3-create-the-connection"></a>3. de verbinding maken

Stel de gedeelde sleutel in en maak de verbinding van het klassieke VNet met het Resource Manager-VNet. U kunt de gedeelde sleutel niet instellen met behulp van de portal. Zorg ervoor dat u deze stappen uitvoert terwijl u bent aangemeld met de klassieke versie van de Power shell-cmdlets. Gebruik hiervoor **add-AzureAccount**. Als dat niet het geval is, kunt u de-AzureVNetGatewayKey niet instellen.

- In dit voor beeld is **-VNetName** de naam van het klassieke VNet dat is gevonden in het netwerk configuratie bestand. 
- De **-LocalNetworkSiteName** is de naam die u hebt opgegeven voor de lokale site, zoals gevonden in het configuratie bestand van uw netwerk.
- De **-SharedKey** is een waarde die u genereert en opgeeft. Voor dit voor beeld hebben we *abc123* gebruikt, maar u kunt een complexere waarde genereren. Het belang rijk is dat de waarde die u hier opgeeft, dezelfde waarde is die u hebt opgegeven bij het maken van uw Resource Manager naar een klassieke verbinding.

```powershell
Set-AzureVNetGatewayKey -VNetName "Group ClassicRG ClassicVNet" `
-LocalNetworkSiteName "172B9E16_RMVNetLocal" -SharedKey abc123
```

## <a name="section-6---verify-your-connections"></a><a name="verify"></a>Sectie 6: uw verbindingen controleren

U kunt uw verbindingen controleren met behulp van de Azure Portal of Power shell. Bij het verifiëren moet u mogelijk een minuut of twee wachten als de verbinding wordt gemaakt. Wanneer een verbinding tot stand is gebracht, verandert de verbindings status van ' verbinding maken met ' verbonden '.

### <a name="to-verify-the-connection-from-your-classic-vnet-to-your-resource-manager-vnet"></a>De verbinding van uw klassieke VNet naar uw Resource Manager-VNet controleren

[!INCLUDE [vpn-gateway-verify-connection-azureportal-classic](../../includes/vpn-gateway-verify-connection-azureportal-classic-include.md)]

### <a name="to-verify-the-connection-from-your-resource-manager-vnet-to-your-classic-vnet"></a>De verbinding van uw Resource Manager VNet naar uw klassieke VNet controleren

[!INCLUDE [vpn-gateway-verify-connection-portal-rm](../../includes/vpn-gateway-verify-connection-portal-rm-include.md)]

## <a name="vnet-to-vnet-faq"></a><a name="faq"></a>Veelgestelde vragen over VNet-naar-VNet

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]
