---
title: Scala & Java-bibliotheken voor Apache Spark beheren
description: Meer informatie over het toevoegen en beheren van scala-en Java-bibliotheken in azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: c70ecc4fc5469d728bc12d47024585ccf00ff98e
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102098703"
---
# <a name="manage-scala-and-java-packages-for-apache-spark-in-azure-synapse-analytics"></a>Scala-en Java-pakketten beheren voor Apache Spark in azure Synapse Analytics

Bibliotheken bieden herbruikbare code die u mogelijk wilt toevoegen aan uw Program ma's of projecten. 

Mogelijk moet u uw serverloze Apache Spark pool omgeving om verschillende redenen bijwerken. U kunt bijvoorbeeld het volgende doen:
- een van de kern afhankelijkheden heeft een nieuwe versie uitgegeven.
- u hebt een extra pakket nodig om uw machine learning model te trainen of uw gegevens voor te bereiden.
- u hebt een beter pakket gevonden en hebt het oudere pakket niet meer nodig.

Als u een derde of lokaal gemaakte code beschikbaar wilt maken voor uw toepassingen, kunt u een bibliotheek installeren op een van de serverloze Apache Spark Pools of de notitieblok sessie. In dit artikel wordt besproken hoe u scala en Java-pakketten kunt beheren.

## <a name="default-installation"></a>Standaard installatie
Apache Spark in azure Synapse Analytics beschikt over een volledige set bibliotheken voor algemene taken voor data engineering, gegevens voorbereiding, machine learning en gegevens visualisatie. De lijst met volledige bibliotheken vindt u op [Apache Spark-versie ondersteuning](apache-spark-version-support.md). 

Wanneer een Spark-exemplaar wordt gestart, worden deze bibliotheken automatisch opgenomen. Extra scala/Java-pakketten kunnen worden toegevoegd aan de Spark-groep en op het sessie niveau.

## <a name="workspace-packages"></a>Werkruimte pakketten
Werkruimte pakketten kunnen aangepaste of privé jar-bestanden zijn. U kunt deze pakketten uploaden naar uw werk ruimte en deze later toewijzen aan een specifieke Spark-groep.

Werkruimte pakketten toevoegen:
1. Ga naar het   >  tabblad **werk ruimte-pakketten** beheren.
2. Upload uw JAR-bestanden met behulp van de bestands kiezer.
3. Zodra de bestanden zijn geüpload naar de Azure Synapse-werk ruimte, kunt u deze jar-bestanden toevoegen aan een bepaalde Apache Spark groep.

