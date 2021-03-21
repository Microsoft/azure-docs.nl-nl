---
title: Analyses toevoegen aan de Bing Webzoekopdrachten-API
titleSuffix: Azure Cognitive Services
description: Bing-statistieken bieden analyses voor de Bing Afbeeldingen zoeken-API. Analytics include volume, Top query teken reeksen, geografische distributie en meer.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 02fea02cca5950ef8467377a866e9a765af9e2e1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96349551"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Analyses toevoegen aan de Bing Zoeken-API's

> [!WARNING]
> Bing Search-API's worden van Cognitive Services naar Bing Search Services verplaatst. Vanaf **30 oktober 2020** moeten nieuwe instanties van Bing Search worden ingericht overeenkomstig het proces dat [hier](/bing/search-apis/bing-web-search/create-bing-search-service-resource) is beschreven.
> Bing Search-API's die zijn ingericht met Cognitive Services, worden voor de komende drie jaar of tot het einde van uw Enterprise Agreement ondersteund, afhankelijk van wat het eerst afloopt.
> Raadpleeg [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource) voor migratie-instructies.

Bing-statistieken bieden analyses voor de Bing Zoeken-API's. Deze analyse omvat het aanroep volume, de belangrijkste query reeksen, geografische distributie en nog veel meer. U kunt de Bing-statistieken in de [Azure Portal](https://ms.portal.azure.com) inschakelen door te navigeren naar uw Azure-resource en op **Bing-statistieken inschakelen** te klikken.

