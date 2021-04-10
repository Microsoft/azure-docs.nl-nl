---
title: Toegangs sleutels voor opslag accounts wijzigen
titleSuffix: Azure Machine Learning
description: Meer informatie over het wijzigen van de toegangs sleutels voor het Azure Storage-account dat wordt gebruikt door uw werk ruimte. Azure Machine Learning gebruikt een Azure Storage-account om gegevens en modellen op te slaan.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 06/19/2020
ms.openlocfilehash: 787f7727e18b7055055ed2b764ddce5be20f5afc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102503433"
---
# <a name="regenerate-storage-account-access-keys"></a>Toegangs sleutels voor het opslag account opnieuw genereren


Meer informatie over het wijzigen van de toegangs sleutels voor Azure Storage accounts die worden gebruikt door Azure Machine Learning. Azure Machine Learning kunt opslag accounts gebruiken om gegevens of getrainde modellen op te slaan.

Uit veiligheids overwegingen moet u de toegangs sleutels voor een Azure Storage-account mogelijk wijzigen. Wanneer u de toegangs sleutel opnieuw genereert, moet Azure Machine Learning worden bijgewerkt om de nieuwe sleutel te gebruiken. Azure Machine Learning maakt mogelijk gebruik van het opslag account voor zowel model opslag als een gegevens opslag.

> [!IMPORTANT]

