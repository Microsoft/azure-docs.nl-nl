---
title: Voer Azure Automation runbooks uit op een Hybrid Runbook Worker
description: In dit artikel wordt beschreven hoe u runbooks kunt uitvoeren op computers in uw lokale datacenter of een andere cloudprovider met de Hybrid Runbook Worker.
services: automation
ms.subservice: process-automation
ms.date: 03/10/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6501fd26a5c7966c4cde596df40346b106c57cce
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834782"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>Runbooks uitvoeren op een Hybrid Runbook Worker

Runbooks die worden uitgevoerd op [een Hybrid Runbook Worker](automation-hybrid-runbook-worker.md) beheren doorgaans resources op de lokale computer of op resources in de lokale omgeving waarin de werkrol is geïmplementeerd. Runbooks in Azure Automation beheren doorgaans resources in de Azure-cloud. Hoewel ze verschillend worden gebruikt, zijn runbooks die worden uitgevoerd in Azure Automation en runbooks die worden uitgevoerd op een Hybrid Runbook Worker identieke structuur.

Wanneer u een runbook ontwerpt om te worden uitgevoerd op een Hybrid Runbook Worker, moet u het runbook bewerken en testen op de computer waarop de werkmedewerker wordt uitgevoerd. De hostmachine beschikt over alle PowerShell-modules en netwerktoegang die vereist zijn voor het beheren van de lokale resources. Zodra u het runbook op de Hybrid Runbook Worker machine hebt getest, kunt u het uploaden naar de Azure Automation-omgeving, waar het kan worden uitgevoerd op de werker.

## <a name="plan-runbook-job-behavior"></a>Runbook-taakgedrag plannen

Azure Automation verwerkt taken in Hybrid Runbook Workers anders dan taken die worden uitgevoerd in Azure-sandboxes. Als u een langlopende runbook hebt, moet u ervoor zorgen dat het bestand is tegen mogelijk opnieuw opstarten. Zie taken voor meer informatie over [Hybrid Runbook Worker taakgedrag.](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs)

Taken voor Hybrid Runbook Workers worden uitgevoerd onder het lokale **systeemaccount** in Windows of **het nxautomation-account** in Linux. Voor Linux controleert u of **het nxautomation-account** toegang heeft tot de locatie waar de runbookmodules zijn opgeslagen. Wanneer u de [cmdlet Install-Module](/powershell/module/powershellget/install-module) gebruikt, moet u AllUsers opgeven voor de parameter om ervoor te zorgen dat het `Scope` **nxautomation-account** toegang heeft. Zie Bekende problemen voor PowerShell op [niet-Windows-platforms](/powershell/scripting/whats-new/what-s-new-in-powershell-70)voor meer informatie over PowerShell op Linux.

## <a name="configure-runbook-permissions"></a>Runbookmachtigingen configureren

Definieer machtigingen voor uw runbook om op de volgende Hybrid Runbook Worker uit te voeren:

* Zorg dat het runbook zijn eigen verificatie voor lokale resources biedt.
* Configureer verificatie met [behulp van beheerde identiteiten voor Azure-resources.](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)
* Geef een Uitvoeren als-account op om een gebruikerscontext voor alle runbooks op te geven.

### <a name="use-runbook-authentication-to-local-resources"></a>Runbookverificatie gebruiken voor lokale resources

Als u een runbook voorbereidt dat eigen verificatie voor resources biedt, gebruikt u [referenties](./shared-resources/credentials.md) en [certificaatactiva](./shared-resources/certificates.md) in uw runbook. Er zijn verschillende cmdlets waarmee u referenties kunt opgeven, zodat het runbook kan worden geverifieerd bij verschillende resources. In het volgende voorbeeld ziet u een deel van een runbook dat een computer opnieuw opstart. Deze haalt referenties op uit een referentie-asset en de naam van de computer uit een variabele asset en gebruikt deze waarden vervolgens met de `Restart-Computer` cmdlet .

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

