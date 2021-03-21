---
title: Rapport-API ophalen
description: Gebruik deze API om analyse rapporten op te halen die zijn gepland in partner centrum.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 3383af447f40ea984bce9cbc956f22ee6c5af200
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583842"
---
# <a name="get-report-api"></a>Rapport-API ophalen

Deze API haalt alle rapporten op die zijn gepland.

**Syntaxis van aanvraag**

| **Methode** | **Aanvraag-URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport?reportId={Report ID}&reportName={Report Name}&queryId={Query ID} ` |

**Aanvraag header**

| **Header** | **Type** | **Beschrijving** |
| --- | --- | --- |
| Autorisatie | tekenreeks | Vereist. Het toegangs token Azure Active Directory (Azure AD) in de vorm `Bearer <token>` |
| Content-Type | tekenreeks | `Application/JSON` |

**Para meter Path**

Geen

**Query parameter**

| **Parameter naam** | **Vereist** | **Type** | **Beschrijving** |
| --- | --- | --- | --- |
| `reportId` | Nee | tekenreeks | Filteren om alleen details op te halen van rapporten met de `reportId` opgegeven in dit argument |
| `reportName` | Nee | tekenreeks | Filteren om alleen details op te halen van rapporten met de `reportName` opgegeven in dit argument |
| `queryId` | Nee | booleaans | Vooraf gedefinieerde systeem query's in het antwoord toevoegen |

**Woordenlijst**

Geen

**Response**

De nettolading van de reactie is als volgt gestructureerd in JSON-indeling:

Antwoord code: 200, 400, 401, 403, 404, 500

Nettolading van reactie:

```json
{
  "Value": [
    {
      "ReportId": "string",
      "ReportName": "string",
      "Description": "string",
      "QueryId": "string",
      "Query": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string",
      "StartTime": "string",
      "ReportStatus": "string",
      "RecurrenceInterval": 0,
      " RecurrenceCount": 0,
      "CallbackUrl": "string",
      "Format": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**Woordenlijst**

Deze tabel bevat de belangrijkste definities van elementen in het antwoord.

| **Parameter** | **Beschrijving** |
| --- | --- |
| `ReportId` | Unieke UUID van het rapport dat is gemaakt |
| `ReportName` | De naam die aan het rapport is gegeven in de aanvraag lading |
| `Description` | Beschrijving die is opgegeven bij het maken van het rapport |
| `QueryId` | Query-ID die is door gegeven op het moment dat het rapport is gemaakt |
| `Query` | Query tekst die voor dit rapport wordt uitgevoerd |
| `User` | Gebruikers-ID die is gebruikt om het rapport te maken |
| `CreatedTime` | Tijdstip waarop het rapport is gemaakt. De tijd notatie is JJJJ-MM-DDTuu: mm: ssZ |
| `ModifiedTime` | Tijdstip waarop het rapport voor het laatst is gewijzigd. De tijd notatie is JJJJ-MM-DDTuu: mm: ssZ |
| `StartTime` | De uitvoering van de tijd wordt gestart. De tijd notatie is JJJJ-MM-DDTuu: mm: ssZ |
| `ReportStatus` | De status van de rapport uitvoering. De mogelijke waarden worden onderbroken, actief en inactief. |
| `RecurrenceInterval` | Het interval voor het terugkeer patroon tijdens het maken van het rapport |
| `RecurrenceCount` | Aantal herhalingen dat is gegeven tijdens het maken van het rapport |
| `CallbackUrl` | Call back-URL die in de aanvraag is opgenomen |
| `Format` | Indeling van de rapport bestanden |
