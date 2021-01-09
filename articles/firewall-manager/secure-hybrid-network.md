---
title: 'Zelfstudie: Uw virtuele hubnetwerk beveiligen met Azure Firewall Manager'
description: In deze zelfstudie leert u hoe u uw virtuele netwerk beveiligt met Azure Firewall Manager met behulp van Azure Portal.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 06/30/2020
ms.author: victorh
ms.openlocfilehash: 5d662efc927235711c4dd5e8a424d8775279c30b
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98048042"
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
4. Selecteer **Volgende: Regels**.
5. Selecteer **Een regelverzameling toevoegen**.
6. Bij **Naam** typt u **RCNet01**.
7. Bij **Type regelverzameling** selecteert u **Netwerk**.
8. Bij **Prioriteit** typt u **100**.
9. Bij **Actie** selecteert u **Toestaan**.
10. Onder **Regels** typt u bij **Naam** de naam **AllowWeb**.
11. Bij **Bronadressen** typt u **192.168.1.0/24**.
12. Bij **Protocol** selecteert u **TCP**.
13. Typ bij **Doelpoorten** **80**.
14. Bij **Doeltype** selecteert u **IP-adres**.
15. Bij **Doel** typt u **10.6.0.0/16**.
16. Voer op de volgende rij van de regel de volgende informatie in:
 
    Naam: typ **AllowRDP**<br>
    IP-adres van bron: typ **192.168.1.0/24**.<br>
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
2. Selecteer **Virtueel netwerk** onder **Netwerken**.
4. Geef bij **Naam** **VNet-hub** op.
5. Bij **Adresruimte** typt u **10.5.0.0/16**.
6. Bij **Abonnement** selecteert u uw abonnement.
7. Geef voor **Resourcegroep** de naam **FW-Hybrid-Test** op.
8. Selecteer bij **Locatie** **VS - oost**.
9. Onder **Subnet** typt u bij **Naam** de naam **AzureFirewallSubnet**. De firewall zal zich in dit subnet bevinden, en de subnetnaam **moet** AzureFirewallSubnet zijn.
10. Bij **Adresbereik** typt u **10.5.0.0/26**. 
11. Accepteer de standaardwaarden voor de overige instellingen en selecteer **Maken**.

## <a name="create-the-spoke-virtual-network"></a>Het virtuele spoke-netwerk maken

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Selecteer **Virtueel netwerk** onder **Netwerken**.
4. Bij **Naam** typt u **VNet-Spoke**.
5. Bij **Adresruimte** typt u **10.6.0.0/16**.
6. Bij **Abonnement** selecteert u uw abonnement.
7. Geef voor **Resourcegroep** de naam **FW-Hybrid-Test** op.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
9. Onder **Subnet** typt u **SN-Workload** bij **Naam**.
10. Bij **Adresbereik** typt u **10.6.0.0/24**.
11. Accepteer de standaardwaarden voor de overige instellingen en selecteer **Maken**.

## <a name="create-the-on-premises-virtual-network"></a>Het on-premises virtuele netwerk maken

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Selecteer **Virtueel netwerk** onder **Netwerken**.
4. Bij **Naam** typt u **VNet-OnPrem**.
5. Bij **Adresruimte** typt u **192.168.0.0/16**.
6. Bij **Abonnement** selecteert u uw abonnement.
7. Geef voor **Resourcegroep** de naam **FW-Hybrid-Test** op.
8. Bij **Locatie** selecteert u dezelfde locatie die u eerder hebt gebruikt.
9. Onder **Subnet** typt u **SN-Corp** bij **Naam**.
10. Bij **Adresbereik** typt u **192.168.1.0/24**.
11. Accepteer de standaardwaarden voor de overige instellingen en selecteer **Maken**.

Nadat het virtuele netwerk is geïmplementeerd, maakt u een tweede subnet voor de gateway.

1. Selecteer op de pagina **VNet-Onprem** **Subnetten**.
2. Selecteer **+Subnet**.
3. Typ **GatewaySubnet** bij **Naam**.
4. Typ **192.168.2.0/24** bij **Adresbereik (CIDR-blok)** .
5. Selecteer **OK**.

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Dit is het openbare IP-adres dat wordt gebruikt voor de on-premises gateway.

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Typ in het zoekvak **openbaar IP-adres** en druk op **Enter**.
3. Selecteer **Openbaar IP-adres** en selecteer vervolgens **Maken**.
4. Bij de naam typt u **VNet-Onprem-GW-pip**.
5. Bij de resourcegroep typt u **FW-Hybrid-Test**.
6. Selecteer bij **Locatie** de optie **VS - oost**.
7. Accepteer de overige standaardwaarden en klik op **Maken**.

