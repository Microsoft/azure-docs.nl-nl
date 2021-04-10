---
title: Inleiding tot ML Services in Azure HDInsight
description: Meer informatie over het gebruik van ML Services in HDInsight om toepassingen voor het analyseren van big data te maken.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 87f4181e820b1c6ecdeb0fda85a88e80db248dd2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98943925"
---
# <a name="what-is-ml-services-in-azure-hdinsight"></a>Wat is ML Services in Azure HDInsight?

Microsoft Machine Learning Server is beschikbaar als implementatieoptie wanneer u HDInsight-clusters maakt in Azure. Het clustertype dat deze optie biedt, wordt **ML Services** genoemd. Deze mogelijkheid biedt op aanvraag toegang tot aanpasbare, gedistribueerde analysemethoden in HDInsight.

ML Services op HDInsight biedt de nieuwste mogelijkheden voor R-gebaseerde analyses van gegevenssets van vrijwel elke grootte. De gegevenssets kunnen worden geladen in Azure Blob of Data Lake Storage. Uw R-toepassingen kunnen gebruikmaken van de 8000 + R-pakketten met open source. De routines in ScaleR, het Microsoft-pakket voor het analyseren van big data, zijn ook beschikbaar.

Het edge-knooppunt biedt een handige plaats om verbinding te maken met het cluster en om uw R-scripts uit te voeren. Met het edge-knooppunt kunt u de geparallelliseerde gedistribueerde functies van ScaleR uitvoeren in de kernen van de server. U kunt ze ook uitvoeren op de knooppunten van het cluster door Hadoop Map Reduce van ScaleR te gebruiken. U kunt ook de compute-contexten van Apache Spark gebruiken.

