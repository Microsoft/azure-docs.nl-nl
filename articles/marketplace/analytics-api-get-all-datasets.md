---
title: De API alle data sets ophalen
description: Gebruik deze API om alle beschik bare gegevens sets voor de analyse van commerciële Marketplace op te halen.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 23aac2c94ffd909ca132cbc481998b9eda317156
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583802"
---
# <a name="get-all-datasets-api"></a>De API alle data sets ophalen

Met de API alle data sets ophalen worden alle beschik bare gegevens sets opgehaald. Met gegevens sets worden de tabellen, kolommen, metrische gegevens en peri Oden weer gegeven.

**Syntaxis van aanvraag**

| **Methode** | **Aanvraag-URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledDataset?datasetName={Dataset Name}` |

**Aanvraag header**

| **Header** | **Type** | **Beschrijving** |
| --- | --- | --- |
| Autorisatie | tekenreeks | Vereist. Het toegangs token Azure Active Directory (Azure AD) in de vorm `Bearer <token>` |
| Content-Type | tekenreeks | `Application/JSON` |

**Para meter Path**

Geen

**Queryparameter**

| **Parameter naam** | **Type** | **Vereist** | **Beschrijving** |
| --- | --- | --- | --- |
| `datasetName` | tekenreeks | No | Filteren om details van slechts één gegevensset op te halen |

**Payload aanvragen**

Geen

**Woordenlijst**

Geen

**Response**

De nettolading van de reactie is als volgt gestructureerd:

Antwoord code: 200, 400, 401, 403, 404, 500

Voor beeld van een nettolading van antwoorden:

```json
{
   "Value":[
      {
         "DatasetName ":"string",
         "SelectableColumns":[
            "string"
         ],
         "AvailableMetrics":[
            "string"
         ],
         "AvailableDateRanges ":[
            "string"
         ]
      }
   ],
   "TotalCount":int,
   "Message":"<Error Message>",
   "StatusCode": int
}
```

**Woordenlijst**

In deze tabel worden de belangrijkste elementen in het antwoord gedefinieerd:

| **Parameter** | **Beschrijving** |
| --- | --- |
| `DatasetName` | De naam van de gegevensset die dit Matrix object definieert |
| `SelectableColumns` | Onbewerkte kolommen die kunnen worden opgegeven in de kolommen selecteren |
| `AvailableMetrics` | Kolom namen voor aggregatie/metriek die kunnen worden opgegeven in de kolommen selecteren |
| `AvailableDateRanges` | Datum bereik dat kan worden gebruikt in rapport query's voor de gegevensset |
| `NextLink` | Koppeling naar volgende pagina als de gegevens zijn gepagineerd |
| `TotalCount` | Aantal gegevens sets in de waarde-matrix |
| `StatusCode` | Resultaat code. De mogelijke waarden zijn 200, 400, 401, 403, 500 |
