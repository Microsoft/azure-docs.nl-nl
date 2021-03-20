---
title: Veelgestelde vragen over meter service-Api's-micro soft Commercial Marketplace
description: Veelgestelde vragen over de Api's van de meter service voor SaaS-aanbiedingen in Microsoft AppSource en Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/01/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: ddec23f695396b8322d51da62158a97456096ae8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87319997"
---
# <a name="marketplace-metered-billing-apis---faq"></a>Geplaatste facturerings-Api's met Marketplace-Veelgestelde vragen

Zodra een klant zich abonneert op een SaaS-service, of Azure-toepassing met een abonnement voor beheerde apps, wordt het verbruik bijgehouden voor elke facturerings dimensie die wordt gebruikt.  Als het verbruik de inbegrepen hoeveel heden overschrijdt die zijn ingesteld voor de periode die is geselecteerd door de klant, stuurt uw service gebruiks gebeurtenissen naar micro soft.

## <a name="for-both-saas-offers-and-managed-apps"></a>Voor zowel SaaS-aanbiedingen als beheerde apps

### <a name="how-often-is-it-expected-to-emit-usage"></a>Hoe vaak verwacht het gebruik?

In het ideale geval moet u het gebruik elk uur voor het afgelopen uur verwachten, alleen als er in het vorige uur gebruik wordt gebruikt.

### <a name="is-there-a-maximal-period-between-one-emission-and-the-next-one"></a>Is er een maximum periode tussen één emissie en de volgende?

Deze beperking is niet van pas. Alleen gebruik verzenden als dit gebeurt. Als u bijvoorbeeld slechts één eenheid van het gebruik per abonnement wilt indienen, kunt u dat doen.

### <a name="what-is-the-maximum-delay-between-the-time-an-event-occurs-and-the-time-a-usage-event-is-emitted-to-microsoft"></a>Wat is de maximale vertraging tussen het tijdstip waarop een gebeurtenis plaatsvindt en hoe lang een gebruiks gebeurtenis wordt verzonden naar micro soft?

In het ideale geval wordt de gebruiks gebeurtenis elk uur verzonden voor gebeurtenissen die in het afgelopen uur zijn opgetreden. Er worden echter vertragingen verwacht. De toegestane maximum vertraging is 24 uur, waarna er geen gebruiks gebeurtenissen worden geaccepteerd. Het best practice is het verzamelen van het hele uur en het verzenden van gegevens als één gebeurtenis aan het einde van het uur.

Als bijvoorbeeld een gebruiks gebeurtenis plaatsvindt om 1 uur op een dag, hebt u tot 1 uur op de volgende dag een gebruiks gebeurtenis die aan deze gebeurtenis is gekoppeld.  Als het gebruik van het systeem niet beschikbaar is, kan het worden hersteld en wordt de gebruiks gebeurtenis vervolgens verzonden voor het uur interval waarin het gebruik heeft plaatsgevonden, zonder verlies van betrouw baarheid.

Als 24 uur zijn verstreken na het werkelijke gebruik, kunt u nog steeds de verbruikte eenheden verzenden met latere gebruiks gebeurtenissen.  Deze praktijk kan echter de geloofwaardigheid van de facturerings gebeurtenis rapporten voor de eind klant nadelig voor u voor de hand hebben.  We raden u aan om de emissie van meters slechts eenmaal per dag/week/maand te voor komen.  Het is moeilijker om het werkelijke gebruik door een klant te begrijpen en om problemen of vragen op te lossen die kunnen worden gegenereerd met betrekking tot gebruiks gebeurtenissen.

Een andere reden om elk uur gebruik te verzenden, is om te voor komen dat de gebruiker het abonnement annuleert voordat de uitgever de emissie gebeurtenis dagelijks/wekelijks/maandelijks verzendt.

### <a name="what-happens-when-you-send-more-than-one-usage-event-in-the-same-hour"></a>Wat gebeurt er wanneer u meer dan één gebruiks gebeurtenis in hetzelfde uur verzendt?

Er wordt slechts één gebruiks gebeurtenis geaccepteerd voor het interval van één uur. Het uur interval begint bij minuut 0 en eindigt op minuut 59.  Als er meer dan één gebruiks gebeurtenis voor hetzelfde uur wordt verzonden, worden latere gebruiks gebeurtenissen verwijderd als dubbele waarden.

### <a name="what-happens-when-the-customer-cancels-the-purchase-within-the-time-allowed-by-the-cancellation-policy"></a>Wat gebeurt er wanneer de klant de aankoop annuleert binnen de tijd die is toegestaan door het annulerings beleid?

Het forfaitaire bedrag wordt niet in rekening gebracht, maar het gebruik van de overschrijding.

### <a name="can-custom-meter-plans-be-used-for-one-time-payments"></a>Kunnen aangepaste meter plannen worden gebruikt voor eenmalige betalingen?

Ja, u kunt een aangepaste dimensie definiëren als één eenheid voor eenmalige betaling en deze slechts eenmaal verzenden voor elke klant.

### <a name="can-custom-meter-plans-be-used-to-tiered-pricing-model"></a>Kunnen aangepaste meter plannen worden gebruikt voor het gelaagde prijs model?

