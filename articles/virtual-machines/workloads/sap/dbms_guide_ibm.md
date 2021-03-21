---
title: Implementatie van IBM Db2 Azure Virtual Machines DBMS voor SAP-workload | Microsoft Docs
description: DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor IBM Db2
services: virtual-machines-linux,virtual-machines-windows
author: msjuergent
manager: bburns
tags: azure-resource-manager
keywords: Azure, Db2, SAP, IBM
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/20/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3f89f218c82505fd6bc261d41938d4619b32bf8a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101675972"
---
# <a name="ibm-db2-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor IBM Db2

Met Microsoft Azure kunt u uw bestaande SAP-toepassing die wordt uitgevoerd op IBM Db2 voor Linux, UNIX en Windows (LUW) migreren naar Azure virtual machines. Met SAP op IBM Db2 voor LUW kunnen beheerders en ontwikkel aars nog steeds gebruikmaken van dezelfde hulpprogram ma's voor ontwikkeling en beheer, die on-premises beschikbaar zijn.
Algemene informatie over het uitvoeren van SAP Business Suite op IBM Db2 voor LUW vindt u in het SAP Community Network (SCN) op <https://www.sap.com/community/topic/db2-for-linux-unix-and-windows.html> .

Zie SAP Note [2233094]voor meer informatie over SAP op DB2 voor LUW op Azure. 

De diverse artikelen over SAP-workloads op Azure zijn uitgebracht.  Het is raadzaam om te beginnen bij de [SAP-werk belasting op Azure](./get-started.md) . Ga aan de slag en kies vervolgens het interesse gebied

De volgende SAP-opmerkingen zijn gerelateerd aan SAP on Azure met betrekking tot het gebied dat in dit document wordt besproken:

| Nummer van notitie |Titel |
| --- |--- |
| [1928533] |SAP-toepassingen op Azure: ondersteunde producten en Azure VM-typen |
| [2015553] |SAP op Microsoft Azure: vereisten voor ondersteuning |
| [1999351] |Problemen met verbeterde Azure-bewaking voor SAP oplossen |
| [2178632] |Belangrijkste meet waarden voor SAP op Microsoft Azure |
| [1409604] |Virtualisatie op Windows: uitgebreide bewaking |
| [2191498] |SAP op Linux met Azure: uitgebreide bewaking |
| [2233094] |DB6: SAP-toepassingen op Azure met behulp van IBM DB2 voor Linux, UNIX en Windows-aanvullende informatie |
| [2243692] |Linux on Microsoft Azure (IaaS) VM: SAP-licentie problemen |
| [1984787] |SUSE LINUX Enter prise Server 12: installatie notities |
| [2002167] |Red Hat Enterprise Linux 7. x: installatie en upgrade |
| [1597355] |Aanbeveling voor wissel geheugen voor Linux |

Als PR-Lees dit document moet u de document [overwegingen voor Azure virtual machines DBMS-implementatie van de SAP-werk belasting](dbms_guide_general.md) en andere hand leidingen in de [SAP-werk belasting op de Azure-documentatie](./get-started.md)lezen. 


## <a name="ibm-db2-for-linux-unix-and-windows-version-support"></a>Ondersteuning voor IBM Db2 voor Linux-, UNIX-en Windows-versies
SAP op IBM Db2 voor LUW op Microsoft Azure virtual machine Services wordt ondersteund vanaf de Db2-versie 10,5.

Raadpleeg SAP Note [1928533]voor informatie over ondersteunde SAP-producten en typen Azure VM.

## <a name="ibm-db2-for-linux-unix-and-windows-configuration-guidelines-for-sap-installations-in-azure-vms"></a>IBM Db2 voor Linux-, UNIX-en Windows-configuratie richtlijnen voor SAP-installaties in azure-Vm's
### <a name="storage-configuration"></a>Opslagconfiguratie
Raadpleeg het artikel [Azure Storage typen voor SAP-workload](./planning-guide-storage.md) voor een overzicht van Azure Storage-typen voor SAP-werk belasting. alle database bestanden moeten worden opgeslagen op gekoppelde schijven van Azure Block Storage (Windows: NFFS, Linux: xfs, ext4 of ext3). Elk type netwerk station of externe shares zoals de volgende Azure-Services worden **niet** ondersteund voor database bestanden: 

