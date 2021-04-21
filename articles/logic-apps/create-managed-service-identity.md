---
title: Verifiëren met beheerde identiteiten
description: Toegang tot resources die zijn beveiligd Azure Active Directory zonder u aan te melden met referenties of geheimen met behulp van een beheerde identiteit
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm, azla
ms.topic: article
ms.date: 03/30/2021
ms.openlocfilehash: 8e081257d70c9bc9c9f75df18b30f8dcf119e48e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763339"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Toegang tot Azure-resources verifiëren met behulp van beheerde identiteiten in Azure Logic Apps

Als u eenvoudig toegang wilt krijgen tot andere resources die zijn beveiligd door Azure Active Directory (Azure AD) en uw identiteit wilt verifiëren, kan uw logische app gebruikmaken van een beheerde identiteit [(voorheen](../active-directory/managed-identities-azure-resources/overview.md) Managed Service Identity of MSI) in plaats van referenties, geheimen of Azure AD-tokens. Azure beheert deze identiteit voor u en helpt uw referenties te beveiligen omdat u geen geheimen hoeft te beheren of rechtstreeks Azure AD-tokens hoeft te gebruiken.

Azure Logic Apps biedt ondersteuning [*voor door het systeem toegewezen*](../active-directory/managed-identities-azure-resources/overview.md) en door de gebruiker [*toegewezen*](../active-directory/managed-identities-azure-resources/overview.md) beheerde identiteiten. Uw logische app of afzonderlijke verbindingen kunnen de door het  systeem toegewezen identiteit of één door de gebruiker toegewezen identiteit gebruiken, die u kunt delen in een groep logische apps, maar niet beide.

<a name="triggers-actions-managed-identity"></a>

## <a name="where-can-logic-apps-use-managed-identities"></a>Waar kunnen logische apps beheerde identiteiten gebruiken?

Op dit moment kunnen alleen specifieke [ingebouwde triggers](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) en acties en specifieke beheerde [connectors](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions) die Azure AD OAuth ondersteunen, een beheerde identiteit gebruiken voor verificatie. Hier is bijvoorbeeld een selectie:

<a name="built-in-managed-identity"></a>

**Ingebouwde triggers en acties**

* Azure API Management
* Azure App Services
* Azure Functions
* HTTP
* HTTP + Webhook

> [!NOTE]
> Hoewel de HTTP-trigger en -actie verbindingen met Azure Storage-accounts achter Azure-firewalls kunnen verifiëren met behulp van de door het systeem toegewezen beheerde identiteit, kunnen ze de door de gebruiker toegewezen beheerde identiteit niet gebruiken om dezelfde verbindingen te verifiëren.

<a name="managed-connectors-managed-identity"></a>

**Beheerde connectors**

* Azure Automation
* Azure Event Grid
* Azure Key Vault
* Azure Resource Manager
* HTTP met Azure AD

