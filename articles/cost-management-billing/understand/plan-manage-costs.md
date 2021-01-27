---
title: Plannen om Azure-kosten te beheren
description: Leer hoe u kosten voor Azure kunt plannen en functies voor het bijhouden en beheren van kosten voor uw Azure-account gebruiken.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 210f7daa9305f2fb343dfae050354264e2fd823e
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898559"
---
# <a name="plan-to-manage-azure-costs"></a>Plannen om Azure-kosten te beheren

Dit artikel helpt u aan de slag te gaan met het plannen voor het beheren van uw Azure-kosten. Wanneer u zich aanmeldt voor Azure, zijn er een aantal zaken die u kunt doen om meer inzicht in uw uitgaven te krijgen:

- Ontvang geschatte kosten voordat u services toevoegt met behulp van de [prijscalculator](https://azure.microsoft.com/pricing/calculator/), het prijzenoverzicht voor Azure of bij het toevoegen van services in de Azure-portal.
- Bewaak kosten met [budgetten](../costs/tutorial-acm-create-budgets.md), [meldingen](../costs/cost-mgt-alerts-monitor-usage-spending.md) en [kostenanalyses](../costs/quick-acm-cost-analysis.md).
- Controleer de kosten op uw factuur door deze te vergelijken met [gedetailleerde gebruiksgegevensbestanden](../manage/download-azure-invoice-daily-usage-date.md).
- Integreer facturerings- en kostengegevens met uw eigen rapportagesysteem met behulp van de [Billing](/rest/api/billing/)- en [Consumption](/rest/api/consumption/)-API's.
- Gebruik aanvullende bronnen en hulpprogramma's voor Enterprise Agreement- (EA), Cloud Solution Provider- (CSP) en Azure Sponsorship-klanten.
- Maak [gratis gebruik van een aantal populaire Azure-services gedurende 12 maanden](../manage/create-free-services.md) die beschikbaar zijn bij het [gratis Azure-account](https://azure.microsoft.com/free/). Naast de aanbevelingen die hieronder worden vermeld, zie [Voorkomen dat u moet betalen voor een gratis account](../manage/avoid-charges-free-account.md).

Raadpleeg [Uw Azure-abonnement annuleren](../manage/cancel-azure-subscription.md) als u uw Azure-abonnement wilt annuleren.

## <a name="get-estimated-costs-before-adding-azure-services"></a>Geschatte kosten ontvangen voordat u Azure-services toevoegt

Gebruik een van de volgende hulpprogramma's om de verbruikskosten van een Azure-service te schatten:
- Azure-prijscalculator
- Prijzenoverzicht voor Azure
- Azure Portal

Op de afbeeldingen in de volgende secties ziet u voorbeeldprijzen in Amerikaanse dollar. De genoemde prijzen zijn alleen bedoeld als voorbeeld. Ze zijn niet bedoeld om werkelijke kosten te impliceren. 

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Online kosten schatten met behulp van de Prijscalculator

Bekijk de [Prijscalculator](https://azure.microsoft.com/pricing/calculator/) voor geschatte maandelijkse kosten van de service die u wilt toevoegen. U kunt de valuta wijzigen om de schatting in uw lokale valuta weer te geven.

![Schermopname van het menu Prijscalculator](./media/plan-manage-costs/pricing-calc.png)

U kunt de geschatte kosten weergeven voor elke eigen Azure-service. In de onderstaande schermopname wordt bijvoorbeeld geschat dat de rekenuren voor een A1 virtuele Windows-machine $ 66,96 per maand kost als u deze VM de hele tijd blijft uitvoeren:

![Schermopname van de Prijscalculator, met de geschatte kosten voor een A1 Windows-VM](./media/plan-manage-costs/pricing-calc-vm.png)

De genoemde prijzen zijn alleen bedoeld als voorbeeld. Ze zijn niet bedoeld om werkelijke kosten te impliceren.

Zie de [Veelgestelde vragen over prijzen](https://azure.microsoft.com/pricing/faq/) voor meer informatie over prijzen. Als u met een Azure-verkoopmedewerker wilt spreken, belt u het telefoonnummer bovenaan de pagina Veelgestelde vragen.

### <a name="review-prices"></a>Prijzen bekijken

Als u toegang hebt tot Azure via een Enterprise Agreement (EA) of een Microsoft-klantovereenkomst (MCA), kunt u het prijzenoverzicht voor uw Azure-account bekijken en downloaden. Het prijzenoverzicht is een Excel-bestand met prijzen voor alle Azure-Services. Zie [Uw prijzen voor Azure weergeven en downloaden](../manage/ea-pricing.md) voor meer informatie.

Voor andere soorten abonnementen kunt u standaarddetailhandelsprijzen ophalen met de [Azure Retail Prices API](/rest/api/cost-management/retail-prices/azure-retail-prices).

### <a name="review-estimated-costs-in-the-azure-portal"></a>Geschatte kosten controleren in Azure Portal

U kunt de geschatte kosten per maand bekijken bij het toevoegen van een service in de Azure-portal. Wanneer u bijvoorbeeld de grootte van uw Windows-VM kiest, ziet u de geschatte maandelijkse kosten voor de rekenuren:

![Voorbeeld: een A1 Windows-VM, met geschatte kosten per maand](./media/plan-manage-costs/vm-size-cost.png)

De genoemde prijzen zijn alleen bedoeld als voorbeeld. Ze zijn niet bedoeld om werkelijke kosten te impliceren.

## <a name="monitor-costs-when-using-azure-services"></a>Kosten bewaken bij het gebruik van Azure-services
U kunt kosten bewaken met de volgende hulpprogramma's:

- Budget- en kostenmeldingen
- Kostenanalyse

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Kosten bijhouden met budget- en kostenmeldingen

Maak [budgetten](../costs/tutorial-acm-create-budgets.md) om kosten te beheren maak en [meldingen](../costs/cost-mgt-alerts-monitor-usage-spending.md) waarmee belanghebbenden automatisch worden geïnformeerd over afwijkende en te hoge uitgaven.

### <a name="explore-and-analyze-costs-with-cost-analysis"></a><a name="costs"></a> Kosten verkennen en analyseren met Kostenanalyse

Nadat u uw Azure-services bent gaan uitvoeren, moet u de kosten regelmatig controleren om uw Azure-uitgaven bij te houden. U kunt Kostenanalyse gebruiken om inzicht te krijgen in de kosten van uw Azure-gebruik.

Ga naar [Kostenbeheer en facturering in de Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

Klik op **Kostenanalyse** aan de linkerkant van het scherm om de huidige kosten weer te geven, onderverdeeld naar verschillende draaipunten, zoals de service, locatie en het abonnement. Als u een service toevoegt of een aankoop doet, moet u 24 uur wachten voordat de gegevens worden weergegeven. Standaard worden in Kostenanalyse de kosten voor uw huidige bereik weergegeven. In de onderstaande schermopname worden bijvoorbeeld kosten voor de factureringsrekening van Contoso weergegeven. Gebruik Bereik om over te schakelen naar een ander bereik in Kostenanalyse. Zie [Understand and work with scopes](../costs/understand-work-scopes.md#scopes) (Engelstalig) voor meer informatie over bereiken

![Schermopname van de kostenanalyseweergave in Azure Portal](./media/plan-manage-costs/cost-analysis.png)

U kunt de weergave filteren op verschillende eigenschappen, zoals labels, resourcetype en tijdsbestek. Klik op **Filter toevoegen** om het filter voor een eigenschap toe te voegen en selecteer de gewenste filterwaarden. Selecteer **Exporteren** als u de weergave wilt exporteren als bestand met door komma's gescheiden waarden (CSV).

Daarnaast kunt u op de labels van het diagram klikken om de dagelijkse uitgavengeschiedenis voor dat label weer te geven. In de onderstaande schermopname ziet u bijvoorbeeld dat de dagelijkse kosten van het uitvoeren van uw VM's worden weergegeven wanneer u een virtuele machine selecteert.

:::image type="content" source="./media/plan-manage-costs/cost-history.png" alt-text="Schermopname van de uitgavengeschiedenisweergave in Azure Portal" lightbox="./media/plan-manage-costs/cost-history.png" :::

## <a name="optimize-and-reduce-costs"></a>Kosten optimaliseren en verlagen

Als u niet bekend bent met de principes van kostenbeheer, leest u [Uw cloudinvestering optimaliseren met Azure Cost Management](../costs/cost-mgt-best-practices.md).

In Azure Portal kunt u ook Azure-kosten optimaliseren en verlagen met behulp van automatisch afsluiten voor virtuele machines en Advisor-aanbevelingen.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Kostenbesparende functies overwegen zoals automatisch afsluiten voor virtuele machines

Afhankelijk van uw scenario kunt u automatisch afsluiten voor uw virtuele machines configureren in Azure Portal. Zie [Automatisch afsluiten voor virtuele machines met behulp van Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/) voor meer informatie.

![Schermopname van de optie Automatisch afsluiten in de portal](./media/plan-manage-costs/auto-shutdown.png)

Automatisch afsluiten is wat anders dan uitschakelen in de virtuele machine met behulp van aan-uitopties. Met Automatisch afsluiten worden uw virtuele machines gestopt en wordt de toewijzing hiervan ongedaan gemaakt om ervoor te zorgen dat er geen extra kosten meer in rekening worden gebracht. Zie de veelgestelde vragen over prijzen voor [virtuele Linux-machines](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) en [virtuele Windows-machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) over VM-statussen voor meer informatie.

Raadpleeg [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/) voor meer kostenbesparende functies voor uw ontwikkelings- en testomgevingen.

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Azure Advisor-aanbevelingen inschakelen en beoordelen

[Azure Advisor](../../advisor/advisor-overview.md) helpt u kosten te besparen door resources te identificeren die weinig worden gebruikt. Zoek naar **Advisor** in de Azure-portal:

![Schermopname van de knop Azure Advisor in Azure Portal](./media/plan-manage-costs/advisor-button.png)

Selecteer **Kosten** aan de linkerkant. Op het tabblad **Kosten** ziet u aanbevelingen voor acties:

![Schermopname van een voorbeeld van de Advisor-aanbevelingen voor kosten](./media/plan-manage-costs/advisor-action.png)

De genoemde prijzen zijn alleen bedoeld als voorbeeld. Ze zijn niet bedoeld om werkelijke kosten te impliceren.

Lees de zelfstudie [Kosten optimaliseren op basis van aanbevelingen](../costs/tutorial-acm-opt-recommendations.md) voor een begeleide zelfstudie over kostenbesparende Advisor-aanbevelingen.

## <a name="integrate-with-billing-and-consumption-apis"></a>Integreren met de API's voor facturerings- en verbruiksgegevens

Gebruik de Azure [Billing](/rest/api/billing/)- en [Consumption](/rest/api/consumption/)-API's om programmatisch facturerings-en kostengegevens op te halen. Gebruik zowel de RateCard-API als de gebruik-API om uw gefactureerde gebruik op te halen. Zie [Inzicht verkrijgen in het gebruik van Microsoft Azure-resources](../manage/consumption-api-overview.md#usage-details-api)voor meer informatie.

## <a name="additional-resources-and-special-cases"></a><a name="other-offers"></a> Aanvullende resources en speciale gevallen

### <a name="ea-csp-and-sponsorship-customers"></a>EA-, CSP- en Sponsorship-klanten
Ga in gesprek met uw accountbeheerder of Azure-partner om aan de slag te gaan.

| Aanbieding | Resources |
|-------------------------------|-----------------------------------------------------------------------------------|
| Enterprise Agreement (EA) | [EA portal](https://ea.azure.com/), [helpdocumenten](https://ea.azure.com/helpdocs) en [Power BI-rapport](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Cloud Solution Provider (CSP) | In gesprek gaan met uw provider |
| Azure Sponsorship | [Sponsorship-portal](https://www.microsoftazuresponsorships.com/) |

Als u IT voor een grote organisatie beheert, wordt aanbevolen de [zakelijke ondersteuning voor Azure](/azure/architecture/cloud-adoption-guide/subscription-governance) en het [zakelijke technische document voor IT](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) te lezen (PDF-downloads, alleen in het Engels).

### <a name="enterprise-agreement-cost-views-in-the-azure-portal"></a><a name="EA"></a> Enterprise Agreement-kostenweergaven in Azure Portal

Zakelijke kostenweergaven zijn momenteel als openbare preview-versie beschikbaar. Aandachtspunten:

- Abonnementskosten zijn gebaseerd op gebruik en zijn exclusief vooruitbetaalde bedragen, overschrijdingen, opgenomen hoeveelheden, aanpassingen en belasting. De daadwerkelijke kosten worden op inschrijvingsniveau berekend.
- De bedragen die in Azure Portal worden weergegeven, kunnen afwijken van de bedragen in de Enterprise-portal. Het kan enkele minuten duren voordat wijzigingen als gevolg van updates in de Enterprise-portal worden weergegeven in Azure Portal.
- Als u geen kosten ziet, komt dit waarschijnlijk door een van de volgende oorzaken:
    - U hebt geen machtigingen op abonnementsniveau. Als u de zakelijke kostenweergaven wilt zien, moet u een lezer met factureringsrechten, een lezer, een inzender of een eigenaar op abonnementsniveau zijn.
    - U bent eigenaar van een account en uw inschrijvingsbeheerder heeft de instelling AO-weergavekosten uitgeschakeld.  Neem contact op met uw inschrijvingsbeheerder voor toegang tot kosten.
    - U bent een afdelingsbeheerder en uw inschrijvingsbeheerder heeft de instelling **DA-weergavekosten** uitgeschakeld.  Neem contact op met uw inschrijvingsbeheerder voor toegang.
    - U hebt Azure via een kanaalpartner aangeschaft en de partner heeft geen prijsgegevens gepubliceerd.  
- Als u instellingen met betrekking tot kostentoegang in de Enterprise-portal bijwerkt, is er een vertraging van enkele minuten voordat de wijzigingen worden weergegeven in de Azure-portal.
- Instructies voor de bestedingslimiet en facturen zijn niet van toepassing op EA-abonnementen.

### <a name="check-your-subscription-and-access"></a>Uw abonnement en toegang controleren

Als u kosten wilt weergeven, hebt u toegang tot kosten- of factureringsgegevens nodig op rekening- of abonnementsniveau. De toegang is afhankelijk van het type factureringsrekening. Zie[Uw factureringsrekeningen weergeven in de Azure-portal](../manage/view-all-accounts.md) voor meer informatie over factureringsrekeningen en het controleren van uw type factureringsrekening.

Als u toegang hebt tot Azure via een factureringsrekening van een Microsoft Online-serviceprogramma (MOSP), raadpleegt u [Toegang tot factureringsgegevens beheren voor Azure](../manage/manage-billing-access.md).

Als u toegang hebt tot Azure via een factureringsrekening van een Enterprise Agreement (EA), raadpleegt u [Informatie over beheerdersrollen voor Azure Enterprise Agreements in Azure ](../manage/understand-ea-roles.md).

Als u toegang hebt tot Azure via een factureringsrekening van een Microsoft-klantovereenkomst (MCA), raadpleegt u [Informatie over beheerdersrollen voor Microsoft-klantovereenkomsten in Azure ](../manage/understand-mca-roles.md).

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Een Service Level Agreement-tegoed aanvragen voor een service-incident

In de SLA (Service Level Agreement) worden de toezeggingen van Microsoft voor bedrijfstijd en connectiviteit beschreven. Een service-incident wordt gerapporteerd wanneer er zich een probleem voordoet bij Azure-services die invloed heeft op de bedrijfstijd of connectiviteit, ook wel *uitval* genoemd. Als we de serviceniveaus voor elke service niet kunnen bereiken en onderhouden zoals dat wordt beschreven in de SLA, komt u mogelijk in aanmerking voor tegoed ter hoogte van een deel van uw maandelijkse servicekosten.

U kunt als volgt een tegoed aanvragen:

Meld u aan bij de [Azure-portal](https://portal.azure.com/). Als u meerdere accounts hebt, controleert u of u het account gebruikt die door de uitvaltijd van Azure is beïnvloed. Maak vervolgens een nieuwe ondersteuningsaanvraag.

Onder **Type probleem** selecteert u **Facturering** en vervolgens **Terugbetalingsaanvraag** onder **Probleemtype**.

Voeg details toe om op te geven dat u een aanvraag indient voor een SLA-tegoed, en noem zowel de datum/tijd/tijdzone als de betrokken services (virtuele machines, websites, enzovoort)

Controleer tot slot uw contactgegevens en selecteer **Maken** om uw aanvraag in te dienen.

Voor een aantal services gelden voorwaarden voordat de SLA van toepassing is. Voor virtuele machines moeten bijvoorbeeld twee of meer instanties in dezelfde beschikbaarheidsset zijn geïmplementeerd.

Zie [Service Level Agreements](https://azure.microsoft.com/support/legal/sla/) en de documentatie met de [SLA-samenvatting voor Azure-services](https://azure.microsoft.com/support/legal/sla/summary/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het gebruik van [bestedingslimieten](../manage/spending-limit.md) om te voorkomen dat u teveel uitgeeft.
- Begin met het [analyseren van uw Azure-kosten](../costs/quick-acm-cost-analysis.md).