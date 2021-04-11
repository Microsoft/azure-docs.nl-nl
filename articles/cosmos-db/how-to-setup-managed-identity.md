---
title: Beheerde identiteiten met Azure AD configureren voor uw Azure Cosmos DB-account
description: Meer informatie over het configureren van beheerde identiteiten met Azure Active Directory voor uw Azure Cosmos DB-account
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/02/2021
ms.author: thweiss
ms.openlocfilehash: 30efaed09a400611861bdd3adeae1f650054b405
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231426"
---
# <a name="configure-managed-identities-with-azure-active-directory-for-your-azure-cosmos-db-account"></a>Beheerde identiteiten met Azure Active Directory voor uw Azure Cosmos DB account configureren
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory. In dit artikel wordt beschreven hoe u een beheerde identiteit voor Azure Cosmos DB-accounts maakt.

> [!NOTE]
> Alleen door het systeem toegewezen beheerde identiteiten worden momenteel ondersteund door Azure Cosmos DB.

## <a name="prerequisites"></a>Vereisten

- Zie [Wat zijn beheerde identiteiten voor Azure-resources?](../active-directory/managed-identities-azure-resources/overview.md) als u niet bekend met beheerde identiteiten voor Azure-resources. Zie [beheerde identiteits typen](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)voor meer informatie over beheerde identiteits typen.
- Voor het instellen van beheerde identiteiten moet uw account de [rol Inzender](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)voor het DocumentDB-account hebben.

## <a name="add-a-system-assigned-identity"></a>Een door het systeem toegewezen identiteit toevoegen

### <a name="using-an-azure-resource-manager-arm-template"></a>Een Azure Resource Manager-sjabloon (ARM) gebruiken

> [!IMPORTANT]
> Zorg ervoor dat u een `apiVersion` `2021-03-15` of meer gebruikt bij het werken met beheerde identiteiten.

Als u een door het systeem toegewezen identiteit wilt inschakelen voor een nieuw of bestaand Azure Cosmos DB account, neemt u de volgende eigenschap op in de resource definitie:

```json
"identity": {
    "type": "SystemAssigned"
}
```

De `resources` sectie van uw arm-sjabloon moet er dan als volgt uitzien:

```json
"resources": [
    {
        "type": " Microsoft.DocumentDB/databaseAccounts",
        "identity": {
            "type": "SystemAssigned"
        },
        // ...
    },
    // ...
 ]
```

Nadat uw Azure Cosmos DB-account is gemaakt of bijgewerkt, wordt de volgende eigenschap weer gegeven:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<azure-ad-tenant-id>",
    "principalId": "<azure-ad-principal-id>"
}
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)
- Meer informatie over [door de klant beheerde sleutels op Azure Cosmos DB](how-to-setup-cmk.md)
