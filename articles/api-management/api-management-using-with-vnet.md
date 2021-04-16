---
title: Azure API Management gebruiken met virtuele netwerken
description: Meer informatie over het instellen van een verbinding met een virtueel netwerk in Azure API Management toegang tot webservices via dit netwerk.
services: api-management
author: vladvino
ms.service: api-management
ms.topic: how-to
ms.date: 04/12/2021
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: 5612da51c1896aaa40ff2a0fb90e3343f676de43
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531616"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Azure API Management gebruiken met virtuele netwerken
Met Azure Virtual Networks (VNETs) kunt u uw Azure-resources in een routeerbaar netwerk (buiten internet) plaatsen waarvan u de toegang beheert. Deze netwerken kunnen vervolgens worden verbonden met uw on-premises netwerken met behulp van verschillende VPN-technologieën. Voor meer informatie over Azure Virtual Networks begint u hier met de informatie: [Azure Virtual Network Overview](../virtual-network/virtual-networks-overview.md).

Azure API Management kunnen worden geïmplementeerd in het virtuele netwerk (VNET), zodat het toegang heeft tot back-endservices binnen het netwerk. De ontwikkelaarsportal en API-gateway kunnen zodanig worden geconfigureerd dat ze toegankelijk zijn via internet of alleen binnen het virtuele netwerk.

> [!NOTE]
> De URL van het API-importdocument moet worden gehost op een openbaar toegankelijk internetadres.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Vereisten

Als u de stappen wilt uitvoeren die in dit artikel worden beschreven, hebt u het volgende nodig:

+ **Een actief Azure-abonnement.**

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ **Een API Management-exemplaar.** Zie Een Azure API Management [maken voor meer informatie.](get-started-create-service-instance.md)

[!INCLUDE [api-management-public-ip-for-vnet](../../includes/api-management-public-ip-for-vnet.md)]

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>VNET-verbinding inschakelen

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>VNET-connectiviteit inschakelen met behulp van de Azure Portal

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw API Management-exemplaar te vinden. Zoek en selecteer **API Management services.**

1. Kies uw API Management-exemplaar.

1. Selecteer **Virtueel netwerk.**
1. Configureer API Management-exemplaar dat moet worden geïmplementeerd in een virtueel netwerk.

    :::image type="content" source="media/api-management-using-with-vnet/api-management-menu-vnet.png" alt-text="Selecteer virtueel netwerk in Azure Portal.":::
    
1. Selecteer het gewenste toegangstype:

    * **Uit:** dit is de standaardinstelling. API Management wordt niet geïmplementeerd in een virtueel netwerk.

    * **Extern:** de API Management-gateway en de ontwikkelaarsportal zijn toegankelijk vanaf het openbare internet via een externe load balancer. De gateway heeft toegang tot resources in het virtuele netwerk.

        ![Openbare peering][api-management-vnet-public]

    * **Intern:** de API Management-gateway en de ontwikkelaarsportal zijn alleen toegankelijk vanuit het virtuele netwerk via een interne load balancer. De gateway heeft toegang tot resources in het virtuele netwerk.

        ![Persoonlijke peering][api-management-vnet-private]

