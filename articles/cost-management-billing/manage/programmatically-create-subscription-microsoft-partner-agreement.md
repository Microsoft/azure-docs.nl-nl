---
title: Programmatisch Azure-abonnementen voor een Microsoft Partner-overeenkomst maken met de nieuwste API's
description: Leer hoe u programmatisch Azure-abonnementen voor een Microsoft Partner-overeenkomst kunt maken met behulp van de nieuwste versies van REST API, Azure CLI en Azure PowerShell.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: de1183c1364fcb7e5483559899c2939df15d26b6
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215763"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-partner-agreement-with-the-latest-apis"></a>Programmatisch Azure-abonnementen voor een Microsoft Partner-overeenkomst maken met de nieuwste API's

Dit artikel helpt u programmatisch Azure-abonnementen voor een Microsoft Partner-overeenkomst te maken met behulp van de meest recente API-versies. Als u nog de oudere preview-versie gebruikt, raadpleegt u [Programmatisch Azure-abonnementen maken met preview-API's](programmatically-create-subscription-preview.md). 

In dit artikel leert u hoe u via programmatisch abonnementen kunt maken met behulp van Azure Resource Manager.

Wanneer u programmatisch een Azure-abonnement maakt, wordt dat abonnement beheerd op basis van de overeenkomst waaronder u Azure-services van Microsoft of een geautoriseerde wederverkoper hebt verkregen. Zie de [Juridische informatie van Microsoft Azure](https://azure.microsoft.com/support/legal/) voor meer informatie.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

In het account van de Cloud Solution Provider van uw organisatie moet u de rol van globale beheerder of beheerderagent hebben om een abonnement voor uw factureringsrekening te maken. Zie [Partner Center - Gebruikersrollen en -machtigingen toewijzen](/partner-center/permissions-overview) voor meer informatie.

Raadpleeg [Toegang tot een Microsoft Partner-overeenkomst controleren](mpa-request-ownership.md#check-access-to-a-microsoft-partner-agreement) als u niet weet of u toegang hebt tot een Microsoft Partner-overeenkomstaccount.

In de volgende voorbeelden worden REST API's gebruikt. PowerShell en Azure CLI worden momenteel niet ondersteund.

## <a name="find-the-billing-accounts-that-you-have-access-to"></a>De factureringsrekeningen zoeken waartoe u toegang hebt

Doe de volgende aanvraag om alle factureringsrekeningen weer te geven waartoe u toegang hebt.

### <a name="rest"></a>[REST](#tab/rest-getBillingAccount-MPA)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01
```

In de API-respons worden de factureringsrekeningen vermeld.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Partner",
        "agreementType": "MicrosoftPartnerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": true
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Gebruik de eigenschap `displayName` om de factureringsrekening te identificeren waarvoor u abonnementen wilt maken. Zorg ervoor dat agreementType van het account gelijk is aan *MicrosoftPartnerAgreement*. Kopieer de `name` voor het account. Kopieer bijvoorbeeld `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` om een abonnement te maken voor het factureringsaccount `Contoso`. Plak de waarde ergens, zodat u deze in de volgende stap kunt gebruiken.

<!--
### [PowerShell](#tab/azure-powershell-getBillingAccounts-MPA)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli-getBillingAccounts-MPA)

```azurecli
> az billing account list
```
Er wordt een lijst weer gegeven met alle facturerings accounts waartoe u toegang hebt 

```json
[
  {
    "accountStatus": "Active",
    "accountType": "Partner",
    "agreementType": "MicrosoftPartnerAgreement",
    "billingProfiles": {
      "hasMoreResults": false,
      "value": null
    },
    "departments": null,
    "displayName": "Contoso",
    "enrollmentAccounts": null,
    "enrollmentDetails": null,
    "hasReadAccess": true,
    "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
    "soldTo": null,
    "type": "Microsoft.Billing/billingAccounts"
  }
]
```

Gebruik de eigenschap displayName om het facturerings account te identificeren waarvoor u abonnementen wilt maken. Zorg ervoor dat agreementType van het account gelijk is aan MicrosoftPartnerAgreement. Kopieer de naam voor het account. Als u bijvoorbeeld een abonnement voor het contoso-facturerings account wilt maken, kopieert u 99a13315-XXXX-XXXX-XXXX-XXXXXXXXXXXX: XXXXXXXX-XXXX-XXXX-XXXX-xxxxxxxxxxxx_xxxx-xx-xx. Plak de waarde ergens, zodat u deze in de volgende stap kunt gebruiken.

---

## <a name="find-customers-that-have-azure-plans"></a>Klanten zoeken die Azure-plannen hebben

Doe de volgende aanvraag, met de `name` die u in de eerste stap hebt gekopieerd (```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```), om alle klanten in het factureringsaccount weer te geven voor wie u Azure-abonnementen kunt maken.

### <a name="rest"></a>[REST](#tab/rest-getCustomers)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers?api-version=2020-05-01
```

In de API-respons worden de klanten in de factureringsrekening vermeld die een Azure-plan hebben. U kunt abonnementen voor deze klanten maken.

```json
{
  "totalCount": 2,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso toys Billing Profile",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
        "displayName": "Contoso toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }
  ]
}

```

Gebruik eigenschap `displayName` om de klant te identificeren waarvoor u abonnementen wilt maken. Kopieer de `id` voor de klant. Kopieer bijvoorbeeld `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx` om een abonnement te maken voor `Fabrikam toys`. Plak deze waarde ergens voor gebruik in latere stappen.

<!--
### [PowerShell](#tab/azure-powershell-getCustomers)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->


### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli-getCustomers)

```json
> az billing customer list --account-name 99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx
```

In de API-respons worden de klanten in de factureringsrekening vermeld die een Azure-plan hebben. U kunt abonnementen voor deze klanten maken.

```json
[
  {
    "billingProfileDisplayName": "Fabrikam toys Billing Profile",
    "billingProfileId": "providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "displayName": "Fabrikam toys",
    "id": "providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "resellers": null,
    "type": "Microsoft.Billing/billingAccounts/customers"
  },
  {
    "billingProfileDisplayName": "Contoso toys Billing Profile",
    "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "displayName": "Contoso toys",
    "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "d49c364c-f866-4cc2-a284-d89f369b7951",
    "resellers": null,
    "type": "Microsoft.Billing/billingAccounts/customers"
  }
]

```

Gebruik eigenschap `displayName` om de klant te identificeren waarvoor u abonnementen wilt maken. Kopieer de `id` voor de klant. Kopieer bijvoorbeeld `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/7d15644f-xxxx-xxxx-xxxx-xxxxxxxxxxxx` om een abonnement te maken voor `Fabrikam toys`. Plak deze waarde ergens voor gebruik in latere stappen.

---

## <a name="get-the-resellers-for-a-customer"></a>De resellers voor een klant ophalen

Deze sectie is optioneel en alleen voor indirecte providers.

Als u een indirecte provider in het CSP-model met twee lagen bent, kunt u een reseller opgeven tijdens het maken van abonnementen voor klanten.

### <a name="rest"></a>[REST](#tab/rest-getIndirectResellers)

Doe de volgende aanvraag, met de `id` die u in de tweede stap hebt gekopieerd (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) om alle resellers weer te geven die beschikbaar zijn voor een klant.

```json
GET "https://management.azure.com/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx?$expand=resellers&api-version=2020-05-01"
```
In de API-respons worden de resellers voor de klant vermeld:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "billingProfileDisplayName": "Fabrikam toys Billing Profile",
    "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
}]
}
```

Gebruik de eigenschap `description` om de reseller te identificeren die aan het abonnement is gekoppeld. Kopieer de `resellerId` voor de reseller. Kopieer bijvoorbeeld `3xxxxx` om `Wingtip` te koppelen. Plak de waarde ergens, zodat u deze in de volgende stap kunt gebruiken.

<!--
### [PowerShell](#tab/azure-powershell-getIndirectResellers)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli-getIndirectResellers)

Voer de volgende aanvraag uit met de `name` gekopieerde van de eerste stap ( ```99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx``` ) en de klant die is `name` gekopieerd uit de vorige stap ( ```acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` ).

```azurecli-interactive
 > az billing customer show --expand "enabledAzurePlans,resellers" --account-name "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx" --name "acba85c9-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

