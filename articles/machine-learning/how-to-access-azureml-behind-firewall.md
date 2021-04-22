---
title: Een firewall gebruiken
titleSuffix: Azure Machine Learning
description: Toegang tot Azure Machine Learning werkruimten beheren met Azure Firewalls. Meer informatie over de hosts die u via de firewall moet toestaan.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/18/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: d907b93e6186a3d10e7ed5a6dbfe0e0c49731edb
ms.sourcegitcommit: 5ce88326f2b02fda54dad05df94cf0b440da284b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107890398"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Werkruimte achter een firewall gebruiken voor Azure Machine Learning

In dit artikel leert u hoe u een Azure Firewall om de toegang tot uw werkruimte Azure Machine Learning en het openbare internet te beheren. Zie Enterprise-beveiliging voor Azure Machine Learning voor meer [informatie over het beveiligen Azure Machine Learning.](concept-enterprise-security.md)

> [!WARNING]
> Toegang tot gegevensopslag achter een firewall wordt alleen ondersteund in code first experiences. Het gebruik [Azure Machine Learning-studio](overview-what-is-machine-learning-studio.md) toegang tot gegevens achter een firewall wordt niet ondersteund. Als u met de studio met gegevensopslag in [](../virtual-network/quick-create-portal.md) een particulier netwerk wilt werken, moet u eerst een virtueel netwerk instellen en de studio toegang geven tot gegevens die zijn opgeslagen in [een virtueel netwerk.](how-to-enable-studio-virtual-network.md)

## <a name="azure-firewall"></a>Azure Firewall

Wanneer u Azure Firewall, gebruikt u __DNAT (Destination Network Address Translation)__ om NAT-regels voor inkomende verkeer te maken. Maak voor uitgaand verkeer __netwerk- en/of__ __toepassingsregels.__ Deze regelverzamelingen worden gedetailleerder beschreven in [Wat zijn enkele Azure Firewall concepten.](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts)

### <a name="inbound-configuration"></a>Binnenkomende configuratie

