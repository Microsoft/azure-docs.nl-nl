---
title: Zone-redundante hoge beschikbaarheid beheren - Azure CLI - Azure Database for MySQL Flexible Server
description: In dit artikel wordt beschreven hoe u zone-redundante hoge beschikbaarheid configureert in Azure Database for MySQL flexibele server met de Azure CLI.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 04/1/2021
ms.custom: references_regions
ms.openlocfilehash: fb53ad309c741fc898bcf3e27347038c0e382ea4
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107509089"
---
# <a name="manage-zone-redundant-high-availability-in-azure-database-for-mysql-flexible-server-with-azure-cli"></a>Zone-redundante hoge beschikbaarheid beheren in Azure Database for MySQL Flexible Server met Azure CLI

> [!NOTE]
> Azure Database for MySQL Flexible Server is in openbare preview. 

In dit artikel wordt beschreven hoe u zone-redundante configuratie met hoge beschikbaarheid kunt in- of uitschakelen op het moment dat de server op uw flexibele server wordt gemaakt. U kunt zone-redundante hoge beschikbaarheid ook uitschakelen nadat de server is gemaakt. Het inschakelen van zone-redundante hoge beschikbaarheid na het maken van de server wordt niet ondersteund.

De functie voor hoge beschikbaarheid biedt fysiek afzonderlijke primaire en stand-byreplica's in verschillende zones. Zie de documentatie over concepten [voor hoge beschikbaarheid voor meer informatie.](./concepts/../concepts-high-availability.md) Het in- of uitschakelen van hoge beschikbaarheid verandert niet uw andere instellingen, waaronder VNET-configuratie, firewallinstellingen en back-upretentie. Het uitschakelen van hoge beschikbaarheid heeft geen invloed op de connectiviteit en bewerkingen van uw toepassing.

> [!IMPORTANT]
> Zone-redundante hoge beschikbaarheid is beschikbaar in een beperkte set regio's. Bekijk hier de ondersteunde [regio's.](https://docs.microsoft.com/azure/mysql/flexible-server/overview#azure-regions) 

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

## <a name="enable-high-availability-during-server-creation"></a>Hoge beschikbaarheid inschakelen tijdens het maken van de server
U kunt alleen een server maken met de prijscategorie Algemeen gebruik of Geoptimaliseerd voor geheugen met hoge beschikbaarheid. U kunt hoge beschikbaarheid voor een server alleen tijdens het maken inschakelen.

**Gebruik:**

```azurecli
az mysql flexible-server create [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**Voorbeeld:**
```azurecli
az mysql flexible-server create --name myservername --sku-name Standard-D2ds_v4 --resource-group myresourcegroup --high-availability Enabled
```

## <a name="disable-high-availability"></a>Hoge beschikbaarheid uitschakelen

U kunt hoge beschikbaarheid uitschakelen met behulp van [de opdracht az mysql flexible-server update.](/cli/azure/mysql/flexible-server#az_mysql_flexible_server_update) Houd er rekening mee dat het uitschakelen van hoge beschikbaarheid alleen wordt ondersteund als de server is gemaakt met hoge beschikbaarheid. 

```azurecli
az mysql flexible-server update [--high-availability {Disabled, Enabled}]
                                [--resource-group]
                                [--name]
```

**Voorbeeld:**
```azurecli
az mysql flexible-server update --resource-group myresourcegroup --name myservername --high-availability Disabled
```


## <a name="next-steps"></a>Volgende stappen

-   Meer informatie over [bedrijfscontinuïteit](./concepts-business-continuity.md)
-   Meer informatie over [zone-redundante hoge beschikbaarheid](./concepts-high-availability.md)
