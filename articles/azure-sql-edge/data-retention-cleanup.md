---
title: Historische gegevens beheren met Bewaar beleid-Azure SQL Edge
description: Meer informatie over het beheren van historische gegevens met Bewaar beleid in Azure SQL Edge
keywords: SQL-rand, gegevens retentie
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/04/2020
ms.openlocfilehash: f547f0b3219889b54364c4805c2dd5b183a9861a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392364"
---
# <a name="manage-historical-data-with-retention-policy"></a>Historische gegevens beheren met Bewaar beleid

Gegevens retentie kan op de data base en alle onderliggende tabellen afzonderlijk worden ingeschakeld, zodat gebruikers flexibele ouderdoms beleid kunnen maken voor hun tabellen en data bases. Het Toep assen van gegevens retentie is eenvoudig: er is slechts één para meter vereist voor het maken van tabellen of als onderdeel van een ALTER TABLE-bewerking. 

Nadat het Bewaar beleid voor gegevens gedefinieerde is voor een Data Base en de onderliggende tabel, wordt een timer taak voor achtergrond tijd uitgevoerd om eventuele verouderde records uit de tabel die is ingeschakeld voor het bewaren van gegevens te verwijderen. De identificatie van overeenkomende rijen en het verwijderen van de tabel wordt op transparante wijze uitgevoerd in de achtergrond taak die door het systeem is gepland en uitgevoerd. De leeftijds voorwaarde voor de tabel rijen wordt gecontroleerd op basis van de kolom die wordt gebruikt als de `filter_column` in de tabel definitie. Als de Bewaar periode bijvoorbeeld is ingesteld op één week, kunnen tabel rijen die in aanmerking komen voor opschonen, voldoen aan een van de volgende voor waarden: 

- Als in de kolom filter de date time offset-gegevens type wordt gebruikt, is de voor waarde `filter_column < DATEADD(WEEK, -1, SYSUTCDATETIME())`
- Anders is de voor waarde `filter_column < DATEADD(WEEK, -1, SYSDATETIME())`

## <a name="data-retention-cleanup-phases"></a>Opschonings fasen voor het bewaren van gegevens

De opschoon bewerking voor het bewaren van gegevens bestaat uit twee fasen. 
- Detectie fase: in deze fase worden alle tabellen in de gebruikers databases door de opschoon bewerking geïdentificeerd om een lijst voor opschonen te maken. Detectie wordt één keer per dag uitgevoerd.
- Opschonings fase: in deze fase wordt de opschoning uitgevoerd voor alle tabellen met een eindige gegevens retentie, geïdentificeerd in de detectie fase. Als de opschoon bewerking niet kan worden uitgevoerd op een tabel, wordt die tabel tijdens de huidige uitvoering overgeslagen en wordt er opnieuw geprobeerd in de volgende iteratie. De volgende principes worden gebruikt tijdens het opschonen
    - Als een verouderde rij is vergrendeld door een andere trans actie, wordt die rij overgeslagen. 
    - Opschonen wordt uitgevoerd met een standaard time-out voor vijf seconden vergren delen. Als de vergren delingen niet kunnen worden verkregen in de tabellen binnen het time-outvenster, wordt de tabel tijdens de huidige uitvoering overgeslagen en wordt opnieuw geprobeerd tijdens de volgende iteratie.
    - Als er een fout optreedt tijdens het opschonen van een tabel, wordt die tabel overgeslagen en wordt deze in de volgende iteratie opgenomen.

## <a name="manual-cleanup"></a>Hand matig opschonen

Afhankelijk van de instellingen voor het bewaren van gegevens in een tabel en de aard van de werk belasting op de data base, is het mogelijk dat de automatische opschonings thread niet alle verouderde rijen verwijdert tijdens de uitvoering. Om dit te helpen en gebruikers toe te staan om de verouderde rijen hand matig te verwijderen, `sys.sp_cleanup_data_retention` is de opgeslagen procedure geïntroduceerd in de Azure SQL-rand. 

Deze opgeslagen procedure heeft drie para meters. 
    - Schema naam: naam van het schema dat eigenaar is van de tabel. Dit is een vereiste para meter. 
    - Tabel naam: naam van de tabel waarvoor hand matig opschonen wordt uitgevoerd. Dit is een vereiste para meter. 
    - RowCount (uitvoer)-uitvoer variabele. Retourneert het aantal rijen dat is opgeschoond door het hand matig opschonen van SP. Dit is een optionele para meter. 

