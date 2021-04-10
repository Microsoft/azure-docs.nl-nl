---
title: Python-bibliotheken voor Apache Spark beheren
description: Meer informatie over het toevoegen en beheren van python-bibliotheken die worden gebruikt door Apache Spark in azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 2d6ac02402414f096a46fec0340c3074d8e1784a
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104586638"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Python-bibliotheken voor Apache Spark beheren in azure Synapse Analytics

Bibliotheken bieden herbruikbare code die u mogelijk wilt toevoegen aan uw Program ma's of projecten. 

Mogelijk moet u uw serverloze Apache Spark pool omgeving om verschillende redenen bijwerken. U kunt bijvoorbeeld het volgende doen:
- een van de kern afhankelijkheden heeft zojuist een nieuwe versie uitgegeven.
- u hebt een extra pakket nodig om uw machine learning model te trainen of uw gegevens voor te bereiden.
- u hebt een beter pakket gevonden en hebt het oudere pakket niet meer nodig.

Als u een derde of lokaal gemaakte code beschikbaar wilt maken voor uw toepassingen, kunt u een bibliotheek installeren op een van de serverloze Apache Spark Pools of de notitieblok sessie. In dit artikel wordt beschreven hoe u python-bibliotheken kunt beheren op uw serverloze Apache Spark groep.

## <a name="default-installation"></a>Standaard installatie
Apache Spark in azure Synapse Analytics beschikt over een volledige set bibliotheken voor algemene taken voor data engineering, gegevens voorbereiding, machine learning en gegevens visualisatie. De lijst met volledige bibliotheken vindt u op [Apache Spark-versie ondersteuning](apache-spark-version-support.md). 

Wanneer een Spark-exemplaar wordt gestart, worden deze bibliotheken automatisch opgenomen. Extra python en aangepaste pakketten kunnen worden toegevoegd aan de Spark-groep en op het sessie niveau.

## <a name="pool-libraries"></a>Groeps bibliotheken
Wanneer u de python-bibliotheken hebt geïdentificeerd die u wilt gebruiken voor uw Spark-toepassing, kunt u deze in een Spark-groep installeren. Bibliotheken op groeps niveau zijn beschikbaar voor alle notebooks en taken die worden uitgevoerd in de groep.

Er zijn twee manieren om een bibliotheek te installeren op een cluster:
-  Een werkruimte bibliotheek installeren die is geüpload als een werkruimte pakket.
-  Geef een *requirements.txt* of *Conda environment. yml* Environment Specification om pakketten te installeren van opslag plaatsen zoals PyPI, Conda-vervalsing en meer.

> [!IMPORTANT]
> - Als het pakket dat u installeert groot is of veel tijd nodig heeft om te worden geïnstalleerd, is dit van invloed op de start tijd van de Spark-instantie.
> - Het wijzigen van de PySpark-, Python-, scala/Java-, .NET-of Spark-versie wordt niet ondersteund.
> - Het installeren van pakketten van externe opslag plaatsen, zoals PyPI, Conda-vervalsing of de standaard Conda-kanalen, wordt niet ondersteund in werk ruimten waarvoor DEP is ingeschakeld.

### <a name="install-python-packages"></a>Python-pakketten installeren
Python-pakketten kunnen worden geïnstalleerd vanuit opslag plaatsen zoals PyPI en Conda-Forge door een omgevings specificatie bestand op te geven. 

#### <a name="environment-specification-formats"></a>Indeling van omgevings specificaties

##### <a name="pip-requirementstxt"></a>PIP-requirements.txt
Een *requirements.txt* -bestand (uitvoer van de `pip freeze` opdracht) kan worden gebruikt om een upgrade van de omgeving uit te voeren. Wanneer een pool wordt bijgewerkt, worden de pakketten die in dit bestand worden weer gegeven gedownload van PyPI. De volledige afhankelijkheden worden vervolgens in de cache opgeslagen en bewaard voor later gebruik van de groep. 

