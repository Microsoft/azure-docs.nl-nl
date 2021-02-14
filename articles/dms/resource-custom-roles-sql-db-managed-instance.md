---
title: 'Aangepaste rollen: online SQL Server naar migraties van SQL-beheerde exemplaren'
titleSuffix: Azure Database Migration Service
description: Meer informatie over het gebruik van aangepaste rollen voor SQL Server naar online migraties van Azure SQL Managed instance.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 1228234b6a2904c453ec92f3c09a7b3f55604953
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100363760"
---
# <a name="custom-roles-for-sql-server-to-azure-sql-managed-instance-online-migrations"></a>Aangepaste rollen voor SQL Server naar online migraties van Azure SQL Managed instance

Azure Database Migration Service gebruikt een APP-ID om te communiceren met Azure-Services. Voor de APP-ID is de rol Inzender vereist op het abonnements niveau (die veel bedrijfs beveiligings afdelingen niet toestaat) of het maken van aangepaste rollen die de specifieke machtigingen verlenen die Azure Database Migration Service vereist. Omdat er een limiet van 2.000 aangepaste rollen in Azure Active Directory, wilt u wellicht alle machtigingen combi neren die specifiek door de APP-ID zijn vereist in een of twee aangepaste rollen, en vervolgens de APP-ID toekennen aan de aangepaste rol voor specifieke objecten of resource groepen (versus op abonnements niveau). Als het aantal aangepaste rollen niet van belang is, kunt u de aangepaste rollen splitsen op resource type om drie aangepaste rollen te maken, zoals hieronder wordt beschreven.

De sectie AssignableScopes van de JSON-teken reeks van de roldefinitie stelt u in staat om te bepalen waar de machtigingen worden weer gegeven in de gebruikers interface van de **roltoewijzing toevoegen** in de portal. Waarschijnlijk wilt u de rol in de resource groep of zelfs op resource niveau definiëren om te voor komen dat de gebruikers interface met extra rollen wordt gewirward. Houd er rekening mee dat hiermee de werkelijke roltoewijzing niet wordt uitgevoerd.

## <a name="minimum-number-of-roles"></a>Minimum aantal rollen

Het is momenteel raadzaam om mini maal twee aangepaste rollen te maken voor de APP-ID, een op het niveau van de resource en de andere op het abonnements niveau.

> [!NOTE]
> De laatste vereiste voor een aangepaste rol kan uiteindelijk worden verwijderd, omdat de nieuwe SQL Managed instance-code wordt geïmplementeerd in Azure.

**Aangepaste rol voor de App-ID**. Deze rol is vereist voor Azure Database Migration Service migratie op het niveau van de *resource* of de *resource groep* die als host fungeert voor de Azure database Migration service (Zie het artikel [de portal gebruiken om een Azure AD-toepassing en Service-Principal te maken die toegang heeft tot resources](../active-directory/develop/howto-create-service-principal-portal.md)voor meer informatie over de App-ID).

```json
{
  "Name": "DMS Role - App ID",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write",
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read",
        "Microsoft.DataMigration/locations/*",
        "Microsoft.DataMigration/services/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/ResourceGroups/<StorageAccount_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<ManagedInstance_rg_name>",
    "/subscriptions/<subscription_id>/ResourceGroups/<DMS_rg_name>",
  ]
}
```

**Aangepaste rol voor het app-ID-abonnement**. Deze rol is vereist voor Azure Database Migration Service-migratie op *abonnements* niveau die als host fungeert voor het SQL Managed instance.

```json
{
  "Name": "DMS Role - App ID - Sub",
  "IsCustom": true,
  "Description": "DMS App ID access at subscription level to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/locations/managedDatabaseRestoreAzureAsyncOperation/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>"
  ]
}
```

De bovenstaande JSON moet worden opgeslagen in drie tekst bestanden en u kunt de AzureRM, AZ Power shell-cmdlets of Azure CLI gebruiken om de rollen te maken met behulp van **New-azurermroledefinition wordt (AzureRM)** of **New-AzRoleDefinition (AZ)**.

Zie het artikel [aangepaste Azure-rollen](../role-based-access-control/custom-roles.md)voor meer informatie.

Nadat u deze aangepaste rollen hebt gemaakt, moet u roltoewijzingen toevoegen aan gebruikers en APP-ID ('s) aan de juiste resources of resource groepen:

* De rol ' DMS rol-App-ID ' moet worden verleend aan de APP-ID die wordt gebruikt voor de migraties, en op het opslag account, het Azure Database Migration Service exemplaar en het resource niveau van de SQL Managed instance. Deze wordt verleend op het niveau van de resource of de resource groep die als host fungeert voor de Azure Database Migration Service.
* De rol ' DMS-functie-ID-sub ' moet worden toegekend aan de APP-ID op het abonnements niveau dat als host fungeert voor het beheerde exemplaar van SQL (dat wordt verleend aan de resource of de resource groep). Deze vereiste is tijdelijk totdat een code-update is geïmplementeerd.

## <a name="expanded-number-of-roles"></a>Uitgebreid aantal rollen

Als het aantal aangepaste rollen in uw Azure Active Directory geen bezorgdheid is, raden we u aan om in totaal drie rollen te maken. U hebt nog steeds de rol ' DMS-functie-app-ID – sub ' nodig, maar de rol ' DMS Role-App-ID ' is in twee verschillende rollen gesplitst op resource type.

**Aangepaste rol voor de APP-ID voor het beheerde SQL-exemplaar**

```json
{
  "Name": "DMS Role - App ID - SQL MI",
  "IsCustom": true,
  "Description": "DMS App ID access to complete MI migrations",
  "Actions": [
        "Microsoft.Sql/managedInstances/read",
        "Microsoft.Sql/managedInstances/write",
        "Microsoft.Sql/managedInstances/databases/read",
        "Microsoft.Sql/managedInstances/databases/write",
        "Microsoft.Sql/managedInstances/databases/delete",
        "Microsoft.Sql/managedInstances/metrics/read"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<ManagedInstance_rg_name>"
  ]
}
```

**Aangepaste rol voor de APP-ID voor opslag**

```json
{
  "Name": "DMS Role - App ID - Storage",
  "IsCustom": true,
  "Description": "DMS App ID storage access to complete MI migrations",
  "Actions": [
"Microsoft.Storage/storageAccounts/read",
        "Microsoft.Storage/storageAccounts/listKeys/action",
        "Microsoft.Storage/storageaccounts/blobservices/read",
        "Microsoft.Storage/storageaccounts/blobservices/write"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/<subscription_id>/resourceGroups/<StorageAccount_rg_name>"
  ]
}
```

## <a name="role-assignment"></a>Roltoewijzing

Als u een rol wilt toewijzen aan gebruikers/APP-ID, opent u de Azure Portal en voert u de volgende stappen uit:

1. Ga naar de resource groep of resource (met uitzonde ring van de rol die moet worden toegekend aan het abonnement), ga naar **Access Control** en schuif vervolgens naar de aangepaste rollen die u zojuist hebt gemaakt.

2. Selecteer de gewenste rol, selecteer de APP-ID en sla de wijzigingen op.

  Uw APP-ID ('s) wordt nu weer gegeven op het tabblad **roltoewijzingen** .

## <a name="next-steps"></a>Volgende stappen

* Bekijk de richt lijnen voor de migratie van uw scenario in de micro soft [Data Base-migratie handleiding](https://datamigration.microsoft.com/).
