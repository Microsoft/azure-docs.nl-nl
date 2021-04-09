---
title: Iteratieve ontwikkeling en fout opsporing in Azure Data Factory
description: Meer informatie over het ontwikkelen en fouten opsporen Data Factory pijp lijnen iteratief in de ADF UX
ms.date: 02/23/2021
ms.topic: conceptual
ms.service: data-factory
services: data-factory
documentationcenter: ''
ms.workload: data-services
author: kromerm
ms.author: makromer
ms.openlocfilehash: ef47d311f5f096db962ea27792e7871dbf0ef81a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101712943"
---
# <a name="iterative-development-and-debugging-with-azure-data-factory"></a>Iteratief ontwikkelen en fouten opsporen met Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met Azure Data Factory kunt u Data Factory pijp lijnen iteratief ontwikkelen en fouten opsporen tijdens het ontwikkelen van uw oplossingen voor gegevens integratie. Met deze functies kunt u uw wijzigingen testen voordat u een pull-aanvraag maakt of deze naar de data factory-service publiceert. 

Bekijk de volgende video voor een inleiding en demonstratie van acht minuten voor deze functie:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Iterative-development-and-debugging-with-Azure-Data-Factory/player]

## <a name="debugging-a-pipeline"></a>Fouten opsporen in een pijplijn

Wanneer u een ontwerpt met behulp van het pijp lijn-canvas, kunt u uw activiteiten testen met de functie voor **fout opsporing** . Wanneer u test uitvoeringen uitvoert, hoeft u uw wijzigingen niet te publiceren naar de data factory voordat u **debug** selecteert. Deze functie is handig in scenario's waarin u er zeker van wilt zijn dat de wijzigingen werken zoals verwacht voordat u de data factory werk stroom bijwerkt.

![Mogelijkheid tot fouten opsporen op het pijplijn papier](media/iterative-development-debugging/iterative-development-1.png)

Wanneer de pijp lijn wordt uitgevoerd, kunt u de resultaten van elke activiteit bekijken op het tabblad **uitvoer** van het pijp lijn-canvas.

Bekijk de resultaten van uw test uitvoeringen in het **uitvoer** venster van het pijp lijn papier.

![Uitvoer venster van het pijp lijn papier](media/iterative-development-debugging/iterative-development-2.png)

Wanneer een test uitvoering slaagt, voegt u meer activiteiten aan uw pijp lijn toe en voert u de fout opsporing op een iteratieve manier door. U kunt ook een test uitvoering **Annuleren** terwijl deze wordt uitgevoerd.

> [!IMPORTANT]
> Als u **debug** selecteert, wordt de pijp lijn werkelijk uitgevoerd. Als de pijp lijn bijvoorbeeld Kopieer activiteit bevat, kopieert de test uitvoering de gegevens van de bron naar het doel. Als gevolg hiervan raden wij u aan test mappen in uw Kopieer activiteiten en andere activiteiten te gebruiken bij het opsporen van fouten. Nadat u de fout opsporing voor de pijp lijn hebt uitgevoerd, gaat u naar de daad werkelijke mappen die u wilt gebruiken voor normale bewerkingen.

### <a name="setting-breakpoints"></a>Onderbrekings punten instellen

Met Azure Data Factory kunt u fouten opsporen in een pijp lijn totdat u een bepaalde activiteit op het pijp lijn teken hebt bereikt. Plaats een onderbrekings punt op de activiteit tot u wilt testen en selecteer **fout opsporing**. Data Factory zorgt ervoor dat de test alleen wordt uitgevoerd tot de activiteit onderbrekings punt op het pijp lijn-canvas. Deze functie voor het *opsporen van fouten* is handig wanneer u niet de volledige pijp lijn wilt testen, maar alleen een subset van activiteiten in de pijp lijn.

![Onderbrekings punten op het pijp lijn papier](media/iterative-development-debugging/iterative-development-3.png)

Selecteer een element op het pijplijn doek om een onderbrekings punt in te stellen. De optie *debug until* wordt weer gegeven als een lege rode cirkel in de rechter bovenhoek van het element.

![Voordat een onderbrekings punt voor het geselecteerde element wordt ingesteld](media/iterative-development-debugging/iterative-development-4.png)

Nadat u de optie *debug until* hebt geselecteerd, verandert deze in een gevulde rode cirkel om aan te geven dat het onderbrekings punt is ingeschakeld.

