---
title: Bespaar op Azure Synapse Analytics-kosten met gereserveerde Azure-capaciteit
description: Meer informatie over hoe u kosten bespaart met gereserveerde capaciteit voor Azure Synapse Analytics om zo geld te besparen.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: bd43b668c318b825c5c5b6f36fc1da1055863bed
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599094"
---
# <a name="save-costs-for-azure-synapse-analytics-charges-with-reserved-capacity"></a>Bespaar op Azure Synapse Analytics-kosten met gereserveerde capaciteit

U kunt met Azure Synapse Analytics geld besparen door een reservering vast te leggen voor uw cDWU-gebruik voor de duur van één of drie jaar. Als u Azure Synapse Analytics-reserveringscapaciteit wilt kopen, moet u de Azure-regio en de periode kiezen. Voeg daarna de Azure Synapse Analytics-SKU toe aan uw winkelwagen en kies de hoeveelheid cDWU-eenheden die u wilt aanschaffen.

Wanneer u een reservering aanschaft, wordt het gebruik van Azure Synapse Analytics dat met de reserveringskenmerken overeenkomt niet meer tegen het tarief voor betalen per gebruik in rekening gebracht.

Een reservering omvat geen opslag- en netwerkkosten die samenhangen met het gebruik van Azure Synapse Analytics.

Wanneer de gereserveerde capaciteit vervalt, blijven de Azure Synapse Analytics-exemplaren actief, maar wordt u gefactureerd tegen het tarief voor betalen per gebruik. Reserveringen worden niet automatisch vernieuwd.

Raadpleeg het [aanbod voor Azure Synapse Analytics-reserveringscapaciteit](https://azure.microsoft.com/pricing/details/synapse-analytics/) voor prijsinformatie.

U kunt Azure Synapse Analytics-reserveringscapaciteit kopen in de [Azure-portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade). Betaal [vooraf of per maand](./prepare-buy-reservation.md) voor de reservering. Gereserveerde capaciteit kopen:

- U moet de rol van eigenaar hebben voor ten minste één Enterprise- abonnement of een abonnement op gebruiksbasis.
- Voor Enterprise-abonnementen moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in [EA Portal](https://ea.azure.com/). Als de instelling is uitgeschakeld, moet u een EA-beheerder zijn.
- Voor het programma Cloud Solution Provider (CSP) kunnen alleen beheerders of verkoopmedewerkers gereserveerde Azure Synapse Analytics-capaciteit kopen.

Zie [Het gebruik van Azure-reserveringen voor uw Enterprise-inschrijving begrijpen](understand-reserved-instance-usage-ea.md) en [Inzicht krijgen in het gebruik van Azure-reserveringen voor uw abonnement op gebruiksbasis](understand-reserved-instance-usage.md) voor meer informatie over hoe kosten voor reserveringsaankopen in rekening worden gebracht bij Enterprise-klanten en bij klanten op gebruiksbasis.

## <a name="choose-the-right-size-before-purchase"></a>Het juiste formaat kiezen voordat u overgaat tot aanschaf

De grootte van de Azure Synapse Analytics-reservering moet worden gebaseerd op het totale aantal cDWU-eenheden (compute Data Warehouse Unit) dat u verbruikt. Aankopen worden gedaan in stappen van 100 cDWU.

Stel dat uw totale verbruik van Azure Synapse Analytics DW3000c is. U wilt voor alles gereserveerde capaciteit aanschaffen. U moet dan 30 eenheden van gereserveerde cDWU-capaciteit kopen.

## <a name="buy-azure-synapse-analytics-reserved-capacity"></a>Gereserveerde capaciteit voor Azure Synapse Analytics kopen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Alle services** > **Reserveringen**.
3. Selecteer een abonnement. Gebruik de lijst Abonnementen om het abonnement te kiezen dat wordt gebruikt om te betalen voor de gereserveerde capaciteit. Via de betalingswijze voor het abonnement worden de kosten voor de gereserveerde capaciteit in rekening gebracht. Het abonnementstype moet een Enterprise-overeenkomst zijn (nummers van aanbieding: MS-AZR-0017P of MS-AZR-0148P) of Betalen per gebruik (nummers van aanbieding: MS-AZR-0003P of MS-AZR-0023P).
   - Voor een Enterprise-abonnement worden de kosten in mindering gebracht op het saldo van Azure-vooruitbetaling (voorheen financiële toezegging) of in rekening gebracht als overschrijding.
   - Voor een Betalen per gebruik-abonnement worden de kosten in rekening gebracht op de creditcard of de factuurbetalingswijze van het abonnement.
4. Selecteer een bereik. Gebruik de lijst Bereik om een abonnementsbereik te kiezen.
   - **Bereik van één resourcegroep**: de reserveringskorting wordt alleen toegepast op de overeenkomende resources in de geselecteerde resourcegroep.
   - **Bereik van één abonnement**: de reserveringskorting wordt toegepast op de overeenkomende resources in het geselecteerde abonnement.
   - **Gedeeld bereik**: de reserveringskorting wordt toegepast op overeenkomende resources binnen in aanmerking komende abonnementen die zich in de factureringscontext bevinden. Voor Enterprise Agreement-klanten is de inschrijving de factureringscontext. Voor afzonderlijke abonnementen met tarieven voor betalen per gebruik is het factureringsbereik alle in aanmerking komende abonnementen die zijn gemaakt door de accountbeheerder.
   - Voor Enterprise-klanten is de factureringscontext de inschrijving.
   - Voor betalen per gebruik-klanten bestaat het gedeelde bereik uit alle abonnementen op gebruiksbasis gemaakt door de accountbeheerder.
5. Selecteer een regio om een Azure-regio te kiezen die wordt gedekt door de gereserveerde capaciteit.
6. Kies een hoeveelheid. Voer het aantal van 100 cDWU in dat u wilt kopen.    
   Zo geeft een hoeveelheid van 30 bijvoorbeeld elk uur 3.000 cDWU met gereserveerde capaciteit.
7. Bekijk de kosten voor de Azure Synapse Analytics-capaciteitsreservering in de sectie **Kosten**.
8. Selecteer **Aankoop**.
9. Selecteer **Deze reservering weergeven** om de status van uw aankoop te bekijken.

## <a name="cancel-exchange-or-refund-reservations"></a>Reserveringen annuleren, ruilen of terugbetalen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](exchange-and-refund-azure-reservations.md) voor meer informatie.

Er wordt automatisch een reserveringskorting toegepast op het aantal Azure Synapse Analytics-exemplaren dat overeenkomt met het bereik en de regio van de gereserveerde capaciteit van Azure Synapse Analytics. U kunt het bereik van de gereserveerde capaciteit van Azure Synapse Analytics bijwerken met de [Azure-portal](https://portal.azure.com/), PowerShell, CLI of via de API.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/).

## <a name="next-steps"></a>Volgende stappen

- Zie [Hoe reserveringskortingen van toepassing zijn op Azure Synapse Analytics](prepay-sql-data-warehouse-charges.md) voor meer informatie over hoe reserveringskortingen van toepassing zijn op Azure Synapse Analytics.

- Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:
  - [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md)
  - [Azure-reserveringen beheren](manage-reserved-vm-instance.md)
  - [Korting op Azure-reserveringen begrijpen](understand-reservation-charges.md)
  - [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](understand-reserved-instance-usage.md)
  - [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)