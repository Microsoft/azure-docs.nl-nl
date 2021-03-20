---
title: Kosten optimaliseren voor Azure Disk Storage met reserve ringen
description: Meer informatie over het aanschaffen van Azure Disk Storage reserveringen om kosten te besparen op Premium SSD Managed disks.
author: roygara
ms.author: rogarana
ms.date: 01/30/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: db6575894904e6ced2d4be48fec5961f5b8b8a54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98602632"
---
# <a name="reduce-costs-with-azure-disks-reservation"></a>Kosten verlagen met Azure-schijfreservering

Bespaar op uw Azure Disk Storage gebruik met gereserveerde capaciteit. Azure Disk Storage reserve ringen gecombineerd met Azure Reserved Virtual Machine Instances kunt u de totale kosten van virtuele machines (VM) verlagen. De reserverings korting wordt automatisch toegepast op de overeenkomende schijven in het geselecteerde reserverings bereik. Als gevolg van deze automatische toepassing hoeft u geen reserve ring toe te wijzen aan een beheerde schijf om de kortingen te krijgen.

Kortingen worden per uur toegepast, afhankelijk van het schijf gebruik. Niet-gebruikte gereserveerde capaciteit wordt niet overgedragen. Azure Disk Storage reserverings kortingen zijn niet van toepassing op niet-beheerde schijven, Ultra schijven of het gebruik van pagina-blobs.

## <a name="determine-your-storage-needs"></a>Uw opslag behoeften bepalen

Bepaal uw opslag behoeften voordat u een reserve ring aanschaft. Momenteel zijn Azure Disk Storage reserve ringen alleen beschikbaar voor geselecteerde Azure Premium SSD-Sku's. De SKU van een Premium SSD bepaalt de grootte en prestaties van de schijf.

Bij het bepalen van uw opslag behoeften kunt u geen schijven zien op basis van slechts capaciteit. U kunt bijvoorbeeld geen reserve ring voor een P40-schijf hebben en gebruiken om te betalen voor twee kleinere P30-schijven. Bij het aanschaffen van een reserve ring koopt u alleen een reserve ring voor het totale aantal schijven per SKU.

Er wordt een schijf reservering per schijf-SKU gemaakt. Als gevolg hiervan is het reserverings verbruik gebaseerd op de eenheid van de schijf-Sku's in plaats van de gegeven grootte.

Stel dat u een P40-schijf met 2 TiB van de ingerichte opslag capaciteit reserveert. Stel ook dat u slechts twee P30-schijven toewijst. De P40-reserve ring in dat geval heeft geen rekening met het P30-verbruik en u betaalt het betalen naar gebruik-tarief op de P30-schijven.
<br/>
<br/>
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

## <a name="purchase-considerations"></a>Aankoopoverwegingen

We raden u aan de volgende procedures uit te voeren bij het overwegen van een aankoop van schijf reservering:

- Analyseer uw gebruiks gegevens om te helpen bepalen welke reserve ringen u moet aanschaffen. Zorg ervoor dat u het gebruik in schijf-Sku's bijhoudt in plaats van de ingerichte schijf capaciteit.
- Controleer uw schijf reservering samen met uw VM-reserve ring. We raden u ten zeerste aan reserve ringen te maken voor zowel het gebruik van virtuele machines als voor schijf gebruik voor maximale besparingen. U kunt beginnen met het bepalen van de juiste reserve ring van de virtuele machine en vervolgens de schijf reservering evalueren. Over het algemeen hebt u een standaard configuratie voor elk van uw workloads. Een SQL Server-server kan bijvoorbeeld twee P40-gegevens schijven en één P30-besturingssysteem schijf bevatten.
  
  Met dit soort patroon kunt u de gereserveerde hoeveelheid bepalen die u mogelijk aanschaft. Deze aanpak kan het evaluatie proces vereenvoudigen en ervoor zorgen dat u beschikt over een uitgelijnd abonnement voor uw virtuele machine en schijven. Het plan bevat overwegingen zoals abonnementen of regio's.

## <a name="purchase-restrictions"></a>Aankoop beperkingen

Er zijn momenteel geen reserverings kortingen beschikbaar voor het volgende:

- Onbeheerde schijven of pagina-blobs.
- Standaard Ssd's of standaard harde schijven (Hdd's).
- Premium-SSD Sku's die kleiner zijn dan P30: P1, P2, P3, P4, P6, P10, P15 en P20 SSD-Sku's.
- Schijven in de regio's Azure Government, Azure Duitsland en Azure China.

In zeldzame gevallen beperkt Azure de aanschaf van nieuwe reserve ringen aan een subset schijf-Sku's vanwege een geringe capaciteit in een regio.

## <a name="buy-a-disk-reservation"></a>Een schijf reservering aanschaffen

