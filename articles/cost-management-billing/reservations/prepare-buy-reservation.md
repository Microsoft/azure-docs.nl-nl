---
title: Azure-reservering aanschaffen
description: Meer informatie over belangrijke punten die u helpen om een Azure-reservering te kopen.
author: bandersmsft
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/12/2021
ms.author: banders
ms.openlocfilehash: 13a9e3ad1dcdfa230d757230e3fdea91e4ee9d23
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310390"
---
# <a name="buy-a-reservation"></a>Een reservering kopen

Azure-reserveringen helpen u geld te besparen door een toezegging te doen voor een abonnement van één of drie jaar voor veel Azure-resources. Bekijk vóórdat u een toezegging doet om een reservering aan te schaffen, eerst de volgende secties om u voor te bereiden op de aankoop.

## <a name="who-can-buy-a-reservation"></a>Wie kan een reservering kopen

Als u een reservering wilt kopen, moet u een eigenaarsrol of reserveringskopersrol hebben in een Azure-abonnement van het type Enterprise (MS-AZR-0017P of MS-AZR-0148P) of betalen per gebruik (MS-AZR-0003P of MS-AZR-0023P) of Microsoft-klantovereenkomst. Cloud Solution Providers kunnen Azure-reserveringen aanschaffen via Azure Portal of het  [Partnercentrum](/partner-center/azure-reservations) .

Enterprise Agreement-klanten (EA) kunnen het aantal aankopen voor EA-beheerders beperken door de optie **Gereserveerde instanties toevoegen** in de EA-portal uit te schakelen. EA-beheerders moeten het toegangsniveau van een eigenaar of reserveringskoper hebben op ten minste één EA-abonnement om een reservering aan te schaffen. De optie is nuttig voor bedrijven die hun reserveringen willen laten aanschaffen door een gecentraliseerd team.

Reserveringskorting is alleen van toepassing op resources die zijn gekoppeld aan abonnementen die via Enterprise, een Cloud Solution Provider (CSP), een Microsoft-klantovereenkomst of afzonderlijk abonnementen met tarieven voor betalen per gebruik zijn aangeschaft.

## <a name="scope-reservations"></a>Reserveringen koppelen

U kunt een reservering aan een abonnement of aan resourcegroepen koppelen. Bij het instellen van het bereik voor een reservering selecteert u op welk punt de reserveringskorting van toepassing is. Wanneer u de reservering aan een resourcegroep koppelt, is reserveringskorting alleen van toepassing op de resourcegroep, niet op het hele abonnement.

### <a name="reservation-scoping-options"></a>Opties voor het koppelen van reserveringen

U beschikt over drie opties om een reservering te koppelen, afhankelijk van uw behoeften:

- **Bereik van één resourcegroep**: de reserveringskorting wordt alleen toegepast op de overeenkomende resources in de geselecteerde resourcegroep.
- **Bereik van één abonnement**: de reserveringskorting wordt toegepast op de overeenkomende resources in het geselecteerde abonnement.
- **Gedeeld bereik**: de reserveringskorting wordt toegepast op overeenkomende resources binnen in aanmerking komende abonnementen die zich in de factureringscontext bevinden.
    - Voor Enterprise Agreement-klanten is de inschrijving de factureringscontext. Het gedeelde reserveringsbereik bevat meerdere Active Directory-tenants in een registratie.
    - Voor Microsoft-klantovereenkomst-klanten is het factuurbereik het factureringsprofiel.
    - Voor afzonderlijke abonnementen met tarieven voor betalen per gebruik is het factureringsbereik alle in aanmerking komende abonnementen die zijn gemaakt door de accountbeheerder.

Wanneer u reserveringskorting op uw gebruik toepast, wordt de reservering in deze volgorde door Azure verwerkt:

1. Reserve ringen met één resource groeps bereik
2. Reserve ringen met één abonnements bereik
3. Reserve ringen met een gedeeld bereik (meerdere abonnementen), eerder beschreven

