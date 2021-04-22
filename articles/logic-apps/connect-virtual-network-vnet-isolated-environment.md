---
title: Verbinding maken met virtuele Azure-netwerken met een ISE
description: Maak een ISE (Integration Service Environment) die toegang heeft tot virtuele Azure-netwerken (VNET's) vanuit Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 04/21/2021
ms.openlocfilehash: bfef9f2b5420ac9377cc369d7bf9a9bdac76743b
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107874221"
---
# <a name="connect-to-azure-virtual-networks-from-azure-logic-apps-by-using-an-integration-service-environment-ise"></a>Verbinding maken met virtuele Azure-netwerken van Azure Logic Apps met behulp van een ISE (Integration service Environment)

Voor scenario's waarin uw logische apps en integratieaccounts toegang nodig hebben tot een virtueel [Azure-netwerk,](../virtual-network/virtual-networks-overview.md)maakt u een [ISE *(Integration Service Environment).*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) Een ISE is een particuliere en geïsoleerde omgeving die gebruikmaakt van toegewezen opslag en andere bronnen die gescheiden worden gehouden van de "wereldwijde", multi-tenant Logic Apps-service. Deze scheiding vermindert ook de invloed die andere Azure-tenants mogelijk hebben op de prestaties van uw apps. Een ISE biedt u ook uw eigen vaste IP-adressen. Deze IP-adressen staan los van de statische IP-adressen die worden gedeeld door de logische apps in de openbare service met meerdere tenants.

Wanneer u een ISE maakt, injecteert *Azure* die ISE in uw virtuele Azure-netwerk, waarna de Logic Apps-service in uw virtuele netwerk wordt geïmplementeerd. Wanneer u een logische app of integratieaccount maakt, selecteert u uw ISE als locatie. Uw logische app of integratieaccount heeft vervolgens rechtstreeks toegang tot resources, zoals virtuele machines (VM's), servers, systemen en services, in uw virtuele netwerk.

![Integratieserviceomgeving selecteren](./media/connect-virtual-network-vnet-isolated-environment/select-logic-app-integration-service-environment.png)

> [!IMPORTANT]
> Logische apps en integratieaccounts kunnen alleen samenwerken in een ISE als beide dezelfde *ISE* gebruiken als hun locatie.

Een ISE heeft hogere limieten voor de duur van de run, opslagretentie, doorvoer, TIME-outs voor HTTP-aanvragen en -antwoorden, berichtgrootten en aangepaste connectoraanvragen. Zie [Informatie over limieten en configuratie voor Azure Logic Apps](../logic-apps/logic-apps-limits-and-config.md) voor meer informatie. Zie Access to Azure Virtual Network resources from Azure Logic Apps (Toegang tot [Azure Virtual Network-resources vanuit Azure Logic Apps) voor meer informatie over ISE'Azure Logic Apps.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)

In dit artikel wordt beschreven hoe u deze taken kunt uitvoeren met behulp van de Azure Portal:

* Schakel toegang voor uw ISE in.
* Maak uw ISE.
* Voeg extra capaciteit toe aan uw ISE.

U kunt ook een ISE maken met behulp van de Azure Resource Manager [quickstart-sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) of met behulp van de Logic Apps REST API, waaronder het instellen van door de klant beheerde sleutels:

* [Een integratieserviceomgeving (ISE) maken met behulp van de Logic Apps REST API](../logic-apps/create-integration-service-environment-rest-api.md)
* [Door de klant beheerde sleutels instellen voor het versleutelen van data-at-rest voor ISE's](../logic-apps/customer-managed-keys-integration-service-environment.md)

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. Als u nog geen abonnement op Azure hebt, [registreer u dan nu voor een gratis Azure-account](https://azure.microsoft.com/free/).

  > [!IMPORTANT]
  > Logische apps, ingebouwde triggers, ingebouwde acties en connectors die in uw ISE worden uitgevoerd, gebruiken een ander prijsplan dan het prijsplan op basis van verbruik. Zie het Logic Apps prijsmodel voor meer informatie over [de manier waarop prijzen en facturering werken Logic Apps ISE's.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Zie prijzen voor Logic Apps [prijzen.](../logic-apps/logic-apps-pricing.md)

* Een [virtueel Azure-netwerk](../virtual-network/virtual-networks-overview.md) met vier lege *subnetten,* die vereist zijn voor het maken en implementeren van resources in uw ISE en die worden gebruikt door deze interne en verborgen onderdelen:

  * Logic Apps Compute
  * Interne App Service Environment (connectors)
  * Interne API Management (connectors)
  * Interne Redis voor caching en prestaties
  
  U kunt de subnetten van tevoren maken of wanneer u uw ISE maakt, zodat u de subnetten tegelijkertijd kunt maken. Voordat u echter uw subnetten maakt, controleert u de [subnetvereisten.](#create-subnet)

  * Zorg ervoor dat uw virtuele netwerk [toegang biedt voor uw ISE,](#enable-access) zodat uw ISE correct werkt en toegankelijk blijft.

  * Als u [ExpressRoute](../expressroute/expressroute-introduction.md) samen met geforceerd [tunneling](../firewall/forced-tunneling.md)gebruikt of wilt gebruiken, moet u een [routetabel](../virtual-network/manage-route-table.md) maken met de volgende specifieke route en de routetabel koppelen aan elk subnet dat wordt gebruikt door uw ISE:

    **Naam:**<*routenaam*><br>
    **Adres voorvoegsel:** 0.0.0.0/0<br>
    **Volgende hop:** Internet
    
    Deze specifieke routetabel is vereist zodat Logic Apps kunnen communiceren met andere afhankelijke Azure-services, zoals Azure Storage en Azure SQL DB. Zie adres voor [0.0.0.0/0](../virtual-network/virtual-networks-udr-overview.md#default-route)voor meer informatie over deze route. Als u geen gebruik maakt van geforceerd tunnelen met ExpressRoute, hebt u deze specifieke routetabel niet nodig.
    
    Met ExpressRoute kunt u uw on-premises netwerken uitbreiden naar Microsoft Cloud en verbinding maken met Microsoft-cloudservices via een privéverbinding die wordt gefaciliilieerd door de connectiviteitsprovider. ExpressRoute is met name een virtueel particulier netwerk dat verkeer routeert via een particulier netwerk, in plaats van via het openbare internet. Uw logische apps kunnen verbinding maken met on-premises resources in hetzelfde virtuele netwerk wanneer ze verbinding maken via ExpressRoute of een virtueel particulier netwerk.
   
  * Als u een virtueel netwerkapparaat [(NVA)](../virtual-network/virtual-networks-udr-overview.md#user-defined)gebruikt, moet u ervoor zorgen dat U TLS/SSL-beëindiging niet inschakelen of het uitgaande TLS/SSL-verkeer wijzigen. Zorg er ook voor dat u geen inspectie inschakelen voor verkeer dat afkomstig is van het subnet van uw ISE. Zie Routering van virtueel [netwerkverkeer voor meer informatie.](../virtual-network/virtual-networks-udr-overview.md)

  * Als u aangepaste DNS-servers wilt gebruiken voor uw virtuele Azure-netwerk, stelt u deze [servers](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) in door deze stappen te volgen voordat u uw ISE in uw virtuele netwerk implementeert. Zie Een virtueel netwerk maken, wijzigen of verwijderen voor meer informatie over het beheren [van DNS-serverinstellingen.](../virtual-network/manage-virtual-network.md#change-dns-servers)

    > [!NOTE]
    > Als u de DNS-server of DNS-serverinstellingen wijzigt, moet u de ISE opnieuw opstarten zodat de ISE deze wijzigingen kan ophalen. Zie Uw [ISE opnieuw opstarten voor meer informatie.](../logic-apps/ise-manage-integration-service-environment.md#restart-ISE)

<a name="enable-access"></a>

## <a name="enable-access-for-ise"></a>Toegang inschakelen voor ISE

Wanneer u een ISE gebruikt met een virtueel Azure-netwerk, is een veelvoorkomende configuratieprobleem een of meer geblokkeerde poorten. De connectors die u gebruikt voor het maken van verbindingen tussen uw ISE- en doelsystemen, hebben mogelijk ook hun eigen poortvereisten. Als u bijvoorbeeld met een FTP-systeem communiceert via de FTP-connector, moet de poort die u op uw FTP-systeem gebruikt beschikbaar zijn, bijvoorbeeld poort 21 voor het verzenden van opdrachten.

Open de poorten die in deze tabel worden beschreven voor elk subnet om ervoor te zorgen dat uw ISE toegankelijk is en dat de logische apps in die ISE kunnen communiceren via elk subnet in uw virtuele [netwerk.](#network-ports-for-ise) Als vereiste poorten niet beschikbaar zijn, werkt uw ISE niet goed.

* Als u meerdere ISE-exemplaren hebt die toegang nodig hebben tot andere eindpunten met [](../virtual-network/virtual-networks-overview.md#filter-network-traffic) IP-beperkingen, implementeert u een [Azure Firewall of](../firewall/overview.md) een virtueel netwerkapparaat in uw virtuele netwerk en routeert u uitgaand verkeer via die firewall of het virtuele netwerkapparaat. Vervolgens kunt u [één, uitgaand, openbaar,](connect-virtual-network-vnet-set-up-single-ip-address.md) statisch en voorspelbaar IP-adres instellen dat alle ISE-exemplaren in uw virtuele netwerk kunnen gebruiken om te communiceren met doelsystemen. Op die manier hoeft u geen extra firewall-openingen in te stellen op die doelsystemen voor elke ISE.

   > [!NOTE]
   > U kunt deze benadering gebruiken voor één ISE wanneer voor uw scenario het aantal IP-adressen moet worden beperkt dat toegang nodig heeft. Overweeg of de extra kosten voor de firewall of het virtuele netwerkapparaat zinvol zijn voor uw scenario. Meer informatie over [Azure Firewall prijzen.](https://azure.microsoft.com/pricing/details/azure-firewall/)

* Als u zonder beperkingen een nieuw virtueel Azure-netwerk en -subnetten hebt gemaakt, hoeft u geen netwerkbeveiligingsgroepen [(NSG's)](../virtual-network/network-security-groups-overview.md#network-security-groups) in uw virtuele netwerk in te stellen om verkeer tussen subnetten te controleren.

* Voor een bestaand virtueel  netwerk kunt u desgewenst netwerkbeveiligingsgroepen [(NSG's)](../virtual-network/network-security-groups-overview.md#network-security-groups) instellen om netwerkverkeer tussen [subnetten te filteren.](../virtual-network/tutorial-filter-network-traffic.md) Als u deze route wilt volgen of als u al NSG's gebruikt, moet u de poorten openen die [in](#network-ports-for-ise) deze tabel worden beschreven voor deze NSG's.

  Wanneer u [NSG-beveiligingsregels](../virtual-network/network-security-groups-overview.md#security-rules)in stelt, moet u zowel de  **TCP-**  als **UDP-protocollen** gebruiken. U kunt ook Elke selecteren, zodat u geen afzonderlijke regels hoeft te maken voor elk protocol. NSG-beveiligingsregels beschrijven de poorten die u moet openen voor de IP-adressen die toegang tot die poorten nodig hebben. Zorg ervoor dat firewalls, routers of andere items die tussen deze eindpunten bestaan, deze poorten ook toegankelijk houden voor deze IP-adressen.

* Als u geforceerd tunneling via uw firewall hebt ingesteld om internetverkeer om te leiden, controleert u de vereisten voor geforceerd [tunnelen.](#forced-tunneling)

<a name="network-ports-for-ise"></a>

### <a name="network-ports-used-by-your-ise"></a>Netwerkpoorten die worden gebruikt door uw ISE

In deze tabel worden de poorten beschreven die uw ISE nodig heeft om toegankelijk te zijn en het doel voor die poorten. Om de complexiteit bij het instellen van beveiligingsregels te verminderen, gebruikt de tabel [servicetags](../virtual-network/service-tags-overview.md) die groepen IP-adres voorvoegsels voor een specifieke Azure-service vertegenwoordigen. Waar vermeld, *verwijzen interne ISE* en *externe ISE* naar het toegangs-eindpunt dat is geselecteerd tijdens [het maken van ISE.](connect-virtual-network-vnet-isolated-environment.md#create-environment) Zie Eindpunttoegang [voor meer informatie.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access)

> [!IMPORTANT]
> Voor alle regels moet u ervoor zorgen dat u bronpoorten in stelt op `*` omdat bronpoorten kortstondig zijn.

#### <a name="inbound-security-rules"></a>Inkomende beveiligingsregels

| Doel | Bronservicetag of IP-adressen | Bronpoorten | Doelservicetag of IP-adressen | Doelpoorten | Notities |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Intersubnetcommunicatie binnen een virtueel netwerk | Adresruimte voor het virtuele netwerk met ISE-subnetten | * | Adresruimte voor het virtuele netwerk met ISE-subnetten | * | Vereist om verkeer tussen *de* subnetten in uw virtuele netwerk te laten stromen. <p><p>**Belangrijk:** om verkeer tussen de onderdelen *in* elk subnet te laten stromen, moet u ervoor zorgen dat u alle poorten in elk subnet opent. |
| Beide: <p>Communicatie met uw logische app <p><p>Geschiedenis van runs voor logische app| Interne ISE: <br>**VirtualNetwork** <p><p>Externe ISE: **internet of** zie **Notities** | * | **VirtualNetwork** | 443 | In plaats van de **internetservicetag** te gebruiken, kunt u het bron-IP-adres voor deze items opgeven: <p><p>- De computer of service die aanvraagtriggers of webhooks aanroept in uw logische app <p>- De computer of service van waar u toegang wilt krijgen tot de geschiedenis van de logische app <p><p>**Belangrijk:** het sluiten of blokkeren van deze poort voorkomt aanroepen naar logische apps met aanvraagtriggers of webhooks. U hebt ook geen toegang tot invoer en uitvoer voor elke stap in de geschiedenis van de runs. U kunt echter geen toegang krijgen tot de geschiedenis van de logic app-runs.|
| Logic Apps designer - dynamische eigenschappen | **LogicAppsManagement** | * | **VirtualNetwork** | 454 | Aanvragen zijn afkomstig van de Logic Apps van het eindpunt voor [binnenkomende IP-adressen](../logic-apps/logic-apps-limits-and-config.md#inbound) voor die regio. <p><p>**Belangrijk:** als u werkt met Azure Government cloud, werkt de **LogicAppsManagement-servicetag** niet. In plaats daarvan moet u de Logic Apps [IP-adressen voor de](../logic-apps/logic-apps-limits-and-config.md#azure-government-inbound) Azure Government. |
| Statuscontrole van netwerk | **LogicApps** | * | **VirtualNetwork** | 454 | Aanvragen zijn afkomstig van Logic Apps van het eindpunt voor binnenkomende [IP-adressen](../logic-apps/logic-apps-limits-and-config.md#inbound) en [uitgaande IP-adressen](../logic-apps/logic-apps-limits-and-config.md#outbound) voor die regio. <p><p>**Belangrijk:** als u met een Azure Government cloud werkt, werkt de **LogicApps-servicetag** niet. In plaats daarvan moet u zowel de Logic Apps [ip-adressen](../logic-apps/logic-apps-limits-and-config.md#azure-government-inbound) als de uitgaande [IP-adressen](../logic-apps/logic-apps-limits-and-config.md#azure-government-outbound) voor Azure Government. |
| Connectorimplementatie | **AzureConnectors** | * | **VirtualNetwork** | 454 | Vereist voor het implementeren en bijwerken van connectors. Als u deze poort sluit of blokkeert, mislukken ISE-implementaties en worden connectorupdates en -oplossingen voorkomen. <p><p>**Belangrijk:** als u werkt met Azure Government cloud, werkt de servicetag **AzureConnectors** niet. In plaats daarvan moet u de uitgaande IP-adressen van de [beheerde connector voor Azure Government.](../logic-apps/logic-apps-limits-and-config.md#azure-government-outbound) |
| App Service beheerafhankelijkheid | **AppServiceManagement** | * | **VirtualNetwork** | 454, 455 ||
| Communicatie vanuit Azure Traffic Manager | **AzureTrafficManager** | * | **VirtualNetwork** | Interne ISE: 454 <p><p>Externe ISE: 443 ||
| Beide: <p>Implementatie van connectorbeleid <p>API Management - beheer-eindpunt | **APIManagement** | * | **VirtualNetwork** | 3443 | Voor de implementatie van het connectorbeleid is poorttoegang vereist voor het implementeren en bijwerken van connectors. Het sluiten of blokkeren van deze poort zorgt ervoor dat ISE-implementaties mislukken en connectorupdates en -oplossingen voorkomen. |
| Toegang Azure Cache voor Redis exemplaren tussen rolin instances | **VirtualNetwork** | * | **VirtualNetwork** | 6379 - 6383, plus **opmerkingen**| Als ISE alleen met Azure Cache voor Redis werkt, moet u deze uitgaande en binnenkomende poorten openen die worden beschreven in de [veelgestelde Azure Cache voor Redis .](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements) |
|||||||

#### <a name="outbound-security-rules"></a>Uitgaande beveiligingsregels

| Doel | Bronservicetag of IP-adressen | Bronpoorten | Doelservicetag of IP-adressen | Doelpoorten | Notities |
|---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
| Intersubnetcommunicatie binnen een virtueel netwerk | Adresruimte voor het virtuele netwerk met ISE-subnetten | * | Adresruimte voor het virtuele netwerk met ISE-subnetten | * | Vereist om verkeer te laten *stromen tussen* de subnetten in uw virtuele netwerk. <p><p>**Belangrijk:** zorg ervoor dat u alle poorten *in* elk subnet opent om verkeer tussen de onderdelen in elk subnet te laten stromen. |
| Communicatie vanuit uw logische app | **VirtualNetwork** | * | Varieert op basis van doel | Varieert op basis van doel | Doelpoorten variëren op basis van de eindpunten voor de externe services waarmee uw logische app moet communiceren. <p><p>De doelpoort is bijvoorbeeld 443 voor een webservice, poort 25 voor een SMTP-service, poort 22 voor een SFTP-service, en meer. |
| Azure Active Directory | **VirtualNetwork** | * | **AzureActiveDirectory** | 80, 443 ||
| Azure Storage afhankelijkheid | **VirtualNetwork** | * | **Storage** | 80, 443, 445 ||
| Verbindingsbeheer | **VirtualNetwork** | * | **AppService** | 443 ||
| Diagnostische logboeken publiceren & metrische gegevens | **VirtualNetwork** | * | **AzureMonitor** | 443 ||
| Azure SQL afhankelijkheid | **VirtualNetwork** | * | **SQL** | 1433 ||
| Azure Resource Health | **VirtualNetwork** | * | **AzureMonitor** | 1886 | Vereist voor het publiceren van de status naar Resource Health. |
| Afhankelijkheid van logboek naar Event Hub-beleid en bewakingsagent | **VirtualNetwork** | * | **EventHub** | 5672 ||
| Toegang Azure Cache voor Redis exemplaren tussen rolin instances | **VirtualNetwork** | * | **VirtualNetwork** | 6379 - 6383, plus **opmerkingen**| Als u wilt dat ISE met Azure Cache voor Redis werkt, moet u deze uitgaande en binnenkomende poorten openen die worden beschreven in [de veelgestelde vragen Azure Cache voor Redis is beschreven.](../azure-cache-for-redis/cache-how-to-premium-vnet.md#outbound-port-requirements) |
| DNS-naamresolutie | **VirtualNetwork** | * | IP-adressen voor aangepaste DNS Domain Name System servers in uw virtuele netwerk | 53 | Alleen vereist als u aangepaste DNS-servers in uw virtuele netwerk gebruikt |
|||||||

Daarnaast moet u uitgaande regels toevoegen voor App Service Environment [(ASE)](../app-service/environment/intro.md):

* Als u Azure Firewall gebruikt, moet u uw firewall instellen met de [FQDN-tag (Fully Qualified](../firewall/fqdn-tags.md#current-fqdn-tags)Domain Name) van App Service Environment (ASE), die uitgaande toegang tot ase-platformverkeer toestaat.

* Als u een ander firewallapparaat dan Azure Firewall gebruikt, moet  u uw firewall instellen met alle regels die worden vermeld in de [firewallintegratieafhankelijkheden](../app-service/environment/firewall-integration.md#dependencies) die vereist zijn voor App Service Environment.

<a name="forced-tunneling"></a>

#### <a name="forced-tunneling-requirements"></a>Vereisten voor geforceerd tunnelen

Als u geforceerd [tunneling via](../firewall/forced-tunneling.md) uw firewall in of gebruikt, moet u extra externe afhankelijkheden voor uw ISE toestaan. Met geforceerd tunnelen kunt u internetverkeer omleiden naar een aangewezen volgende hop, zoals uw virtuele particuliere netwerk (VPN) of naar een virtueel apparaat, in plaats van naar internet, zodat u uitgaand netwerkverkeer kunt inspecteren en controleren.

Als u geen toegang voor deze afhankelijkheden toegeeft, mislukt uw ISE-implementatie en werkt uw geïmplementeerde ISE niet meer.

* Door de gebruiker gedefinieerde routes

  Als u asymmetrische routering wilt voorkomen, moet u een route definiëren voor elk IP-adres dat hieronder wordt vermeld met **Internet** als de volgende hop.
  
  * [App Service Environment-beheeradressen](../app-service/environment/management-addresses.md)
  * [Azure IP-adressen voor connectors in de ISE-regio, beschikbaar in dit downloadbestand](https://www.microsoft.com/download/details.aspx?id=56519)
  * [Azure Traffic Manager-beheeradressen](https://azuretrafficmanagerdata.blob.core.windows.net/probes/azure/probe-ip-ranges.json)
  * [Logic Apps inkomende en uitgaande adressen voor de ISE-regio](../logic-apps/logic-apps-limits-and-config.md#firewall-configuration-ip-addresses-and-service-tags)
  * [Azure IP-adressen voor connectors in de ISE-regio, die zich in dit downloadbestand](https://www.microsoft.com/download/details.aspx?id=56519)

* Service-eindpunten

  U moet service-eindpunten inschakelen voor Azure SQL, Storage, Service Bus, KeyVault en Event Hubs omdat u geen verkeer via een firewall naar deze services kunt verzenden.

*  Andere binnenkomende en uitgaande afhankelijkheden

   Uw firewall *moet* de volgende binnenkomende en uitgaande afhankelijkheden toestaan:
   
   * [Azure App Service afhankelijkheden](../app-service/environment/firewall-integration.md#deploying-your-ase-behind-a-firewall)
   * [Afhankelijkheden van Azure Cache-service](../azure-cache-for-redis/cache-how-to-premium-vnet.md#what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks)
   * [Azure API Management-afhankelijkheden](../api-management/api-management-using-with-vnet.md#-common-network-configuration-issues)

<a name="create-environment"></a>

## <a name="create-your-ise"></a>Een ISE maken

1. Voer in [Azure Portal](https://portal.azure.com)in het hoofdzoekvak van Azure in als `integration service environments` uw filter en selecteer **Integratieserviceomgevingen.**

   ![Zoek en selecteer Integratieserviceomgevingen](./media/connect-virtual-network-vnet-isolated-environment/find-integration-service-environment.png)

1. Selecteer in **het deelvenster Integratieserviceomgevingen** de optie **Toevoegen.**

   ![Selecteer Toevoegen om een integratieserviceomgeving te maken](./media/connect-virtual-network-vnet-isolated-environment/add-integration-service-environment.png)

1. Geef deze gegevens op voor uw omgeving en selecteer **beoordelen en maken,** bijvoorbeeld:

   ![Omgevingsdetails verstrekken](./media/connect-virtual-network-vnet-isolated-environment/integration-service-environment-details.png)

   | Eigenschap | Vereist | Waarde | Beschrijving |
   |----------|----------|-------|-------------|
   | **Abonnement** | Ja | <*Azure-subscription-name*> | Het Azure-abonnement dat moet worden gebruikt voor uw omgeving |
   | **Resourcegroep** | Ja | <*Naam-van-Azure-resourcegroep*> | Een nieuwe of bestaande Azure-resourcegroep waarin u uw omgeving wilt maken |
   | **Naam van integratieserviceomgeving** | Yes | <*environment-name*> | Uw ISE-naam, die alleen letters, cijfers, afbreekstreepingstekens ( `-` ), onderstrepingstekens ( `_` ) en punten ( ) mag `.` bevatten. |
   | **Locatie** | Ja | <*Azure-datacenter-regio*> | De Azure-datacenterregio waar u uw omgeving wilt implementeren |
   | **SKU** | Yes | **Premium** of **Developer (geen SLA)** | De ISE-SKU die moet worden gemaakt en gebruikt. Zie ISE-SKU's voor de verschillen tussen [deze SKU's.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#ise-level) <p><p>**Belangrijk:** deze optie is alleen beschikbaar bij het maken van ISE en kan later niet meer worden gewijzigd. |
   | **Extra capaciteit** | Premium: <br>Yes <p><p>Ontwikkelaar: <br>Niet van toepassing | Premium: <br>0 tot 10 <p><p>Ontwikkelaar: <br>Niet van toepassing | Het aantal extra verwerkingseenheden dat moet worden gebruikt voor deze ISE-resource. Zie ISE-capaciteit toevoegen om capaciteit toe [te voegen nadat deze is gemaakt.](../logic-apps/ise-manage-integration-service-environment.md#add-capacity) |
   | **Toegangs-eindpunt** | Yes | **Intern** of **extern** | Het type toegangs-eindpunten dat moet worden gebruikt voor uw ISE. Deze eindpunten bepalen of aanvraag- of webhooktriggers op logische apps in uw ISE aanroepen van buiten uw virtuele netwerk kunnen ontvangen. <p><p>Als u bijvoorbeeld de volgende triggers op basis van een webhook wilt gebruiken, moet u **Extern selecteren:** <p><p>- Azure DevOps <br>- Azure Event Grid <br>- Common Data Service <br>- Office 365 <br>- SAP (ISE-versie) <p><p>Uw selectie is ook van invloed op de manier waarop u invoer en uitvoer in de geschiedenis van de logische app-uitvoer kunt bekijken en openen. Zie [ISE-eindpunttoegang voor meer informatie.](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md#endpoint-access) <p><p>**Belangrijk:** u kunt het toegangspunt alleen selecteren tijdens het maken van de ISE en u kunt deze optie later niet meer wijzigen. |
   | **Virtueel netwerk** | Ja | <*Azure-virtual-network-name*> | Het virtuele Azure-netwerk waarin u uw omgeving wilt injecteren, zodat logische apps in die omgeving toegang hebben tot uw virtuele netwerk. Als u geen netwerk hebt, maakt u [eerst een virtueel Azure-netwerk.](../virtual-network/quick-create-portal.md) <p><p>**Belangrijk:** u kunt *deze* injectie alleen uitvoeren wanneer u uw ISE maakt. |
   | **Subnetten** | Yes | <*subnet-resource-list*> | Een ISE  vereist vier lege subnetten, die vereist zijn voor het maken en implementeren van resources in uw ISE en worden gebruikt door interne Logic Apps-onderdelen, zoals connectors en caching voor prestaties. <p>**Belangrijk:** controleer de vereisten voor het subnet voordat u doorgaat met deze [stappen om uw subnetten te maken.](#create-subnet) |
   |||||

   <a name="create-subnet"></a>

   **Subnetten maken**

   Uw ISE  vereist vier lege subnetten, die nodig zijn voor het maken en implementeren van resources in uw ISE en worden gebruikt door interne Logic Apps-onderdelen, zoals connectors en caching voor prestaties. U kunt deze subnetadressen niet wijzigen nadat u uw omgeving hebt aan het maken.  Als u uw ISE maakt en implementeert via de Azure Portal, moet u ervoor zorgen dat u deze subnetten niet delegeert naar Azure-services. Als u echter uw ISE maakt en implementeert via de REST API, Azure PowerShell of een [](../virtual-network/manage-subnet-delegation.md) Azure Resource Manager-sjabloon, moet u één leeg subnet delegeren naar `Microsoft.integrationServiceEnvironment` . Zie Een [subnetdelegering toevoegen voor meer informatie.](../virtual-network/manage-subnet-delegation.md)

   Elk subnet moet aan deze vereisten voldoen:

   * Hiermee wordt een naam gebruikt die begint met een alfabetisch teken of een onderstrepingsteken (geen cijfers) en die geen gebruikmaakt van de volgende tekens: `<` , , , , , , , `>` `%` `&` `\\` `?` `/` .

   * Maakt gebruik [van de CIDRInter-Domain indeling (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)
   
     > [!IMPORTANT]
     >
     > Gebruik niet de volgende IP-adresruimten voor uw virtuele netwerk of subnetten omdat deze niet kunnen worden opgelost door Azure Logic Apps:<p>
     > 
     > * 0.0.0.0/8
     > * 100.64.0.0/10
     > * 127.0.0.0/8
     > * 168.63.129.16/32
     > * 169.254.169.254/32

   * Maakt gebruik `/27` van een in de adresruimte omdat elk subnet 32 adressen vereist. Heeft bijvoorbeeld `10.0.0.0/27` 32 adressen omdat 2<sup>(32-27)</sup> 2<sup>5</sup> of 32 is. Meer adressen bieden geen extra voordelen. Zie [IPv4 CIDR-blokken](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing#IPv4_CIDR_blocks)voor meer informatie over het berekenen van adressen.

   * Als u [ExpressRoute gebruikt,](../expressroute/expressroute-introduction.md)moet u een [routetabel](../virtual-network/manage-route-table.md) maken met de volgende route en die tabel koppelen aan elk subnet dat door uw ISE wordt gebruikt:

     **Naam:**<*routenaam*><br>
     **Adres voorvoegsel:** 0.0.0.0/0<br>
     **Volgende hop:** Internet

   1. Selecteer in **de lijst Subnetten** de optie **Subnetconfiguratie beheren.**

      ![Subnetconfiguratie beheren](./media/connect-virtual-network-vnet-isolated-environment/manage-subnet-configuration.png)

   1. Selecteer in **het deelvenster Subnetten** de optie **Subnet**.

      ![Vier lege subnetten toevoegen](./media/connect-virtual-network-vnet-isolated-environment/add-empty-subnets.png)

   1. Geef in **het deelvenster Subnet** toevoegen deze informatie op.

      * **Naam:** de naam voor uw subnet
      * **Adresbereik (CIDR-blok)**: Het bereik van uw subnet in uw virtuele netwerk en in CIDR-indeling

      ![Subnetdetails toevoegen](./media/connect-virtual-network-vnet-isolated-environment/provide-subnet-details.png)

   1. Wanneer u gereed bent, selecteert u **OK**.

   1. Herhaal deze stappen voor nog drie subnetten.

      > [!NOTE]
      > Als de subnetten die u probeert te maken niet geldig zijn, wordt Azure Portal een bericht weergegeven, maar wordt de voortgang niet geblokkeerd.

   Zie Een subnet voor een virtueel netwerk toevoegen voor meer informatie over het maken van [subnetten.](../virtual-network/virtual-network-manage-subnet.md)

1. Nadat Azure uw ISE-gegevens heeft gevalideerd, selecteert **u Maken,** bijvoorbeeld:

   ![Nadat de validatie is geslaagd, selecteert u Maken](./media/connect-virtual-network-vnet-isolated-environment/ise-validation-success.png)

   Azure begint met het implementeren van uw omgeving, wat meestal binnen twee uur duurt. Soms kan de implementatie maximaal vier uur duren. Als u de implementatiestatus wilt controleren, selecteert u op de Azure-werkbalk het meldingenpictogram. Hiermee opent u het deelvenster Meldingen.

   ![Implementatiestatus controleren](./media/connect-virtual-network-vnet-isolated-environment/environment-deployment-status.png)

   Als de implementatie is voltooid, wordt in Azure deze melding weer:

   ![Implementatie geslaagd](./media/connect-virtual-network-vnet-isolated-environment/deployment-success-message.png)

   Volg anders de instructies Azure Portal voor het oplossen van problemen met de implementatie.

   > [!NOTE]
   > Als de implementatie mislukt of u uw ISE verwijdert, kan het een uur of in zeldzame gevallen langer duren voordat Azure uw subnetten vrijgeeft. Mogelijk moet u dus wachten voordat u deze subnetten opnieuw kunt gebruiken in een andere ISE.
   >
   > Als u uw virtuele netwerk verwijdert, duurt het in het algemeen maximaal twee uur voordat Azure uw subnetten vrij geeft, maar deze bewerking kan langer duren. 
   > Zorg er bij het verwijderen van virtuele netwerken voor dat er geen resources meer zijn verbonden. 
   > Zie [Virtueel netwerk verwijderen.](../virtual-network/manage-virtual-network.md#delete-a-virtual-network)

1. Als u uw omgeving wilt weergeven, **selecteert** u Ga naar resource als Azure niet automatisch naar uw omgeving gaat nadat de implementatie is uitgevoerd.

1. Voor een ISE  die toegang heeft tot externe eindpunten, moet u een netwerkbeveiligingsgroep maken als u er nog geen hebt en een inkomende beveiligingsregel toevoegen om verkeer van uitgaande IP-adressen van beheerde connector toe te staan. Volg deze stappen om deze regel in te stellen:

   1. Selecteer in uw ISE-menu onder **Instellingen** de optie **Eigenschappen.**

   1. Kopieer **onder Uitgaande IP-adressen** van connector de openbare IP-adresbereiken, die ook worden weergegeven in dit artikel Limieten en configuratie - Uitgaande [IP-adressen.](../logic-apps/logic-apps-limits-and-config.md#outbound)

   1. Maak een netwerkbeveiligingsgroep als u er nog geen hebt.
   
   1. Voeg op basis van de volgende informatie een beveiligingsregel voor binnenkomende gegevens toe voor de openbare uitgaande IP-adressen die u hebt gekopieerd. Zie [Zelfstudie: Netwerkverkeer filteren](../virtual-network/tutorial-filter-network-traffic.md#create-a-network-security-group)met een netwerkbeveiligingsgroep met behulp van de Azure Portal.

      | Doel | Bronservicetag of IP-adressen | Bronpoorten | Doelservicetag of IP-adressen | Doelpoorten | Notities |
      |---------|------------------------------------|--------------|-----------------------------------------|-------------------|-------|
      | Verkeer van uitgaande IP-adressen van connector toestaan | <*connector-public-outbound-IP-addresses*> | * | Adresruimte voor het virtuele netwerk met ISE-subnetten | * | |
      |||||||

1. Zie Manage your integration service environment (Uw integratieserviceomgeving beheren) om de netwerktoestand voor uw ISE [te controleren.](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

   > [!CAUTION]
   > Als het netwerk van uw ISE niet meer in orde is, kan de interne App Service Environment (ASE) die door uw ISE wordt gebruikt, ook een slechte status krijgen. Als de ASE langer dan zeven dagen niet in orde is, wordt de ASE tijdelijk opgeschort. Als u deze status wilt oplossen, controleert u de installatie van uw virtuele netwerk. Los eventuele problemen op die u vindt en start uw ISE opnieuw op. Anders wordt na 90 dagen de opgeschorte ASE verwijderd en wordt uw ISE onbruikbaar. Zorg er dus voor dat uw ISE in orde blijft om het benodigde verkeer toe te staan.
   > 
   > Raadpleeg de volgende onderwerpen voor meer informatie:
   >
   > * [Azure App Service diagnostische gegevens](../app-service/overview-diagnostics.md)
   > * [Logboekregistratie van berichten voor Azure App Service Environment](../app-service/environment/using-an-ase.md#logging)

1. Zie Resources toevoegen aan integratieserviceomgevingen om te beginnen met het maken van logische apps en andere [artefacten](../logic-apps/add-artifacts-integration-service-environment-ise.md)in uw ISE.

   > [!IMPORTANT]
   > Nadat u uw ISE hebt gemaakt, zijn beheerde ISE-connectors beschikbaar die u kunt gebruiken, maar ze worden niet automatisch weergegeven in de connector- picker in logic app designer. Voordat u deze ISE-connectors kunt gebruiken, moet u deze connectors handmatig toevoegen aan en implementeren in [uw ISE,](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment) zodat ze worden weergegeven in de ontwerpfunctie voor logische apps.

## <a name="next-steps"></a>Volgende stappen

* [Resources toevoegen aan integratieserviceomgevingen](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Integratieserviceomgevingen beheren](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)
* Meer informatie over [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* Meer informatie over [integratie van virtuele netwerken voor Azure-services](../virtual-network/virtual-network-for-azure-services.md)
