---
title: Problemen met de bibliotheek installatie oplossen
description: Deze zelf studie biedt een overzicht van het oplossen van problemen met de bibliotheek installatie.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 57e9d0c584600a8fac90499d72cfac1620052603
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694917"
---
# <a name="troubleshoot-library-installation-errors"></a>Problemen met de bibliotheek installatie oplossen 
Als u een derde of lokaal gemaakte code beschikbaar wilt maken voor uw toepassingen, kunt u een bibliotheek installeren op een van de serverloze Apache Spark groepen. De pakketten die in het requirements.txt-bestand worden weer gegeven, worden vanaf PyPi gedownload op het moment dat de pool wordt gestart. Dit vereisten bestand wordt gebruikt wanneer een Spark-exemplaar wordt gemaakt vanuit die Spark-groep. Zodra een bibliotheek is geïnstalleerd voor een Spark-groep, is deze beschikbaar voor alle sessies die gebruikmaken van dezelfde groep. 

In sommige gevallen is het mogelijk dat er geen bibliotheek wordt weer gegeven in uw Apache Spark groep. Dit probleem treedt vaak op wanneer er een fout is opgetreden in de opgegeven requirements.txt of opgegeven bibliotheken. Als er een fout optreedt in het installatie proces van de bibliotheek, wordt de Apache Spark-groep teruggezet naar de bibliotheken die zijn opgegeven in de Synapse-basis runtime.

Het doel van dit document is om veelvoorkomende problemen op te lossen en te helpen bij het opsporen van fouten in de bibliotheek installatie.

## <a name="force-update-your-apache-spark-pool"></a>Bijwerken van uw Apache Spark groep afdwingen
Wanneer u de bibliotheken in uw Apache Spark pool bijwerkt, worden deze wijzigingen opgehaald zodra de groep opnieuw is opgestart. Als u actieve taken hebt, blijven deze taken worden uitgevoerd op de oorspronkelijke versie van de Spark-groep.

Als u de wijzigingen wilt Toep assen, selecteert u de optie om **nieuwe instellingen** af te dwingen. Met deze instelling worden alle huidige sessies voor de geselecteerde Spark-groep beëindigd. Zodra de sessies zijn beëindigd, moet u wachten tot de groep opnieuw is opgestart. 

