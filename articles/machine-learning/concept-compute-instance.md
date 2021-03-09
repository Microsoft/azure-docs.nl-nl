---
title: Wat is een Azure Machine Learning-rekeninstantie?
titleSuffix: Azure Machine Learning
description: Meer informatie over de Azure Machine Learning Compute-instantie, een volledig beheerd werk station in de Cloud.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 10/02/2020
ms.openlocfilehash: e4f6db9e462f937d44bc0e1dbc2d3c4d3e915ba3
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102503637"
---
# <a name="what-is-an-azure-machine-learning-compute-instance"></a>Wat is een Azure Machine Learning-rekeninstantie?

Een Azure Machine Learning Compute-instantie is een beheerd werk station in de Cloud voor gegevens wetenschappers.

Reken instanties maken het eenvoudig om aan de slag te gaan met Azure Machine Learning-ontwikkeling en bieden mogelijkheden voor het beheer en de bedrijfs voorbereiding voor IT-beheerders.  

Gebruik een reken instantie als uw volledig geconfigureerde en beheerde ontwikkel omgeving in de Cloud voor machine learning. Ze kunnen ook worden gebruikt als een reken doel voor training en demijnen voor ontwikkelings-en test doeleinden.  

Gebruik een [Azure machine learning Compute-Cluster](how-to-create-attach-compute-cluster.md) met mogelijkheden voor schalen op meerdere knoop punten voor de training van productie-kwaliteits modellen. Gebruik [Azure Kubernetes service-cluster](how-to-deploy-azure-kubernetes-service.md)voor productie kwaliteit van het model.

Zorg ervoor dat de communicatie tussen websockets niet is uitgeschakeld voor de Jupyter-functionaliteit van reken instanties. Zorg ervoor dat uw netwerk WebSocket-verbindingen toestaat naar *. instances.azureml.net en *. instances.azureml.ms.

## <a name="why-use-a-compute-instance"></a>Waarom een reken instantie gebruiken?

Een reken instantie is een volledig beheerd, op de cloud gebaseerd werk station dat is geoptimaliseerd voor uw machine learning-ontwikkel omgeving. Dit biedt de volgende voordelen:

|Belangrijkste voordelen|Beschrijving|
|----|----|
|Productiviteit|U kunt modellen bouwen en implementeren met behulp van geïntegreerde notebooks en de volgende hulpprogram ma's in Azure Machine Learning studio:<br/>-Jupyter<br/>-Jjupyterlab<br/>-RStudio (preview-versie)<br/>Reken instantie is volledig geïntegreerd met Azure Machine Learning werk ruimte en Studio. U kunt notitie blokken en gegevens delen met andere gegevens wetenschappers in de werk ruimte.<br/> U kunt ook [VS code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) gebruiken met reken instanties.
|Beheerde & beveiligd|Verminder uw beveiligings footprint en voeg naleving toe met beveiligings vereisten voor ondernemingen. Reken instanties bieden robuust beheer beleid en beveiligde netwerk configuraties zoals:<br/><br/>-Autoinrichting van Resource Manager-sjablonen of Azure Machine Learning SDK<br/>- [Op rollen gebaseerd toegangs beheer op basis van Azure (Azure RBAC)](../role-based-access-control/overview.md)<br/>- [Ondersteuning voor virtuele netwerken](./how-to-secure-training-vnet.md#compute-instance)<br/>-SSH-beleid voor het inschakelen/uitschakelen van SSH-toegang<br/>TLS 1,2 ingeschakeld |
|Vooraf geconfigureerd &nbsp; voor &nbsp; ml|Bespaar tijd bij het instellen van taken met vooraf geconfigureerde en up-to-date ML-pakketten, diepe leer frameworks, GPU-Stuur Programma's.|
|Volledig aanpasbaar|Uitgebreide ondersteuning voor Azure VM-typen, waaronder Gpu's en persistente aanpassing op laag niveau, zoals het installeren van pakketten en stuur Programma's, maakt een koud probleem van geavanceerde scenario's. |

U kunt zelf [een reken instantie maken](how-to-create-manage-compute-instance.md?tabs=python#create) , maar een beheerder kan [een reken instantie voor u maken](how-to-create-manage-compute-instance.md?tabs=python#create-on-behalf-of-preview).

## <a name="tools-and-environments"></a><a name="contents"></a>Hulpprogram ma's en omgevingen

> [!IMPORTANT]
> Items die zijn gemarkeerd (preview) in dit artikel zijn momenteel beschikbaar als open bare preview.
> De preview-versie wordt aangeboden zonder Service Level Agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Met Azure Machine Learning Compute-instantie kunt u modellen ontwerpen, trainen en implementeren in een volledig geïntegreerde laptop ervaring in uw werk ruimte.

U kunt Jupyter-notebooks in [VS code](https://techcommunity.microsoft.com/t5/azure-ai/power-your-vs-code-notebooks-with-azml-compute-instances/ba-p/1629630) uitvoeren met Compute instance als de externe server zonder SSH vereist. U kunt ook VS code-integratie inschakelen via [externe SSH-extensie](https://devblogs.microsoft.com/python/enhance-your-azure-machine-learning-experience-with-the-vs-code-extension/).

U kunt [pakketten installeren](how-to-access-terminal.md#install-packages) en [kernels toevoegen](how-to-access-terminal.md#add-new-kernels) aan uw reken exemplaar.  

De volgende hulpprogram ma's en omgevingen zijn al geïnstalleerd op de compute-instantie: 

|Algemene hulpprogram ma's & omgevingen|Details|
|----|:----:|
|Stuurprogramma's|`CUDA`</br>`cuDNN`</br>`NVIDIA`</br>`Blob FUSE` |
|Intel MPI-bibliotheek||
|Azure CLI ||
|Azure Machine Learning-voor beelden ||
|Docker||
|Nginx||
|NCCL 2,0 ||
|Protobuf|| 

|**R** -hulpprogram ma's & omgevingen|Details|
|----|:----:|
|RStudio server open source Edition (preview-versie)||
|R-kernel||
|Azure Machine Learning SDK voor R|[azuremlsdk](https://azure.github.io/azureml-sdk-for-r/reference/index.html)</br>SDK steekproeven|

|**PYTHON** -hulpprogram ma's & omgevingen|Details|
|----|----|
|Anaconda Python||
|Jupyter en-extensies||
|Jjupyterlab en-extensies||
[Azure Machine Learning-SDK voor Python](/python/api/overview/azure/ml/intro)</br>van PyPI|Omvat het meren deel van de extra azureml-pakketten.  Als u de volledige lijst wilt weer geven, [opent u een Terminal venster op uw reken exemplaar](how-to-access-terminal.md) en voert u uit <br/> `conda list -n azureml_py36 azureml*` |
|Andere PyPI-pakketten|`jupytext`</br>`tensorboard`</br>`nbconvert`</br>`notebook`</br>`Pillow`|
|Conda-pakketten|`cython`</br>`numpy`</br>`ipykernel`</br>`scikit-learn`</br>`matplotlib`</br>`tqdm`</br>`joblib`</br>`nodejs`</br>`nb_conda_kernels`|
|Uitgebreide leer pakketten|`PyTorch`</br>`TensorFlow`</br>`Keras`</br>`Horovod`</br>`MLFlow`</br>`pandas-ml`</br>`scrapbook`|
|ONNX-pakketten|`keras2onnx`</br>`onnx`</br>`onnxconverter-common`</br>`skl2onnx`</br>`onnxmltools`|
|Azure Machine Learning python & R SDK-voor beelden||

Python-pakketten zijn allemaal geïnstalleerd in de **Python 3,6-AzureML-** omgeving.  

## <a name="accessing-files"></a>Toegang tot bestanden

Notebooks en R-scripts worden opgeslagen in het standaard opslag account van uw werk ruimte in de Azure-bestands share.  Deze bestanden bevinden zich in de map gebruikers bestanden. Met deze opslag kunt u eenvoudig notitie blokken delen tussen Compute-exemplaren. Het opslag account zorgt er ook voor dat uw notitie blokken veilig blijven behouden wanneer u een reken instantie stopt of verwijdert.

Het account van de Azure-bestands share van uw werk ruimte is gekoppeld als een station op het reken exemplaar. Dit station is de standaard werkmap voor Jupyter, Jupyter Labs en RStudio. Dit betekent dat de notitie blokken en andere bestanden die u maakt in Jupyter, Jjupyterlab of RStudio automatisch worden opgeslagen op de bestands share en ook beschikbaar zijn voor gebruik in andere reken instanties.

De bestanden in de bestands share zijn toegankelijk vanuit alle reken instanties in dezelfde werk ruimte. Wijzigingen in deze bestanden op het reken exemplaar worden betrouwbaar weer gegeven in de bestands share.

U kunt de meest recente Azure Machine Learning-voor beelden ook klonen naar uw map in de map gebruikers bestanden in de werkruimte bestands share.

Het schrijven van kleine bestanden kan langzamer zijn op netwerk stations dan het schrijven naar de lokale schijf van de reken instantie zelf.  Als u veel kleine bestanden schrijft, kunt u een map rechtstreeks op het reken exemplaar gebruiken, zoals een `/tmp` Directory. Houd er rekening mee dat deze bestanden niet toegankelijk zijn vanuit andere compute-exemplaren. 

U kunt de `/tmp` Directory op het reken exemplaar voor uw tijdelijke gegevens gebruiken.  Schrijf echter geen grote gegevens bestanden op de besturingssysteem schijf van het reken exemplaar.  Gebruik in plaats daarvan [gegevens opslag](concept-azure-machine-learning-architecture.md#datasets-and-datastores) . Als u de Jjupyterlab Git-extensie hebt geïnstalleerd, kan deze ook leiden tot trage prestaties van reken instanties.

## <a name="managing-a-compute-instance"></a>Een reken instantie beheren

Selecteer in uw werk ruimte in Azure Machine Learning Studio **Compute** en selecteer vervolgens **Compute instance** bovenin.

![Een reken instantie beheren](./media/concept-compute-instance/manage-compute-instance.png)

U kunt de volgende acties uitvoeren:

* [Maak een reken instantie](#create). 
* Vernieuw het tabblad Compute instances.
* Een reken instantie starten, stoppen en opnieuw starten.  U betaalt voor de instantie wanneer deze wordt uitgevoerd. Stop de reken instantie wanneer u deze niet gebruikt om de kosten te verlagen. Als u een reken instantie stopt, wordt de toewijzing ervan ongedaan. Start het vervolgens opnieuw wanneer u het nodig hebt. Opmerking bij het stoppen van de reken instantie wordt de facturering voor reken uren gestopt, maar u wordt nog steeds gefactureerd voor de schijf, het open bare IP-adres en de standaard load balancer.
* Een reken instantie verwijderen.
* De lijst met reken processen filteren om alleen de items weer te geven die u hebt gemaakt.

Voor elk reken proces in uw werk ruimte die u kunt gebruiken, kunt u het volgende doen:

* Toegang tot Jupyter, Jjupyterlab, RStudio op het reken exemplaar
* SSH naar Compute-instantie. SSH-toegang is standaard uitgeschakeld, maar kan worden ingeschakeld op het moment dat het reken proces wordt gemaakt. SSH-toegang is via het mechanisme voor open bare/persoonlijke sleutels. Op het tabblad krijgt u details over SSH-verbindingen zoals IP-adres, gebruikers naam en poort nummer.
* Details ophalen over een specifiek reken exemplaar, zoals het IP-adres en de regio.

Met [Azure RBAC](../role-based-access-control/overview.md) kunt u bepalen welke gebruikers in de werk ruimte een reken instantie kunnen maken, verwijderen, starten, stoppen en opnieuw starten. Alle gebruikers in de rol Inzender en eigenaar van de werk ruimte kunnen reken instanties maken, verwijderen, starten, stoppen en opnieuw starten in de werk ruimte. Maar alleen de maker van een specifiek reken exemplaar of de gebruiker die is toegewezen als deze namens hen is gemaakt, heeft toegang tot Jupyter, Jjupyterlab en RStudio op die reken instantie. Een reken instantie is toegewezen aan één gebruiker met hoofd toegang en kan worden terminal in via Jupyter/Jjupyterlab/RStudio. Reken instantie heeft aanmelding voor één gebruiker en alle acties gebruiken de identiteit van die gebruiker voor Azure RBAC en de toewijzing van experimenten. SSH-toegang wordt beheerd via het mechanisme voor open bare/persoonlijke sleutels.

Deze acties kunnen worden beheerd door Azure RBAC:
* *Micro soft. MachineLearningServices/werk ruimten/reken-en lees bewerkingen*
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Micro soft. MachineLearningServices/werk ruimten/berekenen/verwijderen*
* *Micro soft. MachineLearningServices/werk ruimten/berekeningen/starten/actie*
* *Micro soft. MachineLearningServices/werk ruimten/berekeningen/stoppen/actie*
* *Micro soft. MachineLearningServices/werk ruimten/berekeningen/opnieuw opstarten/actie*

Als u een reken instantie wilt maken, moet u machtigingen hebben voor de volgende acties:
* *Microsoft.MachineLearningServices/workspaces/computes/write*
* *Microsoft.MachineLearningServices/workspaces/checkComputeNameAvailability/action*


### <a name="create-a-compute-instance"></a><a name="create"></a>Een rekenproces maken

Maak in uw werk ruimte in Azure Machine Learning Studio [een nieuw reken exemplaar](how-to-create-attach-compute-studio.md#compute-instance) van het gedeelte **Compute** of in de sectie **notebooks** , wanneer u klaar bent om een van uw notitie blokken uit te voeren. 

U kunt ook een exemplaar maken
* Rechtstreeks vanuit de [ervaring met geïntegreerde notebooks](tutorial-1st-experiment-sdk-setup.md#azure)
* In Azure Portal
* Van Azure Resource Manager sjabloon. Zie de [sjabloon Create a Azure machine learning Compute instance](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance)voor een voorbeeld sjabloon.
* Met [Azure machine learning SDK](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/machine-learning/concept-compute-instance.md)
* Vanuit de [cli-uitbrei ding voor Azure machine learning](reference-azure-machine-learning-cli.md#computeinstance)

De toegewezen kernen per regio per VM-serie quota en het totale regionale quotum, dat van toepassing is op het maken van een reken instantie, worden gecombineerd en gedeeld met Azure Machine Learning trainings berekenings cluster quotum. Wanneer het reken exemplaar wordt gestopt, wordt er geen quotum vrijgegeven om ervoor te zorgen dat u het reken exemplaar opnieuw kunt starten.


### <a name="create-on-behalf-of-preview"></a>Maken namens (preview-versie)

Als beheerder kunt u een compute-instantie maken namens een gegevens wetenschapper en de instantie hieraan toewijzen met:
* [Azure Resource Manager sjabloon](https://github.com/Azure/azure-quickstart-templates/tree/master/101-machine-learning-compute-create-computeinstance).  Zie [identiteits object-Id's zoeken voor verificatie configuratie](../healthcare-apis/find-identity-object-ids.md)voor meer informatie over het vinden van de TenantID en ObjectID die nodig zijn in deze sjabloon.  U kunt deze waarden ook vinden in de Azure Active Directory Portal.
* REST-API

De gegevens wetenschapper u het reken exemplaar maakt voor heeft de volgende Azure RBAC-machtigingen nodig: 
* *Micro soft. MachineLearningServices/werk ruimten/berekeningen/starten/actie*
* *Micro soft. MachineLearningServices/werk ruimten/berekeningen/stoppen/actie*
* *Micro soft. MachineLearningServices/werk ruimten/berekeningen/opnieuw opstarten/actie*
* *Micro soft. MachineLearningServices/werk ruimten/berekeningen/applicationaccess/actie*

De gegevens wetenschapper kunnen het reken proces starten, stoppen en opnieuw starten. Ze kunnen het reken exemplaar gebruiken voor:
* Jupyter
* Jjupyterlab
* RStudio
* Geïntegreerde notebooks

## <a name="compute-target"></a>Rekendoel

Reken instanties kunnen worden gebruikt als een [trainings berekenings doel](concept-compute-target.md#train) vergelijkbaar met Azure machine learning Compute-trainings clusters. 

Een reken instantie:
* Bevat een taak wachtrij.
* Voert taken veilig uit in een virtuele netwerk omgeving, zonder dat ondernemingen de SSH-poort hoeven te openen. De taak wordt uitgevoerd in een omgeving met containers en verpakt uw model afhankelijkheden in een docker-container.
* Kan meerdere kleine taken parallel uitvoeren (preview).  Twee taken per kernen kunnen parallel worden uitgevoerd terwijl de rest van de taken in de wachtrij worden geplaatst.
* Ondersteunt multi-GPU gedistribueerde trainings taken met één knoop punt

U kunt reken instantie gebruiken als een lokaal doel voor het afwijzen van de implementatie voor scenario's voor testen en fout opsporing.


## <a name="what-happened-to-notebook-vm"></a><a name="notebookvm"></a>Wat is er gebeurd met de VM van de notebook?

Reken instanties vervangen de VM van de notebook.  

Alle notitieblok bestanden die zijn opgeslagen in de werkruimte bestands share en gegevens in werkruimte gegevens archieven, zijn toegankelijk vanuit een reken instantie. Alle aangepaste pakketten die eerder op een notebook-VM zijn geïnstalleerd, moeten echter opnieuw worden geïnstalleerd op het reken exemplaar. Quota beperkingen, die van toepassing zijn op het maken van reken clusters, zijn ook van toepassing op het maken van reken instanties.

Er kunnen geen nieuwe Vm's voor het notitie blok worden gemaakt. U kunt echter nog steeds toegang krijgen tot en gebruikmaken van de laptop-Vm's die u hebt gemaakt, met volledige functionaliteit. Reken instanties kunnen worden gemaakt in dezelfde werk ruimte als de bestaande virtuele machines van het werk blok.


## <a name="next-steps"></a>Volgende stappen

* [Een reken instantie maken en beheren](how-to-create-manage-compute-instance.md)
* [Zelf studie: uw eerste ml-model trainen](tutorial-1st-experiment-sdk-train.md) laat zien hoe u een reken instantie met een geïntegreerde notebook kunt gebruiken.
