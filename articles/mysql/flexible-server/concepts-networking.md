---
title: Overzicht van netwerken-Azure Database for MySQL flexibele server
description: Meer informatie over connectiviteit en netwerk opties in de optie flexibele server implementatie voor Azure Database for MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: c83f36216e7488df94c372234d0541a4ee9f99b5
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106492219"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server-preview"></a>Connectiviteits-en netwerk concepten voor Azure Database for MySQL-flexibele server (preview-versie)

In dit artikel worden de open bare en persoonlijke connectiviteits opties voor uw server beschreven. U leert hoe u de netwerk concepten voor Azure Database for MySQL flexibele server benadert om een server veilig in azure te maken.

> [!IMPORTANT]
> Azure Database for MySQL-flexibele server is in preview.

## <a name="choosing-a-networking-option"></a>Een netwerk optie kiezen
U hebt twee netwerk opties voor uw Azure Database for MySQL flexibele server. De opties zijn **privétoegang (VNet-integratie)** en openbare toegang **(toegestane IP-adressen)** . Bij het maken van de server moet u één optie kiezen. 

> [!NOTE]
> Uw netwerk optie kan niet worden gewijzigd nadat de server is gemaakt. 

* **Privétoegang (VNet-integratie)** : u kunt uw flexibele server implementeren in uw [virtuele Azure-netwerk](../../virtual-network/virtual-networks-overview.md). Virtuele Azure-netwerken bieden privé- en beveiligde netwerkcommunicatie. Resources in een virtueel netwerk kunnen communiceren via privé-IP-adressen.

   Kies de optie van VNet-integratie als u over de volgende mogelijkheden wilt beschikken:
   * Verbinding maken tussen Azure-resources in hetzelfde virtuele netwerk of een gekoppeld [virtueel netwerk](../../virtual-network/virtual-network-peering-overview.md) naar uw flexibele server
   * VPN of ExpressRoute gebruiken om verbinding te maken met een flexibele server vanuit andere resources dan Azure
   * Geen openbaar eindpunt

* **Open bare toegang (toegestane IP-adressen)** : uw flexibele server wordt geopend via een openbaar eind punt. Het openbare eindpunt is een openbaar omzetbaar DNS-adres. De zin 'toegestane IP-adressen' verwijst naar een bereik van IP's die u toestemming geeft om toegang te krijgen tot uw server. Deze machtigingen worden **firewallregels** genoemd. 

   Kies de open bare toegangs methode als u de volgende mogelijkheden wilt:
   * Verbinding maken met Azure-resources die geen ondersteuning bieden voor virtuele netwerken
   * Verbinding maken tussen bronnen buiten een Azure die niet zijn verbonden via VPN of ExpressRoute 
   * De flexibele server heeft een openbaar eind punt

