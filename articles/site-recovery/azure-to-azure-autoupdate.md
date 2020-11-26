---
title: Automatische update van de Mobility-service in Azure Site Recovery
description: Overzicht van de automatische update van de Mobility-service bij het repliceren van virtuele Azure-machines met behulp van Azure Site Recovery.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: sideeksh
ms.openlocfilehash: 42d3c74229ab7eeec0ac716073a9e631775fd002
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187337"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatische update van de Mobility-service in azure-naar-Azure-replicatie

Azure Site Recovery maakt gebruik van een maandelijkse release-uitgebracht om eventuele problemen op te lossen en bestaande functies te verbeteren of nieuwe onderdelen toe te voegen. Als u actueel wilt blijven met de service, moet u elke maand een patch implementatie plannen. Als u wilt voor komen dat de overhead aan elke upgrade is gekoppeld, kunt u Site Recovery toestaan onderdeel updates te beheren.

Zoals vermeld in [Azure-to-Azure-nood herstel architectuur](azure-to-azure-architecture.md), wordt de Mobility-service geïnstalleerd op alle Azure virtual machines (vm's) waarvoor replicatie van de ene Azure-regio naar de andere is ingeschakeld. Wanneer u automatische updates gebruikt, werkt elke nieuwe release de Mobility service-extensie bij.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Hoe werkt automatische updates?

Wanneer u Site Recovery gebruikt voor het beheren van updates, implementeert het een globaal runbook (gebruikt door Azure-Services) via een Automation-account dat is gemaakt in hetzelfde abonnement als de kluis. Elke kluis gebruikt één Automation-account. Voor elke VM in een kluis controleert het runbook of er actieve automatische updates zijn. Als er een nieuwere versie van de Mobility service-extensie beschikbaar is, wordt de update geïnstalleerd.

Het standaard runbook-schema wordt dagelijks om 12:00 uur in de tijd zone van de Geografie van de gerepliceerde VM weer gegeven. U kunt het runbook-schema ook wijzigen via het Automation-account.

> [!NOTE]
> Vanaf [Update pakket 35](site-recovery-whats-new.md#updates-march-2019)kunt u een bestaand Automation-account kiezen dat u wilt gebruiken voor updates. Vóór update pakket 35 heeft Site Recovery standaard het Automation-account gemaakt. U kunt deze optie alleen selecteren wanneer u replicatie voor een virtuele machine inschakelt. Het is niet beschikbaar voor een virtuele machine waarop replicatie al is ingeschakeld. De instelling die u selecteert, is van toepassing op alle virtuele Azure-machines die in dezelfde kluis worden beveiligd.

Bij het inschakelen van automatische updates is het niet nodig om uw virtuele Azure-machines opnieuw op te starten of de actieve replicatie te beïnvloeden.

De facturering van taken in het Automation-account is gebaseerd op het aantal taak runtime minuten dat in een maand is gebruikt. Het uitvoeren van de taak duurt een paar seconden tot ongeveer een minuut en wordt gedekt als gratis eenheden. Standaard worden 500 minuten opgenomen als gratis eenheden voor een Automation-account, zoals wordt weer gegeven in de volgende tabel:

| Gratis eenheden inbegrepen (elke maand) | Prijs |
|---|---|
| Taak runtime 500 minuten | ₹ 0.14/minuut

## <a name="enable-automatic-updates"></a>Automatische updates inschakelen

Er zijn verschillende manieren waarop Site Recovery de extensie-updates kunt beheren:

- [Beheren als onderdeel van de stap replicatie inschakelen](#manage-as-part-of-the-enable-replication-step)
- [De instellingen voor het bijwerken van de extensie in de kluis in-/uitschakelen](#toggle-the-extension-update-settings-inside-the-vault)
- [Updates hand matig beheren](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Beheren als onderdeel van de stap replicatie inschakelen

Wanneer u replicatie voor een virtuele machine inschakelt [vanuit de VM-weer gave](azure-to-azure-quickstart.md) of [vanuit de Recovery Services-kluis](azure-to-azure-how-to-enable-replication.md), kunt u toestaan dat Site Recovery updates voor de site Recovery-extensie beheert of hand matig beheert.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Extensie-instellingen":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>De instellingen voor het bijwerken van de extensie in de kluis in-/uitschakelen

1. Ga vanuit de Recovery Services kluis naar **Manage**  >  **site Recovery-infra structuur** beheren.
1. Onder voor de update-instellingen **van Azure virtual machines**  >  -**extensie**  >  **kunt u site Recovery beheren** selecteren. **On**

   Als u de extensie hand matig wilt beheren, selecteert u **uit**.

1. Selecteer **Opslaan**.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Instellingen voor extensie-updates":::

> [!IMPORTANT]
> Wanneer u **site Recovery voor beheer toestaan** kiest, wordt de instelling toegepast op alle virtuele machines in de kluis.

> [!NOTE]
> Een van beide opties waarschuwt u voor het Automation-account dat wordt gebruikt voor het beheren van updates. Als u deze functie voor de eerste keer gebruikt in een kluis, wordt standaard een nieuw Automation-account gemaakt. U kunt de instelling ook aanpassen en een bestaand Automation-account kiezen. Eenmaal gedefinieerd, wordt voor alle volgende acties voor het inschakelen van replicatie in dezelfde kluis het geselecteerde Automation-account gebruikt. Op dit moment worden in de vervolg keuzelijst alleen Automation-accounts weer geven die zich in dezelfde resource groep bevinden als de kluis.

> [!IMPORTANT]
> Het volgende script moet worden uitgevoerd in de context van een Automation-account.
Voor een aangepast Automation-account gebruikt u het volgende script:

```azurepowershell
param(
    [Parameter(Mandatory=$true)]
    [String] $VaultResourceId,
    [Parameter(Mandatory=$true)]
    [ValidateSet("Enabled",'Disabled')]
    [Alias("Enabled or Disabled")]
    [String] $AutoUpdateAction,
    [Parameter(Mandatory=$false)]
    [String] $AutomationAccountArmId
)
$SiteRecoveryRunbookName = "Modify-AutoUpdateForVaultForPatner"
$TaskId = [guid]::NewGuid().ToString()
$SubscriptionId = "00000000-0000-0000-0000-000000000000"
$AsrApiVersion = "2018-01-10"
$RunAsConnectionName = "AzureRunAsConnection"
$ArmEndPoint = "https://management.azure.com"
$AadAuthority = "https://login.windows.net/"
$AadAudience = "https://management.core.windows.net/"
$AzureEnvironment = "AzureCloud"
$Timeout = "160"
function Throw-TerminatingErrorMessage
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
        )
    throw ("Message: {0}, TaskId: {1}.") -f $Message, $TaskId
}
function Write-Tracing
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [ValidateSet("Informational", "Warning", "ErrorLevel", "Succeeded", IgnoreCase = $true)]
                [String]
        $Level,
        [Parameter(Mandatory=$true)]
        [String]
        $Message,
            [Switch]
        $DisplayMessageToUser
        )
    Write-Output $Message
}
function Write-InformationTracing
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
        )
    Write-Tracing -Message $Message -Level Informational -DisplayMessageToUser
}
function ValidateInput()
{
    try
    {
        if(!$VaultResourceId.StartsWith("/subscriptions", [System.StringComparison]::OrdinalIgnoreCase))
        {
            $ErrorMessage = "The vault resource id should start with /subscriptions."
            throw $ErrorMessage
        }
        $Tokens = $VaultResourceId.SubString(1).Split("/")
        if(!($Tokens.Count % 2 -eq 0))
        {
            $ErrorMessage = ("Odd Number of tokens: {0}." -f $Tokens.Count)
            throw $ErrorMessage
        }
        if(!($Tokens.Count/2 -eq 4))
        {
            $ErrorMessage = ("Invalid number of resource in vault ARM id expected:4, actual:{0}." -f ($Tokens.Count/2))
            throw $ErrorMessage
        }
        if($AutoUpdateAction -ieq "Enabled" -and [string]::IsNullOrEmpty($AutomationAccountArmId))
        {
            $ErrorMessage = ("The automation account ARM id should not be null or empty when AutoUpdateAction is enabled.")
            throw $ErrorMessage
        }
    }
    catch
    {
        $ErrorMessage = ("ValidateInput failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}
function Initialize-SubscriptionId()
{
    try
    {
        $Tokens = $VaultResourceId.SubString(1).Split("/")
        $Count = 0
                $ArmResources = @{}
        while($Count -lt $Tokens.Count)
        {
            $ArmResources[$Tokens[$Count]] = $Tokens[$Count+1]
            $Count = $Count + 2
        }
                return $ArmResources["subscriptions"]
    }
    catch
    {
        Write-Tracing -Level ErrorLevel -Message ("Initialize-SubscriptionId: failed with [Exception: {0}]." -f $_.Exception) -DisplayMessageToUser
        throw
    }
}
function Invoke-InternalRestMethod($Uri, $Headers, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-RestMethod -Uri $Uri -Headers $Headers
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }
            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}
function Invoke-InternalWebRequest($Uri, $Headers, $Method, $Body, $ContentType, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-WebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -UseBasicParsing
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }
            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}
function Get-Header([ref]$Header, $AadAudience, $AadAuthority, $RunAsConnectionName){
    try
    {
        $RunAsConnection = Get-AutomationConnection -Name $RunAsConnectionName
        $TenantId = $RunAsConnection.TenantId
        $ApplicationId = $RunAsConnection.ApplicationId
        $CertificateThumbprint = $RunAsConnection.CertificateThumbprint
        $Path = "cert:\CurrentUser\My\{0}" -f $CertificateThumbprint
        $Secret = Get-ChildItem -Path $Path
        $ClientCredential = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.ClientAssertionCertificate(
                $ApplicationId,
                $Secret)
        # Trim the forward slash from the AadAuthority if it exist.
        $AadAuthority = $AadAuthority.TrimEnd("/")
        $AuthContext = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(
            "{0}/{1}" -f $AadAuthority, $TenantId )
        $AuthenticationResult = $authContext.AcquireToken($AadAudience, $Clientcredential)
        $Header.Value['Content-Type'] = 'application\json'
        $Header.Value['Authorization'] = $AuthenticationResult.CreateAuthorizationHeader()
        $Header.Value["x-ms-client-request-id"] = $TaskId + "/" + (New-Guid).ToString() + "-" + (Get-Date).ToString("u")
    }
    catch
    {
        $ErrorMessage = ("Get-BearerToken: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}
function Get-ProtectionContainerToBeModified([ref] $ContainerMappingList)
{
    try
    {
        Write-InformationTracing ("Get protection container mappings : {0}." -f $VaultResourceId)
        $ContainerMappingListUrl = $ArmEndPoint + $VaultResourceId + "/replicationProtectionContainerMappings" + "?api-version=" + $AsrApiVersion
        Write-InformationTracing ("Getting the bearer token and the header.")
        Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
        $Result = @()
        Invoke-InternalRestMethod -Uri $ContainerMappingListUrl -Headers $header -Result ([ref]$Result)
        $ContainerMappings = $Result[0]
        Write-InformationTracing ("Total retrieved container mappings: {0}." -f $ContainerMappings.Value.Count)
        foreach($Mapping in $ContainerMappings.Value)
        {
            if(($Mapping.properties.providerSpecificDetails -eq $null) -or ($Mapping.properties.providerSpecificDetails.instanceType -ine "A2A"))
            {
                Write-InformationTracing ("Mapping properties: {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the provider does not match." -f ($Mapping.Id))
                continue;
            }
            if($Mapping.Properties.State -ine "Paired")
            {
                Write-InformationTracing ("Ignoring container mapping: {0} as the state is not paired." -f ($Mapping.Id))
                continue;
            }
            Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties.providerSpecificDetails))
            $MappingAutoUpdateStatus = $Mapping.properties.providerSpecificDetails.agentAutoUpdateStatus
            $MappingAutomationAccountArmId = $Mapping.properties.providerSpecificDetails.automationAccountArmId
            $MappingHealthErrorCount = $Mapping.properties.HealthErrorDetails.Count
            if($AutoUpdateAction -ieq "Enabled" -and
                ($MappingAutoUpdateStatus -ieq "Enabled") -and
                ($MappingAutomationAccountArmId -ieq $AutomationAccountArmId) -and
                ($MappingHealthErrorCount -eq 0))
            {
                Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the auto update is already enabled and is healthy." -f ($Mapping.Id))
                continue;
            }
            ($ContainerMappingList.Value).Add($Mapping.id)
        }
    }
    catch
    {
        $ErrorMessage = ("Get-ProtectionContainerToBeModified: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}
$OperationStartTime = Get-Date
$ContainerMappingList = New-Object System.Collections.Generic.List[System.String]
$JobsInProgressList = @()
$JobsCompletedSuccessList = @()
$JobsCompletedFailedList = @()
$JobsFailedToStart = 0
$JobsTimedOut = 0
$Header = @{}
$AzureRMProfile = Get-Module -ListAvailable -Name AzureRM.Profile | Select Name, Version, Path
$AzureRmProfileModulePath = Split-Path -Parent $AzureRMProfile.Path
Add-Type -Path (Join-Path $AzureRmProfileModulePath "Microsoft.IdentityModel.Clients.ActiveDirectory.dll")
$Inputs = ("Tracing inputs VaultResourceId: {0}, Timeout: {1}, AutoUpdateAction: {2}, AutomationAccountArmId: {3}." -f $VaultResourceId, $Timeout, $AutoUpdateAction, $AutomationAccountArmId)
Write-Tracing -Message $Inputs -Level Informational -DisplayMessageToUser
$CloudConfig = ("Tracing cloud configuration ArmEndPoint: {0}, AadAuthority: {1}, AadAudience: {2}." -f $ArmEndPoint, $AadAuthority, $AadAudience)
Write-Tracing -Message $CloudConfig -Level Informational -DisplayMessageToUser
$AutomationConfig = ("Tracing automation configuration RunAsConnectionName: {0}." -f $RunAsConnectionName)
Write-Tracing -Message $AutomationConfig -Level Informational -DisplayMessageToUser
ValidateInput
$SubscriptionId = Initialize-SubscriptionId
Get-ProtectionContainerToBeModified ([ref]$ContainerMappingList)
$Input = @{
  "properties"= @{
    "providerSpecificInput"= @{
        "instanceType" = "A2A"
        "agentAutoUpdateStatus" = $AutoUpdateAction
        "automationAccountArmId" = $AutomationAccountArmId
    }
  }
}
$InputJson = $Input |  ConvertTo-Json
if ($ContainerMappingList.Count -eq 0)
{
    Write-Tracing -Level Succeeded -Message ("Exiting as there are no container mappings to be modified.") -DisplayMessageToUser
    exit
}
Write-InformationTracing ("Container mappings to be updated has been retrieved with count: {0}." -f $ContainerMappingList.Count)
try
{
    Write-InformationTracing ("Start the modify container mapping jobs.")
    ForEach($Mapping in $ContainerMappingList)
    {
    try {
            $UpdateUrl = $ArmEndPoint + $Mapping + "?api-version=" + $AsrApiVersion
            Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
            $Result = @()
            Invoke-InternalWebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -Result ([ref]$Result)
            $Result = $Result[0]
            $JobAsyncUrl = $Result.Headers['Azure-AsyncOperation']
            Write-InformationTracing ("The modify container mapping job invoked with async url: {0}." -f $JobAsyncUrl)
            $JobsInProgressList += $JobAsyncUrl;
            # Rate controlling the set calls to maximum 60 calls per minute.
            # ASR throttling for set calls is 200 in 1 minute.
            Start-Sleep -Milliseconds 1000
        }
        catch{
            Write-InformationTracing ("The modify container mappings job creation failed for: {0}." -f $Ru)
            Write-InformationTracing $_
            $JobsFailedToStart++
        }
    }
    Write-InformationTracing ("Total modify container mappings has been initiated: {0}." -f $JobsInProgressList.Count)
}
catch
{
    $ErrorMessage = ("Modify container mapping jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
try
{
    while($JobsInProgressList.Count -ne 0)
    {
        Sleep -Seconds 30
        $JobsInProgressListInternal = @()
        ForEach($JobAsyncUrl in $JobsInProgressList)
        {
            try
            {
                Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
                $Result = Invoke-RestMethod -Uri $JobAsyncUrl -Headers $header
                $JobState = $Result.Status
                if($JobState -ieq "InProgress")
                {
                    $JobsInProgressListInternal += $JobAsyncUrl
                }
                elseif($JobState -ieq "Succeeded" -or `
                    $JobState -ieq "PartiallySucceeded" -or `
                    $JobState -ieq "CompletedWithInformation")
                {
                    Write-InformationTracing ("Jobs succeeded with state: {0}." -f $JobState)
                    $JobsCompletedSuccessList += $JobAsyncUrl
                }
                else
                {
                    Write-InformationTracing ("Jobs failed with state: {0}." -f $JobState)
                    $JobsCompletedFailedList += $JobAsyncUrl
                }
            }
            catch
            {
                Write-InformationTracing ("The get job failed with: {0}. Ignoring the exception and retrying the next job." -f $_.Exception)
                # The job on which the tracking failed, will be considered in progress and tried again later.
                $JobsInProgressListInternal += $JobAsyncUrl
            }
            # Rate controlling the get calls to maximum 120 calls each minute.
            # ASR throttling for get calls is 10000 in 60 minutes.
            Start-Sleep -Milliseconds 500
        }
        Write-InformationTracing ("Jobs remaining {0}." -f $JobsInProgressListInternal.Count)
        $CurrentTime = Get-Date
        if($CurrentTime -gt $OperationStartTime.AddMinutes($Timeout))
        {
            Write-InformationTracing ("Tracing modify cloud pairing jobs has timed out.")
            $JobsTimedOut = $JobsInProgressListInternal.Count
            $JobsInProgressListInternal = @()
        }
        $JobsInProgressList = $JobsInProgressListInternal
    }
}
catch
{
    $ErrorMessage = ("Tracking modify cloud pairing jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage  -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
Write-InformationTracing ("Tracking modify cloud pairing jobs completed.")
Write-InformationTracing ("Modify cloud pairing jobs success: {0}." -f $JobsCompletedSuccessList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed: {0}." -f $JobsCompletedFailedList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed to start: {0}." -f $JobsFailedToStart)
Write-InformationTracing ("Modify cloud pairing jobs timedout: {0}." -f $JobsTimedOut)
if($JobsTimedOut -gt  0)
{
    $ErrorMessage = "One or more modify cloud pairing jobs has timedout."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
elseif($JobsCompletedSuccessList.Count -ne $ContainerMappingList.Count)
{
    $ErrorMessage = "One or more modify cloud pairing jobs failed."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
Write-Tracing -Level Succeeded -Message ("Modify cloud pairing completed.") -DisplayMessageToUser
```

### <a name="manage-updates-manually"></a>Updates hand matig beheren

1. Als er nieuwe updates zijn voor de Mobility-service die is geïnstalleerd op uw Vm's, ziet u de volgende melding: er **is een nieuwe site Recovery update voor de replicatie agent beschikbaar. Klik om te installeren.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Venster gerepliceerde items":::

1. Selecteer de melding om de pagina VM selecteren te openen.
1. Kies de Vm's die u wilt bijwerken en selecteer vervolgens **OK**. De update Mobility-service wordt gestart voor elke geselecteerde VM.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="VM-lijst van gerepliceerde items":::

## <a name="common-issues-and-troubleshooting"></a>Veelvoorkomende problemen en probleem oplossing

Als er een probleem is met de automatische updates, wordt er een fout melding weer geven onder **configuratie problemen** in het kluis dashboard.

Als u automatische updates niet kunt inschakelen, raadpleegt u de volgende veelvoorkomende fouten en aanbevolen acties:

- **Fout**: u hebt geen machtigingen om een uitvoeren als-account voor Azure (Service-Principal) te maken en de rol Inzender toe te kennen aan de Service-Principal.

  **Aanbevolen actie**: Zorg ervoor dat het aangemelde account is toegewezen als Inzender en probeer het opnieuw. Voor meer informatie over het toewijzen van machtigingen raadpleegt u de sectie vereiste machtigingen [: gebruik de portal om een Azure AD-toepassing en Service-Principal te maken die toegang hebben tot resources](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

  Als u de meeste problemen wilt verhelpen nadat u automatische updates hebt ingeschakeld, selecteert u **herstellen**. Als de knop herstellen niet beschikbaar is, raadpleegt u het fout bericht dat wordt weer gegeven in het deel venster instellingen voor het bijwerken van de extensie.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Knop Site Recovery service herstellen in instellingen voor extensie-updates":::

- **Fout**: het run as-account heeft geen machtiging voor toegang tot de Recovery Services-resource.

  **Aanbevolen actie**: Verwijder [het uitvoeren als-account en maak het vervolgens opnieuw](../automation/manage-runas-account.md). Of zorg ervoor dat de Azure Active Directory toepassing van het Automation uitvoeren als-account toegang heeft tot de Recovery Services-resource.

- **Fout**: het run as-account is niet gevonden. Een van deze is verwijderd of niet gemaakt: Azure Active Directory toepassing, Service-Principal, rol, Automation-certificaat Asset, Automation-verbindings element, of de vinger afdruk is niet identiek aan het certificaat en de verbinding.

  **Aanbevolen actie**: Verwijder [het uitvoeren als-account en maak het vervolgens opnieuw](../automation/manage-runas-account.md).

- **Fout**: het uitvoeren als-certificaat van Azure dat wordt gebruikt door het Automation-account is bijna verlopen.

  Het zelfondertekende certificaat dat voor het run as-account is gemaakt, verloopt één jaar na de aanmaak datum. U kunt het certificaat op elk gewenst moment vernieuwen voordat het verloopt. Als u zich hebt geregistreerd voor e-mail meldingen, ontvangt u ook e-mails wanneer een actie is vereist aan uw zijde. Deze fout wordt weer gegeven twee maanden vóór de verval datum en wordt gewijzigd in een kritieke fout als het certificaat is verlopen. Zodra het certificaat is verlopen, werkt automatisch bijwerken pas nadat u dit hebt vernieuwd.

  **Aanbevolen actie**: Selecteer **herstellen** en vervolgens **certificaat vernieuwen** om dit probleem op te lossen.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="renew-CERT":::

  > [!NOTE]
  > Nadat u het certificaat hebt vernieuwd, vernieuwt u de pagina om de huidige status weer te geven.
