---
title: 'Quickstart: Een & weergeven Azure Key Vault certificaten instellen met Azure PowerShell'
description: In deze quickstart wordt uitgelegd hoe u een certificaat instelt en ophaalt uit Azure Key Vault met behulp van Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-azurepowershell
ms.date: 01/27/2021
ms.author: mbaldwin
ms.openlocfilehash: 32150fb4cce76d5a2537c5ec969f265e0d8aae20
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107816184"
---
# <a name="quickstart-set-and-retrieve-a-certificate-from-azure-key-vault-using-azure-powershell"></a>Quickstart: Een certificaat uit Azure Key Vault instellen en ophalen met behulp van Azure PowerShell

In deze quickstart maakt u een sleutelkluis in Azure Key Vault met behulp van Azure PowerShell. Azure Key Vault is een cloudservice die werkt als een beveiligd geheimenarchief. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. U kunt het [Overzicht](../general/overview.md) raadplegen voor meer informatie over Key Vault. Azure PowerShell wordt gebruikt voor het maken en beheren van Azure-resources met behulp van opdrachten of scripts. Nadat u dat hebt gedaan, gaat u een certificaat opslaan.

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

## <a name="add-a-certificate-to-key-vault"></a>Een certificaat toevoegen aan Key Vault

Als u een certificaat wilt toevoegen aan de kluis, hoeft u maar een paar extra stappen uit te voeren. Dit certificaat kan worden gebruikt door een toepassing. 

Typ de onderstaande opdrachten om een zelfondertekend certificaat te maken met een beleid met de naam **ExampleCertificate**:

```azurepowershell-interactive
$Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal

Add-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate" -CertificatePolicy $Policy
```

U kunt nu verwijzen naar dit certificaat dat u aan Azure Key Vault hebt toegevoegd met behulp van de bijbehorende URI. Gebruik **https://<your-unique-keyvault-name>.vault.azure.net/certificates/ExampleCertificate'** om de huidige versie op te halen. 

Een eerder opgeslagen certificaat weergeven:

```azurepowershell-interactive
Get-AzKeyVaultCertificate -VaultName "<your-unique-keyvault-name>" -Name "ExampleCertificate"
```

U hebt nu een sleutelkluis gemaakt, een certificaat opgeslagen en dat vervolgens opgehaald.

**Problemen oplossen:**

Bewerking heeft een ongeldige statuscode 'Verboden' geretourneerd

Als u deze foutmelding ontvangt, heeft het account dat toegang heeft tot de Azure Key Vault niet de juiste machtigingen om certificaten te maken.

Voer de volgende Azure PowerShell uit om de juiste machtigingen toe te wijzen:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> -ObjectId <AzureObjectID> -PermissionsToCertificates get,list,update,create
```

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Key Vault gemaakt en daar een certificaat in opgeslagen. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Zie de referentie voor de [Azure PowerShell Key Vault-cmdlets](/powershell/module/az.keyvault/)
- Raadpleeg het [Overzicht voor Key Vault-beveiliging](../general/security-features.md)
