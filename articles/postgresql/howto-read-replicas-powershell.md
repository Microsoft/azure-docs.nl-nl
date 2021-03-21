---
title: Lees replica's beheren-Azure PowerShell-Azure Database for PostgreSQL
description: Meer informatie over het instellen en beheren van Lees replica's in Azure Database for PostgreSQL met behulp van Power shell.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b0a5547928bd7d19343c50e40ab9fcb2c335e893
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97674528"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-postgresql-using-powershell"></a>Lees replica's maken en beheren in Azure Database for PostgreSQL met behulp van Power shell

In dit artikel leert u hoe u in de Azure Database for PostgreSQL-service Lees replica's maakt en beheert met behulp van Power shell. Zie het [overzicht](concepts-read-replicas.md)voor meer informatie over het lezen van replica's.

## <a name="azure-powershell"></a>Azure PowerShell

U kunt met behulp van Power shell Lees replica's maken en beheren.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

- De [AZ Power shell-module](/powershell/azure/install-az-ps) die lokaal is geïnstalleerd of [Azure Cloud shell](https://shell.azure.com/) in de browser
- Een [Azure database for postgresql server](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Hoewel de PowerShell-module Az.PostgreSql in preview is, moet u deze afzonderlijk van de PowerShell-module Az installeren met behulp van de volgende opdracht: `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Zodra de PowerShell-module Az.PostgreSql algemeen beschikbaar is, wordt deze onderdeel van toekomstige releases van Az PowerShell en is de module systeemeigen beschikbaar vanuit Azure Cloud Shell.

Als u Power shell lokaal wilt gebruiken, maakt u verbinding met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> De functie voor het lezen van replica's is alleen beschikbaar voor Azure Database for PostgreSQL-servers in de prijs Categorieën Algemeen of geoptimaliseerd voor geheugen. Zorg ervoor dat de primaire server zich in een van deze prijs categorieën bevindt.

### <a name="create-a-read-replica"></a>Een leesreplica maken

Een lees replica-server kan worden gemaakt met behulp van de volgende opdracht:

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSqlServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

Voor de `New-AzPostgreSqlServerReplica` opdracht zijn de volgende para meters vereist:

| Instelling | Voorbeeldwaarde | Beschrijving  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  De resource groep waar de replica-server is gemaakt.  |
| Name | mydemoreplicaserver | De naam van de nieuwe replica server die wordt gemaakt. |

Gebruik de **locatie** parameter om een lees replica te maken. In het volgende voor beeld wordt een replica gemaakt in de regio **VS-West** .

```azurepowershell-interactive
Get-AzPostgreSqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzPostgreSQLServerReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Ga naar het [artikel concepten van replica's lezen](concepts-read-replicas.md)voor meer informatie over de regio's die u kunt maken in de replica.

Lees replica's worden standaard gemaakt met dezelfde server configuratie als de primaire, tenzij de **SKU** -para meter is opgegeven.

> [!NOTE]
> Het is raadzaam om de configuratie van de replica server te behouden met gelijke of hogere waarden dan de primaire waarde om ervoor te zorgen dat de replica kan blijven werken met de Master.

### <a name="list-replicas-for-a-primary-server"></a>Replica's weer geven voor een primaire server

Voer de volgende opdracht uit om alle replica's voor een bepaalde primaire server weer te geven:

```azurepowershell-interactive
Get-AzPostgreSQLReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Voor de `Get-AzPostgreSQLReplica` opdracht zijn de volgende para meters vereist:

| Instelling | Voorbeeldwaarde | Beschrijving  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  De resource groep waar de replica-server wordt gemaakt.  |
| ServerName | mydemoserver | De naam of ID van de primaire server. |

### <a name="stop-a-replica-server"></a>Een replica server stoppen

Als u een lees replica-server stopt, wordt de Lees replica gepromoot als onafhankelijke server. U kunt dit doen door de `Update-AzPostgreSqlServer` cmdlet uit te voeren en door de waarde ReplicationRole in te stellen op `None` .

```azurepowershell-interactive
Update-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -ReplicationRole None
```

### <a name="delete-a-replica-server"></a>Een replica server verwijderen

Het verwijderen van een lees replica-server kan worden uitgevoerd door de cmdlet uit te voeren `Remove-AzPostgreSqlServer` .

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-primary-server"></a>Een primaire server verwijderen

> [!IMPORTANT]
> Als u een primaire server verwijdert, wordt de replicatie naar alle replica servers gestopt en wordt de primaire server zelf verwijderd. Replicaservers worden zelfstandige servers die nu zowel lees-als schrijfbewerkingen ondersteunen.

Als u een primaire server wilt verwijderen, kunt u de `Remove-AzPostgreSqlServer` cmdlet uitvoeren.

```azurepowershell-interactive
Remove-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure Database for PostgreSQL server opnieuw opstarten met Power shell](howto-restart-server-powershell.md)