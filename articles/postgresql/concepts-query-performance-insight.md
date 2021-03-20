---
title: Query Performance Insight-Azure Database for PostgreSQL-één server
description: In dit artikel wordt de functie Query Performance Insight van Azure Database for PostgreSQL-één-server beschreven.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: babf2c8208732a194184549dfa5ed3228b376d0f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91710257"
---
# <a name="query-performance-insight"></a>Inzicht in queryprestaties 

**Van toepassing op:** Azure Database for PostgreSQL-één server versie 9,6, 10, 11

Query Performance Insight helpt u snel te identificeren wat uw langste query's zijn, hoe ze in de loop van de tijd veranderen en welke wacht tijden van invloed zijn op deze.

## <a name="permissions"></a>Machtigingen
De machtigingen **Eigenaar** of **Inzender** zijn vereist om de tekst van de query's weer te geven in Query Performance Insight. Met de machtiging **Lezer** kunt u grafieken en tabellen weergeven maar geen tekst opvragen.

## <a name="prerequisites"></a>Vereisten
Query Performance Insight werkt alleen als de gegevens in het [query archief](concepts-query-store.md)aanwezig zijn.

## <a name="viewing-performance-insights"></a>Prestatie inzichten weer geven
De weergave [Query Performance Insight](concepts-query-performance-insight.md) in de Azure Portal toont visualisaties van belangrijke informatie uit de Query Store. 

Selecteer op de pagina Portal van uw Azure Database for PostgreSQL-server **query prestaties inzicht** onder de sectie **intelligente prestaties** van de menu balk.

:::image type="content" source="./media/concepts-query-performance-insight/query-performance-insight-landing-page.png" alt-text="Langlopende query's Query Performance Insight":::

Op het tabblad **langlopende query's** worden de vijf meest voorkomende query's weer gegeven op gemiddelde duur per uitvoering, samengevoegd in intervallen van 15 minuten. U kunt meer query's weergeven door een optie te selecteren in de vervolgkeuzelijst **Aantal query's**. Het is mogelijk dat de grafiekkleuren voor een specifieke query-id verschillen wanneer u dit doet.

U kunt in de grafiek klikken en slepen om de tijdspanne te beperken tot een specifiek tijdvenster. U kunt ook de pictogrammen in-en uitzoomen gebruiken om respectievelijk een kleinere of grotere periode weer te geven.

De tabel onder de grafiek bevat meer informatie over de langlopende query's in dat tijd venster.

Selecteer het tabblad **Wachtstatistieken** om de bijbehorende visualisaties voor wachttijden in de server weer te geven.

:::image type="content" source="./media/concepts-query-performance-insight/query-performance-insight-wait-statistics.png" alt-text="Query Performance Insight wacht op statistieken":::

## <a name="considerations"></a>Overwegingen
* Query Performance Insight is niet beschikbaar voor het [lezen van replica's](concepts-read-replicas.md).

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [bewaking en afstemming](concepts-monitoring.md) in Azure Database for PostgreSQL.


