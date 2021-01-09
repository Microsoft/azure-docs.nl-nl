---
title: Verifiëren met beheerde identiteiten
description: Toegang tot bronnen die worden beveiligd door Azure Active Directory zonder u aan te melden met referenties of geheimen met behulp van een beheerde identiteit
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 10/27/2020
ms.openlocfilehash: 1152c8b72bcb830a7ba4efa053d3ffff667f9dc8
ms.sourcegitcommit: c4c554db636f829d7abe70e2c433d27281b35183
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98034166"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Toegang tot Azure-bronnen verifiëren door beheerde identiteiten te gebruiken in Azure Logic Apps

Om toegang te krijgen tot andere bronnen die worden beveiligd door Azure Active Directory (Azure AD) en uw identiteit te verifiëren zonder zich aan te melden, kan uw logische app gebruikmaken van een [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md) (voorheen Managed Service Identity of MSI), in plaats van referenties of geheimen. Azure beheert deze identiteit voor u en helpt u bij het beveiligen van uw referenties omdat u geen geheimen hoeft op te geven of te wisselen.

Azure Logic Apps ondersteunt zowel door het [*systeem toegewezen*](../active-directory/managed-identities-azure-resources/overview.md) als door de [*gebruiker toegewezen*](../active-directory/managed-identities-azure-resources/overview.md) beheerde identiteiten. Uw logische app kan gebruikmaken van de door het systeem toegewezen identiteit of *één* door de gebruiker toegewezen identiteit, die u kunt delen via een groep logische apps, maar niet beide. Momenteel ondersteunen alleen [specifieke ingebouwde triggers en acties](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) beheerde identiteiten, niet beheerde connectors of verbindingen, bijvoorbeeld:

* HTTP
* Azure Functions
* Azure API Management
* Azure App Services

In dit artikel wordt beschreven hoe u beide soorten beheerde identiteiten instelt voor uw logische app. Raadpleeg de volgende onderwerpen voor meer informatie:

* [Triggers en acties die beheerde identiteiten ondersteunen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Ondersteunde verificatie typen voor uitgaande oproepen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Limieten voor beheerde identiteiten voor Logic apps](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Azure-Services die ondersteuning bieden voor Azure AD-verificatie met beheerde identiteiten](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/). Zowel de beheerde identiteit als de Azure-doel resource waarvoor u toegang nodig hebt, moeten hetzelfde Azure-abonnement gebruiken.

* Als u een beheerde identiteit toegang wilt geven tot een Azure-resource, moet u een rol toevoegen aan de doel resource voor die identiteit. Om rollen toe te voegen, hebt u [Azure AD-beheerders machtigingen](../active-directory/roles/permissions-reference.md) nodig waarmee rollen kunnen worden toegewezen aan identiteiten in de bijbehorende Azure AD-Tenant.

* De doel-Azure-resource waartoe u toegang wilt krijgen. Op deze resource voegt u een rol toe voor de beheerde identiteit, waarmee de logische app de toegang tot de doel bron kan verifiëren.

