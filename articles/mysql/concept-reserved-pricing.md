---
title: Vooruitbetalen voor reken kracht met gereserveerde capaciteit-Azure Database for MySQL
description: Vooruitbetalen voor Azure Database for MySQL Compute-resources met gereserveerde capaciteit
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: b1f51cd9e9f310c68834c16f83c182250a1a4eaf
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98598025"
---
# <a name="prepay-for-azure-database-for-mysql-compute-resources-with-reserved-capacity"></a>Vooruitbetalen voor Azure Database for MySQL Compute-resources met gereserveerde capaciteit

Azure Database for MySQL helpt u bij het besparen van geld door vooraf te betalen voor reken resources vergeleken met prijzen voor betalen per gebruik. Met Azure Database for MySQL gereserveerde capaciteit maakt u vooraf een toezeg ging op de MySQL-server voor een periode van één of drie jaar om een aanzienlijke korting op de reken kosten te krijgen. Als u Azure Database for MySQL gereserveerde capaciteit wilt kopen, moet u de Azure-regio, het implementatie type, de prestatie-laag en de periode opgeven. </br>

U hoeft de reserve ring niet toe te wijzen aan specifieke Azure Database for MySQL-servers. Een Azure Database for MySQL die al wordt uitgevoerd, krijgt automatisch het voor deel van gereserveerde prijzen. Als u een reserve ring aanschaft, betaalt u de reken kosten vooraf voor een periode van één of drie jaar. Zodra u een reserve ring koopt, worden de kosten voor de Azure Data Base voor MySQL-berekeningen die overeenkomen met de reserverings kenmerken niet meer in rekening gebracht tegen de betalen naar gebruik-tarieven. Een reserve ring geldt niet voor software-, netwerk-of opslag kosten die zijn gekoppeld aan de MySQL-database server. Aan het einde van de reserverings termijn verloopt het facturerings voordeel en de Azure Database for MySQL worden gefactureerd op basis van de betalen naar gebruik-prijs. Reserve ringen worden niet automatisch vernieuwd. Voor prijs informatie raadpleegt u de [Azure database for MySQL gereserveerde capaciteits aanbieding](https://azure.microsoft.com/pricing/details/mysql/). </br>

U kunt Azure Database for MySQL gereserveerde capaciteit kopen in de [Azure Portal](https://portal.azure.com/). Betaal [vooraf of per maand](../cost-management-billing/reservations/prepare-buy-reservation.md) voor de reservering. De gereserveerde capaciteit kopen:

* U moet de rol van eigenaar zijn voor minstens één bedrijf of een afzonderlijk abonnement met betalen per gebruik-tarieven.
* Voor Enterprise Agreements moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA Portal](https://ea.azure.com/). Als deze instelling is uitgeschakeld, moet u een EA-beheerder zijn voor het abonnement.
* Voor de Cloud Solution Provider (CSP)-programma kunnen alleen beheerders of verkoop medewerkers Azure Database for MySQL gereserveerde capaciteit kopen. </br>

De details over de manier waarop zakelijke klanten en betalen per gebruik-klanten in rekening worden gebracht voor reserverings aankopen, Zie [het gebruik van Azure-reserve ringen voor uw Enter prise-inschrijving](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) en [inzicht krijgen in het gebruik van Azure-reserve ringen voor uw abonnement op basis van betalen naar gebruik](../cost-management-billing/reservations/understand-reserved-instance-usage.md).


## <a name="determine-the-right-database-size-before-purchase"></a>De juiste data base-grootte bepalen vóór de aankoop

De grootte van de reserve ring moet worden gebaseerd op de totale reken tijd die wordt gebruikt door de bestaande of binnenkort geïmplementeerde server binnen een bepaalde regio en met dezelfde prestatie-laag en hardwarematige generatie.</br>

Stel bijvoorbeeld dat u een algemeen doel, GEN5-32 vCore MySQL-data base uitvoert en twee geoptimaliseerde geheugen, GEN5 – 16 vCore MySQL-data bases. Verder moet u in de volgende maand een extra algemeen doel, GEN5-32 vCore-database server en één geoptimaliseerd voor geheugen, GEN5-16 vCore-database server, implementeren. Stel dat u weet dat u deze resources ten minste één jaar nodig hebt. In dit geval moet u een 64 (2x32)-vCores aanschaffen, een reserve ring van 1 jaar voor een algemene doel einde van de data base-GEN5 en een 48 (2x16 + 16) vCore 1 jaar reserve ring voor single data base Optimized-GEN5


## <a name="buy-azure-database-for-mysql-reserved-capacity"></a>Azure Database for MySQL gereserveerde capaciteit kopen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Alle services** > **Reserveringen**.
3. Selecteer **toevoegen** en selecteer vervolgens in het deel venster reserve ringen **Azure database for MySQL** om een nieuwe reserve ring voor uw MySQL-data bases aan te schaffen.
4. Vul de vereiste velden in. Bestaande of nieuwe data bases die overeenkomen met de kenmerken die u selecteert, komen in aanmerking voor de korting op gereserveerde capaciteit. Het werkelijke aantal Azure Database for MySQL servers dat de korting krijgt, is afhankelijk van het bereik en de geselecteerde hoeveelheid.


:::image type="content" source="media/concepts-reserved-pricing/mysql-reserved-price.png" alt-text="Overzicht van gereserveerde prijzen":::


In de volgende tabel worden de vereiste velden beschreven.

| Veld | Beschrijving |
| :------------ | :------- |
| Abonnement   | Het abonnement dat wordt gebruikt om te betalen voor de reserve ring van de Azure Database for MySQL gereserveerde capaciteit. Voor de betalings methode voor het abonnement worden de kosten vooraf in rekening gebracht voor de reserve ring van de Azure Database for MySQL gereserveerde capaciteit. Het abonnements type moet een Enter prise Agreement zijn (nummer van de aanbieding: MS-AZR-0017P of MS-AZR-0148P) of een afzonderlijke overeenkomst met betalen per gebruik-prijs (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P). Voor een Enter prise-abonnement worden de kosten afgetrokken van de Azure-voor uitbetaling van de inschrijving (voorheen monetaire toezeg ging genoemd)-saldo of in rekening gebracht als overschrijding. Voor een individueel abonnement met betalen per gebruik-tarieven worden de kosten in rekening gebracht op basis van de credit card of factuur betalings methode voor het abonnement.
| Bereik | Het bereik van de vCore-reserve ring kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u het volgende selecteert: </br></br> **Gedeeld**, de vCore-reserverings korting wordt toegepast op Azure database for MySQL servers die worden uitgevoerd in een abonnement binnen uw facturerings context. Voor zakelijke klanten is het gedeelde bereik de inschrijving en omvat alle abonnementen binnen de inschrijving. Voor betalen per gebruik-klanten bestaat het gedeelde bereik uit alle abonnementen op gebruiksbasis gemaakt door de accountbeheerder.</br></br> **Eén abonnement**, de vCore-reserverings korting wordt toegepast op Azure database for MySQL servers in dit abonnement. </br></br> **Eén resource groep**, de reserverings korting wordt toegepast op Azure database for MySQL servers in het geselecteerde abonnement en de geselecteerde resource groep in dat abonnement.
| Regio | De Azure-regio die wordt gedekt door de Azure Database for MySQL gereserveerde capaciteits reservering.
| Implementatie type | Het resource type Azure Database for MySQL waarvoor u de reserve ring wilt kopen.
| Prestatie niveau | De servicelaag voor de Azure Database for MySQL-servers.
| Term | Één jaar
| Aantal | De hoeveelheid reken resources die worden aangeschaft in de Azure Database for MySQL gereserveerde capaciteits reservering. De hoeveelheid is een aantal vCores in de geselecteerde Azure-regio en-prestatie-laag die worden gereserveerd en de facturerings korting krijgt. Als u bijvoorbeeld werkt met of plant om een Azure Database for MySQL servers uit te voeren met de totale reken capaciteit van GEN5 16 vCores in de regio VS-Oost, zou u de hoeveelheid instellen op 16 om het voor deel van alle servers te maximaliseren.

## <a name="cancel-exchange-or-refund-reservations"></a>Reserveringen annuleren, ruilen of terugbetalen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) voor meer informatie.

## <a name="vcore-size-flexibility"></a>flexibiliteit van vCore-grootte

met de flexibiliteit van vCore-grootte kunt u binnen een prestatie-laag en-regio omhoog of omlaag schalen zonder verlies van het voor deel van de gereserveerde capaciteit. 

## <a name="need-help--contact-us"></a>Hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

De vCore-reserverings korting wordt automatisch toegepast op het aantal Azure Database for MySQL servers dat overeenkomt met het reserverings bereik en de kenmerken voor de gereserveerde capaciteit van de Azure Database for MySQL. U kunt het bereik van de Azure data base for MySQL gereserveerde capaciteits reservering bijwerken via Azure Portal, Power shell, CLI of via de API. </br></br>
Zie Azure Database for MySQL gereserveerde capaciteit beheren voor meer informatie over het beheren van de gereserveerde capaciteit van Azure Database for MySQL.

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

* [Wat zijn Azure Reservations](../cost-management-billing/reservations/save-compute-costs-reservations.md)?
* [Azure-reserveringen beheren](../cost-management-billing/reservations/manage-reserved-vm-instance.md)
* [Korting op Azure-reserveringen begrijpen](../cost-management-billing/reservations/understand-reservation-charges.md)
* [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](../cost-management-billing/reservations/understand-reservation-charges-mysql.md)
* [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
* [Azure-reserveringen in CSP-programma (Cloud Solution Provider) van partnercentrum](/partner-center/azure-reservations)