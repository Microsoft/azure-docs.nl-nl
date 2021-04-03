---
title: Architectuur voor herstel na nood gevallen voor VMware VM in Azure Site Recovery
description: Dit artikel bevat een overzicht van de onderdelen en architectuur die worden gebruikt bij het instellen van herstel na nood gevallen van on-premises virtuele VMware-machines naar Azure met Azure Site Recovery
author: rayne-wiselman
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/06/2019
ms.author: raynew
ms.openlocfilehash: 5cf4dc5123040fd2af8efe54153867a8709fe1ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97652225"
---
# <a name="vmware-to-azure-disaster-recovery-architecture"></a>Architectuur voor nood herstel van VMware naar Azure

In dit artikel worden de architectuur en processen beschreven die worden gebruikt bij het implementeren van herstel na nood gevallen, failover en herstel van virtuele VMware-machines (Vm's) tussen een on-premises VMware-site en Azure met behulp van de [Azure site Recovery](site-recovery-overview.md) -service.


## <a name="architectural-components"></a>Architectuuronderdelen

De volgende tabel en afbeelding bieden een weer gave op hoog niveau van de onderdelen die worden gebruikt voor VMware-herstel na nood gevallen naar Azure.

**Onderdeel** | **Vereiste** | **Details**
--- | --- | ---
**Azure** | Een Azure-abonnement, Azure Storage account voor cache, beheerde schijf en Azure-netwerk. | Gerepliceerde gegevens van on-premises Vm's worden opgeslagen in azure Storage. Virtuele Azure-machines worden gemaakt met de gerepliceerde gegevens wanneer u een failover van on-premises naar Azure uitvoert. De Azure-VM's maken verbinding met het virtuele Azure-netwerk wanneer ze worden gemaakt.
**Computer van de configuratie server** | Één on-premises computer. We raden u aan deze uit te voeren als een VMware-VM die kan worden geïmplementeerd vanuit een gedownloade OVF-sjabloon.<br/><br/> Op de computer worden alle on-premises Site Recovery onderdelen uitgevoerd, waaronder de configuratie server, de proces server en de hoofddoel server. | **Configuratie server**: coördineert de communicatie tussen on-premises en Azure, en beheert de gegevens replicatie.<br/><br/> **Proces server**: standaard geïnstalleerd op de configuratie server. Het ontvangt replicatie gegevens; optimaliseert het met caching, compressie en versleuteling. en verzendt deze naar Azure Storage. De processerver installeert ook Azure Site Recovery Mobility Service op VM's die u wilt repliceren, en detecteert automatisch on-premises computers. Naarmate uw implementatie groeit, kunt u extra, afzonderlijke proces servers toevoegen om grotere volumes van replicatie verkeer af te handelen.<br/><br/> **Hoofddoel server**: standaard geïnstalleerd op de configuratie server. De replicatie gegevens worden verwerkt tijdens de failback vanuit Azure. Voor grote implementaties kunt u een extra, afzonderlijke Master doel server toevoegen voor failback.
**VMware-servers** | VMware-Vm's worden gehost op on-premises vSphere ESXi-servers. We raden aan dat een vCenter-Server de hosts beheert. | Tijdens Site Recovery implementatie voegt u VMware-servers toe aan de Recovery Services kluis.
**Gerepliceerde machines** | Mobility service is geïnstalleerd op elke virtuele VMware-machine die u repliceert. | U wordt aangeraden automatische installatie vanaf de proces server toe te staan. U kunt de service ook hand matig installeren of een geautomatiseerde implementatie methode gebruiken, zoals Configuration Manager.

![Diagram van de relatie tussen VMware en Azure-replicatie architectuur.](./media/vmware-azure-architecture/arch-enhanced.png)

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

Raadpleeg [de sectie netwerk vereisten in het artikel](vmware-azure-deploy-configuration-server.md#prerequisites)vereisten voor een uitgebreide lijst met url's die moeten worden gefilterd voor communicatie tussen on-premises Azure site Recovery-infra structuur en Azure-Services.

## <a name="replication-process"></a>Replicatieproces

1. Wanneer u replicatie voor een virtuele machine inschakelt, begint de initiële replicatie naar Azure Storage met behulp van het opgegeven replicatie beleid. Houd rekening met het volgende:
    - Voor virtuele VMware-machines is de replicatie op blok niveau, bijna continu, met behulp van de Mobility Service-agent die op de virtuele machine wordt uitgevoerd.
    - Alle instellingen voor het replicatie beleid worden toegepast:
        - **RPO-drempel waarde**. Deze instelling heeft geen invloed op replicatie. Het helpt bij de bewaking. Er wordt een gebeurtenis gegenereerd en optioneel een e-mail verzonden als de huidige RPO de drempel waarde overschrijdt die u opgeeft.
        - **Bewaar periode van het herstel punt**. Met deze instelling geeft u op hoe ver terug in de tijd die u wilt door gaan wanneer er een onderbreking optreedt. De maximale Bewaar periode voor Premium-opslag is 24 uur. In de standaard opslag is 72 uur. 
        - **App-consistente moment opnamen**. U kunt de app-consistente moment opname elke 1 tot 12 uur nemen, afhankelijk van de behoeften van uw app. Moment opnamen zijn standaard Azure Blob-moment opnamen. De Mobility-agent die wordt uitgevoerd op een VM, verzoekt een VSS-moment opname in overeenstemming met deze instelling en blad wijzers die als een toepassings consistent punt in de replicatie stroom wordt genoemd.

2. Verkeer wordt gerepliceerd naar open bare eind punten van Azure Storage via internet. U kunt ook Azure ExpressRoute gebruiken met [micro soft-peering](../expressroute/expressroute-circuit-peerings.md#microsoftpeering). Het repliceren van verkeer via een site-naar-site virtueel particulier netwerk (VPN) van een on-premises site naar Azure wordt niet ondersteund.
3. Bij de eerste replicatie bewerking wordt ervoor gezorgd dat volledige gegevens op de computer op het moment van replicatie inschakelen naar Azure worden verzonden. Wanneer de initiële replicatie is voltooid, begint de replicatie van Delta wijzigingen naar Azure. Bijgehouden wijzigingen voor een machine worden verzonden naar de proces server.
4. Communicatie gebeurt als volgt:

    - Vm's communiceren met de on-premises configuratie server op poort HTTPS 443 inkomend voor replicatie beheer.
    - De configuratie Server organiseert de replicatie met Azure via poort HTTPS 443 uitgaand.
    - Vm's verzenden replicatie gegevens naar de proces server (die wordt uitgevoerd op de computer van de configuratie server) op poort HTTPS 9443-binnenkomend. Deze poort kan worden gewijzigd.
    - De proces server ontvangt replicatie gegevens, optimaliseert en versleutelt deze en verzendt deze naar Azure Storage via poort 443 uitgaand.
5. De replicatie gegevens melden het eerste land in een cache-opslag account in Azure. Deze logboeken worden verwerkt en de gegevens worden opgeslagen in een Azure Managed disk (ook wel ASR-Seed-schijf genoemd). De herstel punten worden op deze schijf gemaakt.

![Diagram van het replicatie proces van VMware naar Azure.](./media/vmware-azure-architecture/v2a-architecture-henry.png)

## <a name="resynchronization-process"></a>Proces opnieuw synchroniseren

1. Tijdens de eerste replicatie of tijdens de overdracht van Delta wijzigingen, kunnen er problemen met de netwerk verbinding tussen de bron machine en de proces server of tussen proces servers en Azure ontstaan. Een van deze kan leiden tot fouten in de gegevens overdracht naar Azure.
2. Site Recovery markeert een computer voor hersynchronisatie om problemen met gegevens integriteit te voor komen en de kosten voor gegevens overdracht te minimaliseren.
3. Een machine kan ook als volgt worden gemarkeerd voor hersynchronisatie in situaties met het behoud van de consistentie tussen de bron computer en gegevens die zijn opgeslagen in azure
    - Als een computer geforceerd afsluiten afdwingt
    - Als een computer configuratie wijzigingen ondergaat, zoals het wijzigen van de grootte van de schijf (het formaat van de schijf aanpassen van 2 TB in 4 TB)
4. Bij een hersynchronisatie worden alleen Delta gegevens naar Azure verzonden. Gegevens overdracht tussen on-premises en Azure wordt geminimaliseerd door controle sommen van gegevens tussen de bron computer en gegevens die zijn opgeslagen in Azure.
5. De standaard instelling is dat de hersynchronisatie is gepland om automatisch buiten kantoor uren te worden uitgevoerd. Als u niet wilt wachten op de standaard hersynchronisatie buiten werk uren, kunt u een virtuele machine hand matig opnieuw synchroniseren. Als u dit wilt doen, gaat u naar Azure Portal en selecteert u de virtuele machine > opnieuw te **synchroniseren**.
6. Als de standaard hersynchronisatie buiten kantoor uren mislukt en een hand matige interventie vereist is, wordt er een fout gegenereerd op de specifieke machine in Azure Portal. U kunt de fout oplossen en de hersynchronisatie hand matig activeren.
7. Zodra de hersynchronisatie is voltooid, wordt replicatie van wijzigingen in de Delta hervat.

## <a name="replication-policy"></a>Beleid voor replicatie 

Wanneer u Azure VM-replicatie inschakelt, Site Recovery standaard een nieuw replicatie beleid maken met de standaard instellingen in de tabel.

**Beleidsinstelling** | **Details** | **Prijs**
--- | --- | ---
**Bewaarperiode van herstelpunt** | Hiermee geeft u op hoelang Site Recovery herstel punten bewaard | 24 uur
**Frequentie van de app-consistente momentopname** | Hoe vaak Site Recovery een app-consistente moment opname gebruikt. | Elke vier uur

### <a name="managing-replication-policies"></a>Replicatie beleid beheren

U kunt de standaard instellingen voor het replicatie beleid als volgt beheren en wijzigen:
- U kunt de instellingen wijzigen terwijl u replicatie inschakelt.
- U kunt op elk gewenst moment een replicatie beleid maken en dit vervolgens Toep assen wanneer u replicatie inschakelt.

### <a name="multi-vm-consistency"></a>Multi-VM-consistentie

Als u wilt dat Vm's samen worden gerepliceerd en gedeelde crash-consistente en toepassings consistente herstel punten op failover hebben, kunt u ze samen in een replicatie groep verzamelen. Multi-VM-consistentie heeft de prestaties van de werk belasting en mag alleen worden gebruikt voor Vm's met werk belastingen die consistentie op alle computers nodig hebben. 



## <a name="snapshots-and-recovery-points"></a>Momentopnamen en herstelpunten

Herstel punten worden gemaakt op basis van moment opnamen van VM-schijven die op een bepaald moment worden uitgevoerd. Wanneer u een failover voor een virtuele machine doorvoert, gebruikt u een herstel punt om de virtuele machine op de doel locatie te herstellen.

Als er een failover wordt uitgevoerd, willen we doorgaans controleren of de VM begint zonder beschadiging of verlies van gegevens en dat de gegevens van de virtuele machine consistent zijn voor het besturings systeem en voor apps die worden uitgevoerd op de virtuele machine. Dit is afhankelijk van het type moment opnamen dat is gemaakt.

Site Recovery maakt moment opnamen als volgt:

1. Site Recovery maakt standaard consistente moment opnamen van gegevens en app-consistente moment opnamen als u een frequentie opgeeft.
2. Herstel punten worden gemaakt op basis van de moment opnamen en opgeslagen in overeenstemming met de Bewaar instellingen in het replicatie beleid.

### <a name="consistency"></a>Consistentie

In de volgende tabel worden verschillende soorten consistentie beschreven.

### <a name="crash-consistent"></a>Crash-consistent

**Beschrijving** | **Details** | **Aanbeveling**
--- | --- | ---
Een crash consistente moment opname legt gegevens vast die zich op de schijf bevonden toen de moment opname werd gemaakt. Het bevat niets in het geheugen.<br/><br/> Het bevat het equivalent van de gegevens op de schijf die aanwezig zouden zijn als de virtuele machine is vastgelopen of het netsnoer van de server is opgehaald op het moment dat de moment opname werd gemaakt.<br/><br/> Een crash consistent garandeert geen gegevens consistentie voor het besturings systeem of voor apps op de virtuele machine. | Met Site Recovery worden standaard crash consistente herstel punten in elke vijf minuten gemaakt. Deze instelling kan niet worden gewijzigd.<br/><br/>  | Vandaag kunnen de meeste apps goed worden hersteld met crash-consistente punten.<br/><br/> Crash-consistente herstel punten zijn doorgaans voldoende voor de replicatie van besturings systemen en apps, zoals DHCP-servers en afdruk servers.

### <a name="app-consistent"></a>App-consistent

**Beschrijving** | **Details** | **Aanbeveling**
--- | --- | ---
App-consistente herstel punten worden gemaakt op basis van app-consistente moment opnamen.<br/><br/> Een app-consistente moment opname bevat alle informatie in een crash consistente moment opname, plus alle gegevens in het geheugen en trans acties die worden uitgevoerd. | App-consistente moment opnamen gebruiken de Volume Shadow Copy Service (VSS):<br/><br/>   1) Azure Site Recovery maakt gebruik van de methode Copy only backup (VSS_BT_COPY) waarbij de back-uptijd en het Volg nummer van het transactie logboek van micro soft SQL niet worden gewijzigd </br></br> 2) als er een moment opname wordt gestart, voert VSS een Kopieer bewerking voor het schrijven van de schijf uit op het volume.<br/><br/>   3) voordat de koeien wordt uitgevoerd, informeert VSS elke app op de computer die de geheugenresidente gegevens op schijf moet leegmaken.<br/><br/>   4) vervolgens kan de app back-up/herstel na nood gevallen (in dit geval Site Recovery) de momentopname gegevens lezen en door gaan. | App-consistente moment opnamen worden gemaakt op basis van de frequentie die u opgeeft. Deze frequentie moet altijd kleiner zijn dan de instelling voor het bewaren van herstel punten. Als u bijvoorbeeld de herstel punten behoudt met de standaard instelling van 24 uur, stelt u de frequentie in op minder dan 24 uur.<br/><br/>Ze zijn ingewik kelder en nemen meer tijd in beslag dan crash-consistente moment opnamen.<br/><br/> Ze zijn van invloed op de prestaties van apps die worden uitgevoerd op een virtuele machine die is ingeschakeld voor replicatie. 

