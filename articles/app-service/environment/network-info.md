---
title: Aandachtspunten voor netwerken
description: Meer informatie over het ASE-netwerk verkeer en over het instellen van netwerk beveiligings groepen en door de gebruiker gedefinieerde routes met uw ASE.
author: ccompy
ms.assetid: 955a4d84-94ca-418d-aa79-b57a5eb8cb85
ms.topic: article
ms.date: 07/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 91b6134e7c809a8af75aa1cf23523e352e0a1a0e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95997338"
---
# <a name="networking-considerations-for-an-app-service-environment"></a>Netwerk overwegingen voor een App Service Environment #

## <a name="overview"></a>Overzicht ##

 Azure [app service Environment][Intro] is een implementatie van Azure app service in een subnet in uw Azure Virtual Network (VNet). Er zijn twee implementatie typen voor een App Service omgeving (ASE):

- **Externe ASE**: de door ASE gehoste apps worden weer gegeven op een IP-adres dat toegankelijk is via internet. Zie [een externe ASE maken][MakeExternalASE]voor meer informatie.
- **ILB ASE**: Hiermee worden de door ASE gehoste apps op een IP-adres in uw VNet weer gegeven. Het interne eind punt is een interne load balancer (ILB). Dit is de reden waarom het een ILB ASE wordt genoemd. Zie [een ILB-ASE maken en gebruiken][MakeILBASE]voor meer informatie.

Alle as, External en ILB hebben een open bare VIP die wordt gebruikt voor inkomend beheer verkeer en als het van-adres bij het maken van aanroepen van de ASE aan Internet. De aanroepen van een ASE die naar Internet gaan, verlaten het VNet via de VIP die is toegewezen voor de ASE. Het open bare IP-adres van deze VIP is het bron-IP-adres voor alle aanroepen van de ASE die naar Internet gaan. Als de apps in uw ASE aanroepen naar resources in uw VNet of via een VPN, is de bron-IP een van de Ip's in het subnet dat wordt gebruikt door uw ASE. Omdat de ASE zich binnen het VNet bevindt, kan het ook toegang krijgen tot bronnen in het VNet zonder extra configuratie. Als het VNet is verbonden met uw on-premises netwerk, hebben apps in uw ASE ook toegang tot resources zonder extra configuratie.

![Externe ASE][1] 

Als u een externe ASE hebt, is de open bare VIP ook het eind punt waarop uw ASE-apps worden omgezet voor:

* HTTP/S 
* FTP/S
* Webimplementatie
* Foutopsporing op afstand

![ILB ASE][2]

Als u een ILB ASE hebt, is het adres van het ILB-adres het eind punt voor HTTP/S, FTP/S, webimplementatie en fout opsporing op afstand.

## <a name="ase-subnet-size"></a>Grootte van ASE-subnet ##

De grootte van het subnet dat wordt gebruikt voor het hosten van een ASE kan niet worden gewijzigd nadat de ASE is geïmplementeerd.  De ASE maakt gebruik van een adres voor elke infrastructuur functie en voor elk exemplaar van de geïsoleerde App Service-abonnement.  Daarnaast zijn er vijf adressen die worden gebruikt door Azure-netwerken voor elk subnet dat wordt gemaakt.  Voor een ASE zonder App Service-abonnementen worden 12 adressen gebruikt voordat u een app maakt.  Als het een ILB-ASE is, worden er 13 adressen gebruikt voordat u een app maakt in die ASE. Wanneer u uw ASE uitbreidt, worden de infrastructuur rollen toegevoegd elke meervoud van 15 en 20 van uw App Service-abonnement.

   > [!NOTE]
   > Niets anders kan zich in het subnet bevinden, maar de ASE. Zorg ervoor dat u een adres ruimte kiest voor toekomstige groei. U kunt deze instelling later niet meer wijzigen. U kunt het beste een grootte van `/24` met 256-adressen aanraden.

