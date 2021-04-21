---
title: Certificaten beheren in Azure Automation
description: In dit artikel wordt beschreven hoe u kunt werken met certificaten voor toegang door runbooks en DSC-configuraties.
services: automation
ms.subservice: shared-capabilities
ms.date: 12/22/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 788f15cd1edad228e695e6e87f5b630b8e4fdf55
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834440"
---
# <a name="manage-certificates-in-azure-automation"></a>Certificaten beheren in Azure Automation

Azure Automation worden certificaten veilig opgeslagen voor toegang door runbooks en DSC-configuraties, met behulp van de cmdlet [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) voor Azure Resource Manager resources. Met beveiligde certificaatopslag kunt u runbooks en DSC-configuraties maken die gebruikmaken van certificaten voor verificatie of deze toevoegen aan Azure- of resources van derden.

>[!NOTE]
>Beveilig assets in Azure Automation referenties, certificaten, verbindingen en versleutelde variabelen. Deze assets worden versleuteld en opgeslagen in Automation met behulp van een unieke sleutel die wordt gegenereerd voor elk Automation-account. Automation slaat de sleutel op in de door het systeem beheerde Key Vault service. Voordat een beveiligde asset wordt opgeslagen, laadt Automation de sleutel Key Vault gebruikt om de asset te versleutelen.

## <a name="powershell-cmdlets-to-access-certificates"></a>PowerShell-cmdlets voor toegang tot certificaten

