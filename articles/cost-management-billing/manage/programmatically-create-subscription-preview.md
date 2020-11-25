---
title: Programmatisch Azure-abonnementen maken met preview-API's
description: Leer hoe u programmatisch meer Azure-abonnementen kunt maken met behulp van preview-versies van REST API, Azure CLI en Azure PowerShell.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 68d890386d53b4115c773b128f8678bac9579e53
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94844332"
---
# <a name="programmatically-create-azure-subscriptions-with-preview-apis"></a>Programmatisch Azure-abonnementen maken met preview-API's

Dit artikel helpt u programmatisch Azure-abonnementen te maken met behulp van onze oudere preview-API. In dit artikel leert u hoe u via programmatisch abonnementen kunt maken met behulp van Azure Resource Manager.

Er zijn nieuwe artikelen over de nieuwste API-versie voor gebruik met verschillende abonnementstypen voor een Azure-overeenkomst:

- [Programmatisch EA-abonnementen maken met de nieuwste API](programmatically-create-subscription-enterprise-agreement.md)
- [Programmatisch MCA-abonnementen maken met de nieuwste API](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Programmatisch MPA-abonnementen maken met de nieuwste API](Programmatically-create-subscription-microsoft-customer-agreement.md)

U kunt de informatie in dit artikel echter nog steeds gebruiken, als u niet de meest recente API-versie wilt gebruiken.

Azure-klanten met een facturerings account voor de volgende overeenkomsttypen kunnen programmatisch abonnementen maken:

- Enterprise Agreement
- Microsoft-klantovereenkomst (MCA)
- Microsoft Partner-overeenkomst (MPA)

