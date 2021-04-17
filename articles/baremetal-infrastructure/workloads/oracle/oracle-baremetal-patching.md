---
title: Patchoverwegingen voor BareMetal voor Oracle
description: Meer informatie over overwegingen voor besturingssysteem-/kernelpatching voor BareMetal Infrastructure voor Oracle.
ms.topic: reference
ms.subservice: workloads
ms.date: 04/14/2021
ms.openlocfilehash: 5af713c776def8af558531c84013b221a8d30087
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559017"
---
# <a name="patching-considerations-for-baremetal-for-oracle"></a>Patchoverwegingen voor BareMetal voor Oracle

In dit artikel kijken we naar belangrijke overwegingen voor besturingssysteem-/kernelpatching voor de BareMetal-infrastructuur voor Oracle.

Voor de juiste netwerkprestaties en systeemstabiliteit installeert u de besturingssysteemspecifieke versie van de eNIC- en fNIC-stuurprogramma's, zoals wordt weergegeven in de volgende compatibiliteitstabel. 

Servers worden geleverd aan klanten met compatibele versies. Tijdens het besturingssysteem (OS)/kernelpatching kunnen stuurprogramma's echter worden teruggedraaid naar de standaardversies van het stuurprogramma. Controleer daarom of de juiste stuurprogrammaversie de volgende patchbewerkingen voor het besturingssysteem of de kernel wordt uitgevoerd.

| Leverancier van het besturingssysteem | Versie van het besturingssysteempakket | Firmwareversie | eNIC-stuurprogramma | fNIC-stuurprogramma |
| --- | --- | --- | --- | --- |
| Red Hat | RHEL 7.6 | 3.2.3i | 2.3.0.53 | 1.6.0.34 |
| Red Hat | RHEL 7.6 | 4.1.1b | 2.3.0.53 | 1.6.0.34 |

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de Ethernet-configuratie van BareMetal voor Oracle.

> [!div class="nextstepaction"]
> [Ethernet-configuratie van BareMetal voor Oracle](oracle-baremetal-ethernet.md)

