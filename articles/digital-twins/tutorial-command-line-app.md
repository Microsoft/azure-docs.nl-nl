---
title: 'Zelfstudie: De basisbeginselen verkennen met een voorbeeldclient-app'
titleSuffix: Azure Digital Twins
description: Zelfstudie voor het verkennen van de Azure Digital Twins SDK's met behulp van een voorbeeldopdrachtregeltoepassing
author: baanders
ms.author: baanders
ms.date: 5/8/2020
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: a9d8c42432d619ab120afd0bd5734f6fa269b514
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96458107"
---
# <a name="tutorial-explore-azure-digital-twins-with-a-sample-client-app"></a>Zelfstudie: Azure Digital Twins verkennen met een voorbeeldclient-app

In deze zelfstudie wordt een voorbeeldtoepassing geïntroduceerd die een opdrachtregelclienttoepassing implementeert voor interactie met een Azure Digital Twins-instantie. De client-app is vergelijkbaar met degene die is geschreven in [*Zelfstudie: Een client-app coderen*](tutorial-code.md).

U kunt dit voorbeeld gebruiken om essentiële Azure Digital Twins-acties uit te voeren, zoals het uploaden van modellen, het maken en wijzigen van tweelingen en het maken van relaties. U kunt ook de code van het voor beeld bekijken voor meer informatie over de Azure Digital Twins-API's, en oefenen met het implementeren van uw eigen opdrachten door het voorbeeldproject te wijzigen zoals u maar wilt.

In deze zelfstudie gaat u...
> [!div class="checklist"]
> * Een Azure Digital Twins-instantie instellen
> * De voorbeeldopdrachtregel-app configureren om te interageren met de instantie
> * De opdrachtregel-app gebruiken om Azure Digital Twins te verkennen, inclusief **modellen**, **digitale tweelingen**, **relaties** en **query's**

[!INCLUDE [Azure Digital Twins tutorial: sample prerequisites](../../includes/digital-twins-tutorial-sample-prereqs.md)]

[!INCLUDE [Azure Digital Twins tutorial: configure the sample project](../../includes/digital-twins-tutorial-sample-configure.md)]

## <a name="explore-with-the-sample-solution"></a>Verkennen met de voorbeeldoplossing

Nu de instantie en de voorbeeld-app zijn geconfigureerd, gaat u het voorbeeldproject en wat vooraf geschreven voorbeeldcode gebruiken om een eenvoudige Azure Digital Twins-oplossing uit te bouwen en te verkennen. De hoofdcomponenten van de oplossing zijn **modellen**, **digitale tweelingen** en **relaties**, resulterend in een **tweelinggrafiek** van een omgeving waarop query's kunnen worden uitgevoerd.

### <a name="model-a-physical-environment-with-dtdl"></a>Een fysieke omgeving modelleren met DTDL

De eerste stap bij het maken van een Azure Digital Twins-oplossing is het definiëren van tweeling [**modellen**](concepts-models.md) voor uw omgeving. 

Modellen lijken op klassen in objectgeoriënteerde programmeertalen. Ze bieden door de gebruiker gedefinieerde sjablonen die [digitale tweelingen](concepts-twins-graph.md) later kunnen volgen en instantiëren. Ze zijn geschreven in een JSON-achtige taal, **Digital Twins Definition Language (DTDL)** genaamd, en kunnen de *eigenschappen*, *telemetrie*, *relaties* en *componenten* van een tweeling definiëren.

> [!NOTE]
> Met DTDL kunnen ook *opdrachten* op digitale tweelingen worden gedefinieerd. Opdrachten worden momenteel echter niet ondersteund in de Azure Digital Twins-service.

Gebruik in het Visual Studio-venster waar het project _**AdtE2ESample**_ open is, het deelvenster *Solution Explorer* om naar de map *AdtSampleApp\SampleClientApp\Models* te navigeren. Deze map bevat voorbeeldmodellen.

Selecteer *Room.json* om het in het bewerkingsvenster te openen, en pas het als volgt aan:

1. **Werk het versienummer bij** om aan te geven dat u een meer up-to-date versie van dit model maakt. U doet dit door de *1* aan het einde van de `@id`-waarde rte veranderen in een *2*. Elk nummer dat hoger is dan het huidige versienummer werkt ook.
1. **Wijzig een eigenschap**. Wijzig de naam van eigenschap `Humidity` in *HumidityLevel* (of iets anders als u dat wilt. Als u iets anders gebruikt dan *HumidityLevel*, onthoud dan wat u hebt gebruikt en gebruik het overal in de zelfstudie in plaats van *HumidityLevel*).
1. **Voeg een eigenschap toe**. Plak onder de eigenschap `HumidityLevel` die eindigt op regel 15 de volgende code om de eigenschap `RoomName` toe te voegen aan de ruimte:

    ```json
    ,
    {
      "@type": "Property",
      "name": "RoomName",
      "schema": "string"
    }
    ```
1. **Voeg een relatie toe**. Plak onder de eigenschap `RoomName` die u zojuist hebt toegevoegd de volgende code om de mogelijkheid toe te voegen dat deze tweeling *contains*-relaties kan maken met andere tweelingen:

    ```json
    ,
    {
      "@type": "Relationship",
      "name": "contains"
    }
    ```

Wanneer u klaar bent, zou het bijgewerkte model er als volgt uit moeten zien:

:::image type="content" source="media/tutorial-command-line-app/room-model.png" alt-text="Bewerkte Room.json met bijgewerkte eigenschappen HumidityLevel en RoomName en de relatie 'contains'" border="false":::

Vergeet niet het bestand op te slaan voordat u verdergaat.

> [!TIP]
> Als u wilt proberen een eigen model te maken, kunt u de code van het *Room*-model in een nieuw bestand plakken dat u met de extensie *.json* opslaat in de map *AdtSampleApp\SampleClientApp\Models*. Vervolgens kunt u ermee spelen en eigenschappen en relaties toevoegen om alles te vertegenwoordigen wat u wilt. U kunt ook naar de andere voorbeeldmodellen in deze map kijken voor ideeën.

> [!TIP] 
> Er is een taalagnostisch [DTDL-validatorvoorbeeld](/samples/azure-samples/dtdl-validator/dtdl-validator) dat u kunt gebruiken om modeldocumenten te controleren en u ervan te verzekeren dat de DTDL geldig is. Het voorbeeld is gemaakt op basis van de DTDL-parserbibliotheek, waarover u meer kunt lezen in [*Instructies: Modellen parseren en valideren*](how-to-parse-models.md).

### <a name="get-started-with-the-command-line-app"></a>Aan de slag met de opdrachtregel-app

Nu u een model hebt gedefinieerd, bestaan de resterende stappen uit het gebruiken van de voorbeeld-app om te interageren met uw Azure Digital Twins-instantie. Voer het project uit met deze knop op de werkbalk:

:::image type="content" source="media/tutorial-command-line-app/start-button-sample.png" alt-text="De startknop van Visual Studio (project SampleClientApp)":::

Er wordt een consolevenster geopend, de verificatie wordt uitgevoerd en er wordt gewacht op een opdracht. 
* Verificatie wordt afgehandeld via de browser: uw standaardwebbrowser wordt geopend met een verificatieprompt. Gebruik deze prompt om u aan te melden met uw Azure-referenties. Vervolgens kunt u het browsertabblad of -venster sluiten.

Dit is een schermopname van hoe de projectconsole eruitziet:

:::image type="content" source="media/tutorial-command-line-app/command-line-app.png" alt-text="Welkomstbericht van de opdrachtregel-app":::

> [!TIP]
> Voer in de projectconsole `help` in en druk op Enter voor een lijst met alle mogelijke opdrachten die u bij dit project kunt gebruiken.
> :::image type="content" source="media/tutorial-command-line-app/command-line-app-help.png" alt-text="Uitvoer van de help-opdracht":::

Laat de projectconsole aan staan voor de rest van de stappen in deze zelfstudie.

#### <a name="upload-models-to-azure-digital-twins"></a>Modellen uploaden naar Azure Digital Twins

Na het ontwerpen van modellen moet u deze uploaden naar uw Azure Digital Twins-instantie. Hierdoor wordt uw Azure Digital Twins-service-instantie geconfigureerd met de woordenlijst van uw eigen aangepaste domein. Nadat u de modellen hebt geüpload, kunt u tweelinginstanties maken die ze gebruiken.

Voer in het projectconsolevenster de volgende opdracht uit om uw bijgewerkte *Room*-model te uploaden, evenals een *Floor*-model dat u ook in de volgende sectie gebruikt om verschillende soorten tweelingen te maken.

```cmd/sh
CreateModels Room Floor
```

