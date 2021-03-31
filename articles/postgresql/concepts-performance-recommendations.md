---
title: Aanbevelingen voor prestaties-Azure Database for PostgreSQL-één server
description: In dit artikel wordt de functie aanbevolen prestatie aanbeveling in Azure Database for PostgreSQL-één server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: d547844671e6485e71be7dd8c355de08f3dec5e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91710563"
---
# <a name="performance-recommendations-in-azure-database-for-postgresql---single-server"></a>Aanbevelingen voor prestaties in Azure Database for PostgreSQL-één server

**Van toepassing op:** Azure Database for PostgreSQL-één server versie 9,6, 10, 11

Met de functie voor prestatie verbeteringen worden uw data bases geanalyseerd om aangepaste suggesties te maken voor betere prestaties. Voor het produceren van de aanbevelingen kijken de analyse naar verschillende database kenmerken, waaronder schema. Schakel het [query archief](concepts-query-store.md) op de server in om de functie aanbevelingen voor prestaties volledig te benutten. Nadat u een aanbevolen prestatie aanbeveling hebt geïmplementeerd, moet u de prestaties testen om de impact van deze wijzigingen te evalueren. 

## <a name="permissions"></a>Machtigingen
De machtigingen van **Eigenaar** of **Inzender** zijn vereist voor het uitvoeren van analyses met de functie Prestatieaanbevelingen.

## <a name="performance-recommendations"></a>Aanbevelingen voor prestaties
De functie [Prestatieaanbevelingen](concepts-performance-recommendations.md) analyseert workloads op de server om indexen te analyseren met de mogelijkheid om prestaties te verbeteren.

Open **prestatie aanbevelingen** van het gedeelte **intelligente prestaties** van de menu balk op de pagina Azure portal voor uw postgresql-server.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-page.png" alt-text="Landingspagina van Prestatieaanbevelingen":::

Selecteer **analyseren** en kies een Data Base, waarmee de analyse wordt gestart. Afhankelijk van uw werk belasting kan het enkele minuten duren voordat de analyse is voltooid. Wanneer de analyse is voltooid, verschijnt er een melding in de portal. Analyse voert een grondige controle uit van uw data base. We raden u aan om analyses uit te voeren tijdens rustige Peri Oden. 

In het venster **aanbevelingen** wordt een lijst met aanbevelingen weer gegeven als deze zijn gevonden.

:::image type="content" source="./media/concepts-performance-recommendations/performance-recommendations-result.png" alt-text="Nieuwe pagina prestatie aanbevelingen":::

Aanbevelingen worden niet automatisch toegepast. Als u de aanbeveling wilt Toep assen, kopieert u de query tekst en voert u deze uit vanaf de client van uw keuze. Vergeet niet om te testen en te controleren om de aanbeveling te evalueren. 

## <a name="recommendation-types"></a>Aanbevelings typen

Op dit moment worden twee typen aanbevelingen ondersteund: *Create* index en *Drop Index*.

### <a name="create-index-recommendations"></a>Aanbevelingen voor index maken
Bij het *maken van index* aanbevelingen worden nieuwe indexen voorgesteld om de meest frequente uitvoeringen of tijdrovende query's in de werk belasting te versnellen. Voor dit aanbevelings type moet het [query archief](concepts-query-store.md) worden ingeschakeld. In query Store worden query gegevens verzameld en worden de gedetailleerde query runtime en de frequentie statistieken beschreven die de analyse gebruikt om de aanbeveling te doen.

### <a name="drop-index-recommendations"></a>Aanbevelingen voor drop index
Naast het detecteren van ontbrekende indexen, Azure Database for PostgreSQL analyseert de prestaties van bestaande indexen. Als een index zelden wordt gebruikt of overbodig is, raadt het analyse programma aan dit neer te zetten.

## <a name="considerations"></a>Overwegingen
* Prestatie aanbevelingen zijn niet beschikbaar voor het [lezen van replica's](concepts-read-replicas.md).
## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [bewaking en afstemming](concepts-monitoring.md) in Azure Database for PostgreSQL.

