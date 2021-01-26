---
title: Reken kosten met gereserveerde capaciteit opslaan
description: Meer informatie over het aanschaffen van de gereserveerde capaciteit van Azure Data Factory data flow om uw reken kosten op te slaan.
ms.topic: conceptual
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.date: 01/25/2021
ms.openlocfilehash: 8bea1ff0dcb945e969553a4f643e289a5157f966
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98811660"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-data-factory-data-flows"></a>Bespaar kosten voor resources met gereserveerde capaciteit-Azure Data Factory gegevens stromen

Bespaar geld met Azure Data Factory kosten voor de gegevens stroom door het door voeren van een reserve ring voor reken resources vergeleken met de prijzen voor betalen per gebruik. Met gereserveerde capaciteit maakt u een toezeg ging voor het gebruik van ADF-gegevens gedurende een periode van één of drie jaar om een aanzienlijke korting op de reken kosten te krijgen. Als u gereserveerde capaciteit wilt kopen, moet u de Azure-regio, het reken type, het aantal kernen en de periode opgeven.

U hoeft de reserve ring niet toe te wijzen aan een specifieke Factory of Integration runtime. Bestaande fabrieken of nieuwe geïmplementeerde fabrieken krijgen automatisch het voor deel. Door een reserve ring te kopen, legt u het gebruik van de reken kosten voor de gegevens stroom gedurende een periode van één of drie jaar voor. Zodra u een reserve ring koopt, worden de reken kosten die overeenkomen met de reserverings kenmerken niet meer in rekening gebracht tegen de betalen naar gebruik-tarieven. 

U kunt gereserveerde capaciteit kopen in de [Azure Portal](https://portal.azure.com). Betaal [vooraf of per maand](https://docs.microsoft.com/azure/cost-management-billing/reservations/prepare-buy-reservation.md) voor de reservering. Gereserveerde capaciteit kopen:

- U moet de rol van eigenaar zijn voor minstens één bedrijf of een afzonderlijk abonnement met betalen per gebruik-tarieven.
- Voor Enterprise Agreements moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA Portal](https://ea.azure.com). Als deze instelling is uitgeschakeld, moet u een EA-beheerder zijn voor het abonnement. Gereserveerde capaciteit.

Zie het [gebruik van Azure-reserve ringen voor uw Enter prise-inschrijving](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage-ea) en [inzicht krijgen in het gebruik van Azure-reserve ringen voor uw abonnement voor betalen per gebruik](https://docs.microsoft.com/azure/cost-management-billing/reservations/understand-reserved-instance-usage.md)voor meer informatie over hoe klanten en betalen per gebruik in rekening worden gebracht voor reserverings aankopen.

> [!NOTE]
> Bij gereserveerde capaciteit van aankopen worden geen specifieke infrastructuur resources (virtuele machines of clusters) voor uw gebruik toegewezen of gereserveerd.

## <a name="determine-proper-azure-ir-sizes-needed-before-purchase"></a>Bepaal de juiste Azure IR grootten voordat u deze aanschaft

De grootte van de reserve ring moet worden gebaseerd op de totale reken tijd die wordt gebruikt door de bestaande of binnenkort geïmplementeerde gegevens stromen met behulp van dezelfde Compute-laag.

Stel bijvoorbeeld dat u per uur een pijp lijn uitvoert met behulp van geheugen dat is geoptimaliseerd met 32 kernen. Verder moet u plannen dat u in de volgende maand een extra pijp lijn wilt implementeren die gebruikmaakt van de algemene 64-kernen. Stel dat u weet dat u deze resources ten minste één jaar nodig hebt. In dit geval moet u een reserve ring van 32 kernen van 1 jaar aanschaffen voor gegevens stromen geoptimaliseerd voor geheugen en een reserve ring voor algemeen gebruik van 64 Core 1 jaar.

## <a name="buy-reserved-capacity"></a>Gereserveerde capaciteit kopen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **Alle services** > **Reserveringen**.
3. Selecteer **toevoegen** en selecteer vervolgens in het deel venster **Inkoop reserveringen** de optie **ADF-gegevens stromen** om een nieuwe reserve ring voor gegevens stromen van ADF te kopen.
4. Vul de vereiste velden en kenmerken in die u in aanmerking selecteert om de gereserveerde capaciteits korting te verkrijgen. Het werkelijke aantal gegevens stromen dat de korting krijgt, hangt af van het bereik en de geselecteerde hoeveelheid.
5. Controleer de kosten van de capaciteits reservering in het gedeelte **kosten** .
6. Selecteer **Aankoop**.
7. Selecteer **deze reserve ring weer geven** om de status van uw aankoop te bekijken.

## <a name="cancel-exchange-or-refund-reservations"></a>Reserveringen annuleren, ruilen of terugbetalen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](https://docs.microsoft.com/azure/cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) voor meer informatie.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

- [Korting op Azure-reserveringen begrijpen](data-flow-understand-reservation-charges.md)
