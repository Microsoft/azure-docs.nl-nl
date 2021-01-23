---
title: REST API's in Azure Enterprise
description: In dit artikel worden de REST API's beschreven die u kunt gebruiken bij de inschrijving van uw Azure-onderneming.
author: bandersmsft
ms.author: banders
ms.date: 01/21/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 1fdf64053a55eb33d80ed461c231e8c6dd84d63b
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677728"
---
# <a name="azure-enterprise-rest-apis"></a>REST API's in Azure Enterprise

In dit artikel worden de REST API's beschreven die u kunt gebruiken bij de inschrijving van uw Azure-onderneming. Er wordt ook uitleg gegeven over het oplossen van veelvoorkomende problemen aan de hand van REST API's.

## <a name="consumption-and-usage-apis"></a>API's voor verbruik en gebruik

Microsoft Enterprise Azure-klanten kunnen gebruiks- en factureringsgegevens ophalen via REST API's. De eigenaar van de rol (ondernemingsbeheerder, afdelingsbeheerder, accounteigenaar) moet toegang tot de API inschakelen door een sleutel te genereren in Azure EA Portal. Vervolgens kan iedereen met het inschrijvingsnummer en de sleutel via de API toegang krijgen tot de gegevens.

### <a name="available-apis"></a>Beschikbare API's

