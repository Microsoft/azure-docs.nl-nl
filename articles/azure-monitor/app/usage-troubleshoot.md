---
title: Problemen oplossen met hulpprogram ma's voor gebruikers analyse-Azure-toepassing Insights
description: 'Gids voor probleem oplossing: analyse van site-en app-gebruik met Application Insights.'
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/11/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a1d77016fdf94de4fdd574b0d4cbd22d6b0b8490
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105024741"
---
# <a name="troubleshoot-user-behavior-analytics-tools-in-application-insights"></a>Problemen oplossen met hulpprogram ma's voor analyse van gebruikers gedrag in Application Insights
Hebt u vragen over de [hulpprogram ma's voor analyse van gebruikers gedrag in Application Insights](usage-overview.md): [gebruikers, sessies, gebeurtenissen](usage-segmentation.md), [trechters](usage-funnels.md), [Gebruikersstromen](usage-flows.md), [retentie](usage-retention.md)of cohorten? Hier volgen enkele antwoorden.

## <a name="counting-users"></a>Gebruikers tellen
**De analyse hulpprogramma's voor gebruikers gedrag tonen dat mijn app één gebruiker/sessie heeft, maar ik weet dat mijn app veel gebruikers/sessies heeft. Hoe kan ik het verkeerde aantal herstellen?**

Alle telemetrie-gebeurtenissen in Application Insights hebben een [anonieme gebruikers-id](./data-model-context.md) en een [sessie-id](./data-model-context.md) als twee van de standaard eigenschappen. Standaard tellen alle hulpprogram ma's voor gebruiks analyse gebruikers en sessies op basis van deze Id's. Als deze standaard eigenschappen niet worden gevuld met unieke Id's voor elke gebruiker en sessie van uw app, wordt in de gebruiks analyse hulpprogramma's een onjuist aantal gebruikers en sessies weer geven.

Als u een web-app bewaken, is de eenvoudigste oplossing om de [Application Insights java script SDK](./javascript.md) toe te voegen aan uw app en ervoor te zorgen dat het script fragment wordt geladen op elke pagina die u wilt bewaken. Met de Java script SDK worden automatisch anonieme gebruikers-en sessie-Id's gegenereerd. vervolgens worden telemetrie-gebeurtenissen met deze Id's gevuld wanneer ze vanuit uw app worden verzonden.

Als u een webservice bewaken (geen gebruikers interface), [maakt u een telemetrie-initialisatie functie waarmee de eigenschappen van de anonieme gebruikers-id en de sessie-id worden ingevuld](./usage-overview.md) op basis van de begrippen van de service van unieke gebruikers en sessies.

Als uw app [geverifieerde gebruikers-id's](./api-custom-events-metrics.md#authenticated-users)verzendt, kunt u tellen op basis van geverifieerde gebruikers-id's in het hulp programma gebruikers. Kies in de vervolg keuzelijst weer geven de optie geverifieerde gebruikers.

De hulpprogram ma's voor analyse van gebruikers gedrag bieden momenteel geen ondersteuning voor het tellen van gebruikers of sessies op basis van andere eigenschappen dan een anonieme gebruikers-ID, een geverifieerde gebruikers-ID of een sessie-ID.

## <a name="naming-events"></a>Naamgevings gebeurtenissen
**Mijn app heeft duizenden verschillende pagina weergaven en aangepaste gebeurtenis namen. Het is moeilijk om deze te onderscheiden en de analyse hulpprogramma's van het gebruikers gedrag worden vaak niet meer op de reactie. Hoe kan ik deze naamgevings problemen oplossen?**

De pagina weergave en aangepaste gebeurtenis namen worden gebruikt in de analyse hulpprogramma's voor gebruikers gedrag. De bron van de naamgeving van gebeurtenissen is van cruciaal belang voor het verkrijgen van waarde uit deze hulpprogram ma's. Het doel is een evenwicht tussen het gebruik van te weinig, te veel generieke namen (' knop geklikt ') en er zijn teveel, specifieke namen (op de knop bewerken geklikt op http: \/ /www.contoso.com/index).

Als u wijzigingen wilt aanbrengen in de pagina weergave en aangepaste gebeurtenis namen die uw app verzendt, moet u de bron code van uw app wijzigen en opnieuw implementeren. **Alle telemetriegegevens in Application Insights worden 90 dagen opgeslagen en kunnen niet worden verwijderd**. wijzigingen die u aanbrengt in gebeurtenis namen, worden 90 dagen in volledig manifesten. Voor de 90 dagen nadat de naam is gewijzigd, worden zowel de oude als de nieuwe namen van de gebeurtenissen in uw telemetrie weer gegeven. u kunt daarom query's en communicatie binnen uw teams aanpassen.

Als uw app te veel pagina weergave namen verzendt, controleert u of deze pagina weergave namen hand matig zijn opgegeven in de code of dat ze automatisch worden verzonden door de Application Insights java script SDK:

* Als de namen van de pagina weergaven hand matig worden opgegeven in code met behulp van de [ `trackPageView` API](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md), wijzigt u de naam zodat deze minder specifiek is. Vermijd veelvoorkomende fouten, zoals het plaatsen van de URL in de naam van de pagina weergave. Gebruik in plaats daarvan de URL-para meter van de `trackPageView` API. Andere details van de pagina weergave naam verplaatsen naar aangepaste eigenschappen.

* Als de Application Insights java script SDK automatisch pagina weergave namen verzendt, kunt u de titels van uw pagina's wijzigen of overschakelen naar het hand matig verzenden van pagina weergave namen. De SDK verzendt standaard de [titel](https://developer.mozilla.org/docs/Web/HTML/Element/title) van elke pagina als de weergave naam van de pagina. U kunt uw titels zo wijzigen dat ze meer algemeen zijn, maar mindful zijn van SEO en andere gevolgen voor deze wijziging. Het hand matig opgeven van pagina weergave namen met de `trackPageView` API overschrijft de automatisch verzamelde namen, zodat u meer algemene namen in telemetrie kunt verzenden zonder pagina titels te wijzigen.   

Als uw app te veel aangepaste gebeurtenis namen verzendt, wijzigt u de naam in de code zodat deze minder specifiek is. Vermijd het gebruik van Url's en andere gegevens per pagina of dynamische informatie rechtstreeks in de aangepaste gebeurtenis namen. Verplaats deze gegevens in plaats daarvan naar aangepaste eigenschappen van de aangepaste gebeurtenis met de `trackEvent` API. In plaats van kunnen we bijvoorbeeld iets Voorst `appInsights.trackEvent("Edit button clicked on http://www.contoso.com/index")` Ellen als `appInsights.trackEvent("Edit button clicked", { "Source URL": "http://www.contoso.com/index" })` .

## <a name="next-steps"></a>Volgende stappen

* [Overzicht van analyse hulpprogramma's voor gebruikers gedrag](usage-overview.md)

## <a name="get-help"></a>Hulp vragen
* [Stack Overflow](https://stackoverflow.com/questions/tagged/ms-application-insights)