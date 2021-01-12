---
title: Azure API Management gebruiken met virtuele netwerken
description: Meer informatie over het instellen van een verbinding met een virtueel netwerk in azure API Management en het verkrijgen van toegang tot webservices.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/10/2020
ms.author: apimpm
ms.custom: references_regions
ms.openlocfilehash: d0d5434de747b48464df1c07f8c7b6a7e785c858
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98070928"
---
# <a name="how-to-use-azure-api-management-with-virtual-networks"></a>Azure API Management gebruiken met virtuele netwerken
Met Azure Virtual Networks (VNETs) kunt u uw Azure-resources in een routeerbaar netwerk (buiten internet) plaatsen waarvan u de toegang beheert. Deze netwerken kunnen vervolgens worden verbonden met uw on-premises netwerken met behulp van verschillende VPN-technologieën. Voor meer informatie over Azure Virtual Networks begint u met de informatie hier: [overzicht van azure Virtual Network](../virtual-network/virtual-networks-overview.md).

Azure API Management kan worden geïmplementeerd in het virtuele netwerk (VNET), zodat het toegang heeft tot back-end-services in het netwerk. De ontwikkelaars Portal en API-gateway kunnen zodanig worden geconfigureerd dat ze toegankelijk zijn vanaf internet of alleen binnen het virtuele netwerk.

> [!NOTE]
> De URL van het importeren van het API-document moet worden gehost op een openbaar toegankelijk Internet adres.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev.md](../../includes/api-management-availability-premium-dev.md)]

## <a name="prerequisites"></a>Vereisten

Voor het uitvoeren van de stappen die in dit artikel worden beschreven, hebt u het volgende nodig:

+ Een actief Azure-abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Een APIM-exemplaar. Zie [een Azure API Management-exemplaar maken](get-started-create-service-instance.md)voor meer informatie.

## <a name="enable-vnet-connection"></a><a name="enable-vpn"> </a>VNET-verbinding inschakelen

### <a name="enable-vnet-connectivity-using-the-azure-portal"></a>VNET-connectiviteit inschakelen met behulp van de Azure Portal

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw API Management-exemplaar te vinden. Zoek en selecteer **API Management Services**.

2. Kies uw API Management-exemplaar.

3. Selecteer **virtueel netwerk**.
4. Configureer het API Management-exemplaar dat in een virtueel netwerk moet worden geïmplementeerd.

    :::image type="content" source="media/api-management-using-with-vnet/api-management-menu-vnet.png" alt-text="Selecteer virtueel netwerk in Azure Portal.":::
    
5. Selecteer het gewenste toegangs type:

    * **Uit**: dit is de standaard instelling. API Management wordt niet geïmplementeerd in een virtueel netwerk.

    * **Extern**: de API Management Gateway en de ontwikkelaars Portal zijn toegankelijk via het open bare Internet via een extern Load Balancer. De gateway kan toegang krijgen tot bronnen in het virtuele netwerk.

        ![Openbare peering][api-management-vnet-public]

    * **Intern**: de API Management Gateway en de ontwikkelaars Portal zijn alleen toegankelijk vanuit het virtuele netwerk via een interne Load Balancer. De gateway kan toegang krijgen tot bronnen in het virtuele netwerk.

        ![Persoonlijke peering][api-management-vnet-private]

6. Als u **extern** of **intern** hebt gekozen, ziet u een lijst met alle regio's waar uw API Management-service is ingericht. Kies een **locatie** en kies vervolgens het **virtuele netwerk** en het **subnet**. De lijst met virtuele netwerken wordt gevuld met zowel de klassieke als de virtuele netwerk bronnen van de resource manager die beschikbaar zijn in uw Azure-abonnementen die zijn ingesteld in de regio die u configureert.

    > [!IMPORTANT]
    > Bij het implementeren van een Azure API Management-exemplaar naar een resource manager VNET, moet de service zich in een toegewijd subnet bevinden dat geen andere resources bevat, met uitzonde ring van Azure API Management-exemplaren. Als er wordt geprobeerd een Azure API Management-exemplaar te implementeren in een resource manager VNET-subnet dat andere resources bevat, mislukt de implementatie.

    Selecteer vervolgens **Toepassen**. De pagina **virtueel netwerk** van uw API Management-exemplaar wordt bijgewerkt met de nieuwe opties voor het virtuele netwerk en het subnet.

    :::image type="content" source="media/api-management-using-with-vnet/api-management-using-vnet-select.png" alt-text="Instellingen van het virtuele netwerk in de portal.":::