**Saldo en overzicht**: de [API Saldo en overzicht](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) biedt een maandelijks overzicht van de informatie over saldi, nieuwe aankopen, Azure Marketplace-servicekosten, aanpassingen en overschrijdingskosten. Zie [Rapportage-API's voor Enterprise-klanten - Saldo en overzicht](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) voor meer informatie.

**Gebruiksgegevens**: de [API Gebruiksgegevens](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) biedt een dagelijkse uitsplitsing van de verbruikte hoeveelheden en de geschatte kosten per inschrijving. Het resultaat bevat ook informatie over instanties, meters en afdelingen. Via de API kunnen gegevens worden opgevraagd per factureringsperiode of aan de hand van een opgegeven begin- en einddatum. Zie [Rapportage-API's voor Enterprise-klanten - Gebruiksgegevens](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) voor meer informatie.

**Marketplace Store-kosten**: [API Marketplace Store-kosten](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) retourneert op gebruik gebaseerde Marketplace-kosten, uitgesplitst per dag van de opgegeven factureringsperiode of op basis van de begin- en einddatum. Zie [Rapportage-API's voor Enterprise-klanten - Marketplace Store-kosten](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) voor meer informatie.

**Prijzenoverzicht:** de [API Prijzenoverzicht](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) biedt het toepasselijke tarief voor elke meter voor een inschrijvings- en factureringsperiode. Zie [Rapportage-API's voor Enterprise-klanten - Prijzenoverzicht](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) voor meer informatie.

**Factureringsperioden:** de [API Factureringsperioden](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) retourneert een lijst met factureringsperioden met verbruiksgegevens voor een inschrijving (in omgekeerde chronologische volgorde). Elke periode bevat een eigenschap die verwijst naar de API-route voor de vier sets gegevens: BalanceSummary, UsageDetails, Marketplace-kosten en PriceSheet. Zie [Rapportage-API's voor Enterprise-klanten - Factureringsperioden](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) voor meer informatie.

### <a name="enable-api-data-access"></a>Toegang tot API-gegevens inschakelen

Roleigenaren kunnen de volgende stappen uitvoeren in Azure EA Portal. Navigeer naar **Rapporten** > **Gebruik downloaden** > **API-toegangssleutel**. Ze kunnen dan:

- Primaire en secundaire toegangssleutels genereren.
- Toegangssleutels uitschakelen.
- De begin- en einddata van toegangssleutels bekijken.

### <a name="generate-or-retrieve-the-api-key"></a>De API-sleutel genereren of ophalen

1. Meld u aan als ondernemingsbeheerder.
2. Klik op **Rapporten** in het linkernavigatievenster en klik op het tabblad **Gebruik downloaden**.
3. Klik **API-toegangssleutel**.
4. Selecteer onder **Toegangssleutels voor inschrijving** het symbool voor het genereren van een sleutel om een primaire of secundaire sleutel te maken.
5. Selecteer **Sleutel uitbreiden** om de volledige gegenereerde API-toegangssleutel weer te geven.
6. Selecteer **Kopiëren** om de API-toegangssleutel op te halen voor direct gebruik.

![Voorbeeld van de pagina API-toegangssleutel](./media/ea-portal-rest-apis/ea-create-generate-or-retrieve-the-api-key.png)

Voer de volgende stappen uit als u de API-toegangssleutels wilt geven aan mensen die in uw inschrijving geen ondernemingsbeheerders zijn:

1. Klik in het navigatievenster links op **Beheren**.
2. Klik op het potloodsymbool naast **DA-kostenweergave** (weergave van kosten door de afdelingsbeheerder).
3. Klik op **Inschakelen** en klik vervolgens op **Opslaan**.
4. Klik op het potloodsymbool naast **AO-kostenweergave** (weergave van kosten door accounteigenaar).
5. Klik op **Inschakelen** en klik vervolgens op **Opslaan**.

![Voorbeeld met de DA- en OA-weergavekosten ingeschakeld](./media/ea-portal-rest-apis/create-ea-generate-or-retrieve-api-key-enable-ao-do-view.png) Met de voorgaande stappen krijgen mensen met de API-toegangssleutel toegang tot de kosten- en prijsinformatie in gebruiksrapporten.

### <a name="pass-keys-in-the-api"></a>Sleutels doorgeven in de API

Geef de API-sleutel door voor elke aanroep voor verificatie en autorisatie. Geef de volgende eigenschap door aan HTTP-headers:

| Aanvraagheadersleutel | Waarde |
| --- | --- |
| Autorisatie | Geef de waarde in deze indeling op: **bearer {API\_KEY}**
Voorbeeld: bearer \&lt;APIKey\&gt; |

### <a name="swagger"></a>Swagger

Er is voor de volgende API's een Swagger-eindpunt beschikbaar in [Enterprise Reporting v3 APIs](https://consumption.azure.com/swagger/ui/index). Swagger helpt de API te inspecteren. Gebruik Swagger om client-SDK's te genereren met behulp van [AutoRest](https://github.com/Azure/AutoRest) of [Swagger CodeGen](https://swagger.io/swagger-codegen/). Gegevens die na 1 mei 2014 beschikbaar zijn geworden, zijn toegankelijk via de API.

### <a name="api-response-codes"></a>API-responscodes

Als u een API gebruikt, worden de antwoordstatuscodes weergegeven. Ze worden in de volgende tabel beschreven.

| Reactiestatuscode | Bericht | Beschrijving |
| --- | --- | --- |
| 200 | OK | Geen fout |
| 401 | Niet geautoriseerd | De API-sleutel is niet gevonden, is ongeldig, is verlopen, enzovoort. |
| 404 | Niet beschikbaar | Rapporteindpunt niet gevonden |
| 400 | Onjuiste aanvraag | Ongeldige parameters: datumbereiken, EA-cijfers, enzovoort. |
| 500 | Serverfout | Er is een onverwachte fout opgetreden bij het verwerken van de aanvraag |

### <a name="usage-and-billing-data-update-frequency"></a>Updatefrequentie van de gebruiks- en factureringsgegevens

De gegevensbestanden voor gebruik en facturering worden elke 24 uur bijgewerkt voor de huidige factureringsmaand. Er kan echter een gegevenslatentie zijn van maximaal drie dagen. Als er bijvoorbeeld sprake is van gebruik op maandag, kan het zijn dat het gegevensbestand pas donderdag wordt weergegeven.

### <a name="azure-service-catalog"></a>Azure-servicecatalogus

Alle Azure-services worden in een Azure Storage-blog verzonden naar een catalogus in CSV-indeling. De catalogus is handig als u een gecureerde catalogus wilt bouwen van alle Azure-services voor uw systeem. De huidige catalogus bevindt zich op [https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv](https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv) .

### <a name="csv-data-file-details"></a>Details van het CSV-gegevensbestand

In de volgende informatie worden de eigenschappen van API-rapporten beschreven.

#### <a name="usage-summary"></a>Overzicht van gebruik

De JSON-indeling wordt gegenereerd op basis van het CSV-rapport. Als gevolg hiervan is de indeling hetzelfde als de CSV-indeling van het overzicht. De kolomnaam wordt gebruikt, dus u moet deserialiseren naar een gegevenstabel wanneer u de JSON-overzichtsgegevens gebruikt.

| CSV-kolomnaam | JSON-kolomnaam | Nieuwe JSON-kolom | Opmerking |
| --- | --- | --- | --- |
| AccountOwnerId | AccountOwnerLiveId | AccountOwnerLiveId |   |
| Accountnaam | AccountName | AccountName |   |
| ServiceAdministratorId | ServiceAdministratorLiveId | ServiceAdministratorLiveId |   |
| SubscriptionId | SubscriptionId | SubscriptionId |   |
| SubscriptionGuid | MOCPSubscriptionGuid | SubscriptionGuid |   |
| Subscription Name | SubscriptionName | SubscriptionName |   |
| Date | Date | Date | Hier wordt de datum weergegeven waarop het servicecatalogusrapport is gemaakt. De indeling is een gegevenstekenreeks zonder tijdstempel. |
| Maand | Maand | Maand |   |
| Dag | Dag | Dag |   |
| Jaar | Jaar | Jaar |   |
| Product | BillableItemName | Product |   |
| Meter ID | ResourceGUID | MeterId |   |
| De categorie van de meter | Service | MeterCategory | Nuttig voor het zoeken van services. Relevant voor services met meerdere servicetypen (ServiceType). Voorbeeld: virtuele machines. |
| Meter Sub-Category | ServiceType | MeterSubCategory | Biedt een tweede detailniveau voor een service. Voorbeeld: A1 VM (niet-Windows).  |
| Meter Region | ServiceRegion | MeterRegion | Het derde detailniveau dat is vereist voor een service. Handig om de regiocontext van de ResourceGUID te vinden. |
| Meter Name | ServiceResource | MeterName | De naam van de service. |
| Consumed Quantity | ResourceQtyConsumed | ConsumedQuantity |   |
| ResourceRate | ResourceRate | ResourceRate |   |
| ExtendedCost | ExtendedCost | ExtendedCost |   |
| Resource Location | ServiceSubRegion | ResourceLocation |   |
| Consumed Service | ServiceInfo | ConsumedService |   |
| Instance ID | Onderdeel | InstanceId |   |
| ServiceInfo1 | ServiceInfo1 | ServiceInfo1 |   |
| ServiceInfo2 | ServiceInfo2 | ServiceInfo2 |   |
| AdditionalInfo | AdditionalInfo | AdditionalInfo |   |
| Tags | Tags | Tags |   |
| Store Service Identifier   | OrderNumber | StoreServiceIdentifier   |   |
| Department Name | DepartmentName | DepartmentName |   |
| Cost Center | CostCenter | CostCenter |   |
| Meeteenheid | UnitOfMeasure | UnitOfMeasure | Voorbeeld waarden: uren, GB, gebeurtenissen, pushes, eenheid, Eenheiduren, MB, eenheden per dag |
| ResourceGroup | ResourceGroup | ResourceGroup |   |

#### <a name="azure-marketplace-report"></a>Azure Marketplace-rapport

| CSV-kolomnaam | JSON-kolomnaam | Nieuwe JSON-kolom |
| --- | --- | --- |
| AccountOwnerId | AccountOwnerId | AccountOwnerId |
| Accountnaam | AccountName | AccountName |
| SubscriptionId | SubscriptionId | SubscriptionId |
| SubscriptionGuid | SubscriptionGuid | SubscriptionGuid |
| Subscription Name | SubscriptionName |  SubscriptionName |
| Datum | BillingCycle |  Datum (Alleen datumreeks. Geen tijdstempel.)
| Maand | Maand |  Maand |
| Dag | Dag |  Dag |
| Jaar | Jaar |  Jaar |
| Meter ID | MeterResourceId |  MeterId |
| Naam van de uitgever | PublisherFriendlyName |  PublisherName |
| Naam van aanbieding | OfferFriendlyName |  OfferName |
| Naam van het plan | PlanFriendlyName |  PlanName |
| Consumed Quantity | BilledQty |  ConsumedQuantity |
| ResourceRate | ResourceRate | ResourceRate |
| ExtendedCost | ExtendedCost | ExtendedCost |
| Meeteenheid | UnitOfMeasure | UnitOfMeasure |
| Instance ID | InstanceId | InstanceId |
| Aanvullende informatie | AdditionalInfo | AdditionalInfo |
| Tags | Tags | Tags |
| Bestelnummer | OrderNumber | OrderNumber |
| Department Name | DepartmentNames | DepartmentName |
| Cost Center | CostCenters |  CostCenter |
| Resourcegroep | ResourceGroup |  ResourceGroup |

#### <a name="price-sheet"></a>Prijzenoverzicht

| CSV-kolomnaam | JSON-kolomnaam | Opmerking |
| --- | --- | --- |
| Service | Service |  Prijs is niet gewijzigd |
| Meeteenheid | UnitOfMeasure |   |
| Onderdeelnummer voor overschrijding | ConsumptionPartNumber |   |
| Eenheidsprijs overschrijding | ConsumptionPrice |   |
| Valutacode | CurrencyCode |     |

### <a name="common-api-issues"></a>Veelvoorkomende API-problemen

Omdat u gebruikmaakt van Azure Enterprise REST API's, kan het zijn dat u tegen de volgende veelvoorkomende problemen aanloopt.

U kunt proberen een API-sleutel te gebruiken die niet over het juiste verificatietype beschikt. API-sleutels kunnen worden gegenereerd door:

- Zakelijke beheerder
- Afdelingsbeheerder (DA)
- Accounteigenaar (AO)

Een sleutel die door de EA-beheerder wordt gegenereerd, geeft toegang tot alle informatie voor die inschrijving. Een EA-beheerder met alleen-lezenrechten kan geen API-sleutels genereren.

Een sleutel die wordt gegenereerd door een DA of AO biedt geen toegang tot gegevens over saldi, kosten en prijslijsten.

Elke zes maanden verloopt er een API-sleutel. Als deze is verlopen, moet u er opnieuw een genereren.

Als er een time-outfout optreedt, kunt u deze oplossen door de drempelwaarde voor time-outs te verhogen.

Mogelijk ontvangt u een melding over een 401-verloopfout (geen autorisatie). De fout wordt meestal veroorzaakt door een verlopen sleutel. Als de sleutel is verlopen, kunt u deze opnieuw genereren.

U ziet mogelijk meldingen van 400- en 404-fouten (niet beschikbaar) die worden geretourneerd na een API-aanroep. Dit gebeurt als er geen gegevens beschikbaar zijn voor het geselecteerde datumbereik. Deze fout kan bijvoorbeeld optreden omdat er onlangs een inschrijvingsoverdracht is geïnitieerd. Gegevens vanaf een specifieke datum en later bevinden zich nu in een nieuwe inschrijving. De fout kan ook optreden als u een nieuw inschrijvingsnummer gebruikt om informatie op te halen die zich in een oude inschrijving bevindt.

## <a name="next-steps"></a>Volgende stappen

- Azure EA Portal-beheerders moeten [Beheer van Azure EA Portal](ea-portal-administration.md) lezen voor meer informatie over algemene beheertaken.
- Zie [Problemen met toegang tot Azure EA Portal](ea-portal-troubleshoot.md) als u hulp nodig hebt met het oplossen van problemen Azure EA Portal.