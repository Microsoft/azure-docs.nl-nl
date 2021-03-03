---
title: Implementatie van SAP ASE Azure Virtual Machines DBMS voor SAP-workload | Microsoft Docs
description: DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor SAP ASE
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 430af1dfcbd9c7faabfca2f3f5b80b3b3106a889
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675804"
---
# <a name="sap-ase-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>DBMS-implementatie voor SAP-werkbelasting in virtuele Azure-machines voor SAP ASE

In dit document worden verschillende gebieden besproken waarmee u rekening moet houden wanneer u SAP ASE implementeert in azure IaaS. Als een voor waarde voor dit document, moet u de document [overwegingen voor azure virtual machines DBMS-implementatie van de SAP-werk belasting](dbms_guide_general.md) en andere hand leidingen in de [SAP-werk belasting op de Azure-documentatie](./get-started.md)lezen. Dit document behandelt de SAP-ASE die worden uitgevoerd op Linux en op Windows-besturings systemen. De mini maal ondersteunde release op Azure is SAP ASE 16.0.02 (release 16 ondersteunings pakket 2). Het is raadzaam om de nieuwste versie van SAP en het meest recente patch niveau te implementeren.  Mini maal SAP ASE 16.0.03.07 (versie 16 Support Pack 3 patch niveau 7) wordt aanbevolen.  U kunt de meest recente versie van SAP vinden in de [beoogde ASE 16,0-release planning en informatie over de CR-lijst](https://wiki.scn.sap.com/wiki/display/SYBASE/Targeted+ASE+16.0+Release+Schedule+and+CR+list+Information).

Meer informatie over de versie ondersteuning voor SAP-toepassingen of de locatie van de installatie media vindt u naast de SAP-product beschikbaarheids matrix op de volgende locaties:

- [SAP-ondersteunings Opmerking #2134316](https://launchpad.support.sap.com/#/notes/2134316)
- [SAP-ondersteunings Opmerking #1941500](https://launchpad.support.sap.com/#/notes/1941500)
- [SAP-ondersteunings Opmerking #1590719](https://launchpad.support.sap.com/#/notes/1590719)
- [SAP-ondersteunings Opmerking #1973241](https://launchpad.support.sap.com/#/notes/1973241)

Opmerking: in de documentatie binnen en buiten de SAP-wereld wordt naar de naam van het product verwezen als Sybase ASE of SAP ASE, of in sommige gevallen beide. Om consistent te blijven, gebruiken we de naam **SAP ASE** in deze documentatie.

## <a name="operating-system-support"></a>Ondersteuning voor besturings systeem
De SAP-product beschikbaarheids matrix bevat de ondersteunde combi Naties van besturings systemen en SAP-kernel voor elke SAP-toepassing.  Linux-distributies SUSE 12. x, SUSE 15. x, Red Hat 7. x worden volledig ondersteund.  Oracle Linux als besturings systeem voor SAP ASE wordt niet ondersteund.  Het is raadzaam om de meest recente Linux-releases te gebruiken die beschikbaar zijn. Windows-klanten moeten versies van Windows Server 2016 of Windows Server 2019 gebruiken.  Oudere versies van Windows, zoals Windows 2012, worden technisch ondersteund, maar de meest recente versie van Windows wordt altijd aanbevolen.


## <a name="specifics-to-sap-ase-on-windows"></a>Details van SAP-ASE in Windows
Vanaf Microsoft Azure kunt u uw bestaande SAP ASE-toepassingen migreren naar Azure Virtual Machines. Met SAP ASE in een virtuele machine van Azure kunt u de total cost of ownership van de implementatie, het beheer en het onderhoud van toepassingen op bedrijfs niveau verminderen door deze toepassingen eenvoudig te migreren naar Microsoft Azure. Met SAP ASE in een virtuele machine van Azure kunnen beheerders en ontwikkel aars nog steeds gebruikmaken van dezelfde hulpprogram ma's voor ontwikkeling en beheer die on-premises beschikbaar zijn.

Microsoft Azure biedt talrijke verschillende typen virtuele machines waarmee u kleinste SAP-systemen en-landschappen kunt uitvoeren tot grote SAP-systemen en landschappen met duizenden gebruikers. SAP-beschik bare SAP'S-nummers van de verschillende SAP gecertificeerde VM Sku's vindt u in [SAP-ondersteunings opmerking #1928533](https://launchpad.support.sap.com/#/notes/1928533).

Documentatie voor het installeren van SAP ASE in Windows vindt u in de [hand leiding voor de installatie handleiding voor SAP ASE voor Windows](https://help.sap.com/viewer/36031975851a4f82b1022a9df877280b/16.0.3.7/en-US/a660d3f1bc2b101487cbdbf10069c3ac.html)

Het vergren delen van pagina's in het geheugen is een instelling waarmee wordt voor komen dat de SAP ASE-database buffer wordt uitgewisseld.  Deze instelling is nuttig voor grote drukke systemen met veel geheugen. Neem contact op met BC-DB-SYB voor meer informatie. 


## <a name="linux-operating-system-specific-settings"></a>Specifieke instellingen voor Linux-besturings systeem
Op Linux-Vm's voert u uit `saptune` met profiel SAP-ASE Linux enorme pagina's moet standaard zijn ingeschakeld en kunnen worden geverifieerd met de opdracht  

`cat /proc/meminfo` 

De pagina grootte is doorgaans 2048 KB. Zie het artikel [enorme pagina's op Linux](https://help.sap.com/viewer/ecbccd52e7024feaa12f4e780b43bc3b/16.0.3.7/en-US/a703d580bc2b10149695f7d838203fad.html) voor meer informatie. 


## <a name="recommendations-on-vm-and-disk-structure-for-sap-ase-deployments"></a>Aanbevelingen voor de VM-en schijf structuur voor SAP ASE-implementaties

SAP-ASE voor SAP NetWeaver-toepassingen worden ondersteund in een VM-type dat wordt vermeld in de [SAP-ondersteunings opmerking #1928533](https://launchpad.support.sap.com/#/notes/1928533) typische VM-typen die worden gebruikt voor de middel grote, SAP ASE-database servers in de Data Base zijn  Grote multi-terabyte-data bases kunnen gebruikmaken van VM-typen uit de M-serie. De SAP ASE-transactie logboek schrijf prestaties kunnen worden verbeterd door de Write Accelerator uit de M-serie in te scha kelen. Write Accelerator moet zorgvuldig worden getest met SAP ASE, omdat SAP ASE logboek schrijf bewerkingen uitvoert.  Bekijk de [SAP-ondersteunings opmerking #2816580](../../how-to-enable-write-accelerator.md) en overweeg een prestatie test uit te voeren.  
Write Accelerator is alleen bedoeld voor de transactie logboek schijf. De cache op schijf niveau moet worden ingesteld op geen. Het is niet versteld als Azure Write Accelerator vergelijk bare verbeteringen niet met andere DBMS weergeeft. Op basis van de manier waarop SAP ASE naar het transactie logboek schrijft, kan het zijn dat er weinig voor geen versnelling door Azure Write Accelerator is.
Afzonderlijke schijven worden aanbevolen voor gegevens apparaten en logboek apparaten.  De systeem databases sybsecurity en `saptools` vereisen geen toegewezen schijven en kunnen worden geplaatst op de schijven met de SAP-database gegevens en-logboek apparaten 

![Opslag configuratie voor SAP ASE](./media/dbms-guide-sap-ase/sap-ase-disk-structure.png)

### <a name="file-systems-stripe-size--io-balancing"></a>Bestands systemen, Stripe-grootte & IO-verdeling 
SAP ASE schrijft gegevens opeenvolgend naar schijf opslag apparaten, tenzij anders geconfigureerd. Dit betekent dat een lege SAP ASE-data base met vier apparaten gegevens alleen naar het eerste apparaat schrijft.  De andere schijf apparaten worden alleen naar geschreven wanneer het eerste apparaat vol is.  De hoeveelheid Lees-en schrijf bewerkingen voor elk SAP ASE-apparaat is waarschijnlijk niet hetzelfde. Om schijf-IO te verdelen over alle beschik bare Azure-schijven, moeten Windows-opslag ruimten of Linux LVM2 worden gebruikt. In Linux is het raadzaam om XFS-bestands systeem te gebruiken voor het format teren van de schijven. De grootte van de LVM-Stripe moet worden getest met een prestatie test. 128 KB-Stripe-grootte is een goed uitgangs punt. In Windows moet de grootte van de NTFS-toewijzings eenheid (AUS) worden getest. 64 KB kan worden gebruikt als een begin waarde. 

U kunt het beste een automatische database uitbreiding configureren, zoals wordt beschreven in het artikel een [Automatische database ruimte uitbreiding configureren in SAP Adaptive Server Enter prise](https://blogs.sap.com/2014/07/09/configuring-automatic-database-space-expansion-in-sap-adaptive-server-enterprise/)  en [SAP-ondersteunings Opmerking #1815695](https://launchpad.support.sap.com/#/notes/1815695). 

### <a name="sample-sap-ase-on-azure-virtual-machine-disk-and-file-system-configurations"></a>Voor beeld van SAP-ASE op virtuele machines van Azure, schijf-en bestandssysteem configuraties 
In de onderstaande sjablonen ziet u voor beelden van configuraties voor Linux en Windows. Voordat u de configuratie van de virtuele machine en schijf bevestigt, moet u ervoor zorgen dat de netwerk-en opslag bandbreedte quota van de afzonderlijke virtuele machine voldoende zijn om te voldoen aan de zakelijke vereisten. Houd er ook rekening mee dat verschillende Azure VM-typen verschillende maximum aantallen schijven hebben die kunnen worden gekoppeld aan de virtuele machine. Een E4s_v3 VM heeft bijvoorbeeld een limiet van 48 MB/sec. opslag-i/o-door voer. Als de door Voer van de back-upactiviteit van de data base meer dan 48 MB per seconde vereist, is een groter VM-type met meer opslag bandbreedte door Voer niet te voor komen. Bij het configureren van Azure Storage moet u er ook voor zorgen dat met name met [Azure Premium Storage](../../premium-storage-performance.md) de door Voer en IOPS per GB aan capaciteit worden gewijzigd. Meer informatie over dit onderwerp vindt u in het artikel [welke schijf typen beschikbaar zijn in azure?](../../disks-types.md). De quota's voor specifieke Azure VM-typen worden beschreven in het artikel [geheugen geoptimaliseerde grootte van virtuele machines](../../sizes-memory.md) en de artikelen die eraan zijn gekoppeld. 

> [!NOTE]
>  Als een DBMS-systeem van on-premises naar Azure wordt verplaatst, is het raadzaam om bewaking uit te voeren op de virtuele machine en de CPU, het geheugen, de IOPS en de opslag doorvoer te beoordelen. Vergelijk de piek waarden die worden waargenomen met de VM-quota limieten die zijn gedocumenteerd in de hierboven genoemde artikelen

De onderstaande voor beelden zijn bedoeld ter illustratie en kunnen worden gewijzigd op basis van individuele behoeften. Vanwege het ontwerp van SAP ASE is het aantal gegevens apparaten niet zo kritiek als bij andere data bases. Het aantal gegevens apparaten dat in dit document wordt beschreven, is alleen een hand leiding. 

Een voor beeld van een configuratie voor een Small SAP ASE DB-server met een database grootte van 50 GB – 250 GB, zoals SAP Solution Manager, kan er als volgt uitzien

| Configuratie | Windows | Linux | Opmerkingen |
| --- | --- | --- | --- |
| VM-type | E4s_v3 (4 vCPU/32 GB RAM-geheugen) | E4s_v3 (4 vCPU/32 GB RAM-geheugen) | --- |
| Versneld netwerken | Inschakelen | Inschakelen | ---|
| SAP ASE-versie | 16.0.03.07 of hoger | 16.0.03.07 of hoger | --- |
| aantal gegevens apparaten | 4 | 4 | ---|
| aantal logboek apparaten | 1 | 1 | --- |
| aantal tijdelijke apparaten | 1 | 1 | meer voor SAP BW workload |
| Besturingssysteem | Windows Server 2019 | SUSE 12 SP4/15 SP1 of RHEL 7,6 | --- |
| Schijf aggregatie | Opslagruimten | LVM2 | --- |
| Bestandssysteem | NTFS | XFS |
| Indeling blok grootte | vereist workload testen | vereist workload testen | --- |
| aantal en type gegevens schijven | Premium-opslag: 2 x P10 (RAID0) | Premium-opslag: 2 x P10 (RAID0)| Cache = alleen-lezen |
| aantal en type logboek schijven | Premium-opslag: 1 x P20  | Premium-opslag: 1 x P20 | Cache = geen |
| ASE MaxMemory-para meter | 90% van het fysieke RAM-geheugen | 90% van het fysieke RAM-geheugen | ervan uitgaande dat één instantie |
| aantal back-upapparaten | 4 | 4| --- |
| # en type van back-upschijven | 1 | 1 | --- |


Een voor beeld van een configuratie voor een middel grote SAP ASE DB-server met een database grootte van 250 GB tot 750 GB, zoals een kleiner SAP Business Suite-systeem, kan er als volgt uitzien:

| Configuratie | Windows | Linux | Opmerkingen |
| --- | --- | --- | --- |
| VM-type | E16s_v3 (16 vCPU/128 GB RAM-geheugen) | E16s_v3 (16 vCPU/128 GB RAM-geheugen) | --- |
| Versneld netwerken | Inschakelen | Inschakelen | ---|
| SAP ASE-versie | 16.0.03.07 of hoger | 16.0.03.07 of hoger | --- |
| aantal gegevens apparaten | 8 | 8 | ---|
| aantal logboek apparaten | 1 | 1 | --- |
| aantal tijdelijke apparaten | 1 | 1 | meer voor SAP BW workload |
| Besturingssysteem | Windows Server 2019 | SUSE 12 SP4/15 SP1 of RHEL 7,6 | --- |
| Schijf aggregatie | Opslagruimten | LVM2 | --- |
| Bestandssysteem | NTFS | XFS |
| Indeling blok grootte | vereist workload testen | vereist workload testen | --- |
| aantal en type gegevens schijven | Premium-opslag: 4 x P20 (RAID0) | Premium-opslag: 4 x P20 (RAID0)| Cache = alleen-lezen |
| aantal en type logboek schijven | Premium-opslag: 1 x P20  | Premium-opslag: 1 x P20 | Cache = geen |
| ASE MaxMemory-para meter | 90% van het fysieke RAM-geheugen | 90% van het fysieke RAM-geheugen | ervan uitgaande dat één instantie |
| aantal back-upapparaten | 4 | 4| --- |
| # en type van back-upschijven | 1 | 1 | --- |

Een voor beeld van een configuratie voor een Small SAP ASE DB-server met een database grootte van 750 GB tot 2000 GB, zoals een groter SAP Business Suite-systeem, kan er als volgt uitzien:

| Configuratie | Windows | Linux | Opmerkingen |
| --- | --- | --- | --- |
| VM-type | E64s_v3 (64 vCPU/432 GB RAM) | E64s_v3 (64 vCPU/432 GB RAM) | --- |
| Versneld netwerken | Inschakelen | Inschakelen | ---|
| SAP ASE-versie | 16.0.03.07 of hoger | 16.0.03.07 of hoger | --- |
| aantal gegevens apparaten | 16 | 16 | ---|
| aantal logboek apparaten | 1 | 1 | --- |
| aantal tijdelijke apparaten | 1 | 1 | meer voor SAP BW workload |
| Besturingssysteem | Windows Server 2019 | SUSE 12 SP4/15 SP1 of RHEL 7,6 | --- |
| Schijf aggregatie | Opslagruimten | LVM2 | --- |
| Bestandssysteem | NTFS | XFS |
| Indeling blok grootte | vereist workload testen | vereist workload testen | --- |
| aantal en type gegevens schijven | Premium-opslag: 4 x P30 (RAID0) | Premium-opslag: 4 x P30 (RAID0)| Cache = alleen-lezen |
| aantal en type logboek schijven | Premium-opslag: 1 x P20  | Premium-opslag: 1 x P20 | Cache = geen |
| ASE MaxMemory-para meter | 90% van het fysieke RAM-geheugen | 90% van het fysieke RAM-geheugen | ervan uitgaande dat één instantie |
| aantal back-upapparaten | 4 | 4| --- |
| # en type van back-upschijven | 1 | 1 | --- |


Een voor beeld van een configuratie voor een Small SAP ASE DB-server met een database grootte van 2 TB +, zoals een groter, wereld wijd gebruikt SAP Business Suite-systeem, kan er als volgt uitzien:

| Configuratie | Windows | Linux | Opmerkingen |
| --- | --- | --- | --- |
| VM-type | M-serie (1,0 tot 4,0 TB RAM)  | M-serie (1,0 tot 4,0 TB RAM) | --- |
| Versneld netwerken | Inschakelen | Inschakelen | ---|
| SAP ASE-versie | 16.0.03.07 of hoger | 16.0.03.07 of hoger | --- |
| aantal gegevens apparaten | 32 | 32 | ---|
| aantal logboek apparaten | 1 | 1 | --- |
| aantal tijdelijke apparaten | 1 | 1 | meer voor SAP BW workload |
| Besturingssysteem | Windows Server 2019 | SUSE 12 SP4/15 SP1 of RHEL 7,6 | --- |
| Schijf aggregatie | Opslagruimten | LVM2 | --- |
| Bestandssysteem | NTFS | XFS |
| Indeling blok grootte | vereist workload testen | vereist workload testen | --- |
| aantal en type gegevens schijven | Premium-opslag: 4 x P30 (RAID0) | Premium-opslag: 4 x P30 (RAID0)| Cache = alleen-lezen, overwegen Azure Ultra Disk |
| aantal en type logboek schijven | Premium-opslag: 1 x P20  | Premium-opslag: 1 x P20 | Cache = geen, overwegende Azure Ultra Disk |
| ASE MaxMemory-para meter | 90% van het fysieke RAM-geheugen | 90% van het fysieke RAM-geheugen | ervan uitgaande dat één instantie |
| aantal back-upapparaten | 16 | 16 | --- |
| # en type van back-upschijven | 4 | 4 | LVM2/opslag ruimten gebruiken |


### <a name="backup--restore-considerations-for-sap-ase-on-azure"></a>Overwegingen voor back-up & herstellen voor SAP ASE op Azure
Het verhogen van het aantal gegevens en back-upapparaten verhoogt de back-up-en herstel prestaties. Het is raadzaam om de Azure-schijven die als host fungeren voor het SAP ASE-back-upapparaat, te verwijderen zoals weer gegeven in de tabellen die u eerder hebt getoond. Houd er rekening mee dat het aantal back-upapparaten en schijven moet worden gespreid, en zorg ervoor dat de door Voer van de back-up niet meer dan 40%-50% van het totale VM-doorvoer quotum mag bedragen. Het wordt aanbevolen SAP back-upcompressie als standaard waarde te gebruiken. Meer informatie vindt u in de artikelen:

- [SAP-ondersteunings Opmerking #1588316](https://launchpad.support.sap.com/#/notes/1588316)
- [SAP-ondersteunings Opmerking #1801984](https://launchpad.support.sap.com/#/notes/1801984)
- [SAP-ondersteunings Opmerking #1585981](https://launchpad.support.sap.com/#/notes/1585981) 

Gebruik geen station D:\ of/Temp ruimte als de data base-of logboek dump bestemming.

### <a name="impact-of-database-compression"></a>Impact van database compressie
In configuraties waarbij de I/O-band breedte een beperkende factor kan worden, kunnen metingen, die het aantal IOPS verminderen, de werk belasting die in een IaaS-scenario zoals Azure kan worden uitgevoerd, worden uitgerekt. Daarom is het raadzaam om ervoor te zorgen dat SAP ASE-compressie wordt gebruikt voordat u een bestaande SAP-data base uploadt naar Azure.

De aanbeveling om compressie toe te passen voordat uploadt naar Azure, heeft verschillende oorzaken:

* De hoeveelheid gegevens die naar Azure moet worden geüpload, is lager
* De duur van het uitvoeren van de compressie is korter, ervan uitgaande dat een grotere hardware kan gebruiken met meer Cpu's of een hogere I/O-band breedte of minder I/o-latentie on-premises.
* Kleinere database grootten kunnen leiden tot minder kosten voor schijf toewijzing

Gegevens-en LOB-Compression werken in een virtuele machine die wordt gehost in azure Virtual Machines, zoals on-premises. Voor meer informatie over het controleren of compressie al in gebruik is in een bestaande SAP ASE-data base, raadpleegt u [SAP-ondersteunings opmerking 1750510](https://launchpad.support.sap.com/#/notes/1750510). Voor meer informatie over SAP ASE-database compressie raadpleegt u [SAP-ondersteunings opmerking #2121797](https://launchpad.support.sap.com/#/notes/2121797)

## <a name="high-availability-of-sap-ase-on-azure"></a>Hoge Beschik baarheid van SAP-ASE op Azure 
De hand leiding voor HADR-gebruikers bevat informatie over het instellen en configureren van een ' always-on '-oplossing van 2 node SAP ASE.  Daarnaast wordt een derde herstel knooppunt voor nood gevallen ook ondersteund. SAP ASE ondersteunt veel configuraties met hoge Beschik baarheid, waaronder gedeelde schijven en systeem eigen clusteren van het besturings systeem (zwevend IP-adres). Voor de enige ondersteunde configuratie in azure wordt fout beheer zonder zwevend IP-adres gebruikt.  De methode voor zwevende IP-adressen werkt niet in Azure.  De SAP-kernel is een ' HA-bewuste ' toepassing en weet wat de primaire en secundaire SAP ASE-servers zijn. Er zijn geen nauwe integraties tussen de SAP-ASE en Azure, de interne load balancer van Azure wordt niet gebruikt. Daarom moet de Standard SAP ASE-documentatie worden gevolgd vanaf de [Gebruikers handleiding voor SAP ASE HADR](https://help.sap.com/viewer/efe56ad3cad0467d837c8ff1ac6ba75c/16.0.3.7/en-US/a6645e28bc2b1014b54b8815a64b87ba.html) 

> [!NOTE]
> Voor de enige ondersteunde configuratie in azure wordt fout beheer zonder zwevend IP-adres gebruikt.  De methode voor zwevende IP-adressen werkt niet in Azure. 

### <a name="third-node-for-disaster-recovery"></a>Derde knoop punt voor herstel na nood geval
Meer dan het gebruik van SAP ASE Always-On voor lokale hoge Beschik baarheid, kunt u de configuratie uitbreiden naar een asynchroon gerepliceerd knoop punt in een andere Azure-regio. Documentatie over een dergelijk scenario vindt u [hier](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199).

## <a name="sap-ase-database-encryption--ssl"></a>SAP ASE-database versleuteling & SSL 
SAP software Provisioning Manager (SWPM) biedt een optie voor het versleutelen van de data base tijdens de installatie.  Als u versleuteling wilt gebruiken, kunt u het beste SAP Full Data Base Encryption gebruiken.  Details weer geven die zijn gedocumenteerd in:

- [SAP-ondersteunings Opmerking #2556658](https://launchpad.support.sap.com/#/notes/2556658)
- [SAP-ondersteunings Opmerking #2224138](https://launchpad.support.sap.com/#/notes/2224138)
- [SAP-ondersteunings Opmerking #2401066](https://launchpad.support.sap.com/#/notes/2401066)
- [SAP-ondersteunings Opmerking #2593925](https://launchpad.support.sap.com/#/notes/2593925) 

> [!NOTE]
> Als een SAP ASE-data base is versleuteld, werkt de compressie van de back-updump niet. Zie ook [SAP-ondersteunings opmerking #2680905](https://launchpad.support.sap.com/#/notes/2680905) 

## <a name="sap-ase-on-azure-deployment-checklist"></a>Controle lijst voor SAP-ASE op Azure-implementatie
 
- SAP ASE 16.0.03.07 of hoger implementeren
- Bijwerken naar de nieuwste versie en patches van FaultManager en SAPHostAgent
- Implementeren in het meest recente gecertificeerde besturings systeem, zoals Windows 2019, SuSE 15,1 of RedHat 7,6 of hoger
- Gebruik SAP Certified Vm's: High Memory Azure VM Sku's, zoals Es_v3 of voor x-grote systemen, VM-Sku's van de M-serie worden aanbevolen
- Zoek het quotum voor de gecombineerde doorvoer capaciteit van schijven en het totale aantal VM'S van de virtuele machine met het schijf ontwerp.  Voldoende schijven implementeren
- Schijven samen voegen met behulp van Windows-opslag ruimten of Linux LVM2 met de juiste Stripe-grootte en het bestands systeem
- Voldoende apparaten maken voor gegevens, logboeken, tijdelijke en back-updoeleinden
- Overweeg het gebruik van UltraDisk voor x-grote systemen 
- `saptune`SAP-ASE uitvoeren op Linux-besturings systeem 
- De data base beveiligen met DB-versleuteling: Hiermee worden sleutels in Azure Key Vault hand matig opgeslagen 
- De [SAP on Azure controle lijst](./sap-deployment-checklist.md) volt ooien 
- Logboek back-up en volledige back-up configureren 
- Test HA/DR, back-up maken en herstellen en voer stress & volume test uit 
- Bevestigen dat de automatische data base-extensie werkt 

## <a name="using-dbacockpit-to-monitor-database-instances"></a>Data base-exemplaren bewaken met behulp van DBACockpit
Voor SAP-systemen, die gebruikmaken van SAP ASE als database platform, is de DBACockpit toegankelijk als Inge sloten browser vensters in trans actie DBACockpit of als Webdynpro. De volledige functionaliteit voor het bewaken en beheren van de data base is echter alleen beschikbaar in de Webdynpro-implementatie van de DBACockpit.

Net als bij on-premises systemen zijn er verschillende stappen vereist om alle SAP NetWeaver-functionaliteit in te scha kelen die wordt gebruikt door de Webdynpro-implementatie van de DBACockpit. Volg de [SAP-ondersteunings opmerking #1245200](https://launchpad.support.sap.com/#/notes/1245200) om het gebruik van webdynpros in te scha kelen en de vereiste bestanden te genereren. Wanneer u de instructies in de bovenstaande opmerkingen volgt, configureert u ook Internet Communication Manager ( `ICM` ) samen met de poorten die moeten worden gebruikt voor http-en HTTPS-verbindingen. De standaard instelling voor http ziet er als volgt uit:

> ICM/server_port_0 = vervoerder = HTTP, poort = 8000, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> ICM/server_port_1 = door Voer = HTTPS, poort = 443 $ $, PROCTIMEOUT = 600, TIMEOUT = 600
> 
> 

en de koppelingen die zijn gegenereerd in trans actie DBACockpit zien er ongeveer als volgt uit:

> https: \/ / \<fullyqualifiedhostname> : 44300/SAP/BC/webdynpro/SAP/dba_cockpit
> 
> http: \/ / \<fullyqualifiedhostname> : 8000/SAP/BC/webdynpro/SAP/dba_cockpit
> 
> 

Afhankelijk van de manier waarop de virtuele machine van Azure die als host fungeert voor het SAP-systeem is verbonden met uw AD en DNS, moet u ervoor zorgen dat ICM gebruikmaakt van een volledig gekwalificeerde hostnaam die kan worden opgelost op de computer waar u de DBACockpit van probeert te openen. Zie [SAP-ondersteunings opmerking #773830](https://launchpad.support.sap.com/#/notes/773830) als u wilt weten hoe ICM de volledige gekwalificeerde hostnaam bepaalt op basis van de profiel parameters en stel para meter ICM/host_name_full expliciet in als dat nodig is.

Als u de virtuele machine hebt geïmplementeerd in een Cloud-Only scenario zonder cross-premises-connectiviteit tussen on-premises en Azure, moet u een openbaar IP-adres en een opgeven `domainlabel` . De indeling van de open bare DNS-naam van de virtuele machine ziet er als volgt uit:

> `<custom domainlabel`>. `<azure region`>. cloudapp.azure.com
> 
> 

Meer informatie over de DNS-naam kan worden gevonden [hier] [virtual-machines-azurerm-versus-azuresm].

De SAP-profiel parameter ICM/host_name_full instellen op de DNS-naam van de virtuele machine van Azure de koppeling kan er ongeveer als volgt uitzien:

> https: \/ /mydomainlabel.westeurope.cloudapp.net:44300/SAP/BC/webdynpro/SAP/dba_cockpit
> 
> http: \/ /mydomainlabel.westeurope.cloudapp.net:8000/SAP/BC/webdynpro/SAP/dba_cockpit

In dat geval moet u het volgende doen:

* Regels voor binnenkomend verkeer toevoegen aan de netwerk beveiligings groep in de Azure Portal voor de TCP/IP-poorten die worden gebruikt voor communicatie met ICM
* Voeg regels voor binnenkomend verkeer toe aan de Windows Firewall configuratie voor de TCP/IP-poorten die worden gebruikt voor communicatie met de ICM

Voor een geautomatiseerde import van alle beschik bare correcties is het raadzaam om regel matig de correctie verzameling SAP-opmerking toe te passen die van toepassing is op uw SAP-versie:

* [SAP-ondersteunings Opmerking #1558958](https://launchpad.support.sap.com/#/notes/1558958)
* [SAP-ondersteunings Opmerking #1619967](https://launchpad.support.sap.com/#/notes/1619967)
* [SAP-ondersteunings Opmerking #1882376](https://launchpad.support.sap.com/#/notes/1882376)

Meer informatie over DBA cockpit voor SAP ASE vindt u in de volgende SAP-opmerkingen:

* [SAP-ondersteunings Opmerking #1605680](https://launchpad.support.sap.com/#/notes/1605680)
* [SAP-ondersteunings Opmerking #1757924](https://launchpad.support.sap.com/#/notes/1757924)
* [SAP-ondersteunings Opmerking #1757928](https://launchpad.support.sap.com/#/notes/1757928)
* [SAP-ondersteunings Opmerking #1758182](https://launchpad.support.sap.com/#/notes/1758182)
* [SAP-ondersteunings Opmerking #1758496](https://launchpad.support.sap.com/#/notes/1758496)    
* [SAP-ondersteunings Opmerking #1814258](https://launchpad.support.sap.com/#/notes/1814258)
* [SAP-ondersteunings Opmerking #1922555](https://launchpad.support.sap.com/#/notes/1922555)
* [SAP-ondersteunings Opmerking #1956005](https://launchpad.support.sap.com/#/notes/1956005)


## <a name="useful-links-notes--whitepapers-for-sap-ase"></a>Handige koppelingen, notities & witboeken voor SAP ASE
De start pagina voor de [SAP ASE 16.0.03.07-documentatie](https://help.sap.com/viewer/product/SAP_ASE/16.0.3.7/en-US) bevat koppelingen naar verschillende documenten waarvan de documenten:

- SAP ASE Learning traject-beheer & bewaking
- SAP ASE Learning-reis-installatie & upgrade

zijn handig. Een ander nuttig document is [SAP-toepassingen op SAP Adaptive Server Enter prise best practices voor migratie en runtime](https://assets.cdn.sap.com/sapcom/docs/2016/06/26450353-767c-0010-82c7-eda71af511fa.pdf).

Andere nuttige SAP-ondersteunings opmerkingen zijn:

- [SAP-ondersteunings Opmerking #2134316](https://launchpad.support.sap.com/#/notes/2134316) 
- [SAP-ondersteunings Opmerking #1748888](https://launchpad.support.sap.com/#/notes/1748888) 
- [SAP-ondersteunings Opmerking #2588660](https://launchpad.support.sap.com/#/notes/2588660) 
- [SAP-ondersteunings Opmerking #1680803](https://launchpad.support.sap.com/#/notes/1680803) 
- [SAP-ondersteunings Opmerking #1724091](https://launchpad.support.sap.com/#/notes/1724091) 
- [SAP-ondersteunings Opmerking #1775764](https://launchpad.support.sap.com/#/notes/1775764) 
- [SAP-ondersteunings Opmerking #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP-ondersteunings Opmerking #1928533](https://launchpad.support.sap.com/#/notes/1928533)
- [SAP-ondersteunings Opmerking #2015553](https://launchpad.support.sap.com/#/notes/2015553)
- [SAP-ondersteunings Opmerking #1750510](https://launchpad.support.sap.com/#/notes/1750510) 
- [SAP-ondersteunings Opmerking #1752266](https://launchpad.support.sap.com/#/notes/1752266) 
- [SAP-ondersteunings Opmerking #2162183](https://launchpad.support.sap.com/#/notes/2162183) 
- [SAP-ondersteunings Opmerking #1588316](https://launchpad.support.sap.com/#/notes/158831) 

Andere informatie wordt gepubliceerd op 

- [SAP-toepassingen op SAP-adaptieve server Enter prise](https://community.sap.com/topics/applications-on-ase)
- [SAP ASE InfoCenter](http://infocenter.sybase.com/help/index.jsp) 
- [SAP ASE always on with 3e Setup van het DR-knoop punt](https://techcommunity.microsoft.com/t5/running-sap-applications-on-the/installation-procedure-for-sybase-16-3-patch-level-3-always-on/ba-p/368199)

Een maandelijkse nieuws brief wordt gepubliceerd via [SAP-ondersteunings notitie #2381575](https://launchpad.support.sap.com/#/notes/2381575) 


## <a name="next-steps"></a>Volgende stappen
Raadpleeg het artikel [SAP-workloads op Azure: controle lijst voor planning en implementatie](./sap-deployment-checklist.md)