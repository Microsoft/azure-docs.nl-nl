---
title: Problemen met Azure Automation runbook oplossen
description: In dit artikel leest u hoe u problemen met Azure Automation runbooks oplost en oplost.
services: automation
ms.subservice: ''
ms.date: 11/03/2020
ms.topic: troubleshooting
ms.custom: has-adal-ref
ms.openlocfilehash: e154284df8eaad798c5cfaf4de69c40601863cf4
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053666"
---
# <a name="troubleshoot-runbook-issues"></a>Problemen met runbooks oplossen

 In dit artikel worden runbook-problemen beschreven die zich kunnen voordoen en hoe u deze kunt oplossen. Zie [Runbook-uitvoering in azure Automation](../automation-runbook-execution.md)voor algemene informatie.

## <a name="diagnose-runbook-issues"></a>Problemen met runbook vaststellen

Wanneer u fouten ontvangt tijdens het uitvoeren van een runbook in Azure Automation, kunt u de volgende stappen gebruiken om de problemen op te lossen:

1. Zorg ervoor dat het runbook-script correct wordt uitgevoerd op uw lokale machine.

    Zie [Power shell docs](/powershell/scripting/overview) of [python docs](https://docs.python.org/3/)(Engelstalig) voor naslag informatie en modules voor talen. Als u uw script lokaal uitvoert, kunnen veelvoorkomende fouten worden gedetecteerd en opgelost, zoals:

      * Ontbrekende modules
      * Syntaxis fouten
      * Logische fouten

1. [Fout stromen](../automation-runbook-output-and-messages.md#runbook-output)voor runbook onderzoeken.

    Bekijk deze streams voor specifieke berichten en vergelijk ze met de fouten die in dit artikel worden beschreven.

1. Zorg ervoor dat uw knoop punten en Automation-werk ruimte de vereiste modules hebben.

    Als uw runbook modules importeert, controleert u of deze beschikbaar zijn voor uw Automation-account met behulp van de stappen in [import modules](../shared-resources/modules.md#import-modules). Werk de Power shell-modules bij naar de meest recente versie door de instructies in [Update Azure PowerShell-modules in azure Automation](../automation-update-azure-modules.md)te volgen. Zie [problemen met modules oplossen](shared-resources.md#modules)voor meer informatie over probleem oplossing.

1. Als uw runbook wordt onderbroken of onverwacht mislukt:

    * [Vernieuw het certificaat](../manage-runas-account.md#cert-renewal) als het uitvoeren als-account is verlopen.
    * [Vernieuw de webhook](../automation-webhooks.md#renew-a-webhook) als u probeert een verlopen webhook te gebruiken om het runbook te starten.
    * [Controleer de status](../automation-runbook-execution.md#job-statuses) van de taak om de huidige runbooknaam en enkele mogelijke oorzaken van het probleem vast te stellen.
    * [Voeg extra uitvoer](../automation-runbook-output-and-messages.md#working-with-message-streams) aan het runbook toe om te bepalen wat er gebeurt voordat het runbook wordt onderbroken.
    * [Alle uitzonde ringen verwerken](../automation-runbook-execution.md#exceptions) die door uw taak worden gegenereerd.

1. Voer deze stap uit als de runbook-taak of de omgeving op Hybrid Runbook Worker niet reageert.

    Als u uw runbooks uitvoert op een Hybrid Runbook Worker in plaats van in Azure Automation, moet u mogelijk [de Hybrid worker zelf oplossen](hybrid-runbook-worker.md).

## <a name="scenario-runbook-fails-with-a-no-permission-or-forbidden-403-error"></a><a name="runbook-fails-no-permission"></a>Scenario: het Runbook is mislukt met een machtiging of een verboden 403-fout

### <a name="issue"></a>Probleem

Uw runbook mislukt met een machtiging zonder toestemming of verboden 403-fout, of gelijkwaardig.

### <a name="cause"></a>Oorzaak

Run as-accounts hebben mogelijk niet dezelfde machtigingen voor Azure-resources als uw huidige Automation-account. 

### <a name="resolution"></a>Oplossing

Zorg ervoor dat uw uitvoeren als-account [machtigingen heeft voor toegang tot alle resources](../../role-based-access-control/role-assignments-portal.md) die worden gebruikt in uw script.

## <a name="scenario-sign-in-to-azure-account-failed"></a><a name="sign-in-failed"></a>Scenario: aanmelden bij het Azure-account is mislukt

### <a name="issue"></a>Probleem

Er wordt een van de volgende fouten weer gegeven wanneer u met de `Connect-AzAccount` cmdlet werkt:

```error
Unknown_user_type: Unknown User Type
```

```error
No certificate was found in the certificate store with thumbprint
```

### <a name="cause"></a>Oorzaak

Deze fouten treden op als de naam van het referentie-element niet geldig is. Ze kunnen ook optreden als de gebruikers naam en het wacht woord die u hebt gebruikt voor het instellen van het Automation-referentie-element niet geldig zijn.

### <a name="resolution"></a>Oplossing

Voer de volgende stappen uit om te bepalen wat er mis is:

1. Zorg ervoor dat u geen speciale tekens hebt. Deze tekens bevatten het `\@` teken in de naam van het Automation-referentie-element dat u gebruikt om verbinding te maken met Azure.
1. Controleer of u de gebruikers naam en het wacht woord kunt gebruiken die zijn opgeslagen in de Azure Automation referentie in uw lokale Power shell ISE-editor. Voer de volgende cmdlets uit in de Power shell-ISE.

   ```powershell
   $Cred = Get-Credential
   #Using Azure Service Management
   Add-AzureAccount –Credential $Cred
   #Using Azure Resource Manager
   Connect-AzAccount –Credential $Cred
   ```

1. Als uw verificatie lokaal mislukt, hebt u de referenties van uw Azure Active Directory (Azure AD) niet juist ingesteld. Zie het artikel [verifiëren met Azure met Azure Active Directory](../automation-use-azure-ad.md)voor het correct instellen van het Azure ad-account.

1. Als de fout tijdelijk wordt weer gegeven, kunt u proberen logica toe te voegen aan uw verificatie routine om te zorgen voor een betere verificatie.

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

## <a name="scenario-run-login-azurermaccount-to-log-in"></a><a name="login-azurerm"></a>Scenario: Voer Login-AzureRMAccount uit om u aan te melden

### <a name="issue"></a>Probleem

Wanneer u een runbook uitvoert, wordt de volgende fout weer gegeven:

```error
Run Login-AzureRMAccount to login.
```

### <a name="cause"></a>Oorzaak

Deze fout kan optreden wanneer u geen uitvoeren als-account gebruikt of als het run as-account is verlopen. Zie [Azure Automation uitvoeren als-accounts Overview](../automation-security-overview.md#run-as-accounts)(Engelstalig) voor meer informatie.

Deze fout heeft twee primaire oorzaken:

* Er zijn verschillende versies van de AzureRM-of AZ-module.
* U probeert toegang te krijgen tot resources in een afzonderlijk abonnement.

### <a name="resolution"></a>Oplossing

Als deze fout melding wordt weer gegeven nadat u een AzureRM-of AZ-module hebt bijgewerkt, werkt u alle modules bij naar dezelfde versie.

Als u probeert toegang te krijgen tot resources in een ander abonnement, voert u de volgende stappen uit om machtigingen te configureren:

1. Ga naar het Automation uitvoeren als-account en kopieer de **toepassings-id** en **vinger afdruk**.

    ![Toepassings-ID en vinger afdruk kopiëren](../media/troubleshoot-runbooks/collect-app-id.png)

1. Ga naar de **toegangs beheer** locatie van het abonnement waarbij het Automation-account *niet* wordt gehost en voeg een nieuwe roltoewijzing toe.

    ![Toegangsbeheer](../media/troubleshoot-runbooks/access-control.png)

1. Voeg de **toepassings-id** die u eerder hebt verzameld, toe. Selecteer **Inzender** machtigingen.

    ![Roltoewijzing toevoegen](../media/troubleshoot-runbooks/add-role-assignment.png)

1. Kopieer de naam van het abonnement.

1. U kunt nu de volgende runbook-code gebruiken om de machtigingen van uw Automation-account te testen voor het andere abonnement. Vervang door `"\<CertificateThumbprint\>"` de waarde die u in stap 1 hebt gekopieerd. Vervang door `"\<SubscriptionName\>"` de waarde die u in stap 4 hebt gekopieerd.

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

## <a name="scenario-unable-to-find-the-azure-subscription"></a><a name="unable-to-find-subscription"></a>Scenario: het Azure-abonnement kan niet worden gevonden

### <a name="issue"></a>Probleem

U ontvangt de volgende fout melding wanneer u werkt met `Select-AzureSubscription` de `Select-AzureRMSubscription` cmdlet,, of `Select-AzSubscription` :

```error
The subscription named <subscription name> cannot be found.
```

### <a name="error"></a>Fout

Deze fout kan optreden als:

* De naam van het abonnement is niet geldig.
* De Azure AD-gebruiker die probeert de abonnements gegevens op te halen, is niet geconfigureerd als beheerder van het abonnement.
* De cmdlet is niet beschikbaar.

### <a name="resolution"></a>Oplossing

Volg deze stappen om te bepalen of u hebt geverifieerd voor Azure en toegang hebt tot het abonnement dat u wilt selecteren:

1. Om ervoor te zorgen dat uw script zelfstandig werkt, test u het buiten Azure Automation.
1. Zorg ervoor dat uw script de cmdlet [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount) uitvoert voordat u de `Select-*` cmdlet uitvoert.
1. Voeg `Disable-AzContextAutosave –Scope Process` aan het begin van uw runbook toe. Deze cmdlet zorgt ervoor dat alle referenties alleen van toepassing zijn op de uitvoering van het huidige runbook.
1. Als het fout bericht nog steeds wordt weer gegeven, wijzigt u de code door de para meter toe te voegen `AzContext` voor `Connect-AzAccount` en voert u de code uit.

   ```powershell
   Disable-AzContextAutosave –Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $context = Get-AzContext

   Get-AzVM -ResourceGroupName myResourceGroup -AzContext $context
    ```

## <a name="scenario-runbooks-fail-when-dealing-with-multiple-subscriptions"></a><a name="runbook-auth-failure"></a>Scenario: Runbooks mislukken bij het omgaan met meerdere abonnementen

### <a name="issue"></a>Probleem

Bij het uitvoeren van runbooks kan het runbook geen Azure-resources beheren.

### <a name="cause"></a>Oorzaak

Het runbook gebruikt niet de juiste context wanneer het wordt uitgevoerd.

### <a name="resolution"></a>Oplossing

De context van het abonnement kan verloren gaan wanneer een runbook meerdere runbooks aanroept. Als u er zeker van wilt zijn dat de context van het abonnement wordt door gegeven aan de runbooks, laat u het runbook van de client de context door geven aan de `Start-AzureRmAutomationRunbook` cmdlet in de `AzureRmContext` para meter. Gebruik de `Disable-AzureRmContextAutosave` cmdlet met de `Scope` para meter ingesteld op `Process` om ervoor te zorgen dat de opgegeven referenties alleen worden gebruikt voor het huidige runbook. Zie [Abonnementen](../automation-runbook-execution.md#subscriptions) voor meer informatie.

```azurepowershell-interactive
# Ensures that any credentials apply only to the execution of this runbook
Disable-AzContextAutosave –Scope Process

# Connect to Azure with Run As account
$ServicePrincipalConnection = Get-AutomationConnection -Name 'AzureRunAsConnection'

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $ServicePrincipalConnection.TenantId `
    -ApplicationId $ServicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $ServicePrincipalConnection.CertificateThumbprint

$AzContext = Select-AzSubscription -SubscriptionId $ServicePrincipalConnection.SubscriptionID

$params = @{"VMName"="MyVM";"RepeatCount"=2;"Restart"=$true}

Start-AzAutomationRunbook `
    –AutomationAccountName 'MyAutomationAccount' `
    –Name 'Test-ChildRunbook' `
    -ResourceGroupName 'LabRG' `
    -AzContext $AzContext `
    –Parameters $params –wait
```

## <a name="scenario-authentication-to-azure-fails-because-multifactor-authentication-is-enabled"></a><a name="auth-failed-mfa"></a>Scenario: authenticatie naar Azure mislukt omdat multi-factor Authentication is ingeschakeld

### <a name="issue"></a>Probleem

U ontvangt de volgende fout melding bij de verificatie bij Azure met uw Azure-gebruikers naam en-wacht woord:

```error
Add-AzureAccount: AADSTS50079: Strong authentication enrollment (proof-up) is required
```

### <a name="cause"></a>Oorzaak

Als u multi-factor Authentication voor uw Azure-account hebt, kunt u geen Azure Active Directory gebruiker gebruiken om te verifiëren bij Azure. In plaats daarvan moet u een certificaat of een Service-Principal gebruiken om te verifiëren.

### <a name="resolution"></a>Oplossing

Zie [een klassiek uitvoeren als-account maken voor het beheren van Azure-Services](../automation-create-standalone-account.md#create-a-classic-run-as-account)voor het gebruik van een klassiek uitvoeren als-account met cmdlets van het klassieke Azure-implementatie model. Zie Service-Principal [maken met behulp van Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) en [verificatie van een service-principal met Azure Resource Manager](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)om een service-principal met Azure Resource Manager-cmdlets te gebruiken.

## <a name="scenario-runbook-fails-with-a-task-was-canceled-error-message"></a><a name="task-was-cancelled"></a>Scenario: het fout bericht ' een taak is geannuleerd ' in het Runbook is mislukt

### <a name="issue"></a>Probleem

Het runbook is mislukt met een fout die vergelijkbaar is met het volgende voor beeld:

```error
Exception: A task was canceled.
```

### <a name="cause"></a>Oorzaak

Deze fout kan worden veroorzaakt door het gebruik van verouderde Azure-modules.

### <a name="resolution"></a>Oplossing

U kunt deze fout oplossen door uw Azure-modules bij te werken naar de nieuwste versie:

1. Selecteer **modules** in uw Automation-account en selecteer vervolgens **Azure-modules bijwerken**.
1. De update neemt ongeveer 15 minuten in beslag. Nadat de bewerking is voltooid, voert u het runbook dat is mislukt opnieuw uit.

Zie [Azure-modules bijwerken in azure Automation](../automation-update-azure-modules.md)voor meer informatie over het bijwerken van uw modules.

## <a name="scenario-term-not-recognized-as-the-name-of-a-cmdlet-function-or-script"></a><a name="not-recognized-as-cmdlet"></a>Scenario: term wordt niet herkend als de naam van een cmdlet, functie of script

### <a name="issue"></a>Probleem

Het runbook is mislukt met een fout die vergelijkbaar is met het volgende voor beeld:

```error
The term 'Connect-AzAccount' is not recognized as the name of a cmdlet, function, script file, or operable program. Check the spelling of the name, or if the path was included verify that the path is correct and try again.
```

### <a name="cause"></a>Oorzaak

Deze fout kan de volgende oorzaken hebben:

* De module die de cmdlet bevat, wordt niet geïmporteerd in het Automation-account.
* De module die de cmdlet bevat, is geïmporteerd, maar is verouderd.

### <a name="resolution"></a>Oplossing

Voer een van de volgende taken uit om deze fout op te lossen:

* Zie [Azure PowerShell-modules bijwerken in azure Automation](../automation-update-azure-modules.md) voor informatie over het bijwerken van uw modules in uw Automation-account voor een Azure-module.
* Zorg ervoor dat de module is geïmporteerd in uw Automation-account voor een niet-Azure-module.

## <a name="scenario-cmdlet-fails-in-pnp-powershell-runbook-on-azure-automation"></a>Scenario: cmdlet mislukt in PnP Power shell-runbook op Azure Automation

### <a name="issue"></a>Probleem

Wanneer een runbook een door een PnP-Power shell gegenereerd object naar de Azure Automation-uitvoer schrijft, kan de cmdlet-uitvoer niet weer streamen naar Automation.

### <a name="cause"></a>Oorzaak

Dit probleem treedt meestal op wanneer Azure Automation runbooks verwerkt die PnP-Power shell-cmdlets aanroepen, bijvoorbeeld, `add-pnplistitem` zonder dat de retour objecten worden onderschept.

### <a name="resolution"></a>Oplossing

Bewerk uw scripts om retour waarden toe te wijzen aan variabelen, zodat de cmdlets niet proberen om hele objecten te schrijven naar de standaard uitvoer. Een script kan de uitvoer stroom omleiden naar een cmdlet, zoals hier wordt weer gegeven.

```azurecli
  $null = add-pnplistitem
```

Als uw script cmdlet-uitvoer parseert, moet het script de uitvoer opslaan in een variabele en de variabele bewerken in plaats van de uitvoer simpelweg te streamen.

```azurecli
$SomeVariable = add-pnplistitem ....
if ($SomeVariable.someproperty -eq ....
```

## <a name="scenario-cmdlet-not-recognized-when-executing-a-runbook"></a><a name="cmdlet-not-recognized"></a>Scenario: cmdlet wordt niet herkend bij het uitvoeren van een runbook

### <a name="issue"></a>Probleem

De runbook-taak is mislukt met de volgende fout:

```error
<cmdlet name>: The term <cmdlet name> is not recognized as the name of a cmdlet, function, script file, or operable program.
```

### <a name="cause"></a>Oorzaak

Deze fout wordt veroorzaakt wanneer de Power shell-engine de cmdlet die u in uw runbook gebruikt, niet kan vinden. Het is mogelijk dat de module met de cmdlet ontbreekt in het account. er is een naam conflict met een runbooknaam, of de cmdlet bestaat ook in een andere module en de naam kan niet worden omgezet met Automation.

### <a name="resolution"></a>Oplossing

Gebruik een van de volgende oplossingen om het probleem op te lossen:

* Zorg ervoor dat u de naam van de cmdlet correct hebt ingevoerd.
* Zorg ervoor dat de cmdlet bestaat in uw Automation-account en dat er geen conflicten zijn. Als u wilt controleren of de cmdlet aanwezig is, opent u een runbook in de bewerkings modus en zoekt u naar de cmdlet die u wilt zoeken in de bibliotheek of voert u uit `Get-Command <CommandName>` . Nadat u hebt gecontroleerd of de cmdlet beschikbaar is voor het account en dat er geen naam conflicten met andere cmdlets of runbooks zijn, voegt u de cmdlet toe aan het canvas. Zorg ervoor dat u een geldige para meter in uw runbook gebruikt.
* Als er sprake is van een naam conflict en de cmdlet is beschikbaar in twee verschillende modules, lost u het probleem op door de volledig gekwalificeerde naam voor de cmdlet te gebruiken. U kunt bijvoorbeeld `ModuleName\CmdletName` gebruiken.
* Als u het runbook on-premises uitvoert in een Hybrid worker-groep, moet u ervoor zorgen dat de module en de cmdlet zijn geïnstalleerd op de computer die als host fungeert voor de Hybrid Worker.

## <a name="scenario-incorrect-object-reference-on-call-to-add-azaccount"></a><a name="object-reference-not-set"></a>Scenario: onjuiste object verwijzing bij het aanroepen van Add-AzAccount

### <a name="issue"></a>Probleem

U ontvangt deze fout melding wanneer u werkt met `Add-AzAccount` , een alias voor de `Connect-AzAccount` cmdlet:

```error
Add-AzAccount : Object reference not set to an instance of an object
```

### <a name="cause"></a>Oorzaak

Deze fout kan optreden als het runbook de juiste stappen niet uitvoert voordat wordt aangeroepen `Add-AzAccount` om het Automation-account toe te voegen. Een voor beeld van een van de benodigde stappen is aanmelden met een uitvoeren als-account. Zie [runbook-uitvoering in azure Automation](../automation-runbook-execution.md)voor de juiste bewerkingen voor het gebruik van uw runbook.

## <a name="scenario-object-reference-not-set-to-an-instance-of-an-object"></a><a name="child-runbook-object"></a>Scenario: de object verwijzing is niet ingesteld op een exemplaar van een object

### <a name="issue"></a>Probleem

U ontvangt de volgende fout wanneer u een onderliggend runbook aanroept met de `Wait` para meter en de uitvoer stroom bevat een object:

```error
Object reference not set to an instance of an object
```

### <a name="cause"></a>Oorzaak

Als de stroom objecten bevat, `Start-AzAutomationRunbook` wordt de uitvoer stroom niet op de juiste wijze verwerkt.

### <a name="resolution"></a>Oplossing

Implementeer een polling-logica en gebruik de cmdlet [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) om de uitvoer op te halen. Een voor beeld van deze logica wordt hier gedefinieerd:

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

## <a name="scenario-runbook-fails-because-of-deserialized-object"></a><a name="fails-deserialized-object"></a>Scenario: Runbook is mislukt vanwege een gedeserialiseerd object

### <a name="issue"></a>Probleem

Het runbook is mislukt met de volgende fout:

```error
Cannot bind parameter <ParameterName>.

Cannot convert the <ParameterType> value of type Deserialized <ParameterType> to type <ParameterType>.
```

### <a name="cause"></a>Oorzaak

Als uw runbook een Power shell-werk stroom is, worden complexe objecten in een gedeserialiseerd indeling opgeslagen om de status van het runbook te behouden als de werk stroom wordt onderbroken.

### <a name="resolution"></a>Oplossing

Gebruik een van de volgende oplossingen om dit probleem op te lossen:

* Als u complexe objecten van de ene cmdlet naar de andere wilt verpakken, plaatst u deze cmdlets in een `InlineScript` activiteit.
* Geef de naam of waarde die u nodig hebt uit het complexe object in plaats van het volledige object door te geven.
* Gebruik een Power shell-runbook in plaats van een Power shell workflow-runbook.

## <a name="scenario-400-bad-request-status-when-calling-a-webhook"></a><a name="expired webhook"></a>Scenario: 400 ongeldige aanvraag status bij het aanroepen van een webhook

### <a name="issue"></a>Probleem

Wanneer u probeert een webhook voor een Azure Automation runbook aan te roepen, wordt de volgende fout weer gegeven:

```error
400 Bad Request : This webhook has expired or is disabled
```

### <a name="cause"></a>Oorzaak

De webhook die u probeert aan te roepen, is uitgeschakeld of is verlopen. 

### <a name="resolution"></a>Oplossing

Als de webhook is uitgeschakeld, kunt u deze opnieuw inschakelen via de Azure Portal. Als de webhook is verlopen, moet u deze verwijderen en vervolgens opnieuw maken. U kunt [een webhook alleen vernieuwen](../automation-webhooks.md#renew-a-webhook) als deze nog niet is verlopen. 

## <a name="scenario-429-the-request-rate-is-currently-too-large"></a><a name="429"></a>Scenario: 429: de aanvraag frequentie is momenteel te groot

### <a name="issue"></a>Probleem

Wanneer u de cmdlet uitvoert, wordt het volgende fout bericht weer gegeven `Get-AzAutomationJobOutput` :

```error
429: The request rate is currently too large. Please try again
```

### <a name="cause"></a>Oorzaak

Deze fout kan optreden bij het ophalen van de taak uitvoer van een runbook met veel [uitgebreide stromen](../automation-runbook-output-and-messages.md#write-output-to-verbose-stream).

### <a name="resolution"></a>Oplossing

Voer een van de volgende handelingen uit om deze fout op te lossen:

* Bewerk het runbook en verminder het aantal taak stromen dat wordt verzonden.
* Verminder het aantal streams dat moet worden opgehaald bij het uitvoeren van de cmdlet. U kunt dit doen door de waarde van de `Stream` para meter voor de cmdlet [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) in te stellen op alleen uitvoer stromen op te halen. 

## <a name="scenario-runbook-job-fails-because-allocated-quota-was-exceeded"></a><a name="quota-exceeded"></a>Scenario: Runbook-taak is mislukt omdat het toegewezen quotum is overschreden

### <a name="issue"></a>Probleem

De runbook-taak is mislukt met de volgende fout:

```error
The quota for the monthly total job run time has been reached for this subscription
```

### <a name="cause"></a>Oorzaak

Deze fout treedt op wanneer de taak uitvoering het quotum van 500 minuten voor uw account overschrijdt. Dit quotum is van toepassing op alle typen taken voor taak uitvoering. Sommige van deze taken testen een taak, start een taak vanuit de portal, voert een taak uit met behulp van webhooks of een taak plannen om uit te voeren met behulp van de Azure Portal of uw Data Center. Zie [prijzen voor Automation](https://azure.microsoft.com/pricing/details/automation/)voor meer informatie over prijzen voor Automation.

### <a name="resolution"></a>Oplossing

Als u meer dan 500 minuten van verwerking per maand wilt gebruiken, wijzigt u uw abonnement van de laag gratis in de laag basis:

1. Meld u aan bij uw Azure-abonnement.
1. Selecteer het Automation-account dat u wilt bijwerken.
1. Selecteer **instellingen** en selecteer vervolgens **prijzen**.
1. Selecteer op de pagina onder **inschakelen** om uw account te upgraden naar de laag basis.

## <a name="scenario-runbook-output-stream-greater-than-1-mb"></a><a name="output-stream-greater-1mb"></a>Scenario: Runbook-uitvoer stroom groter dan 1 MB

### <a name="issue"></a>Probleem

Het runbook dat wordt uitgevoerd in de Azure sandbox mislukt met de volgende fout:

```error
The runbook job failed due to a job stream being larger than 1MB, this is the limit supported by an Azure Automation sandbox.
```

### <a name="cause"></a>Oorzaak

Deze fout treedt op omdat uw runbook heeft geprobeerd te veel uitzonderings gegevens te schrijven naar de uitvoer stroom.

### <a name="resolution"></a>Oplossing

Er is een limiet van 1 MB voor de uitvoer stroom van de taak. Zorg ervoor dat uw runbook aanroepen naar een uitvoerbaar bestand of subproces samensluit met behulp van `try` en `catch` blokken. Als de bewerkingen een uitzonde ring veroorzaken, laat u de code het bericht van de uitzonde ring in een Automation-variabele schrijven. Met deze techniek wordt voor komen dat het bericht naar de uitvoer stroom van de taak wordt geschreven. Voor Hybrid Runbook Worker taken die worden uitgevoerd, wordt de uitvoer stroom afgekapt tot 1 MB zonder fout bericht.

## <a name="scenario-runbook-job-start-attempted-three-times-but-fails-to-start-each-time"></a><a name="job-attempted-3-times"></a>Scenario: het starten van een Runbook-taak is drie keer gestart, maar kan niet elke keer worden gestart

### <a name="issue"></a>Probleem

Het runbook is mislukt met de volgende fout:

```error
The job was tried three times but it failed
```

### <a name="cause"></a>Oorzaak

Deze fout treedt op als gevolg van een van de volgende problemen:

* **Geheugen limiet.** Een taak kan mislukken als er meer dan 400 MB aan geheugen wordt gebruikt. De gedocumenteerde limieten voor geheugen die aan een sandbox zijn toegewezen, worden gevonden bij [limieten voor Automation-Service](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits). 

* **Netwerk sockets.** Azure-sandboxes zijn beperkt tot 1.000 gelijktijdige netwerk sockets. Zie voor meer informatie [Automation-Service limieten](../../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits).

* **Module is niet compatibel.** Module-afhankelijkheden zijn mogelijk niet juist. In dit geval retourneert uw runbook meestal een `Command not found` of- `Cannot bind parameter` bericht.

* **Geen verificatie met Active Directory voor sandbox.** Uw runbook heeft geprobeerd een uitvoerbaar of subproces dat wordt uitgevoerd in een Azure sandbox aan te roepen. Het configureren van runbooks om te verifiëren met Azure AD met behulp van de Azure Active Directory Authentication Library (ADAL) wordt niet ondersteund.

### <a name="resolution"></a>Oplossing

* **Geheugen limiet, netwerk sockets.** Voorgestelde manieren om binnen de geheugen limieten te werken, zijn om de werk belasting over meerdere runbooks te verdelen, minder gegevens in het geheugen te verwerken, overbodige uitvoer van runbooks te vermijden en te bepalen hoeveel controle punten worden geschreven naar uw runbooks in Power shell workflow. Gebruik de methode Clear, zoals `$myVar.clear` , om variabelen te wissen en `[GC]::Collect` direct te gebruiken om garbagecollection onmiddellijk uit te voeren. Deze acties verminderen het geheugen gebruik van uw runbook tijdens runtime.

* **Module is niet compatibel.** Werk uw Azure-modules bij door de stappen te volgen in de [Azure Automation Azure PowerShell-modules bij te werken](../automation-update-azure-modules.md).

* **Geen verificatie met Active Directory voor sandbox.** Wanneer u zich met een runbook verifieert bij Azure AD, moet u ervoor zorgen dat de Azure AD-module beschikbaar is in uw Automation-account. Zorg ervoor dat u het uitvoeren als-account de benodigde machtigingen verleent om de taken uit te voeren die door het runbook worden geautomatiseerd.

  Als uw runbook geen uitvoerbaar of subproces kan aanroepen dat wordt uitgevoerd in een Azure-sandbox, gebruikt u het runbook op een [Hybrid Runbook worker](../automation-hrw-run-runbooks.md). Hybrid Workers worden niet beperkt door de geheugen-en netwerk limieten die Azure-sandboxes hebben.

## <a name="scenario-powershell-job-fails-with-cannot-invoke-method-error-message"></a><a name="cannot-invoke-method"></a>Scenario: Power shell-taak mislukt met fout bericht ' kan methode niet aanroepen '

### <a name="issue"></a>Probleem

Het volgende fout bericht wordt weer gegeven wanneer u een Power shell-taak start in een runbook dat wordt uitgevoerd in Azure:

```error
Exception was thrown - Cannot invoke method. Method invocation is supported only on core types in this language mode.
```

### <a name="cause"></a>Oorzaak

Deze fout kan erop wijzen dat runbooks die worden uitgevoerd in een Azure-sandbox, niet kunnen worden uitgevoerd in de [volledige taal modus](/powershell/module/microsoft.powershell.core/about/about_language_modes).

### <a name="resolution"></a>Oplossing

Er zijn twee manieren om deze fout op te lossen:

* Gebruik start [-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) in plaats van de [Start-taak](/powershell/module/microsoft.powershell.core/start-job)te gebruiken om het runbook te starten.
* Probeer het runbook uit te voeren op een Hybrid Runbook Worker.

Zie voor meer informatie over dit gedrag en ander gedrag van Azure Automation runbooks [Runbook-uitvoering in azure Automation](../automation-runbook-execution.md).

## <a name="scenario-a-long-running-runbook-fails-to-complete"></a><a name="long-running-runbook"></a>Scenario: een langlopend runbook wordt niet voltooid

### <a name="issue"></a>Probleem

Het runbook wordt na drie uur weer gegeven met de status gestopt. Deze fout kan ook worden weer gegeven:

```error
The job was evicted and subsequently reached a Stopped state. The job cannot continue running.
```

Dit gedrag is inherent aan het ontwerp van Azure-sandboxes vanwege de [billijke](../automation-runbook-execution.md#fair-share) bewaking van processen binnen Azure Automation. Als een proces langer dan drie uur wordt uitgevoerd, stopt de billijke share automatisch een runbook. De status van een runbook dat de duur van de reële share overschrijdt, verschilt per runbook-type. Power shell-en python-runbooks zijn ingesteld op de status gestopt. De Power shell-werk stroom-runbooks zijn ingesteld op mislukt.

### <a name="cause"></a>Oorzaak

Het runbook is uitgevoerd gedurende een periode van drie uur dat is toegestaan door een billijke share in een Azure-sandbox.

### <a name="resolution"></a>Oplossing

Een aanbevolen oplossing is om het runbook uit te voeren op een [Hybrid Runbook worker](../automation-hrw-run-runbooks.md). Hybrid Workers worden niet beperkt door de limiet van drie uur voor de billijke delen van Azure-sandboxes. Runbooks die worden uitgevoerd op Hybrid Runbook Workers moeten worden ontwikkeld ter ondersteuning van het opnieuw starten van gedrag als er onverwachte problemen zijn met de lokale infra structuur.

Een andere oplossing is het optimaliseren van het runbook door [onderliggende runbooks](../automation-child-runbooks.md)te maken. Als uw runbook met dezelfde functie wordt uitgevoerd op verschillende resources, bijvoorbeeld in een database bewerking op verschillende data bases, kunt u de functie verplaatsen naar een onderliggend runbook. Elk onderliggend runbook wordt parallel uitgevoerd in een afzonderlijk proces. Dit gedrag vermindert de totale tijd voor het volt ooien van het bovenliggende runbook.

De Power shell-cmdlets waarmee het onderliggende runbook-scenario wordt ingeschakeld, zijn:

* [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook). met deze cmdlet kunt u een runbook starten en parameters aan het runbook doorgeven.
* [Get-AzAutomationJob](/powershell/module/Az.Automation/Get-AzAutomationJob). Als er bewerkingen zijn die moeten worden uitgevoerd nadat het onderliggende runbook is voltooid, kunt u met deze cmdlet de taak status voor elk onderliggend item controleren.

## <a name="scenario-error-in-job-streams-about-the-get_serializationsettings-method"></a><a name="get-serializationsettings"></a>Scenario: fout in taak stromen over de methode get_SerializationSettings

### <a name="issue"></a>Probleem

U ziet de volgende fout in uw taak stromen voor een runbook:

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

Deze fout wordt waarschijnlijk veroorzaakt door het gebruik van een onvolledige migratie van AzureRM naar AZ-modules in uw runbook. Dit kan leiden tot Azure Automation om een runbook-taak te starten door alleen AzureRM-modules te gebruiken en vervolgens een andere taak te starten met alleen AZ-modules, wat leidt tot een storing in de sandbox.

### <a name="resolution"></a>Oplossing

Het gebruik van AZ en AzureRM-cmdlets in hetzelfde runbook wordt niet aanbevolen. Zie [migreren naar AZ-modules](../shared-resources/modules.md#migrate-to-az-modules)voor meer informatie over het juiste gebruik van de modules.

## <a name="scenario-access-denied-when-using-azure-sandbox-for-runbook-or-application"></a><a name="access-denied-azure-sandbox"></a>Scenario: toegang geweigerd bij gebruik van Azure sandbox voor runbook of toepassing

### <a name="issue"></a>Probleem

Wanneer uw runbook of toepassing probeert uit te voeren in een Azure-sandbox, weigert de toegang tot de omgeving.

### <a name="cause"></a>Oorzaak

Dit probleem kan optreden omdat Azure-sandboxes geen toegang tot alle out-of-process COM-servers. Zo kan een toepassing of runbook in de sandbox niet aanroepen naar Windows Management Instrumentation (WMI) of naar de Windows Installer-service (msiserver.exe).

### <a name="resolution"></a>Oplossing

Zie voor meer informatie over het gebruik van Azure-sandboxs de [Runbook Execution Environment](../automation-runbook-execution.md#runbook-execution-environment).

## <a name="scenario-invalid-forbidden-status-code-when-using-key-vault-inside-a-runbook"></a>Scenario: ongeldige status code bij het gebruik van Key Vault in een runbook

### <a name="issue"></a>Probleem

Wanneer u probeert toegang te krijgen tot Azure Key Vault via een Azure Automation runbook, wordt de volgende fout weer geven:

```error
Operation returned an invalid status code 'Forbidden'
```

### <a name="cause"></a>Oorzaak

Mogelijke oorzaken van dit probleem zijn:

* Er wordt geen uitvoeren als-account gebruikt.
* Onvoldoende machtigingen.

### <a name="resolution"></a>Oplossing

#### <a name="not-using-a-run-as-account"></a>Geen uitvoeren als-account gebruiken

Volg [stap 5: Voeg verificatie toe om Azure-resources te beheren](../learn/automation-tutorial-runbook-textual-powershell.md#step-5---add-authentication-to-manage-azure-resources) om ervoor te zorgen dat u een uitvoeren als-account gebruikt om toegang te krijgen tot Key Vault.

#### <a name="insufficient-permissions"></a>Onvoldoende machtigingen

[Voeg machtigingen toe aan Key Vault](../manage-runas-account.md#add-permissions-to-key-vault) om ervoor te zorgen dat uw uitvoeren als-account voldoende machtigingen heeft voor toegang tot Key Vault.

## <a name="recommended-documents"></a>Aanbevolen documenten

* [Uitvoering van Runbook in Azure Automation](../automation-runbook-execution.md)
* [Een runbook starten in Azure Automation](../start-runbooks.md)

## <a name="next-steps"></a>Volgende stappen

Als uw probleem hier niet wordt weer gegeven of u het probleem niet kunt oplossen, kunt u een van de volgende kanalen proberen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure-forums](https://azure.microsoft.com/support/forums/).
* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) , het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Met Azure-ondersteuning kunt u verbinding maken met de Azure-Community voor antwoorden, ondersteuning en experts.
* Als u meer hulp nodig hebt, kunt u een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/)en selecteer **ondersteuning verkrijgen**.
