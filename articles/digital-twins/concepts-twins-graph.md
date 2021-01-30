---
title: Digital twins en het tweelinggrafiek
titleSuffix: Azure Digital Twins
description: Meer informatie over het concept van een digitaal, en hoe hun relaties een grafiek maken.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 105128af60b90324e93bcad1bd9900a55461378f
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071712"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>Meer informatie over digitale apparaatdubbels en hun twee grafieken

In een Azure Digital Apparaatdubbels-oplossing worden de entiteiten in uw omgeving vertegenwoordigd door **Digital apparaatdubbels**. Een digitale dubbele is een exemplaar van een van uw door uw eigen gedefinieerde [modellen](concepts-models.md). Het kan worden verbonden met andere digitale apparaatdubbels via **relaties** om een **dubbele grafiek** te vormen: deze dubbele grafiek is de weer gave van uw gehele omgeving.

> [!TIP]
> ' Azure Digital Apparaatdubbels ' verwijst naar deze Azure-service als geheel. ' Digitale twee (en) ' of alleen ' dubbele (s) ' verwijzen naar afzonderlijke dubbele knoop punten in uw exemplaar van de service.

## <a name="digital-twins"></a>Digitale tweelingen

Voordat u een digitale dubbele in uw Azure Digital Apparaatdubbels-exemplaar kunt maken, moet u een *model* hebben dat naar de service wordt geüpload. Een model beschrijft de set eigenschappen, telemetrie-berichten en relaties die een bepaalde dubbele kan hebben, onder andere. Zie [*concepten: Custom modelers*](concepts-models.md)(Engelstalig) voor informatie over de typen die in een model zijn gedefinieerd.

Na het maken en uploaden van een model, kan uw client-app een exemplaar van het type maken. Dit is een digitale dubbele. Nadat u bijvoorbeeld een model van de *vloer* hebt gemaakt, kunt u een of meer digitale apparaatdubbels maken die gebruikmaken van dit type (zoals een *Floor*-type met dubbele naam *GroundFloor*, een andere met de naam *Floor2*, enzovoort).

[!INCLUDE [digital-twins-versus-device-twins](../../includes/digital-twins-versus-device-twins.md)]

## <a name="relationships-a-graph-of-digital-twins"></a>Relaties: een grafiek met digitale apparaatdubbels

Apparaatdubbels zijn verbonden met een twee ledige grafiek met hun relaties. De relaties die een dubbele kan hebben, worden gedefinieerd als onderdeel van het model.  

De model *Floor* kan bijvoorbeeld een *contains* -relatie definiëren die de apparaatdubbels van het type *room* bedoelt. Met deze definitie kunt u met Azure Digital Apparaatdubbels de relaties maken van elke *vloer* , in plaats van een wille keurige *kamer* , met inbegrip *van apparaatdubbels* die subtypen *bevatten.* 

Het resultaat van dit proces is een set knoop punten (de Digital apparaatdubbels) die is verbonden via randen (hun relaties) in een grafiek.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-with-the-apis"></a>Maken met de Api's

