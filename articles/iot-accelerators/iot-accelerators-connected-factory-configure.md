---
title: De Connected Factory-topologie configureren-Azure | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de Connected Factory Solution Accelerator met de bijbehorende topologie kunt configureren.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: e015c6761b920ef37af2bbfd67ced5fc3218d532
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105646078"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>De Connected Factory Solution Accelerator configureren

> [!IMPORTANT]
> Zie [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) voor de meest recente inhoud terwijl we dit artikel bijwerken.

De Connected Factory Solution Accelerator toont een gesimuleerd dash board voor een fictief bedrijf contoso. Dit bedrijf heeft fabrieken in diverse wereld wijde locaties.

In dit artikel wordt contoso als voor beeld gebruikt om te beschrijven hoe u de topologie van een verbonden fabrieks oplossing kunt configureren.

## <a name="simulated-factories-configuration"></a>Configuratie gesimuleerde fabrieken

Elke contoso fabriek heeft productie lijnen die bestaan uit drie stations. Elk station is een echte OPC UA-server met een specifieke rol:

* Assembly station
* Test station
* Verpakkings station

Deze OPC UA-servers hebben OPC UA-knoop punten en [OPC Publisher](overview-opc-publisher.md) verzendt de waarden van deze knoop punten naar verbonden Factory. Dit omvat:

* Huidige operationele status, zoals het huidige energie verbruik.
* Productie-informatie, zoals het aantal geproduceerde producten.

U kunt het dash board gebruiken om in te zoomen op de contoso-fabrieks topologie vanuit een globale weer gave naar een weer gave op het niveau van het station. Het Connected Factory-dash board maakt het volgende mogelijk:

* De visualisatie van OEE-en KPI-cijfers voor elke laag in de topologie.
* De visualisatie van huidige waarden van OPC UA-knoop punten in de stations.
* De aggregatie van de OEE-en KPI-cijfers van het niveau station naar het globale niveau.
* De visualisatie van waarschuwingen en acties die moeten worden uitgevoerd als waarden specifieke drempels bereiken.

## <a name="connected-factory-topology"></a>Topologie Connected Factory

De topologie van fabrieken, productie lijnen en stations is hiërarchisch:

* Het globale niveau heeft fabrieks knooppunten als onderliggende knoop punten.
* De fabrieken hebben productie regel knooppunten als onderliggende knoop punten.
* De productie lijnen hebben station knooppunten als onderliggende knoop punten.
* De stations (OPC UA-servers) hebben OPC UA-knoop punten als onderliggend niveau.

Elk knoop punt in de topologie heeft een gemeen schappelijke set eigenschappen die het volgende definiëren:

* Een unieke id voor het topologie knooppunt.
* Een naam.
* Een beschrijving.
* Een afbeelding.
* De onderliggende elementen van het topologie knooppunt.
* Minimum, doel en maximum waarden voor OEE en KPI-cijfers en de waarschuwings acties die moeten worden uitgevoerd.

## <a name="topology-configuration-file"></a>Topologie configuratie bestand

