---
title: Afhankelijkheidsanalyse op basis van een agent instellen in Azure Migrate
description: In dit artikel wordt beschreven hoe u afhankelijkheidsanalyse op basis van een agent kunt instellen in Azure Migrate.
author: rashi-ms
ms.author: rajosh
ms.manager: abhemraj
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: 6ded5d4ed8c2a55939bba908a05adbd2dea2ccbf
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714772"
---
# <a name="set-up-dependency-visualization"></a>Visualisatie van afhankelijkheden instellen

In dit artikel wordt beschreven hoe u afhankelijkheidsanalyse op basis van een agent in Azure Migrate: Detectie en evaluatie. [Afhankelijkheidsanalyse helpt](concepts-dependency-visualization.md) u bij het identificeren en begrijpen van afhankelijkheden tussen servers die u wilt evalueren en migreren naar Azure.

## <a name="before-you-start"></a>Voordat u begint

- Bekijk de ondersteunings- en implementatievereisten voor afhankelijkheidsanalyse op basis van een agent voor:
    - [Servers in VMware-omgeving](migrate-support-matrix-vmware.md#dependency-analysis-requirements-agent-based)
    - [Fysieke servers](migrate-support-matrix-physical.md#agent-based-dependency-analysis-requirements)
    - [Servers in Hyper-V-omgeving](migrate-support-matrix-hyper-v.md#agent-based-dependency-analysis-requirements)
- Zorg ervoor dat u:
    - Een project Azure Migrate maken. Als u dit niet hebt, [maakt u er](./create-manage-projects.md) nu een.
    - Controleer of u de volgende [Azure Migrate:](how-to-assess.md) Detectie en evaluatieprogramma aan het project hebt toegevoegd.
    - Een apparaat Azure Migrate [om](migrate-appliance.md) on-premises servers te ontdekken. Het apparaat detecteert on-premises servers en verzendt metagegevens en prestatiegegevens naar Azure Migrate: Detectie en evaluatie. Stel een apparaat in voor:
        - [Servers in VMware-omgeving](how-to-set-up-appliance-vmware.md)
        - [Servers in Hyper-V-omgeving](how-to-set-up-appliance-hyper-v.md)
        - [Fysieke servers](how-to-set-up-appliance-physical.md)
- Als u visualisatie van afhankelijkheden wilt gebruiken, koppelt u [een Log Analytics-werkruimte](../azure-monitor/logs/manage-access.md) aan een Azure Migrate project:
    - U kunt een werkruimte pas koppelen nadat u het Azure Migrate hebt gemaakt en servers in het Azure Migrate detecteren.
    - Zorg ervoor dat u een werkruimte in het abonnement hebt die het Azure Migrate bevat.
    - De werkruimte moet zich in de regio's VS - oost, Azië - zuidoost of Europa - west bevinden. Werkruimten in andere regio's kunnen niet worden gekoppeld aan een project.
    - De werkruimte moet zich in een regio waarin [Servicetoewijzing wordt ondersteund.](../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)
    - U kunt een nieuwe of bestaande Log Analytics-werkruimte koppelen aan een Azure Migrate project.
    - U koppelt de werkruimte de eerste keer dat u afhankelijkheidsvisualisatie voor een server in stelt. De werkruimte voor een Azure Migrate project kan niet worden gewijzigd nadat het is toegevoegd.
    - In Log Analytics wordt de werkruimte die aan Azure Migrate gekoppeld, getagd met de sleutel van het migratieproject en de projectnaam.

## <a name="associate-a-workspace"></a>Een werkruimte koppelen

1. Nadat u servers voor evaluatie hebt ontdekt, klikt u in **Servers**  >  **Azure Migrate: Detectie en evaluatie** op **Overzicht.**  
2. Klik **Azure Migrate: Detectie en evaluatie** op **Essentials.**
3. Klik **in de OMS-werkruimte** op **Configuratie vereist.**

     ![Log Analytics-werkruimte configureren](./media/how-to-create-group-machine-dependencies/oms-workspace-select.png)   

4. Geef **in OMS-werkruimte** configureren op of u een nieuwe werkruimte wilt maken of een bestaande werkruimte wilt gebruiken.
    - U kunt een bestaande werkruimte selecteren in alle werkruimten in het projectabonnement.
    - U hebt lezertoegang tot de werkruimte nodig om deze te koppelen.
5. Als u een nieuwe werkruimte maakt, selecteert u er een locatie voor.

    ![Een nieuwe werkruimte toevoegen](./media/how-to-create-group-machine-dependencies/workspace.png)

> [!Note]
> [Meer informatie over](https://docs.microsoft.com/azure/azure-monitor/logs/private-link-security) het configureren van de OMS-werkruimte voor connectiviteit met privé-eindpunten.  

## <a name="download-and-install-the-vm-agents"></a>De VM-agents downloaden en installeren

Installeer de agents op elke server die u wilt analyseren.

> [!NOTE]
> Voor servers die worden bewaakt System Center Operations Manager 2012 R2 of hoger, hoeft u de MMA-agent niet te installeren. Servicetoewijzing kan worden geïntegreerd met Operations Manager. [Volg de](../azure-monitor/vm/service-map-scom.md#prerequisites) richtlijnen voor integratie.

1. Klik **Azure Migrate: Detectie en evaluatie** op **Detectieservers.**
2. Klik voor elke server die u wilt analyseren met afhankelijkheidsvisualisatie in de kolom **Afhankelijkheden** op **Installatie van agent vereist.**
3. Download op **de pagina Afhankelijkheden** de MMA en afhankelijkheidsagent voor Windows of Linux.
4. Kopieer **onder MMA-agent configureren** de werkruimte-id en -sleutel. U hebt deze nodig wanneer u de MMA-agent installeert.

    ![De agents installeren](./media/how-to-create-group-machine-dependencies/dependencies-install.png)


## <a name="install-the-mma"></a>De MMA installeren

Installeer de MMA op elke Windows- of Linux-server die u wilt analyseren.

### <a name="install-mma-on-a-windows-server"></a>MMA installeren op een Windows-server

De agent installeren op een Windows-server:

1. Dubbelklik op de gedownloade agent.
2. Klik op de pagina **Welkom** op **Volgende**. Klik op de pagina **Licentievoorwaarden** op **Akkoord** om de licentie te accepteren.
3. Behoud of wijzig in **Doelmap** de standaardinstallatiemap > **Volgende**.
4. Selecteer in **Installatieopties voor agent** de optie **Azure Log Analytics** > **Volgende**.
5. Klik op **Toevoegen** om een nieuwe Log Analytics-werkruimte toe te voegen. Plak de werkruimte-id en -sleutel die u in de portal hebt gekopieerd. Klik op **Volgende**.

U kunt de agent installeren vanaf de opdrachtregel of met behulp van een geautomatiseerde methode zoals Configuration Manager of [Intigua](https://www.intigua.com/intigua-for-azure-migration).
- [Meer informatie](../azure-monitor/agents/log-analytics-agent.md#installation-options) over het gebruiken van deze methoden om de MMA-agent te installeren.
- De MMA-agent kan ook worden geïnstalleerd met behulp van dit [script](https://github.com/brianbar-MSFT/Install-MMA).
- [Meer informatie over](../azure-monitor/agents/agents-overview.md#supported-operating-systems) de Windows-besturingssystemen die worden ondersteund door MMA.

### <a name="install-mma-on-a-linux-server"></a>MMA installeren op een Linux-server

De MMA installeren op een Linux-server:

1. Breng de juiste bundel (x86 of x64) met behulp van scp/ftp over naar uw Linux-computer.
2. Installeer de bundel met behulp van het argument --install.

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[Meer informatie](../azure-monitor/agents/agents-overview.md#supported-operating-systems) over de lijst met door MMA ondersteunde Linux-besturingssystemen. 

## <a name="install-the-dependency-agent"></a>De afhankelijkheidsagent installeren

1. Als u de afhankelijkheidsagent op een Windows-server wilt installeren, dubbelklikt u op het installatiebestand en volgt u de wizard.
2. Als u de afhankelijkheidsagent op een Linux-server wilt installeren, installeert u als root met behulp van de volgende opdracht:

    ```sh InstallDependencyAgent-Linux64.bin```

- [Meer informatie](../azure-monitor/vm/vminsights-enable-hybrid.md#dependency-agent) over hoe u scripts kunt gebruiken om de afhankelijkheidsagent te installeren.
- [Meer informatie over](../azure-monitor/vm/vminsights-enable-overview.md#supported-operating-systems) de besturingssystemen die worden ondersteund door de afhankelijkheidsagent.


## <a name="create-a-group-using-dependency-visualization"></a>Een groep maken met behulp van afhankelijkheidsvisualisatie

Maak nu een groep voor evaluatie. 


> [!NOTE]
> Groepen waarvoor u afhankelijkheden wilt visualiseren, mogen niet meer dan 10 servers bevatten. Als u meer dan 10 servers hebt, splitst u ze op in kleinere groepen.

1. Klik **Azure Migrate: Detectie en evaluatie** op **Detectieservers.**
2. Klik in **de kolom Afhankelijkheden** **op Afhankelijkheden weergeven** voor elke server die u wilt controleren.
3. Op de afhankelijkheidskaart ziet u het volgende:
    - Binnenkomende (clients) en uitgaande (servers) TCP-verbindingen van en naar de server.
    - Afhankelijke servers waarop de afhankelijkheidsagents niet zijn geïnstalleerd, worden gegroepeerd op poortnummers.
    - Afhankelijke servers waarop afhankelijkheidsagents zijn geïnstalleerd, worden als afzonderlijke vakken weergegeven.
    - Processen die worden uitgevoerd op de server. Vouw elk servervak uit om de processen te bekijken.
    - Servereigenschappen (inclusief FQDN, besturingssysteem, MAC-adres). Klik op elk servervak om de details weer te geven.

4. U kunt afhankelijkheden voor verschillende tijdsduuren bekijken door te klikken op de tijdsduur in het tijdsbereiklabel.
    - Het bereik is standaard een uur. 
    - U kunt het tijdsbereik wijzigen of begin- en einddatums en duur opgeven.
    - Het tijdsbereik kan maximaal een uur zijn. Als u een langer bereik nodig hebt, gebruikt u Azure Monitor om een langere periode een query uit te voeren op afhankelijke gegevens.

5. Nadat u de afhankelijke servers hebt geïdentificeerd die u wilt groepen, gebruikt u Ctrl +Klik om meerdere servers op de kaart te selecteren en klikt u op **Computers groepken.**
6. Geef een groepsnaam op.
7. Controleer of de afhankelijke servers zijn ontdekt door Azure Migrate.

    - Als een afhankelijke server niet wordt ontdekt door Azure Migrate: Detectie en evaluatie, kunt u deze niet toevoegen aan de groep.
    - Als u een server wilt toevoegen, moet u detectie opnieuw uitvoeren en controleren of de server is ontdekt.

8. Als u een evaluatie voor deze groep wilt maken, schakelt u het selectievakje in om een nieuwe evaluatie voor de groep te maken.
8. Klik op **OK** om de groep op te slaan.

Nadat u de groep hebt gemaakt, raden we u aan agents te installeren op alle servers in de groep en vervolgens afhankelijkheden voor de hele groep te visualiseren.

## <a name="query-dependency-data-in-azure-monitor"></a>Afhankelijkheidsgegevens opvragen in Azure Monitor

U kunt afhankelijkheidsgegevens opvragen die zijn vastgelegd door Servicetoewijzing in de Log Analytics-werkruimte die is gekoppeld aan het Azure Migrate project. Log Analytics wordt gebruikt voor het schrijven en uitvoeren van Azure Monitor logboekquery's.

- [Meer informatie over het](../azure-monitor/vm/service-map.md#log-analytics-records) zoeken Servicetoewijzing gegevens in Log Analytics.
- [Krijg een overzicht van](../azure-monitor/logs/get-started-queries.md) het schrijven van logboekquery's in [Log Analytics.](../azure-monitor/logs/log-analytics-tutorial.md)

Voer als volgt een query uit voor afhankelijkheidsgegevens:

1. Nadat u de agents hebt geïnstalleerd, gaat u naar de portal en klikt u op **Overzicht**.
2. Klik **Azure Migrate: Detectie en evaluatie** op **Overzicht.** Klik op de pijl-omlaag om **Essentials uit te vouwen.**
3. Klik **in OMS-werkruimte** op de naam van de werkruimte.
3. Klik op de pagina Log Analytics-werkruimte > **Algemeen** op **Logboeken.**
4. Schrijf uw query en klik op **Uitvoeren.**

### <a name="sample-queries"></a>Voorbeeldquery's

Hier zijn enkele voorbeeldquery's die u kunt gebruiken om afhankelijkheidsgegevens te extraheren.

- U kunt de query's aanpassen om de gewenste gegevenspunten te extraheren.
- [Bekijk](../azure-monitor/vm/service-map.md#log-analytics-records) een volledige lijst met afhankelijkheidsgegevensrecords.
- [Bekijk](../azure-monitor/vm/service-map.md#sample-log-searches) aanvullende voorbeeldquery's.

#### <a name="sample-review-inbound-connections"></a>Voorbeeld: Binnenkomende verbindingen controleren

Controleer binnenkomende verbindingen voor een set servers.

- De records in de tabel voor metrische verbindingsgegevens (VMConnection) vertegenwoordigen geen afzonderlijke fysieke netwerkverbindingen.
- Er worden meerdere fysieke netwerkverbindingen gegroepeerd in een logische verbinding.
- [Meer informatie over](../azure-monitor/vm/service-map.md#connections) hoe gegevens van fysieke netwerkverbindingen worden geaggregeerd in VMConnection.

```
// the servers of interest
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

#### <a name="sample-summarize-sent-and-received-data"></a>Voorbeeld: Verzonden en ontvangen gegevens samenvatten

Dit voorbeeld geeft een overzicht van de hoeveelheid gegevens die worden verzonden en ontvangen op binnenkomende verbindingen tussen een set servers.

```
// the servers of interest
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

[Maak een evaluatie](how-to-create-assessment.md) voor een groep.
