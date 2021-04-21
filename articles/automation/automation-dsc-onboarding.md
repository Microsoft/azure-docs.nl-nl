---
title: Schakel Azure Automation State Configuration
description: In dit artikel wordt beschreven hoe u machines kunt instellen voor beheer met Azure Automation State Configuration.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: d338c5f34d49663345582198ff53ba50a2919d7e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829418"
---
# <a name="enable-azure-automation-state-configuration"></a>Schakel Azure Automation State Configuration

In dit onderwerp wordt beschreven hoe u uw machines kunt instellen voor beheer met Azure Automation State Configuration. Zie overzicht van Azure Automation State Configuration voor [meer informatie over deze service.](automation-dsc-overview.md)

## <a name="enable-azure-vms"></a>Azure-VM's inschakelen

Azure Automation State Configuration kunt u eenvoudig Azure-VM's inschakelen voor configuratiebeheer met behulp van de Azure Portal, Azure Resource Manager-sjablonen of PowerShell. Achter de camera en zonder dat een beheerder extern toegang moet hebben tot een VM, registreert de Azure VM-Desired State Configuration de VM bij Azure Automation State Configuration. Omdat de Azure-extensie asynchroon wordt uitgevoerd, worden de stappen voor het bijhouden van de voortgang ervan opgegeven in [Status van VM-installatie controleren.](#check-status-of-vm-setup)

> [!NOTE]
>Bij het implementeren van DSC op een Linux-knooppunt wordt de **map /tmp** gebruikt. Modules zoals `nxautomation` worden tijdelijk gedownload voor verificatie voordat ze op de juiste locaties worden geïnstalleerd. Om ervoor te zorgen dat modules correct worden geïnstalleerd, heeft de Log Analytics-agent voor Linux lees-/schrijfmachtigingen nodig voor de **map /tmp.**<br><br>
>De Log Analytics-agent voor Linux wordt uitgevoerd als `omsagent` de gebruiker. Voer de >uit om de gebruiker `omsagent` schrijfmachtigingen te `setfacl -m u:omsagent:rwx /tmp` verlenen.

### <a name="enable-a-vm-using-azure-portal"></a>Een VM inschakelen met Azure Portal

