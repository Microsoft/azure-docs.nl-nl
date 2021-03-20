---
title: Gegevens laten verlopen in Azure Cosmos DB met Time to Live
description: Met TTL biedt Microsoft Azure Cosmos DB de mogelijkheid om documenten na een bepaalde tijd automatisch van het systeem te verwijderen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/04/2020
ms.reviewer: sngun
ms.openlocfilehash: cf9d0aea9ab9e79a5f184a42e1bb785b6fb870a7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93360085"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Time to Live (TTL) configureren in Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Met **time to Live** of TTL biedt Azure Cosmos DB de mogelijkheid om automatisch items uit een container te verwijderen na een bepaalde tijds periode. Standaard kunt u time to Live instellen op container niveau en de waarde per item overschrijven. Nadat u de TTL hebt ingesteld op een container of op item niveau, worden deze items na de periode door Azure Cosmos DB automatisch verwijderd, sinds de tijd waarop ze voor het laatst zijn gewijzigd. De waarde voor time to Live wordt in seconden geconfigureerd. Wanneer u TTL configureert, worden de verlopen items automatisch door het systeem verwijderd op basis van de TTL-waarde, zonder dat er een Verwijder bewerking hoeft te worden uitgevoerd die expliciet door de client toepassing wordt verleend. De maximum waarde voor TTL is 2147483647.

Het verwijderen van verlopen items is een achtergrond taak die gebruikmaakt van aanvragen voor links op [aanvraag](request-units.md), die aanvraag eenheden zijn die niet zijn verbruikt door gebruikers aanvragen. Zelfs nadat de TTL is verlopen, wordt de verwijdering van gegevens vertraagd als de container is overbelast met aanvragen en als er onvoldoende RU beschikbaar is. Gegevens worden verwijderd wanneer voldoende RUs beschikbaar is om de Verwijder bewerking uit te voeren. Hoewel het verwijderen van gegevens is vertraagd, worden er geen gegevens geretourneerd door query's (door een API) nadat de TTL is verlopen.

> Deze inhoud is gerelateerd aan Azure Cosmos DB transactionele Store-TTL. Als u op zoek bent naar een levens duur van een analytische opslag, waarmee u NoETL HTAP-scenario's via [Azure Synapse link](./synapse-link.md)kunt maken, klikt u [hier](./analytical-store-introduction.md#analytical-ttl).

## <a name="time-to-live-for-containers-and-items"></a>Time to Live voor containers en items

De waarde voor TTL (time to Live) wordt ingesteld in seconden en wordt geïnterpreteerd als een verschil vanaf het tijdstip waarop een item voor het laatst is gewijzigd. U kunt time to Live instellen voor een container of een item in de container:

1. **Time to Live op een container** (ingesteld met `DefaultTimeToLive` ):

   - Als de waarde ontbreekt (of is ingesteld op null), worden items niet automatisch verlopen.

   - Indien aanwezig en de waarde is ingesteld op '-1 ', is deze gelijk aan oneindig en worden items niet standaard verloopt.

   - Indien aanwezig en de waarde is ingesteld op een getal dat *niet gelijk* is aan nul *' n '* : items verlopen *' n '* seconden na het tijdstip waarop het voor het laatst is gewijzigd.

2. **Time to Live voor een item** (ingesteld met `ttl` ):

   - Deze eigenschap is alleen van toepassing als deze `DefaultTimeToLive` aanwezig is en niet is ingesteld op null voor de bovenliggende container.

   - Indien aanwezig, wordt de `DefaultTimeToLive` waarde van de bovenliggende container overschreven.

## <a name="time-to-live-configurations"></a>Time to Live configuraties

- Als TTL op een container is ingesteld op *' n '* , verlopen de items in die container na *n* seconden.  Als er items in dezelfde container zijn die hun eigen tijd hebben, ingesteld op-1 (wat aangeeft dat ze niet verlopen) of als sommige items de time-to-Live-instelling hebben overschreven met een ander nummer, verlopen deze items op basis van hun eigen geconfigureerde TTL-waarde.

- Als TTL niet is ingesteld voor een container, heeft de time-to-Live voor een item in deze container geen effect.

- Als TTL voor een container is ingesteld op-1, verloopt een item in deze container met de time to Live ingesteld op n, vervalt na n seconden en blijven de resterende items verlopen.

## <a name="examples"></a>Voorbeelden

In deze sectie worden enkele voor beelden weer gegeven met verschillende time to Live-waarden die zijn toegewezen aan containers en items:

### <a name="example-1"></a>Voorbeeld 1

De TTL voor de container is ingesteld op Null (DefaultTimeToLive = null)

|TTL voor item| Resultaat|
|---|---|
|TTL = Null|TTL is uitgeschakeld. Het item verloopt nooit (standaard).|
|TTL =-1|TTL is uitgeschakeld. Het item verloopt nooit.|
|TTL = 2000|TTL is uitgeschakeld. Het item verloopt nooit.|

### <a name="example-2"></a>Voorbeeld 2

TTL in container is ingesteld op-1 (DefaultTimeToLive =-1)

|TTL voor item| Resultaat|
|---|---|
|TTL = Null|TTL is ingeschakeld. Het item verloopt nooit (standaard).|
|TTL =-1|TTL is ingeschakeld. Het item verloopt nooit.|
|TTL = 2000|TTL is ingeschakeld. Het item verloopt na 2000 seconden.|

### <a name="example-3"></a>Voorbeeld 3

TTL in container is ingesteld op 1000 (DefaultTimeToLive = 1000)

|TTL voor item| Resultaat|
|---|---|
|TTL = Null|TTL is ingeschakeld. Het item verloopt na 1000 seconden (standaard).|
|TTL =-1|TTL is ingeschakeld. Het item verloopt nooit.|
|TTL = 2000|TTL is ingeschakeld. Het item verloopt na 2000 seconden.|

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het configureren van Time to Live in de volgende artikelen:

- [Time to Live configureren](how-to-time-to-live.md)