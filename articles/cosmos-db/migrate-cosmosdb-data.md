---
title: Honderden terabytes aan gegevens migreren naar Azure Cosmos DB
description: In dit document wordt beschreven hoe u een 100s van terabytes aan gegevens kunt migreren naar Cosmos DB
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/23/2019
ms.openlocfilehash: c45445415f3eaa7cb0f9069dd5f64b57c19e5836
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96437147"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>Honderden terabytes aan gegevens migreren naar Azure Cosmos DB 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB kan terabytes aan gegevens opslaan. U kunt een grootschalige gegevensmigratie uitvoeren om de werkbelasting van uw productie naar Azure Cosmos DB te verplaatsen. In dit artikel worden de uitdagingen beschreven van het verplaatsen van grootschalige gegevens naar Azure Cosmos DB en wordt u voorgesteld aan het hulpprogramma dat u helpt bij deze uitdagingen en dat gegevens naar Azure Cosmos DB migreert. In deze casestudy heeft de klant de Cosmos DB SQL-API gebruikt.  

Voordat u de volledige werk belasting naar Azure Cosmos DB migreert, kunt u een subset van gegevens migreren om enkele van de aspecten te valideren, zoals de partitie sleutel keuze, query prestaties en gegevens modellering. Nadat u het testen van het concept hebt gevalideerd, kunt u de gehele werk belasting naar Azure Cosmos DB verplaatsen.  

## <a name="tools-for-data-migration"></a>Hulpprogram ma's voor gegevens migratie 

Azure Cosmos DB migratie strategieën op dit moment verschillen op basis van de API-keuze en de grootte van de gegevens. Voor het migreren van kleinere gegevens sets – voor het valideren van de gegevensmodeling, de query prestaties, de partitie sleutel keuze, enzovoort: u kunt het [hulp programma voor gegevens migratie](import-data.md) of de [Azure Cosmos DB-connector van de Azure Data Factory](../data-factory/connector-azure-cosmos-db.md)kiezen. Als u bekend bent met Spark, kunt u er ook voor kiezen om de [Azure Cosmos DB Spark-connector](spark-connector.md) te gebruiken om gegevens te migreren.

## <a name="challenges-for-large-scale-migrations"></a>Uitdagingen voor grootschalige migraties 

De bestaande hulpprogram ma's voor het migreren van gegevens naar Azure Cosmos DB hebben enkele beperkingen die bijzonder duidelijk worden op grote schaal bewerkingen:

 * **Beperkte mogelijkheden voor uitschalen**: om terabytes aan gegevens zo snel mogelijk naar Azure Cosmos DB te migreren en de volledige ingerichte door Voer effectief te verbruiken, moeten de migratie-clients voor onbepaalde tijd kunnen uitschalen.  

* **Geen voortgang van bijhouden en controleren**: het is belang rijk om de voortgang van de migratie bij te houden en te controleren op het moment dat u grote gegevens sets migreert. Anders moet elke fout die tijdens de migratie optreedt, de migratie stoppen en moet u het proces helemaal opnieuw starten. Het is niet productief om het hele migratie proces opnieuw te starten wanneer 99% van de service al is voltooid.  

* **Geen wachtrij met onbestelbare berichten**: in grote gegevens sets kunnen er in sommige gevallen problemen optreden met delen van de bron gegevens. Daarnaast kunnen er tijdelijke problemen zijn met de client of het netwerk. Een van deze gevallen zou ertoe kunnen leiden dat de volledige migratie niet kan worden uitgevoerd. Hoewel de meeste migratie hulpprogramma's robuuste pogingen bieden die zich tegen tijdelijke problemen beschermen, is het niet altijd voldoende. Als er bijvoorbeeld minder dan 0,01% van de brongegevens documenten groter is dan 2 MB, wordt het schrijven van het document in Azure Cosmos DB mislukt. In het ideale geval is het handig voor het migratie programma om deze ' mislukte ' documenten op te slaan in een andere wachtrij met onbestelbare berichten. Dit kan worden verwerkt na de migratie. 

Veel van deze beperkingen worden vastgesteld voor hulpprogram ma's zoals Azure Data Factory, Azure Data Migration Services. 

## <a name="custom-tool-with-bulk-executor-library"></a>Aangepast hulp programma met de bibliotheek voor bulksgewijs uitvoerder 

De uitdagingen die in de bovenstaande sectie worden beschreven, kunnen worden opgelost met behulp van een aangepast hulp programma dat eenvoudig kan worden uitgeschaald over meerdere instanties en dat kan leiden tot tijdelijke storingen. Daarnaast kan het aangepaste hulp programma de migratie op verschillende controle punten onderbreken en hervatten. Azure Cosmos DB biedt al de [bulk](./bulk-executor-overview.md) -uitvoerder bibliotheek die enkele van deze functies bevat. De bibliotheek bulk-uitvoerder heeft bijvoorbeeld al de functionaliteit voor het afhandelen van tijdelijke fouten en het kan uitschalen van threads in één knoop punt om te verbruiken ongeveer 500 K RUs per knoop punt. De bulk-uitvoerder bibliotheek partitioneert ook de bron-gegevensset in micro batches die onafhankelijk worden uitgevoerd als een vorm van controle punten.  

