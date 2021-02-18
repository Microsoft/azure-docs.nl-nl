---
title: Syslog-gegevens bronnen verzamelen met Log Analytics agent in Azure Monitor
description: Syslog is een protocol voor gebeurtenis registratie dat algemeen is voor Linux. In dit artikel wordt beschreven hoe u een verzameling van syslog-berichten configureert in Log Analytics en Details van de records die ze maken.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/21/2020
ms.openlocfilehash: 2d7406c1e801a07f10342c47e7334e6a12bfd449
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610301"
---
# <a name="collect-syslog-data-sources-with-log-analytics-agent"></a>Syslog-gegevens bronnen verzamelen met Log Analytics agent
Syslog is een protocol voor gebeurtenis registratie dat algemeen is voor Linux. Toepassingen zullen berichten verzenden die kunnen worden opgeslagen op de lokale computer of worden geleverd aan een syslog-Collector. Wanneer de Log Analytics-agent voor Linux is geïnstalleerd, wordt de lokale syslog-daemon geconfigureerd voor het door sturen van berichten naar de agent. De agent verzendt het bericht vervolgens naar Azure Monitor waar een corresponderende record wordt gemaakt.  

> [!IMPORTANT]
> Dit artikel heeft betrekking op het verzamelen van syslog-gebeurtenissen met de [log Analytics-agent](../platform/log-analytics-agent.md) , een van de agents die door Azure monitor worden gebruikt. Andere agents verzamelen verschillende gegevens en worden anders geconfigureerd. Zie [overzicht van Azure monitor agents](../agents/agents-overview.md) voor een lijst met beschik bare agents en de gegevens die ze kunnen verzamelen.

