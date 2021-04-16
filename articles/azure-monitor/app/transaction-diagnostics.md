---
title: Azure-toepassing Insights Transaction Diagnostics | Microsoft Docs
description: Application Insights end-to-end-transactiediagnose
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 60365079c295e154ff0a38277c9ccdec35157e6e
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481392"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Gecombineerde transactiediagnose voor onderdelen

De uniforme diagnostische ervaring correleert automatisch telemetrie aan de serverzijde van al uw Application Insights bewaakte onderdelen in één weergave. Het maakt niet uit of u meerdere resources met afzonderlijke instrumentatiesleutels hebt. Application Insights detecteert de onderliggende relatie en stelt u in staat om eenvoudig het toepassingsonderdeel, de afhankelijkheid of de uitzondering te diagnosticeren die een vertraging of storing in de transactie hebben veroorzaakt.

## <a name="what-is-a-component"></a>Wat is een onderdeel?

Onderdelen zijn onafhankelijk implementeerbare onderdelen van uw gedistribueerde/microservicestoepassing. Ontwikkelaars en operationele teams hebben zichtbaarheid op codeniveau of toegang tot telemetrie die door deze toepassingsonderdelen wordt gegenereerd.

* Onderdelen verschillen van 'waargenomen' externe afhankelijkheden, zoals SQL, EventHub, enzovoort, die uw team/organisatie mogelijk geen toegang heeft (code of telemetrie).
* Onderdelen worden uitgevoerd op een groot aantal server-/rol-/container-exemplaren.
* Onderdelen kunnen afzonderlijke Application Insights zijn (zelfs als abonnementen verschillend zijn) of verschillende rollen die rapporteren aan één Application Insights instrumentatiesleutel. De nieuwe ervaring toont details over alle onderdelen, ongeacht hoe ze zijn ingesteld.

