---
title: Service limieten voor lagen en sku's
titleSuffix: Azure Cognitive Search
description: Service limieten die worden gebruikt voor capaciteits planning en maximum aantal limieten voor aanvragen en antwoorden voor Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 994ed74750d159dfdb83259e9fe921f870ec2241
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99509364"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Servicelimieten in Azure Cognitive Search

De maximum limieten voor opslag, werk belastingen en hoeveel heden indexen en andere objecten zijn afhankelijk van of u Azure-Cognitive Search in de **gratis**, **basis**, **standaard** of **geoptimaliseerde** prijs categorieën voor opslag [inricht](search-create-service-portal.md) .

+ **Gratis** is een multi tenant-gedeelde service die wordt geleverd bij uw Azure-abonnement. 

+ **Basic** biedt specifieke computer bronnen voor productie werkbelastingen op een kleinere schaal, maar deelt een netwerk infrastructuur met andere tenants.

+ **Standaard** wordt uitgevoerd op toegewezen computers met meer opslag-en verwerkings capaciteit op elk niveau. Standard is beschikbaar op vier niveaus: S1, S2, S3 en S3 HD. S3 high density (S3 HD) is ontworpen voor [multitenancy](search-modeling-multitenant-saas-applications.md) en grote hoeveel heden kleine indices (3000 indexen per service). S3 HD biedt geen functie voor [indexering](search-indexer-overview.md) en gegevens opname moet gebruikmaken van api's die gegevens van bron naar index pushen. 

+ **Opslag geoptimaliseerd** wordt uitgevoerd op toegewezen computers met meer totale opslag, opslag bandbreedte en geheugen dan **standaard**. Deze laag is gericht op grote, Trag veranderende indexen. Geoptimaliseerde opslag is beschikbaar op twee niveaus: L1 en L2.

## <a name="subscription-limits"></a>Abonnementslimieten
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Opslag limieten
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Index limieten

| Resource | Gratis | Basis &nbsp; <sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Maximale aantal indexen |3 |5 of 15 |50 |200 |200 |1000 per partitie of 3000 per service |10 |10 |
| Maximum aantal eenvoudige velden per index |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Maximum aantal complexe verzamelings velden per index |40 |40 |40 |40 |40 |40 |40 |40 |
| Maximum aantal elementen in alle complexe verzamelingen per document &nbsp; <sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Maximale diepte van complexe velden |10 |10 |10 |10 |10 |10 |10 |10 |
| Maximum aantal [suggesties](/rest/api/searchservice/suggesters) per index |1 |1 |1 |1 |1 |1 |1 |1 |
| Maximum aantal [Score profielen](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) per index |100 |100 |100 |100 |100 |100 |100 |100 |
| Maximum aantal functies per profiel |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Basic-services die zijn gemaakt vóór december 2017 hebben lagere limieten (5 in plaats van 15) op indices. Basic-laag is de enige SKU met een ondergrens van 100 velden per index.

