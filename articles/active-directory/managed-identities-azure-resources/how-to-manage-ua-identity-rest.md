---
title: Door de gebruiker toegewezen beheerde identiteiten beheren met REST-Azure AD
description: Stapsgewijze instructies voor het maken, weer geven en verwijderen van een door de gebruiker toegewezen beheerde identiteit om REST API aanroepen uit te voeren.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/02/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 117f9a1c173f2083dd4621f4f3f41b6e83d1d46b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96546689"
---
# <a name="create-list-or-delete-a-user-assigned-managed-identity-using-rest-api-calls"></a>Een door de gebruiker toegewezen beheerde identiteit maken, weer geven of verwijderen met REST API-aanroepen

Beheerde identiteiten voor Azure-resources bieden Azure-Services de mogelijkheid om te verifiëren bij services die ondersteuning bieden voor Azure AD-verificatie, zonder dat er referenties in uw code nodig zijn. 

In dit artikel leert u hoe u een door de gebruiker toegewezen beheerde identiteit kunt maken, weer geven en verwijderen met behulp van krul om REST API aanroepen te maken.

## <a name="prerequisites"></a>Vereisten

- Als u niet bekend bent met beheerde identiteiten voor Azure-resources, raadpleegt u de sectie [Overzicht](overview.md). **Let op dat u nagaat wat het [verschil is tussen een door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteit](overview.md#managed-identity-types)** .
- Als u nog geen Azure-account hebt, [registreer u dan voor een gratis account](https://azure.microsoft.com/free/) voordat u verdergaat.
- U kunt alle opdrachten in dit artikel in de cloud of lokaal uitvoeren:
    - Gebruik de [Azure Cloud Shell](../../cloud-shell/overview.md) om uit te voeren in de cloud.
    - Als u lokaal wilt uitvoeren, installeert u [krul](https://curl.haxx.se/download.html) en de [Azure cli](/cli/azure/install-azure-cli).

## <a name="obtain-a-bearer-access-token"></a>Een Bearer-toegangs token verkrijgen

1. Als u lokaal uitvoert, meldt u zich aan bij Azure via de Azure CLI:

    ```
    az login
    ```

1. Een toegangs token verkrijgen met [AZ account Get-access-token](/cli/azure/account#az_account_get_access_token)

    ```azurecli-interactive
    az account get-access-token
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit maken 

Als u een door de gebruiker toegewezen beheerde identiteit wilt maken, moet uw account beschikken over de rol toewijzing [beheerde identiteits bijdrage](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**Aanvraagheaders**

|Aanvraagheader  |Beschrijving  |
|---------|---------|
|*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
|*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken.        |

**Aanvraagbody**

|Naam  |Beschrijving  |
|---------|---------|
|location     | Vereist. Resource locatie.        |

## <a name="list-user-assigned-managed-identities"></a>Door de gebruiker toegewezen beheerde identiteiten weer geven

Als u een door de gebruiker toegewezen beheerde identiteit wilt weer geven/lezen, moet uw account beschikken over de rol [Managed Identity](../../role-based-access-control/built-in-roles.md#managed-identity-operator) of [Managed id contributor](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Aanvraagheader  |Beschrijving  |
|---------|---------|
|*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
|*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Een door de gebruiker toegewezen beheerde identiteit verwijderen

Als u een door de gebruiker toegewezen beheerde identiteit wilt verwijderen, moet uw account beschikken over de rol toewijzing [beheerde identiteits bijdrage](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) .

> [!NOTE]
> Als u een door de gebruiker toegewezen beheerde identiteit verwijdert, wordt de verwijzing niet verwijderd uit een resource waaraan deze is toegewezen. Een door de gebruiker toegewezen beheerde identiteit verwijderen uit een virtuele machine met behulp van krul Zie [een door de gebruiker toegewezen identiteit verwijderen uit een Azure-VM](qs-configure-rest-vm.md#remove-a-user-assigned-managed-identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Aanvraagheader  |Beschrijving  |
|---------|---------|
|*Content-Type*     | Vereist. Ingesteld op `application/json`.        |
|*Autorisatie*     | Vereist. Ingesteld op een geldig `Bearer`-toegangstoken.        |

## <a name="next-steps"></a>Volgende stappen

Voor informatie over het toewijzen van een door de gebruiker toegewezen beheerde identiteit aan een Azure VM-VMSS met behulp van krul, [configureert u beheerde identiteiten voor Azure-resources op een Azure-VM met behulp van rest API aanroepen](qs-configure-rest-vm.md#user-assigned-managed-identity) en [Configureer beheerde identiteiten voor Azure-resources op een schaalset voor virtuele machines met behulp van rest API-aanroepen](qs-configure-rest-vmss.md#user-assigned-managed-identity).
