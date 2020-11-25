---
title: Gereserveerde capaciteit in Azure Cosmos DB om de kosten te optimaliseren
description: Meer informatie over het kopen van Azure Cosmos DB gereserveerde capaciteit om uw reken kosten op te slaan.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: tisande
ms.reviewer: sngun
ms.openlocfilehash: 27725b1a3dd6059010ce67977c39891a012c037e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995779"
---
# <a name="optimize-cost-with-reserved-capacity-in-azure-cosmos-db"></a>Kosten met gereserveerde capaciteit optimaliseren in Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Gereserveerde Azure Cosmos DB-capaciteit helpt om geld te besparen doordat u zich verplicht tot een reservering voor Azure Cosmos DB-resources voor een periode van één of drie jaar. Met gereserveerde Azure Cosmos DB-capaciteit kunt u een korting krijgen op de doorvoer die wordt ingericht voor Cosmos DB-resources. Voorbeelden van resources zijn databases en containers (tabellen, verzamelingen en grafieken).

Azure Cosmos DB gereserveerde capaciteit kan uw Cosmos DB kosten aanzienlijk reduceren &mdash; tot 65 procent op reguliere prijzen met een garantie van één jaar of drie jaar vooraf. Gereserveerde capaciteit biedt een facturerings korting en heeft geen invloed op de runtime status van uw Azure Cosmos DB-resources.

Azure Cosmos DB gereserveerde capaciteit heeft betrekking op de door Voer ingericht voor uw resources. De opslag- en netwerkkosten vallen hier niet onder. Zodra u een reserve ring koopt, worden de doorvoer kosten die overeenkomen met de reserverings kenmerken niet meer in rekening gebracht tegen de betalen naar gebruik-tarieven. Zie het artikel over [Azure-reserve ringen](../cost-management-billing/reservations/save-compute-costs-reservations.md) voor meer informatie over reserve ringen.

