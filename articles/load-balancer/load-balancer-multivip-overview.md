---
title: Meerdere frontends-Azure Load Balancer
description: Met dit leer traject gaat u aan de slag met een overzicht van meerdere frontends op Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 5c2072d13cab9839a276c0437747d7075918e78a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94696877"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Meerdere frontends voor Azure Load Balancer

Met Azure Load Balancer kunt u service verdeling op meerdere poorten, meerdere IP-adressen of beide. U kunt open bare en interne load balancer definities gebruiken om gelijkmatige verdeling van stromen over een set Vm's.

In dit artikel worden de grond beginselen van deze mogelijkheid, belang rijke concepten en beperkingen beschreven. Als u alleen services wilt weer geven op één IP-adres, kunt u eenvoudige instructies vinden voor [open bare](./quickstart-load-balancer-standard-public-portal.md) of [interne](./quickstart-load-balancer-standard-internal-portal.md) Load Balancer configuraties. Het toevoegen van meerdere frontends is incrementeel naar een configuratie met één frontend. Aan de hand van de concepten in dit artikel kunt u op elk gewenst moment een vereenvoudigde configuratie uitbreiden.

Wanneer u een Azure Load Balancer definieert, zijn een front-end-en een back-end-pool configuratie verbonden met regels. De status test waarnaar wordt verwezen door de regel wordt gebruikt om te bepalen hoe nieuwe stromen worden verzonden naar een knoop punt in de back-end-pool. Het front-end (ook wel VIP) wordt gedefinieerd door een 3-tuple die bestaat uit een IP-adres (openbaar of intern), een transport protocol (UDP of TCP) en een poort nummer van de taakverdelings regel. De back-end-pool is een verzameling virtuele-machine-IP-configuraties (onderdeel van de NIC-resource) die verwijzen naar de Load Balancer back-end-groep.

De volgende tabel bevat enkele voor beelden van frontend-configuraties:

| Front-end | IP-adres | protocol | poort |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

De tabel bevat vier verschillende frontends. Front-end-#1, #2 en #3 zijn één frontend met meerdere regels. Hetzelfde IP-adres wordt gebruikt, maar de poort of het protocol verschilt voor elke front-end. Front-ends #1 en #4 zijn een voor beeld van meerdere front-ends, waarbij hetzelfde frontend-protocol en dezelfde poort opnieuw worden gebruikt voor meerdere frontends.

Azure Load Balancer biedt flexibiliteit bij het definiëren van de taakverdelings regels. Een regel declareert hoe een adres en poort op de front-end wordt toegewezen aan het doel adres en de poort op de back-end. Of de back-end-poorten opnieuw worden gebruikt tussen regels is afhankelijk van het type regel. Elk type regel heeft specifieke vereisten die van invloed kunnen zijn op de configuratie van de host en het testen van het ontwerp. Er zijn twee soorten regels:

1. De standaard regel zonder het opnieuw gebruiken van de backend-poort
2. De zwevende IP-regel waarbij de back-endservers opnieuw worden gebruikt

Met Azure Load Balancer kunt u beide regel typen op dezelfde load balancer configuratie combi neren. De load balancer kan deze gelijktijdig gebruiken voor een bepaalde virtuele machine of een combi natie hiervan, zolang u de beperkingen van de regel onderhoudt. Welk type regel u kiest, is afhankelijk van de vereisten van uw toepassing en de complexiteit van de ondersteuning van die configuratie. U moet evalueren welke regel typen het meest geschikt zijn voor uw scenario.

We verkennen deze scenario's nog verder door te beginnen met het standaard gedrag.

## <a name="rule-type-1-no-backend-port-reuse"></a>Regel type #1: geen backend-poort opnieuw gebruiken

