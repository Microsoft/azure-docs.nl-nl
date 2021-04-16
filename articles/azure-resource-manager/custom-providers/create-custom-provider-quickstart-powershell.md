---
title: Een aangepaste Azure-resourceprovider maken met Azure PowerShell
description: Beschrijft hoe u een aangepaste Azure-resourceprovider kunt maken met Azure PowerShell
author: MSEvanhi
ms.author: evanhi
ms.date: 09/22/2020
ms.topic: quickstart
ms.devlang: azurepowershell
ms.custom:
- devx-track-azurepowershell
- mode-api
ms.openlocfilehash: 9c19eb41210b6fba1935a0d158c8240375f4f8f5
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533920"
---
# <a name="quickstart-create-an-azure-custom-resource-provider-with-azure-powershell"></a>Quickstart: Een aangepaste Azure-resourceprovider maken met Azure PowerShell

In deze quickstart leert u hoe u uw eigen aangepaste Azure-resourceprovider kunt maken met behulp van de PowerShell-module [Az.CustomProviders](/powershell/module/az.customproviders).

> [!CAUTION]
> Azure Custom Providers wordt momenteel in openbare preview uitgevoerd. Deze preview-versie wordt geleverd zonder Service Level Agreement. Dit wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="requirements"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

Als u PowerShell lokaal wilt gebruiken, moet u voor dit artikel de Az-module van PowerShell installeren en verbinding maken met uw Azure-account met behulp van de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount). Zie [Azure PowerShell installeren](/powershell/azure/install-az-ps) voor meer informatie over het installeren van de Az-module van PowerShell. Als u Cloud Shell gebruikt, raadpleegt u [Overzicht van Azure Cloud Shell](../../cloud-shell/overview.md) voor meer informatie.

> [!IMPORTANT]
> Zo lang de PowerShell-module **Az.CustomProviders** in de preview-fase is, moet u deze afzonderlijk installeren met behulp van de cmdlet `Install-Module`. Zodra de PowerShell-module algemeen beschikbaar komt, wordt het onderdeel van toekomstige releases van Az PowerShell en is de module systeemeigen beschikbaar vanuit Azure Cloud Shell.

```azurepowershell-interactive
Install-Module -Name Az.CustomProviders
```

Als u meerdere Azure-abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer een specifiek abonnement met de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext).

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resourcegroep](../../azure-resource-manager/management/overview.md) met de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd.

In het volgende voorbeeld wordt een resourcegroep gemaakt met de opgegeven naam en op de opgegeven locatie.

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroup -Location westus2
```

## <a name="create-a-custom-resource-provider"></a>Een aangepaste resourceprovider maken

Als u een aangepaste resourceprovider wilt maken of bijwerken, gebruikt u de cmdlet [New-AzCustomProvider](/powershell/module/az.customproviders/new-azcustomprovider), zoals te zien in het volgende voorbeeld.

```azurepowershell-interactive
New-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type -Location westus2 -ResourceType @{Name='CustomRoute1'; Endpoint='https://www.contoso.com/'}
```

## <a name="get-the-custom-resource-provider-manifest"></a>Het manifest van aangepaste resourceproviders ophalen

Als u informatie wilt ophalen over het manifest van de aangepaste resourceprovider, kunt u de cmdlet [Get-AzCustomProvider](/powershell/module/az.customproviders/get-azcustomprovider) gebruiken, zoals te zien is in het volgende voorbeeld.

```azurepowershell-interactive
Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type | Format-List
```

## <a name="create-an-association"></a>Een koppeling maken

Als u een koppeling wilt maken of bijwerken, gebruikt u de cmdlet [New-AzCustomProviderAssociation](/powershell/module/az.customproviders/new-azcustomproviderassociation), zoals te zien in het volgende voorbeeld.

```azurepowershell-interactive
$provider = Get-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
New-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc -TargetResourceId $provider.Id
```

## <a name="get-an-association"></a>Een koppeling ophalen

Als u informatie over een koppeling wilt ophalen, gebruikt u de cmdlet [Get-AzCustomProviderAssociation](/powershell/module/az.customproviders/get-azcustomproviderassociation), zoals te zien in het volgende voorbeeld.

```azurepowershell-interactive
Get-AzCustomProviderAssociation -Scope $resourceId -Name MyAssoc
```

## <a name="clean-up-resources"></a>Resources opschonen

Als de resources die in dit artikel zijn gemaakt, niet meer nodig zijn, kunt u ze verwijderen door het volgende voorbeeld uit te voeren.

### <a name="delete-an-association"></a>Een koppeling verwijderen

Als u een koppeling wilt verwijderen, gebruikt u de cmdlet [Remove-AzCustomProviderAssociation](/powershell/module/az.customproviders/remove-azcustomproviderassociation). In het volgende voorbeeld wordt een koppeling verwijderd.

```azurepowershell
Remove-AzCustomProviderAssociation -Scope $id -Name Namespace.Type
```

### <a name="delete-a-custom-resource-provider"></a>Een aangepaste resourceprovider verwijderen

Als u een aangepaste resourceprovider wilt verwijderen, gebruikt u de cmdlet [Remove-AzCustomProvider](/powershell/module/az.customproviders/remove-azcustomprovider). In het volgende voorbeeld wordt een aangepaste resourceprovider verwijderd.

```azurepowershell-interactive
Remove-AzCustomProvider -ResourceGroupName myResourceGroup -Name Namespace.Type
```

### <a name="delete-the-resource-group"></a>De resourcegroep verwijderen

> [!CAUTION]
> In het volgende voorbeeld worden de opgegeven resourcegroep en alle resources erin verwijderd.
> Als resources buiten het bereik van dit artikel in de opgegeven resourcegroep bestaan, worden ze ook verwijderd.

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Aangepaste Azure-resourceproviders](overview.md).
