---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file
ms.openlocfilehash: a550087f552238820ecff079ff1dc2523c4172d3
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776781"
---
Azure Disk Encryption kunnen worden ingeschakeld en beheerd via [de Azure CLI](/cli/azure) en [Azure PowerShell](/powershell/azure/new-azureps-module-az). Als u dit wilt doen, moet u de hulpprogramma's lokaal installeren en verbinding maken met uw Azure-abonnement.

### <a name="azure-cli"></a>Azure CLI

De [Azure CLI 2.0](/cli/azure) is een opdrachtregelprogramma voor het beheren van Azure-resources. De CLI is ontworpen om flexibel query's uit te voeren op gegevens, langlopende bewerkingen als niet-blokkerende processen te ondersteunen en het uitvoeren van scripts eenvoudig te maken. U kunt deze lokaal installeren door de stappen te volgen in [Azure CLI installeren.](/cli/azure/install-azure-cli)

Gebruik de opdracht [az login](/cli/azure/reference-index#az_login) om u aan te melden bij uw [Azure-account](/cli/azure/authenticate-azure-cli)met de Azure CLI.

```azurecli
az login
```

Als u een tenant wilt selecteren om u bij aan te melden, gebruikt u:
    
```azurecli
az login --tenant <tenant>
```

Als u meerdere abonnementen hebt en een specifiek abonnement wilt [opgeven,](/cli/azure/account#az_account_set)kunt u uw abonnementslijst op halen met [az account list](/cli/azure/account#az_account_list) en opgeven met az account set .
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

Zie Aan de slag [met Azure CLI 2.0](/cli/azure/get-started-with-azure-cli)voor meer informatie. 

### <a name="azure-powershell"></a>Azure PowerShell
De [Azure PowerShell az module biedt](/powershell/azure/new-azureps-module-az) een set cmdlets die gebruikmaakt van het [Azure Resource Manager-model](../articles/azure-resource-manager/management/overview.md) voor het beheren van uw Azure-resources. U kunt deze in uw browser gebruiken [met Azure Cloud Shell](../articles/cloud-shell/overview.md)of u kunt deze installeren op uw lokale computer met behulp van de instructies in De module Azure PowerShell [installeren.](/powershell/azure/install-az-ps) 

Als u deze al lokaal hebt geïnstalleerd, moet u ervoor zorgen dat u de nieuwste versie van Azure PowerShell SDK gebruikt om deze te Azure Disk Encryption. Download de nieuwste versie van [Azure PowerShell release](https://github.com/Azure/azure-powershell/releases).

Als [u zich wilt aanmelden bij uw Azure-account Azure PowerShell](/powershell/azure/authenticate-azureps), gebruikt u de cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount)

```powershell
Connect-AzAccount
```

Als u meerdere abonnementen hebt en er een wilt opgeven, gebruikt u de cmdlet [Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) om deze weer te geven, gevolgd door de cmdlet [Set-AzContext:](/powershell/module/az.accounts/set-azcontext)

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

Als u de cmdlet [Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) gebruikt, wordt gecontroleerd of het juiste abonnement is geselecteerd.

Gebruik de cmdlet [Get-command](/powershell/module/microsoft.powershell.core/get-command) om te controleren of Azure Disk Encryption cmdlets zijn geïnstalleerd:
     
```powershell
Get-command *diskencryption*
```
Zie Aan de slag met Azure PowerShell [voor meer informatie.](/powershell/azure/get-started-azureps)