Wanneer u omhoog of omlaag schaalt, worden nieuwe rollen van de juiste grootte toegevoegd. vervolgens worden uw workloads van de huidige grootte gemigreerd naar de doel grootte. De oorspronkelijke Vm's worden alleen verwijderd nadat de werk belastingen zijn gemigreerd. Als u een ASE met 100 ASP-exemplaren had, is er een periode waarin u het aantal Vm's moet verdubbelen.  Daarom wordt u aangeraden een '/24 ' te gebruiken voor alle wijzigingen die u mogelijk nodig hebt.  

## <a name="ase-dependencies"></a>ASE-afhankelijkheden ##

### <a name="ase-inbound-dependencies"></a>Inkomende afhankelijkheden ASE ###

Alleen voor de ASE van de ASE moeten de volgende poorten zijn geopend:

| Gebruik | Van | Tot |
|-----|------|----|
| Beheer | App Service-beheer adressen | ASE-subnet: 454, 455 |
|  ASE interne communicatie | ASE-subnet: alle poorten | ASE-subnet: alle poorten
|  Azure load balancer-binnenkomend toestaan | Azure Load Balancer | ASE-subnet: 16001

Er zijn twee andere poorten die kunnen worden weer gegeven als geopend op een poort scan, 7654 en 1221. Ze beantwoorden met een IP-adres en niets meer. Ze kunnen indien gewenst worden geblokkeerd. 

Het inkomende beheer verkeer bevat naast systeem bewaking ook de opdracht en controle van de ASE. De bron adressen voor dit verkeer worden weer gegeven in het document met [ASE-beheer adressen][ASEManagement] . De netwerk beveiligings configuratie moet toegang toestaan vanaf de ASE-beheer adressen op de poorten 454 en 455. Als u de toegang van deze adressen blokkeert, wordt uw ASE beschadigd en wordt de status onderbroken. Het TCP-verkeer dat zich in de poorten 454 en 455 bevindt, moet vanuit hetzelfde VIP worden teruggestuurd of u hebt een asymmetrisch routerings probleem. 

Binnen het ASE-subnet zijn er veel poorten gebruikt voor communicatie tussen interne onderdelen en ze kunnen worden gewijzigd. Hiervoor moeten alle poorten in het ASE-subnet toegankelijk zijn vanuit het ASE-subnet. 

Voor de communicatie tussen de Azure load balancer en het ASE-subnet moeten de mini maal te openen poorten 454, 455 en 16001 zijn. De 16001-poort wordt gebruikt voor het keepen van keepalive-verkeer tussen de load balancer en de ASE. Als u gebruikmaakt van een ILB-ASE, kunt u het verkeer vergren delen tot alleen de 454, 455 en 16001 poorten.  Als u een externe ASE gebruikt, moet u rekening houden met de normale toegangs poorten voor apps.  

De andere poorten waarmee u rekening moet houden, zijn de toepassings poorten:

| Gebruik | Poorten |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP-FTPS    | 21, 990, 10001-10020 |
|  Visual Studio externe fout opsporing  |  4020, 4022, 4024 |
|  Web Deploy-Service | 8172 |

Als u de toepassings poorten blokkeert, kan uw ASE nog steeds werken, maar kan uw app dit niet doen.  Als u met apps toegewezen IP-adressen met een externe ASE gebruikt, moet u verkeer toestaan van de Ip's die aan uw apps zijn toegewezen in het ASE-subnet op de poorten die worden weer gegeven op de pagina ASE Portal > IP-adressen.

### <a name="ase-outbound-dependencies"></a>Uitgaande afhankelijkheden ASE ###

Voor uitgaande toegang is een ASE afhankelijk van meerdere externe systemen. Veel van deze systeem afhankelijkheden zijn gedefinieerd met DNS-namen en worden niet toegewezen aan een vaste set IP-adressen. De ASE vereist daarom uitgaande toegang vanuit het ASE-subnet naar alle externe IP-adressen over diverse poorten. 