De modellen of voorspellingen die voortkomen uit de analyses, kunnen worden gedownload voor on-premises gebruik. Ze kunnen ook elders in Azure worden `operationalized`. Met name via [Azure Machine Learning Studio (klassiek)](https://studio.azureml.net) en de [Azure Machine Learning-webservice](../../machine-learning/classic/deploy-a-machine-learning-web-service.md).

## <a name="get-started-with-ml-services-on-hdinsight"></a>Aan de slag met ML Services in HDInsight

Als u een ML Services-cluster in HDInsight wilt maken, selecteert u het clustertype **ML Services**. Het clustertype ML Services bevat ML Server op de gegevensknooppunten en het edge-knooppunt. Het edge-knooppunt fungeert als een landingszone voor analyses op basis van ML Services. Zie [Apache Hadoop-clusters maken met behulp van Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) voor meer informatie over hoe u het cluster maakt.

## <a name="why-choose-ml-services-in-hdinsight"></a>Waarom ML Services kiezen in HDInsight?

ML Services in HDInsight biedt de volgende belangrijke voordelen:

### <a name="ai-innovation-from-microsoft-and-open-source"></a>AI-innovatie van Microsoft en open source

  ML Services bevat een uiterst aanpasbare, gedistribueerde set algoritmen, zoals [RevoscaleR](/machine-learning-server/r-reference/revoscaler/revoscaler), [revoscalepy](/machine-learning-server/python-reference/revoscalepy/revoscalepy-package) en [microsoftML](/machine-learning-server/python-reference/microsoftml/microsoftml-package). Deze algoritmen kunnen worden gebruikt voor gegevensgrootten die groter zijn dan de grootte van het fysieke geheugen. Ze kunnen ook op een gedistribueerde manier worden uitgevoerd op een groot aantal verschillende platformen. Meer informatie over de verzameling aangepaste [R-pakketten](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) van Microsoft en [Python-pakketten](/machine-learning-server/python-reference/introducing-python-package-reference) die zijn opgenomen in het product.
  
  ML Services slaat een brug tussen de innovaties van Microsoft en de bijdragen van de opensource-community (R-, Python- en AI-toolkits). Alles op één platform op bedrijfsniveau. Alle open-source machine learning-pakketten van R of Python kunnen worden gebruikt in combinatie met de innovaties van Microsoft.

### <a name="simple-secure-and-high-scale-operationalization-and-administration"></a>Eenvoudige, veilige en grootschalige uitvoering en beheer

  Ondernemingen die gebruikmaken van traditionele paradigma's en omgevingen, investeren veel tijd en energie in de uitvoering. Deze actie resulteert in aanzienlijke kosten en vertragingen, met inbegrip van de vertaaltijd voor: modellen, iteraties om ze geldig en actueel te houden, wettelijke goedkeuring en het beheren van machtigingen.

  ML Services biedt een [uitvoering](/machine-learning-server/what-is-operationalization) op bedrijfsniveau. Nadat een Machine Learning-model is voltooid, kunt u met slechts enkele klikken API's voor webservices genereren. Deze [webservices](/machine-learning-server/operationalize/concept-what-are-web-services) worden gehost op een serverraster in de cloud en kunnen worden geïntegreerd met Line-Of-Business-apps. Dankzij de mogelijkheid om naar een elastisch raster te implementeren, kunt u naadloos schalen met de behoeften van uw bedrijf, zowel voor batch- als realtimescoring. Zie [ML Services in HDInsight uitvoeren](r-server-operationalize.md) voor instructies.

<!---
* **Deep ecosystem engagements to deliver customer success with optimal total cost of ownership**

  Individuals embarking on the journey of making their applications intelligent or simply wanting to learn the new world of AI and machine learning, need the right resources to help them get started. In addition to this documentation, Microsoft provides several learning resources and has engaged several training partners to help you ramp up and become productive quickly.
--->

> [!NOTE]  
> Het ML Services-clustertype in HDInsight wordt alleen ondersteund in HDInsight 3.6. HDInsight 3.6 wordt op 31 december 2020 buiten gebruik gesteld.

## <a name="key-features-of-ml-services-on-hdinsight"></a>Belangrijke functies van ML Services in HDInsight

De volgende functies zijn opgenomen in ML Services in HDInsight.

| Functiecategorie | Beschrijving |
|------------------|-------------|
| Geschikt voor R | [R-pakketten](/machine-learning-server/r-reference/introducing-r-server-r-package-reference) voor oplossingen die zijn geschreven in R, met een opensource-distributie van R en een runtime-infrastructuur voor het uitvoeren van scripts. |
| Geschikt voor Python | [Python-modules](/machine-learning-server/python-reference/introducing-python-package-reference) voor oplossingen die zijn geschreven in Python, met een opensource-distributie van Python en een runtime-infrastructuur voor het uitvoeren van scripts.
| [Vooraf getrainde modellen](/machine-learning-server/install/microsoftml-install-pretrained-models) | Voor een visuele analyse en sentimentanalyse van tekst, klaar voor het scoren van de gegevens die u opgeeft. |
| [Implementeren en gebruiken](r-server-operationalize.md) | `Operationalize` uw server en implementeer oplossingen als een webservice. |
| [Externe uitvoering](r-server-hdinsight-manage.md#connect-remotely-to-microsoft-ml-services) | Start externe sessies op het ML Services-cluster op uw netwerk vanaf uw clientwerkstation. |

## <a name="data-storage-options-for-ml-services-on-hdinsight"></a>Gegevensopslagopties voor ML Services in HDInsight

De standaardopslag voor het HDFS-bestandssysteem kan een Azure Storage-account of Azure Data Lake Storage zijn. De gegevens die tijdens de analyse worden geüpload naar de clusteropslag, worden permanent gemaakt. De gegevens zijn zelfs nog beschikbaar nadat het cluster is verwijderd. Er zijn verschillende hulpprogramma's die de gegevensoverdracht naar de opslag kunnen verwerken. De hulpprogramma's omvatten de op de portal gebaseerde uploadvoorziening van het opslagaccount en het hulpprogramma AzCopy.

U kunt toegang tot extra Blob- en Data Lake-archieven inschakelen tijdens het maken van het cluster. U bent niet beperkt tot de primaire opslagoptie die wordt gebruikt.  Zie het artikel [Azure Storage-opties voor ML Services in HDInsight](./r-server-storage.md) voor meer informatie over het gebruik van meerdere opslagaccounts.

U kunt Azure Files als opslagoptie gebruiken op het edge-knooppunt. Met Azure Files is het mogelijk bestandsshares die zijn gemaakt in Azure Storage, over te dragen naar het Linux-bestandssysteem. Zie [Azure Storage-opties voor ML Services in HDInsight](r-server-storage.md) voor meer informatie.

## <a name="access-ml-services-edge-node"></a>Toegang tot het edge-knooppunt van ML Services

U kunt verbinding maken met Microsoft ML Server op het edge-knooppunt met behulp van een browser of SSH/PuTTy. De R-console wordt standaard geïnstalleerd tijdens het maken van het cluster.  

## <a name="develop-and-run-r-scripts"></a>R-scripts ontwikkelen en uitvoeren

Uw R-scripts kunnen elk van de 8000+ R-pakketten met open source gebruiken. U kunt ook de geparallelliseerde en gedistribueerde routines uit de ScaleR-bibliotheek gebruiken. Scripts die worden uitgevoerd op het edge-knooppunt worden uitgevoerd in de R-interpreter op dat knooppunt. Behalve voor stappen waarin ScaleR-functies worden aangeroepen met een compute-context van Map Reduce (RxHadoopMR) of Spark (RxSpark). De functies worden op een gedistribueerde manier uitgevoerd voor de gegevensknooppunten die aan de gegevens zijn gekoppeld. Zie [Opties voor compute-context voor ML Services op HDInsight](r-server-compute-contexts.md) voor meer informatie over contextopties.

## <a name="operationalize-a-model"></a>Een model `Operationalize`

Als uw gegevensmodellering is voltooid, `operationalize` u het model om voorspellingen voor nieuwe on-premises of Azure-gegevens te maken. Dit proces wordt ook wel scoren genoemd. U kunt in HDInsight, in Azure Machine Learning of on-premises scoren.

### <a name="score-in-hdinsight"></a>Scoren in HDInsight

Als u wilt scoren in HDInsight, schrijft u een R-functie. De functie roept uw model aan om voorspellingen te maken voor een nieuw gegevensbestand dat u hebt geüpload naar uw opslagaccount. Sla de voorspellingen vervolgens weer op in het opslagaccount. U kunt deze routine op aanvraag uitvoeren op het edge-knooppunt van uw cluster of met behulp van een geplande taak.

### <a name="score-in-azure-machine-learning-aml"></a>Scoren in Azure Machine Learning (AML)

Als u wilt scoren met behulp van Azure Machine Learning, gebruikt u het open-source R-pakket van Azure Machine Learning, ook wel [AzureML](https://cran.r-project.org/src/contrib/Archive/AzureML/) genoemd, om uw model als een Azure-webservice te publiceren. Voor het gemak is dit pakket vooraf geïnstalleerd op het edge-knooppunt. Gebruik vervolgens de functies in Azure Machine Learning om een gebruikersinterface voor de webservice te maken en roep vervolgens de webservice aan die nodig is om te scoren. Converteer ScaleR-modelobjecten vervolgens naar gelijkwaardige opensource-modelobjecten voor gebruik met de webservice. Gebruik hiervoor ScaleR-functies voor cast-conversie, zoals `as.randomForest()` voor op ensemble gebaseerde modellen.

### <a name="score-on-premises"></a>On-premises scoren

Als u on-premises wilt scoren nadat u uw model hebt gemaakt, serialiseert u het model in R. Vervolgens downloadt en deserialiseert u het model zodat u het kunt gebruiken om nieuwe gegevens te scoren. U kunt nieuwe gegevens scoren door de benadering te gebruiken die eerder is beschreven in Scoren in HDInsight, of door [webservices](/machine-learning-server/operationalize/concept-what-are-web-services) te gebruiken.

## <a name="maintain-the-cluster"></a>Het cluster onderhouden

### <a name="install-and-maintain-r-packages"></a>R-pakketten installeren en onderhouden

De meeste R-pakketten die u gebruikt, zijn vereist op het edge-knooppunt, omdat de meeste stappen van uw R-scripts hier worden uitgevoerd. Als u aanvullende R-pakketten op het edge-knooppunt wilt installeren, kunt u de methode `install.packages()` in R gebruiken.

Als u alleen ScaleR-bibliotheekroutines gebruikt, hebt u doorgaans geen extra R-pakketten nodig. Mogelijk hebt u aanvullende pakketten nodig voor **rxExec**- of **RxDataStep**-uitvoering op de gegevensknooppunten.

De extra pakketten kunnen worden geïnstalleerd met een scriptactie nadat u het cluster hebt gemaakt. Zie [ML Services in HDInsight-cluster beheren](r-server-hdinsight-manage.md) voor meer informatie.

### <a name="change-apache-hadoop-mapreduce-memory-settings"></a>Geheugeninstellingen wijzigen voor Apache Hadoop MapReduce

Het beschikbare geheugen voor ML Services kan worden gewijzigd wanneer er een MapReduce-taak wordt uitgevoerd. Als u een cluster wilt aanpassen, gebruikt u de gebruikersinterface van Apache Ambari voor uw cluster. Zie [HDInsight-clusters beheren met behulp van de webinterface van Ambari](../hdinsight-hadoop-manage-ambari.md) voor instructies voor het gebruik van de gebruikersinterface van Ambari.

Het beschikbare geheugen voor ML Services kan worden gewijzigd door Hadoop-schakelopties in de aanroep naar **RxHadoopMR** te gebruiken:

```r
hadoopSwitches = "-libjars /etc/hadoop/conf -Dmapred.job.map.memory.mb=6656"
```

### <a name="scale-your-cluster"></a>Uw cluster schalen

Een bestaand ML Services-cluster in HDInsight kan omhoog of omlaag worden geschaald via de portal. Door op te schalen krijgt u extra capaciteit voor grotere verwerkingstaken. U kunt een cluster weer afschalen wanneer deze niet actief is. Zie [HDInsight-clusters beheren](../hdinsight-administer-use-portal-linux.md) voor instructies over het schalen van een cluster.

### <a name="maintain-the-system"></a>Het systeem onderhouden

Het onderhoud van het besturingssysteem wordt buiten kantooruren uitgevoerd op de onderliggende virtuele Linux-machines in een HDInsight-cluster. Doorgaans worden de onderhoudswerkzaamheden elke maandag en donderdag 's nachts om 3:30 uur (lokale tijd van de VM) uitgevoerd. Updates zijn niet van invloed op meer dan een kwart van het cluster tegelijk.

Wanneer er onderhoud wordt uitgevoerd, is het mogelijk dat taken langzamer worden uitgevoerd. Ze worden wel gewoon volledig uitgevoerd. Alle aangepaste software of lokale gegevens die u hebt, blijven bewaard tijdens deze onderhoudsgebeurtenissen, tenzij er een onherstelbare fout optreedt waardoor een cluster opnieuw moet worden gebouwd.

## <a name="ide-options-for-ml-services-on-hdinsight"></a>IDE-opties voor ML Services in HDInsight

Het Linux-edge-knooppunt van een HDInsight-cluster is de landingszone voor analyses op basis van R. Recente versies van HDInsight bieden een op een browser gebaseerde IDE van RStudio Server op het edge-knooppunt. RStudio Server is voor de ontwikkeling en uitvoering productiever dan de R-console.

Een desktop-IDE kan toegang krijgen tot het cluster via een externe compute-context van MapReduce of Spark. Een aantal opties: [R Tools for Visual Studio](https://marketplace.visualstudio.com/items?itemName=MikhailArkhipov007.RTVS2019) (RTVS) van Microsoft, RStudio en Eclipse-gebaseerde StatET van Walware.

Open de R-console op het edge-knooppunt door **R** achter de opdrachtprompt te typen. Als u de console-interface gebruikt, is het handig om R-script in een teksteditor te ontwikkelen. Vervolgens kunt u eventueel delen van het script knippen en in uw R-console plakken.

## <a name="pricing"></a>Prijzen

De prijzen voor een ML Services HDInsight-cluster zijn op ongeveer dezelfde wijze gestructureerd als voor andere HDInsight-clustertypes. Ze zijn gebaseerd op de grootte van de onderliggende VM's voor de naam-, gegevens- en edge-knooppunten. Ook het aantal core-uren. Zie [Prijsdetails voor Azure HDInsight ](https://azure.microsoft.com/pricing/details/hdinsight/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over het gebruik van ML Services op HDInsight-clusters:

* [Een R-script met behulp van RStudio Server uitvoeren op een ML Services-cluster in Azure HDInsight](machine-learning-services-quickstart-job-rstudio.md)
* [Opties voor compute-context voor ML Services-cluster in HDInsight](r-server-compute-contexts.md)
* [Opslagopties voor ML Services-cluster in HDInsight](r-server-storage.md)