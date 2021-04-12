---
title: API voor rapport query's ophalen
description: Gebruik deze API om alle query's te verkrijgen die beschikbaar zijn voor gebruik in commerciële Marketplace Analytics-rapporten.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: e2be43e8402e5179fb62d810fe7b9f41e704c49d
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583867"
---
# <a name="get-report-queries-api"></a>API voor rapport query's ophalen

Met de query rapport query's ophalen worden alle query's opgehaald die beschikbaar zijn voor gebruik in rapporten. Hiermee worden alle systeem-en door de gebruiker gedefinieerde query's standaard opgehaald.

**Syntaxis van aanvraag**

| **Methode** | **Aanvraag-URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries?queryId={QueryID}&queryName={QueryName}&includeSystemQueries={include_system_queries}&includeOnlySystemQueries={include_only_system_queries}` |
|||

**Aanvraag header**

| **Header** | **Type** | **Beschrijving** |
| --- | --- | --- |
| Autorisatie | tekenreeks | Vereist. Het toegangs token Azure Active Directory (Azure AD) in de vorm `Bearer <token>` |
| Content-Type | tekenreeks | `Application/JSON` |
||||

**Para meter Path**

Geen

**Query parameter**

| **Parameternaam** | **Type** | **Vereist** | **Beschrijving** |
| --- | --- | --- | --- |
| `queryId` | tekenreeks | No | Filteren om alleen details op te halen van query's met de ID die is opgegeven in het argument |
| `queryName` | tekenreeks | No | Filter om alleen details op te halen van query's met de naam die is opgegeven in het argument |
| `IncludeSystemQueries` | booleaans | No | Vooraf gedefinieerde systeem query's in het antwoord toevoegen |
| `IncludeOnlySystemQueries` | booleaans | No | Alleen systeem query's in het antwoord bevatten |
|||||

**Payload aanvragen**

Geen

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
      "QueryId": "string",
      "Name": "string",
      "Description": "string",
      "Query": "string",
      "Type": "string",
      "User": "string",
      "CreatedTime": "string",
      "ModifiedTime": "string"
    }
  ],
  "TotalCount": 0,
  "Message": "string",
  "StatusCode": 0
}
```

**Woordenlijst**

In deze tabel worden de belangrijkste definities van elementen in het antwoord beschreven.

| **Parameter** | **Beschrijving** |
| --- | --- |
| `QueryId` | Unieke UUID van de query |
| `Name` | Naam die aan de query is gegeven op het moment dat de query wordt gemaakt |
| `Description` | Beschrijving gegeven tijdens het maken van de query |
| `Query` | Query teken reeks rapport |
| `Type` | Ingesteld op `userDefined` voor door de gebruiker gemaakte query's en `system` voor vooraf gedefinieerde systeem query's |
| `User` | Gebruikers-ID die de query heeft gemaakt |
| `CreatedTime` | Tijdstip waarop de query is gemaakt |
| `TotalCount` | Aantal gegevens sets in de waarde-matrix |
| `StatusCode` | Resultaat code. De mogelijke waarden zijn 200, 400, 401, 403, 500 |
|||
