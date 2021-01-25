---
title: Veelgebruikte toepassingen van kostenanalyse in Azure Cost Management
description: In dit artikel wordt uitgelegd hoe u veelvoorkomende analysetaken kunt uitvoeren in Azure Cost Management.
author: bandersmsft
ms.author: banders
ms.date: 07/24/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 72b371fd1184c9c9e69593c5ad7e57310abf1de2
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600039"
---
# <a name="common-cost-analysis-uses"></a>Veelgebruikte toepassingen van kostenanalyse

Gebruikers van Azure Cost Management willen vaak antwoorden op vragen die veel andere gebruikers ook hebben gesteld. Dit artikel begeleidt u bij het uitvoeren van veelvoorkomende analysetaken in Cost Management.

## <a name="view-forecasted-costs"></a>Geraamde kosten weergeven

Geraamde kosten worden weergegeven in gebieden voor kostenanalyse, voor gebieds- en gestapelde kolomweergaven. De prognose is gebaseerd op uw resourcegebruik in het verleden. Wijzigingen in uw resourcegebruik zijn van invloed op de geraamde kosten.

Ga in de Azure-portal naar het onderdeel Kostenanalyse voor uw bereik. Bijvoorbeeld: **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.

In de standaardweergave bevat de bovenste grafiek de sectie Werkelijke/afgeschreven kosten en de sectie Geraamde kosten. De effen kleur van de grafiek duidt de Werkelijke/afgeschreven kosten aan. De gearceerde kleur duidt de Geraamde kosten aan.