Het aangepaste hulp programma maakt gebruik van de bibliotheek voor bulk-uitvoerder en biedt ondersteuning voor uitbrei ding op meerdere clients en bij het volgen van fouten tijdens het opname proces. Als u dit hulp programma wilt gebruiken, moeten de bron gegevens worden gepartitioneerd in afzonderlijke bestanden in Azure Data Lake Storage (ADLS) zodat verschillende migratie medewerkers elk bestand kunnen ophalen en opnemen in Azure Cosmos DB. Het aangepaste hulp programma maakt gebruik van een afzonderlijke verzameling, die meta gegevens over de voortgang van de migratie voor elk afzonderlijk bron bestand in ADLS opslaat en eventuele fouten registreert die eraan zijn gekoppeld.  

In de volgende afbeelding wordt het migratie proces beschreven dat gebruikmaakt van dit aangepaste hulp programma. Het hulp programma wordt uitgevoerd op een set virtuele machines en elke virtuele machine voert een query uit op de tracerings verzameling in Azure Cosmos DB om een lease te verkrijgen op een van de bron gegevens partities. Zodra dit is gebeurd, wordt de bron gegevens partitie door het hulp programma gelezen en opgenomen in Azure Cosmos DB met behulp van de bulk-uitvoerder bibliotheek. Vervolgens wordt de verzameling bijhouden bijgewerkt met de voortgang van de opname van gegevens en eventuele fouten die zijn opgetreden. Nadat een gegevens partitie is verwerkt, probeert het hulp programma de volgende beschik bare bron partitie op te vragen. Het proces blijft de volgende bron partitie verwerken totdat alle gegevens zijn gemigreerd. De bron code voor het hulp programma is [hier](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion)beschikbaar.  

 
:::image type="content" source="./media/migrate-cosmosdb-data/migrationsetup.png" alt-text="Migratie Hulpprogramma's instellen" border="false":::
 

 

De tracerings verzameling bevat documenten, zoals wordt weer gegeven in het volgende voor beeld. Deze documenten worden één voor elke partitie in de bron gegevens weer gegeven.  Elk document bevat de meta gegevens voor de gegevens partitie van de bron, zoals de locatie, de migratie status en eventuele fouten (indien van toepassing):  

```json
{ 
  "owner": "25812@bulkimporttest07", 
  "jsonStoreEntityImportResponse": { 
    "numberOfDocumentsReceived": 446688, 
    "isError": false, 
    "totalRequestUnitsConsumed": 3950252.2800000003, 
    "errorInfo": [], 
    "totalTimeTakenInSeconds": 188, 
    "numberOfDocumentsImported": 446688 
  }, 
  "storeType": "AZURE_BLOB", 
  "name": "sourceDataPartition", 
  "location": "sourceDataPartitionLocation", 
  "id": "sourceDataPartitionId", 
  "isInProgress": false, 
  "operation": "unpartitioned-writes", 
  "createDate": { 
    "seconds": 1561667225, 
    "nanos": 146000000 
  }, 
  "completeDate": { 
    "seconds": 1561667515, 
    "nanos": 180000000 
  }, 
  "isComplete": true 
} 
```
 

## <a name="prerequisites-for-data-migration"></a>Vereisten voor gegevens migratie 

Voordat de gegevens migratie wordt gestart, moet u rekening houden met de volgende vereisten:  

#### <a name="estimate-the-data-size"></a>Schatting van de gegevens grootte:  

De grootte van de bron gegevens kan niet exact worden toegewezen aan de gegevens grootte in Azure Cosmos DB. Er kunnen een aantal voorbeeld documenten uit de bron worden ingevoegd om de gegevens grootte in Azure Cosmos DB te controleren. Afhankelijk van de grootte van het voorbeeld document, kan de totale gegevens grootte in Azure Cosmos DB na de migratie worden geschat. 

Als bijvoorbeeld elk document na de migratie in Azure Cosmos DB ongeveer 1 KB is en als er ongeveer 60.000.000.000 documenten in de bron-gegevensset staan, zou de geschatte grootte in Azure Cosmos DB dicht bij 60 TB liggen. 

 

#### <a name="pre-create-containers-with-enough-rus"></a>Maak vooraf containers met voldoende RUs: 

Hoewel Azure Cosmos DB opslag automatisch wordt geschaald, is het niet raadzaam om te beginnen met de kleinste container grootte. Kleinere containers hebben een lagere Beschik baarheid van de door Voer, wat betekent dat de migratie veel meer tijd in beslag neemt. In plaats daarvan is het handig om de containers met de uiteindelijke gegevens grootte te maken (zoals in de vorige stap is geschat) en om ervoor te zorgen dat de werk belasting van de migratie de ingerichte door Voer volledig verbruikt.  

