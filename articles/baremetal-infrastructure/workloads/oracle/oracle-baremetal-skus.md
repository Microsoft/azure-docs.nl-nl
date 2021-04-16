---
title: BareMetal-SKU's voor Oracle-workloads
description: Meer informatie over de SKU's voor de workloads van de Oracle BareMetal-infrastructuur.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 2482f8ed682a982ee3c8c4907e21b4e6c6ebbb4f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559015"
---
# <a name="baremetal-skus-for-oracle-workloads"></a>BareMetal-SKU's voor Oracle-workloads

In dit artikel bekijken we gespecialiseerde BareMetal Infrastructure-SKU's voor Oracle-workloads.

BareMetal-infrastructuur voor Oracle-SKU's variëren van twee sockets tot maximaal vier sockets. U kunt ook kiezen uit verschillende CPU-kernen en geheugengrootten om te voldoen aan de vereisten van uw workload. Hier is een tabel met een overzicht van de functies van beschikbare SKU's.
 
| **Oracle**  **Certified-hardware** | **Model** | **Totaal geheugen** | **Storage** | **Beschikbaarheid** |
| --- | --- | --- | --- | --- |
| JA | SAP HANA op Azure S32m- 2 x Intel® Xeon® Processor I623416 CPU-kernen en 32 CPU-threads | 1,5 TB | --- | Beschikbaar |
| JA | SAP HANA op Azure S64m- 4 x Intel® Xeon® Processor I623432 CPU-kernen en 64 CPU-threads | 3,0 TB | --- | Beschikbaar |
| JA | SAP HANA op Azure S96: 2 x Intel® Xeon® Processor E7-8890 v448 CPU-kernen en 96 CPU-threads | 768 GB | 3,0 TB | Beschikbaar |
| JA | SAP HANA op Azure S224 – 4 x Intel® Xeon® De 8276-processor 112 CPU-kernen en 224 CPU-threads | 3,0 TB | 6,3 TB | Beschikbaar |
| JA | SAP HANA op Azure S224m– 4 x Intel® Xeon® De 8276 processor 112 CPU-kernen en 224 CPU-threads | 6,0 TB | 10,5 TB | Beschikbaar |

- CPU-kernen = som van niet-hyperthreaded CPU-kernen (het totale aantal fysieke processors) van de servereenheid. 
- CPU-threads = som van rekenthreads geleverd door hyperthreaded CPU-kernen (het totale aantal logische processors) van de servereenheid. De meeste eenheden zijn standaard geconfigureerd voor het gebruik van Hyper-Threading Technologie.
- Servers zijn toegewezen aan klanten.
- De klant heeft toegang tot de hoofdmap (geen hypervisor).
- Servers staan niet rechtstreeks op Azure VT's.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de opslag die wordt aangeboden door BareMetal Infrastructure for Oracle.

> [!div class="nextstepaction"]
> [Opslag op BareMetal voor Oracle-workloads](oracle-baremetal-storage.md)
