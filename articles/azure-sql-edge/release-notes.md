---
title: Release opmerkingen voor Azure SQL Edge
description: Release opmerkingen bevatten informatie over wat er nieuw is of wat er is gewijzigd in de installatie kopieën van Azure SQL Edge.
keywords: release opmerkingen SQL-rand
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: e078fb91b3279b6f4321cd51dfb094f82bbe5f14
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696376"
---
# <a name="azure-sql-edge-release-notes"></a>Release opmerkingen bij Azure SQL Edge 

In dit artikel wordt beschreven wat er nieuw is en wat er is gewijzigd bij elke nieuwe build van Azure SQL Edge.

## <a name="azure-sql-edge-102"></a>Azure SQL Edge-1.0.2

SQL engine build 15.0.2000.1554

### <a name="fixes"></a>Oplossingen

- T-SQL-streaming  
   - Herstel in eigendom en machtigingen voor streaming-objecten
   - Logboek registratie van verbeteringen met logboek rotatie en logboek voorvoegsel
   - Azure Stream Analytics: verbeteringen in de logboek registratie, fout code/fout berichten in adapters verbeteren 

- ONNX
    - Fout oplossingen voor parallel query scenario en model opruim fouten
    - Upgrade van ONNX-runtime naar 1.5.1

## <a name="azure-sql-edge-101"></a>Azure SQL Edge-1.0.1

SQL engine build 15.0.2000.1553

### <a name="whats-new"></a>Wat is nieuw?

- Toestaan dat Date_Bucket expressies worden gedefinieerd in berekende kolommen.

### <a name="fixes"></a>Oplossingen

- Oplossing voor Bewaar beleid voor het verwijderen van een tabel waarvoor een Bewaar beleid is ingeschakeld met een oneindige time-out
- Ondersteuning voor DacFx-implementatie voor streaming-functies en retentie-beleids functies 
- DacFx-implementatie oplossing om implementatie vanuit een geneste map in een SAS-URL mogelijk te maken 
- Voors PELLEn voor het ondersteunen van lange kolom namen in fout berichten

## <a name="azure-sql-edge-100-rtm"></a>Azure SQL Edge 1.0.0 (RTM)

SQL engine build 15.0.2000.1552

### <a name="whats-new"></a>Wat is nieuw?
- Container installatie kopieën op basis van Ubuntu 18,04 
- Ondersteuning voor `IGNORE NULL` en- `RESPECT NULL` syntaxis met `LAST_VALUE()` en- `FIRST_VALUE()` functies 
- Verbeteringen van de betrouw baarheid van voor SPELing met ONNX
- Ondersteuning voor opschonen op basis van het beleid voor gegevens behoud:
   - Ondersteuning voor ring buffers voor een opruimings taak voor retentie voor het oplossen van problemen
- Ondersteuning voor nieuwe functies: 
   - Snel herstel
   - Query's automatisch afstemmen
   - Scenario's voor parallelle uitvoering
- Energiebesparende verbeteringen voor de modus voor laag energie verbruik
- Ondersteuning voor streaming New-functie: 
   - [Momentopname Vensters](/stream-analytics-query/snapshot-window-azure-stream-analytics): met een nieuw venster kunt u op gebeurtenissen groeperen die op hetzelfde moment aankomen.
   - [TopOne](/stream-analytics-query/topone-azure-stream-analytics) en [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics) kunnen worden ingeschakeld als analytische functies. U kunt de records retour neren die zijn gesorteerd op basis van de kolom van uw keuze. Ze hoeven geen deel uit te maken van een venster. 
   - Verbeteringen in [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics). 

### <a name="fixes"></a>Oplossingen
- Aanvullende fout berichten en Details voor het oplossen van problemen met T-SQL-streaming 
- Verbeteringen voor het behouden van de levens duur van de accu in de modus niet actief 
- Oplossingen voor T-SQL Streaming Engine: 
   - Taken voor gestopt streamen opruimen 
   - Oplossingen voor lokalisatie 
   - Verbeterde Unicode-verwerking 
   - Verbeterde fout opsporing voor het streamen van SQL Edge T-SQL, zodat gebruikers fouten in taken kunnen opvragen van get_streaming_job
- Opschonen op basis van het beleid voor gegevens behoud: 
    - Oplossingen voor scenario's voor het maken en opschonen van Bewaar beleid
- Oplossingen voor achtergrond timer taken om energie besparing te verbeteren voor de modus voor laag energie verbruik

### <a name="known-issues"></a>Bekende problemen 
- De Date_Bucket T-SQL-functie kan niet worden gebruikt in een berekende kolom.


## <a name="ctp-23"></a>CTP 2,3
SQL engine build 15.0.2000.1549
### <a name="whats-new"></a>Wat is nieuw?
- Ondersteuning voor aangepaste oorsprongen in de functie Date_Bucket () 
- Ondersteuning voor BACPAC-bestanden als onderdeel van SQL-implementatie
- Ondersteuning voor opschonen op basis van het beleid voor gegevens behoud:      
   - DDL-ondersteuning voor het inschakelen van het Bewaar beleid 
   - Opschonen voor opgeslagen procedures en de taak achtergrond opschonen
   - Uitgebreide gebeurtenissen voor het controleren van opschoon taken

