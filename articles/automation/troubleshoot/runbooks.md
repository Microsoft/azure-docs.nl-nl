---
title: Problemen Azure Automation runbook oplossen
description: In dit artikel wordt beschreven hoe u problemen met runbooks Azure Automation oplossen.
services: automation
ms.date: 02/11/2021
ms.topic: troubleshooting
ms.custom: has-adal-ref, devx-track-azurepowershell
ms.openlocfilehash: 7964bc62aefc912a0f61744841784600575c98de
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831218"
---
# <a name="troubleshoot-runbook-issues"></a>Problemen met runbooks oplossen

 In dit artikel worden runbookproblemen beschreven die zich kunnen voordoen en hoe u deze kunt oplossen. Zie Runbook execution [in Azure Automation (Uitvoering van runbook in Azure Automation) voor algemene informatie.](../automation-runbook-execution.md)

## <a name="diagnose-runbook-issues"></a>Runbookproblemen vaststellen

Wanneer er fouten optreden tijdens het uitvoeren van runbooks in Azure Automation, kunt u de volgende stappen gebruiken om de problemen te diagnosticeren:

1. Zorg ervoor dat uw runbookscript goed wordt uitgevoerd op uw lokale computer.

    Zie [PowerShell Docs](/powershell/scripting/overview) of Python Docs voor taalverwijzingen [en leermodules.](https://docs.python.org/3/) Door het script lokaal uit te voeren, kunt u veelvoorkomende fouten ontdekken en oplossen, zoals:

      * Ontbrekende modules
      * Syntaxisfouten
      * Logische fouten

1. Runbookfoutstromen [onderzoeken.](../automation-runbook-output-and-messages.md#runbook-output)

    Bekijk deze stromen voor specifieke berichten en vergelijk deze met de fouten die in dit artikel worden beschreven.

1. Zorg ervoor dat uw knooppunten en Automation-werkruimte de vereiste modules hebben.

    Als uw runbook modules importeert, controleert u of ze beschikbaar zijn voor uw Automation-account met behulp van de stappen in [Modules importeren.](../shared-resources/modules.md#import-modules) Werk uw PowerShell-modules bij naar de nieuwste versie door de instructies in [Update Azure PowerShell modules in Azure Automation](../automation-update-azure-modules.md). Zie Troubleshoot modules (Problemen met modules oplossen) [voor meer informatie over probleemoplossing.](shared-resources.md#modules)

1. Als uw runbook is opgeschort of onverwacht mislukt:

    * [Vernieuw het certificaat](../manage-runas-account.md#cert-renewal) als het Uitvoeren als-account is verlopen.
    * [Vernieuw de webhook](../automation-webhooks.md#renew-a-webhook) als u een verlopen webhook probeert te gebruiken om het runbook te starten.
    * [Controleer de taakstatussen](../automation-runbook-execution.md#job-statuses) om de status van het huidige runbook en een aantal mogelijke oorzaken van het probleem te bepalen.
    * [Voeg extra uitvoer toe](../automation-runbook-output-and-messages.md#working-with-message-streams) aan het runbook om te bepalen wat er gebeurt voordat het runbook wordt opgeschort.
    * [Ver handelen alle uitzonderingen](../automation-runbook-execution.md#exceptions) af die door uw taak worden veroorzaakt.

1. Voer deze stap uit als de runbook-taak of de omgeving op Hybrid Runbook Worker niet reageert.

    Als u uw runbooks op een Hybrid Runbook Worker in plaats van in Azure Automation, moet u mogelijk problemen met de [Hybrid Worker zelf oplossen.](hybrid-runbook-worker.md)

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>Scenario: Runbook mislukt met de fout Geen machtiging of Verboden 403

### <a name="issue"></a>Probleem

Uw runbook mislukt met de fout Geen machtiging of Verboden 403 of gelijkwaardig.

### <a name="cause"></a>Oorzaak

Uitvoeren als-accounts hebben mogelijk niet dezelfde machtigingen voor Azure-resources als uw huidige Automation-account. 

### <a name="resolution"></a>Oplossing

Zorg ervoor dat uw Uitvoeren als-account [machtigingen heeft voor toegang tot alle resources die](../../role-based-access-control/role-assignments-portal.md) in uw script worden gebruikt.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Scenario: Aanmelden bij Azure-account is mislukt

### <a name="issue"></a>Probleem

U ontvangt een van de volgende fouten wanneer u met de `Connect-AzAccount` cmdlet werkt:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Oorzaak

Deze fouten treden op als de naam van de referentie-asset ongeldig is. Ze kunnen ook optreden als de gebruikersnaam en het wachtwoord die u hebt gebruikt voor het instellen van de Automation-referentie-asset niet geldig zijn.

### <a name="resolution"></a>Oplossing

Volg deze stappen om te bepalen wat er mis is:

1. Zorg ervoor dat u geen speciale tekens hebt. Deze tekens bevatten het teken in de naam van de `\@` Automation-referentie-asset die u gebruikt om verbinding te maken met Azure.
1. Controleer of u de gebruikersnaam en het wachtwoord kunt gebruiken die zijn opgeslagen in de Azure Automation in uw lokale PowerShell ISE-editor. Voer de volgende cmdlets uit in PowerShell ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount -Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount -Credential $Cred
   ```

1. Als uw verificatie lokaal mislukt, hebt u uw referenties Azure Active Directory (Azure AD) niet juist ingesteld. Zie het artikel Verifiëren bij Azure met behulp van Azure Active Directory om het Azure AD-account [correct in te stellen.](../automation-use-azure-ad.md)

1. Als de fout tijdelijk lijkt te zijn, voegt u pogingslogica toe aan uw verificatieroutine om de verificatie robuuster te maken.

   ```powershell
   # Get the connection "AzureRunAsConnection"
   $connectionName = "AzureRunAsConnection"
   $servicePrincipalConnection = Get-AutomationConnection -Name $connectionName

   $logonAttempt = 0
   $logonResult = $False

   while(!($connectionResult) -And ($logonAttempt -le 10))
   {
       $LogonAttempt++
       #Logging in to Azure...
       $connectionResult = Connect-AzAccount `
                              -ServicePrincipal `
                              -Tenant $servicePrincipalConnection.TenantId `
                              -ApplicationId $servicePrincipalConnection.ApplicationId `
                              -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint

       Start-Sleep -Seconds 30
   }
   ```

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Scenario: Een Login-AzureRMAccount om u aan te melden

### <a name="issue"></a>Probleem

U krijgt de volgende foutmelding wanneer u een runbook uit te voeren:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Oorzaak

Deze fout kan optreden wanneer u geen Uitvoeren als-account gebruikt of als het Uitvoeren als-account is verlopen. Zie overzicht van Uitvoeren [Azure Automation-accounts](../automation-security-overview.md#run-as-accounts)voor meer informatie.

Deze fout heeft twee primaire oorzaken:

* Er zijn verschillende versies van de AzureRM- of Az-module.
* U probeert toegang te krijgen tot resources in een afzonderlijk abonnement.

### <a name="resolution"></a>Oplossing

Als u deze fout ontvangt nadat u één AzureRM- of Az-module hebt bijgewerkt, moet u al uw modules bijwerken naar dezelfde versie.

Als u toegang probeert te krijgen tot resources in een ander abonnement, volgt u deze stappen om machtigingen te configureren:

1. Ga naar het Uitvoeren als-account van Automation en kopieer de **toepassings-id** **en vingerafdruk**.

    ![Toepassings-id en vingerafdruk kopiëren](../media/troubleshoot-runbooks/collect-app-id.png)

1. Ga naar het toegangsbeheer **van het abonnement** waar het Automation-account niet wordt gehost en voeg een nieuwe roltoewijzing toe. 

    ![Toegangsbeheer](../media/troubleshoot-runbooks/access-control.png)

1. Voeg de **toepassings-id toe** die u eerder hebt verzameld. Selecteer  Inzendermachtigingen.

    ![Roltoewijzing toevoegen](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Kopieer de naam van het abonnement.

1. U kunt nu de volgende runbookcode gebruiken om de machtigingen van uw Automation-account naar het andere abonnement te testen. Vervang `<CertificateThumbprint>` door de waarde die u in stap 1 hebt gekopieerd. Vervang `"<SubscriptionName>"` door de waarde die u in stap 4 hebt gekopieerd.

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint "<CertificateThumbprint>"
    #Select the subscription you want to work with
    Select-AzSubscription -SubscriptionName '<YourSubscriptionNameGoesHere>'

    #Test and get outputs of the subscriptions you granted access.
    $subscriptions = Get-AzSubscription
    foreach($subscription in $subscriptions)
    {
        Set-AzContext $subscription
        Write-Output $subscription.Name
    }
    ```

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Scenario: Kan het Azure-abonnement niet vinden

### <a name="issue"></a>Probleem

U ontvangt de volgende foutmelding wanneer u met `Select-AzureSubscription` de `Select-AzureRMSubscription` cmdlet , of `Select-AzSubscription` werkt:

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Fout

Deze fout kan optreden als:

* De naam van het abonnement is ongeldig.
* De Azure AD-gebruiker die de abonnementsgegevens probeert op te halen, is niet geconfigureerd als beheerder van het abonnement.
* De cmdlet is niet beschikbaar.

### <a name="resolution"></a>Oplossing

Volg deze stappen om te bepalen of u zich hebt geverifieerd bij Azure en toegang hebt tot het abonnement dat u wilt selecteren:

1. Als u er zeker van wilt zijn dat uw script zelfstandig werkt, test u het buiten Azure Automation.
1. Zorg ervoor dat met uw script de cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) wordt uitgevoerd voordat u de `Select-*` cmdlet gaat uitvoeren.
1. Voeg `Disable-AzContextAutosave -Scope Process` toe aan het begin van het runbook. Deze cmdlet zorgt ervoor dat referenties alleen van toepassing zijn op de uitvoering van het huidige runbook.
1. Als het foutbericht nog steeds wordt weergegeven, wijzigt u de code door de parameter voor toe te `AzContext` voegen en voert u vervolgens de code `Connect-AzAccount` uit.

   ```powershell
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Scenario: Runbooks mislukken bij het werken met meerdere abonnementen

### <a name="issue"></a>Probleem

Bij het uitvoeren van runbooks kan het runbook geen Azure-resources beheren.

### <a name="cause"></a>Oorzaak

Het runbook gebruikt niet de juiste context bij het uitvoeren. Dit kan komen doordat het runbook per ongeluk probeert toegang te krijgen tot het onjuiste abonnement.

Mogelijk ziet u fouten zoals deze:

```error
Get-AzVM : The client '<automation-runas-account-guid>' with object id '<automation-runas-account-guid>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope '/subscriptions/<subcriptionIdOfSubscriptionWichDoesntContainTheVM>/resourceGroups/REsourceGroupName/providers/Microsoft.Compute/virtualMachines/VMName '.
   ErrorCode: AuthorizationFailed
   StatusCode: 403
   ReasonPhrase: Forbidden Operation
   ID : <AGuidRepresentingTheOperation> At line:51 char:7 + $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $UNBV... +
```

### <a name="resolution"></a>Oplossing

De abonnementscontext gaat mogelijk verloren wanneer een runbook meerdere runbooks aanroept. Volg de onderstaande richtlijnen om te voorkomen dat u per ongeluk toegang probeert te krijgen tot het onjuiste abonnement.

* Om te voorkomen dat naar het verkeerde abonnement wordt verwezen, schakelt u het opslaan van context in uw Automation-runbooks uit met behulp van de volgende code aan het begin van elk runbook.

   ```azurepowershell-interactive
   Disable-AzContextAutosave -Scope Process
   ```

* De Azure PowerShell-cmdlets ondersteunen de `-DefaultProfile` parameter . Dit is toegevoegd aan alle Az- en AzureRm-cmdlets ter ondersteuning van het uitvoeren van meerdere PowerShell-scripts in hetzelfde proces, zodat u de context en welk abonnement voor elke cmdlet kunt opgeven. Met uw runbooks moet u het contextobject in uw runbook opslaan wanneer het runbook wordt gemaakt (dat wil zeggen wanneer een account zich meldt) en telkens wanneer het wordt gewijzigd, en verwijzen naar de context wanneer u een Az-cmdlet opgeeft.

   > [!NOTE]
   > U moet een contextobject doorgeven, zelfs wanneer u de context rechtstreeks met cmdlets zoals [Set-AzContext](/powershell/module/az.accounts/Set-AzContext) of [Select-AzSubscription manipuleert.](/powershell/module/servicemanagement/azure.service/set-azuresubscription)

   ```azurepowershell-interactive
   $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName 
   $context = Add-AzAccount `
             -ServicePrincipal `
             -TenantId $servicePrincipalConnection.TenantId `
             -ApplicationId $servicePrincipalConnection.ApplicationId `
             -Subscription 'cd4dxxxx-xxxx-xxxx-xxxx-xxxxxxxx9749' `
             -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
   $context = Set-AzContext -SubscriptionName $subscription `
       -DefaultProfile $context
   Get-AzVm -DefaultProfile $context
   ```
  
## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Scenario: Verificatie bij Azure mislukt omdat meervoudige verificatie is ingeschakeld

### <a name="issue"></a>Probleem

U ontvangt de volgende fout bij het authenticeren bij Azure met uw Azure-gebruikersnaam en -wachtwoord:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Oorzaak

Als uw Azure-account meervoudige verificatie heeft, kunt u geen gebruiker Azure Active Directory om te verifiëren bij Azure. In plaats daarvan moet u een certificaat of een service-principal gebruiken om te verifiëren.

### <a name="resolution"></a>Oplossing

Zie Een klassiek Uitvoeren als-account maken om Azure-services te beheren als u een klassiek Uitvoeren als-account wilt gebruiken met cmdlets voor klassieke [Azure-implementatiemodellen.](../automation-create-standalone-account.md#create-a-classic-run-as-account) Zie Creating service principal using Azure Portal (Service-Azure Resource Manager principal maken met Azure Portal) en [Authenticating a service principal with Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)(Service-principal maken met behulp van [Azure Portal)](../../active-directory/develop/howto-create-service-principal-portal.md) en Authenticating a service principal with Azure Resource Manager .

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>Scenario: Runbook mislukt met het foutbericht 'A task was canceled' (Een taak is geannuleerd)

### <a name="issue"></a>Probleem

Uw runbook mislukt met een fout die lijkt op het volgende voorbeeld:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Oorzaak

Deze fout kan worden veroorzaakt door het gebruik van verouderde Azure-modules.

### <a name="resolution"></a>Oplossing

U kunt deze fout oplossen door uw Azure-modules bij te werken naar de nieuwste versie:

1. Selecteer modules in uw **Automation-account** en selecteer vervolgens **Azure-modules bijwerken.**
1. De update duurt ongeveer 15 minuten. Nadat dit is voltooid, moet u het mislukte runbook opnieuw uitvoeren.

Zie Azure-modules bijwerken in Azure Automation voor meer informatie [over het bijwerken Azure Automation.](../automation-update-azure-modules.md)

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>Scenario: Term wordt niet herkend als de naam van een cmdlet, functie of script

### <a name="issue"></a>Probleem

Uw runbook mislukt met een fout die lijkt op het volgende voorbeeld:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Oorzaak

Deze fout kan de volgende oorzaken hebben:

* De module die de cmdlet bevat, wordt niet geïmporteerd in het Automation-account.
* De module die de cmdlet bevat, wordt geïmporteerd, maar is verouderd.

### <a name="resolution"></a>Oplossing

Doe een van de volgende taken om deze fout op te lossen:

* Zie How to [update Azure PowerShell modules in Azure Automation](../automation-update-azure-modules.md) (Uw modules bijwerken in uw Automation-account) voor een Azure-module.
* Zorg er voor een niet-Azure-module voor dat de module is geïmporteerd in uw Automation-account.

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>Scenario: Cmdlet mislukt in PnP PowerShell-runbook op Azure Automation

### <a name="issue"></a>Probleem

Wanneer een runbook een door PnP PowerShell gegenereerd object rechtstreeks naar de Azure Automation-uitvoer schrijft, kan de uitvoer van de cmdlet niet worden teruggestreamd naar Automation.

### <a name="cause"></a>Oorzaak

Dit probleem treedt meestal op Azure Automation runbooks verwerkt die PnP PowerShell-cmdlets aanroepen, bijvoorbeeld , zonder de retourobjecten `add-pnplistitem` te detecteren.

### <a name="resolution"></a>Oplossing

Bewerk uw scripts om retourwaarden toe te wijzen aan variabelen, zodat de cmdlets niet proberen hele objecten naar de standaarduitvoer te schrijven. Een script kan de uitvoerstroom omleiden naar een cmdlet, zoals hier wordt weergegeven.

```azurecli
  $null = add-pnplistitem
```

Als uw script cmdlet-uitvoer parseert, moet het script de uitvoer opslaan in een variabele en de variabele bewerken in plaats van de uitvoer te streamen.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Scenario: Cmdlet wordt niet herkend bij het uitvoeren van een runbook

### <a name="issue"></a>Probleem

De runbook-taak mislukt met de volgende fout:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Oorzaak

Deze fout wordt veroorzaakt wanneer de PowerShell-engine de cmdlet die u in uw runbook gebruikt, niet kan vinden. Het is mogelijk dat de module met de cmdlet ontbreekt in het account, er een naamconflict is met een runbooknaam of dat de cmdlet ook in een andere module aanwezig is en dat Automation de naam niet kan oplossen.

### <a name="resolution"></a>Oplossing

Gebruik een van de volgende oplossingen om het probleem op te lossen:

* Zorg ervoor dat u de naam van de cmdlet correct hebt ingevoerd.
* Zorg ervoor dat de cmdlet in uw Automation-account bestaat en dat er geen conflicten zijn. Als u wilt controleren of de cmdlet aanwezig is, opent u een runbook in de bewerkingsmodus en zoekt u naar de cmdlet die u wilt zoeken in de bibliotheek of voer `Get-Command <CommandName>` uit. Nadat u hebt gevalideerd dat de cmdlet beschikbaar is voor het account en dat er geen naamconflicten zijn met andere cmdlets of runbooks, voegt u de cmdlet toe aan het canvas. Zorg ervoor dat u een geldige parameterset in uw runbook gebruikt.
* Als er een naamconflict is en de cmdlet beschikbaar is in twee verschillende modules, lost u het probleem op met behulp van de volledig gekwalificeerde naam voor de cmdlet. U kunt bijvoorbeeld `ModuleName\CmdletName` gebruiken.
* Als u het runbook on-premises in een Hybrid Worker-groep wilt uitvoeren, moet u ervoor zorgen dat de module en cmdlet zijn geïnstalleerd op de computer die als host voor de Hybrid Worker wordt gebruikt.

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Scenario: Onjuiste objectverwijzing bij aanroep van Add-AzAccount

### <a name="issue"></a>Probleem

U ontvangt deze foutmelding wanneer u werkt met `Add-AzAccount` . Dit is een alias voor de `Connect-AzAccount` cmdlet :

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Oorzaak

Deze fout kan optreden als het runbook niet de juiste stappen doet voordat u aanroept `Add-AzAccount` om het Automation-account toe te voegen. Een voorbeeld van een van de benodigde stappen is aanmelden met een Uitvoeren als-account. Zie [Runbookuitvoering](../automation-runbook-execution.md)in Azure Automation voor de juiste bewerkingen die u in uw runbook Azure Automation.

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Scenario: Objectverwijzing is niet ingesteld op een exemplaar van een object

### <a name="issue"></a>Probleem

U ontvangt de volgende foutmelding wanneer u een onderliggend runbook aanroept met de `Wait` parameter en de uitvoerstroom een -object bevat:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Oorzaak

Als de stroom objecten bevat, `Start-AzAutomationRunbook` wordt de uitvoerstroom niet correct verwerkt.

### <a name="resolution"></a>Oplossing

Implementeert een pollinglogica en gebruik de cmdlet [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) om de uitvoer op te halen. Hier wordt een voorbeeld van deze logica gedefinieerd:

```powershell
$automationAccountName = "ContosoAutomationAccount"
$runbookName = "ChildRunbookExample"
$resourceGroupName = "ContosoRG"

function IsJobTerminalState([string] $status) {
    return $status -eq "Completed" -or $status -eq "Failed" -or $status -eq "Stopped" -or $status -eq "Suspended"
}

$job = Start-AzAutomationRunbook -AutomationAccountName $automationAccountName -Name $runbookName -ResourceGroupName $resourceGroupName
$pollingSeconds = 5
$maxTimeout = 10800
$waitTime = 0
while($false -eq (IsJobTerminalState $job.Status) -and $waitTime -lt $maxTimeout) {
   Start-Sleep -Seconds $pollingSeconds
   $waitTime += $pollingSeconds
   $job = $job | Get-AzAutomationJob
}

$job | Get-AzAutomationJobOutput | Get-AzAutomationJobOutputRecord | Select-Object -ExpandProperty Value
```

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Scenario: Runbook mislukt vanwege gedeserialiseerd object

### <a name="issue"></a>Probleem

Uw runbook mislukt met de volgende fout:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Oorzaak

Als uw runbook een PowerShell-werkstroom is, worden complexe objecten in een gedeserialiseerde indeling op te slaat om de status van het runbook te blijven gebruiken als de werkstroom wordt opgeschort.

### <a name="resolution"></a>Oplossing

Gebruik een van de volgende oplossingen om dit probleem op te lossen:

* Als u complexe objecten van de ene cmdlet naar de andere doorspijpt, verpakt u deze cmdlets in een `InlineScript` activiteit.
* Geef de naam of waarde door die u nodig hebt van het complexe object in plaats van het hele object door te geven.
* Gebruik een PowerShell-runbook in plaats van een PowerShell Workflow-runbook.

## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>Scenario: Status 400 Bad Request bij het aanroepen van een webhook

### <a name="issue"></a>Probleem

Wanneer u probeert een webhook aan te roepen voor een Azure Automation runbook, wordt de volgende fout weergegeven:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Oorzaak

De webhook die u probeert aan te roepen, is uitgeschakeld of is verlopen. 

### <a name="resolution"></a>Oplossing

Als de webhook is uitgeschakeld, kunt u deze opnieuw inschakelen via de Azure Portal. Als de webhook is verlopen, moet u deze verwijderen en opnieuw maken. U kunt [een webhook alleen vernieuwen](../automation-webhooks.md#renew-a-webhook) als deze nog niet is verlopen. 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Scenario: 429: De aanvraagsnelheid is momenteel te hoog

### <a name="issue"></a>Probleem

U ontvangt het volgende foutbericht bij het uitvoeren van de `Get-AzAutomationJobOutput` cmdlet:

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Oorzaak

Deze fout kan optreden bij het ophalen van taakuitvoer van een runbook met veel [uitgebreide streams.](../automation-runbook-output-and-messages.md#write-output-to-verbose-stream)

### <a name="resolution"></a>Oplossing

Ga op een van de volgende dingen te werk om deze fout op te lossen:

* Bewerk het runbook en verminder het aantal taakstromen dat wordt uitgegeven.
* Verminder het aantal stromen dat moet worden opgehaald bij het uitvoeren van de cmdlet. Hiervoor kunt u de waarde van de parameter voor de `Stream` cmdlet [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) instellen om alleen uitvoerstromen op te halen. 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>Scenario: Runbook-taak mislukt omdat het toegewezen quotum is overschreden

### <a name="issue"></a>Probleem

De runbook-taak mislukt met de volgende fout:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer de taakuitvoering het gratis quotum van 500 minuten voor uw account overschrijdt. Dit quotum is van toepassing op alle typen taakuitvoeringstaken. Sommige van deze taken zijn het testen van een job, het starten van een taak vanuit de portal, het uitvoeren van een taak met behulp van webhooks of het plannen van een taak die moet worden uitgevoerd met behulp van de Azure Portal of uw datacenter. Zie Automation-prijzen voor meer informatie over [prijzen voor Automation.](https://azure.microsoft.com/pricing/details/automation/)

### <a name="resolution"></a>Oplossing

Als u meer dan 500 minuten verwerking per maand wilt gebruiken, wijzigt u uw abonnement van de gratis laag in de basic-laag:

1. Meld u aan bij uw Azure-abonnement.
1. Selecteer het Automation-account dat u wilt upgraden.
1. Selecteer **Instellingen** en selecteer vervolgens **Prijzen.**
1. Selecteer **Inschakelen** op de pagina onder om uw account te upgraden naar de basic-laag.

## <a name="scenario-runbook-output-stream-greater-than-1-mb"></a><a name="output-stream-greater-1mb"></a>Scenario: Runbookuitvoerstroom groter dan 1 MB

### <a name="issue"></a>Probleem

Het runbook dat wordt uitgevoerd in de Azure-sandbox mislukt met de volgende fout:

```error
The runbook job failed due to a job stream being larger than 1MB, this is the limit supported by an Azure Automation sandbox.
```

### <a name="cause"></a>Oorzaak

Deze fout treedt op omdat uw runbook heeft geprobeerd te veel uitzonderingsgegevens naar de uitvoerstroom te schrijven.

### <a name="resolution"></a>Oplossing

Er is een limiet van 1 MB voor de taakuitvoerstroom. Zorg ervoor dat uw runbook aanroepen naar een uitvoerbaar bestand of subprocessen insluit met behulp van `try` - en `catch` -blokken. Als de bewerkingen een uitzondering vormen, moet u de code het bericht van de uitzondering laten schrijven naar een Automation-variabele. Deze techniek voorkomt dat het bericht naar de uitvoerstroom van de taak wordt geschreven. Voor Hybrid Runbook Worker uitgevoerd, wordt de uitvoerstroom, afgekapt tot 1 MB, weergegeven zonder foutbericht.

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>Scenario: er is drie keer geprobeerd een runbook-taak te starten, maar deze taak kan niet elke keer worden uitgevoerd

### <a name="issue"></a>Probleem

Uw runbook mislukt met de volgende fout:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Oorzaak

Deze fout treedt op vanwege een van de volgende problemen:

* **Geheugenlimiet.** Een taak kan mislukken als deze meer dan 400 MB geheugen gebruikt. De gedocumenteerde limieten voor geheugen die zijn toegewezen aan een sandbox, vindt u in [Automation-servicelimieten.](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) 

* **Netwerksockers.** Azure-sandboxes zijn beperkt tot 1000 gelijktijdige netwerksockes. Zie [Automation-servicelimieten voor meer informatie.](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits)

* **Module niet compatibel.** Moduleafhankelijkheden zijn mogelijk niet juist. In dit geval retourneert uw runbook doorgaans een `Command not found` - of `Cannot bind parameter` -bericht.

* **Geen verificatie met Active Directory voor sandbox.** Uw runbook heeft geprobeerd een uitvoerbaar bestand of subprocessen aan te roepen die worden uitgevoerd in een Azure-sandbox. Het configureren van runbooks voor verificatie met Azure AD met behulp van Azure Active Directory Authentication Library (ADAL) wordt niet ondersteund.

### <a name="resolution"></a>Oplossing

* **Geheugenlimiet, netwerksockers.** Voorgestelde manieren om binnen de geheugenlimieten te werken, zijn het splitsen van de werkbelasting over meerdere runbooks, het verwerken van minder gegevens in het geheugen, het schrijven van onnodige uitvoer van uw runbooks en het overwegen van het aantal controlepunten dat in uw PowerShell Workflow-runbooks wordt geschreven. Gebruik de methode clear, zoals , om variabelen te leeg te maken en `$myVar.clear` te gebruiken om garbage collection onmiddellijk uit te `[GC]::Collect` voeren. Deze acties verminderen de geheugenvoetafdruk van uw runbook tijdens runtime.

* **Module niet compatibel.** Werk uw Azure-modules bij door de stappen te volgen in Azure PowerShell [modules bijwerken in Azure Automation](../automation-update-azure-modules.md).

* **Geen verificatie met Active Directory voor sandbox.** Wanneer u zich bij Azure AD verifieert met een runbook, moet u ervoor zorgen dat de Azure AD-module beschikbaar is in uw Automation-account. Zorg ervoor dat u het Uitvoeren als-account de benodigde machtigingen verleent om de taken uit te voeren die door het runbook worden automatiseren.

  Als uw runbook geen uitvoerbaar bestand of subprocessen kan aanroepen die worden uitgevoerd in een Azure-sandbox, gebruikt u het runbook op [een Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybrid Workers worden niet beperkt door de geheugen- en netwerklimieten die Azure-sandboxes hebben.

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>Scenario: PowerShell-taak mislukt met foutbericht 'Kan methode niet aanroepen'

### <a name="issue"></a>Probleem

U ontvangt het volgende foutbericht wanneer u een PowerShell-taak start in een runbook dat wordt uitgevoerd in Azure:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Oorzaak

Deze fout kan erop wijzen dat runbooks die worden uitgevoerd in een Azure-sandbox, niet kunnen worden uitgevoerd in de [modus Volledige taal.](/powershell/module/microsoft.powershell.core/about/about_language_modes)

### <a name="resolution"></a>Oplossing

Er zijn twee manieren om deze fout op te lossen:

* In plaats van [Start-Job te gebruiken,](/powershell/module/microsoft.powershell.core/start-job)gebruikt [u Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) om het runbook te starten.
* Probeer het runbook uit te voeren op een Hybrid Runbook Worker.

Zie [Runbookuitvoering in](../automation-runbook-execution.md)Azure Automation voor meer informatie over dit gedrag en Azure Automation andere Azure Automation runbooks.

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Scenario: Een langlopende runbook kan niet worden voltooid

### <a name="issue"></a>Probleem

Uw runbook heeft de status Gestopt nadat het drie uur is uitgevoerd. Mogelijk krijgt u ook deze foutmelding:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Dit gedrag is ontwerp in Azure-sandboxes vanwege de [fair share-controle](../automation-runbook-execution.md#fair-share) van processen binnen Azure Automation. Als een proces langer dan drie uur wordt uitgevoerd, stopt fair share automatisch een runbook. De status van een runbook dat de tijdslimiet voor fair share heeft overschreden, verschilt per runbooktype. PowerShell- en Python-runbooks zijn ingesteld op de status Gestopt. PowerShell Workflow-runbooks zijn ingesteld op Mislukt.

### <a name="cause"></a>Oorzaak

Het runbook heeft de limiet van drie uur overschreden die is toegestaan door fair share in een Azure-sandbox.

### <a name="resolution"></a>Oplossing

Een aanbevolen oplossing is om het runbook uit te voeren op [een Hybrid Runbook Worker](../automation-hrw-run-runbooks.md). Hybrid Workers worden niet beperkt door de limiet van drie uur voor fair share-runbooks van Azure-sandboxes. Runbooks die worden uitgevoerd op Hybrid Runbook Workers moeten worden ontwikkeld om gedrag bij opnieuw opstarten te ondersteunen als er onverwachte problemen zijn met de lokale infrastructuur.

Een andere oplossing is om het runbook te optimaliseren door [onderliggende runbooks te maken.](../automation-child-runbooks.md) Als uw runbook op verschillende resources door dezelfde functie loopt, bijvoorbeeld in een databasebewerking op verschillende databases, kunt u de functie verplaatsen naar een onderliggend runbook. Elk onderliggend runbook wordt parallel uitgevoerd in een afzonderlijk proces. Dit gedrag vermindert de totale tijd die nodig is om het bovenliggende runbook te voltooien.

De PowerShell-cmdlets die het onderliggende runbookscenario mogelijk maken, zijn:

* [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook). met deze cmdlet kunt u een runbook starten en parameters aan het runbook doorgeven.
* [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob). Als er bewerkingen moeten worden uitgevoerd nadat het onderliggende runbook is voltooid, kunt u met deze cmdlet de taakstatus voor elk onderliggend runbook controleren.

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Scenario: Fout in taakstromen over de get_SerializationSettings methode

### <a name="issue"></a>Probleem

U ziet de volgende fout in uw taakstromen voor een runbook:

```error
Connect-AzAccount : Method 'get_SerializationSettings' in type
'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly
'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35'
does not have an implementation.
At line:16 char:1
+ Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -Appl ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [Connect-AzAccount], TypeLoadException
    + FullyQualifiedErrorId : System.TypeLoadException,Microsoft.Azure.Commands.Profile.ConnectAzAccountCommand
```

### <a name="cause"></a>Oorzaak

Deze fout wordt waarschijnlijk veroorzaakt door een onvolledige migratie van AzureRM naar Az-modules in uw runbook. Deze situatie kan ertoe Azure Automation een runbook-taak te starten met alleen AzureRM-modules en vervolgens een andere taak te starten met behulp van alleen Az-modules, wat leidt tot een sandbox-crash.

### <a name="resolution"></a>Oplossing

Het gebruik van Az- en AzureRM-cmdlets in hetzelfde runbook wordt niet aanbevolen. Zie Migreren naar [Az-modules](../shared-resources/modules.md#migrate-to-az-modules)voor meer informatie over het juiste gebruik van de modules.

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>Scenario: Toegang geweigerd bij gebruik van Azure-sandbox voor runbook of toepassing

### <a name="issue"></a>Probleem

Wanneer uw runbook of toepassing probeert uit te voeren in een Azure-sandbox, wordt de toegang door de omgeving ontnomen.

### <a name="cause"></a>Oorzaak

Dit probleem kan optreden omdat Azure-sandboxes de toegang tot alle out-of-process COM-servers verhinderen. Een sandboxtoepassing of runbook kan bijvoorbeeld geen aanroepen naar Windows Management Instrumentation (WMI) of in de Windows Installer-service (msiserver.exe).

### <a name="resolution"></a>Oplossing

Zie Runbookuitvoeringsomgeving voor meer informatie over het gebruik van [Azure-sandboxes.](../automation-runbook-execution.md#runbook-execution-environment)

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>Scenario: Ongeldige statuscode verboden bij gebruik van Key Vault in een runbook

### <a name="issue"></a>Probleem

Wanneer u toegang probeert te krijgen Azure Key Vault een Azure Automation runbook, krijgt u de volgende foutmelding:

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>Oorzaak

Mogelijke oorzaken voor dit probleem zijn:

* Geen Uitvoeren als-account gebruiken.
* Onvoldoende machtigingen.

### <a name="resolution"></a>Oplossing

#### <a name="not-using-a-run-as-account"></a>Geen Uitvoeren als-account gebruiken

Volg [stap 5: verificatie toevoegen om Azure-resources](../learn/automation-tutorial-runbook-textual-powershell.md#step-5---add-authentication-to-manage-azure-resources) te beheren om ervoor te zorgen dat u een Uitvoeren als-account gebruikt voor toegang tot Key Vault.

#### <a name="insufficient-permissions"></a>Onvoldoende machtigingen

[Voeg machtigingen toe aan Key Vault](../manage-runas-account.md#add-permissions-to-key-vault) om ervoor te zorgen dat uw Uitvoeren als-account voldoende machtigingen heeft voor toegang tot Key Vault.

## <a name="recommended-documents"></a>Aanbevolen documenten

* [Uitvoering van runbook in Azure Automation](../automation-runbook-execution.md)
* [Een runbook starten in Azure Automation](../start-runbooks.md)

## <a name="next-steps"></a>Volgende stappen

Als u het probleem hier niet ziet of als u het probleem niet kunt oplossen, probeert u een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums.](https://azure.microsoft.com/support/forums/)
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) , het officiële Microsoft Azure account voor het verbeteren van de klantervaring. Ondersteuning voor Azure maakt u verbinding met de Azure-community voor antwoorden, ondersteuning en experts.
* Als u meer hulp nodig hebt, kunt u een ondersteuning voor Azure indienen. Ga naar de [ondersteuning voor Azure site](https://azure.microsoft.com/support/options/)en selecteer **Ondersteuning krijgen.**
