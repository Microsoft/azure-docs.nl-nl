---
title: Beveiligings incidenten beheren in Azure Security Center | Microsoft Docs
description: Dit document helpt u bij het gebruik van Azure Security Center voor het beheren van beveiligings incidenten.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 722a508679c74f9d62df07575ffa1006528f4398
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652097"
---
# <a name="manage-security-incidents-in-azure-security-center"></a>Beveiligings incidenten beheren in Azure Security Center

Uitsorteren en onderzoek van beveiligings waarschuwingen kan tijdrovend zijn voor zelfs de meest ervaren beveiligings analisten. Voor veel is het moeilijk te weten waar u moet beginnen. 

Security Center gebruikt [Analytics](./security-center-alerts-overview.md) om de informatie te verbinden tussen afzonderlijke [beveiligings waarschuwingen](security-center-managing-and-responding-alerts.md). Met deze verbindingen kunt Security Center een enkele weer gave van een aanvals campagne en de bijbehorende waarschuwingen bieden die u helpen de acties van de aanvaller en de betrokken resources te begrijpen.

Op deze pagina vindt u een overzicht van incidenten in Security Center.

## <a name="what-is-a-security-incident"></a>Wat is een beveiligingsincident?

In Security Center is een beveiligingsincident een samenloop van alle waarschuwingen voor een resource die overeenstemt met [kill chain](alerts-reference.md#intentions)-patronen. Incidenten worden weer gegeven op de pagina [beveiligings waarschuwingen](security-center-managing-and-responding-alerts.md) . Selecteer een incident om de gerelateerde waarschuwingen weer te geven en meer informatie te krijgen.

## <a name="managing-security-incidents"></a>Beveiligingsincidenten beheren

1. Op de pagina waarschuwingen van Security Center klikt u op de knop **filter toevoegen** om te filteren op waarschuwings naam in het **beveiligings incident waarschuwing dat op meerdere resources is gedetecteerd**. 

    :::image type="content" source="media/security-center-incident/locating-incidents.png" alt-text="Het vinden van de incidenten op de pagina waarschuwingen in Azure Security Center":::

    De lijst wordt nu gefilterd, zodat alleen incidenten worden weer gegeven. U ziet dat beveiligings incidenten een ander pictogram voor beveiligings waarschuwingen hebben.

    :::image type="content" source="media/security-center-incident/incidents-list.png" alt-text="Lijst met incidenten op de pagina waarschuwingen in Azure Security Center":::

1. Als u de details van een incident wilt weer geven, selecteert u er een in de lijst. Er wordt een zijvenster weer gegeven met meer informatie over het incident.

    :::image type="content" source="media/security-center-incident/incident-quick-peek.png" alt-text="Zijvenster met details van het incident":::

1. Selecteer **volledige details weer geven** om meer details weer te geven.

    [![Reageren op beveiligings incidenten in Azure Security Center](media/security-center-incident/incident-details.png)](media/security-center-incident/incident-details.png#lightbox)

    In het linkerdeel venster van de pagina beveiligings incident ziet u informatie op hoog niveau over het beveiligings incident: titel, Ernst, status, activiteit tijd, beschrijving en de betreffende resource. Naast de betreffende resource ziet u de relevante Azure-Tags. Gebruik deze tags om de organisatie context van de resource af te leiden wanneer de waarschuwing wordt onderzocht.

    Het rechterdeel venster bevat het tabblad **waarschuwingen** met de beveiligings waarschuwingen die zijn gecorreleerd als onderdeel van dit incident. 

    >[!TIP]
    > Voor meer informatie over een specifieke waarschuwing, selecteert u deze. 

    [![Tabblad actie ondernemen van incident](media/security-center-incident/incident-take-action-tab.png)](media/security-center-incident/incident-take-action-tab.png#lightbox)

    Als u wilt overschakelen naar het tabblad **actie ondernemen** , selecteert u het tabblad of de knop aan de onderkant van het rechterdeel venster. Gebruik dit tabblad om verdere acties uit te voeren, zoals:
    - *De dreiging beperken* : biedt hand matige herstel stappen voor dit beveiligings incident
    - *Toekomstige aanvallen voor komen* : bevat aanbevelingen voor beveiliging om de kwets baarheid te verminderen, de beveiliging postuur te verbeteren en toekomstige aanvallen te voor komen
    - *Automatische reactie activeren* : biedt de mogelijkheid om een logische app als reactie op dit beveiligings incident te activeren
    - *Vergelijk bare waarschuwingen onderdrukken* : biedt de mogelijkheid om toekomstige waarschuwingen met vergelijk bare kenmerken te onderdrukken als de waarschuwing niet relevant is voor uw organisatie 

   > [!NOTE]
   > Dezelfde waarschuwing kan bestaan als onderdeel van een incident, en als een zelfstandige waarschuwing worden weer gegeven.

1. Volg de herstels tappen bij elke waarschuwing om de bedreigingen in het incident op te lossen.


## <a name="next-steps"></a>Volgende stappen

Op deze pagina zijn de mogelijkheden van het beveiligings incident van Security Center uitgelegd. Zie de volgende pagina's voor verwante informatie:

- [Beveiligings waarschuwingen in Security Center](security-center-alerts-overview.md)
- [Beveiligingswaarschuwingen beheren en erop reageren](security-center-managing-and-responding-alerts.md)