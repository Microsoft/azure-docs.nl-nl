---
title: Continue implementatie Azure Automation Chocolatey instellen
description: In dit artikel wordt beschreven hoe u continue implementatie in kunt stellen met State Configuration chocolatey package manager.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: 717561614a3e42995bbce6746839fd9b7cbca37e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834854"
---
# <a name="set-up-continuous-deployment-with-chocolatey"></a>Doorlopende implementatie instellen met Chocolatey

In een DevOps-wereld zijn er veel hulpprogramma's om te helpen met verschillende punten in de pijplijn voor continue integratie. Azure Automation [State Configuration](automation-dsc-overview.md) is een welkome nieuwe toevoeging aan de opties die DevOps-teams kunnen gebruiken. 

Azure Automation is een beheerde service in Microsoft Azure waarmee u verschillende taken kunt automatiseren met runbooks, knooppunten en gedeelde resources, zoals referenties, planningen en globale variabelen. Azure Automation State Configuration breidt deze automatiseringsmogelijkheid uit met PowerShell Desired State Configuration (DSC)-hulpprogramma's. Hier is een goed [overzicht.](automation-dsc-overview.md)

In dit artikel wordt beschreven hoe u continue implementatie (CD) in kunt stellen voor een Windows-computer. U kunt de techniek eenvoudig uitbreiden om zo veel Windows-computers op te nemen als nodig is in de rol, bijvoorbeeld een website, en van daar naar aanvullende rollen te gaan.

