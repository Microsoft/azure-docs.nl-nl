---
title: Schaal sessie hosts Azure Automation virtueel bureau blad van Windows (klassiek)-Azure
description: Het automatisch schalen van hosts met Windows Virtual Desktop (klassieke) sessies met Azure Automation.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd14af6c95654708f339f4a68cd333d0e3162553
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89078177"
---
# <a name="scale-windows-virtual-desktop-classic-session-hosts-using-azure-automation"></a>Windows Virtual Desktop (klassieke) sessie-hosts schalen met behulp van Azure Automation

>[!IMPORTANT]
>Deze inhoud is van toepassing op Windows Virtual Desktop (klassiek), dat geen ondersteuning biedt voor Azure Resource Manager Windows Virtual Desktop-objecten.

U kunt de totale kosten voor het implementeren van virtuele Windows-Bureau bladen verlagen door uw virtuele machines (Vm's) te schalen. Dit betekent dat de virtuele machines van de host worden afgesloten en opnieuw worden toegewezen tijdens gebruiks uren en vervolgens weer worden ingeschakeld en opnieuw worden toegewezen tijdens piek uren.

In dit artikel vindt u meer informatie over het hulp programma voor schalen dat is gebouwd met het Azure Automation-account en de Azure Logic-app, waarmee de Vm's van de virtuele machine in uw Windows-desktop omgeving automatisch worden geschaald. Voor meer informatie over het gebruik van het hulp programma schalen gaat u verder met [vereisten](#prerequisites).

## <a name="how-the-scaling-tool-works"></a>De werking van het hulp programma voor schalen

Het hulp programma voor schalen biedt een voordelige automatiserings optie voor klanten die hun host-VM-kosten voor hun sessie willen optimaliseren.

U kunt het hulp programma schalen gebruiken voor het volgende:

- Plan Vm's om te starten en stoppen op basis van pieken en Off-Peak kantoor uren.
- Uitschalen van Vm's op basis van het aantal sessies per CPU-kern.
- Schaal in Vm's tijdens Off-Peak uur, waardoor het minimale aantal actieve host-Vm's wordt uitgevoerd.

Het hulp programma voor schalen maakt gebruik van een combi natie van een Azure Automation account, een Power shell-runbook, een webhook en de Azure Logic-app die u kunt gebruiken. Wanneer het hulp programma wordt uitgevoerd, roept Azure Logic app een webhook aan om het Azure Automation runbook te starten. Het runbook maakt vervolgens een taak.

Tijdens de piek duur van het gebruik controleert de taak het huidige aantal sessies en de VM-capaciteit van de huidige actieve sessiehost voor elke hostgroep. Deze informatie wordt gebruikt om te berekenen of de actieve host-Vm's bestaande sessies kunnen ondersteunen op basis van de para meter *SessionThresholdPerCPU* die voor het **CreateOrUpdateAzLogicApp.ps1** bestand is gedefinieerd. Als de virtuele machines van de sessiehost geen bestaande sessies kunnen ondersteunen, start de taak extra host-Vm's in de hostgroep.

>[!NOTE]
>*SessionThresholdPerCPU* beperkt het aantal sessies op de virtuele machine niet. Deze para meter bepaalt alleen wanneer nieuwe Vm's moeten worden gestart om de verbindingen te verdelen. Als u het aantal sessies wilt beperken, moet u de instructies [set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) volgen om de *MaxSessionLimit* -para meter dienovereenkomstig te configureren.

Tijdens het gebruik van de tijd bepaalt de taak hoeveel host-Vm's voor sessies moeten worden afgesloten op basis van de para meter *MinimumNumberOfRDSH* . Als u de para meter *LimitSecondsToForceLogOffUser* instelt op een positieve waarde die niet gelijk is aan nul, stelt de taak de host-vm's voor de sessie in op de afvoer modus om te voor komen dat nieuwe sessies verbinding maken met de hosts. De taak meldt vervolgens alle momenteel aangemelde gebruikers om hun werk op te slaan, de geconfigureerde tijds duur te wachten en de gebruikers vervolgens af te dwingen om zich af te melden. Zodra alle gebruikers sessies op de sessiehost van de host-VM zijn afgemeld, wordt de VM door de taak afgesloten. Nadat de VM is uitgeschakeld, wordt de afsluit modus voor de sessie-host opnieuw ingesteld door de taak.

>[!NOTE]
>Als u de host-VM van de sessie hand matig instelt op de verwerkings modus, wordt de host-VM van de sessie niet beheerd door de taak. Als de host-VM van de sessie wordt uitgevoerd en is ingesteld op de modus voor afvoer, wordt deze beschouwd als niet-beschikbaar, waardoor de taak extra Vm's start om de belasting te verwerken. We raden u aan om virtuele Azure-machines te labelen voordat u deze hand matig instelt op de afvoer modus. U kunt de tag een naam met de para meter *MaintenanceTagName* wanneer u later een scheduler voor Azure Logic app maakt. Met tags kunt u deze Vm's onderscheiden van de virtuele machines die door het hulp programma voor schalen worden beheerd. Als u de onderhouds code instelt, voor komt u ook dat het hulp programma voor schalen wijzigingen in de virtuele machine aanbrengt totdat u de tag verwijdert.

Als u de para meter *LimitSecondsToForceLogOffUser* op nul instelt, kan de instelling van de sessie configuratie in opgegeven groeps beleid de gebruikers sessies afmelden afhandelen. Als u deze groeps beleidsregels wilt zien, gaat u naar **computer configuratie**  >  **beleid**  >  **Beheersjablonen**  >  **Windows-onderdelen**  >  **extern bureaublad-services**  >  **extern bureaublad sessie**  >  **duur sessie tijds limieten**. Als er actieve sessies op een host-VM zijn, wordt de VM van de host in de sessie uitgevoerd. Als er geen actieve sessies zijn, wordt de host-VM van de sessie afgesloten met de taak.

Tijdens een periode neemt de taak ook *MaxSessionLimit* in rekening om te bepalen of het huidige aantal sessies groter is dan 90% van de maximale capaciteit. Als dat het geval is, start de taak meer Vm's voor de host van sessies.

De taak wordt regel matig uitgevoerd op basis van een ingesteld interval van het terugkeer patroon. U kunt dit interval wijzigen op basis van de grootte van uw virtuele bureau blad-omgeving van Windows, maar houd er rekening mee dat het starten en afsluiten van Vm's kan enige tijd duren, dus vergeet niet om de vertraging te onthouden. We raden u aan het interval voor het terugkeer patroon in te stellen op elke 15 minuten.

Het hulp programma heeft echter ook de volgende beperkingen:

- Deze oplossing geldt alleen voor gegroepeerde Vm's met meerdere sessies.
- Deze oplossing beheert Vm's in een wille keurige regio, maar kan alleen worden gebruikt in hetzelfde abonnement als uw Azure Automation-account en Azure Logic-app.
- De maximale runtime van een taak in het runbook is 3 uur. Als het starten of stoppen van de virtuele machines in de hostgroep langer duurt dan dat, mislukt de taak. Zie [gedeelde bronnen](../../automation/automation-runbook-execution.md#fair-share)voor meer informatie.

>[!NOTE]
>Het hulp programma voor schalen bepaalt de taakverdelings modus van de hostgroep die momenteel wordt geschaald. Het hulp programma maakt gebruik van de modus breedte-eerste taak verdeling voor zowel de piek als de piek uren.

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het instellen van het hulp programma voor schalen, moet u de volgende dingen doen:

- Een [Windows-Tenant en-hostgroep voor virtueel bureau blad](create-host-pools-arm-template.md)
- Vm's van de hostgroep zijn geconfigureerd en geregistreerd bij de virtuele bureau blad-service van Windows
- Een gebruiker met [toegang tot Inzender](../../role-based-access-control/role-assignments-portal.md) op Azure-abonnement

De computer die u gebruikt om het hulp programma te implementeren, moet beschikken over:

- Windows Power shell 5,1 of hoger
- De micro soft AZ Power shell-module

Als u alles klaar hebt, kunt u aan de slag.

## <a name="create-or-update-an-azure-automation-account"></a>Een Azure Automation-account maken of bijwerken

>[!NOTE]
>Als u al een Azure Automation account hebt met een runbook waarop een oudere versie van het schaal script wordt uitgevoerd, hoeft u alleen de onderstaande instructies te volgen om te controleren of deze is bijgewerkt.

Eerst hebt u een Azure Automation-account nodig om het Power shell-runbook uit te voeren. Het proces dat wordt beschreven in deze sectie is geldig, zelfs als u een bestaand Azure Automation account hebt dat u wilt gebruiken om het Power shell-runbook in te stellen. U kunt Windows Hello als volgt instellen:

1. Open Windows PowerShell.

2. Voer de volgende cmdlet uit om u aan te melden bij uw Azure-account.

    ```powershell
    Login-AzAccount
    ```

    >[!NOTE]
    >Uw account moet bijdrage rechten hebben op het Azure-abonnement waar u het hulp programma voor schalen wilt implementeren.

3. Voer de volgende cmdlet uit om het script te downloaden voor het maken van het Azure Automation-account:

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzAutoAccount.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzAutoAccount.ps1"
    ```

4. Voer de volgende cmdlet uit om het script uit te voeren en het Azure Automation-account te maken. U kunt waarden voor de para meters invullen of ze een opmerking geven om de standaard instellingen te gebruiken.

    ```powershell
    $Params = @{
         "AADTenantId"           = "<Azure_Active_Directory_tenant_ID>"   # Optional. If not specified, it will use the current Azure context
         "SubscriptionId"        = "<Azure_subscription_ID>"              # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"     = "<Resource_group_name>"                # Optional. Default: "WVDAutoScaleResourceGroup"
         "AutomationAccountName" = "<Automation_account_name>"            # Optional. Default: "WVDAutoScaleAutomationAccount"
         "Location"              = "<Azure_region_for_deployment>"
         "WorkspaceName"         = "<Log_analytics_workspace_name>"       # Optional. If specified, Log Analytics will be used to configure the custom log table that the runbook PowerShell script can send logs to
    }

    .\CreateOrUpdateAzAutoAccount.ps1 @Params
    ```

5. De uitvoer van de cmdlet bevat een webhook-URI. Zorg ervoor dat u een record van de URI bewaart, omdat u deze gebruikt als para meter bij het instellen van het uitvoerings schema voor de Azure Logic-app.

6. Als u de parameter naam **ruimte** voor log Analytics hebt opgegeven, bevat de uitvoer van de cmdlet ook de log Analytics werk ruimte-id en de primaire sleutel. Zorg ervoor dat de URI wordt onthouden omdat u deze later opnieuw moet gebruiken als para meter bij het instellen van het uitvoerings schema voor de Azure Logic-app.

7. Nadat u uw Azure Automation-account hebt ingesteld, meldt u zich aan bij uw Azure-abonnement en controleert u of uw Azure Automation-account en het relevante runbook zijn weer gegeven in de opgegeven resource groep, zoals in de volgende afbeelding:

    >[!div class="mx-imgBorder"]
    >![Een afbeelding van de pagina overzicht van Azure met het nieuw gemaakte Azure Automation account en runbook.](media/automation-account.png)

    Als u wilt controleren of de webhook het moet zijn, selecteert u de naam van uw runbook. Ga vervolgens naar het gedeelte resources van het runbook en selecteer **webhooks**.

## <a name="create-an-azure-automation-run-as-account"></a>Een Azure Automation uitvoeren als-account maken

Nu u een Azure Automation account hebt, moet u ook een Azure Automation uitvoeren als-account maken als u er nog geen hebt. Met dit account krijgt u toegang tot uw Azure-resources.

Een [Azure Automation uitvoeren als-account](../../automation/manage-runas-account.md) biedt verificatie voor het beheren van resources in azure met Azure-cmdlets. Wanneer u een uitvoeren als-account maakt, wordt er in Azure Active Directory een nieuwe Service-Principal-gebruiker gemaakt en wordt de rol Inzender toegewezen aan de gebruiker van de Service-Principal op het abonnements niveau. Een uitvoeren als-account van Azure is een uitstekende manier om veilig te verifiëren met certificaten en een Service Principal Name zonder dat u een gebruikers naam en wacht woord hoeft op te slaan in een referentie object. Zie machtigingen voor het [uitvoeren als](../../automation/manage-runas-account.md#limit-run-as-account-permissions)-account beperken voor meer informatie over run as-account authenticatie.

Elke gebruiker die lid is van de rol abonnements beheerders en mede beheerder van het abonnement kan een uitvoeren als-account maken.

Een uitvoeren als-account maken in uw Azure Automation-account:

1. Selecteer in de Azure-portal de optie **Alle services**. In de lijst met resources voert u **Automation-accounts** in en selecteert u deze.

2. Selecteer op de pagina **Automation-accounts** de naam van uw Azure Automation-account.

3. Selecteer in het deel venster aan de linkerkant van het venster **uitvoeren als-accounts** in de sectie **account instellingen** .

4. Selecteer een **uitvoeren als-account voor Azure**. Wanneer het deel venster **uitvoeren als-account voor Azure toevoegen** wordt weer gegeven, controleert u de overzichts informatie en selecteert u vervolgens **maken** om het proces voor het maken van het account te starten.

5. Wacht enkele minuten totdat het run as-account is gemaakt in Azure. U kunt de voortgang van het maken volgen in het menu onder meldingen.

6. Wanneer het proces is voltooid, wordt in het opgegeven Azure Automation-account een Asset met de naam **AzureRunAsConnection** gemaakt. Selecteer een **uitvoeren als-account voor Azure**. Het verbindings element bevat de toepassings-ID, Tenant-ID, abonnements-ID en vinger afdruk van het certificaat. Onthoud de toepassings-ID omdat u deze later gebruikt. U kunt ook dezelfde informatie vinden op de pagina **verbindingen** . Als u naar deze pagina wilt gaan, selecteert u in het deel venster aan de linkerkant van het venster **verbindingen** onder de sectie **gedeelde bronnen** en klikt u op het verbindings element met de naam **AzureRunAsConnection**.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Een roltoewijzing maken in Windows Virtual Desktop

Vervolgens moet u een roltoewijzing maken zodat **AzureRunAsConnection** kan communiceren met het virtuele bureau blad van Windows. Zorg ervoor dat u Power shell gebruikt om u aan te melden met een account dat is gemachtigd om roltoewijzingen te maken.

Down load en Importeer eerst de [Windows Virtual Desktop Power shell-module](/powershell/windows-virtual-desktop/overview/) voor gebruik in uw Power shell-sessie als u dat nog niet hebt gedaan. Voer de volgende PowerShell-cmdlets uit om verbinding te maken met Windows Virtual Desktop en uw tenants weer te geven.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

# If your tenant is not in "Default Tenant Group", uncomment the following line and specify the name of your tenant group
# Set-RdsContext -TenantGroupName "<Tenant_Group_Name>"

Get-RdsTenant
```

Wanneer u de Tenant met de hostgroepen die u wilt schalen vindt, volgt u de instructies in [een Azure Automation uitvoeren als-account maken](#create-an-azure-automation-run-as-account) om de toepassings-id van de **AzureRunAsConnection** te verzamelen en de naam van het Windows-Tenant voor virtueel bureau blad te gebruiken die u hebt ontvangen van de vorige cmdlet in de volgende cmdlet om de roltoewijzing te maken:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId "<applicationid>" -TenantName "<tenantname>"
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>De Azure Logic-app en het uitvoerings schema maken

Ten slotte moet u de Azure Logic-app maken en een uitvoerings schema instellen voor het nieuwe hulp programma voor schalen.

1. Open Windows PowerShell.

2. Voer de volgende cmdlet uit om u aan te melden bij uw Azure-account.

    ```powershell
    Login-AzAccount
    ```

3. Voer de volgende cmdlet uit om het script voor het maken van de Azure Logic-app te downloaden.

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzLogicApp.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzLogicApp.ps1"
    ```

4. Voer de volgende cmdlet uit om u aan te melden bij Windows Virtual Desktop met een account met de machtigingen RDS owner of RDS contributor.

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

    # If your tenant is not in "Default Tenant Group", uncomment the following line and specify the name of your tenant group
    # Set-RdsContext -TenantGroupName "<Tenant_Group_Name>"
    ```

5. Voer het volgende Power shell-script uit om de Azure Logic-app en het uitvoerings schema voor uw hostgroep te maken.

    >[!NOTE]
    >U moet dit script uitvoeren voor elke hostgroep die u automatisch wilt schalen, maar u hebt slechts één Azure Automation-account nodig.

    ```powershell
    $AADTenantId = (Get-AzContext).Tenant.Id

    $AzSubscription = Get-AzSubscription | Out-GridView -OutputMode:Single -Title "Select your Azure Subscription"
    Select-AzSubscription -Subscription $AzSubscription.Id

    $ResourceGroup = Get-AzResourceGroup | Out-GridView -OutputMode:Single -Title "Select the resource group for the new Azure Logic App"

    $RDBrokerURL = (Get-RdsContext).DeploymentUrl
    $WVDTenant = Get-RdsTenant | Out-GridView -OutputMode:Single -Title "Select your WVD tenant"
    $WVDHostPool = Get-RdsHostPool -TenantName $WVDTenant.TenantName | Out-GridView -OutputMode:Single -Title "Select the host pool you'd like to scale"

    $LogAnalyticsWorkspaceId = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Workspace ID returned by when you created the Azure Automation account, otherwise leave it blank"
    $LogAnalyticsPrimaryKey = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Primary Key returned by when you created the Azure Automation account, otherwise leave it blank"
    $RecurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
    $BeginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
    $EndPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
    $TimeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
    $SessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
    $MinimumNumberOfRDSH = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
    $MaintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"
    $LimitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, any session host VM that has user sessions, will be left untouched"
    $LogOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
    $LogOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

    $AutoAccount = Get-AzAutomationAccount | Out-GridView -OutputMode:Single -Title "Select the Azure Automation account"
    $AutoAccountConnection = Get-AzAutomationConnection -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName | Out-GridView -OutputMode:Single -Title "Select the Azure RunAs connection asset"

    $WebhookURIAutoVar = Get-AzAutomationVariable -Name 'WebhookURI' -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName

    $Params = @{
         "AADTenantId"                   = $AADTenantId                             # Optional. If not specified, it will use the current Azure context
         "SubscriptionID"                = $AzSubscription.Id                       # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"             = $ResourceGroup.ResourceGroupName         # Optional. Default: "WVDAutoScaleResourceGroup"
         "Location"                      = $ResourceGroup.Location                  # Optional. Default: "West US2"
         "RDBrokerURL"                   = $RDBrokerURL                             # Optional. Default: "https://rdbroker.wvd.microsoft.com"
         "TenantGroupName"               = $WVDTenant.TenantGroupName               # Optional. Default: "Default Tenant Group"
         "TenantName"                    = $WVDTenant.TenantName
         "HostPoolName"                  = $WVDHostPool.HostPoolName
         "LogAnalyticsWorkspaceId"       = $LogAnalyticsWorkspaceId                 # Optional. If not specified, script will not log to the Log Analytics
         "LogAnalyticsPrimaryKey"        = $LogAnalyticsPrimaryKey                  # Optional. If not specified, script will not log to the Log Analytics
         "ConnectionAssetName"           = $AutoAccountConnection.Name              # Optional. Default: "AzureRunAsConnection"
         "RecurrenceInterval"            = $RecurrenceInterval                      # Optional. Default: 15
         "BeginPeakTime"                 = $BeginPeakTime                           # Optional. Default: "09:00"
         "EndPeakTime"                   = $EndPeakTime                             # Optional. Default: "17:00"
         "TimeDifference"                = $TimeDifference                          # Optional. Default: "-7:00"
         "SessionThresholdPerCPU"        = $SessionThresholdPerCPU                  # Optional. Default: 1
         "MinimumNumberOfRDSH"           = $MinimumNumberOfRDSH                     # Optional. Default: 1
         "MaintenanceTagName"            = $MaintenanceTagName                      # Optional.
         "LimitSecondsToForceLogOffUser" = $LimitSecondsToForceLogOffUser           # Optional. Default: 1
         "LogOffMessageTitle"            = $LogOffMessageTitle                      # Optional. Default: "Machine is about to shutdown."
         "LogOffMessageBody"             = $LogOffMessageBody                       # Optional. Default: "Your session will be logged off. Please save and close everything."
         "WebhookURI"                    = $WebhookURIAutoVar.Value
    }

    .\CreateOrUpdateAzLogicApp.ps1 @Params
    ```

    Nadat u het script hebt uitgevoerd, moet de Azure Logic app worden weer gegeven in een resource groep, zoals wordt weer gegeven in de volgende afbeelding.

    >[!div class="mx-imgBorder"]
    >![Een afbeelding van de overzichts pagina voor een voor beeld van een Azure Logic-app.](../media/logic-app.png)

    Als u wijzigingen wilt aanbrengen in het uitvoerings schema, zoals het wijzigen van het terugkeer-interval of de tijd zone, gaat u naar de scheduler Azure Logic app automatisch schalen en selecteert u **bewerken** om naar de Azure Logic app Designer te gaan.

    >[!div class="mx-imgBorder"]
    >![Een afbeelding van de ontwerp functie voor Azure Logic app. De herhalings-en webhook-menu's waarmee de gebruiker terugkeer tijden en het webhookbestand kunnen bewerken.](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Uw schaal programma beheren

Nu u het hulp programma voor schalen hebt gemaakt, hebt u toegang tot de uitvoer. In deze sectie worden enkele functies beschreven die u mogelijk nuttig vindt.

### <a name="view-job-status"></a>Taakstatus weergeven

U kunt een samenvattings status van alle runbook-taken weer geven of een uitgebreide status van een bepaalde runbook-taak weer geven in de Azure Portal.

Aan de rechter kant van uw geselecteerde Azure Automation-account, onder ' taak statistieken ', kunt u een lijst met samen vattingen van alle runbook-taken weer geven. Als u de pagina **taken** aan de linkerkant van het venster opent, worden de huidige statussen van de taak, start tijden en voltooiings tijden weer gegeven.

>[!div class="mx-imgBorder"]
>![Een scherm opname van de pagina taak status.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Uitvoer van Logboeken en hulpprogram ma's voor schalen weer geven

U kunt de logboeken van scale-out-en schaal bewerkingen bekijken door uw runbook te openen en de taak te selecteren.

Navigeer naar het runbook in de resource groep die als host fungeert voor het Azure Automation-account en selecteer **overzicht**. Selecteer op de pagina overzicht een taak onder **recente taken** om de uitvoer van het hulp programma weer te geven, zoals wordt weer gegeven in de volgende afbeelding.

>[!div class="mx-imgBorder"]
>![Een afbeelding van het uitvoer venster voor het hulp programma voor schalen.](media/tool-output.png)

### <a name="check-the-runbook-script-version-number"></a>Het versie nummer van het runbook-script controleren

U kunt controleren welke versie van het runbook-script u gebruikt door het runbook-bestand te openen in uw Azure Automation-account en **weer gave** te selecteren. Er wordt een script voor het runbook weer gegeven aan de rechter kant van het scherm. In het script ziet u het versie nummer in de indeling `v#.#.#` onder de `SYNOPSIS` sectie. U kunt [hier](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/wvd-scaling-script/basicScale.ps1#L1)het meest recente versie nummer vinden. Als u geen versie nummer in uw runbook-script ziet, betekent dit dat u een eerdere versie van het script gebruikt en dit meteen moet bijwerken. Als u uw runbook-script moet bijwerken, volgt u de instructies in [een Azure Automation-account maken of bijwerken](#create-or-update-an-azure-automation-account).

### <a name="reporting-issues"></a>Rapportage problemen

Wanneer u een probleem meldt, moet u de volgende informatie opgeven om problemen op te lossen:

- Een volledig logboek van het tabblad **alle logboeken** in de taak die het probleem heeft veroorzaakt. Volg de instructies in [Logboeken en de uitvoer van het hulp programma voor schalen](#view-logs-and-scaling-tool-output)voor meer informatie over het ophalen van het logboek. Als er sprake is van gevoelige of persoonlijke informatie in het logboek, kunt u deze verwijderen voordat u het probleem naar ons verzendt.

- De versie van het runbook-script dat u gebruikt. Zie [het versie nummer van het runbook-script controleren](#check-the-runbook-script-version-number) voor meer informatie over het ophalen van het versie nummer.

- Het versie nummer van elk van de volgende Power shell-modules die zijn geïnstalleerd in uw Azure Automation-account. U kunt deze modules vinden door Azure Automation account te openen, **modules** te selecteren onder de sectie **gedeelde resources** in het deel venster aan de linkerkant van het venster en vervolgens te zoeken naar de naam van de module.
    - Az.Accounts
    - Az.Compute
    - Az.Resources
    - Az.Automation
    - OMSIngestionAPI
    - Micro soft. RDInfra. RDPowershell

- De verval datum voor het [Run as-account](#create-an-azure-automation-run-as-account). Als u dit wilt vinden, opent u uw Azure Automation-account en selecteert u **uitvoeren als-accounts** onder **account instellingen** in het deel venster aan de linkerkant van het venster. De verval datum moet onder het **Azure uitvoeren als-account** vallen.

### <a name="log-analytics"></a>Log Analytics

Als u hebt besloten om Log Analytics te gebruiken, kunt u in de **Logboeken** van uw log Analytics-werk ruimte alle logboek gegevens weer geven in een aangepast logboek met de naam **WVDTenantScale_CL** onder **aangepaste logboeken** . We hebben een aantal voorbeeld query's weer gegeven die u wellicht nuttig vindt.

- Als u alle logboeken voor een hostgroep wilt weer geven, voert u de volgende query in

    ```Kusto
    WVDTenantScale_CL
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Als u het totale aantal actieve host-Vm's en actieve gebruikers sessies in uw hostgroep wilt weer geven, voert u de volgende query in

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Number of running session hosts:"
         or logmessage_s contains "Number of user sessions:"
         or logmessage_s contains "Number of user sessions per Core:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Als u de status van alle host-Vm's in een hostgroep wilt weer geven, voert u de volgende query in

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Session host:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Voer de volgende query in om eventuele fouten en waarschuwingen weer te geven

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "ERROR:" or logmessage_s contains "WARN:"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

## <a name="report-issues"></a>Problemen melden

Probleem rapporten voor het hulp programma voor schalen worden momenteel verwerkt door Microsoft Ondersteuning. Wanneer u een probleem rapport maakt, moet u ervoor zorgen dat u de instructies in [rapportage problemen](#reporting-issues)volgt. Als u feedback hebt over het hulp programma of als u nieuwe functies wilt aanvragen, opent u een GitHub-probleem met de naam ' 4-WVD-scaling-tool ' op de [pagina RDS github](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4-WVD-scaling-tool).