Een virtuele Azure-State Configuration via de [Azure Portal:](https://portal.azure.com/)

1. Navigeer naar Azure Automation account waarin u VM's wilt inschakelen. 

2. Selecteer op State Configuration pagina Knooppunten en klik vervolgens op **Toevoegen.** 

3. Kies een VM die u wilt inschakelen.

4. Als op de machine niet de gewenste PowerShell-statusextensie is geïnstalleerd en de energietoestand actief is, klikt u op **Verbinden.**

5. Voer **onder Registratie** de lokale [PowerShell DSC-Configuration Manager waarden in](/powershell/scripting/dsc/managing-nodes/metaConfig) die vereist zijn voor uw use-case. U kunt eventueel een knooppuntconfiguratie invoeren om toe te wijzen aan de VM.

![VM inschakelen](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>Een VM inschakelen met behulp Azure Resource Manager sjablonen

U kunt een VM installeren en inschakelen voor State Configuration met behulp Azure Resource Manager sjablonen. Zie [Server beheerd door Desired State Configuration service](https://azure.microsoft.com/resources/templates/101-automation-configuration/) voor een voorbeeldsjabloon waarmee een bestaande virtuele State Configuration. Als u een virtuele-machineschaalset beheert, bekijkt u de voorbeeldsjabloon in Configuratie van virtuele-machineschaalsets beheerd [door Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="enable-machines-using-powershell"></a>Machines inschakelen met PowerShell

U kunt de cmdlet [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) in PowerShell gebruiken om VM's in te State Configuration. 

> [!NOTE]
>De `Register-AzAutomationDscNode` cmdlet is momenteel alleen geïmplementeerd voor computers met Windows, omdat deze alleen de Windows-extensie activeert.

### <a name="register-vms-across-azure-subscriptions"></a>VM's registreren voor Azure-abonnementen

De beste manier om VM's uit andere Azure-abonnementen te registreren, is door de DSC-extensie te gebruiken in een Azure Resource Manager implementatiesjabloon. Voorbeelden zijn te vinden in [Desired State Configuration-extensie met Azure Resource Manager-sjablonen.](../virtual-machines/extensions/dsc-template.md)

Zie Machines veilig inschakelen met registratie voor informatie over de registratiesleutel en registratie-URL die als parameters in de sjabloon [moeten worden gebruikt.](#enable-machines-securely-using-registration)

## <a name="enable-physicalvirtual-windows-machines"></a>Fysieke/virtuele Windows-machines inschakelen

U kunt Windows-servers die on-premises of in andere cloudomgevingen (waaronder AWS EC2-exemplaren) worden uitgevoerd, inschakelen voor Azure Automation State Configuration. De servers moeten uitgaande [toegang hebben tot Azure](automation-dsc-overview.md#network-planning).

1. Zorg ervoor dat de meest recente versie van [WMF 5](https://aka.ms/wmf5latest) is geïnstalleerd op de computers om deze in te State Configuration. Daarnaast moet WMF 5 worden geïnstalleerd op de computer die u gebruikt voor het inschakelen van de machines.
1. Volg de instructies in [DSC-metaconfiguraties](#generate-dsc-metaconfigurations) genereren om een map te maken met de vereiste DSC-metaconfiguraties. 
1. Gebruik de volgende cmdlet om de PowerShell DSC-metaconfiguraties op afstand toe te passen op de computers die moeten worden ingeschakeld. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Als u de PowerShell DSC-metaconfiguraties niet op afstand kunt toepassen, kopieert u de **map metaconfiguraties** naar de machines die u inschakelen. Voeg vervolgens code toe om [Set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) lokaal aan te roepen op de machines.
1. Gebruik de Azure Portal of cmdlets om te controleren of de machines worden weergegeven als State Configuration knooppunten die zijn geregistreerd in uw Azure Automation account.

## <a name="enable-physicalvirtual-linux-machines"></a>Fysieke/virtuele Linux-machines inschakelen

U kunt Linux-servers die on-premises of in andere cloudomgevingen worden uitgevoerd, inschakelen voor State Configuration. De servers moeten uitgaande [toegang hebben tot Azure](automation-dsc-overview.md#network-planning).

1. Zorg ervoor dat de nieuwste versie van [PowerShell Desired State Configuration voor Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) is geïnstalleerd op de computers om deze in te State Configuration.
2. Als de standaardwaarden van de lokale [PowerShell DSC Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig4) overeenkomen met uw use-case en u machines wilt inschakelen zodat ze zowel van en naar de volgende State Configuration:

   - Gebruik op elke Linux-computer die moet worden ingeschakeld om de computer in te stellen met de standaardinstellingen voor Configuration Manager `Register.py` PowerShell DSC.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Zie Machines veilig inschakelen met registratie voor informatie over de registratiesleutel en registratie-URL [voor uw Automation-account.](#enable-machines-securely-using-registration)

3. Als de standaardinstellingen voor local Configuration Manager (LCM) van PowerShell DSC niet overeenkomen met uw use-case of als u computers wilt inschakelen die alleen rapporteren aan Azure Automation State Configuration, volgt u de stappen 4-7. Ga anders rechtstreeks door naar stap 7.

4. Volg de instructies in de [sectie DSC-metaconfiguraties](#generate-dsc-metaconfigurations) genereren om een map te produceren met de vereiste DSC-metaconfiguraties.

5. Zorg ervoor dat de meest recente versie van [WMF 5](https://aka.ms/wmf5latest) is geïnstalleerd op de computer die wordt gebruikt om uw computers in te State Configuration.

6. Voeg als volgt code toe om de PowerShell DSC-metaconfiguraties op afstand toe te passen op de machines die moeten worden ingeschakeld.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Als u de PowerShell DSC-metaconfiguraties niet op afstand kunt toepassen, kopieert u de metaconfiguraties die overeenkomen met de externe computers uit de map die in stap 4 wordt beschreven naar de Linux-machines.

8. Voeg code toe om lokaal aan `Set-DscLocalConfigurationManager.py` te roepen op elke Linux-machine om deze in te State Configuration.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Gebruik de Azure Portal of cmdlets om ervoor te zorgen dat de machines die u wilt inschakelen nu worden gebruikt als DSC-knooppunten die zijn geregistreerd in uw Azure Automation account.

## <a name="generate-dsc-metaconfigurations"></a>DSC-metaconfiguraties genereren

Als u een computer wilt inschakelen State Configuration, kunt u een [DSC-metaconfiguratie genereren.](/powershell/scripting/dsc/managing-nodes/metaConfig) Deze configuratie vertelt de DSC-agent om pull-taken uit en/of te rapporteren aan Azure Automation State Configuration. U kunt een DSC-metaconfiguratie voor Azure Automation State Configuration genereren met behulp van een PowerShell DSC-configuratie of de Azure Automation PowerShell-cmdlets.

> [!NOTE]
> DSC-metaconfiguraties bevatten de geheimen die nodig zijn om een machine in een Automation-account in te stellen voor beheer. Zorg ervoor dat u de DSC-metaconfiguraties die u maakt op de juiste manier bebeveiligen of na gebruik verwijdert.

Proxyondersteuning voor metaconfiguraties wordt beheerd door de [lokale Configuration Manager,](/powershell/scripting/dsc/managing-nodes/metaconfig)de Windows PowerShell DSC-engine. LCM wordt uitgevoerd op alle doelknooppunten en is verantwoordelijk voor het aanroepen van de configuratiebronnen die zijn opgenomen in een DSC-metaconfiguratiescript. U kunt proxyondersteuning opnemen in een metaconfiguratie door zo nodig definities van en eigenschappen op te nemen `ProxyURL` in de blokken , en `ProxyCredential` `ConfigurationRepositoryWeb` `ResourceRepositoryWeb` `ReportServerWeb` . Een voorbeeld van de URL-instelling is `ProxyURL = "http://172.16.3.6:3128";` . De `ProxyCredential` eigenschap is ingesteld op een `PSCredential` -object, zoals beschreven in [Referenties beheren in Azure Automation.](shared-resources/credentials.md) 

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>DSC-metaconfiguraties genereren met behulp van een DSC-configuratie

1. Open VSCode (of uw favoriete editor) als beheerder op een computer in uw lokale omgeving. Op de computer moet de nieuwste versie van [WMF 5 zijn](https://aka.ms/wmf5latest) geïnstalleerd.
1. Kopieer het volgende script lokaal. Dit script bevat een PowerShell DSC-configuratie voor het maken van metaconfiguraties en een opdracht voor het maken van de metaconfiguratie.

    > [!NOTE]
    > State Configuration namen van knooppuntconfiguraties zijn in de Azure Portal. Als de case niet overeenkomen, wordt het knooppunt niet weergegeven op het **tabblad Knooppunten.**

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl       = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. Vul de registratiesleutel en URL voor uw Automation-account in, evenals de namen van de machines die u wilt inschakelen. Alle andere parameters zijn optioneel. Zie Machines veilig inschakelen met registratie voor informatie over de registratiesleutel en registratie-URL [voor uw Automation-account.](#enable-machines-securely-using-registration)

1. Als u wilt dat de computers DSC-statusinformatie rapporteren aan Azure Automation State Configuration, maar niet pull-configuratie of PowerShell-modules, stelt u de `ReportOnly` parameter in op true.

1. Als `ReportOnly` niet is ingesteld, rapporteren de machines DSC-statusinformatie aan Azure Automation State Configuration en pull-configuratie of PowerShell-modules. Stel de parameters dienovereenkomstig in de `ConfigurationRepositoryWeb` `ResourceRepositoryWeb` blokken , en `ReportServerWeb` in.

1. Voer het script uit. U hebt nu een werkmap met de **naam DscMetaConfigs** met de PowerShell DSC-metaconfiguraties voor de machines die u wilt inschakelen (als beheerder).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>DSC-metaconfiguraties genereren met behulp Azure Automation cmdlets

Als de standaardinstellingen van PowerShell DSC LCM overeenkomen met uw use-case en u computers wilt inschakelen voor zowel pull van als rapportage naar Azure Automation State Configuration, kunt u de benodigde DSC-metaconfiguraties eenvoudiger genereren met behulp van de Azure Automation-cmdlets.

1. Open de PowerShell-console of VSCode als beheerder op een computer in uw lokale omgeving.
2. Maak verbinding met Azure Resource Manager met [behulp van Connect-AzAccount.](/powershell/module/Az.Accounts/Connect-AzAccount)
3. Download de PowerShell DSC-metaconfiguraties voor de machines die u wilt inschakelen van het Automation-account waarin u knooppunten instelt.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. U hebt nu een **map DscMetaConfigs** met de PowerShell DSC-metaconfiguraties voor de machines die u wilt inschakelen (als beheerder).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>Machines veilig inschakelen met behulp van registratie

U kunt machines veilig inschakelen voor een Azure Automation-account via het WMF 5 DSC-registratieprotocol. Met dit protocol kan een DSC-knooppunt worden geverifieerd bij een PowerShell DSC-pull- of rapportserver, inclusief Azure Automation State Configuration. Het knooppunt registreert zich bij de server bij de registratie-URL en verifieert met behulp van een registratiesleutel. Tijdens de registratie onderhandelen het DSC-knooppunt en de DSC-pull-/rapportserver over een uniek certificaat dat het knooppunt kan gebruiken voor verificatie na de registratie bij de server. Dit proces voorkomt dat ingeschakelde knooppunten elkaar imiteren, bijvoorbeeld als een knooppunt is aangetast en zich kwaadwillend gedraagt. Na de registratie wordt de registratiesleutel niet opnieuw gebruikt voor verificatie en wordt deze verwijderd uit het knooppunt.

U kunt de vereiste informatie voor het State Configuration-registratieprotocol ophalen uit **Sleutels** onder **Accountinstellingen** in de Azure Portal. 

![Azure Automation-sleutels en -URL](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Registratie-URL is het URL-veld op de pagina Sleutels.
- Registratiesleutel is de waarde van het veld **Primaire toegangssleutel** of het veld Secundaire **toegangssleutel** op de pagina Sleutels. Beide sleutels kunnen worden gebruikt.

Voor extra beveiliging kunt u op elk moment op de pagina Sleutels de primaire en secundaire toegangssleutels van een Automation-account opnieuw maken. Het opnieuw maken van sleutels voorkomt dat toekomstige knooppuntregistraties eerdere sleutels gebruiken.

## <a name="re-register-a-node"></a>Een knooppunt opnieuw registreren

Nadat u een computer hebt geregistreerd als een DSC-knooppunt in Azure Automation State Configuration, zijn er verschillende redenen waarom u dat knooppunt in de toekomst mogelijk opnieuw moet registreren.

- **Certificaatvernieuwing.** Voor versies van Windows Server vóór Windows Server 2019 onderhandelt elk knooppunt automatisch over een uniek certificaat voor verificatie dat na één jaar verloopt. Als een certificaat verloopt zonder verlenging, kan het knooppunt niet communiceren met Azure Automation en is het `Unresponsive` gemarkeerd. Op dit moment kan het DSC-registratieprotocol van PowerShell certificaten niet automatisch vernieuwen wanneer ze bijna zijn verlopen en moet u de knooppunten na een jaar opnieuw registreren. Voordat u zich opnieuw registreert, moet u ervoor zorgen dat op elk knooppunt WMF 5 RTM wordt uitgevoerd. 

    Opnieuw registreren uitgevoerd 90 dagen of minder vanaf de verlooptijd van het certificaat, of op een bepaald moment na de verlooptijd van het certificaat, resulteert in een nieuw certificaat wordt gegenereerd en gebruikt. Een oplossing voor dit probleem is opgenomen in Windows Server 2019 en hoger.

- **Wijzigingen in DSC LCM-waarden.** Mogelijk moet u de [PowerShell DSC LCM-waarden](/powershell/scripting/dsc/managing-nodes/metaConfig4) wijzigen die zijn ingesteld tijdens de eerste registratie van het knooppunt, bijvoorbeeld `ConfigurationMode` . Op dit moment kunt u deze DSC-agentwaarden alleen wijzigen via opnieuw registreren. De enige uitzondering hierop is de waarde voor Knooppuntconfiguratie die is toegewezen aan het knooppunt. U kunt dit rechtstreeks in Azure Automation DSC wijzigen.

U kunt een knooppunt opnieuw registreren op dezelfde manier als u het knooppunt in eerste instantie hebt geregistreerd, met behulp van een van de methoden die in dit document worden beschreven. U hoeft de registratie van een knooppunt bij het Azure Automation State Configuration ongedaan te maken voordat u het opnieuw registreert.

## <a name="check-status-of-vm-setup"></a>De status van de VM-installatie controleren

State Configuration kunt u eenvoudig Azure Windows-VM's inschakelen voor configuratiebeheer. Achter de hand wordt de Azure VM Desired State Configuration-extensie gebruikt om de VM te registreren bij Azure Automation State Configuration. Omdat de Azure VM-Desired State Configuration-extensie asynchroon wordt uitgevoerd, kan het belangrijk zijn om de voortgang bij te houden en problemen met de uitvoering op te lossen.

> [!NOTE]
> Elke methode voor het inschakelen van Azure Windows-VM's voor State Configuration die gebruikmaakt van de Azure VM Desired State Configuration-extensie kan tot een uur duren voordat Azure Automation VM's als geregistreerd hebben. Deze vertraging wordt veroorzaakt door de installatie van WMF 5 op de VM door de Azure VM Desired State Configuration-extensie, die vereist is om VM's in te State Configuration.

De status van de Azure VM-extensie Desired State Configuration weergeven:

1. Navigeer Azure Portal de VM die wordt ingeschakeld.
2. Klik **op Extensies** onder **Instellingen.** 
3. Selecteer nu **DSC** of **DSCForLinux,** afhankelijk van uw besturingssysteem. 
4. Klik op Gedetailleerde status weergeven **voor meer informatie.**

## <a name="next-steps"></a>Volgende stappen

- Zie Aan de slag met Azure Automation State Configuration om aan de [slag te gaan.](automation-dsc-getting-started.md)
- Zie Compile DSC configurations in Azure Automation State Configuration (DSC-configuraties compileren in Azure Automation State Configuration) voor meer informatie over het compileren van [DSC-configuraties, zodat u ze kunt toewijzen aan doelknooppunten.](automation-dsc-compile.md)
- Zie [Az.Automation](/powershell/module/az.automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
- Zie prijzen voor Azure Automation State Configuration informatie over [prijzen.](https://azure.microsoft.com/pricing/details/automation/)
- Zie Continue implementatie instellen met Chocolatey Azure Automation State Configuration voor een voorbeeld van het gebruik van Azure Automation State Configuration in een pijplijn voor [continue implementatie.](automation-dsc-cd-chocolatey.md)
- Zie Problemen met Azure Automation State Configuration voor informatie over [het oplossen van Azure Automation State Configuration.](./troubleshoot/desired-state-configuration.md)
