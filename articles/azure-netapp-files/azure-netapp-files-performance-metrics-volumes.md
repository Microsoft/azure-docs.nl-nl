---
title: Aanbevolen benchmark tests voor prestaties-Azure NetApp Files
description: Meer informatie over aanbevelingen voor benchmark tests voor de prestaties en metrische gegevens van het volume met behulp van Azure NetApp Files.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: f73091552a78760024189b173897913edca724bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100593414"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Aanbevelingen van prestatiebenchmarks voor Azure NetApp Files

In dit artikel vindt u aanbevelingen voor het testen van de prestaties en metrische gegevens van het volume met behulp van Azure NetApp Files.

## <a name="overview"></a>Overzicht

Als u de prestatie kenmerken van een Azure NetApp Files volume wilt weten, kunt u het open source-hulp programma [FIO](https://github.com/axboe/fio) gebruiken om een reeks benchmarks uit te voeren voor het simuleren van verschillende werk belastingen. FIO kan worden geïnstalleerd op zowel Linux-als Windows-besturings systemen.  Het is een uitstekend hulp programma voor het verkrijgen van een snelle moment opname van zowel IOPS als door Voer voor een volume.

### <a name="vm-instance-sizing"></a>Grootte van VM-exemplaar

Zorg ervoor dat u een VM-exemplaar (virtuele machine) gebruikt dat de juiste grootte heeft voor het uitvoeren van de tests voor de beste resultaten. In de volgende voor beelden wordt een Standard_D32s_v3-exemplaar gebruikt. Zie [grootten voor virtuele Windows-machines in azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor Windows-Vm's en [grootten voor virtuele Linux-machines in azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) voor Vm's op basis van Linux voor meer informatie over de grootte van VM-exemplaren.

### <a name="azure-netapp-files-volume-sizing"></a>Volume grootte Azure NetApp Files

Zorg ervoor dat u het juiste service niveau en volume quotum grootte kiest voor het verwachte prestatie niveau. Zie [service niveaus voor Azure NetApp files](azure-netapp-files-service-levels.md) voor meer informatie.

### <a name="virtual-network-vnet-recommendations"></a>Aanbevelingen voor virtuele netwerken (VNet)

U moet de benchmark tests uitvoeren in hetzelfde VNet als Azure NetApp Files. In het volgende voor beeld wordt de aanbeveling gedemonstreerd:

![VNet-aanbevelingen](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Installatie van FIO

FIO is beschikbaar in binaire indeling voor zowel Linux als Windows. Volg de sectie binaire pakketten in [FIO](https://github.com/axboe/fio) om te installeren voor het platform van uw keuze.

## <a name="fio-examples-for-iops"></a>FIO-voor beelden voor IOPS 

De FIO-voor beelden in deze sectie gebruiken de volgende configuratie:
* Grootte van VM-exemplaar: D32s_v3
* Service niveau en grootte van capaciteits groep: Premium/50 TiB
* Volume quotum grootte: 48 TiB

In de volgende voor beelden ziet u de FIO wille keurige Lees-en schrijf bewerkingen.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: 8k-blok grootte 100% wille keurige Lees bewerkingen

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Uitvoer: 68k Read IOPS weer gegeven

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: 8k-blok grootte 100% wille keurige schrijf bewerkingen

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Uitvoer: 73k write IOPS wordt weer gegeven

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>FIO-voor beelden voor band breedte

In de voor beelden in deze sectie ziet u de FIO sequentiële Lees-en schrijf bewerkingen.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 64k blok grootte 100% sequentiële Lees bewerkingen

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Uitvoer: 11,8 Gbit/s-door Voer weer gegeven

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: 64k blok grootte 100% opeenvolgende schrijf bewerkingen

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Uitvoer: 12,2 Gbit/s-door Voer weer gegeven

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Metrische gegevens van volume

Azure NetApp Files prestatie gegevens zijn beschikbaar via Azure Monitor tellers. De tellers zijn beschikbaar via de Azure Portal en REST API GET-aanvragen. 

U kunt historische gegevens weer geven voor de volgende informatie:
* Gemiddelde lees latentie 
* Gemiddelde schrijf latentie 
* IOPS lezen (gemiddeld)
* IOPS schrijven (gemiddeld)
* Logische volume grootte (gemiddeld)
* Grootte van moment opname van volume (gemiddeld)

### <a name="using-azure-monitor"></a>Azure Monitor gebruiken 

U kunt op de pagina metrische gegevens toegang krijgen tot Azure NetApp Files tellers per volume, zoals hieronder wordt weer gegeven:

![Azure Monitor metrische gegevens](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

U kunt ook een dash board maken in Azure Monitor voor Azure NetApp Files door naar de pagina metrische gegevens te gaan, te filteren op NetApp en de gewenste volume tellers op te geven: 

![Azure Monitor-dashboard](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Azure Monitor API-toegang

U kunt toegang krijgen tot Azure NetApp Files meter items door gebruik te maken van REST API-aanroepen. Zie [ondersteunde metrische gegevens met Azure monitor: micro soft. NetApp/netAppAccounts/capacityPools/volumes](../azure-monitor/essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) voor tellers voor capaciteits Pools en volumes.

In het volgende voor beeld wordt een GET URL weer gegeven voor het weer geven van logische volume grootte:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Volgende stappen

- [Serviceniveau's voor Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Prestatiebenchmarks voor Linux](performance-benchmarks-linux.md)