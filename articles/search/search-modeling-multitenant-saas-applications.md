---
title: Multitenancy en isolatie van inhoud
titleSuffix: Azure Cognitive Search
description: Meer informatie over veelgebruikte ontwerp patronen voor SaaS-toepassingen met meerdere tenants tijdens het gebruik van Azure Cognitive Search.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/06/2021
ms.openlocfilehash: 7833dcf8fbe2b6460346310a4d094c7bb5d606c4
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581579"
---
# <a name="design-patterns-for-multitenant-saas-applications-and-azure-cognitive-search"></a>Ontwerp patronen voor SaaS-toepassingen met meerdere tenants en Azure Cognitive Search

Een multi tenant-toepassing biedt dezelfde services en mogelijkheden voor elk aantal tenants dat de gegevens van een andere Tenant niet kan zien of delen. In dit document worden de isolatie strategieën voor tenants besproken voor multi tenant-toepassingen die zijn gebouwd met Azure Cognitive Search.

## <a name="azure-cognitive-search-concepts"></a>Azure Cognitive Search-concepten

Als een Search-as-a-service-oplossing kunnen ontwikkel aars met [Azure Cognitive Search](search-what-is-azure-search.md) uitgebreidere Zoek ervaringen toevoegen aan toepassingen zonder dat ze een infra structuur hoeven te beheren of een expert te worden in het ophalen van informatie. Gegevens worden naar de service geüpload en vervolgens opgeslagen in de Cloud. Door eenvoudige aanvragen te gebruiken voor de Azure Cognitive Search-API, kunnen de gegevens worden gewijzigd en doorzocht. 

### <a name="search-services-indexes-fields-and-documents"></a>Services, indexen, velden en documenten doorzoeken

Voordat u ontwerp patronen bespreekt, is het belang rijk dat u een paar basis concepten begrijpt.

Wanneer u Azure Cognitive Search gebruikt, wordt er één geabonneerd op een *Zoek service*. Wanneer gegevens worden geüpload naar Azure Cognitive Search, wordt deze opgeslagen in een *index* binnen de zoek service. Er kan een aantal indexen binnen één service zijn. Als u de vertrouwde concepten van data bases wilt gebruiken, kan de zoek service worden likened naar een Data Base terwijl de indexen binnen een service kunnen worden likened aan tabellen in een Data Base.

Elke index binnen een zoek service heeft een eigen schema, dat wordt gedefinieerd door een aantal aanpas bare *velden*. Gegevens worden toegevoegd aan een Azure Cognitive Search-index in de vorm van afzonderlijke *documenten*. Elk document moet worden geüpload naar een bepaalde index en moet overeenkomen met het schema van de index. Bij het zoeken naar gegevens met behulp van Azure Cognitive Search worden de Zoek opdrachten in volledige tekst op basis van een bepaalde index uitgegeven.  Om deze concepten te vergelijken met die van een Data Base, kunnen velden worden likened aan kolommen in een tabel en kunnen de documenten worden likened naar rijen.

### <a name="scalability"></a>Schaalbaarheid

