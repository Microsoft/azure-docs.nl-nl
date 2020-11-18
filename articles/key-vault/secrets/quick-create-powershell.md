---
title: 'Quickstart: Een geheim in Key Vault instellen en daaruit ophalen met PowerShell'
description: In deze quickstart leert u hoe u met behulp van Azure PowerShell geheimen kunt maken, ophalen en verwijderen in een Azure-sleutelkluis.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: mvc, devx-track-azurepowershell
ms.date: 09/30/2020
ms.author: mbaldwin
ms.openlocfilehash: c28dbc25abfd701450cf9f232ea1a4b5e16841aa
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686188"
---
# <a name="quickstart-set-and-retrieve-a-secret-from-azure-key-vault-using-powershell"></a>Quickstart: Een geheim uit Azure Key Vault instellen en ophalen met PowerShell

Azure Key Vault is een cloudservice die werkt als een beveiligd geheimenarchief. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. U kunt het [Overzicht](../general/overview.md) raadplegen voor meer informatie over Key Vault. In deze snelstart gebruikt u PowerShell om een sleutelkluis te maken. Vervolgens slaat u een geheim op in de zojuist gemaakte kluis.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om PowerShell lokaal te installeren en gebruiken, is versie 5.0.0 of hoger van de Azure PowerShell-module vereist voor deze zelfstudie. Typ `$PSVersionTable.PSVersion` om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Connect-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

```azurepowershell-interactive
Connect-AzAccount
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een Azure-resourcegroep met behulp van de opdracht [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd.

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Een sleutelkluis maken

Vervolgens maakt u een sleutelkluis. Om deze stap te kunnen uitvoeren, hebt u enkele gegevens nodig:

Hoewel we in deze quickstart 'Contoso KeyVault2' als naam voor de sleutelkluis gebruiken, moet u een unieke naam opgeven.

- **Kluisnaam** Contoso-Vault2.
- **Naam van resourcegroep** ContosoResourceGroup.
- **Locatie** VS - oost.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

De uitvoer van deze cmdlet toont eigenschappen van de nieuw gemaakte sleutelkluis. Let op de onderstaande twee eigenschappen:

* **Kluisnaam**: in het voorbeeld is dat **Contoso-Vault2**. U gebruikt deze naam voor andere Sleutelkluis-cmdlets.
* **Kluis-URI**: in dit voorbeeld is dat https://Contoso-Vault2.vault.azure.net/. Toepassingen die via de REST API gebruikmaken van uw kluis, moeten deze URI gebruiken.

Nadat de kluis is gemaakt, is uw Azure-account het enige account dat iets met deze nieuwe kluis mag doen.

## <a name="give-your-user-account-permissions-to-manage-secrets-in-key-vault"></a>Geef uw gebruikersaccount machtigingen voor het beheren van geheimen in Key Vault

Gebruik de cmdlet Set-AzKeyVaultAccessPolicy van Azure PowerShell om het toegangsbeleid van Key Vault bij te werken en geheimmachtigingen toe te voegen aan uw gebruikersaccount.
```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName 'Contoso-Vault2' -UserPrincipalName 'user@domain.com' -PermissionsToSecrets get,set,delete
```

## <a name="adding-a-secret-to-key-vault"></a>Een geheim toevoegen aan Key Vault

Als u een geheim wilt toevoegen aan de kluis, hoeft u maar een paar stappen uit te voeren. In dit geval voegt u een wachtwoord toe dat door een toepassing kan worden gebruikt. Het wachtwoord heeft de naam **ExamplePassword** en slaat daarin de waarde van **hVFkk965BuUv** op.

Converteer eerst de waarde van **hVFkk965BuUv** naar een beveiligde tekenreeks door het volgende te typen:

```azurepowershell-interactive
$secretvalue = ConvertTo-SecureString 'hVFkk965BuUv' -AsPlainText -Force
```

Typ vervolgens de onderstaande PowerShell-opdrachten om een geheim te maken in Key Vault met de naam **ExamplePassword** en de waarde **hVFkk965BuUv**:


```azurepowershell-interactive
$secret = Set-AzKeyVaultSecret -VaultName 'Contoso-Vault2' -Name 'ExamplePassword' -SecretValue $secretvalue
```

## <a name="retrieve-a-secret-from-key-vault"></a>Een geheim lezen uit Key Vault

Als u de waarde in het geheim als tekst zonder opmaak wilt weergeven:

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName 'Contoso-Vault2' -Name 'ExamplePassword'
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

U hebt nu een sleutelkluis gemaakt, een geheim opgeslagen en dat vervolgens opgehaald.

## <a name="clean-up-resources"></a>Resources opschonen

 Andere snelstartgidsen en zelfstudies in deze verzameling zijn gebaseerd op deze snelstartgids. Als u van plan bent om verder te gaan met andere snelstarts en zelfstudies, kunt u deze resources intact laten.

U kunt de opdracht [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) gebruiken om de resourcegroep, sleutelkluis en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt.

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een sleutelkluis gemaakt en daar een geheim in opgeslagen. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Zie de referentie voor de [Azure PowerShell Key Vault-cmdlets](/powershell/module/az.keyvault/#key_vault)
- Bekijk de [best practices voor Azure Key Vault](../general/best-practices.md)