In het volgende voor beeld ziet u de uitvoering van de hand matige opschoon bewerking voor de tabel `dbo.data_retention_table` .

```sql
declare @rowcnt bigint 
EXEC sys.sp_cleanup_data_retention 'dbo', 'data_retention_table', @rowcnt output 
select @rowcnt 
```

## <a name="how-obsolete-rows-are-deleted"></a>Hoe verouderde rijen worden verwijderd

Het opschonings proces is afhankelijk van de indeling van de index van de tabel. Er wordt een achtergrond taak gemaakt om verouderde gegevens opschoning uit te voeren voor alle tabellen met een eindige Bewaar periode. Bij het opruimen van de logica voor de index van de rowstore (B-Tree of heap) wordt verouderde rij in kleinere segmenten (Maxi maal 10.000) voor het minimaliseren van de belasting van het database logboek en het IO-subsysteem verwijderd. Hoewel bij het opruimen logica de vereiste B-structuur index wordt gebruikt, kan de volg orde van verwijderde items voor de rijen die ouder zijn dan de Bewaar periode niet stevig worden gegarandeerd. Zorg er daarom voor dat u niet afhankelijk bent van de opschoon volgorde in uw toepassingen.

Met de opschoon taak voor de geclusterde column Store worden hele Rijg roepen tegelijk verwijderd (meestal bevat 1.000.000 van de rijen). Dit is zeer efficiënt, vooral wanneer gegevens worden gegenereerd en in een hoog tempo verlopen.

![Opschonen van gegevens retentie](./media/data-retention-cleanup/data-retention-cleanup.png)

Met uitstekende gegevens compressie en een efficiënte Bewaar periode voor retentie is geclusterde column store-index een perfecte keuze voor scenario's wanneer uw werk belasting snel een hoge hoeveelheid gegevens genereert.

> [!Note]
> In het geval van B-structuur indexen en-heaps voert gegevens retentie een Verwijder query uit voor de onderliggende tabellen. Dit kan conflicteren met triggers voor verwijderen in de tabellen. Het is raadzaam om Verwijder triggers uit de tabellen te verwijderen of om gegevens retentie niet in te scha kelen voor tabellen die een DML-trigger hebben verwijderd.

## <a name="monitoring-data-retention-cleanup"></a>Opruimen van gegevens retentie bewaken

Het opruimen van het beleid voor het bewaren van gegevens kan worden bewaakt met behulp van Extended Events (XEvents) in de Azure SQL-rand. Zie [XEvents Overview](/sql/relational-databases/extended-events/extended-events)(Engelstalig) voor meer informatie over Extended Events. 

De volgende zes uitgebreide gebeurtenissen helpen bij het volgen van de status van de opschoon bewerkingen. 

| Naam | Beschrijving |
|------| ------------|
| data_retention_task_started  | Treedt op wanneer de achtergrond taak voor het opruimen van tabellen met het Bewaar beleid wordt gestart. |
| data_retention_task_completed  | Deze gebeurtenis treedt op wanneer de achtergrond taak voor het opruimen van tabellen met het Bewaar beleid eindigt. |
| data_retention_task_exception  | Treedt op wanneer de achtergrond taak voor het opruimen van tabellen met het Bewaar beleid niet kan worden uitgevoerd buiten het opruim proces voor retentie dat specifiek is voor de tabel. |
| data_retention_cleanup_started  | Treedt op wanneer het opschonen van de tabel met het Bewaar beleid voor gegevens wordt gestart. |
| data_retention_cleanup_exception  | Gebeurt met het opruimen van de tabel met het Bewaar beleid mislukt. |
| data_retention_cleanup_completed  | Treedt op wanneer het opschonen van de tabel met het Bewaar beleid voor gegevens eindigt. |  

Daarnaast is er een nieuw ring buffer type `RING_BUFFER_DATA_RETENTION_CLEANUP` met de naam toegevoegd aan sys.dm_os_ring_buffers dynamische beheer weergave. Deze weer gave kan worden gebruikt om de opschoon bewerkingen voor het bewaren van gegevens te bewaken. 


## <a name="next-steps"></a>Volgende stappen
- [Beleid voor gegevens retentie](data-retention-overview.md)
- [Het Bewaar beleid voor gegevens in-en uitschakelen](data-retention-enable-disable.md)