In de vorige stap. omdat de omvang van de gegevens ongeveer 60 TB werd geschat, is een container van ten minste 2,4 M RUs vereist voor de volledige gegevensset.  

 

#### <a name="estimate-the-migration-speed"></a>De migratie snelheid schatten: 

Ervan uitgaande dat de werk belasting van de migratie de volledige ingerichte door Voer kan verbruiken, is de beschik bare hoeveelheid een schatting van de migratie snelheid. Het vorige voor beeld is 5 RUs vereist voor het schrijven van een document van 1 KB naar Azure Cosmos DB SQL-API-account.  2.400.000 RUs zou een overdracht van 480.000 documenten per seconde (of 480 MB/s) toestaan. Dit betekent dat de volledige migratie van 60 TB 125.000 seconden of ongeveer 34 uur duurt.  

Als u de migratie binnen een dag wilt volt ooien, moet u de ingerichte door Voer verhogen tot 5.000.000 RUs. 

 

#### <a name="turn-off-the-indexing"></a>Het indexeren uitschakelen:  

Aangezien de migratie zo snel mogelijk moet worden voltooid, is het raadzaam om de tijd en het RUs te minimaliseren die zijn besteed aan het maken van indexen voor elk van de opgenomen documenten.  Azure Cosmos DB alle eigenschappen automatisch indexeert, is het de moeite waard om het indexeren naar een of meer voor waarden te minimaliseren of het volledig uit te scha kelen voor de cursus van de migratie. U kunt het indexerings beleid van de container uitschakelen door de indexingMode te wijzigen in geen, zoals hieronder wordt weer gegeven:  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

Nadat de migratie is voltooid, kunt u de indexering bijwerken.  

## <a name="migration-process"></a>Migratieproces 

Nadat de vereisten zijn voltooid, kunt u gegevens migreren met de volgende stappen:  

1. Importeer eerst de gegevens van de bron naar Azure Blob Storage. Om de migratie snelheid te verhogen, is het handig om te parallelliserenen voor verschillende bron partities. Voordat u met de migratie begint, moet de bron gegevensverzameling zijn gepartitioneerd naar bestanden met een grootte van ongeveer 200 MB.   

2. De bibliotheek voor bulk-uitvoerder kan worden geschaald om 500.000 RUs te gebruiken in één client-VM. Omdat de beschik bare door Voer is 5.000.000 RUs, moeten 10 Ubuntu 16,04 Vm's (Standard_D32_v3) worden ingericht in dezelfde regio waarin uw Azure Cosmos-data base zich bevindt. U moet deze Vm's voorbereiden met het hulp programma voor migratie en het bijbehorende instellingen bestand.  

3. Voer de wachtrij stap uit op een van de virtuele machines van de client. Met deze stap maakt u de tracerings verzameling, die de ADLS-container scant en een document voor voortgangs tracering maakt voor elk van de partitie bestanden van de bron gegevens.  

4. Voer vervolgens de stap importeren uit op alle client-Vm's. Elk van de clients kan eigenaar worden van een bron partitie en de gegevens opnemen in Azure Cosmos DB. Zodra de service is voltooid en de status ervan wordt bijgewerkt in de tracerings verzameling, kunnen de clients vervolgens een query uitvoeren voor de volgende beschik bare bron partitie in de verzameling tracking.  

5. Dit proces wordt voortgezet totdat de volledige set met bron partities is opgenomen. Zodra alle bron partities zijn verwerkt, moet u het hulp programma opnieuw uitvoeren in de modus voor fout correctie op dezelfde tracking-verzameling. Deze stap is vereist om de bron partities te identificeren die opnieuw moeten worden verwerkt als gevolg van fouten.  

6. Sommige van deze fouten kunnen worden veroorzaakt door onjuiste documenten in de bron gegevens. Deze moeten worden aangeduid en opgelost. Vervolgens moet u de import stap op de mislukte partities opnieuw uitvoeren om ze opnieuw op te nemen. 

Nadat de migratie is voltooid, kunt u controleren of het aantal documenten in Azure Cosmos DB hetzelfde is als het aantal documenten in de bron database. In dit voor beeld is de totale grootte in Azure Cosmos DB 65 terabytes. Na de migratie kan indexering selectief worden ingeschakeld en het RUs kan worden verlaagd naar het niveau dat nodig is voor de bewerkingen van de werk belasting.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het uitproberen van de voorbeeld toepassingen die de bulk-uitvoerder bibliotheek in [.net](bulk-executor-dot-net.md) en [Java](bulk-executor-java.md)gebruiken. 
* De bibliotheek bulk-uitvoerder is geïntegreerd in de Cosmos DB Spark-connector, Zie [Azure Cosmos DB artikel Spark-connector](spark-connector.md) voor meer informatie.  
* Neem contact op met het product team van Azure Cosmos DB door een ondersteunings ticket te openen onder het probleem type ' algemeen advies ' en ' grote (TB +) ' subtype ' voor meer informatie over grootschalige migraties.