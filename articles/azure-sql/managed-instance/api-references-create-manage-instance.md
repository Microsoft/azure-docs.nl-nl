---
title: Beheer API naslag voor Azure SQL Managed Instance
description: Meer informatie over het maken en configureren van beheerde exemplaren van Azure SQL Managed Instance.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: devx-track-azurecli
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 148b24aea42072f1901c76c7a09a126340ef9951
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784367"
---
# <a name="managed-api-reference-for-azure-sql-managed-instance"></a>Beheerde API-verwijzing voor Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

U kunt beheerde exemplaren van Azure SQL Managed Instance maken en configureren met behulp van Azure Portal, PowerShell, Azure CLI, REST API en Transact-SQL. In dit artikel vindt u een overzicht van de functies en de API die u kunt gebruiken om beheerde exemplaren te maken en te configureren.

## <a name="azure-portal-create-a-managed-instance"></a>Azure Portal: Een beheerd exemplaar maken

Zie [Quickstart: Een](instance-create-quickstart.md)beheerd exemplaar maken voor een quickstart over het maken van een beheerd exemplaar.

## <a name="powershell-create-and-configure-managed-instances"></a>PowerShell: Beheerde exemplaren maken en configureren

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> De module PowerShell Azure Resource Manager wordt nog steeds ondersteund in Azure SQL Database, maar alle toekomstige ontwikkeling is voor de Az.Sql-module. Zie [AzureRM.Sql](/powershell/module/AzureRM.Sql/) voor deze cmdlets. De argumenten voor de opdrachten in de Az-module en in de AzureRM-modules zijn vrijwel identiek.

Als u beheerde exemplaren wilt maken en beheren Azure PowerShell, gebruikt u de volgende PowerShell-cmdlets. Als u PowerShell wilt installeren of upgraden, zie De module [Azure PowerShell installeren.](/powershell/azure/install-az-ps)

> [!TIP]
> Zie Snelstartscript: Een beheerd exemplaar maken met behulp van een [PowerShell-bibliotheek voor PowerShell-voorbeeldscripts.](/archive/blogs/sqlserverstorageengine/quick-start-script-create-azure-sql-managed-instance-using-powershell)

| Cmdlet | Beschrijving |
| --- | --- |
|[New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)|Hiermee maakt u een beheerd exemplaar. |
|[Get-AzSqlInstance](/powershell/module/az.sql/get-azsqlinstance)|Retourneert informatie over een beheerd exemplaar.|
|[Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)|Hiermee stelt u eigenschappen in voor een beheerd exemplaar.|
|[Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance)|Hiermee verwijdert u een beheerd exemplaar.|
|[Get-AzSqlInstanceOperation](/powershell/module/az.sql/get-azsqlinstanceoperation)|Haalt een lijst op met beheerbewerkingen die worden uitgevoerd op het beheerde exemplaar of een specifieke bewerking.|
|[Stop-AzSqlInstanceOperation](/powershell/module/az.sql/stop-azsqlinstanceoperation)|Annuleert de specifieke beheerbewerking die wordt uitgevoerd op het beheerde exemplaar.|
|[New-AzSqlInstanceDatabase](/powershell/module/az.sql/new-azsqlinstancedatabase)|Hiermee maakt u SQL Managed Instance database.|
|[Get-AzSqlInstanceDatabase](/powershell/module/az.sql/get-azsqlinstancedatabase)|Retourneert informatie over een SQL Managed Instance database.|
|[Remove-AzSqlInstanceDatabase](/powershell/module/az.sql/remove-azsqlinstancedatabase)|Hiermee verwijdert u SQL Managed Instance database.|
|[Restore-AzSqlInstanceDatabase](/powershell/module/az.sql/restore-azsqlinstancedatabase)|Herstelt een SQL Managed Instance database.|

## <a name="azure-cli-create-and-configure-managed-instances"></a>Azure CLI: Beheerde exemplaren maken en configureren

Als u beheerde exemplaren wilt maken en configureren [met Azure CLI,](/cli/azure)gebruikt u de volgende [Azure CLI-opdrachten voor SQL Managed Instance](/cli/azure/sql/mi). Gebruik [Azure Cloud Shell](../../cloud-shell/overview.md) om de CLI uit te voeren in uw browser of [installeer](/cli/azure/install-azure-cli) deze in macOS, Linux of Windows.

