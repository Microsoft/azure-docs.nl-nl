---
title: De logboek-doorstuur server implementeren om CEF-gegevens te verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het implementeren van de agent voor het verbinden van CEF-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2021
ms.author: yelevin
ms.openlocfilehash: ee28837d3e687d78b645a1ab18a9add1f8e57fcc
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771257"
---
# <a name="step-1-deploy-the-log-forwarder"></a>Stap 1: de logboek-doorstuur server implementeren


In deze stap wijst en configureert u de Linux-computer waarmee de logboeken worden doorgestuurd van uw beveiligings oplossing naar uw Azure Sentinel-werk ruimte. Deze computer kan een fysieke of virtuele machine in uw on-premises omgeving, een Azure-VM of een VM in een andere cloud zijn. Met behulp van de opgegeven koppeling voert u een script uit op de aangewezen computer die de volgende taken uitvoert:

- Installeert de Log Analytics agent voor Linux (ook wel bekend als de OMS-agent) en configureert deze voor de volgende doel einden:
    - Luis teren naar CEF-berichten van de ingebouwde Linux syslog-daemon op TCP-poort 25226
    - de berichten veilig via TLS verzenden naar uw Azure Sentinel-werk ruimte, waar ze worden geparseerd en verrijkt

- Hiermee configureert u de ingebouwde Linux syslog-daemon (rsyslog. d/syslog-ng) voor de volgende doel einden:
    - Luis teren naar syslog-berichten van uw beveiligings oplossingen op TCP-poort 514
    - door sturen van alleen de berichten die worden geïdentificeerd als CEF aan de Log Analytics-agent op localhost via TCP-poort 25226
 
## <a name="prerequisites"></a>Vereisten

- U moet een verhoogde machtigingen (sudo) hebben op de aangewezen Linux-machine.

- **Python 2,7** of **3** moet zijn geïnstalleerd op de Linux-computer.<br>Gebruik de `python -version` opdracht om te controleren.

- De Linux-machine mag niet worden verbonden met een Azure-werk ruimte voordat u de Log Analytics-Agent installeert.

- Uw Linux-machine moet mini maal **4 CPU-kernen en 8 GB RAM-geheugen** hebben.

    > [!NOTE]
    > - Een single-doorstuur machine voor logboek registratie met behulp van de **rsyslog** -daemon heeft een ondersteunde capaciteit van **Maxi maal 8500 gebeurtenissen per seconde (EPS)** verzameld.

- Mogelijk hebt u de werk ruimte-ID en de primaire sleutel voor de werk ruimte op een bepaald moment in dit proces nodig. U vindt deze in de werkruimte resource onder **agent beheer**.

## <a name="run-the-deployment-script"></a>Het implementatiescript uitvoeren
 
1. Klik in het navigatie menu van de Azure-Sentinel op **Data connectors**. Klik in de lijst met connectors op de tegel **algemene gebeurtenis indeling (CEF)** en klik vervolgens op de knop **pagina connector openen** op de rechter benedenhoek. 

1. Onder **1,2 Installeer de CEF-Collector op de Linux-computer**, kopieer de koppeling die u hebt gemaakt onder **Voer het volgende script uit om de CEF-Collector te installeren en toe te passen**, of uit de onderstaande tekst (waarbij de werk ruimte-id en de primaire sleutel worden toegepast in plaats van de tijdelijke aanduidingen):

    ```bash
    sudo wget -O cef_installer.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/cef_installer.py&&sudo python cef_installer.py [WorkspaceID] [Workspace Primary Key]
    ```

