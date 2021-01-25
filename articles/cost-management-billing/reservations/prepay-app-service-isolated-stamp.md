---
title: Opslaan voor Azure App Service met gereserveerde capaciteit
description: Lees meer informatie over besparen op zegelkosten voor Azure App Service Isolated met gereserveerde capaciteit.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: fb463d80e8cb980505df825d6f22d75f8a5c5f5a
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599199"
---
# <a name="save-costs-for-azure-app-service-isolated-stamp-fee-with-reserved-capacity"></a>Bespaar op zegelkosten voor Azure App Service Isolated met gereserveerde capaciteit

U kunt besparen op zegelkosten voor Azure App Service Isolated door u vast te leggen voor een reservering voor uw zegelgebruik voor de duur van drie jaar. Als u gereserveerde capaciteit voor Isolated-zegelkosten wilt kopen, moet u de Azure-regio kiezen waar de zegel wordt geïmplementeerd en hoeveel zegels u wilt kopen.

Wanneer u een reservering koopt, wordt het Isolated-zegelkostengebruik dat met de reserveringskenmerken overeenkomt, niet meer tegen het tarief voor betalen per gebruik in rekening gebracht. De reservering wordt automatisch toegepast op het aantal Isolated-zegels dat overeenkomt met het gereserveerde capaciteitsbereik en de regio. U hoeft geen reservering toe te wijzen aan een Isolated-zegel. De reservering is niet van toepassing op werkrollen, dus alle andere resources die aan de zegel zijn gekoppeld, worden afzonderlijk in rekening gebracht.

Wanneer de gereserveerde capaciteit vervalt, blijven Isolated-zegels actief, maar wordt u gefactureerd tegen het tarief voor betalen per gebruik. Reserveringen worden niet automatisch vernieuwd.

## <a name="determine-the-right-reservation-to-purchase"></a>Bepalen welke reservering u moet kopen

Door een reservering te kopen, legt u zich vast voor het gebruik van een gereserveerde hoeveelheid voor de komende drie jaar. Controleer uw gebruiksgegevens om te bepalen hoeveel App Service Isolated-zegels u consistent gebruikt en mogelijk gaat gebruiken.

Zorg bovendien ervoor dat u begrijpt hoe een Linux-of Windows-meter wordt verzonden via de Isolated-zegel.

- Via een lege Isolated-zegel wordt standaard de Windows-zegelmeter verzonden. Dit is bijvoorbeeld het geval als er geen werkrollen zijn geïmplementeerd. Deze meter blijft hetzelfde als er Windows-werkrollen op de zegel worden geïmplementeerd.
- De meter wordt echter gewijzigd in een Linux-zegelmeter als u een Linux-werkrol implementeert.
- In gevallen waarin zowel Linux-als Windows-werkrollen worden geïmplementeerd, wordt de Windows-meter verzonden via de zegel.

De zegelmeter kan tijdens de levensduur dus veranderen van Windows in Linux en omgekeerd.

Koop Windows-zegelreserveringen als u een of meer Windows-werkrollen op de zegel hebt. De enige situatie waarin u een Linux-zegelreservering moet kopen, is als u _alleen_ Linux-werkrollen op de zegel wilt.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Gereserveerde capaciteit voor Isolated-zegels kopen

U kunt gereserveerde capaciteit voor Isolated-zegels kopen in [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). Betaal [vooraf of per maand](./prepare-buy-reservation.md) voor de reservering. Als u gereserveerde capaciteit wilt kopen, moet u de eigenaarsrol hebben voor ten minste één Enterprise-abonnement of een afzonderlijk abonnement met tarieven voor betalen per gebruik.

