---
title: 'Zelfstudie: Uw virtuele hubnetwerk beveiligen met Azure Firewall Manager'
description: In deze zelfstudie leert u hoe u uw virtuele netwerk beveiligt met Azure Firewall Manager met behulp van Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 03/03/2021
ms.author: victorh
ms.openlocfilehash: f631100003a4ea6e191a5bdc13a11eb9aa327268
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212536"
---
# <a name="tutorial-secure-your-hub-virtual-network-using-azure-firewall-manager"></a>Zelfstudie: Uw virtuele hubnetwerk beveiligen met Azure Firewall Manager

Als u het on-premises netwerk verbindt met een virtueel Azure-netwerk om een hybride netwerk te maken, is de mogelijkheid om de toegang tot uw Azure-netwerkresources te beheren een belangrijk onderdeel van een algemeen beveiligingsplan.

Met behulp van Azure Firewall Manager kunt u een virtueel hubnetwerk maken om het hybride netwerkverkeer te beveiligen dat bestemd is voor privé-IP-adressen, Azure PaaS en internet. U kunt Azure Firewall Manager gebruiken om de netwerktoegang in een hybride netwerk te beheren met behulp van beleid dat toegestaan en geweigerd netwerkverkeer definieert.

Firewall Manager biedt ook ondersteuning voor een beveiligde virtuele architectuur met hubs. Zie [Wat zijn de opties voor de Azure Firewall Manager-architectuur?](vhubs-and-vnets.md) voor een vergelijking van de architectuurtypen voor beveiligde virtuele hubs en virtuele hubnetwerken.

Voor deze zelfstudie maakt u drie virtuele netwerken:

- **VNet-Hub**: de firewall bevindt zich in dit virtuele netwerk.
- **VNet-spoke**: het virtuele spoke-netwerk vertegenwoordigt de workload die zich bevindt in Azure.
- **VNet-Onprem**: het on-premises virtuele netwerk vertegenwoordigt een on-premises netwerk. In een daadwerkelijke implementatie kan het zijn verbonden via een VPN of een ExpressRoute-verbinding. Om het eenvoudig te houden wordt in deze zelfstudie een VPN-gatewayverbinding gebruikt en wordt een on-premises netwerk vertegenwoordigd door een virtueel Azure-netwerk.

![Hybride netwerk](media/tutorial-hybrid-portal/hybrid-network-firewall.png)

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een firewallbeleid maken
> * De virtuele netwerken maken
> * De firewall configureren en implementeren
> * De VPN-gateways maken en verbinden
> * De hub- en virtuele spoke-netwerken koppelen
> * De routes maken
> * De virtuele machines maken
> * De firewall testen


## <a name="prerequisites"></a>Vereisten

Een hybride netwerk gebruikt het model van hub-en-spoke om verkeer tussen Azure VNets en on-premises netwerken te routeren. De hub-en-spoke-architectuur heeft de volgende vereisten:

- Stel **AllowGatewayTransit** in bij peering tussen VNet-hub en VNet-spoke. In een hub en spoke-netwerkarchitectuur zorgt een gatewayovergang dat virtuele spoke-netwerken de VPN-gateway in de hub kunnen delen, in plaats van in elk virtueel spoke-netwerk VPN-gateways te implementeren. 

   Routes naar de via de gateway verbonden virtuele netwerken of on-premises netwerken worden bovendien automatisch doorgegeven aan de routeringstabellen voor de als peer ingestelde virtuele netwerken met behulp van de gatewayovergang. Zie [VPN-gatewayoverdracht kunt configureren voor peering voor virtuele netwerken](../vpn-gateway/vpn-gateway-peering-gateway-transit.md) voor meer informatie.

