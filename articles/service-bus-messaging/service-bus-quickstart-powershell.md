---
title: Azure PowerShell gebruiken om een Service Bus-wachtrij te maken
description: In deze quickstart leert u hoe u een Service Bus-naamruimte en een wachtrij in de naamruimte maakt met behulp van Azure PowerShell.
author: spelluru
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 08/12/2020
ms.author: spelluru
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8cf024735c66e6bae9d334e3d8ce8d0a0eed1426
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95799111"
---
# <a name="use-azure-powershell-to-create-a-service-bus-namespace-and-a-queue"></a>Azure PowerShell gebruiken om een Service Bus-naamruimte en -wachtrij te maken
In deze quickstart wordt beschreven hoe u een Service Bus-naamruimte en -wachtrij maakt met behulp van Azure PowerShell. Er wordt ook beschreven hoe u autorisatiereferenties ophaalt die een client-toepassing kan gebruiken voor het verzenden/ontvangen van berichten naar/van de wachtrij. 

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u over een Azure-abonnement beschikt om deze quickstart uit te voeren. Als u nog geen abonnement op Azure hebt, kunt u een [gratis account][] maken voordat u begint. 

In deze quickstart gebruikt u Azure Cloud Shell dat u kunt starten nadat u zich hebt aangemeld bij Azure Portal. Zie [Overzicht van Azure Cloud Shell](../cloud-shell/overview.md) voor meer informatie over Azure Cloud Shell. U kunt Azure PowerShell ook op uw computer [installeren](/powershell/azure/install-Az-ps) en gebruiken. 


## <a name="provision-resources"></a>Resources inrichten
1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Start Azure Cloud Shell door het pictogram te selecteren dat wordt weergegeven in de volgende afbeelding: 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell starten":::
3. Ga in het onderste Cloud Shell-venster van **Bash** naar **PowerShell**. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/cloud-power-shell.png" alt-text="Overschakelen naar de PowerShell-modus":::    
4. Voer de volgende opdracht uit om een Azure-resourcegroep te maken. Werk de naam en locatie van de resourcegroep desgewenst bij. 

    ```azurepowershell-interactive
    New-AzResourceGroup –Name ContosoRG –Location eastus
    ```
5. Voer de volgende opdracht uit om een naamruimte voor Service Bus-berichten te maken. In dit voorbeeld is `ContosoRG` de resourcegroep die u in de vorige stap hebt gemaakt. `ContosoSBusNS` is de naam van de Service Bus-naamruimte die u in die resourcegroep hebt gemaakt. 

    ```azurepowershell-interactive
    New-AzServiceBusNamespace -ResourceGroupName ContosoRG -Name ContosoSBusNS -Location eastus
    ```
6. Voer het volgende uit om een wachtrij te maken in de naamruimte die u in de vorige stap hebt gemaakt. 

    ```azurepowershell-interactive
    New-AzServiceBusQueue -ResourceGroupName ContosoRG -NamespaceName ContosoSBusNS -Name ContosoOrdersQueue 
    ```
7. Haal de primaire verbindingstekenreeks voor de naamruimte op. U gebruikt deze verbindingstekenreeks om verbinding te maken met de wachtrij en berichten te verzenden en te ontvangen. 

    ```azurepowershell-interactive    
    Get-AzServiceBusKey -ResourceGroupName ContosoRG -Namespace ContosoSBusNS -Name RootManageSharedAccessKey
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

[gratis account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio

