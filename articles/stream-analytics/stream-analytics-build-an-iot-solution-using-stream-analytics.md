---
title: Een IoT-oplossing bouwen met behulp van Azure Stream Analytics
description: Aan de slag met de zelf studie voor de Stream Analytics IoT-oplossing van een tollbooth-scenario
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/06/2018
ms.custom: seodec18
ms.openlocfilehash: ddec53b18cd6f374a5665298b43b46122bcfa143
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98016147"
---
# <a name="build-an-iot-solution-by-using-stream-analytics"></a>Een IoT-oplossing bouwen met behulp van Stream Analytics

## <a name="introduction"></a>Introductie
In deze oplossing leert u hoe u Azure Stream Analytics kunt gebruiken om real-time inzichten uit uw gegevens te halen. Ontwikkel aars kunnen eenvoudig gegevens stromen combi neren, zoals Klik-streams, logboeken en door apparaten gegenereerde gebeurtenissen, met historische records of referentie gegevens om zakelijke inzichten af te leiden. Als volledig beheerde, realtime stroom berekenings service die wordt gehost in Microsoft Azure, bevat Azure Stream Analytics ingebouwde tolerantie, lage latentie en schaal baarheid, zodat u binnen enkele minuten aan de slag kunt.

Nadat u deze oplossing hebt voltooid, kunt u het volgende doen:

* Raadpleeg de Azure Stream Analytics Portal.
* Een streaming-taak configureren en implementeren.
* Praktijk problemen opgeleen en oplossen met behulp van de Stream Analytics query taal.
* Ontwikkel streaming-oplossingen voor uw klanten door gebruik te maken van Stream Analytics met vertrouwen.
* Gebruik de controle-en logboek registratie-ervaring om problemen op te lossen.