De uitvoer zou moeten aangeven dat de modellen met succes zijn gemaakt.

> [!TIP]
> Als u eerder uw eigen model hebt gemaakt, kunt u dit hier ook uploaden, door de bestandsnaam (u kunt de extensie weglaten) toe te voegen aan de lijst `Room Floor` in de opdracht hierboven.

Controleer of de modellen zijn gemaakt door de opdracht `GetModels true` uit te voeren. Hierdoor wordt een query uitgevoerd op de Azure Digital Twins-instanties naar alle modellen die zijn geüpload, en worden alle gegevens ervan afgedrukt. Bekijk het bewerkte *Room*-model in de resultaten:

:::image type="content" source="media/tutorial-command-line-app/output-get-models.png" alt-text="Resultaten van GetModels waarin het bijgewerkte Room-model wordt weergegeven":::

#### <a name="errors"></a>Fouten

De voorbeeldtoepassing handelt ook fouten van de service af. 

Voer de opdracht `CreateModels` nogmaals uit om te proberen een van de modellen die u zojuist het geüpload een tweede keer te uploaden:

```cmd/sh
CreateModels Room
```

Aangezien modellen niet kunnen worden overschreven, retourneert dit nu een servicefout.
Voor meer informatie over hoe u bestaande modellen kunt verwijderen, zie [ *Aangepaste modellen beheren*](how-to-manage-model.md).
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

### <a name="create-digital-twins"></a>Digitale tweelingen maken

Nu er een paar modellen zijn geüpload naar uw instantie van Azure Digital Twins, kunt u [**digitale tweelingen**](concepts-twins-graph.md) maken op basis van de modeldefinities. Digitale tweelingen vertegenwoordigen de entiteiten in uw bedrijfsomgeving: dingen als sensoren op een boerderij, ruimten in een gebouw, of lichten in een auto. 

U gebruikt de opdracht `CreateDigitalTwin` om een digitale tweeling te maken. U moet verwijzen naar het model waarop de tweeling is gebaseerd, en kunt desgewenst aanvangswaarden definiëren voor alle eigenschappen in het model. U hoeft in dit stadium geen relatiegegevens door de geven.

Voer deze code uit in de actieve projectconsole om een aantal tweelingen te maken op basis van het *Room*-model dat u eerder hebt bijgewerkt en een ander model, *Floor*. Zoals u weet heeft *Room* drie eigenschappen, zodat u argumenten kunt opgeven met de aanvangswaarden daarvan.

```cmd/sh
CreateDigitalTwin dtmi:example:Room;2 room0 RoomName string Room0 Temperature double 70 HumidityLevel double 30
CreateDigitalTwin dtmi:example:Room;2 room1 RoomName string Room1 Temperature double 80 HumidityLevel double 60
CreateDigitalTwin dtmi:example:Floor;1 floor0
CreateDigitalTwin dtmi:example:Floor;1 floor1
```

> [!TIP]
> Als u eerder uw eigen model hebt geüpload, kunt u proberen uw eigen `CreateDigitalTwin`-opdracht te maken op basis van de opdrachten hierboven om een tweeling van uw eigen modeltype toe te voegen.

De uitvoer van deze opdrachten zou moeten aangeven dat de tweelingen met succes zijn gemaakt. 

:::image type="content" source="media/tutorial-command-line-app/output-create-digital-twin.png" alt-text="Fragment van de resultaten van CreateDigitalTwin-opdrachten, met floor0, floor1, room0, en room1":::

U kunt ook de opdracht `Query` uitvoeren om te verifiëren dat de tweelingen zijn gemaakt. Deze opdracht voert een query uit op uw Azure Digital Twins-instantie naar alle digitale tweelingen die deze bevat. Kijk in de resultaten naar de tweelingen *floor0*, *floor1*, *room0* en *room1*.

#### <a name="modify-a-digital-twin"></a>Een digitale dubbel wijzigen

U kunt de eigenschappen van een digitale dubbel die u hebt gemaakt, ook wijzigen. Probeer deze opdracht uit te voeren om de RoomName van *room0* te wijzigen van *room0* in *PresidentialSuite*:

```cmd/sh
UpdateDigitalTwin room0 add /RoomName string PresidentialSuite
```

De uitvoer zou moeten aangeven dat de tweeling met succes is bijgewerkt.

U kunt ook verifiëren door deze opdracht uit te voeren om de informatie van *room0* te zien:

