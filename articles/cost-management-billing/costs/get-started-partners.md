---
title: Aan de slag met Azure Cost Management voor partners
description: In dit artikel wordt uitgelegd hoe partners Azure Cost Management-functies gebruiken en hoe ze toegang tot Cost Management voor hun klanten mogelijk maken.
author: bandersmsft
ms.author: banders
ms.date: 11/16/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: aparnag
ms.custom: secdec18
ms.openlocfilehash: cd3ca4d3ddf73469cd1f1fc065eccb369cf765af
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905671"
---
# <a name="get-started-with-azure-cost-management-for-partners"></a>Aan de slag met Azure Cost Management voor partners

Azure Cost Management is standaard beschikbaar voor directe partners die hun klanten hebben voorbereid op een Microsoft-klantovereenkomst en [een Azure-plan hebben gekocht](/partner-center/purchase-azure-plan). In dit artikel wordt uitgelegd hoe partners [Azure Cost Management](../index.yml)-functies gebruiken om de kosten voor abonnementen in het Azure-plan weer te geven. Ook wordt beschreven hoe partners toegang tot Cost Management bieden voor hun klanten tegen verkoopprijzen.

Voor directe partners en indirecte providers hebben de globale beheerder en beheerderagents toegang tot Cost Management in de partnertenant en kosten beheren tegen gefactureerde prijzen.

Resellers en klanten hebben toegang tot Cost Management in de tenant van de klant en kunnen abonnementskosten weergeven, waarbij de kosten worden berekend en weergegeven in tarieven voor de detailhandel. Ze moeten wel Azure RBAC-toegang tot het abonnement in de tenant van de klant hebben om de kosten te kunnen zien. Het beleid voor de zichtbaarheid van kosten moet zijn ingeschakeld door de provider van de tenant van de klant.

Klanten kunnen Cost Management-functies gebruiken wanneer ze zijn ingeschakeld door hun CSP-partner.

CSP-partners gebruiken Cost Management voor het volgende:

- Inzicht krijgen in gefactureerde kosten en de kosten aan de klant, abonnementen, resourcegroepen en services koppelen.
- Een intuïtieve weergave van Azure-kosten krijgen in [Kostenanalyse](quick-acm-cost-analysis.md) met mogelijkheden voor het analyseren van kosten per klant, abonnement, resourcegroep, resource, meter, service en vele andere dimensies.
- Resourcekosten bekijken die Partner Earned Credit (PEC) in de kostenanalyse hebben toegepast.
- Meldingen en automatisering instellen met behulp van programmatische [budgetten](tutorial-acm-create-budgets.md) en waarschuwingen wanneer de kosten de budgetten overschrijden.
- Schakel het Azure Resource Manager-beleid in dat klanten toegang biedt tot Cost Management-gegevens. Klanten kunnen gegevens over verbruikskosten voor hun abonnementen dan weergeven met behulp van [betalen per gebruik-tarieven](https://azure.microsoft.com/pricing/calculator/).
- Exporteer hun kosten- en gebruiksgegevens naar een opslag-blob met een betalen per gebruik-abonnement.

Hier volgt een voorbeeld van de kosten voor alle klanten.

[![Voorbeeld van de kosten voor alle klanten](./media/get-started-partners/customer-costs1.png)](./media/get-started-partners/customer-costs1.png#lightbox)

Hier volgt een voorbeeld van de kosten voor één klant.

[![Voorbeeld van de kosten voor één klant](./media/get-started-partners/customer-costs2.png)](./media/get-started-partners/customer-costs2.png#lightbox)

Alle functionaliteit die beschikbaar is in Azure Cost Management is ook beschikbaar met REST-API's. Gebruik de API's om kostenbeheertaken te automatiseren.

## <a name="prerequisites"></a>Vereisten

Als partner is Azure Cost Management standaard alleen beschikbaar voor abonnementen die zich in het Azure-plan bevinden.

Als u Azure Cost Management in de Azure-portal wilt inschakelen, moet u de Microsoft-klantovereenkomst hebben bevestigd (namens de klant) en de klant hebben overgezet naar het Azure-plan. Alleen de kosten voor abonnementen die zijn overgezet naar het Azure-plan, zijn beschikbaar in Azure Cost Management.

Azure Cost Management heeft leestoegang tot uw factureringsrekening of abonnement nodig.

Zie [Gebruikersrollen en machtigingen toewijzen](/partner-center/permissions-overview) voor meer informatie over het inschakelen en toewijzen van toegang tot Azure Cost Management voor een factureringsrekening. De rollen **Globale beheerder** en **Beheerdersagent** kunnen de kosten voor een factureringsrekening beheren.

Om toegang te krijgen tot Azure Cost Management op het abonnementsbereik, kunnen gebruikers met Azure RBAC-toegang tot een abonnement de kosten bekijken op detailhandel-tarieven (betalen naar gebruik). Het beleid voor de [zichtbaarheid van kosten voor de tenant van de klant](#enable-the-policy-to-view-azure-usage-charges) moet echter zijn ingeschakeld. Zie voor een volledige lijst met ondersteunde accounttypen [Gegevens van Cost Management begrijpen](understand-cost-mgt-data.md).

## <a name="how-cost-management-uses-scopes"></a>Hoe Cost Management bereiken gebruikt

Bereiken zijn de plekken waar u factureringsgegevens beheert, over rollen beschikt die specifiek zijn voor betalingen, facturen beheert en algemeen accountbeheer uitvoert. Facturerings- en accountrollen worden afzonderlijk beheerd vanaf bereiken die worden gebruikt voor resourcebeheer, en die gebruik maken van Azure RBAC. Om de intentie van de afzonderlijke bereiken duidelijk te onderscheiden, met inbegrip van de verschillen in toegangsbeheer, worden ze respectievelijk factureringsbereiken en Azure RBAC-bereiken genoemd.

Zie [Begrijpen en werken met bereiken](understand-work-scopes.md) voor meer informatie over factureringsbereiken en Azure RBAC-bereiken en hoe kostenbeheer werkt met bereiken.

## <a name="manage-costs-with-partner-tenant-billing-scopes"></a>Kosten beheren met factureringsbereiken van een partner-tenant

Nadat u uw klanten hebt voorbereid op een Microsoft-klantovereenkomst, zijn de volgende _factureringsbereiken_ beschikbaar in uw tenant. Gebruik de bereiken voor het beheren van kosten in Cost Management.

### <a name="billing-account-scope"></a>Bereik van factureringsrekening

Gebruik het bereik van de factureringsrekening om kosten vóór belastingen voor al uw klanten en factureringsprofielen weer te geven. Factuurkosten worden alleen weergegeven voor producten op basis van verbruik op de Microsoft-klantovereenkomst. Factuurkosten worden echter weergegeven voor op aankopen gebaseerde producten voor klanten op zowel de Microsoft-klantovereenkomst als de CSP-aanbieding. Op dit moment is de standaardvaluta voor het weergeven van de kosten in het bereik Amerikaanse dollars. Budgetten die zijn ingesteld voor het bereik, zijn ook in USD.

In het kader van verschillende klant-gefactureerde valuta's gebruiken partners het bereik van factureringsrekeningen om budgetten in te stellen en kosten in USD te beheren voor hun klanten, abonnementen, resources en resourcegroepen.

Partners filteren ook kosten in een specifieke factuurvaluta voor klanten in de weergave voor kostenanalyse. Selecteer de lijst met **Werkelijke kosten** om de kosten in de ondersteunde factureringsvaluta van de klant weer te geven.

![Voorbeeld van de werkelijke kostenselectie voor valuta's](./media/get-started-partners/actual-cost-selector.png)

Gebruik de weergave [Afgeschreven kosten](quick-acm-cost-analysis.md#customize-cost-views) in factureringsbereiken om de verlaagde kosten van gereserveerde instanties weer te geven in een reserveringsperiode.

### <a name="billing-profile-scope"></a>Bereik van het factureringsprofiel

Gebruik het bereik van het factureringsprofiel om de kosten vóór belasting in de factureringsvaluta voor al uw klanten te bekijken voor alle producten en abonnementen die in een factuur zijn opgenomen. U kunt de kosten in een factureringsprofiel voor een specifieke factuur filteren met behulp van het filter **InvoiceID**. Het filter toont het verbruik en de aanschafkosten van het product voor een specifieke factuur. U kunt ook de kosten voor een specifieke klant op de factuur filteren om de kosten vóór belasting te bekijken.

Nadat u klanten voorbereidt op een Microsoft-klantovereenkomst, ontvangt u een factuur met alle kosten voor alle producten (verbruik, aankopen en rechten) voor deze klanten op de Microsoft-klantovereenkomst. Wanneer in dezelfde valuta wordt gefactureerd, omvatten deze facturen ook de kosten voor rechten en aangeschafte producten zoals SaaS, Azure Marketplace en reserveringen voor klanten die zich nog in de CSP-aanbieding bevinden.

Voor het afstemmen van de kosten voor de klantfactuur kunt u met het bereik van het factureringsprofiel alle kosten bekijken die opgeteld worden voor een factuur voor uw klanten. Net als bij de factuur toont het bereik de kosten voor elke klant in de nieuwe Microsoft-klantovereenkomst. In het bereik worden ook de kosten van producten voor klantrechten weergegeven die nog in de huidige CSP-aanbieding zijn opgenomen.

De bereiken van het factureringsprofiel en de factureringsrekening zijn de enige toepasselijke bereiken die de kosten voor rechten en op aankoop gebaseerde producten zoals Azure Marketplace en reserveringsaankopen tonen.

In factureringsprofielen worden de abonnementen gedefinieerd die zijn opgenomen in een factuur. Factureringsprofielen zijn het functionele equivalent van een Enterprise Agreement-inschrijving. Een factureringsprofiel is het bereik waarin facturen worden gegenereerd.

Op dit moment is de factureringsvaluta van de klant de standaardvaluta bij het weergeven van de kosten in het bereik van het factureringsprofiel. Budgetten die zijn ingesteld op het bereik van het factureringsprofiel, worden in de factureringsvaluta opgenomen.

Partners kunnen het bereik gebruiken voor het afstemmen op facturen. En ze gebruiken het bereik om budgetten in te stellen in de factureringsvaluta voor de volgende items:

- Specifieke gefilterde factuur
- Klant
- Abonnement
- Resourcegroep
- Resource
- Azure-service
- Meter
- ResellerMPNID

### <a name="customer-scope"></a>Klantbereik

Partners gebruiken het bereik voor het beheren van de kosten die zijn gekoppeld aan klanten die zijn voorbereid op de Microsoft-klantovereenkomst. Het bereik staat partners toe om de kosten vóór belasting voor een specifieke klant in een factureringsvaluta weer te geven. U kunt ook de kosten vóór belastingen voor een specifiek abonnement, resourcegroep of resource filteren.

Het bereik van de klant bevat geen klanten die zich in de huidige CSP-aanbieding bevinden. Het bereik bevat alleen klanten die een Microsoft-klantovereenkomst hebben. De kosten voor rechten, niet voor Azure-gebruik, voor klanten van de huidige CSP-aanbieding zijn beschikbaar op de factureringsrekening en het bereik van factureringsprofielen wanneer u het klantfilter toepast. De budgetten die zijn ingesteld op dit bereik, worden in de factureringsvaluta opgenomen.

## <a name="partner-access-to-billing-scopes-in-cost-management"></a>Partnertoegang tot de factureringsbereiken in Cost Management

Alleen gebruikers met de rollen **Globale beheerder** en **Beheerdersagent** kunnen de kosten voor factureringsrekeningen, factureringsprofielen en klanten rechtstreeks in de Azure-Tenant van de partner beheren en weergeven. Zie [Gebruikersrollen en -machtigingen toewijzen](/partner-center/permissions-overview) voor meer informatie over de rollen van het partnercentrum.

## <a name="enable-cost-management-for-customer-tenant-subscriptions"></a>Kostenbeheer inschakelen voor tenantabonnementen van klanten

Partners kunnen toegang tot Cost Management mogelijk maken nadat klanten zijn voorbereid op een Microsoft-klantovereenkomst. Vervolgens kunnen partners een beleid inschakelen waarmee klanten hun kosten voor gebruikte services in Azure bekijken die worden berekend op basis van betalen naar gebruik-detailhandelstarieven. Kosten worden weergegeven in de factureringsvaluta van de klant voor het verbruikte gebruik van het abonnement en de resourcegroepen van het Azure RBAC.

Wanneer het beleid voor de zichtbaarheid van kosten wordt ingeschakeld door de partner, kan elke gebruiker met Azure Resource Manager-toegang tot het abonnement, kosten op basis van betalen naar gebruik-tarieven beheren en analyseren. Effectief kunnen wederverkopers en klanten die de juiste Azure RBAC-toegang hebben tot de Azure-abonnementen, kosten bekijken.

Ongeacht het beleid kunnen globale beheerders en beheerdersagenten van de provider abonnementskosten bekijken als ze toegang hebben tot het abonnement en de resourcegroep.

### <a name="enable-the-policy-to-view-azure-usage-charges"></a>Het beleid inschakelen voor het weergeven van Azure-gebruiks kosten

U moet lid zijn van de groep **beheerdersagent** om het beleid weer te geven en bij te werken. Gebruik de volgende informatie om het beleid in te schakelen waarmee klanten Azure-gebruikskosten kunnen weergeven.

Meld u aan bij de *partner-tenant* in Azure Portal en selecteer **Kostenbeheer en facturering**. Selecteer het relevante factureringsbereik in het gebied voor het factureringsbereik en selecteer vervolgens **Klanten**. De lijst met klanten is gekoppeld aan de factureringsrekening. *Als u zich per ongeluk aanmeldt bij de tenant van de klant, wordt de lijst **Klanten** niet weergegeven.*

Selecteer in de lijst met klanten de klant waarvoor u de kosten wilt weergeven.

[![Klanten selecteren in Cost Management](./media/get-started-partners/customer-list.png)](./media/get-started-partners/customer-list.png#lightbox)

Selecteer **Beleid** onder **Instellingen**.

Het huidige beleid voor het berekenen van de kosten wordt weergegeven voor **Azure-gebruiks** kosten die zijn gekoppeld aan de abonnementen voor de geselecteerde klant.
![Beleid waarmee klanten Betalen per gebruik-kosten kunnen weergeven](./media/get-started-partners/cost-management-billing-policies.png)

Wanneer het beleid is ingesteld op **Nee**, is Azure Cost Management niet beschikbaar voor abonnementsgebruikers die zijn gekoppeld aan de klant. Tenzij deze is ingeschakeld door een partner, wordt het beleid voor kostenzichtbaarheid standaard uitgeschakeld voor alle gebruikers van het abonnement.

Wanneer het kostenbeleid is ingesteld op **Ja**, kunnen abonnementsgebruikers die zijn gekoppeld aan de tenant van de klant, de gebruikskosten bekijken bij Betalen naar gebruik-tarieven.

Wanneer het beleid voor het berekenen van de kosten is ingeschakeld, worden voor alle services waarvoor het abonnementsgebruik geldt, kosten op basis van Betalen naar gebruik-tarieven weergegeven. Het gebruik van de reservering wordt weergegeven met nul kosten voor de werkelijke en afgeschreven kosten. Aankopen en rechten zijn niet gekoppeld aan een specifiek abonnement. Aankopen worden dus niet weergegeven in het abonnementsbereik. De globale beheerder/beheerderagent van een directe partner of een indirecte provider kan ook de [Update Customer-API](/rest/api/billing/2019-10-01-preview/policies/updatecustomer) gebruiken om het beleid voor kostenzichtbaarheid van elke klant op schaal in te stellen.

### <a name="view-subscription-costs-in-the-customer-tenant"></a>Abonnementskosten bekijken in de tenant van de klant

Als u de kosten voor een abonnement wilt bekijken, opent u **Kostenbeheer en facturering** in de Azure-tenant van de klant. Selecteer **Kostenanalyse** het vereiste abonnement om te beginnen met het controleren van de kosten. U kunt de verbruikskosten voor elk abonnement afzonderlijk bekijken in de tenant van de klant.

[![Kostenanalyse als klant weergeven](./media/get-started-partners/subscription-costs.png)](./media/get-started-partners/subscription-costs.png#lightbox)

Kostenanalyse, budgetten en waarschuwingen zijn beschikbaar voor het abonnement en de Azure RBAC-scopes van de resourcegroep tegen kosten op basis van Betalen per gebruik.

Bij afgeschreven weergaven en werkelijke kosten voor gereserveerde instanties in de Azure RBAC-bereiken worden nul kosten weergegeven. Aankoopkosten voor rechten, zoals gereserveerde instanties en Marketplace-kosten, worden alleen weergegeven in factureringsbereiken in de tenant van de partner waar de aankopen zijn gedaan.

De verkoopprijzen die worden gebruikt voor het berekenen van de kosten in de weergave zijn dezelfde prijzen die worden weergegeven in de Azure-prijscalculator voor alle klanten. De kosten die worden weergegeven, omvatten geen eventuele kortingen of tegoeden van de partner, zoals door partner verdiende tegoeden (PEC), niveaukortingen en Global Service-kortingen.

 

## <a name="analyze-costs-in-cost-analysis"></a>Kosten in kostenanalyse beoordelen

Partners met toegang tot de factureringsbereiken in de partner-tenant kunnen gefactureerde kosten in kostenanalyse over klanten verkennen en analyseren voor een specifieke klant of voor een factuur. In de [Kostenanalyse](quick-acm-cost-analysis.md)-weergave kunt u ook [weergaven opslaan](quick-acm-cost-analysis.md#saving-and-sharing-customized-views) en gegevens exporteren naar [CSV- en PNG-bestanden](quick-acm-cost-analysis.md#download-usage-data).

Azure RBAC-gebruikers met toegang tot het abonnement in de tenant van de klant kunnen ook retailkosten analyseren voor abonnementen in de tenant van de klant, weergaven opslaan en gegevens exporteren naar CSV- en PNG-bestanden.

U kunt filteren en groeperen op functies in kostenanalyse gebruiken om kosten te analyseren op basis van meerdere velden. In de volgende sectie worden de partner-specifieke velden weergegeven.

## <a name="data-fields"></a>Gegevensvelden

De volgende gegevensvelden zijn te vinden in detailbestanden voor gebruik en Cost Management-API's. Indien beschikbaar, wordt gelijkwaardige informatie over het Partnercentrum weergegeven. Voor de volgende vetgedrukte velden kunt u filteren en groeperen op functies in kostenanalyse gebruiken om kosten te analyseren op basis van meerdere velden. Vetgedrukte velden zijn alleen van toepassing op Microsoft-klantovereenkomsten die door partners worden ondersteund.

| **Veldnaam** | **Beschrijving** | **Partnercentrum-equivalent** |
| --- | --- | --- |
| InvoiceId | Factuur-ID die wordt weergegeven op de factuur voor de specifieke transactie. | Factuurnummer waar de transactie wordt weergegeven. |
| previousInvoiceID | Verwijzing naar een oorspronkelijke factuur als dit regelitem een restitutie is (negatieve kosten). Alleen ingevuld als er een restitutie is. | N.v.t. |
| billingAccountName | De naam van de factureringsrekening die de partner voorstelt. Alle kosten worden in rekening gebracht voor alle klanten die zijn voorbereid op een Microsoft-klantovereenkomst en de CSP-klanten die recht hebben op aankopen als SaaS, Azure Marketplace en reserveringen. | N.v.t. |
| billingAccountID | De id van de factureringsrekening die de partner voorstelt. | MCAPI Partner Commerce-basis id. Wordt gebruikt in een aanvraag, maar is niet opgenomen in een antwoord.|
| billingProfileID | De id voor het factureringsprofiel waarmee de kosten voor facturen worden gegroepeerd in een enkele factureringsvaluta voor klanten die zijn voorbereid op een Microsoft-klantovereenkomst en de CSP-klanten die recht hebben op aankopen als SaaS, Azure Marketplace en reserveringen. | Facturerings-groep-ID van MCAPI-partner. Wordt gebruikt in een aanvraag, maar is niet opgenomen in een antwoord. |
| billingProfileName | De naam van het factureringsprofiel waarmee de kosten voor facturen worden gegroepeerd in een enkele factureringsvaluta voor klanten die zijn voorbereid op een Microsoft-klantovereenkomst en de CSP-klanten die recht hebben op aankopen als SaaS, Azure Marketplace en reserveringen. | N.v.t. |
| invoiceSectionName | De naam van het project dat in de factuur wordt gefactureerd. Niet van toepassing op Microsoft-klantovereenkomsten die worden voorbereid door partners. | N.v.t. |
| invoiceSectionID | De id voor het project dat in de factuur wordt gefactureerd. Niet van toepassing op Microsoft-klantovereenkomsten die worden voorbereid door partners. | N.v.t. |
| **CustomerTenantID** | De id van de Azure Active Directory-tenant van het abonnement van de klant. | Organisatie-id van de klant: de Azure Active Directory TenantID van de klant. |
| **CustomerName** | De naam van de Azure Active Directory-tenant van het abonnement van de klant. | De organisatienaam van de klant, zoals weergegeven in het partnercentrum. Belangrijk voor het afstemmen van de factuur met uw systeemgegevens. |
| **CustomerTenantDomainName** | De domeinnaam van de Azure Active Directory-tenant van het abonnement van de klant. | Azure Active Directory Tenant-domein van de klant. |
| **PartnerTenantID** | Id voor de Azure Active Directory-tenant van de partner. | Azure Active Directory Tenant-ID van de partner, in GUID-indeling, aangeroepen als partner-id. |
| **PartnerName** | Naam de Azure Active Directory-tenant van de partner. | Naam van de partner. |
| **ResellerMPNID** | MPNID voor de reseller die aan het abonnement is gekoppeld. | MPN-ID van de geregistreerde reseller voor het abonnement. Niet beschikbaar voor huidige activiteit. |
| costCenter | Kostenplaats die aan het abonnement is gekoppeld. | N.v.t. |
| billingPeriodStartDate | Begindatum van de factureringsperiode, zoals op de factuur wordt weergegeven. | N.v.t. |
| billingPeriodEndDate | Einddatum van de factureringsperiode, zoals op de factuur wordt weergegeven. | N.v.t. |
| servicePeriodStartDate | De begindatum voor de beoordelingsperiode wanneer het serviceverbruik is beoordeeld op kosten. De prijzen voor Azure-Services worden bepaald voor de beoordelingsperiode. | ChargeStartDate in partnercentrum.  De begindatum van de factureringscyclus, met uitzondering van de datum van voorheen niet-gefactureerde ongebruikte gegevens van een eerdere factureringscyclus. De tijd is altijd het begin van de dag, 0:00. |
| servicePeriodEndDate | De begindatum voor de periode wanneer het serviceverbruik is beoordeeld op kosten. De prijzen voor Azure-Services worden bepaald voor de beoordelingsperiode. | N.v.t. |
| date | Voor Azure-verbruiksgegevens wordt de datum van gebruik weergegeven als geclassificeerd. Voor een gereserveerde instantie wordt de aangeschafte datum weergegeven. Voor terugkerende kosten en eenmalige kosten, zoals Marketplace en ondersteuning, wordt de aankoopdatum weer gegeven. | N.v.t. |
| productID | Id voor het product dat de transitorische kosten per verbruik of aankoop heeft. Het is de aaneengeschakelde sleutel van productID en SKuID, zoals wordt weergegeven in het partnercentrum. | De id van het product. |
| product | Id voor het product dat de transitorische kosten per verbruik of aankoop heeft, zoals weergegeven op de factuur. | De productnaam in de catalogus. |
| serviceFamily | Toont de servicefamilie voor het product dat is gekocht of in rekening wordt gebracht. Bijvoorbeeld Storage of Compute. | N.v.t. |
| productOrderId | De id van de asset of de Azure-plannaam waarvan het abonnement deel uitmaakt. Bijvoorbeeld Azure-plan. | CommerceSubscriptionID |
| productOrderName | De naam van het Azure-plan waarvan het abonnement deel uitmaakt. Bijvoorbeeld Azure-plan. | N.v.t.|
| consumedService | Verbruikte service (verouderde taxonomie) zoals gebruikt in verouderde EA-gebruiksgegevens. | De service die wordt weergegeven in het partnercentrum. Bijvoorbeeld Microsoft.Storage, Microsoft.Compute en microsoft.operationalinsights. |
| meterId | Gemeten id voor het gemeten verbruik. | De id van de gebruikte meter. |
| meterName | Hiermee wordt de naam van de meter voor gemeten verbruik geïdentificeerd. | De naam van de gebruikte meter. |
| meterCategory | Identificeert de service op het hoogste niveau voor het gebruik. | De service op het hoogste niveau voor het gebruik. |
| meterSubCategory | Definieert het type Azure-service dat van invloed kan zijn op het tarief. | Het type Azure-service dat van invloed kan zijn op het tarief.|
| meterRegion | De datacenterlocatie voor bepaalde services waarbij de prijs is gebaseerd op de datacenterlocatie. | De regionale locatie van een datacentrumr voor services, indien van toepassing en gevuld. |
| subscription ID | De unieke door Microsoft gegenereerde id voor het Azure-abonnement. | EntitlementID |
| subscriptionName | De naam van het Azure-abonnement. | N.v.t. |
| Termijn | Hiermee wordt de geldigheidstermijn van de aanbieding weergegeven. Gereserveerde instanties tonen bijvoorbeeld 12 maanden van een jaar van de gereserveerde instantie. Voor eenmalige aankopen of terugkerende aankopen wordt voor de periode één maand voor SaaS, Azure Marketplace en ondersteuning weergegeven. Niet van toepassing op Azure-verbruik. | N.v.t. |
| publisherType (waarden: firstParty, thirdPartyReseller, thirdPartyAgency) | Type uitgever die de uitgever identificeert als eerste partij, reseller van derden of bureau van derden. | N.v.t. |
| partNumber | Onderdeelnummer voor de niet-gebruikte gereserveerde instantie en Azure Marketplace-Services. | N.v.t. |
| publisherName | De naam van de uitgever van de service, inclusief Microsoft of uitgevers als derden. | De naam van de uitgever van het product.|
| reservationId | De id voor de gereserveerde instantie-aankoop. | N.v.t. |
| reservationName | De naam van de gereserveerde instantie. | N.v.t. |
| reservationOrderId | OderID van de gereserveerde instantie. | N.v.t. |
| frequency | De betalingsfrequentie voor een gereserveerde instantie. | N.v.t. |
| resourceGroup | De naam van de Azure-resourcegroep die wordt gebruikt voor levenscyclus-resourcebeheer. | De naam van de resourcegroep. |
| instanceID (of) ResourceID | Id voor de resource-instantie. | Wordt weergegeven als een ResourceURI die volledige resource-eigenschappen bevat. |
| resourceLocation | Naam van de resourcelocatie. | De locatie van de resource. |
| Locatie | De genormaliseerde locatie van de resource. | N.v.t. |
| effectivePrice | De werkelijke eenheidsprijs van de service in de prijsvaluta. Uniek voor een product, servicegroep, meter en aanbieding. Wordt gebruikt met prijzen in het prijsoverzicht voor de factureringsrekening. Als er gelaagde prijzen of een inbegrepen hoeveelheid zijn, wordt de overvloeiprijs voor verbruik weer gegeven. | De eenheidsprijs nadat aanpassingen zijn gemaakt. |
| Aantal | Gemeten hoeveelheid die is gekocht of verbruikt. De hoeveelheid van de meter die wordt gebruikt tijdens de factureringsperiode. | Aantal eenheden. Zorg ervoor dat deze overeenkomt met de informatie in uw factureringssysteem tijdens het afstemmen. |
| unitOfMeasure | Hiermee wordt de eenheid geïdentificeerd waarin de service wordt gefactureerd. Bijvoorbeeld GB en uren. | Hiermee wordt de eenheid geïdentificeerd waarin de service wordt gefactureerd. Bijvoorbeeld GB, uren of per 10.000. |
| pricingCurrency | De valuta waarin de eenheidsprijs wordt gedefinieerd. | De valuta in de prijslijst.|
| billingCurrency | De valuta waarin de gefactureerde kosten worden gedefinieerd. | De valuta van de geografische regio van de klant. |
| chargeType | Definieert het type kosten dat de kosten vertegenwoordigen in Azure Cost Management, zoals aankoop en terugbetaling. | Het type kosten of correctie. Niet beschikbaar voor huidige activiteit. |
| costinBillingCurrency | ExtendedCost of overvloeikosten vóór belasting in de gefactureerde valuta. | N.v.t. |
| costinPricingCurrency | Berekende kosten of overvloeikosten vóór belasting in de prijsvaluta om te correleren met prijzen. | N.v.t. |
| **costinUSD** | Geschatte berekende kosten of overvloei kosten vóór belasting in USD. | N.v.t. |
| **paygCostInBillingCurrency** | Hiermee worden de kosten weergegeven als de prijzen verkoopprijzen zijn. Hiermee worden de prijzen voor betalen naar gebruik in de factureringsvaluta weergegeven. Alleen beschikbaar voor Azure RBAC-bereiken. | N.v.t. |
| **paygCostInUSD** | Hiermee worden de kosten weergegeven als de prijzen verkoopprijzen zijn. Toont de prijzen voor Betalen naar gebruik in USD. Alleen beschikbaar voor Azure RBAC-bereiken. | N.v.t. |
| exchangeRate | De wisselkoers die is gebruikt om de kosten van de prijsvaluta om te zetten in de factureringsvaluta. | Wordt aangeduid als PCToBCExchangeRate in het partnercentrum. De prijsvaluta voor facturerings-wisselkoers.|
| exchangeRateDate | De datum voor de wisselkoers die is gebruikt om de kosten van de prijsvaluta om te zetten in de factureringsvaluta. | Wordt aangeduid als PCToBCExchangeRateDat in het partnercentrum. De prijsvaluta voor de datum van de facturerings-wisselkoers.|
| isAzureCreditEligible | Geeft aan of de kosten in aanmerking komen voor betaling door Azure-tegoed. | N.v.t. |
| serviceInfo1 | Verouderd veld waarin optionele servicespecifieke metagegevens worden vastgelegd. | Interne metagegevens van Azure-service. |
| serviceInfo2 | Verouderd veld waarin optionele servicespecifieke metagegevens worden vastgelegd. | Service-informatie. Bijvoorbeeld een afbeeldingstype voor een virtuele machine en ISP-naam voor ExpressRoute.|
| additionalInfo | Servicespecifieke metagegevens. Bijvoorbeeld een installatiekopie voor een virtuele machine. | Aanvullende informatie die niet wordt behandeld in andere kolommen. De servicespecifieke metagegevens. Bijvoorbeeld een installatiekopie voor een virtuele machine.|
| tags | Tag die u toewijst aan de meter. Tags gebruiken om factureringsrecords te groeperen. U kunt bijvoorbeeld tags gebruiken om kosten te verdelen naar de afdeling die gebruikmaakt van de meter. | Tags die door de klant worden toegevoegd.|
| **partnerEarnedCreditRate** | Het tarief van de korting wordt toegepast als er een door de partner verdiend tegoed (PEC) is op basis van de toegang tot de beheerder van de partner. | Het tarief van het verdiende tegoed van de partner (PEC). Bijvoorbeeld 0% of 15%. |
| **partnerEarnedCreditApplied** | Geeft aan of het verdiende tegoed van de partner is toegepast. | N.v.t. |

## <a name="view-partner-earned-credit-pec-resource-costs"></a>Resourcekosten voor het verdiende tegoed van partners (PEC) weergeven

In Azure Cost Management kunnen partners kostenanalyse gebruiken om de kosten te bekijken die de PEC-voordelen hebben ontvangen.

Meld u aan bij de partner-tenant in de Azure-portal en selecteer **Cost Management + Facturering**. In **Cost Management** selecteert u **Kostenanalyse**.

In de weergave Kostenanalyse worden de kosten van de factureringsrekening voor de partner weergegeven. Selecteer het **Bereik** waar nodig voor de partner, een specifieke klant of een factureringsprofiel voor het afstemmen van facturen.

Selecteer in een ringdiagram de vervolgkeuzelijst en selecteer **PartnerEarnedCreditApplied** om in te zoomen op PEC-kosten.

![Voorbeeld van het weergeven van het verdiende tegoed van een partner](./media/get-started-partners/cost-analysis-pec1.png)

Wanneer de eigenschap **PartnerEarnedCreditApplied** _Waar_ is, hebben de gekoppelde kosten het voordeel van de verdiende beheerderstoegang van de partner.

Wanneer de eigenschap **PartnerEarnedCreditApplied** _Onwaar_ is, voldoen de gekoppelde kosten niet aan de vereiste rechten voor het tegoed. Of de aangeschafte service komt niet in aanmerking voor het verdiende tegoed van de partner.

Service-gebruiksgegevens hebben doorgaans 8-24 uur om te verschijnen in Cost Management. Zie [Updates en retentie voor kosten- en gebruiksgegevens](understand-cost-mgt-data.md#cost-and-usage-data-updates-and-retention) voor meer informatie. PEC-tegoeden worden binnen 48 uur na de toegangstijd weergegeven in Azure Cost Management.


U kunt ook groeperen en filteren op de eigenschap **PartnerEarnedCreditApplied** met behulp van de **Groeperen op**-opties. Gebruik de opties om kosten te onderzoeken die wel of geen PEC hebben.

![Groeperen of filteren op het verdiende tegoed van de partner](./media/get-started-partners/cost-analysis-pec2.png)

## <a name="export-cost-data-to-azure-storage"></a>Kostengegevens exporteren naar Azure Storage

Partners met toegang tot de factureringsbereiken in een partner-tenant kunnen hun kosten- en gebruiksgegevens exporteren naar een Azure Storage-blob. De blob moet zich in een abonnement op de partnertenant bevinden, dat geen [gedeeld serviceabonnement](/partner-center/shared-services) en geen klantabonnement is. Om het exporteren van kostengegevens in te schakelen, raden wij u aan een onafhankelijk Betalen per gebruik-abonnement in de partner-tenant in te stellen om de geëxporteerde kostengegevens te hosten. Het export-opslagaccount wordt gemaakt op de Azure Storage-blob die wordt gehost in het abonnement voor Betalen naar gebruik. Op basis van het bereik waarin de partner de export maakt, worden de bijbehorende gegevens automatisch op regelmatige basis geëxporteerd naar het opslagaccount.

Gebruikers met Azure RBAC-toegang tot het abonnement kunnen de kostengegevens ook exporteren naar een Azure Storage-blob die wordt gehost in een abonnement in de tenant van de klant.

### <a name="create-an-export-in-a-partner-tenant-or-customer-tenant"></a>Een export maken in een tenant van een partner of klant

Meld u aan bij de partner- of klant-tenant in de Azure-portal en selecteer **Cost Management + Facturering**. Selecteer een geschikt bereik, bijvoorbeeld een factureringsrekening voor een Microsoft-partnerovereenkomst, en selecteer vervolgens **Kostenanalyse**. Wanneer de pagina wordt geladen, selecteert u **Exporteren**. Selecteer **Alle exports weergeven** onder Exporteren plannen.

![Selecteer Exporteren en Alle exports weergeven](./media/get-started-partners/export01.png)

Selecteer vervolgens **Toevoegen**, typ de naam en selecteer een exporttype. Selecteer het tabblad **Opslag** en voer de vereiste gegevens in.

![Voeg nieuwe export toe en selecteer het tabblad Opslag](./media/get-started-partners/export02.png)

Wanneer u een export maakt in de partner-tenant, selecteert u het abonnement voor Betalen naar gebruik in de partner-tenant. Maak een Azure Storage-account met dat abonnement.

Voor Azure RBAC-gebruikers in de tenant van de klant selecteert u een abonnement in de tenant van de klant. Maak een Azure Storage-account met het abonnement.

Bekijk de inhoud en selecteer vervolgens **Maken** om een export te plannen.

Als u gegevens wilt controleren in de lijst Exporteren, selecteert u de naam van het opslagaccount. Selecteer op de pagina Opslagaccount de optie **Containers** en selecteer vervolgens de container. Ga naar de bijbehorende map en selecteer het CSV-bestand. Selecteer **Download** om het CSV-bestand op te halen en te openen. De geëxporteerde gegevens die worden geëxporteerd, zijn vergelijkbaar met kostengegevens die gelijk zijn aan de gebruiksgegevens van de Azure-portal.

![Voorbeeld van geëxporteerde gegevens](./media/get-started-partners/example-export-data.png)

## <a name="cost-management-rest-apis"></a>REST API's voor Cost Management

Partners en klanten kunnen Cost Management-API's gebruiken die worden beschreven in de volgende secties voor algemene taken.

### <a name="azure-cost-management-apis---direct-and-indirect-providers"></a>Azure Cost Management-API's - directe en indirecte providers

Partners met toegang tot de factureringsbereiken in een partner-tenant kunnen de volgende API's gebruiken om gefactureerde kosten weer te geven.

API's in het abonnementsbereik kunnen worden aangeroepen door een partner, ongeacht het kostenbeleid als ze toegang hebben tot het abonnement. Andere gebruikers met toegang tot het abonnement, zoals de klant of de wederverkoper, kunnen de API's pas aanroepen nadat de partner het kostenbeleid voor de tenant van de klant heeft ingeschakeld.


#### <a name="to-get-a-list-of-billing-accounts"></a>Een lijst met factureringsperioden krijgen

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-customers"></a>Een lijst met klanten krijgen

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-subscriptions"></a>Een lijst met abonnementen krijgen

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingSubscriptions?api-version=2019-10-01-preview
```

#### <a name="to-get-a-list-of-invoices-for-a-period-of-time"></a>Een lijst met facturen voor een bepaalde tijd krijgen

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/invoices?api-version=2019-10-01-preview&periodStartDate={periodStartDate}&periodEndDate={periodEndDate}
```

De API-aanroep retourneert een aantal facturen met elementen die vergelijkbaar zijn met de volgende JSON-code.

```
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/{billingAccountID}/billingProfiles/{BillingProfileID}/invoices/{InvoiceID}",
      "name": "{InvoiceID}",
      "properties": {
        "amountDue": {
          "currency": "USD",
          "value": x.xx
        },
        ...
    }
```

Gebruik de waarde van het voorgaande geretourneerde id-veld en vervang deze in het volgende voorbeeld als het bereik dat u wilt doorzoeken op gebruiksgegevens.

```
GET https://management.azure.com/{id}/providers/Microsoft.Consumption/UsageDetails?api-version=2019-10-01
```

In het voorbeeld worden de gebruiksrecords geretourneerd die zijn gekoppeld aan de specifieke factuur.


#### <a name="to-get-the-policy-for-customers-to-view-costs"></a>Het beleid ophalen voor klanten om de kosten te bekijken

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-set-the-policy-for-customers-to-view-costs"></a>Het beleid instellen voor klanten om de kosten te bekijken

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/policies/default?api-version=2019-10-01-preview
```

#### <a name="to-get-azure-service-usage-for-a-billing-account"></a>Azure-servicegebruik voor een factureringsrekening krijgen

```
GET https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/providers/Microsoft.Consumption/usageDetails?api-version=2019-10-01
```

#### <a name="to-download-a-customers-azure-service-usage"></a>Het Azure-servicegebruik van een klant downloaden

De volgende Get-aanroep is een asynchrone bewerking.

```
GET https://management.azure.com/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/usageDetails/download?api-version=2019-10-01 -verbose
```

Roep de `Location` URI aan die in het antwoord is geretourneerd om de bewerkingsstatus te controleren. Wanneer de status *Voltooid* is, bevat de eigenschap `downloadUrl` een koppeling die u kunt gebruiken om het gegenereerde rapport te downloaden.


#### <a name="to-get-or-download-the-price-sheet-for-consumed-azure-services"></a>Het prijzenoverzicht voor verbruikte Azure-services ophalen of downloaden

Gebruik eerst het volgende bericht.

```
POST https://management.azure.com/providers/Microsoft.Billing/BillingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheet/default/download?api-version=2019-10-01-preview&format=csv" -verbose
```

Roep vervolgens de eigenschapswaarde van de asynchrone bewerking aan. Bijvoorbeeld:

```
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/billingProfiles/{billingProfileID}/pricesheetDownloadOperations/{operation}?sessiontoken=0:11186&api-version=2019-10-01-preview
```
De voorgaande ophaal-aanroep retourneert de downloadkoppeling met het prijzenoverzicht.


#### <a name="to-get-aggregated-costs"></a>Om geaggregeerde kosten te verkrijgen

```
POST https://management.azure.com/providers/microsoft.billing/billingAccounts/{billingAccountName}/providers/microsoft.costmanagement/query?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-partner"></a>Een budget voor een partner maken

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/providers/Microsoft.CostManagement/budgets/partnerworkshopbudget?api-version=2019-10-01
```

#### <a name="create-a-budget-for-a-customer"></a>Een budget voor een klant maken

```
PUT https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountName}/customers/{customerID}/providers/Microsoft.Consumption/budgets/{budgetName}?api-version=2019-10-01
```

#### <a name="delete-a-budget"></a>Een budget verwijderen

```
DELETE
https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/providers/Microsoft.CostManagement/budgets/{budgetName}?api-version=2019-10-01
```


## <a name="next-steps"></a>Volgende stappen
- [Kosten analyseren](quick-acm-cost-analysis.md) in Cost Management
- [Maken en beheren van budgetten](tutorial-acm-create-budgets.md) met Cost Management
