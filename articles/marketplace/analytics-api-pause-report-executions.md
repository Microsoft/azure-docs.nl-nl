---
title: De API voor rapport uitvoeringen onderbreken
description: Gebruik deze API om de geplande uitvoering van een rapport van een commerciële Marketplace-analyse te onderbreken.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 39b535278fef42818f572631cfa1cb1f923930a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583835"
---
# <a name="pause-report-executions-api"></a>De API voor rapport uitvoeringen onderbreken

Tijdens de uitvoering van deze API wordt de geplande uitvoering van-rapporten onderbroken.

**Syntaxis van aanvraag**

| Methode | Aanvraag-URI |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/pause/{Report ID}` |
|||

**Aanvraag header**

| Header | Type | Description |
| ------------ | ------------- | ------------- |
| Autorisatie | tekenreeks | Vereist. Het toegangs token Azure Active Directory (Azure AD) in de vorm `Bearer <token>` |
| Content-Type | tekenreeks | `Application/JSON` |
||||

**Para meter Path**

Geen

**Queryparameter**

| Parameternaam | Vereist | Type | Beschrijving |
| ------------ | ------------- | ------------- | ------------- |
| `reportId` | Ja | tekenreeks | ID van het rapport dat wordt gewijzigd |
|||||

**Woordenlijst**

Geen

**Response**

De nettolading van de reactie is als volgt gestructureerd in JSON-indeling:

Antwoord code: 200, 400, 401, 403, 404, 500-antwoord Payload:

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
| `ReportId` | UUID (Universally Unique Identifier) van het verwijderde rapport |
| `ReportName` | De naam die tijdens het maken van het rapport is gegeven |
| `Description` | Beschrijving gegeven tijdens het maken van het rapport |
| `QueryId` | Query-ID die is door gegeven op het moment dat het rapport is gemaakt |
| `Query` | Query tekst die voor dit rapport wordt uitgevoerd |
| `User` | Gebruikers-ID die is gebruikt voor het maken van het rapport |
| `CreatedTime` | Tijdstip waarop het rapport is gemaakt. De tijd notatie is JJJJ-MM-DDTuu: mm: ssZ |
| `ModifiedTime` | Tijdstip waarop het rapport voor het laatst is gewijzigd. De tijd notatie is JJJJ-MM-DDTuu: mm: ssZ |
| `StartTime` | Tijd dat de uitvoering van het rapport begint. De tijd notatie is JJJJ-MM-DDTuu: mm: ssZ |
| `ReportStatus` | De status van de rapport uitvoering. De mogelijke waarden worden onderbroken, actief en inactief. |
| `RecurrenceInterval` | Het interval voor het terugkeer patroon tijdens het maken van het rapport |
| `RecurrenceCount` | Aantal herhalingen dat is gegeven tijdens het maken van het rapport |
| `CallbackUrl` | Call back-URL die in de aanvraag is opgenomen |
| `Format` | Indeling van de rapport bestanden |
|||
