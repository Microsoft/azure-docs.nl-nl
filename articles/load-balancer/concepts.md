---
title: Azure Load Balancer-concepten
description: Een overzicht van Azure Load Balancer-concepten
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/13/2020
ms.author: allensu
ms.openlocfilehash: 3f8c288f950f34e1764c50e8eb74a8a73b39b3d7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94698526"
---
# <a name="azure-load-balancer-algorithm"></a>Algoritme voor Azure Load Balancer

Load Balancer biedt verschillende mogelijkheden voor zowel UDP- als TCP-toepassingen.

## <a name="load-balancing-algorithm"></a>Algoritme voor taakverdeling

U kunt een taakverdelingsregel maken om verkeer van de front-end naar een back-endpool te distribueren. Azure Load Balancer gebruikt een hash-algoritme voor de distributie van binnenkomende stromen (niet bytes). Load Balancer herschrijft de headers van stromen naar instanties van back-endpools. Er is een server beschikbaar voor het ontvangen van nieuwe stromen als een statustest aangeeft dat het back-endeindpunt in orde is.

Standaard gebruikt Load Balancer een hash van vijf tuples.

De hash bevat:

- **IP-adres van bron**
- **Bronpoort**
- **IP-adres van doel**
- **Doelpoort**
- **IP-protocolnummer voor het toewijzen van stromen aan beschikbare servers**

Affiniteit met een IP-bronadres wordt gemaakt met behulp van een hash met twee of drie tuples. Pakketten van dezelfde stroom komen binnen op dezelfde instantie achter de front-end waarop taakverdeling wordt toegepast.

Wanneer de client een nieuwe stroom initieert vanaf hetzelfde bron-IP, wordt de bronpoort gewijzigd. Als gevolg hiervan kan de hash van vijf tuples ertoe leiden dat het verkeer naar een ander back-endeindpunt gaat.
Zie [De distributiemodus configureren voor Azure Load Balancer](./load-balancer-distribution-mode.md) voor meer informatie.

Op de volgende afbeelding wordt een hash-distributiepunt weergegeven:

![Hash-distributie](./media/load-balancer-overview/load-balancer-distribution.png)

*Afbeelding: Hash-distributie*

## <a name="application-independence-and-transparency"></a>Toepassingsonafhankelijkheid en -transparantie

Load Balancer communiceert niet rechtstreeks met TCP of UDP of met de toepassingslaag. Elk TCP- of UDP-toepassingsscenario kan worden ondersteund. Load Balancer sluit of start geen stromen en communiceert ook niet met de nettolading van de stroom. Load Balancer biedt geen gatewayfunctionaliteit voor de toepassingslaag. Protocol-handshakes worden altijd direct uitgevoerd tussen de client en de instantie van de back-endpool. Een reactie op een inkomende stroom is altijd een reactie van een virtuele machine. Als de stroom op de virtuele machine aankomt, blijft het oorspronkelijke bron-IP-adres ook behouden.

- Elk eindpunt wordt beantwoord door een virtuele machine. Zo treedt een TCP-handshake bijvoorbeeld op tussen de client en de geselecteerde back-end-VM. Een reactie op een aanvraag naar een front-end is een antwoord dat is gegenereerd door een back-end-VM. Wanneer u de verbinding met een front-end valideert, valideert u de algehele connectiviteit met ten minste één back-end-VM.
- Toepassingsnettoladingen zijn transparant voor de load balancer. Elke UDP- of TCP-toepassing kan worden ondersteund.
- Omdat de load balancer geen interactie heeft met de TCP-nettolading en TLS-offload levert, kunt u uitgebreide versleutelde scenario's maken. Door load balancer te gebruiken, wordt grote scale-out voor TLS-toepassingen mogelijk door de TLS-verbinding op de VM zelf te beëindigen. De sleutelcapaciteit van de TLS-sessie wordt bijvoorbeeld alleen beperkt door het type en het aantal VM's dat u aan de back-endpool toevoegt.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de [onderdelen](components.md) waaruit Azure Load Balancer is opgebouwd.
- Zie [Een openbare Standard Load Balancer maken](quickstart-load-balancer-standard-public-portal.md) om een Load balancer te gaan gebruiken: maak eerst een, maak VM's met daarop een aangepaste IIS-extensie geïnstalleerd en pas taakverdeling toe op de web-app tussen de VM's.
- Meer informatie over [Uitgaande verbindingen voor Azure Load Balancer](load-balancer-outbound-connections.md).
- Meer informatie over [Azure Load Balancer](load-balancer-overview.md).
- Meer informatie over [Statuscontroles](load-balancer-custom-probe-overview.md).
- Meer informatie over [Diagnostische tests van Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Meer informatie over [Netwerkbeveiligingsgroepen](../virtual-network/network-security-groups-overview.md).