Ondersteuning voor beheerde connectors is momenteel beschikbaar als preview-versie. Zie Verificatietypen voor triggers en acties die verificatie ondersteunen voor de [huidige lijst.](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

In dit artikel wordt beschreven hoe u beide soorten beheerde identiteiten kunt instellen voor uw logische app. Raadpleeg de volgende onderwerpen voor meer informatie:

* [Triggers en acties die beheerde identiteiten ondersteunen](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)
* [Limieten voor beheerde identiteiten voor logische apps](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Azure-services die Ondersteuning bieden voor Azure AD-verificatie met beheerde identiteiten](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. Als u nog geen abonnement hebt, [meld u dan aan voor een gratis Azure-account](https://azure.microsoft.com/free/). Zowel de beheerde identiteit als de Azure-doelresource waar u toegang nodig hebt, moeten hetzelfde Azure-abonnement gebruiken.

* Als u een beheerde identiteit toegang wilt geven tot een Azure-resource, moet u een rol toevoegen aan de doelresource voor die identiteit. Als u rollen wilt toevoegen, hebt u [Azure AD-beheerdersmachtigingen](../active-directory/roles/permissions-reference.md) nodig die rollen kunnen toewijzen aan identiteiten in de bijbehorende Azure AD-tenant.

* De Azure-doelresource die u wilt openen. Aan deze resource voegt u een rol toe voor de beheerde identiteit, waarmee de logische app de toegang tot de doelresource kan verifiëren.

* De logische app waar u de trigger of acties wilt [gebruiken die beheerde identiteiten ondersteunen.](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

## <a name="enable-managed-identity"></a>Beheerde identiteit inschakelen

Als u de beheerde identiteit wilt instellen die u wilt gebruiken, volgt u de koppeling voor die identiteit:

* [Door het systeem toegewezen identiteit](#system-assigned)
* [Door de gebruiker toegewezen identiteit](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Door het systeem toegewezen identiteit inschakelen

In tegenstelling tot door de gebruiker toegewezen identiteiten hoeft u de door het systeem toegewezen identiteit niet handmatig te maken. Als u de door het systeem toegewezen identiteit voor uw logische app wilt instellen, kunt u de volgende opties gebruiken:

* [Azure-portal](#azure-portal-system-logic-app)
* [Azure Resource Manager-sjablonen](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Door het systeem toegewezen identiteit inschakelen in Azure Portal

1. Open in [Azure Portal](https://portal.azure.com)de logische app in Logic App Designer.

1. Selecteer in het menu van de logische app **onder Instellingen** de **optie Identiteit.** Selecteer **Systeem toegewezen**  >  **op**  >  **Opslaan.** Wanneer Azure u vraagt om te bevestigen, selecteert u **Ja.**

   ![De door het systeem toegewezen identiteit inschakelen](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Als er een foutbericht wordt weergegeven dat u slechts één beheerde identiteit kunt hebben, is uw logische app al gekoppeld aan de door de gebruiker toegewezen identiteit. Voordat u de door het systeem toegewezen identiteit  kunt toevoegen, moet u eerst de door de gebruiker toegewezen identiteit verwijderen uit uw logische app.

   Uw logische app kan nu gebruikmaken van de door het systeem toegewezen identiteit, die is geregistreerd bij Azure AD en wordt vertegenwoordigd door een object-id.

   ![Object-id voor door het systeem toegewezen identiteit](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Eigenschap | Waarde | Beschrijving |
   |----------|-------|-------------|
   | **Object-id** | <*identity-resource-ID*> | Een Globally Unique Identifier (GUID) die de door het systeem toegewezen identiteit voor uw logische app in een Azure AD-tenant vertegenwoordigt |
   ||||

1. Volg nu de [stappen die die identiteit toegang geven tot de resource](#access-other-resources) verder in dit onderwerp.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Door het systeem toegewezen identiteit inschakelen in Azure Resource Manager sjabloon

Als u het maken en implementeren van Azure-resources, zoals logische apps, wilt automatiseren, kunt u Azure Resource Manager [gebruiken.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) Als u de door het systeem toegewezen beheerde identiteit voor uw logische app wilt inschakelen in de sjabloon, voegt u het object en de onderliggende eigenschap toe aan de resourcedefinitie van de logische app in de `identity` `type` sjabloon, bijvoorbeeld:

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

Wanneer Azure de resourcedefinitie van uw logische app maakt, `identity` krijgt het object de volgende aanvullende eigenschappen:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Eigenschap (JSON) | Waarde | Beschrijving |
|-----------------|-------|-------------|
| `principalId` | <*principal-id*> | De Globally Unique Identifier (GUID) van het service-principal-object voor de beheerde identiteit die uw logische app in de Azure AD-tenant vertegenwoordigt. Deze GUID wordt soms weergegeven als een 'object-id' of `objectID` . |
| `tenantId` | <*Azure-AD-tenant-ID*> | De Globally Unique Identifier (GUID) die staat voor de Azure AD-tenant waar de logische app nu lid is. In de Azure AD-tenant heeft de service-principal dezelfde naam als het exemplaar van de logische app. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Door de gebruiker toegewezen identiteit inschakelen

Als u een door de gebruiker toegewezen beheerde identiteit voor uw logische app wilt instellen, moet u die identiteit eerst maken als een afzonderlijke zelfstandige Azure-resource. Dit zijn de opties die u kunt gebruiken:

* [Azure-portal](#azure-portal-user-identity)
* [Azure Resource Manager-sjablonen](#template-user-identity)
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

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Door de gebruiker toegewezen identiteit maken in de Azure Portal

1. Voer in [Azure Portal](https://portal.azure.com)in het zoekvak op een pagina in `managed identities` en selecteer **Beheerde identiteiten.**

   ![Beheerde identiteiten zoeken en selecteren](./media/create-managed-service-identity/find-select-managed-identities.png)

1. Selecteer **onder Beheerde identiteiten** de optie **Toevoegen.**

   ![Nieuwe beheerde identiteit toevoegen](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Geef informatie op over uw beheerde identiteit en selecteer **beoordelen en maken,** bijvoorbeeld:

   ![Door de gebruiker toegewezen beheerde identiteit maken](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Abonnement** | Ja | <*Azure-subscription-name*> | De naam voor het te gebruiken Azure-abonnement |
   | **Resourcegroep** | Ja | <*Naam-van-Azure-resourcegroep*> | De naam voor de resourcegroep die moet worden gebruikt. Maak een nieuwe groep of selecteer een bestaande groep. In dit voorbeeld wordt een nieuwe groep met de naam `fabrikam-managed-identities-RG` gemaakt. |
   | **Regio** | Ja | <*Azure-regio*> | De Azure-regio waar informatie over uw resource moet worden opgeslagen. In dit voorbeeld wordt 'US - west' gebruikt. |
   | **Naam** | Ja | <*user-assigned-identity-name*> | De naam die aan uw door de gebruiker toegewezen identiteit moet worden gegeven. In dit voorbeeld wordt `Fabrikam-user-assigned-identity` gebruikt. |
   |||||

   Na het valideren van deze gegevens maakt Azure uw beheerde identiteit. U kunt nu de door de gebruiker toegewezen identiteit toevoegen aan uw logische app. U kunt niet meer dan één door de gebruiker toegewezen identiteit toevoegen aan uw logische app.

1. Zoek en Azure Portal logische app in Logic App Designer.

1. Selecteer in het menu van de logische app **onder Instellingen** **de** optie Identiteit en selecteer vervolgens Door de **gebruiker toegewezen**  >  **toevoegen.**

   ![Door de gebruiker toegewezen beheerde identiteit toevoegen](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. Selecteer in **het deelvenster Door de gebruiker toegewezen beheerde identiteit toevoegen** in de lijst Abonnement uw Azure-abonnement als dit nog niet is geselecteerd.  Zoek en selecteer in de *lijst* met alle beheerde identiteiten in dat abonnement de door de gebruiker toegewezen identiteit die u wilt. Als u de lijst wilt filteren, voert u in het zoekvak **Door de** gebruiker toegewezen beheerde identiteiten de naam in voor de identiteit of resourcegroep. Wanneer u klaar bent, selecteert u **Toevoegen.**

   ![Selecteer de door de gebruiker toegewezen identiteit die u wilt gebruiken](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Als er een foutbericht wordt weergegeven dat u slechts één beheerde identiteit kunt hebben, is uw logische app al gekoppeld aan de door het systeem toegewezen identiteit. Voordat u de door de gebruiker toegewezen identiteit kunt toevoegen, moet u eerst de door het systeem toegewezen identiteit in uw logische app uitschakelen.

   Uw logische app is nu gekoppeld aan de door de gebruiker toegewezen beheerde identiteit.

   ![Associatie met door de gebruiker toegewezen identiteit](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Volg nu de [stappen die die identiteit toegang geven tot de resource](#access-other-resources) verder in dit onderwerp.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Een door de gebruiker toegewezen identiteit maken in een Azure Resource Manager sjabloon

Als u het maken en implementeren van Azure-resources, zoals logische apps, [wilt](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)automatiseren, kunt u Azure Resource Manager gebruiken, die ondersteuning bieden voor door de gebruiker toegewezen [identiteiten voor verificatie.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) In de sectie van uw sjabloon zijn de volgende items vereist voor de resourcedefinitie van uw logische `resources` app:

* Een `identity` object met de eigenschap ingesteld `type` op `UserAssigned`

* Een onderliggend `userAssignedIdentities` object dat de door de gebruiker toegewezen resource en naam specificeert

In dit voorbeeld ziet u de resourcedefinitie van een logische app voor een HTTP PUT-aanvraag en een niet-geparameteriseerd `identity` object. Het antwoord op de PUT-aanvraag en de volgende GET-bewerking hebben ook dit `identity` object:

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

Als uw sjabloon ook de resourcedefinitie van de beheerde identiteit bevat, kunt u het `identity` object parameteriseren. In dit voorbeeld ziet u hoe het `userAssignedIdentities` onderliggende object verwijst naar een `userAssignedIdentity` variabele die u in de sectie van uw sjabloon definieert. `variables` Deze variabele verwijst naar de resource-id voor uw door de gebruiker toegewezen identiteit.

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

## <a name="give-identity-access-to-resources"></a>Identiteit toegang geven tot resources

Voordat u de beheerde identiteit van uw logische app kunt gebruiken voor verificatie, moet u toegang instellen voor die identiteit in de Azure-resource waar u de identiteit wilt gebruiken. Als u deze taak wilt voltooien, wijst u de juiste rol toe aan die identiteit op de Azure-doelresource. Dit zijn de opties die u kunt gebruiken:

* [Azure-portal](#azure-portal-assign-access)
* [Azure Resource Manager-sjabloon](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)) - Zie Roltoewijzing toevoegen met behulp van [Azure RBAC](../role-based-access-control/role-assignments-powershell.md)en Azure PowerShell .
* Azure CLI ([az role assignment create](/cli/azure/role/assignment#az_role_assignment_create)) - Zie Roltoewijzing toevoegen met behulp van Azure [RBAC en Azure CLI voor meer informatie.](../role-based-access-control/role-assignments-cli.md)
* [Azure REST-API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Toegang toewijzen in de Azure Portal

Geef op de Azure-doelresource waar u de beheerde identiteit toegang wilt, die identiteit op rollen gebaseerde toegang tot de doelresource.

1. Ga in [Azure Portal](https://portal.azure.com)naar de Azure-resource waar u uw beheerde identiteit toegang wilt geven.

1. Selecteer in het menu van de resource Toegangsbeheer **(IAM) Roltoewijzingen** waar u de huidige roltoewijzingen  >   voor die resource kunt controleren. Selecteer op de werkbalk **Roltoewijzing**  >  **toevoegen.**

   ![Selecteer Toevoegen > roltoewijzing toevoegen](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Als de **optie Roltoewijzing** toevoegen is uitgeschakeld, hebt u waarschijnlijk geen machtigingen. Zie Beheerdersrolmachtigingen in Azure Active Directory voor meer informatie over de machtigingen voor het [beheren van rollen Azure Active Directory.](../active-directory/roles/permissions-reference.md)

1. Selecteer **onder Roltoewijzing** toevoegen een **rol** die uw identiteit de benodigde toegang geeft tot de doelresource.

   Voor het voorbeeld van dit onderwerp heeft uw identiteit een rol nodig die toegang heeft  tot de blob in een [Azure Storage-container.](../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights)Selecteer daarom de rol Bijdrager voor opslagblobgegevens voor de beheerde identiteit.

   ![Selecteer de rol Bijdrager voor opslagblobgegevens](./media/create-managed-service-identity/select-role-for-identity.png)

1. Volg deze stappen voor uw beheerde identiteit:

   * **Door het systeem toegewezen identiteit**

     1. Selecteer logische **app in** het vak Toegang **toewijzen aan.** Wanneer de **eigenschap Abonnement** wordt weergegeven, selecteert u het Azure-abonnement dat is gekoppeld aan uw identiteit.

        ![Toegang selecteren voor door het systeem toegewezen identiteit](./media/create-managed-service-identity/assign-access-system.png)

     1. Selecteer onder **het** vak Selecteren uw logische app in de lijst. Als de lijst te lang is, gebruikt u het **vak Selecteren** om de lijst te filteren.

        ![Logische app selecteren voor door het systeem toegewezen identiteit](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Door de gebruiker toegewezen identiteit**

     1. Selecteer in **het vak Toegang toewijzen** aan de optie Door de gebruiker toegewezen **beheerde identiteit.** Wanneer de **eigenschap Abonnement** wordt weergegeven, selecteert u het Azure-abonnement dat is gekoppeld aan uw identiteit.

        ![Selecteer toegang voor door de gebruiker toegewezen identiteit](./media/create-managed-service-identity/assign-access-user.png)

     1. Selecteer in **het** vak Selecteren uw identiteit in de lijst. Als de lijst te lang is, gebruikt u het **vak Selecteren** om de lijst te filteren.

        ![Selecteer uw door de gebruiker toegewezen identiteit](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Selecteer **Opslaan** als u klaar bent.

   In de lijst met roltoewijzingen van de doelresource worden nu de geselecteerde beheerde identiteit en rol weergegeven. In dit voorbeeld ziet u hoe u de door het systeem toegewezen identiteit kunt gebruiken voor één logische app en een door de gebruiker toegewezen identiteit voor een groep andere logische apps.

   ![Beheerde identiteiten en rollen toegevoegd aan doelresource](./media/create-managed-service-identity/added-roles-for-identities.png)

   Voor meer informatie wijst [u een beheerde identiteit toegang tot een resource](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)toe met behulp van de Azure Portal .

1. Volg nu de [stappen voor het verifiëren van toegang met de identiteit](#authenticate-access-with-identity) in een trigger of actie die beheerde identiteiten ondersteunt.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Toegang verifiëren met beheerde identiteit

Nadat u de beheerde identiteit voor uw logische [app](#azure-portal-system-logic-app) hebt ingeschakeld en die identiteit toegang hebt verlenen tot de doelresource of -entiteit, [](#access-other-resources)kunt u die identiteit gebruiken in triggers en acties die beheerde identiteiten [ondersteunen.](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

> [!IMPORTANT]
> Als u een Azure-functie hebt waarin u de door het systeem toegewezen identiteit wilt gebruiken, moet u eerst [verificatie inschakelen voor Azure Functions.](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-functions)

Deze stappen laten zien hoe u de beheerde identiteit gebruikt met een trigger of actie via de Azure Portal. Zie Beheerde identiteitsverificatie als u de beheerde identiteit wilt opgeven in de onderliggende JSON-definitie van een trigger of [actie.](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)

1. Open in [Azure Portal](https://portal.azure.com)de logische app in de ontwerpfunctie voor logische apps.

1. Als u dit nog niet hebt gedaan, voegt u de trigger of actie [toe die beheerde identiteiten ondersteunt.](logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

   > [!NOTE]
   > Niet alle triggers en acties ondersteunen het toevoegen van een verificatietype. Zie Verificatietypen voor triggers en acties die [verificatie ondersteunen voor meer informatie.](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

1. Volg deze stappen voor de trigger of actie die u hebt toegevoegd:

   * **Ingebouwde triggers en acties die ondersteuning bieden voor het gebruik van een beheerde identiteit**

     1. Voeg de **eigenschap Authentication** toe als de eigenschap nog niet wordt weergegeven.

     1. Selecteer **beheerde** identiteit **onder Verificatietype.**

     Zie [Example: Authenticate built-in trigger or action with a managed identity (Voorbeeld: ingebouwde trigger of actie verifiëren met een beheerde identiteit) voor meer informatie.](#authenticate-built-in-managed-identity)
 
   * **Triggers en acties van beheerde connectors die ondersteuning bieden voor het gebruik van een beheerde identiteit**

     1. Selecteer op de tenantselectiepagina de optie **Verbinding maken met beheerde identiteit.**

     1. Geef op de volgende pagina een verbindingsnaam op.

        Standaard toont de lijst met beheerde identiteiten alleen de momenteel ingeschakelde beheerde identiteit, omdat een logische app ondersteuning biedt voor het inschakelen van slechts één beheerde identiteit tegelijk, bijvoorbeeld:

        ![Schermopname van de pagina verbindingsnaam en de geselecteerde beheerde identiteit.](./media/create-managed-service-identity/system-assigned-managed-identity.png)

     Zie Voor meer informatie Voorbeeld: Trigger of actie voor [beheerde connector verifiëren met een beheerde identiteit.](#authenticate-managed-connector-managed-identity)

<a name="authenticate-built-in-managed-identity"></a>

#### <a name="example-authenticate-built-in-trigger-or-action-with-a-managed-identity"></a>Voorbeeld: Ingebouwde trigger of actie verifiëren met een beheerde identiteit

De HTTP-trigger of -actie kan de door het systeem toegewezen identiteit gebruiken die u hebt ingeschakeld voor uw logische app. In het algemeen gebruikt de HTTP-trigger of -actie deze eigenschappen om de resource of entiteit op te geven die u wilt openen:

| Eigenschap | Vereist | Beschrijving |
|----------|----------|-------------|
| **Methode** | Yes | De HTTP-methode die wordt gebruikt door de bewerking die u wilt uitvoeren |
| **URI** | Yes | De eindpunt-URL voor toegang tot de Azure-doelresource of -entiteit. De URI-syntaxis bevat doorgaans de [resource-id](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) voor de Azure-resource of -service. |
| **Kopteksten** | No | Headerwaarden die u nodig hebt of wilt opnemen in de uitgaande aanvraag, zoals het inhoudstype |
| **Query's** | No | Queryparameters die u nodig hebt of wilt opnemen in de aanvraag, zoals de parameter voor een specifieke bewerking of de API-versie voor de bewerking die u wilt uitvoeren |
| **Verificatie** | Yes | Het verificatietype dat moet worden gebruikt voor het authenticeren van toegang tot de doelresource of entiteit |
||||

Een specifiek voorbeeld: stel dat u [](/rest/api/storageservices/snapshot-blob) de bewerking Momentopnameblob wilt uitvoeren op een blob in het Azure Storage-account waar u eerder toegang voor uw identiteit hebt ingesteld. De connector [Azure Blob Storage](/connectors/azureblob/) biedt deze bewerking momenteel echter niet. In plaats daarvan kunt u deze bewerking uitvoeren met behulp van de [HTTP-actie](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) of een andere [Blob REST API voor services bewerking](/rest/api/storageservices/operations-on-blobs).

> [!IMPORTANT]
> Als u toegang wilt krijgen tot Azure-opslagaccounts achter firewalls met behulp van HTTP-aanvragen en beheerde identiteiten, moet u ook uw opslagaccount instellen, met uitzondering van toegang door vertrouwde [Microsoft-services](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service).

Als u de [blob-bewerking Momentopname wilt uitvoeren,](/rest/api/storageservices/snapshot-blob)geeft de HTTP-actie de volgende eigenschappen op:

| Eigenschap | Vereist | Voorbeeldwaarde | Beschrijving |
|----------|----------|---------------|-------------|
| **Methode** | Yes | `PUT`| De HTTP-methode die door de momentopnameblobbewerking wordt gebruikt |
| **URI** | Yes | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | De resource-id voor een Azure Blob Storage in de globale (openbare) Azure-omgeving, die gebruikmaakt van deze syntaxis |
| **Kopteksten** | Voor Azure Storage | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` <p>`x-ms-date` = `@{formatDateTime(utcNow(),'r')}` | De `x-ms-blob-type` `x-ms-version` headerwaarden , en `x-ms-date` zijn vereist voor Azure Storage bewerkingen. <p><p>**Belangrijk:** in uitgaande HTTP-triggers en actieaanvragen voor Azure Storage vereist de header de eigenschap en de API-versie voor de bewerking `x-ms-version` die u wilt uitvoeren. De `x-ms-date` moet de huidige datum zijn. Anders mislukt uw logische app met een `403 FORBIDDEN` fout. Als u de huidige datum in de vereiste notatie wilt opmaken, kunt u de expressie in de voorbeeldwaarde gebruiken. <p>Raadpleeg de volgende onderwerpen voor meer informatie: <p><p>- [Aanvraagheaders - Momentopnameblob](/rest/api/storageservices/snapshot-blob#request) <br>- [Versien voor Azure Storage services](/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
| **Query's** | Alleen voor de momentopnameblobbewerking | `comp` = `snapshot` | De naam en waarde van de queryparameter voor de bewerking. |
|||||

Hier ziet u een voorbeeld van een HTTP-actie met al deze eigenschapswaarden:

![Een HTTP-actie toevoegen voor toegang tot een Azure-resource](./media/create-managed-service-identity/http-action-example.png)

1. Nadat u de HTTP-actie hebt toevoegen, voegt u de **eigenschap Authentication** toe aan de HTTP-actie. Selecteer verificatie **in de** lijst Nieuwe parameter **toevoegen.**

   ![De eigenschap 'Verificatie' toevoegen aan http-actie](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Niet alle triggers en acties ondersteunen het toevoegen van een verificatietype. Zie Verificatietypen voor triggers en acties die [verificatie ondersteunen voor meer informatie.](../logic-apps/logic-apps-securing-a-logic-app.md#authentication-types-supported-triggers-actions)

1. Selecteer **beheerde identiteit in** de lijst **Verificatietype.**

   ![Selecteer voor Verificatie de optie Beheerde identiteit](./media/create-managed-service-identity/select-managed-identity.png)

1. Selecteer in de lijst met beheerde identiteiten een van de beschikbare opties op basis van uw scenario.

   * Als u de door het systeem toegewezen identiteit hebt ingesteld, selecteert u Door het systeem **toegewezen beheerde** identiteit als deze nog niet is geselecteerd.

     ![Selecteer 'Door het systeem toegewezen beheerde identiteit'](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Als u een door de gebruiker toegewezen identiteit hebt ingesteld, selecteert u die identiteit als deze nog niet is geselecteerd.

     ![Selecteer de door de gebruiker toegewezen identiteit](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Dit voorbeeld gaat verder met de **Door het systeem toegewezen beheerde identiteit**.

1. Bij sommige triggers en acties wordt de eigenschap **Doelgroep** ook weergegeven om de doelresource-id in te stellen. Stel de **eigenschap Doelgroep** in op de [resource-id voor de doelresource of -service.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) Anders gebruikt de eigenschap **Doelgroep** standaard de `https://management.azure.com/` resource-id. Dit is de resource-id voor Azure Resource Manager.

   > [!IMPORTANT]
   > Zorg ervoor dat de  doelresource-id exact overeenkomt met de waarde die Azure Active Directory (AD) verwacht, inclusief eventuele vereiste slashes. Voor de resource-id voor alle Azure Blob Storage accounts is bijvoorbeeld een slash vereist. Voor de resource-id voor een specifiek opslagaccount is echter geen schuine streep nodig. Controleer de [resource-ID's voor de Azure-services die ondersteuning bieden voor Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

   In dit voorbeeld stelt u **de eigenschap Doelgroep** in op , zodat de toegangstokens die worden gebruikt voor verificatie geldig zijn voor alle `https://storage.azure.com/` opslagaccounts. U kunt echter ook de URL van de hoofdservice opgeven, `https://fabrikamstorageaccount.blob.core.windows.net` , voor een specifiek opslagaccount.

   ![Stel de eigenschap 'Doelgroep' in op doelresource-id](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Zie de volgende onderwerpen voor meer informatie over het autoriseren van toegang Azure Storage Azure AD:

   * [Toegang verlenen tot Azure-blobs en -wachtrijen met behulp van Azure Active Directory](../storage/common/storage-auth-aad.md)
   * [Toegang tot Azure Storage met Azure Active Directory](/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Ga door met het bouwen van de logische app zoals u dat wilt.

<a name="authenticate-managed-connector-managed-identity"></a>

#### <a name="example-authenticate-managed-connector-trigger-or-action-with-a-managed-identity"></a>Voorbeeld: Trigger of actie van beheerde connector verifiëren met een beheerde identiteit

De Azure Resource Manager, Een **resource lezen,** kan de beheerde identiteit gebruiken die u hebt ingeschakeld voor uw logische app. In dit voorbeeld ziet u hoe u de door het systeem toegewezen beheerde identiteit gebruikt.

1. Nadat u de actie aan uw werkstroom hebt toegevoegd, selecteert u op de selectiepagina van de tenant de optie **Verbinding maken met beheerde identiteit**.

   ![Schermopname met Azure Resource Manager actie en 'Verbinding maken met beheerde identiteit' geselecteerd.](./media/create-managed-service-identity/select-connect-managed-identity.png)

   De actie toont nu de verbindingsnaampagina met de lijst met beheerde identiteiten, die het type beheerde identiteit bevat dat momenteel is ingeschakeld voor de logische app.

1. Geef op de pagina Verbindingsnaam een naam op voor de verbinding. Selecteer in de lijst met beheerde identiteiten  de beheerde identiteit, in dit voorbeeld door het systeem toegewezen beheerde identiteit, en selecteer **Maken.** Als u een door de gebruiker toegewezen beheerde identiteit hebt ingeschakeld, selecteert u in plaats daarvan die identiteit.

   ![Schermopname van Azure Resource Manager actie met de ingevoerde verbindingsnaam en 'Door het systeem toegewezen beheerde identiteit' geselecteerd.](./media/create-managed-service-identity/system-assigned-managed-identity.png)

   Als de beheerde identiteit niet is ingeschakeld, wordt de volgende fout weergegeven wanneer u de verbinding probeert te maken:

   *U moet een beheerde identiteit inschakelen voor uw logische app en vervolgens de vereiste toegang verlenen tot de identiteit in de doelresource.*

   ![Schermopname van Azure Resource Manager actie met een fout wanneer er geen beheerde identiteit is ingeschakeld.](./media/create-managed-service-identity/system-assigned-managed-identity-disabled.png)

1. Nadat de verbinding is gemaakt, kan de ontwerper dynamische waarden, inhoud of schema's ophalen met behulp van verificatie van beheerde identiteiten.

1. Ga door met het bouwen van de logische app zoals u dat wilt.

<a name="logic-app-resource-definition-connection-managed-identity"></a>

### <a name="logic-app-resource-definition-and-connections-that-use-a-managed-identity"></a>Resourcedefinitie van logische app en verbindingen die gebruikmaken van een beheerde identiteit

Een verbinding die een beheerde identiteit mogelijk maakt en gebruikt, is een speciaal verbindingstype dat alleen werkt met een beheerde identiteit. Tijdens runtime gebruikt de verbinding de beheerde identiteit die is ingeschakeld voor de logische app. Deze configuratie wordt opgeslagen in het object van de resourcedefinitie van de logische app, dat het object bevat dat aanwijzers bevat naar de resource-id van de verbinding, samen met de resource-id van de identiteit, als de door de gebruiker toegewezen identiteit `parameters` `$connections` is ingeschakeld.

In dit voorbeeld ziet u hoe de configuratie eruit ziet wanneer de logische app de door het systeem toegewezen beheerde identiteit in bedrijf stelt:

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/connections/{connection-name}",
            "connectionName": "{connection-name}",
            "connectionProperties": {
               "authentication": {
                  "type": "ManagedServiceIdentity"
               }
            },
            "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
         }
      }
   }
}
```

In dit voorbeeld ziet u hoe de configuratie eruit ziet wanneer de logische app een door de gebruiker toegewezen beheerde identiteit in staat stelt:

```json
"parameters": {
   "$connections": {
      "value": {
         "<action-name>": {
            "connectionId": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/Microsoft.Web/connections/{connection-name}",
            "connectionName": "{connection-name}",
            "connectionProperties": {
               "authentication": {
                  "identity": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{resourceGroupName}/providers/microsoft.managedidentity/userassignedidentities/{managed-identity-name}",
                  "type": "ManagedServiceIdentity"
               }
            },
            "id": "/subscriptions/{Azure-subscription-ID}/providers/Microsoft.Web/locations/{Azure-region}/managedApis/{managed-connector-type}"
         }
      }
   }
}
```

Tijdens runtime controleert de Logic Apps-service of een beheerde connectortrigger en acties in de logische app zijn ingesteld voor het gebruik van de beheerde identiteit en of alle vereiste machtigingen zijn ingesteld om de beheerde identiteit te gebruiken voor toegang tot de doelbronnen die zijn opgegeven door de trigger en acties. Als dit lukt, haalt de Logic Apps-service het Azure AD-token op dat is gekoppeld aan de beheerde identiteit en gebruikt deze identiteit om toegang tot de doelresource te verifiëren en de geconfigureerde bewerking in triggers en acties uit te voeren.

<a name="arm-templates-connection-resource-managed-identity"></a>

## <a name="arm-template-for-managed-connections-and-managed-identities"></a>ARM-sjabloon voor beheerde verbindingen en beheerde identiteiten

Als u de implementatie met een ARM-sjabloon automatiseert en uw logische app een trigger of actie voor een beheerde connector bevat die gebruikmaakt van een beheerde identiteit, controleert u of de onderliggende verbindingsresourcedefinitie de eigenschap bevat met als `parameterValueType` `Alternative` eigenschapswaarde. Anders stelt uw ARM-implementatie de verbinding niet in voor het gebruik van de beheerde identiteit voor verificatie en werkt de verbinding niet in de werkstroom van uw logische app. Deze vereiste geldt alleen voor [specifieke triggers en acties voor](#managed-connectors-managed-identity) beheerde connectors waarbij u de optie Verbinding maken met [ **beheerde identiteit hebt** geselecteerd.](#authenticate-managed-connector-managed-identity)

Hier is bijvoorbeeld de definitie van de onderliggende verbindingsresource voor een Azure Automation-actie die gebruikmaakt van een beheerde identiteit waarbij de definitie de eigenschap bevat, die is ingesteld op als de `parameterValueType` `Alternative` eigenschapswaarde:

```json
{
    "type": "Microsoft.Web/connections",
    "name": "[variables('automationAccountApiConnectionName')]",
    "apiVersion": "2016-06-01",
    "location": "[parameters('location')]",
    "kind": "V1",
    "properties": {
        "api": {
            "id": "[subscriptionResourceId('Microsoft.Web/locations/managedApis', parameters('location'), 'azureautomation')]"
        },
        "customParameterValues": {},
        "displayName": "[variables('automationAccountApiConnectionName')]",
        "parameterValueType": "Alternative"
    }
},
```

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Beheerde identiteit uitschakelen

Als u wilt stoppen met het gebruik van een beheerde identiteit voor uw logische app, hebt u de volgende opties:

* [Azure-portal](#azure-portal-disable)
* [Azure Resource Manager-sjablonen](#template-disable)
* Azure PowerShell
  * [Roltoewijzing verwijderen](../role-based-access-control/role-assignments-powershell.md)
  * [Door de gebruiker toegewezen identiteit verwijderen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [Roltoewijzing verwijderen](../role-based-access-control/role-assignments-cli.md)
  * [Door de gebruiker toegewezen identiteit verwijderen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Azure REST-API
  * [Roltoewijzing verwijderen](../role-based-access-control/role-assignments-rest.md)
  * [Door de gebruiker toegewezen identiteit verwijderen](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Als u uw logische app verwijdert, wordt de beheerde identiteit automatisch uit Azure AD verwijderd.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Beheerde identiteit in de Azure Portal

Verwijder in Azure Portal eerst de toegang van de identiteit tot [uw doelresource](#disable-identity-target-resource). Schakel vervolgens de door het systeem toegewezen identiteit uit of verwijder de door de gebruiker toegewezen identiteit uit [uw logische app](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Identiteitstoegang verwijderen uit resources

1. Ga in [Azure Portal](https://portal.azure.com)naar de Azure-doelresource waar u de toegang voor de beheerde identiteit wilt verwijderen.

1. Selecteer toegangsbeheer (IAM) in het menu van de **doelresource.** Selecteer **roltoewijzingen onder de werkbalk.**

1. Selecteer in de lijst met rollen de beheerde identiteiten die u wilt verwijderen. Selecteer Verwijderen op de **werkbalk.**

   > [!TIP]
   > Als de **optie** Verwijderen is uitgeschakeld, hebt u waarschijnlijk geen machtigingen. Zie Beheerdersrolmachtigingen in Azure Active Directory voor meer informatie over de machtigingen voor het [beheren van rollen Azure Active Directory.](../active-directory/roles/permissions-reference.md)

De beheerde identiteit is nu verwijderd en heeft geen toegang meer tot de doelresource.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Beheerde identiteit uitschakelen in logische app

1. Open in [Azure Portal](https://portal.azure.com)de logische app in Logic App Designer.

1. Selecteer in het menu van de logische app **onder Instellingen** de optie **Identiteit** en volg de stappen voor uw identiteit:

   * Selecteer **Systeem toegewezen**  >  **op**  >  **Opslaan.** Wanneer Azure u vraagt om te bevestigen, selecteert u **Ja.**

     ![De door het systeem toegewezen identiteit uitschakelen](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Selecteer **Door de gebruiker** toegewezen en de beheerde identiteit en selecteer vervolgens **Verwijderen.** Wanneer Azure u vraagt om te bevestigen, selecteert u **Ja.**

     ![De door de gebruiker toegewezen identiteit verwijderen](./media/create-managed-service-identity/remove-user-assigned-identity.png)

De beheerde identiteit is nu uitgeschakeld voor uw logische app.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Beheerde identiteit uitschakelen in Azure Resource Manager sjabloon

Als u de beheerde identiteit van de logische app hebt gemaakt met behulp van een Azure Resource Manager sjabloon, stelt u de onderliggende eigenschap van het `identity` object `type` in op `None` .

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Volgende stappen

* [Beveiligde toegang en gegevens in Azure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
