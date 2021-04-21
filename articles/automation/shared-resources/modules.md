---
title: Modules beheren in Azure Automation
description: In dit artikel wordt beschreven hoe u PowerShell-modules gebruikt voor het inschakelen van cmdlets in runbooks en DSC-resources in DSC-configuraties.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/01/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: eaff96907b48ddc0fc92296a015ceb063149e6ec
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832748"
---
# <a name="manage-modules-in-azure-automation"></a>Modules beheren in Azure Automation

Azure Automation maakt gebruik van een aantal PowerShell-modules voor het inschakelen van cmdlets in runbooks en DSC-resources in DSC-configuraties. Ondersteunde modules zijn onder andere:

* [Azure PowerShell Az.Automation](/powershell/azure/new-azureps-module-az).
* [Azure PowerShell AzureRM.Automation](/powershell/module/azurerm.automation/).
* Andere PowerShell-modules.
* Interne `Orchestrator.AssetManagement.Cmdlets` module.
* Python 2-modules.
* Aangepaste modules die u maakt.

Wanneer u een Automation-account maakt, worden Azure Automation modules standaard geïmporteerd. Zie [Standaardmodules.](#default-modules)

## <a name="sandboxes"></a>Sandboxes

Wanneer Automation runbook- en DSC-compilatietaken uitvoert, worden de modules in sandboxes geladen waar de runbooks kunnen worden uitgevoerd en de DSC-configuraties kunnen worden gecompileert. Automatisering plaatst ook automatisch DSC-resources in modules op de DSC-pull-server. Machines kunnen de resources pullen wanneer ze de DSC-configuraties toepassen.

>[!NOTE]
>Zorg ervoor dat u alleen de modules importeert die uw runbooks en DSC-configuraties nodig hebben. Het is niet raadzaam om de Az-hoofdmodule te importeren. Het bevat veel andere modules die u mogelijk niet nodig hebt, wat prestatieproblemen kan veroorzaken. Importeer in plaats daarvan afzonderlijke modules, zoals Az.Compute.

Cloud-sandbox ondersteunt maximaal 48 systeemoproepen en beperkt alle andere aanroepen uit veiligheidsoverwegingen. Andere functionaliteit, zoals referentiebeheer en sommige netwerken, wordt niet ondersteund in de cloud-sandbox.

Vanwege het aantal modules en cmdlets dat is opgenomen, is het moeilijk om van tevoren te weten welke cmdlets niet-ondersteunde aanroepen doen. Over het algemeen hebben we problemen gezien met cmdlets waarvoor verhoogde toegang is vereist, waarvoor een referentie is vereist als parameter of cmdlets met betrekking tot netwerken. Cmdlets die volledige stack netwerkbewerkingen uitvoeren, worden niet ondersteund in de sandbox, met inbegrip van [Connect-AipService](/powershell/module/aipservice/connect-aipservice) van de AIPService PowerShell-module en [Resolve-DnsName](/powershell/module/dnsclient/resolve-dnsname) van de DNSClient-module.

Dit zijn bekende beperkingen met betrekking tot de sandbox. De aanbevolen tijdelijke oplossing is het implementeren van een [Hybrid Runbook Worker](../automation-hybrid-runbook-worker.md) of het gebruik [van Azure Functions](../../azure-functions/functions-overview.md).

## <a name="default-modules"></a>Standaardmodules

De volgende tabel bevat modules die Azure Automation standaard worden geïmporteerd wanneer u uw Automation-account maakt. Automatisering kan nieuwere versies van deze modules importeren. U kunt de oorspronkelijke versie echter niet verwijderen uit uw Automation-account, zelfs niet als u een nieuwere versie verwijdert. Houd er rekening mee dat deze standaardmodules verschillende AzureRM-modules bevatten.

De standaardmodules worden ook wel globale modules genoemd. In de Azure Portal is de **eigenschap Globale module** **waar** wanneer u een module bekijkt die is geïmporteerd toen het account werd gemaakt.

![Schermopname van de globale module-eigenschap in Azure Portal](../media/modules/automation-global-modules.png)

Automation importeert de Az-hoofdmodule niet automatisch in nieuwe of bestaande Automation-accounts. Zie Migreren naar [Az-modules](#migrate-to-az-modules)voor meer informatie over het werken met deze modules.

> [!NOTE]
> Het is niet raadzaam om modules en runbooks te wijzigen in Automation-accounts die worden gebruikt voor de implementatie van [VM's buiten bedrijfsuren starten/stoppen](../automation-solution-vm-management.md) functie.

|Modulenaam|Versie|
|---|---|
| AuditPolicyDsc | 1.1.0.0 |
| Azure | 1.0.3 |
| Azure.Storage | 1.0.3 |
| AzureRM.Automation | 1.0.3 |
| AzureRM.Compute | 1.2.1 |
| AzureRM.Profile | 1.0.3 |
| AzureRM.Resources | 1.0.3 |
| AzureRM.Sql | 1.0.3 |
| AzureRM.Storage | 1.0.3 |
| ComputerManagementDsc | 5.0.0.0 |
| GPRegistryPolicyParser | 0,2 |
| Microsoft.PowerShell.Core | 0 |
| Microsoft.PowerShell.Diagnostics |  |
| Microsoft.PowerShell.Management |  |
| Microsoft.PowerShell.Security |  |
| Microsoft.PowerShell.Utility |  |
| Microsoft.WSMan.Management |  |
| Orchestrator.AssetManagement.Cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-modules"></a>Az-modules

Voor Az.Automation hebben de meeste cmdlets dezelfde namen als de namen die worden gebruikt voor de AzureRM-modules, behalve dat het voorvoegsel `AzureRM` is gewijzigd in `Az` . Zie de lijst met uitzonderingen voor een lijst met Az-modules die deze naamconventie [niet volgen.](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)

## <a name="internal-cmdlets"></a>Interne cmdlets

Azure Automation ondersteunt de interne `Orchestrator.AssetManagement.Cmdlets` module voor de Log Analytics-agent voor Windows, die standaard is geïnstalleerd. De volgende tabel definieert de interne cmdlets. Deze cmdlets zijn ontworpen om te worden gebruikt in plaats van Azure PowerShell cmdlets om te communiceren met gedeelde resources. Ze kunnen geheimen ophalen uit versleutelde variabelen, referenties en versleutelde verbindingen.

>[!NOTE]
>De interne cmdlets zijn alleen beschikbaar wanneer u runbooks in de Azure-sandboxomgeving of op een Windows-Hybrid Runbook Worker. 

|Naam|Beschrijving|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Houd er rekening mee dat de interne cmdlets verschillen in naamgeving van de Az- en AzureRM-cmdlets. Interne cmdlet-namen bevatten geen woorden zoals of in het zelfstandig naamwoord `Azure` , maar gebruiken wel het woord `Az` `Automation` . Het gebruik ervan wordt aanbevolen voor het gebruik van Az- of AzureRM-cmdlets tijdens het uitvoeren van runbook in een Azure-sandbox of op een Windows-Hybrid Runbook Worker. Ze vereisen minder parameters en worden uitgevoerd in de context van uw taak die al wordt uitgevoerd.

Gebruik Az- of AzureRM-cmdlets voor het bewerken van Automation-resources buiten de context van een runbook. 

## <a name="python-modules"></a>Python-modules

U kunt Python 2-runbooks maken in Azure Automation. Zie Python 2-pakketten beheren in Azure Automation voor informatie over [de Python Azure Automation.](../python-packages.md)

## <a name="custom-modules"></a>Aangepaste modules

Azure Automation ondersteunt aangepaste PowerShell-modules die u maakt voor gebruik met uw runbooks en DSC-configuraties. Een type aangepaste module is een integratiemodule die eventueel een bestand met metagegevens bevat om de aangepaste functionaliteit voor de module-cmdlets te definiëren. Een voorbeeld van het gebruik van een integratiemodule is beschikbaar in [Een verbindingstype toevoegen.](../automation-connections.md#add-a-connection-type)

Azure Automation kunt een aangepaste module importeren om de cmdlets beschikbaar te maken. Achter de schermen slaat het de module op en gebruikt deze in de Azure-sandboxes, net als bij andere modules.

## <a name="migrate-to-az-modules"></a>Migreren naar Az-modules

Deze sectie laat zien hoe u migreert naar de Az-modules in Automation. Zie Migrate [Azure PowerShell from AzureRM to Az (Migratie van AzureRM naar Az) voor meer informatie.](/powershell/azure/migrate-from-azurerm-to-az)

Het wordt niet aangeraden Om AzureRM-modules en Az-modules uit te werken in hetzelfde Automation-account. Wanneer u zeker weet dat u wilt migreren van AzureRM naar Az, kunt u het beste een volledige migratie uitvoeren. Automation hergebruikt sandboxes vaak in het Automation-account om opstarttijden te besparen. Als u geen volledige modulemigratie maakt, kunt u een taak starten die alleen AzureRM-modules gebruikt en vervolgens een andere taak starten die alleen Az-modules gebruikt. De sandbox loopt binnenkort vast en u krijgt een foutmelding dat de modules niet compatibel zijn. Deze situatie resulteert in willekeurig optredende crashes voor een bepaald runbook of bepaalde configuratie.

>[!NOTE]
>Wanneer u een nieuw Automation-account maakt, worden de AzureRM-modules standaard geïnstalleerd, zelfs na de migratie naar Az-modules. U kunt de zelfstudierunbooks nog steeds bijwerken met de AzureRM-cmdlets. U moet deze runbooks echter niet uitvoeren.

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Uw runbooks en DSC-configuraties testen vóór de modulemigratie

Zorg ervoor dat u alle runbooks en DSC-configuraties zorgvuldig test, in een afzonderlijk Automation-account, voordat u naar de Az-modules migreert. 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Alle runbooks die gebruikmaken van AzureRM-modules stoppen en de plannen ervan op de scheepen

Om ervoor te zorgen dat u geen bestaande runbooks of DSC-configuraties die gebruikmaken van AzureRM-modules, uit te voeren, moet u alle betrokken runbooks en configuraties stoppen en de inrichting ervan in de weg staan. Controleer eerst elk runbook of de DSC-configuratie en de planningen afzonderlijk, om ervoor te zorgen dat u het item indien nodig in de toekomst opnieuw kunt plannen.

Wanneer u klaar bent om uw planningen te verwijderen, kunt u de Azure Portal of de cmdlet [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) gebruiken. Zie [Een schema verwijderen.](schedules.md#remove-a-schedule)

### <a name="remove-azurerm-modules"></a>AzureRM-modules verwijderen

Het is mogelijk om de AzureRM-modules te verwijderen voordat u de Az-modules importeert. Als u dit wel doet, kunt u de synchronisatie van broncodebeheer onderbreken en ervoor zorgen dat scripts die nog steeds zijn gepland, mislukken. Zie AzureRM verwijderen als u besluit de modules [te verwijderen.](/powershell/azure/migrate-from-azurerm-to-az#uninstall-azurerm)

### <a name="import-az-modules"></a>Az-modules importeren

Als u een Az-module importeert in uw Automation-account, wordt de module niet automatisch geïmporteerd in de PowerShell-sessie die door runbooks wordt gebruikt. Modules worden in de volgende situaties geïmporteerd in de PowerShell-sessie:

* Wanneer een runbook een cmdlet aanroept vanuit een module.
* Wanneer een runbook de module expliciet importeert met de cmdlet [Import-Module.](/powershell/module/microsoft.powershell.core/import-module)
* Wanneer een runbook de module expliciet importeert met de [instructie using module.](/powershell/module/microsoft.powershell.core/about/about_using#module-syntax) De using-instructie wordt ondersteund vanaf Windows PowerShell 5.0 en ondersteunt klassen en enumtype importeren.
* Wanneer een runbook een andere afhankelijke module importeert.

U kunt de Az-modules importeren in het Automation-account vanuit Azure Portal. Vergeet niet om alleen de Az-modules te importeren die u nodig hebt, niet elke Beschikbare Az-module. Omdat [Az.Accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) een afhankelijkheid is voor de andere Az-modules, moet u deze module vóór andere modules importeren.

1. Selecteer vanuit uw Automation-account onder **Gedeelde resources** de optie **Modules.**
2. Selecteer **Bladeren in galerie**.  
3. Voer in de zoekbalk de modulenaam in (bijvoorbeeld `Az.Accounts` ).
4. Selecteer op de pagina PowerShell-module **importeren om** de module in uw Automation-account te importeren.

    ![Schermopname van het importeren van modules in uw Automation-account](../media/modules/import-module.png)

U kunt dit importeren ook doen via de [PowerShell Gallery](https://www.powershellgallery.com), door te zoeken naar de module die u wilt importeren. Wanneer u de module vindt, selecteert u deze en kiest u **Azure Automation** tabblad. Selecteer **Implementeren om Azure Automation.**

![Schermopname van het rechtstreeks importeren van modules PowerShell Gallery](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Uw runbooks testen

Nadat u de Az-modules hebt geïmporteerd in het Automation-account, kunt u beginnen met het bewerken van uw runbooks en DSC-configuraties om de nieuwe modules te gebruiken. Een manier om de wijziging van een runbook voor het gebruik van de nieuwe cmdlets te testen, is met behulp van de opdracht aan het `Enable-AzureRmAlias -Scope Process` begin van het runbook. Door deze opdracht toe te voegen aan uw runbook, kan het script zonder wijzigingen worden uitgevoerd.

## <a name="author-modules"></a>Auteursmodules

U wordt aangeraden de overwegingen in deze sectie te volgen wanneer u een aangepaste PowerShell-module maakt voor gebruik in Azure Automation. Als u de module wilt voorbereiden voor import, moet u ten minste een .psd1-, .psm1- of PowerShell-module **DLL-bestand** maken met dezelfde naam als de modulemap. Vervolgens zipt u de modulemap zodat Azure Automation als één bestand kunt importeren. Het **ZIP-pakket** moet dezelfde naam hebben als de ingesloten modulemap.

Zie How to Write a PowerShell Script Module (Een PowerShell-scriptmodule schrijven) voor meer informatie over het schrijven van [een PowerShell-module.](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)

### <a name="version-folder"></a>Versiemap

Met versiebeheer van PowerShell-modules naast elkaar kunt u meer dan één versie van een module in PowerShell gebruiken. Dit kan handig zijn als u oudere scripts hebt die zijn getest en alleen werken met een bepaalde versie van een PowerShell-module, maar voor andere scripts is een nieuwere versie van dezelfde PowerShell-module vereist.

Als u PowerShell-modules zo wilt maken dat ze meerdere versies bevatten, maakt u de modulemap en maakt u vervolgens een map in deze modulemap voor elke versie van de module die u wilt gebruiken. In het volgende voorbeeld biedt een module met de *naam TestModule* twee versies: 1.0.0 en 2.0.0.

```dos
TestModule
   1.0.0
   2.0.0
```

Kopieer in elk van de versiemappen uw .psm1-, .psd1- of PowerShell-module **DLL-bestanden** waaruit een module bestaat, naar de respectieve versiemap. Zip de modulemap zodat Azure Automation als één ZIP-bestand kan importeren. In Automation wordt alleen de hoogste versie van de geïmporteerde module weergeven, maar als het modulepakket naast elkaar versies van de module bevat, zijn ze allemaal beschikbaar voor gebruik in uw runbooks of DSC-configuraties.  

Hoewel Automation modules ondersteunt die naast elkaar versies binnen hetzelfde pakket bevatten, biedt het geen ondersteuning voor het gebruik van meerdere versies van een module in modulepakketimports. U importeert bijvoorbeeld **module A**, die versies 1 en 2 bevat, in uw Automation-account. Later werkt u **module A** bij met versies 3 en 4. Wanneer u importeert in uw Automation-account, kunnen alleen de versies 3 en 4 worden gebruikt in runbooks of DSC-configuraties. Als u wilt dat alle versies - 1, 2, 3 en 4 beschikbaar zijn, moet het ZIP-bestand dat u importeert versies 1, 2, 3 en 4 bevatten.

Als u verschillende versies van dezelfde module tussen runbooks wilt gebruiken, moet u altijd de versie declareer die u in uw runbook wilt gebruiken met behulp van de cmdlet en de `Import-Module` parameter `-RequiredVersion <version>` opnemen. Zelfs als de versie die u wilt gebruiken de nieuwste versie is. Dit komt doordat runbooktaken in dezelfde sandbox kunnen worden uitgevoerd. Als de sandbox al expliciet een module met een bepaald versienummer heeft geladen, omdat een eerdere taak in die sandbox dit heeft gezegd, worden in toekomstige taken in die sandbox niet automatisch de nieuwste versie van die module geladen. Dit komt doordat een bepaalde versie ervan al in de sandbox is geladen.

Gebruik voor een DSC-resource de volgende opdracht om een bepaalde versie op te geven:

```powershell
Import-DscResource -ModuleName <ModuleName> -ModuleVersion <version>
```

### <a name="help-information"></a>Help-informatie

Neem een samenvatting, beschrijving en help-URI op voor elke cmdlet in uw module. In PowerShell kunt u help-informatie voor cmdlets definiëren met behulp van de `Get-Help` cmdlet . In het volgende voorbeeld ziet u hoe u een samenvatting en help-URI definieert in een **.psm1-modulebestand.**

  ```powershell
  <#
       .SYNOPSIS
        Gets a Contoso User account
  #>
  function Get-ContosoUser {
  [CmdletBinding](DefaultParameterSetName='UseConnectionObject', `
  HelpUri='https://www.contoso.com/docs/information')]
  [OutputType([String])]
  param(
     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $UserName,

     [Parameter(ParameterSetName='UserAccount', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [string]
     $Password,

     [Parameter(ParameterSetName='ConnectionObject', Mandatory=true)]
     [ValidateNotNullOrEmpty()]
     [Hashtable]
     $Connection
  )

  switch ($PSCmdlet.ParameterSetName) {
     "UserAccount" {
        $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Door deze informatie op te geven, wordt Help-tekst via `Get-Help` de cmdlet in de PowerShell-console weer gegeven. Deze tekst wordt ook weergegeven in de Azure Portal.

  ![Schermopname van help voor integratiemodule](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Type verbinding

Als de module verbinding maakt met een externe service, definieert u een verbindingstype met behulp van [een aangepaste integratiemodule](#custom-modules). Elke cmdlet in de module moet een exemplaar van dat verbindingstype (verbindingsobject) accepteren als parameter. Telkens wanneer gebruikers een cmdlet aanroepen, geven gebruikers parameters van de verbindingsactivum toe aan de bijbehorende parameters van de cmdlet.

![Een aangepaste verbinding gebruiken in de Azure Portal](../media/modules/connection-create-new.png)

In het volgende runbookvoorbeeld wordt een Contoso-verbindingsactivum met de naam gebruikt voor toegang tot Contoso-resources en het retourneren van gegevens `ContosoConnection` van de externe service. In dit voorbeeld worden de velden aan de eigenschappen en van een object en `UserName` vervolgens doorgegeven aan de `Password` `PSCredential` cmdlet .

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

Een eenvoudigere en betere manier om dit gedrag te benaderen, is door het verbindingsobject rechtstreeks door te geven aan de cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

U kunt soortgelijk gedrag voor uw cmdlets inschakelen door ze toe te staan om een verbindingsobject rechtstreeks als parameter te accepteren, in plaats van alleen verbindingsvelden voor parameters. Meestal wilt u voor elke parameter een set parameters instellen, zodat een gebruiker die automation niet gebruikt, uw cmdlets kan aanroepen zonder een hashtabel te maken om te fungeren als het verbindingsobject. De parameterset wordt `UserAccount` gebruikt om de eigenschappen van het verbindingsveld door te geven. `ConnectionObject` hiermee kunt u de verbinding rechtstreeks doorgeven.

### <a name="output-type"></a>Uitvoertype

Definieer het uitvoertype voor alle cmdlets in uw module. Als u een uitvoertype voor een cmdlet definieert, kan IntelliSense tijdens het ontwerpen helpen bij het bepalen van de uitvoereigenschappen van de cmdlet. Deze praktijk is vooral nuttig tijdens het ontwerpen van grafische runbooks, waarvoor kennis van ontwerptijd essentieel is voor een eenvoudige gebruikerservaring met uw module.

Voeg `[OutputType([<MyOutputType>])]` toe, waarbij `MyOutputType` een geldig type is. Zie Over Functions `OutputType` [OutputTypeAttribute voor meer informatie](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)over . De volgende code is een voorbeeld van het toevoegen `OutputType` aan een cmdlet:

  ```powershell
  function Get-ContosoUser {
  [OutputType([String])]
  param(
     [string]
     $Parameter1
  )
  # <script location here>
  }
  ```

  ![Schermopname van het uitvoertype van het grafische runbook](../media/modules/runbook-graphical-module-output-type.png)

  Dit gedrag is vergelijkbaar met de 'type ahead'-functionaliteit van de uitvoer van een cmdlet in de PowerShell-integratieserviceomgeving, zonder deze uit te voeren.

  ![Schermopname van POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Cmdlet-status

Maak alle cmdlets in uw module staatloos. Meerdere runbooktaken kunnen tegelijkertijd worden uitgevoerd in hetzelfde proces en `AppDomain` dezelfde sandbox. Als er een status op deze niveaus wordt gedeeld, kunnen taken van invloed zijn op elkaar. Dit gedrag kan leiden tot onregelmatige en moeilijk te diagnosticeren problemen. Hier is een voorbeeld van wat u niet moet doen:

  ```powershell
  $globalNum = 0
  function Set-GlobalNum {
     param(
         [int] $num
     )

     $globalNum = $num
  }
  function Get-GlobalNumTimesTwo {
     $output = $globalNum * 2

     $output
  }
  ```

### <a name="module-dependency"></a>Moduleafhankelijkheid

Zorg ervoor dat de module volledig is opgenomen in een pakket dat kan worden gekopieerd met behulp van xcopy. Automation-modules worden gedistribueerd naar de Automation-sandboxes wanneer runbooks worden uitgevoerd. De modules moeten onafhankelijk van de host werken die ze wordt uitgevoerd.

U moet een modulepakket kunnen zip-upen en verplaatsen, en het pakket normaal laten werken wanneer het wordt geïmporteerd in de PowerShell-omgeving van een andere host. Zorg ervoor dat de module niet afhankelijk is van bestanden buiten de modulemap die wordt ingepakt wanneer de module in Automation wordt geïmporteerd.

Uw module mag niet afhankelijk zijn van unieke registerinstellingen op een host. Voorbeelden zijn de instellingen die worden gemaakt wanneer een product wordt geïnstalleerd.

### <a name="module-file-paths"></a>Modulebestandspaden

Zorg ervoor dat alle bestanden in de module paden hebben met minder dan 140 tekens. Paden van meer dan 140 tekens lang veroorzaken problemen bij het importeren van runbooks. Automation kan geen bestand met een padgrootte van meer dan 140 tekens importeren in de PowerShell-sessie met `Import-Module` .

## <a name="import-modules"></a>Modules importeren

In deze sectie worden verschillende manieren beschrijft waarop u een module in uw Automation-account kunt importeren.

### <a name="import-modules-in-the-azure-portal"></a>Modules importeren in de Azure Portal

Een module importeren in de Azure Portal:

1. Ga naar uw Automation-account.
2. Selecteer **onder Gedeelde resources** de optie **Modules**.
3. Selecteer **Een module toevoegen.**
4. Selecteer het **ZIP-bestand** dat uw module bevat.
5. Selecteer **OK om** te beginnen met het importeren van het proces.

### <a name="import-modules-by-using-powershell"></a>Modules importeren met behulp van PowerShell

U kunt de cmdlet [New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) gebruiken om een module in uw Automation-account te importeren. De cmdlet gebruikt een URL voor een ZIP-pakket van de module.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

U kunt dezelfde cmdlet ook gebruiken om een module rechtstreeks vanuit de PowerShell Gallery importeren. Zorg ervoor dat u `ModuleName` en uit `ModuleVersion` de [PowerShell Gallery.](https://www.powershellgallery.com)

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>Modules importeren uit de PowerShell Gallery

U kunt deze [PowerShell Gallery](https://www.powershellgallery.com) rechtstreeks vanuit de galerie of vanuit uw Automation-account importeren.

Een module rechtstreeks vanuit de PowerShell Gallery:

1. Ga naar https://www.powershellgallery.com en zoek naar de module die u wilt importeren.
2. Selecteer **onder Installatieopties** op **het tabblad Azure Automation** implementeren naar **Azure Automation**. Met deze actie wordt de Azure Portal. 
3. Selecteer op de pagina Importeren uw Automation-account en selecteer **OK.**

![Schermopname van de PowerShell Gallery importmodule](../media/modules/powershell-gallery.png)

Als u een PowerShell Gallery module rechtstreeks vanuit uw Automation-account wilt importeren:

1. Selecteer **onder Gedeelde resources** de optie **Modules**. 
2. Selecteer **Bladeren in galerie** en zoek vervolgens in de galerie naar een module. 
3. Selecteer de module die u wilt importeren en selecteer **Importeren.** 
4. Selecteer **OK om** het importproces te starten.

![Schermopname van het importeren van PowerShell Gallery module uit de Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Modules verwijderen

Als u problemen hebt met een module of als u wilt teruggaan naar een eerdere versie van een module, kunt u deze verwijderen uit uw Automation-account. U kunt de oorspronkelijke versies van [](#default-modules) de standaardmodules die worden geïmporteerd, niet verwijderen wanneer u een Automation-account maakt. Als de module die u wilt verwijderen een nieuwere versie van een van de [standaardmodules](#default-modules)is, wordt deze teruggeschreven naar de versie die is geïnstalleerd met uw Automation-account. Anders wordt elke module die u uit uw Automation-account verwijdert, verwijderd.

### <a name="delete-modules-in-the-azure-portal"></a>Modules in de Azure Portal

Een module in de volgende Azure Portal:

1. Ga naar uw Automation-account. Selecteer **onder Gedeelde resources** de optie **Modules**.
2. Selecteer de module die u wilt verwijderen.
3. Selecteer verwijderen op de pagina **Module.** Als deze module een van de [standaardmodules](#default-modules)is, wordt deze teruggeschreven naar de versie die bestond toen het Automation-account werd gemaakt.

### <a name="delete-modules-by-using-powershell"></a>Modules verwijderen met behulp van PowerShell

Als u een module wilt verwijderen via PowerShell, moet u de volgende opdracht uitvoeren:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>Volgende stappen

* Zie Aan de slag met Azure PowerShell voor meer informatie over het [gebruik Azure PowerShell.](/powershell/azure/get-started-azureps)

* Zie Een module Windows PowerShell maken voor meer informatie [over het maken Windows PowerShell PowerShell-modules.](/powershell/scripting/developer/module/writing-a-windows-powershell-module)