## <a name="configure-and-deploy-the-firewall"></a>De firewall configureren en implementeren

Wanneer beveiligingsbeleidsregels aan een dergelijke hub worden gekoppeld, wordt dit een *virtueel hubnetwerk* genoemd.

Converteer het virtuele netwerk **VNet-Hub** naar een virtueel *hubnetwerk* en beveilig het met Azure Firewall.

1. Typ **Firewall Manager** in de zoekbalk van Azure Portal en druk op **Enter**.
3. Selecteer op de pagina van Azure Firewall Manager onder **Beveiliging toevoegen aan virtuele netwerken** de optie **Virtuele netwerken van de hub weergeven**.
4. Selecteer **Virtuele netwerken** converteren.
5. Selecteer **VNet-hub** en selecteer vervolgens **Volgende: Azure Firewall**.
6. Selecteer bij **Firewallbeleid** de optie **Pol-Net01**.
7. Selecteer **Volgende > Controleren en bevestigen**
8. Controleer de gegevens en selecteer vervolgens **Bevestigen**.


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
10. Bij **Openbaar IP-adres** selecteert u **Bestaande gebruiken* en typt u **VNet-Onprem-GW-pip** als de naam.
11. Accepteer voor de overige instellingen de standaardwaarden en selecteer **Beoordelen en maken**.
12. Controleer de configuratie en selecteer vervolgens **Maken**.

### <a name="create-the-vpn-connections"></a>De VPN-verbindingen maken

U kunt nu de VPN-verbindingen maken tussen de hub-gateway en de on-premises gateway.

In deze stap maakt u de verbinding van het virtuele hub-netwerk naar het on-premises virtuele netwerk. Hier ziet u een gedeelde sleutel waarnaar wordt verwezen in de voorbeelden. U kunt uw eigen waarden voor de gedeelde sleutel gebruiken. Het belangrijkste is dat de gedeelde sleutel voor beide verbindingen moet overeenkomen. Het kan even duren voordat de verbinding is gemaakt.

1. Open de resourcegroep **FW-Hybrid-Test** en selecteer de gateway **GW-hub**.
2. Selecteer **Verbindingen** in de linkerkolom.
3. Selecteer **Toevoegen**.
4. Typ **Hub-to-Onprem** als de naam van de verbinding.
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
4. Bij **Naam** typt u **HubtoSpoke**.
5. Bij **Virtueel netwerk** selecteert u **VNet-spoke**.
6. Voor de naam van de peering van VNetSpoke naar VNet-hub typt u **SpoketoHub**.
7. Selecteer **Gatewayoverdracht toestaan**.
8. Selecteer **OK**.

### <a name="configure-additional-settings-for-the-spoketohub-peering"></a>Aanvullende instellingen configureren voor de SpoketoHub-peering

U moet **Doorgestuurd verkeer toestaan** inschakelen voor de SpoketoHub-peering.

1. Open de resourcegroep **FW-Hybrid-Test** en selecteer het virtuele netwerk **VNet-Spoke**.
2. Selecteer in de linkerkolom **Peerings**.
3. Selecteer de peering **SpoketoHub**.
4. Selecteer **Ingeschakeld** onder **Doorgestuurd verkeer van VNet-hub naar VNet-spoke toestaan**.
5. Selecteer **Opslaan**.

## <a name="create-the-routes"></a>De routes maken

Maak nu een paar routes:

- Een route van het subnet van de hub-gateway naar het spoke-subnet via het IP-adres van de firewall
- Een standaardroute van het spoke-subnet via het IP-adres van de firewall

