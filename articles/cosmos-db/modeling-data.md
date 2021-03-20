---
title: Model gegevens in Azure Cosmos DB
titleSuffix: Azure Cosmos DB
description: Meer informatie over gegevens modellering in NoSQL-data bases, verschillen tussen het model leren van gegevens in een relationele data base en een document database.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: a141177846def9c94216684c1083d0d336eeda1e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93333236"
---
# <a name="data-modeling-in-azure-cosmos-db"></a>Gegevens modellering in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

U kunt zonder schema-Free data bases, zoals Azure Cosmos DB, het eenvoudig maken om ongestructureerde en semi-gestructureerde gegevens op te slaan en op te vragen, maar u kunt het beste even best Eden aan het gebruik van uw gegevens model om optimaal te profiteren van de prestaties en schaal baarheid en de laagste kosten.

Hoe worden gegevens opgeslagen? Hoe gaat uw toepassing ophalen en query's uitvoeren? Is uw toepassing lezen/zwaar of schrijven-zwaar?

Na het lezen van dit artikel kunt u de volgende vragen beantwoorden:

* Wat is gegevens modellering en waarom moet ik dit doen?
* Hoe worden model gegevens in Azure Cosmos DB afwijkend van een relationele data base?
* Hoe kan ik Express-gegevens relaties in een niet-relationele data base?
* Wanneer kan ik gegevens insluiten en wanneer kan ik een koppeling maken naar gegevens?

## <a name="embedding-data"></a>Gegevens insluiten

Wanneer u gegevens modelleert in Azure Cosmos DB probeert u uw entiteiten te behandelen als **zelfstandige items** die worden weer gegeven als JSON-documenten.

Laten we eerst zien hoe we gegevens in een relationele data base kunnen model leren. In het volgende voor beeld ziet u hoe een persoon kan worden opgeslagen in een relationele data base.

:::image type="content" source="./media/sql-api-modeling-data/relational-data-model.png" alt-text="Relationeel database model" border="false":::

Bij het werken met relationele data bases is de strategie het normaliseren van al uw gegevens. Het normaliseren van uw gegevens omvat meestal het nemen van een entiteit, zoals een persoon, en het opsplitsen daarvan in discrete onderdelen. In het bovenstaande voor beeld kan een persoon meerdere contact gegevens records hebben, evenals meerdere adres records. De contact gegevens kunnen verder worden opgesplitst door algemene velden, zoals een type, verder uit te pakken. Hetzelfde geldt voor het adres, elke record kan van het type *thuis* of *zakelijk* zijn.

De richt zich op de lokale locatie bij het normaliseren van gegevens is om te **voor komen dat er redundante gegevens worden opgeslagen** op elke record en om te verwijzen naar gegevens. In dit voor beeld, om een persoon te lezen met al hun contact gegevens en-adressen, moet u samen voegingen gebruiken om uw gegevens in runtime effectief te maken (of te denormaliseren).

```sql
SELECT p.FirstName, p.LastName, a.City, cd.Detail
FROM Person p
JOIN ContactDetail cd ON cd.PersonId = p.Id
JOIN ContactDetailType cdt ON cdt.Id = cd.TypeId
JOIN Address a ON a.PersonId = p.Id
```

Voor het bijwerken van één persoon met hun contact gegevens en-adressen is schrijf bewerkingen in veel afzonderlijke tabellen vereist.

Laten we nu eens kijken hoe we dezelfde gegevens als een op zichzelf staande entiteit in Azure Cosmos DB model leren.

```json
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "addresses": [
        {
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zip": 98012
        }
    ],
    "contactDetails": [
        {"email": "thomas@andersen.com"},
        {"phone": "+1 555 555-5555", "extension": 5555}
    ]
}
```

Met behulp van de bovenstaande benadering hebben we de persoons record **genormaliseerd** door alle informatie met betrekking tot deze persoon, zoals de contact gegevens en-adressen, in te **sluiten** in *één JSON* -document.
Omdat we niet zijn beperkt tot een vast schema, hebben we de flexibiliteit om zaken te doen, zoals het volledig hebben van contact gegevens van verschillende vormen.

