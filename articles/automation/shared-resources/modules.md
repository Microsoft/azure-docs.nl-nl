---
title: Modules beheren in Azure Automation
description: In dit artikel leest u hoe u Power shell-modules gebruikt om cmdlets in te scha kelen in runbooks en DSC-resources in DSC-configuraties.
services: automation
ms.subservice: shared-capabilities
ms.date: 02/01/2021
ms.topic: conceptual
ms.openlocfilehash: 57dfc388dcba926706a695c93d2afa1956432be8
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104953932"
---
# <a name="manage-modules-in-azure-automation"></a>Modules beheren in Azure Automation

Azure Automation gebruikt een aantal Power shell-modules om cmdlets in te scha kelen in runbooks en DSC-resources in DSC-configuraties. Ondersteunde modules zijn:

* [Azure PowerShell AZ. Automation](/powershell/azure/new-azureps-module-az).
* [Azure PowerShell AzureRM. Automation](/powershell/module/azurerm.automation/).
* Andere Power shell-modules.
* Interne `Orchestrator.AssetManagement.Cmdlets` module.
* Python 2-modules.
* Aangepaste modules die u maakt.

Wanneer u een Automation-account maakt, Azure Automation standaard een aantal modules geïmporteerd. Zie [standaard modules](#default-modules).

## <a name="sandboxes"></a>Sandboxes

Wanneer Automation runbook-en DSC-compilatie taken uitvoert, worden de modules geladen in sandboxes waar de runbooks kunnen worden uitgevoerd en kunnen de DSC-configuraties worden gecompileerd. Met Automation worden ook alle DSC-resources in modules op de DSC-pull-server geplaatst. Machines kunnen de bronnen ophalen wanneer ze de DSC-configuraties Toep assen.

>[!NOTE]
>Zorg ervoor dat u alleen de modules importeert die door uw runbooks en DSC-configuraties zijn vereist. Het is niet raadzaam om de hoofdmap AZ-module te importeren. Het bevat veel andere modules die u mogelijk niet nodig hebt. Dit kan prestatie problemen veroorzaken. Importeer in plaats daarvan afzonderlijke modules, zoals AZ. compute.

Cloud sandbox ondersteunt Maxi maal 48 systeem aanroepen en beperkt alle andere aanroepen om veiligheids redenen. Andere functies, zoals referentie beheer en bepaalde netwerken, worden niet ondersteund in de sandbox voor Clouds.

Vanwege het aantal modules en cmdlets is het moeilijk te weten welke van de cmdlets niet-ondersteunde aanroepen zullen maken. Over het algemeen hebben we problemen gezien met cmdlets waarvoor verhoogde toegang is vereist, is een referentie vereist als para meter of cmdlets die betrekking hebben op netwerken. Alle cmdlets die volledige stack-netwerk bewerkingen uitvoeren, worden niet ondersteund in de sandbox, waaronder [Connect-AipService](/powershell/module/aipservice/connect-aipservice) van de AipService Power shell-module en [omzetten-DnsName](/powershell/module/dnsclient/resolve-dnsname) van de DNSClient-module.

Dit zijn bekende beperkingen met betrekking tot de sandbox. De aanbevolen tijdelijke oplossing is om een [Hybrid Runbook worker](../automation-hybrid-runbook-worker.md) te implementeren of [Azure functions](../../azure-functions/functions-overview.md)te gebruiken.

## <a name="default-modules"></a>Standaard modules

De volgende tabel bevat de modules die standaard door Azure Automation worden geïmporteerd wanneer u uw Automation-account maakt. Met Automation kunnen nieuwere versies van deze modules worden geïmporteerd. U kunt de oorspronkelijke versie echter niet verwijderen uit uw Automation-account, zelfs als u een nieuwere versie verwijdert. Houd er rekening mee dat deze standaard modules verschillende AzureRM-modules bevatten.

De standaard modules worden ook wel globale modules genoemd. In de Azure Portal is de eigenschap **Global module** **True** wanneer een module wordt weer gegeven die werd geïmporteerd toen het account werd gemaakt.

![Scherm opname van eigenschap Global module in azure Portal](../media/modules/automation-global-modules.png)

Automation importeert de hoofdmap AZ module niet automatisch in nieuwe of bestaande Automation-accounts. Zie [Migrating to AZ modules](#migrate-to-az-modules)(Engelstalig) voor meer informatie over het werken met deze modules.

> [!NOTE]
> Het is niet raadzaam om modules en runbooks te wijzigen in Automation-accounts die worden gebruikt voor de implementatie van de functie [VM's buiten bedrijfsuren starten/stoppen](../automation-solution-vm-management.md) .

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
| Micro soft. Power shell. core | 0 |
| Micro soft. Power shell. Diagnostics |  |
| Micro soft. Power shell. Management |  |
| Micro soft. Power shell. Security |  |
| Microsoft.PowerShell.Utility |  |
| Micro soft. WSMan. Management |  |
| Orchestrator. AssetManagement. cmdlets | 1 |
| PSDscResources | 2.9.0.0 |
| SecurityPolicyDsc | 2.1.0.0 |
| StateConfigCompositeResources | 1 |
| xDSCDomainjoin | 1.1 |
| xPowerShellExecutionPolicy | 1.1.0.0 |
| xRemoteDesktopAdmin | 1.1.0.0 |

## <a name="az-modules"></a>AZ-modules

Voor AZ. Automation hebben de meeste cmdlets dezelfde namen als die voor de AzureRM-modules, behalve dat het `AzureRM` voor voegsel is gewijzigd in `Az` . Zie de [lijst met uitzonde ringen](/powershell/azure/migrate-from-azurerm-to-az#update-cmdlets-modules-and-parameters)voor een lijst met AZ-modules die niet voldoen aan deze naamgevings Conventie.

## <a name="internal-cmdlets"></a>Interne cmdlets

Azure Automation ondersteunt de interne `Orchestrator.AssetManagement.Cmdlets` module voor de log Analytics-agent voor Windows die standaard wordt geïnstalleerd. In de volgende tabel worden de interne cmdlets gedefinieerd. Deze cmdlets zijn ontworpen om te worden gebruikt in plaats van Azure PowerShell-cmdlets om te communiceren met gedeelde bronnen. Ze kunnen geheimen ophalen van versleutelde variabelen, referenties en versleutelde verbindingen.

>[!NOTE]
>De interne cmdlets zijn alleen beschikbaar wanneer u runbooks uitvoert in de Azure sandbox-omgeving of op een Windows-Hybrid Runbook Worker. 

|Naam|Beschrijving|
|---|---|
|Get-AutomationCertificate|`Get-AutomationCertificate [-Name] <string> [<CommonParameters>]`|
|Get-AutomationConnection|`Get-AutomationConnection [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]` |
|Get-AutomationPSCredential|`Get-AutomationPSCredential [-Name] <string> [<CommonParameters>]` |
|Get-AutomationVariable|`Get-AutomationVariable [-Name] <string> [-DoNotDecrypt] [<CommonParameters>]`|
|Set-AutomationVariable|`Set-AutomationVariable [-Name] <string> -Value <Object> [<CommonParameters>]` |
|Start-AutomationRunbook|`Start-AutomationRunbook [-Name] <string> [-Parameters <IDictionary>] [-RunOn <string>] [-JobId <guid>] [<CommonParameters>]`|
|Wait-AutomationJob|`Wait-AutomationJob -Id <guid[]> [-TimeoutInMinutes <int>] [-DelayInSeconds <int>] [-OutputJobsTransitionedToRunning] [<CommonParameters>]`|

Houd er rekening mee dat de interne cmdlets verschillen van de namen van de cmdlets AZ en AzureRM. Interne cmdlet-namen bevatten geen woorden zoals `Azure` of `Az` in het zelfstandig naam woord `Automation` . We raden u aan het gebruik van AZ of AzureRM-cmdlets te gebruiken tijdens het uitvoeren van een runbook in een Azure sandbox of op een Windows-Hybrid Runbook Worker. Ze vereisen minder para meters en worden uitgevoerd in de context van uw taak die al wordt uitgevoerd.

Gebruik AZ of AzureRM-cmdlets voor het bewerken van Automation-resources buiten de context van een runbook. 

## <a name="python-modules"></a>Python-modules

U kunt python 2-runbooks maken in Azure Automation. Zie [python 2-pakketten beheren in azure Automation](../python-packages.md)voor informatie over python-modules.

## <a name="custom-modules"></a>Aangepaste modules

Azure Automation ondersteunt aangepaste Power shell-modules die u maakt voor gebruik met runbooks en DSC-configuraties. Eén type aangepaste module is een integratie module die optioneel een bestand met meta gegevens bevat voor het definiëren van de aangepaste functionaliteit voor de module-cmdlets. Een voor beeld van het gebruik van een integratie module vindt u in [een verbindings type toevoegen](../automation-connections.md#add-a-connection-type).

Azure Automation kunt een aangepaste module importeren om de bijbehorende cmdlets beschikbaar te maken. Achter de schermen wordt de module opgeslagen en gebruikt deze in de Azure-sandboxes, net als bij andere modules.

## <a name="migrate-to-az-modules"></a>Migreren naar AZ-modules

In deze sectie wordt beschreven hoe u migreert naar de AZ-modules in Automation. Zie [Azure PowerShell migreren van AzureRM naar AZ](/powershell/azure/migrate-from-azurerm-to-az)voor meer informatie.

Het is niet raadzaam om AzureRM-modules en AZ-modules uit te voeren in hetzelfde Automation-account. Wanneer u zeker weet dat u van AzureRM naar AZ wilt migreren, kunt u het beste volledig door voeren in een volledige migratie. Automation gebruikt vaak sandboxes binnen het Automation-account om op te slaan op de opstart tijden. Als u geen volledige module migratie maakt, kunt u een taak starten die alleen gebruikmaakt van AzureRM modules en vervolgens een andere taak starten die alleen AZ-modules gebruikt. De sandbox loopt binnenkort vast en er wordt een fout bericht weer gegeven met de mede deling dat de modules niet compatibel zijn. Deze situatie resulteert in wille keurige crashes voor een bepaald runbook of bepaalde configuratie.

>[!NOTE]
>Wanneer u een nieuw Automation-account maakt, zelfs na migratie naar AZ-modules, worden de AzureRM-modules standaard door Automation geïnstalleerd. U kunt de zelf studie-runbooks nog steeds bijwerken met de AzureRM-cmdlets. U moet deze runbooks echter niet uitvoeren.

### <a name="test-your-runbooks-and-dsc-configurations-prior-to-module-migration"></a>Uw runbooks en DSC-configuraties testen voorafgaand aan de module migratie

Zorg ervoor dat u alle runbooks en DSC-configuraties zorgvuldig test, in een afzonderlijk Automation-account voordat u migreert naar de AZ-modules. 

### <a name="stop-and-unschedule-all-runbooks-that-use-azurerm-modules"></a>Alle runbooks die gebruikmaken van AzureRM-modules stoppen en de planning ervan opheffen

Om ervoor te zorgen dat u geen bestaande runbooks of DSC-configuraties uitvoert die gebruikmaken van AzureRM-modules, moet u alle betrokken runbooks en configuraties stoppen en de planning ervan opheffen. Zorg er eerst voor dat u elke runbook-of DSC-configuratie en de bijbehorende schema's afzonderlijk bekijkt, om ervoor te zorgen dat u het item indien nodig opnieuw kunt plannen in de toekomst.

Wanneer u klaar bent om uw planningen te verwijderen, kunt u de Azure Portal of de cmdlet [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) gebruiken. Zie [een schema verwijderen](schedules.md#remove-a-schedule).

### <a name="remove-azurerm-modules"></a>AzureRM-modules verwijderen

Het is mogelijk om de AzureRM-modules te verwijderen voordat u de AZ-modules importeert. Als u dit wel doet, kunt u de synchronisatie van broncode beheer onderbreken en ervoor zorgen dat scripts die nog niet zijn gepland, mislukken. Als u besluit de modules te verwijderen, raadpleegt u [AzureRM verwijderen](/powershell/azure/migrate-from-azurerm-to-az#uninstall-azurerm).

### <a name="import-az-modules"></a>Importeren AZ-modules

Wanneer u een AZ-module importeert in uw Automation-account, wordt de module niet automatisch geïmporteerd in de Power shell-sessie die door runbooks wordt gebruikt. Modules worden in de volgende situaties geïmporteerd in de Power shell-sessie:

* Wanneer een runbook een cmdlet aanroept vanuit een module.
* Wanneer een runbook de module expliciet importeert met de cmdlet [import-module](/powershell/module/microsoft.powershell.core/import-module) .
* Wanneer een runbook de module expliciet importeert met de instructie [using module](/powershell/module/microsoft.powershell.core/about/about_using#module-syntax) . De instructie using wordt ondersteund vanaf Windows Power shell 5,0 en ondersteunt klassen en het importeren van het type Enum.
* Wanneer een runbook een andere afhankelijke module importeert.

U kunt de AZ-modules importeren in het Automation-account vanuit het Azure Portal. Vergeet niet om alleen de AZ-modules te importeren die u nodig hebt, niet elke AZ-module die beschikbaar is. Omdat [AZ. accounts](https://www.powershellgallery.com/packages/Az.Accounts/1.1.0) een afhankelijkheid voor de andere AZ-modules zijn, moet u deze module vóór andere importeren.

1. Ga naar het Automation-account en selecteer **modules** onder **gedeelde resources**.
2. Selecteer **Bladeren in Galerie**.  
3. Voer in de zoek balk de module naam in (bijvoorbeeld `Az.Accounts` ).
4. Selecteer op de pagina Power shell-module **importeren** om de module te importeren in uw Automation-account.

    ![Scherm opname van het importeren van modules in uw Automation-account](../media/modules/import-module.png)

U kunt deze import bewerking ook uitvoeren via de [PowerShell Gallery](https://www.powershellgallery.com)door te zoeken naar de module die u wilt importeren. Wanneer u de module hebt gevonden, selecteert u deze en kiest u het tabblad **Azure Automation** . Selecteer **implementeren in azure Automation**.

![Scherm afbeelding van het rechtstreeks importeren van modules vanuit PowerShell Gallery](../media/modules/import-gallery.png)

### <a name="test-your-runbooks"></a>Uw runbooks testen

Nadat u de AZ-modules in het Automation-account hebt geïmporteerd, kunt u beginnen met het bewerken van uw runbooks en DSC-configuraties om de nieuwe modules te gebruiken. Een van de manieren om de wijziging van een runbook te testen voor het gebruik van de nieuwe cmdlets, is met behulp `Enable-AzureRmAlias -Scope Process` van de opdracht aan het begin van het runbook. Door deze opdracht aan uw runbook toe te voegen, kan het script zonder wijzigingen worden uitgevoerd.

## <a name="author-modules"></a>Modules schrijven

U wordt aangeraden de overwegingen in deze sectie te volgen wanneer u een aangepaste Power shell-module ontwerpt voor gebruik in Azure Automation. Als u de module wilt voorbereiden voor importeren, moet u ten minste een. psd1-,. psm1-of Power shell-module **. dll** -bestand maken met dezelfde naam als de module map. Vervolgens moet u de map module zo instellen dat Azure Automation deze als één bestand kunt importeren. Het **zip** -pakket moet dezelfde naam hebben als de map in de module.

Zie [een Power shell-script module schrijven](/powershell/scripting/developer/module/how-to-write-a-powershell-script-module)voor meer informatie over het ontwerpen van een Power shell-module.

### <a name="version-folder"></a>Versie map

Met de side-by-side module versie beheer van Power shell kunt u meer dan één versie van een module in Power shell gebruiken. Dit kan handig zijn als u oudere scripts hebt die zijn getest en alleen werken met een bepaalde versie van een Power shell-module, maar voor andere scripts een nieuwere versie van dezelfde Power shell-module nodig is.

Als u Power shell-modules wilt maken zodat deze meerdere versies bevatten, maakt u de map module en maakt u vervolgens een map in deze module map voor elke versie van de module die u wilt kunnen Toep assen. In het volgende voor beeld biedt een module met de naam *TestModule* twee versies: 1.0.0 en 2.0.0.

```dos
TestModule
   1.0.0
   2.0.0
```

Kopieer in elk van de versie mappen uw Power shell. psm1-,. psd1-of Power shell-module **. dll** -bestanden waaruit een module in de betreffende versie map is. De module map zo instellen dat Azure Automation deze als één ZIP-bestand kunt importeren. Tijdens Automation wordt alleen de hoogste versie van de module geïmporteerd, als het module pakket naast elkaar aanwezige versies van de module bevat, allemaal beschikbaar voor gebruik in uw runbooks of DSC-configuraties.  

Hoewel Automation modules ondersteunt die naast elkaar worden gebruikt binnen hetzelfde pakket, biedt het geen ondersteuning voor het gebruik van meerdere versies van een module over het importeren van module pakketten. U importeert bijvoorbeeld **module A**, die versies 1 en 2 in uw Automation-account bevat. Later kunt u **module A** bijwerken met versie 3 en 4, wanneer u importeert in uw Automation-account, zijn alleen versie 3 en 4 bruikbaar in RUNBOOKS of DSC-configuraties. Als u wilt dat alle versies 1, 2, 3 en 4 beschikbaar zijn, moet het zip-bestand versie 1, 2, 3 en 4 bevatten.

Als u verschillende versies van dezelfde module tussen runbooks wilt gebruiken, moet u de versie die u in uw runbook wilt gebruiken, altijd declareren met de `Import-Module` cmdlet en de para meter toevoegen `-RequiredVersion <version>` . Zelfs als de versie die u wilt gebruiken de meest recente versie is. Dit komt doordat runbook-taken in dezelfde sandbox kunnen worden uitgevoerd. Als de sandbox al expliciet een module van een bepaald versie nummer heeft geladen, omdat een eerdere taak in die sandbox dit zou doen, wordt de meest recente versie van de module niet automatisch geladen in toekomstige taken in die sandbox. Dit komt omdat er al een versie van is geladen in de sandbox.

Voor een DSC-resource gebruikt u de volgende opdracht om een bepaalde versie op te geven:

```powershell
Import-DscResource -ModuleName <ModuleName> -ModuleVersion <version>
```

### <a name="help-information"></a>Help-informatie

Neem een samen vatting, beschrijving en Help-URI op voor elke cmdlet in uw module. In Power shell kunt u Help-informatie voor cmdlets definiëren met behulp van de- `Get-Help` cmdlet. In het volgende voor beeld ziet u hoe u een samen vatting en Help-URI definieert in een **. psm1** -module bestand.

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
        $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $UserName, $Password
        Connect-Contoso -Credential $cred
     }
     "ConnectionObject" {
        Connect-Contoso -Connection $Connection
    }
  }
  }
  ```

  Als u deze informatie opgeeft, wordt de Help-tekst weer gegeven via de `Get-Help` cmdlet in de Power shell-console. Deze tekst wordt ook weer gegeven in de Azure Portal.

  ![Scherm afbeelding van de Help voor integratie modules](../media/modules/module-activity-description.png)

### <a name="connection-type"></a>Type verbinding

Als de module verbinding maakt met een externe service, definieert u een verbindings type met behulp van een [aangepaste integratie module](#custom-modules). Elke cmdlet in de module moet een exemplaar van dat verbindings type (verbindings object) accepteren als een para meter. Gebruikers wijzen alle para meters van de verbindings Asset toe aan de bijbehorende para meters telkens wanneer ze een cmdlet aanroepen.

![Gebruik een aangepaste verbinding in het Azure Portal](../media/modules/connection-create-new.png)

In het volgende voor beeld van een runbook wordt een contoso-verbindings element gebruikt dat is aangeroepen `ContosoConnection` om toegang te krijgen tot contoso-resources en gegevens te retour neren van de externe In dit voor beeld worden de velden toegewezen aan de `UserName` `Password` Eigenschappen en van een `PSCredential` object en vervolgens door gegeven aan de cmdlet.

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $contosoConnection.UserName, $contosoConnection.Password
  Connect-Contoso -Credential $cred
  }
  ```

Een eenvoudiger en betere manier om dit gedrag te benaderen, is door het verbindings object rechtstreeks door te geven aan de cmdlet:

  ```powershell
  $contosoConnection = Get-AutomationConnection -Name 'ContosoConnection'

  Connect-Contoso -Connection $contosoConnection
  }
  ```

U kunt hetzelfde gedrag voor uw cmdlets inschakelen door ze in staat te stellen een verbindings object rechtstreeks als para meter te accepteren, in plaats van alleen verbindings velden voor para meters. Doorgaans wilt u voor elke para meter instellen, zodat een gebruiker die Automation niet gebruikt, uw cmdlets kan aanroepen zonder een hashtabel te maken die als verbindings object moet fungeren. De parameterset `UserAccount` wordt gebruikt om de eigenschappen van het verbindings veld door te geven. `ConnectionObject` Hiermee kunt u de verbinding direct door geven.

### <a name="output-type"></a>Uitvoertype

Definieer het uitvoer type voor alle cmdlets in uw module. Als u een uitvoer type voor een cmdlet definieert, kunt u met de ontwerp tijd IntelliSense de uitvoer eigenschappen van de cmdlet tijdens het ontwerpen bepalen. Deze praktijk is vooral nuttig tijdens het ontwerpen van een grafisch runbook, waarvoor ontwerp tijd kennis essentieel is voor een eenvoudige gebruikers ervaring met uw module.

Toevoegen `[OutputType([<MyOutputType>])]` , waarbij `MyOutputType` een geldig type is. `OutputType`Zie [about functions OutputTypeAttribute](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)(Engelstalig) voor meer informatie over. De volgende code is een voor beeld van het toevoegen `OutputType` aan een cmdlet:

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

  ![Scherm opname van grafisch runbook-uitvoer type](../media/modules/runbook-graphical-module-output-type.png)

  Dit gedrag is vergelijkbaar met de functionaliteit ' type vooruit ' van de uitvoer van een cmdlet in de Power Shell Integration service-omgeving, zonder dat u deze hoeft uit te voeren.

  ![Scherm opname van POSH IntelliSense](../media/modules/automation-posh-ise-intellisense.png)

### <a name="cmdlet-state"></a>Cmdlet-status

Alle cmdlets in de module stateless maken. Meerdere runbook-taken kunnen tegelijkertijd worden uitgevoerd in hetzelfde `AppDomain` proces en in dezelfde sandbox. Als er een status op die niveaus wordt gedeeld, kunnen de taken van invloed zijn op elkaar. Dit gedrag kan leiden tot onregelmatige en moeilijk te onderzoeken problemen. Hier volgt een voor beeld van wat u niet moet doen:

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

### <a name="module-dependency"></a>Module afhankelijkheid

Zorg ervoor dat de module volledig is opgenomen in een pakket dat met Xcopy kan worden gekopieerd. Automatiserings modules worden gedistribueerd naar de Automation-sandboxes wanneer runbooks worden uitgevoerd. De modules moeten onafhankelijk van de host werken waarop ze worden uitgevoerd.

U moet een module pakket kunnen opwaarderen en verplaatsen, en dit als normaal laten functioneren wanneer het wordt geïmporteerd in de Power shell-omgeving van een andere host. Als u dit wilt doen, moet u ervoor zorgen dat de module niet afhankelijk is van bestanden buiten de module-map die is ingepakt wanneer de module in Automation wordt geïmporteerd.

Uw module moet niet afhankelijk zijn van unieke register instellingen op een host. Voor beelden zijn de instellingen die worden gemaakt wanneer een product wordt geïnstalleerd.

### <a name="module-file-paths"></a>Bestands paden van module

Zorg ervoor dat alle bestanden in de module paden hebben die minder dan 140 tekens bevatten. Alle paden met een lengte van 140 tekens veroorzaken problemen bij het importeren van runbooks. Met Automation kunt u een bestand met een grootte van Maxi maal 140 tekens niet importeren in de Power shell-sessie met `Import-Module` .

## <a name="import-modules"></a>Modules importeren

In deze sectie worden verschillende manieren gedefinieerd waarop u een module kunt importeren in uw Automation-account.

### <a name="import-modules-in-the-azure-portal"></a>Modules importeren in de Azure Portal

Een module importeren in de Azure Portal:

1. Ga naar uw Automation-account.
2. Onder **gedeelde bronnen** selecteert u **modules**.
3. Selecteer **een module toevoegen**.
4. Selecteer het **zip** -bestand dat de module bevat.
5. Selecteer **OK** om te beginnen met het importeren van het proces.

### <a name="import-modules-by-using-powershell"></a>Modules importeren met behulp van Power shell

U kunt de cmdlet [New-AzAutomationModule](/powershell/module/az.automation/new-azautomationmodule) gebruiken om een module te importeren in uw Automation-account. De cmdlet maakt een URL voor een module. zip-pakket.

```azurepowershell-interactive
New-AzAutomationModule -Name <ModuleName> -ContentLinkUri <ModuleUri> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

U kunt ook dezelfde cmdlet gebruiken om rechtstreeks een module te importeren vanuit het PowerShell Gallery. Zorg ervoor dat u `ModuleName` `ModuleVersion` de [PowerShell Gallery](https://www.powershellgallery.com)ophaalt.

```azurepowershell-interactive
$moduleName = <ModuleName>
$moduleVersion = <ModuleVersion>
New-AzAutomationModule -AutomationAccountName <AutomationAccountName> -ResourceGroupName <ResourceGroupName> -Name $moduleName -ContentLinkUri "https://www.powershellgallery.com/api/v2/package/$moduleName/$moduleVersion"
```

### <a name="import-modules-from-the-powershell-gallery"></a>Modules importeren uit de PowerShell Gallery

U kunt [PowerShell Gallery](https://www.powershellgallery.com) modules rechtstreeks vanuit de galerie of vanuit uw Automation-account importeren.

Een module rechtstreeks vanuit het PowerShell Gallery importeren:

1. Ga naar https://www.powershellgallery.com en zoek naar de module die u wilt importeren.
2. Onder **installatie opties**, op het tabblad **Azure Automation** , selecteert **u implementeren naar Azure Automation**. Met deze actie wordt de Azure Portal geopend. 
3. Selecteer uw Automation-account op de pagina importeren en selecteer **OK**.

![Scherm afbeelding van de module voor het PowerShell Gallery importeren](../media/modules/powershell-gallery.png)

Een PowerShell Gallery module rechtstreeks vanuit uw Automation-account importeren:

1. Onder **gedeelde bronnen** selecteert u **modules**. 
2. Selecteer **Browse Gallery** en zoek in de galerie naar een module. 
3. Selecteer de module die u wilt importeren en selecteer **importeren**. 
4. Selecteer **OK** om het import proces te starten.

![Scherm opname van het importeren van een PowerShell Gallery module vanuit de Azure Portal](../media/modules/gallery-azure-portal.png)

## <a name="delete-modules"></a>Modules verwijderen

Als u problemen ondervindt met een module, of als u een eerdere versie van een module wilt herstellen, kunt u deze verwijderen uit uw Automation-account. U kunt de oorspronkelijke versies van de [standaard modules](#default-modules) die worden geïmporteerd wanneer u een Automation-account maakt, niet verwijderen. Als de module die u wilt verwijderen een nieuwere versie is van een van de [standaard modules](#default-modules), wordt de versie hersteld die is geïnstalleerd met uw Automation-account. Anders wordt de module verwijderd die u uit uw Automation-account verwijdert.

### <a name="delete-modules-in-the-azure-portal"></a>Modules in de Azure Portal verwijderen

Een module verwijderen in de Azure Portal:

1. Ga naar uw Automation-account. Onder **gedeelde bronnen** selecteert u **modules**.
2. Selecteer de module die u wilt verwijderen.
3. Selecteer op de pagina module de optie **verwijderen**. Als deze module een van de [standaard modules](#default-modules)is, wordt deze teruggedraaid naar de versie die bestond tijdens het maken van het Automation-account.

### <a name="delete-modules-by-using-powershell"></a>Modules verwijderen met behulp van Power shell

Als u een module wilt verwijderen via Power shell, voert u de volgende opdracht uit:

```azurepowershell-interactive
Remove-AzAutomationModule -Name <moduleName> -AutomationAccountName <automationAccountName> -ResourceGroupName <resourceGroupName>
```

## <a name="next-steps"></a>Volgende stappen

* Zie [aan de slag met Azure PowerShell](/powershell/azure/get-started-azureps)voor meer informatie over het gebruik van Azure PowerShell modules.

* Zie [een Windows Power shell-module schrijven](/powershell/scripting/developer/module/writing-a-windows-powershell-module)voor meer informatie over het maken van Power shell-modules.