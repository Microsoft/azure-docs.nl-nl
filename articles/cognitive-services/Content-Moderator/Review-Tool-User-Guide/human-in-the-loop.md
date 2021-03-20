---
title: Meer informatie over het evalueren van hulp middelen-Content Moderator
titleSuffix: Azure Cognitive Services
description: Meer informatie over het hulp programma Content Moderator beoordeling, een website die een combi natie van een gecombineerde AI-en Human Review-inspanning coördineert.
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: ddf4d17cd2631f5bc299e392c46bd9065bb13744
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93146610"
---
# <a name="content-moderator-review-tool"></a>Hulp programma Content Moderator beoordeling

Azure Content Moderator biedt services voor het combi neren van machine learning inhouds toezicht met beoordeling door personeel. De website van het [controle programma](https://contentmoderator.cognitive.microsoft.com) is een gebruiks vriendelijke front-end die gedetailleerde toegang biedt tot deze services.

## <a name="what-it-does"></a>Wat het doet

Als u het [hulp programma voor controle](https://contentmoderator.cognitive.microsoft.com)gebruikt in combi natie met de door de machine ondersteunde toezicht-api's, kunt u de volgende taken uitvoeren in het proces voor content toezicht:

- Gebruik één set hulpprogram ma's om inhoud in meerdere indelingen (tekst, afbeelding en video) te gebruiken.
- Automatiseer het maken van mensen [beoordelingen](../review-api.md#reviews) wanneer de resultaten van toezicht-api's binnenkomen.
- Inhouds beoordelingen toewijzen of escaleren naar meerdere beoordelings teams, geordend op inhouds categorie of ervarings niveau.
- Gebruik standaard-of aangepaste logische filters ([werk stromen](../review-api.md#workflows)) om inhoud te sorteren en te traceren zonder dat u code hoeft te schrijven.
- Gebruik [connectors](./configure.md#connectors) om inhoud te verwerken met micro soft PhotoDNA, Text Analytics en face Services naast de content moderator-api's.
- Prestatie gegevens van de belangrijkste prestaties van uw inhouds restrictie processen ophalen.

## <a name="review-tool-dashboard"></a>Dash board hulp programma evalueren

Op het tabblad **dash board** ziet u belang rijke metrische gegevens voor inhouds beoordelingen die in het hulp programma worden uitgevoerd. Bekijk het totale aantal, de volledige en de in behandeling zijnde Beoordelingen voor afbeeldings-, tekst-en video-inhoud. 

In de tabel **in behandeling zijn** de uitsplitsing van gebruikers en subteams die wachtende of voltooide beoordelingen hebben, evenals de sla-tijd resterend. U kunt de items in de tabel selecteren om naar hun beoordelingen te gaan. In het zoekvak boven de tabel kunt u de resultaten filteren op team naam en met het **filter** pictogram kunt u filteren op andere metrische gegevens.

Als u overschakelt naar het tabblad **voltooide beoordelingen** , wordt het totale aantal verwerkte of voltooide items van gebruikers en subteams weer gegeven. U kunt deze gegevens filteren op dezelfde als de beoordelingen die in behandeling zijn.

Als u op de tekst in de rechter bovenhoek van het dash board klikt, worden de dagelijkse persoonlijke gegevens weer gegeven. dit rapporteert het aantal beoordelingen dat voor elk inhouds type is voltooid.

> [!div class="mx-imgBorder"]
> ![Het dash board van het controle programma in een browser](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>Referenties van het controle programma

Wanneer u zich aanmeldt met het [hulp programma voor controle](https://contentmoderator.cognitive.microsoft.com), wordt u gevraagd een Azure-regio voor uw account te selecteren. Dit komt doordat het [beoordelings programma](https://contentmoderator.cognitive.microsoft.com) een gratis proef versie genereert voor Azure content moderator Services. U hebt deze sleutel nodig om toegang te krijgen tot een van de services van een REST-aanroep of client-SDK. U kunt de URL van uw sleutel en API-eind punt weer geven door **beheerders**  >  **referenties** te selecteren.

> [!div class="mx-imgBorder"]
> ![Content Moderator referenties](images/settings-6-credentials.png)

## <a name="next-steps"></a>Volgende stappen

Zie [het hulp programma beoordeling configureren](./configure.md) voor meer informatie over toegang tot resources van het hulp programma bekijken en instellingen wijzigen.