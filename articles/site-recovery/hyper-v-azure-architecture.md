---
title: Architectuur van herstel na nood geval voor Hyper-V in Azure Site Recovery
description: Dit artikel bevat een overzicht van de onderdelen en architectuur die worden gebruikt bij het implementeren van herstel na nood gevallen voor on-premises virtuele Hyper-V-machines (zonder VMM) naar Azure met de Azure Site Recovery-service.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: raynew
ms.openlocfilehash: c5025b83619b505728bfdf5c4e1ccc81d3bb225e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654758"
---
# <a name="hyper-v-to-azure-disaster-recovery-architecture"></a>Architectuur van Hyper-V naar Azure voor herstel na nood gevallen


In dit artikel worden de architectuur en processen beschreven die worden gebruikt wanneer u virtuele Hyper-V-machines (Vm's) repliceert tussen on-premises Hyper-V-hosts en Azure, met behulp van de [Azure site Recovery](site-recovery-overview.md) -service.

Hyper-V-hosts kunnen eventueel worden beheerd in persoonlijke Clouds van System Center Virtual Machine Manager (VMM).



## <a name="architectural-components---hyper-v-without-vmm"></a>Architectuur onderdelen-Hyper-V zonder VMM

De volgende tabel en afbeelding bieden een weer gave op hoog niveau van de onderdelen die worden gebruikt voor Hyper-V-replicatie naar Azure, wanneer Hyper-V-hosts niet door VMM worden beheerd.

**Onderdeel** | **Vereiste** | **Details**
--- | --- | ---
**Azure** | Een Azure-abonnement, een Azure-opslag account en een Azure-netwerk. | Gerepliceerde gegevens van on-premises VM-workloads worden opgeslagen in het opslag account. Virtuele Azure-machines worden gemaakt met de gegevens van de gerepliceerde werk belasting wanneer een failover vanaf uw on-premises site plaatsvindt.<br/><br/> De Azure-VM's maken verbinding met het virtuele Azure-netwerk wanneer ze worden gemaakt.
**Hyper-V** | Tijdens de Site Recovery-implementatie verzamelt u Hyper-V-hosts en-clusters in Hyper-V-sites. U installeert de Azure Site Recovery provider en Recovery Services agent op elke zelfstandige Hyper-V-host of op elk Hyper-V-cluster knooppunt. | De provider coördineert de replicatie met Site Recovery via internet. De Recovery Services-agent handelt de gegevensreplicatie af.<br/><br/> De communicatie vanuit zowel de Provider als de agent is beveiligd en versleuteld. De gerepliceerde gegevens in de Azure-opslag zijn eveneens versleuteld.
**Virtuele Hyper-V-machines** | Een of meer virtuele machines die worden uitgevoerd op Hyper-V. | U hoeft niets expliciet te installeren op virtuele machines.


**Hyper-V naar Azure-architectuur (zonder VMM)**

![Diagram van on-premises Hyper-V-site naar Azure-architectuur zonder VMM.](./media/hyper-v-azure-architecture/arch-onprem-azure-hypervsite.png)


## <a name="architectural-components---hyper-v-with-vmm"></a>Architectuur onderdelen-Hyper-V met VMM

De volgende tabel en afbeelding bieden een weer gave op hoog niveau van de onderdelen die worden gebruikt voor Hyper-V-replicatie naar Azure, wanneer Hyper-V-hosts worden beheerd in VMM-Clouds.

**Onderdeel** | **Vereiste** | **Details**
--- | --- | ---
**Azure** | Een Azure-abonnement, een Azure-opslag account en een Azure-netwerk. | Gerepliceerde gegevens van on-premises VM-workloads worden opgeslagen in het opslag account. Virtuele Azure-machines worden gemaakt met de gerepliceerde gegevens wanneer een failover vanaf uw on-premises site plaatsvindt.<br/><br/> De Azure-VM's maken verbinding met het virtuele Azure-netwerk wanneer ze worden gemaakt.
**VMM-server** | De VMM-server heeft een of meer clouds met Hyper-V-hosts. | U installeert de Site Recovery provider op de VMM-server om de replicatie met Site Recovery te organiseren en de server in de Recovery Services kluis te registreren.
**Hyper-V-host** | Een of meer Hyper-V-hosts/clusters worden beheerd door VMM. |  U installeert de Recovery Services-agent op elke Hyper-V-host of het cluster knooppunt.
**Virtuele Hyper-V-machines** | Er worden een of meer VM's uitgevoerd op een Hyper-V-hostserver. | U hoeft niets expliciet te installeren op virtuele machines.
**Netwerken** | Er zijn logische en VM-netwerken ingesteld op de VMM-server. Het VM-netwerk moet worden gekoppeld aan een logisch netwerk dat is gekoppeld aan de Cloud. | VM-netwerken worden toegewezen aan virtuele netwerken van Azure. Wanneer Azure Vm's zijn gemaakt na een failover, worden ze toegevoegd aan het Azure-netwerk dat is toegewezen aan het VM-netwerk.

**Hyper-V naar Azure-architectuur (met VMM)**

![Diagram met een on-premises Hyper-V-site naar Azure-architectuur met VMM.](./media/hyper-v-azure-architecture/arch-onprem-onprem-azure-vmm.png)

## <a name="set-up-outbound-network-connectivity"></a>Uitgaande netwerk verbinding instellen

Als u wilt dat Site Recovery werkt zoals verwacht, moet u de uitgaande netwerk verbinding wijzigen zodat uw omgeving kan repliceren.

> [!NOTE]
> Site Recovery biedt geen ondersteuning voor het gebruiken van een verificatieproxy om netwerkconnectiviteit te beheren.

### <a name="outbound-connectivity-for-urls"></a>Uitgaande connectiviteit voor URL's

Als u een URL-firewallproxy gebruikt om de uitgaande connectiviteit te beheren, staat u toegang tot deze URL’s toe:

| **Naam**                  | **Commercieel**                               | **Overheid**                                 | **Beschrijving** |
| ------------------------- | -------------------------------------------- | ---------------------------------------------- | ----------- |
| Storage                   | `*.blob.core.windows.net`                  | `*.blob.core.usgovcloudapi.net` | Hiermee kunnen gegevens van de VM naar het cache-opslagaccount in de bronregio worden geschreven. |
| Azure Active Directory    | `login.microsoftonline.com`                | `login.microsoftonline.us`                   | Verzorgt autorisatie en authenticatie voor de URL’s van Site Recovery. |
| Replicatie               | `*.hypervrecoverymanager.windowsazure.com` | `*.hypervrecoverymanager.windowsazure.com`   | Maakt het de VM mogelijk te communiceren met de Site Recovery-service. |
| Service Bus               | `*.servicebus.windows.net`                 | `*.servicebus.usgovcloudapi.net`             | Maakt het de VM mogelijk bewakings- en diagnosegegevens van Site Recovery te schrijven. |


## <a name="replication-process"></a>Replicatieproces

![Diagram van het replicatie proces van Hyper-V naar Azure](./media/hyper-v-azure-architecture/arch-hyperv-azure-workflow.png)

**Replicatie-en herstel proces**


### <a name="enable-protection"></a>Beveiliging inschakelen

1. Nadat u de beveiliging voor een Hyper-V-VM in Azure Portal of on-premises hebt ingeschakeld, start **Beveiliging inschakelen**.
2. Met deze taak wordt gecontroleerd of de machine voldoet aan de vereisten. Hierna wordt [CreateReplicationRelationship](/windows/win32/hyperv_v2/createreplicationrelationship-msvm-replicationservice) aangeroepen om replicatie in te stellen op basis van de instellingen die u hebt geconfigureerd.
3. De taak start initiële replicatie door de methode [StartReplication](/windows/win32/hyperv_v2/startreplication-msvm-replicationservice) aan te roepen om een volledige VM-replicatie te initialiseren en de virtuele schijven van de virtuele machine naar Azure te versturen.
4. U kunt de taak controleren op het tabblad **taken** .      ![Scherm afbeelding van de lijst taken op het tabblad taken. ](media/hyper-v-azure-architecture/image1.png) ![Screen Shot van het scherm beveiliging inschakelen met meer details.](media/hyper-v-azure-architecture/image2.png)


### <a name="initial-data-replication"></a>Initiële gegevens replicatie

1. Wanneer de initiële replicatie wordt geactiveerd, wordt een [Hyper-V VM](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd560637(v=ws.10)) -moment opname gemaakt.
2. Virtuele harde schijven op de virtuele machine worden een voor een gerepliceerd tot ze allemaal zijn gekopieerd naar Azure. Dit kan enige tijd duren, afhankelijk van de grootte van de virtuele machine en de netwerk bandbreedte. [Meer informatie over het](https://support.microsoft.com/kb/3056159) verg Roten van de netwerk bandbreedte.
3. Als er wijzigingen in de schijf optreden terwijl de eerste replicatie wordt uitgevoerd, worden de wijzigingen door de Hyper-V replica Replication tracker bijgehouden als Hyper-V-replicatie Logboeken (. HRL). Deze logboek bestanden bevinden zich in dezelfde map als de schijven. Elke schijf heeft een bijbehorend. HRL-bestand dat wordt verzonden naar de secundaire opslag. De momentopname- en logboekbestanden nemen schijfbronnen in beslag terwijl de eerste replicatie wordt uitgevoerd.
4. Als de initiële replicatie is voltooid, wordt de VM-momentopname verwijderd.
5. Verschillen in het logboek worden gesynchroniseerd en samengevoegd op de bovenliggende schijf.


### <a name="finalize-protection-process"></a>Beveiligings proces volt ooien

1. Nadat de initiële replicatie is voltooid, wordt de **beveiliging van de virtuele-machine** taak voltooid. De configuratie van het netwerk en andere instellingen na de replicatie wordt zodanig geconfigureerd dat de virtuele machine wordt beveiligd.
2. In deze fase kunt u de VM-instellingen controleren om er zeker van te zijn dat deze gereed is voor failover. U kunt een nood herstel analyse (testfailover) uitvoeren voor de virtuele machine om te controleren of er een failover wordt uitgevoerd naar verwachting. 


## <a name="delta-replication"></a>Replicatie van verschillen

1. Na de initiële replicatie begint de replicatie van verschillen, in overeenstemming met het replicatie beleid.
2. Met de Hyper-V replica Replication tracker worden wijzigingen in een virtuele harde schijf bijgehouden als. HRL-bestanden. Elke schijf die voor replicatie is geconfigureerd, heeft een bijbehorend .hrl-bestand.
3. Het logboek wordt verzonden naar het opslag account van de klant. Wanneer een logboek wordt door lopen naar Azure, worden de wijzigingen in de primaire schijf bijgehouden in een ander logboek bestand, in dezelfde map.
4. Tijdens de eerste en Delta replicatie kunt u de virtuele machine in de Azure Portal bewaken.

### <a name="resynchronization-process"></a>Proces opnieuw synchroniseren

1. Als de replicatie van verschillen mislukt en een volledige replicatie veel bandbreedte of tijd zou kosten, wordt de virtuele machine gemarkeerd voor hersynchronisatie.
    - Als de HRL-bestanden bijvoorbeeld 50% van de schijfgrootte in beslag nemen, wordt de virtuele machine gemarkeerd voor hersynchronisatie.
    -  De standaard instelling is dat de hersynchronisatie is gepland om automatisch buiten kantoor uren te worden uitgevoerd.
1.  Bij een hersynchronisatie worden alleen Delta gegevens verzonden.
    - Hiermee wordt de hoeveelheid verzonden gegevens geminimaliseerd door controle sommen van de bron-en doel-Vm's te berekenen.
    - Er wordt gebruikgemaakt van een vast blok kerend segmentatie algoritme waarbij bron-en doel bestanden worden onderverdeeld in vaste segmenten.
    - Er worden controle sommen voor elk segment gegenereerd. Deze worden vergeleken om te bepalen welke blokken van de bron moeten worden toegepast op het doel.
2. Na hersynchronisatie moet de normale replicatie van verschillen worden hervat.
3. Als u niet wilt wachten op de standaard hersynchronisatie buiten werk uren, kunt u een virtuele machine hand matig opnieuw synchroniseren. Als er bijvoorbeeld sprake is van een storing. Als u dit wilt doen, selecteert u in de Azure Portal de virtuele machine > opnieuw te **synchroniseren**.

    ![Scherm afbeelding met de optie opnieuw synchroniseren.](./media/hyper-v-azure-architecture/image4-site.png)


### <a name="retry-process"></a>Proces opnieuw proberen

Als er een replicatiefout optreedt, wordt de replicatie automatisch opnieuw geprobeerd. Nieuwe poging is geclassificeerd zoals beschreven in de tabel.

**Categorie** | **Details**
--- | ---
**Niet-herstelbare fouten** | Er wordt geen nieuwe poging gedaan. De status van de virtuele machine is **Kritiek** en tussenkomst van de beheerder is vereist.<br/><br/> Voor beelden van deze fouten zijn een verbroken VHD-keten, een ongeldige status voor de replica-VM, fout in netwerk verificatie, autorisatie fouten en niet-gevonden virtuele machines (voor zelfstandige Hyper-V-servers).
**Herstelbare fouten** | Nieuwe pogingen worden elk replicatie-interval uitgevoerd met behulp van een exponentiële uitstelbewerking die het interval voor nieuwe pogingen vanaf het begin van de eerste poging met 1, 2, 4, 8 en 10 minuten verhoogt. Als een fout zich blijft voordoen, probeert u het om de 30 minuten opnieuw. Voor beelden hiervan zijn netwerk fouten, lage schijf fouten en weinig geheugen.



## <a name="failover-and-failback-process"></a>Failover- en failbackproces

1. U kunt een geplande of niet-geplande failover uitvoeren van on-premises virtuele Hyper-V-machines naar Azure. Als u een geplande failover uitvoert, worden de virtuele bronmachines afgesloten om gegevensverlies te voorkomen. Voer een niet-geplande failover uit als uw primaire site niet toegankelijk is.
2. U kunt een failover van één machine uitvoeren of herstelplannen maken om de failover van meerdere virtuele machines te coördineren.
3. U voert een failover uit. Nadat de eerste fase van de failover is voltooid, kunt u de gemaakte replica-Vm's in azure zien. U kunt een openbaar IP-adres toewijzen aan de VM, indien nodig.
4. Vervolgens voert u de failover uit om toegang te krijgen tot de workload vanuit de replica-VM van Azure.

Als uw on-premises infra structuur weer actief is, kunt u een failback uitvoeren. Failback vindt plaats in drie fasen:

1. Een geplande failover van Azure naar de on-premises site starten:
    - **Downtime minimaliseren**: als u deze optie gebruikt site Recovery worden gegevens gesynchroniseerd voor failover. Er wordt gecontroleerd op gewijzigde gegevens blokken en deze worden gedownload naar de on-premises site, terwijl de virtuele machine van Azure actief blijft, waardoor de uitval tijd wordt geminimaliseerd. Wanneer u hand matig opgeeft dat de failover moet worden voltooid, wordt de Azure-VM afgesloten, worden eventuele laatste Delta wijzigingen gekopieerd en wordt de failover gestart.
    - **Volledige down load**: met deze optie gegevens worden tijdens de failover gesynchroniseerd. Met deze optie wordt de volledige schijf gedownload. Het is sneller omdat er geen controle sommen worden berekend, maar er is meer uitval tijd. Gebruik deze optie als u de replica-Azure-Vm's enige tijd hebt uitgevoerd of als de on-premises VM is verwijderd.
    - **VM maken**: u kunt een failback uitvoeren naar dezelfde virtuele machine of naar een andere virtuele machine. U kunt opgeven dat Site Recovery de virtuele machine moet maken als deze nog niet bestaat.

2. Wanneer de initiële synchronisatie is voltooid, selecteert u om de failover te volt ooien. Nadat deze is voltooid, kunt u zich aanmelden bij de on-premises VM om te controleren of alles werkt zoals verwacht. In de Azure Portal ziet u dat de virtuele machines van Azure zijn gestopt.
3.  Vervolgens voert u de failover door en opent u de werk belasting opnieuw vanaf de on-premises VM.
4. Nadat de workloads weer zijn hersteld, schakelt u omgekeerde replicatie in, zodat de on-premises Vm's opnieuw naar Azure worden gerepliceerd.



## <a name="next-steps"></a>Volgende stappen


Volg [deze zelf studie](tutorial-prepare-azure.md) om aan de slag te gaan met Hyper-V naar Azure-replicatie.


