---
title: Azure-rollen toewijzen met behulp van Azure CLI-Azure RBAC
description: Meer informatie over het verlenen van toegang tot Azure-resources voor gebruikers, groepen, service-principals of beheerde identiteiten met behulp van Azure CLI en Azure op rollen gebaseerd toegangs beheer (Azure RBAC).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/28/2020
ms.author: rolyon
ms.custom: contperf-fy21q1, devx-track-azurecli
ms.openlocfilehash: ee356f32b6799c6182ec1c9e061a35271a4bbc23
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100556980"
---
# <a name="assign-azure-roles-using-azure-cli"></a>Azure-rollen toewijzen met behulp van Azure CLI

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] In dit artikel wordt beschreven hoe u rollen toewijst met behulp van Azure CLI.

## <a name="prerequisites"></a>Vereisten

Als u rollen wilt toewijzen, hebt u het volgende nodig:

- `Microsoft.Authorization/roleAssignments/write`machtigingen, zoals beheerder of [eigenaar](built-in-roles.md#owner) van [gebruikers toegang](built-in-roles.md#user-access-administrator)
- [Bash in azure Cloud shell](../cloud-shell/overview.md) of [Azure cli](/cli/azure)

## <a name="steps-to-assign-an-azure-role"></a>Stappen voor het toewijzen van een Azure-rol

Als u een rol wilt toewijzen, bestaat uit drie elementen: beveiligingsprincipal, roldefinitie en bereik.

### <a name="step-1-determine-who-needs-access"></a>Stap 1: bepalen wie toegang moet hebben

U kunt een rol toewijzen aan een gebruiker, groep, Service-Principal of beheerde identiteit. Als u een rol wilt toewijzen, moet u mogelijk de unieke ID van het object opgeven. De ID heeft de volgende indeling: `11111111-1111-1111-1111-111111111111` . U kunt de ID ophalen met behulp van de Azure Portal of Azure CLI.

**Gebruiker**

Voor een Azure AD-gebruiker haalt u de user principal name op, zoals *patlong \@ contoso.com* of de gebruikers object-id. Als u de object-ID wilt ophalen, kunt u [AZ AD User show](/cli/azure/ad/user#az_ad_user_show)gebruiken.

```azurecli
az ad user show --id "{principalName}" --query "objectId" --output tsv
```

**Groep**

Voor een Azure AD-groep hebt u de object-ID van de groep nodig. Als u de object-ID wilt ophalen, kunt u [AZ Ad Group show](/cli/azure/ad/group#az_ad_group_show) of [AZ Ad Group List](/cli/azure/ad/group#az_ad_group_list)gebruiken.

```azurecli
az ad group show --group "{groupName}" --query "objectId" --output tsv
```

**Service-principal**

Voor een Azure AD-Service-Principal (identiteit die wordt gebruikt door een toepassing) hebt u de Service-Principal object-ID nodig. Als u de object-ID wilt ophalen, kunt u [AZ AD SP List](/cli/azure/ad/sp#az_ad_sp_list)gebruiken. Gebruik voor een service-principal de object-ID en **niet** de toepassings-id.

```azurecli
az ad sp list --all --query "[].{displayName:displayName, objectId:objectId}" --output tsv
az ad sp list --display-name "{displayName}"
```

**Beheerde identiteit**

Voor een door het systeem toegewezen of een door de gebruiker toegewezen beheerde identiteit hebt u de object-ID nodig. Als u de object-ID wilt ophalen, kunt u [AZ AD SP List](/cli/azure/ad/sp#az_ad_sp_list)gebruiken.

```azurecli
az ad sp list --all --filter "servicePrincipalType eq 'ManagedIdentity'"
```

Als u alleen de door de gebruiker toegewezen beheerde identiteiten wilt weer geven, kunt u [AZ ID List](/cli/azure/identity#az_identity_list)gebruiken.

```azurecli
az identity list
```
    
### <a name="step-2-select-the-appropriate-role"></a>Stap 2: Selecteer de juiste rol

Machtigingen worden samen in rollen gegroepeerd. U kunt kiezen uit een lijst met verschillende [ingebouwde rollen van Azure](built-in-roles.md) of u kunt uw eigen aangepaste rollen gebruiken. Het is een best practice om toegang te verlenen met de mini maal benodigde bevoegdheden, dus vermijd het toewijzen van een bredere rol.

U kunt de [lijst met AZ Role definition](/cli/azure/role/definition#az_role_definition_list)gebruiken om de rollen weer te geven en de unieke rol-id op te halen.

```azurecli
az role definition list --query "[].{name:name, roleType:roleType, roleName:roleName}" --output tsv
```

U kunt als volgt de details van een bepaalde rol weer geven.

```azurecli
az role definition list --name "{roleName}"
```

Zie [Azure Role-definities weer](role-definitions-list.md#azure-cli)geven voor meer informatie.
 
### <a name="step-3-identify-the-needed-scope"></a>Stap 3: het benodigde bereik identificeren

Azure biedt vier niveaus van bereik: resource, [resource groep](../azure-resource-manager/management/overview.md#resource-groups), abonnement en [beheer groep](../governance/management-groups/overview.md). Het is een best practice om toegang te verlenen met de mini maal benodigde bevoegdheden, dus vermijd het toewijzen van een rol in een breder bereik. Zie [Bereik](scope-overview.md) voor meer informatie over het bereik.

**Resourcebereik**

Voor resource bereik hebt u de resource-ID voor de resource nodig. U kunt de resource-ID vinden door te kijken naar de eigenschappen van de resource in de Azure Portal. Een resource-ID heeft de volgende indeling.

```
/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}
```

**Bereik van de resourcegroep**

Voor het bereik van de resource groep hebt u de naam van de resource groep nodig. U vindt de naam op de pagina **resource groepen** in de Azure portal of u kunt de [lijst AZ Group](/cli/azure/group#az_group_list)gebruiken.

```azurecli
az group list --query "[].{name:name}" --output tsv
```

**Abonnements bereik** 

Voor het abonnements bereik hebt u de abonnements-ID nodig. U kunt de ID vinden op de pagina **abonnementen** in het Azure portal of u kunt [AZ account list](/cli/azure/account#az_account_list)gebruiken.

```azurecli
az account list --query "[].{name:name, id:id}" --output tsv
```

**Beheer groeps bereik** 

Voor beheer groeps bereik hebt u de naam van de beheer groep nodig. U kunt de naam vinden op de pagina **beheer groepen** in de Azure portal of u kunt [AZ Account Management-Group List](/cli/azure/account/management-group#az_account_management_group_list)gebruiken.

```azurecli
az account management-group list --query "[].{name:name, id:id}" --output tsv
```
    
### <a name="step-4-assign-role"></a>Stap 4: rol toewijzen

Als u een rol wilt toewijzen, gebruikt u de opdracht [AZ Role Assignment Create](/cli/azure/role/assignment#az_role_assignment_create) . Afhankelijk van het bereik heeft de opdracht meestal een van de volgende notaties.

**Resourcebereik**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/{providerName}/{resourceType}/{resourceSubType}/{resourceName}"
```

**Bereik van de resourcegroep**

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}"
```

**Abonnements bereik** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--subscription "{subscriptionNameOrId}"
```

**Beheer groeps bereik** 

```azurecli
az role assignment create --assignee "{assignee}" \
--role "{roleNameOrId}" \
--scope "/providers/Microsoft.Management/managementGroups/{managementGroupName}"
``` 

Hieronder ziet u een voor beeld van de uitvoer wanneer u de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) toewijst aan een gebruiker in een bereik van een resource groep.

```azurecli
{
  "canDelegate": null,
  "id": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Authorization/roleAssignments/{roleAssignmentId}",
  "name": "{roleAssignmentId}",
  "principalId": "{principalId}",
  "principalType": "User",
  "resourceGroup": "{resourceGroupName}",
  "roleDefinitionId": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/9980e02c-c2be-4d73-94e8-173b1dc7cf3c",
  "scope": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}",
  "type": "Microsoft.Authorization/roleAssignments"
}
```
    
## <a name="assign-role-examples"></a>Voor beelden van rollen toewijzen

#### <a name="assign-a-role-for-all-blob-containers-in-a-storage-account-resource-scope"></a>Een rol toewijzen voor alle BLOB-containers in een bron bereik van een opslag account

Hiermee wijst u de rol van de BLOB voor het decoderen van gegevens over de [opslag](built-in-roles.md#storage-blob-data-contributor) van een opslag account met de naam *storage12345* toe aan een service-principal met object-id *55555555-5555-5555-5555-555555555555* .

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345"
```

#### <a name="assign-a-role-for-a-specific-blob-container-resource-scope"></a>Een rol toewijzen voor een specifiek BLOB-container resource bereik

Hiermee wijst u de rol van [BLOB voor gegevens opslag](built-in-roles.md#storage-blob-data-contributor) toe aan een service-principal met object-id *55555555-5555-5555-5555-555555555555* in een resource bereik voor een BLOB-container met de naam *BLOB-01*.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg/providers/Microsoft.Storage/storageAccounts/storage12345/blobServices/default/containers/blob-container-01"
```

#### <a name="assign-a-role-for-a-group-in-a-specific-virtual-network-resource-scope"></a>Een rol toewijzen voor een groep in een specifiek bron bereik voor een virtueel netwerk

Wijst de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) toe aan de *team groep Anne Mack* met id 22222222-2222-2222-2222-222222222222 bij een resource bereik voor een virtueel netwerk met de naam *Pharma-Sales-project-Network*.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Virtual Machine Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/pharma-sales/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network"
```

#### <a name="assign-a-role-for-a-user-at-a-resource-group-scope"></a>Een rol toewijzen aan een gebruiker in een bereik van een resource groep

Wijst de rol van [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) toe aan *patlong \@ contoso.com* gebruiker op het *Pharma-Sales-* resource groeps bereik.

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-a-user-using-the-unique-role-id-at-a-resource-group-scope"></a>Een rol voor een gebruiker toewijzen met behulp van de unieke rol-ID in een resource groeps bereik

Er zijn een aantal keren dat een rolnaam kan worden gewijzigd, bijvoorbeeld:

- U gebruikt uw eigen aangepaste rol en u besluit de naam te wijzigen.
- U gebruikt een preview-functie met **(preview)** in de naam. Wanneer de rol wordt vrijgegeven, wordt de naam van de rol gewijzigd.

Zelfs als een rol een andere naam heeft gekregen, wordt de rol-ID niet gewijzigd. Als u scripts of automatisering gebruikt om roltoewijzingen te maken, is het een best practice om de unieke rol-ID te gebruiken in plaats van de rolnaam. Als de naam van een rol wordt gewijzigd, zijn uw scripts waarschijnlijker goed.

In het volgende voor beeld wordt de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) toegewezen aan de *patlong \@ contoso.com* -gebruiker op het *Pharma-Sales-* resource groeps bereik.

```azurecli
az role assignment create --assignee "patlong@contoso.com" \
--role "9980e02c-c2be-4d73-94e8-173b1dc7cf3c" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-all-blob-containers-at-a-resource-group-scope"></a>Een rol toewijzen voor alle BLOB-containers in een bereik van een resource groep

Hiermee wordt de rol van [BLOB-gegevens](built-in-roles.md#storage-blob-data-contributor) van de opslag gebruiker toegewezen aan een service-principal met de object-id *55555555-5555-5555-5555-555555555555* in het *voor beeld-opslag-RG-* bron groeps bereik.

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--resource-group "Example-Storage-rg"
```

U kunt ook de volledig gekwalificeerde resource groep opgeven met de `--scope` para meter:

```azurecli
az role assignment create --assignee "55555555-5555-5555-5555-555555555555" \
--role "Storage Blob Data Contributor" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-Storage-rg"
```

#### <a name="assign-a-role-for-an-application-at-a-resource-group-scope"></a>Een rol toewijzen voor een toepassing in een bereik van een resource groep

Wijst de rol van [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) toe aan een toepassing met Service-Principal object-id 44444444-4444-4444-4444-444444444444 op het *Pharma* van de resource groep.

```azurecli
az role assignment create --assignee "44444444-4444-4444-4444-444444444444" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-a-new-service-principal-at-a-resource-group-scope"></a>Een rol toewijzen voor een nieuwe Service-Principal in een bereik van een resource groep

Als u een nieuwe Service-Principal maakt en een rol onmiddellijk probeert toe te wijzen aan die Service-Principal, kan die roltoewijzing in sommige gevallen mislukken. Als u bijvoorbeeld een script gebruikt voor het maken van een nieuwe beheerde identiteit en vervolgens probeert een rol toe te wijzen aan die Service-Principal, kan de roltoewijzing mislukken. De oorzaak van deze fout is waarschijnlijk een replicatie vertraging. De service-principal wordt gemaakt in één regio. de roltoewijzing kan echter plaatsvinden in een andere regio waarvoor de Service-Principal nog niet is gerepliceerd. Als u dit scenario wilt aanpakken, geeft u het Principal-type op bij het maken van de roltoewijzing.

Als u een rol wilt toewijzen, gebruikt u [AZ Role Assignment Create](/cli/azure/role/assignment#az_role_assignment_create), geeft u een waarde op voor `--assignee-object-id` , en stelt `--assignee-principal-type` u vervolgens in op `ServicePrincipal` .

```azurecli
az role assignment create --assignee-object-id "{assigneeObjectId}" \
--assignee-principal-type "{assigneePrincipalType}" \
--role "{roleNameOrId}" \
--resource-group "{resourceGroupName}" \
--scope "/subscriptions/{subscriptionId}"
```

In het volgende voor beeld wordt de rol van [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) toegewezen aan de door *MSI-test* beheerde identiteit in het *Pharma* van de resource groep:

```azurecli
az role assignment create --assignee-object-id "33333333-3333-3333-3333-333333333333" \
--assignee-principal-type "ServicePrincipal" \
--role "Virtual Machine Contributor" \
--resource-group "pharma-sales"
```

#### <a name="assign-a-role-for-a-user-at-a-subscription-scope"></a>Een rol toewijzen aan een gebruiker op een abonnements bereik

Wijst de rol van [lezer](built-in-roles.md#reader) toe aan de *annm \@ example.com* -gebruiker bij een abonnements bereik.

```azurecli
az role assignment create --assignee "annm@example.com" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="assign-a-role-for-a-group-at-a-subscription-scope"></a>Een rol toewijzen aan een groep in een abonnements bereik

Wijst de rol van [lezer](built-in-roles.md#reader) toe aan de *team groep Anne Mack* met id 22222222-2222-2222-2222-222222222222 bij een abonnements bereik.

```azurecli
az role assignment create --assignee "22222222-2222-2222-2222-222222222222" \
--role "Reader" \
--subscription "00000000-0000-0000-0000-000000000000"
```

#### <a name="assign-a-role-for-all-blob-containers-at-a-subscription-scope"></a>Een rol toewijzen voor alle BLOB-containers in een abonnements bereik

Wijst de rol [opslag-BLOB-gegevens lezer](built-in-roles.md#storage-blob-data-reader) toe aan de *Alain \@ example.com* -gebruiker bij een abonnements bereik.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Storage Blob Data Reader" \
--scope "/subscriptions/00000000-0000-0000-0000-000000000000"
```

#### <a name="assign-a-role-for-a-user-at-a-management-group-scope"></a>Een rol toewijzen aan een gebruiker in een bereik van een beheer groep

Wijst de rol [facturerings lezer](built-in-roles.md#billing-reader) toe aan de *Alain \@ example.com* -gebruiker in een bereik van een beheer groep.

```azurecli
az role assignment create --assignee "alain@example.com" \
--role "Billing Reader" \
--scope "/providers/Microsoft.Management/managementGroups/marketing-group"
```

## <a name="next-steps"></a>Volgende stappen

- [Azure-roltoewijzingen weer geven met Azure CLI](role-assignments-list-cli.md)
- [Azure CLI gebruiken voor het beheren van Azure-resources en-resource groepen](../azure-resource-manager/management/manage-resources-cli.md)
