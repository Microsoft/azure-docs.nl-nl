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
ms.openlocfilehash: 60c5ddcc67db6e4a0649458cfbd5c2949aa9a32a
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102202039"
---
# <a name="delete-resources-from-azure"></a>Resources verwijderen uit Azure

> [!NOTE]
>  De opties voor het verwijderen van resources in dit artikel zijn onomkeerbaar.

> [!NOTE]
>  Aangezien de enige connectiviteits modus die wordt aangeboden voor Azure Arc ingeschakelde gegevens services momenteel de modus indirect verbonden is, wordt het verwijderen van een exemplaar van Kubernetes niet verwijderd uit Azure en wordt het verwijderen van een exemplaar uit Azure niet verwijderd uit Kubernetes.  Voor het verwijderen van een resource is een tweede stap proces en dit wordt in de toekomst verbeterd.  Kubernetes is de bron van waarheid en Azure wordt bijgewerkt om deze weer spie gelen.

In sommige gevallen moet u mogelijk hand matig Azure Arc enabled Data Services-resources in Azure Resource Manager (ARM) verwijderen.  U kunt deze resources verwijderen met een van de volgende opties.

- [Resources verwijderen uit Azure](#delete-resources-from-azure)
  - [Een hele resource groep verwijderen](#delete-an-entire-resource-group)
  - [Specifieke resources in de resource groep verwijderen](#delete-specific-resources-in-the-resource-group)
  - [Resources verwijderen met de Azure CLI](#delete-resources-using-the-azure-cli)
    - [Resources van SQL Managed instance verwijderen met behulp van de Azure CLI](#delete-sql-managed-instance-resources-using-the-azure-cli)
    - [PostgreSQL grootschalige-server groeps resources verwijderen met behulp van de Azure CLI](#delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli)
    - [Resources van Azure Arc data controller verwijderen met de Azure CLI](#delete-azure-arc-data-controller-resources-using-the-azure-cli)
    - [Een resource groep verwijderen met de Azure CLI](#delete-a-resource-group-using-the-azure-cli)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="delete-an-entire-resource-group"></a>Een hele resource groep verwijderen
Als u een specifieke resource groep hebt gebruikt voor Azure Arc-gegevens Services en u *Alles* in de resource groep wilt verwijderen, kunt u de resource groep verwijderen waarmee alles erin wordt verwijderd.  

U kunt als volgt een resource groep verwijderen uit de Azure Portal:

- Blader naar de resource groep in de Azure Portal waar de Data Services-resources van Azure Arc zijn gemaakt.
- Klik op de knop **resource groep verwijderen** .
- Bevestig de verwijdering door de naam van de resource groep in te voeren en op **verwijderen** te klikken.

## <a name="delete-specific-resources-in-the-resource-group"></a>Specifieke resources in de resource groep verwijderen

U kunt specifieke Azure-Arc-gegevens services-resources in een resource groep in de Azure Portal verwijderen door het volgende te doen:

- Blader naar de resource groep in de Azure Portal waar de Data Services-resources van Azure Arc zijn gemaakt.
- Selecteer alle resources die moeten worden verwijderd.
- Klik op de knop verwijderen.
- Bevestig het verwijderen door Ja te typen en op **verwijderen** te klikken.

## <a name="delete-resources-using-the-azure-cli"></a>Resources verwijderen met de Azure CLI

U kunt specifieke Azure-Arc-gegevens services-resources verwijderen met behulp van de Azure CLI.

### <a name="delete-sql-managed-instance-resources-using-the-azure-cli"></a>Resources van SQL Managed instance verwijderen met behulp van de Azure CLI

Als u SQL Managed instance-resources wilt verwijderen uit Azure met behulp van Azure CLI, vervangt u de tijdelijke aanduidingen in de onderstaande opdracht en voert u deze uit.

```azurecli
az resource delete --name <sql instance name> --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group <resource group name>

#Example
#az resource delete --name sql1 --resource-type Microsoft.AzureArcData/sqlManagedInstances --resource-group rg1
```

### <a name="delete-postgresql-hyperscale-server-group-resources-using-the-azure-cli"></a>PostgreSQL grootschalige-server groeps resources verwijderen met behulp van de Azure CLI

Als u een PostgreSQL grootschalige-Server groep wilt verwijderen uit Azure met behulp van Azure CLI, vervangt u de tijdelijke aanduidingen in de onderstaande opdracht en voert u deze uit.

```azurecli
az resource delete --name <postgresql instance name> --resource-type Microsoft.AzureArcData/postgresInstances --resource-group <resource group name>

#Example
#az resource delete --name pg1 --resource-type Microsoft.AzureArcData/postgresInstances --resource-group rg1
```

### <a name="delete-azure-arc-data-controller-resources-using-the-azure-cli"></a>Resources van Azure Arc data controller verwijderen met de Azure CLI

> [!NOTE]
> Voordat u een Azure-Arc-gegevens controller verwijdert, moet u alle data base-instantie bronnen verwijderen die deze beheert.

Als u een Azure-Arc-gegevens controller uit Azure wilt verwijderen met behulp van Azure CLI, vervangt u de tijdelijke aanduidingen in de onderstaande opdracht en voert u deze uit.

```azurecli
az resource delete --name <data controller name> --resource-type Microsoft.AzureArcData/dataControllers --resource-group <resource group name>

#Example
#az resource delete --name dc1 --resource-type Microsoft.AzureArcData/dataControllers --resource-group rg1
```

### <a name="delete-a-resource-group-using-the-azure-cli"></a>Een resource groep verwijderen met de Azure CLI

U kunt ook de Azure CLI gebruiken om [een resource groep te verwijderen](../../azure-resource-manager/management/delete-resource-group.md).