---
title: Programmatisch Azure Enterprise Agreement-abonnementen maken met de nieuwste API's
description: Leer hoe u programmatisch Azure Enterprise Agreement-abonnementen maakt met behulp van de nieuwste versies van REST API, Azure CLI, Azure PowerShell en Azure Resource Manager sjablonen.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/29/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: e57f385dce6446ebb3aa2df0ceb48f97a7e0c2f4
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107877876"
---
# <a name="programmatically-create-azure-enterprise-agreement-subscriptions-with-the-latest-apis"></a>Programmatisch Azure Enterprise Agreement-abonnementen maken met de nieuwste API's

Dit artikel helpt u programmatisch Azure EA-abonnementen (Enterprise Agreement) voor een EA-factureringsaccount te maken met behulp van de meest recente API-versies. Als u nog de oudere preview-versie gebruikt, raadpleegt u [Programmatisch Azure-abonnementen maken met preview-API's](programmatically-create-subscription-preview.md). 

In dit artikel leert u hoe u via programmatisch abonnementen kunt maken met behulp van Azure Resource Manager.

Wanneer u programmatisch een Azure-abonnement maakt, wordt dat abonnement beheerd op basis van de overeenkomst waaronder u Azure-services van Microsoft of een geautoriseerde wederverkoper hebt verkregen. Zie de [Juridische informatie van Microsoft Azure](https://azure.microsoft.com/support/legal/) voor meer informatie.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

U moet de rol van eigenaar hebben voor een inschrijvingsaccount om een abonnement te maken. U kunt de rol op twee manieren verkrijgen:

* De Enterprise-beheerder van uw inschrijving kan [u een accounteigenaar maken](https://ea.azure.com/helpdocs/addNewAccount) (aanmelden vereist) waardoor u eigenaar van het inschrijvingsaccount bent.
* Een bestaande eigenaar van het inschrijvingsaccount kan [u toegang verlenen](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). En als u een service-principal wilt gebruiken om een EA-abonnement te maken, moet u [die service-principal de mogelijkheid verlenen abonnementen te maken](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put).  
    Als u een SPN gebruikt om abonnementen te maken, gebruikt u de ObjectId van de Azure AD-toepassingsregistratie als de Service Principal ObjectId met [behulp van Azure Active Directory PowerShell](/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) of [Azure CLI.](/cli/azure/ad/sp?view=azure-cli-latest#az_ad_sp_list)
  > [!NOTE]
  > Zorg ervoor dat u de juiste API-versie gebruikt om de eigenaarsmachtigingen voor het inschrijvingsaccount te verlenen. Voor dit artikel en voor de API's die erin worden beschreven, gebruikt u de API [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Als u migreert om de nieuwere API's te gebruiken, moet u de eigenaarsmachtigingen opnieuw toekennen met behulp van [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Uw vorige configuratie die is gemaakt met [versie 2015-07-01](grant-access-to-create-subscription.md) wordt niet automatisch geconverteerd voor het gebruik van de nieuwere API's.

## <a name="find-accounts-you-have-access-to"></a>Accounts zoeken waartoe u toegang hebt

Wanneer u bent toegevoegd aan een inschrijvingsaccount dat is gekoppeld aan een accounteigenaar, wordt de relatie account-naar-inschrijving gebruikt om te bepalen waaraan de abonnementskosten moeten worden gefactureerd. Alle abonnementen die onder het account zijn gemaakt, worden gefactureerd aan de EA-inschrijving waarin het account zich bevindt. Als u abonnementen wilt maken, moet u waarden over het inschrijvingsaccount en de gebruiker-principals doorgeven om eigenaar van het abonnement te kunnen zijn.

Als u de volgende opdrachten wilt uitvoeren, moet u zijn aangemeld bij de *basismap* van de accounteigenaar. Dit is de map waarin standaard abonnementen worden gemaakt.

### <a name="rest"></a>[REST](#tab/rest)

Aanvraag voor een opsomming van alle inschrijvingsaccounts waartoe u toegang hebt:

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

De API-respons vermeldt alle inschrijvingsaccounts waartoe u toegang hebt:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
      "name": "1234567",
      "properties": {
        "accountStatus": "Unknown",
        "accountType": "Enterprise",
        "agreementType": "EnterpriseAgreement",
        "soldTo": {
          "companyName": "Contoso",
          "country": "US "
        },
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "enrollmentAccounts": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
            "name": "7654321",
            "type": "Microsoft.Billing/enrollmentAccounts",
            "properties": {
              "accountName": "Contoso",
              "accountOwnerEmail": "kenny@contoso.onmicrosoft.com",
              "costCenter": "Test",
              "isDevTest": false
            }
          }
        ],
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```

De waarden voor een factureringsbereik `id` en zijn hetzelfde. De `id` voor uw inschrijvingsaccount is het factureringsbereik waarbinnen de abonnementsaanvraag wordt gestart. Het is belangrijk dat u de id kent, omdat het een vereiste parameter is die u later in het artikel gaat gebruiken om een abonnement te maken.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik Azure CLI of een REST API om deze waarde op te halen.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Vraag een opsomming aan van alle inschrijvingsaccounts waartoe u toegang hebt:

```azurecli-interactive
> az billing account list
```

Antwoord geeft een lijst weer van alle inschrijvingsaccounts tot wie u toegang hebt

```json
[
  {
    "accountStatus": "Unknown",
    "accountType": "Enterprise",
    "agreementType": "EnterpriseAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": [
      {
        "accountName": "Contoso",
        "accountOwner": "",
        "costCenter": "Test",
        "department": null,
        "endDate": null,
        "id": "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/7654321",
        "name": "7654321",
        "startDate": null,
        "status": null,
        "type": "Microsoft.Billing/enrollmentAccounts"
      }
    ],
    "enrollmentDetails": null,
    "hasReadAccess": false,
    "id": "/providers/Microsoft.Billing/billingAccounts/1234567",
    "name": "1234567",
    "soldTo": {
      "addressLine1": null,
      "addressLine2": null,
      "addressLine3": null,
      "city": null,
      "companyName": "Contoso",
      "country": "US ",
      "district": null,
      "email": null,
      "firstName": null,
      "lastName": null,
      "phoneNumber": null,
      "postalCode": null,
      "region": null
    },
    "type": "Microsoft.Billing/billingAccounts"
  },
```

De waarden voor een factureringsbereik `id` en zijn hetzelfde. De `id` voor uw inschrijvingsaccount is het factureringsbereik waarbinnen de abonnementsaanvraag wordt gestart. Het is belangrijk dat u de id kent, omdat het een vereiste parameter is die u later in het artikel gaat gebruiken om een abonnement te maken.

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Abonnementen maken onder een specifiek inschrijvingsaccount

In het volgende voorbeeld wordt in het inschrijvingsaccount dat u in de vorige stap hebt geselecteerd, een abonnement gemaakt met de naam *Dev Team Subscription*. 

### <a name="rest"></a>[REST](#tab/rest)

Roep de PUT API aan om een aanvraag/alias voor het maken van een abonnement te maken.

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01 
```

Geef in de hoofdtekst van de aanvraag de `id` van een van uw `enrollmentAccounts` op als `billingScope`.

```json 
{
  "properties": {
        "billingScope": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321",
        "DisplayName": "Dev Team Subscription", //Subscription Display Name
        "Workload": "Production"
  }
}
```

Toegestane waarden voor `Workload` zijn `Production` en `DevTest`.

#### <a name="response"></a>Antwoord

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Accepted"
  }
}
```

U kunt een GET uitvoeren op dezelfde URL om de status van de aanvraag op te halen.

### <a name="request"></a>Aanvraag

```json
GET https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="response"></a>Antwoord

```json
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "subscriptionId": "b5bab918-e8a9-4c34-a2e2-ebc1b75b9d74",
    "provisioningState": "Succeeded"
  }
}
```

Een 'in voortgang'-status wordt geretourneerd als een `Accepted`-status onder `provisioningState`.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u de meest recente versie van de module met de cmdlet `New-AzSubscriptionAlias` wilt installeren, voert u `Install-Module Az.Subscription` uit. Zie [PowerShellGet-module ophalen](/powershell/scripting/gallery/installing-psget) om een recente versie van PowerShellGet te installeren.

Voer de volgende [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription)-opdracht uit met het factureringsbereik `"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload "Production"
```

U ontvangt de subscriptionId als onderdeel van het antwoord van de opdracht.

```azurepowershell
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "type": "Microsoft.Subscription/aliases",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  }
}
```

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Installeer eerst de extensie door `az extension add --name account` en `az extension add --name alias` uit te voeren.

Voer de volgende [az account alias create](/cli/azure/account/alias#az_account_alias_create)-opdracht uit en geef `billing-scope` en `id` op van een van uw `enrollmentAccounts`. 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/1234567/enrollmentAccounts/654321" --display-name "Dev Team Subscription" --workload "Production"
```

U ontvangt de subscriptionId als onderdeel van het antwoord van de opdracht.

```azurecli
{
  "id": "/providers/Microsoft.Subscription/aliases/sampleAlias",
  "name": "sampleAlias",
  "properties": {
    "provisioningState": "Succeeded",
    "subscriptionId": "4921139b-ef1e-4370-a331-dd2229f4f510"
  },
  "type": "Microsoft.Subscription/aliases"
}
```

---

## <a name="use-arm-template"></a>ARM-sjabloon gebruiken

In de vorige sectie hebt u kunnen zien hoe u een abonnement maakt met PowerShell, CLI of REST API. Als u het maken van abonnementen wilt automatiseren, kunt u een ARM Azure Resource Manager sjabloon (ARM-sjabloon) gebruiken.

Met de volgende sjabloon maakt u een abonnement. Geef `billingScope` voor de id van het inschrijvingsaccount op. Geef `targetManagementGroup` voor de beheergroep op waar u het abonnement wilt maken.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-08-01/managementGroupDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "subscriptionAliasName": {
            "type": "string",
            "metadata": {
                "description": "Provide a name for the alias. This name will also be the display name of the subscription."
            }
        },
        "billingScope": {
            "type": "string",
            "metadata": {
                "description": "Provide the full resource ID of billing scope to use for subscription creation."
            }
        },
        "targetManagementGroup": {
            "type": "string",
            "metadata": {
                "description": "Provide the ID of the target management group to place the subscription."
            }
        }
    },
    "resources": [
        {
            "scope": "/", 
            "name": "[parameters('subscriptionAliasName')]",
            "type": "Microsoft.Subscription/aliases",
            "apiVersion": "2020-09-01",
            "properties": {
                "workLoad": "Production",
                "displayName": "[parameters('subscriptionAliasName')]",
                "billingScope": "[parameters('billingScope')]",
                "managementGroupId": "[tenantResourceId('Microsoft.Management/managementGroups/', parameters('targetManagementGroup'))]"
            }
        }
    ],
    "outputs": {}
}
```

Implementeer de sjabloon op [beheergroepniveau.](../../azure-resource-manager/templates/deploy-to-management-group.md)

### <a name="rest"></a>[REST](#tab/rest)

```json
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/mg1/providers/Microsoft.Resources/deployments/exampledeployment?api-version=2020-06-01
```

Met een aanvraag body:

```json
{
  "location": "eastus",
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json"
    },
    "parameters": {
      "subscriptionAliasName": {
        "value": "sampleAlias"
      },
      "billingScope": {
        "value": "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"
      },
      "targetManagementGroup": {
        "value": "mg2"
      }
    },
    "mode": "Incremental"
  }
}
```

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzManagementGroupDeployment `
  -Name exampledeployment `
  -Location eastus `
  -ManagementGroupId mg1 `
  -TemplateFile azuredeploy.json `
  -subscriptionAliasName sampleAlias `
  -billingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" `
  -targetManagementGroup mg2
```

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

```azurecli-interactive
az deployment mg create \
  --name exampledeployment \
  --location eastus \
  --management-group-id mg1 \
  --template-file azuredeploy.json \
  --parameters subscriptionAliasName='sampleAlias' billingScope='/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321' targetManagementGroup=mg2
```

---

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Beperkingen van de API voor het maken van Azure Enter prise-abonnementen

- Alleen Azure Enterprise-abonnementen worden met de API gemaakt.
- Er is een limiet van 2000 abonnementen per inschrijvingsaccount. Daarna kunnen alleen nog meer abonnementen voor het account worden gemaakt in Azure Portal. Als u meer abonnementen via de API wilt maken, maakt u een nieuw inschrijvingsaccount. Het aantal geannuleerde, verwijderde en overgedragen abonnementen ten opzichte van de limiet van 2000.
- Gebruikers die geen accounteigenaar zijn, maar die zijn toegevoegd aan een inschrijvingsaccount via Azure RBAC, kunnen geen abonnementen maken in Azure Portal.
- U kunt de tenant niet selecteren voor het abonnement waarin deze moet worden gemaakt. Het abonnement wordt altijd gemaakt in de starttenant van de accounteigenaar. Zie [Abonnementstenant wijzigen](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) om het abonnement naar een andere tenant te verplaatsen.


## <a name="next-steps"></a>Volgende stappen

* Nu u een abonnement hebt gemaakt, kunt u die mogelijkheid verlenen aan andere gebruikers en service-principals. Zie [Toegang verlenen voor het maken van Azure Enterprise-abonnementen (preview)](grant-access-to-create-subscription.md) voor meer informatie.
* Zie [Resources organiseren met Azure-beheergroepen](../../governance/management-groups/overview.md) voor meer informatie over het beheren van grote aantallen abonnementen met behulp van beheergroepen.
* Zie Abonnementen verplaatsen als u de beheergroep voor een [abonnement wilt wijzigen.](../../governance/management-groups/manage.md#move-subscriptions)