Als u een Azure Machine Learning  compute-exemplaar of rekencluster __gebruikt,__ voegt u een door de gebruiker gedefinieerde [routes (UDR's)](../virtual-network/virtual-networks-udr-overview.md) toe voor het subnet dat de Azure Machine Learning bevat. Deze route dwingt verkeer __af van__ de IP-adressen van de resources en naar het `BatchNodeManagement` openbare `AzureMachineLearning` IP-adres van uw reken-exemplaar en rekencluster.

Met deze UDR's kan de Batch-service communiceren met rekenknooppunten voor taakplanning. Voeg ook het IP-adres voor de Azure Machine Learning Service, omdat dit vereist is voor toegang tot reken-exemplaren. Wanneer u het IP-adres voor Azure Machine Learning Service toevoegt, moet u het IP-adres voor zowel de primaire als de secundaire __Azure-regio__ toevoegen. De primaire regio is de regio waarin uw werkruimte zich bevindt.

Zie Ensure business continuity & disaster recovery using Azure Paired Regions (Bedrijfscontinuïteit garanderen & herstel na noodherstel met behulp van [gekoppelde Azure-regio's) voor](../best-practices-availability-paired-regions.md#azure-regional-pairs)informatie over de secundaire regio. Als uw regio zich bijvoorbeeld Azure Machine Learning Service VS - oost 2, is de secundaire regio VS - centraal. 

Gebruik een van de volgende methoden om een lijst met IP-adressen van de Batch-service en Azure Machine Learning Service op te halen:

* Download de [Azure IP-adresbereiken en servicetags](https://www.microsoft.com/download/details.aspx?id=56519) en zoek het bestand naar `BatchNodeManagement.<region>` en , waarbij uw `AzureMachineLearning.<region>` `<region>` Azure-regio is.

* Gebruik de [Azure CLI om](/cli/azure/install-azure-cli) de informatie te downloaden. In het volgende voorbeeld worden de IP-adresgegevens gedownload en worden de gegevens gefilterd voor de regio VS - oost 2 (primair) en vs - centraal (secundair):

    ```azurecli-interactive
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
    # Get primary region IPs
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
    # Get secondary region IPs
    az network list-service-tags -l "Central US" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='centralus']"
    ```

    > [!TIP]
    > Als u de regio's US-Virginia, US-Arizona of China-East-2 gebruikt, retourneren deze opdrachten geen IP-adressen. Gebruik in plaats daarvan een van de volgende koppelingen om een lijst met IP-adressen te downloaden:
    >
    > * [Azure IP-adresbereiken en servicetags voor Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
    > * [Azure IP-adresbereiken en servicetags voor Azure China](https://www.microsoft.com//download/details.aspx?id=57062)

Wanneer u de UDR's toevoegt, definieert u de route voor elk gerelateerd batch-IP-adres voorvoegsel en stelt u __Volgend hoptype in__ op __Internet__. In de volgende afbeelding ziet u een voorbeeld van deze UDR in de Azure Portal:

![Voorbeeld van een UDR voor een adres voorvoegsel](./media/how-to-enable-virtual-network/user-defined-route.png)

> [!IMPORTANT]
> De IP-adressen kunnen na een periode veranderen.

Zie Create an Azure Batch pool in a virtual network (Een Azure Batch [maken in een virtueel netwerk) voor meer informatie.](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling)

### <a name="outbound-configuration"></a>Uitgaande configuratie

1. Voeg __netwerkregels toe__ om verkeer van __en naar__ de __volgende__ servicetags toe te staan:

    * AzureActiveDirectory
    * AzureMachineLearning
    * AzureResourceManager
    * Storage.region
    * KeyVault.region
    * ContainerRegistry.region

    Als u van plan bent om de standaard Docker-afbeeldingen van Microsoft te gebruiken en door de gebruiker beheerde afhankelijkheden in te stellen, moet u ook de volgende servicetags toevoegen:

    * MicrosoftContainerRegistry.region
    * AzureFrontDoor.FirstParty

    Voor vermeldingen die `region` bevatten, vervangt u door de Azure-regio die u gebruikt. Bijvoorbeeld `keyvault.westus`.

    Selecteer voor __het protocol__ `TCP` . Selecteer voor de bron- en __doelpoorten.__ `*`

1. Voeg __toepassingsregels toe__ voor de volgende hosts:

    > [!NOTE]
    > Dit is geen volledige lijst met hosts die vereist zijn voor alle Python-resources op internet, alleen de meest gebruikte. Als u bijvoorbeeld toegang nodig hebt tot een GitHub-opslagplaats of een andere host, moet u de vereiste hosts voor dat scenario identificeren en toevoegen.

    | **Hostnaam** | **Doel** |
    | ---- | ---- |
    | **graph.windows.net** | Wordt gebruikt door Azure Machine Learning reken-exemplaar/cluster. |
    | **anaconda.com**</br>**\*.anaconda.com** | Wordt gebruikt om standaardpakketten te installeren. |
    | **\*.anaconda.org** | Wordt gebruikt om gegevens van de repo op te halen. |
    | **pypi.org** | Wordt gebruikt om afhankelijkheden van de standaardindex weer te bieden, indien vandaan, en de index wordt niet overschreven door de gebruikersinstellingen. Als de index wordt overschreven, moet u ook **\* .pythonhosted.org.** |
    | **cloud.r-project.org** | Wordt gebruikt bij het installeren van CRAN-pakketten voor R-ontwikkeling. |
    | **\*pytorch.org** | Wordt gebruikt door enkele voorbeelden op basis van PyTorch. |
    | **\*.tensorflow.org** | Wordt gebruikt door enkele voorbeelden op basis van Tensorflow. |

    Bij __Protocol:Poort selecteert__ u __http, https gebruiken.__

    Zie Deploy and configure Azure Firewall voor meer informatie over het configureren [van Azure Firewall.](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule)

1. Als u de toegang wilt beperken tot modellen die zijn geïmplementeerd op Azure Kubernetes Service (AKS), gaat u naar Beperk het verkeer [voor Azure Kubernetes Service](../aks/limit-egress-traffic.md).

## <a name="other-firewalls"></a>Andere firewalls

De richtlijnen in deze sectie zijn algemeen, omdat elke firewall zijn eigen terminologie en specifieke configuraties heeft. Als u vragen hebt over het toestaan van communicatie via uw firewall, raadpleegt u de documentatie voor de firewall die u gebruikt.

Als deze niet juist is geconfigureerd, kan de firewall problemen veroorzaken met het gebruik van uw werkruimte. Er zijn verschillende hostnamen die beide worden gebruikt door de Azure Machine Learning werkruimte. In de volgende secties vindt u hosts die vereist zijn voor Azure Machine Learning.

### <a name="microsoft-hosts"></a>Microsoft-hosts

De hosts in deze sectie zijn eigendom van Microsoft en bieden services die vereist zijn voor een goede werking van uw werkruimte. In de volgende tabellen worden de hostnamen voor de openbare Azure-, Azure Government- en Azure China 21Vianet weergegeven.

**Algemene Azure-hosts**

| **Vereist voor** | **Openbare Azure-peering** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Azure Portal | management.azure.com | management.azure.us | management.azure.cn |
| Azure Resource Manager | management.azure.com | management.usgovcloudapi.net | management.chinacloudapi.cn |

**Azure Machine Learning hosts**

| **Vereist voor** | **Openbare Azure-peering** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*.azureml.ms | \*.ml.azure.us | \*.ml.azure.cn |
| Experimenten, Geschiedenis, Hyperdrive, labelen | \*.experiments.azureml.net | \*.ml.azure.us | \*.ml.azure.cn |
| Modelbeheer | \*.modelmanagement.azureml.net | \*.ml.azure.us | \*.ml.azure.cn |
| Pijplijn | \*.aether.ms | \*.ml.azure.us | \*.ml.azure.cn |
| Designer (Studio Service) | \*.studioservice.azureml.com | \*.ml.azure.us | \*.ml.azure.cn |
| Geïntegreerde notebook | \*.notebooks.azure.net | \*.notebooks.usgovcloudapi.net |\*.notebooks.chinacloudapi.cn |
| Geïntegreerde notebook | \*.file.core.windows.net | \*.file.core.usgovcloudapi.net | \*.file.core.chinacloudapi.cn |
| Geïntegreerde notebook | \*.dfs.core.windows.net | \*.dfs.core.usgovcloudapi.net | \*.dfs.core.chinacloudapi.cn |
| Geïntegreerde notebook | \*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net | \*.blob.core.chinacloudapi.cn |
| Geïntegreerde notebook | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| Geïntegreerde notebook | \*.aznbcontent.net |  | |

**Azure Machine Learning-exemplaar en rekenclusterhosts maken**

| **Vereist voor** | **Openbare Azure-peering** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Rekencluster/-exemplaar | \*.batchai.core.windows.net | \*.batchai.core.usgovcloudapi.net |\*.batchai.ml.azure.cn |
| Rekencluster/-exemplaar | graph.windows.net | graph.windows.net | graph.chinacloudapi.cn |
| Rekenproces | \*.instances.azureml.net | \*.instances.azureml.us | \*.instances.azureml.cn |
| Rekenproces | \*.instances.azureml.ms |  |  |

**Gekoppelde resources die worden gebruikt door Azure Machine Learning**

| **Vereist voor** | **Openbare Azure-peering** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Storage-account | core.windows.net | core.usgovcloudapi.net | core.chinacloudapi.cn |
| Azure Key Vault | vault.azure.net | vault.usgovcloudapi.net | vault.azure.cn |
| Azure Container Registry | azurecr.io | azurecr.us | azurecr.cn |
| Microsoft Container Registry | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |


> [!TIP]
> Als u van plan bent federatief te gebruiken, volgt u het artikel [Best practices voor het beveiligen Active Directory Federation Services](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) identiteit.

Gebruik ook de informatie in [geforceerd tunneling om](how-to-secure-training-vnet.md#forced-tunneling) IP-adressen toe te voegen voor `BatchNodeManagement` en `AzureMachineLearning` .

Zie Toegang tot modellen die zijn geïmplementeerd op Azure Kubernetes Service (AKS) beperken voor meer informatie over het beperken van de toegang [tot Azure Kubernetes Service](../aks/limit-egress-traffic.md).

### <a name="python-hosts"></a>Python-hosts

De hosts in deze sectie worden gebruikt om Python-pakketten te installeren. Ze zijn vereist tijdens de ontwikkeling, training en implementatie. 

> [!NOTE]
> Dit is geen volledige lijst met hosts die vereist zijn voor alle Python-resources op internet, alleen de meest gebruikte. Als u bijvoorbeeld toegang nodig hebt tot een GitHub-opslagplaats of een andere host, moet u de vereiste hosts voor dat scenario identificeren en toevoegen.

| **Hostnaam** | **Doel** |
| ---- | ---- |
| **anaconda.com**</br>**\*.anaconda.com** | Wordt gebruikt om standaardpakketten te installeren. |
| **\*.anaconda.org** | Wordt gebruikt om gegevens van de repo op te halen. |
| **pypi.org** | Wordt gebruikt om afhankelijkheden van de standaardindex weer te bieden, indien vandaan, en de index wordt niet overschreven door de gebruikersinstellingen. Als de index wordt overschreven, moet u ook **\* .pythonhosted.org.** |
| **\*pytorch.org** | Wordt gebruikt door enkele voorbeelden op basis van PyTorch. |
| **\*.tensorflow.org** | Wordt gebruikt door enkele voorbeelden op basis van Tensorflow. |

### <a name="r-hosts"></a>R-hosts

De hosts in deze sectie worden gebruikt om R-pakketten te installeren. Ze zijn vereist tijdens de ontwikkeling, training en implementatie.

> [!NOTE]
> Dit is geen volledige lijst met hosts die vereist zijn voor alle R-resources op internet, alleen de meest gebruikte. Als u bijvoorbeeld toegang nodig hebt tot een GitHub-opslagplaats of een andere host, moet u de vereiste hosts voor dat scenario identificeren en toevoegen.

| **Hostnaam** | **Doel** |
| ---- | ---- |
| **cloud.r-project.org** | Wordt gebruikt bij het installeren van CRAN-pakketten. |

> [!IMPORTANT]
> Intern maakt de R SDK voor Azure Machine Learning gebruik van Python-pakketten. U moet dus ook Python-hosts toestaan via de firewall.
## <a name="next-steps"></a>Volgende stappen

* [Zelfstudie: Azure Firewall implementeren en configureren met Azure Portal](../firewall/tutorial-firewall-deploy-portal.md)
* [Azure ML-experiment- en deductietaken beveiligen binnen een Azure Virtual Network](how-to-network-security-overview.md)
