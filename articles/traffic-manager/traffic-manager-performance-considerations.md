---
title: Prestatie overwegingen voor Azure Traffic Manager | Microsoft Docs
description: Inzicht krijgen in de prestaties van Traffic Manager en hoe u de prestaties van uw website kunt testen wanneer u Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: duau
ms.openlocfilehash: 5a0de772598877029649f8a985c79e28f5264535
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98186172"
---
# <a name="performance-considerations-for-traffic-manager"></a>Prestatieoverwegingen voor Traffic Manager

Op deze pagina worden de prestatie overwegingen voor het gebruik van Traffic Manager uitgelegd. Denkt u zich het volgende scenario eens in:

U hebt exemplaren van uw website in de regio's Westus en EastAsia. Een van de exemplaren mislukt de status controle voor de Traffic Manager-test. Toepassings verkeer wordt omgeleid naar de in orde zijnde regio. Deze failover wordt verwacht, maar de prestaties kunnen een probleem zijn op basis van de latentie van het verkeer dat nu naar een regio voor een afstand wordt verzonden.

## <a name="performance-considerations-for-traffic-manager"></a>Prestatieoverwegingen voor Traffic Manager

De enige invloed op de prestaties die Traffic Manager kan hebben op uw website is de eerste DNS-zoek opdracht. Een DNS-aanvraag voor de naam van uw Traffic Manager profiel wordt verwerkt door de micro soft DNS-basis server die als host fungeert voor de zone trafficmanager.net. Traffic Manager vult en regel matig updates van de DNS-basis servers van micro soft op basis van het Traffic Manager beleid en de test resultaten. Dus zelfs tijdens de eerste DNS-zoek opdracht worden er geen DNS-query's verzonden naar Traffic Manager.

Traffic Manager bestaat uit verschillende onderdelen: DNS-naam servers, een API-service, de opslaglaag en een eindpunt bewakings service. Als een Traffic Manager-service onderdeel mislukt, heeft dit geen invloed op de DNS-naam die is gekoppeld aan uw Traffic Manager profiel. De records in de micro soft DNS-servers blijven ongewijzigd. Eindpunt controle en DNS-updates worden echter niet uitgevoerd. Daarom kan Traffic Manager DNS niet bijwerken om naar uw failover-site te verwijzen wanneer de primaire site uitvalt.

De DNS-naam omzetting is snel en de resultaten worden opgeslagen in de cache. De snelheid van de eerste DNS-zoek opdracht is afhankelijk van de DNS-servers die de client gebruikt voor naam omzetting. Normaal gesp roken kan een client een DNS-zoek opdracht binnen ~ 50 MS volt ooien. De resultaten van de zoek actie worden in de cache opgeslagen voor de duur van de DNS-TTL (time-to-Live). De standaard-TTL voor Traffic Manager is 300 seconden.

Verkeer loopt niet door Traffic Manager. Zodra de DNS-zoek opdracht is voltooid, heeft de client een IP-adres voor een exemplaar van uw website. De client maakt rechtstreeks verbinding met dit adres en geeft geen Traffic Manager door. Het Traffic Manager-beleid dat u kiest, heeft geen invloed op de DNS-prestaties. Een prestatie routering methode kan echter een negatieve invloed hebben op de ervaring van de toepassing. Als uw beleid bijvoorbeeld verkeer omleidt van Noord-Amerika naar een exemplaar dat in Azië wordt gehost, kan de netwerk latentie voor deze sessies een prestatie probleem zijn.

## <a name="measuring-traffic-manager-performance"></a>Prestaties meten Traffic Manager

Er zijn verschillende websites die u kunt gebruiken om inzicht te krijgen in de prestaties en het gedrag van een Traffic Manager profiel. Veel van deze sites zijn gratis, maar kunnen beperkingen hebben. Sommige sites bieden uitgebreide bewaking en rapportage voor een vergoeding.

De hulpprogram ma's op deze sites meten DNS-latentie en geven de opgeloste IP-adressen weer voor client locaties over de hele wereld. De meeste van deze hulpprogram ma's slaan de DNS-resultaten niet op in de cache. Daarom tonen de hulpprogram ma's de volledige DNS-zoek opdracht telkens wanneer een test wordt uitgevoerd. Wanneer u van uw eigen client test, wordt alleen de volledige DNS-Zoek prestaties uitgevoerd tijdens de TTL-duur.

## <a name="sample-tools-to-measure-dns-performance"></a>Voorbeeldhulpprogramma’s voor het meten van de DNS-prestaties

* [SolveDNS](https://www.solvedns.com/dns-comparison/)

    SolveDNS biedt veel prestatie hulpprogramma's. Met het hulp programma voor vergelijken van DNS kunt u zien hoe lang het duurt om uw DNS-naam op te lossen en hoe die worden vergeleken met andere DNS-service providers.

* [WebSitePulse](https://www.websitepulse.com/help/tools.php)

    Een van de eenvoudigste hulpprogram ma's is WebSitePulse. Voer de URL in om de DNS-omzettings tijd, de eerste byte, de laatste byte en andere prestatie statistieken weer te geven. U kunt kiezen uit drie verschillende test locaties. In dit voor beeld ziet u dat de eerste uitvoering laat zien dat de DNS-zoek opdracht 0,204 sec.

    ![Scherm opname waarin het ' WebSitePulse-hulp programma met het Zoek resultaat ' DNS ' wordt weer gegeven.](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse.png)

    Omdat de resultaten in de cache worden opgeslagen, wordt de tweede test voor hetzelfde Traffic Manager-eind punt dat de DNS-zoek opdracht 0,002 sec.

    ![pulse2](./media/traffic-manager-performance-considerations/traffic-manager-web-site-pulse2.png)

* [Synthetische monitor voor CA-apps](https://asm.ca.com/en/checkit.php)

    Deze site, voorheen bekend als de Watch-muis controle, geeft u de DNS-omzettings tijd van meerdere geografische regio's tegelijk weer. Voer de URL in om de DNS-omzettings tijd, verbindings tijd en snelheid van verschillende geografische locaties weer te geven. Gebruik deze test om te zien welke gehoste service wordt geretourneerd voor verschillende locaties over de hele wereld.

    ![pulse1](./media/traffic-manager-performance-considerations/traffic-manager-web-site-watchmouse.png)

* [Pingdom](https://tools.pingdom.com/)

    Dit hulp programma biedt prestatie statistieken voor elk element van een webpagina. Op het tabblad pagina analyse wordt het percentage tijd weer gegeven dat is besteed aan DNS-Zoek opdrachten.

* [Wat is mijn DNS?](https://www.whatsmydns.net/)

    Deze site voert een DNS-zoek opdracht uit van 20 verschillende locaties en geeft de resultaten weer op een kaart.

* [Webinterface graaf](https://www.digwebinterface.com)

    Deze site bevat meer gedetailleerde DNS-informatie, inclusief CNAME en records. Controleer of u de ' inkleuren uitvoer ' en ' Statistieken ' selecteert onder Opties en selecteer ' alle ' onder naam servers.

## <a name="next-steps"></a>Volgende stappen

[Over Traffic Manager routerings methoden voor verkeer](traffic-manager-routing-methods.md)

[Uw instellingen voor Traffic Manager testen](traffic-manager-testing-settings.md)

[Bewerkingen op Traffic Manager (REST API-referentiemateriaal)](/previous-versions/azure/reference/hh758255(v=azure.100))

[Azure Traffic Manager-cmdlets](/powershell/module/az.trafficmanager)