* [Microsoft Azure-bestands service](/archive/blogs/windowsazurestorage/introducing-microsoft-azure-file-service)

* [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)

Door gebruik te maken van schijven op basis van de BLOB-opslag of Managed Disks van Azure-pagina's, zijn de instructies [voor de implementatie van azure virtual machines DBMS voor SAP-workload](dbms_guide_general.md) ook van toepassing op implementaties met de DB2-DBMS.

Zoals eerder beschreven in het gedeelte Algemeen van het document, bestaan er quota's voor IOPS-door Voer voor Azure-schijven. De exacte quota's zijn afhankelijk van het gebruikte VM-type. Hier vindt u een lijst met VM-typen met hun quota [(Linux)][virtual-machines-sizes-linux] en [hier (Windows)][virtual-machines-sizes-windows].

Zolang het huidige quotum voor IOPS per schijf voldoende is, is het mogelijk om alle database bestanden op te slaan op één enkele gekoppelde schijf. Dat u altijd de gegevens bestanden en transactie logboek bestanden moet scheiden op verschillende schijven/Vhd's.

Raadpleeg het hoofd stuk ' overwegingen voor de veiligheid en prestaties van data bases voor database Directory's ' in SAP-installatie handleidingen voor informatie over prestaties.

U kunt ook Windows-opslag groepen (alleen beschikbaar in Windows Server 2012 en hoger) gebruiken als beschreven [aandachtspunten voor de implementatie van Azure virtual machines DBMS voor SAP-workload](dbms_guide_general.md) of LVM of mdadm in Linux om een groot logisch apparaat te maken op meerdere schijven.

<!-- sapdata and saptmp are terms in the SAP and DB2 world and now spelling errors -->

Voor de schijven met de Db2-opslag paden voor uw `sapdata` en `saptmp` mappen, moet u een sector grootte van fysieke schijven opgeven van 512 KB. Wanneer u Windows-opslag groepen gebruikt, moet u de opslag groepen hand matig maken via de opdracht regel interface met behulp van de para meter `-LogicalSectorSizeDefault` . Voor meer informatie raadpleegt u <https://technet.microsoft.com/itpro/powershell/windows/storage/new-storagepool>.

Voor virtuele machines uit de M-serie van Azure kan de latentie die in de transactie Logboeken wordt geschreven, worden verminderd met factoren, vergeleken met de prestaties van Azure Premium Storage, wanneer u Azure Write Accelerator gebruikt. Daarom moet u Azure Write Accelerator implementeren voor de VHD (s) die het volume vormen voor de Db2-transactie Logboeken. Details kunnen worden gelezen in het document [Write Accelerator](../../how-to-enable-write-accelerator.md).

## <a name="recommendation-on-vm-and-disk-structure-for-ibm-db2-deployment"></a>Aanbeveling op de VM-en schijf structuur voor de implementatie van IBM Db2

IBM Db2 voor SAP NetWeaver-toepassingen wordt ondersteund op elk VM-type dat wordt vermeld in de SAP-ondersteunings opmerking [1928533].  De aanbevolen VM-families voor het uitvoeren van een IBM Db2-Data Base zijn Esd_v4/Eas_v4/Es_v3 en M/M_v2-serie voor grote data bases met meerdere terabytes. De schrijf prestaties van de IBM Db2-transactie logboek schijf kunnen worden verbeterd door de Write Accelerator van de M-serie in te scha kelen. 

Hieronder volgt een basislijn configuratie voor verschillende grootten en het gebruik van SAP op Db2-implementaties van kleine tot grote. De lijst is gebaseerd op Azure Premium Storage. Azure Ultra disk wordt echter volledig ondersteund met Db2 en kan ook worden gebruikt. Gebruik gewoon de waarden voor capaciteit, burst-door Voer en burst IOPS om de configuratie van de ultra schijf te definiëren. U kunt de IOPS voor het/DB2/ <SID> /log_dir beperken tot ongeveer 5000 IOPS. 