U kunt ook een [InlineScript-activiteit](automation-powershell-workflow.md#use-inlinescript) gebruiken. `InlineScript` met kunt u codeblokken uitvoeren op een andere computer met referenties.

### <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>Runbookverificatie gebruiken met beheerde identiteiten

Hybrid Runbook Workers op virtuele Azure-machines kunnen beheerde identiteiten gebruiken om te verifiëren bij Azure-resources. Het gebruik van beheerde identiteiten voor Azure-resources in plaats van Uitvoeren als-accounts biedt voordelen omdat u het volgende niet hoeft te doen:

* Exporteer het Uitvoeren als-certificaat en importeer het in het Hybrid Runbook Worker.
* Vernieuw het certificaat dat wordt gebruikt door het Uitvoeren als-account.
* Verloop het Run As-verbindingsobject in uw runbookcode.

Volg de volgende stappen voor het gebruik van een beheerde identiteit voor Azure-resources op een Hybrid Runbook Worker:

1. Maak een Azure-VM.
1. Beheerde identiteiten configureren voor Azure-resources op de VM. Zie [Beheerde identiteiten configureren voor Azure-resources op een VM met behulp van de Azure Portal.](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
1. Geef de VM toegang tot een resourcegroep in Resource Manager. Raadpleeg Een [door het Windows-VM-systeem toegewezen beheerde](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)identiteit gebruiken voor toegang tot Resource Manager .
1. Installeer de Hybrid Runbook Worker op de VM. Zie Deploy a Windows Hybrid Runbook Worker or Deploy a Linux Hybrid Runbook Worker [(Een](automation-windows-hrw-install.md) [Windows-Hybrid Runbook Worker).](automation-linux-hrw-install.md)
1. Werk het runbook bij om de cmdlet [Connect-AzAccount te gebruiken](/powershell/module/az.accounts/connect-azaccount) met de `Identity` parameter om te verifiëren bij Azure-resources. Deze configuratie vermindert de noodzaak om een Uitvoeren als-account te gebruiken en het bijbehorende accountbeheer uit te voeren.

    ```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
    ```

    > [!NOTE]
    > `Connect-AzAccount -Identity` werkt voor een Hybrid Runbook Worker met behulp van een door het systeem toegewezen identiteit en één door de gebruiker toegewezen identiteit. Als u meerdere door de gebruiker toegewezen identiteiten op de Hybrid Runbook Worker gebruikt, moet uw runbook de parameter voor opgeven om een specifieke door de gebruiker toegewezen identiteit `AccountId` `Connect-AzAccount` te selecteren.

### <a name="use-runbook-authentication-with-run-as-account"></a>Runbookverificatie gebruiken met Een Uitvoeren als-account

In plaats van dat uw runbook eigen verificatie voor lokale resources biedt, kunt u een Uitvoeren als-account voor een Hybrid Runbook Worker opgeven. Als u een Uitvoeren als-account wilt opgeven, moet u een [referentie-asset](./shared-resources/credentials.md) definiëren die toegang heeft tot lokale resources. Deze resources omvatten certificaatopslag en alle runbooks worden onder deze referenties uitgevoerd op een Hybrid Runbook Worker in de groep.

- De gebruikersnaam voor de referentie moet een van de volgende indelingen hebben:

   * Domein\gebruikersnaam
   * username@domain
   * gebruikersnaam (voor accounts die lokaal zijn op de on-premises computer)

- Als u het **PowerShell-runbook Export-RunAsCertificateToHybridWorker** wilt gebruiken, moet u de Az-modules voor Azure Automation installeren op de lokale computer.

#### <a name="use-a-credential-asset-to-specify-a-run-as-account"></a>Een referentie-asset gebruiken om een Uitvoeren als-account op te geven

Gebruik de volgende procedure om een Uitvoeren als-account op te geven voor een Hybrid Runbook Worker groep:

1. Maak een [referentie-asset](./shared-resources/credentials.md) met toegang tot lokale resources.
1. Open het Automation-account in het Azure Portal.
1. Selecteer **Hybrid Worker groepen** en selecteer vervolgens de specifieke groep.
1. Selecteer **Alle instellingen,** gevolgd door instellingen **voor hybrid worker-groepen.**
1. Wijzig de waarde van **Uitvoeren als** van **Standaard** in **Aangepast.**
1. Selecteer de referentie en klik op **Opslaan.**

## <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>Uitvoeren als-accountcertificaat installeren

Als onderdeel van uw geautomatiseerde bouwproces voor het implementeren van resources in Azure, hebt u mogelijk toegang tot on-premises systemen nodig ter ondersteuning van een taak of een reeks stappen in uw implementatiereeks. Als u verificatie wilt bieden voor Azure met behulp van het Uitvoeren als-account, moet u het Uitvoeren als-accountcertificaat installeren.

>[!NOTE]
>Dit PowerShell-runbook wordt momenteel niet uitgevoerd op Linux-machines. Deze wordt alleen uitgevoerd op Windows-computers.
>

Het volgende PowerShell-runbook met de naam **Export-RunAsCertificateToHybridWorker** exporteert het Uitvoeren als-certificaat uit uw Azure Automation account. Het runbook downloadt en importeert het certificaat in het certificaatopslag van de lokale computer op een Hybrid Runbook Worker die is verbonden met hetzelfde account. Zodra deze stap is voltooid, controleert het runbook of de werkmedewerker kan worden geverifieerd bij Azure met behulp van het Uitvoeren als-account.

>[!NOTE]
>Dit PowerShell-runbook is niet ontworpen of bedoeld om buiten uw Automation-account te worden uitgevoerd als een script op de doelmachine.
>

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>Voor PowerShell-runbooks zijn `Add-AzAccount` en `Add-AzureRMAccount` aliassen voor `Connect-AzAccount`. Als u uw bibliotheekitems niet ziet, kunt u gebruiken of kunt u uw `Connect-AzAccount` modules bijwerken in uw `Add-AzAccount` Automation-account.

Het uitvoeren als-account voorbereiden:

1. Sla het **runbook Export-RunAsCertificateToHybridWorker** op uw computer op met de **extensie .ps1.**
1. Importeer deze in uw Automation-account.
1. Bewerk het runbook en wijzig de waarde van de `Password` variabele in uw eigen wachtwoord.
1. Publiceer het runbook.
1. Voer het runbook uit en richt Hybrid Runbook Worker op de groep die runbooks wordt uitgevoerd en geverifieerd met behulp van het Uitvoeren als-account. 
1. Bekijk de taakstroom om te zien dat de poging om het certificaat te importeren in het lokale computeropslag wordt vermeld, gevolgd door meerdere regels. Dit gedrag is afhankelijk van het aantal Automation-accounts dat u in uw abonnement definieert en de mate van succes van de verificatie.

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Werken met ondertekende runbooks op een Windows-Hybrid Runbook Worker

U kunt een Windows-Hybrid Runbook Worker om alleen ondertekende runbooks uit te voeren.

> [!IMPORTANT]
> Nadat u een configuratie hebt geconfigureerd Hybrid Runbook Worker alleen ondertekende runbooks uit te voeren, kunnen niet-ondertekende runbooks niet worden uitgevoerd op de werkwerker.

### <a name="create-signing-certificate"></a>Handtekeningcertificaat maken

In het volgende voorbeeld wordt een zelf-ondertekend certificaat gemaakt dat kan worden gebruikt voor het ondertekenen van runbooks. Met deze code maakt u het certificaat en exporteert u het, zodat Hybrid Runbook Worker het later kan importeren. De vingerafdruk wordt ook geretourneerd voor later gebruik bij het verwijzen naar het certificaat.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
    -Subject "CN=contoso.com" `
    -KeyAlgorithm RSA `
    -KeyLength 2048 `
    -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
    -KeyExportPolicy Exportable `
    -KeyUsage DigitalSignature `
    -Type CodeSigningCert

# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Certificaat importeren en werkmedewerkers configureren voor handtekeningvalidatie

Kopieer het certificaat dat u hebt gemaakt naar elk Hybrid Runbook Worker in een groep. Voer het volgende script uit om het certificaat te importeren en configureer de werkmedewerkers voor het gebruik van handtekeningvalidatie op runbooks.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Uw runbooks ondertekenen met behulp van het certificaat

Nu Hybrid Runbook Workers is geconfigureerd voor het gebruik van alleen ondertekende runbooks, moet u runbooks ondertekenen die moeten worden gebruikt op de Hybrid Runbook Worker. Gebruik de volgende PowerShell-voorbeeldcode om deze runbooks te ondertekenen.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Wanneer een runbook is ondertekend, moet u het importeren in uw Automation-account en publiceren met het handtekeningblok. Zie Een runbook importeren voor meer informatie over [het importeren van runbooks.](manage-runbooks.md#import-a-runbook)

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Werken met ondertekende runbooks op een Linux-Hybrid Runbook Worker

Als u wilt kunnen werken met ondertekende runbooks, moet een Linux-Hybrid Runbook Worker [het uitvoerbare GPG-bestand](https://gnupg.org/index.html) op de lokale computer hebben.

> [!IMPORTANT]
> Nadat u een configuratie hebt geconfigureerd Hybrid Runbook Worker alleen ondertekende runbooks uit te voeren, kunnen niet-ondertekende runbooks niet worden uitgevoerd op de werkwerker.

U voert de volgende stappen uit om deze configuratie te voltooien:

* Een GPG-sleutelring en keypair maken
* De sleutelhanger beschikbaar maken voor de Hybrid Runbook Worker
* Controleer of handtekeningvalidatie is aan
* Een runbook ondertekenen

### <a name="create-a-gpg-keyring-and-keypair"></a>Een GPG-sleutelring en keypair maken

Als u de GPG-sleutelring en keypair wilt maken, gebruikt u het [Hybrid Runbook Worker nxautomation-account](automation-runbook-execution.md#log-analytics-agent-for-linux).

1. Gebruik de sudo-toepassing om u aan te melden als **het nxautomation-account.**

    ```bash
    sudo su - nxautomation
    ```

1. Zodra u **nxautomation gebruikt, genereert** u de GPG-sleutelpair. GPG leidt u door de stappen. U moet naam, e-mailadres, verlooptijd en wachtwoordzin verstrekken. Vervolgens wacht u totdat er voldoende entropie op de computer is om de sleutel te genereren.

    ```bash
    sudo gpg --generate-key
    ```

1. Omdat de GPG-map is gegenereerd met sudo, moet u de eigenaar ervan **wijzigen in nxautomation** met behulp van de volgende opdracht.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>De sleutelhanger beschikbaar maken voor de Hybrid Runbook Worker

Zodra de sleutelhanger is gemaakt, maakt u deze beschikbaar voor de Hybrid Runbook Worker. Wijzig het instellingenbestand **home/nxautomation/state/worker.conf** om de volgende voorbeeldcode op te nemen onder de bestandssectie `[worker-optional]` .

```bash
gpg_public_keyring_path = /home/nxautomation/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Controleer of handtekeningvalidatie is aan

Als handtekeningvalidatie is uitgeschakeld op de computer, moet u deze in- en uit te voeren met de volgende sudo-opdracht. Vervang `<LogAnalyticsworkspaceId>` door uw werkruimte-id.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Een runbook ondertekenen

Nadat u handtekeningvalidatie hebt geconfigureerd, gebruikt u de volgende GPG-opdracht om het runbook te ondertekenen.

```bash
gpg --clear-sign <runbook name>
```

Het ondertekende runbook heet **<runbook name> .asc.**

U kunt nu het ondertekende runbook uploaden naar Azure Automation uitvoeren als een gewoon runbook.

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>Een runbook starten op een Hybrid Runbook Worker

[Een runbook starten in Azure Automation](start-runbooks.md) beschrijft verschillende methoden voor het starten van een runbook. Bij het starten van een runbook Hybrid Runbook Worker een optie Uitvoeren **op** gebruikt waarmee u de naam van een Hybrid Runbook Worker opgeven. Wanneer een groep is opgegeven, haalt een van de werksters in die groep het runbook op en voert het uit. Als uw runbook deze optie niet opgeeft, Azure Automation runbook op de gebruikelijke manier uitgevoerd.

Wanneer u een runbook in de Azure Portal start,  krijgt u de optie Uitvoeren op te zien waarvoor u **Azure** of **Hybrid Worker.** Als u **Hybrid Worker** selecteert, kunt u de Hybrid Runbook Worker selecteren in een vervolgkeuzekeuze.

Wanneer u een runbook start met behulp van PowerShell, gebruikt u de `RunOn` parameter met de cmdlet [Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook) In het volgende voorbeeld wordt Windows PowerShell runbook met de naam **Test-Runbook** te starten op een Hybrid Runbook Worker met de naam MyHybridGroup.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -RunOn "MyHybridGroup"
```

## <a name="logging"></a>Logboekregistratie

Ter hulp bij het oplossen van problemen met uw runbooks die worden uitgevoerd op een hybride runbook worker, worden logboeken lokaal opgeslagen op de volgende locatie:

* In Windows op voor `C:\ProgramData\Microsoft\System Center\Orchestrator\<version>\SMA\Sandboxes` gedetailleerde logboekregistratie van het runtimeproces van de taak. Statusgebeurtenissen van runbook-taak op hoog niveau worden geschreven naar het gebeurtenislogboek **Application and Services Logs\Microsoft-Automation\Operations.**

* In Linux kunt u de hybrid worker-logboeken van de gebruiker vinden op `/home/nxautomation/run/worker.log` en runbook worker systeemlogboeken vindt u op `/var/opt/microsoft/omsagent/run/automationworker/worker.log` .

## <a name="next-steps"></a>Volgende stappen

* Als uw runbooks niet worden voltooid, bekijkt u de gids voor probleemoplossing voor [runbookuitvoeringsfouten.](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)
* Zie PowerShell Docs voor meer informatie over PowerShell, inclusief taalverwijzing en [leermodules.](/powershell/scripting/overview)
* Zie [Az.Automation](/powershell/module/az.automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