- Stel **UseRemoteGateways** bij peering tussen VNet-spoke en VNet-hub. Als **UseRemoteGateways** is ingesteld en **AllowGatewayTransit** ook is ingesteld voor externe peering, gebruikt het virtuele spoke-netwerk gateways van het externe virtuele netwerk voor overdracht.
- Om het verkeer van het spoke-subnet te routeren via de hub-firewall, hebt u een door de gebruiker gedefinieerde route (UDR) nodig die naar de firewall wijst en waarvoor de instelling **Doorgifte van route van virtuele netwerkgateway** is uitgeschakeld. Deze optie voorkomt routedistributie naar de spoke-subnetten. Hierdoor veroorzaken geleerde routes geen conflicten met uw UDR.
- Configureer een UDR in het subnet van de hubgateway die verwijst naar het IP-adres van de firewall als de volgende hop naar de spoke-netwerken. Er is geen door de gebruiker gedefinieerde route (UDR) nodig in het Azure Firewall-subnet, omdat het de routes overneemt van BGP.

Zie het gedeelte [Routes maken](#create-the-routes) in deze zelfstudie voor informatie over hoe deze routes worden gemaakt.

>[!NOTE]
>Azure Firewall moet een directe verbinding met internet hebben. Als uw AzureFirewallSubnet een standaardroute naar uw on-premises netwerk via BGP leert, moet u deze overschrijven met een UDR van 0.0.0.0/0 met de waarde **NextHopType** ingesteld op **Internet** om directe verbinding met internet te houden.
>
>Azure Firewall kan worden geconfigureerd voor ondersteuning van geforceerde tunneling. Zie [Geforceerde tunneling van Azure Firewall](../firewall/forced-tunneling.md) voor meer informatie.

>[!NOTE]
>Verkeer tussen rechtstreeks gepeerde VNets wordt rechtstreeks gerouteerd, zelfs als de UDR naar Azure Firewall als standaardgateway wijst. Als u in dit scenario subnet-naar-subnet-verkeer wilt verzenden, moet een UDR het voorvoegsel van het doelsubnetwerk expliciet op beide subnetten bevatten.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-a-firewall-policy"></a>Een firewallbeleid maken

1. Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).
2. Typ **Firewall Manager** in de zoekbalk van Azure Portal en druk op **Enter**.
3. Selecteer **Azure-firewallbeleidsregels weergeven** op de pagina van Azure Firewall Manager.

   ![Firewallbeleid](media/tutorial-hybrid-portal/firewall-manager-policy.png)

1. Selecteer **Azure-firewallbeleid maken**.
1. Selecteer uw abonnement, selecteer voor Resourcegroep de optie **Nieuwe maken** en maak een resourcegroep met de naam **FW-Hybrid-Test**.
2. Voor de beleidsnaam typt u **Pol-Net01**.
3. Selecteer bij Regio **VS - oost**.
1. Selecteer **volgende: DNS-instellingen**.
1. Selecteer **volgende: TLS-inspectie (preview-versie)**
1. Selecteer **Volgende: Regels**.
1. Selecteer **Een regelverzameling toevoegen**.
1. Bij **Naam** typt u **RCNet01**.
1. Bij **Type regelverzameling** selecteert u **Netwerk**.
1. Bij **Prioriteit** typt u **100**.
1. Bij **Actie** selecteert u **Toestaan**.
1. Onder **Regels** typt u bij **Naam** de naam **AllowWeb**.
1. Typ bij **Bron** **192.168.1.0/24**.
1. Bij **Protocol** selecteert u **TCP**.
1. Typ bij **Doelpoorten** **80**.
1. Bij **Doeltype** selecteert u **IP-adres**.
1. Bij **Doel** typt u **10.6.0.0/16**.
1. Voer op de volgende rij van de regel de volgende informatie in:
 
    Naam: typ **AllowRDP**<br>
    Bron: Typ **192.168.1.0/24**.<br>
    Protocol: selecteer **TCP**<br>
    Doelpoorten: typ **3389**<br>
    Doeltype: selecteer **IP-adres**<br>
    Bij Doel typt u **10.6.0.0/16**

1. Selecteer **Toevoegen**.
2. Selecteer **Controleren + maken**.
3. Controleer de gegevens en selecteer vervolgens **Maken**.

## <a name="create-the-firewall-hub-virtual-network"></a>Het virtuele hub-netwerk voor de firewall maken

