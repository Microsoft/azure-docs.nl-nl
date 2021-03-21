---
title: Server opnieuw starten-Azure PowerShell-Azure Database for PostgreSQL
description: In dit artikel wordt beschreven hoe u een Azure Database for PostgreSQL server opnieuw kunt opstarten met behulp van Power shell.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 06/08/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2d37de4a1861cb78b4a76c8ca7bc8c3643245b32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97706963"
---
# <a name="restart-azure-database-for-postgresql-server-using-powershell"></a>Azure Database for PostgreSQL server opnieuw opstarten met Power shell

In dit onderwerp wordt beschreven hoe u een Azure Database for PostgreSQL server opnieuw kunt starten. Mogelijk moet u de server opnieuw opstarten om onderhouds redenen te zorgen, waardoor tijdens de bewerking een korte onderbreking optreedt.

Het opnieuw opstarten van de server wordt geblokkeerd als de service bezet is. De service kan bijvoorbeeld een eerder aangevraagde bewerking verwerken, zoals het schalen van vCores.

> [!NOTE] 
> De tijd die nodig is om opnieuw op te starten, is afhankelijk van het PostgreSQL-herstel proces. Om de herstarttijd te verlagen, raden we u aan om de hoeveelheid activiteit die op de server plaatsvindt, te minimaliseren voordat de computer opnieuw wordt opgestart. Mogelijk wilt u ook de frequentie van het controle punt verhogen. U kunt ook aan het controle punt gerelateerde parameter waarden afstemmen, inclusief `max_wal_size` . Het is ook raadzaam om de opdracht uit te voeren `CHECKPOINT` voordat de server opnieuw wordt opgestart.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

- De [AZ Power shell-module](/powershell/azure/install-az-ps) die lokaal is geïnstalleerd of [Azure Cloud shell](https://shell.azure.com/) in de browser
- Een [Azure database for postgresql server](quickstart-create-postgresql-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Hoewel de PowerShell-module Az.PostgreSql in preview is, moet u deze afzonderlijk van de PowerShell-module Az installeren met behulp van de volgende opdracht: `Install-Module -Name Az.PostgreSql -AllowPrerelease`.
> Zodra de PowerShell-module Az.PostgreSql algemeen beschikbaar is, wordt deze onderdeel van toekomstige releases van Az PowerShell en is de module systeemeigen beschikbaar vanuit Azure Cloud Shell.

Als u Power shell lokaal wilt gebruiken, maakt u verbinding met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Start de server opnieuw

Start de server opnieuw op met de volgende opdracht:

```azurepowershell-interactive
Restart-AzPostgreSqlServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Azure Database for PostgreSQL-server maken met behulp van Power shell](quickstart-create-postgresql-server-database-using-azure-powershell.md)