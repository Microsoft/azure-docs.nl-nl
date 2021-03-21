---
title: Configuratie van Azure Automation status inschakelen
description: In dit artikel leest u hoe u computers instelt voor beheer met Azure Automation status configuratie.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: c0dc68bd7dacf0cd7f4be9732d45831e2dbb712c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98897000"
---
# <a name="enable-azure-automation-state-configuration"></a>Configuratie van Azure Automation status inschakelen

In dit onderwerp wordt beschreven hoe u uw machines kunt instellen voor beheer met Azure Automation status configuratie. Zie [Azure Automation status configuratie-overzicht](automation-dsc-overview.md)voor meer informatie over deze service.

## <a name="enable-azure-vms"></a>Virtuele Azure-machines inschakelen

Met de configuratie van Azure Automation status kunt u Azure-Vm's eenvoudig inschakelen voor configuratie beheer, met behulp van de Azure Portal, Azure Resource Manager sjablonen of Power shell. Onder de schermen en zonder een beheerder die een virtuele machine op afstand heeft, registreert de Azure VM desired state Configuration extension de VM met Azure Automation status configuratie. Omdat de Azure-extensie asynchroon wordt uitgevoerd, worden de stappen voor het volgen van de voortgang weer gegeven in de [status controleren van de VM-installatie](#check-status-of-vm-setup).

> [!NOTE]
>Voor het implementeren van DSC voor een Linux-knoop punt wordt de map **map/tmp** gebruikt. Modules die `nxautomation` tijdelijk worden gedownload voor verificatie voordat ze op hun juiste locaties worden geïnstalleerd. Om ervoor te zorgen dat modules correct worden geïnstalleerd, heeft de Log Analytics-agent voor Linux Lees-en schrijf machtigingen nodig voor de map **map/tmp** .<br><br>
>De Log Analytics-agent voor Linux wordt uitgevoerd als de `omsagent` gebruiker. Voer de opdracht uit om >schrijf machtiging voor de gebruiker toe te kennen `omsagent` `setfacl -m u:omsagent:rwx /tmp` .

### <a name="enable-a-vm-using-azure-portal"></a>Een VM inschakelen met behulp van Azure Portal

