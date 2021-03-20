---
title: Een connection string genereren met Power shell-Azure Database for PostgreSQL
description: In dit artikel vindt u een Azure PowerShell voor beeld van het genereren van een connection string om verbinding te maken met Azure Database for PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/6/2020
ms.openlocfilehash: 29641bdc3696f0722ca430dde912768071fd9b16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91708166"
---
# <a name="how-to-generate-an-azure-database-for-postgresql-connection-string-with-powershell"></a>Een Azure Database for PostgreSQL connection string genereren met Power shell

In dit artikel wordt beschreven hoe u een connection string genereert voor een Azure Database for PostgreSQL-server. U kunt een connection string gebruiken om verbinding te maken met een Azure Database for PostgreSQL van verschillende toepassingen.

## <a name="requirements"></a>Vereisten

In dit artikel worden de resources die in de volgende handleiding zijn gemaakt, als uitgangspunt gebruikt:

* [Snelstartgids: een Azure Database for PostgreSQL-server maken met behulp van Power shell](quickstart-create-postgresql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De `Get-AzPostgreSqlConnectionString` cmdlet wordt gebruikt voor het genereren van een Connection String voor het verbinden van toepassingen met Azure database for PostgreSQL. In het volgende voorbeeld wordt de verbindingsreeks voor een PHP-client uit **mydemoserver** geretourneerd.

```azurepowershell-interactive
Get-AzPostgreSqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
host=mydemoserver.postgres.database.azure.com port=5432 dbname={your_database} user=myadmin@mydemoserver password={your_password} sslmode=require
```

Geldige waarden voor de parameter `Client` zijn:

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure Database for PostgreSQL server parameters aanpassen met behulp van Power shell](howto-configure-server-parameters-using-powershell.md)
