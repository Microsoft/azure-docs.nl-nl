---
title: Resources implementeren met REST API en sjabloon
description: Gebruik Azure Resource Manager en Resource Manager-REST API om resources te implementeren in Azure. De resources zijn gedefinieerd in een Resource Manager-sjabloon.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 90e50598176ddc0327a81df105740f58afd930bc
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105732564"
---
# <a name="deploy-resources-with-arm-templates-and-azure-resource-manager-rest-api"></a>Resources implementeren met ARM-sjablonen en Azure Resource Manager REST API

In dit artikel wordt uitgelegd hoe u de Azure Resource Manager REST API met Azure Resource Manager sjablonen (ARM-sjablonen) kunt gebruiken om uw resources te implementeren in Azure.

U kunt uw sjabloon insluiten in de hoofd tekst van de aanvraag of een koppeling naar een bestand. Wanneer u een bestand gebruikt, kan dit een lokaal bestand zijn of een extern bestand dat beschikbaar is via een URI. Als uw sjabloon zich in een opslag account bevindt, kunt u de toegang tot de sjabloon beperken en een SAS-token (Shared Access Signature) opgeven tijdens de implementatie.

## <a name="deployment-scope"></a>Implementatie bereik

U kunt uw implementatie richten op een resource groep, een Azure-abonnement, een beheer groep of een Tenant. Afhankelijk van het bereik van de implementatie, gebruikt u verschillende opdrachten.

- Gebruik [implementaties-maken](/rest/api/resources/resources/deployments/createorupdate)om te implementeren in een **resource groep**. De aanvraag wordt verzonden naar:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

- Als u wilt implementeren in een **abonnement**, gebruikt u [implementaties-maken bij abonnements bereik](/rest/api/resources/resources/deployments/createorupdateatsubscriptionscope). De aanvraag wordt verzonden naar:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

  Zie [resource groepen en-resources op abonnements niveau maken](deploy-to-subscription.md)voor meer informatie over implementaties op abonnements niveau.

- Als u wilt implementeren in een **beheer groep**, gebruikt u [implementaties-maken voor het bereik van de beheer groep](/rest/api/resources/resources/deployments/createorupdateatmanagementgroupscope). De aanvraag wordt verzonden naar:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

  Zie [resources maken op het niveau van de beheer groep](deploy-to-management-group.md)voor meer informatie over implementaties op het niveau van beheer groepen.

- Als u wilt implementeren in een **Tenant**, gebruikt u [implementaties-maken of bijwerken in het Tenant bereik](/rest/api/resources/resources/deployments/createorupdateattenantscope). De aanvraag wordt verzonden naar:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
  ```

  Zie [resources maken op Tenant niveau](deploy-to-tenant.md)voor meer informatie over implementaties op Tenant niveau.

In de voor beelden in dit artikel worden de implementaties van resource groepen gebruikt.

## <a name="deploy-with-the-rest-api"></a>Implementeren met de REST-API

1. [Algemene para meters en kopteksten](/rest/api/azure/)instellen, inclusief verificatie tokens.

1. Als u implementeert in een resource groep die niet bestaat, maakt u de resource groep. Geef uw abonnements-ID, de naam van de nieuwe resource groep en de locatie op die u nodig hebt voor uw oplossing. Zie [een resource groep maken](/rest/api/resources/resources/resourcegroups/createorupdate)voor meer informatie.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2020-06-01
   ```

   Met een aanvraag tekst zoals:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Voordat u uw sjabloon implementeert, kunt u een voor beeld bekijken van de wijzigingen die door de sjabloon in uw omgeving worden aangebracht. Gebruik de [bewerking What-if](template-deploy-what-if.md) om te controleren of de sjabloon de wijzigingen aanbrengt die u verwacht. Wat-als ook de sjabloon voor fouten valideert.

