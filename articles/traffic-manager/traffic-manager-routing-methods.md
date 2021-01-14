---
title: Azure Traffic Manager-routerings methoden voor verkeer
description: In deze artikelen vindt u informatie over de verschillende routerings methoden voor verkeer die worden gebruikt door Traffic Manager
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: duau
ms.openlocfilehash: 0eb49f3c2acc31cba7b245995cf3bcb579113e4c
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98183810"
---
# <a name="traffic-manager-routing-methods"></a>Methoden voor het doorsturen van Traffic Manager

Azure Traffic Manager ondersteunt zes verkeers routerings methoden om te bepalen hoe netwerk verkeer wordt gerouteerd naar de verschillende service-eind punten. Voor elk profiel past Traffic Manager de routerings methode voor verkeer toe die eraan is gekoppeld aan elke DNS-query die wordt ontvangen. De routerings methode voor verkeer bepaalt welk eind punt wordt geretourneerd in het DNS-antwoord.

De volgende methoden voor verkeers routering zijn beschikbaar in Traffic Manager:

* **[Prioriteit](#priority-traffic-routing-method):** Selecteer **prioriteit** wanneer u een primair service-eind punt voor al het verkeer wilt gebruiken en back-ups voor het geval de primaire of de back-eindpunten niet beschikbaar zijn.
* **[Gewogen](#weighted):** Selecteer **gewogen** wanneer u verkeer wilt distribueren over een set eind punten, hetzij gelijkmatig als op basis van gewichten, dat u definieert.
* **[Prestaties](#performance):** Selecteer **prestaties** wanneer u eind punten op verschillende geografische locaties hebt en u wilt dat eind gebruikers het ' dichtstbijgelegen ' eind punt gebruiken in termen van de laagste netwerk latentie.
* **[Geografisch](#geographic):** Selecteer **geografisch** zodat gebruikers naar specifieke eind punten (Azure, extern of genest) worden geleid op basis van de geografische locatie waarvan hun DNS-query afkomstig is. Dit biedt Traffic Manager klanten de mogelijkheid om scenario's in te scha kelen waarbij de geografische regio van een gebruiker wordt gewerkt en deze op basis van het belang rijk is. Voor beelden zijn het voldoen aan data soevereiniteit-mandaten, lokalisatie van inhoud & gebruikers ervaring en het meten van verkeer van verschillende regio's.
* **[Meerdere waarden](#multivalue):** Selecteer **meerdere waarden** voor Traffic Manager profielen die alleen IPv4/IPv6-adressen kunnen hebben als eind punten. Wanneer er een query voor dit profiel wordt ontvangen, worden alle in orde zijnde eind punten geretourneerd.
* **[Subnet](#subnet):** Selecteer **subnet** Traffic-routerings methode om sets van IP-adresbereiken voor eind gebruikers toe te wijzen aan een bepaald eind punt binnen een Traffic Manager profiel. Wanneer een aanvraag wordt ontvangen, wordt het eind punt geretourneerd dat is toegewezen aan het bron-IP-adres van de aanvraag. 


Alle Traffic Manager profielen bevatten bewaking van de eindpunt status en automatische endpoint-failover. Zie [Traffic Manager endpoint monitoring](traffic-manager-monitoring.md)voor meer informatie. Eén Traffic Manager profiel kan slechts één routerings methode voor verkeer gebruiken. U kunt op elk gewenst moment een andere methode voor verkeers routering voor uw profiel selecteren. Wijzigingen worden binnen één minuut toegepast en er wordt geen downtime in rekening gebracht. Verkeers routerings methoden kunnen worden gecombineerd met geneste Traffic Manager profielen. Met nesten kunt u geavanceerde en flexibele verkeer routerings configuraties maken die voldoen aan de behoeften van grotere, complexe toepassingen. Zie [geneste Traffic Manager profielen](traffic-manager-nested-profiles.md)voor meer informatie.

## <a name="priority-traffic-routing-method"></a>Prioriteits verkeer: routerings methode

Een organisatie wil vaak betrouw baarheid bieden voor de services door een of meer back-upservices te implementeren voor het geval de primaire service uitvalt. Met de routerings methode ' Priority ' kunnen Azure-klanten het failover-patroon eenvoudig implementeren.

![Azure Traffic Manager ' Priority ' Traffic-routerings methode](media/traffic-manager-routing-methods/priority.png)

Het Traffic Manager-profiel bevat een lijst met service-eindpunten met prioriteit. Standaard verzendt Traffic Manager al het verkeer naar het primaire eindpunt (met de hoogste prioriteit). Als het primaire eindpunt niet beschikbaar is, routeert Traffic Manager het verkeer naar het tweede eindpunt. Als zowel het primaire als het secundaire eindpunt niet beschikbaar is, gaat het verkeer naar het derde, enzovoort. De beschikbaarheid van het eindpunt is gebaseerd op de geconfigureerde status (ingeschakeld of uitgeschakeld) en de voortdurende eindpuntcontrole.

### <a name="configuring-endpoints"></a>Eind punten configureren

Met Azure Resource Manager configureert u de eindpunt prioriteit expliciet met behulp van de eigenschap Priority voor elk eind punt. Deze eigenschap is een waarde tussen 1 en 1000. Lagere waarden vertegenwoordigen een hogere prioriteit. Eind punten kunnen geen prioriteits waarden delen. Het instellen van de eigenschap is optioneel. Als u dit weglaat, wordt een standaard prioriteit op basis van de eindpunt volgorde gebruikt.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Gewogen verkeer-routerings methode
Met de methode gewogen verkeers routering kunt u verkeer gelijkmatig distribueren of een vooraf gedefinieerde wegings factor gebruiken.

![Azure Traffic Manager ' gewogen ' Traffic-routerings methode](media/traffic-manager-routing-methods/weighted.png)

In de routerings methode gewogen verkeer, wijst u een gewicht toe aan elk eind punt in de configuratie van het Traffic Manager-profiel. Het gewicht is een geheel getal tussen 1 en 1000. Deze parameter is optioneel. Als u dit weglaat, gebruikt Traffic managers een standaard gewicht van ' 1 '. Het hogere gewicht, des te hoger de prioriteit.

Voor elke ontvangen DNS-query kiest Traffic Manager willekeurig een beschikbaar eindpunt. De kans dat een eindpunt wordt gekozen, is gebaseerd op het gewicht dat is toegewezen aan alle beschikbare eindpunten. Het gebruik van hetzelfde gewicht over alle eind punten resulteert in een gelijkmatige verdeling van verkeer. Als u een hoger of lager gewicht gebruikt voor specifieke eind punten, worden deze eind punten meer of minder vaak geretourneerd in de DNS-antwoorden.

De methode weighted maakt enkele handige scenario's mogelijk:

* Geleidelijke upgrade van de toepassing: wijs een percentage van het verkeer toe om naar een nieuw eind punt te sturen en verhoog het verkeer geleidelijk tot 100%.
* Toepassings migratie naar Azure: Maak een profiel met zowel Azure als externe eind punten. Pas het gewicht van de eind punten aan om de voor keur te geven aan de nieuwe eind punten.
* Cloud-bursting voor extra capaciteit: een on-premises implementatie in de Cloud snel uitbreiden door deze achter een Traffic Manager profiel te plaatsen. Wanneer u extra capaciteit nodig hebt in de Cloud, kunt u meer eind punten toevoegen of inschakelen en opgeven welk gedeelte van verkeer naar elk eind punt gaat.

Naast het gebruik van de Azure Portal, kunt u gewichten configureren met behulp van Azure PowerShell, CLI en de REST Api's.

Het is belang rijk om te begrijpen dat DNS-antwoorden in de cache worden opgeslagen door clients en door de recursieve DNS-servers die de clients gebruiken om DNS-namen om te zetten. Deze cache kan invloed hebben op de verdeling van gewogen verkeer. Wanneer het aantal clients en recursieve DNS-servers groot is, werkt de verkeers distributie als verwacht. Wanneer het aantal clients of recursieve DNS-servers echter klein is, kan de verkeers distributie aanzienlijk worden verduidelijkt in de cache.

Veelvoorkomende use-cases zijn:

* Ontwikkel-en test omgevingen
* Communicatie tussen toepassingen
* Toepassingen die gericht zijn op een smalle gebruikers basis die een gemeen schappelijke recursieve DNS-infra structuur delen (bijvoorbeeld werk nemers van het bedrijf die verbinding maken via een proxy)

Deze DNS-cache-effecten zijn gebruikelijk voor alle op DNS gebaseerde Traffic-routerings systemen, niet alleen voor Azure Traffic Manager. In sommige gevallen kan het expliciet verwijderen van de DNS-cache een tijdelijke oplossing bieden. In andere gevallen is het mogelijk dat een alternatieve methode voor verkeers routering het meest geschikt is.

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>Prestatie verkeer-routerings methode

Het implementeren van eind punten op twee of meer locaties over de hele wereld kan de reactie snelheid van veel toepassingen verbeteren door verkeer te routeren naar de locatie die het dichtst bij u ligt. De methode voor het routerings verkeer van prestaties biedt deze mogelijkheid.

![Verkeer van de Azure Traffic Manager-routerings methode voor prestaties](media/traffic-manager-routing-methods/performance.png)

Het dichtstbijgelegen eind punt is niet noodzakelijkerwijs het dichtst bij de geografische afstand gemeten. In plaats daarvan bepaalt de routerings methode voor prestaties het dichtstbijzijnde eind punt door netwerk latentie te meten. Traffic Manager houdt een tabel met Internet latentie bij om de retour tijd tussen IP-adresbereiken en elk Azure-Data Center bij te houden.

Traffic Manager zoekt het bron-IP-adres van de binnenkomende DNS-aanvraag in de tabel Internet latentie. Traffic Manager kiest vervolgens een beschikbaar eind punt in het Azure-Data Center met de laagste latentie voor dat IP-adres bereik en retourneert dat eind punt in het DNS-antwoord.

Zoals uitgelegd in de werking van [Traffic Manager](traffic-manager-how-it-works.md), ontvangt Traffic Manager geen DNS-query's rechtstreeks van clients. DNS-query's zijn afkomstig uit de recursieve DNS-service die door de clients zijn geconfigureerd om te worden gebruikt. Daarom is het IP-adres dat wordt gebruikt om het ' dichtstbijgelegen ' eind punt te bepalen, niet het IP-adres van de client, maar het is het IP-adres van de recursieve DNS-service. In de praktijk is dit IP-adres een goede proxy voor de client.


Traffic Manager de Internet latentie tabel regel matig bijwerkt om wijzigingen aan te brengen in het wereld wijde Internet en nieuwe Azure-regio's. De prestaties van toepassingen variëren echter op basis van real-time variaties in de belasting via internet. Prestatie verkeer: route ring bewaakt de belasting van een bepaald service-eind punt niet. Als een eind punt echter niet beschikbaar is, wordt dit door Traffic Manager niet opgenomen in de antwoorden op DNS-query's.


Punten om te noteren:

* Als uw profiel meerdere eind punten in dezelfde Azure-regio bevat, verdeelt Traffic Manager verkeer gelijkmatig over de beschik bare eind punten in die regio. Als u de voor keur geeft aan een andere distributie van verkeer binnen een regio, kunt u [geneste Traffic Manager profielen](traffic-manager-nested-profiles.md)gebruiken.
* Als alle ingeschakelde eind punten in de dichtstbijzijnde Azure-regio worden gedegradeerd, Traffic Manager verkeer verplaatsen naar de eind punten in de dichtstbijzijnde Azure-regio. Als u een voorkeurs reeks failover wilt definiëren, gebruikt u [geneste Traffic Manager profielen](traffic-manager-nested-profiles.md).
* Wanneer u de routerings methode voor prestatie verkeer met externe eind punten of geneste eind punten gebruikt, moet u de locatie van deze eind punten opgeven. Kies de Azure-regio die het dichtst bij uw implementatie ligt. Deze locaties zijn de waarden die worden ondersteund door de tabel Internet latentie.
* Het algoritme waarmee het eind punt wordt gekozen, is deterministisch. Herhaalde DNS-query's van dezelfde client worden omgeleid naar hetzelfde eind punt. Normaal gesp roken gebruiken clients verschillende recursieve DNS-servers wanneer ze onderweg zijn. De client kan worden doorgestuurd naar een ander eind punt. Route ring kan ook worden beïnvloed door updates voor de Internet latentie tabel. Daarom garandeert de routerings methode voor prestatie verkeer niet dat een client altijd wordt doorgestuurd naar hetzelfde eind punt.
* Wanneer de tabel Internet latentie wordt gewijzigd, kunt u zien dat sommige clients worden omgeleid naar een ander eind punt. Deze wijziging van de route ring is nauw keuriger op basis van de huidige latentie gegevens. Deze updates zijn essentieel om de nauw keurigheid van het prestatie verkeer te hand haven, omdat het internet voortdurend wordt gegroeid.

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>Geografisch verkeer: routerings methode

Traffic Manager profielen kunnen worden geconfigureerd voor het gebruik van de geografische routerings methode, zodat gebruikers naar specifieke eind punten (Azure, extern of genest) worden geleid op basis van de geografische locatie waarvan hun DNS-query afkomstig is. Dit biedt Traffic Manager klanten de mogelijkheid om scenario's in te scha kelen waarbij de geografische regio van een gebruiker wordt gewerkt en deze op basis van het belang rijk is. Voor beelden zijn het voldoen aan data soevereiniteit-mandaten, lokalisatie van inhoud & gebruikers ervaring en het meten van verkeer van verschillende regio's.
Wanneer een profiel is geconfigureerd voor geografische route ring, moet aan elk eind punt dat aan het profiel is gekoppeld, een set geografische regio's worden toegewezen. Een geografische regio kan de volgende granulatie niveaus hebben. 
- Wereld: elke regio
- Regionale groepering: bijvoorbeeld Afrika, Midden-Oosten, Australië/Pacific, enzovoort. 
- Land/regio: bijvoorbeeld Ierland, Peru, Hongkong SAR enz. 
- Provincie: bijvoorbeeld VS-Californië, Australië-Queens land, Canada-Alberta enzovoort. (Opmerking: dit granulatie niveau wordt alleen ondersteund voor Staten/provincies in Australië, Canada en USA).

Wanneer een regio of een set regio's wordt toegewezen aan een eind punt, worden alle aanvragen van deze regio's alleen doorgestuurd naar dat eind punt. Traffic Manager gebruikt het bron-IP-adres van de DNS-query om te bepalen van welke regio een gebruiker query's uitvoert. Dit is meestal het IP-adres van de lokale DNS-resolver waarmee de query namens de gebruiker wordt uitgevoerd.  

![Azure Traffic Manager ' geografisch ' verkeer-routerings methode](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager leest het bron-IP-adres van de DNS-query en bepaalt vanuit welke geografische regio deze afkomstig is. Vervolgens ziet u of er een eind punt is waaraan deze geografische regio is toegewezen. Deze zoek actie begint bij het laagste granulatie niveau (provincie waar het wordt ondersteund, anders op het niveau van het land/de regio) en gaat helemaal naar het hoogste niveau, dat wil zeggen **wereld**. De eerste overeenkomst die met deze navigatie is gevonden, wordt aangeduid als het eind punt dat in het query-antwoord moet worden geretourneerd. Bij het vergelijken met een genest type eind punt wordt een eind punt binnen dat onderliggende profiel geretourneerd, op basis van de routerings methode. De volgende punten zijn van toepassing op dit gedrag:

- Een geografische regio kan alleen worden toegewezen aan één eind punt in een Traffic Manager profiel wanneer het routerings type geografische route ring is. Dit zorgt ervoor dat de route ring van gebruikers deterministisch is en klanten kunnen scenario's inschakelen waarvoor een ondubbelzinnige geografische grenzen zijn vereist.
- Als de regio van een gebruiker zich onder de geografische toewijzing van twee verschillende eind punten bevinden, Traffic Manager selecteert het eind punt met de laagste granulatie en worden de aanvragen van die regio niet naar het andere eind punt geroutingeerd. Denk bijvoorbeeld aan een geografisch routerings type profiel met twee eind punten-Endpoint1 en Endpoint2. Endpoint1 is geconfigureerd voor het ontvangen van verkeer van Ierland en Endpoint2 is geconfigureerd om verkeer van Europa te ontvangen. Als een aanvraag afkomstig is uit Ierland, wordt deze altijd doorgestuurd naar Endpoint1.
- Omdat een regio slechts aan één eind punt kan worden toegewezen, wordt deze door Traffic Manager geretourneerd, ongeacht of het eind punt in orde is of niet.

    >[!IMPORTANT]
    >Het wordt ten zeerste aanbevolen dat klanten die de geografische routerings methode gebruiken, deze koppelen aan de geneste type-eind punten die onderliggende profielen hebben met ten minste twee eind punten binnen elke.
- Als er een overeenkomst met een eind punt wordt gevonden en het eind punt de status **gestopt** heeft, retourneert Traffic Manager een niet-gegevens antwoord. In dit geval worden er geen verdere zoek acties meer in de hiërarchie van geografische regio's gemaakt. Dit gedrag is ook van toepassing op geneste eindpunt typen wanneer het onderliggende profiel de status **gestopt** of **uitgeschakeld** heeft.
- Als een eind punt een **Uitgeschakelde** status heeft, wordt dit niet opgenomen in het gebied dat overeenkomt met het proces. Dit gedrag is ook van toepassing op geneste eindpunt typen wanneer het eind punt de status **uitgeschakeld** heeft.
- Als een query afkomstig is uit een geografische regio die geen toewijzing heeft in dat profiel, retourneert Traffic Manager een niet-gegevens antwoord. Daarom wordt het ten zeerste aanbevolen dat klanten geografische route ring gebruiken met één eind punt, in het ideale geval het type genest met ten minste twee eind punten binnen het onderliggende profiel, waarbij de regionale **wereld** hieraan is toegewezen. Dit zorgt er ook voor dat alle IP-adressen die niet zijn toegewezen aan een regio, worden afgehandeld.

Zoals uitgelegd in de werking van [Traffic Manager](traffic-manager-how-it-works.md), ontvangt Traffic Manager geen DNS-query's rechtstreeks van clients. DNS-query's zijn afkomstig uit de recursieve DNS-service die door de clients zijn geconfigureerd om te worden gebruikt. Daarom is het IP-adres dat wordt gebruikt om de regio te bepalen, niet het IP-adres van de client, maar het is het IP-adres van de recursieve DNS-service. In de praktijk is dit IP-adres een goede proxy voor de client.

### <a name="faqs"></a>Veelgestelde vragen

* [Wat zijn enkele gebruiks voorbeelden waarbij geografische route ring nuttig is?](./traffic-manager-faqs.md#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Hoe kan ik bepalen of ik de routerings methode voor prestaties of de geografische routerings methode moet gebruiken?](./traffic-manager-faqs.md#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Wat zijn de regio's die door Traffic Manager worden ondersteund voor geografische route ring?](./traffic-manager-faqs.md#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Hoe bepaalt Traffic Manager of een gebruiker een query uitvoert?](./traffic-manager-faqs.md#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [Is het gegarandeerd dat Traffic Manager de exacte geografische locatie van de gebruiker in elk geval correct kunt bepalen?](./traffic-manager-faqs.md#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Moet een eind punt zich fysiek bevinden in dezelfde regio als de versie die is geconfigureerd met voor geografische route ring?](./traffic-manager-faqs.md#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Kan ik geografische regio's toewijzen aan eind punten in een profiel dat niet is geconfigureerd voor geografische route ring?](./traffic-manager-faqs.md#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Waarom krijg ik een fout melding wanneer ik de routerings methode van een bestaand profiel probeer te wijzigen in geografisch?](./traffic-manager-faqs.md#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Waarom wordt het ten zeerste aanbevolen dat klanten geneste profielen maken in plaats van eind punten onder een profiel waarvoor geografische route ring is ingeschakeld?](./traffic-manager-faqs.md#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [Zijn er beperkingen voor de API-versie die ondersteuning biedt voor dit routerings type?](./traffic-manager-faqs.md#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>Meerdere waarden verkeer-routerings methode
Met de routerings methode met meerdere **waarden** kunt u in één antwoord op een DNS-query meer in orde zijnde eind punten ophalen. Hierdoor kan de aanroeper aan client zijde nieuwe pogingen uitvoeren met andere eind punten in het geval van een geretourneerd eind punt dat niet meer reageert. Dit patroon kan de beschikbaarheid van een service vergroten en de latentie verminderen die is gekoppeld aan een nieuwe DNS-query om een gezond eindpunt op te halen. De methode voor het routeren van meerdere waarden werkt alleen als alle eind punten van het type extern zijn en zijn opgegeven als IPv4-of IPv6-adres. Wanneer een query voor dit profiel wordt ontvangen, worden alle gezonde eind punten geretourneerd en onderhevig aan het Configureer bare maximum aantal retour waarden.

### <a name="faqs"></a>Veelgestelde vragen

* [Wat zijn enkele situaties waarbij de route ring met meerdere waarden nuttig is?](./traffic-manager-faqs.md#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Hoeveel eind punten worden er geretourneerd wanneer meerdere waarden worden geroutingeerd?](./traffic-manager-faqs.md#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Krijgt ik dezelfde set eind punten als de route ring met meerdere waarden wordt gebruikt?](./traffic-manager-faqs.md#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>Subnet Traffic-routerings methode
Met de routerings methode voor het **subnet** Traffic kunt u een set IP-adresbereiken voor eind gebruikers toewijzen aan specifieke eind punten in een profiel. Als Traffic Manager vervolgens een DNS-query voor dat profiel ontvangt, wordt het bron-IP-adres van de aanvraag gecontroleerd (in de meeste gevallen is dit het uitgaande IP-adres van de DNS-resolver dat door de oproepende functie wordt gebruikt), te bepalen aan welk eind punt de toewijzing wordt toegewezen en wordt dat eind punt geretourneerd in het antwoord van de query. 

Het IP-adres dat moet worden toegewezen aan een eind punt kan worden opgegeven als CIDR-bereiken (bijvoorbeeld 1.2.3.0/24) of als een adres bereik (bijvoorbeeld 1.2.3.4-5.6.7.8). De IP-adresbereiken die zijn gekoppeld aan een eind punt moeten uniek zijn binnen dat profiel en mogen geen overlap ping hebben met de IP-adresset van een ander eind punt in hetzelfde profiel.
Als u een eind punt zonder adres bereik definieert, fungeert dat als een terugval en neemt het verkeer uit alle resterende subnetten. Als er geen terugval-eind punt is opgenomen, stuurt Traffic Manager een antwoord voor geen gegevens voor ongedefinieerde bereiken. Daarom wordt u ten zeerste aangeraden om een terugval-eind punt te definiëren, of om ervoor te zorgen dat alle mogelijke IP-bereiken zijn opgegeven voor uw eind punten.

Subnet routering kan worden gebruikt om een andere ervaring te bieden voor gebruikers die verbinding maken vanaf een specifieke IP-adres ruimte. Met behulp van subnetroutering kan een klant bijvoorbeeld alle aanvragen van het hoofdkantoor laten doorsturen naar een ander eindpunt waar ze mogelijk een interne versie van de app testen. Een ander scenario is dat u een andere ervaring wilt bieden aan gebruikers die verbinding maken via een specifieke internetprovider (u wilt bijvoorbeeld gebruikers van een bepaalde provider blokkeren).

### <a name="faqs"></a>Veelgestelde vragen

* [Wat zijn enkele gebruiks voorbeelden waarbij subnet routering nuttig is?](./traffic-manager-faqs.md#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Hoe kent Traffic Manager het IP-adres van de eind gebruiker?](./traffic-manager-faqs.md#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Hoe kan ik IP-adressen opgeven wanneer ik een subnet routering gebruik?](./traffic-manager-faqs.md#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Hoe kan ik een terugval-eind punt opgeven bij het gebruik van subnet routering?](./traffic-manager-faqs.md#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [Wat gebeurt er als een eind punt wordt uitgeschakeld in een type profiel voor een subnet routering?](./traffic-manager-faqs.md#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het ontwikkelen van toepassingen met een hoge Beschik baarheid met behulp van [Traffic Manager endpoint-bewaking](traffic-manager-monitoring.md)