Voor het configureren van de eigenschappen die in de vorige sectie worden weer gegeven, gebruikt de oplossing Connected Factory een configuratie bestand met de naam [ContosoTopologyDescription.jsop](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

U kunt dit bestand vinden in de bron code van de oplossing in de `WebApp/Contoso/Topology` map.

Het volgende code fragment toont een overzicht van het `ContosoTopologyDescription.json` configuratie bestand:

```json
{
  <global_configuration>,
  "Factories": [
    <factory_configuration>,
    "ProductionLines": [
      <production_line_configuration>,
      "Stations": [
        <station_configuration>,
        <more station_configurations>
      ],
      <more production_line_configurations>
    ]
    <more factory_configurations>
  ]
}
```

De algemene eigenschappen van `<global_configuration>` , `<factory_configuration>` , `<production_line_configuration>` en `<station_configuration>` zijn:

* **Naam** (type teken reeks)

  Definieert een beschrijvende naam, die slechts één woord mag zijn voor het topologie knooppunt dat in het dash board wordt weer gegeven.

* **Beschrijving** (type teken reeks)

  Beschrijft het knoop punt van de topologie in meer detail.

* **Afbeelding** (type teken reeks)

  Het pad naar een installatie kopie in de WebApp-oplossing die moet worden weer gegeven wanneer informatie over het topologie knooppunt wordt weer gegeven in het dash board.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (type `<performance_definition>` )

  Met deze eigenschappen worden de minimale, doel-en maximale waarden gedefinieerd van het operationele afbeelding dat wordt gebruikt voor het genereren van waarschuwingen. Met deze eigenschappen worden ook de acties gedefinieerd die moeten worden uitgevoerd als er een waarschuwing wordt gedetecteerd.

De `<factory_configuration>` `<production_line_configuration>` items en hebben de volgende eigenschap:

* **GUID** (type teken reeks)

  Hiermee wordt het topologie knooppunt uniek geïdentificeerd.

`<factory_configuration>` heeft een eigenschap:

* **Locatie** (type `<location_definition>` )

  Hiermee geeft u op waar de fabriek zich bevindt.

`<station_configuration>` heeft eigenschappen:

* **OpcUri** (type teken reeks)

  Deze eigenschap moet worden ingesteld op de OPC UA-toepassings-URI van de OPC UA-server.
  Omdat het globaal uniek moet zijn door de OPC UA-specificatie, wordt deze eigenschap gebruikt om het knoop punt van de station-topologie te identificeren.

* **OpcNodes**, een matrix met OPC UA-knoop punten (type `<opc_node_description>` )

`<location_definition>` heeft eigenschappen:

* **City** (type teken reeks)

  De naam van de plaats die het dichtst bij de locatie ligt

* **Land** (type teken reeks)

  Land van de locatie

* **Latitude** (type Double)

  De breedte graad van de locatie

* **Lengte graad** (type Double)

  De lengte graad van de locatie

`<performance_definition>` heeft eigenschappen:

* **Minimum** (type Double)

  Onderste drempel waarde kan worden bereikt. Als de huidige waarde lager is dan deze drempel, wordt er een waarschuwing gegenereerd.

* **Doel** (type Double)

  Ideale doel waarde.

* **Maximum** (type Double)

  Bovenste drempel waarde kan worden bereikt. Als de huidige waarde hoger is dan deze drempel, wordt er een waarschuwing gegenereerd.

* **MinimumAlertActions** (type `<alert_action>` )

  Hiermee wordt de set acties gedefinieerd die als reactie op een minimale waarschuwing kan worden beschouwd.

* **MaximumAlertActions** (type `<alert_action>` )

  Hiermee wordt de set acties gedefinieerd die als reactie op een maximum waarschuwing kan worden beschouwd.

`<alert_action`> heeft eigenschappen:

* **Type** (teken reeks)

  Het type van de waarschuwings actie. De volgende typen zijn bekend:

  * **AcknowledgeAlert**: de status van de waarschuwing moet worden gewijzigd in bevestigd.
  * **CloseAlert**: alle oudere waarschuwingen van hetzelfde type mogen niet meer worden weer gegeven in het dash board.
  * **CallOpcMethod**: u moet een OPC UA-methode aanroepen.
  * Open **webpagina**: er moet een browser venster worden geopend met aanvullende contextuele informatie.

* **Beschrijving** (type teken reeks)

  Beschrijving van de actie die in het dash board wordt weer gegeven.

* **Para meter** (type teken reeks)

  Vereiste para meters voor het uitvoeren van de actie. De waarde is afhankelijk van het actie type.

  * **AcknowledgeAlert**: er is geen para meter vereist.
  * **CloseAlert**: er is geen para meter vereist.
  * **CallOpcMethod**: de knooppunt gegevens en-para meters van de OPC UA-methode voor het aanroepen van de indeling "NodeId van bovenliggend knoop punt, NodeId van methode om aan te roepen, URI van de OPC UA-server."
  * **Openwebpagina**: de URL die in het browser venster moet worden weer gegeven.

`<opc_node_description>` bevat informatie over OPC UA-knoop punten in een station (OPC UA-server). Knoop punten die geen bestaande OPC UA-knoop punten vertegenwoordigen, maar worden gebruikt als opslag in de berekenings logica van verbonden Factory zijn ook geldig. Het heeft de volgende eigenschappen:

* **NodeId** (type teken reeks)

  Adres van het OPC UA-knoop punt in de adres ruimte van het station (OPC UA-server). De syntaxis moet worden opgegeven in de OPC UA-specificatie voor een NodeId.

* **Symbolische** (type teken reeks)

  De naam die moet worden weer gegeven in het dash board wanneer de waarde van dit OPC UA-knoop punt wordt weer gegeven.

* **Relevantie** (matrix van het type teken reeks)

  Geeft aan dat de waarde van het OPC UA-knoop punt voor de berekening van OEE of KPI relevant is. Elk matrix element kan een van de volgende waarden hebben:

  * **OeeAvailability_Running**: de waarde is relevant voor de berekening van de beschik BAARHEID van OEE.
  * **OeeAvailability_Fault**: de waarde is relevant voor de berekening van de beschik BAARHEID van OEE.
  * **OeePerformance_Ideal**: de waarde is relevant voor de berekening van de prestaties van OEE en is doorgaans een constante waarde.
  * **OeePerformance_Actual**: de waarde is relevant voor de berekening van de prestaties van OEE.
  * **OeeQuality_Good**: de waarde is relevant voor de berekening van de OEE kwaliteit.
  * **OeeQuality_Bad**: de waarde is relevant voor de berekening van de OEE kwaliteit.
  * **Kpi1**: de waarde is relevant voor de berekening van Kpi1.
  * **Kpi2**: de waarde is relevant voor de berekening van Kpi2.

* **Opcode** (type teken reeks)

  Hiermee wordt aangegeven hoe de waarde van het OPC UA-knoop punt wordt verwerkt in time series Insight-query's en OEE/KPI-berekeningen. Elke time series Insight-query streeft naar een specifieke time span, een para meter van de query en levert een resultaat op. De opcode bepaalt hoe het resultaat wordt berekend en kan een van de volgende waarden hebben:

  * **Diff**: verschil tussen de laatste en de eerste waarde in de time span.
  * **Gem**: het gemiddelde van alle waarden in de time span.
  * **Sum**: de som van alle waarden in de time span.
  * **Laatst**: momenteel niet gebruikt.
  * **Aantal**: het aantal waarden in de time span.
  * **Max**: de maximale waarde in de time span.
  * **Min**: de minimale waarde in de time span.
  * **Const**: het resultaat is de waarde die is opgegeven door de eigenschap ConstValue.
  * **SubMaxMin**: het verschil tussen de maximale en de minimale waarde.
  * **Time span**: de time span.

* **Eenheden** (type teken reeks)

  Definieert een eenheid van de waarde die in het dash board wordt weer gegeven.

* **Visible** (type Boole)

  Hiermee wordt bepaald of de waarde in het dash board moet worden weer gegeven.

* **ConstValue** (type Double)

  Als de **opcode** wordt **Const**, is deze eigenschap de waarde van het knoop punt.

* **Minimum** (type Double)

  Als de huidige waarde onder deze waarde valt, wordt er een minimale waarschuwing gegenereerd.

* **Maximum** (type Double)

  Als de huidige waarde boven deze waarde komt, wordt er een maximum waarschuwing gegenereerd.

* **MinimumAlertActions** (type `<alert_action>` )

  Hiermee wordt de set acties gedefinieerd die als reactie op een minimale waarschuwing kan worden beschouwd.

* **MaximumAlertActions** (type `<alert_action>` )

  Hiermee wordt de set acties gedefinieerd die als reactie op een maximum waarschuwing kan worden beschouwd.

Op het niveau van de stations ziet u ook **simulatie** objecten. Deze objecten worden alleen gebruikt voor het configureren van de verbonden Factory-simulatie en mogen niet worden gebruikt voor het configureren van een echte topologie.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Hoe de configuratie gegevens tijdens runtime worden gebruikt

Alle eigenschappen die in het configuratie bestand worden gebruikt, kunnen in verschillende categorieën worden gegroepeerd, afhankelijk van hoe ze worden gebruikt. Deze categorieën zijn:

### <a name="visual-appearance"></a>Visuele weer gave

Met de eigenschappen in deze categorie wordt het uiterlijk van het Connected Factory-dash board gedefinieerd. Enkele voorbeelden:

* Naam
* Beschrijving
* Installatiekopie
* Locatie
* Eenheden
* Zichtbaar

### <a name="internal-topology-tree-addressing"></a>Interne topologie structuur

De WebApp onderhoudt een intern data dictionary met informatie over alle topologie knooppunten. De eigenschappen **GUID** en **OpcUri** worden gebruikt als sleutels voor toegang tot deze woorden lijst en moeten uniek zijn.

### <a name="oeekpi-computation"></a>OEE/KPI-berekening

De OEE/KPI-cijfers voor de verbonden Factory-simulatie zijn para meters voor:

* De waarde van het OPC UA-knoop punt dat moet worden opgenomen in de berekening.
* Hoe de afbeelding wordt berekend op basis van de telemetrie-waarden.

Verbonden Factory maakt gebruik van de OEE-formules zoals gepubliceerd door de [http://www.oeefoundation.org](http://www.oeefoundation.org) .

OPC UA-knooppunt objecten in stations maken labels mogelijk voor gebruik in de OEE/KPI-berekening. De eigenschap **relevantie** geeft aan waarvoor OEE/KPI de waarde van het OPC UA-knoop punt moet worden gebruikt. De eigenschap **opcode** definieert hoe de waarde wordt opgenomen in de berekening.

### <a name="alert-handling"></a>Afhandeling van waarschuwingen

Verbonden Factory ondersteunt een mechanisme voor het genereren van een eenvoudige minimale/maximale drempel waarde voor het optellen van waarschuwingen. Er zijn een aantal vooraf gedefinieerde acties die u kunt configureren als reactie op deze waarschuwingen. De volgende eigenschappen bepalen dit mechanisme:

* Maximum
* Minimum
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Correleren met telemetrie-gegevens

Voor bepaalde bewerkingen, zoals het visualiseren van de laatste waarde of het maken van Time Series Insight-query's, heeft de WebApp een adresserings schema nodig voor de opgenomen telemetriegegevens. De telemetrie die naar een verbonden Factory wordt verzonden, moet ook worden opgeslagen in interne gegevens structuren. De twee eigenschappen die deze bewerkingen inschakelen, bevinden zich op station (OPC UA-server) en het OPC UA-knooppunt niveau:

* **OpcUri**

  Identificeert (wereld wijd uniek) de OPC UA-server van de telemetrie. In de opgenomen berichten wordt deze eigenschap verzonden als **sorteert ze**.

* **NodeId**

  Hiermee wordt de knooppunt waarde in de OPC UA-server geïdentificeerd. De indeling van de eigenschap moet zijn opgegeven in de OPC UA-specificatie. In de opgenomen berichten wordt deze eigenschap verzonden als **NodeId**.

Zie [Wat is OPC Publisher](overview-opc-publisher.md) voor meer informatie over hoe de telemetriegegevens worden opgenomen in verbonden Factory.

## <a name="example-how-kpi1-is-calculated"></a>Voor beeld: hoe KPI1 wordt berekend

De configuratie in het `ContosoTopologyDescription.json` bestand bepaalt hoe OEE/KPI-cijfers worden berekend. In het volgende voor beeld ziet u hoe de eigenschappen in dit bestand de berekening van KPI1 bepalen.

In Connected Factory KPI1 wordt gebruikt om het aantal geproduceerde producten in het afgelopen uur te meten. Elk station (OPC UA-server) in de verbonden Factory-simulatie biedt een OPC UA-knoop punt ( `NodeId: "ns=2;i=385"` ) dat de telemetrie biedt om deze KPI te berekenen.

De configuratie voor dit OPC UA-knoop punt ziet eruit als in het volgende code fragment:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Met deze configuratie kunt u query's uitvoeren op de telemetrie-waarden van dit knoop punt met behulp van Time Series Insights. De Time Series Insights query haalt het volgende op:

* Het aantal waarden.
* De minimale waarde.
* De maximale waarde.
* Het gemiddelde van alle waarden.
* De som van alle waarden voor alle unieke **OpcUri** (**sorteert ze**), **NodeId** -paren in een bepaalde time span.

Een kenmerk van de waarde van het knoop punt **NumberOfManufactureredProducts** is dat deze alleen wordt verhoogd. Voor het berekenen van het aantal producten dat in de time span wordt geproduceerd, gebruikt de verbonden Factory de **opcode** - **SubMaxMin**. De berekening haalt de minimum waarde op aan het begin van de time span en de maximum waarde aan het einde van de time span.

Met de **opcode** in de configuratie wordt de berekenings logica geconfigureerd voor het berekenen van het resultaat van het verschil met de maximum-en minimum waarde. Deze resultaten worden vervolgens onder elkaar verzameld naar het hoofd niveau (globaal) en weer gegeven in het dash board.

## <a name="next-steps"></a>Volgende stappen

Een voorgestelde volgende stap is informatie over [het aanpassen van de oplossing Connected Factory](iot-accelerators-connected-factory-customize.md).
