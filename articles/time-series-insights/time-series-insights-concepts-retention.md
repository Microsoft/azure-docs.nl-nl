---
title: Gegevens retentie in uw omgeving begrijpen-Azure time series Insight | Microsoft Docs
description: In dit artikel worden twee instellingen beschreven die de Bewaar periode van gegevens in uw Azure Time Series Insights omgeving regelen.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/29/2020
ms.custom: seodec18
ms.openlocfilehash: 4f236679d0662df852581a6a8408ed6bc0d4e3fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91535686"
---
# <a name="understand-data-retention-in-azure-time-series-insights-gen1"></a>Meer informatie over gegevens retentie in Azure Time Series Insights gen1

> [!CAUTION]
> Dit is een Gen1-artikel.

In dit artikel worden twee primaire instellingen beschreven die van invloed zijn op de Bewaar periode van gegevens in uw Azure Time Series Insights omgeving.

## <a name="video"></a>Video

### <a name="the-following-video-summarizes-azure-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>De volgende video bevat een overzicht van Azure Time Series Insights gegevens retentie en hoe u deze kunt plannen.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Elk van uw Azure Time Series Insights omgevingen heeft een instelling waarmee de **Bewaar tijd van gegevens** wordt beheerd. De waarde ligt tussen 1 en 400 dagen. De gegevens worden verwijderd op basis van de opslag capaciteit van de omgeving of de Bewaar periode, afhankelijk van wat het eerste komt.

Daarnaast heeft uw Azure Time Series Insights omgeving een instelling voor het gedrag van de **opslag limiet overschreden** . Het bepaalt de ingang en het opschonen van gedrag wanneer de maximale capaciteit van een omgeving wordt bereikt. Er zijn twee manieren om te kiezen bij de configuratie:

- **Oude gegevens opschonen** (standaard)  
- **Ingangs onderbrekingen**

> [!NOTE]
> Wanneer u een nieuwe omgeving maakt, wordt standaard de Bewaar periode geconfigureerd om **oude gegevens te verwijderen**. Deze instelling kan worden in-of uitgeschakeld nadat deze is gemaakt met behulp van de Azure Portal op de pagina **configureren** van de Azure time series Insights omgeving.
>
> - Lees voor meer informatie over het configureren van Bewaar beleid het [configureren van Bewaar periode in azure time series Insights](time-series-insights-how-to-configure-retention.md).

Beide beleids regels voor het bewaren van gegevens worden in meer detail beschreven.

## <a name="purge-old-data"></a>Oude gegevens opschonen

