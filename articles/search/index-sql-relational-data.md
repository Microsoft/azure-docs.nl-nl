---
title: Relationele SQL-gegevens model leren voor importeren en indexeren
titleSuffix: Azure Cognitive Search
description: Meer informatie over het model leren van relationele gegevens, ongebruikelijk in een platte resultatenset voor indexering en zoeken in volledige tekst in azure Cognitive Search.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 6c70b42e7d0f647a3b2b60d29b5098a791e4975f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88924517"
---
# <a name="how-to-model-relational-sql-data-for-import-and-indexing-in-azure-cognitive-search"></a>Relationele SQL-gegevens model leren voor importeren en indexeren in azure Cognitive Search

Azure Cognitive Search accepteert een vlakke rijenset als invoer voor de [Indexing-pijp lijn](search-what-is-an-index.md). Als uw bron gegevens afkomstig zijn uit gekoppelde tabellen in een relationele data base van SQL Server, wordt in dit artikel uitgelegd hoe de resultatenset moet worden samengesteld en hoe een bovenliggende/onderliggende relatie kan worden model leren in een Azure Cognitive Search-index.

Als illustratie verwijzen we naar een hypothetische Hotels-Data Base op basis van [demo gegevens](https://github.com/Azure-Samples/azure-search-sample-data/tree/master/hotels). Ga ervan uit dat de data base bestaat uit een Hotels $-tabel met 50 hotels en een tabel van kamers $ met kamers van verschillende typen, tarieven en voorzieningen, voor een totaal van 750 kamers. Er is een een-op-veel-relatie tussen de tabellen. In onze benadering levert een weer gave de query die 50 rijen, één rij per Hotel retourneert, met bijbehorende room-details die zijn Inge sloten in elke rij.

   ![Tabellen en weer gaven in de hotels-data base](media/index-sql-relational-data/hotels-database-tables-view.png "Tabellen en weer gaven in de hotels-data base")


## <a name="the-problem-of-denormalized-data"></a>Het probleem van Gedenormaliseerde gegevens

Een van de uitdagingen bij het werken met een-op-veel-relaties is dat standaard query's die zijn gebaseerd op gekoppelde tabellen, Gedenormaliseerde gegevens retour neren die niet goed werken in een Azure Cognitive Search scenario. Bekijk het volgende voor beeld dat hotels en kamers samenvoegt.

```sql
SELECT * FROM Hotels$
INNER JOIN Rooms$
ON Rooms$.HotelID = Hotels$.HotelID
```
De resultaten van deze query retour neren alle velden in het Hotel, gevolgd door alle room-velden, met de voorbereidende gegevens van het hotel die worden herhaald voor elke kamer waarde.

   ![Gedenormaliseerde gegevens, redundante Hotel gegevens wanneer room-velden worden toegevoegd](media/index-sql-relational-data/denormalize-data-query.png "Gedenormaliseerde gegevens, redundante Hotel gegevens wanneer room-velden worden toegevoegd")


Hoewel deze query op het Opper vlak slaagt (waarbij alle gegevens in een vlakke rij-set worden verstrekt), mislukt het leveren van de juiste document structuur voor de verwachte Zoek ervaring. Tijdens het indexeren maakt Azure Cognitive Search één Zoek document voor elke rij die wordt opgenomen. Als uw zoek documenten lijken op de bovenstaande resultaten, hebt u dubbele items gezien-zeven afzonderlijke documenten voor het dubbele koepel Hotel alleen. Een query op ' hotels in Florida ' zou zeven resultaten retour neren voor alleen het dubbele koepel-Hotel, waardoor andere relevante Hotels dieper in de zoek resultaten worden verdeeld.

Als u de verwachte ervaring van één document per Hotel wilt ontvangen, moet u een rijenset op de juiste granulatie opgeven, maar met volledige informatie. Gelukkig kunt u dit eenvoudig doen door de technieken in dit artikel te hand nemen.

## <a name="define-a-query-that-returns-embedded-json"></a>Een query definiëren die een Inge sloten JSON retourneert

Als u de verwachte Zoek ervaring wilt bieden, moet uw gegevensset bestaan uit één rij voor elk zoek document in azure Cognitive Search. In ons voor beeld hebben we één rij voor elk Hotel nodig, maar we willen dat onze gebruikers ook kunnen zoeken op andere velden die betrekking hebben op andere ruimten, zoals het nacht tempo, de grootte en het aantal bedden, of een weer gave van het strand, die allemaal deel uitmaken van een kamer detail.

De oplossing is het vastleggen van de ruimte Details als geneste JSON en de JSON-structuur vervolgens in een veld in een weer gave invoegen, zoals in de tweede stap wordt weer gegeven. 

1. Stel dat u twee gekoppelde tabellen hebt, hotels $ en kamers $, die informatie bevatten over 50 hotels en 750 kamers, en die zijn gekoppeld aan het veld HotelID. Deze tabellen bevatten individueel 50 hotels en 750 gerelateerde kamers.

    ```sql
    CREATE TABLE [dbo].[Hotels$](
      [HotelID] [nchar](10) NOT NULL,
      [HotelName] [nvarchar](255) NULL,
      [Description] [nvarchar](max) NULL,
      [Description_fr] [nvarchar](max) NULL,
      [Category] [nvarchar](255) NULL,
      [Tags] [nvarchar](255) NULL,
      [ParkingIncluded] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [LastRenovationDate] [smalldatetime] NULL,
      [Rating] [float] NULL,
      [StreetAddress] [nvarchar](255) NULL,
      [City] [nvarchar](255) NULL,
      [State] [nvarchar](255) NULL,
      [ZipCode] [nvarchar](255) NULL,
      [GeoCoordinates] [nvarchar](255) NULL
    ) ON [PRIMARY] TEXTIMAGE_ON [PRIMARY]
    GO

    CREATE TABLE [dbo].[Rooms$](
      [HotelID] [nchar](10) NULL,
      [Description] [nvarchar](255) NULL,
      [Description_fr] [nvarchar](255) NULL,
      [Type] [nvarchar](255) NULL,
      [BaseRate] [float] NULL,
      [BedOptions] [nvarchar](255) NULL,
      [SleepsCount] [float] NULL,
      [SmokingAllowed] [float] NULL,
      [Tags] [nvarchar](255) NULL
    ) ON [PRIMARY]
    GO
    ```

2. Een weer gave maken die bestaat uit alle velden in de bovenliggende tabel ( `SELECT * from dbo.Hotels$` ), met toevoeging van een nieuw veld *ruimten* die de uitvoer van een geneste query bevat. Een **for JSON auto** -component voor `SELECT * from dbo.Rooms$` de structuur van de uitvoer als JSON. 

     ```sql
   CREATE VIEW [dbo].[HotelRooms]
   AS
   SELECT *, (SELECT *
              FROM dbo.Rooms$
              WHERE dbo.Rooms$.HotelID = dbo.Hotels$.HotelID FOR JSON AUTO) AS Rooms
   FROM dbo.Hotels$
   GO
   ```

   In de volgende scherm afbeelding ziet u de resulterende weer gave, met het veld ruimtes van de *kamers* onderaan. Het veld *ruimtes* bestaat alleen in de weer gave HotelRooms.

   ![HotelRooms weer geven](media/index-sql-relational-data/hotelsrooms-view.png "HoteRooms weer geven")

1. Uitvoeren `SELECT * FROM dbo.HotelRooms` om de rij-set op te halen. Met deze query worden 50 rijen, één per Hotel, met gekoppelde room-informatie als JSON-verzameling geretourneerd. 

   ![Rijenset vanuit de weer gave HotelRooms](media/index-sql-relational-data/hotelrooms-rowset.png "Rijenset vanuit de weer gave HotelRooms")

Deze rijenset kan nu worden geïmporteerd in azure Cognitive Search.

> [!NOTE]
> Bij deze benadering wordt ervan uitgegaan dat de Inge sloten JSON zich onder de [maximum limieten voor kolom grootte van SQL Server](/sql/sql-server/maximum-capacity-specifications-for-sql-server)bevindt. 

 ## <a name="use-a-complex-collection-for-the-many-side-of-a-one-to-many-relationship"></a>Gebruik een complexe verzameling voor de ' veel'-zijde van een een-op-veel-relatie

Maak op de Azure Cognitive Search-zijde een index schema waarmee de een-op-veel-relatie wordt gemodelleerd met geneste JSON. De resultatenset die u in de vorige sectie hebt gemaakt, komt doorgaans overeen met het hieronder opgegeven index schema (we knippen enkele velden voor de boog).

Het volgende voor beeld is vergelijkbaar met het voor beeld in het model leren van [complexe gegevens typen](search-howto-complex-data-types.md#creating-complex-fields). De structuur van de *lokalen* , die de focus heeft van dit artikel, bevindt zich in de verzameling velden van een index met de naam *Hotels*. In dit voor beeld wordt ook een complex type voor het *adres* weer gegeven. Dit verschilt van *kamers* in dat het bestaat uit een vaste set items, in tegens telling tot het veelvoud, een wille keurig aantal items dat is toegestaan in een verzameling.

```json
{
  "name": "hotels",
  "fields": [
    { "name": "HotelId", "type": "Edm.String", "key": true, "filterable": true },
    { "name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
    { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
    { "name": "Category", "type": "Edm.String", "searchable": true, "filterable": false },
    { "name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "facetable": true },
    { "name": "Address", "type": "Edm.ComplexType",
      "fields": [
        { "name": "StreetAddress", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "searchable": true },
        { "name": "City", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true },
        { "name": "StateProvince", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true }
      ]
    },
    { "name": "Rooms", "type": "Collection(Edm.ComplexType)",
      "fields": [
        { "name": "Description", "type": "Edm.String", "searchable": true, "analyzer": "en.lucene" },
        { "name": "Description_fr", "type": "Edm.String", "searchable": true, "analyzer": "fr.lucene" },
        { "name": "Type", "type": "Edm.String", "searchable": true },
        { "name": "BaseRate", "type": "Edm.Double", "filterable": true, "facetable": true },
        { "name": "BedOptions", "type": "Edm.String", "searchable": true, "filterable": true, "facetable": true },
        { "name": "SleepsCount", "type": "Edm.Int32", "filterable": true, "facetable": true },
        { "name": "SmokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true },
        { "name": "Tags", "type": "Edm.Collection", "searchable": true }
      ]
    }
  ]
}
```

Gezien de vorige resultatenset en het bovenstaande index schema, hebt u alle vereiste onderdelen voor een geslaagde indexerings bewerking. De samengevoegde gegevensset voldoet aan de vereisten voor indexering, maar behoudt gedetailleerde informatie. In de Azure Cognitive Search-index vallen de zoek resultaten eenvoudig in op Hotel gebaseerde entiteiten, waarbij de context van afzonderlijke ruimten en hun kenmerken behouden blijft.

## <a name="next-steps"></a>Volgende stappen

Met uw eigen gegevensset kunt u de [wizard gegevens importeren](search-import-data-portal.md) gebruiken om de index te maken en te laden. De wizard detecteert de Inge sloten JSON-verzameling, zoals de groep die is opgenomen in de *ruimten* en leidt tot een index schema dat een verzameling complexe typen bevat. 

  ![Index afgeleid door wizard gegevens importeren](media/index-sql-relational-data/search-index-rooms-complex-collection.png "Index afgeleid door wizard gegevens importeren")

Probeer de volgende Snelstartgids voor meer informatie over de basis stappen van de wizard gegevens importeren.

> [!div class="nextstepaction"]
> [Snelstartgids: een zoek index maken met behulp van Azure Portal](search-get-started-portal.md)