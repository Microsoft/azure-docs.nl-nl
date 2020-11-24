---
title: Wat is Azure Private Link service?
description: Meer informatie over Azure Private Link service.
services: private-link
author: sumeetmittal
ms.service: private-link
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: sumi
ms.openlocfilehash: 27dba675f82c4d34ec793cf492c18b293a6c8c77
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95544255"
---
# <a name="what-is-azure-private-link-service"></a>Wat is Azure Private Link service?

Persoonlijke koppelings service van Azure is de verwijzing naar uw eigen service die wordt aangestuurd door een persoonlijke Azure-koppeling. De service die wordt uitgevoerd achter [Azure Standard Load Balancer](../load-balancer/load-balancer-overview.md) kan worden ingeschakeld voor toegang tot persoonlijke koppelingen, zodat gebruikers die toegang hebben tot uw service, privé kunnen zijn vanuit hun eigen VNets. Uw klanten kunnen een persoonlijk eind punt in hun VNet maken en aan deze service toewijzen. In dit artikel worden de concepten beschreven die betrekking hebben op de kant van de service provider. 

:::image type="content" source="./media/private-link-service-overview/consumer-provider-endpoint.png" alt-text="Werk stroom van persoonlijke koppelings service" border="true":::

*Afbeelding: persoonlijke Azure-koppelings service.*

## <a name="workflow"></a>Werkstroom

![Werk stroom van persoonlijke koppelings service](media/private-link-service-overview/private-link-service-workflow.png)


*Afbeelding: werk stroom van Azure Private Link service.*

### <a name="create-your-private-link-service"></a>Uw persoonlijke koppelings service maken

- Configureer uw toepassing voor uitvoering achter een standaard load balancer in uw virtuele netwerk. Als u uw toepassing al hebt geconfigureerd achter een standaard load balancer, kunt u deze stap overs Laan.   
- Maak een persoonlijke koppelings service die verwijst naar de bovenstaande load balancer. Kies in het load balancer selectie proces de frontend-IP-configuratie waar u het verkeer wilt ontvangen. Kies een subnet voor NAT IP-adressen voor de privé koppelings service. Het is raadzaam ten minste acht NAT IP-adressen beschikbaar te hebben in het subnet. Alle consumenten verkeer lijkt afkomstig te zijn van deze groep persoonlijke IP-adressen naar de service provider. Kies de juiste eigenschappen/instellingen voor de persoonlijke koppelings service.    

    > [!NOTE]
    > De service Azure private link wordt alleen ondersteund op Standard Load Balancer. 
    
### <a name="share-your-service"></a>Uw service delen

Nadat u een persoonlijke koppelings service hebt gemaakt, wordt door Azure een wereld wijd unieke naam van een alias met de naam '-' met het adres dat u voor uw service opgeeft, gegenereerd. U kunt de alias of de resource-URI van uw service met uw klanten offline delen. Consumenten kunnen een koppeling met een particuliere verbinding starten met behulp van de alias of de resource-URI.
 
### <a name="manage-your-connection-requests"></a>Uw verbindings aanvragen beheren

Nadat een gebruiker een verbinding initieert, kan de service provider de verbindings aanvraag accepteren of afwijzen. Alle verbindings aanvragen worden vermeld onder de eigenschap **privateendpointconnections** van de service private link.
 
### <a name="delete-your-service"></a>Uw service verwijderen

Als de persoonlijke koppelings service niet meer in gebruik is, kunt u deze verwijderen. Voordat u de service verwijdert, moet u er echter voor zorgen dat er geen privé-eindpunt verbindingen aan zijn gekoppeld. U kunt alle verbindingen afwijzen en de service verwijderen.

## <a name="properties"></a>Eigenschappen

Een persoonlijke koppelings service specificeert de volgende eigenschappen: 

