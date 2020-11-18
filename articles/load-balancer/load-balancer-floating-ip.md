---
title: Zwevende IP-configuratie Azure Load Balancer
description: Overzicht van Azure Load Balancer zwevende IP-adressen
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
ms.openlocfilehash: 01cca2f2233ed5cdfb3003bb44c40f481bcf9bda
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94699403"
---
# <a name="azure-load-balancer-floating-ip-configuration"></a>Zwevende IP-configuratie Azure Load Balancer

Load Balancer biedt verschillende mogelijkheden voor zowel UDP- als TCP-toepassingen.

## <a name="floating-ip"></a>Zwevend IP-adres

In sommige toepassingsscenario's is het raadzaam of vereist dat u dezelfde poort gebruikt voor meerdere toepassingsexemplaren op één virtuele machine in de back-endpool. Veelvoorkomende voorbeelden waarin een poort opnieuw wordt gebruikt, zijn clustering voor hoge beschikbaarheid, virtuele netwerkapparaten en meerdere TLS-eindpunten beschikbaar maken zonder herversleuteling. Als u de back-endpoort voor meerdere regels opnieuw wilt gebruiken, moet u Zwevend IP inschakelen in de regeldefinitie.

**Zwevend IP** is in de terminologie van Azure een gedeelte van wat Direct Server Return (DSR) wordt genoemd. DSR bestaat uit twee delen:

- Een stroomtopologie
- Een toewijzingsschema voor IP-adressen

Op platformniveau werkt Azure Load Balancer altijd in een DSR-stroomtopologie, ongeacht of Zwevend IP is ingeschakeld of niet. Dit betekent dat het uitgaande deel van een stroom altijd correct wordt herschreven, zodat deze direct naar de oorsprong terugstroomt.
Zonder Zwevend IP wordt een traditionele toewijzingsschema van IP-adressen voor een taakverdeling weergegeven voor gebruiksgemak (het IP van VM-exemplaren). Als Zwevend IP wordt ingeschakeld, wordt de toewijzing van IP-adressen gewijzigd in het frontend-IP van de load balancer om extra flexibiliteit te bieden. Klik [hier](load-balancer-multivip-overview.md) voor meer informatie.

## <a name="limitations"></a><a name = "limitations"></a>Beperkingen

- Zwevend IP wordt momenteel niet ondersteund voor secundaire IP-configuraties voor scenario's voor taak verdeling

## <a name="next-steps"></a>Volgende stappen

- Zie [Een openbare Standard Load Balancer maken](quickstart-load-balancer-standard-public-portal.md) om aan de slag te gaan met een Load Balancer.
- Meer informatie over [Uitgaande verbindingen voor Azure Load Balancer](load-balancer-outbound-connections.md).
- Meer informatie over [Azure Load Balancer](load-balancer-overview.md).
- Meer informatie over [Statuscontroles](load-balancer-custom-probe-overview.md).
- Meer informatie over [Diagnostische tests van Standard Load Balancer](load-balancer-standard-diagnostics.md).
- Meer informatie over [Netwerkbeveiligingsgroepen](../virtual-network/network-security-groups-overview.md).