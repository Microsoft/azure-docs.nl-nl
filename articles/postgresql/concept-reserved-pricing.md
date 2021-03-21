---
title: Prijzen voor gereserveerde berekeningen-Azure Database for PostgreSQL-één server
description: Vooruitbetalen voor Azure Database for PostgreSQL Compute-resources met gereserveerde capaciteit
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/16/2020
ms.openlocfilehash: 9b8dafa4a69358b3f6f09551ac426b908750e2f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98735469"
---
# <a name="prepay-for-azure-database-for-postgresql---single-server-compute-resources-with-reserved-capacity"></a>Vooruitbetalen voor Azure Database for PostgreSQL-reken resources met één server met gereserveerde capaciteit

Azure Database for PostgreSQL helpt u bij het besparen van geld door vooraf te betalen voor reken resources vergeleken met prijzen voor betalen per gebruik. Met Azure Database for PostgreSQL gereserveerde capaciteit kunt u voor een periode van één of drie jaar vooraf een toezeg ging voor een PostgreSQL-server maken om een aanzienlijke korting op de reken kosten te krijgen. Als u Azure Database for PostgreSQL gereserveerde capaciteit wilt kopen, moet u de Azure-regio, het implementatie type, de prestatie-laag en de periode opgeven. </br>

U hoeft de reserve ring niet toe te wijzen aan specifieke Azure Database for PostgreSQL-servers. Een Azure Database for PostgreSQL die al wordt uitgevoerd (of nieuwe geïmplementeerde versies), krijgt automatisch het voor deel van de gereserveerde prijzen. Als u een reserve ring aanschaft, betaalt u voor de reken kosten vooraf een periode van één of drie jaar. Zodra u een reserve ring koopt, worden de Azure data base for PostgreSQL-reken kosten die overeenkomen met de reserverings kenmerken, niet meer in rekening gebracht tegen de betalen naar gebruik-tarieven. Een reserve ring geldt niet voor software-, netwerk-of opslag kosten die zijn gekoppeld aan de PostgreSQL-database servers. Aan het einde van de reserverings termijn verloopt het facturerings voordeel en de Azure Database for PostgreSQL worden gefactureerd op basis van de betalen naar gebruik-prijs. Reserve ringen worden niet automatisch vernieuwd. Voor prijs informatie raadpleegt u de [Azure database for PostgreSQL gereserveerde capaciteits aanbieding](https://azure.microsoft.com/pricing/details/postgresql/). </br>

> [!IMPORTANT]
> Prijzen voor gereserveerde capaciteit zijn beschikbaar voor de Azure Database for PostgreSQL in de implementatie opties voor [één server](./overview.md#azure-database-for-postgresql---single-server) en [grootschalige Citus](./overview.md#azure-database-for-postgresql--hyperscale-citus) . Zie [Deze pagina](concepts-hyperscale-reserved-pricing.md)voor meer informatie over de prijzen van RI op grootschalige (Citus).

U kunt Azure Database for PostgreSQL gereserveerde capaciteit kopen in de [Azure Portal](https://portal.azure.com/). Betaal [vooraf of per maand](../cost-management-billing/reservations/prepare-buy-reservation.md) voor de reservering. De gereserveerde capaciteit kopen:

* U moet de rol van eigenaar zijn voor minstens één bedrijf of een afzonderlijk abonnement met betalen per gebruik-tarieven.
* Voor Enterprise Agreements moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA Portal](https://ea.azure.com/). Als deze instelling is uitgeschakeld, moet u een EA-beheerder zijn voor het abonnement.
* Voor de Cloud Solution Provider (CSP)-programma kunnen alleen beheerders of verkoop medewerkers Azure Database for PostgreSQL gereserveerde capaciteit kopen. </br>

De details over de manier waarop zakelijke klanten en betalen per gebruik-klanten in rekening worden gebracht voor reserverings aankopen, Zie [het gebruik van Azure-reserve ringen voor uw Enter prise-inschrijving](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) en [inzicht krijgen in het gebruik van Azure-reserve ringen voor uw abonnement op basis van betalen naar gebruik](../cost-management-billing/reservations/understand-reserved-instance-usage.md).


## <a name="determine-the-right-server-size-before-purchase"></a>De juiste server grootte bepalen vóór de aankoop

De grootte van de reserve ring moet worden gebaseerd op de totale reken tijd die wordt gebruikt door de bestaande of binnenkort gedistribueerde servers binnen een bepaalde regio en met dezelfde prestatie-laag en hardwarematige generatie.</br>

Stel bijvoorbeeld dat u een GEN5-Data Base voor algemeen gebruik uitvoert: 32 vCore PostgreSQL, en twee door het geheugen geoptimaliseerde GEN5 – 16 vCore PostgreSQL-data bases. Verder moet u in de volgende maand een aanvullende algemene doel stelling GEN5 – 8 vCore-database server en één door het geheugen geoptimaliseerde GEN5-32 vCore-database server gebruiken. Stel dat u weet dat u deze resources ten minste één jaar nodig hebt. In dit geval moet u een 40-(32 + 8) vCores aanschaffen, een reserve ring van één jaar voor de enkelvoudige data base-GEN5 en een 64 (2x16 + 32) vCore één jaar reserve ring voor single data base Optimized-GEN5


## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Azure Database for PostgreSQL gereserveerde capaciteit kopen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Alle services** > **Reserveringen**.
3. Selecteer **toevoegen** en selecteer vervolgens in het deel venster reserve ringen **Azure database for PostgreSQL** om een nieuwe reserve ring voor uw postgresql-data bases aan te schaffen.
4. Vul de vereiste velden in. Bestaande of nieuwe data bases die overeenkomen met de kenmerken die u selecteert, komen in aanmerking voor de korting op gereserveerde capaciteit. Het werkelijke aantal Azure Database for PostgreSQL servers dat de korting krijgt, is afhankelijk van het bereik en de geselecteerde hoeveelheid.


:::image type="content" source="media/concepts-reserved-pricing/postgresql-reserved-price.png" alt-text="Overzicht van gereserveerde prijzen":::


In de volgende tabel worden de vereiste velden beschreven.

| Veld | Beschrijving |
| :------------ | :------- |
| Abonnement   | Het abonnement dat wordt gebruikt om te betalen voor de reserve ring van de Azure Database for PostgreSQL gereserveerde capaciteit. Voor de betalings methode voor het abonnement worden de kosten vooraf in rekening gebracht voor de reserve ring van de Azure Database for PostgreSQL gereserveerde capaciteit. Het abonnements type moet een Enter prise Agreement zijn (nummer van de aanbieding: MS-AZR-0017P of MS-AZR-0148P) of een afzonderlijke overeenkomst met betalen per gebruik-prijs (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P). Voor een Enterprise-abonnement worden de kosten in mindering gebracht op het saldo van Azure-vooruitbetaling (voorheen financiële toezegging) of in rekening gebracht als overschrijding. Voor een individueel abonnement met betalen per gebruik-tarieven worden de kosten in rekening gebracht op basis van de credit card of factuur betalings methode voor het abonnement.
| Bereik | Het bereik van de vCore-reserve ring kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u het volgende selecteert: </br></br> **Gedeeld**, de vCore-reserverings korting wordt toegepast op Azure database for PostgreSQL servers die worden uitgevoerd in een abonnement binnen uw facturerings context. Voor zakelijke klanten is het gedeelde bereik de inschrijving en omvat alle abonnementen binnen de inschrijving. Voor betalen per gebruik-klanten bestaat het gedeelde bereik uit alle abonnementen op gebruiksbasis gemaakt door de accountbeheerder.</br></br> **Eén abonnement**, de vCore-reserverings korting wordt toegepast op Azure database for PostgreSQL servers in dit abonnement. </br></br> **Eén resource groep**, de reserverings korting wordt toegepast op Azure database for PostgreSQL servers in het geselecteerde abonnement en de geselecteerde resource groep in dat abonnement.
| Region | De Azure-regio die wordt gedekt door de Azure Database for PostgreSQL gereserveerde capaciteits reservering.
| Implementatie type | Het resource type Azure Database for PostgreSQL waarvoor u de reserve ring wilt kopen.
| Prestatie niveau | De servicelaag voor de Azure Database for PostgreSQL-servers.
| Termijn | Één jaar
| Aantal | De hoeveelheid reken resources die worden aangeschaft in de Azure Database for PostgreSQL gereserveerde capaciteits reservering. De hoeveelheid is een aantal vCores in de geselecteerde Azure-regio en-prestatie-laag die worden gereserveerd en de facturerings korting krijgt. Als u bijvoorbeeld werkt met of plant om een Azure Database for PostgreSQL servers uit te voeren met de totale reken capaciteit van GEN5 16 vCores in de regio VS-Oost, zou u de hoeveelheid instellen op 16 om het voor deel van alle servers te maximaliseren.

## <a name="cancel-exchange-or-refund-reservations"></a>Reserveringen annuleren, ruilen of terugbetalen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) voor meer informatie.

## <a name="vcore-size-flexibility"></a>flexibiliteit van vCore-grootte

met de flexibiliteit van vCore-grootte kunt u binnen een prestatie-laag en-regio omhoog of omlaag schalen zonder verlies van het voor deel van de gereserveerde capaciteit. Als u schaalt naar een hogere vCores dan de gereserveerde capaciteit, wordt u gefactureerd voor de overtollige vCores met de prijzen voor betalen per gebruik.


## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

De vCore-reserverings korting wordt automatisch toegepast op het aantal Azure Database for PostgreSQL servers dat overeenkomt met het reserverings bereik en de kenmerken voor de gereserveerde capaciteit van de Azure Database for PostgreSQL. U kunt het bereik van de Azure Data Base voor PostgreSQL gereserveerde capaciteits reservering bijwerken via Azure Portal, Power shell, CLI of via de API.

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

* [Wat zijn Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md)?
* [Azure-reserveringen beheren](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Korting op Azure-reserveringen begrijpen](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure-reserveringen in CSP-programma (Cloud Solution Provider) van partnercentrum](/partner-center/azure-reservations)