|Eigenschap |Uitleg  |
|---------|---------|
|Inrichtings status (provisioningState)  |Een alleen-lezen eigenschap met een lijst met de huidige inrichtings status voor de persoonlijke koppelings service. Toepas bare inrichtings statussen zijn: "verwijderen; Is mislukt Is voltooid Bijwerken. Wanneer de inrichtings status geslaagd is, hebt u de service voor persoonlijke koppelingen ingericht.        |
|Alias (alias)     | Alias is een wereld wijd unieke alleen-lezen teken reeks voor uw service. Het helpt u bij het maskeren van de klant gegevens voor uw service en tegelijkertijd een gemakkelijk te delen naam voor uw service te maken. Wanneer u een persoonlijke koppelings service maakt, genereert Azure de alias voor uw service die u met uw klanten kunt delen. Uw klanten kunnen deze alias gebruiken om een verbinding met uw service aan te vragen.          |
|Zicht baarheid (zicht baarheid)     | Zicht baarheid is de eigenschap waarmee de belichtings instellingen voor uw privé koppelings service worden beheerd. Service providers kunnen ervoor kiezen om de bloot stelling aan hun service te beperken tot abonnementen met Azure RBAC-machtigingen (op rollen gebaseerd toegangs beheer), een beperkte set abonnementen of alle Azure-abonnementen.          |
|Automatische goed keuring (automatisch goed keuren)    |   Automatische goed keuring beheert de geautomatiseerde toegang tot de persoonlijke koppelings service. De abonnementen die zijn opgegeven in de lijst met automatische goed keuringen, worden automatisch goedgekeurd wanneer een verbinding wordt aangevraagd vanuit privé-eind punten in die abonnementen.          |
|Load Balancer frontend-IP-configuratie (loadBalancerFrontendIpConfigurations)    |    De service voor persoonlijke koppelingen is gekoppeld aan het frontend-IP-adres van een Standard Load Balancer. Al het verkeer dat is bestemd voor de service, zal de frontend van de SLB bereiken. U kunt SLB-regels zo configureren dat dit verkeer wordt doorgestuurd naar de juiste back-endservers waar uw toepassingen worden uitgevoerd. IP-configuraties van Load Balancer-frontend zijn anders dan NAT IP-configuraties.      |
|NAT IP-configuratie (ipConfigurations)    |    Deze eigenschap verwijst naar de NAT-IP-configuratie (Network Address Translation) voor de persoonlijke koppelings service. Het NAT IP-adres kan worden gekozen vanuit elk subnet in het virtuele netwerk van een service provider. De service voor persoonlijke koppelingen voert NAT-bestemming uit op het verkeer van de persoonlijke verbinding. Dit zorgt ervoor dat er geen IP-conflict is tussen de bron-en doel ruimte (service provider). Aan de doel zijde (service provider zijde) wordt het NAT IP-adres weer gegeven als bron-IP voor alle pakketten die door uw service worden ontvangen en de doel-IP voor alle pakketten die door uw service worden verzonden.       |
|Verbindingen met privé-eind punten (privateEndpointConnections)     |  Met deze eigenschap worden de privé-eind punten weer gegeven die verbinding maken met de service private link. Meerdere persoonlijke eind punten kunnen verbinding maken met dezelfde persoonlijke koppelings service en de service provider kan de status voor afzonderlijke privé-eind punten beheren.        |
|TCP-proxy v2 (EnableProxyProtocol)     |  Met deze eigenschap kan de service provider TCP proxy v2 gebruiken om verbindings informatie over de service gebruiker op te halen. Service provider is verantwoordelijk voor het instellen van de configuratie van de ontvanger, zodat de header Protocol versie v2 kan worden geparseerd.        |
|||


### <a name="details"></a>Details

- De service voor persoonlijke koppelingen kan worden geopend vanuit goedgekeurde persoonlijke eind punten in een open bare regio. Het persoonlijke eind punt kan worden bereikt vanuit hetzelfde virtuele netwerk, in de regio VNets, wereld wijd gekoppelde VNets en on-premises met behulp van particuliere VPN-of ExpressRoute-verbindingen. 
 
- Wanneer u een persoonlijke koppelings service maakt, wordt er een netwerk interface gemaakt voor de levens cyclus van de resource. Deze interface kan niet worden beheerd door de klant.
 
- De service private link moet worden geïmplementeerd in dezelfde regio als het virtuele netwerk en de Standard Load Balancer.  
 
