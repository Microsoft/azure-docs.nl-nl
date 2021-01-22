---
title: Azure Spring Cloud-applogboeken in realtime streamen
description: Logboek streaming gebruiken om toepassings logboeken onmiddellijk weer te geven
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 01/14/2019
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 1eeb291c7a058efd8905e95ebf1ea14fed046691
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98680515"
---
# <a name="stream-azure-spring-cloud-app-logs-in-real-time"></a>Azure Spring Cloud-applogboeken in realtime streamen

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

Met Azure lente-Cloud kunt u logboek streaming in azure CLI inschakelen voor het verkrijgen van real-time toepassings console logboeken voor het oplossen van problemen. U kunt ook [Logboeken en metrische gegevens analyseren met Diagnostische instellingen](./diagnostic-services.md).

## <a name="prerequisites"></a>Vereisten

* Installeer de [Azure cli-extensie](/cli/azure/install-azure-cli) voor de lente-Cloud, mini maal versie 0.2.0.
* Een exemplaar van **Azure lente Cloud** met een actieve toepassing, bijvoorbeeld [lente-Cloud-app](./spring-cloud-quickstart.md).

> [!NOTE]
>  De ASC CLI-extensie wordt bijgewerkt van versie 0.2.0 naar 0.2.1. Deze wijziging is van invloed op de syntaxis van de opdracht voor logboek streaming: `az spring-cloud app log tail` , die wordt vervangen door: `az spring-cloud app logs` . De opdracht: `az spring-cloud app log tail` wordt afgeschaft in een toekomstige release. Als u versie 0.2.0 hebt gebruikt, kunt u een upgrade uitvoeren naar 0.2.1. Verwijder eerst de oude versie met behulp van de opdracht: `az extension remove -n spring-cloud` .  Installeer vervolgens 0.2.1 met de opdracht: `az extension add -n spring-cloud` .

## <a name="use-cli-to-tail-logs"></a>CLI gebruiken om logboeken af te staart

Als u de naam van de resource groep en het service-exemplaar herhaaldelijk wilt opgeven, stelt u de standaard naam van de resource groep en de naam van het cluster in.
```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```
In de volgende voor beelden wordt de resource groep en de service naam wegge laten in de opdrachten.

### <a name="tail-log-for-app-with-single-instance"></a>Staart logboek voor app met één exemplaar
Als een app met de naam auth-service slechts één exemplaar heeft, kunt u het logboek van het app-exemplaar weer geven met de volgende opdracht:
```azurecli
az spring-cloud app logs -n auth-service
```
Hiermee worden logboeken geretourneerd:
```output
...
2020-01-15 01:54:40.481  INFO [auth-service,,,] 1 --- [main] o.apache.catalina.core.StandardService  : Starting service [Tomcat]
2020-01-15 01:54:40.482  INFO [auth-service,,,] 1 --- [main] org.apache.catalina.core.StandardEngine  : Starting Servlet engine: [Apache Tomcat/9.0.22]
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.a.c.c.C.[Tomcat].[localhost].[/uaa]  : Initializing Spring embedded WebApplicationContext
2020-01-15 01:54:40.760  INFO [auth-service,,,] 1 --- [main] o.s.web.context.ContextLoader  : Root WebApplicationContext: initialization completed in 7203 ms

...
```

### <a name="tail-log-for-app-with-multiple-instances"></a>Staart logboek voor app met meerdere exemplaren
Als er meerdere exemplaren bestaan voor de naam van de app `auth-service` , kunt u het exemplaar logboek weer geven met behulp van de `-i/--instance` optie. 

U kunt eerst de naam van het app-exemplaar ophalen met de volgende opdracht.

```azurecli
az spring-cloud app show -n auth-service --query properties.activeDeployment.properties.instances -o table
```
Met resultaten:

```output
Name                                         Status    DiscoveryStatus
-------------------------------------------  --------  -----------------
auth-service-default-12-75cc4577fc-pw7hb  Running   UP
auth-service-default-12-75cc4577fc-8nt4m  Running   UP
auth-service-default-12-75cc4577fc-n25mh  Running   UP
``` 
Vervolgens kunt u logboeken van een app-exemplaar streamen met de optie optie `-i/--instance` :

```azurecli
az spring-cloud app logs -n auth-service -i auth-service-default-12-75cc4577fc-pw7hb
```

U kunt ook details van app-exemplaren uit de Azure Portal ophalen.  Nadat u **apps** hebt geselecteerd in het linkernavigatievenster van uw Azure lente-Cloud service, selecteert u **app-exemplaren**.

### <a name="continuously-stream-new-logs"></a>Doorlopend streamen nieuwe logboeken
Standaard `az spring-cloud ap log tail` worden alleen bestaande logboeken afgedrukt die naar de app-console worden gestreamd en vervolgens afgesloten. Als u nieuwe logboeken wilt streamen, voegt u-f (--follow):  

```azurecli
az spring-cloud app logs -n auth-service -f
``` 
Controleren of alle opties voor logboek registratie worden ondersteund:
```azurecli
az spring-cloud app logs -h 
```

## <a name="next-steps"></a>Volgende stappen
* [Quickstart: Azure Spring Cloud-apps bewaken met logboeken, metrische gegevens en tracering](spring-cloud-quickstart-logs-metrics-tracing.md)
* [Logboeken en metrische gegevens analyseren met Diagnostische instellingen](./diagnostic-services.md)