[![Geraamde kosten](./media/cost-analysis-common-uses/enrollment-forecast.png)](./media/cost-analysis-common-uses/enrollment-forecast.png#lightbox)

## <a name="view-forecasted-costs-grouped-by-service"></a>Geraamde kosten weergeven gegroepeerd op service

De standaardweergave laat geen geraamde kosten zien gegroepeerd op een service, daarom moet u een groep kiezen om toe te voegen.

Ga in de Azure-portal naar het onderdeel Kostenanalyse voor uw bereik. Bijvoorbeeld: **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.

Selecteer **Groeperen op** > **Servicenaam**.

De weergave laat uw kosten zien, gegroepeerd voor elke service. De geraamde kosten worden niet voor elke service berekend. Ze worden geschat voor het **Totaal** van al uw services.

[![Gegroepeerde geraamde kosten](./media/cost-analysis-common-uses/forecast-group-by-service.png)](./media/cost-analysis-common-uses/forecast-group-by-service.png#lightbox)

## <a name="view-forecasted-costs-for-a-service"></a>Geraamde kosten voor een service weergeven

U kunt geraamde kosten weergeven die zijn beperkt tot één service. Het kan bijvoorbeeld voorkomen dat u alleen de geraamde kosten wilt zien voor virtuele machines.

1. Ga in de Azure-portal naar het onderdeel Kostenanalyse voor uw bereik. Bijvoorbeeld: **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.
1. Selecteer **Filter toevoegen** en selecteer vervolgens **Servicenaam**.
1. Selecteer een service in de lijst **Kiezen**. Selecteer bijvoorbeeld **virtuele machines**.

Bekijk de werkelijke kosten voor de selectie en de geraamde kosten.

U kunt meer aanpassingen aan de weergave toevoegen.

1. Voeg een tweede filter toe voor **Meter**, en selecteer een waarde om te filteren op een afzonderlijk metertype onder de geselecteerde servicenaam.
1. Groepeer op **Resource** om de specifieke resources te zien waarvoor kosten in rekening worden gebracht. De geraamde kosten worden niet voor elke service berekend. Ze worden geschat voor het **Totaal** van al uw resources.

[![Geraamde kosten voor een service](./media/cost-analysis-common-uses/forecast-by-service.png)](./media/cost-analysis-common-uses/forecast-by-service.png#lightbox)

## <a name="view-your-azure-and-aws-costs-together"></a>Uw totale kosten voor Azure en AWS weergeven  

Als u de totale kosten voor Azure en AWS wilt weergeven, gebruikt u beheergroepsbereiken in Azure.

1. Maak een beheergroep of selecteer een bestaande beheergroep.
1. Wijs de bestaande Azure-abonnementen die u nodig hebt, toe aan de beheergroep.
1. Wijs *dezelfde* beheergroep toe aan het gekoppelde account van de connector.
1. Ga naar Kostenanalyse en selecteer **Samengevoegde kosten**.
1. Selecteer **Groeperen op** - **Provider**.

## <a name="view-cost-breakdown-by-azure-service"></a>Uitsplitsing van kosten weergeven per Azure-service

Door de kosten per Azure-service te bekijken, kunt u een beter inzicht krijgen in de onderdelen van uw infrastructuur die de meeste kosten met zich meebrengen. De compute-kosten voor VM's kunnen bijvoorbeeld laag zijn. Desondanks kunnen de netwerkkosten aanzienlijk zijn vanwege de hoeveelheid gegevens die afkomstig zijn van de VM's. Een duidelijk beeld van de primaire kostenfactoren van uw Azure-Services is essentieel om waar nodig het servicegebruik te kunnen aanpassen.

1. Ga in de Azure-portal naar het onderdeel Kostenanalyse voor uw bereik. Bijvoorbeeld: **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.
1. Selecteer **Kosten per service** en groepeer vervolgens op **Servicelaag**.
1. Wijzig de weergave in **Tabel**.

[![Uitsplitsing van kosten per Azure-service](./media/cost-analysis-common-uses/breakdown-by-service.png)](./media/cost-analysis-common-uses/breakdown-by-service.png#lightbox)

## <a name="review-invoiced-charges-in-cost-analysis"></a>Gefactureerde kosten in kosten analyse bekijken

Als u de factuurgegevens in de Azure-portal wilt bekijken, navigeert u naar kostenanalyse voor het bereik dat hoort bij de factuur die u analyseert. Selecteer de weergave **Factuurgegevens**. Factuurgegevens laten u de kosten van de factuur zien.

[![Voorbeeld met factuurgegevens](./media/cost-analysis-common-uses/invoice-details.png)](./media/cost-analysis-common-uses/invoice-details.png#lightbox)

Als u de factuurgegevens bekijkt, kunt u de service identificeren die onverwachte kosten heeft en bepalen welke resources rechtstreeks zijn gekoppeld aan de resource in de kostenanalyse. Als u bijvoorbeeld kosten wilt analyseren voor de virtuele-machineservice, gaat u naar de weergave **Geaccumuleerde kosten**. Stel vervolgens de granulariteit in op **Dagelijks** en filter kosten op **Servicenaam: Virtuele machines** en groepskosten op **Resource**.

[![Voorbeeld van samengevoegde kosten voor virtuele machines](./media/cost-analysis-common-uses/virtual-machines.png)](./media/cost-analysis-common-uses/virtual-machines.png#lightbox)

## <a name="view-cost-breakdown-by-azure-resource"></a>Uitsplitsing van kosten weergeven per Azure-resource

Uw services zijn gebouwd met Azure-resources. Door kosten op basis van resources te bekijken, kunt u snel de primaire kostenfactoren identificeren. Als een service resources heeft die te duur zijn, kunt u overwegen om wijzigingen aan te brengen om uw kosten te verlagen.

1. Ga in de Azure-portal naar het onderdeel Kostenanalyse voor uw bereik. Bijvoorbeeld: **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.
1. Selecteer **Kosten per resource**
1. Wijzig de weergave in **Tabel**.

[![Uitsplitsing van kosten weergeven per Azure-resource](./media/cost-analysis-common-uses/cost-by-resource.png)](./media/cost-analysis-common-uses/cost-by-resource.png#lightbox)

## <a name="view-cost-breakdown-by-selected-dimensions"></a>Uitsplitsing van kosten weergeven op geselecteerde dimensies

Met dimensies kunt u uw kosten indelen op basis van verschillende waarden voor metagegevens die worden weergegeven in uw kosten. U kunt uw kosten bijvoorbeeld groeperen op locatie.

1. Ga in de Azure-portal naar het onderdeel Kostenanalyse voor uw bereik. Bijvoorbeeld: **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.
1. Selecteer het filter **Groeperen op**.  
    [![Selecteer een optie voor Groeperen op](./media/cost-analysis-common-uses/group-by.png)](./media/cost-analysis-common-uses/group-by.png#lightbox)
1. U kunt de weergave eventueel opslaan voor later gebruik.
1. Klik op een cirkeldiagram onder het diagram om meer gedetailleerde gegevens weer te geven.  
    [![Uitsplitsing van kosten weergeven op geselecteerde dimensies](./media/cost-analysis-common-uses/drill-down.png)](./media/cost-analysis-common-uses/drill-down.png#lightbox)

## <a name="view-costs-per-day-or-by-month"></a>Kosten per dag of per maand weergeven

Een goed overzicht van de dagelijkse en maandelijkse kosten kan helpen om vast te stellen of er een tijd van de week of het jaar is waarin uw kosten hoger zijn. Als u in een vakantieperiode meer klantverkeer hebt, leidt dat dan tot een evenredige toename van de Azure-kosten? Is vrijdag een dag met meer kosten dan maandag?

1. Ga in de Azure-portal naar het onderdeel Kostenanalyse voor uw bereik. Bijvoorbeeld: **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.
1. Stel **Granulariteit** in op **Maandelijks** of **Dagelijks**.

[![Kosten per dag weergeven](./media/cost-analysis-common-uses/daily-granularity.png)](./media/cost-analysis-common-uses/daily-granularity.png#lightbox)


## <a name="view-your-spot-vm-charges"></a>De kosten van uw spot-VM weergeven

Spot-VM's kunnen grote kostenbesparingen opleveren voor workloads die onderbrekingen kunnen afhandelen. Workloads worden uitgevoerd op ongebruikte Azure-capaciteit. Omdat deze op elk gewenst moment kunnen worden verwijderd, geldt voor spot-VM's een aanzienlijke korting. Voer de volgende stappen uit om de kosten voor uw spot-VM weer te geven.

1. Ga in de Azure-portal naar het onderdeel Kostenanalyse voor uw bereik. Bijvoorbeeld **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.
2. Een filter toevoegen voor **prijsmodel: Spot**.

![Voorbeeld van een spot-VM-filter](./media/cost-analysis-common-uses/spot-vm-filter.png)

De prijsmodeldimensie wordt ook gebruikt om kosten voor op aanvraag en voor reserveringen weer te geven.

## <a name="view-your-reservation-charges"></a>De reserveringskosten weergeven

Gereserveerde instanties zijn een manier om geld te besparen met Azure. Door gebruik te maken van reserveringen betaalt u vooraf voor een bepaald aantal resources voor een bepaalde periode. Kostenanalyse toont de kosten zoals deze op uw factuur worden weergegeven. De kosten worden weergegeven als werkelijke kosten of worden afgeschreven over de loop van de reserveringsperiode.

1. Ga in de Azure-portal naar het onderdeel Kostenanalyse voor uw bereik. Bijvoorbeeld **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.
1. Een filter toevoegen voor **prijsmodel: Reservering**.
1. Klik onder **Bereik** naast de weergegeven kosten op de pijl-omlaag, selecteer de kostenmetriek **Werkelijke kosten** of **Afgeschreven kosten**.

![Een kostenmetriek selecteren](./media/cost-analysis-common-uses/metric-cost.png)

Elke metriek heeft invloed op de manier waarop gegevens worden weergegeven voor de reserveringskosten.

**Werkelijke kosten**: toont de aankoop zoals die op uw factuur wordt weergegeven. Als u bijvoorbeeld een reservering voor één jaar hebt gekocht voor € 1200 in januari, wordt er in de kostenanalyse een kostenpost van € 1200 in de maand januari voor de reservering weergegeven. Er worden geen reserveringskosten weergegeven voor de andere maanden van het jaar. Als u de werkelijke kosten per VM groepeert, zouden er voor een VM die voor een bepaalde maand het voordeel van de reservering heeft gekregen, voor die maand geen kosten zijn.

**Afgeschreven kosten**: dit is een uitsplitsing van de reserveringsaankoop die wordt weergegeven als afgeschreven kosten voor de looptijd van de reserveringstermijn. Als we hetzelfde voorbeeld van voorheen gebruiken, ziet u dat in de kostenanalyse voor elke maand gedurende het hele jaar een kostenpost van € 100 wordt weergegeven, als u in januari een reservering van één jaar hebt aangeschaft voor € 1200. Als u in dit voorbeeld de kosten per VM groepeert, ziet u dat er kosten worden toegekend aan elke virtuele machine die het voordeel van de reservering heeft gekregen.

## <a name="view-your-reservation-utilization"></a>Reserveringsgebruik weergeven

Nadat u een reservering hebt gekocht, is het belangrijk dat u het gebruik ervan bijhoudt, zodat u weet dat u krijgt waarvoor u hebt betaald. Als u bijvoorbeeld tien VM's hebt gekocht voor een jaar en er slechts vijf hebt gebruikt, betekent dat dat de helft van de aankoop een onnodige investering is geweest. Er zijn twee verschillende manieren om uw gebruik te beoordelen:

### <a name="view-unused-ri-costs-in-cost-analysis"></a>Kosten voor gereserveerde instanties bekijken in kostenanalyse

Volg de onderstaande stappen om te bepalen hoeveel onnodige kosten momenteel elke maand worden gemaakt voor uw reserveringsaankoop.

1. Ga in de Azure-portal naar Kostenanalyse voor het bereik waar uw reservering op wordt toegepast. Bijvoorbeeld **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.
1. Een filter toevoegen voor **prijsmodel: Reservering**.
1. Selecteer de weergave **Afgeschreven kosten**.
1. Stel de granulariteit in op **Maandelijks**.
1. Stel de periode in op het huidige jaar of op uw reserveringstermijn.
1. Stel het grafiektype in op **Kolom (gestapeld)** .
1. Groepeer de kosten per **kostentype**.
1. Controleer de resultaten op waarden voor `unusedreservation`.

[![Voorbeeld met het gebruik van de reservering](./media/cost-analysis-common-uses/view-reservation-cost.png)](./media/cost-analysis-common-uses/view-reservation-cost.png#lightbox)

### <a name="view-utilization-in-reservations"></a>Gebruik in Reserveringen weergeven

Zie [Gebruik van reservering optimaliseren](../reservations/manage-reserved-vm-instance.md#optimize-reservation-use) voor gedetailleerde instructies.

## <a name="view-costs-for-a-specific-tag"></a>Kosten voor een specifieke tag weergeven

Veel Azure-gebruikers passen tags toe op hun resources, zoals een kostenplaats of een ontwikkelomgeving (productie en test), om de kosten beter te categoriseren. Tags worden als een dimensie weergegeven in Kostenanalyse. U kunt de dimensie gebruiken om inzicht te krijgen in uw aangepaste categorisaties met behulp van tags.

Ondersteuning voor labels is van toepassing op het gebruik dat is gerapporteerd *nadat* de tag is toegepast op de resource. Tags worden niet met terugwerkende kracht toegepast voor samengetelde kosten.

1. Ga in de Azure-portal naar het onderdeel Kostenanalyse voor uw bereik. Bijvoorbeeld: **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.
1. Selecteer **Groeperen op** voor de tag.

[![Kosten voor een specifieke tag weergeven](./media/cost-analysis-common-uses/tag.png)](./media/cost-analysis-common-uses/tag.png#lightbox)

## <a name="download-your-usage-details"></a>Uw gebruiksgegevens downloaden

Het rapportbestand met gebruiksgegevens, in CSV-indeling, bevat een overzicht van alle kosten die in rekening worden gebracht op een factuur. U kunt het rapport gebruiken om uw gegevens te vergelijken uw factuur om zo een beter inzicht te krijgen. Alle gefactureerde kosten op uw factuur komen overeen met de onderverdeling van kosten in het gebruiksrapport.

1. Ga in de Azure-portal naar het tabblad **Gebruik en kosten** voor een factureringsaccount of abonnement. Bijvoorbeeld: **Kostenbeheer en facturering** > **Facturering** > **Gebruik + kosten**.
1. Selecteer het regelitem dat u wilt downloaden en klik vervolgens op het downloadsymbool.  
    [![Gebruik en kosten downloaden](./media/cost-analysis-common-uses/download1.png)](./media/cost-analysis-common-uses/download1.png#lightbox)
1.  Selecteer het gebruiksbestand dat u wilt downloaden.  
    ![Kies een gebruiksbestand om te downloaden](./media/cost-analysis-common-uses/download2.png)

## <a name="view-monthly-ea-cost-breakdown"></a>Uitsplitsing van maandelijkse EA-kosten weergeven

Uw EA-inschrijving gaat gepaard met kosten voor de hele organisatie. Als u begrijpt hoe kosten worden gemaakt en gefactureerd in de loop van de tijd, kunt u de betrokken belanghebbenden ervan overtuigen dat ze de kosten op een verantwoorde manier moet beheren.

Er worden alleen kosten weergegeven voor uw actieve inschrijving. Als u een inschrijving (inactief) hebt overgebracht naar een nieuwe inschrijving (actief), worden de kosten voor de eerdere inschrijving niet weergegeven in Cost Management.


1. Ga in de Azure-portal naar **Kostenbeheer en facturering** > **Overzicht**.
1. Klik op **Uitsplitsing** voor de huidige maand en kijk waaruit de kosten voor uw Azure-vooruitbetaling (voorheen financiële toezegging) zijn opgebouwd.  
    [![Overzicht EA-kosten overzicht van uitsplitsing](./media/cost-analysis-common-uses/breakdown1.png)](./media/cost-analysis-common-uses/breakdown1.png#lightbox)
1.  Klik op het tabblad **Gebruik en kosten** en bekijk de uitsplitsing van de vorige maand in de gekozen tijdsperiode.  
    [![Het tabblad Gebruik en kosten](./media/cost-analysis-common-uses/breakdown2.png)](./media/cost-analysis-common-uses/breakdown2.png#lightbox)

## <a name="view-enrollment-monthly-cost-by-term"></a>Maandelijkse inschrijvingskosten weergeven per periode

Gebruik een grafische weergave van de maandelijkse kosten van uw inschrijving om inzicht te krijgen in de kostentrends en gefactureerde bedragen voor een bepaalde periode.

1. Ga in de Azure-portal naar het onderdeel Kostenanalyse voor uw bereik. Bijvoorbeeld: **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.
1. Selecteer uw inschrijving en stel de inschrijvingsperiode in.
1. Stel de granulariteit in op maandelijks en stel de weergave in op **Kolom (gestapeld)** .

U kunt gegevens groeperen en filteren voor een meer gedetailleerde analyse.

[![Maandelijkse inschrijvingskosten per periode](./media/cost-analysis-common-uses/enrollment-term1.png)](./media/cost-analysis-common-uses/enrollment-term1.png#lightbox)

## <a name="view-ea-enrollment-accumulated-costs"></a>Samengevoegde kosten voor EA-inschrijving weergeven

Bekijk de netto samengevoegde kosten om inzicht te krijgen in de algemene uitgaven voor uw organisatie gedurende een bepaalde periode.

1. Ga in de Azure-portal naar het onderdeel Kostenanalyse voor uw bereik. Bijvoorbeeld: **Kostenbeheer en facturering** > **Kostenbeheer** > **Kostenanalyse**.
1. Selecteer uw inschrijving en bekijk de huidige samengevoegde kosten.

[![Samengevoegde kosten voor inschrijving](./media/cost-analysis-common-uses/cost-analysis-enrollment.png)](./media/cost-analysis-common-uses/cost-analysis-enrollment.png#lightbox)

## <a name="next-steps"></a>Volgende stappen
- Als u de eerste quickstart voor Cost Management nog niet hebt voltooid, leest u die op [Kosten analyseren](quick-acm-cost-analysis.md).
- Lees de [documentatie van Cost Management](../index.yml).
