---
title: Opslag Azure PowerShell automatisch verg Roten-Azure Database for PostgreSQL
description: In dit artikel wordt beschreven hoe u automatische groei opslag kunt inschakelen met behulp van Power shell in Azure Database for PostgreSQL.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: bc4655ce6cd572183cd92e1c8b2ac10e613ebd8f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92489962"
---
# <a name="auto-grow-storage-in-azure-database-for-postgresql-server-using-powershell"></a>Opslag automatisch uitbreiden in Azure Database for PostgreSQL server met behulp van Power shell

In dit artikel wordt beschreven hoe u een Azure Database for PostgreSQL Server-opslag kunt configureren om te groeien zonder dat dit van invloed is op de werk belasting.

Met opslag automatisch verg Roten wordt voor komen dat uw server [de opslag limiet bereikt](./concepts-pricing-tiers.md#reaching-the-storage-limit) en alleen-lezen wordt. Voor servers met 100 GB of minder ingerichte opslag, wordt de grootte verhoogd met 5 GB wanneer de vrije ruimte lager is dan 10%. Voor servers met meer dan 100 GB ingerichte opslag wordt de grootte verhoogd met 5% wanneer de beschik bare ruimte lager is dan 10 GB. De maximale opslag limieten gelden zoals opgegeven in de sectie opslag van de [prijs categorieën Azure database for PostgreSQL](./concepts-pricing-tiers.md#storage).

> [!IMPORTANT]
> Houd er rekening mee dat opslag alleen omhoog kan worden geschaald.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

- De [AZ Power shell-module](/powershell/azure/install-az-ps) die lokaal is geïnstalleerd of [Azure Cloud shell](https://shell.azure.com/) in de browser
- Een [Azure database for postgresql server](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Hoewel de PowerShell-module Az.PostgreSql in preview is, moet u deze afzonderlijk van de PowerShell-module Az installeren met behulp van de volgende opdracht: `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Zodra de PowerShell-module Az.PostgreSql algemeen beschikbaar is, wordt deze onderdeel van toekomstige releases van Az PowerShell en is de module systeemeigen beschikbaar vanuit Azure Cloud Shell.

Als u Power shell lokaal wilt gebruiken, maakt u verbinding met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

## <a name="enable-postgresql-server-storage-auto-grow"></a>Automatisch verg Roten van PostgreSQL-Server opslag inschakelen

Schakel het automatisch uitbreiden van de server in op een bestaande server met de volgende opdracht:

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Automatisch verg Roten van de server inschakelen bij het maken van een nieuwe server met de volgende opdracht:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Lees replica's maken en beheren in azure database for PostgreSQL met behulp van Power shell](howto-read-replicas-powershell.md).