---
title: Een Cloud service (klassieke) container maken met Power shell | Microsoft Docs
description: In dit artikel wordt uitgelegd hoe u een Cloud service container maakt met Power shell. De container fungeert als host voor web-en werk rollen.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: a8f06ce08c0df4cc86afe6fbbe7eb12fd866e61c
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743267"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-classic-container"></a>Een Azure PowerShell-opdracht gebruiken om een lege Cloud service (klassieke) container te maken

> [!IMPORTANT]
> [Azure Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md) is een nieuw implementatie model op basis van Azure Resource Manager voor het Azure Cloud Services-product.Met deze wijziging worden Azure-Cloud Services die worden uitgevoerd op het Azure Service Manager gebaseerde implementatie model, de naam van Cloud Services (klassiek) gewijzigd en moeten alle nieuwe implementaties [Cloud Services (uitgebreide ondersteuning)](../cloud-services-extended-support/overview.md)gebruiken.

In dit artikel wordt uitgelegd hoe u snel een Cloud Services container maakt met behulp van Azure PowerShell-cmdlets. Volg de onderstaande stappen:

1. Installeer de Microsoft Azure PowerShell cmdlet van de pagina [Azure PowerShell down loads](https://aka.ms/webpi-azps) .
2. Open de Power shell-opdracht prompt.
3. Gebruik de [add-AzureAccount](/powershell/module/servicemanagement/azure.service/add-azureaccount?view=azuresmps-4.0.0&preserve-view=true) om u aan te melden.

   > [!NOTE]
   > Zie [Azure PowerShell installeren en configureren](/powershell/azure/)voor meer instructies voor het installeren van de Azure PowerShell-cmdlet en het maken van een verbinding met uw Azure-abonnement.
   >
   >
4. Gebruik de cmdlet **New-Service** om een lege Azure Cloud service-container te maken.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Volg dit voor beeld om de cmdlet aan te roepen:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Voer de volgende handelingen uit voor meer informatie over het maken van de Azure-Cloud service:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Volgende stappen

* Raadpleeg de opdrachten [Get-service](/powershell/module/servicemanagement/azure.service/Get-AzureService?view=azuresmps-4.0.0&preserve-view=true), [Remove-service](/powershell/module/servicemanagement/azure.service/Remove-AzureService?view=azuresmps-4.0.0&preserve-view=true)en [set-service](/powershell/module/servicemanagement/azure.service/set-azureservice?view=azuresmps-4.0.0&preserve-view=true) om de Cloud service-implementatie te beheren. U kunt ook verwijzen naar [Cloud Services configureren](cloud-services-how-to-configure-portal.md) voor meer informatie.
* Als u uw Cloud service project wilt publiceren naar Azure, raadpleegt u het  **PublishCloudService.ps1** code voorbeeld van [gearchiveerde Cloud Services-opslag plaats](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).