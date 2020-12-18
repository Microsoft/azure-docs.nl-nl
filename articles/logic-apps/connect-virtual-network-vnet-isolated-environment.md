---
title: Verbinding maken met virtuele netwerken van Azure met een ISE
description: Maak een ISE (Integration service Environment) die toegang heeft tot Azure Virtual Networks (VNETs) vanaf Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: 3eaabc6c1e7d34bb5d9433d742581f39bdfbf98e
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/18/2020
ms.locfileid: "97669530"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Verbinding maken met virtuele Azure-netwerken van Azure Logic Apps met behulp van een ISE (Integration service Environment)

Voor scenario's waarin uw Logic apps en integratie accounts toegang nodig hebben tot een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md), maakt u een [ISE ( *Integration service Environment* )](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md). Een ISE is een particuliere en geïsoleerde omgeving die gebruikmaakt van toegewezen opslag en andere bronnen die gescheiden worden gehouden van de "wereldwijde", multi-tenant Logic Apps-service. Deze schei ding vermindert ook de invloed die andere Azure-tenants mogelijk hebben op de prestaties van uw apps. Een ISE biedt u ook uw eigen vaste IP-adressen. Deze IP-adressen zijn gescheiden van de statische IP-adressen die worden gedeeld door de Logic apps in de open bare multi tenant-service.

