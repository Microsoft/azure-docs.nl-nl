---
title: Scenario's voor het opslaan van query's-Azure Database for PostgreSQL-één server
description: In dit artikel worden enkele scenario's beschreven voor het query archief in Azure Database for PostgreSQL-één-server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ad646e39e9b6a87611dc2f40330a77fe70c92c27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91710546"
---
# <a name="usage-scenarios-for-query-store"></a>Gebruiks scenario's voor query Store

**Van toepassing op:** Azure Database for PostgreSQL-één server versie 9,6, 10, 11

U kunt query Store gebruiken in een groot aantal scenario's waarin de prestaties van voorspel bare werk belastingen kritiek zijn. Bekijk de volgende voorbeelden: 
- Best dure query's identificeren en afstemmen 
- A/B-tests 
- Prestaties stabiel houden tijdens upgrades 
- Ad hoc-workloads identificeren en verbeteren 

## <a name="identify-and-tune-expensive-queries"></a>Dure query's identificeren en afstemmen 

### <a name="identify-longest-running-queries"></a>Langst actieve query's identificeren 
Gebruik de weer gave [query Performance Insight](concepts-query-performance-insight.md) in de Azure Portal om snel de langste query's te identificeren. Deze query's gebruiken meestal vaak een aanzienlijke hoeveelheid resources. Het optimaliseren van uw langste vragen kan de prestaties verbeteren door resources vrij te maken voor gebruik door andere query's die op uw systeem worden uitgevoerd. 

### <a name="target-queries-with-performance-deltas"></a>Doel query's met prestatie Deltas 
Met query Store worden de prestatie gegevens in tijd Vensters gesegmenteerd, zodat u de prestaties van een query in de loop van de tijd kunt volgen. Zo kunt u precies zien welke query's bijdragen aan een toename van de totale bestede tijd. Als gevolg hiervan kunt u de problemen met de werk belasting richten.

### <a name="tuning-expensive-queries"></a>Dure query's afstemmen 
Wanneer u een query met optimale prestaties identificeert, is de actie die u uitvoert afhankelijk van de aard van het probleem: 
- Gebruik [aanbevelingen voor prestaties](concepts-performance-recommendations.md) om te bepalen of er aanbevolen indexen zijn. Als dat het geval is, maakt u de index en gebruikt u query Store om de query prestaties te evalueren nadat u de index hebt gemaakt. 
- Zorg ervoor dat de statistieken zijn bijgewerkt voor de onderliggende tabellen die worden gebruikt door de query.
- Overweeg duurste query's te herschrijven. U kunt bijvoorbeeld gebruikmaken van query parameterisering en het gebruik van dynamische SQL verminderen. Implementeer optimale logica bij het lezen van gegevens, zoals het Toep assen van gegevens filtering aan de data base en niet aan de kant van de toepassing. 


## <a name="ab-testing"></a>A/B-tests 
Gebruik query Store om de prestaties van de werk belasting te vergelijken vóór en na de wijziging van een toepassing die u wilt introduceren. Voor beelden van scenario's voor het gebruik van query Store voor het beoordelen van de impact van de omgeving of de toepassing die wordt gewijzigd voor de prestaties van de werk belasting: 
- Een nieuwe versie van een toepassing uit te vouwen. 
- Extra resources toevoegen aan de server. 
- Ontbrekende indexen maken voor tabellen waarnaar wordt verwezen door dure query's. 
 
Pas in een van deze scenario's de volgende werk stroom toe: 
1. Voer de werk belasting uit met het query archief vóór de geplande wijziging om een basis lijn voor prestaties te genereren. 
2. Toepassings wijziging (en) Toep assen op het tijdstip van controle. 
3. De werk belasting lang genoeg blijven uitvoeren om de prestatie kopie van het systeem te genereren na de wijziging. 
4. Vergelijk de resultaten van voor en na de wijziging. 
5. Bepaal of u de wijziging of het terugdraaien wilt behouden. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Ad hoc-workloads identificeren en verbeteren 
Sommige werk belastingen hebben geen dominante query's die u kunt afstemmen om de algehele prestaties van toepassingen te verbeteren. Deze werk belastingen worden meestal gekenmerkt met een relatief groot aantal unieke query's, die allemaal een deel van de systeem bronnen gebruiken. Elke unieke query wordt niet regel matig uitgevoerd, zodat het runtime verbruik van de afzonderlijke activiteiten niet kritiek is. Aan de andere kant, op voor hand dat de toepassing steeds nieuwe query's genereert, wordt een aanzienlijk deel van de systeem bronnen aan het compileren van query's gespendeerd. Dit is niet optimaal. Deze situatie treedt meestal op als uw toepassing query's genereert (in plaats van opgeslagen procedures of query's met para meters) of als deze afhankelijk zijn van object-relationele toewijzings raamwerken die standaard query's genereren. 
 
Als u controle hebt over de toepassings code, kunt u overwegen de Gegevenstoegangslaag te herschrijven om opgeslagen procedures of query's met para meters te gebruiken. Deze situatie kan echter ook worden verbeterd zonder toepassings wijzigingen door het afdwingen van de query parameterisering voor de gehele data base (alle query's) of voor de afzonderlijke query sjablonen met dezelfde query-hash. 

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [Aanbevolen procedures voor het gebruik van query Store](concepts-query-store-best-practices.md)