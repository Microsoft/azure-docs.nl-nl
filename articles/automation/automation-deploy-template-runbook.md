---
title: Een Azure Resource Manager-sjabloon implementeren in een Azure Automation-PowerShell-runbook
description: In dit artikel wordt beschreven hoe u een Azure Resource Manager sjabloon kunt implementeren die is opgeslagen in Azure Storage vanuit een Power shell-runbook.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2020
ms.topic: conceptual
keywords: Power shell, runbook, JSON, Azure Automation
ms.openlocfilehash: d9b443d1840840d3d6202140da235589c73453cc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99051393"
---
# <a name="deploy-an-azure-resource-manager-template-in-a-powershell-runbook"></a>Een Azure Resource Manager-sjabloon implementeren in een Power shell-runbook

U kunt een [Azure Automation Power shell-runbook](./learn/automation-tutorial-runbook-textual-powershell.md) schrijven waarmee een Azure-resource wordt geïmplementeerd met behulp van een [Azure resource management-sjabloon](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). Met de sjablonen kunt u Azure Automation gebruiken om de implementatie van uw Azure-resources te automatiseren. U kunt uw Resource Manager-sjablonen onderhouden op een centrale, veilige locatie, zoals Azure Storage.

In dit artikel maken we een Power shell-runbook dat gebruikmaakt van een resource manager-sjabloon die is opgeslagen in [Azure Storage](../storage/common/storage-introduction.md) voor het implementeren van een nieuw Azure Storage-account.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u [uw voor delen van uw MSDN-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) of [zich aanmelden voor een gratis account](https://azure.microsoft.com/free/).
* [Automation-account](./automation-security-overview.md) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* [Azure Storage account](../storage/common/storage-account-create.md) waarin de Resource Manager-sjabloon moet worden opgeslagen.
* Azure PowerShell geïnstalleerd op een lokale computer. Zie [de Azure PowerShell-module installeren](/powershell/azure/install-az-ps) voor meer informatie over het verkrijgen van Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Het Resource Manager-sjabloon maken

In dit voor beeld gebruiken we een resource manager-sjabloon waarmee een nieuw Azure Storage-account wordt geïmplementeerd.

Kopieer de volgende tekst in een tekst editor:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2018-02-01",
      "location": "[parameters('location')]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

Sla het bestand lokaal **op alsTemplateTest.jsop**.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Sla de Resource Manager-sjabloon op in Azure Storage

Nu gebruiken we Power shell om een Azure Storage bestands share te maken en de **TemplateTest.jsop** bestand te uploaden. Zie [aan de slag met Azure file storage in Windows](../storage/files/storage-dotnet-how-to-use-files.md)voor instructies over het maken van een bestands share en het uploaden van een bestand in de Azure Portal.

Start Power shell op uw lokale machine en voer de volgende opdrachten uit om een bestands share te maken en de Resource Manager-sjabloon te uploaden naar die bestands share.

```powershell
# Log into Azure
Connect-AzAccount

# Get the access key for your storage account
$key = Get-AzStorageAccountKey -ResourceGroupName 'MyAzureAccount' -Name 'MyStorageAccount'

# Create an Azure Storage context using the first access key
$context = New-AzStorageContext -StorageAccountName 'MyStorageAccount' -StorageAccountKey $key[0].value

# Create a file share named 'resource-templates' in your Azure Storage account
$fileShare = New-AzStorageShare -Name 'resource-templates' -Context $context

# Add the TemplateTest.json file to the new file share
# "TemplatePath" is the path where you saved the TemplateTest.json file
$templateFile = 'C:\TemplatePath'
Set-AzStorageFileContent -ShareName $fileShare.Name -Context $context -Source $templateFile
```

## <a name="create-the-powershell-runbook-script"></a>Het Power shell-runbook-script maken

Nu gaan we een Power shell-script maken waarmee de **TemplateTest.jsin** het bestand wordt opgehaald van Azure Storage en de sjabloon implementeren om een nieuw Azure Storage-account te maken.

Plak de volgende tekst in een tekst editor:

```powershell
param (
    [Parameter(Mandatory=$true)]
    [string]
    $ResourceGroupName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountName,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageAccountKey,

    [Parameter(Mandatory=$true)]
    [string]
    $StorageFileName
)

# Authenticate to Azure if running from Azure Automation
$ServicePrincipalConnection = Get-AutomationConnection -Name "AzureRunAsConnection"
Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint | Write-Verbose

#Set the parameter values for the Resource Manager template
$Parameters = @{
    "storageAccountType"="Standard_LRS"
    }

# Create a new context
$Context = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

Get-AzStorageFileContent -ShareName 'resource-templates' -Context $Context -path 'TemplateTest.json' -Destination 'C:\Temp'

$TemplateFile = Join-Path -Path 'C:\Temp' -ChildPath $StorageFileName

# Deploy the storage account
New-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile $TemplateFile -TemplateParameterObject $Parameters 
```

Sla het bestand lokaal op als **DeployTemplate.ps1**.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Importeer en publiceer het runbook in uw Azure Automation-account

Nu gebruiken we Power shell om het runbook te importeren in uw Azure Automation-account en vervolgens het runbook te publiceren. Zie [Runbooks beheren in azure Automation](manage-runbooks.md)voor meer informatie over het importeren en publiceren van een runbook in de Azure Portal.

Als u **DeployTemplate.ps1** wilt importeren in uw Automation-account als een Power shell-runbook, voert u de volgende Power shell-opdrachten uit:

```powershell
# MyPath is the path where you saved DeployTemplate.ps1
# MyResourceGroup is the name of the Azure ResourceGroup that contains your Azure Automation account
# MyAutomationAccount is the name of your Automation account
$importParams = @{
    Path = 'C:\MyPath\DeployTemplate.ps1'
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Type = 'PowerShell'
}
Import-AzAutomationRunbook @importParams

# Publish the runbook
$publishParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
}
Publish-AzAutomationRunbook @publishParams
```

## <a name="start-the-runbook"></a>Het runbook starten

Nu gaan we het runbook starten door de cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) aan te roepen. Zie [starten van een runbook in azure Automation](./start-runbooks.md)voor informatie over het starten van een runbook in de Azure Portal.

