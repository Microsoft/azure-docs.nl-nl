---
title: Kenmerken van een beheerde sleutel maken en ophalen in Azure Key Vault - Azure PowerShell
description: Snelstart waarin wordt getoond hoe u een beheerde sleutel in uw Azure Key Vault en Azure PowerShell
services: key-vault
author: msmbaldwin
ms.author: mbaldwin
ms.date: 01/26/2021
ms.topic: quickstart
ms.service: key-vault
ms.subservice: keys
tags:
- azure-resource-manager
ms.custom:
- mode-api
ms.openlocfilehash: aa984a8f3899db72ead878e2c4381ea6a080e32d
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815428"
---
# <a name="quickstart-set-and-retrieve-a-managed-key-from-azure-key-vault-using-powershell"></a>Quickstart: Een beheerde sleutel instellen en ophalen uit Azure Key Vault powershell

In deze quickstart maakt u een sleutelkluis in Azure Key Vault met behulp van Azure PowerShell. Azure Key Vault is een cloudservice die werkt als een beveiligd geheimenarchief. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. U kunt het [Overzicht](../general/overview.md) raadplegen voor meer informatie over Key Vault. Azure PowerShell wordt gebruikt voor het maken en beheren van Azure-resources met behulp van opdrachten of scripts. Nadat u dat hebt gedaan, slaat u een sleutel op.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om PowerShell lokaal te installeren en gebruiken, is versie 1.0.0 of hoger van de Azure PowerShell-module vereist voor deze zelfstudie. Typ `$PSVersionTable.PSVersion` om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Login-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Gebruik de cmdlet [Azure PowerShell New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) om een resourcegroep met de naam *myResourceGroup* te maken op de *locatie westus.* 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"

## Get your principal ID

To create a managed HSM, you will need your Azure Active Directory principal ID.  To obtain your ID, use the Azure PowerShell [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet, passing your email address to the "UserPrincipalName" parameter:

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName "<your@email.address>"
```

De principal-id wordt geretourneerd in de indeling xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.

## <a name="create-a-managed-hsm"></a>Een beheerde HSM maken

Gebruik de Azure PowerShell [cmdlet New-AzKeyVaultManagedHsm](/powershell/module/az.keyvault/new-azkeyvaultmanagedhsm) om een nieuwe Key Vault HSM te maken. U moet enkele gegevens verstrekken:

- Beheerde HSM-naam: een tekenreeks van 3 tot 24 tekens die alleen cijfers (0-9), letters (a-z, A-Z) en afbreekstreeepten (-) mag bevatten

  > [!Important]
  > Elke beheerde HSM moet een unieke naam hebben. Vervang <naam your-unique-managed-hsm-> door de naam van uw beheerde HSM in de volgende voorbeelden.

- Naam van resourcegroep: **myResourceGroup**.
- De locatie: **EastUS**.
- Uw principal-id: geef de principal Azure Active Directory-id die u in de laatste sectie hebt verkregen, door aan de parameter 'Administrator'. 

```azurepowershell-interactive
New-AzKeyVaultManagedHsm -Name "<your-unique-managed-hsm-name>" -ResourceGroupName "myResourceGroup" -Location "West US" -Administrator "<your-principal-ID>"
```

De uitvoer van deze cmdlet toont eigenschappen van de zojuist gemaakte beheerde HSM. Let op de onderstaande twee eigenschappen:

- **Naam van beheerde HSM:** de naam die u hebt opgegeven voor de parameter --name hierboven.
- **Kluis-URI:** in het voorbeeld is https:// &lt; uw-unieke-beheerde-hsm-naam &gt; .vault.azure.net/. Toepassingen die via de REST API gebruikmaken van uw kluis, moeten deze URI gebruiken.

Vanaf dit punt is uw Azure-account nu als enige gemachtigd om bewerkingen op deze nieuwe kluis uit te voeren.

## <a name="activate-your-managed-hsm"></a>Uw beheerde HSM activeren

Alle gegevensvlakopdrachten worden uitgeschakeld totdat de HSM is geactiveerd. U kunt geen sleutels maken of rollen toewijzen. Alleen de aangewezen beheerders, die zijn toegewezen tijdens het maken van de opdracht, kunnen de HSM activeren. Als u de HSM wilt activeren, moet u het [Beveiligingsdomein](security-domain.md) downloaden.

Om uw HSM te activeren, hebt u het volgende nodig:
- Minimaal 3 RSA-sleutelparen (maximaal 10)
- Geef het minimum aantal sleutels op dat vereist is voor het ontsleutelen van het beveiligingsdomein (quorum)

Als u de HSM wilt activeren, stuurt u ten minste 3 (maximaal 10) openbare RSA-sleutels naar de HSM. De HSM versleutelt het beveiligingsdomein met deze sleutels en stuurt het terug. Als het downloaden van dit beveiligingsdomein voltooid is, is uw HSM klaar voor gebruik. U moet ook een quorum opgeven. Dit is het minimale aantal persoonlijke sleutels dat vereist is voor het ontsleutelen van het beveiligingsdomein.

In het onderstaande voorbeeld ziet u hoe u (hier beschikbaar voor Windows) kunt gebruiken om `openssl` 3 zelfonderschreven certificaten te genereren. [](https://slproweb.com/products/Win32OpenSSL.html)

```console
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Maak de RSA-sleutelparen en het beveiligingsdomeinbestand dat in deze stap is gegenereerd en sla ze op een veilige manier op.

Gebruik de cmdlet [Azure PowerShell Export-AzKeyVaultSecurityDomain](/powershell/module/az.keyvault/export-azkeyvaultsecuritydomain) om het beveiligingsdomein te downloaden en uw beheerde HSM te activeren. In het onderstaande voorbeeld wordt gebruikgemaakt van 3 RSA-sleutelparen (alleen openbare sleutels zijn vereist voor deze opdracht) en wordt het quorum ingesteld op 2.

```azurepowershell-interactive
Export-AzKeyVaultSecurityDomain -Name "<your-unique-managed-hsm-name>" -Certificates "cert_0.cer", "cert_1.cer", "cert_2.cer" -OutputPath "MHSMsd.ps.json" -Quorum 2
```

Sla het beveiligingsdomeinbestand en de RSA-sleutelparen veilig op. U hebt deze nodig voor herstel na noodgevallen of voor het maken van een andere beheerde HSM die hetzelfde beveiligingsdomein deelt, zodat ze sleutels kunnen delen.

Nadat het beveiligingsdomein is gedownload, krijgt uw HSM de status actief en kunt u uw HSM gebruiken.

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [Create a key vault](../../../includes/key-vault-powershell-delete-resources.md)]

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Key Vault gemaakt en daar een certificaat in opgeslagen. Voor meer informatie over Key Vault en hoe u Key Vault integreert met uw toepassingen gaat u verder naar de artikelen hieronder.

- Lees een [Overzicht van Azure Key Vault](../general/overview.md)
- Zie de referentie voor de [Azure PowerShell Key Vault-cmdlets](/powershell/module/az.keyvault/)
- Raadpleeg het [Overzicht voor Key Vault-beveiliging](../general/security-features.md)
