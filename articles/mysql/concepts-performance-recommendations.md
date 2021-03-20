---
title: Aanbevelingen voor prestaties-Azure Database for MySQL
description: In dit artikel wordt de functie aanbevolen prestatie aanbeveling in Azure Database for MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 1445ad3d6be020326c34d60c555c24c435548fd0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94537642"
---
# <a name="performance-recommendations-in-azure-database-for-mysql"></a>Aanbevelingen in Azure Database for MySQL voor prestaties

**Van toepassing op:** Azure Database for MySQL 5,7, 8,0

Met de functie voor prestatie verbeteringen worden uw data bases geanalyseerd om aangepaste suggesties te maken voor betere prestaties. Voor het produceren van de aanbevelingen kijken de analyse naar verschillende database kenmerken, waaronder schema. Schakel het [query archief](concepts-query-store.md) op de server in om de functie aanbevelingen voor prestaties volledig te benutten. Als het prestatie schema is uitgeschakeld, schakelt het inschakelen van query Store performance_schema en een subset van performance schema-instrumenten in die voor de functie zijn vereist. Nadat u een aanbevolen prestatie aanbeveling hebt geïmplementeerd, moet u de prestaties testen om de impact van deze wijzigingen te evalueren.

## <a name="permissions"></a>Machtigingen

De machtigingen van **Eigenaar** of **Inzender** zijn vereist voor het uitvoeren van analyses met de functie Prestatieaanbevelingen.

## <a name="performance-recommendations"></a>Aanbevelingen voor prestaties

De functie [Prestatieaanbevelingen](concepts-performance-recommendations.md) analyseert workloads op de server om indexen te analyseren met de mogelijkheid om prestaties te verbeteren.

Open **prestatie aanbevelingen** van het gedeelte **intelligente prestaties** van de menu balk op de pagina Azure portal voor uw MySQL-server.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-page.png" alt-text="Landingspagina van Prestatieaanbevelingen":::

Selecteer **analyseren** en kies een Data Base, waarmee de analyse wordt gestart. Afhankelijk van uw werk belasting kan het enkele minuten duren voordat de analyse is voltooid. Wanneer de analyse is voltooid, verschijnt er een melding in de portal. Analyse voert een grondige controle uit van uw data base. We raden u aan om analyses uit te voeren tijdens rustige Peri Oden.

In het venster **aanbevelingen** wordt een lijst met aanbevelingen weer gegeven als deze zijn gevonden en de gerelateerde query-id die deze aanbeveling heeft gegenereerd. Met de query-ID kunt u de weer gave [mysql.query_store](concepts-query-store.md#mysqlquery_store) gebruiken voor meer informatie over de query.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-result.png" alt-text="Nieuwe pagina prestatie aanbevelingen":::

Aanbevelingen worden niet automatisch toegepast. Als u de aanbeveling wilt Toep assen, kopieert u de query tekst en voert u deze uit vanaf de client van uw keuze. Vergeet niet om te testen en te controleren om de aanbeveling te evalueren.

## <a name="recommendation-types"></a>Aanbevelings typen

### <a name="index-recommendations"></a>Aanbevelingen voor index

Bij het *maken van index* aanbevelingen worden nieuwe indexen voorgesteld om de meest frequente uitvoeringen of tijdrovende query's in de werk belasting te versnellen. Voor dit aanbevelings type moet het [query archief](concepts-query-store.md) worden ingeschakeld. In query Store worden query gegevens verzameld en worden de gedetailleerde query runtime en de frequentie statistieken beschreven die de analyse gebruikt om de aanbeveling te doen.

### <a name="query-recommendations"></a>Query aanbevelingen

Query aanbevelingen suggereren optimalisaties en herschrijven voor query's in de werk belasting. De prestaties van tijdrovende query's kunnen worden verbeterd door de MySQL-query anti patronen te identificeren en de syntaxis op te lossen. Voor dit aanbevelings type moet het query archief worden ingeschakeld. In query Store worden query gegevens verzameld en worden de gedetailleerde query runtime en de frequentie statistieken beschreven die de analyse gebruikt om de aanbeveling te doen.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [bewaken en afstemmen](concepts-monitoring.md) van Azure database for MySQL.