---
title: Privépakketten van Python gebruiken
titleSuffix: Azure Machine Learning
description: Meer informatie over hoe u veilig kunt werken met privé Python-pakketten vanuit uw Azure Machine Learning omgevingen.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: laobri
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 07/10/2020
ms.openlocfilehash: 5cc1025528959f8c81aa80703e3fc26d59c5266f
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102618097"
---
# <a name="use-private-python-packages-with-azure-machine-learning"></a>Gebruik persoonlijke Python-pakketten met Azure Machine Learning


In dit artikel leest u hoe u privé Python-pakketten veilig kunt gebruiken in Azure Machine Learning. Voor beelden van het gebruik van persoonlijke Python-pakketten zijn:

 * U hebt een persoonlijk pakket ontwikkeld dat u niet openbaar wilt delen.
 * U wilt een opslag plaats met een curator gebruiken van pakketten die zijn opgeslagen in een bedrijfs firewall.

De aanbevolen benadering is afhankelijk van of u slechts enkele pakketten hebt voor een enkele Azure Machine Learning-werk ruimte of een volledige opslag plaats van pakketten voor alle werk ruimten binnen een organisatie.

De persoonlijke pakketten worden gebruikt via de [omgevings](/python/api/azureml-core/azureml.core.environment.environment) klasse. Binnen een omgeving declareert u welke Python-pakketten moeten worden gebruikt, inclusief persoonlijke items. Zie [omgevingen gebruiken](how-to-use-environments.md)voor meer informatie over de omgeving van Azure machine learning in het algemeen. 

## <a name="prerequisites"></a>Vereisten

 * De [Azure machine learning SDK voor python](/python/api/overview/azure/ml/install)
 * Een [Azure machine learning-werk ruimte](how-to-manage-workspace.md)

## <a name="use-small-number-of-packages-for-development-and-testing"></a>Klein aantal pakketten gebruiken voor ontwikkelen en testen

Gebruik de statische methode voor een klein aantal privé-pakketten voor één werk ruimte [`Environment.add_private_pip_wheel()`](/python/api/azureml-core/azureml.core.environment.environment#add-private-pip-wheel-workspace--file-path--exist-ok-false-) . Met deze aanpak kunt u snel een persoonlijk pakket toevoegen aan de werk ruimte en is het goed geschikt voor ontwikkelings-en test doeleinden.

Wijs het pad naar het bestand naar een lokaal wiel bestand en voer de ```add_private_pip_wheel``` opdracht uit. De opdracht retourneert een URL die wordt gebruikt om de locatie van het pakket in uw werk ruimte bij te houden. Leg de opslag-URL vast en geef deze de `add_pip_package()` methode door.

```python
whl_url = Environment.add_private_pip_wheel(workspace=ws,file_path = "my-custom.whl")
myenv = Environment(name="myenv")
conda_dep = CondaDependencies()
conda_dep.add_pip_package(whl_url)
myenv.python.conda_dependencies=conda_dep
```

Intern wordt de URL door Azure Machine Learning service vervangen door een beveiligde SAS-URL, zodat uw wiel bestand privé en beveiligd blijft.

## <a name="use-a-repository-of-packages-from-azure-devops-feed"></a>Een opslag plaats van pakketten van Azure DevOps feed gebruiken

Als u Python-pakketten actief ontwikkelt voor uw machine learning-toepassing, kunt u deze als artefacten hosten in een Azure DevOps-opslag plaats en publiceren als een feed. Met deze aanpak kunt u de DevOps-werk stroom voor het bouwen van pakketten integreren met uw Azure Machine Learning-werkruimte. Lees aan de [slag met Python-pakketten in azure artefacten](/azure/devops/artifacts/quickstarts/python-packages) voor meer informatie over het instellen van python-feeds met behulp van Azure DevOps

Deze benadering maakt gebruik van een persoonlijk toegangs token voor verificatie op basis van de opslag plaats. Dezelfde benadering is van toepassing op andere opslag plaatsen met verificatie op basis van tokens, zoals persoonlijke GitHub-opslag plaatsen. 

 1. [Maak een persoonlijk toegangs token (Pat)](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?tabs=preview-page#create-a-pat) voor uw Azure DevOps-exemplaar. Stel het bereik van het token in op __verpakt > lezen__. 

 2. Voeg de URL van Azure DevOps en PAT als werkruimte eigenschappen toe met behulp van de [Workspace.set_connection](/python/api/azureml-core/azureml.core.workspace.workspace#set-connection-name--category--target--authtype--value-) methode.

     ```python
    from azureml.core import Workspace
    
    pat_token = input("Enter secret token")
    ws = Workspace.from_config()
    ws.set_connection(name="connection-1", 
        category = "PythonFeed",
        target = "https://<my-org>.pkgs.visualstudio.com", 
        authType = "PAT", 
        value = pat_token) 
     ```

 3. Een Azure Machine Learning omgeving maken en Python-pakketten toevoegen vanuit de feed.
    
    ```python
    from azureml.core import Environment
    from azureml.core.conda_dependencies import CondaDependencies
    
    env = Environment(name="my-env")
    cd = CondaDependencies()
    cd.add_pip_package("<my-package>")
    cd.set_pip_option("--extra-index-url https://<my-org>.pkgs.visualstudio.com/<my-project>/_packaging/<my-feed>/pypi/simple")
    env.python.conda_dependencies=cd
    ```

De omgeving is nu klaar om te worden gebruikt in trainings runs of implementaties van webservice-eind punten. Bij het bouwen van de omgeving gebruikt Azure Machine Learning service de PAT om te verifiëren tegen de feed met de overeenkomende basis-URL.

## <a name="use-a-repository-of-packages-from-private-storage"></a>Een opslag plaats van pakketten uit een privé opslag gebruiken

U kunt pakketten gebruiken van een Azure-opslag account in de firewall van uw organisatie. Het opslag account kan een serie pakketten of een interne mirror van openbaar beschik bare pakketten bevatten.

Zie [een Azure machine learning-werk ruimte en gekoppelde resources beveiligen](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts-with-service-endpoints)voor informatie over het instellen van dergelijke persoonlijke opslag. U moet ook [de Azure container Registry (ACR) achter het VNet plaatsen](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr).

> [!IMPORTANT]
> U moet deze stap volt ooien om modellen te kunnen trainen of implementeren met behulp van de opslag plaats van een persoonlijk pakket.

Nadat u deze configuraties hebt voltooid, kunt u naar de pakketten in de Azure Machine Learning omgevings definitie verwijzen met de volledige URL in Azure Blob-opslag.

## <a name="next-steps"></a>Volgende stappen

 * Meer informatie over [Enter prise Security in azure machine learning](concept-enterprise-security.md)