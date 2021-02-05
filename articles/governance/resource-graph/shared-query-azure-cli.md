---
title: 'Quickstart: Een gedeelde query maken met Azure CLI'
description: In deze quickstart volgt u de stappen om de Resource Graph-extensie voor Azure CLI in te schakelen en een gedeelde query te maken.
ms.date: 02/05/2021
ms.topic: quickstart
ms.openlocfilehash: ec1b010771790339a13777624f04c7bd2db01f11
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594363"
---
# <a name="quickstart-create-a-resource-graph-shared-query-using-azure-cli"></a>Quickstart: Een gedeelde Resource Graph-query maken met Azure CLI

De eerste stap voor het gebruik van Azure Resource Graph met [Azure CLI](/cli/azure/) bestaat uit het controleren of de extensie is geïnstalleerd. In deze snelstartgids doorloopt u het proces voor het toevoegen van de extensie aan uw Azure CLI-installatie. U kunt de extensie gebruiken met een lokale installatie van Azure CLI of via de [Azure Cloud Shell](https://shell.azure.com).

Aan het einde van dit proces hebt u de extensie toegevoegd aan de gewenste Azure CLI-installatie en hebt u een gedeelde Resource Graph-query gemaakt.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-extension"></a>De Resource Graph-extensie toevoegen

De extensie moet worden toegevoegd opdat Azure CLI kan werken met Azure Resource Graph. Deze extensie werkt overal waar Azure CLI kan worden gebruikt, met inbegrip van [bash in Windows 10](/windows/wsl/install-win10), [Cloud Shell](https://shell.azure.com) (zowel zelfstandige als in de portal), de [Azure CLI Docker-installatiekopie](https://hub.docker.com/_/microsoft-azure-cli), of lokaal geïnstalleerd.

1. Controleer of de meest recente versie van Azure CLI is geïnstalleerd (minimaal **2.8.0**). Als deze nog niet is geïnstalleerd, volgt u [deze instructies](/cli/azure/install-azure-cli-windows) op.

1. Gebruik [az extention add](/cli/azure/extension#az_extension_add) in uw gewenste Azure CLI-omgeving om de Resource Graph-extensie te importeren met de volgende opdracht:

   ```azurecli-interactive
   # Add the Resource Graph extension to the Azure CLI environment
   az extension add --name resource-graph
   ```

1. Controleer of de extensie is geïnstalleerd en of deze de verwachte versie heeft (ten minste **1.1.0**) met [az extentions list](/cli/azure/extension#az_extension_list):

   ```azurecli-interactive
   # Check the extension list (note that you may have other extensions installed)
   az extension list

   # Run help for graph query options
   az graph query -h
   ```

## <a name="create-a-resource-graph-shared-query"></a>Een gedeelde Resource Graph-query maken

Nu de Azure CLI-extensie is toegevoegd aan uw gewenste omgeving, kunt u een gedeelde Resource Graph-query uitproberen. De gedeelde query is een Azure Resource Manager-object waaraan u machtigingen kunt verlenen of dat u kunt uitvoeren in Azure Resource Graph Explorer. De query biedt een overzicht van het aantal resources, gegroepeerd op _locatie_.

1. Maak een resourcegroep met [az group create](/cli/azure/group#az_group_create) om de gedeelde Azure Resource Graph-query op te slaan. Deze resourcegroep heet `resource-graph-queries` en de locatie is `westus2`.

   ```azurecli-interactive
   # Login first with az login if not using Cloud Shell

   # Create the resource group
   az group create --name 'resource-graph-queries' --location 'westus2'
   ```

1. Maak de gedeelde Azure Resource Graph-query met behulp van de `graph`-extensie en de opdracht [az graph shared-query create](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_create):

   ```azurecli-interactive
   # Create the Azure Resource Graph shared query
   az graph shared-query create --name 'Summarize resources by location' \
      --description 'This shared query summarizes resources by location for a pinnable map graphic.' \
      --graph-query 'Resources | summarize count() by location' \
      --resource-group 'resource-graph-queries'
   ```

1. Geef de gedeelde query's op in de nieuwe resourcegroep. De [AZ Graph Shared-query List](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_list) opdracht retourneert een matrix met waarden.

   ```azurecli-interactive
   # List all the Azure Resource Graph shared queries in a resource group
   az graph shared-query list --resource-group 'resource-graph-queries'
   ```

1. Als u slechts één gedeeld queryresultaat wilt ontvangen, gebruik dan de opdracht [az graph shared-query show](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_show).

   ```azurecli-interactive
   # Show a specific Azure Resource Graph shared query
   az graph shared-query show --resource-group 'resource-graph-queries' \
      --name 'Summarize resources by location'
   ```

1. Voer de gedeelde query uit in Azure CLI met de `{{shared-query-uri}}`-syntaxis in een opdracht [az graph query](/cli/azure/ext/resource-graph/graph#ext_resource_graph_az_graph_query).
   Kopieer eerst het `id`-veld uit de resultaten van de vorige `show`-opdracht. Vervang de `shared-query-uri`-tekst in het voorbeeld door de waarde uit het veld `id`, maar laat de omliggende tekens `{{` en `}}` staan.

   ```azurecli-interactive
   # Run a Azure Resource Graph shared query
   az graph query --graph-query "{{shared-query-uri}}"
   ```

   > [!NOTE]
   > De syntaxis `{{shared-query-uri}}` is een **Preview**-functie.

Een andere manier om gedeelde Resource Graph-query's te vinden, is via het Azure-portal. Gebruik de zoekbalk van het portal om te zoeken naar 'Resource Graph-query's'. Selecteer de gedeelde query. De opgeslagen query wordt weergegeven onder het tabblad **Query** op de pagina **Overzicht**. De knop **Bewerken** opent deze in [Resource Graph Explorer](./first-query-portal.md).

## <a name="clean-up-resources"></a>Resources opschonen

Als u de gedeelde Resource Graph-query, -resourcegroep en -extensie uit uw Azure CLI-omgeving wilt verwijderen, kunt u dit doen met de volgende opdracht:

- [az graph shared-query delete](/cli/azure/ext/resource-graph/graph/shared-query#ext_resource_graph_az_graph_shared_query_delete)
- [az group delete](/cli/azure/group#az_group_delete)
- [az extension remove](/cli/azure/extension#az_extension_remove)

```azurecli-interactive
# Delete the Azure Resource Graph shared query
az graph shared-query delete --resource-group 'resource-graph-queries' \
   --name 'Summarize resources by location'

# Remove the resource group
# WARNING: This command deletes ALL resources you've added to this resource group without prompting for confirmation
az group delete --resource-group 'resource-graph-queries' --yes

# Remove the Azure Resource Graph extension from the Azure CLI environment
az extension remove -n resource-graph
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u de Resource Graph-extensie toegevoegd aan uw Azure CLI-omgeving en een gedeelde query gemaakt. Ga door naar de pagina met details van de querytaal voor meer informatie over de taal van Resource Graph.

> [!div class="nextstepaction"]
> [Meer informatie over de querytaal](./concepts/query-language.md)