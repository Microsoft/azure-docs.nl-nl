---
title: Bibliotheekinstallatiefouten oplossen
description: Deze zelfstudie biedt een overzicht van het oplossen van bibliotheekinstallatiefouten.
services: synapse-analytics
author: midesa
ms.author: midesa
ms.service: synapse-analytics
ms.subservice: spark
ms.topic: conceptual
ms.date: 01/04/2021
ms.openlocfilehash: 006abf62c605c2ca34fd1adeadee8e29ae0fb8fb
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588306"
---
# <a name="troubleshoot-library-installation-errors"></a>Bibliotheekinstallatiefouten oplossen 
Als u code van derden of lokaal gebouwde code beschikbaar wilt maken voor uw toepassingen, kunt u een bibliotheek installeren op een van uw serverloze Apache Spark groepen. De pakketten die worden vermeld in het requirements.txt-bestand, worden gedownload van PyPi op het moment dat de pool wordt gestart. Dit vereistenbestand wordt telkens gebruikt wanneer een Spark-exemplaar wordt gemaakt van die Spark-pool. Zodra een bibliotheek is geïnstalleerd voor een Spark-pool, is deze beschikbaar voor alle sessies die dezelfde pool gebruiken. 

In sommige gevallen is het mogelijk dat er geen bibliotheek wordt weergegeven in uw Apache Spark groep. Deze case treedt vaak op wanneer er een fout optreedt in de opgegeven requirements.txt of opgegeven bibliotheken. Wanneer er een fout optreedt in het installatieproces van de bibliotheek, keert de Apache Spark terug naar bibliotheken die zijn opgegeven in de Synapse-basisruntime.

Het doel van dit document is om veelvoorkomende problemen op te lossen en fouten in de installatie van de bibliotheek op te lossen.

## <a name="force-update-your-apache-spark-pool"></a>Uw Apache Spark geforceer
Wanneer u de bibliotheken in uw Apache Spark bij te werken, worden deze wijzigingen doorgevoerd zodra de pool opnieuw is opgestart. Als u actieve taken hebt, blijven deze taken worden uitgevoerd op de oorspronkelijke versie van de Spark-pool.

U kunt de wijzigingen dwingen toe te passen door de optie nieuwe **instellingen forceer te selecteren.** Met deze instelling worden alle huidige sessies voor de geselecteerde Spark-pool beëindigen. Zodra de sessies zijn beëindigd, moet u wachten tot de pool opnieuw is gestart. 