De ASE communiceert met internet toegankelijke adressen op de volgende poorten:

| Gebruikt | Poorten |
|-----|------|
| DNS | 53 |
| NTP | 123 |
| CRL, Windows-updates, Linux-afhankelijkheden, Azure-Services | 80/443 |
| Azure SQL | 1433 | 
| Bewaking | 12000 |

De uitgaande afhankelijkheden worden weer gegeven in het document met een beschrijving van het [vergren delen van app service Environment uitgaand verkeer](./firewall-integration.md). Als de ASE geen toegang meer heeft tot de afhankelijkheden, werkt deze niet meer. Wanneer dit lang genoeg duurt, wordt de ASE onderbroken. 

### <a name="customer-dns"></a>Klant-DNS ###

Als het VNet is geconfigureerd met een door de klant gedefinieerde DNS-server, gebruiken de werk belasting van de Tenant. De ASE maakt gebruik van Azure DNS voor beheer doeleinden. Als het VNet is geconfigureerd met een door de klant geselecteerde DNS-server, moet de DNS-server bereikbaar zijn vanuit het subnet dat de ASE bevat.

U kunt de DNS-omzetting van uw web-app testen met behulp van de console opdracht *nameresolver*. Ga naar het venster fout opsporing op uw SCM-site voor uw app of ga naar de app in de portal en selecteer console. Vanuit de shell-prompt kunt u de opdracht *nameresolver* uitgeven samen met de DNS-naam die u wilt zoeken. Het resultaat dat u weer krijgt, is hetzelfde als de manier waarop uw app tijdens het maken van dezelfde zoek actie wordt weer gegeven. Als u nslookup gebruikt, kunt u in plaats daarvan een zoek opdracht uitvoeren met behulp van Azure DNS.

Als u de DNS-instelling van het VNet wijzigt waarin uw ASE zich bevindt, moet u de ASE opnieuw opstarten. Om te voor komen dat uw ASE opnieuw wordt opgestart, is het raadzaam om uw DNS-instellingen voor uw VNet te configureren voordat u uw ASE maakt.  

<a name="portaldep"></a>

## <a name="portal-dependencies"></a>Portal dependencies (Portal-afhankelijkheden) ##

Naast de functionele afhankelijkheden van ASE zijn er enkele extra items die betrekking hebben op de portal-ervaring. Enkele van de mogelijkheden van de Azure Portal zijn afhankelijk van directe toegang tot de _SCM-site_. Voor elke app in Azure App Service zijn er twee Url's. De eerste URL is om toegang te krijgen tot uw app. De tweede URL is om toegang te krijgen tot de SCM-site, die ook wel de _kudu-console_ wordt genoemd. Functies die gebruikmaken van de SCM-site zijn onder andere:

-   WebJobs
-   Functions
-   Logboekstreaming
-   Kudu
-   Uitbreidingen
-   Proces Verkenner
-   Console

Wanneer u een ILB-ASE gebruikt, is de SCM-site niet toegankelijk van buiten het VNet. Sommige mogelijkheden werken niet vanuit de app-Portal, omdat ze toegang nodig hebben tot de SCM-site van een app. U kunt rechtstreeks verbinding maken met de SCM-site in plaats van de portal te gebruiken. 

Als uw ILB ASE de domein naam *contoso.appserviceenvironment.net* is en de naam van uw app *testapp* is, wordt de app bereikt op *testapp.contoso.appserviceenvironment.net*. De SCM-site die ermee meegaat, is bereikt op *testapp.scm.contoso.appserviceenvironment.net*.

## <a name="ase-ip-addresses"></a>IP-adressen van ASE ##

Een ASE heeft enkele IP-adressen waarvan u op de hoogte moet zijn. Dit zijn:

