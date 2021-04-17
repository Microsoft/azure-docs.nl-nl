---
title: Opnieuw opstarten/stoppen/starten - Azure Portal - Azure Database for MySQL Flexible Server
description: In dit artikel wordt beschreven hoe u bewerkingen opnieuw opstart/stopt/start in Azure Database for MySQL via de Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 03/30/2021
ms.openlocfilehash: c9e646ad40c669e51f052ac9888cdd7c6883a848
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509069"
---
# <a name="restartstopstart-an-azure-database-for-mysql---flexible-server-preview"></a>Opnieuw opstarten/stoppen/starten van een Azure Database for MySQL - Flexible Server (preview)

> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

In dit artikel wordt beschreven hoe u een flexibele server opnieuw opstart, start en stopt met behulp van Azure CLI.

## <a name="prerequisites"></a>Vereisten
- Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
- Azure CLI installeren of upgraden naar de nieuwste versie. Zie [Azure CLI installeren](/cli/azure/install-azure-cli).
-  Meld u aan bij het Azure-account [met de opdracht az login.](/cli/azure/reference-index#az-login) Let op de eigenschap **id**, die verwijst naar **abonnements-id** voor uw Azure-account.

    ```azurecli-interactive
    az login
    ````

- Als u meerdere abonnementen hebt, kiest u het juiste abonnement waarin u de server wilt maken met behulp van de ```az account set``` opdracht .
`
    ```azurecli
    az account set --subscription <subscription id>
    ```

- Maak een MySQL Flexible Server als u er nog geen hebt gemaakt met de ```az mysql flexible-server create``` opdracht .

    ```azurecli
    az mysql flexible-server create --resource-group myresourcegroup --name myservername
    ```

## <a name="stop-a-running-server"></a>Een server stoppen die wordt uitgevoerd
Voer de opdracht uit om een server te  ```az mysql flexible-server stop``` stoppen. Als u lokale [context gebruikt,](/cli/azure/config/param-persist)hoeft u geen argumenten op te geven.

**Gebruik:**
```azurecli
az mysql flexible-server stop  [--name]
                               [--resource-group]
                               [--subscription]
```

**Voorbeeld zonder lokale context:**
```azurecli
az mysql flexible-server stop  --resource-group --name myservername
```

**Voorbeeld met lokale context:**
```azurecli
az mysql flexible-server stop
```

## <a name="start-a-stopped-server"></a>Een gestopte server starten
Voer de opdracht uit om een server te  ```az mysql flexible-server start``` starten. Als u lokale [context gebruikt,](/cli/azure/config/param-persist)hoeft u geen argumenten op te geven.

**Gebruik:**
```azurecli
az mysql flexible-server start [--name]
                               [--resource-group]
                               [--subscription]
```

**Voorbeeld zonder lokale context:**
```azurecli
az mysql flexible-server start  --resource-group --name myservername
```

**Voorbeeld met lokale context:**
```azurecli
az mysql flexible-server start
```

> [!IMPORTANT]
> Zodra de server opnieuw is opgestart, zijn alle beheerbewerkingen nu beschikbaar voor de flexibele server.

## <a name="restart-a-server"></a>Een server opnieuw opstarten
Voer de opdracht uit om een server opnieuw op te  ```az mysql flexible-server restart``` starten. Als u lokale [context gebruikt,](/cli/azure/config/param-persist)hoeft u geen argumenten op te geven.

**Gebruik:**
```azurecli
az mysql flexible-server restart [--name]
                                 [--resource-group]
                                 [--subscription]
```

**Voorbeeld zonder lokale context:**
```azurecli
az mysql flexible-server restart  --resource-group --name myservername
```

**Voorbeeld met lokale context:**
```azurecli
az mysql flexible-server restart
```


> [!IMPORTANT]
> Zodra de server opnieuw is opgestart, zijn alle beheerbewerkingen nu beschikbaar voor de flexibele server.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [netwerken in Azure Database for MySQL Flexible Server](./concepts-networking.md)
- [Virtueel netwerk met Azure Database for MySQL Flexible Server maken met behulp van de Azure-portal](./how-to-manage-virtual-network-portal.md).

