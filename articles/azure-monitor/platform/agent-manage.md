---
title: De Azure Log Analytics-agent beheren
description: In dit artikel worden de verschillende beheer taken beschreven die u normaal gesp roken uitvoert tijdens de levens cyclus van de Log Analytics Windows-of Linux-agent die op een computer is geïmplementeerd.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/14/2019
ms.openlocfilehash: 15d01a8ac21264bf57ee7e65147e3989cd631b15
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99095553"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>De Log Analytics-agent voor Windows en Linux beheren en onderhouden

Na de eerste implementatie van de Log Analytics Windows-of Linux-agent in Azure Monitor moet u de agent mogelijk opnieuw configureren, bijwerken of verwijderen van de computer als deze de pensionering in de levens cyclus heeft bereikt. U kunt deze routine onderhouds taken eenvoudig hand matig of via Automation beheren, waardoor zowel de operationele fout als de onkosten worden verminderd.

## <a name="upgrading-agent"></a>Agent bijwerken

De Log Analytics-agent voor Windows en Linux kan hand matig worden bijgewerkt naar de meest recente versie of automatisch afhankelijk van het implementatie scenario en de omgeving waarin de virtuele machine wordt uitgevoerd. De volgende methoden kunnen worden gebruikt om de agent bij te werken.

| Omgeving | Installatie methode | Upgrademethode |
|--------|----------|-------------|
| Azure VM | VM-extensie Log Analytics agent voor Windows/Linux | De agent wordt standaard automatisch geüpgraded tenzij u uw Azure Resource Manager-sjabloon hebt geconfigureerd om u af te melden door de eigenschap *autoUpgradeMinorVersion* in te stellen op **False**. |
| Aangepaste installatie kopieën van Azure VM | Hand matige installatie van Log Analytics agent voor Windows/Linux | Het bijwerken van Vm's naar de nieuwste versie van de agent moet worden uitgevoerd vanaf de opdracht regel met het Windows Installer-pakket of Linux self-extracting en installeer bare shell script bundel.|
| Niet-Azure Vm's | Hand matige installatie van Log Analytics agent voor Windows/Linux | Het bijwerken van Vm's naar de nieuwste versie van de agent moet worden uitgevoerd vanaf de opdracht regel met het Windows Installer-pakket of Linux self-extracting en installeer bare shell script bundel. |

### <a name="upgrade-windows-agent"></a>Windows-agent bijwerken 

Als u de agent op een virtuele Windows-machine wilt bijwerken naar de nieuwste versie die niet is geïnstalleerd met behulp van de Log Analytics VM-extensie, voert u uit vanaf de opdracht prompt, het script of andere Automation-oplossing of met de \<platform\> installatie wizard MMASetup-. msi.  

U kunt de nieuwste versie van de Windows-agent downloaden van uw Log Analytics-werk ruimte door de volgende stappen uit te voeren.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Klik in Azure Portal op **Alle services**. Typ in de lijst met resources **Log Analytics**. Als u begint te typen, wordt de lijst gefilterd op basis van uw invoer. Selecteer **Log Analytics-werkruimten**.

3. Selecteer de werk ruimte in de lijst met Log Analytics-werk ruimten.

4. Selecteer in de werk ruimte Log Analytics **Geavanceerde instellingen**, selecteer **verbonden bronnen** en tenslotte Windows- **servers**.

5. Selecteer op de pagina **Windows-servers** de juiste versie van de **Windows-agent** die u wilt downloaden, afhankelijk van de processor architectuur van het Windows-besturings systeem.

