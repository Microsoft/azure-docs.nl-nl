---
title: Programmatisch Azure-abonnementen voor een Microsoft-klantovereenkomst maken met de nieuwste API's
description: Leer hoe u programmatisch Azure-abonnementen voor een Microsoft-klantovereenkomst kunt maken met behulp van de nieuwste versies van REST API, Azure CLI en Azure PowerShell.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 2b0eaef7940b84dbd1e1325b5ae3bfb7b65dcef6
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200560"
---
# <a name="programmatically-create-azure-subscriptions-for-a-microsoft-customer-agreement-with-the-latest-apis"></a>Programmatisch Azure-abonnementen voor een Microsoft-klantovereenkomst maken met de nieuwste API's

Dit artikel helpt u programmatisch Azure-abonnementen voor een Microsoft-klantovereenkomst te maken met behulp van de meest recente API-versies. Als u nog de oudere preview-versie gebruikt, raadpleegt u [Programmatisch Azure-abonnementen maken met preview-API's](programmatically-create-subscription-preview.md). 

In dit artikel leert u hoe u via programmatisch abonnementen kunt maken met behulp van Azure Resource Manager.

Wanneer u programmatisch een Azure-abonnement maakt, wordt dat abonnement beheerd op basis van de overeenkomst waaronder u Azure-services van Microsoft of een geautoriseerde wederverkoper hebt verkregen. Zie de [Juridische informatie van Microsoft Azure](https://azure.microsoft.com/support/legal/) voor meer informatie.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

U moet de rol van eigenaar, bijdrager of Azure-abonnementsmaker op een factuursectie of die van eigenaar of bijdrager op een factureringsprofiel of factureringsrekening hebben om abonnementen te maken. Voor meer informatie, zie [Rollen en taken voor abonnementsfacturering](understand-mca-roles.md#subscription-billing-roles-and-tasks).

Raadpleeg [Toegang tot een Microsoft-klantovereenkomst controleren](../understand/mca-overview.md#check-access-to-a-microsoft-customer-agreement) als u niet weet of u toegang hebt tot een Microsoft-klantovereenkomstaccount.

In de volgende voorbeelden worden REST API's gebruikt. PowerShell en Azure CLI worden momenteel niet ondersteund.

## <a name="find-billing-accounts-that-you-have-access-to"></a>Factureringsrekeningen zoeken waartoe u toegang hebt

Doe de volgende aanvraag om alle factureringsrekeningen weer te geven.

### <a name="rest"></a>[REST](#tab/rest-getBillingAccounts)

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/?api-version=2020-05-01

```
De API-respons vermeldt alle factureringsrekeningen waartoe u toegang hebt.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": {
          "hasMoreResults": false
        },
        "displayName": "Contoso",
        "hasReadAccess": false
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

Gebruik de eigenschap `displayName` om de factureringsrekening te identificeren waarvoor u abonnementen wilt maken. Zorg ervoor dat agreementType van het account gelijk is aan *MicrosoftCustomerAgreement*. Kopieer de `name` van het account.  Kopieer bijvoorbeeld `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` om een abonnement te maken voor het factureringsaccount `Contoso`. Plak de waarde ergens, zodat u deze in de volgende stap kunt gebruiken.

<!--
### [PowerShell](#tab/azure-powershell-getBillingAccounts)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingAccounts)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="find-billing-profiles--invoice-sections-to-create-subscriptions"></a>Factureringsprofielen en factuursecties zoeken om abonnementen te maken

De kosten voor uw abonnement worden weergegeven in een sectie van de factuur van het factureringsprofiel. Gebruik de volgende API om de lijst met factureringsprofielen en factuursecties op te halen waarvoor u gemachtigd bent om Azure-abonnementen te maken.

Eerst krijgt u de lijst met factureringsprofielen onder het factureringsaccount waartoe u toegang hebt.

### <a name="rest"></a>[REST](#tab/rest-getBillingProfiles)
```json
GET https://management.azure.com/providers/Microsoft.Billing/billingaccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingprofiles/?api-version=2020-05-01
```
In de API-respons worden alle factureringsprofielen vermeld waarvoor u toegang hebt om abonnementen te maken:

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx",
      "name": "AW4F-xxxx-xxx-xxx",
      "properties": {
        "billingRelationshipType": "Direct",
        "billTo": {
          "addressLine1": "One Microsoft Way",
          "city": "Redmond",
          "companyName": "Contoso",
          "country": "US",
          "email": "kenny@contoso.com",
          "phoneNumber": "425xxxxxxx",
          "postalCode": "98052",
          "region": "WA"
        },
        "currency": "USD",
        "displayName": "Contoso Billing Profile",
        "enabledAzurePlans": [
          {
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
          },
          {
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
          }
        ],
        "hasReadAccess": true,
        "invoiceDay": 5,
        "invoiceEmailOptIn": false,
        "invoiceSections": {
          "hasMoreResults": false
        },
        "poNumber": "001",
        "spendingLimit": "Off",
        "status": "Active",
        "systemId": "AW4F-xxxx-xxx-xxx",
        "targetClouds": []
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles"
    }
  ]
}
```

 Kopieer de `id` om vervolgens de factuursecties onder het factureringsprofiel te identificeren. Kopieer bijvoorbeeld `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx` en roep de volgende API aan.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoicesections?api-version=2020-05-01
```
### <a name="response"></a>Antwoord

```json
{
  "totalCount": 1,
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
      "name": "SH3V-xxxx-xxx-xxx",
      "properties": {
        "displayName": "Development",
        "state": "Active",
        "systemId": "SH3V-xxxx-xxx-xxx"
      },
      "type": "Microsoft.Billing/billingAccounts/billingProfiles/invoiceSections"
    }
  ]
}
```

Gebruik de eigenschap `id` om de factuursectie te identificeren waarvoor u abonnementen wilt maken. Kopieer de gehele tekenreeks. Bijvoorbeeld `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

<!--
### [PowerShell](#tab/azure-powershell-getBillingProfiles)

we're still working on enabling PowerShell SDK for billing APIs. Check back soon.
-->

<!--
### [Azure CLI](#tab/azure-cli-getBillingProfiles)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-a-subscription-for-an-invoice-section"></a>Een abonnement voor een factuursectie maken

In het volgende voorbeeld wordt een abonnement met de naam *Dev Team subscription* gemaakt voor de factuursectie *Development*. Het abonnement wordt gefactureerd voor het factureringsprofiel van *Contoso Billing Profile* en wordt weergegeven in de sectie *Development* van de factuur. U gebruikt het gekopieerde factureringsbereik uit de vorige stap: `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

### <a name="rest"></a>[REST](#tab/rest-MCA)

```json
PUT  https://management.azure.com/providers/Microsoft.Subscription/aliases/sampleAlias?api-version=2020-09-01
```

### <a name="request-body"></a>Aanvraagbody

```json
{
  "properties":
    {
        "billingScope": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx",
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

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell-MCA)

Als u de meest recente versie van de module met de cmdlet `New-AzSubscriptionAlias` wilt installeren, voert u `Install-Module Az.Subscription` uit. Zie [PowerShellGet-module ophalen](/powershell/scripting/gallery/installing-psget) om een recente versie van PowerShellGet te installeren.

Voer de volgende [New-AzSubscriptionAlias](/powershell/module/az.subscription/new-azsubscription)-opdracht uit met het factureringsbereik `"/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"`. 

```azurepowershell-interactive
New-AzSubscriptionAlias -AliasName "sampleAlias" -SubscriptionName "Dev Team Subscription" -BillingScope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" -Workload 'Production"
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

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli-MCA)

Installeer eerst de extensie door `az extension add --name account` en `az extension add --name alias` uit te voeren.

Voer de volgende [az account alias create](/cli/azure/ext/account/account/alias#ext_account_az_account_alias_create)-opdracht uit.

```azurecli-interactive
az account alias create --name "sampleAlias" --billing-scope "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx" --display-name "Dev Team Subscription" --workload "Production"
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

## <a name="next-steps"></a>Volgende stappen

* Nu u een abonnement hebt gemaakt, kunt u die mogelijkheid verlenen aan andere gebruikers en service-principals. Zie [Toegang verlenen voor het maken van Azure Enterprise-abonnementen (preview)](grant-access-to-create-subscription.md) voor meer informatie.
* Zie [Resources organiseren met Azure-beheergroepen](../../governance/management-groups/overview.md) voor meer informatie over het beheren van grote aantallen abonnementen met behulp van beheergroepen.
