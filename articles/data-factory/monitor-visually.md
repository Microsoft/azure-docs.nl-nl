---
title: Azure Data Factory visueel bewaken
description: Meer informatie over het visueel bewaken van Azure-gegevens fabrieken
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: d177513af9f0ee4fcadb1ea316edf1ad8cb89e5a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104783656"
---
# <a name="visually-monitor-azure-data-factory"></a>Azure Data Factory visueel bewaken

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Zodra u een pijp lijn in Azure Data Factory hebt gemaakt en gepubliceerd, kunt u deze koppelen aan een trigger of hand matig een ad-hoc-uitvoering starten. U kunt al uw pijplijn uitvoeringen in de Azure Data Factory gebruikers ervaring zelf bewaken. Als u de bewakings ervaring wilt openen, selecteert u de **Monitor & tegel beheren** op de blade Data Factory van de [Azure Portal](https://portal.azure.com/). Als u zich al in de ADF-UX bevindt, klikt u op het pictogram **bewaken** in de zijbalk aan de linkerkant.

Standaard worden alle data factory-uitvoeringen weer gegeven in de lokale tijd zone van de browser. Als u de tijd zone wijzigt, worden alle datum-en tijd velden uitgelijnd op het veld dat u hebt geselecteerd.

## <a name="monitor-pipeline-runs"></a>Pijplijnuitvoeringen controleren

De standaard weergave voor bewaking is een lijst met geactiveerde pijplijn uitvoeringen in de geselecteerde tijds periode. U kunt het tijds bereik wijzigen en filteren op status, pijplijn naam of aantekening. Beweeg de muis aanwijzer over de specifieke pijplijn uitvoering om runtime-specifieke acties, zoals opnieuw uitvoeren en het verbruiks rapport, op te halen.

![Lijst weergave voor het bewaken van pijplijn uitvoeringen](media/monitor-visually/pipeline-runs.png)

Het raster pijp lijn uitvoeren bevat de volgende kolommen:

| **Kolomnaam** | **Beschrijving** |
| --- | --- |
| Naam van pijp lijn | Naam van de pijp lijn |
| Start uitvoeren | Begin datum en-tijd voor de pijplijn uitvoering (MM/DD/JJJJ, uu: MM: SS AM/PM) |
| Einde van de uitvoering | Eind datum en-tijd voor de pijplijn uitvoering (MM/DD/JJJJ, uu: MM: SS AM/PM) |
| Duur | Uitvoerings duur (UU: MM: SS) |
| Geactiveerd door | De naam van de trigger die de pijp lijn heeft gestart |
| Status | **Mislukt**, **geslaagd**, **in behandeling**, **geannuleerd** of in **wachtrij** |
| Aantekeningen | Filter bare labels die zijn gekoppeld aan een pijp lijn  |
| Parameters | Para meters voor de pijp lijn run (naam/waarde-paren) |
| Fout | Als de pijp lijn is mislukt, wordt de uitvoerings fout |
| Uitvoerings-id | ID van de pijplijn uitvoering |

U moet hand matig de knop **vernieuwen** selecteren om de lijst met pijp lijn-en activiteit uitvoeringen te vernieuwen. Automatisch vernieuwen wordt op dit moment niet ondersteund.

![Knop Vernieuwen](media/monitor-visually/refresh.png)

Als u de resultaten van een debug-uitvoering wilt weer geven, selecteert u het tabblad **fout opsporing** .

![Selecteer het pictogram actieve debug-uitvoeringen weer geven](media/iterative-development-debugging/view-debug-runs.png)

## <a name="monitor-activity-runs"></a>Uitvoering van activiteiten controleren

Klik op de naam van de pijp lijn voor een gedetailleerde weer gave van de uitvoering van een specifieke pijplijn uitvoering.

![Uitvoeringen van activiteit bekijken](media/monitor-visually/view-activity-runs.png)

In de lijst weergave worden de uitvoeringen van activiteiten weer gegeven die overeenkomen met elke pijplijn uitvoering. Beweeg de muis aanwijzer over de specifieke uitvoering van de activiteit om toepassingsspecifieke informatie op te halen, zoals de JSON-invoer, JSON-uitvoer en gedetailleerde bewakings ervaringen met activiteiten.

![Er is informatie over SalesAnalyticsMLPipeline, gevolgd door een lijst met uitvoeringen van de activiteit.](media/monitor-visually/activity-runs.png)

| **Kolomnaam** | **Beschrijving** |
| --- | --- |
| Activiteitsnaam | De naam van de activiteit in de pijp lijn |
| Type activiteit | Type activiteit, zoals **copy**, **ExecuteDataFlow** of **AzureMLExecutePipeline** |
| Acties | Pictogrammen waarmee u JSON-invoer gegevens, JSON-uitvoer gegevens of gedetailleerde activiteiten voor bewaking kunt weer geven | 
| Start uitvoeren | Begin datum en-tijd voor de uitvoering van de activiteit (MM/DD/JJJJ, uu: MM: SS AM/PM) |
| Duur | Uitvoerings duur (UU: MM: SS) |
| Status | **Mislukt**, **geslaagd**, **in uitvoering** of **geannuleerd** |
| Integration Runtime | Waarop Integration Runtime de activiteit is uitgevoerd |
| Gebruikerseigenschappen | Door de gebruiker gedefinieerde eigenschappen van de activiteit |
| Fout | Als de activiteit is mislukt, wordt de uitvoerings fout |
| Uitvoerings-id | ID van de uitvoering van de activiteit |

Als een activiteit is mislukt, kunt u het gedetailleerde fout bericht zien door te klikken op het pictogram in de kolom fout. 

![Er wordt een melding weer gegeven met fout details, waaronder fout code, fout type en fout Details.](media/monitor-visually/activity-run-error.png)

### <a name="promote-user-properties-to-monitor"></a>Gebruikers eigenschappen promo veren om te bewaken

Promoot elke pijplijn activiteit eigenschap als een gebruikers eigenschap, zodat deze een entiteit wordt die u bewaken. U kunt bijvoorbeeld de **bron** -en **doel** eigenschappen van de Kopieer activiteit in uw pijp lijn promo veren als gebruikers eigenschappen.

> [!NOTE]
> U kunt Maxi maal vijf eigenschappen van pijplijn activiteit verhogen als gebruikers eigenschappen.

![Gebruikers eigenschappen maken](media/monitor-visually/promote-user-properties.png)

Nadat u de gebruikers eigenschappen hebt gemaakt, kunt u deze bewaken in de controle lijst weergaven.

![Kolommen voor gebruikers eigenschappen toevoegen aan de lijst met uitvoeringen van activiteit](media/monitor-visually/choose-user-properties.png)

 Als de bron voor de Kopieer activiteit een tabel naam is, kunt u de naam van de bron tabel bewaken als een kolom in de lijst weergave voor uitvoeringen van activiteit.

![Lijst met uitgevoerde activiteiten met kolommen voor gebruikers eigenschappen](media/monitor-visually/view-user-properties.png)

## <a name="rerun-pipelines-and-activities"></a>Pijp lijnen en activiteiten opnieuw uitvoeren

Als u een pijp lijn die eerder is uitgevoerd vanaf het begin opnieuw wilt uitvoeren, houdt u de muis aanwijzer over de specifieke uitvoering van de pijp lijn en selecteert u **opnieuw uitvoeren**. Als u meerdere pijp lijnen selecteert, kunt u de knop **opnieuw** uitvoeren gebruiken om ze allemaal uit te voeren.

![Een pijp lijn opnieuw uitvoeren](media/monitor-visually/rerun-pipeline.png)

Als u opnieuw wilt starten vanaf een specifiek punt, kunt u dit doen vanuit de weer gave uitvoeringen van activiteit. Selecteer de activiteit die u wilt starten en selecteer **opnieuw uitvoeren vanuit activiteit**. 

![Uitvoering van een activiteit opnieuw uitvoeren](media/monitor-visually/rerun-activity.png)

### <a name="rerun-from-failed-activity"></a>Opnieuw uitvoeren op basis van mislukte activiteit

Als een activiteit mislukt, er een time-out optreedt of de bewerking wordt geannuleerd, kunt u de pijp lijn opnieuw uitvoeren vanaf de mislukte activiteit door **opnieuw uitvoeren te selecteren op basis van de mislukte activiteit**.

![Mislukte activiteit opnieuw uitvoeren](media/monitor-visually/rerun-failed-activity.png)

### <a name="view-rerun-history"></a>Geschiedenis van opnieuw uitvoeren weer geven

U kunt de geschiedenis van opnieuw uitvoeren weer geven voor alle pijplijn uitvoeringen in de lijst weergave.

![Geschiedenis weergeven](media/monitor-visually/rerun-history-1.png)

U kunt ook de uitvoerings geschiedenis weer geven voor een bepaalde pijplijn uitvoering.

![Geschiedenis voor een pijplijn uitvoering weer geven](media/monitor-visually/view-rerun-history.png)

## <a name="monitor-consumption"></a>Verbruik bewaken

U kunt de resources die worden gebruikt door een pijplijn uitvoering bekijken door te klikken op het verbruiks pictogram naast de uitvoering. 

![Scherm afbeelding die laat zien waar u de resources ziet die door een pijp lijn worden verbruikt.](media/monitor-visually/monitor-consumption-1.png)

Als u op het pictogram klikt, wordt een verbruiks rapport geopend met de resources die worden gebruikt door die pijplijn uitvoering. 

![Verbruik bewaken](media/monitor-visually/monitor-consumption-2.png)

U kunt deze waarden in de [Azure-prijs calculator](https://azure.microsoft.com/pricing/details/data-factory/) aansluiten om de kosten van de pijplijn uitvoering te schatten. Zie [prijs](pricing-concepts.md)informatie voor meer informatie over Azure Data Factory prijzen.

> [!NOTE]
> Deze waarden die worden geretourneerd door de prijs calculator is een schatting. Dit geeft niet het exacte bedrag weer dat wordt gefactureerd door Azure Data Factory 

## <a name="gantt-views"></a>Gantt-weer gaven

Een Gantt-diagram is een weer gave waarmee u de uitvoerings geschiedenis over een tijds bereik kunt zien. Als u overschakelt naar een Gantt-weer gave, ziet u alle pijplijn uitvoeringen gegroepeerd op naam die wordt weer gegeven als balken ten opzichte van hoe lang de uitvoering heeft geduurd. U kunt ook groeperen op aantekeningen/Tags die u hebt gemaakt in de pijp lijn. De Gantt-weer gave is ook beschikbaar op het niveau van de uitvoering van de activiteit.

![Voor beeld van een Gantt-diagram](media/monitor-visually/select-gantt.png)

De lengte van de balk informeert de duur van de pijp lijn. U kunt ook de balk selecteren om meer details weer te geven.

![Duur Gantt-diagram](media/monitor-visually/view-gantt-run.png)

## <a name="alerts"></a>Waarschuwingen

U kunt waarschuwingen activeren over ondersteunde metrische gegevens in Data Factory. Selecteer **bewakings**  >  **waarschuwingen & metrische gegevens** op de pagina Data Factory controle om aan de slag te gaan.

![Pagina Data Factory-monitor](media/monitor-visually/start-page.png)

Bekijk de volgende video voor een inleiding en demonstratie van zeven minuten voor deze functie:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Monitor-your-Azure-Data-Factory-pipelines-proactively-with-alerts/player]

### <a name="create-alerts"></a>Waarschuwingen maken

1.  Selecteer **nieuwe waarschuwings regel** om een nieuwe waarschuwing te maken.

    ![Knop nieuwe waarschuwings regel](media/monitor-visually/new-alerts.png)

1.  Geef de naam van de regel op en selecteer de ernst van de waarschuwing.

    ![Vakken voor naam en ernst van de regel](media/monitor-visually/name-and-severity.png)

1.  Selecteer de waarschuwings criteria.

    ![Vak voor doel criteria](media/monitor-visually/add-criteria-1.png)

    ![Scherm afbeelding die laat zien waar u één metriek selecteert om de waarschuwings voorwaarde in te stellen.](media/monitor-visually/add-criteria-2.png)

    ![Lijst met criteria](media/monitor-visually/add-criteria-3.png)

    U kunt waarschuwingen maken voor diverse metrische gegevens, waaronder die voor de ADF-entiteit Count/grootte, activiteit/pijp lijn/trigger uitvoeringen, Integration Runtime (IR) CPU-gebruik/geheugen/knooppunt telling/wachtrij, evenals voor de uitvoeringen van SSIS-pakketten en SSIS-begin-en stop bewerkingen voor de IR.

1.  De waarschuwings logica configureren. U kunt een waarschuwing maken voor de geselecteerde metrische gegevens voor alle pijp lijnen en bijbehorende activiteiten. U kunt ook een bepaald activiteitstype, de naam van de activiteit, de naam van de pijp lijn of het type fout selecteren.

    ![Opties voor het configureren van waarschuwings logica](media/monitor-visually/alert-logic.png)

1.  E-mail-, SMS-, push-en voicemail meldingen configureren voor de waarschuwing. Een actie groep maken of een bestaande kiezen, voor de waarschuwings meldingen.

    ![Opties voor het configureren van meldingen](media/monitor-visually/configure-notification-1.png)

    ![Opties voor het toevoegen van een melding](media/monitor-visually/configure-notification-2.png)

1.  De waarschuwings regel maken.

    ![Opties voor het maken van een waarschuwings regel](media/monitor-visually/create-alert-rule.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het bewaken en beheren van pijp lijnen het [programmatische artikel pijp lijnen bewaken en beheren](./monitor-programmatically.md) .