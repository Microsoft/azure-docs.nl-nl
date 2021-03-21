---
title: API voor rapport query's verwijderen
description: Gebruik deze API om door de gebruiker gedefinieerde query's voor de analyse van commerciële Marketplace te verwijderen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 4fc3479f1e35970a97684396a7a2e0c0c2582128
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102583909"
---
# <a name="delete-report-queries-api"></a>API voor rapport query's verwijderen

Met deze API worden door de gebruiker gedefinieerde query's verwijderd.

**Syntaxis van aanvraag**

| **Methode** | **Aanvraag-URI** |
| --- | --- |
| DELETE | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/{queryId}` |

**Aanvraag header**

| **Header** | **Type** | **Beschrijving** |
| --- | --- | --- |
| Autorisatie | tekenreeks | Vereist. Het toegangs token Azure Active Directory (Azure AD) in de vorm `Bearer <token>` |
| Content-Type | tekenreeks | `Application/JSON` |

**Para meter Path**

| **Parameternaam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| `queryId` | tekenreeks | Filteren om alleen details op te halen van query's met de ID die in dit argument is opgegeven |

**Query parameter**

Geen

**Payload aanvragen**

Geen

**Woordenlijst**

Geen

**Response**

De nettolading van de reactie is als volgt gestructureerd in JSON-indeling.

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

Deze tabel bevat de belangrijkste definities van elementen in het antwoord.

| **Parameter** | **Beschrijving** |
| --- | --- |
| `QueryId` | De unieke UUID van de query die is verwijderd. |
| `Name` | De naam van de query die is verwijderd |
| `Description` | Beschrijving van de verwijderde query |
| `Query` | Rapport query teken reeks van de verwijderde query |
| `Type` | Gebruikergedefinieerd |
| `User` | Gebruikers-ID die de query heeft gemaakt |
| `CreatedTime` | Tijdstip waarop de query is gemaakt |
| `ModifiedTime` | Null |
| `TotalCount` | Aantal gegevens sets in de waarde-matrix |
| `StatusCode` | Resultaat code. De mogelijke waarden zijn 200, 400, 401, 403, 500 |
