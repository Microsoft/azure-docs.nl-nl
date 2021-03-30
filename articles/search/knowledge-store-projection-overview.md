---
title: Projectie concepten
titleSuffix: Azure Cognitive Search
description: Sla uw verrijkte gegevens op in een Data Base van de AI-verrijkings pijp lijn naar een kennis Archief voor gebruik in andere scenario's dan zoeken in volledige tekst.
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 22db4f95bacd926208ac7edf3306cd136d81b00e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85565194"
---
# <a name="knowledge-store-projections-in-azure-cognitive-search"></a>Micro soft Knowledge Store-projecties in azure Cognitive Search

Azure Cognitive Search maakt inhouds verrijking mogelijk via ingebouwde cognitieve vaardig heden en aangepaste vaardig heden als onderdeel van het indexeren. Verrijkingen maken nieuwe informatie, waar ze nog niet aanwezig waren: het extra heren van informatie uit installatie kopieën, het detecteren van sentiment, sleutel zinnen en entiteiten uit tekst, om een paar te noemen. Verrijkingen voegen ook structuur toe aan niet-gedifferentieerde tekst. Al deze processen resulteren in documenten die het zoeken naar volledige tekst effectiever maken. In veel gevallen zijn verrijkte documenten handig voor andere scenario's dan zoeken, zoals voor kennis analyse.

Projecties, een onderdeel van het [kennis archief](knowledge-store-concept-intro.md), zijn weer gaven van verrijkte documenten die kunnen worden opgeslagen in fysieke opslag voor kennis analyse. Met een projectie kunt u uw gegevens projecteren in een vorm die wordt afgestemd op uw behoeften, waardoor de relaties behouden blijven, zodat de hulpprogram ma's zoals Power BI de gegevens kunnen lezen zonder extra inspanning.

Projecties kunnen in tabel vorm worden opgeslagen, met gegevens die in rijen en kolommen in azure Table-opslag zijn bewaard, of JSON-objecten die zijn opgeslagen in Azure Blob Storage. U kunt meerdere projecties van uw gegevens definiëren, terwijl deze worden uitgebreid. Meerdere projecties zijn handig wanneer u wilt dat dezelfde gegevens anders worden geshaped voor afzonderlijke use-cases.

Het kennis archief ondersteunt drie soorten projecties:

+ **Tabellen**: voor gegevens die het beste als rijen en kolommen worden weer gegeven, kunt u met tabel prognoses een geschematiseerde vorm of projectie definiëren in tabel opslag. Alleen geldige JSON-objecten kunnen worden geprojecteerd als tabellen, het verrijkte document kan knoop punten bevatten die geen JSON-objecten zijn en wanneer deze objecten worden geprojecteerd, maakt u een geldig JSON-object met een shaper-vaardigheid of inline-vorm.

+ **Objecten**: wanneer u een JSON-weer gave van uw gegevens en verrijkingen nodig hebt, worden object projecties opgeslagen als blobs. Alleen geldige JSON-objecten kunnen worden geprojecteerd als objecten, het verrijkte document kan knoop punten bevatten die geen JSON-objecten zijn en wanneer deze objecten worden geprojecteerd, maakt u een geldig JSON-object met een shaper-vaardigheid of inline-vorm.

+ **Bestanden**: wanneer u de afbeeldingen die zijn geëxtraheerd uit de documenten wilt opslaan, kunt u met bestands projectes de genormaliseerde installatie kopieën opslaan in Blob Storage.

Als u de prognoses wilt zien die in de context zijn gedefinieerd, kunt u stapsgewijs [een kennis archief maken in rest](knowledge-store-create-rest.md).

## <a name="projection-groups"></a>Projectie groepen

In sommige gevallen moet u uw verrijkte gegevens in verschillende vormen projecteren om te voldoen aan verschillende doel stellingen. In het kennis archief kunt u meerdere groepen projecties definiëren. Projectie groepen hebben de volgende belang rijke kenmerken van wederzijds exclusiviteit en gerelateerde relatie.

### <a name="mutual-exclusivity"></a>Wederzijdse exclusiviteit

Alle inhoud die in één groep is geprojecteerd, is onafhankelijk van de gegevens die in andere projectie groepen zijn geprojecteerd.
Deze onafhankelijkheid impliceert dat u dezelfde gegevens anders hebt geshaped en nog steeds in elke projectie groep hebt herhaald.

### <a name="relatedness"></a>Gerelateerd aan

