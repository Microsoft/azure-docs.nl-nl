---
title: Concepten van kennis winkels
titleSuffix: Azure Cognitive Search
description: Verrijkte documenten verzenden naar Azure Storage waar u verrijkte documenten kunt weer geven, wijzigen en gebruiken in azure Cognitive Search en in andere toepassingen.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 3ec556c6198a00f217568f6591bd4b43c7fc743e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88924296"
---
# <a name="knowledge-store-in-azure-cognitive-search"></a>Knowledge Store in azure Cognitive Search

Knowledge Store is een functie van Azure Cognitive Search die de uitvoer van een [AI-verrijkings pijplijn](cognitive-search-concept-intro.md) persistent maakt voor onafhankelijke analyses of downstream-verwerking. Een *verrijkt document* is de uitvoer van een pijp lijn, gemaakt op basis van inhoud die is geëxtraheerd, gestructureerd en geanalyseerd met behulp van AI-processen. In een Standard AI-pijp lijn zijn verrijkte documenten onwaar, die alleen tijdens het indexeren worden gebruikt en vervolgens worden verwijderd. Als u kiest voor het maken van een kennis archief, kunt u de verrijkte documenten bewaren. 

Als u in het verleden cognitieve vaardig heden hebt gebruikt, weet u al dat *vaardig heden* een document verplaatst via een reeks verrijkingen. Het resultaat kan een zoek index of projecties in een kennis archief zijn. De twee uitvoer, zoek index en kennis opslag zijn producten van dezelfde pijp lijn; afgeleid van dezelfde invoer, maar resulteert in uitvoer die is gestructureerd, opgeslagen en op zeer verschillende manieren gebruikt.

Fysiek is een kennis archief [Azure Storage](../storage/common/storage-account-overview.md), ofwel Azure Table Storage, Azure Blob Storage, of beide. Elk hulp programma of proces waarmee verbinding kan worden gemaakt met Azure Storage, kan de inhoud van een kennis archief gebruiken.


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=235&end=426]


![Kennis archief in pijplijn diagram](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "Kennis archief in pijplijn diagram")

## <a name="benefits-of-knowledge-store"></a>Voor delen van kennis archief

Een kennis archief biedt u de structuur, context en werkelijke inhoud-afgelezen van ongestructureerde en semi-gestructureerde gegevens bestanden zoals blobs, afbeeldings bestanden die analyses hebben ondergaan of zelfs gestructureerde gegevens, met een nieuwe vorm. In een [Stapsgewijze walkthrough](knowledge-store-create-rest.md)kunt u de eerste hand leiding zien hoe een gelijkmatig JSON-document is gepartitioneerd in substructuren, opnieuw te maken in nieuwe structuur en op andere wijze beschikbaar wordt gesteld voor downstream-processen, zoals machine learning en werk belastingen voor data technologie.

Hoewel het handig is om te zien wat een AI-verrijkings pijplijn kan produceren, is het echte potentieel van een kennis archief de mogelijkheid om gegevens te wijzigen. U kunt beginnen met een basis vaardighedenset en vervolgens door lopen om meer structuur niveaus toe te voegen. deze kunnen vervolgens worden gecombineerd tot nieuwe structuren in andere apps dan Azure Cognitive Search.

Geïnventariseerd, de voor delen van kennis archief zijn onder andere het volgende:

+ Gebruik verrijkte documenten in [analyse-en rapportage hulpprogramma's,](#tools-and-apps) met uitzonde ring van zoeken. Power BI met Power Query is een fascinerende keuze, maar elk hulp programma of elke app waarmee verbinding kan worden gemaakt met Azure Storage kan worden opgehaald uit een kennis archief dat u maakt.

+ Verfijn een pijp lijn voor AI-indexering bij het opsporen van fouten en definities van vaardig heden. In een kennis archief ziet u het product van een vaardighedenset-definitie in een AI-Indexing-pijp lijn. U kunt deze resultaten gebruiken om een betere vaardig heden te ontwerpen, want u kunt precies zien hoe de verrijkingen eruitzien. U kunt [Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows) in azure Storage gebruiken om de inhoud van een kennis archief weer te geven.

+ De gegevens vorm geven in nieuwe formulieren. Het omvormen wordt in vaardig heden gecodificeerd, maar het punt is dat deze mogelijkheid nu kan worden geboden door een vaardig heden. De [shaper-vaardigheid](cognitive-search-skill-shaper.md) in azure Cognitive Search is uitgebreid om deze taak te kunnen verwerken. Met Reshaping kunt u een projectie definiëren die wordt afgestemd op uw gebruik van de gegevens, terwijl de relaties behouden blijven.

> [!Note]
> Nieuwe voor AI-verrijkings-en cognitieve vaardig heden? Azure Cognitive Search integreert met Cognitive Services Vision-en taal functies om bron gegevens te extra heren en te verrijken met behulp van optische teken herkenning (OCR) over afbeeldings bestanden, het herkennen van entiteiten en het uitpakken van sleutel zinnen uit tekst bestanden. Zie [AI-verrijking in Azure Cognitive Search](cognitive-search-concept-intro.md)voor meer informatie.

## <a name="physical-storage"></a>Fysieke opslag


> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3&start=455&end=542]


De fysieke expressie van een kennis archief wordt gegeleeerd via het `projections` element van een `knowledgeStore` definitie in een vaardig heden. De projectie definieert een structuur van de uitvoer zodat deze overeenkomt met uw beoogde gebruik.

Projecties kunnen worden gegeleeerd als tabellen, objecten of bestanden.

```json
"knowledgeStore": { 
    "storageConnectionString": "<YOUR-AZURE-STORAGE-ACCOUNT-CONNECTION-STRING>", 
    "projections": [ 
        { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        },
                { 
            "tables": [ ], 
            "objects": [ ], 
            "files": [ ]
        }
```

Het type projectie dat u in deze structuur opgeeft, bepaalt het type opslag dat wordt gebruikt door het kennis archief.

+ Tabel opslag wordt gebruikt bij het definiëren van `tables` . Definieer een tabel projectie wanneer u Structured Reporting-structuren nodig hebt voor invoer in analyse hulpprogramma's of als gegevens frames exporteert naar andere gegevens archieven. U kunt meerdere opgeven `tables` om een subset of Kruis sectie van verrijkte documenten op te halen. In dezelfde projectie groep blijven tabel relaties behouden, zodat u ermee kunt werken.

+ Blob-opslag wordt gebruikt wanneer u `objects` of definieert `files` . De fysieke weer gave van een `object` is een hiërarchische JSON-structuur die een verrijkt document vertegenwoordigt. Een `file` is een afbeelding die uit een document is geëxtraheerd en die intact is overgebracht naar Blob Storage.

Eén projectie object bevat één set `tables` ,, `objects` `files` en voor veel scenario's. het maken van een projectie kan voldoende zijn. 

Het is echter mogelijk om meerdere projecties te maken `table` - `object` - `file` , en u kunt dat doen als u verschillende gegevens relaties wilt. In een set worden gegevens gekoppeld, ervan uitgaande dat deze relaties bestaan en kunnen worden gedetecteerd. Als u extra sets maakt, zijn de documenten in elke groep nooit gerelateerd. Een voor beeld van het gebruik van meerdere projectie groepen is mogelijk als u wilt dat dezelfde gegevens worden geprojecteerd voor gebruik met uw online systeem en u een specifieke manier wilt gebruiken. u wilt ook dat dezelfde gegevens worden geprojecteerd voor gebruik in een Data Science-pijp lijn die anders wordt weer gegeven.

## <a name="requirements"></a>Vereisten 

[Azure Storage](../storage/index.yml) is vereist. Het biedt fysieke opslag. U kunt Blob Storage, Table Storage of beide gebruiken. Blob-opslag wordt gebruikt voor onintacte, verrijkte documenten, meestal wanneer de uitvoer naar downstream-processen gaat. Table Storage is voor segmenten van verrijkte documenten, die vaak worden gebruikt voor analyse en rapportage.

De [vaardig heden](cognitive-search-working-with-skillsets.md) zijn vereist. Het bevat de `knowledgeStore` definitie en bepaalt de structuur en samen stelling van een verrijkt document. U kunt geen kennis archief maken met een lege vaardig heden. U moet ten minste één vaardigheid hebben in een vakkennisset.

[Indexeer functie](search-indexer-overview.md) is vereist. Een vaardig heden wordt aangeroepen door een Indexeer functie, die de uitvoering verstuurt. Indexeer functies worden geleverd met hun eigen set vereisten en kenmerken. Verschillende van deze kenmerken hebben een directe opslag in een kennis archief:

