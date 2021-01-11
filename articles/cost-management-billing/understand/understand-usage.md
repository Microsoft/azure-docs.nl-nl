---
title: Informatie over uw gedetailleerde gebruik en kosten
description: Ontdek hoe u het bestand met uw gedetailleerde gebruik en kosten kunt lezen en begrijpen. Bekijk een lijst met termen en beschrijvingen die in het bestand worden gebruikt.
author: bandersmsft
ms.reviewer: micflan
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 01/04/2021
ms.author: banders
ms.openlocfilehash: 07e3cfdce238d5fc4e2737a49dde6fd624de8506
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97882497"
---
# <a name="understand-the-terms-in-your-azure-usage-and-charges-file"></a>Meer informatie over de gebruiksvoorwaarden in uw bestand voor Azure-gebruik en -kosten

Het gedetailleerde bestand voor Azure-gebruik en -kosten bevat het dagelijks beoordeelde gebruik op basis van overeengekomen tarieven, aankopen (bijvoorbeeld reserveringen en Marketplace-kosten) en restituties voor de opgegeven periode.
Krediet, btw of andere kosten of kortingen vallen niet onder deze kosten.
In de volgende tabel ziet u welke kosten voor elk accounttype zijn inbegrepen.

Accounttype | Azure-gebruik | Marketplace-gebruik | Aankopen | Restituties
--- | --- | --- | --- | ---
Enterprise Agreement (EA) | Ja | Ja | Ja | Nee
Microsoft-klantovereenkomst (MCA) | Ja | Ja | Ja | Ja
Betalen per gebruik (PAYG) | Ja | Ja | Nee | Nee

Zie [Meer informatie over kosten voor uw externe Azure-services](understand-azure-marketplace-charges.md) voor meer informatie over Marketplace-orders (ook wel externe services genoemd).

Zie [Uw Azure-factuur en de dagelijkse gebruiksgegevens downloaden](../manage/download-azure-invoice-daily-usage-date.md) voor downloadinstructies.
U kunt uw CSV-bestand met gebruik en kosten openen in Microsoft Excel of in een andere werkbladtoepassing.

## <a name="list-of-terms-and-descriptions"></a>Lijst met gebruiksvoorwaarden en beschrijvingen

In de volgende tabel worden de belangrijke gebruiksvoorwaarden beschreven die in de nieuwste versie van het bestand voor Azure-gebruik en -kosten worden gebruikt.
In deze lijst worden betalen per gebruik-accounts (PAYG), Enterprise Agreement-accounts (EA) en Microsoft-klantovereenkomstaccounts (MCA) besproken.

