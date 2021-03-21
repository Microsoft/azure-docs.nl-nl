---
title: Programmatische toegang tot veelgestelde vragen over analyse gegevens
description: Veelgestelde vragen over programmatisch toegang tot Analytics-gegevens in het partner centrum voor uw commerciële Marketplace-aanbiedingen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 393a718632138f4ffcf26e4875eea9ba3d886897
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583811"
---
# <a name="programmatic-access-of-analytics-data-common-questions"></a>Programmatische toegang tot veelgestelde vragen over analyse gegevens

In dit artikel worden veelgestelde vragen behandeld over hoe u via een programma toegang hebt tot Analytics-gegevens in het partner centrum voor uw commerciële Marketplace-aanbiedingen.

## <a name="api-responses"></a>API-antwoorden

Wat zijn de verschillende scenario's waaronder ik een API-antwoord kan ontvangen dat niet 200 (geslaagd) is?

In deze tabel worden de API-antwoorden en wat u moet doen als u deze ontvangt.

| Foutbeschrijving | Foutcode | Problemen oplossen |
| ------------ | ------------- | ------------- |
| Niet geautoriseerd | 401 | Dit is een verificatie-uitzonde ring. Controleer de juistheid van het token van de Azure Active Directory (Azure AD). Het Azure AD-token is 60 minuten geldig, waarna u de Azure AD-token opnieuw moet genereren. |
| Ongeldige tabel naam | 400 | De naam van de gegevensset is onjuist. Controleer de naam van de gegevensset opnieuw door de API ' alle gegevens sets ophalen ' aan te roepen. |
| Onjuiste kolom naam | 400| De naam van de kolom in de query is onjuist. Controleer de naam van de kolom opnieuw door de API ' alle gegevens sets ophalen ' aan te roepen of Raadpleeg de kolom namen in de volgende tabellen:<br><ul><li>[Tabel met Order Details](orders-dashboard.md#orders-details-table)</li><li>[Tabel gebruiks gegevens](usage-dashboard.md#usage-details-table)</li><li>[Tabel Klant gegevens](customer-dashboard.md#customer-details-table)</li><li>[Informatie tabel voor Marketplace Insights](insights-dashboard.md#marketplace-insights-details-table)</li></UL> |
| Null of ontbrekende waarde | 400 | Mogelijk ontbreken verplichte para meters als onderdeel van de aanvraag lading van de API. |
| Ongeldige rapport parameters | 400 | Zorg ervoor dat de rapport parameters juist zijn. U kunt bijvoorbeeld een waarde van minder dan 4 opgeven voor de `RecurrenceInterval` para meter. |
| Het interval voor het terugkeer patroon moet tussen 4 en 90 | 400 | Zorg ervoor dat de waarde van de `RecurrenceInterval` aanvraag parameter tussen 4 en 90 ligt. |
| Ongeldige QueryId | 400 | Controleer de gegenereerde `QueryId` . |
| Ongeldige rapport parameters voor maken-start tijd van rapport moet minstens 4 uur zijn vanaf de huidige UTC-tijd- | 400 | De para meter start tijd als onderdeel van de aanvraag lading mag zich niet in het verleden bevallen. De begin tijd van het rapport moet ten minste vier uur vanaf de huidige UTC-tijd zijn. |
| De aangevraagde waarde ' String ' is niet gevonden | 400 | Controleer of u de aanvraag parameters of hebt `callbackurl` bijgewerkt `format` . |
| Er is geen item gevonden met de opgegeven filters. | 404 | Controleer de `reportID` para meter die wordt gebruikt in de API rapport uitvoeringen ophalen. |
| Er zijn geen uitvoeringen die hebben plaatsgevonden voor de opgegeven filter voorwaarden. Controleer de reportId of Executionid is vereist en voer de API opnieuw uit na de geplande uitvoerings tijd van het rapport | 404 | Zorg ervoor dat de `reportId` juist is. Voer de API opnieuw uit nadat de geplande uitvoerings tijd van het rapport is opgegeven in de aanvraag lading. |
| Er is een interne fout opgetreden tijdens het maken van het rapport. Correlatie-ID <> | 500 | Zorg ervoor dat de datum notatie voor de velden StartTime, QueryStartTime en QueryEndTime juist is. |
| Service niet beschikbaar | 500 | Als u voortdurend een service ontvangt die niet beschikbaar is (5xx-fout), moet u een [ondersteunings ticket](support.md)maken. |
||||

## <a name="no-records"></a>Geen records

Ik ontvang API-antwoord 200 wanneer ik het rapport van de veilige locatie down load. Waarom krijg ik geen records?

Controleer of de teken reeks in de query een van de toegestane waarden voor de kolomkop heeft. Met deze query worden bijvoorbeeld nul resultaten geretourneerd:

`"SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = 'Paided' ORDER BY UsageDate DESC TIMESPAN LAST_MONTH"`

In dit voor beeld worden de toegestane waarden voor SKUBillingType betaald of gratis. Raadpleeg de volgende tabellen voor alle mogelijke waarden voor de verschillende kolommen:

- [Tabel met Order Details](orders-dashboard.md#orders-details-table)
- [Tabel gebruiks gegevens](usage-dashboard.md#usage-details-table)
- [Tabel Klant gegevens](customer-dashboard.md#customer-details-table)
- [Informatie tabel voor Marketplace Insights](insights-dashboard.md#marketplace-insights-details-table)

## <a name="next-steps"></a>Volgende stappen

- [Api's voor het verkrijgen van toegang tot commerciële Marketplace Analytics-gegevens](analytics-available-apis.md)
