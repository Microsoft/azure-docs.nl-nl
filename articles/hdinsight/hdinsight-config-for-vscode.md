---
title: Naslag informatie over Azure HDInsight-configuratie-instellingen
description: Introductie van de configuratie van de Azure HDInsight-extensie.
ms.service: hdinsight
ms.topic: how-to
ms.date: 04/07/2021
ms.custom: devx-track-python
ms.openlocfilehash: a9a444c2557ea17114123cbd5084cbbd9fe6dac6
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107259969"
---
# <a name="azure-hdinsight-configuration-settings-reference"></a>Naslag informatie over Azure HDInsight-configuratie-instellingen

De Spark-uitbrei ding voor de Hive-& voor Visual Studio code kan zeer worden geconfigureerd. Op deze pagina worden de belangrijkste instellingen beschreven waarmee u kunt werken.  

Voor algemene informatie over het werken met instellingen in VS code raadpleegt u de [gebruikers-en werk ruimte-instellingen](https://code.visualstudio.com/docs/getstarted/settings)en de [referentie variabelen](https://code.visualstudio.com/docs/editor/variables-reference) voor informatie over vooraf gedefinieerde ondersteuning van variabelen.

## <a name="open-the-azure-hdinsight-configuration"></a>Open de Azure HDInsight-configuratie

1. Open eerst een map om instellingen voor de werk ruimte te maken.
2. Druk op **CTRL + SHIFT + P** of navigeer naar **weer gave**  ->  **opdracht palet...** om alle opdrachten weer te geven.
3. Configuratie van zoek **sets**.
4. Vouw **uitbrei dingen** in de map links uit en selecteer **HDInsight-configuratie**.

 ![HDI-configuratie kopie](./media/HDInsight-config-for-vscode/HDInsight-config-for-vscode.png)

## <a name="general-settings"></a>Algemene instellingen   

|  Eigenschap   | Standaard | Beschrijving   |
| ----- | ----- |----- |
| HDInsight: Azure-omgeving | Azure | Azure-omgeving |
| HDInsight: open enquête koppeling uitschakelen | Ingeschakeld | Het openen van HDInsight-enquête in-/uitschakelen |
| HDInsight: Schakel overs Laan Pyspark-installatie | Niet ingeschakeld | Pyspark-installatie overs Laan inschakelen/uitschakelen |
| HDInsight: aanmeldings tips inschakelen | Niet ingeschakeld | Wanneer deze optie is ingeschakeld, wordt u gevraagd om u aan te melden bij Azure |
| HDInsight: vorige extensie versie | Het versie nummer van de huidige uitbrei ding weer geven | De vorige extensie versie weer geven|
| HDInsight: lettertype familie voor resultaten | -Apple-System, BlinkMacSystemFont, Segoe WPC, Segoe UI, HelveticaNeue-Light, Ubuntu, Droid Sans, Sans-Serif | De lettertype familie instellen voor het resultaten raster. Stel deze waarde in op leeg als u het Editor lettertype wilt gebruiken |
| HDInsight: teken grootte van resultaten | 13 |De teken grootte voor de resultaten gird instellen. Stel deze waarde in op leeg om de editor grootte te gebruiken |
| HDInsight-cluster: gekoppeld cluster | -- | Url's van gekoppelde clusters. Kunt ook het JSON-bestand bewerken om in te stellen |
| HDInsight-Hive: lokalisatie Toep assen | Niet ingeschakeld | Beschrijving Configuratie opties voor het lokaliseren van de geconfigureerde land instelling voor VSCode (moet VSCode opnieuw opstarten om de instellingen van kracht te laten worden)|
| HDInsight-Hive: include-headers kopiëren | Niet ingeschakeld | Beschrijving Configuratie optie voor het kopiëren van resultaten uit de resultaten weergave |
| HDInsight-Hive: nieuwe regel verwijderen kopiëren | Ingeschakeld | Beschrijving Configuratie opties voor het kopiëren van resultaten van meerdere regels vanuit de resultaten weergave |
| HDInsight Hive ›-indeling: kolom definities in kolommen uitlijnen | Niet ingeschakeld | De kolom definitie moet worden uitgelijnd |
| HDInsight Hive ›-indeling: gegevens Type behuizing | geen | Moeten gegevens typen worden opgemaakt als hoofd letters, kleine letters of geen (niet opgemaakt) |
| HDInsight Hive ›-indeling: trefwoord behuizing | geen | Moeten tref woorden worden opgemaakt als hoofd letters, kleine letters of geen (niet opgemaakt) |
| HDInsight Hive ›-indeling: punt Komma's plaatsen vóór volgende instructie | Niet ingeschakeld | Moeten komma's worden geplaatst aan het begin van elke instructie in een lijst, bijvoorbeeld ', mycolumn2 ' in plaats van aan het einde ' mycolumn1 '.|
| HDInsight Hive ›-indeling: locatie selecteren instructie verwijzingen op nieuwe regel | Niet ingeschakeld | Moeten verwijzingen naar objecten in een SELECT-instructie worden opgesplitst in afzonderlijke regels? Bijvoorbeeld, voor ' SELECT C1, C2 van T1 ', staat zowel C1 als C2 op afzonderlijke regels
| HDInsight-Hive: fout Opsporingsgegevens logboek gegevens | Niet ingeschakeld | Beschrijving Uitvoer van debug-logboeken naar de VS code-console (Help-> wissel knop Ontwikkelhulpprogramma's) 
| HDInsight-Hive: standaard open-berichten | Ingeschakeld | True als het deel venster berichten standaard moet worden geopend. ONWAAR voor gesloten|
| HDInsight-Hive: resultaat lettertype familie | -Apple-System, BlinkMacSystemFont, Segoe WPC, Segoe UI, HelveticaNeue-Light, Ubuntu, Droid Sans, Sans-Serif | De lettertype familie instellen voor het resultaten raster. Stel deze waarde in op leeg als u het Editor lettertype wilt gebruiken |
| HDInsight-Hive: teken grootte van resultaten | 13 | De teken grootte instellen voor het resultaten raster. Stel deze waarde in op leeg om de editor grootte te gebruiken |
| HDInsight-Hive › opslaan als CSV: headers toevoegen | Ingeschakeld | Beschrijving Indien waar, worden kolom koppen opgenomen bij het opslaan van resultaten als CSV |
| HDInsight-Hive: snelkoppelingen | -- | Snelkoppelingen met betrekking tot het resultaten venster |
| HDInsight-Hive: batch-tijd weer geven| Niet ingeschakeld | Beschrijving Moet uitvoerings tijd voor afzonderlijke batches worden weer gegeven |
| HDInsight-Hive: deel venster selectie splitsen | volgende | Beschrijving Configuratie opties waarvoor de kolom nieuwe resultaten deel Vensters moet worden geopend |
| Verzen ding van HDInsight-taak: cluster conf | -- | Cluster configuratie |
| Verzen ding van HDInsight-taak: livy conf | -- | Livy-configuratie. POST/batches |
| HDInsight-Jupyter: resultaten toevoegen| Ingeschakeld | Hiermee wordt aangegeven of de resultaten worden toegevoegd aan het venster met resultaten, anders duidelijk en weer gegeven. |
| HDInsight Jupyter: talen | -- | Standaard instellingen per taal. |
| HDInsight Jupyter › log: verbose | Niet ingeschakeld | Als uitgebreide logboek registratie inschakelen |
| HDInsight Jupyter › notebook: startup args | Kan item toevoegen | opdracht regel argumenten voor de jupyter-notebook. Elk argument is een afzonderlijk item in de matrix. Voor een volledige lijst Typ ' jupyter notebook--Help ' in een Terminal venster. |
| HDInsight Jupyter › notebook: opstartmap | $ {workspaceRoot} |-- |
| HDInsight Jupyter: python-uitbrei ding is ingeschakeld | Ingeschakeld | Python-Interactive-Window van MS-python-extensie gebruiken bij het verzenden van pySpark interactieve taken. U kunt ook ons eigen jupyter-venster gebruiken |
| HDInsight Spark.NET: 7z | C:\Program Files\7-Zip | <pad naar 7z.exe> |
| HDInsight Spark.NET: HADOOP_HOME | D:\winutils | <pad naar bin\winutils.exe alleen> Windows-besturings systeem |
| HDInsight Spark.NET: JAVA_HOME | C:\Program Files\Java\ jdk1.8.0_201 \ | Pad naar de start pagina van Java|
| HDInsight Spark.NET: SCALA_HOME | C:\Program Files (x86) \scala\ | Pad naar scala start pagina |
| HDInsight Spark.NET: SPARK_HOME | D:\spark-2.3.3-bin-hadoop2.7\ | Pad naar de Spark-start pagina |
| Hive: tabbladen met persistente query resultaten | Niet ingeschakeld | Hive PersistQueryResultTabs |
| Hive: deel venster selectie splitsen | volgende | Beschrijving Configuratie opties waarvoor de kolom nieuwe resultaten deel Vensters moet worden geopend |
| Hive Interactive: uitvoer bare map kopiëren | Niet ingeschakeld | Als u de map Hive Interactive service runtime naar de map tmp van de gebruiker kopieert |
| HQL Interactive Server: wrapper-poort | 13424 | Component interactieve service poort |
| HQL-taal server: taal wrapper-poort | 12342 | Service poort servers van Hive-taal Luis teren naar. |
| HQL-taal server: maximum aantal problemen | 100 | Hiermee bepaalt u het maximum aantal problemen dat door de server wordt geproduceerd. |
| Synapse Spark Compute: Synapse Spark Compute Azure-omgeving | niets | Synapse Spark Compute Azure-omgeving |
| Synapse Spark pool-taak verzenden: livy conf | -- | Livy-configuratie. POST/batches
| Synapse Spark pool-taak verzenden: Synapse Spark pool-cluster conf | -- | Configuratie van Synapse Spark-pool |


## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over Azure HDInsight voor VSCode het [onderdeel Spark & voor Visual Studio code-Hulpprogram ma's](https://docs.microsoft.com/sql/big-data-cluster/spark-hive-tools-vscode).
- Zie voor een video die demonstreert met behulp van Spark-& Hive voor Visual Studio code [spark & Hive voor Visual Studio code](https://go.microsoft.com/fwlink/?linkid=858706).