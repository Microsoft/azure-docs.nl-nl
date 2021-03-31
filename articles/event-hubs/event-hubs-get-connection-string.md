---
title: Connection string ophalen-Azure Event Hubs | Microsoft Docs
description: Dit artikel bevat instructies voor het ophalen van een connection string die clients kunnen gebruiken om verbinding te maken met Azure Event Hubs.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 5ae6c66ddbbf4b9946e7037e1a7723043bf60507
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86537170"
---
# <a name="get-an-event-hubs-connection-string"></a>Een Event Hubs ophalen connection string

Als u Event Hubs wilt gebruiken, moet u een Event Hubs-naam ruimte maken. Een naam ruimte is een container voor het bereik van meerdere Event hubs of Kafka-onderwerpen. Deze naam ruimte geeft u een unieke [FQDN](https://en.wikipedia.org/wiki/Fully_qualified_domain_name). Zodra een naam ruimte is gemaakt, kunt u de connection string ophalen die nodig zijn om te communiceren met Event Hubs.

In de connection string voor Azure Event Hubs zijn de volgende onderdelen Inge sloten:

* FQDN = de FQDN van de Event hubs-naam ruimte die u hebt gemaakt (deze bevat de naam van de Event hubs-naam ruimte gevolgd door servicebus.windows.net)
* SharedAccessKeyName = de naam die u hebt gekozen voor de SAS-sleutels van uw toepassing
* SharedAccessKey = de gegenereerde waarde van de sleutel.

De connection string sjabloon ziet er als volgt uit
```
Endpoint=sb://<FQDN>/;SharedAccessKeyName=<KeyName>;SharedAccessKey=<KeyValue>
```

Een voor beeld connection string kan er als volgt uitzien `Endpoint=sb://dummynamespace.servicebus.windows.net/;SharedAccessKeyName=DummyAccessKeyName;SharedAccessKey=5dOntTRytoC24opYThisAsit3is2B+OGY1US/fuL3ly=`

Dit artikel begeleidt u bij verschillende manieren om de connection string te verkrijgen.

## <a name="get-connection-string-from-the-portal"></a>connection string ophalen uit de portal
1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 
2. Selecteer **alle services** in het navigatie menu links. 
3. Selecteer **Event hubs** in het gedeelte **Analytics** . 
4. Selecteer uw Event Hub in de lijst met Event hubs.
6. Selecteer op de pagina **Event hubs naam ruimte** de optie **beleid voor gedeelde toegang** in het menu links.

    ![Menu-item voor Shared Access policies](./media/event-hubs-get-connection-string/event-hubs-get-connection-string1.png)
7. Selecteer een **gedeeld toegangs beleid** in de lijst met beleids regels. De standaard naam is: **RootManageSharedAccessPolicy**. U kunt een beleid met de juiste machtigingen toevoegen (lezen, schrijven) en dat beleid gebruiken. 

    ![Beleid voor gedeelde toegang Event Hubs](./media/event-hubs-get-connection-string/event-hubs-get-connection-string2.png)
8. Selecteer de knop **kopiëren** naast het veld **verbindings reeks-primaire sleutel** . 

    ![Event Hubs-connection string ophalen](./media/event-hubs-get-connection-string/event-hubs-get-connection-string3.png)

## <a name="getting-the-connection-string-with-azure-powershell"></a>De connection string ophalen met Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt de [Get-AzEventHubKey](/powershell/module/az.eventhub/get-azeventhubkey) gebruiken om de Connection String op te halen voor de naam van het specifieke beleid/de regel, zoals hieronder wordt weer gegeven:

```azurepowershell-interactive
Get-AzEventHubKey -ResourceGroupName dummyresourcegroup -NamespaceName dummynamespace -AuthorizationRuleName RootManageSharedAccessKey
```

## <a name="getting-the-connection-string-with-azure-cli"></a>De connection string ophalen met Azure CLI
U kunt het volgende gebruiken om de connection string voor de naam ruimte op te halen:

```azurecli-interactive
az eventhubs namespace authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --name RootManageSharedAccessKey
```

U kunt ook het volgende gebruiken om de connection string voor een EventHub-entiteit op te halen:

```azurecli-interactive
az eventhubs eventhub authorization-rule keys list --resource-group dummyresourcegroup --namespace-name dummynamespace --eventhub-name dummyeventhub --name RootManageSharedAccessKey
```

Zie voor meer informatie over Azure CLI-opdrachten voor Event Hubs [Azure CLI voor Event hubs](/cli/azure/eventhubs).

## <a name="next-steps"></a>Volgende stappen

U kunt meer informatie over Event Hubs vinden via de volgende koppelingen:

* [Event Hubs-overzicht](./event-hubs-about.md)
* [Een event hub maken](event-hubs-create.md)
