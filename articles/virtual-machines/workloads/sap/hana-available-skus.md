---
title: SKU's voor SAP HANA in Azure (Large Instances) | Microsoft Docs
description: SKU's voor SAP HANA in Azure (Large Instances).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
keywords: HLI, HANA, SKU's, S896, S224, S448, S672, Optane, SAP
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 4/16/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017, references_regions
ms.openlocfilehash: 3ecbbe4d477f3e6c3c6606528c51b934b6cf534a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718733"
---
# <a name="available-skus-for-hana-large-instances"></a>Beschikbare SKU's voor grote HANA-exemplaren

De BareMetal Infrastructure-service (gecertificeerd voor SAP HANA workloads) op basis van Rev 4.2* is beschikbaar in de volgende regio's:
- Europa -west
- Europa - noord
- Duitsland - west-centraal zones ondersteunen
- VS - oost met Zones-ondersteuning
- VS - oost 2
- VS - zuid-centraal
- VS - west 2 met Zones-ondersteuning

De service BareMetal Infrastructure (gecertificeerd SAP HANA workloads) op basis van Rev 3* heeft beperkte beschikbaarheid in de volgende regio's:
- VS - west
- VS - oost 
- Australië - oost 
- Australië - zuidoost
- Japan - oost


Hier volgt een lijst met beschikbare Azure Large-exemplaren.

> [!IMPORTANT]
> Let op de eerste kolom die de status van HANA-certificering voor elk van de typen Grote exemplaren in de lijst vertegenwoordigt. De kolom moet correleren met [de SAP HANA hardwaremap voor](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) de Azure-SKU's die beginnen met de letter **S**.



