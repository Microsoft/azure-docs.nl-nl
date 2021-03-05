---
title: Programmatisch Azure Enterprise Agreement-abonnementen maken met de nieuwste API's
description: Leer hoe u programmatisch Azure Enterprise Agreement-abonnementen kunt maken met behulp van de nieuwste versies van REST API, Azure CLI en Azure PowerShell.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/13/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 69cc0998be6079b7d3f2ecf209e5a709771ae293
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200594"
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
  > [!NOTE]
  > Zorg ervoor dat u de juiste API-versie gebruikt om de eigenaarsmachtigingen voor het inschrijvingsaccount te verlenen. Voor dit artikel en voor de API's die erin worden beschreven, gebruikt u de API [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Als u migreert om de nieuwere API's te gebruiken, moet u de eigenaarsmachtigingen opnieuw toekennen met behulp van [2019-10-01-preview](/rest/api/billing/2019-10-01-preview/enrollmentaccountroleassignments/put). Uw vorige configuratie die is gemaakt met [versie 2015-07-01](grant-access-to-create-subscription.md) wordt niet automatisch geconverteerd voor het gebruik van de nieuwere API's.

## <a name="find-accounts-you-have-access-to"></a>Accounts zoeken waartoe u toegang hebt

Wanneer u bent toegevoegd aan een inschrijvingsaccount dat is gekoppeld aan een accounteigenaar, wordt de relatie account-naar-inschrijving gebruikt om te bepalen waaraan de abonnementskosten moeten worden gefactureerd. Alle abonnementen die onder het account zijn gemaakt, worden gefactureerd aan de EA-inschrijving waarin het account zich bevindt. Als u abonnementen wilt maken, moet u waarden over het inschrijvingsaccount en de gebruiker-principals doorgeven om eigenaar van het abonnement te kunnen zijn.

Als u de volgende opdrachten wilt uitvoeren, moet u zijn aangemeld bij de *basismap* van de accounteigenaar. Dit is de map waarin standaard abonnementen worden gemaakt.

### <a name="rest"></a>[REST](#tab/rest-getEnrollments)

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

De waarde voor een factureringsbereik en `id` zijn hetzelfde. De `id` voor uw inschrijvingsaccount is het factureringsbereik waarbinnen de abonnementsaanvraag wordt gestart. Het is belangrijk dat u de id kent, omdat het een vereiste parameter is die u later in het artikel gaat gebruiken om een abonnement te maken.

<!-- 
### [PowerShell](#tab/azure-powershell-getEnrollments)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.

-->


<!--
### [Azure CLI](#tab/azure-cli-getEnrollments)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Abonnementen maken onder een specifiek inschrijvingsaccount

In het volgende voorbeeld wordt in het inschrijvingsaccount dat u in de vorige stap hebt geselecteerd, een abonnement gemaakt met de naam *Dev Team Subscription*. 

### <a name="rest"></a>[REST](#tab/rest-EA)

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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-EA)

Als u de meest recente versie van de module met de cmdlet `New-AzSubscriptionAlias` wilt installeren, voert u `Install-Module Az.Subscription` uit. Zie [PowerShellGet-module ophalen](/powershell/scripting/gallery/installing-psget) om een recente versie van PowerShellGet te installeren.

Voer de volgende [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription)-opdracht uit met het factureringsbereik `"/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/BillingAccounts/1234567/enrollmentAccounts/7654321" -Workload 'Production"
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

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli-EA)

Installeer eerst de extensie door `az extension add --name account` en `az extension add --name alias` uit te voeren.

Voer de volgende [az account alias create](/cli/azure/ext/account/account/alias#ext_account_az_account_alias_create)-opdracht uit en geef `billing-scope` en `id` op van een van uw `enrollmentAccounts`. 

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

## <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Beperkingen van de API voor het maken van Azure Enter prise-abonnementen

- Alleen Azure Enterprise-abonnementen worden met de API gemaakt.
- Er is een limiet van 2000 abonnementen per inschrijvingsaccount. Daarna kunnen alleen nog meer abonnementen voor het account worden gemaakt in Azure Portal. Als u meer abonnementen via de API wilt maken, maakt u een nieuw inschrijvingsaccount. Het aantal geannuleerde, verwijderde en overgedragen abonnementen ten opzichte van de limiet van 2000.
- Gebruikers die geen accounteigenaar zijn, maar die zijn toegevoegd aan een inschrijvingsaccount via Azure RBAC, kunnen geen abonnementen maken in Azure Portal.
- U kunt de tenant niet selecteren voor het abonnement waarin deze moet worden gemaakt. Het abonnement wordt altijd gemaakt in de starttenant van de accounteigenaar. Zie [Abonnementstenant wijzigen](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) om het abonnement naar een andere tenant te verplaatsen.


## <a name="next-steps"></a>Volgende stappen

* Nu u een abonnement hebt gemaakt, kunt u die mogelijkheid verlenen aan andere gebruikers en service-principals. Zie [Toegang verlenen voor het maken van Azure Enterprise-abonnementen (preview)](grant-access-to-create-subscription.md) voor meer informatie.
* Zie [Resources organiseren met Azure-beheergroepen](../../governance/management-groups/overview.md) voor meer informatie over het beheren van grote aantallen abonnementen met behulp van beheergroepen.
