---
title: Azure CLI gebruiken om een Azure-rol toe te wijzen voor gegevens toegang
titleSuffix: Azure Storage
description: Meer informatie over het gebruik van Azure CLI om machtigingen toe te wijzen aan een Azure Active Directory beveiligingsprincipal met Azure op rollen gebaseerd toegangs beheer (Azure RBAC). Azure Storage ondersteunt ingebouwde en aangepaste Azure-functies voor verificatie via Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/10/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: c42061520b73966f2cd516716039d78c2b9cbeb8
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375983"
---
# <a name="use-azure-cli-to-assign-an-azure-role-for-access-to-blob-and-queue-data"></a>Azure CLI gebruiken om een Azure-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens

Met Azure Active Directory (Azure AD) worden de toegangs rechten voor beveiligde bronnen geautoriseerd via [toegangs beheer op basis van rollen (Azure RBAC)](../../role-based-access-control/overview.md). Azure Storage definieert een set ingebouwde Azure-rollen die algemene sets machtigingen omvatten die worden gebruikt voor toegang tot BLOB-of wachtrij gegevens.

Wanneer een Azure-rol is toegewezen aan een Azure AD-beveiligings-principal, verleent Azure toegang tot de resources voor die beveiligings-principal. De toegang kan worden beperkt tot het niveau van het abonnement, de resource groep, het opslag account of een afzonderlijke container of wachtrij. Een beveiligings-principal voor Azure AD kan een gebruiker, een groep, een service-principal van de toepassing of een [beheerde identiteit voor Azure-resources](../../active-directory/managed-identities-azure-resources/overview.md)zijn.

In dit artikel wordt beschreven hoe u Azure CLI gebruikt om ingebouwde rollen van Azure op te geven en ze toe te wijzen aan gebruikers. Zie [azure Command-Line interface (CLI)](/cli/azure)voor meer informatie over het gebruik van Azure cli.

## <a name="azure-roles-for-blobs-and-queues"></a>Azure-rollen voor blobs en wacht rijen

[!INCLUDE [storage-auth-rbac-roles-include](../../../includes/storage-auth-rbac-roles-include.md)]

## <a name="determine-resource-scope"></a>Resource bereik bepalen

[!INCLUDE [storage-auth-resource-scope-include](../../../includes/storage-auth-resource-scope-include.md)]

## <a name="list-available-azure-roles"></a>Beschik bare Azure-rollen weer geven

