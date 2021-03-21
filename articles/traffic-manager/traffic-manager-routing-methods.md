---
title: Azure Traffic Manager-routerings methoden voor verkeer
description: Dit artikel helpt u bij het begrijpen van de verschillende routerings methoden voor verkeer die worden gebruikt door Traffic Manager
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/21/2021
ms.author: duau
ms.openlocfilehash: eeebded128f636ecba2e4e0dab1bc2f0632aaa6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98730969"
---
# <a name="traffic-manager-routing-methods"></a>Methoden voor het doorsturen van Traffic Manager

Azure Traffic Manager ondersteunt zes verkeers routerings methoden om te bepalen hoe netwerk verkeer wordt gerouteerd naar de verschillende service-eind punten. Voor elk profiel past Traffic Manager de routerings methode voor verkeer toe die eraan is gekoppeld aan elke DNS-query die wordt ontvangen. De routerings methode voor verkeer bepaalt welk eind punt wordt geretourneerd in het DNS-antwoord.

De volgende methoden voor verkeers routering zijn beschikbaar in Traffic Manager:

* **[Prioriteit](#priority-traffic-routing-method):** Selecteer **prioriteits** routering wanneer u een primair service-eind punt voor al het verkeer wilt hebben. U kunt meerdere back-eindpunten opgeven voor het geval de primaire of een van de back-eindpunten niet beschikbaar is.
* **[Gewogen](#weighted):** Selecteer **gewogen** route ring wanneer u verkeer wilt distribueren over een set van eind punten op basis van hun gewicht. Stel het gewicht hetzelfde in om gelijkmatig te verdelen over alle eind punten.
* **[Prestaties](#performance):** Selecteer **prestatie** routering wanneer u eind punten hebt in verschillende geografische locaties en u wilt dat eind gebruikers het ' dichtstbijgelegen ' eind punt gebruiken voor de laagste netwerk latentie.
* **[Geografisch](#geographic):** Selecteer **geografische** route ring om gebruikers te leiden naar specifieke eind punten (Azure, extern of genest) op basis van waar hun DNS-query's afkomstig zijn van geografisch. Met deze routerings methode is het mogelijk om te voldoen aan scenario's zoals data soevereiniteit-mandaten, lokalisatie van inhoud & gebruikers ervaring en het meten van verkeer van verschillende regio's.
* **[Meerdere waarden](#multivalue):** Selecteer **meerdere waarden** voor Traffic Manager profielen die alleen IPv4/IPv6-adressen kunnen hebben als eind punten. Wanneer er een query voor dit profiel wordt ontvangen, worden alle in orde zijnde eind punten geretourneerd.
* **[Subnet](#subnet):** Selecteer **subnet** Traffic-routerings methode om sets van IP-adresbereiken voor eind gebruikers toe te wijzen aan een bepaald eind punt. Wanneer een aanvraag wordt ontvangen, wordt het eind punt geretourneerd dat is toegewezen aan het bron-IP-adres van de aanvraag. 


Alle Traffic Manager profielen hebben status controle en automatische failover van eind punten. Zie [Traffic Manager endpoint monitoring](traffic-manager-monitoring.md)voor meer informatie. Binnen een Traffic Manager profiel kunt u slechts één routerings methode voor verkeer tegelijk configureren. U kunt op elk gewenst moment een andere methode voor verkeers routering voor uw profiel selecteren. Uw wijzigingen worden binnen een minuut toegepast zonder uitval tijd. U kunt routerings methoden voor verkeer combi neren door geneste Traffic Manager profielen te gebruiken. Door profielen te nesten kunt u geavanceerde configuraties voor verkeer routeren die voldoen aan de behoeften van grotere en complexe toepassingen. Zie [geneste Traffic Manager profielen](traffic-manager-nested-profiles.md)voor meer informatie.

## <a name="priority-traffic-routing-method"></a>Prioriteits verkeer: routerings methode

Een organisatie wil vaak betrouw baarheid bieden voor hun services. Om dit te doen, implementeren ze een of meer back-upservices voor het geval de primaire server uitvalt. Met de routerings methode ' Priority ' kunnen Azure-klanten het failover-patroon eenvoudig implementeren.

![Azure Traffic Manager ' Priority ' Traffic-routerings methode](media/traffic-manager-routing-methods/priority.png)

Het Traffic Manager-profiel bevat een lijst met service-eindpunten met prioriteit. Standaard verzendt Traffic Manager al het verkeer naar het primaire eindpunt (met de hoogste prioriteit). Als het primaire eindpunt niet beschikbaar is, stuurt Traffic Manager het verkeer naar het tweede eindpunt. In een situatie waarin de primaire en secundaire eind punten niet beschikbaar zijn, gaat het verkeer naar de derde, enzovoort. De beschikbaarheid van het eindpunt is gebaseerd op de geconfigureerde status (ingeschakeld of uitgeschakeld) en de voortdurende eindpuntcontrole.

### <a name="configuring-endpoints"></a>Eind punten configureren

Met Azure Resource Manager configureert u de eindpunt prioriteit expliciet met behulp van de eigenschap Priority voor elk eind punt. Deze eigenschap is een waarde tussen 1 en 1000. Een lagere waarde staat voor een hogere prioriteit. Eind punten kunnen geen prioriteits waarden delen. Het instellen van de eigenschap is optioneel. Als u dit weglaat, wordt een standaard prioriteit op basis van de eindpunt volgorde gebruikt.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Gewogen verkeer-routerings methode
Met de methode gewogen verkeers routering kunt u verkeer gelijkmatig distribueren of een vooraf gedefinieerde wegings factor gebruiken.

![Azure Traffic Manager ' gewogen ' Traffic-routerings methode](media/traffic-manager-routing-methods/weighted.png)

In de routerings methode gewogen verkeer, wijst u een gewicht toe aan elk eind punt in de configuratie van het Traffic Manager-profiel. Het gewicht is een geheel getal tussen 1 en 1000. Deze parameter is optioneel. Als u dit weglaat, gebruikt Traffic managers een standaard gewicht van ' 1 '. Het hogere gewicht, des te hoger de prioriteit.

Voor elke ontvangen DNS-query kiest Traffic Manager willekeurig een beschikbaar eindpunt. De kans dat een eindpunt wordt gekozen, is gebaseerd op het gewicht dat is toegewezen aan alle beschikbare eindpunten. Het gebruik van hetzelfde gewicht over alle eind punten resulteert in een gelijkmatige verdeling van verkeer. Als u een hoger of lager gewicht gebruikt voor specifieke eind punten, worden deze eind punten meer of minder vaak geretourneerd in de DNS-antwoorden.

De methode weighted maakt enkele handige scenario's mogelijk:

* Geleidelijke upgrade van de toepassing: met een percentage van het verkeer dat naar een nieuw eind punt kan worden gerouteerd en het verkeer geleidelijk verhogen tot 100%.
* Toepassings migratie naar Azure: Maak een profiel met zowel Azure als externe eind punten. Pas het gewicht van de eind punten aan om de voor keur te geven aan de nieuwe eind punten.
* Cloud-bursting voor meer capaciteit: een on-premises implementatie in de Cloud snel uitbreiden door deze achter een Traffic Manager profiel te plaatsen. Wanneer u extra capaciteit nodig hebt in de Cloud, kunt u meer eind punten toevoegen of inschakelen en opgeven welk gedeelte van verkeer naar elk eind punt gaat.

U kunt gewichten configureren met behulp van de Azure Portal, Azure PowerShell, CLI of de REST Api's.

Een punt om te onthouden is dat DNS-antwoorden in de cache worden opgeslagen door clients. Ze worden ook in de cache opgeslagen door de recursieve DNS-servers die de clients gebruiken om DNS-namen om te zetten. Deze caching kan van invloed zijn op de verdeling van gewogen verkeer. Wanneer het aantal clients en recursieve DNS-servers groot is, werkt de verkeers distributie als verwacht. Wanneer het aantal clients of recursieve DNS-servers echter klein is, kan de verkeers distributie aanzienlijk worden verduidelijkt in de cache.

Veelvoorkomende use-cases zijn:

* Ontwikkel-en test omgevingen
* Communicatie tussen toepassingen
* Toepassingen die gericht zijn op een smalle gebruikers basis die een gemeen schappelijke recursieve DNS-infra structuur delen (bijvoorbeeld werk nemers van het bedrijf die verbinding maken via een proxy)

Deze DNS-cache-effecten zijn gebruikelijk voor alle op DNS gebaseerde Traffic-routerings systemen, niet alleen voor Azure Traffic Manager. In sommige gevallen kan het expliciet verwijderen van de DNS-cache een tijdelijke oplossing bieden. Als dat niet werkt, kan een alternatieve verkeers routerings methode beter geschikt zijn.

## <a name="performance-traffic-routing-method"></a><a name = "performance"></a>Prestatie verkeer-routerings methode

Het implementeren van eind punten op twee of meer locaties over de hele wereld kan de reactie snelheid van uw toepassingen verbeteren. Met de Traffic-routerings methode voor prestaties kunt u verkeer routeren naar de locatie die het dichtst bij u ligt.

![Verkeer van de Azure Traffic Manager-routerings methode voor prestaties](media/traffic-manager-routing-methods/performance.png)

Het dichtstbijgelegen eind punt is niet noodzakelijkerwijs het dichtst bij de geografische afstand gemeten. In plaats daarvan bepaalt de routerings methode voor prestaties het dichtstbijzijnde eind punt door netwerk latentie te meten. Traffic Manager houdt een tabel met Internet latentie bij om de retour tijd tussen IP-adresbereiken en elk Azure-Data Center bij te houden.

Traffic Manager zoekt het bron-IP-adres van de binnenkomende DNS-aanvraag in de tabel Internet latentie. Traffic Manager kiest vervolgens een beschikbaar eind punt in het Azure-Data Center met de laagste latentie voor dat IP-adres bereik. Vervolgens retourneert Traffic Manager dat eind punt in het DNS-antwoord.

Zoals beschreven in de werking van [Traffic Manager](traffic-manager-how-it-works.md), ontvangt Traffic Manager geen DNS-query's rechtstreeks van clients. In plaats daarvan zijn DNS-query's afkomstig van de recursieve DNS-service die de clients hebben geconfigureerd voor gebruik. Daarom is het IP-adres dat wordt gebruikt om het ' dichtstbijgelegen ' eind punt te bepalen, niet het IP-adres van de client, maar het IP-adres van de recursieve DNS-service. Dit IP-adres is een goede proxy voor de client.


Traffic Manager de Internet latentie tabel regel matig bijwerkt om wijzigingen aan te brengen in het wereld wijde Internet en nieuwe Azure-regio's. De prestaties van toepassingen variëren echter op basis van real-time variaties in de belasting via internet. Prestatie verkeer: route ring bewaakt de belasting van een bepaald service-eind punt niet. Als een eind punt niet beschikbaar is, wordt dit door Traffic Manager niet opgenomen in de antwoorden van de DNS-query.


Punten om te noteren:

* Als uw profiel meerdere eind punten in dezelfde Azure-regio bevat, verdeelt Traffic Manager verkeer gelijkmatig over de beschik bare eind punten in die regio. Als u de voor keur geeft aan een andere distributie van verkeer binnen een regio, kunt u [geneste Traffic Manager profielen](traffic-manager-nested-profiles.md)gebruiken.
* Als alle ingeschakelde eind punten in de dichtstbijzijnde Azure-regio worden gedegradeerd, Traffic Manager verkeer verplaatsen naar de eind punten in de dichtstbijzijnde Azure-regio. Als u een voorkeurs reeks failover wilt definiëren, gebruikt u [geneste Traffic Manager profielen](traffic-manager-nested-profiles.md).
* Wanneer u de routerings methode voor prestatie verkeer met externe eind punten of geneste eind punten gebruikt, moet u de locatie van deze eind punten opgeven. Kies de Azure-regio die het dichtst bij uw implementatie ligt. Deze locaties zijn de waarden die worden ondersteund door de tabel Internet latentie.
* Het algoritme waarmee het eind punt wordt gekozen, is deterministisch. Herhaalde DNS-query's van dezelfde client worden omgeleid naar hetzelfde eind punt. Normaal gesp roken gebruiken clients verschillende recursieve DNS-servers wanneer ze onderweg zijn. De client kan worden doorgestuurd naar een ander eind punt. Route ring kan ook worden beïnvloed door updates voor de Internet latentie tabel. Daarom garandeert de routerings methode voor prestatie verkeer niet dat een client altijd wordt doorgestuurd naar hetzelfde eind punt.
* Wanneer de tabel Internet latentie wordt gewijzigd, kunt u zien dat sommige clients worden omgeleid naar een ander eind punt. Deze wijziging van de route ring is nauw keuriger op basis van de huidige latentie gegevens. Deze updates zijn essentieel om de nauw keurigheid van het prestatie verkeer te hand haven, omdat het internet voortdurend wordt gegroeid.

## <a name="geographic-traffic-routing-method"></a><a name = "geographic"></a>Geografisch verkeer: routerings methode

Traffic Manager profielen kunnen worden geconfigureerd voor het gebruik van de geografische routerings methode, zodat gebruikers naar specifieke eind punten (Azure, extern of genest) worden geleid op basis van de geografische locatie waarvan hun DNS-query afkomstig is. Met deze routerings methode kunt u ervoor zorgen dat u voldoet aan data soevereiniteit-mandaten, lokalisatie van inhoud & gebruikers ervaring en het meten van verkeer van verschillende regio's.
Wanneer een profiel is geconfigureerd voor geografische route ring, moet aan elk eind punt dat aan het profiel is gekoppeld, een set geografische regio's worden toegewezen. Een geografische regio kan de volgende granulatie niveaus hebben. 
- Wereld: elke regio
- Regionale groepering: bijvoorbeeld Afrika, Midden-Oosten, Australië/Pacific, enzovoort. 
- Land/regio: bijvoorbeeld Ierland, Peru, Hongkong SAR enz. 
- Provincie: bijvoorbeeld VS-Californië, Australië-Queens land, Canada-Alberta enzovoort. (Opmerking: dit granulatie niveau wordt alleen ondersteund voor Staten/provincies in Australië, Canada en USA).

Wanneer een regio of een set regio's wordt toegewezen aan een eind punt, worden alle aanvragen van deze regio's alleen doorgestuurd naar dat eind punt. Traffic Manager gebruikt het bron-IP-adres van de DNS-query om te bepalen van welke regio een gebruiker een query uitvoert. Wordt meestal gevonden als het IP-adres van de lokale DNS-resolver die de query voor de gebruiker maakt.  

![Azure Traffic Manager ' geografisch ' verkeer-routerings methode](./media/traffic-manager-routing-methods/geographic.png)

Traffic Manager leest het bron-IP-adres van de DNS-query en bepaalt vanuit welke geografische regio het afkomstig is. Vervolgens ziet u of er een eind punt is waaraan deze geografische regio is toegewezen. Deze zoek actie begint bij het laagste granulatie niveau (provincie waar het wordt ondersteund, anders op het niveau van het land/de regio) en gaat helemaal naar het hoogste niveau, dat wil zeggen **wereld**. De eerste overeenkomst die met deze navigatie is gevonden, wordt gekozen als het eind punt dat in het antwoord op de query moet worden geretourneerd. Bij het vergelijken met een genest type eind punt wordt een eind punt binnen dat onderliggende profiel geretourneerd, op basis van de routerings methode. De volgende punten zijn van toepassing op dit gedrag:

- Een geografische regio kan alleen worden toegewezen aan één eind punt in een Traffic Manager profiel wanneer het routerings type geografische route ring is. Deze beperking zorgt ervoor dat de route ring van gebruikers deterministisch is, en klanten kunnen scenario's inschakelen waarvoor ondubbelzinnige geografische grenzen zijn vereist.
- Als de regio van een gebruiker wordt weer gegeven in de geografische toewijzing van twee verschillende eind punten, Traffic Manager selecteert u het eind punt met de laagste granulatie. Traffic Manager houdt geen rekening met het routeren van aanvragen van die regio naar het andere eind punt. Denk bijvoorbeeld aan een geografisch routerings type profiel met twee eind punten-Endpoint1 en Endpoint2. Endpoint1 is geconfigureerd voor het ontvangen van verkeer van Ierland en Endpoint2 is geconfigureerd om verkeer van Europa te ontvangen. Als een aanvraag afkomstig is uit Ierland, wordt deze altijd doorgestuurd naar Endpoint1.
- Omdat een regio slechts aan één eind punt kan worden toegewezen, retourneert Traffic Manager een antwoord, ongeacht of het eind punt in orde is of niet.

    >[!IMPORTANT]
    >Het wordt ten zeerste aanbevolen dat klanten die de geografische routerings methode gebruiken, deze koppelen aan de geneste type-eind punten die onderliggende profielen hebben met ten minste twee eind punten binnen elke.
- Als er een overeenkomst met een eind punt wordt gevonden en het eind punt de status **gestopt** heeft, retourneert Traffic Manager een niet-gegevens antwoord. In dit geval worden er geen verdere zoek acties meer in de hiërarchie van geografische regio's gemaakt. Dit gedrag is ook van toepassing op geneste eindpunt typen wanneer het onderliggende profiel de status **gestopt** of **uitgeschakeld** heeft.
- Als een eind punt een **Uitgeschakelde** status heeft, wordt dit niet opgenomen in het gebied dat overeenkomt met het proces. Dit gedrag is ook van toepassing op geneste eindpunt typen wanneer het eind punt de status **uitgeschakeld** heeft.
- Als een query afkomstig is uit een geografische regio die geen toewijzing heeft in dat profiel, retourneert Traffic Manager een niet-gegevens antwoord. Daarom wordt u ten zeerste aangeraden geografische route ring met één eind punt te gebruiken. In het ideale geval van type genest met ten minste twee eind punten binnen het onderliggende profiel, waarbij de regionale **wereld** wordt toegewezen. Deze configuratie zorgt er ook voor dat alle IP-adressen die niet zijn toegewezen aan een regio, worden afgehandeld.

Zoals beschreven in de werking van [Traffic Manager](traffic-manager-how-it-works.md), ontvangt Traffic Manager geen DNS-query's rechtstreeks van clients. DNS-query's zijn afkomstig van de recursieve DNS-service die de clients hebben geconfigureerd voor gebruik. Daarom is het IP-adres dat wordt gebruikt om de regio te bepalen niet het IP-adres van de client, maar in plaats van het IP-adres van de recursieve DNS-service. Dit IP-adres is een goede proxy voor de client.

### <a name="faqs"></a>Veelgestelde vragen

* [Wat zijn enkele gebruiks voorbeelden waarbij geografische route ring nuttig is?](./traffic-manager-faqs.md#what-are-some-use-cases-where-geographic-routing-is-useful)

* [Hoe kan ik bepalen of ik de routerings methode voor prestaties of de geografische routerings methode moet gebruiken?](./traffic-manager-faqs.md#how-do-i-decide-if-i-should-use-performance-routing-method-or-geographic-routing-method)

* [Wat zijn de regio's die door Traffic Manager worden ondersteund voor geografische route ring?](./traffic-manager-faqs.md#what-are-the-regions-that-are-supported-by-traffic-manager-for-geographic-routing)

* [Hoe bepaalt Traffic Manager of een gebruiker een query uitvoert?](./traffic-manager-faqs.md#how-does-traffic-manager-determine-where-a-user-is-querying-from)

* [Is het gegarandeerd dat Traffic Manager de exacte geografische locatie van de gebruiker in elk geval correct kunt bepalen?](./traffic-manager-faqs.md#is-it-guaranteed-that-traffic-manager-can-correctly-determine-the-exact-geographic-location-of-the-user-in-every-case)

* [Moet een eind punt zich fysiek bevinden in dezelfde regio als de naam die is geconfigureerd met voor geografische route ring?](./traffic-manager-faqs.md#does-an-endpoint-need-to-be-physically-located-in-the-same-region-as-the-one-it-is-configured-with-for-geographic-routing)

* [Kan ik geografische regio's toewijzen aan eind punten in een profiel dat niet is geconfigureerd voor geografische route ring?](./traffic-manager-faqs.md#can-i-assign-geographic-regions-to-endpoints-in-a-profile-that-is-not-configured-to-do-geographic-routing)

* [Waarom krijg ik een fout melding wanneer ik de routerings methode van een bestaand profiel probeer te wijzigen in geografisch?](./traffic-manager-faqs.md#why-am-i-getting-an-error-when-i-try-to-change-the-routing-method-of-an-existing-profile-to-geographic)

* [Waarom wordt het ten zeerste aanbevolen dat klanten geneste profielen maken in plaats van eind punten onder een profiel waarvoor geografische route ring is ingeschakeld?](./traffic-manager-faqs.md#why-is-it-strongly-recommended-that-customers-create-nested-profiles-instead-of-endpoints-under-a-profile-with-geographic-routing-enabled)

* [Zijn er beperkingen voor de API-versie die ondersteuning biedt voor dit routerings type?](./traffic-manager-faqs.md#are-there-any-restrictions-on-the-api-version-that-supports-this-routing-type)

## <a name="multivalue-traffic-routing-method"></a><a name = "multivalue"></a>Meerdere waarden verkeer-routerings methode
Met de routerings methode met meerdere **waarden** kunt u in één antwoord op een DNS-query meer in orde zijnde eind punten ophalen. Met deze configuratie kan de aanroeper aan client zijde nieuwe pogingen uitvoeren met andere eind punten voor het geval een geretourneerd eind punt niet meer reageert. Dit patroon kan de beschikbaarheid van een service vergroten en de latentie verminderen die is gekoppeld aan een nieuwe DNS-query om een gezond eindpunt op te halen. De methode voor het routeren van meerdere waarden werkt alleen als alle eind punten van het type extern zijn en zijn opgegeven als IPv4-of IPv6-adres. Wanneer een query voor dit profiel wordt ontvangen, worden alle gezonde eind punten geretourneerd en onderhevig aan het Configureer bare maximum aantal retour waarden.

### <a name="faqs"></a>Veelgestelde vragen

* [Wat zijn enkele situaties waarbij de route ring met meerdere waarden nuttig is?](./traffic-manager-faqs.md#what-are-some-use-cases-where-multivalue-routing-is-useful)

* [Hoeveel eind punten worden er geretourneerd wanneer meerdere waarden worden geroutingeerd?](./traffic-manager-faqs.md#how-many-endpoints-are-returned-when-multivalue-routing-is-used)

* [Krijgt ik dezelfde set eind punten als de route ring met meerdere waarden wordt gebruikt?](./traffic-manager-faqs.md#will-i-get-the-same-set-of-endpoints-when-multivalue-routing-is-used)

## <a name="subnet-traffic-routing-method"></a><a name = "subnet"></a>Subnet Traffic-routerings methode
Met de routerings methode voor het **subnet** Traffic kunt u een set IP-adresbereiken voor eind gebruikers toewijzen aan specifieke eind punten in een profiel. Als Traffic Manager een DNS-query voor dat profiel ontvangt, wordt het bron-IP-adres van de aanvraag gecontroleerd. Vervolgens wordt bepaald aan welk eind punt het is toegewezen en wordt dat eind punt geretourneerd in het antwoord op de query. In de meeste gevallen is het IP-adres van de bron de DNS-resolver die wordt gebruikt door de oproepende functie.

Het IP-adres dat moet worden toegewezen aan een eind punt kan worden opgegeven als CIDR-bereiken (bijvoorbeeld 1.2.3.0/24) of als een adres bereik (bijvoorbeeld 1.2.3.4-5.6.7.8). Het IP-bereik dat aan een eind punt is gekoppeld, moet uniek zijn binnen dit profiel. Het adres bereik mag geen overlap ping hebben met het IP-adres dat is ingesteld voor een ander eind punt in hetzelfde profiel.
Als u een eind punt zonder adres bereik definieert, fungeert dat als een terugval en neemt het verkeer uit alle resterende subnetten. Als er geen terugval-eind punt is opgenomen, stuurt Traffic Manager een antwoord voor geen gegevens voor ongedefinieerde bereiken. Het wordt ten zeerste aanbevolen om een terugval-eind punt te definiëren om ervoor te zorgen dat alle mogelijke IP-bereiken worden opgegeven voor uw eind punten.

Subnet routering kan worden gebruikt om een andere ervaring te bieden voor gebruikers die verbinding maken vanaf een specifieke IP-adres ruimte. U kunt bijvoorbeeld alle aanvragen van uw bedrijfs kantoor naar een ander eind punt routeren. Deze routerings methode is met name handig als u een interne versie van uw app wilt testen. Een ander scenario is dat u een andere ervaring wilt bieden aan gebruikers die verbinding maken via een specifieke internetprovider (u wilt bijvoorbeeld gebruikers van een bepaalde provider blokkeren).

### <a name="faqs"></a>Veelgestelde vragen

* [Wat zijn enkele gebruiks voorbeelden waarbij subnet routering nuttig is?](./traffic-manager-faqs.md#what-are-some-use-cases-where-subnet-routing-is-useful)

* [Hoe kent Traffic Manager het IP-adres van de eind gebruiker?](./traffic-manager-faqs.md#how-does-traffic-manager-know-the-ip-address-of-the-end-user)

* [Hoe kan ik IP-adressen opgeven wanneer ik een subnet routering gebruik?](./traffic-manager-faqs.md#how-can-i-specify-ip-addresses-when-using-subnet-routing)

* [Hoe kan ik een terugval-eind punt opgeven bij het gebruik van subnet routering?](./traffic-manager-faqs.md#how-can-i-specify-a-fallback-endpoint-when-using-subnet-routing)

* [Wat gebeurt er als een eind punt wordt uitgeschakeld in een type profiel voor een subnet routering?](./traffic-manager-faqs.md#what-happens-if-an-endpoint-is-disabled-in-a-subnet-routing-type-profile)


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het ontwikkelen van toepassingen met een hoge Beschik baarheid met behulp van [Traffic Manager endpoint-bewaking](traffic-manager-monitoring.md)