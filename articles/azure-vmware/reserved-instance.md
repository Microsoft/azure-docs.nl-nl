---
title: Bespaar kosten met het gereserveerde exemplaar van Azure VMware-oplossing
description: Meer informatie over het kopen van een gereserveerde instantie voor de Azure VMware-oplossing.
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 227ea9274f70cc58468e7e0b3ddcf8239f5b7f5b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99550972"
---
# <a name="save-costs-with-azure-vmware-solution"></a>Bespaar kosten met de Azure VMware-oplossing

Wanneer u een gereserveerde instantie van [Azure VMware-oplossing](introduction.md)doorvoert, bespaart u geld.  De reserverings korting wordt automatisch toegepast op de actieve Azure VMware-oplossing hosts die overeenkomen met het reserverings bereik en de kenmerken. Een gereserveerde instantie koopt alleen op het reken deel van uw gebruik en bevat software licentie kosten. 

## <a name="purchase-restriction-considerations"></a>Overwegingen voor de aankoop beperking

Gereserveerde instanties zijn beschikbaar met enkele uitzonde ringen.

-   **Clouds** -reserve ringen zijn alleen beschikbaar in de regio's die worden vermeld op de pagina [beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=azure-vmware) .

-   **Onvoldoende quotum** : voor een reserverings scope in één/gedeeld abonnement moet het quotum van de host zijn opgenomen in het abonnement voor het nieuwe gereserveerde exemplaar. U kunt een [aanvraag voor quotum verhoging maken](enable-azure-vmware-solution.md) om dit probleem op te lossen.

-   **Aanbiedings recht**: u hebt een [Azure Enterprise overeenkomst (EA)](../cost-management-billing/manage/ea-portal-agreements.md) met micro soft nodig.

-   **Capaciteits beperkingen** : in zeldzame omstandigheden beperkt Azure de aanschaf van nieuwe reserve ringen voor Azure VMware Solution host sku's vanwege een geringe capaciteit in een regio.

## <a name="buy-a-reservation"></a>Een reservering kopen

U kunt een gereserveerd exemplaar van een Azure VMware Solution host-exemplaar kopen in de [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D).

U kunt de reserve ring [vooraf of met maandelijkse betalingen](../cost-management-billing/reservations/prepare-buy-reservation.md)betalen.

Deze vereisten zijn van toepassing op het kopen van een gereserveerde, toegewezen host-instantie:

-   U moet een rol van *eigenaar* zijn voor ten minste één EA-abonnement of een abonnement met een betalen naar gebruik-tarief.

