---
title: Windows Server-failovercluster op Azure VMware Solution vSAN met systeem eigen gedeelde schijven
description: Windows Server failover cluster (WSFC) in te stellen op de Azure VMware-oplossing en gebruik te maken van oplossingen waarvoor WSFC-functionaliteit is vereist.
ms.topic: how-to
ms.date: 03/08/2021
ms.openlocfilehash: 84bb846cd3fb6dd1b138308670db7ccf122b2187
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102491274"
---
# <a name="windows-server-failover-cluster-on-azure-vmware-solution-vsan-with-native-shared-disks"></a>Windows Server-failovercluster op Azure VMware Solution vSAN met systeem eigen gedeelde schijven

In dit artikel wordt beschreven hoe u Windows Server-failovercluster kunt instellen in de Azure VMware-oplossing. De implementatie in dit artikel is bedoeld voor het testen van concept-en test doeleinden.

WSFC (Windows Server failover cluster), voorheen bekend als micro soft service Cluster service (MSCS), is een functie van het Windows Server-besturings systeem (OS). WSFC is een bedrijfskritische functie en voor veel toepassingen is vereist. WSFC is bijvoorbeeld vereist voor de volgende configuraties:

- SQL Server geconfigureerd als:
  - Altijd op FCI (failover cluster instance), voor hoge Beschik baarheid op exemplaar niveau.
  - AlwaysOn-beschikbaarheids groep (AG) voor hoge Beschik baarheid op database niveau.
- Windows-bestands Services:
  - Algemene bestands share die wordt uitgevoerd op het actieve cluster knooppunt.
  - Scale-Out Bestands server (SOFS), die bestanden opslaat in een CSV (cluster Shared volumes).
  - Opslagruimten Direct (S2D); lokale schijven die worden gebruikt voor het maken van opslag groepen op verschillende cluster knooppunten.

U kunt het WSFC-cluster hosten op verschillende Azure VMware-oplossings instanties, ook wel cluster-over-Box (CAB) genoemd. U kunt ook het WSFC-cluster op één Azure VMware-oplossings knooppunt plaatsen. Deze configuratie staat bekend als cluster-in-a-Box (CIB). Het gebruik van een CIB-oplossing voor een productie-implementatie wordt niet aanbevolen. Als er een fout optreedt in het knoop punt voor één Azure VMware-oplossing, zouden alle WSFC-cluster knooppunten worden uitgeschakeld en zou de toepassing downtime kunnen ondervinden. Voor de Azure VMware-oplossing zijn mini maal drie knoop punten in een particulier Cloud cluster vereist.