>[!NOTE]
>Tijdens de upgrade van de Log Analytics-agent voor Windows biedt het geen ondersteuning voor het configureren of opnieuw configureren van een werk ruimte om aan te melden. Als u de agent wilt configureren, moet u een van de ondersteunde methoden volgen die worden vermeld onder [een werk ruimte toevoegen of verwijderen](#adding-or-removing-a-workspace).
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Bijwerken met behulp van de installatie wizard

1. Meld u aan bij de computer met een account met beheerders rechten.

2. Voer **MMASetup- \<platform\> . exe** uit om de installatie wizard te starten.

3. Klik op de eerste pagina van de installatie wizard op **volgende**.

4. Klik in het dialoog venster **Setup van micro soft Monitoring Agent** op **Ik ga akkoord** om de gebruiksrecht overeenkomst te accepteren.

5. Klik in het dialoogvenster **Setup van Microsoft Monitoring Agent** op **Bijwerken**. Op de statuspagina wordt de voortgang van de upgrade weergegeven.

6. Wanneer de **configuratie van micro soft monitoring agent is voltooid.** pagina wordt weer gegeven, klikt u op **volt ooien**.

#### <a name="to-upgrade-from-the-command-line"></a>Een upgrade uitvoeren vanaf de opdracht regel

1. Meld u aan bij de computer met een account met beheerders rechten.

2. Als u de installatie bestanden van de agent wilt extra heren, vanaf een opdracht prompt met verhoogde bevoegdheid, `MMASetup-<platform>.exe /c` wordt u gevraagd om het pad naar de bestanden uit te pakken. U kunt ook het pad opgeven door de argumenten door te geven `MMASetup-<platform>.exe /c /t:<Full Path>` .

3. Voer de volgende opdracht uit, waarbij D:\ de locatie is van het upgradelogboekbestand.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Linux-agent bijwerken 

Upgrade van eerdere versies (>1.0.0-47) wordt ondersteund. Wanneer u de installatie uitvoert met de `--upgrade` opdracht, worden alle onderdelen van de agent bijgewerkt naar de meest recente versie.

Voer de volgende opdracht uit om de agent bij te werken.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Een werk ruimte toevoegen of verwijderen

### <a name="windows-agent"></a>Windows-agent
De stappen in deze sectie zijn nood zakelijk als u niet alleen de Windows-agent opnieuw wilt configureren om te rapporteren aan een andere werk ruimte of als u een werk ruimte wilt verwijderen uit de configuratie, maar ook wanneer u de agent wilt configureren om te rapporteren aan meer dan één werk ruimte (ook wel multi-multihoming genoemd). Het configureren van de Windows-agent voor rapportage aan meerdere werk ruimten kan alleen worden uitgevoerd na de initiële installatie van de agent en met de methoden die hieronder worden beschreven.    

#### <a name="update-settings-from-control-panel"></a>Instellingen bijwerken via configuratie scherm

1. Meld u aan bij de computer met een account met beheerders rechten.

2. Open het **Configuratiescherm**.

3. Selecteer **micro soft Monitoring Agent** en klik vervolgens op het tabblad **Azure log Analytics** .

4. Als u een werk ruimte verwijdert, selecteert u deze en klikt u vervolgens op **verwijderen**. Herhaal deze stap voor alle andere werk ruimten waarvan u wilt dat de agent stopt met rapporteren.

5. Als u een werk ruimte wilt toevoegen, klikt u op **toevoegen** en plakt u de werk ruimte-id en werkruimte sleutel (primaire sleutel) in het dialoog venster **een log Analytics werkruimte toevoegen** . Als de computer moet rapporteren aan een Log Analytics-werk ruimte in Azure Government Cloud, selecteert u Azure US Government in de vervolg keuzelijst van de Azure-Cloud.

6. Klik op **OK** om uw wijzigingen op te slaan.

#### <a name="remove-a-workspace-using-powershell"></a>Een werk ruimte verwijderen met Power shell

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Een werk ruimte in azure Commercial toevoegen met Power shell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Een werk ruimte in azure toevoegen voor de Amerikaanse overheid met Power shell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Als u de opdracht regel of het script eerder hebt gebruikt voor het installeren of configureren van de agent, `EnableAzureOperationalInsights` is vervangen door `AddCloudWorkspace` en `RemoveCloudWorkspace` .
>

### <a name="linux-agent"></a>Linux-agent
De volgende stappen laten zien hoe u de Linux-agent opnieuw kunt configureren als u besluit deze te registreren bij een andere werk ruimte of om een werk ruimte te verwijderen uit de configuratie.

1. Als u wilt controleren of het is geregistreerd bij een werk ruimte, voert u de volgende opdracht uit:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    De status moet er als volgt uitzien:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Het is belang rijk dat met de status ook de agent wordt uitgevoerd. anders worden de volgende stappen voor het opnieuw configureren van de agent niet voltooid.

2. Als deze al is geregistreerd bij een werk ruimte, verwijdert u de geregistreerde werk ruimte door de volgende opdracht uit te voeren. Anders gaat u verder met de volgende stap als deze niet is geregistreerd.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Voer de volgende opdracht uit om u te registreren bij een andere werk ruimte:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. Voer de volgende opdracht uit om te controleren of uw wijzigingen zijn doorgevoerd:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    De status moet er als volgt uitzien:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

De Agent service hoeft niet opnieuw te worden gestart om de wijzigingen van kracht te laten worden.

## <a name="update-proxy-settings"></a>Proxy-instellingen bijwerken
Gebruik een van de volgende methoden om deze taak te volt ooien om de agent te configureren om te communiceren met de service via een proxy server of [log Analytics gateway](gateway.md) na de implementatie.

### <a name="windows-agent"></a>Windows-agent

#### <a name="update-settings-using-control-panel"></a>Instellingen bijwerken via configuratie scherm

1. Meld u aan bij de computer met een account met beheerders rechten.

2. Open het **Configuratiescherm**.

3. Selecteer **micro soft Monitoring Agent** en klik vervolgens op het tabblad **proxy-instellingen** .

4. Klik op **Een proxyserver gebruiken** en geef de URL en het poortnummer van de proxyserver of gateway op. Als er voor uw proxyserver of Log Analytics-gateway verificatie is vereist, voert u de gebruikersnaam en het wachtwoord in voor verificatie en klikt u op **OK**.

#### <a name="update-settings-using-powershell"></a>Instellingen bijwerken met Power shell

Kopieer de volgende Power shell-voorbeeld code, werk deze bij met informatie die specifiek is voor uw omgeving en sla het bestand op met een PS1-bestands extensie. Voer het script uit op elke computer die rechtstreeks verbinding maakt met de Log Analytics-werk ruimte in Azure Monitor.

```powershell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object. We need to determine if we
#have the right update rollup with the API we need. If not, no need to run the rest of the script.
$healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

$proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

if (!$proxyMethod)
{
    Write-Output 'Health Service proxy API not present, will not update settings.'
    return
}

Write-Output "Clearing proxy settings."
$healthServiceSettings.SetProxyInfo('', '', '')

$ProxyUserName = $cred.username

Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
$healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)
```

### <a name="linux-agent"></a>Linux-agent
Voer de volgende stappen uit als uw Linux-computers moeten communiceren via een proxy server of Log Analytics gateway. De waarde voor de proxyconfiguratie heeft de volgende syntaxis `[protocol://][user:password@]proxyhost[:port]`. De eigenschap *proxyhost* accepteert een volledig gekwalificeerde domeinnaam of het IP-adres van de proxyserver.

1. Bewerk het bestand `/etc/opt/microsoft/omsagent/proxy.conf` door de volgende opdrachten uit te voeren en de waarden aan te passen aan uw specifieke instellingen.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf
    ```

2. Start de agent opnieuw door de volgende opdracht uit te voeren:

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ```

## <a name="uninstall-agent"></a>Agent verwijderen
Gebruik een van de volgende procedures om de Windows-of Linux-agent te verwijderen met behulp van de opdracht regel of de wizard Setup.

### <a name="windows-agent"></a>Windows-agent

#### <a name="uninstall-from-control-panel"></a>Verwijderen in het configuratie scherm
1. Meld u aan bij de computer met een account met beheerders rechten.

2. Klik in **het configuratie scherm** op **Program ma's en onderdelen**.

3. Klik in **Program ma's en onderdelen** op **micro soft Monitoring Agent**, klik op **verwijderen** en vervolgens op **Ja**.

>[!NOTE]
>U kunt de wizard Setup van agent ook uitvoeren door te dubbel klikken op **MMASetup- \<platform\> . exe**, dat kan worden gedownload via een werk ruimte in de Azure Portal.

#### <a name="uninstall-from-the-command-line"></a>Verwijderen vanaf de opdracht regel
Het gedownloade bestand voor de agent is een op zichzelf staand installatie pakket dat is gemaakt met IExpress. Het installatie programma voor de agent en de ondersteunende bestanden bevinden zich in het pakket en moet worden uitgepakt om correct te worden verwijderd met de opdracht regel die in het volgende voor beeld wordt weer gegeven.

1. Meld u aan bij de computer met een account met beheerders rechten.

2. Als u de installatie bestanden van de agent wilt extra heren, vanaf een opdracht prompt met verhoogde bevoegdheid, `extract MMASetup-<platform>.exe` wordt u gevraagd om het pad naar de bestanden uit te pakken. U kunt ook het pad opgeven door de argumenten door te geven `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>` . Zie voor meer informatie over de opdracht regel parameters die worden ondersteund door IExpress, [opdracht regel parameters voor IExpress](https://www.betaarchive.com/wiki/index.php?title=Microsoft_KB_Archive/197147) en werk vervolgens het voor beeld bij aan uw behoeften.

3. Typ bij de prompt `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb` .

### <a name="linux-agent"></a>Linux-agent
Voer de volgende opdracht op de Linux-computer uit om de agent te verwijderen. Met het argument *--purge* worden de agent en de configuratie ervan volledig verwijderd.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Agent configureren om te rapporteren aan een Operations Manager-beheer groep

### <a name="windows-agent"></a>Windows-agent
Voer de volgende stappen uit om de Log Analytics-agent voor Windows te configureren om te rapporteren aan een System Center Operations Manager-beheer groep.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Meld u aan bij de computer met een account met beheerders rechten.

2. Open het **Configuratiescherm**.

3. Klik op **micro soft Monitoring Agent** en klik vervolgens op het tabblad **Operations Manager** .

4. Als uw Operations Manager-servers integratie met Active Directory hebben, klikt u op **toewijzingen van beheer groepen automatisch bijwerken vanuit AD DS**.

5. Klik op **toevoegen** om het dialoog venster **een beheer groep toevoegen** te openen.

6. Typ in het veld **naam van beheer groep** de naam van uw beheer groep.

7. Typ in het veld **primaire beheer server** de computer naam van de primaire beheer server.

8. Typ in het veld **beheer server poort** het TCP-poort nummer.

9. Kies in het **actie-account** van de agent het lokale systeem account of een lokaal domein account.

10. Klik op **OK** om het dialoog venster **een beheer groep toevoegen** te sluiten en klik vervolgens op **OK** om het dialoog venster **Eigenschappen van micro soft Monitoring Agent** te sluiten.

### <a name="linux-agent"></a>Linux-agent
Voer de volgende stappen uit om de Log Analytics-agent voor Linux te configureren om te rapporteren aan een System Center Operations Manager-beheer groep.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Het bestand bewerken `/etc/opt/omi/conf/omiserver.conf`

2. Zorg ervoor dat de regel die begint met `httpsport=` de poort 1270 definieert. Zoals: `httpsport=1270`

3. Start de OMI-server opnieuw op: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg [problemen met de Linux-agent oplossen](agent-linux-troubleshoot.md) als u problemen ondervindt tijdens het installeren of beheren van de Linux-agent.

- Raadpleeg [problemen met de Windows-agent oplossen](agent-windows-troubleshoot.md) als u problemen ondervindt tijdens het installeren of beheren van de Windows-agent.