1. Als u **Extern** of Intern **hebt** geselecteerd, ziet u een lijst met alle locaties (regio's) waar uw API Management is ingericht. Kies een **Locatie** en kies vervolgens het virtuele **netwerk,** **het subnet** en het **IP-adres.** De lijst met virtuele netwerken wordt gevuld Resource Manager virtuele netwerken die beschikbaar zijn in uw Azure-abonnementen die zijn ingesteld in de regio die u configureert.


    :::image type="content" source="media/api-management-using-with-vnet/api-management-using-vnet-select.png" alt-text="Instellingen voor virtuele netwerken in de portal.":::

    > [!IMPORTANT]
    > * Wanneer uw client **API-versie 2020-12-01** of eerder gebruikt om een Azure API Management-exemplaar te implementeren in een Resource Manager-VNET, moet de service zich in een toegewezen subnet met geen resources, behalve Azure API Management-exemplaren. Als er wordt geprobeerd een Azure API Management-exemplaar te implementeren in een Resource Manager VNET-subnet dat andere resources bevat, mislukt de implementatie.
    > * Wanneer uw client **API-versie 2021-01-01-preview** of hoger gebruikt om een Azure API Management-exemplaar in een virtueel netwerk te implementeren, wordt alleen een virtueel Resource Manager-netwerk ondersteund. Daarnaast kan het gebruikte subnet andere resources bevatten. U hoeft geen subnet te gebruiken dat is toegewezen aan API Management exemplaren. 

1. Selecteer **Toepassen**. De **pagina Virtueel netwerk** van uw API Management-exemplaar wordt bijgewerkt met uw nieuwe virtuele netwerk en subnetopties.

1. Ga door met het configureren van instellingen voor het virtuele netwerk voor de resterende locaties van API Management exemplaar.

7. Selecteer opslaan in de bovenste navigatiebalk en selecteer vervolgens **Netwerkconfiguratie toepassen.**

    Het kan 15 tot 45 minuten duren om de API Management bij te werken.

> [!NOTE]
> Met clients die gebruikmaken van API-versie 2020-12-01 en eerder, verandert het VIP-adres van het API Management-exemplaar telkens wanneer het VNET wordt ingeschakeld of uitgeschakeld. Het VIP-adres wordt ook gewijzigd wanneer API Management wordt verplaatst **van** Extern **naar** intern virtueel netwerk, of vice versa.

> [!IMPORTANT]
> Als u API Management uit een VNET verwijdert of de VNET wijzigt waarin het is geïmplementeerd, kan het eerder gebruikte VNET maximaal zes uur vergrendeld blijven. Gedurende deze periode is het niet mogelijk om het VNET te verwijderen of er een nieuwe resource in te implementeren. Dit gedrag geldt voor clients met API-versie 2018-01-01 en eerder. Clients die gebruikmaken van API-versie 2019-01-01 en hoger, wordt het VNET vrij gemaakt zodra de gekoppelde API Management-service wordt verwijderd.

### <a name="deploy-api-management-into-external-vnet"></a><a name="deploy-apim-external-vnet"> </a>Een API Management implementeren in extern VNET

U kunt ook virtuele netwerkconnectiviteit inschakelen met behulp van de volgende methoden.

### <a name="api-version-2021-01-01-preview"></a>API-versie 2021-01-01-preview

* Azure Resource Manager [sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-api-management-create-with-external-vnet-publicip)

     [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-external-vnet-publicip%2Fazuredeploy.json)

### <a name="api-version-2020-12-01"></a>API-versie 2020-12-01

* Azure Resource Manager [sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-api-management-create-with-external-vnet)
    
     [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-external-vnet%2Fazuredeploy.json)

* Azure PowerShell- maken of bijwerken [van](/powershell/module/az.apimanagement/new-azapimanagement) een API Management-exemplaar in een virtueel netwerk [](/powershell/module/az.apimanagement/update-azapimanagementregion)

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>Verbinding maken met een webservice die wordt gehost in een virtueel netwerk
Nadat uw API Management-service is verbonden met het VNET, is het openen van back-endservices in het VNET niet anders dan toegang tot openbare services. Typ het lokale IP-adres of de hostnaam (als er een DNS-server is geconfigureerd voor het VNET) van uw webservice in het veld **Webservice-URL** wanneer u een nieuwe API maakt of een bestaande bewerkt.

![API toevoegen vanuit VPN][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>Veelvoorkomende problemen met netwerkconfiguratie
Hieronder volgt een lijst met veelvoorkomende configuratieproblemen die zich kunnen voordoen tijdens het implementeren API Management service in een Virtual Network.

* **Aangepaste DNS-serverinstallatie:** de API Management-service is afhankelijk van verschillende Azure-services. Wanneer API Management wordt gehost in een VNET met een aangepaste DNS-server, moet deze de hostnamen van deze Azure-services oplossen. Volg deze [richtlijnen](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) voor aangepaste DNS-instellingen. Zie de onderstaande poortentabel en andere netwerkvereisten ter referentie.

> [!IMPORTANT]
> Als u van plan bent om een aangepaste DNS-server(s)  voor het VNET te gebruiken, moet u deze instellen voordat u er een API Management service in implementeert. Anders moet u de service API Management bijwerken telkens wanneer u de DNS-server(s) wijzigt door de bewerking [Netwerkconfiguratie toepassen uit te voeren](/rest/api/apimanagement/2019-12-01/apimanagementservice/applynetworkconfigurationupdates)

* **Poorten die vereist zijn API Management:** uitgaand en uitgaand verkeer naar het subnet waarin API Management is geïmplementeerd, kunnen worden beheerd met behulp van [netwerkbeveiligingsgroep][Network Security Group]. Als een van deze poorten niet beschikbaar is, API Management mogelijk niet goed werken en mogelijk niet toegankelijk. Een of meer van deze poorten zijn geblokkeerd, is een ander veelvoorkomende fout bij het gebruik van API Management met een VNET.

<a name="required-ports"></a> Wanneer een API Management-service-exemplaar wordt gehost in een VNET, worden de poorten in de volgende tabel gebruikt.

| Bron-/doelpoort(en) | Richting          | Transportprotocol |   [Servicetags](../virtual-network/network-security-groups-overview.md#service-tags) <br> Bron/doel   | Doel ( \* )                                                 | Virtual Network type |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| * / [80], 443                  | Inkomend            | TCP                | INTERNET/VIRTUAL_NETWORK            | Clientcommunicatie met API Management                      | Extern             |
| * / 3443                     | Inkomend            | TCP                | ApiManagement/VIRTUAL_NETWORK       | Beheer-eindpunt voor Azure Portal en PowerShell         | Externe & Intern  |
| * / 443                  | Uitgaand           | TCP                | VIRTUAL_NETWORK /Storage             | **Afhankelijkheid van Azure Storage**                             | Externe & Intern  |
| * / 443                  | Uitgaand           | TCP                | VIRTUAL_NETWORK / AzureActiveDirectory | [Azure Active Directory](api-management-howto-aad.md) en Azure KeyVault-afhankelijkheid                  | Externe & Intern  |
| * / 1433                     | Uitgaand           | TCP                | VIRTUAL_NETWORK/ SQL                 | **Toegang tot Azure SQL-eindpunten**                           | Externe & Intern  |
| * / 443                     | Uitgaand           | TCP                | VIRTUAL_NETWORK / AzureKeyVault                 | **Toegang tot Azure KeyVault**                           | Externe & Intern  |
| * / 5671, 5672, 443          | Uitgaand           | TCP                | VIRTUAL_NETWORK/ EventHub            | Afhankelijkheid voor [logboeken bij Event Hub-beleid](api-management-howto-log-event-hubs.md) en bewakingsagent | Externe & Intern  |
| * / 445                      | Uitgaand           | TCP                | VIRTUAL_NETWORK /Storage             | Afhankelijkheid van Azure-bestands share voor [GIT](api-management-configuration-repository-git.md)                      | Externe & Intern  |
| * / 443, 12000                     | Uitgaand           | TCP                | VIRTUAL_NETWORK / AzureCloud            | Status- en bewakingsextensie         | Externe & Intern  |
| * / 1886, 443                     | Uitgaand           | TCP                | VIRTUAL_NETWORK / AzureMonitor         | Diagnostische [logboeken en metrische gegevens](api-management-howto-use-azure-monitor.md) [publiceren, Resource Health](../service-health/resource-health-overview.md) en [Application Insights](api-management-howto-app-insights.md)                   | Externe & Intern  |
| * / 25, 587, 25028                       | Uitgaand           | TCP                | VIRTUAL_NETWORK/INTERNET            | Verbinding maken met SMTP Relay voor het verzenden van e-mailberichten                    | Externe & Intern  |
| * / 6381 - 6383              | Binnenkomende & uitgaand | TCP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | Toegang tot Redis Service voor [cachebeleid](api-management-caching-policies.md) tussen computers         | Externe & Intern  |
| * / 4290              | Binnenkomende & uitgaand | UDP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | Synchronisatiemeters voor [beleidsregels voor snelheidslimieten](api-management-access-restriction-policies.md#LimitCallRateByKey) tussen computers         | Externe & Intern  |
| * / *                        | Inkomend            | TCP                | AZURE_LOAD_BALANCER/VIRTUAL_NETWORK | Azure Infrastructure Load Balancer                          | Externe & Intern  |

>[!IMPORTANT]
> De poorten waarvoor het *doel* **vetgedrukt** is, zijn vereist om API Management-service te kunnen worden geïmplementeerd. Het blokkeren van de andere poorten leidt echter **tot een verslechtering** van de mogelijkheid om de service die wordt uitgevoerd te gebruiken en te bewaken en de **vastgelegde SLA op te geven.**

+ **TLS-functionaliteit:** als u het bouwen en valideren van TLS/SSL-certificaatketens wilt inschakelen, moet de API Management-service uitgaande netwerkconnectiviteit hebben met ocsp.msocsp.com, mscrl.microsoft.com en crl.microsoft.com. Deze afhankelijkheid is niet vereist als een certificaat dat u uploadt naar API Management de volledige keten naar de CA-hoofdmap bevat.

+ **DNS-toegang:** uitgaande toegang op poort 53 is vereist voor communicatie met DNS-servers. Als er een aangepaste DNS-server aan het andere uiteinde van een VPN-gateway bestaat, moet de DNS-server bereikbaar zijn vanaf het subnet dat als host voor API Management.

+ **Metrische gegevens en statuscontrole:** uitgaande netwerkconnectiviteit naar Azure Monitoring-eindpunten, die worden opgelost onder de volgende domeinen. Zoals weergegeven in de tabel, worden deze URL's weergegeven onder de AzureMonitor-servicetag voor gebruik met netwerkbeveiligingsgroepen.

    | Azure-omgeving | Eindpunten                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Openbare Azure-peering      | <ul><li>gcs.prod.monitoring.core.windows.net(**nieuw**)</li><li>global.prod.microsoftmetrics.com(**nieuw**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**nieuw**)</li><li>prod3-black.prod.microsoftmetrics.com(**nieuw**)</li><li>prod3-red.prod.microsoftmetrics.com(**nieuw**)</li><li>gcs.prod.warm.ingestion.monitoring.azure.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>global.prod.microsoftmetrics.com(**nieuw**)</li><li>shoebox2.prod.microsoftmetrics.com(**nieuw**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**nieuw**)</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.us</li></ul>                                                                                                                                                                                                                                                |
    | Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>global.prod.microsoftmetrics.com(**nieuw**)</li><li>shoebox2.prod.microsoftmetrics.com(**nieuw**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com(**nieuw**)</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.cn</li></ul>                                                                                                                                                                                                                                                |

  >[!IMPORTANT]
  > De wijziging van clusters hierboven met DNS-zone **.nsatc.net** **in .microsoftmetrics.com** is voornamelijk een DNS-wijziging. HET IP-adres van het cluster wordt niet gewijzigd.

+ **Regionale** servicetags: NSG-regels die uitgaande connectiviteit met Storage,SQL en Event Hubs-servicetags toestaan, kunnen gebruikmaken van de regionale versies van die tags die overeenkomen met de regio met het API Management-exemplaar (bijvoorbeeld Storage.WestUS voor een API Management-exemplaar in de regio VS - west). In implementaties voor meerdere regio's moet de NSG in elke regio verkeer naar de servicetags voor die regio en de primaire regio toestaan.

    > [!IMPORTANT]
    > Als u het publiceren [van](api-management-howto-developer-portal.md) de ontwikkelaarsportal voor een API Management-exemplaar in een virtueel netwerk wilt inschakelen, moet u ook uitgaande connectiviteit met blobopslag in de regio VS - west toestaan. Gebruik bijvoorbeeld de servicetag **Storage.WestUS** in een NSG-regel. Connectiviteit met blobopslag in de regio VS - west is momenteel vereist voor het publiceren van de ontwikkelaarsportal voor API Management exemplaar.

+ **SMTP Relay:** uitgaande netwerkconnectiviteit voor de SMTP-relay, die wordt opgelost onder de host `smtpi-co1.msn.com` , , `smtpi-ch1.msn.com` `smtpi-db3.msn.com` `smtpi-sin.msn.com` en `ies.global.microsoft.com`

+ **Developer Portal CAPTCHA:** uitgaande netwerkconnectiviteit voor de CAPTCHA van de ontwikkelaarsportal, die wordt opgelost onder de hosts `client.hip.live.com` en `partner.hip.live.com` .

+ **Azure Portal Diagnostics:** als u de stroom diagnostische logboeken van Azure Portal wilt inschakelen wanneer u de API Management-extensie gebruikt vanuit een Virtual Network, is uitgaande toegang tot op `dc.services.visualstudio.com` poort 443 vereist. Dit helpt bij het oplossen van problemen waarmee u mogelijk te maken kunt krijgen bij het gebruik van de extensie.

+ **Azure Load Balancer:** Het toestaan van binnenkomende aanvragen van servicetag is geen vereiste voor de SKU, omdat er slechts één rekeneenheid `AZURE_LOAD_BALANCER` `Developer` achter wordt geïmplementeerd. Maar het inkomende verkeer van [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) wordt essentieel bij het schalen naar een hogere SKU, zoals , omdat een implementatie mislukt als de statustest `Premium` van Load Balancer mislukt.

+ **Application Insights:** als [Azure-toepassing Insights-bewaking](api-management-howto-app-insights.md) is ingeschakeld op API Management, moeten we uitgaande connectiviteit met het [telemetrie-eindpunt](../azure-monitor/app/ip-addresses.md#outgoing-ports) van de Virtual Network. 

+ Geforceerde tunneling van verkeer naar **on-premises firewall** met expressroute of virtueel netwerkapparaat: een veelgebruikte klantconfiguratie is het definiëren van hun eigen standaardroute (0.0.0.0/0) die al het verkeer van het API Management gedelegeerde subnet dwingt om door een on-premises firewall of een virtueel netwerkapparaat te stromen. Deze verkeersstroom verbreekt altijd de connectiviteit met Azure API Management omdat het uitgaande verkeer on-premises wordt geblokkeerd of NAT naar een onherkenbare set adressen die niet meer met verschillende Azure-eindpunten werkt. Voor de oplossing moet u een aantal dingen doen:

  * Schakel service-eindpunten in op het subnet waarin API Management service is geïmplementeerd. [Service-eindpunten][ServiceEndpoints] moeten zijn ingeschakeld voor Azure SQL, Azure Storage, Azure EventHub en Azure ServiceBus. Door eindpunten rechtstreeks vanuit API Management gedelegeerd subnet naar deze services in te stellen, kunnen ze het Microsoft Azure backbone-netwerk gebruiken voor optimale routering voor serviceverkeer. Als u service-eindpunten gebruikt met API Management met geforceerd tunnelen, wordt het bovenstaande Verkeer van Azure-services niet geforceerd getunneld. Het andere API Management serviceafhankelijkheidsverkeer wordt geforceerd getunneld en kan niet verloren gaan of de API Management-service werkt niet goed.
    
  * Al het verkeer van het besturingsvlak van internet naar het beheer-eindpunt van uw API Management-service wordt gerouteerd via een specifieke set binnenkomende IP's die worden gehost door API Management. Wanneer het verkeer geforceerd wordt getunneld, worden de antwoorden niet symmetrisch teruggemapt naar deze binnenkomende bron-IP's. Om deze beperking te omzeilen, moeten we de volgende door de gebruiker gedefinieerde routes[(UDR's)][UDRs]toevoegen om verkeer terug te sturen naar Azure door de bestemming van deze hostroutes in te stellen op 'Internet'. De set binnenkomende IP-adressen voor besturingsvlakverkeer is gedocumenteerd [IP-adressen van besturingsvlak](#control-plane-ips)

  * Voor andere API Management serviceafhankelijkheden die geforceerd zijn getunneld, moet er een manier zijn om de hostnaam op te lossen en het eindpunt te bereiken. Dit zijn onder andere
      - Metrische gegevens en statuscontrole
      - Azure portal Diagnostics
      - SMTP Relay
      - CAPTCHA voor ontwikkelaarsportal

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>Problemen oplossen
* **Eerste installatie:** wanneer de eerste implementatie van API Management-service in een subnet niet slaagt, wordt u aangeraden eerst een virtuele machine in hetzelfde subnet te implementeren. Vervolgens gaat u extern bureaublad naar de virtuele machine en controleert u of er verbinding is met een van elke onderstaande resource in uw Azure-abonnement
    * Azure Storage blob
    * Azure SQL Database
    * Azure Storage Tabel

  > [!IMPORTANT]
  > Nadat u de connectiviteit hebt gevalideerd, moet u alle resources verwijderen die in het subnet zijn geïmplementeerd voordat u API Management implementeert in het subnet.

* **Controleer de status van de netwerkverbinding:** nadat u API Management hebt geïmplementeerd in het subnet, gebruikt u de portal om de connectiviteit van uw exemplaar met afhankelijkheden zoals Azure Storage. Selecteer in de portal in het linkermenu onder Implementatie en **infrastructuur** de optie **Netwerkverbindingsstatus.**

   :::image type="content" source="media/api-management-using-with-vnet/verify-network-connectivity-status.png" alt-text="Controleer de status van de netwerkverbinding in de portal":::

    * Selecteer **Vereist om** de connectiviteit met de vereiste Azure-services voor de API Management. Een fout geeft aan dat het exemplaar geen kernbewerkingen kan uitvoeren om API's te beheren.
    * Selecteer **Optioneel om** de connectiviteit met optionele services te controleren. Elke fout geeft alleen aan dat de specifieke functionaliteit niet werkt (bijvoorbeeld SMTP). Een fout kan leiden tot een verslechtering van de mogelijkheid om de API Management te gebruiken en bewaken en de vastgelegde SLA te bieden.

Als u verbindingsproblemen wilt oplossen, [bekijkt u Veelvoorkomende problemen met de netwerkconfiguratie en](#network-configuration-issues) lost u de vereiste netwerkinstellingen op.

* **Incrementele updates:** als u wijzigingen aan uw netwerk aan het aanbrengen bent, raadpleegt u [NetworkStatus API](/rest/api/apimanagement/2019-12-01/networkstatus)om te controleren of de API Management-service geen toegang heeft verloren tot een van de kritieke resources waarvan deze afhankelijk is. De verbindingsstatus moet elke 15 minuten worden bijgewerkt.

* **Koppelingen naar resourcenavigatie:** bij het implementeren in Resource Manager vnet-subnetstijl reserveert API Management het subnet door een koppeling voor resourcenavigatie te maken. Als het subnet al een resource van een andere provider bevat, mislukt de **implementatie.** En wanneer u een service API Management naar een ander subnet verplaatst of verwijdert, wordt die koppeling voor resourcenavigatie verwijderd.

## <a name="subnet-size-requirement"></a><a name="subnet-size"></a> Vereiste voor subnetgrootte
Azure reserveert een aantal IP-adressen binnen elk subnet en deze adressen kunnen niet worden gebruikt. De eerste en laatste IP-adressen van de subnetten zijn gereserveerd voor protocolconformatie, samen met drie extra adressen die worden gebruikt voor Azure-services. Zie Zijn er beperkingen voor het gebruik van IP-adressen binnen deze [subnetten?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) voor meer informatie.

Naast de IP-adressen die worden gebruikt door de Azure VNET-infrastructuur, gebruikt elk API Management-exemplaar in het subnet twee IP-adressen per eenheid van premium-SKU of één IP-adres voor de ontwikkelaars-SKU. Elk exemplaar reserveert een extra IP-adres voor de externe load balancer. Bij de implementatie in een intern virtueel netwerk is een extra IP-adres vereist voor de interne load balancer.

Gezien de berekening boven de minimale grootte van het subnet, waarin API Management kunnen worden geïmplementeerd, is /29 dat drie bruikbaar IP-adressen geeft.

Elke extra schaaleenheid van API Management vereist twee extra IP-adressen.

## <a name="routing"></a><a name="routing"></a> Routering
+ Een openbaar IP-adres (VIP) met load balanced wordt gereserveerd om toegang te bieden tot alle service-eindpunten.
+ Een IP-adres uit een SUBNET IP-bereik (DIP) wordt gebruikt voor toegang tot resources binnen het vnet en een openbaar IP-adres (VIP) wordt gebruikt voor toegang tot resources buiten het vnet.
+ Een openbaar IP-adres met load balanced vindt u op de blade Overzicht/Essentials in Azure Portal.

## <a name="limitations"></a><a name="limitations"> </a>Beperkingen
* Voor clients met API-versie 2020-12-01 en eerder mag een subnet met API Management-exemplaren geen andere Azure-resourcetypen bevatten.
* Het subnet en de API Management-service moeten zich in hetzelfde abonnement.
* Een subnet met API Management-exemplaren kan niet worden verplaatst tussen abonnementen.
* Voor implementaties API Management meerdere regio's die zijn geconfigureerd in de modus Intern virtueel netwerk, zijn gebruikers verantwoordelijk voor het beheren van de taakverdeling in meerdere regio's, omdat ze eigenaar zijn van de routering.
* Connectiviteit van een resource in een wereldwijd peered VNET in een andere regio naar API Management-service in de interne modus werkt niet vanwege een platformbeperking. Zie Resources in één virtueel netwerk kunnen niet communiceren met interne [Azure-load balancer in een virtueel peernetwerk](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) voor meer informatie

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"></a> IP-adressen van besturingsvlak

De IP-adressen worden gedeeld door **Azure Environment.** Wanneer u binnenkomende aanvragen toestaat, moet het IP-adres dat is gemarkeerd met **Globaal,** worden toegestaan, samen met het **specifieke** IP-adres van de regio.

| **Azure-omgeving**|   **Regio**|  **IP-adres**|
|-----------------|-------------------------|---------------|
| Openbare Azure-peering| VS - zuid-centraal (globaal)| 104.214.19.224|
| Openbare Azure-peering| VS - noord-centraal (globaal)| 52.162.110.80|
| Openbare Azure-peering| VS - west-centraal| 52.253.135.58|
| Openbare Azure-peering| Korea - centraal| 40.82.157.167|
| Openbare Azure-peering| Verenigd Koninkrijk West| 51.137.136.0|
| Openbare Azure-peering| Japan - west| 40.81.185.8|
| Openbare Azure-peering| VS - noord-centraal| 40.81.47.216|
| Openbare Azure-peering| Verenigd Koninkrijk Zuid| 51.145.56.125|
| Openbare Azure-peering| India - west| 40.81.89.24|
| Openbare Azure-peering| VS - oost| 52.224.186.99|
| Openbare Azure-peering| Europa -west| 51.145.179.78|
| Openbare Azure-peering| Japan - oost| 52.140.238.179|
| Openbare Azure-peering| Frankrijk - centraal| 40.66.60.111|
| Openbare Azure-peering| Canada - oost| 52.139.80.117|
| Openbare Azure-peering| VAE - noord| 20.46.144.85|
| Openbare Azure-peering| Brazilië - zuid| 191.233.24.179|
| Openbare Azure-peering| Brazilië - zuidoost| 191.232.18.181|
| Openbare Azure-peering| Azië - zuidoost| 40.90.185.46|
| Openbare Azure-peering| Zuid-Afrika - noord| 102.133.130.197|
| Openbare Azure-peering| Canada - midden| 52.139.20.34|
| Openbare Azure-peering| Korea - zuid| 40.80.232.185|
| Openbare Azure-peering| India - centraal| 13.71.49.1|
| Openbare Azure-peering| VS - west| 13.64.39.16|
| Openbare Azure-peering| Australië - zuidoost| 20.40.160.107|
| Openbare Azure-peering| Australië - centraal| 20.37.52.67|
| Openbare Azure-peering| India - zuid| 20.44.33.246|
| Openbare Azure-peering| Central US| 13.86.102.66|
| Openbare Azure-peering| Australië - oost| 20.40.125.155|
| Openbare Azure-peering| VS - west 2| 51.143.127.203|
| Openbare Azure-peering| US - oost 2 EUAP| 52.253.229.253|
| Openbare Azure-peering| VS - centraal EUAP| 52.253.159.160|
| Openbare Azure-peering| VS - zuid-centraal| 20.188.77.119|
| Openbare Azure-peering| VS - oost 2| 20.44.72.3|
| Openbare Azure-peering| Europa - noord| 52.142.95.35|
| Openbare Azure-peering| Azië - oost| 52.139.152.27|
| Openbare Azure-peering| Frankrijk - zuid| 20.39.80.2|
| Openbare Azure-peering| Zwitserland - west| 51.107.96.8|
| Openbare Azure-peering| Australië - centraal 2| 20.39.99.81|
| Openbare Azure-peering| UAE - centraal| 20.37.81.41|
| Openbare Azure-peering| Zwitserland - noord| 51.107.0.91|
| Openbare Azure-peering| Zuid-Afrika - west| 102.133.0.79|
| Openbare Azure-peering| Duitsland - west-centraal| 51.116.96.0|
| Openbare Azure-peering| Duitsland - noord| 51.116.0.0|
| Openbare Azure-peering| Noorwegen - oost| 51.120.2.185|
| Openbare Azure-peering| Noorwegen - west| 51.120.130.134|
| Azure China 21Vianet| China - noord (globaal)| 139.217.51.16|
| Azure China 21Vianet| China - oost (globaal)| 139.217.171.176|
| Azure China 21Vianet| China - noord| 40.125.137.220|
| Azure China 21Vianet| China East| 40.126.120.30|
| Azure China 21Vianet| China - noord 2| 40.73.41.178|
| Azure China 21Vianet| China - oost 2| 40.73.104.4|
| Azure Government| USGov Virginia (global)| 52.127.42.160|
| Azure Government| USGov Texas (global)| 52.127.34.192|
| Azure Government| USGov Virginia| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| USGov Arizona| 52.244.32.39|
| Azure Government| USGov Texas| 52.243.154.118|
| Azure Government| USDoD Central| 52.182.32.132|
| Azure Government| USDoD - oost| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>Gerelateerde inhoud
* [Verbinding maken tussen een Virtual Network back-end met behulp van Vpn Gateway](../vpn-gateway/design.md#s2smulti)
* [Een Virtual Network vanuit verschillende implementatiemodellen](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [De API Inspector gebruiken om aanroepen te traceren in Azure API Management](api-management-howto-api-inspector.md)
* [Virtual Network veelgestelde vragen](../virtual-network/virtual-networks-faq.md)
* [Servicetags](../virtual-network/network-security-groups-overview.md#service-tags)

[api-management-using-vnet-menu]: ./media/api-management-using-with-vnet/api-management-menu-vnet.png
[api-management-setup-vpn-select]: ./media/api-management-using-with-vnet/api-management-using-vnet-select.png
[api-management-setup-vpn-add-api]: ./media/api-management-using-with-vnet/api-management-using-vnet-add-api.png
[api-management-vnet-private]: ./media/api-management-using-with-vnet/api-management-vnet-internal.png
[api-management-vnet-public]: ./media/api-management-using-with-vnet/api-management-vnet-external.png

[Enable VPN connections]: #enable-vpn
[Connect to a web service behind VPN]: #connect-vpn
[Related content]: #related-content

[UDRs]: ../virtual-network/virtual-networks-udr-overview.md
[Network Security Group]: ../virtual-network/network-security-groups-overview.md
[ServiceEndpoints]: ../virtual-network/virtual-network-service-endpoints-overview.md
[ServiceTags]: ../virtual-network/network-security-groups-overview.md#service-tags
