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
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.custom: references_regions
ms.openlocfilehash: b0235286260910a45523e3236e7ed3a114eaf57f
ms.sourcegitcommit: 8c93b05c27c7e8a5ba62a4d6fc6fc4d0c3980a21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2021
ms.locfileid: "101601330"
---
# <a name="what-is-routing-preference"></a>Wat is een routeringsvoorkeur?

Met de voor keuren voor Azure-route ring kunt u kiezen hoe uw verkeer tussen Azure en Internet wordt gerouteerd. U kunt ervoor kiezen om verkeer door te sturen via het micro soft-netwerk of via het ISP-netwerk (openbaar Internet). Deze opties worden ook respectievelijk aangeduid als *koude aardappel routering* en *warme aardappel routering* . De prijs van de uitgaande gegevens overdracht is afhankelijk van de selectie van de route ring. U kunt de routerings optie kiezen tijdens het maken van een openbaar IP-adres. Het open bare IP-adres kan worden gekoppeld aan resources, zoals virtuele machines, schaal sets voor virtuele machines, Internet gerichte load balancer, enzovoort. U kunt ook de routerings voorkeur instellen voor Azure storage-resources, zoals blobs, bestanden, Web en Azure DataLake. Verkeer wordt standaard gerouteerd via het wereld wijde netwerk van micro soft voor alle Azure-Services.

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
* Bestanden
* Web
* Azure DataLake

## <a name="pricing"></a>Prijzen
Het prijs verschil tussen beide opties wordt weer gegeven in de prijzen voor gegevens overdracht via internet. Route ring via de **wereld wijde** prijs van gegevens overdracht van micro soft is hetzelfde als de huidige prijs voor het uitbrengen van Internet. Ga naar de [pagina met prijzen voor Azure-band breedte](https://azure.microsoft.com/pricing/details/bandwidth/) voor de meest recente prijs informatie.

## <a name="limitations"></a>Beperkingen

* Routerings voorkeur wordt momenteel niet ondersteund in Australië-centraal, Australië Central2, Canada-oost, Brazilië-zuid, Korea-centraal en Korea-zuid.
* Routerings voorkeur is alleen compatibel met een zone-redundante standaard-SKU van een openbaar IP-adres. De basis-SKU van het open bare IP-adres wordt niet ondersteund.
* Routerings voorkeur ondersteunt momenteel alleen open bare IPv4-IP-adressen. Open bare IPv6-adressen worden niet ondersteund.


## <a name="next-steps"></a>Volgende stappen 

* [Routerings voorkeur configureren voor een virtuele machine met behulp van de Azure PowerShell](configure-routing-preference-virtual-machine-powershell.md)
* [Routerings voorkeur configureren voor een virtuele machine met behulp van Azure CLI](configure-routing-preference-virtual-machine-cli.md)
