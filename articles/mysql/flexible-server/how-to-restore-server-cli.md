---
title: Een Azure Database for MySQL herstellen - Flexible Server met Azure CLI
description: In dit artikel wordt beschreven hoe u herstelbewerkingen in Azure Database for MySQL via de Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/01/2021
ms.openlocfilehash: 61a8439b770d8909e04d1b80a5219810dc72aa34
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509066"
---
# <a name="point-in-time-restore-of-a-azure-database-for-mysql---flexible-server-with-azure-cli"></a>Herstel naar een bepaald tijdstip van een Azure Database for MySQL - Flexible Server met Azure CLI


> [!IMPORTANT]
> Azure Database for MySQL - Flexible Server is momenteel beschikbaar als openbare preview.

Dit artikel bevat stapsgewijs de procedure voor het uitvoeren van herstel naar een bepaald tijdstip op een flexibele server met behulp van back-ups.

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

## <a name="restore-a-server-from-backup-to-a-new-server"></a>Een server herstellen van een back-up naar een nieuwe server

U kunt de volgende opdracht uitvoeren om een server te herstellen naar een vroegste bestaande back-up.

**Gebruik**
```azurecli
az mysql flexible-server restore --restore-time
                                 --source-server
                                 [--ids]
                                 [--location]
                                 [--name]
                                 [--no-wait]
                                 [--resource-group]
                                 [--subscription]
```

**Voorbeeld:** Een server herstellen vanuit deze ```2021-03-03T13:10:00Z``` back-upmomentopname.

```azurecli
az mysql server restore \
--name mydemoserver-restored \
--resource-group myresourcegroup \
--restore-point-in-time "2021-03-03T13:10:00Z" \
--source-server mydemoserver
```
De tijd die nodig is om te herstellen, is afhankelijk van de grootte van de gegevens die zijn opgeslagen op de server.

## <a name="perform-post-restore-tasks"></a>Taken na herstel uitvoeren
Nadat het herstellen is voltooid, moet u de volgende taken uitvoeren om uw gebruikers en toepassingen weer actief te maken:

- Als de nieuwe server is bedoeld om de oorspronkelijke server te vervangen, moet u clients en clienttoepassingen omleiden naar de nieuwe server
- Zorg ervoor dat er geschikte VNet-regels zijn om gebruikers verbinding te laten maken. Deze regels worden niet gekopieerd van de oorspronkelijke server.
- Zorg ervoor dat de juiste aanmeldingen en machtigingen op databaseniveau zijn gebruikt
- Waarschuwingen configureren die geschikt zijn voor de zojuist herstelde server

## <a name="next-steps"></a>Volgende stappen
Meer informatie over [bedrijfscontinuïteit](concepts-business-continuity.md)

