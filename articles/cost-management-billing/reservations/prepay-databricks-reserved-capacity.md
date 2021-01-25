---
title: De kosten voor Azure Databricks optimaliseren door hiervoor vooraf te betalen
description: Ontdek hoe u geld kunt besparen door van tevoren gereserveerde capaciteit voor Azure Databricks te kopen.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 390a8b421a7b34391bde689e4b968fa98cdbaf76
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599173"
---
# <a name="optimize-azure-databricks-costs-with-a-pre-purchase"></a>De kosten voor Azure Databricks optimaliseren door hiervoor vooraf te betalen

U kunt besparen op de kosten voor Azure Databricks-eenheden (DBU's) door van tevoren Azure Databricks-doorvoereenheden (DBCU's) voor één of drie jaar te kopen. U kunt de vooraf gekochte DBCU's op elk gewenst moment tijdens de looptijd gebruiken. Anders dan VM's verlopen vooraf gekochte eenheden niet op uurbasis. Ze zijn dan ook op elk gewenst moment tijdens de looptijd te gebruiken.

Elk gebruik van Azure Databricks wordt automatisch in mindering gebracht op de gereserveerde capaciteit. U hoeft de vooraf gekochte DBCU's niet afzonderlijk te implementeren of toe te wijzen aan uw Azure Databricks-werkruimten om de korting te verrekenen met uw DBU-gebruik.

De korting voor vooruitbetaling geldt alleen voor het DBU-gebruik. Andere kosten, zoals die voor berekeningen, opslag en netwerken, worden afzonderlijk in rekening gebracht.

## <a name="determine-the-right-size-to-buy"></a>Bepalen hoeveel DBCU's u moet kopen

Vooraf gekochte Databricks-capaciteit is van toepassing op alle Databricks-workloads en -servicelagen. U kunt de aankoop vooraf beschouwen als een pool met vooraf betaalde Databricks-doorvoereenheden. Het gebruik wordt in mindering gebracht op de pool, ongeacht de workload of de laag. Hierbij wordt de volgende verhouding gehanteerd:

| **Workload** | **Verhouding DBU-toepassing - Standard-laag** | **Verhouding DBU-toepassing - Premium-laag** |
| --- | --- | --- |
| Data Analytics | 0,4 | 0,55 |
| Data Engineering | 0,15 | 0,30 |
| Data Engineering Light | 0,07 | 0,22 |

Als er bijvoorbeeld een hoeveelheid DBU's wordt gebruikt voor Gegevensanalyse - Standard-laag, worden deze na vermenigvuldiging met 0,4 in mindering gebracht op de pool vooraf gekochte Databricks-doorvoereenheden.

Bereken voordat u tot aanschaf overgaat, het totale aantal DBU's dat voor verschillende workloads en servicelagen wordt verbruikt. Reken de DBU's aan de hand van de hierboven genoemde verhoudingen om naar DBCU's en maak vervolgens een prognose van het totale gebruik in het komende jaar of de komende drie jaar.

## <a name="purchase-databricks-commit-units"></a>Databricks-doorvoereenheden kopen

U kunt Databricks-abonnementen kopen in de [Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D). Voor de aanschaf van gereserveerde capaciteit moet u de rol van eigenaar hebben voor ten minste één Enterprise Agreement.

- U moet beschikken over de rol Eigenaar voor minstens één Enterprise Agreement (aanbiedingsnummers: MS-AZR-0017P of MS-AZR-0148P), of Microsoft-klantovereenkomst of afzonderlijk abonnement met betalen-naar-gebruik-tarieven (aanbiedingsnummers: MS-AZR-0003P of MS-AZR-0023P).
- Voor EA-abonnementen moet de optie Gereserveerde instanties toevoegen zijn ingeschakeld in de EA Portal. Of, als deze instelling is uitgeschakeld, moet u een EA-beheerder van het abonnement zijn.
- Voor Enterprise Agreements moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA Portal](https://ea.azure.com/). Als die instelling is uitgeschakeld, moet u een EA-beheerder van het abonnement zijn.

**Ga als volgt te werk om gereserveerde capaciteit te kopen:**

1. Ga naar de [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22Databricks%22%7D).
1. Selecteer een abonnement. Selecteer in de lijst **Abonnementen** het abonnement dat u wilt gebruiken om voor de gereserveerde capaciteit te betalen. Via de betalingswijze voor het abonnement worden de kosten voor de gereserveerde capaciteit vooraf in rekening gebracht. De kosten worden in mindering gebracht op het saldo van Azure-vooruitbetaling (voorheen financiële toezegging) van de inschrijving of in rekening gebracht als overschrijding.
1. Selecteer een bereik. Gebruik de lijst **Bereik** om een abonnementsbereik te selecteren:
    - **Bereik van één resourcegroep**: de reserveringskorting wordt alleen toegepast op de overeenkomende resources in de geselecteerde resourcegroep.
    - **Bereik van één abonnement**: de reserveringskorting wordt toegepast op de overeenkomende resources in het geselecteerde abonnement.
    - **Gedeeld bereik**: de reserveringskorting wordt toegepast op overeenkomende resources binnen in aanmerking komende abonnementen die zich in de factureringscontext bevinden. Voor Enterprise Agreement-klanten is de factureringscontext de inschrijving.
1. Selecteer hoeveel Azure Databricks-doorvoereenheden u wilt kopen en voltooi de aankoop.


![Voorbeeld van de aankoop van Azure Databricks-capaciteit in de Azure-portal](./media/prepay-databricks-reserved-capacity/data-bricks-pre-purchase.png)

## <a name="change-scope-and-ownership"></a>Bereik en eigendom wijzigen

Na aankoop kunt u de volgende typen wijzigingen aanbrengen in een reservering:

- Het bereik van de reservering bijwerken
- Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)

U kunt de vooraf gekochte pool Databricks-doorvoereenheden niet splitsen of samenvoegen met een andere pool. Raadpleeg [Reserveringen voor Azure-resources beheren](manage-reserved-vm-instance.md) voor meer informatie over het beheren van reserveringen.

## <a name="cancellations-and-exchanges"></a>Annuleren en omwisselen

Vooraf gekochte Databricks-capaciteit kan niet worden geannuleerd of omgewisseld. Alle aankopen zijn definitief.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:
  - [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md)
  - [Inzicht in hoe Azure Databricks-korting op basis van vooraf gekochte DBCU's wordt toegepast](reservation-discount-databricks.md)
  - [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)