Wanneer u een ISE maakt, *injecteert* Azure die ISE in uw virtuele Azure-netwerk, waarna de Logic apps-service in uw virtuele netwerk wordt geïmplementeerd. Wanneer u een logische app of een integratie account maakt, selecteert u uw ISE als locatie. Uw logische app of integratie account kan vervolgens rechtstreeks toegang krijgen tot resources, zoals virtuele machines (Vm's), servers, systemen en services, in uw virtuele netwerk.

![Integratie service omgeving selecteren](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Voor logische apps en integratie accounts die in een ISE samen werken, moeten beide *dezelfde ISE* gebruiken als hun locatie.

Een ISE heeft de limieten voor de duur van de uitvoering, de opslag ruimte, de door Voer, de HTTP-aanvraag en de time-out van het antwoord, de bericht grootte en de aangepaste connector aanvragen verhoogd. Zie [Informatie over limieten en configuratie voor Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md) voor meer informatie. Zie [toegang tot Azure Virtual Network-resources vanuit Azure Logic apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)voor meer informatie over ISEs.

In dit artikel wordt beschreven hoe u deze taken kunt volt ooien met behulp van de Azure Portal:

* Toegang inschakelen voor uw ISE.
* Maak uw ISE.
* Voeg extra capaciteit toe aan uw ISE.

U kunt ook een ISE maken met behulp van de voor [beeld-Azure Resource Manager Quick](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) start-sjabloon of met behulp van de Logic apps rest API, inclusief het instellen van door de klant beheerde sleutels:

* [Een integratieserviceomgeving (ISE) maken met behulp van de Logic Apps REST API](../logic-apps/create-integration-service-environment-rest-api.md)
* [Door de klant beheerde sleutels instellen om gegevens in rust te versleutelen voor ISEs](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Logic apps, ingebouwde triggers, ingebouwde acties en connectors die worden uitgevoerd in uw ISE, gebruiken een prijs plan dat verschilt van het prijs plan op basis van verbruik. Zie het [Logic apps-prijs model](../logic-apps/logic-apps-pricing.md#fixed-pricing)voor meer informatie over de prijzen en facturerings werkzaamheden voor ISEs. Zie [Logic apps prijzen](../logic-apps/logic-apps-pricing.md)voor prijs tarieven.

* Een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md). Het virtuele netwerk moet vier *lege* subnetten hebben, die vereist zijn voor het maken en implementeren van resources in uw ISE en die worden gebruikt door deze interne en verborgen onderdelen:

  * Logic Apps compute
  * Interne App Service Environment (connectors)
  * Interne API Management (connectors)
  * Interne redis voor caching en prestaties
  
  U kunt de subnetten vooraf maken of u kunt wachten totdat u uw ISE maakt, zodat u subnetten tegelijk kunt maken. Controleer echter de vereisten voor het [subnet](#create-subnet)voordat u uw subnets maakt.

  > [!IMPORTANT]
  >
  > Gebruik niet de volgende IP-adres ruimten voor uw virtuele netwerk of subnetten, omdat deze niet kunnen worden omgezet door Azure Logic Apps:<p>
  > 
  > * 0.0.0.0/8
  > * 100.64.0.0/10
  > * 127.0.0.0/8
  > * 168.63.129.16/32
  > * 169.254.169.254/32

  * Zorg ervoor dat het virtuele netwerk [toegang biedt tot uw ISE](#enable-access) zodat uw ISE goed kan werken en toegankelijk moet blijven.

  * Als u [ExpressRoute](../expressroute/expressroute-introduction.md) gebruikt met of wilt gebruiken met [geforceerde tunneling](../firewall/forced-tunneling.md), moet u [een route tabel](../virtual-network/manage-route-table.md) met de volgende specifieke route maken en de route tabel koppelen aan elk SUBNET dat wordt gebruikt door uw ISE:

    **Naam**: <*route naam*><br>
    **Adres voorvoegsel**: 0.0.0.0/0<br>
    **Volgende hop**: Internet
    
    Deze specifieke route tabel is vereist zodat Logic Apps onderdelen kunnen communiceren met andere afhankelijke Azure-Services, zoals Azure Storage en Azure SQL DB. Voor meer informatie over deze route raadpleegt u het [adres voorvoegsel 0.0.0.0/0](../virtual-network/virtual-networks-udr-overview.md#default-route). Als u geen gebruik maakt van geforceerde tunneling met ExpressRoute, hebt u deze specifieke route tabel niet nodig.
    
    Met ExpressRoute kunt u uw on-premises netwerken uitbreiden naar micro soft Cloud en verbinding maken met micro soft-Cloud Services via een persoonlijke verbinding die wordt vereenvoudigd door de connectiviteits provider. ExpressRoute is met name een virtueel particulier netwerk dat verkeer via een particulier netwerk routeert, in plaats van via het open bare Internet. Uw Logic apps kunnen verbinding maken met on-premises resources die zich in hetzelfde virtuele netwerk bevinden wanneer ze verbinding maken via ExpressRoute of een virtueel particulier netwerk.
   
  * Als u een [virtueel netwerk apparaat (NVA)](../virtual-network/virtual-networks-udr-overview.md#user-defined)gebruikt, moet u ervoor zorgen dat u geen TLS/SSL-beëindiging inschakelt of het uitgaande TLS/SSL-verkeer wijzigt. Zorg er ook voor dat u geen inspectie inschakelt voor verkeer dat afkomstig is van het subnet van uw ISE. Zie [virtueel netwerk verkeer routeren](../virtual-network/virtual-networks-udr-overview.md)voor meer informatie.

  * Als u aangepaste DNS-servers wilt gebruiken voor uw virtuele Azure-netwerk, [stelt u die servers in door de volgende stappen uit te voeren](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) voordat u uw ISE implementeert in uw virtuele netwerk. Zie [een virtueel netwerk maken, wijzigen of verwijderen](../virtual-network/manage-virtual-network.md#change-dns-servers)voor meer informatie over het beheren van DNS-server instellingen.

    > [!NOTE]
    > Als u de instellingen van de DNS-server of DNS-server wijzigt, moet u de ISE opnieuw opstarten zodat de ISE deze wijzigingen kan ophalen. Zie [uw ISE opnieuw starten](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE)voor meer informatie.

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Toegang inschakelen voor ISE

Wanneer u een ISE gebruikt met een virtueel Azure-netwerk, heeft een veelvoorkomend installatie probleem een of meer geblokkeerde poorten. De connectors die u gebruikt voor het maken van verbindingen tussen uw ISE-en doel systemen, hebben mogelijk ook hun eigen poort vereisten. Als u bijvoorbeeld communiceert met een FTP-systeem met behulp van de FTP-connector, moet de poort die u op uw FTP-systeem gebruikt, beschikbaar zijn, bijvoorbeeld poort 21 voor het verzenden van opdrachten.

Om ervoor te zorgen dat uw ISE toegankelijk is en dat de Logic apps in die ISE kunnen communiceren via elk subnet in het virtuele netwerk, [opent u de poorten die in deze tabel worden beschreven voor elk subnet](#network-ports-for-ise). Als de vereiste poorten niet beschikbaar zijn, zal uw ISE niet goed werken.

* Als u meerdere ISE-exemplaren hebt die toegang nodig hebben tot andere eind punten met IP-beperkingen, implementeert u een [Azure firewall](../firewall/overview.md) of een [virtueel netwerk apparaat](../virtual-network/virtual-networks-overview.md#filter-network-traffic) in uw virtuele netwerk en stuurt u uitgaand verkeer via die firewall of het virtuele netwerk apparaat. U kunt vervolgens [een enkel, uitgaand, openbaar, statisch en voorspelbaar IP-adres instellen](connect-virtual-network-vnet-set-up-single-ip-address.md) dat alle ISE-instanties in uw virtuele netwerk kunnen gebruiken om te communiceren met doel systemen. Op deze manier hoeft u niet voor elke ISE een extra firewallopening in te stellen in deze doelsystemen.

   > [!NOTE]
   > U kunt deze methode gebruiken voor één ISE wanneer u voor uw scenario het aantal IP-adressen wilt beperken dat toegang nodig heeft. Bepaal of de extra kosten voor de firewall of het virtuele netwerk apparaat logisch kunnen zijn voor uw scenario. Meer informatie over [Azure firewall prijzen](https://azure.microsoft.com/pricing/details/azure-firewall/).

* Als u een nieuw virtueel Azure-netwerk en subnetten zonder beperkingen hebt gemaakt, hoeft u geen [netwerk beveiligings groepen (nsg's)](../virtual-network/network-security-groups-overview.md#network-security-groups) in te stellen in uw virtuele netwerk om verkeer tussen subnetten te beheren.

* Voor een bestaand virtueel netwerk kunt u *optioneel* [netwerk beveiligings groepen (nsg's)](../virtual-network/network-security-groups-overview.md#network-security-groups) instellen om [netwerk verkeer te filteren op subnetten](../virtual-network/tutorial-filter-network-traffic.md). Als u deze route wilt gebruiken of als u al gebruikmaakt van Nsg's, moet u ervoor zorgen dat u [de poorten opent die in deze tabel zijn beschreven](#network-ports-for-ise) voor die nsg's.

  Wanneer u NSG- [beveiligings regels](../virtual-network/network-security-groups-overview.md#security-rules)instelt, moet u *zowel* de **TCP** -als de **UDP** -protocollen gebruiken. u kunt in plaats daarvan **een wille keurige** optie selecteren, zodat u geen afzonderlijke regels voor elk protocol hoeft te maken. NSG-beveiligings regels beschrijven de poorten die u moet openen voor de IP-adressen die toegang moeten hebben tot deze poorten. Zorg ervoor dat alle firewalls, routers of andere items die tussen deze eind punten bestaan, deze poorten ook toegankelijk maken voor die IP-adressen.

* Als u geforceerde tunneling via uw firewall instelt om Internet-gebonden verkeer om te leiden, raadpleegt u de [aanvullende vereisten voor geforceerde tunneling](#forced-tunneling).

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Netwerkpoorten die worden gebruikt door uw ISE

In deze tabel worden de poorten beschreven die uw ISE nodig heeft om toegankelijk te zijn en het doel voor die poorten. De tabel gebruikt [service Tags](../virtual-network/service-tags-overview.md) die groepen met IP-adres voorvoegsels vertegenwoordigen voor een specifieke Azure-service om de complexiteit te verminderen bij het instellen van beveiligings regels. Wanneer u dit hebt opgemerkt, verwijzen *interne ISE* en *externe ISE* naar het [toegangs eindpunt dat is geselecteerd tijdens het maken van ISE](connect-virtual-network-vnet-isolated-environment.md#create-environment). Zie [endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)voor meer informatie.

> [!IMPORTANT]
> Zorg ervoor dat u voor alle regels bron poorten instelt op `*` omdat bron poorten kortstondig zijn.

#### <a name="inbound-security-rules"></a>Inkomende beveiligingsregels

| Doel | Bron servicetag of IP-adressen | Bronpoorten | Servicetag of IP-adressen van doel service | Doelpoorten | Opmerkingen |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Intersubnet-communicatie binnen het virtuele netwerk | Adres ruimte voor het virtuele netwerk met ISE-subnetten | * | Adres ruimte voor het virtuele netwerk met ISE-subnetten | * | Vereist voor verkeer voor stroom *tussen* de subnetten in het virtuele netwerk. <p><p>**Belang rijk**: Zorg ervoor dat u alle poorten in elk subnet opent voor verkeer tussen de *onderdelen* in elk subnet. |
| Beide: <p>Communicatie met uw logische app <p><p>Geschiedenis van de logische app wordt uitgevoerd| Interne ISE: <br>**VirtualNetwork** <p><p>Externe ISE: **Internet** of Zie **opmerkingen** | * | **VirtualNetwork** | 443 | In plaats van het label **Internet** te gebruiken, kunt u het IP-adres van de bron voor deze items opgeven: <p><p>-De computer of service die aanvraag triggers of webhooks aanroept in uw logische app <p>-De computer of service van waaruit u de geschiedenis van de uitvoering van de logische app wilt openen <p><p>**Belang rijk**: door deze poort te sluiten of te blok keren, voor komt u dat er Logic apps worden aangeroepen die aanvraag triggers of webhooks hebben. U kunt er ook voor zorgen dat u geen toegang hebt tot invoer en uitvoer voor elke stap in de geschiedenis van uitvoeringen. U hebt echter geen toegang tot de geschiedenis van de logische app-uitvoeringen.|
| Logic Apps Designer: dynamische eigenschappen | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | Aanvragen zijn afkomstig van de [inkomende IP-adressen](../logic-apps/logic-apps-limits-and-config.md#inbound) van het Logic apps toegangs punt voor die regio. |
| Connector implementatie | **AzureConnectors** | * | **VirtualNetwork** | 454 | Vereist voor het implementeren en bijwerken van connectors. Als u deze poort sluit of blokkeert, mislukken de ISE-implementaties en worden updates en oplossingen voor connectors voor komen. |
| Netwerk status controle | **LogicApps** | * | **VirtualNetwork** | 454 | Aanvragen zijn afkomstig van de [binnenkomende IP-adressen](../logic-apps/logic-apps-limits-and-config.md#inbound) van het Logic apps-eind punt en de [uitgaande IP-adressen](../logic-apps/logic-apps-limits-and-config.md#outbound) voor die regio. |
| Afhankelijkheid van App Service beheer | **AppServiceManagement** | * | **VirtualNetwork** | 454, 455 ||
| Communicatie van Azure Traffic Manager | **AzureTrafficManager** | * | **VirtualNetwork** | Interne ISE: 454 <p><p>Externe ISE: 443 ||
| Beide: <p>Implementatie van connector beleid <p>API Management-beheer eindpunt | **APIManagement** | * | **VirtualNetwork** | 3443 | Voor de implementatie van connector beleid is toegang tot de poort vereist voor het implementeren en bijwerken van connectors. Als u deze poort sluit of blokkeert, mislukken de ISE-implementaties en worden updates en oplossingen voor connectors voor komen. |
| Toegang tot Azure cache voor redis instanties tussen Rolinstanties | **VirtualNetwork** | * | **VirtualNetwork** | 6379-6383, plus **opmerkingen** bekijken| Om ISE te kunnen gebruiken met Azure cache voor redis, moet u deze [uitgaande en binnenkomende poorten openen die worden beschreven door de Azure-cache voor redis-Veelgestelde vragen](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
|||||||

#### <a name="outbound-security-rules"></a>Uitgaande beveiligingsregels

| Doel | Bron servicetag of IP-adressen | Bronpoorten | Servicetag of IP-adressen van doel service | Doelpoorten | Opmerkingen |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Intersubnet-communicatie binnen het virtuele netwerk | Adres ruimte voor het virtuele netwerk met ISE-subnetten | * | Adres ruimte voor het virtuele netwerk met ISE-subnetten | * | Vereist voor verkeer voor stroom *tussen* de subnetten in het virtuele netwerk. <p><p>**Belang rijk**: Zorg ervoor dat u alle poorten in elk subnet opent voor verkeer tussen de *onderdelen* in elk subnet. |
| Communicatie vanuit uw logische app | **VirtualNetwork** | * | Varieert op basis van bestemming | 80, 443 | Bestemming is afhankelijk van de eind punten voor de externe service waarmee de logische app moet communiceren. |
| Azure Active Directory | **VirtualNetwork** | * | **AzureActiveDirectory** | 80, 443 ||
| Azure Storage afhankelijkheid | **VirtualNetwork** | * | **Storage** | 80, 443, 445 ||
| Verbindings beheer | **VirtualNetwork** | * | **AppService** | 443 ||
| Diagnostische logboeken publiceren & metrische gegevens | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Azure SQL-afhankelijkheid | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure Resource Health | **VirtualNetwork** | * | **AzureMonitor** | 1886 | Vereist voor het publiceren van de status naar Resource Health. |
| Afhankelijkheid van het logboek-naar-Event hub-beleid en-bewakings agent | **VirtualNetwork** | * | **EventHub** | 5672 ||
| Toegang tot Azure cache voor redis instanties tussen Rolinstanties | **VirtualNetwork** | * | **VirtualNetwork** | 6379-6383, plus **opmerkingen** bekijken| Om ISE te kunnen gebruiken met Azure cache voor redis, moet u deze [uitgaande en binnenkomende poorten openen die worden beschreven door de Azure-cache voor redis-Veelgestelde vragen](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements). |
| DNS-naam omzetting | **VirtualNetwork** | * | IP-adressen voor aangepaste Domain Name System (DNS)-servers in uw virtuele netwerk | 53 | Alleen vereist wanneer u aangepaste DNS-servers in uw virtuele netwerk gebruikt |
|||||||

Bovendien moet u uitgaande regels toevoegen voor [app service Environment (ASE)](../app-service/environment/intro.md):

* Als u Azure Firewall gebruikt, moet u uw firewall instellen met de code App Service Environment (ASE) [Fully Qualified Domain Name (FQDN)](../firewall/fqdn-tags.md#current-fqdn-tags), die uitgaande toegang tot het verkeer van ASE-platform toestaat.

* Als u een ander firewall apparaat dan Azure Firewall gebruikt, moet u uw firewall instellen met *alle* regels die worden vermeld in de [afhankelijkheden van Firewall integratie](../app-service/environment/firewall-integration.md#dependencies) die vereist zijn voor app service environment.

<a name="forced-tunneling"></a>

#### <a name="forced-tunneling-requirements"></a>Vereisten voor geforceerde tunneling

Als u [geforceerde tunneling](../firewall/forced-tunneling.md) via uw firewall instelt of gebruikt, moet u extra externe afhankelijkheden voor uw ISE toestaan. Met geforceerde tunneling kunt u Internet-gebonden verkeer omleiden naar een aangewezen volgende hop, zoals uw virtuele particuliere netwerk (VPN) of naar een virtueel apparaat, in plaats van naar het Internet, zodat u het uitgaande netwerk verkeer kunt controleren en controleren.

Als u geen toegang voor deze afhankelijkheden toestaat, mislukt uw ISE-implementatie en werkt uw geïmplementeerde ISE niet meer.

* Door de gebruiker gedefinieerde routes

  Om asymmetrische route ring te voor komen, moet u een route definiëren voor elke en elk IP-adres dat hieronder met **Internet** wordt weer gegeven als de volgende hop.
  
  * [App Service Environment-beheer adressen](../app-service/environment/management-addresses.md)
  * [Azure IP-adressen voor connectors in de ISE-regio, beschikbaar in dit download bestand](https://www.microsoft.com/download/details.aspx?id=56519)
  * [Azure Traffic Manager-beheer adressen](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)
  * [Logic Apps inkomende en uitgaande adressen voor de regio ISE](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)
  * [Azure IP-adressen voor connectors in de ISE-regio, die in dit download bestand zijn](https://www.microsoft.com/download/details.aspx?id=56519)

* Service-eindpunten

  U moet Service-eind punten inschakelen voor Azure SQL, Storage, Service Bus en Event Hubs omdat u geen verkeer via een firewall naar deze services kunt verzenden.

*  Andere binnenkomende en uitgaande afhankelijkheden

   Uw firewall *moet* de volgende binnenkomende en uitgaande afhankelijkheden toestaan:
   
   * [Azure App Service afhankelijkheden](../app-service/environment/firewall-integration.md#deploying-your-ase-behind-a-firewall)
   * [Azure Cache Service-afhankelijkheden](../azure-cache-for-redis/cache-how-to-premium-vnet.md#what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-vnets)
   * [Azure API Management-afhankelijkheden](../api-management/api-management-using-with-vnet.md#-common-network-configuration-issues)

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Een ISE maken

1. Voer in het [Azure Portal](https://portal.azure.com)in het hoofd venster van Azure Search in `integration service environments` als uw filter en selecteer **integratie service omgevingen**.

   ![Zoek en selecteer integratie service omgevingen](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Selecteer **toevoegen** in het deel venster **integratie service omgevingen** .

   ![Selecteer toevoegen om de integratie service omgeving te maken](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Geef deze gegevens voor uw omgeving op en selecteer vervolgens **bekijken + maken**, bijvoorbeeld:

   ![Details van de omgeving opgeven](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Abonnement** | Ja | <*Azure-subscription-name*> | Het Azure-abonnement dat u wilt gebruiken voor uw omgeving |
   | **Resourcegroep** | Ja | <*Naam-van-Azure-resourcegroep*> | Een nieuwe of bestaande Azure-resource groep waar u uw omgeving wilt maken |
   | **Naam van de integratie service omgeving** | Ja | <*omgeving-naam*> | De naam van uw ISE, die alleen letters, cijfers, afbreek streepjes ( `-` ), onderstrepings tekens ( `_` ) en punten () kan bevatten `.` . |
   | **Locatie** | Ja | <*Azure-Data Center-regio*> | De Azure Data Center-regio waar u uw omgeving kunt implementeren |
   | **SKU** | Ja | **Premium** of **ontwikkelaar (geen sla)** | De ISE-SKU die u wilt maken en gebruiken. Zie [ISE sku's](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level)(Engelstalig) voor verschillen tussen deze sku's. <p><p>**Belang rijk**: deze optie is alleen beschikbaar bij het maken van ISE en kan later niet worden gewijzigd. |
   | **Extra capaciteit** | Premium: <br>Ja <p><p>Developer <br>Niet van toepassing | Premium: <br>0 tot 10 <p><p>Developer <br>Niet van toepassing | Het aantal extra verwerkings eenheden dat voor deze ISE-resource moet worden gebruikt. Zie [ISE-capaciteit toevoegen](../logic-apps/ise-manage-integration-service-environment.md#add-capacity)om capaciteit toe te voegen na het maken. |
   | **Toegangs eindpunt** | Ja | **Intern** of **extern** | Het type toegangs eindpunten dat moet worden gebruikt voor uw ISE. Deze eind punten bepalen of de aanvraag of webhook triggers op Logic apps in uw ISE kan ontvangen van buiten uw virtuele netwerk. <p><p>Als u bijvoorbeeld de volgende webhook-triggers wilt gebruiken, zorg er dan voor dat u **extern** selecteert: <p><p>-Azure DevOps <br>-Azure Event Grid <br>-Common Data Service <br>-Office 365 <br>-SAP (ISE-versie) <p><p>Uw selectie is ook van invloed op de manier waarop u invoer en uitvoer kunt weer geven en openen in de geschiedenis van de logische app-uitvoeringen. Zie [ISE endpoint Access](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)(Engelstalig) voor meer informatie. <p><p>**Belang rijk**: u kunt het toegangs eindpunt alleen selecteren tijdens het maken van de ISE en deze optie later niet wijzigen. |
   | **Virtueel netwerk** | Ja | <*Azure-virtueel-netwerk-naam*> | Het virtuele Azure-netwerk waar u uw omgeving wilt injecteren zodat logische apps in die omgeving toegang hebben tot uw virtuele netwerk. Als u nog geen netwerk hebt, [maakt u eerst een virtueel netwerk van Azure](../virtual-network/quick-create-portal.md). <p><p>**Belang rijk**: u kunt deze injectie *alleen* uitvoeren wanneer u uw ISE maakt. |
   | **Subnetten** | Ja | <*subnet-Resource-lijst*> | Een ISE vereist vier *lege* subnetten, die vereist zijn voor het maken en implementeren van resources in uw ISE en worden gebruikt door interne Logic apps onderdelen, zoals connectors en caching voor prestaties. <p>**Belang rijk**: Zorg ervoor dat u [de vereisten van het subnet controleert voordat u doorgaat met deze stappen voor het maken van uw subnetten](#create-subnet). |
   |||||

   <a name="create-subnet"></a>

   **Subnetten maken**

   Uw ISE vereist vier *lege* subnetten, die nodig zijn voor het maken en implementeren van resources in uw ISE en worden gebruikt door interne Logic apps onderdelen, zoals connectors en caching voor prestaties. U *kunt* deze subnet-adressen niet wijzigen nadat u uw omgeving hebt gemaakt. Als u uw ISE via de Azure Portal maakt en implementeert, moet u ervoor zorgen dat deze subnetten niet worden overgedragen aan Azure-Services. Als u echter uw ISE maakt en implementeert via de REST API, Azure PowerShell of een Azure Resource Manager sjabloon, moet u één leeg subnet [delegeren](../virtual-network/manage-subnet-delegation.md) `Microsoft.integrationServiceEnvironment` . Zie [een subnet delegering toevoegen](../virtual-network/manage-subnet-delegation.md)voor meer informatie.

   Elk subnet moet aan de volgende vereisten voldoen:

   * Maakt gebruik van een naam die begint met een letter of een onderstrepings teken (geen getallen). deze tekens worden niet gebruikt: `<` ,, `>` `%` , `&` , `\\` , `?` , `/` .

   * Maakt gebruik van de [CIDR-indeling (classable Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) en een klasse B-adres ruimte.
   
     > [!IMPORTANT]
     >
     > Gebruik niet de volgende IP-adres ruimten voor uw virtuele netwerk of subnetten, omdat deze niet kunnen worden omgezet door Azure Logic Apps:<p>
     > 
     > * 0.0.0.0/8
     > * 100.64.0.0/10
     > * 127.0.0.0/8
     > * 168.63.129.16/32
     > * 169.254.169.254/32

   * Maakt gebruik `/27` van een in de adres ruimte, omdat voor elk subnet 32-adressen zijn vereist. `10.0.0.0/27`Heeft bijvoorbeeld 32 adressen omdat 2<sup>(32-27)</sup> 2<sup>5</sup> of 32 is. Meer adressen bieden geen extra voor delen. Zie voor meer informatie over het berekenen van adressen [IPv4 CIDR-blokken](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks).

   * Als u [ExpressRoute](../expressroute/expressroute-introduction.md)gebruikt, moet u [een route tabel](../virtual-network/manage-route-table.md) met de volgende route maken en deze tabel koppelen aan elk subnet dat wordt gebruikt door uw ISE:

     **Naam**: <*route naam*><br>
     **Adres voorvoegsel**: 0.0.0.0/0<br>
     **Volgende hop**: Internet

   1. Selecteer in de lijst **subnets** de optie **subnet-configuratie beheren**.

      ![Subnet-configuratie beheren](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. Selecteer **subnet** in het deel venster **subnetten** .

      ![Vier lege subnetten toevoegen](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. Geef deze informatie op in het deel venster **subnet toevoegen** .

      * **Naam**: de naam voor uw subnet
      * **Adres bereik (CIDR-blok)**: het bereik van uw subnet in uw virtuele netwerk en in CIDR-indeling

      ![Details van subnet toevoegen](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Wanneer u gereed bent, selecteert u **OK**.

   1. Herhaal deze stappen voor nog drie subnetten.

      > [!NOTE]
      > Als de subnetten die u probeert te maken ongeldig zijn, wordt in de Azure Portal een bericht weer gegeven, maar wordt uw voortgang niet geblokkeerd.

   Zie [subnet van een virtueel netwerk toevoegen](../virtual-network/virtual-network-manage-subnet.md)voor meer informatie over het maken van subnetten.

1. Nadat Azure uw ISE-gegevens heeft gevalideerd, selecteert u **maken**, bijvoorbeeld:

   ![Na een geslaagde validatie selecteert u ' maken '](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure begint met het implementeren van uw omgeving, wat doorgaans binnen twee uur duurt. De implementatie kan af en toe Maxi maal vier uur duren. Als u de implementatie status wilt controleren, selecteert u in de Azure-werk balk het pictogram meldingen, waarmee het deel venster meldingen wordt geopend.

   ![Implementatie status controleren](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Als de implementatie is voltooid, wordt in azure deze melding weer gegeven:

   ![Implementatie geslaagd](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   Volg anders de Azure Portal instructies voor het oplossen van problemen met de implementatie.

   > [!NOTE]
   > Als de implementatie mislukt of als u uw ISE verwijdert, kan het tot een uur duren voordat het langer duurt voordat de subnetten worden vrijgegeven. Daarom moet u mogelijk wachten voordat u deze subnetten in een andere ISE opnieuw kunt gebruiken.
   >
   > Als u het virtuele netwerk verwijdert, duurt het over het algemeen Maxi maal twee uur voordat de subnetten worden vrijgegeven, maar deze bewerking kan langer duren. 
   > Wanneer u virtuele netwerken verwijdert, moet u ervoor zorgen dat er nog geen resources zijn verbonden. 
   > Zie [virtueel netwerk verwijderen](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

1. Als u uw omgeving wilt weer geven, selecteert u **naar resource gaan** als Azure niet automatisch naar uw omgeving gaat nadat de implementatie is voltooid.

1. Voor een ISE met *externe* eindpunt toegang, moet u een netwerk beveiligings groep maken als u er nog geen hebt en een regel voor binnenkomende beveiliging toevoegen om verkeer toe te staan van uitgaande IP-adressen van beheerde connectors. Voer de volgende stappen uit om deze regel in te stellen:

   1. Selecteer in het menu ISE onder **instellingen** de optie **Eigenschappen**.

   1. Kopieer onder **uitgaande IP-adressen van connector** de open bare IP-adresbereiken, die ook in dit artikel worden weer gegeven, [limieten en configuratie-uitgaande IP-adressen](../logic-apps/logic-apps-limits-and-config.md#outbound).

   1. Maak een netwerk beveiligings groep als u er nog geen hebt.
   
   1. Voeg aan de hand van de volgende informatie een inkomende beveiligings regel toe voor de open bare uitgaande IP-adressen die u hebt gekopieerd. Zie voor meer informatie [zelf studie: netwerk verkeer filteren met een netwerk beveiligings groep met behulp van de Azure Portal](../virtual-network/tutorial-filter-network-traffic.md#create-a-network-security-group).

      | Doel | Bron servicetag of IP-adressen | Bronpoorten | Servicetag of IP-adressen van doel service | Doelpoorten | Opmerkingen |
      |---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
      | Verkeer van uitgaande IP-adressen van connector toestaan | <*connector-openbaar/uitgaand-IP-adressen*> | * | Adres ruimte voor het virtuele netwerk met ISE-subnetten | * | |
      |||||||

1. Zie [uw integratie service omgeving beheren](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)om de netwerk status van uw ISE te controleren.

   > [!CAUTION]
   > Als het netwerk van uw ISE een slechte status krijgt, kan de interne App Service Environment (ASE) die door uw ISE wordt gebruikt, ook een slechte status krijgen. Als de ASE langer dan zeven dagen niet in orde is, wordt de ASE onderbroken. Controleer de installatie van uw virtuele netwerk om deze status op te lossen. Los eventuele problemen op en start de ISE opnieuw op. 90 anders wordt de onderbroken ASE verwijderd, waarna de ISE onbruikbaar wordt. Zorg er daarom voor dat uw ISE in orde blijft om het benodigde verkeer toe te staan.
   > 
   > Raadpleeg de volgende onderwerpen voor meer informatie:
   >
   > * [Overzicht van Azure App Service diagnostische gegevens](../app-service/overview-diagnostics.md)
   > * [Bericht logboek registratie voor Azure App Service Environment](../app-service/environment/using-an-ase.md#logging)

1. Zie [resources toevoegen aan integratie service omgevingen](../logic-apps/add-artifacts-integration-service-environment-ise.md)om te beginnen met het maken van logische apps en andere artefacten in uw ISE.

   > [!IMPORTANT]
   > Nadat u uw ISE hebt gemaakt, worden beheerde ISE-connectors beschikbaar zodat u deze kunt gebruiken, maar ze worden niet automatisch weer gegeven in de connector kiezer van de ontwerp functie voor logische apps. Voordat u deze ISE-connectors kunt gebruiken, moet u [deze connectors hand matig toevoegen en implementeren voor uw ISE](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) , zodat ze in de ontwerp functie voor logische apps worden weer gegeven.

## <a name="next-steps"></a>Volgende stappen

* [Resources toevoegen aan integratieserviceomgevingen](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Integratieserviceomgevingen beheren](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Meer informatie over [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Meer informatie over de [integratie van virtuele netwerken voor Azure-Services](../virtual-network/virtual-network-for-azure-services.md)