![Python-bibliotheken toevoegen](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Python-bibliotheken toevoegen")

## <a name="track-installation-progress"></a>Voortgang van de installatie bijhouden
Telkens wanneer een pool wordt bijgewerkt met een nieuwe set bibliotheken, wordt er een door het systeem gereserveerde Spark-taak gestart. Met deze Spark-taak kunt u de status van de bibliotheekinstallatie controleren. Als de installatie mislukt vanwege bibliotheekconflicten of andere problemen, keert de Spark-pool terug naar de vorige of standaardtoestand. 

Daarnaast kunnen gebruikers ook de installatielogboeken inspecteren om afhankelijkheidsconflicten te identificeren of te zien welke bibliotheken tijdens de update van de pool zijn geïnstalleerd.

Deze logboeken weergeven:
1. Navigeer naar de lijst met Spark-toepassingen op **het tabblad** Controleren. 
2. Selecteer de spark-toepassings taak van het systeem die overeenkomt met de update van uw pool. Deze systeemtaken worden uitgevoerd onder *de titel SystemReservedJob-LibraryManagement.*
   ![Schermopname met de taak voor een gereserveerde bibliotheek voor het systeem.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Taak voor systeembibliotheek weergeven")
3. Schakel over om de logboeken **voor** **stuurprogramma's en stdout weer te** laten zien. 
4. In de resultaten ziet u de logboeken met betrekking tot de installatie van uw pakketten.
    ![Schermopname met de resultaten van de taak van de gereserveerde bibliotheek voor het systeem.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "De voortgang van de taak van de systeembibliotheek weergeven")

## <a name="validate-your-permissions"></a>Uw machtigingen valideren
Als u bibliotheken wilt installeren  en bijwerken,  moet u de machtiging Inzender voor opslagblobgegevens of Eigenaar van opslagblobgegevens hebben voor het primaire Azure Data Lake Storage Gen2 Storage-account dat is gekoppeld aan de Azure Synapse Analytics-werkruimte.

Als u wilt controleren of u deze machtigingen hebt, kunt u de volgende code uitvoeren:

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
Als er een foutmelding wordt weergegeven, ontbreekt waarschijnlijk de vereiste machtigingen. Als u wilt weten hoe u de vereiste machtigingen verkrijgt, gaat u naar dit document: Machtigingen voor Bijdrager voor opslagblobgegevens of Eigenaar van [opslagblobgegevens toewijzen.](../../storage/common/storage-auth-aad-rbac-portal.md#assign-an-azure-built-in-role)

Als u bovendien een pijplijn gebruikt, moet de werkruimte-MSI ook de machtiging Eigenaar van opslagblobgegevens of Bijdrager voor opslagblobgegevens hebben. Ga naar: Machtigingen verlenen aan door werkruimte beheerde identiteit voor meer informatie over het verlenen van deze machtiging aan [uw werkruimte-identiteit.](../security/how-to-grant-workspace-managed-identity-permissions.md)

## <a name="check-the-environment-configuration-file"></a>Het configuratiebestand van de omgeving controleren
Een omgevingsconfiguratiebestand kan worden gebruikt om de Conda-omgeving bij te werken. Deze acceptabele bestandsindelingen voor Python-poolbeheer worden hier [vermeld.](./apache-spark-manage-python-packages.md)

Het is belangrijk dat u rekening houd met de volgende beperkingen:
   -  De inhoud van het bestand met vereisten mag geen extra lege regels of tekens bevatten. 
   -  [Synapse Runtime](apache-spark-version-support.md) bevat een set bibliotheken die vooraf zijn geïnstalleerd op elke serverloze Apache Spark pool. Pakketten die vooraf zijn geïnstalleerd op de basisruntime, kunnen niet worden verwijderd of verwijderd.
   -  Het wijzigen van de PySpark-, Python-, Scala/Java-, .NET- of Spark-versie wordt niet ondersteund.
   -  Bibliotheken met een Python-sessiebereik accepteren alleen bestanden met een YML-extensie.

## <a name="validate-wheel-files"></a>Wheel-bestanden valideren
De serverloze Synapse Apache Spark groepen zijn gebaseerd op de Linux-distributie. Wanneer u Wheel-bestanden rechtstreeks vanuit PyPI downloadt en installeert, moet u ervoor zorgen dat u de versie selecteert die is gebouwd op Linux en wordt uitgevoerd op dezelfde Python-versie als de Spark-pool.

>[!IMPORTANT]
>Aangepaste pakketten kunnen worden toegevoegd of gewijzigd tussen sessies. U moet echter wachten tot de pool en de sessie opnieuw zijn gestart om het bijgewerkte pakket te zien.

## <a name="check-for-dependency-conflicts"></a>Controleren op afhankelijkheidsconflicten
 Over het algemeen kan het lastig zijn om python-afhankelijkheidsoplossing te beheren. Als u afhankelijkheidsconflicten lokaal wilt oplossen, kunt u uw eigen virtuele omgeving maken op basis van de Synapse Runtime en uw wijzigingen valideren.

De omgeving opnieuw maken en uw updates valideren:
 1. [Download](https://github.com/Azure-Samples/Synapse/blob/main/Spark/Python/base_environment.yml) de sjabloon om de Synapse-runtime lokaal opnieuw te maken. Er kunnen kleine verschillen zijn tussen de sjabloon en de daadwerkelijke Synapse-omgeving.
   
 2. Maak een virtuele omgeving met behulp van [de volgende instructies.](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment) In deze omgeving kunt u een geïsoleerde Python-installatie maken met de opgegeven lijst met bibliotheken. 
    
    ```
    conda myenv create -f environment.yml
    conda activate myenv
    ```
   
 3. Gebruik ``pip install -r <provide your req.txt file>`` om de virtuele omgeving bij te werken met de opgegeven pakketten. Als de installatie resulteert in een fout, is er mogelijk een conflict tussen wat vooraf is geïnstalleerd in de Synapse-basisruntime en wat is opgegeven in het opgegeven vereistenbestand. Deze afhankelijkheidsconflicten moeten worden opgelost om de bijgewerkte bibliotheken op uw serverloze Apache Spark op te halen.

>[!IMPORTANT]
>Problemen kunnen zich snel voor doen wanneer pip en conda samen worden gebruikt. Wanneer u pip en conda combineert, kunt u het beste deze [aanbevolen procedures volgen.](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment)

## <a name="next-steps"></a>Volgende stappen
- De standaardbibliotheken weergeven: [Apache Spark versieondersteuning](apache-spark-version-support.md)