In de API-respons worden de resellers voor de klant vermeld:

```json
{
  "billingProfileDisplayName": "Fabrikam toys Billing Profile",
  "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/YL4M-xxxx-xxx-xxx",
  "displayName": "Fabrikam toys",
  "enabledAzurePlans": [
    {
      "skuDescription": "Microsoft Azure Plan",
      "skuId": "0001"
    }
  ],
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resellers": [
    {
      "description": "Wingtip",
      "resellerId": "3xxxxx"
    }
  ],
  "type": "Microsoft.Billing/billingAccounts/customers"
}

```

Gebruik de eigenschap `description` om de reseller te identificeren die aan het abonnement is gekoppeld. Kopieer de `resellerId` voor de reseller. Kopieer bijvoorbeeld `3xxxxx` om `Wingtip` te koppelen. Plak de waarde ergens, zodat u deze in de volgende stap kunt gebruiken.

---

## <a name="create-a-subscription-for-a-customer"></a>Een abonnement voor een klant maken

In het volgende voorbeeld wordt een abonnement met de naam *Dev Team subscription* voor *Fabrikam toys* gemaakt en wordt reseller *Wingtip* aan het abonnement  gekoppeld. U gebruikt het gekopieerde factureringsbereik uit de vorige stap: `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx`. 

