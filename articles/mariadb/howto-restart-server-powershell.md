---
title: Server opnieuw starten-Azure PowerShell-Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u een Azure Database for MariaDB server opnieuw kunt opstarten met behulp van Power shell.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 5/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 14cde5b1302c46b819bb7d841fb5b84a43c580c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98664865"
---
# <a name="restart-azure-database-for-mariadb-server-using-powershell"></a>Azure Database for MariaDB server opnieuw opstarten met Power shell

In dit onderwerp wordt beschreven hoe u een Azure Database for MariaDB server opnieuw kunt starten. Mogelijk moet u de server opnieuw opstarten om onderhouds redenen te zorgen, waardoor tijdens de bewerking een korte onderbreking optreedt.

Het opnieuw opstarten van de server wordt geblokkeerd als de service bezet is. De service kan bijvoorbeeld een eerder aangevraagde bewerking verwerken, zoals het schalen van vCores.

De hoeveelheid tijd die nodig is voor het volt ooien van een herstart is afhankelijk van het MariaDB-herstel proces. Om de herstarttijd te beperken, raden we u aan om de hoeveelheid activiteit die op de server plaatsvindt, te minimaliseren voordat de computer opnieuw wordt opgestart.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om deze hand leiding te volt ooien:

- De [AZ Power shell-module](/powershell/azure/install-az-ps) die lokaal is geïnstalleerd of [Azure Cloud shell](https://shell.azure.com/) in de browser
- Een [Azure database for MariaDB server](quickstart-create-mariadb-server-database-using-azure-powershell.md)

> [!IMPORTANT]
> Hoewel de PowerShell-module Az.MariaDb in preview is, moet u deze afzonderlijk van de Az-module van PowerShell installeren met behulp van de volgende opdracht: `Install-Module -Name Az.MariaDb -AllowPrerelease`.
> Zodra de PowerShell-module Az.MariaDb algemeen beschikbaar is, wordt het onderdeel van toekomstige releases van Az PowerShell en is de module systeemeigen beschikbaar vanuit Azure Cloud Shell.

Als u Power shell lokaal wilt gebruiken, maakt u verbinding met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="restart-the-server"></a>Start de server opnieuw

Start de server opnieuw op met de volgende opdracht:

```azurepowershell-interactive
Restart-AzMariaDbServer -Name mydemoserver -ResourceGroupName myresourcegroup
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een Azure Database for MariaDB-server maken met behulp van Power shell](quickstart-create-mariadb-server-database-using-azure-powershell.md)