---
title: BareMetal-SKU's voor Oracle-workloads
description: Meer informatie over de SKU's voor de Oracle BareMetal Infrastructure-workloads.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/15/2021
ms.openlocfilehash: 42ff26b9ea9bcc022f1ddbf3dddcb041b2cea3a2
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588850"
---
# <a name="baremetal-skus-for-oracle-workloads"></a>BareMetal-SKU's voor Oracle-workloads

In dit artikel bekijken we gespecialiseerde SKU's voor BareMetal-infrastructuur voor Oracle-workloads.

BareMetal-infrastructuur voor Oracle-SKU's variëren van twee sockets tot maximaal vier sockets. U kunt ook kiezen uit verschillende CPU-kernen en geheugengrootten om te voldoen aan de vereisten van uw workload. Hier is een tabel met een overzicht van de functies van beschikbare SKU's.
 
| **Door Oracle gecertificeerde**  **hardware** | **Model** | **Totaal geheugen** | **Storage** | **Beschikbaarheid** |
| --- | --- | --- | --- | --- |
| JA | SAP HANA op Azure S32m- 2 x Intel® Xeon® I6234 Processor 16 CPU-kernen en 32 CPU-threads | 1,5 TB | --- | Beschikbaar |
| JA | SAP HANA op Azure S64m- 4 x Intel® Xeon® I6234 Processor 32 CPU-kernen en 64 CPU-threads | 3,0 TB | --- | Beschikbaar |
| JA | SAP HANA Azure S96: 2 x Intel® Xeon® E7-8890 v4 Processor 48 CPU-kernen en 96 CPU-threads | 768 GB | 3,0 TB | Beschikbaar |
| JA | SAP HANA Azure S224 – 4 x Intel® Xeon® De 8276-processor 112 CPU-kernen en 224 CPU-threads | 3,0 TB | 6,3 TB | Beschikbaar |
| JA | SAP HANA azure S224m: 4 x Intel® Xeon® Processor 8276 processor 112 CPU-kernen en 224 CPU-threads | 6,0 TB | 10,5 TB | Beschikbaar |

- CPU-kernen = som van niet-hyperthreaded CPU-kernen (het totale aantal fysieke processors) van de servereenheid. 
- CPU-threads = som van rekenthreads geleverd door hyperthreaded CPU-kernen (het totale aantal logische processors) van de servereenheid. De meeste eenheden zijn standaard geconfigureerd voor het gebruik van Hyper-Threading Technologie.
- Servers zijn toegewezen aan klanten.
- De klant heeft toegang tot de hoofdmap (geen hypervisor).
- Servers staan niet rechtstreeks op Azure VT's.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de opslag die wordt aangeboden door BareMetal Infrastructure for Oracle.

> [!div class="nextstepaction"]
> [Opslag op BareMetal voor Oracle-workloads](oracle-baremetal-storage.md)