U kunt Azure Disk Storage reserve ringen aanschaffen via de [Azure Portal](https://portal.azure.com/). U kunt betalen voor de reserve ring, hetzij vooraf, hetzij met maandelijkse betalingen. Zie [reserve ringen met maandelijkse betalingen kopen](../cost-management-billing/reservations/prepare-buy-reservation.md#buy-reservations-with-monthly-payments)voor meer informatie over het aanschaffen met maandelijkse betalingen.

Volg deze stappen om gereserveerde capaciteit aan te schaffen:

1. Ga naar het deel venster [Inkoop reserveringen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) in het Azure Portal.

1. Selecteer **Azure Managed disks** om een reserve ring aan te schaffen.

    ![Deel venster voor het kopen van reserve ringen](media/disks-reserved-capacity/disks-reserved-purchase-reservation.png) 

1. Geef de vereiste waarden op die in de volgende tabel worden beschreven:

   |Element  |Beschrijving  |
   |---------|---------|
   |**Bereik**   |  Hoeveel abonnementen kunnen gebruikmaken van het facturerings voordeel dat is gekoppeld aan de reserve ring. Met deze waarde wordt ook aangegeven hoe de reserve ring wordt toegepast op specifieke abonnementen. <br/><br/> Als u **gedeeld** selecteert, wordt de reserverings korting toegepast op Azure storage capaciteit in elk abonnement binnen uw facturerings context. De facturerings context is gebaseerd op de manier waarop u zich hebt geregistreerd voor Azure. Voor zakelijke klanten is het gedeelde bereik de inschrijving en omvat alle abonnementen binnen de inschrijving. Voor betalen per gebruik-klanten bevat het gedeelde bereik alle afzonderlijke abonnementen met betalen per gebruik-tarieven die zijn gemaakt door de account beheerder.  <br/><br/>  Als u **één abonnement** selecteert, wordt de reserverings korting toegepast op Azure storage capaciteit in het geselecteerde abonnement. <br/><br/> Als u **één resource groep** selecteert, wordt de reserverings korting toegepast op Azure storage capaciteit in het geselecteerde abonnement en in de geselecteerde resource groep van dat abonnement. <br/><br/> U kunt het reserverings bereik wijzigen nadat u de reserve ring hebt aangeschaft.  |
   |**Abonnement**  | Het abonnement dat u gebruikt om te betalen voor de Azure Storage reserve ring. De betalings wijze voor het geselecteerde abonnement wordt gebruikt bij het opladen van de kosten. Het abonnement moet een van de volgende typen zijn:<br/><ul><li> Enterprise Overeenkomst (de nummers MS-AZR-0017P en MS-AZR-0148P). Voor een Enter prise-abonnement worden de kosten afgetrokken van de Azure-voor uitbetaling van de inschrijving (voorheen monetaire toezeg ging genoemd)-saldo of in rekening gebracht als overschrijding.</li><br/><li>Individueel abonnement met betalen per gebruik-tarieven (AZR-0003P en MS-AZR-0023P). Voor een afzonderlijk abonnement met betalen per gebruik-tarieven worden de kosten in rekening gebracht op basis van de credit card of factuur betalings methode voor het abonnement.</li></ul>    |
   | **Disks** | De SKU die u wilt maken. |
   | **Regio** | De regio waar de reserve ring van kracht is. |
   | **Facturerings frequentie** | Hoe vaak het account wordt gefactureerd voor de reserve ring. Opties zijn **maandelijks** en **vooraf**. |

    ![Deel venster voor het selecteren van het product dat u wilt purchase.png](media/disks-reserved-capacity/premium-ssd-reserved-purchase-selection.png)

1. Nadat u de waarden voor de reserve ring hebt opgegeven, worden de kosten door de Azure Portal weer gegeven. In de portal wordt ook het kortings percentage weer gegeven over betalen per gebruik-facturering. Selecteer **volgende** om door te gaan naar het deel venster **reserve ringen voor inkoop** .

1. In het deel venster **Inkoop reserveringen** kunt u uw reserve ring een naam geven en het totale aantal reserve ringen selecteren dat u wilt maken. Het aantal reserve ringen wordt toegewezen aan het aantal schijven. Als u bijvoorbeeld honderd schijven wilt reserveren, voert u de **hoeveelheid** waarde **100** in.

1. Bekijk de totale kosten van de reserve ring.

    ![Het deel venster Inkoop reserveringen](media/disks-reserved-capacity/premium-ssd-reserved-selecting-sku-total-purchase.png)

Nadat u een reserve ring hebt aangeschaft, wordt deze automatisch toegepast op alle bestaande Disk Storage resources die overeenkomen met de reserverings voorwaarden. Als u nog geen Disk Storage resources hebt gemaakt, is de reserve ring van toepassing wanneer u een resource maakt die overeenkomt met de reserverings voorwaarden. In beide gevallen begint de reserverings periode onmiddellijk na een geslaagde aankoop.

## <a name="cancel-exchange-or-refund-reservations"></a>Reserveringen annuleren, ruilen of terugbetalen

U kunt in bepaalde beperkingen reserve ringen annuleren, uitwisselen of terugbetalen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) voor meer informatie.

## <a name="expiration-of-a-reservation"></a>Verval datum van een reserve ring

Wanneer een reserve ring verloopt, wordt elke Azure Disk Storage capaciteit die u gebruikt onder die reserve ring, gefactureerd op basis van het betalen naar gebruik-tarief. Reserveringen worden niet automatisch vernieuwd.

U ontvangt een e-mail melding van 30 dagen vóór het verlopen van de reserve ring en opnieuw op de verval datum. Om te kunnen profiteren van de kosten besparingen die een reserve ring biedt, verlengt u deze niet later dan de verval datum.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen

- [Wat zijn Azure-reserveringen?](../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Begrijpen hoe uw reserverings korting wordt toegepast op Azure Disk Storage](../cost-management-billing/reservations/understand-disk-reservations.md)