```cmd/sh
GetDigitalTwin room0
```

De uitvoer zou de bijgewerkte naam moeten weerspiegelen.

> [!NOTE]
> De onderliggende REST API gebruikt JSON Patch om updates voor een tweeling te definiëren. De opdrachtregel-app reflecteert deze notatie, zodat u kunt experimenteren met wat de onderliggende API's in werkelijkheid verwachten.

### <a name="create-a-graph-by-adding-relationships"></a>Maak een grafiek door relaties toe te voegen

Vervolgens kunt u **relaties** maken tussen deze tweelingen, om ze te verbinden in een [**tweelinggrafiek**](concepts-twins-graph.md). Tweelinggrafieken worden gebruikt om een gehele omgeving voor te stellen. 

Gebruik de opdracht `CreateRelationship` om een relatie toe te voegen. Geef de tweeling op waarvan de relatie afkomstig is, het type toe te voegen relatie en de tweeling waarmee de relatie verbinding maakt. Geef ten slotte een naam (id) op voor de relatie.

Voer de volgende code uit om een 'contains'-relatie toe te voegen van elk van de *Floor*-tweelingen die u eerder hebt gemaakt naar een overeenkomende *Room*-tweeling. Er moet een *contains*-relatie zijn gedefinieerd in het *Floor*-model om dit mogelijk te maken.

```cmd/sh
CreateRelationship floor0 contains room0 relationship0
CreateRelationship floor1 contains room1 relationship1
```

De uitvoer van deze opdrachten bevestigt dat de relaties met succes zijn gemaakt:

:::image type="content" source="media/tutorial-command-line-app/output-create-relationship.png" alt-text="Fragment van de resultaten van CreateRelationship-opdrachten, met relationship0 en relationship1":::

U kunt de relaties ook controleren met elk van de volgende opdrachten, waarmee de relaties in uw instantie van Azure Digital Twins wordt opgevraagd.
* Om alle relaties te zien die afkomstig zijn van elke verdieping (bekijk de relaties van één kant),
    ```cmd/sh
    GetRelationships floor0
    GetRelationships floor1
    ```
* Om alle relaties te zien die aankomen in elke ruimte (bekijk de relaties van de 'andere' kant),
    ```cmd/sh
    GetIncomingRelationships room0
    ```
* Om deze relaties afzonderlijk op te vragen, 
    ```cmd/sh
    GetRelationship floor0 relationship0
    GetRelationship floor1 relationship1
    ```

De tweelingen en relaties die u in deze zelfstudie hebt ingesteld vormen de volgende conceptuele grafiek:

:::image type="content" source="media/tutorial-command-line-app/sample-graph.png" alt-text="Een grafiek die laat zien dat floor0 via relationship0 is verbonden met room0, en floor1 via relationship1 is verbonden met room1" border="false":::

### <a name="query-the-twin-graph-to-answer-environment-questions"></a>Query uitvoeren op de tweelinggrafiek om vragen over de omgeving te beantwoorden

