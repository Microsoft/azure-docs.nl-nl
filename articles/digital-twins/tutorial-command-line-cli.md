---
title: 'Zelf studie: een grafiek maken in azure Digital Apparaatdubbels (CLI)'
titleSuffix: Azure Digital Twins
description: Zelf studie voor het bouwen van een Azure Digital Apparaatdubbels-scenario met Azure CLI
author: baanders
ms.author: baanders
ms.date: 2/26/2021
ms.topic: tutorial
ms.service: digital-twins
ms.openlocfilehash: d155d0c4a18b254f66ff5fb58ea91dbee22d2c34
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496606"
---
# <a name="tutorial-create-an-azure-digital-twins-graph-using-the-azure-cli"></a>Zelf studie: een Azure Digital Apparaatdubbels-grafiek maken met behulp van de Azure CLI

[!INCLUDE [digital-twins-tutorial-selector.md](../../includes/digital-twins-tutorial-selector.md)]

In deze zelf studie maakt u een grafiek in azure Digital Apparaatdubbels met behulp van modellen, apparaatdubbels en relaties. Het hulp programma voor deze zelf studie is de [Azure Digital apparaatdubbels-opdracht die is ingesteld voor de **Azure cli**](how-to-use-cli.md). 

U kunt de CLI-opdrachten gebruiken om essentiële Azure Digital Apparaatdubbels-acties uit te voeren, zoals het uploaden van modellen, het maken en wijzigen van apparaatdubbels en het maken van relaties. U kunt ook de [referentie documentatie voor *AZ DT* command set](/cli/azure/ext/azure-iot/dt?preserve-view=true&view=azure-cli-latest) raadplegen voor een overzicht van de volledige set cli-opdrachten.

In deze zelfstudie gaat u...
> [!div class="checklist"]
> * Een omgeving model leren
> * Digitale tweelingen maken
> * Relaties toevoegen om een grafiek te maken
> * Query's uitvoeren op de grafiek om vragen te beantwoorden

## <a name="prerequisites"></a>Vereisten

Als u de stappen in deze zelf studie wilt uitvoeren, moet u eerst de volgende vereisten volt ooien.

Als u geen abonnement op Azure hebt, **maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)** voordat u begint.

### <a name="download-the-sample-models"></a>De voorbeeld modellen downloaden

