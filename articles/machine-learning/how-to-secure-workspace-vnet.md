---
title: Een werkruimte Azure Machine Learning beveiligen met virtuele netwerken
titleSuffix: Azure Machine Learning
description: Gebruik een geïsoleerde Azure-Virtual Network om uw Azure Machine Learning en bijbehorende resources te beveiligen.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 03/17/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 20f75580c425cee9128f9c94123dcf902642eac4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531032"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>Een werkruimte Azure Machine Learning beveiligen met virtuele netwerken

In dit artikel leert u hoe u een werkruimte Azure Machine Learning de bijbehorende resources in een virtueel netwerk kunt beveiligen.

Dit artikel is deel twee van een vijfdelige reeks die u door het beveiligen van een werkstroom Azure Machine Learning helpen. We raden u ten zeerste aan deel [één: VNet-overzicht te](how-to-network-security-overview.md) lezen om eerst inzicht te krijgen in de algehele architectuur. 

Zie de andere artikelen in deze reeks:

[1. VNet-overzicht](how-to-network-security-overview.md)  >  **2. Beveilig de werkruimte**  >  [3. Beveilig de trainingsomgeving](how-to-secure-training-vnet.md)  >  [4. Beveilig de deferencingomgeving](how-to-secure-inferencing-vnet.md)  >  [5. Studio-functionaliteit inschakelen](how-to-enable-studio-virtual-network.md)

In dit artikel leert u hoe u de volgende werkruimte-resources in een virtueel netwerk kunt inschakelen:
> [!div class="checklist"]
> - Azure Machine Learning-werkruimte
> - Azure Storage-accounts
> - Azure Machine Learning en gegevenssets
> - Azure Key Vault
> - Azure Container Registry

## <a name="prerequisites"></a>Vereisten

+ Lees het artikel [Overzicht van netwerkbeveiliging](how-to-network-security-overview.md) voor meer informatie over veelvoorkomende scenario's voor virtuele netwerken en de algehele architectuur van virtuele netwerken.

+ Een bestaand virtueel netwerk en subnet voor gebruik met uw rekenbronnen.

