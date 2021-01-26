---
title: 'Zelfstudie: Uw virtuele hub beveiligen met Azure PowerShell'
description: In dit artikel wordt beschreven hoe u een Azure Virtual WAN maakt in één regio met Azure Firewall ingeschakeld in de hub.
services: firewall-manager
author: jomore
ms.topic: tutorial
ms.service: firewall-manager
ms.date: 10/22/2020
ms.author: victorh
ms.openlocfilehash: 093b962bb9f42b660fb8cc5c0584ec67ebc87e48
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98789159"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-powershell"></a>Zelfstudie: Uw virtuele hub beveiligen met Azure PowerShell

In deze zelfstudie maakt u een Virtual WAN-exemplaar met een virtuele hub in één regio en implementeert u een Azure Firewall in de virtuele hub om de connectiviteit te beveiligen. In dit voorbeeld demonstreert u beveiligde connectiviteit tussen virtuele netwerken. Verkeer tussen virtuele netwerken en site-naar-site-, punt-naar-site of ExpressRoute-branches wordt ook ondersteund door virtuele beveiligde hub.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De virtuele WAN implementeren
> * Azure Firewall implementeren en aangepaste routering configureren
> * Connectiviteit testen

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

- PowerShell 7

   Voor deze zelfstudie moet u Azure PowerShell lokaal uitvoeren op PowerShell 7. Zie [Migreren van Windows PowerShell 5.1 naar PowerShell 7](/powershell/scripting/install/migrating-from-windows-powershell-51-to-powershell-7?view=powershell-7) om PowerShell 7 te installeren.
- Az.Network versie 3.2.0

    Als u Az.Network versie 3.4.0 of hoger hebt, moet u een downgrade uitvoeren om enkele van de opdrachten in deze zelfstudie te kunnen gebruiken. U kunt de versie van de module Az.Network controleren met behulp van de opdracht `Get-InstalledModule -Name Az.Network`. Als u de module Az.Network wilt verwijderen, voert u `Uninstall-Module -name az.network` uit. Als u de module Az.Network 3.2.0 wilt installeren, voert u `Install-Module az.network -RequiredVersion 3.2.0 -force` uit.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="initial-virtual-wan-deployment"></a>Eerste implementatie van Virtual WAN

Als eerste stap moet u enkele variabelen instellen en de resourcegroep, het virtuele WAN-exemplaar en de virtuele hub maken:

```azurepowershell
# Variable definition
$RG = "vwan-rg"
$Location = "westeurope"
$VwanName = "vwan"
$HubName =  "hub1"
# Create Resource Group, Virtual WAN and Virtual Hub
New-AzResourceGroup -Name $RG -Location $Location
$Vwan = New-AzVirtualWan -Name $VwanName -ResourceGroupName $RG -Location $Location -AllowVnetToVnetTraffic -AllowBranchToBranchTraffic -VirtualWANType "Standard"
$Hub = New-AzVirtualHub -Name $HubName -ResourceGroupName $RG -VirtualWan $Vwan -Location $Location -AddressPrefix "192.168.1.0/24" -Sku "Standard"
```

Maak twee virtuele netwerken en verbind ze met de hub als spokes:

```azurepowershell
# Create Virtual Network
$Spoke1 = New-AzVirtualNetwork -Name "spoke1" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.1.0/24"
$Spoke2 = New-AzVirtualNetwork -Name "spoke2" -ResourceGroupName $RG -Location $Location -AddressPrefix "10.1.2.0/24"
# Connect Virtual Network to Virtual WAN
$Spoke1Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RemoteVirtualNetwork $Spoke1
$Spoke2Connection = New-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RemoteVirtualNetwork $Spoke2
```

Op dit moment hebt u een volledig functioneel Virtual WAN dat any-to-any-connectiviteit biedt. Als u het wilt verbeteren met beveiliging, moet u een Azure Firewall implementeren op elke virtuele hub. Er kan Firewall-beleid worden gebruikt om het Azure Firewall-exemplaar van Virtual WAN efficiënt te beheren. In dit voorbeeld wordt dus ook een firewallbeleid gemaakt:

```azurepowershell
# New Firewall Policy
$FWPolicy = New-AzFirewallPolicy -Name "VwanFwPolicy" -ResourceGroupName $RG -Location $Location
# New Firewall Public IP
$AzFWPIPs = New-AzFirewallHubPublicIpAddress -Count 1
$AzFWHubIPs = New-AzFirewallHubIpAddress -PublicIP $AzFWPIPs
# New Firewall
$AzFW = New-AzFirewall -Name "azfw1" -ResourceGroupName $RG -Location $Location `
            -VirtualHubId $Hub.Id -FirewallPolicyId $FWPolicy.Id `
            -Sku AZFW_Hub -HubIPAddress $AzFWHubIPs
```

Het inschakelen van logboekregistratie van de Azure Firewall op Azure Monitor is optioneel, maar in dit voorbeeld gebruikt u de Firewall-logboeken om te bewijzen dat verkeer de firewall doorkruist:

```azurepowershell
# Optionally, enable looging of Azure Firewall to Azure Monitor
$LogWSName = "vwan-" + (Get-Random -Maximum 99999) + "-" + $RG
$LogWS = New-AzOperationalInsightsWorkspace -Location $Location -Name $LogWSName -Sku Standard -ResourceGroupName $RG
Set-AzDiagnosticSetting -ResourceId $AzFW.Id -Enabled $True -Category AzureFirewallApplicationRule, AzureFirewallNetworkRule -WorkspaceId $LogWS.ResourceId
```

## <a name="deploy-azure-firewall-and-configure-custom-routing"></a>Azure Firewall implementeren en aangepaste routering configureren

U hebt nu een Azure Firewall in de hub, maar u moet nog steeds de routering wijzigen zodat Virtual WAN het verkeer van de virtuele netwerken en van de branches via de firewall verzendt. Dit doet u in twee stappen:

1. Configureer alle virtuele netwerkverbindingen (en de branche-verbindingen indien aanwezig) zodat deze doorgeven aan de Routeringstabel `None`. Het effect van deze configuratie is dat andere virtuele netwerken en branches hun voorvoegsels niet leren en dat er geen routering is om ze te bereiken.
1. U kunt nu statische routes invoegen in de Routeringstabel `Default` (waarbij alle virtuele netwerken en branches standaard zijn gekoppeld), zodat al het verkeer naar de Azure Firewall wordt verzonden.

> [!NOTE]
> Dit is de configuratie die is geïmplementeerd bij het beveiligen van de connectiviteit vanuit de Azure Portal met Azure Firewall Manager

Begin met de eerste stap, om uw virtuele netwerkverbindingen te configureren zodat deze doorgeven aan de Routeringstabel `None`:

```azurepowershell
# Configure Virtual Network connections in hub to propagate to None
$VnetRoutingConfig = $Spoke1Connection.RoutingConfiguration    # We take $Spoke1Connection as baseline for the future vnet config, all vnets will have an identical config
$NoneRT = Get-AzVhubRouteTable -ResourceGroupName $RG -HubName $HubName -Name "noneRouteTable"
$NewPropRT = @{}
$NewPropRT.Add('Id', $NoneRT.id)
$PropRTList = @()
$PropRTList += $NewPropRT
$VnetRoutingConfig.PropagatedRouteTables.Ids = $PropRTList
$VnetRoutingConfig.PropagatedRouteTables.Labels = @()
$Spoke1Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke1" -RoutingConfiguration $VnetRoutingConfig
$Spoke2Connection = Update-AzVirtualHubVnetConnection -ResourceGroupName $RG -ParentResourceName  $HubName -Name "spoke2" -RoutingConfiguration $VnetRoutingConfig
```

U kunt nu doorgaan met de tweede stap, om de statische routes toe te voegen aan de routeringstabel `Default`. In dit voorbeeld past u de standaard configuratie toe die Azure Firewall Manager zou genereren bij het beveiligen van de connectiviteit in een Virtual WAN, maar u kunt de lijst met voorvoegsels in de statische route aanpassen aan uw specifieke vereisten:

