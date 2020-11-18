---
title: Toegang verlenen voor het maken van Azure Enterprise-abonnementen
description: Meer informatie over hoe u een gebruiker of service-principal de mogelijkheid geeft om op een programmatische manier Azure Enterprise-abonnementen te maken.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.reviewer: amberb
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: banders
ms.openlocfilehash: c13670df26e5d0f7774b5a2aac81f656de94c960
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844707"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Toegang verlenen voor het maken van Azure Enterprise-abonnementen (preview)

Als Azure-klant op [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)kunt u een andere gebruiker of Service-Principal toestemming geven om abonnementen te maken die aan uw account worden gefactureerd. In dit artikel leert u hoe u [op rollen gebaseerd toegangsbeheer (Azure RBAC) van Azure](../../role-based-access-control/role-assignments-portal.md) kunt gebruiken om de mogelijkheid te delen om abonnementen te maken, en hoe u het maken van abonnementen kunt controleren. U moet de rol van Eigenaar hebben voor het account dat u wilt delen.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Toegang verlenen

Als u [abonnementen wilt maken onder een inschrijvingsaccount](programmatically-create-subscription-enterprise-agreement.md), moeten gebruikers de Azure RBAC [Eigenaar-rol](../../role-based-access-control/built-in-roles.md#owner) hebben voor dat account. U kunt een gebruiker of een groep gebruikers de Azure RBAC-eigenaarrol verlenen voor een inschrijvingsaccount door de volgende stappen uit te voeren:

1. Haal de object-ID op van het inschrijvingsaccount waaraan u toegang wilt verlenen

    Als u anderen de Azure RBAC-eigenaarrol wilt verlenen voor een inschrijvingsaccount, moet u de Eigenaar van het account of een Azure RBAC-eigenaar van het account zijn.

    # <a name="rest"></a>[REST](#tab/rest)

    Vraag een opsomming aan van alle inschrijvingsaccounts waartoe u toegang hebt:

    ```json
    GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
    ```

    Azure reageert met een lijst met inschrijvingsaccounts waartoe u toegang hebt:

    ```json
    {
      "value": [
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "SignUpEngineering@contoso.com"
          }
        },
        {
          "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
          "type": "Microsoft.Billing/enrollmentAccounts",
          "properties": {
            "principalName": "BillingPlatformTeam@contoso.com"
          }
        }
      ]
    }
    ```

    Gebruik de eigenschap `principalName` om het account te identificeren waaraan u Azure RBAC-eigenaarstoegang wilt verlenen. Kopieer de `name` van dat account. Als u bijvoorbeeld RBAC-eigenaarstoegang wilt verlenen aan het SignUpEngineering@contoso.com-inschrijvingsaccount, kopieert u ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Dit is de object-id van het inschrijvingsaccount. Plak deze waarde ergens zodat u deze in de volgende stap als `enrollmentAccountObjectId` kunt gebruiken.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    Gebruik de cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) om alle inschrijvingsaccounts te vermelden waartoe u toegang hebt. Selecteer **Uitproberen** om de [Azure Cloud Shell](https://shell.azure.com/) te openen. Als u de code wilt plakken, klikt u met de rechtermuisknop op de shell-vensters en selecteert u **Plakken**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure reageert met een lijst met inschrijvingsaccounts waartoe u toegang hebt:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Gebruik de eigenschap `principalName` om het account te identificeren waaraan u Azure RBAC-eigenaarstoegang wilt verlenen. Kopieer de `ObjectId` van dat account. Als u bijvoorbeeld RBAC-eigenaarstoegang wilt verlenen aan het SignUpEngineering@contoso.com-inschrijvingsaccount, kopieert u ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Plak deze object-id ergens zodat u deze als `enrollmentAccountObjectId` in de volgende stap kunt gebruiken.

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

    Gebruik de opdracht [az facturering inschrijvingsaccountlijst](/cli/azure/billing) om alle inschrijvingsaccounts te vermelden waartoe u toegang hebt. Selecteer **Uitproberen** om de [Azure Cloud Shell](https://shell.azure.com/) te openen. Als u de code wilt plakken, klikt u met de rechtermuisknop op de shell-vensters en selecteert u **Plakken**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure reageert met een lijst met inschrijvingsaccounts waartoe u toegang hebt:

    ```json
    [
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "SignUpEngineering@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      },
      {
        "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "principalName": "BillingPlatformTeam@contoso.com",
        "type": "Microsoft.Billing/enrollmentAccounts",
      }
    ]
    ```

    ---

    Gebruik de eigenschap `principalName` om het account te identificeren waaraan u Azure RBAC-eigenaarstoegang wilt verlenen. Kopieer de `name` van dat account. Als u bijvoorbeeld RBAC-eigenaarstoegang wilt verlenen aan het SignUpEngineering@contoso.com-inschrijvingsaccount, kopieert u ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Dit is de object-id van het inschrijvingsaccount. Plak deze waarde ergens zodat u deze in de volgende stap als `enrollmentAccountObjectId` kunt gebruiken.

1. <a id="userObjectId"></a>De object-ID ophalen van de gebruiker of groep waaraan u de Azure RBAC-eigenaarrol wilt toekennen

    1. Zoek in het Azure Portal op **Azure Active Directory**.
    1. Als u een gebruiker toegang wilt verlenen, selecteert u **Gebruikers** in het menu aan de linkerkant. Als u toegang tot een groep wilt verlenen, selecteert u **Groepen**.
    1. Selecteer de gebruiker of groep waaraan u de Azure RBAC-eigenaarrol wilt toewijzen.
    1. Als u een gebruiker hebt geselecteerd, vindt u de object-ID op de profielpagina. Als u een groep hebt geselecteerd, wordt de object-ID op het paginaoverzicht weergegeven. Kopieer de **ObjectID** door het pictogram rechts van het tekstvak te selecteren. Plak deze ergens zodat u deze als `userObjectId` kunt gebruiken tijdens de volgende stap.

1. De gebruiker of groep de rol Azure RBAC-eigenaar verlenen in het inschrijvingsaccount

    Ken met de waarden die u in de eerste twee stappen hebt verzameld, de gebruiker of groep de rol Azure RBAC-eigenaar toe in het inschrijvingsaccount.

    # <a name="rest"></a>[REST](#tab/rest-2)

    Voer de volgende aanvraag uit en vervang ```<enrollmentAccountObjectId>``` door de `name` die u hebt gekopieerd tijdens de eerste stap (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Vervang ```<userObjectId>``` door de object-ID die u uit de tweede stap hebt gekopieerd.

    ```json
    PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>"
      }
    }
    ```

    Wanneer de rol Eigenaar is toegewezen aan het bereik van het inschrijvingsaccount, reageert Azure met informatie van de roltoewijzing:

    ```json
    {
      "properties": {
        "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
        "principalId": "<userObjectId>",
        "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "createdOn": "2018-03-05T08:36:26.4014813Z",
        "updatedOn": "2018-03-05T08:36:26.4014813Z",
        "createdBy": "<assignerObjectId>",
        "updatedBy": "<assignerObjectId>"
      },
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
      "type": "Microsoft.Authorization/roleAssignments",
      "name": "<roleAssignmentGuid>"
    }
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell-2)

    Voer de volgende [New-AzRoleAssignment](../../role-based-access-control/role-assignments-powershell.md)-opdracht uit en vervang ```<enrollmentAccountObjectId>``` door de `ObjectId` die in de eerste stap is verzameld (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Vervang ```<userObjectId>``` door de object-ID die in de tweede stap is verzameld.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli-2)

    Voer de volgende [az role assignment maken](../../role-based-access-control/role-assignments-cli.md)-opdracht uit en vervang ```<enrollmentAccountObjectId>``` door de `name` die in de eerste stap zijn verzameld (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Vervang ```<userObjectId>``` door de object-ID die in de tweede stap is verzameld.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Zodra een gebruiker een Azure RBAC-eigenaar wordt voor uw inschrijvingsaccount, kan deze [programmatisch abonnementen maken](programmatically-create-subscription-enterprise-agreement.md) onder het account. Een abonnement dat is gemaakt door een gemachtigde gebruiker heeft nog steeds de oorspronkelijke eigenaar van het account als servicebeheerder, maar heeft standaard ook de gemachtigde gebruiker als Azure RBAC-eigenaar.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Controleren wie abonnementen heeft gemaakt met behulp van activiteitenlogboeken

Als u de abonnementen die via deze API zijn gemaakt, wilt bijhouden, gebruikt u de [Tenant Activity Log API](/rest/api/monitor/tenantactivitylogs). Het is momenteel niet mogelijk om het maken van abonnementen bij te houden met PowerShell, de CLI of de Azure Portal.

1. [Verhoog het toegangsniveau](../../role-based-access-control/elevate-access-global-admin.md) als tenantbeheerder van de Azure AD-tenant en wijs vervolgens de rol van Lezer toe aan de controlerende gebruiker voor het bereik `/providers/microsoft.insights/eventtypes/management`. Deze toegang is beschikbaar in de rol [Lezer](../../role-based-access-control/built-in-roles.md#reader), de rol [Bewakende inzender](../../role-based-access-control/built-in-roles.md#monitoring-contributor) of een [aangepaste rol](../../role-based-access-control/custom-roles.md).
1. Als controlerende gebruiker roept u de [-API voor Tenant activiteiten](/rest/api/monitor/tenantactivitylogs) aan om activiteiten voor het maken van abonnementen weer te geven. Voorbeeld:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Probeer [ARMClient](https://github.com/projectkudu/ARMClient) om deze API eenvoudig aan te roepen vanaf de opdrachtregel.

## <a name="next-steps"></a>Volgende stappen

* Nu de gebruiker of Service-Principal gemachtigd is om een abonnement te maken, kunt u die identiteit gebruiken om [programmatisch Azure Enterprise-abonnementen te maken](programmatically-create-subscription-enterprise-agreement.md).
* Zie [voorbeeldcode op GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core) voor een voorbeeld voor het maken van abonnementen met behulp van .NET.
* Zie [Overzicht Azure Resource Manager](../../azure-resource-manager/management/overview.md) voor meer informatie over Azure Resource Manager en zijn API’s.
* Zie [Resources organiseren met Azure-beheergroepen](../../governance/management-groups/overview.md) voor meer informatie over het beheren van grote aantallen abonnementen met behulp van beheergroepen
* Zie voor uitgebreide best practice richtlijnen voor grote organisaties op het beheer van abonnementen [Azure Enterprise-steiger - voorschrijvende abonnementsgovernance](/azure/architecture/cloud-adoption-guide/subscription-governance)