In de zelf studie worden twee vooraf geschreven modellen gebruikt die deel uitmaken van het C# [end-to-end-voorbeeld project](/samples/azure-samples/digital-twins-samples/digital-twins-samples/) voor Azure Digital apparaatdubbels. De model bestanden bevinden zich hier: 
* [*Room.jsop*](https://github.com/Azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Room.json)
* [*Floor.jsop*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json)

Als u de bestanden op uw computer wilt ophalen, gebruikt u de navigatie koppelingen hierboven en kopieert u de bestands teksten naar lokale bestanden op uw computer met dezelfde namen (*Room.jsop* en *Floor.jsop*).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="set-up-cloud-shell-session"></a>Een Cloud Shell-sessie instellen
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

### <a name="prepare-an-azure-digital-twins-instance"></a>Een Azure Digital Twins-exemplaar voorbereiden

Als u in dit artikel met Azure Digital Twins wilt werken, moet u eerst **een Azure Digital Twins-instantie instellen**, evenals de vereiste machtigingen om die te gebruiken. Als u al een Azure Digital Twins-exemplaar hebt ingesteld voor een eerdere opdracht, kunt u dit exemplaar gebruiken.

Volg anders de instructies in [*Instructies: een exemplaar en verificatie instellen*](how-to-set-up-instance-cli.md). De instructies bevatten ook stappen om te controleren of u elke stap hebt voltooid en gereed bent om door te gaan met het nieuwe exemplaar.

Nadat u uw Azure Digital Twins-exemplaar hebt ingesteld, noteert u de volgende waarden die u later nodig hebt om verbinding te maken met het exemplaar:
* de hostnaam van het exemplaar ****
* het **Azure-abonnement** dat u hebt gebruikt om het exemplaar te maken. 

U kunt beide waarden voor uw exemplaar ophalen in de uitvoer van de volgende Azure CLI-opdracht: 

```azurecli-interactive
az dt show -n <ADT_instance_name>
```

:::image type="content" source="media/tutorial-command-line/cli/instance-details.png" alt-text="Scherm opname van Cloud Shell browser venster waarin de uitvoer van de opdracht AZ DT show wordt weer gegeven. Het veld hostnaam en abonnements-ID (onderdeel van het veld id) zijn gemarkeerd.":::

## <a name="model-a-physical-environment-with-dtdl"></a>Een fysieke omgeving modelleren met DTDL

Nu het CLI-en Azure Digital Apparaatdubbels-exemplaar is ingesteld, kunt u beginnen met het bouwen van een grafiek van een scenario. 

De eerste stap bij het maken van een Azure Digital Twins-oplossing is het definiëren van tweeling [**modellen**](concepts-models.md) voor uw omgeving. 

Modellen lijken op klassen in objectgeoriënteerde programmeertalen. Ze bieden door de gebruiker gedefinieerde sjablonen die [digitale tweelingen](concepts-twins-graph.md) later kunnen volgen en instantiëren. Ze zijn geschreven in een JSON-achtige taal, **Digital Twins Definition Language (DTDL)** genaamd, en kunnen de *eigenschappen*, *telemetrie*, *relaties* en *componenten* van een tweeling definiëren.

> [!NOTE]
> Met DTDL kunnen ook *opdrachten* op digitale tweelingen worden gedefinieerd. Opdrachten worden momenteel echter niet ondersteund in de Azure Digital Twins-service.

Navigeer op uw computer naar het *Room.js* bestand dat u hebt gemaakt in de sectie [vereisten](#prerequisites) . Open het bestand in een code-editor en wijzig het op de volgende manieren:

[!INCLUDE [digital-twins-tutorial-model-create.md](../../includes/digital-twins-tutorial-model-create.md)]

### <a name="upload-models-to-azure-digital-twins"></a>Modellen uploaden naar Azure Digital Twins

Na het ontwerpen van modellen moet u deze uploaden naar uw Azure Digital Twins-instantie. Hierdoor wordt uw Azure Digital Twins-service-instantie geconfigureerd met de woordenlijst van uw eigen aangepaste domein. Nadat u de modellen hebt geüpload, kunt u tweelinginstanties maken die ze gebruiken.

1. Als u modellen wilt toevoegen met behulp van Cloud Shell, moet u uw model bestanden uploaden naar de opslag van Cloud Shell, zodat de bestanden beschikbaar zijn wanneer u de Cloud Shell opdracht uitvoert die deze gebruikt. Als u dit wilt doen, selecteert u het pictogram bestanden uploaden/downloaden en kiest u uploaden.

    :::image type="content" source="media/how-to-set-up-instance/cloud-shell/cloud-shell-upload.png" alt-text="Scherm opname van Cloud Shell browser venster waarin de selectie van het pictogram voor uploaden wordt weer gegeven.":::
    
    Navigeer naar het *Room.js* bestand op uw computer en selecteer openen. Herhaal deze stap vervolgens voor *Floor.jsop*.

1. Gebruik vervolgens de opdracht [**AZ DT model Create**](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_create) , zoals hieronder wordt weer gegeven om uw bijgewerkte *room* -model te uploaden naar uw Azure Digital apparaatdubbels-exemplaar. Met de tweede opdracht uploadt u een ander model, *basis*, dat u ook in de volgende sectie kunt gebruiken om verschillende soorten apparaatdubbels te maken.

    ```azurecli-interactive
    az dt model create -n <ADT_instance_name> --models Room.json
    az dt model create -n <ADT_instance_name> --models Floor.json
    ```
    
    De uitvoer van elke opdracht geeft informatie weer over het model dat is geüpload.

    >[!TIP]
    >U kunt ook alle modellen in een directory tegelijk uploaden met behulp van de `--from-directory` optie voor de opdracht model maken. Zie [optionele para meters voor *AZ DT model Create*](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_create-optional-parameters)(Engelstalig) voor meer informatie.

1. Controleer of de modellen zijn gemaakt met de opdracht [**AZ DT model List**](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_list) , zoals hieronder wordt weer gegeven. Hiermee wordt een lijst afgedrukt van alle modellen die zijn geüpload naar het Azure Digital Apparaatdubbels-exemplaar met de volledige informatie. 

    ```azurecli-interactive
    az dt model list -n <ADT_instance_name> --definition
    ```
    
    Bekijk het bewerkte *Room*-model in de resultaten:
    
    :::image type="content" source="media/tutorial-command-line/cli/output-get-models.png" alt-text="Scherm opname van Cloud Shell weer geven van het resultaat van de model lijst opdracht, inclusief het bijgewerkte room-model." lightbox="media/tutorial-command-line/cli/output-get-models.png":::

### <a name="errors"></a>Fouten

De CLI verwerkt ook fouten van de service. 

Voer de opdracht `az dt model create` nogmaals uit om te proberen een van de modellen die u zojuist het geüpload een tweede keer te uploaden:

```azurecli-interactive
az dt model create -n <ADT_instance_name> --models Room.json
```

Omdat modellen niet kunnen worden overschreven, wordt er nu een fout code van weer gegeven `ModelIdAlreadyExists` .

## <a name="create-digital-twins"></a>Digitale tweelingen maken

Nu er een paar modellen zijn geüpload naar uw instantie van Azure Digital Twins, kunt u [**digitale tweelingen**](concepts-twins-graph.md) maken op basis van de modeldefinities. Digitale tweelingen vertegenwoordigen de entiteiten in uw bedrijfsomgeving: dingen als sensoren op een boerderij, ruimten in een gebouw, of lichten in een auto. 

Als u een digitale dubbele wilt maken, gebruikt u de opdracht [**AZ DT dubbele maken**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_create) . U moet verwijzen naar het model waarop de tweeling is gebaseerd, en kunt desgewenst aanvangswaarden definiëren voor alle eigenschappen in het model. U hoeft in dit stadium geen relatiegegevens door de geven.

1. Voer deze code uit in het Cloud Shell om verschillende apparaatdubbels te maken, op basis van het *room* -model dat u eerder hebt bijgewerkt en een ander *model, basis*. Zoals u weet heeft *Room* drie eigenschappen, zodat u argumenten kunt opgeven met de aanvangswaarden daarvan. (Initialisatie van eigenschaps waarden is optioneel in het algemeen, maar deze zijn nodig voor deze zelf studie.)

    ```azurecli-interactive
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{"RoomName":"Room0", "Temperature":70, "HumidityLevel":30}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{"RoomName":"Room1", "Temperature":"80", "HumidityLevel":"60"}'
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor0
    az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Floor;1" --twin-id floor1
    ```

    >[!NOTE]
    > Als u Cloud Shell gebruikt in de Power shell-omgeving, moet u mogelijk de aanhalings tekens in de aangegeven volg orde afleiden zodat de `--properties` JSON-waarde correct kan worden geparseerd. Met deze bewerking kunnen de opdrachten voor het maken van de ruimte apparaatdubbels er als volgt uitzien:
    >
    > ```azurecli-interactive
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room0 --properties '{\"RoomName\":\"Room0\", \"Temperature\":70, \"HumidityLevel\":30}'
    > az dt twin create -n <ADT_instance_name> --dtmi "dtmi:example:Room;2" --twin-id room1 --properties '{\"RoomName\":\"Room1\", \"Temperature\":80, \"HumidityLevel\":60}'
    > ```
    > Dit wordt weer gegeven in de onderstaande scherm afbeelding.
    
    De uitvoer van elke opdracht geeft informatie weer over de met succes gemaakte dubbele (inclusief eigenschappen voor de apparaatdubbels die met hen zijn geïnitialiseerd).

1. U kunt controleren of de apparaatdubbels zijn gemaakt met de opdracht [**AZ DT dubbele query**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_query) , zoals hieronder wordt weer gegeven. De query die wordt weer gegeven, zoekt alle digitale apparaatdubbels in uw Azure Digital Apparaatdubbels-exemplaar.
    
    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```
    
    Zoek in de resultaten naar de *room0*-, *room1*-, *floor0*-en *floor1* -apparaatdubbels. Hier volgt een fragment dat een deel van het resultaat van deze query weergeeft.
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Scherm afbeelding van Cloud Shell het gedeeltelijk resultaat van een dubbele query, waaronder room0 en room1, worden weer gegeven." lightbox="media/tutorial-command-line/cli/output-query-all.png":::

### <a name="modify-a-digital-twin"></a>Een digital twin wijzigen

U kunt de eigenschappen van een digitale dubbel die u hebt gemaakt, ook wijzigen. 

1. Voer deze [**AZ DT**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_update) -opdracht met dubbele update uit om de *room0*-kamer van *room0* te wijzigen in *PresidentialSuite*:

    ```azurecli-interactive
    az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{"op":"add", "path":"/RoomName", "value": "PresidentialSuite"}'
    ```
    
    >[!NOTE]
    > Als u Cloud Shell gebruikt in de Power shell-omgeving, moet u mogelijk de aanhalings tekens in de aangegeven volg orde afleiden zodat de `--json-patch` JSON-waarde correct kan worden geparseerd. Met deze bewerking wordt de opdracht voor het bijwerken van de dubbele vormgeving als volgt:
    >
    > ```azurecli-interactive
    > az dt twin update -n <ADT_instance_name> --twin-id room0 --json-patch '{\"op\":\"add\", \"path\":\"/RoomName\", \"value\": \"PresidentialSuite\"}'
    > ```
    > Dit wordt weer gegeven in de onderstaande scherm afbeelding.
    
    Met de uitvoer van deze opdracht worden de huidige gegevens van de twee weer gegeven en ziet u de nieuwe waarde voor de `RoomName` in het resultaat.

    :::image type="content" source="media/tutorial-command-line/cli/output-update-twin.png" alt-text="Scherm opname van Cloud Shell weer geven van het resultaat van de update opdracht, met inbegrip van een kamershape van PresidentialSuite." lightbox="media/tutorial-command-line/cli/output-update-twin.png":::

1. U kunt controleren of de update is geslaagd door de opdracht [**AZ DT dubbele weer geven**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_show) uit te voeren om de informatie van *room0* weer te geven:

    ```azurecli-interactive
    az dt twin show -n <ADT_instance_name> --twin-id room0
    ```
    
    De uitvoer zou de bijgewerkte naam moeten weerspiegelen.

## <a name="create-a-graph-by-adding-relationships"></a>Maak een grafiek door relaties toe te voegen

Vervolgens kunt u **relaties** maken tussen deze tweelingen, om ze te verbinden in een [**tweelinggrafiek**](concepts-twins-graph.md). Tweelinggrafieken worden gebruikt om een gehele omgeving voor te stellen. 

De typen relaties die u kunt maken op basis van een dubbele naar een andere, worden gedefinieerd in de [modellen](#model-a-physical-environment-with-dtdl) die u eerder hebt geüpload. De [model definitie voor *vloer*](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) geeft aan dat vloeren een type relatie met de naam *contains* kunnen hebben. Dit maakt het mogelijk om een *contains*-type-relatie te maken van elke *vloer* , naar de bijbehorende ruimte die deze bevat.

Als u een relatie wilt toevoegen, gebruikt u de opdracht [**AZ DT dubbele relatie maken**](/cli/azure/ext/azure-iot/dt/twin/relationship?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_relationship_create) . Geef het dubbele op waarmee de relatie afkomstig is, het type relatie en de dubbele verbinding waarmee de relatie tot stand wordt gebracht. Ten slotte geeft u de relatie een unieke ID. Als een relatie is gedefinieerd om eigenschappen te hebben, kunt u de relatie-eigenschappen ook initialiseren in deze opdracht.

1. Voer de volgende code uit om een *contains*-type-relatie toe te voegen van elk van de *vloer* -apparaatdubbels die u eerder hebt gemaakt in de bijbehorende *ruimte* , twee. De relaties hebben de naam *relationship0* en *relationship1*.

    ```azurecli-interactive
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship0 --relationship contains --twin-id floor0 --target room0
    az dt twin relationship create -n <ADT_instance_name> --relationship-id relationship1 --relationship contains --twin-id floor1 --target room1
    ```
    
    >[!TIP]
    >De *contains* -relatie in het [ *basis* model](https://github.com/azure-Samples/digital-twins-samples/blob/master/AdtSampleApp/SampleClientApp/Models/Floor.json) is ook gedefinieerd met twee eigenschappen `ownershipUser` en `ownershipDepartment` , zodat u ook argumenten kunt opgeven met de aanvankelijke waarden voor deze wanneer u de relaties maakt.
    > Als u een relatie wilt maken waarbij deze eigenschappen zijn geïnitialiseerd, voegt `--properties` u de optie toe aan een van de bovenstaande opdrachten, zoals:
    > ```azurecli-interactive
    > ... --properties '{"ownershipUser":"MyUser", "ownershipDepartment":"MyDepartment"}'
    > ``` 
    > 
    > Als u Cloud Shell gebruikt in de Power shell-omgeving, moet u mogelijk de aanhalings tekens in de aangegeven volg orde afleiden zodat de `--properties` JSON-waarde correct kan worden geparseerd.
    
    De uitvoer van elke opdracht geeft informatie weer over de relatie die is gemaakt.

1. U kunt de relaties controleren met een van de volgende opdrachten, die query's uitvoeren op de relaties in uw Azure Digital Apparaatdubbels-exemplaar.
    * Om alle relaties weer te geven die op elke verdieping worden weer gegeven (de relaties vanaf één zijde bekijken):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor0
        az dt twin relationship list -n <ADT_instance_name> --twin-id floor1
        ```
    * Om alle relaties weer te geven die op elke ruimte arriveren (de relatie weer geven vanaf de ' andere ' zijde):
        ```azurecli-interactive
        az dt twin relationship list -n <ADT_instance_name> --twin-id room0 --incoming
        az dt twin relationship list -n <ADT_instance_name> --twin-id room1 --incoming
        ```
    * Als u deze relaties afzonderlijk zoekt, op ID:
        ```azurecli-interactive
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor0 --relationship-id relationship0
        az dt twin relationship show -n <ADT_instance_name> --twin-id floor1 --relationship-id relationship1
        ```

De tweelingen en relaties die u in deze zelfstudie hebt ingesteld vormen de volgende conceptuele grafiek:

:::image type="content" source="media/tutorial-command-line/app/sample-graph.png" alt-text="Een diagram waarin een conceptuele grafiek wordt weer gegeven. floor0 is verbonden via relationship0 met room0 en floor1 is verbonden via relationship1 met room1." border="false" lightbox="media/tutorial-command-line/app/sample-graph.png":::

## <a name="query-the-twin-graph-to-answer-environment-questions"></a>Query uitvoeren op de tweelinggrafiek om vragen over de omgeving te beantwoorden

Een hoofdfunctie van Azure Digital Twins is de mogelijkheid om gemakkelijk en efficiënt [query's](concepts-query-language.md) uit te voeren op uw tweelinggrafiek om vragen over uw omgeving te beantwoorden. In de Azure CLI wordt dit gedaan met de opdracht [**AZ DT dubbele query**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_query) .

Voer de volgende query's uit in de Cloud Shell om enkele vragen over de voorbeeld omgeving te beantwoorden.

1. **Welke entiteiten van mijn omgeving worden weer gegeven in azure Digital Apparaatdubbels?** (alles opvragen)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS"
    ```

    Hiermee kunt u in één oogopslag de balans opmaken van uw omgeving en ervoor zorgen dat alles binnen Azure Digital Twins wordt weergegeven zoals u het wilt. Het resultaat hiervan is een uitvoer die elke digitale tweeling met de gegevens ervan bevat. Hier volgt een fragment:

    :::image type="content" source="media/tutorial-command-line/cli/output-query-all.png" alt-text="Scherm afbeelding van Cloud Shell het gedeeltelijk resultaat van een dubbele query, waaronder room0 en room1, worden weer gegeven." lightbox="media/tutorial-command-line/cli/output-query-all.png":::

    >[!TIP]
    >U kunt herkennen dat dit dezelfde opdracht is die u hebt gebruikt in de sectie [*Digital Apparaatdubbels maken*](#create-digital-twins) om alle Azure Digital apparaatdubbels in het exemplaar te vinden.

1. **Wat zijn alle ruimten in mijn omgeving?** (query op model)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DIGITALTWINS T WHERE IS_OF_MODEL(T, 'dtmi:example:Room;2')"
    ```

    U kunt uw query beperken tot tweelingen van een bepaald type om meer specifieke informatie te verkrijgen over wat er wordt weergegeven. Het resultaat hiervan laat *room0* en *room1* zien, maar **niet** *floor0* of *floor1* (omdat dat verdiepingen zijn en geen ruimten).
    
    :::image type="content" source="media/tutorial-command-line/cli/output-query-model.png" alt-text="Scherm opname van Cloud Shell die het resultaat van een model query weer geven, met inbegrip van alleen room0 en room1." lightbox="media/tutorial-command-line/cli/output-query-model.png":::

1. **Wat zijn alle ruimten op *floor0*?** (query op relatie)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0'"
    ```

    U kunt query's uitvoeren op basis van relaties in uw grafiek, om informatie te krijgen over de manier waarop tweelingen zijn verbonden of om uw query te beperken tot een bepaald gebied. Alleen *room0* bevindt zich op *floor0*, dus is dit de enige ruimte in het resultaat.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-relationship.png" alt-text="Scherm opname van Cloud Shell met het resultaat van een relatie query, waaronder room0." lightbox="media/tutorial-command-line/cli/output-query-relationship.png":::

    > [!NOTE]
    > U ziet dat een dubbele ID (zoals *floor0* in de bovenstaande query) wordt opgevraagd met behulp van het veld meta gegevens `$dtId` . 
    >
    >Wanneer u Cloud Shell gebruikt om een query uit te voeren met meta gegevens velden zoals deze die beginnen met `$` , moet u de `$` with a-apostroffen om te laten Cloud shell weten dat het geen variabele is en moet worden gebruikt als een letterlijke waarde in de query tekst. Dit wordt weer gegeven in de bovenstaande scherm afbeelding.

1. **Wat zijn de tweelingen in mijn omgeving met een temperatuur van meer dan 75 °F?** (query op eigenschap)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT * FROM DigitalTwins T WHERE T.Temperature > 75"
    ```

    U kunt een query uitvoeren op de grafiek op basis van eigenschappen om allerlei vragen te beantwoorden, met inbegrip van uitschieters in uw omgeving die mogelijk aandacht vereisen. Andere vergelijkingsoperators ( *<* , *>* , *=* of *!=* ) worden ook ondersteund. *room1* wordt hier weergegeven in de resultaten, omdat deze een temperatuur van 80 °F heeft.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-property.png" alt-text="Scherm afbeelding van Cloud Shell weer gegeven resultaat van eigenschaps query, die alleen room1 bevat." lightbox="media/tutorial-command-line/cli/output-query-property.png":::

1. **Wat zijn alle ruimten op *floor0* met een temperatuur van meer dan 75 °F?** (samengestelde query)

    ```azurecli-interactive
    az dt twin query -n <ADT_instance_name> -q "SELECT room FROM DIGITALTWINS floor JOIN room RELATED floor.contains where floor.`$dtId = 'floor0' AND IS_OF_MODEL(room, 'dtmi:example:Room;2') AND room.Temperature > 75"
    ```

    U kunt de eerdere query's ook combineren zoals u dat in SQL zou doen, met behulp van combinatie-operatoren zoals `AND`, `OR`, `NOT`. Deze query maakt gebruik van `AND` om de vorige query over tweelingtemperaturen specifieker te maken. Het resultaat bevat nu alleen ruimten met een temperatuur van meer dan 75 °F op *floor0*; in dit geval geen. De resultatenset is leeg.

    :::image type="content" source="media/tutorial-command-line/cli/output-query-compound.png" alt-text="Scherm opname van Cloud Shell die het resultaat van een samengestelde query weer geven, die geen items bevat." lightbox="media/tutorial-command-line/cli/output-query-compound.png":::

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u deze zelf studie hebt voltooid, kunt u kiezen welke resources u wilt verwijderen, afhankelijk van wat u nu wilt doen.

* **Als u van plan bent om door te gaan met de volgende zelf studie**, kunt u de resources die u hier instelt, blijven gebruiken en het Azure Digital apparaatdubbels-exemplaar hergebruiken zonder dat u iets hoeft te doen.

* **Als u het Azure Digital apparaatdubbels-exemplaar wilt blijven gebruiken, maar alle modellen, apparaatdubbels en relaties** wilt verwijderen, kunt u de opdracht [**AZ DT dubbele relatie verwijderen**](/cli/azure/ext/azure-iot/dt/twin/relationship?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_relationship_delete), [**AZ DT dubbel delete**](/cli/azure/ext/azure-iot/dt/twin?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_twin_delete)en [**AZ DT model delete**](/cli/azure/ext/azure-iot/dt/model?view=azure-cli-latest&preserve-view=true#ext_azure_iot_az_dt_model_delete) gebruiken om de relaties, apparaatdubbels en modellen in uw exemplaar te wissen.

[!INCLUDE [digital-twins-cleanup-basic.md](../../includes/digital-twins-cleanup-basic.md)]

Misschien wilt u ook de model bestanden verwijderen die u hebt gemaakt op uw lokale machine.

## <a name="next-steps"></a>Volgende stappen 

In deze zelf studie hebt u met Azure Digital Apparaatdubbels begonnen met het bouwen van een grafiek in uw exemplaar met behulp van de Azure CLI. U hebt modellen, digitale apparaatdubbels en relaties gemaakt om een grafiek te maken. U hebt ook enkele query's uitgevoerd in de grafiek om een idee te krijgen van de vragen die Azure Digital Apparaatdubbels kan beantwoorden over een omgeving.

Ga door naar de volgende zelf studie om Azure Digital Apparaatdubbels te combi neren met andere Azure-Services om een gegevensgestuurde, end-to-end-scenario te volt ooien:
> [!div class="nextstepaction"]
> [*Zelfstudie: Een end-to-end-oplossing verbinden*](tutorial-end-to-end.md)