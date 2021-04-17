---
title: Python-bibliotheken voor Apache Spark
description: Meer informatie over het toevoegen en beheren van Python-bibliotheken die worden gebruikt Apache Spark in Azure Synapse Analytics.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 02/26/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.subservice: spark
ms.openlocfilehash: 1d64233fc477ec25f91bb73c744b10210571df41
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588340"
---
# <a name="manage-python-libraries-for-apache-spark-in-azure-synapse-analytics"></a>Python-bibliotheken voor Apache Spark in Azure Synapse Analytics

Bibliotheken bieden herbruikbare code die u mogelijk wilt opnemen in uw programma's of projecten. 

Mogelijk moet u uw serverloze Apache Spark poolomgeving om verschillende redenen bijwerken. U kunt bijvoorbeeld het volgende vinden:
- een van uw kernafhankelijkheden heeft zojuist een nieuwe versie uitgebracht.
- U hebt een extra pakket nodig om uw machine learning te trainen of uw gegevens voor te bereiden.
- u hebt een beter pakket gevonden en hebt het oudere pakket niet meer nodig.

Als u code van derden of lokaal gebouwde code beschikbaar wilt maken voor uw toepassingen, kunt u een bibliotheek installeren op een van uw serverloze Apache Spark-pools of notebooksessie. In dit artikel wordt beschreven hoe u Python-bibliotheken kunt beheren in uw serverloze Apache Spark pool.

## <a name="default-installation"></a>Standaardinstallatie
Apache Spark in Azure Synapse Analytics beschikt over een volledige set bibliotheken voor algemene data engineering, gegevensvoorbereiding, machine learning en taken voor gegevensvisualisatie. De volledige lijst met bibliotheken vindt u op [Apache Spark ondersteuning voor de versie](apache-spark-version-support.md). 

Wanneer een Spark-exemplaar wordt gestart, worden deze bibliotheken automatisch opgenomen. Extra Python- en aangepaste pakketten kunnen worden toegevoegd op het niveau van de Spark-pool en -sessie.

## <a name="pool-libraries"></a>Poolbibliotheken
Zodra u de Python-bibliotheken hebt geïdentificeerd die u wilt gebruiken voor uw Spark-toepassing, kunt u ze installeren in een Spark-pool. Bibliotheken op poolniveau zijn beschikbaar voor alle notebooks en taken die in de pool worden uitgevoerd.

Er zijn twee primaire manieren om een bibliotheek op een cluster te installeren:
-  Installeer een werkruimtebibliotheek die is geüpload als een werkruimtepakket.
-  Geef een *requirements.txt* of *Conda environment.yml-omgevingsspecificatie* op om pakketten te installeren vanuit opslagplaatsen zoals PyPI, Conda-Forge en meer.

> [!IMPORTANT]
> - Als het pakket dat u installeert groot is of lang duurt om te installeren, is dit van invloed op de opstarttijd van het Spark-exemplaar.
> - Het wijzigen van de PySpark-, Python-, Scala/Java-, .NET- of Spark-versie wordt niet ondersteund.
> - Het installeren van pakketten vanuit externe opslagplaatsen, zoals PyPI, Conda-Forge of de standaard Conda-kanalen, wordt niet ondersteund in werkruimten met DEP-ondersteuning.

### <a name="install-python-packages"></a>Python-pakketten installeren
Python-pakketten kunnen worden geïnstalleerd vanuit opslagplaatsen zoals PyPI en Conda-Forge door een omgevingsspecificatiebestand op te geven. 

#### <a name="environment-specification-formats"></a>Indelingen van omgevingsspecificaties

##### <a name="pip-requirementstxt"></a>PIP-requirements.txt
Een *requirements.txt* -bestand (uitvoer van de `pip freeze` opdracht) kan worden gebruikt om de omgeving bij te werken. Wanneer een pool wordt bijgewerkt, worden de pakketten die in dit bestand worden vermeld, gedownload van PyPI. De volledige afhankelijkheden worden vervolgens in de cache opgeslagen voor later hergebruik van de pool. 