> [!TIP]
> Zie Working with SQL Managed Instance using Azure CLI (Werken met SQL Managed Instance Azure CLI) voor een [Azure CLI-snelstart.](https://medium.com/azure-sqldb-managed-instance/working-with-sql-managed-instance-using-azure-cli-611795fe0b44)

| Cmdlet | Beschrijving |
| --- | --- |
|[az sql mi create](/cli/azure/sql/mi#az_sql_mi_create) |Hiermee maakt u een beheerd exemplaar.|
|[az sql mi list](/cli/azure/sql/mi#az_sql_mi_list)|Een lijst met beschikbare beheerde exemplaren.|
|[az sql mi show](/cli/azure/sql/mi#az_sql_mi_show)|Haalt de details voor een beheerd exemplaar op.|
|[az sql mi update](/cli/azure/sql/mi#az_sql_mi_update)|Werkt een beheerd exemplaar bij.|
|[az sql mi delete](/cli/azure/sql/mi#az_sql_mi_delete)|Hiermee verwijdert u een beheerd exemplaar.|
|[az sql mi op list](/cli/azure/sql/mi/op#az_sql_mi_op_list)|Haalt een lijst op met beheerbewerkingen die zijn uitgevoerd op het beheerde exemplaar.|
|[az sql mi op show](/cli/azure/sql/mi/op#az_sql_mi_op_show)|Haalt de specifieke beheerbewerking op die wordt uitgevoerd op het beheerde exemplaar.|
|[az sql mi op cancel](/cli/azure/sql/mi/op#az_sql_mi_op_cancel)|Annuleert de specifieke beheerbewerking die wordt uitgevoerd op het beheerde exemplaar.|
|[az sql midb create](/cli/azure/sql/midb#az_sql_midb_create) |Hiermee maakt u een beheerde database.|
|[az sql midb list](/cli/azure/sql/midb#az_sql_midb_list)|Een lijst met beschikbare beheerde databases.|
|[az sql midb restore](/cli/azure/sql/midb#az_sql_midb_restore)|Herstelt een beheerde database.|
|[az sql midb delete](/cli/azure/sql/midb#az_sql_midb_delete)|Hiermee verwijdert u een beheerde database.|

## <a name="transact-sql-create-and-configure-instance-databases"></a>Transact-SQL: Exemplaardatabases maken en configureren

Als u exemplaardatabases wilt maken en configureren nadat het beheerde exemplaar is gemaakt, gebruikt u de volgende T-SQL-opdrachten. U kunt deze opdrachten uitvoeren met behulp van Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Azure Data Studio,](/sql/azure-data-studio/what-is) [Visual Studio Code of](https://code.visualstudio.com/docs)een ander programma dat verbinding kan maken met een server en Transact-SQL-opdrachten kan doorgeven.

> [!TIP]
> Zie [Quickstart: Azure VM](connect-vm-instance-configure.md) configureren om verbinding te maken met Azure SQL Managed Instance en [Quickstart: Een punt-naar-site-verbinding](point-to-site-p2s-configure.md)configureren naar Azure SQL Managed Instance vanaf on-premises voor quickstarts over het configureren van een beheerd exemplaar met behulp van SQL Server Management Studio in Microsoft Windows.

> [!IMPORTANT]
> U kunt geen beheerd exemplaar maken of verwijderen met transact-SQL.

| Opdracht | Beschrijving |
| --- | --- |
|[CREATE DATABASE](/sql/t-sql/statements/create-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current)|Hiermee maakt u een nieuwe exemplaardatabase in SQL Managed Instance. U moet zijn verbonden met de hoofddatabase om een nieuwe database te kunnen maken.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?preserve-view=true&view=azuresqldb-mi-current) |Wijzigt een exemplaardatabase in SQL Managed Instance.|

## <a name="rest-api-create-and-configure-managed-instances"></a>REST API: Beheerde exemplaren maken en configureren

Als u beheerde exemplaren wilt maken en configureren, gebruikt u deze REST API aanvragen.

| Opdracht | Beschrijving |
| --- | --- |
|[Beheerde exemplaren - Maken of bijwerken](/rest/api/sql/managedinstances/createorupdate)|Hiermee wordt een beheerd exemplaar gemaakt of bijgewerkt.|
|[Beheerde exemplaren - Verwijderen](/rest/api/sql/managedinstances/delete)|Hiermee verwijdert u een beheerd exemplaar.|
|[Beheerde exemplaren - Get](/rest/api/sql/managedinstances/get)|Haalt een beheerd exemplaar op.|
|[Beheerde exemplaren - Lijst](/rest/api/sql/managedinstances/list)|Retourneert een lijst met beheerde exemplaren in een abonnement.|
|[Beheerde exemplaren - Lijst op resourcegroep](/rest/api/sql/managedinstances/listbyresourcegroup)|Retourneert een lijst met beheerde exemplaren in een resourcegroep.|
|[Beheerde exemplaren - Bijwerken](/rest/api/sql/managedinstances/update)|Werkt een beheerd exemplaar bij.|
|[Bewerkingen voor beheerd exemplaar - Lijst per beheerd exemplaar](/rest/api/sql/managedinstanceoperations/listbymanagedinstance)|Haalt een lijst op met beheerbewerkingen die zijn uitgevoerd op het beheerde exemplaar.|
|[Bewerkingen van beheerd exemplaar - Get](/rest/api/sql/managedinstanceoperations/get)|Haalt de specifieke beheerbewerking op die wordt uitgevoerd op het beheerde exemplaar.|
|[Bewerkingen van beheerd exemplaar - Annuleren](/rest/api/sql/managedinstanceoperations/cancel)|Annuleert de specifieke beheerbewerking die is uitgevoerd op het beheerde exemplaar.|

## <a name="next-steps"></a>Volgende stappen

- Zie Migreren naar SQL Server voor meer informatie over het [Azure SQL Database.](../database/migrate-to-database-from-sql-server.md)
- Zie Functies voor meer informatie over [ondersteunde functies.](../database/features-comparison.md)