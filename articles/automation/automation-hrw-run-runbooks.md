---
title: Azure Automation runbooks uitvoeren op een Hybrid Runbook Worker
description: In dit artikel wordt beschreven hoe u runbooks uitvoert op machines in uw lokale Data Center of een andere Cloud provider met de Hybrid Runbook Worker.
services: automation
ms.subservice: process-automation
ms.date: 10/06/2020
ms.topic: conceptual
ms.openlocfilehash: b73fa80085cc0491c8b4d0856d0baacfef1b51c0
ms.sourcegitcommit: ab94795f9b8443eef47abae5bc6848bb9d8d8d01
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/27/2020
ms.locfileid: "96301536"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>Runbooks uitvoeren op een Hybrid Runbook Worker

Runbooks die worden uitgevoerd op een [Hybrid Runbook worker](automation-hybrid-runbook-worker.md) beheren meestal resources op de lokale computer of op basis van resources in de lokale omgeving waar de werk nemer wordt geïmplementeerd. Runbooks in Azure Automation beheren resources doorgaans in de Azure-Cloud. Hoewel ze anders worden gebruikt, zijn runbooks die worden uitgevoerd in Azure Automation en runbooks die worden uitgevoerd op een Hybrid Runbook Worker, in de structuur identiek.

Wanneer u een runbook ontwerpt om uit te voeren op een Hybrid Runbook Worker, moet u het runbook bewerken en testen op de computer die als host fungeert voor de werk nemer. De hostcomputer heeft alle Power shell-modules en netwerk toegang die vereist zijn voor het beheren van de lokale resources. Wanneer u het runbook op de Hybrid Runbook Worker machine hebt getest, kunt u het op de Azure Automation omgeving uploaden, waar het op de werk nemer kan worden uitgevoerd.

## <a name="plan-runbook-job-behavior"></a>Gedrag van runbook-taken plannen

Azure Automation verwerkt taken op Hybrid Runbook Workers anders dan taken die worden uitgevoerd in azure-sandboxes. Als u een langlopend runbook hebt, moet u ervoor zorgen dat het bestand kan worden opgestart. Zie [Hybrid Runbook worker Jobs](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs)voor meer informatie over het gedrag van taken.

Taken voor Hybrid Runbook Workers worden uitgevoerd onder het lokale **systeem** account in Windows of het **nxautomation** -account in Linux. Controleer voor Linux of het **nxautomation** -account toegang heeft tot de locatie waar de runbook-modules zijn opgeslagen. Wanneer u de cmdlet [install-module](/powershell/module/powershellget/install-module) gebruikt, moet u ALLUSERS opgeven voor de `Scope` para meter om er zeker van te zijn dat het **nxautomation** -account toegang heeft. Zie voor meer informatie over Power shell op Linux [bekende problemen voor Power shell op niet-Windows-platforms](/powershell/scripting/whats-new/what-s-new-in-powershell-70?view=powershell-7.1).

## <a name="configure-runbook-permissions"></a>Runbook-machtigingen configureren

Definieer de machtigingen voor uw runbook om op de volgende manieren te worden uitgevoerd op de Hybrid Runbook Worker:

* Zorg ervoor dat het runbook een eigen verificatie voor lokale bronnen biedt.
* Configureer verificatie met behulp [van beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
* Geef een uitvoeren als-account op om een gebruikers context te bieden voor alle runbooks.

### <a name="use-runbook-authentication-to-local-resources"></a>Runbook-verificatie gebruiken voor lokale bronnen

Als u een runbook voorbereidt dat een eigen verificatie voor bronnen biedt, gebruikt u [referentie](./shared-resources/credentials.md) -en [certificaat](./shared-resources/certificates.md) assets in uw runbook. Er zijn verschillende cmdlets waarmee u referenties kunt opgeven, zodat het runbook kan worden geverifieerd bij verschillende bronnen. In het volgende voor beeld ziet u een deel van een runbook waarmee een computer opnieuw wordt opgestart. De referenties worden opgehaald uit een referentie-element en de naam van de computer van een variabele Asset en gebruiken deze waarden vervolgens met de `Restart-Computer` cmdlet.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

U kunt ook een [InlineScript](automation-powershell-workflow.md#use-inlinescript) -activiteit gebruiken. `InlineScript` Hiermee kunt u code blokken op een andere computer met referenties uitvoeren.

### <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>Runbook-verificatie gebruiken met beheerde identiteiten

Hybrid Runbook Workers op virtuele machines van Azure kunnen beheerde identiteiten gebruiken om te verifiëren bij Azure-resources. Het gebruik van beheerde identiteiten voor Azure-resources in plaats van run as-accounts biedt voor delen, omdat u niet hoeft te doen:

* Exporteer het run as-certificaat en importeer het vervolgens in de Hybrid Runbook Worker.
* Vernieuw het certificaat dat wordt gebruikt door het run as-account.
* Het run as-verbindings object in uw runbook-code verwerken.

Volg de volgende stappen om een beheerde identiteit voor Azure-resources te gebruiken op een Hybrid Runbook Worker:

1. Maak een Azure VM.
2. Configureer beheerde identiteiten voor Azure-resources op de VM. Zie [beheerde identiteiten voor Azure-resources configureren op een virtuele machine met behulp van de Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
3. Geef de virtuele machine toegang tot een resource groep in Resource Manager. Raadpleeg [een door Windows-VM-systeem toegewezen beheerde identiteit gebruiken om toegang te krijgen tot Resource Manager](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. Installeer de Hybrid Runbook Worker op de VM. Zie [een Windows-Hybrid Runbook worker implementeren](automation-windows-hrw-install.md) of [een Linux-Hybrid Runbook worker implementeren](automation-linux-hrw-install.md).
5. Werk het runbook bij om de cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) te gebruiken met de `Identity` para meter voor verificatie bij Azure-resources. Deze configuratie vermindert de nood zaak om een uitvoeren als-account te gebruiken en het bijbehorende account beheer uit te voeren.

    ```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
    ```

    > [!NOTE]
    > `Connect-AzAccount -Identity` werkt voor een Hybrid Runbook Worker met behulp van een door het systeem toegewezen identiteit en één door de gebruiker toegewezen identiteit. Als u meerdere door de gebruiker toegewezen identiteiten gebruikt op de Hybrid Runbook Worker, moet uw Runbook de `AccountId` para meter opgeven `Connect-AzAccount` om een specifieke door de gebruiker toegewezen identiteit te selecteren.

### <a name="use-runbook-authentication-with-run-as-account"></a>Runbook-verificatie gebruiken met het run as-account

In plaats van uw runbook zelf verificatie te bieden voor lokale resources, kunt u een uitvoeren als-account opgeven voor een Hybrid Runbook Worker groep. Als u een uitvoeren als-account wilt opgeven, moet u een [referentie-element](./shared-resources/credentials.md) definiëren dat toegang heeft tot lokale bronnen. Deze resources omvatten certificaat archieven en alle runbooks worden onder deze referenties uitgevoerd op een Hybrid Runbook Worker in de groep.

De gebruikers naam voor de referentie moet een van de volgende indelingen hebben:

* vorm
* username@domain
* gebruikers naam (voor accounts lokaal op de on-premises computer)

Gebruik de volgende procedure om een uitvoeren als-account op te geven voor een Hybrid Runbook Worker groep:

1. Maak een [referentie-element](./shared-resources/credentials.md) met toegang tot lokale bronnen.
2. Open het Automation-account in de Azure Portal.
3. Selecteer **Hybrid worker groepen** en selecteer vervolgens de specifieke groep.
4. Selecteer **alle instellingen**, gevolgd door de instellingen van de **Hybrid worker-groep**.
5. Wijzig de waarde van **uitvoeren als** van **standaard** in **aangepast**.
6. Selecteer de referentie en klik op **Opslaan**.

## <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>Het certificaat van het uitvoeren als-account installeren

Als onderdeel van uw geautomatiseerde bouw proces voor het implementeren van resources in azure, hebt u mogelijk toegang tot on-premises systemen nodig ter ondersteuning van een taak of een reeks stappen in uw implementatie reeks. Als u verificatie wilt uitvoeren voor Azure met behulp van het uitvoeren als-account, moet u het certificaat van het uitvoeren als-account installeren.

Het volgende Power shell-runbook, met de naam **export-RunAsCertificateToHybridWorker**, exporteert het run as-certificaat uit uw Azure Automation-account. Het runbook wordt gedownload en geïmporteerd in het certificaat archief van de lokale computer op een Hybrid Runbook Worker dat is verbonden met hetzelfde account. Zodra deze stap is voltooid, controleert het runbook of de werk nemer kan worden geverifieerd bij Azure met behulp van het run as-account.

>[!NOTE]
>Dit Power shell-runbook is niet ontworpen of bedoeld om te worden uitgevoerd buiten uw Automation-account als een script op de doel computer.
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
>Voor PowerShell-runbooks zijn `Add-AzAccount` en `Add-AzureRMAccount` aliassen voor `Connect-AzAccount`. Als u uw bibliotheek items zoekt, kunt u, als u deze niet ziet `Connect-AzAccount` , gebruiken `Add-AzAccount` , of u kunt uw modules bijwerken in uw Automation-account.

De voor bereiding van het uitvoeren als-account volt ooien:

1. Sla het runbook **export-RunAsCertificateToHybridWorker** op uw computer op met de extensie **. ps1** .
2. Importeer deze in uw Automation-account.
3. Bewerk het runbook en wijzig de waarde van de `Password` variabele in uw eigen wacht woord.
4. Publiceer het runbook.
5. Voer het runbook uit om te richten op de Hybrid Runbook Worker groep die runbooks uitvoert en verifieert met behulp van het run as-account. 
6. Controleer de taak stroom om te zien dat de poging om het certificaat te importeren in het archief van de lokale computer, gevolgd door meerdere regels. Dit gedrag is afhankelijk van het aantal Automation-accounts dat u in uw abonnement hebt gedefinieerd en de mate van succes van de verificatie.

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Werken met ondertekende runbooks op een Windows-Hybrid Runbook Worker

U kunt een Windows-Hybrid Runbook Worker zodanig configureren dat alleen ondertekende runbooks worden uitgevoerd.

> [!IMPORTANT]
> Zodra u een Hybrid Runbook Worker hebt geconfigureerd voor het uitvoeren van alleen ondertekende runbooks, kan niet-ondertekende runbooks niet worden uitgevoerd op de werk nemer.

### <a name="create-signing-certificate"></a>Handtekening certificaat maken

In het volgende voor beeld wordt een zelfondertekend certificaat gemaakt dat kan worden gebruikt voor het ondertekenen van runbooks. Deze code maakt het certificaat en exporteert dit zodat de Hybrid Runbook Worker het later kan importeren. De vinger afdruk wordt ook geretourneerd voor later gebruik in de verwijzing naar het certificaat.

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

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Certificaat importeren en werk nemers configureren voor handtekening validatie

Kopieer het certificaat dat u hebt gemaakt naar elke Hybrid Runbook Worker in een groep. Voer het volgende script uit om het certificaat te importeren en configureer de werk nemers om handtekening validatie op runbooks te gebruiken.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Uw runbooks ondertekenen met het certificaat

Met de Hybrid Runbook workers die zijn geconfigureerd voor het gebruik van alleen ondertekende runbooks, moet u runbooks ondertekenen die moeten worden gebruikt op de Hybrid Runbook Worker. Gebruik de volgende Power shell-voorbeeld code om deze runbooks te ondertekenen.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Wanneer een runbook is ondertekend, moet u deze importeren in uw Automation-account en dit publiceren met het handtekening blok. Zie [een Runbook importeren](manage-runbooks.md#import-a-runbook)voor meer informatie over het importeren van runbooks.

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Werken met ondertekende runbooks op een Linux-Hybrid Runbook Worker

Om te kunnen werken met ondertekende runbooks, moet een Linux-Hybrid Runbook Worker het uitvoer bare bestand [gpg](https://gnupg.org/index.html) op de lokale computer hebben.

> [!IMPORTANT]
> Zodra u een Hybrid Runbook Worker hebt geconfigureerd voor het uitvoeren van alleen ondertekende runbooks, kan niet-ondertekende runbooks niet worden uitgevoerd op de werk nemer.

U voert de volgende stappen uit om deze configuratie te volt ooien:

* Een GPG-sleutel hanger en-sleutel paar maken
* De sleutel hanger beschikbaar maken voor de Hybrid Runbook Worker
* Controleren of de handtekening validatie is ingeschakeld
* Een runbook ondertekenen

### <a name="create-a-gpg-keyring-and-keypair"></a>Een GPG-sleutel hanger en-sleutel paar maken

Als u de GPG sleutel hanger en het sleutel paar wilt maken, gebruikt u het [account Hybrid Runbook worker nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux).

1. Gebruik de toepassing sudo om u aan te melden als het **nxautomation** -account.

    ```bash
    sudo su – nxautomation
    ```

2. Wanneer u **nxautomation** gebruikt, genereert u het GPG-sleutel paar. GPG begeleidt u bij de stappen. U moet een naam, e-mail adres, verval tijd en wachtwoordzin opgeven. Vervolgens wacht u tot er voldoende entropie op de computer is om de sleutel te genereren.

    ```bash
    sudo gpg --generate-key
    ```

3. Omdat de GPG-map is gegenereerd met sudo, moet u de eigenaar ervan wijzigen in **nxautomation** met behulp van de volgende opdracht.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>De sleutel hanger beschikbaar maken voor de Hybrid Runbook Worker

Zodra de sleutel hanger is gemaakt, kunt u deze beschikbaar maken voor de Hybrid Runbook Worker. Wijzig het instellingen bestand **Home/nxautomation/State/Worker. conf** zodat de volgende voorbeeld code wordt vermeld in de sectie file `[worker-optional]` .

```bash
gpg_public_keyring_path = /home/nxautomation/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Controleren of de handtekening validatie is ingeschakeld

Als handtekening validatie is uitgeschakeld op de computer, moet u deze inschakelen door de volgende sudo-opdracht uit te voeren. Vervang door `<LogAnalyticsworkspaceId>` de werk ruimte-id.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Een runbook ondertekenen

Nadat u de handtekening validatie hebt geconfigureerd, gebruikt u de volgende GPG-opdracht om het runbook te ondertekenen.

```bash
gpg –-clear-sign <runbook name>
```

Het ondertekende runbook heet **<runbook name> . asc**.

U kunt nu het ondertekende runbook uploaden naar Azure Automation en dit uitvoeren als een normaal runbook.

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>Een runbook starten op een Hybrid Runbook Worker

[Een Runbook starten in azure Automation](start-runbooks.md) worden verschillende methoden beschreven voor het starten van een runbook. Bij het starten van een runbook op een Hybrid Runbook Worker wordt een optie **uitvoeren op** gebruikt waarmee u de naam van een Hybrid Runbook worker groep kunt opgeven. Wanneer een groep is opgegeven, haalt een van de werk nemers in die groep het runbook op en voert deze uit. Als uw runbook deze optie niet opgeeft, wordt het runbook op de gebruikelijke manier door Azure Automation uitgevoerd.

Wanneer u een runbook in de Azure Portal start, wordt de optie **uitvoeren op** weer gegeven waarvoor u **Azure** of **Hybrid worker** kunt selecteren. Als u **Hybrid worker** selecteert, kunt u de Hybrid Runbook worker groep kiezen in een vervolg keuzelijst.

Wanneer u een runbook start met behulp van Power shell, gebruikt u de `RunOn` para meter met de cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) . In het volgende voor beeld wordt Windows Power shell gebruikt om een runbook met de naam **test-runbook** te starten voor een Hybrid Runbook worker groep met de naam MyHybridGroup.

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

## <a name="logging"></a>Logboekregistratie

Voor hulp bij het oplossen van problemen met uw runbooks die worden uitgevoerd op hybride runbook worker, worden logboeken lokaal opgeslagen op de volgende locatie:

* In Windows op `C:\ProgramData\Microsoft\System Center\Orchestrator\<version>\SMA\Sandboxes` voor gedetailleerde proces logboek registratie van de taak runtime. Taak status gebeurtenissen op hoog niveau worden naar het gebeurtenis logboek van de **toepassing en het Services Logs\Microsoft-Automation\Operations** geschreven.

* In Linux kunt u de logboeken van de gebruiker Hybrid worker vinden op `/home/nxautomation/run/worker.log` en kunnen de logboeken voor systeem runbook worker worden gevonden op `/var/opt/microsoft/omsagent/run/automationworker/worker.log` .

## <a name="next-steps"></a>Volgende stappen

* Als uw runbooks niet met succes worden voltooid, raadpleegt u de hand leiding voor het oplossen van de [runbook-uitvoerings fouten](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Zie [Power shell docs](/powershell/scripting/overview)(Engelstalig) voor meer informatie over Power shell, inclusief taal referentie-en leer modules.
* Zie [Az.Automation](/powershell/module/az.automation#automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