- **Openbaar binnenkomend IP-adres**: wordt gebruikt voor app-verkeer in een extern ASE en beheer verkeer in zowel een externe ASE als een ILB-ASE.
- **Uitgaand openbaar IP-adres** dat wordt gebruikt als het ' van ' IP-adres voor uitgaande verbindingen van de ASE die het VNet verlaten, dat niet wordt gerouteerd naar een VPN-verbinding.
- **ILB IP-adres**: het IP-adres van ILB bestaat alleen in een ILB ASE.
- **Op apps gebaseerde SSL-adressen die zijn toegewezen op basis van een app**: alleen mogelijk met een externe ASE en wanneer SSL op basis van IP is geconfigureerd.

Al deze IP-adressen zijn zichtbaar in de Azure Portal van de ASE-gebruikers interface. Als u een ILB-ASE hebt, wordt het IP-adres voor de ILB weer gegeven.

   > [!NOTE]
   > Deze IP-adressen worden niet gewijzigd zolang uw ASE actief blijft.  Als uw ASE wordt onderbroken en teruggezet, worden de adressen die door uw ASE worden gebruikt, gewijzigd. De normale oorzaak van een ASE om te worden onderbroken is als u de toegang tot inkomende beheer blokkeert of toegang tot een ASE-afhankelijkheid blokkeert. 

![IP-adressen][3]

### <a name="app-assigned-ip-addresses"></a>Door apps toegewezen IP-adressen ###

Met een externe ASE kunt u IP-adressen toewijzen aan afzonderlijke apps. U kunt dit niet doen met een ILB-ASE. Zie voor meer informatie over het configureren van uw app voor een eigen IP-adres [een aangepaste DNS-naam beveiligen met een TLS/SSL-binding in azure app service](../configure-ssl-bindings.md).

