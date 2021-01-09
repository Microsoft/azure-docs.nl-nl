---
title: Programmatisch beleid maken
description: Dit artikel helpt u bij het programmatisch maken en beheren van beleids regels voor Azure Policy met Azure CLI, Azure PowerShell en REST API.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 4c3a503e8d48573441093879511490608437b1e1
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98033860"
---
# <a name="programmatically-create-policies"></a>Programmatisch beleid maken

In dit artikel wordt uitgelegd hoe u via programma code beleid maakt en beheert. Met Azure Policy definities worden verschillende regels en effecten voor uw resources afgedwongen. Afdwinging zorgt ervoor dat resources blijven voldoen aan uw bedrijfs normen en service overeenkomsten.

Zie [nalevings gegevens ophalen](get-compliance-data.md)voor meer informatie over naleving.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat aan de volgende vereisten wordt voldaan:

1. Installeer de [ARMClient](https://github.com/projectkudu/ARMClient) als u dit nog niet hebt gedaan. Dit is een hulpprogramma waarmee HTTP-aanvragen worden verzonden naar Azure Resource Manager-API’s.

1. Werk de Azure PowerShell-module bij naar de nieuwste versie. Zie [Azure PowerShell-module installeren](/powershell/azure/install-az-ps) voor gedetailleerde informatie. Zie [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)voor meer informatie over de meest recente versie.

1. Registreer de Azure Policy Insights-resource provider met behulp van Azure PowerShell om te controleren of uw abonnement werkt met de resource provider. Als u een resource provider wilt registreren, moet u gemachtigd zijn om de actie bewerking registreren voor de resource provider uit te voeren. Deze bewerking is opgenomen in de rollen Inzender en Eigenaar. Voer de volgende opdracht uit om de resourceprovider te registreren:

   ```azurepowershell-interactive
   Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
   ```

   Zie [Resourceproviders en -typen](../../../azure-resource-manager/management/resource-providers-and-types.md) voor meer informatie over het registreren en weergeven van resourceproviders.

1. Installeer Azure CLI als u dat nog niet hebt gedaan. U kunt de nieuwste versie downloaden bij de [installatie van Azure cli in Windows](/cli/azure/install-azure-cli-windows).

## <a name="create-and-assign-a-policy-definition"></a>Een beleidsdefinitie maken en toewijzen

De eerste stap voor een betere zicht baarheid van uw resources is het maken en toewijzen van beleid voor uw resources. In de volgende stap leert u hoe u via een programma een beleid maakt en toewijst. Het voorbeeld beleid controleert opslag accounts die zijn geopend voor alle open bare netwerken met behulp van Power shell, Azure CLI en HTTP-aanvragen.

### <a name="create-and-assign-a-policy-definition-with-powershell"></a>Een beleids definitie maken en toewijzen met Power shell

1. Gebruik het volgende JSON-code fragment om een JSON-bestand met de naam AuditStorageAccounts.jsaan te maken.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Zie [Azure Policy Definition structure](../concepts/definition-structure.md)(Engelstalig) voor meer informatie over het ontwerpen van een beleids definitie.

1. Voer de volgende opdracht uit om een beleids definitie te maken met behulp van de AuditStorageAccounts.jsin het bestand.

   ```azurepowershell-interactive
   New-AzPolicyDefinition -Name 'AuditStorageAccounts' -DisplayName 'Audit Storage Accounts Open to Public Networks' -Policy 'AuditStorageAccounts.json'
   ```

   Met de opdracht wordt een beleids definitie gemaakt met de naam _audit Storage accounts open open bare netwerken_.
   Zie [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition)voor meer informatie over andere para meters die u kunt gebruiken.

   Als de para meters worden aangeroepen zonder locatie, wordt `New-AzPolicyDefinition` standaard de beleids definitie opgeslagen in het geselecteerde abonnement van de sessie context. Gebruik de volgende para meters om de definitie op een andere locatie op te slaan:

   - **SubscriptionId** : opslaan in een ander abonnement. Vereist een _GUID_ -waarde.
   - **ManagementGroupName** : Sla het bestand op in een beheer groep. Vereist een _teken reeks_ waarde.

1. Nadat u de beleids definitie hebt gemaakt, kunt u een beleids toewijzing maken door de volgende opdrachten uit te voeren:

   ```azurepowershell-interactive
   $rg = Get-AzResourceGroup -Name 'ContosoRG'
   $Policy = Get-AzPolicyDefinition -Name 'AuditStorageAccounts'
   New-AzPolicyAssignment -Name 'AuditStorageAccounts' -PolicyDefinition $Policy -Scope $rg.ResourceId
   ```

   Vervang _ContosoRG_ door de naam van de gewenste resource groep.

   De **bereik** parameter voor `New-AzPolicyAssignment` werkt met de beheer groep, het abonnement, de resource groep of een enkele resource. De para meter gebruikt een volledig bronpad, die de eigenschap **ResourceID** bij `Get-AzResourceGroup` retourneert. Het patroon voor de **Scope** voor elke container is als volgt. Vervang `{rName}` , `{rgName}` , `{subId}` , en `{mgName}` met de naam van de resource, de naam van de resource groep, de abonnements-id en de beheer groep, respectievelijk.
   `{rType}` wordt vervangen door het **resource type** van de resource, bijvoorbeeld `Microsoft.Compute/virtualMachines` voor een virtuele machine.

   - Resource `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Resource groep- `/subscriptions/{subId}/resourceGroups/{rgName}`
   - Abonnees `/subscriptions/{subId}`
   - Beheer groep- `/providers/Microsoft.Management/managementGroups/{mgName}`

Zie [AZ. resources](/powershell/module/az.resources/#policy)(Engelstalig) voor meer informatie over het beheren van bron beleid met behulp van de Resource Manager Power shell-module.

### <a name="create-and-assign-a-policy-definition-using-armclient"></a>Een beleids definitie maken en toewijzen met behulp van ARMClient

Gebruik de volgende procedure om een beleids definitie te maken.

1. Kopieer het volgende JSON-fragment om een JSON-bestand te maken. U belt het bestand in de volgende stap.

   ```json
   "properties": {
       "displayName": "Audit Storage Accounts Open to Public Networks",
       "policyType": "Custom",
       "mode": "Indexed",
       "description": "This policy ensures that storage accounts with exposure to Public Networks are audited.",
       "parameters": {},
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Storage/storageAccounts"
                   },
                   {
                       "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                       "equals": "Allow"
                   }
               ]
           },
           "then": {
               "effect": "audit"
           }
       }
   }
   ```

1. Maak de beleids definitie met een van de volgende aanroepen:

   ```console
   # For defining a policy in a subscription
   armclient PUT "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2019-09-01" @<path to policy definition JSON file>

   # For defining a policy in a management group
   armclient PUT "/providers/Microsoft.Management/managementgroups/{managementGroupId}/providers/Microsoft.Authorization/policyDefinitions/AuditStorageAccounts?api-version=2019-09-01" @<path to policy definition JSON file>
   ```

   Vervang de voor gaande {subscriptionId} met de ID van uw abonnement of {managementGroupId} door de ID van uw [beheer groep](../../management-groups/overview.md).

   Zie voor meer informatie over de structuur van de query [Azure Policy definities – maken of bijwerken](/rest/api/resources/policydefinitions/createorupdate) en [beleids definities: maken of bijwerken in beheer groep](/rest/api/resources/policydefinitions/createorupdateatmanagementgroup)

Gebruik de volgende procedure om een beleids toewijzing te maken en de beleids definitie toe te wijzen op het niveau van de resource groep.

1. Kopieer het volgende JSON-fragment om een JSON-beleids toewijzings bestand te maken. Vervang voorbeeld informatie in &lt; &gt; symbolen met uw eigen waarden.

   ```json
   {
       "properties": {
           "description": "This policy assignment makes sure that storage accounts with exposure to Public Networks are audited.",
           "displayName": "Audit Storage Accounts Open to Public Networks Assignment",
           "parameters": {},
           "policyDefinitionId": "/subscriptions/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks",
           "scope": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>"
       }
   }
   ```

1. Maak de toewijzing van het beleid met behulp van de volgende aanroep:

   ```console
   armclient PUT "/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Authorization/policyAssignments/Audit Storage Accounts Open to Public Networks?api-version=2019-09-01" @<path to Assignment JSON file>
   ```

   Vervang voorbeeld informatie in &lt; &gt; symbolen met uw eigen waarden.

   Zie [Azure rest API resources](/rest/api/resources/)(Engelstalig) voor meer informatie over het maken van http-aanroepen naar de rest API.

### <a name="create-and-assign-a-policy-definition-with-azure-cli"></a>Een beleids definitie maken en toewijzen met Azure CLI

Als u een beleids definitie wilt maken, gebruikt u de volgende procedure:

1. Kopieer het volgende JSON-fragment om een JSON-beleids toewijzings bestand te maken.

   ```json
   {
       "if": {
           "allOf": [{
                   "field": "type",
                   "equals": "Microsoft.Storage/storageAccounts"
               },
               {
                   "field": "Microsoft.Storage/storageAccounts/networkAcls.defaultAction",
                   "equals": "Allow"
               }
           ]
       },
       "then": {
           "effect": "audit"
       }
   }
   ```

   Zie [Azure Policy Definition structure](../concepts/definition-structure.md)(Engelstalig) voor meer informatie over het ontwerpen van een beleids definitie.

1. Voer de volgende opdracht uit om een beleids definitie te maken:

   ```azurecli-interactive
   az policy definition create --name 'audit-storage-accounts-open-to-public-networks' --display-name 'Audit Storage Accounts Open to Public Networks' --description 'This policy ensures that storage accounts with exposures to public networks are audited.' --rules '<path to json file>' --mode All
   ```

   Met de opdracht wordt een beleids definitie gemaakt met de naam _audit Storage accounts open open bare netwerken_.
   Zie [AZ Policy Definition Create](/cli/azure/policy/definition#az_policy_definition_create)(Engelstalig) voor meer informatie over andere para meters die u kunt gebruiken.

   Als de para meters worden aangeroepen zonder locatie, wordt `az policy definition creation` standaard de beleids definitie opgeslagen in het geselecteerde abonnement van de sessie context. Gebruik de volgende para meters om de definitie op een andere locatie op te slaan:

   - **abonnement** : Sla het bestand op in een ander abonnement. Vereist een _GUID_ -waarde voor de abonnements-id of een _teken reeks_ waarde voor de naam van het abonnement.
   - **beheer-groep** -opslaan in een beheer groep. Vereist een _teken reeks_ waarde.

1. Gebruik de volgende opdracht om een beleids toewijzing te maken. Vervang voorbeeld informatie in &lt; &gt; symbolen met uw eigen waarden.

   ```azurecli-interactive
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   De **bereik** parameter voor `az policy assignment create` werkt met de beheer groep, het abonnement, de resource groep of een enkele resource. De para meter gebruikt een volledig bronpad. Het patroon voor de **Scope** voor elke container is als volgt. Vervang `{rName}` , `{rgName}` , `{subId}` , en `{mgName}` met de naam van de resource, de naam van de resource groep, de abonnements-id en de beheer groep, respectievelijk. `{rType}` wordt vervangen door het **resource type** van de resource, bijvoorbeeld `Microsoft.Compute/virtualMachines` voor een virtuele machine.

   - Resource `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Resource groep- `/subscriptions/{subID}/resourceGroups/{rgName}`
   - Abonnees `/subscriptions/{subID}`
   - Beheer groep- `/providers/Microsoft.Management/managementGroups/{mgName}`

U kunt de Azure Policy definitie-ID ophalen met behulp van Power shell met de volgende opdracht:

```azurecli-interactive
az policy definition show --name 'Audit Storage Accounts with Open Public Networks'
```

De beleids definitie-ID voor de beleids definitie die u hebt gemaakt, moet er ongeveer uitzien als in het volgende voor beeld:

```output
"/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Storage Accounts Open to Public Networks"
```

Zie [Azure cli-resource beleid](/cli/azure/policy)voor meer informatie over hoe u resource beleid kunt beheren met Azure cli.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over de opdrachten en query's in dit artikel.

- [Azure REST API-resources](/rest/api/resources/)
- [Azure PowerShell modules](/powershell/module/az.resources/#policy)
- [Azure CLI-beleids opdrachten](/cli/azure/policy)
- [Naslag informatie over REST API van Azure Policy Insights-resource provider](/rest/api/policy-insights)
- [Uw resources organiseren met Azure-beheergroepen](../../management-groups/overview.md).
