---
title: Prijzen voor gereserveerde berekeningen-Azure Database for PostgreSQL-grootschalige (Citus)
description: Vooruitbetalen voor Azure Database for PostgreSQL-grootschalige (Citus)-reken resources met gereserveerde capaciteit.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: 35d5d196eccb222bf17e0a129fe4e4041b1f6053
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98600725"
---
# <a name="prepay-for-azure-database-for-postgresql---hyperscale-citus-compute-resources-with-reserved-capacity"></a>Vooruitbetalen voor Azure Database for PostgreSQL-grootschalige (Citus) Compute-resources met gereserveerde capaciteit

Azure Database for PostgreSQL – grootschalige (Citus) helpt u nu bij het besparen van geld door vooraf te betalen voor reken resources vergeleken met prijzen voor betalen per gebruik. Met de gereserveerde capaciteit van grootschalige (Citus) kunt u een periode van één of drie jaar vooraf instellen op de Server groep grootschalige (Citus) om een aanzienlijke korting op de reken kosten te krijgen. Als u de gereserveerde capaciteit van grootschalige (Citus) wilt kopen, moet u de Azure-regio, de reserverings periode en de facturerings frequentie opgeven.

> [!IMPORTANT]
> Dit artikel heeft betrekking op gereserveerde capaciteit voor Azure Database for PostgreSQL – grootschalige (Citus). Zie voor meer informatie over gereserveerde capaciteit voor Azure Database for PostgreSQL-één server, [vooruitbetalen voor Azure database for PostgreSQL – Single Server Compute-resources met gereserveerde capaciteit](./concept-reserved-pricing.md).

U hoeft de reserve ring niet toe te wijzen aan specifieke grootschalige (Citus)-Server groepen. Een grootschalige-Server groep (Citus) die al wordt uitgevoerd of die nieuw is geïmplementeerd, krijgt automatisch het voor deel van gereserveerde prijzen. Als u een reserve ring aanschaft, betaalt u voor de reken kosten één jaar of drie jaar. Zodra u een reserve ring koopt, worden de kosten voor de grootschalige (Citus) die overeenkomen met de reserverings kenmerken niet meer in rekening gebracht tegen de betalen naar gebruik-tarieven. 

