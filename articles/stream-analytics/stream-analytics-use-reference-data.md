---
title: Referentie gegevens gebruiken voor Zoek opdrachten in Azure Stream Analytics
description: In dit artikel wordt beschreven hoe u referentie gegevens kunt gebruiken om gegevens in het query ontwerp van een Azure Stream Analytics-taak te zoeken of correleren.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/18/2020
ms.openlocfilehash: e05a4cbbc5fefbfe8a92914ef480f32bdf43ca37
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99560214"
---
# <a name="using-reference-data-for-lookups-in-stream-analytics"></a>Referentie gegevens gebruiken voor Zoek opdrachten in Stream Analytics

Referentie gegevens (ook wel een opzoek tabel genoemd) is een beperkte gegevensset die statisch of langzaam wordt gewijzigd in aard, die wordt gebruikt om een zoek actie uit te voeren of om uw gegevens stromen te verbeteren. In een IoT-scenario kunt u bijvoorbeeld meta gegevens over Sens oren opslaan (die niet vaak veranderen) in referentie gegevens en deze koppelen aan real time IoT-gegevensstreams. Azure Stream Analytics laadt referentie gegevens in het geheugen om stroom verwerking met lage latentie te garanderen. Voor het gebruik van referentie gegevens in uw Azure Stream Analytics-taak gebruikt u meestal een [koppeling voor verwijzings gegevens](/stream-analytics-query/reference-data-join-azure-stream-analytics) in uw query. 

## <a name="example"></a>Voorbeeld  
U kunt een real-time stream van gebeurtenissen die worden gegenereerd wanneer auto's een telefoon stand door geven. Het bewerkings hokje kan de product plaat in realtime vastleggen en samen voegen met een statische gegevensset met registratie gegevens voor het identificeren van de voor de verval periode van de licentie platen.  
  
```SQL  
SELECT I1.EntryTime, I1.LicensePlate, I1.TollId, R.RegistrationId  
FROM Input1 I1 TIMESTAMP BY EntryTime  
JOIN Registration R  
ON I1.LicensePlate = R.LicensePlate  
WHERE R.Expired = '1'
```  

Stream Analytics biedt ondersteuning voor Azure Blob Storage en Azure SQL Database als opslaglaag voor referentie gegevens. U kunt ook referentie gegevens transformeren en/of kopiëren naar Blob-opslag van Azure Data Factory om [een wille keurig aantal Cloud-en on-premises gegevens opslag](../data-factory/copy-activity-overview.md)te gebruiken.

## <a name="azure-blob-storage"></a>Azure Blob Storage

Referentie gegevens worden gemodelleerd als een reeks blobs (gedefinieerd in de invoer configuratie) in oplopende volg orde van de datum/tijd die is opgegeven in de naam van de blob. Het kan **alleen** worden toegevoegd aan het einde van de reeks door gebruik te maken van een datum/tijd die **groter is** dan de waarde die is opgegeven door de laatste Blob in de reeks. Zie [referentie gegevens van een Blob Storage gebruiken voor een Azure stream Analytics taak](data-protection.md)voor meer informatie.

### <a name="configure-blob-reference-data"></a>BLOB-referentie gegevens configureren

Als u uw referentie gegevens wilt configureren, moet u eerst een invoer maken van het type **referentie gegevens**. In de onderstaande tabel wordt elke eigenschap uitgelegd die u moet opgeven tijdens het maken van de referentie gegevens invoer met de beschrijving:

