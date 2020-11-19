---
title: Netwerk beveiliging voor Azure Event Grid bronnen
description: In dit artikel wordt beschreven hoe u service tags gebruikt voor uitgaand verkeer, IP-firewall regels voor binnenkomend en privé-eind punten voor ingangs gegevens met Azure Event Grid.
author: VidyaKukke
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: vkukke
ms.openlocfilehash: 10c9b165041f0a4a1f09511f17bef3629353c3b2
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94917525"
---
# <a name="network-security-for-azure-event-grid-resources"></a>Netwerk beveiliging voor Azure Event Grid bronnen
In dit artikel wordt beschreven hoe u de volgende beveiligings functies gebruikt met Azure Event Grid: 

- Service tags voor uitgaand verkeer
- IP-firewall regels voor binnenkomend verkeer
- Privé-eind punten voor ingangs


## <a name="service-tags"></a>Servicetags
Een servicetag vertegenwoordigt een groep IP-adres voorvoegsels van een bepaalde Azure-service. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag en werkt de servicetag automatisch bij met gewijzigde adressen, zodat de complexiteit van regel matige updates voor netwerk beveiligings regels wordt geminimaliseerd. Zie [service Tags Overview](../virtual-network/service-tags-overview.md)(Engelstalig) voor meer informatie over service tags.