| SAP HANA gecertificeerd | Model | Totaal geheugen | Geheugen-DRAM | Memory Optane | Storage | Beschikbaarheid |
| --- | --- | --- | --- | --- | --- | --- |
| JA <br />[OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2185) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2265) | SAP HANA in Azure S96<br /> – 2 x Intel® Xeon® Processor E7-8890 v4 <br /> 48 CPU-kernen en 96 CPU-threads |  768 GB | 768 GB | --- | 3,0 TB | Beschikbaar |
| JA <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2186) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2269) | SAP HANA in Azure S224<br /> – 4 x Intel® Xeon® Processor 8276 <br /> 112 CPU-kernen en 224 CPU-threads |  3,0 TB | 3,0 TB | --- | 6,3 TB | Beschikbaar |
| JA <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2297) | SAP HANA in Azure S224m<br /> – 4 x Intel® Xeon® Processor 8276 <br /> 112 CPU-kernen en 224 CPU-threads |  6,0 TB | 6,0 TB | --- | 10,5 TB | Beschikbaar |
| JA <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2381) | SAP HANA in Azure S224om<br /> – 4 x Intel® Xeon® Processor 8276 <br /> 112 CPU-kernen en 224 CPU-threads | 6,0 TB |  3,0 TB |  3,0 TB | 10,5 TB | Beschikbaar |
| NO | SAP HANA in Azure S224oo<br /> – 4 x Intel® Xeon® Processor 8276 <br /> 112 CPU-kernen en 224 CPU-threads | 4,5 TB |  1,5 TB |  3,0 TB | 8,4 TB | Beschikbaar |
| NO | SAP HANA in Azure S224ooo<br /> – 4 x Intel® Xeon® Processor 8276 <br /> 112 CPU-kernen en 224 CPU-threads | 7,5 TB |  1,5 TB |  6,0 TB | 12,7 TB | Beschikbaar |
| NO | SAP HANA Azure S224 gebruik<br /> – 4 x Intel® Xeon® Processor 8276 <br /> 112 CPU-kernen en 224 CPU-threads | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB | Beschikbaar |
| JA <br />[OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1983) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2268) | SAP HANA in Azure S384<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-kernen en 384 CPU-threads |  4,0 TB | 4,0 TB | --- | 16 TB | Beschikbaar |
| JA <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2080) | SAP HANA on Azure S384m<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-kernen en 384 CPU-threads |  6,0 TB | 6,0 TB | --- | 18 TB |  Beschikbaar  |
| JA <br />[OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1984) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2267) | SAP HANA on Azure S384xm<br /> – 8 x Intel® Xeon® Processor E7-8890 v4<br /> 192 CPU-kernen en 384 CPU-threads |  8,0 TB | 8,0 TB | --- | 22 TB | Beschikbaar |
| JA <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2411) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2378) | SAP HANA in Azure S448<br /> – 8 x Intel® Xeon® Processor 8276 <br /> 224 CPU-kernen en 448 CPU-threads | 6,0 TB |  6,0 TB |  --- | 10,5 TB | Beschikbaar (alleen Rev 4.2) |
| JA <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2410) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2377) | SAP HANA on Azure S448m<br /> – 8 x Intel® Xeon® Processor 8276 <br /> 224 CPU-kernen en 448 CPU-threads | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Beschikbaar (alleen Rev 4.2) |
| NO | SAP HANA in Azure S448oo<br /> – 8 x Intel® Xeon® Processor 8276 <br /> 224 CPU-kernen en 448 CPU-threads | 9,0 TB |  3,0 TB |  6,0 TB | 14,8 TB  | Beschikbaar (alleen Rev 4.2) |
| NO | SAP HANA in Azure S448om<br /> – 8 x Intel® Xeon® Processor 8276 <br /> 224 CPU-kernen en 448 CPU-threads | 12,0 TB |  6,0 TB |  6,0 TB | 18,9 TB  | Beschikbaar (alleen Rev 4.2) |
| NO | SAP HANA on Azure S448ooo<br /> – 8 x Intel® Xeon® Processor 8276 <br /> 224 CPU-kernen en 448 CPU-threads | 15,0 TB |  3,0 TB |  12,0 TB | 23,2 TB  | Beschikbaar (alleen Rev 4.2) |
| NO | SAP HANA Azure S448 gebruik<br /> – 8 x Intel® Xeon® Processor 8276 <br /> 224 CPU-kernen en 448 CPU-threads | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Beschikbaar (alleen Rev 4.2) |
| JA <br /> [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2049) | SAP HANA in Azure S576m<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 CPU-kernen en 576 CPU-threads |  12,0 TB | 12,0 TB | --- | 28 TB | Beschikbaar (alleen Rev 4.2) |
| NO | SAP HANA on Azure S576xm<br /> – 12 x Intel® Xeon® Processor E7-8890 v4<br /> 288 CPU-kernen en 576 CPU-threads |  18,0 TB | 18.0 | --- |  41 TB | Beschikbaar |
| JA <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2409) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2380) | SAP HANA in Azure S672<br /> – 12 x Intel® Xeon® Processor 8276 <br /> 336 CPU-kernen en 672 CPU-threads | 9,0 TB |  9,0 TB |  --- | 14,7 TB | Beschikbaar (alleen Rev 4.2) |
| JA <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2408) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2379) | SAP HANA in Azure S672m<br /> – 12 x Intel® Xeon® Processor 8276 <br /> 336 CPU-kernen en 672 CPU-threads | 18,0 TB |  18,0 TB |  --- | 27,4 TB | Beschikbaar (alleen Rev 4.2) |
| NO | SAP HANA in Azure S672oo<br /> – 12 x Intel® Xeon® Processor 8276 <br /> 336 CPU-kernen en 672 CPU-threads | 13,5 TB |  4,5 TB |  9,0 TB | 21,1 TB  | Beschikbaar (alleen Rev 4.2) |
| NO | SAP HANA in Azure S672om<br /> – 12 x Intel® Xeon® Processor 8276 <br /> 336 CPU-kernen en 672 CPU-threads | 18,0 TB |  9,0 TB |  9,0 TB | 27,4 TB  | Beschikbaar (alleen Rev 4.2) |
| NO | SAP HANA in Azure S672ooo<br /> – 12 x Intel® Xeon® Processor 8276 <br /> 336 CPU-kernen en 672 CPU-threads | 22,5 TB |  4,5 TB |  18,0 TB | 33,7 TB  | Beschikbaar (alleen Rev 4.2) |
| NO | SAP HANA op Azure S672platform<br /> – 12 x Intel® Xeon® Processor 8276 <br /> 336 CPU-kernen en 672 CPU-threads | 27,0 TB |  9,0 TB |  18,0 TB | 40,0 TB  | Beschikbaar (alleen Rev 4.2) |
| JA <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1985) | SAP HANA in Azure S768m<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 CPU-kernen en 768 CPU-threads |  16,0 TB | 16,0 TB | -- | 36 TB | Beschikbaar |
| NO | SAP HANA in Azure S768xm<br /> – 16 x Intel® Xeon® Processor E7-8890 v4<br /> 384 CPU-kernen en 768 CPU-threads |  24,0 TB | 24,0 TB | --- | 56 TB | Beschikbaar |
|  JA <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2407) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2376)  | SAP HANA in Azure S896<br /> – 16 x Intel® Xeon® Processor 8276 <br /> 448 CPU-kernen en 896 CPU-threads | 12,0 TB |  12,0 TB |  --- | 18,9 TB | Beschikbaar (alleen Rev 4.2) |
| JA <br /> [OLAP,](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/#/solutions?filters=iaas;ve:24&id=s:2406) [OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=2328) | SAP HANA on Azure S896m<br /> – 16 x Intel® Xeon® De 8276-processor <br /> 448 CPU-kernen en 896 CPU-threads | 24,0 TB | 24,0 TB | -- | 35,8 TB | Beschikbaar |
| NO | SAP HANA op Azure S896oo<br /> – 16 x Intel® Xeon® Processor 8276 <br /> 448 CPU-kernen en 896 CPU-threads | 18,0 TB |  6,0 TB |  12,0 TB | 27,4 TB  | Beschikbaar (alleen Rev 4.2) |
| NO | SAP HANA in Azure S896om<br /> – 16 x Intel® Xeon® De 8276-processor <br /> 448 CPU-kernen en 896 CPU-threads | 24,0 TB |  12,0 TB |  12,0 TB | 35,8 TB  | Beschikbaar (alleen Rev 4.2) |
| NO | SAP HANA in Azure S896ooo<br /> – 16 x Intel® Xeon® De 8276-processor <br /> 448 CPU-kernen en 896 CPU-threads | 30,0 TB |  6,0 TB |  24,0 TB | 44,3 TB  | Beschikbaar (alleen Rev 4.2) |
| NO | SAP HANA op Azure S896platform<br /> – 16 x Intel® Xeon® Processor 8276 <br /> 448 CPU-kernen en 896 CPU-threads | 36,0 TB |  12,0 TB |  24,0 TB | 52,7 TB  | Beschikbaar (alleen Rev 4.2) |
| JA <br />[OLTP](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure&recordid=1986) | SAP HANA in Azure S960m<br /> – 20 x Intel® Xeon® Processor E7-8890 v4<br /> 480 CPU-kernen en 960 CPU-threads |  20,0 TB | 20,0 TB | -- | 46 TB | Beschikbaar (alleen Rev 4.2) |


- CPU-kernen = som van niet-hyperthreaded CPU-kernen van de som van de processors van de servereenheid.
- CPU-threads = som van rekenthreads geleverd door hyperthreaded CPU-kernen van de som van de processors van de servereenheid. De meeste eenheden zijn standaard geconfigureerd voor het gebruik van Hyper-Threading Technologie.
- Op basis van aanbevelingen van leveranciers zijn S768m, S768xm en S960m niet geconfigureerd voor het gebruik van Hyper-Threading voor het uitvoeren van SAP HANA.


> [!IMPORTANT]
> De volgende SKU's, maar nog steeds ondersteund, kunnen niet meer worden aangeschaft: S72, S72m, S144, S144m, S192 en S192m 

De specifieke configuraties die u kiest, zijn afhankelijk van de werkbelasting, CPU-resources en het gewenste geheugen. Het is mogelijk dat de OLTP-workload gebruik maakt van de SKU's die zijn geoptimaliseerd voor de OLAP-workload. 

Twee verschillende hardwareklassen verdelen de SKU's in:

- S72, S72m, S96, S144, S144m, S192, S192m, S192xm, S224 en S224m, S224oo, S224om, S224ooo, S224oo, S224k worden de Type I-klasse van SKU's genoemd.
- Alle andere SKU's worden de 'Type II-klasse' van SKU's genoemd.
- Als u geïnteresseerd bent in SKU's die nog niet worden vermeld in de SAP-hardwaremap, neem dan contact op met uw Microsoft-account voor meer informatie. 


Er wordt niet uitsluitend een volledige HANA Large Instance-zegel toegewezen voor één klant&#39;gebruikt. Dit geldt ook voor de racks van reken- en opslagresources die zijn verbonden via een netwerk-fabric die in Azure is geïmplementeerd. De HANA Large Instance-infrastructuur, zoals Azure, implementeert verschillende tenants van klanten die op de volgende drie niveaus van elkaar &quot; &quot; zijn geïsoleerd:

- **Netwerk:** isolatie via virtuele netwerken binnen de HANA Large Instance-zegel.
- **Opslag:** isolatie via virtuele opslagmachines aan opslagvolumes die zijn toegewezen en het isoleren van opslagvolumes tussen tenants.
- **Compute:** toegewezen toewijzing van servereenheden aan één tenant. Geen harde of zachte partitionering van servereenheden. Er wordt geen enkele server of hosteenheid gedeeld tussen tenants. 

De implementaties van HANA Large Instance-eenheden tussen verschillende tenants zijn niet zichtbaar voor elkaar. HANA Large Instance-eenheden die in verschillende tenants zijn geïmplementeerd, kunnen niet rechtstreeks met elkaar communiceren op het ZEGELniveau van het grote HANA-exemplaar. Alleen HANA Large Instance-eenheden binnen één tenant kunnen met elkaar communiceren op het zegelniveau HANA Large Instance.

Een geïmplementeerde tenant in de zegel Grote instantie wordt voor factureringsdoeleinden toegewezen aan één Azure-abonnement. Voor een netwerk is het toegankelijk via virtuele netwerken van andere Azure-abonnementen binnen dezelfde Azure-inschrijving. Als u implementeert met een ander Azure-abonnement in dezelfde Azure-regio, kunt u ook vragen om een gescheiden HANA Large Instance-tenant.

Er zijn belangrijke verschillen tussen het uitvoeren SAP HANA op een grote HANA-instantie en SAP HANA worden uitgevoerd op VM's die zijn geïmplementeerd in Azure:

- Er is geen virtualisatielaag voor SAP HANA in Azure (Large Instances). U krijgt de prestaties van de onderliggende bare-metalhardware.
- In tegenstelling tot Azure is SAP HANA azure-server (Large Instances) toegewezen aan een specifieke klant. Het is niet mogelijk dat een servereenheid of host hard of soft gepart partitioneerd is. Als gevolg hiervan wordt een HANA Large Instance-eenheid als geheel aan een tenant en met die eenheid aan u toegewezen. Het opnieuw opstarten of afsluiten van de server leidt niet automatisch tot het besturingssysteem en SAP HANA wordt geïmplementeerd op een andere server. (Voor type I-klasse-SKU's is de enige uitzondering als een server problemen ondervindt en opnieuw moet worden uitgevoerd op een andere server.)
- In tegenstelling tot Azure, waarbij hostprocessortypen worden geselecteerd voor de beste prijs-prestatieverhouding, zijn de processortypen die zijn gekozen voor SAP HANA in Azure (large instances) de best presterende intel e7v3- en E7v4-processorlijn.

## <a name="next-steps"></a>Volgende stappen
- Raadpleeg [HLI-formaat.](hana-sizing.md)