> [!NOTE]
> Azure Monitor ondersteunt het verzamelen van berichten die worden verzonden door rsyslog of syslog-ng, waarbij rsyslog de standaard-daemon is. De standaard syslog-daemon op versie 5 van Red Hat Enterprise Linux, CentOS en Oracle Linux versie (sysklog) wordt niet ondersteund voor de verzameling syslog-gebeurtenissen. Als u syslog-gegevens uit deze versie van deze distributies wilt verzamelen, moet de [rsyslog-daemon](http://rsyslog.com) worden geïnstalleerd en geconfigureerd om sysklog te vervangen.
>
>

![Syslog-verzameling](media/data-sources-syslog/overview.png)

De volgende faciliteiten worden ondersteund met de syslog-Collector:

* spatiëren
* gebruiker
* mail
* daemon
* dienst
* syslog
* beschikken
* news
* uucp
* cron
* authpriv
* ftp
* local0-local7

Voor elke andere faciliteit [configureert u een gegevens bron met aangepaste logboeken](data-sources-custom-logs.md) in azure monitor.
 
## <a name="configuring-syslog"></a>Syslog configureren
In de Log Analytics-agent voor Linux worden alleen gebeurtenissen verzameld met de faciliteiten en ernst die zijn opgegeven in de configuratie. U kunt syslog configureren via de Azure Portal of door configuratie bestanden te beheren in uw Linux-agents.

### <a name="configure-syslog-in-the-azure-portal"></a>Syslog configureren in de Azure Portal
Configureer syslog vanuit het [menu Data in geavanceerde instellingen](../agents/agent-data-sources.md#configuring-data-sources) voor de werk ruimte log Analytics. Deze configuratie wordt op elke Linux-agent bezorgd bij het configuratie bestand.

U kunt een nieuwe faciliteit toevoegen door eerst de optie **op de onderstaande configuratie Toep assen op mijn machines te** selecteren en vervolgens de naam te typen en te klikken **+** . Voor elke faciliteit worden alleen berichten met de geselecteerde Ernst verzameld.  Controleer de ernst van de specifieke faciliteit die u wilt verzamelen. U kunt geen aanvullende criteria opgeven om berichten te filteren.

![Syslog configureren](media/data-sources-syslog/configure.png)

Standaard worden alle configuratie wijzigingen automatisch naar alle agents gepusht. Als u syslog hand matig op elke Linux-agent wilt configureren, schakelt u het selectie vakje de *onderstaande configuratie Toep assen op mijn machines* uit.

### <a name="configure-syslog-on-linux-agent"></a>Syslog op Linux-agent configureren
Wanneer de [log Analytics-agent is geïnstalleerd op een Linux-client](../learn/quick-collect-linux-computer.md), wordt een standaard-syslog-configuratie bestand geïnstalleerd waarmee de faciliteit en ernst van de verzamelde berichten worden gedefinieerd. U kunt dit bestand wijzigen om de configuratie te wijzigen. Het configuratie bestand verschilt, afhankelijk van de syslog-daemon die de-client heeft geïnstalleerd.

> [!NOTE]
> Als u de syslog-configuratie bewerkt, moet u de syslog-daemon opnieuw opstarten om de wijzigingen van kracht te laten worden.
>
>

#### <a name="rsyslog"></a>rsyslog
Het configuratie bestand voor rsyslog bevindt zich op **/etc/rsyslog.d/95-omsagent.conf**. De standaard inhoud wordt hieronder weer gegeven. Hiermee worden syslog-berichten verzameld die zijn verzonden vanuit de lokale agent voor alle faciliteiten met een waarschuwings niveau of hoger.

```config
kern.warning       @127.0.0.1:25224
user.warning       @127.0.0.1:25224
daemon.warning     @127.0.0.1:25224
auth.warning       @127.0.0.1:25224
syslog.warning     @127.0.0.1:25224
uucp.warning       @127.0.0.1:25224
authpriv.warning   @127.0.0.1:25224
ftp.warning        @127.0.0.1:25224
cron.warning       @127.0.0.1:25224
local0.warning     @127.0.0.1:25224
local1.warning     @127.0.0.1:25224
local2.warning     @127.0.0.1:25224
local3.warning     @127.0.0.1:25224
local4.warning     @127.0.0.1:25224
local5.warning     @127.0.0.1:25224
local6.warning     @127.0.0.1:25224
local7.warning     @127.0.0.1:25224
```

U kunt een faciliteit verwijderen door de sectie van het configuratie bestand te verwijderen. U kunt de ernst die voor een bepaalde faciliteit worden verzameld, beperken door de vermelding van die faciliteit te wijzigen. Als u bijvoorbeeld de gebruikers faciliteit wilt beperken tot berichten met een Ernst fout of hoger, wijzigt u de regel van het configuratie bestand als volgt:

```config
user.error    @127.0.0.1:25224
```

#### <a name="syslog-ng"></a>syslog-aardgas
Het configuratie bestand voor syslog-aardgas is locatie op **/etc/syslog-ng/syslog-ng.conf**.  De standaard inhoud wordt hieronder weer gegeven. Hiermee worden syslog-berichten verzameld die zijn verzonden vanuit de lokale agent voor alle faciliteiten en alle ernst.   

```config
#
# Warnings (except iptables) in one file:
#
destination warn { file("/var/log/warn" fsync(yes)); };
log { source(src); filter(f_warn); destination(warn); };

#OMS_Destination
destination d_oms { udp("127.0.0.1" port(25224)); };

#OMS_facility = auth
filter f_auth_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(auth); };
log { source(src); filter(f_auth_oms); destination(d_oms); };

#OMS_facility = authpriv
filter f_authpriv_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(authpriv); };
log { source(src); filter(f_authpriv_oms); destination(d_oms); };

#OMS_facility = cron
filter f_cron_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(cron); };
log { source(src); filter(f_cron_oms); destination(d_oms); };

#OMS_facility = daemon
filter f_daemon_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(daemon); };
log { source(src); filter(f_daemon_oms); destination(d_oms); };

#OMS_facility = kern
filter f_kern_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(kern); };
log { source(src); filter(f_kern_oms); destination(d_oms); };

#OMS_facility = local0
filter f_local0_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local0); };
log { source(src); filter(f_local0_oms); destination(d_oms); };

#OMS_facility = local1
filter f_local1_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(local1); };
log { source(src); filter(f_local1_oms); destination(d_oms); };

#OMS_facility = mail
filter f_mail_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(mail); };
log { source(src); filter(f_mail_oms); destination(d_oms); };

#OMS_facility = syslog
filter f_syslog_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(syslog); };
log { source(src); filter(f_syslog_oms); destination(d_oms); };

#OMS_facility = user
filter f_user_oms { level(alert,crit,debug,emerg,err,info,notice,warning) and facility(user); };
log { source(src); filter(f_user_oms); destination(d_oms); };
```

U kunt een faciliteit verwijderen door de sectie van het configuratie bestand te verwijderen. U kunt de ernst van een bepaalde faciliteit beperken door deze te verwijderen uit de lijst.  Als u bijvoorbeeld wilt beperken dat de gebruikers faciliteit alleen waarschuwingen en kritieke berichten ontvangt, wijzigt u deze sectie van het configuratie bestand in het volgende:

```config
#OMS_facility = user
filter f_user_oms { level(alert,crit) and facility(user); };
log { source(src); filter(f_user_oms); destination(d_oms); };
```

### <a name="collecting-data-from-additional-syslog-ports"></a>Gegevens verzamelen uit extra syslog-poorten
De Log Analytics-agent luistert naar syslog-berichten op de lokale client op poort 25224.  Wanneer de agent is geïnstalleerd, wordt een standaard syslog-configuratie toegepast en gevonden op de volgende locatie:

* Rsyslog `/etc/rsyslog.d/95-omsagent.conf`
* Syslog-aardgas: `/etc/syslog-ng/syslog-ng.conf`

U kunt het poort nummer wijzigen door twee configuratie bestanden te maken: een gevloeiendeerd configuratie bestand en een rsyslog-of-syslog-aardgas-bestand, afhankelijk van de syslog-daemon die u hebt geïnstalleerd.  

* Het gefluente configuratie bestand moet een nieuw bestand zijn in: `/etc/opt/microsoft/omsagent/conf/omsagent.d` en de waarde in de **poort** vermelding vervangen door uw aangepaste poort nummer.

    ```xml
    <source>
        type syslog
        port %SYSLOG_PORT%
        bind 127.0.0.1
        protocol_type udp
        tag oms.syslog
    </source>
    <filter oms.syslog.**>
        type filter_syslog
    ```

* Voor rsyslog moet u een nieuw configuratie bestand maken in: `/etc/rsyslog.d/` en de waarde% SYSLOG_PORT% vervangen door uw aangepaste poort nummer.  

    > [!NOTE]
    > Als u deze waarde in het configuratie bestand wijzigt `95-omsagent.conf` , wordt deze overschreven wanneer de agent een standaard configuratie toepast.
    >

    ```config
    # OMS Syslog collection for workspace %WORKSPACE_ID%
    kern.warning              @127.0.0.1:%SYSLOG_PORT%
    user.warning              @127.0.0.1:%SYSLOG_PORT%
    daemon.warning            @127.0.0.1:%SYSLOG_PORT%
    auth.warning              @127.0.0.1:%SYSLOG_PORT%
    ```

* Het syslog-ng-config moet worden gewijzigd door de hieronder weer gegeven voorbeeld configuratie te kopiëren en de aangepaste gewijzigde instellingen toe te voegen aan het einde van het syslog-ng. conf-configuratie bestand dat zich bevindt in `/etc/syslog-ng/` . Gebruik **niet** het standaard label **% WORKSPACE_ID% _oms** of **% WORKSPACE_ID_OMS**, Definieer een aangepast label om uw wijzigingen te onderscheiden.  

    > [!NOTE]
    > Als u de standaard waarden in het configuratie bestand wijzigt, worden deze overschreven wanneer de agent een standaard configuratie toepast.
    >

    ```config
    filter f_custom_filter { level(warning) and facility(auth; };
    destination d_custom_dest { udp("127.0.0.1" port(%SYSLOG_PORT%)); };
    log { source(s_src); filter(f_custom_filter); destination(d_custom_dest); };
    ```

Nadat de wijzigingen zijn voltooid, moet de syslog en de Log Analytics Agent-service opnieuw worden gestart om ervoor te zorgen dat de configuratie wijzigingen van kracht worden.   

## <a name="syslog-record-properties"></a>Eigenschappen van syslog-record
Syslog-records hebben het type **syslog** en hebben de eigenschappen in de volgende tabel.

| Eigenschap | Beschrijving |
|:--- |:--- |
| Computer |Computer waarop de gebeurtenis is verzameld. |
| Mogelijkheid |Hiermee definieert u het deel van het systeem dat het bericht heeft gegenereerd. |
| HostIP |Het IP-adres van het systeem dat het bericht verzendt. |
| HostName |De naam van het systeem dat het bericht verzendt. |
| SeverityLevel |Het Ernst niveau van de gebeurtenis. |
| SyslogMessage |De tekst van het bericht. |
| ProcessID |De ID van het proces dat het bericht heeft gegenereerd. |
| EventTime |De datum en tijd waarop de gebeurtenis is gegenereerd. |

## <a name="log-queries-with-syslog-records"></a>Query's registreren met syslog-records
De volgende tabel bevat verschillende voor beelden van logboek query's waarmee syslog-records worden opgehaald.

| Query’s uitvoeren | Description |
|:--- |:--- |
| Syslog |Alle syslogs. |
| Syslog &#124; waarbij SeverityLevel = = "Error" |Alle syslog-records met de ernst van de fout. |
| Syslog &#124; samenvat AggregatedValue = Count () door computer |Aantal syslog-records per computer. |
| Syslog &#124; samenvatte AggregatedValue = Count () door faciliteit |Aantal syslog-records per faciliteit. |

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [logboek query's](../log-query/log-query-overview.md) voor het analyseren van de gegevens die zijn verzameld uit gegevens bronnen en oplossingen.
* Gebruik [aangepaste velden](./../platform/custom-fields.md) voor het parseren van gegevens van syslog-records in afzonderlijke velden.
* [Configureer Linux-agents](../learn/quick-collect-linux-computer.md) om andere typen gegevens te verzamelen.

