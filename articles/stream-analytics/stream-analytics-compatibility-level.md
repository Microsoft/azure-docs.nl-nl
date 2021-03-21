---
title: Compatibiliteits niveaus Azure Stream Analytics
description: Meer informatie over het instellen van een compatibiliteits niveau voor een Azure Stream Analytics-taak en belang rijke wijzigingen in het meest recente compatibiliteits niveau
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: a040aecbdee40832bd21256e26a140a986b65e39
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606239"
---
# <a name="compatibility-level-for-azure-stream-analytics-jobs"></a>Compatibiliteits niveau voor Azure Stream Analytics taken

In dit artikel wordt de optie compatibiliteits niveau in Azure Stream Analytics beschreven.

Stream Analytics is een beheerde service, met [gewone functie-updates en constante prestatie verbeteringen](https://azure.microsoft.com/updates/?product=stream-analytics). De meeste runtime-updates van de service worden automatisch beschikbaar gemaakt voor eind gebruikers, onafhankelijk van het compatibiliteits niveau. Wanneer een nieuwe functionaliteit echter een wijziging in het gedrag van bestaande taken introduceert, of een wijziging in de manier waarop gegevens worden gebruikt bij het uitvoeren van taken, introduceren we deze wijziging onder een nieuw compatibiliteits niveau. U kunt ervoor zorgen dat uw bestaande Stream Analytics-taken worden uitgevoerd zonder grote wijzigingen door de instelling voor het compatibiliteits niveau te verlagen. Wanneer u klaar bent voor het laatste runtime-gedrag, kunt u zich aanmelden door het compatibiliteits niveau te verhogen.


## <a name="choose-a-compatibility-level"></a>Een compatibiliteits niveau kiezen

Compatibiliteits niveau bepaalt het runtime gedrag van een stream Analytics-taak.

Azure Stream Analytics ondersteunt momenteel drie compatibiliteits niveaus:

* 1,2-nieuwste gedrag met de meest recente verbeteringen
* 1,1-vorig gedrag
* 1,0-oorspronkelijk compatibiliteits niveau, geïntroduceerd tijdens de algemene Beschik baarheid van Azure Stream Analytics enkele jaren geleden. 

Wanneer u een nieuwe Stream Analytics taak maakt, is dit een best practice om deze te maken met behulp van het meest recente compatibiliteits niveau. Start uw taak ontwerp dat afhankelijk is van het nieuwste gedrag, zodat u later extra wijzigingen en complexiteit kunt voor komen.

## <a name="set-the-compatibility-level"></a>Het compatibiliteits niveau instellen

U kunt het compatibiliteits niveau instellen voor een Stream Analytics taak in het Azure Portal of met behulp van de [aanroep taak maken rest API](/rest/api/streamanalytics/2016-03-01/streamingjobs/createorreplace#compatibilitylevel).

Het compatibiliteits niveau van de taak in het Azure Portal bijwerken:

1. Gebruik de [Azure Portal](https://portal.azure.com) om naar uw stream Analytics-taak te zoeken.
2. **Stop** de taak voordat u het compatibiliteits niveau bijwerkt. Als uw taak wordt uitgevoerd, kunt u het compatibiliteits niveau niet bijwerken.
3. Onder de kop **configureren** selecteert u **compatibiliteits niveau**.
4. Kies de gewenste waarde voor het compatibiliteits niveau.
5. Selecteer onder aan de pagina **Opslaan** .

![Compatibiliteits niveau Stream Analytics in Azure Portal](media/stream-analytics-compatibility-level/stream-analytics-compat-level-1-2.png)

Wanneer u het compatibiliteits niveau bijwerkt, valideert de T-compiler de taak met de syntaxis die overeenkomt met het geselecteerde compatibiliteits niveau.

## <a name="compatibility-level-12"></a>Compatibiliteits niveau 1,2

De volgende belang rijke wijzigingen worden geïntroduceerd in compatibiliteits niveau 1,2:

###  <a name="amqp-messaging-protocol"></a>AMQP Messa ging Protocol

**1,2-niveau**: Azure stream Analytics gebruikt [AMQP-berichten Protocol (Advanced Message queueing Protocol)](../service-bus-messaging/service-bus-amqp-overview.md) om te schrijven naar service bus-wacht rijen en-onderwerpen. Met AMQP kunt u platform onafhankelijke, hybride toepassingen bouwen met behulp van een open standaard protocol.

### <a name="geospatial-functions"></a>Georuimtelijke functies

**Eerdere niveaus:** Geografie berekeningen Azure Stream Analytics gebruikt.

**niveau van 1,2:** Met Azure Stream Analytics kunt u geometrische geprojecteerde geo-coördinaten berekenen. Er is geen wijziging in de hand tekening van de georuimtelijke functies. De semantiek is echter iets anders, waardoor nauw keuriger kan worden gerekend dan voorheen.

Azure Stream Analytics ondersteunt het indexeren van georuimtelijke referentie gegevens. Referentie gegevens die georuimtelijke elementen bevatten, kunnen worden geïndexeerd voor een snellere samenvoegings berekening.

Met de bijgewerkte georuimtelijke functies wordt de WKT-indeling (volledig herkende tekst) met een georuimtelijke notatie. U kunt andere georuimtelijke onderdelen opgeven die niet eerder met geojson worden ondersteund.

Zie [updates voor georuimtelijke functies in azure stream Analytics-Cloud en IOT Edge](https://azure.microsoft.com/blog/updates-to-geospatial-functions-in-azure-stream-analytics-cloud-and-iot-edge/)voor meer informatie.

### <a name="parallel-query-execution-for-input-sources-with-multiple-partitions"></a>Parallelle query uitvoering voor invoer bronnen met meerdere partities

**Eerdere niveaus:** Voor Azure Stream Analytics query's is het gebruik van de component PARTITION BY vereist voor het verwerken van parallelliseren-query's tussen de verschillende invoer bron partities.

**niveau van 1,2:** Als query logica kan worden geparallelleerd tussen de verschillende invoer bron partities, maakt Azure Stream Analytics afzonderlijke query-instanties en worden berekeningen parallel uitgevoerd.

### <a name="native-bulk-api-integration-with-cosmosdb-output"></a>Systeem eigen bulk-API-integratie met CosmosDB-uitvoer

**Eerdere niveaus:** Het upsert-gedrag is *invoegen of samen voegen*.

**niveau van 1,2:** Systeem eigen bulk-API-integratie met CosmosDB-uitvoer maximaliseert de door Voer en efficiënt afhandelen van bandbreedte aanvragen. Zie [de pagina Azure stream Analytics uitvoer naar Azure Cosmos DB](./stream-analytics-documentdb-output.md#improved-throughput-with-compatibility-level-12)voor meer informatie.

Het gedrag van de upsert wordt *ingevoegd of vervangen*.

### <a name="datetimeoffset-when-writing-to-sql-output"></a>Date time offset bij het schrijven naar SQL-uitvoer

**Eerdere niveaus:** [Date Time offset](/sql/t-sql/data-types/datetimeoffset-transact-sql) types zijn aangepast aan UTC.

**niveau van 1,2:** Date time offset wordt niet meer aangepast.

### <a name="long-when-writing-to-sql-output"></a>Lang bij het schrijven naar SQL-uitvoer

**Eerdere niveaus:** De waarden zijn afgekapt op basis van het doel type.

**niveau van 1,2:** Waarden die niet in het doel type passen, worden afgehandeld op basis van het uitvoer fout beleid.

### <a name="record-and-array-serialization-when-writing-to-sql-output"></a>Serialisatie van records en matrices bij het schrijven naar SQL-uitvoer

**Eerdere niveaus:** Records zijn geschreven als ' record ' en matrices zijn geschreven als ' matrix '.

**niveau van 1,2:** Records en matrices worden geserialiseerd in JSON-indeling.

### <a name="strict-validation-of-prefix-of-functions"></a>Strikte validatie van het voor voegsel van functies

**Eerdere niveaus:** Er is geen strikte validatie van functie voorvoegsels.

**niveau van 1,2:** Azure Stream Analytics heeft een strikte validatie van functie voorvoegsels. Als u een voor voegsel toevoegt aan een ingebouwde functie, treedt er een fout op. Wordt bijvoorbeeld `myprefix.ABS(…)` niet ondersteund.

Het toevoegen van een voor voegsel aan ingebouwde aggregaties resulteert ook in een fout. Wordt bijvoorbeeld `myprefix.SUM(…)` niet ondersteund.

Als u het voor voegsel ' System ' gebruikt voor door de gebruiker gedefinieerde functies, treedt er een fout op.

### <a name="disallow-array-and-object-as-key-properties-in-cosmos-db-output-adapter"></a>Matrix en object niet toestaan als sleutel eigenschappen in Cosmos DB uitvoer adapter

**Eerdere niveaus:** Matrix-en object typen worden ondersteund als een sleutel eigenschap.

**niveau van 1,2:** Matrix-en object typen worden niet meer ondersteund als een sleutel eigenschap.

## <a name="compatibility-level-11"></a>Compatibiliteits niveau 1,1

De volgende belang rijke wijzigingen worden geïntroduceerd in compatibiliteits niveau 1,1:

### <a name="service-bus-xml-format"></a>Service Bus XML-indeling

**niveau van 1,0:** Azure Stream Analytics gebruikgemaakt van DataContractSerializer, waardoor de inhoud van het bericht XML-tags bevat. Bijvoorbeeld:

`@\u0006string\b3http://schemas.microsoft.com/2003/10/Serialization/\u0001{ "SensorId":"1", "Temperature":64\}\u0001`

**niveau van 1,1:** De inhoud van het bericht bevat de stream direct zonder extra tags. Bijvoorbeeld: `{ "SensorId":"1", "Temperature":64}`

### <a name="persisting-case-sensitivity-for-field-names"></a>Hoofdletter gevoeligheid voor veld namen persistent maken

**niveau van 1,0:** Veld namen zijn in kleine letters gewijzigd wanneer ze worden verwerkt door de Azure Stream Analytics-engine.

**niveau 1,1:** hoofdletter gevoeligheid is persistent voor veld namen wanneer deze worden verwerkt door de Azure stream Analytics-engine.

> [!NOTE]
> Het persistent maken van cases is nog niet beschikbaar voor stream Analytics-taken die worden gehost door de Edge-omgeving. Als gevolg hiervan worden alle veld namen omgezet in kleine letters als uw taak wordt gehost aan de rand.

### <a name="floatnandeserializationdisabled"></a>FloatNaNDeserializationDisabled

**niveau van 1,0:** CREATE TABLE opdracht heeft geen gebeurtenissen gefilterd met NaN (geen-a-number). Bijvoorbeeld oneindig,-oneindig) in een FLOAT-kolom Type omdat deze buiten het gedocumenteerde bereik voor deze getallen vallen.

**niveau van 1,1:** Met CREATE TABLE kunt u een sterk schema opgeven. De Stream Analytics-engine valideert dat de gegevens voldoen aan dit schema. Met dit model kan de opdracht gebeurtenissen filteren met NaN-waarden.

### <a name="disable-automatic-conversion-of-datetime-strings-to-datetime-type-at-ingress-for-json"></a>Automatische conversie van datetime-teken reeksen uitschakelen naar DateTime-type bij ingangen voor JSON

**niveau van 1,0:** Met de JSON-parser worden teken reeks waarden met datum/tijd/zone-informatie automatisch geconverteerd naar DATETIME-type bij ingangs gegevens zodat de oorspronkelijke notatie en tijd zone-informatie in de waarde direct verloren gaat. Omdat dit wordt gedaan bij inkomend verkeer, zelfs als dat veld niet in de query is gebruikt, wordt dit geconverteerd naar UTC DateTime.

**niveau van 1,1:** Er wordt geen automatische conversie van teken reeks waarden met datum/tijd/zone-informatie naar DATETIME-type. Als gevolg hiervan worden de tijdzone gegevens en de oorspronkelijke opmaak bewaard. Als het veld NVARCHAR (MAX) echter wordt gebruikt in de query als onderdeel van een datum/tijd-expressie (bijvoorbeeld de functie DATEADD), wordt dit geconverteerd naar het DATETIME-type om de berekening uit te voeren en verliest het oorspronkelijke formulier.

## <a name="next-steps"></a>Volgende stappen

* [Problemen met Azure Stream Analytics invoer oplossen](stream-analytics-troubleshoot-input.md)
* [Resource status Stream Analytics](./stream-analytics-troubleshoot-query.md)