<sup>2</sup> er bestaat een bovengrens voor-elementen, omdat er een groot aantal is dat de opslag die nodig is voor uw index aanzienlijk toeneemt. Een element van een complexe verzameling wordt gedefinieerd als een lid van die verzameling. Als er bijvoorbeeld een [Hotel document met een verzameling complexe verzamelingen](search-howto-complex-data-types.md#indexing-complex-types)wordt aangenomen, wordt elke ruimte in de verzameling kamers als een element beschouwd. Tijdens het indexeren kan de indexerings engine veilig Maxi maal 3000 elementen in het hele document verwerken. [Deze limiet](search-api-migration.md#upgrade-to-2019-05-06) werd geïntroduceerd in `api-version=2019-05-06` en is alleen van toepassing op complexe verzamelingen en niet op teken reeks verzamelingen of complexe velden.

<a name="document-limits"></a>

## <a name="document-limits"></a>Document limieten 

Vanaf oktober 2018 zijn er geen limieten meer voor het aantal documenten voor nieuwe services die zijn gemaakt in een wille keurige laag (Basic, S1, S2, S3, S3 HD) in een wille keurige regio. Voor oudere services die vóór oktober 2018 zijn gemaakt, zijn mogelijk nog steeds limieten voor document aantallen van toepassing.

Als u wilt bepalen of uw service document limieten heeft, gebruikt u de [rest API service statistieken ophalen](/rest/api/searchservice/get-service-statistics). De document limieten worden weer gegeven in het antwoord, waarbij `null` geen limieten worden aangegeven.

> [!NOTE]
> Hoewel er geen document limieten zijn opgelegd door de service, is er een Shard limiet van ongeveer 24.000.000.000 documenten per index op basis-, S1-, S2-en S3-Zoek Services. Voor S3 HD is de limiet voor Shard 2.000.000.000 documenten per index. Elk element van een complexe verzameling telt als een afzonderlijk document in termen van Shard-limieten.

### <a name="document-size-limits-per-api-call"></a>Maximale document grootte per API-aanroep

De maximale document grootte bij het aanroepen van een index-API is ongeveer 16 MB.

De document grootte is in feite een limiet voor de grootte van de aanvraag tekst van de index-API. Omdat u een batch van meerdere documenten tegelijk kunt door geven aan de index-API, is de grootte van het aantal documenten in de batch realistisch. Voor een batch met één document is de maximale document grootte 16 MB JSON.

Houd bij het schatten van de document grootte alleen rekening met de velden die kunnen worden gebruikt door een zoek service. Binaire of afbeeldings gegevens in bron documenten moeten worden wegge laten uit uw berekeningen.  

## <a name="indexer-limits"></a>Limieten voor Indexeer functie

Er zijn maximale uitvoerings tijden beschikbaar om het saldo en de stabiliteit van de service als geheel te bieden, maar grotere gegevens sets hebben mogelijk meer indexerings tijd nodig dan het maximum aantal toestaat. Als een indexerings taak niet kan worden voltooid binnen de toegestane maximum tijd, voert u deze uit volgens een schema. De scheduler houdt de indexerings status bij. Als een geplande indexerings taak om welke reden dan ook wordt onderbroken, kan de Indexeer functie ophalen waar deze voor het laatst bij de volgende geplande uitvoering was gebleven.


| Resource | Gratis &nbsp; <sup>1</sup> | Basis &nbsp; <sup>2</sup>| S1 | S2 | S3 | S3 &nbsp; HD &nbsp; <sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Maximale aantal indexeerfuncties |3 |5 of 15|50 |200 |200 |N.v.t. |10 |10 |
| Maximale aantal gegevensbronnen |3 |5 of 15 |50 |200 |200 |N.v.t. |10 |10 |
| Maximum vaardig heden <sup>4</sup> |3 |5 of 15 |50 |200 |200 |N.v.t. |10 |10 |
| Maximale indexerings belasting per aanroep |10.000 documenten |Alleen beperkt door de maximale hoeveelheid documenten |Alleen beperkt door de maximale hoeveelheid documenten |Alleen beperkt door de maximale hoeveelheid documenten |Alleen beperkt door de maximale hoeveelheid documenten |N.v.t. |Geen limiet |Geen limiet |
| Minimale planning | 5 minuten |5 minuten |5 minuten |5 minuten |5 minuten |5 minuten |5 minuten | 5 minuten |
| Maximale uitvoerings tijd| 1-3 minuten |24 uur |24 uur |24 uur |24 uur |N.v.t.  |24 uur |24 uur |
| Maximale uitvoerings tijd voor Indexeer functies met een vaardig heden- <sup>5</sup> | 3-10 minuten |2 uur |2 uur |2 uur |2 uur |N.v.t.  |2 uur |2 uur |
| BLOB-Indexer: maximale grootte van BLOB, MB |16 |16 |128 |256 |256 |N.v.t.  |256 |256 |
| BLOB-Indexeer functie: maximum aantal tekens inhoud geëxtraheerd uit een BLOB |32.000 |64.000 |4 &nbsp; miljoen |8 &nbsp; miljoen |16 &nbsp; miljoen |N.v.t. |4 &nbsp; miljoen |4 &nbsp; miljoen |

<sup>1</sup> gratis services hebben de maximale uitvoerings tijd van de Indexeer functie van 3 minuten voor BLOB-bronnen en 1 minuut voor alle andere gegevens bronnen. Voor AI-indexering die in Cognitive Services aanroept, zijn gratis services beperkt tot 20 gratis trans acties per dag, waarbij een trans actie wordt gedefinieerd als een document dat door de verrijkings pijplijn is door gegeven.

<sup>2</sup> basis services die zijn gemaakt vóór december 2017 hebben lagere limieten (5 in plaats van 15) voor Indexeer functies, gegevens bronnen en vaardig heden.

<sup>3</sup> S3 HD services bevatten geen ondersteuning voor Indexeer functie.

<sup>4</sup> Maxi maal 30 vaardig heden per vaardig heden.

<sup>5</sup> AI-verrijking en afbeeldings analyse zijn reken intensief en verbruiken een onevenredige hoeveelheid beschik bare verwerkings kracht. De uitvoerings tijd voor deze werk belastingen is inge kort om andere taken in de wachtrij meer kans te geven om uit te voeren.

> [!NOTE]
> Zoals vermeld in de [index limieten](#index-limits), worden ook de bovengrens van 3000 elementen voor alle complexe verzamelingen per document afgedwongen, te beginnen met de nieuwste versie van de Ga-API die complexe typen () op een later niveau ondersteunt `2019-05-06` . Dit betekent dat als u een Indexeer functie hebt gemaakt met een eerdere API-versie, u deze limiet niet onderhevig hebt. Een Indexeer functie die is gemaakt met een eerdere API-versie en die vervolgens is bijgewerkt met een API `2019-05-06` -versie of hoger, wordt nog steeds **uitgesloten** van de limieten voor het behouden van de maximale compatibiliteit. Klanten moeten op de hoogte zijn van de nadelige gevolgen van het hebben van zeer grote complexe verzamelingen (zoals eerder aangegeven). we raden u aan om nieuwe Indexeer functies te maken met de nieuwste versie van de GA-API.

## <a name="shared-private-link-resource-limits"></a>Resource limieten voor gedeelde persoonlijke koppelingen

Indexeer functies hebben toegang tot andere Azure-bronnen [via privé-eind punten](search-indexer-howto-access-private.md) die worden beheerd via de resource-API van de [gedeelde persoonlijke koppeling](/rest/api/searchmanagement/sharedprivatelinkresources). In deze sectie worden de limieten beschreven die zijn gekoppeld aan deze mogelijkheid.

| Resource | Gratis | Basic | S1 | S2 | S3 | S3 HD | L1 | L2
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Ondersteuning voor het indexeren van persoonlijke eind punten | Nee | Ja | Ja | Ja | Ja | Nee | Ja | Ja |
| Ondersteuning voor privé-eind punten voor Indexeer functies met vaardig heden<sup>1</sup> | Nee | Nee | Nee | Ja | Ja | Nee | Ja | Ja |
| Maximum aantal privé-eind punten | N.v.t. | 10 of 30 | 100 | 400 | 400 | N.v.t. | 20 | 20 |
| Maximum aantal afzonderlijke resource typen<sup>2</sup> | N.v.t. | 4 | 7 | 15 | 15 | N.v.t. | 4 | 4 |

<sup>1</sup> AI-verrijking en afbeeldings analyse zijn reken intensief en verbruiken een onevenredige hoeveelheid beschik bare verwerkings kracht. Daarom worden particuliere verbindingen op lagere lagen uitgeschakeld om een nadelige invloed op de prestaties en stabiliteit van de zoek service zelf te voor komen.

<sup>2</sup> het aantal afzonderlijke resource typen wordt berekend als het aantal unieke `groupId` waarden dat wordt gebruikt voor alle gedeelde persoonlijke koppelings resources voor een bepaalde zoek service, onafhankelijk van de status van de resource.

## <a name="synonym-limits"></a>Synoniemen limieten

Het maximum aantal synoniemen toewijzingen varieert per laag. Elke regel kan Maxi maal 20 uitbrei dingen hebben, waarbij een uitbrei ding een gelijkwaardige term is. Als bijvoorbeeld ' kat ', ' Association ' met ' Kitty ', ' Feline ' en ' Felis ' (het genus voor katten) als drie uitbrei dingen worden beschouwd.

| Resource | Gratis | Basic | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Maximum aantal synoniemen |3 |3|5 |10 |20 |20 | 10 | 10 |
| Maximum aantal regels per kaart |5000 |20.000|20.000 |20.000 |20.000 |20.000 | 20.000 | 20.000  |

## <a name="data-limits-ai-enrichment"></a>Gegevens limieten (AI-verrijking)

Een [AI-verrijkings pijplijn](cognitive-search-concept-intro.md) die aanroepen naar een Text Analytics bron voor [entiteit herkenning](cognitive-search-skill-entity-recognition.md), [extractie van sleutel zinnen](cognitive-search-skill-keyphrases.md), [analyse van sentiment](cognitive-search-skill-sentiment.md), [taal detectie](cognitive-search-skill-language-detection.md)en [detectie van persoonlijke informatie](cognitive-search-skill-pii-detection.md) is onderhevig aan gegevens limieten. De maximale grootte van een record moet 50.000 tekens zijn, zoals gemeten door [`String.Length`](/dotnet/api/system.string.length) . Als u uw gegevens wilt opsplitsen voordat u deze naar de sentiment Analyzer verzendt, gebruikt u de functie [tekst splitsen](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Beperkings limieten

API-aanvragen worden beperkt naarmate het systeem de piek capaciteit benadert. Bandbreedte beperking werkt anders voor verschillende Api's. Query-Api's (zoeken/suggesties/automatisch volt ooien) en het indexeren van Api's worden dynamisch beperkt op basis van de belasting van de service. Index-Api's en service bewerkingen-API hebben limieten voor statisch aantal aanvragen. 

Limieten voor statische frequentie aanvragen voor bewerkingen met betrekking tot een index:

+ Lijst indexen (GET/Indexes): 3 per seconde per Zoek eenheid
+ Index ophalen (GET/Indexes/MyIndex): 10 per seconde per Zoek eenheid
+ Create Index (POST/Indexes): 12 per minuut per Zoek eenheid
+ Index maken of bijwerken (PUT/Indexes/MyIndex): 6 per seconde per Zoek eenheid
+ Index verwijderen (/Indexes/MyIndex verwijderen): 12 per minuut per Zoek eenheid 

Limieten voor statische frequentie aanvragen voor bewerkingen met betrekking tot een service:

+ Service statistieken (GET/servicestats): 4 per seconde per Zoek eenheid

## <a name="api-request-limits"></a>API-aanvraag limieten
* Maxi maal 16 MB per aanvraag <sup>1</sup>
* Maxi maal 8 KB URL-lengte
* Maxi maal 1000 documenten per batch met index-uploads, samen voegingen of verwijderingen
* Maxi maal 32 velden in de $orderby-component
* De maximale grootte van de zoek term is 32.766 bytes (32 KB min 2 bytes) van tekst UTF-8-code ring

<sup>1</sup> In Azure Cognitive Search is de hoofd tekst van een aanvraag onderhevig aan een bovengrens van 16 MB, waardoor een praktische limiet wordt opgelegd aan de inhoud van afzonderlijke velden of verzamelingen die niet anderszins beperkt zijn door theoretische limieten (Zie [ondersteunde gegevens typen](/rest/api/searchservice/supported-data-types) voor meer informatie over veld samenstelling en beperkingen).

## <a name="api-response-limits"></a>API-antwoord limieten
* Maxi maal 1000 documenten geretourneerd per pagina met zoek resultaten
* Maxi maal 100 suggesties die worden geretourneerd per API-aanvraag Voorst Ellen

## <a name="api-key-limits"></a>API-sleutel limieten
API-sleutels worden gebruikt voor service verificatie. Er zijn twee typen. Beheerders sleutels worden opgegeven in de aanvraag header en krijgen volledige lees-/schrijftoegang tot de service. Query sleutels zijn alleen-lezen, opgegeven voor de URL en worden meestal gedistribueerd naar client toepassingen.

* Maxi maal 2 beheer sleutels per service
* Maxi maal 50 query sleutels per service
