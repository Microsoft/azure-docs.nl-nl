---
title: Een Azure Resource Manager-sjabloon implementeren in een Azure Automation-PowerShell-runbook
description: In dit artikel wordt beschreven hoe u een Azure Resource Manager implementeert die is opgeslagen in Azure Storage een PowerShell-runbook.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
keywords: powershell, runbook, json, azure automation
ms.openlocfilehash: 20dbf9f9bbf97ed0c24ea3a525c56c7cde2db428
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834836"
---
# <a name="deploy-an-azure-resource-manager-template-in-a-powershell-runbook"></a>Een sjabloon Azure Resource Manager powershell implementeren in een PowerShell-runbook

U kunt een [powershell Azure Automation schrijven die](./learn/automation-tutorial-runbook-textual-powershell.md) een Azure-resource implementeert met behulp van een [Azure Resource Management-sjabloon.](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) Met de sjablonen kunt u Azure Automation om de implementatie van uw Azure-resources te automatiseren. U kunt uw Resource Manager sjablonen onderhouden op een centrale, veilige locatie, zoals Azure Storage.

In dit artikel maken we een PowerShell-runbook dat gebruikmaakt van een Resource Manager-sjabloon die is opgeslagen in [Azure Storage](../storage/common/storage-introduction.md) om een nieuw Azure Storage implementeren.

## <a name="prerequisites"></a>Vereisten

* Azure-abonnement. Als u nog geen abonnement hebt, kunt u uw voordelen als [MSDN-abonnee](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) activeren of u [aanmelden voor een gratis account.](https://azure.microsoft.com/free/)
* [Automation-account](./automation-security-overview.md) om het runbook te bevatten en te verifiëren voor Azure-resources. Dit account moet machtigingen hebben om de virtuele machine te starten en stoppen.
* [Azure Storage voor het](../storage/common/storage-account-create.md) opslaan van de Resource Manager sjabloon.
* Azure PowerShell geïnstalleerd op een lokale computer. Zie Install the Azure PowerShell Module (De [Azure PowerShell-module](/powershell/azure/install-az-ps) installeren) voor informatie over het verkrijgen van Azure PowerShell.

## <a name="create-the-resource-manager-template"></a>Het Resource Manager-sjabloon maken

In dit voorbeeld gebruiken we een Resource Manager die een nieuw Azure Storage implementeert.

Kopieer in een teksteditor de volgende tekst:

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

Sla het bestand lokaal op **TemplateTest.jsop**.

## <a name="save-the-resource-manager-template-in-azure-storage"></a>Sla de sjabloon Resource Manager op in Azure Storage

Nu gebruiken we PowerShell om een Azure Storage te maken en deTemplateTest.js **in het bestand te** uploaden. Zie Aan de slag met [Azure File Storage](../storage/files/storage-dotnet-how-to-use-files.md)in Windows voor instructies over het maken van een bestands share en het uploaden van een bestand in de Azure Portal.

Start PowerShell op uw lokale computer en voer de volgende opdrachten uit om een bestands share te maken en de sjabloon Resource Manager uploaden naar die bestands share.

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

## <a name="create-the-powershell-runbook-script"></a>Het PowerShell-runbookscript maken

Nu maken we een PowerShell-script dat deTemplateTest.jsin het bestand op haalt uit Azure Storage en implementeren we de sjabloon om een nieuw Azure Storage maken. 

Plak de volgende tekst in een teksteditor:

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

Sla het bestand lokaal op **alsDeployTemplate.ps1**.

## <a name="import-and-publish-the-runbook-into-your-azure-automation-account"></a>Het runbook importeren en publiceren in uw Azure Automation account

Nu gebruiken we PowerShell om het runbook te importeren in Azure Automation account en vervolgens het runbook te publiceren. Zie Runbooks beheren in Azure Automation voor meer informatie over het importeren en publiceren van een [runbook in Azure Portal.](manage-runbooks.md)

Voer de **DeployTemplate.ps1** PowerShell-opdrachten uit om deze als Een PowerShell-runbook in uw Automation-account te importeren:

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

Nu starten we het runbook door de cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) aan te roepen. Zie Starting a runbook in Azure Portal (Een [runbook](./start-runbooks.md)starten in Azure Automation) voor meer informatie over het starten van een runbook in Azure Portal .

Voer de volgende opdrachten uit in de PowerShell-console:

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

Nadat het runbook is uitgevoerd, kunt u de status ervan controleren door de eigenschapswaarde van het taakobject op te `$job.Status` haalt.

Het runbook haalt de sjabloon Resource Manager en gebruikt deze om een nieuw Azure Storage implementeren. U kunt zien dat het nieuwe opslagaccount is gemaakt door de volgende opdracht uit te voeren:

```powershell
Get-AzStorageAccount
```

## <a name="next-steps"></a>Volgende stappen

* Zie overzicht van Resource Manager voor [meer informatie Azure Resource Manager sjablonen.](../azure-resource-manager/management/overview.md)
* Zie Inleiding tot Azure Storage om aan de slag [te Azure Storage.](../storage/common/storage-introduction.md)
* Zie Runbooks en modules gebruiken in Azure Automation voor andere nuttige [runbooks Azure Automation.](automation-runbook-gallery.md)
* Zie [Az.Automation](/powershell/module/az.automation#automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
