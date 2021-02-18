---
title: VMWare-bewaking oplossing in Azure Monitor | Microsoft Docs
description: Meer informatie over hoe de VMWare-bewaking-oplossing kan helpen bij het beheren van Logboeken en het controleren van ESXi-hosts.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/04/2018
ms.openlocfilehash: 9dc31cd4f492a4e95ce8232a8df28f07206e23b1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587166"
---
# <a name="vmware-monitoring-deprecated-solution-in-azure-monitor"></a>Oplossing VMWare-bewaking (afgeschaft) in Azure Monitor

![VMware-symbool](./media/vmware/vmware-symbol.png)

> [!NOTE]
> De VMWare-bewaking oplossing is afgeschaft.  Klanten die de oplossing al hebben geïnstalleerd, kunnen deze blijven gebruiken, maar VMWare-bewaking kunnen niet worden toegevoegd aan nieuwe werk ruimten.

De VMWare-bewaking oplossing in Azure Monitor is een oplossing waarmee u een gecentraliseerde aanpak van logboek registratie en bewaking voor grote VMware-Logboeken kunt maken. In dit artikel wordt beschreven hoe u de ESXi-hosts op één locatie kunt oplossen, vastleggen en beheren met behulp van de oplossing. Met deze oplossing kunt u gedetailleerde gegevens voor al uw ESXi-hosts op één locatie bekijken. U kunt de belangrijkste aantallen, status en trends van VM'S en ESXi-hosts die worden geboden via de ESXi-host-logboeken bekijken. U kunt problemen oplossen door gecentraliseerde ESXi te bekijken en te zoeken. En u kunt waarschuwingen maken op basis van zoek query's in het logboek.

De oplossing maakt gebruik van systeem eigen syslog-functionaliteit van de ESXi-host om gegevens naar een doel-VM te pushen, die de Log Analytics-agent heeft. De oplossing schrijft echter geen bestanden naar syslog binnen de doel-VM. De Log Analytics-agent opent poort 1514 en luistert deze. Zodra de gegevens zijn ontvangen, worden de gegevens door de Log Analytics agent naar Azure Monitor gepusht.

## <a name="install-and-configure-the-solution"></a>De oplossing installeren en configureren
Gebruik de volgende informatie om de oplossing te installeren en configureren.