![Scherm afbeelding die de werkruimte pakketten markeert.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Werkruimte pakketten weer geven")

## <a name="pool-libraries"></a>Groeps bibliotheken
Zodra u de scala-en Java-pakketten hebt geïdentificeerd die u wilt gebruiken voor uw Spark-toepassing, kunt u deze installeren in een Spark-groep. Bibliotheken op groeps niveau zijn beschikbaar voor alle notebooks en taken die worden uitgevoerd in de groep.

U kunt de Spark-groeps bibliotheken bijwerken door te navigeren naar Azure Synapse Studio of Azure Portal. Hier kunt u de werkruimte bibliotheken selecteren die u wilt installeren. 

Nadat de wijzigingen zijn opgeslagen, voert een Spark-taak de installatie uit en wordt de resulterende omgeving in de cache opgeslagen, zodat deze later opnieuw kan worden gebruikt. Zodra de taak is voltooid, zullen nieuwe Spark-taken of notitieblok sessies gebruikmaken van de bijgewerkte groeps bibliotheken. 

> [!IMPORTANT]
> - Als het pakket dat u installeert groot is of veel tijd nodig heeft om te worden geïnstalleerd, is dit van invloed op de start tijd van de Spark-instantie.
> - Het wijzigen van de PySpark-, Python-, scala/Java-, .NET-of Spark-versie wordt niet ondersteund.

#### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Pakketten beheren vanuit Azure Synapse Studio of Azure Portal
Spark-groeps bibliotheken kunnen worden beheerd vanuit Azure Synapse Studio of Azure Portal. 

Om bibliotheken bij te werken of toe te voegen aan een Spark-groep:
1. Ga vanuit het Azure Portal naar uw Azure Synapse Analytics-werk ruimte.

    Als u bijwerkt vanuit de **Azure Portal**:

    - Selecteer in de sectie **Synapse resources** het tabblad **Apache Spark groepen** en selecteer een Spark-groep in de lijst.
     
    - Selecteer de **pakketten** in het gedeelte **instellingen** van de Spark-groep.
  
    ![Scherm opname van de knop configuratie bestand voor het uploaden van de omgeving.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Python-bibliotheken toevoegen")
   
    Als u bijwerkt vanuit de **Synapse Studio**:
    - Selecteer **beheren** in het hoofd paneel navigatie en selecteer vervolgens **Apache Spark groepen**.

    - Selecteer de sectie **pakketten** voor een specifieke Spark-groep.
    ![Scherm afbeelding die de configuratie optie voor de upload omgeving van Studio markeert.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Python-bibliotheken toevoegen vanuit Studio")
   
2. Om jar-bestanden toe te voegen, gaat u naar de sectie **werkruimte pakketten** om aan uw groep toe te voegen. 
3. Zodra u de wijzigingen hebt opgeslagen, wordt er een systeem taak geactiveerd om de opgegeven bibliotheken te installeren en in de cache op te slaan. Dit proces helpt de algemene opstart tijd van de sessie te verminderen. 
4. Zodra de taak is voltooid, worden alle nieuwe sessies opgehaald uit de bijgewerkte groeps bibliotheken.

> [!IMPORTANT]
> Als u de optie selecteert om **nieuwe instellingen** af te dwingen, beëindigt u de alle huidige sessies voor de geselecteerde Spark-pool. Zodra de sessies zijn beëindigd, moet u wachten tot de groep opnieuw is opgestart. 
>
> Als deze instelling niet is ingeschakeld, moet u wachten tot de huidige Spark-sessie is beëindigd of het hand matig stoppen. Zodra de sessie is beëindigd, moet u de pool opnieuw opstarten.

#### <a name="track-installation-progress-preview"></a>Voortgang van de installatie volgen (preview-versie)
Telkens wanneer een groep wordt bijgewerkt met een nieuwe set Bibliotheken, wordt een door het systeem gereserveerde Spark-taak gestart. Deze Spark-taak helpt bij het controleren van de status van de bibliotheek installatie. Als de installatie mislukt als gevolg van bibliotheek conflicten of andere problemen, wordt de status van de Spark-groep teruggezet naar de vorige of de standaard instelling. 

Daarnaast kunnen gebruikers de installatie logboeken ook inspecteren om afhankelijkheids conflicten te identificeren of om te zien welke bibliotheken zijn geïnstalleerd tijdens het bijwerken van de groep.

Als u deze logboeken wilt weer geven:
1. Ga naar de lijst met Spark-toepassingen op het tabblad **monitor** . 
2. Selecteer de systeem-Spark-toepassings taak die overeenkomt met uw pool-update. Deze systeem taken worden uitgevoerd onder de titel *SystemReservedJob-LibraryManagement* .
   ![Scherm afbeelding die de systeem bibliotheek taak voor reserve ring markeert.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Systeem bibliotheek taak weer geven")
3. Schakel over om het **stuur programma** -en **stdout** -logboeken weer te geven. 
4. Binnen de resultaten ziet u de logboeken met betrekking tot de installatie van uw pakketten.
    ![Scherm afbeelding die de taak resultaten van de door het systeem gereserveerde bibliotheek markeert.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Taak voortgang van systeem bibliotheek weer geven")

## <a name="session-scoped-libraries"></a>Bibliotheken met sessie bereik 
Naast groeps niveau bibliotheken kunt u ook bibliotheken met sessie bereik aan het begin van een notitieblok sessie opgeven.  Met bibliotheken met sessie bereik kunt u jar-pakketten opgeven en gebruiken die uitsluitend binnen een notitieblok sessie worden gebruikt. 

Wanneer u bibliotheken met sessie bereik gebruikt, is het belang rijk dat u rekening houdt met de volgende punten:
   - Wanneer u bibliotheken met sessie bereik installeert, heeft alleen het huidige notitie blok toegang tot de opgegeven bibliotheken. 
   - Deze bibliotheken zijn niet van invloed op andere sessies of taken die gebruikmaken van dezelfde Spark-groep. 
   - Deze bibliotheken worden boven op de basis-runtime-en pool niveau-bibliotheken geïnstalleerd. 
   - Notebook bibliotheken hebben de hoogste prioriteit.

Als u Java-of scala-pakketten met sessies wilt opgeven, kunt u de ```%%configure``` optie:

```scala
%%configure -f
{
    "conf": {
        "spark.jars": "abfss://<<file system>>@<<storage account>.dfs.core.windows.net/<<path to JAR file>>",
    }
}
```

U wordt aangeraden de configuratie%% aan het begin van uw notitie blok uit te voeren. U kunt dit [document](https://github.com/cloudera/livy#request-body) raadplegen voor de volledige lijst met geldige para meters.

## <a name="next-steps"></a>Volgende stappen
- De standaard bibliotheken weer geven: [ondersteuning van Apache Spark-versie](apache-spark-version-support.md)
- Problemen met bibliotheek installatie fouten oplossen: [problemen met bibliotheek fouten oplossen](apache-spark-troubleshoot-library-errors.md)
