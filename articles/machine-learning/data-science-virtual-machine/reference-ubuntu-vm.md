---
title: 'Verwijzing: Ubuntu Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Details over hulpprogram ma's die zijn opgenomen in de Ubuntu-Data Science Virtual Machine
author: gvashishtha
ms.service: machine-learning
ms.subservice: data-science-vm
ms.custom: devx-track-python
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: 6fb7b7c2e9022b6b629a039a48bb559b36393f79
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013850"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>Naslag informatie: Ubuntu (Linux) Data Science Virtual Machine

Hieronder vindt u een lijst met beschik bare hulpprogram ma's op uw Ubuntu-Data Science Virtual Machine. 

## <a name="deep-learning-libraries"></a>Diep gaande leer bibliotheken

### <a name="cntk"></a>CNTK

De Microsoft Cognitive Toolkit is een open-source diep Learning Toolkit. Python-bindingen zijn beschikbaar in de hoofd-en py35-Conda-omgeving. Het bevat ook een opdracht regel programma (CNTK) dat al aanwezig is in het pad.

Voor beelden van python-notebooks zijn beschikbaar in JupyterHub. Als u een basis voorbeeld wilt uitvoeren op de opdracht regel, voert u de volgende opdrachten uit in de shell:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Zie de sectie CNTK van [github](https://github.com/Microsoft/CNTK) en de [CNTK-wiki](https://github.com/Microsoft/CNTK/wiki)voor meer informatie.

### <a name="caffe"></a>Caffe

Caffe is een diep leer raamwerk van het Berkeley Vision and Learning Center. Het is beschikbaar in/opt/Caffe. U kunt voor beelden vinden in/opt/Caffe/examples.

### <a name="caffe2"></a>Caffe2

Caffe2 is een diep leer raamwerk van Facebook dat is gebaseerd op Caffe. Het is beschikbaar in Python 2,7 in de hoofd omgeving Conda. Als u deze wilt activeren, voert u de volgende opdracht uit vanuit de shell:

```bash
source /anaconda/bin/activate root
```

Enkele voor beelden van notebooks zijn beschikbaar in JupyterHub.

### <a name="h2o"></a>H2O

H2O is een snelle, in-Memory, gedistribueerde machine learning en predictive analytics platform. Er wordt een python-pakket geïnstalleerd in de hoofd-en py35-Anaconda-omgeving. Er is ook een R-pakket geïnstalleerd. 

Als u H2O vanaf de opdracht regel wilt openen, voert u uit `java -jar /dsvm/tools/h2o/current/h2o.jar` . Er zijn verschillende [opdracht regel opties](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) die u mogelijk wilt configureren. U kunt toegang krijgen tot de flow-webgebruikersinterface door te bladeren naar `http://localhost:54321` om aan de slag te gaan. Voor beelden van notebooks zijn ook beschikbaar in JupyterHub.

### <a name="keras"></a>Keras

Keras is een Neural-netwerk-API op hoog niveau in python. Het kan worden uitgevoerd op tensor flow, Microsoft Cognitive Toolkit of Theano. Deze is beschikbaar in de hoofd-en py35 python-omgeving.

### <a name="mxnet"></a>MXNet

MXNet is een diep leer raamwerk dat is ontworpen voor zowel efficiency als flexibiliteit. Het bevat R-en Python-bindingen die zijn opgenomen in de DSVM. Voor beelden van notebooks zijn opgenomen in JupyterHub en voorbeeld code is beschikbaar in/dsvm/samples/mxnet.

### <a name="nvidia-digits"></a>NVIDIA-CIJFERS

Het trainings systeem van de NVIDIA-Learning GPU, ook wel cijfers genoemd, is een systeem om veelvoorkomende, uitgebreide leer taken te vereenvoudigen. Deze taken omvatten het beheren van gegevens, het ontwerpen en trainen van Neural netwerken op GPU-systemen en het bewaken van prestaties in realtime met geavanceerde visualisatie.

CIJFERS zijn beschikbaar als een service met de naam *cijfers*. Start de service en blader naar `http://localhost:5000` om aan de slag te gaan.

CIJFERS worden ook geïnstalleerd als een python-module in de Conda-hoofd omgeving.

### <a name="tensorflow"></a>TensorFlow

Tensor flow is de bibliotheek voor diepe lessen van Google. Het is een open-source software bibliotheek voor numerieke berekening met behulp van gegevens stroom grafieken. Tensor flow is beschikbaar in de py35 python-omgeving en enkele voor beelden van notebooks zijn opgenomen in JupyterHub.

### <a name="theano"></a>Theano

Theano is een python-bibliotheek voor een efficiënte numerieke berekening. Deze is beschikbaar in de hoofd-en py35 python-omgeving. 

### <a name="torch"></a>Torch

Torch is een weten schappelijk computing Framework met brede ondersteuning voor machine learning-algoritmen. Het is beschikbaar in/dsvm/tools/Torch en **de interactieve sessie** en LuaRocks package manager zijn beschikbaar op de opdracht regel. Voor beelden zijn beschikbaar in/dsvm/samples/Torch.

PyTorch is ook beschikbaar in de basis-Anaconda-omgeving. Voor beelden zijn/dsvm/samples/pytorch.

## <a name="microsoft-machine-learning-server"></a>Microsoft Machine Learning Server

R is een van de populairste talen voor gegevens analyse en machine learning. Als u R voor uw analyse wilt gebruiken, is de VM Microsoft Machine Learning Server met micro soft R open en de reken kundige kernel-bibliotheek. De wiskundige kernelmodus optimaliseert wiskundige bewerkingen die gebruikelijk zijn in analytische algoritmen. Micro soft R open is 100 procent compatibel met de KRANs R en een van de R-bibliotheken die in KRANs zijn gepubliceerd, kan worden geïnstalleerd in micro soft R open. 

Machine Learning Server biedt u schaal-en uitoefening van R-modellen in webservices. U kunt uw R-Program ma's bewerken in een van de standaard editors, zoals RStudio, VI of emacs. Als u liever de Emacs-editor gebruikt, is deze vooraf geïnstalleerd. Het emacs ESS (Emacsse Statistics)-pakket vereenvoudigt het werken met R-bestanden in de Emacs-editor.

Als u de R-console wilt openen, voert u **R** in de shell in. Met deze opdracht gaat u naar een interactieve omgeving. Voor het ontwikkelen van uw R-programma gebruikt u doorgaans een editor zoals Emacs of VI en voert u de scripts uit in R. Met RStudio beschikt u over een volledige grafische IDE om uw R-programma te ontwikkelen.

Er is ook een R-script waarmee u de [beste 20 R-pakketten](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) kunt installeren als u wilt. U kunt dit script uitvoeren nadat u zich in de R-interactieve interface bevindt. Zoals eerder vermeld, kunt u die interface openen door **R** in de shell in te voeren.  

## <a name="python"></a>Python

Anaconda python wordt geïnstalleerd met python 2,7-en 3,5-omgevingen. De 2,7-omgeving heet _root_ en de 3,5-omgeving heet _py35_. Deze distributie bevat de basis-python, samen met ongeveer 300 van de populairste wiskundige, technische en gegevens analyse pakketten.

De py35 omgeving is de standaard instelling. Gebruik deze opdracht om de basis omgeving (2,7) te activeren:

```bash
source activate root
```

Gebruik deze opdracht om de py35-omgeving opnieuw te activeren:

```bash
source activate py35
```

Als u een een python-interactieve sessie wilt aanroepen, voert u **python** in de shell in. 

Aanvullende python-bibliotheken installeren met behulp van Conda of PIP. Voor PIP moet u eerst de juiste omgeving activeren als u niet de standaard waarde wilt:

```bash
source activate root
pip install <package>
```

Of geef het volledige pad op naar PIP:

```bash
/anaconda/bin/pip install <package>
```

Voor Conda moet u altijd de omgevings naam (py35 of root) opgeven:

```bash
conda install <package> -n py35
```

Als u een grafische interface gebruikt of X11 door sturen hebt ingesteld, kunt u **pycharm** invoeren om de PYCHARM python IDE te openen. U kunt de standaard tekst editors gebruiken. Daarnaast kunt u Spyder gebruiken, een python IDE die is gebundeld met Anaconda python-distributies. Spyder moet een grafisch bureau blad of X11 door sturen hebben. Het grafische bureau blad heeft een snelkoppeling naar Spyder.

## <a name="jupyter-notebook"></a>Jupyter-notebook

De Anaconda-distributie wordt ook geleverd met een Jupyter-notebook, een omgeving om code en analyse te delen. De Jupyter-notebook wordt geopend via JupyterHub. U meldt zich aan met uw lokale Linux-gebruikers naam en-wacht woord.

De Jupyter-notebook server is vooraf geconfigureerd met python 2-, python 3-en R-kernels. Gebruik het pictogram **Jupyter notebook** bureau blad om de browser te openen en toegang te krijgen tot de notebook server. Als u gebruikmaakt van de virtuele machine via SSH of de X2Go-client, kunt u ook toegang krijgen tot de Jupyter-notebook server op `https://localhost:8000/` .

> [!NOTE]
> Ga door als u certificaat waarschuwingen ontvangt.

U kunt vanaf elke host toegang krijgen tot de Jupyter-notebook server. Voer **https:// \<VM DNS name or IP address\> : 8000/**.

> [!NOTE]
> Poort 8000 wordt standaard geopend in de firewall wanneer de virtuele machine wordt ingericht. 

We hebben voorbeeld notitieblokken, een in Python en één in R, verpakt. U kunt de koppeling naar de voor beelden op de start pagina van het notitie blok zien nadat u zich bij de Jupyter-notebook hebt geverifieerd met behulp van uw lokale Linux-gebruikers naam en-wacht woord. U kunt een nieuw notitie blok maken door **Nieuw** te selecteren en vervolgens de juiste taal-kernel te selecteren. Als de knop **Nieuw** niet wordt weer gegeven, selecteert u het **Jupyter** pictogram in de linkerbovenhoek om naar de start pagina van de notebook server te gaan.

## <a name="apache-spark-standalone"></a>Zelfstandige Apache Spark

Een zelfstandig exemplaar van Apache Spark is vooraf geïnstalleerd op de Linux-DSVM om u te helpen bij het lokaal ontwikkelen van Spark-toepassingen voordat u ze test en implementeert op grote clusters. 

U kunt PySpark-Program ma's uitvoeren via de Jupyter-kernel. Wanneer u Jupyter opent, selecteert u de knop **Nieuw** en ziet u een lijst met beschik bare kernels. **Spark-python** is de PySpark-kernel waarmee u Spark-toepassingen kunt bouwen met behulp van de python-taal. U kunt ook een python IDE zoals PyCharm of Spyder gebruiken om uw Spark-programma te bouwen. 

In deze zelfstandige instantie wordt de Spark-stack uitgevoerd binnen het aanroepende client programma. Met deze functie kunt u sneller en eenvoudiger problemen oplossen, vergeleken met het ontwikkelen van een Spark-cluster.

Jupyter biedt een voor beeld van een PySpark-notebook. U vindt deze in de SparkML in de hoofdmap van Jupyter ($HOME/notebooks/SparkML/pySpark). 

Als u programmeert in R voor Spark, kunt u Microsoft Machine Learning Server, Spark of sparklyr gebruiken. 

Voordat u uitvoert in een Spark-context in Microsoft Machine Learning Server, moet u een eenmalige installatie stap uitvoeren om een lokaal Hadoop-en garen-exemplaar met één knoop punt in te scha kelen. Hadoop-services worden standaard geïnstalleerd, maar uitgeschakeld op de DSVM. Als u deze wilt inschakelen, moet u de volgende opdrachten uitvoeren als hoofd de eerste keer:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

U kunt de Hadoop-gerelateerde services stoppen wanneer u deze niet nodig hebt door uit te voeren ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` .

De/dsvm/samples/MRS Directory bevat een voor beeld van het ontwikkelen en testen van Microsoft Machine Learning Server in een externe Spark-context (het zelfstandige Spark-exemplaar op de DSVM).

## <a name="ides-and-editors"></a>Ide's en editors

U kunt kiezen uit verschillende code-editors, waaronder VI/vim, emacs, PyCharm, RStudio en IntelliJ. 

PyCharm, RStudio en IntelliJ zijn grafische editors. Als u deze wilt gebruiken, moet u zijn aangemeld bij een grafisch bureau blad. U opent ze met de snelkoppelingen voor het menu bureau blad en toepassingen.

Vim en emacs zijn op tekst gebaseerde editors. Op emacs maakt het ESS add-on-pakket met R eenvoudiger in de Emacs-editor. Meer informatie vindt u op de [ESS-website](https://ess.r-project.org/).

LaTex wordt geïnstalleerd via het texlive-pakket, samen met een Emacs-invoeg toepassings pakket met de naam [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Met dit pakket wordt het ontwerpen van uw LaTex-documenten in Emacs vereenvoudigd.  

## <a name="databases"></a>Databases

### <a name="graphical-sql-client"></a>Grafische SQL-client

SQuirrel SQL, een grafische SQL-Client, kan verbinding maken met verschillende data bases (zoals Microsoft SQL Server en MySQL) en SQL-query's uitvoeren. U kunt SQuirrel SQL uitvoeren vanuit een grafische bureaublad sessie (bijvoorbeeld via de X2Go-client) met behulp van een bureaublad pictogram. U kunt ook de-client uitvoeren met behulp van de volgende opdracht in de shell:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Voordat u de eerste keer gebruikt, moet u uw stuur Programma's en database aliassen instellen. De JDBC-Stuur Programma's bevinden zich op/usr/share/java/jdbcdrivers.

Zie [Squirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots)voor meer informatie.

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Opdracht regel Programma's voor toegang tot Microsoft SQL Server

Het ODBC-stuur programmapakket voor SQL Server wordt ook geleverd met twee opdracht regel Programma's:

- **bcp**: het BCP-hulp programma kopieert gegevens tussen een exemplaar van Microsoft SQL Server en een gegevens bestand in een door de gebruiker opgegeven indeling. U kunt het hulp programma BCP gebruiken om grote aantallen nieuwe rijen te importeren in SQL Server tabellen, of om gegevens uit tabellen te exporteren naar gegevens bestanden. Als u gegevens wilt importeren in een tabel, moet u een indelings bestand gebruiken dat voor die tabel is gemaakt. Of u moet inzicht hebben in de structuur van de tabel en de typen gegevens die geldig zijn voor de kolommen.

  Zie [verbinding maken met BCP](/sql/connect/odbc/linux-mac/connecting-with-bcp)voor meer informatie.

- **Sqlcmd**: u kunt Transact-SQL-instructies opgeven met behulp van het hulp programma Sqlcmd. U kunt ook systeem procedures en script bestanden invoeren bij de opdracht prompt. Dit hulp programma gebruikt ODBC om Transact-SQL-batches uit te voeren.

  Zie [verbinding maken met Sqlcmd](/sql/connect/odbc/linux-mac/connecting-with-sqlcmd)voor meer informatie.

  > [!NOTE]
  > Er zijn enkele verschillen in dit hulp programma tussen Linux-en Windows-platforms. Raadpleeg de documentatie voor meer informatie.

### <a name="database-access-libraries"></a>Database toegangs bibliotheken

Bibliotheken zijn beschikbaar in R en python voor database toegang:

* In R kunt u het RODBC-pakket of het dplyr-pakket gebruiken om SQL-instructies op de database server op te vragen of uit te voeren.
* In python biedt de pyodbc-bibliotheek database toegang met ODBC als de onderliggende laag.  

## <a name="azure-tools"></a>Azure-hulpprogramma's

De volgende Azure-hulpprogram ma's zijn geïnstalleerd op de VM:

* **Azure cli**: u kunt de opdracht regel interface in azure gebruiken om Azure-resources te maken en te beheren via shell-opdrachten. Als u de Azure-hulpprogram ma's wilt openen, voert u **Azure Help** in. Zie de [Azure cli-documentatie pagina](/cli/azure/get-started-with-az-cli2)voor meer informatie.
* **Azure Storage Explorer**: Azure Storage Explorer is een grafisch hulp programma dat u kunt gebruiken om te bladeren door de objecten die u in uw Azure-opslag account hebt opgeslagen, en om gegevens van en naar Azure-blobs te uploaden en te downloaden. U hebt toegang tot Storage Explorer via het pictogram Bureaublad snelkoppeling. U kunt deze ook openen vanuit een shell-prompt door **StorageExplorer** in te voeren. U moet zijn aangemeld bij een X2Go-client of X11 door sturen hebben ingesteld.
* **Azure-bibliotheken**: Hier volgen enkele van de vooraf geïnstalleerde bibliotheken.
  
  * **Python**: de Azure-gerelateerde bibliotheken in python zijn *Azure*, *azureml*, *pydocumentdb* en *pyodbc*. Met de eerste drie bibliotheken kunt u toegang krijgen tot Azure Storage-services, Azure Machine Learning en Azure Cosmos DB (een NoSQL-data base in Azure). De vierde bibliotheek, pyodbc (samen met het micro soft ODBC-stuur programma voor SQL Server), biedt toegang tot SQL Server, Azure SQL Database en Azure Synapse Analytics van python met behulp van een ODBC-interface. Voer een **PIP-lijst** in om alle weer gegeven bibliotheken weer te geven. Zorg ervoor dat u deze opdracht uitvoert in zowel de python 2,7-als de 3,5-omgeving.
  * **R**: de Azure-gerelateerde bibliotheken in R zijn AZUREML en RODBC.
  * **Java**: de lijst met Azure Java-bibliotheken vindt u in de map/dsvm/SDK/AzureSDKJava op de virtuele machine. De belangrijkste bibliotheken zijn Azure-opslag-en beheer-Api's, Azure Cosmos DB en JDBC-Stuur Programma's voor SQL Server.  

U kunt de [Azure Portal](https://portal.azure.com) openen vanuit de vooraf geïnstalleerde Firefox-browser. Op de Azure Portal kunt u Azure-resources maken, beheren en bewaken.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning is een volledig beheerde Cloud service waarmee u predictive analytics oplossingen kunt bouwen, implementeren en delen. U kunt uw experimenten en modellen bouwen in Azure Machine Learning Studio (preview). U kunt deze openen vanuit een webbrowser op de Data Science Virtual Machine door [Microsoft Azure machine learning](https://ml.azure.com)te bezoeken.

Nadat u zich hebt aangemeld bij Azure Machine Learning Studio, kunt u een Experimenteer doek gebruiken om een logische stroom te maken voor de machine learning-algoritmen. U hebt ook toegang tot een Jupyter-notebook dat wordt gehost op Azure Machine Learning en probleemloos kan samen werken met de experimenten in Azure Machine Learning Studio. 

Operationeel maken de machine learning modellen die u hebt gemaakt door deze in een webservice-interface in te pakken. Met inzet machine learning-modellen kunnen clients die in elke taal zijn geschreven, voor spellingen van deze modellen aanroepen. Zie de [machine learning-documentatie](https://azure.microsoft.com/documentation/services/machine-learning/)voor meer informatie.

U kunt ook uw modellen bouwen in R of python op de virtuele machine en deze vervolgens implementeren in productie op Azure Machine Learning. Er zijn bibliotheken geïnstalleerd in R (**azureml**) en python (**azureml**) om deze functionaliteit in te scha kelen.

> [!NOTE]
> Deze instructies zijn geschreven voor de Windows-versie van de Data Science Virtual Machine. Maar de informatie over het implementeren van modellen naar Azure Machine Learning is van toepassing op de virtuele Linux-machine.

## <a name="machine-learning-tools"></a>Machine learning-hulpprogram ma's

De VM wordt geleverd met machine learning-hulpprogram ma's en-algoritmen die vooraf zijn gecompileerd en lokaal zijn geïnstalleerd. Deze omvatten:

* **Vowpal Wabbit**: een Fast Online Learning-algoritme.
* **xgboost**: een hulp programma dat geoptimaliseerde, gestimuleerde structuur algoritmen biedt.
* **Rattle**: een op R gebaseerd grafisch hulp programma voor het snel verkennen en model leren van gegevens.
* **Python**: Anaconda python wordt geleverd met machine learning-algoritmen met bibliotheken zoals Scikit. U kunt andere bibliotheken installeren met behulp van de `pip install` opdracht.
* **LightGBM**: een snelle, gedistribueerde, hoogwaardige kleur overgang voor het verbeteren van het Framework op basis van de beslissings structuur algoritmen.
* **R**: er is een uitgebreide bibliotheek met machine learning functies beschikbaar voor R. vooraf geïnstalleerde bibliotheken zijn LM, GLM, randomForest en rpart. U kunt andere bibliotheken installeren door deze opdracht uit te voeren:

    ```r
    install.packages(<lib name>)
    ```

Hier vindt u meer informatie over de eerste drie machine learning hulpprogram ma's in de lijst.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit is een machine learning systeem dat gebruikmaakt van technieken zoals online, hashing, allreduce, reducties, learning2search, actief en interactief leren.

Als u het hulp programma wilt uitvoeren op een eenvoudig voor beeld, gebruikt u de volgende opdrachten:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Er zijn andere, grotere demo's in de Directory. Zie  [deze sectie van github](https://github.com/JohnLangford/vowpal_wabbit) en de [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki)voor meer informatie over Vowpal Wabbit.

### <a name="xgboost"></a>xgboost

De xgboost-bibliotheek is ontworpen en geoptimaliseerd voor geboostte algoritmen (structuur). Het doel van deze bibliotheek is het pushen van de berekenings limieten van machines tot de Extremes die nodig zijn om grootschalige, schaal bare structuren te bieden die schaalbaar, draagbaar en nauw keurig zijn.

Deze wordt weer gegeven als een opdracht regel en een R-bibliotheek. Als u deze bibliotheek in R wilt gebruiken, kunt u een interactieve R-sessie starten (door **R** in de shell in te voeren) en de bibliotheek te laden.

Hier volgt een eenvoudig voor beeld dat u kunt uitvoeren in een R-prompt:

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

Als u de xgboost-opdracht regel wilt uitvoeren, kunt u de volgende opdrachten uitvoeren in de shell:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Een. model-bestand wordt geschreven naar de opgegeven map. Meer informatie over dit voor beeld van deze demo vindt u [op github](https://github.com/dmlc/xgboost/tree/master/demo/CLI/binary_classification).

Zie de [xgboost-documentatie pagina](https://xgboost.readthedocs.org/en/latest/) en de bijbehorende [github-opslag plaats](https://github.com/dmlc/xgboost)voor meer informatie over xgboost.

### <a name="rattle"></a>Rattle

Rattle (de **R** **A** analytische **t** Ool **t** o **L** verdienen **E** asily) maakt gebruik van op GUI gebaseerde gegevens exploratie en model lering. Het biedt statistische en visuele samen vattingen van gegevens, transformeert gegevens die gemakkelijk kunnen worden gemodelleerd, bouwt zowel niet-bewaakte als gecontroleerde modellen van de gegevens, geeft de prestaties van modellen grafisch en scoort nieuwe gegevens sets. Ook wordt er een R-code gegenereerd, waarbij de bewerkingen in de gebruikers interface worden gerepliceerd en die rechtstreeks kunnen worden uitgevoerd in R of worden gebruikt als uitgangs punt voor verdere analyse.

Als u Rattle wilt uitvoeren, moet u zich in een grafische sessie op het bureau blad bevindt. Voer op de Terminal **r** in om de R-omgeving te openen. Voer bij de R-prompt de volgende opdrachten in:

```R
library(rattle)
rattle()
```

Nu wordt een grafische interface geopend met een reeks tabbladen. Gebruik de volgende Snelstartgids-stappen in Rattle om een voor beeld weer te gegeven en een model te bouwen. In sommige stappen wordt u gevraagd om automatisch enkele vereiste R-pakketten te installeren en te laden die nog niet op het systeem zijn geïnstalleerd.

> [!NOTE]
> Als u geen toegang hebt om het pakket te installeren in de systeemmap (de standaard instelling), ziet u mogelijk een prompt op uw R-console venster om pakketten te installeren in uw persoonlijke bibliotheek. Antwoord **y** als u deze prompts ziet.

1. Selecteer **Uitvoeren**.
1. Er wordt een dialoog venster weer gegeven waarin u wordt gevraagd of u de voor beeld-weer gegevens set wilt gebruiken. Selecteer **Ja** om het voor beeld te laden.
1. Selecteer het tabblad **model** .
1. Selecteer **uitvoeren** om een beslissings structuur te maken.
1. Selecteer **tekenen** om de beslissings structuur weer te geven.
1. Selecteer de optie **forest** en selecteer **uitvoeren** om een wille keurig forest te bouwen.
1. Selecteer het tabblad **evalueren** .
1. Selecteer de optie **risico** en selecteer **uitvoeren** om twee **risico's (cumulatief)** prestatie grafieken weer te geven.
1. Selecteer het tabblad **logboek** om de gegenereerde R-code voor de voor gaande bewerkingen weer te geven.
   (Vanwege een fout in de huidige release van Rattle moet u een **#** teken invoegen vóór het **exporteren van dit logboek** in de tekst van het logboek.)
1. Selecteer de knop **exporteren** om het R-script bestand met de naam weather_script op te slaan *. R* naar de basismap.

U kunt Rattle en R afsluiten. Nu kunt u het gegenereerde R-script wijzigen. Of gebruik het script zoals het is en voer het op elk gewenst moment uit om alles te herhalen dat is uitgevoerd in de Rattle-gebruikers interface. Met name voor beginners in R is dit een manier om snel analyses en machine learning uit te voeren in een eenvoudige grafische interface, terwijl automatisch code wordt gegenereerd in R om te wijzigen of te leren.

## <a name="next-steps"></a>Volgende stappen

Hebt u meer vragen? Overweeg een [ondersteunings ticket](https://azure.microsoft.com/support/create-ticket/)te maken.