---
title: Bibliotheken voor Apache Spark beheren in azure Synapse Analytics
description: Meer informatie over het toevoegen en beheren van bibliotheken die worden gebruikt door Apache Spark in azure Synapse Analytics.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 10/16/2020
ms.author: euang
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 6f777b605c5050b7fa4b3b9e3671f3638ad67372
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016255"
---
# <a name="manage-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Bibliotheken voor Apache Spark beheren in azure Synapse Analytics

Bibliotheken bieden herbruikbare code die u mogelijk wilt toevoegen aan uw Program ma's of projecten. Als u derden of lokaal gemaakte code beschikbaar wilt maken voor uw toepassingen, kunt u een bibliotheek installeren op een van uw serverloze Apache Spark Pools (preview). Zodra een bibliotheek is geïnstalleerd voor een Spark-groep, is deze beschikbaar voor alle sessies die gebruikmaken van dezelfde groep. 

## <a name="default-installation"></a>Standaard installatie
Apache Spark in azure Synapse Analytics beschikt over een volledige Anacondas-installatie plus extra bibliotheken. De lijst met volledige bibliotheken vindt u op [Apache Spark-versie ondersteuning](apache-spark-version-support.md). 

Wanneer een Spark-exemplaar wordt gestart, worden deze bibliotheken automatisch opgenomen. Extra python en aangepaste ingebouwde pakketten kunnen worden toegevoegd op het niveau van de Spark-pool (preview).


## <a name="manage-python-packages"></a>Python-pakketten beheren
Wanneer u de bibliotheken hebt geïdentificeerd die u wilt gebruiken voor uw Spark-toepassing, kunt u deze in een Spark-groep (preview-versie) installeren. 

 Een *requirements.txt* -bestand (uitvoer van de `pip freeze` opdracht) kan worden gebruikt voor het bijwerken van de virtuele omgeving. De pakketten die in dit bestand voor installatie of upgrade worden vermeld, worden vanaf PyPi gedownload op het moment dat de pool wordt gestart. Dit vereisten bestand wordt gebruikt wanneer een Spark-exemplaar wordt gemaakt vanuit die Spark-groep.

> [!IMPORTANT]
> - Als het pakket dat u installeert groot is of veel tijd nodig heeft om te worden geïnstalleerd, is dit van invloed op de start tijd van de Spark-instantie.
> - Pakketten waarvoor compiler ondersteuning is vereist op het moment van installatie, zoals GCC, worden niet ondersteund.
> - Pakketten kunnen niet worden gedowngraded, alleen toegevoegd of geüpgraded.

### <a name="requirements-format"></a>Indeling van vereisten

