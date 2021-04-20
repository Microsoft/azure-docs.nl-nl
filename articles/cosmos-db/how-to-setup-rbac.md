---
title: Op rollen gebaseerd toegangsbeheer configureren voor uw Azure Cosmos DB account met Azure AD
description: Informatie over het configureren van op rollen gebaseerd toegangsbeheer met Azure Active Directory voor uw Azure Cosmos DB account
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/19/2021
ms.author: thweiss
ms.openlocfilehash: 209d18dfbadea89f14fd90da9a1bc57b3ccf0dfe
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728069"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account-preview"></a>Op rollen gebaseerd toegangsbeheer configureren met Azure Active Directory voor uw Azure Cosmos DB account (preview)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB op rollen gebaseerd toegangsbeheer is momenteel in preview. Deze preview-versie wordt aangeboden zonder Service Level Agreement en wordt niet aanbevolen voor productieworkloads. Zie Aanvullende gebruiksvoorwaarden voor Microsoft Azure [previews voor meer informatie.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

> [!NOTE]
> Dit artikel gaat over op rollen gebaseerd toegangsbeheer voor gegevensvlakbewerkingen in Azure Cosmos DB. Zie het artikel Op rollen [](role-based-access-control.md) gebaseerd toegangsbeheer toegepast op uw beheervlakbewerkingen als u bewerkingen op de beheervlak gebruikt.

Azure Cosmos DB een ingebouwd op rollen gebaseerd toegangsbeheersysteem (RBAC) waarmee u het volgende kunt doen:

- Verifieren van uw gegevensaanvragen met Azure Active Directory identiteit (Azure AD).
- Autorleer uw gegevensaanvragen met een fijnkeurig, op rollen gebaseerd machtigingsmodel.

## <a name="concepts"></a>Concepten

De Azure Cosmos DB RBAC voor gegevensvlak is gebaseerd op concepten die vaak worden gevonden in andere RBAC-systemen, zoals [Azure RBAC:](../role-based-access-control/overview.md)

- Het [machtigingsmodel](#permission-model) bestaat uit een reeks **acties**; Elk van deze acties wordt toe te staan aan een of meer databasebewerkingen. Enkele voorbeelden van acties zijn het lezen van een item, het schrijven van een item of het uitvoeren van een query.
- Azure Cosmos DB gebruikers **[roldefinities maken](#role-definitions)** die een lijst met toegestane acties bevatten.
- Roldefinities worden toegewezen aan specifieke Azure AD-identiteiten via **[roltoewijzingen.](#role-assignments)** Een roltoewijzing definieert ook het bereik waar de roldefinitie op van toepassing is; Momenteel zijn er drie scopes:
    - Een Azure Cosmos DB account,
    - Een Azure Cosmos DB-database,
    - Een Azure Cosmos DB container.

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="RBAC-concepten":::

> [!NOTE]
> In Azure Cosmos DB RBAC worden momenteel geen ingebouwde roldefinities beschikbaar gemaakt.

## <a name="permission-model"></a><a id="permission-model"></a> Machtigingsmodel

> [!IMPORTANT]
> Dit machtigingsmodel heeft alleen betrekking op databasebewerkingen, zodat u gegevens kunt lezen en schrijven. Dit geldt **niet** voor beheerbewerkingen, zoals het maken van containers of het wijzigen van de doorvoer. Dit betekent dat u **geen gebruik kunt maken Azure Cosmos DB SDK** voor gegevensvlak om beheerbewerkingen te verifiëren met een AAD-identiteit. In plaats daarvan moet u [Azure RBAC gebruiken via:](role-based-access-control.md)
> - [ARM-sjablonen](manage-with-templates.md)
> - [Azure PowerShell scripts ,](manage-with-powershell.md)
> - [Azure CLI-scripts](manage-with-cli.md),
> - Azure-beheerbibliotheken die beschikbaar zijn in
>   - [.NET](https://www.nuget.org/packages/Azure.ResourceManager.CosmosDB)
>   - [Java](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-cosmos)
>   - [Python](https://pypi.org/project/azure-mgmt-cosmosdb/)

De onderstaande tabel bevat alle acties die worden weergegeven door het machtigingsmodel.

| Name | Bijbehorende databasebewerking(en) |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | Metagegevens van account lezen. Zie [Aanvragen voor metagegevens](#metadata-requests) voor meer informatie. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | Maak een nieuw item. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | Een afzonderlijk item lezen op basis van de id en partitiesleutel (punt-lezen). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | Een bestaand item vervangen. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | 'Upsert' van een item, wat betekent dat u het item moet maken als het niet bestaat, of vervang het als het bestaat. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | Een item verwijderen. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | Voer een [SQL-query uit.](sql-query-getting-started.md) |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | Lees uit de wijzigingsfeed [van de container.](read-change-feed.md) |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | Voer een [opgeslagen procedure uit.](stored-procedures-triggers-udfs.md) |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | Conflicten [beheren](conflict-resolution-policies.md) voor accounts met meerdere schrijfregio's (dat wil zeggen, items uit de conflictfeed opsorteren en verwijderen). |

Jokertekens worden ondersteund op het niveau van *zowel containers* *als items:*

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

### <a name="metadata-requests"></a><a id="metadata-requests"></a> Aanvragen voor metagegevens

Wanneer u Azure Cosmos DB SDK's gebruikt, geven deze SDK's alleen-lezen metagegevensaanvragen uit tijdens de initialisatie en om specifieke gegevensaanvragen te verwerken. Met deze aanvragen voor metagegevens worden verschillende configuratiedetails opgehaald, zoals: 

- De algemene configuratie van uw account, waaronder de Azure-regio's waarin het account beschikbaar is.
- De partitiesleutel van uw containers of het indexeringsbeleid.
- De lijst met fysieke partities die een container en hun adressen maken.

Ze halen *geen* gegevens op die u in uw account hebt opgeslagen.

Om de beste transparantie van ons machtigingsmodel te garanderen, worden deze aanvragen voor metagegevens expliciet gedekt door de `Microsoft.DocumentDB/databaseAccounts/readMetadata` actie. Deze actie moet worden toegestaan in elke situatie waarin uw Azure Cosmos DB-account toegankelijk is via een van de Azure Cosmos DB SDK's. Deze kan worden toegewezen (via een roltoewijzing) op elk niveau in de Azure Cosmos DB hiërarchie (account, database of container).

De werkelijke metagegevensaanvragen die door de actie zijn toegestaan, zijn afhankelijk van het bereik waar de `Microsoft.DocumentDB/databaseAccounts/readMetadata` actie aan is toegewezen:

| Bereik | Aanvragen die zijn toegestaan door de actie |
|---|---|
| Account | - De databases onder het account bekijken<br>- Voor elke database onder het account, de toegestane acties voor het databasebereik |
| Database | - Metagegevens van de database lezen<br>- De containers onder de database in een lijst bekijken<br>- Voor elke container onder de database, de toegestane acties in het containerbereik |
| Container | - Metagegevens van de container lezen<br>- Een lijst met fysieke partities onder de container maken<br>- Het adres van elke fysieke partitie oplossen |

## <a name="create-role-definitions"></a><a id="role-definitions"></a> Roldefinities maken

Wanneer u een roldefinitie maakt, moet u het volgende bieden:

- De naam van uw Azure Cosmos DB account.
- De resourcegroep met uw account.
- Het type roldefinitie; alleen `CustomRole` wordt momenteel ondersteund.
- De naam van de roldefinitie.
- Een lijst met [acties](#permission-model) die door de rol moeten worden toegestaan.
- Een of meer scope(s) die de roldefinitie kan worden toegewezen op; ondersteunde scopes zijn:
    - `/` (accountniveau),
    - `/dbs/<database-name>` (databaseniveau),
    - `/dbs/<database-name>/colls/<container-name>` (containerniveau).

> [!NOTE]
> De hieronder beschreven bewerkingen zijn momenteel beschikbaar in:
> - Azure PowerShell: [Az.CosmosDB versie 2.0.1-preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI: [uitbreidingsversie 0.4.0 van cosmosdb-preview](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

Maak een rol met de *naam MyReadOnlyRole* die alleen leesacties bevat:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadOnlyRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed') `
    -AssignableScope "/"
```

Maak een rol met de *naam MyReadWriteRole* die alle acties bevat:

```powershell
New-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -Type CustomRole -RoleName MyReadWriteRole `
    -DataAction @( `
        'Microsoft.DocumentDB/databaseAccounts/readMetadata',
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*', `
        'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*') `
    -AssignableScope "/"
```

Vermeld de roldefinities die u hebt gemaakt om hun ID's op te halen:

```powershell
Get-AzCosmosDBSqlRoleDefinition -AccountName $accountName `
    -ResourceGroupName $resourceGroupName
```

```
RoleName         : MyReadWriteRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}

RoleName         : MyReadOnlyRole
Id               : /subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAcc
                   ounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>
Type             : CustomRole
Permissions      : {Microsoft.Azure.Management.CosmosDB.Models.Permission}
AssignableScopes : {/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAc
                   counts/<myCosmosAccount>}
```

### <a name="using-the-azure-cli"></a>Met behulp van de Azure CLI

Maak een rol met de *naam MyReadOnlyRole* die alleen leesacties bevat:

```json
// role-definition-ro.json
{
    "RoleName": "MyReadOnlyRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ]
    }]
}
```

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-ro.json
```

Maak een rol met de *naam MyReadWriteRole* die alle acties bevat:

```json
// role-definition-rw.json
{
    "RoleName": "MyReadWriteRole",
    "Type": "CustomRole",
    "AssignableScopes": ["/"],
    "Permissions": [{
        "DataActions": [
            "Microsoft.DocumentDB/databaseAccounts/readMetadata",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
            "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ]
    }]
}
```

```azurecli
az cosmosdb sql role definition create --account-name $accountName --resource-group $resourceGroupName --body @role-definition-rw.json
```

Vermeld de roldefinities die u hebt gemaakt om de eigen ID's op te halen:

```azurecli
az cosmosdb sql role definition list --account-name $accountName --resource-group $resourceGroupName
```

```
[
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadWriteRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  },
  {
    "assignableScopes": [
      "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>"
    ],
    "id": "/subscriptions/<mySubscriptionId>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDB/databaseAccounts/<myCosmosAccount>/sqlRoleDefinitions/<roleDefinitionId>",
    "name": "<roleDefinitionId>",
    "permissions": [
      {
        "dataActions": [
          "Microsoft.DocumentDB/databaseAccounts/readMetadata",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery",
          "Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed"
        ],
        "notDataActions": []
      }
    ],
    "resourceGroup": "<myResourceGroup>",
    "roleName": "MyReadOnlyRole",
    "sqlRoleDefinitionGetResultsType": "CustomRole",
    "type": "Microsoft.DocumentDB/databaseAccounts/sqlRoleDefinitions"
  }
]
```

## <a name="create-role-assignments"></a><a id="role-assignments"></a> Roltoewijzingen maken

Zodra u uw roldefinities hebt gemaakt, kunt u deze koppelen aan uw AAD-identiteiten. Wanneer u een roltoewijzing maakt, moet u het volgende bieden:

- De naam van uw Azure Cosmos DB account.
- De resourcegroep die uw account bevat.
- De id van de roldefinitie die moet worden toegewezen.
- De principal-id van de identiteit aan wie de roldefinitie moet worden toegewezen.
- Het bereik van de roltoewijzing; ondersteunde scopes zijn:
    - `/` (accountniveau)
    - `/dbs/<database-name>` (databaseniveau)
    - `/dbs/<database-name>/colls/<container-name>` (containerniveau)

  Het bereik moet overeenkomen met of een subbereik zijn van een van de toewijsbare scopes van de roldefinitie.

> [!NOTE]
> Als u een roltoewijzing voor een service-principal **wilt** maken,  moet u ervoor zorgen dat u de **object-id** ervan gebruikt, zoals te vinden is in de sectie Bedrijfstoepassingen van Azure Active Directory portalblade.

> [!NOTE]
> De hieronder beschreven bewerkingen zijn momenteel beschikbaar in:
> - Azure PowerShell: [Az.CosmosDB versie 2.0.1-preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI: [uitbreidingsversie 0.4.0 van cosmosdb-preview](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

Een rol toewijzen aan een identiteit:

```powershell
$resourceGroupName = "<myResourceGroup>"
$accountName = "<myCosmosAccount>"
$readOnlyRoleDefinitionId = "<roleDefinitionId>" // as fetched above
$principalId = "<aadPrincipalId>"
New-AzCosmosDBSqlRoleAssignment -AccountName $accountName `
    -ResourceGroupName $resourceGroupName `
    -RoleDefinitionId $readOnlyRoleDefinitionId `
    -Scope $accountName `
    -PrincipalId $principalId
```

### <a name="using-the-azure-cli"></a>Met behulp van de Azure CLI

Een rol toewijzen aan een identiteit:

```azurecli
resourceGroupName='<myResourceGroup>'
accountName='<myCosmosAccount>'
readOnlyRoleDefinitionId = '<roleDefinitionId>' // as fetched above
principalId = '<aadPrincipalId>'
az cosmosdb sql role assignment create --account-name $accountName --resource-group $resourceGroupName --scope "/" --principal-id $principalId --role-definition-id $readOnlyRoleDefinitionId
```

## <a name="initialize-the-sdk-with-azure-ad"></a>De SDK initialiseren met Azure AD

Als u de Azure Cosmos DB RBAC in uw toepassing wilt gebruiken, moet u de manier bijwerken waarop u de Azure Cosmos DB SDK initialiseert. In plaats van de primaire sleutel van uw account door te geven, moet u een exemplaar van een klasse `TokenCredential` doorgeven. Dit exemplaar biedt de Azure Cosmos DB SDK de context die nodig is om een AAD-token op te halen namens de identiteit die u wilt gebruiken.

De manier waarop u een `TokenCredential` instantie maakt, valt buiten het bereik van dit artikel. Er zijn veel manieren om een dergelijk exemplaar te maken, afhankelijk van het type AAD-identiteit dat u wilt gebruiken (user principal, service-principal, group enzovoort). Het belangrijkste is dat uw exemplaar moet worden opgelost met de identiteit `TokenCredential` (principal-id) aan wie u uw rollen hebt toegewezen. Hier vindt u voorbeelden van het maken van een `TokenCredential` klasse:

- [in .NET](/dotnet/api/overview/azure/identity-readme#credential-classes)
- [in Java](/java/api/overview/azure/identity-readme#credential-classes)
- [in JavaScript](/javascript/api/overview/azure/identity-readme#credential-classes)

In de onderstaande voorbeelden wordt een service-principal met een exemplaar `ClientSecretCredential` gebruikt.

### <a name="in-net"></a>In .NET

De Azure Cosmos DB RBAC wordt momenteel ondersteund in de `preview` versie van [de .NET SDK V3.](sql-api-sdk-dotnet-standard.md)

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>In Java

De Azure Cosmos DB RBAC wordt momenteel ondersteund in de [Java SDK V4.](sql-api-sdk-java-v4.md)

```java
TokenCredential ServicePrincipal = new ClientSecretCredentialBuilder()
    .authorityHost("https://login.microsoftonline.com")
    .tenantId("<azure-ad-tenant-id>")
    .clientId("<client-application-id>")
    .clientSecret("<client-application-secret>")
    .build();
CosmosAsyncClient Client = new CosmosClientBuilder()
    .endpoint("<account-endpoint>")
    .credential(ServicePrincipal)
    .build();
```

### <a name="in-javascript"></a>In JavaScript

De Azure Cosmos DB RBAC wordt momenteel ondersteund in de [JavaScript SDK V3](sql-api-sdk-node.md).

```javascript
const servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
const client = new CosmosClient({
    "<account-endpoint>",
    aadCredentials: servicePrincipal
});
```

## <a name="auditing-data-requests"></a>Gegevensaanvragen controleren

Wanneer u de Azure Cosmos DB RBAC [gebruikt,](cosmosdb-monitor-resource-logs.md) worden diagnostische logboeken uitgebreid met identiteits- en autorisatiegegevens voor elke gegevensbewerking. Hiermee kunt u gedetailleerde controles uitvoeren en de AAD-identiteit ophalen die wordt gebruikt voor elke gegevensaanvraag die naar uw Azure Cosmos DB account wordt verzonden.

Deze aanvullende informatie stroomt in de **logboekcategorie DataPlaneRequests** en bestaat uit twee extra kolommen:

- `aadPrincipalId_g` toont de principal-id van de AAD-identiteit die is gebruikt om de aanvraag te verifiëren.
- `aadAppliedRoleAssignmentId_g` toont de [roltoewijzing](#role-assignments) die is gehonoreerd bij het autoriseren van de aanvraag.

## <a name="limits"></a>Limieten

- U kunt maximaal 100 roldefinities en 2000 roltoewijzingen per Azure Cosmos DB maken.
- U kunt alleen roldefinities toewijzen aan Azure AD-identiteiten die behoren tot dezelfde Azure AD-tenant als uw Azure Cosmos DB account.
- Azure AD-groepsoplossing wordt momenteel niet ondersteund voor identiteiten die tot meer dan 200 groepen behoren.
- Het Azure AD-token wordt momenteel doorgegeven als een header met elke afzonderlijke aanvraag die naar de Azure Cosmos DB-service wordt verzonden, waardoor de totale nettolading groter wordt.
- Toegang tot uw gegevens met Azure AD via [de Azure Cosmos DB Explorer](data-explorer.md) wordt nog niet ondersteund. Als u Azure Cosmos DB Explorer, moet de gebruiker nog steeds toegang hebben tot de primaire sleutel van het account.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>Welke API's van Azure Cosmos DB worden ondersteund door RBAC?

Alleen de SQL-API wordt momenteel ondersteund.

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>Is het mogelijk om roldefinities en roltoewijzingen te beheren vanuit de Azure Portal?

Dat is op dit moment nog niet mogelijk.

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Welke SDK's in Azure Cosmos DB SQL API ondersteunen RBAC?

De [.NET V3-](sql-api-sdk-dotnet-standard.md) en [Java V4-SDK's](sql-api-sdk-java-v4.md) worden momenteel ondersteund.

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>Wordt het Azure AD-token automatisch vernieuwd door de Azure Cosmos DB-SDK's wanneer het verloopt?

Ja.

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primary-key-when-using-rbac"></a>Is het mogelijk om het gebruik van de primaire sleutel van het account uit te schakelen bij gebruik van RBAC?

Het uitschakelen van de primaire sleutel van het account is momenteel niet mogelijk.

## <a name="next-steps"></a>Volgende stappen

- Krijg een overzicht van [beveiligde toegang tot gegevens in Cosmos DB](secure-access-to-data.md).
- Meer informatie over [RBAC voor Azure Cosmos DB management](role-based-access-control.md).