U kunt het bereik altijd bijwerken nadat u een reservering hebt gekocht. Ga hiervoor naar de reservering, klik op **Configuratie** en koppel de reservering opnieuw. Het opnieuw koppelen van een reservering wordt niet als commerciële transactie beschouwd. Uw reserveringstermijn blijft ongewijzigd. Zie [Het bereik bijwerken na aanschaf van een reservering](manage-reserved-vm-instance.md#change-the-reservation-scope) voor meer informatie over het bijwerken van het bereik.

![Voorbeeld van het wijzigen van een reserveringsbereik](./media/prepare-buy-reservation/rescope-reservation-resource-group.png)

## <a name="discounted-subscription-and-offer-types"></a>Typen abonnementen en aanbiedingen met korting

Reserveringskortingen zijn van toepassing op de volgende in aanmerking komende typen abonnementen en aanbiedingen.

- Enterprise Agreement (aanbiedingsnummers: MS-AZR-0017P of MS-AZR-0148P)
- Abonnementen met een Microsoft-klantovereenkomst.
- Afzonderlijke abonnementen met tarieven voor betalen naar gebruik (aanbiedingsnummers: MS-AZR-0003P of MS-AZR-0023P)
- CSP-abonnementen

Voor resources die voor een abonnement met andere typen aanbiedingen worden uitgevoerd, wordt geen reserveringskorting verkregen.

## <a name="purchase-reservations"></a>Reserveringen aanschaffen

U kunt reserveringen aanschaffen bij de Azure-portal, API's, PowerShell, CLI. Lees de volgende artikelen die van toepassing zijn, wanneer u klaar bent om een reservering aan te schaffen:

- [App Service](prepay-app-service.md)
- [Azure Cache voor Redis](../../azure-cache-for-redis/cache-reserved-pricing.md)
- [Cosmos DB](../../cosmos-db/cosmos-db-reserved-capacity.md)
- [Databricks](prepay-databricks-reserved-capacity.md)
- [Data Explorer](/azure/data-explorer/pricing-reserved-capacity)
- [Disk Storage](../../virtual-machines/disks-reserved-capacity.md)
- [Dedicated Host](../../virtual-machines/prepay-dedicated-hosts-reserved-instances.md)
- [Softwareabonnementen](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Storage](../../storage/blobs/storage-blob-reserved-capacity.md)
- [SQL Database](../../azure-sql/database/reserved-capacity-overview.md)
- [Azure Database for PostgreSQL](../../postgresql/concept-reserved-pricing.md)
- [Azure Database for MySQL](../../mysql/concept-reserved-pricing.md)
- [Azure Database for MariaDB](../../mariadb/concept-reserved-pricing.md)
- [Azure Synapse Analytics](prepay-sql-data-warehouse-charges.md)
- [Virtuele machines](../../virtual-machines/prepay-reserved-vm-instances.md)

## <a name="buy-reservations-with-monthly-payments"></a>Reserveringen kopen met maandelijkse betalingen

U kunt reserveringen aanschaffen met maandelijkse betalingen. In tegenstelling tot een aankoop vooraf waarbij u het volledige bedrag betaalt, worden bij de optie voor maandelijkse betaling de totale kosten van de reservering gelijkmatig verdeeld over alle maanden in de periode. De totale kosten van betalingen vooraf en per maand voor reserveringen zijn hetzelfde en u hoeft ook geen toeslag te betalen wanneer u voor maandelijks betalen kiest.

Als een reservering is aangeschaft met behulp van een Microsoft-klantovereenkomst (MCA), kan uw maandbedrag variëren op basis van de wisselkoers van de huidige maand voor uw lokale valuta.

Maandelijkse betalingen zijn niet beschikbaar voor: Databricks, SUSE Linux-reserveringen, Red Hat-abonnementen en Azure Red Hat OpenShift-licenties.

### <a name="view-payments-made"></a>Gedane betalingen weergeven

U kunt gedane betalingen weergeven met behulp van API's en gebruiksgegevens of in de kostenanalyse. Voor reserveringen die maandelijks worden betaald, wordt de frequentiewaarde weergegeven als **Terugkerend** in gebruiksgegevens en de reserveringskosten-API. Voor reserveringen die vooraf zijn betaald, wordt de waarde weergegeven als **Eenmalig**.

Maandelijkse aankopen worden weergegeven in de standaardweergave van de kostenanalyse. Pas het filter **Aankoop** toe op **Kostentype** en het filter **Terugkerend** op **Frequentie** om alle aankopen te bekijken. Als u alleen reserveringen wilt weergeven, past u een filter toe op **Reservering**.

![Voorbeeld van de aanschafkosten van een reservering in de kostenanalyse](./media/prepare-buy-reservation/cost-analysis.png)

### <a name="exchange-and-refunds"></a>Omruiling en terugbetaling

Net als bij andere reserveringen is terugbetaling of omruiling mogelijk van reserveringen die zijn aangeschaft met maandelijkse facturering. 

Wanneer u een reservering met maandelijkse betaling omruilt, moeten de kosten voor de totale levensduur van de nieuwe aankoop hoger zijn dan de resterende betalingen die worden geannuleerd voor de geretourneerde reservering. Er zijn geen andere limieten of kosten verbonden aan omruiling. U kunt een reservering die vooraf is betaald, omruilen voor een nieuwe reservering die maandelijks wordt gefactureerd. De waarde van de levensduur van de nieuwe reservering moet echter hoger zijn dan de waarde naar rato van de reservering die wordt geretourneerd.

Als u een reservering annuleert die maandelijks wordt betaald, worden geannuleerde toekomstige betalingen in rekening gebracht met een restitutielimiet van USD 50.000.

Zie [Selfservice voor omruiling en terugbetaling van Azure-reserveringen](exchange-and-refund-azure-reservations.md) voor meer informatie over omruiling en terugbetaling.

## <a name="reservation-notifications"></a>Reserveringsmeldingen

Afhankelijk van de manier waarop u voor uw Azure-abonnement betaalt, worden er reserveringsmeldingen per e-mail naar de volgende gebruikers in uw organisatie verzonden. Meldingen worden verzonden voor verschillende gebeurtenissen, zoals: 

- Kopen
- Reserveringen die binnenkort verlopen
- Vervaldatum
- Verlenging
- Opzegging
- Gewijzigd bereik

Voor klanten met EA-abonnementen:

- Meldingen worden alleen naar contactpersonen voor EA-meldingen verzonden.
- Gebruikers die aan een reservering zijn toegevoegd met behulp van de machtiging voor Azure RBAC (IAM), krijgen geen enkele e-mailmelding.

Voor klanten met afzonderlijk abonnementen:

- De koper ontvangt een melding over de aankoop.
- Op het moment van de aankoop krijgt de eigenaar van het factureringsaccount van het abonnement een melding over de aankoop.
- De accounteigenaar ontvangt alle andere meldingen.

## <a name="next-steps"></a>Volgende stappen

- [Reserveringen voor Azure-resources beheren](manage-reserved-vm-instance.md)