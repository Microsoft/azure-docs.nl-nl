---
title: Certificaten exporteren uit Azure Key Vault
description: Meer informatie over het exporteren van certificaten uit Azure Key Vault.
services: key-vault
author: sebansal
tags: azure-key-vault
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.custom: mvc, devx-track-azurecli
ms.date: 08/11/2020
ms.author: sebansal
ms.openlocfilehash: 4339e8217702e9f25877bc8c250b5363e2c59a42
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483692"
---
# <a name="export-certificates-from-azure-key-vault"></a>Certificaten exporteren uit Azure Key Vault

Meer informatie over het exporteren van certificaten uit Azure Key Vault. U kunt certificaten exporteren met behulp van de Azure-CLI, Azure PowerShell of de Azure Portal. U kunt ook de Azure Portal gebruiken om Azure App Service-certificaten te exporteren.

## <a name="about-azure-key-vault-certificates"></a>Informatie over Azure Key Vault-certificaten

Met Azure Key Vault kunt u eenvoudig digitale certificaten voor uw netwerk inrichten, beheren en implementeren. Er wordt ook beveiligde communicatie voor toepassingen ingeschakeld. Raadpleeg [Azure Key Vault-certificaten](./about-certificates.md) voor meer informatie.

### <a name="composition-of-a-certificate"></a>Samenstelling van een certificaat

Wanneer er een Key Vault-certificaat wordt gemaakt, worden er ook een adresseerbare *sleutel* en een *geheim* gemaakt met dezelfde naam. Met de Key Vault-sleutel kunnen sleutelbewerkingen worden uitgevoerd. Met het Key Vault-geheim kan zowel de certificaatwaarde als het geheim worden opgehaald. Een Key Vault-certificaat bevat ook openbare metagegevens voor x509-certificaten. Ga naar [Samenstelling van een certificaat](./about-certificates.md#composition-of-a-certificate) voor meer informatie.

### <a name="exportable-and-non-exportable-keys"></a>Exporteerbare en niet-exporteerbare sleutels

Nadat een Key Vault-certificaat is gemaakt, kan het worden opgehaald uit het adresseerbare geheim met de privésleutel. Haal het certificaat op in PFX- of PEM-indeling.

- **Exporteerbaar**: Het beleid dat wordt gebruikt voor het maken van het certificaat geeft aan dat de sleutel exporteerbaar is.
- **Niet-exporteerbaar**: Het beleid dat wordt gebruikt voor het maken van het certificaat geeft aan dat de sleutel niet-exporteerbaar is. In dit geval maakt de persoonlijke sleutel geen deel uit van de waarde wanneer deze wordt opgehaald als geheim.

Ondersteunde sleuteltypen: RSA, RSA-HSM, EC, EC-HSM, oct ([hier](/rest/api/keyvault/createcertificate/createcertificate#jsonwebkeytype) genoemd) Exporteren is alleen toegestaan met RSA, EC. HSM-sleutels zouden niet exporteerbaar zijn.

Raadpleeg [Over Azure Key Vault-certificaten](./about-certificates.md#exportable-or-non-exportable-key) voor meer informatie.

## <a name="export-stored-certificates"></a>Opgeslagen certificaten exporteren

U kunt opgeslagen certificaten exporteren in Azure Key Vault met behulp van de Azure-CLI, Azure PowerShell of de Azure Portal.

> [!NOTE]
> U hoeft alleen een certificaatwachtwoord in te voeren wanneer u het certificaat in de sleutelkluis importeert. Key Vault slaat het gekoppelde wachtwoord niet op. Wanneer u het certificaat exporteert, is het wachtwoord leeg.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Gebruik de volgende opdracht in de Azure-CLI om het **openbare deel** van een Key Vault-certificaat te downloaden.

```azurecli
az keyvault certificate download --file
                                 [--encoding {DER, PEM}]
                                 [--id]
                                 [--name]
                                 [--subscription]
                                 [--vault-name]
                                 [--version]
```

Bekijk [voorbeelden en parameterdefinities](/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-download) voor meer informatie.

Downloaden als certificaat betekent dat het openbare gedeelte wordt opgehaald. Als u zowel de privésleutel als de openbare metagegevens wilt hebben, kunt u deze downloaden als geheim.

```azurecli
az keyvault secret download -–file {nameofcert.pfx}
                            [--encoding {ascii, base64, hex, utf-16be, utf-16le, utf-8}]
                            [--id]
                            [--name]
                            [--subscription]
                            [--vault-name]
                            [--version]
```

Zie [parameterdefinities](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-download) voor meer informatie.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gebruik deze opdracht in Azure PowerShell om het certificaat de naam **TestCert01** te geven vanuit de sleutelkluis met de naam **ContosoKV01**. Voer de volgende opdracht uit om het certificaat als PFX-bestand te downloaden. Met deze opdrachten krijgt u toegang tot **SecretId** en kunt u de inhoud opslaan als een PFX-bestand.

```azurepowershell
$cert = Get-AzKeyVaultCertificate -VaultName "ContosoKV01" -Name "TestCert01"
$secret = Get-AzKeyVaultSecret -VaultName "ContosoKV01" -Name $cert.Name
$secretValueText = '';
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
    $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
    [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
$secretByte = [Convert]::FromBase64String($secretValueText)
$x509Cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509Cert.Import($secretByte, "", "Exportable,PersistKeySet")
$type = [System.Security.Cryptography.X509Certificates.X509ContentType]::Pfx
$pfxFileByte = $x509Cert.Export($type, $password)

# Write to a file
[System.IO.File]::WriteAllBytes("KeyVault.pfx", $pfxFileByte)
```

Met deze opdracht wordt de hele keten met certificaten met een persoonlijke sleutel geëxporteerd. Het certificaat is beveiligd met een wachtwoord.
Zie [Get-AzKeyVaultCertificate - Voorbeeld 2](/powershell/module/az.keyvault/Get-AzKeyVaultCertificate?view=azps-4.4.0)voor meer informatie over de **Get-AzKeyVaultCertificate**-opdracht en para meters.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Wanneer u op de Azure Portal een certificaat op de blade **Certificaat** maakt/importeert, ontvangt u een melding dat het certificaat is gemaakt. Selecteer het certificaat en de huidige versie om de optie om te downloaden te zien.

Als u het certificaat wilt downloaden, selecteert u **Downloaden in CER-indeling** of **Downloaden in PFX/PEM-indeling**.

![Certificaat downloaden](../media/certificates/quick-create-portal/current-version-shown.png)

**Azure App Service-certificaten exporteren**

Azure App Service-certificaten zijn een handige manier om SSL-certificaten aan te schaffen. U kunt deze toewijzen aan Azure-apps vanuit de portal. U kunt deze certificaten ook exporteren vanuit de portal als PFX-bestanden zodat ze elders gebruikt kunnen worden. Na het importeren bevinden de App Service-certificaten zich onder **geheimen**.

Zie de stappen voor het [exporteren van Azure App Service-certificaten](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx) voor meer informatie.

---

## <a name="read-more"></a>Meer informatie
* [Verschillende typen certificaatbestanden en -definities](/archive/blogs/kaushal/various-ssltls-certificate-file-typesextensions)
