---
title: ANF-configuratie van de virtuele Azure-machine SAP HANA | Microsoft Docs
description: Aanbevelingen voor Azure NetApp Files opslag voor SAP HANA.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, azure, ANF, HANA, Azure NetApp Files, snap shot
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/23/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: de8af71928ad6a83d4930e4e6e0b8dd257148111
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101666624"
---
# <a name="nfs-v41-volumes-on-azure-netapp-files-for-sap-hana"></a>NFS v4.1-volumes in Azure NetApp Files voor SAP HANA

Azure NetApp Files biedt native NFS-shares die kunnen worden gebruikt voor **/Hana/Shared**-, **/Hana/data**-en **/Hana/log** -volumes. Voor het gebruik van ANF NFS-shares voor de **/Hana/data** -en **/Hana/log** -volumes is het gebruik van het v 4.1 NFS-protocol vereist. Het NFS-protocol v3 wordt niet ondersteund voor het gebruik van **/Hana/data** -en **/Hana/log** -volumes bij het baseren van de shares op ANF. 


> [!IMPORTANT]
> Het NFS v3-protocol dat is geïmplementeerd op Azure NetApp Files, wordt **niet** ondersteund voor **/Hana/data** en **/Hana/log**. Het gebruik van de NFS 4,1 is verplicht voor **/Hana/data** -en **/Hana/log** -volumes van een functie punt van weer gave. Overwegende dat voor het **/Hana/Shared** -volume de NFS v3 of het protocol NFS v 4.1 kan worden gebruikt vanuit een functie punt van weer gave.

## <a name="important-considerations"></a>Belangrijke overwegingen

Houd rekening met de volgende belang rijke overwegingen bij het overwegen van Azure NetApp Files voor de SAP net-Weaver en de SAP HANA:

- De minimale capaciteits pool is 4 TiB  
- De minimale volume grootte is 100 GiB
- Azure NetApp Files en alle virtuele machines, waarbij Azure NetApp Files volumes worden gekoppeld, moeten zich in dezelfde Azure-Virtual Network of in gekoppelde [virtuele netwerken](../../../virtual-network/virtual-network-peering-overview.md) in dezelfde regio bevinden
- Het is belang rijk om de virtuele machines dicht bij de Azure NetApp-opslag te laten implementeren voor een lage latentie.  
- Het geselecteerde virtuele netwerk moet een subnet hebben, zijn overgedragen aan Azure NetApp Files
- Zorg ervoor dat de latentie van de database server naar het ANF-volume wordt gemeten en minder dan 1 milliseconde
- De door Voer van een Azure NetApp-volume is een functie van volume quota en service niveau, zoals beschreven in [service niveau voor Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Bij het wijzigen van de grootte van de HANA Azure NetApp-volumes moet de resulterende door Voer voldoen aan de HANA-systeem vereisten
- Probeer volumes samen te voegen voor meer prestaties in een groter volume, bijvoorbeeld gebruik één volume voor/sapmnt,/usr/sap/trans,... indien mogelijk  
- Azure NetApp Files biedt [export beleid](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md): u kunt de toegestane clients, het toegangs type (lezen&schrijven, alleen-lezen, enz.) beheren. 
- Er is nog geen zone bewust van Azure NetApp Files-functie. Momenteel wordt Azure NetApp Files-functie niet geïmplementeerd in alle beschikbaarheids zones in een Azure-regio. Houd rekening met de mogelijke latentie implicaties in sommige Azure-regio's.   
- De gebruikers-ID voor <b>sid</b>adm en de groeps-id voor `sapsys` op de virtuele machines moeten overeenkomen met de configuratie in azure NetApp files. 

> [!IMPORTANT]
> Voor SAP HANA werk belastingen is lage latentie van cruciaal belang. Werk samen met uw micro soft-vertegenwoordiger om ervoor te zorgen dat de virtuele machines en de Azure NetApp Files-volumes dicht bij elkaar worden geïmplementeerd.  

> [!IMPORTANT]
> Als de gebruikers-ID voor <b>sid</b>adm niet overeenkomt met de groeps-id voor `sapsys` tussen de virtuele machine en de Azure NetApp-configuratie, worden de machtigingen voor bestanden op Azure NetApp-volumes, gekoppeld aan de VM, weer gegeven als `nobody` . Zorg ervoor dat u de juiste gebruikers-ID opgeeft voor <b>sid</b>adm en de groeps-id voor `sapsys` , wanneer u [een nieuw systeem](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) wilt Azure NetApp files.


## <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Grootte van HANA-Data Base op Azure NetApp Files

De door Voer van een Azure NetApp-volume is een functie van de volume grootte en het service niveau, zoals beschreven in [service niveau voor Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). 

Het is belang rijk om te begrijpen dat de prestatie relatie de grootte heeft en dat er fysieke limieten gelden voor een LIF (logische interface) van de SVM (virtuele opslag machine).

In de onderstaande tabel ziet u dat het zinvol is om een groot ' standaard volume ' te maken voor het opslaan van back-ups en dat het niet zinvol is om een ' Ultra ' volume groter dan 12 TB te maken, omdat de fysieke bandbreedte capaciteit van één LIF zou worden overschreden. 

De maximale door Voer voor een LIF en één Linux-sessie ligt tussen 1,2 en 1,4 GB/s. Als u meer door Voer voor/Hana/data nodig hebt, kunt u SAP HANA gegevens volume partitioneren gebruiken om de I/O-activiteit tijdens het opnieuw laden van gegevens of HANA opslag punten over meerdere HANA-gegevens bestanden die zich op meerdere NFS-shares bevinden, te verwijderen. Lees deze artikelen voor meer informatie over HANA-gegevens volume striping:

- [De HANA-beheerders handleiding](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [Blog over SAP HANA-gegevens volumes partitioneren](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [SAP-notitie #2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [SAP-notitie #2700123](https://launchpad.support.sap.com/#/notes/2700123)


| Grootte  | Doorvoer standaard | Premium-doorvoer | Maximale door Voer |
| --- | --- | --- | --- |
| 1 TB | 16 MB/sec. | 64 MB/sec. | 128 MB/sec. |
| 2 TB | 32 MB/sec. | 128 MB/sec. | 256 MB/sec. |
| 4 TB | 64 MB/sec. | 256 MB/sec. | 512 MB/sec. |
| 10 TB | 160 MB/sec. | 640 MB/sec. | 1.280 MB/sec. |
| 15 TB | 240 MB/sec. | 960 MB/sec. | 1.400 MB/sec. |
| 20 TB | 320 MB/sec. | 1.280 MB/sec. | 1.400 MB/sec. |
| 40 TB | 640 MB/sec. | 1.400 MB/sec. | 1.400 MB/sec. |

Het is belang rijk om te begrijpen dat de gegevens worden geschreven naar dezelfde Ssd's in de back-end van de opslag. Het prestatie quotum van de capaciteits groep is gemaakt om de omgeving te kunnen beheren.
De opslag-Kpi's zijn gelijk voor alle HANA-database grootten. In bijna alle gevallen weerspiegelt deze veronderstelling niet de realiteit en de verwachte klant. De omvang van HANA-systemen betekent niet noodzakelijkerwijs dat een klein systeem een lage opslag doorvoer vereist: en een groot systeem een hoge opslag doorvoer vereist. Maar over het algemeen kunnen we hogere doorvoer vereisten verwachten voor grotere HANA-data base-exemplaren. Als gevolg van de aanpassings regels van SAP voor de onderliggende hardware bieden dergelijke grotere HANA-instanties ook meer CPU-resources en een hogere parallelle factor in taken als het laden van gegevens nadat een instantie opnieuw is opgestart. Als gevolg hiervan moeten de volume grootten worden toegepast op de verwachtingen en vereisten van de klant. En niet alleen op basis van de vereisten voor zuivere capaciteit.

Bij het ontwerpen van de infra structuur voor SAP in azure moet u rekening houden met enkele minimale vereisten voor opslag doorvoer (voor producties) door SAP, die worden omgezet in kenmerken met minimale door Voer van:

| Volume type en I/O-type | Minimale KPI vereist door SAP | Premium-service niveau | Ultra service-niveau |
| --- | --- | --- | --- |
| Logboek volume schrijven | 250 MB/sec | 4 TB | 2 TB |
| Gegevens volume schrijven | 250 MB/sec | 4 TB | 2 TB |
| Gegevens volume lezen | 400 MB/sec. | 6,3 TB | 3,2 TB |

Aangezien alle drie de Kpi's zijn vereist, moet het **/Hana/data** -volume worden aangepast aan de grotere capaciteit om te voldoen aan de minimale Lees vereisten.

Voor HANA-systemen, die geen hoge band breedte vereisen, kunnen de grootte van het ANF-volume kleiner zijn. En als een HANA-systeem meer door Voer vereist, zou het volume kunnen worden aangepast door de grootte van de capaciteit online te wijzigen. Er zijn geen Kpi's gedefinieerd voor back-upvolumes. De door Voer van het back-upvolume is echter essentieel voor een goed presterende omgeving. Log: de prestaties van het gegevens volume moeten zijn ontworpen voor de verwachtingen van de klant.

> [!IMPORTANT]
> Onafhankelijk van de capaciteit die u implementeert op één NFS-volume, wordt de door Voer verwacht tot een hoeveelheid van 1,2-1.4 GB/sec. band breedte die wordt gebruikt door een consument op een virtuele machine. Dit heeft te maken met de onderliggende architectuur van de ANF-aanbieding en de gerelateerde Linux-sessie limieten rondom NFS. De prestatie-en doorvoer aantallen zoals beschreven in de [resultaten van het artikel prestatie benchmark test voor Azure NetApp files](../../../azure-netapp-files/performance-benchmarks-linux.md) zijn uitgevoerd op een gedeeld NFS-volume met meerdere client-vm's en als gevolg van meerdere sessies. Dit scenario wijkt af van het scenario dat we in SAP meten. Waarbij de door Voer van een enkele virtuele machine wordt gemeten op basis van een NFS-volume. Gehost op ANF.

Om te voldoen aan de vereisten voor de minimale door Voer van SAP voor gegevens en Logboeken, en volgens de richt lijnen voor **/Hana/Shared**, zien de aanbevolen grootten er als volgt uit:

| Volume | Grootte<br /> Laag Premium Storage | Grootte<br /> Ultra Storage-laag | Ondersteund NFS-protocol |
| --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v 4.1 |
| /hana/data | 6,3 TiB | 3,2 TiB | v 4.1 |
| /Hana/Shared omhoog schalen | Min (1 TB, 1 x RAM)  | Min (1 TB, 1 x RAM) | v3 of v 4.1 |
| /Hana/Shared-uitschalen | 1 x RAM-geheugen van worker-knoop punt<br /> per 4 worker-knoop punten  | 1 x RAM-geheugen van worker-knoop punt<br /> per 4 worker-knoop punten  | v3 of v 4.1 |
| /hana/logbackup | 3 x RAM-geheugen  | 3 x RAM-geheugen | v3 of v 4.1 |
| /hana/backup | 2 x RAM-geheugen  | 2 x RAM-geheugen | v3 of v 4.1 |

Voor alle volumes wordt NFS v 4.1 nadrukkelijk aanbevolen

De grootte van de back-upvolumes zijn schattingen. Er moeten exacte vereisten worden gedefinieerd op basis van de werk belasting-en bewerkings processen. Voor back-ups kunt u veel volumes voor verschillende SAP HANA exemplaren consolideren op een (of twee) grotere volumes, die een lager service niveau kunnen hebben dan ANF.

> [!NOTE]
> De Azure NetApp Files, de aanbevelingen voor de grootte die in dit document worden vermeld, zijn gericht op de minimale vereisten SAP houdt bij hun infrastructuur providers. In echte klant implementaties en werkbelasting scenario's, die mogelijk niet voldoende zijn. U kunt deze aanbevelingen als uitgangs punt gebruiken en aanpassen op basis van de vereisten van uw specifieke werk belasting.  

Daarom kunt u overwegen om een vergelijk bare door Voer voor de ANF-volumes te implementeren, zoals vermeld voor Ultra Disk-opslag. Denk ook na over de grootten voor de volumes die worden weer gegeven voor de verschillende VM-Sku's zoals uitgevoerd in de ultra Disk-tabellen.

> [!TIP]
> U kunt de grootte van Azure NetApp Files volumes dynamisch opnieuw instellen, zonder dat u de volumes nodig hebt `unmount` , de virtuele machines stoppen of SAP Hana stoppen. Zo kan de toepassing voldoen aan de verwachte en onvoorziene doorvoer vereisten.

Documentatie over het implementeren van een SAP HANA scale-out configuratie met een stand-by-knoop punt met behulp van NFS v 4.1-volumes die worden gehost in ANF, wordt gepubliceerd in [SAP Hana uitschalen met het stand-by-knoop punt op virtuele machines van Azure met Azure NetApp files op SuSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md).


## <a name="availability"></a>Beschikbaarheid
Updates en upgrades van het ANF-systeem worden toegepast zonder dat dit van invloed is op de klant omgeving. De gedefinieerde [Sla is 99,99%](https://azure.microsoft.com/support/legal/sla/netapp/).


## <a name="volumes-and-ip-addresses-and-capacity-pools"></a>Volumes en IP-adressen en capaciteits Pools
Met ANF is het belang rijk om te begrijpen hoe de onderliggende infra structuur is gebouwd. Een capaciteits groep is alleen een structuur, waardoor het eenvoudiger is om een facturerings model voor ANF te maken. Een capaciteits pool heeft geen fysieke relatie met de onderliggende infra structuur. Als u een capaciteits groep maakt, wordt er alleen een shell gemaakt, die niet meer kan worden gefactureerd. Wanneer u een volume maakt, wordt de eerste SVM (virtuele opslag machine) gemaakt op een cluster met verschillende NetApp-systemen. Er wordt één IP-adres voor deze SVM gemaakt voor toegang tot het volume. Als u meerdere volumes maakt, worden alle volumes gedistribueerd in deze SVM via deze multi-controller NetApp-cluster. Zelfs als u slechts één IP-adres krijgt, worden de gegevens gedistribueerd over meerdere controllers. ANF heeft een logica die automatisch klant werkbelastingen distribueert zodra de volumes of/en capaciteit van de geconfigureerde opslag een intern vooraf gedefinieerd niveau bereiken. U zult merken dat dergelijke gevallen worden genoteerd, omdat er een nieuw IP-adres wordt toegewezen voor toegang tot de volumes.

##<a name="log-volume-and-log-backup-volume"></a>Volume en logboek back-upvolume vastleggen
Het logboek volume (**/Hana/log**) wordt gebruikt voor het schrijven van het online logboek voor opnieuw uitvoeren. Er bevinden zich dus geopende bestanden op dit volume en het is niet verstandig om een moment opname te maken van dit volume. Online logboeken voor opnieuw uitvoeren worden gearchiveerd of opgeslagen in het back-upvolume van het logboek wanneer het online logboek bestand voor opnieuw uitvoeren vol is of een opnieuw uitvoeren van logboek back-up wordt uitgevoerd. Voor redelijke back-upprestaties moet het volume van de back-up van het logboek een goede door voer hebben. Om de opslag kosten te optimaliseren, kan het zinvol zijn om het logboek back-up-volume van meerdere HANA-instanties samen te voegen. Zodat meerdere HANA-exemplaren gebruikmaken van hetzelfde volume en de back-ups naar verschillende directory's kunnen schrijven. Met een dergelijke consolidatie kunt u meer door Voer doen, omdat u het volume iets groter moet maken. 

Hetzelfde geldt voor het volume waarvoor u de volledige HANA-data bases met write-back-ups gebruikt.  
 

## <a name="backup"></a>Backup
Behalve streaming-back-ups en Azure back-upservices back-ups maken van SAP HANA-data bases, zoals beschreven in de [back-upgids voor het artikel voor SAP Hana op Azure virtual machines](./sap-hana-backup-guide.md), kan Azure NetApp files de mogelijkheid om back-ups op basis van opslag te maken uitvoeren. 

SAP HANA ondersteunt:

- Back-ups op basis van opslag momentopnamen van SAP HANA 1,0 SPS7 op
- Momentopname back-ups op basis van opslag voor MDC-(multi data base container) HANA-omgevingen van SAP HANA 2,0 SPS4 op


Het maken van back-ups op basis van opslag momentopnamen is een eenvoudige procedure met vier stappen. 
1. Een HANA (interne) database momentopname maken: een activiteit die u of hulpprogram ma's moet uitvoeren 
1. SAP HANA schrijft gegevens naar de datafiles om een consistente status te maken op de opslag-HANA voert deze stap uit als gevolg van het maken van een HANA-moment opname
1. Een moment opname maken op het **/Hana/data** -volume in de opslag: een stap die u of hulpprogram ma's moet uitvoeren. Het is niet nodig om een moment opname op het **/Hana/log** -volume uit te voeren
1. De HANA (interne) database momentopname verwijderen en de normale werking hervatten: een stap die u of hulpprogram ma's moet uitvoeren

> [!WARNING]
> De laatste stap ontbreekt of het uitvoeren van de laatste stap heeft ernstige gevolgen voor de geheugen vraag van SAP HANA en kan leiden tot een onderbreking van SAP HANA

```
BACKUP DATA FOR FULL SYSTEM CREATE SNAPSHOT COMMENT 'SNAPSHOT-2019-03-18:11:00';
```

![Back-up van ANF-moment opnamen voor SAP HANA](media/hana-vm-operations-netapp/storage-snapshot-scenario.png)

```
az netappfiles snapshot create -g mygroup --account-name myaccname --pool-name mypoolname --volume-name myvolname --name mysnapname 
```

![ANF moment opname van de back-up voor SAP HANA part2](media/hana-vm-operations-netapp/storage-snapshot.png)

```
BACKUP DATA FOR FULL SYSTEM CLOSE SNAPSHOT BACKUP_ID 47110815 SUCCESSFUL SNAPSHOT-2020-08-18:11:00';
```

Deze back-upprocedure voor moment opnamen kan op verschillende manieren worden beheerd, met behulp van verschillende hulpprogram ma's. Een voor beeld is het python-script ' ntaphana_azure. py ' dat beschikbaar is op GitHub [https://github.com/netapp/ntaphana](https://github.com/netapp/ntaphana) Dit is een voorbeeld code, als ' as-is ' zonder onderhoud of ondersteuning.



> [!CAUTION]
> Een moment opname op zichzelf is geen beveiligde back-up omdat deze zich in dezelfde fysieke opslag bevindt als het volume dat u zojuist een moment opname van hebt gemaakt. Het is verplicht om ten minste één moment opname per dag te beveiligen op een andere locatie. Dit kan in dezelfde omgeving worden gedaan, in een externe Azure-regio of in Azure Blob-opslag.


Voor gebruikers van CommVault backup-producten is een tweede optie CommVault IntelliSnap V. 11.21 en hoger. Deze of latere versies van CommVault bieden Azure NetApp Files ondersteuning. Het artikel [CommVault IntelliSnap 11,21](https://documentation.commvault.com/11.21/essential/116350_getting_started_with_backup_and_restore_operations_for_azure_netapp_file_services_smb_shares_and_nfs_shares.html) bevat meer informatie.


### <a name="back-up-the-snapshot-using-azure-blob-storage"></a>Back-up maken van de moment opname met Azure Blob Storage
Back-ups maken naar Azure Blob-opslag is een kosteneffectieve en snelle methode voor het opslaan van back-ups op basis van ANF, HANA-database opslag. Als u de moment opnamen wilt opslaan in Azure Blob-opslag, kunt u het azcopy-hulp programma het beste gebruiken. Down load de nieuwste versie van dit hulp programma en installeer het, bijvoorbeeld in de bin-map waarin het python-script van GitHub is geïnstalleerd.
Down load het meest recente azcopy-hulp programma:

```
root # wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1
Saving to: ‘azcopy_v10.tar.gz’
```

De meest geavanceerde functie is de optie synchroniseren. Als u de optie SYNC gebruikt, houdt azcopy de bron en de doel directory gesynchroniseerd. Het gebruik van de para meter **--Delete-Destination** is belang rijk. Als u deze para meter niet opgeeft, worden de bestanden op de doel site niet door azcopy verwijderd en wordt het ruimte gebruik op de doel zijde verg root. Maak een blok-BLOB-container in uw Azure Storage-account. Maak vervolgens de SAS-sleutel voor de BLOB-container en synchroniseer de map met moment opnamen naar de Azure Blob-container.

Als er bijvoorbeeld een dagelijkse moment opname moet worden gesynchroniseerd met de Azure Blob-container om de gegevens te beveiligen. En slechts één moment opname moet worden bewaard, maar de onderstaande opdracht kan worden gebruikt.

```
root # > azcopy sync '/hana/data/SID/mnt00001/.snapshot' 'https://azacsnaptmytestblob01.blob.core.windows.net/abc?sv=2021-02-02&ss=bfqt&srt=sco&sp=rwdlacup&se=2021-02-04T08:25:26Z&st=2021-02-04T00:25:26Z&spr=https&sig=abcdefghijklmnopqrstuvwxyz' --recursive=true --delete-destination=true
```

## <a name="next-steps"></a>Volgende stappen
Lees het artikel:

- [SAP HANA hoge Beschik baarheid voor virtuele machines van Azure](./sap-hana-availability-overview.md)