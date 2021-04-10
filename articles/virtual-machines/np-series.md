---
title: NP-serie-Azure Virtual Machines
description: Specificaties voor de virtuele machines in de NP-serie.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: vikancha
ms.openlocfilehash: 09adb19623ea866091e1b949e78263661eddbb52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551144"
---
# <a name="np-series-preview"></a>NP-serie (preview-versie) 
De virtuele machines van de NP-serie worden aangedreven door [Xilinx U250 ](https://www.xilinx.com/products/boards-and-kits/alveo/u250.html) fpga's voor het versnellen van werk belastingen, zoals machine learning interferentie, video transcode ring en zoek & analyses in data bases. Virtuele machines uit de NP-serie worden ook aangedreven door Cpu's van de Intel Xeon 8171M (Skylake) met alle core Turbo clock-snelheid van 3,2 GHz.

Dien een aanvraag in met behulp van het [voorbeeld formulier](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR9x_QCQkJXxHl4qOI4jC9YtUOVI0VkgwVjhaTFFQMTVBTDFJVFpBMzJSSCQlQCN0PWcu) als onderdeel van het NP-serie voorbeeld programma.


[Premium Storage](premium-storage-performance.md): ondersteund<br>
[Premium Storage caching](premium-storage-performance.md): ondersteund<br>
[Livemigratie](maintenance-and-updates.md): niet ondersteund<br>
[Updates](maintenance-and-updates.md)voor het behouden van geheugen: niet ondersteund<br>
Ondersteuning voor het genereren van VM'S: generatie 1<br>
[Versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md): ondersteund<br>
[Tijdelijke besturingssysteem schijven](ephemeral-os-disks.md): niet ondersteund <br>
<br>

| Grootte | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | FPGA | FPGA-geheugen: GiB | Max. aantal gegevensschijven | Maximum aantal Nic's/verwachte netwerk bandbreedte (MBps) | 
|---|---|---|---|---|---|---|---|
| Standard_NP10s | 10 | 168 | 736  | 1 | 64  | 8 | 1 / 7500 | 
| Standard_NP20s | 20 | 336 | 1474 | 2 | 128 | 16 | 2 / 15000 | 
| Standard_NP40s | 40 | 672 | 2948 | 4 | 256 | 32 | 4 / 30000 | 



[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Ondersteunde besturingssystemen en stuurprogramma’s
Ga naar de [release opmerkingen van Xilinx runtime (XRT)](https://www.xilinx.com/support/documentation/sw_manuals/xilinx2020_2/ug1451-xrt-release-notes.pdf) voor een volledige lijst met ondersteunde besturings systemen.

Tijdens de preview-versie van Microsoft Azure engineering-teams delen specifieke instructies voor de installatie van Stuur Programma's.

## <a name="other-sizes"></a>Andere grootten

- [Algemeen gebruik](sizes-general.md)
- [Geoptimaliseerd voor geheugen](sizes-memory.md)
- [Geoptimaliseerd voor opslag](sizes-storage.md)
- [Geoptimaliseerde GPU](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