### <a name="fixes"></a>Oplossingen
- Aanvullende fout berichten en Details voor het oplossen van problemen met T-SQL-streaming 
- Verbeteringen voor het behouden van de levens duur van de accu in de modus niet actief 
- T-SQL Streaming-Engine: 
   - Oplossen voor vastgelopen water merk in het substreamde verspringen-venster 
   - Oplossing voor verwerking van Framework-uitzonde ringen om ervoor te zorgen dat deze wordt verzameld als een door de gebruiker te behandel bare fout


## <a name="ctp-22"></a>CTP 2,2
SQL engine build 15.0.2000.1546
### <a name="whats-new"></a>Wat is nieuw?
- Ondersteuning voor niet-hoofd containers 
- Ondersteuning voor het verzamelen van gebruiks-en diagnostische gegevens 
- Updates voor T-SQL-streaming:
   - Ondersteuning voor Unicode-tekens voor Stream-object namen

### <a name="fixes"></a>Oplossingen
- Updates voor T-SQL-streaming:
   - Verbeteringen bij het opschonen van processen
   - Verbeteringen in Logboeken en diagnoses
- Verbetering van de prestaties voor gegevens opname

## <a name="ctp-21"></a>CTP 2,1 
SQL engine build 15.0.2000.1545
### <a name="fixes"></a>Oplossingen
- Het voor spel-met-ONNX modellen toestaan een CPUID-probleem in ARM af te handelen 
- Verbeterde verwerking van het fout traject wanneer T-SQL streaming wordt gestart
- Gecorrigeerde waarde van de vertraging van het water merk in de metrische gegevens van de taak wanneer er geen data zijn.
- Oplossing voor een probleem met de uitvoer adapter wanneer de adapter een variabel schema tussen batches heeft  

## <a name="ctp-20"></a>CTP 2,0 
SQL engine build 15.0.2000.1401
### <a name="whats-new"></a>Wat is nieuw?
-   Product naam is bijgewerkt naar *Azure SQL Edge*
-  Date_Bucket functie:
    - Ondersteuning voor datum-, tijd-en DateTime-typen
- Voors PELLEn met ONNX:
    - ONNX-vereiste voor de RUNTIME-para meter  
- Ondersteuning voor T-SQL-streaming (beperkte preview) 
 
### <a name="known-issues"></a>Bekende problemen

- Probleem: mogelijke fouten met het Toep assen van DACPAC bij het opstarten vanwege een timing probleem.
- Tijdelijke oplossing: Start SQL Server opnieuw. Anders probeert de container de DACPAC opnieuw uit te voeren.

### <a name="request-support"></a>Ondersteuning aanvragen
U kunt ondersteuning aanvragen op de [ondersteunings pagina](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest). Selecteer de volgende velden: 
- **Type probleem**: *Technisch* 
- **Service**: *IOT Edge*
- **Probleem type**: *mijn probleem is gekoppeld aan een IOT Edge module*
- **Subtype van probleem**: *Azure SQL Edge*

:::image type="content" source="media/get-support/support-ticket.png" alt-text="Scherm afbeelding met een voor beeld van een ondersteunings ticket.":::

## <a name="ctp-15"></a>CTP 1,5
SQL engine build 15.0.2000.1331
### <a name="whats-new"></a>Wat is nieuw?
- Date_Bucket functie:
    - Ondersteuning voor het type Date Time offset
- Voors PELLEn met ONNX-modellen:
  - Ondersteuning voor NVARCHAR
 
## <a name="ctp-14"></a>CTP 1,4
SQL engine build 15.0.2000.1247
### <a name="whats-new"></a>Wat is nieuw?
-   Voors PELLEn met ONNX-modellen:
    - VARCHAR-ondersteuning
    - Migratie naar ONNX runtime versie 1,0 

- De volgende functies zijn ingeschakeld:
  - CDC-ondersteuning
  - Geschiedenis tabel met compressie
  - Een hogere factor voor logboek bestand vooruit lezen
  - Batch modus ES filter pushdown
  - Vooruit lezen optimalisaties
 
## <a name="ctp-13"></a>CTP 1,3
SQL engine build 15.0.2000.1147
### <a name="whats-new"></a>Wat is nieuw?
- Implementatie van Azure IoT portal: 
    - Ondersteuning voor het implementeren van AMD64-en ARM-installatie kopieën
    - Ondersteuning voor het maken van streaming-taken
    - DACPAC-implementatie
- Voors PELLEn met ONNX-modellen:
    - Ondersteuning voor numeriek type
- De volgende functies zijn ingeschakeld:
    - Pushdown aggregatie naar column Store-scan
    - Gelukkig-ronde-scans
- Footprint en verminderen van het geheugen gebruik
