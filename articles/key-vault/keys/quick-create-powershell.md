---
title: 'Kenmerken van een sleutel in Azure Key Vault maken en ophalen: Azure PowerShell'
description: Quickstart waarin wordt getoond hoe u een sleutel instelt in Azure Key Vault en daaruit ophaalt met behulp van Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: adbf3080367e54147c981c8ccf0bb6236111b8c7
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071202"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-powershell"></a>Quickstart: Een sleutel instellen in Azure Key Vault en daaruit ophalen met behulp van Azure PowerShell

In deze quickstart maakt u een sleutelkluis in Azure Key Vault met behulp van Azure PowerShell. Azure Key Vault is een cloudservice die werkt als een beveiligd geheimenarchief. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. U kunt het [Overzicht](../general/overview.md) raadplegen voor meer informatie over Key Vault. Azure PowerShell wordt gebruikt voor het maken en beheren van Azure-resources met behulp van opdrachten of scripts. Nadat u dat hebt gedaan, slaat u een sleutel op.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om PowerShell lokaal te installeren en gebruiken, is versie 1.0.0 of hoger van de Azure PowerShell-module vereist voor deze zelfstudie. Typ `$PSVersionTable.PSVersion` om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Login-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

[!INCLUDE [Create a resource group](../../../includes/key-vault-powershell-rg-creation.md)]

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-kv-creation.md)]

## <a name="add-a-key-to-key-vault"></a>Een sleutel toevoegen aan Key Vault

Als u een sleutel wilt toevoegen aan de kluis, hoeft u maar een paar extra stappen uit te voeren. Deze sleutel zou kunnen worden gebruikt door een toepassing. 

Typ de onderstaande opdrachten om een aangeroepen **ExampleKey** te maken:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "ExampleKey" -Destination "Software"
```

U kunt nu naar deze sleutel die u aan Azure Key Vault hebt toegevoegd, verwijzen met behulp van de URI ervan. Gebruik **https://<uw-unieke kluis naam>. Vault.Azure.net/Keys/ExampleKey** om de huidige versie op te halen. 

Een eerder opgeslagen sleutel weergeven:

```azurepowershell-interactive
Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -KeyName "ExampleKey"
```

U hebt nu een sleutelkluis gemaakt, een sleutel opgeslagen en deze vervolgens opgehaald.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Key Vault gemaakt en daar een certificaat in opgeslagen. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Zie de referentie voor de [Azure PowerShell Key Vault-cmdlets](/powershell/module/az.keyvault/)
- Raadpleeg het [Overzicht voor Key Vault-beveiliging](../general/security-overview.md)