* De logische app waarvoor u de [trigger of acties wilt gebruiken die beheerde identiteiten ondersteunen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

## <a name="enable-managed-identity"></a>Beheerde identiteit inschakelen

Als u de beheerde identiteit die u wilt gebruiken, wilt instellen, volgt u de koppeling voor die identiteit:

* [Door het systeem toegewezen identiteit](#system-assigned)
* [Door gebruiker toegewezen identiteit](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Door het systeem toegewezen identiteit inschakelen

In tegens telling tot door de gebruiker toegewezen identiteiten hoeft u de door het systeem toegewezen identiteit niet hand matig te maken. Als u de door het systeem toegewezen identiteit voor uw logische app wilt instellen, kunt u de volgende opties gebruiken:

* [Azure Portal](#azure-portal-system-logic-app)
* [Azure Resource Manager sjablonen](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Door het systeem toegewezen identiteit inschakelen in Azure Portal

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Selecteer **identiteit** onder **instellingen** in het menu van de logische app. Selecteer **systeem toegewezen**  >  **bij**  >  **Opslaan**. Wanneer u wordt gevraagd om te bevestigen, selecteert u **Ja**.

   ![De door het systeem toegewezen identiteit inschakelen](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Als u een fout bericht krijgt dat u slechts één beheerde identiteit kunt hebben, is uw logische app al gekoppeld aan de door de gebruiker toegewezen identiteit. Voordat u de door het systeem toegewezen identiteit kunt toevoegen, moet u eerst de door de gebruiker toegewezen identiteit *verwijderen* uit uw logische app.

   Uw logische app kan nu gebruikmaken van de door het systeem toegewezen identiteit, die is geregistreerd bij Azure Active Directory en wordt vertegenwoordigd door een object-ID.

   ![Object-ID voor door het systeem toegewezen identiteit](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Object-id** | <*identiteit-bron-ID*> | Een GUID (Globally Unique Identifier) die de door het systeem toegewezen identiteit voor uw logische app vertegenwoordigt in een Azure AD-Tenant |
   ||||

1. Volg nu de [stappen voor het verlenen van toegang tot de resource met de-id](#access-other-resources) verderop in dit onderwerp.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Door het systeem toegewezen identiteit in Azure Resource Manager sjabloon inschakelen

U kunt [Azure Resource Manager sjablonen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)gebruiken om het maken en implementeren van Azure-resources zoals Logic apps te automatiseren. Als u de door het systeem toegewezen beheerde identiteit voor uw logische app in de sjabloon wilt inschakelen, voegt `identity` u het object en de `type` onderliggende eigenschap toe aan de resource definitie van de logische app in de sjabloon, bijvoorbeeld:

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

Wanneer Azure de resource definitie van de logische app maakt, `identity` krijgt het object de volgende aanvullende eigenschappen:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Eigenschap (JSON) | Waarde | Beschrijving |
|-----------------|-------|-------------|
| `principalId` | <*Principal-ID*> | De GUID (Globally Unique Identifier) van het Service-Principal-object voor de beheerde identiteit die uw logische app vertegenwoordigt in de Azure AD-Tenant. Deze GUID wordt soms weer gegeven als een ' object-ID ' of `objectID` . |
| `tenantId` | <*Azure-AD-Tenant-ID*> | De GUID (Globally Unique Identifier) die de Azure AD-Tenant vertegenwoordigt waarbij de logische app nu lid is. De Service-Principal in de Azure AD-Tenant heeft dezelfde naam als de logische app-instantie. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Door gebruiker toegewezen identiteit inschakelen

Als u een door de gebruiker toegewezen beheerde identiteit voor uw logische app wilt instellen, moet u deze identiteit eerst als afzonderlijke zelfstandige Azure-resource maken. Dit zijn de opties die u kunt gebruiken:

* [Azure Portal](#azure-portal-user-identity)
* [Azure Resource Manager sjablonen](#template-user-identity)
* Azure PowerShell
  * [Door de gebruiker toegewezen identiteit maken](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Roltoewijzing toevoegen](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [Door de gebruiker toegewezen identiteit maken](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Roltoewijzing toevoegen](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Azure REST-API
  * [Door de gebruiker toegewezen identiteit maken](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Roltoewijzing toevoegen](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Een door de gebruiker toegewezen identiteit maken in de Azure Portal

1. Voer in het [Azure Portal](https://portal.azure.com), in het zoekvak op een wille keurige pagina, de `managed identities` optie **beheerde identiteiten** in en selecteer deze.

   ![Zoek en selecteer beheerde identiteiten](./media/create-managed-service-identity/find-select-managed-identities.png)

1. Selecteer onder **beheerde identiteiten** de optie **toevoegen**.

   ![Nieuwe beheerde identiteit toevoegen](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Geef informatie op over uw beheerde identiteit en selecteer vervolgens **controleren + maken**, bijvoorbeeld:

   ![Door de gebruiker toegewezen beheerde identiteit maken](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Abonnement** | Ja | <*Azure-subscription-name*> | De naam voor het te gebruiken Azure-abonnement |
   | **Resourcegroep** | Ja | <*Naam-van-Azure-resourcegroep*> | De naam voor de resource groep die moet worden gebruikt. Maak een nieuwe groep of selecteer een bestaande groep. In dit voor beeld wordt een nieuwe groep met de naam gemaakt `fabrikam-managed-identities-RG` . |
   | **Regio** | Ja | <*Azure-regio*> | De Azure-regio waar informatie over uw resource moet worden opgeslagen. In dit voorbeeld wordt 'US - west' gebruikt. |
   | **Naam** | Ja | <*door de gebruiker toegewezen identiteits naam*> | De naam om uw door de gebruiker toegewezen identiteit te geven. In dit voorbeeld wordt `Fabrikam-user-assigned-identity` gebruikt. |
   |||||

   Nadat u deze gegevens hebt gevalideerd, maakt Azure uw beheerde identiteit. U kunt nu de door de gebruiker toegewezen identiteit toevoegen aan uw logische app. U kunt niet meer dan één door de gebruiker toegewezen identiteit toevoegen aan uw logische app.

1. Zoek in de Azure Portal uw logische app in de ontwerp functie voor logische apps en open deze.

1. Selecteer onder **instellingen** in het menu van de logische app de optie **identiteit** en selecteer vervolgens **aan gebruiker toegewezen**  >  **toevoegen**.

   ![Door de gebruiker toegewezen beheerde identiteit toevoegen](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. Selecteer uw Azure-abonnement in het deel venster door de **gebruiker toegewezen beheerde identiteit toevoegen** in de lijst **abonnement** als dat nog niet is gebeurd. Zoek in de lijst waarin *alle* beheerde identiteiten in het abonnement worden weer gegeven de door de gebruiker toegewezen identiteit en selecteer deze. Als u de lijst wilt filteren, voert u in het zoekvak door de **gebruiker toegewezen beheerde identiteiten** de naam in voor de identiteit of de resource groep. Wanneer u klaar bent, selecteert u **toevoegen**.

   ![De door de gebruiker toegewezen identiteit selecteren die moet worden gebruikt](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Als u een fout bericht krijgt dat u slechts één beheerde identiteit kunt hebben, is uw logische app al gekoppeld aan de door het systeem toegewezen identiteit. Voordat u de door de gebruiker toegewezen identiteit kunt toevoegen, moet u eerst de door het systeem toegewezen identiteit uitschakelen in uw logische app.

   Uw logische app is nu gekoppeld aan de door de gebruiker toegewezen beheerde identiteit.

   ![Koppeling met door gebruiker toegewezen identiteit](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Volg nu de [stappen voor het verlenen van toegang tot de resource met de-id](#access-other-resources) verderop in dit onderwerp.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Een door de gebruiker toegewezen identiteit maken in een Azure Resource Manager sjabloon

Voor het automatiseren van het maken en implementeren van Azure-resources, zoals Logic apps, kunt u [Azure Resource Manager sjablonen](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)gebruiken die door de [gebruiker toegewezen identiteiten voor verificatie](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)ondersteunen. In de sectie van uw sjabloon `resources` zijn de resource definitie van de logische app vereist voor de volgende items:

* Een `identity` object waarvan de `type` eigenschap is ingesteld op `UserAssigned`

* Een onderliggend `userAssignedIdentities` object dat de door de gebruiker toegewezen resource en naam specificeert

Dit voor beeld toont een resource definitie van een logische app voor een HTTP PUT-aanvraag en bevat een niet-geparametriseerde `identity` object. De reactie op de PUT-aanvraag en de volgende GET-bewerking hebben dit `identity` object ook:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

Als uw sjabloon ook de resource definitie van de beheerde identiteit bevat, kunt u het `identity` object para meters. In dit voor beeld ziet u hoe het onderliggende `userAssignedIdentities` object verwijst naar een `userAssignedIdentity` variabele die u in de sectie van uw sjabloon definieert `variables` . Met deze variabele wordt verwezen naar de resource-ID voor de door de gebruiker toegewezen identiteit.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {}
      }
  ]
}
```

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Identiteits toegang geven tot resources

Voordat u de beheerde identiteit van de logische app voor verificatie kunt gebruiken, moet u de toegang instellen voor die identiteit op de Azure-resource waar u de identiteit wilt gebruiken. Als u deze taak wilt volt ooien, wijst u de juiste rol toe aan die identiteit op de Azure-doel resource. Dit zijn de opties die u kunt gebruiken:

* [Azure Portal](#azure-portal-assign-access)
* [Azure Resource Manager-sjabloon](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)): Zie [roltoewijzing toevoegen met behulp van Azure RBAC en Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)voor meer informatie.
* Azure CLI ([AZ Role Assignment maken](/cli/azure/role/assignment?view=azure-cli-latest&preserve-view=true#az-role-assignment-create)): Zie [roltoewijzing toevoegen met behulp van Azure RBAC en Azure cli](../role-based-access-control/role-assignments-cli.md)voor meer informatie.
* [Azure REST-API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Toegang toewijzen in de Azure Portal

1. Ga in het [Azure Portal](https://portal.azure.com)naar de Azure-resource waar u de beheerde identiteit toegang wilt geven.

1. Selecteer in het menu resource **toegangs beheer (IAM)**  >  **roltoewijzingen** waar u de huidige roltoewijzingen voor die resource kunt controleren. **Selecteer op** de werk balk de optie  >  **roltoewijzing** toevoegen.

   ![Selecteer > functie toewijzing toevoegen.](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Als de optie **roltoewijzing toevoegen** is uitgeschakeld, hebt u waarschijnlijk niet de juiste machtigingen. Zie [Administrator role permissions](../active-directory/roles/permissions-reference.md)(Engelstalig) in azure Active Directory voor meer informatie over de machtigingen waarmee u rollen voor resources kunt beheren.

1. Selecteer onder roltoewijzing **toevoegen** een **rol** die uw identiteit de benodigde toegang tot de doel resource geeft.

   Voor het voor beeld van dit onderwerp moet uw identiteit een [rol hebben die toegang heeft tot de BLOB in een Azure storage-container](../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights). Selecteer dus de rol van **BLOB voor gegevens** koppeling voor de beheerde identiteit.

   ![Selecteer de rol ' BLOB data Inzender voor opslag '](./media/create-managed-service-identity/select-role-for-identity.png)

1. Volg deze stappen voor uw beheerde identiteit:

   * **Door het systeem toegewezen identiteit**

     1. Selecteer in het vak **toegang toewijzen aan** de optie **logische app**. Wanneer de eigenschap **abonnement** wordt weer gegeven, selecteert u het Azure-abonnement dat is gekoppeld aan uw identiteit.

        ![Toegang selecteren voor door het systeem toegewezen identiteit](./media/create-managed-service-identity/assign-access-system.png)

     1. Selecteer in het vak **selecteren** de logische app in de lijst. Als de lijst te lang is, gebruikt u het **selectie** vakje om de lijst te filteren.

        ![Logische app selecteren voor door het systeem toegewezen identiteit](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Door gebruiker toegewezen identiteit**

     1. Selecteer door de **gebruiker toegewezen beheerde identiteit** in het vak **toegang toewijzen aan** . Wanneer de eigenschap **abonnement** wordt weer gegeven, selecteert u het Azure-abonnement dat is gekoppeld aan uw identiteit.

        ![Toegang selecteren voor door de gebruiker toegewezen identiteit](./media/create-managed-service-identity/assign-access-user.png)

     1. Selecteer in het vak **selecteren** uw identiteit in de lijst. Als de lijst te lang is, gebruikt u het **selectie** vakje om de lijst te filteren.

        ![De door de gebruiker toegewezen identiteit selecteren](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Selecteer **Opslaan** als u klaar bent.

   De lijst met roltoewijzingen van de doel resource bevat nu de geselecteerde beheerde identiteit en rol. In dit voor beeld ziet u hoe u de door het systeem toegewezen identiteit kunt gebruiken voor één logische app en een door de gebruiker toegewezen identiteit voor een groep andere logische apps.

   ![Beheerde identiteiten en rollen toegevoegd aan doel bron](./media/create-managed-service-identity/added-roles-for-identities.png)

   [Wijs een beheerde identiteits toegang toe aan een bron met behulp van de Azure Portal](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)voor meer informatie.

1. Volg nu de [stappen om toegang te verifiëren met de identiteit](#authenticate-access-with-identity) in een trigger of actie die beheerde identiteiten ondersteunt.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Toegang verifiëren met beheerde identiteit

Nadat u [de beheerde identiteit voor uw logische app hebt ingeschakeld](#azure-portal-system-logic-app) en [deze identiteit toegang geeft tot de doel resource of entiteit](#access-other-resources), kunt u die identiteit gebruiken in [Triggers en acties die beheerde identiteiten ondersteunen](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

> [!IMPORTANT]
> Als u een Azure-functie hebt waarbij u de door het systeem toegewezen identiteit wilt gebruiken, moet u eerst [verificatie voor Azure functions inschakelen](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-functions).

Deze stappen laten zien hoe u de beheerde identiteit met een trigger of actie kunt gebruiken via de Azure Portal. Zie [beheerde identiteits verificatie](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)voor het opgeven van de beheerde identiteit in een trigger of de onderliggende JSON-definitie van een actie.

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Als u dit nog niet hebt gedaan, voegt u de [trigger of actie toe die beheerde identiteiten ondersteunt](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   De HTTP-trigger of actie kan bijvoorbeeld de door het systeem toegewezen identiteit gebruiken die u hebt ingeschakeld voor uw logische app. Over het algemeen gebruikt de HTTP-trigger of actie deze eigenschappen om de resource of entiteit op te geven waartoe u toegang wilt krijgen:

   | Eigenschap | Vereist | Beschrijving |
   |----------|----------|-------------|
   | **Methode** | Ja | De HTTP-methode die wordt gebruikt door de bewerking die u wilt uitvoeren |
   | **URI** | Ja | De eind punt-URL voor toegang tot de Azure-doel bron of-entiteit. De URI-syntaxis bevat doorgaans de [resource-id](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) voor de Azure-resource of-service. |
   | **Kopteksten** | Nee | Eventuele header waarden die u nodig hebt of wilt toevoegen in de uitgaande aanvraag, zoals het inhouds type |
   | **Query's** | Nee | Alle query parameters die u nodig hebt of wilt toevoegen in de aanvraag, zoals de para meter voor een specifieke bewerking of de API-versie voor de bewerking die u wilt uitvoeren |
   | **Verificatie** | Ja | Het verificatie type dat moet worden gebruikt voor het verifiëren van de toegang tot de doel bron of entiteit |
   ||||

   Stel dat u de bewerking voor het maken van een [moment opname-BLOB](/rest/api/storageservices/snapshot-blob) wilt uitvoeren op een BLOB in het Azure Storage account waar u eerder toegang hebt ingesteld voor uw identiteit. De [Azure Blob Storage-connector](/connectors/azureblob/) biedt deze bewerking echter momenteel niet. In plaats daarvan kunt u deze bewerking uitvoeren met behulp van de [http-actie](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) of een andere [Blob-service rest API bewerking](/rest/api/storageservices/operations-on-blobs).

   > [!IMPORTANT]
   > Om toegang te krijgen tot Azure Storage-accounts achter firewalls met behulp van HTTP-aanvragen en beheerde identiteiten, moet u ervoor zorgen dat u ook uw opslag account hebt ingesteld met de [uitzonde ring die toegang verleent aan vertrouwde micro soft-Services](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

   Als u de [BLOB-bewerking van de moment opname](/rest/api/storageservices/snapshot-blob)wilt uitvoeren, geeft de http-actie deze eigenschappen op:

   | Eigenschap | Vereist | Voorbeeldwaarde | Beschrijving |
   |----------|----------|---------------|-------------|
   | **Methode** | Ja | `PUT`| De HTTP-methode die wordt gebruikt door de BLOB-bewerking van de moment opname |
   | **URI** | Ja | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | De resource-ID voor een Azure Blob Storage-bestand in de Azure Global (open bare) omgeving, die gebruikmaakt van deze syntaxis |
   | **Kopteksten** | Voor Azure Storage | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` <p>`x-ms-date` = `@{formatDateTime(utcNow(),'r'}` | De `x-ms-blob-type` `x-ms-version` waarden, en en `x-ms-date` header zijn vereist voor Azure Storage bewerkingen. <p><p>**Belang rijk**: in uitgaande HTTP-trigger-en actie aanvragen voor Azure Storage vereist de header de `x-ms-version` eigenschap en de API-versie voor de bewerking die u wilt uitvoeren. De `x-ms-date` moet de huidige datum zijn. Anders mislukt de logische app met een `403 FORBIDDEN` fout. Als u de huidige datum wilt ophalen in de vereiste indeling, kunt u de expressie in de voorbeeld waarde gebruiken. <p>Raadpleeg de volgende onderwerpen voor meer informatie: <p><p>- [Aanvraag headers-moment opname-BLOB](/rest/api/storageservices/snapshot-blob#request) <br>- [Versie beheer voor Azure Storage services](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Query's** | Alleen voor de moment opname-BLOB-bewerking | `comp` = `snapshot` | De naam en waarde van de query parameter voor de bewerking. |
   |||||

   Hier volgt een voor beeld van een HTTP-actie waarin al deze eigenschaps waarden worden weer gegeven:

   ![Een HTTP-actie toevoegen voor toegang tot een Azure-resource](./media/create-managed-service-identity/http-action-example.png)

1. Voeg nu de eigenschap **Authentication** toe aan de http-actie. Selecteer in de lijst **nieuwe para meter toevoegen** de optie **verificatie**.

   ![De eigenschap Authentication toevoegen aan de HTTP-actie](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Niet alle triggers en acties bieden ondersteuning voor het toevoegen van een verificatie type. Zie [verificatie toevoegen aan uitgaande oproepen](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)voor meer informatie.

1. Selecteer **beheerde identiteit** in de lijst **verificatie type** .

   ![Voor ' verificatie ' selecteert u ' beheerde identiteit '](./media/create-managed-service-identity/select-managed-identity.png)

1. Selecteer in de lijst beheerde identiteit een van de beschik bare opties op basis van uw scenario.

   * Als u de door het systeem toegewezen identiteit hebt ingesteld, selecteert u door het **systeem toegewezen beheerde identiteit** als dat nog niet is gebeurd.

     ![Selecteer de door het systeem toegewezen beheerde identiteit](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Als u een door de gebruiker toegewezen identiteit instelt, selecteert u die identiteit als deze nog niet is geselecteerd.

     ![De door de gebruiker toegewezen identiteit selecteren](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   In dit voor beeld wordt de door het **systeem toegewezen beheerde identiteit** voortgezet.

1. Bij sommige triggers en acties wordt de eigenschap **doel groep** ook weer gegeven om de doel resource-id in te stellen. Stel de eigenschap **doel groep** in op de [resource-id voor de doel resource of-service](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). Anders wordt de eigenschap **doel groep** standaard gebruikt voor de `https://management.azure.com/` resource-id. Dit is de resource-id voor Azure Resource Manager.

   > [!IMPORTANT]
   > Zorg ervoor dat de doel Resource-ID *precies overeenkomt* met de waarde die Azure Active Directory (AD) verwacht, inclusief alle vereiste afsluitende slashes. De resource-ID voor alle Azure Blob Storage-accounts vereist bijvoorbeeld een afsluitende slash. De resource-ID voor een specifiek opslag account vereist echter geen afsluitende slash. Controleer de [resource-id's voor de Azure-Services die ondersteuning bieden voor Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   In dit voor beeld wordt de eigenschap **doel groep** ingesteld op `https://storage.azure.com/` zodat de toegangs tokens die worden gebruikt voor verificatie geldig zijn voor alle opslag accounts. U kunt echter ook de URL van de basis service `https://fabrikamstorageaccount.blob.core.windows.net` voor een specifiek opslag account opgeven.

   ![Eigenschap doel groep instellen op doel Resource-ID](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Zie de volgende onderwerpen voor meer informatie over het verlenen van toegang met Azure AD voor Azure Storage:

   * [Toegang tot Azure-blobs en-wacht rijen toestaan met behulp van Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Toegang tot Azure Storage autoriseren met Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Ga door met het bouwen van de logische app op de gewenste manier.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Beheerde identiteit uitschakelen

Als u een beheerde identiteit voor uw logische app niet meer wilt gebruiken, hebt u de volgende opties:

* [Azure Portal](#azure-portal-disable)
* [Azure Resource Manager sjablonen](#template-disable)
* Azure PowerShell
  * [Roltoewijzing verwijderen](../role-based-access-control/role-assignments-powershell.md)
  * [Door de gebruiker toegewezen identiteit verwijderen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [Roltoewijzing verwijderen](../role-based-access-control/role-assignments-cli.md)
  * [Door de gebruiker toegewezen identiteit verwijderen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Azure REST-API
  * [Roltoewijzing verwijderen](../role-based-access-control/role-assignments-rest.md)
  * [Door de gebruiker toegewezen identiteit verwijderen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Als u uw logische app verwijdert, wordt de beheerde identiteit door Azure automatisch uit Azure AD verwijderd.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Beheerde identiteit in de Azure Portal uitschakelen

In de Azure Portal verwijdert u eerst de toegang van de identiteit tot [uw doel resource](#disable-identity-target-resource). Schakel vervolgens de door het systeem toegewezen identiteit uit of verwijder de door de gebruiker toegewezen identiteit uit [uw logische app](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Identiteits toegang verwijderen uit resources

1. Ga in het [Azure Portal](https://portal.azure.com)naar de doel-Azure-resource waar u de toegang tot de beheerde identiteit wilt verwijderen.

1. Selecteer **toegangs beheer (IAM)** in het menu van de doel resource. Selecteer op de werk balk **roltoewijzingen**.

1. Selecteer in de lijst rollen de beheerde identiteiten die u wilt verwijderen. Selecteer **verwijderen** op de werk balk.

   > [!TIP]
   > Als de optie **verwijderen** is uitgeschakeld, hebt u waarschijnlijk niet de juiste machtigingen. Zie [Administrator role permissions](../active-directory/roles/permissions-reference.md)(Engelstalig) in azure Active Directory voor meer informatie over de machtigingen waarmee u rollen voor resources kunt beheren.

De beheerde identiteit wordt nu verwijderd en heeft geen toegang meer tot de doel resource.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Beheerde identiteit voor logische app uitschakelen

1. Open in de [Azure Portal](https://portal.azure.com)uw logische app in de ontwerp functie voor logische apps.

1. Selecteer onder **instellingen** in het menu van de logische app de optie **identiteit** en volg de stappen voor uw identiteit:

   * Selecteer **systeem toegewezen**  >  **bij**  >  **Opslaan**. Wanneer u wordt gevraagd om te bevestigen, selecteert u **Ja**.

     ![De door het systeem toegewezen identiteit uitschakelen](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Selecteer de **gebruiker die is toegewezen** en de beheerde identiteit, en selecteer vervolgens **verwijderen**. Wanneer u wordt gevraagd om te bevestigen, selecteert u **Ja**.

     ![De door de gebruiker toegewezen identiteit verwijderen](./media/create-managed-service-identity/remove-user-assigned-identity.png)

De beheerde identiteit is nu uitgeschakeld in uw logische app.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Beheerde identiteit in Azure Resource Manager sjabloon uitschakelen

Als u de beheerde identiteit van de logische app hebt gemaakt met behulp van een Azure Resource Manager sjabloon, stelt u de `identity` onderliggende eigenschap van het object `type` in op `None` .

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Volgende stappen

* [Beveiligde toegang en gegevens in Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
