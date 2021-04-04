---
title: Certificaten in Azure Automation beheren
description: In dit artikel leest u hoe u met certificaten werkt voor toegang door runbooks en DSC-configuraties.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.openlocfilehash: cbf9eb6c97dcceeca5e86e8bef47a39fb685792f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97734807"
---
# <a name="manage-certificates-in-azure-automation"></a>Certificaten in Azure Automation beheren

Azure Automation certificaten veilig opslaat voor toegang door runbooks en DSC-configuraties, met behulp van de cmdlet [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) voor Azure Resource Manager resources. Met beveiligde certificaat opslag kunt u runbooks en DSC-configuraties maken die gebruikmaken van certificaten voor verificatie, of deze toevoegen aan Azure of bronnen van derden.

>[!NOTE]
>Beveilig assets in Azure Automation referenties, certificaten, verbindingen en versleutelde variabelen bevatten. Deze assets worden versleuteld en opgeslagen in Automation met behulp van een unieke sleutel die wordt gegenereerd voor elk Automation-account. Automation slaat de sleutel op in de door het systeem beheerde Key Vault-service. Voordat u een beveiligde Asset opslaat, laadt Automation de sleutel van Key Vault en gebruikt deze om de Asset te versleutelen.

## <a name="powershell-cmdlets-to-access-certificates"></a>Power shell-cmdlets voor toegang tot certificaten

Met de cmdlets in de volgende tabel worden Automation-certificaten met Power shell gemaakt en beheerd. Ze worden geleverd als onderdeel van de [AZ-modules](modules.md#az-modules).

|Cmdlet |Beschrijving|
| --- | ---|
|[Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate)|Haalt informatie op over een certificaat dat moet worden gebruikt in een runbook of DSC-configuratie. U kunt het certificaat zelf alleen ophalen met behulp van de interne `Get-AutomationCertificate` cmdlet.|
|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)|Hiermee maakt u een nieuw certificaat in Automation.|
|[Remove-AzAutomationCertificate](/powershell/module/Az.Automation/Remove-AzAutomationCertificate)|Hiermee verwijdert u een certificaat uit Automation.|
|[Set-AzAutomationCertificate](/powershell/module/Az.Automation/Set-AzAutomationCertificate)|Hiermee stelt u de eigenschappen van een bestaand certificaat in, waaronder het uploaden van het certificaat bestand en het instellen van het wacht woord voor een **PFX** -bestand.|

De cmdlet [add-AzureCertificate](/powershell/module/servicemanagement/azure.service/add-azurecertificate) kan ook worden gebruikt voor het uploaden van een service certificaat voor de opgegeven Cloud service.

## <a name="internal-cmdlets-to-access-certificates"></a>Interne cmdlets voor toegang tot certificaten

