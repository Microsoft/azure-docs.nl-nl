---
title: Prestaties van SMB meerdere kanalen-Azure Files
description: Meer informatie over de prestaties van SMB meerdere kanalen.
author: gunjanj
ms.service: storage
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: gunjanj
ms.subservice: files
ms.openlocfilehash: a9edd93aa265622732be4a7582cce9900959bf6d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100374980"
---
# <a name="smb-multichannel-performance"></a>Prestaties van SMB Multichannel

Met Azure Files SMB meerdere kanalen (preview) kan een SMB 3. x-client verschillende netwerk verbindingen tot stand brengen met de Premium-bestands shares in een FileStorage-account. Het SMB 3,0-protocol heeft de functie SMB meerdere kanalen geïntroduceerd in Windows Server 2012-en Windows 8-clients. Daarom kan elke Azure Files SMB 3. x-client die ondersteuning biedt voor SMB meerdere kanalen profiteren van de functie voor hun Azure Premium-bestands shares. Er zijn geen extra kosten verbonden aan het inschakelen van SMB meerdere kanalen op een opslag account.

## <a name="benefits"></a>Voordelen

Met Azure Files SMB meerdere kanalen kunnen clients verschillende netwerk verbindingen gebruiken die betere prestaties bieden en de kosten van eigendom verlagen. De prestaties worden verhoogd door middel van bandbreedte aggregatie over meerdere Nic's en gebruik te maken van RSS-ondersteuning (Receive Side Scaling) voor Nic's om de i/o-belasting over meerdere Cpu's te verdelen.

- **Verhoogde door Voer**: meerdere verbindingen maken het mogelijk om gegevens over meerdere paden parallel over te brengen en maakt daardoor aanzienlijke voor delen van werk belastingen die grotere bestands grootten met grotere io-grootten gebruiken en vereisen een hoge door Voer van één virtuele machine of een kleinere set vm's. Enkele van deze werk belastingen zijn media en entertainment voor het maken van inhoud of transcode ring, Genomics en risico analyse van financiële services.
- **Hogere IOPS**: de NIC RSS-mogelijkheid maakt effectief verdelen over meerdere cpu's met meerdere verbindingen mogelijk. Dit helpt de schaal van meer IOPS en effectief gebruik van VM-Cpu's te behaalen. Dit is handig voor werk belastingen met kleine i/o-grootten, zoals database toepassingen.
- **Netwerk fout tolerantie**: meerdere verbindingen verminderen het risico van onderbrekingen omdat clients niet langer gebruikmaken van een afzonderlijke verbinding.
- **Automatische configuratie**: Wanneer SMB meerdere kanalen is ingeschakeld op clients en opslag accounts, kan bestaande verbindingen dynamisch worden gedetecteerd en kunnen er indien nodig verbindings paden worden gemaakt.
- **Kosten optimalisatie**: werk belastingen kunnen een hogere schaal belasten van één virtuele machine of een kleine set virtuele machines, terwijl er verbinding wordt gemaakt met Premium-shares. Dit kan de total cost of ownership verminderen door het aantal Vm's te verminderen dat nodig is om een werk belasting uit te voeren en te beheren.

Raadpleeg de [Windows-documentatie](/azure-stack/hci/manage/manage-smb-multichannel)voor meer informatie over SMB meerdere kanalen.

