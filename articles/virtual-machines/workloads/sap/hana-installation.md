---
title: SAP HANA installeren op SAP HANA op Azure (grote exemplaren) | Microsoft Docs
description: SAP HANA installeren op een SAP HANA op Azure (grote exemplaren).
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f1c1397416943e3e25719204a5ccfd593559476e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557595"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>SAP HANA (grote instanties) installeren en configureren in azure

Voordat u dit artikel leest, moet u vertrouwd raken met de [algemene voor waarden van Hana grote instanties](hana-know-terms.md) en de [Hana-Sku's voor grote instanties](hana-available-skus.md).

De installatie van SAP HANA is uw verantwoordelijkheid. U kunt beginnen met de installatie van een nieuwe SAP HANA op de Azure-server (grote exemplaren) nadat u de connectiviteit tussen uw virtuele Azure-netwerken en de HANA-eenheid (s) hebt ingesteld. 

> [!Note]
> Per SAP-beleid moet de installatie van SAP HANA worden uitgevoerd door een persoon die is geslaagd voor het certificeren van gecertificeerde SAP-technologie, SAP HANA certificerings examen of die een SAP-gecertificeerde systeem integrator (SI) is.

Wanneer u van plan bent HANA 2,0 te installeren, raadpleegt u [SAP-ondersteuning opmerking #2235581-SAP Hana: ondersteunde besturings systemen](https://launchpad.support.sap.com/#/notes/2235581/E) om ervoor te zorgen dat het besturings systeem wordt ondersteund door de SAP Hana versie die u installeert. Het ondersteunde besturings systeem voor HANA 2,0 is beperkter dan het ondersteunde besturings systeem voor HANA 1,0. U moet ook controleren of de versie van het besturings systeem waarin u bent geïnteresseerd, wordt vermeld als ondersteund voor de specifieke HLI-eenheid in deze gepubliceerde [lijst](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Klik op de eenheid om de volledige gegevens op te halen uit de lijst met ondersteunde besturings systemen van die eenheid. 

Valideer het volgende voordat u begint met de HANA-installatie:
- [HLI-eenheid (s)](#validate-the-hana-large-instance-units)
- [Configuratie van het besturings systeem](#operating-system)
- [Netwerkconfiguratie](#networking)
- [Opslagconfiguratie](#storage)


## <a name="validate-the-hana-large-instance-units"></a>Valideer de HANA-eenheid voor grote instanties

Nadat u de HANA-eenheid voor grote instanties van micro soft hebt ontvangen, valideert u de volgende instellingen en past u deze zo nodig aan.

De **eerste stap** nadat u de Hana grote instantie hebt ontvangen en toegang en connectiviteit tot de instanties hebt ingesteld, is om Azure portal te controleren of de exemplaren worden weer gegeven met de juiste sku's en het besturings systeem. Lees het [besturings element Azure Hana-grote instanties door Azure Portal](./hana-li-portal.md) voor de stappen die nodig zijn om de controles uit te voeren.

De **tweede stap** nadat u de Hana grote instantie hebt ontvangen en toegang en connectiviteit tot de exemplaren hebt ingesteld, is door het besturings systeem van het exemplaar te registreren bij uw OS-provider. Deze stap omvat het registreren van uw SUSE Linux-besturings systeem in een exemplaar van SUSE SMT dat is geïmplementeerd in een virtuele machine in Azure. 

De HANA-eenheid voor grote instanties kan verbinding maken met dit SMT-exemplaar. (Zie voor meer informatie [hoe u een SMT-server instelt voor SuSE Linux](hana-setup-smt.md)). U moet ook uw Red Hat-besturings systeem registreren bij de Red Hat Subscription Manager waarmee u verbinding moet maken. Zie voor meer informatie de opmerkingen in [Wat is SAP Hana op Azure (grote exemplaren)?](./hana-overview-architecture.md?toc=/azure/virtual-machines/linux/toc.json). 

Deze stap is nodig voor het patchen van het besturings systeem, de verantwoordelijkheid van de klant. Voor SUSE vindt u de documentatie voor het installeren en configureren van SMT op deze pagina over [SMT-installatie](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html).

De **derde stap** is om te controleren op nieuwe patches en oplossingen van de specifieke release/versie van het besturings systeem. Controleer of het patch niveau van de HANA grote instantie de meest recente status heeft. Er zijn mogelijk situaties waarin de meest recente patches niet zijn opgenomen. Na het nemen van een HANA grote instantie-eenheid is het verplicht om te controleren of er patches moeten worden toegepast.

De **vierde stap** bestaat uit het raadplegen van de relevante SAP-notities voor het installeren en configureren van SAP Hana op de specifieke versie van het besturings systeem. Als gevolg van het wijzigen van aanbevelingen of wijzigingen in SAP-opmerkingen of-configuraties die afhankelijk zijn van de afzonderlijke installatie scenario's, kan micro soft niet altijd een een HANA-grote exemplaar-eenheid configureren. 

Daarom is het voor u als klant verplicht om de SAP-notities te lezen met betrekking tot SAP HANA voor uw exacte Linux-versie. Controleer ook de configuraties van de besturingssysteem versie/-versie en pas de configuratie-instellingen toe als u dat nog niet hebt gedaan.

Controleer met name de volgende para meters en pas deze uiteindelijk aan:

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

Vanaf SLES12 SP1 en RHEL 7,2 moeten deze para meters worden ingesteld in een configuratie bestand in de map/etc/sysctl.d. Bijvoorbeeld, een configuratie bestand met de naam 91-NetApp-HANA. conf moet worden gemaakt. Voor oudere SLES-en RHEL-releases moet u deze para meters instellen in/etc/sysctl. conf.

Voor alle RHEL-releases die beginnen met RHEL 6,3, moet u het volgende overwegen: 
- De para meter sunrpc.tcp_slot_table_entries = 128 moet worden ingesteld in/etc/modprobe. d/sunrpc-local. conf. Als het bestand niet bestaat, moet u het eerst maken door de vermelding toe te voegen: 
    - opties sunrpc tcp_max_slot_table_entries = 128

De **vijfde stap** is het controleren van de systeem tijd van de Hana grote exemplaar-eenheid. De exemplaren worden geïmplementeerd met een systeem tijd zone. Deze tijd zone vertegenwoordigt de locatie van de Azure-regio waarin de HANA grote instantie stempel zich bevindt. U kunt de systeem tijd of tijd zone wijzigen van de instanties waarvan u eigenaar bent. 

Als u meer exemplaren in uw Tenant wilt bezorgen, moet u de tijd zone van de nieuwe exemplaren aanpassen. Micro soft heeft geen inzicht in de tijd zone van het systeem die u hebt ingesteld met de exemplaren na de overdracht. Nieuwe geïmplementeerde exemplaren kunnen dus niet worden ingesteld in dezelfde tijd zone als het account dat u hebt gewijzigd in. Het is uw verantwoordelijkheid als klant voor het aanpassen van de tijd zone van de exemplaren die zijn overgedragen, indien nodig. 

De **zesde stap** is het controleren van etc/hosts. Wanneer de Blades worden ontvangen, hebben ze verschillende IP-adressen die zijn toegewezen voor verschillende doel einden. Controleer het bestand etc/hosts. Wanneer eenheden worden toegevoegd aan een bestaande Tenant, is het niet verwacht om etc/hosts van de zojuist geïmplementeerde systemen goed te laten onderhouden met de IP-adressen van systemen die eerder zijn geleverd. Het is uw verantwoordelijkheid als klant om ervoor te zorgen dat een nieuw geïmplementeerd exemplaar kan communiceren en de namen van de eenheden die u eerder in uw Tenant hebt geïmplementeerd, kunt omzetten. 


## <a name="operating-system"></a>Besturingssysteem

De wissel ruimte van de geleverde installatie kopie van het besturings systeem is ingesteld op 2 GB volgens de [SAP-ondersteunings opmerking #1999997-FAQ: SAP Hana geheugen](https://launchpad.support.sap.com/#/notes/1999997/E). Als klant, als u een andere instelling wilt, moet u deze zelf instellen.

[SuSE Linux Enterprise Server 12 SP1 voor SAP-toepassingen](https://www.suse.com/products/sles-for-sap/download/) is de distributie van Linux dat is geïnstalleerd voor SAP Hana op Azure (grote exemplaren). Deze distributie biedt specifiekere SAP-mogelijkheden (met inbegrip van vooraf ingestelde para meters voor het effectief uitvoeren van SAP op SLES).

Zie [Resource Library/White papers](https://www.suse.com/products/sles-for-sap/resource-library#white-papers) op de SuSE-website en [SAP op SuSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE) in het SAP Community Network (SCN) voor verschillende nuttige bronnen die betrekking hebben op de implementatie van SAP Hana op SLES (inclusief de configuratie van hoge Beschik baarheid, beveiliging die specifiek is voor SAP-bewerkingen en meer).

Hieronder vindt u aanvullende en nuttige SAP op SUSE-gerelateerde koppelingen:

- [SAP HANA op SUSE Linux-site](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Aanbevolen procedures voor SAP: in wachtrij plaatsen van replicatie – SAP net-Weaver op SUSE Linux Enter prise 12](https://www.scribd.com/document/351887168/SLES4SAP-NetWeaver-ha-guide-EnqRepl-12-color-en-pdf)
- [ClamSAP – SLES-virus beveiliging voor SAP](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (inclusief SLES 12 voor SAP-toepassingen)

Hieronder vindt u SAP-ondersteunings opmerkingen die van toepassing zijn op het implementeren van SAP HANA op SLES 12:

- [SAP-ondersteunings Opmerking #1944799: SAP HANA richt lijnen voor de installatie van het besturings systeem SLES](http://service.sap.com/sap/support/notes/1944799)
- [SAP-ondersteuning Opmerking #2205917: SAP HANA data base aanbevolen instellingen voor het besturings systeem voor SLES 12 voor SAP-toepassingen](https://launchpad.support.sap.com/#/notes/2205917/E)
- [SAP-ondersteuning Opmerking #1984787 – SUSE Linux Enterprise Server 12: installatie notities](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP-ondersteuning Opmerking #171356: SAP-software op Linux: algemene informatie](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP-ondersteunings Opmerking #1391070 – Linux-UUID-oplossingen](https://launchpad.support.sap.com/#/notes/1391070)

[Red Hat Enterprise Linux voor SAP Hana](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) is een andere aanbieding voor het uitvoeren van SAP Hana op Hana grote instanties. Releases van RHEL 7,2 en 7,3 zijn beschikbaar en worden ondersteund. 

Hieronder vindt u meer nuttige SAP voor koppelingen met een rood Hat-verband:
- [SAP Hana op Red Hat Linux-site](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat).

Hieronder vindt u SAP-ondersteunings opmerkingen die van toepassing zijn op het implementeren van SAP HANA op Red Hat:

- [SAP-ondersteuning Opmerking #2009879-SAP HANA-richt lijnen voor het besturings systeem Red Hat Enterprise Linux (RHEL)](https://launchpad.support.sap.com/#/notes/2009879/E)
- [SAP-ondersteunings Opmerking #2292690-SAP HANA DB: aanbevolen besturingssysteem instellingen voor RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)
- [SAP-ondersteunings Opmerking #1391070 – Linux-UUID-oplossingen](https://launchpad.support.sap.com/#/notes/1391070)
- [SAP-ondersteuning Opmerking #2228351-Linux: SAP HANA data base SPS 11 Revision 110 (of hoger) op RHEL 6 of SLES 11](https://launchpad.support.sap.com/#/notes/2228351)
- [SAP-ondersteunings Opmerking #2397039-FAQ: SAP op RHEL](https://launchpad.support.sap.com/#/notes/2397039)
- [SAP-ondersteunings Opmerking #2002167-Red Hat Enterprise Linux 7. x: installatie en upgrade](https://launchpad.support.sap.com/#/notes/2002167)

### <a name="time-synchronization"></a>Tijdsynchronisatie

SAP-toepassingen die zijn gebouwd op basis van de SAP NetWeaver-architectuur, zijn gevoelig voor tijd verschillen voor de verschillende onderdelen waaruit het SAP-systeem bestaat. SAP ABAP Short dumps met de fout titel van ZDATE \_ groot \_ tijd \_ verschil zijn waarschijnlijk bekend. Dat komt doordat deze korte dumps worden weer gegeven wanneer de systeem tijd van de verschillende servers of Vm's te ver uit elkaar liggen.

Voor SAP HANA op Azure (grote instanties), is de tijd synchronisatie die wordt uitgevoerd in azure niet van toepassing op de reken eenheden van de grote instantie stempels. Deze synchronisatie is niet van toepassing op het uitvoeren van SAP-toepassingen in systeem eigen Azure-Vm's, omdat Azure zeker weet dat de tijd van het systeem correct is gesynchroniseerd. 

Als gevolg hiervan moet u een afzonderlijke tijd server instellen die kan worden gebruikt door SAP-toepassings servers die worden uitgevoerd op virtuele Azure-machines en op de SAP HANA data base-exemplaren die worden uitgevoerd op HANA grote instanties. De opslag infrastructuur in grote instantie stempels is tijd gesynchroniseerd met NTP-servers.


## <a name="networking"></a>Netwerken
We gaan ervan uit dat u de aanbevelingen bij het ontwerpen van uw virtuele Azure-netwerken hebt gevolgd en dat deze virtuele netwerken worden verbonden met de HANA grote instanties, zoals beschreven in de volgende documenten:

- [Overzicht en architectuur van SAP HANA (grote instanties) op Azure](./hana-overview-architecture.md)
- [Infra structuur en connectiviteit van SAP HANA (grote instanties) op Azure](hana-overview-infrastructure-connectivity.md)

Er zijn enkele details over de netwerken van de afzonderlijke eenheden. Elke HANA grote instantie-eenheid wordt geleverd met twee of drie IP-adressen die zijn toegewezen aan twee of drie NIC-poorten. In HANA scale-out configuraties en het HANA-systeem replicatie scenario worden drie IP-adressen gebruikt. Een van de IP-adressen die zijn toegewezen aan de NIC van de eenheid, bevindt zich buiten de IP-adres groep van de server die wordt beschreven in [SAP Hana (grote exemplaren) overzicht en architectuur op Azure](./hana-overview-architecture.md).

Zie voor meer informatie over Ethernet-Details voor uw architectuur de door [HLI ondersteunde scenario's](hana-supported-scenario.md).

## <a name="storage"></a>Storage

De opslag indeling voor SAP HANA op Azure (grote instanties) wordt geconfigureerd door SAP HANA op Azure `service management` via door SAP aanbevolen richt lijnen. Deze richt lijnen zijn gedocumenteerd in het Witboek [SAP Hana Storage-vereisten](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html) . 

De ruwe grootte van de verschillende volumes met de verschillende HANA grote instanties Sku's wordt beschreven in [SAP Hana (grote exemplaren) overzicht en architectuur op Azure](hana-overview-architecture.md).

De naam conventies van de opslag volumes worden weer gegeven in de volgende tabel:

| Opslag gebruik | Naam van koppeling | Volumenaam | 
| --- | --- | ---|
| HANA-gegevens | /hana/data/SID/mnt0000\<m> | Opslag-IP:/hana_data_SID_mnt00001_tenant_vol |
| HANA-logboek | /hana/log/SID/mnt0000\<m> | Opslag-IP:/hana_log_SID_mnt00001_tenant_vol |
| HANA-logboek back-up | /hana/log/backups | Opslag-IP:/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA gedeeld | /hana/shared/SID | Opslag-IP:/hana_shared_SID_mnt00001_tenant_vol/Shared |
| usr/sap | /usr/sap/SID | Opslag-IP:/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*Sid* is de Hana-exemplaar systeem-id. 

*Tenant* is een interne inventarisatie van bewerkingen bij het implementeren van een Tenant.

HANA usr/sap delen hetzelfde volume. De nomenclatuur van de mountpoints bevat de systeem-ID van de HANA-instanties en het koppel nummer. Bij het omhoog schalen van implementaties is er slechts één koppeling, zoals mnt00001. In scale-out-implementaties ziet u aan de andere kant zoveel koppels als u werk nemers en hoofd knooppunten hebt. 

Voor scale-out-omgevingen, gegevens, logboeken en logboek back-upvolumes worden gedeeld en gekoppeld aan elk knoop punt in de scale-out-configuratie. Voor configuraties met meerdere SAP-instanties wordt een andere set volumes gemaakt en gekoppeld aan de HANA-eenheid voor grote exemplaren. Zie door [HLI ondersteunde scenario's](hana-supported-scenario.md)voor informatie over de opslag indeling voor uw scenario.

Wanneer u een exemplaar van de HANA-grote instantie bekijkt, realiseert u zich dat de eenheden worden geleverd met een ruimer schijf volume voor HANA/Data, en dat er een volume HANA/logboek/back-up is. De reden waarom de HANA/gegevens zo groot zijn gemaakt, is dat de opslag momentopnamen die u als klant hebt aangeboden, gebruikmaken van hetzelfde schijf volume. Hoe meer opslag momentopnamen u uitvoert, hoe meer ruimte wordt gebruikt door moment opnamen in de toegewezen opslag volumes. 

De HANA/logboek/back-upvolume is niet het volume voor database back-ups. Het formaat kan worden gebruikt als het back-upvolume voor het HANA-transactie logboek back-ups. Zie [SAP Hana (grote instanties) hoge Beschik baarheid en herstel na nood gevallen op Azure](hana-overview-high-availability-disaster-recovery.md)voor meer informatie. 

Naast de opslag die wordt weer gegeven, kunt u extra opslag capaciteit kopen in stappen van 1 TB. Deze extra opslag ruimte kan worden toegevoegd als nieuwe volumes aan een HANA grote instantie.

In het geval van een onboarding met SAP HANA op Azure `service management` specificeert de klant een gebruikers-id (UID) en groeps-id (GID) voor de groep sidadm gebruiker en sapsys (bijvoorbeeld: 1.000.500). Tijdens de installatie van het SAP HANA-systeem moet u dezelfde waarden gebruiken. Omdat u meerdere HANA-instanties wilt implementeren op een eenheid, krijgt u meerdere sets volumes (één set voor elk exemplaar). Als gevolg hiervan moet u bij de implementatie tijd het volgende definiëren:

- De SID van de verschillende HANA-instanties (sidadm is afgeleid van de instantie).
- De geheugen grootten van de verschillende HANA-exemplaren. De geheugen grootte per exemplaar bepaalt de grootte van de volumes in elke afzonderlijke volumeset.

Op basis van aanbevelingen voor opslag providers zijn de volgende koppelings opties geconfigureerd voor alle gekoppelde volumes (sluit de opstart-LUN uit):

- NFS RW, vers = 4, hard, Timeo = 600, rsize = 1048576, wsize = 1048576, intr, noatime, lock 0 0

Deze koppel punten worden geconfigureerd in bestand/etc/fstab, zoals wordt weer gegeven in de volgende afbeeldingen:

![fstab aan gekoppelde volumes in HANA grote instantie-eenheid](./media/hana-installation/image1_fstab.PNG)

De uitvoer van de opdracht VG-h voor een S72m HANA-grote instantie-eenheid ziet er als volgt uit:

![Scherm opname toont uitvoer van de opdracht voor HANA grote instantie-eenheid.](./media/hana-installation/image2_df_output.PNG)


De opslag controller en knoop punten in de stempels van grote instanties worden gesynchroniseerd met NTP-servers. Wanneer u de SAP HANA op Azure-eenheden (grote instanties) en Azure-Vm's synchroniseert op basis van een NTP-server, mag er geen significante tijd tussen de infra structuur en de reken eenheden in azure of grote instantie stempels zijn.

Als u SAP HANA wilt optimaliseren voor de opslag die hieronder wordt gebruikt, stelt u de volgende SAP HANA configuratie parameters in:

- max_parallel_io_requests 128
- async_read_submit op
- async_write_submit_active op
- alles async_write_submit_blocks
 
Voor SAP HANA 1,0-versies tot SPS12, kunnen deze para meters worden ingesteld tijdens de installatie van de SAP HANA-data base, zoals beschreven in [SAP note #2267798-configuratie van de SAP Hana-data base](https://launchpad.support.sap.com/#/notes/2267798).

U kunt de para meters na de installatie van de SAP HANA-data base ook configureren met behulp van het hdbparam-Framework. 

De opslag die wordt gebruikt in HANA grote instanties heeft een beperking voor de bestands grootte. De limiet voor de [grootte is 16 TB](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html) per bestand. In tegens telling tot beperkingen van de bestands grootte in de EXT3-bestands systemen is HANA niet impliciet van de opslag beperking die wordt afgedwongen door de HANA-opslag voor grote instanties. Als gevolg van een resultaat van HANA wordt niet automatisch een nieuw gegevens bestand gemaakt wanneer de maximale bestands grootte van 16TB is bereikt. Aangezien HANA probeert het bestand groter dan 16 TB te laten groeien, worden fouten gerapporteerd en de index server wordt aan het einde gecrasht.

> [!IMPORTANT]
> U moet de volgende para meters instellen in het configuratie bestand SAP HANA global.ini om te voor komen dat HANA probeert gegevens bestanden te verg Roten die groter zijn dan de bestands grootte limiet van 16 TB van HANA-bestanden voor grote exemplaren.
> 
> - datavolume_striping = True
> - datavolume_striping_size_gb = 15000
> - Zie ook SAP-notitie [#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - Houd rekening met SAP Note [#2631285](https://launchpad.support.sap.com/#/notes/2631285)


Met SAP HANA 2,0 is het hdbparam-Framework afgeschaft. Als gevolg hiervan moeten de para meters worden ingesteld met behulp van SQL-opdrachten. Zie [SAP note #2399079: eliminating of hdbparam in Hana 2](https://launchpad.support.sap.com/#/notes/2399079)(Engelstalig) voor meer informatie.

Raadpleeg de door [HLI ondersteunde scenario's](hana-supported-scenario.md) voor meer informatie over de opslag indeling voor uw architectuur.


**Volgende stappen**

- Raadpleeg de [Hana-installatie op HLI](hana-example-installation.md)










































 







 
