---
title: Overzicht van de connected machine-agent
description: In dit artikel vindt u een gedetailleerd overzicht van Azure Arc beschikbare serversagent, die ondersteuning biedt voor het bewaken van virtuele machines die worden gehost in hybride omgevingen.
ms.date: 03/25/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dd58997a8af86a3789895bfb29bfd5803826fa6f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832955"
---
# <a name="overview-of-azure-arc-enabled-servers-agent"></a>Overzicht van Azure Arc ingeschakelde serversagent

Met Azure Arc servers connected machine-agent kunt u uw Windows- en Linux-machines beheren die buiten Azure worden gehost in uw bedrijfsnetwerk of een andere cloudprovider. Dit artikel biedt een gedetailleerd overzicht van de agent, systeem- en netwerkvereisten en de verschillende implementatiemethoden.

>[!NOTE]
>Vanaf de algemene release van Azure Arc-servers in september 2020 worden alle voorlopige versies van de Azure Connected Machine-agent (agents met een versie  lager dan 1.0) afgeschaft voor 2 februari **2021.**  In dit tijdsbestek kunt u upgraden naar versie 1.0 of hoger voordat de vooraf uitgebrachte agents niet meer kunnen communiceren met de Azure Arc-serverservice.

## <a name="agent-component-details"></a>Details van agentcomponenten

:::image type="content" source="media/agent-overview/connected-machine-agent.png" alt-text="Overzicht van servers met Arc-agent." border="false":::

Het Azure Connected Machine agent bevat verschillende logische onderdelen die zijn gebundeld.

* De Hybrid Instance Metadata Service (HIMDS) beheert de verbinding met Azure en de Azure-identiteit van de verbonden machine.

* De gastconfiguratieagent biedt In-Guest functionaliteit voor beleid en gastconfiguratie, zoals het beoordelen of de machine voldoet aan het vereiste beleid.

    Let op het volgende gedrag met Azure Policy [gastconfiguratie voor](../../governance/policy/concepts/guest-configuration.md) een niet-verbonden machine:

    * Een beleidstoewijzing voor gastenconfiguratie die is gericht op niet-verbonden computers, wordt niet beïnvloed.
    * Gasttoewijzing wordt 14 dagen lokaal opgeslagen. Als de connected machine-agent binnen de periode van 14 dagen opnieuw verbinding maakt met de service, worden beleidstoewijzingen opnieuw toegepast.
    * Toewijzingen worden na 14 dagen verwijderd en na de periode van 14 dagen niet opnieuw toegewezen aan de machine.

* De extensieagent beheert VM-extensies, waaronder installeren, verwijderen en upgraden. Extensies worden gedownload van Azure en gekopieerd naar de `%SystemDrive%\%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads` map in Windows en voor Linux naar `/opt/GC_Ext/downloads` . In Windows wordt de extensie geïnstalleerd op het volgende pad `%SystemDrive%\Packages\Plugins\<extension>` en in Linux wordt de extensie geïnstalleerd op `/var/lib/waagent/<extension>` .

## <a name="instance-metadata"></a>Metagegevens van exemplaar

Metagegevensinformatie over de verbonden machine wordt verzameld nadat de Connected Machine-agent is geregistreerd bij arc-servers. Met name:

* Naam, type en versie van het besturingssysteem
* Computernaam
* Volledig gekwalificeerde domeinnaam (FQDN) van de computer
* Versie van Connected Machine-agent
* FQDN (Fully Qualified Domain Name) voor Active Directory en DNS
* UUID (BIOS-ID)
* Heartbeat van connected machine-agent
* Versie van Connected Machine-agent
* Openbare sleutel voor beheerde identiteit
* Nalevingsstatus en -details van het beleid (als u gastconfiguratiebeleid Azure Policy)

De volgende metagegevens worden door de agent aangevraagd bij Azure:

* Resourcelocatie (regio)
* Id van virtuele machine
* Tags
* Azure Active Directory managed identity certificate
* Beleidstoewijzingen voor gastenconfiguratie
* Extensieaanvragen: installeren, bijwerken en verwijderen.

## <a name="download-agents"></a>Agents downloaden

U kunt het Azure Connected Machine voor Windows en Linux downloaden vanaf de onderstaande locaties.