+ Als u resources wilt implementeren in een virtueel netwerk of subnet, moet uw gebruikersaccount machtigingen hebben voor de volgende acties in op rollen gebaseerd toegangsbeheer van Azure (Azure RBAC):

    - 'Microsoft.Network/virtualNetworks/join/action' op de resource van het virtuele netwerk.
    - 'Microsoft.Network/virtualNetworks/subnet/join/action' op de subnetresource.

    Zie Ingebouwde netwerkrollen voor meer informatie over Azure RBAC [met netwerken](../role-based-access-control/built-in-roles.md#networking)


## <a name="secure-the-workspace-with-private-endpoint"></a>De werkruimte beveiligen met een privé-eindpunt

Azure Private Link kunt u verbinding maken met uw werkruimte met behulp van een privé-eindpunt. Het privé-eindpunt is een set privé-IP-adressen binnen uw virtuele netwerk. Vervolgens kunt u de toegang tot uw werkruimte beperken tot alleen de privé-IP-adressen. Private Link helpt het risico op gegevens exfiltratie te verminderen.

Zie Voor meer informatie over het instellen van Private Link werkruimte [How to configure Private Link](how-to-configure-private-link.md).

> [!Warning]
> Het beveiligen van een werkruimte met privé-eindpunten zorgt niet voor end-to-end-beveiliging op zichzelf. U moet de stappen in de rest van dit artikel en de VNet-serie volgen om afzonderlijke onderdelen van uw oplossing te beveiligen.

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>Azure-opslagaccounts beveiligen met service-eindpunten

Azure Machine Learning ondersteunt opslagaccounts die zijn geconfigureerd voor het gebruik van service-eindpunten of privé-eindpunten. In deze sectie leert u hoe u een Azure-opslagaccount kunt beveiligen met behulp van service-eindpunten. Zie de volgende sectie voor privé-eindpunten.

Als u een Azure-opslagaccount wilt gebruiken voor de werkruimte in een virtueel netwerk, gebruikt u de volgende stappen:

1. Ga in Azure Portal naar de opslagservice die u in uw werkruimte wilt gebruiken.

   [![De opslag die is gekoppeld aan de Azure Machine Learning werkruimte](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. Selecteer netwerken op de pagina __opslagserviceaccount.__

   ![Het netwerkgebied op Azure Storage pagina in de Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. Ga als volgt te werk op het tabblad __Firewalls__ en virtuele netwerken:
    1. Selecteer __Geselecteerde netwerken__.
    1. Selecteer __onder Virtuele netwerken__ de koppeling Bestaand virtueel __netwerk__ toevoegen. Met deze actie wordt het virtuele netwerk toegevoegd waar uw rekencapaciteit zich bevindt (zie stap 1).

        > [!IMPORTANT]
        > Het opslagaccount moet zich in hetzelfde virtuele netwerk en subnet als de reken-exemplaren of clusters die worden gebruikt voor training of de deferie.

    1. Schakel het __selectievakje Vertrouwde Microsoft-services toegang tot dit opslagaccount toestaan__ in. Deze wijziging geeft niet alle Azure-services toegang tot uw opslagaccount.
    
        * Resources van sommige services, **geregistreerd in uw abonnement,** hebben toegang tot het opslagaccount **in hetzelfde abonnement voor** bepaalde bewerkingen. Bijvoorbeeld het schrijven van logboeken of het maken van back-ups.
        * Resources van sommige services kunnen expliciete toegang tot uw opslagaccount krijgen door een __Azure-rol__ toe te wijzen aan de door het systeem toegewezen beheerde identiteit.

        Raadpleeg [Firewalls en virtuele netwerken voor Azure Storage configureren](../storage/common/storage-network-security.md#trusted-microsoft-services) voor meer informatie.

    > [!IMPORTANT]
    > Wanneer u met de Azure Machine Learning SDK werkt, moet uw ontwikkelomgeving verbinding kunnen maken met het Azure Storage account. Wanneer het opslagaccount zich in een virtueel netwerk bevinden, moet de firewall toegang toestaan vanaf het IP-adres van de ontwikkelomgeving.
    >
    > Als u toegang tot het opslagaccount wilt inschakelen, gaat u naar de __Firewalls__ en virtuele netwerken voor het opslagaccount vanuit *een webbrowser op de ontwikkelclient*. Gebruik vervolgens het __selectievakje IP-adres van client__ toevoegen om het IP-adres van de client toe te voegen aan het __ADRESBEREIK.__ U kunt ook het veld __ADRESBEREIK__ gebruiken om handmatig het IP-adres van de ontwikkelomgeving in te voeren. Zodra het IP-adres voor de client is toegevoegd, heeft het toegang tot het opslagaccount via de SDK.

   [![Het deelvenster Firewalls en virtuele netwerken in de Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>Azure-opslagaccounts beveiligen met privé-eindpunten

Azure Machine Learning ondersteunt opslagaccounts die zijn geconfigureerd voor het gebruik van service-eindpunten of privé-eindpunten. Als het opslagaccount privé-eindpunten gebruikt, moet u twee privé-eindpunten configureren voor uw standaardopslagaccount:
1. Een privé-eindpunt met een **subresource van** een blobdoel.
1. Een privé-eindpunt met **een** subresource van het bestandsdoel (bestandsshare).

![Schermopname van de configuratiepagina van het privé-eindpunt met blob- en bestandsopties](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

Als u een privé-eindpunt wilt configureren voor een opslagaccount dat niet de standaardopslag **is,** selecteert u het type **doelsubresource** dat overeenkomt met het opslagaccount dat u wilt toevoegen.

Zie Privé-eindpunten [gebruiken voor meer informatie Azure Storage](../storage/common/storage-private-endpoints.md)

## <a name="secure-datastores-and-datasets"></a>Gegevensstores en gegevenssets beveiligen

In deze sectie leert u hoe u gegevensstores en gegevenssets gebruikt in de SDK-ervaring met een virtueel netwerk. Zie Use Azure Machine Learning-studio in a virtual network (Virtuele Azure Machine Learning-studio [gebruiken in een virtueel netwerk) voor meer informatie over de studio-ervaring.](how-to-enable-studio-virtual-network.md)

Als u toegang wilt krijgen tot gegevens met behulp van de SDK, moet u de verificatiemethode gebruiken die is vereist voor de afzonderlijke service waarin de gegevens worden opgeslagen. Als u bijvoorbeeld een gegevensopslag registreert voor toegang tot Azure Data Lake Store Gen2, moet u nog steeds een service-principal gebruiken zoals beschreven in Verbinding maken met [Azure Storage-services.](how-to-access-data.md#azure-data-lake-storage-generation-2)

### <a name="disable-data-validation"></a>Gegevensvalidatie uitschakelen

Standaard voert Azure Machine Learning geldigheid van gegevens en referentiecontroles uit wanneer u probeert toegang te krijgen tot gegevens met behulp van de SDK. Als de gegevens zich achter een virtueel netwerk Azure Machine Learning deze controles niet voltooien. Als u deze controle wilt overslaan, moet u gegevensstores en gegevenssets maken die de validatie overslaan.

### <a name="use-datastores"></a>Gegevensstores gebruiken

 Azure Data Lake Store Gen1 en Azure Data Lake Store Gen2 slaan validatie standaard over, zodat er geen verdere actie hoeft te worden ondernomen. Voor de volgende services kunt u echter vergelijkbare syntaxis gebruiken om gegevensstorevalidatie over te slaan:

- Azure Blob Storage
- Azure-bestandsshare
- PostgreSQL
- Azure SQL Database

Met het volgende codevoorbeeld maakt u een nieuw Azure Blob-gegevensopslag en stelt u `skip_validation=True` in.

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>Gegevenssets gebruiken

De syntaxis voor het overslaan van de validatie van gegevenssets is vergelijkbaar voor de volgende typen gegevenssets:
- Bestand met scheidingstekens
- JSON 
- Parquet
- SQL
- File

Met de volgende code maakt u een nieuwe JSON-gegevensset en stelt u `validate=False` in.

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```

## <a name="secure-azure-key-vault"></a>Beveiligde Azure Key Vault

Azure Machine Learning gebruikt een gekoppelde Key Vault voor het opslaan van de volgende referenties:
* Het bijbehorende opslagaccount connection string
* Wachtwoorden voor exemplaren van Azure Container Repository
* Verbindingsreeksen met gegevensopslag

Gebruik de Azure Machine Learning om experimenten uit te voeren Azure Key Vault achter een virtueel netwerk:

1. Ga naar de Key Vault die aan de werkruimte is gekoppeld.

1. Selecteer op __Key Vault__ pagina in het linkerdeelvenster de optie __Netwerken.__

1. Ga als volgt te werk op het tabblad __Firewalls__ en virtuele netwerken:
    1. Selecteer __onder Toegang toestaan vanuit__ de optie __Privé-eindpunt en geselecteerde netwerken.__
    1. Selecteer __onder Virtuele netwerken__ de optie Bestaande virtuele netwerken toevoegen __om__ het virtuele netwerk toe te voegen waar uw experimenten-berekening zich bevindt.
    1. Selecteer __ja onder Microsoft-services om deze firewall over__ te __gaan.__

   [![De sectie Firewalls en virtuele netwerken in het deelvenster Key Vault maken](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>ACR (Azure Container Registry) inschakelen

Als u Azure Container Registry in een virtueel netwerk wilt gebruiken, moet u voldoen aan de volgende vereisten:

* Uw Azure Container Registry moet de Premium-versie zijn. Zie SKU's wijzigen voor meer [informatie over het upgraden.](../container-registry/container-registry-skus.md#changing-tiers)

* Uw Azure Container Registry zich in hetzelfde virtuele netwerk en subnet als het opslagaccount en de rekendoelen die worden gebruikt voor training of de deferie.

* Uw Azure Machine Learning moet een Azure Machine Learning [rekencluster bevatten.](how-to-create-attach-compute-cluster.md)

    Wanneer ACR zich achter een virtueel netwerk Azure Machine Learning het niet gebruiken om rechtstreeks Docker-afbeeldingen te bouwen. In plaats daarvan wordt het rekencluster gebruikt om de afbeeldingen te bouwen.

    > [!IMPORTANT]
    > Het rekencluster dat wordt gebruikt om Docker-afbeeldingen te bouwen, moet toegang hebben tot de pakket-opslagplaatsen die worden gebruikt om uw modellen te trainen en te implementeren. Mogelijk moet u netwerkbeveiligingsregels toevoegen die toegang tot openbare repatriëring toestaan, persoonlijke [Python-pakketten](how-to-use-private-python-packages.md)gebruiken of aangepaste [Docker-afbeeldingen](how-to-train-with-custom-image.md) gebruiken die al de pakketten bevatten.

Zodra aan deze vereisten is voldaan, gebruikt u de volgende stappen om de Azure Container Registry.

> [!TIP]
> Als u geen bestaande werkruimte hebt Azure Container Registry bij het maken van de werkruimte, bestaat er mogelijk geen werkruimte. De werkruimte maakt standaard pas een ACR-exemplaar als deze er een nodig heeft. Als u het maken van een model wilt forceer, traint of implementeert u een model met behulp van uw werkruimte voordat u de stappen in deze sectie gebruikt.

1. Zoek de naam van de Azure Container Registry voor uw werkruimte met behulp van een van de volgende methoden:

    __Azure-portal__

    In de overzichtssectie van uw werkruimte wordt __de waarde Register__ aan de Azure Container Registry.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry voor de werkruimte" border="true":::

    __Azure-CLI__

    Als u de [extensie Machine Learning Azure CLI](reference-azure-machine-learning-cli.md)hebt geïnstalleerd, kunt u de opdracht gebruiken om de `az ml workspace show` werkruimtegegevens weer te geven.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Deze opdracht retourneert een waarde die vergelijkbaar is met `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` . Het laatste deel van de tekenreeks is de naam van de Azure Container Registry voor de werkruimte.

1. Beperk de toegang tot uw virtuele netwerk met behulp van de stappen in [Netwerktoegang voor register configureren.](../container-registry/container-registry-vnet.md#configure-network-access-for-registry) Wanneer u het virtuele netwerk toevoegt, selecteert u het virtuele netwerk en het subnet voor uw Azure Machine Learning resources.

1. Configureer de ACR voor de werkruimte op [Toegang door vertrouwde services toestaan.](../container-registry/allow-access-trusted-services.md)

1. Gebruik de python Azure Machine Learning-SDK om een rekencluster te configureren voor het bouwen van Docker-afbeeldingen. Het volgende codefragment laat zien hoe u dit doet:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    # To switch back to using ACR to build (if ACR is not in the VNet):
    # ws.update(image_build_compute = '')
    ```

    > [!IMPORTANT]
    > Uw opslagaccount, rekencluster en Azure Container Registry moeten zich allemaal in hetzelfde subnet van het virtuele netwerk.
    
    Zie de naslaginformatie over [de update()-methode](/python/api/azureml-core/azureml.core.workspace.workspace#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Dit artikel is deel twee van een vijfdelige reeks virtuele netwerken. Zie de rest van de artikelen voor meer informatie over het beveiligen van een virtueel netwerk:

* [Deel 1: Overzicht van virtueel netwerk](how-to-network-security-overview.md)
* [Deel 3: De trainingsomgeving beveiligen](how-to-secure-training-vnet.md)
* [Deel 4: De deferencingomgeving beveiligen](how-to-secure-inferencing-vnet.md)
* [Deel 5: Studio-functionaliteit inschakelen](how-to-enable-studio-virtual-network.md)

Zie ook het artikel over het gebruik van [aangepaste DNS](how-to-custom-dns.md) voor naamresolutie.
