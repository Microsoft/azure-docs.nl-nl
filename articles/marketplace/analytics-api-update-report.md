---
title: Rapport-API bijwerken
description: Gebruik deze API om para meters voor analyse rapporten van commerciële markt plaatsen te rapporteren.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 38680eb291417ded4c2f93539e8d1ae091b1d560
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583793"
---
# <a name="update-report-api"></a>Rapport-API bijwerken

Met deze API kunt u een rapport parameter wijzigen.

**Syntaxis van aanvraag**

| Methode | Aanvraag-URI |
| ------------ | ------------- |
| PUT | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledReport/{Report ID}` |
|||

**Aanvraag header**

| Header | Type | Beschrijving |
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

**Payload aanvragen**

```json
{
  "ReportName": "string",
  "Description": "string",
  "StartTime": "string",
  "RecurrenceInterval": 0,
  "RecurrenceCount": 0,
  "Format": "string",
  "CallbackUrl": "string"
}
```

**Woordenlijst**

Deze tabel bevat de belangrijkste definities van elementen in de aanvraag lading.

| Parameter | Vereist | Beschrijving | Toegestane waarden |
| ------------ | ------------- | ------------- | ------------- |
| `ReportName` | Ja | Naam die aan het rapport moet worden toegewezen | tekenreeks |
| `Description` | No | Beschrijving van het gemaakte rapport | tekenreeks |
| `StartTime` | Ja | Tijds tempel waarna het genereren van het rapport wordt gestart | tekenreeks |
| `RecurrenceInterval` | No | De frequentie waarmee het rapport moet worden gegenereerd in uren. De minimum waarde is 4 | geheel getal |
| `RecurrenceCount` | Nee | Aantal rapporten dat moet worden gegenereerd. Standaard waarde is oneindig | geheel getal |
| `Format` | Ja | Bestands indeling van het geëxporteerde bestand. De standaard waarde is CSV. | CSV/TSV |
| `CallbackUrl` | Ja | https call back-URL die moet worden aangeroepen bij het genereren van rapporten | tekenreeks |
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
| `ReportId` | UUID (Universally Unique Identifier) van het rapport dat wordt bijgewerkt |
| `ReportName` | De naam die aan het rapport is gegeven in de aanvraag lading |
| `Description` | Beschrijving gegeven tijdens het maken van het rapport |
| `QueryId` | Query-ID die is door gegeven op het moment dat het rapport is gemaakt |
| `Query` | Query tekst die voor dit rapport wordt uitgevoerd |
| `User` | Gebruikers-ID die is gebruikt om het rapport te maken |
| `CreatedTime` | Tijdstip waarop het rapport is gemaakt. De tijd notatie is JJJJ-MM-DDTuu: mm: ssZ |
| `ModifiedTime` | Tijdstip waarop het rapport voor het laatst is gewijzigd. De tijd notatie is JJJJ-MM-DDTuu: mm: ssZ |
| `StartTime` | Tijd dat de uitvoering van het rapport begint. De tijd notatie is JJJJ-MM-DDTuu: mm: ssZ |
| `ReportStatus` | De status van de rapport uitvoering. De mogelijke waarden worden onderbroken, actief en inactief. |
| `RecurrenceInterval` | Het interval voor het terugkeer patroon tijdens het maken van het rapport |
| `RecurrenceCount` | Aantal herhalingen dat is gegeven tijdens het maken van het rapport |
| `CallbackUrl` | Call back-URL die in de aanvraag is opgenomen |
| `Format` | Indeling van de rapport bestanden |
|||