Een reserve ring geldt niet voor software-, netwerk-of opslag kosten die zijn gekoppeld aan de grootschalige (Citus)-Server groepen. Aan het einde van de reserverings termijn verloopt het facturerings voordeel en worden de grootschalige (Citus)-Server groepen gefactureerd op basis van de betalen naar gebruik-prijs. Reserve ringen worden niet verlengd. Zie voor prijs informatie de [grootschalige-aanbieding (Citus) gereserveerde capaciteit van Azure database for PostgreSQL](https://azure.microsoft.com/pricing/details/postgresql/hyperscale-citus/).

U kunt gereserveerde capaciteit van grootschalige (Citus) kopen in de [Azure Portal](https://portal.azure.com/). Betaal [vooraf of per maand](../cost-management-billing/reservations/prepare-buy-reservation.md) voor de reservering. De gereserveerde capaciteit kopen:

* U moet de rol van eigenaar zijn voor ten minste één Enterprise Overeenkomst (EA) of een afzonderlijk abonnement met betalen naar gebruik-tarieven.
* Voor Enterprise Overeenkomst-abonnementen moet u **gereserveerde instanties toevoegen** inschakelen in de [EA-Portal](https://ea.azure.com/). Als deze instelling is uitgeschakeld, moet u een Enterprise Overeenkomst beheerder zijn voor het abonnement.
* Voor het programma Cloud Solution Provider (CSP) kan alleen de beheerder of de verkoop agent grootschalige (Citus) gereserveerde capaciteit kopen.

Zie voor informatie over hoe Enterprise Overeenkomst klanten en betalen per gebruik-klanten in rekening worden gebracht voor reserverings aankopen:
- [Meer informatie over het gebruik van Azure-reserve ringen voor uw Enterprise Overeenkomst-inschrijving](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Meer informatie over het gebruik van Azure-reserve ringen voor uw abonnement voor betalen naar gebruik](../cost-management-billing/reservations/understand-reserved-instance-usage.md)

## <a name="determine-the-right-server-group-size-before-purchase"></a>De juiste grootte van de Server groep bepalen vóór de aankoop

De grootte van de reserve ring is gebaseerd op de totale reken tijd die wordt gebruikt door de bestaande of binnenkort geïmplementeerde coördinator en worker-knoop punten in grootschalige (Citus) in een bepaalde regio.

Stel bijvoorbeeld dat u één grootschalige-Server groep (Citus) met 16 vCore-coördinator en 3 8 vCore worker-knoop punten uitvoert. Verder gaan we ervan uit dat u in de volgende maand een extra grootschalige-Server groep (Citus) met een 32 vCore-coördinator en 2 4 vCore worker-knoop punten wilt implementeren. Stel dat u deze resources ten minste één jaar nodig hebt.

In dit geval koopt u een reserve ring van één jaar voor:

* Totaal 16 vCores + 32 vCores = 48 vCores voor coördinator knooppunten
* Totaal 3 knoop punten x 8 vCores + 2 knoop punten x 4 vCores = 24 + 8 = 32 vCores voor worker-knoop punten

## <a name="buy-azure-database-for-postgresql-reserved-capacity"></a>Azure Database for PostgreSQL gereserveerde capaciteit kopen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
1. Selecteer **Alle services** > **Reserveringen**.
1. Selecteer **Toevoegen**. Selecteer in het deel venster **reserve ringen** op **Azure database for PostgreSQL** om een nieuwe reserve ring te kopen voor uw postgresql-data bases.
1. Selecteer het **reken type grootschalige (Citus)** dat u wilt kopen en klik op **selecteren**.
1. Controleer de hoeveelheid voor het geselecteerde reken type op het tabblad **producten** .
1. Ga naar het tabblad **kopen en controleren** om je aankoop te volt ooien.

In de volgende tabel worden de vereiste velden beschreven.

| Veld        | Beschrijving                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|--------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Abonnement | Het abonnement dat wordt gebruikt om te betalen voor de reserve ring van de Azure Database for PostgreSQL gereserveerde capaciteit. Voor de betalings methode voor het abonnement worden de kosten vooraf in rekening gebracht voor de reserve ring van de Azure Database for PostgreSQL gereserveerde capaciteit. Het abonnements type moet een Enterprise Overeenkomst zijn (nummer aanbieding: MS-AZR-0017P of MS-AZR-0148P) of een afzonderlijke overeenkomst met betalen per gebruik-prijs (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P). Voor een Enterprise Overeenkomst-abonnement worden de kosten afgetrokken van de Azure-voor uitbetaling van de inschrijving (voorheen de monetaire toezeg ging genoemd)-saldo of in rekening gebracht als overschrijding. Voor een individueel abonnement met betalen per gebruik-tarieven worden de kosten in rekening gebracht op basis van de credit card of factuur betalings methode voor het abonnement.                                                                                  |
| Bereik        | Het bereik van de vCore-reserve ring kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u **gedeeld** selecteert, wordt de vCore-reserverings korting toegepast op grootschalige (Citus)-Server groepen die worden uitgevoerd in abonnementen binnen uw facturerings context. Voor Enterprise Overeenkomst klanten is de gedeelde Scope de inschrijving en omvat alle abonnementen binnen de inschrijving. Voor betalen per gebruik-klanten is de gedeelde Scope alle abonnementen met betalen per gebruik, gemaakt door de account beheerder. Als u **één abonnement** selecteert, wordt de vCore-reserverings korting toegepast op grootschalige (Citus)-Server groepen in dit abonnement. Als u **één resource groep** selecteert, wordt de reserverings korting toegepast op grootschalige-Server groepen (Citus) in het geselecteerde abonnement en de geselecteerde resource groep in dat abonnement. |
| Region       | De Azure-regio die wordt gedekt door de gereserveerde capaciteits reservering Azure Database for PostgreSQL – grootschalige (Citus).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Termijn         | Eén jaar of drie jaar.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |
| Aantal     | De hoeveelheid reken resources die worden aangeschaft binnen de gereserveerde capaciteits reservering grootschalige (Citus). Met name het aantal coördinator-of worker-knoop punten dat is vCores in de geselecteerde Azure-regio die wordt gereserveerd en die de facturerings korting krijgt. Als u bijvoorbeeld (of plant om te worden uitgevoerd) grootschalige (Citus)-Server groepen met de totale reken capaciteit van 64-coördinator knooppunt vCores en 32 worker-knoop punt vCores in de regio VS-Oost, geeft u het aantal op, respectievelijk 64 en 32 voor de coördinator en worker-knoop punten, om het voor deel van alle servers te maximaliseren.                                                                                                                                                                                                                                                     |



## <a name="cancel-exchange-or-refund-reservations"></a>Reserveringen annuleren, ruilen of terugbetalen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [self-service-uitwisseling en terugbetalingen voor Azure-reserve ringen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md)voor meer informatie.

## <a name="vcore-size-flexibility"></a>flexibiliteit van vCore-grootte

met de flexibiliteit van vCore-grootte kunt u de coördinator en worker-knoop punten binnen een regio omhoog of omlaag schalen zonder dat u het voor deel van de gereserveerde capaciteit kwijtraakt.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

De vCore-reserverings korting wordt automatisch toegepast op het aantal grootschalige (Citus)-Server groepen dat overeenkomt met de Azure Database for PostgreSQL gereserveerde capaciteits scope en-kenmerken. U kunt het bereik van de gereserveerde capaciteits reservering voor Azure Database for PostgreSQL – grootschalige (Citus) bijwerken via de Azure Portal, Power shell, de Azure CLI of de API.

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

* [Wat zijn Azure-reserveringen?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
* [Reserveringen beheren](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Meer informatie over de korting voor Azure-reserve ring](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Het gebruik van de reserve ring begrijpen voor uw abonnement voor betalen naar gebruik](../cost-management-billing/reservations/understand-reservation-charges-postgresql.md)
* [Het gebruik van de reserve ring begrijpen voor uw Enterprise Overeenkomst-inschrijving](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure-reserve ringen in het Partner Center-programma Cloud Solution Provider](/partner-center/azure-reservations)