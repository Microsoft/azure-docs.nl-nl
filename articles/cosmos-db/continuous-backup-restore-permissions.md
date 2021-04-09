---
title: Machtigingen configureren om een Azure Cosmos DB-account te herstellen.
description: Meer informatie over het isoleren en beperken van de machtigingen voor het herstellen van een continu back-upaccount naar een specifieke rol of een principal. U ziet hoe u een ingebouwde rol toewijst met behulp van Azure Portal, CLI of een aangepaste rol definieert.
author: kanshiG
ms.service: cosmos-db
ms.topic: how-to
ms.date: 02/01/2021
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 8b3ce2c195dc2fa3dd703306e731aa5b807b78b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100648600"
---
# <a name="manage-permissions-to-restore-an-azure-cosmos-db-account"></a>Machtigingen voor het herstellen van een Azure Cosmos DB-account beheren
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

> [!IMPORTANT]
> De functie voor herstel naar een bepaald tijdstip (doorlopende back-upmodus) voor Azure Cosmos DB is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt.
> Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met Azure Cosmos DB kunt u de herstel machtigingen voor een doorlopende back-upaccount (preview) voor een specifieke rol of Principal isoleren en beperken. De eigenaar van het account kan een Restore activeren en een rol toewijzen aan andere principals om de herstel bewerking uit te voeren. Deze machtigingen kunnen worden toegepast op het abonnements bereik of nauw keuriger in het bereik van de bron account, zoals wordt weer gegeven in de volgende afbeelding:

:::image type="content" source="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" alt-text="Lijst met rollen die vereist zijn voor het uitvoeren van een herstel bewerking." lightbox="./media/continuous-backup-restore-permissions/restore-roles-permissions.png" border="false":::

Bereik is een set resources met toegang, zie de documentatie van [Azure RBAC](../role-based-access-control/scope-overview.md) voor meer informatie over bereiken. In Azure Cosmos DB zijn toepasselijke bereiken het bron abonnement en het database account voor de meeste use cases. De principal waarmee de herstel acties worden uitgevoerd, moet schrijf machtigingen hebben voor de doel resource groep.

## <a name="assign-roles-for-restore-using-the-azure-portal"></a>Rollen toewijzen voor herstel met behulp van de Azure Portal

Als u een herstel bewerking wilt uitvoeren, moet een gebruiker of een principal de machtiging voor herstellen (die de machtiging *herstellen/actie* heeft) hebben en toestemming hebben om een nieuw account in te richten (dat *Schrijf* machtiging is).  De eigenaar kan de `CosmosRestoreOperator` en ingebouwde rollen toewijzen aan een principal om deze machtigingen te verlenen `Cosmos DB Operator` .

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/)

1. Navigeer naar uw abonnement en ga naar het tabblad **toegangs beheer (IAM)** en **Selecteer**  >  **roltoewijzing** toevoegen toevoegen

1. Selecteer in het deel venster roltoewijzing **toevoegen** , in het veld **rol** , de functie **CosmosRestoreOperator** . Kies **gebruiker, groep of een Service-Principal** voor het veld **toegang toewijzen aan** en zoek naar de naam of e-mail-id van een gebruiker, zoals wordt weer gegeven in de volgende afbeelding:

   :::image type="content" source="./media/continuous-backup-restore-permissions/assign-restore-operator-roles.png" alt-text="CosmosRestoreOperator-en Cosmos DB operator rollen toewijzen." border="true":::

1. Selecteer **Opslaan** om de machtiging voor *herstellen/actie* te verlenen.

1. Herhaal stap 3 met **Cosmos DB operator** rol om de schrijf machtiging te verlenen. Wanneer u deze rol toewijst vanuit de Azure Portal, wordt de machtiging voor terugzetten verleend aan het hele abonnement.

## <a name="permission-scopes"></a>Machtigingsbereiken

