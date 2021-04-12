---
title: Voorbeeld query's voor programmeer analyse
description: Gebruik deze voorbeeld query's om programmatisch toegang te krijgen tot gegevens van micro soft Commercial Marketplace Analytics.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: 7d788448fb3f8a849f79e43fcb0737898f4c9e15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102583827"
---
# <a name="sample-queries-for-programmatic-analytics"></a>Voorbeeld query's voor programmeer analyse

Dit artikel bevat voorbeeld query's voor de micro soft Commercial Marketplace-orders, het gebruik en de klant rapporten. U kunt deze query's gebruiken door het API-eind punt voor het maken van een [rapport query](analytics-programmatic-access.md#create-report-query-api) aan te roepen. Indien nodig kunt u de aanroep [rapport query](analytics-programmatic-access.md#create-report-query-api) -API maken wijzigen om meer kolommen toe te voegen, de reken periode aan te passen en filter voorwaarden toe te voegen. De ondersteunde tijds perioden zijn zes maanden (6 min.), 12 maanden (12M) en aangepaste tijds periode.

Raadpleeg de volgende tabellen voor meer informatie over de kolom namen, kenmerken en beschrijvingen:

- [Tabel Klant gegevens](customer-dashboard.md#customer-details-table)
- [Tabel met Order Details](orders-dashboard.md#orders-details-table)
- [Tabel gebruiks gegevens](usage-dashboard.md#usage-details-table)

## <a name="customers-report-queries"></a>Query's rapporten van klanten

Deze voorbeeld query's zijn van toepassing op het rapport klanten.

| **Query Beschrijving** | **Voorbeeld query** |
| --- | --- |
| Actieve klanten van de partner tot de datum die u kiest | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 1` |
| Verlopen klanten van de partner tot de datum die u kiest | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE IsActive = 0` |
| Lijst met nieuwe klanten van een specifieke Geografie in de afgelopen zes maanden | `SELECT DateAcquired,CustomerCompanyName,CustomerId FROM ISVCustomer WHERE DateAcquired <= ‘2020-06-30’ AND CustomerCountryRegion = ‘United States’` |
|||

## <a name="usage-report-queries"></a>Query's over gebruiks rapporten

Deze voorbeeld query's zijn van toepassing op het gebruiks rapport.

| **Query Beschrijving** | **Voorbeeld query** |
| --- | --- |
| Virtuele machine (VM) genormaliseerd gebruik voor het Marketplace-licentie type ' gefactureerd via Azure ' voor de afgelopen 6 min. | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Onbewerkte VM-gebruik voor het Marketplace-licentie type ' gefactureerd via Azure ' voor de afgelopen 12M | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_1_YEAR` |
| Genormaliseerd gebruik van een VM-licentie type voor uw eigen licentie voor de afgelopen 6 min. | `SELECT MonthStartDate, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Onbewerkte VM-gebruik voor het licentie type ' uw eigen licentie ' voor de afgelopen 6 min. | `SELECT MonthStartDate, RawUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Bring Your Own License’ AND OfferType NOT IN (‘Azure Applications’, ‘SaaS’) TIMESPAN LAST_6_MONTHS` |
| Op basis van gebruiks datum, totaal dagelijks genormaliseerd gebruik en ' geschatte uitgebreide kosten (PC/CC) ' voor betaalde abonnementen voor de afgelopen maand | `SELECT UsageDate, NormalizedUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST_MONTH` |
| Op basis van de gebruiks datum, het dagelijkse totale onbewerkte gebruik en de geschatte uitgebreide kosten (PC/CC) voor betaalde abonnementen voor de afgelopen maand | `SELECT UsageDate, RawUsage, EstimatedExtendedChargePC FROM ISVUsage WHERE SKUBillingType = ‘Paid’ ORDER BY UsageDate DESC TIMESPAN LAST\_MONTH` |
| Voor een specifieke aanbiedings naam, genormaliseerd VM-gebruik voor ' gefactureerd via Azure ' Marketplace-licentie type voor de afgelopen 6 min. | `SELECT OfferName, NormalizedUsage FROM ISVUsage WHERE MarketplaceLicenseType = ‘Billed Through Azure’ AND OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Voor een specifieke aanbiedings naam, gebruik in de data limiet voor de afgelopen 6 min. | `SELECT OfferName, MeteredUsage FROM ISVUsage WHERE OfferName = ‘Example Offer Name’ AND OfferType IN (‘SaaS’, ‘Azure Applications’) TIMESPAN LAST_6_MONTHS` |
|||

## <a name="orders-report-queries"></a>Rapport query's orders

Deze voorbeeld query's zijn van toepassing op het rapport Orders.

| **Query Beschrijving** | **Voorbeeld query** |
| --- | --- |
| Bestel rapport voor het Azure-licentie type als onderneming voor de afgelopen 6 min. | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Enterprise’ TIMESPAN LAST\_6\_MONTHS` |
| Bestel rapport voor het Azure-licentie type als ' betalen naar gebruik ' voor de afgelopen 6 min. | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE AzureLicenseType = ‘Pay as You Go’ TIMESPAN LAST_6_MONTHS` |
| Het rapport Orders voor de naam van een specifieke aanbieding voor de afgelopen 6 min. | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OfferName = ‘Example Offer Name’ TIMESPAN LAST_6_MONTHS` |
| Rapport Orders voor actieve orders voor de afgelopen 6 min. | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Active’ TIMESPAN LAST_6_MONTHS` |
| Rapport Orders voor geannuleerde orders voor de afgelopen 6 min. | `SELECT OrderId, OrderPurchaseDate FROM ISVOrder WHERE OrderStatus = ‘Cancelled’ TIMESPAN LAST_6_MONTHS` |
|||

## <a name="next-steps"></a>Volgende stappen

- [Api's voor het verkrijgen van toegang tot commerciële Marketplace Analytics-gegevens](analytics-available-apis.md)
