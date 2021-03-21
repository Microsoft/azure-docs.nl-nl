---
title: Advisor-waarschuwingen voor metrische gegevens configureren
titleSuffix: Azure Cognitive Services
description: De Advisor-waarschuwingen voor metrische gegevens configureren met behulp van hooks voor e-mail, Web en Azure DevOps.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: mbullwin
ms.openlocfilehash: 30d8fdf99da7a4854db0985bed6256ecd6f7a366
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420917"
---
# <a name="how-to-configure-alerts-and-get-notifications-using-a-hook"></a>Instructies: waarschuwingen configureren en meldingen ontvangen met behulp van een hook

Nadat een afwijkend is gedetecteerd door de para meters Advisor, wordt een waarschuwings melding geactiveerd op basis van waarschuwings instellingen, met behulp van een hook. Een waarschuwings instelling kan worden gebruikt met meerdere detectie configuraties. verschillende para meters zijn beschikbaar om uw waarschuwings regel aan te passen.

## <a name="create-a-hook"></a>Een hook maken

De metrische gegevens adviseur ondersteunt drie verschillende typen hooks: e-mail Hook, webhook en Azure DevOps. U kunt de naam kiezen die geschikt is voor uw specifieke scenario.       

### <a name="email-hook"></a>E-mail Hook

> [!Note]
> De metrische gegevens van de Advisor-resource beheerder moeten de e-mail instellingen configureren en met behulp van de informatie over het invoeren van SMTP-berichten in de Advisor-adviseur voordat afwijkende waarschuwingen kunnen worden verzonden. De beheerder van de resource groep of het abonnement moet ten minste één Cognitive Services-rol voor de functie voor *metrische gegevens adviseur* toewijzen op het tabblad toegangs beheer van de functie voor metrische gegevens van de Advisor. [Meer informatie over de configuratie van e-mail instellingen](../faq.md#how-to-set-up-email-settings-and-enable-alerting-by-email).

Als u een e-mail Hook wilt maken, zijn de volgende para meters beschikbaar: 

Een e-mail Hook is het kanaal voor afwijkende waarschuwingen die moeten worden verzonden naar e-mail adressen die zijn opgegeven in de sectie **e-mail naar** . Er worden twee typen waarschuwings-e-mail berichten verzonden: *gegevensfeed niet-beschik bare* waarschuwingen en *incident rapporten* die een of meer afwijkingen bevatten. 

|Parameter |Beschrijving  |
|---------|---------|
| Name | De naam van de e-mail Hook |
| E-mail verzenden naar| E-mail adressen waarnaar een waarschuwing wordt verzonden|
| Externe koppeling | Optioneel veld dat een aangepaste omleiding mogelijk maakt, zoals voor het oplossen van problemen. |
| Titel van aangepaste afwijkings waarschuwing | Titel sjabloon ondersteunt `${severity}` , `${alertSettingName}` , `${datafeedName}` , `${metricName}` , `${detectConfigName}` , `${timestamp}` , `${topDimension}` , `${incidentCount}` , `${anomalyCount}`

Nadat u op **OK** hebt geklikt, wordt er een e-mail Hook gemaakt. U kunt deze gebruiken in alle waarschuwings instellingen om afwijkende waarschuwingen te ontvangen. 

### <a name="web-hook"></a>Webhook

> [!Note]
> * Gebruik de **post** -aanvraag methode.
> * De aanvraag tekst wordt is vergelijkbaar met:  
    `{"timestamp":"2019-09-11T00:00:00Z","alertSettingGuid":"49635104-1234-4c1c-b94a-744fc920a9eb"}`
> * Wanneer een webhook wordt gemaakt of gewijzigd, wordt de API aangeroepen als een test met een lege aanvraag tekst. Uw API moet een 200 HTTP-code retour neren.

Een webhook is het toegangs punt voor alle informatie die beschikbaar is via de service Metrics Advisor en roept een door de gebruiker opgegeven API aan wanneer een waarschuwing wordt geactiveerd. Alle waarschuwingen kunnen worden verzonden via een webhook.

Als u een webhook wilt maken, moet u de volgende informatie toevoegen:

|Parameter |Beschrijving  |
|---------|---------|
|Eindpunt     | Het API-adres dat moet worden aangeroepen wanneer een waarschuwing wordt geactiveerd.        |
|Gebruikersnaam en wachtwoord | Voor verificatie bij het API-adres. Houd deze zwart als verificatie niet nodig is.         |
|Header     | Aangepaste headers in de API-aanroep.        |

:::image type="content" source="../media/alerts/create-web-hook.png" alt-text="venster voor het maken van webhooks.":::

Wanneer een melding via een webhook wordt gepusht, kunt u de volgende Api's gebruiken om details van de waarschuwing te verkrijgen. Stel de *tijds tempel* -en *ALERTSETTINGGUID* in uw API-service in, waarnaar wordt gepusht en gebruik vervolgens de volgende query's: 
- `query_alert_result_anomalies`
- `query_alert_result_incidents`

### <a name="azure-devops"></a>Azure DevOps

Met metrische gegevens adviseur wordt ook automatisch het maken van een werk item in azure DevOps ondersteund voor het bijhouden van problemen/bugs wanneer er afwijkingen worden gedetecteerd. Alle waarschuwingen kunnen worden verzonden via Azure DevOps hooks.

Als u een Azure DevOps-Hook wilt maken, moet u de volgende gegevens toevoegen

|Parameter |Beschrijving  |
|---------|---------|
| Name | Een naam voor de Hook |
| Organisatie | De organisatie waartoe uw DevOps behoort |
| Project | Het specifieke project in DevOps. |
| Toegangstoken |  Een token voor de verificatie van DevOps. | 

> [!Note]
> U moet schrijf machtigingen verlenen als u metrische gegevens adviseur werk items wilt laten maken op basis van afwijkings waarschuwingen. Nadat u hooks hebt gemaakt, kunt u deze gebruiken in uw waarschuwings instellingen. Beheer uw hooks op de pagina **Hook-instellingen** .

## <a name="add-or-edit-alert-settings"></a>Waarschuwings instellingen toevoegen of bewerken

Ga naar de detail pagina met metrische gegevens om de sectie **waarschuwings instellingen** te vinden in de linkerbenedenhoek van de detail pagina met metrische gegevens. Hiermee worden alle waarschuwings instellingen weer gegeven die van toepassing zijn op de geselecteerde detectie configuratie. Wanneer er een nieuwe detectie configuratie wordt gemaakt, wordt er geen instelling voor waarschuwingen weer gegeven en worden er geen waarschuwingen verzonden.  
U kunt de pictogrammen **toevoegen**, **bewerken** en **verwijderen** gebruiken om de instellingen voor waarschuwingen te wijzigen.

:::image type="content" source="../media/alerts/alert-setting.png" alt-text="Menu-item waarschuwings instellingen.":::

Klik op de knoppen **toevoegen** of **bewerken** om een venster te openen om de instellingen voor waarschuwingen toe te voegen of te bewerken.

:::image type="content" source="../media/alerts/edit-alert.png" alt-text="Waarschuwings instellingen toevoegen of bewerken":::

**Naam van waarschuwings instelling**: de naam van deze waarschuwings instelling. Deze wordt weer gegeven in de titel van het e-mail bericht.

**Hooks**: de lijst met hooks voor het verzenden van waarschuwingen naar.

De sectie die in de bovenstaande scherm afbeelding is gemarkeerd, zijn de instellingen voor een detectie configuratie. U kunt verschillende waarschuwings instellingen instellen voor verschillende detectie configuraties. Kies de doel configuratie met behulp van de derde vervolg keuzelijst in dit venster. 

### <a name="filter-settings"></a>Filterinstellingen 

Hieronder ziet u de filter instellingen voor één detectie configuratie.

**Waarschuwing voor** heeft 4 opties voor het filteren van afwijkingen:

* **Afwijkingen in alle reeksen**: alle afwijkingen worden opgenomen in de waarschuwing.         
* **Afwijkingen in de reeks groep**: filter reeksen per dimensie waarden. Specifieke waarden voor bepaalde dimensies instellen. Afwijkingen worden alleen opgenomen in de waarschuwing wanneer de reeks overeenkomt met de opgegeven waarde.       
* **Afwijkingen in de reeks favorieten**: alleen de reeks die als favoriet is gemarkeerd, wordt opgenomen in de waarschuwing.        |
* **Afwijkingen in de bovenste n van alle reeksen**: dit filter is voor het geval dat u alleen de bezorgt van de serie waarvan de waarde zich in de bovenste n bevindt. Enkele tijds tempels worden teruggestuurd en er wordt gecontroleerd of de waarde van de reeks bij deze tijds tempel in de bovenste N ligt. Als het aantal in de bovenste n groter is dan het opgegeven aantal, wordt de afwijkende waarde opgenomen in een waarschuwing.        |

**Opties voor afwijkingen filteren** is een extra filter met de volgende opties:

- **Ernst** : de afwijkingen worden alleen opgenomen wanneer de ernst van de afwijkende waarde binnen het opgegeven bereik ligt.
- **Uitstellen** : waarschuwingen tijdelijk stoppen voor afwijkingen in de volgende N punten (punt), indien geactiveerd in een waarschuwing.
    - uitstel **type** : wanneer u deze instelt op **reeks**, wordt de reeks alleen uitgesteld met een geactiveerde afwijkings actie. Voor **metrische gegevens** wordt met één geactiveerde afwijking alle reeksen in deze metriek uitgesteld.
    - uitstel **nummer** : het aantal punten (punt) dat moet worden uitgesteld.
    - **opnieuw instellen voor niet-opeenvolgende** : wanneer dit selectie vakje is ingeschakeld, worden alleen de volgende n opeenvolgende afwijkingen door gegenereerd. Als een van de volgende gegevens punten geen afwijkingen is, wordt de uitstel instelling vanaf dat punt opnieuw ingesteld. Als u dit selectie vakje uitschakelt, worden de volgende n punten (punt) uitgesteld, zelfs als opeenvolgende gegevens punten geen afwijkingen hebben.
- **waarde** (optioneel): filteren op waarde. Alleen punt waarden die voldoen aan de voor waarde, worden afwijkend opgenomen. Als u de bijbehorende waarde van een andere metriek gebruikt, moeten de dimensie namen van de twee metrieken consistent zijn.

Afwijkingen die niet worden uitgefilterd, worden in een waarschuwing verzonden.

### <a name="add-cross-metric-settings"></a>Instellingen voor meerdere metrieken toevoegen

Klik op **+ instellingen voor kruis metrieken toevoegen** op de pagina waarschuwings instellingen om een andere sectie toe te voegen.

De **operator** kiezer is de logische relatie van elke sectie om te bepalen of er een waarschuwing wordt verzonden.


|Operator  |Beschrijving  |
|---------|---------|
|AND     | Alleen een waarschuwing verzenden als een reeks overeenkomt met elke waarschuwings sectie en alle gegevens punten afwijkingen zijn. Als de metrische gegevens verschillende dimensie namen hebben, wordt er nooit een waarschuwing geactiveerd.         |
|OF     | De waarschuwing verzenden als ten minste één sectie afwijkingen bevat.         |

:::image type="content" source="../media/alerts/alert-setting-operator.png" alt-text="De operator voor de sectie meerdere waarschuwings instellingen":::

## <a name="next-steps"></a>Volgende stappen

- [Anomaliedetectie aanpassen met feedback](anomaly-feedback.md)
- [Een incident diagnosticeren](diagnose-incident.md).
- [Metrische gegevens configureren en detectieconfiguratie afstemmen](configure-metrics.md)