Voer de volgende opdrachten uit in de Power shell-console:

```powershell
# Set up the parameters for the runbook
$runbookParams = @{
    ResourceGroupName = 'MyResourceGroup'
    StorageAccountName = 'MyStorageAccount'
    StorageAccountKey = $key[0].Value # We got this key earlier
    StorageFileName = 'TemplateTest.json'
}

# Set up parameters for the Start-AzAutomationRunbook cmdlet
$startParams = @{
    ResourceGroupName = 'MyResourceGroup'
    AutomationAccountName = 'MyAutomationAccount'
    Name = 'DeployTemplate'
    Parameters = $runbookParams
}

# Start the runbook
$job = Start-AzAutomationRunbook @startParams
```

Nadat het runbook is uitgevoerd, kunt u de status ervan controleren door de waarde van de eigenschap van het taak object op te halen `$job.Status` .

Het runbook haalt de Resource Manager-sjabloon op en gebruikt deze om een nieuw Azure Storage-account te implementeren. U ziet dat het nieuwe opslag account is gemaakt door de volgende opdracht uit te voeren:

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>Volgende stappen

* Zie [Azure Resource Manager Overview](../azure-resource-manager/management/overview.md)voor meer informatie over Resource Manager-sjablonen.
* Zie [Inleiding tot Azure Storage](../storage/common/storage-introduction.md)om aan de slag te gaan met Azure Storage.
* Zie [runbooks en modules in azure Automation gebruiken](automation-runbook-gallery.md)om andere nuttige Azure Automation runbooks te vinden.
* Zie [Az.Automation](/powershell/module/az.automation#automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
