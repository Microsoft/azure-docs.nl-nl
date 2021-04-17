---
title: Foutopsporingsmodus voor toewijzingsgegevensstroom
description: Een interactieve foutopsporingssessie starten bij het bouwen van gegevensstromen
ms.author: makromer
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.openlocfilehash: ac0e088c587132b32f2112d21bce936eac35dc72
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515860"
---
# <a name="mapping-data-flow-debug-mode"></a>Foutopsporingsmodus voor toewijzingsgegevensstroom

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Overzicht

Azure Data Factory de foutopsporingsmodus van de toewijzingsgegevensstroom kunt u interactief de transformatie van de gegevensvorm bekijken terwijl u uw gegevensstromen bouwt en er fouten in opsport. De foutopsporingssessie kan worden gebruikt in Gegevensstroom ontwerpsessies en tijdens het uitvoeren van pijplijnbuguitvoering van gegevensstromen. Als u de foutopsporingsmodus wilt inschakelen, gebruikt u **Gegevensstroom knop Fouten** opsporen in de bovenste balk van het canvas of pijplijn-canvas wanneer u gegevensstroomactiviteiten hebt.

![Schuifregelaar voor foutopsporing 1](media/data-flow/debugbutton.png "Schuifregelaar voor foutopsporing")

![Schuifregelaar voor foutopsporing 2](media/data-flow/debug-button-4.png "Schuifregelaar voor foutopsporing")

