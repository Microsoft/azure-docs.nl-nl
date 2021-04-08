---
title: Een Azure Machine Learning-werk ruimte beveiligen met virtuele netwerken
titleSuffix: Azure Machine Learning
description: Gebruik een geïsoleerde Azure-Virtual Network om uw Azure Machine Learning-werk ruimte en de bijbehorende resources te beveiligen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 03/17/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: a923f65e5c6183d045f4b7455e0a01edda75d499
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104584331"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>Een Azure Machine Learning-werk ruimte beveiligen met virtuele netwerken

In dit artikel leert u hoe u een Azure Machine Learning werkruimte en de bijbehorende resources in een virtueel netwerk kunt beveiligen.

Dit artikel is deel twee van een serie van vijf delen die u begeleidt bij het beveiligen van een Azure Machine Learning werk stroom. We raden u ten zeerste aan om in [deel één het overzicht van VNet](how-to-network-security-overview.md) te lezen om eerst inzicht te krijgen in de algehele architectuur. 

Zie de andere artikelen in deze serie:

[1. VNet-overzicht](how-to-network-security-overview.md)  >  **2. Beveilig de werk ruimte**  >  [3. De trainings omgeving](how-to-secure-training-vnet.md)  >  [4 beveiligen. Beveilig de vergoedings omgeving](how-to-secure-inferencing-vnet.md)  >  [5. De functionaliteit van Studio inschakelen](how-to-enable-studio-virtual-network.md)

In dit artikel leert u hoe u de volgende werk ruimte-resources in een virtueel netwerk kunt inschakelen:
> [!div class="checklist"]
> - Azure Machine Learning-werkruimte
> - Azure Storage-accounts
> - Azure Machine Learning gegevens opslag en gegevens sets
> - Azure Key Vault
> - Azure Container Registry

## <a name="prerequisites"></a>Vereisten

+ Lees het artikel [overzicht van netwerk beveiliging](how-to-network-security-overview.md) voor inzicht in algemene scenario's voor virtuele netwerken en de algehele architectuur van virtuele netwerken.

+ Een bestaand virtueel netwerk en subnet voor gebruik met uw reken resources.

