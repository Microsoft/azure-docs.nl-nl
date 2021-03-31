---
title: Azure cache beheren voor redis met behulp van klassieke Azure CLI
description: Meer informatie over het installeren van de klassieke Azure CLI op elk platform, hoe u deze kunt gebruiken om verbinding te maken met uw Azure-account en hoe u een Azure-cache maakt en beheert voor redis vanuit de klassieke CLI.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 01/23/2017
ms.author: yegu
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7643f882d5ac330046c169e0a3f2fa4920331d4e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92537691"
---
# <a name="how-to-create-and-manage-azure-cache-for-redis-using-the-azure-classic-cli"></a>Azure-cache voor redis maken en beheren met behulp van de klassieke Azure-CLI
> [!div class="op_single_selector"]
> * [PowerShell](cache-how-to-manage-redis-cache-powershell.md)
> * [Klassieke Azure-CLI](cache-manage-cli.md)
>

De klassieke Azure-CLI is een uitstekende manier om uw Azure-infra structuur vanaf elk platform te beheren. Dit artikel laat u zien hoe u uw Azure-cache voor redis-instanties maakt en beheert met behulp van de klassieke Azure-CLI.

[!INCLUDE [outdated-cli-content](../../includes/contains-classic-cli-content.md)]
> [!NOTE]
> Voor de nieuwste Azure CLI-voorbeeld scripts raadpleegt u [Azure cli Azure cache voor redis-voor beelden](cli-samples.md).

## <a name="prerequisites"></a>Vereisten
Voor het maken en beheren van Azure-cache voor redis-exemplaren met behulp van klassieke Azure-CLI, moet u de volgende stappen uitvoeren.

* U moet een Azure-account hebben. Als u er nog geen hebt, kunt u in slechts enkele ogen blikken een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.
* [Installeer de klassieke Azure-cli](/cli/azure/install-classic-cli).
* Verbind uw Azure CLI-installatie met een persoonlijk Azure-account of met een werk-of school-Azure-account en meld u aan bij de klassieke CLI met behulp van de `azure login` opdracht.
* Voordat u een van de volgende opdrachten uitvoert, moet u de klassieke CLI overschakelen naar de Resource Manager-modus door de opdracht uit te voeren `azure config mode arm` . Zie [de klassieke Azure-CLI gebruiken voor het beheren van Azure-resources en-resource groepen](../azure-resource-manager/management/manage-resources-cli.md)voor meer informatie.

## <a name="azure-cache-for-redis-properties"></a>Eigenschappen van Azure Cache voor Redis
De volgende eigenschappen worden gebruikt bij het maken en bijwerken van Azure-cache voor redis-exemplaren.

| Eigenschap | Switch | Beschrijving |
| --- | --- | --- |
| naam |-n,--naam |De naam van de Azure-cache voor redis. |
| resourcegroep |-g,--resource-Group |De naam van de resource groep. |
| location |-l,--locatie |Locatie voor het maken van de cache. |
| grootte |-z,--grootte |Grootte van de Azure-cache voor redis. Geldige waarden: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4] |
| sku |-x,--SKU |Redis-SKU. Moet een van de volgende zijn: [Basic, Standard, Premium] |
| EnableNonSslPort |-e,--inschakelen-niet-SSL-poort |De eigenschap EnableNonSslPort van de Azure-cache voor redis. Voeg deze markering toe als u de niet-TLS/SSL-poort voor uw cache wilt inschakelen |
| Redis-configuratie |-c,--redis-configuratie |Redis-configuratie. Voer hier een JSON-teken reeks met configuratie sleutels en waarden in. Indeling: "{" ":" "," ":" "}" |
| Redis-configuratie |-f,--redis-configuratie bestand |Redis-configuratie. Geef hier het pad op van een bestand met configuratie sleutels en-waarden. Indeling voor de bestands vermelding: {"": "", "": ""} |
| Aantal Shard |-r,--Shard-Count |Het aantal Shards dat moet worden gemaakt in een Premium-cluster cache met clustering. |
| Virtual Network |-v,--virtueel-netwerk |Wanneer u uw cache host in een VNET, geeft u de exacte ARM-Resource-ID van het virtuele netwerk op voor het implementeren van de Azure-cache voor redis in. Voorbeeld notatie:/subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| sleutel type |-t,--sleutel-type |Type sleutel dat moet worden verlengd. Geldige waarden: [Primary, Secondary] |
| StaticIP |-p,--statisch-IP \<static-ip\> |Bij het hosten van uw cache in een VNET, geeft u een uniek IP-adres op in het subnet voor de cache. Indien niet opgegeven, wordt er een gekozen uit het subnet. |
| Subnet |t,--subnet \<subnet\> |Wanneer u uw cache host in een VNET, geeft u de naam op van het subnet waarin de cache moet worden geïmplementeerd. |
| VirtualNetwork |-v,--virtueel-netwerk \<virtual-network\> |Wanneer u uw cache host in een VNET, geeft u de exacte ARM-Resource-ID van het virtuele netwerk op voor het implementeren van de Azure-cache voor redis in. Voorbeeld notatie:/subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1 |
| Abonnement |-s,--abonnement |De abonnements-id. |