> [!NOTE]
> * **Ontbreken de gerelateerde itemkoppelingen?** Alle gerelateerde telemetrie staat in [de](#cross-component-transaction-chart) bovenste [en](#all-telemetry-with-this-operation-id) onderste secties van de linkerkant. 

## <a name="transaction-diagnostics-experience"></a>Ervaring met transactiediagnose
Deze weergave heeft vier belangrijke onderdelen: resultatenlijst, een transactiegrafiek voor verschillende onderdelen, een tijdreekslijst met alle telemetriegegevens die betrekking hebben op deze bewerking en het detailvenster voor elk geselecteerd telemetrie-item aan de linkerkant.

![Belangrijke onderdelen](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Transactiegrafiek voor kruiscomponenten

Dit diagram bevat een tijdlijn met horizontale balken voor de duur van aanvragen en afhankelijkheden tussen onderdelen. Alle uitzonderingen die worden verzameld, worden ook op de tijdlijn gemarkeerd.

* De bovenste rij in deze grafiek vertegenwoordigt het toegangspunt, de inkomende aanvraag voor het eerste onderdeel met de naam in deze transactie. De duur is de totale tijd die nodig is om de transactie te voltooien.
* Aanroepen naar externe afhankelijkheden zijn eenvoudige niet-samenvouwbare rijen, met pictogrammen die het afhankelijkheidstype vertegenwoordigen.
* Aanroepen naar andere onderdelen zijn samenvouwbare rijen. Elke rij komt overeen met een specifieke bewerking die wordt aangeroepen op het onderdeel.
* Standaard wordt de aanvraag, afhankelijkheid of uitzondering die u hebt geselecteerd aan de rechterkant weergegeven.
* Selecteer een rij om de details [ervan aan de rechterkant weer te geven.](#details-of-the-selected-telemetry) 

> [!NOTE]
> Aanroepen naar andere onderdelen hebben twee rijen: de ene rij vertegenwoordigt de uitgaande aanroep (afhankelijkheid) van het aanroeperonderdeel en de andere rij komt overeen met de inkomende aanvraag van het aangeroepen onderdeel. Het vooraanstaand pictogram en de afzonderlijke stijl van de duurbalken helpen om onderscheid te maken tussen deze staven.

## <a name="all-telemetry-with-this-operation-id"></a>Alle telemetrie met deze bewerkings-id

In deze sectie ziet u een platte lijstweergave in een tijdreeks van alle telemetriegegevens die betrekking hebben op deze transactie. Ook worden de aangepaste gebeurtenissen en traceringen weergegeven die niet worden weergegeven in de transactiegrafiek. U kunt deze lijst filteren op telemetrie die wordt gegenereerd door een specifiek onderdeel/specifieke aanroep. U kunt een telemetrie-item in deze lijst selecteren om de bijbehorende [details aan de rechterkant weer te geven.](#details-of-the-selected-telemetry)

![Tijdreeks van alle telemetrie](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Details van de geselecteerde telemetrie

In dit samenvouwbare deelvenster ziet u de details van elk geselecteerd item uit de transactiegrafiek of de lijst. Met Alles tonen worden alle standaardkenmerken vermeld die worden verzameld. Aangepaste kenmerken worden afzonderlijk vermeld onder de standaardset. Klik op de knop '...' onder het stack-trace-venster om een optie op te halen om de traceer te kopiëren. 'Open profiler traces' of 'Open debug snapshot' toont diagnostische gegevens op codeniveau in de bijbehorende detailvensters.

![Details van uitzondering](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Zoekresultaten

In dit samenvouwbare deelvenster worden de andere resultaten weergegeven die voldoen aan de filtercriteria. Klik op een resultaat om de betreffende details bij te werken in de drie hierboven vermelde secties. We proberen voorbeelden te vinden die waarschijnlijk de details van alle onderdelen beschikbaar zullen hebben, zelfs als steekproeven van kracht zijn in een van deze onderdelen. Deze worden weergegeven als 'voorgestelde' voorbeelden.

![Zoekresultaten](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Profiler en snapshot debugger

[Application Insights profiler](./profiler.md) of [snapshot debugger](snapshot-debugger.md) helpen bij diagnostische gegevens op codeniveau van prestatie- en foutproblemen. Met deze ervaring kunt u profiler-traceringen of momentopnamen van elk onderdeel met één klik bekijken.

Als u Profiler niet aan het werk kunt krijgen, kunt u contact **opnemen met serviceprofilerhelp \@ microsoft.com**

Als u niet aan de slag Snapshot Debugger, kunt u contact opnemen **met snapshothelp \@ microsoft.com**

![Profiler-integratie](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>Veelgestelde vragen

*Ik zie één onderdeel in de grafiek en de andere worden alleen weergegeven als externe afhankelijkheden zonder details van wat er in die onderdelen is gebeurd.*

Mogelijke redenen:

* Zijn de andere onderdelen instrumenteerd met Application Insights?
* Gebruiken ze de nieuwste stabiele Application Insights SDK?
* Als deze onderdelen afzonderlijke Application Insights-resources zijn, [](resources-roles-access-control.md) hebt u dan toegang nodig Als u wel toegang hebt en de onderdelen zijn instrumenteerd met de nieuwste Application Insights-SDK's, laat het ons dan weten via het feedbackkanaal rechtsboven.

*Ik zie dubbele rijen voor de afhankelijkheden. Is dit verwacht?*

Op dit moment wordt de uitgaande afhankelijkheidsoproep afzonderlijk van de inkomende aanvraag weergegeven. Normaal gesproken zien de twee aanroepen er identiek uit, met alleen de duurwaarde die anders is vanwege de netwerkrondrit. Het vooraanstaand pictogram en de afzonderlijke stijl van de duurbalken helpen om onderscheid te maken tussen deze staven. Is deze presentatie van de gegevens verwarrend? Geef ons uw feedback!

*Hoe zit het met klokscheef scheef scheef over verschillende onderdelen?*

Tijdlijnen worden aangepast voor klokscheefheden in de transactiegrafiek. U kunt de exacte tijdstempels zien in het detailvenster of met behulp van Analytics.

*Waarom ontbreken de meeste query's voor gerelateerde items in de nieuwe ervaring?*

Dit is standaard. Alle gerelateerde items, voor alle onderdelen, zijn al beschikbaar aan de linkerkant (bovenste en onderste secties). De nieuwe ervaring heeft twee gerelateerde items die aan de linkerkant niet worden behandelen: alle telemetrie van vijf minuten vóór en na deze gebeurtenis en de gebruikerstijdlijn.

*Ik zie meer gebeurtenissen dan verwacht in de ervaring voor transactiediagnose bij het gebruik van Application Insights JavaScript SDK. Is er een manier om minder gebeurtenissen per transactie te zien?*

De ervaring voor transactiediagnose toont alle telemetrie in [één bewerking](correlation.md#data-model-for-telemetry-correlation) die een bewerkings-id [deelt.](data-model-context.md#operation-id) Standaard maakt de Application Insights SDK voor JavaScript een nieuwe bewerking voor elke unieke paginaweergave. In een toepassing met één pagina (SPA) wordt slechts één paginaweergavegebeurtenis gegenereerd en wordt één bewerkings-id gebruikt voor alle gegenereerde telemetrie. Dit kan ertoe leiden dat veel gebeurtenissen worden gecorreleerd aan dezelfde bewerking. In deze scenario's kunt u Automatisch bijhouden van routes gebruiken om automatisch nieuwe bewerkingen te maken voor navigatie in uw app met één pagina. U moet [enableAutoRouteTracking](javascript.md#single-page-applications) inschakelen zodat er steeds een paginaweergave wordt gegenereerd wanneer de URL-route wordt bijgewerkt (logische paginaweergave). Als u de bewerkings-id handmatig wilt vernieuwen, kunt u dit doen door aan te `appInsights.properties.context.telemetryTrace.traceID = Microsoft.ApplicationInsights.Telemetry.Util.generateW3CId()` roepen. Als u een PageView-gebeurtenis handmatig activeert, wordt ook de bewerkings-id opnieuw ingesteld.