7. Selecteer in de bovenste navigatie balk de optie **Opslaan** en selecteer vervolgens **netwerk configuratie Toep assen**.

> [!NOTE]
> Het VIP-adres van het API Management exemplaar wordt gewijzigd telkens VNET wordt ingeschakeld of uitgeschakeld.
> Het VIP-adres wordt ook gewijzigd wanneer API Management wordt verplaatst van **extern** naar **intern** of andersom.
>

> [!IMPORTANT]
> Als u API Management uit een VNET verwijdert of als u de implementatie wijzigt in, kan het eerder gebruikte VNET vergrendeld blijven tot zes uur. Tijdens deze periode is het niet mogelijk om het VNET te verwijderen of om een nieuwe resource te implementeren. Dit gedrag geldt voor clients die gebruikmaken van API-versie 2018-01-01 en eerder. Clients die gebruikmaken van API-versie 2019-01-01 en hoger, het VNET wordt vrijgegeven zodra de bijbehorende API Management service wordt verwijderd.

## <a name="deploy-api-management-into-external-vnet"></a><a name="deploy-apim-external-vnet"> </a>API Management implementeren in extern VNET

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-api-management-create-with-external-vnet%2Fazuredeploy.json)

* **Een API Management-service maken binnen een vnet**: gebruik de cmdlet [New-AzApiManagement](/powershell/module/az.apimanagement/new-azapimanagement) om een Azure API Management-service in een VNET te maken.

* **Een bestaande API Management-service implementeren binnen een VNET**: gebruik de cmdlet [Update-AzApiManagementRegion](/powershell/module/az.apimanagement/update-azapimanagementregion) om een bestaande Azure API Management-service binnen een Virtual Network te verplaatsen.

## <a name="connect-to-a-web-service-hosted-within-a-virtual-network"></a><a name="connect-vnet"> </a>Verbinding maken met een webservice die wordt gehost in een virtueel netwerk
Nadat uw API Management-service is verbonden met het VNET, is toegang tot de back-end-services binnen het netwerk niet anders dan bij het openen van open bare Services. Typ de naam van het lokale IP-adres of de hostnaam (als een DNS-server is geconfigureerd voor het VNET) van uw webservice in het veld **webservice-URL** wanneer u een nieuwe API maakt of een bestaande bewerkt.

![API van VPN toevoegen][api-management-setup-vpn-add-api]

## <a name="common-network-configuration-issues"></a><a name="network-configuration-issues"> </a>Veelvoorkomende problemen met de netwerk configuratie
Hieronder vindt u een lijst met veelvoorkomende fouten die zich kunnen voordoen tijdens de implementatie van API Management-service in een Virtual Network.

* **Aangepaste installatie van DNS-server**: de API Management-service is afhankelijk van verschillende Azure-Services. Als API Management wordt gehost in een VNET met een aangepaste DNS-server, moet deze de hostnamen van deze Azure-Services omzetten. Volg [deze](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) richt lijnen voor aangepaste DNS-instellingen. Zie de onderstaande tabel poorten en andere netwerk vereisten voor naslag informatie.

> [!IMPORTANT]
> Als u van plan bent om een aangepaste DNS-server (s) te gebruiken voor het VNET, moet u deze instellen **voordat** u een API Management-service implementeert. Anders moet u de API Management-service telkens bijwerken wanneer u de DNS-server (s) wijzigt door de [bewerking netwerk configuratie Toep assen](/rest/api/apimanagement/2019-12-01/apimanagementservice/applynetworkconfigurationupdates) uit te voeren