![Python-bibliotheken toevoegen](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Python-bibliotheken toevoegen")

## <a name="track-installation-progress"></a>Voortgang van de installatie volgen
Telkens wanneer een groep wordt bijgewerkt met een nieuwe set Bibliotheken, wordt een door het systeem gereserveerde Spark-taak gestart. Deze Spark-taak helpt bij het controleren van de status van de bibliotheek installatie. Als de installatie mislukt vanwege bibliotheek conflicten of andere problemen, wordt de status van de Spark-groep teruggezet naar de vorige of de standaard instelling. 

Daarnaast kunnen gebruikers de installatie logboeken ook inspecteren om afhankelijkheids conflicten te identificeren of om te zien welke bibliotheken zijn geïnstalleerd tijdens het bijwerken van de groep.

Als u deze logboeken wilt weer geven:
1. Ga naar de lijst met Spark-toepassingen op het tabblad **monitor** . 
2. Selecteer de systeem-Spark-toepassings taak die overeenkomt met uw pool-update. Deze systeem taken worden uitgevoerd onder de titel *SystemReservedJob-LibraryManagement* .
   ![Scherm afbeelding die de systeem bibliotheek taak voor reserve ring markeert.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Systeem bibliotheek taak weer geven")
3. Schakel over om het **stuur programma** -en **stdout** -logboeken weer te geven. 
4. Binnen de resultaten ziet u de logboeken met betrekking tot de installatie van uw pakketten.
    ![Scherm afbeelding die de taak resultaten van de door het systeem gereserveerde bibliotheek markeert.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Taak voortgang van systeem bibliotheek weer geven")

## <a name="validate-your-permissions"></a>Uw machtigingen valideren
Als u bibliotheken wilt installeren en bijwerken, moet u de machtigingen voor de **gegevens eigenaar** van de **Storage BLOB-data bijdrager** of de opslag-BLOB hebben voor het primaire Azure data Lake Storage Gen2-opslag account dat is gekoppeld aan de Azure Synapse Analytics-werk ruimte.

U kunt controleren of u over deze machtigingen beschikt door de volgende code uit te voeren:

```python
from pyspark.sql.types import StructType,StructField, StringType, IntegerType
data2 = [("James","Smith","Joe","4355","M",3000),
    ("Michael","Rose","Edward","40288","F",4000)
  ]

schema = StructType([ \
    StructField("firstname",StringType(),True), \
    StructField("middlename",StringType(),True), \
    StructField("lastname",StringType(),True), \
    StructField("id", StringType(), True), \
    StructField("gender", StringType(), True), \
    StructField("salary", IntegerType(), True) \
  ])
 
df = spark.createDataFrame(data=data2,schema=schema)

df.write.csv("abfss://<<ENTER NAME OF FILE SYSTEM>>@<<ENTER NAME OF PRIMARY STORAGE ACCOUNT>>.dfs.core.windows.net/validate_permissions.csv")

```
Als er een fout bericht wordt weer gegeven, mist u waarschijnlijk de vereiste machtigingen. Ga voor meer informatie over het verkrijgen van de vereiste machtigingen naar dit document: [toegang tot Inzender voor opslag-blobs of gegevens eigenaar](../../storage/common/storage-auth-aad-rbac-portal.md#assign-an-azure-built-in-role)van de opslag-BLOB toewijzen.

Bovendien, als u een pijp lijn uitvoert, moet de werk ruimte-MSI-gegevens eigenaar van de opslag-BLOB of de gegevensfactory van de opslag-BLOB ook zijn. Ga voor meer informatie over het verlenen van de identiteit van uw werk ruimte aan deze machtiging naar: [machtigingen verlenen aan beheerde identiteit van werk ruimte](../security/how-to-grant-workspace-managed-identity-permissions.md).

## <a name="check-the-environment-configuration-file"></a>Controleer het configuratie bestand van de omgeving
Een configuratie bestand voor de omgeving kan worden gebruikt om de Conda-omgeving bij te werken. Deze acceptabele bestands indelingen voor python-groeps beheer worden [hier](./apache-spark-manage-python-packages.md)weer gegeven.

Het is belang rijk dat u rekening moet houden met de volgende beperkingen:
   -  De inhoud van het vereiste bestand mag geen extra lege regels of tekens bevatten. 
   -  De [Synapse-runtime](apache-spark-version-support.md) bevat een set bibliotheken die vooraf zijn geïnstalleerd op elke serverloze Apache Spark groep. Pakketten die vooraf zijn geïnstalleerd in de basis runtime, kunnen niet worden verwijderd of ongedaan worden gemaakt.
   -  Het wijzigen van de PySpark-, Python-, scala/Java-, .NET-of Spark-versie wordt niet ondersteund.
   -  Met python-sessie bereik bibliotheken worden alleen bestanden met een YML-extensie geaccepteerd.

## <a name="validate-wheel-files"></a>Wiel bestanden valideren
De Synapse serverloze Apache Spark Pools zijn gebaseerd op de Linux-distributie. Wanneer u schijf bestanden rechtstreeks vanuit PyPI downloadt en installeert, moet u ervoor zorgen dat u de versie selecteert die is gebouwd op Linux en wordt uitgevoerd op dezelfde python-versie als de Spark-pool.

>[!IMPORTANT]
>Aangepaste pakketten kunnen worden toegevoegd of gewijzigd tussen sessies. U moet echter wachten tot de groep en de sessie opnieuw worden opgestart om het bijgewerkte pakket weer te geven.

## <a name="check-for-dependency-conflicts"></a>Controleren op afhankelijkheids conflicten
 Over het algemeen kan de oplossing voor python-afhankelijkheid lastig te beheren zijn. Om conflicten met afhankelijkheden lokaal op te lossen, kunt u uw eigen virtuele omgeving maken op basis van de Synapse-runtime en uw wijzigingen valideren.

De omgeving opnieuw maken en uw updates valideren:
 1. [Down load](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml) de sjabloon om de Synapse-runtime lokaal opnieuw te maken. Er kunnen kleine verschillen zijn tussen de sjabloon en de werkelijke Synapse-omgeving.
   
 2. Maak een virtuele omgeving met behulp van de [volgende instructies](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html). Met deze omgeving kunt u een geïsoleerde python-installatie maken met de opgegeven lijst met bibliotheken. 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. Gebruiken ``pip install -r <provide your req.txt file>`` om de virtuele omgeving bij te werken met de opgegeven pakketten. Als de installatie een fout veroorzaakt, is er mogelijk een conflict tussen wat vooraf is geïnstalleerd in de Synapse-basis runtime en wat is opgegeven in het bestand met de opgegeven vereisten. Deze afhankelijkheids conflicten moeten worden opgelost om de bijgewerkte bibliotheken op uw serverloze Apache Spark groep te kunnen ophalen.

>[!IMPORTANT]
>Problemen kunnen arrise wanneer pip en Conda met elkaar worden gebruikt. Bij het combi neren van PIP en Conda is het het beste om deze [Aanbevolen procedures](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#using-pip-in-an-environment)te volgen.

## <a name="next-steps"></a>Volgende stappen
- De standaard bibliotheken weer geven: [ondersteuning van Apache Spark-versie](apache-spark-version-support.md)