#### <a name="extra-small-sap-system-database-size-50---200-gb-example-solution-manager"></a>Extra klein SAP-systeem: data base-grootte 50-200 GB: voor beeld van oplossingen beheer
| VM-naam/-grootte |Db2-koppel punt |Azure Premium Disk |Aantal schijven |IOPS |Door Voer [MB/s] |Grootte [GB] |Burst IOPS |Burst-Passthr [GB] | Stripe-grootte | Caching |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E4ds_v4 |/db2 |P6 |1 |240  |50  |64  |3500  |170  ||  |
|vCPU: 4 |/DB2/ <SID> /sapdata |P10 |2 |1000  |200  |256  |7\.000  |340  |256 kB |ReadOnly |
|RAM: 32 GiB |/DB2/ <SID> /saptmp |P6 |1 |240  |50  |128  |3500  |170  | ||
| |/DB2/ <SID> /log_dir |P6 |2 |480  |100  |128  |7\.000  |340  |64 kB ||
| |/DB2/ <SID> /offline_log_dir |P10 |1 |500  |100  |128  |3500  |170  || |

#### <a name="small-sap-system-database-size-200---750-gb-small-business-suite"></a>Small SAP-systeem: data base grootte 200-750 GB: Small Business Suite
| VM-naam/-grootte |Db2-koppel punt |Azure Premium Disk |Aantal schijven |IOPS |Door Voer [MB/s] |Grootte [GB] |Burst IOPS |Burst-Passthr [GB] | Stripe-grootte | Caching |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E16ds_v4 |/db2 |P6 |1 |240  |50  |64  |3500  |170  || |
|vCPU: 16 |/DB2/ <SID> /sapdata |P15 |4 |4.400  |500  |1,024  |14.000  |680  |256 kB |ReadOnly |
|RAM: 128 GiB |/DB2/ <SID> /saptmp |P6 |2 |480  |100  |128  |7\.000  |340  |128 kB ||
| |/DB2/ <SID> /log_dir |P15 |2 |2200  |250  |512  |7\.000  |340  |64 kB ||
| |/DB2/ <SID> /offline_log_dir |P10 |1 |500  |100  |128  |3500  |170  ||| 

#### <a name="medium-sap-system-database-size-500---1000-gb-small-business-suite"></a>Gemiddeld SAP-systeem: database grootte 500-1000 GB: Small Business Suite
| VM-naam/-grootte |Db2-koppel punt |Azure Premium Disk |Aantal schijven |IOPS |Door Voer [MB/s] |Grootte [GB] |Burst IOPS |Burst-Passthr [GB] | Stripe-grootte | Caching |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E32ds_v4 |/db2 |P6 |1 |240  |50  |64  |3500  |170  || |
|vCPU: 32 |/DB2/ <SID> /sapdata |P30 |2 |10.000  |400  |2,048  |10.000  |400  |256 kB |ReadOnly |
|RAM: 256 GiB |/DB2/ <SID> /saptmp |P10 |2 |1000  |200  |256  |7\.000  |340  |128 kB ||
| |/DB2/ <SID> /log_dir |P20 |2 |4.600  |300  |1,024  |7\.000  |340  |64 kB ||
| |/DB2/ <SID> /offline_log_dir |P15 |1 |1100  |125  |256  |3500  |170  ||| 

#### <a name="large-sap-system-database-size-750---2000-gb-business-suite"></a>Groot SAP-systeem: data base grootte 750-2000 GB: Business Suite
| VM-naam/-grootte |Db2-koppel punt |Azure Premium Disk |Aantal schijven |IOPS |Door Voer [MB/s] |Grootte [GB] |Burst IOPS |Burst-Passthr [GB] | Stripe-grootte | Caching |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|E64ds_v4 |/db2 |P6 |1 |240  |50  |64  |3500  |170  || |
|vCPU: 64 |/DB2/ <SID> /sapdata |P30 |4 |20.000  |800  |4,096  |20.000  |800  |256 kB |ReadOnly |
|RAM: 504 GiB |/DB2/ <SID> /saptmp |P15 |2 |2200  |250  |512  |7\.000  |340  |128 kB ||
| |/DB2/ <SID> /log_dir |P20 |4 |9.200  |600  |2,048  |14.000  |680  |64 kB ||
| |/DB2/ <SID> /offline_log_dir |P20 |1 |2\.300  |150  |512  |3500  |170  || |

