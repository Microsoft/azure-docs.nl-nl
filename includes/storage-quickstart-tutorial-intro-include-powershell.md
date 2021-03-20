---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 1f8f8d314a8bb37a08b3696f597b395a8a4beb8e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67176259"
---
## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij uw Azure-abonnement met de opdracht `Connect-AzAccount` en volg de instructies op het scherm.

```powershell
Connect-AzAccount
```

Als u niet weet welke locatie u kunt gebruiken, kunt u een lijst met de beschikbare locaties weergeven. Geef de lijst met locaties weer met behulp van het volgende codevoorbeeld, en zoek de locatie die u wilt gebruiken. In dit voorbeeld wordt **eastus** gebruikt. Sla de locatie op in een variabele en gebruik de variabele zodat u de locatie op één plek kunt wijzigen.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een Azure-resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

```powershell
$resourceGroup = "myResourceGroup"
New-AzResourceGroup -Name $resourceGroup -Location $location
```

## <a name="create-a-storage-account"></a>Create a storage account

Maak met behulp van [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) een standaardopslagaccount voor algemeen gebruik met LRS-replicatie. Haal vervolgens de opslagaccountcontext op waarin het te gebruiken opslagaccount wordt gedefinieerd. Verwijs bij het werken met een opslagaccount naar de context in plaats van herhaaldelijk de referenties door te geven. Gebruik het volgende voorbeeld om een opslagaccount met de naam *mystorageaccount* met lokaal redundante opslag (LRS) en blob-codering (standaard ingeschakeld) te maken.

```powershell
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name "mystorageaccount" `
  -SkuName Standard_LRS `
  -Location $location `

$ctx = $storageAccount.Context
```
