---
title: De Azure CLI gebruiken om Service Bus-onderwerpen en -abonnementen te maken
description: In deze quickstart leert u hoe u een Service Bus-onderwerp en abonnementen op dat onderwerp kunt maken met behulp van de Azure CLI
ms.date: 06/23/2020
ms.topic: quickstart
author: spelluru
ms.author: spelluru
ms.openlocfilehash: 080b089efa276779420f6d9bc8e76272f1e65788
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90069693"
---
# <a name="use-azure-cli-to-create-a-service-bus-topic-and-subscriptions-to-the-topic"></a>De Azure CLI gebruiken om een Service Bus-onderwerp en abonnementen te maken voor het onderwerp
In deze quickstart gebruikt u de Azure CLI om een Service Bus-onderwerp te maken en vervolgens abonnementen op dat onderwerp te maken. 

## <a name="what-are-service-bus-topics-and-subscriptions"></a>Wat zijn Service Bus-onderwerpen en -abonnementen?
Service Bus-onderwerpen en -abonnementen bieden ondersteuning voor een berichtencommunicatiemodel op basis van *publiceren/abonneren*. Wanneer u gebruikmaakt van onderwerpen en abonnementen, communiceren onderdelen van een gedistribueerde toepassing niet direct met elkaar. In plaats daarvan wisselen ze berichten uit via een onderwerp dat als intermediair fungeert.

![TopicConcepts](./media/service-bus-java-how-to-use-topics-subscriptions/sb-topics-01.png)

Anders dan bij Service Bus-wachtrijen, waarin elk bericht door een enkele gebruiker wordt verwerkt, bieden onderwerpen en abonnementen een één-naar-veel-communicatiewijze, waarbij een patroon voor publiceren/abonneren wordt gebruikt. Het is mogelijk meerdere abonnementen voor een onderwerp te registreren. Wanneer een bericht naar een onderwerp wordt verzonden, wordt dit vervolgens beschikbaar gemaakt voor elk abonnement, waar het vervolgens zelfstandig wordt afgehandeld/verwerkt. Een abonnement op een onderwerp lijkt op een virtuele wachtrij die kopieën van de berichten ontvangen die naar het onderwerp zijn verzonden. U kunt eventueel per abonnement filterregels voor een onderwerp registreren, waardoor u met behulp van een filter of een beperking kunt bepalen welke berichten naar een onderwerp door welke onderwerpabonnementen worden ontvangen.

Met Service Bus-onderwerpen en -abonnementen kunt u opschalen naar het verwerken van grote aantallen berichten voor grote aantallen gebruikers en toepassingen.

## <a name="prerequisites"></a>Vereisten
Als u nog geen abonnement op Azure hebt, kunt u een [gratis account][free account] maken voordat u begint.

In deze quickstart gebruikt u Azure Cloud Shell dat u kunt starten nadat u zich hebt aangemeld bij Azure Portal. Zie [Overzicht van Azure Cloud Shell](../cloud-shell/overview.md) voor meer informatie over Azure Cloud Shell. U kunt Azure PowerShell ook op uw computer [installeren](/cli/azure/install-azure-cli) en gebruiken. 

## <a name="create-a-service-bus-topic-and-subscriptions"></a>Een Service Bus-onderwerp en abonnementen maken
Elk [abonnement op een onderwerp](service-bus-messaging-overview.md#topics) kan een kopie van elk bericht ontvangen. Onderwerpen zijn qua protocol en semantiek volledig compatibel met Service Bus-wachtrijen. Service Bus-onderwerpen ondersteunen een breed scala aan selectieregels met filtervoorwaarden, met optionele acties waarmee berichteigenschappen worden ingesteld of gewijzigd. Telkens wanneer een regel overeenkomt, wordt er een bericht gegenereerd. Volg deze [koppeling](topic-filters.md) voor meer informatie over regels, filters en acties.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
2. Start Azure Cloud Shell door het pictogram te selecteren dat wordt weergegeven in de volgende afbeelding. Schakel over naar de **Bash**-modus als Cloud Shell zich in de **PowerShell**-modus bevindt. 

    :::image type="content" source="./media/service-bus-quickstart-powershell/launch-cloud-shell.png" alt-text="Cloud Shell starten":::
3. Voer de volgende opdracht uit om een Azure-resourcegroep te maken. Werk de naam en locatie van de resourcegroep desgewenst bij. 

    ```azurecli-interactive
    az group create --name MyResourceGroup --location eastus
    ```
4. Voer de volgende opdracht uit om een naamruimte voor Service Bus-berichten te maken. Werk de naam van de naamruimte zo bij dat deze uniek is. 

    ```azurecli-interactive
    namespaceName=MyNameSpace$RANDOM
    az servicebus namespace create --resource-group MyResourceGroup --name $namespaceName --location eastus
    ```
5. Voer de volgende opdracht uit om een onderwerp in de naamruimte te maken. 

    ```azurecli-interactive
    az servicebus topic create --resource-group MyResourceGroup   --namespace-name $namespaceName --name MyTopic
    ```
6. Het eerste abonnement op het onderwerp maken
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S1    
    ```
6. Het tweede abonnement op het onderwerp maken
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S2    
    ```
6. Het derde abonnement op het onderwerp maken
    
    ```azurecli-interactive
    az servicebus topic subscription create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --name S3    
    ```
7. Maak een filter op het eerste abonnement met een filter met aangepaste eigenschappen (`StoreId` is `Store1`, `Store2` of `Store3`).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"    
    ```
8. Maak een filter op het tweede abonnement met een filter met klanteigenschappen (`StoreId = Store4`).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"    
    ```
9. Maak een filter op het derde abonnement met een filter met klanteigenschappen (`StoreId` niet in `Store1`, `Store2`, `Store3` of `Store4`).

    ```azurecli-interactive
    az servicebus topic subscription rule create --resource-group MyResourceGroup --namespace-name $namespaceName --topic-name MyTopic --subscription-name S3 --name MyThirdFilter --filter-sql-expression "StoreId NOT IN ('Store1','Store2','Store3', 'Store4')"     
    ```
10. Voer de volgende opdracht uit om de primaire verbindingsreeks voor de naamruimte op te halen. U gebruikt deze verbindingsreeks om verbinding te maken met de wachtrij en berichten te verzenden en te ontvangen. 

    ```azurecli-interactive
    az servicebus namespace authorization-rule keys list --resource-group MyResourceGroup --namespace-name $namespaceName --name RootManageSharedAccessKey --query primaryConnectionString --output tsv    
    ```

    Noteer de verbindingsreeks en de naam van het onderwerp. U gebruikt deze om berichten te verzenden en ontvangen. 
    

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel voor meer informatie over het verzenden van berichten naar een onderwerp en het ontvangen van die berichten via een abonnement: selecteer de programmeertaal in de inhoudsopgave. 

> [!div class="nextstepaction"]
> [Publiceren en abonneren op berichten](service-bus-dotnet-how-to-use-topics-subscriptions.md)


[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install the Azure CLI]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create
