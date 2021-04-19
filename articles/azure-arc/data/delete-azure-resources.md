---
title: Resources verwijderen uit Azure
description: Resources verwijderen uit Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: ce46b7afe7344fabde03805dc2a0977411be5811
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107716075"
---
# <a name="delete-resources-from-azure"></a>Resources verwijderen uit Azure

In dit artikel wordt beschreven hoe u resources uit Azure verwijdert.

> [!WARNING]
> Wanneer u resources verwijdert zoals beschreven in dit artikel, kunnen deze acties niet ongedaan worden maken.

Als u in de modus voor indirect verbinden een exemplaar uit Kubernetes verwijdert, wordt het niet uit Azure verwijderd en wordt het exemplaar niet uit Kubernetes verwijderd. Voor de modus voor indirect verbinden is het verwijderen van een resource een proces in twee stappen. Dit wordt in de toekomst verbeterd. Kubernetes wordt de bron van waarheid en de portal wordt bijgewerkt om deze weer te geven.

In sommige gevallen moet u de resources in Azure Azure Arc-gegevensservices verwijderen.  U kunt deze resources verwijderen met behulp van een van de volgende opties.

- [Resources verwijderen uit Azure](#delete-resources-from-azure)
  - [Een volledige resourcegroep verwijderen](#delete-an-entire-resource-group)
  - [Specifieke resources in de resourcegroep verwijderen](#delete-specific-resources-in-the-resource-group)
  - [Resources verwijderen met de Azure CLI](#delete-resources-using-the-azure-cli)
    - [Sql Managed Instance-resources verwijderen met behulp van de Azure CLI](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [Resources voor PostgreSQL Hyperscale-servergroep verwijderen met behulp van de Azure CLI](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [Resources Azure Arc gegevenscontroller verwijderen met behulp van de Azure CLI](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [Een resourcegroep verwijderen met behulp van de Azure CLI](#delete-a-resource-group-using-the-azure-cli)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-an-entire-resource-group"></a>Een volledige resourcegroep verwijderen

Als u een specifieke en toegewezen resourcegroep voor Azure Arc-gegevensservices hebt  gebruikt en u alles in de resourcegroep wilt verwijderen, kunt u de resourcegroep verwijderen, waardoor alles erin wordt verwijderd.  

U kunt een resourcegroep in de Azure Portal als volgt verwijderen:

- Blader naar de resourcegroep in de Azure Portal waar de Azure Arc-gegevensservices zijn gemaakt.
- Klik op **de knop Resourcegroep** verwijderen.
- Bevestig de verwijdering door de naam van de resourcegroep in te geven en op **Verwijderen te klikken.**

## <a name="delete-specific-resources-in-the-resource-group"></a>Specifieke resources in de resourcegroep verwijderen

U kunt specifieke Azure Arc-gegevensservices resources in een resourcegroep in de Azure Portal als volgt te doen:

- Blader naar de resourcegroep in de Azure Portal waar de Azure Arc-gegevensservices zijn gemaakt.
- Selecteer alle resources die moeten worden verwijderd.
- Klik op de knop Verwijderen.
- Bevestig de verwijdering door Ja te typen en op **Verwijderen te klikken.**

## <a name="delete-resources-using-the-azure-cli"></a>Resources verwijderen met behulp van de Azure CLI

U kunt specifieke Azure Arc-gegevensservices verwijderen met behulp van de Azure CLI.

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>Resources van SQL Managed Instance verwijderen met behulp van de Azure CLI

Als u sql managed instance-resources uit Azure wilt verwijderen met behulp van de Azure CLI, vervangt u de tijdelijke aanduidingswaarden in de onderstaande opdracht en voer u deze uit.

```azurecli
az resource delete --name <sql instance name> --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>PostgreSQL Hyperscale-servergroepresources verwijderen met behulp van de Azure CLI

Als u een PostgreSQL Hyperscale-servergroepresource uit Azure wilt verwijderen met behulp van de Azure CLI, vervangt u de tijdelijke aanduidingswaarden in de onderstaande opdracht en voer deze uit.

```azurecli
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureArcData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureArcData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>Resources Azure Arc gegevenscontroller verwijderen met behulp van de Azure CLI

> [!NOTE]
> Voordat u een Azure Arc verwijdert, moet u alle resources van het database-exemplaar verwijderen die door de controller worden beheert.

Als u een Azure Arc-gegevenscontroller uit Azure wilt verwijderen met behulp van de Azure CLI, vervangt u de tijdelijke aanduidingswaarden in de onderstaande opdracht en voer u deze uit.

```azurecli
az resource delete --name <data controller name> --resource-type Microsoft.AzureArcData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureArcData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>Een resourcegroep verwijderen met behulp van de Azure CLI

U kunt ook de Azure CLI gebruiken om een [resourcegroep te verwijderen.](../../azure-resource-manager/management/delete-resource-group.md)