* **Poorten die vereist zijn voor API Management**: binnenkomend en uitgaand verkeer in het Subnet waarin API Management wordt geïmplementeerd, kunnen worden beheerd met behulp van de [netwerk beveiligings groep][Network Security Group]. Als een van deze poorten niet beschikbaar is, werkt API Management mogelijk niet goed en wordt deze mogelijk niet meer toegankelijk. Als een of meer van deze poorten zijn geblokkeerd, is een ander veelvoorkomend probleem met de configuratie bij het gebruik van API Management met een VNET.

<a name="required-ports"></a> Wanneer een API Management service-exemplaar wordt gehost in een VNET, worden de poorten in de volgende tabel gebruikt.

| Bron/doel poort (en) | Richting          | Transport Protocol |   [Service Tags](../virtual-network/network-security-groups-overview.md#service-tags) <br> Bron/doel   | Doel ( \* )                                                 | Virtual Network type |
|------------------------------|--------------------|--------------------|---------------------------------------|-------------------------------------------------------------|----------------------|
| */[80], 443                  | Inkomend            | TCP                | INTERNET/VIRTUAL_NETWORK            | Client communicatie met API Management                      | Extern             |
| */3443                     | Inkomend            | TCP                | ApiManagement/VIRTUAL_NETWORK       | Beheer eindpunt voor Azure Portal en Power shell         | Externe & intern  |
| */443                  | Uitgaand           | TCP                | VIRTUAL_NETWORK/opslag             | **Afhankelijkheid van Azure Storage**                             | Externe & intern  |
| */443                  | Uitgaand           | TCP                | VIRTUAL_NETWORK-AzureActiveDirectory | Afhankelijkheid van [Azure Active Directory](api-management-howto-aad.md) en Azure-sleutel kluis                  | Externe & intern  |
| */1433                     | Uitgaand           | TCP                | VIRTUAL_NETWORK/SQL                 | **Toegang tot Azure SQL-eind punten**                           | Externe & intern  |
| */433                     | Uitgaand           | TCP                | VIRTUAL_NETWORK-AzureKeyVault                 | **Toegang tot Azure-sleutel kluis**                           | Externe & intern  |
| */5671, 5672, 443          | Uitgaand           | TCP                | VIRTUAL_NETWORK-EventHub            | Afhankelijkheid voor [logboek registratie van Event hub-beleid](api-management-howto-log-event-hubs.md) en bewakings agent | Externe & intern  |
| */445                      | Uitgaand           | TCP                | VIRTUAL_NETWORK/opslag             | Afhankelijkheid van de Azure-bestands share voor [Git](api-management-configuration-repository-git.md)                      | Externe & intern  |
| */443, 12000                     | Uitgaand           | TCP                | VIRTUAL_NETWORK-Cloud            | Status-en bewakings uitbreiding         | Externe & intern  |
| */1886, 443                     | Uitgaand           | TCP                | VIRTUAL_NETWORK-AzureMonitor         | [Diagnostische logboeken en metrische gegevens](api-management-howto-use-azure-monitor.md), [resource Health](../service-health/resource-health-overview.md) en [Application Insights](api-management-howto-app-insights.md) publiceren                   | Externe & intern  |
| */25, 587, 25028                       | Uitgaand           | TCP                | VIRTUAL_NETWORK/INTERNET            | Verbinding maken met SMTP relay voor het verzenden van e-mail berichten                    | Externe & intern  |
| */6381-6383              | Binnenkomende &-uitgaand | TCP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | Toegang tot de redis-service voor [cache](api-management-caching-policies.md) beleidsregels tussen computers         | Externe & intern  |
| */4290              | Binnenkomende &-uitgaand | UDP                | VIRTUAL_NETWORK/VIRTUAL_NETWORK     | Synchronisatie tellers voor beleids regels voor [frequentie limiet](api-management-access-restriction-policies.md#LimitCallRateByKey) tussen computers         | Externe & intern  |
| * / *                        | Inkomend            | TCP                | AZURE_LOAD_BALANCER/VIRTUAL_NETWORK | Load Balancer van Azure-infra structuur                          | Externe & intern  |

>[!IMPORTANT]
> De poorten waarvoor het *doel* **vet** is, zijn vereist voor het implementeren van API Management service. Het blok keren van de andere poorten leidt echter tot **degradatie** van de mogelijkheid om de actieve service te gebruiken en te **bewaken en de vastgelegde Sla** aan te bieden.

+ **TLS-functionaliteit**: voor het inschakelen van TLS/SSL-certificaat keten maken en valideren van de API Management-service is uitgaande netwerk verbinding vereist met ocsp.msocsp.com, mscrl.microsoft.com en CRL.Microsoft.com. Deze afhankelijkheid is niet vereist als een certificaat dat u uploadt naar API Management de volledige keten naar de basis van de CA bevatten.

+ **DNS-toegang**: uitgaande toegang op poort 53 is vereist voor communicatie met DNS-servers. Als er een aangepaste DNS-server bestaat aan het andere uiteinde van een VPN-gateway, moet de DNS-server bereikbaar zijn vanaf het subnet dat als host fungeert voor de API Management.

+ **Metrische gegevens en status controle**: uitgaande netwerk connectiviteit met Azure monitoring-eind punten die worden omgezet in de volgende domeinen. Zoals in de tabel wordt weer gegeven, worden deze Url's weer gegeven onder de tag AzureMonitor-service voor gebruik met netwerk beveiligings groepen.

    | Azure-omgeving | Eindpunten                                                                                                                                                                                                                                                                                                                                                              |
    |-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Openbare Azure-peering      | <ul><li>gcs.prod.monitoring.core.windows.net (**Nieuw**)</li><li>prod.warmpath.msftcloudes.com (**om te worden afgeschaft**)</li><li>global.prod.microsoftmetrics.com (**Nieuw**)</li><li>global.metrics.nsatc.net (**om te worden afgeschaft**)</li><li>shoebox2.prod.microsoftmetrics.com (**Nieuw**)</li><li>shoebox2.metrics.nsatc.net (**om te worden afgeschaft**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com (**Nieuw**)</li><li>prod3.metrics.nsatc.net (**om te worden afgeschaft**)</li><li>prod3-black.prod.microsoftmetrics.com (**Nieuw**)</li><li>prod3-black.prod3.metrics.nsatc.net (**om te worden afgeschaft**)</li><li>prod3-red.prod.microsoftmetrics.com (**Nieuw**)</li><li>prod3-red.prod3.metrics.nsatc.net (**om te worden afgeschaft**)</li><li>gcs.prod.warm.ingestion.monitoring.azure.com</li></ul> |
    | Azure Government  | <ul><li>fairfax.warmpath.usgovcloudapi.net</li><li>global.prod.microsoftmetrics.com (**Nieuw**)</li><li>global.metrics.nsatc.net (**om te worden afgeschaft**)</li><li>shoebox2.prod.microsoftmetrics.com (**Nieuw**)</li><li>shoebox2.metrics.nsatc.net (**om te worden afgeschaft**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com (**Nieuw**)</li><li>prod3.metrics.nsatc.net (**om te worden afgeschaft**)</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.us</li></ul>                                                                                                                                                                                                                                                |
    | Azure China 21Vianet     | <ul><li>mooncake.warmpath.chinacloudapi.cn</li><li>global.prod.microsoftmetrics.com (**Nieuw**)</li><li>global.metrics.nsatc.net (**om te worden afgeschaft**)</li><li>shoebox2.prod.microsoftmetrics.com (**Nieuw**)</li><li>shoebox2.metrics.nsatc.net (**om te worden afgeschaft**)</li><li>shoebox2-red.prod.microsoftmetrics.com</li><li>shoebox2-black.prod.microsoftmetrics.com</li><li>shoebox2-red.shoebox2.metrics.nsatc.net</li><li>shoebox2-black.shoebox2.metrics.nsatc.net</li><li>prod3.prod.microsoftmetrics.com (**Nieuw**)</li><li>prod3.metrics.nsatc.net (**om te worden afgeschaft**)</li><li>prod3-black.prod.microsoftmetrics.com</li><li>prod3-red.prod.microsoftmetrics.com</li><li>prod5.prod.microsoftmetrics.com</li><li>prod5-black.prod.microsoftmetrics.com</li><li>prod5-red.prod.microsoftmetrics.com</li><li>gcs.prod.warm.ingestion.monitoring.azure.cn</li></ul>                                                                                                                                                                                                                                                |

  >[!IMPORTANT]
  > De wijziging van de bovenstaande clusters met DNS-zone **. nsatc.net** naar **. microsoftmetrics.com** is doorgaans een DNS-wijziging. Het IP-adres van het cluster wordt niet gewijzigd.

+ **Regionale service Tags**: NSG regels voor het toestaan van uitgaande connectiviteit naar opslag-, SQL-en Event hubs-service tags kunnen de regionale versies van deze tags gebruiken die overeenkomen met de regio die het API Management-exemplaar bevat (bijvoorbeeld Storage. westus voor een API Management-exemplaar in de regio vs-West). In implementaties met meerdere regio's moet de NSG in elke regio verkeer naar de service tags voor die regio en de primaire regio toestaan.

    > [!IMPORTANT]
    > Als u het publiceren van de [ontwikkelaars Portal](api-management-howto-developer-portal.md) wilt inschakelen voor een API Management-exemplaar in een virtueel netwerk, moet u ervoor zorgen dat u ook uitgaande connectiviteit met Blob Storage in de regio vs West toestaat. Gebruik bijvoorbeeld het tag **Storage. westus** service in een NSG-regel. Connectiviteit met Blob Storage in de regio vs-West is momenteel vereist voor het publiceren van de ontwikkelaars portal voor een API Management exemplaar.

+ **SMTP-relay**: uitgaande netwerk verbinding voor de SMTP-relay, die wordt omgezet onder de host `smtpi-co1.msn.com` ,, `smtpi-ch1.msn.com` `smtpi-db3.msn.com` `smtpi-sin.msn.com` en `ies.global.microsoft.com`

+ **Ontwikkelaars Portal CAPTCHA**: uitgaand netwerk connectiviteit voor de CAPTCHA van de ontwikkelaars Portal, die wordt omgezet onder de hosts `client.hip.live.com` en `partner.hip.live.com` .

+ **Azure Portal diagnostische gegevens**: als u de stroom van Diagnostische logboeken van Azure Portal wilt inschakelen wanneer u de extensie API management binnen een Virtual Network gebruikt, is uitgaande toegang tot `dc.services.visualstudio.com` op poort 443 vereist. Dit helpt bij het oplossen van problemen die u kunt tegen komen wanneer u uitbrei ding gebruikt.

+ **Azure Load Balancer**: het toestaan van een inkomend verzoek van een service label `AZURE_LOAD_BALANCER` is geen vereiste voor de `Developer` SKU, omdat er slechts één reken eenheid achter wordt geïmplementeerd. Maar inkomend van [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md) wordt van cruciaal belang bij het schalen naar een hogere SKU, `Premium` zoals bij het mislukken van de status test van Load Balancer, mislukt een implementatie.

+ **Application Insights**: als [Azure-toepassing Insights](api-management-howto-app-insights.md) -bewaking is ingeschakeld op API Management, moeten er vanaf de Virtual Network een uitgaande verbinding met het [telemetrische eind punt](../azure-monitor/app/ip-addresses.md#outgoing-ports) worden toegestaan. 

+ **Verkeer naar een on-premises firewall forceren via Express route of virtueel netwerk apparaat**: een algemene klant configuratie is het definiëren van een eigen standaard route (0.0.0.0/0) die ervoor zorgt dat alle verkeer van het API Management overgedragen subnet wordt gestroomd via een on-premises firewall of een virtueel netwerk apparaat. Deze verkeers stroom invariably verbreekt de connectiviteit met Azure API Management, omdat het uitgaande verkeer on-premises of NAT zou worden geblokkeerd door een niet-herken bare set adressen die niet meer werken met verschillende Azure-eind punten. Voor de oplossing moet u een aantal dingen doen:

  * Schakel service-eind punten in op het subnet waarin de API Management-service is geïmplementeerd. [Service-eind punten][ServiceEndpoints] moeten worden ingeschakeld voor Azure Sql, Azure Storage, Azure EventHub en Azure ServiceBus. Als u eind punten rechtstreeks vanuit API Management overgedragen subnet naar deze services inschakelt, kunnen ze het Microsoft Azure backbone-netwerk gebruiken dat een optimale route ring voor service verkeer biedt. Als u service-eind punten gebruikt met een API-beheer met geforceerde tunneling, wordt het bovenstaande verkeer van Azure-Services niet geforceerd getunneld. Het andere API Management verkeer met Service afhankelijkheid wordt geforceerd getunneld en kan niet verloren gaan of de API Management-service werkt niet goed.
    
  * Alle verkeer van het besturings element van Internet naar het beheer eindpunt van uw API Management-service wordt doorgestuurd via een specifieke set inkomende IP-adressen die worden gehost door API Management. Wanneer het verkeer geforceerde tunneling is, worden de antwoorden niet symmetrisch toegewezen aan deze IP-adressen voor binnenkomende bronnen. Om de beperking te overwinnen, moeten we de volgende door de gebruiker gedefinieerde routes ([udr's][UDRs]) toevoegen aan het stuur verkeer terug naar Azure door het doel van deze hostroutes in te stellen op internet. De set met inkomende Ip's voor besturings vlak verkeer is gedocumenteerd [IP-adressen van besturings elementen](#control-plane-ips)

  * Voor andere API Management-service afhankelijkheden die geforceerde tunnels zijn, moet er een manier zijn om de hostnaam op te lossen en te bereiken aan het eind punt. Dit zijn onder andere
      - Metrische gegevens en status controle
      - Diagnostische gegevens Azure Portal
      - SMTP-relay
      - Ontwikkelaars Portal CAPTCHA

## <a name="troubleshooting"></a><a name="troubleshooting"> </a>Problemen oplossen
* **Eerste Setup**: wanneer de eerste implementatie van API Management service in een subnet mislukt, wordt u aangeraden eerst een virtuele machine in hetzelfde subnet te implementeren. Volgende extern bureau blad in de virtuele machine en controleren of er verbinding is met een van de onderstaande resources in uw Azure-abonnement
    * Azure Storage BLOB
    * Azure SQL Database
    * Azure Storage tabel

  > [!IMPORTANT]
  > Nadat u de connectiviteit hebt gevalideerd, moet u alle resources die in het subnet zijn geïmplementeerd, verwijderen voordat u API Management in het subnet implementeert.

* **Status van de netwerk verbinding controleren**: nadat API Management in het subnet is geïmplementeerd, gebruikt u de portal om de connectiviteit van uw exemplaar te controleren op afhankelijkheden zoals Azure Storage. Selecteer in de portal, in het menu links onder **implementatie en infra structuur**, de status van de **netwerk verbinding**.

   :::image type="content" source="media/api-management-using-with-vnet/verify-network-connectivity-status.png" alt-text="Controleer de status van de netwerk verbinding in de portal":::

    * Selecteer **vereist** om de verbinding met de vereiste Azure-services voor API management te controleren. Een fout geeft aan dat het exemplaar geen kern bewerkingen voor het beheren van Api's kan uitvoeren.
    * Selecteer **optioneel** om de connectiviteit met optionele services te controleren. Elke fout geeft alleen aan dat de specifieke functionaliteit niet werkt (bijvoorbeeld SMTP). Een fout kan leiden tot degradatie van de mogelijkheid om het API Management-exemplaar te gebruiken en te bewaken en de vastgelegde SLA aan te bieden.

Als u verbindings problemen wilt oplossen, kunt u [algemene problemen met de netwerk configuratie](#network-configuration-issues) bekijken en de vereiste netwerk instellingen herstellen.

* **Incrementele updates**: als u wijzigingen aanbrengt in uw netwerk, raadpleegt u de [NetworkStatus-API](/rest/api/apimanagement/2019-12-01/networkstatus)om te controleren of de API Management-service geen toegang meer heeft tot een van de kritieke resources waarvan deze afhankelijk is. De connectiviteits status moet elke 15 minuten worden bijgewerkt.

* **Bronnen navigatie koppelingen**: bij het implementeren van het Resource Manager-type vnet-subnet, API Management het subnet reserveert door een resource navigatie koppeling te maken. Als het subnet al een resource van een andere provider bevat, **mislukt** de implementatie. En wanneer u een API Management-service naar een ander subnet verplaatst of verwijdert, worden de navigatie koppeling naar die resource verwijderd.

## <a name="subnet-size-requirement"></a><a name="subnet-size"></a> Vereiste voor de grootte van het subnet
Azure reserveert een aantal IP-adressen binnen elk subnet en deze adressen kunnen niet worden gebruikt. Het eerste en laatste IP-adres van de subnetten zijn gereserveerd voor protocol conformiteit, samen met drie meer adressen die worden gebruikt voor Azure-Services. Zie [zijn er beperkingen voor het gebruik van IP-adressen in deze subnetten?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets) voor meer informatie.

Naast de IP-adressen die worden gebruikt door de Azure VNET-infra structuur, maakt elk API Management-exemplaar in het subnet gebruik van twee IP-adressen per eenheid van Premium SKU of één IP-adres voor de Developer-SKU. Elk exemplaar reserveert een extra IP-adres voor de externe load balancer. Bij het implementeren in een intern virtueel netwerk is een extra IP-adres voor de interne load balancer vereist.

Gezien de berekening boven de minimale grootte van het subnet, waarbij API Management kan worden geïmplementeerd, is/29 dat drie bruikbare IP-adressen bevat.

Voor elke extra schaal eenheid van API Management zijn twee meer IP-adressen vereist.

## <a name="routing"></a><a name="routing"></a> Route ring
+ Een openbaar IP-adres (VIP) met gelijke taak verdeling wordt gereserveerd om toegang te bieden tot alle service-eind punten.
+ Een IP-adres uit een subnet IP-bereik (DIP) wordt gebruikt voor toegang tot bronnen binnen het vnet en een openbaar IP-adres (VIP) wordt gebruikt voor toegang tot bronnen buiten het vnet.
+ Het open bare IP-adres met taak verdeling vindt u op de Blade Overzicht/essentiële elementen in het Azure Portal.

## <a name="limitations"></a><a name="limitations"> </a>Beperkingen
* Een subnet met API Management exemplaren kan geen andere Azure-resource typen bevatten.
* Het subnet en de API Management-service moeten zich in hetzelfde abonnement bevinden.
* Een subnet met API Management exemplaren kan niet worden verplaatst voor abonnementen.
* Voor API Management implementaties in meerdere regio's die in de interne virtuele netwerk modus zijn geconfigureerd, zijn gebruikers verantwoordelijk voor het beheer van de taak verdeling over meerdere regio's, aangezien ze eigenaar zijn van de route ring.
* De connectiviteit van een bron in een globaal gepeerd VNET in een andere regio naar API Management service in de interne modus werkt niet als gevolg van de platform beperking. Zie [resources in één virtueel netwerk kunnen niet communiceren met interne Load Balancer van Azure in een peered virtueel netwerk](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) voor meer informatie

## <a name="control-plane-ip-addresses"></a><a name="control-plane-ips"></a> IP-adressen van besturings vlak

De IP-adressen worden gedeeld door **Azure-omgeving**. Wanneer het IP-adres voor binnenkomende aanvragen dat is gemarkeerd met **Global** , moet worden toegestaan naast het specifieke IP-adres van de **regio** .

| **Azure-omgeving**|   **Regio**|  **IP-adres**|
|-----------------|-------------------------|---------------|
| Openbare Azure-peering| VS Zuid-Centraal (wereld wijd)| 104.214.19.224|
| Openbare Azure-peering| VS Noord-Centraal (wereld wijd)| 52.162.110.80|
| Openbare Azure-peering| VS - west-centraal| 52.253.135.58|
| Openbare Azure-peering| Korea - centraal| 40.82.157.167|
| Openbare Azure-peering| Verenigd Koninkrijk West| 51.137.136.0|
| Openbare Azure-peering| Japan - west| 40.81.185.8|
| Openbare Azure-peering| VS - noord-centraal| 40.81.47.216|
| Openbare Azure-peering| Verenigd Koninkrijk Zuid| 51.145.56.125|
| Openbare Azure-peering| India - west| 40.81.89.24|
| Openbare Azure-peering| VS - oost| 52.224.186.99|
| Openbare Azure-peering| Europa -west| 51.145.179.78|
| Openbare Azure-peering| Japan East| 52.140.238.179|
| Openbare Azure-peering| Frankrijk - centraal| 40.66.60.111|
| Openbare Azure-peering| Canada - oost| 52.139.80.117|
| Openbare Azure-peering| VAE - noord| 20.46.144.85|
| Openbare Azure-peering| Brazil South| 191.233.24.179|
| Openbare Azure-peering| Brazilië-Zuidoost| 191.232.18.181|
| Openbare Azure-peering| Azië - zuidoost| 40.90.185.46|
| Openbare Azure-peering| Zuid-Afrika - noord| 102.133.130.197|
| Openbare Azure-peering| Canada - midden| 52.139.20.34|
| Openbare Azure-peering| Korea - zuid| 40.80.232.185|
| Openbare Azure-peering| Central India| 13.71.49.1|
| Openbare Azure-peering| VS - west| 13.64.39.16|
| Openbare Azure-peering| Australia Southeast| 20.40.160.107|
| Openbare Azure-peering| Australië - centraal| 20.37.52.67|
| Openbare Azure-peering| India - zuid| 20.44.33.246|
| Openbare Azure-peering| Central US| 13.86.102.66|
| Openbare Azure-peering| Australië - oost| 20.40.125.155|
| Openbare Azure-peering| West US 2| 51.143.127.203|
| Openbare Azure-peering| VS-Oost 2 EUAP| 52.253.229.253|
| Openbare Azure-peering| VS - centraal EUAP| 52.253.159.160|
| Openbare Azure-peering| South Central US| 20.188.77.119|
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
| Azure China 21Vianet| China-noord (globaal)| 139.217.51.16|
| Azure China 21Vianet| China-oost (globaal)| 139.217.171.176|
| Azure China 21Vianet| China - noord| 40.125.137.220|
| Azure China 21Vianet| China East| 40.126.120.30|
| Azure China 21Vianet| China - noord 2| 40.73.41.178|
| Azure China 21Vianet| China - oost 2| 40.73.104.4|
| Azure Government| USGove Virginia (Global)| 52.127.42.160|
| Azure Government| USGov Texas (wereld wijd)| 52.127.34.192|
| Azure Government| USGov Virginia| 52.227.222.92|
| Azure Government| USGov Iowa| 13.73.72.21|
| Azure Government| USGov Arizona| 52.244.32.39|
| Azure Government| USGov Texas| 52.243.154.118|
| Azure Government| USDoD-centraal| 52.182.32.132|
| Azure Government| USDoD-Oost| 52.181.32.192|

## <a name="related-content"></a><a name="related-content"> </a>Gerelateerde inhoud
* [Een Virtual Network met back-end verbinden via een VPN-gateway](../vpn-gateway/design.md#s2smulti)
* [Een Virtual Network van verschillende implementatie modellen verbinden](../vpn-gateway/vpn-gateway-connect-different-deployment-models-powershell.md)
* [De API-Inspector gebruiken voor het traceren van aanroepen in azure API Management](api-management-howto-api-inspector.md)
* [Veelgestelde vragen over Virtual Network](../virtual-network/virtual-networks-faq.md)
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
