---
title: Gereserveerde exemplaren Azure EA-VM
description: In dit artikel wordt samengevat hoe u met Azure-reserveringen voor gereserveerde VM-instanties geld kunt besparen met uw Enterprise Enrollment.
author: bandersmsft
ms.author: banders
ms.date: 10/14/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: 6303a94cec9efc01815b6dc6c697abdfe0f84227
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220897"
---
# <a name="azure-ea-vm-reserved-instances"></a>Gereserveerde exemplaren Azure EA-VM

In dit artikel wordt samengevat hoe u met Azure-reserveringen voor gereserveerde VM-instanties geld kunt besparen met uw Enterprise Enrollment. Zie [Wat zijn Azure-reserveringen?](../reservations/save-compute-costs-reservations.md) voor meer informatie over reserveringen.

## <a name="reservation-exchanges-and-refunds"></a>Omruilen en restituties bij reserveringen

U kunt een reservering inwisselen voor een andere reservering van hetzelfde type. Het is ook mogelijk om een restitutie (tot USD 50.000 per jaar) voor een reservering aan te vragen als u deze niet meer nodig hebt. De Azure-portal kan worden gebruikt voor omruiling of terugbetaling van een reservering. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](../reservations/exchange-and-refund-azure-reservations.md) voor meer informatie.

### <a name="partial-refunds"></a>Gedeeltelijke restitutie

Er wordt een gedeeltelijke restitutie gegeven wanneer EA-klanten reserveringen retourneren die zijn gekocht met overschrijding en niet met Azure-vooruitbetaling (voorheen financiële toezegging).

De restitutie wordt in de EA-portal weer gegeven als een negatieve correctie in de vorige maand en een positieve correctie in de huidige maand. Het wordt op dezelfde manier weergegeven als bij een reserveringsuitwisseling. De creditnota verwijst naar het oorspronkelijke factuurnummer. Verwijs dus naar het oorspronkelijke factuurnummer bij het afstemmen van de aanvankelijke aankoop met de creditnota.

## <a name="reservation-costs-and-usage"></a>Reserveringskosten en gebruik

Enterprise Agreement-klanten kunnen kosten en gebruiksgegevens weergeven in Azure Portal en REST API's. Voor reserveringskosten en gebruik kunt u:

- Aankoopgegevens van reserveringen ophalen.
- Nagaan voor welke abonnementen, resourcegroepen of resources reserveringen zijn gebruikt.
- Reserveringsgebruik toerekenen.
- Reserveringsbesparingen berekenen.
- Gegevens over ondergebruikte reserveringen ophalen.
- Reserveringskosten afschrijven.

Zie [Kosten en gebruik van Enterprise Agreement-reserveringen ophalen](../reservations/understand-reserved-instance-usage-ea.md) voor meer informatie over reserveringskosten en gebruik.

Zie [Prijsinformatie - Linux Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) of [Prijsinformatie - Windows Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) voor meer informatie over prijzen.

## <a name="reserved-instances-api-support"></a>Gereserveerde instanties en API-ondersteuning

Gebruik Azure-API's om programmatisch informatie voor uw organisatie te verkrijgen over de Azure-service of softwarereserveringen. Gebruik de API’s bijvoorbeeld voor het volgende:

- Reserveringen zoeken om te kopen
- Een reservering kopen
- Aangeschafte reserveringen weer geven
- Reserveringstoegang weergeven en beheren
- Reserveringen splitsen of samenvoegen
- Het bereik van reserveringen wijzigen

