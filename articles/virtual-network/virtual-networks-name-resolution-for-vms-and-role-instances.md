---
title: Naamomzetting voor resources in virtuele Azure-netwerken
titlesuffix: Azure Virtual Network
description: Naamoplossingsscenario's voor Azure IaaS, hybride oplossingen, tussen verschillende cloudservices, Active Directory en het gebruik van uw eigen DNS-server.
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
ms.openlocfilehash: 80a0b4634a2e84181271b515d2f6f63271cce7f2
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784961"
---
# <a name="name-resolution-for-resources-in-azure-virtual-networks"></a>Naamomzetting voor resources in virtuele Azure-netwerken

Afhankelijk van hoe u Azure gebruikt om IaaS-, PaaS- en hybride oplossingen te hosten, moet u mogelijk toestaan dat de virtuele machines (VM's) en andere resources die in een virtueel netwerk zijn geïmplementeerd, met elkaar kunnen communiceren. Hoewel u communicatie kunt inschakelen met behulp van IP-adressen, is het veel eenvoudiger om namen te gebruiken die gemakkelijk kunnen worden onthouden en die niet veranderen. 

Wanneer resources die zijn geïmplementeerd in virtuele netwerken domeinnamen moeten oplossen naar interne IP-adressen, kunnen ze een van de volgende drie methoden gebruiken:

* [Azure DNS privézones instellen](../dns/private-dns-overview.md)
* [Door Azure geleverde naamoplossing](#azure-provided-name-resolution)
* [Naamresolutie die gebruikmaakt van uw eigen DNS-server](#name-resolution-that-uses-your-own-dns-server) (die mogelijk query's doorsturen naar de door Azure geleverde DNS-servers)

Welk type naamomzetting u gebruikt, is afhankelijk van hoe uw resources met elkaar moeten communiceren. De volgende tabel illustreert scenario's en bijbehorende oplossingen voor naamoplossing:

> [!NOTE]
> Azure DNS privézones is de voorkeursoplossing en biedt u flexibiliteit bij het beheren van uw DNS-zones en -records. Zie voor meer informatie [Azure DNS gebruiken voor privédomeinen](../dns/private-dns-overview.md).

> [!NOTE]
> Als u door Azure geleverde DNS gebruikt, wordt het juiste DNS-achtervoegsel automatisch toegepast op uw virtuele machines. Voor alle andere opties moet u Fully Qualified Domain Names (FQDN) gebruiken of handmatig het juiste DNS-achtervoegsel toepassen op uw virtuele machines.

| **Scenario** | **Oplossing** | **DNS-achtervoegsel** |
| --- | --- | --- |
| Naamom oplossing tussen VM's in hetzelfde virtuele netwerk of Azure Cloud Services-instanties in dezelfde cloudservice. | [Azure DNS privézones of](../dns/private-dns-overview.md) door [Azure geleverde naamoplossing](#azure-provided-name-resolution) |Hostnaam of FQDN |
| Naamom oplossing tussen VM's in verschillende virtuele netwerken of rol instances in verschillende cloudservices. |[Azure DNS privézones of](../dns/private-dns-overview.md) door de klant beheerde DNS-servers die query's doorsturen tussen virtuele netwerken voor oplossing door Azure (DNS-proxy). Zie [Naamresolutie met uw eigen DNS-server.](#name-resolution-that-uses-your-own-dns-server) |Alleen FQDN |
| Naamom oplossing van een Azure App Service (web-app, functie of bot) met behulp van integratie van virtuele netwerken met rol-exemplaren of VM's in hetzelfde virtuele netwerk. |Door de klant beheerde DNS-servers die query's doorsturen tussen virtuele netwerken voor oplossing door Azure (DNS-proxy). Zie [Naamresolutie met uw eigen DNS-server.](#name-resolution-that-uses-your-own-dns-server) |Alleen FQDN |
| Naamom oplossing van App Service Web Apps naar VM's in hetzelfde virtuele netwerk. |Door de klant beheerde DNS-servers die query's doorsturen tussen virtuele netwerken voor oplossing door Azure (DNS-proxy). Zie [Naamresolutie met uw eigen DNS-server.](#name-resolution-that-uses-your-own-dns-server) |Alleen FQDN |
| Naamom oplossing van App Service Web Apps in één virtueel netwerk naar VM's in een ander virtueel netwerk. |Door de klant beheerde DNS-servers die query's doorsturen tussen virtuele netwerken voor oplossing door Azure (DNS-proxy). Zie [Naamresolutie met uw eigen DNS-server.](#name-resolution-that-uses-your-own-dns-server) |Alleen FQDN |
| Oplossing van on-premises computer- en servicenamen van VM's of rol instances in Azure. |Door de klant beheerde DNS-servers (bijvoorbeeld on-premises domeincontroller, lokale alleen-lezen domeincontroller of een secundaire DNS-server die is gesynchroniseerd met zoneoverdrachten). Zie [Naamresolutie met uw eigen DNS-server.](#name-resolution-that-uses-your-own-dns-server) |Alleen FQDN |
| Oplossing van Azure-hostnamen van on-premises computers. |Doorsturen van query's naar een door de klant beheerde DNS-proxyserver in het bijbehorende virtuele netwerk, de proxyserver doorsturen query's naar Azure voor oplossing. Zie [Naamresolutie met uw eigen DNS-server.](#name-resolution-that-uses-your-own-dns-server) |Alleen FQDN |
| Omgekeerde DNS voor interne IP's. |[Azure DNS privézones of](../dns/private-dns-overview.md) door [Azure geleverde naamresolutie of](#azure-provided-name-resolution) [naamresolutie met behulp van uw eigen DNS-server](#name-resolution-that-uses-your-own-dns-server). |Niet van toepassing |
| Naamom oplossing tussen VM's of rol instances die zich in verschillende cloudservices bevinden, niet in een virtueel netwerk. |Niet van toepassing. Connectiviteit tussen VM's en rol instances in verschillende cloudservices wordt niet ondersteund buiten een virtueel netwerk. |Niet van toepassing|

## <a name="azure-provided-name-resolution"></a>Door Azure geleverde naamoplossing

De door Azure geleverde naamresolutie biedt alleen elementaire gezaghebbende DNS-mogelijkheden. Als u deze optie gebruikt, worden de DNS-zonenamen en -records automatisch beheerd door Azure en kunt u de DNS-zonenamen of de levenscyclus van DNS-records niet beheren. Als u een volledig uitgeruste DNS-oplossing voor uw virtuele netwerken nodig hebt, moet u Azure DNS [privézones](../dns/private-dns-overview.md) of door [de klant beheerde DNS-servers gebruiken.](#name-resolution-that-uses-your-own-dns-server)

Naast de resolutie van openbare DNS-namen biedt Azure interne naamom oplossing voor VM's en rol instances die zich in hetzelfde virtuele netwerk of dezelfde cloudservice bevinden. VM's en exemplaren in een cloudservice delen hetzelfde DNS-achtervoegsel, zodat alleen de hostnaam voldoende is. Maar in virtuele netwerken die zijn geïmplementeerd met behulp van het klassieke implementatiemodel, hebben verschillende cloudservices verschillende DNS-achtervoegsels. In dit geval hebt u de FQDN nodig om namen tussen verschillende cloudservices op te lossen. In virtuele netwerken die zijn geïmplementeerd met behulp van het Azure Resource Manager-implementatiemodel, is het DNS-achtervoegsel consistent voor alle virtuele machines in een virtueel netwerk, waardoor de FQDN niet nodig is. DNS-namen kunnen worden toegewezen aan zowel VM's als netwerkinterfaces. Hoewel voor naamoplossing door Azure geen configuratie is vereist, is dit niet de juiste keuze voor alle implementatiescenario's, zoals beschreven in de vorige tabel.

> [!NOTE]
> Wanneer u web- en werkrollen voor cloudservices gebruikt, hebt u ook toegang tot de interne IP-adressen van rol instances met behulp van de Azure Service Management-REST API. Zie Service Management REST API [Reference voor meer informatie.](/previous-versions/azure/ee460799(v=azure.100)) Het adres is gebaseerd op de rolnaam en het exemplaarnummer. 
>

### <a name="features"></a>Functies

De door Azure geleverde naamoplossing bevat de volgende functies:
* Gebruiksgemak. Er is geen configuratie vereist.
* Hoge beschikbaarheid. U hoeft geen clusters van uw eigen DNS-servers te maken en beheren.
* U kunt de service gebruiken in combinatie met uw eigen DNS-servers om zowel on-premises als Azure-hostnamen op te lossen.
* U kunt naamom oplossing gebruiken tussen VM's en rol-exemplaren binnen dezelfde cloudservice, zonder dat u een FQDN nodig hebt.
* U kunt naamomzet gebruiken tussen virtuele machines in virtuele netwerken die gebruikmaken van het Azure Resource Manager implementatiemodel, zonder dat u een FQDN nodig hebt. Virtuele netwerken in het klassieke implementatiemodel vereisen een FQDN bij het oplossen van namen in verschillende cloudservices. 
* U kunt hostnamen gebruiken die uw implementaties het beste beschrijven, in plaats van te werken met automatisch gegenereerde namen.

### <a name="considerations"></a>Overwegingen

Punten om rekening mee te houden wanneer u door Azure geleverde naamoplossing gebruikt:
* Het door Azure gemaakte DNS-achtervoegsel kan niet worden gewijzigd.
* DNS-zoekactie is beperkt tot een virtueel netwerk. DNS-namen die zijn gemaakt voor één virtueel netwerk, kunnen niet worden opgelost vanuit andere virtuele netwerken.
* U kunt uw eigen records niet handmatig registreren.
* WINS en NetBIOS worden niet ondersteund. U kunt uw VM's niet zien in Windows Verkenner.
* Hostnamen moeten compatibel zijn met DNS. Namen mogen alleen 0-9, a-z en '-' gebruiken en mogen niet beginnen of eindigen met een '-'.
* DNS-queryverkeer wordt beperkt voor elke VM. Beperking heeft geen invloed op de meeste toepassingen. Als aanvraagbeperking wordt waargenomen, moet u ervoor zorgen dat caching aan de clientzijde is ingeschakeld. Zie [DNS-clientconfiguratie voor meer informatie.](#dns-client-configuration)
* Alleen VM's in de eerste 180 cloudservices worden geregistreerd voor elk virtueel netwerk in een klassiek implementatiemodel. Deze limiet geldt niet voor virtuele netwerken in Azure Resource Manager.
* Het Azure DNS IP-adres is 168.63.129.16. Dit is een statisch IP-adres en wordt niet gewijzigd.

### <a name="reverse-dns-considerations"></a>Overwegingen voor omgekeerde DNS
Omgekeerde DNS wordt ondersteund in alle virtuele NETWERKEN op basis van ARM. U kunt omgekeerde DNS-query's (PTR-query's) uitgeven om IP-adressen van virtuele machines toe te voegen aan FQDN's van virtuele machines.
* Alle PTR-query's voor IP-adressen van virtuele machines retourneren FQDN's van de \[ vorm vmname \] .internal.cloudapp.net
* Forward lookup op FQDN's van de vorm vmname .internal.cloudapp.net worden opgelost naar \[ het IP-adres dat is toegewezen aan de virtuele \] machine.
* Als het virtuele netwerk is gekoppeld aan een [Azure DNS privézones](../dns/private-dns-overview.md) als een virtueel registratienetwerk, retourneren de omgekeerde DNS-query's twee records. Eén record heeft de vorm \[ vmname \] .[ privatednszonename] en de andere heeft de vorm \[ vmname \] .internal.cloudapp.net
* Omgekeerde DNS-zoekactie is beperkt tot een bepaald virtueel netwerk, zelfs als het is verbonden met andere virtuele netwerken. Omgekeerde DNS-query's (PTR-query's) voor IP-adressen van virtuele machines die zich in peered virtuele netwerken bevinden, retourneren NXDOMAIN.
* Als u omgekeerde DNS-functie in een virtueel netwerk wilt uitschakelen, kunt u dit doen door een zone voor reverse lookup te maken met behulp van [Azure DNS privézones](../dns/private-dns-overview.md) en deze zone te koppelen aan uw virtuele netwerk. Als de IP-adresruimte van uw virtuele netwerk bijvoorbeeld 10.20.0.0/16 is, kunt u een lege privé-DNS-zone maken 20.10.in-addr.arpa en deze koppelen aan het virtuele netwerk. Tijdens het koppelen van de zone aan uw virtuele netwerk moet u automatische registratie op de koppeling uitschakelen. Deze zone overschrijven de standaard zones voor reverse lookup voor het virtuele netwerk en omdat deze zone leeg is, krijgt u NXDOMAIN voor uw omgekeerde DNS-query's. Zie onze [Snelstartgids voor](../dns/private-dns-getstarted-portal.md) meer informatie over het maken van een privé-DNS-zone en het koppelen ervan aan een virtueel netwerk.

> [!NOTE]
> Als u wilt dat omgekeerde DNS-zoekactie het virtuele netwerk overspant, kunt u een zone voor reverse lookup (in-addr.arpa) maken [Azure DNS privézones](../dns/private-dns-overview.md) en deze aan meerdere virtuele netwerken koppelt. U moet de omgekeerde DNS-records voor de virtuele machines echter handmatig beheren.
>


## <a name="dns-client-configuration"></a>DNS-clientconfiguratie

In deze sectie worden caching aan de clientzijde en nieuwe aan de clientzijde opnieuw proberen beslaat.

### <a name="client-side-caching"></a>Caching aan clientzijde

Niet elke DNS-query hoeft via het netwerk te worden verzonden. Caching aan de clientzijde helpt de latentie te verminderen en de tolerantie voor netwerkproblemen te verbeteren door terugkerende DNS-query's uit een lokale cache op te lossen. DNS-records bevatten een TTL-mechanisme (time-to-live), waarmee de cache de record zo lang mogelijk kan opslaan zonder dat dit van invloed is op de nieuwheid van records. Caching aan de clientzijde is dus geschikt voor de meeste situaties.

De standaard Windows DNS-client heeft een ingebouwde DNS-cache. Sommige Linux-distributies bevatten standaard geen caching. Als u vindt dat er nog geen lokale cache is, voegt u een DNS-cache toe aan elke Linux-VM.

Er zijn een aantal verschillende DNS-cachingpakketten beschikbaar (zoals dnsmasq). U kunt als volgende dnsmasq installeren op de meest voorkomende distributies:

* **Ubuntu (maakt gebruik van resolvconf)**:
  * Installeer het pakket dnsmasq met `sudo apt-get install dnsmasq` .
* **SUSE (maakt gebruik van netconf)**:
  * Installeer het pakket dnsmasq met `sudo zypper install dnsmasq` .
  * Schakel de dnsmasq-service in met `systemctl enable dnsmasq.service` . 
  * Start de service dnsmasq met `systemctl start dnsmasq.service` . 
  * Bewerk **/etc/sysconfig/network/config** en wijzig *NETCONFIG_DNS_FORWARDER="" in* *dnsmasq*.
  * Werk resolve.conf bij met `netconfig update` om de cache in te stellen als de lokale DNS-resolver.
* **CentOS (maakt gebruik van NetworkManager)**:
  * Installeer het pakket dnsmasq met `sudo yum install dnsmasq` .
  * Schakel de dnsmasq-service in met `systemctl enable dnsmasq.service` .
  * Start de service dnsmasq met `systemctl start dnsmasq.service` .
  * Voeg *prepend domain-name-servers 127.0.0.1;* toe aan **/etc/dhclient-eth0.conf.**
  * Start de netwerkservice opnieuw op met `service network restart` om de cache in te stellen als de lokale DNS-resolver.

> [!NOTE]
> Het pakket dnsmasq is slechts een van de vele DNS-caches die beschikbaar zijn voor Linux. Voordat u deze gebruikt, controleert u of deze geschikt is voor uw specifieke behoeften en controleert u of er geen andere cache is geïnstalleerd.

    
### <a name="client-side-retries"></a>Nieuwe nieuwe proberen aan de clientzijde

DNS is voornamelijk een UDP-protocol. Omdat het UDP-protocol berichtbezorging niet garandeert, wordt logica voor opnieuw proberen verwerkt in het DNS-protocol zelf. Elke DNS-client (besturingssysteem) kan verschillende logica voor opnieuw proberen vertonen, afhankelijk van de voorkeur van de maker:

* Windows-besturingssystemen proberen het na één seconde opnieuw en vervolgens na nog eens twee, vier seconden en nog eens vier seconden. 
* De standaardinstellingen voor Linux worden na vijf seconden opnieuw ingesteld. We raden u aan de specificaties voor opnieuw proberen vijf keer te wijzigen, met intervallen van één seconde.

Controleer de huidige instellingen op een Linux-VM met `cat /etc/resolv.conf` . Bekijk de *optieregel,* bijvoorbeeld:

```bash
options timeout:1 attempts:5
```

Het bestand resolv.conf wordt meestal automatisch gegenereerd en mag niet worden bewerkt. De specifieke stappen voor het toevoegen van *de optiesregel* variëren per distributie:

* **Ubuntu** (maakt gebruik van resolvconf):
  1. Voeg de *optiesregel* toe aan **/etc/resolvconf/resolv.conf.d/tail.**
  2. Voer uit `resolvconf -u` om bij te werken.
* **SUSE** (maakt gebruik van netconf):
  1. Voeg *time-out:1 attempts:5* toe aan de parameter **NETCONFIG_DNS_RESOLVER_OPTIONS=""** in **/etc/sysconfig/network/config.**
  2. Voer uit `netconfig update` om bij te werken.
* **CentOS** (maakt gebruik van NetworkManager):
  1. Voeg *echo 'options timeout:1 attempts:5'* toe aan **/etc/NetworkManager/dispatcher.d/11-dhclient.**
  2. Werk bij met `service network restart` .

## <a name="name-resolution-that-uses-your-own-dns-server"></a>Naamresolutie die gebruikmaakt van uw eigen DNS-server

In deze sectie worden VM's, rol instances en web-apps beslaat.

### <a name="vms-and-role-instances"></a>VM's en rol-exemplaren

Uw naamoplossingsbehoeften kunnen verder gaan dan de functies van Azure. U moet bijvoorbeeld Microsoft gebruiken om Windows Server Active Directory gebruiken en DNS-namen tussen virtuele netwerken om te zetten. Voor deze scenario's biedt Azure u de mogelijkheid om uw eigen DNS-servers te gebruiken.

DNS-servers in een virtueel netwerk kunnen DNS-query's doorsturen naar de recursieve resolvers in Azure. Hiermee kunt u hostnamen in dat virtuele netwerk oplossen. Een domeincontroller (DC) die wordt uitgevoerd in Azure kan bijvoorbeeld reageren op DNS-query's voor de domeinen en alle andere query's doorsturen naar Azure. Door query's door te geven kunnen VM's zowel uw on-premises resources (via de DC) als door Azure geleverde hostnamen (via de doorsturende functie) zien. Toegang tot de recursieve resolvers in Azure wordt geboden via het virtuele IP-adres 168.63.129.16.

Dns-doorsturen maakt ook DNS-resolutie mogelijk tussen virtuele netwerken en zorgt ervoor dat uw on-premises machines door Azure geleverde hostnamen kunnen oplossen. Als u de hostnaam van een VM wilt oplossen, moet de DNS-server-VM zich in hetzelfde virtuele netwerk bevinden en worden geconfigureerd voor het doorsturen van hostnaamquery's naar Azure. Omdat het DNS-achtervoegsel verschilt in elk virtueel netwerk, kunt u regels voor voorwaardelijk doorsturen gebruiken om DNS-query's te verzenden naar het juiste virtuele netwerk voor oplossing. In de volgende afbeelding ziet u twee virtuele netwerken en een on-premises netwerk dat DNS-resolutie tussen virtuele netwerken doet met behulp van deze methode. Een voorbeeld van een DNS-doorsturende server is beschikbaar in de [galerie Azure-quickstartsjablonen](https://azure.microsoft.com/documentation/templates/301-dns-forwarder/) en [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder).

> [!NOTE]
> Een rol-exemplaar kan naamom oplossing van virtuele machines binnen hetzelfde virtuele netwerk uitvoeren. Dit gebeurt met behulp van de FQDN, die bestaat uit de hostnaam van de VM en **internal.cloudapp.net** DNS-achtervoegsel. In dit geval lukt naamoplossing echter alleen als voor het rol exemplaar de VM-naam is gedefinieerd in het rolschema [(.cscfg-bestand).](/previous-versions/azure/reference/jj156212(v=azure.100))
> `<Role name="<role-name>" vmName="<vm-name>">`
>
> Rol-exemplaren die naamomoplossing moeten uitvoeren van virtuele machines in een ander virtueel netwerk (FQDN met behulp van het **achtervoegsel internal.cloudapp.net),** moeten dit doen met behulp van de methode die in deze sectie wordt beschreven (aangepaste DNS-servers die worden doorgestuurd tussen de twee virtuele netwerken).
>

![Diagram van DNS tussen virtuele netwerken](./media/virtual-networks-name-resolution-for-vms-and-role-instances/inter-vnet-dns.png)

Wanneer u door Azure geleverde naamresolutie gebruikt, biedt Azure Dynamic Host Configuration Protocol (DHCP) een intern DNS-achtervoegsel **(.internal.cloudapp.net)** voor elke VM. Dit achtervoegsel maakt hostnaamresolutie mogelijk omdat de hostnaamrecords zich in de **internal.cloudapp.net** zone. Wanneer u uw eigen oplossing voor naamomoplossing gebruikt, wordt dit achtervoegsel niet aan VM's geleverd omdat dit andere DNS-architecturen verstoort (zoals scenario's die lid zijn van een domein). In plaats daarvan biedt Azure een niet-functionerende tijdelijke aanduiding *(reddog.microsoft.com).*

Indien nodig kunt u het interne DNS-achtervoegsel bepalen met behulp van PowerShell of de API:

* Voor virtuele netwerken in Azure Resource Manager-implementatiemodellen is het achtervoegsel beschikbaar via de netwerkinterface [REST API,](/rest/api/virtualnetwork/networkinterfaces)de [PowerShell-cmdlet Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) en de Azure CLI-opdracht [az network nic show.](/cli/azure/network/nic#az_network_nic_show)
* In klassieke implementatiemodellen is het achtervoegsel beschikbaar via de aanroep [Implementatie-API](/previous-versions/azure/reference/ee460804(v=azure.100)) of de cmdlet [Get-AzureVM -Debug.](/powershell/module/servicemanagement/azure.service/get-azurevm)

Als het doorsturen van query's naar Azure niet aan uw behoeften past, moet u uw eigen DNS-oplossing bieden. Uw DNS-oplossing moet:

* Geef de juiste hostnaamresolutie op, bijvoorbeeld via [DDNS.](virtual-networks-name-resolution-ddns.md) Als u DDNS gebruikt, moet u het opruimen van DNS-records mogelijk uitschakelen. Azure DHCP-leases zijn lang en opruiming kan DNS-records voortijdig verwijderen. 
* Geef de juiste recursieve resolutie op om het oplossen van externe domeinnamen mogelijk te maken.
* Toegankelijk zijn (TCP en UDP op poort 53) van de clients die het gebruikt en toegang hebben tot internet.
* Wees beveiligd tegen toegang via internet om bedreigingen van externe agents te beperken.

> [!NOTE]
> Voor de beste prestaties moet IPv6 worden uitgeschakeld wanneer u azure-VM's als DNS-servers gebruikt.

### <a name="web-apps"></a>Web-apps
Stel dat u naamom oplossing moet uitvoeren vanuit uw web-app die is gebouwd met behulp van App Service, gekoppeld aan een virtueel netwerk, naar VM's in hetzelfde virtuele netwerk. Naast het instellen van een aangepaste DNS-server met een DNS-doorsturendeserver die query's doorsturen naar Azure (virtueel IP-adres 168.63.129.16), moet u de volgende stappen uitvoeren:
1. Schakel integratie van virtuele netwerken in voor uw web-app, als dat nog niet is gebeurd, zoals beschreven in [Uw app integreren met een virtueel netwerk.](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
2. Selecteer in Azure Portal de App Service-app die als host voor  de web-app wordt gebruikt, Netwerk synchroniseren onder **Netwerken,** **Virtual Network-integratie.**

    ![Schermopname van naamresolutie van virtueel netwerk](./media/virtual-networks-name-resolution-for-vms-and-role-instances/webapps-dns.png)

Als u naamom oplossing wilt uitvoeren vanuit uw web-app die is gebouwd met behulp van App Service, gekoppeld aan een virtueel netwerk, naar VM's in een ander virtueel netwerk, moet u aangepaste DNS-servers op beide virtuele netwerken als volgt gebruiken:

* Stel een DNS-server in het virtuele doelnetwerk in op een virtuele machine die ook query's kan doorsturen naar de recursieve resolver in Azure (virtueel IP-adres 168.63.129.16). Een voorbeeld van een DNS-doorsturende server is beschikbaar in de [galerie Azure-quickstartsjablonen](https://azure.microsoft.com/documentation/templates/301-dns-forwarder) en [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/301-dns-forwarder). 
* Stel een DNS-doorsturende machine in het virtuele bronnetwerk in op een virtuele machine. Configureer deze DNS-doorsturen voor het doorsturen van query's naar de DNS-server in uw virtuele doelnetwerk.
* Configureer de DNS-bronserver in de instellingen van het virtuele bronnetwerk.
* Schakel integratie van virtuele netwerken in voor uw web-app om een koppeling te maken met het virtuele bronnetwerk. Volg de instructies in [Uw app integreren met een virtueel netwerk.](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* Selecteer in Azure Portal de App Service-app die als host voor  de web-app wordt gebruikt, Netwerk synchroniseren onder **Netwerken,** **Virtual Network-integratie.**

## <a name="specify-dns-servers"></a>DNS-servers opgeven
Wanneer u uw eigen DNS-servers gebruikt, biedt Azure de mogelijkheid om meerdere DNS-servers per virtueel netwerk op te geven. U kunt ook meerdere DNS-servers opgeven per netwerkinterface (voor Azure Resource Manager) of per cloudservice (voor het klassieke implementatiemodel). DNS-servers die zijn opgegeven voor een netwerkinterface of cloudservice krijgen voorrang op DNS-servers die zijn opgegeven voor het virtuele netwerk.

> [!NOTE]
> Netwerkverbindingseigenschappen, zoals DNS-server-IP's, mogen niet rechtstreeks in VM's worden bewerkt. Dit komt doordat ze mogelijk worden gewist tijdens het herstellen van de service wanneer de virtuele netwerkadapter wordt vervangen. Dit geldt voor zowel Windows- als Linux-VM's.

Wanneer u het implementatiemodel Azure Resource Manager, kunt u DNS-servers opgeven voor een virtueel netwerk en een netwerkinterface. Zie Manage [a virtual network (Een virtueel netwerk beheren) en](manage-virtual-network.md) [Manage a network interface (Een netwerkinterface beheren) voor meer informatie.](virtual-network-network-interface.md)

> [!NOTE]
> Als u kiest voor een aangepaste DNS-server voor uw virtuele netwerk, moet u ten minste één IP-adres van de DNS-server opgeven; anders negeert het virtuele netwerk de configuratie en wordt in plaats daarvan door Azure geleverde DNS gebruikt.

Wanneer u het klassieke implementatiemodel gebruikt, kunt u DNS-servers voor het virtuele netwerk opgeven in het Azure Portal of het [netwerkconfiguratiebestand](/previous-versions/azure/reference/jj157100(v=azure.100)). Voor cloudservices kunt u DNS-servers opgeven via het [serviceconfiguratiebestand](/previous-versions/azure/reference/ee758710(v=azure.100)) of met behulp van PowerShell, met [New-AzureVM.](/powershell/module/servicemanagement/azure.service/new-azurevm)

> [!NOTE]
> Als u de DNS-instellingen wijzigt voor een virtueel netwerk of virtuele machine die al is geïmplementeerd, moet u een DHCP-leasevernieuwing uitvoeren op alle betrokken virtuele machines in het virtuele netwerk om de nieuwe DNS-instellingen van kracht te laten worden. Voor VM's met het Windows-besturingssysteem kunt u dit doen door rechtstreeks `ipconfig /renew` in de VM te typen. De stappen variëren afhankelijk van het besturingssysteem. Zie de relevante documentatie voor uw type besturingssysteem.

## <a name="next-steps"></a>Volgende stappen

Azure Resource Manager implementatiemodel:

* [Een virtueel netwerk beheren](manage-virtual-network.md)
* [Een netwerkinterface beheren](virtual-network-network-interface.md)

Klassiek implementatiemodel:

* [Azure-serviceconfiguratieschema](/previous-versions/azure/reference/ee758710(v=azure.100))
* [Virtual Network-configuratieschema](/previous-versions/azure/reference/jj157100(v=azure.100))
* [Een Virtual Network configureren met behulp van een netwerkconfiguratiebestand](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file)