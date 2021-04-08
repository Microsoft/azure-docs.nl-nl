---
title: Naamomzetting voor resources in virtuele Azure-netwerken
titlesuffix: Azure Virtual Network
description: Scenario's voor naam omzetting voor Azure IaaS, hybride oplossingen, tussen verschillende Cloud Services, Active Directory en het gebruik van uw eigen DNS-server.
services: virtual-network
documentationcenter: na
author: rohinkoul
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/2/2020
ms.author: rohink
ms.custom: fasttrack-edit
ms.openlocfilehash: bbaf2fb99f1268a752fab4322078b0566a054d30
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222850"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Naamomzetting voor resources in virtuele Azure-netwerken

Afhankelijk van hoe u Azure gebruikt om IaaS, PaaS en hybride oplossingen te hosten, moet u mogelijk de virtuele machines (Vm's) en andere resources die in een virtueel netwerk zijn geïmplementeerd, toestaan om met elkaar te communiceren. Hoewel u communicatie kunt inschakelen met behulp van IP-adressen, is het veel eenvoudiger om namen te gebruiken die eenvoudig kunnen worden onthouden en niet te wijzigen. 

Wanneer resources die in virtuele netwerken worden geïmplementeerd, domein namen moeten omzetten in interne IP-adressen, kunnen ze een van de volgende drie methoden gebruiken:

* [Azure DNS particuliere zones](../dns/private-dns-overview.md)
* [Door Azure meegeleverde naam omzetting](#azure-provided-name-resolution)
* [Naam omzetting die gebruikmaakt van uw eigen DNS-server](#name-resolution-that-uses-your-own-dns-server) (waarmee query's kunnen worden doorgestuurd naar de door Azure VERSCHAFTe DNS-servers)

Welk type naamomzetting u gebruikt, is afhankelijk van hoe uw resources met elkaar moeten communiceren. In de volgende tabel ziet u scenario's en bijbehorende oplossingen voor naam omzetting:

> [!NOTE]
> Azure DNS particuliere zones is de aanbevolen oplossing en biedt u flexibiliteit bij het beheren van uw DNS-zones en-records. Zie voor meer informatie [Azure DNS gebruiken voor privédomeinen](../dns/private-dns-overview.md).

> [!NOTE]
> Als u Azure meegeleverde DNS gebruikt, wordt het juiste DNS-achtervoegsel automatisch toegepast op uw virtuele machines. Voor alle andere opties moet u FQDN (Fully Qualified Domain names) gebruiken of het juiste DNS-achtervoegsel hand matig Toep assen op uw virtuele machines.

| **Scenario** | **Oplossing** | **DNS-achtervoegsel** |
| --- | --- | --- |
| Naam omzetting tussen virtuele machines die zich in hetzelfde virtuele netwerk bevinden, of Azure Cloud Services Role-instanties in dezelfde Cloud service. | [Azure DNS persoonlijke zones](../dns/private-dns-overview.md) of door [Azure aangelegde naam omzetting](#azure-provided-name-resolution) |Hostnaam of FQDN |
| Naam omzetting tussen Vm's in verschillende virtuele netwerken of rolinstanties in verschillende Cloud Services. |[Azure DNS particuliere zones](../dns/private-dns-overview.md) of, door de klant beheerde DNS-servers, waarmee query's tussen virtuele netwerken worden doorgestuurd voor omzetting door Azure (DNS-proxy). Zie [naam omzetting met uw eigen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Alleen FQDN |
| Naam omzetting van een Azure App Service (Web-app, functie of bot) met behulp van virtuele netwerk integratie met rolinstanties of Vm's in hetzelfde virtuele netwerk. |Door de klant beheerde DNS-servers query's door sturen tussen virtuele netwerken voor omzetting door Azure (DNS-proxy). Zie [naam omzetting met uw eigen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Alleen FQDN |
| Naam omzetting van App Service Web Apps naar Vm's in hetzelfde virtuele netwerk. |Door de klant beheerde DNS-servers query's door sturen tussen virtuele netwerken voor omzetting door Azure (DNS-proxy). Zie [naam omzetting met uw eigen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Alleen FQDN |
| Naam omzetting van App Service Web Apps in één virtueel netwerk naar virtuele machines in een ander virtueel netwerk. |Door de klant beheerde DNS-servers query's door sturen tussen virtuele netwerken voor omzetting door Azure (DNS-proxy). Zie [naam omzetting met uw eigen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Alleen FQDN |
| Omzetting van on-premises computer-en service namen van Vm's of rolinstanties in Azure. |Door de klant beheerde DNS-servers (on-premises domein controller, lokale alleen-lezen domein controller of een secundaire DNS-server die wordt gesynchroniseerd met zone overdrachten). Zie [naam omzetting met uw eigen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Alleen FQDN |
| Omzetting van Azure hostnamen van on-premises computers. |Query's door sturen naar een door de klant beheerde DNS-proxy server in het bijbehorende virtuele netwerk, stuurt de proxy server query's door naar Azure voor oplossing. Zie [naam omzetting met uw eigen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Alleen FQDN |
| Omgekeerde DNS voor interne Ip's. |[Azure DNS persoonlijke zones](../dns/private-dns-overview.md) of naam omzetting [van Azure,](#azure-provided-name-resolution) [met behulp van uw eigen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Niet van toepassing |
| Naam omzetting tussen Vm's of rolinstanties die zich in verschillende Cloud Services bevinden, niet in een virtueel netwerk. |Niet van toepassing. De connectiviteit tussen virtuele machines en rolinstanties in verschillende Cloud Services wordt niet ondersteund buiten een virtueel netwerk. |Niet van toepassing|

## <a name="azure-provided-name-resolution"></a>Door Azure meegeleverde naam omzetting

De door Azure geleverde naam omzetting biedt alleen elementaire gezaghebbende DNS-mogelijkheden. Als u deze optie gebruikt, worden de DNS-zone namen en records automatisch beheerd door Azure en kunt u de DNS-zone namen of de levens cyclus van DNS-records niet beheren. Als u een volledig functionele DNS-oplossing voor uw virtuele netwerken nodig hebt, moet u [Azure DNS particuliere zones](../dns/private-dns-overview.md) of door de [klant beheerde DNS-servers](#name-resolution-that-uses-your-own-dns-server)gebruiken.

Naast de resolutie van open bare DNS-namen biedt Azure een interne naam omzetting voor Vm's en rolinstanties die zich binnen hetzelfde virtuele netwerk of dezelfde Cloud service bevinden. Vm's en exemplaren in een Cloud service delen hetzelfde DNS-achtervoegsel, zodat de hostnaam alleen voldoende is. Maar in virtuele netwerken die zijn geïmplementeerd met het klassieke implementatie model, hebben verschillende Cloud Services verschillende DNS-achtervoegsels. In dit geval moet u de FQDN-naam voor het omzetten van namen tussen verschillende Cloud Services. In virtuele netwerken die zijn geïmplementeerd met het Azure Resource Manager-implementatie model, is het DNS-achtervoegsel consistent op alle virtuele machines in een virtueel netwerk, zodat de FQDN niet nodig is. DNS-namen kunnen worden toegewezen aan virtuele machines en netwerk interfaces. Hoewel voor de naam omzetting van Azure geen configuratie vereist is, is het niet de juiste keuze voor alle implementatie scenario's, zoals beschreven in de vorige tabel.

> [!NOTE]
> Wanneer u Cloud Services-Web-en-werk rollen gebruikt, hebt u ook toegang tot de interne IP-adressen van rolinstanties die gebruikmaken van de Azure Service Management-REST API. Zie de naslag informatie voor [Service Management-rest API](/previous-versions/azure/ee460799(v=azure.100)). Het adres is gebaseerd op de naam van de rol en het exemplaar nummer. 
>

### <a name="features"></a>Functies

De door Azure geleverde naam omzetting bevat de volgende functies:
* Gebruiks gemak. Er is geen configuratie vereist.
* Hoge beschikbaarheid. U hoeft geen clusters van uw eigen DNS-servers te maken en te beheren.
* U kunt de service in combi natie met uw eigen DNS-servers gebruiken om zowel lokale als Azure-hostnamen op te lossen.
* U kunt naam omzetting tussen virtuele machines en rolinstanties binnen dezelfde Cloud service gebruiken, zonder dat u een FQDN hoeft te hebben.
* U kunt naam omzetting tussen Vm's gebruiken in virtuele netwerken die gebruikmaken van het Azure Resource Manager-implementatie model, zonder dat hiervoor een FQDN nodig is. Voor virtuele netwerken in het klassieke implementatie model is een FQDN vereist wanneer u namen in verschillende Cloud Services wilt omzetten. 
* U kunt hostnamen gebruiken die uw implementaties het beste beschrijven, in plaats van met automatisch gegenereerde namen te werken.

### <a name="considerations"></a>Overwegingen

Punten waarmee u rekening moet houden wanneer u door Azure meegeleverde naam omzetting gebruikt:
* Het DNS-achtervoegsel dat door Azure is gemaakt, kan niet worden gewijzigd.
* De DNS-zoek opdracht is binnen het bereik van een virtueel netwerk. DNS-namen die zijn gemaakt voor één virtuele netwerken kunnen niet worden omgezet van andere virtuele netwerken.
* U kunt uw eigen records niet hand matig registreren.
* WINS en NetBIOS worden niet ondersteund. U kunt uw Vm's niet zien in Windows Verkenner.
* Hostnamen moeten compatibel zijn met DNS. Namen mogen alleen 0-9, a-z en '-' bevatten en mag niet beginnen of eindigen met een-.
* DNS-query verkeer wordt beperkt voor elke virtuele machine. Beperking is niet van invloed op de meeste toepassingen. Als de aanvraag beperking wordt waargenomen, moet u ervoor zorgen dat caching aan client zijde is ingeschakeld. Zie [DNS-client configuratie](#dns-client-configuration)voor meer informatie.
* Alleen Vm's in de eerste 180-Cloud Services zijn geregistreerd voor elk virtueel netwerk in een klassiek implementatie model. Deze limiet is niet van toepassing op virtuele netwerken in Azure Resource Manager.
* Het Azure DNS IP-adres is 168.63.129.16. Dit is een statisch IP-adres dat niet wordt gewijzigd.

### <a name="reverse-dns-considerations"></a>Overwegingen voor omgekeerde DNS
Omgekeerde DNS wordt ondersteund in alle virtuele netwerken op basis van ARM. U kunt reverse DNS-query's (PTR-query's) uitgeven om IP-adressen van virtuele machines toe te wijzen aan de FQDN-namen van virtuele machines.
* Alle PTR-query's voor IP-adressen van virtuele machines retour neren FQDN-namen van het formulier \[ vmname \] . internal.cloudapp.net
* Forward lookup op FQDN-namen van \[ het formulier vmname \] . internal.cloudapp.net wordt omgezet naar het IP-adres dat aan de virtuele machine is toegewezen.
* Als het virtuele netwerk is gekoppeld aan een [Azure DNS particuliere zones](../dns/private-dns-overview.md) als een virtuele registratie-netwerk, retour neren de reverse DNS-query's twee records. Eén record heeft de indeling \[ vmname \] . [ privatednszonename] en de andere hebben de vorm \[ vmname \] . internal.cloudapp.net
* Achterwaartse DNS-zoek opdracht is binnen het bereik van een bepaald virtueel netwerk, zelfs als deze is gekoppeld aan andere virtuele netwerken. Omgekeerde DNS-query's (PTR-query's) voor IP-adressen van virtuele machines die zich in gekoppelde virtuele netwerken bevinden, wordt NXDOMAIN geretourneerd.
* Als u omgekeerde DNS-functie in een virtueel netwerk wilt uitschakelen, kunt u dit doen door een zone voor reverse lookup te maken met behulp van [Azure DNS particuliere zones](../dns/private-dns-overview.md) en deze zone aan uw virtuele netwerk te koppelen. Als de IP-adres ruimte van uw virtuele netwerk bijvoorbeeld 10.20.0.0/16 is, kunt u een lege privé-DNS-zone 20.10.in-addr. arpa maken en deze koppelen aan het virtuele netwerk. Wanneer u de zone koppelt aan uw virtuele netwerk, moet u automatische registratie op de koppeling uitschakelen. Deze zone overschrijft de standaard zones voor reverse lookup voor het virtuele netwerk en omdat deze zone leeg is, ontvangt u NXDOMAIN voor uw omgekeerde DNS-query's. Raadpleeg onze [Snelstartgids](../dns/private-dns-getstarted-portal.md) voor meer informatie over het maken van een privé-DNS-zone en het koppelen van deze aan een virtueel netwerk.

> [!NOTE]
> Als u een achterwaartse DNS-zoek opdracht wilt begrenzen over een virtueel netwerk, kunt u een zone voor reverse lookup (in-addr. arpa) [Azure DNS particuliere zones](../dns/private-dns-overview.md) maken en deze koppelen aan meerdere virtuele netwerken. U hoeft de omgekeerde DNS-records voor de virtuele machines echter niet hand matig te beheren.
>


## <a name="dns-client-configuration"></a>Configuratie van de DNS-client

Deze sectie is van toepassing op caching aan client zijde en aan client zijde nieuwe pogingen.

### <a name="client-side-caching"></a>Caching aan client zijde

Niet elke DNS-query moet via het netwerk worden verzonden. Caching aan de client zijde helpt de latentie te verminderen en de tolerantie voor netwerk problemen te verbeteren door terugkerende DNS-query's uit een lokale cache op te lossen. DNS-records bevatten een TTL-mechanisme (time-to-Live) waarmee de cache de record zo lang mogelijk kan opslaan zonder dat dit van invloed is op de versheid van de record. Caching aan de client zijde is dus geschikt voor de meeste situaties.

De standaard Windows DNS-client heeft een ingebouwde DNS-cache. Voor sommige Linux-distributies is de standaard instelling geen caching. Als u merkt dat er al geen lokale cache is, voegt u een DNS-cache toe aan elke virtuele Linux-machine.

Er zijn een aantal verschillende DNS-cache pakketten beschikbaar (zoals dnsmasq). U kunt als volgt dnsmasq installeren op de meest voorkomende distributies:

* **Ubuntu (maakt gebruik van resolvconf)**:
  * Installeer het dnsmasq-pakket met `sudo apt-get install dnsmasq` .
* **SuSE (gebruikt netconf)**:
  * Installeer het dnsmasq-pakket met `sudo zypper install dnsmasq` .
  * Schakel de dnsmasq-service in met `systemctl enable dnsmasq.service` . 
  * Start de dnsmasq-service met `systemctl start dnsmasq.service` . 
  * Bewerk **/etc/sysconfig/network/config** en wijzig *NETCONFIG_DNS_FORWARDER = ""* in *dnsmasq*.
  * Update Resolve. conf with `netconfig update` om de cache in te stellen als de lokale DNS-resolver.
* **CentOS (maakt gebruik van NetworkManager)**:
  * Installeer het dnsmasq-pakket met `sudo yum install dnsmasq` .
  * Schakel de dnsmasq-service in met `systemctl enable dnsmasq.service` .
  * Start de dnsmasq-service met `systemctl start dnsmasq.service` .
  * Voeg *laten voorafgaan door domein naam-servers 127.0.0.1;* toe aan **/etc/dhclient-ETH0.conf**.
  * Start de netwerk service opnieuw met `service network restart` om de cache in te stellen als de lokale DNS-resolver.

> [!NOTE]
> Het dnsmasq-pakket is slechts een van de vele DNS-caches die beschikbaar zijn voor Linux. Controleer voordat u het gebruikt de geschiktheid voor uw specifieke behoeften en controleer of er geen andere cache is geïnstalleerd.

    
### <a name="client-side-retries"></a>Nieuwe pogingen aan client zijde

DNS is voornamelijk een UDP-protocol. Omdat het UDP-protocol geen aflevering van berichten garandeert, wordt de logica voor opnieuw proberen verwerkt in het DNS-protocol zelf. Elke DNS-client (besturings systeem) kan verschillende pogings logica vertonen, afhankelijk van de voor keur van de maker:

* Windows-besturings systemen worden na één seconde opnieuw geprobeerd, en daarna weer na twee seconden, vier seconden en nog eens vier seconden. 
* De standaard installatie van Linux wordt na vijf seconden herhaald. Het is raadzaam om de specificaties voor opnieuw proberen te wijzigen in vijf keer, met een interval van één seconde.

Controleer de huidige instellingen op een Linux-VM met `cat /etc/resolv.conf` . Bekijk de *optie* regel, bijvoorbeeld:

```bash
options timeout:1 attempts:5
```

Het bestand ungenerate. conf wordt doorgaans automatisch gegenereerd en mag niet worden bewerkt. De specifieke stappen voor het toevoegen van de *optie* regel variëren per distributie:

* **Ubuntu** (maakt gebruik van resolvconf):
  1. Voeg de *optie* lijn toe aan **/etc/resolvconf/resolv.conf.d/tail**.
  2. Voer uit `resolvconf -u` om bij te werken.
* **SuSE** (gebruikt netconf):
  1. *Time-out toevoegen: 1 pogingen: 5* tot de para meter **NETCONFIG_DNS_RESOLVER_OPTIONS = ""** in **/etc/sysconfig/network/config**.
  2. Voer uit `netconfig update` om bij te werken.
* **CentOS** (maakt gebruik van NetworkManager):
  1. *Echo ' Options time-out: 1 pogingen: 5 '* toevoegen aan **/etc/NetworkManager/dispatcher.d/11-dhclient**.
  2. Bijwerken met `service network restart` .

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Naam omzetting die gebruikmaakt van uw eigen DNS-server

Deze sectie heeft betrekking op Vm's, rolinstanties en web-apps.

### <a name="vms-and-role-instances"></a>Vm's en rolinstanties

Uw naam omzettings behoeften kunnen verder gaan dan de functies van Azure. U moet bijvoorbeeld micro soft Windows Server Active Directory domeinen gebruiken om DNS-namen te kunnen omzetten tussen virtuele netwerken. Azure biedt de mogelijkheid om uw eigen DNS-servers te gebruiken om deze scenario's te kunnen behandelen.

DNS-servers binnen een virtueel netwerk kunnen DNS-query's door sturen naar recursieve resolvers in Azure. Hierdoor kunt u hostnamen omzetten in dat virtuele netwerk. Een domein controller (DC) die in azure wordt uitgevoerd, kan bijvoorbeeld reageren op DNS-query's voor de domeinen en alle andere query's door sturen naar Azure. Met doorgestuurde query's kunnen Vm's zowel uw on-premises resources (via de DC) als door Azure verschafte hostnamen (via de doorstuur server) zien. Toegang tot recursieve resolvers in azure wordt geboden via de 168.63.129.16 van het virtuele IP-adres.

Door sturen via DNS zorgt er ook voor dat de DNS-omzetting tussen virtuele netwerken mogelijk is en dat uw on-premises machines door Azure gedefinieerde hostnamen kunnen omzetten. Om de hostnaam van een VM op te lossen, moet de virtuele machine van de DNS-server zich in hetzelfde virtuele netwerk bevinden en worden geconfigureerd voor het door sturen van hostname-query's naar Azure. Omdat het DNS-achtervoegsel in elk virtueel netwerk verschilt, kunt u voorwaardelijke regels voor door sturen gebruiken om DNS-query's naar het juiste virtuele netwerk te verzenden voor oplossing. In de volgende afbeelding ziet u twee virtuele netwerken en een on-premises netwerk met DNS-omzetting tussen virtuele netwerken met behulp van deze methode. Een voor beeld van een DNS-doorstuur server is beschikbaar in de [Azure Quick Start-sjablonen galerie](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) en [github](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> Met een rolinstantie kan naam omzetting van Vm's binnen hetzelfde virtuele netwerk worden uitgevoerd. Dit doet u met behulp van de FQDN-naam, die bestaat uit de hostnaam van de virtuele machine en het DNS-achtervoegsel van de **Internal.cloudapp.net** . In dit geval wordt naam omzetting echter alleen geslaagd als voor de rolinstantie de VM-naam is gedefinieerd in het [Role-schema (cscfg-bestand)](/previous-versions/azure/reference/jj156212(v=azure.100)).
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> U moet de volgende methoden gebruiken om de naam omzetting van Vm's in een ander virtueel netwerk (FQDN met het **Internal.cloudapp.net** -achtervoegsel) te kunnen uitvoeren met behulp van de methode die in deze sectie wordt beschreven (aangepaste DNS-servers die worden doorgestuurd tussen de twee virtuele netwerken).
>

![Diagram van DNS tussen virtuele netwerken](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Wanneer u door Azure geleverde naam omzetting gebruikt, biedt Azure Dynamic Host Configuration Protocol (DHCP) een intern DNS-achtervoegsel (**. internal.cloudapp.net**) voor elke virtuele machine. Met dit achtervoegsel kunnen hostnamen worden omgezet, omdat de naam van de hostnaam zich in de **Internal.cloudapp.net** zone bevindt. Wanneer u uw eigen naam omzettings oplossing gebruikt, wordt dit achtervoegsel niet aan Vm's verstrekt omdat het een conflict veroorzaakt met andere DNS-architecturen (zoals scenario's die zijn gekoppeld aan een domein). In plaats daarvan biedt Azure een niet-functionerende tijdelijke aanduiding (*reddog.Microsoft.com*).

Indien nodig kunt u het interne DNS-achtervoegsel bepalen met behulp van Power shell of de API:

* Voor virtuele netwerken in Azure Resource Manager-implementatie modellen is het achtervoegsel beschikbaar via de [netwerk interface rest API](/rest/api/virtualnetwork/networkinterfaces), de Power shell [-cmdlet Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) en de opdracht [AZ Network NIC Azure cli weer geven](/cli/azure/network/nic#az-network-nic-show) .
* In klassieke implementatie modellen is het achtervoegsel beschikbaar via de [Get-API](/previous-versions/azure/reference/ee460804(v=azure.100)) -aanroep voor de implementatie of met de cmdlet [Get-AzureVM-debug](/powershell/module/servicemanagement/azure.service/get-azurevm) .

Als het door sturen van query's naar Azure niet aan uw behoeften voldoet, moet u uw eigen DNS-oplossing opgeven. Uw DNS-oplossing moet:

* Geef bijvoorbeeld de juiste naam omzetting via [DDNS](virtual-networks-name-resolution-ddns.md)op. Als u DDNS gebruikt, moet u mogelijk de DNS-record opruiming uitschakelen. Azure DHCP-leases zijn lang en bij het opruimen kunnen DNS-records voor tijdig worden verwijderd. 
* Geef een geschikte recursieve oplossing op om omzetting van externe domein namen toe te staan.
* Toegankelijk zijn (TCP en UDP op poort 53) van de clients die het verzendt en toegang hebben tot internet.
* Worden beveiligd tegen toegang vanaf internet, om bedreigingen van externe agents te beperken.

> [!NOTE]
> Voor de beste prestaties moet IPv6 worden uitgeschakeld wanneer u virtuele Azure-machines als DNS-servers gebruikt.

### <a name="web-apps"></a>Web-apps
Stel dat u naam omzetting moet uitvoeren vanuit uw web-app die is gebouwd met behulp van App Service, gekoppeld aan een virtueel netwerk, op Vm's in hetzelfde virtuele netwerk. Voer de volgende stappen uit, naast het instellen van een aangepaste DNS-server met een DNS-forwarder die query's doorstuurt naar Azure (virtueel IP-adres 168.63.129.16):
1. Schakel de virtuele netwerk integratie in voor uw web-app, als deze nog niet is gemaakt, zoals wordt beschreven in [uw app integreren met een virtueel netwerk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
2. Selecteer in de Azure Portal, voor het App Service-abonnement dat als host fungeert voor de web-app, de optie **netwerk synchroniseren** onder **netwerken** **Virtual Network integratie**.

    ![Scherm opname van naam omzetting van virtueel netwerk](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Als u naam omzetting moet uitvoeren vanuit uw web-app die is gebouwd met App Service, gekoppeld aan een virtueel netwerk en virtuele machines in een ander virtueel netwerk, moet u als volgt aangepaste DNS-servers gebruiken in beide virtuele netwerken:

* Stel een DNS-server in het virtuele doel netwerk in, op een virtuele machine die ook query's kan door sturen naar de recursieve resolver in azure (virtueel IP-adres 168.63.129.16). Een voor beeld van een DNS-doorstuur server is beschikbaar in de [Azure Quick Start-sjablonen galerie](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) en [github](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Stel een DNS-doorstuur server in het virtuele bron netwerk op een virtuele machine in. Configureer deze DNS-doorstuur server voor het door sturen van query's naar de DNS-servers in uw doel-virtueel netwerk.
* Configureer de DNS-bron server in de instellingen van uw virtuele bron netwerk.
* Schakel de integratie van het virtuele netwerk voor uw web-app in om een koppeling met het virtuele bron netwerk te maken, gevolgd door de instructies in [uw app integreren met een virtueel netwerk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Selecteer in de Azure Portal, voor het App Service-abonnement dat als host fungeert voor de web-app, de optie **netwerk synchroniseren** onder **netwerken** **Virtual Network integratie**.

## <a name="specify-dns-servers"></a>DNS-servers opgeven
Wanneer u uw eigen DNS-servers gebruikt, biedt Azure de mogelijkheid om meerdere DNS-servers per virtueel netwerk op te geven. U kunt ook meerdere DNS-servers opgeven per netwerkinterface (voor Azure Resource Manager) of per cloudservice (voor het klassieke implementatiemodel). DNS-servers die zijn opgegeven voor een netwerk interface of Cloud service krijgen voor rang op DNS-servers die zijn opgegeven voor het virtuele netwerk.

> [!NOTE]
> De eigenschappen van de netwerk verbinding, zoals DNS-server-Ip's, mogen niet rechtstreeks worden bewerkt in Vm's. Dit komt doordat ze tijdens het herstellen van de service mogelijk worden gewist wanneer de virtuele netwerk adapter wordt vervangen. Dit is van toepassing op virtuele Windows-en Linux-machines.

Wanneer u het Azure Resource Manager-implementatie model gebruikt, kunt u DNS-servers voor een virtueel netwerk en een netwerk interface opgeven. Zie [een virtueel netwerk beheren](manage-virtual-network.md) en [een netwerk interface beheren](virtual-network-network-interface.md)voor meer informatie.

> [!NOTE]
> Als u een aangepaste DNS-server voor uw virtuele netwerk kiest, moet u ten minste één IP-adres van de DNS-server opgeven. anders wordt de configuratie door het virtuele netwerk genegeerd en wordt in plaats daarvan Azure-DNS gebruikt.

Wanneer u het klassieke implementatie model gebruikt, kunt u DNS-servers voor het virtuele netwerk opgeven in het Azure Portal of het [netwerk configuratie bestand](/previous-versions/azure/reference/jj157100(v=azure.100)). Voor Cloud Services kunt u DNS-servers opgeven via het [Service configuratie bestand](/previous-versions/azure/reference/ee758710(v=azure.100)) of met behulp van Power shell, met [New-AzureVM](/powershell/module/servicemanagement/azure.service/new-azurevm).

> [!NOTE]
> Als u de DNS-instellingen voor een virtueel netwerk of een virtuele machine die al is geïmplementeerd, wijzigt, moet u de DHCP-lease vernieuwing uitvoeren op alle betrokken Vm's in het virtuele netwerk om de nieuwe DNS-instellingen van kracht te laten worden. Voor Vm's waarop het Windows-besturings systeem wordt uitgevoerd, kunt u dit doen door `ipconfig /renew` rechtstreeks in de virtuele machine te typen. De stappen variëren afhankelijk van het besturings systeem. Raadpleeg de relevante documentatie voor het type besturings systeem.

## <a name="next-steps"></a>Volgende stappen

Azure Resource Manager implementatie model:

* [Een virtueel netwerk beheren](manage-virtual-network.md)
* [Een netwerk interface beheren](virtual-network-network-interface.md)

Klassiek implementatie model:

* [Configuratie schema van Azure-service](/previous-versions/azure/reference/ee758710(v=azure.100))
* [Configuratie schema Virtual Network](/previous-versions/azure/reference/jj157100(v=azure.100))
* [Een Virtual Network configureren met behulp van een netwerk configuratie bestand](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file)