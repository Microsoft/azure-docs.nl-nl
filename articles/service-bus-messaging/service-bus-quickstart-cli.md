---
title: 'Quickstart: de Azure-CLI gebruiken om een Service Bus-wachtrij te maken | Microsoft Docs'
description: In deze quickstart leert u hoe u een Service Bus-naamruimte en vervolgens een wachtrij in die naamruimte maakt met behulp van de Azure CLI.
author: spelluru
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.openlocfilehash: 14892b114f3bb8a0bb6c07f7cdd01a174b931cf9
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95810634"
---
# <a name="use-the-azure-cli-to-create-a-service-bus-namespace-and-a-queue"></a>De Azure CLI gebruiken om een Service Bus-naamruimte en -wachtrij te maken
In deze quickstart wordt beschreven hoe u een Service Bus-naamruimte en -wachtrij maakt met behulp van de Azure CLI. Er wordt ook beschreven hoe u autorisatiereferenties ophaalt die een client-toepassing kan gebruiken voor het verzenden/ontvangen van berichten naar/van de wachtrij. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Vereisten
Als u nog geen abonnement op Azure hebt, kunt u een [gratis account][free account] maken voordat u begint.

In deze quickstart gebruikt u Azure Cloud Shell dat u kunt starten nadat u zich hebt aangemeld bij Azure Portal. Zie [Overzicht van Azure Cloud Shell](../cloud-shell/overview.md) voor meer informatie over Azure Cloud Shell. U kunt Azure PowerShell ook op uw computer [installeren](/cli/azure/install-azure-cli) en gebruiken. 

## <a name="provision-resources"></a>Resources inrichten
1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
2. Start Azure Cloud Shell door het pictogram te selecteren dat wordt weergegeven in de volgende afbeelding. Schakel over naar de **Bash**-modus als Cloud Shell zich in de **PowerShell**-modus bevindt. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell starten":::
3. Voer de volgende opdracht uit om een Azure-resourcegroep te maken. Werk de naam en locatie van de resourcegroep desgewenst bij. 

    ```azurecli-interactive
    az group create --name ContosoRG --location eastus
    ```
4. Voer de volgende opdracht uit om een naamruimte voor Service Bus-berichten te maken.

    ```azurecli-interactive
    az servicebus namespace create --resource-group ContosoRG --name ContosoSBusNS --location eastus
    ```
5. Voer de volgende opdracht uit om een wachtrij te maken in de naamruimte die u in de vorige stap hebt gemaakt. In dit voorbeeld is `ContosoRG` de resourcegroep die u in de vorige stap hebt gemaakt. `ContosoSBusNS` is de naam van de Service Bus-naamruimte die u in die resourcegroep hebt gemaakt. 

    ```azurecli-interactive
    az servicebus queue create --resource-group ContosoRG --namespace-name ContosoSBusNS --name ContosoOrdersQueue
    ```
6. Voer de volgende opdracht uit om de primaire verbindingstekenreeks voor de naamruimte op te halen. U gebruikt deze verbindingstekenreeks om verbinding te maken met de wachtrij en berichten te verzenden en te ontvangen. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group ContosoRG --namespace-name ContosoSBusNS --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    Noteer de verbindingstekenreeks en de naam van de wachtrij. U gebruikt deze om berichten te verzenden en ontvangen. 


## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u een Service Bus-naamruimte en een wachtrij in de naamruimte gemaakt. Raadpleeg een van de volgende quickstarts in het gedeelte **Berichten verzenden en ontvangen** om te zien hoe berichten worden verzonden/ontvangen naar/van de wachtrij. 

- [.NET](service-bus-dotnet-get-started-with-queues.md)
- [Java](service-bus-java-how-to-use-queues.md)
- [JavaScript](service-bus-nodejs-how-to-use-queues.md)
- [Python](service-bus-python-how-to-use-queues.md)
- [PHP](service-bus-php-how-to-use-queues.md)
- [Ruby](service-bus-ruby-how-to-use-queues.md)

[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