+ Als u resources wilt implementeren in een virtueel netwerk of subnet, moet uw gebruikers account over machtigingen beschikken voor de volgende acties in azure op rollen gebaseerd toegangs beheer (Azure RBAC):

    - ' Micro soft. Network/virtualNetworks/lid/Action ' op de virtuele netwerk resource.
    - ' Micro soft. Network/virtualNetworks/subnet/lid/Action ' op de bron van het subnet.

    Zie voor meer informatie over Azure RBAC met netwerken de [ingebouwde rollen voor netwerken](../role-based-access-control/built-in-roles.md#networking)


## <a name="secure-the-workspace-with-private-endpoint"></a>De werk ruimte beveiligen met een persoonlijk eind punt

Met de persoonlijke Azure-koppeling kunt u verbinding maken met uw werk ruimte met behulp van een persoonlijk eind punt. Het persoonlijke eind punt is een reeks privé-IP-adressen in uw virtuele netwerk. Vervolgens kunt u de toegang tot uw werk ruimte beperken tot alleen de privé-IP-adressen. Een persoonlijke koppeling helpt het risico van gegevens exfiltration te verminderen.

Zie [persoonlijke koppeling configureren](how-to-configure-private-link.md)voor meer informatie over het instellen van een persoonlijke koppelings werkruimte.

> [!Warning]
> Het beveiligen van een werk ruimte met privé-eind punten garandeert geen end-to-end beveiliging. U moet de stappen in de rest van dit artikel en de VNet-serie volgen om de afzonderlijke onderdelen van uw oplossing te beveiligen.

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>Azure Storage-accounts beveiligen met Service-eind punten

Azure Machine Learning ondersteunt opslag accounts die zijn geconfigureerd voor het gebruik van service-eind punten of privé-eind punten. In deze sectie leert u hoe u een Azure-opslag account kunt beveiligen met behulp van service-eind punten. Zie de volgende sectie voor privé-eind punten.

Als u een Azure-opslag account wilt gebruiken voor de werk ruimte in een virtueel netwerk, gebruikt u de volgende stappen:

1. Ga in het Azure Portal naar de opslag service die u wilt gebruiken in uw werk ruimte.

   [![De opslag die is gekoppeld aan de Azure Machine Learning-werk ruimte](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Selecteer op de pagina Storage-Service account de optie __netwerken__.

   ![Het netwerk gebied op de pagina Azure Storage in het Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Voer op het tabblad __firewalls en virtuele netwerken__ de volgende acties uit:
    1. Selecteer __Geselecteerde netwerken__.
    1. Selecteer onder __virtuele netwerken__ de koppeling __bestaande virtuele netwerk toevoegen__ . Met deze actie wordt het virtuele netwerk waar uw Compute zich bevindt, toegevoegd (zie stap 1).

        > [!IMPORTANT]
        > Het opslag account moet zich in hetzelfde virtuele netwerk en subnet bevinden als de reken instanties of clusters die worden gebruikt voor de training of de interferentie.

    1. Schakel het selectie vakje __vertrouwde micro soft-Services toegang geven tot dit opslag account__ in. Deze wijziging geeft niet alle Azure-Services toegang tot uw opslag account.
    
        * Resources van sommige services die **in uw abonnement zijn geregistreerd**, hebben toegang tot het opslag account **in hetzelfde abonnement** voor Select-bewerkingen. U kunt bijvoorbeeld Logboeken schrijven of back-ups maken.
        * Resources van sommige services kunnen expliciet toegang krijgen tot uw opslag account door __een Azure-rol__ toe te wijzen aan de door het systeem toegewezen beheerde identiteit.

        Raadpleeg [Firewalls en virtuele netwerken voor Azure Storage configureren](../storage/common/storage-network-security.md#trusted-microsoft-services) voor meer informatie.

    > [!IMPORTANT]
    > Wanneer u werkt met de Azure Machine Learning SDK, moet uw ontwikkel omgeving verbinding kunnen maken met het Azure Storage-account. Wanneer het opslag account zich in een virtueel netwerk bevindt, moet de firewall toegang toestaan vanuit het IP-adres van de ontwikkel omgeving.
    >
    > Als u toegang tot het opslag account wilt inschakelen, gaat u naar de __firewalls en virtuele netwerken__ voor het opslag account *vanuit een webbrowser op de ontwikkelings-client*. Gebruik vervolgens het selectie vakje __uw client-IP-adres toevoegen__ om het IP-adres van de client toe te voegen aan het __adres bereik__. U kunt ook het veld __adres bereik__ gebruiken om hand matig het IP-adres van de ontwikkel omgeving in te voeren. Zodra het IP-adres voor de client is toegevoegd, heeft het toegang tot het opslag account met de SDK.

   [![Het deel venster firewalls en virtuele netwerken in de Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>Azure Storage-accounts beveiligen met privé-eind punten

Azure Machine Learning ondersteunt opslag accounts die zijn geconfigureerd voor het gebruik van service-eind punten of privé-eind punten. Als het opslag account gebruikmaakt van privé-eind punten, moet u twee persoonlijke eind punten configureren voor uw standaard-opslag account:
1. Een persoonlijk eind punt met een subresource voor een **BLOB** -doel.
1. Een persoonlijk eind punt met een **Bestands** doel-subresource (file share).

![Scherm opname van pagina met persoonlijke eindpunt configuratie met Blob en bestands opties](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

Als u een persoonlijk eind punt wilt configureren voor een opslag account dat **niet** de standaard opslag is, selecteert u het **doel bron** type dat overeenkomt met het opslag account dat u wilt toevoegen.

Zie [privé-eind punten voor Azure Storage gebruiken](../storage/common/storage-private-endpoints.md) voor meer informatie

## <a name="secure-datastores-and-datasets"></a>Beveiligde gegevens opslag en gegevens sets

In deze sectie leert u hoe u gegevens opslag en gegevens sets kunt gebruiken in de SDK-ervaring met een virtueel netwerk. Zie [Azure machine learning Studio gebruiken in een virtueel netwerk](how-to-enable-studio-virtual-network.md)voor meer informatie over de Studio-ervaring.

Voor toegang tot gegevens met behulp van de SDK moet u de verificatie methode gebruiken die vereist is voor de afzonderlijke service waarin de gegevens zijn opgeslagen. Als u bijvoorbeeld een gegevens opslag voor toegang tot Azure Data Lake Store Gen2 registreert, moet u nog steeds een Service-Principal gebruiken zoals beschreven in [verbinding maken met Azure Storage-services](how-to-access-data.md#azure-data-lake-storage-generation-2).

### <a name="disable-data-validation"></a>Gegevens validatie uitschakelen

Azure Machine Learning voert standaard gegevens over geldigheid en referenties controles uit wanneer u gegevens probeert te openen met behulp van de SDK. Als de gegevens zich achter een virtueel netwerk bevindt, kan Azure Machine Learning deze controles niet volt ooien. Als u deze controle wilt overs Laan, moet u data stores en gegevens sets maken die validatie overs Laan.

### <a name="use-datastores"></a>Gegevens opslag gebruiken

 Azure Data Lake Store gen1 en Azure Data Lake Store Gen2 de validatie standaard overs Laan, dus er is geen verdere actie nodig. Voor de volgende services kunt u echter soort gelijke syntaxis gebruiken om de validatie van gegevens opslag over te slaan:

- Azure Blob Storage
- Azure-bestands share
- PostgreSQL
- Azure SQL Database

Met het volgende code voorbeeld maakt u een nieuwe Azure Blob-gegevens opslag en-sets `skip_validation=True` .

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>Gegevens sets gebruiken

De syntaxis voor het overs laan van de validatie van de gegevensset is vergelijkbaar met de volgende typen gegevens sets:
- Bestand met scheidings tekens
- JSON 
- Parquet
- SQL
- File

Met de volgende code wordt een nieuwe JSON-gegevensset en-sets gemaakt `validate=False` .

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```

## <a name="secure-azure-key-vault"></a>Beveiligde Azure Key Vault

Azure Machine Learning maakt gebruik van een Key Vault exemplaar om de volgende referenties op te slaan:
* Het gekoppelde opslag account connection string
* Wacht woorden voor Azure container repository-instanties
* Verbindings reeksen naar gegevens archieven

Als u Azure Machine Learning experimenten wilt gebruiken met Azure Key Vault achter een virtueel netwerk, gebruikt u de volgende stappen:

1. Ga naar de Key Vault die aan de werk ruimte is gekoppeld.

1. Selecteer op de pagina __Key Vault__ in het linkerdeel venster __netwerken__.

1. Voer op het tabblad __firewalls en virtuele netwerken__ de volgende acties uit:
    1. Selecteer onder __toegang toestaan vanuit__ de optie __persoonlijk eind punt en geselecteerde netwerken__.
    1. Selecteer onder __virtuele netwerken__ __bestaande virtuele netwerken toevoegen__ om het virtuele netwerk toe te voegen waarin uw experimenten worden berekend.
    1. Onder __vertrouwde micro soft-Services toestaan deze firewall te omzeilen__, selecteert u __Ja__.

   [![De sectie firewalls en virtuele netwerken in het deel venster Key Vault](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Azure Container Registry inschakelen (ACR)

Als u Azure Container Registry binnen een virtueel netwerk wilt gebruiken, moet u voldoen aan de volgende vereisten:

* Uw Azure Container Registry moet Premium-versie zijn. Zie [wijzigen van sku's](../container-registry/container-registry-skus.md#changing-tiers)voor meer informatie over het uitvoeren van upgrades.

* Uw Azure Container Registry moeten zich in hetzelfde virtuele netwerk en subnet bevinden als het opslag account en reken doelen die worden gebruikt voor training of deinterferentie.

* Uw Azure Machine Learning-werk ruimte moet een [Azure machine learning Compute-Cluster](how-to-create-attach-compute-cluster.md)bevatten.

    Wanneer ACR zich achter een virtueel netwerk bevindt, kan Azure Machine Learning het niet gebruiken om docker-installatie kopieën rechtstreeks te bouwen. In plaats daarvan wordt het berekenings cluster gebruikt voor het bouwen van de installatie kopieën.

    > [!IMPORTANT]
    > Het reken cluster dat wordt gebruikt om docker-installatie kopieën te bouwen, moet toegang hebben tot de pakket opslagplaatsen die worden gebruikt voor het trainen en implementeren van uw modellen. Mogelijk moet u netwerk beveiligings regels toevoegen die toegang tot open bare opslag plaatsen toestaan, [persoonlijke Python-pakketten gebruiken](how-to-use-private-python-packages.md)of [aangepaste docker-installatie kopieën](how-to-train-with-custom-image.md) gebruiken die al de pakketten bevatten.

Als aan deze vereisten wordt voldaan, gebruikt u de volgende stappen om Azure Container Registry in te scha kelen.

> [!TIP]
> Als u geen bestaande Azure Container Registry hebt gebruikt bij het maken van de werk ruimte, bestaat er mogelijk niet. In de werk ruimte wordt standaard geen ACR-exemplaar gemaakt totdat dit is vereist. Als u het maken van een model wilt forceren, traint of implementeert u uw werk ruimte met behulp van de stappen in deze sectie.

1. Zoek de naam van de Azure Container Registry voor uw werk ruimte met behulp van een van de volgende methoden:

    __Azure-portal__

    Vanuit het gedeelte Overzicht van uw werk ruimte koppelt de __register__ waarde aan de Azure container Registry.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry voor de werk ruimte" border="true":::

    __Azure-CLI__

    Als u [de machine learning extensie voor Azure cli hebt geïnstalleerd](reference-azure-machine-learning-cli.md), kunt u de `az ml workspace show` opdracht gebruiken om de werkruimte gegevens weer te geven.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Met deze opdracht wordt een waarde geretourneerd die vergelijkbaar is met `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` . Het laatste deel van de teken reeks is de naam van de Azure Container Registry voor de werk ruimte.

1. Beperk de toegang tot uw virtuele netwerk met behulp van de stappen in [netwerk toegang configureren voor het REGI ster](../container-registry/container-registry-vnet.md#configure-network-access-for-registry). Wanneer u het virtuele netwerk toevoegt, selecteert u het virtuele netwerk en subnet voor uw Azure Machine Learning-resources.

1. Configureer de ACR voor de werk ruimte om [toegang door vertrouwde services toe te staan](../container-registry/allow-access-trusted-services.md).

1. Gebruik de Azure Machine Learning python-SDK om een berekenings cluster te configureren voor het bouwen van docker-installatie kopieën. In het volgende code fragment ziet u hoe u dit doet:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    # To switch back to using ACR to build (if ACR is not in the VNet):
    # ws.update(image_build_compute = None)
    ```

    > [!IMPORTANT]
    > Uw opslag account, reken cluster en Azure Container Registry moeten zich allemaal in hetzelfde subnet van het virtuele netwerk bevinden.
    
    Zie voor meer informatie de verwijzing naar methode [Update ()](/python/api/azureml-core/azureml.core.workspace.workspace#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) .

## <a name="next-steps"></a>Volgende stappen

Dit artikel is deel twee van een virtuele netwerk reeks van vijf delen. Raadpleeg de rest van de artikelen voor meer informatie over het beveiligen van een virtueel netwerk:

* [Deel 1: overzicht van virtueel netwerk](how-to-network-security-overview.md)
* [Deel 3: de trainings omgeving beveiligen](how-to-secure-training-vnet.md)
* [Deel 4: de omgeving voor het afwijzen van interferentie beveiligen](how-to-secure-inferencing-vnet.md)
* [Deel 5: de functionaliteit van Studio inschakelen](how-to-enable-studio-virtual-network.md)

Zie ook het artikel over het gebruik van [aangepaste DNS](how-to-custom-dns.md) voor naam omzetting.