Ja, het kan worden geïmplementeerd met elke aangepaste dimensie die een enkele prijs categorie vertegenwoordigt.

Contoso wil bijvoorbeeld $0,5 per e-mail in rekening brengen voor de eerste 1000 e-mails, $0,4 per e-mail tussen 1000 en 5000 e-mail berichten en $0,2 per e-mail voor meer dan 5000 e-mail berichten. Ze kunnen drie aangepaste dimensies definiëren die overeenkomen met de drie prijs categorieën voor e-mail. Verzend eenheden van de eerste dimensie, zolang het aantal e-mails onder 1000 blijft, then eenheden van de tweede dimensie wanneer het aantal e-mails ligt tussen 1000 en 5000, en ten slotte de eenheden van de derde dimensie voor meer dan 5000 e-mail berichten.

### <a name="what-happens-if-the-marketplace-metering-service-has-an-outage"></a>Wat gebeurt er als de Marketplace-meet service een storing heeft?

Als de ISV een aangepaste meter verzendt en er een fout optreedt, is dit mogelijk veroorzaakt door een probleem aan de kant van micro soft (meestal dat er een fout is opgetreden in het geval dat er geen fouten zijn geaccepteerd), dan moet de ISV wachten en de emissie opnieuw proberen.

Als de fout zich blijft voordoen, verzendt u die aangepaste meter het volgende uur opnieuw (accumulatie van het aantal). Ga door met dit proces totdat er een niet-fout bericht wordt ontvangen.

## <a name="for-saas-offers-only"></a>Alleen voor SaaS-aanbiedingen

### <a name="what-happens-when-you-emit-usage-for-a-saas-subscription-that-has-been-unsubscribed-already"></a>Wat gebeurt er wanneer u het gebruik van een SaaS-abonnement dat al is afgemeld, uitschrijft?

Een gebruiks gebeurtenis die wordt verzonden naar het Marketplace-platform wordt niet geaccepteerd nadat een SaaS-abonnement is verwijderd.

Gebruik kan alleen worden verzonden voor abonnementen met de status geabonneerd (en niet voor abonnementen in `PendingFulfillmentStart` , `Suspended` , of `Unsubscribed` status).

De enige uitzonde ring is het rapporteren van het gebruik van de tijd voordat het SaaS-abonnement werd geannuleerd.

De klant heeft bijvoorbeeld het SaaS-abonnement vandaag om 3 uur geannuleerd. De Publisher kan nu nog steeds 5 uur gebruiken voor de periode tussen de gisteren en 3 uur voor dit SaaS-abonnement.

### <a name="can-you-get-a-list-of-all-saas-subscriptions-including-active-and-unsubscribed-subscriptions"></a>Kunt u een lijst met alle SaaS-abonnementen, waaronder actieve en niet-geabonneerde abonnementen, ophalen?

Ja, wanneer u de List van de [lijst abonnementen ophalen](pc-saas-fulfillment-api-v2.md#subscription-apis) aanroept, omdat het een lijst bevat met alle SaaS-abonnementen. In het veld Status in het antwoord voor elk SaaS-abonnement wordt vastgelegd of het abonnement actief of afgemeld is.

### <a name="are-the-start-and-end-dates-of-saas-subscription-term-and-overage-usage-emission-connected"></a>Zijn de begin-en eind datums van de SaaS-abonnements periode en de overschrijding gebruikt?

Overschrijding-gebeurtenissen kunnen op elk moment worden verzonden voor bestaande SaaS-abonnementen in de status *geabonneerd* . Het is de verantwoordelijkheid van de uitgever voor het verzenden van gebruiks gebeurtenissen op basis van het beleid dat in het facturerings plan is gedefinieerd. De overschrijding moet worden berekend op basis van de datums die zijn gedefinieerd in de periode van het SaaS-abonnement. 

Als de uitgever bijvoorbeeld een SaaS-abonnement definieert dat 1000 e-mail berichten voor $100 in maandelijks vaste frequentie bevat, wordt bij elke e-mail boven 1000 de waarde $1 in rekening gebracht via aangepaste dimensie.

Wanneer de klant het abonnement inkoopt en activeert op 6 januari, wordt de 1000-e-mail die is opgenomen in het vaste bedrag, geteld vanaf deze dag. Als tot en met 5 februari (het einde van de eerste maand van het abonnement) slechts 900 e-mail berichten worden verzonden, betaalt de klant het vaste tarief alleen voor de eerste maand van dit abonnement en worden er geen overschrijding-gebruiks gebeurtenissen door de uitgever verzonden tussen 6 januari en februari 5. Op 6 februari wordt het abonnement automatisch verlengd en wordt het aantal opnieuw gestart. Als de klant op 15 februari 1000 e-mail berichten heeft verzonden, worden de rest van de e-mail berichten die tot en met 5 zijn verzonden, in rekening gebracht als overschrijding ($1 per e-mail) op basis van de overschrijding-gebruiks gebeurtenissen die door de uitgever worden gegenereerd.

## <a name="next-steps"></a>Volgende stappen

- Zie Api's voor Marketplace- [meet service](./marketplace-metering-service-apis.md)voor meer informatie.