![Doorlopende implementatie voor IaaS-VM's](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Op hoog niveau

Er is hier nogal wat aan de hand, maar gelukkig kan het worden onderverdeeld in twee hoofdprocessen:

- Code schrijven en testen en vervolgens installatiepakketten maken en publiceren voor de belangrijkste en secundaire versies van het systeem.
- VM's maken en beheren die de code in de pakketten installeren en uitvoeren.  

Zodra beide kernprocessen zijn geïmplementeerd, is het eenvoudig om het pakket automatisch bij te werken op uw VM's wanneer er nieuwe versies worden gemaakt en geïmplementeerd.

## <a name="component-overview"></a>Overzicht van onderdelen

Pakketmanagers zoals [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) zijn bekend in de Linux-wereld, maar niet zo veel in de Windows-wereld.
[Chocolatey](https://chocolatey.org/) is zo'n ding en het [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) van Scott Hanselman over het onderwerp is een goede inleiding. Kort gezegd kunt u met Chocolatey de opdrachtregel gebruiken om pakketten vanuit een centrale opslagplaats te installeren op een Windows-besturingssysteem. U kunt uw eigen opslagplaats maken en beheren en Chocolatey kan pakketten installeren vanuit een door u toegewezen aantal opslagplaatsen.

[PowerShell DSC](/powershell/scripting/dsc/overview/overview) is een PowerShell-hulpprogramma waarmee u de configuratie kunt declareeren die u voor een machine wilt gebruiken. Als u bijvoorbeeld Chocolatey wilt installeren, IIS wilt installeren, poort 80 hebt geopend en versie 1.0.0 van uw website wilt installeren, implementeert de DSC Local Configuration Manager (LCM) die configuratie. Een DSC-pull-server bevat een opslagplaats met configuraties voor uw computers. De LCM op elke computer controleert periodiek of de configuratie overeenkomt met de opgeslagen configuratie. U kunt de status rapporteren of proberen de machine weer in overeenstemming te brengen met de opgeslagen configuratie. U kunt de opgeslagen configuratie op de pull-server bewerken om ervoor te zorgen dat een computer of set computers in overeenstemming komt met de gewijzigde configuratie.

Een DSC-resource is een codemodule met specifieke mogelijkheden, zoals het beheren van netwerken, Active Directory of SQL Server. De Chocolatey DSC-resource weet onder andere hoe u toegang krijgt tot een NuGet-server, pakketten downloadt, pakketten installeert, en meer. Er zijn veel andere DSC-resources in [de PowerShell Gallery.](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title) U installeert deze modules op uw Azure Automation State Configuration pull-server voor gebruik door uw configuraties.

Resource Manager-sjablonen bieden een declaratieve manier om uw infrastructuur te genereren, bijvoorbeeld netwerken, subnetten, netwerkbeveiliging en -routering, load balancers, NIC's, VM's, en meer. Hier is een [artikel](../azure-resource-manager/management/deployment-models.md) waarin het Resource Manager implementatiemodel (declaratief) wordt vergeleken met het implementatiemodel van Azure Service Management (ASM of klassiek) (imperatief). Dit artikel bevat een bespreking van de belangrijkste resourceproviders: compute, opslag en netwerk.

Een belangrijke functie van een Resource Manager sjabloon is de mogelijkheid om een VM-extensie te installeren in de VM wanneer deze wordt ingericht. Een VM-extensie heeft specifieke mogelijkheden, zoals het uitvoeren van een aangepast script, het installeren van antivirussoftware en het uitvoeren van een DSC-configuratiescript. Er zijn veel andere typen VM-extensies.

## <a name="quick-trip-around-the-diagram"></a>Snelle reis door het diagram

Vanaf de bovenkant schrijft u uw code, bouwt u deze, test u deze en maakt u vervolgens een installatiepakket. Chocolatey kan verschillende typen installatiepakketten verwerken, zoals MSI, MSU, ZIP. En u hebt de volledige kracht van PowerShell om de daadwerkelijke installatie uit te doen als de systeemeigen mogelijkheden van Chocolatey daar niet aan toe zijn. Plaats het pakket op een plek die bereikbaar is: een pakketopslagplaats. In dit gebruiksvoorbeeld wordt een openbare map in een Azure Blob Storage-account gebruikt, maar dit kan overal zijn. Chocolatey werkt systeemeigen met NuGet-servers en enkele andere voor het beheer van pakketmetagegevens. [In dit artikel](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) worden de opties beschreven. In het gebruiksvoorbeeld wordt NuGet gebruikt. Een Nuspec zijn metagegevens over uw pakketten. De Nuspec-gegevens worden gecompileerd in een NuPkg en opgeslagen op een NuGet-server. Wanneer uw configuratie een pakket op naam aanvraagt en verwijst naar een NuGet-server, pakt de Chocolatey DSC-resource op de VM het pakket en installeert het. U kunt ook een specifieke versie van een pakket aanvragen.

Linksonder in de afbeelding ziet u een Azure Resource Manager sjabloon. In dit gebruiksvoorbeeld registreert de VM-extensie de VM met de Azure Automation State Configuration pull-server als een knooppunt. De configuratie wordt tweemaal opgeslagen in de pull-server: eenmaal als tekst zonder tekst en eenmaal gecompileerd als een MOF-bestand. In de Azure Portal vertegenwoordigt de MOF een knooppuntconfiguratie, in plaats van een eenvoudige configuratie. Het is het artefact dat is gekoppeld aan een knooppunt, zodat het knooppunt de configuratie weet. Hieronder vindt u informatie over het toewijzen van de knooppuntconfiguratie aan het knooppunt.

Het maken van de Nuspec, het compileren en opslaan ervan op een NuGet-server is een klein ding. En u beheert al VM's. 

Als u de volgende stap wilt maken voor continue implementatie, moet u de pull-server één keer instellen, uw knooppunten één keer registreren en de eerste configuratie op de server maken en opslaan. Wanneer pakketten worden bijgewerkt en geïmplementeerd in de opslagplaats, hoeft u alleen de configuratie en knooppuntconfiguratie op de pull-server te vernieuwen als dat nodig is.

Als u niet begint met een Resource Manager sjabloon, is dat prima. Er zijn PowerShell-opdrachten om u te helpen uw VM's te registreren bij de pull-server. Zie [Onboarding machines for management by Azure Automation State Configuration (Machines onboarden voor beheer door Azure Automation State Configuration).](automation-dsc-onboarding.md)

## <a name="about-the-usage-example"></a>Over het gebruiksvoorbeeld

Het gebruiksvoorbeeld in dit artikel begint met een VM van een algemene Windows Server 2012 R2-afbeelding uit de Azure-galerie. U kunt beginnen met elke opgeslagen installatie afbeelding en vervolgens aanpassen met de DSC-configuratie.
Het wijzigen van de configuratie die standaard is gemaakt in een installatie afbeelding is echter veel moeilijker dan het dynamisch bijwerken van de configuratie met behulp van DSC.

U hoeft geen sjabloon voor Resource Manager en de VM-extensie te gebruiken om deze techniek te gebruiken voor uw VM's. En uw VM's hoeven niet in Azure te zijn om cd-beheer te kunnen uitvoeren. U hoeft alleen Chocolatey te installeren en de LCM te configureren op de VM, zodat deze weet waar de pull-server zich is.

Wanneer u een pakket bij werkt op een VM die in productie is, moet u die VM uit de roulatie halen terwijl de update wordt geïnstalleerd. Hoe u dit doet, varieert aanzienlijk. Als u bijvoorbeeld een VM achter een Azure Load Balancer, kunt u een aangepaste test toevoegen. Bij het bijwerken van de VM moet u het eindpunt van de test een 400 laten retourneren. De aanpassing die nodig is om deze wijziging te veroorzaken, kan zich in uw configuratie, evenals de aanpassing om deze terug te zetten naar het retourneren van een 200 zodra de update is voltooid.

De volledige bron voor dit gebruiksvoorbeeld staat in [Visual Studio project](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) op GitHub.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Stap 1: de pull-server en het Automation-account instellen

Op een geverifieerde ( `Connect-AzAccount` ) PowerShell-opdrachtregel: (kan enkele minuten duren terwijl de pull-server is ingesteld)

```azurepowershell-interactive
New-AzResourceGroup -Name MY-AUTOMATION-RG -Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount -ResourceGroupName MY-AUTOMATION-RG -Location MY-RG-LOCATION-IN-QUOTES -Name MY-AUTOMATION-ACCOUNT
```

U kunt uw Automation-account in een van de volgende regio's plaatsen (ook wel locaties genoemd): VS - oost 2, VS - zuid-centraal, US Gov - Virginia, Europa - west, Azië - zuidoost, Japan - oost, India - centraal en Australië - zuidoost, Canada - centraal, Europa - noord.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Stap 2: de VM-extensie aanpassen aan de Resource Manager sjabloon

Details voor VM-registratie (met behulp van de PowerShell DSC VM-extensie) die zijn opgegeven in deze [Azure-quickstart-sjabloon.](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)
Met deze stap registreert u de nieuwe VM bij de pull-server in de lijst met State Configuration knooppunten. Onderdeel van deze registratie is het opgeven van de knooppuntconfiguratie die moet worden toegepast op het knooppunt. Deze knooppuntconfiguratie hoeft nog niet te bestaan in de pull-server. Het is dus prima dat stap 4 de eerste keer wordt uitgevoerd. Maar hier in stap 2 moet u de naam van het knooppunt en de naam van de configuratie hebben bepaald. In dit gebruiksvoorbeeld is het knooppunt 'isvbox' en is de configuratie ISVBoxConfig. De naam van de knooppuntconfiguratie (die moet worden opgegeven in DeploymentTemplate.jsis dus 'ISVBoxConfig.isvbox'.

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Stap 3: vereiste DSC-resources toevoegen aan de pull-server

De PowerShell Gallery is instrumenteerd om DSC-resources te installeren in uw Azure Automation account.
Navigeer naar de resource die u wilt en klik op de Azure Automation implementeren.

![PowerShell Gallery voorbeeld](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Onlangs is een andere techniek toegevoegd aan Azure Portal kunt u nieuwe modules binnen halen of bestaande modules bijwerken. Klik op de resource van het Automation-account, de tegel Assets en ten slotte op de tegel Modules. Met het pictogram Bladeren in galerie kunt u de lijst met modules in de galerie bekijken, inzoomen op details en uiteindelijk importeren in uw Automation-account. Dit is een uitstekende manier om uw modules van tijd tot tijd up-to-date te houden. En de importfunctie controleert afhankelijkheden met andere modules om ervoor te zorgen dat er niets wordt gesynchroniseerd.

Er is ook een handmatige benadering, die slechts één keer per resource wordt gebruikt, tenzij u deze later wilt upgraden. Zie Authoring Integration Modules for Azure Automation voor meer informatie over het schrijven van [PowerShell-integratiemodules.](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)

>[!NOTE]
>De mapstructuur van een PowerShell-integratiemodule voor een Windows-computer verschilt iets van de mapstructuur die wordt verwacht door de Azure Automation. 

1. Installeer [Windows Management Framework v5](https://aka.ms/wmf5latest) (niet nodig voor Windows 10).

2. Installeer de integratiemodule.

    ```azurepowershell-interactive
    Install-Module -Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Kopieer de modulemap **van c:\Program Files\WindowsPowerShell\Modules\MODULE-NAME** naar een tijdelijke map.

4. Verwijder voorbeelden en documentatie uit de hoofdmap.

5. Zip de hoofdmap en noem het ZIP-bestand de naam van de map.

6. Plaats het ZIP-bestand op een bereikbare HTTP-locatie, zoals blobopslag, in een Azure Storage-account.

7. Voer de volgende opdracht uit.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME -ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

In het opgenomen voorbeeld worden deze stappen geïmplementeerd voor cChoco en xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Stap 4: de knooppuntconfiguratie toevoegen aan de pull-server

Er is niets speciaals aan de eerste keer dat u uw configuratie in de pull-server importeert en compileert. Alle latere import- of compilaties van dezelfde configuratie zien er precies hetzelfde uit. Telkens wanneer u uw pakket bij werkt en het moet pushen naar productie, moet u deze stap doen nadat u ervoor hebt gezorgd dat het configuratiebestand juist is, inclusief de nieuwe versie van uw pakket. Hier is het configuratiebestand **ISVBoxConfig.ps1**:

```powershell
Configuration ISVBoxConfig
{
    Import-DscResource -ModuleName cChoco
    Import-DscResource -ModuleName xNetworking

    Node 'isvbox' {

        cChocoInstaller installChoco
        {
            InstallDir = 'C:\choco'
        }

        WindowsFeature installIIS
        {
            Ensure = 'Present'
            Name   = 'Web-Server'
        }

        xFirewall WebFirewallRule
        {
            Direction    = 'Inbound'
            Name         = 'Web-Server-TCP-In'
            DisplayName  = 'Web Server (TCP-In)'
            Description  = 'IIS allow incoming web site traffic.'
            Enabled       = 'True'
            Action       = 'Allow'
            Protocol     = 'TCP'
            LocalPort    = '80'
            Ensure       = 'Present'
        }

        cChocoPackageInstaller trivialWeb
        {
            Name      = 'trivialweb'
            Version   = '1.0.0'
            Source    = 'MY-NUGET-V2-SERVER-ADDRESS'
            DependsOn = '[cChocoInstaller]installChoco','[WindowsFeature]installIIS'
        }
    }
}
```

Dit is het **New-ConfigurationScript.ps1script** (gewijzigd voor het gebruik van de Az-module):

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published -Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Deze stappen resulteren in een nieuwe knooppuntconfiguratie met de **naam ISVBoxConfig.isvbox** op de pull-server. De naam van de knooppuntconfiguratie is gebouwd als `configurationName.nodeName` .

## <a name="step-5-create-and-maintain-package-metadata"></a>Stap 5: metagegevens van pakketten maken en onderhouden

Voor elk pakket dat u in de pakketopslagplaats zet, hebt u een Nuspec nodig die het beschrijft. Deze moet worden gecompileerd en opgeslagen op uw NuGet-server. Dit proces wordt hier [beschreven.](https://docs.nuget.org/create/creating-and-publishing-a-package) 

U kunt deze **MyGet.org** als een NuGet-server. U kunt deze service kopen, maar u bent een gratis starter-SKU. Op [NuGet](https://www.nuget.org/)vindt u instructies voor het installeren van uw eigen NuGet-server voor uw persoonlijke pakketten.

## <a name="step-6-tie-it-all-together"></a>Stap 6: alles aan elkaar binden

Telkens wanneer een versie qa slaagt en wordt goedgekeurd voor implementatie, wordt het pakket gemaakt en worden nuspec en nupkg bijgewerkt en geïmplementeerd op de NuGet-server. De configuratie (stap 4) moet ook worden bijgewerkt om akkoord te gaan met het nieuwe versienummer. Deze moet vervolgens naar de pull-server worden verzonden en gecompileerd.

Vanaf dat moment is het aan de VM's die afhankelijk zijn van die configuratie om de update op te halen en te installeren. Elk van deze updates is eenvoudig: slechts een of twee regels van PowerShell. Voor Azure DevOps worden sommige ervan ingekapseld in buildtaken die in een build aan elkaar kunnen worden vastgeketend. In [dit artikel](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) vindt u meer informatie. In [deze GitHub-opslagplaats worden](https://github.com/Microsoft/vso-agent-tasks) de beschikbare buildtaken belicht.

## <a name="related-articles"></a>Verwante artikelen:
* [Azure Automation DSC-overzicht](automation-dsc-overview.md)
* [Machines onboarden voor beheer door Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Volgende stappen

- Zie overzicht Azure Automation State Configuration [voor een overzicht.](automation-dsc-overview.md)
- Zie Aan de slag met de functie om [aan de slag te Azure Automation State Configuration.](automation-dsc-getting-started.md)
- Zie Compile DSC configurations in Azure Automation State Configuration (DSC-configuraties compileren in Azure Automation State Configuration) voor meer informatie over het compileren van [DSC-configuraties, zodat u ze kunt toewijzen aan doelknooppunten.](automation-dsc-compile.md)
- Zie [Az.Automation](/powershell/module/az.automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
- Zie prijzen voor Azure Automation State Configuration informatie over [prijzen.](https://azure.microsoft.com/pricing/details/automation/)
