---
title: Release opmerkingen voor Azure SQL Edge
description: Release opmerkingen bevatten informatie over wat er nieuw is of wat er is gewijzigd in de installatie kopieën van Azure SQL Edge
keywords: release opmerkingen SQL-rand
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: bb0c176ee8c145e1268827d79b453628e6f1bc28
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95808631"
---
# <a name="azure-sql-edge-release-notes"></a>Release opmerkingen bij Azure SQL Edge 

In dit artikel wordt beschreven wat er nieuw is en wat er is gewijzigd bij elke nieuwe build van Azure SQL Edge.

## <a name="azure-sql-edge---101"></a>Azure SQL Edge-1.0.1

### <a name="sql-engine-build-number---15020001553"></a>Buildnummer van SQL-engine-15.0.2000.1553

### <a name="whats-new"></a>Wat is nieuw?

- In berekende kolommen gedefinieerde Datebucket-expressie toestaan

### <a name="fixes"></a>Oplossingen

- Oplossing voor Bewaar beleid voor het verwijderen van een tabel met Bewaar beleid is ingeschakeld met een oneindige time-out
- Ondersteuning voor de functies streaming en bewaar beleid in DacFx-implementatie 
- DacFx-implementatie oplossing om implementatie vanuit geneste map in SAS-URL mogelijk te maken 
- Voors PELLEn voor het ondersteunen van lange kolom namen in fout berichten

## <a name="azure-sql-edge---100-rtm"></a>Azure SQL Edge-1.0.0 (RTM)

### <a name="sql-engine-build-number---15020001552"></a>Buildnummer van SQL-engine-15.0.2000.1552

### <a name="whats-new"></a>Wat is nieuw?
1. Ubuntu-container installatie kopieën op basis van 18,04. 
2. Ondersteuning voor `IGNORE NULL` en- `RESPECT NULL` syntaxis met `LAST_VALUE()` en- `FIRST_VALUE()` functies. 
3. Verbetering van de betrouw baarheid voor voor SPELing met ONNX.
4. Ondersteuning voor het opruimen van gegevens Bewaar beleid.
   - Ondersteuning voor ring buffer voor het opruimen van Bewaar taken voor het oplossen van problemen.
5. Ondersteuning voor nieuwe functies 
   - Snel herstel
   - Automatisch afstemmen van query's
   - Parallelle uitvoerings scenario's inschakelen
6. Energiebesparende verbeteringen voor de modus voor laag energie verbruik
7. Ondersteuning voor streaming van nieuwe functies 
   - [Snap shot-Vensters](/stream-analytics-query/snapshot-window-azure-stream-analytics) : het nieuwe venster type waarmee kan worden gegroepeerd op gebeurtenissen die op dezelfde tijd aankomen. 
   - Schakel [TopOne](/stream-analytics-query/topone-azure-stream-analytics) en [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) in als analytische functie. Dit kan ertoe leiden dat records worden geretourneerd die zijn gesorteerd op basis van de kolom van uw keuze, zonder dat het nodig is om een deel van een venster te maken. 
   - Verbeteringen in [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics). 

### <a name="fixes"></a>Oplossingen
1. Aanvullende fout berichten en Details voor het oplossen van problemen met TSQL-streaming. 
2. Verbeteringen voor het behouden van de levens duur van de accu in de modus niet-actief 
3. Oplossingen voor TSQL Streaming-Engine: 
   - Taak voor gestopt streamen opschonen 
   - Oplossingen voor verbeteringen voor lokalisatie en Unicode-verwerking
   - Verbetering van de fout opsporing voor Edge TSQL-streaming, waarmee gebruikers fouten in taken kunnen opvragen van get_streaming_job.
4. Opschonen op basis van een beleid voor gegevens retentie
   - Oplossingen voor het maken en opschonen van Bewaar beleid.
5. Oplossingen voor achtergrond timer taken om energie besparing te verbeteren voor de modus voor laag energie verbruik.

### <a name="known-issues"></a>Bekende problemen 
1. Date_Bucket T-SQL-functie kan niet worden gebruikt in een berekende kolom.


## <a name="ctp-23"></a>CTP 2,3
### <a name="sql-engine-build-number---15020001549"></a>Buildnummer van SQL-engine-15.0.2000.1549
### <a name="whats-new"></a>Wat is nieuw?
1. Ondersteuning voor aangepaste oorsprongen in de functie Date_Bucket (). 
2. Ondersteuning voor BacPac-bestanden als onderdeel van SQL-implementatie.
3. Ondersteuning voor het opruimen van gegevens Bewaar beleid.      
   - DDL-ondersteuning voor het inschakelen van Bewaar beleid 
   - Opgeslagen procedures en opschoon taak voor achtergrond opschonen
   - Uitgebreide gebeurtenissen voor het controleren van opschoon taken

### <a name="fixes"></a>Oplossingen
1. Aanvullende fout berichten en Details voor het oplossen van problemen met TSQL-streaming. 
2. Verbeteringen voor het behouden van de levens duur van de accu in de modus niet-actief 
3. Oplossingen voor TSQL Streaming-Engine: 
   - Probleem met vastgelopen water merk oplossen met verspringen-venster met substream 
   - Opgeloste verwerking van Framework-uitzonde ringen verhelpen om ervoor te zorgen dat deze wordt verzameld als een gebruikers actie fout


## <a name="ctp-22"></a>CTP 2,2
### <a name="sql-engine-build-number---15020001546"></a>Buildnummer van SQL-engine-15.0.2000.1546
### <a name="whats-new"></a>Wat is nieuw?
1. Ondersteuning voor niet-hoofd containers 
2. Ondersteuning voor het verzamelen van gebruiks-en diagnostische gegevens 
3. Updates voor T-SQL-streaming
   - Ondersteuning voor Unicode-tekens voor Stream-object namen

### <a name="fixes"></a>Oplossingen
1. Updates voor T-SQL-streaming
   - Verbeteringen bij het opschonen van processen
   - Verbeteringen in Logboeken en diagnoses
2. Verbetering van de prestaties voor gegevens opname

## <a name="ctp-21"></a>CTP 2,1 
### <a name="sql-engine-build-number---15020001545"></a>Buildnummer van SQL-engine-15.0.2000.1545
### <a name="fixes"></a>Oplossingen
1. Verhelp de voor SPELing met ONNX-modellen om het CPUID-probleem in ARM af te handelen 
2. Oplossing voor het verbeteren van de verwerking van het fout traject bij het opstarten van TSQL-streaming 
3. Corrigeer de onjuiste waarde van het watermerk vertraging in de metrische gegevens van de taak wanneer er geen data zijn. 
4. Los het probleem met de uitvoer adapter op wanneer de adapter een variabel schema tussen batches heeft.  

## <a name="ctp-20"></a>CTP 2,0 
### <a name="sql-engine-build-number---15020001401"></a>Buildnummer van SQL-engine-15.0.2000.1401
### <a name="whats-new"></a>Wat is nieuw?
1.  De product naam is bijgewerkt naar ' Azure SQL Edge '
1.  Functie Date_bucket

    i.  Ondersteuning voor datum, tijd, type DateTime
3.  Voors PELLEn met ONNX
    
    i.  RUNTIME parameter vereist voor ONNX 
    
4.  Ondersteuning voor TSQL-streaming (beperkte preview) 
 
### <a name="known-issues"></a>Bekende problemen

1. <b>Probleem:</b> Mogelijke fouten met het Toep assen van dacpac bij het opstarten vanwege een timing probleem.

    <b>Tijdelijke oplossing:</b> Als SQL Server of container opnieuw wordt gestart, wordt de dacpac opnieuw geprobeerd en wordt het probleem opgelost
### <a name="request-support"></a>Ondersteuning aanvragen
1. U kunt ondersteuning aanvragen op de [pagina ondersteuning](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

4. Zorg ervoor dat de volgende velden zijn geselecteerd: 
    * Probleem type-technisch 
    * Service-IoT Edge
    * Probleem type: mijn probleem is gekoppeld aan een IoT Edge module
    * Subtype van probleem-Azure SQL Edge

   ![Voor beeld van een ondersteunings ticket](media/get-support/support-ticket.png)

## <a name="ctp-15"></a>CTP 1,5
### <a name="sql-engine-build-number---15020001331"></a>Buildnummer van SQL-engine-15.0.2000.1331
### <a name="whats-new"></a>Wat is nieuw?
1. Functie Date_bucket
    
    i. Ondersteuning voor date time offset type
2. Voors PELLEn met ONNX-modellen

    i. ondersteuning voor nvarchar
 
## <a name="ctp-14"></a>CTP 1,4
### <a name="sql-engine-build-number---15020001247"></a>Buildnummer van SQL-engine-15.0.2000.1247
### <a name="whats-new"></a>Wat is nieuw?
1.  Voors PELLEn met ONNX-modellen
 
    i.  Varchar-ondersteuning
    
    ii. Migratie naar ONNX runtime versie 1,0 
2.  Ondersteuning voor functies: de volgende functies zijn ingeschakeld:

    i.   CDC-ondersteuning

    ii.  Geschiedenis tabel met compressie

    iii. Hogere schaal factor voor het vooraf lezen van Logboeken

    4.  Batch modus ES filter pushdown

    v.   Vooruit-optimaliseringen lezen
 
## <a name="ctp-13"></a>CTP 1,3
### <a name="sql-engine-build-number---15020001147"></a>Buildnummer van SQL-engine-15.0.2000.1147
### <a name="whats-new"></a>Wat is nieuw?
1. Azure IOT-Portal implementeren 

    i.   Ondersteuning voor het implementeren van AMD64-en ARM-installatie kopieën

    ii.  Ondersteuning voor het maken van streaming-taken

    iii. Dacpac-implementatie
2. Voors PELLEn met ONNX-modellen

    i. Ondersteuning voor numeriek type
3. Ondersteuning voor functies: de volgende functies zijn ingeschakeld:

    i.  Pushdown aggregatie naar column Store-scan

    ii. Gelukkig-ronde-scans
4. Vermindering van footprint en geheugen gebruik
