---
title: Log Analytics-agent installeren op Linux-computers
description: In dit artikel wordt beschreven hoe u verbinding maakt tussen Linux-computers die worden gehost in andere Clouds of on-premises naar Azure Monitor met de Log Analytics-agent voor Linux.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2020
ms.openlocfilehash: 5171cefdb82b958ae8148ff63f1daef5f67916c6
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105044954"
---
# <a name="install-log-analytics-agent-on-linux-computers"></a>Log Analytics-agent installeren op Linux-computers
In dit artikel vindt u informatie over het installeren van de Log Analytics-agent op Linux-computers met behulp van de volgende methoden:

* [Installeer de agent voor Linux met behulp van een wrapper-script](#install-the-agent-using-wrapper-script) dat wordt gehost op github. Dit is de aanbevolen methode om de agent te installeren en bij te werken wanneer de computer verbinding heeft met internet, rechtstreeks of via een proxy server.
* De agent [hand matig downloaden en installeren](#install-the-agent-manually) . Dit is vereist wanneer de Linux-computer geen toegang tot internet heeft en communiceert met Azure Monitor of Azure Automation via de [log Analytics gateway](./gateway.md). 

>[!IMPORTANT]
> De installatie methoden die in dit artikel worden beschreven, worden doorgaans gebruikt voor virtuele machines, on-premises of in andere Clouds. Zie [installatie opties](./log-analytics-agent.md#installation-options) voor efficiëntere opties die u kunt gebruiken voor virtuele machines van Azure.



## <a name="supported-operating-systems"></a>Ondersteunde besturingssystemen

Zie [overzicht van Azure monitor agents](agents-overview.md#supported-operating-systems) voor een lijst met Linux-distributies die worden ondersteund door de log Analytics-agent.

>[!NOTE]
>OpenSSL 1.1.0 wordt alleen ondersteund op x86_x64 platforms (64-bits) en OpenSSL die ouder zijn dan 1. x niet op elk platform wordt ondersteund.

>[!NOTE]
>Het uitvoeren van de Log Analytics Linux-agent in containers wordt niet ondersteund. Als u containers wilt bewaken, moet u gebruikmaken van de [container bewakings oplossing](../containers/containers.md) voor docker-hosts of [container Insights](../containers/container-insights-overview.md) voor Kubernetes.

Vanaf versies die na augustus 2018 zijn uitgebracht, maken we de volgende wijzigingen in ons ondersteunings model:  

* Alleen de server versies worden ondersteund, niet van de client.  
* Richt u op de ondersteuning van een van de door [Azure Linux geviseerde distributies](../../virtual-machines/linux/endorsed-distros.md). Houd er rekening mee dat er enige vertraging is tussen een nieuwe distributie/versie die door Azure Linux wordt goedgekeurd en wordt ondersteund voor de Log Analytics Linux-agent.
* Alle kleine releases worden ondersteund voor elke primaire versie die wordt vermeld.
* Versies die de ondersteunings datum van de fabrikant hebben door gegeven, worden niet ondersteund.
* Alleen VM-installatie kopieën ondersteunen; containers, zelfs degene die zijn afgeleid van officiële distributie-publicatie kopieën, worden niet ondersteund.
* Nieuwe versies van AMI worden niet ondersteund.  
* Alleen versies met OpenSSL 1. x worden standaard ondersteund.

>[!NOTE]
>Als u gebruikmaakt van een distributie of-versie die momenteel niet wordt ondersteund en niet is afgestemd op ons ondersteunings model, wordt u aangeraden deze opslag plaats te splitsen en te bevestigen dat micro soft support geen ondersteuning biedt voor gevorkeerde agent versies.

### <a name="python-requirement"></a>Python-vereiste

Vanaf agent versie 1.13.27 ondersteunt de Linux-agent zowel python 2 als 3. We raden altijd aan de nieuwste agent te gebruiken. 

Als u een oudere versie van de agent gebruikt, moet de virtuele machine standaard Python 2 gebruiken. Als uw virtuele machine gebruikmaakt van een distributie die niet standaard Python 2 bevat, moet u deze installeren. Met de volgende voorbeeld opdrachten wordt python 2 op verschillende distributies geïnstalleerd.

 - Red Hat, CentOS, Oracle: `yum install -y python2`
 - Ubuntu, Debian: `apt-get install -y python2`
 - SuSE `zypper install -y python2`

Het uitvoer bare bestand van python2 moet zijn alias voor *python*. Hier volgt een methode die u kunt gebruiken om deze alias in te stellen:

1. Voer de volgende opdracht uit om alle bestaande aliassen te verwijderen.
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. Voer de volgende opdracht uit om de alias te maken.

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

## <a name="supported-linux-hardening"></a>Ondersteunde Linux-beveiliging
De OMS-agent heeft beperkte aanpassings ondersteuning voor Linux. 

De volgende worden momenteel ondersteund: 
- FIPs

De volgende overwegingen worden in aanmerking genomen, maar nog niet ondersteund:
- CIS
- SELINUX

Andere methoden voor beveiliging en aanpassing worden niet ondersteund en worden niet gepland voor de OMS-agent.  

## <a name="agent-prerequisites"></a>Agent vereisten

De volgende tabel geeft een overzicht van de pakketten die vereist zijn voor de [ondersteunde Linux-distributies](#supported-operating-systems) waarop de agent wordt geïnstalleerd.

|Vereist pakket |Beschrijving |Minimale versie |
|-----------------|------------|----------------|
|Glibc |    GNU C-bibliotheek | 2.5-12 
|Openssl    | OpenSSL-bibliotheken | 1.0. x of 1.1. x |
|Curl | Krul webclient | 7.15.5 |
|Python | | 2,7 of 3.6 +
|Python-ctypes | | 
|PAM | Pluggable Authentication Modules | | 

>[!NOTE]
>Rsyslog of syslog-ng is vereist voor het verzamelen van syslog-berichten. De standaard syslog-daemon op versie 5 van Red Hat Enterprise Linux, CentOS en Oracle Linux versie (sysklog) wordt niet ondersteund voor de verzameling syslog-gebeurtenissen. Als u syslog-gegevens uit deze versie van deze distributies wilt verzamelen, moet de rsyslog-daemon worden geïnstalleerd en geconfigureerd om sysklog te vervangen.

## <a name="network-requirements"></a>Netwerkvereisten
Zie [overzicht van log Analytics agent](./log-analytics-agent.md#network-requirements) voor de netwerk vereisten voor de Linux-agent.

## <a name="agent-install-package"></a>Agent installatie pakket

De Log Analytics-agent voor Linux bestaat uit meerdere pakketten. Het release bestand bevat de volgende pakketten, die beschikbaar zijn door de shell bundel uit te voeren met de `--extract` para meter:

**Pakket** | **Versie** | **Beschrijving**
----------- | ----------- | --------------
omsagent | 1.13.9 | De Log Analytics-agent voor Linux
omsconfig | 1.1.1 | Configuratie agent voor de Log Analytics-agent
Omi | 1.6.4 | Open Management Infrastructure (OMI)--een Lightweight CIM-server. *Houd er rekening mee dat OMI een cron-taak moet uitvoeren die nodig is voor de werking van de service*
SCx | 1.6.4 | OMI CIM-providers voor metrische gegevens over de prestaties van het besturings systeem
Apache-cimprov | 1.0.1 | Bewakings provider van Apache HTTP-server prestaties voor OMI. Alleen geïnstalleerd als de Apache HTTP-server wordt gedetecteerd.
MySQL-cimprov | 1.0.1 | MySQL-server prestatie bewakings provider voor OMI. Alleen geïnstalleerd als MySQL/MariaDB-server wordt gedetecteerd.
docker-cimprov | 1.0.0 | Docker-provider voor OMI. Alleen geïnstalleerd als docker wordt gedetecteerd.

### <a name="agent-installation-details"></a>Details van de agent installatie

Na de installatie van de Log Analytics agent voor Linux-pakketten, worden de volgende aanvullende configuratie wijzigingen van het hele systeem toegepast. Deze artefacten worden verwijderd wanneer u het omsagent-pakket verwijdert.

* Er is een niet-bevoegde gebruiker met de naam: `omsagent` gemaakt. De daemon wordt uitgevoerd onder deze referentie. 
* Er wordt een sudo- *include* -bestand gemaakt in `/etc/sudoers.d/omsagent` . Hiermee wordt geautoriseerd `omsagent` om de syslog-en omsagent-daemons opnieuw te starten. Als sudo *bevatten* instructies worden niet ondersteund in de geïnstalleerde versie van sudo, worden deze vermeldingen geschreven naar `/etc/sudoers` .
* De syslog-configuratie is gewijzigd om een subset van gebeurtenissen naar de agent door te sturen. Zie [syslog-gegevens verzameling configureren](data-sources-syslog.md)voor meer informatie.

Op een bewaakte Linux-computer wordt de agent vermeld als `omsagent` . `omsconfig` is de Log Analytics agent voor Linux-configuratie agent die elke vijf minuten zoekt naar de nieuwe configuratie van de portal-zijde. De nieuwe en bijgewerkte configuratie wordt toegepast op de agent configuratie bestanden die zich bevinden op `/etc/opt/microsoft/omsagent/conf/omsagent.conf` .

## <a name="install-the-agent-using-wrapper-script"></a>De agent installeren met behulp van het wrapper-script

Met de volgende stappen configureert u de installatie van de agent voor Log Analytics in Azure en Azure Government Cloud met het wrapper-script voor Linux-computers die rechtstreeks kunnen communiceren of via een proxy server om de agent te downloaden die wordt gehost op GitHub en de agent te installeren.  

Als uw Linux-computer moet communiceren via een proxy server om Log Analytics, kan deze configuratie worden opgegeven op de opdracht regel met behulp van `-p [protocol://][user:password@]proxyhost[:port]` . De *protocol* eigenschap accepteert `http` of `https` , en de eigenschap *proxyhost* accepteert een Fully Qualified Domain name of IP-adres van de proxy server. 

Bijvoorbeeld: `https://proxy01.contoso.com:30443`

Als verificatie vereist is in beide gevallen, moet u de gebruikers naam en het wacht woord opgeven. Bijvoorbeeld: `https://user01:password@proxy01.contoso.com:30443`

1. Als u de Linux-computer wilt configureren om verbinding te maken met een Log Analytics werkruimte, voert u de volgende opdracht uit om de werk ruimte-ID en de primaire sleutel op te geven. Met de volgende opdracht wordt de agent gedownload, de bijbehorende controlesom gevalideerd en de agent geïnstalleerd.
    
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

De Log Analytics-agent voor Linux bevindt zich in een zelfextraherende shell-script bundel en kan worden geïnstalleerd. Deze bundel bevat Debian-en RPM-pakketten voor elk van de agent onderdelen en kan rechtstreeks of geëxtraheerd worden geïnstalleerd om de afzonderlijke pakketten op te halen. Er is één bundel voor x64 en één voor x86-architecturen. 

> [!NOTE]
> Voor virtuele Azure-machines wordt u aangeraden de agent te installeren met behulp van de [Azure log Analytics VM-extensie](../../virtual-machines/extensions/oms-linux.md) voor Linux. 

1. [Down load](https://github.com/microsoft/OMS-Agent-for-Linux#azure-install-guide) en breng de juiste bundel (x64 of x86) om naar uw virtuele Linux-machine of fysieke computer met SCP/SFTP.

2. Installeer de bundel met behulp van het `--install` argument. Als u tijdens de installatie wilt voorbereiden op een Log Analytics-werk ruimte, geeft u de `-w <WorkspaceID>` `-s <workspaceKey>` para meters en op die eerder zijn gekopieerd.

    >[!NOTE]
    >U moet het argument gebruiken `--upgrade` als er afhankelijke pakketten, zoals Omi, SCx, omsconfig of hun oudere versies, worden geïnstalleerd. Dit is het geval als de System Center Operations Manager-agent voor Linux al is geïnstalleerd. 

    ```
    sudo sh ./omsagent-*.universal.x64.sh --install -w <workspace id> -s <shared key>
    ```

3. Als u de Linux-agent wilt configureren om een Log Analytics-werk ruimte te installeren en er verbinding mee te maken via een Log Analytics gateway, voert u de volgende opdracht uit om de para meters proxy, werk ruimte-ID en werkruimte sleutel op te geven. Deze configuratie kan worden opgegeven op de opdracht regel met behulp van `-p [protocol://][user:password@]proxyhost[:port]` . De eigenschap *proxyhost* accepteert een Fully Qualified Domain name of IP-adres van de log Analytics-Gateway server.  

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

    Als verificatie is vereist, moet u de gebruikers naam en het wacht woord opgeven. Bijvoorbeeld: 
    
    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -p https://<proxy user>:<proxy password>@<proxy address>:<proxy port> -w <workspace id> -s <shared key>
    ```

4. Als u de Linux-computer wilt configureren om verbinding te maken met een Log Analytics-werk ruimte in Azure Government Cloud, voert u de volgende opdracht uit om de werk ruimte-ID en de primaire sleutel die u eerder hebt gekopieerd,

    ```
    sudo sh ./omsagent-*.universal.x64.sh --upgrade -w <workspace id> -s <shared key> -d opinsights.azure.us
    ```

Voer de volgende opdracht uit als u de agent pakketten wilt installeren en zo wilt configureren dat deze op een later tijdstip worden gerapporteerd aan een specifieke Log Analytics-werk ruimte:

```
sudo sh ./omsagent-*.universal.x64.sh --upgrade
```

Als u de agent pakketten wilt uitpakken uit de bundel zonder de agent te installeren, voert u de volgende opdracht uit:

```
sudo sh ./omsagent-*.universal.x64.sh --extract
```

## <a name="upgrade-from-a-previous-release"></a>Upgrade uitvoeren van een eerdere versie

Een upgrade uitvoeren van een eerdere versie, te beginnen met versie 1.0.0-47, wordt in elke release ondersteund. Voer de installatie uit met de `--upgrade` para meter om alle onderdelen van de agent bij te werken naar de meest recente versie.

## <a name="cache-information"></a>Cache gegevens
Gegevens van de Log Analytics-agent voor Linux bevinden zich in de cache op de lokale computer op *% STATE_DIR_WS%/out_oms_common*. buffer * voordat deze wordt verzonden naar Azure monitor. Aangepaste logboek gegevens worden gebufferd in *% STATE_DIR_WS%/out_oms_blob*. buffer *. Het pad kan verschillen voor sommige [oplossingen en gegevens typen](https://github.com/microsoft/OMS-Agent-for-Linux/search?utf8=%E2%9C%93&q=+buffer_path&type=).

De agent probeert om de 20 seconden te uploaden. Als dit mislukt, wordt een exponentieel toenemende tijds duur gewacht totdat deze slaagt: 30 seconden vóór de tweede poging, 60 seconden vóór de derde, 120 seconden... en tot Maxi maal 16 minuten tussen nieuwe pogingen tot de verbinding opnieuw tot stand is gebracht. De agent zal Maxi maal zes keer opnieuw proberen voor een gegeven segment van gegevens voordat u het volgende kunt verwijderen en verplaatsen. Dit wordt vervolgd totdat de agent opnieuw kan worden geüpload. Dit betekent dat gegevens kunnen worden gebufferd tot ongeveer 30 minuten voordat ze worden verwijderd.

De standaard cache grootte is 10 MB, maar kan worden gewijzigd in het [bestand omsagent. conf](https://github.com/microsoft/OMS-Agent-for-Linux/blob/e2239a0714ae5ab5feddcc48aa7a4c4f971417d4/installer/conf/omsagent.conf).


## <a name="next-steps"></a>Volgende stappen

- Raadpleeg [de log Analytics-agent voor Windows en Linux beheren en onderhouden voor](agent-manage.md) meer informatie over het opnieuw configureren, bijwerken of verwijderen van de agent van de virtuele machine.

- Raadpleeg [problemen met de Linux-agent oplossen](agent-linux-troubleshoot.md) als u problemen ondervindt tijdens het installeren of beheren van de agent.
