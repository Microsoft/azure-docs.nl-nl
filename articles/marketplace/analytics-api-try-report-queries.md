---
title: Query-API voor rapporten uitproberen
description: Gebruik deze API om een rapport query uit te voeren voor analyse rapporten voor commerciële Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 0db212be06182128bbd8a3bf694a2f893ce82eae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583798"
---
# <a name="try-report-queries-api"></a>Query-API voor rapporten uitproberen

Met deze API wordt een rapport query-instructie uitgevoerd. De API retourneert slechts tien records die u als partner kunt gebruiken om te controleren of de gegevens naar verwachting zijn.

> [!IMPORTANT]
> Deze API heeft een time-out van 100 seconden voor de uitvoering van query's. Als u merkt dat de API meer dan 100 seconden duurt, is het zeer waarschijnlijk dat de query syntactisch juist is of dat u een andere fout code dan 200 zou hebben ontvangen. Het werkelijke rapport wordt gegenereerd als de query syntaxis juist is.

**Syntaxis van aanvraag**

| **Methode** | **Aanvraag-URI** |
| --- | --- |
| GET | `https://api.partnercenter.microsoft.com/insights/v1/cmp/ScheduledQueries/testQueryResult?exportQuery={query text}` |
|||

**Aanvraag header**

| **Header** | **Type** | **Beschrijving** |
| --- | --- | --- |
| Autorisatie | tekenreeks | Vereist. Het toegangs token Azure Active Directory (Azure AD) in de vorm `Bearer <token>` |
| Content-Type | tekenreeks | `Application/JSON` |
|||

**QueryParameter**

| **Parameter naam** | **Type** | **Beschrijving** |
| --- | --- | --- |
| `exportQuery` | tekenreeks | Rapport query teken reeks die moet worden uitgevoerd |
| `queryId` | tekenreeks | Een geldige bestaande query-ID |
|||

**Para meter** Path  

Geen

**Payload aanvragen**

Geen

**Woordenlijst**

Geen

**Response**

De nettolading van de reactie is als volgt gestructureerd:

Antwoord code: 200, 400, 401, 403, 404, 500

Reactie Payload: Top 10 rijen van uitvoering van query
