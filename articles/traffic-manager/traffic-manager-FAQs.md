---
title: Azure Traffic Manager-Veelgestelde vragen
description: In dit artikel vindt u antwoorden op veelgestelde vragen over Traffic Manager
services: traffic-manager
documentationcenter: ''
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/03/2021
ms.author: duau
ms.openlocfilehash: 0d4f1ed6bab5775c44b2a745e1edc5fc07e0c06d
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215456"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Veelgestelde vragen over Traffic Manager

## <a name="traffic-manager-basics"></a>Basis beginselen van Traffic Manager

### <a name="what-ip-address-does-traffic-manager-use"></a>Welk IP-adres Traffic Manager gebruiken?

Zoals beschreven in de werking van [Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager werkt op DNS-niveau. Hiermee verzendt u DNS-antwoorden naar directe clients naar het juiste service-eind punt. Clients maken vervolgens rechtstreeks verbinding met het service-eind punt, niet via Traffic Manager.

Daarom biedt Traffic Manager geen eind punt of IP-adres waarmee clients verbinding kunnen maken. Als u een statisch IP-adres voor uw service wilt, dat moet worden geconfigureerd op de service, niet in Traffic Manager.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Welk type verkeer kan worden gerouteerd met Traffic Manager?
Zoals beschreven in de werking van [Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), kan een Traffic Manager-eind punt een Internet gerichte service zijn die binnen of buiten Azure wordt gehost. Daarom kan Traffic Manager verkeer dat afkomstig is van het open bare Internet, routeren naar een set eind punten die ook Internet Facing zijn. Als u eind punten hebt die zich in een particulier netwerk (bijvoorbeeld een interne versie van [Azure Load Balancer](../load-balancer/components.md#frontend-ip-configurations)) bevinden of als u wilt dat gebruikers DNS-aanvragen van dergelijke interne netwerken hebben gedaan, kunt u Traffic Manager niet gebruiken om dit verkeer te routeren.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Ondersteunt Traffic Manager ' Sticky ' sessies?

Zoals beschreven in de werking van [Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager werkt op DNS-niveau. Het gebruikt DNS-antwoorden om clients naar het juiste service-eind punt te sturen. Clients maken rechtstreeks verbinding met het service-eind punt, niet via Traffic Manager. Daarom wordt het HTTP-verkeer tussen de client en de server niet in Traffic Manager weer geven.

Daarnaast behoort het bron-IP-adres van de DNS-query die wordt ontvangen door Traffic Manager tot de recursieve DNS-service, niet van de client. Daarom heeft Traffic Manager geen manier om afzonderlijke clients te traceren en kunnen er geen ' Sticky ' sessies worden geïmplementeerd. Deze beperking is gebruikelijk voor alle op DNS gebaseerde Traffic Management-systemen en is niet specifiek voor Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Waarom zie ik een HTTP-fout bij het gebruik van Traffic Manager?

Zoals beschreven in de werking van [Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager werkt op DNS-niveau. Het gebruikt DNS-antwoorden om clients naar het juiste service-eind punt te sturen. Clients maken vervolgens rechtstreeks verbinding met het service-eind punt, niet via Traffic Manager. Traffic Manager ziet geen HTTP-verkeer tussen client en server. Daarom moet elke HTTP-fout die u ziet afkomstig zijn van uw toepassing. De client kan alleen verbinding maken met de toepassing als alle stappen voor de omzetting van de DNS zijn voltooid. Dit omvat alle interactie die Traffic Manager heeft op de verkeers stroom van de toepassing.

Verder onderzoek moet daarom gericht zijn op de toepassing.

De header van de HTTP-host die vanuit de browser van de client wordt verzonden, is de meest voorkomende bron van problemen. Zorg ervoor dat de toepassing is geconfigureerd om de juiste host-header te accepteren voor de domein naam die u gebruikt. Zie [een aangepaste domein naam configureren voor een web-app in azure app service met behulp van Traffic Manager](../app-service/configure-domain-traffic-manager.md)voor eind punten met behulp van de Azure app service.

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Wat is de invloed van de prestaties van het gebruik van Traffic Manager?

Zoals beschreven in de werking van [Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager werkt op DNS-niveau. Omdat clients rechtstreeks verbinding maken met uw service-eind punten, is er geen invloed op de prestaties bij het gebruik van Traffic Manager zodra de verbinding tot stand is gebracht.

Omdat Traffic Manager integreert met toepassingen op het DNS-niveau, moet er een extra DNS-zoek opdracht worden toegevoegd aan de DNS-omzettings keten. De impact van Traffic Manager op de DNS-omzettings tijd is mini maal. Traffic Manager gebruikt een wereld wijd netwerk met naam servers en gebruikmaakt van een [anycast](https://en.wikipedia.org/wiki/Anycast) -netwerk om ervoor te zorgen dat DNS-query's altijd worden doorgestuurd naar de dichtstbijzijnde beschik bare naam server. Daarnaast betekent caching van DNS-antwoorden dat de extra DNS-latentie die wordt gemaakt met behulp van Traffic Manager alleen van toepassing is op een fractie van sessies.

De prestatie methode routeert verkeer naar het dichtstbijzijnde beschik bare eind punt. Het resultaat is dat de algehele prestatie-impact die is gekoppeld aan deze methode, mini maal moet zijn. Elke toename van de DNS-latentie moet worden verschoven door de lagere netwerk latentie naar het eind punt.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Welke toepassings protocollen kan ik gebruiken met Traffic Manager?

Zoals beschreven in de werking van [Traffic Manager](../traffic-manager/traffic-manager-how-it-works.md), Traffic Manager werkt op DNS-niveau. Zodra de DNS-zoek opdracht is voltooid, maken clients rechtstreeks verbinding met het eind punt van de toepassing, niet via Traffic Manager. Daarom kan de verbinding elk toepassings protocol gebruiken. Als u TCP selecteert als bewakings protocol, kan de controle van het eind punt van de status van het Traffic Manager worden uitgevoerd zonder een toepassings protocol te gebruiken. Als u ervoor kiest om de status te verifiëren met behulp van een toepassings protocol, moet het eind punt kunnen reageren op HTTP-of HTTPS GET-aanvragen.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Kan ik Traffic Manager gebruiken met de domein naam ' Blot '?

Ja. Zie [Configure a alias record voor ondersteuning van Apex-domein namen met Traffic Manager](../dns/tutorial-alias-tm.md)voor meer informatie over het maken van een alias record voor uw domein naam Apex om te verwijzen naar een Azure Traffic Manager-profiel.

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Houdt Traffic Manager rekening met het subnet van de client wanneer DNS-query's worden verwerkt? 

Ja, naast het bron-IP-adres van de DNS-query dat wordt ontvangen (dit is meestal het IP-adres van de DNS-resolver), wordt bij het uitvoeren van zoek acties voor geografische, prestatie-en subnet-routerings methoden ook het client-subnetadres beschouwd als het is opgenomen in de query door de resolver om de aanvraag namens de eind gebruiker te maken.  
In het bijzonder [RFC 7871: client-subnet in DNS-query's](https://tools.ietf.org/html/rfc7871) die een [UITBREIDINGS mechanisme voor DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) bieden dat kan worden door gegeven aan het client subnet adres van resolvers die ondersteuning bieden voor dit protocol.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Wat is een DNS TTL en wat is van invloed op mijn gebruikers?

Wanneer een DNS-query wordt aangeland op Traffic Manager, wordt er een waarde ingesteld in het antwoord met de naam time-to-Live (TTL). Deze waarde, waarvan de eenheid zich in seconden bevindt, geeft aan dat DNS-resolvers downstream worden aangegeven hoe lang het antwoord in de cache moet worden opgeslagen. Hoewel DNS-resolvers niet garanderen dat dit resultaat in de cache wordt opgeslagen, kunnen ze reageren op eventuele volgende query's uit de cache in plaats van Traffic Manager DNS-servers. Dit heeft betrekking op de reacties als volgt:

- een hogere TTL vermindert het aantal query's dat wordt gelandd op de Traffic Manager DNS-servers, waardoor de kosten voor een klant kunnen worden verminderd omdat het aantal query's dat wordt geleverd, een Factureerbaar gebruik is.
- een hogere TTL kan de tijd verminderen die nodig is om een DNS-zoek opdracht uit te voeren.
- een hogere TTL betekent ook dat uw gegevens niet de meest recente status gegevens weer spie gelen die Traffic Manager zijn verkregen via de probing-agents.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Hoe hoog of laag kan ik de TTL instellen voor Traffic Manager reacties?

U kunt per profiel niveau de DNS TTL instellen op een waarde van Maxi maal 0 seconden en Maxi maal 2.147.483.647 seconden (het maximum bereik dat compatibel is met [RFC-1035](https://www.ietf.org/rfc/rfc1035.txt )). Een TTL van 0 betekent dat downstream DNS-resolvers geen query-antwoorden in de cache opslaan en dat alle query's naar verwachting de Traffic Manager DNS-servers voor omzetting kunnen bereiken.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Hoe kan ik inzicht krijgen in het volume van query's die worden verzonden naar mijn profiel? 

Een van de metrische gegevens die door Traffic Manager worden gegeven, is het aantal query's dat wordt beantwoord door een profiel. U kunt deze informatie ophalen op profiel niveau aggregatie of u kunt het verder splitsen om het volume van query's te zien waarin specifieke eind punten zijn geretourneerd. Daarnaast kunt u waarschuwingen instellen om u te waarschuwen als het query-antwoord volume de voor waarden overschrijdt die u hebt ingesteld. [Traffic Manager metrische gegevens en waarschuwingen](traffic-manager-metrics-alerts.md)voor meer informatie.

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Traffic Manager geografische routerings methode voor geografisch verkeer

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Wat zijn enkele gebruiks voorbeelden waarbij geografische route ring nuttig is?

Geografisch routerings type kan worden gebruikt in elk scenario waarbij een Azure-klant hun gebruikers moet onderscheiden op basis van geografische regio's. Als u bijvoorbeeld de geografische routerings methode voor verkeer gebruikt, kunt u gebruikers van specifieke regio's een andere gebruikers ervaring geven dan die van andere regio's. Een ander voor beeld is het naleven van lokale data soevereiniteit-mandaten die vereisen dat gebruikers uit een specifieke regio alleen worden geleverd door eind punten in die regio.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Hoe kan ik bepalen of ik de routerings methode voor prestaties of de geografische routerings methode moet gebruiken?

Het belangrijkste verschil tussen deze twee populaire routerings methoden is dat in de routerings methode voor prestaties uw primaire doel is om verkeer naar het eind punt te verzenden dat de laagste latentie kan bieden aan de aanroeper, terwijl geografische route ring het primaire doel is om een geo-Fence af te dwingen voor uw bellers, zodat u ze opzettelijk kunt door sturen naar een bepaald eind punt. De overlap ping treedt op omdat er een correlatie is tussen de geografische dichtheid en de lagere latentie, hoewel dit niet altijd waar is. Het kan zijn dat er een eind punt in een andere geografie is dat een betere latentie-ervaring kan bieden voor de aanroeper en dat in dat geval de route ring van de gebruiker naar dat eind punt wordt verzonden, maar geografisch door geografische route ring wordt verzonden naar het eind punt dat u hebt toegewezen voor hun geografische regio. Als u dit duidelijk wilt maken, overweeg dan het volgende voor beeld: met geografische route ring kunt u ongebruikelijke toewijzingen maken, zoals het verzenden van al het verkeer vanuit Azië naar eind punten in de VS en al het Amerikaanse verkeer naar eind punten in Azië. In dat geval zal geografische route ring precies worden uitgevoerd wat u hebt geconfigureerd voor de taak en is het optimaliseren van prestaties niet van belang. 
>[!NOTE]
>Er kunnen scenario's zijn waarin u mogelijk zowel prestaties als geografische routerings mogelijkheden nodig hebt, voor deze scenario's met geneste profielen kan een fantastische keuze zijn. U kunt bijvoorbeeld een bovenliggend profiel instellen met geografische route ring waarbij u alle verkeer verzendt van Noord-Amerika naar een genest profiel met eind punten in de Verenigde Staten en prestaties routeren gebruiken om dat verkeer binnen die set naar het beste eind punt te verzenden. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Wat zijn de regio's die door Traffic Manager worden ondersteund voor geografische route ring?

De land-of regio hiërarchie die door Traffic Manager wordt gebruikt, vindt u [hier](traffic-manager-geographic-regions.md). Hoewel deze pagina up-to-date wordt gehouden met alle wijzigingen, kunt u dezelfde gegevens ook programmatisch ophalen met behulp van de [Azure Traffic Manager rest API](/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Hoe bepaalt Traffic Manager of een gebruiker een query uitvoert?

Traffic Manager kijkt naar het bron-IP-adres van de query (dit is waarschijnlijk een lokale DNS-resolver waarmee de query namens de gebruiker wordt uitgevoerd) en er wordt een intern IP-adres toegewezen aan de regio voor het bepalen van de locatie. Deze kaart wordt voortdurend bijgewerkt om rekening te houden met wijzigingen op internet. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Is het gegarandeerd dat Traffic Manager de exacte geografische locatie van de gebruiker in elk geval correct kunt bepalen?

Nee, Traffic Manager kan niet garanderen dat de geografische regio die we afleiden van het bron-IP-adres van een DNS-query altijd overeenkomt met de locatie van de gebruiker om de volgende redenen:

- Eerst worden, zoals beschreven in de vorige veelgestelde vragen, het bron-IP-adres dat wordt weer geven, van een DNS-resolver die de zoek opdracht namens de gebruiker uitvoert. Hoewel de geografische locatie van de DNS-resolver een goede proxy is voor de geografische locatie van de gebruiker, kan deze ook verschillen, afhankelijk van de footprint van de DNS resolver-service en de specifieke DNS resolver-service die een klant heeft gekozen. Zo kan een klant in Maleisië opgeven dat de instellingen van het apparaat gebruikmaken van een DNS resolver-service waarvan de DNS-server in Singapore kan worden opgenomen om de query oplossingen voor die gebruiker/dit apparaat te verwerken. In dat geval kan Traffic Manager alleen het IP-adres van de resolver zien die overeenkomt met de vestiging Singapore. Zie ook de veelgestelde vragen over ondersteuning voor client-subnetten op deze pagina.

- Ten tweede gebruikt Traffic Manager een interne kaart om het IP-adres naar de omzetting van geografische regio's uit te voeren. Hoewel deze kaart voortdurend wordt gevalideerd en bijgewerkt om de nauw keurigheid en het account voor de veranderende aard van het Internet te verg Roten, is er nog steeds de mogelijkheid dat de gegevens niet exact overeenkomen met de geografische locatie van alle IP-adressen.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Moet een eind punt zich fysiek bevinden in dezelfde regio als de versie die is geconfigureerd met voor geografische route ring?

Nee, de locatie van het eind punt legt geen beperkingen op waaraan regio's kunnen worden toegewezen. Bijvoorbeeld: een eind punt in US-Central Azure-regio kan alle gebruikers van India naar dit werk item sturen.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Kan ik geografische regio's toewijzen aan eind punten in een profiel dat niet is geconfigureerd voor geografische route ring?

Ja, als de routerings methode van een profiel niet geografisch is, kunt u met de [Azure Traffic Manager rest API](/rest/api/trafficmanager/) geografische regio's toewijzen aan eind punten in dat profiel. In het geval van niet-geografische routerings type profielen wordt deze configuratie genegeerd. Als u een dergelijk profiel op een later tijdstip naar een geografisch routerings type wijzigt, kunt Traffic Manager deze toewijzingen gebruiken.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Waarom krijg ik een fout melding wanneer ik de routerings methode van een bestaand profiel probeer te wijzigen in geografisch?

Aan alle eind punten onder een profiel met geografische route ring moet ten minste één regio worden toegewezen. Als u een bestaand profiel wilt omzetten naar een geografisch routerings type, moet u eerst geografische regio's aan alle eind punten koppelen met behulp van de [Azure Traffic Manager rest API](/rest/api/trafficmanager/) voordat u het routerings type wijzigt in geografisch. Als u Portal gebruikt, verwijdert u eerst de eind punten, wijzigt u de routerings methode van het profiel in geografisch en voegt u vervolgens de eind punten samen met hun geografische regio toewijzing toe.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Waarom wordt het ten zeerste aanbevolen dat klanten geneste profielen maken in plaats van eind punten onder een profiel waarvoor geografische route ring is ingeschakeld?

Een regio kan slechts aan één eind punt binnen een profiel worden toegewezen als de geografische routerings methode wordt gebruikt. Als dat eind punt geen genest type is waaraan een onderliggend profiel is gekoppeld, en als dat eind punt een slechte status heeft, blijft Traffic Manager verkeer naar het verstuurt, omdat het alternatief voor het niet verzenden van het verkeer niet beter is. Traffic Manager failover naar een ander eind punt, zelfs wanneer de toegewezen regio een bovenliggend element is van de regio die is toegewezen aan het eind punt (bijvoorbeeld als een eind punt met regio Spanje niet in orde is, wordt er geen failover uitgevoerd naar een ander eind punt waaraan de regio-Europa is toegewezen). Dit wordt gedaan om ervoor te zorgen dat Traffic Manager eerbiedigt van de geografische grenzen die een klant heeft ingesteld in het profiel. Om het voor deel van het mislukken van een failover naar een ander eind punt te krijgen wanneer een eind punt niet in orde is, wordt aanbevolen dat geografische regio's worden toegewezen aan geneste profielen met meerdere eind punten in plaats van afzonderlijke eind punten. Op deze manier, als een eind punt in het geneste onderliggende profiel mislukt, kan verkeer een failover naar een ander eind punt binnen hetzelfde geneste onderliggende profiel worden uitgevoerd.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Zijn er beperkingen voor de API-versie die ondersteuning biedt voor dit routerings type?

Ja, alleen API-versie 2017-03-01 en hoger ondersteunt het geografische routerings type. Oudere API-versies kunnen niet worden gebruikt voor het maken van profielen van geografisch routerings type of het toewijzen van geografische regio's aan eind punten. Als een oudere API-versie wordt gebruikt om profielen op te halen uit een Azure-abonnement, wordt een profiel van geografisch routerings type niet geretourneerd. Als er een oudere API-versie wordt gebruikt, wordt bij elk profiel dat wordt geretourneerd met eind punten met een geografische regio toewijzing, de geografische regio toewijzing niet weer gegeven.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Routerings methode voor het subnet Traffic Traffic Manager

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Wat zijn enkele gebruiks voorbeelden waarbij subnet routering nuttig is?

Met subnet routering kunt u de ervaring onderscheiden die u levert voor specifieke sets van gebruikers die worden geïdentificeerd door de bron-IP van het IP-adres van de DNS-aanvragen. Er wordt een voor beeld weer gegeven van verschillende inhoud als gebruikers verbinding maken met een website vanuit uw zakelijke hoofd kantoor. Een andere manier is dat gebruikers van bepaalde Isp's alleen toegang hebben tot eind punten die alleen IPv4-verbindingen ondersteunen als deze Isp's onderfractie hebben als IPv6 wordt gebruikt.
Een andere reden voor het gebruik van de methode voor het routeren van subnetten is in combi natie met andere profielen in een geneste profielset. Als u bijvoorbeeld geografische routerings methode wilt gebruiken voor het geokleuren van uw gebruikers, maar voor een specifieke Internet provider die u een andere routerings methode wilt uitvoeren, kunt u een profiel met een routerings methode voor het subnet als het bovenliggende profiel hebben en die provider overschrijven voor gebruik van een specifiek onderliggend profiel en het standaard geografische profiel voor iedereen gebruiken.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Hoe kent Traffic Manager het IP-adres van de eind gebruiker?

Apparaten voor eind gebruikers gebruiken meestal een DNS-resolver om namens u de DNS-zoek opdracht uit te voeren. Het uitgaande IP-adres van deze resolvers is wat Traffic Manager ziet als het bron-IP-adres. Daarnaast zoekt de routerings methode van het subnet ook of er een ECS-informatie (EDNS0 Extended client subnet) is die is door gegeven met de aanvraag. Als er een ECS-informatie aanwezig is, is dit het adres dat wordt gebruikt om de route ring te bepalen. Als er geen ECS-informatie is, wordt het bron-IP-adres van de query gebruikt voor routerings doeleinden.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Hoe kan ik IP-adressen opgeven wanneer ik een subnet routering gebruik?

De IP-adressen die moeten worden gekoppeld aan een eind punt kunnen op twee manieren worden opgegeven. Eerst kunt u de decimale notatie met vier punten met een begin-en eind adres gebruiken om het bereik op te geven (bijvoorbeeld 1.2.3.4-5.6.7.8 of 3.4.5.6-3.4.5.6). Ten tweede kunt u de CIDR-notatie gebruiken om het bereik op te geven (bijvoorbeeld 1.2.3.0/24). U kunt meerdere bereiken opgeven en beide notatie typen gebruiken in een bereikset. Er zijn enkele beperkingen van toepassing.

-    U mag geen overlappende adresbereiken hebben omdat elk IP-adres moet worden toegewezen aan slechts één eind punt
-    Het begin adres mag niet langer zijn dan het eind adres
-    In het geval van de CIDR-notatie moet het IP-adres vóór het '/' het begin adres van het bereik zijn (bijvoorbeeld 1.2.3.0/24 is geldig, maar 1.2.3.4.4/24 is niet geldig)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Hoe kan ik een terugval-eind punt opgeven bij het gebruik van subnet routering?

Als u een eind punt waaraan geen subnetten zijn toegewezen, in een profiel met subnet routering, wordt een aanvraag die niet overeenkomt met andere eind punten, hier omgeleid. Het is raadzaam dat u een dergelijk terugval-eind punt in uw profiel hebt, omdat Traffic Manager een NXDOMAIN-reactie retourneert als er een aanvraag wordt ontvangen en deze niet is toegewezen aan eind punten of als deze is toegewezen aan een eind punt, maar dat eind punt niet in orde is.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>Wat gebeurt er als een eind punt wordt uitgeschakeld in een type profiel voor een subnet routering?

Als u een eind punt met de naam van het subnet route ring hebt, wordt de Traffic Manager gedraagt alsof het eind punt en de toegewezen subnetten niet bestaan. Als een query die overeenkomt met de IP-adres toewijzing wordt ontvangen en het eind punt is uitgeschakeld, retourneert Traffic Manager een terugval-eind punt (een zonder toewijzingen) of als een dergelijk eind punt niet aanwezig is, retourneert een NXDOMAIN-antwoord.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Traffic Manager methode voor het routeren van meerdere waarden

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Wat zijn enkele situaties waarbij de route ring met meerdere waarden nuttig is?

Multi waarde-route ring retourneert meerdere gezonde eind punten in één query-antwoord. Het belangrijkste voor deel hiervan is dat, als een eind punt niet in orde is, de client meer opties heeft om het opnieuw te proberen zonder een andere DNS-aanroep te maken (waardoor dezelfde waarde kan worden geretourneerd vanuit een upstream-cache). Dit is van toepassing op Beschik baarheid gevoelige toepassingen die de downtime tot een minimum willen beperken.
Een ander gebruik voor een routerings methode met meerdere waarden is als een eind punt ' Dual-Home ' is voor zowel IPv4-als IPv6-adressen en u de aanroeper beide opties wilt geven om uit te kiezen wanneer een verbinding met het eind punt wordt geïnitieerd.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Hoeveel eind punten worden er geretourneerd wanneer meerdere waarden worden geroutingeerd?

U kunt het maximum aantal eind punten opgeven dat moet worden geretourneerd en met meerdere waarden wordt niet meer dan dat aantal gezonde eind punten geretourneerd wanneer een query wordt ontvangen. De Maxi maal mogelijke waarde voor deze configuratie is 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Krijgt ik dezelfde set eind punten als de route ring met meerdere waarden wordt gebruikt?

We kunnen niet garanderen dat dezelfde set van eind punten wordt geretourneerd in elke query. Dit wordt ook beïnvloed door het feit dat een aantal van de eind punten mogelijk niet in orde is op het moment dat ze niet worden opgenomen in het antwoord

## <a name="real-user-measurements"></a>Real-user-metingen

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Wat zijn de voor delen van het gebruik van Real-user-metingen?

Wanneer u de methode voor de route ring van prestaties gebruikt, wordt in Traffic Manager de beste Azure-regio voor de eind gebruiker gekozen om verbinding te maken met door het bron-IP-adres en het subnet van de EDNS-client te controleren (indien dit is door gegeven) en te controleren op de netwerk latentie intelligentie die de service onderhoudt. Real-user-metingen breidt dit uit voor uw eind gebruikers door hun ervaring te laten bijdragen aan deze latentie tabel en ervoor te zorgen dat deze tabel op de juiste wijze de netwerken van eind gebruikers bedient van de locatie waar uw eind gebruikers verbinding maken met Azure. Dit leidt tot een grotere nauw keurigheid van de route ring van uw eind gebruiker.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Kan ik Real-user-metingen gebruiken met niet-Azure-regio's?

Real-user-metingen metingen en rapporten alleen voor de latentie om Azure-regio's te bereiken. Als u gebruikmaakt van route ring op basis van prestaties met eind punten die worden gehost in niet-Azure-regio's, kunt u nog steeds profiteren van deze functie door grotere latentie-informatie te hebben over de representatieve Azure-regio die u hebt geselecteerd om te worden gekoppeld aan dit eind punt.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Welke routerings methode heeft voor delen van Real-user-metingen?

De aanvullende gegevens die via Real-user-metingen zijn verkregen, zijn alleen van toepassing op profielen die gebruikmaken van de routerings methode voor prestaties. De koppeling Real-user-metingen is beschikbaar vanuit alle profielen wanneer u deze weergeeft via de Azure Portal.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Moet ik elk profiel afzonderlijk inschakelen Real-user-metingen?

Nee, u hoeft deze maar één keer per abonnement in te scha kelen en alle latentie gegevens die worden gemeten en gerapporteerd, zijn beschikbaar voor alle profielen.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Hoe kan ik Real-user-metingen voor mijn abonnement uitschakelen?

U kunt geen kosten meer in rekening brengen die betrekking hebben op Real-user-metingen wanneer u stopt met het verzamelen en verzenden van latentie metingen van uw client toepassing. Als u bijvoorbeeld Java script insluit op webpagina's, kunt u deze functie niet meer gebruiken door de Java script te verwijderen of door de aanroep uit te scha kelen wanneer de pagina wordt gerenderd.

U kunt Real-user-metingen ook uitschakelen door uw sleutel te verwijderen. Wanneer u de sleutel verwijdert, worden alle metingen die naar Traffic Manager met die sleutel worden verzonden, verwijderd.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Kan ik Real-user-metingen gebruiken met andere client toepassingen dan webpagina's?

Ja, Real-user-metingen is ontworpen voor het opnemen van gegevens die zijn verzameld via verschillende soorten clients van eind gebruikers. Deze veelgestelde vragen worden bijgewerkt als nieuwe typen client toepassingen worden ondersteund.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Hoeveel metingen worden er uitgevoerd telkens wanneer mijn Real-user-metingen ingeschakelde webpagina wordt weer gegeven?

Als Real-user-metingen wordt gebruikt met de beschik bare meting java script, resulteert elke pagina-rendering in zes metingen die worden uitgevoerd. Deze worden vervolgens weer aan de Traffic Manager-service gerapporteerd. Er worden kosten in rekening gebracht voor deze functie op basis van het aantal metingen dat is gerapporteerd aan Traffic Manager service. Als de gebruiker bijvoorbeeld naar de pagina gaat tijdens het maken van de metingen, maar voordat deze werd gerapporteerd, worden deze metingen niet in rekening gebracht voor facturerings doeleinden.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Is er een vertraging voordat Real-user-metingen script wordt uitgevoerd op mijn webpagina?

Nee, er is geen vertraging geprogrammeerd voordat het script wordt aangeroepen.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Kan ik Real-user-metingen gebruiken met alleen de Azure-regio's die ik wil meten?

Nee, elke keer dat deze wordt aangeroepen, meet het Real-user-metingen script een set van zes Azure-regio's, zoals bepaald door de service. Deze set verandert tussen verschillende aanroepen en wanneer een groot aantal dergelijke aanroepen plaatsvindt, wordt de meet dekking verdeeld over de verschillende Azure-regio's.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Kan ik het aantal metingen beperken dat is uitgevoerd op een specifiek aantal?

De meting java script bevindt zich in uw webpagina en u hebt de volledige controle over wanneer u deze start en stopt met het gebruik ervan. Zolang de Traffic Manager-service een aanvraag ontvangt voor een lijst met te meten Azure-regio's, wordt een set regio's geretourneerd.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Kan ik zien welke metingen worden uitgevoerd door mijn client toepassing als onderdeel van Real-user-metingen?

Aangezien de meting logica wordt uitgevoerd vanuit uw client toepassing, hebt u de volledige controle over wat er gebeurt, zoals het bekijken van de latentie metingen. Traffic Manager rapporteert geen geaggregeerde weer gave van de metingen die zijn ontvangen in de sleutel die aan uw abonnement is gekoppeld.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Kan ik het meting script van Traffic Manager wijzigen?

Hoewel u bepaalt wat er op uw webpagina is inge sloten, raden we u ten zeerste aan wijzigingen aan te brengen in het meting script om ervoor te zorgen dat de latenties correct worden gemeten en gerapporteerd.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Is het mogelijk dat anderen de sleutel zien die ik gebruik met Real-user-metingen?

Wanneer u het meting script op een webpagina insluit, kunnen anderen het script en de sleutel van uw Real-user-metingen (RUM) zien. Het is echter belang rijk om te weten dat deze sleutel afwijkt van uw abonnements-id en wordt gegenereerd door Traffic Manager alleen voor dit doel einde te worden gebruikt. De veiligheid van uw RUM-sleutel heeft geen gevolgen voor uw Azure-account beveiliging.

### <a name="can-others-abuse-my-rum-key"></a>Kunnen anderen mijn RUM-sleutel misbruiken?

Hoewel het mogelijk is dat anderen uw sleutel gebruiken om onjuiste gegevens naar Azure te verzenden, wordt het bewerkings plan niet door een paar onjuiste metingen gewijzigd, omdat er rekening mee wordt gehouden met alle andere metingen die we ontvangen. Als u uw sleutels moet wijzigen, kunt u de sleutel opnieuw genereren op het moment dat de oude sleutel wordt verwijderd.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Moet ik de meting java script in al mijn webpagina's plaatsen?

Real-user-metingen levert meer waarde als het aantal metingen wordt verhoogd. Het is dan ook belang rijk om te bepalen of u deze in al uw webpagina's of een aantal selecteren moet plaatsen. We raden u aan om te beginnen door deze te plaatsen op de meest bezochte pagina waar een gebruiker vijf seconden of meer op de pagina wordt verwacht te blijven.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Kan informatie over mijn eind gebruikers worden geïdentificeerd door Traffic Manager als ik Real-user-metingen gebruik?

Wanneer de gegeven meting java script wordt gebruikt, heeft Traffic Manager inzicht in het client-IP-adres van de eind gebruiker en het bron-IP-adres van de lokale DNS-resolver die ze gebruiken. Traffic Manager maakt alleen gebruik van het IP-adres van de client nadat het is afgekapt zodat de specifieke eind gebruiker die de metingen heeft verzonden, niet kan worden geïdentificeerd.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>Moet de afmeting van de webpagina Real-user-metingen Traffic Manager voor route ring gebruiken?

Nee, het is niet nodig om Traffic Manager te gebruiken. De routerings kant van Traffic Manager werkt afzonderlijk van het meet deel van de echte gebruiker en hoewel het een goed idee is om ze beide in dezelfde Web-eigenschap te hebben, hoeven ze zich niet te bevinden.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Moet ik een service op Azure-regio's hosten om met Real-user-metingen te gebruiken?

Nee, u hoeft geen onderdelen aan de server zijde op Azure te hosten om Real-user-metingen te kunnen werken. De afbeelding met één pixel die wordt gedownload door de meting java script en de service die wordt uitgevoerd in verschillende Azure-regio's, wordt gehost en beheerd door Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Neemt mijn Azure-bandbreedte gebruik toe wanneer ik Real-user-metingen gebruik?

Zoals vermeld in het vorige antwoord, worden de Server onderdelen van Real-user-metingen eigendom en beheerd door Azure. Dit betekent dat uw Azure-bandbreedte gebruik niet wordt verhoogd omdat u Real-user-metingen gebruikt. Dit omvat geen bandbreedte gebruik buiten de Azure-kosten. We beperken de band breedte die wordt gebruikt door slechts één pixel afbeelding te downloaden om de latentie naar een Azure-regio te meten. 

## <a name="traffic-view"></a>Verkeersweergave

### <a name="what-does-traffic-view-do"></a>Wat doet Verkeersweergave?

Verkeersweergave is een functie van Traffic Manager die u helpt meer inzicht te krijgen in uw gebruikers en hoe hun ervaring zich kan voordoen. Het gebruikt de query's die worden ontvangen door Traffic Manager en de netwerk latentie Intelligence-tabellen die de service onderhoudt om u het volgende te bieden:

- De regio's van waaruit uw gebruikers verbinding maken met uw eind punten in Azure.
- Het volume van gebruikers die verbinding maken vanuit deze regio's.
- De Azure-regio's waarnaar ze worden doorgestuurd.
- De latentie-ervaring van deze Azure-regio's.

Deze informatie is beschikbaar om u te gebruiken via geografische kaart-overlay en tabellaire weer gaven in de portal, naast de beschik bare onbewerkte gegevens die u kunt downloaden.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Hoe kan ik profiteren van het gebruik van Verkeersweergave?

Verkeersweergave geeft u het algehele overzicht van het verkeer dat door uw Traffic Manager-profielen wordt ontvangen. Met name kan het worden gebruikt om te begrijpen waar uw gebruikers basis verbinding maakt, en wat hun gemiddelde latentie ervaring is. U kunt deze informatie vervolgens gebruiken om gebieden te vinden waarin u zich moet concentreren, bijvoorbeeld door uw Azure-footprint uit te breiden naar een regio die deze gebruikers met een lagere latentie kan verwerken. Een ander inzicht dat u kunt afleiden van Verkeersweergave is het weer geven van de patronen van verkeer naar verschillende regio's die op zijn beurt u kunnen helpen bij het nemen van beslissingen over het verg Roten of verkleinen van de voor Raad in die regio's.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Hoe wijkt Verkeersweergave af van de Traffic Manager metrische gegevens die beschikbaar zijn via Azure monitor?

Azure Monitor kan worden gebruikt om inzicht te krijgen in het verkeer dat door uw profiel en de bijbehorende eind punten wordt ontvangen. U kunt ook de status van de eind punten bijhouden door de resultaten van de status controle weer te geven. Als u meer wilt weten over de ervaring van uw eind gebruiker met het maken van verbinding met Azure op een regionaal niveau, kunt Verkeersweergave gebruiken om dat te doen.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Gebruikt Verkeersweergave informatie over het subnet van EDNS-client?

De DNS-query's die door Azure Traffic Manager worden geleverd, overwegen ECS-informatie om de nauw keurigheid van de route ring te verg Roten. Maar bij het maken van de gegevensset die laat zien waar de gebruikers verbinding mee maken, gebruikt Verkeersweergave alleen het IP-adres van de DNS-resolver.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Hoeveel dagen aan gegevens Verkeersweergave gebruik?

Verkeersweergave wordt de uitvoer gemaakt door de gegevens te verwerken van de zeven dagen vóór de dag voordat deze door u worden bekeken. Dit is een zwevend venster en de meest recente gegevens worden gebruikt telkens wanneer u een bezoek doet.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Hoe verwerkt Verkeersweergave externe eind punten?

Wanneer u externe eind punten gebruikt die buiten Azure-regio's worden gehost in een Traffic Manager profiel, kunt u ervoor kiezen om deze te koppelen aan een Azure-regio die een proxy voor de latentie kenmerken is (dit is in feite nodig als u de methode voor de prestaties van de route ring gebruikt). Als deze Azure-regio toewijzing heeft, worden de metrische gegevens over de latentie van Azure-regio's gebruikt bij het maken van de Verkeersweergave uitvoer. Als er geen Azure-regio is opgegeven, is de latentie-informatie leeg in de gegevens voor deze externe eind punten.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Moet ik Verkeersweergave inschakelen voor elk profiel in mijn abonnement?

Tijdens de preview-periode is Verkeersweergave ingeschakeld op abonnements niveau. Als onderdeel van de verbeteringen die we hebben aangebracht vóór de algemene Beschik baarheid, kunt u nu Verkeersweergave inschakelen op profiel niveau, zodat u meer gedetailleerdere mogelijkheden hebt om deze functie in te scha kelen. Verkeersweergave wordt standaard uitgeschakeld voor een profiel.

>[!NOTE]
>Als u tijdens de preview-periode Verkeersweergave op abonnements niveau hebt ingeschakeld, moet u deze nu weer inschakelen voor elk van de profielen onder dat abonnement.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Hoe kan ik Verkeersweergave uitschakelen?

U kunt Verkeersweergave voor elk profiel uitschakelen met behulp van de portal of REST API. 

### <a name="how-does-traffic-view-billing-work"></a>Hoe werkt Verkeersweergave facturering?

Verkeersweergave prijzen zijn gebaseerd op het aantal gegevens punten dat wordt gebruikt om de uitvoer te maken. Op dit moment wordt het enige ondersteunde gegevens type de query's die uw profiel ontvangt. Daarnaast wordt u alleen gefactureerd voor de verwerking die is uitgevoerd op het moment dat u Verkeersweergave hebt ingeschakeld. Dit betekent dat als u in een maand Verkeersweergave inschakelt voor een bepaalde periode en deze functie uitschakelt tijdens andere tijden, alleen de gegevens punten die worden verwerkt terwijl u het aantal functies hebt ingeschakeld in uw factuur.

## <a name="traffic-manager-endpoints"></a>Traffic Manager-eindpunten

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Kan ik Traffic Manager gebruiken met eind punten van meerdere abonnementen?

Het gebruik van eind punten van meerdere abonnementen is niet mogelijk met Azure Web Apps. Voor Azure Web Apps is vereist dat alle aangepaste domein namen die worden gebruikt met Web Apps alleen worden gebruikt binnen één abonnement. Het is niet mogelijk om Web Apps te gebruiken uit meerdere abonnementen met dezelfde domein naam.

Voor andere eindpunt typen is het mogelijk om Traffic Manager te gebruiken met eind punten van meer dan één abonnement. In Resource Manager kunnen eind punten van elk abonnement worden toegevoegd aan Traffic Manager, mits de persoon die het Traffic Manager profiel heeft geconfigureerd Lees toegang heeft tot het eind punt. Deze machtigingen kunnen worden verleend met behulp [van toegangs beheer op basis van rollen (Azure RBAC)](../role-based-access-control/role-assignments-portal.md). Eind punten van andere abonnementen kunnen worden toegevoegd met behulp van [Azure PowerShell](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) of de [Azure cli](/cli/azure/network/traffic-manager/endpoint#az-network-traffic-manager-endpoint-create).

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Kan ik Traffic Manager gebruiken met staging-sleuven van Cloud service?

Ja. Staging-sleuven van Cloud Services kunnen worden geconfigureerd in Traffic Manager als externe eind punten. Status controles worden nog steeds in rekening gebracht tegen het Azure-eindpunten tarief.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Ondersteunt Traffic Manager IPv6-eind punten?

Traffic Manager biedt momenteel geen IPv6-adresseer bare naam servers. Traffic Manager kan echter nog steeds worden gebruikt door IPv6-clients die verbinding maken met IPv6-eind punten. Een client maakt DNS-aanvragen niet rechtstreeks naar Traffic Manager. In plaats daarvan maakt de client gebruik van een recursieve DNS-service. Een IPv6-client verzendt aanvragen naar de recursieve DNS-service via IPv6. Vervolgens moet de recursieve service verbinding kunnen maken met de Traffic Manager naam servers met behulp van IPv4.

Traffic Manager antwoordt met de DNS-naam of het IP-adres van het eind punt. Er zijn twee opties voor het ondersteunen van een IPv6-eind punt. U kunt het eind punt toevoegen als een DNS-naam die een bijbehorende AAAA-record heeft en Traffic Manager de status controleert of het eind punt en retour neren als een CNAME-record type in het query-antwoord. U kunt dit eind punt ook rechtstreeks toevoegen met het IPv6-adres en Traffic Manager retourneert een AAAA-type record in het query-antwoord.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Kan ik Traffic Manager gebruiken met meer dan één web-app in dezelfde regio?

Normaal gesp roken wordt Traffic Manager gebruikt voor het omleiden van verkeer naar toepassingen die in verschillende regio's worden geïmplementeerd. Het kan echter ook worden gebruikt wanneer een toepassing meer dan één implementatie in dezelfde regio heeft. De Traffic Manager Azure-eind punten staan niet toe dat meer dan een web-app-eind punt uit dezelfde Azure-regio wordt toegevoegd aan hetzelfde Traffic Manager-profiel.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription"></a>Hoe kan ik de Azure-eind punten van mijn Traffic Manager-profiel verplaatsen naar een andere resource groep of een ander abonnement?

Azure-eind punten die zijn gekoppeld aan een Traffic Manager profiel, worden bijgehouden met hun resource-Id's. Wanneer een Azure-resource die wordt gebruikt als een eind punt (bijvoorbeeld een openbaar IP-adres, een klassieke Cloud service, WebApp of een ander Traffic Manager profiel dat op een geneste manier wordt gebruikt) wordt verplaatst naar een andere resource groep of een ander abonnement, wordt de resource-ID gewijzigd. In dit scenario moet u het Traffic Manager-profiel momenteel bijwerken door eerst de eind punten te verwijderen en vervolgens opnieuw aan het profiel toe te voegen.

## <a name="traffic-manager-endpoint-monitoring"></a>Eindpuntbewaking in Traffic Manager

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Is het Traffic Managerbaar voor problemen met Azure-regio's?

Traffic Manager is een belang rijk onderdeel van de levering van Maxi maal beschik bare toepassingen in Azure.
Om hoge Beschik baarheid te kunnen bieden, moet Traffic Manager een uitzonderlijk hoog niveau van Beschik baarheid hebben en flexibel zijn voor regionale storingen.

Traffic Manager onderdelen zijn standaard flexibel voor een volledige uitval van een Azure-regio. Deze tolerantie is van toepassing op alle Traffic Manager onderdelen: de DNS-naam servers, de API, de opslaglaag en de eindpunt bewakings service.

In het onwaarschijnlijke geval van een storing van een volledige Azure-regio, wordt Traffic Manager verwacht normaal te blijven functioneren. Toepassingen die in meerdere Azure-regio's zijn geïmplementeerd, kunnen afhankelijk zijn van Traffic Manager om verkeer naar een beschikbaar exemplaar van hun toepassing te sturen.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Wat is de invloed van de locatie van de resource groep op Traffic Manager?

Traffic Manager is een enkele, wereld wijde service. Het is niet regionaal. De locatie van de resource groep is niet hetzelfde als Traffic Manager profielen die in die resource groep zijn geïmplementeerd.

Azure Resource Manager vereist dat alle resource groepen een locatie opgeven, waarmee de standaard locatie wordt bepaald voor resources die in die resource groep worden geïmplementeerd. Wanneer u een Traffic Manager profiel maakt, wordt het in een resource groep gemaakt. Alle Traffic Manager profielen gebruiken **globaal** als locatie, waarbij de standaard instelling van de resource groep wordt overschreven.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Hoe kan ik de huidige status van elk eind punt bepalen?

De huidige bewakings status van elk eind punt, naast het algehele profiel, wordt weer gegeven in de Azure Portal. Deze informatie is ook beschikbaar via de verkeers monitor [rest API](/rest/api/trafficmanager/), [Power shell-cmdlets](/powershell/module/az.trafficmanager)en [platformoverschrijdende Azure cli](/cli/azure/install-classic-cli).

U kunt Azure Monitor ook gebruiken om de status van uw eind punten bij te houden en een visuele weer gave ervan te bekijken. Zie de [documentatie van Azure monitoring](../azure-monitor/data-platform.md)voor meer informatie over het gebruik van Azure monitor.

### <a name="can-i-monitor-https-endpoints"></a>Kan ik HTTPS-eind punten controleren?

Ja. Traffic Manager biedt ondersteuning voor het zoeken naar HTTPS. Configureer **https** als het protocol in de bewakings configuratie.

Traffic Manager kan geen certificaat validatie opgeven, met inbegrip van:

* Certificaten aan de server zijde worden niet gevalideerd
* Certificaten aan de SNI-server zijde zijn niet gevalideerd
* Client certificaten worden niet ondersteund

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Moet ik een IP-adres of een DNS-naam gebruiken wanneer ik een eind punt toevoeg?

Traffic Manager ondersteunt het toevoegen van eind punten met behulp van drie manieren om ze te verwijzen als een DNS-naam, als een IPv4-adres en als een IPv6-adres. Als het eind punt als een IPv4-of IPv6-adres is toegevoegd, is het antwoord van de query respectievelijk van het record type A of AAAA. Als het eind punt is toegevoegd als een DNS-naam, is de query reactie van het record type CNAME. Het toevoegen van eind punten als IPv4-of IPv6-adres is alleen toegestaan als het eind punt van het type **extern** is.
Alle routerings methoden en bewakings instellingen worden ondersteund door de drie typen eindpunt adressering.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Welke typen IP-adressen kan ik gebruiken bij het toevoegen van een eind punt?

Met Traffic Manager kunt u IPv4-of IPv6-adressen gebruiken om eind punten op te geven. Er zijn enkele beperkingen die hieronder worden weer gegeven:

- Adressen die overeenkomen met gereserveerde privé-IP-adres ruimten zijn niet toegestaan. Voor beelden van deze adressen zijn: RFC 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 en RFC 5771.
- Het adres mag geen poort nummers bevatten (u kunt opgeven welke poorten moeten worden gebruikt in de configuratie-instellingen van het profiel)
- Een van de twee eind punten in hetzelfde profiel kan hetzelfde doel-IP-adres hebben

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Kan ik verschillende typen eindpunt adresseren gebruiken binnen één profiel?

Nee, met Traffic Manager kunt u geen typen adres Sering in een profiel combi neren, met uitzonde ring van een profiel met een routerings type met meerdere waarden waar u IPv4-en IPv6-adresserings typen kunt combi neren

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>Wat gebeurt er wanneer het record type van een binnenkomende query verschilt van het record type dat is gekoppeld aan het adresserings type van de eind punten?

Wanneer een query wordt ontvangen voor een profiel, zoekt Traffic Manager eerst het eind punt dat moet worden geretourneerd volgens de opgegeven routerings methode en de integriteits status van de eind punten. Vervolgens wordt gekeken naar het record type dat is aangevraagd in de inkomende query en het record type dat is gekoppeld aan het eind punt voordat een antwoord wordt geretourneerd op basis van de onderstaande tabel.

Voor profielen met een andere routerings methode dan meerdere waarden:

|Binnenkomende query aanvraag|     Eindpunttype|     Antwoord gegeven|
|--|--|--|
|ALLE |    A/AAAA/CNAME |    Doel eindpunt| 
|A |    A/CNAME |    Doel eindpunt|
|A |    AAAA |    Geen gegevens |
|AAAA |    AAAA/CNAME |    Doel eindpunt|
|AAAA |    A |    Geen gegevens |
|CNAME |    CNAME |    Doel eindpunt|
|CNAME     |A/AAAA |    Geen gegevens |
|

Voor profielen waarvoor een routerings methode is ingesteld op meerdere waarden:

|Binnenkomende query aanvraag|     Eindpunttype |    Antwoord gegeven|
|--|--|--|
|ALLE |    Combi natie van A en AAAA |    Doel eindpunten|
|A |    Combi natie van A en AAAA |    Alleen doel eindpunten van het type A|
|AAAA    |Combi natie van A en AAAA|     Alleen doel eindpunten van het type AAAA|
|CNAME |    Combi natie van A en AAAA |    Geen gegevens |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Kan ik een profiel gebruiken met door IPv4/IPv6 geadresseerde eind punten in een genest profiel?

Ja, u kunt de uitzonde ring dat een profiel van het type meerdere waarden geen bovenliggend profiel in een geneste profielset kan zijn.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Ik heb een eind punt van een webtoepassing in mijn Traffic Manager profiel gestopt, maar ik heb geen verkeer ontvangen, zelfs nadat ik het heb gestart. Hoe kan ik dit oplossen?

Wanneer een eind punt van een Azure-webtoepassing wordt gestopt Traffic Manager stopt met het controleren van de status en worden de status controles pas opnieuw gestart nadat is vastgesteld dat het eind punt opnieuw is opgestart. Als u deze vertraging wilt voor komen, schakelt u dat eind punt uit in het Traffic Manager profiel nadat u het eind punt opnieuw hebt gestart.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Kan ik Traffic Manager gebruiken, zelfs als mijn toepassing geen ondersteuning voor HTTP of HTTPS heeft?

Ja. U kunt TCP opgeven als controle protocol en Traffic Manager kan een TCP-verbinding initiëren en wachten op een reactie van het eind punt. Als het eind punt reageert op de verbindings aanvraag met een reactie op het tot stand brengen van de verbinding, binnen de time-outperiode, wordt het eind punt als in orde gemarkeerd.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Welke specifieke antwoorden zijn vereist van het eind punt bij het gebruik van TCP-bewaking?

Wanneer TCP-bewaking wordt gebruikt, start Traffic Manager een drieweg TCP-handshake door een SYN-aanvraag te verzenden naar het eind punt op de opgegeven poort. Vervolgens wordt gewacht op een SYN-ACK-reactie van het eind punt voor een bepaalde tijd (opgegeven in de time-outinstellingen).

- Als er een SYN-ACK-antwoord wordt ontvangen binnen de time-outperiode die is opgegeven in de controle-instellingen, wordt dat eind punt als gezond beschouwd. Een FIN of FIN-ACK is het verwachte antwoord van de Traffic Manager wanneer het regel matig een socket beëindigt.
- Als een SYN-ACK-antwoord wordt ontvangen na de opgegeven time-out, reageert de Traffic Manager met een eerste om de verbinding opnieuw in te stellen.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Hoe snel Traffic Manager kan ik mijn gebruikers weghalen uit een beschadigd eind punt?

Traffic Manager biedt meerdere instellingen waarmee u het failover-gedrag van uw Traffic Manager profiel als volgt kunt beheren:

- u kunt opgeven dat de Traffic Manager de eind punten vaker moet controleren door het probing-interval op 10 seconden in te stellen. Dit zorgt ervoor dat alle eind punten die een slechte status hebben, zo snel mogelijk kunnen worden gedetecteerd. 
- u kunt opgeven hoe lang moet worden gewacht voordat een time-out voor de status controle is (de minimale time-outwaarde is 5 seconden).
- u kunt opgeven hoeveel fouten zich kunnen voordoen voordat het eind punt is gemarkeerd als beschadigd. Deze waarde kan minder zijn dan 0, in welk geval het eind punt is gemarkeerd als een slechte status zodra de eerste status controle mislukt. Het gebruik van de minimum waarde 0 voor het toegelaten aantal fouten kan echter leiden tot eind punten vanwege eventuele tijdelijke problemen die zich op het moment van zoeken kunnen voordoen.
- u kunt de TTL (time-to-Live) voor de DNS-reactie opgeven als 0. Dit betekent dat DNS-resolvers het antwoord niet in de cache kunnen opslaan en elke nieuwe query krijgt een antwoord met de meest actuele status informatie die de Traffic Manager heeft.

Met deze instellingen kunt Traffic Manager failovers uitvoeren binnen 10 seconden nadat een eind punt is beschadigd en een DNS-query wordt uitgevoerd voor het bijbehorende profiel.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Hoe kan ik verschillende bewakings instellingen opgeven voor verschillende eind punten in een profiel?

Instellingen voor Traffic Manager bewaking bevinden zich op een niveau per profiel. Als u voor slechts één eind punt een andere bewakings instelling moet gebruiken, kan dit eind punt worden uitgevoerd als een [genest profiel](traffic-manager-nested-profiles.md) waarvan de bewakings instellingen verschillen van het bovenliggende profiel.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Hoe kan ik HTTP-headers toewijzen aan de Traffic Manager status controles voor mijn eind punten?

Met Traffic Manager kunt u aangepaste headers opgeven in de HTTP (S) status controles die worden geïnitieerd voor uw eind punten. Als u een aangepaste koptekst wilt opgeven, kunt u dat doen op profiel niveau (van toepassing op alle eind punten) of op het niveau van het eind punt opgeven. Als er op beide niveaus een kop is gedefinieerd, wordt het profiel niveau 1 overschreven door het niveau dat is opgegeven op het endpointniveau.
Een veelvoorkomende use-case is het opgeven van hostheaders zodat Traffic Manager aanvragen mogelijk correct worden doorgestuurd naar een eind punt dat wordt gehost in een multi tenant-omgeving. Een ander voor beeld hiervan is het identificeren van Traffic Manager aanvragen van de HTTP (S)-aanvraag logboeken van een eind punt

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Welke host-header gebruikt de eindpunt status controles?

Als er geen aangepaste host-header-instelling is opgegeven, is de host-header die wordt gebruikt door Traffic Manager, de DNS-naam van het eind punt dat in het profiel is geconfigureerd, indien beschikbaar.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Wat zijn de IP-adressen waarvan de status controles afkomstig zijn?

Klik [hier](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) voor meer informatie over het ophalen van de lijsten met IP-adressen waarvan Traffic Manager status controles kunnen zijn. U kunt REST API, Azure CLI of Azure PowerShell gebruiken om de meest recente lijst op te halen. Controleer de vermelde Ip's om ervoor te zorgen dat binnenkomende verbindingen van deze IP-adressen op de eind punten zijn toegestaan om de status te controleren.

Voor beeld van het gebruik van Azure PowerShell:

```azurepowershell-interactive
$serviceTags = Get-AzNetworkServiceTag -Location eastus
$result = $serviceTags.Values | Where-Object { $_.Name -eq "AzureTrafficManager" }
$result.Properties.AddressPrefixes
```

> [!NOTE]
> Open bare IP-adressen kunnen zonder kennisgeving worden gewijzigd. Zorg ervoor dat u de meest recente informatie ophaalt met behulp van de service label detectie-API of het Download bare JSON-bestand.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Hoeveel status controles voor mijn eind punt kan ik verwachten van Traffic Manager?

Het aantal Traffic Manager status controles dat uw eind punt bereikt, is afhankelijk van het volgende:

- de waarde die u hebt ingesteld voor het controle-interval (kleinere interval betekent dat meer aanvragen op uw eind punt binnen een bepaalde periode worden gepingd).
- het aantal locaties van waaruit de status controles afkomstig zijn (de IP-adressen waarvan u deze controles kunt verwachten, worden vermeld in de vorige veelgestelde vragen).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Hoe kan ik een melding krijgen als een van mijn eind punten uitvalt?

Een van de metrische gegevens die door Traffic Manager worden verschaft, is de status van eind punten in een profiel. U kunt deze weer geven als een aggregatie van alle eind punten in een profiel (bijvoorbeeld 75% van uw eind punten in orde) of, op een niveau per eind punt. Traffic Manager metrische gegevens worden weer gegeven via Azure Monitor en u kunt de [waarschuwings mogelijkheden](../azure-monitor/alerts/alerts-metric.md) ervan gebruiken om meldingen te ontvangen wanneer er een wijziging is in de status van uw eind punt. Zie [Traffic Manager metrische gegevens en waarschuwingen](traffic-manager-metrics-alerts.md)voor meer informatie.  

## <a name="traffic-manager-nested-profiles"></a>Traffic Manager geneste profielen

### <a name="how-do-i-configure-nested-profiles"></a>Hoe kan ik geneste profielen configureren?

Geneste Traffic Manager profielen kunnen worden geconfigureerd met behulp van zowel de Azure Resource Manager als de klassieke Azure REST Api's, Azure PowerShell-cmdlets en platformoverschrijdende Azure CLI-opdrachten. Ze worden ook ondersteund via de nieuwe Azure Portal.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Hoeveel lagen nesten ondersteunt Traffic Manager?

U kunt profielen nesten tot 10 niveaus diep. ' Lussen ' zijn niet toegestaan.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Kan ik andere eindpunt typen met geneste onderliggende profielen combi neren in hetzelfde Traffic Manager profiel?

Ja. Er zijn geen beperkingen voor het combi neren van eind punten van verschillende typen binnen een profiel.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Hoe is het facturerings model van toepassing op geneste profielen?

Er zijn geen negatieve prijs gevolgen voor het gebruik van geneste profielen.

Traffic Manager facturering heeft twee onderdelen: status controles van eind punten en miljoenen DNS-query's

* Status controles van het eind punt: er worden geen kosten in rekening gebracht voor een onderliggend profiel wanneer het is geconfigureerd als een eind punt in een bovenliggend profiel. De controle van de eind punten in het onderliggende profiel wordt op de gebruikelijke manier gefactureerd.
* DNS-query's: elke query wordt slechts één keer geteld. Een query op basis van een bovenliggend profiel dat een eind punt uit een onderliggend profiel retourneert, wordt alleen voor het bovenliggende profiel geteld.

Zie de pagina met prijzen voor [Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/)voor meer informatie.

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Is er sprake van invloed op de prestaties van geneste profielen?

Nee. Het gebruik van geneste profielen heeft geen invloed op de prestaties.

De Traffic Manager naam servers bladeren de profiel hiërarchie intern tijdens het verwerken van elke DNS-query. Een DNS-query naar een bovenliggend profiel kan een DNS-antwoord met een eind punt ontvangen van een onderliggend profiel. Eén CNAME-record wordt gebruikt ongeacht of u één profiel of geneste profielen gebruikt. Het is niet nodig om een CNAME-record te maken voor elk profiel in de hiërarchie.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Hoe berekent Traffic Manager de status van een genest eind punt in een bovenliggend profiel?

Het bovenliggende profiel voert niet rechtstreeks status controles uit op het kind. In plaats daarvan wordt de status van de eind punten van het onderliggende profiel gebruikt voor het berekenen van de algemene status van het onderliggende profiel. Deze informatie wordt door gegeven aan de hiërarchie van geneste profielen om de status van het geneste eind punt te bepalen. Het bovenliggende profiel gebruikt deze geaggregeerde status om te bepalen of het verkeer naar de onderliggende locatie kan worden omgeleid.

In de volgende tabel wordt het gedrag van Traffic Manager status controles voor een genest eind punt beschreven.

| Monitor status van onderliggend profiel | Monitor status van bovenliggend eind punt | Notities |
| --- | --- | --- |
| Uitgeschakeld. Het onderliggende profiel is uitgeschakeld. |Gestopt |De status van het bovenliggende eind punt is gestopt, niet uitgeschakeld. De uitgeschakelde status is gereserveerd om aan te geven dat u het eind punt in het bovenliggende profiel hebt uitgeschakeld. |
| Gedegradeerd. Ten minste één onderliggend Profiel van de onderliggende profielen heeft een gedegradeerde status. |Online: het aantal online eindpunten in het onderliggende profiel is ten minste de waarde van MinChildEndpoints.<BR>CheckingEndpoint: het aantal online plus CheckingEndpoint-eind punten in het onderliggende profiel is ten minste de waarde van MinChildEndpoints.<BR>Gedegradeerd: anders. |Verkeer wordt doorgestuurd naar het eind punt van de status CheckingEndpoint. Als MinChildEndpoints te hoog is ingesteld, wordt het eind punt altijd gedegradeerd. |
| Online. Ten minste één onderliggend profiel voor een onderliggend knoop punt is een online status. Er is geen eind punt met de gedegradeerde status. |Zie hierboven. | |
| CheckingEndpoints. Ten minste één onderliggend profiel voor een onderliggend knoop punt is ' CheckingEndpoint '. Er zijn geen eind punten ' online ' of ' verslechterd ' |Hetzelfde als hierboven. | |
| Inactieve. Alle eind punten van een onderliggend profiel zijn uitgeschakeld of gestopt of dit profiel heeft geen eind punten. |Gestopt | |

## <a name="next-steps"></a>Volgende stappen:

- Meer informatie over Traffic Manager [endpoint-bewaking en automatische failover](../traffic-manager/traffic-manager-monitoring.md).
- Meer informatie over methoden voor het [routeren](../traffic-manager/traffic-manager-routing-methods.md)van Traffic Manager verkeer.