Het ophalen van een volledige persoons record uit de data base is nu een **enkele Lees bewerking** voor één container en voor één item. Het bijwerken van een persoons record, met hun contact gegevens en-adressen, is ook een **enkele schrijf bewerking** voor één item.

Door gegevens te denormaliseren, moet uw toepassing mogelijk minder query's en updates uitvoeren om veelvoorkomende bewerkingen te volt ooien.

### <a name="when-to-embed"></a>Wanneer insluiten

In het algemeen gebruikt u Inge sloten gegevens modellen wanneer:

* Er zijn **relaties** tussen entiteiten.
* Er zijn **een-op-veel-** relaties tussen entiteiten.
* Er zijn Inge sloten gegevens die niet **regel matig veranderen**.
* Er zijn Inge sloten gegevens die niet worden uitgebreid **zonder gebonden**.
* Er zijn Inge sloten gegevens die **vaak samen worden opgevraagd**.

> [!NOTE]
> Normaal gesp roken gegevens modellen bieden betere **Lees** prestaties.

### <a name="when-not-to-embed"></a>Wanneer niet worden inge sloten

De regel van de vuist in Azure Cosmos DB is om alles te denormaliseren en alle gegevens in één item in te sluiten, maar dit kan leiden tot enkele situaties die moeten worden vermeden.

Neem dit JSON-fragment op.

```json
{
    "id": "1",
    "name": "What's new in the coolest Cloud",
    "summary": "A blog post by someone real famous",
    "comments": [
        {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
        {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
        …
        {"id": 100001, "author": "jane", "comment": "and on we go ..."},
        …
        {"id": 1000000001, "author": "angry", "comment": "blah angry blah angry"},
        …
        {"id": ∞ + 1, "author": "bored", "comment": "oh man, will this ever end?"},
    ]
}
```

Dit kan zijn dat een post-entiteit met Inge sloten opmerkingen eruit zou zien als we een typische blog of CMS-systeem zouden model leren. Het probleem met dit voor beeld is dat de opmerkingen matrix niet is **gebonden**, wat inhoudt dat er geen (praktische) limiet is voor het aantal opmerkingen dat één post kan hebben. Dit kan een probleem zijn omdat de grootte van het item oneindig groot kan worden uitgebreid.

Naarmate de grootte van het item groeit, is de mogelijkheid om gegevens over de kabel te verzenden en om het item op de juiste schaal te lezen en bij te werken, van invloed.

In dit geval is het beter om rekening te houden met het volgende gegevens model.

```json
Post item:
{
    "id": "1",
    "name": "What's new in the coolest Cloud",
    "summary": "A blog post by someone real famous",
    "recentComments": [
        {"id": 1, "author": "anon", "comment": "something useful, I'm sure"},
        {"id": 2, "author": "bob", "comment": "wisdom from the interwebs"},
        {"id": 3, "author": "jane", "comment": "....."}
    ]
}

Comment items:
{
    "postId": "1"
    "comments": [
        {"id": 4, "author": "anon", "comment": "more goodness"},
        {"id": 5, "author": "bob", "comment": "tails from the field"},
        ...
        {"id": 99, "author": "angry", "comment": "blah angry blah angry"}
    ]
},
{
    "postId": "1"
    "comments": [
        {"id": 100, "author": "anon", "comment": "yet more"},
        ...
        {"id": 199, "author": "bored", "comment": "will this ever end?"}
    ]
}
```

Dit model bevat de drie meest recente opmerkingen die zijn Inge sloten in de post-container, een matrix met een vaste set kenmerken. De overige opmerkingen worden gegroepeerd in batches van 100 opmerkingen en opgeslagen als afzonderlijke items. De grootte van de batch is gekozen als 100 omdat de gebruiker met de fictieve toepassing 100 opmerkingen tegelijk kan laden.  

Een andere geval waarin het insluiten van gegevens geen goed idee is wanneer de Inge sloten gegevens vaak worden gebruikt in verschillende items en regel matig worden gewijzigd.

Neem dit JSON-fragment op.

