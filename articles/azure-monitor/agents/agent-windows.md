---
title: Log Analytics-agent installeren op Windows-computers
description: In dit artikel wordt beschreven hoe u Windows-computers Azure Monitor die worden gehost in andere Clouds of on-premises verbindt met de Log Analytics-agent voor Windows.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/03/2020
ms.openlocfilehash: aec39b86f9651539028efce93ba6a88c3be75b0c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102038307"
---
# <a name="install-log-analytics-agent-on-windows-computers"></a>Log Analytics-agent installeren op Windows-computers
In dit artikel vindt u informatie over het installeren van de Log Analytics-agent op Windows-computers met behulp van de volgende methoden:

* Hand matige installatie met behulp van de [installatie wizard](#install-agent-using-setup-wizard) of de [opdracht regel](#install-agent-using-command-line).
* [Azure Automation desired state Configuration (DSC)](#install-agent-using-dsc-in-azure-automation). 

>[!IMPORTANT]
> De installatie methoden die in dit artikel worden beschreven, worden doorgaans gebruikt voor virtuele machines, on-premises of in andere Clouds. Zie [installatie opties](./log-analytics-agent.md#installation-options) voor efficiëntere opties die u kunt gebruiken voor virtuele machines van Azure.

> [!NOTE]
> Als u de agent moet configureren om te rapporteren aan meer dan één werk ruimte, kan deze niet worden uitgevoerd tijdens de eerste installatie, maar u kunt de instellingen ook bijwerken via configuratie scherm of Power shell, zoals beschreven in [een werk ruimte toevoegen of verwijderen](agent-manage.md#adding-or-removing-a-workspace).  

## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

Zie [overzicht van Azure monitor agents](agents-overview.md#supported-operating-systems) voor een lijst met Windows-versies die worden ondersteund door de log Analytics-agent.

### <a name="sha-2-code-signing-support-requirement"></a>SHA-2-ondersteuning voor ondertekening van code 
De Windows-agent gaat exclusief SHA-2-ondertekening gebruiken op 17 augustus 2020. Deze wijziging is van invloed op klanten die de Log Analytics-agent gebruiken op een verouderd besturings systeem als onderdeel van een Azure-service (Azure Monitor, Azure Automation, Azure Updatebeheer, Azure Wijzigingen bijhouden, Azure Security Center, Azure Sentinel, Windows Defender ATP). Voor de wijziging is geen actie van de klant vereist tenzij u de agent uitvoert op een verouderde versie van het besturings systeem (Windows 7, Windows Server 2008 R2 en Windows Server 2008). Klanten die gebruikmaken van een verouderde versie van het besturings systeem, moeten de volgende acties uitvoeren op hun computers vóór 17 augustus 2020 of hun agents stoppen met het verzenden van gegevens naar hun Log Analytics-werk ruimten:

1. Installeer het meest recente Service Pack voor uw besturings systeem. De vereiste Service Pack versies zijn:
    - Windows 7 SP1
    - Windows Server 2008 SP2
    - Windows Server 2008 R2 SP1

2. Installeer de Windows-updates voor SHA-2-ondertekening voor uw besturings systeem, zoals beschreven in [2019 SHA-2-ondersteuning voor ondertekening van code voor Windows en WSUS](https://support.microsoft.com/help/4472027/2019-sha-2-code-signing-support-requirement-for-windows-and-wsus)
3. Update naar de nieuwste versie van de Windows-agent (versie 10.20.18029).
4. Aanbevolen om de agent te configureren voor het [gebruik van TLS 1,2](agent-windows.md#configure-agent-to-use-tls-12). 

## <a name="network-requirements"></a>Netwerkvereisten
Zie [overzicht van log Analytics agent](./log-analytics-agent.md#network-requirements) voor de netwerk vereisten voor de Windows-agent.


   
## <a name="configure-agent-to-use-tls-12"></a>Agent configureren voor het gebruik van TLS 1,2
[TLS 1,2](/windows-server/security/tls/tls-registry-settings#tls-12) -protocol waarborgt de beveiliging van gegevens die onderweg zijn voor communicatie tussen de Windows-agent en de log Analytics service. Als u op een [besturings systeem installeert zonder dat tls 1,2 standaard is ingeschakeld](../logs/data-security.md#sending-data-securely-using-tls-12), moet u TLS 1,2 configureren aan de hand van de onderstaande stappen.

1. Zoek de volgende registersubsleutel: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Een subsleutel maken onder **protocollen** voor TLS 1,2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1,2**
3. Maak een subsleutel van de **client** onder de subsleutel TLS 1,2-Protocol versie die u eerder hebt gemaakt. Bijvoorbeeld **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ client**.
4. Maak de volgende DWORD-waarden onder **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ client**:

    * **Ingeschakeld** [waarde = 1]
    * **DisabledByDefault** [waarde = 0]  

Configureer .NET Framework 4,6 of hoger voor de ondersteuning van beveiligde crypto grafie, omdat deze standaard is uitgeschakeld. De [sterke crypto grafie](/dotnet/framework/network-programming/tls#schusestrongcrypto) maakt gebruik van veiliger netwerk protocollen zoals TLS 1,2 en blokkeert protocollen die niet beveiligd zijn. 

1. Zoek de volgende registersubsleutel: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\ . NETFramework\v4.0.30319**.  
2. Maak de DWORD-waarde **schusestrongcrypto toe** onder deze subsleutel met de waarde **1**.  
3. Zoek de volgende registersubsleutel: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\ . NETFramework\v4.0.30319**.  
4. Maak de DWORD-waarde **schusestrongcrypto toe** onder deze subsleutel met de waarde **1**. 
5. Start het systeem opnieuw op om de instellingen van kracht te laten worden. 

## <a name="install-agent-using-setup-wizard"></a>Agent installeren met de wizard Setup
Met de volgende stappen wordt de Log Analytics agent in Azure en Azure Government Cloud geïnstalleerd en geconfigureerd met behulp van de installatie wizard voor de agent op uw computer. Als u wilt weten hoe u de agent kunt configureren om ook te rapporteren aan een System Center Operations Manager-beheer groep, raadpleegt u [de Operations Manager-agent implementeren met de wizard Setup van agent](/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. Selecteer in uw Log Analytics-werk ruimte op de pagina **Windows-servers** waar u eerder naar hebt genavigeerd, de juiste versie van de **Windows-agent downloaden** om te downloaden, afhankelijk van de processor architectuur van het Windows-besturings systeem.   
2. Voer Setup uit om de agent op de computer te installeren.
2. Klik op de pagina **Welkom** op **Volgende**.
3. Lees de licentie op de pagina **Licentievoorwaarden** en klik op **Akkoord**.
4. Op de pagina **Doelmap** wijzigt u desgewenst de standaardinstallatiemap en klikt u op **Volgende**.
5. Op de pagina **Installatieopties voor agent** kiest u ervoor de agent verbinding te laten maken met Azure Log Analytics en klikt u op **Volgende**.   
6. Ga op de pagina **Azure Log Analytics** als volgt te werk:
   1. Plak de **Werkruimte-id** en **Werkruimtesleutel (primaire sleutel)** die u eerder hebt gekopieerd.  Als de computer moet rapporteren aan een Log Analytics-werk ruimte in Azure Government Cloud, selecteert u **Azure US Government** in de vervolg keuzelijst van de **Azure-Cloud** .  
   2. Als de computer met de Log Analytics-service moet communiceren via een proxyserver, klikt u op **Geavanceerd** en geeft u de URL en het poortnummer van de proxyserver op.  Als er voor uw proxyserver verificatie is vereist, voert u de gebruikersnaam en het wachtwoord in voor verificatie met de proxyserver. Klik vervolgens op **Volgende**.  
7. Klik op **Volgende** als u de vereiste configuratie-instellingen hebt voltooid.<br><br> ![Werkruimte-id en primaire sleutel plakken](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. Controleer op de pagina **Gereed om te installeren** uw keuzes en klik op **Installeren**.
9. Klik op de pagina **Configuratie voltooid** op **Voltooien**.

Als u klaar bent, wordt **micro soft Monitoring Agent** weer gegeven in het **configuratie scherm**. Als u wilt bevestigen dat het rapport wordt gerapporteerd aan Log Analytics, raadpleegt u de [agent connectiviteit controleren op Log Analytics](#verify-agent-connectivity-to-azure-monitor). 

## <a name="install-agent-using-command-line"></a>Agent installeren met behulp van de opdracht regel
Het gedownloade bestand voor de agent is een op zichzelf staand installatie pakket.  Het installatie programma voor de agent en de ondersteunende bestanden bevinden zich in het pakket en moet worden uitgepakt om correct te kunnen installeren met behulp van de opdracht regel die wordt weer gegeven in de volgende voor beelden.    

>[!NOTE]
>Als u een agent wilt bijwerken, moet u de Log Analytics Scripting API gebruiken. Zie het onderwerp [beheer en onderhoud van de log Analytics-agent voor Windows en Linux](agent-manage.md) voor meer informatie.

De volgende tabel bevat de specifieke para meters die worden ondersteund door de installatie voor de agent, waaronder wanneer geïmplementeerd met Automation DSC.

|MMA-specifieke opties                   |Notities         |
|---------------------------------------|--------------|
| NOAPM=1                               | Optionele parameter. Hiermee wordt de agent geïnstalleerd zonder bewaking van .NET-toepassings prestaties.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = de agent configureren om te rapporteren aan een werk ruimte                |
|OPINSIGHTS_WORKSPACE_ID                | Werk ruimte-ID (GUID) voor de toe te voegen werk ruimte                    |
|OPINSIGHTS_WORKSPACE_KEY               | Werkruimte sleutel die in eerste instantie wordt gebruikt voor verificatie met de werk ruimte |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | De cloud omgeving opgeven waarin de werk ruimte zich bevindt <br> 0 = Azure-commerciële Cloud (standaard) <br> 1 = Azure Government |
|OPINSIGHTS_PROXY_URL               | URI voor de te gebruiken proxy |
|OPINSIGHTS_PROXY_USERNAME               | Gebruikers naam voor toegang tot een geverifieerde proxy |
|OPINSIGHTS_PROXY_PASSWORD               | Wacht woord voor toegang tot een geverifieerde proxy |

1. Als u de installatie bestanden van de agent wilt extra heren, vanaf een opdracht prompt met verhoogde bevoegdheid, `MMASetup-<platform>.exe /c` wordt u gevraagd om het pad naar de bestanden uit te pakken.  U kunt ook het pad opgeven door de argumenten door te geven `MMASetup-<platform>.exe /c /t:<Full Path>` .  
2. Als u de agent op de achtergrond wilt installeren en configureren om te rapporteren aan een werk ruimte in de commerciële cloud van Azure, vanuit de map die u de installatie bestanden hebt uitgepakt, typt u: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   of om de agent te configureren om te rapporteren aan de Azure-Cloud voor de Amerikaanse overheid, typt u: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >De teken reeks waarden voor de para meters *OPINSIGHTS_WORKSPACE_ID* en *OPINSIGHTS_WORKSPACE_KEY* moeten worden ingekapseld in dubbele aanhalings tekens om te instrueren dat Windows Installer interprit als geldige opties voor het pakket. 

## <a name="install-agent-using-dsc-in-azure-automation"></a>Agent installeren met behulp van DSC in Azure Automation

U kunt het volgende script voorbeeld gebruiken om de agent te installeren met behulp van Azure Automation DSC.   Als u geen Automation-account hebt, raadpleegt u aan de [slag met Azure Automation](../../automation/index.yml) om inzicht te krijgen in de vereisten en stappen voor het maken van een Automation-account dat is vereist voor het gebruik van Automation DSC.  Als u niet bekend bent met Automation DSC, raadpleegt u [aan de slag met Automation DSC](../../automation/automation-dsc-getting-started.md).

In het volgende voor beeld wordt de 64-bits agent geïnstalleerd, geïdentificeerd door de `URI` waarde. U kunt ook de 32-bits versie gebruiken door de URI-waarde te vervangen. De Uri's voor beide versies zijn:

- Windows 64-bits agent- https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32-bits agent- https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Deze procedure en het script voorbeeld bieden geen ondersteuning voor de upgrade van de agent die al is geïmplementeerd op een Windows-computer.

De 32-bits en 64-bits versies van het agent pakket hebben verschillende product codes en nieuwe versies die zijn uitgebracht, hebben ook een unieke waarde.  De product code is een GUID die de principal-identificatie vormt van een toepassing of product en wordt vertegenwoordigd door de **eigenschap van** de Windows Installer.  De `ProductId` waarde in het **MMAgent.ps1** script moet overeenkomen met de product code van het 32-bits-of 64-bits agent-installatie pakket.

Als u de product code rechtstreeks van het agent-installatie pakket wilt ophalen, kunt u Orca.exe van de [Windows SDK-onderdelen gebruiken voor Windows Installer ontwikkel aars](/windows/win32/msi/platform-sdk-components-for-windows-installer-developers) die een onderdeel zijn van de Windows Software Development Kit of met behulp van Power shell volgens een [voorbeeld script](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/)  dat is geschreven door een micro soft Valuable Professional (MVP).  Voor beide benaderingen moet u eerst het **MOMagent.msi** bestand extra heren uit het installatie pakket MMASetup.  Dit wordt eerder weer gegeven in de eerste stap onder de sectie de [agent installeren met de opdracht regel](#install-agent-using-command-line).  

1. Importeer de xPSDesiredStateConfiguration DSC-module vanuit [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) naar Azure Automation.  
2.    Maak Azure Automation variabele assets voor *OPSINSIGHTS_WS_ID* en *OPSINSIGHTS_WS_KEY*. Stel *OPSINSIGHTS_WS_ID* in op uw log Analytics werk ruimte-id en stel *OPSINSIGHTS_WS_KEY* in op de primaire sleutel van uw werk ruimte.
3.    Kopieer het script en sla het op als MMAgent.ps1.

```powershell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

    Import-DscResource -ModuleName xPSDesiredStateConfiguration
    Import-DscResource -ModuleName PSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}

```

4. Werk de `ProductId` waarde in het script bij met de product code die is geëxtraheerd uit de nieuwste versie van het installatie pakket van de agent met behulp van de hierboven aanbevolen methoden. 
5. [Importeer het MMAgent.ps1 configuratie script](../../automation/automation-dsc-getting-started.md#import-a-configuration-into-azure-automation) in uw Automation-account. 
6. [Wijs een Windows-computer of-knoop punt](../../automation/automation-dsc-getting-started.md#enable-an-azure-resource-manager-vm-for-management-with-state-configuration) toe aan de configuratie. Binnen 15 minuten controleert het knoop punt de configuratie en wordt de agent naar het knoop punt gepusht.

## <a name="verify-agent-connectivity-to-azure-monitor"></a>Agent connectiviteit met Azure Monitor controleren

Nadat de installatie van de agent is voltooid, controleert u of deze is verbonden en de rapportage kan op twee manieren worden uitgevoerd.  

Ga in de computer naar **Configuratiescherm** en zoek het item **Microsoft Monitoring Agent**.  Selecteer de agent en op het tabblad **Azure log Analytics** een bericht met de melding: **micro soft monitoring agent is verbonden met de Microsoft Operations Management Suite-Service.**<br><br> ![Verbindingsstatus van MMA met Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

U kunt ook een eenvoudige logboek query uitvoeren in de Azure Portal.  

1. Zoek in het Azure Portal naar en selecteer **monitor**.
1. Selecteer **Logboeken** in het menu.
1. In het deel venster **Logboeken** in het query veld Type:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

In de zoek resultaten worden de heartbeat-records weer gegeven voor de computer die aangeeft dat deze is verbonden met en rapportage aan de service.

## <a name="cache-information"></a>Cache gegevens

Gegevens van de Log Analytics-agent worden in de cache opgeslagen op de lokale computer met de *status C:\Program Files\Microsoft monitoring Agent\Agent\Health service* voordat deze wordt verzonden naar Azure monitor. De agent probeert om de 20 seconden te uploaden. Als dit mislukt, wordt een exponentieel toenemende duur gewacht totdat deze slaagt. Het wacht 30 seconden vóór de tweede poging, 60 seconden vóór de volgende 120 seconden, enzovoort tot een maximum van 8,5 uur tussen de pogingen tot de verbinding tot stand is gebracht. Deze wacht tijd wordt enigszins wille keurig geaselect om te voor komen dat alle agents gelijktijdig verbinding proberen te kunnen. De oudste gegevens worden verwijderd wanneer de maximum buffer wordt bereikt.

De standaard cache grootte is 50 MB, maar kan worden geconfigureerd tussen mini maal 5 MB en Maxi maal 1,5 GB. Deze wordt opgeslagen in de register sleutel *HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Persistence Cache Maximum*. De waarde vertegenwoordigt het aantal pagina's, met 8 KB per pagina.


## <a name="next-steps"></a>Volgende stappen

- Raadpleeg [de log Analytics-agent voor Windows en Linux beheren en onderhouden voor](agent-manage.md) meer informatie over het opnieuw configureren, bijwerken of verwijderen van de agent van de virtuele machine.

- Raadpleeg [problemen met de Windows-agent oplossen](agent-windows-troubleshoot.md) als u problemen ondervindt tijdens het installeren of beheren van de agent.