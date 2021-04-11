---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 59359d37fe347c8568c7944f75accdbc04cddb93
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105967116"
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

    ```bash
    export KEY_VAULT_NAME=<your-unique-keyvault-name>
    ```