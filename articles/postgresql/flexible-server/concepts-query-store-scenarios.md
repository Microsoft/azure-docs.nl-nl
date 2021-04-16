---
title: Query Store-scenario's - Azure Database for PostgreSQL - Flex Server
description: In dit artikel worden enkele scenario's voor Query Store in Azure Database for PostgreSQL - Flex Server beschreven.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: ac0374cf91229e7f1b3f84d967d9825e2a3090f2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559041"
---
# <a name="usage-scenarios-for-query-store---flexible-server"></a>Gebruiksscenario's voor Query Store - Flexible Server

**Van toepassing op:** Azure Database for PostgreSQL - Single Server versie 11, 12

U kunt Query Store gebruiken in een groot aantal scenario's waarin het bijhouden en onderhouden van voorspelbare workloadprestaties essentieel is. Bekijk de volgende voorbeelden: 
- Belangrijkste dure query's identificeren en afstemmen 
- A/B-tests 
- Prestaties stabiel houden tijdens upgrades 
- Ad-hocworkloads identificeren en verbeteren 

## <a name="identify-and-tune-expensive-queries"></a>Dure query's identificeren en afstemmen 

### <a name="identify-longest-running-queries"></a>Langst lopende query's identificeren 
Gebruik Query Store-weergaven op azure_sys database van uw server om snel de langst lopende query's te identificeren. Deze query's verbruiken doorgaans de meeste resources. Het optimaliseren van uw langst lopende query's kan de prestaties verbeteren door resources vrij te maken die worden gebruikt door andere query's die op uw systeem worden uitgevoerd. 

### <a name="target-queries-with-performance-deltas"></a>Doelquery's met prestatiedelta's 
Query Store segmenteert de prestatiegegevens in tijdvensters, zodat u de prestaties van een query in de tijd kunt bijhouden. Zo kunt u precies bepalen welke query's bijdragen aan een toename van de totale tijd die wordt besteed. Als gevolg hiervan kunt u gerichte probleemoplossing voor uw workload uitvoeren.

### <a name="tuning-expensive-queries"></a>Dure query's afstemmen 
Wanneer u een query met suboptimale prestaties identificeert, is de actie die u onderneemt afhankelijk van de aard van het probleem: 
- Zorg ervoor dat de statistieken up-to-date zijn voor de onderliggende tabellen die door de query worden gebruikt.
- Overweeg dure query's te herschrijven. Maak bijvoorbeeld gebruik van queryparameters en verminder het gebruik van dynamische SQL. Implementeer optimale logica bij het lezen van gegevens, zoals het toepassen van gegevensfiltering aan de databasezijde, niet aan de toepassingszijde. 


## <a name="ab-testing"></a>A/B-tests 
Gebruik Query Store om de prestaties van workloads voor en na een toepassingswijziging te vergelijken die u wilt introduceren of vóór en na de migratie. Voorbeeldscenario's voor het gebruik van Query Store om de impact van wijzigingen in de prestaties van de werkbelasting te beoordelen: 
- Migratie tussen PostgreSQL-versies. 
- Een nieuwe versie van een toepassing uitrollen. 
- Aanvullende resources toevoegen aan de server. 
- Ontbrekende indexen maken voor tabellen waarnaar wordt verwezen door dure query's. 
- Migratie van enkele server naar Flex Server. 
 
Pas in elk van deze scenario's de volgende werkstroom toe: 
1. Voer uw workload uit met Query Store vóór de geplande wijziging om een prestatiebasislijn te genereren. 
2. Pas toepassingswijziging(en) toe op het gecontroleerde moment in de tijd. 
3. Blijf de workload lang genoeg uitvoeren om na de wijziging een prestatieafbeelding van het systeem te genereren. 
4. Vergelijk de resultaten van vóór en na de wijziging. 
5. Bepaal of u de wijziging of terugdraaien wilt behouden. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Ad-hocworkloads identificeren en verbeteren 
Sommige workloads hebben geen dominante query's die u kunt afstemmen om de algehele prestaties van de toepassing te verbeteren. Deze workloads worden doorgaans gekenmerkt door een relatief groot aantal unieke query's, die elk een deel van de systeembronnen verbruiken. Elke unieke query wordt niet vaak uitgevoerd, dus het verbruik van de runtime afzonderlijk is niet kritiek. Daarentegen wordt, gezien het feit dat de toepassing steeds nieuwe query's genereert, een aanzienlijk deel van de systeembronnen besteed aan querycompilatie, wat niet optimaal is. Dit gebeurt meestal als uw toepassing query's genereert (in plaats van opgeslagen procedures of geparameteriseerde query's) of als deze afhankelijk is van frameworks voor object-relationele toewijzing die standaard query's genereren. 
 
Als u de controle over de toepassingscode hebt, kunt u overwegen de gegevenstoegangslaag te herschrijven om opgeslagen procedures of geparameteriseerde query's te gebruiken. Deze situatie kan echter ook worden verbeterd zonder toepassingswijzigingen door het afdwingen van queryparameterisatie voor de hele database (alle query's) of voor de afzonderlijke querysjablonen met dezelfde queryhash. 

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over de [best practices voor het gebruik van Query Store](concepts-query-store-best-practices.md)
