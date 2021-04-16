---
title: Application Insights beschikbaarheidsoverzicht
description: Stel terugkerende webtests in om de beschikbaarheid en reactiesnelheid van uw app of website te bewaken.
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: c3b7a1d0bf8c50c77e5062a702bcdd7600d98d7a
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520704"
---
# <a name="availability-tests-overview"></a>Overzicht van beschikbaarheidstests

Nadat u uw web-app/website hebt geïmplementeerd, kunt u terugkerende tests instellen om de beschikbaarheid en reactiesnelheid te bewaken. [Application Insights](./app-insights-overview.md) verzendt regelmatig webaanvragen naar uw toepassing vanaf punten over de hele wereld. U kunt een waarschuwing ontvangen als uw toepassing niet reageert of als deze te langzaam reageert.

U kunt beschikbaarheidstests instellen voor alle HTTP- en HTTPS-eindpunten die toegankelijk zijn op het openbare internet. U hoeft geen wijzigingen aan te brengen in de website die u test. In feite hoeft het niet eens een site van u te zijn. U kunt de beschikbaarheid testen van een REST API van uw service afhankelijk is.

## <a name="types-of-availability-tests"></a>Typen beschikbaarheidstests

Er zijn vier soorten beschikbaarheidstests:

* [URL-pingtest:](monitor-web-app-availability.md)deze categorie bevat twee eenvoudige tests die u via de portal kunt maken.
    - Eenvoudige pingtest: een eenvoudige test die u kunt maken in de Azure Portal.
    - Standaard pingtest: Een geavanceerdere standaard pingtest met functies zoals het gebruik van http-aanvraagmethoden (bijvoorbeeld , , enzovoort) of het toevoegen `GET` `HEAD` van aangepaste `POST` headers.
* [Webtest met meerdere stappen:](availability-multistep.md)een opname van een reeks webaanvragen, die kan worden afgespeeld om complexere scenario's te testen. Webtests met meerdere stappen worden gemaakt in Visual Studio Enterprise en geüpload naar de portal voor uitvoering.
* [Beschikbaarheidstests voor aangepaste](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)trajecten: als u besluit een aangepaste toepassing te maken om beschikbaarheidstests uit te voeren, kan de methode worden gebruikt om de resultaten te verzenden `TrackAvailability()` naar Application Insights.

> [!IMPORTANT]
> Zowel de [URL-pingtest](monitor-web-app-availability.md) als de webtest met meerdere stappen zijn afhankelijk van de [DNS-infrastructuur](availability-multistep.md) van het openbare internet om de domeinnamen van de geteste eindpunten om te zetten. Dit betekent dat als u Privé-DNS gebruikt, u ervoor moet zorgen dat elke domeinnaam van uw test ook kan worden opgelost door de openbare domeinnaamservers of dat u in plaats daarvan aangepaste beschikbaarheidstests voor trajecten kunt [gebruiken.](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)

**U kunt maximaal 100 beschikbaarheidstests per resource Application Insights maken.**

## <a name="troubleshooting"></a>Problemen oplossen

Specifiek [artikel voor probleemoplossing.](troubleshoot-availability.md)

## <a name="next-step"></a>Volgende stap

* [Beschikbaarheidswaarschuwingen](availability-alerts.md)
* [Webtests met meerdere stappen](availability-multistep.md)
* [URL-tests](monitor-web-app-availability.md)
* [Aangepaste beschikbaarheidstests maken en uitvoeren met Azure Functions.](availability-azure-functions.md)