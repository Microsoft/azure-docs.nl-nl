---
title: Azure Automation continue implementatie instellen met chocolade
description: In dit artikel leest u hoe u doorlopende implementatie kunt instellen met status configuratie en chocolade Package Manager.
services: automation
ms.subservice: dsc
ms.date: 08/08/2018
ms.topic: conceptual
ms.custom: references_regions
ms.openlocfilehash: bb5f7b5e8214bd3b04bd7b9544ab4bc589f6c4bf
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896322"
---
# <a name="set-up-continuous-deployment-with-chocolatey"></a>Doorlopende implementatie instellen met Chocolatey

In een DevOps-wereld zijn er veel hulpprogram ma's om te helpen bij verschillende punten in de pipeline voor continue integratie. Azure Automation [status configuratie](automation-dsc-overview.md) is een nieuwe aanvulling op de opties die DevOps teams kunnen gebruiken. 

Azure Automation is een beheerde service in Microsoft Azure waarmee u verschillende taken kunt automatiseren met runbooks, knoop punten en gedeelde bronnen, zoals referenties, schema's en globale variabelen. Met de configuratie van de Azure Automation-status wordt deze automatiserings mogelijkheid uitgebreid met hulpprogram ma's voor desired state Configuration (DSC) van Power shell. Hier volgt een geweldig [overzicht](automation-dsc-overview.md).

In dit artikel wordt beschreven hoe u doorlopende implementatie (CD) instelt voor een Windows-computer. U kunt de techniek eenvoudig uitbreiden met zo veel Windows-computers als nodig is voor de rol, bijvoorbeeld een website, en daar gaat u naar extra rollen.

