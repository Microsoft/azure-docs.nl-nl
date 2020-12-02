---
title: Routerings voorkeur in azure
description: Meer informatie over hoe u kunt kiezen hoe uw verkeers routes tussen Azure en Internet met routerings voorkeur kunnen worden gekozen.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
Customer intent: As an Azure customer, I want to learn more about routing choices for my internet egress traffic.
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 3f5653e4c184e88bce86026681dde1aee9e14d49
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491414"
---
# <a name="what-is-routing-preference-preview"></a>Wat is routerings voorkeur (preview)?

Met de voor keuren voor Azure-route ring kunt u kiezen hoe uw verkeer tussen Azure en Internet wordt gerouteerd. U kunt ervoor kiezen om verkeer door te sturen via het micro soft-netwerk of via het ISP-netwerk (openbaar Internet). Deze opties worden ook respectievelijk aangeduid als *koude aardappel routering* en *warme aardappel routering* . De prijs van de uitgaande gegevens overdracht is afhankelijk van de selectie van de route ring. U kunt de routerings optie kiezen tijdens het maken van een openbaar IP-adres. Het open bare IP-adres kan worden gekoppeld aan resources, zoals virtuele machines, schaal sets voor virtuele machines, Internet gerichte load balancer, enzovoort. U kunt ook de routerings voorkeur instellen voor Azure storage-resources, zoals blobs, bestanden, Web en Azure DataLake. Verkeer wordt standaard gerouteerd via het wereld wijde netwerk van micro soft voor alle Azure-Services.

> [!IMPORTANT]
> De voor keuren voor route ring is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

## <a name="routing-via-microsoft-global-network"></a>Route ring via een wereld wijd netwerk van micro soft

Wanneer u uw verkeer via het *wereld wijde micro soft-netwerk* rondstuurt, wordt verkeer via een van de grootste netwerken op de wereldbol bezorgd met meer dan 160.000 mijl van glas met meer dan 165 Edge-aanwezigheid (pop). Het netwerk is goed ingericht met meerdere redundante glasvezel paden om te zorgen voor uitzonderlijk hoge betrouw baarheid en beschik baarheid. De verkeers techniek wordt beheerd door een software-gedefinieerde WAN-controller die ervoor zorgt dat er weinig latentie wordt geselecteerd voor uw verkeer en biedt de Premium-netwerk prestaties.

![Route ring via een wereld wijd netwerk van micro soft](media/routing-preference-overview/route-via-microsoft-global-network.png)

Binnenkomend **verkeer:** De wereld wijde BGP-aankondiging zorgt ervoor dat binnenkomend verkeer het micro soft-netwerk het dichtst bij de gebruiker komt. Als een gebruiker van Singapore bijvoorbeeld toegang heeft tot Azure-resources die worden gehost in Chicago, wordt het verkeer in het micro soft Global Network in de Singapore Edge-POP ingevoerd en verplaatst naar het micro soft-netwerk naar de service die wordt gehost in Chicago.

Uitgaand **verkeer:** Het uitgaande verkeer volgt hetzelfde principe. Verkeer reist het meren deel van de trajecten op het wereld wijde netwerk van micro soft en dicht bij de gebruiker. Als verkeer van Azure Chicago bijvoorbeeld bestemd is voor een gebruiker uit Singapore, reist het verkeer via het micro soft-netwerk van Chicago naar Singapore en sluit het het micro soft-netwerk in Singapore Edge POP.

Zowel binnenkomend als uitgaand verkeer blijft in bulk van de reis op het wereld wijde netwerk van micro soft. Dit wordt ook wel *koude aardappel routering* genoemd.


## <a name="routing-over-public-internet-isp-network"></a>Route ring via een openbaar Internet (ISP-netwerk)

De nieuwe routerings keuze *Internet routering* minimaliseert de reis op het wereld wijde netwerk van micro soft en maakt gebruik van het netwerk voor transit-isp's om uw verkeer te routeren. Deze routerings optie met kosten optimalisatie biedt netwerk prestaties die vergelijkbaar zijn met andere cloud providers.

