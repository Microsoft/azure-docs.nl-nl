---
title: Groottes van virtuele Azure-machines voor veld-Programmeer bare poort matrices (FPGA)
description: Geeft een lijst van de verschillende geoptimaliseerde FPGA-grootten die beschikbaar zijn voor virtuele machines in Azure. Bevat informatie over het aantal Vcpu's, gegevens schijven en Nic's en de opslag doorvoer en netwerk bandbreedte voor grootten in deze serie.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-fpga
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: vikancha
ms.openlocfilehash: d9eb0d5bc93cbe9c2a7cbae56c336115bb227b04
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102557672"
---
# <a name="fpga-optimized-virtual-machine-sizes"></a>FPGA geoptimaliseerde grootte van virtuele machines

FPGA geoptimaliseerde VM-grootten zijn gespecialiseerde virtuele machines die beschikbaar zijn met één of meerdere Fpga's. Deze grootten zijn ontworpen voor computerintensieve werk belastingen. Dit artikel bevat informatie over het aantal en het type van Fpga's, Vcpu's, gegevens schijven en Nic's. Opslag doorvoer en netwerk bandbreedte worden ook voor elke grootte in deze groepering opgenomen.

- De grootte van de [NP-serie](np-series.md) is geoptimaliseerd voor werk belastingen, zoals machine learning interferentie, video transcode ring en zoek-& analyses in data bases. De NP-serie wordt aangedreven door Xilinx U250-Accelerators.


## <a name="deployment-considerations"></a>Overwegingen bij de implementatie

- Zie [producten beschikbaar per regio](https://azure.microsoft.com/regions/services/)voor de beschik baarheid van virtuele machines uit de N-serie.

- Vm's uit de N-serie kunnen alleen worden geïmplementeerd in het Resource Manager-implementatie model.

- Als u meer dan een paar virtuele machines uit de N-serie wilt implementeren, kunt u een abonnement op basis van betalen naar gebruik of andere aankoop opties overwegen. Als u een [gratis account van Azure](https://azure.microsoft.com/free/) gebruikt, kunt u slechts een paar Azure Compute-resources van Azure gebruiken.

- Mogelijk moet u het quotum voor kernen (per regio) in uw Azure-abonnement verhogen en het afzonderlijke quotum voor NP-kernen verhogen. Als u een quotum toename wilt aanvragen, opent u gratis [een aanvraag voor een online klant ondersteuning](../azure-portal/supportability/how-to-create-azure-support-request.md) . De standaard limieten kunnen variëren, afhankelijk van de categorie van uw abonnement.

## <a name="other-sizes"></a>Andere grootten

- [Algemeen gebruik](sizes-general.md)
- [Geoptimaliseerde rekenkracht](sizes-compute.md)
- [Versneld GPU-reken kracht](sizes-gpu.md)
- [Krachtig rekenvermogen](sizes-hpc.md)
- [Geoptimaliseerd voor geheugen](sizes-memory.md)
- [Geoptimaliseerd voor opslag](sizes-storage.md)
- [Vorige generaties](sizes-previous-gen.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe [Azure Compute units (ACU)](acu.md) u kan helpen bij het vergelijken van de reken prestaties in azure-sku's.
