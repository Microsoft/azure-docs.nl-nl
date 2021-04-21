---
title: Azure CLI gebruiken om een Azure-rol toe te wijzen voor gegevenstoegang
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van Azure CLI om machtigingen toe te wijzen aan een Azure Active Directory beveiligingsprincipaal met op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC). Azure Storage ondersteunt ingebouwde en aangepaste Azure-rollen voor verificatie via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 93d9a81ab75efe4ea38189a7280e041e545a2b7d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785321"
---
# <a name="use-azure-cli-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Azure CLI gebruiken om een Azure-rol toe te wijzen voor toegang tot blob- en wachtrijgegevens

Azure Active Directory (Azure AD) autoreert toegangsrechten voor beveiligde resources via op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC).](../../role-based-access-control/overview.md) Azure Storage definieert een set ingebouwde Azure-rollen die algemene sets machtigingen omvatten die worden gebruikt voor toegang tot blob- of wachtrijgegevens.

Wanneer een Azure-rol wordt toegewezen aan een Azure AD-beveiligingsprincipaal, verleent Azure toegang tot deze resources voor die beveiligingsprincipaal. Toegang kan worden beperkt tot het niveau van het abonnement, de resourcegroep, het opslagaccount of een afzonderlijke container of wachtrij. Een Azure AD-beveiligingsprincipaal kan een gebruiker, een groep, een toepassingsservice-principal of een beheerde identiteit voor [Azure-resources zijn.](../../active-directory/managed-identities-azure-resources/overview.md)

In dit artikel wordt beschreven hoe u Azure CLI gebruikt om ingebouwde Azure-rollen weer te geven en toe te wijzen aan gebruikers. Zie Azure Command-Line [Interface (CLI) voor](/cli/azure)meer informatie over het gebruik van Azure CLI.

## <a name="azure-roles-for-blobs-and-queues"></a>Azure-rollen voor blobs en wachtrijen

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Resourcebereik bepalen

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>Beschikbare Azure-rollen op een lijst zetten

Gebruik de opdracht [az role definition list](/cli/azure/role/definition#az_role_definition_list) om beschikbare ingebouwde Azure-rollen weer te geven met Azure CLI:

```azurecli-interactive
az role definition list --out table
```

U ziet de ingebouwde Azure Storage gegevensrollen, samen met andere ingebouwde rollen voor Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-azure-role-to-a-security-principal"></a>Een Azure-rol toewijzen aan een beveiligingsprincipaal

Als u een Azure-rol wilt toewijzen aan een beveiligingsprincipaal, gebruikt [u de opdracht az role assignment create.](/cli/azure/role/assignment#az_role_assignment_create) De indeling van de opdracht kan verschillen op basis van het bereik van de toewijzing. In de volgende voorbeelden ziet u hoe u een rol toewijst aan een gebruiker in verschillende scopes, maar u kunt dezelfde opdracht gebruiken om een rol toe te wijzen aan een beveiligingsprincipaal.

> [!IMPORTANT]
> Wanneer u een Azure Storage account maakt, krijgt u niet automatisch machtigingen voor toegang tot gegevens via Azure AD. U moet uzelf expliciet een Azure RBAC-rol toewijzen voor gegevenstoegang. U kunt deze toewijzen op het niveau van uw abonnement, resourcegroep, opslagaccount, container of wachtrij.
>
> Als het opslagaccount is vergrendeld met een Azure Resource Manager alleen-lezenvergrendeling, voorkomt de vergrendeling de toewijzing van Azure RBAC-rollen die zijn beperkt tot het opslagaccount of aan een gegevenscontainer (blobcontainer of wachtrij).

### <a name="container-scope"></a>Containerbereik

Als u een rol wilt toewijzen die is toegewezen aan een container, geeft u een tekenreeks op die het bereik van de container voor de `--scope` parameter bevat. Het bereik voor een container heeft de volgende vorm:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

In het volgende voorbeeld wordt de rol Bijdrager voor **opslagblobgegevens** toegewezen aan een gebruiker, binnen het bereik van het niveau van de container. Zorg ervoor dat u de voorbeeldwaarden en de tijdelijke aanduidingen tussen haakjes vervangt door uw eigen waarden:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>Wachtrijbereik

Als u een rol wilt toewijzen die is toegewezen aan een wachtrij, geeft u een tekenreeks op die het bereik van de wachtrij voor de `--scope` parameter bevat. Het bereik voor een wachtrij heeft de volgende vorm:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

In het volgende voorbeeld wordt de rol Inzender voor **opslagwachtrijgegevens** toegewezen aan een gebruiker, met een bereik dat is beperkt tot het niveau van de wachtrij. Zorg ervoor dat u de voorbeeldwaarden en de tijdelijke aanduidingen tussen haakjes vervangt door uw eigen waarden:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>Bereik van opslagaccount

Als u een rol wilt toewijzen die is toegewezen aan het opslagaccount, geeft u het bereik van de opslagaccountresource voor de `--scope` parameter op. Het bereik voor een opslagaccount heeft de volgende vorm:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

In het volgende voorbeeld ziet u hoe u de rol Lezer van **opslagblobgegevens** toewijst aan een gebruiker op het niveau van het opslagaccount. Zorg ervoor dat u de voorbeeldwaarden vervangt door uw eigen waarden: \

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Bereik van de resourcegroep

Als u een rol wilt toewijzen die is toegewezen aan de resourcegroep, geeft u de naam of id van de resourcegroep voor de `--resource-group` parameter op. In het volgende voorbeeld wordt de rol Gegevenslezer voor **opslagwachtrijen** toegewezen aan een gebruiker op het niveau van de resourcegroep. Zorg ervoor dat u de voorbeeldwaarden en tijdelijke aanduidingen tussen vierkante haken vervangt door uw eigen waarden:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>Abonnementsbereik

Als u een rol wilt toewijzen die is toegewezen aan het abonnement, geeft u het bereik op voor het abonnement voor de `--scope` parameter . Het bereik voor een abonnement heeft de volgende vorm:

```
/subscriptions/<subscription>
```

In het volgende voorbeeld ziet u hoe u de rol Lezer voor **opslagblobgegevens** toewijst aan een gebruiker op het niveau van het opslagaccount. Zorg ervoor dat u de voorbeeldwaarden vervangt door uw eigen waarden: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Volgende stappen

- [Azure-roltoewijzingen toevoegen of verwijderen met behulp van Azure PowerShell module](../../role-based-access-control/role-assignments-powershell.md)
- [Gebruik de Azure PowerShell om een Azure-rol toe te wijzen voor toegang tot blob- en wachtrijgegevens](storage-auth-aad-rbac-powershell.md)
- [Azure Portal gebruiken om een Azure-rol toe te wijzen voor toegang tot blob- en wachtrijgegevens](storage-auth-aad-rbac-portal.md)
