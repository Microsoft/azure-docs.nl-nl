---
title: Redis-cli gebruiken met Azure Cache voor Redis
description: Meer informatie over het gebruik *redis-cli.exe* als opdrachtregelprogramma voor interactie met een Azure Cache voor Redis als client
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 81327bd5fc76d14d60d26bd912da8de054e5308d
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833954"
---
# <a name="use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Gebruik het opdrachtregelprogramma Redis met Azure Cache voor Redis

*redis-cli.exe* is een populair opdrachtregelprogramma voor interactie met een Azure Cache voor Redis als client. Dit hulpprogramma is ook beschikbaar voor gebruik met Azure Cache voor Redis.

Het hulpprogramma is beschikbaar voor Windows-platforms door de [Redis-opdrachtregelprogramma's](https://github.com/MSOpenTech/redis/releases/)voor Windows te downloaden. 

Als u het opdrachtregelprogramma op een ander platform wilt uitvoeren, downloadt Azure Cache voor Redis van [https://redis.io/download](https://redis.io/download) .

## <a name="gather-cache-access-information"></a>Toegangsgegevens voor de cache verzamelen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt de benodigde informatie voor toegang tot de cache op drie manieren verzamelen:

1. Azure CLI met [az redis list-keys](/cli/azure/redis#az_redis_list_keys)
2. Azure PowerShell [Get-AzRedisCacheKey gebruiken](/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Via Azure Portal.

In deze sectie haalt u de sleutels op uit de Azure Portal.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Toegang inschakelen voor redis-cli.exe

Met Azure Cache voor Redis is standaard alleen de TLS-poort (6380) ingeschakeld. Het `redis-cli.exe` opdrachtregelprogramma biedt geen ondersteuning voor TLS. U hebt twee configuratieopties om deze te gebruiken:

1. [De niet-TLS-poort inschakelen (6379)](cache-configure.md#access-ports)  -  **Deze configuratie wordt niet aanbevolen,** omdat in deze configuratie de toegangssleutels in niet-doorgestuurde tekst via TCP worden verzonden. Deze wijziging kan de toegang tot uw cache in gevaar brengen. Het enige scenario waarin u deze configuratie kunt overwegen, is wanneer u alleen toegang hebt tot een testcache.

2. Download en installeer [stunnel](https://www.stunnel.org/downloads.html).

    Voer **stunnel GUI Start uit om** de server te starten.

    Klik met de rechtermuisknop op het taakbalkpictogram voor de stunnelserver en klik **op Logboekvenster weergeven.**

    Klik in het menu stunnellogboekvenster op **Configuratie**  >  **bewerken om** het huidige configuratiebestand te openen.

    Voeg de volgende vermelding toe *voorredis-cli.exe* onder de **sectie Servicedefinities.** Voeg de werkelijke cachenaam in plaats van `yourcachename` in. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Sla het configuratiebestand op en sluit het. 
  
    Klik in het menu stunnellogboekvenster op   >  **ConfiguratieConfiguratie opnieuw laden.**


## <a name="connect-using-the-redis-command-line-tool"></a>Maak verbinding met behulp van het opdrachtregelprogramma Redis.

Wanneer u stunnel gebruikt, *redis-cli.exe* en geeft u alleen uw poort *en* de toegangssleutel *(primaire* of secundaire) door om verbinding te maken met de cache.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![Schermopname die laat zien dat uw verbinding met de cache is geslaagd.](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Als u een testcache  gebruikt met de onbeveiligde niet-TLS-poort, moet u uw hostnaam, poort en toegangssleutel (primaire of secundaire) uitvoeren en doorgeven om verbinding te maken met de `redis-cli.exe` testcache.   

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel met redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van de [Redis-console om](cache-configure.md#redis-console) opdrachten uit te geven.