> [!NOTE]
> De grootte van het subnet AzureFirewallSubnet is /26. Zie [Veelgestelde vragen over Azure Firewall](../firewall/firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size) voor meer informatie over de grootte van het subnet.

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Zoek naar **virtueel netwerk** en selecteer **virtueel netwerk**.
1. Selecteer **Maken**.
1. Bij **Abonnement** selecteert u uw abonnement.
1. Geef voor **Resourcegroep** de naam **FW-Hybrid-Test** op.
1. Geef bij **Naam** **VNet-hub** op.
1. Selecteer bij **Regio** **VS - oost**.
1. Selecteer **volgende: IP-adressen**.

1. Typ **10.5.0.0/16** voor **IPv4-adres ruimte**.
1. Onder **Subnetnaam** selecteert u **standaard**.
1.  Wijzig de **naam** van het subnet in **AzureFirewallSubnet**. De firewall bevindt zich in dit subnet en de naam van het subnet **moet** AzureFirewallSubnet zijn.
1. Typ **10.5.0.0/26** bij **Subnetadresbereik**. 
1. Accepteer de andere standaard instellingen en selecteer vervolgens **Opslaan**.
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.

## <a name="create-the-spoke-virtual-network"></a>Het virtuele spoke-netwerk maken

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Zoek naar **virtueel netwerk** en selecteer **virtueel netwerk**.
1. Selecteer **Maken**.
1. Bij **Abonnement** selecteert u uw abonnement.
1. Geef voor **Resourcegroep** de naam **FW-Hybrid-Test** op.
1. Bij **Naam** typt u **VNet-Spoke**.
1. Selecteer bij **Regio** **VS - oost**.
1. Selecteer **volgende: IP-adressen**.

1. Bij **IPv4-adresruimte** typt u **10.6.0.0/16**.
1. Onder **Subnetnaam** selecteert u **standaard**.
1. Wijzig de **naam** van het subnet in **sn-workload**.
1. Typ **10.6.0.0/24** bij **Subnetadresbereik**. 
1. Accepteer de andere standaard instellingen en selecteer vervolgens **Opslaan**.
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.


## <a name="create-the-on-premises-virtual-network"></a>Het on-premises virtuele netwerk maken

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Zoek naar **virtueel netwerk** en selecteer **virtueel netwerk**.
1. Selecteer **Maken**.
1. Bij **Abonnement** selecteert u uw abonnement.
1. Geef voor **Resourcegroep** de naam **FW-Hybrid-Test** op.
1. Bij **Naam** typt u **VNet-OnPrem**.
1. Selecteer bij **Regio** **VS - oost**.
1. Selecteer **volgende: IP-adressen**.

1. Bij **IPv4-adresruimte** typt u **192.168.0.0/16**.
1. Onder **Subnetnaam** selecteert u **standaard**.
1. Wijzig de **naam** van het subnet in **sn-Corp**.
1. Typ **192.168.1.0/24** bij **Subnetadresbereik**. 
1. Accepteer de andere standaard instellingen en selecteer vervolgens **Opslaan**.
2. Selecteer **subnet toevoegen**.
3. Typ **GatewaySubnet** in het **subnet naam**.
4. Bij **Subnetadresbereik** typt u **192.168.2.0/24**.
5. Selecteer **Toevoegen**.
1. Selecteer **Controleren en maken**.
1. Selecteer **Maken**.




## <a name="configure-and-deploy-the-firewall"></a>De firewall configureren en implementeren

Wanneer beveiligings beleid is gekoppeld aan een hub, wordt dit een *hub virtueel netwerk* genoemd.

Converteer het virtuele netwerk **VNet-Hub** naar een virtueel *hubnetwerk* en beveilig het met Azure Firewall.

1. Typ **Firewall Manager** in de zoekbalk van Azure Portal en druk op **Enter**.
3. Selecteer op de pagina van Azure Firewall Manager onder **Beveiliging toevoegen aan virtuele netwerken** de optie **Virtuele netwerken van de hub weergeven**.
1. Schakel onder **virtuele netwerken** het selectie vakje voor **VNet-hub** in.
1. Selecteer **beveiliging beheren** en selecteer vervolgens **een firewall implementeren met firewall-beleid**.
1. Schakel op de pagina **virtuele netwerken converteren** onder **firewall beleid** het selectie vakje voor **Pol-Net01** in.
1. Selecteer **volgende: controleren + bevestigen**
1. Controleer de gegevens en selecteer vervolgens **Bevestigen**.


   Het implementeren duurt een paar minuten.