## <a name="failover-and-failback-process"></a>Failover- en failbackproces

Nadat de replicatie is ingesteld en u een nood herstel analyse (testfailover) uitvoert om te controleren of alles werkt zoals verwacht, kunt u failover en failback uitvoeren zoals u dat nodig hebt.

1. U voert een failover uit voor één computer of u maakt een herstel plan om meerdere Vm's tegelijk uit te voeren. Het voor deel van een herstel plan in plaats van de failover van één machine omvat:
    - U kunt app-afhankelijkheden model leren door alle virtuele machines in de app in één herstel plan op te nemen.
    - U kunt scripts, Azure-runbooks en pauzes voor hand matige acties toevoegen.
2. Nadat u de eerste failover hebt geactiveerd, voert u deze uit om toegang te krijgen tot de workload vanuit de Azure-VM.
3. Als uw primaire on-premises site weer beschikbaar is, kunt u voor bereidingen voor failback. Als u een failback wilt uitvoeren, moet u een failover-infra structuur instellen, waaronder:

    * **Tijdelijke proces server in azure**: als u een failback van Azure wilt uitvoeren, stelt u een Azure vm in om te fungeren als proces server voor het afhandelen van replicatie van Azure. U kunt deze virtuele machine verwijderen wanneer de failback is voltooid.
    * **VPN-verbinding**: als u een failback wilt uitvoeren, hebt u een VPN-verbinding (of ExpressRoute) van het Azure-netwerk nodig naar de on-premises site.
    * **Afzonderlijke hoofddoel server**: de Master doel server die is geïnstalleerd met de configuratie server op de on-premises virtuele VMWare-machine, verwerkt de failback. Als u grote hoeveel heden verkeer wilt herstellen, moet u voor dit doel een afzonderlijke on-premises Master doel server instellen.
    * **Failbackbeleid**: als u wilt repliceren naar de on-premises site, hebt u een failbackbeleid nodig. Dit beleid wordt automatisch gemaakt wanneer u een replicatie beleid maakt op basis van on-premises naar Azure.
4. Nadat de onderdelen zijn geïmplementeerd, vindt failback plaats in drie acties:

    - Fase 1: Beveilig de Azure Vm's opnieuw, zodat ze worden gerepliceerd van Azure naar de on-premises virtuele VMware-machines.
    -  Fase 2: een failover uitvoeren naar de on-premises site.
    - Fase 3: nadat de workloads zijn hersteld, schakelt u de replicatie voor de on-premises Vm's opnieuw in.
    
 

![Diagram waarin VMware-failback van Azure wordt weer gegeven.](./media/vmware-azure-architecture/enhanced-failback.png)


## <a name="next-steps"></a>Volgende stappen

Volg [deze zelf studie om de](vmware-azure-tutorial.md) replicatie van VMware naar Azure in te scha kelen.