![Route ring via het open bare Internet](media/routing-preference-overview/route-via-isp-network.png)

Binnenkomend **verkeer:** Het ingangs traject maakt gebruik van *Hot aardappel routering* , wat betekent dat verkeer het micro soft-netwerk binnenkomt dat het dichtst bij de regio van de gehoste service is. Bijvoorbeeld, als een gebruiker van Singapore toegang heeft tot Azure-resources die worden gehost in Chicago, loopt verkeer via het open bare Internet en voert het micro soft Global Network in Chicago in.

Uitgaand **verkeer:** Het uitgaande verkeer volgt hetzelfde principe. Verkeer verlaat het micro soft-netwerk in dezelfde regio waarin de service wordt gehost. Als verkeer van uw service in azure Chicago bijvoorbeeld bestemd is voor een gebruiker uit Singapore, sluit het verkeer het micro soft-netwerk in Chicago en wordt het open bare Internet naar de gebruiker in Singapore gezonden.

## <a name="supported-services"></a>Ondersteunde services

Openbaar IP-adres met voorkeurs instelling voor route ring ' micro soft Global Network ' kan worden gekoppeld aan elke Azure-service. Het open bare IP-adres met de **Voorkeurs keuze voor** route ring kan echter worden gekoppeld aan de volgende Azure-resources:

* Virtuele machine
* Schaalset voor virtuele machines
* Azure Kubernetes Service (AKS)
* Internet gerichte load balancer
* Application Gateway
* Azure Firewall

Voor opslag gebruiken primaire eind punten altijd het **wereld wijde netwerk van micro soft**. U kunt secundaire eind punten met **Internet** inschakelen als uw keuze voor verkeers routering. Ondersteunde opslag Services zijn:

* Blobs
* Files
* Web
* Azure DataLake

## <a name="pricing"></a>Prijzen
Het prijs verschil tussen beide opties wordt weer gegeven in de prijzen voor gegevens overdracht via internet. Route ring via de **wereld wijde** prijs van gegevens overdracht van micro soft is hetzelfde als de huidige prijs voor het uitbrengen van Internet. Ga naar de [pagina met prijzen voor Azure-band breedte](https://azure.microsoft.com/pricing/details/bandwidth/) voor de meest recente prijs informatie. 

## <a name="availability"></a>Beschikbaarheid

Ondersteuning voor route ring van voor keuren is beschikbaar in de volgende regio's voor services, zoals virtuele machines en Internet gerichte load balancer die gebruikmaken van een openbaar IP-adres voor Internet uitgaand verkeer: Europa-noord, Europa-west, Frankrijk-zuid, UK-zuid, VS-Oost, Noord-Centraal VS, Zuid-Centraal VS, VS-West, West-Centraal VS, Azië en Japan-West.

Ondersteuning voor route ring van het opslag account is beschikbaar in de volgende Azure-regio's: Noord-Centraal VS, VS-West-Centraal, Zuid-Centraal VS, Oost-VS, VS-West, Europa-noord, Frankrijk-zuid, Duitsland-west-centraal, Zwitserland-west, Zuid-Azië-oost, Japan-Oost en Japan-West.
## <a name="limitations"></a>Beperkingen

* Routerings voorkeur is alleen compatibel met de standaard-SKU van het open bare IP-adres. De basis-SKU van het open bare IP-adres wordt niet ondersteund.
* Routerings voorkeur ondersteunt momenteel alleen open bare IPv4-IP-adressen. Open bare IPv6-adressen worden niet ondersteund.
* Virtuele machines met meerdere Nic's kunnen slechts één type routerings voorkeur hebben.


## <a name="next-steps"></a>Volgende stappen

* [Routerings voorkeur configureren voor een virtuele machine met behulp van de Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md)
* [Routerings voorkeur configureren voor een virtuele machine met behulp van Azure CLI](configure-routing-preference-virtual-machine-cli.md)