- Eén persoonlijke koppelings service kan worden geopend vanuit meerdere persoonlijke eind punten die deel uitmaken van verschillende VNets, abonnementen en/of Active Directory tenants. De verbinding wordt tot stand gebracht via een werk stroom voor verbindingen. 
 
- Er kunnen meerdere services voor persoonlijke koppelingen worden gemaakt op hetzelfde Standard Load Balancer met verschillende front-end IP-configuraties. Er zijn limieten voor het aantal persoonlijke koppelings Services dat u kunt maken per Standard Load Balancer en per abonnement. Zie [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)voor meer informatie.
 
- Aan de persoonlijke koppelings service kunnen meer dan één NAT IP-configuratie zijn gekoppeld. Bij het kiezen van meer dan één NAT IP-configuratie kunnen service providers worden geschaald. Op dit moment kunnen service providers Maxi maal acht NAT IP-adressen per privé koppelings service toewijzen. Met elk IP-adres van NAT kunt u meer poorten toewijzen voor uw TCP-verbindingen en daarom uitschalen. Nadat u meerdere NAT IP-adressen aan een persoonlijke koppelings service hebt toegevoegd, kunt u de NAT IP-adressen niet verwijderen. Dit wordt gedaan om ervoor te zorgen dat actieve verbindingen niet worden beïnvloed tijdens het verwijderen van de NAT IP-adressen.


## <a name="alias"></a>Alias

**Alias** is een wereld wijd unieke naam voor uw service. Het helpt u bij het maskeren van de klant gegevens voor uw service en tegelijkertijd een gemakkelijk te delen naam voor uw service te maken. Wanneer u een persoonlijke koppelings service maakt, genereert Azure een alias voor uw service die u met uw klanten kunt delen. Uw klanten kunnen deze alias gebruiken om een verbinding met uw service aan te vragen.

De alias bestaat uit drie delen: *voor voegsel*. *GUID*. *Achtervoegsel*

- Het voor voegsel is de service naam. U kunt een eigen voor voegsel kiezen. Nadat "alias" is gemaakt, kunt u deze niet meer wijzigen, dus Selecteer uw voor voegsel op de juiste manier.  
- De GUID wordt door het platform verschaft. Zo kunt u de naam globaal uniek maken. 
- Achtervoegsel wordt toegevoegd door Azure: *Region*. Azure. privatelinkservice 

Volledige alias:  *voor voegsel*. {GUID}. *regio*. Azure. privatelinkservice  

## <a name="control-service-exposure"></a>Service blootstelling controleren

Met de service voor persoonlijke koppelingen kunt u de bloot stelling van uw service met de instelling zicht baarheid beheren. U kunt de service privé maken voor het gebruik van verschillende VNets waarvan u eigenaar bent (alleen Azure RBAC-machtigingen), de bloot stelling beperken tot een beperkt aantal abonnementen die u vertrouwt of openbaar maken zodat alle Azure-abonnementen verbindingen kunnen aanvragen op de privé koppelings service. De instellingen voor zicht baarheid bepalen of een consument verbinding kan maken met uw service of niet. 

## <a name="control-service-access"></a>Toegang tot de service beheren

Consumenten die een bloot stelling hebben (beheerd door de zichtbaarheids instelling), kunnen een persoonlijk eind punt in hun VNets maken en een verbinding met uw persoonlijke koppelings service aanvragen. De verbinding van het particuliere eind punt wordt gemaakt met de status in behandeling op het object van de persoonlijke koppelings service. De service provider is verantwoordelijk voor het optreden van de verbindings aanvraag. U kunt de verbinding goed keuren, de verbinding afwijzen of de verbinding verwijderen. Alleen verbindingen die zijn goedgekeurd, kunnen verkeer verzenden naar de persoonlijke koppelings service.

