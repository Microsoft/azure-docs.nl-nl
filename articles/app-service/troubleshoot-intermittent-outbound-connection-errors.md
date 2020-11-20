---
title: Problemen met terugkerende uitgaande verbindings fouten in Azure App Service oplossen
description: Problemen oplossen met onregelmatige verbindings fouten en gerelateerde prestatie problemen in Azure App Service
author: v-miegge
manager: barbkess
ms.topic: troubleshooting
ms.date: 11/19/2020
ms.author: ramakoni
ms.custom: security-recommendations,fasttrack-edit
ms.openlocfilehash: 989f47c0ff60865a8e8be15e089cdcf96ab2550c
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94968295"
---
# <a name="troubleshooting-intermittent-outbound-connection-errors-in-azure-app-service"></a>Problemen met terugkerende uitgaande verbindings fouten in Azure App Service oplossen

Dit artikel helpt u bij het oplossen van onregelmatige verbindings fouten en gerelateerde prestatie problemen in [Azure app service](./overview.md). In dit onderwerp vindt u meer informatie over het oplossen van problemen met methoden voor het afronden van de bron adres netwerk omzetting (SNAT)-poorten. Als u op elk gewenst moment meer hulp nodig hebt, neemt u contact op met de Azure-experts op [MSDN Azure en de stack overflow forums](https://azure.microsoft.com/support/forums/). U kunt ook een ondersteunings incident voor Azure opslaan. Ga naar de [ondersteunings site van Azure](https://azure.microsoft.com/support/options/) en selecteer **ondersteuning verkrijgen**.

## <a name="symptoms"></a>Symptomen

Toepassingen en functies die worden gehost op Azure-app service, kunnen een of meer van de volgende symptomen ondervinden:

* Trage reactie tijden voor alle of een aantal exemplaren in een service abonnement.
* Onregelmatige 5xx of **onjuiste gateway** fouten
* Time-outfout berichten
* Kan geen verbinding maken met externe eind punten (zoals SQLDB, Service Fabric, andere app-Services, enzovoort)

## <a name="cause"></a>Oorzaak

De grote oorzaak van problemen met een onregelmatige verbinding is een limiet bereikt tijdens het maken van nieuwe uitgaande verbindingen. U kunt onder andere het volgende beperken:

* TCP-verbindingen: er is een limiet voor het aantal uitgaande verbindingen dat kan worden gemaakt. De limiet voor uitgaande verbindingen is gekoppeld aan de grootte van de gebruikte werk nemer.
* SNAT-poorten: [uitgaande verbindingen in azure](../load-balancer/load-balancer-outbound-connections.md) worden de SNAT-poort beperkingen en de invloed hiervan op uitgaande verbindingen beschreven. Azure gebruikt bron Network Address Translation (SNAT) en load balancers (niet beschikbaar voor klanten) om te communiceren met open bare IP-adressen. Elk exemplaar van Azure-app service krijgt in eerste instantie een vooraf toegewezen aantal SNAT-poorten van **128** . De limiet voor de SNAT-poort is van invloed op het openen van verbindingen met dezelfde combi natie van adres en poort. Als uw app verbinding maakt met een combi natie van combi Naties van adressen en poorten, gebruikt u geen SNAT-poorten. De SNAT-poorten worden gebruikt wanneer u herhaaldelijk aanroepen naar dezelfde combi natie van adres en poort hebt. Zodra een poort is vrijgegeven, kan de poort zo nodig opnieuw worden gebruikt. Het Azure-netwerk load balancer de SNAT-poort alleen vrijmaken van gesloten verbindingen na een wacht tijd van 4 minuten.

Wanneer toepassingen of functies snel een nieuwe verbinding openen, kunnen ze snel hun vooraf toegewezen quotum van de 128 poorten. Ze worden vervolgens geblokkeerd totdat een nieuwe SNAT-poort beschikbaar komt, hetzij via dynamische toewijzing van extra SNAT-poorten, hetzij via hergebruik van een vrijgemaakte SNAT-poort. Als voor uw app geen SNAT-poorten worden uitgevoerd, zijn er onregelmatige problemen met de verbinding met uitgaand verkeer. 

## <a name="avoiding-the-problem"></a>Het probleem voor komen

Er zijn enkele oplossingen waarmee u limieten voor de SNAT-poort kunt voor komen. Deze omvatten:

* verbindings groepen: door uw verbindingen te groeperen, voor komt u dat er nieuwe netwerk verbindingen voor aanroepen naar hetzelfde adres en dezelfde poort worden geopend.
* Service-eind punten: u hebt geen limiet voor de SNAT-poort voor de services die zijn beveiligd met Service-eind punten.
* privé-eind punten: u hebt geen SNAT-poort beperking voor services die zijn beveiligd met persoonlijke eind punten.
* NAT-gateway: met een NAT-gateway hebt u 64 KB uitgaande SNAT-poorten die kunnen worden gebruikt door de resources die verkeer verzenden.

Als het SNAT-poort probleem wordt voor komen, wordt het maken van nieuwe verbindingen herhaaldelijk op dezelfde host en poort voor komen. Verbindings groepen zijn een van de duidelijkere manieren om dit probleem op te lossen.

Als uw bestemming een Azure-service is die service-eind punten ondersteunt, kunt u het gebruik van [regionale VNet-integratie](./web-sites-integrate-with-vnet.md) en service-eind punten of privé-eind punten vermijden. Wanneer u de regionale VNet-integratie gebruikt en service-eind punten in het subnet met integratie plaatst, hebben uw door apps uitgaand verkeer naar die services geen uitgaande SNAT-poort beperkingen. Als u gebruikmaakt van regionale VNet-integratie en privé-eind punten, hebt u ook geen uitgaande SNAT-poort problemen voor die bestemming. 

Als uw bestemming een extern eind punt buiten Azure is, geeft u met behulp van een NAT-gateway een uitgaand SNAT-poort. Het biedt ook een speciaal uitgaand adres dat u niet met iedereen deelt. 

Als dat mogelijk is, kunt u de code verbeteren om verbindings groepen te gebruiken en de volledige situatie te voor komen. Het is niet altijd mogelijk code snel genoeg te wijzigen om deze situatie te verhelpen. Voor de gevallen waarin u uw code niet op tijd kunt wijzigen, kunt u profiteren van de andere oplossingen. De beste oplossing voor het probleem is het combi neren van alle oplossingen, zoals u het beste kunt doen. Probeer service-eind punten en persoonlijke eind punten te gebruiken voor Azure-Services en de NAT-gateway voor de rest. 

Algemene strategieën voor het beperken van de SNAT-poort uitputting worden besproken in de [sectie probleem oplossing](../load-balancer/load-balancer-outbound-connections.md) van de **uitgaande verbindingen van de Azure** -documentatie. Van deze strategieën zijn de volgende van toepassing op apps en functies die worden gehost op Azure-app service.

### <a name="modify-the-application-to-use-connection-pooling"></a>De toepassing wijzigen voor het gebruik van groepsgewijze verbindingen

* Voor het groeperen van HTTP-verbindingen bekijkt u [HTTP-verbindingen pool met HttpClientFactory](/aspnet/core/performance/performance-best-practices#pool-http-connections-with-httpclientfactory).
* Raadpleeg [SQL Server Connection Pooling (ADO.net)](/dotnet/framework/data/adonet/sql-server-connection-pooling)voor informatie over SQL Server groepsgewijze verbindingen.
* Raadpleeg [DbContext pooling](/ef/core/what-is-new/ef-core-2.0#dbcontext-pooling)voor meer over het implementeren van groepen met Entity Framework-toepassingen.

Hier volgt een verzameling koppelingen voor het implementeren van verbindings groepen door verschillende oplossings stacks.

#### <a name="node"></a>Knooppunt

Standaard worden verbindingen voor NodeJS niet actief bewaard. Hieronder vindt u de populaire data bases en pakketten voor groepsgewijze verbindingen die voor beelden bevatten voor het implementeren ervan.

* [MySQL](https://github.com/mysqljs/mysql#pooling-connections)
* [MongoDB](https://blog.mlab.com/2017/05/mongodb-connection-pooling-for-express-applications/)
* [PostgreSQL](https://node-postgres.com/features/pooling)
* [SQL Server](https://github.com/tediousjs/node-mssql#connection-pools)

HTTP-keepalive

* [agentkeepalive](https://www.npmjs.com/package/agentkeepalive)
* [ Documentatie voorNode.js v 13.9.0](https://nodejs.org/api/http.html)

#### <a name="java"></a>Java

Hieronder vindt u de populaire bibliotheken die worden gebruikt voor JDBC-verbindings Pools, met voor beelden voor het implementeren ervan: JDBC-verbindings groepen.

* [Tomcat 8](https://tomcat.apache.org/tomcat-8.0-doc/jdbc-pool.html)
* [C3p0](https://github.com/swaldman/c3p0)
* [HikariCP](https://github.com/brettwooldridge/HikariCP)
* [Apache DBCP](https://commons.apache.org/proper/commons-dbcp/)

Pooling van HTTP-verbindingen

* [Apache-verbindings beheer](https://hc.apache.org/httpcomponents-client-ga/tutorial/html/connmgmt.html)
* [Klasse PoolingHttpClientConnectionManager](http://hc.apache.org/httpcomponents-client-ga/httpclient/apidocs/org/apache/http/impl/conn/PoolingHttpClientConnectionManager.html)

#### <a name="php"></a>PHP

Hoewel PHP geen ondersteuning biedt voor groepsgewijze verbindingen, kunt u permanente database verbindingen met de back-endserver proberen.
 
* MySQL-server

   * [Mysqli-verbindingen](https://www.php.net/manual/mysqli.quickstart.connections.php) voor nieuwere versies
   * [mysql_pconnect](https://www.php.net/manual/function.mysql-pconnect.php) voor oudere versies van php

* Andere gegevens bronnen

   * [PHP-verbindings beheer](https://www.php.net/manual/en/pdo.connections.php)

### <a name="modify-the-application-to-reuse-connections"></a>Wijzig de toepassing om verbindingen opnieuw te gebruiken

*  Zie [verbindingen beheren in azure functions](../azure-functions/manage-connections.md)voor aanvullende pointers en voor beelden over het beheren van verbindingen in azure functions.

### <a name="modify-the-application-to-use-less-aggressive-retry-logic"></a>De toepassing wijzigen voor het gebruik van minder agressieve nieuwe logica

* Bekijk het [nieuwe patroon](/azure/architecture/patterns/retry)voor meer informatie en voor beelden.

### <a name="use-keepalives-to-reset-the-outbound-idle-timeout"></a>Keepalives gebruiken om de uitgaande time-out voor inactiviteit opnieuw in te stellen

* Voor het implementeren van keepalives voor Node.js-apps, [moet u controleren of de toepassing van mijn knoop punt overmatige uitgaande oproepen](./app-service-web-nodejs-best-practices-and-troubleshoot-guide.md#my-node-application-is-making-excessive-outbound-calls)maakt.

### <a name="additional-guidance-specific-to-app-service"></a>Aanvullende richt lijnen die specifiek zijn voor App Service:

* Een [belasting test](/azure/devops/test/load-test/app-service-web-app-performance-test) moet de werkelijke gegevens in een constante voedings snelheid simuleren. Door apps en functies te testen onder echte wereld wijde stress kunnen de SNAT-poort afvoer problemen voor tijdig worden opgespoord en opgelost.
* Zorg ervoor dat de back-end-services snel reacties kunnen retour neren. Raadpleeg [Azure SQL database prestatie problemen oplossen met intelligent Insights](../azure-sql/database/intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow)voor meer informatie over het oplossen van prestatie problemen met Azure SQL database.
* Uitschalen naar meer exemplaren van het App Service plan. Zie [een app schalen in azure app service](./manage-scale-up.md)voor meer informatie over schalen. Aan elk worker-exemplaar in een app service-plan is een aantal SNAT-poorten toegewezen. Als u uw gebruik verspreid over meer instanties, kunt u het SNAT-poort gebruik per exemplaar verkrijgen onder de aanbevolen limiet van 100 uitgaande verbindingen, per uniek extern eind punt.
* Overweeg om over te stappen op [app service Environment (ASE)](./environment/using-an-ase.md), waarbij u één uitgaand IP-adres hebt toegewezen en de limieten voor verbindingen en SNAT-poorten veel hoger zijn. In een ASE is het aantal SNAT-poorten per instantie gebaseerd op de tabel voor de vooraf- [toewijzing van Azure Load Balancer](../load-balancer/load-balancer-outbound-connections.md#snatporttable) , bijvoorbeeld een ASE met 1-50 worker-werk exemplaren heeft 1024 vooraf toegewezen poorten per instantie, terwijl een ASE met 51-100 512 exemplaren van de vooraf toegewezen poorten per instantie.

Het voor komen van de uitgaande TCP-limieten is gemakkelijker te oplossen, omdat de limieten worden ingesteld op basis van de grootte van uw werk nemer. U kunt de limieten in [sandbox cross-VM numerieke limieten weer geven-TCP-verbindingen](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Limiet naam|Beschrijving|Klein (a1)|Middel groot (a2)|Groot (a3)|Geïsoleerde laag (ASE)|
|---|---|---|---|---|---|
|Verbindingen|Aantal verbindingen voor de hele virtuele machine|1920|3968|8064|16.000|

Als u uitgaande TCP-limieten wilt voor komen, kunt u de grootte van uw werk nemers verg Roten of horizon taal schalen.

## <a name="troubleshooting"></a>Problemen oplossen

Als u de twee typen uitgaande verbindings limieten kent, en wat uw app doet, moet u de problemen gemakkelijker oplossen. Als u weet dat uw app veel aanroepen naar hetzelfde opslag account maakt, verdacht u mogelijk een SNAT-limiet. Als uw app een groot aantal aanroepen naar eind punten via internet maakt, zou u vermoeden dat u de limiet van de virtuele machine bereikt.

Als u het gedrag van de toepassing niet kent om de oorzaak snel te bepalen, zijn er enkele hulp middelen en technieken beschikbaar in App Service om u te helpen bij die bepaling.

### <a name="find-snat-port-allocation-information"></a>Informatie over de toewijzing van SNAT-poorten zoeken

U kunt [app service diagnostische](./overview-diagnostics.md) gegevens gebruiken om informatie over de SNAT-poort toewijzing te vinden en de toewijzings metriek voor de SNAT-poorten van een app service site te bekijken. Voer de volgende stappen uit om informatie over de SNAT-poort toewijzing te vinden:

1. Om toegang te krijgen tot App Service diagnostische gegevens, gaat u naar uw App Service web-app of App Service Environment in de [Azure Portal](https://portal.azure.com/). Selecteer in het linkernavigatievenster **problemen vaststellen en oplossen**.
2. De categorie Beschik baarheid en prestaties selecteren
3. Selecteer de tegel SNAT-poort uitputting in de lijst met beschik bare tegels onder de categorie. De praktijk is het volgende te bedenken onder 128.
Als u dit nodig hebt, kunt u nog steeds een ondersteunings ticket openen. de ondersteunings technicus krijgt de metric van back-end voor u.

Omdat het gebruik van de SNAT-poort niet beschikbaar is als metriek, is het niet mogelijk om automatisch te schalen op basis van het gebruik van de SNAT-poort, of om automatische schalen te configureren op basis van de toewijzings metriek voor de SNAT-poorten.

### <a name="tcp-connections-and-snat-ports"></a>TCP-verbindingen en SNAT-poorten

TCP-verbindingen en SNAT-poorten zijn niet rechtstreeks gerelateerd. Een gebruiks detectie voor TCP-verbindingen is opgenomen in de Blade problemen vaststellen en oplossen van een App Service-site. Zoek naar de zin TCP-verbindingen om deze te vinden.

* De SNAT-poorten worden alleen gebruikt voor externe netwerk stromen, terwijl het totale aantal TCP-verbindingen lokale loop back-verbindingen bevat.
* Een SNAT-poort kan worden gedeeld door verschillende stromen, als de stromen verschillend zijn in een van de protocollen, het IP-adres of de poort. De metrische gegevens voor TCP-verbindingen tellen elke TCP-verbinding.
* De limiet voor TCP-verbindingen treedt op op het niveau van het worker-exemplaar. De uitgaande taak verdeling van het Azure-netwerk maakt geen gebruik van de TCP-verbindings gegevens voor de limiet voor SNAT-poorten.
* De limieten voor TCP-verbindingen worden beschreven in [sandbox cross-VM-numerieke limieten-TCP-verbindingen](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#cross-vm-numerical-limits)

|Limiet naam|Beschrijving|Klein (a1)|Middel groot (a2)|Groot (a3)|Geïsoleerde laag (ASE)|
|---|---|---|---|---|---|
|Verbindingen|Aantal verbindingen voor de hele virtuele machine|1920|3968|8064|16.000|

### <a name="webjobs-and-database-connections"></a>Webjobs en database verbindingen
 
Als de SNAT-poorten uitgeput zijn, waarbij webjobs geen verbinding kunnen maken met SQL Database, is er geen metriek om weer te geven hoeveel verbindingen worden geopend door elk afzonderlijk webtoepassingsproces. Als u de problematische Webtaak wilt vinden, kunt u verschillende webjobs naar een andere App Service plan verplaatsen om te zien of de situatie zich verbetert of als er een probleem in een van de plannen blijft. Herhaal dit proces totdat u de problematische Webtaak hebt gevonden.

### <a name="using-snat-ports-sooner"></a>Een SNAT-poort gebruiken

U kunt geen Azure-instellingen wijzigen om de gebruikte SNAT-poorten eerder vrij te geven, omdat alle SNAT-poorten worden vrijgegeven volgens de onderstaande voor waarden en het gedrag inherent is aan het ontwerp.
 
* Als een van de servers of de client FINACK verzendt, [wordt de SNAT-poort](../load-balancer/load-balancer-outbound-connections.md) na 240 seconden vrijgegeven.
* Als een RST wordt weer gegeven, wordt de SNAT-poort na 15 seconden vrijgegeven.
* Als er een time-out voor inactiviteit is bereikt, wordt de poort vrijgegeven.
 
## <a name="additional-information"></a>Aanvullende informatie

* [SNAT met App Service](https://4lowtherabbit.github.io/blogs/2019/10/SNAT/)
* [Problemen met prestatie problemen met langzame apps in Azure App Service oplossen](./troubleshoot-performance-degradation.md)
