---
title: 'Quickstart: uw eerste Python-query'
description: In deze quickstart voert u de stappen uit om de Resource Graph-bibliotheek voor Python in te schakelen en uw eerste query uit te voeren.
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 6fc7b8f6a6fa7cde8d10dec6a4b2b834d41325e6
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920119"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-python"></a>Quickstart: uw eerste Resource Graph-query uitvoeren met Python

De eerste stap voor het gebruik van Azure Resource Graph bestaat uit het controleren of de vereiste bibliotheken voor Python zijn geïnstalleerd. In deze quickstart doorloopt u het proces voor het toevoegen van de bibliotheken aan uw Python-installatie.

Aan het einde van dit proces hebt u de bibliotheken toegevoegd aan uw Python-installatie en hebt u uw eerste Resource Graph-query uitgevoerd.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>De Resource Graph-bibliotheek toevoegen

De bibliotheek moet worden toegevoegd opdat Python query's kan uitvoeren voor Azure Resource Graph. Deze bibliotheek werkt waar Python kan worden gebruikt, inclusief [bash in Windows 10](/windows/wsl/install-win10) of lokaal geïnstalleerd.

1. Controleer of de meest recente versie van Python is geïnstalleerd (minimaal **3.8**). Als deze nog niet is geïnstalleerd, downloadt u deze op [Python.org](https://www.python.org/downloads/).

1. Controleer of de meest recente Azure CLI is geïnstalleerd (minimaal **2.5.1**). Als deze nog niet is geïnstalleerd, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Azure CLI is vereist om Python in te schakelen voor gebruik van de **op CLI gebaseerde verificatie** in de volgende voorbeelden. Zie [Authenticate using the Azure management libraries for Python](/azure/developer/python/azure-sdk-authenticate) (Verificatie met de Azure-beheerbibliotheken voor Python) voor meer informatie over andere opties.

1. Verifieer via Azure CLI.

   ```azurecli
   az login
   ```

1. Installeer in de gewenste Python-omgeving de vereiste bibliotheken voor Azure Resource Graph:

   ```bash
   # Add the Resource Graph library for Python
   pip install azure-mgmt-resourcegraph

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Als Python is geïnstalleerd voor alle gebruikers, moet deze opdracht worden uitgevoerd vanaf een console met verhoogde bevoegdheden.

1. Controleer of de bibliotheken zijn geïnstalleerd. `azure-mgmt-resourcegraph` moet **2.0.0** of hoger zijn, `azure-mgmt-resource` moet **9.0.0** of hoger zijn en `azure-cli-core` moet **2.5.0** of hoger zijn.

   ```bash
   # Check each installed library
   pip show azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
   ```

## <a name="run-your-first-resource-graph-query"></a>Uw eerste Resource Graph-query uitvoeren

Nu de Python-bibliotheken zijn toegevoegd aan uw gewenste omgeving, kunt u een eenvoudige Resource Graph-query uitvoeren. De query retourneert de eerste vijf Azure-resources met de **naam** en het **resourcetype** van elke resource.

1. Voer als volgt uw eerste Azure Resource Graph-query uit met de geïnstalleerde bibliotheken en de `resources`-methode:

   ```python
   # Import Azure Resource Graph library
   import azure.mgmt.resourcegraph as arg
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import SubscriptionClient
   
   # Wrap all the work in a function
   def getresources( strQuery ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create Azure Resource Graph client and set options
       argClient = get_client_from_cli_profile(arg.ResourceGraphClient)
       argQueryOptions = arg.models.QueryRequestOptions(result_format="objectArray")
       
       # Create query
       argQuery = arg.models.QueryRequest(subscriptions=subsList, query=strQuery, options=argQueryOptions)
       
       # Run query
       argResults = argClient.resources(argQuery)
   
       # Show Python object
       print(argResults)
   
   getresources("Resources | project name, type | limit 5")
   ```

   > [!NOTE]
   > Omdat deze voorbeeldquery geen sorteermodificator geeft, bijvoorbeeld `order by`, zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd.

1. Werk de aanroep bij naar `getresources` en wijzig de query om te `order by` op de eigenschap **Naam**:

   ```python
   getresources("Resources | project name, type | limit 5 | order by name asc")
   ```

   > [!NOTE]
   > Net als bij de eerste query zal deze query waarschijnlijk per aanvraag een andere set resources opleveren als de query meerdere keren wordt uitgevoerd. De volgorde van de queryopdrachten is belangrijk. In dit voorbeeld komt `order by` na `limit`. Met deze opdracht worden de queryresultaten eerst beperkt en vervolgens gerangschikt.

1. Werk de aanroep bij naar `getresources` en wijzig de query om eerst te `order by` de eigenschap **Naam** en daarna de resultaten van de top vijf te `limit`:

   ```python
   getresources("Resources | project name, type | order by name asc | limit 5")
   ```

Wanneer de laatste query meerdere keren wordt uitgevoerd, ervan uitgaande dat niets in uw omgeving verandert, zijn de geretourneerde resultaten consistent en gesorteerd op de eigenschap **Naam**, maar nog steeds beperkt tot de top vijf.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de geïnstalleerde bibliotheken uit uw Python-omgeving wilt verwijderen, kunt u dit doen met de volgende opdracht:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-resourcegraph azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de Resource Graph-bibliotheken toegevoegd aan uw Python-omgeving en uw eerste query uitgevoerd. Ga verder naar de pagina met details van de querytaal voor meer informatie over de taal van Resource Graph.

> [!div class="nextstepaction"]
> [Meer informatie over de querytaal](./concepts/query-language.md)