Termijn | Accounttype | Beschrijving
--- | --- | ---
AccountName | EA, PAYG | De weergavenaam van het EA-inschrijvingsaccount of het PAYG-factureringsaccount.
AccountOwnerId<sup>1</sup> | EA, PAYG | De unieke id voor het EA-inschrijvingsaccount of het PAYG-factureringsaccount.
AdditionalInfo | Alle | Servicespecifieke metagegevens. Bijvoorbeeld een installatiekopie voor een virtuele machine.
BillingAccountId<sup>1</sup> | Alle | Unieke id voor het hoofdfactureringsaccount.
BillingAccountName | Alle | De naam van het factureringsaccount.
BillingCurrency | Alle | De valuta die aan het factureringsaccount is gekoppeld.
BillingPeriod | EA, PAYG | De factureringsperiode voor de kosten.
BillingPeriodEndDate | Alle | De einddatum van de factureringsperiode.
BillingPeriodStartDate | Alle | De begindatum van de factureringsperiode.
BillingProfileId<sup>1</sup> | Alle | De unieke id van de EA-inschrijving, het PAYG-abonnement, het MCA-factureringsprofiel of het geconsolideerde AWS-account.
BillingProfileName | Alle | De naam van de EA-inschrijving, het PAYG-abonnement, het MCA-factureringsprofiel of het geconsolideerde AWS-account.
ChargeType | Alle | Hiermee wordt aangegeven of de kosten betrekking hebben op het gebruik (**Usage**), een aankoop (**Purchase**) of een restitutie (**Refund**).
ConsumedService | Alle | De naam van de service waaraan de kosten zijn gekoppeld.
CostCenter<sup>1</sup> | EA, MCA | De kostenplaats die voor het abonnement is gedefinieerd voor het bijhouden van de kosten (alleen beschikbaar in openstaande factureringsperioden voor MCA-accounts).
Kosten | EA, PAYG | Zie CostInBillingCurrency.
CostInBillingCurrency | MCA | De kostprijs van de kosten in de factureringsvaluta, vóór aftrek van krediet of btw.
CostInPricingCurrency | MCA | De kostprijs van de kosten in de prijsvaluta, vóór aftrek van krediet of btw.
Valuta | EA, PAYG | Zie BillingCurrency.
Date<sup>1</sup> | Alle | De datum waarop het gebruik of de aankoop zijn betaald.
EffectivePrice | Alle | De gemengde eenheidsprijs voor de periode. Het gemiddelde van de gemengde prijzen als gevolg van schommelingen in de eenheidsprijs, zoals gestaffelde lagen, waardoor de prijs daalt naarmate de hoeveelheid in de loop van de tijd toeneemt.
ExchangeRateDate | MCA | De datum waarop de wisselkoers is bepaald.
ExchangeRatePricingToBilling | MCA | De wisselkoers die is gebruikt om de kosten in de prijseenheid om te zetten in de factureringsvaluta.
Frequency | Alle | Geeft aan of kan worden verwacht dat de kosten worden herhaald. Kosten kunnen eenmalig in rekening worden gebracht (**OneTime**), herhaaldelijk per maand of per jaar (**Recurring**) of op basis van het gebruik (**UsageBased**).
InvoiceId | PAYG, MCA | De unieke document-id die op de factuur-PDF wordt vermeld.
InvoiceSection | MCA | Zie InvoiceSectionName.
InvoiceSectionId<sup>1</sup> | EA, MCA | De unieke id voor de EA-afdeling of het MCA-factuurgedeelte.
InvoiceSectionName | EA, MCA | De naam voor de EA-afdeling of het MCA-factuurgedeelte.
IsAzureCreditEligible | Alle | Geeft aan of de kosten in aanmerking komen om te worden betaald met Azure-tegoed (waarden: True, False).
Locatie | MCA | De locatie van het datacenter waarop de resource wordt uitgevoerd.
MeterCategory | Alle | De naam van de classificatiecategorie voor de meter. Bijvoorbeeld *Cloudservices* en *Netwerken*.
MeterId<sup>1</sup> | Alle | De unieke id voor de meter.
MeterName | Alle | De naam van de meter.
MeterRegion | Alle | De naam van de datacenterlocatie voor services waarvan de prijs wordt bepaald aan de hand van de locatie. Zie Locatie.
MeterSubCategory | Alle | De naam van de subclassificatiecategorie voor de meter.
OfferId<sup>1</sup> | Alle | De naam van de aangeschafte aanbieding.
PayGPrice | Alle | Verkoopprijs voor de resource.
PartNumber<sup>1</sup> | EA, PAYG | De id die wordt gebruikt om prijzen voor specifieke meters op te halen.
PlanName | EA, PAYG | De naam van het Marketplace-abonnement.
PreviousInvoiceId | MCA | Verwijzing naar de oorspronkelijke factuur als dit regelitem een restitutie is.
PricingCurrency | MCA | De valuta die wordt gebruikt bij beoordeling op basis van overeengekomen prijzen.
PricingModel | Alle | De id die aangeeft hoe de meter is geprijsd. (Waarden: op aanvraag, reservering, spot)
Product | Alle | De naam van het product.
ProductId<sup>1</sup> | MCA | De unieke id voor het product.
ProductOrderId | Alle | De unieke id voor de productorder.
ProductOrderName | Alle | De unieke naam voor de productorder.
PublisherName | Alle | De uitgever voor Marketplace-services.
PublisherType | Alle | Het type uitgever (waarden: **Azure**, **AWS**, **Marketplace**).
Aantal | Alle | Het aantal eenheden dat is aangeschaft of verbruikt.
ReservationId | EA, MCA | De unieke id voor de aangeschafte reserveringsinstantie.
ReservationName | EA, MCA | De naam voor de aangeschafte reserveringsinstantie.
ResourceGroup | Alle | De naam van de [resourcegroep](../../azure-resource-manager/management/overview.md) waarin de resource zich bevindt. Niet alle kosten zijn afkomstig van resources die zijn geïmplementeerd in resourcegroepen. Kosten zonder resourcegroep worden weergegeven als null/leeg, **Overige** of **Niet van toepassing**.
ResourceId<sup>1</sup> | Alle | De unieke id van de [Azure Resource Manager](/rest/api/resources/resources)-resource.
ResourceLocation | Alle | De locatie van het datacenter waarop de resource wordt uitgevoerd. Zie Locatie.
ResourceName | EA, PAYG | De naam van de resource. Niet alle kosten zijn afkomstig van geïmplementeerde resources. Kosten zonder resourcetype worden weergegeven als null/leeg, **Overige** of **Niet van toepassing**.
ResourceType | MCA | Het type resource-instantie. Niet alle kosten zijn afkomstig van geïmplementeerde resources. Kosten zonder resourcetype worden weergegeven als null/leeg, **Overige** of **Niet van toepassing**.
ServiceFamily | MCA | De servicereeks waarvan de service deel uitmaakt.
ServiceInfo1 | Alle | Servicespecifieke metagegevens.
ServiceInfo2 | Alle | Een verouderd veld met optionele, servicespecifieke metagegevens.
ServicePeriodEndDate | MCA | De einddatum van de beoordelingsperiode waarin de prijzen voor de verbruikte of aangeschafte service zijn gedefinieerd en vergrendeld.
ServicePeriodStartDate | MCA | De begindatum van de beoordelingsperiode waarin de prijzen voor de verbruikte of aangeschafte service zijn gedefinieerd en vergrendeld.
SubscriptionId<sup>1</sup> | Alle | De unieke id voor het Azure-abonnement.
SubscriptionName | Alle | De naam van het Azure-abonnement.
Tags<sup>1</sup> | Alle | De tags die aan de resource zijn toegewezen. Dit is exclusief tags voor resourcegroepen. Kan worden gebruikt om kosten voor interne terugstorting te groeperen of te distribueren. Zie [Uw Azure-resources organiseren met tags](https://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/) voor meer informatie.
Termijn | Alle | Hiermee wordt de geldigheidstermijn van de aanbieding weergegeven. Bijvoorbeeld: In het geval van gereserveerde instanties wordt 12 maanden als termijn weergegeven. Voor eenmalige aankopen of terugkerende aankopen is de termijn 1 maand (SaaS, Marketplace-ondersteuning). Dit is niet van toepassing op Azure-verbruik.
UnitOfMeasure | Alle | De maateenheid voor facturering van de service. Computeservices worden bijvoorbeeld per uur gefactureerd.
UnitPrice | EA, PAYG | De prijs per eenheid voor de kosten.

_<sup>**1**</sup> Velden die worden gebruikt om een unieke id te maken voor één kostenrecord._

Opmerking: het hoofdlettergebruik en de ruimte tussen accounttypen is mogelijk niet voor alle velden gelijk.
Oudere versies van gebruiksbestanden voor betalen per gebruik hebben aparte secties voor het overzicht en het dagelijkse gebruik.

### <a name="list-of-terms-from-older-apis"></a>Lijst met termen uit oudere API's
In de volgende tabel worden termen die in oudere API’s zijn gebruikt, gekoppeld aan de nieuwe termen. Raadpleeg de bovenstaande tabel voor die beschrijvingen.

Oude term | Nieuwe term
--- | ---
ConsumedQuantity | Aantal
IncludedQuantity | N.v.t.
InstanceId | ResourceId
Tarief | EffectivePrice
Eenheid | UnitOfMeasure
UsageDate | Date
UsageEnd | Date
UsageStart | Date

## <a name="ensure-charges-are-correct"></a>Controleer of de kosten juist zijn

Voor meer informatie over gedetailleerd gebruik en gedetailleerde kosten, leest u hoe u uw [betalen per gebruik-factuur](review-individual-bill.md) of [factuur voor uw Microsoft-klantenovereenkomst](review-customer-agreement-bill.md) moet interpreteren.

## <a name="unexpected-usage-or-charges"></a>Onverwacht gebruik of onverwachte kosten

Als u gebruik of kosten hebt die u niet herkent, zijn er verschillende dingen die u kunt doen om te begrijpen waarom:

- De factuur met kosten voor de resource bekijken
- Uw gefactureerde kosten bekijken in Kostenanalyse
- Vaststellen welke personen verantwoordelijk zijn voor de resource en met hen overleggen
- De auditlogboeken analyseren
- Gebruikersmachtigingen voor het bovenliggende bereik van de resource analyseren
- Een [Azure-ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458) om de kosten te helpen identificeren

Raadpleeg [Onverwachte kosten analyseren](analyze-unexpected-charges.md) voor meer informatie.

Houd er rekening mee dat de meeste gebruikersacties niet worden vastgelegd in Azure. In plaats hiervan wordt resourcegebruik voor facturering vastgelegd door Microsoft. Als u een piek in het gebruik ziet in het verleden, en u logboekregistratie niet hebt ingeschakeld, kan Microsoft de oorzaak niet achterhalen. Schakel logboekregistratie in voor de service waarvoor u het toegenomen gebruik wilt bekijken, zodat het juiste technische team u kan helpen met het probleem.

## <a name="need-help-contact-us"></a>Hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- [Uw Microsoft Azure-factuur weergeven en downloaden](download-azure-invoice.md)
- [Uw Microsoft Azure-gebruik en -kosten weergeven en downloaden](download-azure-daily-usage.md)