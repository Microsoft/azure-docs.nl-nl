---
title: Releaseopmerkingen voor Data Management Gateway
description: Opmerkingen bij de release van Data Management Gateway Tory
services: data-factory
author: nabhishek
manager: anandsub
ms.assetid: 14762e82-76d9-41c4-ba9f-14a54da29c36
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 0538777cadf7935a6684932a17fadc10849518d8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001623"
---
# <a name="release-notes-for-data-management-gateway"></a>Releaseopmerkingen voor Data Management Gateway
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. Als u de huidige versie van de Data Factory-service gebruikt, raadpleegt u [zelf-hostende Integration runtime in v2](../create-self-hosted-integration-runtime.md).

Een van de uitdagingen voor moderne gegevens integratie is het verplaatsen van gegevens naar en van on-premises naar de Cloud. Data Factory maakt deze integratie met Data Management Gateway, een agent die u on-premises kunt installeren om hybride gegevens verplaatsing in te scha kelen.

Raadpleeg de volgende artikelen voor gedetailleerde informatie over Data Management Gateway en hoe u deze kunt gebruiken:

*  [Gegevensbeheergateway](data-factory-data-management-gateway.md)
*  [Gegevens verplaatsen tussen on-premises en de Cloud met behulp van Azure Data Factory](data-factory-move-data-between-onprem-and-cloud.md)