Wanneer u de schuifregelaar in hebt gezet, wordt u gevraagd om te selecteren welke integratieruntimeconfiguratie u wilt gebruiken. Als AutoResolveIntegrationRuntime wordt gekozen, wordt een cluster met acht kernen van algemene rekenkracht met een standaard time to live van 60 minuten geactiveerd. Als u meer inactieve teams wilt toestaan voordat er een times-out voor de sessie wordt uitgevoerd, kunt u een hogere TTL-instelling kiezen. Zie Prestaties van gegevensstroom voor meer informatie over runtimes [voor gegevensstroomintegratie.](concepts-data-flow-performance.md#ir)

![IR-selectie voor foutopsporing](media/data-flow/debug-new-1.png "IR-selectie voor foutopsporing")

Wanneer de foutopsporingsmodus is aan, bouwt u uw gegevensstroom interactief met een actief Spark-cluster. De sessie wordt gesloten nadat u foutopsporing hebt uitgeschakeld in Azure Data Factory. U moet rekening houden met de kosten per uur die worden gemaakt door Azure Databricks de tijd dat u de foutopsporingssessie hebt ingeschakeld.

In de meeste gevallen is het een goed idee om uw gegevensstromen te bouwen in de foutopsporingsmodus, zodat u uw bedrijfslogica kunt valideren en uw gegevenstransformaties kunt bekijken voordat u uw werk in de Azure Data Factory. Gebruik de knop Fouten opsporen in het pijplijnpaneel om uw gegevensstroom in een pijplijn te testen.

![Foutopsporingssessies voor gegevensstromen weergeven](media/iterative-development-debugging/view-dataflow-debug-sessions.png)

> [!NOTE]
> Elke foutopsporingssessie die een gebruiker start vanuit de ADF-browsergebruikersinterface is een nieuwe sessie met een eigen Spark-cluster. U kunt de bewakingsweergave voor foutopsporingssessies hierboven gebruiken om foutopsporingssessies per factory weer te geven en te beheren. Er worden kosten in rekening gebracht voor elk uur dat elke foutopsporingssessie wordt uitgevoerd, inclusief de TTL-tijd.

## <a name="cluster-status&quot;></a>De clusterstatus

De clusterstatusindicator bovenaan het ontwerpoppervlak wordt groen wanneer het cluster gereed is voor foutopsporing. Als uw cluster al warm is, wordt de groene indicator bijna direct weergegeven. Als uw cluster nog niet werd uitgevoerd toen u de foutopsporingsmodus in ging, voert het Spark-cluster een koude opstart uit. De indicator draait totdat de omgeving gereed is voor interactieve debugging.

Wanneer u klaar bent met de foutopsporing, schakelt u de schakelknop Foutopsporing uit, zodat uw Spark-cluster kan worden beëindigd en u niet meer wordt gefactureerd voor foutopsporingsactiviteiten.

## <a name=&quot;debug-settings&quot;></a>Instellingen voor foutopsporing

Zodra u de foutopsporingsmodus hebt inschakelen, kunt u bewerken hoe een gegevensstroom een voorbeeld van gegevens bekijkt. Foutopsporingsinstellingen kunnen worden bewerkt door te klikken op Instellingen voor foutopsporing op de werkbalk Gegevensstroom canvas. U kunt hier de rijlimiet of bestandsbron selecteren die u wilt gebruiken voor elk van de brontransformaties. De rijlimieten in deze instelling zijn alleen voor de huidige foutopsporingssessie. U kunt ook de gekoppelde faseringsservice selecteren die moet worden gebruikt voor een Azure Synapse Analytics bron. 

![Instellingen voor foutopsporing](media/data-flow/debug-settings.png &quot;Foutopsporingsinstellingen")

Als u parameters in uw Gegevensstroom of een van de gegevenssets waarnaar wordt verwezen, kunt u opgeven welke waarden moeten worden gebruikt tijdens debuggen door het tabblad **Parameters te** selecteren.

Gebruik de steekproefinstellingen hier om te wijzen naar voorbeeldbestanden of voorbeeldtabellen met gegevens, zodat u uw brongegevenssets niet hoeft te wijzigen. Door hier een voorbeeldbestand of tabel te gebruiken, kunt u dezelfde logica en eigenschapsinstellingen in uw gegevensstroom onderhouden tijdens het testen op een subset van gegevens.

![Parameters voor foutopsporingsinstellingen](media/data-flow/debug-settings2.png "Parameters voor foutopsporingsinstellingen")

De standaard-IR die wordt gebruikt voor de foutopsporingsmodus in ADF-gegevensstromen is een klein 4-core knooppunt met één werkstroom met een 4-core knooppunt met één stuurprogramma. Dit werkt prima met kleinere gegevensvoorbeelden bij het testen van uw gegevensstroomlogica. Als u de rijlimieten in uw foutopsporingsinstellingen uitbreidt tijdens het voorbeeld van gegevens of als u een groter aantal rijen in de bron in de bron in de steekproef instelt tijdens het opsporen van pijplijnopsporing, kunt u overwegen om een grotere rekenomgeving in te stellen in een nieuwe Azure Integration Runtime. Vervolgens kunt u de foutopsporingssessie opnieuw starten met behulp van de grotere rekenomgeving.

## <a name="data-preview"></a>Voorbeeld van gegevens

Als foutopsporing is geselecteerd, wordt het tabblad Gegevensvoorbeeld weergegeven in het onderste deelvenster. Als de foutopsporingsmodus niet is Gegevensstroom u alleen de huidige metagegevens in en uit elk van uw transformaties op het tabblad Inspecteren weergegeven. In het voorbeeld van gegevens wordt alleen een query uitgevoerd op het aantal rijen dat u hebt ingesteld als uw limiet in uw foutopsporingsinstellingen. Klik **op Vernieuwen om** de voorbeeldgegevens op te halen.

![Voorbeeld van gegevens](media/data-flow/datapreview.png "Voorbeeld van gegevens")

> [!NOTE]
> Bestandsbronnen beperken alleen de rijen die u ziet, niet de rijen die worden gelezen. Voor zeer grote gegevenssets is het raadzaam om een klein deel van dat bestand te gebruiken voor uw tests. U kunt een tijdelijk bestand selecteren in Instellingen voor foutopsporing voor elke bron die een type bestandsset is.

Wanneer u in de foutopsporingsmodus in Gegevensstroom, worden uw gegevens niet naar de Sink-transformatie geschreven. Een foutopsporingssessie is bedoeld als een testharnas voor uw transformaties. Sinks zijn niet vereist tijdens het opsporen van fouten en worden genegeerd in uw gegevensstroom. Als u het schrijven van de gegevens in uw sink wilt testen, voert u de Gegevensstroom uit vanuit een Azure Data Factory-pijplijn en gebruikt u de foutopsporingsuitvoering vanuit een pijplijn.

Data Preview is een momentopname van uw getransformeerde gegevens met behulp van rijlimieten en steekproeven van gegevens uit gegevensframes in het Spark-geheugen. Daarom worden de sink-stuurprogramma's niet gebruikt of getest in dit scenario.

### <a name="testing-join-conditions"></a>Joinvoorwaarden testen

Wanneer u de transformaties Joins, Exists of Lookup test, moet u een kleine set bekende gegevens gebruiken voor uw test. U kunt de bovenstaande optie Foutopsporingsinstellingen gebruiken om een tijdelijk bestand in te stellen voor uw tests. Dit is nodig omdat u bij het beperken of nemen van steekproeven van rijen uit een grote gegevensset niet kunt voorspellen welke rijen en welke sleutels in de stroom worden gelezen om te worden getest. Het resultaat is niet-deterministisch, wat betekent dat uw joinvoorwaarden kunnen mislukken.

### <a name="quick-actions"></a>Snelle acties

Zodra u het voorbeeld van de gegevens ziet, kunt u een snelle transformatie genereren voor het typecasten, verwijderen of wijzigen van een kolom. Klik op de kolomkop en selecteer vervolgens een van de opties in de werkbalk van het gegevensvoorbeeld.

![Schermopname van de werkbalk van het gegevensvoorbeeld met opties: Typecast, Wijzigen, Statistieken en Verwijderen.](media/data-flow/quick-actions1.png "Snelle acties")

Wanneer u een wijziging selecteert, wordt het gegevensvoorbeeld onmiddellijk vernieuwd. Klik **op Bevestigen** in de rechterbovenhoek om een nieuwe transformatie te genereren.

![Schermopname van de knop Bevestigen.](media/data-flow/quick-actions2.png "Snelle acties")

**Met Typecast** en **Modify** wordt een afgeleide kolomtransformatie gegenereerd en **met Verwijderen** wordt een Select-transformatie gegenereerd.

![Schermopname van de Instellingen van afgeleide kolom.](media/data-flow/quick-actions3.png "Snelle acties")

> [!NOTE]
> Als u uw Gegevensstroom, moet u de voorbeeldgegevens opnieuw ophalen voordat u een snelle transformatie toevoegt.

### <a name="data-profiling"></a>Gegevensprofilering

Als u een kolom selecteert op het tabblad Gegevensvoorbeeld en klikt op Statistieken **in** de werkbalk voor gegevensvoorbeelden, wordt een grafiek rechts van uw gegevensraster weergegeven met gedetailleerde statistieken over elk veld. Azure Data Factory maakt een beslissing op basis van de gegevenssampling van welk type grafiek moet worden weergegeven. Velden met hoge kardinaliteit worden standaard weergegeven op NULL-/NOT NULL-grafieken, terwijl categorische en numerieke gegevens met een lage kardinaliteit staafdiagrammen weergeven met de frequentie van de gegevenswaarde. U ziet ook de maximale/lengte van tekenreeksvelden, min/max-waarden in numerieke velden, standaarddev, percentielen, tellingen en gemiddeld.

![Kolomstatistieken](media/data-flow/stats.png "Kolomstatistieken")

## <a name="next-steps"></a>Volgende stappen

* Wanneer u klaar bent met het bouwen en debuggen van uw gegevensstroom, voert [u deze uit vanuit een pijplijn.](control-flow-execute-data-flow-activity.md)
* Wanneer u uw pijplijn test met een gegevensstroom, gebruikt u de optie Voor het uitvoeren van [foutopsporing voor pijplijnen.](iterative-development-debugging.md)