In deze sectie ziet u hoe u digitale apparaatdubbels en relaties van een client toepassing maakt. Het bevat .NET-code voorbeelden die gebruikmaken van de [DigitalTwins-api's](/rest/api/digital-twins/dataplane/twins), om aanvullende context te bieden over wat er in elk van deze concepten gaat.

### <a name="create-digital-twins"></a>Digitale tweelingen maken

Hieronder vindt u een code fragment van de clientcode die gebruikmaakt van de [DigitalTwins-api's](/rest/api/digital-twins/dataplane/twins) voor het instantiëren van een dubbele van het type *kamer*.

U kunt de eigenschappen van een dubbele waarde initialiseren wanneer deze wordt gemaakt of later instellen. Als u een dubbele met geïnitialiseerde eigenschappen wilt maken, moet u een JSON-document maken dat de benodigde initialisatie waarden verschaft.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

U kunt ook een helper-klasse met de naam gebruiken `BasicDigitalTwin` om eigenschaps velden in een ' twee ' object direct sneller op te slaan als alternatief voor het gebruik van een woorden lijst. Zie voor meer informatie over de Help-klasse en voor beelden van het gebruik van het artikel [*een digitale dubbele sectie maken*](how-to-manage-twin.md#create-a-digital-twin) van *How-to: Manage Digital apparaatdubbels*.

>[!NOTE]
>Hoewel dubbele eigenschappen als optioneel worden beschouwd en dus niet hoeven te worden geïnitialiseerd, moeten [onderdelen](concepts-models.md#elements-of-a-model) op de dubbele **taak** worden ingesteld wanneer de dubbele eigenschap wordt gemaakt. Ze kunnen lege objecten zijn, maar de onderdelen zelf moeten aanwezig zijn.

### <a name="create-relationships"></a>Relaties maken

Hier volgt een voor beeld van een client code die gebruikmaakt van de [DigitalTwins-api's](/rest/api/digital-twins/dataplane/twins) voor het bouwen van een relatie tussen een van de twee *basis* typen, *GroundFloor* en een *kamer* type digitale dubbele naam *Cafe*.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_3":::

## <a name="json-representations-of-graph-elements"></a>JSON-representaties van grafiek elementen

Digitale en dubbele gegevens en relatie gegevens worden beide opgeslagen in JSON-indeling. Dit betekent dat wanneer u een [query uitvoert op de dubbele grafiek](how-to-query-graph.md) in uw Azure Digital apparaatdubbels-exemplaar, het resultaat een JSON-weer gave is van digitale apparaatdubbels en relaties die u hebt gemaakt.

### <a name="digital-twin-json-format"></a>Digitale dubbele JSON-indeling

Wanneer het wordt weer gegeven als een JSON-object, worden de volgende velden weer gegeven in een digitale dubbele:

| Veldnaam | Beschrijving |
| --- | --- |
| `$dtId` | Een door de gebruiker gegeven teken reeks voor de ID van de digitale dubbele |
| `$etag` | Standaard-HTTP-veld toegewezen door de webserver |
| `$conformance` | Een enum met de status van de conformiteit van deze digitale twee richting *(conformiteit*, *non-conformiteit*, *onbekend*) |
| `{propertyName}` | De waarde van een eigenschap in JSON ( `string` , Number type of object) |
| `$relationships` | De URL van het pad naar de verzameling relationships. Dit veld is niet aanwezig als de digitale twee kanten geen uitgaande relatie randen hebben. |
| `$metadata.$model` | Beschrijving De ID van de model interface die dit digitale dubbele |
| `$metadata.{propertyName}.desiredValue` | [Alleen voor Beschrijf bare eigenschappen] De gewenste waarde van de opgegeven eigenschap |
| `$metadata.{propertyName}.desiredVersion` | [Alleen voor Beschrijf bare eigenschappen] De versie van de gewenste waarde |
| `$metadata.{propertyName}.ackVersion` | De versie die wordt bevestigd door de apparaat-app die de digitale-twee implementeert |
| `$metadata.{propertyName}.ackCode` | [Alleen voor Beschrijf bare eigenschappen] De `ack` code die wordt geretourneerd door de apparaat-app die de digitale-twee implementeert |
| `$metadata.{propertyName}.ackDescription` | [Alleen voor Beschrijf bare eigenschappen] De `ack` beschrijving die wordt geretourneerd door de apparaat-app die de digitale-twee implementeert |
| `{componentName}` | Een JSON-object dat de eigenschaps waarden en meta gegevens van de component bevat, vergelijkbaar met die van het hoofd object. Dit object bestaat ook als het onderdeel geen eigenschappen heeft. |
| `{componentName}.{propertyName}` | De waarde van de eigenschap van het onderdeel in JSON ( `string` , Number type of object) |
| `{componentName}.$metadata` | De meta gegevens voor het onderdeel, vergelijkbaar met het hoofd niveau `$metadata` |

Hier volgt een voor beeld van een digitale dubbele indeling als een JSON-object:

```json
{
  "$dtId": "Cafe",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
      "TableOccupancy": {
        "desiredValue": 1,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Room;1",
    "Temperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "Location": {
      "desiredValue": {
        "x": 101,
        "y": 33,
      },
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="relationship-json-format"></a>De relatie-JSON-indeling

Wanneer wordt weer gegeven als een JSON-object, worden de volgende velden weer gegeven in een relatie van een digitale dubbele:

| Veldnaam | Beschrijving |
| --- | --- |
| `$relationshipId` | Een door de gebruiker gegeven teken reeks voor de ID van deze relatie. Deze teken reeks is uniek in de context van de bron digitale dubbele, wat betekent dat deze `sourceId`  +  `relationshipId` uniek is in de context van het Azure Digital apparaatdubbels-exemplaar. |
| `$etag` | Standaard-HTTP-veld toegewezen door de webserver |
| `$sourceId` | De ID van de bron digitale dubbele |
| `$targetId` | De ID van de doel-digitale-dubbele |
| `$relationshipName` | De naam van de relatie |
| `{propertyName}` | Beschrijving De waarde van een eigenschap van deze relatie, in JSON ( `string` , Number type of object) |

Hier volgt een voor beeld van een relatie die is opgemaakt als een JSON-object:

```json
{
  "$relationshipId": "relationship-01",
  "$etag": "W/\"506e8391-2b21-4ac9-bca3-53e6620f6a90\"",
  "$sourceId": "GroundFloor",
  "$targetId": "Cafe",
  "$relationshipName": "contains",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>Volgende stappen

Zie grafiek elementen beheren met Azure Digital-Api's:
* [*Instructies: digitale apparaatdubbels beheren*](how-to-manage-twin.md)
* [*Instructies: de dubbele grafiek met relaties beheren*](how-to-manage-graph.md)

U kunt ook meer informatie vinden over het uitvoeren van query's op de Azure Digital Apparaatdubbels-grafiek:
* [*Concepten: query taal*](concepts-query-language.md)