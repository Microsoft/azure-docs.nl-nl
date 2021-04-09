---
title: Certificaten en veilige toegang Azure Key Vault met batch gebruiken
description: Meer informatie over hoe u via een programma toegang hebt tot uw referenties vanuit Key Vault met behulp van Azure Batch.
ms.topic: how-to
ms.date: 10/28/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eaaeaa05caca7897eb649b56504b643038f08d53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99260126"
---
# <a name="use-certificates-and-securely-access-azure-key-vault-with-batch"></a>Certificaten en veilige toegang Azure Key Vault met batch gebruiken

In dit artikel leert u hoe u batch knooppunten instelt op veilige toegang tot referenties die zijn opgeslagen in [Azure Key Vault](../key-vault/general/overview.md). Het is niet mogelijk om uw beheerders referenties in Key Vault in te stellen en vervolgens de referenties voor het verkrijgen van een vaste code voor toegang tot Key Vault vanuit een script. De oplossing is het gebruik van een certificaat dat uw batch knooppunten toegang geeft tot Key Vault.

Als u wilt verifiëren bij Azure Key Vault van een batch-knoop punt, hebt u het volgende nodig:

- Een Azure Active Directory-Referentie (Azure AD)
- Een certificaat
- Een batch-account
- Een batch-pool met ten minste één knoop punt

## <a name="obtain-a-certificate"></a>Een certificaat verkrijgen

Als u nog geen certificaat hebt, kunt u er het beste een zelfondertekend certificaat genereren met behulp van het `makecert` opdracht regel programma.

U kunt `makecert` dit pad meestal vinden: `C:\Program Files (x86)\Windows Kits\10\bin\<arch>` . Open een opdracht prompt als beheerder en ga naar `makecert` het volgende voor beeld.

```console
cd C:\Program Files (x86)\Windows Kits\10\bin\x64
```

Gebruik vervolgens het `makecert` hulp programma om zelfondertekende certificaat bestanden te maken met de naam `batchcertificate.cer` en `batchcertificate.pvk` . De gebruikte algemene naam (CN) is niet van belang voor deze toepassing, maar het is wel handig om er iets van te maken dat aangeeft waarvoor het certificaat wordt gebruikt.

```console
makecert -sv batchcertificate.pvk -n "cn=batch.cert.mydomain.org" batchcertificate.cer -b 09/23/2019 -e 09/23/2019 -r -pe -a sha256 -len 2048
```

Voor batch is een `.pfx` bestand vereist. Gebruik het hulp programma [Pvk2pfx](/windows-hardware/drivers/devtest/pvk2pfx) om de `.cer` en `.pvk` bestanden die zijn gemaakt door `makecert` te converteren naar één `.pfx` bestand.

```console
pvk2pfx -pvk batchcertificate.pvk -spc batchcertificate.cer -pfx batchcertificate.pfx -po
```

## <a name="create-a-service-principal"></a>Een service-principal maken

De toegang tot Key Vault wordt verleend aan een **gebruiker** of een **Service-Principal**. Als u toegang wilt krijgen tot Key Vault via een programma, gebruikt u een [Service-Principal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) met het certificaat dat u in de vorige stap hebt gemaakt. De Service-Principal moet zich in dezelfde Azure AD-Tenant beKey Vault.

```powershell
$now = [System.DateTime]::Parse("2020-02-10")
# Set this to the expiration date of the certificate
$expirationDate = [System.DateTime]::Parse("2021-02-10")
# Point the script at the cer file you created $cerCertificateFilePath = 'c:\temp\batchcertificate.cer'
$cer = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$cer.Import($cerCertificateFilePath)
# Load the certificate into memory
$credValue = [System.Convert]::ToBase64String($cer.GetRawCertData())
# Create a new AAD application that uses this certificate
$newADApplication = New-AzureRmADApplication -DisplayName "Batch Key Vault Access" -HomePage "https://batch.mydomain.com" -IdentifierUris "https://batch.mydomain.com" -certValue $credValue -StartDate $now -EndDate $expirationDate
# Create new AAD service principal that uses this application
$newAzureAdPrincipal = New-AzureRmADServicePrincipal -ApplicationId $newADApplication.ApplicationId
```

De Url's voor de toepassing zijn niet belang rijk, omdat we deze alleen gebruiken voor Key Vault toegang.

