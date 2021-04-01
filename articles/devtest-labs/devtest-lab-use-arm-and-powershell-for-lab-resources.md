---
title: Labs maken of wijzigen met behulp van Azure Resource Manager sjablonen
description: Meer informatie over het gebruik van Azure Resource Manager sjablonen met Power shell om Labs automatisch te maken of te wijzigen in een DevTest-Lab
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 645f1d0717514d2c7e7b16844513327127e4e1a8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87272637"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Labs automatisch maken of wijzigen met Azure Resource Manager sjablonen en Power shell

DevTest Labs biedt veel Azure Resource Manager-sjablonen en Power shell-scripts waarmee u snel en automatisch nieuwe Labs kunt maken of bestaande Labs moet wijzigen en vervolgens deze bronnen kan implementeren.

Dit artikel helpt u bij het gebruik van deze sjablonen en scripts om het maken, aanpassen en implementeren van uw Labs te automatiseren. In dit artikel wordt ook beschreven waar u meer informatie kunt vinden over het gebruik van Power shell voor het uitvoeren van enkele veelvoorkomende taken in DevTest Labs.

## <a name="step-1-gather-your-templates-and-scripts"></a>Stap 1: uw sjablonen en scripts verzamelen
U kunt vooraf gemaakte [Azure Resource Manager sjablonen](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) en [Power shell-scripts](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts) vinden in onze open bare [github-opslag plaats](https://github.com/Azure/azure-devtestlab). Gebruik ze als-is of pas ze aan uw eigen behoeften aan en sla ze op in uw eigen [persoonlijke Git-opslag plaats](devtest-lab-add-artifact-repo.md).

## <a name="step-2-modify-your-azure-resource-manager-template"></a>Stap 2: de Azure Resource Manager sjabloon wijzigen
U kunt de stappen volgen in [uw eerste Azure Resource Manager sjabloon maken](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) als u nog nooit een sjabloon hebt gemaakt.

Daarnaast bieden [Aanbevolen procedures voor het maken van Azure Resource Manager sjablonen](../azure-resource-manager/templates/template-best-practices.md) veel richt lijnen en suggesties om u te helpen bij het maken van Azure Resource Manager sjablonen die betrouwbaar en eenvoudig te gebruiken zijn. Normaal gesp roken gebruikt u een variant van een van de benaderingen of voor beelden die worden weer gegeven en wijzigt u uw sjabloon voor uw behoeften.

## <a name="step-3-deploy-resources-with-powershell"></a>Stap 3: resources implementeren met Power shell
Nadat u uw sjablonen en scripts hebt aangepast, volgt u de stappen die nodig zijn om [resources te implementeren met Resource Manager-sjablonen en Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md). Het artikel bevat algemene informatie over het gebruik van Azure PowerShell met Azure Resource Manager sjablonen om uw resources te implementeren in Azure.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>Algemene taken die u kunt uitvoeren in DevTest Labs met behulp van Power shell
Er zijn veel andere veelvoorkomende taken die u kunt automatiseren met behulp van Power shell. In de volgende secties van de documentatie vindt u een overzicht van de stappen die nodig zijn om deze taken uit te voeren.

* [Een aangepaste installatie kopie maken van een VHD-bestand met behulp van Power shell](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [VHD-bestand uploaden naar het opslag account van de Lab met behulp van Power shell](devtest-lab-upload-vhd-using-powershell.md)
* [Een externe gebruiker toevoegen aan een Lab met behulp van Power shell](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [Een aangepaste rol voor een lab maken met behulp van Power shell](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>Volgende stappen
* Meer informatie over het maken van een [persoonlijke Git-opslag plaats](devtest-lab-add-artifact-repo.md) waar u uw aangepaste sjablonen of scripts opslaat.
* Verken de [Azure Resource Manager sjablonen van de Azure Quick Start-sjabloon galerie](https://github.com/Azure/azure-quickstart-templates).