1. Als u een sjabloon wilt implementeren, geeft u uw abonnements-ID, de naam van de resource groep, de naam van de implementatie op in de aanvraag-URI.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2020-10-01
   ```

   Geef in de hoofd tekst van de aanvraag een koppeling op naar uw sjabloon en parameter bestand. Zie [Een Resource Manager-parameterbestand maken](parameter-files.md) voor meer informatie over het parameterbestand.

   U ziet `mode` dat de is ingesteld op **Incrementeel**. Stel `mode` in op **volt ooien** als u een volledige implementatie wilt uitvoeren. Wees voorzichtig met het gebruik van de volledige modus, omdat u per ongeluk resources kunt verwijderen die zich niet in uw sjabloon bevinden.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    Als u de respons inhoud wilt registreren, moet u de inhoud van de aanvraag of beide insluit `debugSetting` in het verzoek.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    U kunt uw opslag account instellen om een SAS-token (Shared Access Signature) te gebruiken. Zie [Toegang delegeren met een hand tekening voor gedeelde toegang](/rest/api/storageservices/delegate-access-with-shared-access-signature)voor meer informatie.

    Als u een gevoelige waarde voor een para meter (bijvoorbeeld een wacht woord) moet opgeven, voegt u die waarde toe aan een sleutel kluis. Haal de sleutel kluis op tijdens de implementatie, zoals wordt weer gegeven in het vorige voor beeld. Zie [Azure Key Vault gebruiken om de waarde van een beveiligde para meter door te geven tijdens de implementatie](key-vault-parameter.md)voor meer informatie.

1. In plaats van te koppelen aan bestanden voor de sjabloon en para meters, kunt u deze in de hoofd tekst van de aanvraag toevoegen. In het volgende voor beeld wordt de hoofd tekst van de aanvraag met de sjabloon en de parameter inline weer gegeven:

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
          "storageAccountType": {
            "type": "string",
            "defaultValue": "Standard_LRS",
            "allowedValues": [
              "Standard_LRS",
              "Standard_GRS",
              "Standard_ZRS",
              "Premium_LRS"
            ],
            "metadata": {
              "description": "Storage Account type"
            }
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. Gebruik [implementaties-ophalen](/rest/api/resources/resources/deployments/get)om de status van de sjabloon implementatie te verkrijgen.

   ```HTTP
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-10-01
   ```

## <a name="deployment-name"></a>Naam van implementatie

U kunt uw implementatie een naam geven, zoals `ExampleDeployment` .

Telkens wanneer u een implementatie uitvoert, wordt een item toegevoegd aan de implementatie geschiedenis van de resource groep met de naam van de implementatie. Als u een andere implementatie uitvoert en deze dezelfde naam geeft, wordt de vorige vermelding vervangen door de huidige implementatie. Als u de unieke vermeldingen in de implementatie geschiedenis wilt behouden, geeft u elke implementatie een unieke naam.

Als u een unieke naam wilt maken, kunt u een wille keurig getal toewijzen. U kunt ook een datum waarde toevoegen.

Als u gelijktijdige implementaties uitvoert naar dezelfde resource groep met dezelfde implementatie naam, wordt alleen de laatste implementatie voltooid. Implementaties met dezelfde naam die nog niet zijn voltooid, worden vervangen door de laatste implementatie. Als u bijvoorbeeld een implementatie uitvoert met de naam `newStorage` die een opslag account implementeert `storage1` en tegelijkertijd een andere implementatie uitvoert met de naam `newStorage` die een opslag account implementeert `storage2` , implementeert u slechts één opslag account. De naam van het resulterende opslag account is `storage2` .

Als u echter een implementatie uitvoert met de naam `newStorage` die een opslag account implementeert `storage1` en direct nadat de implementatie is voltooid, voert u `newStorage` `storage2` twee opslag accounts uit met een naam die een opslag account implementeert. Een heeft `storage1` de naam en de andere heet `storage2` . Maar u hebt slechts één vermelding in de implementatie geschiedenis.

Wanneer u een unieke naam voor elke implementatie opgeeft, kunt u deze gelijktijdig zonder conflict uitvoeren. Als u een implementatie uitvoert met de naam `newStorage1` die een opslag account implementeert `storage1` en tegelijkertijd een andere implementatie uitvoert met de naam `newStorage2` die een opslag account implementeert `storage2` , hebt u twee opslag accounts en twee vermeldingen in de implementatie geschiedenis.

Geef elke implementatie een unieke naam om conflicten met gelijktijdige implementaties te voor komen en te zorgen voor unieke vermeldingen in de implementatie geschiedenis.

## <a name="next-steps"></a>Volgende stappen

- Als u wilt terugkeren naar een geslaagde implementatie wanneer u een fout krijgt, raadpleegt u [herstellen bij fout naar geslaagde implementatie](rollback-on-error.md).
- Zie [Azure Resource Manager implementatie modi](deployment-modes.md)om op te geven hoe u resources wilt afhandelen die in de resource groep aanwezig zijn, maar die niet zijn gedefinieerd in de sjabloon.
- Zie [asynchrone Azure-bewerkingen volgen](../management/async-operations.md)voor meer informatie over het verwerken van asynchrone rest-bewerkingen.
- Zie [inzicht krijgen in de structuur en syntaxis van arm-sjablonen](template-syntax.md)voor meer informatie over sjablonen.