```json
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "holdings": [
        {
            "numberHeld": 100,
            "stock": { "symbol": "zaza", "open": 1, "high": 2, "low": 0.5 }
        },
        {
            "numberHeld": 50,
            "stock": { "symbol": "xcxc", "open": 89, "high": 93.24, "low": 88.87 }
        }
    ]
}
```

Dit kan de aandelen portefeuille van een persoon vertegenwoordigen. We hebben ervoor gekozen de aandelen informatie in elk portfolio document in te sluiten. In een omgeving waarin gerelateerde gegevens regel matig worden gewijzigd, zoals een aandelen handels toepassing, wordt het insluiten van gegevens die regel matig worden gewijzigd, al dan niet telkens wanneer een aandeel wordt verhandeld, bijgewerkt.

Voorraad *zazaen* kunnen veel honderden keer per dag worden verhandeld en duizenden gebruikers kunnen *Zaza* op hun portefeuille hebben. Met een gegevens model zoals hierboven zou het een groot aantal duizenden portefeuille documenten op elke dag moeten bijwerken, waardoor ze niet goed kunnen worden geschaald.

## <a name="referencing-data"></a>Verwijzen naar gegevens

Het insluiten van gegevens werkt goed voor veel gevallen, maar er zijn scenario's bij het denormaliseren van uw gegevens, waardoor er meer problemen zijn dan het waard is. Wat doen we nu?

Relationele data bases zijn niet de enige plaats waar u relaties tussen entiteiten kunt maken. In een document database kunt u gegevens in één document hebben die betrekking hebben op gegevens in andere documenten. We raden u aan om geen systemen te bouwen die beter geschikt zijn voor een relationele data base in Azure Cosmos DB of een andere document database, maar eenvoudige relaties zijn prima en kunnen nuttig zijn.

In de JSON hieronder hebben we gekozen om het voor beeld van een aandelen portefeuille van eerder te gebruiken, maar deze keer verwijzen we naar het voorraad item op de Port Folio in plaats van het in te sluiten. Op deze manier, wanneer de voorraad items regel matig worden gewijzigd, is het enige document dat moet worden bijgewerkt, het document met één aandeel.

```json
Person document:
{
    "id": "1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "holdings": [
        { "numberHeld":  100, "stockId": 1},
        { "numberHeld":  50, "stockId": 2}
    ]
}

Stock documents:
{
    "id": "1",
    "symbol": "zaza",
    "open": 1,
    "high": 2,
    "low": 0.5,
    "vol": 11970000,
    "mkt-cap": 42000000,
    "pe": 5.89
},
{
    "id": "2",
    "symbol": "xcxc",
    "open": 89,
    "high": 93.24,
    "low": 88.87,
    "vol": 2970200,
    "mkt-cap": 1005000,
    "pe": 75.82
}
```

Een onmiddellijke neerwaartse aanpak van deze benadering is dat als uw toepassing is vereist voor het weer geven van informatie over elk aandeel dat wordt bewaard bij het weer geven van de Port Folio van een persoon. in dit geval moet u meerdere trips naar de data base maken om de informatie voor elk voorraad document te laden. Hier hebben we een beslissing genomen voor het verbeteren van de efficiëntie van schrijf bewerkingen, die regel matig worden uitgevoerd, maar op de Lees bewerkingen die mogelijk minder invloed hebben op de prestaties van dit specifieke systeem.

> [!NOTE]
> Genormaliseerde gegevens modellen **kunnen meer retouren** naar de server vereisen.

### <a name="what-about-foreign-keys"></a>Hoe zit het met refererende sleutels?

Omdat er momenteel geen concept van een beperking, refererende sleutel of anders is, zijn de relaties tussen documenten die u in documenten hebt, effectief "zwakke koppelingen" en worden niet door de data base zelf gecontroleerd. Als u er zeker van wilt zijn dat de gegevens waarnaar wordt verwezen in werkelijkheid bestaan, moet u dit doen in uw toepassing of via het gebruik van triggers aan de server zijde of opgeslagen procedures op Azure Cosmos DB.

### <a name="when-to-reference"></a>Waarnaar moet worden verwezen

