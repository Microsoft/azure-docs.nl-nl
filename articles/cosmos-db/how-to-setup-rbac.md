---
title: Op rollen gebaseerd toegangs beheer configureren voor uw Azure Cosmos DB-account met Azure AD
description: Meer informatie over het configureren van op rollen gebaseerd toegangs beheer met Azure Active Directory voor uw Azure Cosmos DB-account
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thweiss
ms.openlocfilehash: d83109f380a3044073cf2dd8d10f29027ebb9f41
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690903"
---
# <a name="configure-role-based-access-control-with-azure-active-directory-for-your-azure-cosmos-db-account-preview"></a>Op rollen gebaseerd toegangs beheer configureren met Azure Active Directory voor uw Azure Cosmos DB-account (preview-versie)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!IMPORTANT]
> Azure Cosmos DB op rollen gebaseerd toegangs beheer is momenteel beschikbaar als preview-versie. Deze preview-versie wordt zonder Service Level Agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Zie voor meer informatie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!NOTE]
> Dit artikel heeft betrekking op op rollen gebaseerd toegangs beheer voor gegevensvlak bewerkingen in Azure Cosmos DB. Zie op [rollen gebaseerd toegangs beheer](role-based-access-control.md) dat is toegepast op het artikel beheer vlak bewerkingen als u beheer vlak bewerkingen gebruikt.

Azure Cosmos DB biedt een ingebouwd systeem voor op rollen gebaseerd toegangs beheer (RBAC) waarmee u het volgende kunt doen:

- Verifieer uw gegevens aanvragen met een Azure Active Directory-identiteit (Azure AD).
- Autoriseer uw gegevens aanvragen met een verfijnd, op rollen gebaseerd machtigings model.

## <a name="concepts"></a>Concepten

Het Azure Cosmos DB Data-vlak RBAC is gebaseerd op concepten die algemeen worden gevonden in andere RBAC-systemen zoals [Azure RBAC](../role-based-access-control/overview.md):

