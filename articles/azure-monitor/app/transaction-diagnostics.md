---
title: Diagnose van Azure-toepassing Insights-trans acties | Microsoft Docs
description: End-to-end-diagnose van trans acties Application Insights
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 7623b7131e6344a67c468d0436884ebfef9b0058
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96746094"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Unified cross-component Trans Action Diagnostics

Met de geïntegreerde diagnose-ervaring wordt de telemetrie van de server automatisch afgestemd op alle Application Insights bewaakte onderdelen in één weer gave. Het maakt niet uit of u meerdere resources met afzonderlijke instrumentatie sleutels hebt. Application Insights detecteert de onderliggende relatie en stelt u in staat om eenvoudig te diagnosticeren op het toepassings onderdeel, de afhankelijkheid of de uitzonde ring waardoor een transactie vertraging of-storing is ontstaan.

## <a name="what-is-a-component"></a>Wat is een onderdeel?

Onderdelen zijn onafhankelijk te implementeren onderdelen van uw gedistribueerde/micro Services-toepassing. Ontwikkel aars en Operations-teams hebben inzicht in de code of toegang tot telemetrie die door deze toepassings onderdelen zijn gegenereerd.

* Onderdelen verschillen van de "waargenomen" externe afhankelijkheden, zoals SQL, EventHub enzovoort. uw team/organisatie heeft mogelijk geen toegang tot (code of telemetrie).
* Onderdelen worden uitgevoerd op een wille keurig aantal server/Role/container-exemplaren.
* Onderdelen kunnen bestaan uit afzonderlijke Application Insights instrumentatie sleutels (zelfs als abonnementen verschillend zijn) of verschillende rollen die rapporteren aan één Application Insights instrumentatie sleutel. De nieuwe ervaring toont details over alle onderdelen, ongeacht hoe ze zijn ingesteld.