Wanneer u programmatisch een Azure-abonnement maakt, wordt het abonnement beheerd op basis van de overeenkomst waaronder u Azure-services van Microsoft of een geautoriseerde wederverkoper hebt verkregen. Zie de [Juridische informatie van Microsoft Azure](https://azure.microsoft.com/support/legal/) voor meer informatie.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-subscriptions-for-an-ea-billing-account"></a>Abonnementen maken voor een EA-factureringsrekening

Gebruik de informatie in de volgende secties om EA-abonnementen te maken.

### <a name="prerequisites"></a>Vereisten

U moet de rol van eigenaar hebben voor een inschrijvingsaccount om een abonnement te maken. U kunt de rol op twee manieren verkrijgen:

* De Enterprise-beheerder van uw inschrijving kan [u een accounteigenaar maken](https://ea.azure.com/helpdocs/addNewAccount) en aanmelden vereist waardoor u eigenaar van het inschrijvingsaccount bent.
* Een bestaande eigenaar van het inschrijvingsaccount kan [u toegang verlenen](grant-access-to-create-subscription.md). En als u een service-principal wilt gebruiken om een EA-abonnement te maken, moet u [die service-principal de mogelijkheid verlenen abonnementen te maken](grant-access-to-create-subscription.md).

### <a name="find-accounts-you-have-access-to"></a>Accounts zoeken waartoe u toegang hebt

Wanneer u bent toegevoegd aan een inschrijvingsaccount dat is gekoppeld aan een accounteigenaar, wordt de relatie account-naar-inschrijving gebruikt om te bepalen waaraan de abonnementskosten moeten worden gefactureerd. Alle abonnementen die onder het account zijn gemaakt, worden gefactureerd aan de EA-inschrijving waarin het account zich bevindt. Als u abonnementen wilt maken, moet u waarden over het inschrijvingsaccount en de gebruiker-principals doorgeven om eigenaar van het abonnement te kunnen zijn.

Als u de volgende opdrachten wilt uitvoeren, moet u zijn aangemeld bij de *basismap* van de accounteigenaar. Dit is de map waarin standaard abonnementen worden gemaakt.

### <a name="rest"></a>[REST](#tab/rest)

Vraag een opsomming aan van alle inschrijvingsaccounts waartoe u toegang hebt:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

De API-respons vermeldt alle inschrijvingsaccounts waartoe u toegang hebt:

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

Gebruik de eigenschap `principalName` om het account te identificeren waarvoor abonnementen moeten worden gefactureerd. Kopieer de `name` van dat account. Als u bijvoorbeeld abonnementen wilt maken onder het inschrijvingsaccount SignUpEngineering@contoso.com, kopieert u ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. De id is de object-id van het inschrijvingsaccount. Plak de waarde ergens, zodat u deze in de volgende stap als `enrollmentAccountObjectId` kunt gebruiken.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Open [Azure Cloud Shell](https://shell.azure.com/) en selecteer PowerShell.

Gebruik de cmdlet [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount) om alle inschrijvingsaccounts te vermelden waartoe u toegang hebt.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Azure reageert met een lijst met inschrijvingsaccounts waartoe u toegang hebt:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```
Gebruik de eigenschap `principalName` om het account te identificeren waarvoor abonnementen moeten worden gefactureerd. Kopieer de `ObjectId` van dat account. Als u bijvoorbeeld abonnementen wilt maken onder het inschrijvingsaccount SignUpEngineering@contoso.com, kopieert u ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Plak de object-id ergens, zodat u in de volgende stap kunt gebruiken als `enrollmentAccountObjectId`.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Gebruik de opdracht [az billing enrollment-account list](/cli/azure/billing) om alle inschrijvingsaccounts te vermelden waartoe u toegang hebt.

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

Gebruik de eigenschap `principalName` om het account te identificeren waarvoor abonnementen moeten worden gefactureerd. Kopieer de `name` van dat account. Als u bijvoorbeeld abonnementen wilt maken onder het inschrijvingsaccount SignUpEngineering@contoso.com, kopieert u ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. De id is de object-id van het inschrijvingsaccount. Plak de waarde ergens, zodat u deze in de volgende stap als `enrollmentAccountObjectId` kunt gebruiken.

---

### <a name="create-subscriptions-under-a-specific-enrollment-account"></a>Abonnementen maken onder een specifiek inschrijvingsaccount

In het volgende voorbeeld wordt in het inschrijvingsaccount dat u in de vorige stap hebt geselecteerd, een abonnement gemaakt met de naam *Dev Team Subscription*. De aanbieding voor het abonnement *MS-AZR-0017P* (regulier Microsoft Enterprise Agreement). Optioneel worden twee gebruikers toegevoegd als Azure RBAC-eigenaren voor het abonnement.

### <a name="rest"></a>[REST](#tab/rest)

Voer de volgende aanvraag uit en vervang `<enrollmentAccountObjectId>` door de `name` die u hebt gekopieerd tijdens de eerste stap (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Zie [object-id's van gebruikers ophalen](grant-access-to-create-subscription.md#userObjectId) voor het opgeven van eigenaren.

```json
POST https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-03-01-preview

{
  "displayName": "Dev Team Subscription",
  "offerType": "MS-AZR-0017P",
  "owners": [
    {
      "objectId": "<userObjectId>"
    },
    {
      "objectId": "<servicePrincipalObjectId>"
    }
  ]
}
```

| Naam van element  | Vereist | Type   | Beschrijving                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Nee      | Tekenreeks | De weergavenaam van het abonnement. Als deze niet is opgegeven, wordt deze ingesteld op de naam van de aanbieding, bijvoorbeeld Microsoft Azure Enterprise.                                 |
| `offerType`   | Ja      | Tekenreeks | De aanbieding voor het abonnement. De twee opties voor EA zijn [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (gebruikt voor productie) en [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (ontwikkelen/testen; moet worden [ingeschakeld met behulp van de EA-Portal](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `owners`      | Nee       | Tekenreeks | De object-id van een gebruiker die u wilt toevoegen als een Azure RBAC-eigenaar voor het abonnement wanneer deze wordt gemaakt.  |

Als onderdeel van de header `Location` wordt in het antwoord een URL weergegeven waarmee u de status van de maakbewerking van een abonnement kunt achterhalen. Wanneer het abonnement is gemaakt, retourneert een GET-bewerking voor de URL van het type `Location` een `subscriptionLink`-object, dat de abonnement-id heeft. Raadpleeg [Documentatie over abonnement-API](/rest/api/subscription/) voor meer informatie

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u de meest recente versie van de module met de cmdlet `New-AzSubscription` wilt installeren, voert u `Install-Module Az.Subscription` uit. Zie [PowerShellGet-module ophalen](/powershell/scripting/gallery/installing-psget) om een recente versie van PowerShellGet te installeren.

Voer de opdracht [New-AzSubscription](/powershell/module/az.subscription) hieronder uit en vervang `<enrollmentAccountObjectId>` door de `ObjectId` die in de eerste stap is verzameld (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Zie [object-id's van gebruikers ophalen](grant-access-to-create-subscription.md#userObjectId) voor het opgeven van eigenaren.

```azurepowershell-interactive
New-AzSubscription -OfferType MS-AZR-0017P -Name "Dev Team Subscription" -EnrollmentAccountObjectId <enrollmentAccountObjectId> -OwnerObjectId <userObjectId1>,<servicePrincipalObjectId>
```

| Naam van element  | Vereist | Type   | Beschrijving |
|---------------|----------|--------|----|
| `Name` | Nee      | Tekenreeks | De weergavenaam van het abonnement. Als deze niet is opgegeven, wordt deze ingesteld op de naam van de aanbieding, bijvoorbeeld *Microsoft Azure Enterprise*. |
| `OfferType`   | Ja      | Tekenreeks | De abonnementaanbieding. De twee opties voor EA zijn [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (gebruikt voor productie) en [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (ontwikkelen/testen; moet worden [ingeschakeld met behulp van de EA-Portal](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `EnrollmentAccountObjectId`      | Ja       | Tekenreeks | De object-id van het inschrijvingsaccount waaronder het abonnement wordt gemaakt en waarvoor het wordt gefactureerd. De waarde is een GUID die u ophaalt van `Get-AzEnrollmentAccount`. |
| `OwnerObjectId`      | Nee       | Tekenreeks | De object-id van een gebruiker die moet worden toegevoegd als Azure RBAC-eigenaar voor het abonnement wanneer deze wordt gemaakt.  |
| `OwnerSignInName`    | Nee       | Tekenreeks | De object-id van een gebruiker die moet worden toegevoegd als Azure RBAC-eigenaar voor het abonnement wanneer dit wordt gemaakt. U kunt de parameter gebruiken in plaats van `OwnerObjectId`.|
| `OwnerApplicationId` | Nee       | Tekenreeks | De toepassing-id van een service-principal die moet worden toegevoegd als Azure RBAC-eigenaar voor het abonnement wanneer het wordt gemaakt. U kunt de parameter gebruiken in plaats van `OwnerObjectId`. Wanneer u de parameter gebruikt, moet de service-principal [leestoegang voor de map hebben](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true).|

Zie [New-AzSubscription](/powershell/module/az.subscription/New-AzSubscription) voor een volledige lijst met parameters.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

Installeer eerst de preview-extensie door `az extension add --name subscription` uit te voeren.

Voer de opdracht [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create&preserve-view=true) hieronder uit en vervang `<enrollmentAccountObjectId>` door de `name` die u in de eerste stap hebt gekopieerd (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Zie [object-id's van gebruikers ophalen](grant-access-to-create-subscription.md#userObjectId) voor het opgeven van eigenaren.

```azurecli-interactive
az account create --offer-type "MS-AZR-0017P" --display-name "Dev Team Subscription" --enrollment-account-object-id "<enrollmentAccountObjectId>" --owner-object-id "<userObjectId>","<servicePrincipalObjectId>"
```

| Naam van element  | Vereist | Type   | Beschrijving |
|---------------|----------|--------|------------|
| `display-name` | Nee      | Tekenreeks | De weergavenaam van het abonnement. Als deze niet is opgegeven, wordt deze ingesteld op de naam van de aanbieding, bijvoorbeeld *Microsoft Azure Enterprise*.|
| `offer-type`   | Ja      | Tekenreeks | De aanbieding voor het abonnement. De twee opties voor EA zijn [MS-AZR-0017P](https://azure.microsoft.com/pricing/enterprise-agreement/) (gebruikt voor productie) en [MS-AZR-0148P](https://azure.microsoft.com/offers/ms-azr-0148p/) (ontwikkelen/testen; moet worden [ingeschakeld met behulp van de EA-Portal](https://ea.azure.com/helpdocs/DevOrTestOffer)).                |
| `enrollment-account-object-id`      | Ja       | Tekenreeks | De object-id van het inschrijvingsaccount waaronder het abonnement wordt gemaakt en waarvoor het wordt gefactureerd. De waarde is een GUID die u ophaalt van `az billing enrollment-account list`. |
| `owner-object-id`      | Nee       | Tekenreeks | De object-id van een gebruiker die moet worden toegevoegd als Azure RBAC-eigenaar voor het abonnement wanneer deze wordt gemaakt.  |
| `owner-upn`    | Nee       | Tekenreeks | De object-id van een gebruiker die moet worden toegevoegd als Azure RBAC-eigenaar voor het abonnement wanneer dit wordt gemaakt. U kunt de parameter gebruiken in plaats van `owner-object-id`.|
| `owner-spn` | Nee       | Tekenreeks | De toepassing-id van een service-principal die moet worden toegevoegd als Azure RBAC-eigenaar voor het abonnement wanneer het wordt gemaakt. U kunt de parameter gebruiken in plaats van `owner-object-id`. Wanneer u de parameter gebruikt, moet de service-principal [leestoegang voor de map hebben](/powershell/azure/active-directory/signing-in-service-principal?view=azureadps-2.0#give-the-service-principal-reader-access-to-the-current-tenant-get-azureaddirectoryrole&preserve-view=true).|

Zie [az account create](/cli/azure/ext/subscription/account?view=azure-cli-latest#-ext-subscription-az-account-create&preserve-view=true) voor een volledige lijst met parameters.

---

### <a name="limitations-of-azure-enterprise-subscription-creation-api"></a>Beperkingen van de API voor het maken van Azure Enter prise-abonnementen

- Alleen Azure Enterprise-abonnementen kunnen met de API worden gemaakt.
- Er is een limiet van 2000 abonnementen per inschrijvingsaccount. Daarna kunnen alleen nog meer abonnementen voor het account worden gemaakt in Azure Portal. Als u meer abonnementen via de API wilt maken, maakt u een nieuw inschrijvingsaccount. Het aantal geannuleerde, verwijderde en overgedragen abonnementen ten opzichte van de limiet van 2000.
- Gebruikers die geen accounteigenaar zijn, maar die zijn toegevoegd aan een inschrijvingsaccount met Azure RBAC, kunnen geen abonnementen maken in de Azure-portal.
- U kunt de tenant niet selecteren voor het abonnement waarin deze moet worden gemaakt. Het abonnement wordt altijd gemaakt in de starttenant van de accounteigenaar. Zie [Abonnementstenant wijzigen](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) om het abonnement naar een andere tenant te verplaatsen.


## <a name="create-subscriptions-for-an-mca-account"></a>Abonnementen maken voor een MCA-account

Gebruik de informatie in de volgende secties om abonnementen te maken voor een MCA-account.

### <a name="prerequisites"></a>Vereisten

U moet de rol van eigenaar, bijdrager of Azure-abonnementsmaker op een factuursectie of die van eigenaar of bijdrager op een factureringsprofiel of factureringsrekening hebben om abonnementen te maken. Voor meer informatie, zie [Rollen en taken voor abonnementsfacturering](understand-mca-roles.md#subscription-billing-roles-and-tasks).

In de volgende voorbeelden worden REST API's gebruikt. PowerShell en Azure CLI worden momenteel niet ondersteund.

### <a name="find-billing-accounts-that-you-have-access-to"></a>Factureringsrekeningen zoeken waartoe u toegang hebt

Doe de volgende aanvraag om alle factureringsrekeningen weer te geven.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```
De API-respons vermeldt alle factureringsrekeningen waartoe u toegang hebt.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "41b29574-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Gebruik de eigenschap `displayName` om de factureringsrekening te identificeren waarvoor u abonnementen wilt maken. Zorg ervoor dat agreementType van het account gelijk is aan *MicrosoftCustomerAgreement*. Kopieer de `name` van het account.  Kopieer bijvoorbeeld `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` om een abonnement te maken voor het factureringsaccount `Contoso`. Plak de waarde ergens, zodat u deze in de volgende stap kunt gebruiken.

### <a name="find-invoice-sections-to-create-subscriptions"></a>Factuursecties zoeken om abonnementen te maken

De kosten voor uw abonnement worden weergegeven in een sectie van de factuur van het factureringsprofiel. Gebruik de volgende API om de lijst met factuursecties en factureringsprofielen op te halen waarvoor u gemachtigd bent om Azure-abonnementen te maken.

Voer de volgende aanvraag uit en vervang `<billingAccountName>` door de `name` die u hebt gekopieerd tijdens de eerste stap (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```).

```json
POST https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/listInvoiceSectionsWithCreateSubscriptionPermission?api-version=2019-10-01-preview
```

In de API-respons worden alle factuursecties en de bijbehorende factureringsprofielen vermeld waarvoor u toegang hebt om abonnementen te maken:

```json
{
    "value": [{
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Development",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/invoiceSections/GJ77-xxxx-xxx-xxx"
    }, {
        "billingProfileDisplayName": "Contoso finance",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
        "enabledAzurePlans": [{
            "productId": "DZH318Z0BPS6",
            "skuId": "0001",
            "skuDescription": "Microsoft Azure Plan"
        }, {
            "productId": "DZH318Z0BPS6",
            "skuId": "0002",
            "skuDescription": "Microsoft Azure Plan for DevTest"
        }],
        "invoiceSectionDisplayName": "Testing",
        "invoiceSectionId": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX"
  }]
}

```

Gebruik de eigenschap `invoiceSectionDisplayName` om de factuursectie te identificeren waarvoor u abonnementen wilt maken. Kopieer `invoiceSectionId`, `billingProfileId` en een van de `skuId` voor de factuursectie. Als u bijvoorbeeld een abonnement wilt maken van het type `Microsoft Azure plan` voor factuursectie `Development`, kopieert u `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX`, `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-xxxx-xxx-xxx` en `0001`. Bewaar de waarden ergens, zodat u deze kunt gebruiken in de volgende stap.

### <a name="create-a-subscription-for-an-invoice-section"></a>Een abonnement voor een factuursectie maken

In het volgende voorbeeld wordt een abonnement van het type *Microsoft Azure Plan* gemaakt met de naam *Dev Team subscription* voor de factuursectie *Development*. Het abonnement wordt gefactureerd voor het factureringsprofiel van *Contoso finance* en wordt weergegeven in de sectie *Development* van de factuur.

Voer de volgende aanvraag uit en vervang `<invoiceSectionId>` door de `invoiceSectionId` die u tijdens de tweede stap (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_2019-05-31/billingProfiles/PBFV-XXXX-XXX-XXX/invoiceSections/GJGR-XXXX-XXX-XXX```) hebt gekopieerd. Geef de `billingProfileId` en `skuId` die in de tweede stap zijn gekopieerd, door in de aanvraagparameters van de API. Zie [object-id's van gebruikers ophalen](grant-access-to-create-subscription.md#userObjectId) voor het opgeven van eigenaren.

```json
POST https://management.azure.com<invoiceSectionId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "billingProfileId": "<billingProfileId>",
  "skuId": "<skuId>",
  "owners": [
      {
        "objectId": "<userObjectId>"
      },
      {
        "objectId": "<servicePrincipalObjectId>"
      }
    ],
  "costCenter": "35683",
  "managementGroupId": "/providers/Microsoft.Management/managementGroups/xxxxxxx",",
}'

```

| Naam van element  | Vereist | Type   | Beschrijving                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Ja      | Tekenreeks | De weergavenaam van het abonnement.|
| `billingProfileId`   | Ja      | Tekenreeks | De id van het factureringsprofiel waarmee de kosten voor het abonnement worden gefactureerd.  |
| `skuId` | Ja      | Tekenreeks | De SKU-id die het type het Azure-plan bepaalt. |
| `owners`      | Nee       | Tekenreeks | De object-id van een gebruiker of service-principal die moet worden toegevoegd als Azure RBAC-eigenaar voor het abonnement wanneer het wordt gemaakt.  |
| `costCenter` | Nee      | Tekenreeks | Kostenplaats die aan het abonnement is gekoppeld. Deze wordt weergegeven in het CSV-bestand voor het gebruik. |
| `managementGroupId` | Nee      | Tekenreeks | De id van de beheergroep waaraan het abonnement wordt toegevoegd. Zie [Beheergroepen - Lijst-API](/rest/api/resources/managementgroups/list) voor een lijst met beheergroepen. Gebruik de id van een beheergroep van de API. |

In de respons krijgt u een `subscriptionCreationResult`-object terug voor bewaking. Wanneer het abonnement is gemaakt, retourneert het `subscriptionCreationResult`-object een `subscriptionLink`-object, dat de abonnement-id bevat.

## <a name="create-subscriptions-for-an-mpa-billing-account"></a>Abonnementen maken voor een MPA-factureringsrekening

Gebruik de informatie in de volgende secties om abonnementen te maken voor een MCA-factureringsaccount.

### <a name="prerequisites"></a>Vereisten

In het account van de Cloud Solution Provider van uw organisatie moet u de rol van globale beheerder of beheerderagent hebben om een abonnement voor uw factureringsrekening te maken. Zie [Partner Center - Gebruikersrollen en -machtigingen toewijzen](/partner-center/permissions-overview) voor meer informatie.

In de volgende voorbeelden worden REST API's gebruikt. PowerShell en Azure CLI worden momenteel niet ondersteund.

### <a name="find-the-billing-accounts-that-you-have-access-to"></a>De factureringsrekeningen zoeken waartoe u toegang hebt

Voer de onderstaande aanvraag uit om alle factureringsrekeningen weer te geven waartoe u toegang hebt.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

In de API-respons worden de factureringsrekeningen vermeld.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftPartnerAgreement",
        "displayName": "Contoso",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "name": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "4f89e155-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "displayName": "Fabrikam",
        "hasReadAccess": true,
        "organizationId": "1d100e69-xxxx-xxxx-xxxx-xxxxxxxxxxxxx_xxxx-xx-xx"
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}

```
Gebruik de eigenschap `displayName` om de factureringsrekening te identificeren waarvoor u abonnementen wilt maken. Zorg ervoor dat agreementType van het account gelijk is aan *MicrosoftPartnerAgreement*. Kopieer de `name` voor het account. Kopieer bijvoorbeeld `99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` om een abonnement te maken voor het factureringsaccount `Contoso`. Plak de waarde ergens, zodat u deze in de volgende stap kunt gebruiken.

### <a name="find-customers-that-have-azure-plans"></a>Klanten zoeken die Azure-plannen hebben

Voer de volgende aanvraag uit, waarbij u `<billingAccountName>` vervangt door de `name` die in de eerste stap (```5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx```) is gekopieerd, om alle klanten in de factureringsrekening te vermelden voor wie u Azure-abonnementen kunt maken.

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/<billingAccountName>/customers?api-version=2019-10-01-preview
```
In de API-respons worden de klanten in de factureringsrekening vermeld die een Azure-plan hebben. U kunt abonnementen voor de klanten maken.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Contoso USD",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam toys"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "97c3fac4-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "properties": {
        "billingProfileDisplayName": "Fabrikam sports",
        "billingProfileId": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/JUT6-xxxx-xxxx-xxxx",
        "displayName": "Fabrikam bakery"
      },
      "type": "Microsoft.Billing/billingAccounts/customers"
    }]
}

