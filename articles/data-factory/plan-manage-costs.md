---
title: Kosten plannen en beheren voor Azure Data Factory
description: In dit artikel wordt beschreven hoe u de kosten voor Azure Data Factory kunt plannen en beheren
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: d89275acdb8e13a792ea3166d874fabf911bb686
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100372889"
---
# <a name="plan-and-manage-costs-for-azure-data-factory"></a>Kosten plannen en beheren voor Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory is een serverloze en elastische gegevens integratie service die is gebouwd voor Cloud schaal.  Dit betekent dat er geen Compute met een vaste grootte is die u moet plannen voor piek belasting. in plaats daarvan geeft u op hoeveel resource u op aanvraag per bewerking wilt toewijzen, zodat u de ETL-processen op een veel meer schaal bare manier kunt ontwerpen. Daarnaast wordt ADF gefactureerd op basis van een op verbruik gebaseerd abonnement, wat betekent dat u alleen betaalt voor wat u gebruikt.

In dit artikel wordt beschreven hoe u de kosten voor Azure Data Factory kunt plannen en beheren.

*   Eerst voert u aan het begin van het ETL-project het concept testen uit en gebruikt u een combi natie van verbruik per pijp lijn en prijs calculator om de kosten te schatten.
*   Nadat u uw pijp lijnen voor productie hebt geïmplementeerd, gebruikt u de functies voor kosten beheer om budgetten in te stellen en kosten te bewaken. U kunt ook de geraamde kosten bekijken en trends in de uitgaven identificeren.
*   Daarnaast kunt u het verbruik per pijp lijn en informatie over het gebruik per activiteit weer geven om te begrijpen welke pijp lijnen en welke activiteiten Costliest zijn en kandidaten voor de kosten reductie identificeren.

## <a name="estimate-costs-using-pipeline-and-activity-run-consumption-and-pricing-calculator"></a>Kosten ramen met behulp van pijp lijn-en activiteit uitvoeren verbruik en prijs calculator