Deze functie biedt betere prestatie voordelen voor toepassingen met meerdere threads, maar biedt doorgaans geen ondersteuning voor toepassingen met één thread. Zie de sectie [prestaties vergelijken](#performance-comparison) voor meer informatie.

## <a name="limitations"></a>Beperkingen

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Regionale beschikbaarheid

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="configuration"></a>Configuratie

SMB meerdere kanalen werkt alleen wanneer de functie is ingeschakeld op client-side (uw client) en aan de service zijde (uw Azure Storage-account).

SMB meerdere kanalen is standaard ingeschakeld op Windows-clients. U kunt uw configuratie controleren door de volgende Power shell-opdracht uit te voeren: 

`Get-smbClientConfiguration | select EnableMultichannel`.
 
In uw Azure Storage-account moet u SMB meerdere kanalen inschakelen. Zie [SMB meerdere kanalen inschakelen (preview)](storage-files-enable-smb-multichannel.md).

### <a name="disable-smb-multichannel"></a>SMB meerdere kanalen uitschakelen
In de meeste scenario's, met name multi-threaded workloads, moeten clients betere prestaties zien met SMB meerdere kanalen. Er zijn echter enkele specifieke scenario's, zoals werk belastingen met één thread of voor test doeleinden, wilt u SMB meerdere kanalen mogelijk uitschakelen. Zie [prestatie vergelijking](#performance-comparison) voor meer informatie.

## <a name="verify-smb-multichannel-is-configured-correctly"></a>Controleren of SMB meerdere kanalen correct is geconfigureerd

1. Maak een Premium-bestands share of gebruik een bestaande.
1. Zorg ervoor dat uw client SMB meerdere kanalen ondersteunt (voor een of meer netwerk adapters is schalen aan de ontvangst zijde ingeschakeld). Raadpleeg de [Windows-documentatie](/azure-stack/hci/manage/manage-smb-multichannel) voor meer informatie.
1. Een bestands share koppelen aan de client.
1. Genereer load met uw toepassing.
    Een kopieer programma zoals Robocopy/MT of een hulp programma voor prestaties zoals Diskspd naar lezen/schrijven bestanden kan loads genereren.
1. Open Power shell als beheerder en gebruik de volgende opdracht: `Get-SmbMultichannelConnection |fl`
1. Zoeken naar **MaxChannels** -en **CurrentChannels** -eigenschappen

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG" alt-text="Scherm opname van Get-SMBMultichannelConnection resultaten." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-connection.PNG":::

## <a name="performance-comparison"></a>Vergelijking van prestaties

Er zijn twee categorieën van werk belasting voor lezen/schrijven-patronen met één thread en meerdere threads. De meeste werk belastingen gebruiken meerdere bestanden, maar er kunnen specifieke gebruiks situaties zijn waarbij de werk belasting met één bestand in een share werkt. In deze sectie worden de verschillende use cases en de invloed van de prestaties voor elk gebruik besproken. Over het algemeen zijn de meeste werk belastingen multi-threaded en kunnen werk belasting worden gedistribueerd over meerdere bestanden, zodat ze aanzienlijke prestatie verbeteringen kunnen opleveren met SMB meerdere kanalen.

- **Multi-threaded/meerdere bestanden**: afhankelijk van het werkbelasting patroon moet u belang rijke prestatie verbetering zien in read en write IOS via meerdere kanalen. De prestatie verhogingen variëren van elke locatie tussen 2x en 4, voor IOPS, door Voer en latentie. Voor deze categorie moet SMB meerdere kanalen zijn ingeschakeld voor de beste prestaties.
- **Multi-threaded/één bestand**: voor de meeste gebruiks voorbeelden in deze categorie kunnen werk belastingen profiteren van het gebruik van SMB meerdere kanalen, met name als de werk belasting een gemiddelde io-grootte > ~ 16 KB heeft. Enkele voorbeeld scenario's die van SMB meerdere kanalen profiteren, zijn back-ups of herstel van één groot bestand. Een uitzonde ring waarbij u SMB meerdere kanalen mogelijk wilt uitschakelen, is dat de werk belasting klein IOs zwaar is. In dat geval kan het prestatie verlies van meer dan ongeveer 10% worden verminderd. Afhankelijk van de use-case kunt u de belasting spreiden over meerdere bestanden of de functie uitschakelen. Zie de sectie [configuratie](#configuration) voor meer informatie.
- **Enkelvoudige/meerdere bestanden of één bestand**: voor de meeste werk belastingen met één thread zijn er minimale prestatie voordelen vanwege een gebrek aan parallelie, meestal is er sprake van een geringe prestatie vermindering van meer dan 10% als SMB meerdere kanalen is ingeschakeld. In dit geval is het ideaal om SMB meerdere kanalen uit te scha kelen, met één uitzonde ring. Als de werk belasting met één thread de belasting kan verdelen over meerdere bestanden en gebruikmaakt van een gemiddelde grotere IO-grootte (> ~ 16 KB), moeten er minder prestatie voordelen van SMB meerdere kanalen beschikbaar zijn.

### <a name="performance-test-configuration"></a>Configuratie van prestatie testen

Voor de grafieken in dit artikel is de volgende configuratie gebruikt: een enkele standaard D32s v3-VM met één NIC met RSS-functionaliteit met vier kanalen. De belasting is gegenereerd met diskspd.exe, meerdere threads met een IO-diepte van 10 en wille keurige IOs met verschillende i/o-grootten.

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Max. aantal gegevensschijven | Maxi maal cache geheugen en tijdelijke opslag doorvoer: IOPS/MBps (cache grootte in GiB) | Maxi maal aantal niet-opgeslagen schijf doorvoer: IOPS/MBps | Max. aantal NIC's|Verwachte netwerk bandbreedte (Mbps) |
|---|---|---|---|---|---|---|---|---|
| [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8|16000 |

:::image type="content" source="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG" alt-text="Scherm opname van de configuratie van de prestatie test." lightbox="media/storage-files-smb-multichannel-performance/files-smb-multi-channel-nic-settings-all-nics.PNG":::

### <a name="mutli-threadedmultiple-files-with-smb-multichannel"></a>Multi-threads/meerdere bestanden met SMB multi kanaal

Laden is gegenereerd op basis van 10 bestanden met verschillende i/o-grootten. De test resultaten voor opschalen hebben aanzienlijke verbeteringen in de resultaten van IOPS en door Voer met SMB meerdere kanalen ingeschakeld. In de volgende diagrammen ziet u de resultaten:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png" alt-text="Diagram van prestaties" lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png" alt-text="Diagram van doorvoer prestaties." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-multiple-files-compared-to-single-channel-throughput-performance.png":::

- Op één NIC, voor lees bewerkingen, werd de prestatie verhoging van 2x-3x waargenomen en voor schrijf bewerkingen, met een toename van 3x-4x in termen van zowel IOPS als door voer.
- SMB meerdere kanalen toegestaan IOPS en door Voer om VM-limieten te bereiken, zelfs met één NIC en de vier limieten voor het kanaal.
- Sinds het afvoeren (of lezen naar de opslag) niet is gemeten, heeft lees doorvoer de VM-limiet van 16.000 Mbps (2 GiB/s) overschreden. De test is behaald >2,7 GiB/s. Voor inkomend verkeer (of schrijf bewerkingen naar opslag) zijn nog steeds VM-limieten van toepassing.
- Het verspreiden van belasting over meerdere bestanden is toegestaan voor aanzienlijke verbeteringen.

Een voor beeld van een opdracht die is gebruikt in deze tests is: 

`diskspd.exe -W300 -C5 -r -w100 -b4k -t8 -o8 -Sh -d60 -L -c2G -Z1G z:\write0.dat z:\write1.dat z:\write2.dat z:\write3.dat z:\write4.dat z:\write5.dat z:\write6.dat z:\write7.dat z:\write8.dat z:\write9.dat `.

### <a name="multi-threadedsingle-file-workloads-with-smb-multichannel"></a>Werk belastingen met meerdere threads/enkelvoudige bestanden met SMB multi kanaal

De belasting is gegenereerd op basis van één 128 GiB-bestand. Als SMB meerdere kanalen is ingeschakeld, zijn in de meeste gevallen verbeteringen aangebracht in de geschaalde test met multi-threaded/enkelvoudige bestanden. In de volgende diagrammen ziet u de resultaten:

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png" alt-text="Diagram van IOPS-prestaties." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-iops-performance.png":::

:::image type="content" source="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png" alt-text="Diagram van de prestaties van één bestands doorvoer." lightbox="media/storage-files-smb-multichannel-performance/diagram-smb-multi-channel-single-file-compared-to-single-channel-throughput-performance.png":::

- Op één NIC met een grotere gemiddelde i/o-grootte (> ~ 16 KB) zijn er belang rijke verbeteringen aangebracht in lees-en schrijf bewerkingen.
- Voor kleinere IO-grootten is er een lichte impact van ~ 10% op de prestaties als SMB meerdere kanalen is ingeschakeld. Dit kan worden verholpen door de belasting over meerdere bestanden te spreiden of de functie uit te scha kelen.
- De prestaties zijn nog steeds afhankelijk van de [limieten van enkele bestanden](storage-files-scale-targets.md#file-scale-targets).

## <a name="optimizing-performance"></a>Prestaties optimaliseren

De volgende tips kunnen u helpen uw prestaties te optimaliseren:

- Zorg ervoor dat uw opslag account en uw client zich in dezelfde Azure-regio bevinden om de netwerk latentie te verminderen.
- Gebruik toepassingen met meerdere threads en verspreide taken over meerdere bestanden.
- Prestatie voordelen van SMB meerdere kanalen nemen toe met het aantal bestanden dat de belasting distribueert.
- De prestaties van Premium-shares zijn gebonden aan de ingerichte share grootte (IOPS/uitgaand/binnenkomend) en limieten voor één bestand. Zie voor meer informatie [over het inrichten van Premium-bestands shares](understanding-billing.md#provisioned-model).
- De maximale prestaties van één VM-client zijn nog steeds gebonden aan VM-limieten. [Standard_D32s_v3](../../virtual-machines/dv3-dsv3-series.md) kan bijvoorbeeld een maximale band breedte van 16.000 Mbps (of 2 Gbps) ondersteunen. de uitvoer van de virtuele machine (schrijf bewerkingen naar opslag) is niet toegestaan in een Data limiet, ingangs (Lees bewerkingen van opslag). De prestaties van bestands shares zijn afhankelijk van de netwerk limieten van de computer, Cpu's, interne opslag beschik bare netwerk bandbreedte, i/o-grootte, parallelle uitvoering en andere factoren.
- De eerste test is doorgaans een warm-up, de resultaten worden verwijderd en de test wordt herhaald.
- Als de prestaties beperkt zijn door één client en de werk belasting nog steeds lager is dan de ingerichte limieten voor shares, kunnen hogere prestaties worden bereikt door de belasting over meerdere clients te spreiden.

### <a name="the-relationship-between-iops-throughput-and-io-sizes"></a>De relatie tussen IOPS, door Voer en IO-grootten

**Door Voer = i/o-grootte * IOPS**

Hogere i/o-grootten zorgen voor een hogere door Voer en hebben hogere latenties, wat resulteert in een lager aantal net IOPS. Bij kleinere IO-grootten wordt meer IOPS gereduceerd, maar resulteert dit in lagere netto-door Voer en latenties.

## <a name="next-steps"></a>Volgende stappen

- [SMB meerdere kanalen inschakelen op een FileStorage-account (preview-versie)](storage-files-enable-smb-multichannel.md)
- Raadpleeg de [Windows-documentatie](/azure-stack/hci/manage/manage-smb-multichannel) voor meer informatie over SMB meerdere kanalen.
