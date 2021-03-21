---
title: Fouten opsporen in terugkerende taken in Azure Data Lake Analytics
description: Meer informatie over het gebruik van Azure Data Lake-Hulpprogram Ma's voor Visual Studio voor het opsporen van fouten in een abnormale terugkerende taak.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 05/20/2018
ms.openlocfilehash: b95d4b0a082e8eac191d610c9516e5ce72180b4c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92219170"
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>Problemen met een abnormale terugkerende taak oplossen

In dit artikel wordt beschreven hoe u [Azure data Lake-Hulpprogram ma's voor Visual Studio](https://aka.ms/adltoolsvs) kunt gebruiken om problemen met terugkerende taken op te lossen. Meer informatie over pijp lijn en terugkerende taken van de [Azure data Lake en Azure HDInsight-blog](/archive/blogs/azuredatalake/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy).

Terugkerende taken delen meestal dezelfde query logica en vergelijk bare invoer gegevens. Stel bijvoorbeeld dat u een terugkerende taak hebt die elke maandag ochtend om 8:00 uur wordt uitgevoerd. de wekelijkse actieve gebruiker van de afgelopen week tellen. De scripts voor deze taken delen één script sjabloon die de query logica bevat. De invoer voor deze taken zijn de gebruiks gegevens voor de afgelopen week. Het delen van dezelfde query logica en soort gelijke invoer betekent meestal dat de prestaties van deze taken vergelijkbaar en stabiel zijn. Als een van uw terugkerende taken plotseling abnormaal werkt, mislukt of een hoop vertraging oploopt, wilt u mogelijk het volgende doen:

- Raadpleeg de statistieken rapporten voor de vorige uitvoeringen van de terugkerende taak om te zien wat er is gebeurd.
- Vergelijk de abnormale taak met een normaal item om erachter te gaan wat er is gewijzigd.

Met de **weer gave gerelateerde taak** in azure data Lake-hulpprogram Ma's voor Visual Studio kunt u de voortgang van het oplossen van problemen met beide gevallen versnellen.

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>Stap 1: terugkerende taken zoeken en gerelateerde taak weergave openen

Als u de gerelateerde taak weergave wilt gebruiken om een probleem met een terugkerende taak op te lossen, moet u eerst de terugkerende taak in Visual Studio vinden en vervolgens gerelateerde taak weergave openen.

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>Voor beeld 1: u hebt de URL voor de terugkerende taak

Via **hulpprogram ma's**  >  **Data Lake**  >  **taak weergave** kunt u de taak-URL voor het openen van de taak weergave in Visual Studio plakken. Selecteer **gerelateerde taken weer geven** om de gerelateerde taak weergave te openen.

![Koppeling Verwante taken weer geven in Data Lake Analytics-Hulpprogram Ma's](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>Voor beeld 2: u hebt de pijp lijn voor de terugkerende taak, maar niet de URL

In Visual Studio kunt u de pipeline-browser openen via Server Explorer > uw Azure Data Lake Analytics-account > **pijp lijnen**. (Als u dit knoop punt niet kunt vinden in Server Explorer, kunt u [de nieuwste invoeg toepassing downloaden](https://aka.ms/adltoolsvs).) 

![Het knoop punt pijp lijnen selecteren](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

In de pipeline-browser worden alle pijp lijnen voor het Data Lake Analytics-account links weer gegeven. U kunt de pijp lijnen uitvouwen om alle terugkerende taken te vinden. vervolgens selecteert u het item dat problemen heeft. Gerelateerde taak weergave wordt aan de rechter kant geopend.

![Een pijp lijn selecteren en gerelateerde taak weergave openen](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>Stap 2: een statistieken rapport analyseren

Boven aan de gerelateerde taak weergave wordt een samen vatting en statistieken rapport weer gegeven. Daar kunt u de mogelijke hoofd oorzaak van het probleem vinden. 

1.  In het rapport geeft de X-as de tijd voor het verzenden van taken weer. Gebruik het om de abnormale taak te vinden.
2.  Gebruik het proces in het volgende diagram om statistieken te controleren en inzicht te krijgen in het probleem en de mogelijke oplossingen.

![Proces diagram voor het controleren van statistieken](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>Stap 3: de abnormale taak vergelijken met een normale taak

U kunt alle verzonden terugkerende taken vinden via de taken lijst onder aan gerelateerde taak weergave. Als u meer inzichten en mogelijke oplossingen wilt vinden, klikt u met de rechter muisknop op de abnormale taak. Gebruik de weer gave voor de taak vergelijking om de abnormale taak te vergelijken met een eerder gebruikelijk.

![Snelmenu voor het vergelijken van taken](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

Let op de grote verschillen tussen deze twee taken. Deze verschillen veroorzaken waarschijnlijk de prestatie problemen. Als u verder wilt controleren, gebruikt u de stappen in het volgende diagram:

![Proces diagram voor het controleren van verschillen tussen taken](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>Volgende stappen

* [Problemen met gegevens scheefheid oplossen](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [Fout opsporing voor door de gebruiker gedefinieerde C#-code voor mislukte U-SQL-taken](data-lake-analytics-debug-u-sql-jobs.md)