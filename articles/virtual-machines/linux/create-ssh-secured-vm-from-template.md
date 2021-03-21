---
title: Een virtuele Linux-machine in azure maken op basis van een sjabloon
description: De Azure CLI gebruiken om een virtuele Linux-machine te maken op basis van een resource manager-sjabloon
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.date: 03/22/2019
ms.author: cynthn
ms.openlocfilehash: a4e1bf56df52717255d2bae0a38186335d922ff1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554680"
---
# <a name="how-to-create-a-linux-virtual-machine-with-azure-resource-manager-templates"></a>Een virtuele Linux-machine maken met Azure Resource Manager sjablonen

Meer informatie over hoe u een virtuele Linux-machine (VM) maakt met behulp van een Azure Resource Manager sjabloon en de Azure CLI vanuit de Azure Cloud shell. Zie [een virtuele Windows-machine maken op basis van een resource manager-sjabloon](../windows/ps-template.md)voor het maken van een virtuele Windows-machine.

U kunt de sjabloon ook implementeren vanuit de Azure Portal. Als u de sjabloon wilt openen in de portal, selecteert u de knop **implementeren in azure** .

[![Implementeren in Azure](../../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-vm-sshkey%2Fazuredeploy.json)

## <a name="templates-overview"></a>Overzicht van sjablonen

Azure Resource Manager sjablonen zijn JSON-bestanden waarmee de infra structuur en configuratie van uw Azure-oplossing worden gedefinieerd. Door het gebruik van een sjabloon kunt u gedurende de levenscyclus de oplossing herhaaldelijk implementeren en erop vertrouwen dat uw resources consistent worden geïmplementeerd. Voor meer informatie over de indeling van de sjabloon en hoe u deze bouwt, raadpleegt u [Quick Start: Azure Resource Manager sjablonen maken en implementeren met behulp van de Azure Portal](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). Als u de JSON-syntaxis voor resourcetypen wilt bekijken, raadpleegt u [Define resources in Azure Resource Manager templates](/azure/templates/microsoft.compute/allversions) (Resources definiëren in Azure Resource Manager-sjablonen).

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Het maken van een virtuele machine in azure omvat meestal twee stappen:

1. Een resourcegroep maken. Een Azure-resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Voordat een virtuele machine wordt gemaakt, moet een resourcegroep worden gemaakt.
1. Hiermee maakt u een virtuele machine.

In het volgende voor beeld wordt een VM gemaakt op basis van een [Azure Quick](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json)start-sjabloon. Alleen SSH-verificatie is toegestaan voor deze implementatie. Wanneer u hierom wordt gevraagd, geeft u de waarde van uw eigen open bare SSH-sleutel op, zoals de inhoud van *~/.ssh/id_rsa. pub*. Als u een SSH-sleutel paar moet maken, raadpleegt [u een SSH-sleutel paar maken en gebruiken voor virtuele Linux-machines in azure](mac-create-ssh-keys.md). Hier volgt een kopie van de sjabloon:

[!code-json[create-linux-vm](~/quickstart-templates/101-vm-sshkey/azuredeploy.json)]

Als u het CLI-script wilt uitvoeren, selecteert u **proberen** het te openen met de Azure Cloud shell. Als u het script wilt plakken, klikt u met de rechter muisknop op de shell en selecteert u vervolgens **Plakken**:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the project name (used for generating resource names):" &&
read projectName &&
echo "Enter the administrator username:" &&
read username &&
echo "Enter the SSH public key:" &&
read key &&
az group create --name $resourceGroupName --location "$location" &&
az deployment group create --resource-group $resourceGroupName --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-vm-sshkey/azuredeploy.json --parameters projectName=$projectName adminUsername=$username adminPublicKey="$key" &&
az vm show --resource-group $resourceGroupName --name "$projectName-vm" --show-details --query publicIps --output tsv
```

De laatste Azure CLI-opdracht toont het open bare IP-adres van de zojuist gemaakte VM. U hebt het open bare IP-adres nodig om verbinding te maken met de virtuele machine. Zie de volgende sectie van dit artikel.

In het vorige voor beeld hebt u een sjabloon opgegeven die is opgeslagen in GitHub. U kunt ook een sjabloon downloaden of maken en het lokale pad opgeven met de `--template-file` para meter.

Hier volgen enkele aanvullende bronnen:

- Raadpleeg [Azure Resource Manager-documentatie](../../azure-resource-manager/index.yml) voor meer informatie over het ontwikkelen van Resource Manager-sjablonen.
- Zie [Naslag informatie over Azure-sjablonen](/azure/templates/microsoft.compute/allversions)voor een overzicht van de Azure virtual machine-schema's.
- Zie [Azure Quick](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)start-sjablonen voor meer voor beelden van virtuele-machine sjablonen.

## <a name="connect-to-virtual-machine"></a>Verbinding maken met de virtuele machine

U kunt de virtuele machine vervolgens als normaal SSHen naar uw VM. Geef het open bare IP-adres van de voor gaande opdracht op:

```bash
ssh <adminUsername>@<ipAddress>
```

## <a name="next-steps"></a>Volgende stappen

In dit voor beeld hebt u een virtuele Linux-machine gemaakt. Ga naar de [Azure Quick](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Compute&pageNumber=1&sort=Popular)start-sjablonen voor meer Resource Manager-sjablonen die toepassings raamwerken bevatten of complexere omgevingen maken.

Voor meer informatie over het maken van sjablonen bekijkt u de JSON-syntaxis en de eigenschappen voor de typen resources die u hebt geïmplementeerd:

- [Micro soft. Network/networkSecurityGroups](/azure/templates/microsoft.network/networksecuritygroups)
- [Microsoft.Network/publicIPAddresses](/azure/templates/microsoft.network/publicipaddresses)
- [Microsoft.Network/virtualNetworks](/azure/templates/microsoft.network/virtualnetworks)
- [Micro soft. Network/networkInterfaces](/azure/templates/microsoft.network/networkinterfaces)
- [Micro soft. Compute/informatie](/azure/templates/microsoft.compute/virtualmachines)
