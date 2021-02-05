---
title: Opslaan voor Azure App Service met gereserveerde capaciteit
description: Meer informatie over hoe u kosten kunt besparen voor Azure App Service Premium v3 gereserveerde instanties en de kosten voor geïsoleerde stem pels.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 02/01/2021
ms.author: banders
ms.custom: references_regions
ms.openlocfilehash: 89e0c62b580c0c354fc7277e61b452005a86e3d9
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99577794"
---
# <a name="save-costs-with-azure-app-service-reserved-instances"></a>Bespaar kosten met Azure App Service gereserveerde instanties

In dit artikel wordt uitgelegd hoe u kunt opslaan met Azure App Service gereserveerde instanties voor Premium v3-instanties en de kosten voor de geïsoleerde stempel.

## <a name="save-with-premium-v3-reserved-instances"></a>Opslaan met Premium v3 gereserveerde instanties

Wanneer u een gereserveerde Azure App Service Premium v3-exemplaar doorvoert, kunt u geld besparen. De reserverings korting wordt automatisch toegepast op het aantal actieve instanties dat overeenkomt met het reserverings bereik en de kenmerken. U hoeft geen reserve ring aan een exemplaar toe te wijzen om de kortingen te krijgen.

## <a name="determine-the-right-reserved-instance-size-before-you-buy"></a>De juiste gereserveerde instantie grootte bepalen voordat u deze koopt

Voordat u een reserve ring koopt, moet u de grootte van het gereserveerde Premium v3-exemplaar bepalen dat u nodig hebt. De volgende secties helpen u bij het bepalen van de juiste Premium v3 gereserveerde instantie grootte.

### <a name="use-reservation-recommendations"></a>Aanbevelingen voor reserverings vereisten gebruiken

U kunt reserverings aanbevelingen gebruiken om te helpen bij het bepalen van de reserve ringen die u moet aanschaffen.

- Aanbevelingen voor aankopen en aanbevolen hoeveelheid worden weer gegeven wanneer u een gereserveerde Premium v3-instantie in de Azure Portal koopt.
- Azure Advisor biedt inkoop aanbevelingen voor afzonderlijke abonnementen.
- U kunt de Api's gebruiken voor het verkrijgen van inkoop aanbevelingen voor zowel het gedeelde bereik als het bereik van één abonnement. Zie voor meer informatie [gereserveerde instanties aankoop aanbeveling api's voor zakelijke klanten](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation).
- Voor Enterprise Overeenkomst (EA) en klanten overeenkomst (MCA) van micro soft, zijn er aanbevelingen voor het delen van gedeelde en enkelvoudige abonnementen beschikbaar met de [Azure Consumption Insights Power bi inhouds pakket](/power-bi/service-connect-to-azure-consumption-insights).

#### <a name="instance-size-flexibility-setting"></a>Instelling voor de flexibiliteit van de instantiegrootte

De instelling voor de flexibiliteit van de instantiegrootte bepaalt welke services de kortingen voor de gereserveerde instanties ontvangen.

Of de instelling is in-of uitgeschakeld, de reserverings kortingen zijn automatisch van toepassing op het gebruik van overeenkomende Premium v3 gereserveerde instanties.

### <a name="analyze-your-usage-information"></a>Uw gebruiks gegevens analyseren

Analyseer uw gebruiks gegevens om te helpen bepalen welke reserve ringen u moet aanschaffen. Gebruiks gegevens zijn beschikbaar in het gebruiks bestand en Api's. Gebruik ze samen om te bepalen welke reserve ring moet worden gekocht. Controleren op Premium v3-exemplaren die dagelijks een hoog gebruik hebben om het aantal te kopen reserve ringen te bepalen.

Uw gebruiks bestand toont uw kosten per facturerings periode en dagelijks gebruik. Zie [uw Azure-gebruik en-kosten bekijken en downloaden](../understand/download-azure-daily-usage.md)voor meer informatie over het downloaden van uw gebruiks bestand. Vervolgens kunt u met behulp van de informatie in het gebruiks bestand [bepalen welke reserve ring moet worden gekocht](determine-reservation-purchase.md).

### <a name="purchase-restriction-considerations"></a>Overwegingen voor de aankoop beperking

Er zijn geen reserverings kortingen van toepassing op de volgende Premium v3-exemplaren:

