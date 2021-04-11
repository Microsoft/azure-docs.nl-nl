---
Titel: Media Services account ARM-sjabloon: Azure Media Services beschrijving: in dit artikel wordt beschreven hoe u een ARM-sjabloon gebruikt om een Media Services-account te maken.
Services: Media Services documentationcenter: ' ' Auteur: IngridAtMicrosoft Manager: femila editor: ' '

MS. service: Media-Services MS. workload: MS. topic: Snelstartgids MS. date: 03/23/2021 MS. Author: inhenkel MS. Custom: onderwerp-armqs

---

# <a name="quickstart-media-services-account-arm-template"></a>Quickstart: ARM-sjabloon van Azure Media Services-account

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

In dit artikel wordt uitgelegd hoe u een Media Services-account kunt maken met een Azure Resource Manager-sjabloon.

## <a name="introduction"></a>Inleiding

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Lezers die ervaring hebben met ARM-sjablonen kunnen doorgaan met de [sectie over implementatie](#deploy-the-template).

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-media-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Als u nog nooit een ARM-sjabloon hebt geïmplementeerd, is het handig om meer te weten te komen over [Azure ARM-sjablonen](../../azure-resource-manager/templates/index.yml) en de [zelfstudie](../../azure-resource-manager/templates/template-tutorial-create-first-template.md?tabs=azure-powershell) te doen.

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstart-sjablonen](https://azure.microsoft.com/resources/templates/101-media-services-create/).

De syntaxis voor de JSON-code Fence is:

:::code language="json" source="~/quickstart-templates/101-media-services-create/azuredeploy.json":::

Er worden drie soorten Azure-resources gedefinieerd in de sjabloon:

- [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts): een opslagaccount maken
- [Microsoft.Media/mediaservices](/azure/templates/microsoft.media/mediaservices): een Media Services-account maken

## <a name="set-the-account"></a>Het account instellen

```azurecli-interactive

az account set --subscription {your subscription name or id}

```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

```azurecli-interactive

az group create --name {the name you want to give your resource group} --location "{pick a location}"

```

## <a name="assign-a-variable-to-your-deployment-file"></a>Wijs een variabele toe aan het implementatiebestand

Voor het gemak maakt u een variabele waarmee het pad naar het sjabloonbestand wordt opgeslagen. Deze variabele maakt het voor u gemakkelijker om de implementatieopdrachten uit te voeren omdat u het pad niet telkens opnieuw hoeft te typen.

```azurecli-interactive

templateFile="{provide the path to the template file}"

```

## <a name="deploy-the-template"></a>De sjabloon implementeren

U wordt gevraagd om de naam van het Media Services-account in te voeren.

```azurecli-interactive

az deployment group create --name {the name you want to give to your deployment} --resource-group {the name of resource group you created} --template-file $templateFile

```

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

U ziet een JSON-antwoord van deze strekking:

```json
{
  "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Resources/deployments/amsarmquickstartdeploy",
  "location": null,
  "name": "amsarmquickstartdeploy",
  "properties": {
    "correlationId": "{correlationid}",
    "debugSetting": null,
    "dependencies": [
      {
        "dependsOn": [
          {
            "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
            "resourceGroup": "amsarmquickstartrg",
            "resourceName": "storagey44cfdmliwatk",
            "resourceType": "Microsoft.Storage/storageAccounts"
          }
        ],
        "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg",
        "resourceName": "{accountname}",
        "resourceType": "Microsoft.Media/mediaServices"
      }
    ],
    "duration": "PT1M10.8615001S",
    "error": null,
    "mode": "Incremental",
    "onErrorDeployment": null,
    "outputResources": [
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Media/mediaServices/{accountname}",
        "resourceGroup": "amsarmquickstartrg"
      },
      {
        "id": "/subscriptions/{subscriptionid}/resourceGroups/amsarmquickstartrg/providers/Microsoft.Storage/storageAccounts/storagey44cfdmliwatk",
        "resourceGroup": "amsarmquickstartrg"
      }
    ],
    "outputs": null,
    "parameters": {
      "mediaServiceName": {
        "type": "String",
        "value": "{accountname}"
      }
    },
    "parametersLink": null,
    "providers": [
      {
        "id": null,
        "namespace": "Microsoft.Media",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "mediaServices"
          }
        ]
      },
      {
        "id": null,
        "namespace": "Microsoft.Storage",
        "registrationPolicy": null,
        "registrationState": null,
        "resourceTypes": [
          {
            "aliases": null,
            "apiVersions": null,
            "capabilities": null,
            "locations": [
              "eastus"
            ],
            "properties": null,
            "resourceType": "storageAccounts"
          }
        ]
      }
    ],
    "provisioningState": "Succeeded",
    "templateHash": "{templatehash}",
    "templateLink": null,
    "timestamp": "2020-11-24T23:25:52.598184+00:00",
    "validatedResources": null
  },
  "resourceGroup": "amsarmquickstartrg",
  "tags": null,
  "type": "Microsoft.Resources/deployments"
}

```

Bevestig in Azure Portal dat uw resources zijn gemaakt.

![quickstartresources gemaakt](./media/media-services-arm-template-quickstart/quickstart-arm-template-resources.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u niet van plan bent om de resources te gebruiken die u zojuist hebt gemaakt, kunt u de resourcegroep verwijderen.

```azurecli-interactive

az group delete --name {name of the resource group}

```

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het gebruik van een ARM-sjabloon door het proces te volgen voor het maken van een sjabloon met parameters, variabelen en meer, kunt u de zelfstudie raadplegen

> [!div class="nextstepaction"]
> [Zelfstudie: uw eerste ARM-sjabloon maken en implementeren](../../azure-resource-manager/templates/template-tutorial-create-first-template.md)