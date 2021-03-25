---
title: Kosten optimaliseren voor Azure Files met gereserveerde capaciteit
titleSuffix: Azure Files
description: Meer informatie over het besparen van kosten op Azure file share-implementaties met behulp van Azure Files gereserveerde capaciteit.
services: storage
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 03/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 918320cdb24442e551249e4e67d65e4ba85846c8
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027563"
---
# <a name="optimize-costs-for-azure-files-with-reserved-capacity"></a>Kosten optimaliseren voor Azure Files met gereserveerde capaciteit
U kunt geld besparen op de opslag kosten voor Azure-bestands shares met capaciteits reserveringen. Azure Files gereserveerde capaciteit biedt u een korting op capaciteit voor opslag kosten wanneer u een reserve ring voor een jaar of drie jaar doorvoert. Een reserve ring biedt een vaste hoeveelheid opslag capaciteit voor de periode van de reserve ring.

Azure Files gereserveerde capaciteit kan de capaciteits kosten voor het opslaan van gegevens in uw Azure-bestands shares aanzienlijk verlagen. Hoeveel u opslaat, is afhankelijk van de duur van uw reserve ring, de totale capaciteit die u wilt reserveren en de laag-en redundantie-instellingen die u hebt gekozen voor Azure-bestands shares. Gereserveerde capaciteit biedt een facturerings korting en heeft geen invloed op de status van uw Azure-bestands shares.

