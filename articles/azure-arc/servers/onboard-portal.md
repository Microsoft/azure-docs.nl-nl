---
title: Verbinding maken tussen hybride computers en Azure via de Azure Portal
description: In dit artikel leert u hoe u de agent kunt installeren en computers kunt verbinden met Azure met behulp van Azure Arc-servers van de Azure Portal.
ms.date: 11/05/2020
ms.topic: conceptual
ms.openlocfilehash: 97962f7fd9816e398f017555d7043cf65db00ed8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584956"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Verbinding maken tussen hybride computers en Azure via de Azure Portal

U kunt servers voor Azure-Arc inschakelen voor een of meer Windows-of Linux-computers in uw omgeving door een reeks stappen hand matig uit te voeren. U kunt ook een geautomatiseerde methode gebruiken door een sjabloon script uit te voeren dat wij bieden. Met dit script wordt het downloaden en installeren van beide agents geautomatiseerd.

Voor deze methode moet u beheerders rechten op de computer hebben om de agent te installeren en configureren. Op Linux, met behulp van het hoofd account en in Windows, bent u lid van de lokale groep Administrators.

Voordat u aan de slag gaat, moet u de [vereisten](agent-overview.md#prerequisites) controleren en controleren of uw abonnement en resources voldoen aan de vereisten. Zie [ondersteunde Azure-regio's](overview.md#supported-regions)voor meer informatie over ondersteunde regio's en andere gerelateerde overwegingen.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Het installatie script genereren op basis van de Azure Portal

Het script om het downloaden en installeren te automatiseren en de verbinding met Azure Arc tot stand te brengen, is beschikbaar via de Azure Portal. Ga als volgt te werk om het proces te voltooien:

1. Ga in uw browser naar de [Azure Portal](https://portal.azure.com).

1. Selecteer, op de pagina **Servers - Azure Arc** de optie **Toevoegen** in de linkerbovenhoek.

1. Selecteer, op de pagina **Een methode selecteren**, de tegel **Servers toevoegen met interactief script** en selecteer vervolgens **Script genereren**.

1. Selecteer op de pagina **Script genereren** het abonnement en de resourcegroep waarin u de machine wilt beheren binnen Azure. Selecteer een Azure-locatie waar de metagegevens van de machine worden opgeslagen. Deze locatie kan hetzelfde zijn als, of verschillen van, de locatie van de resourcegroep.

1. Controleer de informatie op de pagina **Vereisten** en selecteer daarna **Volgende: Resourcegegevens**.

1. Geef, op de pagina **Resourcegegevens**, het volgende op:

    1. Selecteer in de vervolgkeuzelijst **Resourcegroep**, de resourcegroep van waaruit de machine wordt beheerd.
    1. Selecteer in de vervolg keuzelijst **Regio**, de Azure-regio om de metagegevens van de server op te slaan.
    1. Selecteer in de vervolgkeuzelijst **Besturingssysteem**, het besturingssysteem waarop het script zal worden uitgevoerd.
    1. Als de machine communiceert door middel van een proxyserver, geeft u het volgende op: het IP-adres van de proxyserver, of de naam en het poortnummer die de machine zal gebruiken om met de proxyserver te communiceren. Voer de waarde in de indeling `http://<proxyURL>:<proxyport>` in.
    1. Selecteer **Volgende: Tags**.

1. Controleer, op de pagina **Tags**, de standaard **Fysieke locatiecodes** die worden voorgesteld. Voer vervolgens een waarde in, of geef één of meer **Aangepaste codes** op om uw standaarden te ondersteunen.

1. Selecteer **Volgende: Het script downloaden en uitvoeren**.

1. Bekijk, op de pagina **Het script downloaden en uitvoeren**, de overzichtsgegevens. Selecteer vervolgens **Downloaden**. Als u nog wijzigingen wilt aanbrengen, selecteert u **Vorige**.

## <a name="install-and-validate-the-agent-on-windows"></a>De agent in Windows installeren en valideren

### <a name="install-manually"></a>De installatie handmatig uitvoeren

U kunt de aangesloten machine agent hand matig installeren door de Windows Installer-pakket *AzureConnectedMachineAgent.msi* uit te voeren. U kunt de nieuwste versie van het [Windows agent-Windows Installer pakket](https://aka.ms/AzureConnectedMachineAgent) downloaden van het micro soft Download centrum.

>[!NOTE]
>* Als u de agent wilt installeren of verwijderen, moet u over *beheerders* machtigingen beschikken.
>* U moet eerst het installatie pakket downloaden en kopiëren naar een map op de doel server of vanuit een gedeelde netwerkmap. Als u het installatie pakket zonder opties uitvoert, wordt een installatie wizard gestart die u kunt volgen om de agent interactief te installeren.

Als de machine moet communiceren via een proxy server naar de service, moet u na de installatie van de agent een opdracht uitvoeren die wordt beschreven in de onderstaande stappen. Met deze opdracht wordt de systeem omgevings variabele proxy server ingesteld `https_proxy` .

Als u niet bekend bent met de opdracht regel opties voor Windows Installer-pakketten, raadpleegt u [Msiexec Standard-opdracht regel opties](/windows/win32/msi/standard-installer-command-line-options) en [Msiexec-opdracht regel opties](/windows/win32/msi/command-line-options).

Voer bijvoorbeeld het installatie programma uit met de `/?` para meter om de optie Help en snelle referentie te controleren.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

1. Voer de volgende opdracht uit om de agent op de achtergrond te installeren en een installatie logboek bestand te maken in de `C:\Support\Logs` map die bestaat.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
    ```

    Als de agent niet kan worden gestart nadat de installatie is voltooid, raadpleegt u de logboeken voor gedetailleerde informatie over de fout. De logboekmap is *%ProgramData%\AzureConnectedMachineAgent\log*.

2. Als de machine moet communiceren via een proxy server, voert u de volgende opdracht uit om de proxy server-omgevings variabele in te stellen:

    ```powershell
    [Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
    $env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
    # For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
    Restart-Service -Name himds
    ```

    >[!NOTE]
    >De agent biedt geen ondersteuning voor het instellen van proxy verificatie in deze preview.
    >

3. Nadat u de agent hebt geïnstalleerd, moet u deze configureren om te communiceren met de Azure Arc-service door de volgende opdracht uit te voeren:

    ```dos
    "%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"
    ```

### <a name="install-with-the-scripted-method"></a>Installeren met de script methode

1. Meld u aan bij de server.

1. Open een PowerShell-opdrachtprompt met verhoogde bevoegdheid.

    >[!NOTE]
    >Het script ondersteunt alleen uitvoering vanaf een 64-bits versie van Windows Power shell.
    >

1. Ga naar de map of share waarnaar u het script hebt gekopieerd en voer dit uit op de server door het script `./OnboardingScript.ps1` uit te voeren.

Als de agent niet kan worden gestart nadat de installatie is voltooid, raadpleegt u de logboeken voor gedetailleerde informatie over de fout. De logboekmap is *%ProgramData%\AzureConnectedMachineAgent\log*.

## <a name="install-and-validate-the-agent-on-linux"></a>De agent op Linux installeren en valideren

De verbonden machine agent voor Linux is opgenomen in de voorkeurs pakket indeling voor de distributie (. RPM of. DEB) die wordt gehost in de micro soft [package-opslag plaats](https://packages.microsoft.com/). De [shell-script bundel `Install_linux_azcmagent.sh` ](https://aka.ms/azcmagent) voert de volgende acties uit:

* Hiermee configureert u de hostmachine voor het downloaden van het agent pakket van packages.microsoft.com.

* Hiermee wordt het Hybrid resource provider-pakket geïnstalleerd.

Desgewenst kunt u de agent met uw proxy gegevens configureren door de para meter op te nemen `--proxy "{proxy-url}:{proxy-port}"` .

Het script bevat ook logica voor het identificeren van ondersteunde en niet-ondersteunde distributies en controleert de machtigingen die nodig zijn om de installatie uit te voeren.

In het volgende voor beeld wordt de agent gedownload en geïnstalleerd:

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent.
bash ~/Install_linux_azcmagent.sh
```

1. Voer de volgende opdrachten uit om de agent te downloaden en te installeren, met inbegrip `--proxy` van de para meter voor het configureren van de agent om te communiceren via uw proxy server:

    ```bash
    # Download the installation package.
    wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

    # Install the connected machine agent.
    bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
    ```

2. Nadat u de agent hebt geïnstalleerd, moet u deze configureren om te communiceren met de Azure Arc-service door de volgende opdracht uit te voeren:

    ```bash
    azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --cloud "cloudName"
    if [ $? = 0 ]; then echo "\033[33mTo view your onboarded server(s), navigate to https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.HybridCompute%2Fmachines\033[m"; fi
    ```

### <a name="install-with-the-scripted-method"></a>Installeren met de script methode

1. Meld u bij de server aan met een account dat toegang heeft tot het hoofd niveau.

1. Ga naar de map of share waarnaar u het script hebt gekopieerd en voer dit uit op de server door het script `./OnboardingScript.sh` uit te voeren.

Als de agent niet kan worden gestart nadat de installatie is voltooid, raadpleegt u de logboeken voor gedetailleerde informatie over de fout. De logboekmap is *var/opt/azcmagent/log*.

## <a name="verify-the-connection-with-azure-arc"></a>De verbinding met Azure Arc controleren

Nadat u de agent hebt geïnstalleerd en geconfigureerd om verbinding te maken met Azure Arc-servers, gaat u naar Azure Portal om te controleren of de server verbinding heeft gemaakt. Bekijk uw computers in [Azure Portal](https://aka.ms/hybridmachineportal).

![Een geslaagde server verbinding](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Volgende stappen

* Informatie over probleem oplossing vindt u in de [hand leiding problemen met verbonden machine agent oplossen](troubleshoot-agent-onboard.md).

* Meer informatie over het beheren van uw machine met [Azure Policy](../../governance/policy/overview.md), voor zaken als VM- [gast configuratie](../../governance/policy/concepts/guest-configuration.md), moet u controleren of de computer rapporteert aan de verwachte log Analytics-werk ruimte, de bewaking inschakelen met [Azure monitor met vm's](../../azure-monitor/vm/vminsights-enable-policy.md)en nog veel meer.

* Meer informatie over de [log Analytics-agent](../../azure-monitor/agents/log-analytics-agent.md). De Log Analytics-agent voor Windows en Linux is vereist wanneer u bewakings gegevens van het besturings systeem en werk belasting wilt verzamelen, deze wilt beheren met Automation-runbooks of-functies zoals Updatebeheer, of om andere Azure-Services zoals [Azure Security Center](../../security-center/security-center-introduction.md)te gebruiken.