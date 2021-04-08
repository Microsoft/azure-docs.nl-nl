---
title: API voor rapport uitvoeringen hervatten
description: Gebruik deze API om de geplande uitvoering van een onderbroken commerciële Marketplace-analyse rapport te hervatten.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4a11783b28352cb62c5a3c0d38e45dcdc47a8d86
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583799"
---
# <a name="resume-report-executions-api"></a>API voor rapport uitvoeringen hervatten

Tijdens de uitvoering van deze API wordt de geplande uitvoering van een onderbroken commerciële Marketplace-analyse rapport hervat.

**Syntaxis van aanvraag**

| Methode | Aanvraag-URI |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/resume/{reportId}` |
|||

**Aanvraag header**

| Header | Type | Description |
| ------------ | ------------- | ------------- |
| Autorisatie | tekenreeks | Vereist. Het toegangs token Azure Active Directory (Azure AD) in de vorm `Bearer <token>` |
| Content-Type | tekenreeks | `Application/JSON` |
||||

**Para meter Path**

Geen

**Query parameter**

| Parameternaam | Vereist | Type | Beschrijving |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Ja | tekenreeks | ID van het rapport dat wordt gewijzigd |
|||||

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
| `ReportId` | UUID (Universally Unique Identifier) van het gehervate rapport |
| `ReportName` | De naam die tijdens het maken van het rapport is gegeven |
| `Description` | Beschrijving gegeven tijdens het maken van het rapport |
| `QueryId` | Query-ID die is door gegeven op het moment dat het rapport is gemaakt |
| `Query` | Query tekst die voor dit rapport wordt uitgevoerd |
| `User` | Gebruikers-ID die is gebruikt voor het maken van het rapport |
| `CreatedTime` | Tijdstip waarop het rapport is gemaakt. De tijd notatie is JJJJ-MM-DDTuu: mm: ssZ |
| `ModifiedTime` | Tijdstip waarop het rapport voor het laatst is gewijzigd. De tijd notatie is JJJJ-MM-DDTuu: mm: ssZ |
| `StartTime` | Tijdstip waarop de uitvoering van het rapport begint. De tijd notatie is JJJJ-MM-DDTuu: mm: ssZ |
| `ReportStatus` | De status van de rapport uitvoering. De mogelijke waarden worden onderbroken, actief en inactief. |
| `RecurrenceInterval` | Het interval voor het terugkeer patroon tijdens het maken van het rapport |
| `RecurrenceCount` | Aantal herhalingen dat is gegeven tijdens het maken van het rapport |
| `CallbackUrl` | Call back-URL die in de aanvraag is opgenomen |
| `Format` | Indeling van de rapport bestanden |
|||