Het is belang rijk dat u een ondersteunde WSFC-configuratie implementeert. U wilt dat uw oplossing wordt ondersteund op vSphere en met de Azure VMware-oplossing. VMware bevat een gedetailleerd document over WSFC op vSphere 6,7, [Setup voor failover clustering en micro soft Cluster service](https://docs.vmware.com/en/VMware-vSphere/6.7/vsphere-esxi-vcenter-server-67-setup-mscs.pdf).

Dit artikel richt zich op WSFC op Windows Server 2016 en Windows Server 2019. Oudere versies van Windows Server hebben geen [algemene ondersteuning](https://support.microsoft.com/lifecycle/search?alpha=windows%20server) en daarom worden ze hier niet in overweging gegeven.

U moet eerst [een WSFC maken](https://docs.microsoft.com/windows-server/failover-clustering/create-failover-cluster). Zie [failover clustering in Windows Server](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)voor meer informatie over WSFC. Gebruik de informatie die we in dit artikel bieden voor de specifieke gegevens van een WSFC-implementatie op de Azure VMware-oplossing.

## <a name="prerequisites"></a>Vereisten

- Azure VMware-oplossings omgeving
- Installatie media van micro soft Windows Server-besturings systeem

## <a name="reference-architecture"></a>Referentiearchitectuur

De Azure VMware-oplossing biedt systeem eigen ondersteuning voor gevirtualiseerde WSFC. Het ondersteunt SCSI-3 permanente reserve ringen (SCSI3PR) op een virtuele-schijf niveau. Deze ondersteuning is vereist voor WSFC om toegang te arbitrate tot een gedeelde schijf tussen knoop punten. Ondersteuning van SCSI3PRs maakt het configureren van WSFC mogelijk met een schijf resource die wordt gedeeld tussen virtuele machines in vSAN-gegevens opslag.

In het volgende diagram ziet u de architectuur van virtuele WSFC-knoop punten in een privécloud van Azure VMware-oplossing. Er wordt weer gegeven waar de Azure VMware-oplossing zich bevindt, met inbegrip van de virtuele WSFC-servers (Red Box), ten opzichte van het bredere Azure-platform. Dit diagram illustreert een typische hub-spoke-architectuur, maar een vergelijk bare configuratie is mogelijk met het gebruik van Azure Virtual WAN. Beide bieden allemaal de waarde andere Azure-Services die u kunt meenemen.

[![Diagram van de architectuur van WSFC virtuele knoop punten op een privécloud met Azure VMware-oplossing.](media/windows-server-failover-cluster/windows-server-failover-architecture.png)](media/windows-server-failover-cluster/windows-server-failover-architecture.png#lightbox)

## <a name="supported-configurations"></a>Ondersteunde configuraties

Op dit moment worden de volgende configuraties ondersteund:

- Micro soft Windows Server 2012 of hoger.
- Maxi maal vijf failover clustering-knoop punten per cluster.
- Maxi maal vier PARAVIRTUAL SCSI-adapters per VM.
- Maxi maal 64 schijven per PARAVIRTUAL SCSI-adapter.

## <a name="virtual-machine-configuration-requirements"></a>Configuratie vereisten voor virtuele machines

### <a name="wsfc-node-configuration-parameters"></a>Configuratie parameters voor WSFC-knoop punt

- Installeer de nieuwste VMware-Hulpprogram Ma's op elk WSFC-knoop punt.
- Het is niet mogelijk om niet-gedeelde en gedeelde schijven te mengen op één virtuele SCSI-adapter. Bijvoorbeeld als de systeem schijf (station C:) is gekoppeld aan SCSI0:0, de eerste gedeelde schijf wordt gekoppeld aan SCSI1:0. Een VM-knoop punt van een WSFC heeft dezelfde virtuele SCSI-controller als een gewone VM-Maxi maal vier (4) virtuele SCSI-controllers.
- SCSI-Id's van virtuele schijven moeten consistent zijn tussen alle Vm's die als host fungeren voor knoop punten van hetzelfde WSFC.

| **Onderdeel** | **Vereisten** |
| --- | --- |
| VM-hardware-versie | 11 of hoger voor de ondersteuning van Live vMotion. |
| Virtuele NIC | VMXNET3 geparavirtualiseerde netwerk interface kaart (NIC); Schakel de in-Guest Windows Receive Side Scaling (RSS) in op de virtuele NIC. |
| Geheugen | Gebruik volledig VM-reserverings geheugen voor knoop punten in het WSFC-cluster. |
| Verhoog de I/O-time-out van elk WSFC-knoop punt. | Wijzig HKEY \_ lokale \_ MACHINE\System\CurrentControlSet\Services\Disk\TimeOutValueSet in 60 seconden of meer. (Als u het cluster opnieuw maakt, wordt deze waarde mogelijk opnieuw ingesteld op de standaard instelling, dus moet u deze opnieuw wijzigen.) |
| Controle van Windows-cluster status | De waarde van de para meter SameSubnetThreshold van Windows cluster Health Monitoring moet worden gewijzigd zodat er mini maal 10 gemiste heartbeats zijn toegestaan. Dit is [de standaard waarde in Windows Server 2016](https://techcommunity.microsoft.com/t5/failover-clustering/tuning-failover-cluster-network-thresholds/ba-p/371834). Deze aanbeveling is van toepassing op alle toepassingen die gebruikmaken van WSFC, inclusief gedeelde en niet-gedeelde schijven. |

### <a name="wsfc-node---boot-disks-configuration-parameters"></a>Configuratie parameters voor het WSFC-knoop punt-opstart schijven


| **Onderdeel** | **Vereisten** |
| --- | --- |
| Type SCSI-controller | LSI Logic SAS |
| Schijf modus | Virtueel |
| Delen van SCSI-bus | Geen |
| Wijzig geavanceerde instellingen voor een virtuele SCSI-controller die als host fungeert voor het opstart apparaat. | Voeg de volgende geavanceerde instellingen toe aan elk WSFC-knoop punt:<br /> scsix. returnNoConnectDuringAPD = "TRUE"<br />scsix. returnBusyOnNoConnectStatus = "FALSE"<br />Waarbij X het ID-nummer van de SCSI-bus controller van het opstart apparaat is. X is standaard ingesteld op 0. |

### <a name="wsfc-node---shared-disks-configuration-parameters"></a>Configuratie parameters van het WSFC-knoop punt-gedeelde schijven


| **Onderdeel** | **Vereisten** |
| --- | --- |
| Type SCSI-controller | VMware-Paravirtualize (PARAVIRTUAL SCSI) |
| Schijf modus | Onafhankelijk-permanent (stap 2 in de onderstaande afbeelding). Met deze instelling zorgt u ervoor dat alle schijven worden uitgesloten van moment opnamen. Moment opnamen worden niet ondersteund voor op WSFC gebaseerde Vm's. |
| Delen van SCSI-bus | Fysiek (stap 1 in de onderstaande afbeelding) |
| Vlag voor meerdere schrijf markeringen | Niet gebruikt |
| Schijfindeling | Dik ingericht. (EZT) is niet vereist voor vSAN.) |

:::image type="content" source="media/windows-server-failover-cluster/edit-settings-virtual-hardware.png" alt-text="Een scherm opname met de pagina instellingen bewerken voor virtuele hardware.":::

## <a name="non-supported-scenarios"></a>Niet-ondersteunde scenario's

De volgende functies worden niet ondersteund voor WSFC in azure VMware-oplossing:

- NFS-gegevens archieven
- Opslagruimten
- vSAN met behulp van iSCSI-service
- vSAN uitgerekt cluster
- Verbeterde compatibiliteit met vMotion (EVC)
- vSphere fout tolerantie (FT)
- Momentopnamen
- Live (online) Storage vMotion
- N-poort-ID-virtualisatie (NPIV)

Dynamische wijzigingen in de hardware van de virtuele machine kunnen de heartbeat tussen de WSFC-knoop punten verstoren.

De volgende activiteiten worden niet ondersteund en kunnen een WSFC-knooppunt failover veroorzaken:

- Dynamisch toevoegen van geheugen
- Warm toevoegen van CPU
- Moment opnamen gebruiken
- De grootte van een gedeelde schijf verg Roten
- De status van de virtuele machine onderbreken en hervatten
- Geheugen overschrijding die leidt tot ESXi wisseling of VM-geheugen ballon
- Het lokale VMDK-bestand uitbreiden, zelfs als dit niet is gekoppeld aan de SCSI bus-controller voor delen

## <a name="configure-wsfc-with-shared-disks-on-azure-vmware-solution-vsan"></a>WSFC configureren met gedeelde schijven op de Azure VMware-oplossing vSAN

1. Zorg ervoor dat er een Active Directory omgeving beschikbaar is.
2. Maak virtuele machines (Vm's) op de vSAN-gegevens opslag.
3. Schakel alle virtuele machines in, Configureer de hostnaam, IP-adressen, voeg alle virtuele machines toe aan een Active Directory domein en installeer meest recente beschik bare updates van het besturings systeem.
4. Installeer de nieuwste VMware-Hulpprogram Ma's.
5. Schakel de functie Windows Server failover cluster in en configureer deze op elke virtuele machine.
6. Een cluster-Witness configureren voor quorum (een bestands share-Witness werkt prima).
7. Schakel alle knoop punten van het WSFC-cluster uit.
8. Voeg een of meer Pvscsi SCSI-controllers (Maxi maal vier) toe aan elk VM-onderdeel van de WSFC. Gebruik de instellingen volgens de voor gaande alinea's.
9. Voeg op het eerste cluster knooppunt alle benodigde gedeelde schijven toe met behulp van **nieuwe**  >  **harde schijf** van apparaat toevoegen. Het delen van schijven moet blijven staan als niet- **opgegeven** (standaard) en schijf modus als **onafhankelijk permanent**. Koppel deze aan de controller (s) die u in de vorige stappen hebt gemaakt.
10. Ga door met de resterende WSFC-knoop punten. Voeg de schijven die zijn gemaakt in de vorige stap toe door **nieuwe apparaat toevoegen**  >  **bestaande vaste schijf** te selecteren. Zorg ervoor dat u dezelfde SCSI-Id's voor de schijf op alle WSFC-knoop punten behoudt.
11. Schakel het eerste WSFC-knoop punt in. Meld u aan en open de schijf beheer console (MMC). Zorg ervoor dat de toegevoegde gedeelde schijven kunnen worden beheerd door het besturings systeem en worden geïnitialiseerd. Schijven Format teren en een stationsletter toewijzen.
12. Schakel de andere WSFC-knoop punten uit.
13. Voeg de schijf toe aan het WSFC-cluster met behulp van de **wizard schijf toevoegen** en voeg deze toe aan een cluster Shared volume.
14. Test een failover met behulp van de **wizard schijf verplaatsen** en controleer of het WSFC-cluster met gedeelde schijven goed werkt.
15. Voer de **wizard validatie cluster** uit om te controleren of het cluster en de bijbehorende knoop punten goed werken.

    Het is belang rijk dat u de volgende specifieke items van de cluster validatie test in acht houdt:

       - **Permanente reserve ring van opslag ruimten valideren**. Als u geen opslag ruimten gebruikt voor uw cluster (zoals op Azure VMware Solution vSAN), is deze test niet van toepassing. U kunt de resultaten van de test permanente reserve ring van opslag ruimten valideren negeren, inclusief deze waarschuwing. U kunt deze test uitsluiten om waarschuwingen te voor komen.
        
      - **Netwerk communicatie valideren**. De cluster validatie test genereert een waarschuwing dat er slechts één netwerk interface per cluster knooppunt beschikbaar is. U kunt deze waarschuwing negeren. De Azure VMware-oplossing biedt de vereiste Beschik baarheid en prestaties omdat de knoop punten zijn verbonden met een van de NSX-T-segmenten. Bewaar dit item echter als onderdeel van de cluster validatie test, omdat hierdoor andere aspecten van de netwerk communicatie worden gevalideerd.

16. Maak een DRS-regel voor het scheiden van de virtuele WSFC-Vm's tussen Azure VMware-oplossings knooppunten. Gebruik de volgende regels: één host-naar-VM-affiniteit en een regel voor de anti-affiniteit van VM-naar-VM. Op deze manier kunnen cluster knooppunten niet worden uitgevoerd op dezelfde Azure VMware-oplossings host.

    >[!NOTE]
    > Hiervoor moet u een ticket voor ondersteunings aanvraag maken. Onze ondersteunings organisatie van Azure kan u hierbij helpen.

## <a name="related-information"></a>Gerelateerde informatie

- [Failoverclustering in Windows Server](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [Richt lijnen voor micro soft Clustering op vSphere (1037959) (vmware.com)](https://kb.vmware.com/s/article/1037959)
- [Over het instellen van failover clustering en micro soft Cluster service (vmware.com)](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.mscs.doc/GUID-1A2476C0-CA66-4B80-B6F9-8421B6983808.html)
- [vSAN 6,7 U3-WSFC met gedeelde schijven &amp; SCSI-3 permanente reserve ringen (VMware.com)](https://blogs.vmware.com/virtualblocks/2019/08/23/vsan67-u3-wsfc-shared-disksupport/)
- [Limieten voor Azure VMware-oplossingen](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-vmware-solution-limits)

## <a name="next-steps"></a>Volgende stappen

Nu u een WSFC-oplossing in azure VMware hebt ingesteld, kunt u het volgende weten:

- Instellen van uw nieuwe WSFC door meer toepassingen toe te voegen waarvoor de WSFC-functionaliteit is vereist. Bijvoorbeeld SQL Server en SAP ASCS.
- Een back-upoplossing instellen.
  - [Azure Backup Server voor de Azure VMware-oplossing instellen](https://docs.microsoft.com/azure/azure-vmware/set-up-backup-server-for-azure-vmware-solution)
  - [Back-upoplossingen voor virtuele machines van Azure VMware Solution](https://docs.microsoft.com/azure/azure-vmware/ecosystem-back-up-vms)