In het algemeen gebruikt u genormaliseerde gegevens modellen als:

* **Een-op-veel-** relaties vertegenwoordigen.
* **Veel-op-veel** -relaties vertegenwoordigen.
* Vaak gerelateerde gegevens **wijzigingen**.
* Gegevens waarnaar wordt verwezen, kunnen **onbegrensd** zijn.

> [!NOTE]
> Normaal gesp roken biedt doorgaans betere **Schrijf** prestaties.

### <a name="where-do-i-put-the-relationship"></a>Waar kan ik de relatie plaatsen?

De groei van de relatie helpt te bepalen in welk document de referentie moet worden opgeslagen.

Als we de JSON hieronder bekijken, modellen uitgevers en boeken.

```json
Publisher document:
{
    "id": "mspress",
    "name": "Microsoft Press",
    "books": [ 1, 2, 3, ..., 100, ..., 1000]
}

Book documents:
{"id": "1", "name": "Azure Cosmos DB 101" }
{"id": "2", "name": "Azure Cosmos DB for RDBMS Users" }
{"id": "3", "name": "Taking over the world one JSON doc at a time" }
...
{"id": "100", "name": "Learn about Azure Cosmos DB" }
...
{"id": "1000", "name": "Deep Dive into Azure Cosmos DB" }
```

Als het aantal boeken per uitgever klein is met beperkte groei, kan het handig zijn om de boek verwijzing in het Publisher-document op te slaan. Als het aantal boeken per uitgever echter niet is gebonden, zou dit gegevens model kunnen leiden tot onveranderlijke en groeiende matrices, zoals in het voor beeld van een Publisher-document hierboven.

Het omschakelen van dingen rond een beetje zou leiden tot een model dat nog steeds dezelfde gegevens bevat, maar deze grote, onveranderlijke verzamelingen vermijdt.

```json
Publisher document:
{
    "id": "mspress",
    "name": "Microsoft Press"
}

Book documents:
{"id": "1","name": "Azure Cosmos DB 101", "pub-id": "mspress"}
{"id": "2","name": "Azure Cosmos DB for RDBMS Users", "pub-id": "mspress"}
{"id": "3","name": "Taking over the world one JSON doc at a time"}
...
{"id": "100","name": "Learn about Azure Cosmos DB", "pub-id": "mspress"}
...
{"id": "1000","name": "Deep Dive into Azure Cosmos DB", "pub-id": "mspress"}
```

In het bovenstaande voor beeld hebben we de niet-gebonden verzameling verwijderd uit het Publisher-document. In plaats daarvan hebben we een verwijzing naar de uitgever in elk boek document.

### <a name="how-do-i-model-manymany-relationships"></a>Hoe kan ik model: veel relaties?

In een relationele data base zijn *veel* relaties vaak gemodelleerd met samenvoeg tabellen, die alleen worden gekoppeld aan records uit andere tabellen.


:::image type="content" source="./media/sql-api-modeling-data/join-table.png" alt-text="Tabellen koppelen" border="false":::

Misschien bent u geneigd om hetzelfde te repliceren met behulp van documenten en een gegevens model te produceren dat er ongeveer als volgt uitziet.

```json
Author documents:
{"id": "a1", "name": "Thomas Andersen" }
{"id": "a2", "name": "William Wakefield" }

Book documents:
{"id": "b1", "name": "Azure Cosmos DB 101" }
{"id": "b2", "name": "Azure Cosmos DB for RDBMS Users" }
{"id": "b3", "name": "Taking over the world one JSON doc at a time" }
{"id": "b4", "name": "Learn about Azure Cosmos DB" }
{"id": "b5", "name": "Deep Dive into Azure Cosmos DB" }

Joining documents:
{"authorId": "a1", "bookId": "b1" }
{"authorId": "a2", "bookId": "b1" }
{"authorId": "a1", "bookId": "b2" }
{"authorId": "a1", "bookId": "b3" }
```

Dit zou werken. Het laden van een auteur met hun boeken of het laden van een boek met de auteur, vereist echter altijd ten minste twee extra query's voor de data base. Een query voor het lid van het document en vervolgens een andere query voor het ophalen van het werkelijke document dat wordt toegevoegd.

