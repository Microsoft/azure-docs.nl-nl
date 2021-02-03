---
title: 'Zelfstudie: Met SQL beheerd exemplaar toevoegen aan een failovergroep'
titleSuffix: Azure SQL Managed Instance
description: In deze zelfstudie leert u hoe u een failovergroep maakt tussen een primair en secundair met Azure SQL beheerd exemplaar.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: high-availability
ms.custom: sqldbrb=1, devx-track-azurepowershell
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: sashan
ms.date: 08/27/2019
ms.openlocfilehash: 1609f188af8ffb58251edc806e19f7820a6b0869
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/03/2021
ms.locfileid: "99525718"
---
# <a name="tutorial-add-sql-managed-instance-to-a-failover-group"></a>Zelfstudie: Met SQL beheerd exemplaar toevoegen aan een failovergroep
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Beheerde exemplaren van Azure SQL Managed Instance toevoegen aan een failovergroep. In dit artikel leert u het volgende:

> [!div class="checklist"]
> - Een primair beheerd exemplaar maken.
> - Een secundair beheerd exemplaar maken als onderdeel van een [failovergroep](../database/auto-failover-group-overview.md). 
> - Failover testen.

  > [!NOTE]
  > - Zorg er bij het doorlopen van deze zelfstudie voor dat u resources configureert met de [vereisten voor het instellen van failovergroepen voor SQL Managed Instance](../database/auto-failover-group-overview.md#enabling-geo-replication-between-managed-instances-and-their-vnets). 
  > - Het maken van een beheerd exemplaar kan aanzienlijke tijd duren. Hierdoor kan het enkele uren duren om deze zelfstudie te voltooien. Raadpleeg [Beheerbewerkingen voor SQL Managed Instance](sql-managed-instance-paas-overview.md#management-operations) voor meer informatie over inrichtingsduur. 
  > - Voor beheerde exemplaren die deel uitmaken van een failovergroep zijn [Azure ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md), globale VNet-peering of twee verbonden VPN-gateways vereist. Deze zelfstudie biedt stappen voor het maken en verbinden van de VPN-gateways. Sla deze stappen over als u ExpressRoute al hebt geconfigureerd. 


## <a name="prerequisites"></a>Vereisten

# <a name="portal"></a>[Portal](#tab/azure-portal)
Het volgende moet zijn geïnstalleerd om deze zelfstudie te voltooien: 

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) als u er nog geen hebt.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Zorg dat u over het volgende beschikt om de zelfstudie te kunnen voltooien:

- Een Azure-abonnement. [Maak een gratis account](https://azure.microsoft.com/free/) als u er nog geen hebt.
- [Azure PowerShell](/powershell/azure/)

---


## <a name="create-a-resource-group-and-primary-managed-instance"></a>Een resourcegroep en primair beheerd exemplaar maken

In deze stap maakt u de resourcegroep en het primaire beheerde exemplaar voor uw failovergroep met behulp van de Azure-portal of PowerShell. 

Implementeer beide beheerde exemplaren in [gekoppelde regio's](../../best-practices-availability-paired-regions.md), om prestatieredenen. Beheerde exemplaren die zich in geografisch gekoppelde regio's bevinden, presteren veel beter dan die in niet-gekoppelde regio's. 


# <a name="portal"></a>[Portal](#tab/azure-portal) 

Maak de resourcegroep en het primaire beheerde exemplaar met behulp van de Azure-portal. 

1. Selecteer **Azure SQL** in het menu aan de linkerzijde van de Azure Portal. Als **Azure SQL** niet voorkomt in de lijst, selecteert u **Alle services** en typt u `Azure SQL` in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze favoriet te maken en toe te voegen als item in de linkernavigatiebalk. 
1. Selecteer **+ Toevoegen** om de pagina **SQL-implementatieoptie selecteren** te openen. U kunt meer informatie over de verschillende databases bekijken door **Details weergeven** te selecteren op de tegel **Databases**.
1. Selecteer **Maken** op de tegel **Met SQL beheerde exemplaren**. 

    ![SQL Managed Instance selecteren](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Doe het volgende op het tabblad **Basisbeginselen** van de pagina **Met Azure SQL beheerd exemplaar maken**:
    1. Selecteer onder **Projectdetails** uw **abonnement** in de vervolgkeuzelijst, en kies vervolgens **Nieuwe resourcegroep maken**. Typ een naam voor uw resourcegroep, zoals `myResourceGroup`. 
    1. Geef onder **Details van met SQL beheerd exemplaar** de naam op van uw beheerde exemplaar, en de regio waarin u het beheerde exemplaar wilt implementeren. Laat **Compute + opslag** staan op de standaardwaarden. 
    1. Geef onder **Beheerdersaccount** aanmeldingsgegevens voor een beheerder op, zoals `azureuser`, en een complex beheerderswachtwoord. 

    ![Een primair beheerd exemplaar maken](./media/failover-group-add-instance-tutorial/primary-sql-mi-values.png)

1. Laat de overige instellingen op de standaardwaarden staan, en selecteer **Controleren + maken** om de SQL Managed Instance-instellingen te controleren. 
1. Selecteer **Maken** om het primaire beheerde exemplaar te maken. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Maak de resourcegroep en het primaire beheerde exemplaar met behulp van PowerShell. 

   ```powershell-interactive
   # Connect-AzAccount
   # The SubscriptionId in which to create these objects
   $SubscriptionId = '<Subscription-ID>'
   # Create a random identifier to use as subscript for the different resource names
   $randomIdentifier = $(Get-Random)
   # Set the resource group name and location for SQL Managed Instance
   $resourceGroupName = "myResourceGroup-$randomIdentifier"
   $location = "eastus"
   $drLocation = "eastus2"
   
   # Set the networking values for your primary managed instance
   $primaryVNet = "primaryVNet-$randomIdentifier"
   $primaryAddressPrefix = "10.0.0.0/16"
   $primaryDefaultSubnet = "primaryDefaultSubnet-$randomIdentifier"
   $primaryDefaultSubnetAddress = "10.0.0.0/24"
   $primaryMiSubnetName = "primaryMISubnet-$randomIdentifier"
   $primaryMiSubnetAddress = "10.0.0.0/24"
   $primaryMiGwSubnetAddress = "10.0.255.0/27"
   $primaryGWName = "primaryGateway-$randomIdentifier"
   $primaryGWPublicIPAddress = $primaryGWName + "-ip"
   $primaryGWIPConfig = $primaryGWName + "-ipc"
   $primaryGWAsn = 61000
   $primaryGWConnection = $primaryGWName + "-connection"
   
   
   # Set the networking values for your secondary managed instance
   $secondaryVNet = "secondaryVNet-$randomIdentifier"
   $secondaryAddressPrefix = "10.128.0.0/16"
   $secondaryDefaultSubnet = "secondaryDefaultSubnet-$randomIdentifier"
   $secondaryDefaultSubnetAddress = "10.128.0.0/24"
   $secondaryMiSubnetName = "secondaryMISubnet-$randomIdentifier"
   $secondaryMiSubnetAddress = "10.128.0.0/24"
   $secondaryMiGwSubnetAddress = "10.128.255.0/27"
   $secondaryGWName = "secondaryGateway-$randomIdentifier"
   $secondaryGWPublicIPAddress = $secondaryGWName + "-IP"
   $secondaryGWIPConfig = $secondaryGWName + "-ipc"
   $secondaryGWAsn = 62000
   $secondaryGWConnection = $secondaryGWName + "-connection"
   
   
   
   # Set the SQL Managed Instance name for the new managed instances
   $primaryInstance = "primary-mi-$randomIdentifier"
   $secondaryInstance = "secondary-mi-$randomIdentifier"
   
   # Set the admin login and password for SQL Managed Instance
   $secpasswd = "PWD27!"+(New-Guid).Guid | ConvertTo-SecureString -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential ("azureuser", $secpasswd)
   
   
   # Set the SQL Managed Instance service tier, compute level, and license mode
   $edition = "General Purpose"
   $vCores = 8
   $maxStorage = 256
   $computeGeneration = "Gen5"
   $license = "LicenseIncluded" #"BasePrice" or LicenseIncluded if you have don't have SQL Server license that can be used for AHB discount
   
   # Set failover group details
   $vpnSharedKey = "mi1mi2psk"
   $failoverGroupName = "failovergroup-$randomIdentifier"
   
   # Show randomized variables
   Write-host "Resource group name is" $resourceGroupName
   Write-host "Password is" $secpasswd
   Write-host "Primary Virtual Network name is" $primaryVNet
   Write-host "Primary default subnet name is" $primaryDefaultSubnet
   Write-host "Primary SQL Managed Instance subnet name is" $primaryMiSubnetName
   Write-host "Secondary Virtual Network name is" $secondaryVNet
   Write-host "Secondary default subnet name is" $secondaryDefaultSubnet
   Write-host "Secondary SQL Managed Instance subnet name is" $secondaryMiSubnetName
   Write-host "Primary SQL Managed Instance name is" $primaryInstance
   Write-host "Secondary SQL Managed Instance name is" $secondaryInstance
   Write-host "Failover group name is" $failoverGroupName
   
   # Suppress networking breaking changes warning (https://aka.ms/azps-changewarnings
   Set-Item Env:\SuppressAzurePowerShellBreakingChangeWarnings "true"
   
   # Set the subscription context
   Set-AzContext -SubscriptionId $subscriptionId 
   
   # Create the resource group
   Write-host "Creating resource group..."
   $resourceGroup = New-AzResourceGroup -Name $resourceGroupName -Location $location -Tag @{Owner="SQLDB-Samples"}
   $resourceGroup
   
   # Configure the primary virtual network
   Write-host "Creating primary virtual network..."
   $primaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -Name $primaryVNet `
                         -AddressPrefix $primaryAddressPrefix
   
                     Add-AzVirtualNetworkSubnetConfig `
                         -Name $primaryMiSubnetName `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                     | Set-AzVirtualNetwork
   $primaryVirtualNetwork
   
   
   # Configure the primary managed instance subnet
   Write-host "Configuring primary MI subnet..."
   $primaryVirtualNetwork = Get-AzVirtualNetwork -Name $primaryVNet -ResourceGroupName $resourceGroupName
   
   
   $primaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $primaryMiSubnetName `
                           -VirtualNetwork $primaryVirtualNetwork
   $primaryMiSubnetConfig
   
   # Configure the network security group management service
   Write-host "Configuring primary MI subnet..."
   
   $primaryMiSubnetConfigId = $primaryMiSubnetConfig.Id
   
   $primaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'primaryNSGMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryNSGMiManagementService
   
   # Configure the route table management service
   Write-host "Configuring primary MI route table management service..."
   
   $primaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'primaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $location
   $primaryRouteTableMiManagementService
   
   # Configure the primary network security group
   Write-host "Configuring primary network security group..."
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $primaryVirtualNetwork `
                         -Name $primaryMiSubnetName `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NetworkSecurityGroup $primaryNSGMiManagementService `
                         -RouteTable $primaryRouteTableMiManagementService | `
                       Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryNSGMiManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $PrimaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $PrimaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   Write-host "Primary network security group configured successfully."
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "primaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "primaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $PrimaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Primary network route table configured successfully."
   
   
   # Create the primary managed instance
   
   Write-host "Creating primary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $primaryInstance `
                         -ResourceGroupName $resourceGroupName `
                         -Location $location `
                         -SubnetId $primaryMiSubnetConfigId `
                         -AdministratorCredential $mycreds `
                         -StorageSizeInGB $maxStorage `
                         -VCore $vCores `
                         -Edition $edition `
                         -ComputeGeneration $computeGeneration `
                         -LicenseType $license
   Write-host "Primary SQL Managed Instance created successfully."
   ```

In dit gedeelte van de zelfstudie wordt gebruikgemaakt van de volgende PowerShell-cmdlets:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een Azure-resourcegroep.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u een virtueel netwerk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie aan een virtueel netwerk toe. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hiermee haalt u een subnet in een virtueel netwerk op. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Hiermee maakt u een netwerkbeveiligingsgroep. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Hiermee maakt u een routetabel. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie aan een virtueel netwerk toe.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Hiermee werkt u een virtueel netwerk bij.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hiermee haalt u een netwerkbeveiligingsgroep op. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hiermee voegt u de configuratie van een netwerkbeveiligingsregel toe aan een netwerkbeveiligingsgroep. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Hiermee werkt u een netwerkbeveiligingsgroep bij.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Hiermee voegt u een route toe aan een routetabel. |
| [Get-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Hiermee werkt u een routetabel bij.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Hiermee maakt u een beheerd exemplaar.  |

---

## <a name="create-secondary-virtual-network"></a>Secundair virtueel netwerk maken

Als u de Azure-portal gebruikt om het beheerde exemplaar te maken, moet u het virtuele netwerk afzonderlijk maken, omdat is vereist dat de subnetten van het primaire en secundaire beheerde exemplaar geen overlappende bereiken hebben. Als u PowerShell gebruikt om het beheerde exemplaar te configureren, gaat u verder met stap 3. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Voer de volgende stappen uit om het subnetbereik van het primaire virtuele netwerk te controleren:

1. Ga in de [Azure-portal](https://portal.azure.com) naar uw resourcegroep en selecteer het virtuele netwerk voor het primaire exemplaar.  
2. Selecteer **Subnetten** onder **Instellingen**, en noteer het **Adresbereik**. Het adresbereik van het subnet van het virtuele netwerk voor het secundaire beheerde exemplaar mag dit bereik niet overlappen. 


   ![Primair subnet](./media/failover-group-add-instance-tutorial/verify-primary-subnet-range.png)

Volg deze stappen om een virtueel netwerk te maken:

1. Selecteer in de [Azure-portal](https://portal.azure.com) de optie **Een resource maken** en zoek naar *virtueel netwerk*. 
1. Selecteer de optie **Virtual Network** die is gepubliceerd door Microsoft, en selecteer vervolgens **Maken** op de volgende pagina. 
1. Vul de vereiste velden in om het virtuele netwerk voor uw beheerde exemplaar te configureren, en selecteer vervolgens **Maken**. 

   In de volgende tabel ziet u de waarden die nodig zijn voor het secundaire virtuele netwerk:

    | **Veld** | Waarde |
    | --- | --- |
    | **Naam** |  De naam van het virtuele netwerk dat moet worden gebruikt voor het secundaire beheerde exemplaar, zoals `vnet-sql-mi-secondary`. |
    | **Adresruimte** | De adresruimte voor het virtuele netwerk, zoals `10.128.0.0/16`. | 
    | **Abonnement** | Het abonnement waarin uw primaire beheerde exemplaar en resourcegroep zich bevinden. |
    | **Regio** | De locatie waar u het secundaire beheerde exemplaar gaat implementeren. |
    | **Subnet** | Een naam voor het subnet. Standaard is de naam `default` opgegeven. |
    | **Adresbereik**| Het adresbereik voor het subnet. Dit moet verschillen van het adresbereik van het subnet dat wordt gebruikt voor het virtuele netwerk van het primaire beheerde exemplaar, zoals `10.128.0.0/24`.  |
    | &nbsp; | &nbsp; |

    ![Waarden voor secundair virtueel netwerk](./media/failover-group-add-instance-tutorial/secondary-virtual-network.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Deze stap is alleen noodzakelijk als u de Azure-portal gebruikt om SQL Managed Instance te implementeren. Ga verder met stap 3 als u PowerShell gebruikt. 

---

## <a name="create-a-secondary-managed-instance"></a>Een secundair beheerd exemplaar maken
In deze stap maakt u een secundair beheerd exemplaar in de Azure-portal, waarmee ook de netwerken tussen de twee beheerde exemplaren worden geconfigureerd. 

Uw secundaire beheerde exemplaar moet:
- Leeg zijn. 
- Een ander subnet en IP-bereik hebben dan het primaire beheerde exemplaar. 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Het tweede secundaire beheerde exemplaar maken met behulp van de Azure-portal. 

1. Selecteer **Azure SQL** in het menu aan de linkerzijde van de Azure Portal. Als **Azure SQL** niet voorkomt in de lijst, selecteert u **Alle services** en typt u `Azure SQL` in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze favoriet te maken en toe te voegen als item in de linkernavigatiebalk. 
1. Selecteer **+ Toevoegen** om de pagina **SQL-implementatieoptie selecteren** te openen. U kunt meer informatie over de verschillende databases bekijken door **Details weergeven** te selecteren op de tegel **Databases**.
1. Selecteer **Maken** op de tegel **Met SQL beheerde exemplaren**. 

    ![SQL Managed Instance selecteren](./media/failover-group-add-instance-tutorial/select-managed-instance.png)

1. Vul op het tabblad **Basisbeginselen** van de pagina **Met Azure SQL beheerd exemplaar maken** de vereiste velden in om uw secundaire beheerde exemplaar te configureren. 

   In de volgende tabel ziet u de waarden die nodig zijn voor het secundaire beheerde exemplaar:
 
    | **Veld** | Waarde |
    | --- | --- |
    | **Abonnement** |  Het abonnement waarin uw primaire beheerde exemplaar zich bevindt. |
    | **Resourcegroep**| De resourcegroep waarin uw primaire beheerde exemplaar zich bevindt. |
    | **Naam van met SQL beheerd exemplaar** | De naam van het nieuwe secundaire beheerde exemplaar, zoals `sql-mi-secondary`.  | 
    | **Regio**| De locatie voor uw secundaire beheerde exemplaar.  |
    | **Aanmeldingsgegevens voor een beheerder voor het met SQL beheerde exemplaar** | De aanmeldingsgegevens die u wilt gebruiken voor uw nieuwe, secundaire beheerde exemplaar, zoals `azureuser`. |
    | **Wachtwoord** | Een complex wachtwoord dat wordt gebruikt voor de beheerdersaanmelding bij het nieuwe secundaire beheerde exemplaar.  |
    | &nbsp; | &nbsp; |

1. Selecteer in de vervolgkeuzelijst op het tabblad **Netwerken** voor **Virtueel netwerk** het virtuele netwerk dat u hebt gemaakt voor het secundaire beheerde exemplaar.

   ![Secundaire MI-netwerken](./media/failover-group-add-instance-tutorial/networking-settings-for-secondary-mi.png)

1. Kies op het tabblad **Aanvullende instellingen** bij **Geo-replicatie** **Ja** om te _Gebruiken als secundaire failover_. Selecteer in de vervolgkeuzelijst het primaire beheerde exemplaar. 
    
   Zorg ervoor dat de sortering en tijdzone overeenkomen met die van het primaire beheerde exemplaar. Het primaire beheerde exemplaar dat u in deze zelfstudie hebt gemaakt, gebruikt de standaardwaarde `SQL_Latin1_General_CP1_CI_AS` voor collatie en `(UTC) Coordinated Universal Time` voor tijdzone. 

   ![Netwerken voor secundair beheerd exemplaar](./media/failover-group-add-instance-tutorial/secondary-mi-failover.png)

1. Selecteer **Controleren + maken** om de instellingen voor het secundaire beheerde exemplaar te controleren. 
1. Selecteer **Maken** om het secundaire beheerde exemplaar te maken. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Het tweede secundaire beheerde exemplaar maken met behulp van PowerShell. 

   ```powershell-interactive
   # Configure the secondary virtual network
   Write-host "Configuring secondary virtual network..."
   
   $SecondaryVirtualNetwork = New-AzVirtualNetwork `
                         -ResourceGroupName $resourceGroupName `
                         -Location $drlocation `
                         -Name $secondaryVNet `
                         -AddressPrefix $secondaryAddressPrefix
   
   Add-AzVirtualNetworkSubnetConfig `
                         -Name $secondaryMiSubnetName `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -AddressPrefix $secondaryMiSubnetAddress `
                       | Set-AzVirtualNetwork
   $SecondaryVirtualNetwork
   
   # Configure the secondary managed instance subnet
   Write-host "Configuring secondary MI subnet..."
   
   $SecondaryVirtualNetwork = Get-AzVirtualNetwork -Name $secondaryVNet `
                                   -ResourceGroupName $resourceGroupName
   
   $secondaryMiSubnetConfig = Get-AzVirtualNetworkSubnetConfig `
                           -Name $secondaryMiSubnetName `
                           -VirtualNetwork $SecondaryVirtualNetwork
   $secondaryMiSubnetConfig
   
   # Configure the secondary network security group management service
   Write-host "Configuring secondary network security group management service..."
   
   $secondaryMiSubnetConfigId = $secondaryMiSubnetConfig.Id
   
   $secondaryNSGMiManagementService = New-AzNetworkSecurityGroup `
                         -Name 'secondaryToMIManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryNSGMiManagementService
   
   # Configure the secondary route table MI management service
   Write-host "Configuring secondary route table MI management service..."
   
   $secondaryRouteTableMiManagementService = New-AzRouteTable `
                         -Name 'secondaryRouteTableMiManagementService' `
                         -ResourceGroupName $resourceGroupName `
                         -location $drlocation
   $secondaryRouteTableMiManagementService
   
   # Configure the secondary network security group
   Write-host "Configuring secondary network security group..."
   
   Set-AzVirtualNetworkSubnetConfig `
                         -VirtualNetwork $SecondaryVirtualNetwork `
                         -Name $secondaryMiSubnetName `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NetworkSecurityGroup $secondaryNSGMiManagementService `
                         -RouteTable $secondaryRouteTableMiManagementService `
                       | Set-AzVirtualNetwork
   
   Get-AzNetworkSecurityGroup `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryToMIManagementService" `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 9000,9003,1438,1440,1452 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix $secondaryMiSubnetAddress `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 300 `
                         -Name "allow_health_probe_inbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix AzureLoadBalancer `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1000 `
                         -Name "allow_tds_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 1433 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_inbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_inbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Inbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 100 `
                         -Name "allow_management_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange 80,443,12000 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 200 `
                         -Name "allow_misubnet_outbound" `
                         -Access Allow `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix $secondaryMiSubnetAddress `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1100 `
                         -Name "allow_redirect_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 11000-11999 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 1200 `
                         -Name "allow_geodr_outbound" `
                         -Access Allow `
                         -Protocol Tcp `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix VirtualNetwork `
                         -DestinationPortRange 5022 `
                         -DestinationAddressPrefix * `
                       | Add-AzNetworkSecurityRuleConfig `
                         -Priority 4096 `
                         -Name "deny_all_outbound" `
                         -Access Deny `
                         -Protocol * `
                         -Direction Outbound `
                         -SourcePortRange * `
                         -SourceAddressPrefix * `
                         -DestinationPortRange * `
                         -DestinationAddressPrefix * `
                       | Set-AzNetworkSecurityGroup
   
   
   Get-AzRouteTable `
                         -ResourceGroupName $resourceGroupName `
                         -Name "secondaryRouteTableMiManagementService" `
                       | Add-AzRouteConfig `
                         -Name "secondaryToMIManagementService" `
                         -AddressPrefix 0.0.0.0/0 `
                         -NextHopType Internet `
                       | Add-AzRouteConfig `
                         -Name "ToLocalClusterNode" `
                         -AddressPrefix $secondaryMiSubnetAddress `
                         -NextHopType VnetLocal `
                       | Set-AzRouteTable
   Write-host "Secondary network security group configured successfully."
   
   # Create the secondary managed instance
   
   $primaryManagedInstanceId = Get-AzSqlInstance -Name $primaryInstance -ResourceGroupName $resourceGroupName | Select-Object Id
   
   
   Write-host "Creating secondary SQL Managed Instance..."
   Write-host "This will take some time, see https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations or more information."
   New-AzSqlInstance -Name $secondaryInstance `
                     -ResourceGroupName $resourceGroupName `
                     -Location $drLocation `
                     -SubnetId $secondaryMiSubnetConfigId `
                     -AdministratorCredential $mycreds `
                     -StorageSizeInGB $maxStorage `
                     -VCore $vCores `
                     -Edition $edition `
                     -ComputeGeneration $computeGeneration `
                     -LicenseType $license `
                     -DnsZonePartner $primaryManagedInstanceId.Id
   Write-host "Secondary SQL Managed Instance created successfully."
   ```

In dit gedeelte van de zelfstudie wordt gebruikgemaakt van de volgende PowerShell-cmdlets:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een Azure-resourcegroep.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u een virtueel netwerk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie aan een virtueel netwerk toe. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hiermee haalt u een subnet in een virtueel netwerk op. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Hiermee maakt u een netwerkbeveiligingsgroep. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Hiermee maakt u een routetabel. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie aan een virtueel netwerk toe.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Hiermee werkt u een virtueel netwerk bij.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hiermee haalt u een netwerkbeveiligingsgroep op. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hiermee voegt u de configuratie van een netwerkbeveiligingsregel toe aan een netwerkbeveiligingsgroep. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Hiermee werkt u een netwerkbeveiligingsgroep bij.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Hiermee voegt u een route toe aan een routetabel. |
| [Get-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Hiermee werkt u een routetabel bij.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Hiermee maakt u een beheerd exemplaar.  |

---

## <a name="create-a-primary-gateway"></a>Een primaire gateway maken 

Twee beheerde exemplaren kunnen alleen deel uitmaken van een failovergroep als ExpressRoute of een gateway tussen de virtuele netwerken van de twee beheerde exemplaren is geconfigureerd, om netwerkcommunicatie toe te staan. Als u ervoor kiest om [ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md) te configureren in plaats van twee VPN-gateways te verbinden, gaat u verder met [Stap 7](#create-a-failover-group).  

In dit artikel worden de stappen beschreven voor het maken van de twee VPN-gateways en het verbinden ervan, maar u kunt doorgaan met het maken van de failovergroep als u in plaats hiervan ExpressRoute hebt geconfigureerd. 

> [!NOTE]
> De SKU van de gateway beïnvloedt de doorvoerprestaties. In deze zelfstudie wordt een gateway geïmplementeerd met de meest eenvoudige SKU (`HwGw1`). Implementeer een hogere SKU (voorbeeld: `VpnGw3`) om een hogere doorvoer te krijgen. Zie [Gateway-SKU's](../../vpn-gateway/vpn-gateway-about-vpngateways.md#benchmark) voor alle beschikbare opties

# <a name="portal"></a>[Portal](#tab/azure-portal)

De gateway voor het virtuele netwerk van het primaire beheerde exemplaar maken met behulp van de Azure-portal. 


1. Ga in de [Azure-portal](https://portal.azure.com) naar uw resourcegroep en selecteer de resource **Virtueel netwerk** voor uw primaire beheerde exemplaar. 
1. Selecteer **Subnetten** onder **Instellingen**, en selecteer vervolgens om een nieuw **Gatewaysubnet** te maken. Laat de standaardwaarden staan. 

   ![Gateway toevoegen voor primair beheerd exemplaar](./media/failover-group-add-instance-tutorial/add-subnet-gateway-primary-vnet.png)

1. Nadat de subnetgateway is gemaakt, selecteert u **Een resource maken** in het linkernavigatievenster, en typt u vervolgens `Virtual network gateway` in het zoekvak. Selecteer de resource **Virtuele netwerkgateway** die is gepubliceerd door **Microsoft**. 

   ![Een virtuele netwerkgateway maken](./media/failover-group-add-instance-tutorial/create-virtual-network-gateway.png)

1. Vul de vereiste velden in om de gateway voor uw primaire beheerde exemplaar te configureren. 

   In de volgende tabel ziet u de waarden die nodig zijn voor de gateway voor het primaire beheerde exemplaar:
 
    | **Veld** | Waarde |
    | --- | --- |
    | **Abonnement** |  Het abonnement waarin uw primaire beheerde exemplaar zich bevindt. |
    | **Naam** | De naam voor de virtuele netwerkgateway, zoals `primary-mi-gateway`. | 
    | **Regio** | De regio waarin uw primaire beheerde exemplaar zich bevindt. |
    | **Gatewaytype** | Selecteer **VPN**. |
    | **VPN-type** | Selecteer **Op route gebaseerd**. |
    | **SKU**| Laat de standaardinstelling `VpnGw1` staan. |
    | **Virtueel netwerk**| Selecteer het virtuele netwerk dat is gemaakt in sectie 2, zoals `vnet-sql-mi-primary`. |
    | **Openbaar IP-adres**| Selecteer **Nieuw maken**. |
    | **Naam openbaar IP-adres**| Voer een naam in voor het IP-adres, zoals `primary-gateway-IP`. |
    | &nbsp; | &nbsp; |

1. Laat verder alle standaardwaarden staan, en selecteer **Controleren + maken** om de instellingen voor de virtuele netwerkgateway te controleren.

   ![Instellingen voor de primaire gateway](./media/failover-group-add-instance-tutorial/settings-for-primary-gateway.png)

1. Selecteer **Maken** om de nieuwe virtuele netwerkgateway te maken. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

De gateway voor het virtuele netwerk van het primaire beheerde exemplaar maken met behulp van PowerShell. 

   ```powershell-interactive
   # Create the primary gateway
   Write-host "Adding GatewaySubnet to primary VNet..."
   Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $primaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $primaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $primaryVNet `
                     -ResourceGroupName $resourceGroupName
   $primaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $primaryVirtualNetwork
   
   Write-host "Creating primary gateway..."
   Write-host "This will take some time."
   $primaryGWPublicIP = New-AzPublicIpAddress -Name $primaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $location -AllocationMethod Dynamic
   $primaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $primaryGWIPConfig `
            -Subnet $primaryGatewaySubnet -PublicIpAddress $primaryGWPublicIP
   
   $primaryGateway = New-AzVirtualNetworkGateway -Name $primaryGWName -ResourceGroupName $resourceGroupName `
       -Location $location -IpConfigurations $primaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $primaryGWAsn
   $primaryGateway
   ```

In dit gedeelte van de zelfstudie wordt gebruikgemaakt van de volgende PowerShell-cmdlets:

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie aan een virtueel netwerk toe. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Hiermee werkt u een virtueel netwerk bij.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hiermee haalt u een subnet in een virtueel netwerk op. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Hiermee maakt u een IP-configuratie voor een virtuele netwerkgateway. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Hiermee maakt u een virtuele netwerkgateway. |


---


## <a name="create-secondary-gateway"></a>Secundaire gateway maken 
In deze stap maakt u de gateway voor het virtuele netwerk van het secundaire beheerde exemplaar met behulp van de Azure-portal. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

Herhaal met behulp van de Azure-portal de stappen uit de vorige sectie om het subnet en de gateway voor het virtuele netwerk te maken voor het secundaire beheerde exemplaar. Vul de vereiste velden in om de gateway voor uw secundaire beheerde exemplaar te configureren. 

   In de volgende tabel ziet u de waarden die nodig zijn voor de gateway voor het secundaire beheerde exemplaar:

   | **Veld** | Waarde |
   | --- | --- |
   | **Abonnement** |  Het abonnement waarin uw secundaire beheerde exemplaar zich bevindt. |
   | **Naam** | De naam voor de virtuele netwerkgateway, zoals `secondary-mi-gateway`. | 
   | **Regio** | De regio waarin uw secundaire beheerde exemplaar zich bevindt. |
   | **Gatewaytype** | Selecteer **VPN**. |
   | **VPN-type** | Selecteer **Op route gebaseerd**. |
   | **SKU**| Laat de standaardinstelling `VpnGw1` staan. |
   | **Virtueel netwerk**| Selecteer het virtuele netwerk voor het secundaire beheerde exemplaar, zoals `vnet-sql-mi-secondary`. |
   | **Openbaar IP-adres**| Selecteer **Nieuw maken**. |
   | **Naam openbaar IP-adres**| Voer een naam in voor het IP-adres, zoals `secondary-gateway-IP`. |
   | &nbsp; | &nbsp; |

   ![Instellingen voor de secundaire gateway](./media/failover-group-add-instance-tutorial/settings-for-secondary-gateway.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

De gateway voor het virtuele netwerk van het secundaire beheerde exemplaar maken met behulp van PowerShell. 

   ```powershell-interactive
   # Create the secondary gateway
   Write-host "Creating secondary gateway..."
   
   Write-host "Adding GatewaySubnet to secondary VNet..."
   Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName `
                   | Add-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -AddressPrefix $secondaryMiGwSubnetAddress `
                   | Set-AzVirtualNetwork
   
   $secondaryVirtualNetwork  = Get-AzVirtualNetwork `
                     -Name $secondaryVNet `
                     -ResourceGroupName $resourceGroupName
   $secondaryGatewaySubnet = Get-AzVirtualNetworkSubnetConfig `
                     -Name "GatewaySubnet" `
                     -VirtualNetwork $secondaryVirtualNetwork
   $drLocation = $secondaryVirtualNetwork.Location
   
   Write-host "Creating secondary gateway..."
   Write-host "This will take some time."
   $secondaryGWPublicIP = New-AzPublicIpAddress -Name $secondaryGWPublicIPAddress -ResourceGroupName $resourceGroupName `
            -Location $drLocation -AllocationMethod Dynamic
   $secondaryGatewayIPConfig = New-AzVirtualNetworkGatewayIpConfig -Name $secondaryGWIPConfig `
            -Subnet $secondaryGatewaySubnet -PublicIpAddress $secondaryGWPublicIP
   
   $secondaryGateway = New-AzVirtualNetworkGateway -Name $secondaryGWName -ResourceGroupName $resourceGroupName `
       -Location $drLocation -IpConfigurations $secondaryGatewayIPConfig -GatewayType Vpn `
       -VpnType RouteBased -GatewaySku VpnGw1 -EnableBgp $true -Asn $secondaryGWAsn
   $secondaryGateway
   ```

In dit gedeelte van de zelfstudie wordt gebruikgemaakt van de volgende PowerShell-cmdlets:

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie aan een virtueel netwerk toe. | 
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Hiermee werkt u een virtueel netwerk bij.  |
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hiermee haalt u een subnet in een virtueel netwerk op. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Hiermee maakt u een IP-configuratie voor een virtuele netwerkgateway. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Hiermee maakt u een virtuele netwerkgateway. |

---


## <a name="connect-the-gateways"></a>De gateways verbinden
In deze stap maakt u een verbinding in twee richtingen tussen de beide gateways van de twee virtuele netwerken. 


# <a name="portal"></a>[Portal](#tab/azure-portal)

De twee gateways verbinden met behulp van de Azure-portal. 


1. Selecteer **Een resource maken** in de [Azure-portal](https://portal.azure.com).
1. Typ `connection` in het zoekvak, en druk vervolgens op Enter om te zoeken. Dit brengt u naar de resource **Verbinding**, gepubliceerd door Microsoft.
1. Selecteer **Maken** om de verbinding te maken. 
1. Selecteer op de pagina **Basisbeginselen** de volgende waarden, en selecteer vervolgens **OK**. 
    1. Selecteer `VNet-to-VNet` voor **Verbindingstype**. 
    1. Selecteer uw abonnement in de vervolgkeuzelijst. 
    1. Selecteer de resourcegroep voor SQL Managed Instance in de vervolgkeuzelijst. 
    1. Selecteer de locatie van uw primaire beheerde instantie in de vervolgkeuzelijst. 
1. Selecteer op de pagina **Instellingen** de volgende waarden, of voer ze in, en selecteer vervolgens **OK**:
    1. Kies de primaire netwerkgateway voor de **Eerste virtuele netwerkgateway**, zoals `primaryGateway`.  
    1. Kies de secundaire netwerkgateway voor de **Tweede virtuele netwerkgateway**, zoals `secondaryGateway`. 
    1. Schakel het selectievakje in naast **Connectiviteit in twee richtingen tot stand brengen**. 
    1. Laat de standaardnaam voor de primaire verbinding staan, of kies een nieuwe naam naar keuze. 
    1. Geef een **Gedeelde sleutel (PSK)** op voor de verbinding, zoals `mi1m2psk`. 
    1. Selecteer **OK** om uw wijzigingen op te slaan. 

    ![Gatewayverbinding maken](./media/failover-group-add-instance-tutorial/create-gateway-connection.png)

    

1. Controleer op de pagina **Controleren + maken** de instellingen voor de verbinding in twee richtingen, en selecteer vervolgens **OK** om de verbinding te maken. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

De twee gateways verbinden met behulp van PowerShell. 

   ```powershell-interactive
   # Connect the primary to secondary gateway
   Write-host "Connecting the primary gateway to secondary gateway..."
   New-AzVirtualNetworkGatewayConnection -Name $primaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $primaryGateway -VirtualNetworkGateway2 $secondaryGateway -Location $location `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $primaryGWConnection
   
   # Connect the secondary to primary gateway
   Write-host "Connecting the secondary gateway to primary gateway..."
   
   New-AzVirtualNetworkGatewayConnection -Name $secondaryGWConnection -ResourceGroupName $resourceGroupName `
       -VirtualNetworkGateway1 $secondaryGateway -VirtualNetworkGateway2 $primaryGateway -Location $drLocation `
       -ConnectionType Vnet2Vnet -SharedKey $vpnSharedKey -EnableBgp $true
   $secondaryGWConnection
   ```

In dit gedeelte van de zelfstudie wordt gebruikgemaakt van de volgende PowerShell-cmdlet:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Hiermee maakt u een verbinding tussen de twee virtuele netwerkgateways.   |

---


## <a name="create-a-failover-group"></a>Een failovergroep maken
In deze stap maakt u de failovergroep, en voegt u beide beheerde exemplaren toe aan deze groep. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
De failovergroep maken met behulp van de Azure-portal. 


1. Selecteer **Azure SQL** in het menu aan de linkerzijde van de [Azure-portal](https://portal.azure.com). Als **Azure SQL** niet voorkomt in de lijst, selecteert u **Alle services** en typt u `Azure SQL` in het zoekvak. (Optioneel) Selecteer de ster naast **Azure SQL** om deze favoriet te maken en toe te voegen als item in de linkernavigatiebalk. 
1. Selecteer het primaire beheerde exemplaar dat u in de eerste sectie hebt gemaakt, zoals `sql-mi-primary`. 
1. Ga onder **Instellingen** naar **Exemplaarfailovergroepen**, en kies vervolgens **Groep toevoegen** om de pagina **Exemplaarfailovergroep** te openen. 

   ![Een failovergroep toevoegen](./media/failover-group-add-instance-tutorial/add-failover-group.png)

1. Typ op de pagina **Exemplaarfailovergroep** de naam van de failovergroep, zoals `failovergrouptutorial`. Kies vervolgens in de vervolgkeuzelijst het secundaire beheerde exemplaar, zoals `sql-mi-secondary`. Selecteer **Maken** om de failovergroep te maken. 

   ![Failovergroep maken](./media/failover-group-add-instance-tutorial/create-failover-group.png)

1. Zodra de implementatie van de failovergroep is voltooid, wordt u teruggeleid naar de pagina **Failovergroep**. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
De failovergroep maken met behulp van PowerShell. 

   ```powershell-interactive
   Write-host "Creating the failover group..."
   $failoverGroup = New-AzSqlDatabaseInstanceFailoverGroup -Name $failoverGroupName `
        -Location $location -ResourceGroupName $resourceGroupName -PrimaryManagedInstanceName $primaryInstance `
        -PartnerRegion $drLocation -PartnerManagedInstanceName $secondaryInstance `
        -FailoverPolicy Automatic -GracePeriodWithDataLossHours 1
   $failoverGroup
   ```

In dit gedeelte van de zelfstudie wordt gebruikgemaakt van de volgende PowerShell-cmdlet:

| Opdracht | Opmerkingen |
|---|---|
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Hiermee maakt u een nieuwe failovergroep voor Azure SQL Managed Instance.  |


---


## <a name="test-failover"></a>Testfailover
In deze stap voert u een failover van uw failovergroep naar de secundaire server uit, en voert u vervolgens een failback uit met behulp van de Azure-portal. 


# <a name="portal"></a>[Portal](#tab/azure-portal)
Failover testen met behulp van de Azure-portal. 


1. Ga naar uw _secundaire_ beheerde exemplaar in de [Azure-portal](https://portal.azure.com), en selecteer onder Instellingen de optie **Exemplaarfailovergroepen**. 
1. Controleer welk beheerde exemplaar het primaire is en welk beheerde exemplaar het secundaire is. 
1. Selecteer **Failover** en selecteer vervolgens **Ja** bij de waarschuwing over het verbreken van de verbinding met TDS-sessies. 

   ![Failover uitvoeren voor de failovergroep](./media/failover-group-add-instance-tutorial/failover-mi-failover-group.png)

1. Controleer welk beheerde exemplaar het primaire is en welk beheerde exemplaar het secundaire is. Als de failover is geslaagd, zijn de twee exemplaren van rol gewisseld. 

   ![Beheerde exemplaren zijn na de failover van rol gewisseld](./media/failover-group-add-instance-tutorial/mi-switched-after-failover.png)

1. Ga naar het nieuwe _secundaire_ beheerde exemplaar, en selecteer **Failover** opnieuw om het primaire exemplaar weer terug te zetten naar de primaire rol. 


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Failover testen met behulp van PowerShell. 

   ```powershell-interactive
    
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   
   # Fail over the primary managed instance to the secondary role
   Write-host "Failing primary over to the secondary location"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to secondary location"
   ```


Herstel de failovergroep weer naar de primaire server:

   ```powershell-interactive
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $drLocation -Name $failoverGroupName
   
   # Fail the primary managed instance back to the primary role
   Write-host "Failing primary back to primary role"
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName | Switch-AzSqlDatabaseInstanceFailoverGroup
   Write-host "Successfully failed failover group to primary location"
   
   # Verify the current primary role
   Get-AzSqlDatabaseInstanceFailoverGroup -ResourceGroupName $resourceGroupName `
       -Location $location -Name $failoverGroupName
   ```

In dit gedeelte van de zelfstudie wordt gebruikgemaakt van de volgende PowerShell-cmdlets:

| Opdracht | Opmerkingen |
|---|---|
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Hiermee haalt u failovergroepen voor Azure SQL Managed Instance op of vermeldt u deze.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Hiermee voert u een nieuwe failover uit voor een failovergroep voor SQL Managed Instance. | 

---



## <a name="clean-up-resources"></a>Resources opschonen
U kunt resources opschonen door eerst de beheerde exemplaren te verwijderen, vervolgens het virtuele cluster, daarna eventuele resterende resources, en ten slotte de resourcegroep. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Navigeer in [Azure Portal](https://portal.azure.com) naar uw resourcegroep. 
1. Selecteer het beheerde exemplaar of de beheerde exemplaren en selecteer vervolgens **Verwijderen**. Typ `yes` in het tekstvak om te bevestigen dat u de resource wilt verwijderen en selecteer vervolgens **Verwijderen**. Het kan enige tijd duren voordat dit proces op de achtergrond is voltooid. Zolang het nog niet is voltooid, kunt u het *virtuele cluster* of andere afhankelijke resources niet verwijderen. Controleer de verwijdering op het tabblad **Activiteit** om na te gaan of uw beheerde exemplaar is verwijderd. 
1. Wanneer het beheerde exemplaar is verwijderd, verwijdert u het *virtuele cluster* door dit te selecteren in uw resourcegroep en vervolgens **Verwijderen** te kiezen. Typ `yes` in het tekstvak om te bevestigen dat u de resource wilt verwijderen en selecteer vervolgens **Verwijderen**. 
1. Verwijder eventuele andere resources. Typ `yes` in het tekstvak om te bevestigen dat u de resource wilt verwijderen en selecteer vervolgens **Verwijderen**. 
1. Verwijder de resourcegroep door **Resourcegroep verwijderen** te selecteren, de naam van de resourcegroep, `myResourceGroup`, in te voeren en vervolgens **Verwijderen** te selecteren. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

U moet de resourcegroep twee keer verwijderen. Als u de resourcegroep de eerste keer verwijdert, worden de beheerde exemplaren en de virtuele clusters verwijderd, maar daarna wordt het foutbericht `Remove-AzResourceGroup : Long running operation failed with status 'Conflict'` weergegeven. Voer de opdracht Remove-AzResourceGroup een tweede keer uit om eventuele resterende resources en de resourcegroep te verwijderen.

```powershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing SQL Managed Instance and virtual cluster..."
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
Write-host "Removing residual resources and resource group..."
```

In dit gedeelte van de zelfstudie wordt gebruikgemaakt van de volgende PowerShell-cmdlet:

| Opdracht | Opmerkingen |
|---|---|
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep. |

---

## <a name="full-script"></a>Volledige script

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[!code-powershell-interactive[main](../../../powershell_scripts/sql-database/failover-groups/add-managed-instance-to-failover-group-az-ps.ps1 "Add SQL Managed Instance to a failover group")]

In dit script worden de volgende opdrachten gebruikt. Elke opdracht in de tabel is een koppeling naar opdracht-specifieke documentatie.

| Opdracht | Opmerkingen |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Hiermee maakt u een Azure-resourcegroep.  |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Hiermee maakt u een virtueel netwerk.  |
| [Add-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/add-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie aan een virtueel netwerk toe. | 
| [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork) | Hiermee haalt u een virtueel netwerk op in een resourcegroep. | 
| [Get-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/get-azvirtualnetworksubnetconfig) | Hiermee haalt u een subnet in een virtueel netwerk op. | 
| [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) | Hiermee maakt u een netwerkbeveiligingsgroep. | 
| [New-AzRouteTable](/powershell/module/az.network/new-azroutetable) | Hiermee maakt u een routetabel. |
| [Set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) | Hiermee voegt u een subnetconfiguratie aan een virtueel netwerk toe.  |
| [Set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) | Hiermee werkt u een virtueel netwerk bij.  |
| [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup) | Hiermee haalt u een netwerkbeveiligingsgroep op. |
| [Add-AzNetworkSecurityRuleConfig](/powershell/module/az.network/add-aznetworksecurityruleconfig)| Hiermee voegt u de configuratie van een netwerkbeveiligingsregel toe aan een netwerkbeveiligingsgroep. |
| [Set-AzNetworkSecurityGroup](/powershell/module/az.network/set-aznetworksecuritygroup) | Hiermee werkt u een netwerkbeveiligingsgroep bij.  | 
| [Add-AzRouteConfig](/powershell/module/az.network/add-azrouteconfig) | Hiermee voegt u een route toe aan een routetabel. |
| [Get-AzRouteTable](/powershell/module/az.network/set-azroutetable) | Hiermee werkt u een routetabel bij.  |
| [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance) | Hiermee maakt u een beheerd exemplaar.  |
| [Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)| Hiermee retourneert u informatie over een met Azure SQL beheerd exemplaar. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Hiermee maakt u een openbaar IP-adres.  | 
| [New-AzVirtualNetworkGatewayIpConfig](/powershell/module/az.network/new-azvirtualnetworkgatewayipconfig) | Hiermee maakt u een IP-configuratie voor een virtuele netwerkgateway. |
| [New-AzVirtualNetworkGateway](/powershell/module/az.network/new-azvirtualnetworkgateway) | Hiermee maakt u een virtuele netwerkgateway. |
| [New-AzVirtualNetworkGatewayConnection](/powershell/module/az.network/new-azvirtualnetworkgatewayconnection) | Hiermee maakt u een verbinding tussen de twee virtuele netwerkgateways.   |
| [New-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/new-azsqldatabaseinstancefailovergroup)| Hiermee maakt u een nieuwe failovergroep voor SQL Managed Instance.  |
| [Get-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/get-azsqldatabaseinstancefailovergroup) | Hiermee haalt u failovergroepen voor Azure SQL Managed Instance op of vermeldt u deze.| 
| [Switch-AzSqlDatabaseInstanceFailoverGroup](/powershell/module/az.sql/switch-azsqldatabaseinstancefailovergroup) | Hiermee voert u een nieuwe failover uit voor een failovergroep voor SQL Managed Instance. | 
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Hiermee verwijdert u een resourcegroep. | 

# <a name="portal"></a>[Portal](#tab/azure-portal) 

Er zijn geen scripts beschikbaar voor de Azure-portal.

---

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een failovergroep geconfigureerd tussen twee beheerde exemplaren. U hebt geleerd hoe u:

> [!div class="checklist"]
> - Een primair beheerd exemplaar maken.
> - Een secundair beheerd exemplaar maken als onderdeel van een [failovergroep](../database/auto-failover-group-overview.md). 
> - Failover testen.

Ga naar de volgende quickstart over het verbinding maken met een met SQL beheerd exemplaar, en het herstellen van een database in SQL Managed Instance: 

> [!div class="nextstepaction"]
> [Verbinding maken met een met SQL beheerd exemplaar](connect-vm-instance-configure.md)
> [Een database herstellen in SQL Managed Instance](restore-sample-database-quickstart.md)


