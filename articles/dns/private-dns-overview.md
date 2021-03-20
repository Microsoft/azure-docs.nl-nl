---
title: Wat is privé-DNS in Azure?
description: In dit artikel gaat u aan de slag met een overzicht van de privé-DNS-hostingservice op Microsoft Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 6/12/2019
ms.author: rohink
ms.openlocfilehash: 540c4d6f2dde64cef9b5795d635ac6259bfc69c3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87985628"
---
# <a name="what-is-azure-private-dns"></a>Wat is privé-DNS in Azure?

Het Domain Name System, of DNS, is verantwoordelijk voor het omzetten van een servicenaam naar het bijbehorende IP-adres.  Azure DNS is een service voor het hosten van DNS-domeinen die naamomzetting verzorgt met behulp van de Microsoft Azure-infrastructuur. Naast de ondersteuning van internetgerichte DNS-domeinen ondersteunt Azure DNS ook privé-DNS-zones.

Azure Privé-DNS biedt een betrouwbare, veilige DNS-service voor het beheren en omzetten van domeinnamen in een virtueel netwerk zonder dat u een aangepaste DNS-oplossing hoeft toe te voegen. Met behulp van privé-DNS-zones kunt u uw eigen aangepaste domeinnamen gebruiken in plaats van de door Azure geleverde namen die momenteel beschikbaar zijn. Met aangepaste domeinnamen kunt u uw virtuele netwerkarchitectuur aanpassen aan de behoeften van uw organisatie. Het biedt naamomzetting voor virtuele machines (VM's) binnen één of verschillende virtuele netwerken. Daarnaast kunt u zonenamen configureren met een split-horizon-weergave, waardoor u de naam kunt delen in een privé-DNS-zone en een openbare DNS-zone.

Als u de records van een privé-DNS-zone wilt omzetten vanuit uw virtuele netwerk, moet u het virtuele netwerk koppelen aan de zone. Gekoppelde virtuele netwerken hebben volledige toegang en kunnen alle in de privézone gepubliceerde DNS-records omzetten. Daarnaast kunt u ook de optie voor automatische registratie inschakelen op een koppeling met een virtueel netwerk. Als u automatische registratie voor een koppeling met een virtueel netwerk inschakelt, worden de DNS-records voor de virtuele machines in dat virtuele netwerk geregistreerd in de privézone. Als automatische registratie is ingeschakeld, werkt Azure DNS ook de zonerecords bij wanneer er een virtuele machine wordt gemaakt, het IP-adres ervan wordt gewijzigd of deze wordt verwijderd.

![Overzicht van DNS](./media/private-dns-overview/scenario.png)

> [!NOTE]
> Gebruik als best practice geen *.local*-domein voor uw privé-DNS-zone. Niet alle besturingssystemen ondersteunen dit.

## <a name="benefits"></a>Voordelen

Azure Privé-DNS biedt de volgende voordelen:

* **Hiermee zijn aangepaste DNS-oplossingen niet meer nodig**. Voorheen hebben veel klanten aangepaste DNS-oplossingen gemaakt voor het beheren van DNS-zones in hun virtuele netwerk. U kunt nu DNS-zones beheren met behulp van de systeemeigen Azure-infrastructuur, waardoor u niet meer de belasting van het maken en beheren van aangepaste DNS-oplossingen hebt.

* **U kunt alle algemene DNS-recordtypen gebruiken**. Azure DNS biedt ondersteuning voor A-, AAAA-, CNAME-, MX-, PTR-, SOA-, SRV- en TXT-records.

* **Automatisch beheer van hostnaamrecords**. Naast het hosten van uw aangepaste DNS-records, onderhoudt Azure automatisch hostnaamrecords voor de virtuele machines in de opgegeven virtuele netwerken. In dit scenario kunt u de domeinnamen die u gebruikt, optimaliseren zonder dat u aangepaste DNS-oplossingen hoeft te maken of toepassingen hoeft te wijzigen.

* **Hostnaamomzetting binnen virtuele netwerken**. In tegenstelling tot door Azure verschafte hostnamen kunnen privé-DNS-zones worden gedeeld binnen virtuele netwerken. Deze mogelijkheid vereenvoudigt scenario's met meerdere netwerken en servicedetectie, zoals peering van virtuele netwerken.

* **Vertrouwde hulpprogramma's en gebruikerservaring**. Voor een kortere leercurve gebruikt deze service genoegzaam bekende Azure DNS-hulpprogramma's (Azure Portal, Azure PowerShell, Azure CLI, Azure Resource Manager-sjablonen en de REST API).

* **Split-horizon-ondersteuning voor DNS**. Met Azure DNS kunt u zones maken met dezelfde naam die worden omgezet in verschillende antwoorden vanuit een virtueel netwerk en het openbare internet. Een typisch scenario voor split-horizon DNS is het leveren van een speciale versie van een service voor gebruik in uw virtuele netwerk.

* **Verkrijgbaar in alle Azure-regio's**. De functie Azure DNS Private Zones is beschikbaar in alle Azure-regio's in de openbare cloud van Azure.

## <a name="capabilities"></a>Functionaliteit

Azure DNS biedt de volgende functionaliteit:

* **Automatische registratie van virtuele machines vanuit een virtueel netwerk dat is gekoppeld aan een privézone waarvoor automatische registratie is ingeschakeld**. De virtuele machines worden geregistreerd (toegevoegd) aan de privézone als A-records die verwijzen naar hun privé-IP-adressen. Wanneer een virtuele machine wordt verwijderd uit een koppeling met een virtueel netwerk waarvoor automatische registratie is ingeschakeld, wordt in Azure DNS ook automatisch de bijbehorende DNS-record uit de gekoppelde privézone verwijderd.

* **Forward DNS-omzetting wordt ondersteund in virtuele netwerken die zijn gekoppeld aan de privézone**. Voor de DNS-omzetting binnen virtuele netwerken is er geen expliciete afhankelijkheid, zodat de virtuele netwerken met elkaar worden gekoppeld. Het is echter mogelijk dat u virtuele netwerken wilt koppelen voor andere scenario's (bijvoorbeeld HTTP-verkeer).

* **Omgekeerde DNS-zoekacties worden ondersteund binnen het bereik van het virtuele netwerk**. In omgekeerde DNS-zoekacties voor een privé-IP-adres in het virtuele netwerk dat is toegewezen aan een privézone, wordt de FQDN-naam geretourneerd waarin de host-/recordnaam en de zonenaam als achtervoegsel is opgenomen.

## <a name="other-considerations"></a>Andere overwegingen

Azure DNS heeft de volgende beperkingen:

* Een specifiek virtueel netwerk kan aan slechts één privézone worden gekoppeld als automatische registratie van DNS-records van VM's is ingeschakeld. U kunt echter meerdere virtuele netwerken koppelen aan één DNS-zone.
* Een omgekeerd DNS werkt alleen voor privé-IP-ruimte in het gekoppelde virtuele netwerk
* In een omgekeerd DNS voor een privé-IP-adres voor een gekoppeld virtueel netwerk wordt *internal.cloudapp.net* geretourneerd als het standaardachtervoegsel voor de virtuele machine. Voor virtuele netwerken die zijn gekoppeld aan een privézone waarvoor automatische registratie is ingeschakeld, worden in een omgekeerd DNS voor een privé-IP-adres twee FQDN-namen geretourneerd: een met standaard het achtervoegsel *internal.cloudapp.net* en een ander met het achtervoegsel van de privézone.
* Voorwaardelijk doorsturen wordt in het eigen systeem momenteel niet ondersteund. Zie [Naamomzetting voor VM's en rolinstanties](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) voor het inschakelen van omzetting tussen Azure-netwerken en on-premises netwerken.
 
## <a name="pricing"></a>Prijzen

Zie [Prijzen van Azure DNS](https://azure.microsoft.com/pricing/details/dns/) voor informatie over prijzen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van een privézone in Azure DNS met behulp van [Azure PowerShell](./private-dns-getstarted-powershell.md) of [Azure CLI](./private-dns-getstarted-cli.md).

* Lees meer over een aantal veelvoorkomende [scenario's voor privézones](./private-dns-scenarios.md) die u kunt realiseren met privézones in Azure DNS.

* Zie [Veelgestelde vragen over Privé-DNS](./dns-faq-private.md) voor veelgestelde vragen en antwoorden over privézones in Azure DNS, waaronder specifiek gedrag dat u kunt verwachten voor bepaalde soorten bewerkingen.

* Ga naar [Overzicht van DNS-zones en -records](dns-zones-records.md) voor meer informatie over DNS-zones en records.

* Informatie over enkele van de andere belangrijke [netwerkmogelijkheden](../networking/networking-overview.md) van Azure.
