---
title: Vooruitbetalen voor reken kracht met gereserveerde capaciteit-Azure cache voor redis
description: Vooruitbetalen voor Azure cache voor redis Compute-resources met gereserveerde capaciteit
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 2f3472aa495042749410bc0b9635f0924a02e1fd
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98598557"
---
# <a name="prepay-for-azure-cache-for-redis-compute-resources-with-reserved-capacity"></a>Vooruitbetalen voor Azure cache voor redis Compute-resources met gereserveerde capaciteit

Azure cache voor redis helpt u nu bij het besparen van geld door vooraf te betalen voor reken resources vergeleken met de prijzen voor betalen per gebruik. Met Azure cache voor de gereserveerde capaciteit van redis maakt u voor een periode van één of drie jaar vooraf een toezeg ging voor de cache om een aanzienlijke korting op de reken kosten te krijgen. Als u Azure cache wilt kopen voor redis gereserveerde capaciteit, moet u de Azure-regio, de servicelaag en de periode opgeven.

U hoeft de reserve ring niet toe te wijzen aan een specifieke Azure-cache voor redis-exemplaren. Een Azure-cache die al wordt uitgevoerd voor redis of die zojuist is geïmplementeerd, krijgt automatisch het voor deel van gereserveerde prijzen, tot de gereserveerde cache grootte. Als u een reserve ring aanschaft, betaalt u de reken kosten vooraf voor een periode van één of drie jaar. Zodra u een reserve ring koopt, worden de kosten voor Azure-cache voor redis die overeenkomen met de reserverings kenmerken niet meer in rekening gebracht tegen de betalen naar gebruik-tarieven. Een reserve ring dekt geen netwerk-of opslag kosten die zijn gekoppeld aan de cache. Aan het einde van de reserverings termijn verloopt het facturerings voordeel en de Azure-cache voor redis wordt gefactureerd op basis van de betalen naar gebruik-prijs. Reserve ringen worden niet automatisch vernieuwd. Voor prijs informatie raadpleegt u de [Azure-cache voor redis gereserveerde capaciteit](https://azure.microsoft.com/pricing/details/cache).

U kunt in de [Azure Portal](https://portal.azure.com/)Azure-cache kopen voor redis gereserveerde capaciteit. De gereserveerde capaciteit kopen:

* U moet de rol van eigenaar zijn voor minstens één bedrijf of een afzonderlijk abonnement met betalen per gebruik-tarieven.
* Voor Enterprise Agreements moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA Portal](https://ea.azure.com/). Als deze instelling is uitgeschakeld, moet u een EA-beheerder zijn voor het abonnement.
* Voor de Cloud Solution Provider (CSP)-programma kunnen alleen de beheerders of verkoop agenten Azure-cache kopen voor redis gereserveerde capaciteit.

Zie het [gebruik van Azure-reserve ringen voor uw Enter prise-inschrijving](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) en inzicht krijgen in het gebruik van [Azure-reserve ringen voor uw abonnement op basis van betalen per gebruik](../cost-management-billing/reservations/understand-reserved-instance-usage.md)voor meer informatie over de wijze waarop klanten en betalen per gebruik in rekening worden gebracht voor reserverings aankopen.


## <a name="determine-the-right-cache-size-before-purchase"></a>De juiste cache grootte bepalen vóór de aankoop

De grootte van de reserve ring moet worden gebaseerd op de totale hoeveelheid geheugen die wordt gebruikt door de bestaande of binnenkort geïmplementeerde cache binnen een bepaalde regio en met dezelfde servicelaag.

Stel bijvoorbeeld dat u twee caches gebruikt, één bij 13 GB en de andere bij 26 GB. U hebt beide voor ten minste één jaar nodig. Stel dat u van plan bent om de bestaande 13 GB-caches voor een maand te schalen naar 26 GB om te voldoen aan de behoeften van uw seizoen en vervolgens terug te schalen. In dit geval kunt u 1 P2-cache en 1 P3-cache of 3 P2-caches aanschaffen voor een reserve ring van één jaar om de besparingen te maximaliseren. U ontvangt korting op de totale hoeveelheid cache geheugen die u reserveert, onafhankelijk van de manier waarop deze hoeveelheid wordt toegewezen in uw caches.


## <a name="buy-azure-cache-for-redis-reserved-capacity"></a>Azure-cache kopen voor redis gereserveerde capaciteit

U kunt een gereserveerde VM-instantie kopen in de [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/). Betaal [vooraf of per maand](../cost-management-billing/reservations/prepare-buy-reservation.md) voor de reservering.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Alle services** > **Reserveringen**.
3. Selecteer **toevoegen** en selecteer in het deel venster Inkoop reserveringen de optie **Azure-cache voor redis** om een nieuwe reserve ring voor uw caches te kopen.
4. Vul de vereiste velden in. Bestaande of nieuwe data bases die overeenkomen met de kenmerken die u selecteert, komen in aanmerking voor de korting op gereserveerde capaciteit. Het werkelijke aantal van uw Azure-cache voor redis-instanties die de korting krijgen, is afhankelijk van het bereik en de geselecteerde hoeveelheid.


![Overzicht van gereserveerde prijzen](media/cache-reserved-pricing/cache-reserved-price.png)


In de volgende tabel worden de vereiste velden beschreven.

| Veld | Beschrijving |
| :------------ | :------- |
| Abonnement   | Het abonnement dat wordt gebruikt voor de betaling van de Azure-cache voor gereserveerde capaciteits reservering redis. Voor de betalings methode voor het abonnement worden de kosten vooraf in rekening gebracht voor de Azure-cache voor gereserveerde capaciteits reservering redis. Het abonnements type moet een Enter prise Agreement zijn (nummer van de aanbieding: MS-AZR-0017P of MS-AZR-0148P) of een afzonderlijke overeenkomst met betalen per gebruik-prijs (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P). Voor een Enter prise-abonnement worden de kosten afgetrokken van de Azure-voor uitbetaling van de inschrijving (voorheen monetaire toezeg ging genoemd)-saldo of in rekening gebracht als overschrijding. Voor een individueel abonnement met betalen per gebruik-tarieven worden de kosten in rekening gebracht op basis van de credit card of factuur betalings methode voor het abonnement.
| Bereik | Het bereik van de reserve ring kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u het volgende selecteert: </br></br> **Gedeeld**, de reserverings korting wordt toegepast op Azure cache voor redis-exemplaren die worden uitgevoerd in abonnementen binnen uw facturerings context. Voor zakelijke klanten is het gedeelde bereik de inschrijving en omvat alle abonnementen binnen de inschrijving. Voor betalen per gebruik-klanten bestaat het gedeelde bereik uit alle abonnementen op gebruiksbasis gemaakt door de accountbeheerder.</br></br> **Eén abonnement**: de reserverings korting wordt toegepast op Azure cache voor redis-exemplaren in dit abonnement. </br></br> **Eén resource groep**, de reserverings korting wordt toegepast op Azure cache voor redis-exemplaren in het geselecteerde abonnement en de geselecteerde resource groep in dat abonnement.
| Regio | De Azure-regio die wordt gedekt door de Azure-cache voor gereserveerde capaciteits reservering van redis.
| Prijscategorie | De servicelaag voor de Azure-cache voor redis-servers.
| Term | Eén jaar of drie jaar
| Aantal | De hoeveelheid reken resources die in de Azure-cache worden gekocht voor gereserveerde capaciteits reservering redis. De hoeveelheid is een aantal caches in de geselecteerde Azure-regio en service tier die worden gereserveerd en de facturerings korting krijgt. Als u bijvoorbeeld uitvoert of een Azure-cache wilt uitvoeren voor redis-servers met de totale cache capaciteit van 26 GB in de regio VS-Oost, geeft u de hoeveelheid op die u het equivalent van 26 GB geeft om het voor deel van alle caches te maximaliseren. Dit kan 1 P3-of 2 P2-caches zijn.

## <a name="cancel-exchange-or-refund-reservations"></a>Reserveringen annuleren, ruilen of terugbetalen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) voor meer informatie.

## <a name="cache-size-flexibility"></a>Flexibiliteit van cache grootte

Met de flexibiliteit van cache grootte kunt u binnen een servicelaag en regio omhoog of omlaag schalen zonder het voor deel van de gereserveerde capaciteit te verliezen.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

De reserverings korting wordt automatisch toegepast op de Azure-cache voor redis-exemplaren die overeenkomen met het reserverings bereik en de kenmerken. U kunt het bereik van de reserve ring bijwerken via de Azure Portal, Power shell, Azure CLI of de API.

*  Zie [inzicht in de korting voor Azure-reserve ringen](../cost-management-billing/reservations/understand-azure-cache-for-redis-reservation-charges.md) voor meer informatie over hoe gereserveerde capaciteits kortingen worden toegepast op Azure cache voor redis.

* Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

    * [Wat zijn Azure-reserveringen?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
    * [Azure-reserveringen beheren](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
    * [Korting op Azure-reserveringen begrijpen](../cost-management-billing/reservations/understand-reservation-charges.md)
    * [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](../cost-management-billing/reservations/understand-reservation-charges-mysql.md)
    * [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
    * [Azure-reserveringen in CSP-programma (Cloud Solution Provider) van partnercentrum](/partner-center/azure-reservations)