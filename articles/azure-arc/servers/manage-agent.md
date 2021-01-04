---
title: De agent voor Azure Arc-servers beheren
description: In dit artikel worden de verschillende beheer taken beschreven die u normaal gesp roken uitvoert tijdens de levens cyclus van de computer agent die verbonden is met Azure Arc ingeschakeld.
ms.date: 12/21/2020
ms.topic: conceptual
ms.openlocfilehash: f408048f61f76d6b258ea8e063630b4e2aa841af
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97724371"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>De verbonden machine agent beheren en onderhouden

Na de eerste implementatie van de met Azure Arc ingeschakelde servers die zijn verbonden met de computer agent voor Windows of Linux, moet u de agent mogelijk opnieuw configureren, bijwerken of verwijderen van de computer. U kunt deze routine onderhouds taken eenvoudig hand matig of via Automation beheren, waardoor zowel de operationele fout als de onkosten worden verminderd.

## <a name="before-uninstalling-agent"></a>Voordat de agent wordt verwijderd

Houd rekening met het volgende voordat u de verbonden machine agent van uw met Arc ingeschakelde server verwijdert, om onverwachte problemen of kosten toe te voegen aan uw Azure-factuur:

* Als u Azure VM-extensies hebt geïmplementeerd op een ingeschakelde server en u de verbonden machine agent verwijdert of de resource verwijdert die de Arc-server in de resource groep vertegenwoordigt, blijven die uitbrei dingen actief en voeren ze de normale werking uit.

* Als u de bron verwijdert die de Arc-server in uw resource groep vertegenwoordigt, maar u de VM-extensies niet verwijdert, kunt u de geïnstalleerde VM-extensies niet meer beheren wanneer u de computer opnieuw registreert.

Voor servers of machines die u niet meer wilt beheren met Azure Arc-servers, moet u deze stappen volgen om het beheer te stoppen:

1. Verwijder de VM-extensies van de computer of server. De volgende stappen worden hieronder beschreven.

