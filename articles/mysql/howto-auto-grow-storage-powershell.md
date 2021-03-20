---
title: Opslag Azure PowerShell automatisch verg Roten-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u automatische groei opslag kunt inschakelen met behulp van Power shell in Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 4/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e8c9f6f66e484fbd9ebe5c15934936d6e5c59073
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94542028"
---
# <a name="auto-grow-storage-in-azure-database-for-mysql-server-using-powershell"></a>Opslag automatisch uitbreiden in Azure Database for MySQL server met behulp van Power shell

In dit artikel wordt beschreven hoe u een Azure Database for MySQL server-opslag kunt configureren om te groeien zonder dat dit van invloed is op de werk belasting.

Met opslag automatisch verg Roten wordt voor komen dat uw server [de opslag limiet bereikt](./concepts-pricing-tiers.md#reaching-the-storage-limit) en alleen-lezen wordt. Voor servers met 100 GB of minder ingerichte opslag, wordt de grootte verhoogd met 5 GB wanneer de vrije ruimte lager is dan 10%. Voor servers met meer dan 100 GB ingerichte opslag wordt de grootte verhoogd met 5% wanneer de beschik bare ruimte lager is dan 10 GB. De maximale opslag limieten gelden zoals opgegeven in de sectie opslag van de [prijs categorieën Azure database for MySQL](./concepts-pricing-tiers.md#storage).

> [!IMPORTANT]
> Houd er rekening mee dat opslag alleen omhoog kan worden geschaald.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

- De [AZ Power shell-module](/powershell/azure/install-az-ps) die lokaal is geïnstalleerd of [Azure Cloud shell](https://shell.azure.com/) in de browser
- Een [Azure database for mysql server](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Zo lang de PowerShell-module Az.MySQL in preview is, moet u deze afzonderlijk van de Az-module van PowerShell installeren met behulp van de volgende opdracht: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Zodra de PowerShell-module Az.MySQL algemeen beschikbaar is, wordt het onderdeel van toekomstige releases van Az PowerShell en is de module systeemeigen beschikbaar vanuit Azure Cloud Shell.

Als u Power shell lokaal wilt gebruiken, maakt u verbinding met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

## <a name="enable-mysql-server-storage-auto-grow"></a>MySQL-server opslag automatisch verg Roten inschakelen

Schakel het automatisch uitbreiden van de server in op een bestaande server met de volgende opdracht:

```azurepowershell-interactive
Update-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -StorageAutogrow Enabled
```

Automatisch verg Roten van de server inschakelen bij het maken van een nieuwe server met de volgende opdracht:

```azurepowershell-interactive
$Password = Read-Host -Prompt 'Please enter your password' -AsSecureString
New-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup -Sku GP_Gen5_2 -StorageAutogrow Enabled -Location westus -AdministratorUsername myadmin -AdministratorLoginPassword $Password
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Lees replica's maken en beheren in azure database for MySQL met behulp van Power shell](howto-read-replicas-powershell.md).