- Het [machtigings model](#permission-model) bestaat uit een reeks **acties**. elk van deze acties is gekoppeld aan een of meer database bewerkingen. Enkele voor beelden van acties zijn het lezen van een item, het schrijven van een item of het uitvoeren van een query.
- Azure Cosmos DB gebruikers **[roldefinities](#role-definitions)** met een lijst met toegestane acties maken.
- Roldefinities krijgen toegewezen aan specifieke Azure AD-identiteiten via **[roltoewijzingen](#role-assignments)**. Een roltoewijzing definieert ook het bereik waarop de functie definitie van toepassing is. Momenteel zijn er momenteel drie bereiken:
    - Een Azure Cosmos DB-account,
    - Een Azure Cosmos DB-Data Base,
    - Een Azure Cosmos DB-container.

  :::image type="content" source="./media/how-to-setup-rbac/concepts.png" alt-text="RBAC-concepten":::

> [!NOTE]
> De Azure Cosmos DB RBAC maakt momenteel geen ingebouwde roldefinities beschikbaar.

## <a name="permission-model"></a><a id="permission-model"></a> Machtigings model

De volgende tabel bevat alle acties die door het machtigings model worden weer gegeven.

| Name | Bijbehorende database bewerking (en) |
|---|---|
| `Microsoft.DocumentDB/databaseAccounts/readMetadata` | Meta gegevens van account lezen. Zie [meta gegevens aanvragen](#metadata-requests) voor meer informatie. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/create` | Maak een nieuw item. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/read` | Een afzonderlijk item lezen met de ID en de partitie sleutel (punt-lezen). |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/replace` | Een bestaand item vervangen. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/upsert` | Een item ' Upsert ', wat betekent dat het wordt gemaakt als het niet bestaat of vervangen als het bestaat. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/delete` | Een item verwijderen. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeQuery` | Een [SQL-query](sql-query-getting-started.md)uitvoeren. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/readChangeFeed` | Lezen uit de [wijzigings feed](read-change-feed.md)van de container. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/executeStoredProcedure` | Een [opgeslagen procedure](stored-procedures-triggers-udfs.md)uitvoeren. |
| `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/manageConflicts` | [Conflicten](conflict-resolution-policies.md) voor multi-write-regio accounts beheren (dat wil zeggen items weer geven en verwijderen van de feed conflict). |

Joker tekens worden ondersteund in zowel *containers* als *item* niveaus:

- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/*`
- `Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers/items/*`

> [!IMPORTANT]
> Dit machtigings model geldt alleen voor database bewerkingen waarmee u gegevens kunt lezen en schrijven. Dit geldt **niet** voor een soort beheer bewerkingen, zoals het maken van containers of het wijzigen van de door voer. Als u beheer bewerkingen met een AAD-identiteit wilt verifiëren, gebruikt u in plaats daarvan [Azure RBAC](role-based-access-control.md) .

### <a name="metadata-requests"></a><a id="metadata-requests"></a> Meta gegevens aanvragen

Bij gebruik van Azure Cosmos DB Sdk's geven deze Sdk's alleen-lezen-aanvragen voor meta gegevens tijdens de initialisatie en om specifieke gegevens aanvragen te verwerken. Met deze meta gegevens aanvragen worden verschillende configuratie gegevens opgehaald, zoals: 

- De algemene configuratie van uw account, met inbegrip van de Azure-regio's waar het account beschikbaar is.
- De partitie sleutel van de containers of het indexerings beleid.
- De lijst met fysieke partities waaruit een container en hun adressen zijn gemaakt.

De gegevens die u in uw account hebt opgeslagen, worden *niet* opgehaald.

Om ervoor te zorgen dat het machtigings model de beste transparantie heeft, worden deze meta gegevens aanvragen expliciet gedekt door de `Microsoft.DocumentDB/databaseAccounts/readMetadata` actie. Deze actie moet worden toegestaan in elke situatie waarbij uw Azure Cosmos DB-account wordt geopend via een van de Azure Cosmos DB Sdk's. Deze kan worden toegewezen (via een roltoewijzing) op elk niveau in de Azure Cosmos DB hiërarchie (dat wil zeggen, account, data base of container).

De daad werkelijke aanvragen voor meta gegevens die door de `Microsoft.DocumentDB/databaseAccounts/readMetadata` actie worden toegestaan, zijn afhankelijk van het bereik waaraan de actie is toegewezen:

| Bereik | Aanvragen die zijn toegestaan door de actie |
|---|---|
| Account | -De data bases onder het account weer geven<br>-Voor elke Data Base onder het account, de toegestane acties voor het bereik van de data base |
| Database | -Data base-meta gegevens lezen<br>-De containers in de Data Base weer geven<br>-Voor elke container in de-data base, de toegestane acties in het container bereik |
| Container | -Meta gegevens van container lezen<br>-Fysieke partities onder de container weer geven<br>-Het omzetten van het adres van elke fysieke partitie |

## <a name="create-role-definitions"></a><a id="role-definitions"></a> Roldefinities maken

Wanneer u een roldefinitie maakt, moet u het volgende opgeven:

- De naam van uw Azure Cosmos DB-account.
- De resource groep die uw account bevat.
- Het type van de roldefinitie; `CustomRole` wordt momenteel alleen ondersteund.
- De naam van de functie definitie.
- Een lijst met [acties](#permission-model) die door de rol moeten worden toegestaan.
- Een of meer bereik (en) waaraan de roldefinitie kan worden toegewezen. ondersteunde bereiken zijn:
    - `/` (account niveau),
    - `/dbs/<database-name>` (database niveau),
    - `/dbs/<database-name>/colls/<container-name>` (container niveau).

> [!NOTE]
> De bewerkingen die hieronder worden beschreven, zijn momenteel beschikbaar in:
> - Azure PowerShell: [AZ. CosmosDB versie 2.0.1-Preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI: [' cosmosdb-preview ' extensie versie 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

### <a name="using-azure-powershell"></a>Azure PowerShell gebruiken

Maak een rol met de naam *MyReadOnlyRole* die alleen lees acties bevat:

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

Maak een rol met de naam *MyReadWriteRole* die alle acties bevat:

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

De roldefinities weer geven die u hebt gemaakt om de Id's op te halen:

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

Maak een rol met de naam *MyReadOnlyRole* die alleen lees acties bevat:

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

Maak een rol met de naam *MyReadWriteRole* die alle acties bevat:

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

De roldefinities weer geven die u hebt gemaakt om de Id's op te halen:

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

Wanneer u de roldefinities hebt gemaakt, kunt u deze koppelen aan uw AAD-identiteiten. Bij het maken van een roltoewijzing moet u het volgende opgeven:

- De naam van uw Azure Cosmos DB-account.
- De resource groep die uw account bevat.
- De ID van de roldefinitie die moet worden toegewezen.
- De principal-ID van de identiteit waaraan de roldefinitie moet worden toegewezen.
- Het bereik van de roltoewijzing; ondersteunde bereiken zijn:
    - `/` (account niveau)
    - `/dbs/<database-name>` (database niveau)
    - `/dbs/<database-name>/colls/<container-name>` (container niveau)

  Het bereik moet overeenkomen of een subbereik zijn van een van de toewijs bare bereiken van de functie definitie.

> [!NOTE]
> Als u een roltoewijzing voor een Service-Principal wilt maken, moet u ervoor zorgen dat u de bijbehorende **object-id** gebruikt, zoals is gevonden in het gedeelte **bedrijfs toepassingen** van de Blade **Azure Active Directory** Portal.

> [!NOTE]
> De bewerkingen die hieronder worden beschreven, zijn momenteel beschikbaar in:
> - Azure PowerShell: [AZ. CosmosDB versie 2.0.1-Preview](https://www.powershellgallery.com/packages/Az.CosmosDB/2.0.1-preview)
> - Azure CLI: [' cosmosdb-preview ' extensie versie 0.4.0](https://github.com/Azure/azure-cli-extensions/tree/master/src/cosmosdb-preview)

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
az cosmosdb sql role assignment create --account-name $accountName --resource-group --scope "/" --principalId $principalId --role-definition-id $readOnlyRoleDefinitionId
```

## <a name="initialize-the-sdk-with-azure-ad"></a>De SDK initialiseren met Azure AD

Als u de Azure Cosmos DB RBAC wilt gebruiken in uw toepassing, moet u de manier waarop u de Azure Cosmos DB SDK initialiseert, bijwerken. In plaats van de primaire sleutel van uw account door te geven, moet u een exemplaar van een klasse door geven `TokenCredential` . Dit exemplaar biedt de Azure Cosmos DB SDK met de context die is vereist om een AAD-token op te halen namens de identiteit die u wilt gebruiken.

De manier waarop u een `TokenCredential` exemplaar maakt, valt buiten het bereik van dit artikel. Er zijn veel manieren om een dergelijke instantie te maken, afhankelijk van het type AAD-identiteit dat u wilt gebruiken (User Principal, Service Principal, Group, enz.). Het belangrijkste is dat uw `TokenCredential` exemplaar moet worden omgezet in de identiteit (Principal-id) waaraan u uw rollen hebt toegewezen. U kunt voor beelden van het maken van een `TokenCredential` klasse vinden:

- [in .NET](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme#credential-classes)
- [in Java](https://docs.microsoft.com/java/api/overview/azure/identity-readme#credential-classes)

In de onderstaande voor beelden wordt een service-principal met een `ClientSecretCredential` exemplaar gebruikt.

### <a name="in-net"></a>In .NET

> [!NOTE]
> U moet de `preview` versie van de Azure Cosmos db .NET SDK gebruiken om toegang te krijgen tot deze functie.

```csharp
TokenCredential servicePrincipal = new ClientSecretCredential(
    "<azure-ad-tenant-id>",
    "<client-application-id>",
    "<client-application-secret>");
CosmosClient client = new CosmosClient("<account-endpoint>", servicePrincipal);
```

### <a name="in-java"></a>In Java

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

## <a name="auditing-data-requests"></a>Controle van gegevens aanvragen

Wanneer u de Azure Cosmos DB RBAC gebruikt, worden [Diagnostische logboeken](cosmosdb-monitor-resource-logs.md) uitgebreid met identiteits-en autorisatie gegevens voor elke gegevens bewerking. Zo kunt u gedetailleerde controle uitvoeren en de AAD-identiteit ophalen die wordt gebruikt voor elke gegevens aanvraag die naar uw Azure Cosmos DB-account wordt verzonden.

Deze extra informatie loopt over in de logboek categorie **DataPlaneRequests** en bestaat uit twee extra kolommen:

- `aadPrincipalId_g` toont de principal-ID van de AAD-identiteit die is gebruikt om de aanvraag te verifiëren.
- `aadAppliedRoleAssignmentId_g` toont de [roltoewijzing](#role-assignments) die is gerespecteerd bij het autoriseren van de aanvraag.

## <a name="limits"></a>Limieten

- U kunt Maxi maal 100 functie definities en 2.000 roltoewijzingen per Azure Cosmos DB account maken.
- De oplossing voor Azure AD-groepen wordt momenteel niet ondersteund voor identiteiten die deel uitmaken van meer dan 200 groepen.
- Het Azure AD-token wordt momenteel door gegeven als een header waarbij elke afzonderlijke aanvraag wordt verzonden naar de Azure Cosmos DB-Service, waardoor de totale nettolading groter wordt.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

### <a name="which-azure-cosmos-db-apis-are-supported-by-rbac"></a>Welke Azure Cosmos DB Api's worden ondersteund door RBAC?

Alleen de SQL-API wordt momenteel ondersteund.

### <a name="is-it-possible-to-manage-role-definitions-and-role-assignments-from-the-azure-portal"></a>Is het mogelijk om roldefinities en roltoewijzingen van de Azure Portal te beheren?

Azure Portal ondersteuning voor functie beheer is nog niet beschikbaar.

### <a name="which-sdks-in-azure-cosmos-db-sql-api-support-rbac"></a>Welke Sdk's in Azure Cosmos DB SQL API ondersteunen RBAC?

De sdk's voor [.net v3](sql-api-sdk-dotnet-standard.md) en [Java v4](sql-api-sdk-java-v4.md) worden momenteel ondersteund.

### <a name="is-the-azure-ad-token-automatically-refreshed-by-the-azure-cosmos-db-sdks-when-it-expires"></a>Wordt het Azure AD-token automatisch vernieuwd door de Azure Cosmos DB Sdk's wanneer het verloopt?

Ja.

### <a name="is-it-possible-to-disable-the-usage-of-the-account-primary-key-when-using-rbac"></a>Is het mogelijk om het gebruik van de primaire sleutel van het account uit te scha kelen wanneer u RBAC gebruikt?

Het uitschakelen van de primaire sleutel van het account is momenteel niet mogelijk.

## <a name="next-steps"></a>Volgende stappen

- Bekijk een overzicht van [beveiligde toegang tot gegevens in Cosmos DB](secure-access-to-data.md).
- Meer informatie over [RBAC voor Azure Cosmos DB-beheer](role-based-access-control.md).