2. Koppel de computer met een van de volgende methoden los van Azure Arc:

    * `azcmagent disconnect`Opdracht uitvoeren op de computer of server.

    * Van de geselecteerde geregistreerde Arc-server in de Azure Portal door **verwijderen** te selecteren in de bovenste balk.

    * De [Azure cli](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-cli#delete-resource) of [Azure PowerShell](../../azure-resource-manager/management/delete-resource-group.md?tabs=azure-powershell#delete-resource)gebruiken. Voor het `ResourceType` gebruik van de para meter `Microsoft.HybridCompute/machines` .

3. Verwijder de agent van de computer of server. Volg de onderstaande stappen.

## <a name="upgrading-agent"></a>Agent bijwerken

De agent van de Azure Connected machine wordt regel matig bijgewerkt om problemen met oplossingen, stabiliteits verbeteringen en nieuwe functionaliteit aan te pakken. [Azure Advisor](../../advisor/advisor-overview.md) identificeert resources die niet gebruikmaken van de nieuwste versie van de machine agent en raadt u aan om te upgraden naar de nieuwste versie. U ontvangt een melding wanneer u de met Arc ingeschakelde server selecteert door een banner op de pagina **overzicht** te presen teren of door toegang te krijgen tot Advisor via de Azure Portal.

De Azure Connected machine-agent voor Windows en Linux kan hand matig worden bijgewerkt naar de meest recente versie of automatisch afhankelijk van uw vereisten.

In de volgende tabel worden de methoden beschreven die worden ondersteund om de upgrade van de agent uit te voeren.

| Besturingssysteem | Upgrademethode |
|------------------|----------------|
| Windows | Handmatig<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [Zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| Red Hat Enter prise, Amazon, CentOS linux | [yum](https://wiki.centos.org/PackageManagement/Yum) |

### <a name="windows-agent"></a>Windows-agent

Update pakket voor de verbonden machine agent voor Windows is beschikbaar vanaf:

* Microsoft Update

* [Microsoft Update catalogus](https://www.catalog.update.microsoft.com/Home.aspx)

* [Windows agent Windows Installer-pakket](https://aka.ms/AzureConnectedMachineAgent) van het micro soft Download centrum.

De agent kan worden geüpgraded na diverse methoden ter ondersteuning van het software-update beheer proces. U kunt zich buiten Microsoft Update downloaden en uitvoeren vanaf de opdracht prompt, vanuit een script of andere oplossing voor automatisering of vanuit de wizard gebruikers interface door uit te voeren `AzureConnectedMachine.msi` .

> [!NOTE]
> * Als u de agent wilt bijwerken, moet u over *beheerders* machtigingen beschikken.
> * Als u de upgrade hand matig wilt uitvoeren, moet u eerst het installatie pakket downloaden en kopiëren naar een map op de doel server of vanuit een gedeelde netwerkmap. 

Als u niet bekend bent met de opdracht regel opties voor Windows Installer-pakketten, raadpleegt u [Msiexec Standard-opdracht regel opties](/windows/win32/msi/standard-installer-command-line-options) en [Msiexec-opdracht regel opties](/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Bijwerken met behulp van de installatie wizard

1. Meld u aan bij de computer met een account met beheerders rechten.

2. Voer **AzureConnectedMachineAgent.msi** uit om de installatie wizard te starten.

De wizard Setup detecteert of een vorige versie bestaat en voert vervolgens automatisch een upgrade van de agent uit. Wanneer de upgrade is voltooid, wordt de wizard Setup automatisch gesloten.

#### <a name="to-upgrade-from-the-command-line"></a>Een upgrade uitvoeren vanaf de opdracht regel

1. Meld u aan bij de computer met een account met beheerders rechten.

2. Voer de volgende opdracht uit om de agent op de achtergrond bij te werken en een installatie logboek bestand te maken in de `C:\Support\Logs` map.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux-agent

Als u de agent op een Linux-computer wilt bijwerken naar de nieuwste versie, zijn er twee opdrachten nodig. Eén opdracht voor het bijwerken van de lokale pakket index met de lijst met meest recente beschik bare pakketten van de opslag plaatsen en één opdracht om het lokale pakket bij te werken.

U kunt het meest recente agent pakket downloaden van de [pakket opslagplaats](https://packages.microsoft.com/)van micro soft.

> [!NOTE]
> Als u de agent wilt bijwerken, moet u toegangs machtigingen voor het *hoofd* hebben of met een account met verhoogde rechten met behulp van sudo.

#### <a name="upgrade-ubuntu"></a>Ubuntu bijwerken

1. Voer de volgende opdracht uit om de lokale pakket index bij te werken met de meest recente wijzigingen die in de opslag plaatsen zijn aangebracht:

    ```bash
    apt update
    ```

2. Voer de volgende opdracht uit om uw systeem bij te werken:

    ```bash
    apt upgrade
    ```

Acties van de [apt](https://help.ubuntu.com/lts/serverguide/apt.html) -opdracht, zoals het installeren en verwijderen van pakketten, worden geregistreerd in het `/var/log/dpkg.log` logboek bestand.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Upgrade Red Hat/CentOS/Amazon Linux

1. Voer de volgende opdracht uit om de lokale pakket index bij te werken met de meest recente wijzigingen die in de opslag plaatsen zijn aangebracht:

    ```bash
    yum check-update
    ```

2. Voer de volgende opdracht uit om uw systeem bij te werken:

    ```bash
    yum update
    ```

Acties van de [yum](https://access.redhat.com/articles/yum-cheat-sheet) -opdracht, zoals het installeren en verwijderen van pakketten, worden geregistreerd in het `/var/log/yum.log` logboek bestand. 

#### <a name="upgrade-suse-linux-enterprise"></a>Upgrade van SUSE Linux Enter prise

1. Voer de volgende opdracht uit om de lokale pakket index bij te werken met de meest recente wijzigingen die in de opslag plaatsen zijn aangebracht:

    ```bash
    zypper refresh
    ```

2. Voer de volgende opdracht uit om uw systeem bij te werken:

    ```bash
    zypper update
    ```

Acties van de [Zypper](https://en.opensuse.org/Portal:Zypper) -opdracht, zoals het installeren en verwijderen van pakketten, worden geregistreerd in het `/var/log/zypper.log` logboek bestand.

## <a name="about-the-azcmagent-tool"></a>Over het hulp programma Azcmagent

Het hulp programma Azcmagent (Azcmagent.exe) wordt gebruikt voor het configureren van de door Azure Arc ingeschakelde machine agent tijdens de installatie of het wijzigen van de eerste configuratie van de agent na de installatie. Azcmagent.exe biedt opdracht regel parameters om de agent aan te passen en de status ervan weer te geven:

* **Connect** : de computer verbinden met Azure Arc

* De verbinding met de computer met Azure Arc **verbreken**

* De agent status **weer geven en** de bijbehorende configuratie-eigenschappen (naam van de resource groep, abonnements-id, versie enzovoort), die u kan helpen bij het oplossen van een probleem met de agent. Neem de `-j` para meter op om de resultaten in JSON-indeling uit te voeren.

* **Logboeken** : maakt een zip-bestand in de huidige map met logboeken die u kan helpen bij het oplossen van problemen.

* **Versie** : toont de versie van de verbonden machine agent.

* **-h of--Help** : toont beschik bare opdracht regel parameters

    Als u bijvoorbeeld gedetailleerde Help voor de para meter **Connect** wilt weer geven, typt u `azcmagent connect -h` . 

* **-v of-uitgebreid** -uitgebreide logboek registratie inschakelen

U kunt hand matig **verbinding maken** en de verbinding **verbreken** terwijl u zich interactief aanmeldt, of automatiseren met dezelfde service-principal die u hebt gebruikt om meerdere agents uit te voeren of met een [toegangs token](../../active-directory/develop/access-tokens.md)van het micro soft Identity platform. Raadpleeg het volgende [artikel](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) om een service-principal te maken als u geen Service-Principal hebt gebruikt om de machine te registreren met servers die geschikt zijn voor Azure Arc.

>[!NOTE]
>U moet toegangs machtigingen voor het *hoofd* hebben op Linux-machines om **azcmagent** uit te voeren.

### <a name="connect"></a>Verbinding maken

Met deze para meter geeft u een resource op in Azure Resource Manager waarmee de machine wordt gemaakt in Azure. De resource bevindt zich in het opgegeven abonnement en de resource groep en de gegevens over de machine worden opgeslagen in de Azure-regio die is opgegeven door de `--location` instelling. De standaard resource naam is de hostnaam van de computer, indien niet opgegeven.

Een certificaat dat overeenkomt met de door het systeem toegewezen identiteit van de computer, wordt vervolgens lokaal gedownload en opgeslagen. Zodra deze stap is voltooid, beginnen de Azure Connected machine-Metadata Service en de gast configuratie agent met het synchroniseren met servers die zijn ingeschakeld voor Azure Arc.

Als u verbinding wilt maken met behulp van een Service-Principal, voert u de volgende opdracht uit:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Als u verbinding wilt maken met behulp van een toegangs token, voert u de volgende opdracht uit:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Voer de volgende opdracht uit om verbinding te maken met uw referenties met verhoogde bevoegdheden (interactief):

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Verbinding verbreken

Met deze para meter wordt een resource opgegeven in Azure Resource Manager die de machine vertegenwoordigt die wordt verwijderd in Azure. De agent wordt niet van de computer verwijderd, maar u kunt de agent afzonderlijk verwijderen. Als u de verbinding met de computer opnieuw wilt registreren met servers die zijn ingeschakeld voor Azure, kunt u `azcmagent connect` een nieuwe resource maken in Azure.

> [!NOTE]
> Als u een of meer van de Azure VM-extensies hebt geïmplementeerd op uw met Arc ingeschakelde server en u de registratie ervan in azure verwijdert, worden de uitbrei dingen nog steeds geïnstalleerd. Het is belang rijk om te begrijpen dat, afhankelijk van de geïnstalleerde uitbrei ding, de functie actief wordt uitgevoerd. Voor computers die zijn bedoeld om buiten gebruik te worden gesteld of niet meer worden beheerd door Arc-servers, moeten de uitbrei dingen eerst worden verwijderd voordat de registratie van Azure wordt verwijderd.

Voer de volgende opdracht uit om de verbinding met een service-principal te verbreken:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Als u de verbinding wilt verbreken met een toegangs token, voert u de volgende opdracht uit:

`azcmagent disconnect --access-token <accessToken>`

Voer de volgende opdracht uit als u de verbinding wilt verbreken met de referenties die zijn toegevoegd aan een verhoogde bevoegdheid (interactief):

`azcmagent disconnect`

## <a name="remove-the-agent"></a>De agent verwijderen

Voer een van de volgende methoden uit om de met Windows of Linux verbonden machine agent te verwijderen van de computer. Als de agent wordt verwijderd, wordt de registratie van de computer bij servers met Arc ingeschakeld niet ongedaan gemaakt of worden de Azure VM-extensies verwijderd. Hef de registratie van de machine op en verwijder de geïnstalleerde VM-extensies afzonderlijk wanneer u de machine niet meer hoeft te beheren in Azure en deze stappen moeten worden uitgevoerd voordat de agent wordt verwijderd.

### <a name="windows-agent"></a>Windows-agent

Met beide van de volgende methoden wordt de agent verwijderd, maar wordt de map *C:\Program Files\AzureConnectedMachineAgent* niet verwijderd van de computer.

#### <a name="uninstall-from-control-panel"></a>Verwijderen in het configuratie scherm

1. Ga als volgt te werk om de Windows-agent te verwijderen van de computer:

    a. Meld u bij de computer aan met een account met beheerders machtigingen.  
    b. Selecteer in **het configuratie scherm** de optie **Program ma's en onderdelen**.  
    c. In **Program ma's en onderdelen** selecteert u **Azure Connected machine agent**, selecteert u **verwijderen** en selecteert u vervolgens **Ja**.  

    >[!NOTE]
    > U kunt de wizard Setup van agent ook uitvoeren door te dubbel klikken op het **AzureConnectedMachineAgent.msi** Installer-pakket.

#### <a name="uninstall-from-the-command-line"></a>Verwijderen vanaf de opdracht regel

Als u de agent hand matig wilt verwijderen via de opdracht prompt of als u een automatische methode wilt gebruiken, zoals een script, kunt u het volgende voor beeld gebruiken. Eerst moet u de product code ophalen. Dit is een GUID die de principal-id van het toepassings pakket is van het besturings systeem. Het verwijderen wordt uitgevoerd met behulp van de Msiexec.exe opdracht regel `msiexec /x {Product Code}` .

1. Open de REGI ster-editor.

2. Zoek onder register sleutel naar `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` de GUID van de product code en kopieer deze.

3. U kunt de agent vervolgens verwijderen via Msiexec met behulp van de volgende voor beelden:

   * Vanaf het opdracht regel type:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * U kunt dezelfde stappen uitvoeren met behulp van Power shell:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linux-agent

> [!NOTE]
> Als u de agent wilt verwijderen, moet u toegangs machtigingen voor het *hoofd* hebben of met een account met verhoogde rechten met behulp van sudo.

Als u de Linux-agent wilt verwijderen, is de opdracht die moet worden gebruikt, afhankelijk van het Linux-besturings systeem.

- Voor Ubuntu voert u de volgende opdracht uit:

    ```bash
    sudo apt purge azcmagent
    ```

- Voor RHEL, CentOS en Amazon Linux voert u de volgende opdracht uit:

    ```bash
    sudo yum remove azcmagent
    ```

- Voor SLES voert u de volgende opdracht uit:

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>Registratie van machine ongedaan maken

Als u van plan bent om het beheer van de machine met ondersteunende services in azure te stoppen, voert u de volgende stappen uit om de registratie van de computer bij te werken met servers waarop Arc is ingeschakeld. U kunt deze stappen uitvoeren vóór of na het verwijderen van de verbonden machine agent van de computer.

1. Open Azure Arc enabled servers door naar de [Azure Portal](https://aka.ms/hybridmachineportal)te gaan.

2. Selecteer de computer in de lijst, selecteer het beletsel teken (**...**) en selecteer vervolgens **verwijderen**.

## <a name="update-or-remove-proxy-settings"></a>Proxy-instellingen bijwerken of verwijderen

Als u de agent wilt configureren om te communiceren met de service via een proxy server of als u deze configuratie na de implementatie wilt verwijderen, of gebruik een van de volgende methoden om deze taak te volt ooien.

> [!NOTE]
> Arc ingeschakelde servers biedt geen ondersteuning voor het gebruik van een [log Analytics gateway](../../azure-monitor/platform/gateway.md) als een proxy voor de verbonden machine agent.
>

### <a name="windows"></a>Windows

Als u de omgevings variabele proxy server wilt instellen, voert u de volgende opdracht uit:

```powershell
# If a proxy server is needed, execute these commands with the proxy URL and port.
[Environment]::SetEnvironmentVariable("https_proxy","http://{proxy-url}:{proxy-port}","Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
Restart-Service -Name himds
```

Als u de agent zo wilt configureren dat deze niet meer communiceert via een proxy server, voert u de volgende opdracht uit om de omgevings variabele van de proxy server te verwijderen en de Agent service opnieuw te starten:

```powershell
[Environment]::SetEnvironmentVariable("https_proxy",$null,"Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# For the changes to take effect, the agent service needs to be restarted after the proxy environment variable removed.
Restart-Service -Name himds
```

### <a name="linux"></a>Linux

Als u de proxy server wilt instellen, voert u de volgende opdracht uit vanuit de map waarnaar u het installatie pakket voor de agent hebt gedownload:

```bash
# Reconfigure the connected machine agent and set the proxy server.
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

Als u de agent zo wilt configureren dat deze niet meer communiceert via een proxy server, voert u de volgende opdracht uit om de proxy configuratie te verwijderen:

```bash
sudo azcmagent_proxy remove
```

## <a name="next-steps"></a>Volgende stappen

* Informatie over probleem oplossing vindt u in de [hand leiding problemen met verbonden machine agent oplossen](troubleshoot-agent-onboard.md).

* Meer informatie over het beheren van uw machine met [Azure Policy](../../governance/policy/overview.md), voor zaken als VM- [gast configuratie](../../governance/policy/concepts/guest-configuration.md), moet u controleren of de computer rapporteert aan de verwachte log Analytics-werk ruimte, de bewaking inschakelen met [Azure monitor met vm's](../../azure-monitor/insights/vminsights-enable-policy.md)en nog veel meer.

* Meer informatie over de [log Analytics-agent](../../azure-monitor/platform/log-analytics-agent.md). De Log Analytics-agent voor Windows en Linux is vereist wanneer u bewakings gegevens van het besturings systeem en werk belasting wilt verzamelen, deze wilt beheren met Automation-runbooks of-functies zoals Updatebeheer, of om andere Azure-Services zoals [Azure Security Center](../../security-center/security-center-introduction.md)te gebruiken.