---
title: Logboeken en metrische gegevens weer geven met Kibana en Grafana
description: Logboeken en metrische gegevens weer geven met Kibana en Grafana
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 12/08/2020
ms.topic: how-to
ms.openlocfilehash: cb53aba300b933c78d9ac2f5fc5cf8054f3413e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "104669998"
---
# <a name="view-logs-and-metrics-using-kibana-and-grafana"></a>Logboeken en metrische gegevens weer geven met Kibana en Grafana

Kibana- en Grafana-webdashboards zijn bedoeld om inzicht en duidelijkheid te geven wat betreft de Kubernetes-naamruimten die worden gebruikt door Azure Arc-gegevensservices.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


## <a name="monitor-azure-sql-managed-instances-on-azure-arc"></a>Beheerde Azure SQL-instanties in azure Arc bewaken

Voer de volgende CLI-opdracht uit om toegang te krijgen tot de logboeken en bewakings dashboards voor met Arc ingeschakelde SQL Managed instance. `azdata`

```bash

azdata arc sql endpoint list -n <name of SQL instance>

```
De relevante Grafana-Dash boards zijn:

* "Metrische gegevens van Azure SQL Managed instance"
* "Metrische gegevens van het host-knoop punt"
* "Peul metrische gegevens van de host"


> [!NOTE]
>  Wanneer u wordt gevraagd om een gebruikers naam en wacht woord in te voeren, voert u de gebruikers naam en het wacht woord in die u hebt opgegeven op het moment dat u de Azure arctangens-gegevens controller hebt gemaakt.

> [!NOTE]
>  U wordt gevraagd om een certificaat waarschuwing omdat de certificaten die worden gebruikt in de preview zelfondertekende certificaten zijn.


## <a name="monitor-azure-database-for-postgresql-hyperscale-on-azure-arc"></a>Azure Database for PostgreSQL grootschalige in azure Arc bewaken

Voer de volgende CLI-opdracht uit om toegang te krijgen tot de logboeken en controle dashboards voor PostgreSQL grootschalige. `azdata`

```bash

azdata arc postgres endpoint list -n <name of postgreSQL instance>

```

De relevante postgreSQL-Dash boards zijn:

* "Metrische gegevens over post gres"
* "Metrische tabel gegevens post gres"
* "Metrische gegevens van het host-knoop punt"
* "Peul metrische gegevens van de host"


## <a name="additional-firewall-configuration"></a>Aanvullende firewall configuratie

Afhankelijk van waar de gegevens controller is geïmplementeerd, is het mogelijk dat u poorten op uw firewall moet openen om toegang te krijgen tot de Kibana-en Grafana-eind punten.

Hieronder ziet u een voor beeld van hoe u dit kunt doen voor een Azure-VM. U moet dit doen als u Kubernetes hebt geïmplementeerd met behulp van het script.

In de onderstaande stappen wordt uitgelegd hoe u een NSG-regel maakt voor de eind punten Kibana en Grafana:

### <a name="find-the-name-of-the-nsg"></a>De naam van de NSG zoeken

```azurecli
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

### <a name="add-the-nsg-rule"></a>De regel NSG toevoegen

Zodra u de naam van de NSG hebt, kunt u een regel toevoegen met behulp van de volgende opdracht:

```azurecli
az network nsg rule create -n ports_30777 --nsg-name azurearcvmNSG --priority 600 -g azurearcvm-rg --access Allow --description 'Allow Kibana and Grafana ports' --destination-address-prefixes '*' --destination-port-ranges 30777 --direction Inbound --protocol Tcp --source-address-prefixes '*' --source-port-ranges '*'
```


## <a name="next-steps"></a>Volgende stappen
- Probeer [metrische gegevens en logboeken te uploaden naar Azure monitor](upload-metrics-and-logs-to-azure-monitor.md)
- Meer informatie over Grafana:
   - [Aan de slag](https://grafana.com/docs/grafana/latest/getting-started/getting-started)
   - [Grond beginselen van Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
   - [Zelf studies voor Grafana](https://grafana.com/tutorials/grafana-fundamentals/#1)
- Meer informatie over Kibana
   - [Inleiding](https://www.elastic.co/webinars/getting-started-kibana?baymax=default&elektra=docs&storm=top-video)
   - [Kibana-hand leiding](https://www.elastic.co/guide/en/kibana/current/index.html)
   - [Inleiding tot dash board DrillDowns met gegevens visualisaties in Kibana](https://www.elastic.co/webinars/dashboard-drilldowns-with-data-visualizations-in-kibana/)
   - [Kibana-Dash boards maken](https://www.elastic.co/webinars/how-to-build-kibana-dashboards/)