## <a name="see-all-azure-cache-for-redis-commands"></a>Alle Azure-cache voor redis-opdrachten weer geven
Als u alle Azure-cache voor redis-opdrachten en de bijbehorende para meters wilt weer geven, gebruikt u de `azure rediscache -h` opdracht.

```azurecli
C:\>azure rediscache -h
help:    Commands to manage your Azure Cache for Redis(s)
help:
help:    Create an Azure Cache for Redis
help:      rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Delete an existing Azure Cache for Redis
help:      rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    List all Azure Cache for Redis within your Subscription or Resource Group
help:      rediscache list [options]
help:
help:    Show properties of an existing Azure Cache for Redis
help:      rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Change settings of an existing Azure Cache for Redis
help:      rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Renew the authentication key for an existing Azure Cache for Redis
help:      rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:      rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help  output usage information
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="create-an-azure-cache-for-redis"></a>Een Azure Cache voor Redis-instantie maken
Als u een Azure-cache wilt maken voor redis, gebruikt u de volgende opdracht:

```azurecli
    azure rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
```

Voer de opdracht uit voor meer informatie over deze opdracht `azure rediscache create -h` .

```azurecli
C:\>azure rediscache create -h
help:    Create an Azure Cache for Redis
help:
help:    Usage: rediscache create [--name <name> --resource-group <resource-group> --location <location> [options]]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -l, --location <location>                                Location to create cache.
help:      -z, --size <size>                                        Size of the Azure Cache for Redis. Valid values: [C0, C1, C2, C3, C4, C5, C6, P1, P2, P3, P4]
help:      -x, --sku <sku>                                          Redis SKU. Should be one of : [Basic, Standard, Premium]
help:      -e, --enable-non-ssl-port                                EnableNonSslPort property of the Azure Cache for Redis. Add this flag if you want to enable the non-TLS/SSL Port for your cache
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here. Format:"{"<key1>":"<value1>","<key2>":"<value2>"}"
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here. Format for the file entry: {"<key1>":"<value1>","<key2>":"<value2>"}
help:      -r, --shard-count <shard-count>                          Number of Shards to create on a Premium Cluster Cache
help:      -v, --virtual-network <virtual-network>                  The exact ARM resource ID of the virtual network to deploy the Azure Cache for Redis in. Example format: /subscriptions/{subid}/resourceGroups/{resourceGroupName}/Microsoft.ClassicNetwork/VirtualNetworks/vnet1
help:      -t, --subnet <subnet>                                    Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -p, --static-ip <static-ip>                              Required when deploying an Azure Cache for Redis inside an existing Azure Virtual Network
help:      -s, --subscription <id>                                  the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="delete-an-existing-azure-cache-for-redis"></a>Een bestaande Azure-cache voor redis verwijderen
Als u een Azure-cache voor redis wilt verwijderen, gebruikt u de volgende opdracht:

```azurecli
    azure rediscache delete [--name <name> --resource-group <resource-group> ]
```

Voer de opdracht uit voor meer informatie over deze opdracht `azure rediscache delete -h` .

```azurecli
C:\>azure rediscache delete -h
help:    Delete an existing Azure Cache for Redis
help:
help:    Usage: rediscache delete [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which the cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-all-azure-cache-for-redis-within-your-subscription-or-resource-group"></a>Alle Azure-cache voor redis in uw abonnement of resource groep weer geven
Als u alle Azure-cache voor redis binnen uw abonnement of resource groep wilt weer geven, gebruikt u de volgende opdracht:

```azurecli
    azure rediscache list [options]