* Voeg de VMWare-bewaking oplossing toe aan uw abonnement met behulp van het proces dat wordt beschreven in [een bewakings oplossing installeren](./solutions.md#install-a-monitoring-solution).

#### <a name="supported-vmware-esxi-hosts"></a>Ondersteunde VMware ESXi hosts
vSphere ESXi host 5,5, 6,0 en 6,5

#### <a name="prepare-a-linux-server"></a>Een Linux-server voorbereiden
Maak een virtuele machine van het Linux-besturings systeem om alle syslog-gegevens van de ESXi-hosts te ontvangen. De [log Analytics Linux-agent](../vm/quick-collect-linux-computer.md) is het verzamel punt voor alle ESXi host syslog-gegevens. U kunt meerdere ESXi-hosts gebruiken om logboeken door te sturen naar één Linux-server, zoals in het volgende voor beeld.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

   ![syslog-stroom](./media/vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Syslog-verzameling configureren
1. Het door sturen van syslog instellen voor VSphere. Zie [syslog configureren op ESXi 5,0 en hoger (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322)voor gedetailleerde informatie over het instellen van syslog-forwarding. Ga naar de **ESXi-configuratie**  >  **Software**  >  **Geavanceerde instellingen**  >  **syslog**.
   ![vsphereconfig](./media/vmware/vsphere1.png)  
1. Voeg in het veld *syslog. Global. logHost* uw Linux-server en het poort nummer *1514* toe. Bijvoorbeeld `tcp://hostname:1514` of `tcp://123.456.789.101:1514`
1. Open de ESXi host-firewall voor syslog. **ESXi**  >  **Software**  >  **Beveiligings profiel**  >  **Firewall** en open **Eigenschappen**.  

    ![vspherefw](./media/vmware/vsphere2.png)  

    ![vspherefwproperties](./media/vmware/vsphere3.png)  
1. Controleer de vSphere-console om te controleren of syslog juist is ingesteld. Bevestig op de ESXI-host dat poort **1514** is geconfigureerd.
1. Down load en installeer de Log Analytics-agent voor Linux op de Linux-server. Zie de [documentatie voor log Analytics-agent voor Linux](https://github.com/Microsoft/OMS-Agent-for-Linux)voor meer informatie.
1. Nadat de Log Analytics-agent voor Linux is geïnstalleerd, gaat u naar de map/etc/opt/Microsoft/omsagent/sysconf/omsagent.d en kopieert u het bestand vmware_esxi. conf naar de map/etc/opt/Microsoft/omsagent/conf/omsagent.d en wijzigt u de eigenaar/groep en de machtigingen van het bestand. Bijvoorbeeld:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
   sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```
1. Start de Log Analytics-agent voor Linux opnieuw op door uit te voeren `sudo /opt/microsoft/omsagent/bin/service_control restart` .
1. Test de connectiviteit tussen de Linux-server en de ESXi-host met behulp van de `nc` opdracht op de ESXi-host. Bijvoorbeeld:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

1. Voer in de Azure Portal een logboek query uit voor `VMware_CL` . Wanneer Azure Monitor de syslog-gegevens verzamelt, behoudt deze de indeling syslog. In de portal worden bepaalde specifieke velden vastgelegd, zoals *hostname* en *Procesnaam*.  

    ![Scherm afbeelding toont een logboek query voor type = VMware_CL met een tijds tempel als resultaat.](./media/vmware/type.png)  

    Als de zoek resultaten van de logboeken in de weer gave overeenkomen met de bovenstaande afbeelding, kunt u het dash board van de VMWare-bewaking oplossing gebruiken.  

## <a name="vmware-data-collection-details"></a>Details van VMware-gegevens verzameling
De VMWare-bewaking oplossing verzamelt diverse metrische gegevens over prestaties en meldt zich aan bij ESXi-hosts met behulp van de Log Analytics agents voor Linux die u hebt ingeschakeld.

De volgende tabel toont methoden voor gegevens verzameling en andere informatie over hoe gegevens worden verzameld.

| platform | Log Analytics-agent voor Linux | System Center Operations Manager-agent | Azure Storage | Operations Manager vereist? | Operations Manager agent gegevens die via een beheer groep zijn verzonden | verzamelings frequentie |
| --- | --- | --- | --- | --- | --- | --- |
| Linux |&#8226; |  |  |  |  |om de 3 minuten |

In de volgende tabel ziet u voor beelden van gegevens velden die door de VMWare-bewaking oplossing zijn verzameld:

| veld naam | beschrijving |
| --- | --- |
| Device_s |VMware-opslag apparaten |
| ESXIFailure_s |fout typen |
| EventTime_t |tijdstip waarop gebeurtenis is opgetreden |
| HostName_s |ESXi-hostnaam |
| Operation_s |VM maken of virtuele machine verwijderen |
| ProcessName_s |gebeurtenis naam |
| ResourceId_s |naam van de VMware-host |
| ResourceLocation_s |VMware |
| ResourceName_s |VMware |
| ResourceType_s |Hyper-V |
| SCSIStatus_s |Status van VMware SCSI |
| SyslogMessage_s |Syslog-gegevens |
| UserName_s |gebruiker die virtuele machine heeft gemaakt of verwijderd |
| VMName_s |VM-naam |
| Computer |hostcomputer |
| TimeGenerated |tijdstip waarop de gegevens zijn gegenereerd |
| DataCenter_s |VMware-Data Center |
| StorageLatency_s |opslag latentie (MS) |

## <a name="vmware-monitoring-solution-overview"></a>Overzicht van VMWare-bewaking oplossingen
De VMware-tegel wordt weer gegeven in uw Log Analytics-werk ruimte. Het bevat een weer gave op hoog niveau van eventuele fouten. Wanneer u op de tegel klikt, gaat u naar een dashboard weergave.

![Scherm afbeelding toont de VMware-tegel waarin negen fouten worden weer gegeven.](./media/vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Navigeren in de dashboard weergave
In de weer gave **VMware** -dash board worden Blades ingedeeld op:

* Aantal mislukte statussen
* Belangrijkste host op basis van aantal gebeurtenissen
* Aantal meest voorkomende gebeurtenissen
* Activiteiten van virtuele machine
* ESXi

![solution1](./media/vmware/solutionview1-1.png)

![solution2](./media/vmware/solutionview1-2.png)

Klik op een Blade om Log Analytics Zoek venster te openen waarin gedetailleerde informatie voor de Blade wordt weer gegeven.

Hier kunt u de logboek query bewerken om deze te wijzigen voor iets specifiek. Zie [gegevens zoeken met behulp van logboek query's in azure monitor](../logs/log-query-overview.md)voor meer informatie over het maken van logboek query's.

#### <a name="find-esxi-host-events"></a>ESXi host-gebeurtenissen zoeken
Eén ESXi-host genereert meerdere logboeken op basis van hun processen. De VMWare-bewaking oplossing centraliseert deze en geeft een overzicht van het aantal gebeurtenissen. Deze gecentraliseerde weer gave helpt u te begrijpen welke ESXi-host een groot aantal gebeurtenissen heeft en welke gebeurtenissen het vaakst optreden in uw omgeving.

![gebeurtenislog](./media/vmware/events.png)

U kunt verder inzoomen door te klikken op een ESXi-host of een gebeurtenis type.

Wanneer u op een ESXi-hostnaam klikt, ziet u informatie van die ESXi-host. Als u de resultaten van het gebeurtenis type wilt beperken, voegt u deze toe aan `“ProcessName_s=EVENT TYPE”` uw zoek query. U kunt **proces** naam selecteren in het zoek filter. Zo beperkt u de informatie voor u.

![Scherm afbeelding van de ESXi host per gebeurtenis aantal en uitsplitsing per gebeurtenis type-Blade in de VMWare-bewaking dashboard weergave.](./media/vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Activiteiten met hoge VM zoeken
Een virtuele machine kan op elke ESXi-host worden gemaakt en verwijderd. Het is handig voor een beheerder om te bepalen hoeveel Vm's een ESXi-host maakt. Die op zijn beurt helpt bij het begrijpen van de prestaties en capaciteits planning. Het bijhouden van gebeurtenissen van een VM-activiteit is van cruciaal belang bij het beheren van uw omgeving.

![Scherm afbeelding van de Blade virtuele-machine activiteiten in het dash board van VMWare-bewaking, met een grafiek van het maken en verwijderen van VM'S door de ESXi-host.](./media/vmware/vmactivities1.png)

Als u aanvullende gegevens voor het maken van VM-ESXi wilt weer geven, klikt u op een ESXi-hostnaam.

![Scherm opname van een deel venster van het VMWare-bewaking dash board waarin een tabel wordt weer gegeven met een rij gegevens voor elke virtuele machine die wordt gemaakt door een ESXi-host.](./media/vmware/createvm.png)

#### <a name="common-log-queries"></a>Veelvoorkomende logboek query's
De oplossing bevat andere nuttige query's die u kunnen helpen bij het beheren van uw ESXi-hosts, zoals hoge opslag ruimte, opslag latentie en probleem met het pad.

![Scherm opname toont aanbevolen Zoek opdrachten, wat nuttig is voor opgeslagen query's.](./media/vmware/queries.png)


#### <a name="save-queries"></a>Query's opslaan
Het opslaan van logboek query's is een standaard functie in Azure Monitor en kan u helpen bij het bewaren van query's die u nuttig hebt gevonden. Nadat u een query hebt gemaakt die u nuttig vindt, slaat u deze op door op de **Favorieten** te klikken. Met een opgeslagen query kunt u deze later eenvoudig opnieuw gebruiken vanaf de pagina [mijn dash board](../visualize/tutorial-logs-dashboards.md) waar u uw eigen aangepaste Dash boards kunt maken.

![Scherm afbeelding toont een deel van een aangepast dash board met de naam zoeken in Logboeken met pictogrammen voor ongedaan maken, exporteren, waarschuwing, opslaan, favorieten en geschiedenis.](./media/vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Waarschuwingen maken op basis van query's
Nadat u uw query's hebt gemaakt, wilt u mogelijk de query's gebruiken om u te waarschuwen wanneer er specifieke gebeurtenissen optreden. Zie [waarschuwingen in log Analytics](../alerts/alerts-overview.md) voor informatie over het maken van waarschuwingen. Zie voor voor beelden van waarschuwings query's en andere query voorbeelden de [bewaking VMware met log Analytics](/archive/blogs/msoms/monitor-vmware-using-oms-log-analytics) blog post.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen
### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>Wat moet ik doen op de ESXi? Wat is de impact van het in mijn huidige omgeving?
De oplossing maakt gebruik van het native ESXi-host syslog-mechanisme voor het door sturen van hosts. U hebt geen aanvullende micro soft-software op de ESXi-host nodig om de logboeken vast te leggen. Het moet een lage invloed hebben op uw bestaande omgeving. U moet echter wel het door sturen van syslog instellen. Dit is ESXI-functionaliteit.

### <a name="do-i-need-to-restart-my-esxi-host"></a>Moet ik mijn ESXi-host opnieuw opstarten?
Nee. Opnieuw opstarten is niet vereist voor dit proces. Soms werkt vSphere de syslog niet goed bij. Meld u in dat geval aan bij de ESXi-host en laad de syslog opnieuw. U hoeft de host dus niet opnieuw op te starten, zodat dit proces niet wordt verstoord door uw omgeving.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-log-analytics"></a>Kan ik het volume aan logboek gegevens dat naar Log Analytics is verzonden, verg Roten of verkleinen?
Ja, dat kunt u. U kunt de instellingen voor ESXi in vSphere gebruiken. De logboek verzameling is gebaseerd op het *info* niveau. Als u het maken of verwijderen van een virtuele machine wilt controleren, moet u dus het *info* niveau op hosten. Raadpleeg de [VMware Knowledge Base](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658)voor meer informatie.

### <a name="why-is-hostd-not-providing-data-to-log-analytics-my-log-setting-is-set-to-info"></a>Waarom wordt het hosten van gegevens niet verstrekt aan Log Analytics? De logboek instelling is ingesteld op info.
Er is een fout opgetreden in de ESXi voor de syslog-tijds tempel. Raadpleeg de [VMware Knowledge Base](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202)voor meer informatie. Nadat u de tijdelijke oplossing hebt toegepast, moet u hosten normaal functioneren.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Kan ik meerdere ESXi-hosts syslog-gegevens naar één virtuele machine door sturen met omsagent?
Ja. U kunt meerdere ESXi-hosts door sturen naar één virtuele machine met omsagent.

### <a name="why-dont-i-see-data-flowing-into-log-analytics"></a>Waarom zie ik geen gegevens die in Log Analytics worden geplaatst?
Er kunnen meerdere redenen zijn:

* De ESXi-host pusht de gegevens niet goed naar de virtuele machine met omsagent. Voer de volgende stappen uit om te testen:

  1. Als u wilt bevestigen, meldt u zich aan bij de ESXi-host met SSH en voert u de volgende opdracht uit: `nc -z ipaddressofVM 1514`

      Als dit niet lukt, zijn de vSphere-instellingen in de geavanceerde configuratie waarschijnlijk niet correct. Zie [syslog-verzameling configureren](#configure-syslog-collection) voor meer informatie over het instellen van de ESXi-host voor het door sturen van syslog.
  1. Als de verbinding met de syslog-poort is geslaagd, maar u nog steeds geen gegevens ziet, laadt u de syslog op de ESXi-host opnieuw met behulp van SSH om de volgende opdracht uit te voeren: `esxcli system syslog reload`
* De virtuele machine met Log Analytics agent is niet correct ingesteld. Voer de volgende stappen uit om dit te testen:

  1. Log Analytics luistert naar poort 1514. Voer de volgende opdracht uit om te controleren of deze is geopend: `netstat -a | grep 1514`
  1. U ziet dat de poort `1514/tcp` geopend is. Als dat niet het geval is, controleert u of de omsagent juist is geïnstalleerd. Als u de poort gegevens niet ziet, is de syslog-poort niet geopend op de VM.

    a. Controleer of de Log Analytics agent wordt uitgevoerd met behulp van `ps -ef | grep oms` . Als de service niet wordt uitgevoerd, start u het proces door de opdracht uit te voeren `sudo /opt/microsoft/omsagent/bin/service_control start`

     b. Open het bestand `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf`.

     c. Controleer of de juiste gebruikers-en groeps instelling geldig is, vergelijkbaar met: `-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

     d. Als het bestand niet bestaat of als de gebruikers-en groeps instelling onjuist zijn, moet u [een Linux-server voorbereiden](#prepare-a-linux-server).

## <a name="next-steps"></a>Volgende stappen
* Gebruik [logboek query's](../logs/log-query-overview.md) in log Analytics om gedetailleerde VMware-hostgegevens weer te geven.
* [Maak uw eigen Dash boards](../visualize/tutorial-logs-dashboards.md) waarin VMware-hostgegevens worden weer gegeven.
* [Waarschuwingen maken](../alerts/alerts-overview.md) wanneer specifieke VMware-host-gebeurtenissen optreden.