Zie [API's voor automatisering van Azure-reserveringen](../reservations/reservation-apis.md) voor meer informatie.

## <a name="azure-reserved-virtual-machine-instances"></a>Voor Azure gereserveerde VM-instanties

Gereserveerde instanties kunnen de kosten voor uw virtuele machine tot 72 procent verlagen ten opzichte van de prijzen voor betalen per gebruik voor alle VM’s. Of een besparing van maximaal 82 procent in combinatie met het voordeel van Azure Hybrid. Gereserveerde instanties helpen u uw workloads, budget en prognoses beter te beheren met een vooruitbetaling voor een periode van één jaar of drie jaar. U kunt reserveringen ook uitwisselen of annuleren als uw bedrijfsbehoeften veranderen.

### <a name="how-to-buy-reserved-virtual-machine-instances"></a>Gereserveerde instanties van virtuele machines aanschaffen

Om een ​​gereserveerde instantie van virtuele machine van Azure te kopen, moet een Enterprise Azure-registratiebeheerder de aankoopoptie _Instantie reserveren_ inschakelen. Deze optie bevindt zich in het gedeelte _Inschrijvingsdetails_ van het tabblad _Inschrijving_ in de [Microsoft Azure Enterprise Portal](https://ea.azure.com/).

Nadat de EA-inschrijving zo is ingesteld dat gereserveerde instanties kunnen worden toegevoegd, kan een accounteigenaar met een actief abonnement dat bij de EA-inschrijving hoort, een gereserveerde VM-instantie aanschaffen in de [Azure-portal](https://aka.ms/reservations). Raadpleeg [Vooruitbetalen voor virtuele machines en geld besparen met gereserveerde VM-instanties](../../virtual-machines/prepay-reserved-vm-instances.md) voor meer informatie.

### <a name="how-to-view-reserved-instance-purchase-details"></a>Aankoopgegevens bekijken van gereserveerde instanties

U kunt de aankoopgegevens van uw gereserveerde instantie bekijken via het menu _Reservering_ aan de linkerzijde van de [Azure-portal](https://aka.ms/reservations) of via de [Azure EA-portal](https://ea.azure.com/). Selecteer **Rapporten** in het menu aan de linkerzijde en scrol naar beneden naar het gedeelte _Kosten per services_ in het tabblad _Gebruiksoverzicht_. Scrol naar de onderkant van het gedeelte en uw aankopen en gebruik van gereserveerde instanties worden aan het einde weergegeven, zoals aangegeven door de aanduiding `1 year` of `3 years` naast de servicenaam, bijvoorbeeld: `Standard_DS1_v2 eastus 1 year` or `Standard_D2s_v3 eastus2 3 years`.

### <a name="how-can-i-change-the-subscription-associated-with-reserved-instance-or-transfer-my-reserved-instance-benefits-to-a-subscription-under-the-same-account"></a>Hoe verander ik het abonnement dat bij de gereserveerde instantie hoort of hoe draag ik de voordelen van mijn gereserveerde instantie over aan een abonnement in hetzelfde account?

U verandert het abonnement dat de voordelen van gereserveerde instanties ontvangt als volgt:

- Aanmelden bij de [Azure-portal](https://aka.ms/reservations).
- Werk het toegepaste abonnementsbereik bij door een ander abonnement binnen hetzelfde account te koppelen.

Zie [Het reserveringsbereik wijzigen](../reservations/manage-reserved-vm-instance.md#change-the-reservation-scope) voor meer informatie over het wijzigen van het bereik van een reservering.

### <a name="how-to-view-reserved-instance-usage-details"></a>Gebruiksgegevens van gereserveerde instanties bekijken

U kunt de gebruiksgegevens van uw gereserveerde instanties bekijken in de [Azure-portal](https://aka.ms/reservations) of in de [Azure EA-portal](https://ea.azure.com/) (voor EA-klanten met toegang tot factureringsgegevens) in het gedeelte _Rapporten_ > _Gebruiksoverzicht_ > _Kosten per services_. Uw gereserveerde instanties kunnen worden geïdentificeerd als servicenamen die het woord 'Reservering' bevatten, bijvoorbeeld: `Reservation-Base VM or Virtual Machines Reservation-Windows Svr (1 Core)`.

Uw gebruiksgegevens en een geavanceerde download van een CSV-rapport bevatten aanvullende gebruiksinformatie van de gereserveerde instantie. Het veld _Aanvullende informatie_ helpt u bij het identificeren van het gebruik van de gereserveerde instantie.

Als u geen gebruik hebt gemaakt van het voordeel van Azure Hybrid om de gereserveerde VM-instanties aan te schaffen, laten gereserveerde instanties twee meters zien (hardware en software). Als u het voordeel van Azure Hybrid gebruikt om de gereserveerde instantie aan te schaffen, ziet u de softwaremeter niet in de gebruiksgegevens van uw gereserveerde instantie.

### <a name="reserved-instance-billing"></a>Facturering van gereserveerde instanties

Voor zakelijke klanten wordt Azure-vooruitbetaling gebruikt om gereserveerde VM-instanties van Azure aan te schaffen. Als uw inschrijving genoeg saldo voor Azure-vooruitbetaling heeft om de aankoop van de gereserveerde instantie te dekken, wordt het bedrag afgeschreven van uw saldo voor Azure-vooruitbetaling en ontvangt u geen factuur voor de aankoop.

Wanneer Azure EA-klanten alle Azure-vooruitbetaling hebben gebruikt, kunnen er nog steeds gereserveerde instanties worden gekocht. Deze aankopen worden in rekening gebracht op de volgende overschrijdingsfactuur. Overschrijding van gereserveerde instanties, indien van toepassing, vormen onderdeel van uw reguliere overschrijdingsfactuur.

### <a name="reserved-instance-expiration"></a>Verloop van gereserveerde instanties

U ontvangt e-mail meldingen, de eerste 1 30 dagen vóór de verval datum van de reserve ring en de andere een verval datum. Wanneer de reservering verloopt, blijven geïmplementeerde VM's actief en worden deze in rekening gebracht tegen het tarief voor betalen per gebruik. Raadpleeg [Aanbieding van gereserveerde instanties van virtuele machines](https://azure.microsoft.com/pricing/reserved-vm-instances/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat zijn Azure-reserveringen?](../reservations/save-compute-costs-reservations.md) voor meer informatie over Azure-reserveringen.
- Zie [Kosten en gebruik van Enterprise Agreement-reserveringen ophalen](../reservations/understand-reserved-instance-usage-ea.md) voor meer informatie over Enterprise-reserveringskosten en -gebruik.
- Zie [Prijsinformatie - Linux Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) of [Prijsinformatie - Windows Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) voor meer informatie over prijzen.
