---
title: Afhankelijkheids analyse op basis van een agent instellen in de evaluatie van Azure Migrate server
description: In dit artikel wordt beschreven hoe u een afhankelijkheids analyse op basis van een agent instelt in Azure Migrate server-evaluatie.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 1a3f2ae4829c7f4ae41d31e2a2fc35d79adf3d4c
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596708"
---
# <a name="set-up-dependency-visualization"></a>Visualisatie van afhankelijkheid instellen

In dit artikel wordt beschreven hoe u een afhankelijkheids analyse op basis van een agent instelt in Azure Migrate: Server Assessment. [Afhankelijkheids analyse](concepts-dependency-visualization.md) helpt u bij het identificeren en begrijpen van afhankelijkheden tussen computers die u wilt beoordelen en die u wilt migreren naar Azure.

## <a name="before-you-start"></a>Voordat u begint

- Bekijk de ondersteunings-en implementatie vereisten voor analyse van afhankelijkheden op basis van een agent voor:
    - [VMware-VM's](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [Fysieke servers](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [Virtuele Hyper-V-machines](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements).
- Zorg ervoor dat:
    - Een Azure Migrate project hebben. Als u dit niet doet, [maakt](./create-manage-projects.md) u er nu een.
    - Controleer of u het Azure Migrate: Server Assessment Tool hebt [toegevoegd](how-to-assess.md) aan het project.
    - Stel een [Azure migrate apparaat](migrate-appliance.md) in om on-premises machines te detecteren. Het apparaat detecteert on-premises machines en verstuurt meta gegevens en prestatie gegevens naar Azure Migrate: Server evaluatie. Stel een apparaat in voor:
        - [VMware](how-to-set-up-appliance-vmware.md) Virtuele.
        - [Hyper-V](how-to-set-up-appliance-hyper-v.md) Virtuele.
        - [Fysieke servers](how-to-set-up-appliance-physical.md).
- Als u afhankelijkheids visualisatie wilt gebruiken, koppelt u een [log Analytics-werk ruimte](../azure-monitor/logs/manage-access.md) aan een Azure migrate project:
    - U kunt een werk ruimte pas toevoegen nadat u het Azure Migrate apparaat hebt ingesteld en de computers in het Azure Migrate-project detecteert.
    - Zorg ervoor dat u een werk ruimte hebt in het abonnement met het Azure Migrate-project.
    - De werk ruimte moet zich bevinden in de regio's VS-Oost, Zuidoost-Azië of Europa-west. Werk ruimten in andere regio's kunnen niet worden gekoppeld aan een project.
    - De werk ruimte moet zich in een regio bevinden waarin [servicetoewijzing wordt ondersteund](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions).
    - U kunt een nieuwe of bestaande Log Analytics-werk ruimte koppelen aan een Azure Migrate-project.
    - U koppelt de werk ruimte de eerste keer dat u een afhankelijkheids visualisatie instelt voor een machine. De werk ruimte voor een Azure Migrate project kan niet worden gewijzigd nadat deze is toegevoegd.
    - In Log Analytics wordt de werk ruimte die is gekoppeld aan Azure Migrate gelabeld met de sleutel van het migratie project en de project naam.

## <a name="associate-a-workspace"></a>Een werk ruimte koppelen

1. Nadat u computers voor beoordeling hebt gedetecteerd, klikt u in **servers**  >  **Azure migrate: Server evaluatie** op **overzicht**.  
2. Klik in **Azure migrate: Server evaluatie** op **essentiële** elementen.
3. Klik in de **OMS-werk ruimte** op **configuratie vereist**.

     ![Log Analytics-werkruimte configureren](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. In de **werk ruimte OMS configureren** geeft u op of u een nieuwe werk ruimte wilt maken of een bestaande wilt gebruiken.
    - U kunt een bestaande werk ruimte selecteren uit alle werk ruimten in het project abonnement migreren.
    - U hebt toegang tot de lezer nodig om de werk ruimte te koppelen.
5. Als u een nieuwe werk ruimte maakt, selecteert u een locatie.

    ![Een nieuwe werk ruimte toevoegen](./media/how-to-create-group-machine-dependencies/workspace.png)


## <a name="download-and-install-the-vm-agents"></a>De VM-agents downloaden en installeren

Installeer de agents op elke computer die u wilt analyseren.

> [!NOTE]
> Voor computers die worden bewaakt door System Center Operations Manager 2012 R2 of hoger, hoeft u de MMA-agent niet te installeren. Servicetoewijzing integreert met Operations Manager. [Volg](../azure-monitor/vm/service-map-scom.md#prerequisites) de richt lijnen voor integratie.

1. Klik in **Azure migrate: Server evaluatie** op **gedetecteerde servers**.
2. Voor elke computer die u wilt analyseren met afhankelijkheids visualisatie, klikt u in de kolom **afhankelijkheden** op **Agent installatie vereist**.
3. Down load de MMA-en Dependency-agent voor Windows of Linux op de pagina **afhankelijkheden** .
4. Onder **MMA-agent configureren kopieert u** de werk ruimte-ID en-sleutel. U hebt deze nodig wanneer u de MMA-Agent installeert.

    ![De agents installeren](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>De MMA installeren

Installeer de MMA op elke Windows-of Linux-computer die u wilt analyseren.

### <a name="install-mma-on-a-windows-machine"></a>MMA installeren op een Windows-computer

Installeer de agent als volgt op een Windows-computer:

1. Dubbelklik op de gedownloade agent.
2. Klik op de pagina **Welkom** op **Volgende**. Klik op de pagina **Licentievoorwaarden** op **Akkoord** om de licentie te accepteren.
3. Behoud of wijzig in **Doelmap** de standaardinstallatiemap > **Volgende**.
4. Selecteer in **Installatieopties voor agent** de optie **Azure Log Analytics** > **Volgende**.
5. Klik op **Toevoegen** om een nieuwe Log Analytics-werkruimte toe te voegen. Plak de werkruimte-id en -sleutel die u in de portal hebt gekopieerd. Klik op **Volgende**.

U kunt de agent installeren vanaf de opdracht regel of met behulp van een geautomatiseerde methode als Configuration Manager of [Intigua](https://www.intigua.com/intigua-for-azure-migration).
- [Meer informatie](../azure-monitor/agents/log-analytics-agent.md#installation-options) over het gebruiken van deze methoden om de MMA-agent te installeren.
- De MMA-agent kan ook worden geïnstalleerd met behulp van dit [script](https://github.com/brianbar-MSFT/Install-MMA).
- Meer [informatie](../azure-monitor/agents/agents-overview.md#supported-operating-systems) over de Windows-besturings systemen die worden ondersteund door MMA.

### <a name="install-mma-on-a-linux-machine"></a>MMA installeren op een Linux-computer

De MMA op een Linux-computer installeren:

1. Breng de juiste bundel (x86 of x64) met behulp van scp/ftp over naar uw Linux-computer.
2. Installeer de bundel met behulp van het argument --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Meer informatie](../azure-monitor/agents/agents-overview.md#supported-operating-systems) over de lijst met door MMA ondersteunde Linux-besturingssystemen. 

## <a name="install-the-dependency-agent"></a>De afhankelijkheidsagent installeren

1. Als u de afhankelijkheidsagent op een Windows-computer wilt installeren, dubbelklikt u op het installatiebestand en volgt u de wizard.
2. Als u de afhankelijkheidsagent op een Linux-computer wilt installeren, installeert u als root met de volgende opdracht:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Meer informatie](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent) over hoe u scripts kunt gebruiken om de afhankelijkheidsagent te installeren.
- Meer [informatie](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) over de besturings systemen die worden ondersteund door de afhankelijkheids agent.


## <a name="create-a-group-using-dependency-visualization"></a>Een groep maken met behulp van afhankelijkheids visualisatie

Maak nu een groep voor evaluatie. 


> [!NOTE]
> Groepen waarvoor u afhankelijkheden wilt visualiseren, mogen niet meer dan 10 machines bevatten. Als u meer dan 10 computers hebt, splitst u deze in kleinere groepen.

1. Klik in **Azure migrate: Server evaluatie** op **gedetecteerde servers**.
2. Klik in de kolom **afhankelijkheden** op **afhankelijkheden weer geven** voor elke computer die u wilt controleren.
3. Op de afhankelijkheids kaart ziet u het volgende:
    - Inkomende (clients) en uitgaande (servers) TCP-verbindingen van en naar de computer.
    - Afhankelijke computers waarop geen afhankelijkheids agenten zijn geïnstalleerd, worden gegroepeerd op poort nummers.
    - Afhankelijke computers waarop afhankelijkheids agenten zijn geïnstalleerd, worden weer gegeven als afzonderlijke vakken.
    - Processen die worden uitgevoerd op de computer. Vouw elke machine uit om de processen weer te geven.
    - Computer eigenschappen (inclusief FQDN, besturings systeem, MAC-adres). Klik op elke computer vak om de details weer te geven.

4. U kunt afhankelijkheden voor verschillende tijds duren bekijken door te klikken op de tijds duur in het label tijds bereik.
    - Het bereik is standaard een uur. 
    - U kunt het tijdsbereik wijzigen of begin- en einddatums en duur opgeven.
    - Het tijds bereik kan Maxi maal een uur duren. Als u een langere periode nodig hebt, gebruikt u Azure Monitor voor het opvragen van afhankelijke gegevens gedurende langere tijd.

5. Nadat u de afhankelijke computers die u wilt groeperen, hebt geïdentificeerd, gebruikt u CTRL + klikken om meerdere computers op de kaart te selecteren en klikt u op **machines groeperen**.
6. Geef een groepsnaam op.
7. Controleer of de afhankelijke machines worden gedetecteerd door Azure Migrate.

    - Als een afhankelijke machine niet wordt gedetecteerd door Azure Migrate: Server evaluatie, kunt u deze niet toevoegen aan de groep.
    - Als u een machine wilt toevoegen, voert u de detectie opnieuw uit en controleert u of de computer is gedetecteerd.

8. Als u een evaluatie voor deze groep wilt maken, schakelt u het selectievakje in om een nieuwe evaluatie voor de groep te maken.
8. Klik op **OK** om de groep op te slaan.

Nadat u de groep hebt gemaakt, wordt u aangeraden agents op alle computers in de groep te installeren en vervolgens afhankelijkheden voor de hele groep te visualiseren.

## <a name="query-dependency-data-in-azure-monitor"></a>Query's uitvoeren op afhankelijkheids gegevens in Azure Monitor

U kunt een query uitvoeren op afhankelijkheids gegevens die zijn vastgelegd door Servicetoewijzing in de werk ruimte Log Analytics die aan het Azure Migrate project is gekoppeld. Log Analytics wordt gebruikt om Azure Monitor-logboek query's te schrijven en uit te voeren.

- [Meer informatie over het](../azure-monitor/vm/service-map.md#log-analytics-records) zoeken naar servicetoewijzing gegevens in log Analytics.
- [Bekijk een overzicht van het](../azure-monitor/logs/get-started-queries.md)  schrijven van logboek query's in [log Analytics](../azure-monitor/logs/log-analytics-tutorial.md).

Voer een query uit voor afhankelijkheids gegevens als volgt:

1. Nadat u de agents hebt geïnstalleerd, gaat u naar de portal en klikt u op **Overzicht**.
2. Klik in **Azure migrate: Server evaluatie** op **overzicht**. Klik op de pijl-omlaag om de **kernen** uit te vouwen.
3. Klik in de **OMS-werk ruimte** op de naam van de werk ruimte.
3. Klik op de pagina Log Analytics werk ruimte > **Algemeen** op **Logboeken**.
4. Schrijf uw query en klik op **uitvoeren**.

### <a name="sample-queries"></a>Voorbeeldquery's

Hier volgen enkele voor beelden van query's die u kunt gebruiken om afhankelijkheids gegevens te extra heren.

- U kunt de query's aanpassen om de gewenste gegevenspunten te extraheren.
- [Bekijk](../azure-monitor/vm/service-map.md#log-analytics-records) een volledige lijst met afhankelijkheids gegevens records.
- [Bekijk](../azure-monitor/vm/service-map.md#sample-log-searches) aanvullende voorbeeld query's.

#### <a name="sample-review-inbound-connections"></a>Voor beeld: binnenkomende verbindingen controleren

Controleer binnenkomende verbindingen voor een set Vm's.

- De records in de tabel voor de metrische gegevens van de verbinding (VMConnection) vertegenwoordigen geen afzonderlijke fysieke netwerk verbindingen.
- Er worden meerdere fysieke netwerkverbindingen gegroepeerd in een logische verbinding.
- Meer [informatie](../azure-monitor/vm/service-map.md#connections) over hoe fysieke netwerk verbindings gegevens worden geaggregeerd in VMConnection.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="sample-summarize-sent-and-received-data"></a>Voor beeld: verzonden en ontvangen gegevens samenvatten

In dit voor beeld wordt een overzicht gegeven van het volume van de gegevens die worden verzonden en ontvangen op binnenkomende verbindingen tussen een set machines.

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z);
VMConnection
| where Direction == 'inbound'
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

## <a name="next-steps"></a>Volgende stappen

[Een evaluatie](how-to-create-assessment.md) voor een groep maken.