Als al deze samenvoegings tabel wordt uitgevoerd, worden twee gegevens van elkaar gelijmd en moet u daarom niet volledig verwijderen?
Houd rekening met het volgende.

```json
Author documents:
{"id": "a1", "name": "Thomas Andersen", "books": ["b1", "b2", "b3"]}
{"id": "a2", "name": "William Wakefield", "books": ["b1", "b4"]}

Book documents:
{"id": "b1", "name": "Azure Cosmos DB 101", "authors": ["a1", "a2"]}
{"id": "b2", "name": "Azure Cosmos DB for RDBMS Users", "authors": ["a1"]}
{"id": "b3", "name": "Learn about Azure Cosmos DB", "authors": ["a1"]}
{"id": "b4", "name": "Deep Dive into Azure Cosmos DB", "authors": ["a2"]}
```

Nu als ik een auteur had, weet ik dan onmiddellijk welke boeken ze hebben geschreven en omgekeerd als ik een boek document had geladen ik weet wat de Id's van de auteur (s) zijn. Hiermee slaat u de tussenliggende query op voor de samenvoegings tabel om het aantal server retouren te verminderen dat uw toepassing moet maken.

## <a name="hybrid-data-models"></a>Hybride gegevens modellen

We hebben nu het insluiten (of denormaliseren) doorstaan en er wordt verwezen naar (of genormaliseerd) gegevens, die elk hun eigen kant hebben en elke keer heeft geknoeid zoals we hebben gezien.

Het hoeft niet altijd of te zijn, niet Scared om dingen een beetje te mengen.

Op basis van de specifieke gebruiks patronen en werk belastingen van uw toepassing zijn er situaties waarin het combi neren van Inge sloten en gerefereerde gegevens zinvol is en kan leiden tot een eenvoudigere toepassings logica met minder server retouren, terwijl er nog steeds een goed prestatie niveau wordt gehandhaafd.

Bekijk de volgende JSON.

```json
Author documents:
{
    "id": "a1",
    "firstName": "Thomas",
    "lastName": "Andersen",
    "countOfBooks": 3,
    "books": ["b1", "b2", "b3"],
    "images": [
        {"thumbnail": "https://....png"}
        {"profile": "https://....png"}
        {"large": "https://....png"}
    ]
},
{
    "id": "a2",
    "firstName": "William",
    "lastName": "Wakefield",
    "countOfBooks": 1,
    "books": ["b1"],
    "images": [
        {"thumbnail": "https://....png"}
    ]
}

Book documents:
{
    "id": "b1",
    "name": "Azure Cosmos DB 101",
    "authors": [
        {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
        {"id": "a2", "name": "William Wakefield", "thumbnailUrl": "https://....png"}
    ]
},
{
    "id": "b2",
    "name": "Azure Cosmos DB for RDBMS Users",
    "authors": [
        {"id": "a1", "name": "Thomas Andersen", "thumbnailUrl": "https://....png"},
    ]
}
```

Hier hebben we (voornamelijk) het Inge sloten model gevolgd, waar gegevens uit andere entiteiten zijn Inge sloten in het document op het hoogste niveau, maar er wordt naar andere gegevens verwezen.

Als u het boek document bekijkt, kunnen we enkele interessante velden zien wanneer we de matrix van auteurs bekijken. Er is een `id` veld dat het veld wordt gebruikt om terug te verwijzen naar een auteur-document, de standaard procedure in een genormaliseerd model, maar we hebben ook `name` en `thumbnailUrl` . We hebben `id` de toepassing kunnen blijven gebruiken om aanvullende informatie te verkrijgen die hij nodig heeft uit het respectieve auteurs document met behulp van de "link", maar omdat in onze toepassing de naam van de auteur en een miniatuur afbeelding worden weer gegeven met elk boek dat wordt weer geven, kunnen we een retour naar de server per boek in een lijst opslaan door **bepaalde** gegevens van de auteur te denormaliseren.