U kunt service tags gebruiken voor het definiëren van netwerk toegangs beheer voor [netwerk beveiligings groepen](../virtual-network/network-security-groups-overview.md#security-rules) of [Azure firewall](../firewall/service-tags.md). Gebruik service tags in plaats van specifieke IP-adressen wanneer u beveiligings regels maakt. Door de naam van de service label (bijvoorbeeld **AzureEventGrid**) op te geven in het juiste *bron* -of *doel* veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren.

| Servicetag | Doel | Kunt u inkomend of uitgaand gebruiken? | Kan regionaal worden? | Kunt gebruiken met Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|
| AzureEventGrid | Azure Event Grid. | Beide | Nee | Nee |


## <a name="ip-firewall"></a>IP-firewall 
Azure Event Grid ondersteunt toegangs beheer op basis van IP voor het publiceren naar onderwerpen en domeinen. Met besturings elementen op basis van IP kunt u de uitgevers beperken tot een onderwerp of domein tot een set goedgekeurde computers en Cloud Services. Deze functie is een aanvulling op de [verificatie mechanismen](security-authentication.md) die door Event grid worden ondersteund.

Het onderwerp en het domein zijn standaard toegankelijk vanaf internet, zolang de aanvraag geldig is voor verificatie en autorisatie. Met IP-firewall kunt u dit nog verder beperken tot een reeks IP-adressen of IP-adresbereiken in CIDR-notatie [(klasseloze Inter-Domain route ring)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Uitgevers die afkomstig zijn van een ander IP-adres, worden geweigerd en ontvangen een 403 (verboden)-antwoord.

Zie [Configure IP firewall](configure-firewall.md)(Engelstalig) voor stapsgewijze instructies voor het configureren van IP-Firewall voor onderwerpen en domeinen.

## <a name="private-endpoints"></a>Privé-eindpunten
U kunt [privé-eind punten](../private-link/private-endpoint-overview.md) gebruiken om direct vanuit uw virtuele netwerk toegang te geven tot uw onderwerpen en domeinen, zonder dat u het open bare Internet [hoeft te passeren](../private-link/private-link-overview.md) . Een persoonlijk eind punt is een speciale netwerk interface voor een Azure-service in uw VNet. Wanneer u een persoonlijk eind punt maakt voor uw onderwerp of domein, biedt het een beveiligde verbinding tussen clients in uw VNet en uw Event Grid bron. Het persoonlijke eind punt krijgt een IP-adres uit het IP-adres bereik van uw VNet. De verbinding tussen het persoonlijke eind punt en de Event Grid-Service maakt gebruik van een beveiligde persoonlijke koppeling.

![Architectuurdiagram](./media/network-security/architecture-diagram.png)

Met persoonlijke eind punten voor uw Event Grid-resource kunt u het volgende doen:

- Veilige toegang tot uw onderwerp of domein vanuit een VNet over een micro soft-backbone-netwerk in plaats van het open bare Internet.
- Maak veilig verbinding vanaf on-premises netwerken die verbinding maken met het VNet met behulp van VPN-of Express-routes met privé-peering.

Wanneer u een persoonlijk eind punt maakt voor een onderwerp of domein in uw VNet, wordt een aanvraag voor goed keuring verzonden naar de eigenaar van de resource. Als de gebruiker die het persoonlijke eind punt wil maken ook eigenaar van de resource is, wordt deze aanvraag voor toestemming automatisch goedgekeurd. Anders heeft de verbinding de status in **behandeling** tot goedgekeurd. Toepassingen in het VNet kunnen naadloos verbinding maken met de Event Grid-Service via het persoonlijke eind punt, met behulp van dezelfde verbindings reeksen en autorisatie mechanismen die ze anders zouden gebruiken. Resource-eigen aren kunnen toestemmings aanvragen en de persoonlijke eind punten beheren via het tabblad **privé-eind punten** voor de resource in de Azure Portal.

### <a name="connect-to-private-endpoints"></a>Verbinding maken met privé-eind punten
Uitgevers in een VNet met behulp van het privé-eind punt moeten hetzelfde connection string gebruiken voor het onderwerp of het domein als clients die verbinding maken met het open bare eind punt. Met DNS-omzetting worden verbindingen van het VNet naar het onderwerp of het domein via een persoonlijke koppeling automatisch gerouteerd. Event Grid maakt standaard een [privé-DNS-zone](../dns/private-dns-overview.md) die is gekoppeld aan het VNet met de nood zakelijke update voor de privé-eind punten. Als u echter uw eigen DNS-server gebruikt, moet u mogelijk aanvullende wijzigingen aanbrengen in uw DNS-configuratie.

### <a name="dns-changes-for-private-endpoints"></a>DNS-wijzigingen voor privé-eind punten
Wanneer u een persoonlijk eind punt maakt, wordt de DNS CNAME-record voor de resource bijgewerkt naar een alias in een subdomein met het voor voegsel `privatelink` . Standaard wordt een privé-DNS-zone gemaakt die overeenkomt met het subdomein van de persoonlijke koppeling. 

Wanneer u het onderwerp of de URL van het domein eindpunt oplost van buiten het VNet met het persoonlijke eind punt, wordt dit omgezet in het open bare eind punt van de service. De DNS-bron records voor onderwerpa, wanneer deze zijn omgezet van **buiten het VNet** dat als host fungeert voor het persoonlijke eind punt, zijn:

| Naam                                          | Type      | Waarde                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | CNAME     | \<Azure traffic manager profile\>

U kunt de toegang voor een client buiten het VNet weigeren of beheren via het open bare eind punt met behulp van de [IP-firewall](#ip-firewall). 

Wanneer het is opgelost vanuit het VNet dat het persoonlijke eind punt host, wordt het onderwerp of de URL van het domein eind punt omgezet naar het IP-adres van het privé-eind punt. De DNS-bron records voor het onderwerp ' Topica ', wanneer deze zijn omgezet in **het VNet** dat als host fungeert voor het persoonlijke eind punt, zijn:

| Naam                                          | Type      | Waarde                                         |
| --------------------------------------------- | ----------| --------------------------------------------- |  
| `topicA.westus.eventgrid.azure.net`             | CNAME     | `topicA.westus.privatelink.eventgrid.azure.net` |
| `topicA.westus.privatelink.eventgrid.azure.net` | A         | 10.0.0.5

Deze aanpak maakt het mogelijk om toegang tot het onderwerp of het domein te krijgen met dezelfde connection string voor clients op het VNet dat als host fungeert voor de persoonlijke eind punten en clients buiten het VNet.

Als u een aangepaste DNS-server in uw netwerk gebruikt, kunnen clients de FQDN voor het onderwerp of het domein-eind punt omzetten in het IP-adres van het privé-eind punt. Configureer uw DNS-server voor het delegeren van het subdomein van uw privé-koppeling naar de privé-DNS-zone voor het VNet of configureer de A-records voor `topicOrDomainName.regionName.privatelink.eventgrid.azure.net` met het IP-adres van het privé-eind punt.

De aanbevolen DNS-zone naam is `privatelink.eventgrid.azure.net` .

### <a name="private-endpoints-and-publishing"></a>Persoonlijke eind punten en publicatie

In de volgende tabel worden de verschillende statussen van de verbinding met het persoonlijke eind punt en de gevolgen voor het publiceren beschreven:

| Verbindings status   |  Publiceren voltooid (Ja/Nee) |
| ------------------ | -------------------------------|
| Goedgekeurd           | Ja                            |
| Afgewezen           | Nee                             |
| In behandeling            | Nee                             |
| Ontkoppeld       | Nee                             |

Voor een geslaagde publicatie moet de verbindings status van het particuliere eind punt worden **goedgekeurd**. Als een verbinding wordt geweigerd, kan deze niet worden goedgekeurd met behulp van de Azure Portal. De enige mogelijkheid is de verbinding te verwijderen en in plaats daarvan een nieuwe te maken.

## <a name="pricing-and-quotas"></a>Prijzen en quota
**Privé-eind punten** zijn beschikbaar in de lagen basis en premium van Event grid. Event Grid kunnen Maxi maal 64 persoonlijke eindpunt verbindingen worden gemaakt per onderwerp of domein. 

De functie **IP-firewall** is beschikbaar in de lagen basis en premium van Event grid. U kunt Maxi maal 16 IP-firewall regels maken per onderwerp of domein.

## <a name="next-steps"></a>Volgende stappen
U kunt IP-Firewall voor uw Event Grid-bron configureren om de toegang via het open bare Internet te beperken tot alleen een select set met IP-adressen of IP-adresbereiken. Zie [Configure IP firewall](configure-firewall.md)(Engelstalig) voor stapsgewijze instructies.

U kunt privé-eind punten zo configureren dat alleen toegang wordt beperkt tot geselecteerde virtuele netwerken. Zie voor stapsgewijze instructies [persoonlijke eind punten configureren](configure-private-endpoints.md).

Zie problemen [met netwerk connectiviteit oplossen](troubleshoot-network-connectivity.md) voor informatie over het oplossen van problemen met de netwerk verbinding