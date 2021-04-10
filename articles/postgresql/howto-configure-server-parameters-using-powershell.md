---
title: Server parameters configureren-Azure PowerShell-Azure Database for PostgreSQL
description: In dit artikel wordt beschreven hoe u de service parameters in Azure Database for PostgreSQL kunt configureren met behulp van Power shell.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.devlang: azurepowershell
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6e9bf55aa46e2206928a5f93285a4001dc731f64
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604291"
---
# <a name="customize-azure-database-for-postgresql-server-parameters-using-powershell"></a>Azure Database for PostgreSQL server parameters aanpassen met behulp van Power shell

U kunt configuratie parameters voor een Azure Database for PostgreSQL server weer geven, tonen en bijwerken met behulp van Power shell. Een subset van de engine configuraties wordt weer gegeven op server niveau en kan worden gewijzigd.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

- De [AZ Power shell-module](/powershell/azure/install-az-ps) die lokaal is geïnstalleerd of [Azure Cloud shell](https://shell.azure.com/) in de browser
- Een [Azure database for postgresql server](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Hoewel de PowerShell-module Az.PostgreSql in preview is, moet u deze afzonderlijk van de PowerShell-module Az installeren met behulp van de volgende opdracht: `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Zodra de PowerShell-module Az.PostgreSql algemeen beschikbaar is, wordt deze onderdeel van toekomstige releases van Az PowerShell en is de module systeemeigen beschikbaar vanuit Azure Cloud Shell.

Als u Power shell lokaal wilt gebruiken, maakt u verbinding met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="list-server-configuration-parameters-for-azure-database-for-postgresql-server"></a>Server configuratie parameters voor Azure Database for PostgreSQL server weer geven

Voer de cmdlet uit om alle para meters die kunnen worden gewijzigd op een server en hun waarden weer te geven `Get-AzPostgreSqlConfiguration` .

In het volgende voor beeld worden de server configuratie parameters weer gegeven voor de server **mydemoserver** in de resource groep **myresourcegroup**.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Voor de definitie van elk van de vermelde para meters, zie de sectie PostgreSQL-verwijzing in [omgevings variabelen](https://www.postgresql.org/docs/12/libpq-envars.html).

## <a name="show-server-configuration-parameter-details"></a>Details van server configuratie parameters weer geven

Als u details over een bepaalde configuratie parameter voor een server wilt weer geven, voert u de `Get-AzPostgreSqlConfiguration` cmdlet uit en geeft u de para meter **name** op.

In dit voor beeld worden details weer gegeven van de configuratie parameter server van het **langzame \_ query \_ logboek** voor server **mydemoserver** onder resource groep **myresourcegroup**.

```azurepowershell-interactive
Get-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

## <a name="modify-a-server-configuration-parameter-value"></a>Een waarde voor de para meter server configuratie wijzigen

U kunt ook de waarde van een bepaalde server configuratie parameter wijzigen, waarmee de onderliggende configuratie waarde wordt bijgewerkt voor de PostgreSQL-server engine. Gebruik de cmdlet om de configuratie bij te werken `Update-AzPostgreSqlConfiguration` .

Voor het bijwerken van de configuratie parameter van de **langzame \_ query \_ logboek** server van server **mydemoserver** onder resource groep **myresourcegroup**.

```azurepowershell-interactive
Update-AzPostgreSqlConfiguration -Name slow_query_log -ResourceGroupName myresourcegroup -ServerName mydemoserver -Value On
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Opslag automatisch uitbreiden in azure database for postgresql server met behulp van Power shell](howto-auto-grow-storage-powershell.md).