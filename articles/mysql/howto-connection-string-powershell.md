---
title: Een verbindingsreeks genereren met PowerShell - Azure Database for MySQL
description: Dit artikel biedt een Azure PowerShell-voorbeeld voor het genereren van een verbindingsreeks om verbinding met Azure Database for MariaDB te maken.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: 9f960d32dcf1f359327dccc01eeb06825cc3a062
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94541467"
---
# <a name="how-to-generate-an-azure-database-for-mysql-connection-string-with-powershell"></a>Een Azure Database for MariaDB-verbindingsreeks genereren met PowerShell

In dit artikel wordt beschreven hoe u een verbindingsreeks genereert voor een Azure Database for MySQL-server. U kunt een verbindingsreeks gebruiken om verbinding te maken met een Azure Database for MySQL vanuit verschillende toepassingen.

## <a name="requirements"></a>Vereisten

In dit artikel worden de resources die in de volgende handleiding zijn gemaakt, als uitgangspunt gebruikt:

* [Snelstart: Een Azure Database for MySQL-server maken met behulp van PowerShell](quickstart-create-mysql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De cmdlet `Get-AzMySqlConnectionString` wordt gebruikt om een verbindingsreeks te genereren voor het verbinden van toepassingen met Azure Database for MySQL. In het volgende voorbeeld wordt de verbindingsreeks voor een PHP-client uit **mydemoserver** geretourneerd.

```azurepowershell-interactive
Get-AzMySqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mysql.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
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
> [Azure Database for MySQL-serverparameters aanpassen met behulp van PowerShell](howto-configure-server-parameters-using-powershell.md)