Als u beschik bare ingebouwde Azure-rollen wilt weer geven met Azure CLI, gebruikt u de opdracht [AZ Role definition List](/cli/azure/role/definition#az-role-definition-list) :

```azurecli-interactive
az role definition list --out table
```

De ingebouwde Azure Storage gegevens rollen worden weer gegeven, samen met andere ingebouwde rollen voor Azure:

```Example
Storage Blob Data Contributor             Allows for read, write and delete access to Azure Storage blob containers and data
Storage Blob Data Owner                   Allows for full access to Azure Storage blob containers and data, including assigning POSIX access control.
Storage Blob Data Reader                  Allows for read access to Azure Storage blob containers and data
Storage Queue Data Contributor            Allows for read, write, and delete access to Azure Storage queues and queue messages
Storage Queue Data Message Processor      Allows for peek, receive, and delete access to Azure Storage queue messages
Storage Queue Data Message Sender         Allows for sending of Azure Storage queue messages
Storage Queue Data Reader                 Allows for read access to Azure Storage queues and queue messages
```

## <a name="assign-an-azure-role-to-a-security-principal"></a>Een Azure-rol toewijzen aan een beveiligingsprincipal

Als u een Azure-rol aan een beveiligingsprincipal wilt toewijzen, gebruikt u de opdracht [AZ Role Assignment Create](/cli/azure/role/assignment#az-role-assignment-create) . De indeling van de opdracht kan verschillen op basis van het bereik van de toewijzing. In de volgende voor beelden ziet u hoe u een rol toewijst aan een gebruiker in verschillende bereiken, maar u kunt dezelfde opdracht gebruiken om een rol toe te wijzen aan een beveiligings-principal.

> [!IMPORTANT]
> Wanneer u een Azure Storage-account maakt, worden er niet automatisch machtigingen toegewezen om toegang te krijgen tot gegevens via Azure AD. U moet uzelf expliciet een Azure RBAC-rol toewijzen voor gegevens toegang. U kunt deze toewijzen op het niveau van uw abonnement, resource groep, opslag account of container of wachtrij.
>
> Als het opslag account is vergrendeld met een Azure Resource Manager alleen-lezen vergrendeling, voor komt de vergren deling dat de toewijzing van Azure RBAC-rollen die zijn afgestemd op het opslag account of aan een gegevens container (BLOB-container of wachtrij) wordt toegewezen.

### <a name="container-scope"></a>Container bereik

Als u een Role bereik wilt toewijzen aan een container, geeft u een teken reeks op met het bereik van de container voor de `--scope` para meter. Het bereik voor een container bevindt zich in de vorm:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>
```

In het volgende voor beeld wordt de rol **Storage BLOB data Inzender** toegewezen aan een gebruiker en wordt het bereik van de container bepaald. Vervang de voorbeeld waarden en de waarden van de tijdelijke aanduiding tussen vier Kante haken door uw eigen waarden:

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/blobServices/default/containers/<container>"
```

### <a name="queue-scope"></a>Wachtrij bereik

Als u een Role bereik aan een wachtrij wilt toewijzen, geeft u een teken reeks met het bereik van de wachtrij voor de `--scope` para meter op. Het bereik voor een wachtrij bevindt zich in de vorm:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>
```

In het volgende voor beeld wordt de rol **Inzender voor opslag wachtrij gegevens** toegewezen aan een gebruiker, op het niveau van de wachtrij. Vervang de voorbeeld waarden en de waarden van de tijdelijke aanduiding tussen vier Kante haken door uw eigen waarden:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Contributor" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/queueServices/default/queues/<queue>"
```

### <a name="storage-account-scope"></a>Bereik van opslag account

Als u een rollen bereik wilt toewijzen aan het opslag account, geeft u het bereik van de bron van het opslag account op voor de `--scope` para meter. Het bereik van een opslag account bevindt zich in de vorm:

```
/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>
```

In het volgende voor beeld ziet u hoe u de rol **Storage BLOB data Reader** toewijst aan een gebruiker op het niveau van het opslag account. Zorg ervoor dat u de voorbeeld waarden vervangt door uw eigen waarden: \

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>"
```

### <a name="resource-group-scope"></a>Bereik van de resourcegroep

Als u een rollen bereik wilt toewijzen aan de resource groep, geeft u de naam van de resource groep of ID voor de `--resource-group` para meter op. In het volgende voor beeld wordt de rol van **gegevens lezer van de opslag wachtrij** toegewezen aan een gebruiker op het niveau van de resource groep. Vervang de voorbeeld waarden en de waarden van de tijdelijke aanduiding tussen vier Kante haken door uw eigen waarden:

```azurecli-interactive
az role assignment create \
    --role "Storage Queue Data Reader" \
    --assignee <email> \
    --resource-group <resource-group>
```

### <a name="subscription-scope"></a>Abonnements bereik

Als u een rollen bereik wilt toewijzen aan het abonnement, geeft u het bereik op voor het abonnement voor de `--scope` para meter. Het bereik voor een abonnement bevindt zich in de vorm:

```
/subscriptions/<subscription>
```

In het volgende voor beeld ziet u hoe u de rol **Storage BLOB data Reader** toewijst aan een gebruiker op het niveau van het opslag account. Zorg ervoor dat u de voorbeeld waarden vervangt door uw eigen waarden: 

```azurecli-interactive
az role assignment create \
    --role "Storage Blob Data Reader" \
    --assignee <email> \
    --scope "/subscriptions/<subscription>"
```

## <a name="next-steps"></a>Volgende stappen

- [Azure-roltoewijzingen toevoegen of verwijderen met de module Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md)
- [De Azure PowerShell-module gebruiken om een Azure-rol toe te wijzen voor toegang tot Blob-en wachtrij gegevens](storage-auth-aad-rbac-powershell.md)
- [Azure Portal gebruiken om een Azure-rol toe te wijzen voor toegang tot blob- en wachtrijgegevens](storage-auth-aad-rbac-portal.md)
