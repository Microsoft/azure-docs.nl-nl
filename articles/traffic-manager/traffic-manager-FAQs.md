---
title: Azure Traffic Manager - Veelgestelde vragen
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
ms.openlocfilehash: 708d63695cbba53578b13d1674b9aa99018bcae4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791117"
---
# <a name="traffic-manager-frequently-asked-questions-faq"></a>Traffic Manager veelgestelde vragen (FAQ)

## <a name="traffic-manager-basics"></a>Traffic Manager basisinformatie

### <a name="what-ip-address-does-traffic-manager-use"></a>Welk IP-adres Traffic Manager gebruikt?

Zoals uitgelegd in [Hoe Traffic Manager werkt,](../traffic-manager/traffic-manager-how-it-works.md)Traffic Manager op DNS-niveau. Dns-antwoorden worden verzonden om clients naar het juiste service-eindpunt te leiden. Clients maken vervolgens rechtstreeks verbinding met het service-eindpunt, niet via Traffic Manager.

Daarom biedt Traffic Manager geen eindpunt of IP-adres voor clients om verbinding mee te maken. Als u een statisch IP-adres voor uw service wilt, moet dat worden geconfigureerd bij de service, niet in Traffic Manager.

### <a name="what-types-of-traffic-can-be-routed-using-traffic-manager"></a>Welke soorten verkeer kunnen worden gerouteerd met Traffic Manager?
Zoals uitgelegd in How Traffic Manager Works (Hoe Traffic Manager [werkt)](../traffic-manager/traffic-manager-how-it-works.md)kan Traffic Manager eindpunt elke internetservice zijn die binnen of buiten Azure wordt gehost. Daarom kunnen Traffic Manager verkeer dat afkomstig is van het openbare internet, omgeleid naar een set eindpunten die ook op internet zijn gericht. Als u eindpunten hebt die zich binnen een particulier netwerk (bijvoorbeeld een interne versie van [Azure Load Balancer)](../load-balancer/components.md#frontend-ip-configurations)of gebruikers dns-aanvragen laten doen van dergelijke interne netwerken, kunt u Traffic Manager niet gebruiken om dit verkeer te routeer.

### <a name="does-traffic-manager-support-sticky-sessions"></a>Biedt Traffic Manager ondersteuning voor 'plaksessies'?

Zoals uitgelegd in [Hoe Traffic Manager werkt,](../traffic-manager/traffic-manager-how-it-works.md)Traffic Manager op DNS-niveau. Dns-antwoorden worden gebruikt om clients naar het juiste service-eindpunt te leiden. Clients maken rechtstreeks verbinding met het service-eindpunt, niet via Traffic Manager. Daarom ziet Traffic Manager http-verkeer tussen de client en de server niet.

Daarnaast behoort het bron-IP-adres van de DNS-query die door Traffic Manager wordt ontvangen, tot de recursieve DNS-service, niet de client. Daarom heeft Traffic Manager geen manier om afzonderlijke clients bij te houden en kunnen er geen 'sticky' sessies worden geïmplementeerd. Deze beperking is gebruikelijk voor alle op DNS gebaseerde verkeersbeheersystemen en is niet specifiek voor Traffic Manager.

### <a name="why-am-i-seeing-an-http-error-when-using-traffic-manager"></a>Waarom zie ik een HTTP-fout bij het gebruik Traffic Manager?

Zoals uitgelegd in [Hoe Traffic Manager werkt,](../traffic-manager/traffic-manager-how-it-works.md)Traffic Manager op DNS-niveau. Er wordt gebruikgemaakt van DNS-antwoorden om clients naar het juiste service-eindpunt te leiden. Clients maken vervolgens rechtstreeks verbinding met het service-eindpunt, niet via Traffic Manager. Traffic Manager ziet geen HTTP-verkeer tussen client en server. Daarom moet elke HTTP-fout die u ziet, afkomstig zijn van uw toepassing. Alle STAPPEN voor DNS-resolutie zijn voltooid om de client verbinding te laten maken met de toepassing. Dit omvat elke interactie die Traffic Manager heeft op de verkeersstroom van de toepassing.

Verder onderzoek moet zich daarom richten op de toepassing.

De HTTP-hostheader die vanuit de browser van de client wordt verzonden, is de meest voorkomende bron van problemen. Zorg ervoor dat de toepassing is geconfigureerd om de juiste hostheader te accepteren voor de domeinnaam die u gebruikt. Zie Een aangepaste domeinnaam configureren voor een [web-app in](../app-service/configure-domain-traffic-manager.md)Azure App Service voor eindpunten die de Azure App Service gebruiken Traffic Manager.

### <a name="what-is-the-performance-impact-of-using-traffic-manager"></a>Wat zijn de gevolgen voor de prestaties van het gebruik Traffic Manager?

Zoals uitgelegd in [Hoe Traffic Manager werkt,](../traffic-manager/traffic-manager-how-it-works.md)Traffic Manager op DNS-niveau. Omdat clients rechtstreeks verbinding maken met uw service-eindpunten, heeft dit geen invloed op de prestaties wanneer ze Traffic Manager zodra de verbinding tot stand is gebracht.

Omdat Traffic Manager kan worden geïntegreerd met toepassingen op DNS-niveau, moet er een extra DNS-zoekactie worden ingevoegd in de dns-resolutieketen. De impact van Traffic Manager op de DNS-resolutietijd is minimaal. Traffic Manager maakt gebruik van een globaal netwerk van naamservers en maakt gebruik van [anycast-netwerken](https://en.wikipedia.org/wiki/Anycast) om ervoor te zorgen dat DNS-query's altijd worden doorgeleid naar de dichtstbijzijnde beschikbare naamserver. Bovendien betekent caching van DNS-antwoorden dat de extra DNS-latentie die wordt gemaakt door het gebruik van Traffic Manager alleen van toepassing is op een fractie van de sessies.

De prestatiemethode routeert verkeer naar het dichtstbijzijnde beschikbare eindpunt. Het nettoresultaat is dat de algehele impact op de prestaties die aan deze methode is gekoppeld, minimaal moet zijn. Een toename in DNS-latentie moet worden verschoven door een lagere netwerklatentie naar het eindpunt.

### <a name="what-application-protocols-can-i-use-with-traffic-manager"></a>Welke toepassingsprotocollen kan ik gebruiken met Traffic Manager?

Zoals uitgelegd in [Hoe Traffic Manager werkt,](../traffic-manager/traffic-manager-how-it-works.md)Traffic Manager op DNS-niveau. Zodra de DNS-zoekactie is voltooid, maken clients rechtstreeks verbinding met het eindpunt van de toepassing, niet via Traffic Manager. Daarom kan de verbinding elk toepassingsprotocol gebruiken. Als u TCP als bewakingsprotocol selecteert, Traffic Manager de statuscontrole van eindpunten worden uitgevoerd zonder toepassingsprotocollen te gebruiken. Als u ervoor kiest om de status te verifiëren met behulp van een toepassingsprotocol, moet het eindpunt kunnen reageren op HTTP- of HTTPS GET-aanvragen.

### <a name="can-i-use-traffic-manager-with-a-naked-domain-name"></a>Kan ik Traffic Manager zonder voornaam gebruiken?

Ja. Zie Configure an alias record to support apex domain names with Traffic Manager (Een aliasrecord configureren om apex-domeinnamen te ondersteunen met Traffic Manager) voor meer informatie over het maken van een aliasrecord voor uw domeinnaam-apex om te verwijzen [naar een Azure Traffic Manager-profiel.](../dns/tutorial-alias-tm.md)

### <a name="does-traffic-manager-consider-the-client-subnet-address-when-handling-dns-queries"></a>Houdt Traffic Manager rekening met het subnetadres van de client bij het verwerken van DNS-query's? 

Ja, naast het bron-IP-adres van de DNS-query die wordt ontvangen (dit is meestal het IP-adres van de DNS-resolver), bij het uitvoeren van zoekmethoden voor geografische, prestatie- en subnetrouteringsmethoden, wordt in Traffic Manager ook rekening gebracht met het subnetadres van de client als het wordt opgenomen in de query door de resolver die de aanvraag namens de eindgebruiker doet.  
Met name [RFC 7871 – Clientsubnet in](https://tools.ietf.org/html/rfc7871) DNS-query's die een uitbreidingsmechanisme biedt voor [DNS (EDNS0)](https://tools.ietf.org/html/rfc2671) dat het clientsubnetadres kan doorgeven van resolvers die dit ondersteunen.

### <a name="what-is-dns-ttl-and-how-does-it-impact-my-users"></a>Wat is DNS TTL en wat is de invloed ervan op mijn gebruikers?

Wanneer een DNS-query op een Traffic Manager, stelt deze een waarde in het antwoord in met de naam time-to-live (TTL). Deze waarde, waarvan de eenheid in seconden is, geeft downstream aan DNS-resolvers aan hoe lang dit antwoord in de cache moet worden opgeslagen. Hoewel DNS-resolvers dit resultaat niet gegarandeerd in de cache kunnen plaatsen, kunnen ze met caching reageren op volgende query's uit de cache in plaats van naar de DNS-servers Traffic Manager te gaan. Dit heeft als volgt gevolgen voor de antwoorden:

- Een hogere TTL vermindert het aantal query's dat op de Traffic Manager DNS-servers belandt, waardoor de kosten voor een klant kunnen worden beperkt omdat het aantal query's dat wordt verzonden een factureerbaar gebruik is.
- Een hogere TTL kan mogelijk de tijd verminderen die nodig is om een DNS-zoekactie uit te doen.
- een hogere TTL betekent ook dat uw gegevens niet de meest recente statusinformatie weerspiegelen die Traffic Manager heeft verkregen via de agents.

### <a name="how-high-or-low-can-i-set-the-ttl-for-traffic-manager-responses"></a>Hoe hoog of laag kan ik de TTL instellen voor Traffic Manager antwoorden?

U kunt instellen op profielniveau de DNS-TTL zo laag als 0 seconden en maximaal 2.147.483.647 seconden (het maximale bereik compatibel met [RFC-1035).](https://www.ietf.org/rfc/rfc1035.txt ) Een TTL van 0 betekent dat downstream DNS-resolvers query-antwoorden niet in de cache plaatsen en dat alle query's naar verwachting de Traffic Manager DNS-servers bereiken voor resolutie.

### <a name="how-can-i-understand-the-volume-of-queries-coming-to-my-profile"></a>Hoe begrijp ik het aantal query's dat mijn profiel binnenkomt? 

Een van de metrische gegevens die door Traffic Manager is het aantal query's dat door een profiel wordt beantwoord. U kunt deze informatie verkrijgen op een aggregatie op profielniveau of u kunt deze verder opsplitsen om het aantal query's te zien waarin specifieke eindpunten zijn geretourneerd. Bovendien kunt u waarschuwingen instellen om u te waarschuwen als het antwoordvolume van de query de voorwaarden die u hebt ingesteld, kruist. Voor meer informatie kunt [Traffic Manager en waarschuwingen bekijken.](traffic-manager-metrics-alerts.md)

## <a name="traffic-manager-geographic-traffic-routing-method"></a>Traffic Manager geografische verkeersrouteringsmethode

### <a name="what-are-some-use-cases-where-geographic-routing-is-useful"></a>Wat zijn enkele gebruiksgevallen waarbij geografische routering nuttig is?

Geografische routeringstype kan worden gebruikt in elk scenario waarin een Azure-klant zijn gebruikers moet onderscheiden op basis van geografische regio's. Met de methode Geografische verkeersroutering kunt u gebruikers uit specifieke regio's bijvoorbeeld een andere gebruikerservaring geven dan gebruikers uit andere regio's. Een ander voorbeeld is het voldoen aan lokale gegevenssoevereiniteitsmanda die vereisen dat gebruikers uit een specifieke regio alleen worden bediend door eindpunten in die regio.

### <a name="how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method"></a>Hoe kan ik bepalen of ik de Routeringsmethode voor prestaties of Geografische routeringsmethode moet gebruiken?

Het belangrijkste verschil tussen deze twee populaire routeringsmethoden is dat bij De routeringsmethode voor prestaties uw primaire doel is om verkeer te verzenden naar het eindpunt dat de laagste latentie kan bieden aan de aanroeper, terwijl bij Geografische routering het primaire doel is om een geo-fence af te dwingen voor uw aanroepers, zodat u deze bewust naar een specifiek eindpunt kunt routeren. De overlapping vindt plaats omdat er een correlatie is tussen geografische nabijheid en een lagere latentie, hoewel dit niet altijd waar is. Mogelijk is er een eindpunt in een ander geografisch gebied dat een betere latentie-ervaring voor de aanroeper kan bieden. In dat geval stuurt Prestatieroutering de gebruiker naar dat eindpunt, maar stuurt geografische routering deze altijd naar het eindpunt dat u hebt aanbeland voor de geografische regio. Kijk eens naar het volgende voorbeeld: met Geografische routering kunt u ongebruikelijke toewijzingen maken, zoals het verzenden van al het verkeer van Azië naar eindpunten in de VERENIGDE Staten en al het Amerikaanse verkeer naar eindpunten in Azië. In dat geval doet geografische routering bewust precies waarvoor u deze hebt geconfigureerd en wordt prestatieoptimalisatie geen overweging. 
>[!NOTE]
>Er zijn mogelijk scenario's waarin u mogelijk zowel prestatie- als geografische routeringsmogelijkheden nodig hebt. Voor deze scenario's kunnen geneste profielen een goede keuze zijn. U kunt bijvoorbeeld een bovenliggend profiel met geografische routering instellen waar u al het verkeer van Noord-Amerika naar een genest profiel met eindpunten in de VERENIGDE Staten verzendt en prestatieroutering gebruikt om dat verkeer naar het beste eindpunt binnen die set te verzenden. 

### <a name="what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing"></a>Wat zijn de regio's die worden ondersteund door Traffic Manager voor geografische routering?

De land-/regiohiërarchie die wordt gebruikt door Traffic Manager kunt u hier [vinden.](traffic-manager-geographic-regions.md) Hoewel deze pagina up-to-date wordt gehouden met eventuele wijzigingen, kunt u dezelfde informatie ook programmatisch ophalen met behulp van [de Azure Traffic Manager REST API](/rest/api/trafficmanager/). 

### <a name="how-does-traffic-manager-determine-where-a-user-is-querying-from"></a>Hoe bepaalt Traffic Manager waar een gebruiker query's op uitvoert?

Traffic Manager bekijkt het bron-IP-adres van de query (dit is waarschijnlijk een lokale DNS-resolver die query's uitvoert namens de gebruiker) en gebruikt een intern IP-adres naar regiokaart om de locatie te bepalen. Deze kaart wordt voortdurend bijgewerkt om rekening te houden met wijzigingen op internet. 

### <a name="is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case"></a>Is het gegarandeerd dat Traffic Manager in elk geval de exacte geografische locatie van de gebruiker correct kan bepalen?

Nee, Traffic Manager kan om de volgende redenen niet garanderen dat de geografische regio die we afleiden van het bron-IP-adres van een DNS-query altijd overeenkomt met de locatie van de gebruiker:

- Ten eerste, zoals beschreven in de vorige veelgestelde vragen, is het bron-IP-adres dat we zien dat van een DNS-resolver die de zoekactie namens de gebruiker doet. Hoewel de geografische locatie van de DNS-resolver een goede proxy is voor de geografische locatie van de gebruiker, kan deze ook verschillen, afhankelijk van de footprint van de DNS-resolverservice en de specifieke DNS-resolverservice die een klant heeft gekozen. Een klant die zich in Maleisie bevindt, kan bijvoorbeeld in de instellingen van het apparaat opgeven dat een DNS-resolverservice wordt gebruikt waarvan de DNS-server in Singapore kan worden opgehaald om de queryresoluties voor die gebruiker/het apparaat te verwerken. In dat geval Traffic Manager alleen het IP-adres van de resolver zien dat overeenkomt met de locatie Singapore. Zie ook de eerdere veelgestelde vragen over adresondersteuning voor clientsubnetten op deze pagina.

- Ten tweede gebruikt Traffic Manager een interne kaart om het IP-adres te vertalen naar geografische regiovertalingen. Hoewel deze kaart voortdurend wordt gevalideerd en bijgewerkt om de nauwkeurigheid te vergroten en rekening te houden met de veranderende aard van internet, bestaat de kans dat onze informatie geen exacte weergave is van de geografische locatie van alle IP-adressen.

###  <a name="does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing"></a>Moet een eindpunt zich fysiek in dezelfde regio bevinden als het eindpunt dat is geconfigureerd voor geografische routering?

Nee, de locatie van het eindpunt legt geen beperkingen op aan welke regio's aan het eindpunt kunnen worden toegesneden. Een eindpunt in een Azure-US-Central kan bijvoorbeeld alle gebruikers uit India omgeleid krijgen.

### <a name="can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing"></a>Kan ik geografische regio's toewijzen aan eindpunten in een profiel dat niet is geconfigureerd voor geografische routering?

Ja, als de routeringsmethode van een profiel [](/rest/api/trafficmanager/) niet geografisch is, kunt u de Azure Traffic Manager REST API om geografische regio's toe te wijzen aan eindpunten in dat profiel. In het geval van niet-geografische routeringstypeprofielen wordt deze configuratie genegeerd. Als u een dergelijk profiel op een later tijdstip wijzigt in een geografisch routeringstype, Traffic Manager deze toewijzingen gebruiken.

### <a name="why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic"></a>Waarom krijg ik een foutmelding wanneer ik probeer de routeringsmethode van een bestaand profiel te wijzigen in Geografisch?

Aan alle eindpunten onder een profiel met geografische routering moet ten minste één regio zijn toe te passen. Als u een bestaand profiel wilt converteren naar een geografisch routeringstype, moet u eerst geografische regio's aan alle eindpunten koppelen met behulp van de Azure Traffic Manager REST API voordat u het routeringstype in geografische [regio's](/rest/api/trafficmanager/) kunt wijzigen. Als u de portal gebruikt, verwijdert u eerst de eindpunten, wijzigt u de routeringsmethode van het profiel in geografische regio en voegt u de eindpunten toe samen met de geografische regiotoewijzing.

### <a name="why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled"></a>Waarom wordt het sterk aanbevolen dat klanten geneste profielen maken in plaats van eindpunten onder een profiel met geografische routering ingeschakeld?

Een regio kan worden toegewezen aan slechts één eindpunt binnen een profiel als deze de geografische routeringsmethode gebruikt. Als dat eindpunt geen genest type is met een onderliggend profiel eraan gekoppeld, en als dat eindpunt een slechte status heeft, blijft Traffic Manager verkeer naar het eindpunt verzenden omdat het alternatief voor het niet verzenden van verkeer niet beter is. Traffic Manager maakt geen failover naar een ander eindpunt, zelfs niet wanneer de regio die is toegewezen een bovenliggend deel is van de regio die is toegewezen aan het eindpunt dat niet in orde is (als bijvoorbeeld een eindpunt met regio Spanje een slechte status heeft, wordt er geen failover naar een ander eindpunt met de regio Europa toegewezen). Dit wordt gedaan om ervoor te zorgen dat Traffic Manager de geografische grenzen respecteert die een klant in zijn profiel heeft ingesteld. Om het voordeel te krijgen van een failing over te brengen naar een ander eindpunt wanneer een eindpunt niet in orde is, wordt aanbevolen dat geografische regio's worden toegewezen aan geneste profielen met meerdere eindpunten in dit eindpunt in plaats van afzonderlijke eindpunten. Als op deze manier een eindpunt in het geneste onderliggende profiel uitvalt, kan verkeer een failover naar een ander eindpunt binnen hetzelfde geneste onderliggende profiel maken.

### <a name="are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type"></a>Zijn er beperkingen met de API-versie die ondersteuning biedt voor dit routeringstype?

Ja, alleen API-versie 2017-03-01 en nieuwer ondersteunt het geografische routeringstype. Oudere API-versies kunnen niet worden gebruikt om profielen van het geografische routeringstype te maken of geografische regio's toe te wijzen aan eindpunten. Als een oudere API-versie wordt gebruikt om profielen op te halen uit een Azure-abonnement, wordt geen profiel van het geografische routeringstype geretourneerd. Wanneer u oudere API-versies gebruikt, wordt voor elk profiel dat wordt geretourneerd met eindpunten met een geografische regiotoewijzing, bovendien geen geografische regiotoewijzing weergegeven.

## <a name="traffic-manager-subnet-traffic-routing-method"></a>Traffic Manager verkeersrouteringsmethode subnet

### <a name="what-are-some-use-cases-where-subnet-routing-is-useful"></a>Wat zijn enkele gebruiksgevallen waarbij subnetroutering nuttig is?

Met subnetroutering kunt u onderscheid maken tussen de ervaring die u biedt voor specifieke sets gebruikers die worden geïdentificeerd door het bron-IP-adres van het IP-adres van hun DNS-aanvragen. Een voorbeeld hiervan is het weergeven van andere inhoud als gebruikers verbinding maken met een website vanuit het hoofdkantoor van uw bedrijf. Een andere manier is het beperken van gebruikers van bepaalde ISP's tot alleen toegang tot eindpunten die alleen IPv4-verbindingen ondersteunen als deze ISP's subparse prestaties hebben wanneer IPv6 wordt gebruikt.
Een andere reden om subnetrouteringsmethode te gebruiken, is in combinatie met andere profielen in een geneste profielset. Als u bijvoorbeeld geografische routeringsmethode wilt gebruiken voor geo-fencing van uw gebruikers, maar voor een specifieke internetprovider een andere routeringsmethode wilt gebruiken, kunt u een profiel hebben met subnetrouteringsmethode als het bovenliggende profiel en die INTERNETPROVIDER overschrijven om een specifiek onderliggend profiel te gebruiken en het standaard geografische profiel voor iedereen te hebben.

### <a name="how-does-traffic-manager-know-the-ip-address-of-the-end-user"></a>Hoe weet Traffic Manager ip-adres van de eindgebruiker?

Apparaten van eindgebruikers gebruiken doorgaans een DNS-resolver om de DNS-zoekactie namens hen uit te voeren. Het uitgaande IP-adres van dergelijke resolvers is wat Traffic Manager als het bron-IP-adres ziet. Daarnaast kijkt de subnetrouteringsmethode ook of er ECS-gegevens (Extended Client Subnet) van EDNS0 zijn die zijn doorgegeven met de aanvraag. Als ECS-informatie aanwezig is, is dat het adres dat wordt gebruikt om de routering te bepalen. Als er geen ECS-gegevens zijn, wordt het bron-IP-adres van de query gebruikt voor routeringsdoeleinden.

### <a name="how-can-i-specify-ip-addresses-when-using-subnet-routing"></a>Hoe kan ik IP-adressen opgeven bij het gebruik van subnetroutering?

De IP-adressen die aan een eindpunt moeten worden koppelen, kunnen op twee manieren worden opgegeven. Eerst kunt u de decimale octet-notatie met kwadraten gebruiken met een begin- en eindadres om het bereik op te geven (bijvoorbeeld 1.2.3.4-5.6.7.8 of 3.4.5.6-3.4.5.6). Ten tweede kunt u de CIDR-notatie gebruiken om het bereik op te geven (bijvoorbeeld 1.2.3.0/24). U kunt meerdere bereik opgeven en beide notatietypen in een bereikset gebruiken. Er gelden enkele beperkingen.

-    U kunt geen overlap van adresbereiken hebben omdat elk IP-adres aan slechts één eindpunt moet worden toegesneden
-    Het beginadres mag niet meer zijn dan het eindadres
-    In het geval van de CIDR-notatie moet het IP-adres vóór de /het beginadres van dat bereik zijn (bijvoorbeeld 1.2.3.0/24 is geldig, maar 1.2.3.4.4/24 is niet geldig)

### <a name="how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing"></a>Hoe kan ik een terugval-eindpunt opgeven bij het gebruik van subnetroutering?

Als u in een profiel met subnetroutering een eindpunt hebt waar geen subnetten aan zijn toewezen, wordt elke aanvraag die niet met andere eindpunten komt, hier naar omgeleid. Het wordt ten zeerste aanbevolen dat u een dergelijk terugval-eindpunt in uw profiel hebt, omdat Traffic Manager een NXDOMAIN-antwoord retourneert als er een aanvraag binnenkomt en deze niet is toe te staan aan eindpunten of als deze is toe te staan aan een eindpunt, maar dat eindpunt niet in orde is.

### <a name="what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile"></a>Wat gebeurt er als een eindpunt is uitgeschakeld in een profiel voor subnetrouteringstype?

Als u in een profiel met subnetroutering een eindpunt hebt met dat is uitgeschakeld, gedraagt Traffic Manager zich alsof dat eindpunt en de subnettoewijzingen niet bestaan. Als een query die zou overeenkomen met de IP-adrestoewijzing wordt ontvangen en het eindpunt is uitgeschakeld, retourneert Traffic Manager een terugval-eindpunt (een zonder toewijzingen) of als een dergelijk eindpunt niet aanwezig is, retourneert een NXDOMAIN-antwoord.

## <a name="traffic-manager-multivalue-traffic-routing-method"></a>Traffic Manager verkeersrouteringsmethode met meerdere waarde

### <a name="what-are-some-use-cases-where-multivalue-routing-is-useful"></a>Wat zijn enkele gebruiksgevallen waarbij routering met meerdere waardes nuttig is?

Routering met meerdere waarde retourneert meerdere gezonde eindpunten in één queryreactie. Het belangrijkste voordeel hiervan is dat als een eindpunt niet in orde is, de client meer opties heeft om het opnieuw te proberen zonder nog een DNS-aanroep te doen (die mogelijk dezelfde waarde uit een upstream-cache retourneert). Dit is van toepassing op beschikbaarheidsgevoelige toepassingen die de downtime willen minimaliseren.
Een ander gebruik voor routeringsmethode met meerdere waarde is als een eindpunt 'dual-homed' is voor zowel IPv4- als IPv6-adressen en u de aanroeper beide opties wilt geven om uit te kiezen wanneer er een verbinding met het eindpunt wordt gestart.

### <a name="how-many-endpoints-are-returned-when-multivalue-routing-is-used"></a>Hoeveel eindpunten worden geretourneerd wanneer routering met meerdere waarde wordt gebruikt?

U kunt het maximum aantal eindpunten opgeven dat moet worden geretourneerd en MultiValue retourneert niet meer dan dat aantal gezonde eindpunten wanneer een query wordt ontvangen. De maximaal mogelijke waarde voor deze configuratie is 10.

### <a name="will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used"></a>Krijg ik dezelfde set eindpunten wanneer routering met meerdere waarde wordt gebruikt?

We kunnen niet garanderen dat dezelfde set eindpunten in elke query wordt geretourneerd. Dit wordt ook beïnvloed door het feit dat sommige eindpunten mogelijk niet in orde zijn op welk punt ze niet worden opgenomen in het antwoord

## <a name="real-user-measurements"></a>Real-user-metingen

### <a name="what-are-the-benefits-of-using-real-user-measurements"></a>Wat zijn de voordelen van het gebruik Real-user-metingen?

Wanneer u de routeringsmethode voor prestaties gebruikt, kiest Traffic Manager de beste Azure-regio waarmee uw eindgebruiker verbinding kan maken door het bron-IP- en EDNS-clientsubnet te controleren (indien doorgegeven) en deze te controleren op basis van de netwerklatentie-intelligentie die de service onderhoudt. Real-user-metingen breidt dit uit voor uw eindgebruikersbestand door hun ervaring te laten bijdragen aan deze latentietabel en ervoor te zorgen dat deze tabel de netwerken van eindgebruikers overspant van waar uw eindgebruikers verbinding maken met Azure. Dit leidt tot een grotere nauwkeurigheid in de routering van uw eindgebruiker.

### <a name="can-i-use-real-user-measurements-with-non-azure-regions"></a>Kan ik een Real-user-metingen met niet-Azure-regio's?

Real-user-metingen en rapporteert alleen over de latentie om Azure-regio's te bereiken. Als u routering op basis van prestaties gebruikt met eindpunten die worden gehost in niet-Azure-regio's, kunt u nog steeds profiteren van deze functie doordat u meer latentie hebt over de representatieve Azure-regio die u hebt geselecteerd om aan dit eindpunt te worden gekoppeld.

### <a name="which-routing-method-benefits-from-real-user-measurements"></a>Welke routeringsmethode profiteert van Real-user-metingen?

De aanvullende informatie die is verkregen Real-user-metingen zijn alleen van toepassing op profielen die gebruikmaken van de routeringsmethode voor prestaties. De Real-user-metingen is beschikbaar in alle profielen wanneer u deze via de Azure Portal.

### <a name="do-i-need-to-enable-real-user-measurements-each-profile-separately"></a>Moet ik de Real-user-metingen afzonderlijk inschakelen?

Nee, u hoeft deze slechts één keer per abonnement in te stellen en alle gemeten en gerapporteerde latentiegegevens zijn beschikbaar voor alle profielen.

### <a name="how-do-i-turn-off-real-user-measurements-for-my-subscription"></a>Hoe kan ik mijn Real-user-metingen uitschakelen?

U kunt stoppen met het in rekening brengen van kosten Real-user-metingen u stopt met het verzamelen en terugsturen van latentiemetingen vanuit uw clienttoepassing. Wanneer u bijvoorbeeld JavaScript ingesloten in webpagina's meet, kunt u stoppen met het gebruik van deze functie door het JavaScript te verwijderen of door de aanroep uit te schakelen wanneer de pagina wordt weergegeven.

U kunt de Real-user-metingen ook uitschakelen door uw sleutel te verwijderen. Nadat u de sleutel hebt verwijderd, worden alle metingen die naar Traffic Manager met die sleutel verzonden, verwijderd.

### <a name="can-i-use-real-user-measurements-with-client-applications-other-than-web-pages"></a>Kan ik Real-user-metingen met andere clienttoepassingen dan webpagina's?

Ja, Real-user-metingen is ontworpen om gegevens op te nemen die zijn verzameld via verschillende typen clients van eindgebruikers. Deze veelgestelde vragen worden bijgewerkt wanneer nieuwe typen clienttoepassingen worden ondersteund.

### <a name="how-many-measurements-are-made-each-time-my-real-user-measurements-enabled-web-page-is-rendered"></a>Hoeveel metingen worden er gemaakt telkens als Real-user-metingen ingeschakelde webpagina wordt weergegeven?

Wanneer Real-user-metingen wordt gebruikt met de opgegeven JavaScript-meting, resulteert elke paginarendering in zes metingen die worden uitgevoerd. Deze worden vervolgens weer gerapporteerd aan de Traffic Manager service. Er worden kosten in rekening gebracht voor deze functie op basis van het aantal metingen dat wordt gerapporteerd aan Traffic Manager service. Als de gebruiker bijvoorbeeld weg navigeert van uw webpagina terwijl de metingen worden uitgevoerd, maar voordat deze werd gerapporteerd, worden deze metingen niet meegenomen voor factureringsdoeleinden.

### <a name="is-there-a-delay-before-real-user-measurements-script-runs-in-my-webpage"></a>Is er een vertraging voordat Real-user-metingen script wordt uitgevoerd op mijn webpagina?

Nee, er is geen geprogrammeerde vertraging voordat het script wordt aangeroepen.

### <a name="can-i-use-real-user-measurements-with-only-the-azure-regions-i-want-to-measure"></a>Kan ik Real-user-metingen alleen de Azure-regio's gebruiken die ik wil meten?

Nee, telkens wanneer het wordt aangeroepen, meet Real-user-metingen script een set van zes Azure-regio's zoals bepaald door de service. Deze set verandert tussen verschillende aanroepen en wanneer een groot aantal dergelijke aanroepen zich voor doen, omvat de meetdekking verschillende Azure-regio's.

### <a name="can-i-limit-the-number-of-measurements-made-to-a-specific-number"></a>Kan ik het aantal metingen beperken tot een specifiek getal?

De JavaScript-meting is ingesloten op uw webpagina en u hebt volledige controle over wanneer u deze moet starten en stoppen. Zolang de Traffic Manager een aanvraag ontvangt om een lijst met Azure-regio's te meten, wordt een set regio's geretourneerd.

### <a name="can-i-see-the-measurements-taken-by-my-client-application-as-part-of-real-user-measurements"></a>Kan ik de metingen die door mijn clienttoepassing zijn genomen, zien als onderdeel van Real-user-metingen?

Omdat de metingslogica wordt uitgevoerd vanuit uw clienttoepassing, hebt u volledige controle over wat er gebeurt, inclusief het zien van de latentiemetingen. Traffic Manager rapporteert geen geaggregeerde weergave van de metingen die zijn ontvangen onder de sleutel die is gekoppeld aan uw abonnement.

### <a name="can-i-modify-the-measurement-script-provided-by-traffic-manager"></a>Kan ik het meetscript wijzigen dat door de Traffic Manager?

Hoewel u de controle hebt over wat er op uw webpagina is ingesloten, raden we u ten zeerste af om wijzigingen aan te brengen in het meetscript om ervoor te zorgen dat deze de latentie correct meet en rapporteert.

### <a name="will-it-be-possible-for-others-to-see-the-key-i-use-with-real-user-measurements"></a>Kunnen anderen de sleutel zien die ik met de Real-user-metingen?

Wanneer u het meetscript insluit op een webpagina, kunnen anderen het script en uw SCRIPT-sleutel (REAL-USER-METINGEN) zien. Maar het is belangrijk te weten dat deze sleutel verschilt van uw abonnements-id en wordt gegenereerd door Traffic Manager om alleen voor dit doel te worden gebruikt. Als u weet wat uw UWE-sleutel is, is de veiligheid van uw Azure-account niet in gevaar.

### <a name="can-others-abuse-my-rum-key"></a>Kunnen anderen mijn KEY-sleutel misbruiken?

Hoewel het voor anderen mogelijk is om uw sleutel te gebruiken om verkeerde gegevens naar Azure te verzenden, verandert de routering niet door een paar verkeerde metingen, omdat er rekening mee wordt gehouden samen met alle andere metingen die we ontvangen. Als u uw sleutels wilt wijzigen, kunt u de sleutel opnieuw genereren. Op dat moment wordt de oude sleutel verwijderd.

### <a name="do-i-need-to-put-the-measurement-javascript-in-all-my-web-pages"></a>Moet ik de meting JavaScript in al mijn webpagina's zetten?

Real-user-metingen levert meer waarde op naarmate het aantal metingen toeneemt. Dat gezegd hebbende, is het aan u om te bepalen of u deze op al uw webpagina's moet zetten of op een select aantal webpagina's. Het wordt aanbevolen om deze te plaatsen op uw meest bezochte pagina, waar een gebruiker naar verwachting vijf seconden of langer op die pagina blijft.

### <a name="can-information-about-my-end-users-be-identified-by-traffic-manager-if-i-use-real-user-measurements"></a>Kan er aan de Traffic Manager informatie over mijn eindgebruikers worden Real-user-metingen?

Wanneer de opgegeven meting JavaScript wordt gebruikt, heeft Traffic Manager inzicht in het client-IP-adres van de eindgebruiker en het bron-IP-adres van de lokale DNS-resolver die ze gebruiken. Traffic Manager gebruikt het IP-adres van de client pas nadat het is afgekapt om de specifieke eindgebruiker die de metingen heeft verzonden niet te kunnen identificeren.

### <a name="does-the-webpage-measuring-real-user-measurements-need-to-be-using-traffic-manager-for-routing"></a>Moet de webpagina die Real-user-metingen, gebruik maken van Traffic Manager voor routering?

Nee, deze hoeft geen gebruik te Traffic Manager. De routeringszijde van Traffic Manager werkt afzonderlijk van het gedeelte Meting van echte gebruikers en hoewel het een goed idee is om beide in dezelfde web-eigenschap te hebben, hoeven ze dat niet te zijn.

### <a name="do-i-need-to-host-any-service-on-azure-regions-to-use-with-real-user-measurements"></a>Moet ik een service in Azure-regio's hosten voor gebruik met Real-user-metingen?

Nee, u hoeft geen serveronderdeel in Azure te hosten om Real-user-metingen te werken. De afbeelding met één pixel die is gedownload door de meting JavaScript en de service die deze in verschillende Azure-regio's wordt uitgevoerd, wordt gehost en beheerd door Azure. 

### <a name="will-my-azure-bandwidth-usage-increase-when-i-use-real-user-measurements"></a>Neemt het gebruik van mijn Azure-bandbreedte toe wanneer ik Real-user-metingen?

Zoals vermeld in het vorige antwoord, zijn de serveronderdelen van Real-user-metingen eigendom van en worden ze beheerd door Azure. Dit betekent dat uw azure-bandbreedtegebruik niet toeneemt omdat u Real-user-metingen. Dit omvat geen bandbreedtegebruik buiten de kosten die Azure in rekening brengt. We minimaliseren de bandbreedte die wordt gebruikt door slechts één pixelafbeelding te downloaden om de latentie naar een Azure-regio te meten. 

## <a name="traffic-view"></a>Verkeersweergave

### <a name="what-does-traffic-view-do"></a>Wat doet Verkeersweergave doen?

Verkeersweergave is een functie van Traffic Manager waarmee u meer inzicht kunt krijgen in uw gebruikers en hoe hun ervaring is. Het maakt gebruik van de query's die worden ontvangen door Traffic Manager en de netwerklatentie-intelligencetabellen die de service onderhoudt om u het volgende te bieden:

- De regio's waar uw gebruikers verbinding maken met uw eindpunten in Azure.
- Het aantal gebruikers dat verbinding maakt vanuit deze regio's.
- De Azure-regio's waar ze naar worden doorgeleid.
- Hun latentie-ervaring voor deze Azure-regio's.

Deze informatie is beschikbaar voor gebruik via geografische kaart-overlay- en tabelweergaven in de portal en is beschikbaar als onbewerkte gegevens die u kunt downloaden.

### <a name="how-can-i-benefit-from-using-traffic-view"></a>Hoe kan ik profiteren van het gebruik van Verkeersweergave?

Verkeersweergave geeft u een algemeen overzicht van het verkeer dat uw Traffic Manager ontvangen. Het kan met name worden gebruikt om te begrijpen waar uw gebruikersbasis verbinding mee maakt en even belangrijk wat hun gemiddelde latentie-ervaring is. U kunt deze informatie vervolgens gebruiken om gebieden te vinden waarin u zich moet richten, bijvoorbeeld door uw Azure-footprint uit te breiden naar een regio die gebruikers met een lagere latentie kan bedienen. Een ander inzicht dat u kunt afleiden uit het gebruik van Verkeersweergave is het bekijken van de verkeerspatronen naar verschillende regio's, die u op hun beurt kunnen helpen bij het nemen van beslissingen over het vergroten of afnemen van de uitvinding in deze regio's.

### <a name="how-is-traffic-view-different-from-the-traffic-manager-metrics-available-through-azure-monitor"></a>Hoe verschilt Verkeersweergave van de metrische gegevens Traffic Manager beschikbaar zijn via Azure Monitor?

Azure Monitor kunnen worden gebruikt om op aggregatieniveau het verkeer te begrijpen dat door uw profiel en de eindpunten ervan wordt ontvangen. U kunt hiermee ook de status van de eindpunten bijhouden door de resultaten van de statuscontrole weer te geven. Wanneer u meer wilt weten over de ervaring van uw eindgebruiker bij het maken van verbinding met Azure op regionaal niveau, kunt Verkeersweergave gebruiken om dat te bereiken.

### <a name="does-traffic-view-use-edns-client-subnet-information"></a>Gebruikt Verkeersweergave EDNS-clientsubnetgegevens?

De DNS-query's die door Azure Traffic Manager worden gebruikt, houden rekening met ECS-informatie om de nauwkeurigheid van de routering te verhogen. Maar bij het maken van de gegevensset die laat zien waar de gebruikers verbinding maken, gebruikt Verkeersweergave alleen het IP-adres van de DNS-resolver.

### <a name="how-many-days-of-data-does-traffic-view-use"></a>Hoeveel dagen aan gegevens worden Verkeersweergave gebruikt?

Verkeersweergave maakt de uitvoer door de gegevens te verwerken van de zeven dagen vóór de dag vóór wanneer deze door u worden bekeken. Dit is een bewegend venster en de meest recente gegevens worden telkens wanneer u bezoekt gebruikt.

### <a name="how-does-traffic-view-handle-external-endpoints"></a>Hoe worden Verkeersweergave externe eindpunten verwerkt?

Wanneer u externe eindpunten gebruikt die buiten Azure-regio's worden gehost in een Traffic Manager-profiel, kunt u ervoor kiezen om deze toe te staan aan een Azure-regio die een proxy is voor de latentiekenmerken (dit is in feite nodig als u een routeringsmethode voor prestaties gebruikt). Als deze Azure-regio is toegewezen, worden de metrische latentiegegevens van die Azure-regio gebruikt bij het maken van Verkeersweergave uitvoer. Als er geen Azure-regio is opgegeven, zijn de latentiegegevens leeg in de gegevens voor die externe eindpunten.

### <a name="do-i-need-to-enable-traffic-view-for-each-profile-in-my-subscription"></a>Moet ik een Verkeersweergave inschakelen voor elk profiel in mijn abonnement?

Tijdens de preview-Verkeersweergave ingeschakeld op abonnementsniveau. Als onderdeel van de verbeteringen die we hebben aangebracht vóór de algemene beschikbaarheid, kunt u Verkeersweergave nu inschakelen op profielniveau, zodat u deze functie gedetailleerder kunt inschakelen. Standaard wordt Verkeersweergave uitgeschakeld voor een profiel.

>[!NOTE]
>Als u de Verkeersweergave op abonnementsniveau hebt ingeschakeld tijdens de preview-periode, moet u deze nu opnieuw inschakelen voor elk profiel onder dat abonnement.
 
### <a name="how-can-i-turn-off-traffic-view"></a>Hoe kan ik de Verkeersweergave?

U kunt de Verkeersweergave voor elk profiel uitschakelen met behulp van de portal of REST API. 

### <a name="how-does-traffic-view-billing-work"></a>Hoe werkt Verkeersweergave facturering?

Verkeersweergave is gebaseerd op het aantal gegevenspunten dat wordt gebruikt om de uitvoer te maken. Het enige gegevenstype dat momenteel wordt ondersteund, zijn de query's die uw profiel ontvangt. Bovendien wordt u alleen gefactureerd voor de verwerking die is uitgevoerd toen u Verkeersweergave ingeschakeld. Dit betekent dat als u Verkeersweergave gedurende een bepaalde periode in een maand inschakelen en deze tijdens andere perioden uit te schakelen, alleen de gegevenspunten verwerkt terwijl u de functie ingeschakeld tellen voor uw factuur.

## <a name="traffic-manager-endpoints"></a>Traffic Manager-eindpunten

### <a name="can-i-use-traffic-manager-with-endpoints-from-multiple-subscriptions"></a>Kan ik Traffic Manager eindpunten uit meerdere abonnementen gebruiken?

Het gebruik van eindpunten uit meerdere abonnementen is niet mogelijk met Azure Web Apps. Azure Web Apps vereist dat een aangepaste domeinnaam die wordt gebruikt met Web Apps slechts binnen één abonnement wordt gebruikt. Het is niet mogelijk om Web Apps meerdere abonnementen met dezelfde domeinnaam te gebruiken.

Voor andere eindpunttypen is het mogelijk om een Traffic Manager eindpunten uit meer dan één abonnement te gebruiken. In Resource Manager kunnen eindpunten van elk abonnement worden toegevoegd aan Traffic Manager, zolang de persoon die het Traffic Manager-profiel configureert, leestoegang heeft tot het eindpunt. Deze machtigingen kunnen worden verleend met behulp van [op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC).](../role-based-access-control/role-assignments-portal.md) Eindpunten van andere abonnementen kunnen worden toegevoegd met behulp [van Azure PowerShell](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) of de [Azure CLI.](/cli/azure/network/traffic-manager/endpoint#az_network_traffic_manager_endpoint_create)

### <a name="can-i-use-traffic-manager-with-cloud-service-staging-slots"></a>Kan ik Traffic Manager'staging-slots' van de cloudservice gebruiken?

Ja. 'Faseringssleuven' van de cloudservice kunnen worden geconfigureerd in Traffic Manager als externe eindpunten. Statuscontroles worden nog steeds in rekening gebracht tegen het tarief voor Azure-eindpunten.

### <a name="does-traffic-manager-support-ipv6-endpoints"></a>Ondersteunt Traffic Manager IPv6-eindpunten?

Traffic Manager biedt momenteel geen adresseerbare IPv6-naamservers. IPv6 Traffic Manager kunnen echter nog steeds worden gebruikt door IPv6-clients die verbinding maken met IPv6-eindpunten. Een client maakt geen DNS-aanvragen rechtstreeks naar Traffic Manager. In plaats daarvan gebruikt de client een recursieve DNS-service. Een IPv6-client verzendt aanvragen naar de recursieve DNS-service via IPv6. Vervolgens moet de recursieve service contact kunnen opnemen met de Traffic Manager servers met behulp van IPv4.

Traffic Manager reageert met de DNS-naam of het IP-adres van het eindpunt. Er zijn twee opties ter ondersteuning van een IPv6-eindpunt. U kunt het eindpunt toevoegen als een DNS-naam met een bijbehorende AAAA-record. Traffic Manager controleert dat eindpunt en retournt het als een CNAME-recordtype in het query-antwoord. U kunt dat eindpunt ook rechtstreeks toevoegen met behulp van het IPv6-adres en Traffic Manager retourneren een AAAA-typerecord in het query-antwoord.

### <a name="can-i-use-traffic-manager-with-more-than-one-web-app-in-the-same-region"></a>Kan ik Traffic Manager met meer dan één web-app in dezelfde regio?

Normaal gesproken Traffic Manager gebruikt om verkeer om te leiden naar toepassingen die zijn geïmplementeerd in verschillende regio's. Het kan echter ook worden gebruikt wanneer een toepassing meer dan één implementatie in dezelfde regio heeft. De Traffic Manager Azure-eindpunten staan niet toe dat meer dan één web-app-eindpunt uit dezelfde Azure-regio wordt toegevoegd aan hetzelfde Traffic Manager profiel.

### <a name="how-do-i-move-my-traffic-manager-profiles-azure-endpoints-to-a-different-resource-group-or-subscription"></a>Hoe kan ik de Azure Traffic Manager-eindpunten van mijn profiel verplaatsen naar een andere resourcegroep of een ander abonnement?

Azure-eindpunten die aan een Traffic Manager zijn gekoppeld, worden bij te houden met behulp van hun resource-ID's. Wanneer een Azure-resource die wordt gebruikt als eindpunt (bijvoorbeeld een openbaar IP-adres, klassieke cloudservice, web-app of een ander Traffic Manager-profiel dat op een geneste manier wordt gebruikt) wordt verplaatst naar een andere resourcegroep of een ander abonnement, wordt de resource-id gewijzigd. In dit scenario moet u het Traffic Manager bijwerken door eerst de eindpunten te verwijderen en vervolgens weer toe te voegen aan het profiel.

## <a name="traffic-manager-endpoint-monitoring"></a>Eindpuntbewaking in Traffic Manager

### <a name="is-traffic-manager-resilient-to-azure-region-failures"></a>Is Traffic Manager bestand tegen fouten in Azure-regio's?

Traffic Manager is een belangrijk onderdeel van de levering van toepassingen met hoge beschikbare gegevens in Azure.
Voor hoge beschikbaarheid moeten Traffic Manager een uitzonderlijk hoge beschikbaarheid hebben en bestand zijn tegen regionale storingen.

Standaard zijn Traffic Manager-onderdelen bestand tegen een volledige storing in een Azure-regio. Deze tolerantie is van toepassing op Traffic Manager onderdelen: de DNS-naamservers, de API, de opslaglaag en de eindpuntbewakingsservice.

In het onwaarschijnlijke geval van een storing in een hele Azure-regio, Traffic Manager normaal functioneren. Toepassingen die in meerdere Azure-regio's zijn geïmplementeerd, kunnen afhankelijk zijn Traffic Manager verkeer om te leiden naar een beschikbaar exemplaar van hun toepassing.

### <a name="how-does-the-choice-of-resource-group-location-affect-traffic-manager"></a>Wat is de invloed van de keuze van de locatie van de resourcegroep Traffic Manager?

Traffic Manager is één wereldwijde service. Het is niet regionaal. De keuze van de locatie van de resourcegroep maakt geen verschil Traffic Manager profielen die in die resourcegroep zijn geïmplementeerd.

Azure Resource Manager moeten alle resourcegroepen een locatie opgeven, waarmee de standaardlocatie wordt bepaald voor resources die in die resourcegroep zijn geïmplementeerd. Wanneer u een Traffic Manager maakt, wordt het gemaakt in een resourcegroep. Alle Traffic Manager gebruiken **globaal** als locatie, wat de standaardinstelling van de resourcegroep overschrijven.

### <a name="how-do-i-determine-the-current-health-of-each-endpoint"></a>Hoe kan ik de huidige status van elk eindpunt bepalen?

De huidige bewakingsstatus van elk eindpunt wordt, naast het algehele profiel, weergegeven in de Azure Portal. Deze informatie is ook beschikbaar via de Traffic Monitor [REST API,](/rest/api/trafficmanager/) [PowerShell-cmdlets](/powershell/module/az.trafficmanager)en [platformoverschrijdende Azure CLI.](/cli/azure/install-classic-cli)

U kunt ook Azure Monitor om de status van uw eindpunten bij te houden en een visuele weergave van deze eindpunten te bekijken. Zie de Azure Monitoring-documentatie Azure Monitor meer informatie over het gebruik [van Azure Monitor.](../azure-monitor/data-platform.md)

### <a name="can-i-monitor-https-endpoints"></a>Kan ik HTTPS-eindpunten bewaken?

Ja. Traffic Manager ondersteunt probing via HTTPS. Configureer **HTTPS** als het protocol in de bewakingsconfiguratie.

Traffic Manager kan geen certificaatvalidatie bieden, waaronder:

* Certificaten aan de serverzijde worden niet gevalideerd
* SNI-certificaten aan de serverzijde worden niet gevalideerd
* Clientcertificaten worden niet ondersteund

### <a name="do-i-use-an-ip-address-or-a-dns-name-when-adding-an-endpoint"></a>Gebruik ik een IP-adres of een DNS-naam bij het toevoegen van een eindpunt?

Traffic Manager ondersteunt het toevoegen van eindpunten op drie manieren om hier naar te verwijzen: als een DNS-naam, als een IPv4-adres en als een IPv6-adres. Als het eindpunt wordt toegevoegd als een IPv4- of IPv6-adres, is het query-antwoord respectievelijk van recordtype A of AAAA. Als het eindpunt is toegevoegd als dns-naam, is het query-antwoord van het recordtype CNAME. Het toevoegen van eindpunten als IPv4- of IPv6-adres is alleen toegestaan als het eindpunt van het type **Extern is.**
Alle routeringsmethoden en bewakingsinstellingen worden ondersteund door de drie adresseringstypen voor eindpunten.

### <a name="what-types-of-ip-addresses-can-i-use-when-adding-an-endpoint"></a>Welke typen IP-adressen kan ik gebruiken bij het toevoegen van een eindpunt?

Traffic Manager kunt u IPv4- of IPv6-adressen gebruiken om eindpunten op te geven. Hieronder vindt u enkele beperkingen:

- Adressen die overeenkomen met gereserveerde privé-IP-adresruimten zijn niet toegestaan. Deze adressen omvatten de adressen die worden genoemd in RFC 1918, RFC 6890, RFC 5737, RFC 3068, RFC 2544 en RFC 5771
- Het adres mag geen poortnummers bevatten (u kunt opgeven welke poorten moeten worden gebruikt in de profielconfiguratie-instellingen)
- Geen twee eindpunten in hetzelfde profiel kunnen hetzelfde doel-IP-adres hebben

### <a name="can-i-use-different-endpoint-addressing-types-within-a-single-profile"></a>Kan ik verschillende adresseringstypen voor eindpunten binnen één profiel gebruiken?

Nee, Traffic Manager biedt u niet de mogelijkheid om adresseringstypen voor eindpunten binnen een profiel te combineren, met uitzondering van een profiel met een routeringstype met meerdere waardes waar u IPv4- en IPv6-adresseringstypen kunt combineren

### <a name="what-happens-when-an-incoming-querys-record-type-is-different-from-the-record-type-associated-with-the-addressing-type-of-the-endpoints"></a>Wat gebeurt er wanneer het recordtype van een binnenkomende query verschilt van het recordtype dat is gekoppeld aan het adresseringstype van de eindpunten?

Wanneer een query wordt ontvangen voor een profiel, zoekt Traffic Manager eerst het eindpunt dat moet worden geretourneerd volgens de opgegeven routeringsmethode en de status van de eindpunten. Vervolgens wordt het recordtype dat is aangevraagd in de binnenkomende query en het recordtype dat aan het eindpunt is gekoppeld, weergegeven voordat een antwoord wordt weergegeven op basis van de onderstaande tabel.

Voor profielen met een andere routeringsmethode dan MultiValue:

|Inkomende queryaanvraag|     Eindpunttype|     Antwoord gegeven|
|--|--|--|
|ALLE |    A / AAAA / CNAME |    Doel-eindpunt| 
|A |    A/CNAME |    Doel-eindpunt|
|A |    AAAA |    NODATA |
|AAAA |    AAAA /CNAME |    Doel-eindpunt|
|AAAA |    A |    NODATA |
|CNAME |    CNAME |    Doel-eindpunt|
|CNAME     |A/AAAA |    NODATA |
|

Voor profielen waarbij routeringsmethode is ingesteld op MultiValue:

|Inkomende queryaanvraag|     Eindpunttype |    Antwoord gegeven|
|--|--|--|
|ALLE |    Combinatie van A en AAAA |    Doel-eindpunten|
|A |    Combinatie van A en AAAA |    Alleen doel-eindpunten van het type A|
|AAAA    |Combinatie van A en AAAA|     Alleen doel-eindpunten van het type AAAA|
|CNAME |    Combinatie van A en AAAA |    NODATA |

### <a name="can-i-use-a-profile-with-ipv4--ipv6-addressed-endpoints-in-a-nested-profile"></a>Kan ik een profiel gebruiken met eindpunten met IPv4-/IPv6-adressen in een genest profiel?

Ja, u kunt met uitzondering dat een profiel van het type MultiValue geen bovenliggend profiel mag zijn in een geneste profielset.

### <a name="i-stopped-an-web-application-endpoint-in-my-traffic-manager-profile-but-i-am-not-receiving-any-traffic-even-after-i-restarted-it-how-can-i-fix-this"></a>Ik heb een eindpunt voor een webtoepassing gestopt in Traffic Manager-profiel, maar ik krijg geen verkeer, zelfs niet nadat ik het opnieuw heb opgestart. Hoe kan ik dit oplossen?

Wanneer een eindpunt van een Azure-webtoepassing wordt gestopt Traffic Manager de statuscontrole gestopt en worden de statuscontroles pas opnieuw gestart nadat is gedetecteerd dat het eindpunt opnieuw is opgestart. Om deze vertraging te voorkomen, schakelt u dat eindpunt uit en schakelt u het opnieuw in het Traffic Manager nadat u het eindpunt opnieuw hebt opgestart.

### <a name="can-i-use-traffic-manager-even-if-my-application-does-not-have-support-for-http-or-https"></a>Kan ik een Traffic Manager zelfs als mijn toepassing geen ondersteuning voor HTTP of HTTPS heeft?

Ja. U kunt TCP opgeven als het bewakingsprotocol en Traffic Manager kunt een TCP-verbinding initiëren en wachten op een reactie van het eindpunt. Als het eindpunt op de verbindingsaanvraag reageert met een antwoord om de verbinding tot stand te brengen, wordt dat eindpunt binnen de time-outperiode gemarkeerd als in orde.

### <a name="what-specific-responses-are-required-from-the-endpoint-when-using-tcp-monitoring"></a>Welke specifieke antwoorden zijn vereist vanaf het eindpunt bij het gebruik van TCP-bewaking?

Wanneer TCP-bewaking wordt gebruikt, Traffic Manager een TCP-handshake in drie punten gestart door een SYN-aanvraag te verzenden naar het eindpunt op de opgegeven poort. Vervolgens wacht het op een SYN-ACK-antwoord van het eindpunt voor een bepaalde tijd (opgegeven in de time-outinstellingen).

- Als een SYN-ACK-antwoord wordt ontvangen binnen de time-outperiode die is opgegeven in de bewakingsinstellingen, wordt dat eindpunt als in orde beschouwd. Een FIN- of FIN-ACK is het verwachte antwoord van de Traffic Manager een socket regelmatig wordt beëindigd.
- Als er na de opgegeven time-out een SYN-ACK-antwoord wordt ontvangen, reageert de Traffic Manager met een RST om de verbinding opnieuw in te stellen.

### <a name="how-fast-does-traffic-manager-move-my-users-away-from-an-unhealthy-endpoint"></a>Hoe snel worden Traffic Manager gebruikers van een slecht eindpunt verwijderd?

Traffic Manager biedt meerdere instellingen die u kunnen helpen om het failovergedrag van uw Traffic Manager als volgt te beheren:

- U kunt opgeven dat de Traffic Manager eindpunten vaker test door het testinterval in te stellen op 10 seconden. Dit zorgt ervoor dat een eindpunt dat niet in orde is zo snel mogelijk kan worden gedetecteerd. 
- U kunt opgeven hoe lang moet worden gewacht voordat er een time-out van een statuscontroleaanvraag is (minimale time-outwaarde is 5 sec).
- U kunt opgeven hoeveel fouten er kunnen optreden voordat het eindpunt wordt gemarkeerd als niet in orde. Deze waarde kan laag zijn als 0. In dat geval wordt het eindpunt gemarkeerd als niet in orde zodra de eerste statuscontrole is mislukt. Het gebruik van de minimumwaarde 0 voor het getolereerde aantal fouten kan er echter toe leiden dat eindpunten uit de roulatie worden genomen als gevolg van tijdelijke problemen die zich kunnen voordoen op het moment van de probing.
- U kunt opgeven dat de time-to-live (TTL) voor het DNS-antwoord zo laag als 0. Dit betekent dat DNS-resolvers het antwoord niet in de cache kunnen cachen en dat elke nieuwe query een antwoord krijgt dat de meest recente statusinformatie bevat die de Traffic Manager heeft.

Met behulp van deze instellingen Traffic Manager failovers binnen tien seconden nadat een eindpunt een slechte status heeft en er een DNS-query wordt uitgevoerd op basis van het bijbehorende profiel.

### <a name="how-can-i-specify-different-monitoring-settings-for-different-endpoints-in-a-profile"></a>Hoe kan ik verschillende bewakingsinstellingen opgeven voor verschillende eindpunten in een profiel?

Traffic Manager bewakingsinstellingen zijn per profielniveau. Als u een andere bewakingsinstelling voor slechts één eindpunt wilt gebruiken, kan dit worden gedaan door dat eindpunt te gebruiken als een [genest](traffic-manager-nested-profiles.md) profiel waarvan de bewakingsinstellingen verschillen van het bovenliggende profiel.

### <a name="how-can-i-assign-http-headers-to-the-traffic-manager-health-checks-to-my-endpoints"></a>Hoe kan ik HTTP-headers toewijzen aan Traffic Manager statuscontroles aan mijn eindpunten?

Traffic Manager kunt u aangepaste headers opgeven in de HTTP(S)-statuscontroles die worden gestart naar uw eindpunten. Als u een aangepaste header wilt opgeven, kunt u dat doen op profielniveau (van toepassing op alle eindpunten) of opgeven op eindpuntniveau. Als een header op beide niveaus is gedefinieerd, wordt het profielniveau één overschreven door de header die is opgegeven op eindpuntniveau.
Een veelvoorkomende toepassing hiervoor is het opgeven van hostheaders, zodat Traffic Manager-aanvragen correct kunnen worden gerouteerd naar een eindpunt dat wordt gehost in een omgeving met meerdere tenants. Een ander gebruiksgeval hiervoor is het identificeren Traffic Manager aanvragen uit de HTTP(S)-aanvraaglogboeken van een eindpunt

### <a name="what-host-header-do-endpoint-health-checks-use"></a>Welke hostheader wordt gebruikt voor statuscontroles van eindpunten?

Als er geen aangepaste hostheaderinstelling is opgegeven, is de hostheader die wordt gebruikt door Traffic Manager de DNS-naam van het eindpuntdoel dat in het profiel is geconfigureerd, als dat beschikbaar is.

### <a name="what-are-the-ip-addresses-from-which-the-health-checks-originate"></a>Wat zijn de IP-adressen van waaruit de statuscontroles afkomstig zijn?

Klik [hier](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) voor informatie over het ophalen van de lijsten met IP-adressen waaruit Traffic Manager statuscontroles afkomstig kunnen zijn. U kunt REST API, Azure CLI of Azure PowerShell de meest recente lijst ophalen. Controleer de VERMELDE IP-adressen om ervoor te zorgen dat binnenkomende verbindingen van deze IP-adressen zijn toegestaan op de eindpunten om de status ervan te controleren.

Voorbeeld van het gebruik Azure PowerShell:

```azurepowershell-interactive
$serviceTags = Get-AzNetworkServiceTag -Location eastus
$result = $serviceTags.Values | Where-Object { $_.Name -eq "AzureTrafficManager" }
$result.Properties.AddressPrefixes
```

> [!NOTE]
> Openbare IP-adressen kunnen zonder kennisgeving worden gewijzigd. Zorg ervoor dat u de meest recente informatie op haalt met behulp van de Service Tag Discovery-API of het downloadbare JSON-bestand.

### <a name="how-many-health-checks-to-my-endpoint-can-i-expect-from-traffic-manager"></a>Hoeveel statuscontroles naar mijn eindpunt kan ik verwachten van Traffic Manager?

Het aantal Traffic Manager statuscontroles dat uw eindpunt bereikt, is afhankelijk van het volgende:

- de waarde die u hebt ingesteld voor het bewakingsinterval (een kleiner interval betekent dat er binnen een bepaalde periode meer aanvragen op uw eindpunt komen).
- het aantal locaties waar de statuscontroles vandaan komen (de IP-adressen van waar u kunt verwachten dat deze controles worden vermeld in de voorgaande veelgestelde vragen).

### <a name="how-can-i-get-notified-if-one-of-my-endpoints-goes-down"></a>Hoe kan ik een melding ontvangen als een van mijn eindpunten uitgaat?

Een van de metrische gegevens die door Traffic Manager is de status van eindpunten in een profiel. U kunt dit zien als een aggregatie van alle eindpunten binnen een profiel (bijvoorbeeld 75% van uw eindpunten is in orde) of, op eindpuntniveau. Traffic Manager metrische gegevens worden via Azure Monitor en u [](../azure-monitor/alerts/alerts-metric.md) kunt de waarschuwingsmogelijkheden gebruiken om meldingen te ontvangen wanneer er een wijziging is in de status van uw eindpunt. Zie Metrische gegevens en [Traffic Manager waarschuwingen voor meer informatie.](traffic-manager-metrics-alerts.md)  

## <a name="traffic-manager-nested-profiles"></a>Traffic Manager profielen maken

### <a name="how-do-i-configure-nested-profiles"></a>Hoe kan ik geneste profielen configureren?

Geneste Traffic Manager kunnen worden geconfigureerd met zowel de Azure Resource Manager als de klassieke Azure REST API's, Azure PowerShell-cmdlets en platformoverschrijdende Azure CLI-opdrachten. Ze worden ook ondersteund via de nieuwe Azure Portal.

### <a name="how-many-layers-of-nesting-does-traffic-manger-support"></a>Hoeveel lagen nesten biedt Traffic Manger ondersteuning?

U kunt profielen tot 10 niveaus diep nesten. Lussen zijn niet toegestaan.

### <a name="can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile"></a>Kan ik andere eindpunttypen combineren met geneste onderliggende profielen, in hetzelfde Traffic Manager profiel?

Ja. Er zijn geen beperkingen voor het combineren van eindpunten van verschillende typen binnen een profiel.

### <a name="how-does-the-billing-model-apply-for-nested-profiles"></a>Hoe is het factureringsmodel van toepassing op geneste profielen?

Het gebruik van geneste profielen heeft geen negatieve invloed op de prijzen.

Traffic Manager facturering bestaat uit twee onderdelen: statuscontroles van eindpunten en miljoenen DNS-query's

* Statuscontroles voor eindpunten: er worden geen kosten in rekening brengen voor een onderliggend profiel wanneer het is geconfigureerd als een eindpunt in een bovenliggend profiel. Bewaking van de eindpunten in het onderliggende profiel wordt op de gebruikelijke manier gefactureerd.
* DNS-query's: elke query wordt slechts één keer geteld. Een query op een bovenliggend profiel die een eindpunt van een onderliggend profiel retourneert, wordt alleen meegetelde voor het bovenliggende profiel.

Zie de pagina met prijzen Traffic Manager [meer informatie.](https://azure.microsoft.com/pricing/details/traffic-manager/)

### <a name="is-there-a-performance-impact-for-nested-profiles"></a>Zijn er gevolgen voor de prestaties van geneste profielen?

Nee. Er zijn geen gevolgen voor de prestaties bij het gebruik van geneste profielen.

De Traffic Manager-naamservers doorkruisen de profielhiërarchie intern bij het verwerken van elke DNS-query. Een DNS-query naar een bovenliggend profiel kan een DNS-antwoord met een eindpunt van een onderliggend profiel ontvangen. Er wordt één CNAME-record gebruikt, ongeacht of u één profiel of geneste profielen gebruikt. U hoeft geen CNAME-record te maken voor elk profiel in de hiërarchie.

### <a name="how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile"></a>Hoe berekent Traffic Manager de status van een genest eindpunt in een bovenliggend profiel?

Het bovenliggende profiel voert geen statuscontroles rechtstreeks uit op het onderliggende profiel. In plaats daarvan wordt de status van de eindpunten van het onderliggende profiel gebruikt om de algehele status van het onderliggende profiel te berekenen. Deze informatie wordt doorgegeven aan de geneste profielhiërarchie om de status van het geneste eindpunt te bepalen. Het bovenliggende profiel gebruikt deze geaggregeerde status om te bepalen of het verkeer naar het onderliggende profiel kan worden omgeleid.

De volgende tabel beschrijft het gedrag van Traffic Manager statuscontroles voor een genest eindpunt.

| Monitorstatus onderliggend profiel | Status van bovenliggend eindpunt bewaken | Notities |
| --- | --- | --- |
| Uitgeschakeld. Het onderliggende profiel is uitgeschakeld. |Gestopt |De status van het bovenliggende eindpunt is Gestopt, niet Uitgeschakeld. De status Uitgeschakeld is gereserveerd om aan te geven dat u het eindpunt in het bovenliggende profiel hebt uitgeschakeld. |
| Gedegradeerd. Ten minste één onderliggend profiel-eindpunt heeft de status Gedegradeerd. |Online: het aantal online eindpunten in het onderliggende profiel is ten minste de waarde van MinChildEndpoints.<BR>CheckingEndpoint: het aantal Online- en CheckingEndpoint-eindpunten in het onderliggende profiel is ten minste de waarde van MinChildEndpoints.<BR>Gedegradeerd: anders. |Verkeer wordt doorgeleid naar een eindpunt van statusControlepunt. Als MinChildEndpoints te hoog is ingesteld, is het eindpunt altijd gedegradeerd. |
| Online. Ten minste één onderliggend profiel-eindpunt heeft de status Online. Geen enkel eindpunt heeft de status Gedegradeerd. |Zie hierboven. | |
| CheckingEndpoints. Ten minste één onderliggend profiel-eindpunt is 'CheckingEndpoint'. Er zijn geen eindpunten 'Online' of 'Gedegradeerd' |Hetzelfde als hierboven. | |
| Inactief. Alle eindpunten van het onderliggende profiel zijn Uitgeschakeld of Gestopt, of dit profiel heeft geen eindpunten. |Gestopt | |

## <a name="next-steps"></a>Volgende stappen:

- Meer informatie over Traffic Manager [eindpuntbewaking en automatische failover](../traffic-manager/traffic-manager-monitoring.md).
- Meer informatie over Traffic Manager [routeringsmethoden voor verkeer.](../traffic-manager/traffic-manager-routing-methods.md)