* [Windows-agent Windows Installer-pakket](https://aka.ms/AzureConnectedMachineAgent) uit het Microsoft Downloadcentrum.

* Het Linux-agentpakket wordt gedistribueerd vanuit de [pakketopslagplaats van](https://packages.microsoft.com/) Microsoft met behulp van de voorkeurspakketindeling voor de distributie (. RPM of . DEB).

De Azure Connected Machine voor Windows en Linux kan handmatig of automatisch worden bijgewerkt naar de nieuwste versie, afhankelijk van uw vereisten. Klik [hier](manage-agent.md) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

### <a name="supported-environments"></a>Ondersteunde omgevingen

Arc-servers ondersteunen de installatie van de Connected Machine-agent op elke fysieke server en virtuele machine die *buiten* Azure wordt gehost. Dit omvat virtuele machines die worden uitgevoerd op platforms zoals VMware, Azure Stack HCI en andere cloudomgevingen. Arc-servers bieden geen ondersteuning voor het installeren van de agent op virtuele machines die worden uitgevoerd in Azure of virtuele machines die worden uitgevoerd op Azure Stack Hub of Azure Stack Edge, omdat ze al zijn gemodelleerd als Azure-VM's.

### <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

De volgende versies van het besturingssysteem Windows en Linux worden officieel ondersteund voor de Azure Connected Machine agent:

- Windows Server 2008 R2, Windows Server 2012 R2 en hoger (inclusief Server Core)
- Ubuntu 16.04 en 18.04 LTS (x64)
- CentOS Linux 7 (x64)
- SUSE Linux Enterprise Server (SLES) 15 (x64)
- Red Hat Enterprise Linux (RHEL) 7 (x64)
- Amazon Linux 2 (x64)
- Oracle Linux 7

> [!WARNING]
> In de naam van de Linux-host of de Windows-computer mag niet een van de gereserveerde woorden of merken voorkomen, anders mislukt het registreren van de verbonden machine met Azure. Zie [Fouten in de gereserveerde resourcenaam oplossen](../../azure-resource-manager/templates/error-reserved-resource-name.md) voor een lijst met de gereserveerde woorden.

### <a name="required-permissions"></a>Vereiste machtigingen

* Als u machines wilt onboarden, bent u lid **van Azure Connected Machine rol Onboarding** of [Inzender](../../role-based-access-control/built-in-roles.md#contributor) in de resourcegroep.

* Als u een machine wilt lezen, wijzigen en verwijderen, bent u lid van de rol Azure Connected Machine **resourcebeheerder** in de resourcegroep.

* Als u een resourcegroep wilt selecteren in de vervolgkeuzelijst wanneer u de [](../../role-based-access-control/built-in-roles.md#reader) methode **Script** genereren gebruikt, bent u minimaal lid van de rol Lezer voor die resourcegroep.

### <a name="azure-subscription-and-service-limits"></a>Limieten voor het Azure-abonnement en de Azure-service

Voordat u uw machines configureert met Azure Arc ingeschakelde [](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) servers, controleert u de limieten van het Azure Resource Manager-abonnement en de [resourcegroeplimieten](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) om te plannen voor het aantal machines dat moet worden verbonden.

Azure Arc-servers ondersteunt maximaal 5000 machine-exemplaren in een resourcegroep.

### <a name="transport-layer-security-12-protocol"></a>Transport Layer Security 1.2-protocol

Om de beveiliging van gegevens die onderweg zijn naar Azure te waarborgen, raden we u ten zeerste aan machine te configureren voor het gebruik van Transport Layer Security (TLS) 1.2. Oudere versies van TLS/Secure Sockets Layer (SSL) zijn kwetsbaar en hoewel ze nog steeds werken om compatibiliteit met eerdere versies toe te staan, worden ze **niet aanbevolen.**

|Platform/taal | Ondersteuning | Meer informatie |
| --- | --- | --- |
|Linux | Linux-distributies zijn vaak afhankelijk [van OpenSSL](https://www.openssl.org) voor TLS 1.2-ondersteuning. | Controleer het [OpenSSL-wijzigingslogo](https://www.openssl.org/news/changelog.html) om te controleren of uw versie van OpenSSL wordt ondersteund.|
| Windows Server 2012 R2 en hoger | Ondersteund en standaard ingeschakeld. | Bevestig dat u nog steeds de [standaardinstellingen gebruikt.](/windows-server/security/tls/tls-registry-settings)|

### <a name="networking-configuration"></a>Netwerkconfiguratie

De Connected Machine-agent voor Linux en Windows communiceert veilig uitgaand naar Azure Arc via TCP-poort 443. Als de computer verbinding maakt via een firewall of proxyserver om via internet te communiceren, controleert u het volgende om inzicht te krijgen in de netwerkconfiguratievereisten.

> [!NOTE]
> Arc-servers bieden geen ondersteuning voor het gebruik van een [Log Analytics-gateway](../../azure-monitor/agents/gateway.md) als proxy voor de Connected Machine-agent.
>

Als de uitgaande connectiviteit wordt beperkt door uw firewall of proxyserver, moet u ervoor zorgen dat de onderstaande URL's niet worden geblokkeerd. Wanneer u alleen de IP-bereiken of domeinnamen toestaat die de agent nodig heeft om met de service te communiceren, moet u toegang tot de volgende servicetags en URL's toestaan.

Servicetags:

* AzureActiveDirectory
* AzureTrafficManager
* AzureResourceManager
* AzureArcInfrastructure

Urls:

| Agentresource | Description |
|---------|---------|
|`management.azure.com`|Azure Resource Manager|
|`login.windows.net`|Azure Active Directory|
|`login.microsoftonline.com`|Azure Active Directory|
|`dc.services.visualstudio.com`|Application Insights|
|`*.guestconfiguration.azure.com` |Gastconfiguratie|
|`*.his.arc.azure.com`|Hybride identiteitsservice|
|`www.office.com`|Office 365|

Preview-agents (versie 0.11 en lager) vereisen ook toegang tot de volgende URL's:

| Agentresource | Description |
|---------|---------|
|`agentserviceapi.azure-automation.net`|Gastconfiguratie|
|`*-agentservice-prod-1.azure-automation.net`|Gastconfiguratie|

Zie het JSON-bestand - Azure IP-adresbereiken en servicetags – openbare cloud voor een lijst met [IP-adressen voor elke servicetag/-regio.](https://www.microsoft.com/download/details.aspx?id=56519) Microsoft publiceert wekelijkse updates met elke Azure-service en de IP-adresbereiken die worden gebruikt. Deze informatie in het JSON-bestand is de huidige point-in-time-lijst van de IP-adresbereiken die overeenkomen met elke servicetag. De IP-adressen kunnen worden gewijzigd. Als IP-adresbereiken vereist zijn voor uw firewallconfiguratie, moet de **Servicetag AzureCloud** worden gebruikt om toegang tot alle Azure-services toe te staan. Schakel beveiligingsbewaking of inspectie van deze URL's niet uit, sta ze toe zoals u dat met ander internetverkeer zou doen.

Bekijk Overzicht van [servicetags voor meer informatie.](../../virtual-network/service-tags-overview.md)

### <a name="register-azure-resource-providers"></a>Azure-resourceproviders registreren

Servers voor Azure Arc zijn afhankelijk van de volgende Azure-resourceproviders in uw abonnement om deze service te kunnen gebruiken:

* **Microsoft.HybridCompute**
* **Microsoft.GuestConfiguration**

Als ze niet zijn geregistreerd, kunt u ze registreren met behulp van de volgende opdrachten:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

U kunt de resourceproviders ook registreren in de Azure Portal door de stappen onder [Azure Portal.](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)

## <a name="installation-and-configuration"></a>Installatie en configuratie

U kunt machines in uw hybride omgeving rechtstreeks verbinden met Azure met behulp van verschillende methoden, afhankelijk van uw vereisten. In de volgende tabel wordt elke methode belicht om te bepalen welke methode het beste werkt voor uw organisatie.

> [!IMPORTANT]
> De Connected Machine-agent kan niet worden geïnstalleerd op een virtuele Azure Windows-machine. Als u dit probeert te doen, detecteert de installatie dit en wordt er terug worpen.

| Methode | Beschrijving |
|--------|-------------|
| Interactief | Installeer de agent handmatig op één of een klein aantal computers aan de hand van de stappen in [Computers verbinden Azure Portal](onboard-portal.md).<br> Vanuit de Azure Portal kunt u een script genereren en uitvoeren op de computer om de installatie- en configuratiestappen van de agent te automatiseren.|
| Op schaal | Installeer en configureer de agent voor meerdere computers die de Machines [verbinden volgen met behulp van een service-principal.](onboard-service-principal.md)<br> Met deze methode maakt u een service-principal om machines niet-interactief te verbinden.|
| Op schaal | Installeer en configureer de agent voor meerdere computers volgens de methode [Using Windows PowerShell DSC](onboard-dsc.md).<br> Deze methode maakt gebruik van een service-principal om machines niet-interactief te verbinden met PowerShell DSC. |

## <a name="connected-machine-agent-technical-overview"></a>Technisch overzicht van connected machine-agent

### <a name="windows-agent-installation-details"></a>Installatiedetails van Windows-agent

De Connected Machine-agent voor Windows kan worden geïnstalleerd met behulp van een van de volgende drie methoden:

* Dubbelklik op het bestand `AzureConnectedMachineAgent.msi` .
* Handmatig door het pakket Windows Installer uitvoeren `AzureConnectedMachineAgent.msi` vanuit de opdrachtshell.
* Vanuit een PowerShell-sessie met behulp van een scriptmethode.

Na de installatie van de Connected Machine-agent voor Windows worden de volgende configuratiewijzigingen voor het hele systeem toegepast.

* De volgende installatiemappen worden gemaakt tijdens de installatie.

    |Map |Description |
    |-------|------------|
    |%ProgramFiles%\AzureConnectedMachineAgent |Standaardinstallatiepad met de agentondersteuningsbestanden.|
    |%ProgramData%\AzureConnectedMachineAgent |Bevat de agentconfiguratiebestanden.|
    |%ProgramData%\AzureConnectedMachineAgent\Tokens |Bevat de verkregen tokens.|
    |%ProgramData%\AzureConnectedMachineAgent\Config |Bevat het configuratiebestand van de agent `agentconfig.json` waarin de registratiegegevens worden geregistreerd bij de service.|
    |%ProgramFiles%\ArcConnectedMachineAgent\ExtensionService\GC | Installatiepad met de bestanden van de gastconfiguratieagent. |
    |%ProgramData%\GuestConfig |Bevat de (toegepaste) beleidsregels van Azure.|
    |%ProgramFiles%\AzureConnectedMachineAgent\ExtensionService\downloads | Extensies worden gedownload van Azure en hier gekopieerd.|

* De volgende Windows-services worden gemaakt op de doelmachine tijdens de installatie van de agent.

    |Servicenaam |Weergavenaam |Procesnaam |Description |
    |-------------|-------------|-------------|------------|
    |himds |Azure Hybrid Instance Metadata Service |himds |Deze service implementeert de Azure Instance Metadata Service (IMDS) voor het beheren van de verbinding met Azure en de Azure-identiteit van de verbonden machine.|
    |GCArcService |Arc-service voor gastconfiguratie |gc_service |Controleert de gewenste statusconfiguratie van de machine.|
    |ExtensionService |Gastconfiguratie-extensieservice | gc_service |Installeert de vereiste extensies die zijn gericht op de computer.|

* De volgende omgevingsvariabelen worden gemaakt tijdens de installatie van de agent.

    |Name |Standaardwaarde |Beschrijving |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Er zijn verschillende logboekbestanden beschikbaar voor het oplossen van problemen. Ze worden beschreven in de volgende tabel.

    |Logboek |Description |
    |----|------------|
    |%ProgramData%\AzureConnectedMachineAgent\Log\himds.log |Registreert gegevens van de service agents (HIMDS) en interactie met Azure.|
    |%ProgramData%\AzureConnectedMachineAgent\Log\azcmagent.log |Bevat de uitvoer van de opdrachten van het hulpprogramma azcmagent, wanneer het uitgebreide argument (-v) wordt gebruikt.|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent.log |Registreert gegevens van de activiteit van de DSC-service,<br> met name de connectiviteit tussen de HIMDS-service en Azure Policy.|
    |%ProgramData%\GuestConfig\gc_agent_logs\gc_agent_telemetry.txt |Registreert gegevens over DSC-service-telemetrie en uitgebreide logboekregistratie.|
    |%ProgramData%\GuestConfig\ext_mgr_logs|Registreert gegevens over het onderdeel Extensieagent.|
    |%ProgramData%\GuestConfig\extension_logs\<Extension>|Registreert gegevens van de geïnstalleerde extensie.|

* De lokale beveiligingsgroep **Toepassingen voor hybride agentextensie** wordt gemaakt.

* Tijdens het verwijderen van de agent worden de volgende artefacten niet verwijderd.

    * %ProgramData%\AzureConnectedMachineAgent\Log
    * %ProgramData%\AzureConnectedMachineAgent en subdirectories
    * %ProgramData%\GuestConfig

### <a name="linux-agent-installation-details"></a>Installatiedetails van Linux-agent

De Connected Machine-agent voor Linux wordt opgegeven in de voorkeurspakketindeling voor de distributie (. RPM of . DEB) die wordt gehost in de [Microsoft-pakketopslagplaats](https://packages.microsoft.com/). De agent is geïnstalleerd en geconfigureerd met de shellscriptbundel [Install_linux_azcmagent.sh.](https://aka.ms/azcmagent)

Na de installatie van de Connected Machine-agent voor Linux worden de volgende configuratiewijzigingen voor het hele systeem toegepast.

* De volgende installatiemappen worden gemaakt tijdens de installatie.

    |Map |Description |
    |-------|------------|
    |/var/opt/azcmagent/ |Standaardinstallatiepad met de agentondersteuningsbestanden.|
    |/opt/azcmagent/ |
    |/opt/GC_Ext | Installatiepad met de bestanden van de gastconfiguratieagent.|
    |/opt/DSC/ |
    |/var/opt/azcmagent/tokens |Bevat de verkregen tokens.|
    |/var/lib/GuestConfig |Bevat de (toegepaste) beleidsregels van Azure.|
    |/opt/GC_Ext/downloads|Extensies worden gedownload van Azure en hier gekopieerd.|

* De volgende daemons worden gemaakt op de doelmachine tijdens de installatie van de agent.

    |Servicenaam |Weergavenaam |Procesnaam |Description |
    |-------------|-------------|-------------|------------|
    |himdsd.service |Azure Connected Machine Agent Service |himds |Deze service implementeert de Azure Instance Metadata Service (IMDS) voor het beheren van de verbinding met Azure en de Azure-identiteit van de verbonden machine.|
    |gcad.servce |GC Arc-service |gc_linux_service |Controleert de gewenste statusconfiguratie van de machine. |
    |extd.service |Extensieservice |gc_linux_service | Installeert de vereiste extensies die gericht zijn op de computer.|

* Er zijn verschillende logboekbestanden beschikbaar voor het oplossen van problemen. Ze worden beschreven in de volgende tabel.

    |Logboek |Description |
    |----|------------|
    |/var/opt/azcmagent/log/himds.log |Registreert gegevens van de service agents (HIMDS) en interactie met Azure.|
    |/var/opt/azcmagent/log/azcmagent.log |Bevat de uitvoer van de opdrachten van het hulpprogramma azcmagent, wanneer het uitgebreide argument (-v) wordt gebruikt.|
    |/opt/logs/dsc.log |Registreert gegevens van de activiteit van de DSC-service,<br> met name de connectiviteit tussen de himds-service en Azure Policy.|
    |/opt/logs/dsc.telemetry.txt |Registreert gegevens over DSC-service-telemetrie en uitgebreide logboekregistratie.|
    |/var/lib/GuestConfig/ext_mgr_logs |Registreert gegevens over het onderdeel Extensieagent.|
    |/var/lib/GuestConfig/extension_logs|Registreert gegevens van de geïnstalleerde extensie.|

* De volgende omgevingsvariabelen worden gemaakt tijdens de installatie van de agent. Deze variabelen worden ingesteld in `/lib/systemd/system.conf.d/azcmagent.conf` .

    |Name |Standaardwaarde |Beschrijving |
    |-----|--------------|------------|
    |IDENTITY_ENDPOINT |http://localhost:40342/metadata/identity/oauth2/token ||
    |IMDS_ENDPOINT |http://localhost:40342 ||

* Tijdens het verwijderen van de agent worden de volgende artefacten niet verwijderd.

    * /var/opt/azcmagent
    * /opt/logs

## <a name="next-steps"></a>Volgende stappen

* Als u wilt beginnen met het evalueren Azure Arc ingeschakelde servers, volgt u het artikel [Connect hybrid machines to Azure from the Azure Portal](onboard-portal.md).

* Informatie over probleemoplossing vindt u in de handleiding Problemen met [de connected machine-agent oplossen.](troubleshoot-agent-onboard.md)