> [!NOTE]
> * **De koppelingen van het gerelateerde item ontbreken?** Alle gerelateerde telemetrie bevindt zich in de [bovenste](#cross-component-transaction-chart) en [onderste](#all-telemetry-with-this-operation-id) gedeelten van de linkerkant. 

## <a name="transaction-diagnostics-experience"></a>Onderwerkings ervaring voor trans acties
Deze weer gave heeft vier belang rijke onderdelen: lijst met resultaten, een transactie diagram met meerdere componenten, een lijst met tijd reeksen van alle telemetrie die verband houden met deze bewerking en het detail venster voor elk geselecteerd telemetrie-item aan de linkerkant.

![Belangrijkste onderdelen](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Transactie diagram met meerdere componenten

Deze grafiek bevat een tijd lijn met horizontale balken voor de duur van aanvragen en afhankelijkheden tussen onderdelen. Eventuele uitzonde ringen die worden verzameld, worden ook op de tijd lijn gemarkeerd.

* De bovenste rij in deze grafiek vertegenwoordigt het ingangs punt, de inkomende aanvraag voor de eerste component die wordt aangeroepen in deze trans actie. De duur is de totale tijd die nodig is om de trans actie te volt ooien.
* Alle aanroepen naar externe afhankelijkheden zijn eenvoudige niet-samenvouw bare rijen, met pictogrammen die het type afhankelijkheid vertegenwoordigen.
* Aanroepen naar andere onderdelen zijn samenvouw bare rijen. Elke rij komt overeen met een specifieke bewerking die wordt aangeroepen bij het onderdeel.
* De aanvraag, afhankelijkheid of uitzonde ring die u hebt geselecteerd, wordt standaard weer gegeven aan de rechter kant.
* Selecteer een rij om de [Details](#details-of-the-selected-telemetry)ervan weer te geven. 

> [!NOTE]
> Aanroepen naar andere componenten hebben twee rijen: één rij vertegenwoordigt de uitgaande aanroep (afhankelijkheid) van het onderdeel aanroeper en de andere rij komt overeen met de inkomende aanvraag bij het aangeroepen onderdeel. Het leidende pictogram en de verschillende stijlen van de duur balken helpen bij het onderscheiden van elkaar.

## <a name="all-telemetry-with-this-operation-id"></a>Alle telemetrie met deze bewerkings-id

In deze sectie wordt een platte lijst weergave weer gegeven in een tijd reeks van alle telemetrie die aan deze trans actie zijn gerelateerd. Het bevat ook de aangepaste gebeurtenissen en traceringen die niet worden weer gegeven in het transactie diagram. U kunt deze lijst filteren op telemetrie die is gegenereerd door een specifiek onderdeel/gesprek. U kunt elk wille keurig telemetrie-item selecteren in deze lijst om [de bijbehorende details aan de rechter kant](#details-of-the-selected-telemetry)weer te geven.

![Tijd reeks van alle telemetrie](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Details van de geselecteerde telemetrie

In dit samenvouw paneel wordt de details weer gegeven van een geselecteerd item uit de transactie grafiek of de lijst. ' Alles weer geven ' bevat alle standaard kenmerken die worden verzameld. Aangepaste kenmerken worden afzonderlijk vermeld onder de standaardset. Klik op de '... ' onder het venster Stack tracering krijgt u de mogelijkheid om de tracering te kopiëren. "Profiler-traceringen openen" of "moment opname van fout opsporing openen" toont diagnostische gegevens over code niveau in overeenkomende detail deel Vensters.

![Uitzonderings Details](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Zoekresultaten

In dit samenvouw paneel worden de andere resultaten weer gegeven die voldoen aan de filter criteria. Klik op een resultaat om de betreffende Details in de bovenstaande drie secties bij te werken. We proberen voor beelden te vinden die waarschijnlijk de details van alle onderdelen bevatten, zelfs als er steek proeven van kracht zijn. Deze worden weer gegeven als ' Aanbevolen ' voor beelden.

![Zoekresultaten](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Fout opsporing voor Profiler en moment opname

[Application Insights Profiler](./profiler.md) of [snap shot debugger](snapshot-debugger.md) Help bij het op codeniveau diagnosticeren van prestatie-en fout problemen. Met deze ervaring kunt u Profiler-traceringen of-moment opnamen van elk onderdeel met één klik bekijken.

Als u de Profiler niet meer kunt gebruiken, neemt u contact op met **serviceprofilerhelp \@ Microsoft.com**

Als Snapshot Debugger niet kan worden weer geven, neemt u contact op met **snapshothelp \@ Microsoft.com**

![Integratie van Profiler](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>Veelgestelde vragen

*Ik zie één component in de grafiek en de andere worden alleen weer gegeven als externe afhankelijkheden zonder dat er details zijn van wat er in deze onderdelen is gebeurd.*

Mogelijke redenen:

* Bevinden de andere onderdelen zich met behulp van Application Insights?
* Gebruiken ze de nieuwste stabiele Application Insights SDK?
* Als deze onderdelen van elkaar zijn gescheiden Application Insights resources, hebt u [toegang](resources-roles-access-control.md) nodig als u toegang hebt en de onderdelen zijn voorzien van de meest recente Application Insights sdk's. laat het ons weten via het feedback kanaal rechtsboven.

*Er worden dubbele rijen weer geven voor de afhankelijkheden. Wordt dit verwacht?*

Op dit moment wordt de uitgaande afhankelijkheids aanroep gescheiden van de inkomende aanvraag weer gegeven. Normaal gesp roken zien de twee oproepen er hetzelfde uit als de waarde voor de duur verschilt als gevolg van de netwerk retour. Het leidende pictogram en de verschillende stijlen van de duur balken helpen bij het onderscheiden van elkaar. Is deze presentatie van de gegevens verwarrend? Geef ons uw feedback.

*Wat is de klok tussen verschillende onderdeel exemplaren?*

Tijd lijnen worden aangepast voor klok scheefheden in het transactie diagram. U kunt de exacte tijds tempels bekijken in het detail venster of door analyse te gebruiken.

*Waarom ontbreekt het meren deel van de query's van gerelateerde items?*

Dit is standaard. Alle verwante items, in alle onderdelen, zijn al beschikbaar aan de linkerkant (bovenste en onderste gedeelten). De nieuwe ervaring heeft twee verwante items die aan de linkerkant niet worden bedekt: alle telemetrie van vijf minuten voor en na deze gebeurtenis en de tijd lijn van de gebruiker.