1. Selecteer op de startpagina van de Azure-portal **Een resource maken**.
2. Typ in het zoekvak **routeringstabel** en druk op **Enter**.
3. Selecteer **Routeringstabel**.
4. Selecteer **Maken**.
5. Voor de naam typt u **UDR-Hub-Spoke**.
6. Selecteer **FW-Hybrid-Test** als de resourcegroep.
8. Selecteer bij **Locatie** **(VS) VS - oost**.
9. Selecteer **Maken**.
10. Nadat de routeringstabel is gemaakt, selecteert u deze om de pagina Routeringstabel te openen.
11. Selecteer **Routes** in de linkerkolom.
12. Selecteer **Toevoegen**.
13. Voor de routenaam typt u **ToSpoke**.
14. Voor het adresvoorvoegsel typt u **10.6.0.0/16**.
15. Voor het volgende hoptype selecteert u **Virtueel apparaat**.
16. Voor het adres van de volgende hop typt u het privé-IP-adres voor de firewall dat u eerder hebt genoteerd.
17. Selecteer **OK**.

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
6. Voor de naam typt u **UDR-DG**.
7. Selecteer **FW-Hybrid-Test** als de resourcegroep.
8. Selecteer bij **Locatie** **(VS) VS - oost**.
4. Selecteer **Uitgeschakeld** voor **Doorgifte van route van virtuele netwerkgateway**.
1. Selecteer **Maken**.
2. Nadat de routeringstabel is gemaakt, selecteert u deze om de pagina Routeringstabel te openen.
3. Selecteer **Routes** in de linkerkolom.
4. Selecteer **Toevoegen**.
5. Voor de routenaam typt u **ToHub**.
6. Voor het adresvoorvoegsel typt u **0.0.0.0/0**.
7. Voor het volgende hoptype selecteert u **Virtueel apparaat**.
8. Voor het adres van de volgende hop typt u het privé-IP-adres voor de firewall dat u eerder hebt genoteerd.
9. Selecteer **OK**.

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
    - **Resourcegroep**: selecteer **FW-Hybrid-Test**.
    - **Naam van virtuele machine**: *VM-Spoke-01*.
    - **Regio** -  *(VS) VS - oost*.
    - **Gebruikersnaam**: *azureuser*.
    - **Wachtwoord**: typ uw wachtwoord

4. Selecteer **Volgende: schijven**.
5. Accepteer de standaardwaarden en selecteer **Volgende: Netwerken**.
6. Selecteer **VNet-Spoke** voor het virtuele netwerk en het subnet is **SN-Workload**.
7. Selecteer **Geen** voor **Openbaar IP**.
8. Selecteer voor **Openbare binnenkomende poorten** **Geselecteerde poorten toestaan** en selecteer vervolgens **HTTP (80)** en **RDP (3389)** .
9. Selecteer **Volgende: Beheer**.
10. Selecteer **Uit** voor **Diagnostische gegevens over opstarten**.
11. Selecteer **Beoordelen en maken**, controleer de instellingen op de overzichtspagina en selecteer ten slotte **Maken**.

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
    - **Resourcegroep**: selecteer Bestaande gebruiken en selecteer vervolgens **FW-Hybrid-Test**.
    - **Naam virtuele machine** - *VM-Onprem*.
    - **Regio** -  *(VS) VS - oost*.
    - **Gebruikersnaam**: *azureuser*.
    - **Wachtwoord**: typ uw wachtwoord.

4. Selecteer **Volgende: schijven**.
5. Accepteer de standaardwaarden en selecteer **Volgende: Netwerken**.
6. Selecteer **VNet-Onprem** voor het virtuele netwerk en controleer of het subnet **SN-Corp** is.
7. Selecteer voor **Openbare binnenkomende poorten** **Geselecteerde poorten toestaan** en selecteer vervolgens **RDP (3389)** .
8. Selecteer **Volgende: Beheer**.
9. Selecteer **Uit** voor **Diagnostische gegevens over opstarten**.
10. Selecteer **Beoordelen en maken**, controleer de instellingen op de overzichtspagina en selecteer ten slotte **Maken**.

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

1. Open de resourcegroep **FW-Hybrid-Test** en selecteer het firewallbeleid **Pol-Net01**.
2. Selecteer **Regels** onder **Instellingen**.
3. Selecteer onder **Netwerkregels** de regelverzameling **RCNet01**, selecteer de weglatingstekens (...) en selecteer **Bewerken**.
4. Selecteer bij **Actie regelverzameling** de optie **Weigeren**.
5. Selecteer **Opslaan**.

Sluit eventuele externe bureaubladen en browsers in **VM-Onprem** voordat u de gewijzigde regels test. Nadat de update van de regelverzameling is voltooid, voert u de tests opnieuw uit. Ze moeten deze keer allemaal mislukken.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de firewall-resources voor de volgende zelfstudie bewaren. Als u ze niet meer nodig hebt, verwijdert u de resourcegroep **FW-Hybrid-Test** om alle firewall-gerelateerde resources te verwijderen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Zelfstudie: Uw virtuele WAN beveiligen met Azure Firewall Manager](secure-cloud-network.md)