U kunt Azure Cosmos DB gereserveerde capaciteit kopen via de [Azure Portal](https://portal.azure.com). Betaal [vooraf of per maand](../cost-management-billing/reservations/prepare-buy-reservation.md) voor de reservering. Gereserveerde capaciteit kopen:

* U moet de rol van eigenaar zijn voor minstens één bedrijf of een afzonderlijk abonnement met betalen per gebruik-tarieven.  
* Voor Enterprise Agreements moet de optie **Gereserveerde instanties toevoegen** zijn ingeschakeld in de [EA Portal](https://ea.azure.com). Als deze instelling is uitgeschakeld, moet u een EA-beheerder zijn voor het abonnement.
* Voor het programma Cloud Solution Provider (CSP) kunnen alleen beheer agenten of verkoop medewerkers Azure Cosmos DB gereserveerde capaciteit kopen.

## <a name="determine-the-required-throughput-before-purchase"></a>De vereiste door Voer voor aankoop bepalen

De omvang van de inkoop met gereserveerde capaciteit moet worden gebaseerd op de totale hoeveelheid door Voer die de bestaande of binnenkort geïmplementeerde Azure Cosmos DB resources op elk uur gebruiken. Bijvoorbeeld: aanschaf van 30.000 RU/s gereserveerde capaciteit als dat het consistente gebruiks patroon van elk uur is. In dit voor beeld wordt een ingerichte door Voer van meer dan 30.000 RU/s gefactureerd op basis van uw betalen naar gebruik-tarief. Als ingerichte door Voer in een uur lager is dan 30.000 RU/s, wordt de extra gereserveerde capaciteit voor dat uur verspild.

We berekenen aankoop aanbevelingen op basis van het gebruiks patroon van uw uur. Het gebruik van de afgelopen 7, 30 en 60 dagen is geanalyseerd en de aanschaf van gereserveerde capaciteit waarmee uw besparing wordt gemaximaliseerd, wordt aanbevolen. Met de volgende stappen kunt u de aanbevolen reserverings grootten weer geven in de Azure Portal:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).  

2. Selecteer **alle services**-  >  **reserve ringen**  >  **toevoegen**.

3. Kies in het deel venster **aankoop reserveringen** de optie **Azure Cosmos DB**.

4. Selecteer het tabblad **Aanbevolen** om de aanbevolen reserve ringen te bekijken:

U kunt aanbevelingen filteren op de volgende kenmerken:

- **Termijn** (1 jaar of 3 jaar)
- **Facturerings frequentie** (maandelijks of vooraf)
- **Type door Voer** (ru/s versus meerdere REGIO'S write ru/s)

Daarnaast kunt u aanbevelingen bereiken binnen één resource groep, één abonnement of uw volledige Azure-inschrijving. 

Hier volgt een voor beeld van een aanbeveling:

:::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-recommendation.png" alt-text="Aanbevelingen voor gereserveerde capaciteit":::

Deze aanbeveling om een 30.000 RU/s-reserve ring te kopen, geeft aan dat de reserverings grootte van een 30.000 RU/sec. voor de reserve ring van drie jaar de besparingen kan maximaliseren. In dit geval wordt de aanbeveling berekend op basis van de afgelopen 30 dagen van Azure Cosmos DB gebruik. Als deze klant verwacht dat de afgelopen 30 dagen van Azure Cosmos DB gebruik representatief is voor toekomstig gebruik, maximaliseren ze de besparingen door een 30.000 RU/s-reserve ring aan te schaffen.

## <a name="buy-azure-cosmos-db-reserved-capacity"></a>Azure Cosmos DB gereserveerde capaciteit kopen

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).  

2. Selecteer **alle services**-  >  **reserve ringen**  >  **toevoegen**.  

3. Kies in het deel venster **aankoop reserveringen** **Azure Cosmos DB** om een nieuwe reserve ring te kopen.  

4. Vul de vereiste velden in zoals beschreven in de volgende tabel:

   :::image type="content" source="./media/cosmos-db-reserved-capacity/fill-reserved-capacity-form.png" alt-text="Het formulier voor de gereserveerde capaciteit invullen":::

   |Veld  |Description  |
   |---------|---------|
   |Bereik   |   Optie die bepaalt hoeveel abonnementen het facturerings voordeel kunnen gebruiken dat is gekoppeld aan de reserve ring. Het bepaalt ook hoe de reserve ring wordt toegepast op specifieke abonnementen. <br/><br/>  Als u **gedeeld** selecteert, wordt de reserverings korting toegepast op Azure Cosmos DB exemplaren die worden uitgevoerd in een abonnement binnen uw facturerings context. De facturerings context is gebaseerd op de manier waarop u zich hebt geregistreerd voor Azure. Voor zakelijke klanten is het gedeelde bereik de inschrijving en omvat alle abonnementen binnen de inschrijving. Voor betalen naar gebruik-klanten is de gedeelde Scope alle afzonderlijke abonnementen met betalen per gebruik-tarieven die door de account beheerder zijn gemaakt.  <br/><br/>  Als u **één abonnement** selecteert, wordt de reserverings korting toegepast op Azure Cosmos DB exemplaren in het geselecteerde abonnement. <br/><br/> Als u **één resource groep** selecteert, wordt de reserverings korting toegepast op Azure Cosmos DB exemplaren in het geselecteerde abonnement en de geselecteerde resource groep in dat abonnement. <br/><br/> U kunt het reserverings bereik wijzigen nadat u de gereserveerde capaciteit hebt gekocht.  |
   |Abonnement  |   Het abonnement dat wordt gebruikt om te betalen voor de Azure Cosmos DB gereserveerde capaciteit. De betalings wijze voor het geselecteerde abonnement wordt gebruikt bij het opladen van de kosten. Het abonnement moet een van de volgende typen zijn: <br/><br/>  Enterprise Agreement (nummers van aanbiedingen: MS-AZR-0017P of MS-AZR-0148P): voor een Enter prise-abonnement worden de kosten in rekening gebracht op basis van de monetaire toezeg ging van de inschrijving of worden aangerekend als overschrijding. <br/><br/> Individueel abonnement met betalen per gebruik-tarieven (aanbiedings nummers: MS-AZR-0003P of MS-AZR-0023P): voor een afzonderlijk abonnement met betalen per gebruik-tarieven, worden de kosten in rekening gebracht op de credit card-of factuur betalings methode voor het abonnement.    |
   | Resourcegroep | De resource groep waarop de gereserveerde capaciteits korting wordt toegepast. |
   |Termijn  |   Eén jaar of drie jaar.   |
   |Type door Voer   |  De door Voer is ingericht als aanvraag eenheden. U kunt een reserve ring voor de ingerichte door voer kopen voor zowel de installatie van één regio als voor meerdere schrijf bewerkingen in regio's. Het doorvoer type heeft twee waarden waaruit u kunt kiezen: 100 RU/s per uur en 100 multi-regio schrijft RU/s per uur.|
   | Gereserveerde capaciteits eenheden| De hoeveelheid door Voer die u wilt reserveren. U kunt deze waarde berekenen door de door voer te bepalen die nodig is voor al uw Cosmos DB resources (bijvoorbeeld data bases of containers) per regio. Vervolgens vermenigvuldigt u deze met het aantal regio's dat u koppelt aan uw Cosmos-data base. Bijvoorbeeld: als u vijf regio's met 1.000.000 RU per seconde in elke regio hebt, selecteert u 5.000.000 RU per seconde voor de aanschaf van de reserve ring capaciteit. |


5. Nadat u het formulier hebt ingevuld, wordt de prijs berekend die is vereist om de gereserveerde capaciteit aan te schaffen. De uitvoer toont ook het percentage van de korting die u krijgt met de gekozen opties. Klik vervolgens op **selecteren**

6. Controleer in het deel venster **aankoop reserveringen** de korting en de prijs van de reserve ring. Deze reserverings prijs is van toepassing op Azure Cosmos DB resources waarbij de door Voer is ingericht in alle regio's.  

   :::image type="content" source="./media/cosmos-db-reserved-capacity/reserved-capacity-summary.png" alt-text="Samen vatting van gereserveerde capaciteit":::

7. Selecteer **beoordelen + kopen** en **Nu kopen**. U ziet de volgende pagina wanneer de aankoop slaagt:

Nadat u een reserve ring hebt gekocht, wordt deze onmiddellijk toegepast op bestaande Azure Cosmos DB resources die overeenkomen met de voor waarden van de reserve ring. Als u geen bestaande Azure Cosmos DB resources hebt, wordt de reserve ring toegepast wanneer u een nieuwe Cosmos DB instantie implementeert die overeenkomt met de voor waarden van de reserve ring. In beide gevallen wordt de periode van de reserve ring direct na een succes volle aankoop gestart.

Wanneer de reserve ring verloopt, blijven uw Azure Cosmos DB-exemplaren actief en worden de normale tarieven voor betalen naar gebruik gefactureerd.

## <a name="cancel-exchange-or-refund-reservations"></a>Reserveringen annuleren, ruilen of terugbetalen

Annulering, ruiling of terugbetaling van reserveringen is mogelijk met bepaalde beperkingen. Zie [Selfserviceopties voor inwisselen en retourneren van Azure-reserveringen](../cost-management-billing/reservations/exchange-and-refund-azure-reservations.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

De reserverings korting wordt automatisch toegepast op de Azure Cosmos DB resources die overeenkomen met het reserverings bereik en de kenmerken. U kunt het bereik van de reserve ring bijwerken via de Azure Portal, Power shell, Azure CLI of de API.

*  Zie [inzicht in de korting voor Azure-reserve ringen](../cost-management-billing/reservations/understand-cosmosdb-reservation-charges.md)voor meer informatie over hoe gereserveerde capaciteits kortingen worden toegepast op Azure Cosmos db.

* Raadpleeg de volgende artikelen voor meer informatie over Azure-reserveringen:

   * [Wat zijn Azure-reserveringen?](../cost-management-billing/reservations/save-compute-costs-reservations.md)  
   * [Reserveringen beheren](../cost-management-billing/reservations/manage-reserved-vm-instance.md)  
   * [Inzicht in het gebruik van reserveringen voor Enterprise-inschrijvingen](../cost-management-billing/reservations/understand-reserved-instance-usage-ea.md)  
   * [Inzicht in het gebruik van reserveringen voor uw abonnement met betalen per gebruik](../cost-management-billing/reservations/understand-reserved-instance-usage.md)
   * [Azure-reserve ringen in het Partner Center CSP-programma](/partner-center/azure-reservations)

## <a name="need-help-contact-us"></a>Hulp nodig? Neem contact met ons op.

Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).