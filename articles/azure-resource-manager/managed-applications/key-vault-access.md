---
title: Key Vault gebruiken bij het implementeren van een beheerde app
description: Laat zien hoe u toegangs geheimen in Azure Key Vault kunt gebruiken bij het implementeren van beheerde toepassingen
author: tfitzmac
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: tomfitz
ms.openlocfilehash: 5dc219747b9cc74b6c6aac8ab190ebfbe5ae9b32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "81458280"
---
# <a name="access-key-vault-secret-when-deploying-azure-managed-applications"></a>Key Vault geheim openen bij het implementeren van Azure Managed Applications

Wanneer u tijdens de implementatie een beveiligde waarde (zoals een wacht woord) wilt door geven, kunt u de waarde van een [Azure Key Vault](../../key-vault/general/overview.md)ophalen. Als u toegang wilt krijgen tot de Key Vault bij het implementeren van beheerde toepassingen, moet u toegang verlenen tot de service-principal van het **toestel bron provider** . De service Managed Applications gebruikt deze identiteit voor het uitvoeren van bewerkingen. De Service-Principal moet toegang hebben tot de Key Vault om een waarde op te halen uit een Key Vault tijdens de implementatie.

In dit artikel wordt beschreven hoe u de Key Vault configureert voor gebruik met beheerde toepassingen.

## <a name="enable-template-deployment"></a>Sjabloon implementatie inschakelen

1. Selecteer uw Key Vault in de portal.

1. Selecteer **Toegangsbeleid**.   

   ![Toegangs beleid selecteren](./media/key-vault-access/select-access-policies.png)

1. Selecteer **Klik hierop om geavanceerde beleidsregels weer te geven**.

   ![Geavanceerd toegangs beleid weer geven](./media/key-vault-access/advanced.png)

1. Selecteer **toegang tot Azure Resource Manager inschakelen voor sjabloon implementatie**. Selecteer vervolgens **Opslaan**.

   ![Sjabloon implementatie inschakelen](./media/key-vault-access/enable-template.png)

## <a name="add-service-as-contributor"></a>Service als Inzender toevoegen

1. Klik op **Toegangsbeheer (IAM)** .

   ![Toegangs beheer selecteren](./media/key-vault-access/access-control.png)

1. Selecteer **Roltoewijzing toevoegen**.

   ![Toevoegen selecteren](./media/key-vault-access/add-access-control.png)

1. Selecteer **Inzender** voor de rol. Zoek naar de **resource provider** van het apparaat en selecteer deze in de beschik bare opties.

   ![Provider zoeken](./media/key-vault-access/search-provider.png)

1. Selecteer **Opslaan**.

## <a name="reference-key-vault-secret"></a>Referentie Key Vault geheim

Als u een geheim wilt door geven van een Key Vault naar een sjabloon in uw beheerde toepassing, moet u een [gekoppelde of geneste sjabloon](../templates/linked-templates.md) gebruiken en verwijzen naar de Key Vault in de para meters voor de gekoppelde of geneste sjabloon. Geef de resource-ID van de Key Vault en de naam van het geheim op.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
        "description": "The location where the resources will be deployed."
      }
    },
    "vaultName": {
      "type": "string",
      "metadata": {
        "description": "The name of the keyvault that contains the secret."
      }
    },
    "secretName": {
      "type": "string",
      "metadata": {
        "description": "The name of the secret."
      }
    },
    "vaultResourceGroupName": {
      "type": "string",
      "metadata": {
        "description": "The name of the resource group that contains the keyvault."
      }
    },
    "vaultSubscription": {
      "type": "string",
      "defaultValue": "[subscription().subscriptionId]",
      "metadata": {
        "description": "The name of the subscription that contains the keyvault."
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "name": "dynamicSecret",
      "properties": {
        "mode": "Incremental",
        "expressionEvaluationOptions": {
          "scope": "inner"
        },
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "adminLogin": {
              "type": "string"
            },
            "adminPassword": {
              "type": "securestring"
            },
            "location": {
              "type": "string"
            }
          },
          "variables": {
            "sqlServerName": "[concat('sql-', uniqueString(resourceGroup().id, 'sql'))]"
          },
          "resources": [
            {
              "type": "Microsoft.Sql/servers",
              "apiVersion": "2018-06-01-preview",
              "name": "[variables('sqlServerName')]",
              "location": "[parameters('location')]",
              "properties": {
                "administratorLogin": "[parameters('adminLogin')]",
                "administratorLoginPassword": "[parameters('adminPassword')]"
              }
            }
          ],
          "outputs": {
            "sqlFQDN": {
              "type": "string",
              "value": "[reference(variables('sqlServerName')).fullyQualifiedDomainName]"
            }
          }
        },
        "parameters": {
          "location": {
            "value": "[parameters('location')]"
          },
          "adminLogin": {
            "value": "ghuser"
          },
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[resourceId(parameters('vaultSubscription'), parameters('vaultResourceGroupName'), 'Microsoft.KeyVault/vaults', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }
  ],
  "outputs": {
  }
}
```

## <a name="next-steps"></a>Volgende stappen

U hebt uw Key Vault zodanig geconfigureerd dat deze toegankelijk is tijdens de implementatie van een beheerde toepassing.

* Zie voor informatie over het door geven van een waarde van een Key Vault als een sjabloon parameter [Azure Key Vault gebruiken om een veilige parameter waarde door te geven tijdens de implementatie](../templates/key-vault-parameter.md).
* Zie [voorbeeld projecten voor door Azure beheerde toepassingen](sample-projects.md)voor voor beelden van beheerde toepassingen.
* Zie [Aan de slag met CreateUiDefinition](create-uidefinition-overview.md) voor meer informatie over het maken van een UI-definitiebestand voor een beheerde toepassing.
