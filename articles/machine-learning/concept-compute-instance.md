---
title: Wat is een Azure Machine Learning-rekeninstantie?
titleSuffix: Azure Machine Learning
description: Meer informatie over Azure Machine Learning compute-exemplaar, een volledig beheerd cloudwerkstation.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 10/02/2020
ms.openlocfilehash: e540bdd0502db98dbbd6ddc5a60ca8a644fbc23d
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107886150"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Wat is een Azure Machine Learning-rekeninstantie?

Een Azure Machine Learning compute-instantie is een beheerd cloudwerkstation voor gegevenswetenschappers.

Met reken-exemplaren kunt u eenvoudig aan de slag met Azure Machine Learning en beheer- en enterprise-gereedheidsmogelijkheden bieden voor IT-beheerders.  

Gebruik een reken-exemplaar als uw volledig geconfigureerde en beheerde ontwikkelomgeving in de cloud voor machine learning. Ze kunnen ook worden gebruikt als een rekendoel voor training en deferencing voor ontwikkelings- en testdoeleinden.  

Gebruik voor modeltraining op productiekwaliteit een [Azure Machine Learning rekencluster](how-to-create-attach-compute-cluster.md) met schaalmogelijkheden voor meerdere knooppunt. Gebruik voor de implementatie van het [productiemodel Azure Kubernetes Service cluster](how-to-deploy-azure-kubernetes-service.md).

Zorg ervoor dat websockockecommunicatie niet is uitgeschakeld om de Jupyter-functionaliteit van het reken exemplaar te laten werken. Zorg ervoor dat uw netwerk websocket-verbindingen met *.instances.azureml.net en *.instances.azureml.ms.

## <a name="why-use-a-compute-instance"></a>Waarom een reken-exemplaar gebruiken?

Een reken-exemplaar is een volledig beheerd cloudwerkstation dat is geoptimaliseerd voor machine learning ontwikkelomgeving. Dit biedt de volgende voordelen:

