---
title: Een firewall gebruiken
titleSuffix: Azure Machine Learning
description: Toegang tot Azure Machine Learning-werk ruimten beheren met Azure-firewalls. Meer informatie over de hosts die u via de firewall moet toestaan.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/18/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 0fa3492555b2870ae7b95abec08bbd3280cdc985
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/20/2020
ms.locfileid: "97705061"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>De werk ruimte achter een firewall gebruiken voor Azure Machine Learning

In dit artikel vindt u informatie over het configureren van Azure Firewall voor het beheren van de toegang tot uw Azure Machine Learning-werk ruimte en het open bare Internet. Zie [Enter prise Security for Azure machine learning](concept-enterprise-security.md)voor meer informatie over het beveiligen van Azure machine learning.

> [!WARNING]
> Toegang tot gegevens opslag achter een firewall wordt alleen ondersteund in code-eerste ervaringen. Het gebruik van de [Azure machine learning Studio](overview-what-is-machine-learning-studio.md) om toegang te krijgen tot gegevens achter een firewall, wordt niet ondersteund. Als u met de studio met behulp van de gegevens opslag in een particulier netwerk wilt werken, moet u eerst [een virtueel netwerk instellen](../virtual-network/quick-create-portal.md) en [de Studio toegang geven tot gegevens die zijn opgeslagen in een virtueel netwerk](how-to-enable-studio-virtual-network.md).

## <a name="azure-firewall"></a>Azure Firewall