```azurepowershell
# Create static routes in default Route table
$AzFWId = $(Get-AzVirtualHub -ResourceGroupName $RG -name  $HubName).AzureFirewall.Id
$AzFWRoute = New-AzVHubRoute -Name "private-traffic" -Destination @("0.0.0.0/0", "10.0.0.0/8", "172.16.0.0/12", "192.168.0.0/16") -DestinationType "CIDR" -NextHop $AzFWId -NextHopType "ResourceId"
$DefaultRT = Update-AzVHubRouteTable -Name "defaultRouteTable" -ResourceGroupName $RG -VirtualHubName  $HubName -Route @($AzFWRoute)
```

## <a name="test-connectivity"></a>Connectiviteit testen

Nu hebt u een volledig operationele beveiligde hub. Als u de connectiviteit wilt testen, hebt u één virtuele machine nodig in elke spoke van het virtuele netwerk dat is verbonden met de hub:

```azurepowershell
# Create VMs in spokes for testing
$VMLocalAdminUser = "lab-user"
$VMLocalAdminSecurePassword = ConvertTo-SecureString -AsPlainText -Force
$VMCredential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
$VMSize = "Standard_B2ms"
# Spoke1
$Spoke1 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke1"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke1 -AddressPrefix "10.1.1.0/26"
$Spoke1 | Set-AzVirtualNetwork
$VM1 = New-AzVM -Name "spoke1-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke1" -SubnetName "vm" -PublicIpAddressName "spoke1-pip"
$NIC1 = Get-AzNetworkInterface -ResourceId $($VM1.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke1VMPrivateIP = $NIC1.IpConfigurations[0].PrivateIpAddress
$Spoke1VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke1-pip")
# Spoke2
$Spoke2 = Get-AzVirtualNetwork -ResourceGroupName $RG -Name "spoke2"
Add-AzVirtualNetworkSubnetConfig -Name "vm" -VirtualNetwork $Spoke2 -AddressPrefix "10.1.2.0/26"
$Spoke2 | Set-AzVirtualNetwork
$VM2 = New-AzVM -Name "spoke2-vm" -ResourceGroupName $RG -Location $Location `
            -Image "UbuntuLTS" -credential $VMCredential `
            -VirtualNetworkName "spoke2" -SubnetName "vm" -PublicIpAddressName "spoke2-pip"
$NIC2 = Get-AzNetworkInterface -ResourceId $($VM2.NetworkProfile.NetworkInterfaces[0].Id)
$Spoke2VMPrivateIP = $NIC2.IpConfigurations[0].PrivateIpAddress
$Spoke2VMPIP = $(Get-AzPublicIpAddress -ResourceGroupName $RG -Name "spoke2-pip")
```

De standaardconfiguratie in het firewallbeleid is om alles te verwijderen. Daarom moet u enkele regels configureren. Begin met DNAT-regels, zodat de virtuele testmachines toegankelijk zijn via het openbare IP-adres van de Firewall:

```azurepowershell
# Adding DNAT rules for virtual machines in the spokes
$AzFWPublicAddress = $AzFW.HubIPAddresses.PublicIPs.Addresses[0].Address
$NATRuleSpoke1 = New-AzFirewallPolicyNatRule -Name "Spoke1SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10001 `
        -TranslatedAddress $Spoke1VMPrivateIP -TranslatedPort 22
$NATRuleSpoke2 = New-AzFirewallPolicyNatRule -Name "Spoke2SSH" -Protocol "TCP" `
        -SourceAddress "*" -DestinationAddress $AzFWPublicAddress -DestinationPort 10002 `
        -TranslatedAddress $Spoke2VMPrivateIP -TranslatedPort 22
$NATCollection = New-AzFirewallPolicyNatRuleCollection -Name "SSH" -Priority 100 `
        -Rule @($NATRuleSpoke1, $NATRuleSpoke2) -ActionType "Dnat"
$NATGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "NAT" -Priority 100 -RuleCollection $NATCollection -FirewallPolicyObject $FWPolicy
```

Nu kunt u enkele voorbeeldregels configureren. Definieer een netwerkregel die SSH-verkeer toestaat, plus een toepassingsregel waarmee Internettoegang tot de FQDN-naam (Fully Qualified Domain Name) `ifconfig.co` wordt toegestaan. Deze URL retourneert het bron-IP-adres dat wordt weergegeven in de HTTP-aanvraag:

```azurepowershell
# Add Network Rule
$SSHRule = New-AzFirewallPolicyNetworkRule -Name PermitSSH -Protocol TCP `
        -SourceAddress "10.0.0.0/8" -DestinationAddress "10.0.0.0/8" -DestinationPort 22
$NetCollection = New-AzFirewallPolicyFilterRuleCollection -Name "Management" -Priority 100 -ActionType Allow -Rule $SSHRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "Management" -Priority 200 -RuleCollection $NetCollection -FirewallPolicyObject $FWPolicy
# Add Application Rul
$ifconfigRule = New-AzFirewallPolicyApplicationRule -Name PermitIfconfig -SourceAddress "10.0.0.0/8" -TargetFqdn "ifconfig.co" -Protocol "http:80","https:443"
$AppCollection = New-AzFirewallPolicyFilterRuleCollection -Name "TargetURLs" -Priority 300 -ActionType Allow -Rule $ifconfigRule
$NetGroup = New-AzFirewallPolicyRuleCollectionGroup -Name "TargetURLs" -Priority 300 -RuleCollection $AppCollection -FirewallPolicyObject $FWPolicy
```

Voordat u verkeer daadwerkelijk verzendt, kunt u de effectieve routes van de virtuele machines inspecteren. Deze moeten de voorvoegsels bevatten die zijn geleerd van het Virtual WAN (`0.0.0.0/0` plus RFC1918), maar niet het voorvoegsel van de andere spoke:

```azurepowershell
# Check effective routes in the VM NIC in spoke 1
# Note that 10.1.2.0/24 (the prefix for spoke2) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC1.Name | ft
# Check effective routes in the VM NIC in spoke 2
# Note that 10.1.1.0/24 (the prefix for spoke1) should not appear
Get-AzEffectiveRouteTable -ResourceGroupName $RG -NetworkInterfaceName $NIC2.Name | ft
```

Genereer nu verkeer van de ene virtuele machine naar de andere en controleer of dit wordt verwijderd in de Azure Firewall. In de volgende SSH-opdrachten moet u de vingerafdrukken van de virtuele machines accepteren en het wachtwoord opgeven dat u hebt gedefinieerd tijdens het maken van de virtuele machines. In dit voorbeeld gaat u vijf ICMP-echo-aanvraagpakketten verzenden vanaf de virtuele machine in spoke1 naar spoke2, plus een TCP-verbindingspoging op poort 22 met behulp van het Linux-hulpprogramma `nc` (met de `-vz`-vlaggen wordt alleen een verbindingsaanvraag verzonden en wordt het resultaat weergegeven). Als het goed is, ziet u dat de ping mislukt en dat de TCP-verbindingspoging op poort 22 slaagt, omdat deze wordt toegestaan door de netwerkregel die u eerder hebt geconfigureerd:

```azurepowershell
# Connect to one VM and ping the other. It shouldnt work, because the firewall should drop the traffic, since no rule for ICMP is configured
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "ping $Spoke2VMPrivateIP -c 5"
# Connect to one VM and send a TCP request on port 22 to the other. It should work, because the firewall is configured to allow SSH traffic (port 22)
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "nc -vz $Spoke2VMPrivateIP 22"
```

U kunt ook internetverkeer controleren. HTTP-aanvragen via het hulpprogramma `curl` naar de FQDN die u hebt toegestaan in het firewallbeleid (`ifconfig.co`) zouden moeten werken, maar HTTP-aanvragen naar een andere bestemming moeten mislukken (in dit voorbeeld test u met `bing.com`):

```azurepowershell
# This HTTP request should succeed, since it is allowed in an app rule in the AzFW, and return the public IP of the FW
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 ifconfig.co"
# This HTTP request should fail, since the FQDN bing.com is not in any app rule in the firewall policy
ssh $AzFWPublicAddress -p 10001 -l $VMLocalAdminUser "curl -s4 bing.com"
```

De eenvoudigste manier om te controleren of de pakketten door de firewall worden verwijderd, is door de logboeken te controleren. Aangezien u de Azure Firewall hebt geconfigureerd om logboeken te verzenden naar Azure Monitor, kunt u de Kusto-querytaal gebruiken om de relevante logboeken uit Azure Monitor op te halen:

> [!NOTE]
> Het kan ongeveer 1 minuut duren voordat de logboeken worden verzonden naar Azure Monitor

```azurepowershell
# Getting Azure Firewall network rule Logs
$LogWS = Get-AzOperationalInsightsWorkspace -ResourceGroupName $RG
$LogQuery = 'AzureDiagnostics 
| where Category == "AzureFirewallNetworkRule" 
| where TimeGenerated >= ago(5m) 
| parse msg_s with Protocol " request from " SourceIP ":" SourcePortInt:int " to " TargetIP ":" TargetPortInt:int * 
| parse msg_s with * ". Action: " Action1a 
| parse msg_s with * " was " Action1b " to " NatDestination 
| parse msg_s with Protocol2 " request from " SourceIP2 " to " TargetIP2 ". Action: " Action2 
| extend SourcePort = tostring(SourcePortInt),TargetPort = tostring(TargetPortInt) 
| extend Action = case(Action1a == "", case(Action1b == "",Action2,Action1b), Action1a),Protocol = case(Protocol == "", Protocol2, Protocol),SourceIP = case(SourceIP == "", SourceIP2, SourceIP),TargetIP = case(TargetIP == "", TargetIP2, TargetIP),SourcePort = case(SourcePort == "", "N/A", SourcePort),TargetPort = case(TargetPort == "", "N/A", TargetPort),NatDestination = case(NatDestination == "", "N/A", NatDestination) 
| project TimeGenerated, Protocol, SourceIP,SourcePort,TargetIP,TargetPort,Action, NatDestination, Resource 
| take 25 '
$(Invoke-AzOperationalInsightsQuery -Workspace $LogWS -Query $LogQuery).Results | ft
```

In de vorige opdracht moet u verschillende vermeldingen zien:

* Er wordt een DNAT uitgevoerd op uw SSH-verbinding
* Verwijderde ICMP-pakketten tussen de VM’s in de spokes (10.1.1.4 en 10.1.2.4)
* Toegestane SSH-verbindingen tussen de VM’s in de spokes

Hier volgt een voorbeeld van de uitvoer die wordt geproduceerd met de bovenstaande opdracht:

```
TimeGenerated            Protocol    SourceIP       SourcePort TargetIP      TargetPort Action  NatDestination Resource
-------------            --------    --------       ---------- --------      ---------- ------  -------------- --------
2020-10-04T20:53:02.41Z  TCP         109.125.122.99 62281      51.105.224.44 10001      DNAT'ed 10.1.1.4:22    AZFW1
2020-10-04T20:53:07.045Z TCP         10.1.1.4       35932      10.1.2.4      22         Allow   N/A            AZFW1
2020-10-04T20:53:50.119Z TCP         109.125.122.99 62293      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:52:47.475Z TCP         109.125.122.99 62273      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:04.682Z TCP         109.125.122.99 62200      51.105.224.44 10001      DNAT'ed 10.1.2.4:22    AZFW1
2020-10-04T20:51:17.031Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:18.049Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:19.075Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:20.097Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:51:21.121Z ICMP Type=8 10.1.1.4       N/A        10.1.2.4      N/A        Deny    N/A            AZFW1
2020-10-04T20:52:52.356Z TCP         10.1.1.4       53748      10.1.2.4      22         Allow   N/A            AZFW1
```

Als u de logboeken voor de toepassingsregels wilt weergeven (met een beschrijving van de toegestane en geweigerde HTTP-verbindingen) of als u de manier wilt wijzigen waarop de logboeken worden weergegeven, kunt u het proberen met andere KQL-query’s. U vindt enkele voorbeelden in [Azure Monitor-logboeken voor Azure Firewall](../firewall/firewall-workbook.md).


## <a name="clean-up-resources"></a>Resources opschonen

Als u de testomgeving wilt verwijderen, kunt u de resourcegroep met alle ingesloten objecten verwijderen:

```azurepowershell
# Delete resource group and all contained resources
Remove-AzResourceGroup -Name $RG
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over vertrouwde beveiligingspartners](trusted-security-partners.md)