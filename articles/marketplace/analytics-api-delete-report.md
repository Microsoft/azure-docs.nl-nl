---
title: Rapport-API verwijderen
description: Gebruik deze API om alle rapport-en rapport uitvoerings records voor commerciële Marketplace Analytics-rapporten te verwijderen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7c39f8bc0db44f1d8aa885969ca09d90b0dcd332
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583879"
---
# <a name="delete-report-api"></a>Rapport-API verwijderen

Als deze API wordt uitgevoerd, worden alle rapporten en rapport uitvoerings records verwijderd.

**Syntaxis van aanvraag**

| Methode | Aanvraag-URI |
| ------------ | ------------- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
|||

**Aanvraag header**

| Header | Type | Beschrijving |
| ------------ | ------------- | ------------- |
| Autorisatie | tekenreeks | Vereist. Het Azure AD-toegangs token in de vorm `Bearer <token>` |
| Type inhoud | tekenreeks | `Application/JSON` |
||||

**Para meter Path**

Geen

**Query parameter**

| Parameternaam | Vereist | tekenreeks | Beschrijving |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Ja | tekenreeks | ID van het rapport dat wordt gewijzigd |
|||||

**Woordenlijst**

Geen

**Response**

De nettolading van de reactie is als volgt gestructureerd:

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
      "RecurrenceCount": 0,
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

| Parameter | Beschrijving |
| ------------ | ------------- |
| `ReportId` | Unieke UUID van het verwijderde rapport |
| `ReportName` | De naam die tijdens het maken van het rapport is gegeven |
| `Description` | Beschrijving gegeven tijdens het maken van het rapport |
| `QueryId` | Query-ID die is door gegeven op het moment dat het rapport is gemaakt |
| `Query` | Query tekst die voor dit rapport wordt uitgevoerd |
| `User` | Gebruikers-ID die is gebruikt om het rapport te maken |
| `CreatedTime` | Tijdstip waarop het rapport is gemaakt. De tijd notatie is JJJJ-MM-DDTuu: mm: ssZ |
| `ModifiedTime` | Tijdstip waarop het rapport voor het laatst is gewijzigd. De tijd notatie is JJJJ-MM-DDTuu: mm: ssZ |
| `StartTime` | Tijd dat de uitvoering van het rapport begint. De tijd notatie is JJJJ-MM-DDTuu: mm: ssZ |
| `ReportStatus` | Status van rapport uitvoering. De mogelijke waarden worden onderbroken, actief en inactief. |
| `RecurrenceInterval` | Het interval voor het terugkeer patroon tijdens het maken van het rapport |
| `RecurrenceCount` | Aantal herhalingen dat is gegeven tijdens het maken van het rapport |
| `CallbackUrl` | Call back-URL die in de aanvraag is opgenomen |
| `Format` | Indeling van de rapport bestanden |
|||