Gebruik bij het gebruik van Azure Firewall __doel-Network Address Translation (DNAT)__ om nat-regels voor inkomend verkeer te maken. Maak voor uitgaand verkeer __netwerk__ -en/of __toepassings__ regels. Deze regel verzamelingen worden uitgebreid beschreven in [wat Azure firewall-concepten zijn](../firewall/firewall-faq.md#what-are-some-azure-firewall-concepts).

### <a name="inbound-configuration"></a>Binnenkomende configuratie

Als u een Azure Machine Learning __Compute-instantie__ of __Compute-Cluster__ gebruikt, voegt u een door de [gebruiker gedefinieerde routes (udr's)](../virtual-network/virtual-networks-udr-overview.md) toe voor het subnet dat de Azure machine learning resources bevat. Deze route forceert verkeer __van__ de IP-adressen van de `BatchNodeManagement` `AzureMachineLearning` resources en naar het open bare IP-adres van uw reken instantie en reken cluster.

Deze Udr's inschakelen de batch-service om te communiceren met reken knooppunten voor het plannen van taken. Voeg ook het IP-adres voor de Azure Machine Learning-service toe waarin de resources bestaan, omdat dit vereist is voor toegang tot reken instanties. Gebruik een van de volgende methoden om een lijst met IP-adressen van de batch-service en Azure Machine Learning-service te verkrijgen:

* Down load de [Azure IP-bereiken en-service Tags](https://www.microsoft.com/download/details.aspx?id=56519) en zoek het bestand voor `BatchNodeManagement.<region>` en `AzureMachineLearning.<region>` , waar `<region>` is uw Azure-regio.

* Gebruik de [Azure cli](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) om de informatie te downloaden. In het volgende voor beeld worden de IP-adres gegevens gedownload en worden de gegevens voor de regio VS Oost 2 gefilterd:

    ```azurecli-interactive
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
    ```

    > [!TIP]
    > Als u gebruikmaakt van de Amerikaanse Virginia, US-Arizona regio's of China-Oost-2 regio's, retour neren deze opdrachten geen IP-adressen. Gebruik in plaats daarvan een van de volgende koppelingen voor het downloaden van een lijst met IP-adressen:
    >
    > * [IP-adresbereiken en service tags voor Azure voor Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
    > * [Azure IP-adresbereiken en service tags voor Azure China](https://www.microsoft.com//download/details.aspx?id=57062)

Wanneer u de Udr's toevoegt, definieert u de route voor elk gerelateerde IP-adres voorvoegsel voor batch en stelt u het __type volgende hop__ in op __Internet__. In de volgende afbeelding ziet u een voor beeld van deze UDR in de Azure Portal:

![Voor beeld van een UDR voor een adres voorvoegsel](./media/how-to-enable-virtual-network/user-defined-route.png)

> [!IMPORTANT]
> De IP-adressen kunnen na verloop van tijd veranderen.

Zie [een Azure batch groep maken in een virtueel netwerk](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)voor meer informatie.

### <a name="outbound-configuration"></a>Uitgaande configuratie

1. Voeg __netwerk regels__ toe, waardoor verkeer __van__ en __naar__ de volgende service tags wordt toegestaan:

    * AzureActiveDirectory
    * AzureMachineLearning
    * AzureResourceManager
    * Opslag. regio
    * Sleutel kluis. regio
    * ContainerRegistry. regio

    Als u van plan bent de standaard-docker-installatie kopieën van micro soft te gebruiken en door gebruikers beheerde afhankelijkheden in te scha kelen, moet u ook de volgende service Tags toevoegen:

    * MicrosoftContainerRegistry. regio
    * AzureFrontDoor.FirstParty

    Voor vermeldingen die bevatten `region` , vervangt u door de Azure-regio die u gebruikt. Bijvoorbeeld `keyvault.westus`.

    Selecteer voor het __protocol__ `TCP` . Selecteer voor de bron- en doel poort `*` .

1. Voeg __toepassings regels__ toe voor de volgende hosts:

    > [!NOTE]
    > Dit is geen volledige lijst met de hosts die vereist zijn voor alle python-bronnen op internet, alleen het meest gebruikte. Als u bijvoorbeeld toegang tot een GitHub-opslag plaats of andere host nodig hebt, moet u de vereiste hosts voor dat scenario identificeren en toevoegen.

    | **Hostnaam** | **Doel** |
    | ---- | ---- |
    | **anaconda.com**</br>**\*. anaconda.com** | Wordt gebruikt om standaard pakketten te installeren. |
    | **\*. anaconda.org** | Wordt gebruikt om opslag plaats-gegevens op te halen. |
    | **pypi.org** | Wordt gebruikt voor het weer geven van afhankelijkheden uit de standaard index, indien aanwezig, en de index wordt niet overschreven door gebruikers instellingen. Als de index wordt overschreven, moet u ook **\* . pythonhosted.org** toestaan. |
    | **cloud.r-project.org** | Wordt gebruikt bij het installeren van KRANs pakketten voor R-ontwikkeling. |
    | **\*pytorch.org** | Wordt gebruikt door enkele voor beelden op basis van PyTorch. |
    | **\*. tensorflow.org** | Wordt gebruikt door enkele voor beelden op basis van tensor flow. |

    Selecteer voor __Protocol: poort__ __http, https__ gebruiken.

    Zie [Azure firewall implementeren en configureren](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)voor meer informatie over het configureren van toepassings regels.

1. Als u de toegang wilt beperken tot modellen die zijn geïmplementeerd op Azure Kubernetes service (AKS), raadpleegt u uitgaand [verkeer beperken in de Azure Kubernetes-service](../aks/limit-egress-traffic.md).

## <a name="other-firewalls"></a>Andere firewalls

De richt lijnen in deze sectie zijn algemeen, aangezien elke firewall een eigen terminologie en specifieke configuraties heeft. Als u vragen hebt over het toestaan van communicatie via uw firewall, raadpleegt u de documentatie voor de firewall die u gebruikt.

Als niet correct is geconfigureerd, kan de firewall problemen veroorzaken met uw werk ruimte. Er zijn verschillende hostnamen die worden gebruikt door de Azure Machine Learning-werk ruimte. In de volgende secties worden de hosts vermeld die vereist zijn voor Azure Machine Learning.

### <a name="microsoft-hosts"></a>Micro soft-hosts

De hosts in deze sectie zijn eigendom van micro soft en bieden services die nodig zijn om uw werk ruimte goed te laten functioneren. In de volgende tabellen worden de hostnamen weer geven voor de regio's Azure Public, Azure Government en Azure China 21Vianet.

**Algemene Azure-hosts**

| **Vereist voor** | **Openbare Azure-peering** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Azure Portal | management.azure.com | management.azure.us | management.azure.cn |

**Azure Machine Learning-hosts**

| **Vereist voor** | **Openbare Azure-peering** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*. azureml.ms | \*. ml.azure.us | \*. ml.azure.cn |
| Experimenten, geschiedenis, Hyperdrive, labels | \*. experiments.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| Modelbeheer | \*. modelmanagement.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| Pijplijn | \*. aether.ms | \*. ml.azure.us | \*. ml.azure.cn |
| Designer (Studio-Service) | \*. studioservice.azureml.com | \*. ml.azure.us | \*. ml.azure.cn |
| Geïntegreerde notebook | \*. notebooks.azure.net | \*. notebooks.usgovcloudapi.net |\*. notebooks.chinacloudapi.cn |
| Geïntegreerde notebook | \*. file.core.windows.net | \*. file.core.usgovcloudapi.net | \*. file.core.chinacloudapi.cn |
| Geïntegreerde notebook | \*.dfs.core.windows.net | \*. dfs.core.usgovcloudapi.net | \*. dfs.core.chinacloudapi.cn |
| Geïntegreerde notebook | \*.blob.core.windows.net | \*. blob.core.usgovcloudapi.net | \*. blob.core.chinacloudapi.cn |
| Geïntegreerde notebook | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| Geïntegreerde notebook | \*. aznbcontent.net |  | |

**Azure Machine Learning Compute-instantie en Compute-clusterhosts**

| **Vereist voor** | **Openbare Azure-peering** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Reken cluster/exemplaar | \*. batchai.core.windows.net | \*. batchai.core.usgovcloudapi.net |\*. batchai.ml.azure.cn |
| Rekenproces | \*. instances.azureml.net | \*. instances.azureml.us | \*. instances.azureml.cn |
| Rekenproces | \*. instances.azureml.ms |  |  |

**Gekoppelde resources die worden gebruikt door Azure Machine Learning**

| **Vereist voor** | **Openbare Azure-peering** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Storage-account | core.windows.net | core.usgovcloudapi.net | core.chinacloudapi.cn |
| Azure Key Vault | vault.azure.net | vault.usgovcloudapi.net | vault.azure.cn |
| Azure Container Registry | azurecr.io | azurecr.us | azurecr.cn |
| Micro soft Container Registry | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |


> [!TIP]
> Als u van plan bent federatieve identiteiten te gebruiken, volgt u de [Aanbevolen procedures voor het beveiligen van Active Directory Federation Services](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) -artikelen.

Gebruik ook de informatie in [geforceerde tunneling](how-to-secure-training-vnet.md#forced-tunneling) om IP-adressen voor en toe te voegen `BatchNodeManagement` `AzureMachineLearning` .

Voor informatie over het beperken van de toegang tot modellen die zijn geïmplementeerd op Azure Kubernetes service (AKS), raadpleegt u uitgaand [verkeer beperken in de Azure Kubernetes-service](../aks/limit-egress-traffic.md).

### <a name="python-hosts"></a>Python-hosts

De hosts in deze sectie worden gebruikt voor het installeren van Python-pakketten. Ze zijn vereist tijdens de ontwikkeling, training en implementatie. 

> [!NOTE]
> Dit is geen volledige lijst met de hosts die vereist zijn voor alle python-bronnen op internet, alleen het meest gebruikte. Als u bijvoorbeeld toegang tot een GitHub-opslag plaats of andere host nodig hebt, moet u de vereiste hosts voor dat scenario identificeren en toevoegen.

| **Hostnaam** | **Doel** |
| ---- | ---- |
| **anaconda.com**</br>**\*. anaconda.com** | Wordt gebruikt om standaard pakketten te installeren. |
| **\*. anaconda.org** | Wordt gebruikt om opslag plaats-gegevens op te halen. |
| **pypi.org** | Wordt gebruikt voor het weer geven van afhankelijkheden uit de standaard index, indien aanwezig, en de index wordt niet overschreven door gebruikers instellingen. Als de index wordt overschreven, moet u ook **\* . pythonhosted.org** toestaan. |
| **\*pytorch.org** | Wordt gebruikt door enkele voor beelden op basis van PyTorch. |
| **\*. tensorflow.org** | Wordt gebruikt door enkele voor beelden op basis van tensor flow. |

### <a name="r-hosts"></a>R-hosts

De hosts in deze sectie worden gebruikt voor het installeren van R-pakketten. Ze zijn vereist tijdens de ontwikkeling, training en implementatie.

> [!NOTE]
> Dit is geen volledige lijst met de hosts die vereist zijn voor alle R-bronnen op het Internet, alleen het meest voorkomende. Als u bijvoorbeeld toegang tot een GitHub-opslag plaats of andere host nodig hebt, moet u de vereiste hosts voor dat scenario identificeren en toevoegen.

| **Hostnaam** | **Doel** |
| ---- | ---- |
| **cloud.r-project.org** | Wordt gebruikt bij het installeren van KRANs pakketten. |

> [!IMPORTANT]
> Intern maakt de R SDK voor Azure Machine Learning gebruik van Python-pakketten. Daarom moet u ook python-hosts via de Firewall toestaan.
## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Azure Firewall implementeren en configureren met Azure Portal](../firewall/tutorial-firewall-deploy-portal.md)
* [Azure ML-experiment- en deductietaken beveiligen binnen een Azure Virtual Network](how-to-network-security-overview.md)
