---
title: Kenmerken van een beheerde sleutel maken en ophalen in Azure Key Vault – Azure PowerShell
description: Quick Start laat zien hoe u een beheerde sleutel kunt instellen en ophalen uit Azure Key Vault met behulp van Azure PowerShell
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 01/26/2021
ms.author: mbaldwin
ms.openlocfilehash: 943555e9f7a26530a075aee27dd310d96974e652
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99072910"
---
# <a name="quickstart-set-and-retrieve-a-managed-key-from-azure-key-vault-using-powershell"></a>Snelstartgids: een beheerde sleutel instellen en ophalen uit Azure Key Vault met behulp van Power shell

In deze quickstart maakt u een sleutelkluis in Azure Key Vault met behulp van Azure PowerShell. Azure Key Vault is een cloudservice die werkt als een beveiligd geheimenarchief. U kunt veilig sleutels, wachtwoorden, certificaten en andere geheime informatie opslaan. U kunt het [Overzicht](../general/overview.md) raadplegen voor meer informatie over Key Vault. Azure PowerShell wordt gebruikt voor het maken en beheren van Azure-resources met behulp van opdrachten of scripts. Nadat u dat hebt gedaan, slaat u een sleutel op.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om PowerShell lokaal te installeren en gebruiken, is versie 1.0.0 of hoger van de Azure PowerShell-module vereist voor deze zelfstudie. Typ `$PSVersionTable.PSVersion` om de versie te bekijken. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Als u PowerShell lokaal uitvoert, moet u ook `Login-AzAccount` uitvoeren om verbinding te kunnen maken met Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en beheerd. Gebruik de Azure PowerShell [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet om een resource groep met de naam *myResourceGroup* te maken op de locatie *westus* . 

```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "WestUS"

## Get your principal ID

To create a managed HSM, you will need your Azure Active Directory principal ID.  To obtain your ID, use the Azure PowerShell [Get-AzADUser](/powershell/module/az.resources/get-azaduser) cmdlet, passing your email address to the "UserPrincipalName" parameter:

```azurepowershell-interactive
Get-AzADUser -UserPrincipalName "<your@email.address>"
```

Uw Principal-ID wordt geretourneerd in de indeling ' XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX '.

## <a name="create-a-managed-hsm"></a>Een beheerde HSM maken

Gebruik de Azure PowerShell [New-AzKeyVaultManagedHsm](/powershell/module/az.keyvault/new-azkeyvaultmanagedhsm) cmdlet om een nieuwe Key Vault beheerde HSM te maken. U moet enkele gegevens verstrekken:

- Beheerde HSM-naam: een teken reeks van 3 tot 24 tekens die alleen getallen (0-9), letters (A-z, A-Z) en afbreek streepjes (-) kan bevatten

  > [!Important]
  > Elke beheerde HSM moet een unieke naam hebben. Vervang <uw-unieke beheerde HSM-naam> door de naam van uw beheerde HSM in de volgende voor beelden.

- Naam van resourcegroep: **myResourceGroup**.
- De locatie: **EastUS**.
- Uw Principal-ID: Geef de principal-ID van Azure Active Directory die u in de laatste sectie hebt verkregen aan de Administrator-para meter. 

```azurepowershell-interactive
New-AzKeyVaultManagedHsm -Name "<your-unique-managed-hsm-name>" -ResourceGroupName "myResourceGroup" -Location "West US" -Administrator "<your-principal-ID>"
```

De uitvoer van deze cmdlet toont eigenschappen van de zojuist gemaakte beheerde HSM. Let op de onderstaande twee eigenschappen:

- **Beheerde HSM-naam**: de naam die u hebt door gegeven aan de para meter--name hierboven.
- **Kluis-URI**: in het voor beeld is dit https:// &lt; your-unique-Managed-HSM-name &gt; . Vault.Azure.net/. Toepassingen die via de REST API gebruikmaken van uw kluis, moeten deze URI gebruiken.

Vanaf dit punt is uw Azure-account nu als enige gemachtigd om bewerkingen op deze nieuwe kluis uit te voeren.

## <a name="activate-your-managed-hsm"></a>Uw beheerde HSM activeren

Alle gegevenslaag opdrachten worden uitgeschakeld totdat de HSM wordt geactiveerd. U kunt geen sleutels maken of rollen toewijzen. Alleen de aangewezen beheerders, die zijn toegewezen tijdens het maken van de opdracht, kunnen de HSM activeren. Als u de HSM wilt activeren, moet u het [Beveiligingsdomein](security-domain.md) downloaden.

Om uw HSM te activeren, hebt u het volgende nodig:
- Minimaal 3 RSA-sleutelparen (maximaal 10)
- Geef het minimum aantal sleutels op dat vereist is voor het ontsleutelen van het beveiligingsdomein (quorum)

Als u de HSM wilt activeren, stuurt u ten minste 3 (maximaal 10) openbare RSA-sleutels naar de HSM. De HSM versleutelt het beveiligingsdomein met deze sleutels en stuurt het terug. Als het downloaden van dit beveiligingsdomein voltooid is, is uw HSM klaar voor gebruik. U moet ook een quorum opgeven. Dit is het minimale aantal persoonlijke sleutels dat vereist is voor het ontsleutelen van het beveiligingsdomein.

In het voor beeld hieronder ziet u hoe u `openssl` ( [hier](https://slproweb.com/products/Win32OpenSSL.html)beschikbaar voor Windows) kunt gebruiken voor het genereren van 3 zelf ondertekend certificaat.

```console
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Maak de RSA-sleutelparen en het beveiligingsdomeinbestand dat in deze stap is gegenereerd en sla ze op een veilige manier op.

Gebruik de Azure PowerShell [-cmdlet Export-AzKeyVaultSecurityDomain](/powershell/module/az.keyvault/export-azkeyvaultsecuritydomain) om het beveiligings domein te downloaden en uw beheerde HSM te activeren. In het onderstaande voorbeeld wordt gebruikgemaakt van 3 RSA-sleutelparen (alleen openbare sleutels zijn vereist voor deze opdracht) en wordt het quorum ingesteld op 2.

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
- Raadpleeg het [Overzicht voor Key Vault-beveiliging](../general/security-overview.md)