## <a name="prerequisites"></a>Vereisten
U hebt de volgende vereisten nodig om deze oplossing te volt ooien:
* Een [Azure-abonnement](https://azure.microsoft.com/pricing/free-trial/)

## <a name="scenario-introduction-hello-toll"></a>Scenario Inleiding: "Hallo, gratis!"
Een gratis station is een gemeen schappelijke verschijnsel. U ontvangt deze op veel expressways, bruggen en tunnels over de hele wereld. Elk gratis station heeft meerdere inbellen. Bij hand matige standen hoeft u niet meer te betalen aan een niet-gratis. Op de automatische stand, een sensor op elke stand, scant een RFID-kaart die is aangebracht in de Windshield van uw Voer tuig wanneer u de telefoon op het bewerkings hokje doorgeeft. Het is eenvoudig om het passeren van Voer tuigen te visualiseren als een gebeurtenis stroom waarover interessante bewerkingen kunnen worden uitgevoerd.

![Afbeelding van auto's op het telefoon nummer](media/stream-analytics-build-an-iot-solution-using-stream-analytics/cars-in-toll-booth.jpg)

## <a name="incoming-data"></a>Inkomende gegevens
Deze oplossing werkt met twee gegevens stromen. Sens oren die zijn geïnstalleerd in de start en afsluiten, produceren de eerste stroom. De tweede stroom is een statische lookup-gegevensset met registratie gegevens voor het Voer tuig.

### <a name="entry-data-stream"></a>Gegevens stroom van vermelding
De gegevens stroom van de vermelding bevat informatie over auto's wanneer deze niet-gratis stations binnenkomt. De gebeurtenissen voor het afsluiten van gegevens worden live gestreamd naar een event hub-wachtrij vanuit een web-app die is opgenomen in de voor beeld-app.

| TollID | EntryTime | LicensePlate | Staat | Merk | Model | VehicleType | VehicleWeight | Gratis | Tag |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| 1 |2014-09-10 12:01:00.000 |JNB 7001 |NY |Honda |CRV |1 |0 |7 | |
| 1 |2014-09-10 12:02:00.000 |YXZ 1001 |NY |Toyota |Camry |1 |0 |4 |123456789 |
| 3 |2014-09-10 12:02:00.000 |ABC 1004 |CT |Ford |Taurus |1 |0 |5 |456789123 |
| 2 |2014-09-10 12:03:00.000 |XYZ 1003 |CT |Toyota |Corolla |1 |0 |4 | |
| 1 |2014-09-10 12:03:00.000 |BNJ 1007 |NY |Honda |CRV |1 |0 |5 |789123456 |
| 2 |2014-09-10 12:05:00.000 |COB 1007 |NJ |Toyota |4x4 |1 |0 |6 |321987654 |

Hier volgt een korte beschrijving van de kolommen:

| Kolom | Beschrijving |
| --- | --- |
| TollID |De ID van het telefoon nummer waarmee een telefoon stand wordt aangeduid |
| EntryTime |De datum en tijd van binnenkomst van het Voer tuig in UTC |
| LicensePlate |Het nummer van de licentie plaat van het Voer tuig |
| Staat |Een status in Verenigde Staten |
| Merk |De fabrikant van de auto |
| Model |Het model nummer van de auto |
| VehicleType |1 voor passagiers voertuigen of 2 voor commerciële Voer tuigen |
| WeightType |Gewicht van het Voer tuig in tonnen; 0 voor reizigers voertuigen |
| Gratis |De niet-gratis waarde in USD |
| Tag |De e-code op de auto die de betaling automatiseert; leeg waar de betaling hand matig is gedaan |

### <a name="exit-data-stream"></a>Gegevens stroom afsluiten
De gegevens stroom voor afsluiten bevat informatie over auto's die het bewerkings station verlaten. De gebeurtenissen voor het afsluiten van gegevens worden live gestreamd naar een event hub-wachtrij vanuit een web-app die is opgenomen in de voor beeld-app.

| **TollId** | **ExitTime** | **LicensePlate** |
| --- | --- | --- |
| 1 |2014-09-10T12:03:00.0000000 Z |JNB 7001 |
| 1 |2014-09-10T12:03:00.0000000 Z |YXZ 1001 |
| 3 |2014-09-10T12:04:00.0000000 Z |ABC 1004 |
| 2 |2014-09-10T12:07:00.0000000 Z |XYZ 1003 |
| 1 |2014-09-10T12:08:00.0000000 Z |BNJ 1007 |
| 2 |2014-09-10T12:07:00.0000000 Z |COB 1007 |

Hier volgt een korte beschrijving van de kolommen:

| Kolom | Beschrijving |
| --- | --- |
| TollID |De ID van het telefoon nummer waarmee een telefoon stand wordt aangeduid |
| ExitTime |De datum en tijd van het verlaten van het Voer tuig in UTC |
| LicensePlate |Het nummer van de licentie plaat van het Voer tuig |

### <a name="commercial-vehicle-registration-data"></a>Inschrijvings gegevens voor commercieel Voer tuig
De oplossing maakt gebruik van een statische moment opname van een registratie database van het Voer tuig. Deze gegevens worden opgeslagen als een JSON-bestand in Azure Blob Storage, opgenomen in het voor beeld.

| LicensePlate | Registratie | Verlopen |
| --- | --- | --- |
| SVT 6023 |285429838 |1 |
| XLZ 3463 |362715656 |0 |
| BBV 1005 |876133137 |1 |
| RIV 8632 |992711956 |0 |
| SNY 7188 |592133890 |0 |
| ELH 9896 |678427724 |1 |

Hier volgt een korte beschrijving van de kolommen:

| Kolom | Beschrijving |
| --- | --- |
| LicensePlate |Het nummer van de licentie plaat van het Voer tuig |
| Registratie |De registratie-ID van het Voer tuig |
| Verlopen |De registratie status van het Voer tuig: 0 als de registratie van het Voer tuig actief is, 1 als de registratie is verlopen |

## <a name="set-up-the-environment-for-azure-stream-analytics"></a>De omgeving instellen voor Azure Stream Analytics
Als u deze oplossing wilt volt ooien, hebt u een Microsoft Azure-abonnement nodig. Als u geen Azure-account hebt, kunt u [een gratis proef versie aanvragen](https://azure.microsoft.com/pricing/free-trial/).

Volg de stappen in de sectie ' uw Azure-account opschonen ' aan het einde van dit artikel zodat u het beste gebruik kunt maken van uw Azure-tegoed.

## <a name="deploy-the-sample"></a>Het voorbeeld implementeren
Er zijn verschillende bronnen die eenvoudig kunnen worden geïmplementeerd in een resource groep, samen met een paar klikken. De definitie van de oplossing wordt gehost in de GitHub-opslag plaats op [https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/TollApp) .

### <a name="deploy-the-tollapp-template-in-the-azure-portal"></a>De TollApp-sjabloon implementeren in de Azure Portal
1. Gebruik deze koppeling om [TollApp Azure-sjabloon te implementeren](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-stream-analytics%2Fmaster%2FSamples%2FTollApp%2FVSProjects%2FTollAppDeployment%2Fazuredeploy.json)om de TollApp-omgeving te implementeren in Azure.

2. Meld u aan bij de Azure Portal als u hierom wordt gevraagd.

3. Kies het abonnement waarin de verschillende resources worden gefactureerd.

4. Geef een nieuwe resource groep op met een unieke naam, bijvoorbeeld `MyTollBooth` .

5. Selecteer een Azure-locatie.

6. Geef een **interval** op als aantal seconden. Deze waarde wordt gebruikt in de voor beeld-web-app, voor de frequentie waarmee gegevens worden verzonden naar Event hub.

7. **Ga akkoord** met de voor waarden.

8. Selecteer **vastmaken aan dash board** zodat u de resources later eenvoudig kunt vinden.

9. Selecteer **aanschaffen** om de voorbeeld sjabloon te implementeren.

10. Na enkele ogen blikken wordt er een melding weer gegeven om te bevestigen dat de **implementatie is geslaagd**.

### <a name="review-the-azure-stream-analytics-tollapp-resources"></a>Bekijk de Azure Stream Analytics TollApp-resources

1. Aanmelden bij Azure Portal

2. Zoek de resource groep die u in de vorige sectie hebt genoemd.

3. Controleer of de volgende resources worden weer gegeven in de resource groep:
   - Een Cosmos DB-account
   - Eén Azure Stream Analytics taak
   - Een Azure Storage-account
   - Een Azure Event hub
   - Twee Web Apps

## <a name="examine-the-sample-tollapp-job"></a>De voor beeld-TollApp-taak bekijken
1. Vanaf de resource groep in de vorige sectie selecteert u de Stream Analytics streaming-taak, te beginnen met de naam **tollapp** (de naam bevat wille keurige tekens voor de unieke aanduiding).

2. Op de pagina **overzicht** van de taak ziet u het **query** -vak om de query syntaxis weer te geven.

   ```sql
   SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*) AS Count
   INTO CosmosDB
   FROM EntryStream TIMESTAMP BY EntryTime
   GROUP BY TUMBLINGWINDOW(minute, 3), TollId
   ```

   Als u de bedoeling van de query wilt parafraseren, moet u het aantal Voer tuigen dat in een telefoon stand wordt ingevoerd, tellen. Omdat een hokje op de weg een voortdurende stroom van Voer tuigen heeft, zijn de start gebeurtenissen vergelijkbaar met een stroom die nooit stopt. Als u de stroom wilt kwantificeren, moet u een periode definiëren om over te meten. Hoe gaat u verder met het verfijnen van de vraag, "hoeveel Voer tuigen om de drie minuten een telefoon nummer invoeren?" Dit wordt meestal het aantal tumblingvenstertriggers genoemd.

   Zoals u kunt zien, maakt Azure Stream Analytics gebruik van een query taal die lijkt op SQL en voegt een paar uitbrei dingen toe om de tijd gerelateerde aspecten van de query op te geven.  Lees voor meer informatie over [time management](/stream-analytics-query/time-management-azure-stream-analytics) en [venster](/stream-analytics-query/windowing-azure-stream-analytics) constructies die in de query worden gebruikt.

3. Bekijk de invoer van de voorbeeld taak TollApp. Alleen de EntryStream-invoer wordt gebruikt in de huidige query.
   - **EntryStream** -invoer is een event hub-verbinding waarbij gegevens worden gequeued die elke keer dat een auto een tollbooth op de weg binnenkomt. In een web-app die deel uitmaakt van het voor beeld, worden de gebeurtenissen gemaakt en worden de gegevens in de wachtrij geplaatst in deze event hub. Houd er rekening mee dat deze invoer wordt opgevraagd in de component FROM van de streaming-query.
   - **ExitStream** -invoer is een event hub-verbinding waarbij gegevens worden geplaatst die elke keer dat een auto een tollbooth op de weg verlaat. Deze streaming-invoer wordt gebruikt in latere variaties van de query syntaxis.
   - **Registratie** -invoer is een Azure Blob-opslag verbinding, die verwijst naar een statische registration.jsin het bestand, die wordt gebruikt voor zoek acties als dat nodig is. Deze invoer voor referentie gegevens wordt in latere variaties van de query syntaxis gebruikt.

4. Bekijk de uitvoer van de voorbeeld taak TollApp.
   - **Cosmos DB** -uitvoer is een Cosmos-database container die de uitvoer-Sink-gebeurtenissen ontvangt. Houd er rekening mee dat deze uitvoer wordt gebruikt in component INTO van de streaming-query.

## <a name="start-the-tollapp-streaming-job"></a>De TollApp streaming-taak starten
Volg deze stappen om de streaming-taak te starten:

1. Selecteer op de pagina **overzicht** van de taak **Start**.

2. Selecteer **nu** in het deel venster **taak starten** .

3. Nadat de taak is uitgevoerd, kunt u na enkele ogen blikken de **bewakings** grafiek bekijken op de pagina **overzicht** van de streaming-taak. In de grafiek moeten enkele duizenden invoer gebeurtenissen en tien tallen uitvoer gebeurtenissen worden weer gegeven.

## <a name="review-the-cosmosdb-output-data"></a>De CosmosDB-uitvoer gegevens controleren
1. Zoek de resource groep die de TollApp-resources bevat.

2. Selecteer het Azure Cosmos DB account met het naam patroon **tollapp \<random\> -Cosmos**.

3. Selecteer de **Data Explorer** kop om de pagina Data Explorer te openen.

4. Vouw de **tollAppDatabase**-  >  **tollAppCollection**-  >  **documenten** uit.

5. In de lijst met id's worden verschillende documenten weer gegeven zodra de uitvoer beschikbaar is.

6. Selecteer elke id om het JSON-document te controleren. U ziet elke tollid, windowend-tijd en het aantal auto's vanuit dat venster.

7. Na een extra periode van drie minuten is er nog een set van vier documenten beschikbaar, één document per tollid.


## <a name="report-total-time-for-each-car"></a>Totale tijd rapport voor elke auto
De gemiddelde tijd die nodig is om een auto door te geven aan de niet-gratis manier om de efficiëntie van het proces en de klant ervaring te beoordelen.

Als u de totale tijd wilt vinden, voegt u de EntryTime-stroom samen met de ExitTime-stroom. Voeg de twee invoer stromen toe aan de overeenkomstige overeenkomende TollId-en LicencePlate-kolommen. Voor de operator voor **samen voegen** moet u tijdelijke Leeway opgeven die het acceptabele tijds verschil tussen de gekoppelde gebeurtenissen beschrijft. Gebruik de functie **DATEDIFF** om op te geven dat gebeurtenissen niet meer dan 15 minuten van elkaar moeten zijn. Pas de functie **DATEDIFF** ook toe op afsluiten en invoer tijden om de werkelijke tijd te berekenen dat een auto in het bewerkings station doorbrengt. Let op het verschil van het gebruik van **DATEDIFF** wanneer het wordt gebruikt in een **Select** -instructie en niet op een **samenvoegings** voorwaarde.

```sql
SELECT EntryStream.TollId, EntryStream.EntryTime, ExitStream.ExitTime, EntryStream.LicensePlate, DATEDIFF (minute, EntryStream.EntryTime, ExitStream.ExitTime) AS DurationInMinutes
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN ExitStream TIMESTAMP BY ExitTime
ON (EntryStream.TollId= ExitStream.TollId AND EntryStream.LicensePlate = ExitStream.LicensePlate)
AND DATEDIFF (minute, EntryStream, ExitStream ) BETWEEN 0 AND 15
```

### <a name="to-update-the-tollapp-streaming-job-query-syntax"></a>De query syntaxis van de TollApp streaming-taak bijwerken:

1. Selecteer op de pagina **overzicht** van de taak **stoppen**.

2. Wacht even totdat de melding is gestopt.

3. Selecteer in de kop taak TOPOLOGIE **< > query**

4. Plak de aangepaste streaming SQL-query.

5. Selecteer **Opslaan** om de query op te slaan. Bevestig **Ja** om de wijzigingen op te slaan.

6. Selecteer op de pagina **overzicht** van de taak **Start**.

7. Selecteer **nu** in het deel venster **taak starten** .

### <a name="review-the-total-time-in-the-output"></a>De totale tijd in de uitvoer controleren
Herhaal de stappen in de vorige sectie om de uitvoer gegevens van de CosmosDB van de streaming-taak te bekijken. Bekijk de nieuwste JSON-documenten.

In dit document ziet u bijvoorbeeld een voor beeld van een auto met een bepaalde licentie plaat, de entrytime en de eind tijd, en het veld calculated durationinminutes, dat de duur van de niet-bewerkings periode als twee minuten weergeeft:
```JSON
{
    "tollid": 4,
    "entrytime": "2018-04-05T06:51:39.0491173Z",
    "exittime": "2018-04-05T06:53:09.0491173Z",
    "licenseplate": "JVR 9425",
    "durationinminutes": 2,
    "id": "ff52eb25-d580-7566-2879-1f52bba6601e",
    "_rid": "+8E4AI1DZgBjAAAAAAAAAA==",
    "_self": "dbs/+8E4AA==/colls/+8E4AI1DZgA=/docs/+8E4AI1DZgBjAAAAAAAAAA==/",
    "_etag": "\"ad02f6b8-0000-0000-0000-5ac5c8330000\"",
    "_attachments": "attachments/",
    "_ts": 1522911283
}
```

## <a name="report-vehicles-with-expired-registration"></a>Meld Voer tuigen met verlopen registratie
Azure Stream Analytics kunt statische moment opnamen van referentie gegevens gebruiken om samen te voegen met tijdelijke gegevens stromen. Gebruik de volgende voorbeeld vraag om deze functie te demonstreren. De registratie-invoer is een statisch BLOB-JSON-bestand met de verval datum van licentie codes. Door deel te nemen aan de afgeleide van de licentie, worden de referentie gegevens vergeleken met elk Voer tuig dat door de telefoon wordt door gegeven.

Als er een bedrijfs voertuig is geregistreerd bij het niet-bewerkings bedrijf, kan dit worden door gegeven aan het bewerkings hokje zonder te worden gestopt voor inspectie. Gebruik de opzoek tabel voor registratie om alle commerciële Voer tuigen te identificeren die verlopen registraties hebben.

```sql
SELECT EntryStream.EntryTime, EntryStream.LicensePlate, EntryStream.TollId, Registration.RegistrationId
INTO CosmosDB
FROM EntryStream TIMESTAMP BY EntryTime
JOIN Registration
ON EntryStream.LicensePlate = Registration.LicensePlate
WHERE Registration.Expired = '1'
```

1. Herhaal de stappen in de vorige sectie om de query syntaxis van de TollApp streaming-taak bij te werken.

2. Herhaal de stappen in de vorige sectie om de uitvoer gegevens van de CosmosDB van de streaming-taak te bekijken.

Voorbeelduitvoer:
```json
    {
        "entrytime": "2018-04-05T08:01:28.0252168Z",
        "licenseplate": "GMT 3221",
        "tollid": 1,
        "registrationid": "763220582",
        "id": "47db0535-9716-4eb2-db58-de7886966cbf",
        "_rid": "y+F8AJ9QWACSAQAAAAAAAA==",
        "_self": "dbs/y+F8AA==/colls/y+F8AJ9QWAA=/docs/y+F8AJ9QWACSAQAAAAAAAA==/",
        "_etag": "\"88007d8d-0000-0000-0000-5ac5d7e20000\"",
        "_attachments": "attachments/",
        "_ts": 1522915298
    }
```

## <a name="scale-out-the-job"></a>De taak uitschalen
Azure Stream Analytics is zo ontworpen dat ze op een flexibele manier kunnen worden geschaald, zodat het grote hoeveel heden gegevens kan verwerken. De Azure Stream Analytics-query kan een **partitie by** -component gebruiken om het systeem te laten opschalen dat deze stap uitschaalt. **PartitionId** is een speciale kolom die wordt toegevoegd aan het systeem en die overeenkomt met de partitie-id van de invoer (Event hub).

Als u de query wilt uitschalen naar partities, bewerkt u de query syntaxis in de volgende code:
```sql
SELECT TollId, System.Timestamp AS WindowEnd, COUNT(*)AS Count
INTO CosmosDB
FROM EntryStream
TIMESTAMP BY EntryTime
PARTITION BY PartitionId
GROUP BY TUMBLINGWINDOW(minute,3), TollId, PartitionId
```

De streaming-taak omhoog schalen naar meer streaming-eenheden:

1. De huidige taak **stoppen** .

2. Werk de query syntaxis op de **query pagina< >** en sla de wijzigingen op.

3. Selecteer onder de kop CONFIGURe op de streaming-taak **schalen**.

4. Schuif de schuif regelaar voor **streaming-eenheden** van 1 tot en met 6. Streaming-eenheden definiëren de hoeveelheid reken kracht die de taak kan ontvangen. Selecteer **Opslaan**.

5. **Start** de streaming-taak om de extra schaal te demonstreren. Azure Stream Analytics verdeelt werk over meer compute-resources en zorgt voor een betere door Voer, waarbij de hoeveelheid werk over resources wordt gepartitioneerd met behulp van de kolom die is opgegeven in de component PARTITION BY.

## <a name="monitor-the-job"></a>De taak bewaken
Het **monitor** gebied bevat statistieken over de taak die wordt uitgevoerd. De eerste configuratie is vereist voor het gebruik van het opslag account in dezelfde regio (de naam is niet hetzelfde als de rest van dit document).

![Azure Stream Analytics taak bewaking](media/stream-analytics-build-an-iot-solution-using-stream-analytics/stream-analytics-job-monitoring.png)

U hebt ook toegang tot **activiteiten logboeken** vanuit het gebied met taak dashboard **instellingen** .

## <a name="clean-up-the-tollapp-resources"></a>De TollApp-resources opschonen
1. Stop de Stream Analytics taak in de Azure Portal.

2. Zoek de resource groep die acht resources bevat die gerelateerd zijn aan de sjabloon TollApp.

3. Selecteer **Resourcegroep verwijderen**. Typ de naam van de resource groep om de verwijdering te bevestigen.

## <a name="conclusion"></a>Conclusie
Met deze oplossing hebt u de Azure Stream Analytics-service geïntroduceerd. Hier wordt uitgelegd hoe u invoer en uitvoer configureert voor de Stream Analytics taak. Met het scenario voor gratis gegevens hebben de oplossing gemeen schappelijke typen problemen die zich voordoen in de ruimte van gegevens in beweging en hoe deze kunnen worden opgelost met eenvoudige SQL-achtige query's in Azure Stream Analytics. De oplossing beschreven SQL-extensie constructies voor het werken met tijdelijke gegevens. U hebt geleerd hoe u gegevens stromen samenvoegt, hoe u de gegevens stroom verrijkt met statische referentie gegevens en hoe u een query uitbreidt om een hogere door voer te krijgen.

Hoewel deze oplossing een goede inleiding biedt, is het niet mogelijk om op enigerlei wijze te worden voltooid. U kunt meer query patronen vinden met behulp van de SAQL-taal op [query voorbeelden voor algemene stream Analytics gebruiks patronen](stream-analytics-stream-analytics-query-patterns.md).