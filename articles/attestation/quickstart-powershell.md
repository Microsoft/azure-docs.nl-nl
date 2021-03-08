---
title: Azure Attestation instellen met behulp van Microsoft Azure PowerShell
description: Een attestation-provider instellen en configureren met Azure PowerShell.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: ebc12982bcc05c2d07f91fbc4d85afc170adf075
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453390"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-powershell"></a>Quickstart: Azure Attestation instellen met behulp van Microsoft Azure PowerShell

Volg de onderstaande stappen voor het maken en configureren van een Attestation-provider met behulp van Azure PowerShell. Zie [Overzicht van Azure PowerShell](/powershell/azure/) voor meer informatie over het installeren en uitvoeren van Azure PowerShell.

Houd er rekening mee dat door de PowerShell Gallery Transport Layer Security (TLS) versies 1,0 en 1,1 verouderd zijn. TLS 1,2 of een latere versie wordt aanbevolen. De volgende fouten kunnen optreden:

- WAARSCHUWING: Kan de pakketbron 'https://www.powershellgallery.com/api/v2 ' niet omzetten
- PackageManagement\Install-Package: Er is geen overeenkomst gevonden voor de opgegeven zoekcriteria enmodule naam 

Als u wilt blijven werken met de PowerShell Gallery, voert u de volgende opdracht uit vóór de installatie-module-opdrachten

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
```

## <a name="install-azattestation-powershell-module"></a>De module Az.Attestation PowerShell installeren

Op machines met Azure PowerShell installeert u de Az.Attestation Power shell-module. Deze bevat cmdlets voor Azure Attestation.  

### <a name="initial-installation"></a>Eerste installatie

Beëindig alle bestaande PowerShell-Vensters.

Als u wilt installeren voor "huidige gebruiker", start u een PowerShell-venster zonder verhoogde bevoegdheden en voert u de volgende opdracht uit:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope CurrentUser
```

Als u wilt installeren voor "alle gebruikers", start u een PowerShell-venster zonder verhoogde bevoegdheden en voert u de volgende opdracht uit:

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope AllUsers
```

Sluit de PowerShell-console met verhoogde bevoegdheden.

### <a name="update-the-installation"></a>Werk de installatie bij

Beëindig alle bestaande PowerShell-Vensters.

Als u wilt bijwerken voor "huidige gebruiker", start u een PowerShell-venster zonder verhoogde bevoegdheden en voert u de volgende opdracht uit:

```powershell
Update-Module -Name Az.Attestation
```

Als u wilt bijwerken voor "alle gebruikers", start u een PowerShell-venster met verhoogde bevoegdheden en voert u de volgende opdracht uit:

```powershell
Update-Module -Name Az.Attestation
```

Sluit de PowerShell-console met verhoogde bevoegdheden.

### <a name="get-installed-modules"></a>Geïnstalleerde modules ophalen

Minimale versie van AZ-modules die vereist zijn om Attestation-bewerkingen te ondersteunen:
- Az 4.5.0
- Az.Accounts 1.9.2
- Az.Attestation 0.1.8

Voer de onderstaande opdracht uit om de geïnstalleerde versie van alle AZ-modules te controleren 

```powershell
Get-InstalledModule
```
Als de versies niet overeenkomen met de minimumvereiste, voert u de Update-Module-opdrachten uit.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure in de PowerShell-console (zonder verhoogde toegangsrechten).

```powershell
Connect-AzAccount
```

Als dat nodig is, schakelt u over naar het abonnement dat moet worden gebruikt voor Azure Attestation.

```powershell
Set-AzContext -Subscription <subscription id>  
```

## <a name="register-microsoftattestation-resource-provider"></a>Registreer de resourceprovider Microsoft.Attestation

Registreer de resourceprovider Microsoft.Attestation in een abonnement. Zie [Azure-resourceproviders en -typen](../azure-resource-manager/management/resource-providers-and-types.md)voor meer informatie over Azure-resourceproviders en het configureren en het beheren van resourceproviders. Houd er rekening mee dat het registreren van een resourceprovider slechts eenmaal is vereist voor een abonnement.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Attestation
```
## <a name="regional-availability-of-azure-attestation"></a>Regionale beschikbaarheid van Azure Attestation

```powershell
(Get-AzResourceProvider -ProviderNamespace Microsoft.Attestation)[0].Locations
```

## <a name="create-an-azure-resource-group"></a>Maak een Azure-resourcegroep

Maak een resource groep voor de Attestation-provider. Houd er rekening mee dat andere Azure-resources (met inbegrip van een virtuele machine met een client-toepassingsexemplaar) in dezelfde resourcegroep kunnen worden geplaatst.

```powershell
$location = "uksouth" 
$attestationResourceGroup = "<attestation provider resource group name>"
New-AzResourceGroup -Name $attestationResourceGroup -Location $location 
```

## <a name="create-and-manage-an-attestation-provider"></a>Maak en beheer een Attestation-provider

New-AzAttestation maakt een Attestation-provider.

```powershell
$attestationProvider = "<attestation provider name>" 
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location
```

PolicySignerCertificateFile is een bestand dat een set vertrouwde ondertekeningssleutels specificeert. Als er een bestandsnaam is opgegeven voor de parameter PolicySignerCertificateFile, kan de Attestation-provider alleen worden geconfigureerd met beleid in ondertekende JWT-indeling. Anders kan beleid worden geconfigureerd in een tekst of een niet-ondertekende JWT-indeling.

