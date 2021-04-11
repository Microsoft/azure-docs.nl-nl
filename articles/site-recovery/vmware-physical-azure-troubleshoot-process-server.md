---
title: Problemen met de Azure Site Recovery process server oplossen
description: In dit artikel wordt beschreven hoe u problemen met de Azure Site Recovery process server oplost
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.openlocfilehash: c6696fb395aee7284f1354269cd4cc3167ff3198
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581806"
---
# <a name="troubleshoot-the-process-server"></a>Problemen met de proces server oplossen

De [site Recovery](site-recovery-overview.md) -proces server wordt gebruikt wanneer u herstel na nood gevallen instelt voor Azure voor on-premises VMware-vm's en fysieke servers. In dit artikel wordt beschreven hoe u problemen met de proces server oplost, met inbegrip van replicatie-en verbindings problemen.

Meer [informatie](vmware-physical-azure-config-process-server-overview.md) over de proces server.

## <a name="before-you-start"></a>Voordat u begint

Voordat u begint met het oplossen van problemen:

1. Zorg ervoor dat u begrijpt hoe [proces servers worden bewaakt](vmware-physical-azure-monitor-process-server.md).
2. Bekijk de aanbevolen procedures hieronder.
3. Zorg ervoor dat u rekening houdt met [capaciteits overwegingen](site-recovery-plan-capacity-vmware.md#capacity-considerations)en gebruik grootte richtlijnen voor de [Configuratie server](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) of [zelfstandige proces servers](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Aanbevolen procedures voor de implementatie van proces servers

Voor optimale prestaties van proces servers hebben we een aantal algemene aanbevolen procedures toegelicht.

**Best practice** | **Details**
--- |---
**Gebruik** | Zorg ervoor dat de configuratie server/zelfstandige proces server alleen wordt gebruikt voor het beoogde doel. U hoeft niets anders op de computer uit te voeren.
**IP-adres** | Zorg ervoor dat de proces server een statisch IPv4-adres heeft en dat NAT niet is geconfigureerd.
**Beheer geheugen/CPU-gebruik** |Behoud het CPU-en geheugen gebruik onder 70%.
**Beschik bare ruimte** | Beschik bare ruimte verwijst naar de cache schijf ruimte op de proces server. Replicatie gegevens worden opgeslagen in de cache voordat ze worden geüpload naar Azure.<br/><br/> Beschik bare ruimte boven 25% blijven. Als deze onder 20% komt, wordt de replicatie beperkt voor gerepliceerde machines die aan de proces server zijn gekoppeld.

## <a name="check-process-server-health"></a>Status van de proces server controleren

De eerste stap bij het oplossen van problemen is het controleren van de status en status van de proces server. U doet dit door alle waarschuwingen te bekijken, te controleren of de vereiste services worden uitgevoerd en te controleren of er sprake is van een heartbeat van de proces server. Deze stappen worden in de volgende afbeelding samenvatten, gevolgd door procedures om u te helpen bij het uitvoeren van de stappen.

![Problemen met de proces server status oplossen](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Stap 1: problemen met de proces server status waarschuwingen oplossen

De proces server genereert een aantal status waarschuwingen. Deze waarschuwingen en aanbevolen acties worden in de volgende tabel samenvatten.

**Waarschuwingstype** | **Fout** | **Problemen oplossen**
--- | --- | --- 
![In orde][green] | Geen  | De proces server is verbonden en in orde.
![Waarschuwing][yellow] | De opgegeven services worden niet uitgevoerd. | 1. Controleer of de services worden uitgevoerd.<br/> 2. als de services worden uitgevoerd zoals verwacht, volgt u de onderstaande instructies voor het oplossen van problemen [met connectiviteit en replicatie](#check-connectivity-and-replication).
![Waarschuwing][yellow]  | CPU-gebruik > 80% gedurende de afgelopen 15 minuten. | 1. Voeg geen nieuwe machines toe.<br/>2. Controleer of het aantal Vm's dat gebruikmaakt van de proces server wordt uitgelijnd op [gedefinieerde limieten](site-recovery-plan-capacity-vmware.md#capacity-considerations)en overweeg een [extra proces server](vmware-azure-set-up-process-server-scale.md)in te stellen.<br/>3. Volg de onderstaande instructies om [verbindings-en replicatie problemen op te lossen](#check-connectivity-and-replication).
![Kritiek][red] |  CPU-gebruik > 95% gedurende de afgelopen 15 minuten. | 1. Voeg geen nieuwe machines toe.<br/>2. Controleer of het aantal Vm's dat gebruikmaakt van de proces server wordt uitgelijnd op [gedefinieerde limieten](site-recovery-plan-capacity-vmware.md#capacity-considerations)en overweeg een [extra proces server](vmware-azure-set-up-process-server-scale.md)in te stellen.<br/>3. Volg de onderstaande instructies om [verbindings-en replicatie problemen op te lossen](#check-connectivity-and-replication).<br/> 4. als het probleem zich blijft voordoen, voert u de [Deployment planner](./site-recovery-deployment-planner.md) voor de replicatie van VMware/fysieke servers uit.
![Waarschuwing][yellow] | Geheugen gebruik > 80% gedurende de afgelopen 15 minuten. |  1. Voeg geen nieuwe machines toe.<br/>2. Controleer of het aantal Vm's dat gebruikmaakt van de proces server wordt uitgelijnd op [gedefinieerde limieten](site-recovery-plan-capacity-vmware.md#capacity-considerations)en overweeg een [extra proces server](vmware-azure-set-up-process-server-scale.md)in te stellen.<br/>3. Volg de instructies die aan de waarschuwing zijn gekoppeld.<br/> 4. als het probleem zich blijft voordoen, volgt u de onderstaande instructies om [verbindings-en replicatie problemen op te lossen](#check-connectivity-and-replication).
![Kritiek][red] | Geheugen gebruik > 95% gedurende de afgelopen 15 minuten. | 1. Voeg geen nieuwe machines toe en overweeg een [extra proces server](vmware-azure-set-up-process-server-scale.md)in te stellen.<br/> 2. Volg de instructies die aan de waarschuwing zijn gekoppeld.<br/> 3.4. Als het probleem blijft bestaan, volgt u de onderstaande instructies om problemen [met connectiviteit en replicatie op te lossen](#check-connectivity-and-replication).<br/> 4. als het probleem zich blijft voordoen, voert u de [Deployment planner](./site-recovery-deployment-planner.md) uit voor problemen met de replicatie van VMware/fysieke servers.
![Waarschuwing][yellow] | De beschik bare ruimte in de cachemap < 30% gedurende de afgelopen 15 minuten. | 1. Voeg geen nieuwe machines toe en overweeg een [extra proces server](vmware-azure-set-up-process-server-scale.md)in te stellen.<br/>2. Controleer of het aantal Vm's dat gebruikmaakt van de proces server wordt uitgelijnd op [richt lijnen](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Volg de onderstaande instructies om [verbindings-en replicatie problemen op te lossen](#check-connectivity-and-replication).
![Kritiek][red] |  Beschik bare ruimte < 25% gedurende de afgelopen 15 minuten | 1. Volg de instructies die zijn gekoppeld aan de waarschuwing voor dit probleem.<br/> 2.3. Volg de onderstaande instructies om [verbindings-en replicatie problemen op te lossen](#check-connectivity-and-replication).<br/> 3. als het probleem zich blijft voordoen, voert u de [Deployment planner](./site-recovery-deployment-planner.md) voor de replicatie van VMware/fysieke servers uit.
![Kritiek][red] | Geen heartbeat van de proces server gedurende 15 minuten of langer. De tmansvs-service communiceert niet met de configuratie server. | 1) Controleer of de proces server actief is.<br/> 2. Controleer of de tmassvc wordt uitgevoerd op de proces server.<br/> 3. Volg de onderstaande instructies om [verbindings-en replicatie problemen op te lossen](#check-connectivity-and-replication).


![Tabel sleutel](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Stap 2: process server-services controleren

De volgende tabel bevat een overzicht van de services die op de proces server moeten worden uitgevoerd. Er zijn kleine verschillen in Services, afhankelijk van de implementatie van de proces server. 

Voor alle services, met uitzonde ring van de Microsoft Azure Recovery Services-agent (obengine), controleert u of de StartType is ingesteld op **automatisch** of **automatisch (vertraagd starten)**.
 
**Implementatie** | **Actieve services**
--- | ---
**Proces server op de configuratie server** | ProcessServer; ProcessServerMonitor; cxprocessserver Inmage-PushInstall; Log upload-service (LogUpload); Inmage Scout-toepassings service; Microsoft Azure Recovery Services-agent (obengine); Inmage Scout-agent-Sentinel-Outpost (svagents); tmansvc World Wide Web Publishing-service (W3SVC); MySQL Microsoft Azure Site Recovery-service (DRA)
**Proces server die wordt uitgevoerd als zelfstandige server** | ProcessServer; ProcessServerMonitor; cxprocessserver Inmage-PushInstall; Log upload-service (LogUpload); Inmage Scout-toepassings service; Microsoft Azure Recovery Services-agent (obengine); Inmage Scout-agent-Sentinel-Outpost (svagents); tmansvc.
**Proces server geïmplementeerd in azure voor failback** | ProcessServer; ProcessServerMonitor; cxprocessserver Inmage-PushInstall; Logboek-upload service (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Stap 3: de heartbeat van de proces server controleren

Als er geen heartbeat van de proces server is (fout code 806), doet u het volgende:

1. Controleer of de virtuele machine van de proces server actief is.
2. Controleer de logboeken op fouten.

    C:\ProgramData\ASR\home\svsystems\eventmanager *. log c\programdata\asr\home\svsystems\ monitor_protection*. log

## <a name="check-connectivity-and-replication"></a>Connectiviteit en replicatie controleren

 Initiële en doorlopende replicatie fouten worden vaak veroorzaakt door verbindings problemen tussen bron machines en de proces server, of tussen de proces server en Azure. Deze stappen worden in de volgende afbeelding samenvatten, gevolgd door procedures om u te helpen bij het uitvoeren van de stappen.

![Stroom diagram met stappen voor het oplossen van connectiviteit en replicatie.](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Stap 4: de tijd synchronisatie op de bron computer controleren

Zorg ervoor dat de systeem datum en-tijd voor de gerepliceerde machine synchroon zijn. [Meer informatie](/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Stap 5: controleren op antivirus software op de bron machine

Controleer of het Site Recovery niet wordt geblokkeerd door antivirus software op de gerepliceerde machine. Raadpleeg [dit artikel](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program)als u site Recovery van antivirus Programma's wilt uitsluiten.

## <a name="step-6-check-connectivity-from-source-machine"></a>Stap 6: de connectiviteit van de bron computer controleren


1. Installeer de [Telnet-client](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771275(v=ws.10)) op de bron computer als dat nodig is. Gebruik Ping niet.
2. Ping vanaf de bron machine de proces server op de HTTPS-poort met Telnet. Standaard is 9443 de HTTPS-poort voor replicatie verkeer.

    `telnet <process server IP address> <port>`

3. Controleer of de verbinding is geslaagd.


**Connectiviteit** | **Details** | **Actie**
--- | --- | ---
**Laatste** | Telnet toont een leeg scherm en de proces server is bereikbaar. | Er is geen verdere actie vereist.
**Mislukt** | U kunt geen verbinding maken | Zorg ervoor dat binnenkomende poort 9443 is toegestaan op de proces server. Als u bijvoorbeeld een perimeter netwerk of een gescreend subnet hebt. Controleer de connectiviteit opnieuw.
**Gedeeltelijk geslaagd** | U kunt verbinding maken, maar de bron machine meldt dat de proces server niet kan worden bereikt. | Ga door met de volgende procedure voor probleem oplossing.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Stap 7: problemen met een onbereikbare proces server oplossen

Als de proces server niet bereikbaar is vanaf de bron computer, wordt fout 78186 weer gegeven. Als dit probleem niet wordt opgelost, leidt dit tot zowel toepassings consistente als crash consistente herstel punten die niet op de verwachte manier worden gegenereerd.

Los problemen op door te controleren of de bron machine het IP-adres van de proces server kan bereiken en voer het cxpsclient-hulp programma op de bron machine uit om de end-to-end-verbinding te controleren.


### <a name="check-the-ip-connection-on-the-process-server"></a>Controleer de IP-verbinding op de proces server

Als Telnet is gelukt, maar de bron machine rapporteert dat de proces server niet kan worden bereikt, controleert u of u het IP-adres van de proces server kunt bereiken.

1. Probeer in een webbrowser IP-adres https://te bereiken <PS_IP>: <PS_Data_Port>/.
2. Als er een HTTPS-certificaat fout wordt weer gegeven, is dat normaal. Als u de fout negeert, ziet u een 400-ongeldige aanvraag. Dit betekent dat de server de browser aanvraag niet kan verwerken en dat de standaard HTTPS-verbinding prima is.
3. Als deze controle niet werkt, noteert u het fout bericht van de browser. Een 407-fout geeft bijvoorbeeld aan dat er een probleem is met de verificatie van de proxy.

### <a name="check-the-connection-with-cxpsclient"></a>Controleer de verbinding met cxpsclient

Daarnaast kunt u het hulp programma cxpsclient uitvoeren om de end-to-end-verbinding te controleren.

1. Voer het hulp programma als volgt uit:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. Controleer op de proces server de gegenereerde logboeken in deze mappen:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Controleer de logboeken van de bron-VM op Upload fouten (fout 78028)

Probleem met het uploaden van gegevens die zijn geblokkeerd voor bron machines naar de proces service kan leiden tot zowel crash-consistente als toepassings consistente herstel punten die niet worden gegenereerd. 

1. Als u problemen met het uploaden van het netwerk wilt oplossen, kunt u fouten in dit logboek zoeken:

    C:\Program Files (x86) \Microsoft Azure site Recovery\agent\svagents *. log 

2. Gebruik de rest van de procedures in dit artikel om problemen met het uploaden van gegevens op te lossen.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Stap 8: controleren of de proces server gegevens pusht

Controleer of de proces server bezig is met het pushen van gegevens naar Azure.

  1. Open taak beheer op de proces server (druk op CTRL + SHIFT + ESC).
  2. Selecteer het tabblad **prestaties** > **Open Broncontrole**.
  3. Op **Broncontrole** pagina selecteert u het tabblad **netwerk** . Controleer onder **processen met netwerk activiteit** of cbengine.exe actief een grote hoeveelheid gegevens verzendt.

       ![Scherm afbeelding van een groot aantal volumes onder processen met netwerk activiteit.](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Als cbengine.exe geen grote hoeveelheid gegevens verstuurt, voltooit u de stappen in de volgende secties.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Stap 9: de verbinding tussen de proces server en Azure Blob-opslag controleren

1. Selecteer **cbengine.exe** in Broncontrole.
2. Controleer onder **TCP-verbindingen** of er verbinding is tussen de proces server en de Azure-opslag.

  ![Scherm opname van de connectiviteit tussen cbengine.exe en de URL van de Azure Blob Storage.](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Services controleren

Als er geen verbinding is tussen de proces server en de URL van de Azure Blob Storage, controleert u of de services worden uitgevoerd.

1. Selecteer in het configuratie scherm de optie **Services**.
2. Controleer of de volgende services worden uitgevoerd:

    - cxprocessserver
    - Inmage Scout-agent: Sentinel/Outpost
    - Microsoft Azure Recovery Services-agent
    - Microsoft Azure Site Recovery-service
    - tmansvc

3. Een service die niet wordt uitgevoerd, starten of opnieuw starten.
4. Controleer of de proces server is verbonden en bereikbaar is. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Stap 10: de verbinding tussen de proces server en het open bare IP-adres van Azure controleren

1. Open op de proces server in **%ProgramFiles%\Microsoft Azure Recovery Services Agent\Temp** het meest recente bestand CBEngineCurr. errlog.
2. Zoek in het bestand naar **443** of de verbindings poging voor de teken reeks **is mislukt**.

  ![Fouten Logboeken in de map Temp](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Als u problemen ziet, vindt u uw open bare Azure IP-adres in het CBEngineCurr. currLog-bestand met behulp van poort 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. Gebruik op de opdracht regel van de proces server Telnet om uw open bare IP-adres van Azure te pingen.
6. Als u geen verbinding kunt maken, volgt u de volgende procedure.

## <a name="step-11-check-process-server-firewall-settings"></a>Stap 11: de firewall instellingen van de proces server controleren. 

Controleer of de op IP-adres gebaseerde firewall op de proces server de toegang blokkeert.

1. Voor op IP-adres gebaseerde firewall regels:

    a) down load de volledige lijst met [Microsoft Azure Data Center IP-bereiken](https://www.microsoft.com/download/details.aspx?id=41653).

    b) Voeg de IP-adresbereiken toe aan uw firewall configuratie om ervoor te zorgen dat de firewall communicatie met Azure toestaat (en naar de standaard HTTPS-poort 443).

    c) IP-adresbereiken toestaan voor de Azure-regio van uw abonnement en voor de regio Azure West US (gebruikt voor toegangs beheer en identiteits beheer).

2. Voor op URL gebaseerde firewalls voegt u de Url's die in de volgende tabel worden vermeld toe aan de firewall configuratie.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Stap 12: de proxy-instellingen van de proces server controleren 

1. Als u een proxy server gebruikt, moet u ervoor zorgen dat de naam van de proxy server wordt opgelost door de DNS-server. Controleer de waarde die u hebt opgegeven bij het instellen van de configuratie server in de register sleutel **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.
2. Zorg ervoor dat dezelfde instellingen worden gebruikt door de Azure Site Recovery-agent om gegevens te verzenden.

    a) zoeken naar **Microsoft Azure backup**.

    b) Open **Microsoft Azure backup** en selecteer Eigenschappen van **actie**  >  **wijzigen**.

    c) op het tabblad **proxy configuratie** moet het proxy adres gelijk zijn aan het proxy adres dat wordt weer gegeven in de Register instellingen. Als dat niet het geval is, wijzigt u dit in hetzelfde adres.

## <a name="step-13-check-bandwidth"></a>Stap 13: band breedte controleren

Verg root de band breedte tussen de proces server en Azure en controleer of het probleem zich nog steeds voordoet.


## <a name="next-steps"></a>Volgende stappen

Als u meer hulp nodig hebt, kunt u uw vraag plaatsen op de [pagina micro soft Q&een vraag voor Azure site Recovery](/answers/topics/azure-site-recovery.html). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png