7. Als de implementatie is voltooid, gaat u naar de resourcegroep **FW-Hybrid-Test** en selecteert u de firewall.
9. Noteer het **Persoonlijk IP-adres voor firewall** op de pagina **Overzicht**. U zult het later gebruiken wanneer u de standaardroute maakt.

## <a name="create-and-connect-the-vpn-gateways"></a>De VPN-gateways maken en verbinden

Het virtuele hub-netwerk en het on-premises virtuele netwerk zijn verbonden via VPN-gateways.

### <a name="create-a-vpn-gateway-for-the-hub-virtual-network"></a>Een VPN-gateway maken voor het virtuele hub-netwerk

Maak nu een VPN-gateway voor het virtuele hub-netwerk. Voor netwerk-naar-netwerk-configuraties is een RouteBased VpnType vereist. Het maken van een VPN-gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde VPN-gateway-SKU.

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Typ in het zoekvak **virtuele netwerkgateway** en druk op **Enter**.
3. Selecteer **Virtuele netwerkgateway** en vervolgens **Maken**.
4. Bij **Naam** typt u **GW-hub**.
5. Selecteer bij **Regio** **(VS) VS - oost**.
6. Bij **Gatewaytype** selecteert u **VPN**.
7. Bij **VPN-type** selecteert u **Op route gebaseerd**.
8. Bij **SKU** selecteert u **Basis**.
9. Bij **Virtueel netwerk** selecteert u **VNet-hub**.
10. Bij **Openbaar IP-adres** selecteert u **Nieuwe maken** en typt u **VNet-hub-GW-pip** als de naam.
11. Accepteer voor de overige instellingen de standaardwaarden en selecteer **Beoordelen en maken**.
12. Controleer de configuratie en selecteer vervolgens **Maken**.

### <a name="create-a-vpn-gateway-for-the-on-premises-virtual-network"></a>Een VPN-gateway maken voor het on-premises virtuele netwerk

Maak nu de VPN-gateway voor het on-premises virtuele netwerk. Voor netwerk-naar-netwerk-configuraties is een RouteBased VpnType vereist. Het maken van een VPN-gateway duurt vaak 45 minuten of langer, afhankelijk van de geselecteerde VPN-gateway-SKU.

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Typ in het zoekvak **virtuele netwerkgateway** en druk op **Enter**.
3. Selecteer **Virtuele netwerkgateway** en vervolgens **Maken**.
4. Bij **Naam** typt u **GW-Onprem**.
5. Selecteer bij **Regio** **(VS) VS - oost**.
6. Bij **Gatewaytype** selecteert u **VPN**.
7. Bij **VPN-type** selecteert u **Op route gebaseerd**.
8. Bij **SKU** selecteert u **Basis**.
9. Bij **Virtueel netwerk** selecteert u **VNet-Onprem**.
10. Bij **Openbaar IP-adres** selecteert u **Nieuwe maken** en typt u **VNet-Onprem-GW-pip** als de naam.
11. Accepteer voor de overige instellingen de standaardwaarden en selecteer **Beoordelen en maken**.
12. Controleer de configuratie en selecteer vervolgens **Maken**.

### <a name="create-the-vpn-connections"></a>De VPN-verbindingen maken

U kunt nu de VPN-verbindingen maken tussen de hub-gateway en de on-premises gateway.

In deze stap maakt u de verbinding van het virtuele hub-netwerk naar het on-premises virtuele netwerk. Hier ziet u een gedeelde sleutel waarnaar wordt verwezen in de voorbeelden. U kunt uw eigen waarden voor de gedeelde sleutel gebruiken. Het belangrijkste is dat de gedeelde sleutel voor beide verbindingen moet overeenkomen. Het duurt enige tijd om de verbinding te maken.

