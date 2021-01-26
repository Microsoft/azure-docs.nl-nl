---
title: Watchlists beheren in azure Sentinel met behulp van REST API | Microsoft Docs
description: In dit artikel wordt beschreven hoe Azure Sentinel Watchlists kan worden beheerd met behulp van Log Analytics REST API om Watchlists en hun items te maken, wijzigen en verwijderen.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: reference
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/11/2021
ms.author: yelevin
ms.openlocfilehash: ea571f9b033ba82709a13c6d32649f3228ee04b1
ms.sourcegitcommit: 95c2cbdd2582fa81d0bfe55edd32778ed31e0fe8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98798405"
---
# <a name="manage-watchlists-in-azure-sentinel-using-rest-api"></a>Watchlists in azure-Sentinel beheren met REST API

> [!IMPORTANT]
> De functie Watchlists is momenteel beschikbaar als **Preview-versie**. Zie de [aanvullende gebruiks voorwaarden voor Microsoft Azure previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor aanvullende juridische voor waarden die van toepassing zijn op Azure-functies die in bèta, preview of op andere wijze nog niet beschikbaar zijn in algemene Beschik baarheid.

Met Azure Sentinel, die deel uitmaakt van Azure Monitor Log Analytics, kunt u Log Analytics REST API gebruiken om Watchlists te beheren. In dit document wordt beschreven hoe u Watchlists en hun items kunt maken, wijzigen en verwijderen met behulp van de REST API.  Watchlists die op deze manier zijn gemaakt, worden weer gegeven in de Azure Sentinel-gebruikers interface.

## <a name="common-uri-parameters"></a>Algemene URI-para meters

Hieronder vindt u de algemene URI-para meters voor alle watch list-API-opdrachten:

| Naam | In | Vereist | Type | Beschrijving |
|-|-|-|-|-|
| **Abonnements** | leertraject | ja | GUID | de ID van het Azure-abonnement |
| **ResourceGroupName** | leertraject | ja | tekenreeks | de naam van de resource groep binnen het abonnement |
| **WorkspaceName** | leertraject | ja | tekenreeks | de naam van de Log Analytics-werk ruimte |
| **{watchlistAlias}** | leertraject | klikt | tekenreeks | de naam van een bepaalde watch list<br>\* Niet vereist bij het ophalen van alle Watchlists |
| **{watchlistItemId}** | leertraject | Ja * * | GUID | de ID van het item dat u wilt maken in, toe te voegen aan of te verwijderen uit de watch list<br>\*\* Alleen vereist voor watch list-item opdrachten |
| **{API-versie}** | query | ja | tekenreeks | de versie van het protocol dat wordt gebruikt om deze aanvraag uit te voeren. Vanaf 29 oktober 2020 is de API-versie van watch list *2019-01-01-preview* |
| **{bearerToken}** | autorisatie | ja | token | het Bearer-autorisatie token |
|  

## <a name="retrieve-all-watchlists"></a>Alle Watchlists ophalen

Met deze opdracht worden alle Watchlists opgehaald die zijn gekoppeld aan een werk ruimte, zonder hun items.

### <a name="request-uri"></a>Aanvraag-URI
(URI is een enkele regel, opgesplitst voor een eenvoudige Lees baarheid)

| Methode | Aanvraag-URI |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists?api-version={{api-version}}` |
|

### <a name="responses"></a>Antwoorden

| Statuscode | Hoofdtekst van de reactie | Beschrijving |
|-|-|-|
| 200/OK | Lijst met bestaande Watchlists of leeg als er geen watch list gevonden |  |
| 400/ongeldige aanvraag |  | Syntaxis van onjuiste aanvraag, ongeldige aanvraag parameter... |
|

## <a name="look-up-a-watchlist-by-name"></a>Een watch list op naam opzoeken

Met deze opdracht wordt een specifiek watch list opgehaald dat is gekoppeld aan een werk ruimte, zonder items.

### <a name="request-uri"></a>Aanvraag-URI
(URI is een enkele regel, opgesplitst voor een eenvoudige Lees baarheid)

| Methode | Aanvraag-URI |
|-|-|
| GET | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Antwoorden

| Statuscode | Hoofdtekst van de reactie | Beschrijving |
|-|-|-|
| 200/OK | De aangevraagde watch list |  |
| 400/ongeldige aanvraag |  | Syntaxis van onjuiste aanvraag, ongeldige aanvraag parameter... |
| 404/niet gevonden |  | Geen watch list gevonden met de aangevraagde naam |
|

## <a name="create-a-watchlist"></a>Een watch list maken

Met deze opdracht maakt u een watch list en voegt u hieraan items toe. Er zijn twee aanroepen naar dit eind punt om de watch list en de bijbehorende items te maken: de eerste maakt de watch list (Parent) en de tweede voegt de items toe.

### <a name="request-uri"></a>Aanvraag-URI
(URI is een enkele regel, opgesplitst voor een eenvoudige Lees baarheid)

| Methode | Aanvraag-URI |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>Aanvraagbody

Hier volgt een voor beeld van de aanvraag tekst van een aanvraag voor het maken van een watch list:

```json
{
    "properties": {
        "displayName": "High Value Assets Watchlist",
        "source": "Local file",
        "provider": "Microsoft",
        "numberOfLinesToSkip":"1",
        "rawContent": "metadata line\nheader1, header2\nval1, val2",
        "contentType": "text/csv"
    }
}
```

### <a name="responses"></a>Antwoorden

| Statuscode | Hoofdtekst van de reactie | Beschrijving |
|-|-|-|
| 200/OK | De watch list die door de aanvraag is gemaakt, zonder items |  |
| 400/ongeldige aanvraag |  | Syntaxis van onjuiste aanvraag, ongeldige aanvraag parameter... |
| 409/conflict |  | De bewerking is mislukt. er is een bestaande watch list met dezelfde alias |
|

## <a name="delete-a-watchlist"></a>Een watch list verwijderen

Met deze opdracht worden een watch list en de bijbehorende items verwijderd.

### <a name="request-uri"></a>Aanvraag-URI
(URI is een enkele regel, opgesplitst voor een eenvoudige Lees baarheid)

| Methode | Aanvraag-URI |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Antwoorden

| Statuscode | Hoofdtekst van de reactie | Beschrijving |
|-|-|-|
| 200/OK | Lege antwoord tekst |  |
| 204/geen inhoud | Lege antwoord tekst | Niets verwijderd |
| 400/ongeldige aanvraag |  | Syntaxis van onjuiste aanvraag, ongeldige aanvraag parameter... |
|

## <a name="add-or-update-a-watchlist-item"></a>Een watch list-item toevoegen of bijwerken

Wanneer deze opdracht wordt uitgevoerd op een bestaande *watchlisItemId* (een GUID), wordt het item bijgewerkt met de gegevens die zijn opgenomen in de hoofd tekst van de aanvraag. Anders wordt er een nieuw item gemaakt.

### <a name="request-uri"></a>Aanvraag-URI
(URI is een enkele regel, opgesplitst voor een eenvoudige Lees baarheid)

| Methode | Aanvraag-URI |
|-|-|
| PUT | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="request-body"></a>Aanvraagbody

Hier volgt een voor beeld van de aanvraag tekst van een watch list-item voor het toevoegen/bijwerken van aanvragen:

```json
{
    "properties": {
      "itemsKeyValue": {
           "Gateway subnet": "10.0.255.224/27",
           "Web Tier": "10.0.1.0/24",
           "Business tier": "10.0.2.0/24",
           "Private DMZ in": "10.0.0.0/27",
           "Public DMZ out": "10.0.0.96/27"
      }
}
}
```

### <a name="responses"></a>Antwoorden

| Statuscode | Hoofdtekst van de reactie | Beschrijving |
|-|-|-|
| 200/OK | Het watch list-item dat door de aanvraag is gemaakt of bijgewerkt |  |
| 400/ongeldige aanvraag |  | Syntaxis van onjuiste aanvraag, ongeldige aanvraag parameter... |
| 409/conflict |  | De bewerking is mislukt. er is een bestaande watch list met dezelfde alias |
|

## <a name="delete-a-watchlist-item"></a>Een watch list-item verwijderen

Met deze opdracht wordt een bestaand watch list-item verwijderd.

### <a name="request-uri"></a>Aanvraag-URI
(URI is een enkele regel, opgesplitst voor een eenvoudige Lees baarheid)

| Methode | Aanvraag-URI |
|-|-|
| DELETE | `https://management.azure.com/subscriptions/{{subscriptionId}}/`<br>`resourceGroups/{{resourceGroupName}}/`<br>`providers/Microsoft.OperationalInsights/`<br>`workspaces/{{workspaceName}}/`<br>`providers/Microsoft.SecurityInsights/`<br>`watchlists/{{watchlistAlias}}/`<br>`watchlistitems/{{watchlistItemId}}?api-version={{api-version}}` |
|

### <a name="responses"></a>Antwoorden

| Statuscode | Hoofdtekst van de reactie | Beschrijving |
|-|-|-|
| 200/OK | Lege antwoord tekst |  |
| 204/geen inhoud | Lege antwoord tekst | Niets verwijderd |
| 400/ongeldige aanvraag |  | Syntaxis van onjuiste aanvraag, ongeldige aanvraag parameter... |
|

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u Watchlists en de bijbehorende items in azure Sentinel beheert met behulp van de Log Analytics-API. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over [Watchlists](watchlists.md)
- Verken andere toepassingen van de [Azure Sentinel API](/rest/api/securityinsights/)