> [!IMPORTANT]
> * Bing-statistieken zijn niet beschikbaar voor resources in de `F0` prijs categorie gratis.
> * U mag geen gegevens gebruiken die beschikbaar zijn via het Bing statistieken-dash board om toepassingen te maken voor distributie aan derden.
> * Als u de Bing-statistieken inschakelt, wordt uw abonnements rente enigszins verhoogd. Zie de [prijzen](https://aka.ms/bingstatisticspricing) voor meer informatie.


In de volgende afbeelding ziet u de beschik bare analyses voor elk Bing Search API-eind punt.

![Ondersteunings matrix voor distributie per eind punt](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>Toegang tot uw analyse

Bing werkt de Analytics-gegevens elke 24 uur bij en houdt tot Maxi maal 13 maanden aan geschiedenis die u vanuit het [dash board Analytics](https://bingapistatistics.com)kunt openen. Zorg ervoor dat u bent aangemeld met dezelfde Microsoft-account (MSA) die u hebt gebruikt om u aan te melden voor Bing-statistieken.

> [!NOTE]  
> * Het kan tot 24 uur duren voordat metrische gegevens op het dash board worden weer gegeven. Het dash board toont de datum en tijd waarop de gegevens voor het laatst zijn bijgewerkt.  
> * Metrische gegevens zijn beschikbaar vanaf het moment dat u de invoeg toepassing Bing statistieken inschakelt.

## <a name="filter-the-data"></a>De gegevens filteren

Standaard worden in de grafieken en grafieken alle metrische gegevens weer gegeven waartoe u toegang hebt. U kunt de gegevens die worden weer gegeven in de grafieken en grafieken filteren door de resources, de markten, de eind punten en rapportage periode te selecteren waarin u bent geïnteresseerd. U kunt de volgende filters wijzigen:

- **Resource-id**: de unieke resource-id waarmee uw Azure-abonnement wordt geïdentificeerd. De lijst bevat meerdere Id's als u zich abonneert op meer dan één Bing Search-API-laag. Standaard zijn alle resources geselecteerd.  
  
- **Markten**: de markten waar de resultaten van afkomstig zijn. Bijvoorbeeld: en-US (Engels, Verenigde Staten). Standaard zijn alle markten geselecteerd. De `en-WW` markt is de markt die Bing gebruikt als de aanroep geen markt heeft opgegeven en Bing de markt van de gebruiker niet kan bepalen.  
  
- **Eind punten**: de Bing Search API-eind punten. De lijst bevat alle eind punten waarvoor u een betaald abonnement hebt. Standaard zijn alle eind punten geselecteerd.  

- **Tijds bestek**: de rapportage periode. U kunt het volgende opgeven:
  - **Alle**: inclusief Maxi maal 13 maanden aan gegevens  
  - **Afgelopen 24 uur**: bevat analyses van de afgelopen 24 uur  
  - **Afgelopen week**: bevat analyses van de afgelopen 7 dagen  
  - **Afgelopen maand**: bevat analyses van de afgelopen 30 dagen  
  - **Een aangepast datum bereik**: bevat analyses uit het opgegeven datum bereik, indien beschikbaar  

## <a name="charts-and-graphs"></a>Grafieken en grafieken

Het dash board toont grafieken en grafieken van de metrische gegevens die beschikbaar zijn voor het geselecteerde eind punt. Niet alle metrische gegevens zijn beschikbaar voor alle eind punten. De grafieken en grafieken voor elk eind punt zijn statisch (u kunt geen grafieken en grafieken selecteren om weer te geven). Het dash board toont alleen grafieken en grafieken waarvoor gegevens worden weer gegeven.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

Hieronder vindt u mogelijke metrische gegevens en eindpunt beperkingen.

- **Aanroep volume**: toont het aantal aanroepen dat is gemaakt tijdens de rapportage periode. Als de rapportage periode voor een dag geldt, toont het diagram het aantal aanroepen dat per uur wordt uitgevoerd. Anders toont de grafiek het aantal aanroepen per dag van de rapportage periode.  
  
  > [!NOTE]
  > Het aanroepende volume kan verschillen van facturerings rapporten, wat in het algemeen alleen geslaagde aanroepen omvat.

- **Meest voorkomende query's**: toont de meest voorkomende query's en het aantal exemplaren van elke query tijdens de rapportage periode. U kunt het aantal weer gegeven query's configureren. U kunt bijvoorbeeld de Top 25-, 50-of 75-query's weer geven. De meest voorkomende Query's zijn niet beschikbaar voor de volgende eind punten:  

  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - /videos/details
  - /news
  - /news/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > Sommige query termen kunnen worden onderdrukt om vertrouwelijke informatie, zoals e-mail berichten, telefoon nummers, SSN enz., te verwijderen.

- **Geografische distributie**: de markten waar de zoek resultaten afkomstig zijn. Bijvoorbeeld `en-us` (Engels, Verenigde Staten). Bing gebruikt de `mkt` query parameter om de markt te bepalen, indien opgegeven. Anders gebruikt Bing signalen zoals het IP-adres van de beller om de markt te bepalen.

- De **code verdeling** van de reactie: de HTTP-status codes van alle aanroepen tijdens de rapportage periode.

- **Distributie van oorsprong aanroepen**: de typen browsers die door de gebruikers worden gebruikt. Bijvoorbeeld micro soft Edge, Chrome, Safari en FireFox. Aanroepen van buiten een browser (zoals bots, Postman of krul via een console-app) worden gegroepeerd onder Bibliotheken. De oorsprong wordt bepaald aan de hand van de waarde van de User-Agent-header van de aanvraag. Als de aanvraag de User-Agent header niet bevat, probeert Bing de oorsprong van andere signalen af te leiden.  

- **Distributie van veilige Zoek opdrachten**: de distributie van veilige Zoek waarden. Bijvoorbeeld uit, gemiddeld of strikt. De `safeSearch` query parameter bevat de waarde, indien opgegeven. Anders wordt de waarde standaard ingesteld op gemiddeld.  

- **Antwoorden aangevraagde distributie**: de webzoekopdrachten API-antwoorden die u hebt aangevraagd in de `responseFilter` query parameter.  

- **Antwoorden geretourneerde distributie**: de antwoorden die webzoekopdrachten API geretourneerd in het antwoord.

- **Distributie van de antwoord server**: de toepassings server die uw API-aanvragen heeft verzonden. De mogelijke waarden zijn Bing.com (voor verkeer dat wordt bediend door Desktop-en laptop apparaten) en Bing.com-Mobile (voor verkeer dat wordt bediend door mobiele apparaten). De server wordt bepaald aan de hand van de waarde van de User-Agent-header van de aanvraag. Als de aanvraag de User-Agent header niet bevat, probeert Bing de server af te leiden van andere signalen.

## <a name="next-steps"></a>Volgende stappen

* [Wat zijn de Bing Zoeken-API's?](bing-api-comparison.md)
* [Vereisten voor gebruik en weergave van Bing Zoeken-API's](use-display-requirements.md)