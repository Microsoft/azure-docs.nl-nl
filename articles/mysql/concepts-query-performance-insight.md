---
title: Query Performance Insight-Azure Database for MySQL
description: In dit artikel wordt de Query Performance Insight functie in Azure Database for MySQL beschreven
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 5/12/2020
ms.openlocfilehash: 30457b950e6400c799f35924e7b2ee90d868a465
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94535177"
---
# <a name="query-performance-insight-in-azure-database-for-mysql"></a>Inzicht in queryprestaties in Azure Database for MySQL

**Van toepassing op:** Azure Database for MySQL 5,7, 8,0

Query Performance Insight helpt u snel te identificeren wat uw langste query's zijn, hoe ze in de loop van de tijd veranderen en welke wacht tijden van invloed zijn op deze.

## <a name="common-scenarios"></a>Algemene scenario's

### <a name="long-running-queries"></a>Langdurige query's

- Langst uitgevoerde query's in de afgelopen X uur identificeren
- Eerste N query's identificeren die op resources wachten
 
### <a name="wait-statistics"></a>Wacht statistieken

- Wat is de wacht aard van een query?
- Uitleg over trends voor de resource wacht en waar de bron conflicten bestaan

## <a name="permissions"></a>Machtigingen

De machtigingen **Eigenaar** of **Inzender** zijn vereist om de tekst van de query's weer te geven in Query Performance Insight. Met de machtiging **Lezer** kunt u grafieken en tabellen weergeven maar geen tekst opvragen.

## <a name="prerequisites"></a>Vereisten

Query Performance Insight werkt alleen als de gegevens in het [query archief](concepts-query-store.md)aanwezig zijn.

## <a name="viewing-performance-insights"></a>Prestatie inzichten weer geven

De weergave [Query Performance Insight](concepts-query-performance-insight.md) in de Azure Portal toont visualisaties van belangrijke informatie uit de Query Store.

Selecteer op de pagina Portal van uw Azure Database for MySQL-server **query Performance Insight** onder het gedeelte **intelligente prestaties** van de menu balk.

### <a name="long-running-queries"></a>Langdurige query's

Op het tabblad **langlopende query's** worden de top 5 query's weer gegeven op gemiddelde duur per uitvoering, samengevoegd in intervallen van 15 minuten. U kunt meer query's weer geven door te selecteren in de vervolg keuzelijst **aantal query's** . Het is mogelijk dat de grafiekkleuren voor een specifieke query-id verschillen wanneer u dit doet.

U kunt in de grafiek klikken en slepen om de tijdspanne te beperken tot een specifiek tijdvenster. U kunt ook de pictogrammen in-en uitzoomen gebruiken om respectievelijk een kleinere of grotere tijds periode weer te geven.

:::image type="content" source="./media/concepts-query-performance-insight/query-performance-insight-landing-page.png" alt-text="Langlopende query's Query Performance Insight"::: 

### <a name="wait-statistics"></a>Wacht statistieken

> [!NOTE]
> Wacht statistieken zijn bedoeld voor het oplossen van problemen met de prestaties van query's. U wordt aangeraden alleen in te scha kelen voor het oplossen van problemen. <br>Als het fout bericht wordt weer gegeven in de Azure Portal *, wordt het probleem ' micro soft. DBforMySQL ' aangetroffen. kan de aanvraag niet uitvoeren. Als dit probleem zich blijft voordoen of onverwacht, kunt u contact opnemen met ondersteuning met deze informatie.* gebruik tijdens het weer geven van wacht statistieken een kleinere periode.

Wacht statistieken bieden een weer gave van de wacht gebeurtenissen die optreden tijdens het uitvoeren van een specifieke query. Meer informatie over de gebeurtenis typen wacht in de [documentatie](https://go.microsoft.com/fwlink/?linkid=2098206)van de MySQL-engine.

Selecteer het tabblad **Wachtstatistieken** om de bijbehorende visualisaties voor wachttijden in de server weer te geven.

Query's die worden weer gegeven in de weer gave wachten statistieken, worden gegroepeerd op de query's die de grootste wacht tijden tijdens het opgegeven tijds interval vertonen.

:::image type="content" source="./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png" alt-text="Query Performance Insight wacht op statistieken":::

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [bewaken en afstemmen](concepts-monitoring.md) van Azure database for MySQL.