### <a name="rest"></a>[REST](#tab/rest-MPA)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>Aanvraagbody

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "DisplayName": "Dev Team subscription",
        "Workload": "Production"
    }
}
```
### <a name="response"></a>Antwoord

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

Geef de optionele *resellerId*, die in de tweede stap is gekopieerd, door in de aanvraagbody van de API.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-MPA)

Als u de meest recente versie van de module met de cmdlet `New-AzSubscriptionAlias` wilt installeren, voert u `Install-Module Az.Subscription` uit. Zie [PowerShellGet-module ophalen](/powershell/scripting/gallery/installing-psget) om een recente versie van PowerShellGet te installeren.

Voer de volgende [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription)-opdracht uit met het factureringsbereik `"/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" -Workload 'Production"
```

U ontvangt de subscriptionId als onderdeel van het antwoord van de opdracht.

```azurepowershell
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

Geef de optionele *resellerId* door die in de tweede stap is gekopieerd in de `New-AzSubscriptionAlias`-aanroep.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli-MPA)

Installeer eerst de extensie door `az extension add --name account` en `az extension add --name alias` uit te voeren.

Voer de volgende [az account alias create](/cli/azure/ext/account/account/alias#ext_account_az_account_alias_create)-opdracht uit. 

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx" --display-name "Dev Team Subscription" --workload "Production"
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

Geef de optionele *resellerId* door die in de tweede stap is gekopieerd in de `az account alias create`-aanroep.

---

## <a name="next-steps"></a>Volgende stappen

* Nu u een abonnement hebt gemaakt, kunt u die mogelijkheid verlenen aan andere gebruikers en service-principals. Zie [Toegang verlenen voor het maken van Azure Enterprise-abonnementen (preview)](grant-access-to-create-subscription.md) voor meer informatie.
* Zie [Resources organiseren met Azure-beheergroepen](../../governance/management-groups/overview.md) voor meer informatie over het beheren van grote aantallen abonnementen met behulp van beheergroepen.