![Continue implementatie voor IaaS-Vm's](./media/automation-dsc-cd-chocolatey/cdforiaasvm.png)

## <a name="at-a-high-level"></a>Op een hoog niveau

Er is hier heel iets aan de slag, maar gelukkig kan het worden onderverdeeld in twee hoofd processen:

- Code schrijven en testen, vervolgens installatie pakketten maken en publiceren voor primaire en secundaire versies van het systeem.
- Vm's maken en beheren waarmee de code in de pakketten wordt geïnstalleerd en uitgevoerd.  

Wanneer beide kern processen zijn geïmplementeerd, is het eenvoudig om het pakket op uw Vm's automatisch bij te werken, omdat nieuwe versies worden gemaakt en geïmplementeerd.

## <a name="component-overview"></a>Overzicht van onderdelen

Pakket beheerders zoals [apt-get](https://en.wikipedia.org/wiki/Advanced_Packaging_Tool) zijn goed bekend in de Linux-wereld, maar niet zo veel in de Windows-wereld.
Het [is een goed idee](https://chocolatey.org/) om de [blog](https://www.hanselman.com/blog/IsTheWindowsUserReadyForAptget.aspx) van Scott Hanselman in het onderwerp een fantastische inleiding te hebben. In een kort gezegd kunt u met chocolade de opdracht regel gebruiken om pakketten van een centrale opslag plaats te installeren op een Windows-besturings systeem. U kunt uw eigen opslag plaats maken en beheren, en Choco lade kan pakketten installeren vanaf elk gewenst aantal opslag plaatsen die u opgeeft.

[Power shell DSC](/powershell/scripting/dsc/overview/overview) is een Power shell-hulp programma waarmee u de gewenste configuratie voor een machine kunt declareren. Als u bijvoorbeeld wilt dat Choco lade geïnstalleerd, IIS geïnstalleerd, poort 80 geopend en versie 1.0.0 van uw website is geïnstalleerd, implementeert de DSC Local Configuration Manager (LCM) die configuratie. Een DSC-pull-server bevat een opslag plaats met configuraties voor uw machines. De LCM wordt op elke computer periodiek gecontroleerd om te zien of de configuratie overeenkomt met de opgeslagen configuratie. Het rapport kan de status rapporteren of proberen de machine weer in te zetten met de opgeslagen configuratie. U kunt de opgeslagen configuratie op de pull-server bewerken om ervoor te zorgen dat een machine of een set van machines wordt uitgelijnd met de gewijzigde configuratie.

Een DSC-resource is een module code met specifieke mogelijkheden, zoals het beheren van netwerken, Active Directory of SQL Server. De chocolade DSC-resource weet hoe u toegang kunt krijgen tot een NuGet-server (onder andere), pakketten downloaden, pakketten installeert, enzovoort. Er zijn veel andere DSC-resources in de [PowerShell Gallery](https://www.powershellgallery.com/packages?q=dsc+resources&prerelease=&sortOrder=package-title). U installeert deze modules op de pull-server voor de configuratie van de Azure Automation status voor gebruik door uw configuraties.

Resource Manager-sjablonen bieden een declaratieve manier voor het genereren van uw infra structuur, bijvoorbeeld netwerken, subnetten, netwerk beveiliging en route ring, load balancers, Nic's, Vm's, enzovoort. Hier volgt een [artikel](../azure-resource-manager/management/deployment-models.md) dat het Resource Manager-implementatie model (declaratief) vergelijkt met het Azure Service Management (ASM of klassiek)-implementatie model (verplicht). Dit artikel bevat een bespreking van de belangrijkste resource providers: compute, Storage en Network.

Een belang rijke functie van een resource manager-sjabloon is de mogelijkheid om een VM-extensie te installeren in de virtuele machine wanneer deze is ingericht. Een VM-extensie heeft specifieke mogelijkheden, zoals het uitvoeren van een aangepast script, het installeren van antivirus software en het uitvoeren van een DSC-configuratie script. Er zijn veel andere typen VM-extensies.

## <a name="quick-trip-around-the-diagram"></a>Snelle reis rond het diagram

Vanaf de bovenkant schrijft u uw code, bouwt u deze, test u deze en maakt u een installatie pakket. Choco lade kan verschillende typen installatie pakketten verwerken, zoals MSI, MSU, ZIP. En u beschikt over de volledige kracht van Power shell om de daad werkelijke installatie uit te voeren als de systeem eigen mogelijkheden van Choco lade niet zijn. Plaats het pakket op een plek die bereikbaar is, een package-opslag plaats. In dit voor beeld wordt een open bare map in een Azure Blob-opslag account gebruikt, maar deze kan zich overal bevinden. Chocolade werkt standaard met NuGet-servers en enkele andere voor het beheer van de meta gegevens van pakketten. In [dit artikel](https://github.com/chocolatey/choco/wiki/How-To-Host-Feed) worden de opties beschreven. In het gebruiks voorbeeld wordt NuGet gebruikt. Een Nuspec is meta gegevens over uw pakketten. De Nuspec-gegevens worden gecompileerd in een NuPkg en opgeslagen op een NuGet-server. Als uw configuratie een pakket met de naam opvraagt en naar een NuGet-server verwijst, wordt het pakket door de chocolade DSC-resource op de VM gepakt en geïnstalleerd. U kunt ook een specifieke versie van een pakket aanvragen.

In de linkerbenedenhoek van de afbeelding bevindt zich een Azure Resource Manager sjabloon. In dit voor beeld registreert de VM-extensie de VM met de Azure Automation status configuratie pull-server als een knoop punt. De configuratie wordt twee keer opgeslagen op de pull-server: eenmaal als tekst zonder opmaak en eenmaal gecompileerd als MOF-bestand. In de Azure Portal vertegenwoordigt de MOF een knooppunt configuratie, in tegens telling tot een eenvoudige configuratie. Het is het artefact dat is gekoppeld aan een knoop punt zodat het knoop punt de configuratie kent. In de onderstaande details ziet u hoe u de knooppunt configuratie aan het knoop punt toewijst.

Het maken van de Nuspec, het compileren ervan en het opslaan op een NuGet-server is een kleine ding. En u bent al bezig met het beheren van virtuele machines. 

Als u de volgende stap voor continue implementatie wilt uitvoeren, moet u de pull-server één keer configureren, uw knoop punten tegelijk registreren en de eerste configuratie op de server maken en opslaan. Als pakketten worden geüpgraded en geïmplementeerd naar de opslag plaats, hoeft u de configuratie-en knooppunt configuratie alleen op de pull-server te vernieuwen als dat nodig is.

Als u niet met een resource manager-sjabloon begint, is dat prima. Er zijn Power shell-opdrachten waarmee u uw Vm's bij de pull-server kunt registreren. Zie voor meer informatie [onboarding machines voor beheer door Azure Automation status configuratie](automation-dsc-onboarding.md).

## <a name="about-the-usage-example"></a>Over het gebruiks voorbeeld

Het voor beeld van het gebruik in dit artikel begint met een virtuele machine van een generieke installatie kopie van Windows Server 2012 R2 vanuit de Azure Gallery. U kunt beginnen met een opgeslagen installatie kopie en vervolgens van daaruit verfijnen met de DSC-configuratie.
Het wijzigen van de configuratie die in een installatie kopie wordt geïntegreerde, is echter veel moeilijker dan het dynamisch bijwerken van de configuratie met behulp van DSC.

U hoeft geen Resource Manager-sjabloon en de VM-extensie te gebruiken om deze techniek met uw Vm's te gebruiken. En uw Vm's hoeven zich niet in azure te bezien onder CD-beheer. Dat is alles wat nodig is dat chocolade wordt geïnstalleerd en de LCM die is geconfigureerd op de virtuele machine, zodat u weet waar de pull-server zich bevindt.

Wanneer u een pakket bijwerkt op een virtuele machine die in productie is, moet u deze VM uit de rotatie halen terwijl de update wordt geïnstalleerd. Hoe u dit doet, varieert aanzienlijk. Als u bijvoorbeeld een virtuele machine achter een Azure Load Balancer, kunt u een aangepaste test toevoegen. Laat tijdens het bijwerken van de virtuele machine het eind punt een 400 retour neren. De verfijning die nodig is om deze wijziging te veroorzaken, kan zich in uw configuratie bevindt, evenals de verfijning om deze terug te zetten om een 200 te retour neren nadat de update is voltooid.

De volledige bron voor dit gebruiks voorbeeld bevindt zich in [dit Visual Studio-project](https://github.com/sebastus/ARM/tree/master/CDIaaSVM) op github.

## <a name="step-1-set-up-the-pull-server-and-automation-account"></a>Stap 1: de pull-server en het Automation-account instellen

Op een geverifieerde ( `Connect-AzAccount` ) Power shell-opdracht regel: (kan enkele minuten duren terwijl de pull-server is ingesteld)

```azurepowershell-interactive
New-AzResourceGroup –Name MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES
New-AzAutomationAccount –ResourceGroupName MY-AUTOMATION-RG –Location MY-RG-LOCATION-IN-QUOTES –Name MY-AUTOMATION-ACCOUNT
```

U kunt uw Automation-account in een van de volgende regio's plaatsen (ook wel locaties genoemd): VS-Oost 2, Zuid-Centraal VS, US Gov-Virginia, Europa-west, Zuidoost-Azië, Japan-Oost, Centraal-India en Australië-zuidoost, Canada-centraal Europa-noord.

## <a name="step-2-make-vm-extension-tweaks-to-the-resource-manager-template"></a>Stap 2: de aanpassingen van de VM-extensie maken voor de Resource Manager-sjabloon

Details voor de registratie van de virtuele machine (met behulp van de Power shell DSC-extensie) die is opgenomen in deze [Azure Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/dsc-extension-azure-automation-pullserver)start-sjabloon.
Met deze stap wordt de nieuwe virtuele machine geregistreerd bij de pull-server in de lijst met status configuratie knooppunten. In deze registratie wordt de knooppunt configuratie opgegeven die op het knoop punt moet worden toegepast. Deze knooppunt configuratie hoeft nog niet te bestaan op de pull-server. het is dus een goed probleem dat stap 4 de eerste keer wordt uitgevoerd. Maar u moet in stap 2 de naam van het knoop punt en de naam van de configuratie hebben bepaald. In dit voor beeld is het knoop punt ' isvbox ' en de configuratie is ' ISVBoxConfig '. De naam van de knooppunt configuratie (die moet worden opgegeven in DeploymentTemplate.jsop) is dus ISVBoxConfig. isvbox.

## <a name="step-3-add-required-dsc-resources-to-the-pull-server"></a>Stap 3: de vereiste DSC-resources toevoegen aan de pull-server

De PowerShell Gallery is instrumenteel om DSC-resources in uw Azure Automation-account te installeren.
Ga naar de gewenste resource en klik op de knop implementeren in Azure Automation.

![PowerShell Gallery-voor beeld](./media/automation-dsc-cd-chocolatey/xNetworking.PNG)

Een andere techniek die onlangs aan de Azure Portal is toegevoegd, biedt u de mogelijkheid om nieuwe modules op te halen of bestaande modules bij te werken. Klik op de resource van het Automation-account, de tegel assets en tenslotte op de tegel modules. Met het pictogram Browse Gallery kunt u de lijst met modules in de galerie bekijken, inzoomen op Details en uiteindelijk importeren in uw Automation-account. Dit is een uitstekende manier om uw modules van tijd tot tijd up-to-date te houden. En met de import functie wordt gecontroleerd op afhankelijkheden met andere modules om te controleren of er niets is gesynchroniseerd.

Er is ook een hand matige aanpak die slechts één keer per resource wordt gebruikt, tenzij u deze later wilt bijwerken. Zie [integratie modules ontwerpen voor Azure Automation](https://azure.microsoft.com/blog/authoring-integration-modules-for-azure-automation/)voor meer informatie over het ontwerpen van Power shell-integratie modules.

>[!NOTE]
>De mapstructuur van een Power shell-integratie module voor een Windows-computer is iets anders dan de mapstructuur die wordt verwacht door de Azure Automation. 

1. Installeer [Windows Management Framework V5](https://aka.ms/wmf5latest) (niet vereist voor Windows 10).

2. Installeer de integratie module.

    ```azurepowershell-interactive
    Install-Module –Name MODULE-NAME`    <—grabs the module from the PowerShell Gallery
    ```

3. Kopieer de map module van **C:\Program Files\WindowsPowerShell\Modules\MODULE-name** naar een tijdelijke map.

4. Verwijder voor beelden en documentatie uit de hoofdmap.

5. U kunt de hoofdmap van het ZIP-bestand voorzien van de naam van de map.

6. Plaats het ZIP-bestand in een bereik bare HTTP-locatie, zoals Blob Storage in een Azure Storage-account.

7. Voer de volgende opdracht uit.

    ```azurepowershell-interactive
    New-AzAutomationModule `
      -ResourceGroupName MY-AUTOMATION-RG -AutomationAccountName MY-AUTOMATION-ACCOUNT `
      -Name MODULE-NAME –ContentLinkUri 'https://STORAGE-URI/CONTAINERNAME/MODULE-NAME.zip'
    ```

Het opgenomen voor beeld implementeert deze stappen voor cChoco en xNetworking. 

## <a name="step-4-add-the-node-configuration-to-the-pull-server"></a>Stap 4: de knooppunt configuratie toevoegen aan de pull-server

Er is niets speciaal voor de eerste keer dat u uw configuratie in de pull-server importeert en compileert. Alle latere invoer of compilaties van dezelfde configuratie zien er precies hetzelfde uit. Telkens wanneer u het pakket bijwerkt en het moet worden gepusht naar productie, voert u deze stap uit nadat u hebt gecontroleerd of het configuratie bestand juist is, met inbegrip van de nieuwe versie van uw pakket. Hier ziet u het configuratie bestand **ISVBoxConfig.ps1**:

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

Hier is het **New-ConfigurationScript.ps1** script (gewijzigd voor het gebruik van de module AZ):

```powershell
Import-AzAutomationDscConfiguration `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -SourcePath C:\temp\AzureAutomationDsc\ISVBoxConfig.ps1 `
    -Published –Force

$jobData = Start-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -ConfigurationName ISVBoxConfig

$compilationJobId = $jobData.Id

Get-AzAutomationDscCompilationJob `
    -ResourceGroupName MY-AUTOMATION-RG –AutomationAccountName MY-AUTOMATION-ACCOUNT `
    -Id $compilationJobId
```

Deze stappen resulteren in een nieuwe knooppunt configuratie met de naam **ISVBoxConfig. isvbox** op de pull-server worden geplaatst. De naam van de knooppunt configuratie is gebouwd als `configurationName.nodeName` .

## <a name="step-5-create-and-maintain-package-metadata"></a>Stap 5: de meta gegevens van het pakket maken en onderhouden

Voor elk pakket dat u in de opslag plaats van het pakket plaatst, hebt u een Nuspec nodig waarin het wordt beschreven. Het moet worden gecompileerd en opgeslagen op uw NuGet-server. Dit proces wordt [hier](https://docs.nuget.org/create/creating-and-publishing-a-package)beschreven. 

U kunt **MyGet.org** gebruiken als een NuGet-server. U kunt deze service kopen, maar thee is een gratis start-SKU. Op [NuGet](https://www.nuget.org/)vindt u instructies voor het installeren van uw eigen NuGet-server voor uw privé-pakketten.

## <a name="step-6-tie-it-all-together"></a>Stap 6: alles koppelen

Telkens wanneer een versie QA gaat en is goedgekeurd voor implementatie, wordt het pakket gemaakt en worden nuspec en nupkg bijgewerkt en geïmplementeerd naar de NuGet-server. De configuratie (stap 4) moet ook worden bijgewerkt om overeen te komen met het nieuwe versie nummer. Deze moet vervolgens worden verzonden naar de pull-server en worden gecompileerd.

Vanaf dat moment is het tot de virtuele machines die afhankelijk zijn van die configuratie om de update te halen en te installeren. Elk van deze updates is eenvoudig: slechts een regel of twee van Power shell. Voor Azure DevOps worden sommige van deze ingekapseld in bouw taken die kunnen worden samengevoegd in een build. In dit [artikel](https://www.visualstudio.com/docs/alm-devops-feature-index#continuous-delivery) vindt u meer informatie. Deze [github opslag plaats](https://github.com/Microsoft/vso-agent-tasks) bevat informatie over de beschik bare build-taken.

## <a name="related-articles"></a>Verwante artikelen:
* [Overzicht van Azure Automation DSC](automation-dsc-overview.md)
* [Onboarding van machines voor beheer door Azure Automation DSC](automation-dsc-onboarding.md)

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Automation status configuratie Overview](automation-dsc-overview.md)(Engelstalig) voor een overzicht.
- Zie aan de slag [met Azure Automation status configuratie](automation-dsc-getting-started.md)om aan de slag te gaan met het gebruik van de functie.
- Zie [DSC-configuraties compileren in azure Automation status configuratie](automation-dsc-compile.md)voor meer informatie over het compileren van DSC-configuraties zodat u ze aan doel knooppunten kunt toewijzen.
- Zie [Az.Automation](/powershell/module/az.automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
- Zie [prijzen voor Azure Automation status configuratie](https://azure.microsoft.com/pricing/details/automation/)voor prijs informatie.
