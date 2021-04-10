---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 64db4de6628fcd8f3cf160bb2bde1b577219cb10
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729813"
---
Azure Disk Encryption kunnen worden ingeschakeld en beheerd via de [Azure cli](/cli/azure) en [Azure PowerShell](/powershell/azure/new-azureps-module-az). Hiervoor moet u de hulpprogram ma's lokaal installeren en verbinding maken met uw Azure-abonnement.

### <a name="azure-cli"></a>Azure CLI

De [Azure CLI 2,0](/cli/azure) is een opdracht regel programma voor het beheer van Azure-resources. De CLI is ontworpen voor flexibelere query gegevens, ondersteunt langlopende bewerkingen als niet-blokkerende processen en maakt het uitvoeren van scripts eenvoudig. U kunt deze lokaal installeren door de stappen in [de Azure cli installeren](/cli/azure/install-azure-cli)te volgen.

Als u [zich wilt aanmelden bij uw Azure-account met de Azure cli](/cli/azure/authenticate-azure-cli), gebruikt u de opdracht [AZ login](/cli/azure/reference-index#az-login) .

```azurecli
az login
```

Als u een Tenant wilt selecteren om u aan te melden, gebruikt u:
    
```azurecli
az login --tenant <tenant>
```

Als u meerdere abonnementen hebt en u een specifiek abonnement wilt opgeven, kunt u uw lijst met abonnementen ophalen met [AZ account list](/cli/azure/account#az-account-list) en opgeven met [AZ account set](/cli/azure/account#az-account-set).
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Zie [aan de slag met Azure CLI 2,0](/cli/azure/get-started-with-azure-cli)voor meer informatie. 

### <a name="azure-powershell"></a>Azure PowerShell
De [Azure PowerShell AZ-module](/powershell/azure/new-azureps-module-az) bevat een set cmdlets die gebruikmaken van het [Azure Resource Manager](../articles/azure-resource-manager/management/overview.md) model voor het beheren van uw Azure-resources. U kunt dit in uw browser gebruiken met [Azure Cloud shell](../articles/cloud-shell/overview.md), of u kunt het installeren op uw lokale computer met behulp van de instructies in [de Azure PowerShell-module installeren](/powershell/azure/install-az-ps). 

Als u dit al hebt geïnstalleerd, moet u ervoor zorgen dat u de nieuwste versie van Azure PowerShell SDK-versie gebruikt om Azure Disk Encryption te configureren. Down load de nieuwste versie van [Azure PowerShell release](https://github.com/Azure/azure-powershell/releases).

Gebruik de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) om u aan [te melden bij uw Azure-account met Azure PowerShell](/powershell/azure/authenticate-azureps).

```powershell
Connect-AzAccount
```

Als u meerdere abonnementen hebt en er een wilt opgeven, gebruikt u de cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) om ze weer te geven, gevolgd door de cmdlet [set-AzContext](/powershell/module/az.accounts/set-azcontext) :

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Als u de cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) uitvoert, wordt gecontroleerd of het juiste abonnement is geselecteerd.

Als u wilt controleren of de Azure Disk Encryption-cmdlets zijn geïnstalleerd, gebruikt u de cmdlet [Get-opdracht](/powershell/module/microsoft.powershell.core/get-command) :
     
```powershell
Get-command *diskencryption*
```
Zie [aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps)voor meer informatie.