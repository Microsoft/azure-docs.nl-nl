---
title: Een geheim voor meerdere regels opslaan in Azure Key Vault
description: Zelf studie waarin wordt getoond hoe u meerdere geheimen van Azure Key Vault kunt instellen met behulp van Azure CLI en Power shell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurecli
ms.date: 03/17/2021
ms.author: mbaldwin
ms.openlocfilehash: e320795d5e8623dea9b97ac6371a0ffc6e6117f1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610281"
---
# <a name="store-a-multi-line-secret-in-azure-key-vault"></a>Een geheim voor meerdere regels opslaan in Azure Key Vault

De [Snelstartgids van Azure cli](quick-create-cli.md) en [Azure PowerShell Quick](quick-create-powershell.md) start laat zien hoe u een geheim van één regel opslaat.   U kunt Key Vault ook gebruiken voor het opslaan van een geheim op meerdere regels, zoals een JSON-bestand of een persoonlijke RSA-sleutel.

Er kunnen geen geheimen voor meerdere regels worden door gegeven aan de opdracht regel voor de Azure CLI [AZ-sleutel kluis](/cli/azure/keyvault/secret#az_keyvault_secret_set) of de Azure PowerShell [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet via de commandline. In plaats daarvan moet u het multi-line geheim eerst opslaan als een tekst bestand. 

U kunt bijvoorbeeld een tekst bestand met de naam ' secretfile.txt ' maken die de volgende regels bevat:

```bash
This is my
multi-line
secret
```

U kunt dit bestand vervolgens door geven aan de geheime set van de Azure CLI [AZ-sleutel kluis](/cli/azure/keyvault/secret#az_keyvault_secret_set) met behulp van de `--file` para meter.

```azurecli-interactive
az keyvault secret set --vault-name "<your-unique-keyvault-name>" --name "MultilineSecret" --file "secretfile.txt"
```

Met Azure PowerShell moet u eerst in het bestand lezen met behulp van de cmdlet [Get-content](/powershell/module/microsoft.powershell.management/get-content) en deze vervolgens converteren naar een veilige teken reeks met behulp van [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring). 

```azurepowershell-interactive
$RawSecret =  Get-Content "secretfile.txt" -Raw
$SecureSecret = ConvertTo-SecureString -String $RawSecret -AsPlainText -Force
```

Ten slotte slaat u het geheim op met behulp van de cmdlet [set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret) .

```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName "<your-unique-keyvault-name>" -Name "MultilineSecret" -SecretValue $SecureSecret
```

In beide gevallen kunt u het opgeslagen geheim weer geven met behulp van de Azure CLI AZ-sleutel [kluis](/cli/azure/keyvault/secret#az_keyvault_secret_show) met de Azure PowerShell opdracht ' [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) cmdlet.

```azurecli-interactive
az keyvault secret show --name "MultilineSecret" --vault-name "<your-unique-keyvault-name>" --query "value"
```

Het geheim wordt geretourneerd met een nieuwe regel die is inge sloten:

```bash
"This is\nmy multi-line\nsecret"
```

## <a name="next-steps"></a>Volgende stappen

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Raadpleeg de [Snelstartgids van Azure cli](quick-create-cli.md)
- Zie de [Azure cli AZ](/cli/azure/keyvault) -sleutel kluis-opdrachten
- Raadpleeg de [Azure PowerShell Snelstartgids](quick-create-powershell.md)
- Zie de [Azure PowerShell AZ. sleutel kluis-cmdlets](/powershell/module/az.keyvault#key-vault)