## <a name="grant-rights-to-key-vault"></a>Rechten toekennen aan Key Vault

De service-principal die in de vorige stap is gemaakt, heeft toestemming nodig om de geheimen op te halen uit Key Vault. Machtigingen kunnen worden verleend via de [Azure Portal](../key-vault/general/assign-access-policy-portal.md) of met de Power shell-opdracht hieronder.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'BatchVault' -ServicePrincipalName '"https://batch.mydomain.com' -PermissionsToSecrets 'Get'
```

## <a name="assign-a-certificate-to-a-batch-account"></a>Een certificaat toewijzen aan een batch-account

Maak een batch-pool en ga naar het tabblad certificaat in de groep en wijs het certificaat toe dat u hebt gemaakt. Het certificaat bevindt zich nu op alle batch knooppunten.

Wijs vervolgens het certificaat toe aan het batch-account. Door het certificaat toe te wijzen aan het account, kan de batch deze toewijzen aan de groepen en vervolgens aan de knoop punten. De eenvoudigste manier om dit te doen is door naar uw batch-account in de portal te gaan, naar **certificaten** te gaan en **toevoegen** te selecteren. Upload het `.pfx` bestand dat u eerder hebt gegenereerd en geef het wacht woord op. Zodra het certificaat is voltooid, wordt het toegevoegd aan de lijst en kunt u de vinger afdruk controleren.

Wanneer u nu een batch-pool maakt, kunt u naar **certificaten** in de groep navigeren en het certificaat toewijzen dat u hebt gemaakt voor die groep. Wanneer u dit doet, moet u ervoor zorgen dat u **LocalMachine** selecteert voor de opslag locatie. Het certificaat wordt geladen op alle batch knooppunten in de groep.

## <a name="install-azure-powershell"></a>Azure PowerShell installeren

Als u van plan bent om toegang te krijgen tot Key Vault met behulp van Power shell-scripts op uw knoop punten, moet de Azure PowerShell-bibliotheek zijn geïnstalleerd. Als op uw knoop punten Windows Management Framework (WMF) 5 is geïnstalleerd, kunt u de opdracht install-module gebruiken om deze te downloaden. Als u gebruikmaakt van knoop punten die geen WMF 5 hebben, kunt u deze eenvoudig installeren door het Azure PowerShell-bestand te bundelen `.msi` met uw batch-bestanden en vervolgens het installatie programma aan te roepen als eerste deel van het batch-opstart script. Zie dit voor beeld voor meer informatie:

```powershell
$psModuleCheck=Get-Module -ListAvailable -Name Azure -Refresh
if($psModuleCheck.count -eq 0) {
    $psInstallerPath = Join-Path $downloadPath "azure-powershell.3.4.0.msi" Start-Process msiexec.exe -ArgumentList /i, $psInstallerPath, /quiet -wait
}
```

## <a name="access-key-vault"></a>Key Vault openen

Nu bent u klaar om toegang te krijgen tot Key Vault in scripts die worden uitgevoerd op uw batch knooppunten. Als u toegang wilt krijgen tot Key Vault vanuit een script, hebt u uw script nodig om te verifiëren bij Azure AD met behulp van het certificaat. Als u dit wilt doen in Power shell, gebruikt u de volgende voorbeeld opdrachten. Geef de juiste GUID op voor de **vinger afdruk**, **App-ID** (de ID van uw service-principal) en **Tenant-id** (de Tenant waar uw Service-Principal zich bevindt).

```powershell
Add-AzureRmAccount -ServicePrincipal -CertificateThumbprint -ApplicationId
```

Zodra de verificatie is uitgevoerd, hebt u toegang tot de sleutel kluis zoals u dat gewend bent.

```powershell
$adminPassword=Get-AzureKeyVaultSecret -VaultName BatchVault -Name batchAdminPass
```

Dit zijn de referenties die u in uw script kunt gebruiken.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Key Vault](../key-vault/general/overview.md).
- Bekijk de [Azure Security-basis lijn voor batch](security-baseline.md).
- Meer informatie over batch-functies, zoals [het configureren van toegang tot reken knooppunten, het](pool-endpoint-configuration.md) [gebruik van Linux-reken knooppunten](batch-linux-nodes.md)en het [gebruik van privé-eind punten](private-connectivity.md).