Elke Azure Cognitive Search-service in de [prijs categorie](https://azure.microsoft.com/pricing/details/search/) Standard kan worden geschaald in twee dimensies: opslag en beschik baarheid.

+ Er kunnen *partities* worden toegevoegd om de opslag van een zoek service te verg Roten.
+ *Replica's* kunnen worden toegevoegd aan een service om de door voer te verg Roten van aanvragen die door een zoek service kunnen worden verwerkt.

Door partities en replica's toe te voegen en te verwijderen, kan de capaciteit van de zoek service groeien met de hoeveelheid gegevens en het verkeer dat de toepassing vereist. Om een zoek service te kunnen bereiken voor een Lees- [Sla](https://azure.microsoft.com/support/legal/sla/search/v1_0/), zijn er twee replica's nodig. Om een service een [Sla](https://azure.microsoft.com/support/legal/sla/search/v1_0/)voor lezen/schrijven te kunnen krijgen, zijn er drie replica's nodig.

### <a name="service-and-index-limits-in-azure-cognitive-search"></a>Service-en index limieten in azure Cognitive Search

Er zijn een aantal verschillende [prijs categorieën](https://azure.microsoft.com/pricing/details/search/) in azure Cognitive Search. elk van de lagen heeft verschillende [limieten en quota's](search-limits-quotas-capacity.md). Sommige van deze limieten bevinden zich op het niveau van de service, sommige zijn op het niveau van de index en sommige zijn op partitie niveau.

|  | Basic | Standard1 | Standard2 | Standard3 | Standard3 HD |
| --- | --- | --- | --- | --- | --- |
| **Maximum aantal Replica's per service** |3 |12 |12 |12 |12 |
| **Maximum aantal partities per service** |1 |12 |12 |12 |3 |
| **Maximum aantal Zoek eenheden (Replica's * partities) per service** |3 |36 |36 |36 |36 (Maxi maal 3 partities) |
| **Maximale opslag per service** |2 GB |300 GB |1,2 TB |2,4 TB |600 GB |
| **Maximale opslag per partitie** |2 GB |25 GB |100 GB |200 GB |200 GB |
| **Maximum aantal indexen per service** |5 |50 |200 |200 |3000 (max. 1000 indexen/partitie) |

#### <a name="s3-high-density"></a>S3-hoge dichtheid

In de prijs categorie S3 van Azure Cognitive Search is er een optie voor de high-density modus (HD) die specifiek is ontworpen voor multi tenant scenario's. In veel gevallen is het nood zakelijk om een groot aantal kleinere tenants te ondersteunen onder één service om de voor delen van eenvoud en kosten efficiëntie te verhalen.

Met S3 HD kunnen de vele kleine indexen worden ingepakt onder het beheer van één zoek service door de mogelijkheid te bieden om indexen te schalen met behulp van partities voor de mogelijkheid om meer indexen in één service te hosten.

Een S3-service is ontworpen voor het hosten van een vast aantal indexen (Maxi maal 200) en toestaan dat elke index horizon taal kan worden geschaald wanneer er nieuwe partities worden toegevoegd aan de service. Het toevoegen van partities aan S3 HD Services verhoogt het maximum aantal indexen dat door de service kan worden gehost. De ideale maximum grootte voor een afzonderlijke S3HD-index is ongeveer 50-80 GB, maar er is geen limiet voor de vaste grootte voor elke index die door het systeem wordt opgelegd.

## <a name="considerations-for-multitenant-applications"></a>Overwegingen voor multi tenant-toepassingen

Multi tenant-toepassingen moeten bronnen efficiënt verdelen over de tenants met behoud van wat privacy niveau tussen de verschillende tenants. Er zijn enkele overwegingen bij het ontwerpen van de architectuur voor een dergelijke toepassing:

+ *Tenant isolatie:* Toepassings ontwikkelaars moeten passende maat regelen nemen om ervoor te zorgen dat tenants geen ongeoorloofde of ongewenste toegang hebben tot de gegevens van andere tenants. Buiten het perspectief van de privacy van gegevens vereisen de isolatie strategieën voor tenants een effectief beheer van gedeelde bronnen en bescherming tegen lawaai van de ruis.

+ *Kosten van Cloud resource:* Net als bij elke andere toepassing moeten software oplossingen kosten concurrerend zijn als onderdeel van een multi tenant-toepassing.

+ *Gemakkelijke bewerkingen:* Bij het ontwikkelen van een architectuur met meerdere tenants is de invloed op de bewerkingen en complexiteit van de toepassing een belang rijke overweging. Azure Cognitive Search heeft een [Sla van 99,9%](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

+ *Algemene footprint:* Multi tenant-toepassingen moeten mogelijk doel treffend zijn voor tenants die over de hele wereld worden gedistribueerd.

+ *Schaal baarheid:* Ontwikkel aars van toepassingen moeten overwegen hoe ze zich afstemmen tussen het onderhouden van een voldoende laag niveau van toepassings complexiteit en het ontwerpen van de toepassing om te schalen met het aantal tenants en de grootte van de gegevens en workload van de tenants.

Azure Cognitive Search biedt een aantal grenzen die kunnen worden gebruikt voor het isoleren van gegevens en werk belasting van tenants.

## <a name="modeling-multitenancy-with-azure-cognitive-search"></a>Multitenancy model leren met Azure Cognitive Search

In het geval van een multi tenant scenario gebruikt de ontwikkelaar van de toepassing een of meer zoek services en wordt de tenants verdeeld over services, indices of beide. Azure Cognitive Search heeft enkele algemene patronen bij het model leren van een scenario met meerdere tenants:

+ *Eén index per Tenant:* Elke Tenant heeft een eigen index binnen een zoek service die wordt gedeeld met andere tenants.

+ *Eén service per Tenant:* Elke Tenant heeft een eigen exclusieve Azure Cognitive Search-service, die het hoogste niveau van gegevens en de belasting van de workload biedt.

+ *Combi natie van:* Grotere, meer actieve tenants krijgen toegewezen services, terwijl bij kleinere tenants afzonderlijke indexen binnen gedeelde services worden toegewezen.

## <a name="model-1-one-index-per-tenant"></a>Model 1: Eén index per Tenant

:::image type="content" source="media/search-modeling-multitenant-saas-applications/azure-search-index-per-tenant.png" alt-text="Een portrayal van het model van de index-per-Tenant" border="false":::

In een model voor index-per Tenant nemen meerdere tenants één Azure Cognitive Search-service waarbij elke Tenant een eigen index heeft.

Tenants bereiken gegevens isolatie, omdat alle Zoek opdrachten en document bewerkingen worden uitgegeven op index niveau in azure Cognitive Search. In de toepassingslaag moet u zich bewust zijn van het directe verkeer van de verschillende tenants naar de juiste indexen, terwijl u ook resources op service niveau voor alle tenants beheert.

Een sleutel kenmerk van het model van de index-per-Tenant is de mogelijkheid van de ontwikkelaar van de toepassing om de capaciteit van een zoek service over te nemen op basis van de tenants van de toepassing. Als de tenants een ongelijke verdeling van de werk belasting hebben, kan de optimale combi natie van tenants worden gedistribueerd over de indexen van een zoek service voor een aantal uiterst actieve, tijdrovende tenants en tegelijkertijd een lange staart van minder actieve tenants bieden. De afweging is het onvermogen van het model voor het afhandelen van situaties waarbij elke Tenant gelijktijdig Maxi maal actief is.

Het model van de index-per-Tenant vormt de basis voor een variabel kosten model, waarbij een volledige Azure Cognitive Search-service vooraf wordt aangeschaft en vervolgens wordt gevuld met tenants. Hierdoor kan ongebruikte capaciteit worden aangegeven voor experimenten en gratis accounts.

Voor toepassingen met een algemene footprint is het model van de index-per-Tenant mogelijk niet het meest efficiënt. Als de tenants van een toepassing over de hele wereld worden gedistribueerd, kan er een afzonderlijke service nodig zijn voor elke regio, waardoor de kosten voor elk van de verschillende regio's kunnen worden gedupliceerd.

Azure Cognitive Search maakt het mogelijk de afzonderlijke indexen te schalen en het totale aantal indexen dat moet worden uitgebreid. Als er een geschikte prijs categorie wordt gekozen, kunnen partities en replica's worden toegevoegd aan de gehele zoek service wanneer een afzonderlijke index binnen de service te groot is voor wat betreft opslag of verkeer.

Als het totale aantal indexen te groot is voor één service, moet een andere service worden ingericht voor de nieuwe tenants. Als indexen moeten worden verplaatst tussen de zoek services als nieuwe services worden toegevoegd, moeten de gegevens uit de index hand matig worden gekopieerd van de ene index naar de andere als Azure Cognitive Search de index niet mag worden verplaatst.

## <a name="model-2-once-service-per-tenant"></a>Model 2: na service per Tenant

:::image type="content" source="media/search-modeling-multitenant-saas-applications/azure-search-service-per-tenant.png" alt-text="Een portrayal van het service-per-Tenant model" border="false":::

In een architectuur met een service-per-Tenant heeft elke Tenant een eigen zoek service.

In dit model bereikt de toepassing het maximale isolatie niveau voor de tenants. Elke service heeft specifieke opslag en door Voer voor het verwerken van een zoek opdracht en afzonderlijke API-sleutels.

Voor toepassingen waarbij elke Tenant een grote footprint heeft of als de werk belasting weinig variabiliteit heeft van Tenant naar Tenant, is het model van de service-per-Tenant een efficiënte keuze omdat resources niet worden gedeeld tussen de werk belastingen van verschillende tenants.

Een service per Tenant model biedt ook het voor deel van een voorspelbaar, vast kosten model. Er is geen investering vooraf in een volledige zoek service, totdat er een Tenant is om deze in te vullen. de kosten per Tenant zijn echter hoger dan een model per Tenant.

Het model service-per-Tenant is een efficiënte keuze voor toepassingen met een algemene footprint. Met geografisch gedistribueerde tenants is het eenvoudig om elke Tenant service in de juiste regio te hebben.

De uitdagingen bij het schalen van dit patroon ontstaan als afzonderlijke tenants hun service uitgroeien. Azure Cognitive Search biedt momenteel geen ondersteuning voor het bijwerken van de prijs categorie van een zoek service, zodat alle gegevens hand matig moeten worden gekopieerd naar een nieuwe service.

## <a name="model-3-hybrid"></a>Model 3: hybride

Een ander patroon voor het model leren van multitenancy is het combi neren van zowel de strategie per Tenant als voor de service per Tenant.

Door de twee patronen te mengen, kunnen de grootste tenants van een toepassing speciale services innemen, terwijl de lange staart van minder actieve, kleinere tenants in een gedeelde service indexen kan innemen. Dit model zorgt ervoor dat de grootste tenants voortdurend hoge prestaties van de service hebben terwijl u de kleinere tenants van alle onrustige neighbors kunt beveiligen.

Het implementeren van deze strategie is echter afhankelijk van de prognose bij het voors pellen van de tenants die een specifieke service vereisen tegenover een index in een gedeelde service. Toepassings complexiteit neemt toe met de nood zaak om beide multitenancys modellen te beheren.

## <a name="achieving-even-finer-granularity"></a>Nauw keurigere granulariteit bereiken

De bovenstaande ontwerp patronen voor het model leren van scenario's met meerdere tenants in azure Cognitive Search uitgaan van een uniform bereik waarbij elke Tenant een geheel exemplaar van een toepassing is. Toepassingen kunnen soms veel kleinere bereiken verwerken.

Als service-per-Tenant-en index-per-Tenant modellen niet voldoende kleine bereiken zijn, is het mogelijk om een index te model leren om een nog nauw keurigere mate van granulatie te bereiken.

Als u een enkele index anders wilt gebruiken voor verschillende client eindpunten, kan een veld worden toegevoegd aan een index die een bepaalde waarde voor elke mogelijke client aanduidt. Telkens wanneer een client Azure Cognitive Search aanroept om een index op te vragen of te wijzigen, geeft de code van de client toepassing de juiste waarde voor dat veld aan met behulp van de [filter](./query-odata-filter-orderby-syntax.md) functie van Azure Cognitive Search op het moment van de query.

Deze methode kan worden gebruikt voor het bezorgen van de functionaliteit van afzonderlijke gebruikers accounts, afzonderlijke machtigings niveaus en zelfs volledig gescheiden toepassingen.

> [!NOTE]
> Met behulp van de hierboven beschreven methode kunt u een enkele index configureren voor meerdere tenants die van invloed is op de relevantie van de zoek resultaten. Zoek relevantie scores worden berekend op het bereik op index niveau, niet op Tenant niveau, zodat alle gegevens van de tenants worden opgenomen in de onderliggende statistieken van de relevantie scores zoals term frequentie.
>

## <a name="next-steps"></a>Volgende stappen

Azure Cognitive Search is een fascinerende keuze voor veel toepassingen. Bij het evalueren van de verschillende ontwerp patronen voor multi tenant-toepassingen, moet u rekening houden met de [verschillende prijs categorieën](https://azure.microsoft.com/pricing/details/search/) en de bijbehorende [service limieten](search-limits-quotas-capacity.md) voor het Best passend maken van Azure Cognitive Search voor het aanpassen van werk belastingen en architecturen van elke omvang.