In het volgende codefragment ziet u de indeling voor het bestand met vereisten. De naam van het PyPI-pakket wordt samen met een exacte versie weergegeven. Dit bestand volgt de indeling die wordt beschreven in de [referentiedocumentatie over pip-freeze.](https://pip.pypa.io/en/stable/reference/pip_freeze/) 

In dit voorbeeld wordt een specifieke versie vastgemaakt. 
```
absl-py==0.7.0
adal==1.2.1
alabaster==0.7.10
```
##### <a name="yml-format-preview"></a>YML-indeling (preview)
Daarnaast kunt u ook een bestand *environment.yml leveren* om de poolomgeving bij te werken. De pakketten die in dit bestand worden vermeld, worden gedownload van de conda-standaardkanalen, Conda-Forge en PyPI. U kunt andere kanalen opgeven of de standaardkanalen verwijderen met behulp van de configuratieopties.

In dit voorbeeld worden de kanalen en Conda/PyPI-afhankelijkheden opgegeven. 

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
Zie Creating an environment from an environment.yml file (Een omgeving maken op basis van een environment.yml-bestand) voor meer informatie over het maken van een omgeving op basis van dit bestand [environment.yml.](https://conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html#activating-an-environment
)

#### <a name="update-python-packages"></a>Python-pakketten bijwerken
Zodra u het bestand met de omgevingsspecificatie of een set bibliotheken hebt geïdentificeerd die u wilt installeren in de Spark-pool, kunt u de Spark-poolbibliotheken bijwerken door naar de Azure Synapse Studio of de Azure Portal. Hier kunt u de omgevingsspecificatie verstrekken en de werkruimtebibliotheken selecteren die u wilt installeren. 

Zodra de wijzigingen zijn opgeslagen, wordt met een Spark-taak de installatie uitgevoerd en wordt de resulterende omgeving in de cache opgeslagen voor later hergebruik. Zodra de taak is voltooid, maken nieuwe Spark-taken of notebooksessies gebruik van de bijgewerkte poolbibliotheken. 

##### <a name="manage-packages-from-azure-synapse-studio-or-azure-portal"></a>Pakketten beheren vanuit Azure Synapse Studio of Azure Portal
Spark-poolbibliotheken kunnen worden beheerd vanuit Azure Synapse Studio of Azure Portal. 

Bibliotheken bijwerken of toevoegen aan een Spark-pool:
1. Navigeer naar Azure Synapse Analytics werkruimte van de Azure Portal.

    Als u vanaf de **Azure Portal:**

    - Selecteer in **de sectie Synapse-resources** het **tabblad Apache Spark en** selecteer een Spark-pool in de lijst.
     
    - Selecteer de **pakketten in de** sectie **Instellingen** van de Spark-pool.
  
    ![Schermopname waarin de knop voor het configuratiebestand voor de uploadomgeving is gelicht.](./media/apache-spark-azure-portal-add-libraries/apache-spark-add-library-azure.png "Python-bibliotheken toevoegen")
   
    Als u vanaf de **Synapse Studio:**
    - Selecteer **Beheren** in het hoofdnavigatievenster en selecteer vervolgens **Apache Spark pools.**

    - Selecteer de **sectie Pakketten** voor een specifieke Spark-pool.
    ![Schermopname waarin de configuratieoptie voor de uploadomgeving uit Studio is gelicht.](./media/apache-spark-azure-portal-add-libraries/studio-update-libraries.png "Python-bibliotheken toevoegen vanuit Studio")
   
2. Upload het omgevingsconfiguratiebestand met behulp van de  **bestands** selector in de sectie Pakketten van de pagina.
3. U kunt ook extra **werkruimtepakketten selecteren om** toe te voegen aan uw groep. 
4. Zodra u de wijzigingen hebt opgeslagen, wordt een systeem taak geactiveerd om de opgegeven bibliotheken te installeren en in de cache op te slaan. Dit proces vermindert de totale opstarttijd van de sessie. 
5. Zodra de taak is voltooid, zullen alle nieuwe sessies de bijgewerkte poolbibliotheken ophalen.

> [!IMPORTANT]
> Als u de optie nieuwe **instellingen forceert,** kunt u alle huidige sessies voor de geselecteerde Spark-pool beëindigen. Zodra de sessies zijn beëindigd, moet u wachten tot de pool opnieuw is gestart. 
>
> Als deze instelling is uitgeschakeld, moet u wachten tot de huidige Spark-sessie is gestopt of handmatig. Zodra de sessie is beëindigd, moet u de pool opnieuw opstarten.


##### <a name="track-installation-progress-preview"></a>Voortgang van de installatie bijhouden (preview)
Telkens wanneer een pool wordt bijgewerkt met een nieuwe set bibliotheken, wordt er een door het systeem gereserveerde Spark-taak gestart. Met deze Spark-taak kunt u de status van de bibliotheekinstallatie controleren. Als de installatie mislukt vanwege bibliotheekconflicten of andere problemen, keert de Spark-pool terug naar de vorige of standaardtoestand. 

Daarnaast kunnen gebruikers ook de installatielogboeken inspecteren om afhankelijkheidsconflicten te identificeren of te zien welke bibliotheken tijdens de update van de pool zijn geïnstalleerd.

Deze logboeken weergeven:
1. Navigeer naar de lijst met Spark-toepassingen op **het tabblad** Monitor. 
2. Selecteer de Spark-toepassings taak van het systeem die overeenkomt met de update van uw pool. Deze systeemtaken worden uitgevoerd onder *de titel SystemReservedJob-LibraryManagement.*
   ![Schermopname met de taak voor een gereserveerde bibliotheek voor het systeem.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job.png "Taak voor systeembibliotheek weergeven")
3. Schakel over om de logboeken **voor stuurprogramma's** **en stdout weer te** zien. 
4. In de resultaten ziet u de logboeken met betrekking tot de installatie van uw afhankelijkheden.
    ![Schermopname met de resultaten van de taak van de gereserveerde bibliotheek voor het systeem.](./media/apache-spark-azure-portal-add-libraries/system-reserved-library-job-results.png "De voortgang van de taak van de systeembibliotheek weergeven")

## <a name="install-wheel-files"></a>Wheel-bestanden installeren
Python-wheel-bestanden zijn een veelgebruikte manier om Python-bibliotheken te verpakken. Binnen Azure Synapse Analytics kunnen gebruikers hun wheel-bestanden uploaden naar een bekende locatie van het Azure Data Lake Storage-account of uploaden met behulp van de interface Azure Synapse Workspace-pakketten.

### <a name="workspace-packages-preview"></a>Werkruimtepakketten (preview)
Werkruimtepakketten kunnen aangepaste of persoonlijke wheel-bestanden zijn. U kunt deze pakketten uploaden naar uw werkruimte en deze later toewijzen aan een specifieke Spark-pool.

Werkruimtepakketten toevoegen:
1. Navigeer naar **het**  >  **tabblad Werkruimtepakketten** beheren.
2. Upload uw wheel-bestanden met behulp van de bestands selector.
3. Zodra de bestanden zijn geüpload naar Azure Synapse werkruimte, kunt u deze pakketten toevoegen aan een bepaalde Apache Spark groep.

![Schermopname met werkruimtepakketten.](./media/apache-spark-azure-portal-add-libraries/studio-add-workspace-package.png "Werkruimtepakketten weergeven")

>[!WARNING]
>- Binnen Azure Synapse kan een Apache Spark-pool gebruikmaken van aangepaste bibliotheken die worden geüpload als werkruimtepakketten of die zijn geüpload binnen een bekend Azure Data Lake Storage pad. Beide opties kunnen echter niet tegelijkertijd worden gebruikt binnen dezelfde Apache Spark groep. Als pakketten worden geleverd met behulp van beide methoden, worden alleen de wheel-bestanden die zijn opgegeven in de lijst Werkruimtepakketten geïnstalleerd. 
>
>- Zodra werkruimtepakketten (preview) worden gebruikt voor het installeren van pakketten op een bepaalde Apache Spark-groep, is er een beperking dat u geen pakketten meer kunt opgeven met behulp van het pad naar het opslagaccount in dezelfde groep.  

### <a name="storage-account"></a>Storage-account
Aangepaste wheel-pakketten kunnen worden geïnstalleerd in de Apache Spark-pool door alle wheel-bestanden te uploaden naar het Azure Data Lake Storage (Gen2)-account dat is gekoppeld aan de Synapse-werkruimte. 

De bestanden moeten worden geüpload naar het volgende pad in de standaardcontainer van het opslagaccount: 

```
abfss://<file_system>@<account_name>.dfs.core.windows.net/synapse/workspaces/<workspace_name>/sparkpools/<pool_name>/libraries/python/
```

>[!WARNING]
> In sommige gevallen moet u mogelijk het bestandspad maken op basis van de bovenstaande structuur als het nog niet bestaat. U moet bijvoorbeeld de map in de map toevoegen als deze ```python``` ```libraries``` nog niet bestaat.

> [!IMPORTANT]
> Als u aangepaste bibliotheken wilt installeren met behulp van  de Azure  DataLake Storage-methode, moet u de machtiging Inzender voor opslagblobgegevens of Eigenaar van opslagblobgegevens hebben voor het primaire Gen2-opslagaccount dat is gekoppeld aan de Azure Synapse Analytics-werkruimte.


## <a name="session-scoped-packages-preview"></a>Pakketten binnen sessiebereik (preview)
Naast pakketten op groepsniveau kunt u aan het begin van een notebooksessie ook bibliotheken met sessiebereik opgeven.  Met sessiebereikbibliotheken kunt u aangepaste Python-omgevingen opgeven en gebruiken in een notebooksessie. 

Wanneer u sessiebibliotheken gebruikt, is het belangrijk dat u rekening houdt met de volgende punten:
   - Wanneer u bibliotheken binnen sessiebereik installeert, heeft alleen het huidige notebook toegang tot de opgegeven bibliotheken. 
   - Deze bibliotheken hebben geen invloed op andere sessies of taken die gebruikmaken van dezelfde Spark-pool. 
   - Deze bibliotheken worden geïnstalleerd op de basisruntime- en poolniveaubibliotheken. 
   - Notebookbibliotheken hebben de hoogste prioriteit.

Pakketten met sessiebereik opgeven:
1.  Navigeer naar de geselecteerde Spark-pool en zorg ervoor dat u bibliotheken op sessieniveau hebt ingeschakeld.  U kunt deze instelling inschakelen door te navigeren naar **het** tabblad Apache Spark groep  >    >   beheren. ![Sessiepakketten inschakelen.](./media/apache-spark-azure-portal-add-libraries/enable-session-packages.png "Sessiepakketten inschakelen")
2.  Zodra de instelling is toegepast, kunt u een notebook openen en **Sessiepakketten** >  **configureren selecteren.**
  ![Sessiepakketten opgeven.](./media/apache-spark-azure-portal-add-libraries/update-session-notebook.png "Sessieconfiguratie bijwerken")
3.  Hier kunt u een bestand Conda *environment.yml uploaden* om pakketten in een sessie te installeren of bij te werken. Zodra u de sessie start, worden de opgegeven bibliotheken geïnstalleerd. Zodra de sessie is beëindigd, zijn deze bibliotheken niet meer beschikbaar, omdat ze specifiek zijn voor uw sessie.

## <a name="verify-installed-libraries"></a>Geïnstalleerde bibliotheken controleren
Voer de volgende code uit om te controleren of de juiste versies van de juiste bibliotheken zijn geïnstalleerd vanuit PyPI:
```python
import pkg_resources
for d in pkg_resources.working_set:
     print(d)
```
In sommige gevallen moet u de pakketversie afzonderlijk inspecteren om de pakketversies van Conda te bekijken.

## <a name="next-steps"></a>Volgende stappen
- De standaardbibliotheken weergeven: [Apache Spark versieondersteuning](apache-spark-version-support.md)
- Bibliotheekinstallatiefouten oplossen: [Bibliotheekfouten oplossen](apache-spark-troubleshoot-library-errors.md)
- Een privé-Conda-kanaal maken met uw Azure Data Lake Storage-account: [Privékanalen van Conda](./spark/../apache-spark-custom-conda-channel.md)
