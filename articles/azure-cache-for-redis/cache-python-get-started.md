---
title: 'Quickstart: Azure Cache voor Redis in Python gebruiken'
description: In deze quickstart leert u een Python-app te maken die gebruikmaakt van Azure Cache voor Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.devlang: python
ms.topic: quickstart
ms.custom:
- mvc
- seo-python-october2019
- devx-track-python
ms.date: 11/05/2019
ms.openlocfilehash: 048c31bedb56bdb66dc3b80d12c3014652e34625
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121147"
---
# <a name="quickstart-use-azure-cache-for-redis-in-python"></a>Quickstart: Azure Cache voor Redis in Python gebruiken

In dit artikel neemt u Azure Cache voor Redis op in een Python-app voor toegang tot een veilige, toegewezen cache die toegankelijk is vanuit elke toepassing binnen Azure.

## <a name="skip-to-the-code-on-github"></a>Ga naar de code op GitHub

Als u direct naar de code wilt gaan, raadpleegt u de [python Quick](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/python) start op github.

## <a name="prerequisites"></a>Vereisten

- Azure-abonnement: [u kunt een gratis abonnement nemen](https://azure.microsoft.com/free/)
- [Python 2 of 3](https://www.python.org/downloads/)

## <a name="create-an-azure-cache-for-redis-instance"></a>Een instantie van Azure Cache voor Redis maken
[!INCLUDE [redis-cache-create](../../includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

## <a name="install-redis-py"></a>Redis-py installeren

[Redis-py](https://github.com/andymccurdy/redis-py) is een Python-interface voor Azure Cache voor Redis. Gebruik het hulpprogramma voor Python-pakketten, *pip*, om het *redis-py*-pakket te installeren vanuit een opdrachtprompt. 

In het volgende voorbeeld is *pip3* voor Python 3 gebruikt om *redis-py* op Windows 10 te installeren via een opdrachtprompt voor de beheerder.

![De Python-interface redis-py installeren in Azure Cache voor Redis](./media/cache-python-get-started/cache-python-install-redis-py.png)

## <a name="read-and-write-to-the-cache"></a>Lezen en schrijven naar de cache

Voer Python uit vanaf de opdrachtregel en test uw cache met behulp van de volgende code. Vervang `<Your Host Name>` en `<Your Access Key>` door de waarden van uw Azure Cache voor Redis-instantie. Uw hostnaam heeft de indeling *\<DNS name>.redis.cache.windows.net*.

```python
>>> import redis
>>> r = redis.StrictRedis(host='<Your Host Name>',
        port=6380, db=0, password='<Your Access Key>', ssl=True)
>>> r.set('foo', 'bar')
True
>>> r.get('foo')
b'bar'
```

> [!IMPORTANT]
> Voor Azure Cache voor Redis versie 3.0 of hoger wordt de TLS/SSL certificaatcontrole afgedwongen. ssl_ca_certs moeten expliciet worden ingesteld wanneer u verbinding maakt met Azure Cache voor Redis. Voor RedHat Linux vindt u ssl_ca_certs in de certificaatmodule */etc/pki/tls/certs/ca-bundle.crt*.

## <a name="create-a-python-sample-app"></a>Een Python-voorbeeldapp maken

Maak een nieuw tekstbestand, voeg het volgende script toe en sla het bestand op als *PythonApplication1.py*. Vervang `<Your Host Name>` en `<Your Access Key>` door de waarden van uw Azure Cache voor Redis-instantie. Uw hostnaam heeft de indeling *\<DNS name>.redis.cache.windows.net*.

```python
import redis

myHostname = "<Your Host Name>"
myPassword = "<Your Access Key>"

r = redis.StrictRedis(host=myHostname, port=6380,
                      password=myPassword, ssl=True)

result = r.ping()
print("Ping returned : " + str(result))

result = r.set("Message", "Hello!, The cache is working with Python!")
print("SET Message returned : " + str(result))

result = r.get("Message")
print("GET Message returned : " + result.decode("utf-8"))

result = r.client_list()
print("CLIENT LIST returned : ")
for c in result:
    print("id : " + c['id'] + ", addr : " + c['addr'])
```

Voer *PythonApplication1.py* uit met Python. Als het goed is, ziet u resultaten zoals in het volgende voorbeeld:

![Python-script uitvoeren om toegang tot de cache te testen](./media/cache-python-get-started/cache-python-completed.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u klaar bent met de Azure-resourcegroep en -resources die u hebt gemaakt in deze quickstart, kunt u ze verwijderen om kosten te voorkomen.

> [!IMPORTANT]
> Het verwijderen van een resourcegroep kan niet ongedaan worden gemaakt. De resourcegroep en alle bijbehorende resources worden permanent verwijderd. Als u uw Azure Cache voor Redis-exemplaar hebt gemaakt in een bestaande resourcegroep die u wilt behouden, kunt u alleen de cache wissen door **Wissen** te selecteren op de pagina **Overzicht** van de cache. 

U kunt de resourcegroep en het bijbehorende Redis Cache voor Azure exemplaar als volgt verwijderen:

1. Zoek en selecteer in [Azure Portal](https://portal.azure.com) de optie **Resourcegroepen**.
1. Voer in het tekstvak **Filteren op naam** de naam in van de resourcegroep met uw cache-exemplaar en selecteer de naam vervolgens in de zoekresultaten. 
1. Selecteer **Resourcegroep verwijderen** op de pagina van de resourcegroep.
1. Typ de naam van de resourcegroep en selecteer vervolgens **Verwijderen**.
   
   ![Uw resourcegroep voor Azure Cache voor Redis verwijderen](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Maak een eenvoudige ASP.NET-web-app die gebruikmaakt van Azure Cache voor Redis.](./cache-web-app-howto.md)