De volgende kenmerken zijn van toepassing, ongeacht of u de persoonlijke toegang of de optie voor open bare toegang wilt gebruiken:
* Verbindingen van toegestane IP-adressen moeten worden geverifieerd bij de MySQL-server met geldige referenties
* [Versleuteling](#tls-and-ssl) van de verbinding is beschikbaar voor uw netwerk verkeer
* De server heeft een Fully Qualified Domain Name (FQDN). Voor de eigenschap hostname in verbindings reeksen raden we u aan de FQDN te gebruiken in plaats van een IP-adres.
* Beide opties bepalen de toegang op server niveau, niet op Data Base-of tabel niveau. U kunt de eigenschappen van MySQL gebruiken om de data base, tabel en andere toegang tot het object te beheren.


## <a name="private-access-vnet-integration"></a>Privétoegang (VNet-integratie)
Persoonlijke toegang met vnet-integratie (Virtual Network) biedt persoonlijke en veilige communicatie voor uw MySQL-flexibele server.

### <a name="virtual-network-concepts"></a>Concepten van virtueel netwerk
Hier volgen enkele concepten die u moet kennen bij het gebruik van virtuele netwerken met MySQL-flexibele servers.

* **Virtueel netwerk** : een Azure Virtual Network (VNet) bevat een persoonlijke IP-adres ruimte die voor uw gebruik is geconfigureerd. Ga naar het [overzicht van azure Virtual Network](../../virtual-network/virtual-networks-overview.md) voor meer informatie over Azure Virtual Network.

    Het virtuele netwerk moet zich in dezelfde Azure-regio bevinden als uw flexibele server.

* **Gedelegeerd subnet** : een virtueel netwerk bevat subnetten (subnetwerken). Met subnetten kunt u het virtuele netwerk segmenteren in kleinere adres ruimten. Azure-resources worden geïmplementeerd in specifieke subnetten binnen een virtueel netwerk. 

   Uw MySQL-flexibele server moet zich in een subnet bevinden dat wordt **overgedragen** voor het flexibele gebruik van MySQL-servers. Deze delegatie houdt in dat alleen Azure Database for MySQL Flexibele servers dat subnet kunnen gebruiken. Er kunnen zich geen andere Azure-resourcetypen in het gedelegeerde subnet bevinden. U delegeert een subnet door de eigenschap Delegation toe te wijzen als micro soft. DBforMySQL/flexibleServers.

* **Netwerk beveiligings groepen (NSG)** Met beveiligings regels in netwerk beveiligings groepen kunt u het type netwerk verkeer filteren dat in en uit de subnetten van het virtuele netwerk en netwerk interfaces kan stromen. Bekijk het [overzicht van de netwerk beveiligings groep](../../virtual-network/network-security-groups-overview.md) voor meer informatie.

* **Peering op virtueel netwerk** Met Virtual Network-peering kunt u naadloos verbinding maken met twee of meer virtuele netwerken in Azure. De gekoppelde virtuele netwerken worden als een voor connectiviteits doeleinden weer gegeven. Het verkeer tussen virtuele machines in gekoppelde virtuele netwerken maakt gebruik van de micro soft backbone-infra structuur. Het verkeer tussen de client toepassing en de flexibele server in de peered VNets wordt alleen gerouteerd via het particuliere netwerk van micro soft en is alleen op dat netwerk geïsoleerd.

Flexibele server ondersteunt de peering van virtuele netwerken binnen dezelfde Azure-regio. Peering-VNets tussen regio's **worden niet ondersteund**. Bekijk de [concepten van de peering van het virtuele netwerk](../../virtual-network/virtual-network-peering-overview.md) voor meer informatie.

### <a name="connecting-from-peered-vnets-in-same-azure-region"></a>Verbinding maken vanaf een peered VNets in dezelfde Azure-regio
Als de client toepassing probeert verbinding te maken met een flexibele server in het gekoppelde virtuele netwerk, kan het zijn dat er geen verbinding kan worden gemaakt met behulp van de flexibele server servername omdat de DNS-naam voor de flexibele server niet kan worden omgezet in een gekoppeld VNet. Er zijn twee opties om dit probleem op te lossen:
* Privé IP-adres gebruiken (aanbevolen voor dev/test-scenario): deze optie kan worden gebruikt voor ontwikkelings-en test doeleinden. U kunt nslookup gebruiken om het privé-IP-adres voor uw flexibele server naam (Fully Qualified Domain Name) te reverse lookup en een privé IP-adres gebruiken om verbinding te maken vanuit de client toepassing. Het gebruik van het privé IP-adres voor de verbinding met een flexibele server wordt niet aanbevolen voor productie gebruik omdat het kan worden gewijzigd tijdens de geplande of niet-geplande gebeurtenis.
* Privé-DNS zone gebruiken (aanbevolen voor productie): deze optie is geschikt voor productie doeleinden. U richt een [privé-DNS-zone](../../dns/private-dns-getstarted-portal.md) in en koppelt deze aan het virtuele netwerk van de client. In de privé-DNS-zone voegt u een [a-record](../../dns/dns-zones-records.md#record-types) toe voor uw flexibele server met behulp van het privé-IP-adres. U kunt vervolgens de A-record gebruiken om verbinding te maken tussen de client toepassing in een peered virtueel netwerk en een flexibele server.

### <a name="connecting-from-on-premises-to-flexible-server-in-virtual-network-using-expressroute-or-vpn"></a>Verbinding maken tussen on-premises en flexibele server in Virtual Network met behulp van ExpressRoute of VPN
Voor werk belastingen waarvoor toegang is vereist tot flexibele server in het virtuele netwerk vanaf een on-premises netwerk, hebt u [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute/) of [VPN](/azure/architecture/reference-architectures/hybrid-networking/vpn/) en een virtueel netwerk nodig [die zijn verbonden met on-premises](/azure/architecture/reference-architectures/hybrid-networking/). Wanneer deze installatie is uitgevoerd, hebt u een DNS-doorstuur server nodig om de flexibele servername op te lossen als u verbinding wilt maken vanuit een client toepassing (zoals MySQL Workbench) die wordt uitgevoerd op een on-premises virtueel netwerk. Deze DNS-doorstuur server is verantwoordelijk voor het omzetten van alle DNS-query's via een doorstuur server van het [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)naar de door Azure VERSCHAFTe DNS-service.

U hebt de volgende resources nodig om correct te configureren:

- On-premises netwerk
- MySQL flexibele server ingericht met persoonlijke toegang (VNet-integratie)
- Virtueel netwerk [verbonden met on-premises](/azure/architecture/reference-architectures/hybrid-networking/)
- DNS-doorstuur server- [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md) gebruiken die zijn geïmplementeerd in azure

U kunt vervolgens de flexibele server naam (FQDN) gebruiken om verbinding te maken tussen de client toepassing in een peered virtueel netwerk of een on-premises netwerk naar een flexibele server.

### <a name="unsupported-virtual-network-scenarios"></a>Niet-ondersteunde scenario's voor virtuele netwerken
* Openbaar eind punt (of openbaar IP of DNS): een flexibele server die is geïmplementeerd in een virtueel netwerk kan geen openbaar eind punt hebben
* Nadat de flexibele server op een virtueel netwerk en subnet is geïmplementeerd, kunt u deze niet naar een ander virtueel netwerk of subnet verplaatsen. U kunt het virtuele netwerk niet verplaatsen naar een andere resource groep of een ander abonnement.
* De subnet-grootte (adres ruimten) kan niet worden verhoogd wanneer de resources zich in het subnet bevinden
* Peering-VNets tussen regio's wordt niet ondersteund

Meer informatie over het inschakelen van persoonlijke toegang (vnet-integratie) met behulp van de [Azure Portal](how-to-manage-virtual-network-portal.md) of [Azure cli](how-to-manage-virtual-network-cli.md).

> [!NOTE]
> Als u de aangepaste DNS-server gebruikt, moet u een DNS-Forwarder gebruiken om de FQDN van Azure Database for MySQL-flexibele server op te lossen. Raadpleeg [de naam omzetting die gebruikmaakt van uw eigen DNS-server](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) voor meer informatie.

## <a name="public-access-allowed-ip-addresses"></a>Openbare toegang (toegestane IP-adressen)
De kenmerken van de open bare toegangs methode zijn onder andere:
* Alleen de IP-adressen die u toestaat, hebben toegang tot de flexibele MySQL-server. Standaard zijn er geen IP-adressen toegestaan. U kunt IP-adressen toevoegen tijdens het maken van de server of later.
* De MySQL-server heeft een openbaar omgezette DNS-naam
* Uw flexibele server bevindt zich niet in een van uw virtuele Azure-netwerken
* Netwerk verkeer van en naar de server gaat niet via een particulier netwerk. Het verkeer maakt gebruik van de algemene Internet routes.

### <a name="firewall-rules"></a>Firewall-regels
Het verlenen van machtigingen aan een IP-adres wordt een firewall regel genoemd. Als een verbindings poging afkomstig is van een IP-adres dat u niet hebt toegestaan, wordt er een fout melding weer geven op de oorspronkelijke client.


### <a name="allowing-all-azure-ip-addresses"></a>Alle Azure IP-adressen toestaan
Als er geen vast uitgaand IP-adres beschikbaar is voor uw Azure-service, kunt u overwegen verbindingen van alle IP-adressen van Azure-Data Center in te scha kelen.

> [!IMPORTANT]
> Met de optie **open bare toegang vanaf Azure-Services en-resources in azure toestaan** wordt de firewall zodanig geconfigureerd dat alle verbindingen van Azure worden toegestaan, met inbegrip van verbindingen van de abonnementen van andere klanten. Wanneer u deze optie selecteert, zorgt u er dan voor dat uw aanmeldings- en gebruikersmachtigingen de toegang beperken tot alleen geautoriseerde gebruikers.

Meer informatie over het inschakelen en beheren van open bare toegang (toegestane IP-adressen) met behulp van de [Azure Portal](how-to-manage-firewall-portal.md) of [Azure cli](how-to-manage-firewall-cli.md).


### <a name="troubleshooting-public-access-issues"></a>Problemen met open bare toegang oplossen
Houd rekening met de volgende punten wanneer de toegang tot de Microsoft Azure-Data Base voor MySQL-Server service niet werkt zoals verwacht:

* **Wijzigingen in de allowlist zijn nog niet doorgevoerd:** Het kan een vertraging van vijf minuten duren voordat wijzigingen in de configuratie van de Azure Database for MySQL Server-firewall van kracht worden.

* **Verificatie is mislukt:** Als een gebruiker geen machtigingen heeft op de Azure Database for MySQL server of het gebruikte wacht woord onjuist is, wordt de verbinding met de Azure Database for MySQL-server geweigerd. Het maken van een firewall instelling biedt clients alleen de mogelijkheid om verbinding te maken met uw server. Elke client moet nog steeds de benodigde beveiligings referenties opgeven.

* **IP-adres van dynamische client:** Als u een Internet verbinding hebt met dynamische IP-adres sering en u problemen ondervindt met het verkrijgen van de firewall, kunt u een van de volgende oplossingen proberen:

   * Vraag uw Internet provider (ISP) naar het IP-adres bereik dat is toegewezen aan uw client computers die toegang hebben tot de Azure Database for MySQL server en voeg het IP-adres bereik vervolgens toe als een firewall regel.
   * Neem in plaats daarvan statische IP-adressen op voor uw client computers en voeg vervolgens het statische IP-adres toe als een firewall regel.
  
* De **firewall regel is niet beschikbaar voor de IPv6-indeling:** De firewall-regels moeten de IPv4-indeling hebben. Als u firewall regels in de IPv6-indeling opgeeft, wordt de validatie fout weer gegeven.


## <a name="hostname"></a>Hostnaam
Ongeacht welke netwerk optie u kiest, raden we u aan om altijd een Fully Qualified Domain Name (FQDN) als hostnaam te gebruiken bij het maken van verbinding met uw flexibele server. Het IP-adres van de server is niet gegarandeerd statisch. Door gebruik te maken van de FQDN-namen kunt u voor komen dat uw connection string worden gewijzigd. 

Voorbeeld
* Aanbevelingen `hostname = servername.mysql.database.azure.com`
* Vermijd het gebruik van `hostname = 10.0.0.4` (een privé adres) of `hostname = 40.2.45.67` (een openbaar IP), indien mogelijk


## <a name="tls-and-ssl"></a>TLS en SSL
Azure Database for MySQL flexibele server ondersteunt het verbinden van uw client toepassingen met de MySQL-server met behulp van Secure Sockets Layer (SSL) met TLS-code ring (trans port Layer Security). TLS is een industrie standaard protocol dat versleutelde netwerk verbindingen tussen uw database server-en client toepassingen waarborgt, zodat u kunt voldoen aan de nalevings vereisten.

Azure Database for MySQL flexibele server ondersteunt standaard versleutelde verbindingen met behulp van Transport Layer Security (TLS 1,2) en alle binnenkomende verbindingen met TLS 1,0 en TLS 1,1 worden standaard geweigerd. De versleutelde configuratie voor het afdwingen van verbinding of TLS-versie op de flexibele server kan worden geconfigureerd en gewijzigd. 

Hieronder vindt u de verschillende configuratie van SSL-en TLS-instellingen die u kunt hebben voor uw flexibele server:

| Scenario   | Instellingen voor server parameters      | Beschrijving                                    |
|------------|--------------------------------|------------------------------------------------|
|SSL uitschakelen (versleutelde verbindingen) | require_secure_transport = uit |Als uw oudere toepassing geen versleutelde verbindingen met de MySQL-server ondersteunt, kunt u het afdwingen van versleutelde verbindingen met uw flexibele server uitschakelen door require_secure_transport = uit te stellen.|
|SSL afdwingen met TLS-versie < 1,2 | require_secure_transport = ON en tls_version = TLSV1 of TLSV 1.1| Als uw oudere toepassing versleutelde verbindingen ondersteunt, maar TLS-versie < 1,2, kunt u versleutelde verbindingen inschakelen, maar uw flexibele server zo configureren dat verbindingen met de TLS-versie (v 1.0 of v 1.1) die door uw toepassing worden ondersteund, worden toegestaan|
|SSL afdwingen met TLS-versie = 1.2 (standaard configuratie)|require_secure_transport = ON en tls_version = TLSV 1.2| Dit is de aanbevolen en standaard configuratie voor een flexibele server.|
|SSL afdwingen met TLS-versie = 1.3 (ondersteund met MySQL v 8.0 en hoger)| require_secure_transport = op en tls_version = TLSV 1.3| Dit is handig en aanbevolen voor het ontwikkelen van nieuwe toepassingen|

> [!Note]
> Wijzigingen in SSL-code ring op een flexibele server worden niet ondersteund. FIPS-coderings suites worden standaard afgedwongen wanneer tls_version is ingesteld op TLS-versie 1,2. Voor TLS-versies met uitzonde ring van versie 1,2 wordt SSL-code ring ingesteld op standaard instellingen die bij de installatie van MySQL-community's worden geleverd.

Lees hoe u [verbinding maakt met behulp van SSL/TLS](how-to-connect-tls-ssl.md) voor meer informatie. 


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het inschakelen van privétoegang (VNet-integratie) met behulp van [Azure Portal](how-to-manage-virtual-network-portal.md) of de [Azure CLI](how-to-manage-virtual-network-cli.md)
* Meer informatie over het inschakelen van openbare toegang (toegestane IP-adressen) met behulp van [Azure Portal](how-to-manage-firewall-portal.md) of [Azure CLI](how-to-manage-firewall-cli.md)
* Meer informatie over het [gebruik van TLS](how-to-connect-tls-ssl.md)
