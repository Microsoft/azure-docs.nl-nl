---
title: Azure lente-Cloud implementeren met Azure PowerShell
description: Azure lente-Cloud implementeren met Azure PowerShell
author: bmitchell287
ms.author: brendm
ms.topic: conceptual
ms.service: spring-cloud
ms.devlang: azurepowershell
ms.date: 11/16/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3cb320a37818084f2fbcad22a3cc992655b19c3d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104878009"
---
# <a name="deploy-azure-spring-cloud-with-azure-powershell"></a>Azure Spring Cloud implementeren met Azure PowerShell

In dit artikel wordt beschreven hoe u een exemplaar van Azure lente-Cloud kunt maken met behulp van de Power shell-module [AZ. SpringCloud](/powershell/module/Az.SpringCloud) .

## <a name="requirements"></a>Vereisten

* Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [azure-powershell-requirements-no-header.md](../../includes/azure-powershell-requirements-no-header.md)]

  > [!IMPORTANT]
  > Hoewel de Power shell-module **AZ. SpringCloud** in preview is, moet u deze afzonderlijk installeren met behulp van de `Install-Module` cmdlet. Nadat de PowerShell-module algemeen beschikbaar is geworden, wordt deze onderdeel van toekomstige releases van de Az PowerShell-module en is deze standaard beschikbaar vanuit Azure Cloud Shell.

  ```azurepowershell-interactive
  Install-Module -Name Az.SpringCloud
  ```

* Als u meerdere Azure-abonnementen hebt, kiest u het juiste abonnement waarin de resource moet worden gefactureerd. Selecteer een specifiek abonnement met de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext).

  ```azurepowershell-interactive
  Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
  ```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) met de cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd.

In het volgende voorbeeld wordt een resourcegroep gemaakt met de opgegeven naam en op de opgegeven locatie.

```azurepowershell-interactive
New-AzResourceGroup -Name <resource group name> -Location eastus
```

## <a name="provision-a-new-instance-of-azure-spring-cloud"></a>Een nieuw exemplaar van Azure lente-Cloud inrichten

Als u een nieuw exemplaar van Azure lente Cloud wilt maken, gebruikt u de cmdlet [New-AzSpringCloud](/powershell/module/az.springcloud/new-azspringcloud) . In het volgende voor beeld wordt een Azure lente-Cloud service met de opgegeven naam gemaakt in de eerder gemaakte resource groep.

```azurepowershell-interactive
New-AzSpringCloud -ResourceGroupName <resource group name> -name <service instance name> -Location eastus
```

## <a name="create-a-new-azure-spring-cloud-app"></a>Een nieuwe Azure lente-Cloud-app maken

Als u een nieuwe app wilt maken, gebruikt u de cmdlet [New-AzSpringCloudApp](/powershell/module/az.springcloud/new-azspringcloudapp) . In het volgende voor beeld wordt een Azure lente-Cloud-app met de naam gemaakt `gateway` .

```azurepowershell-interactive
New-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="create-a-new-azure-spring-cloud-deployment"></a>Een nieuwe Azure lente-Cloud implementatie maken

Als u een nieuwe implementatie wilt maken, gebruikt u de cmdlet [New-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/new-azspringcloudappdeployment) . In het volgende voor beeld wordt een Azure lente-Cloud implementatie gemaakt met `default` de naam voor de `gateway` app.

```azurepowershell-interactive
New-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -Name <service instance name> -AppName gateway -DeploymentName default
```

## <a name="get-an-azure-spring-cloud-service"></a>Een Azure lente-Cloud service ophalen

Als u een Azure lente-Cloud service en de bijbehorende eigenschappen wilt ophalen, gebruikt u de cmdlet [Get-AzSpringCloud](/powershell/module/az.springcloud/get-azspringcloud) . In het volgende voor beeld wordt informatie opgehaald over de opgegeven Azure lente-Cloud service.

```azurepowershell-interactive
Get-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

## <a name="get-an-azure-spring-cloud-app"></a>Een Azure lente-Cloud-app ophalen

Als u een Azure lente-Cloud-app en de bijbehorende eigenschappen wilt ophalen, gebruikt u de cmdlet [Get-AzSpringCloudApp](/powershell/module/az.springcloud/get-azspringcloudapp) . In het volgende voor beeld wordt informatie opgehaald over de `gateway` lente-Cloud-app.

```azurepowershell-interactive
Get-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

## <a name="get-an-azure-spring-cloud-deployment"></a>Een Azure lente-Cloud implementatie ophalen

Als u een Azure lente-Cloud implementatie en de bijbehorende eigenschappen wilt ophalen, gebruikt u de cmdlet [Get-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/get-azspringcloudappdeployment) . In het volgende voor beeld wordt informatie opgehaald over de `default` lente-Cloud implementatie.

```azurepowershell-interactive
Get-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

## <a name="clean-up-resources"></a>Resources opschonen

Als de resources die in dit artikel zijn gemaakt, niet meer nodig zijn, kunt u ze verwijderen door het volgende voorbeeld uit te voeren.

### <a name="delete-an-azure-spring-cloud-deployment"></a>Een Azure lente-Cloud implementatie verwijderen

Als u een Azure lente-Cloud implementatie wilt verwijderen, gebruikt u de cmdlet [Remove-AzSpringCloudAppDeployment](/powershell/module/az.springcloud/remove-azspringcloudappdeployment) . In het volgende voor beeld wordt een Azure veer Cloud-app-implementatie `default` met de naam voor de opgegeven service en app verwijderd.

```azurepowershell-interactive
Remove-AzSpringCloudAppDeployment -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway -DeploymentName default
```

### <a name="delete-an-azure-spring-cloud-app"></a>Een Azure lente-Cloud-app verwijderen

Als u een lente-Cloud-app wilt verwijderen, gebruikt u de cmdlet [Remove-AzSpringCloudApp](/powershell/module/Az.SpringCloud/remove-azspringcloudapp) . In het volgende voor beeld wordt de `gateway` app verwijderd uit de opgegeven service en resource groep.

```azurepowershell
Remove-AzSpringCloudApp -ResourceGroupName <resource group name> -ServiceName <service instance name> -AppName gateway
```

### <a name="delete-an-azure-spring-cloud-service"></a>Een Azure lente-Cloud service verwijderen

Als u een Azure lente-Cloud service wilt verwijderen, gebruikt u de cmdlet [Remove-AzSpringCloud](/powershell/module/Az.SpringCloud/remove-azspringcloud) . In het volgende voor beeld wordt de opgegeven Azure lente-Cloud service verwijderd.

```azurepowershell
Remove-AzSpringCloud -ResourceGroupName <resource group name> -ServiceName <service instance name>
```

### <a name="delete-the-resource-group"></a>De resourcegroep verwijderen

> [!CAUTION]
> In het volgende voorbeeld worden de opgegeven resourcegroep en alle resources erin verwijderd.
> Als resources buiten het bereik van dit artikel in de opgegeven resourcegroep bestaan, worden ze ook verwijderd.

```azurepowershell-interactive
Remove-AzResourceGroup -Name <resource group name>
```

## <a name="next-steps"></a>Volgende stappen

[Azure lente-bronnen voor ontwikkel aars](spring-cloud-resources.md)in de Cloud.