1. Open de resourcegroep **FW-Hybrid-Test** en selecteer de gateway **GW-hub**.
2. Selecteer **Verbindingen** in de linkerkolom.
3. Selecteer **Toevoegen**.
4. Voor de verbindings naam typt **u hub-naar-premises**.
5. Bij **Verbindingstype** selecteert u **VNet-naar-VNet**.
6. Bij **Tweede virtuele netwerkgateway** selecteert u **GW-Onprem**.
7. Bij **Gedeeld sleutel (PSK)** typt u **AzureA1b2C3**.
8. Selecteer **OK**.

Maak de verbinding van het on-premises virtuele netwerk naar het virtuele hub-netwerk. Deze stap is vergelijkbaar met de vorige, behalve dat u de verbinding maakt vanuit VNet-Onprem naar VNet-hub. Zorg dat de gedeelde sleutels overeenkomen. De verbinding wordt na enkele minuten tot stand gebracht.

1. Open de resourcegroep **FW-Hybrid-Test** en selecteer de gateway **GW-Onprem**.
2. Selecteer **Verbindingen** in de linkerkolom.
3. Selecteer **Toevoegen**.
4. Typ **Onprem-to-Hub** als de naam van de verbinding.
5. Bij **Verbindingstype** selecteert u **VNet-naar-VNet**.
6. Bij **Tweede virtuele netwerkgateway** selecteert u **GW-hub**.
7. Bij **Gedeeld sleutel (PSK)** typt u **AzureA1b2C3**.
8. Selecteer **OK**.


#### <a name="verify-the-connection"></a>De verbinding controleren

Na ongeveer vijf minuten moeten beide verbindingen de status **Verbonden** hebben.

![Gatewayverbindingen](media/secure-hybrid-network/gateway-connections.png)

## <a name="peer-the-hub-and-spoke-virtual-networks"></a>De hub- en virtuele spoke-netwerken koppelen

Koppel nu de virtuele hub- en spoke-netwerken.

1. Open de resourcegroep **FW-Hybrid-Test** en selecteer het virtuele netwerk **VNet-hub**.
2. Selecteer in de linkerkolom **Peerings**.
3. Selecteer **Toevoegen**.
4. Onder **Dit virtueel netwerk**:
 
   
   |Naam van de instelling  |Waarde  |
   |---------|---------|
   |Naam van peeringkoppeling| HubtoSpoke|
   |Verkeer naar een extern virtueel netwerk|   Toestaan (standaard)      |
   |Verkeer dat wordt doorgestuurd vanuit een extern virtueel netwerk    |   Toestaan (standaard)      |
   |Virtuele netwerk gateway of route server    |  De gateway van dit virtuele netwerk gebruiken       |
    
5. Onder **Extern virtueel netwerk**:

   |Naam van de instelling  |Waarde  |
   |---------|---------|
   |Naam van peeringkoppeling | SpoketoHub|
   |Implementatiemodel voor het virtuele netwerk| Resource Manager|
   |Abonnement|\<your subscription\>|
   |Virtueel netwerk| VNet-Spoke
   |Verkeer naar een extern virtueel netwerk     |   Toestaan (standaard)      |
   |Verkeer dat wordt doorgestuurd vanuit een extern virtueel netwerk    |   Toestaan (standaard)      |
   |Gateway van een virtueel netwerk     |  De gateway van het externe virtuele netwerk gebruiken       |

5. Selecteer **Toevoegen**.

   :::image type="content" source="media/secure-hybrid-network/firewall-peering.png" alt-text="VNet-peering":::

## <a name="create-the-routes"></a>De routes maken

Maak nu een paar routes:

- Een route van het subnet van de hub-gateway naar het spoke-subnet via het IP-adres van de firewall
- Een standaardroute van het spoke-subnet via het IP-adres van de firewall

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Typ in het zoekvak **routeringstabel** en druk op **Enter**.
3. Selecteer **Routeringstabel**.
4. Selecteer **Maken**.
1. Selecteer **FW-Hybrid-Test** als de resourcegroep.
1. Selecteer bij **Regio** **VS - oost**.
1. Voor de naam typt u **UDR-Hub-Spoke**.
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.
1. Nadat de routeringstabel is gemaakt, selecteert u deze om de pagina Routeringstabel te openen.
1. Selecteer **Routes** in de linkerkolom.
1. Selecteer **Toevoegen**.
1. Voor de routenaam typt u **ToSpoke**.
1. Voor het adresvoorvoegsel typt u **10.6.0.0/16**.
1. Voor het volgende hoptype selecteert u **Virtueel apparaat**.
1. Voor het adres van de volgende hop typt u het privé-IP-adres voor de firewall dat u eerder hebt genoteerd.
1. Selecteer **OK**.

