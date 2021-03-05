---
title: Resource Manager-sjablonen implementeren met behulp van GitHub-acties
description: Hierin wordt beschreven hoe u Azure Resource Manager sjablonen (ARM-sjablonen) implementeert met behulp van GitHub-acties.
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 243316b32d5b0cf62f03ae77d8a9fb919743ace1
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172827"
---
# <a name="deploy-arm-templates-by-using-github-actions"></a>ARM-sjablonen implementeren met behulp van GitHub-acties

[Github-acties](https://docs.github.com/en/actions) is een reeks functies in github voor het automatiseren van uw werk stromen voor software ontwikkeling op dezelfde locatie waar u code opslaat en samen werken aan pull-aanvragen en-problemen.

Gebruik de [actie Azure Resource Manager sjabloon implementeren](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) om de implementatie van een Azure Resource Manager sjabloon (arm-sjabloon) naar Azure te automatiseren.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een GitHub-account. Als u geen account hebt, kunt u zich registreren voor een [gratis](https://github.com/join) account.
    - Een GitHub-opslag plaats voor het opslaan van uw Resource Manager-sjablonen en uw werk stroom bestanden. Zie [een nieuwe opslag plaats maken](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository)om er een te maken.


## <a name="workflow-file-overview"></a>Overzicht van werkstroom bestand

Een werkstroom wordt gedefinieerd door een YAML-bestand (.yml) in het pad `/.github/workflows/` in uw opslagplaats. Deze definitie bevat de verschillende stappen en parameters die deel uitmaken van de werkstroom.

Het bestand heeft twee secties:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | 1. Definieer een service-principal. <br /> 2. Maak een GitHub-opslagplaats. |
|**Implementeren** | 1. Implementeer de Resource Manager-sjabloon. |

## <a name="generate-deployment-credentials"></a>Genereer implementatiereferenties


U kunt een [service-principal](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) maken met de opdracht [az ad sp create-for-rbac](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) in de [Azure CLI](/cli/azure/). Voer deze opdracht uit met [Azure Cloud Shell](https://shell.azure.com/) in de Azure Portal of door de knop **Uitproberen** te selecteren.

Maak een resource groep als u deze nog niet hebt.

```azurecli-interactive
    az group create -n {MyResourceGroup}
```

Vervang de plaatsaanduiding `myApp` door de naam van uw toepassing.

```azurecli-interactive
   az ad sp create-for-rbac --name {myApp} --role contributor --scopes /subscriptions/{subscription-id}/resourceGroups/{MyResourceGroup} --sdk-auth
```

In het bovenstaande voorbeeld vervangt u de plaatsaanduidingen door uw abonnements-id en resourcegroepsnaam. De uitvoer is een JSON-object met de roltoewijzingsreferenties die toegang bieden tot uw App Service-app, vergelijkbaar met hieronder. Kopieer dit JSON-object voor later gebruik. U hebt alleen de secties nodig met de waarden `clientId`, `clientSecret`, `subscriptionId` en `tenantId`.

```output
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Het is altijd een goed idee om minimale toegang te verlenen. Het bereik in het vorige voor beeld is beperkt tot de resource groep.



## <a name="configure-the-github-secrets"></a>De GitHub-geheimen configureren

U moet geheimen maken voor uw Azure-referenties, resource groep en abonnementen.

1. In [GitHub](https://github.com/), bladert u in uw opslagplaats.

1. Selecteer **Instellingen > Geheimen > Nieuwe geheimen**.

1. Plak de volledige JSON-uitvoer van de Azure CLI-opdracht in het waardeveld van het geheim. Geef het geheim de naam `AZURE_CREDENTIALS`.

1. Maak nog een geheim met de naam `AZURE_RG` . Voeg de naam van uw resource groep toe aan het veld waarde van het geheim (bijvoorbeeld: `myResourceGroup` ).

1. Maak een aanvullend geheim met de naam `AZURE_SUBSCRIPTION` . Voeg uw abonnements-ID toe aan het veld waarde van het geheim (bijvoorbeeld: `90fd3f9d-4c61-432d-99ba-1273f236afa2` ).

## <a name="add-resource-manager-template"></a>Resource Manager-sjabloon toevoegen

Voeg een resource manager-sjabloon toe aan uw GitHub-opslag plaats. Met deze sjabloon maakt u een opslag account.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

U kunt het bestand plaatsen op een wille keurige plaats in de opslag plaats. In het workflow-voor beeld in de volgende sectie wordt ervan uitgegaan dat het sjabloon bestand de naam **azuredeploy.js** heeft en het is opgeslagen in de hoofdmap van uw opslag plaats.

## <a name="create-workflow"></a>Werkstroom maken

Het werk stroom bestand moet worden opgeslagen in de map **. github/werk stromen** in de hoofdmap van uw opslag plaats. De extensie van een werk stroom bestand kan **. yml** of **. yaml**.

1. Selecteer in de GitHub-opslag plaats **acties** in het bovenste menu.
1. Selecteer **nieuwe werk stroom**.
1. Selecteer **zelf een werk stroom instellen**.
1. Wijzig de naam van het werk stroom bestand als u de voor keur geeft aan een andere naam dan **Main. yml**. Bijvoorbeeld: **deployStorageAccount. yml**.
1. Vervang de inhoud van het yml-bestand door het volgende:

    ```yml
    on: [push]
    name: Azure ARM
    jobs:
      build-and-deploy:
        runs-on: ubuntu-latest
        steps:

          # Checkout code
        - uses: actions/checkout@main

          # Log into Azure
        - uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}

          # Deploy ARM template
        - name: Run ARM deploy
          uses: azure/arm-deploy@v1
          with:
            subscriptionId: ${{ secrets.AZURE_SUBSCRIPTION }}
            resourceGroupName: ${{ secrets.AZURE_RG }}
            template: ./azuredeploy.json
            parameters: storageAccountType=Standard_LRS

          # output containerName variable from template
        - run: echo ${{ steps.deploy.outputs.containerName }}
    ```
    > [!NOTE]
    > U kunt in plaats daarvan een JSON-indelings parameter bestand opgeven in de actie ARM implementeren (bijvoorbeeld: `.azuredeploy.parameters.json` ).

    De eerste sectie van het werk stroom bestand bevat:

    - **naam**: de naam van de werk stroom.
    - **op**: de naam van de GitHub-gebeurtenissen die de werk stroom activeren. De werk stroom wordt geactiveerd wanneer er sprake is van een push gebeurtenis op de hoofd vertakking, waardoor ten minste één van de opgegeven bestanden wordt gewijzigd. De twee bestanden zijn het werk stroom bestand en het sjabloon bestand.

1. Selecteer **Doorvoeren starten**.
1. Selecteer **rechtstreeks door voeren naar de hoofd vertakking**.
1. Selecteer **nieuw bestand door voeren** (of **wijzigingen door voeren**).

Omdat de werk stroom zo is geconfigureerd dat deze wordt geactiveerd door het werk stroom bestand of het sjabloon bestand dat wordt bijgewerkt, wordt de werk stroom meteen gestart nadat u de wijzigingen hebt doorgevoerd.

## <a name="check-workflow-status"></a>Werk stroom status controleren

1. Selecteer het tabblad **acties** . Er wordt een **deployStorageAccount. yml** -werk stroom weer gegeven. Het duurt 1-2 minuten om de werk stroom uit te voeren.
1. Selecteer de werk stroom om deze te openen.
1. Selecteer **arm implementeren** in het menu om de implementatie te controleren.

## <a name="clean-up-resources"></a>Resources opschonen
Als uw resource groep en opslag plaats niet meer nodig zijn, moet u de resources opschonen die u hebt geïmplementeerd door de resource groep en de GitHub-opslag plaats te verwijderen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw eerste ARM-sjabloon maken](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Module leren: de implementatie van ARM-sjablonen automatiseren door gebruik te maken van GitHub-acties](/learn/modules/deploy-templates-command-line-github-actions/)
