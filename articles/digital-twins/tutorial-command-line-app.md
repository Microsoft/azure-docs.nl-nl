---
title: 'Zelf studie: een grafiek maken in azure Digital Apparaatdubbels (client-app)'
titleSuffix: Azure Digital Twins
description: Zelf studie voor het bouwen van een Azure Digital Apparaatdubbels-scenario met behulp van een voorbeeld toepassing voor een opdracht regel
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: c18366fd4bc510f32ac0ef255b27709797a3b626
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103493698"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-a-sample-client-app"></a>Zelf studie: een Azure Digital Apparaatdubbels-grafiek maken met behulp van een voor beeld-client-app

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

In deze zelf studie maakt u een grafiek in azure Digital Apparaatdubbels met behulp van modellen, apparaatdubbels en relaties. Het hulp programma voor deze zelf studie is een voor beeld van een **opdracht regel-client toepassing** voor interactie met een Azure Digital apparaatdubbels-exemplaar. De client-app is vergelijkbaar met degene die is geschreven in [*Zelfstudie: Een client-app coderen*](tutorial-code.md).

U kunt dit voorbeeld gebruiken om essentiële Azure Digital Twins-acties uit te voeren, zoals het uploaden van modellen, het maken en wijzigen van tweelingen en het maken van relaties. U kunt ook de [code van het voor beeld](https://github.com/Azure-Samples/digital-twins-samples/tree/master/) bekijken voor meer informatie over de Azure Digital Apparaatdubbels-api's en oefenen met het implementeren van uw eigen opdrachten door het voorbeeld project te wijzigen.

In deze zelfstudie gaat u...
> [!div class="checklist"]
> * Een omgeving model leren
> * Digitale tweelingen maken
> * Relaties toevoegen om een grafiek te maken
> * Query's uitvoeren op de grafiek om vragen te beantwoorden

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

### <a name="run-the-sample-project"></a>Het voorbeeld project uitvoeren

Nu de app en verificatie zijn ingesteld, voert u het project uit met deze knop op de werk balk:

:::image type="content" source="media/tutorial-command-line/app/start-button-sample.png" alt-text="Scherm afbeelding van de knop Start van Visual Studio (SampleClientApp-project)." lightbox="media/tutorial-command-line/app/start-button-sample.png":::

Er wordt een consolevenster geopend, de verificatie wordt uitgevoerd en er wordt gewacht op een opdracht. 
* Verificatie wordt afgehandeld via de browser: uw standaardwebbrowser wordt geopend met een verificatieprompt. Gebruik deze prompt om u aan te melden met uw Azure-referenties. Vervolgens kunt u het browsertabblad of -venster sluiten.

Dit is een schermopname van hoe de projectconsole eruitziet:

:::image type="content" source="media/tutorial-command-line/app/command-line-app.png" alt-text="Scherm opname van het welkomst bericht van de opdracht regel-app." lightbox="media/tutorial-command-line/app/command-line-app.png":::

> [!TIP]
> Voer in de projectconsole `help` in en druk op Enter voor een lijst met alle mogelijke opdrachten die u bij dit project kunt gebruiken.

Laat de projectconsole aan staan voor de rest van de stappen in deze zelfstudie.

## <a name="model-a-physical-environment-with-dtdl"></a>Een fysieke omgeving modelleren met DTDL

Nu het Azure Digital Apparaatdubbels-exemplaar en de voor beeld-app zijn ingesteld, kunt u beginnen met het bouwen van een grafiek van een scenario. 

De eerste stap bij het maken van een Azure Digital Twins-oplossing is het definiëren van tweeling [**modellen**](concepts-models.md) voor uw omgeving. 

Modellen lijken op klassen in objectgeoriënteerde programmeertalen. Ze bieden door de gebruiker gedefinieerde sjablonen die [digitale tweelingen](concepts-twins-graph.md) later kunnen volgen en instantiëren. Ze zijn geschreven in een JSON-achtige taal, **Digital Twins Definition Language (DTDL)** genaamd, en kunnen de *eigenschappen*, *telemetrie*, *relaties* en *componenten* van een tweeling definiëren.

> [!NOTE]
> Met DTDL kunnen ook *opdrachten* op digitale tweelingen worden gedefinieerd. Opdrachten worden momenteel echter niet ondersteund in de Azure Digital Twins-service.

In uw Visual Studio-venster waarin het _**AdtE2ESample**_ -project is geopend, gebruikt u het deel venster *Solution Explorer* om naar de *map AdtSampleApp\SampleClientApp\Models* te gaan. Deze map bevat voorbeeldmodellen.

Selecteer *Room.json* om het in het bewerkingsvenster te openen, en pas het als volgt aan:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Modellen uploaden naar Azure Digital Twins

Na het ontwerpen van modellen moet u deze uploaden naar uw Azure Digital Twins-instantie. Hierdoor wordt uw Azure Digital Twins-service-instantie geconfigureerd met de woordenlijst van uw eigen aangepaste domein. Nadat u de modellen hebt geüpload, kunt u tweelinginstanties maken die ze gebruiken.

1. Voer in het projectconsolevenster de volgende opdracht uit om uw bijgewerkte *Room*-model te uploaden, evenals een *Floor*-model dat u ook in de volgende sectie gebruikt om verschillende soorten tweelingen te maken.

    ```cmd/sh
    CreateModels Room Floor
    ```
    
    De uitvoer zou moeten aangeven dat de modellen met succes zijn gemaakt.

1. Controleer of de modellen zijn gemaakt door de opdracht `GetModels true` uit te voeren. Hierdoor wordt een query uitgevoerd op de Azure Digital Twins-instanties naar alle modellen die zijn geüpload, en worden alle gegevens ervan afgedrukt. Bekijk het bewerkte *Room*-model in de resultaten:

    :::image type="content" source="media/tutorial-command-line/app/output-get-models.png" alt-text="Scherm opname van het resultaat van GetModels, waarin het bijgewerkte room-model wordt weer gegeven." lightbox="media/tutorial-command-line/app/output-get-models.png":::

### <a name="errors"></a>Fouten

De voorbeeldtoepassing handelt ook fouten van de service af. 

Voer de opdracht `CreateModels` nogmaals uit om te proberen een van de modellen die u zojuist het geüpload een tweede keer te uploaden:

```cmd/sh
CreateModels Room
```

Aangezien modellen niet kunnen worden overschreven, retourneert dit nu een servicefout.
Zie [*How to: Manage DTDL models*](how-to-manage-model.md)(Engelstalig) voor meer informatie over het verwijderen van bestaande modellen.
```cmd/sh
Response 409: Service request failed.
Status: 409 (Conflict)

Content:
{"error":{"code":"ModelAlreadyExists","message":"Could not add model dtmi:example:Room;2 as it already exists. Use Model_List API to view models that already exist. See the Swagger example.(http://aka.ms/ModelListSwSmpl)"}}

Headers:
Strict-Transport-Security: REDACTED
Date: Wed, 20 May 2020 00:53:49 GMT
Content-Length: 223
Content-Type: application/json; charset=utf-8
```

## <a name="create-digital-twins"></a>Digitale tweelingen maken

Nu er een paar modellen zijn geüpload naar uw instantie van Azure Digital Twins, kunt u [**digitale tweelingen**](concepts-twins-graph.md) maken op basis van de modeldefinities. Digitale tweelingen vertegenwoordigen de entiteiten in uw bedrijfsomgeving: dingen als sensoren op een boerderij, ruimten in een gebouw, of lichten in een auto. 

U gebruikt de opdracht `CreateDigitalTwin` om een digitale tweeling te maken. U moet verwijzen naar het model waarop de tweeling is gebaseerd, en kunt desgewenst aanvangswaarden definiëren voor alle eigenschappen in het model. U hoeft in dit stadium geen relatiegegevens door de geven.

1. Voer deze code uit in de actieve projectconsole om een aantal tweelingen te maken op basis van het *Room*-model dat u eerder hebt bijgewerkt en een ander model, *Floor*. Zoals u weet heeft *Room* drie eigenschappen, zodat u argumenten kunt opgeven met de aanvangswaarden daarvan. (Initialisatie van eigenschaps waarden is optioneel in het algemeen, maar deze zijn nodig voor deze zelf studie.)

    ```cmd/sh
    CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
    CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
    CreateDigitalTwin dtmi:example:Floor;1 floor0
    CreateDigitalTwin dtmi:example:Floor;1 floor1
    ```

    De uitvoer van deze opdrachten zou moeten aangeven dat de tweelingen met succes zijn gemaakt. 
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-digital-twin.png" alt-text="Scherm opname van een uittreksel van het resultaat van de CreateDigitalTwin-opdrachten, waaronder floor0, floor1, room0 en room1." lightbox="media/tutorial-command-line/app/output-create-digital-twin.png":::

1. U kunt controleren of de apparaatdubbels zijn gemaakt door de opdracht uit te voeren `Query` . Deze opdracht voert een query uit op uw Azure Digital Twins-instantie naar alle digitale tweelingen die deze bevat. Zoek in de resultaten naar de *room0*-, *room1*-, *floor0*-en *floor1* -apparaatdubbels.

### <a name="modify-a-digital-twin"></a>Een digital twin wijzigen

U kunt de eigenschappen van een digitale dubbel die u hebt gemaakt, ook wijzigen. 

> [!NOTE]
> De onderliggende REST API maakt gebruik van de [JSON-patch](http://jsonpatch.com/) indeling om updates te definiëren voor een dubbele. De opdracht regel-app gebruikt deze indeling ook om een echte ervaring te geven met de verwachte Api's.

1. Voer deze opdracht uit om de *room0*-kamer van *room0* te wijzigen in *PresidentialSuite*:
    
    ```cmd/sh
    UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
    ```
    
    De uitvoer zou moeten aangeven dat de tweeling met succes is bijgewerkt.

1. U kunt controleren of de update is voltooid door deze opdracht uit te voeren om de informatie van *room0* te bekijken:

    ```cmd/sh
    GetDigitalTwin room0
    ```
    
    De uitvoer zou de bijgewerkte naam moeten weerspiegelen.


## <a name="create-a-graph-by-adding-relationships"></a>Maak een grafiek door relaties toe te voegen

Vervolgens kunt u **relaties** maken tussen deze tweelingen, om ze te verbinden in een [**tweelinggrafiek**](concepts-twins-graph.md). Tweelinggrafieken worden gebruikt om een gehele omgeving voor te stellen. 

De typen relaties die u kunt maken op basis van een dubbele naar een andere, worden gedefinieerd in de [modellen](#model-a-physical-environment-with-dtdl) die u eerder hebt geüpload. De [model definitie voor *vloer*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) geeft aan dat vloeren een type relatie met de naam *contains* kunnen hebben. Dit maakt het mogelijk om een *contains*-type-relatie te maken van elke *vloer* , naar de bijbehorende ruimte die deze bevat.

Gebruik de opdracht `CreateRelationship` om een relatie toe te voegen. Geef het dubbele op waarmee de relatie afkomstig is, het type relatie en de dubbele verbinding waarmee de relatie tot stand wordt gebracht. Ten slotte geeft u de relatie een unieke ID.

1. Voer de volgende code uit om een 'contains'-relatie toe te voegen van elk van de *Floor*-tweelingen die u eerder hebt gemaakt naar een overeenkomende *Room*-tweeling. De relaties hebben de naam *relationship0* en *relationship1*.

    ```cmd/sh
    CreateRelationship floor0 contains room0 relationship0
    CreateRelationship floor1 contains room1 relationship1
    ```

    >[!TIP]
    >De *contains* -relatie in het [ *basis* model](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) is ook gedefinieerd met twee teken reeks eigenschappen `ownershipUser` en `ownershipDepartment` , zodat u ook argumenten kunt opgeven met de aanvankelijke waarden voor deze wanneer u de relaties maakt.
    > Hier volgt een alternatieve versie van de bovenstaande opdracht om *relationship0* te maken die ook initiële waarden voor deze eigenschappen bevat:
    > ```cmd/sh
    > CreateRelationship floor0 contains room0 relationship0 ownershipUser string MyUser ownershipDepartment string myDepartment
    > ``` 
    
    De uitvoer van deze opdrachten bevestigt dat de relaties met succes zijn gemaakt:
    
    :::image type="content" source="media/tutorial-command-line/app/output-create-relationship.png" alt-text="Scherm opname van een fragment van het resultaat van de CreateRelationship-opdrachten, waaronder relationship0 en relationship1." lightbox="media/tutorial-command-line/app/output-create-relationship.png":::

1. U kunt de relaties controleren met een van de volgende opdrachten, die query's uitvoeren op de relaties in uw Azure Digital Apparaatdubbels-exemplaar.
    * Om alle relaties weer te geven die op elke verdieping worden weer gegeven (de relaties vanaf één zijde bekijken):
        ```cmd/sh
        GetRelationships floor0
        GetRelationships floor1
        ```
    * Om alle relaties weer te geven die op elke ruimte arriveren (de relatie weer geven vanaf de ' andere ' zijde):
        ```cmd/sh
        GetIncomingRelationships room0
        GetIncomingRelationships room1
        ```
    * Als u deze relaties afzonderlijk zoekt, op ID:
        ```cmd/sh
        GetRelationship floor0 relationship0
        GetRelationship floor1 relationship1
        ```

De tweelingen en relaties die u in deze zelfstudie hebt ingesteld vormen de volgende conceptuele grafiek:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Een diagram waarin een conceptuele grafiek wordt weer gegeven. floor0 is verbonden via relationship0 met room0 en floor1 is verbonden via relationship1 met room1." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Query uitvoeren op de tweelinggrafiek om vragen over de omgeving te beantwoorden

Een hoofdfunctie van Azure Digital Twins is de mogelijkheid om gemakkelijk en efficiënt [query's](concepts-query-language.md) uit te voeren op uw tweelinggrafiek om vragen over uw omgeving te beantwoorden. 

Voer de volgende opdrachten uit in de actieve project console om enkele vragen over de voorbeeld omgeving te beantwoorden.

1. **Welke entiteiten van mijn omgeving worden weer gegeven in azure Digital Apparaatdubbels?** (alles opvragen)

    ```cmd/sh
    Query
    ```

    Hiermee kunt u in één oogopslag de balans opmaken van uw omgeving en ervoor zorgen dat alles binnen Azure Digital Twins wordt weergegeven zoals u het wilt. Het resultaat hiervan is een uitvoer die elke digitale tweeling met de gegevens ervan bevat. Hier volgt een fragment:

    :::image type="content" source="media/tutorial-command-line/app/output-query-all.png" alt-text="Scherm opname met een gedeeltelijk resultaat van de dubbele query, waaronder room0 en floor1.":::

    >[!NOTE]
    >In het voorbeeldproject is de opdracht `Query` zonder aanvullende argumenten het equivalent van `Query SELECT * FROM DIGITALTWINS`. Gebruik de langere (volledige) query om alle apparaatdubbels in uw exemplaar op te vragen met behulp van de [query-API's](/rest/api/digital-twins/dataplane/query) of de [CLI-opdrachten](how-to-use-cli.md).

1. **Wat zijn alle ruimten in mijn omgeving?** (query op model)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    U kunt uw query beperken tot tweelingen van een bepaald type om meer specifieke informatie te verkrijgen over wat er wordt weergegeven. Het resultaat hiervan laat *room0* en *room1* zien, maar **niet** *floor0* of *floor1* (omdat dat verdiepingen zijn en geen ruimten).
    
    :::image type="content" source="media/tutorial-command-line/app/output-query-model.png" alt-text="Scherm opname van het resultaat van de model query, met alleen room0 en room1.":::

1. **Wat zijn alle ruimten op *floor0*?** (query op relatie)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    U kunt query's uitvoeren op basis van relaties in uw grafiek, om informatie te krijgen over de manier waarop tweelingen zijn verbonden of om uw query te beperken tot een bepaald gebied. Alleen *room0* bevindt zich op *floor0*, dus is dit de enige ruimte in het resultaat.

    :::image type="content" source="media/tutorial-command-line/app/output-query-relationship.png" alt-text="Scherm opname van het resultaat van de relatie query, met room0.":::

1. **Wat zijn de tweelingen in mijn omgeving met een temperatuur van meer dan 75 °F?** (query op eigenschap)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    U kunt een query uitvoeren op de grafiek op basis van eigenschappen om allerlei vragen te beantwoorden, met inbegrip van uitschieters in uw omgeving die mogelijk aandacht vereisen. Andere vergelijkingsoperators ( *<* , *>* , *=* of *!=* ) worden ook ondersteund. *room1* wordt hier weergegeven in de resultaten, omdat deze een temperatuur van 80 °F heeft.

    :::image type="content" source="media/tutorial-command-line/app/output-query-property.png" alt-text="Scherm opname van het resultaat van de eigenschaps query, waarbij alleen room1 wordt weer gegeven.":::

1. **Wat zijn alle ruimten op *floor0* met een temperatuur van meer dan 75 °F?** (samengestelde query)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    U kunt de eerdere query's ook combineren zoals u dat in SQL zou doen, met behulp van combinatie-operatoren zoals `AND`, `OR`, `NOT`. Deze query maakt gebruik van `AND` om de vorige query over tweelingtemperaturen specifieker te maken. Het resultaat bevat nu alleen ruimten met een temperatuur van meer dan 75 °F op *floor0*; in dit geval geen. De resultatenset is leeg.

    :::image type="content" source="media/tutorial-command-line/app/output-query-compound.png" alt-text="Scherm opname van het resultaat van de samengestelde query, waarbij geen resultaten worden weer gegeven." lightbox="media/tutorial-command-line/app/output-query-compound.png":::

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u deze zelf studie hebt voltooid, kunt u kiezen welke resources u wilt verwijderen, afhankelijk van wat u nu wilt doen.

* **Als u van plan bent om door te gaan met de volgende zelf studie**, kunt u de resources die u hier instelt, blijven gebruiken voor het gebruik van dit Azure Digital apparaatdubbels-exemplaar en de voor beeld-app configureren voor de volgende zelf studie

* **Als u het Azure Digital apparaatdubbels-exemplaar wilt blijven gebruiken, maar alle modellen, apparaatdubbels en relaties wilt verwijderen**, kunt u de voor beeld-app `DeleteAllTwins` en `DeleteAllModels` -opdrachten gebruiken om respectievelijk de apparaatdubbels en modellen in uw exemplaar te wissen.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Misschien wilt u ook de projectmap van uw lokale computer verwijderen.

## <a name="next-steps"></a>Volgende stappen 

In deze zelf studie hebt u met Azure Digital Apparaatdubbels begonnen door een grafiek in uw exemplaar te bouwen met behulp van een voor beeld-client toepassing. U hebt modellen, digitale apparaatdubbels en relaties gemaakt om een grafiek te maken. U hebt ook enkele query's uitgevoerd in de grafiek om een idee te krijgen van de vragen die Azure Digital Apparaatdubbels kan beantwoorden over een omgeving.

Ga door naar de volgende zelf studie om Azure Digital Apparaatdubbels te combi neren met andere Azure-Services om een gegevensgestuurde, end-to-end-scenario te volt ooien:
> [!div class="nextstepaction"]
> [*Zelfstudie: Een end-to-end-oplossing verbinden*](tutorial-end-to-end.md)