Als de naam van de auteur is gewijzigd of als hij of zij de foto wil bijwerken, moeten we dan ook elk boek dat ze ooit hebben gepubliceerd, maar voor onze toepassing bijwerken, op basis van de veronderstelling dat auteurs hun namen niet vaak wijzigen, is dit een acceptabel ontwerp besluit.  

In het voor beeld zijn er **vooraf berekende aggregaties-** waarden voor het besparen van de dure verwerking van een lees bewerking. In het voor beeld zijn enkele van de Inge sloten gegevens in het auteurs document gegevens die tijdens runtime worden berekend. Telkens wanneer een nieuw boek wordt gepubliceerd, wordt een boek document gemaakt **en** wordt het veld countOfBooks ingesteld op een berekende waarde op basis van het aantal boek documenten dat voor een bepaalde auteur bestaat. Deze Optima Lise ring is goed in Lees bare systemen waar we berekeningen kunnen uitvoeren bij schrijf bewerkingen om Lees bewerkingen te optimaliseren.

De mogelijkheid om een model met vooraf berekende velden te maken, wordt mogelijk gemaakt omdat Azure Cosmos DB **meerdere document transacties** ondersteunt. Veel NoSQL-archieven kunnen geen trans acties uitvoeren voor documenten en daarom moeten ontwerp beslissingen worden genomen, zoals ' altijd insluiten alles ', als gevolg van deze beperking. Met Azure Cosmos DB kunt u triggers aan de server zijde of opgeslagen procedures gebruiken die boeken invoegen en auteurs bijwerken in een zure trans actie. Nu **hoeft u niet alles** in één document in te sluiten om er zeker van te zijn dat uw gegevens consistent blijven.

## <a name="distinguishing-between-different-document-types"></a>Onderscheiden van verschillende document typen

In sommige scenario's wilt u mogelijk verschillende document typen combi neren in dezelfde verzameling. Dit is meestal het geval wanneer u wilt dat er meerdere, gerelateerde documenten zich in dezelfde [partitie](partitioning-overview.md)bevinden. U kunt bijvoorbeeld zowel boeken als boek beoordelingen in dezelfde verzameling plaatsen en partitioneren door `bookId` . In dat geval wilt u meestal aan uw documenten toevoegen met een veld dat het type identificeert om ze te onderscheiden.

```json
Book documents:
{
    "id": "b1",
    "name": "Azure Cosmos DB 101",
    "bookId": "b1",
    "type": "book"
}

Review documents:
{
    "id": "r1",
    "content": "This book is awesome",
    "bookId": "b1",
    "type": "review"
},
{
    "id": "r2",
    "content": "Best book ever!",
    "bookId": "b1",
    "type": "review"
}
```

## <a name="next-steps"></a>Volgende stappen

De grootste Takeaways van dit artikel is om te begrijpen dat gegevens modellering in een schema vrije wereld net zo belang rijk is als ooit.

Net zoals er geen enkele manier is om een stukje gegevens op een scherm weer te geven, is er geen enkele manier om uw gegevens te model leren. U moet inzicht hebben in uw toepassing en bepalen hoe de gegevens worden geproduceerd, gebruikt en verwerkt. Door enkele van de richt lijnen die hier worden gepresenteerd toe te passen, kunt u instellen dat u een model maakt dat de onmiddellijke behoeften van uw toepassing versnelt. Als uw toepassingen moeten worden gewijzigd, kunt u gebruikmaken van de flexibiliteit van een schema-gratis data base, zodat u deze wijziging gemakkelijk en uw gegevens model eenvoudig ontwikkelt.

Raadpleeg de [documentatie](https://azure.microsoft.com/documentation/services/cosmos-db/) pagina van de service voor meer informatie over Azure Cosmos db.

Als u wilt weten hoe u uw gegevens op meerdere partities kunt Shard, raadpleegt u [gegevens partitioneren in azure Cosmos DB](partitioning-overview.md).

Raadpleeg [ gegevens modellering en partitioneren-een Real-World voor beeld](how-to-model-partition-example.md)voor meer informatie over het model leren en partitioneren van gegevens op Azure Cosmos DB met behulp van een Real-World-voor beeld.