|**Eigenschaps naam**  |**Beschrijving**  |
|---------|---------|
|Invoeralias   | Een beschrijvende naam die wordt gebruikt in de taak query om te verwijzen naar deze invoer.   |
|Opslagaccount   | De naam van het opslag account waarin uw blobs zich bevinden. Als deze zich in hetzelfde abonnement bevindt als uw Stream Analytics-taak, kunt u deze selecteren in de vervolg keuzelijst.   |
|Sleutel van het opslag account   | De geheime sleutel die is gekoppeld aan het opslag account. Dit wordt automatisch ingevuld als het opslag account zich in hetzelfde abonnement als uw Stream Analytics-taak bevindt.   |
|Opslagcontainer   | Containers bieden een logische groepering voor blobs die zijn opgeslagen in de Microsoft Azure Blob service. Wanneer u een BLOB uploadt naar de Blob service, moet u een container voor die BLOB opgeven.   |
|Padpatroon   | Dit is een vereiste eigenschap die wordt gebruikt om uw blobs binnen de opgegeven container te vinden. Binnen het pad kunt u een of meer exemplaren van de volgende twee variabelen opgeven:<BR>{date}, {time}<BR>Voor beeld 1: producten/{date}/{time}/product-list.csv<BR>Voor beeld 2: producten/{date}/product-list.csv<BR>Voor beeld 3: product-list.csv<BR><br> Als de BLOB niet bestaat in het opgegeven pad, wacht de Stream Analytics taak oneindig voordat de BLOB beschikbaar wordt.   |
|Datum notatie [Optioneel]   | Als u {date} hebt gebruikt binnen het door u opgegeven pad-patroon, kunt u de datum notatie selecteren waarin uw blobs zijn ingedeeld in de vervolg keuzelijst met ondersteunde indelingen.<BR>Voor beeld: JJJJ/MM/DD, MM/DD/JJJJ, enzovoort.   |
|Tijd notatie [Optioneel]   | Als u {time} hebt gebruikt binnen het door u opgegeven pad-patroon, kunt u de tijd notatie selecteren waarin uw blobs zijn ingedeeld in de vervolg keuzelijst met ondersteunde indelingen.<BR>Voor beeld: HH, HH/mm of uu-mm.  |
|Serialisatie-indeling voor gebeurtenissen   | Om ervoor te zorgen dat uw query's werken zoals verwacht, Stream Analytics moet weten welke serialisatie-indeling u gebruikt voor binnenkomende gegevens stromen. Voor referentie gegevens zijn de ondersteunde indelingen CSV en JSON.  |
|Encoding   | UTF-8 is op dit moment de enige coderings indeling die wordt ondersteund.  |

### <a name="static-reference-data"></a>Statische referentie gegevens

Als uw referentie gegevens niet naar verwachting worden gewijzigd, wordt ondersteuning voor statische referentie gegevens ingeschakeld door een statisch pad op te geven in de invoer configuratie. Azure Stream Analytics haalt de BLOB op uit het opgegeven pad. {date} en {time} substitutie tokens zijn niet vereist. Omdat referentie gegevens onveranderbaar zijn in Stream Analytics, wordt het overschrijven van een statische referentie gegevens-BLOB niet aanbevolen.

### <a name="generate-reference-data-on-a-schedule"></a>Referentie gegevens op basis van een planning genereren

Als uw referentie gegevens een langzaam gewijzigde gegevensset zijn, wordt de ondersteuning voor het vernieuwen van referentie gegevens ingeschakeld door een pad patroon op te geven in de invoer configuratie met behulp van de {date}-en {time} substitutie tokens. Stream Analytics worden de bijgewerkte definities voor referentie gegevens opgehaald op basis van dit pad patroon. Een patroon van `sample/{date}/{time}/products.csv` met de datum notatie **' jjjj-mm-dd '** en een tijd notatie van **' uu-mm '** zorgt er stream Analytics voor het ophalen van de bijgewerkte BLOB `sample/2015-04-16/17-30/products.csv` bij 5:30 uur op zestiende, 2015 UTC-tijd zone.

Azure Stream Analytics automatisch wordt gescand op vernieuwde referentie gegevens-blobs met een interval van één minuut. Als een blob met tijds tempel 10:30:00 met een kleine vertraging is geüpload (bijvoorbeeld 10:30:30), ziet u een kleine vertraging in Stream Analytics taak die naar deze BLOB verwijst. Om dergelijke scenario's te voor komen, is het raadzaam om de BLOB vóór de effectief beoogde doel tijd (10:30:00 in dit voor beeld) te uploaden om de Stream Analytics taak genoeg tijd te geven om deze in het geheugen te detecteren en te laden en bewerkingen uit te voeren. 

