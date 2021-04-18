---
title: Planning voor een Azure File Sync implementatie | Microsoft Docs
description: Plan een implementatie met Azure File Sync, een service waarmee u verschillende Azure-bestands shares in de cache kunt opslaan op een on-premises Windows Server- of cloud-VM.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions
ms.openlocfilehash: 9382feeb16f2f6a82b946c05a6b4780866fdda5c
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600145"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planning voor de implementatie van Azure Files Sync

:::row:::
    :::column:::
        [![Interview en demo met de introductie Azure File Sync- klik om af te spelen.](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Azure File Sync is een service waarmee u verschillende Azure-bestands shares in de cache kunt opslaan op een on-premises Windows Server- of cloud-VM. 
        
        In dit artikel maakt u kennis met Azure File Sync en functies. Als u bekend bent met Azure File Sync, kunt u de implementatiehandleiding voor Azure File Sync [volgen om](storage-sync-files-deployment-guide.md) deze service uit te proberen.        
    :::column-end:::
:::row-end:::

De bestanden worden opgeslagen in de cloud in [Azure-bestands shares.](storage-files-introduction.md) Azure-bestands shares kunnen op twee manieren worden gebruikt: door deze serverloze Azure-bestands shares (SMB) rechtstreeks te mounten of door Azure-bestands shares on-premises op te Azure File Sync. Met welke implementatieoptie u kiest, worden de aspecten gewijzigd die u moet overwegen bij het plannen van uw implementatie. 

- **Directe bevestiging van** een Azure-bestands share: omdat Azure Files SMB-toegang biedt, kunt u Azure-bestands shares on-premises of in de cloud met behulp van de standaard SMB-client die beschikbaar is in Windows, macOS en Linux. Omdat Azure-bestands shares serverloos zijn, is voor implementatie voor productiescenario's geen beheer van een bestandsserver of NAS-apparaat vereist. Dit betekent dat u geen softwarepatches hoeft toe te passen of fysieke schijven hoeft te verwisselen. 

- **Azure-bestands share on-premises** cachen met Azure File Sync: met Azure File Sync kunt u de bestands shares van uw organisatie centraliseren in Azure Files, terwijl u de flexibiliteit, prestaties en compatibiliteit van een on-premises bestandsserver be behouden. Azure File Sync transformeert een on-premises (of cloud) Windows Server naar een snelle cache van uw Azure-bestands share. 

## <a name="management-concepts"></a>Beheerconcepten
Een Azure File Sync-implementatie heeft drie fundamentele beheerobjecten:

- **Azure-bestands share:** een Azure-bestands share is een serverloze cloudbestands share die het *cloud-eindpunt* van een Azure File Sync synchronisatierelatie biedt. Bestanden in een Azure-bestands share zijn rechtstreeks toegankelijk met SMB of het FileREST-protocol, hoewel we u aansporen om voornamelijk toegang te krijgen tot de bestanden via de Windows Server-cache wanneer de Azure-bestands share wordt gebruikt met Azure File Sync. Dit komt door Azure Files een efficiënt mechanisme voor wijzigingsdetectie zoals Windows Server heeft. Wijzigingen in de Azure-bestands share nemen dus direct de tijd om terug te gaan naar de server-eindpunten.
- **Server-eindpunt:** het pad op de Windows-server dat wordt gesynchroniseerd met een Azure-bestands share. Dit kan een specifieke map op een volume of de hoofdmap van het volume zijn. Er kunnen meerdere server-eindpunten op hetzelfde volume bestaan als hun naamruimten elkaar niet overlappen.
- **Synchronisatiegroep:** het object dat de synchronisatierelatie definieert tussen een **cloud-eindpunt** of Azure-bestands share en een server-eindpunt. Eindpunten binnen een synchronisatiegroep worden onderling synchroon gehouden. Als u bijvoorbeeld twee afzonderlijke sets bestanden hebt die u wilt beheren met Azure File Sync, maakt u twee synchronisatiegroepen en voegt u verschillende eindpunten toe aan elke synchronisatiegroep.

### <a name="azure-file-share-management-concepts"></a>Beheerconcepten voor Azure-bestands delen
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Azure File Sync managementconcepten
Synchronisatiegroepen worden geïmplementeerd in **Opslagsynchronisatieservices.** Dit zijn objecten op het hoogste niveau die servers registreren voor gebruik met Azure File Sync en de synchronisatiegroepsrelaties bevatten. De opslagsynchronisatieserviceresource is een peer van de resource van het opslagaccount en kan op dezelfde manier worden geïmplementeerd in Azure-resourcegroepen. Een opslagsynchronisatieservice kan synchronisatiegroepen maken die Azure-bestands shares bevatten voor meerdere opslagaccounts en meerdere geregistreerde Windows-servers.

Voordat u een synchronisatiegroep in een opslagsynchronisatieservice kunt maken, moet u eerst een Windows-server registreren bij de opslagsynchronisatieservice. Hiermee maakt u **een geregistreerd serverobject** dat een vertrouwensrelatie tussen uw server of cluster en de opslagsynchronisatieservice vertegenwoordigt. Als u een opslagsynchronisatieservice wilt registreren, moet u eerst de Azure File Sync op de server installeren. Een afzonderlijke server of cluster kan slechts bij één opslagsynchronisatieservice tegelijk worden geregistreerd.

Een synchronisatiegroep bevat één cloud-eindpunt of Azure-bestands share en ten minste één server-eindpunt. Het server-eindpuntobject bevat de instellingen waarmee de mogelijkheid voor **cloudopslaglagen** wordt geconfigureerd. Dit biedt de caching-mogelijkheid van Azure File Sync. Als u wilt synchroniseren met een Azure-bestands share, moet het opslagaccount met de Azure-bestands share zich in dezelfde Azure-regio als de opslagsynchronisatieservice hebben.

> [!Important]  
> U kunt wijzigingen aanbrengen in de naamruimte van een cloud-eindpunt of server-eindpunt in de synchronisatiegroep en uw bestanden laten synchroniseren met de andere eindpunten in de synchronisatiegroep. Als u het cloud-eindpunt (Azure-bestands share) rechtstreeks wijzigt, moeten wijzigingen eerst worden gedetecteerd door een Azure File Sync wijzigingsdetectie. Een wijzigingsdetectie job wordt slechts eenmaal per 24 uur gestart voor een cloud-eindpunt. Zie veelgestelde vragen [Azure Files meer informatie.](storage-files-faq.md#afs-change-detection)

### <a name="consider-the-count-of-storage-sync-services-needed"></a>Houd rekening met het aantal benodigde opslagsynchronisatieservices
In een vorige sectie wordt de kernresource besproken die moet worden geconfigureerd voor Azure File Sync: een *opslagsynchronisatieservice.* Een Windows-server kan slechts bij één opslagsynchronisatieservice worden geregistreerd. Het is daarom vaak het beste om slechts één opslagsynchronisatieservice te implementeren en alle servers die deze service heeft geregistreerd. 

Maak alleen meerdere opslagsynchronisatieservices als u het volgende hebt:
* afzonderlijke sets servers die nooit gegevens met elkaar mogen uitwisselen. In dit geval wilt u het systeem zo ontwerpen dat bepaalde sets servers worden uitgesloten voor synchronisatie met een Azure-bestands share die al wordt gebruikt als een cloud-eindpunt in een synchronisatiegroep in een andere opslagsynchronisatieservice. Een andere manier om dit te bekijken is dat Windows-servers die zijn geregistreerd bij een andere opslagsynchronisatieservice, niet kunnen worden gesynchroniseerd met dezelfde Azure-bestands share.
* een behoefte aan meer geregistreerde servers of synchronisatiegroepen dan één opslagsynchronisatieservice kan ondersteunen. Bekijk de [Azure File Sync schaaldoelen voor](storage-files-scale-targets.md#azure-file-sync-scale-targets) meer informatie.

## <a name="plan-for-balanced-sync-topologies"></a>Uitgebalanceerde synchronisatie-topologies plannen
Voordat u resources implementeert, is het belangrijk om te plannen wat u wilt synchroniseren op een lokale server, met welke Azure-bestands share. Als u een plan maakt, kunt u bepalen hoeveel opslagaccounts, Azure-bestands shares en synchronisatie-resources u nodig hebt. Deze overwegingen zijn nog steeds relevant, zelfs als uw gegevens zich momenteel niet op een Windows-server bevinden of op de server die u op de lange termijn wilt gebruiken. De [migratiesectie kan](#migration) helpen bij het bepalen van de juiste migratiepaden voor uw situatie.

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="windows-file-server-considerations"></a>Overwegingen voor Windows-bestandsservers
Als u de synchronisatiemogelijkheid op Windows Server wilt inschakelen, moet u de Azure File Sync agent installeren. De Azure File Sync-agent biedt twee hoofdonderdelen: , de Windows-achtergrondservice die verantwoordelijk is voor het bewaken van wijzigingen op de server-eindpunten en het initiëren van synchronisatiesessies, en , een bestandssysteemfilter dat opslag in cloudlagen en snel herstel na noodherstel mogelijk `FileSyncSvc.exe` `StorageSync.sys` maakt.  

### <a name="operating-system-requirements"></a>Vereisten voor het besturingssysteem
Azure File Sync wordt ondersteund met de volgende versies van Windows Server:

| Versie | Ondersteunde SKU's | Ondersteunde implementatieopties |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter, Standard en IoT | Full en Core |
| Windows Server 2016 | Datacenter, Standard en Opslagserver | Full en Core |
| Windows Server 2012 R2 | Datacenter, Standard en Opslagserver | Full en Core |

Toekomstige versies van Windows Server worden toegevoegd wanneer ze worden uitgebracht.

> [!Important]  
> We raden u aan om alle servers die u gebruikt met Azure File Sync bijgewerkt te houden met de nieuwste updates van Windows Update. 

### <a name="minimum-system-resources"></a>Minimale systeembronnen
Azure File Sync vereist een server, fysiek of virtueel, met ten minste één CPU en minimaal 2 GiB geheugen.

> [!Important]  
> Als de server wordt uitgevoerd in een virtuele machine met dynamisch geheugen ingeschakeld, moet de virtuele machine worden geconfigureerd met minimaal 2048 MiB aan geheugen.

Voor de meeste productieworkloads wordt niet aangeraden om een Azure File Sync met alleen de minimale vereisten te configureren. Zie [Aanbevolen systeemresources](#recommended-system-resources) voor meer informatie.

### <a name="recommended-system-resources"></a>Aanbevolen systeembronnen
Net als bij elke serverfunctie of -toepassing worden de systeemresourcevereisten voor Azure File Sync bepaald door de schaal van de implementatie; Grotere implementaties op een server vereisen meer systeembronnen. Voor Azure File Sync wordt de schaal bepaald door het aantal objecten op de server-eindpunten en het verloop van de gegevensset. Eén server kan server-eindpunten hebben in meerdere synchronisatiegroepen en het aantal objecten dat wordt vermeld in de volgende tabelaccounts voor de volledige naamruimte waar een server aan is gekoppeld. 

Bijvoorbeeld server-eindpunt A met 10 miljoen objecten + server-eindpunt B met 10 miljoen objecten = 20 miljoen objecten. Voor deze voorbeeldimplementatie raden we 8 CPU's, 16 GiB geheugen aan voor een stabiele status en (indien mogelijk) 48 GiB geheugen voor de eerste migratie.
 
Naamruimtegegevens worden uit prestatieoverwegingen opgeslagen in het geheugen. Daarom hebben grotere naamruimten meer geheugen nodig om goede prestaties te behouden en is voor meer verloop meer CPU nodig om te verwerken. 
 
In de volgende tabel hebben we zowel de grootte van de naamruimte als een conversie naar capaciteit voor typische bestands shares voor algemeen gebruik opgegeven, waarbij de gemiddelde bestandsgrootte 512 KiB is. Als uw bestandsgrootten kleiner zijn, kunt u overwegen om extra geheugen toe te voegen voor dezelfde hoeveelheid capaciteit. Baseer uw geheugenconfiguratie op de grootte van de naamruimte.

| Naamruimtegrootte: bestanden & mappen (miljoenen)  | Typische capaciteit (TiB)  | CPU-kernen  | Aanbevolen geheugen (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (initiële synchronisatie)/ 2 (typisch verloop)      |
| 5        | 2.3     | 2        | 16 (initiële synchronisatie)/ 4 (typisch verloop)    |
| 10       | 4.7     | 4        | 32 (initiële synchronisatie)/ 8 (typisch verloop)   |
| 30       | 14.0    | 8        | 48 (initiële synchronisatie)/ 16 (typisch verloop)   |
| 50       | 23.3    | 16       | 64 (initiële synchronisatie)/ 32 (typisch verloop)  |
| 100*     | 46.6    | 32       | 128 (initiële synchronisatie)/ 32 (typisch verloop)  |

\*Het synchroniseren van meer dan 100 miljoen bestanden & mappen wordt op dit moment niet aanbevolen. Dit is een zachte limiet op basis van onze geteste drempelwaarden. Zie schaalbaarheids- Azure Files [prestatiedoelen voor meer informatie.](storage-files-scale-targets.md#azure-file-sync-scale-targets)

> [!TIP]
> De initiële synchronisatie van een naamruimte is een intensieve bewerking en we raden u aan meer geheugen toe te geven totdat de initiële synchronisatie is voltooid. Dit is niet vereist, maar kan de initiële synchronisatie versnellen. 
> 
> Normaal verloop is 0,5% van de naamruimte die per dag verandert. Voor een hoger verloop kunt u overwegen om meer CPU toe te voegen. 

- Een lokaal gekoppeld volume dat is geformatteerd met het NTFS-bestandssysteem.

### <a name="evaluation-cmdlet"></a>Evaluatie-cmdlet
Voordat u Azure File Sync implementeert, moet u evalueren of het compatibel is met uw systeem met behulp van Azure File Sync evaluatie-cmdlet. Met deze cmdlet wordt gecontroleerd op mogelijke problemen met uw bestandssysteem en gegevensset, zoals niet-ondersteunde tekens of een niet-ondersteunde besturingssysteemversie. De controles hebben betrekking op de meeste, maar niet op alle functies die hieronder worden vermeld; We raden u aan de rest van deze sectie zorgvuldig te lezen om ervoor te zorgen dat uw implementatie soepel verloopt. 

De evaluatie-cmdlet kan worden geïnstalleerd door de Az PowerShell-module te installeren. Deze kan worden geïnstalleerd door de instructies hier te volgen: [Installeren en configureren Azure PowerShell](/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Gebruik  
U kunt het evaluatiehulpprogramma op verschillende manieren aanroepen: u kunt de systeemcontroles, de gegevenssetcontroles of beide uitvoeren. Systeem- en gegevenssetcontroles uitvoeren: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Alleen uw gegevensset testen:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Alleen systeemvereisten testen:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name> -SkipNamespaceChecks
```
 
De resultaten weergeven in CSV:
```powershell
$validation = Invoke-AzStorageSyncCompatibilityCheck C:\DATA
$validation.Results | Select-Object -Property Type, Path, Level, Description, Result | Export-Csv -Path C:\results.csv -Encoding utf8
```

### <a name="file-system-compatibility"></a>Compatibiliteit van bestandssysteem
Azure File Sync wordt alleen ondersteund op rechtstreeks gekoppelde NTFS-volumes. Direct gekoppelde opslag, of DAS, op Windows Server betekent dat het Windows Server-besturingssysteem eigenaar is van het bestandssysteem. DAS kan worden geleverd via het fysiek koppelen van schijven aan de bestandsserver, het koppelen van virtuele schijven aan een bestandsserver-VM (zoals een virtuele machine die wordt gehost door Hyper-V) of zelfs via ISCSI.

Alleen NTFS-volumes worden ondersteund; ReFS, FAT, FAT32 en andere bestandssystemen worden niet ondersteund.

In de volgende tabel ziet u de interop-status van NTFS-bestandssysteemfuncties: 

| Functie | Ondersteuningsstatus | Notities |
|---------|----------------|-------|
| ACL’s (toegangsbeheerlijsten) | Volledig ondersteund | Discretionaire toegangsbeheerlijsten in Windows-stijl blijven behouden door Azure File Sync en worden afgedwongen door Windows Server op server-eindpunten. ACL's kunnen ook worden afgedwongen wanneer u de Azure-bestands share rechtstreeks aan elkaar kunt toevoegen, maar hiervoor is aanvullende configuratie vereist. Zie de [sectie Identiteit](#identity) voor meer informatie. |
| Vaste koppelingen | Overgeslagen | |
| Symbolische koppelingen | Overgeslagen | |
| Bevestigingspunten | Gedeeltelijk ondersteund | Verbindingspunten kunnen de hoofdmap van een server-eindpunt zijn, maar worden overgeslagen als ze zijn opgenomen in de naamruimte van een server-eindpunt. |
| Kruispunten | Overgeslagen | Bijvoorbeeld: Distributed File System mappen DfrsrPrivate en DFSRoots. |
| Reparsepunten | Overgeslagen | |
| NTFS-compressie | Volledig ondersteund | |
| Sparse bestanden | Volledig ondersteund | Sparse bestanden worden gesynchroniseerd (worden niet geblokkeerd), maar ze worden als een volledig bestand gesynchroniseerd met de cloud. Als de bestandsinhoud in de cloud (of op een andere server) wordt gewijzigd, is het bestand niet langer verspreid wanneer de wijziging wordt gedownload. |
| Alternatieve gegevensstromen (ADS) | Behouden, maar niet gesynchroniseerd | Classificatietags die zijn gemaakt door de infrastructuur voor bestandsclassificatie worden bijvoorbeeld niet gesynchroniseerd. Bestaande classificatietags voor bestanden op elk van de server-eindpunten blijven ongewijzigd. |

<a id="files-skipped"></a>Azure File Sync worden ook bepaalde tijdelijke bestanden en systeemmappen overgeslagen:

| Bestand/map | Notitie |
|-|-|
| pagefile.sys | Bestand specifiek voor systeem |
| Desktop.ini | Bestand specifiek voor systeem |
| Duimschroef | Tijdelijk bestand voor miniaturen |
| ehthumbs.db | Tijdelijk bestand voor mediaminiaturen |
| ~$\*.\* | Tijdelijk Office-bestand |
| \*.tmp | Tijdelijk bestand |
| \*.laccdb | Toegang tot DB-vergrendelingsbestand|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Intern synchronisatiebestand|
| \\Informatie over systeemvolume | Map die specifiek is voor volume |
| $RECYCLE. Bin| Map |
| \\SyncShareState | Map voor synchronisatie |

### <a name="failover-clustering"></a>Failoverclustering
Windows Server Failover Clustering wordt ondersteund door Azure File Sync voor de implementatieoptie Bestandsserver voor algemeen gebruik. Failoverclustering wordt niet ondersteund op 'Scale-out bestandsserver voor toepassingsgegevens' (SOFS) of op CSV's (Clustered Shared Volumes).

> [!Note]  
> De Azure File Sync agent moet worden geïnstalleerd op elk knooppunt in een failovercluster om de synchronisatie goed te laten werken.

### <a name="data-deduplication"></a>Gegevensontdubbeling
**Windows Server 2016 en Windows Server 2019**   
Gegevens ontdubbeling wordt ondersteund, ongeacht of opslag in cloudlagen is ingeschakeld of uitgeschakeld op een of meer server-eindpunten op het volume voor Windows Server 2016 en Windows Server 2019. Als u Gegevensverdubbeling inschakelen op een volume met ingeschakelde cloudopslaglagen, kunt u meer bestanden on-premises in de cache opslaan zonder dat u meer opslagruimte inrichten. 

Wanneer gegevensverdubbeling is ingeschakeld op een volume waarop opslag in cloudlagen is ingeschakeld, worden voor ontdubbeling geoptimaliseerde bestanden binnen de locatie van het server-eindpunt gelaagd als een normaal bestand op basis van de beleidsinstellingen voor opslag in cloudlagen. Zodra de voor ontdubbeling geoptimaliseerde bestanden in lagen zijn opgeslagen, wordt de garbage collection-taak voor gegevensverdubbeling automatisch uitgevoerd om schijfruimte vrij te maken door onnodige segmenten te verwijderen waarnaar niet meer wordt verwezen door andere bestanden op het volume.

Houd er rekening mee dat de volumebesparing alleen van toepassing is op de server; uw gegevens in de Azure-bestands share worden niet ontdubbeld.

> [!Note]  
> Ter ondersteuning van gegevensverdubbeling op volumes met cloudopslaglagen ingeschakeld op Windows Server 2019, moet Windows-update [KB4520062 - oktober 2019](https://support.microsoft.com/help/4520062) of een latere maandelijkse rollup-update worden geïnstalleerd en moet Azure File Sync agentversie 12.0.0.0 of hoger zijn vereist.

**Windows Server 2012 R2**  
Azure File Sync biedt geen ondersteuning voor gegevensverdubbeling en cloudopslaglagen op hetzelfde volume in Windows Server 2012 R2. Als Gegevensverdubbeling is ingeschakeld op een volume, moet opslag in cloudlagen worden uitgeschakeld. 

**Opmerkingen**
- Als Gegevensverdubbeling is geïnstalleerd vóór de installatie van de Azure File Sync-agent, is een herstart vereist om gegevensverdubbeling en opslag in cloudlagen op hetzelfde volume te ondersteunen.
- Als Gegevensverdubbeling is ingeschakeld op een volume nadat opslag in cloudlagen is ingeschakeld, optimaliseert de eerste optimalisatie van ontdubbeling bestanden op het volume dat nog niet in een laag is opgeslagen en heeft dit de volgende invloed op opslag in cloudlagen:
    - Het beleid voor vrije ruimte blijft bestanden in lagen opslaan op basis van de vrije ruimte op het volume met behulp van de heatmap.
    - Met datumbeleid wordt opslag in lagen overgeslagen van bestanden die mogelijk anderszins in aanmerking komen voor opslag in lagen omdat de optimalisatie-taak voor ontdubbeling toegang heeft tot de bestanden.
- Voor lopende optimalisatietaken voor ontdubbeling wordt opslag in cloudlagen met datumbeleid vertraagd door de instelling [MinimumBestandAgeDays](/powershell/module/deduplication/set-dedupvolume) voor gegevensverdubbeling als het bestand nog niet in lagen is opgeslagen. 
    - Voorbeeld: als de instelling MinimumFileAgeDays zeven dagen is en het datumbeleid voor cloudopslaglagen 30 dagen is, worden met het datumbeleid bestanden na 37 dagen in lagen opgeslagen.
    - Opmerking: zodra een bestand is gelaagd op Azure File Sync, wordt het bestand overgeslagen met de optimalisatie van ontdubbeling.
- Als een server met Windows Server 2012 R2 met de Azure File Sync-agent is bijgewerkt naar Windows Server 2016 of Windows Server 2019, moeten de volgende stappen worden uitgevoerd ter ondersteuning van gegevensverdubbeling en opslag in cloudlagen op hetzelfde volume:  
    - Verwijder de Azure File Sync voor Windows Server 2012 R2 en start de server opnieuw op.
    - Download de Azure File Sync voor de nieuwe serverbesturingssysteemversie (Windows Server 2016 of Windows Server 2019).
    - Installeer de Azure File Sync agent en start de server opnieuw op.  
    
    Opmerking: de Azure File Sync configuratie-instellingen op de server worden bewaard wanneer de agent wordt verwijderd en opnieuw wordt geïnstalleerd.

### <a name="distributed-file-system-dfs"></a>Distributed File System (DFS)
Azure File Sync ondersteunt samenwerking met DFS-naamruimten (DFS-N) en DSF-replicatie (DFS-R).

**DFS-naamruimten (DFS-N)**: Azure File Sync wordt volledig ondersteund op DFS-N-servers. U kunt de Azure File Sync op een of meer DFS-N-leden installeren om gegevens te synchroniseren tussen de server-eindpunten en het cloud-eindpunt. Zie overzicht van [DFS-naamruimten voor meer informatie.](/windows-server/storage/dfs-namespaces/dfs-overview)
 
**DSF-replicatie (DFS-R)**: omdat DFS-R en Azure File Sync beide replicatieoplossingen zijn, raden we in de meeste gevallen aan om DFS-R te vervangen door Azure File Sync. Er zijn echter verschillende scenario's waarin u DFS-R en Azure File Sync gebruiken:

- U migreert van een DFS-R-implementatie naar een Azure File Sync implementatie. Zie Migrate [a DSF-replicatie (DFS-R) deployment to Azure File Sync (Een DFS-R)-implementatie migreren naar Azure File Sync.](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync)
- Niet elke on-premises server die een kopie van uw bestandsgegevens nodig heeft, kan rechtstreeks met internet worden verbonden.
- Vertakkingsservers consolideren gegevens op één hubserver waarvoor u de Azure File Sync.

Een Azure File Sync en DFS-R werken als het goed is:

1. Azure File Sync cloudopslaglagen moeten worden uitgeschakeld op volumes met DFS-R-gerepliceerde mappen.
2. Server-eindpunten mogen niet worden geconfigureerd in DFS-R alleen-lezen replicatiemappen.

Zie overzicht van DSF-replicatie [voor meer informatie.](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj127250(v=ws.11))

### <a name="sysprep"></a>Sysprep
Het gebruik van sysprep op een server Azure File Sync agent is geïnstalleerd, wordt niet ondersteund en kan leiden tot onverwachte resultaten. Installatie van agent en serverregistratie moeten plaatsvinden na het implementeren van de serverinstallatie-installatie en het voltooien van sysprep mini-setup.

### <a name="windows-search"></a>Windows Search
Als opslag in cloudlagen is ingeschakeld op een server-eindpunt, worden bestanden die gelaagd zijn overgeslagen en niet geïndexeerd door Windows Search. Niet-gelaagde bestanden worden correct geïndexeerd.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Andere HSM-oplossingen (Hierarchical Storage Management)
Er mogen geen andere HSM-oplossingen worden gebruikt met Azure File Sync.

## <a name="performance-and-scalability"></a>Prestatie en schaalbaarheid

Omdat de Azure File Sync-agent wordt uitgevoerd op een Windows Server-computer die verbinding maakt met de Azure-bestands shares, zijn de effectieve synchronisatieprestaties afhankelijk van een aantal factoren in uw infrastructuur: Windows Server en de onderliggende schijfconfiguratie, de netwerkbandbreedte tussen de server en de Azure-opslag, de bestandsgrootte, de totale grootte van de gegevensset en de activiteit op de gegevensset. Omdat Azure File Sync op bestandsniveau werkt, worden de prestatiekenmerken van een Azure File Sync-oplossing beter gemeten in het aantal objecten (bestanden en mappen) dat per seconde wordt verwerkt.

Wijzigingen in de Azure-bestands share met behulp van de Azure Portal of SMB worden niet onmiddellijk gedetecteerd en gerepliceerd als wijzigingen in het server-eindpunt. Azure Files nog geen wijzigingsmeldingen of logboeken heeft, is er dus geen manier om automatisch een synchronisatiesessie te starten wanneer bestanden worden gewijzigd. Op Windows Server gebruikt Azure File Sync [Windows USN-logboeken](/windows/win32/fileio/change-journals) om automatisch een synchronisatiesessie te starten wanneer bestanden worden gewijzigd

Als u wijzigingen in de Azure-bestands share wilt detecteren, Azure File Sync een geplande taak genaamd een wijzigingsdetectie-taak. Een wijzigingsdetectie-taak bevat elk bestand in de bestands share en vergelijkt dit vervolgens met de synchronisatieversie voor dat bestand. Wanneer de taak wijzigingsdetectie vaststelt dat bestanden zijn gewijzigd, Azure File Sync een synchronisatiesessie gestart. De taak voor wijzigingsdetectie wordt elke 24 uur gestart. Omdat de taak wijzigingsdetectie werkt door elk bestand in de Azure-bestands share te opsnoemen, duurt wijzigingsdetectie langer in grotere naamruimten dan in kleinere naamruimten. Voor grote naamruimten kan het langer dan één keer per 24 uur duren om te bepalen welke bestanden zijn gewijzigd.

Zie Metrische prestatiegegevens en [Azure File Sync en schaaldoelen](storage-files-scale-targets.md#azure-file-sync-performance-metrics) voor [Azure File Sync meer informatie](storage-files-scale-targets.md#azure-file-sync-scale-targets)

## <a name="identity"></a>Identiteit
Azure File Sync werkt met uw standaard ad-identiteit zonder speciale instellingen, behalve het instellen van synchronisatie. Wanneer u een Azure File Sync, is de algemene verwachting dat de meeste toegangen via de Azure File Sync-cachingservers gaan in plaats van via de Azure-bestands share. Omdat de server-eindpunten zich op Windows Server bevinden en Windows Server ad- en Windows-stijl ACL's al lange tijd ondersteunt, is er niets meer nodig dan ervoor te zorgen dat de Windows-bestandsservers die zijn geregistreerd bij de opslagsynchronisatieservice lid zijn van een domein. Azure File Sync worden ACL's opgeslagen in de bestanden in de Azure-bestands share en worden deze gerepliceerd naar alle server-eindpunten.

Hoewel het langer duurt om wijzigingen rechtstreeks in de Azure-bestands share te synchroniseren met de server-eindpunten in de synchronisatiegroep, kunt u er ook voor zorgen dat u uw AD-machtigingen ook rechtstreeks in de cloud kunt afdwingen voor uw bestands share. Hiervoor moet u uw opslagaccount toevoegen aan uw on-premises AD, net zoals uw Windows-bestandsservers lid zijn van een domein. Zie Active Directory-overzicht voor meer informatie over het toevoegen van uw opslagaccount aan een Active Directory van de klant [Azure Files active directory.](storage-files-active-directory-overview.md)

> [!Important]  
> Domein dat uw opslagaccount aan Active Directory moet toevoegen, is niet vereist voor een Azure File Sync. Dit is een strikt optionele stap waarmee de Azure-bestands share on-premises ACL's kan afdwingen wanneer gebruikers de Azure-bestands share rechtstreeks aan elkaar toevoegen.

## <a name="networking"></a>Netwerken
De Azure File Sync-agent communiceert met uw opslagsynchronisatieservice en Azure-bestands share met behulp van het Azure File Sync REST-protocol en het FileREST-protocol, die beide altijd HTTPS via poort 443 gebruiken. SMB wordt nooit gebruikt voor het uploaden of downloaden van gegevens tussen uw Windows Server en de Azure-bestands share. Omdat de meeste organisaties HTTPS-verkeer via poort 443 toestaan, is een speciale netwerkconfiguratie meestal niet vereist voor het implementeren van Azure File Sync.

Op basis van het beleid of de unieke wettelijke vereisten van uw organisatie vereist u mogelijk meer beperkende communicatie met Azure en daarom biedt Azure File Sync verschillende mechanismen voor het configureren van netwerken. Op basis van uw vereisten kunt u het volgende doen:

- Tunnelsynchronisatie en upload/download verkeer via uw ExpressRoute of Azure VPN. 
- Maak gebruik van Azure Files en Azure-netwerken zoals service-eindpunten en privé-eindpunten.
- Configureer Azure File Sync om uw proxy in uw omgeving te ondersteunen.
- Netwerkactiviteit van de Azure File Sync.

Zie netwerkoverwegingen Azure File Sync meer informatie over Azure File Sync [en netwerken.](storage-sync-files-networking-overview.md)

## <a name="encryption"></a>Versleuteling
Wanneer u Azure File Sync gebruikt, zijn er drie verschillende versleutelingslagen om rekening mee te houden: versleuteling op de at-rest-opslag van Windows Server, versleuteling in transit tussen de Azure File Sync-agent en Azure, en versleuteling van uw gegevens in de Azure-bestands share. 

### <a name="windows-server-encryption-at-rest"></a>Windows Server-versleuteling -at-rest 
Er zijn twee strategieën voor het versleutelen van gegevens op Windows Server die doorgaans met Azure File Sync werken: versleuteling onder het bestandssysteem, zodat het bestandssysteem en alle gegevens die naar de server worden geschreven, worden versleuteld en versleuteling in de bestandsindeling zelf. Deze methoden sluiten elkaar niet uit; Ze kunnen indien gewenst samen worden gebruikt, omdat het doel van versleuteling anders is.

Als u versleuteling wilt bieden onder het bestandssysteem, biedt Windows Server het Postvak IN van BitLocker. BitLocker is volledig transparant voor Azure File Sync. De belangrijkste reden om een versleutelingsmechanisme zoals BitLocker te gebruiken, is om fysieke exfiltratie van gegevens uit uw on-premises datacenter te voorkomen door iemand die de schijven steelt en om te voorkomen dat een niet-geautoriseerd besturingssysteem sideloadt om niet-geautoriseerde lees-/schrijfgegevens naar uw gegevens uit te voeren. Zie Overzicht van BitLocker voor meer informatie [over BitLocker.](/windows/security/information-protection/bitlocker/bitlocker-overview)

Producten van derden die op dezelfde manier werken als BitLocker, omdat ze zich onder het NTFS-volume Azure File Sync. 

De andere hoofdmethode voor het versleutelen van gegevens is het versleutelen van de gegevensstroom van het bestand wanneer de toepassing het bestand opgeslagen. Sommige toepassingen kunnen dit zelf doen, maar dit is meestal niet het geval. Een voorbeeld van een methode voor het versleutelen van de gegevensstroom van het bestand is Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Active Directory RMS. De belangrijkste reden om een versleutelingsmechanisme zoals AIP/RMS te gebruiken, is om gegevensexfiltratie van gegevens uit uw bestands share te voorkomen door personen die deze naar alternatieve locaties kopiëren, zoals een flashstation, of door deze per e-mail naar een onbevoegde persoon te verzenden. Wanneer de gegevensstroom van een bestand is versleuteld als onderdeel van de bestandsindeling, blijft dit bestand versleuteld op de Azure-bestands share. 

Azure File Sync werkt niet samen met NTFS Encrypted File System (NTFS EFS) of versleutelingsoplossingen van derden die zich boven het bestandssysteem, maar onder de gegevensstroom van het bestand. 

### <a name="encryption-in-transit"></a>Versleuteling 'in transit'

> [!NOTE]
> Azure File Sync-service wordt de ondersteuning voor TLS1.0 en 1.1 op 1 augustus 2020 verwijderd. Alle ondersteunde Azure File Sync agentversies gebruiken standaard al TLS1.2. Het gebruik van een eerdere versie van TLS kan optreden als TLS1.2 is uitgeschakeld op uw server of als er een proxy wordt gebruikt. Als u een proxy gebruikt, raden we u aan de proxyconfiguratie te controleren. Azure File Sync serviceregio's die na 1-5-2020 zijn toegevoegd, ondersteunen alleen TLS1.2 en ondersteuning voor TLS1.0 en 1.1 wordt vanaf 1 augustus 2020 verwijderd uit bestaande regio's.  Zie de gids voor probleemoplossing [voor meer informatie.](storage-sync-files-troubleshoot.md#tls-12-required-for-azure-file-sync)

Azure File Sync-agent communiceert met uw opslagsynchronisatieservice en Azure-bestands share met behulp van het Azure File Sync REST-protocol en het FileREST-protocol, die beide altijd HTTPS via poort 443 gebruiken. Azure File Sync verzendt geen niet-versleutelde aanvragen via HTTP. 

Azure-opslagaccounts bevatten een switch voor het vereisen van versleuteling in transit, die standaard is ingeschakeld. Zelfs als de schakel op het niveau van het opslagaccount is uitgeschakeld, wat betekent dat niet-versleutelde verbindingen met uw Azure-bestands shares mogelijk zijn, gebruikt Azure File Sync nog steeds alleen versleutelde kanalen om toegang te krijgen tot uw bestands share.

De primaire reden om versleuteling tijdens de overdracht uit te schakelen voor het opslagaccount is om een verouderde toepassing te ondersteunen die moet worden uitgevoerd op een ouder besturingssysteem, zoals Windows Server 2008 R2 of een oudere Linux-distributie, terwijl er rechtstreeks met een Azure-bestandsdeling wordt gesproken. Als de verouderde toepassing met de Windows Server-cache van de bestands share praat, heeft het in-/uit-schakelen van deze instelling geen effect. 

We raden u ten zeerste aan ervoor te zorgen dat versleuteling van gegevens die onderweg zijn is ingeschakeld.

Zie [Veilige overdracht vereisen in Azure Storage](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) voor meer informatie over versleuteling in transit.

### <a name="azure-file-share-encryption-at-rest"></a>Versleuteling van Azure-bestandsdeel in rust
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Opslaglagen
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Standaardbestands shares maximaal 100 TiB inschakelen
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Regionale beschikbaarheid
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Beschikbaarheid van Azure File Sync-regio

Zie Beschikbare producten per regio [voor regionale beschikbaarheid.](https://azure.microsoft.com/global-infrastructure/services/?products=storage)

In de volgende regio's moet u toegang tot Azure Storage aanvragen voordat u deze Azure File Sync gebruiken:

- Frankrijk - zuid
- Zuid-Afrika - west
- UAE - centraal

Volg het proces in dit document om toegang aan te vragen [voor deze regio's.](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="redundancy"></a>Redundantie
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> Geografisch redundante en geografisch zone-redundante opslag hebben de mogelijkheid om handmatig een failover-opslag uit te brengen naar de secundaire regio. We raden u aan dit niet buiten een noodscenario te doen wanneer u Azure File Sync vanwege de toegenomen kans op gegevensverlies. In het geval van een noodgeval waarbij u een handmatige failover van opslag wilt initiëren, moet u een ondersteuningscase bij Microsoft openen om Azure File Sync te laten synchroniseren met het secundaire eindpunt.

## <a name="migration"></a>Migratie
Als u een bestaande Windows-bestandsserver 2012R2 of nieuwer hebt, kan Azure File Sync direct worden geïnstalleerd, zonder dat u gegevens hoeft over te zetten naar een nieuwe server. Als u van plan bent om te migreren naar een nieuwe Windows-bestandsserver als onderdeel van de overstap op Azure File Sync, of als uw gegevens zich momenteel op NaS (Network Attached Storage) bevinden, zijn er verschillende mogelijke migratiemethoden voor het gebruik van Azure File Sync met deze gegevens. Welke migratiebenadering u moet kiezen, is afhankelijk van waar uw gegevens zich momenteel bevinden. 

Bekijk het artikel [Azure File Sync azure file share migration overview](storage-files-migration-overview.md) (Overzicht van migratie van gegevens en Azure-bestands delen). Hier vindt u gedetailleerde richtlijnen voor uw scenario.

## <a name="antivirus"></a>Antivirus
Omdat antivirusprogramma's werken door bestanden te scannen op bekende schadelijke code, kan een antivirusproduct leiden tot het inroepen van gelaagde bestanden, wat leidt tot hoge kosten voor het aantal uit te voeren gegevens. In versie 4.0 en hoger van de Azure File Sync-agent hebben gelaagde bestanden het beveiligde Windows-kenmerk FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS ingesteld. We raden u aan contact op te nemen met uw softwareleverancier om te leren hoe u de oplossing kunt configureren om het lezen van bestanden met deze kenmerkset over te slaan (veel doen dit automatisch). 

De in-house antivirusoplossingen van Microsoft, Windows Defender en System Center Endpoint Protection (SCEP), slaan beide automatisch leesbestanden over waarin dit kenmerk is ingesteld. We hebben deze getest en één klein probleem geïdentificeerd: wanneer u een server toevoegt aan een bestaande synchronisatiegroep, worden bestanden die kleiner zijn dan 800 bytes, teruggeroepen (gedownload) op de nieuwe server. Deze bestanden blijven op de nieuwe server en worden niet gelaagd omdat ze niet voldoen aan de vereiste voor de opslaglaaggrootte (>64 kB).

> [!Note]  
> Antivirusleveranciers kunnen de compatibiliteit tussen hun product en Azure File Sync controleren met behulp van [de Azure File Sync Antivirus Compatibility Test Suite.](https://www.microsoft.com/download/details.aspx?id=58322)Deze kan worden gedownload in het Microsoft Downloadcentrum.

## <a name="backup"></a>Backup 
Als opslag in cloudlagen is ingeschakeld, mogen oplossingen die rechtstreeks een back-up maken van het server-eindpunt of een VM waarop het server-eindpunt zich bevindt, niet worden gebruikt. Cloudopslaglagen zorgen ervoor dat er slechts een subset van uw gegevens wordt opgeslagen op het server-eindpunt, met de volledige gegevensset die zich in uw Azure-bestands share bewaard. Afhankelijk van de gebruikte back-upoplossing worden gelaagde bestanden overgeslagen en geen back-up gemaakt (omdat het FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS-kenmerk is ingesteld), of worden ze teruggeroepen naar de schijf, wat resulteert in hoge kosten voor het aantal uitkomende gegevens. U kunt het beste een back-upoplossing in de cloud gebruiken om rechtstreeks een back-up van de Azure-bestands share te maken. Zie Over back-ups van [Azure-bestands delen](../../backup/azure-file-share-backup-overview.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) of neem contact op met uw back-upprovider om te zien of deze ondersteuning biedt voor het maken van back-ups van Azure-bestands shares.

Als u liever een on-premises back-upoplossing gebruikt, moeten back-ups worden uitgevoerd op een server in de synchronisatiegroep die opslag in cloudlagen heeft uitgeschakeld. Wanneer u een herstel wilt uitvoeren, gebruikt u de herstelopties op volume- of bestandsniveau. Bestanden die zijn hersteld met de hersteloptie op bestandsniveau worden gesynchroniseerd met alle eindpunten in de synchronisatiegroep en bestaande bestanden worden vervangen door de versie die is hersteld vanuit een back-up.  Herstel op volumeniveau vervangt geen nieuwere bestandsversies in de Azure-bestands share of andere server-eindpunten.

> [!WARNING]
> Als u Robocopy /B moet gebruiken met een Azure File Sync-agent die wordt uitgevoerd op de bron- of doelserver, moet u een upgrade uitvoeren naar Azure File Sync agentversie v12.0 of hoger. Het gebruik van Robocopy /B met agentversies lager dan v12.0 leidt tot beschadiging van gelaagde bestanden tijdens het kopiëren.

> [!Note]  
> Bare-metal herstellen (BMR) kan onverwachte resultaten veroorzaken en wordt momenteel niet ondersteund.

> [!Note]  
> Met versie 9 van de Azure File Sync agent worden VSS-momentopnamen (inclusief het tabblad Vorige versies) nu ondersteund op volumes waarvoor opslag in cloudlagen is ingeschakeld. U moet echter compatibiliteit met eerdere versies inschakelen via PowerShell. [Meer informatie](storage-sync-files-deployment-guide.md#self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service).

## <a name="data-classification"></a>Gegevensclassificatie
Als u software voor gegevensclassificatie hebt geïnstalleerd, kan het inschakelen van opslag in cloudlagen om twee redenen leiden tot hogere kosten:

1. Als opslag in cloudlagen is ingeschakeld, worden uw meest populaire bestanden lokaal in de cache opgeslagen en worden de coolste bestanden in lagen opgeslagen in de Azure-bestands share in de cloud. Als uw gegevensclassificatie regelmatig alle bestanden in de bestands share scant, moeten de bestanden die in een cloudlaag zijn opgeslagen, worden ingeroepen wanneer ze worden gescand. 

2. Als de software voor gegevensclassificatie gebruikmaakt van de metagegevens in de gegevensstroom van een bestand, moet het bestand volledig worden ingeroepen om de software de classificatie te laten zien. 

Deze toename van zowel het aantal terugroepen als de hoeveelheid gegevens die wordt ingeroepen, kan de kosten verhogen.

## <a name="azure-file-sync-agent-update-policy"></a>Updatebeleid Azure File Sync-agent
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Volgende stappen
* [Overweeg firewall- en proxyinstellingen](storage-sync-files-firewall-and-proxy.md)
* [Een Azure Files-implementatie plannen](storage-files-planning.md)
* [Azure Files implementeren](./storage-how-to-create-file-share.md)
* [Azure Files SYNC implementeren](storage-sync-files-deployment-guide.md)
* [Azure File Sync bewaken](storage-sync-files-monitoring.md)