```

Gebruik eigenschap `displayName` om de klant te identificeren waarvoor u abonnementen wilt maken. Kopieer de `id` voor de klant. Kopieer bijvoorbeeld `/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx` om een abonnement te maken voor `Fabrikam toys`. Plak deze waarde ergens voor gebruik in latere stappen.

### <a name="optional-for-indirect-providers-get-the-resellers-for-a-customer"></a>Optioneel voor indirecte providers: De resellers voor een klant ophalen

Als u een indirecte provider in het CSP-model met twee lagen bent, kunt u een reseller opgeven tijdens het maken van abonnementen voor klanten.

Voer de volgende aanvraag uit en vervang `<customerId>` door de `id` die is gekopieerd in de tweede stap (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) om alle resellers weer te geven die beschikbaar zijn voor een klant.

```json
GET https://management.azure.com<customerId>?$expand=resellers&api-version=2019-10-01-preview
```
In de API-respons worden de resellers voor de klant vermeld:

```json
{
  "value": [{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "2ed2c490-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "3xxxxx",
        "description": "Wingtip"
      }
    ]
  }
},
{
  "id": "/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "4ed2c793-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Billing/billingAccounts/customers",
  "properties": {
    "displayName": "Fabrikam toys",
    "resellers": [
      {
        "resellerId": "5xxxxx",
        "description": "Tailspin"
      }
    ]
  }
}]
}
```

Gebruik eigenschap `description` om de reseller te identificeren die aan het abonnement moet worden gekoppeld. Kopieer de `resellerId` voor de reseller. Kopieer bijvoorbeeld `3xxxxx` om `Wingtip` te koppelen. Plak de waarde ergens, zodat u deze in de volgende stap kunt gebruiken.

### <a name="create-a-subscription-for-a-customer"></a>Een abonnement voor een klant maken

In het volgende voorbeeld wordt een abonnement met de naam *Dev Team subscription* voor *Fabrikam toys* gemaakt en wordt reseller *Wingtip* aan het abonnement gekoppeld. 

Voer de volgende aanvraag uit en vervang `<customerId>` door de `id` die u tijdens de tweede stap (```/providers/Microsoft.Billing/billingAccounts/99a13315-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/customers/2281f543-xxxx-xxxx-xxxx-xxxxxxxxxxxx```) hebt gekopieerd. Geef de optionele *resellerId*, die in de tweede stap is gekopieerd, door aan de aanvraagparameters van de API.

```json
POST https://management.azure.com<customerId>/providers/Microsoft.Subscription/createSubscription?api-version=2018-11-01-preview
```

```json
'{"displayName": "Dev Team subscription",
  "skuId": "0001",
  "resellerId": "<resellerId>",
}'
```

| Naam van element  | Vereist | Type   | Beschrijving                                                                                               |
|---------------|----------|--------|-----------------------------------------------------------------------------------------------------------|
| `displayName` | Ja      | Tekenreeks | De weergavenaam van het abonnement.|
| `skuId` | Ja      | Tekenreeks | De SKU-id van het Azure-plan. Gebruik *0001* voor abonnementen van het type Microsoft Azure Plan |
| `resellerId`      | Nee       | Tekenreeks | De MPN-id van de reseller die aan het abonnement wordt gekoppeld.  |

In de respons krijgt u een `subscriptionCreationResult`-object terug voor bewaking. Wanneer het abonnement is gemaakt, retourneert het `subscriptionCreationResult`-object een `subscriptionLink`-object. Dit bevat de abonnements-id.

## <a name="next-steps"></a>Volgende stappen

- Zie [sample code on GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core) (voorbeeldcode op GitHub) voor een voorbeeld voor het maken van een Enterprise Agreement (EA)-abonnement met behulp van .NET.
* Nu u een abonnement hebt gemaakt, kunt u die mogelijkheid verlenen aan andere gebruikers en service-principals. Zie [Toegang verlenen voor het maken van Azure Enterprise-abonnementen (preview)](grant-access-to-create-subscription.md) voor meer informatie.
* Zie [Resources organiseren met Azure-beheergroepen](../../governance/management-groups/overview.md) voor meer informatie over het beheren van grote aantallen abonnementen met behulp van beheergroepen.
