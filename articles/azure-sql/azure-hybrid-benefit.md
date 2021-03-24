---
title: Azure Hybrid Benefit
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Gebruik bestaande SQL Server licenties voor Azure SQL Database en SQL Managed instance-kortingen.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 02/16/2021
ms.openlocfilehash: f7a37e761e37e295bbb92e442b1813ebded2a7cd
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955275"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Azure Hybrid Benefit-Azure SQL Database & beheerde instantie van SQL
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

In de ingerichte Compute-laag van het op vCore gebaseerde aankoop model kunt u uw bestaande licenties uitwisselen voor kortings tarieven op Azure SQL Database en Azure SQL Managed instance met behulp van [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). Met dit voor deel van Azure kunt u tot wel 30% of zelfs meer besparen op SQL Database & SQL Managed instance met behulp van uw SQL Server licenties met Software Assurance. De [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) pagina heeft een reken machine om te helpen bij het bepalen van de besparingen.  Houd er rekening mee dat de Azure Hybrid Benefit niet van toepassing is op Azure SQL Database Server.

> [!NOTE]
> Voor het wijzigen van Azure Hybrid Benefit is geen uitval tijd vereist.

![VCore-prijs structuur](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>Een licentie model kiezen

Met Azure Hybrid Benefit kunt u ervoor kiezen om alleen te betalen voor de onderliggende Azure-infra structuur met behulp van uw bestaande SQL Server licentie voor de SQL Server-data base-engine zelf (prijzen voor basis berekeningen), of u kunt betalen voor zowel de onderliggende infra structuur als de SQL Server licentie (prijs in licentie inbegrepen).

U kunt uw licentie model kiezen of wijzigen in de Azure Portal: 
- Voor nieuwe data bases, tijdens het maken, selecteert u **Data Base configureren** op het tabblad **basis beginselen** en selecteert u de optie om geld te besparen.
- Voor bestaande data bases selecteert **u configureren** in het menu **instellingen** en selecteert u de optie om geld te besparen.

U kunt ook een nieuwe of bestaande data base configureren met behulp van een van de volgende Api's:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Het licentie type instellen of bijwerken met behulp van Power shell:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Het licentie type instellen of bijwerken met behulp van de Azure CLI:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [AZ SQL mi Create](/cli/azure/sql/mi#az-sql-mi-create)
- [AZ SQL mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST API](#tab/rest)

Het licentie type instellen of bijwerken met behulp van de REST API:

- [Data bases: maken of bijwerken](/rest/api/sql/databases/createorupdate)
- [Data bases-bijwerken](/rest/api/sql/databases/update)
- [Beheerde instanties: maken of bijwerken](/rest/api/sql/managedinstances/createorupdate)
- [Beheerde instanties-bijwerken](/rest/api/sql/managedinstances/update)

* * *


### <a name="azure-hybrid-benefit-questions"></a>Vragen over Azure Hybrid Benefit

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>Zijn er twee gebruiks rechten met Azure Hybrid Benefit voor SQL Server?

U hebt 180 dagen aan dubbele gebruiks rechten van de licentie om ervoor te zorgen dat migraties naadloos worden uitgevoerd. Na deze periode van 180 dagen kunt u de SQL Server licentie in de Cloud alleen gebruiken in SQL Database. U hebt geen rechten voor dubbel gebruik meer on-premises en in de Cloud.

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>Hoe verschilt Azure Hybrid Benefit voor SQL Server van License Mobility?

We bieden de voor delen van License Mobility aan SQL Server klanten met Software Assurance. Hierdoor kan hun licenties opnieuw worden toegewezen aan de gedeelde servers van een partner. U kunt dit voor deel gebruiken op Azure IaaS en AWS EC2.

Azure Hybrid Benefit voor SQL Server wijkt af van de mobiliteit van licenties op twee belang rijke gebieden:

- Dit biedt economische voor delen voor het verplaatsen van uiterst gevirtualiseerde werk belastingen naar Azure. SQL Server Enterprise Edition-klanten kunnen in de Algemeen SKU vier kernen krijgen in azure voor elke kern waarover ze on-premises beschikken voor zeer gevirtualiseerde toepassingen. Licentie mobiliteit staat geen bijzondere kosten voordelen toe voor het verplaatsen van gevirtualiseerde werk belastingen naar de Cloud.
- Het biedt een PaaS-doel op Azure (SQL Managed instance) dat zeer compatibel is met SQL Server.

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>Wat zijn de specifieke rechten van de Azure Hybrid Benefit voor SQL Server?

Gebruikers van SQL Database en SQL Managed instance hebben de volgende rechten die zijn gekoppeld aan Azure Hybrid Benefit voor SQL Server:

|Licentie footprint|Wat doet Azure Hybrid Benefit voor SQL Server u doen?|
|---|---|
|SQL Server Enterprise Edition core-klanten met SA|<li>Kan het basis tarief voor grootschalige, Algemeen of Bedrijfskritiek SKU betalen</li><br><li>1 kern on-premises = 4 kernen in grootschalige SKU</li><br><li>1 kern on-premises = 4 kernen in Algemeen SKU</li><br><li>1 kern on-premises = 1 kern geheugen in Bedrijfskritiek SKU</li>|
|SQL Server Standard Edition core-klanten met SA|<li>Kan het basis tarief voor grootschalige, Algemeen of Bedrijfskritiek SKU betalen</li><br><li>1 kern on-premises = 1 kern in grootschalige SKU</li><br><li>1 kern on-premises = 1 kern geheugen in Algemeen SKU</li><br><li>4 core on-premises = 1 kern geheugen in Bedrijfskritiek SKU</li>|
|||


## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over het kiezen van een Azure SQL-implementatie optie [de juiste implementatie optie in Azure SQL](azure-sql-iaas-vs-paas-what-is-overview.md).
- Zie [SQL Database & functies van SQL Managed](database/features-comparison.md)instance voor een vergelijking van de functies van SQL database en SQL Managed instance.