Wanneer een app een eigen IP-gebaseerd SSL-adres heeft, reserveert de ASE twee poorten om aan dat IP-adres toe te wijzen. Een poort is voor HTTP-verkeer en de andere poort is voor HTTPS. Deze poorten worden weer gegeven in de ASE-gebruikers interface in de sectie IP-adressen. Verkeer moet de poorten van het VIP kunnen bereiken of de apps zijn niet toegankelijk. Deze vereiste is belang rijk om te onthouden wanneer u netwerk beveiligings groepen (Nsg's) configureert.

## <a name="network-security-groups"></a>Netwerkbeveiligingsgroepen ##

[Netwerk beveiligings groepen][NSGs] bieden de mogelijkheid om netwerk toegang binnen een VNet te beheren. Wanneer u de portal gebruikt, is er een impliciete regel voor weigeren met de laagste prioriteit om alles te weigeren. Wat u maakt, zijn uw regels voor toestaan.

In een ASE hebt u geen toegang tot de virtuele machines die worden gebruikt voor het hosten van de ASE zelf. Ze bevinden zich in een door micro soft beheerd abonnement. Als u de toegang tot de apps op de ASE wilt beperken, stelt u Nsg's in op het ASE-subnet. Daarom moet u zorgvuldig aandacht schenken aan de ASE-afhankelijkheden. Als u afhankelijkheden blokkeert, werkt de ASE niet meer.

Nsg's kan worden geconfigureerd via de Azure Portal of via Power shell. In deze informatie ziet u de Azure Portal. U kunt Nsg's in de portal maken en beheren als een resource op het hoogste niveau onder **netwerken**.

De vereiste vermeldingen in een NSG om een ASE te kunnen gebruiken, zijn verkeer toestaan:

**Inkomend verkeer**
* TCP van het IP-service label AppServiceManagement op poort 454.455
* TCP van de load balancer op poort 16001
* vanuit het ASE-subnet naar het ASE-subnet op alle poorten

**Uitgaand**
* UDP naar alle IP-adressen op poort 53
* UDP naar alle IP-adressen op poort 123
* TCP naar alle IP-adressen op poort 80, 443
* TCP naar het IP-service label `Sql` op poort 1433
* TCP naar alle IP-adressen op poort 12000
* naar het ASE-subnet op alle poorten

Deze poorten bevatten niet de poorten die uw apps nodig hebben voor een succes volle toepassing. Als voor beeld moet uw app mogelijk een MySQL-server aanroepen op poort 3306. Network Time Protocol (NTP) op poort 123 is het tijd synchronisatie protocol dat wordt gebruikt door het besturings systeem. De NTP-eind punten zijn niet specifiek voor App Services, kunnen afwijken van het besturings systeem en bevinden zich niet in een goed gedefinieerde lijst met adressen. Om problemen met tijd synchronisatie te voor komen, moet u UDP-verkeer toestaan voor alle adressen op poort 123. De uitgaande TCP naar poort 12000 verkeer is voor systeem ondersteuning en-analyse. De eind punten zijn dynamisch en bevinden zich niet in een duidelijk gedefinieerde set adressen.

De normale poorten voor app-toegang zijn:

| Gebruik | Poorten |
|----------|-------------|
|  HTTP/HTTPS  | 80, 443 |
|  FTP-FTPS    | 21, 990, 10001-10020 |
|  Visual Studio externe fout opsporing  |  4020, 4022, 4024 |
|  Web Deploy-Service | 8172 |

Wanneer rekening wordt gehouden met de inkomende en uitgaande vereisten, moet de Nsg's er ongeveer uitzien als de Nsg's die in dit voor beeld wordt weer gegeven. 

![Inkomende beveiligingsregels][4]

Met een standaard regel kunnen de IP-adressen in het VNet worden gecommuniceerd met het ASE-subnet. Een andere standaard regel maakt het load balancer, ook wel bekend als de open bare VIP, om te communiceren met de ASE. Als u de standaard regels wilt zien, selecteert u **standaard regels** naast het pictogram **toevoegen** . Als u voor de standaard regels een andere regel voor het weigeren van alle gegevens plaatst, voor komt u verkeer tussen de VIP en de ASE. Als u verkeer wilt voor komen dat zich binnen het VNet bevindt, voegt u uw eigen regel toe om binnenkomende verbindingen toe te staan. Gebruik een bron die gelijk is aan AzureLoadBalancer met een doel van **een en een poort bereik van** **\*** . Omdat de NSG-regel wordt toegepast op het ASE-subnet, hoeft u niet specifiek te zijn in het doel.

Als u een IP-adres aan uw app hebt toegewezen, moet u ervoor zorgen dat de poorten geopend blijven. Selecteer **app service Environment**  >  **IP-adressen** om de poorten weer te geven.  

Alle items die worden weer gegeven in de volgende regels voor uitgaande verbindingen, zijn vereist, met uitzonde ring van het laatste item. Ze bieden netwerk toegang tot de ASE-afhankelijkheden die eerder in dit artikel werden vermeld. Als u ze blokkeert, werkt uw ASE niet meer. Met het laatste item in de lijst kan uw ASE communiceren met andere resources in uw VNet.

![Uitgaande beveiligingsregels][5]

Nadat uw Nsg's zijn gedefinieerd, wijst u deze toe aan het subnet waar uw ASE zich bevindt. Als u het ASE VNet of subnet niet meer weet, kunt u het zien op de pagina ASE Portal. Als u de NSG aan uw subnet wilt toewijzen, gaat u naar de gebruikers interface van het subnet en selecteert u de NSG.

## <a name="routes"></a>Routes ##

Geforceerde tunneling is het instellen van routes in uw VNet, zodat het uitgaande verkeer niet rechtstreeks naar Internet gaat, maar ook op een ExpressRoute-gateway of een virtueel apparaat.  Als u uw ASE op een zodanige manier moet configureren, leest u het document over het [configureren van uw app service Environment met geforceerde tunneling][forcedtunnel].  In dit document ziet u de opties die beschikbaar zijn voor het werken met ExpressRoute en geforceerde tunneling.

Wanneer u in de portal een ASE maakt, wordt er ook een set route tabellen gemaakt op het subnet dat is gemaakt met de ASE.  Deze routes zeggen gewoon het verzenden van uitgaand verkeer rechtstreeks naar het internet.  
Als u dezelfde routes hand matig wilt maken, voert u de volgende stappen uit:

1. Ga naar Azure Portal. Selecteer **netwerk**  >  **route tabellen**.

2. Maak een nieuwe route tabel in dezelfde regio als uw VNet.

3. Selecteer in de gebruikers interface van de route tabel de optie **routes**  >  **toevoegen**.

4. Stel het **type van de volgende hop** in op **Internet** en het **adres voorvoegsel** op **0.0.0.0/0**. Selecteer **Opslaan**.

    Vervolgens ziet u iets zoals het volgende:

    ![Functionele routes][6]

5. Nadat u de nieuwe route tabel hebt gemaakt, gaat u naar het subnet dat uw ASE bevat. Selecteer uw route tabel in de lijst in de portal. Nadat u de wijziging hebt opgeslagen, ziet u de Nsg's en routes die zijn vermeld bij uw subnet.

    ![Nsg's en routes][7]

## <a name="service-endpoints"></a>Service-eindpunten ##

Met service-eindpunten kunt u de toegang tot multitenant-services beperken tot een reeks virtuele Azure-netwerken en subnetten. In de documentatie [Virtual Network Service Endpoints][serviceendpoints] (Service-eindpunten voor virtuele netwerken) vindt u meer informatie over service-eindpunten. 

Wanneer u service-eindpunten voor een bron inschakelt, worden er routes gemaakt die een hogere prioriteit hebben dan alle andere routes. Als u service-eind punten gebruikt voor een Azure-service, met een geforceerde getunnelde ASE, wordt het verkeer naar die services niet geforceerd getunneld. 

Als Service-eindpunten in een subnet met een Azure SQL-exemplaar is ingeschakeld, moet Service-eindpunten zijn ingeschakeld op alle Azure SQL-exemplaren waarmee vanuit dat subnet een verbinding wordt gemaakt. Als u vanuit hetzelfde subnet toegang wilt hebben tot meerdere Azure SQL-exemplaren, is het niet mogelijk om Service-eindpunten wel op het ene Azure SQL-exemplaar in te schakelen en niet op een ander. Geen enkele andere Azure-service gedraagt zich als Azure SQL met betrekking tot service-eind punten. Wanneer u Service-eindpunten met Azure Storage inschakelt, kunt u de toegang tot die resource vanuit uw subnet vergrendelen en toegang behouden tot andere Azure Storage-accounts, zelfs als Service-eindpunten op die accounts niet is ingeschakeld.  

![Service-eindpunten][8]

<!--Image references-->
[1]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow.png
[2]: ./media/network_considerations_with_an_app_service_environment/networkase-overflow2.png
[3]: ./media/network_considerations_with_an_app_service_environment/networkase-ipaddresses.png
[4]: ./media/network_considerations_with_an_app_service_environment/networkase-inboundnsg.png
[5]: ./media/network_considerations_with_an_app_service_environment/networkase-outboundnsg.png
[6]: ./media/network_considerations_with_an_app_service_environment/networkase-udr.png
[7]: ./media/network_considerations_with_an_app_service_environment/networkase-subnet.png
[8]: ./media/network_considerations_with_an_app_service_environment/serviceendpoint.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[ConfigureASEv1]: app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: app-service-app-service-environment-intro.md
[mobileapps]: /previous-versions/azure/app-service-mobile/app-service-mobile-value-prop
[Functions]: ../../azure-functions/index.yml
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ss-cert.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[ASEManagement]: ./management-addresses.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[forcedtunnel]: ./forced-tunnel-support.md
[serviceendpoints]: ../../virtual-network/virtual-network-service-endpoints-overview.md