> [!NOTE]
> Momenteel Stream Analytics taken alleen voor het vernieuwen van blobs kijken wanneer de machine tijd overgaat op de tijd die in de naam van de blob is gecodeerd. De taak zoekt bijvoorbeeld `sample/2015-04-16/17-30/products.csv` zo snel mogelijk, maar niet eerder dan 5:30 uur op zestien april, 2015 UTC-tijd zone. Er wordt *nooit* gezocht naar een blob met een gecodeerde tijd die ouder is dan de laatste die is gedetecteerd.
> 
> Zodra de taak de BLOB heeft gevonden, `sample/2015-04-16/17-30/products.csv` worden alle bestanden met een versleutelde datum die ouder is dan 5:30 uur, 16 april, 2015, dus als er een late aankomtde `sample/2015-04-16/17-25/products.csv` BLOB wordt gemaakt in dezelfde container die de taak niet gebruikt.
> 
> `sample/2015-04-16/17-30/products.csv`De taak zal dit bestand ook gebruiken op basis van 10:03 tot 16 april 2015, maar er is geen blob met een eerdere datum aanwezig in de container. deze wordt vanaf 10:03 uur vanaf 16 april, 2015 en de vorige referentie gegevens gebruikt tot dan.
> 
> Een uitzonde ring hierop is wanneer de taak gegevens in een keer opnieuw moet verwerken of wanneer de taak voor het eerst wordt gestart. Op het moment dat de taak wordt gezocht naar de meest recente blob die is geproduceerd voordat de begin tijd van de taak is opgegeven. Dit wordt gedaan om ervoor te zorgen dat er een **niet-lege** referentie gegevensverzameling is wanneer de taak wordt gestart. Als er geen kan worden gevonden, wordt de volgende diagnose weer gegeven in de taak: `Initializing input without a valid reference data blob for UTC time <start time>` .

[Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/) kan worden gebruikt om de taak te organiseren van het maken van de bijgewerkte blobs die vereist zijn door stream Analytics om definities van referentie gegevens bij te werken. Een Data Factory is een cloudgebaseerde gegevensintegratieservice waarmee de verplaatsing en transformatie van gegevens wordt beheerd en geautomatiseerd. Data Factory ondersteunt het [maken van verbinding met een groot aantal Cloud-en on-premises gegevens opslag](../data-factory/copy-activity-overview.md) en het verplaatsen van gegevens eenvoudig volgens een regel matig schema dat u opgeeft. Bekijk dit github-voor [beeld](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/ReferenceDataRefreshForASAJobs)voor meer informatie en stapsgewijze instructies voor het instellen van een Data Factory pijp lijn voor het genereren van referentie gegevens voor stream Analytics die worden vernieuwd op basis van een vooraf gedefinieerd schema.

### <a name="tips-on-refreshing-blob-reference-data"></a>Tips voor het vernieuwen van BLOB-referentie gegevens

1. Overschrijf de blobs van referentie gegevens niet omdat ze onveranderbaar zijn.
2. De aanbevolen manier om referentie gegevens te vernieuwen, is:
    * {Date}/{time} in het pad patroon gebruiken
    * Een nieuwe BLOB toevoegen met hetzelfde container-en pad-patroon dat is gedefinieerd in de taak invoer
    * Gebruik een datum/tijd die **groter is** dan het tijdstip dat is opgegeven door de laatste Blob in de reeks.
3. Referentie gegevens-blobs worden **niet** gerangschikt op de tijd ' laatst gewijzigd ' van de blob, maar alleen door de tijd en de datum die zijn opgegeven in de naam van de blob met de {date}-en {time} vervangingen.
3. Om te voor komen dat een groot aantal blobs wordt vermeld, kunt u overwegen om zeer oude blobs te verwijderen waarvoor de verwerking niet meer wordt uitgevoerd. ASA kan worden uitgevoerd om een kleine hoeveelheid opnieuw te verwerken in sommige scenario's, zoals opnieuw opstarten.