Koppel nu de route aan het subnet.

1. Selecteer op de pagina **UDR-Hub-Spoke - Routes** **Subnetten**.
2. Selecteer **Koppelen**.
4. Onder **Virtueel netwerk** selecteert u **VNet-hub**.
5. Onder **Subnet** selecteert u **GatewaySubnet**.
6. Selecteer **OK**.

Maak nu de standaardroute vanaf het spoke-subnet.

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Typ in het zoekvak **routeringstabel** en druk op **Enter**.
3. Selecteer **Routeringstabel**.
5. Selecteer **Maken**.
7. Selecteer **FW-Hybrid-Test** als de resourcegroep.
8. Selecteer bij **Regio** **VS - oost**.
1. Voor de naam typt u **UDR-DG**.
4. Selecteer **Nee** als u **Gateway routes wilt door sturen**.
1. Selecteer **Controleren + maken**.
1. Selecteer **Maken**.
1. Nadat de routeringstabel is gemaakt, selecteert u deze om de pagina Routeringstabel te openen.
1. Selecteer **Routes** in de linkerkolom.
1. Selecteer **Toevoegen**.
1. Voor de routenaam typt u **ToHub**.
1. Voor het adresvoorvoegsel typt u **0.0.0.0/0**.
1. Voor het volgende hoptype selecteert u **Virtueel apparaat**.
1. Voor het adres van de volgende hop typt u het privé-IP-adres voor de firewall dat u eerder hebt genoteerd.
1. Selecteer **OK**.

Koppel nu de route aan het subnet.

1. Selecteer op de pagina **UDR-DG - Routes** **Subnetten**.
2. Selecteer **Koppelen**.
4. Onder **Virtueel netwerk** selecteert u **VNet-spoke**.
5. Onder **Subnet** selecteert u **SN-Workload**.
6. Selecteer **OK**.

## <a name="create-virtual-machines"></a>Virtuele machines maken

Maak nu de spoke-workloadmachines en on-premises virtuele machines en plaats ze in de toepasselijke subnetten.

### <a name="create-the-workload-virtual-machine"></a>De virtuele workloadmachine maken

Maak in het virtuele spoke-netwerk een virtuele machine waarop IIS wordt uitgevoerd, zonder openbaar IP-adres.

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Selecteer **Windows Server 2016 Datacenter** onder **Populair**.
3. Voer deze waarden in voor de virtuele machine:
    - **Resource groep** : Selecteer **FW-Hybrid-test**
    - **Naam van de virtuele machine**: *VM-spoke-01*
    - **Regio**  -  VS *-Oost*
    - **Gebruikers naam**: Typ een gebruikers naam
    - **Wacht woord**: Typ een wacht woord

4. Selecteer **Volgende: schijven**.
5. Accepteer de standaardwaarden en selecteer **Volgende: Netwerken**.
6. Selecteer **VNet-Spoke** voor het virtuele netwerk en het subnet is **SN-Workload**.
8. Selecteer voor **Openbare binnenkomende poorten** **Geselecteerde poorten toestaan** en selecteer vervolgens **HTTP (80)** en **RDP (3389)** .
1. Selecteer **Volgende: Beheer**.
1. Selecteer **Uitschakelen** voor **Diagnostische gegevens over opstarten**.
1. Selecteer **controleren + maken**, Controleer de instellingen op de pagina samen vatting en selecteer vervolgens **maken**.

### <a name="install-iis"></a>IIS installeren

