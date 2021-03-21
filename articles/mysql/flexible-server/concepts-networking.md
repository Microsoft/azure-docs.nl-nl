---
title: Overzicht van netwerken-Azure Database for MySQL flexibele server
description: Meer informatie over connectiviteit en netwerk opties in de optie flexibele server implementatie voor Azure Database for MySQL
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: a8e2d77ff3c7cb2e4352b21cd87d630331e28660
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96906145"
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
   * Verbinding maken tussen Azure-resources in hetzelfde virtuele netwerk en uw flexibele server met behulp van privé-IP-adressen
   * VPN of ExpressRoute gebruiken om verbinding te maken met een flexibele server vanuit andere resources dan Azure
   * De flexibele server heeft geen openbaar eind punt

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

* **Wijzigingen in de acceptatie lijst zijn nog niet doorgevoerd:** Het kan een vertraging van vijf minuten duren voordat wijzigingen in de configuratie van de Azure Database for MySQL Server-firewall van kracht worden.

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
Azure Database for MySQL flexibele server ondersteunt het verbinden van uw client toepassingen met de MySQL-service met behulp van Transport Layer Security (TLS). TLS is een industrie standaard protocol dat versleutelde netwerk verbindingen tussen uw database server en client toepassingen garandeert. TLS is een bijgewerkt Protocol van Secure Sockets Layer (SSL).

Azure Database for MySQL flexibele server ondersteunt alleen versleutelde verbindingen met behulp van Transport Layer Security (TLS 1,2). Alle binnenkomende verbindingen met TLS 1,0 en TLS 1,1 worden geweigerd. U kunt de TLS-versie niet uitschakelen of wijzigen om verbinding te maken met Azure Database for MySQL flexibele server.


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het inschakelen van privétoegang (VNet-integratie) met behulp van [Azure Portal](how-to-manage-virtual-network-portal.md) of de [Azure CLI](how-to-manage-virtual-network-cli.md)
* Meer informatie over het inschakelen van openbare toegang (toegestane IP-adressen) met behulp van [Azure Portal](how-to-manage-firewall-portal.md) of [Azure CLI](how-to-manage-firewall-cli.md)
* Meer informatie over het [gebruik van TLS](how-to-connect-tls-ssl.md)