- Voor Enterprise-abonnementen moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in [EA Portal](https://ea.azure.com/). Als de instelling is uitgeschakeld, moet u een EA-beheerder zijn.
- Voor het programma Cloud Solution Provider (CSP) kunnen alleen beheerders of verkoopmedewerkers gereserveerde Azure Synapse Analytics-capaciteit kopen.

**Ga als volgt te werk om gereserveerde capaciteit te kopen:**

1. Ga naar [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Selecteer een abonnement. Gebruik de lijst **Abonnementen** om het abonnement te kiezen dat wordt gebruikt om te betalen voor de gereserveerde capaciteit. Via de betalingswijze voor het abonnement worden de kosten voor de gereserveerde capaciteit in rekening gebracht. Het abonnementstype moet een Enterprise-overeenkomst zijn (nummers van aanbieding: MS-AZR-0017P of MS-AZR-0148P) of Betalen per gebruik (nummers van aanbieding: MS-AZR-0003P of MS-AZR-0023P) of een CSP-abonnement.
    - Voor een Enterprise-abonnement worden de kosten in mindering gebracht op het saldo van Azure-vooruitbetaling (voorheen financiële toezegging) of in rekening gebracht als overschrijding.
    - Voor een Betalen per gebruik-abonnement worden de kosten in rekening gebracht op de creditcard of de factuurbetalingswijze van het abonnement.
1. Selecteer een waarde voor **Bereik** om een abonnementsbereik te kiezen.
    - **Bereik van één resourcegroep**: de reserveringskorting wordt alleen toegepast op de overeenkomende resources in de geselecteerde resourcegroep.
    - **Bereik van één abonnement**: de reserveringskorting wordt toegepast op de overeenkomende resources in het geselecteerde abonnement.
    - **Gedeeld bereik**: de reserveringskorting wordt toegepast op overeenkomende resources binnen in aanmerking komende abonnementen die zich in de factureringscontext bevinden. Voor Enterprise Agreement-klanten is de inschrijving de factureringscontext. Voor afzonderlijke abonnementen met tarieven voor betalen per gebruik is het factureringsbereik alle in aanmerking komende abonnementen die zijn gemaakt door de accountbeheerder.
1. Selecteer een waarde voor **Regio** om een Azure-regio te kiezen die wordt gedekt door de gereserveerde capaciteit en voeg de reservering toe aan de winkelwagen.
1. Selecteer een Isolated-abonnementstype en klik vervolgens op **Selecteren**.  
    ![Voorbeeld](./media/prepay-app-service-isolated-stamp/app-service-isolated-stamp-select.png)
1. Voer het aantal te reserveren App Service Isolated-zegels in. Met een aantal van drie krijgt u bijvoorbeeld drie gereserveerde zegels per regio. Klik op **Next: Controleren en kopen**.
1. Controleer uw bestelling en klik op **Nu kopen**.

Na de aankoop kunt u op elk gewenst moment naar [Reserveringen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) gaan om de status van de aankoop te bekijken.

## <a name="cancel-exchange-or-refund-reservations"></a>Annulering, omwisseling of terugbetaling van reserveringen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfservice voor ruiling en terugbetaling van Azure-reserveringen](exchange-and-refund-azure-reservations.md) voor meer informatie.

## <a name="discount-application-shown-in-usage-data"></a>Kortingstoepassing weergeven in gebruiksgegevens

Uw gebruiksgegevens kosten niets voor het deel waarvoor u een reserveringskorting verkrijgt. In de gebruiksgegevens wordt de reserveringskorting weergegeven voor elk zegelexemplaar in elke reservering.

Zie [Kosten en gebruik van Enterprise Agreement-reserveringen ophalen](understand-reserved-instance-usage-ea.md) voor meer informatie over het weergeven van reserveringskorting in gebruiksgegevens als u een Enterprise Agreement-klant (EA) bent. Zie anders [Meer informatie over het gebruik van Azure-reserveringen voor uw afzonderlijke abonnement met tarieven voor betalen per gebruik](understand-reserved-instance-usage.md).

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:
  - [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md)
  - [Meer informatie over hoe een korting voor Azure App Service Isolated-zegelreserveringen wordt toegepast](reservation-discount-app-service-isolated-stamp.md)
  - [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)