+ Voor Indexeer functies is een [ondersteunde Azure-gegevens bron](search-indexer-overview.md#supported-data-sources) vereist (de pijp lijn die uiteindelijk het kennis archief maakt, wordt gestart door gegevens uit een ondersteunde bron op Azure te halen). 

+ Voor Indexeer functies is een zoek index vereist. Een Indexeer functie vereist dat u een index schema opgeeft, zelfs als u deze nooit wilt gebruiken. Een minimale index heeft één teken reeks veld, aangeduid als de sleutel.

+ Indexeer functies bieden optionele veld toewijzingen, waarmee een bron veld wordt gealiasd voor een doel veld. Als een standaard veld toewijzing moet worden gewijzigd (om een andere naam of een ander type te gebruiken), kunt u een [veld toewijzing](search-indexer-field-mappings.md) maken binnen een Indexeer functie. Voor de uitvoer van het kennis archief kan het doel een veld in een blob-object of-tabel zijn.

+ Indexeer functies hebben schema's en andere eigenschappen, zoals het wijzigen van detectie mechanismen van verschillende gegevens bronnen, kunnen ook worden toegepast op een kennis archief. U kunt bijvoorbeeld met regel matige tussen pozen een verrijking [plannen](search-howto-schedule-indexers.md) om de inhoud te vernieuwen. 

## <a name="how-to-create-a-knowledge-store"></a>Een kennis archief maken

Als u een kennis winkel wilt maken, gebruikt u de portal of de REST API ( `api-version=2020-06-30` ).

### <a name="use-the-azure-portal"></a>De Azure-portal gebruiken

De wizard **gegevens importeren** bevat opties voor het maken van een kennis archief. Voor de eerste verkenning [maakt u uw eerste kennis archief in vier stappen](knowledge-store-connect-power-bi.md).

1. Selecteer een ondersteunde gegevens bron.

1. Verrijking opgeven: een resource koppelen, vaardig heden selecteren en een kennis archief opgeven. 

1. Maak een index schema. De wizard vereist deze en kan één voor u afleiden.

1. Voer de wizard uit. In deze laatste stap vindt extra heren, verrijking en opslag plaats.

### <a name="use-create-skillset-rest-api"></a>Vaardig heden maken gebruiken (REST API)]

A `knowledgeStore` wordt gedefinieerd binnen een [vaardig heden](cognitive-search-working-with-skillsets.md), die op zijn beurt wordt aangeroepen door een [Indexeer functie](search-indexer-overview.md). Tijdens verrijking maakt Azure Cognitive Search een ruimte in uw Azure Storage-account en projecteert de verrijkte documenten als blobs of in tabellen, afhankelijk van uw configuratie.

De REST API is één mechanisme waarmee u programmatisch een kennis archief kunt maken. Een eenvoudige manier om te verkennen, is [het maken van uw eerste kennis archief met behulp van Postman en de rest API](knowledge-store-create-rest.md).

<a name="tools-and-apps"></a>

## <a name="how-to-connect-with-tools-and-apps"></a>Verbinding maken met hulpprogram ma's en apps

Zodra de verrijkingen in de opslag ruimte bestaan, kunnen alle hulp middelen of technologieën die verbinding maken met Azure Blob of tabel opslag worden gebruikt om de inhoud te verkennen, te analyseren of te gebruiken. De volgende lijst is een begin:

+ [Storage Explorer](knowledge-store-view-storage-explorer.md) om de verrijkte document structuur en-inhoud weer te geven. Beschouw dit als het basislijn hulp programma voor het weer geven van de inhoud van het kennis archief.

+ [Power bi](knowledge-store-connect-power-bi.md) voor rapportage en analyse. 

+ [Azure Data Factory](../data-factory/index.yml) voor verdere manipulatie.

<a name="kstore-rest-api"></a>

## <a name="api-reference"></a>API-verwijzing

REST API versie `2020-06-30` biedt kennis opslag via aanvullende definities op vaardig heden. Naast de referentie raadpleegt u  [een kennis archief maken met behulp van Postman](knowledge-store-create-rest.md) voor meer informatie over het aanroepen van de api's.

+ [Vaardig heden maken (API-Version = 2020-06-30)](/rest/api/searchservice/create-skillset)
+ [Vaardig heden bijwerken (API-Version = 2020-06-30)](/rest/api/searchservice/update-skillset)


## <a name="next-steps"></a>Volgende stappen

Knowledge Store biedt persistentie van verrijkte documenten, handig bij het ontwerpen van een vaardig heden of het maken van nieuwe structuren en inhoud voor het gebruik door client toepassingen die toegang kunnen krijgen tot een Azure Storage-account.

De eenvoudigste benadering voor het maken van verrijkte documenten is [via de portal](knowledge-store-create-portal.md), maar u kunt ook postman en de rest API gebruiken. Dit is handiger als u inzicht wilt in de manier waarop objecten worden gemaakt en waarnaar wordt verwezen.

> [!div class="nextstepaction"]
> [Een kennis archief maken met behulp van Postman en REST](knowledge-store-create-rest.md)

Voor meer informatie over projecties, de mogelijkheden en hoe u [deze definieert in een vaardig heden](knowledge-store-projection-overview.md)

> [!div class="nextstepaction"]
> [Prognoses in een kennis archief](knowledge-store-projection-overview.md)

Begin met het [definiëren van projecties in een kennis archief](knowledge-store-projections-examples.md) voor een zelf studie over geavanceerde concepten zoals het delen, inline vorm geven en relaties.

> [!div class="nextstepaction"]
> [Projecties definiëren in een kennis archief](knowledge-store-projections-examples.md)