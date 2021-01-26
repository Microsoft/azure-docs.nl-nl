---
title: Azure Firewall-toepassingsregels met SQL-FQDN's configureren
description: In dit artikel leert u hoe u SQL-FQDN-in Azure Firewall toepassings regels kunt configureren.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: how-to
ms.date: 06/18/2020
ms.author: victorh
ms.openlocfilehash: c65f32cc3ce56ddf3fd235de8c002528e7a3cebd
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791439"
---
# <a name="configure-azure-firewall-application-rules-with-sql-fqdns"></a>Azure Firewall-toepassingsregels met SQL-FQDN's configureren

U kunt nu Azure Firewall toepassings regels configureren met SQL-FQDN-waarden. Hierdoor kunt u de toegang tot de virtuele netwerken beperken tot alleen de opgegeven SQL Server-exemplaren.

Met SQL-FQDN's kunt u verkeer filteren:

- Van uw VNets naar een Azure SQL Database of Azure Synapse Analytics. Bijvoorbeeld: alleen toegang tot *SQL-server1.database.Windows.net* toestaan.
- Van on-premises naar Azure SQL Managed instances of SQL IaaS die worden uitgevoerd in uw VNets.
- Van spoke-naar-spoke tot Azure SQL Managed instances of SQL IaaS die worden uitgevoerd in uw VNets.

SQL FQDN-filtering wordt alleen ondersteund in de [proxy modus](../azure-sql/database/connectivity-architecture.md#connection-policy) (poort 1433). Als u SQL in de standaard omleidings modus gebruikt, kunt u de toegang filteren met behulp van de SQL-service-tag als onderdeel van de [netwerk regels](features.md#network-traffic-filtering-rules).
Als u niet-standaardpoorten gebruikt voor SQL IaaS-verkeer, kunt u die poorten configureren in de toepassingsregels van de firewall.

## <a name="configure-using-azure-cli"></a>Configureren met behulp van Azure CLI

1. Implementeer een [Azure Firewall met behulp van Azure cli](deploy-cli.md).
2. Als u verkeer filtert op Azure SQL Database, Azure Synapse Analytics of SQL Managed instance, zorg ervoor dat de SQL-connectiviteits modus is ingesteld op **proxy**. Zie [Azure SQL-connectiviteits instellingen](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli)voor meer informatie over het overschakelen naar een andere SQL-connectiviteits modus.

   > [!NOTE]
   > De SQL- *proxy* modus kan leiden tot meer latentie vergeleken met *omleiden*. Als u de omleidings modus wilt blijven gebruiken. Dit is de standaard instelling voor clients die verbinding maken met Azure. u kunt de toegang filteren met behulp van de SQL- [service-tag](service-tags.md) in Firewall- [netwerk regels](tutorial-firewall-deploy-portal.md#configure-a-network-rule).

3. Maak een nieuwe regel verzameling met een toepassings regel met behulp van SQL FQDN om toegang tot een SQL-Server toe te staan:

   ```azurecli
    az extension add -n azure-firewall
    
    az network firewall application-rule create \ 
    -g FWRG \
    --f azfirewall \ 
    --c sqlRuleCollection \
    --priority 1000 \
    --action Allow \
    --name sqlRule \
    --protocols mssql=1433 \
    --source-addresses 10.0.0.0/24 \
    --target-fqdns sql-serv1.database.windows.net
   ```

## <a name="configure-using-azure-powershell"></a>Configureren met behulp van Azure PowerShell

1. Implementeer een [Azure Firewall met behulp van Azure PowerShell](deploy-ps.md).
2. Als u verkeer filtert op Azure SQL Database, Azure Synapse Analytics of SQL Managed instance, zorg ervoor dat de SQL-connectiviteits modus is ingesteld op **proxy**. Zie [Azure SQL-connectiviteits instellingen](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli)voor meer informatie over het overschakelen naar een andere SQL-connectiviteits modus.

   > [!NOTE]
   > De SQL- *proxy* modus kan leiden tot meer latentie vergeleken met *omleiden*. Als u de omleidings modus wilt blijven gebruiken. Dit is de standaard instelling voor clients die verbinding maken met Azure. u kunt de toegang filteren met behulp van de SQL- [service-tag](service-tags.md) in Firewall- [netwerk regels](tutorial-firewall-deploy-portal.md#configure-a-network-rule).

3. Maak een nieuwe regel verzameling met een toepassings regel met behulp van SQL FQDN om toegang tot een SQL-Server toe te staan:

   ```azurepowershell
   $AzFw = Get-AzFirewall -Name "azfirewall" -ResourceGroupName "FWRG"
    
   $sqlRule = @{
      Name          = "sqlRule"
      Protocol      = "mssql:1433" 
      TargetFqdn    = "sql-serv1.database.windows.net"
      SourceAddress = "10.0.0.0/24"
   }
    
   $rule = New-AzFirewallApplicationRule @sqlRule
    
   $sqlRuleCollection = @{
      Name       = "sqlRuleCollection" 
      Priority   = 1000 
      Rule       = $rule
      ActionType = "Allow"
   }
    
   $ruleCollection = New-AzFirewallApplicationRuleCollection @sqlRuleCollection
    
   $Azfw.ApplicationRuleCollections.Add($ruleCollection)    
   Set-AzFirewall -AzureFirewall $AzFw    
   ```

## <a name="configure-using-the-azure-portal"></a>Configureren met behulp van Azure Portal
1. Implementeer een [Azure Firewall met behulp van Azure cli](deploy-cli.md).
2. Als u verkeer filtert op Azure SQL Database, Azure Synapse Analytics of SQL Managed instance, zorg ervoor dat de SQL-connectiviteits modus is ingesteld op **proxy**. Zie [Azure SQL-connectiviteits instellingen](../azure-sql/database/connectivity-settings.md#change-the-connection-policy-via-the-azure-cli)voor meer informatie over het overschakelen naar een andere SQL-connectiviteits modus.  

   > [!NOTE]
   > De SQL- *proxy* modus kan leiden tot meer latentie vergeleken met *omleiden*. Als u de omleidings modus wilt blijven gebruiken. Dit is de standaard instelling voor clients die verbinding maken met Azure. u kunt de toegang filteren met behulp van de SQL- [service-tag](service-tags.md) in Firewall- [netwerk regels](tutorial-firewall-deploy-portal.md#configure-a-network-rule).
3. Voeg de toepassings regel met het juiste protocol, de poort en de SQL FQDN toe en selecteer vervolgens **Opslaan**.
   ![toepassings regel met SQL FQDN](media/sql-fqdn-filtering/application-rule-sql.png)
4. Toegang tot SQL vanaf een virtuele machine in een VNet waarmee het verkeer via de firewall wordt gefilterd. 
5. Controleer of [Azure firewall logboeken](./firewall-workbook.md) het verkeer mag weer geven.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure SQL database connectiviteits architectuur](../azure-sql/database/connectivity-architecture.md)voor meer informatie over de SQL-proxy en omleidings modi.