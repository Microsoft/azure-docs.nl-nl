---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 5e912b76c2ef68aa01dae57d1b42abc386a8e67b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89482120"
---
1. Gebruik de opdracht `az group create` om een resourcegroep te maken:

    ```azurecli
    az group create --name KeyVault-PythonQS-rg --location eastus
    ```

    Als u liever de 'eastus' wijzigt in een locatie die dichterbij ligt, kan dat ook.

1. Gebruik `az keyvault create` om de sleutelkluis te maken:

    ```azurecli
    az keyvault create --name <your-unique-keyvault-name> --resource-group KeyVault-PythonQS-rg
    ```

    Vervang `<your-unique-keyvault-name>` door een naam die in de volledige Azure-omgeving uniek is. Normaal gesproken gebruikt u uw persoonlijke of bedrijfsnaam samen met andere getallen en id's. 

1. Maak een omgevingsvariabele aan die de naam van de Key Vault levert aan de code:

    # <a name="cmd"></a>[cmd](#tab/cmd)

    ```cmd
    set KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    # <a name="bash"></a>[bash](#tab/bash)

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```

    ---