Een hoofdfunctie van Azure Digital Twins is de mogelijkheid om gemakkelijk en efficiënt [query's](concepts-query-language.md) uit te voeren op uw tweelinggrafiek om vragen over uw omgeving te beantwoorden. Voer de volgende opdrachten uit in de actieve projectconsole om een idee te krijgen van hoe dat werkt.

* **Wat zijn alle entiteiten in mijn omgeving die worden vertegenwoordigd in Azure Digital Twins?** (alles opvragen)

    ```cmd/sh
    Query
    ```

    Hiermee kunt u in één oogopslag de balans opmaken van uw omgeving en ervoor zorgen dat alles binnen Azure Digital Twins wordt weergegeven zoals u het wilt. Het resultaat hiervan is een uitvoer die elke digitale tweeling met de gegevens ervan bevat. Hier volgt een fragment:

    :::image type="content" source="media/tutorial-command-line-app/output-query-all.png" alt-text="Gedeeltelijke resultaten van dubbelquery, met room0 en floor1":::

    >[!NOTE]
    >In het voorbeeldproject is de opdracht `Query` zonder aanvullende argumenten het equivalent van `Query SELECT * FROM DIGITALTWINS`. Gebruik de langere (volledige) query om alle apparaatdubbels in uw exemplaar op te vragen met behulp van de [query-API's](/rest/api/digital-twins/dataplane/query) of de [CLI-opdrachten](how-to-use-cli.md).

* **Wat zijn alle ruimten in mijn omgeving?** (query op model)

    ```cmd/sh
    Query SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')
    ```

    U kunt uw query beperken tot tweelingen van een bepaald type om meer specifieke informatie te verkrijgen over wat er wordt weergegeven. Het resultaat hiervan laat *room0* en *room1* zien, maar **niet** *floor0* of *floor1* (omdat dat verdiepingen zijn en geen ruimten).
    
    :::image type="content" source="media/tutorial-command-line-app/output-query-model.png" alt-text="Resultaten van modelquery, alleen room0 en room1 weergegeven":::

* **Wat zijn alle ruimten op *floor0*?** (query op relatie)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0'
    ```

    U kunt query's uitvoeren op basis van relaties in uw grafiek, om informatie te krijgen over de manier waarop tweelingen zijn verbonden of om uw query te beperken tot een bepaald gebied. Alleen *room0* bevindt zich op *floor0*, dus is dit de enige ruimte in het resultaat.

    :::image type="content" source="media/tutorial-command-line-app/output-query-relationship.png" alt-text="Resultaten van relatiequery, met room0 weergegeven":::

* **Wat zijn de tweelingen in mijn omgeving met een temperatuur van meer dan 75 °F?** (query op eigenschap)

    ```cmd/sh
    Query SELECT * FROM DigitalTwins T WHERE T.Temperature > 75
    ```

    U kunt een query uitvoeren op de grafiek op basis van eigenschappen om allerlei vragen te beantwoorden, met inbegrip van uitschieters in uw omgeving die mogelijk aandacht vereisen. Andere vergelijkingsoperators ( *<* , *>* , *=* of *!=* ) worden ook ondersteund. *room1* wordt hier weergegeven in de resultaten, omdat deze een temperatuur van 80 °F heeft.

    :::image type="content" source="media/tutorial-command-line-app/output-query-property.png" alt-text="Resultaten van eigenschapsquery, alleen room1 weergegeven":::

* **Wat zijn alle ruimten op *floor0* met een temperatuur van meer dan 75 °F?** (samengestelde query)

    ```cmd/sh
    Query SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75
    ```

    U kunt de eerdere query's ook combineren zoals u dat in SQL zou doen, met behulp van combinatie-operatoren zoals `AND`, `OR`, `NOT`. Deze query maakt gebruik van `AND` om de vorige query over tweelingtemperaturen specifieker te maken. Het resultaat bevat nu alleen ruimten met een temperatuur van meer dan 75 °F op *floor0*; in dit geval geen. De resultatenset is leeg.

    :::image type="content" source="media/tutorial-command-line-app/output-query-compound.png" alt-text="Resultaten van samengestelde query, met geen resultaten":::

## <a name="clean-up-resources"></a>Resources opschonen

Het project in deze zelfstudie vormt de basis voor de volgende zelfstudie, [*Zelfstudie: Een end-to-end-oplossing verbinden*](tutorial-end-to-end.md). Als u van plan bent door te gaan naar de volgende zelfstudie, kunt u de resources die u hier hebt ingesteld behouden en deze Azure Digital Twins-instantie en geconfigureerde voorbeeld-app blijven gebruiken.
* In dit geval kunt u de voorbeeld-apps `DeleteAllTwins` en `DeleteAllModels` gebruiken om de tweelingen respectievelijk de modellen in uw instantie op te ruimen. Hiermee krijgt u een schone lei voor de volgende zelfstudie.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Verwijder ten slotte de voorbeeldprojectmap die u hebt gedownload naar uw lokale computer.

## <a name="next-steps"></a>Volgende stappen 

In deze zelfstudie bent u begonnen met Azure Digital Twins door het opzetten van een instantie en een clienttoepassing om met de instantie te interageren. U hebt de client-app gebruikt om Azure Digital Twins te verkennen, waarbij u modellen, digitale tweelingen en relaties hebt gemaakt. U hebt ook enkele query's op de oplossing uitgevoerd om een idee te krijgen van de vragen die Azure Digital Twins over een omgeving kan beantwoorden.

Ga door naar de volgende zelfstudie om de voorbeeldopdrachtregel-app te gebruiken in combinatie met andere Azure-services om een op gegevens gebaseerd end-to-end scenario te voltooien:
> [!div class="nextstepaction"]
> [*Zelfstudie: Een end-to-end-oplossing verbinden*](tutorial-end-to-end.md)