De cmdlets in de volgende tabel maken en beheren Automation-certificaten met PowerShell. Ze worden als onderdeel van de [Az-modules verzenden.](modules.md#az-modules)

|Cmdlet |Beschrijving|
| --- | ---|
|[Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate)|Hiermee wordt informatie opgehaald over een certificaat dat moet worden gebruikt in een runbook- of DSC-configuratie. U kunt het certificaat zelf alleen ophalen met behulp van de interne `Get-AutomationCertificate` cmdlet .|
|[New-AzAutomationCertificate](/powershell/module/Az.Automation/New-AzAutomationCertificate)|Hiermee maakt u een nieuw certificaat in Automation.|
|[Remove-AzAutomationCertificate](/powershell/module/Az.Automation/Remove-AzAutomationCertificate)|Hiermee verwijdert u een certificaat uit Automation.|
|[Set-AzAutomationCertificate](/powershell/module/Az.Automation/Set-AzAutomationCertificate)|Hiermee stelt u de eigenschappen voor een bestaand certificaat in, waaronder het uploaden van het certificaatbestand en het instellen van het wachtwoord voor een **PFX-bestand.**|

De [cmdlet Add-AzureCertificate](/powershell/module/servicemanagement/azure.service/add-azurecertificate) kan ook worden gebruikt om een servicecertificaat voor de opgegeven cloudservice te uploaden.

## <a name="internal-cmdlets-to-access-certificates"></a>Interne cmdlets voor toegang tot certificaten

De interne cmdlet in de volgende tabel wordt gebruikt voor toegang tot certificaten in uw runbooks. Deze cmdlet wordt geleverd met de globale module `Orchestrator.AssetManagement.Cmdlets` . Zie Interne [cmdlets voor meer informatie.](modules.md#internal-cmdlets)

| Interne cmdlet | Description |
|:---|:---|
|`Get-AutomationCertificate`|Haalt een certificaat op dat moet worden gebruikt in een runbook- of DSC-configuratie. Retourneert [een System.Security.Cryptography.X509Certificates.X509Certificate2-object.](/dotnet/api/system.security.cryptography.x509certificates.x509certificate2)|

> [!NOTE]
> Vermijd het gebruik van variabelen in de `Name` parameter van in een runbook- of `Get-AutomationCertificate` DSC-configuratie. Dergelijke variabelen kunnen de detectie van afhankelijkheden tussen runbooks of DSC-configuraties en Automation-variabelen tijdens de ontwerptijd bemoeilijken.

## <a name="python-functions-to-access-certificates"></a>Python-functies voor toegang tot certificaten

Gebruik de functie in de volgende tabel voor toegang tot certificaten in een Python 2- en 3-runbook. Python 3-runbooks zijn momenteel beschikbaar als preview-versie.

| Functie | Beschrijving |
|:---|:---|
| `automationassets.get_automation_certificate` | Hiermee wordt informatie over een certificaatactivum opgehaald. |

> [!NOTE]
> U moet de `automationassets` module aan het begin van uw Python-runbook importeren om toegang te krijgen tot de assetfuncties.

## <a name="create-a-new-certificate"></a>Een nieuw certificaat maken

Wanneer u een nieuw certificaat maakt, uploadt u een CER- of PFX-bestand naar Automation. Als u het certificaat als exporteerbaar markeren, kunt u het overdragen uit het Automation-certificaatopslag. Als het niet exporteerbaar is, kan het alleen worden gebruikt voor ondertekening binnen het runbook of de DSC-configuratie. Automatisering vereist dat het certificaat de provider **Microsoft Enhanced RSA en AES Cryptographic Provider heeft.**

### <a name="create-a-new-certificate-with-the-azure-portal"></a>Maak een nieuw certificaat met de Azure Portal

1. Selecteer in uw Automation-account in het linkerdeelvenster **Certificaten onder** **Gedeelde resource.**
1. Selecteer op **de pagina Certificaten** de optie Een certificaat **toevoegen.**
1. Typ in **het** veld Naam een naam voor het certificaat.
1. Als u wilt zoeken naar **een CER-** of **PFX-bestand,** kiest u onder Een **certificaatbestand uploaden** de optie **Een bestand selecteren.** Als u een **PFX-bestand selecteert,** geeft u een wachtwoord op en geeft u aan of het kan worden geëxporteerd.
1. Selecteer **Maken om** het nieuwe certificaatactivum op te slaan.

### <a name="create-a-new-certificate-with-powershell"></a>Een nieuw certificaat maken met PowerShell

In het volgende voorbeeld wordt gedemonstreerd hoe u een nieuw Automation-certificaat maakt en exporteerbaar markeert. In dit voorbeeld wordt een bestaand **PFX-bestand** geïmporteerd.

```powershell-interactive
$certificateName = 'MyCertificate'
$PfxCertPath = '.\MyCert.pfx'
$CertificatePassword = ConvertTo-SecureString -String 'P@$$w0rd' -AsPlainText -Force
$ResourceGroup = "ResourceGroup01"

New-AzAutomationCertificate -AutomationAccountName "MyAutomationAccount" -Name $certificateName -Path $PfxCertPath -Password $CertificatePassword -Exportable -ResourceGroupName $ResourceGroup
```

### <a name="create-a-new-certificate-with-a-resource-manager-template"></a>Een nieuw certificaat maken met een Resource Manager sjabloon

In het volgende voorbeeld wordt gedemonstreerd hoe u een certificaat implementeert in uw Automation-account met behulp van een Resource Manager via PowerShell:

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

## <a name="get-a-certificate"></a>Een certificaat verkrijgen

Gebruik de interne cmdlet om een `Get-AutomationCertificate` certificaat op te halen. U kunt de cmdlet [Get-AzAutomationCertificate](/powershell/module/Az.Automation/Get-AzAutomationCertificate) niet gebruiken, omdat deze informatie retourneert over het certificaatactivum, maar niet over het certificaat zelf.

### <a name="textual-runbook-examples"></a>Voorbeelden van tekstrunbook

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

In het volgende voorbeeld ziet u hoe u een certificaat toevoegt aan een cloudservice in een runbook. In dit voorbeeld wordt het wachtwoord opgehaald uit een versleutelde automatiseringsvariabele.

```powershell-interactive
$serviceName = 'MyCloudService'
$cert = Get-AutomationCertificate -Name 'MyCertificate'
$certPwd = Get-AzAutomationVariable -ResourceGroupName "ResourceGroup01" `
-AutomationAccountName "MyAutomationAccount" -Name 'MyCertPassword'
Add-AzureCertificate -ServiceName $serviceName -CertToDeploy $cert
```

# <a name="python-2"></a>[Python 2](#tab/python2)

In het volgende voorbeeld ziet u hoe u toegang krijgt tot certificaten in Python 2-runbooks.

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print cert
```

# <a name="python-3"></a>[Python 3](#tab/python3)

In het volgende voorbeeld ziet u hoe u toegang krijgt tot certificaten in Python 3-runbooks (preview).

```python
# get a reference to the Azure Automation certificate
cert = automationassets.get_automation_certificate("AzureRunAsCertificate")

# returns the binary cert content  
print (cert)
```

---

### <a name="graphical-runbook-example"></a>Voorbeeld van grafisch runbook

Voeg een activiteit voor de interne cmdlet toe aan een grafisch runbook door met de rechtermuisknop op het certificaat in het deelvenster Bibliotheek te klikken en Toevoegen aan `Get-AutomationCertificate` **canvas te selecteren.**

![Schermopname van het toevoegen van een certificaat aan het canvas](../media/certificates/automation-certificate-add-to-canvas.png)

In de volgende afbeelding ziet u een voorbeeld van het gebruik van een certificaat in een grafisch runbook.

![Schermopname van een voorbeeld van grafisch ontwerp](../media/certificates/graphical-runbook-add-certificate.png)

## <a name="next-steps"></a>Volgende stappen

* Zie Modules beheren in Azure Automation voor meer informatie over de cmdlets die worden gebruikt voor toegang [tot Azure Automation.](modules.md)
* Zie Runbookuitvoering in Azure Automation voor algemene [informatie over runbooks.](../automation-runbook-execution.md)
* Zie overzicht van Azure Automation State Configuration voor [meer informatie Azure Automation State Configuration DSC-configuraties.](../automation-dsc-overview.md)