![Na het instellen van een onderbrekings punt voor het geselecteerde element](media/iterative-development-debugging/iterative-development-5.png)

## <a name="monitoring-debug-runs"></a>Fout opsporing van bewaking wordt uitgevoerd

Wanneer u de uitvoering van een pijplijn fout opsporing uitvoert, worden de resultaten weer gegeven in het **uitvoer** venster van het pijplijn doek. Het tabblad uitvoer bevat alleen de meest recente uitvoering die tijdens de huidige browser sessie is opgetreden. 

![Uitvoer venster van het pijp lijn papier](media/iterative-development-debugging/iterative-development-2.png)

Als u een historisch overzicht wilt bekijken van de uitvoeringen van fout opsporing of als u een lijst met alle actieve debug-uitvoeringen wilt zien, kunt u naar de **monitor** ervaring gaan. 

![Selecteer het pictogram actieve debug-uitvoeringen weer geven](media/iterative-development-debugging/view-debug-runs.png)

> [!NOTE]
> De Azure Data Factory-service blijft de geschiedenis van debug-uitvoering gedurende 15 dagen behouden. 

## <a name="debugging-mapping-data-flows"></a>Fout opsporing van gegevens stromen toewijzen

Met toewijzing van gegevens stromen kunt u een code maken voor de vrije gegevens transformatie die op schaal wordt uitgevoerd. Wanneer u uw logica bouwt, kunt u een foutopsporingssessie inschakelen om interactief met uw gegevens te werken met behulp van een live Spark-cluster. Lees voor meer informatie over het [toewijzen van de fout opsporings modus voor gegevens stromen](concepts-data-flow-debug-mode.md).

U kunt fouten bij het opsporen van actieve gegevens stromen in een fabriek controleren in de **monitor** ervaring.

![Fout opsporingsgegevens voor gegevens stromen weer geven](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

De voorbeeld weergave van gegevens in de ontwerp functie voor gegevens stromen en het opsporen van pijp lijnen voor gegevens stromen is bedoeld om te werken met kleine voor beelden van gegevens. Als u echter uw logica in een pijp lijn of gegevens stroom wilt testen met grote hoeveel heden gegevens, verg root u de Azure Integration Runtime die worden gebruikt in de foutopsporingssessie met meer kernen en een minimale reken kracht voor algemeen gebruik.
 
### <a name="debugging-a-pipeline-with-a-data-flow-activity"></a>Fouten opsporen in een pijp lijn met een gegevens stroom activiteit

Bij het uitvoeren van een pijp lijn voor fout opsporing met een gegevens stroom hebt u twee opties voor het gebruik van compute. U kunt een bestaand cluster voor fout opsporing gebruiken of een nieuw just-in-time-cluster maken voor uw gegevens stromen.

Als u een bestaande foutopsporingssessie gebruikt, wordt de start tijd van de gegevens stroom aanzienlijk verminderd omdat het cluster al actief is, maar niet wordt aanbevolen voor complexe of parallelle werk belastingen, omdat het niet mogelijk is om meerdere taken tegelijk uit te voeren.

Met de activity runtime wordt een nieuw cluster gemaakt op basis van de instellingen die zijn opgegeven in de Integration runtime van elke gegevens stroom activiteit. Hierdoor kan elke taak worden geïsoleerd en moet deze worden gebruikt voor complexe werk belastingen of prestatie testen. U kunt ook de TTL beheren in de Azure IR zodat de cluster bronnen die voor fout opsporing worden gebruikt, nog steeds beschikbaar zijn voor die periode om extra taak aanvragen te kunnen uitvoeren.

> [!NOTE]
> Als u een pijp lijn hebt met gegevens stromen die worden uitgevoerd in parallelle of gegevens stromen die moeten worden getest met grote gegevens sets, kiest u activiteit runtime gebruiken zodat Data Factory de Integration Runtime kunt gebruiken die u hebt geselecteerd in de gegevens stroom activiteit. Hierdoor kunnen de gegevens stromen op meerdere clusters worden uitgevoerd en kan de parallelle gegevens stroom worden uitgevoerd.

![Een pijp lijn uitvoeren met een gegevensstroom](media/iterative-development-debugging/iterative-development-dataflow.png)

## <a name="next-steps"></a>Volgende stappen

Nadat u uw wijzigingen hebt getest, kunt u ze promo veren tot hogere omgevingen met behulp [van continue integratie en implementatie in azure Data Factory](continuous-integration-deployment.md).
