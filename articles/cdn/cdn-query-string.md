---
title: Het gedrag van Azure CDN caching bepalen met query reeksen-Standard-laag
description: Met Azure CDN query reeks cachet u regelt hoe bestanden in de cache worden opgeslagen wanneer een webaanvraag een query reeks bevat. In dit artikel wordt de query reeks in de cache opgeslagen in Azure CDN standaard producten.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: 17410e4f-130e-489c-834e-7ca6d6f9778d
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/11/2018
ms.author: allensu
ms.openlocfilehash: 1521d08ef9d431bbe8b3fd3a578297d440ed56b3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018576"
---
# <a name="control-azure-cdn-caching-behavior-with-query-strings---standard-tier"></a>Het gedrag van Azure CDN caching bepalen met query reeksen-Standard-laag
> [!div class="op_single_selector"]
> * [Standaardlaag](cdn-query-string.md)
> * [Premium-laag](cdn-query-string-premium.md)
> 

## <a name="overview"></a>Overzicht
Met Azure Content Delivery Network (CDN) kunt u bepalen hoe bestanden in de cache worden opgeslagen voor een webaanvraag die een query reeks bevat. In een webaanvraag met een query reeks is de query reeks het gedeelte van de aanvraag dat wordt uitgevoerd na een vraag teken (?). Een query reeks kan een of meer sleutel-waardeparen bevatten, waarbij de veld naam en de waarde ervan gescheiden worden door een gelijkteken (=). Elk sleutel-waardepaar wordt gescheiden door een en-teken (&). Bijvoorbeeld http://www.contoso.com/content.mov?field1=value1&veld2 = waarde2. Als er meer dan één sleutel/waarde-paar in een query reeks van een aanvraag is, is de volg orde hiervan niet van belang. 

> [!IMPORTANT]
> De Azure CDN Standard-en Premium-producten bieden dezelfde cache functionaliteit voor de query reeks, maar de gebruikers interface wijkt af. In dit artikel wordt de interface voor **Azure CDN Standard van micro soft** beschreven, **Azure CDN Standard van Akamai** en **Azure CDN Standard van Verizon**. Zie [Azure CDN gedrag in cache opslaan met query reeksen-Premium-laag voor het](cdn-query-string-premium.md)opslaan van de query reeks met **Azure CDN Premium van Verizon**.

Er zijn drie modi voor de query reeks beschikbaar:

- **Query reeksen negeren**: standaard modus. In deze modus geeft het CDN-knoop punt (-of-Presence) de query teken reeksen van de aanvrager door aan de oorspronkelijke server in de eerste aanvraag en slaat de Asset op in de cache. Alle volgende aanvragen voor de Asset die worden bediend door de POP, negeren de query teken reeksen totdat het activum in de cache verloopt.

- **Caching voor query reeksen overs Laan**: in deze modus worden aanvragen met query teken reeksen niet in de cache geplaatst op het CDN pop-knoop punt. Het POP-knoop punt haalt de Asset rechtstreeks van de oorspronkelijke server op en geeft deze door aan de aanvrager bij elke aanvraag.

- **Elke unieke URL in de cache opslaan**: in deze modus wordt elke aanvraag met een unieke URL, inclusief de query reeks, behandeld als een unieke Asset met een eigen cache. Bijvoorbeeld: het antwoord van de oorspronkelijke server voor een aanvraag voor bijvoorbeeld. ashx? q = test1 wordt in de cache geplaatst in het POP-knoop punt en geretourneerd voor volgende caches met dezelfde query reeks. Een aanvraag voor beeld. ashx? q = Test2 wordt in de cache opgeslagen als een afzonderlijk activum met een eigen time-to-Live-instelling.
   
    >[!IMPORTANT] 
    > Gebruik deze modus niet wanneer de query reeks para meters bevat die worden gewijzigd met elke aanvraag, zoals een sessie-ID of een gebruikers naam, omdat dit leidt tot een lage verhouding in de cache-treffer.

## <a name="changing-query-string-caching-settings-for-standard-cdn-profiles"></a>De cache-instellingen voor de query reeks wijzigen voor standaard CDN-profielen
1. Open een CDN-profiel en selecteer vervolgens het CDN-eind punt dat u wilt beheren.
   
   ![Eind punten van CDN-profiel](./media/cdn-query-string/cdn-endpoints.png)
   
2. Klik in het linkerdeel venster onder instellingen op **cache regels**.
   
    ![Knop Regels voor CDN-caching](./media/cdn-query-string/cdn-caching-rules-btn.png)
   
3. Selecteer in de lijst **cache gedrag query reeks** de modus query reeks en klik vervolgens op **Opslaan**.
   
   ![Cache opties voor de CDN-query teken reeks](./media/cdn-query-string/cdn-query-string.png)

> [!IMPORTANT]
> Omdat het enige tijd kost om de registratie door te geven Azure CDN, zijn wijzigingen in de cache teken reeks instellingen mogelijk niet onmiddellijk zichtbaar:
> - Voor profielen van **Azure CDN Standard van Microsoft** is het doorgeven gewoonlijk binnen 10 minuten voltooid. 
> - Profielen van **Azure CDN Standard van Akamai** worden doorgaans binnen één minuut doorgegeven. 
> - Profielen van **Azure CDN Standard van Verizon** en **Azure CDN Premium van Verizon** worden normaal gesproken binnen 10 minuten doorgegeven. 



