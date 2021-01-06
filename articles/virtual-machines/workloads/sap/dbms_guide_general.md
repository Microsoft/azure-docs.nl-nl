---
title: Overwegingen voor Azure Virtual Machines DBMS-implementatie voor SAP-workload | Microsoft Docs
description: Overwegingen voor de implementatie van Azure Virtual Machines DBMS voor SAP-workloads
author: msjuergent
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 09/20/2020
ms.author: juergent
ms.reviewer: cynthn
ms.openlocfilehash: 5638b60b60f3952ab40fa000e457082f4748ac35
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "96484342"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Overwegingen voor de implementatie van Azure Virtual Machines DBMS voor SAP-workloads
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png



Deze hand leiding maakt deel uit van de documentatie over het implementeren en implementeren van SAP-software op Microsoft Azure. Lees, voordat u deze hand leiding leest, de [plannings-en implementatie handleiding][planning-guide] en de artikelen waarnaar wordt verwezen in de plannings handleiding. In dit document worden de algemene implementatie aspecten van SAP-gerelateerde DBMS-systemen op Microsoft Azure virtuele machines (Vm's) beschreven met behulp van de Azure Infrastructure as a Service (IaaS)-mogelijkheden.

Het artikel vormt een aanvulling op de SAP-installatie documentatie en SAP-notities, die de primaire resources vertegenwoordigen voor installaties en implementaties van SAP-software op bepaalde platformen.

In dit document worden overwegingen geïntroduceerd voor het uitvoeren van SAP-gerelateerde DBMS-systemen in azure Vm's. Er zijn in dit hoofd stuk enkele verwijzingen naar specifieke DBMS-systemen. In plaats daarvan worden de specifieke DBMS-systemen na dit document verwerkt in dit artikel.

## <a name="definitions"></a>Definities
In het hele document worden deze voor waarden gebruikt:

* **IaaS**: Infrastructure as a service.
* **PaaS**: platform as a service.
* **SaaS**: software als een service.
* **SAP-onderdeel**: een afzonderlijke SAP-toepassing, zoals het ERP Central-onderdeel (ECC), Business WAREHOUSE (BW), Solution Manager of Enterprise Portal (EP). SAP-onderdelen kunnen worden gebaseerd op traditionele ABAP of Java-technologieën of op een toepassing die niet op netbasis is gebaseerd, zoals bedrijfs objecten.
* **SAP-omgeving**: een of meer sap-onderdelen die logisch zijn gegroepeerd om een zakelijke functie uit te voeren, zoals ontwikkeling, kwaliteits bewaking, training, herstel na nood gevallen of productie.
* **SAP liggend**: deze term verwijst naar de volledige SAP-assets in het IT-landschap van een klant. SAP liggend omvat alle productie-en niet-product omgevingen.
* **SAP-systeem**: de combi natie van een DBMS-laag en een toepassingslaag van, bijvoorbeeld een SAP ERP-ontwikkelings systeem, een SAP Business Warehouse-test systeem of een SAP CRM-productie systeem. In azure-implementaties wordt het delen van deze twee lagen tussen on-premises en Azure niet ondersteund. Als gevolg hiervan is een SAP-systeem on-premises geïmplementeerd of geïmplementeerd in Azure. U kunt de verschillende systemen van een SAP-landschap in azure of on-premises implementeren. U kunt bijvoorbeeld het ontwikkel-en test systeem van SAP CRM implementeren in azure, maar het productie systeem van SAP CRM on-premises implementeren.
* **Cross-premises**: hierin wordt een scenario beschreven waarin vm's worden geïmplementeerd naar een Azure-abonnement met een site-naar-site-, multi site-of Azure ExpressRoute-verbinding tussen de on-premises data centers en Azure. In algemene documentatie over Azure worden dit soort implementaties ook beschreven als scenario's voor cross-premises. 

    De reden hiervoor is om on-premises domeinen, on-premises Active Directory en on-premises DNS uit te breiden naar Azure. De on-premises liggende lands worden uitgebreid naar de Azure-assets van het abonnement. Met deze uitbrei ding kunnen de Vm's deel uitmaken van het on-premises domein. Domein gebruikers van het on-premises domein hebben toegang tot de servers en kunnen services uitvoeren op deze Vm's, zoals DBMS-Services. Communicatie en naam omzetting tussen Vm's die zijn geïmplementeerd op locatie en Vm's die in azure zijn geïmplementeerd, zijn mogelijk. Dit scenario is het meest voorkomende scenario dat wordt gebruikt voor het implementeren van SAP-assets op Azure. Zie [planning en ontwerp voor VPN-gateway](../../../vpn-gateway/vpn-gateway-about-vpngateways.md)voor meer informatie.

> [!NOTE]
> Cross-premises implementaties van SAP-systemen zijn waar Azure virtual machines die SAP-systemen uitvoeren, lid zijn van een on-premises domein en worden ondersteund voor SAP-productie systemen. Cross-premises configuraties worden ondersteund voor het implementeren van onderdelen of het volt ooien van SAP-landschappen in Azure. Zelfs voor het uitvoeren van het volledige SAP-landschap in azure moeten deze Vm's deel uitmaken van een on-premises domein en Active Directory/LDAP. 
>
> In vorige versies van de documentatie werden hybride scenario's beschreven. De term *hybride* wordt geroot in het feit dat er een cross-premises verbinding is tussen on-premises en Azure. In dit geval betekent hybride ook dat de virtuele machines in azure deel uitmaken van de on-premises Active Directory.
>
>

In sommige micro soft-documentatie worden cross-premises scenario's enigszins anders beschreven, met name voor configuraties met hoge Beschik baarheid in DBMS. In het geval van SAP-gerelateerde documenten, wordt het cross-premises scenario omlaag gekook voor site-naar-site-of particuliere [ExpressRoute](https://azure.microsoft.com/services/expressroute/) -connectiviteit en een SAP-landschap dat is gedistribueerd tussen on-premises en Azure.

## <a name="resources"></a>Resources
Er zijn andere artikelen beschikbaar op SAP-werk belasting op Azure. Begin met [SAP-workload op Azure: Ga](./get-started.md) aan de slag en kies vervolgens uw interesse gebied.

De volgende SAP-opmerkingen zijn gerelateerd aan SAP on Azure ten aanzien van het gebied dat in dit document wordt besproken.

| Nummer van notitie | Titel |
| --- | --- |
| [1928533] |SAP-toepassingen op Azure: ondersteunde producten en Azure VM-typen |
| [2015553] |SAP op Microsoft Azure: vereisten voor ondersteuning |
| [1999351] |Problemen met verbeterde Azure-bewaking voor SAP oplossen |
| [2178632] |Belangrijkste meet waarden voor SAP op Microsoft Azure |
| [1409604] |Virtualisatie op Windows: uitgebreide bewaking |
| [2191498] |SAP op Linux met Azure: uitgebreide bewaking |
| [2039619] |SAP-toepassingen op Microsoft Azure met behulp van de Oracle-Data Base: ondersteunde producten en versies |
| [2233094] |DB6: SAP-toepassingen op Azure met behulp van IBM DB2 voor Linux, UNIX en Windows: aanvullende informatie |
| [2243692] |Linux on Microsoft Azure (IaaS) VM: SAP-licentie problemen |
| [1984787] |SUSE LINUX Enter prise Server 12: installatie notities |
| [2002167] |Red Hat Enterprise Linux 7. x: installatie en upgrade |
| [2069760] |Oracle Linux 7. x SAP-installatie en-upgrade |
| [1597355] |Aanbeveling voor wissel geheugen voor Linux |
| [2171857] |Oracle Database 12c: bestandssysteem ondersteuning op Linux |
| [1114181] |Oracle Database 11g: bestandssysteem ondersteuning op Linux |


Zie de [SAP Community-wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)voor meer informatie over alle SAP-notities voor Linux.

U hebt een praktische kennis van Microsoft Azure architectuur nodig en hoe Microsoft Azure virtuele machines worden geïmplementeerd en beheerd. Zie de [Azure-documentatie](../../../index.yml)voor meer informatie.

In het algemeen zijn de installatie en configuratie van Windows, Linux en DBMS in wezen hetzelfde als elke virtuele machine of bare metal-machine die u on-premises installeert. Er zijn een aantal architectuur-en systeem beheer implementatie beslissingen die afwijken wanneer u Azure IaaS gebruikt. In dit document worden de specifieke architectuur-en systeem beheer verschillen beschreven die moeten worden voor bereid wanneer u Azure IaaS gebruikt.


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Opslag structuur van een virtuele machine voor RDBMS-implementaties
Als u dit hoofd stuk wilt volgen, leest en begrijpt u de informatie die wordt weer gegeven in:

- [Azure Virtual Machines planning en implementatie voor SAP net-Weaver](./planning-guide.md)
- [Azure Storage-typen voor SAP-workload](./planning-guide-storage.md)
- [Welke SAP-software wordt ondersteund voor Azure-implementaties](./sap-supported-product-on-azure.md)
- [SAP-workload in scenario's met virtuele Azure-machines](./sap-planning-supported-configurations.md) 

U moet inzicht hebben in de verschillende VM-Series en de verschillen tussen de standaard-en Premium-opslag voordat u dit hoofd stuk leest. 

Voor Azure Block Storage wordt het gebruik van Azure Managed disks nadrukkelijk aanbevolen. Lees het artikel [Introduction to Managed disks for Azure vm's](../../managed-disks-overview.md)voor meer informatie over Azure Managed disks.

In een basis configuratie raden wij doorgaans een implementatie structuur aan waarbij het besturings systeem, DBMS en mogelijke SAP binaire bestanden van de database bestanden gescheiden zijn. Als u eerdere aanbevelingen wijzigt, raden we u aan afzonderlijke Azure-schijven te hebben voor:

- Het besturings systeem (basis-VHD of VHD)
- Uitvoer bare bestanden van het database beheersysteem
- SAP uitvoer bare bestanden zoals/usr/sap

Een configuratie die deze onderdelen in drie verschillende Azure-schijven scheidt, kan leiden tot hogere tolerantie, omdat overmatige logboek-of dump schrijf bewerkingen door de bestanden DBMS of SAP worden verstoord door de schijf quota van de besturingssysteem schijf. 

De logboek bestanden voor de DBMS-gegevens en-trans actie/opnieuw worden opgeslagen in door Azure ondersteunde blok opslag of Azure NetApp Files. Ze worden opgeslagen op afzonderlijke schijven en als logische schijven gekoppeld aan de oorspronkelijke VM van de installatie kopie van het besturings systeem van Azure. Voor Linux-implementaties worden verschillende aanbevelingen gedocumenteerd, met name voor SAP HANA. Lees het artikel [Azure Storage typen voor de SAP-werk belasting](./planning-guide-storage.md) voor de mogelijkheden en ondersteuning van de verschillende opslag typen voor uw scenario. 

Wanneer u de indeling van de schijf plant, zoekt u de beste balans tussen deze items:

* Het aantal gegevens bestanden.
* Het aantal schijven dat de bestanden bevat.
* De IOPS-quota van een enkele schijf of NFS-share.
* De gegevens doorvoer per schijf of NFS-share.
* Het aantal extra gegevens schijven dat mogelijk is per VM-grootte.
* De totale opslag-of netwerk doorvoer van een virtuele machine kan bieden.
* De latentie van verschillende Azure Storage typen kan worden geboden.
* VM-service overeenkomsten.

Azure dwingt een IOPS-quotum per gegevens schijf of NFS-share af. Deze quota wijken af voor schijven die worden gehost op de verschillende Azure Block Storage-oplossingen of-shares. De I/O-latentie verschilt ook van deze verschillende opslag typen. 

Elk van de verschillende VM-typen heeft een beperkt aantal gegevens schijven dat u kunt bijvoegen. Een andere beperking is dat alleen bepaalde VM-typen kunnen worden gebruikt, bijvoorbeeld Premium-opslag. Normaal gesp roken besluit u een bepaald VM-type te gebruiken op basis van de CPU-en geheugen vereisten. U kunt ook de vereisten voor IOPS, latentie en schijf doorvoer overwegen die meestal worden geschaald met het aantal schijven of het type Premium-opslag schijven. Het aantal IOPS en de door Voer die door elke schijf moet worden behaald, kan de schijf grootte bepalen, met name voor Premium Storage.

> [!NOTE]
> Voor DBMS-implementaties wordt aangeraden Azure Premium Storage, Ultra disk of Azure NetApp Files op basis van NFS-shares (uitsluitend voor SAP HANA) voor gegevens, transactie logboek bestanden of opnieuw te installeren. Het maakt niet uit of u productie-of niet-product systemen wilt implementeren.

> [!NOTE]
> Om te profiteren van de [Single VM-Sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)van Azure, moeten alle schijven die zijn gekoppeld Azure Premium Storage of een Azure Ultra disk-type zijn, inclusief de basis-VHD (Azure Premium Storage).

> [!NOTE]
> Het hosten van hoofd database bestanden, zoals gegevens en logboek bestanden, van SAP-data bases op opslaghardware die zich bevinden in gegevens centra van derden naast Azure data centers wordt niet ondersteund. Opslag die wordt aangeboden via software-apparaten die worden gehost in virtuele machines van Azure, wordt ook niet ondersteund voor deze use-case. Voor SAP DBMS-workloads wordt alleen opslag die wordt weer gegeven als systeem eigen Azure-service ondersteund voor de gegevens-en transactie logboek bestanden van SAP-data bases in het algemeen. Verschillende DBMS ondersteunen mogelijk verschillende typen Azure-opslag. Raadpleeg voor meer informatie het artikel [Azure Storage typen voor SAP-workload](./planning-guide-storage.md)

De plaatsing van de database bestanden en het logboek bestand en het type Azure Storage dat u gebruikt, wordt gedefinieerd door de vereisten voor IOPS, latentie en door voer. Met name voor Azure Premium Storage om voldoende IOPS te krijgen, is het mogelijk dat u meerdere schijven gebruikt of een grotere Premium-opslag schijf gebruikt. Als u meerdere schijven gebruikt, bouwt u een software striping op de schijven die de gegevens bestanden bevatten of het logboek bestand en voert u de bestanden opnieuw uit. In dergelijke gevallen worden de IOPS en de door Voer van de schijf van de onderliggende Premium-opslag schijven of de maximale Haal bare IOPS van standaard opslag schijven cumulatief voor de resulterende Stripe-set.

Als uw IOPS-vereiste groter is dan wat een enkele VHD kan bieden, kunt u het aantal IOPS dat nodig is voor de database bestanden over een aantal Vhd's verdelen. De eenvoudigste manier om de IOPS-belasting over schijven te verdelen, is door een software Striper op de verschillende schijven te bouwen. Plaats vervolgens een aantal gegevens bestanden van de SAP-DBMS op de Lun's gehaald uit de software striping. Het aantal schijven in de Stripe wordt aangedreven door IOPS-aanvragen, vereisten voor schijf doorvoer en volume vereisten.


---
> ![Windows-opslag striping][Logo_Windows] Windows
>
> U wordt aangeraden Windows-opslag ruimten te gebruiken voor het maken van stripesets op meerdere Azure-Vhd's. Gebruik ten minste Windows Server 2012 R2 of Windows Server 2016.
>
> ![Linux-opslag striping][Logo_Linux] Linux
>
> Alleen MDADM en Logical Volume Manager (LVM) worden ondersteund voor het bouwen van een software-RAID op Linux. Zie voor meer informatie:
>
> - [Software-RAID op Linux configureren](/previous-versions/azure/virtual-machines/linux/configure-raid) met behulp van MDADM
> - [LVM configureren op een virtuele Linux-machine in azure](/previous-versions/azure/virtual-machines/linux/configure-lvm) met behulp van LVM
>
>

---

Voor Azure Ultra disk is striping niet nodig omdat u IOPS en schijf doorvoer onafhankelijk van de grootte van de schijf kunt definiëren.


> [!NOTE]
> Omdat Azure Storage drie installatie kopieën van de Vhd's houdt, is het niet logisch om een redundantie te configureren wanneer u een striping uitvoert. U hoeft alleen striping te configureren zodat de I/O's over de verschillende Vhd's worden verdeeld.
>

### <a name="managed-or-nonmanaged-disks"></a>Beheerd of niet-beheerde schijven
Een Azure Storage-account is een administratieve constructie en ook een onderwerp van beperkingen. De beperkingen verschillen tussen standaard opslag accounts en Premium Storage-accounts. Zie [Azure Storage schaal baarheid en prestatie doelen](../../../storage/common/scalability-targets-standard-account.md)voor meer informatie over de mogelijkheden en beperkingen.

Voor standaard opslag moet u er rekening mee houden dat de IOPS per opslag account beperkt is. Bekijk de rij met de **totale aanvraag snelheid** in het artikel [Azure Storage schaal baarheid en prestatie doelen](../../../storage/common/scalability-targets-standard-account.md). Er is ook een aanvankelijke limiet voor het aantal opslag accounts per Azure-abonnement. Verdeel Vhd's voor het grotere SAP-landschap in verschillende opslag accounts om te voor komen dat deze opslag accounts worden beperkt. Dit is omslachtig als u op een paar honderd virtuele machines met meer dan duizend Vhd's spreekt.

Het gebruik van standaard opslag voor DBMS-implementaties in combi natie met een SAP-workload wordt niet aanbevolen. Verwijzingen en aanbevelingen naar standaard opslag zijn daarom beperkt tot dit korte [artikel](/archive/blogs/mast/configuring-azure-virtual-machines-for-optimal-storage-performance)

Micro soft heeft [Azure-Managed disks](https://azure.microsoft.com/services/managed-disks/) geïntroduceerd in 2017 om te voor komen dat het administratieve werk van het plannen en implementeren van vhd's in verschillende Azure-opslag accounts. Beheerde schijven zijn beschikbaar voor standaard opslag en Premium-opslag. De belangrijkste voor delen van beheerde schijven vergeleken met niet-beheerde schijven zijn:

- Voor beheerde schijven distribueert Azure de verschillende Vhd's over verschillende opslag accounts automatisch tijdens de implementatie. Op deze manier worden de limieten voor opslag accounts voor gegevens volume, I/O-door Voer en IOPS niet bereikt.
- Met Managed disks voldoet Azure Storage aan de concepten van Azure-beschikbaarheids sets. Als de virtuele machine deel uitmaakt van een Azure-beschikbaarheidsset, worden de basis-VHD en de gekoppelde schijf van een virtuele machine geïmplementeerd in verschillende fout-en update domeinen.


> [!IMPORTANT]
> Gezien de voor delen van Azure Managed Disks, raden we u ten zeerste aan Azure Managed Disks te gebruiken voor uw DBMS-implementaties en SAP-implementaties in het algemeen.
>

Als u wilt converteren van onbeheerd naar beheerde schijven, raadpleegt u:

- [Een virtuele Windows-machine van niet-beheerde schijven converteren naar beheerde schijven](../../windows/convert-unmanaged-to-managed-disks.md).
- [Een virtuele Linux-machine van niet-beheerde schijven converteren naar beheerde schijven](../../linux/convert-unmanaged-to-managed-disks.md).


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Caching voor Vm's en gegevens schijven
Wanneer u schijven aan Vm's koppelt, kunt u kiezen of het I/O-verkeer tussen de virtuele machine en de schijven in azure Storage in de cache wordt opgeslagen.

De volgende aanbevelingen gaan ervan uit dat deze I/O-kenmerken voor standaard-DBMS:

- Het is voornamelijk een lees werk belasting tegen gegevens bestanden van een Data Base. Deze Lees bewerkingen zijn essentieel voor het DBMS-systeem.
- Schrijven naar de gegevens bestanden vindt plaats in bursts op basis van controle punten of een constante stroom. Gemiddeld per dag, er zijn minder schrijf bewerkingen dan lees bewerkingen. Naast Lees bewerkingen van gegevens bestanden zijn deze schrijf bewerkingen asynchroon en bevatten ze geen gebruikers transacties.
- Er zijn nauwelijks Lees bewerkingen van het transactie logboek of het opnieuw uitvoeren van bestanden. Uitzonde ringen zijn grote I/O's bij het uitvoeren van back-ups van transactie Logboeken.
- De belangrijkste belasting op trans acties of opnieuw uitvoeren van logboek bestanden wordt geschreven. Afhankelijk van de aard van de workload, kunt u een I/O's-versie hebben van Maxi maal 4 KB of, in andere gevallen, I/O-groottes van 1 MB of meer.
- Alle schrijf bewerkingen moeten op een betrouw bare manier op de schijf worden bewaard.

Voor standaard opslag zijn de mogelijke cache typen:

* Geen
* Lezen
* Lezen/Schrijven

Als u consistente en deterministische prestaties wilt, stelt u de cache in op standaard opslag voor alle schijven die DBMS-gerelateerde gegevens bestanden bevatten, logboeken en opnieuw uitvoeren van bestanden en tabel ruimte op **geen**. Het opslaan in cache van de basis-VHD kan met de standaard waarde blijven.

Voor Azure Premium Storage bestaan de volgende cache opties:

* Geen
* Lezen
* Lezen/schrijven
* Geen + Write Accelerator, alleen voor virtuele machines uit de M-serie van Azure
* Lezen + Write Accelerator, alleen voor virtuele machines uit de M-serie van Azure

Voor Premium Storage wordt u aangeraden de **Lees cache voor gegevens bestanden** van de SAP-data base te gebruiken en **geen cache te kiezen voor de schijven van logboek bestanden**.

Voor implementaties met een M-serie raden we u aan Azure Write Accelerator te gebruiken voor uw DBMS-implementatie. Zie [Write Accelerator inschakelen](../../how-to-enable-write-accelerator.md)voor meer informatie, beperkingen en implementatie van Azure write Accelerator.

Voor Ultra disk en Azure NetApp Files worden er geen cache opties aangeboden.


### <a name="azure-nonpersistent-disks"></a>Niet-permanente Azure-schijven
Azure Vm's bieden niet-permanente schijven nadat een virtuele machine is geïmplementeerd. Als een VM opnieuw wordt opgestart, wordt alle inhoud op die stations gewist. Het is een gegeven dat gegevens bestanden en logboek bestanden en het opnieuw uitvoeren van data bases, onder geen enkele omstandigheden, zich op deze niet-persistente stations bevinden. Er zijn mogelijk uitzonde ringen voor sommige data bases, waarbij deze niet-permanente schijven geschikt zijn voor TempDB en tijdelijke tablespaces. Vermijd het gebruik van schijven voor virtuele machines van de A-serie, omdat deze niet-persistente schijven beperkt zijn bij de door Voer met die VM-serie. 

Zie [inzicht in het tijdelijke station op Windows-vm's in azure](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines)voor meer informatie.

---
> ![Windows-niet-persistente schijf][Logo_Windows] Windows
>
> Station D in een Azure-VM is een niet-persistent station dat wordt ondersteund door een aantal lokale schijven op het Azure Compute-knoop punt. Omdat het niet-persistent is, gaan alle wijzigingen die zijn aangebracht in de inhoud op station D verloren wanneer de virtuele machine opnieuw wordt opgestart. Wijzigingen omvatten onder andere opgeslagen bestanden, mappen die zijn gemaakt en toepassingen die zijn geïnstalleerd.
>
> ![Linuxnonpersisted-schijf][Logo_Linux] Linux
>
> Linux Azure-Vm's koppelen automatisch een station op/mnt/resource dat een niet-persistent station is dat wordt ondersteund door lokale schijven op het Azure Compute-knoop punt. Omdat het niet-persistent is, gaan alle wijzigingen in de inhoud van/mnt/resource verloren wanneer de virtuele machine opnieuw wordt opgestart. Wijzigingen omvatten onder andere opgeslagen bestanden, mappen die zijn gemaakt en toepassingen die zijn geïnstalleerd.
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage tolerantie
Microsoft Azure Storage slaat de basis-VHD, met besturings systeem en gekoppelde schijven of blobs, op ten minste drie afzonderlijke opslag knooppunten. Dit type opslag wordt lokaal redundante opslag (LRS) genoemd. LRS is de standaard waarde voor alle typen opslag in Azure.

Er zijn andere redundantie methoden. Zie [Azure Storage-replicatie](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) voor meer informatie.

> [!NOTE]
> Azure Premium Storage, Ultra disk en Azure NetApp Files (uitsluitend voor SAP HANA) zijn het aanbevolen type opslag voor DBMS-Vm's en schijven die data bases opslaan en bestanden vastleggen en opnieuw uitvoeren. De enige beschik bare redundantie methode voor deze opslag typen is LRS. Als gevolg hiervan moet u database methoden configureren om database gegevens replicatie in te scha kelen in een andere Azure-regio of beschikbaarheids zone. Database methoden bevatten SQL Server always on, Oracle Data Guard en HANA System Replication.


> [!NOTE]
> Voor DBMS-implementaties wordt het gebruik van geo-redundante opslag (GRS) niet aanbevolen voor standaard opslag. GRS heeft ernstige gevolgen voor de prestaties en voldoet niet aan de schrijf volgorde voor verschillende Vhd's die zijn gekoppeld aan een virtuele machine. Het is niet mogelijk om de schrijf volgorde over verschillende Vhd's te laten leiden tot inconsistente data bases op de replicatie doel zijde. Deze situatie doet zich voor als de data base en het logboek bestand en de bestanden opnieuw worden verdeeld over meerdere Vhd's, zoals in het algemeen het geval is, op de bron-VM.



## <a name="vm-node-resiliency"></a>Tolerantie van VM-knoop punt
Azure biedt een aantal verschillende Sla's voor Vm's. Zie de meest recente release van [Sla voor virtual machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)voor meer informatie. Omdat de DBMS-laag essentieel is voor Beschik baarheid in een SAP-systeem, moet u inzicht hebben in beschikbaarheids sets, Beschikbaarheidszones en onderhouds gebeurtenissen. Zie [de beschik baarheid van virtuele Windows-machines beheren in azure](../../manage-availability.md) en [de beschik baarheid van virtuele Linux-machines beheren in azure](../../manage-availability.md)voor meer informatie over deze concepten.

De minimale aanbeveling voor productie DBMS-scenario's met een SAP-werk belasting is:

- Implementeer twee Vm's in een afzonderlijke beschikbaarheidsset in dezelfde Azure-regio.
- Voer deze twee Vm's uit in hetzelfde virtuele Azure-netwerk en er zijn Nic's gekoppeld uit dezelfde subnetten.
- Gebruik database methoden om een hot standby-stand met de tweede virtuele machine te gebruiken. Methoden kunnen worden SQL Server always on, Oracle Data Guard of HANA System Replication.

U kunt ook een derde VM in een andere Azure-regio implementeren en dezelfde database methoden gebruiken om een asynchrone replica in een andere Azure-regio op te geven.

Zie [deze zelf studie](../../windows/tutorial-availability-sets.md)voor meer informatie over het instellen van Azure-beschikbaarheids sets.



## <a name="azure-network-considerations"></a>Aandachtspunten voor Azure-netwerken
Gebruik bij grootschalige SAP-implementaties de blauw druk van [Azure Virtual Data Center](/azure/architecture/vdc/networking-virtual-datacenter). Gebruik dit voor de configuratie van uw virtuele netwerk en machtigingen en roltoewijzingen voor verschillende onderdelen van uw organisatie.

Deze aanbevolen procedures zijn het resultaat van honderden implementaties van klanten:

- De virtuele netwerken waarmee de SAP-toepassing wordt geïmplementeerd, hebben geen toegang tot internet.
- De data base-Vm's worden uitgevoerd in hetzelfde virtuele netwerk als de toepassingslaag, gescheiden in een ander subnet van de SAP-toepassingslaag.
- De virtuele machines in het virtuele netwerk hebben een statische toewijzing van het privé-IP-adres. Zie [IP-adres typen en toewijzings methoden in azure](../../../virtual-network/public-ip-addresses.md)voor meer informatie.
- Routerings beperkingen van en naar de DBMS-Vm's zijn *niet* ingesteld met firewalls die zijn geïnstalleerd op de lokale DBMS-vm's. In plaats daarvan wordt verkeers routering gedefinieerd met [netwerk beveiligings groepen (nsg's)](../../../virtual-network/network-security-groups-overview.md).
- Wijs verschillende Nic's toe aan de virtuele machine om verkeer te scheiden en te isoleren naar de DBMS-VM. Elke NIC krijgt een ander IP-adres en elke NIC wordt toegewezen aan een ander subnet van het virtuele netwerk. Elk subnet heeft verschillende NSG-regels. De isolatie of schei ding van netwerk verkeer is een meting voor route ring. Het wordt niet gebruikt voor het instellen van quota's voor netwerk doorvoer.

> [!NOTE]
> Het toewijzen van vaste IP-adressen via Azure betekent dat ze worden toegewezen aan afzonderlijke virtuele Nic's. Wijs geen statische IP-adressen binnen het gast besturingssysteem toe aan een virtuele NIC. Sommige Azure-Services, zoals Azure Backup, zijn afhankelijk van het feit dat ten minste de primaire virtuele NIC is ingesteld op DHCP en niet op statische IP-adressen. Zie [problemen met back-ups van virtuele Azure-machines oplossen](../../../backup/backup-azure-vms-troubleshoot.md#networking)voor meer informatie. Als u meerdere statische IP-adressen wilt toewijzen aan een virtuele machine, wijst u meerdere virtuele Nic's toe aan een virtuele machine.
>


> [!WARNING]
> Het configureren van [virtuele netwerk apparaten](https://azure.microsoft.com/solutions/network-appliances/) in het communicatie traject tussen de SAP-toepassing en de DBMS-laag van een SAP NetWeaver-, hybris-of S/4HANA SAP-systeem wordt niet ondersteund. Deze beperking is voor functionaliteits-en prestatie redenen. Het communicatie-pad tussen de SAP-toepassingslaag en de DBMS-laag moet direct een. De beperking bevat geen [toepassings beveiligings groep (ASG) en NSG regels](../../../virtual-network/network-security-groups-overview.md) als de regels ASG en NSG een direct communicatie traject toestaan. 
>
> Andere scenario's waarin virtuele netwerk apparaten niet worden ondersteund, zijn in:
>
> * Communicatie paden tussen Azure-Vm's die Linux pacemaker-cluster knooppunten en SBD-apparaten vertegenwoordigen, zoals wordt beschreven in [hoge Beschik baarheid voor SAP NetWeaver op Azure-vm's op SuSE Linux Enterprise Server voor SAP-toepassingen](./high-availability-guide-suse.md).
> * Communicatie paden tussen virtuele machines van Azure en Windows Server Scale-Out Bestands server (SOFS) die zijn ingesteld zoals beschreven in [cluster a SAP ASCS/SCS-exemplaar op een Windows-failovercluster met behulp van een bestands share in azure](./sap-high-availability-guide-wsfc-file-share.md). 
>
> Virtuele netwerk apparaten in communicatie paden kunnen eenvoudig de netwerk latentie tussen twee communicatie partners verdubbelen. Ze kunnen de door Voer ook beperken in kritieke paden tussen de SAP-toepassingslaag en de DBMS-laag. In sommige klant scenario's kunnen virtuele netwerk apparaten ervoor zorgen dat pacemaker Linux-clusters mislukken. Dit zijn gevallen waarin communicatie tussen de Linux pacemaker-cluster knooppunten via een virtueel netwerk apparaat communiceert met hun SBD-apparaat.
>

> [!IMPORTANT]
> Een ander ontwerp dat *niet* wordt ondersteund, is de schei ding van de SAP-toepassingslaag en de DBMS-laag in verschillende virtuele [netwerken van Azure die niet met elkaar](../../../virtual-network/virtual-network-peering-overview.md) zijn gekoppeld. U wordt aangeraden de SAP-toepassingsobjectlaag en de DBMS-laag te scheiden met behulp van subnetten in een virtueel Azure-netwerk in plaats van met behulp van verschillende virtuele netwerken van Azure. 
>
> Als u besluit de aanbeveling niet te volgen en in plaats daarvan de twee lagen te scheiden in verschillende virtuele netwerken, moeten de twee virtuele netwerken [gelijkwaardig](../../../virtual-network/virtual-network-peering-overview.md)zijn. 
>
> Houd er rekening mee dat het netwerk verkeer tussen twee [gepeerde](../../../virtual-network/virtual-network-peering-overview.md) virtuele netwerken van Azure onderhevig is aan kosten voor overdracht. Een zeer groot gegevens volume dat uit veel terabytes bestaat, wordt uitgewisseld tussen de SAP-toepassingslaag en de DBMS-laag. U kunt aanzienlijke kosten optellen als de SAP-toepassingsobjectlaag en de DBMS-laag worden gescheiden tussen twee peered virtuele netwerken van Azure.

Gebruik twee virtuele machines voor de implementatie van uw productie-DBMS in een Azure-beschikbaarheidsset of tussen twee Azure-beschikbaarheidszones. Gebruik ook afzonderlijke route ring voor de SAP-toepassingslaag en het beheer-en bewerkings verkeer naar de twee virtuele DBMS-Vm's. Zie de volgende afbeelding:

![Diagram van twee virtuele machines in twee subnetten](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Azure Load Balancer gebruiken om verkeer om te leiden
Het gebruik van privé-IP-adressen die worden gebruikt in functies als SQL Server always on of HANA-systeem replicatie vereist de configuratie van een Azure load balancer. De load balancer gebruikt test poorten om het actieve DBMS-knoop punt te bepalen en het verkeer alleen naar dat actieve database knooppunt te routeren. 

Als er een failover van het database knooppunt is, hoeft de SAP-toepassing niet opnieuw te worden geconfigureerd. In plaats daarvan worden de meest voorkomende SAP-toepassings architecturen opnieuw verbonden met het persoonlijke virtuele IP-adres. Ondertussen reageert de load balancer op de failover van het knoop punt door het verkeer door te sturen naar het virtuele particuliere IP-adres naar het tweede knoop punt.

Azure biedt twee verschillende [Load Balancer sku's](../../../load-balancer/load-balancer-overview.md): een basis-SKU en een standaard-SKU. Op basis van de voor delen van Setup en functionaliteit moet u de standaard-SKU van de Azure-load balancer gebruiken. Een van de grote voor delen van de standaard versie van de load balancer is dat het gegevens verkeer niet via de load balancer zelf wordt gerouteerd.

Een voor beeld van hoe u een interne load balancer kunt configureren, vindt u in de zelf studie over het artikel [: een SQL Server beschikbaarheids groep in azure hand matig configureren virtual machines](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-tutorial.md#create-an-azure-load-balancer)

> [!NOTE]
> Er zijn verschillen in gedrag van de basis-en standaard-SKU met betrekking tot de toegang tot open bare IP-adressen. De manier waarop de beperkingen van de standaard-SKU worden omzeild om toegang te krijgen tot open bare IP-adressen, wordt beschreven in de [open bare eindpunt connectiviteit van het document voor virtual machines met behulp van Azure Standard Load Balancer in scenario's met hoge Beschik baarheid van SAP](./high-availability-guide-standard-load-balancer-outbound-connections.md)


### <a name="azure-accelerated-networking"></a>Versneld Azure-netwerken
Om de netwerk latentie tussen virtuele Azure-machines verder te verminderen, raden we u aan om [Azure versneld netwerken](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)te kiezen. Gebruik deze methode wanneer u Azure-Vm's implementeert voor een SAP-workload, met name voor de SAP-toepassingslaag en de SAP-DBMS-laag.

> [!NOTE]
> Niet alle VM-typen ondersteunen versneld netwerken. In het vorige artikel vindt u een overzicht van de VM-typen die ondersteuning bieden voor versneld netwerken.
>

---
> ![Windows versneld netwerken][Logo_Windows] Windows
>
> Zie [een virtuele Windows-machine maken met versneld netwerken](../../../virtual-network/create-vm-accelerated-networking-powershell.md)voor meer informatie over het implementeren van vm's met versneld netwerken voor Windows.
>
> ![Versneld Linux-netwerken][Logo_Linux] Linux
>
> Zie [een virtuele Linux-machine maken met versneld netwerken](../../../virtual-network/create-vm-accelerated-networking-cli.md)voor meer informatie over Linux-distributie.
>
>

---

> [!NOTE]
> In het geval van SUSE, Red Hat en Oracle Linux, wordt versneld netwerken ondersteund met recente releases. Oudere releases, zoals SLES 12 SP2 of RHEL 7,2, bieden geen ondersteuning voor versnelde netwerken van Azure.
>


## <a name="deployment-of-host-monitoring"></a>Implementatie van hostcontrole
Voor het productie gebruik van SAP-toepassingen in azure virtual machines vereist SAP de mogelijkheid om hostcontrole gegevens op te halen van de fysieke hosts waarop de virtuele machines van Azure worden uitgevoerd. Een specifiek patch niveau voor de SAP-Hosta Gent is vereist voor het inschakelen van deze mogelijkheid in SAPOSCOL en SAP host agent. Het exacte patch niveau wordt beschreven in SAP Note [1409604].

Zie de [implementatie handleiding][deployment-guide]voor meer informatie over de implementatie van-onderdelen die hostgegevens leveren aan SAPOSCOL en SAP host agent en het levenscyclus beheer van deze onderdelen.


## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over een bepaald DBMS:

- [DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor SQL Server](dbms_guide_sqlserver.md)
- [DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor Oracle](dbms_guide_oracle.md)
- [Implementatie van IBM DB2 Azure Virtual Machines DBMS voor SAP-workload](dbms_guide_ibm.md)
- [DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor SAP ASE](dbms_guide_sapase.md)
- [SAP maxDB, Live cache en inhouds Server implementatie op Azure](dbms_guide_maxdb.md)
- [Bedieningsgids voor SAP HANA op Azure](hana-vm-operations.md)
- [SAP HANA hoge Beschik baarheid voor virtuele machines van Azure](sap-hana-availability-overview.md)
- [Back-upgids voor SAP HANA op virtuele machines van Azure](sap-hana-backup-guide.md)