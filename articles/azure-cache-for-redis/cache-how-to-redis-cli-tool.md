---
title: Gebruik redis-CLI met Azure cache voor redis
description: Meer informatie over het gebruik van *redis-cli.exe* als een opdracht regel programma voor interactie met een Azure-cache voor redis als een-client
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: e4f5fc7290b45f65067f6711f70476e13a010223
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102183383"
---
# <a name="use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Gebruik het opdracht regel programma redis met Azure cache voor redis

*redis-cli.exe* is een populair opdracht regel programma voor interactie met een Azure-cache voor redis als een-client. Dit hulp programma is ook beschikbaar voor gebruik met Azure cache voor redis.

Het hulp programma is beschikbaar voor Windows-platforms door de [redis-opdracht regel Programma's voor Windows](https://github.com/MSOpenTech/redis/releases/)te downloaden. 

Als u het opdracht regel programma wilt uitvoeren op een ander platform, downloadt u Azure-cache voor redis van [https://redis.io/download](https://redis.io/download) .

## <a name="gather-cache-access-information"></a>Toegangs gegevens van cache verzamelen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt de gegevens die nodig zijn voor toegang tot de cache verzamelen met behulp van drie methoden:

1. Azure CLI met [AZ redis List-Keys](/cli/azure/redis#az-redis-list-keys)
2. Azure PowerShell met [Get-AzRedisCacheKey](/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Via Azure Portal.

In deze sectie haalt u de sleutels op uit het Azure Portal.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Toegang inschakelen voor redis-cli.exe

Met Azure cache voor redis is alleen de TLS-poort (6380) standaard ingeschakeld. Het `redis-cli.exe` opdracht regel programma biedt geen ondersteuning voor TLS. U hebt twee configuratie-opties om deze te gebruiken:

1. [Schakel de niet-TLS-poort in (6379)](cache-configure.md#access-ports)  -  **Deze configuratie wordt niet aanbevolen** omdat in deze configuratie de toegangs sleutels worden verzonden via TCP als Lees bare tekst. Met deze wijziging kan de toegang tot uw cache worden aangetast. Het enige scenario waarin u rekening moet houden met deze configuratie is wanneer u alleen een test cache opent.

2. Down load en Installeer [stunnel](https://www.stunnel.org/downloads.html).

    Voer **STUNNEL GUI start** uit om de server te starten.

    Klik met de rechter muisknop op het taak balk pictogram voor de stunnel-server en klik op **logboek venster weer geven**.

    Klik in het menu van het stunnel-logboek op **configuratie**  >  **bewerkings configuratie** om het huidige configuratie bestand te openen.

    Voeg de volgende vermelding toe voor *redis-cli.exe* in het gedeelte **service definities** . Plaats de daad werkelijke cache naam in plaats van `yourcachename` . 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Sla het configuratie bestand op en sluit het. 
  
    **Klik in** het menu van het stunnel-logboek op configuratie  >  **opnieuw laden** configuratie.


## <a name="connect-using-the-redis-command-line-tool"></a>Verbinding maken met behulp van het opdracht regel programma redis.

Wanneer u stunnel gebruikt, voert u *redis-cli.exe* uit en geeft u alleen uw *poort* door en de *toegangs sleutel* (primair of secundair) om verbinding te maken met de cache.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![Scherm opname die laat zien dat de verbinding met de cache is geslaagd.](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Als u een test cache gebruikt met de niet- **beveiligde** niet-TLS-poort, voert u `redis-cli.exe` de *hostnaam*, *poort* en *toegangs sleutel* (primair of secundair) uit om verbinding te maken met de test cache.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel met redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van de [redis-console](cache-configure.md#redis-console) om opdrachten uit te geven.