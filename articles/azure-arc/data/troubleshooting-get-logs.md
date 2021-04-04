---
title: Logboeken ophalen voor het oplossen van problemen met Azure Arc ingeschakelde gegevens Services
description: Informatie over het ophalen van logboek bestanden van een gegevens controller voor het oplossen van problemen met Azure Arc ingeschakelde gegevens Services.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 0c4cff7583f08fe27649cee464fcef802cddd88f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93234037"
---
# <a name="get-logs-to-troubleshoot-azure-arc-enabled-data-services"></a>Logboeken ophalen voor het oplossen van problemen met Azure Arc ingeschakelde gegevens Services

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u doorgaat, hebt u het volgende nodig:

* [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]. Zie [client hulpprogramma's voor het implementeren en beheren van Azure Arc Data Services installeren](./install-client-tools.md)voor meer informatie.
* Een beheerders account om u aan te melden bij de gegevens controller voor Azure Arc ingeschakeld.

## <a name="get-log-files"></a>Logboek bestanden ophalen

U kunt Service logboeken over alle verschillende of specifieke doel einden ophalen voor het oplossen van problemen. Een manier is om standaard Kubernetes-hulpprogram ma's zoals de `kubectl logs` opdracht te gebruiken. In dit artikel gebruikt u het [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] hulp programma, waardoor het eenvoudiger is om alle logboeken tegelijk op te halen.

1. Meld u aan bij de gegevens controller met een Administrator-account.

   ```console
   azdata login
   ```

2. Voer de volgende opdracht uit om de logboeken te dumpen:

   ```console
   azdata arc dc debug copy-logs --namespace <namespace name> --exclude-dumps --skip-compress
   ```

   Bijvoorbeeld:

   ```console
   #azdata arc dc debug copy-logs --namespace arc --exclude-dumps --skip-compress
   ```

De gegevens controller maakt de logboek bestanden in de huidige werkmap in een submap met de naam `logs` . 

## <a name="options"></a>Opties

De `azdata arc dc debug copy-logs` opdracht biedt de volgende opties voor het beheren van de uitvoer:

* Uitvoer de logboek bestanden naar een andere map met behulp van de `--target-folder` para meter.
* Comprimeer de bestanden door de para meter te weglaten `--skip-compress` .
* Activeer en voeg geheugen dumps toe door te weglaten `--exclude-dumps` . Deze methode wordt niet aanbevolen, tenzij Microsoft Ondersteuning de geheugen dumps heeft aangevraagd. Voor het ophalen van een geheugen dump moet de gegevens controller instelling worden `allowDumps` ingesteld op `true` wanneer de gegevens controller wordt gemaakt.
* Filter voor het verzamelen van Logboeken voor alleen een specifieke pod ( `--pod` ) of container ( `--container` ) op naam.
* Filter voor het verzamelen van Logboeken voor een specifieke aangepaste resource door de `--resource-kind` `--resource-name` para meters en te geven. De `resource-kind` parameter waarde moet een van de aangepaste resource definitie namen zijn. U kunt deze namen ophalen met behulp van de opdracht `kubectl get customresourcedefinition` .

Met deze para meters kunt u de `<parameters>` in het volgende voor beeld vervangen: 

```console
azdata arc dc debug copy-logs --target-folder <desired folder> --exclude-dumps --skip-compress -resource-kind <custom resource definition name> --resource-name <resource name> --namespace <namespace name>
```

Bijvoorbeeld:

```console
#azdata arc dc debug copy-logs --target-folder C:\temp\logs --exclude-dumps --skip-compress --resource-kind postgresql-12 --resource-name pg1 --namespace arc
```

De volgende maphiërarchie is een voor beeld. Het is ingedeeld op pod name, then container en vervolgens op Directory-hiërarchie in de container.

```output
<export directory>
├───debuglogs-arc-20200827-180403
│   ├───bootstrapper-vl8j2
│   │   └───bootstrapper
│   │       ├───apt
│   │       └───fsck
│   ├───control-j2dm5
│   │   ├───controller
│   │   │   └───controller
│   │   │       ├───2020-08-27
│   │   │       └───2020-08-28
│   │   └───fluentbit
│   │       ├───agent
│   │       ├───fluentbit
│   │       └───supervisor
│   │           └───log
│   ├───controldb-0
│   │   ├───fluentbit
│   │   │   ├───agent
│   │   │   ├───fluentbit
│   │   │   └───supervisor
│   │   │       └───log
│   │   └───mssql-server
│   │       ├───agent
│   │       ├───mssql
│   │       ├───mssql-server
│   │       └───supervisor
│   │           └───log
│   ├───controlwd-ln6j8
│   │   └───controlwatchdog
│   │       └───controlwatchdog
│   ├───logsdb-0
│   │   └───elasticsearch
│   │       ├───agent
│   │       ├───elasticsearch
│   │       ├───provisioner
│   │       └───supervisor
│   │           └───log
│   ├───logsui-7gg2d
│   │   └───kibana
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───kibana
│   │       └───supervisor
│   │           └───log
│   ├───metricsdb-0
│   │   └───influxdb
│   │       ├───agent
│   │       ├───influxdb
│   │       └───supervisor
│   │           └───log
│   ├───metricsdc-2f62t
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-jznd2
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsdc-n5vnx
│   │   └───telegraf
│   │       ├───agent
│   │       ├───apt
│   │       ├───fsck
│   │       ├───supervisor
│   │       │   └───log
│   │       └───telegraf
│   ├───metricsui-h748h
│   │   └───grafana
│   │       ├───agent
│   │       ├───grafana
│   │       └───supervisor
│   │           └───log
│   └───mgmtproxy-r5zxs
│       ├───fluentbit
│       │   ├───agent
│       │   ├───fluentbit
│       │   └───supervisor
│       │       └───log
│       └───service-proxy
│           ├───agent
│           ├───nginx
│           └───supervisor
│               └───log
└───debuglogs-kube-system-20200827-180431
    ├───coredns-8bbb65c89-kklt7
    │   └───coredns
    ├───coredns-8bbb65c89-z2vvr
    │   └───coredns
    ├───coredns-autoscaler-5585bf8c9f-g52nt
    │   └───autoscaler
    ├───kube-proxy-5c9s2
    │   └───kube-proxy
    ├───kube-proxy-h6x56
    │   └───kube-proxy
    ├───kube-proxy-nd2b7
    │   └───kube-proxy
    ├───metrics-server-5f54b8994-vpm5r
    │   └───metrics-server
    └───tunnelfront-db87f4cd8-5xwxv
        ├───tunnel-front
        │   ├───apt
        │   └───journal
        └───tunnel-probe
            ├───apt
            ├───journal
            └───openvpn
```

## <a name="next-steps"></a>Volgende stappen

[azdata Arc DC-fout opsporing kopiëren-logboeken](/sql/azdata/reference/reference-azdata-arc-dc-debug#azdata-arc-dc-debug-copy-logs?toc=/azure/azure-arc/data/toc.json&bc=/azure/azure-arc/data/breadcrumb/toc.json)
