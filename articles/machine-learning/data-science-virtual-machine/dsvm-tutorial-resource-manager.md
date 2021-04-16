---
title: 'Quickstart: Een Data Science VM maken met behulp van een Resource Manager-sjabloon'
titleSuffix: Azure Data Science Virtual Machine
description: In deze quickstart gebruikt u een Azure Resource Manager-sjabloon om snel een Data Science Virtual Machine te implementeren.
services: machine-learning
author: lobrien
ms.author: laobri
ms.date: 06/10/2020
ms.topic: quickstart
ms.service: data-science-vm
ms.custom:
- subject-armqs
- mode-arm
ms.openlocfilehash: 0683634223a63281ce2b42ebb02f87f9211a589e
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530652"
---
# <a name="quickstart-create-an-ubuntu-data-science-virtual-machine-using-an-arm-template"></a>Quickstart: Een virtuele Ubuntu Data Science-machine maken met een ARM-sjabloon

In deze quickstart ziet u hoe u een Data Science Virtual Machine maakt voor Ubuntu 18.04 met behulp van een Azure Resource Manager-sjabloon (ARM-sjabloon). Data Science Virtual Machines zijn virtuele machines in de cloud waarop vooraf een reeks frameworks en hulpprogramma's voor gegevenswetenschappen en machine learning wordt geladen. Wanneer de VM wordt geïmplementeerd op rekenresources met een GPU, worden alle hulpprogramma's en bibliotheken geconfigureerd voor het gebruik van de GPU.

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-ubuntu-DSVM-GPU-or-CPU%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

* Een Azure-abonnement. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/services/machine-learning/) aan voordat u begint.

* Als u de CLI-opdrachten in dit document wilt gebruiken vanuit uw **lokale omgeving**, hebt u de [Azure CLI nodig](/cli/azure/install-azure-cli).

## <a name="review-the-template"></a>De sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-vm-ubuntu-DSVM-GPU-or-CPU/).

:::code language="json" source="~/quickstart-templates/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json":::

De volgende resources zijn gedefinieerd in de sjabloon:

* [Microsoft.Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
* [Microsoft.Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
* [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
* [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
* [Microsoft.Storage/storageAccounts](/azure/templates/microsoft.storage/storageaccounts)
* [Microsoft.Compute/virtualMachines](/azure/templates/microsoft.compute/virtualmachines): Maak een virtuele machine in de cloud. In deze sjabloon wordt de virtuele machine geconfigureerd als een Data Science Virtual Machine met Ubuntu 18.04.

## <a name="deploy-the-template"></a>De sjabloon implementeren

Als u de sjabloon uit de Azure CLI wilt gebruiken, meldt u zich aan en kiest u uw abonnement (Zie [Aanmelden met Azure CLI](/cli/azure/authenticate-azure-cli)). Voer vervolgens

```azurecli-interactive
read -p "Enter the name of the resource group to create:" resourceGroupName &&
read -p "Enter the Azure location (e.g., centralus):" location &&
read -p "Enter the authentication type (must be 'password' or 'sshPublicKey') :" authenticationType &&
read -p "Enter the login name for the administrator account (may not be 'admin'):" adminUsername &&
read -p "Enter administrator account secure string (value of password or ssh public key):" adminPasswordOrKey &&
templateUri="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-ubuntu-DSVM-GPU-or-CPU/azuredeploy.json" &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri $templateUri --parameters adminUsername=$adminUsername authenticationType=$authenticationType adminPasswordOrKey=$adminPasswordOrKey &&
echo "Press [ENTER] to continue ..." &&
read
```

Wanneer u de bovenstaande opdracht uitvoert, voert u het volgende in:

1. De naam van de resourcegroep die u wilt maken voor het opslaan van de DSVM en de bijbehorende resources
1. De Azure-locatie waarin u de implementatie wilt uitvoeren.
1. Het type verificatie dat u wilt gebruiken (voer de tekenreeks `password` of `sshPublicKey` in).
1. De aanmeldingsnaam van het beheerdersaccount (deze waarde mag niet `admin` zijn).
1. De waarde van het wachtwoord of de openbare SSH-sleutel voor het account.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

Uw Data Science Virtual Machine weergeven:

1. Ga naar de [Azure-portal](https://portal.azure.com)
1. Meld u aan.
1. Kies de resourcegroep die u net hebt gemaakt.

U ziet de gegevens van de resourcegroep:

:::image type="content" source="media/dsvm-tutorial-resource-manager/resource-group-home.png" alt-text="Schermopname van een basisresource groep met een DSVM":::

Klik op de resource van de virtuele machine om naar de pagina met gegevens van de VM te gaan. Hier vindt u informatie over de virtuele machine, waaronder verbindingsgegevens.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze virtuele machine wilt gebruiken, verwijdert u deze. Aangezien de DSVM is gekoppeld aan andere resources, zoals een opslagaccount, wilt u waarschijnlijk de gehele resourcegroep verwijderen die u hebt gemaakt. U kunt de resourcegroep verwijderen via de portal door op de knop **Verwijderen** te klikken en te bevestigen. U kunt de resourcegroep ook uit de CLI verwijderen met:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Data Science Virtual Machine gemaakt op basis van een ARM-sjabloon.

> [!div class="nextstepaction"]
> [Voorbeelden van programma's en ML-scenario's](dsvm-samples-and-walkthroughs.md)
