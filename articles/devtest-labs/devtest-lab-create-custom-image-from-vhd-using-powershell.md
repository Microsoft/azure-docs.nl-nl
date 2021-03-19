---
title: Een aangepaste installatie kopie maken van een VHD-bestand met behulp van Azure PowerShell
description: Het maken van een aangepaste installatie kopie in Azure DevTest Labs automatiseren vanuit een VHD-bestand met behulp van Power shell
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 4b0712fdbec1ce23ad9e09d972e425cb7941107b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87288986"
---
# <a name="create-a-custom-image-from-a-vhd-file-using-powershell"></a>Een aangepaste installatie kopie maken van een VHD-bestand met behulp van Power shell

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="step-by-step-instructions"></a>Stapsgewijze instructies

De volgende stappen helpen u bij het maken van een aangepaste installatie kopie vanuit een VHD-bestand met behulp van Power shell:

1. Meld u bij een Power shell-prompt aan bij uw Azure-account met de volgende aanroep van de cmdlet **Connect-AzAccount** .

    ```powershell
    Connect-AzAccount
    ```

1.  Selecteer het gewenste Azure-abonnement door de cmdlet **Select-AzSubscription** aan te roepen. Vervang de volgende tijdelijke aanduiding voor de variabele **$subscriptionId** door een geldige Azure-abonnements-id.

    ```powershell
    $subscriptionId = '<Specify your subscription ID here>'
    Select-AzSubscription -SubscriptionId $subscriptionId
    ```

1.  Haal het lab-object op door de cmdlet **Get-AzResource** aan te roepen. Vervang de volgende tijdelijke aanduidingen voor de variabelen **$labRg** en **$labName** door de juiste waarden voor uw omgeving.

    ```powershell
    $labRg = '<Specify your lab resource group name here>'
    $labName = '<Specify your lab name here>'
    $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
    ```

1.  Vervang de volgende tijdelijke aanduiding voor de variabele **$vhdUri** door de URI naar uw GEÜPLOADe VHD-bestand. U kunt de URI van het VHD-bestand ophalen van de Blade blob van het opslag account in de Azure Portal.

    ```powershell
    $vhdUri = '<Specify the VHD URI here>'
    ```

1.  Maak de aangepaste installatie kopie met behulp van de cmdlet **New-AzResourceGroupDeployment** . Vervang de volgende tijdelijke aanduidingen voor de variabelen **$customImageName** en **$customImageDescription** naar zinvolle namen voor uw omgeving.

    ```powershell
    $customImageName = '<Specify the custom image name>'
    $customImageDescription = '<Specify the custom image description>'

    $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

    New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
    ```

## <a name="powershell-script-to-create-a-custom-image-from-a-vhd-file"></a>Power shell-script voor het maken van een aangepaste installatie kopie van een VHD-bestand

Het volgende Power shell-script kan worden gebruikt om een aangepaste installatie kopie te maken op basis van een VHD-bestand. Vervang de tijdelijke aanduidingen (beginnen en eindigen met punt haken) door de juiste waarden voor uw behoeften.

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<Specify your subscription ID here>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<Specify your lab resource group name here>'
$labName = '<Specify your lab name here>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Set the URI of the VHD file.
$vhdUri = '<Specify the VHD URI here>'

# Set the custom image name and description values.
$customImageName = '<Specify the custom image name>'
$customImageDescription = '<Specify the custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="related-blog-posts"></a>Gerelateerde blog berichten

- [Aangepaste afbeeldingen of formules?](./devtest-lab-faq.md#blog-post)
- [Aangepaste installatie kopieën kopiëren tussen Azure DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)

## <a name="next-steps"></a>Volgende stappen

- [Een virtuele machine toevoegen aan uw Lab](devtest-lab-add-vm.md)