- **Oude gegevens opschonen** is de standaard instelling voor Azure time series Insights omgevingen.  
- Het **opschonen van oude gegevens** verdient de voor keur wanneer gebruikers altijd hun *meest recente gegevens* in hun Azure time series Insights omgeving willen hebben.
- Met de instelling **oude gegevens opschonen** worden gegevens *verwijderd* zodra de limieten van de omgeving (de retentie tijd, de grootte of het aantal, afhankelijk van wat het eerste komt) worden bereikt. De Bewaar periode is standaard ingesteld op 30 dagen.
- De oudste opgenomen gegevens worden eerst opgeschoond (de ' First ' first out '-benadering).

### <a name="example-one"></a>Voor beeld 1

Bekijk een voor beeld van een omgeving met Bewaar gedrag door te gaan met inkomend **en oude gegevens op te schonen**:

De **Bewaar tijd voor gegevens** is ingesteld op 400 dagen. De **capaciteit** wordt ingesteld op S1-eenheid, die 30 GB aan totale capaciteit bevat. Laten we uitgaan dat inkomende gegevens elke dag gemiddeld 500 MB worden opgeteld. In deze omgeving kunnen alleen 60 dagen voor gegevens worden bewaard op basis van het aantal inkomende gegevens, omdat de maximum capaciteit wordt bereikt om 60 dagen. De inkomende gegevens worden als volgt verzameld: 500 MB elke dag x 60 dagen = 30 GB.

Op de dag van de 61st worden de meest recente gegevens in de omgeving weer gegeven, maar worden de oudste gegevens ouder dan 60 dagen verwijderd. Het leegmaken maakt ruimte voor de nieuwe gegevensstreaming in, zodat nieuwe gegevens kunnen worden geverkennen. Als de gebruiker gegevens langer wil bewaren, kunnen ze de omvang van de omgeving verg Roten door extra eenheden toe te voegen of minder gegevens te pushen.  

### <a name="example-two"></a>Voor beeld twee

Denk ook na over een omgeving geconfigureerde Bewaar gedrag door te gaan met inkomend **en oude gegevens te verwijderen**. In dit voor beeld wordt de **Bewaar periode voor gegevens** ingesteld op een lagere waarde van 180 dagen. De **capaciteit** wordt ingesteld op S1-eenheid, die 30 GB aan totale capaciteit bevat. Als u gegevens voor de volledige 180 dagen wilt opslaan, mag de dagelijkse ingang niet meer dan 0,166 GB (166 MB) per dag bedragen.  

Wanneer het dagelijkse ingangs gemiddelde van deze omgeving meer dan 0,166 GB per dag overschrijdt, kunnen gegevens gedurende 180 dagen niet worden opgeslagen, omdat sommige gegevens worden opgeschoond. Houd rekening met deze omgeving tijdens een actief tijds bestek. Stel dat de ingangs snelheid van de omgeving kan toenemen tot een gemiddelde van 0,189 GB per dag. In het kader van deze bezette tijd worden ongeveer 158 dagen aan gegevens bewaard (30 GB/0.189 = 158,73 dagen retentie). Deze tijd is kleiner dan het gewenste tijds bestek voor het bewaren van gegevens.

## <a name="pause-ingress"></a>Ingangs onderbrekingen

- De instelling voor het onderbreken van de **onderbreking** is zodanig ontworpen dat de gegevens niet worden opgeschoond als de limieten voor grootte en aantal zijn bereikt voordat de Bewaar periode is verstreken.  
- **Onderbrekingen** van inkomend verkeer bieden extra tijd voor de gebruikers om de capaciteit van hun omgeving te verg Roten voordat de gegevens worden opgeschoond door schending van de Bewaar periode.
- Het helpt u bij het verlies van gegevens, maar u kunt ook een kans maken voor het verlies van uw meest recente gegevens als binnenkomend wordt gepauzeerd na de Bewaar periode van de bron van de gebeurtenis.
- Zodra de maximum capaciteit van een omgeving is bereikt, worden de gegevens in de omgeving echter onderbroken totdat de volgende aanvullende acties worden uitgevoerd:

  - U verhoogt de maximum capaciteit van de omgeving om meer schaal eenheden toe te voegen, zoals wordt beschreven in [uw Azure time series Insights omgeving schalen](time-series-insights-how-to-scale-your-environment.md).
  - De Bewaar periode voor gegevens is bereikt en de gegevens worden opgeschoond, waardoor de omgeving onder de maximum capaciteit komt.

### <a name="example-three"></a>Voor beeld drie

Denk na over een omgeving met Bewaar gedrag dat is geconfigureerd om **ingangen te onderbreken**. In dit voor beeld wordt de **Bewaar periode voor gegevens** ingesteld op 60 dagen. De **capaciteit** is ingesteld op drie (3) eenheden van S1. We gaan ervan uit dat deze omgeving elke dag 2 GB gegevens bevat. In deze omgeving wordt binnenkomend gepauzeerd zodra de maximum capaciteit is bereikt.

Op dat moment wordt in de omgeving dezelfde gegevensset weer gegeven totdat ingangen wordt hervat of **totdat de** inschakeling is ingeschakeld (waardoor oudere gegevens worden opgeschoond om ruimte te maken voor nieuwe gegevens).

Wanneer ingangs werkzaamheden worden hervat:

- Gegevens stromen in de volg orde waarin deze zijn ontvangen door de gebeurtenis bron
- De gebeurtenissen worden geïndexeerd op basis van hun tijds tempel, tenzij u het Bewaar beleid voor uw gebeurtenis bron hebt overschreden. [Event hubs Veelgestelde vragen](../event-hubs/event-hubs-faq.md) voor meer informatie over het configureren van de Bewaar periode voor gebeurtenis bronnen.

> [!IMPORTANT]
> Stel waarschuwingen in om te zorgen dat binnenkomend verkeer niet wordt onderbroken. Gegevens verlies is mogelijk omdat de standaard Bewaar periode 1 dag is voor Azure-gebeurtenis bronnen. Wanneer de ingang is onderbroken, verliest u waarschijnlijk de meest recente gegevens, tenzij er extra actie wordt ondernomen. U moet de capaciteit verg Roten of overschakelen om **oude gegevens te verwijderen** om de kans op gegevens verlies te voor komen.

In de betrokken Event Hubs kunt u overwegen om de eigenschap voor het **bewaren van berichten** aan te passen om gegevens verlies te minimaliseren wanneer inkomend onderbreken plaatsvindt in azure time series Insights.

[![Bewaren van Event hub-berichten.](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

Als er geen eigenschappen zijn geconfigureerd voor de gebeurtenis bron ( `timeStampPropertyName` ), Azure time series Insights standaard ingesteld op de tijds tempel van de aankomst bij Event hub als de X-as. Als `timeStampPropertyName` is geconfigureerd om iets anders te zijn, zoekt de omgeving naar de geconfigureerde `timeStampPropertyName` waarde in het gegevens pakket wanneer gebeurtenissen worden geparseerd.

Lees [hoe u uw Azure time series Insights omgeving kunt schalen](time-series-insights-how-to-scale-your-environment.md) om uw omgeving te schalen, zodat u meer capaciteit hebt of de Bewaar periode kunt verg Roten.

## <a name="next-steps"></a>Volgende stappen

- Voor informatie over het configureren of wijzigen van de instellingen voor het bewaren van gegevens, raadpleegt u [retentie configureren in azure time series Insights](time-series-insights-how-to-configure-retention.md).

- Meer informatie over het [beperken van latentie in azure time series Insights](time-series-insights-environment-mitigate-latency.md).
