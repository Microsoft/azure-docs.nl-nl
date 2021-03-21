---
title: Fout opsporings modus gegevens stroom toewijzen
description: Een interactieve sessie voor fout opsporing starten bij het bouwen van gegevens stromen
ms.author: makromer
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/19/2021
ms.openlocfilehash: 0aa472aca40acbaf3f8c8a09469d08fe6b37187a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699756"
---
# <a name="mapping-data-flow-debug-mode"></a>Fout opsporings modus gegevens stroom toewijzen

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Overzicht

Met de modus fout opsporing van gegevens stroom van Azure Data Factory-toewijzing kunt u de transformatie van de gegevensshape interactief bekijken terwijl u uw gegevens stromen bouwt en oplost. De foutopsporingssessie kan worden gebruikt in combi natie met data flow-ontwerp sessies en tijdens het opsporen van fouten in de pipeline voor het uitvoeren van gegevens stromen. Als u de foutopsporingsmodus wilt inschakelen, gebruikt u de knop ' fout opsporing voor gegevens stromen ' boven aan het ontwerp oppervlak.

![Schuif regelaar voor fout opsporing](media/data-flow/debugbutton.png "Schuif regelaar voor fout opsporing")

Zodra u de schuif regelaar hebt ingeschakeld, wordt u gevraagd om te selecteren welke configuratie voor de integratie-runtime u wilt gebruiken. Als AutoResolveIntegrationRuntime is gekozen, wordt een cluster met acht kernen van algemene berekeningen met een standaard tijd van 60 minuten live. Als u een langer inactief team wilt toestaan voordat er een time-out optreedt voor uw sessie, kunt u een hogere TTL-instelling kiezen. Zie [prestaties van gegevens stroom](concepts-data-flow-performance.md#ir)voor meer informatie over data flow Integration-Runtimes.

![Fout opsporing IR-selectie](media/data-flow/debug-new-1.png "Fout opsporing IR-selectie")

Wanneer de foutopsporingsmodus is ingeschakeld, bouwt u interactief uw gegevens stroom met een actief Spark-cluster. De sessie wordt gesloten zodra u debug uitschakelt in Azure Data Factory. U moet rekening houden met de kosten per uur die zijn gemaakt door Azure Databricks gedurende de periode dat de foutopsporingssessie is ingeschakeld.

In de meeste gevallen is het een goed idee om uw gegevens stromen te bouwen in de foutopsporingsmodus, zodat u uw bedrijfs logica kunt valideren en uw gegevens transformaties weer geven voordat u uw werk in Azure Data Factory publiceert. Gebruik de knop ' fout opsporing ' in het deel venster pijplijn om uw gegevens stroom in een pijp lijn te testen.

![Fout opsporingsgegevens voor gegevens stromen weer geven](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

> [!NOTE]
> Elke debug-sessie die een gebruiker start vanuit de gebruikers interface van de ADF-browser, is een nieuwe sessie met een eigen Spark-cluster. U kunt de weer gave controle voor debug-sessies hierboven gebruiken voor het weer geven en beheren van debug-sessies per Factory. Er worden kosten in rekening gebracht voor elk uur dat elke foutopsporingssessie wordt uitgevoerd, inclusief de TTL-tijd.

## <a name="cluster-status"></a>De clusterstatus

De cluster status indicator boven aan het ontwerp oppervlak wordt groen wanneer het cluster gereed is voor fout opsporing. Als uw cluster al warm is, wordt de groene indicator bijna onmiddellijk weer gegeven. Als uw cluster niet al wordt uitgevoerd op het moment dat u de foutopsporingsmodus hebt geactiveerd, wordt het in het Spark-cluster koud opgestart. De indicator wordt gedraaid tot de omgeving gereed is voor interactieve fout opsporing.

Wanneer u klaar bent met de fout opsporing, schakelt u de schakel optie voor fout opsporing uit zodat uw Spark-cluster kan worden beëindigd en worden er geen kosten in rekening gebracht voor debug-activiteiten.

## <a name="debug-settings"></a>Instellingen voor fout opsporing

Wanneer u de foutopsporingsmodus inschakelt, kunt u de weer gave van gegevens in een gegevens stroom bewerken. Instellingen voor fout opsporing kunnen worden bewerkt door te klikken op instellingen voor fout opsporing op de werk balk gegevensstroom canvas. U kunt de rijlimiet of de bestands bron selecteren die u voor elk van uw bron transformaties wilt gebruiken. De limieten voor rijen in deze instelling gelden alleen voor de huidige foutopsporingssessie. U kunt ook de gekoppelde staging-service selecteren die moet worden gebruikt voor een Azure Synapse Analytics-bron. 

![Instellingen voor fout opsporing](media/data-flow/debug-settings.png "Instellingen voor fout opsporing")

Als u para meters hebt in uw gegevens stroom of een van de data sets waarnaar wordt verwezen, kunt u opgeven welke waarden moeten worden gebruikt tijdens de fout opsporing door het tabblad **para meters** te selecteren.

Gebruik de bemonsterings instellingen hier om te verwijzen naar voorbeeld bestanden of voorbeeld tabellen met gegevens, zodat u de bron gegevens sets niet hoeft te wijzigen. Met behulp van een voorbeeld bestand of-tabel kunt u de instellingen voor de logica en eigenschappen in uw gegevens stroom behouden tijdens het testen op basis van een subset van gegevens.

![Para meters voor fout opsporing](media/data-flow/debug-settings2.png "Para meters voor fout opsporing")

De standaard-IR die wordt gebruikt voor de foutopsporingsmodus in ADF-gegevens stromen is een klein 4-core single worker-knoop punt met een 4-core knoop punt met één stuur programma. Dit werkt prima met kleinere voor beelden van gegevens bij het testen van de logica van uw gegevens stroom. Als u de limieten voor rijen in de instellingen voor fout opsporing uitvouwt tijdens de preview van gegevens of als u een groter aantal voorbeeld rijen in uw bron hebt ingesteld tijdens de fout opsporing voor de pijp lijn, kunt u overwegen om een grotere reken omgeving in te stellen in een nieuwe Azure Integration Runtime. Vervolgens kunt u de foutopsporingssessie opnieuw starten met behulp van de grotere Compute-omgeving.

## <a name="data-preview"></a>Voorbeeld van gegevens

Met fout opsporing in wordt het tabblad voor beeld van gegevens lichter op het onderste paneel. Zonder de foutopsporingsmodus in, worden in de gegevens stroom alleen de huidige meta gegevens in en uit elk van de trans formaties op het tabblad inspectie weer gegeven. In de preview van de gegevens wordt alleen het aantal rijen weer gegeven dat u hebt ingesteld als uw limiet voor de instellingen voor fout opsporing. Klik op **vernieuwen** om de voorbeeld gegevens op te halen.

![Voorbeeld van gegevens](media/data-flow/datapreview.png "Voorbeeld van gegevens")

> [!NOTE]
> Bestands bronnen beperken alleen de rijen die u ziet, niet de rijen die worden gelezen. Voor zeer grote gegevens sets wordt u aangeraden een klein deel van het bestand te maken en dit te gebruiken voor uw test doeleinden. U kunt een tijdelijk bestand selecteren in instellingen voor fout opsporing voor elke bron die een bestands gegevensset type is.

Wanneer de foutopsporingsmodus in de gegevens stroom wordt uitgevoerd, worden uw gegevens niet naar de Sink-trans formatie geschreven. Een foutopsporingssessie is bedoeld om te fungeren als een test harnas voor uw trans formaties. Sinks zijn niet vereist tijdens fout opsporing en worden genegeerd in uw gegevens stroom. Als u het schrijven van de gegevens in uw Sink wilt testen, voert u de gegevens stroom uit vanuit een Azure Data Factory pijp lijn en gebruikt u de uitvoering van de fout opsporing vanuit een pijp lijn.

Preview van gegevens is een moment opname van uw getransformeerde gegevens met behulp van Rijlimiet en gegevens bemonstering van gegevens frames in Spark-geheugen. Daarom kunnen de Sink-Stuur Programma's niet worden gebruikt of getest in dit scenario.

### <a name="testing-join-conditions"></a>Voor waarden voor samen voegen testen

Wanneer eenheids testen samen voegen, bestaan of trans formaties opzoeken, moet u ervoor zorgen dat u een kleine set bekende gegevens gebruikt voor uw test. U kunt de optie instellingen voor fout opsporing hierboven gebruiken om een tijdelijk bestand in te stellen dat moet worden gebruikt voor het testen. Dit is nodig omdat bij het beperken of bemonsteren van rijen uit een grote gegevensset u niet kunt voors pellen welke rijen en welke sleutels in de stroom worden gelezen om te testen. Het resultaat is niet-deterministisch, wat betekent dat uw samenvoegings voorwaarden kunnen mislukken.

### <a name="quick-actions"></a>Snelle acties

Zodra u het voor beeld van de gegevens ziet, kunt u een snelle trans formatie genereren om een kolom te typecast, te verwijderen of te wijzigen. Klik op de kolomkop en selecteer een van de opties op de werk balk data preview.

![In de scherm afbeelding wordt de werk balk data preview weer gegeven met opties: typecast, Modify, Statistics en Remove.](media/data-flow/quick-actions1.png "Snelle acties")

Wanneer u een wijziging hebt geselecteerd, wordt de preview van de gegevens direct vernieuwd. Klik op **bevestigen** in de rechter bovenhoek om een nieuwe trans formatie te genereren.

![Scherm afbeelding toont de knop bevestigen.](media/data-flow/quick-actions2.png "Snelle acties")

Met **typecast** en **Modify** wordt een afgeleide kolom transformatie **gegenereerd en wordt** er een selectie transformatie gegenereerd.

![De scherm opname toont de instellingen van de afgeleide kolom.](media/data-flow/quick-actions3.png "Snelle acties")

> [!NOTE]
> Als u de gegevens stroom bewerkt, moet u de voor beeld van de gegevens opnieuw ophalen voordat u een snelle trans formatie toevoegt.

### <a name="data-profiling"></a>Gegevens profilering

Als u een kolom selecteert op het tabblad voor beeld van gegevens en op **Statistieken** klikt in de werk balk voor data-preview, wordt in de rechter kant van het gegevens raster een grafiek weer gegeven met gedetailleerde statistieken over elk veld. Azure Data Factory maakt een bepaling op basis van de gegevens bemonstering van welk type grafiek moet worden weer gegeven. Velden met hoge kardinaliteit worden standaard ingesteld op NULL/niet-NULL-grafieken terwijl categorische en numerieke gegevens met lage kardinaliteit balk diagrammen weer geven met een frequentie van gegevens waarden. U ziet ook de Max/len lengte van teken reeks velden, min/max-waarden in numerieke velden, standaard dev, percentielen, tellingen en gemiddelde.

![Kolom statistieken](media/data-flow/stats.png "Kolom statistieken")

## <a name="next-steps"></a>Volgende stappen

* Wanneer u klaar bent met het maken en opsporen van fouten in uw gegevens stroom, [voert u deze uit vanuit een pijp lijn.](control-flow-execute-data-flow-activity.md)
* Wanneer u uw pijp lijn test met een gegevens stroom, gebruikt u de optie voor het uitvoeren van de pipeline- [fout opsporing.](iterative-development-debugging.md)