```powershell
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location -PolicySignersCertificateFile "C:\test\policySignersCertificates.pem"
```

Zie [Voorbeelden Certificaat van beleidsondertekening](policy-signer-examples.md) voor het voorbeeld PolicySignersCertificateFile.

Get-AzAttestation haalt de eigenschappen van de Attestation-provider zoals status en AttestURI op. Maak een notitie van AttestURI, omdat deze later nodig is.

```azurepowershell
Get-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup  
```

De bovenstaande opdracht moet een uitvoer leveren zoals hieronder: 

```
Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
Location: MyLocation
ResourceGroupName: MyResourceGroup
Name: MyAttestationProvider
Status: Ready
TrustModel: AAD
AttestUri: https://MyAttestationProvider.us.attest.azure.net 
Tags: 
TagsTable: 
```

Attestation-providers kunnen worden verwijderd met de cmdlet Remove-AzAttestation.  

```powershell
Remove-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup
```

## <a name="policy-management"></a>Beleidsbeheer

Voor het beheren van beleid, heeft een Azure AD-gebruiker de volgende machtigingen nodig voor 'Acties':
- Microsoft.Attestation/attestationProviders/attestation/read
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

Deze machtigingen kunnen worden toegewezen aan een AD-gebruiker via een rol zoals 'Eigenaar' (machtigingen voor jokertekens), 'Inzender' (machtigingen voor jokertekens) of 'Attestation-inzender' (alleen specifieke machtigingen voor Azure Attestation).  

Voor het lezen van beleid, heeft een Azure AD-gebruiker de volgende machtigingen nodig voor 'Acties':
- Microsoft.Attestation/attestationProviders/attestation/read

Deze machtiging kan worden toegewezen aan een AD-gebruiker via een rol zoals 'Lezer' (machtigingen voor jokertekens) of 'Attestation-lezer' (alleen specifieke machtigingen voor Azure Attestation).

Onderstaande PowerShell-cmdlets bieden het beheer van beleid voor een Attestation-provider geboden (één TEE per keer).

Get-AzAttestationPolicy retourneert het huidige beleid voor de opgegeven TEE. De cmdlet geeft beleid weer in de tekst- en JWT-indeling van het beleid.

```powershell
$teeType = "<tee Type>"
Get-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

De ondersteunde TEE-typen zijn SgxEnclave, OpenEnclave en VbsEnclave.

Set-AttestationPolicy stelt een nieuw beleid in voor de opgegeven TEE. De cmdlet accepteert beleid in tekst- of JWT-indeling en wordt beheerd door de parameter PolicyFormat. "Tekst" is de standaardwaarde voor PolicyFormat. 

```powershell
$policyFormat = "<policy format>"
$policy=Get-Content -path "C:\test\policy.txt" -Raw
Set-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType -Policy $policy -PolicyFormat $policyFormat 
```

Als PolicySignerCertificateFile wordt opgegeven tijdens het maken van een Attestation-provider, kunnen beleidsregels alleen worden geconfigureerd in ondertekende JWT-indeling. Anders kan beleid worden geconfigureerd in een tekst of een niet-ondertekende JWT-indeling.

Attestation-beleid in JWT-indeling moet een claim met de naam "AttestationPolicy" bevatten. Voor ondertekend beleid moet JWT zijn ondertekend met een persoonlijke sleutel die overeenkomt met een van de bestaande certificaten voor beleidsondertekening.

Zie [Voorbeelden van een Attestation-beleid](policy-examples.md) voor voorbeelden van beleid.

Reset-AzAttestationPolicy stelt het beleid opnieuw in op de standaardwaarde voor de opgegeven TEE.

```powershell
Reset-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

## <a name="policy-signer-certificates-management"></a>Beheer van certificaten voor beleidsondertekening

Onderstaande PowerShell-cmdlets bieden het beheer van de certificaten van beleidsondertekening voor een Attestation-provider geboden:

```powershell
Get-AzAttestationPolicySigners -Name $attestationProvider -ResourceGroupName $attestationResourceGroup

Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>

Remove-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>
```

Het certificaat van beleidsondertekening is een ondertekende JWT met een claim met de naam "maa-policyCertificate". De waarde van de claim is een JWK die de vertrouwde ondertekeningssleutel bevat die moet worden toegevoegd. De JWT moet zijn ondertekend met een persoonlijke sleutel die overeenkomt met een van de bestaande certificaten voor beleidsondertekening.

Houd er rekening mee dat alle semantische manipulatie van het certificaat van beleidsondertekening moet worden uitgevoerd buiten PowerShell. Wat PowerShell betreft, is dit een eenvoudige tekenreeks.

Zie [Voorbeelden certificaat van beleidsondertekening](policy-signer-examples.md) voor een voorbeeldertificaat van beleidsondertekening.

Zie [Azure Attestation PowerShell-cmdlets voor meer informatie over de cmdlets en de bijbehorende parameters](/powershell/module/az.attestation/?view=azps-4.3.0#attestation) 

## <a name="next-steps"></a>Volgende stappen

- [Een Attestation-beleid ontwerpen en ondertekenen](author-sign-policy.md)
- [Een SGX-enclave-attest verklaren met codevoorbeelden](/samples/browse/?expanded=azure&terms=attestation)