## <a name="azure-sql-database"></a>Azure SQL Database

Azure SQL Database referentie gegevens worden opgehaald door uw Stream Analytics-taak en worden opgeslagen als een moment opname in het geheugen voor verwerking. De moment opname van de referentie gegevens wordt ook opgeslagen in een container in een opslag account dat u opgeeft in de configuratie-instellingen. De container wordt automatisch gemaakt wanneer de taak wordt gestart. Als de taak wordt gestopt of een mislukte status krijgt, worden de automatisch gemaakte containers verwijderd wanneer de taak opnieuw wordt gestart.  

Als uw referentie gegevens een langzaam gewijzigde gegevensset zijn, moet u de moment opname die wordt gebruikt in uw taak regel matig vernieuwen. Met Stream Analytics kunt u een vernieuwings frequentie instellen wanneer u uw Azure SQL Database invoer verbinding configureert. De Stream Analytics runtime zal een query uitvoeren op uw Azure SQL Database met het interval dat is opgegeven met de vernieuwings frequentie. De hoogste vernieuwings frequentie die wordt ondersteund, is eenmaal per minuut. Voor elke vernieuwing slaat Stream Analytics een nieuwe moment opname op in het beschik bare opslag account.

Stream Analytics biedt twee opties voor het uitvoeren van query's op uw Azure SQL Database. Een momentopname query is verplicht en moet worden opgenomen in elke taak. Stream Analytics voert de momentopname query periodiek uit op basis van het Vernieuwings interval en gebruikt het resultaat van de query (de moment opname) als de set met referentie gegevens. De momentopname query moet in de meeste scenario's passen, maar als u prestatie problemen ondervindt met grote gegevens sets en snelle vernieuwings frequenties, kunt u de optie Delta-query gebruiken. Query's die meer dan 60 seconden duren om de referentie gegevensset te retour neren, resulteren in een time-out.

Met de optie Delta query Stream Analytics wordt de momentopname query in eerste instantie uitgevoerd om een basislijn referentie gegevensset op te halen. Daarna voert Stream Analytics de Delta query periodiek uit op basis van het Vernieuwings interval om incrementele wijzigingen op te halen. Deze incrementele wijzigingen worden doorlopend toegepast op de set met referentie gegevens om deze bijgewerkt te houden. Het gebruik van een Delta query kan helpen de opslag kosten en netwerk-I/O-bewerkingen te verlagen.

### <a name="configure-sql-database-reference"></a>SQL Database referentie configureren

Als u uw SQL Database referentie gegevens wilt configureren, moet u eerst **referentie gegevens** invoer maken. In de onderstaande tabel wordt elke eigenschap uitgelegd die u moet opgeven tijdens het maken van de invoer van referentie gegevens met de beschrijving. Zie [referentie gegevens van een SQL database gebruiken voor een Azure stream Analytics taak](sql-reference-data.md)voor meer informatie.

U kunt [Azure SQL Managed instance](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md) als referentie gegevens invoer gebruiken. U moet een [openbaar eind punt configureren in het SQL Managed instance](../azure-sql/managed-instance/public-endpoint-configure.md) en vervolgens hand matig de volgende instellingen configureren in azure stream Analytics. Een virtuele Azure-machine met SQL Server met een gekoppelde data base wordt ook ondersteund door de onderstaande instellingen hand matig te configureren.