Het volgende code fragment toont de indeling voor het vereisten bestand. De naam van het PyPi-pakket wordt samen met een exacte versie weer gegeven. Dit bestand heeft de indeling die wordt beschreven in de hand leiding voor het [blok keren](https://pip.pypa.io/en/stable/reference/pip_freeze/) van de PIP. In dit voor beeld wordt een specifieke versie gespeld. 

```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```

### <a name="install-python-packages"></a>Python-pakketten installeren
Tijdens het ontwikkelen van uw Spark-toepassing is het mogelijk dat u bestaande of nieuwe bibliotheken moet bijwerken. Bibliotheken kunnen worden bijgewerkt tijdens of na het maken van de groep.

#### <a name="install-packages-during-pool-creation"></a>Pakketten installeren tijdens het maken van een groep
U kunt als volgt bibliotheken installeren op een Spark-groep (preview) tijdens het maken van de groep:
   
1. Ga vanuit het Azure Portal naar uw Azure Synapse Analytics-werk ruimte.
   
2. Selecteer **Apache Spark groep maken** en selecteer vervolgens het tabblad **aanvullende instellingen** . 
   
3. Upload het omgevings configuratie bestand met behulp van de bestands kiezer in het gedeelte **pakketten** van de pagina. 
   
    ![Python-bibliotheken toevoegen tijdens het maken van een groep](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-add-library-python.png "Python-bibliotheken toevoegen")
 

#### <a name="install-packages-from-the-synapse-workspace"></a>Pakketten installeren vanuit de Synapse-werk ruimte
Als u extra bibliotheken wilt bijwerken of toevoegen aan een Spark-groep (preview) van de Azure Synapse Analytics-portal:

1.  Ga vanuit het Azure Portal naar uw Azure Synapse Analytics-werk ruimte.
   
2.  Start uw Azure Synapse Analytics-werk ruimte vanuit het Azure Portal.

3.  Selecteer **beheren** in het hoofd paneel navigatie en selecteer vervolgens **Apache Spark groepen**.
   
4. Selecteer één Spark-groep en upload het omgevings configuratie bestand met behulp van de bestands kiezer in het gedeelte  **pakketten** van de pagina.

    ![Python-bibliotheken toevoegen in Synapse](./media/apache-spark-azure-portal-add-libraries/apache-spark-azure-portal-update.png)
   
#### <a name="install-packages-from-the-azure-portal"></a>Pakketten van de Azure Portal installeren
Als u een bibliotheek wilt installeren op een Spark-pool (preview), rechtstreeks vanuit de Azure Portal:
   
 1. Ga vanuit het Azure Portal naar uw Azure Synapse Analytics-werk ruimte.
   
 2. Selecteer in de sectie **Synapse resources** het tabblad **Apache Spark groepen** en selecteer een Spark-groep in de lijst.
   
 3. Selecteer **pakketten** in de sectie **instellingen** van de Spark-groep. 

 4. Upload het omgevings configuratie bestand met de bestands kiezer.

    ![Scherm afbeelding van de knop configuratie bestand voor het uploaden van de omgeving.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Python-bibliotheken toevoegen")

### <a name="verify-installed-libraries"></a>Geïnstalleerde bibliotheken verifiëren

Voer de volgende code uit om te controleren of de juiste versies van de juiste bibliotheken zijn geïnstalleerd

```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
### <a name="update-python-packages"></a>Python-pakketten bijwerken
Pakketten kunnen op elk moment tussen sessies worden toegevoegd of gewijzigd. Wanneer een nieuw pakket configuratie bestand wordt geüpload, worden de bestaande pakketten en versies overschreven.  

Een bibliotheek bijwerken of verwijderen:
1. Navigeer naar uw Azure Synapse Analytics-werk ruimte. 

2. Selecteer de **Apache Spark-groep** die u wilt bijwerken met behulp van de Azure portal of de Azure Synapse-werk ruimte.

3. Ga naar de sectie **pakketten** en upload een nieuw configuratie bestand voor de omgeving
   
4. Zodra u de wijzigingen hebt opgeslagen, moet u actieve sessies beëindigen en de pool opnieuw opstarten. Desgewenst kunt u actieve sessies geforceerd beëindigen door het selectie vakje in te scha kelen om **nieuwe instellingen** af te dwingen.

    ![Python-bibliotheken toevoegen](./media/apache-spark-azure-portal-add-libraries/update-libraries.png "Python-bibliotheken toevoegen")
   

> [!IMPORTANT]
> Als u de optie selecteert om **nieuwe instellingen** af te dwingen, beëindigt u de alle huidige sessies voor de geselecteerde Spark-pool. Zodra de sessies zijn beëindigd, moet u wachten tot de groep opnieuw is opgestart. 
>
> Als deze instelling niet is ingeschakeld, moet u wachten tot de huidige Spark-sessie is beëindigd of het hand matig stoppen. Zodra de sessie is beëindigd, moet u de pool opnieuw opstarten. 


## <a name="manage-a-python-wheel"></a>Een python-wiel beheren

### <a name="install-a-custom-wheel-file"></a>Een aangepast wiel bestand installeren
Aangepaste, ingebouwde wiel pakketten kunnen worden geïnstalleerd op de Apache Spark groep door alle wiel bestanden te uploaden naar het Azure Data Lake Storage-account (Gen2) dat is gekoppeld aan de Synapse-werk ruimte. 

De bestanden moeten worden geüpload naar het volgende pad in de standaard container van het opslag account: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

>[!IMPORTANT]
>Aangepaste pakketten kunnen worden toegevoegd of gewijzigd tussen sessies. U moet echter wachten tot de groep en de sessie opnieuw worden opgestart om het bijgewerkte pakket weer te geven.

## <a name="next-steps"></a>Volgende stappen
- De standaard bibliotheken weer geven: [ondersteuning van Apache Spark-versie](apache-spark-version-support.md)