De actie voor het goed keuren van de verbindingen kan worden geautomatiseerd met behulp van de eigenschap voor automatisch goed keuren van de privé koppelings service. Automatische goed keuring is een mogelijkheid voor service providers om een set abonnementen vooraf goed te keuren voor automatische toegang tot hun service. Klanten moeten hun abonnementen offline delen voor service providers om toe te voegen aan de lijst met automatische goed keuringen. Automatische goed keuring is een subset van de zichtbaarheids matrix. Visibility bepaalt de belichtings instellingen, terwijl automatische goed keuring de goedkeurings instellingen voor uw service controleert. Als een klant een verbinding aanvraagt vanuit een abonnement in de lijst met automatische goed keuring, wordt de verbinding automatisch goedgekeurd en wordt de verbinding tot stand gebracht. Service Providers hoeven de aanvraag niet meer hand matig goed te keuren. Als een klant daarentegen een verbinding aanvraagt vanaf een abonnement in de zichtbaarheids matrix en niet in de matrix voor automatische goed keuring, wordt de service provider door de aanvraag gehaald, maar moet de service provider de verbindingen hand matig goed keuren.

## <a name="getting-connection-information-using-tcp-proxy-v2"></a>Verbindings gegevens ophalen met TCP-proxy v2

Bij het gebruik van een persoonlijke koppelings service is het IP-bron adres van de pakketten die afkomstig zijn van een particulier eind punt, Network Address Translation (NAT) op de service provider kant met behulp van de NAT-IP die is toegewezen van het virtuele netwerk van de provider. De toepassingen ontvangen daarom het toegewezen NAT IP-adres in plaats van het werkelijke IP-adres van de service-consumers. Als uw toepassing een eigen IP-adres voor de bron van de consument nodig heeft, kunt u proxy protocol inschakelen voor uw service en de informatie ophalen uit de header van het proxy protocol. Naast het bron-IP-adres bevat de header van het proxy protocol ook de LinkID van het persoonlijke eind punt. Combi natie van bron-IP-adres en LinkID kunnen service providers helpen hun consumenten op unieke wijze te identificeren. Meer informatie over proxy protocol vindt u [hier](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt). 

Deze informatie wordt als volgt gecodeerd met een aangepaste TLV-vector (type-length-waarde):

Aangepaste TLV-gegevens:

|Veld |Lengte (octetten)  |Beschrijving  |
|---------|---------|----------|
|Type  |1        |PP2_TYPE_AZURE (0xEE)|
|Lengte  |2      |Lengte van waarde|
|Waarde  |1     |PP2_SUBTYPE_AZURE_PRIVATEENDPOINT_LINKID (0x01)|
|  |4        |UINT32 (4 bytes) die de LINKID van het persoonlijke eind punt vertegenwoordigen. Gecodeerd in little endian-indeling.|

 > [!NOTE]
 > Service provider is verantwoordelijk om ervoor te zorgen dat de service achter de standaard load balancer zo is geconfigureerd dat de header van het proxy protocol wordt geparseerd volgens de [specificatie](https://www.haproxy.org/download/1.8/doc/proxy-protocol.txt) wanneer proxy protocol is ingeschakeld op de privé koppelings service. De aanvraag mislukt als het proxy protocol is ingeschakeld op de privé koppelings service, maar de service van de service provider niet is geconfigureerd voor het parseren van de header. Op dezelfde manier mislukt de aanvraag als de service provider een header van het proxy protocol verwacht, terwijl de instelling niet is ingeschakeld op de persoonlijke koppelings service. Zodra het proxy protocol is ingeschakeld, wordt de header van het proxy protocol ook opgenomen in HTTP/TCP-status tests van de host naar de virtuele machine van de back-end, zelfs als er geen client informatie in de header voor komt. 

## <a name="limitations"></a>Beperkingen

Hier volgen de bekende beperkingen bij het gebruik van de service private link:
- Alleen ondersteund op Standard Load Balancer 
- Ondersteunt alleen IPv4-verkeer
- Biedt alleen ondersteuning voor TCP-verkeer

## <a name="next-steps"></a>Volgende stappen
- [Een persoonlijke koppelings service maken met behulp van Azure PowerShell](create-private-link-service-powershell.md)
- [Een persoonlijke koppelings service maken met behulp van Azure CLI](create-private-link-service-cli.md)