Zie [Azure files prijzen](https://azure.microsoft.com/pricing/details/storage/files/)voor de prijs informatie over de reserverings capaciteit voor Azure files.

## <a name="reservation-terms-for-azure-files"></a>Reserverings voorwaarden voor Azure Files
In de volgende secties worden de voor waarden van een Azure Files capaciteits reservering beschreven.

### <a name="reservation-capacity"></a>Reserve ring capaciteit
U kunt Azure Files gereserveerde capaciteit kopen in eenheden van 10 TiB en 100 TiB per maand voor een periode van één of drie jaar.

### <a name="reservation-scope"></a>Reserverings bereik
Azure Files gereserveerde capaciteit is beschikbaar voor één abonnement of voor meerdere abonnementen (gedeeld bereik). Bij het bereiken van één abonnement wordt de reserverings korting alleen toegepast op het geselecteerde abonnement. Wanneer een bereik wordt toegepast op meerdere abonnementen, wordt de reserverings korting gedeeld via deze abonnementen binnen de facturerings context van de klant. Een reserve ring is van toepassing op uw gebruik binnen het aangeschafte bereik en kan niet worden beperkt tot een specifiek opslag account, container of object binnen het abonnement.

Een capaciteits reservering voor Azure Files geldt alleen voor de hoeveelheid gegevens die is opgeslagen in een abonnement of een gedeelde resource groep. Kosten voor trans acties, band breedte en gegevens overdracht zijn niet opgenomen in de reserve ring. Zodra u een reserve ring koopt, worden de capaciteits kosten die overeenkomen met de reserverings kenmerken, in rekening gebracht tegen de kortings tarieven in plaats van de betalen naar gebruik-tarieven. Zie [Wat zijn Azure Reservations?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)voor meer informatie over Azure-reserve ringen.

### <a name="supported-tiers-and-redundancy-options"></a>Ondersteunde lagen en redundantie opties
Azure Files gereserveerde capaciteit is alleen beschikbaar voor standaard bestands shares die zijn geïmplementeerd in opslag accounts voor algemeen gebruik (GPv2). Gereserveerde capaciteit is niet beschikbaar voor Azure-bestands shares in de lagen Premium of trans actie geoptimaliseerd.

Op dit moment ondersteunen alleen de Azure-bestands shares in de warme en cool-lagen reserve ringen. Alle opslag redundantie ondersteuning voor reserve ringen. Zie [Azure files redundantie](storage-files-planning.md#redundancy)voor meer informatie over redundantie opties.

### <a name="security-requirements-for-purchase"></a>Beveiligings vereisten voor aankoop
Gereserveerde capaciteit kopen:

- U moet de rol van **eigenaar** zijn voor minstens één bedrijf of een afzonderlijk abonnement met betalen per gebruik-tarieven.
- Voor ondernemings abonnementen moet u **gereserveerde instanties toevoegen** inschakelen in de EA-Portal. Als deze instelling is uitgeschakeld, moet u een EA-beheerder zijn voor het abonnement.
- Voor het programma Cloud Solution Provider (CSP) kunnen alleen beheer agenten of verkoop medewerkers Azure Files gereserveerde capaciteit kopen.

## <a name="determine-required-capacity-before-purchase"></a>Vereiste capaciteit bepalen vóór aankoop
Wanneer u een Azure Files reserve ring aanschaft, moet u de optie regio, laag en redundantie kiezen voor de reserve ring. Uw reserve ring is alleen geldig voor gegevens die zijn opgeslagen in die regio, laag en redundantie niveau. Stel dat u een reserve ring aanschaft voor gegevens in VS West voor de warme laag met zone-redundante opslag (ZRS). Deze reserve ring is niet van toepassing op gegevens in VS Oost, gegevens in de coole laag of gegevens in geografisch redundante opslag (GRS). U kunt echter wel een andere reserve ring aanschaffen voor uw aanvullende behoeften.  

Er zijn reserve ringen beschikbaar voor 10 TiB-of 100 TiB-blokken, met hogere kortingen voor 100 TiB-blokken. Wanneer u een reserve ring aanschaft in de Azure Portal, kan micro soft u aanbevelingen geven op basis van uw vorige gebruik om te helpen bepalen welke reserve ring u moet aanschaffen.

## <a name="purchase-azure-files-reserved-capacity"></a>Gereserveerde capaciteit van Azure Files aankoop
U kunt Azure Files gereserveerde capaciteit aanschaffen via de [Azure Portal](https://portal.azure.com). Betaal vooraf of per maand voor de reservering. Zie voor meer informatie over aankopen met maandelijkse betalingen [Azure-reserve ringen aanschaffen met vooraf of maandelijks betalen](../../cost-management-billing/reservations/prepare-buy-reservation.md).

Zie [inzicht in de korting op Azure Storage gereserveerde capaciteit](../../cost-management-billing/reservations/understand-storage-charges.md)voor hulp bij het identificeren van de reserverings voorwaarden die geschikt zijn voor uw scenario.

Volg deze stappen om gereserveerde capaciteit aan te schaffen:

1. Navigeer naar de Blade [aankoop reserveringen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/Browse_AddCommand) in het Azure Portal.  
1. Selecteer **Azure files** om een nieuwe reserve ring te kopen.  
1. Vul de vereiste velden in zoals beschreven in de volgende tabel:

    ![Scherm afbeelding die laat zien hoe gereserveerde capaciteit kan worden aangeschaft](./media/files-reserve-capacity/select-reserved-capacity.png)

   |Veld  |Beschrijving  |
   |---------|---------|
   |**Bereik**   |  Hiermee wordt aangegeven hoeveel abonnementen het facturerings voordeel kunnen gebruiken dat is gekoppeld aan de reserve ring. Het bepaalt ook hoe de reserve ring wordt toegepast op specifieke abonnementen. <br/><br/> Als u **gedeeld** selecteert, wordt de reserverings korting toegepast op Azure files capaciteit in een abonnement binnen uw facturerings context. De facturerings context is gebaseerd op de manier waarop u zich hebt geregistreerd voor Azure. Voor zakelijke klanten is het gedeelde bereik de inschrijving en omvat alle abonnementen binnen de inschrijving. Voor betalen per gebruik-klanten bevat het gedeelde bereik alle afzonderlijke abonnementen met betalen per gebruik-tarieven die zijn gemaakt door de account beheerder.  <br/><br/>  Als u **één abonnement** selecteert, wordt de reserverings korting toegepast op Azure files capaciteit in het geselecteerde abonnement. <br/><br/> Als u **één resource groep** selecteert, wordt de reserverings korting toegepast op Azure files capaciteit in het geselecteerde abonnement en de geselecteerde resource groep in dat abonnement. <br/><br/> U kunt het reserverings bereik wijzigen nadat u de reserve ring hebt aangeschaft.  |
   |**Abonnement**  | Het abonnement dat wordt gebruikt om te betalen voor de Azure Files reserve ring. De betalings wijze voor het geselecteerde abonnement wordt gebruikt bij het opladen van de kosten. Het abonnement moet een van de volgende typen zijn: <br/><br/>  Enterprise Overeenkomst (nummers van aanbiedingen: MS-AZR-0017P of MS-AZR-0148P): voor een Enter prise-abonnement worden de kosten in rekening gebracht op basis van de Azure-voor uitbetaling van de inschrijving (voorheen monetaire toezeg ging genoemd)-saldo of in rekening gebracht als overschrijding. <br/><br/> Individueel abonnement met betalen per gebruik-tarieven (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P): voor een afzonderlijk abonnement met betalen per gebruik-tarieven, worden de kosten in rekening gebracht op de credit card-of factuur betalings methode voor het abonnement.    |
   | **Regio** | De regio waar de reserve ring van kracht is. |
   | **Laag** | De laag waar de reserve ring van kracht is. De opties zijn *Hot* en *cool*. |
   | **Redundantie** | De redundantie optie voor de reserve ring. Opties zijn *LRS*, *ZRS*, *GRS* en *GZRS*. Zie [Azure files redundantie](storage-files-planning.md#redundancy)voor meer informatie over redundantie opties. |
   | **Facturerings frequentie** | Hiermee wordt aangegeven hoe vaak het account wordt gefactureerd voor de reserve ring. De opties zijn *maandelijks* of *vooraf*. |
   | **Grootte** | De hoeveelheid capaciteit die moet worden gereserveerd. |
   |**Termijn**  | Eén jaar of drie jaar.   |

1. Nadat u de para meters voor de reserve ring hebt geselecteerd, worden de kosten weer gegeven in de Azure Portal. In de portal wordt ook het kortings percentage weer gegeven over betalen per gebruik-facturering.

1. Controleer op de Blade **aankoop reserveringen** de totale kosten van de reserve ring. U kunt ook een naam voor de reserve ring opgeven.

Nadat u een reserve ring hebt aangeschaft, wordt deze automatisch toegepast op alle bestaande Azure-bestands shares die overeenkomen met de voor waarden van de reserve ring. Als u nog geen Azure-bestands shares hebt gemaakt, wordt de reserve ring toegepast wanneer u een resource maakt die overeenkomt met de voor waarden van de reserve ring. In beide gevallen begint de periode van de reserve ring onmiddellijk na een geslaagde aankoop.

## <a name="exchange-or-refund-a-reservation"></a>Een reserve ring uitwisselen of terugbetalen
U kunt een reserve ring uitwisselen of terugbetalen, met bepaalde beperkingen. Deze beperkingen worden beschreven in de volgende secties.

Als u een reserve ring wilt omruilen of terugbetalen, gaat u naar de reserverings details in het Azure Portal. Selecteer **Exchange** of **terugbetaling** en volg de instructies om een ondersteunings aanvraag in te dienen. Wanneer de aanvraag is verwerkt, ontvangt micro soft u een e-mail om de voltooiing van de aanvraag te bevestigen.

Zie voor meer informatie over Azure Reservations-beleid [self-service uitwisselingen en terugstortingen voor Azure Reservations](../../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md).

### <a name="exchange-a-reservation"></a>Een reserve ring uitwisselen
Door een reserve ring uit te wisselen, kunt u een evenredige restitutie ontvangen op basis van het niet-gebruikte gedeelte van de reserve ring. U kunt de terugbetaling vervolgens Toep assen op de aankoop prijs van een nieuwe Azure Files reserve ring.

Er is geen limiet voor het aantal uitwisselingen dat u kunt maken. Daarnaast zijn er geen kosten verbonden aan een uitwisseling. De nieuwe reserve ring die u aanschaft, moet gelijk zijn aan of hoger zijn dan het gefactureerde tegoed van de oorspronkelijke reserve ring. Een Azure Files reserve ring kan alleen worden uitgewisseld voor een andere Azure Files reserve ring en niet voor een andere Azure-service.

### <a name="refund-a-reservation"></a>Een reserve ring terugbetalen
U kunt op elk gewenst moment een Azure Files reserve ring annuleren. Wanneer u annuleert, ontvangt u een gefactureerde restitutie op basis van de resterende termijn van de reserve ring, min de kosten voor een vroege beëindiging van 12 procent. De maximale terugbetaling per jaar is $50.000.

Annuleren van een reserve ring beëindigt onmiddellijk de reserve ring en retourneert de resterende maanden naar micro soft. Het resterende tarief saldo, min de vergoeding, wordt terugbetaald naar de oorspronkelijke aankoop vorm.

## <a name="expiration-of-a-reservation"></a>Verval datum van een reserve ring
Wanneer een reserve ring verloopt, wordt de Azure Files capaciteit die u onder die reserve ring gebruikt, gefactureerd op basis van het tarief voor betalen naar gebruik. Reserveringen worden niet automatisch vernieuwd.

U ontvangt een e-mail melding van 30 dagen vóór het verstrijken van de reserve ring en opnieuw op de verval datum. Om te kunnen profiteren van de kosten besparingen die een reserve ring biedt, verlengt u deze niet later dan de verval datum.

## <a name="need-help-contact-us"></a>Hebt u hulp nodig? Contact opnemen
Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Volgende stappen
- [Wat zijn Azure-reserveringen?](../../cost-management-billing/reservations/save-compute-costs-reservations.md)
- [Meer informatie over de manier waarop reserverings kortingen worden toegepast op Azure Storage-services](../../cost-management-billing/reservations/understand-storage-charges.md)