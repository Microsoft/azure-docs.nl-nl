---
title: Lees replica's beheren-Azure PowerShell-Azure Database for MySQL
description: Meer informatie over het instellen en beheren van Lees replica's in Azure Database for MySQL met behulp van Power shell.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 8/24/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fe33730fc11bfc18b7d67471e1077fb9490385d4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94541926"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-powershell"></a>Lees replica's maken en beheren in Azure Database for MySQL met behulp van Power shell

In dit artikel leert u hoe u in de Azure Database for MySQL-service Lees replica's maakt en beheert met behulp van Power shell. Zie het [overzicht](concepts-read-replicas.md)voor meer informatie over het lezen van replica's.

## <a name="azure-powershell"></a>Azure PowerShell

U kunt met behulp van Power shell Lees replica's maken en beheren.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

- De [AZ Power shell-module](/powershell/azure/install-az-ps) die lokaal is geïnstalleerd of [Azure Cloud shell](https://shell.azure.com/) in de browser
- Een [Azure database for mysql server](quickstart-create-mysql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Zo lang de PowerShell-module Az.MySQL in preview is, moet u deze afzonderlijk van de Az-module van PowerShell installeren met behulp van de volgende opdracht: `Install-Module -Name Az.MySql -AllowPrerelease`.
> Zodra de PowerShell-module Az.MySQL algemeen beschikbaar is, wordt het onderdeel van toekomstige releases van Az PowerShell en is de module systeemeigen beschikbaar vanuit Azure Cloud Shell.

Als u Power shell lokaal wilt gebruiken, maakt u verbinding met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/Connect-AzAccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

> [!IMPORTANT]
> De functie voor het lezen van replica's is alleen beschikbaar voor Azure Database for MySQL-servers in de prijs Categorieën Algemeen of geoptimaliseerd voor geheugen. Zorg ervoor dat de bron server zich in een van deze prijs categorieën bevindt.

### <a name="create-a-read-replica"></a>Een leesreplica maken

> [!IMPORTANT]
> Wanneer u een replica maakt voor een bron die geen bestaande replica's heeft, wordt de bron eerst opnieuw opgestart om zichzelf voor te bereiden voor replicatie. Houd dit in overweging en voer deze bewerkingen uit tijdens een rustige periode.

Een lees replica-server kan worden gemaakt met behulp van de volgende opdracht:

```azurepowershell-interactive
Get-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

Voor de `New-AzMySqlReplica` opdracht zijn de volgende para meters vereist:

| Instelling | Voorbeeldwaarde | Beschrijving  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  De resource groep waar de replica-server is gemaakt.  |
| Name | mydemoreplicaserver | De naam van de nieuwe replica server die wordt gemaakt. |

Gebruik de **locatie** parameter om een lees replica te maken. In het volgende voor beeld wordt een replica gemaakt in de regio **VS-West** .

```azurepowershell-interactive
Get-AzMySqlServer -Name mrdemoserver -ResourceGroupName myresourcegroup |
  New-AzMySqlReplica -Name mydemoreplicaserver -ResourceGroupName myresourcegroup -Location westus
```

Ga naar het [artikel concepten van replica's lezen](concepts-read-replicas.md)voor meer informatie over de regio's die u kunt maken in de replica.

Lees replica's worden standaard gemaakt met dezelfde server configuratie als de bron, tenzij de **SKU** -para meter is opgegeven.

> [!NOTE]
> Het is raadzaam om de configuratie van de replica server te behouden in gelijke of hogere waarden dan de bron om ervoor te zorgen dat de replica kan blijven werken met de Master.

### <a name="list-replicas-for-a-source-server"></a>Replica's weer geven voor een bron server

Als u alle replica's voor een bepaalde bron server wilt weer geven, voert u de volgende opdracht uit:

```azurepowershell-interactive
Get-AzMySqlReplica -ResourceGroupName myresourcegroup -ServerName mydemoserver
```

Voor de `Get-AzMySqlReplica` opdracht zijn de volgende para meters vereist:

| Instelling | Voorbeeldwaarde | Beschrijving  |
| --- | --- | --- |
| ResourceGroupName |  myResourceGroup |  De resource groep waar de replica-server wordt gemaakt.  |
| ServerName | mydemoserver | De naam of ID van de bron server. |

### <a name="delete-a-replica-server"></a>Een replica server verwijderen

Het verwijderen van een lees replica-server kan worden uitgevoerd door de cmdlet uit te voeren `Remove-AzMySqlServer` .

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoreplicaserver -ResourceGroupName myresourcegroup
```

### <a name="delete-a-source-server"></a>Een bron server verwijderen

> [!IMPORTANT]
> Als u een bronserver verwijdert, wordt de replicatie naar alle replicaservers gestopt en wordt de bronserver zelf verwijderd. Replicaservers worden zelfstandige servers die nu zowel lees-als schrijfbewerkingen ondersteunen.

Als u een bron server wilt verwijderen, kunt u de `Remove-AzMySqlServer` cmdlet uitvoeren.

```azurepowershell-interactive
Remove-AzMySqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure Database for MySQL server opnieuw opstarten met Power shell](howto-restart-server-powershell.md)