Met projectie groepen kunt u uw documenten nu projecteren in verschillende projectie typen, terwijl u de relaties tussen projectie typen behoudt. Alle inhoud die is geprojecteerd binnen één projectie groep, behoudt de relaties binnen de gegevens in de projectie typen. In tabellen zijn relaties gebaseerd op een gegenereerde sleutel en elk onderliggend knoop punt behoudt een verwijzing naar het bovenliggende knoop punt. In typen (tabellen, objecten en bestanden) worden relaties behouden wanneer één knoop punt wordt geprojecteerd over verschillende typen. Denk bijvoorbeeld aan een scenario waarin u een document hebt met afbeeldingen en tekst. U kunt de tekst projecteren naar tabellen of objecten en de afbeeldingen naar bestanden waar de tabellen of objecten een kolom/eigenschap met de bestands-URL hebben.

## <a name="input-shaping"></a>Invoer vorming

Het ophalen van uw gegevens in de juiste vorm of structuur is essentieel voor een effectief gebruik, zijn IT-tabellen of-objecten. De mogelijkheid om uw gegevens te vorm geven of te structureren op basis van de manier waarop u toegang wilt krijgen en deze te gebruiken, is een belang rijke functie die wordt weer gegeven als de **shaper** -vaardigheid binnen de vaardig heden.  

Projecties zijn gemakkelijker te definiëren wanneer u een object in de verrijkings structuur hebt dat overeenkomt met het schema van de projectie. Met de bijgewerkte [shaper-vaardigheid](cognitive-search-skill-shaper.md) kunt u een object uit verschillende knoop punten van de verrijkings structuur samen stellen en dit onder een nieuw knoop punt bevinden. Met de **shaper** -vaardigheid kunt u complexe typen met geneste objecten definiëren.

Wanneer u een nieuwe vorm hebt gedefinieerd die alle elementen bevat die u wilt uitprojecteren, kunt u deze vorm nu gebruiken als de bron voor de projecties of als invoer voor een andere vaardigheid.

## <a name="projection-slicing"></a>Projectie segmentering

Bij het definiëren van een projectie groep kan een enkel knoop punt in de verrijkings structuur worden gesegmenteerd in meerdere gerelateerde tabellen of objecten. Het toevoegen van een projectie met een bronpad dat een onderliggend element is van een bestaande projectie resulteert in het onderliggende knoop punt dat wordt gesegmenteerd uit het bovenliggende knoop punt en wordt geprojecteerd in de nieuwe, nog gerelateerde tabel of object. Met deze methode kunt u één knoop punt in een shaper-vaardigheid definiëren dat de bron voor al uw projecties kan zijn.

## <a name="table-projections"></a>Tabel prognoses

Omdat het importeren eenvoudiger wordt, raden we aan om tabel prognoses te maken voor het verkennen van gegevens met Power BI. Daarnaast kunnen tabel projecties de kardinaliteit tussen tabel relaties wijzigen. 

U kunt één document in uw index in meerdere tabellen projecteren, waarbij de relaties behouden blijven. Bij het projecteren in meerdere tabellen wordt de volledige vorm geprojecteerd in elke tabel, tenzij een onderliggend knoop punt de bron is van een andere tabel binnen dezelfde groep.

### <a name="defining-a-table-projection"></a>Tabel projectie definiëren

Wanneer u een tabel projectie in het `knowledgeStore` element van uw vaardig heden definieert, begint u met het toewijzen van een knoop punt op de verrijkings structuur met de tabel bron. Dit knoop punt is doorgaans de uitvoer van een **shaper** -vaardigheid die u hebt toegevoegd aan de lijst met vaardig heden voor het produceren van een specifieke vorm die u moet projecteren in tabellen. Het knoop punt dat u hebt gekozen voor project, kan worden gesegmenteerd tot project in meerdere tabellen. De definitie van tabellen is een lijst met tabellen die u wilt projecteren.

Voor elke tabel zijn drie eigenschappen vereist:

+ TableName: de naam van de tabel in Azure Storage.

+ generatedKeyName: de naam van de kolom voor de sleutel waarmee deze rij uniek wordt geïdentificeerd.

+ Bron: het knoop punt uit de verrijkings structuur waar u uw verrijkingen van bevindt. Dit knoop punt is doorgaans de uitvoer van een shaper, maar het kan ook de uitvoer van een van de vaardig heden zijn.

