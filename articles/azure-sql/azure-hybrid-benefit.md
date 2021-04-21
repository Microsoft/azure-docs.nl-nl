---
title: Azure Hybrid Benefit
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Gebruik bestaande SQL Server voor Azure SQL Database- en SQL Managed Instance licenties.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 02/16/2021
ms.openlocfilehash: b5f85e0dcb8ca70d5773b8f1c3b53e0b449ef013
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779197"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Azure Hybrid Benefit : Azure SQL Database & SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

In de ingerichte compute-laag van het aankoopmodel op basis van vCore kunt u uw bestaande licenties inruilen voor kortingstarieven op Azure SQL Database en Azure SQL Managed Instance met behulp van [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). Met dit Azure-voordeel kunt u tot 30 procent of zelfs hoger besparen op SQL Database & SQL Managed Instance door uw SQL Server-licenties met Software Assurance. De [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) heeft een rekenmachine om de besparingen te bepalen.  Houd er rekening mee dat Azure Hybrid Benefit niet van toepassing is op Azure SQL Database serverloos.

> [!NOTE]
> Het wijzigen Azure Hybrid Benefit vereist geen downtime.

![Prijsstructuur voor vcore](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>Een licentiemodel kiezen

Met Azure Hybrid Benefit kunt u ervoor kiezen om alleen te betalen voor de onderliggende Azure-infrastructuur met behulp van uw bestaande SQL Server-licentie voor de SQL Server-database-engine zelf (basisrekenprijzen), of u kunt betalen voor zowel de onderliggende infrastructuur als de SQL Server-licentie (prijs inbegrepen bij licentie).

U kunt uw licentiemodel kiezen of wijzigen in de Azure Portal: 
- Voor nieuwe databases selecteert u tijdens het maken database **configureren** op het tabblad **Basisbeginselen** en selecteert u de optie om geld te besparen.
- Voor bestaande databases selecteert u **Configureren** in het menu **Instellingen** en selecteert u de optie om geld te besparen.

U kunt ook een nieuwe of bestaande database configureren met behulp van een van de volgende API's:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Het licentietype instellen of bijwerken met behulp van PowerShell:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Het licentietype instellen of bijwerken met behulp van de Azure CLI:

- [az sql db create](/cli/azure/sql/db#az_sql_db_create)
- [az sql mi create](/cli/azure/sql/mi#az_sql_mi_create)
- [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update)

# <a name="rest-api"></a>[REST API](#tab/rest)

Het licentietype instellen of bijwerken met behulp van de REST API:

- [Databases : maken of bijwerken](/rest/api/sql/databases/createorupdate)
- [Databases - Bijwerken](/rest/api/sql/databases/update)
- [Beheerde exemplaren - Maken of bijwerken](/rest/api/sql/managedinstances/createorupdate)
- [Beheerde exemplaren - Bijwerken](/rest/api/sql/managedinstances/update)

* * *


### <a name="azure-hybrid-benefit-questions"></a>Azure Hybrid Benefit vragen

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Zijn er rechten voor dubbel gebruik met Azure Hybrid Benefit voor SQL Server?

U hebt 180 dagen aan rechten voor dubbel gebruik van de licentie om ervoor te zorgen dat migraties naadloos worden uitgevoerd. Na die periode van 180 dagen kunt u alleen de SQL Server in de cloud in SQL Database. U hebt niet langer rechten voor dubbel gebruik on-premises en in de cloud.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Hoe verschilt Azure Hybrid Benefit voor SQL Server van licentiemobiliteit?

We bieden voordelen op het gebied van licentiemobiliteit SQL Server klanten met Software Assurance. Hierdoor kunnen hun licenties opnieuw worden toewijzen aan de gedeelde servers van een partner. U kunt dit voordeel gebruiken in Azure IaaS en AWS EC2.

Azure Hybrid Benefit voor SQL Server verschilt van licentiemobiliteit op twee belangrijke gebieden:

- Het biedt economische voordelen voor het verplaatsen van zeer gevirtualiseerde workloads naar Azure. SQL Server Enterprise Edition-klanten kunnen vier kernen in Azure in de Algemeen-SKU krijgen voor elke kern die ze on-premises hebben voor zeer gevirtualiseerde toepassingen. Licentiemobiliteit biedt geen speciale kostenvoordelen voor het verplaatsen van gevirtualiseerde workloads naar de cloud.
- Het biedt een PaaS-bestemming in Azure (SQL Managed Instance) die zeer compatibel is met SQL Server.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Wat zijn de specifieke rechten van de Azure Hybrid Benefit voor SQL Server?

SQL Database en SQL Managed Instance hebben de volgende rechten die zijn gekoppeld aan Azure Hybrid Benefit voor SQL Server:

|Licentievoetafdruk|Wat Azure Hybrid Benefit u SQL Server?|
|---|---|
|SQL Server Enterprise Edition Core-klanten met SA|<li>Kan het basistarief betalen voor Hyperscale, Algemeen of Bedrijfskritiek SKU</li><br><li>1 kern on-premises = 4 kernen in Hyperscale SKU</li><br><li>1 kern on-premises = 4 kernen in Algemeen SKU</li><br><li>1 kern on-premises = 1 kern in Bedrijfskritiek SKU</li>|
|SQL Server Standard Edition Core-klanten met SA|<li>Kan het basistarief betalen voor Hyperscale, Algemeen of Bedrijfskritiek SKU</li><br><li>1 kern on-premises = 1 kern in Hyperscale SKU</li><br><li>1 kern on-premises = 1 kern in Algemeen SKU</li><br><li>4 kernen on-premises = 1 kern in Bedrijfskritiek SKU</li>|
|||


## <a name="next-steps"></a>Volgende stappen

- Zie De juiste implementatieoptie kiezen in Azure SQL voor hulp bij het kiezen [van een Azure SQL.](azure-sql-iaas-vs-paas-what-is-overview.md)
- Zie voor een vergelijking SQL Database en SQL Managed Instance functies SQL Database & SQL Managed Instance [functies.](database/features-comparison.md)