-   Voor EA-abonnementen moet u de optie **gereserveerde instanties toevoegen** inschakelen in de [EA-Portal](https://ea.azure.com/). Als deze optie is uitgeschakeld, moet u een EA-beheerder zijn voor het abonnement om deze in te scha kelen.

-   Voor het abonnement van een Azure-plan voor Cloud Solution Provider (CSP) moet de partner de gereserveerde instanties kopen in de Azure Portal van de klant. 

### <a name="buy-reserved-instances-for-an-ea-subscription"></a>Gereserveerde instanties voor een EA-abonnement kopen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

2. Selecteer **Alle services** > **Reserveringen**.

3. Selecteer **Nu kopen** en selecteer vervolgens **Azure VMware-oplossing**.

4. Voer de vereiste velden in. De geselecteerde kenmerken die overeenkomen met de Azure VMware Solution-hosts komen in aanmerking voor de reserverings korting.  Kenmerken zijn onder andere de SKU, regio's (indien van toepassing) en het bereik. Met het bereik voor een reservering selecteert u waarop de reserveringskorting van toepassing is.

   Als u een EA-overeenkomst hebt, kunt u de **optie meer toevoegen** gebruiken om snel instanties toe te voegen. De optie is niet beschikbaar voor andere typen abonnementen.

   | Veld        |  Beschrijving |
   | ------------ | ------------ |
   | Abonnement | Het abonnement dat wordt gebruikt om te betalen voor de reserve ring. Via de betalingswijze voor het abonnement worden de kosten voor de reservering in rekening gebracht. Het abonnements type moet een Enter prise Agreement zijn (nummer van de aanbieding: MS-AZR-0017P of MS-AZR-0148P), micro soft-klant overeenkomst of een afzonderlijk abonnement met betalen per gebruik-tarieven (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P). De kosten worden in mindering gebracht op het saldo van Azure-vooruitbetaling (voorheen financiële toezegging), indien beschikbaar, of in rekening gebracht als overschrijding. Voor een abonnement met betalen per gebruik-tarieven worden de kosten in rekening gebracht voor de credit card van het abonnement of een factuur betalings methode. |
   | Bereik        | Het bereik van de reserve ring kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u het volgende selecteert:<br><ul><li><b>Bereik van één resource groep</b> : past de reserverings korting alleen toe op de overeenkomende resources in de geselecteerde resource groep.</li><li><b>Bereik van één abonnement</b> : past de reserverings korting toe op de overeenkomende resources in het geselecteerde abonnement.</li><li><b>Gedeeld bereik</b> : past de reserverings korting toe op overeenkomende resources in in aanmerking komende abonnementen in de facturerings context. Voor EA-klanten is de facturerings context de inschrijving. Voor afzonderlijke abonnementen met tarieven voor betalen per gebruik is het factureringsbereik alle in aanmerking komende abonnementen die zijn gemaakt door de accountbeheerder.</li></ul>       |
   | Region       | De Azure-regio die wordt gedekt door de reserve ring.   |
   | Grootte van de host    | AV36    |
   | Termijn         | Eén jaar of drie jaar.  |
   | Aantal     | Het aantal exemplaren dat moet worden gekocht binnen de reserve ring. De hoeveelheid is het aantal actieve Azure VMware-oplossings hosts dat de facturerings korting kan krijgen.    |

### <a name="buy-reserved-instances-for-a-csp-subscription"></a>Gereserveerde instanties voor een CSP-abonnement kopen

Csp's die gereserveerde instanties voor hun klanten willen aanschaffen, moeten de **beheerder namens** (administrate) in de documentatie van het [partner centrum](/partner-center/azure-plan-manage)gebruiken. Raadpleeg voor meer informatie de [administrate-video (beheerder namens)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) .

1. Meld u aan bij [Partner Center](https://partner.microsoft.com).

2. Selecteer **CSP** om toegang te krijgen tot het gebied **klanten** .

3. Vouw klant gegevens uit en selecteer **Microsoft Azure Management Portal**. 

   :::image type="content" source="media/reserved-instances/csp-partner-center-aobo.png" alt-text="Het gebied klanten van micro soft-partner centrum" lightbox="media/reserved-instances/csp-partner-center-aobo.png":::

4. Selecteer in het Azure Portal **alle services**-  >  **reserve ringen**.

5. Selecteer **Nu kopen** en selecteer vervolgens **Azure VMware-oplossing**.

   :::image type="content" source="media/reserved-instances/csp-buy-ri-azure-portal.png" alt-text="Microsoft Azure-portal reserve ringen" lightbox="media/reserved-instances/csp-buy-ri-azure-portal.png":::

6. Voer de vereiste velden in. De geselecteerde kenmerken die overeenkomen met de Azure VMware Solution-hosts komen in aanmerking voor de reserverings korting.  Kenmerken zijn onder andere de SKU, regio's (indien van toepassing) en het bereik. Met het bereik voor een reservering selecteert u waarop de reserveringskorting van toepassing is.

   | Veld        |  Beschrijving |
   | ------------ | ------------ |
   | Abonnement | Het abonnement dat wordt gebruikt om te betalen voor de reserve ring. Via de betalingswijze voor het abonnement worden de kosten voor de reservering in rekening gebracht. Het abonnements type moet een in aanmerking komende abonnement zijn, in dit geval een CSP-abonnement|
   | Bereik        | Het bereik van de reserve ring kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u het volgende selecteert:<br><ul><li><b>Bereik van één resource groep</b> : past de reserverings korting alleen toe op de overeenkomende resources in de geselecteerde resource groep.</li><li><b>Bereik van één abonnement</b> : past de reserverings korting toe op de overeenkomende resources in het geselecteerde abonnement.</li><li><b>Gedeeld bereik</b> : past de reserverings korting toe op overeenkomende resources in in aanmerking komende abonnementen in de facturerings context. Voor EA-klanten is de facturerings context de inschrijving. Voor afzonderlijke abonnementen met tarieven voor betalen per gebruik is het factureringsbereik alle in aanmerking komende abonnementen die zijn gemaakt door de accountbeheerder.</li></ul>       |
   | Region       | De Azure-regio die wordt gedekt door de reserve ring.   |
   | Grootte van de host    | AV36    |
   | Termijn         | Eén jaar of drie jaar.  |
   | Aantal     | Het aantal exemplaren dat moet worden gekocht binnen de reserve ring. De hoeveelheid is het aantal actieve Azure VMware-oplossings hosts dat de facturerings korting kan krijgen.     |

Zie [Azure-reserve ringen weer geven als een Cloud Solution Provider (CSP)](../cost-management-billing/reservations/how-to-view-csp-reservations.md) -artikel voor meer informatie over het weer geven van de aangeschafte reserve ringen voor uw klant.

## <a name="usage-data-and-reservation-usage"></a>Gebruiks gegevens en reserverings gebruik

Uw gebruik dat een reserverings korting oplevert, heeft een goede prijs van nul. U kunt zien welk exemplaar van de Azure VMware-oplossing de reserverings korting voor elke reserve ring heeft ontvangen.

Voor meer informatie over hoe reserverings kortingen worden weer gegeven in gebruiks gegevens:

- Zie voor EA-klanten [inzicht in het gebruik van Azure-reserve ringen voor uw Enter prise-inschrijving](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)
- Zie [het gebruik van Azure-reserve ringen voor uw betalen per gebruik-abonnement](../cost-management-billing/reservations/understand-reserved-instance-usage.md) voor afzonderlijke abonnementen

## <a name="change-a-reservation-after-purchase"></a>Een reserve ring wijzigen na aankoop

U kunt deze wijzigingen aanbrengen in een reserve ring na de aankoop:

-   Het bereik van de reservering bijwerken

-   Flexibiliteit van de instantie grootte (indien van toepassing)

-   Eigendom

U kunt ook een reserve ring in kleinere segmenten splitsen of reserve ringen samen voegen. Geen van de wijzigingen veroorzaakt een nieuwe commerciële trans actie of wijzigt de eind datum van de reserve ring.

Zie voor meer informatie over door CSP beheerde reserve ringen [verkoop Microsoft Azure reserve ringen aan klanten met behulp van partner centrum, de Azure portal of api's](/partner-center/azure-reservations).



>[!NOTE]
>Zodra u de reserve ring hebt aangeschaft, kunt u deze typen wijzigingen niet rechtstreeks aanbrengen:
>
> - Een bestaande reserverings regio
> - SKU
> - Aantal
> - Duur
>
>U kunt echter wel een reserve ring *uitwisselen* als u wijzigingen wilt aanbrengen.

## <a name="cancel-exchange-or-refund-reservations"></a>Reserveringen annuleren, ruilen of terugbetalen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) voor meer informatie.

Csp's kunnen reserve ringen annuleren, uitwisselen of terugbetalen, met bepaalde beperkingen, gekocht voor hun klant. Zie voor meer informatie [beheren, annuleren, uitwisselen of terugbetalen Microsoft Azure reserve ringen voor klanten](/partner-center/azure-reservations-manage).

## <a name="next-steps"></a>Volgende stappen

Nu u een gereserveerde instantie van Azure VMware-oplossing hebt gekocht, wilt u mogelijk meer informatie over:

- [Een Azure VMware-oplossings evaluatie maken](../migrate/how-to-create-azure-vmware-solution-assessment.md).
- [DHCP voor Azure VMware-oplossing beheren](manage-dhcp.md).
- [Levenscyclus beheer van virtuele machines met Azure VMware-oplossingen](lifecycle-management-of-azure-vmware-solution-vms.md).
 