- **Preview-of promotie-instanties** : alle Premium v3 gereserveerde instanties-Series of grootte die in Preview zijn of waarbij promotie meter wordt gebruikt.
- **Clouds** -reserve ringen zijn niet beschikbaar voor aankopen in de regio's Duitsland en China.

## <a name="buy-a-premium-v3-reserved-instance"></a>Een gereserveerde Premium v3-instantie kopen

U kunt een gereserveerde Premium v3-instantie kopen in de [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22VirtualMachines%22%7D). Betaal [vooraf of per maand](prepare-buy-reservation.md) voor de reservering. Deze vereisten zijn van toepassing op het kopen van een Premium v3 gereserveerde instantie:

- U moet een rol van eigenaar zijn voor ten minste één EA-abonnement of een abonnement met een betalen naar gebruik-tarief.
- Voor EA-abonnementen moet de optie **gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA-Portal](https://ea.azure.com/). Of, als deze instelling is uitgeschakeld, moet u een EA-beheerder van het abonnement zijn.
- Voor het programma Cloud Solution Provider (CSP) kunnen alleen de beheerders of verkoop medewerkers reserve ringen kopen.

Een instantie kopen:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer **Alle services** > **Reserveringen**.
3. Selecteer **toevoegen** om een nieuwe reserve ring aan te schaffen en klik vervolgens op **exemplaar**.
4. Vul de verplichte velden in. Premium v3 gereserveerde instanties die overeenkomen met de kenmerken die u selecteert, komen in aanmerking voor de reserverings korting. Het werkelijke aantal Premium v3 gereserveerde instanties dat de korting krijgt, is afhankelijk van de geselecteerde scope en hoeveelheid.

Als u een EA-overeenkomst hebt, kunt u de **optie meer toevoegen** gebruiken om snel extra instanties toe te voegen. De optie is niet beschikbaar voor andere typen abonnementen.

| **Veld** | **Beschrijving** |
|---|---|
| Abonnement | Het abonnement dat wordt gebruikt om te betalen voor de reserve ring. Via de betalingswijze voor het abonnement worden de kosten voor de reservering in rekening gebracht. Het abonnements type moet een Enter prise Agreement zijn (nummer van de aanbieding: MS-AZR-0017P of MS-AZR-0148P) of een micro soft-klant overeenkomst of een afzonderlijk abonnement met betalen per gebruik-tarieven (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P). De kosten worden in mindering gebracht op het saldo van het reserveringsbedrag, indien beschikbaar, of in rekening gebracht als overschrijding. Voor een abonnement met betalen per gebruik-tarieven worden de kosten in rekening gebracht op basis van de credit card-of factuur betalings methode voor het abonnement. |
| Bereik | Het bereik van de reserve ring kan betrekking hebben op één abonnement of meerdere abonnementen (gedeeld bereik). Als u het volgende selecteert: <ul><li>**Bereik van één resourcegroep**: de reserveringskorting wordt alleen toegepast op de overeenkomende resources in de geselecteerde resourcegroep. </li><li>**Bereik van één abonnement**: de reserveringskorting wordt toegepast op de overeenkomende resources in het geselecteerde abonnement.</li><li>**Gedeeld bereik**: de reserveringskorting wordt toegepast op overeenkomende resources binnen in aanmerking komende abonnementen die zich in de factureringscontext bevinden. Voor EA-klanten is de facturerings context de inschrijving. Voor afzonderlijke abonnementen met tarieven voor betalen per gebruik is het factureringsbereik alle in aanmerking komende abonnementen die zijn gemaakt door de accountbeheerder.</li></ul> |
| Region | De Azure-regio die wordt gedekt door de reserve ring. |
| Grootte van gereserveerde Premium v3-instantie | De grootte van de gereserveerde Premium v3-exemplaren. |
| Optimaliseren voor | De flexibiliteit van de Premium v3 gereserveerde instantie grootte is standaard geselecteerd. Klik op **Geavanceerde instellingen** om de waarde voor de flexibiliteit van de instantie grootte te wijzigen om de reserverings korting toe te passen op andere Premium v3 gereserveerde instanties in dezelfde [Premium v3 gereserveerd exemplaar formaat groep](../../virtual-machines/reserved-vm-instance-size-flexibility.md). Met de capaciteitsprioriteit wordt prioriteit toegekend aan de datacentercapaciteit voor uw implementaties. Het biedt extra vertrouwen in uw vermogen om de Premium v3 gereserveerde instanties te starten wanneer u ze nodig hebt. Capaciteits prioriteit is alleen beschikbaar wanneer het reserverings bereik één abonnement is. |
| Termijn | Eén jaar of drie jaar. Er is ook een periode van 5 jaar die alleen beschikbaar is voor HBv2 Premium v3 gereserveerde instanties. |
| Aantal | Het aantal exemplaren dat wordt aangeschaft binnen de reserve ring. De hoeveelheid is het aantal uitgevoerde Premium v3 gereserveerde instanties waarmee de factuur korting kan worden verkregen. Als u bijvoorbeeld 10 Standard \_ D2 Premium v3 gereserveerde instanties in het VS-Oost hebt uitgevoerd, geeft u hoeveelheid op als 10 om het voor deel te maximaliseren voor alle met Premium v3 gereserveerde instanties. |

## <a name="save-with-isolated-stamp-fees"></a>Bespaar met de kosten voor de geïsoleerde stempel

U kunt besparen op zegelkosten voor Azure App Service Isolated door u vast te leggen voor een reservering voor uw zegelgebruik voor de duur van drie jaar. Als u gereserveerde capaciteit voor Isolated-zegelkosten wilt kopen, moet u de Azure-regio kiezen waar de zegel wordt geïmplementeerd en hoeveel zegels u wilt kopen.

Wanneer u een reservering koopt, wordt het Isolated-zegelkostengebruik dat met de reserveringskenmerken overeenkomt, niet meer tegen het tarief voor betalen per gebruik in rekening gebracht. De reservering wordt automatisch toegepast op het aantal Isolated-zegels dat overeenkomt met het gereserveerde capaciteitsbereik en de regio. U hoeft geen reservering toe te wijzen aan een Isolated-zegel. De reservering is niet van toepassing op werkrollen, dus alle andere resources die aan de zegel zijn gekoppeld, worden afzonderlijk in rekening gebracht.

Wanneer de gereserveerde capaciteit vervalt, blijven Isolated-zegels actief, maar wordt u gefactureerd tegen het tarief voor betalen per gebruik. Reserveringen worden niet automatisch vernieuwd.

## <a name="determine-the-right-isolated-stamp-reservation-to-purchase"></a>De juiste vast te stellen hand reservering voor de geïsoleerde stempel bepalen

Door een reservering te kopen, legt u zich vast voor het gebruik van een gereserveerde hoeveelheid voor de komende drie jaar. Controleer uw gebruiksgegevens om te bepalen hoeveel App Service Isolated-zegels u consistent gebruikt en mogelijk gaat gebruiken.

Zorg bovendien ervoor dat u begrijpt hoe een Linux-of Windows-meter wordt verzonden via de Isolated-zegel.

- Via een lege Isolated-zegel wordt standaard de Windows-zegelmeter verzonden. Dit is bijvoorbeeld het geval als er geen werkrollen zijn geïmplementeerd. Deze meter blijft hetzelfde als er Windows-werkrollen op de zegel worden geïmplementeerd.
- De meter wordt echter gewijzigd in een Linux-zegelmeter als u een Linux-werkrol implementeert.
- In gevallen waarin zowel Linux-als Windows-werkrollen worden geïmplementeerd, wordt de Windows-meter verzonden via de zegel.

De zegelmeter kan tijdens de levensduur dus veranderen van Windows in Linux en omgekeerd.

Koop Windows-zegelreserveringen als u een of meer Windows-werkrollen op de zegel hebt. De enige situatie waarin u een Linux-zegelreservering moet kopen, is als u _alleen_ Linux-werkrollen op de zegel wilt.

## <a name="buy-isolated-stamp-reserved-capacity"></a>Gereserveerde capaciteit voor Isolated-zegels kopen

U kunt gereserveerde capaciteit voor Isolated-zegels kopen in [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D). Betaal [vooraf of per maand](./prepare-buy-reservation.md) voor de reservering. Als u gereserveerde capaciteit wilt kopen, moet u de eigenaarsrol hebben voor ten minste één Enterprise-abonnement of een afzonderlijk abonnement met tarieven voor betalen per gebruik.

- Voor Enterprise-abonnementen moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in [EA Portal](https://ea.azure.com/). Als de instelling is uitgeschakeld, moet u een EA-beheerder zijn.
- Voor het programma Cloud Solution Provider (CSP) kunnen alleen beheerders of verkoopmedewerkers gereserveerde Azure Synapse Analytics-capaciteit kopen.

**Ga als volgt te werk om gereserveerde capaciteit te kopen:**

1. Ga naar [Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/CreateBlade/referrer/documentation/filters/%7B%22reservedResourceType%22%3A%22AppService%22%7D).
1. Selecteer een abonnement. Gebruik de lijst **Abonnementen** om het abonnement te kiezen dat wordt gebruikt om te betalen voor de gereserveerde capaciteit. Via de betalingswijze voor het abonnement worden de kosten voor de gereserveerde capaciteit in rekening gebracht. Het abonnementstype moet een Enterprise-overeenkomst zijn (nummers van aanbieding: MS-AZR-0017P of MS-AZR-0148P) of Betalen per gebruik (nummers van aanbieding: MS-AZR-0003P of MS-AZR-0023P) of een CSP-abonnement.
    - Voor een Enterprise-abonnement worden de kosten in mindering gebracht op het saldo van Azure-vooruitbetaling (voorheen financiële toezegging) of in rekening gebracht als overschrijding.
    - Voor een Betalen per gebruik-abonnement worden de kosten in rekening gebracht op de creditcard of de factuurbetalingswijze van het abonnement.
1. Selecteer een waarde voor **Bereik** om een abonnementsbereik te kiezen.
    - **Bereik van één resourcegroep**: de reserveringskorting wordt alleen toegepast op de overeenkomende resources in de geselecteerde resourcegroep.
    - **Bereik van één abonnement**: de reserveringskorting wordt toegepast op de overeenkomende resources in het geselecteerde abonnement.
    - **Gedeeld bereik**: de reserveringskorting wordt toegepast op overeenkomende resources binnen in aanmerking komende abonnementen die zich in de factureringscontext bevinden. Voor Enterprise Agreement-klanten is de inschrijving de factureringscontext. Voor afzonderlijke abonnementen met tarieven voor betalen per gebruik is het factureringsbereik alle in aanmerking komende abonnementen die zijn gemaakt door de accountbeheerder.
1. Selecteer een waarde voor **Regio** om een Azure-regio te kiezen die wordt gedekt door de gereserveerde capaciteit en voeg de reservering toe aan de winkelwagen.
1. Selecteer een Isolated-abonnementstype en klik vervolgens op **Selecteren**.  
    ![Voorbeeld](./media/prepay-app-service/app-service-isolated-stamp-select.png)
1. Voer het aantal te reserveren App Service Isolated-zegels in. Met een aantal van drie krijgt u bijvoorbeeld drie gereserveerde zegels per regio. Klik op **Next: Controleren en kopen**.
1. Controleer uw bestelling en klik op **Nu kopen**.

Na de aankoop kunt u op elk gewenst moment naar [Reserveringen](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) gaan om de status van de aankoop te bekijken.

## <a name="cancel-exchange-or-refund-reservations"></a>Annulering, omwisseling of terugbetaling van reserveringen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfservice voor ruiling en terugbetaling van Azure-reserveringen](exchange-and-refund-azure-reservations.md) voor meer informatie.

## <a name="discount-application-shown-in-usage-data"></a>Kortingstoepassing weergeven in gebruiksgegevens

Uw gebruiksgegevens kosten niets voor het deel waarvoor u een reserveringskorting verkrijgt. In de gebruiksgegevens wordt de reserveringskorting weergegeven voor elk zegelexemplaar in elke reservering.

Zie [Kosten en gebruik van Enterprise Agreement-reserveringen ophalen](understand-reserved-instance-usage-ea.md) voor meer informatie over het weergeven van reserveringskorting in gebruiksgegevens als u een Enterprise Agreement-klant (EA) bent. Zie anders [Meer informatie over het gebruik van Azure-reserveringen voor uw afzonderlijke abonnement met tarieven voor betalen per gebruik](understand-reserved-instance-usage.md).

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:
  - [Wat zijn Azure-reserveringen?](save-compute-costs-reservations.md)
  - [Meer informatie over hoe een korting voor Azure App Service Isolated-zegelreserveringen wordt toegepast](reservation-discount-app-service.md)
  - [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](understand-reserved-instance-usage-ea.md)