|Belangrijkste voordelen|Description|
|----|----|
|Productiviteit|U kunt modellen bouwen en implementeren met behulp van geïntegreerde notebooks en de volgende hulpprogramma's in Azure Machine Learning-studio:<br/>- Jupyter<br/>- JupyterLab<br/>- VS Code (preview)<br/>- RStudio (preview)<br/>De reken-instantie is volledig geïntegreerd met Azure Machine Learning werkruimte en studio. U kunt notebooks en gegevens delen met andere gegevenswetenschappers in de werkruimte.<br/> U kunt VS [Code ook gebruiken met](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) reken-exemplaren.
|Beheerde & beveiligd|Verklein uw beveiligingsvoetafdruk en voeg naleving toe aan de beveiligingsvereisten van ondernemingen. Reken-exemplaren bieden robuust beheerbeleid en beveiligde netwerkconfiguraties, zoals:<br/><br/>- Automatisch inbouwen vanuit Resource Manager-sjablonen of Azure Machine Learning SDK<br/>- [Op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC)](../role-based-access-control/overview.md)<br/>- [Ondersteuning voor virtueel netwerk](./how-to-secure-training-vnet.md#compute-instance)<br/>- SSH-beleid voor het in-/uitschakelen van SSH-toegang<br/>TLS 1.2 ingeschakeld |
|Vooraf geconfigureerd &nbsp; voor &nbsp; ML|Bespaar tijd op installatietaken met vooraf geconfigureerde en bijgewerkte ML-pakketten, deep learning-frameworks, GPU-stuurprogramma's.|
|Volledig aanpasbaar|Brede ondersteuning voor Azure-VM-typen, waaronder GPU's en persistente aanpassingen op laag niveau, zoals het installeren van pakketten en stuurprogramma's, maakt geavanceerde scenario's eenvoudig. |

U kunt [zelf een reken-exemplaar](how-to-create-manage-compute-instance.md?tabs=python#create) maken of een beheerder kan [een reken-exemplaar voor u maken.](how-to-create-manage-compute-instance.md?tabs=python#create-on-behalf-of-preview)

## <a name="tools-and-environments"></a><a name="contents"></a>Hulpprogramma's en omgevingen

> [!IMPORTANT]
> Items die in dit artikel zijn gemarkeerd (preview) zijn momenteel beschikbaar als openbare preview.
> De preview-versie wordt aangeboden zonder Service Level Agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Azure Machine Learning compute-exemplaar kunt u modellen maken, trainen en implementeren in een volledig geïntegreerde notebookervaring in uw werkruimte.

U kunt Jupyter-notebooks uitvoeren in [VS Code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) met behulp van een reken-exemplaar als de externe server, zonder dat er SSH nodig is. U kunt VS Code-integratie ook inschakelen via de [externe SSH-extensie](https://devblogs.microsoft.com/python/enhance-your-azure-machine-learning-experience-with-the-vs-code-extension/).

U kunt [pakketten installeren en kernels](how-to-access-terminal.md#install-packages) [toevoegen aan uw](how-to-access-terminal.md#add-new-kernels) reken-exemplaar.  

De volgende hulpprogramma's en omgevingen zijn al geïnstalleerd op het reken-exemplaar: 

|Algemene hulpprogramma's & omgevingen|Details|
|----|:----:|
|Stuurprogramma's|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI-bibliotheek||
|Azure CLI ||
|Azure Machine Learning voorbeelden ||
|Docker||
|Nginx||
|NCCL 2.0 ||
|Protobuf|| 

|**R-hulpprogramma'&** omgevingen|Details|
|----|:----:|
|RStudio Server Open Source Edition (preview)||
|R-kernel||
|Azure Machine Learning SDK voor R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK steekproeven|

|**PYTHON-hulpprogramma'&** omgevingen|Details|
|----|----|
|Anaconda Python||
|Jupyter en extensies||
|Jupyterlab en extensies||
[Azure Machine Learning-SDK voor Python](/python/api/overview/azure/ml/intro)</br>van PyPI|Bevat de meeste extra azureml-pakketten.  Als u de volledige lijst wilt zien, [opent u een terminalvenster op uw reken-exemplaar en](how-to-access-terminal.md) voer u uit <br/> `conda list -n azureml_py36 azureml*` |
|Andere PyPI-pakketten|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda-pakketten|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Deep Learning-pakketten|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX-pakketten|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning Python & R SDK-voorbeelden||

Python-pakketten worden allemaal geïnstalleerd in de **Python 3.6 - AzureML-omgeving.**  

## <a name="accessing-files"></a>Toegang tot bestanden

Notebooks en R-scripts worden opgeslagen in het standaardopslagaccount van uw werkruimte in de Azure-bestands share.  Deze bestanden bevinden zich in de map Gebruikersbestanden. Met deze opslag kunt u eenvoudig notebooks delen tussen reken-exemplaren. Het opslagaccount bewaart uw notebooks ook veilig wanneer u een reken-exemplaar stopt of verwijdert.

Het Azure-bestands shareaccount van uw werkruimte wordt als een station aan de reken-instantie bevestigd. Dit station is de standaarddirectory voor Jupyter, Jupyter Labs en RStudio. Dit betekent dat de notebooks en andere bestanden die u maakt in Jupyter, JupyterLab of RStudio automatisch worden opgeslagen op de bestands share en beschikbaar zijn voor gebruik in andere reken-exemplaren.

De bestanden in de bestands share zijn toegankelijk vanaf alle reken-exemplaren in dezelfde werkruimte. Eventuele wijzigingen in deze bestanden op het reken-exemplaar worden betrouwbaar terug naar de bestands share opgeslagen.

U kunt ook de meest recente voorbeelden Azure Machine Learning naar uw map onder de map gebruikersbestanden in de bestands share van de werkruimte.

Het schrijven van kleine bestanden kan langzamer zijn op netwerkstations dan het schrijven naar de lokale schijf van het reken exemplaar.  Als u veel kleine bestanden schrijft, kunt u proberen een map rechtstreeks op het reken exemplaar te gebruiken, zoals een `/tmp` map. Houd er rekening mee dat deze bestanden niet toegankelijk zijn vanaf andere reken-exemplaren. 

U kunt de `/tmp` map op de reken-instantie gebruiken voor uw tijdelijke gegevens.  Schrijf echter geen grote bestanden met gegevens op de besturingssysteemschijf van de reken-instantie.  Gebruik [in plaats daarvan gegevensstores.](concept-azure-machine-learning-architecture.md#datasets-and-datastores) Als u de JupyterLab-git-extensie hebt geïnstalleerd, kan dit ook leiden tot vertraging in de prestaties van rekenexe exemplaren.

## <a name="managing-a-compute-instance"></a>Een reken-exemplaar beheren

Selecteer in uw werkruimte in Azure Machine Learning-studio **compute** en selecteer vervolgens **Rekenin exemplaar** bovenaan.

![Een reken-exemplaar beheren](./media/concept-compute-instance/manage-compute-instance.png)

U kunt de volgende acties uitvoeren:

* [Maak een reken-exemplaar.](#create) 
* Vernieuw het tabblad Reken-exemplaren.
* Een reken-exemplaar starten, stoppen en opnieuw starten.  U betaalt voor het exemplaar wanneer deze wordt uitgevoerd. Stop de reken-instantie wanneer u deze niet gebruikt om de kosten te verlagen. Als u een reken-exemplaar stopt, wordt de toewijzing ervan weer in de weg gerekend. Start deze opnieuw wanneer u deze nodig hebt. Houd er rekening mee dat het stoppen van de berekenings-instantie de facturering voor rekenuren stopt, maar u wordt nog steeds gefactureerd voor schijf-, openbare IP- en load balancer.
* Een reken-exemplaar verwijderen.
* Filter de lijst met reken-exemplaren om alleen de exemplaren weer te geven die u hebt gemaakt.

Voor elk reken exemplaar in uw werkruimte dat u kunt gebruiken, kunt u het volgende doen:

* Toegang tot Jupyter, JupyterLab, RStudio op de reken-instantie
* SSH in reken-exemplaar. SSH-toegang is standaard uitgeschakeld, maar kan worden ingeschakeld tijdens het maken van het rekenproces. SSH-toegang is via een mechanisme voor openbare/persoonlijke sleutels. Op het tabblad vindt u details voor de SSH-verbinding, zoals IP-adres, gebruikersnaam en poortnummer.
* Meer informatie over een specifiek reken exemplaar, zoals IP-adres en regio.

[Met Azure RBAC](../role-based-access-control/overview.md) kunt u bepalen welke gebruikers in de werkruimte een reken-exemplaar kunnen maken, verwijderen, starten, stoppen en opnieuw starten. Alle gebruikers in de rol Inzender en Eigenaar van de werkruimte kunnen reken-exemplaren in de werkruimte maken, verwijderen, starten, stoppen en opnieuw starten. Alleen de maker van een specifiek reken exemplaar, of de gebruiker die is toegewezen als deze namens hen is gemaakt, heeft echter toegang tot Jupyter, JupyterLab en RStudio op dat reken exemplaar. Een reken-exemplaar is toegewezen aan één gebruiker met hoofdtoegang en kan worden geterminal via Jupyter/JupyterLab/RStudio. Het rekenproces heeft een aanmeldgegevens voor één gebruiker en alle acties gebruiken de identiteit van die gebruiker voor Azure RBAC en de toewijzing van experiment-runs. SSH-toegang wordt beheerd via een mechanisme voor openbare/persoonlijke sleutels.

Deze acties kunnen worden beheerd door Azure RBAC:
* *Microsoft.MachineLearningServices/workspaces/computes/read*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/computes/delete*
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*

Als u een reken-exemplaar wilt maken, moet u machtigingen hebben voor de volgende acties:
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/checkComputeNameAvailability/action*


### <a name="create-a-compute-instance"></a><a name="create"></a>Een rekenproces maken

Maak in uw werkruimte in [](how-to-create-attach-compute-studio.md#compute-instance) Azure Machine Learning-studio een nieuw reken-exemplaar vanuit de sectie **Compute** of in de sectie Notebooks wanneer u klaar bent om een van uw **notebooks** uit te voeren. 

U kunt ook een exemplaar maken
* Rechtstreeks vanuit de [geïntegreerde notebookervaring](tutorial-1st-experiment-sdk-setup.md#azure)
* In Azure Portal
* Vanuit Azure Resource Manager sjabloon. Zie voor een voorbeeldsjabloon [de sjabloon een Azure Machine Learning reken-exemplaar maken.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance)
* Met [Azure Machine Learning SDK](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/concept-compute-instance.md)
* Vanuit de [CLI-extensie voor Azure Machine Learning](reference-azure-machine-learning-cli.md#computeinstance)

De toegewezen kernen per regio per VM-familiequotum en het totale regionale quotum, dat van toepassing is op het maken van een rekenproces, worden samengevoegd en gedeeld met Azure Machine Learning training van rekenclusterquota. Als u de reken-instantie stopt, wordt er geen quotum uitgebracht om ervoor te zorgen dat u de reken-instantie opnieuw kunt starten.


### <a name="create-on-behalf-of-preview"></a>Maken namens (preview)

Als beheerder kunt u namens een data scientist een reken-exemplaar maken en het exemplaar aan hen toewijzen met:
* [Azure Resource Manager sjabloon .](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance)  Zie Id's van [identiteitsobjecten](../healthcare-apis/fhir/find-identity-object-ids.md)zoeken voor verificatieconfiguratie voor meer informatie over het vinden van de TenantID en ObjectID die nodig zijn in deze sjabloon.  U kunt deze waarden ook vinden in Azure Active Directory portal.
* REST-API

De data scientist voor wie u de reken-instantie maakt, heeft de volgende Azure RBAC-machtigingen nodig: 
* *Microsoft.MachineLearningServices/workspaces/computes/start/action*
* *Microsoft.MachineLearningServices/workspaces/computes/stop/action*
* *Microsoft.MachineLearningServices/workspaces/computes/restart/action*
* *Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action*

De data scientist kan de reken-instantie starten, stoppen en opnieuw starten. Ze kunnen de reken-instantie gebruiken voor:
* Jupyter
* JupyterLab
* RStudio
* Geïntegreerde notebooks

## <a name="compute-target"></a>Rekendoel

Reken-exemplaren kunnen worden gebruikt als een [trainingsrekendoel](concept-compute-target.md#train) dat vergelijkbaar is Azure Machine Learning rekentrainingsclusters. 

Een reken-exemplaar:
* Heeft een taakwachtrij.
* Taken worden veilig uitgevoerd in een virtuele netwerkomgeving, zonder dat ondernemingen de SSH-poort moeten openen. De taak wordt uitgevoerd in een containeromgeving en verpakt uw modelafhankelijkheden in een Docker-container.
* Kan meerdere kleine taken parallel uitvoeren (preview).  Twee taken per kern kunnen parallel worden uitgevoerd terwijl de rest van de taken in de wachtrij staan.
* Ondersteunt gedistribueerde trainingstaken met meerdere GPU's met één knooppunt

U kunt het rekenincident gebruiken als lokaal deferencing-implementatiedoel voor test-/foutopsporingsscenario's.

> [!TIP]
> Het reken exemplaar heeft een besturingssysteemschijf van 120 GB. Als u geen schijfruimte meer hebt, gebruikt u de [terminal](how-to-access-terminal.md) om ten minste 1-2 GB te leeg te maken voordat u de [reken-instantie](how-to-create-manage-compute-instance.md#manage) stopt of opnieuw start.


## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Wat is er gebeurd met notebook-VM?

Reken-exemplaren vervangen de notebook-VM.  

Notebook-bestanden die zijn opgeslagen in de bestands share van de werkruimte en gegevens in gegevensopslag van de werkruimte, zijn toegankelijk vanaf een reken-exemplaar. Aangepaste pakketten die eerder op een notebook-VM zijn geïnstalleerd, moeten echter opnieuw worden geïnstalleerd op het reken-exemplaar. Quotumbeperkingen die van toepassing zijn op het maken van rekenclusters, zijn ook van toepassing op het maken van rekenprocessen.

Er kunnen geen nieuwe notebook-VM's worden gemaakt. U kunt echter nog steeds notebook-VM's openen en gebruiken die u hebt gemaakt, met volledige functionaliteit. Reken-exemplaren kunnen worden gemaakt in dezelfde werkruimte als de bestaande notebook-VM's.


## <a name="next-steps"></a>Volgende stappen

* [Een reken-exemplaar maken en beheren](how-to-create-manage-compute-instance.md)
* [Zelfstudie: Uw eerste ML-model trainen](tutorial-1st-experiment-sdk-train.md) laat zien hoe u een reken-exemplaar gebruikt met een geïntegreerd notebook.
