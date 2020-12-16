---
title: Programmatisch Azure-abonnementen maken met een Azure Resource Manager-sjabloon
description: Ontdek hoe u programmatisch abonnementen kunt maken met behulp van een Azure Resource Manager-sjabloon.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 21791b096d66b73c522c50f38cae7fd8a1b9fb70
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97507554"
---
# <a name="programmatically-create-azure-subscriptions-with-an-azure-resource-manager-template"></a>Programmatisch Azure-abonnementen maken met een Azure Resource Manager-sjabloon

Dit artikel toont u hoe u programmatisch Azure-abonnementen kunt maken met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon). Azure-klanten met een facturerings account voor de volgende overeenkomsttypen kunnen met behulp van een ARM-sjabloon abonnementen maken:

- Enterprise Agreement (EA)
    - Zie voor meer informatie [Azure Enterprise REST API's](ea-portal-rest-apis.md)
- Microsoft-klantovereenkomst (MCA)
    - [Azure Billing REST API](/rest/api/billing)
    - [Azure-abonnement-API's](/rest/api/subscription)
- Microsoft Partner-overeenkomst (MPA)
    - [Azure Billing REST API](/rest/api/billing)
    - [Azure-abonnement-API's](/rest/api/subscription)

Wanneer u met behulp van een ARM-sjabloon een Azure-abonnement maakt, wordt dat abonnement beheerd op basis van de overeenkomst waaronder u Azure-services van Microsoft of een geautoriseerde reseller hebt verkregen. Zie de [Juridische informatie van Microsoft Azure](https://azure.microsoft.com/support/legal/) voor meer informatie.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="create-subscriptions-using-arm-templates"></a>Abonnementen maken met behulp van ARM-sjablonen

U kunt abonnementen maken met een Azure Resource Manager-sjabloon (ARM-sjabloon), zodat u uw implementatieprocessen voor productie en testen kunt automatiseren. In het volgende voorbeeld gebruikt u een ARM-sjabloon voor het maken van een Azure-abonnement en een Azure-resourcegroep.

## <a name="prerequisites"></a>Vereisten

Als u abonnementen wilt maken, moet u één van de onderstaande rollen hebben: 

- Eigenaar van het Azure-abonnement op een factuursectie
- Inzender van het Azure-abonnement op een factuursectie
- Auteur van het Azure-abonnement op een factuursectie
- Eigenaar van het Azure-abonnement voor een factureringsprofiel of een factureringsaccount
- Inzender van het Azure-abonnement voor een factureringsprofiel of een factureringsaccount

Voor meer informatie, zie [Rollen en taken voor abonnementsfacturering](understand-mca-roles.md#subscription-billing-roles-and-tasks).

Daarnaast moet u, omdat u een implementatie met een ARM-sjabloon uitvoert, schrijfmachtigingen hebben voor het hoofdobject. Om de ARM-implementatie onder een beheergroep te maken moet u schrijfmachtigingen hebben voor de beheergroep. De actie is uitsluitend voor het maken van een ARM-implementatie. Als een abonnement wordt gemaakt, wordt dit alleen gemaakt in de beheergroep die is opgegeven in de ARM-sjabloon.

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

Gebruik de eigenschap `displayName` om de factureringsrekening te identificeren waarvoor u abonnementen wilt maken. Zorg ervoor dat agreementType van het account gelijk is aan *MicrosoftCustomerAgreement*. Kopieer de `name` van het account. Kopieer bijvoorbeeld `5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx` om een abonnement te maken voor het factureringsaccount `Contoso`. Plak de waarde ergens, zodat u deze in de volgende stap kunt gebruiken.

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

### [Azure CLI](#tab/azure-cli-getBillingProfiles)

we're still working on enabling CLI SDK for billing APIs. Check back soon.
-->

---

## <a name="create-a-subscription-and-resource-group-with-a-template"></a>Een abonnement en resourcegroep maken met een sjabloon

Met de volgende ARM-sjabloon wordt een abonnement met de naam *Dev Team subscription* gemaakt voor de factuursectie *Development*. Het abonnement wordt gefactureerd voor het factureringsprofiel van *Contoso Billing Profile* en wordt weergegeven in de sectie *Development* van de factuur. U gebruikt het gekopieerde factureringsbereik uit de vorige stap: `/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx`. 

### <a name="request"></a>Aanvraag

```rest
PUT https://management.azure.com/providers/Microsoft.Resources/deployments/sampleTemplate?api-version=2019-10-01
```

### <a name="request-body"></a>Aanvraagbody

```json
{
  "properties":
    {
      "location": "westus",
      "properties": {
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {
            "uniqueAliasName": "sampleAlias"
          },
          "resources": [
            {
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2019-10-01",
              "name": "sampleTemplate",
              "location": "westus",
              "properties": {
                "expressionEvaluationOptions": {
                  "scope": "inner"
                },
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                  "contentVersion": "1.0.0.0",
                  "variables": {
                    "uniqueAliasName": "sampleAlias"
                  },
                  "resources": [
                    {
                      "name": "[variables('uniqueAliasName')]",
                      "type": "Microsoft.Subscription/aliases",
                      "apiVersion": "2020-09-01",
                      "properties": {
                        "workLoad": "Production",
                        "displayName": "Dev Team subscription",
                        "billingScope": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx_xxxx-xx-xx/billingProfiles/AW4F-xxxx-xxx-xxx/invoiceSections/SH3V-xxxx-xxx-xxx"
                      },
                      "dependsOn": [],
                      "tags": {}
                    }
                  ],
                  "outputs": {
                    "subscriptionId": {
                      "type": "string",
                      "value": "[replace(reference(variables('uniqueAliasName')).subscriptionId, 'invalidrandom/', '')]"
                    }
                  }
                }
              }
            },
            {
              "name": "sampleOuterResource",
              "type": "Microsoft.Resources/deployments",
              "apiVersion": "2019-10-01",
              "location": "westus",
              "properties": {
                "expressionEvaluationOptions": {
                  "scope": "inner"
                },
                "mode": "Incremental",
                "parameters": {
                  "subscriptionId": {
                    "value": "[reference('sampleTemplate').outputs.subscriptionId.value]"
                  }
                },
                "template": {
                  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
              "contentVersion": "1.0.0.0",
                  "parameters": {
                    "subscriptionId": {
                      "type": "string"
                    }
                  },
                  "variables": {},
                  "resources": [
                    {
                      "name": "sampleInnerResource",
                      "type": "Microsoft.Resources/deployments",
                      "subscriptionId": "[parameters('subscriptionId')]",
                      "apiVersion": "2019-10-01",
                      "location": "westus",
                      "properties": {
                        "expressionEvaluationOptions": {
                          "scope": "inner"
                        },
                        "mode": "Incremental",
                        "parameters": {},
                        "template": {
                          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
                          "contentVersion": "1.0.0.0",
                          "parameters": {},
                          "variables": {},
                          "resources": [
                            {
                              "type": "Microsoft.Resources/resourceGroups",
                              "apiVersion": "2020-05-01",
                              "location": "[deployment().location]",
                              "name": "sampleRG",
                              "properties": {},
                              "tags": {}
                            }
                          ],
                          "outputs": {}
                        }
                      }
                    }
                  ],
                  "outputs": {}
                }
              }
            }
          ],
          "outputs": {
            "messageFromLinkedTemplate": {
              "type": "string",
              "value": "[reference('sampleTemplate').outputs.subscriptionId.value]"
            }
          }
        },
        "mode": "Incremental"
      }
    }
}
```

### <a name="response"></a>Antwoord

```json
{
  "id": "/providers/Microsoft.Resources/deployments/sampleTemplate",
  "name": "sampleTemplate",
  "type": "Microsoft.Resources/deployments",
  "location": "westus",
  "properties": {
    "templateHash": "16005880870587497948",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Accepted",
    "timestamp": "2020-10-07T19:06:34.110811Z",
    "duration": "PT0.1345459S",
    "correlationId": "2b57ddf6-7e27-42cb-90b4-90eeccd11a28",
    "providers": [
      {
        "namespace": "Microsoft.Resources",
        "resourceTypes": [
          {
            "resourceType": "deployments",
            "locations": [
              "westus"
            ]
          }
        ]
      }
    ],
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/providers/Microsoft.Resources/deployments/sampleTemplate",
            "resourceType": "Microsoft.Resources/deployments",
            "resourceName": "anuragTemplate1"
          }
        ],
        "id": "/providers/Microsoft.Resources/deployments/sampleOuterResource",
        "resourceType": "Microsoft.Resources/deployments",
        "resourceName": "sampleOuterResource"
      }
    ]
  }
}
```

U kunt GET gebruiken om de status van de implementatie op te vragen en de voortgang te bewaken.

```json
GET https://management.azure.com/providers/Microsoft.Resources/deployments/sampleTemplate?api-version=2019-10-01
```

### <a name="response"></a>Antwoord

```json
{
  "id": "/providers/Microsoft.Resources/deployments/sampleDeployment5",
  "name": "sampleDeployment5",
  "type": "Microsoft.Resources/deployments",
  "location": "westus",
  "properties": {
    "templateHash": "16005880870587497948",
    "parameters": {},
    "mode": "Incremental",
    "provisioningState": "Succeeded",
    "timestamp": "2020-10-07T19:07:20.8007311Z",
    "duration": "PT46.824466S",
    "correlationId": "2b57ddf6-7e27-42cb-90b4-90eeccd11a28",
    "providers": [
      {
        "namespace": "Microsoft.Resources",
        "resourceTypes": [
          {
            "resourceType": "deployments",
            "locations": [
              "westus"
            ]
          }
        ]
      }
    ],
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/providers/Microsoft.Resources/deployments/sampleTemplate",
            "resourceType": "Microsoft.Resources/deployments",
            "resourceName": "sampleTemplate"
          }
        ],
        "id": "/providers/Microsoft.Resources/deployments/sampleOuterResource",
        "resourceType": "Microsoft.Resources/deployments",
        "resourceName": "sampleOuterResource"
      }
    ],
    "outputs": {
      "messageFromLinkedTemplate": {
        "type": "String",
        "value": "16edf959-11fd-48bb-9a46-85190963ead9"
      }
    },
    "outputResources": [
      {
        "id": "/providers/Microsoft.Subscription/aliases/sampleAlias"
      },
      {
        "id": "/subscriptions/16edf959-11fd-48bb-9a46-85190963ead9/resourceGroups/sampleRG"
      }
    ]
  }
}
```

In het vorige voor beeld ziet u dat het gemaakte abonnement `16edf959-11fd-48bb-9a46-85190963ead9` is gemaakt en dat de gemaakte resourcegroep `sampleRG` is.

## <a name="next-steps"></a>Volgende stappen

* Nu u een abonnement hebt gemaakt, kunt u die mogelijkheid verlenen aan andere gebruikers en service-principals. Zie [Toegang verlenen voor het maken van Azure Enterprise-abonnementen (preview)](grant-access-to-create-subscription.md) voor meer informatie.
* Zie [Resources organiseren met Azure-beheergroepen](../../governance/management-groups/overview.md) voor meer informatie over het beheren van grote aantallen abonnementen met behulp van beheergroepen.