|Bereik  |Voorbeeld  |
|---------|---------|
|Abonnement | /subscriptions/00000000-0000-0000-0000-000000000000 |
|Resourcegroep | /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Example-cosmosdb-rg |
|CosmosDB restorable account resource | /Subscriptions/00000000-0000-0000-0000-000000000000/providers/Microsoft.DocumentDB/locaties/West VS/restorableDatabaseAccounts/23e99a35-cd36-4df4-9614-f767a03b9995|

De herstor bare account bron kan worden geëxtraheerd uit de uitvoer van de `az cosmosdb restorable-database-account list --name <accountname>` opdracht in CLI of `Get-AzCosmosDBRestorableDatabaseAccount -DatabaseAccountName <accountname>` cmdlet in Power shell. Het naam kenmerk in de uitvoer vertegenwoordigt het `instanceID` restorable-account. Zie het artikel [Power shell](continuous-backup-restore-powershell.md) of [cli](continuous-backup-restore-command-line.md) voor meer informatie.

## <a name="permissions"></a>Machtigingen

De volgende machtigingen zijn vereist om de verschillende activiteiten uit te voeren die betrekking hebben op het herstellen van accounts voor continue back-upmodus:

|Machtiging  |Impact  |Minimum bereik  |Maximum bereik  |
|---------|---------|---------|---------|
|`Microsoft.Resources/deployments/validate/action`, `Microsoft.Resources/deployments/write` | Deze machtigingen zijn vereist voor de implementatie van de ARM-sjabloon om het herstelde account te maken. Zie de [RestorableAction](#custom-restorable-action) hieronder voor meer informatie over het instellen van deze rol. | Niet van toepassing | Niet van toepassing  |
|`Microsoft.DocumentDB/databaseAccounts/write` | Deze machtiging is vereist voor het herstellen van een account in een resource groep | De resource groep waaronder het teruggezette account is gemaakt. | Het abonnement waarmee het herstelde account is gemaakt |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` |Deze machtiging is vereist voor het bereik van de bron herstelbaar database account zodat er herstel acties kunnen worden uitgevoerd.  | De *RestorableDatabaseAccount* -resource die deel uitmaakt van het bron account dat wordt hersteld. Deze waarde wordt ook gegeven door de `ID` eigenschap van de bron van het herstorable database account. Een voor beeld van een restorable account is */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/Region/restorableDatabaseAccounts/<GUID-instanceid>* | Het abonnement met het database account dat kan worden ontstorbaar. De resource groep kan niet worden gekozen als bereik.  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read` |Deze machtiging is vereist voor het bereik van de bron herstelbaar database account voor het weer geven van de database accounts die kunnen worden hersteld.  | De *RestorableDatabaseAccount* -resource die deel uitmaakt van het bron account dat wordt hersteld. Deze waarde wordt ook gegeven door de `ID` eigenschap van de bron van het herstorable database account. Een voor beeld van een restorable account is */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/Region/restorableDatabaseAccounts/<GUID-instanceid>*| Het abonnement met het database account dat kan worden ontstorbaar. De resource groep kan niet worden gekozen als bereik.  |
|`Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read` | Deze machtiging is vereist voor het bereik van de bron herstorable om het lezen van mogelijk te maken bronnen, zoals een lijst met data bases en containers, toe te staan voor een restorable-account.  | De *RestorableDatabaseAccount* -resource die deel uitmaakt van het bron account dat wordt hersteld. Deze waarde wordt ook gegeven door de `ID` eigenschap van de bron van het herstorable database account. Een voor beeld van een restorable account is */subscriptions/subscriptionId/providers/Microsoft.DocumentDB/locations/Region/restorableDatabaseAccounts/<GUID-instanceid>*| Het abonnement met het database account dat kan worden ontstorbaar. De resource groep kan niet worden gekozen als bereik. |

## <a name="azure-cli-role-assignment-scenarios-to-restore-at-different-scopes"></a>Scenario's voor het toewijzen van Azure CLI-rollen voor het herstellen van verschillende bereiken

Rollen met een machtiging kunnen worden toegewezen aan verschillende bereiken om nauw keuriger controle te krijgen over wie de herstel bewerking kan uitvoeren binnen een abonnement of een bepaald account.

### <a name="assign-capability-to-restore-from-any-restorable-account-in-a-subscription"></a>Mogelijkheid tot herstellen van een herstel bare account in een abonnement toewijzen

De `CosmosRestoreOperator` ingebouwde rol op abonnements niveau toewijzen

```azurecli-interactive
az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope /subscriptions/<subscriptionId>
```

### <a name="assign-capability-to-restore-from-a-specific-account"></a>Mogelijkheid tot het herstellen van een specifiek account toewijzen

* Wijs een schrijf actie voor een gebruiker toe aan de specifieke resource groep. Deze actie is vereist voor het maken van een nieuw account in de resource groep.

* Wijs de ingebouwde rol *CosmosRestoreOperator* toe aan het specifieke restor bare database account dat moet worden hersteld. In de volgende opdracht wordt de scope voor de *RestorableDatabaseAccount* opgehaald uit de `ID` eigenschap in de uitvoer van `az cosmosdb restorable-database-account` (als u cli gebruikt) of  `Get-AzCosmosDBRestorableDatabaseAccount` (als u Power shell gebruikt).

  ```azurecli-interactive
   az role assignment create --role "CosmosRestoreOperator" --assignee <email> –scope <RestorableDatabaseAccount>
  ```

### <a name="assign-capability-to-restore-from-any-source-account-in-a-resource-group"></a>Mogelijkheid tot het herstellen van een bron account in een resource groep toewijzen.
Deze bewerking wordt momenteel niet ondersteund.

## <a name="custom-role-creation-for-restore-action-with-cli"></a><a id="custom-restorable-action"></a>Aangepaste rol maken voor herstel actie met CLI

De eigenaar van het abonnement kan de machtiging bieden voor het herstellen naar een andere Azure AD-identiteit. De machtiging herstellen is gebaseerd op de actie: `Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action` en moet worden opgenomen in de herstel machtiging. Er is een ingebouwde rol met de naam *CosmosRestoreOperator* waarvoor deze rol is opgenomen. U kunt de machtiging toewijzen met behulp van deze ingebouwde rol of een aangepaste rol maken.

De RestorableAction hieronder vertegenwoordigt een aangepaste rol. U moet deze rol expliciet maken. Met de volgende JSON-sjabloon maakt u een aangepaste *RestorableAction* met herstel machtiging:

```json
{
  "assignableScopes": [
    "/subscriptions/23587e98-b6ac-4328-a753-03bcd3c8e744"
  ],
  "description": "Can do a restore request for any Cosmos DB database account with continuous backup",
  "permissions": [
    {
      "actions": [
        "Microsoft.Resources/deployments/validate/action",
        "Microsoft.DocumentDB/databaseAccounts/write",
        "Microsoft.Resources/deployments/write",  
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/restore/action",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/read",
        "Microsoft.DocumentDB/locations/restorableDatabaseAccounts/*/read"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "Name": "RestorableAction",
    "roleType": "CustomRole"
}
```

Gebruik vervolgens de volgende sjabloon implementatie opdracht voor het maken van een rol met de machtiging herstellen met ARM-sjabloon:

```azurecli-interactive
az role definition create --role-definition <JSON_Role_Definition_Path>
```

## <a name="next-steps"></a>Volgende stappen

* Configureer en beheer doorlopende back-ups met behulp van de [Azure Portal](continuous-backup-restore-portal.md), [Power shell](continuous-backup-restore-powershell.md), [cli](continuous-backup-restore-command-line.md)of [Azure Resource Manager](continuous-backup-restore-template.md).
* [Resource model van de modus continue back-up](continuous-backup-restore-resource-model.md)