De configuratie van een virtuele Azure-machine via de [Azure Portal](https://portal.azure.com/)inschakelen:

1. Navigeer naar het Azure Automation-account waarin u virtuele machines kunt inschakelen. 

2. Selecteer op de pagina status configuratie het tabblad **knoop punten** en klik vervolgens op **toevoegen**.

3. Kies een virtuele machine die u wilt inschakelen.

4. Als op de computer de gewenste uitbrei ding voor Power shell niet is geïnstalleerd en de energie status actief is, klikt u op **verbinding maken**.

5. Voer onder **registratie** de [lokale Power shell DSC-Configuration Manager waarden](/powershell/scripting/dsc/managing-nodes/metaConfig) in die vereist zijn voor uw use-case. U kunt desgewenst een knooppunt configuratie invoeren om aan de virtuele machine toe te wijzen.

![VM inschakelen](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>Een VM inschakelen met behulp van Azure Resource Manager sjablonen

U kunt een virtuele machine installeren en inschakelen voor status configuratie met behulp van Azure Resource Manager sjablonen. Zie [server beheerd door desired state Configuration-service](https://azure.microsoft.com/resources/templates/101-automation-configuration/) voor een voorbeeld sjabloon waarmee een bestaande VM kan worden geconfigureerd voor status configuratie. Als u een schaalset voor virtuele machines beheert, raadpleegt u de voorbeeld sjabloon in [configuratie van virtuele-machine schaal sets die wordt beheerd door Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="enable-machines-using-powershell"></a>Machines inschakelen met Power shell

U kunt de cmdlet [REGI ster-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) in Power shell gebruiken om vm's in te scha kelen voor status configuratie. 

> [!NOTE]
>De `Register-AzAutomationDscNode` cmdlet wordt momenteel alleen geïmplementeerd voor computers met Windows, omdat alleen de Windows-extensie wordt geactiveerd.

### <a name="register-vms-across-azure-subscriptions"></a>Vm's registreren in azure-abonnementen

De beste manier om Vm's van andere Azure-abonnementen te registreren, is door gebruik te maken van de DSC-extensie in een Azure Resource Manager-implementatie sjabloon. Voor beelden vindt u in de [desired state Configuration extension met Azure Resource Manager sjablonen](../virtual-machines/extensions/dsc-template.md).

Als u de registratie sleutel en registratie-URL wilt vinden die u wilt gebruiken als para meters in de sjabloon, raadpleegt u [computers veilig inschakelen met registratie](#enable-machines-securely-using-registration).

## <a name="enable-physicalvirtual-windows-machines"></a>Fysieke/virtuele Windows-machines inschakelen

U kunt Windows-servers met on-premises of in andere Cloud omgevingen (waaronder AWS EC2-instanties) inschakelen voor Azure Automation status configuratie. De servers moeten [uitgaande toegang hebben tot Azure](automation-dsc-overview.md#network-planning).

1. Zorg ervoor dat de meest recente versie van [WMF 5](https://aka.ms/wmf5latest) is geïnstalleerd op de computers om status configuratie in te scha kelen. Daarnaast moet WMF 5 zijn geïnstalleerd op de computer die u gebruikt om de computers in te scha kelen.
1. Volg de instructies in [DSC-configuratie genereren](#generate-dsc-metaconfigurations) om een map te maken met de vereiste DSC-configuratie. 
1. Gebruik de volgende cmdlet om de Power shell DSC-configuratie op afstand toe te passen op de computers die u wilt inschakelen. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Als u de Power shell DSC-webconfiguraties niet op afstand kunt Toep assen, kopieert u de map met de **configuratie** van de werkmap naar de machines die u inschakelt. Voeg vervolgens code toe om [set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) lokaal op de computers aan te roepen.
1. Controleer met behulp van de Azure Portal of cmdlets of de computers worden weer gegeven als status configuratie knooppunten die zijn geregistreerd in uw Azure Automation-account.

## <a name="enable-physicalvirtual-linux-machines"></a>Fysieke/virtuele Linux-machines inschakelen

U kunt Linux-servers die on-premises of in andere Cloud omgevingen worden uitgevoerd, inschakelen voor status configuratie. De servers moeten [uitgaande toegang hebben tot Azure](automation-dsc-overview.md#network-planning).

1. Zorg ervoor dat de meest recente versie van de [Power shell desired state Configuration voor Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) is geïnstalleerd op de computers om status configuratie in te scha kelen.
2. Als de [lokale Power shell DSC-Configuration Manager standaard waarden](/powershell/scripting/dsc/managing-nodes/metaConfig4) overeenkomen met uw use-case en u computers wilt inschakelen, zodat deze beide worden opgehaald uit en rapporteren aan de status configuratie:

   - Op elke Linux-machine die u wilt inschakelen, gebruikt `Register.py` u om de computer in te scha kelen met de lokale Configuration Manager standaard instellingen van Power shell.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Als u de registratie sleutel en registratie-URL voor uw Automation-account wilt vinden, raadpleegt u [computers veilig inschakelen met registratie](#enable-machines-securely-using-registration).

3. Als de standaard instellingen van de Power shell DSC Local Configuration Manager (LCM) niet overeenkomen met uw use-case of als u computers wilt inschakelen die alleen rapporteren aan Azure Automation status configuratie, volgt u stap 4-7. Ga anders verder met stap 7.

4. Volg de aanwijzingen in de sectie [DSC-mailconfiguraties genereren](#generate-dsc-metaconfigurations) om een map te maken met de vereiste DSC-configuratie.

5. Zorg ervoor dat de meest recente versie van [WMF 5](https://aka.ms/wmf5latest) is geïnstalleerd op de computer die wordt gebruikt om uw computers in te scha kelen voor status configuratie.

6. Voeg als volgt code toe om de Power shell DSC-configuratie op afstand toe te passen op de computers die u wilt inschakelen.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Als u de Power shell DSC-webconfiguraties niet op afstand kunt Toep assen, kopieert u de-configuratie die overeenkomt met de externe computers uit de map die wordt beschreven in stap 4 van de Linux-machines.

8. Voeg code toe om `Set-DscLocalConfigurationManager.py` lokaal op elke Linux-machine aan te roepen om status configuratie in te scha kelen.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Gebruik de Azure Portal of cmdlets om ervoor te zorgen dat de computers nu kunnen worden weer gegeven als DSC-knoop punten die zijn geregistreerd in uw Azure Automation-account.

## <a name="generate-dsc-metaconfigurations"></a>DSC-mailconfiguraties genereren

Als u een machine voor status configuratie wilt inschakelen, kunt u een [DSC-configuratie](/powershell/scripting/dsc/managing-nodes/metaConfig)genereren. Deze configuratie vertelt de DSC-agent om van en/of rapport te halen uit Azure Automation status configuratie. U kunt een DSC-configuratie genereren voor de configuratie van de Azure Automation status met behulp van een Power shell DSC-configuratie of de Azure Automation Power shell-cmdlets.

> [!NOTE]
> DSC-mailconfiguraties bevatten de geheimen die nodig zijn om een machine in een Automation-account in te scha kelen voor beheer. Zorg ervoor dat u de DSC-configuratie die u maakt op de juiste wijze beveiligt, of verwijder ze na gebruik.

Proxy ondersteuning voor-configuratie wordt beheerd door de [lokale Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig), de Windows Power shell DSC-engine. De LCM wordt uitgevoerd op alle doel knooppunten en is verantwoordelijk voor het aanroepen van de configuratie resources die zijn opgenomen in een DSC-configuratie script. U kunt ondersteuning voor proxy in een-configuratie opnemen door definities van en eigenschappen toe te voegen aan de hand van `ProxyURL` `ProxyCredential` de `ConfigurationRepositoryWeb` `ResourceRepositoryWeb` elementen, en `ReportServerWeb` . Een voor beeld van de URL-instelling is `ProxyURL = "http://172.16.3.6:3128";` . De `ProxyCredential` eigenschap wordt ingesteld op een `PSCredential` object, zoals beschreven in [referenties beheren in azure Automation](shared-resources/credentials.md). 

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>DSC-mailconfiguraties genereren met behulp van een DSC-configuratie

1. Open VSCode (of uw favoriete editor) als beheerder op een computer in uw lokale omgeving. Op de computer moet de nieuwste versie van [WMF 5](https://aka.ms/wmf5latest) zijn geïnstalleerd.
1. Kopieer het volgende script lokaal. Dit script bevat een Power shell DSC-configuratie voor het maken van een eigen configuratie en een opdracht voor het maken van de configuratie van de-de-de-de-de-de

    > [!NOTE]
    > Naam configuratie knooppunt configuratie namen zijn hoofdletter gevoelig in de Azure Portal. Als de aanvraag niet overeenkomt, wordt het knoop punt niet weer gegeven op het tabblad **knoop punten** .

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

1. Vul de registratie sleutel en de URL voor uw Automation-account in, evenals de namen van de computers die u wilt inschakelen. Alle andere para meters zijn optioneel. Als u de registratie sleutel en registratie-URL voor uw Automation-account wilt vinden, raadpleegt u [computers veilig inschakelen met registratie](#enable-machines-securely-using-registration).

1. Als u wilt dat de computers DSC-status informatie rapporteren aan Azure Automation status configuratie, maar niet pull-configuratie-of Power shell-modules, stelt u de `ReportOnly` para meter in op True.

1. Als `ReportOnly` niet is ingesteld, rapporteert de computers DSC-status informatie aan Azure Automation status configuratie en pull-configuratie of Power shell-modules. Stel para meters in overeenkomstig de `ConfigurationRepositoryWeb` `ResourceRepositoryWeb` elementen,, en `ReportServerWeb` .

1. Voer het script uit. U hebt nu een werkmap met de naam **DscMetaConfigs**, die de Power shell DSC-configuratie bevat voor de computers die u wilt inschakelen (als beheerder).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>DSC-mailconfiguraties genereren met behulp van Azure Automation-cmdlets

Als de standaard instellingen van de Power shell DSC-functie overeenkomen met uw use-case en u wilt dat computers worden opgehaald van en rapporteren aan Azure Automation status configuratie, kunt u de benodigde DSC-standaard configuraties meer genereren met behulp van de Azure Automation-cmdlets.

1. Open de Power shell-console of VSCode als beheerder op een computer in uw lokale omgeving.
2. Maak verbinding met Azure Resource Manager via [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount).
3. Down load de Power shell DSC-configuratie voor de computers die u wilt inschakelen op basis van het Automation-account waarin u knoop punten instelt.

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

1. U hebt nu een **DscMetaConfigs** -map met de Power shell DSC-configuratie voor de computers om in te scha kelen (als beheerder).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>Computers veilig met registratie inschakelen

U kunt computers veilig voor een Azure Automation-account inschakelen via het WMF 5 DSC-registratie protocol. Met dit protocol kan een DSC-knoop punt worden geverifieerd bij een Power shell DSC-pull-of-rapport server, met inbegrip van Azure Automation status configuratie. Het knoop punt wordt geregistreerd bij de server op de registratie-URL en verifieert met behulp van een registratie sleutel. Tijdens de registratie onderhandelen het DSC-knoop punt en de DSC-pull-of rapport server een uniek certificaat voor het knoop punt dat moet worden gebruikt voor verificatie bij de server post-registratie. Dit proces voor komt dat ingeschakelde knoop punten elkaar imiteren, bijvoorbeeld als een knoop punt is aangetast en zich op een kwaad aardig manier bevindt. Na de registratie wordt de registratie sleutel niet opnieuw gebruikt voor verificatie en wordt deze verwijderd uit het knoop punt.

U kunt de informatie ophalen die is vereist voor het status configuratie registratie protocol uit **sleutels** onder **account instellingen** in de Azure Portal. 

![Sleutels en URL van Azure Automation](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- De registratie-URL is het URL-veld op de pagina sleutels.
- De registratie sleutel is de waarde van het **primaire toegangs sleutel** veld of het veld **secundaire toegangs sleutel** op de pagina sleutels. Beide sleutels kunnen worden gebruikt.

Voor extra beveiliging kunt u op elk gewenst moment de primaire en secundaire toegangs sleutels van een Automation-account opnieuw genereren op de pagina sleutels. Het opnieuw genereren van sleutels voor komt dat toekomstige knooppunt registraties eerdere sleutels gebruiken.

## <a name="re-register-a-node"></a>Een knoop punt opnieuw registreren

Na het registreren van een computer als een DSC-knoop punt in Azure Automation status configuratie, zijn er verschillende redenen waarom u het knoop punt in de toekomst mogelijk opnieuw moet registreren.

- **Certificaat verlenging.** Voor versies van Windows Server vóór Windows Server 2019 onderhandelt elk knoop punt automatisch een uniek certificaat voor verificatie dat verloopt na één jaar. Als een certificaat zonder verlenging is verlopen, kan het knoop punt niet communiceren met Azure Automation en is dit gemarkeerd `Unresponsive` . Op dit moment kan het Power shell DSC-registratie protocol niet automatisch certificaten vernieuwen wanneer deze bijna verlopen. u moet de knoop punten na een jaar opnieuw registreren. Zorg ervoor dat op elk knoop punt WMF 5 RTM wordt uitgevoerd voordat u de registratie opnieuw uitvoert. 

    Opnieuw registreren is 90 dagen of minder geldig vanaf de verloop tijd van het certificaat, of op elk gewenst moment na de verloop tijd van het certificaat, worden de resultaten van een nieuw certificaat gegenereerd en gebruikt. Een oplossing voor dit probleem is opgenomen in Windows Server 2019 en hoger.

- **Wijzigingen in DSC LCM-waarden.** Mogelijk moet u de [Power shell DSC ICM-waarden](/powershell/scripting/dsc/managing-nodes/metaConfig4) wijzigen die zijn ingesteld tijdens de eerste registratie van het knoop punt, bijvoorbeeld `ConfigurationMode` . Op dit moment kunt u deze waarden van de DSC-agent alleen wijzigen door opnieuw te registreren. De enige uitzonde ring is de knooppunt configuratie waarde die aan het knoop punt is toegewezen. U kunt dit rechtstreeks in Azure Automation DSC wijzigen.

U kunt een knoop punt opnieuw registreren, net zoals u het knoop punt in eerste instantie hebt geregistreerd, met behulp van een van de methoden die in dit document worden beschreven. U hoeft de registratie van een knoop punt bij Azure Automation status configuratie niet ongedaan te maken voordat u het opnieuw registreert.

## <a name="check-status-of-vm-setup"></a>De status van de VM-installatie controleren

Met status configuratie kunt u gemakkelijk Azure Windows-Vm's inschakelen voor configuratie beheer. Onder de schermen wordt de Azure VM desired state Configuration-extensie gebruikt voor het registreren van de virtuele machine met Azure Automation status configuratie. Omdat de configuratie-uitbrei ding desired state van Azure VM asynchroon wordt uitgevoerd, kunt u de voortgang bijhouden en de uitvoering ervan oplossen.

> [!NOTE]
> Elke methode voor het inschakelen van Azure Windows-Vm's voor status configuratie die gebruikmaakt van de desired state Configuration-extensie van Azure, kan tot een uur duren voordat Azure Automation de Vm's als geregistreerd weergeeft. Deze vertraging wordt veroorzaakt door de installatie van WMF 5 op de VM door de configuratie-uitbrei ding desired state van Azure VM, die vereist is om Vm's in te scha kelen voor status configuratie.

De status van de configuratie-uitbrei ding voor de gewenste Azure-VM weer geven:

1. Navigeer in het Azure Portal naar de virtuele machine die wordt ingeschakeld.
2. Klik op **uitbrei dingen** onder **instellingen**. 
3. Selecteer nu **DSC** of **DSCForLinux**, afhankelijk van uw besturings systeem. 
4. Klik op **gedetailleerde status weer geven** voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie aan de slag [met de configuratie van de Azure Automation-status](automation-dsc-getting-started.md)om aan de slag te gaan.
- Zie [DSC-configuraties compileren in azure Automation status configuratie](automation-dsc-compile.md)voor meer informatie over het compileren van DSC-configuraties zodat u ze aan doel knooppunten kunt toewijzen.
- Zie [Az.Automation](/powershell/module/az.automation) voor een naslagdocumentatie voor een PowerShell-cmdlet.
- Zie [prijzen voor Azure Automation status configuratie](https://azure.microsoft.com/pricing/details/automation/)voor prijs informatie.
- Zie voor een voor beeld van het gebruik van Azure Automation status configuratie in een pijp lijn voor continue implementatie een [continue implementatie met Choco lade instellen](automation-dsc-cd-chocolatey.md).
- Zie [problemen met de configuratie van Azure Automation status oplossen](./troubleshoot/desired-state-configuration.md)voor informatie over het oplossen van problemen.
