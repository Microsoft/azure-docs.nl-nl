---
title: Sjablonen Resource Manager implementeren met behulp van GitHub Actions
description: Beschrijft hoe u Azure Resource Manager sjablonen (ARM-sjablonen) implementeert met behulp van GitHub Actions.
ms.topic: conceptual
ms.date: 10/13/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: ec29ae019555c54ccdcef9dd743706f8d6401bbd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781969"
---
# <a name="deploy-arm-templates-by-using-github-actions"></a>ARM-sjablonen implementeren met behulp van GitHub Actions

[GitHub Actions](https://docs.github.com/en/actions) is een suite met functies in GitHub waarmee u uw werkstromen voor softwareontwikkeling kunt automatiseren op dezelfde plaats waar u code opgeslagen en samenwerkt aan pull-aanvragen en -problemen.

Gebruik de [actie Azure Resource Manager sjabloon implementeren](https://github.com/marketplace/actions/deploy-azure-resource-manager-arm-template) om het implementeren van een Azure Resource Manager arm-sjabloon (ARM-sjabloon) in Azure te automatiseren.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een GitHub-account. Als u geen account hebt, kunt u zich registreren voor een [gratis](https://github.com/join) account.
    - Een GitHub-opslagplaats voor het opslaan van uw Resource Manager en uw werkstroombestanden. Zie Een nieuwe opslagplaats [maken als u er een wilt maken.](https://docs.github.com/en/github/creating-cloning-and-archiving-repositories/creating-a-new-repository)


## <a name="workflow-file-overview"></a>Overzicht van werkstroom bestand

Een werkstroom wordt gedefinieerd door een YAML-bestand (.yml) in het pad `/.github/workflows/` in uw opslagplaats. Deze definitie bevat de verschillende stappen en parameters die deel uitmaken van de werkstroom.

Het bestand heeft twee secties:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | 1. Definieer een service-principal. <br /> 2. Maak een GitHub-opslagplaats. |
|**Implementeren** | 1. Implementeer de Resource Manager sjabloon. |

## <a name="generate-deployment-credentials"></a>Genereer implementatiereferenties


U kunt een [service-principal](../../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) maken met de opdracht [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) in de [Azure CLI](/cli/azure/). Voer deze opdracht uit met [Azure Cloud Shell](https://shell.azure.com/) in de Azure Portal of door de knop **Uitproberen** te selecteren.

Maak een resourcegroep als u er nog geen hebt.

```azurecli-interactive
    az group create -n {MyResourceGroup} -l {location}
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
> Het is altijd een goed idee om minimale toegang te verlenen. Het bereik in het vorige voorbeeld is beperkt tot de resourcegroep.



## <a name="configure-the-github-secrets"></a>De GitHub-geheimen configureren

U moet geheimen maken voor uw Azure-referenties, resourcegroep en abonnementen.

1. In [GitHub](https://github.com/), bladert u in uw opslagplaats.

1. Selecteer **Instellingen > Geheimen > Nieuwe geheimen**.

1. Plak de volledige JSON-uitvoer van de Azure CLI-opdracht in het waardeveld van het geheim. Geef het geheim de naam `AZURE_CREDENTIALS`.

1. Maak nog een geheim met de naam `AZURE_RG` . Voeg de naam van uw resourcegroep toe aan het waardeveld van het geheim (bijvoorbeeld: `myResourceGroup` ).

1. Maak een extra geheim met de naam `AZURE_SUBSCRIPTION` . Voeg uw abonnements-id toe aan het waardeveld van het geheim (bijvoorbeeld: `90fd3f9d-4c61-432d-99ba-1273f236afa2` ).

## <a name="add-resource-manager-template"></a>Een Resource Manager toevoegen

Voeg een Resource Manager toe aan uw GitHub-opslagplaats. Met deze sjabloon maakt u een opslagaccount.

```url
https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
```

U kunt het bestand overal in de opslagplaats plaatsen. In het werkstroomvoorbeeld in de volgende sectie wordt ervan uitgenomen dat het sjabloonbestand de naam **azuredeploy.jsop** en wordt het opgeslagen in de hoofdmap van uw opslagplaats.

## <a name="create-workflow"></a>Werkstroom maken

Het werkstroombestand moet worden opgeslagen in **de map .github/workflows** in de hoofdmap van uw opslagplaats. De bestandsextensie van de werkstroom kan **.yml** of **.yaml zijn.**

1. Selecteer in uw GitHub-opslagplaats **acties** in het bovenste menu.
1. Selecteer **Nieuwe werkstroom.**
1. Selecteer **zelf een werkstroom instellen.**
1. Wijzig de naam van het werkstroombestand als u liever een andere naam hebt dan **main.yml.** Bijvoorbeeld: **deployStorageAccount.yml.**
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
    > U kunt in plaats daarvan een parametersbestand voor de JSON-indeling opgeven in de actie ARM Implementeren (voorbeeld: `.azuredeploy.parameters.json` ).

    De eerste sectie van het werkstroombestand bevat:

    - **name:** de naam van de werkstroom.
    - **on**: de naam van de GitHub-gebeurtenissen die de werkstroom activeren. De werkstroom wordt trigger wanneer er een pushgebeurtenis op de main branch, waarmee ten minste een van de twee opgegeven bestanden wordt wijzigt. De twee bestanden zijn het werkstroombestand en het sjabloonbestand.

1. Selecteer **Doorvoeren starten**.
1. Selecteer **Rechtstreeks naar de main branch.**
1. Selecteer **Nieuw bestand commit** (of Commit **changes).**

Omdat de werkstroom is geconfigureerd om te worden geactiveerd door het werkstroombestand of het sjabloonbestand dat wordt bijgewerkt, wordt de werkstroom direct gestart nadat u de wijzigingen hebt doorgevoerd.

## <a name="check-workflow-status"></a>Werkstroomstatus controleren

1. Selecteer het **tabblad** Acties. U ziet een **werkstroom Create deployStorageAccount.yml.** Het duurt 1-2 minuten om de werkstroom uit te voeren.
1. Selecteer de werkstroom om deze te openen.
1. Selecteer **ARM-implementatie uitvoeren** in het menu om de implementatie te controleren.

## <a name="clean-up-resources"></a>Resources opschonen
Wanneer uw resourcegroep en opslagplaats niet meer nodig zijn, schoont u de resources op die u hebt geïmplementeerd door de resourcegroep en uw GitHub-opslagplaats te verwijderen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Uw eerste ARM-sjabloon maken](./template-tutorial-create-first-template.md)

> [!div class="nextstepaction"]
> [Learn-module: De implementatie van ARM-sjablonen automatiseren met behulp van GitHub Actions](/learn/modules/deploy-templates-command-line-github-actions/)
