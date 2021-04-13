---
title: Wat is privé-DNS in Azure?
description: In dit artikel gaat u aan de slag met een overzicht van de privé-DNS-hostingservice op Microsoft Azure.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: overview
ms.date: 04/09/2021
ms.author: rohink
ms.openlocfilehash: 560a88c973d71b3e2c6533e05e4f374f9a5bcd8f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107311478"
---
# <a name="what-is-azure-private-dns"></a>Wat is privé-DNS in Azure?

De Domain Name System, of DNS, is verantwoordelijk voor het vertalen van een service naam naar een IP-adres.  Azure DNS is een hosting service voor domeinen en biedt een naam omzetting met behulp van de Microsoft Azure-infra structuur. Azure DNS biedt geen ondersteuning voor Internet gerichte DNS-domeinen, maar biedt ook ondersteuning voor privé-DNS-zones.

Azure Privé-DNS biedt een betrouw bare en veilige DNS-service voor uw virtuele netwerk. In azure Privé-DNS worden domein namen in het virtuele netwerk beheerd en omgezet zonder dat er een aangepaste DNS-oplossing hoeft te worden geconfigureerd. Met behulp van privé-DNS-zones kunt u uw eigen aangepaste domein naam gebruiken in plaats van de door Azure verschafte namen tijdens de implementatie. Met behulp van een aangepaste domein naam kunt u uw virtuele netwerk architectuur aanpassen aan de behoeften van uw organisatie. Het biedt een naam omzetting voor virtuele machines (Vm's) in een virtueel netwerk en verbonden virtuele netwerken. Daarnaast kunt u zonenamen configureren met een split-horizon-weergave, waardoor u de naam kunt delen in een privé-DNS-zone en een openbare DNS-zone.

Als u de records van een privé-DNS-zone wilt omzetten vanuit uw virtuele netwerk, moet u het virtuele netwerk koppelen aan de zone. Gekoppelde virtuele netwerken hebben volledige toegang en kunnen alle in de privézone gepubliceerde DNS-records omzetten. U kunt ook registratie inschakelen op een virtuele netwerk koppeling. Wanneer u de optie voor het inschakelen van de registratie op een virtueel netwerk hebt ingeschakeld, worden de DNS-records voor de virtuele machines in dat virtuele netwerk geregistreerd in de privé zone. Als automatische registratie is ingeschakeld, wordt de zone record door Azure DNS bijgewerkt wanneer een virtuele machine wordt gemaakt, wordt het IP-adres gewijzigd of wordt het verwijderd.

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

* **Automatische registratie van virtuele machines vanuit een virtueel netwerk dat is gekoppeld aan een privézone waarvoor automatische registratie is ingeschakeld**. Virtuele machines worden geregistreerd bij de privé zone als een record die verwijst naar hun privé-IP-adressen. Wanneer een virtuele machine in een virtuele netwerk koppeling met automatische registratie is ingeschakeld, wordt de bijbehorende DNS-record uit de gekoppelde persoonlijke zone automatisch door Azure DNS verwijderd.

* **Forward DNS-omzetting wordt ondersteund in virtuele netwerken die zijn gekoppeld aan de privézone**. Voor de DNS-omzetting binnen virtuele netwerken is er geen expliciete afhankelijkheid, zodat de virtuele netwerken met elkaar worden gekoppeld. Het is echter mogelijk dat u virtuele netwerken wilt koppelen voor andere scenario's (bijvoorbeeld HTTP-verkeer).

* **Omgekeerde DNS-zoekacties worden ondersteund binnen het bereik van het virtuele netwerk**. Achterwaartse DNS-zoek opdracht voor een privé-IP die aan een privé zone is gekoppeld, wordt een FQDN-naam geretourneerd die de host/recordnaam en de zone naam als achtervoegsel bevat.

## <a name="other-considerations"></a>Andere overwegingen

Azure DNS heeft de volgende beperkingen:

* Een specifiek virtueel netwerk kan aan slechts één privézone worden gekoppeld als automatische registratie van DNS-records van VM's is ingeschakeld. U kunt echter meerdere virtuele netwerken koppelen aan één DNS-zone.
* Een omgekeerd DNS werkt alleen voor privé-IP-ruimte in het gekoppelde virtuele netwerk
* Omgekeerde DNS voor een privé-IP-adres in het gekoppelde virtuele netwerk wordt geretourneerd `internal.cloudapp.net` als het standaard achtervoegsel voor de virtuele machine. Voor virtuele netwerken die zijn gekoppeld aan een privé zone waarvoor registratie is ingeschakeld, retourneert reverse DNS voor een persoonlijk IP-adres twee FQDN-namen: een met standaard het achtervoegsel `internal.cloudapp.net` en een andere met het achtervoegsel van de privé zone.
* Voorwaardelijk door sturen wordt momenteel niet ondersteund. Zie [Naamomzetting voor VM's en rolinstanties](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) voor het inschakelen van omzetting tussen Azure-netwerken en on-premises netwerken.
 
## <a name="pricing"></a>Prijzen

Zie [Prijzen van Azure DNS](https://azure.microsoft.com/pricing/details/dns/) voor informatie over prijzen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het maken van een privézone in Azure DNS met behulp van [Azure PowerShell](./private-dns-getstarted-powershell.md) of [Azure CLI](./private-dns-getstarted-cli.md).

* Lees meer over een aantal veelvoorkomende [scenario's voor privézones](./private-dns-scenarios.md) die u kunt realiseren met privézones in Azure DNS.

* Voor veelgestelde vragen en antwoorden over privé zones in Azure DNS raadpleegt u [privé-DNS Veelgestelde vragen](./dns-faq-private.md).

* Ga naar [Overzicht van DNS-zones en -records](dns-zones-records.md) voor meer informatie over DNS-zones en records.

* Informatie over enkele van de andere belangrijke [netwerkmogelijkheden](../networking/networking-overview.md) van Azure.