## <a name="current-version"></a>HUIDIGE VERSIE 
De release opmerkingen worden hier niet meer onderhouden. Lees [hier](https://go.microsoft.com/fwlink/?linkid=853077) de nieuwste opmerkingen bij de release




## <a name="earlier-versions"></a>Eerdere versies
## <a name="21063477"></a>2.10.6347.7
### <a name="enhancements-"></a>Opties
- U kunt DNS-vermeldingen toevoegen om service bus toe te staan in plaats van alle IP-adressen van Azure uit uw firewall toe te staan (indien nodig). U kunt de betreffende DNS-vermelding vinden op Azure Portal (Data Factory-> ' Author and Deploy '-> gateways '-> ' serviceUrls ' (in JSON)
- HDFS connector ondersteunt nu een zelfondertekend openbaar certificaat door u te laten TLS-validatie overs Laan.
- Opgelost: probleem met de gateway offline tijdens het bijwerken (vanwege een klok scheefheid)


## <a name="2963132"></a>2.9.6313.2
### <a name="enhancements-"></a>Opties
-   U kunt DNS-vermeldingen toevoegen om Service Bus in plaats van alle IP-adressen van Azure van uw firewall toe te staan (indien nodig). Meer informatie vindt u hier.
-   U kunt nu gegevens kopiëren van/naar een enkele blok-BLOB tot 4,75 TB. Dit is de Maxi maal ondersteunde grootte van een blok-blob. (eerdere limiet is 195 GB).
-   Opgelost: onvoldoende geheugen bij het uitgepakt van verschillende kleine bestanden tijdens de Kopieer activiteit.
-   Opgelost: probleem bij het kopiëren van de document database naar SQL Server met de functie idempotentie.
-   Opgelost: SQL Cleanup script werkt niet met SQL Server van de wizard kopiëren.
-   Fixed: de naam van de kolom met de ruimte aan het einde werkt niet in de Kopieer activiteit.

## <a name="28662833"></a>2.8.66283.3
### <a name="enhancements-"></a>Opties
- Opgelost: probleem met ontbrekende referenties op de gateway computer opnieuw opstarten.
- Opgelost: probleem met registratie tijdens het herstellen van de gateway met behulp van een back-upbestand.


## <a name="2762401"></a>2.7.6240.1
### <a name="enhancements-"></a>Opties
- Fixed: onjuiste Lees van de decimale waarde van Oracle als bron.

## <a name="2661922"></a>2.6.6192.2
### <a name="whats-new"></a>Nieuwe functies
- Klanten kunnen feedback geven over de registratie van de gateway.
- Een nieuwe compressie-indeling ondersteunen: ZIP (effenen)

### <a name="enhancements-"></a>Opties
- Prestatie verbetering voor Oracle-sink, HDFS-bron.
- Fout oplossing voor automatische gateway-updates, gateway parallelle verwerkings capaciteit.


## <a name="2561641"></a>2.5.6164.1
### <a name="enhancements"></a>Verbeteringen 
- Verbeterde en robuuste ervaring voor het registreren van de gateway: u kunt nu de voortgangs status bijhouden tijdens het registratie proces van de gateway, waardoor de registratie-ervaring sneller reageert.
- Verbetering van het herstel proces van de gateway: u kunt nog steeds een gateway herstellen, zelfs als u niet over het back-upbestand van de gateway met deze update beschikt. Hiervoor moet u de referenties van de gekoppelde service opnieuw instellen in de portal.
- Fout oplossing.

## <a name="2461511"></a>2.4.6151.1

### <a name="whats-new"></a>Nieuwe functies

- U kunt de referenties van de gegevens bron nu lokaal opslaan. De referenties zijn versleuteld. De referenties van de gegevens bron kunnen worden hersteld en hersteld met behulp van het back-upbestand dat vanaf de bestaande gateway, al op locatie, kan worden geëxporteerd.

### <a name="enhancements-"></a>Opties

- Verbeterde en krachtigere gateway registratie-ervaring.
- Ondersteuning voor automatische detectie van QuoteChar-configuratie voor tekst opmaak in de wizard kopiëren en verbeteren de nauw keurigheid van de detectie van de indeling.

## <a name="2361002"></a>2.3.6100.2

- Ondersteuning voor firstRowAsHeader en SkipLineCount automatische detectie in de wizard kopiëren voor tekst bestanden in het on-premises bestands systeem en HDFS.
- Verbeter de stabiliteit van de netwerk verbinding tussen gateway en Service Bus
- Enkele fouten opgelost


## <a name="2260721"></a>2.2.6072.1

*  Biedt ondersteuning voor het instellen van de HTTP-proxy voor de gateway met behulp van de gateway Configuration Manager. Als deze zijn geconfigureerd, worden Azure Blob, Azure Table, Azure Data Lake en document database geopend via de HTTP-proxy.
*  Ondersteunt het verwerken van kopteksten voor tekst opmaak bij het kopiëren van gegevens van/naar Azure Blob, Azure Data Lake Store, on-premises bestands systeem en on-premises HDFS.
*  Ondersteunt het kopiëren van gegevens uit een toevoeg-Blob en pagina-BLOB samen met de al ondersteunde blok-blob.
*  Introduceert een nieuwe gateway status **online (beperkt)**, wat aangeeft dat de belangrijkste functionaliteit van de gateway werkt, met uitzonde ring van de ondersteuning van interactieve bewerkingen voor de wizard kopiëren.
*  Verbetert de robuustheid van Gateway registratie met behulp van registratie sleutel.

## <a name="216040"></a>2.1.6040.

*  Het DB2-stuur programma is nu opgenomen in het installatie pakket van de gateway. U hoeft dit niet afzonderlijk te installeren.
*  Het DB2-stuur programma ondersteunt nu z/OS en DB2 voor i (als/400) samen met de platforms die al worden ondersteund (Linux, UNIX en Windows).
*  Ondersteunt het gebruik van Azure Cosmos DB als bron of doel voor on-premises gegevens opslag
*  Ondersteunt het kopiëren van gegevens van/naar koude/Hot Blob Storage samen met het al ondersteunde opslag account voor algemeen gebruik.
*  Met kunt u verbinding maken met SQL Server via gateway met externe aanmeldings bevoegdheden.  

## <a name="2060131"></a>2.0.6013.1

*  U kunt de taal/cultuur selecteren die door een gateway moet worden gebruikt tijdens hand matige installatie.

*  Wanneer de gateway niet werkt zoals verwacht, kunt u ervoor kiezen om de gateway logboeken van de afgelopen zeven dagen naar micro soft te verzenden om de probleem oplossing van het probleem te vergemakkelijken. Als de gateway geen verbinding heeft met de Cloud service, kunt u ervoor kiezen om Gateway logboeken op te slaan en te archiveren.  

*  Verbeteringen in de gebruikers interface voor gateway Configuration Manager:

    *  Zorg ervoor dat de status van de gateway zichtbaar is op het tabblad Start.

    *  De besturings elementen zijn ingedeeld en vereenvoudigd.

    *  U kunt gegevens uit een opslag kopiëren met behulp van het programma voor het [gratis kopiëren van code](data-factory-copy-data-wizard-tutorial.md). Zie [gefaseerde kopie](data-factory-copy-activity-performance.md#staged-copy) voor meer informatie over deze functie in het algemeen.
*  U kunt Data Management Gateway gebruiken om gegevens rechtstreeks vanuit een SQL Server-data base in Azure Machine Learning uit te brengen.

*  Prestatieverbeteringen

    * Verbeter de prestaties voor het weer geven van schema/preview op basis van SQL Server in het programma code-Free Copy.

## <a name="11259531"></a>1.12.5953.1

*  Opgeloste fouten

## <a name="11159181"></a>1.11.5918.1

*  De maximale grootte van het gebeurtenis logboek van de gateway is verhoogd van 1 MB tot 40 MB.

*  Er wordt een waarschuwings venster weer gegeven wanneer opnieuw opstarten nodig is tijdens het automatisch bijwerken van de gateway. U kunt ervoor kiezen om direct opnieuw op te starten of op een later tijdstip.

*  Als het bijwerken van de automatische update mislukt, probeert het installatie programma voor de gateway het automatisch bijwerken drie maal op het maximum.

*  Prestatieverbeteringen

    * Verbeter de prestaties voor het laden van grote tabellen vanaf de on-premises server in scenario met code-Free Copy.

*  Opgeloste fouten

## <a name="11058921"></a>1.10.5892.1

*  Prestatieverbeteringen

*  Opgeloste fouten

## <a name="1958652"></a>1.9.5865.2

*  Mogelijkheid om de auto-update op nul te zetten
*  Nieuw tray-pictogram met Gateway status indicatoren
*  De mogelijkheid om nu bij te werken vanaf de client
*  Mogelijkheid om de tijd van de update planning in te stellen
*  Power shell-script voor het in-/uitschakelen van automatische updates
*  Ondersteuning voor JSON-indeling  
*  Prestatieverbeteringen
*  Opgeloste fouten

## <a name="1858221"></a>1.8.5822.1

*  Probleemoplossings ervaring verbeteren
*  Prestatieverbeteringen
*  Opgeloste fouten

### <a name="1757951"></a>1.7.5795.1

*  Prestatieverbeteringen
*  Opgeloste fouten

### <a name="1757641"></a>1.7.5764.1

*  Prestatieverbeteringen
*  Opgeloste fouten

### <a name="1657351"></a>1.6.5735.1

*  Ondersteuning voor on-premises HDFS-bron/Sink
*  Prestatieverbeteringen
*  Opgeloste fouten

### <a name="1656961"></a>1.6.5696.1

*  Prestatieverbeteringen
*  Opgeloste fouten

### <a name="1656761"></a>1.6.5676.1

*  Diagnostische hulpprogram ma's voor Configuration Manager ondersteunen
*  Ondersteuning voor tabel kolommen voor gegevens bronnen in tabel vorm voor Azure Data Factory
*  Azure Synapse Analytics voor Azure Data Factory ondersteunen
*  Ondersteuning voor reclusive in BlobSource en FileSource voor Azure Data Factory
*  Ondersteuning voor CopyBehavior: MergeFiles, PreserveHierarchy en FlattenHierarchy in BlobSink en FileSink met een binaire kopie voor Azure Data Factory
*  Voortgang van het rapporteren van de ondersteuning voor het kopiëren van activiteiten voor Azure Data Factory
*  Verbindings validatie van gegevens bron voor Azure Data Factory ondersteunen
*  Opgeloste fouten

### <a name="1656721"></a>1.6.5672.1

*  Naam van de ondersteunings tabel voor de ODBC-gegevens bron voor Azure Data Factory
*  Prestatieverbeteringen
*  Opgeloste fouten

### <a name="1656581"></a>1.6.5658.1

*  Bestands Sink voor Azure Data Factory ondersteunen
*  Ondersteuning voor het behouden van een hiërarchie in een binaire kopie voor Azure Data Factory
*  Idempotentie voor de ondersteuning van Kopieer activiteiten voor Azure Data Factory
*  Opgeloste fouten

### <a name="1656401"></a>1.6.5640.1

*  Ondersteuning voor drie gegevens bronnen voor Azure Data Factory (ODBC, OData, HDFS)
*  Ondersteunings aanhalings teken in CSV-parser voor Azure Data Factory
*  Compressie-ondersteuning (BZip2)
*  Opgeloste fouten

### <a name="1556121"></a>1.5.5612.1

*  Ondersteuning voor vijf relationele data bases voor Azure Data Factory (MySQL, PostgreSQL, DB2, Teradata en Sybase)
*  Compressie-ondersteuning (gzip en deflate)
*  Prestatieverbeteringen
*  Opgeloste fouten

### <a name="1455491"></a>1.4.5549.1

*  Ondersteuning voor Oracle-gegevens bronnen voor Azure Data Factory toevoegen
*  Prestatieverbeteringen
*  Opgeloste fouten

### <a name="1454921"></a>1.4.5492.1

*  Unified binary die ondersteuning biedt voor zowel Microsoft Azure Data Factory als Office 365 Power BI Services
*  De configuratie-UI en het registratie proces verfijnen
*  Azure Data Factory: ondersteuning voor Azure inkomend en uitgaand verkeer voor SQL Server gegevens bron

### <a name="1253031"></a>1.2.5303.1

*  Los een time-outprobleem op ter ondersteuning van meer tijdrovende gegevens bron verbindingen.

### <a name="1155268"></a>1.1.5526.8

*  Vereist .NET Framework 4.5.1 als een vereiste tijdens de installatie.

### <a name="1051442"></a>1.0.5144.2

*  Er zijn geen wijzigingen die van invloed zijn op Azure Data Factory scenario's.
