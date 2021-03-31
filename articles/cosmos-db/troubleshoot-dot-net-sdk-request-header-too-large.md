---
title: Problemen oplossen met het bericht ' aanvraag header te groot ' of 400 ongeldige aanvraag in Azure Cosmos DB
description: Meer informatie over het vaststellen en oplossen van de aanvraag header te grote uitzonde ring.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: a14503bc56777563b6360143efaa86a136b22278
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340512"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-header-too-large-message"></a>Problemen vaststellen en oplossen Azure Cosmos DB bericht ' aanvraag header te groot '
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Het bericht ' aanvraag header te groot ' wordt gegenereerd met een HTTP-fout code 400. Deze fout treedt op als de grootte van de aanvraag header zo groot is geworden dat deze de Maxi maal toegestane grootte overschrijdt. U wordt aangeraden de meest recente versie van de SDK te gebruiken. Gebruik ten minste versie 3. x of 2. x, omdat deze versies het traceren van de header grootte aan het uitzonderings bericht toevoegen.

## <a name="troubleshooting-steps"></a>Stappen voor probleemoplossing
Het bericht ' koptekst aanvraag is te groot ' treedt op als de sessie of het vervolg token te groot is. In de volgende secties worden de oorzaak van het probleem en de bijbehorende oplossing in elke categorie beschreven.

### <a name="session-token-is-too-large"></a>Sessie token is te groot

#### <a name="cause"></a>Oorzaak:
Een verkeerde 400-aanvraag treedt hoogstwaarschijnlijk op omdat het sessie token te groot is. Als de volgende instructies waar zijn, is het sessie token te groot:

* De fout treedt op bij punt bewerkingen zoals maken, lezen en bijwerken, waarbij er geen vervolg token is.
* De uitzonde ring is gestart zonder dat er wijzigingen in de toepassing zijn aangebracht. Het sessie token groeit naarmate het aantal partities toeneemt in de container. Het aantal partities neemt toe naarmate de hoeveelheid gegevens toeneemt of als de door Voer is verhoogd.

#### <a name="temporary-mitigation"></a>Tijdelijke beperking: 
Start uw client toepassing opnieuw op om alle sessie tokens opnieuw in te stellen. Uiteindelijk neemt het sessie token terug naar de vorige grootte waardoor het probleem is veroorzaakt. Gebruik de oplossing in de volgende sectie om dit probleem volledig te voor komen.

#### <a name="solution"></a>Oplossing:
1. Volg de instructies in de artikelen over de prestatie tips van [.net v3](performance-tips-dotnet-sdk-v3-sql.md) of [.NET v2](performance-tips.md) . Converteer de toepassing om de modus directe verbinding te gebruiken met de Transmission Control Protocol (TCP). De modus directe verbinding met het TCP-protocol heeft niet de beperking voor de header grootte, zoals het HTTP-protocol, waardoor dit probleem wordt voor komen. Zorg ervoor dat u de nieuwste versie van de SDK gebruikt. Deze bevat een correctie voor query bewerkingen wanneer de service Interop niet beschikbaar is.
1. Als de modus directe verbinding met het TCP-protocol geen optie is voor uw workload, vermindert u deze door het [consistentie niveau](how-to-manage-consistency.md)van de client te wijzigen. Het sessie token wordt alleen gebruikt voor sessie consistentie, het standaard consistentie niveau voor Azure Cosmos DB. Andere consistentie niveaus gebruiken niet het sessie token.

### <a name="continuation-token-is-too-large"></a>Vervolg token is te groot

#### <a name="cause"></a>Oorzaak:
De 400-ongeldige aanvraag treedt op bij query bewerkingen waarbij het vervolg token wordt gebruikt als het vervolg token te groot is geworden of als verschillende query's verschillende voortzettings token groottes hebben.
    
#### <a name="solution"></a>Oplossing:
1. Volg de instructies in de artikelen over de prestatie tips van [.net v3](performance-tips-dotnet-sdk-v3-sql.md) of [.NET v2](performance-tips.md) . Converteer de toepassing voor gebruik van de modus directe verbinding met het TCP-protocol. De modus directe verbinding met het TCP-protocol heeft niet de beperking voor de header grootte, zoals het HTTP-protocol, waardoor dit probleem wordt voor komen. 
1. Als de modus directe verbinding met het TCP-protocol geen optie is voor uw workload, stelt u de `ResponseContinuationTokenLimitInKb` optie in. U kunt deze optie vinden in `FeedOptions` v2 of `QueryRequestOptions` in v3.

## <a name="next-steps"></a>Volgende stappen
* Problemen [vaststellen en oplossen](troubleshoot-dot-net-sdk.md) wanneer u de Azure Cosmos db .NET SDK gebruikt.
* Meer informatie over prestatie richtlijnen voor [.net v3](performance-tips-dotnet-sdk-v3-sql.md) en [.NET v2](performance-tips.md).
