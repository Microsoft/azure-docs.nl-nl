---
title: Azure Storage-typen voor SAP-workload
description: Azure Storage-typen plannen voor SAP-workloads
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 04/08/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0605c8d6cb3f650a1955688d95ece62fb56d06bc
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256035"
---
# <a name="azure-storage-types-for-sap-workload"></a>Azure Storage-typen voor SAP-workload
Azure heeft talloze opslag typen die in de mogelijkheden, door Voer, latentie en prijzen aanzienlijk verschillen. Sommige opslag typen zijn niet of zijn beperkt bruikbaar voor SAP-scenario's. Dat zijn verschillende Azure Storage-typen goed geschikt of geoptimaliseerd voor specifieke SAP-werkbelasting scenario's. Met name voor SAP HANA hebben sommige Azure Storage-typen gecertificeerd voor het gebruik met SAP HANA. In dit document gaan we de verschillende soorten opslag door lopen en de mogelijkheden en bruikbaarheid van SAP-workloads en SAP-onderdelen beschrijven.

Opmerkingen over de eenheden die in dit artikel worden gebruikt. De leveranciers van de open bare Cloud zijn verplaatst om GiB ([gibibyte](https://en.wikipedia.org/wiki/Gibibyte)) of TIB ([tebibyte](https://en.wikipedia.org/wiki/Tebibyte) als maat eenheden te gebruiken in plaats van Gigabyte of terabyte. Daarom gebruiken alle Azure-documentatie en Mentoraat deze eenheden.  In het hele document verwijzen we alleen naar deze grootte-eenheden van MiB-, GiB-en TiB-eenheden. U moet mogelijk met MB, GB en TB plannen. Let dus op enkele kleine verschillen in de berekeningen als u de grootte van een 400-MiB per seconde moet aanpassen, in plaats van een 250-door Voer per seconde.

## <a name="microsoft-azure-storage-resiliency"></a>Microsoft Azure Storage tolerantie

Microsoft Azure opslag van Standard-HDD, Standard-SSD, Azure Premium-opslag en ultra Disk blijven de basis-VHD (met OS) en gegevens schijven of Vhd's met gekoppelde virtuele machines in drie kopieën op drie verschillende opslag knooppunten. Het uitvoeren van een failover naar een andere replica en het seeden van een nieuwe replica in het geval van een storing in een opslag knooppunt is transparant. Als gevolg van deze redundantie is het **niet** nodig om een ander type opslag redundantie laag te gebruiken op meerdere Azure-schijven. Dit feit wordt lokale redundante opslag (LRS) genoemd. LRS is standaard voor deze opslag typen in Azure. [Azure NetApp files](https://azure.microsoft.com/services/netapp/) biedt voldoende redundantie voor het uitvoeren van dezelfde sla's als andere systeem eigen Azure-opslag.

Er zijn diverse redundante methoden, die allemaal worden beschreven in het artikel [Azure storage replicatie](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) die van toepassing zijn op enkele van de verschillende opslag typen die Azure te bieden heeft. 

Houd er ook wel van uit dat verschillende typen Azure-opslag van invloed zijn op de beschik baarheid van één VM, zoals uitgebracht in [Sla voor virtual machines](https://azure.microsoft.com/support/legal/sla/virtual-machines).

### <a name="azure-managed-disks"></a>Azure Managed disks

Managed disks is een resource type in Azure Resource Manager dat kan worden gebruikt in plaats van Vhd's die zijn opgeslagen in Azure Storage accounts. Managed Disks automatisch uitgelijnd met de [Availability set] [virtual-machines-Manage-Availability] van de virtuele machine waaraan ze zijn gekoppeld, waardoor de beschik baarheid van uw virtuele machine en de services die op de virtuele machine worden uitgevoerd, worden verhoogd. Lees het [artikel overzicht](../../managed-disks-overview.md)voor meer informatie.

In dit voor beeld wordt gebruikgemaakt van de flexibiliteit van Managed disks:

- U implementeert uw SAP-virtuele machines in een Azure-beschikbaarheidsset met uw twee DBMS-Vm's 
- Terwijl Azure de virtuele machines implementeert, wordt de schijf met de installatie kopie van het besturings systeem in een ander opslag cluster geplaatst. Dit voor komt dat beide Vm's worden beïnvloed door een probleem met één Azure Storage-cluster
- Wanneer u nieuwe beheerde schijven maakt die u aan deze Vm's toewijst om de gegevens en logboek bestanden van uw Data Base op te slaan, worden deze nieuwe schijven voor de twee virtuele machines ook geïmplementeerd in afzonderlijke opslag clusters, zodat geen van de schijven van de eerste virtuele machine opslag clusters deelt met de schijven van de tweede VM

Implementatie zonder Managed disks in door de klant gedefinieerde opslag accounts is een wille keurige schijf toewijzing en heeft geen inzicht in het feit dat Vm's worden geïmplementeerd in een AvSet voor tolerantie doeleinden.

> [!NOTE]
> Daarom moeten nieuwe implementaties van virtuele machines die gebruikmaken van Azure Block Storage voor hun schijven (alle Azure-opslag met uitzonde ring van Azure NetApp Files) gebruikmaken van Azure Managed disks voor de basis-VHD/OS-schijven, gegevens schijven die SAP-database bestanden bevatten. Onafhankelijk van het feit of u de Vm's implementeert via beschikbaarheidsset, op Beschikbaarheidszones of onafhankelijk van de sets en zones. Schijven die worden gebruikt voor het opslaan van back-ups, hoeven geen beheerde schijven te zijn.

> [!NOTE]
> Azure Managed disks bieden alleen lokale redundantie (LRS). 


## <a name="storage-scenarios-with-sap-workloads"></a>Opslag scenario's met SAP-workloads
Er is persistente opslag nodig in de SAP-werk belasting in verschillende onderdelen van de stack die u in azure implementeert. In deze scenario's wordt Mini maal het volgende weer geven:

- Permanente de basis-VHD van uw virtuele machine met het besturings systeem en andere software die u op die schijf installeert. Deze schijf/VHD is de hoofdmap van uw VM. Eventuele wijzigingen die u aanbrengt, moeten behouden blijven. De volgende keer dat u de virtuele machine stopt en opnieuw opstart, worden alle wijzigingen die zijn aangebracht voordat ze nog bestaan. Met name in gevallen waarin de virtuele machine door Azure wordt geïmplementeerd op een andere host dan die oorspronkelijk werd uitgevoerd
- Permanente gegevens schijven. Deze schijven zijn de Vhd's die u koppelt om toepassings gegevens op te slaan in. Deze toepassings gegevens kunnen gegevens zijn en bestanden van een Data Base, back-upbestanden of software-installaties registreren/opnieuw uitvoeren. Betekent elke schijf buiten de basis-VHD die het besturings systeem bevat
- Bestands shares of gedeelde schijven die uw globale transport Directory bevatten voor NetWeaver of S/4HANA. De inhoud van deze shares wordt geconsumeerd door software die wordt uitgevoerd op meerdere Vm's of wordt gebruikt voor het bouwen van failover cluster scenario's met hoge Beschik baarheid
- De/sapmnt-map of algemene bestands shares voor EDI-processen of dergelijke. De inhoud van deze shares wordt geconsumeerd door software die wordt uitgevoerd op meerdere Vm's of wordt gebruikt voor het bouwen van failover cluster scenario's met hoge Beschik baarheid

In de volgende secties worden de verschillende typen Azure-opslag en de gebruiks vriendelijkheid voor SAP-werk belasting besproken die van toepassing zijn op de vier bovenstaande scenario's. Een algemene categorisatie van de manier waarop de verschillende typen Azure-opslag moeten worden gebruikt, wordt beschreven in het artikel [welke schijf typen beschikbaar zijn in azure?](../../disks-types.md). De aanbevelingen voor het gebruik van de verschillende typen Azure-opslag voor SAP-workloads zijn niet aanzienlijk anders.

Lees voor ondersteunings beperkingen voor Azure Storage-typen voor SAP NetWeaver/Application Layer of S/4HANA de [SAP-ondersteunings opmerking 2015553](https://launchpad.support.sap.com/#/notes/2015553) voor SAP Hana gecertificeerde en ondersteunde Azure-opslag typen raadpleegt u het artikel [SAP Hana opslag configuraties voor virtuele Azure-machines](./hana-vm-operations-storage.md).

In de secties waarin de verschillende typen Azure-opslag worden beschreven, krijgt u meer achtergrond over de beperkingen en mogelijkheden die gebruikmaken van de door SAP ondersteunde opslag. 

## <a name="storage-recommendations-for-sap-storage-scenarios"></a>Opslag aanbevelingen voor SAP-opslag scenario's
Voordat u naar de details gaat, worden de samen vatting en aanbevelingen aan het begin van het document weer gegeven. De Details voor de specifieke typen Azure-opslag volgen deze sectie van het document. Samen vatting van de opslag aanbevelingen voor de SAP-opslag scenario's in een tabel ziet er als volgt uit:

| Gebruiks scenario | Standard - HDD | Standard - SSD | Premium Storage | Ultraschijven | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| Besturingssysteemschijf | niet geschikt |  beperkt geschikt (niet-productie) | aanbevelingen | niet mogelijk | niet mogelijk |
| Globale transport Directory | niet ondersteund | niet ondersteund | aanbevelingen | aanbevelingen | aanbevelingen |
| /sapmnt | niet geschikt | beperkt geschikt (niet-productie) | aanbevelingen | aanbevelingen | aanbevelingen |
| DBMS-gegevens volume SAP HANA M/Mv2 VM-families | niet ondersteund | niet ondersteund | aanbevelingen | aanbevelingen | Aanbevolen<sup>2</sup> |
| DBMS-logboek volume SAP HANA M/Mv2 VM-families | niet ondersteund | niet ondersteund | Aanbevolen<sup>1</sup> | aanbevelingen | Aanbevolen<sup>2</sup> | 
| DBMS-gegevens volume SAP HANA Esv3/Edsv4 VM-families | niet ondersteund | niet ondersteund | aanbevelingen | aanbevelingen | Aanbevolen<sup>2</sup> |
| DBMS-logboek volume SAP HANA Esv3/Edsv4 VM-families | niet ondersteund | niet ondersteund | niet ondersteund | aanbevelingen | Aanbevolen<sup>2</sup> | 
| DBMS-gegevens volume niet-HANA | niet ondersteund | beperkt geschikt (niet-productie) | aanbevelingen | aanbevelingen | niet ondersteund |
| DBMS-logboek volume niet-HANA M/Mv2 VM-families | niet ondersteund | beperkt geschikt (niet-productie) | Aanbevolen<sup>1</sup> | aanbevelingen | niet ondersteund |
| DBMS-logboek volume niet-HANA niet-M/Mv2 VM-families | niet ondersteund | beperkt geschikt (niet-productie) | geschikt voor een gemiddelde werk belasting | aanbevelingen | niet ondersteund |


<sup>1</sup> met het gebruik van [Azure write Accelerator](../../how-to-enable-write-accelerator.md) voor Mv2 VM-families voor logboek-en opnieuw uitvoeren op logboek volumes <sup>2</sup> met ANF zijn/Hana/data en/Hana/log vereist voor ANF 

Eigenschappen die u kunt verwachten van de lijst met verschillende soorten opslag, zoals:

| Gebruiks scenario | Standard - HDD | Standard - SSD | Premium Storage | Ultraschijven | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| SLA voor door Voer/IOPS | nee | nee | ja | ja | ja |
| Lees latentie | hoog | gemiddeld tot hoog | gebrek | submilliseconde | submilliseconde |
| Latentie schrijf bewerkingen | hoog | gemiddeld tot hoog  | laag (submilliseconden<sup>1</sup>) | submilliseconde | submilliseconde |
| HANA ondersteund | nee | nee | Ja<sup>1</sup> | ja | ja |
| Schijf momentopnamen mogelijk | ja | ja | ja | nee | ja |
| Toewijzing van schijven op verschillende opslag clusters bij gebruik van beschikbaarheids sets | via Managed disks | via Managed disks | via Managed disks | schijf type wordt niet ondersteund voor virtuele machines die zijn geïmplementeerd via beschikbaarheids sets | geen<sup>3</sup> |
| Uitgelijnd met Beschikbaarheidszones | ja | ja | ja | ja | betrokkenheid van micro soft nodig |
| Zonegebonden-redundantie | niet voor beheerde schijven | niet voor beheerde schijven | niet voor beheerde schijven | nee | nee |
| Geo-redundantie | niet voor beheerde schijven | niet voor beheerde schijven | nee | nee | nee |


<sup>1</sup> met gebruik van [Azure write Accelerator](../../how-to-enable-write-accelerator.md) voor Mv2 VM-families voor logboeken/opnieuw uitvoeren van logboek volumes

<sup>2</sup> kosten zijn afhankelijk van ingerichte IOPS en door Voer

<sup>3</sup> het maken van verschillende ANF-capaciteits groepen biedt geen garantie voor de implementatie van capaciteits groepen op verschillende opslag eenheden


> [!IMPORTANT]
> Als u een I/O-latentie van minder dan 1 milliseconden wilt gebruiken met behulp van Azure NetApp Files (ANF), moet u samen werken met micro soft om de juiste plaatsing tussen uw Vm's en de NFS-shares op basis van ANF te regelen. Tot nu toe is er geen mechanisme aanwezig dat een automatische nabijheid vormt tussen een geïmplementeerde VM en de NFS-volumes die worden gehost op ANF. Gezien de verschillende instellingen van de verschillende Azure-regio's, kan de I/O-latentie na 1 milliseconde worden gepusht als de virtuele machine en de NFS-share niet in de buurt zijn toegewezen.


> [!IMPORTANT]
> Geen van de momenteel aangeboden Azure Block Storage-based Managed disks of Azure NetApp Files bieden zonegebonden of geografische redundantie. Daarom moet u ervoor zorgen dat uw architectuur met hoge Beschik baarheid en herstel na nood gevallen niet afhankelijk zijn van welk type Azure native storage-replicatie voor deze beheerde schijven, NFS-of SMB-shares.


## <a name="azure-premium-storage"></a>Azure Premium-opslag
Azure Premium SSD-opslag is geïntroduceerd met het doel om het volgende te bieden:

* Lage I/O-latentie
* Sla's voor IOPS en door Voer
* Minder variabiliteit in I/O-latentie

Dit type opslag is gericht op DBMS-workloads, opslag verkeer waarvoor een lage latentie vertraging van één cijfer en de kosten voor de door Voer in het geval van Azure Premium Storage niet het daad werkelijke gegevens volume is dat op dergelijke schijven is opgeslagen, maar de grootte categorie van een dergelijke schijf, onafhankelijk van de hoeveelheid gegevens die op de schijf is opgeslagen. U kunt ook schijven maken op Premium-opslag die niet rechtstreeks worden toegewezen in de grootte categorieën die in het artikel [Premium-SSD](../../disks-types.md#premium-ssd)worden weer gegeven. De conclusies in dit artikel zijn:

- De opslag is ingedeeld in bereiken. Bijvoorbeeld, een schijf in het bereik 513 GiB tot 1024 GiB capaciteit delen dezelfde mogelijkheden en dezelfde maandelijkse kosten
- De IOPS per GiB volgen geen lineair over de grootte categorieën. Kleinere schijven onder 32 GiB hebben hogere IOPS-tarieven per GiB. Voor schijven van meer dan 32 GiB tot 1024 GiB ligt het aantal IOPS per GiB tussen 4-5 IOPS per GiB. Voor grotere schijven tot 32.767 GiB is de IOPS-frequentie per GiB lager dan 1
- De I/O-door Voer voor deze opslag is niet lineair met de grootte van de schijf categorie. Voor kleinere schijven, zoals de categorie tussen 65 GiB en 128 GiB capaciteit, is de door Voer ongeveer 780KB/GiB. Overwegende dat voor de extreem grote schijven, zoals een 32.767 GiB-schijf, de door Voer rond 28KB/GiB
- De Sla's voor IOPS en door Voer kunnen niet worden gewijzigd zonder de capaciteit van de schijf te wijzigen


De functie matrix voor SAP-werk belasting ziet er als volgt uit:

| Mogelijkheid| Opmerking| Opmerkingen/koppelingen | 
| --- | --- | --- | 
| VHD-basis versie van het besturings systeem | oplos | alle systemen |
| Gegevensschijf | oplos | alle systemen- [speciaal voor SAP Hana](../../how-to-enable-write-accelerator.md) |
| SAP Global Trans Port-map | JA | [Ondersteund](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP-sapmnt | oplos | alle systemen |
| Back-upopslag | oplos | voor opslag van back-ups op korte termijn |
| Shares/gedeelde schijf | niet beschikbaar | Azure Premium-bestanden of derden vereist |
| Flexibiliteit | LRS | Geen GRS of ZRS beschikbaar voor schijven |
| Latentie | laag naar normaal | - |
| SLA VOOR IOPS | JA | - |
| IOPS lineaire tot capaciteit | halve lineaire tussen haakjes  | [Prijzen beheerde schijven](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Maximum aantal IOPS per schijf | 20.000 [afhankelijk van de schijf grootte](https://azure.microsoft.com/pricing/details/managed-disks/) | VM- [limieten](../../sizes.md) ook overwegen |
| SLA voor door Voer | JA | - |
| Door Voer lineair naar capaciteit | halve lineaire tussen haakjes | [Prijzen beheerde schijven](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA-gecertificeerd | JA | [speciaal voor SAP HANA](../../how-to-enable-write-accelerator.md) |
| Schijf momentopnamen mogelijk | JA | - |
| Azure Backup mogelijke VM-moment opnamen | JA | met uitzonde ring van [Write Accelerator](../../how-to-enable-write-accelerator.md) schijven in de cache  |
| Kosten | DRAGER | - |

Azure Premium Storage voldoet niet aan de SAP HANA opslag latentie-Kpi's met de algemene typen caching die worden aangeboden met Azure Premium Storage. Als u wilt voldoen aan de opslag latentie-Kpi's voor het schrijven van SAP HANA logboeken, moet u Azure Write Accelerator cache gebruiken, zoals beschreven in het artikel [enable write Accelerator](../../how-to-enable-write-accelerator.md). Azure Write Accelerator voor delen van alle andere DBMS-systemen voor het schrijven van transactie logboeken en het opnieuw registreren van Logboeken. Daarom is het raadzaam om het te gebruiken in alle SAP-DBMS-implementaties. Voor SAP HANA is het gebruik van Azure Write Accelerator in combi natie met Azure Premium Storage verplicht.



**Samen vatting:** Azure Premium Storage is een van de Azure-opslag typen die worden aanbevolen voor SAP-workloads. Deze aanbeveling is van toepassing op niet-productie en productie systemen. Azure Premium Storage is geschikt voor het verwerken van data base-workloads. Het gebruik van Azure Write Accelerator wordt de schrijf latentie voor Azure Premium-schijven aanzienlijk verbeterd. Voor DBMS-systemen met een hoge IOPS-en doorvoer snelheid moet u echter gebruikmaken van de opslag capaciteit van de inrichting of u moet functies zoals Windows-opslag ruimten of logische volume managers in Linux gebruiken om stripesets te maken die u de gewenste capaciteit aan de ene kant geven, maar ook de benodigde IOPS of door Voer met de beste kosten besparing.


### <a name="azure-burst-functionality-for-premium-storage"></a>Azure burst-functionaliteit voor Premium-opslag
Voor Azure Premium-opslag schijven die kleiner zijn dan of gelijk zijn aan 512 GiB in capaciteit, wordt burst-functionaliteit aangeboden. De exacte manier waarop schijf bursting werkt, wordt beschreven in het artikel [schijf bursting](../../disk-bursting.md). Wanneer u het artikel leest, begrijpt u het concept van het aantal beschik bare IOPS en door Voer in de tijden dat de I/O-werk belasting lager is dan de nominale IOPS en de door Voer van de schijven (Zie [prijzen voor beheerde schijven](https://azure.microsoft.com/pricing/details/managed-disks/)voor meer informatie over de nominale door Voer). U gaat de Delta van IOPS en door Voer samen voegen tussen het huidige gebruik en de nominale waarden van de schijf. De bursts zijn beperkt tot Maxi maal 30 minuten.

De ideale gevallen waarin deze burst-functionaliteit kan worden gepland in, zijn waarschijnlijk de volumes of schijven die gegevens bestanden voor de verschillende DBMS bevatten. De I/O-werk belasting verwacht op die volumes, vooral met kleine tot middel grote systemen, moet er als volgt uitzien:

- Laag tot gemiddeld Lees werk, omdat gegevens in de cache in het geheugen worden opgeslagen, of als in het geval van HANA volledig in het geheugen moet worden bewaard
- Triggers van schrijven die zijn geactiveerd door database controlepunten of opslag punten die regel matig worden uitgegeven
- Backup-werk belasting die in een doorlopende stroom leest in gevallen waarbij back-ups niet worden uitgevoerd via opslag momentopnamen
- Voor SAP HANA worden de gegevens in het geheugen geladen nadat een instantie opnieuw is opgestart

Met name op kleinere DBMS-systemen waarbij uw werk belasting enkele honderden trans acties per seconde verwerkt, kan een dergelijke burst-functionaliteit ook zinvol zijn voor de schijven of volumes die de trans actie opslaan of het logboek opnieuw uitvoeren. Verwachte werk belasting voor een dergelijke schijf of volumes ziet er als volgt uit:

- Er wordt regel matig naar de schijf geschreven die afhankelijk is van de werk belasting en de aard van de werk belasting, omdat elke door voering uitgegeven door de toepassing waarschijnlijk een I/O-bewerking veroorzaakt
- Hogere werk belasting bij de door Voer voor gevallen van operationele taken, zoals het maken of opnieuw samen stellen van indexen
- Bursts lezen bij het uitvoeren van een transactie logboek of het opnieuw uitvoeren van back-ups van Logboeken


## <a name="azure-ultra-disk"></a>Azure Ultra Disk
Azure-ultraschijven leveren een hoge doorvoer, hoge IOPS en een consistente schijfopslag met lage latentie voor Azure IaaS-VM's. Enkele extra voor delen van ultra schijven zijn de mogelijkheid om de IOPS en de door Voer van de schijf dynamisch te wijzigen, samen met uw workloads, zonder dat u uw virtuele machines (VM) opnieuw hoeft op te starten. Ultra disks zijn geschikt voor gegevensintensieve workloads, zoals SAP DBMS-workloads. Ultra disks kan alleen worden gebruikt als gegevens schijven en kan niet worden gebruikt als een VHD-basis schijf waarop het besturings systeem wordt opgeslagen. Het gebruik van Azure Premium Storage wordt aangeraden als VHD-schijf op basis van.

Wanneer u een ultra schijf maakt, hebt u drie dimensies die u kunt definiëren:

- De capaciteit van de schijf. Bereiken zijn van 4 GiB tot 65.536 GiB
- Ingerichte IOPS voor de schijf. Verschillende maximum waarden zijn van toepassing op de capaciteit van de schijf. Lees het artikel [Ultra Disk](../../disks-types.md#ultra-disk) voor meer informatie
- Ingerichte band breedte voor opslag. De maximale band breedte is afhankelijk van de capaciteit van de schijf. Lees het artikel [Ultra Disk](../../disks-types.md#ultra-disk) voor meer informatie

De kosten van één schijf worden bepaald door de drie dimensies die u afzonderlijk kunt definiëren voor de specifieke schijven. 


De functie matrix voor SAP-werk belasting ziet er als volgt uit:

| Mogelijkheid| Opmerking| Opmerkingen/koppelingen | 
| --- | --- | --- | 
| VHD-basis versie van het besturings systeem | werkt niet | - |
| Gegevensschijf | oplos | alle systemen  |
| SAP Global Trans Port-map | JA | [Ondersteund](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP-sapmnt | oplos | alle systemen |
| Back-upopslag | oplos | voor opslag van back-ups op korte termijn |
| Shares/gedeelde schijf | niet beschikbaar | Externe partij vereist |
| Flexibiliteit | LRS | Geen GRS of ZRS beschikbaar voor schijven |
| Latentie | zeer laag | - |
| SLA VOOR IOPS | JA | - |
| IOPS lineaire tot capaciteit | halve lineaire tussen haakjes  | [Prijzen beheerde schijven](https://azure.microsoft.com/pricing/details/managed-disks/) |
| Maximum aantal IOPS per schijf | 1.200 tot 160.000 | afhankelijk van de schijf capaciteit |
| SLA voor door Voer | JA | - |
| Door Voer lineair naar capaciteit | halve lineaire tussen haakjes | [Prijzen beheerde schijven](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA-gecertificeerd | JA | - |
| Schijf momentopnamen mogelijk | NO | - |
| Azure Backup mogelijke VM-moment opnamen | NO | - |
| Kosten | Hoger dan Premium-opslag | - |



**Samen vatting:** Azure Ultra disks is een geschikte opslag met lage latentie voor allerlei soorten SAP-workloads. Tot nu toe kan Ultra Disk alleen worden gebruikt in combi Naties met virtuele machines die zijn geïmplementeerd via Beschikbaarheidszones (zonegebonden-implementatie). Ultra Disk ondersteunt op dit moment geen moment opnamen van de opslag. In tegens telling tot alle andere opslag kan Ultra disk niet worden gebruikt voor de schijf met de basis-VHD. Ultra disk is ideaal voor gevallen waarin de I/O-werk belasting een hoop schommelt en u de geïmplementeerde opslag doorvoer of IOPS wilt aanpassen aan de werkbelasting patronen voor opslag in plaats van de grootte voor het maximale gebruik van band breedte en IOPS.


## <a name="azure-netapp-files-anf"></a>Azure NetApp-bestanden (ANF)
[Azure NetApp files](https://azure.microsoft.com/services/netapp/) is het resultaat van een samen werking tussen micro soft en NetApp met het doel om hoge prestaties van Azure native NFS en SMB-shares te bieden. De nadruk ligt op het bieden van een hoge band breedte en een lage latentie opslag die DBMS-implementatie scenario's mogelijk maakt en in de loop van de tijd standaard operationele functionaliteit van de NetApp-opslag mogelijk maken via Azure. NFS/SMB-shares worden aangeboden in drie verschillende service niveaus die differentiëren bij opslag doorvoer en-prijs. De service niveaus worden beschreven in het artikel [service niveaus voor Azure NetApp files](../../../azure-netapp-files/azure-netapp-files-service-levels.md). Voor de verschillende typen SAP-werk belasting worden de volgende service niveaus nadrukkelijk aanbevolen:

- SAP DBMS-workload: prestaties, idea-zo ultra
- SAPMNT-share: prestaties, idea-ideaal Ultra
- Globale transport Directory: prestaties, idea-zo ultra

> [!NOTE]
> De minimale inrichtings grootte is een 4-TiB eenheid met de naam capaciteits pool. Vervolgens maakt u een volume uit deze capaciteits groep. Het kleinste volume dat u kunt maken, is 100 GiB. U kunt een capaciteits groep in TiB-stappen uitbreiden. Voor prijzen raadpleegt u het artikel [Azure NetApp files prijzen](https://azure.microsoft.com/pricing/details/netapp/)

ANF-opslag wordt momenteel ondersteund voor verschillende SAP-werkbelasting scenario's:

- SMB-of NFS-shares opgeven voor de globale transport Directory van SAP
- De share sapmnt in scenario's met hoge Beschik baarheid, zoals beschreven in:
    - [Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's in Windows met Azure NetApp Files (SMB) voor SAP-toepassingen](./high-availability-guide-windows-netapp-files-smb.md)
    - [Hoge Beschik baarheid voor SAP NetWeaver op Azure Vm's op SUSE Linux Enterprise Server met Azure NetApp Files voor SAP-toepassingen](./high-availability-guide-suse-netapp-files.md)
    - [Azure Virtual Machines hoge Beschik baarheid voor SAP NetWeaver op Red Hat Enterprise Linux met Azure NetApp Files voor SAP-toepassingen](./high-availability-guide-rhel-netapp-files.md)
- SAP HANA implementaties die gebruikmaken van NFS v 4.1-shares voor/Hana/data-en/Hana/log-volumes en/of NFS v 4.1-of NFS v3-volumes voor/Hana/Shared-volumes zoals beschreven in het artikel [SAP Hana opslag configuraties voor virtuele Azure-machines](./hana-vm-operations-storage.md)
- Oracle-implementaties in Oracle Linux-gast besturingssysteem met [dNFS] (( https://docs.oracle.com/en/database/oracle/oracle-database/19/ntdbi/creating-an-oracle-database-on-direct-nfs.html#GUID-2A0CCBAB-9335-45A8-B8E3-7E8C4B889DEA) dOracle-gegevens en logboek volumes opnieuw uitvoeren. Meer informatie vindt u in het artikel [Azure virtual machines Oracle DBMS-implementatie voor SAP-workload](./dbms_guide_oracle.md)

> [!NOTE]
> Er wordt geen andere workload DBMS ondersteund voor op Azure NetApp Files gebaseerde NFS-of SMB-shares. Updates en wijzigingen worden gegeven als dit wordt gewijzigd.

Net als bij Azure Premium Storage kan een vaste of lineaire doorvoer grootte per GB een probleem zijn wanneer u verplicht bent om te voldoen aan de minimum aantallen in de door voer. Als dit het geval is voor SAP HANA. Met ANF kan dit probleem meer worden uitgesp roken dan met Azure Premium-schijf. In het geval van een Azure Premium-schijf kunt u verschillende kleinere schijven maken met een relatief hoge door Voer per GiB en deze over elkaar halen om rendabel te zijn en een hogere door voer te hebben tegen lagere capaciteit. Dit type striping werkt niet voor NFS-of SMB-shares die worden gehost op ANF. Deze beperking resulteerde in de implementatie van overcapaciteit als:

- Als u bijvoorbeeld een door Voer van 250 MiB per seconde wilt uitvoeren op een NFS-volume dat wordt gehost op ANF, moet u de TiB-capaciteit van 1,95 implementeren van het ultra service-niveau. 
- Als u 400 MiB per seconde wilt, moet u de TiB-capaciteit van 3,125 implementeren. Het is echter wel mogelijk dat u over capaciteit nodig hebt om de door voer te verkrijgen die u nodig hebt voor het volume. Deze overinrichting van capaciteit heeft gevolgen voor de prijzen van kleinere HANA-instanties. 
- In de ruimte voor het gebruik van NFS boven op ANF voor de SAP/sapmnt-map gaat u meestal naar de minimale capaciteit van 100 GiB tot 150 GiB die wordt afgedwongen door Azure NetApp Files. De ervaring van de klant bleek echter dat de gerelateerde door Voer van 12,8 MiB/SEC (met ultra service niveau) mogelijk niet voldoende is en een negatieve invloed kan hebben op de stabiliteit van het SAP-systeem. In dergelijke gevallen kunnen klanten problemen voor komen door het volume van het/sapmnt-volume te verg Roten, zodat dat volume meer door Voer wordt verstrekt.

De functie matrix voor SAP-werk belasting ziet er als volgt uit:

| Mogelijkheid| Opmerking| Opmerkingen/koppelingen | 
| --- | --- | --- | 
| VHD-basis versie van het besturings systeem | werkt niet | - |
| Gegevensschijf | oplos | Alleen SAP HANA  |
| SAP Global Trans Port-map | JA | Zowel SMB als NFS |
| SAP-sapmnt | oplos | alle systemen SMB (alleen Windows) of NFS (alleen Linux) |
| Back-upopslag | oplos | - |
| Shares/gedeelde schijf | JA | SMB 3,0, NFS v3 en NFS v 4.1 |
| Flexibiliteit | LRS | Geen GRS of ZRS beschikbaar voor schijven |
| Latentie | zeer laag | - |
| SLA VOOR IOPS | JA | - |
| IOPS lineaire tot capaciteit | strikt lineair  | Afhankelijk van [service niveau](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| SLA voor door Voer | JA | - |
| Door Voer lineair naar capaciteit | halve lineaire tussen haakjes | Afhankelijk van [service niveau](../../../azure-netapp-files/azure-netapp-files-service-levels.md) |
| HANA-gecertificeerd | JA | - |
| Schijf momentopnamen mogelijk | JA | - |
| Azure Backup mogelijke VM-moment opnamen | NO | - |
| Kosten | Hoger dan Premium-opslag | - |


Aanvullende ingebouwde functionaliteit van ANF-opslag:

- Mogelijkheid om moment opnamen van het volume uit te voeren
- Klonen van ANF-volumes van moment opnamen
- Volumes herstellen vanaf moment opnamen (module-herstellen)

**Samen vatting**: Azure NetApp files is een Hana-gecertificeerde opslag met lage LATENTIE waarmee NFS-en SMB-volumes of-shares kunnen worden geïmplementeerd. De opslag wordt geleverd met drie verschillende service niveaus die een verschillende door Voer en IOPS bieden op lineaire wijze per GiB capaciteit van het volume. De ANF-opslag wordt ingeschakeld voor het implementeren van SAP HANA scale-out scenario's met een stand-by-knoop punt. De opslag is geschikt voor het leveren van bestands shares die nodig zijn voor de/sapmnt of de globale transport Directory van SAP. ANF Storage wordt geleverd met functionaliteits beschikbaarheid die beschikbaar is als systeem eigen NetApp-functionaliteit.  



## <a name="azure-standard-ssd-storage"></a>Azure Standard SSD-opslag
Vergeleken met de opslag capaciteit van Azure Standard-schijven biedt Azure Standard SSD-opslag betere Beschik baarheid, consistentie, betrouw baarheid en latentie. Het is geoptimaliseerd voor workloads die consistente prestaties nodig hebben op lagere IOPS-niveaus. Deze opslag is de minimale opslag ruimte die wordt gebruikt voor niet-productie SAP-systemen met een lage IOPS-en doorvoer vereisten. De functie matrix voor SAP-werk belasting ziet er als volgt uit:

| Mogelijkheid| Opmerking| Opmerkingen/koppelingen | 
| --- | --- | --- | 
| VHD-basis versie van het besturings systeem | alleen geschikt | niet-productie systemen |
| Gegevensschijf | alleen geschikt | sommige niet-productie systemen met lage IOPS-en latentie vereisten |
| SAP Global Trans Port-map | NO | [Niet ondersteund](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP-sapmnt | alleen geschikt | niet-productie systemen |
| Back-upopslag | oplos | - |
| Shares/gedeelde schijf | niet beschikbaar | Externe partij vereist |
| Flexibiliteit | LRS, GRS | Geen ZRS beschikbaar voor schijven |
| Latentie | hoog | te hoog voor SAP Global Trans Port Directory, of productie systemen |
| SLA VOOR IOPS | NO | - |
| Maximum aantal IOPS per schijf | 500 | Onafhankelijk van de grootte van de schijf |
| SLA voor door Voer | NO | - |
| HANA-gecertificeerd | NO | - |
| Schijf momentopnamen mogelijk | JA | - |
| Azure Backup mogelijke VM-moment opnamen | JA | - |
| Kosten | LAAG | - |



**Samen vatting:** Azure Standard SSD-opslag is de minimale aanbeveling voor niet-productie-Vm's voor de basis-VHD, mogelijke DBMS-implementaties met relatieve latentie ingevoeligheid en/of lage IOPS-en doorvoer snelheden. Dit type Azure-opslag wordt niet meer ondersteund om de SAP Global Trans Port-map te hosten. 



## <a name="azure-standard-hdd-storage"></a>Opslag van Azure Standard-HDD
De Azure Standard-HDD-opslag was het enige opslag type toen de Azure-infra structuur in het jaar 2014 is gecertificeerd voor SAP NetWeaver-workload. In het jaar 2014 waren de virtuele machines van Azure klein en laag in de opslag doorvoer. Dit opslag type kan daarom gewoon aan de eisen voldoen. De opslag is ideaal voor niet-beschik bare latenties, die u in de SAP-ruimte nauwelijks kunt belasten. Met de toenemende door Voer van Azure Vm's en de toegenomen werk belasting van deze Vm's, wordt dit opslag type niet meer in rekening gebracht voor het gebruik met SAP-scenario's. De functie matrix voor SAP-werk belasting ziet er als volgt uit:

| Mogelijkheid| Opmerking| Opmerkingen/koppelingen | 
| --- | --- | --- | 
| VHD-basis versie van het besturings systeem | niet geschikt | - |
| Gegevensschijf | niet geschikt | - |
| SAP Global Trans Port-map | NO | [Niet ondersteund](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP-sapmnt | NO | Niet ondersteund |
| Back-upopslag | oplos | - |
| Shares/gedeelde schijf | niet beschikbaar | Vereist Azure Files of externe partij |
| Flexibiliteit | LRS, GRS | Geen ZRS beschikbaar voor schijven |
| Latentie | hoog | te hoog voor DBMS-gebruik, SAP Global Trans Port Directory of sapmnt/saploc |
| SLA VOOR IOPS | NO | - |
| Maximum aantal IOPS per schijf | 500 | Onafhankelijk van de grootte van de schijf |
| SLA voor door Voer | NO | - |
| HANA-gecertificeerd | NO | - |
| Schijf momentopnamen mogelijk | JA | - |
| Azure Backup mogelijke VM-moment opnamen | JA | - |
| Kosten | LAAG | - |



**Samen vatting:** Standard-HDD is een Azure-opslag type dat alleen mag worden gebruikt voor het opslaan van SAP-back-ups. Het mag alleen worden gebruikt als basis-VHD voor niet-actieve systemen, zoals buiten gebruik gestelde systemen die worden gebruikt voor het opzoeken van gegevens en daar. Maar er zijn geen actieve ontwikkelings-, QA-of productie-Vm's op basis van die opslag. Ook worden database bestanden gehost op die opslag


## <a name="azure-vm-limits-in-storage-traffic"></a>Limieten voor Azure-VM'S in opslag verkeer
In het tegenovergestelde van on-premises scenario's speelt het afzonderlijke VM-type dat u selecteert, een vitale rol in de opslag bandbreedte die u kunt gebruiken. Voor de verschillende opslag typen moet u rekening houden met het volgende:

| Opslagtype| Linux | Windows | Opmerkingen |
| --- | --- | --- | --- |
| Standard HDD | [Grootten voor virtuele Linux-machines in azure](../../sizes.md) | [Grootten voor virtuele Windows-machines in azure](../../sizes.md) | De opslag limieten van medium of grote Vm's zijn waarschijnlijk hard. |
| Standard SSD | [Grootten voor virtuele Linux-machines in azure](../../sizes.md) | [Grootten voor virtuele Windows-machines in azure](../../sizes.md) | De opslag limieten van medium of grote Vm's zijn waarschijnlijk hard. |
| Premium Storage | [Grootten voor virtuele Linux-machines in azure](../../sizes.md) | [Grootten voor virtuele Windows-machines in azure](../../sizes.md) | Onbeperkte limieten voor IOPS of VM-doorvoer opslag met opslag configuratie |
| Ultra Disk-opslag | [Grootten voor virtuele Linux-machines in azure](../../sizes.md) | [Grootten voor virtuele Windows-machines in azure](../../sizes.md) | Onbeperkte limieten voor IOPS of VM-doorvoer opslag met opslag configuratie |
| Azure NetApp Files | [Grootten voor virtuele Linux-machines in azure](../../sizes.md) | [Grootten voor virtuele Windows-machines in azure](../../sizes.md) | Opslag verkeer maakt gebruik van netwerk doorvoer bandbreedte en geen opslag bandbreedte. |

Als beperkingen kunt u het volgende weten:

- Hoe kleiner de virtuele machine is, hoe minder schijven u kunt bijvoegen. Dit is niet van toepassing op ANF. Sinds u NFS-of SMB-shares koppelt, treedt er geen limiet op voor het aantal gedeelde volumes dat moet worden gekoppeld
- Vm's hebben I/O-door Voer en IOPS-limieten die eenvoudig kunnen worden overschreden met Premium-opslag schijven en ultra schijven
- Met ANF neemt het verkeer naar de gedeelde volumes gebruik van de netwerk bandbreedte van de virtuele machine en niet de opslag bandbreedte
- Met grote NFS-volumes in de TiB capaciteits ruimte met dubbele cijfers, wordt de door Voer van een dergelijk volume uit één virtuele machine op basis van de limieten van Linux voor één sessie met het gedeelde volume. 

Als u virtuele Azure-machines in de levens cyclus van een SAP-systeem up-to-size hebt, moet u de IOPS-en opslag doorvoer limieten van het nieuwe en grotere VM-type evalueren. In sommige gevallen kan het zinvol zijn om de opslag configuratie aan te passen aan de nieuwe mogelijkheden van de Azure VM. 


## <a name="striping-or-not-striping"></a>Striping of geen striping
Als u een Stripe-set van meerdere Azure-schijven in één groter volume maakt, kunt u de IOPS en door Voer van de afzonderlijke schijven in één volume verzamelen. Het wordt alleen gebruikt voor Azure Standard-opslag en Azure Premium Storage. Met de volledige schijf van Azure kunt u de door Voer en IOPS onafhankelijk van de capaciteit van een schijf configureren. het gebruik van stripesets is niet vereist. Gedeelde volumes die zijn gebaseerd op NFS of SMB, kunnen niet worden gestripd. Als gevolg van de niet-lineaire aard van Azure Premium Storage-door Voer en IOPS, kunt u kleinere capaciteit inrichten met dezelfde IOPS en door voer dan grote enkele Azure Premium-opslag schijven. Dit is de methode voor een hogere door Voer of IOPS bij lagere kosten met Azure Premium Storage. Met een striping over twee P15 Premium-opslag schijven krijgt u bijvoorbeeld de volgende mogelijkheden: 

- 250-MiB per seconde. Een dergelijk volume gaat over 512 GiB-capaciteit. Als u één schijf wilt hebben die u 250 MiB-door Voer per seconde krijgt, moet u een P40-schijf kiezen met 2 TiB-capaciteit. 
- 400 MiB per seconde door het verwijderen van vier P10 Premium-opslag schijven met een totale capaciteit van 512 GiB door strepen. Als u één schijf met een minimum van 500 MiB-door Voer per seconde wilt hebben, moet u een P60 Premium-opslag schijf kiezen met 8 TiB. Omdat de kosten van Premium Storage bijna lineair zijn met de capaciteit, kunt u de kosten besparen door gebruik te maken van striping.

Sommige regels moeten worden gevolgd voor Stripe:

- Geen in-VM geconfigureerde opslag moet worden gebruikt omdat Azure Storage de gegevens overbodig blijft
- De schijven waarop de Stripe-set is toegepast, moeten even groot zijn

Striping over meerdere kleinere schijven is de beste manier om een goede prijs-prestatie verhouding te krijgen met behulp van Azure Premium Storage. Het is duidelijk dat striping een aantal extra overhead voor implementatie en beheer heeft.

Lees de documentatie voor het verschillende DBMS, zoals [SAP Hana opslag configuraties van virtuele Azure-machines](./hana-vm-operations-storage.md), voor specifieke aanbevelingen voor de Stripe-grootte.




## <a name="next-steps"></a>Volgende stappen
Lees de artikelen:

- [Overwegingen voor de implementatie van Azure Virtual Machines DBMS voor SAP-workloads](./dbms_guide_general.md)
- [Configuraties van SAP HANA in virtuele Azure-machineopslag](./hana-vm-operations-storage.md)
