---
title: Capaciteit van een Azure API Management-exemplaar | Microsoft Docs
description: In dit artikel wordt uitgelegd wat de capaciteits metriek is en hoe u weloverwogen beslissingen kunt nemen, of u een Azure API Management-exemplaar wilt schalen.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/18/2018
ms.author: apimpm
ms.custom: fasttrack-edit
ms.openlocfilehash: c7c002b57f2220ac0a9fba43a8081b2a4ed800e7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93081056"
---
# <a name="capacity-of-an-azure-api-management-instance"></a>Capaciteit van een Azure API Management-exemplaar

**Capaciteit** is de belangrijkste [Azure monitor meet waarde](api-management-howto-use-azure-monitor.md#view-metrics-of-your-apis) voor het nemen van gefundeerde beslissingen of het schalen van een API Management instantie voor meer belasting. De constructie is complex en legt een bepaald gedrag voor.

In dit artikel wordt uitgelegd wat de **capaciteit** is en hoe deze zich gedraagt. Hier ziet u hoe u de metrische gegevens van de **capaciteit** kunt openen in de Azure Portal en wordt voorgesteld wanneer u uw API Management exemplaar kunt schalen of bijwerken.

> [!IMPORTANT]
> In dit artikel wordt beschreven hoe u uw Azure API Management-exemplaar kunt controleren en schalen op basis van de metrische gegevens van de capaciteit. Het is echter even belang rijk om te begrijpen wat er gebeurt wanneer een afzonderlijke API Management instantie de capaciteit daad werkelijk heeft *bereikt* . In azure API Management wordt geen beperking op service niveau toegepast om te voor komen dat de instanties fysiek worden overbelast. Wanneer een exemplaar de fysieke capaciteit bereikt, zal dit vergelijkbaar zijn met alle overbelaste webservers die geen binnenkomende aanvragen kunnen verwerken: de latentie neemt toe, verbindingen worden verbroken, er worden time-outfouten gegenereerd, enzovoort. Dit betekent dat API-clients moeten worden voor bereid om te kunnen omgaan met deze mogelijkheid, vergelijkbaar met andere externe services (bijvoorbeeld door beleid voor opnieuw proberen toe te passen).

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt volgen, hebt u het volgende nodig:

+ Een actief Azure-abonnement.

    [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

+ Een APIM-exemplaar. Zie [een Azure API Management-exemplaar maken](get-started-create-service-instance.md)voor meer informatie.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="what-is-capacity"></a>Wat is capaciteit?

![Diagram waarin de metrische gegevens van de capaciteit worden uitgelegd.](./media/api-management-capacity/capacity-ingredients.png)

**Capaciteit** is een belasting indicator voor een API Management exemplaar. Het weerspiegelt het bronnen gebruik (CPU, geheugen) en de lengte van de netwerk wachtrij. Met het CPU-en geheugen gebruik worden bronnen verbruikt door:

+ API Management data vlak-Services, zoals aanvraag verwerking, waarmee doorstuur aanvragen kunnen worden toegevoegd of een beleid kan worden uitgevoerd.
+ Services voor API Management beheer, zoals beheer acties die via de Azure-portal of ARM worden toegepast, of de belasting die afkomstig is van de [ontwikkelaars Portal](api-management-howto-developer-portal.md).
+ Geselecteerde besturingssysteem processen, met inbegrip van processen waarbij de kosten van TLS-Handshakes voor nieuwe verbindingen betrokken zijn.

Totale **capaciteit** is een gemiddelde van de eigen waarden van elke eenheid van een API Management-exemplaar.

Hoewel de **capaciteits metriek** is ontworpen om problemen met uw API Management-exemplaar op te sporen, zijn er gevallen waarin problemen niet worden weer gegeven in wijzigingen in de **capaciteits metriek**.

## <a name="capacity-metric-behavior"></a>Gedrag van capaciteits metriek

Als gevolg van de constructie kan de **capaciteit** van het werkelijke leven worden beïnvloed door veel variabelen, bijvoorbeeld:

+ verbindings patronen (nieuwe verbinding op basis van een aanvraag en de bestaande verbinding opnieuw gebruiken)
+ grootte van een aanvraag en antwoord
+ beleids regels die zijn geconfigureerd op elke API of het aantal clients dat aanvragen verzendt.

De complexere bewerkingen voor de aanvragen zijn, des te hoger het **capaciteits** verbruik is. Complexe transformatie beleidsregels gebruiken bijvoorbeeld veel meer CPU dan een eenvoudige aanvraag door sturen. Reacties van trage back-end-service worden ook verhoogd.

> [!IMPORTANT]
> De **capaciteit** is geen directe meting van het aantal aanvragen dat wordt verwerkt.

![Pieken bij capaciteits metrieken](./media/api-management-capacity/capacity-spikes.png)

**Capaciteit** kan ook af en toe gelijk zijn aan een waarde die groter is dan nul, zelfs als er geen aanvragen worden verwerkt. Dit gebeurt vanwege systeem-of platformspecifieke acties en moet niet in aanmerking worden genomen bij het bepalen of u een instantie wilt schalen.

De **metriek** voor weinig capaciteit betekent niet noodzakelijkerwijs dat uw API Management-exemplaar geen problemen ondervindt.
  
## <a name="use-the-azure-portal-to-examine-capacity"></a>De Azure Portal gebruiken om de capaciteit te controleren
  
![Metrische capaciteitswaarde](./media/api-management-capacity/capacity-metric.png)  

1. Navigeer naar uw APIM-instantie in de [Azure Portal](https://portal.azure.com/).
2. Selecteer **Metrische gegevens**.
3. Selecteer in de sectie paars de optie **capaciteits** metriek van beschik bare metrische gegevens en behoud de standaard **Gem** aggregatie.

    > [!TIP]
    > U moet altijd een **capaciteits** metriek op locatie bekijken om onjuiste interpretaties te voor komen.

4. Selecteer in het groene gedeelte **locatie** voor het splitsen van de metriek per dimensie.
5. Kies een gewenste periode in de bovenste balk van de sectie.

    U kunt een waarschuwing instellen om u te laten weten wanneer er iets onverwachts plaatsvindt. Ontvang bijvoorbeeld meldingen wanneer uw APIM-exemplaar de verwachte piek capaciteit van meer dan 20 minuten overschrijdt.

    >[!TIP]
    > U kunt waarschuwingen configureren om u op de hoogte te stellen wanneer uw service weinig capaciteit heeft of Azure Monitor functionaliteit voor automatisch schalen gebruiken om een Azure API Management-eenheid toe te voegen. De schaal bewerking kan ongeveer 30 minuten duren, dus u moet uw regels dienovereenkomstig plannen.  
    > De hoofd locatie mag alleen worden geschaald.

## <a name="use-capacity-for-scaling-decisions"></a>Capaciteit voor het schalen van beslissingen gebruiken

**Capaciteit** is de metrische gegevens voor het nemen van beslissingen of een API Management-exemplaar moet worden geschaald om meer belasting te bieden. Overweeg het volgende:

+ Een lange termijn trend en gemiddeld te bekijken.
+ Het negeren van plotselinge pieken die waarschijnlijk niet zijn gerelateerd aan een toename van de belasting (Zie ' gedrag van capaciteits metriek ' voor uitleg).
+ Het bijwerken of schalen van uw exemplaar, wanneer de waarde van de **capaciteit** groter is dan 60% of 70% gedurende een langere periode (bijvoorbeeld 30 minuten). Verschillende waarden kunnen beter werken voor uw service of scenario.

>[!TIP]  
> Als u uw verkeer vooraf kunt schatten, test u uw APIM-exemplaar op workloads die u verwacht. U kunt de belasting van de aanvraag op uw Tenant geleidelijk verhogen en controleren welke waarde de capaciteits metriek overeenkomt met uw piek belasting. Volg de stappen in de vorige sectie om Azure Portal te gebruiken om te begrijpen hoeveel capaciteit er op een bepaald moment wordt gebruikt.

## <a name="next-steps"></a>Volgende stappen

- [Een Azure API Management service-exemplaar schalen of bijwerken](upgrade-and-scale.md)
- [Optimaliseer en sla uw Cloud uitgaven op](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)