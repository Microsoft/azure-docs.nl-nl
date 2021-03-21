---
title: Een verbindingsreeks genereren met PowerShell - Azure Database for MariaDB
description: Dit artikel biedt een Azure PowerShell-voorbeeld voor het genereren van een verbindingsreeks om verbinding met Azure Database for MySQL te maken.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/5/2020
ms.openlocfilehash: 9dee109c701d3760c93f39e639dcfd7cae07b595
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98663720"
---
# <a name="how-to-generate-an-azure-database-for-mariadb-connection-string-with-powershell"></a>Azure Database for MariaDB-verbindingsreeks genereren met PowerShell

In dit artikel wordt beschreven hoe u een verbindingsreeks genereert voor een Azure Database for MariaDB-server. U kunt een verbindingsreeks gebruiken om verbinding te maken met een Azure Database for MariaDB vanuit verschillende toepassingen.

## <a name="requirements"></a>Vereisten

In dit artikel worden de resources die in de volgende handleiding zijn gemaakt, als uitgangspunt gebruikt:

* [Quickstart: Een Azure Database for MariaDB-server maken met behulp van PowerShell](quickstart-create-mariadb-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>De verbindingsreeks ophalen

De cmdlet `Get-AzMariaDbConnectionString` wordt gebruikt om een verbindingsreeks te genereren voor het verbinden van toepassingen met Azure Database for MariaDB. In het volgende voorbeeld wordt de verbindingsreeks voor een PHP-client uit **mydemoserver** geretourneerd.

```azurepowershell-interactive
Get-AzMariaDbConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mariadb.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
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
> [Azure Database for MariaDB-serverparameters aanpassen met behulp van PowerShell](howto-configure-server-parameters-using-powershell.md)