> De referenties die zijn geregistreerd bij data stores worden opgeslagen in uw Azure Key Vault gekoppeld aan de werk ruimte. Als u [voorlopig verwijderen](../key-vault/general/soft-delete-overview.md) hebt ingeschakeld voor uw Key Vault, bevat dit artikel instructies voor het bijwerken van referenties. Als u de registratie van de gegevens opslag ongedaan maakt en opnieuw probeert te registreren onder dezelfde naam, mislukt deze actie. Zie [voorlopig verwijderen inschakelen voor een bestaande sleutel kluis]( https://docs.microsoft.com/azure/key-vault/general/soft-delete-change#turn-on-soft-delete-for-an-existing-key-vault) voor het inschakelen van voorlopig verwijderen in dit scenario.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie het artikel [een werk ruimte maken](how-to-manage-workspace.md) voor meer informatie.

* De [Azure machine learning SDK](/python/api/overview/azure/ml/install).

* De [Azure machine learning cli-extensie](reference-azure-machine-learning-cli.md).

> [!NOTE]
> De code fragmenten in dit document zijn getest met versie 1.0.83 van de python-SDK.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Wat moet worden bijgewerkt?

Opslag accounts kunnen worden gebruikt door de Azure Machine Learning-werk ruimte (logboeken, modellen, moment opnamen, enz.) en als gegevens opslag. Het proces voor het bijwerken van de werk ruimte is een enkele Azure CLI-opdracht en kan worden uitgevoerd na het bijwerken van de opslag sleutel. Het proces voor het bijwerken van gegevens opslag is meer betrokken en hiervoor is het vereist dat er wordt gedetecteerd welke data stores momenteel gebruikmaken van het opslag account en vervolgens opnieuw moeten worden geregistreerd.

> [!IMPORTANT]
> Werk de werk ruimte bij met behulp van de Azure CLI en de gegevens opslag in Python, op hetzelfde moment. Het bijwerken van de ene of de andere is niet voldoende en kan fouten veroorzaken totdat beide zijn bijgewerkt.

Gebruik de volgende code om de opslag accounts te detecteren die worden gebruikt door uw gegevens opslag:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Met deze code wordt gezocht naar geregistreerde gegevens opslag die Azure Storage gebruikt en wordt de volgende informatie weer gegeven:

* Data Store-naam: de naam van de gegevens opslag waarvoor het opslag account is geregistreerd.
* Naam van het opslag account: de naam van het Azure Storage-account.
* Container: de container in het opslag account dat wordt gebruikt door deze registratie.

Ook wordt aangegeven of de gegevens opslag voor een Azure-Blob of een Azure-bestands share is, omdat er verschillende methoden zijn om elk type gegevens opslag opnieuw te registreren.

Als er een vermelding bestaat voor het opslag account dat u wilt gebruiken voor het opnieuw genereren van toegangs sleutels voor, slaat u de naam van het gegevens archief, de naam van het opslag account en de naam van de container op.

## <a name="update-the-access-key"></a>De toegangs sleutel bijwerken

Gebruik de volgende stappen om Azure Machine Learning bij te werken voor het gebruik van de nieuwe sleutel:

> [!IMPORTANT]
> Voer alle stappen uit en werk de werk ruimte bij met behulp van de CLI en gegevens opslag met python. Het bijwerken van de ene of de andere kan fouten veroorzaken totdat beide zijn bijgewerkt.

1. Genereer de sleutel opnieuw. Zie [toegangs sleutels voor opslag accounts beheren](../storage/common/storage-account-keys-manage.md)voor meer informatie over het opnieuw genereren van een toegangs sleutel. Sla de nieuwe sleutel op.

1. De nieuwe sleutel wordt na een uur automatisch gesynchroniseerd met de Azure Machine Learning-werk ruimte. Gebruik de volgende stappen om ervoor te zorgen dat de werk ruimte direct wordt gesynchroniseerd met de nieuwe sleutel:

    1. Meld u aan bij het Azure-abonnement dat uw werk ruimte bevat met behulp van de volgende Azure CLI-opdracht:

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. Als u de werk ruimte voor het gebruik van de nieuwe sleutel wilt bijwerken, gebruikt u de volgende opdracht. Vervang door de `myworkspace` naam van uw Azure machine learning werkruimte en vervang door `myresourcegroup` de naam van de Azure-resource groep die de werk ruimte bevat.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        Met deze opdracht worden automatisch de nieuwe sleutels gesynchroniseerd voor het Azure-opslag account dat wordt gebruikt door de werk ruimte.

1. U kunt de gegevens die gebruikmaken van het opslag account opnieuw registreren via de SDK of [de Azure machine learning Studio](https://ml.azure.com).
    1. **Als u gegevens opslag opnieuw wilt registreren via de python-SDK**, gebruikt u de waarden in de sectie [Wat moet worden bijgewerkt](#whattoupdate) en de sleutel uit stap 1 met de volgende code. 
    
        Sinds `overwrite=True` is opgegeven, overschrijft deze code de bestaande registratie en werkt deze bij om de nieuwe sleutel te gebruiken.
    
        ```python
        # Re-register the blob container
        ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                                  datastore_name='your datastore name',
                                                  container_name='your container name',
                                                  account_name='your storage account name',
                                                  account_key='new storage account key',
                                                  overwrite=True)
        # Re-register file shares
        ds_file = Datastore.register_azure_file_share(workspace=ws,
                                              datastore_name='your datastore name',
                                              file_share_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
        
        ```
    
    1. **Als u gegevens opslag opnieuw wilt registreren via Studio**, selecteert u **gegevens opslag** in het linkerdeel venster van de Studio. 
        1. Selecteer de gegevens opslag die u wilt bijwerken.
        1. Selecteer de knop **referenties bijwerken** in de linkerbovenhoek. 
        1. Gebruik uw nieuwe toegangs sleutel uit stap 1 om het formulier in te vullen en klik op **Opslaan**.
        
            Als u de referenties voor uw **standaard gegevens opslag** bijwerkt, voert u deze stap uit en herhaalt u stap 2b om uw nieuwe sleutel opnieuw te synchroniseren met de standaard gegevens opslag van de werk ruimte. 

## <a name="next-steps"></a>Volgende stappen

Zie de verwijzing naar een klasse voor meer informatie over het registreren van gegevens opslag [`Datastore`](/python/api/azureml-core/azureml.core.datastore%28class%29) .