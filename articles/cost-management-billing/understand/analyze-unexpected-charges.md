---
title: Onverwachte Azure-kosten analyseren
description: Meer informatie over het analyseren van onverwachte kosten voor uw Azure-abonnement.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: f6df8c6ff0db74798b71d57feae91e5245f2a6c2
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028715"
---
# <a name="analyze-unexpected-charges"></a>Onverwachte kosten analyseren

De infrastructuur van de cloudresource die u voor uw organisatie hebt gemaakt, is waarschijnlijk ingewikkeld. Veel Azure-resourcetypen kunnen verschillende soorten kosten hebben. Azure-resources zijn mogelijk eigendom van verschillende teams in uw organisatie en zij kunnen verschillende typen factureringsmodellen hebben die van toepassing zijn op verschillende resources. Als u meer inzicht wilt krijgen in de kosten, kunt u om te beginnen deze analyseren met behulp van een of meer van de strategieën in de volgende secties.

## <a name="review-invoice-for-resource-responsible-for-charge"></a>Factuur voor de resource die verantwoordelijk is voor de kosten beoordelen

Hoe u uw Azure-services aanschaft, helpt u te bepalen welke methodologie en hulpprogramma's er voor u beschikbaar zijn wanneer u de resource identificeert die voor de kosten verantwoordelijk is. Als u wilt bepalen welke methodologie op u van toepassing is, [ moet u eerst vaststellen welk type Azure-aanbieding u hebt](../costs/understand-cost-mgt-data.md#determine-your-offer-type). Bepaal vervolgens uw klantcategorie in de lijst met [ondersteunde Azure-aanbiedingen](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers).

De volgende artikelen bevatten gedetailleerde stappen waarin wordt uitgelegd hoe u uw factuur kunt controleren op basis van uw klanttype. In elk artikel vindt u instructies voor het downloaden van een CSV-bestand met daarin gegevens over het gebruik en de kosten over een bepaalde factureringsperiode.

