---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 894a89126d1ee3ed909134f3e0dd914166568654
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90606149"
---
U kunt de wachtrij bekijken in de [Azure Portal](../articles/storage/queues/storage-quickstart-queues-portal.md) of in de [Microsoft Azure Storage Explorer](https://storageexplorer.com/). U kunt de wachtrij ook bekijken in de Azure CLI, zoals wordt beschreven in de volgende stappen:

1. Open het bestand *local.setting.json* van het functieproject en kopieer de verbindingsreekswaarde. Voer in een terminal- of opdrachtvenster de volgende opdracht uit om een omgevingsvariabele met de naam `AZURE_STORAGE_CONNECTION_STRING` te maken en uw specifieke verbindingsreeks in plaats van `<MY_CONNECTION_STRING>` te plakken. (Deze omgevingsvariabele zorgt ervoor dat u de verbindingsreeks niet hoeft op te geven bij elke volgende opdracht met het argument `--connection-string`.)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    export AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Azure-CLI](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (Optioneel) Gebruik de opdracht [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) om de opslagwachtrijen in uw account te bekijken. De uitvoer van deze opdracht moet een wachtrij bevatten met de naam `outqueue`, die is gemaakt toen de functie het eerste bericht naar die wachtrij heeft geschreven.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. Gebruik de opdracht [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) om het bericht uit deze wachtrij te lezen. Dit moet de voornaam zijn die u eerder hebt gebruikt bij het testen van de functie. Met deze opdracht leest en verwijdert u het eerste bericht in de wachtrij. 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Azure-CLI](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    Dit script maakt gebruik van certutil voor het decoderen van de met base64 gecodeerde berichtenverzameling uit een lokaal tijdelijk bestand. Als er geen uitvoer is, probeert u `> NUL` uit het script te verwijderen om het onderdrukken van certutil-uitvoer te stoppen voor het geval dat er een fout is opgetreden. 
    
    ---
    
    Omdat de berichttekst [base64-gecodeerd](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding) is opgeslagen, moet het bericht worden gedecodeerd voordat het kan worden weergegeven. Nadat u `az storage message get` hebt uitgevoerd, wordt het bericht uit de wachtrij verwijderd. Als er slechts één bericht in `outqueue` was, wordt er geen bericht weergegeven wanneer u deze opdracht een tweede keer uitvoert en wordt in plaats daarvan een foutmelding weergegeven.