|**Eigenschaps naam**|**Beschrijving**  |
|---------|---------|
|Invoeralias|Een beschrijvende naam die wordt gebruikt in de taak query om te verwijzen naar deze invoer.|
|Abonnement|Kies uw abonnement|
|Database|De Azure SQL Database die uw referentie gegevens bevat. Voor het beheerde exemplaar van SQL moet de poort 3342 worden opgegeven. Bijvoorbeeld *sampleserver. public. data base. Windows. net, 3342*|
|Gebruikersnaam|De gebruikers naam die aan uw Azure SQL Database is gekoppeld.|
|Wachtwoord|Het wacht woord dat is gekoppeld aan uw Azure SQL Database.|
|Periodiek vernieuwen|Met deze optie kunt u een vernieuwings frequentie kiezen. Als u aan kiest, kunt u de vernieuwings frequentie opgeven in DD: uu: MM.|
|Momentopname query|Dit is de standaard optie voor query's waarmee de referentie gegevens uit uw SQL Database worden opgehaald.|
|Delta query|Kies voor geavanceerde scenario's met grote gegevens sets en een korte vernieuwings frequentie om een Delta query toe te voegen.|

## <a name="size-limitation"></a>Grootte beperken

Het is raadzaam om referentie gegevens sets te gebruiken die kleiner zijn dan 300 MB voor de beste prestaties. Referentie gegevens sets 5 GB of lager worden ondersteund in taken met 6 SUs of meer. Het gebruik van een zeer grote referentie gegevens kan van invloed zijn op de end-to-end latentie van uw taak. Naarmate de complexiteit van de query toeneemt met stateful verwerking, zoals statistische gegevens over het venster, tijdelijke samen voegingen en tijdelijke analytische functies, wordt ervan uitgegaan dat de Maxi maal ondersteunde grootte van de referentie data afneemt. Als Azure Stream Analytics de referentie gegevens niet kan laden en complexe bewerkingen kunt uitvoeren, kan de taak geen geheugen meer gebruiken. In dergelijke gevallen is het gebruik van metrische gegevens van% en een bereik van 100%.    

|**Aantal streaming-eenheden**  |**Aanbevolen grootte**  |
|---------|---------|
|1   |50 MB of lager   |
|3   |150 MB of lager   |
|6 en hoger   |5 GB of lager.    |

Ondersteuning voor compressie is niet beschikbaar voor referentie gegevens.

## <a name="joining-multiple-reference-datasets-in-a-job"></a>Meerdere referentie gegevens sets in een taak samen voegen
U kunt slechts één stroom invoer samen voegen met één referentie gegevens invoer in één stap van uw query. U kunt echter meerdere referentie gegevens sets koppelen door uw query in meerdere stappen te splitsen. Hieronder kunt u een voorbeeld bekijken.

```SQL  
With Step1 as (
    --JOIN input stream with reference data to get 'Desc'
    SELECT streamInput.*, refData1.Desc as Desc
    FROM    streamInput
    JOIN    refData1 ON refData1.key = streamInput.key 
)
--Now Join Step1 with second reference data
SELECT *
INTO    output 
FROM    Step1
JOIN    refData2 ON refData2.Desc = Step1.Desc 
``` 

## <a name="iot-edge-jobs"></a>IoT Edge taken

Alleen lokale referentie gegevens worden ondersteund voor Stream Analytics Edge-taken. Wanneer een taak wordt geïmplementeerd op IoT Edge apparaat, worden er referentie gegevens uit het door de gebruiker gedefinieerde bestandspad geladen. Een bestand met referentie gegevens gereed is voor het apparaat. Plaats voor een Windows-container het bestand met referentie gegevens op de lokale schijf en deel het lokale station met de docker-container. Voor een Linux-container maakt u een docker-volume en vult u het gegevens bestand in op het volume.

Referentie gegevens op IoT Edge update worden geactiveerd door een implementatie. Nadat de Stream Analytics-module is geactiveerd, worden de bijgewerkte gegevens gekozen zonder dat de actieve taak wordt gestopt.

Er zijn twee manieren om de referentie gegevens bij te werken:

* Het pad naar de referentie gegevens in uw Stream Analytics-taak bijwerken vanuit Azure Portal.

* Werk de IoT Edge-implementatie bij.

## <a name="next-steps"></a>Volgende stappen
> [!div class="nextstepaction"]
> [Snelstart: Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.get.started]: ./stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: /stream-analytics-query/stream-analytics-query-language-reference
[stream.analytics.rest.api.reference]: /rest/api/streamanalytics/
