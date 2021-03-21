---
title: Opties voor DNS-naam omzetting voor virtuele Linux-machines
description: Scenario's voor naam omzetting voor virtuele Linux-machines in azure IaaS, inclusief de meegeleverde DNS-services, hybride externe DNS en uw eigen DNS-server.
author: RicksterCDN
ms.service: virtual-machines
ms.subservice: networking
ms.topic: conceptual
ms.date: 10/19/2016
ms.author: rclaus
ms.collection: linux
ms.openlocfilehash: e689e934f11e3cc2a621f25525e507a72a7044b8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556788"
---
# <a name="dns-name-resolution-options-for-linux-virtual-machines-in-azure"></a>Opties voor DNS-naam omzetting voor virtuele Linux-machines in azure
Azure biedt standaard DNS-naam omzetting voor alle virtuele machines die zich in één virtueel netwerk bevinden. U kunt uw eigen DNS-oplossing voor naam omzetting implementeren door uw eigen DNS-services te configureren op de virtuele machines die door Azure worden gehost. De volgende scenario's kunnen u helpen bij het kiezen van het abonnement dat geschikt is voor uw situatie.

* [Naam omzetting die Azure biedt](#name-resolution-that-azure-provides)
* [Naam omzetting met uw eigen DNS-server](#name-resolution-using-your-own-dns-server)

Welk type naam omzetting u gebruikt, is afhankelijk van hoe uw virtuele machines en rolinstanties met elkaar moeten communiceren.

In de volgende tabel ziet u scenario's en bijbehorende oplossingen voor naam omzetting:

| **Scenario** | **Oplossing** | **Achtervoegsel** |
| --- | --- | --- |
| Naam omzetting tussen rolinstanties of virtuele machines in hetzelfde virtuele netwerk |Naam omzetting die Azure biedt |hostnaam of FQDN-naam (FULLy Qualified Domain Name) |
| Naam omzetting tussen rolinstanties of virtuele machines in verschillende virtuele netwerken |Door de klant beheerde DNS-servers die query's door sturen tussen virtuele netwerken voor omzetting door Azure (DNS-proxy). Zie [naam omzetting met uw eigen DNS-server](#name-resolution-using-your-own-dns-server). |Alleen FQDN |
| Omzetting van on-premises computers en service namen van rolinstanties of virtuele machines in azure |Door de klant beheerde DNS-servers (bijvoorbeeld on-premises domein controller, lokale alleen-lezen domein controller of een secundaire DNS-server die is gesynchroniseerd met zone overdracht). Zie [naam omzetting met uw eigen DNS-server](#name-resolution-using-your-own-dns-server). |Alleen FQDN |
| Omzetting van Azure hostnamen van on-premises computers |Query's door sturen naar een door de klant beheerde DNS-proxy server in het bijbehorende virtuele netwerk. De proxy server stuurt query's naar Azure door voor het oplossen van problemen. Zie [naam omzetting met uw eigen DNS-server](#name-resolution-using-your-own-dns-server). |Alleen FQDN |
| Omgekeerde DNS voor interne Ip's |[Naam omzetting met uw eigen DNS-server](#name-resolution-using-your-own-dns-server) |n.v.t. |

## <a name="name-resolution-that-azure-provides"></a>Naam omzetting die Azure biedt
Naast de resolutie van open bare DNS-namen biedt Azure interne naam omzetting voor virtuele machines en rolinstanties die zich in hetzelfde virtuele netwerk bevinden. In virtuele netwerken die zijn gebaseerd op Azure Resource Manager, is het DNS-achtervoegsel consistent in het virtuele netwerk. de FQDN is niet nodig. DNS-namen kunnen worden toegewezen aan zowel netwerk interface kaarten (Nic's) als virtuele machines. Hoewel voor de naam omzetting die Azure biedt geen configuratie vereist is, is het niet de juiste keuze voor alle implementatie scenario's, zoals wordt weer gegeven in de voor gaande tabel.

### <a name="features-and-considerations"></a>Functies en overwegingen
**Functies**

* Er is geen configuratie vereist voor het gebruik van naam omzetting die Azure biedt.
* De service voor naam omzetting die Azure biedt, is Maxi maal beschikbaar. U hoeft geen clusters van uw eigen DNS-servers te maken en te beheren.
* De service voor naam omzetting die Azure biedt, kan samen met uw eigen DNS-servers worden gebruikt voor het omzetten van zowel on-premises als Azure-hostnamen.
* Er wordt een naam omzetting gegeven tussen virtuele machines in virtuele netwerken zonder dat hiervoor de FQDN nodig is.
* U kunt hostnamen gebruiken die uw implementaties het beste beschrijven in plaats van met automatisch gegenereerde namen.

**Tot**

* Het DNS-achtervoegsel dat door Azure wordt gemaakt, kan niet worden gewijzigd.
* U kunt uw eigen records niet hand matig registreren.
* WINS en NetBIOS worden niet ondersteund.
* Hostnamen moeten compatibel zijn met DNS.
    Namen mogen alleen 0-9, a-z en '-' bevatten, maar ze kunnen niet beginnen of eindigen met een-. Zie RFC 3696 sectie 2.
* DNS-query verkeer wordt beperkt voor elke virtuele machine. Beperking is niet van invloed op de meeste toepassingen.  Als de aanvraag beperking wordt waargenomen, moet u ervoor zorgen dat caching aan client zijde is ingeschakeld.  Zie voor meer informatie [de meest van naam omzetting ophalen die Azure biedt](#getting-the-most-from-name-resolution-that-azure-provides).

### <a name="getting-the-most-from-name-resolution-that-azure-provides"></a>Optimaal profiteren van naam omzetting die Azure biedt
**Caching aan client zijde:**

Sommige DNS-query's worden niet via het netwerk verzonden. Caching aan de client zijde helpt de latentie te verminderen en de tolerantie voor netwerk consistentie te verbeteren door terugkerende DNS-query's uit een lokale cache op te lossen. DNS-records bevatten een TTL (time-to-Live) waarmee de cache de record zo lang mogelijk kan opslaan zonder dat dit van invloed is op de versheid van de record. Als gevolg hiervan is caching aan client zijde geschikt voor de meeste situaties.

Voor sommige Linux-distributies is de standaard instelling geen caching. U wordt aangeraden een cache aan elke virtuele Linux-machine toe te voegen nadat u hebt gecontroleerd of er al een lokale cache is.

Er zijn verschillende DNS-cache pakketten, zoals dnsmasq, beschikbaar. Hier volgen de stappen voor het installeren van dnsmasq op de meest voorkomende distributies:

**Ubuntu (maakt gebruik van resolvconf)**
  * Installeer het dnsmasq-pakket ("sudo apt-get install dnsmasq").

**SuSE (gebruikt netconf)**:
1. Installeer het dnsmasq-pakket ("sudo Zypper install dnsmasq").
2. Schakel de dnsmasq-service (' systemctl Enable dnsmasq. service ') in.
3. Start de dnsmasq-service (systemctl start dnsmasq. service).
4. Bewerk "/etc/sysconfig/network/config" en wijzig NETCONFIG_DNS_FORWARDER = "" in "dnsmasq".
5. Update Dispatches. conf (' netconfig update ') om de cache in te stellen als de lokale DNS-resolver.

**CentOS door Rogue Wave software (voorheen openstaande logica; maakt gebruik van NetworkManager)**
1. Installeer het dnsmasq-pakket ("sudo yum install dnsmasq").
2. Schakel de dnsmasq-service (' systemctl Enable dnsmasq. service ') in.
3. Start de dnsmasq-service (systemctl start dnsmasq. service).
4. Voeg ' laten voorafgaan door Domain-name-servers 127.0.0.1; ' toe aan '/etc/dhclient-ETH0.conf '.
5. Start de netwerk service opnieuw (' service opnieuw starten van het netwerk ') om de cache in te stellen als de lokale DNS-resolver

> [!NOTE]
> : Het pakket ' dnsmasq ' is slechts een van de vele DNS-caches die beschikbaar zijn voor Linux. Voordat u het gebruikt, controleert u de geschiktheid van uw behoeften en is er geen andere cache geïnstalleerd.
>
>

**Nieuwe pogingen aan client zijde**

DNS is voornamelijk een UDP-protocol. Omdat het UDP-protocol geen aflevering van berichten garandeert, wordt het DNS-protocol zelf opnieuw geprobeerd. Elke DNS-client (besturings systeem) kan verschillende nieuwe logica vertonen, afhankelijk van de voor keur van de maker:

* Windows-besturings systemen worden na één seconde opnieuw geprobeerd, na een tweede, vier en een andere vier seconden.
* De standaard installatie van Linux wordt na vijf seconden herhaald.  U moet dit wijzigen om vijf keer met een interval van één seconde opnieuw te proberen.  

Als u de huidige instellingen op een virtuele Linux-machine wilt controleren, ' kat/etc/resolv.conf ', en Bekijk de regel ' opties ', bijvoorbeeld:

```config-conf
options timeout:1 attempts:5
```

Het bestand ungenerate. conf wordt automatisch gegenereerd en mag niet worden bewerkt. De specifieke stappen voor het toevoegen van de regel ' opties ' variëren per distributie:

**Ubuntu** (maakt gebruik van resolvconf)
1. Voeg de optie lijn toe aan '/etc/resolvconf/resolv.conf.d/head '.
2. Voer ' resolvconf-u ' uit om bij te werken.

**SuSE** (gebruikt netconf)
1. Voeg ' time-out: 1 pogingen: 5 ' toe aan de para meter NETCONFIG_DNS_RESOLVER_OPTIONS = ' ' in '/etc/sysconfig/network/config '.
2. Voer ' netconfig update ' uit om bij te werken.

**CentOS door Rogue Wave software (voorheen** openstaande logica) (maakt gebruik van NetworkManager)
1. ' RES_OPTIONS = ' time-out: 1 pogingen: 5 ' ' toevoegen aan '/etc/sysconfig/network '.
2. Voer ' service netwerk opnieuw starten ' uit om bij te werken.

## <a name="name-resolution-using-your-own-dns-server"></a>Naam omzetting met uw eigen DNS-server
Uw naam omzettings behoeften kunnen verder gaan dan de functies die door Azure worden geboden. U kunt bijvoorbeeld een DNS-omzetting tussen virtuele netwerken vereisen. Voor dit scenario kunt u uw eigen DNS-servers gebruiken.  

DNS-servers binnen een virtueel netwerk kunnen DNS-query's door sturen naar recursieve resolvers van Azure om hostnamen die zich in hetzelfde virtuele netwerk bevinden, op te lossen. Een DNS-server die wordt uitgevoerd in azure kan bijvoorbeeld reageren op DNS-query's voor de eigen DNS-zone bestanden en alle andere query's door sturen naar Azure. Met deze functionaliteit kunnen virtuele machines uw vermeldingen zien in uw zone bestanden en hostnamen die Azure biedt (via de doorstuur server). Toegang tot recursieve resolvers van Azure wordt geboden via de 168.63.129.16 van het virtuele IP-adres.

Door sturen via DNS zorgt er ook voor dat de DNS-omzetting mogelijk is tussen virtuele netwerken en dat uw on-premises machines hostnamen kunnen omzetten die Azure biedt. Om de hostnaam van een virtuele machine op te lossen, moet de virtuele machine van de DNS-server zich in hetzelfde virtuele netwerk bevinden en worden geconfigureerd voor het door sturen van hostname-query's naar Azure. Omdat het DNS-achtervoegsel in elk virtueel netwerk verschilt, kunt u voorwaardelijke regels voor door sturen gebruiken om DNS-query's naar het juiste virtuele netwerk te verzenden voor oplossing. In de volgende afbeelding ziet u twee virtuele netwerken en een on-premises netwerk met DNS-omzetting tussen virtuele netwerken met behulp van deze methode:

![DNS-omzetting tussen virtuele netwerken](./media/azure-dns/inter-vnet-dns.png)

Wanneer u naam omzetting gebruikt die door Azure wordt geboden, wordt het interne DNS-achtervoegsel aan elke virtuele machine verstrekt met behulp van DHCP. Wanneer u uw eigen naam omzettings oplossing gebruikt, wordt dit achtervoegsel niet verstrekt aan virtuele machines, omdat het achtervoegsel van invloed is op andere DNS-architecturen. Als u wilt verwijzen naar machines op FQDN of als u het achtervoegsel op uw virtuele machines wilt configureren, kunt u Power shell of de API gebruiken om het achtervoegsel te bepalen:

* Voor virtuele netwerken die worden beheerd door Azure Resource Manager, is het achtervoegsel beschikbaar via de resource van de [netwerk interface kaart](/rest/api/virtualnetwork/networkinterfaces) . U kunt ook de `azure network public-ip show <resource group> <pip name>` opdracht uitvoeren om de details van uw open bare IP-adres, inclusief de FQDN van de NIC, weer te geven.

Als het door sturen van query's naar Azure niet aan uw behoeften voldoet, moet u uw eigen DNS-oplossing opgeven.  Uw DNS-oplossing moet:

* Geef de juiste omzetting van hostnamen op, bijvoorbeeld via [DDNS](../../virtual-network/virtual-networks-name-resolution-ddns.md). Als u DDNS gebruikt, moet u het mogelijk zijn om DNS-record opruiming uit te scha kelen. DHCP-leases van Azure zijn erg lang en opruiming kan DNS-records voor tijdig verwijderen.
* Geef een geschikte recursieve oplossing op om omzetting van externe domein namen toe te staan.
* Toegankelijk zijn (TCP en UDP op poort 53) van de clients die het gebruikt en toegang hebben tot internet.
* Worden beveiligd tegen toegang vanaf internet om bedreigingen van externe agents te beperken.

> [!NOTE]
> Voor de beste prestaties moet u, wanneer u virtuele machines gebruikt in Azure DNS servers, IPv6 uitschakelen en een [openbaar IP-adres op exemplaar niveau](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip) toewijzen aan elke virtuele machine van de DNS-server.  
>
>