Het volgende code fragment toont de indeling voor het vereisten bestand. De naam van het PyPI-pakket wordt samen met een exacte versie weer gegeven. Dit bestand heeft de indeling die wordt beschreven in de hand leiding voor het [blok keren](https://pip.pypa.io/en/stable/reference/pip_freeze/) van de PIP. 

In dit voor beeld wordt een specifieke versie gespeld. 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format-preview"></a>YML-indeling (preview-versie)
Daarnaast kunt u ook een *environment. yml* -bestand opgeven om de groeps omgeving bij te werken. De pakketten die in dit bestand worden vermeld, worden gedownload uit de standaard Conda-kanalen, Conda-vervalsing en PyPI. U kunt andere kanalen opgeven of de standaard kanalen verwijderen door de configuratie opties te gebruiken.

In dit voor beeld worden de afhankelijkheden Channel en Conda/PyPI opgegeven. 

```
name: stats2
channels:
- defaults
dependencies:
- bokeh
- numpy
- pip:
  - matplotlib
  - koalas==1.7.0
```
Zie [een omgeving maken vanuit een environment. yml-bestand](https://docs.conda.io/projects/conda/latest/user-guide/tasks/manage-environments.html#creating-an-environment-file-manually)voor meer informatie over het maken van een omgeving vanuit deze omgeving. yml-bestand.

#### <a name="update-python-packages"></a>Python-pakketten bijwerken
Zodra u het omgevings specificatie bestand of de set met bibliotheken die u wilt installeren in de Spark-groep hebt geïdentificeerd, kunt u de Spark-groeps bibliotheken bijwerken door te navigeren naar Azure Synapse Studio of Azure Portal. Hier kunt u de omgevings specificatie opgeven en de werkruimte bibliotheken selecteren die u wilt installeren. 

Zodra de wijzigingen zijn opgeslagen, voert een Spark-taak de installatie uit en wordt de resulterende omgeving in de cache opgeslagen, zodat deze later opnieuw kan worden gebruikt. Zodra de taak is voltooid, zullen nieuwe Spark-taken of notitieblok sessies gebruikmaken van de bijgewerkte groeps bibliotheken. 

##### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Pakketten beheren vanuit Azure Synapse Studio of Azure Portal
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
   
2. Upload het omgevings configuratie bestand met behulp van de bestands kiezer in het gedeelte  **pakketten** van de pagina.
3. U kunt ook extra **werkruimte pakketten** selecteren om aan uw groep toe te voegen. 
4. Zodra u de wijzigingen hebt opgeslagen, wordt er een systeem taak geactiveerd om de opgegeven bibliotheken te installeren en in de cache op te slaan. Dit proces helpt de algemene opstart tijd van de sessie te verminderen. 
5. Zodra de taak is voltooid, worden alle nieuwe sessies opgehaald uit de bijgewerkte groeps bibliotheken.

> [!IMPORTANT]
> Als u de optie selecteert om **nieuwe instellingen** af te dwingen, beëindigt u de alle huidige sessies voor de geselecteerde Spark-pool. Zodra de sessies zijn beëindigd, moet u wachten tot de groep opnieuw is opgestart. 
>
> Als deze instelling niet is ingeschakeld, moet u wachten tot de huidige Spark-sessie is beëindigd of het hand matig stoppen. Zodra de sessie is beëindigd, moet u de pool opnieuw opstarten.


##### <a name="track-installation-progress-preview"></a>Voortgang van de installatie volgen (preview-versie)
Telkens wanneer een groep wordt bijgewerkt met een nieuwe set Bibliotheken, wordt een door het systeem gereserveerde Spark-taak gestart. Deze Spark-taak helpt bij het controleren van de status van de bibliotheek installatie. Als de installatie mislukt als gevolg van bibliotheek conflicten of andere problemen, wordt de status van de Spark-groep teruggezet naar de vorige of de standaard instelling. 

Daarnaast kunnen gebruikers de installatie logboeken ook inspecteren om afhankelijkheids conflicten te identificeren of om te zien welke bibliotheken zijn geïnstalleerd tijdens het bijwerken van de groep.

Als u deze logboeken wilt weer geven:
1. Ga naar de lijst met Spark-toepassingen op het tabblad **monitor** . 
2. Selecteer de systeem-Spark-toepassings taak die overeenkomt met uw pool-update. Deze systeem taken worden uitgevoerd onder de titel *SystemReservedJob-LibraryManagement* .
   ![Scherm afbeelding die de systeem bibliotheek taak voor reserve ring markeert.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Systeem bibliotheek taak weer geven")
3. Schakel over om het **stuur programma** -en **stdout** -logboeken weer te geven. 
4. Binnen de resultaten ziet u de logboeken met betrekking tot de installatie van uw afhankelijkheden.
    ![Scherm afbeelding die de taak resultaten van de door het systeem gereserveerde bibliotheek markeert.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "Taak voortgang van systeem bibliotheek weer geven")

## <a name="install-wheel-files"></a>Wiel bestanden installeren
Python-wiel bestanden zijn een veelgebruikte manier voor het inpakken van python-bibliotheken. In azure Synapse Analytics kunnen gebruikers hun wiel bestanden uploaden naar een bekende locatie, het Azure Data Lake Storage-account of uploaden met behulp van de Azure Synapse-interface voor het maken van de werk ruimte.

### <a name="workspace-packages-preview"></a>Werkruimte pakketten (preview-versie)
Werkruimte pakketten kunnen aangepaste of privé-wiel bestanden zijn. U kunt deze pakketten uploaden naar uw werk ruimte en deze later toewijzen aan een specifieke Spark-groep.

Werkruimte pakketten toevoegen:
1. Ga naar het   >  tabblad **werk ruimte-pakketten** beheren.
2. Upload uw wiel bestanden met behulp van de bestands kiezer.
3. Zodra de bestanden zijn geüpload naar de Azure Synapse-werk ruimte, kunt u deze pakketten toevoegen aan een bepaalde Apache Spark groep.

![Scherm afbeelding die de werkruimte pakketten markeert.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Werkruimte pakketten weer geven")

>[!WARNING]
>- Binnen Azure Synapse kan een Apache Spark groep gebruikmaken van aangepaste bibliotheken die zijn geüpload als werkruimte pakketten of worden geüpload binnen een bekend Azure Data Lake Storage pad. Beide opties kunnen echter niet gelijktijdig worden gebruikt binnen dezelfde Apache Spark groep. Als er met beide methoden pakketten worden opgegeven, worden alleen de wiel bestanden geïnstalleerd die zijn opgegeven in de lijst met werkruimte pakketten. 
>
>- Zodra werkruimte pakketten (preview) worden gebruikt om pakketten te installeren op een bepaalde Apache Spark pool, is er een beperking dat u geen pakketten meer kunt opgeven met het pad naar het opslag account in dezelfde groep.  

### <a name="storage-account"></a>Storage-account
Aangepaste, ingebouwde wiel pakketten kunnen worden geïnstalleerd op de Apache Spark groep door alle wiel bestanden te uploaden naar het Azure Data Lake Storage-account (Gen2) dat is gekoppeld aan de werk ruimte Synapse. 

De bestanden moeten worden geüpload naar het volgende pad in de standaard container van het opslag account: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

>[!WARNING]
> In sommige gevallen moet u het bestandspad wellicht maken op basis van de bovenstaande structuur als deze nog niet bestaat. Mogelijk moet u de ```python``` map in de ```libraries``` map toevoegen als deze nog niet bestaat.

> [!IMPORTANT]
> Als u aangepaste bibliotheken wilt installeren met behulp van de Azure DataLake-opslag methode, moet u de machtigingen voor de **opslag-BLOB gegevens Inzender** of de **blobgegevens eigenaar** hebben voor het primaire Gen2-opslag account dat is gekoppeld aan de Azure Synapse Analytics-werk ruimte.


## <a name="session-scoped-packages-preview"></a>Pakketten met sessie bereik (preview-versie)
Naast pool level pakketten kunt u ook bibliotheken met sessie bereik aan het begin van een notitieblok sessie opgeven.  Met bibliotheken met sessie bereik kunt u aangepaste python-omgevingen opgeven en gebruiken in een notitieblok sessie. 

Wanneer u bibliotheken met sessie bereik gebruikt, is het belang rijk dat u rekening houdt met de volgende punten:
   - Wanneer u bibliotheken met sessie bereik installeert, heeft alleen het huidige notitie blok toegang tot de opgegeven bibliotheken. 
   - Deze bibliotheken zijn niet van invloed op andere sessies of taken die gebruikmaken van dezelfde Spark-groep. 
   - Deze bibliotheken worden boven op de basis-runtime-en pool niveau-bibliotheken geïnstalleerd. 
   - Notebook bibliotheken hebben de hoogste prioriteit.

Pakketten met sessie bereik opgeven:
1.  Ga naar de geselecteerde Spark-groep en zorg ervoor dat u beschikt over de ingeschakelde bibliotheken op sessie niveau.  U kunt deze instelling inschakelen door te navigeren naar het   >  tabblad **Apache Spark groeps**  >  **pakketten** beheren. ![Schakel sessie pakketten in.](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "Sessie pakketten inschakelen")
2.  Zodra de instelling is toegepast, kunt u een notitie blok openen en **sessie** >  **pakketten** configureren selecteren.
  ![Sessie pakketten opgeven.](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "Sessie configuratie bijwerken")
3.  Hier kunt u een Conda *environment. yml* -bestand uploaden om pakketten binnen een sessie te installeren of bij te werken. Zodra u de sessie hebt gestart, worden de opgegeven bibliotheken geïnstalleerd. Zodra de sessie is beëindigd, zijn deze bibliotheken niet langer beschikbaar omdat ze specifiek zijn voor uw sessie.

## <a name="verify-installed-libraries"></a>Geïnstalleerde bibliotheken verifiëren
Voer de volgende code uit om te controleren of de juiste versies van de juiste bibliotheken zijn geïnstalleerd vanaf PyPI:
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
In sommige gevallen moet u de pakket versie mogelijk afzonderlijk controleren om de pakket versies van Conda te kunnen bekijken.

## <a name="next-steps"></a>Volgende stappen
- De standaard bibliotheken weer geven: [ondersteuning van Apache Spark-versie](apache-spark-version-support.md)
- Problemen met bibliotheek installatie fouten oplossen: [problemen met bibliotheek fouten oplossen](apache-spark-troubleshoot-library-errors.md)
- Een persoonlijk Conda-kanaal maken met uw Azure Data Lake Storage account: [Conda private channels](./spark/../apache-spark-custom-conda-channel.md)