- [Controleproces voor factuur voor Betalen per gebruik](review-individual-bill.md#charges)
- [Controleproces voor factuur voor Enterprise Agreement](review-enterprise-agreement-bill.md)
- [Controleproces voor factuur voor Microsoft-klantovereenkomst](review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Controleproces voor Microsoft Partner-overeenkomst](review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

Op uw Azure-factuur worden alle kosten voor die maand per _meter_ geaggregeerd. Meters worden gebruikt om het gebruik van een resource gedurende een bepaalde periode bij te houden, en worden gebruikt om uw factuur te berekenen. Wanneer u één Azure-resource maakt, zoals een virtuele machine, worden er een of meer meterexemplaren voor die resource gemaakt.

Filter het CSV-bestand met het gebruik op de _MeterName_ zoals die wordt weergegeven op de factuur die u wilt analyseren, zodat u alle regelitems ziet die van toepassing zijn op de meter. De _InstanceID_ voor het regelitem komt overeen met de werkelijke Azure-resource die de kosten heeft gegenereerd.

Wanneer u de desbetreffende resource hebt geïdentificeerd, kunt u Kostenanalyse in Azure Cost Management gebruiken om de kosten verder te analyseren die zijn gerelateerd aan de resource. Zie [Beginnen met analyseren van kosten](../costs/quick-acm-cost-analysis.md) voor meer informatie over het gebruik van Kostenanalyse.

## <a name="review-invoiced-charges-in-cost-analysis"></a>Gefactureerde kosten in kosten analyse bekijken

Als u de factuurgegevens in de Azure-portal wilt bekijken, navigeert u naar kostenanalyse voor het bereik dat hoort bij de factuur die u analyseert. Selecteer de weergave **Factuurgegevens**. Factuurgegevens laten u de kosten van de factuur zien.

[![Voorbeeld met factuurgegevens](./media/analyze-unexpected-charges/invoice-details.png)](./media/analyze-unexpected-charges/invoice-details.png#lightbox)

Als u de factuurgegevens bekijkt, kunt u de service identificeren die onverwachte kosten heeft en bepalen welke resources rechtstreeks zijn gekoppeld aan de resource in de kostenanalyse. Als u bijvoorbeeld kosten wilt analyseren voor de virtuele-machineservice, gaat u naar de weergave **Geaccumuleerde kosten**. Stel vervolgens de granulariteit in op **Dagelijks** en filter kosten op **Servicenaam: Virtuele machines** en groepskosten op **Resource**.

[![Voorbeeld van samengevoegde kosten voor virtuele machines](./media/analyze-unexpected-charges/virtual-machines.png)](./media/analyze-unexpected-charges/virtual-machines.png#lightbox)

## <a name="identify-spikes-in-cost-over-time"></a>Pieken in de kosten in de loop van de tijd identificeren

Soms weet u mogelijk niet welke recente kosten de wijzigingen in uw gefactureerde kosten hebben veroorzaakt. Als u wilt weten wat er is gewijzigd, kunt u Kostenanalyse gebruiken om [een uitsplitsing per dag of per maand te zien van kosten die in de loop van de tijd zijn gemaakt](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month). Nadat u de weergave hebt gemaakt, kunt u de kosten groeperen op **service** of **resource** om de wijzigingen te identificeren. U kunt uw weergave ook wijzigen in een **lijndiagram** voor een betere visualisatie van de gegevens.

![Voorbeeld van de kosten in de loop van de tijd in Kostenanalyse](./media/analyze-unexpected-charges/costs-over-time.png)

## <a name="determine-resource-pricing-and-billing-model"></a>Resourceprijzen en het factureringsmodel bepalen

Eén enkele resource kan zorgen voor een toename van de kosten voor meerdere Azure-producten en -services. Bekijk de pagina [Azure-prijzen per product](https://azure.microsoft.com/pricing/#product-pricing) voor meer informatie over de prijzen voor elke Azure-service. Een virtuele machine (VM) die in Azure is gemaakt, kan bijvoorbeeld de volgende meters hebben om het gebruik bij te houden. Elk van deze kan verschillende prijzen hebben.

- Rekenuren
- Uren IP-adres
- Inkomende gegevensoverdracht
- Uitgaande gegevensoverdracht
- Standard - Beheerde schijf
- Standard - Beheerde schijfbewerkingen
- Standard - IO - Schijf
- Standard - IO - Lezen blok-blob
- Standard - IO - Schrijven blok-blob
- Standard - IO - Verwijderen blok-blob

Wanneer de virtuele machine wordt gemaakt, begint elke meter gebruiksrecords te verzenden. Het gebruik en de metertarieven worden bijgehouden in het Azure-metersysteem. In het CSV-gebruiksbestand kunt u de meters zien die zijn gebruikt voor het berekenen van uw factuur.

## <a name="find-people-responsible-for-the-resource-and-engage"></a>Stel vast welke personen verantwoordelijk zijn voor de resource en spreek ze erop aan

Vaak weet het team dat verantwoordelijk is voor een bepaalde resource, dat er iets is veranderd met betrekking tot een resource. Het is nuttig om het team erbij te betrekken als u wilt vaststellen waarom er bepaalde kosten zijn gemaakt. Het verantwoordelijke team kan de resource bijvoorbeeld onlangs hebben gemaakt, de SKU ervan hebben bijgewerkt (waardoor de resourcetarieven zijn gewijzigd) of de workload voor de resource hebben verhoogd vanwege wijzigingen in de code. Lees ook de volgende secties voor meer technieken om te bepalen wie eigenaar is van een resource.

### <a name="analyze-the-audit-logs-for-the-resource"></a>De auditlogboeken voor de resource analyseren

Als u gemachtigd bent om een resource weer te geven, zou u ook toegang moeten hebben tot de auditlogboeken. Raadpleeg de logboeken om de gebruiker te vinden die verantwoordelijk is voor de meest recente wijzigingen aan een resource. Zie [Gebeurtenissen in het Azure-activiteitenlogboek weergeven en ophalen](../../azure-monitor/platform/activity-log.md#view-the-activity-log) voor meer informatie.

### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>Gebruikersmachtigingen voor het bovenliggende bereik van de resource analyseren

Personen met schrijftoegang tot een abonnement of resourcegroep beschikken meestal over informatie over de resources die zijn gemaakt. Ze zouden het doel van een resource moeten kunnen verklaren of u kunnen verwijzen naar de persoon die het wel weet. Zie [Toegang voor een gebruiker tot Azure-resources controleren](../../role-based-access-control/check-access.md) als u wilt weten hoe u kunt vaststellen welke personen machtigingen voor een abonnementsbereik hebben. U kunt een vergelijkbaar proces voor resourcegroepen gebruiken.

## <a name="get-help-to-identify-charges"></a>Hulp krijgen bij het identificeren van kosten

Als u de voorgaande strategieën hebt gebruikt en u nog steeds niet weet waar bepaalde kosten vandaan komen, of als u hulp nodig hebt bij factureringsproblemen, kunt u [een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [hoe u uw cloudinvesteringen kunt optimaliseren met Azure Cost Management](../costs/cost-mgt-best-practices.md).