---
title: Reken kosten met gereserveerde capaciteit opslaan
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Meer informatie over het kopen van Azure SQL Database en de gereserveerde capaciteit van SQL Managed instance om uw reken kosten op te slaan.
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 10/13/2020
ms.openlocfilehash: cacd43502a01352c24f8fcfd85b12aac781dccbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98602508"
---
# <a name="save-costs-for-resources-with-reserved-capacity---azure-sql-database--sql-managed-instance"></a>Bespaar kosten voor resources met gereserveerde capaciteit-Azure SQL Database & SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)] 

Bespaar geld met Azure SQL Database en SQL Managed instance door door te gaan naar een reserve ring voor reken resources vergeleken met prijzen voor betalen per gebruik. Met gereserveerde capaciteit maakt u een toezeg ging voor SQL Database en/of SQL Managed instance voor een periode van één of drie jaar om een aanzienlijke korting op de reken kosten te krijgen. Als u gereserveerde capaciteit wilt kopen, moet u de Azure-regio, het implementatie type, de prestatie-laag en de periode opgeven.

U hoeft de reserve ring niet toe te wijzen aan een specifieke data base of een beheerd exemplaar. Bij bestaande implementaties die al worden uitgevoerd of die nieuw zijn geïmplementeerd, krijgt u automatisch het voor deel. Door een reserve ring te kopen, legt u het gebruik voor de reken kosten voor een periode van één of drie jaar toe. Zodra u een reserve ring koopt, worden de reken kosten die overeenkomen met de reserverings kenmerken niet meer in rekening gebracht tegen de betalen naar gebruik-tarieven. 

Een reserve ring is van toepassing op zowel primaire als factureer bare secundaire reken replica's, maar heeft geen betrekking op software, netwerken of opslag kosten die zijn gekoppeld aan de service. Aan het einde van de reserverings termijn verloopt het facturerings voordeel en wordt de data base of het beheerde exemplaar gefactureerd op basis van de betalen naar gebruik-prijs. Reserve ringen worden niet automatisch verlengd. Voor prijs informatie raadpleegt u de [aanbieding gereserveerde capaciteit](https://azure.microsoft.com/pricing/details/sql-database/managed/).