#### <a name="large-multi-terabyte-sap-system-database-size-2-tb-global-business-suite-system"></a>Grote multi-terabyte SAP-systeem: data base grootte 2 TB +: Global Business Suite-systeem
| VM-naam/-grootte |Db2-koppel punt |Azure Premium Disk |Aantal schijven |IOPS |Door Voer [MB/s] |Grootte [GB] |Burst IOPS |Burst-Passthr [GB] | Stripe-grootte | Caching |
| --- | --- | --- | :---: | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
|M128s |/db2 |P10 |1 |500  |100  |128  |3500  |170  || |
|vCPU: 128 |/DB2/ <SID> /sapdata |P40 |4 |30.000  |1,000  |8,192  |30.000  |1,000  |256 kB |ReadOnly |
|RAM: 2048 GiB |/DB2/ <SID> /saptmp |P20 |2 |4.600  |300  |1,024  |7\.000  |340  |128 kB ||
| |/DB2/ <SID> /log_dir |P30 |4 |20.000  |800  |4,096  |20.000  |800  |64 kB |Write Accelerator |
| |/DB2/ <SID> /offline_log_dir |P30 |1 |5\.000  |200  |1,024  |5\.000  |200  || |


### <a name="backuprestore"></a>Back-up en herstellen
De functionaliteit voor maken en herstellen van IBM Db2 voor LUW wordt ondersteund op dezelfde manier als bij de standaard Windows Server-besturings systemen en Hyper-V.

Zorg ervoor dat u beschikt over een geldige database back-upstrategie. 

Net als bij bare-metal implementaties is de prestaties van back-up/herstel afhankelijk van het aantal volumes dat parallel kan worden gelezen en de door Voer van die volumes. Daarnaast kan het CPU-verbruik dat door back-upcompressie wordt gebruikt, een belang rijke rol spelen op Vm's met Maxi maal acht CPU-threads. Daarom kan een ervan uitgaan:

* Hoe minder schijven er worden gebruikt voor het opslaan van de database apparaten, hoe kleiner de totale door Voer bij het lezen
* Het kleinere aantal CPU-threads in de virtuele machine, des te groter is de impact van back-upcompressie
* Hoe minder doelen (Stripe-Directory's, schijven) om de back-up naar te schrijven, hoe lager de door Voer

Om het aantal doelen te verhogen waarnaar moet worden geschreven, kunnen twee opties worden gebruikt/gecombineerd, afhankelijk van uw behoeften:

* Het back-updoel volume over meerdere schijven verwijderen om de IOPS-door Voer voor dat striped volume te verbeteren
* Meerdere doel mappen gebruiken om de back-up naar te schrijven

>[!NOTE]
>Db2 in Windows biedt geen ondersteuning voor de Windows VSS-technologie. Als gevolg hiervan kan de toepassings consistente back-up van de virtuele machine van Azure Backup service niet worden gebruikt voor virtuele machines waarin de Db2-DBMS wordt geïmplementeerd.

### <a name="high-availability-and-disaster-recovery"></a>Hoge beschikbaarheid en herstel na noodgevallen

#### <a name="linux-pacemaker"></a>Linux-pacemaker

Een ' HADR-herstel bewerking met hoge Beschik baarheid met pacemaker wordt ondersteund. Zowel de SLES-als RHEL-besturings systemen worden ondersteund. Deze configuratie biedt een hoge Beschik baarheid van IBM Db2 voor SAP. Implementatie handleidingen:
* SLES: [hoge Beschik baarheid van IBM DB2 LUW op Azure vm's op SuSE Linux Enterprise Server met pacemaker](dbms-guide-ha-ibm.md) 
* RHEL: [hoge Beschik baarheid van IBM DB2 LUW op Azure-vm's op Red Hat Enterprise Linux server](high-availability-guide-rhel-ibm-db2-luw.md)

#### <a name="windows-cluster-server"></a>Windows-cluster server

Micro soft Cluster Server (MSCS) wordt niet ondersteund.

Er wordt ondersteuning geboden voor een nood herstel met hoge Beschik baarheid van Db2 (HADR). Als de virtuele machines van de HA-configuratie werk naam omzetting hebben, is de installatie in azure niet van toepassing op de installatie die on-premises wordt uitgevoerd. Het is niet raadzaam om alleen te vertrouwen op de IP-omzetting.

Gebruik Geo-Replication niet voor de opslag accounts waarin de database schijven zijn opgeslagen. Zie de document [overwegingen voor Azure virtual machines DBMS-implementatie voor SAP-werk belasting](dbms_guide_general.md)voor meer informatie. 

### <a name="accelerated-networking"></a>Versneld netwerken
Voor Db2-implementaties in Windows wordt u ten zeerste aangeraden de functionaliteit van Azure te gebruiken voor versneld netwerken, zoals beschreven in het document [Azure versneld netwerken](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Houd ook rekening met aanbevelingen die zijn gedaan in [overwegingen voor Azure virtual machines DBMS-implementatie voor SAP-workloads](dbms_guide_general.md). 


### <a name="specifics-for-linux-deployments"></a>Details voor Linux-implementaties
Zolang het quotum voor de huidige IOPS per schijf voldoende is, is het mogelijk om alle database bestanden op één schijf op te slaan. Dat u altijd de gegevens bestanden en transactie logboek bestanden moet scheiden op verschillende schijven/Vhd's.

Als de IOPS of I/O-door Voer van één Azure VHD niet voldoende is, kunt u ook LVM (Logical Volume Manager) of MDADM gebruiken, zoals beschreven in de document [overwegingen voor Azure virtual machines DBMS-implementatie voor SAP-werk belasting](dbms_guide_general.md) om één groot logisch apparaat te maken op meerdere schijven.
Voor de schijven met de Db2-opslag paden voor uw sapdata-en saptmp-directory's moet u een sector grootte van fysieke schijven opgeven van 512 KB.

<!-- sapdata and saptmp are terms in the SAP and DB2 world and now spelling errors -->


### <a name="other"></a>Anders
Alle andere algemene gebieden, zoals Azure-beschikbaarheids sets of SAP-bewaking, zijn van toepassing zoals beschreven in de document [overwegingen voor Azure virtual machines DBMS-implementatie voor SAP-werk belasting](dbms_guide_general.md) voor implementaties van vm's met de IBM-data base.

[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692


## <a name="next-steps"></a>Volgende stappen
Lees het artikel 

- [Overwegingen voor de implementatie van Azure Virtual Machines DBMS voor SAP-workloads](dbms_guide_general.md)

[azure-cli]:../../../cli-install-nodejs.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azure/
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits

[dbms-guide]:dbms-guide.md 
[dbms-guide-2.1]:dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f 
[dbms-guide-2.2]:dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 
[dbms-guide-2.3]:dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 
[dbms-guide-2]:dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 
[dbms-guide-3]:dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 
[dbms-guide-5.5.1]:dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 
[dbms-guide-5.5.2]:dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b 
[dbms-guide-5.6]:dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 
[dbms-guide-5.8]:dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 
[dbms-guide-5]:dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 
[dbms-guide-8.4.1]:dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 
[dbms-guide-8.4.2]:dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d 
[dbms-guide-8.4.3]:dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c 
[dbms-guide-8.4.4]:dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 
[dbms-guide-900-sap-cache-server-on-premises]:dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8
[dbms-guide-managed-disks]:dbms-guide.md#f42c6cb5-d563-484d-9667-b07ae51bce29

[dbms-guide-figure-100]:media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:deployment-guide.md 
[deployment-guide-2.2]:deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 
[deployment-guide-3.1.2]:deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab 
[deployment-guide-3.2]:deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 
[deployment-guide-3.3]:deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 
[deployment-guide-3.4]:deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e 
[deployment-guide-4.1]:deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 
[deployment-guide-4.2]:deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e 
[deployment-guide-4.3]:deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc 
[deployment-guide-4.4.2]:deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 
[deployment-guide-4.4]:deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d 
[deployment-guide-4.5.1]:deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 
[deployment-guide-4.5.2]:deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f 
[deployment-guide-4.5]:deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca 
[deployment-guide-5.1]:deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 
[deployment-guide-5.2]:deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1
[deployment-guide-5.3]:deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 

[deployment-guide-configure-monitoring-scenario-1]:deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b 
[deployment-guide-configure-proxy]:deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d 
[deployment-guide-figure-100]:media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:deployment-guide.md#figure-11
[deployment-guide-figure-1100]:media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:deployment-guide.md#figure-14
[deployment-guide-figure-1400]:media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:deployment-guide.md#figure-5
[deployment-guide-figure-50]:media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:deployment-guide.md#figure-6
[deployment-guide-figure-600]:media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:deployment-guide.md#figure-7
[deployment-guide-figure-700]:media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b

[deploy-template-cli]:../../../resource-group-template-deploy-cli.md
[deploy-template-portal]:../../../resource-group-template-deploy-portal.md
[deploy-template-powershell]:../../../resource-group-template-deploy.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[getting-started-dbms]:get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:../../virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:../../virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:../../virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:../../virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:../../virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:../../virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:https://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:planning-guide.md 
[planning-guide-1.2]:planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff 
[planning-guide-11]:planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 
[planning-guide-11.4.1]:planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 
[planning-guide-11.5]:planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f 
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 
[planning-guide-3.1]:planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a 
[planning-guide-3.2.1]:planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 
[planning-guide-3.2.2]:planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 
[planning-guide-3.2.3]:planning-guide.md#18810088-f9be-4c97-958a-27996255c665 
[planning-guide-3.2]:planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 
[planning-guide-3.3.2]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 
[planning-guide-5.1.1]:planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 
[planning-guide-5.1.2]:planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c 
[planning-guide-5.2.1]:planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef 
[planning-guide-5.2.2]:planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 
[planning-guide-5.2]:planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 
[planning-guide-5.3.1]:planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 
[planning-guide-5.3.2]:planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a 
[planning-guide-5.4.2]:planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 
[planning-guide-5.5.1]:planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 
[planning-guide-5.5.3]:planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d 
[planning-guide-7.1]:planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 
[planning-guide-7]:planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 
[planning-guide-azure-premium-storage]:planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 

[planning-guide-figure-100]:media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd 
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f 

[powershell-install-configure]:https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps
[resource-group-authoring-templates]:../../../resource-group-authoring-templates.md
[resource-group-overview]:../../../azure-resource-manager/management/overview.md
[resource-groups-networking]:../../../networking/networking-overview.md
[sap-pam]:https://support.sap.com/pam 
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../../../storage/common/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../../../storage/common/storage-azure-cli.md#copy-blobs
[storage-introduction]:../../../storage/common/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../../../storage/common/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../../disks-types.md
[storage-redundancy]:../../../storage/common/storage-redundancy.md
[storage-scalability-targets]:../../../storage/common/scalability-targets-standard-accounts.md
[storage-use-azcopy]:../../../storage/common/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:../../linux/attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../../../resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:../../../resource-manager-deployment-model.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:../../virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:../../linux/cli-deploy-templates.md 
[virtual-machines-deploy-rmtemplates-powershell]:../../virtual-machines-windows-ps-manage.md 
[virtual-machines-linux-agent-user-guide]:../../linux/agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:../../linux/agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:../../linux/capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:../../linux/capture-image.md#step-2-capture-the-vm
[virtual-machines-linux-configure-raid]:../../linux/configure-raid.md
[virtual-machines-linux-configure-lvm]:../../linux/configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:../../virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:../../linux/suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:../../linux/redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:../../linux/add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:../../linux/add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:../../linux/quick-create-cli.md
[virtual-machines-linux-update-agent]:../../linux/update-agent.md
[virtual-machines-manage-availability-linux]:../../linux/manage-availability.md
[virtual-machines-manage-availability-windows]:../../windows/manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes-linux]:../../linux/sizes.md
[virtual-machines-sizes-windows]:../../windows/sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./../../windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:../../../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md
[virtual-machines-sql-server-infrastructure-services]:../../../azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md
[virtual-machines-sql-server-performance-best-practices]:../../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md
[virtual-machines-upload-image-windows-resource-manager]:../../virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:../../virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/resources/templates/sql-server-2014-alwayson-existing-vnet-and-ad/
[virtual-network-deploy-multinic-arm-cli]:../linux/multiple-nics.md
[virtual-network-deploy-multinic-arm-ps]:../windows/multiple-nics.md
[virtual-network-deploy-multinic-arm-template]:../../../virtual-network/template-samples.md
[virtual-networks-configure-vnet-to-vnet-connection]:../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../../../virtual-network/manage-virtual-network.md#create-a-virtual-network
[virtual-networks-manage-dns-in-vnet]:../../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../../../virtual-network/virtual-network-deploy-multinic-classic-ps.md
[virtual-networks-nsg]:../../../virtual-network/security-overview.md
[virtual-networks-reserved-private-ip]:../../../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../../../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../../../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../../../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../../../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../../../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../../../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../../../cli-install-nodejs.md
[xplat-cli-azure-resource-manager]:../../../xplat-cli-azure-resource-manager.md