Hier volgt een voor beeld van tabel prognoses.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [
            { "tableName": "MainTable", "generatedKeyName": "SomeId", "source": "/document/EnrichedShape" },
            { "tableName": "KeyPhrases", "generatedKeyName": "KeyPhraseId", "source": "/document/EnrichedShape/*/KeyPhrases/*" },
            { "tableName": "Entities", "generatedKeyName": "EntityId", "source": "/document/EnrichedShape/*/Entities/*" }
          ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Zoals gedemonstreerd in dit voor beeld, worden de sleutel zinnen en entiteiten gemodelleerd in verschillende tabellen en bevatten ze een verwijzing naar het bovenliggende item (MainTable) voor elke rij.

## <a name="object-projections"></a>Object projecties

Object projecties zijn JSON-representaties van de verrijkings structuur die vanuit elk knoop punt kan worden gebrond. In veel gevallen kan dezelfde **shaper** -vaardigheid die een tabel projectie maakt, worden gebruikt voor het genereren van een object projectie. 

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [
            {
              "storageContainer": "hotelreviews", 
              "source": "/document/hotel"
            }
          ]
        },
        {
            "files": [ ]
        }
      ]
    }
}
```

Voor het genereren van een object projectie zijn enkele object-specifieke kenmerken vereist:

+ storageContainer: de BLOB-container waar de objecten worden opgeslagen
+ Bron: het pad naar het knoop punt van de verrijkings structuur die de basis vormt van de projectie

## <a name="file-projection"></a>Bestands projectie

Bestands prognoses zijn vergelijkbaar met object projecties en werken alleen op de `normalized_images` verzameling. Net als bij object projecties worden bestands projecties opgeslagen in de BLOB-container met het map-voor voegsel van de base64-gecodeerde waarde van de document-ID. Bestands projecties kunnen niet dezelfde container delen als object projecties en moeten worden geprojecteerd in een andere container.

```json
{
    "name": "your-skillset",
    "skills": [
      …your skills
    ],
"cognitiveServices": {
… your cognitive services key info
    },

    "knowledgeStore": {
      "storageConnectionString": "an Azure storage connection string",
      "projections" : [
        {
          "tables": [ ]
        },
        {
          "objects": [ ]
        },
        {
            "files": [
                 {
                  "storageContainer": "ReviewImages",
                  "source": "/document/normalized_images/*"
                }
            ]
        }
      ]
    }
}
```

## <a name="projection-lifecycle"></a>Projectie levenscyclus

De projecties hebben een levens cyclus die is gekoppeld aan de bron gegevens in uw gegevens bron. Naarmate uw gegevens worden bijgewerkt en opnieuw geïndexeerd, worden uw projecties bijgewerkt met de resultaten van de verrijkingen, zodat uw projecties uiteindelijk consistent zijn met de gegevens in uw gegevens bron. De projecties nemen het verwijderings beleid over dat u voor de index hebt geconfigureerd. Projecties worden niet verwijderd wanneer de Indexeer functie of de zoek service zelf wordt verwijderd.

## <a name="using-projections"></a>Projecties gebruiken

Nadat de Indexeer functie is uitgevoerd, kunt u de geprojecteerde gegevens in de containers of tabellen die u hebt opgegeven door projecties lezen.

Voor analyses is het verkennen van Power BI zo eenvoudig als het instellen van Azure Table-opslag als de gegevens bron. U kunt eenvoudig een set visualisaties op uw gegevens maken met behulp van de relaties in.

Als u echter de verrijkte gegevens in een Data Science-pijp lijn moet gebruiken, kunt u [de gegevens van blobs laden in een Panda data frame](../machine-learning/team-data-science-process/explore-data-blob.md).

Ten slotte, als u uw gegevens uit het kennis archief moet exporteren, Azure Data Factory over connectors beschikt voor het exporteren van de gegevens en deze in de door u gewenste data base. 

## <a name="next-steps"></a>Volgende stappen

Als volgende stap maakt u uw eerste kennis archief met behulp van voorbeeld gegevens en instructies.

> [!div class="nextstepaction"]
> [Maak een Knowledge Store in rest](knowledge-store-create-rest.md).

Begin met het [definiëren van projecties in een kennis archief](knowledge-store-projections-examples.md) voor een zelf studie over geavanceerde concepten zoals het delen, inline vorm geven en relaties.

> [!div class="nextstepaction"]
> [Projecties definiëren in een kennis archief](knowledge-store-projections-examples.md)
