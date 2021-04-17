---
title: Azure Monitor for SAP Solutions implementeren met Azure PowerShell
description: Azure Monitor for SAP Solutions implementeren met Azure PowerShell
author: sameeksha91
ms.author: sakhare
ms.date: 09/08/2020
ms.topic: quickstart
ms.service: virtual-machines-sap
ms.devlang: azurepowershell
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 0af2611bada7a9aad206ce7463ef72ec930c89a2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538693"
---
# <a name="quickstart-deploy-azure-monitor-for-sap-solutions-with-azure-powershell"></a>Quickstart: Azure Monitor for SAP Solutions implementeren met Azure PowerShell

In dit artikel wordt beschreven hoe u Azure Monitor for SAP Solutions-resources kunt maken met behulp van de PowerShell-module [Az.HanaOnAzure](/powershell/module/az.hanaonazure/#sap-hana-on-azure).

> [!CAUTION]
> Azure Monitor for SAP Solutions bevindt zich momenteel in de openbare preview-versie. Deze preview-versie wordt geleverd zonder Service Level Agreement. Dit wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="requirements"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

Als u PowerShell lokaal wilt gebruiken, moet u voor dit artikel de Az-module van PowerShell installeren en verbinding maken met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Zie [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie over het installeren van de Az-module van PowerShell. Als u Cloud Shell gebruikt, raadpleegt u [Overzicht van Azure Cloud Shell](../../../cloud-shell/overview.md) voor meer informatie.

> [!IMPORTANT]
> Zo lang de PowerShell-module **Az.HanaOnAzure** in de preview-fase is, moet u deze afzonderlijk installeren met behulp van de cmdlet `Install-Module`. Zodra de PowerShell-module algemeen beschikbaar komt, wordt het onderdeel van toekomstige releases van Az PowerShell en is de module systeemeigen beschikbaar vanuit Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.HanaOnAzure
```

Als u meerdere Azure-abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer een specifiek abonnement met de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resourcegroep](../../../azure-resource-manager/management/overview.md) met de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd.

In het volgende voorbeeld wordt een resourcegroep gemaakt met de opgegeven naam en op de opgegeven locatie.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="sap-monitor"></a>SAP-monitor

Als u een SAP-monitor wilt maken, gebruikt u de cmdlet [New-AzSapMonitor](/powershell/module/az.hanaonazure/new-azsapmonitor). In het volgende voorbeeld wordt een SAP-monitor gemaakt voor het opgegeven abonnement, de resourcegroep en de resourcenaam.

```azurepowershell-interactive
$Workspace = New-AzOperationalInsightsWorkspace -ResourceGroupName myResourceGroup -Name sapmonitor-test -Location westus2 -Sku Standard

$WorkspaceKey = Get-AzOperationalInsightsWorkspaceSharedKey -ResourceGroupName myResourceGroup -Name sapmonitor-test

$SapMonitorParams = @{
  Name = 'ps-sapmonitor-t01'
  ResourceGroupName = 'myResourceGroup'
  Location = 'westus2'
  EnableCustomerAnalytic = $true
  MonitorSubnet = '/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/vnet-sap/subnets/mysubnet'
  LogAnalyticsWorkspaceSharedKey = $WorkspaceKey.PrimarySharedKey
  LogAnalyticsWorkspaceId = $Workspace.CustomerId
  LogAnalyticsWorkspaceResourceId = $Workspace.ResourceId
}
New-AzSapMonitor @SapMonitorParams
```

Als u de eigenschappen van een SAP-monitor wilt ophalen, gebruikt u de cmdlet [Get-AzSapMonitor](/powershell/module/az.hanaonazure/get-azsapmonitor). In het volgende voorbeeld worden de eigenschappen van een SAP-monitor opgehaald voor het abonnement, de resourcegroep en de resourcenaam die zijn opgegeven.

```azurepowershell-interactive
Get-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-spamonitor-t01
```

## <a name="provider-instance"></a>Exemplaar provider

Als u een providerexemplaar wilt maken, gebruikt u de cmdlet [New-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/new-azsapmonitorproviderinstance). In het volgende voorbeeld wordt een providerexemplaar gemaakt voor het opgegeven abonnement, de resourcegroep en de resourcenaam.

```azurepowershell-interactive
$SapProviderParams = @{
  ResourceGroupName = 'myResourceGroup'
  Name = 'ps-sapmonitorins-t01'
  SapMonitorName = 'yemingmonitor'
  ProviderType = 'SapHana'
  HanaHostname = 'hdb1-0'
  HanaDatabaseName = 'SYSTEMDB'
  HanaDatabaseSqlPort = '30015'
  HanaDatabaseUsername = 'SYSTEM'
  HanaDatabasePassword = (ConvertTo-SecureString 'Manager1' -AsPlainText -Force)
}
New-AzSapMonitorProviderInstance @SapProviderParams
```

Als u de eigenschappen van een providerexemplaar wilt ophalen, gebruikt u de cmdlet [Get-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/get-azsapmonitorproviderinstance). In het volgende voorbeeld worden de eigenschappen van een providerexemplaar opgehaald voor het abonnement, de resourcegroep, de SapMonitor-naam en de resourcenaam die zijn opgegeven.

```azurepowershell-interactive
Get-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01
```

## <a name="clean-up-resources"></a>Resources opschonen

Als de resources die in dit artikel zijn gemaakt, niet meer nodig zijn, kunt u ze verwijderen door het volgende voorbeeld uit te voeren.

### <a name="delete-the-provider-instance"></a>Het providerexemplaar verwijderen

Als u een providerexemplaar wilt verwijderen, gebruikt u de cmdlet [Remove-AzSapMonitorProviderInstance](/powershell/module/az.hanaonazure/remove-azsapmonitorproviderinstance). In het volgende voorbeeld wordt een providerexemplaar verwijderd voor het abonnement, de resourcegroep, de SapMonitor-naam en de resourcenaam die zijn opgegeven.

```azurepowershell-interactive
Remove-AzSapMonitorProviderInstance -ResourceGroupName myResourceGroup -SapMonitorName ps-spamonitor-t01 -Name ps-sapmonitorins-t02
```

### <a name="delete-the-sap-monitor"></a>De SAP-monitor verwijderen

Als u een SAP-monitor wilt verwijderen, gebruikt u de cmdlet [Remove-AzSapMonitor](/powershell/module/az.hanaonazure/remove-azsapmonitor). In het volgende voorbeeld wordt een SAP-monitor verwijderd voor het abonnement, de resourcegroep en de monitornaam die zijn opgegeven.

```azurepowershell
Remove-AzSapMonitor -ResourceGroupName myResourceGroup -Name ps-sapmonitor-t02
```

### <a name="delete-the-resource-group"></a>De resourcegroep verwijderen

> [!CAUTION]
> In het volgende voorbeeld worden de opgegeven resourcegroep en alle resources erin verwijderd.
> Als resources buiten het bereik van dit artikel in de opgegeven resourcegroep bestaan, worden ze ook verwijderd.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure Monitor for SAP Solutions](azure-monitor-overview.md).
