---
title: Lijst met systeem query's
description: Meer informatie over systeem query's die u kunt gebruiken om via programma code analyse gegevens over uw aanbiedingen te verkrijgen in de micro soft Commercial Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: sayantanroy83
ms.author: sroy
ms.date: 3/08/2021
ms.openlocfilehash: f2b5f7eb559e349947f88067a3d2ea53d99b7cbf
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102583826"
---
# <a name="list-of-system-queries"></a>Lijst met systeem query's

De volgende systeem query's kunnen worden gebruikt in de [rapport-API maken](analytics-programmatic-access.md#create-report-api) rechtstreeks met een `QueryId` . De systeem query's zijn vergelijkbaar met het exporteren van rapporten in het partner centrum voor een 6 min.-reken periode van zes maanden.

Zie de volgende artikelen voor meer informatie over de kolom namen, kenmerken en beschrijving:

- [Dash board klanten in commerciële Marketplace-analyse](customer-dashboard.md#customer-details-table)
- [Dashboard Bestellingen in Commerciële marketplace-analyses](orders-dashboard.md#orders-details-table)
- [Gebruiks dashboard in commerciële Marketplace-analyse](usage-dashboard.md#usage-details-table)
- [Dashboard Inzichten marketplace in Commerciële marketplace-analyses](insights-dashboard.md#marketplace-insights-details-table)

De volgende secties bevatten rapport query's voor verschillende rapporten.

## <a name="customers-report-query"></a>Query rapport klanten

**Rapport beschrijving**: klanten rapporteren voor de afgelopen 6 min.

**QueryID**:  `b9df4929-073f-4795-b0cb-a2c81b11e28d`

**Rapport query**:

`SELECT MarketplaceSubscriptionId,DateAcquired,DateLost,ProviderName,ProviderEmail,FirstName,LastName,Email,CustomerCompanyName,CustomerCity,CustomerPostalCode,CustomerCommunicationCulture,CustomerCountryRegion,AzureLicenseType,PromotionalCustomers,CustomerState,CommerceRootCustomer,CustomerId,BillingAccountId,ID  FROM ISVCustomer TIMESPAN LAST_6_MONTHS`

## <a name="orders-report-query"></a>Rapport query Orders

**Rapport beschrijving**: het rapport Orders voor de afgelopen 6 min.

**QueryID**:  `fd0f299c-5a1c-4929-9f48-bfc6cc44355d`

**Rapport query**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,Sku,CustomerCountry,IsPreviewSKU,OrderId,OrderQuantity,CloudInstanceName,IsNewCustomer,OrderStatus,OrderCancelDate,CustomerCompanyName,CustomerName,OrderPurchaseDate,OfferName,TrialEndDate,CustomerId,BillingAccountId FROM ISVOrder TIMESPAN LAST_6_MONTHS`

## <a name="usage-report-queries"></a>Query's over gebruiks rapporten

**Rapport beschrijving**: genormaliseerd gebruik van VM-rapport voor de laatste 6 min.

**QueryID**:  `2c6f384b-ad52-4aed-965f-32bfa09b3778`

**Rapport query**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,NormalizedUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Rapport beschrijving**: het onbewerkte verbruiks rapport van de VM voor de afgelopen 6 min.

**QueryID**:  `3f19fb95-5bc4-4ee0-872e-cedd22578512`

**Rapport query**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,RawUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('vm core image', 'Virtual Machine Licenses', 'multisolution') TIMESPAN LAST_6_MONTHS`

**Rapport beschrijving**: rapport over gebruik in de data limiet voor de afgelopen 6 min.

**QueryID**:  `f0c4927f-1f23-4c99-be4a-1371a5a9a086`

**Rapport query**:

`SELECT MarketplaceSubscriptionId,MonthStartDate,OfferType,AzureLicenseType,MarketplaceLicenseType,SKU,CustomerCountry,IsPreviewSKU,SKUBillingType,IsInternal,VMSize,CloudInstanceName,ServicePlanName,OfferName,DeploymentMethod,CustomerName,CustomerCompanyName,UsageDate,IsNewCustomer,CoreSize,TrialEndDate,CustomerCurrencyCC,PriceCC,PayoutCurrencyPC,EstimatedPricePC,UsageReference,UsageUnit,CustomerId,BillingAccountId,MeteredUsage,EstimatedExtendedChargeCC,EstimatedExtendedChargePC FROM ISVUsage WHERE OfferType IN ('SaaS', 'Azure Applications') TIMESPAN LAST_6_MONTHS`

## <a name="marketplace-insights-report-query"></a>Rapport query voor Marketplace Insights

**Rapport beschrijving**: marketplace Insights-rapport voor de afgelopen 6 min.

**QueryID**:  `6fd7624b-aa9f-42df-a61d-67d42fd00e92`

**Rapport query**:

`Date,OfferName,ReferralDomain,CountryName,PageVisits,GetItNow,ContactMe,TestDrive,FreeTrial FROM ISVMarketplaceInsights TIMESPAN LAST_6_MONTHS`

## <a name="next-steps"></a>Volgende stappen

- [Api's voor het verkrijgen van toegang tot commerciële Marketplace Analytics-gegevens](analytics-available-apis.md)
- [Voorbeeld toepassing voor toegang tot de gegevens van commerciële Marketplace Analytics](analytics-sample-application.md)
