---
title: Een Azure Portal-dashboard maken met PowerShell
description: Meer informatie over het maken van een dashboard in Azure Portal met behulp van Azure PowerShell.
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.date: 07/24/2020
ms.openlocfilehash: 02e243a7296555d73427f8e31c4abdf9c3e56735
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745737"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-powershell"></a>Quickstart: Een Azure Portal-dashboard maken met PowerShell

Een dashboard in de Azure-portal is een gerichte en georganiseerde weergave van uw cloudresources. Dit artikel richt zich op het gebruik van de PowerShell-module Az.Portal voor het maken van een dashboard.
Het dashboard toont de prestaties van een virtuele machine (VM), evenals een aantal statische gegevens en links.

## <a name="requirements"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

Als u PowerShell lokaal wilt gebruiken, moet u voor dit artikel de AZ Powershell-module installeren en verbinding maken met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Zie [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie over het installeren van de AZ PowerShell-module.

> [!IMPORTANT]
> Zo lang de PowerShell-module **Az.Portal** in preview is, moet u deze afzonderlijk van de Az-module van PowerShell installeren met de cmdlet `Install-Module`. Zodra de PowerShell-module algemeen beschikbaar komt, wordt het onderdeel van toekomstige releases van Az PowerShell en is de module systeemeigen beschikbaar vanuit Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.Portal
```

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

## <a name="choose-a-specific-azure-subscription"></a>Een specifiek Azure-abonnement kiezen

Als u meerdere Azure-abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer een specifiek abonnement met de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="define-variables"></a>Variabelen definiëren

U gebruikt herhaaldelijk verschillende gegevens. Maak variabelen om de gegevens op te slaan.

```azurepowershell-interactive
# Name of resource group used throughout this article
$resourceGroupName = 'myResourceGroup'

# Azure region
$location = 'centralus'

# Dashboard Title
$dashboardTitle = 'Simple VM Dashboard'

# Dashboard Name
$dashboardName = $dashboardTitle -replace '\s'

# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id

# Name of test VM
$vmName = 'SimpleWinVM'
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) met de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd.

In het volgende voorbeeld wordt een resourcegroep gemaakt op basis van de naam in de variabele `$resourceGroupName` in de regio die is opgegeven in de variabele `$location`.

```azurepowershell-interactive
New-AzResourceGroup -Name $resourceGroupName -Location $location
```

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Het dashboard dat u in het volgende deel van deze quickstart maakt, vereist een bestaande VM. Volg deze stappen om een virtuele machine te maken.

Sla referenties voor de virtuele machine op in een variabele. Het wachtwoord moet complex zijn. Dit is een nieuwe gebruikersnaam en nieuw wachtwoord. Het is bijvoorbeeld niet het account dat u gebruikt om u aan te melden bij Azure. Zie [gebruikersnaamvereisten](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) en [wachtwoordvereisten](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm) voor meer informatie.

```azurepowershell-interactive
$Cred = Get-Credential
```

Maak de VM.

```azurepowershell-interactive
$AzVmParams = @{
  ResourceGroupName = $resourceGroupName
  Name = $vmName
  Location = $location
  Credential = $Cred
}
New-AzVm @AzVmParams
```

De VM-implementatie wordt nu gestart en duurt doorgaans enkele minuten. Nadat de implementatie is voltooid, gaat u verder met de volgende sectie.

## <a name="download-the-dashboard-template"></a>De dashboardsjabloon downloaden

Omdat Azure-dashboards resources zijn, kunnen ze als JSON worden weergegeven. Met de volgende code wordt een JSON-weergave van een voorbeelddashboard gedownload. Zie [De structuur van Azure-dashboards](./azure-portal-dashboards-structure.md) voor meer informatie.

```azurepowershell-interactive
$myPortalDashboardTemplateUrl = 'https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json'

$myPortalDashboardTemplatePath = "$env:TEMP\portal-dashboard-template-testvm.json"

Invoke-WebRequest -Uri $myPortalDashboardTemplateUrl -OutFile $myPortalDashboardTemplatePath -UseBasicParsing
```

## <a name="customize-the-template"></a>De sjabloon aanpassen

Voer de volgende code uit om de gedownloade sjabloon aan te passen.

```azurepowershell
$Content = Get-Content -Path $myPortalDashboardTemplatePath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $resourceGroupName
$Content = $Content -replace '<vmName>', $vmName
$Content = $Content -replace '<dashboardTitle>', $dashboardTitle
$Content = $Content -replace '<location>', $location
$Content | Out-File -FilePath $myPortalDashboardTemplatePath -Force
```

Zie [Referentie voor Microsoft Portal-dashboardsjablonen](/azure/templates/microsoft.portal/dashboards) voor meer informatie.

## <a name="deploy-the-dashboard-template"></a>De dashboardsjabloon implementeren

U kunt de `New-AzPortalDashboard`-cmdlet die deel uitmaakt van de Az.Portal-module gebruiken om de sjabloon rechtstreeks vanuit PowerShell te implementeren.

```azurepowershell
$DashboardParams = @{
  DashboardPath = $myPortalDashboardTemplatePath
  ResourceGroupName = $resourceGroupName
  DashboardName = $dashboardName
}
New-AzPortalDashboard @DashboardParams
```

## <a name="review-the-deployed-resources"></a>De geïmplementeerde resources bekijken

Controleer of het dashboard is gemaakt.

```azurepowershell
Get-AzPortalDashboard -Name $dashboardName -ResourceGroupName $resourceGroupName
```

Controleer of u in Azure Portal gegevens over de VM kunt zien.

1. In de Azure-portal selecteert u **Dashboard**.

   ![Navigatie in de Azure-portal naar dashboard](media/quickstart-portal-dashboard-powershell/navigate-to-dashboards.png)

1. Op de dashboardpagina selecteert u **Eenvoudig VM-dashboard**.

   ![Navigeren naar een eenvoudig VM-dashboard](media/quickstart-portal-dashboard-powershell/select-simple-vm-dashboard.png)

1. Bekijk het dashboard. U kunt zien dat een deel van de inhoud statisch is, maar er zijn ook grafieken waarin de prestaties van de virtuele machine worden weergegeven.

   ![Eenvoudig VM-dashboard bekijken](media/quickstart-portal-dashboard-powershell/review-simple-vm-dashboard.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u de virtuele machine en het bijbehorende dashboard wilt verwijderen, verwijdert u de resourcegroep waarin deze zich bevinden.

> [!CAUTION]
> In het volgende voorbeeld worden de opgegeven resourcegroep en alle resources erin verwijderd.
> Als resources buiten het bereik van dit artikel in de opgegeven resourcegroep bestaan, worden ze ook verwijderd.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $resourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over de cmdlets in de PowerShell-module Az.Portal:

> [!div class="nextstepaction"]
> [Microsoft Azure PowerShell: Portal-dashboard-cmdlets](/powershell/module/Az.Portal/)