U kunt de [ADF-prijs calculator](https://azure.microsoft.com/pricing/calculator/?service=data-factory) gebruiken om een schatting te krijgen van de kosten voor het uitvoeren van uw ETL-workload in azure Data Factory.  Als u de calculator wilt gebruiken, moet u gegevens invoeren, zoals het aantal uitgevoerde activiteiten, het aantal gegevens integratie-eenheids uren, het type berekening dat wordt gebruikt voor de gegevens stroom, het aantal kernen, het aantal exemplaren, de uitvoerings duur, enzovoort.

Een van de veelgestelde vragen over de prijs calculator is welke waarden moeten worden gebruikt als invoer.  Tijdens de test fase kunt u proef bewerkingen uitvoeren met behulp van voorbeeld gegevens sets om het verbruik van verschillende ADF-meters te begrijpen.  Op basis van het verbruik voor de voor beeld-gegevensset kunt u het verbruik voor de volledige gegevensset en de uitoefening-planning projecteren.

> [!NOTE]
> De prijzen die in deze voor beelden worden gebruikt, zijn hypothetisch en zijn niet bedoeld als werkelijke prijzen.

Stel bijvoorbeeld dat u 1 TB aan gegevens dagelijks wilt verplaatsen van AWS S3 naar Azure Data Lake Gen2.  U kunt het verplaatsen van 100 GB aan gegevens uitvoeren om de door Voer van gegevens opname te meten en inzicht te krijgen in het bijbehorende facturerings verbruik.

Hier volgt een voor beeld van een Details over het uitvoeren van een Kopieer activiteit (uw werkelijke kilometer vergoeding is afhankelijk van de vorm van uw specifieke gegevensset, netwerk snelheden, uitvoer limieten voor S3-account, ingangs limieten voor ADLS Gen2 en andere factoren).

![De S3-kopie wordt uitgevoerd](media/plan-manage-costs/s3-copy-run-details.png)

Door gebruik te maken van de [verbruiks bewaking op het niveau van de pijp lijn](#monitor-consumption-at-pipeline-run-level), kunt u de bijbehorende hoeveelheid verbruiks meters voor gegevens verplaatsing zien:

![S3-pijp lijn verbruik kopiëren](media/plan-manage-costs/s3-copy-pipeline-consumption.png)

Daarom is het totale aantal DIU dat nodig is om 1 TB per dag voor de hele maand te verplaatsen:

1,2667 (DIU-uren) * (1 TB/100 GB) * 30 (dagen per maand) = 380 DIU-uur

Nu kunt u 30 activiteiten en 380 DIU-uren in de ADF-prijs calculator aansluiten om een schatting te krijgen van uw maandelijkse factuur:

![Prijs calculator voor kopiëren van S3](media/plan-manage-costs/s3-copy-pricing-calculator.png)

## <a name="use-budgets-and-cost-alerts"></a>Budgetten en kostenwaarschuwingen gebruiken

U kunt [budgetten](../cost-management-billing/costs/tutorial-acm-create-budgets.md) maken om kosten te beheren en waarschuwingen te maken waarmee belanghebbenden automatisch worden geïnformeerd over afwijkende uitgaven en het risico om teveel uit te geven.  Waarschuwingen zijn gebaseerd op de vergelijking tussen uitgaven en drempelwaarden voor budgetten en kosten.  Wanneer u een budget maakt, kunt u dit doen op het abonnements niveau of op een lagere granulatie door extra filters toe te voegen, zoals de resource-ID en de naam van de meter.  U kunt echter geen budgetten maken voor afzonderlijke pijp lijnen in een fabriek.

## <a name="monitor-costs-at-factory-level"></a>Bewaak de kosten op het niveau van de fabriek

Als u begint met het gebruik van Azure Data Factory, ziet u de kosten die zijn gemaakt in het deel venster [kosten analyse](../cost-management-billing/costs/quick-acm-cost-analysis.md) in de Azure Portal.

1. Als u de [kosten analyse](../cost-management-billing/costs/quick-acm-cost-analysis.md)wilt weer geven, opent u het venster **Cost Management en facturering** , selecteert u **kosten beheer** in het menu en selecteert u vervolgens **kosten analyse openen**.
2. In de standaard weergave worden de gecumuleerde kosten voor de huidige maand weer gegeven.  U kunt overschakelen naar een ander tijds bereik en een andere granulariteit, zoals dagelijks of maandelijks.
3. Als u de kosten voor één service, zoals Azure Data Factory, wilt beperken, selecteert u **filter toevoegen** en selecteert u vervolgens **service naam**.  Kies vervolgens **Azure Data Factory v2** in de lijst.
4. U kunt extra filters toevoegen om de kosten voor een specifiek fabrieks exemplaar en de specifieke granulatie van de ADF-meter te analyseren.

   ![Kostenanalyse](media/plan-manage-costs/cost-analysis.png)

## <a name="monitor-consumption-at-pipeline-run-level"></a>Verbruik op pijp lijn controleren-uitvoerings niveau

Afhankelijk van de typen activiteiten die u in uw pijp lijn hebt, hoeveel gegevens u verplaatst en transformeert en de complexiteit van de trans formatie, worden er verschillende facturerings meters in Azure Data Factory.

U kunt de hoeveelheid verbruik voor verschillende meters voor afzonderlijke pijplijn uitvoeringen weer geven in de gebruikers ervaring van Azure Data Factory. Als u de bewakings ervaring wilt openen, selecteert u de **Monitor & tegel beheren** op de blade Data Factory van de [Azure Portal](https://portal.azure.com/). Als u zich al in de ADF-UX bevindt, klikt u op het pictogram **bewaken** in de zijbalk aan de linkerkant. De standaard weergave voor bewaking is een lijst met pijplijn uitvoeringen.

Als u op de knop **verbruik** naast de naam van de pijp lijn klikt, wordt er een pop-upvenster weer gegeven waarin u het verbruik van de pijplijn uitvoering hebt geaggregeerd voor alle activiteiten in de pijp lijn.

![Verbruik van pijplijn uitvoering](media/plan-manage-costs/pipeline-run-consumption.png)

![Details van pijplijn verbruik](media/plan-manage-costs/pipeline-consumption-details.png)

In de weer gave verbruik van pijp lijn uitvoeren ziet u het verbruikte aantal voor elke ADF-meter voor de specifieke pijplijn uitvoering, maar de werkelijke prijs wordt niet weer gegeven, omdat de hoeveelheid die aan u wordt gefactureerd, afhankelijk is van het type Azure-account dat u hebt en het type valuta dat wordt gebruikt.  Zie voor de volledige lijst met ondersteunde accounttypen [Gegevens van Azure Cost Management begrijpen](../cost-management-billing/costs/understand-cost-mgt-data.md).

## <a name="monitor-consumption-at-activity-run-level"></a>Verbruik op activiteit controleren-uitvoerings niveau
Zodra u het geaggregeerde verbruik op pijplijn niveau hebt begrepen, zijn er scenario's waarin u verder kunt inzoomen en moet worden vastgesteld wat de kostbaarste activiteit binnen de pijp lijn is.

Voor een overzicht van het verbruik op activiteit-uitvoerings niveau, gaat u naar uw data factory **Author & monitor** -gebruikers interface. Klik op het tabblad **monitor** , waar u een lijst met pijplijn uitvoeringen ziet, op de koppeling van de **pijplijn naam** voor toegang tot de lijst met uitvoeringen van activiteiten in de pijplijn uitvoering.  Klik op de knop **uitvoer** naast de naam van de activiteit en zoek naar de eigenschap **BILLABLEDURATION** in de JSON-uitvoer:

Hier volgt een voor beeld van het uitvoeren van een Kopieer activiteit:

![Uitvoer kopiëren](media/plan-manage-costs/copy-output.png)

En hier volgt een voor beeld van een activiteit voor het uitvoeren van toewijzings gegevens stromen:

![Uitvoer gegevensstroom](media/plan-manage-costs/dataflow-output.png)

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over de werking van prijzen in Azure Data Factory:

- [Pagina met Azure Data Factory prijzen](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [Uitleg over Azure Data Factory via voor beelden](./pricing-concepts.md)
- [Azure Data Factory prijs calculator](https://azure.microsoft.com/pricing/calculator/?service=data-factory)