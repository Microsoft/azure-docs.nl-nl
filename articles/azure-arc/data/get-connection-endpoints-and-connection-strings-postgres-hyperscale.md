---
title: Verbindings eindpunten en formulier verbindings reeksen ophalen voor de PostgreSQL grootschalige-Server groep met Arc ingeschakeld
titleSuffix: Azure Arc enabled data services
description: Verbindings eindpunten en formulier verbindings reeksen ophalen voor de PostgreSQL grootschalige-Server groep met Arc ingeschakeld
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: de7d23689ae984ea0abece5edb03cf8a0c3a9be1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670338"
---
# <a name="get-connection-endpoints-and-form-connection-strings-for-your-arc-enabled-postgresql-hyperscale-server-group"></a>Verbindings eindpunten en formulier verbindings reeksen ophalen voor de PostgreSQL grootschalige-Server groep met Arc ingeschakeld

In dit artikel wordt uitgelegd hoe u de verbindings eindpunten voor uw server groep kunt ophalen en hoe u verbindings reeksen maakt die u gebruikt voor uw toepassingen en/of hulpprogram ma's.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>Eind punten voor verbinding ophalen:

### <a name="from-cli-with-azdata"></a>Van CLI met azdata
#### <a name="1-connect-to-your-arc-data-controller"></a>1. Maak verbinding met uw Arc-gegevens controller:
- Als u al een sessie hebt geopend op de host van de Arc-gegevens controller: Voer de volgende opdracht uit:
```console
azdata login
```

- Als u geen sessie hebt geopend op de host van de Arc-gegevens controller: Voer de volgende opdracht uit 
```console
azdata login --endpoint https://<external IP address of host/data controller>:30080
```

#### <a name="2-show-the-connection-endpoints"></a>2. de verbindings eindpunten weer geven
Voer de volgende opdracht uit:
```console
azdata arc postgres endpoint list -n <server group name>
```
Bijvoorbeeld:
```console
azdata arc postgres endpoint list -n postgres01
```

De lijst met eind punten wordt weer gegeven: het PostgreSQL-eind punt dat u gebruikt om verbinding te maken met uw toepassing en de data base-, Kibana-en Grafana-eind punten voor log Analytics en controle te gebruiken. Bijvoorbeeld: 
```console
Arc
 ===================================================================================================================
 Postgres01 Instance
 -------------------------------------------------------------------------------------------------------------------
 Description           Endpoint

 PostgreSQL Instance   postgresql://postgres:<replace with password>@12.345.567.89:5432
 Log Search Dashboard  https://89.345.712.81:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))
 Metrics Dashboard     https://89.345.712.81:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01

```
Gebruik deze eind punten voor het volgende:
- Uw verbindings reeksen vormen en verbinding maken met uw client hulpprogramma's of-toepassingen
- Toegang tot de Grafana- en Kibana-dashboards vanuit uw browser

U kunt bijvoorbeeld het eind punt met de naam _postgresql instance_ gebruiken om verbinding te maken met psql met uw server groep. Bijvoorbeeld:
```console
psql postgresql://postgres:MyPassworkd@12.345.567.89:5432
psql (10.14 (Ubuntu 10.14-0ubuntu0.18.04.1), server 12.4 (Ubuntu 12.4-1.pgdg16.04+1))
WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
> [!NOTE]
>
> - Het wacht woord van de _post gres_ -gebruiker die is opgegeven in het eind punt '_postgresql instance_' is het wacht woord dat u hebt gekozen bij het implementeren van de Server groep.
> - Over azdata: de lease die aan uw verbinding is gekoppeld, duurt ongeveer 10 uur. Daarna moet u opnieuw verbinding maken. Als uw lease is verlopen, wordt het volgende fout bericht weer gegeven wanneer u probeert een opdracht uit te voeren met azdata (met uitzonde ring van azdata-aanmelding): _fout: (401)_ 
>  _reden: niet-geautoriseerde_ 
>  _http-antwoord headers: HTTPHeaderDict ({' date ': ' Sun, 06 sep 2020 16:58:38 GMT ', ' content-length ': ' 0 ', ' www-authenticatie ': '_ 
>  _Basic realm = "aanmeldings_ referenties vereist", Bearer-fout = "invalid_token", error_description = "het token is verlopen"}) _ wanneer dit gebeurt, moet u opnieuw verbinding maken met azdata zoals hierboven is uitgelegd.

## <a name="from-cli-with-kubectl"></a>Van CLI met kubectl
- Als uw server groep van post gres versie 12 (standaard) is, wordt de volgende opdracht uitgevoerd:
```console
kubectl get postgresql-12/<server group name> -n <namespace name>
```
- Als uw server groep van post gres versie 11 is, is de volgende opdracht:
```console
kubectl get postgresql-11/<server group name> -n <namespace name>
```

Deze opdrachten produceren uitvoer zoals hieronder. U kunt deze informatie gebruiken om de verbindings reeksen te maken:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   3/3          123.456.789.4:31066      5d20h
``` 


## <a name="form-connection-strings"></a>Verbindings reeksen voor formulier:
Gebruik de onderstaande tabel met sjablonen van verbindings reeksen voor uw server groep. U kunt deze vervolgens kopiëren/plakken en aanpassen als meer nodig:

### <a name="adonet"></a>ADO.NET

```ado.net
Server=192.168.1.121;Database=postgres;Port=24276;User Id=postgres;Password={your_password_here};Ssl Mode=Require;`
```

### <a name="c-libpq"></a>C++ (libpq)

```cpp
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="jdbc"></a>JDBC

```jdbc
jdbc:postgresql://192.168.1.121:24276/postgres?user=postgres&password={your_password_here}&sslmode=require
```

### <a name="nodejs"></a>Node.js

```node.js
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="php"></a>PHP

```php
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="psql"></a>psql

```psql
psql "host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require"
```

### <a name="python"></a>Python

```python
dbname='postgres' user='postgres' host='192.168.1.121' password='{your_password_here}' port='24276' sslmode='true'
```

### <a name="ruby"></a>Ruby

```ruby
host=192.168.1.121; dbname=postgres user=postgres password={your_password_here} port=24276 sslmode=require
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [uitschalen (werk knooppunten toevoegen)](scale-out-postgresql-hyperscale-server-group.md) voor uw server groep
- Meer informatie over het [schalen van omhoog of omlaag (verg Roten/verkleinen van geheugen/vcores)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) uw server groep