![Meerdere frontend-afbeelding met groene en paarse front-end](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

In dit scenario worden de front-ends als volgt geconfigureerd:

| Front-end | IP-adres | protocol | poort |
| --- | --- | --- | --- |
| ![groen front-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![paarse front-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

De DIP is het doel van de binnenkomende stroom. In de back-end-pool geeft elke VM de gewenste service op een unieke poort op een DIP. Deze service wordt via een regel definitie aan het frontend gekoppeld.

We definiëren twee regels:

| Regel | Kaart-front-end | Naar back-end-groep |
| --- | --- | --- |
| 1 |![groen front-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![groene back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![groene back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2:80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![paarse back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![paarse back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2:81 |

De volledige toewijzing in Azure Load Balancer is nu als volgt:

| Regel | IP-adres voor front-end | protocol | poort | Doel | poort |
| --- | --- | --- | --- | --- | --- |
| ![groene regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |IP-adres van DIP |80 |
| ![paarse regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |IP-adres van DIP |81 |

Elke regel moet een stroom maken met een unieke combi natie van het doel-IP-adres en de doel poort. Door de bestemmings poort van de stroom te variëren, kunnen meerdere regels stromen naar hetzelfde DIP leveren op verschillende poorten.

Status controles worden altijd omgeleid naar het DIP van een virtuele machine. U moet ervoor zorgen dat de test de status van de virtuele machine weergeeft.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Regel type #2: gebruik van een back-end-poort met zwevend IP

Azure Load Balancer biedt de flexibiliteit voor het opnieuw gebruiken van de frontend-poort op meerdere frontends, ongeacht het gebruikte regel type. Daarnaast hebben sommige toepassings scenario's de voor keur of vereist dat dezelfde poort wordt gebruikt door meerdere exemplaren van de toepassing op één virtuele machine in de back-endadresgroep. Veelvoorkomende voorbeelden waarin een poort opnieuw wordt gebruikt, zijn clustering voor hoge beschikbaarheid, virtuele netwerkapparaten en meerdere TLS-eindpunten beschikbaar maken zonder herversleuteling.

Als u de back-endpoort voor meerdere regels opnieuw wilt gebruiken, moet u Zwevend IP inschakelen in de regeldefinitie.

' Zwevend IP ' is de terminologie van Azure voor een deel van wat direct server Return (DSR) wordt genoemd. DSR bestaat uit twee delen: een stroom topologie en een schema voor het toewijzen van IP-adressen. Op platformniveau werkt Azure Load Balancer altijd in een DSR-stroomtopologie, ongeacht of Zwevend IP is ingeschakeld of niet. Dit betekent dat het uitgaande deel van een stroom altijd correct wordt herschreven, zodat deze direct naar de oorsprong terugstroomt.

Met het standaard regel type biedt Azure een traditioneel schema voor IP-adres toewijzing voor taak verdeling voor gebruiks gemak. Als zwevende IP wordt ingeschakeld, wordt het schema voor IP-adres toewijzing gewijzigd zodat er meer flexibiliteit is, zoals hieronder wordt uitgelegd.

In het volgende diagram ziet u deze configuratie:

![Meerdere frontend-afbeelding met groene en paarse frontend met DSR](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Voor dit scenario heeft elke VM in de back-end-pool drie netwerk interfaces:

* DIP: een virtuele NIC die is gekoppeld aan de virtuele machine (IP-configuratie van de NIC-resource van Azure)
* Front-end 1: een loop back-interface in het gast besturingssysteem dat is geconfigureerd met het IP-adres van de frontend 1
* Front-end 2: een loop back-interface in het gast besturingssysteem dat is geconfigureerd met het IP-adres van de frontend 2

Voor elke virtuele machine in de back-endadresgroep voert u de volgende opdrachten uit vanaf een Windows-opdracht prompt.

Als u de lijst met interface namen op uw virtuele machine wilt ophalen, typt u de volgende opdracht:

```console
netsh interface show interface 
```

Voor de VM-NIC (beheerd door Azure) typt u deze opdracht:

```console
netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled
```

(Vervang InterfaceName door de naam van deze interface)

Herhaal deze opdrachten voor elke loop back-interface die u hebt toegevoegd:

```console
netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled 
```

(Vervang InterfaceName door de naam van deze loop back-Interface)

```console
netsh interface ipv4 set interface “interfacename” weakhostsend=enabled 
```

(Vervang InterfaceName door de naam van deze loop back-Interface)

> [!IMPORTANT]
> De configuratie van de loop back-interfaces wordt uitgevoerd in het gast besturingssysteem. Deze configuratie wordt niet uitgevoerd of wordt niet beheerd door Azure. Zonder deze configuratie werken de regels niet. Voor de definities van de status tests wordt de DIP van de virtuele machine gebruikt in plaats van de loop back-interface die het DSR-frontend vertegenwoordigt. Daarom moet uw service test reacties geven op een DIP-poort die de status weerspiegelt van de service die wordt aangeboden op de loop back-interface die het DSR-frontend vertegenwoordigt.


Laten we uitgaan van dezelfde frontend-configuratie als in het vorige scenario:

| Front-end | IP-adres | protocol | poort |
| --- | --- | --- | --- |
| ![groen front-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![paarse front-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

We definiëren twee regels:

| Regel | Front-end | Toewijzen aan back-end-groep |
| --- | --- | --- |
| 1 |![groene regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![groene back-end](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (in VM1 en VM2) |
| 2 |![paarse regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![paarse back-end](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (in VM1 en VM2) |

De volgende tabel bevat de volledige toewijzing in de load balancer:

| Regel | IP-adres voor front-end | protocol | poort | Doel | poort |
| --- | --- | --- | --- | --- | --- |
| ![groene regel](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |hetzelfde als frontend (65.52.0.1) |hetzelfde als frontend (80) |
| ![paarse regel](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |hetzelfde als frontend (65.52.0.2) |hetzelfde als frontend (80) |

Het doel van de binnenkomende stroom is het frontend-IP-adres op de loop back-interface in de virtuele machine. Elke regel moet een stroom maken met een unieke combi natie van het doel-IP-adres en de doel poort. Door het doel-IP-adres van de stroom te variëren, is het gebruik van de poort mogelijk op dezelfde VM. Uw service wordt blootgesteld aan de load balancer door deze te binden aan het IP-adres en de poort van de respectieve loop back-interface.

U ziet dat in dit voor beeld de doel poort niet wordt gewijzigd. Hoewel dit een zwevend IP-scenario is, biedt Azure Load Balancer ook ondersteuning voor het definiëren van een regel voor het herschrijven van de back-end-doel poort en om deze te onderscheiden van de front-end-doel poort.

Het type zwevende IP-regel is de basis van verschillende load balancer configuratie patronen. Een voor beeld dat momenteel beschikbaar is, is de [SQL AlwaysOn met meerdere listeners](../azure-sql/virtual-machines/windows/availability-group-listener-powershell-configure.md) configuratie. Na verloop van tijd worden er meer van deze scenario's gedocumenteerd.

## <a name="limitations"></a>Beperkingen

* Meerdere frontend-configuraties worden alleen ondersteund met IaaS-Vm's.
* Met de zwevende IP-regel moet uw toepassing gebruikmaken van de primaire IP-configuratie voor uitgaande SNAT-stromen. Als uw toepassing wordt gebonden aan het frontend-IP-adres dat is geconfigureerd op de loop back-interface in het gast besturingssysteem, is de uitgaande SNAT van Azure niet beschikbaar voor het herschrijven van de uitgaande stroom en mislukt de stroom.  Bekijk [uitgaande scenario's](load-balancer-outbound-connections.md).
* Zwevend IP wordt momenteel niet ondersteund voor secundaire IP-configuraties voor scenario's met interne taakverdeling.
* Open bare IP-adressen zijn van invloed op de facturering. Zie [IP-adres prijzen](https://azure.microsoft.com/pricing/details/ip-addresses/) voor meer informatie.
* De abonnements limieten zijn van toepassing. Zie [service limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Bekijk [uitgaande verbindingen](load-balancer-outbound-connections.md) om inzicht te krijgen in de gevolgen van meerdere frontends voor het gedrag van uitgaande verbindingen.