---
title: 'Quickstart: Rekenkracht schalen voor toegewezen SQL-pool (voorheen SQL DW) (Azure PowerShell)'
description: U kunt rekenkracht schalen voor toegewezen SQL-pool (voorheen SQL DW) met behulp van Azure PowerShell.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: igorstan
ms.custom: seo-lt-2019, devx-track-azurepowershell
ms.openlocfilehash: 87e10740e6081431bad96daa930f61238ca495bd
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96921905"
---
# <a name="quickstart-scale-compute-for-dedicated-sql-pool-formerly-sql-dw-with-azure-powershell"></a>Quickstart: Rekenkracht schalen voor toegewezen SQL-pool (voorheen SQL DW) met Azure PowerShell

U kunt rekenkracht schalen voor toegewezen SQL-pool (voorheen SQL DW) met behulp van Azure PowerShell. [Vergroot de schaal van Compute](sql-data-warehouse-manage-compute-overview.md) voor betere prestaties of verklein de schaal juist om kosten te besparen.

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

In deze quickstart wordt ervan uitgegaan dat u al een toegewezen SQL-pool (voorheen SQL DW) hebt die u kunt opschalen. Gebruik [Maken en koppelen - portal](create-data-warehouse-portal.md) om een toegewezen SQL-pool (voorheen SQL DW) met de naam **mySampleDataWarehouse** te maken als dat nodig is.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

Meld u aan bij uw Azure-abonnement met behulp van de opdracht [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) en volg de instructies op het scherm.

```powershell
Connect-AzAccount
```

Voer [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) uit om te zien welk abonnement u gebruikt.

```powershell
Get-AzSubscription
```

Als u een ander abonnement dan het standaardabonnement wilt gebruiken, voert u [Set-AzContext](/powershell/module/az.accounts/set-azcontext?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) uit.

```powershell
Set-AzContext -SubscriptionName "MySubscription"
```

## <a name="look-up-data-warehouse-information"></a>Datawarehousegegevens opzoeken

Zoek de databasenaam, de servernaam en de resourcegroep op voor het datawarehouse dat u wilt onderbreken en hervatten.

Volg deze stappen om de locatiegegevens voor uw datawarehouse op te zoeken.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Klik op **Azure Synapse Analytics (voorheen SQL DW)** op de linkernavigatiepagina van de Azure Portal.
3. Selecteer **mySampleDataWarehouse** op de pagina **Azure Synapse Analytics (voorheen SQL DW)** om het datawarehouse te openen.

    ![Servernaam en resourcegroep](./media/quickstart-scale-compute-powershell/locate-data-warehouse-information.png)

4. Noteer de naam van het datawarehouse. Deze wordt gebruikt als de databasenaam. Een datawarehouse is een type database. Noteer ook de naam van de server en de resourcegroep. U gebruikt de servernaam en de naam van de resourcegroep in de opdrachten onderbreken en hervatten.
5. Gebruik alleen het eerste deel van de servernaam in de PowerShell-cmdlets. In de voorgaande afbeelding is de volledige servernaam sqlpoolservername.database.windows.net. We gebruiken **sqlpoolservername** als de servernaam in de PowerShell-cmdlet.

## <a name="scale-compute"></a>De schaal van Compute aanpassen

In de toegewezen SQL-pool (voorheen SQL DW) kunt u het aantal rekenresources verhogen of verlagen door de datawarehouse-eenheden aan te passen. Met behulp van [Maken en verbinden - portal](create-data-warehouse-portal.md) is **mySampleDataWarehouse** gemaakt en vervolgens gestart met 400 DWU's. In de volgende stappen wordt het aantal DWU's voor **mySampleDataWarehouse** aangepast.

Als u datawarehouse-eenheden wilt wijzigen, gebruikt u de PowerShell-cmdlet [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). In het volgende voorbeeld worden de datawarehouse-eenheden ingesteld op DW300c voor de database **mySampleDataWarehouse** die wordt gehost in de resourcegroep **resourcegroupname** op server **sqlpoolservername**.

```Powershell
Set-AzSqlDatabase -ResourceGroupName "resourcegroupname" -DatabaseName "mySampleDataWarehouse" -ServerName "sqlpoolservername" -RequestedServiceObjectiveName "DW300c"
```

## <a name="check-data-warehouse-state"></a>Status van datawarehouse controleren

Gebruik de PowerShell-cmdlet [Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) als u de huidige status van de datawarehouse wilt zien. Deze cmdlet geeft de status van de **mySampleDataWarehouse**-database in ResourceGroup **resourcegroupname** en server **sqlpoolservername.database.windows.net** weer.

```powershell
$database = Get-AzSqlDatabase -ResourceGroupName resourcegroupname -ServerName sqlpoolservername -DatabaseName mySampleDataWarehouse
```

Dit resulteert in ongeveer het volgende:

```powershell
ResourceGroupName             : resourcegroupname
ServerName                    : sqlpoolservername
DatabaseName                  : mySampleDataWarehouse
Location                      : North Europe
DatabaseId                    : 34d2ffb8-b70a-40b2-b4f9-b0a39833c974
Edition                       : DataWarehouse
CollationName                 : SQL_Latin1_General_CP1_CI_AS
CatalogCollation              :
MaxSizeBytes                  : 263882790666240
Status                        : Online
CreationDate                  : 11/20/2017 9:18:12 PM
CurrentServiceObjectiveId     : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
CurrentServiceObjectiveName   : DW300c
RequestedServiceObjectiveId   : 284f1aff-fee7-4d3b-a211-5b8ebdd28fea
RequestedServiceObjectiveName :
ElasticPoolName               :
EarliestRestoreDate           :
Tags                          :
ResourceId                    : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/
                                resourceGroups/resourcegroupname/providers/Microsoft.Sql/servers/sqlpoolservername/databases/mySampleDataWarehouse
CreateMode                    :
ReadScale                     : Disabled
ZoneRedundant                 : False
```

U ziet de **Status** van de database in de uitvoer. In dit geval kunt u zien dat deze database online is.  Wanneer u deze opdracht uitvoert, ontvangt u de statuswaarde Online, Onderbreken, Hervatten, Schalen of Onderbroken.

Voer de volgende opdracht uit om de status op zichzelf te bekijken:

```powershell
$database | Select-Object DatabaseName,Status
```

## <a name="next-steps"></a>Volgende stappen

U hebt nu geleerd hoe u de rekenkracht voor een toegewezen SQL-pool (voorheen SQL DW) kunt schalen. Ga verder met de zelfstudie voor het laden van gegevens voor meer informatie over toegewezen SQL-pool (voorheen SQL DW).

> [!div class="nextstepaction"]
>[Gegevens laden in een toegewezen SQL-pool](load-data-from-azure-blob-storage-using-copy.md)