U kunt gereserveerde capaciteit kopen in de [Azure Portal](https://portal.azure.com). Betaal [vooraf of per maand](../../cost-management-billing/reservations/prepare-buy-reservation.md) voor de reservering. Gereserveerde capaciteit kopen:

- U moet de rol van eigenaar zijn voor minstens één bedrijf of een afzonderlijk abonnement met betalen per gebruik-tarieven.
- Voor Enterprise Agreements moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA Portal](https://ea.azure.com). Als deze instelling is uitgeschakeld, moet u een EA-beheerder zijn voor het abonnement. Gereserveerde capaciteit.

Zie het [gebruik van Azure-reserve ringen voor uw Enter prise-inschrijving](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md) en [inzicht krijgen in het gebruik van Azure-reserve ringen voor uw abonnement voor betalen per gebruik](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)voor meer informatie over hoe klanten en betalen per gebruik in rekening worden gebracht voor reserverings aankopen.

> [!NOTE]
> Bij gereserveerde capaciteit van aankopen worden geen specifieke infrastructuur resources (virtuele machines of knoop punten) voor uw gebruik toegewezen of gereserveerd.

## <a name="determine-correct-size-before-purchase"></a>Bepaal de juiste grootte vóór de aankoop

De grootte van de reserve ring moet worden gebaseerd op de totale reken tijd die wordt gebruikt door de bestaande of binnenkort geïmplementeerde data base of het beheerde exemplaar binnen een bepaalde regio en met dezelfde prestatie-laag en hardwarematige generatie.

Stel bijvoorbeeld dat u één algemeen doel, GEN5-16 vCore elastische pool en twee essentiële GEN5-4 vCore-afzonderlijke data bases gebruikt. Verder is het van plan dat u in de volgende maand een extra algemene doel einde GEN5 – 16 vCore elastische pool en een elastische pool van het bedrijf kritiek GEN5 – 32 vCore hebt. Stel dat u weet dat u deze resources ten minste één jaar nodig hebt. In dit geval moet u een 32 (2x16) vCores 1 jaar-reserve ring aanschaffen voor de enkelvoudige data base/elastische pool algemeen doel-GEN5 en een 40 (2x4 + 32) vCore 1 jaar reserve ring voor één data base/elastische pool, kritiek-GEN5.

## <a name="buy-reserved-capacity"></a>Gereserveerde capaciteit kopen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **Alle services** > **Reserveringen**.
3. Selecteer **toevoegen** en selecteer vervolgens in het deel venster **reserve ringen** **SQL database** om een nieuwe reserve ring voor SQL database aan te schaffen.
4. Vul de vereiste velden in. Bestaande data bases in SQL Database en SQL Managed instance die overeenkomen met de kenmerken die u selecteert, komen in aanmerking voor de gereserveerde capaciteits korting. Het werkelijke aantal data bases of beheerde instanties dat de korting krijgt, is afhankelijk van het bereik en de geselecteerde hoeveelheid.

    ![Scherm afbeelding voor het verzenden van de gereserveerde capaciteits aankoop](./media/reserved-capacity-overview/sql-reserved-vcores-purchase.png)

    In de volgende tabel worden de vereiste velden beschreven.
    
    | Veld      | Beschrijving|
    |------------|--------------|
    |Abonnement|Het abonnement dat wordt gebruikt voor het betalen van de capaciteits reservering. Voor de betalings wijze voor het abonnement worden de kosten vooraf in rekening gebracht voor de reserve ring. Het abonnements type moet een Enter prise Agreement (aanbiedings nummer MS-AZR-0017P of MS-AZR-0148P) of een afzonderlijke overeenkomst met betalen per gebruik-prijs (aanbiedings nummer MS-AZR-0003P of MS-AZR-0023P) zijn. Voor een Enterprise-abonnement worden de kosten in mindering gebracht op het saldo van Azure-vooruitbetaling (voorheen financiële toezegging) of in rekening gebracht als overschrijding. Voor een individueel abonnement met betalen per gebruik-tarieven worden de kosten in rekening gebracht op basis van de credit card of factuur betalings methode voor het abonnement.|
    |Bereik       |Het bereik van de vCore-reserve ring kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u <br/><br/>**Gedeeld**, de vCore-reserverings korting wordt toegepast op de data base of het beheerde exemplaar dat wordt uitgevoerd in abonnementen binnen uw facturerings context. Voor zakelijke klanten is het gedeelde bereik de inschrijving en omvat alle abonnementen binnen de inschrijving. Voor betalen per gebruik-klanten bestaat het gedeelde bereik uit alle abonnementen op gebruiksbasis gemaakt door de accountbeheerder.<br/><br/>**Eén abonnement**, de vCore-reserverings korting wordt toegepast op de data bases of beheerde exemplaren in dit abonnement. <br/><br/>**Eén resource groep**, de reserverings korting wordt toegepast op de exemplaren van data bases of beheerde exemplaren in het geselecteerde abonnement en de geselecteerde resource groep in dat abonnement.|
    |Region      |De Azure-regio die wordt gedekt door de capaciteits reservering.|
    |Implementatie type|Het SQL-resource type waarvoor u de reserve ring wilt aanschaffen.|
    |Prestatie niveau|De servicelaag voor de data bases of beheerde exemplaren. |
    |Termijn        |Eén jaar of drie jaar.|
    |Aantal    |De hoeveelheid reken resources die worden gekocht binnen de capaciteits reservering. De hoeveelheid is een aantal vCores in de geselecteerde Azure-regio en-prestatie-laag die worden gereserveerd en de facturerings korting krijgt. Als u bijvoorbeeld meerdere data bases uitvoert of plant met de totale reken capaciteit van GEN5 16 vCores in de regio VS-Oost, geeft u de hoeveelheid op als 16 om het voor deel voor alle data bases te maximaliseren. |

1. Controleer de kosten van de capaciteits reservering in het gedeelte **kosten** .
1. Selecteer **Aankoop**.
1. Selecteer **deze reserve ring weer geven** om de status van uw aankoop te bekijken.

## <a name="cancel-exchange-or-refund-reservations"></a>Reserveringen annuleren, ruilen of terugbetalen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) voor meer informatie.

## <a name="vcore-size-flexibility"></a>flexibiliteit van vCore-grootte

met de flexibiliteit van vCore-grootte kunt u binnen een prestatie-laag en-regio omhoog of omlaag schalen zonder verlies van het voor deel van de gereserveerde capaciteit. Met gereserveerde capaciteit beschikt u ook over de flexibiliteit om uw warme data bases in en uit elastische Pools (binnen dezelfde regio en prestatie-laag) tijdelijk te verplaatsen als onderdeel van uw normale bewerkingen zonder verlies van het voor deel van de gereserveerde capaciteit. Door een niet-toegepaste buffer in uw reserve ring te bewaren, kunt u de prestatie pieken effectief beheren zonder uw budget te overschrijden.

## <a name="limitation"></a>Beperking

U kunt geen DTU-gebaseerde data bases (Basic, Standard of Premium) reserveren in SQL Database.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Volgende stappen

De vCore-reserverings korting wordt automatisch toegepast op het aantal data bases of beheerde instanties die overeenkomen met het bereik en de kenmerken van de capaciteits reservering. U kunt het bereik van de capaciteits reservering bijwerken via de [Azure Portal](https://portal.azure.com), Power shell, Azure CLI of de API.

Zie [gereserveerde capaciteit beheren](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)voor meer informatie over het beheren van de capaciteits reservering.

Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

- [Wat zijn Azure-reserveringen?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Azure-reserveringen beheren](../../cost-management-billing/reservations/manage-reserved-vm-instance.md)
- [Korting op Azure-reserveringen begrijpen](../../cost-management-billing/reservations/understand-reservation-charges.md)
- [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](../../cost-management-billing/reservations/understand-reserved-instance-usage.md)
- [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](../../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- [Azure-reserveringen in CSP-programma (Cloud Solution Provider) van partnercentrum](/partner-center/azure-reservations)