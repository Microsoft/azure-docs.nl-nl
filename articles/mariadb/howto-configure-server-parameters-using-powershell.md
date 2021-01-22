---
title: Server parameters configureren-Azure PowerShell-Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u de service parameters in Azure Database for MariaDB kunt configureren met behulp van Power shell.
author: savjani
ms.author: pariks
ms.service: jroth
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 10/1/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 891cb5c6e570269d0414e7b7cece760af143927f
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98662454"
---
# <a name="configure-server-parameters-in-azure-database-for-mariadb-using-powershell"></a>Server parameters configureren in Azure Database for MariaDB met behulp van Power shell

U kunt configuratie parameters voor een Azure Database for MariaDB server weer geven, tonen en bijwerken met behulp van Power shell. Een subset van de engine configuraties wordt weer gegeven op server niveau en kan worden gewijzigd.

>[!Note]
> Serverparameters kunnen globaal worden bijgewerkt op serverniveau via de [Azure CLI](./howto-configure-server-parameters-cli.md), [PowerShell](./howto-configure-server-parameters-using-powershell.md) of [Azure Portal](./howto-server-parameters.md).

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

- De [AZ Power shell-module](/powershell/azure/install-az-ps) die lokaal is geïnstalleerd of [Azure Cloud shell](https://shell.azure.com/) in de browser
- Een [Azure database for MariaDB server](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Hoewel de PowerShell-module Az.MariaDb in preview is, moet u deze afzonderlijk van de Az-module van PowerShell installeren met behulp van de volgende opdracht: `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Zodra de PowerShell-module Az.MariaDb algemeen beschikbaar is, wordt het onderdeel van toekomstige releases van Az PowerShell en is de module systeemeigen beschikbaar vanuit Azure Cloud Shell.

Als u Power shell lokaal wilt gebruiken, maakt u verbinding met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-mariadb-server"></a>Server configuratie parameters voor Azure Database for MariaDB server weer geven

Voer de cmdlet uit om alle para meters die kunnen worden gewijzigd op een server en hun waarden weer te geven `Get-AzMariaDbConfiguration` .

In het volgende voor beeld worden de server configuratie parameters weer gegeven voor de server **mydemoserver** in de resource groep **myresourcegroup**.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Zie de sectie MariaDB Reference op [Server systeem variabelen](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html)voor de definitie van elk van de vermelde para meters.

## <a name="show-server-configuration-parameter-details"></a>Details van server configuratie parameters weer geven

Als u details over een bepaalde configuratie parameter voor een server wilt weer geven, voert u de `Get-AzMariaDbConfiguration` cmdlet uit en geeft u de para meter **name** op.

In dit voor beeld worden details weer gegeven van de configuratie parameter server van het **langzame \_ query \_ logboek** voor server **mydemoserver** onder resource groep **myresourcegroup**.

```azurepowershell-interactive
Get-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Een waarde voor de para meter server configuratie wijzigen

U kunt ook de waarde van een bepaalde server configuratie parameter wijzigen, waarmee de onderliggende configuratie waarde wordt bijgewerkt voor de MariaDB-server engine. Gebruik de cmdlet om de configuratie bij te werken `Update-AzMariaDbConfiguration` .

Voor het bijwerken van de configuratie parameter van de **langzame \_ query \_ logboek** server van server **mydemoserver** onder resource groep **myresourcegroup**.

```azurepowershell-interactive
Update-AzMariaDbConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Opslag automatisch uitbreiden in azure database for MariaDB server met behulp van Power shell](howto-auto-grow-storage-powershell.md).