```

Voer de opdracht uit voor meer informatie over deze opdracht `azure rediscache list -h` .

```azurecli
C:\>azure rediscache list -h
help:    List all Azure Cache for Redis within your Subscription or Resource Group
help:
help:    Usage: rediscache list [options]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -g, --resource-group <resource-group>  Name of the Resource Group
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="show-properties-of-an-existing-azure-cache-for-redis"></a>Eigenschappen van een bestaand Azure-cache geheugen weer geven voor redis
Als u de eigenschappen van een bestaand Azure-cache geheugen voor redis wilt weer geven, gebruikt u de volgende opdracht:

```azurecli
    azure rediscache show [--name <name> --resource-group <resource-group>]
```

Voer de opdracht uit voor meer informatie over deze opdracht `azure rediscache show -h` .

```azurecli
C:\>azure rediscache show -h
help:    Show properties of an existing Azure Cache for Redis
help:
help:    Usage: rediscache show [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

<a name="scale"></a>

## <a name="change-settings-of-an-existing-azure-cache-for-redis"></a>Instellingen van een bestaande Azure-cache wijzigen voor redis
Als u de instellingen van een bestaande Azure-cache voor redis wilt wijzigen, gebruikt u de volgende opdracht:

```azurecli
    azure rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
```

Voer de opdracht uit voor meer informatie over deze opdracht `azure rediscache set -h` .

```azurecli
C:\>azure rediscache set -h
help:    Change settings of an existing Azure Cache for Redis
help:
help:    Usage: rediscache set [--name <name> --resource-group <resource-group> --redis-configuration <redis-configuration>/--redis-configuration-file <redisConfigurationFile>]
help:
help:    Options:
help:      -h, --help                                               output usage information
help:      -v, --verbose                                            use verbose output
help:      -vv                                                      more verbose with debug output
help:      --json                                                   use json output
help:      -n, --name <name>                                        Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>                    Name of the Resource Group
help:      -c, --redis-configuration <redis-configuration>          Redis Configuration. Enter a JSON formatted string of configuration keys and values here.
help:      -f, --redis-configuration-file <redisConfigurationFile>  Redis Configuration. Enter the path of a file containing configuration keys and values here.
help:      -s, --subscription <subscription>                        the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="renew-the-authentication-key-for-an-existing-azure-cache-for-redis"></a>De verificatie sleutel voor een bestaande Azure-cache vernieuwen voor redis
Voor het vernieuwen van de verificatie sleutel voor een bestaande Azure-cache voor redis, gebruikt u de volgende opdracht:

```azurecli
    azure rediscache renew-key [--name <name> --resource-group <resource-group> --key-type <key-type>]
```

Opgeven `Primary` of `Secondary` voor `key-type` .

Voer de opdracht uit voor meer informatie over deze opdracht `azure rediscache renew-key -h` .

```azurecli
C:\>azure rediscache renew-key -h
help:    Renew the authentication key for an existing Azure Cache for Redis
help:
help:    Usage: rediscache renew-key [--name <name> --resource-group <resource-group> ]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which cache exists
help:      -t, --key-type <key-type>              type of key to renew. Valid values are: 'Primary', 'Secondary'.
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```

## <a name="list-primary-and-secondary-keys-of-an-existing-azure-cache-for-redis"></a>De primaire en secundaire sleutels van een bestaande Azure-cache voor redis weer geven
Als u de primaire en secundaire sleutels van een bestaande Azure-cache voor redis wilt weer geven, gebruikt u de volgende opdracht:

```azurecli
    azure rediscache list-keys [--name <name> --resource-group <resource-group>]
```

Voer de opdracht uit voor meer informatie over deze opdracht `azure rediscache list-keys -h` .

```azurecli
C:\>azure rediscache list-keys -h
help:    Lists Primary and Secondary key of an existing Azure Cache for Redis
help:
help:    Usage: rediscache list-keys [--name <name> --resource-group <resource-group>]
help:
help:    Options:
help:      -h, --help                             output usage information
help:      -v, --verbose                          use verbose output
help:      -vv                                    more verbose with debug output
help:      --json                                 use json output
help:      -n, --name <name>                      Name of the Azure Cache for Redis.
help:      -g, --resource-group <resource-group>  Name of the Resource Group under which Cache exists
help:      -s, --subscription <subscription>      the subscription identifier
help:
help:    Current Mode: arm (Azure Resource Management)
```