De interne cmdlet in de volgende tabel wordt gebruikt voor toegang tot certificaten in uw runbooks. Deze cmdlet wordt geleverd met de globale module `Orchestrator.AssetManagement.Cmdlets` . Zie [interne cmdlets](modules.md#internal-cmdlets)voor meer informatie.

| Interne cmdlet | Description |
|:---|:---|
|`Get-AutomationCertificate`|Haalt een certificaat op dat moet worden gebruikt in een runbook-of DSC-configuratie. Retourneert een [System. Security. Cryptography. X509Certificates. X509Certificate2](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2) -object.|

> [!NOTE]
> Vermijd het gebruik van variabelen in de `Name` para meter van `Get-AutomationCertificate` in een RUNBOOK of DSC-configuratie. Dergelijke variabelen kunnen de detectie van afhankelijkheden tussen runbooks of DSC-configuraties en Automation-variabelen tijdens ontwerp tijd bemoeilijken.

## <a name="python-functions-to-access-certificates"></a>Python-functies voor toegang tot certificaten

Gebruik de functie in de volgende tabel voor toegang tot certificaten in een python 2-en 3-runbook. Python 3-runbooks zijn momenteel beschikbaar als preview-versie.

| Functie | Beschrijving |
|:---|:---|
| `automationassets.get_automation_certificate` | Haalt informatie op over een certificaat Asset. |

> [!NOTE]
> U moet de `automationassets` module op het begin van het python-runbook importeren om toegang te krijgen tot de Asset-functies.

## <a name="create-a-new-certificate"></a>Een nieuw certificaat maken

Wanneer u een nieuw certificaat maakt, uploadt u een CER-of PFX-bestand naar Automation. Als u het certificaat als exporteerbaar markeert, kunt u het uit het Automation-certificaat archief overzetten. Als de service niet kan worden geëxporteerd, kan deze alleen worden gebruikt voor ondertekening binnen het runbook of de DSC-configuratie. Voor Automation is vereist dat het certificaat de provider **micro soft Enhanced RSA en AES Cryptographic Provider** heeft.

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Een nieuw certificaat maken met de Azure Portal

1. Klik vanuit uw Automation-account in het linkerdeel venster op **certificaten** onder **gedeelde bron**.
1. Selecteer op de pagina **certificaten** de optie **een certificaat toevoegen**.
1. Typ in het veld **naam** een naam voor het certificaat.
1. Als u wilt bladeren naar een **CER** -of **PFX** -bestand, kiest u onder **een certificaat bestand uploaden** de optie **een bestand selecteren**. Als u een **PFX** -bestand selecteert, geeft u een wacht woord op en geeft u aan of het kan worden geëxporteerd.
1. Selecteer **maken** om het nieuwe certificaat activum op te slaan.

### <a name="create-a-new-certificate-with-powershell"></a>Een nieuw certificaat maken met Power shell

In het volgende voor beeld ziet u hoe u een nieuw Automation-certificaat maakt en dit markeert als exporteerbaar. In dit voor beeld wordt een bestaand **PFX** -bestand geïmporteerd.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath –Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Een nieuw certificaat maken met een resource manager-sjabloon

In het volgende voor beeld ziet u hoe u een certificaat implementeert in uw Automation-account met behulp van een resource manager-sjabloon via Power shell:

```powershell-interactive
$AutomationAccountName = "<automation account name>"
$PfxCertPath = '<PFX cert path and filename>'
$CertificatePassword = '<password>'
$certificateName = '<certificate name>' #A name of your choosing
$ResourceGroupName = '<resource group name>' #The one that holds your automation account
$flags = [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::Exportable `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::PersistKeySet `
    -bor [System.Security.Cryptography.X509Certificates.X509KeyStorageFlags]::MachineKeySet
# Load the certificate into memory
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPath, $CertificatePassword, $flags)
# Export the certificate and convert into base 64 string
$Base64Value = [System.Convert]::ToBase64String($PfxCert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12))
$Thumbprint = $PfxCert.Thumbprint


$json = @"
{
    '`$schema': 'https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#',
    'contentVersion': '1.0.0.0',
    'resources': [
        {
            'name': '$AutomationAccountName/$certificateName',
            'type': 'Microsoft.Automation/automationAccounts/certificates',
            'apiVersion': '2015-10-31',
            'properties': {
                'base64Value': '$Base64Value',
                'thumbprint': '$Thumbprint',
                'isExportable': true
            }
        }
    ]
}
"@

$json | out-file .\template.json
New-AzResourceGroupDeployment -Name NewCert -ResourceGroupName $ResourceGroupName -TemplateFile .\template.json
```

## <a name="get-a-certificate"></a>Een certificaat ophalen

Gebruik de interne cmdlet om een certificaat op te halen `Get-AutomationCertificate` . U kunt de cmdlet [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) niet gebruiken, omdat deze informatie over de certificaat Asset retourneert, maar niet het certificaat zelf.

### <a name="textual-runbook-examples"></a>Voor beelden van tekst-runbook

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

In het volgende voor beeld ziet u hoe u een certificaat toevoegt aan een Cloud service in een runbook. In dit voor beeld wordt het wacht woord opgehaald uit een versleutelde automatiserings variabele.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
–AutomationAccountName "MyAutomationAccount" –Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

# <a name="python-2"></a>[Python 2](#tab/python2)

Het volgende voor beeld laat zien hoe u toegang krijgt tot certificaten in Python 2-runbooks.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

# <a name="python-3"></a>[Python 3](#tab/python3)

Het volgende voor beeld laat zien hoe u toegang krijgt tot certificaten in Python 3-runbooks (preview-versie).

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print (cert)
```

---

### <a name="graphical-runbook-example"></a>Voor beeld van grafisch runbook

Voeg een activiteit voor de interne `Get-AutomationCertificate` cmdlet toe aan een grafisch runbook door met de rechter muisknop op het certificaat in het deel venster Bibliotheek te klikken en **toevoegen aan canvas** te selecteren.

![Scherm opname van het toevoegen van een certificaat aan het canvas](../media/certificates/automation-certificate-add-to-canvas.png)

In de volgende afbeelding ziet u een voor beeld van het gebruik van een certificaat in een grafisch runbook.

![Scherm afbeelding van een voor beeld van grafisch ontwerpen](../media/certificates/graphical-runbook-add-certificate.png)

## <a name="next-steps"></a>Volgende stappen

* Zie [modules beheren in azure Automation](modules.md)voor meer informatie over de cmdlets die worden gebruikt voor toegang tot certificaten.
* Zie voor algemene informatie over runbooks [Runbook-uitvoering in azure Automation](../automation-runbook-execution.md).
* Zie [Azure Automation status configuratie Overview](../automation-dsc-overview.md)(Engelstalig) voor meer informatie over DSC-configuraties.