1. Zorg ervoor dat het script wordt uitgevoerd en controleer of er geen fout-of waarschuwings berichten worden weer gegeven.
    - Mogelijk wordt er een bericht weer gegeven waarin u wordt gevraagd een opdracht uit te voeren om een probleem met de toewijzing van het *computer* veld te corrigeren. Zie de [uitleg in het implementatie script](#mapping-command) voor meer informatie.

1. Ga verder met [stap 2: uw beveiligings oplossing configureren voor het door sturen van CEF-berichten](connect-cef-solution-config.md).


> [!NOTE]
> **Dezelfde computer gebruiken voor het door sturen van zowel normale syslog- *als* CEF-berichten**
>
> Als u van plan bent deze doorstuur server voor logboeken te gebruiken voor het door sturen van [syslog-berichten](connect-syslog.md) en CEF, kunt u het dupliceren van gebeurtenissen naar de tabellen syslog en CommonSecurityLog vermijden:
>
> 1. Op elke bron machine die logboeken naar de doorstuur server verzendt in CEF-indeling, moet u het syslog-configuratie bestand bewerken om de faciliteiten te verwijderen die worden gebruikt om CEF-berichten te verzenden. Op deze manier worden de functies die in CEF worden verzonden, ook niet in syslog verzonden. Zie [syslog op Linux-agent configureren](../azure-monitor/agents/data-sources-syslog.md#configure-syslog-on-linux-agent) voor gedetailleerde instructies over hoe u dit doet.
>
> 1. U moet de volgende opdracht uitvoeren op die computers om de synchronisatie van de agent met de syslog-configuratie in azure Sentinel uit te scha kelen. Dit zorgt ervoor dat de configuratie wijziging die u in de vorige stap hebt aangebracht, niet wordt overschreven.<br>
> `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable'`

> [!NOTE]
> **De bron van het veld TimeGenerated wijzigen**
>
> - Standaard vult de Log Analytics agent het veld *TimeGenerated* in het schema in met de tijd dat de agent de gebeurtenis van de syslog-daemon heeft ontvangen. Als gevolg hiervan wordt de tijd waarop de gebeurtenis is gegenereerd in het bronsysteem, niet opgeslagen in Azure Sentinel.
>
> - U kunt echter de volgende opdracht uitvoeren, zodat het script wordt gedownload en uitgevoerd `TimeGenerated.py` . Met dit script wordt de Log Analytics-agent zo geconfigureerd dat het veld *TimeGenerated* wordt ingevuld met de oorspronkelijke tijd van de gebeurtenis op het bron systeem, in plaats van de tijd die door de agent is ontvangen.
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="deployment-script-explained"></a>Uitleg bij implementatie script

Hier volgt een beschrijving van de opdracht-voor-opdracht van de acties van het implementatie script.

Kies een syslog-daemon om de juiste beschrijving te bekijken.

# <a name="rsyslog-daemon"></a>[rsyslog-daemon](#tab/rsyslog)

1. **De Log Analytics-agent downloaden en installeren:**

    - Hiermee wordt het installatie script gedownload voor de Linux-agent van Log Analytics (OMS).

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Hiermee wordt de Log Analytics-agent geïnstalleerd.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **De configuratie van de Log Analytics-agent instellen om te Luis teren op poort 25226 en CEF-berichten door sturen naar Azure Sentinel:**

    - Hiermee wordt de configuratie gedownload uit de GitHub-opslag plaats van de Log Analytics agent.

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **De syslog-daemon configureren:**

    - Opent poort 514 voor TCP-communicatie met het syslog-configuratie bestand `/etc/rsyslog.conf` .

    - Hiermee configureert u de daemon voor het door sturen van CEF-berichten naar de Log Analytics-agent op TCP-poort 25226 door een speciaal configuratie bestand in te voegen `security-config-omsagent.conf` in de map syslog daemon `/etc/rsyslog.d/` .

        Inhoud van het `security-config-omsagent.conf` bestand:

        ```bash
        if $rawmsg contains "CEF:" or $rawmsg contains "ASA-" then @@127.0.0.1:25226 
        ```

1. **De syslog-daemon en de Log Analytics-agent opnieuw starten:**

    - Hiermee wordt de rsyslog-daemon opnieuw gestart.
    
        ```bash
        service rsyslog restart
        ```

    - Hiermee wordt de Log Analytics-agent opnieuw gestart.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **De toewijzing van het *computer* veld zoals verwacht controleren:**

    - Hiermee zorgt u ervoor dat het veld *computer* in de syslog-bron juist is toegewezen in de log Analytics-agent met behulp van de volgende opdracht: 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>Als er een probleem is met de toewijzing, produceert het script een fout bericht dat u **de volgende opdracht hand matig moet uitvoeren** (waarbij de werk ruimte-id in plaats van de tijdelijke aanduiding wordt toegepast). De opdracht zorgt ervoor dat de juiste toewijzing wordt uitgevoerd en start de agent opnieuw.
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

# <a name="syslog-ng-daemon"></a>[syslog-ng-daemon](#tab/syslogng)

1. **De Log Analytics-agent downloaden en installeren:**

    - Hiermee wordt het installatie script gedownload voor de Linux-agent van Log Analytics (OMS).

        ```bash
        wget -O onboard_agent.sh https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/
            master/installer/scripts/onboard_agent.sh
        ```

    - Hiermee wordt de Log Analytics-agent geïnstalleerd.
    
        ```bash
        sh onboard_agent.sh -w [workspaceID] -s [Primary Key] -d opinsights.azure.com
        ```

1. **De configuratie van de Log Analytics-agent instellen om te Luis teren op poort 25226 en CEF-berichten door sturen naar Azure Sentinel:**

    - Hiermee wordt de configuratie gedownload uit de GitHub-opslag plaats van de Log Analytics agent.

        ```bash
        wget -O /etc/opt/microsoft/omsagent/[workspaceID]/conf/omsagent.d/security_events.conf
            https://raw.githubusercontent.com/microsoft/OMS-Agent-for-Linux/master/installer/conf/
            omsagent.d/security_events.conf
        ```

1. **De syslog-daemon configureren:**

    - Opent poort 514 voor TCP-communicatie met het syslog-configuratie bestand `/etc/syslog-ng/syslog-ng.conf` .

    - Hiermee configureert u de daemon voor het door sturen van CEF-berichten naar de Log Analytics-agent op TCP-poort 25226 door een speciaal configuratie bestand in te voegen `security-config-omsagent.conf` in de map syslog daemon `/etc/syslog-ng/conf.d/` .

        Inhoud van het `security-config-omsagent.conf` bestand:

        ```bash
        filter f_oms_filter {match(\"CEF\|ASA\" ) ;};destination oms_destination {tcp(\"127.0.0.1\" port(25226));};
        log {source(s_src);filter(f_oms_filter);destination(oms_destination);};
        ```

1. **De syslog-daemon en de Log Analytics-agent opnieuw starten:**

    - Hiermee wordt de syslog-ng-daemon opnieuw gestart.
    
        ```bash
        service syslog-ng restart
        ```

    - Hiermee wordt de Log Analytics-agent opnieuw gestart.

        ```bash
        /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```

1. **De toewijzing van het *computer* veld zoals verwacht controleren:**

    - Hiermee zorgt u ervoor dat het veld *computer* in de syslog-bron juist is toegewezen in de log Analytics-agent met behulp van de volgende opdracht: 

        ```bash
        grep -i "'Host' => record\['host'\]"  /opt/microsoft/omsagent/plugin/filter_syslog_security.rb
        ```
    - <a name="mapping-command"></a>Als er een probleem is met de toewijzing, produceert het script een fout bericht dat u **de volgende opdracht hand matig moet uitvoeren** (waarbij de werk ruimte-id in plaats van de tijdelijke aanduiding wordt toegepast). De opdracht zorgt ervoor dat de juiste toewijzing wordt uitgevoerd en start de agent opnieuw.
    
        ```bash
        sed -i -e "/'Severity' => tags\[tags.size - 1\]/ a \ \t 'Host' => record['host']" -e "s/'Severity' => tags\[tags.size - 1\]/&,/" /opt/microsoft/omsagent/plugin/filter_syslog_security.rb && sudo /opt/microsoft/omsagent/bin/service_control restart [workspaceID]
        ```
---

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u de Log Analytics-agent implementeert voor het verbinden van CEF-apparaten met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over de [veld toewijzing CEF en CommonSecurityLog](cef-name-mapping.md).
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](./tutorial-detect-threats-built-in.md).
