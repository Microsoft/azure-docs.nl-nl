---
title: Log Analytics-agent installeren op Linux-computers
description: In dit artikel wordt beschreven hoe u Linux-computers die worden gehost in andere clouds of on-premises, verbindt met Azure Monitor met de Log Analytics-agent voor Linux.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 23597804a34a9bc409db179010569024aa472016
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725922"
---
# <a name="install-log-analytics-agent-on-linux-computers"></a>Log Analytics-agent installeren op Linux-computers
In dit artikel vindt u meer informatie over het installeren van de Log Analytics-agent op Linux-computers met behulp van de volgende methoden:

* [Installeer de agent voor Linux met behulp van een wrapper-script dat](#install-the-agent-using-wrapper-script) wordt gehost op GitHub. Dit is de aanbevolen methode om de agent te installeren en bij te werken wanneer de computer verbinding heeft met internet, rechtstreeks of via een proxyserver.
* [Download en installeer de](#install-the-agent-manually) agent handmatig. Dit is vereist wanneer de Linux-computer geen toegang heeft tot internet en communiceert met Azure Monitor of Azure Automation via de [Log Analytics-gateway.](./gateway.md) 

>[!IMPORTANT]
> De installatiemethoden die in dit artikel worden beschreven, worden doorgaans gebruikt voor on-premises virtuele machines of in andere clouds. Zie [Installatieopties voor](./log-analytics-agent.md#installation-options) efficiëntere opties die u kunt gebruiken voor virtuele Azure-machines.



## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

Zie [Overzicht van Azure Monitor agents](agents-overview.md#supported-operating-systems) voor een lijst met Linux-distributies die worden ondersteund door de Log Analytics-agent.

>[!NOTE]
>OpenSSL 1.1.0 wordt alleen ondersteund op x86_x64-platformen (64-bits) en OpenSSL eerder dan 1.x wordt op geen enkel platform ondersteund.

>[!NOTE]
>Het uitvoeren van de Log Analytics Linux-agent in containers wordt niet ondersteund. Als u containers wilt bewaken, maakt u gebruik van de [containerbewakingsoplossing](../containers/containers.md) voor Docker-hosts of [Container Insights](../containers/container-insights-overview.md) voor Kubernetes.

Vanaf de versies die na augustus 2018 zijn uitgebracht, brengen we de volgende wijzigingen aan in ons ondersteuningsmodel:  

* Alleen de serverversies worden ondersteund, niet de client.  
* Richt u op een van de [door Azure Linux goedgekeurde distributies.](../../virtual-machines/linux/endorsed-distros.md) Houd er rekening mee dat er enige vertraging kan zijn tussen een nieuwe distributie/versie die door Azure Linux is goedgekeurd en die wordt ondersteund voor de Log Analytics Linux-agent.
* Alle kleine releases worden ondersteund voor elke vermelde hoofdversie.
* Versies die de einddatum van de ondersteuningsdatum van de fabrikant hebben doorgenomen, worden niet ondersteund.
* Ondersteunt alleen VM-afbeeldingen; -containers, zelfs die zijn afgeleid van officiële uitgevers van distributie-afbeeldingen, worden niet ondersteund.
* Nieuwe versies van AMI worden niet ondersteund.  
* Alleen versies met OpenSSL 1.x worden standaard ondersteund.

>[!NOTE]
>Als u een distributie of versie gebruikt die momenteel niet wordt ondersteund en niet is afgestemd op ons ondersteuningsmodel, raden we u aan deze repo te vertakken, met de bevestiging dat Microsoft Ondersteuning geen hulp biedt bij gevorkte agentversies.

### <a name="python-requirement"></a>Python-vereiste

Vanaf Agent versie 1.13.27 biedt de Linux-agent ondersteuning voor zowel Python 2 als 3. U wordt altijd aangeraden de meest recente agent te gebruiken. 

Als u een oudere versie van de agent gebruikt, moet de virtuele machine standaard Python 2 gebruiken. Als uw virtuele machine een distributie gebruikt die niet standaard Python 2 bevat, moet u deze installeren. Met de volgende voorbeeldopdrachten wordt Python 2 op verschillende distributies geïnstalleerd.

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - Suse: `zypper install -y python2`

Het uitvoerbare python2-bestand moet een alias hebben voor *python*. Hieronder volgt één methode die u kunt gebruiken om deze alias in te stellen:

1. Voer de volgende opdracht uit om eventuele bestaande aliassen te verwijderen.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Voer de volgende opdracht uit om de alias te maken.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## <a name="supported-linux-hardening"></a>Ondersteunde Linux-hardening
De OMS-agent biedt beperkte ondersteuning voor aanpassen en beperken voor Linux.

Het volgende wordt momenteel ondersteund: 
- Fips

Het volgende wordt niet ondersteund:
- Gos
- Selinux

Ondersteuning voor CIS- en CIINUX-hardening is gepland voor [Azure Monitoring Agent.](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview) Verdere methoden voor hardening en aanpassing worden niet ondersteund of gepland voor DE OMS-agent.  

## <a name="agent-prerequisites"></a>Vereisten voor de agent

De volgende tabel markeert de pakketten die vereist zijn [voor ondersteunde Linux-distributies](#supported-operating-systems) waar de agent op wordt geïnstalleerd.

|Vereist pakket |Description |Minimale versie |
|-----------------|------------|----------------|
|Glibc |    GNU C-bibliotheek | 2.5-12 
|Openssl    | OpenSSL-bibliotheken | 1.0.x of 1.1.x |
|Curl | cURL-webclient | 7.15.5 |
|Python | | 2.7 of 3.6+
|Python-ctypes | | 
|PAM | Pluggable Authentication Modules | | 

>[!NOTE]
>rsyslog of syslog-ng zijn vereist voor het verzamelen van syslog-berichten. De standaard-syslog-daemon op versie 5 van Red Hat Enterprise Linux, CentOS en Oracle Linux-versie (sysklog) wordt niet ondersteund voor het verzamelen van syslog-gebeurtenissen. Als u syslog-gegevens wilt verzamelen uit deze versie van deze distributies, moet de rsyslog-daemon worden geïnstalleerd en geconfigureerd om sysklog te vervangen.

## <a name="network-requirements"></a>Netwerkvereisten
Zie [Overzicht van Log Analytics-agent](./log-analytics-agent.md#network-requirements) voor de netwerkvereisten voor de Linux-agent.

## <a name="agent-install-package"></a>Installatiepakket voor agent

De Log Analytics-agent voor Linux bestaat uit meerdere pakketten. Het releasebestand bevat de volgende pakketten, die beschikbaar zijn door de shellbundel met de parameter uit te `--extract` stellen:

**Pakket** | **Versie** | **Beschrijving**
----------- | ----------- | --------------
omsagent | 1.13.9 | De Log Analytics-agent voor Linux
omsconfig | 1.1.1 | Configuratieagent voor de Log Analytics-agent
Omi | 1.6.4 | Open Management Infrastructure (OMI) : een lichtgewicht CIM-server. *Houd er rekening mee dat OMI hoofdtoegang vereist om een Cron-taak uit te voeren die nodig is voor de werking van de service*
scx | 1.6.4 | OMI CIM-providers voor metrische gegevens over de prestaties van het besturingssysteem
apache-cimprov | 1.0.1 | Prestatiebewakingsprovider van Apache HTTP Server voor OMI. Alleen geïnstalleerd als Apache HTTP Server wordt gedetecteerd.
mysql-cimprov | 1.0.1 | Prestatiebewakingsprovider voor MySQL Server voor OMI. Alleen geïnstalleerd als de MySQL/MariaDB-server wordt gedetecteerd.
docker-cimprov | 1.0.0 | Docker-provider voor OMI. Alleen geïnstalleerd als Docker wordt gedetecteerd.

### <a name="agent-installation-details"></a>Installatiedetails van agent

Na de installatie van de Log Analytics-agent voor Linux-pakketten worden de volgende aanvullende configuratiewijzigingen voor het hele systeem toegepast. Deze artefacten worden verwijderd wanneer het omsagent-pakket wordt verwijderd.

* Er wordt een niet-bevoegde gebruiker met de naam : `omsagent` gemaakt. De daemon wordt uitgevoerd onder deze referentie. 
* Er wordt een sudoers *include-bestand* gemaakt in `/etc/sudoers.d/omsagent` . Hiermee wordt gemachtigd `omsagent` om de syslog- en omsagent-daemons opnieuw op te starten. Als sudo *include-instructies* niet worden ondersteund in de geïnstalleerde versie van sudo, worden deze vermeldingen naar `/etc/sudoers` geschreven.
* De syslog-configuratie wordt gewijzigd om een subset van gebeurtenissen door te geven aan de agent. Zie [Syslog-gegevensverzameling configureren voor meer informatie.](data-sources-syslog.md)

Op een bewaakte Linux-computer wordt de agent vermeld als `omsagent` . `omsconfig` is de Log Analytics-agent voor Linux-configuratieagent die elke vijf minuten zoekt naar een nieuwe configuratie aan de portalzijde. De nieuwe en bijgewerkte configuratie wordt toegepast op de agentconfiguratiebestanden die zich bevinden in `/etc/opt/microsoft/omsagent/conf/omsagent.conf` .

## <a name="install-the-agent-using-wrapper-script"></a>De agent installeren met behulp van een wrapper-script

Met de volgende stappen configureert u de installatie van de agent voor Log Analytics in Azure en Azure Government Cloud met behulp van het wrapperscript voor Linux-computers die rechtstreeks of via een proxyserver kunnen communiceren om de agent te downloaden die wordt gehost op GitHub en de agent te installeren.  

Als uw Linux-computer via een proxyserver moet communiceren met Log Analytics, kan deze configuratie worden opgegeven op de opdrachtregel door op te `-p [protocol://][user:password@]proxyhost[:port]` geven. De *protocol-eigenschap* accepteert of en de `http` eigenschap `https` *proxyhost* accepteert een fully qualified domain name of IP-adres van de proxyserver. 

Bijvoorbeeld: `https://proxy01.contoso.com:30443`

Als verificatie is vereist in beide gevallen, moet u de gebruikersnaam en het wachtwoord opgeven. Bijvoorbeeld: `https://user01:password@proxy01.contoso.com:30443`

1. Als u de Linux-computer wilt configureren om verbinding te maken met een Log Analytics-werkruimte, moet u de volgende opdracht uitvoeren om de werkruimte-id en primaire sleutel op te geven. Met de volgende opdracht wordt de agent gedownload, de bijbehorende controlesom gevalideerd en de agent geïnstalleerd.
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    De volgende opdracht bevat de proxyparameter `-p` en een voorbeeld van de syntaxis wanneer verificatie vereist is voor de proxyserver:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Als u de Linux-computer wilt configureren om verbinding te maken met een Log Analytics-werkruimte in de Azure Government-cloud, voert u de volgende opdracht uit met de eerder gekopieerde werkruimte-id en primaire sleutel. Met de volgende opdracht wordt de agent gedownload, de bijbehorende controlesom gevalideerd en de agent geïnstalleerd. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    De volgende opdracht bevat de proxyparameter `-p` en een voorbeeld van de syntaxis wanneer verificatie vereist is voor de proxyserver:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://]<proxy user>:<proxy password>@<proxyhost>[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```
2. Start de agent opnieuw door de volgende opdracht uit te voeren: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 


## <a name="install-the-agent-manually"></a>De agent handmatig installeren

De Log Analytics-agent voor Linux wordt geleverd in een zelfuitpakkende en installeerbare shellscriptbundel. Deze bundel bevat Debian- en RPM-pakketten voor elk van de agentonderdelen en kan rechtstreeks worden geïnstalleerd of geëxtraheerd om de afzonderlijke pakketten op te halen. Er is één bundel beschikbaar voor x64 en één voor x86-architecturen. 

> [!NOTE]
> Voor Azure-VM's raden we u aan de agent op deze [VM-extensie](../../virtual-machines/extensions/oms-linux.md) voor Linux te installeren. 

1. [Download](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) de juiste bundel (x64 of x86) en breng deze over naar uw Linux-VM of fysieke computer met behulp van scp/sftp.

2. Installeer de bundel met behulp van het `--install` argument . Als u wilt onboarden naar een Log Analytics-werkruimte tijdens de installatie, geeft u de `-w <WorkspaceID>` parameters en op die u eerder hebt `-s <workspaceKey>` gekopieerd.

    >[!NOTE]
    >U moet het argument gebruiken als afhankelijke pakketten, zoals `--upgrade` omi, scx, omsconfig of de oudere versies ervan, zijn geïnstalleerd, zoals het geval is als de system Center Operations Manager-agent voor Linux al is geïnstalleerd. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Als u de Linux-agent wilt configureren voor het installeren van en verbinding maken met een Log Analytics-werkruimte via een Log Analytics-gateway, moet u de volgende opdracht uitvoeren om de parameters proxy, werkruimte-id en werkruimtesleutel op te geven. Deze configuratie kan worden opgegeven op de opdrachtregel door op te `-p [protocol://][user:password@]proxyhost[:port]` geven. De *eigenschap proxyhost* accepteert een volledig gekwalificeerde domeinnaam of een IP-adres van de Log Analytics-gatewayserver.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Als verificatie is vereist, moet u de gebruikersnaam en het wachtwoord opgeven. Bijvoorbeeld: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Als u de Linux-computer zo wilt configureren dat deze verbinding maakt met een Log Analytics-werkruimte in Azure Government cloud, geeft u de werkruimte-id en primaire sleutel op die u eerder hebt gekopieerd.

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Als u de agentpakketten wilt installeren en configureren om op een later tijdstip te rapporteren aan een specifieke Log Analytics-werkruimte, moet u de volgende opdracht uitvoeren:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Als u de agentpakketten uit de bundel wilt extraheren zonder de agent te installeren, moet u de volgende opdracht uitvoeren:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="upgrade-from-a-previous-release"></a>Upgraden van een vorige versie

Een upgrade van een vorige versie vanaf versie 1.0.0-47 wordt in elke release ondersteund. Voer de installatie uit met de `--upgrade` parameter om alle onderdelen van de agent bij te werken naar de nieuwste versie.

## <a name="cache-information"></a>Cachegegevens
Gegevens van de Log Analytics-agent voor Linux worden op de lokale computer in de cache opgeslagen op *%STATE_DIR_WS%/out_oms_common*.buffer* voordat ze naar de Azure Monitor. Aangepaste logboekgegevens worden gebufferd in *%STATE_DIR_WS%/out_oms_blob*.buffer*. Het pad kan voor sommige oplossingen en [gegevenstypen verschillen.](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=)

De agent probeert elke 20 seconden te uploaden. Als dit mislukt, wordt een exponentieel toenemende tijd gewacht totdat deze slaagt: 30 seconden vóór de tweede poging, 60 seconden vóór de derde, 120 seconden... en dus maximaal 16 minuten tussen nieuwe proberen totdat er weer verbinding wordt gemaakt. De agent zal maximaal 6 keer opnieuw proberen voor een bepaald deel van de gegevens voordat deze wordt verwijderd en naar de volgende wordt verplaatst. Dit gaat door totdat de agent opnieuw kan worden geüpload. Dit betekent dat gegevens maximaal 30 minuten kunnen worden gebufferd voordat ze worden verwijderd.

De standaardcachegrootte is 10 MB, maar kan worden gewijzigd in het [bestand omsagent.conf.](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf)


## <a name="next-steps"></a>Volgende stappen

- Zie [De Log Analytics-agent](agent-manage.md) voor Windows en Linux beheren en onderhouden voor meer informatie over het opnieuw configureren, upgraden of verwijderen van de agent van de virtuele machine.

- Zie [Problemen met de Linux-agent oplossen](agent-linux-troubleshoot.md) als u problemen ondervindt tijdens het installeren of beheren van de agent.