1. Open Cloud Shell via de Azure-portal en controleer of deze is ingesteld op **PowerShell**.
2. Voer de volgende opdracht uit om IIS op de virtuele machine te installeren en indien nodig de locatie te wijzigen:

   ```azurepowershell-interactive
   Set-AzVMExtension `
           -ResourceGroupName FW-Hybrid-Test `
           -ExtensionName IIS `
           -VMName VM-Spoke-01 `
           -Publisher Microsoft.Compute `
           -ExtensionType CustomScriptExtension `
           -TypeHandlerVersion 1.4 `
           -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell      Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
           -Location EastUS
   ```

### <a name="create-the-on-premises-virtual-machine"></a>De on-premises virtuele machine maken

Dit is een virtuele machine waarmee u verbinding kunt maken met het openbare IP-adres via Extern bureaublad. Vanaf daar maakt u vervolgens verbinding met de on-premises server via de firewall.

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Selecteer **Windows Server 2016 Datacenter** onder **Populair**.
3. Voer deze waarden in voor de virtuele machine:
    - **Resource groep** : Selecteer bestaande en selecteer vervolgens **FW-Hybrid-test**
    - Naam van de **virtuele machine**  -  *VM-premises*
    - **Regio**  -  VS *-Oost*
    - **Gebruikers naam**: Typ een gebruikers naam
    - **Wachtwoord**: typ uw wachtwoord

4. Selecteer **Volgende: schijven**.
5. Accepteer de standaardwaarden en selecteer **Volgende: Netwerken**.
6. Selecteer **VNet-Onprem** voor het virtuele netwerk en controleer of het subnet **SN-Corp** is.
7. Selecteer voor **Openbare binnenkomende poorten** **Geselecteerde poorten toestaan** en selecteer vervolgens **RDP (3389)** .
8. Selecteer **Volgende: Beheer**.
9. Selecteer **uitschakelen** voor **Diagnostische gegevens over opstarten**.
10. Selecteer **controleren + maken**, Controleer de instellingen op de pagina samen vatting en selecteer vervolgens **maken**.

## <a name="test-the-firewall"></a>De firewall testen

1. Noteer eerst het privé-IP-adres van de virtuele machine VM-Spoke-01 op de overzichtspagina van VM-Spoke-01.

2. Maak vanuit de Azure-portal verbinding met de virtuele machine **VM-OnPrem**.
<!---2. Open a Windows PowerShell command prompt on **VM-Onprem**, and ping the private IP for **VM-spoke-01**.

   You should get a reply.--->
3. Open een webbrowser op **VM-OnPrem** en ga naar http://\<VM-spoke-01 private IP\>.

   U ziet de webpagina **VM-spoke-01**: ![Webpagina VM-Spoke-01](media/secure-hybrid-network/vm-spoke-01-web.png)

4. Maak vanaf de virtuele machine **VM-Onprem** via Extern bureaublad verbinding met **VM-spoke-01** op het privé-IP-adres.

   Deze verbinding moet worden gemaakt en u moet zich kunnen aanmelden.

Nu u hebt geverifieerd dat de firewallregels werken:

<!---- You can ping the server on the spoke VNet.--->
- U kunt de bladeren op de webserver in het virtuele spoke-netwerk.
- U kunt verbinding maken met de server in het virtuele spoke-netwerk met behulp van RDP.

Wijzig vervolgens de verzameling netwerkregels van de firewall in **Weigeren** om te controleren of de regels werken zoals verwacht.

1. Open de resource groep **FW-Hybrid-test** en selecteer het **Net01-** firewall beleid.
2. Selecteer onder **instellingen** **regel verzamelingen**.
1. Selecteer de **RCNet01** -regel verzameling.
1. Selecteer bij **Actie regelverzameling** de optie **Weigeren**.
1. Selecteer **Opslaan**.

Sluit eventuele externe bureaubladen en browsers in **VM-Onprem** voordat u de gewijzigde regels test. Nadat de update van de regelverzameling is voltooid, voert u de tests opnieuw uit. Ze moeten allemaal deze tijd niet verbinden.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt uw firewall bronnen voor nader onderzoek houden, of als u deze niet meer nodig hebt, verwijdert u de resource groep **FW-Hybrid-test** om alle firewall-gerelateerde resources te verwijderen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Uw virtuele WAN beveiligen met Azure Firewall Manager](secure-cloud-network.md)