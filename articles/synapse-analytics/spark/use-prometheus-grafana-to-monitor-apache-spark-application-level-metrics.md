---
title: 'Zelf studie: Apache Spark meet waarden op toepassings niveau bewaken met Prometheus en Grafana'
description: 'Zelf studie: informatie over het implementeren van de Apache Spark-oplossing voor metrische gegevens van toepassingen naar een Azure Kubernetes service-cluster (AKS) en informatie over het integreren van de Grafana-Dash boards.'
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: 6a0b63dc7fda25e3911ae713a0bea7ae7a0969f9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104602295"
---
# <a name="tutorial-monitor-apache-spark-application-level-metrics-with-prometheus-and-grafana"></a>Zelf studie: gegevens van Apache Spark op toepassings niveau controleren met Prometheus en Grafana

## <a name="overview"></a>Overzicht

In deze zelf studie leert u hoe u de oplossing voor de Apache Spark metrische gegevens van de toepassing implementeert in een Azure Kubernetes service-cluster (AKS) en leert hoe u de Grafana-Dash boards kunt integreren.

U kunt deze oplossing gebruiken voor het verzamelen en opvragen van de Apache Spark metrische gegevens bij realtime. Met de geïntegreerde Grafana-Dash boards kunt u uw Apache Spark-toepassing diagnosticeren en bewaken. De bron code en de configuraties zijn open source op GitHub.

## <a name="prerequisites"></a>Vereisten

1.  [Azure CLI](/cli/azure/install-azure-cli)
2.  [3.30 helm-client](https://github.com/helm/helm/releases)
3.  [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
4.  [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/en-us/services/kubernetes-service/)

Of gebruik de [Azure Cloud shell](https://shell.azure.com/), die al de Azure CLI, helm-client en kubectl uit het-vak bevat.

## <a name="log-in-to-azure"></a>Meld u aan bij Azure.

```bash
az login
az account set --subscription "<subscription_id>"
```

## <a name="create-an-azure-kubernetes-service-instance-aks"></a>Een exemplaar van de Azure Kubernetes-service maken (AKS)

Gebruik de Azure CLI-opdracht voor het maken van een Kubernetes-cluster in uw abonnement.

```
az aks create --name <kubernetes_name> --resource-group <kubernetes_resource_group> --location <location> --node-vm-size Standard_D2s_v3
az aks get-credentials --name <kubernetes_name> --resource-group <kubernetes_resource_group>
```

Opmerking: deze stap kan worden overgeslagen als u al een AKS-cluster hebt.

## <a name="create-a-service-principal-and-grant-permission-to-synapse-workspace"></a>Een service-principal maken en machtigingen verlenen aan de Synapse-werk ruimte

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

Het resultaat moet er als volgt uitzien:

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

Noteer de appId, het wacht woord en de tenantID.

[![scherm afbeelding verlenen machtiging Srbac](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png#lightbox)

1. Meld u aan bij uw [Azure Synapse Analytics-werk ruimte](https://web.azuresynapse.net/) als Synapse-beheerder

2. Selecteer in Synapse Studio in het linkerdeel venster de optie **Manage > Access Control**

3. Klik op de knop toevoegen in de linkerbovenhoek om **een roltoewijzing toe te voegen**

4. Voor bereik kiest u **werk ruimte**

5. Kies voor rol **Synapse Compute-operator**

6. Voer voor gebruiker selecteren de **<in service_principal_name>** en klik op uw Service-Principal

7. Klik op **Toep assen** (wacht drie minuten om de machtiging te activeren.)

## <a name="install-connector-prometheus-server-grafana-dashboard"></a>Connector installeren, Prometheus server, Grafana-dash board

1. Voeg Synapse-Charts opslag plaats toe aan helm-client.

```bash
helm repo add synapse-charts https://github.com/microsoft/azure-synapse-spark-metrics/releases/download/helm-chart
```

2.  Onderdelen installeren via helm-client:

```bash
helm install spo synapse-charts/synapse-prometheus-operator --create-namespace --namespace spo \
    --set synapse.workspaces[0].workspace_name="<workspace_name>" \
    --set synapse.workspaces[0].tenant_id="<tenant_id>" \
    --set synapse.workspaces[0].service_principal_name="<service_principal_app_id>" \
    --set synapse.workspaces[0].service_principal_password="<service_principal_password>" \
    --set synapse.workspaces[0].subscription_id="<subscription_id>" \
    --set synapse.workspaces[0].resource_group="<workspace_resource_group_name>"
```

 - workspace_name: naam van Synapse-werk ruimte.
 - subscription_id: ID van Synapse-werkruimte abonnement.
 - workspace_resource_group_name: naam van resource groep voor Synapse-werk ruimte.
 - tenant_id: Tenant-ID van Synapse-werk ruimte.
 - service_principal_app_id: de service-principal ' appId '
 - service_principal_password: het wacht woord voor de Service-Principal dat u hebt gemaakt.

## <a name="log-in-to-grafana"></a>Aanmelden bij Grafana

Het standaard wachtwoord en-adres van Grafana ophalen. U kunt het wacht woord wijzigen in de Grafana-instellingen.

```bash
kubectl get secret --namespace spo spo-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
kubectl -n spo get svc spo-grafana
```

Ontvang service-IP, kopieer & plak het externe IP-adres naar de browser en meld u aan met de gebruikers naam beheerder en het wacht woord.

## <a name="use-grafana-dashboards"></a>Grafana-Dash boards gebruiken

Zoek Synapse dash board in de linkerbovenhoek van de Grafana-pagina (Home-> Synapse Workspace/Synapse toepassing), probeer een voorbeeld code uit te voeren in Synapse Studio en wacht enkele seconden op de metrische gegevens die worden opgehaald.

U kunt ook de Dash boards ' Synapse werk ruimte/werk ruimte ' en ' Synapse-werk ruimte/Spark-Pools ' gebruiken om een overzicht te krijgen van uw werk ruimte en uw Apache Spark groepen.

## <a name="uninstall"></a>Verwijderen

Verwijder de opdracht onderdelen op helm als volgt.

```bash
helm delete <release_name> -n <namespace>
```

Verwijder het AKS-cluster.

```bash
az aks delete --name <kubernetes_cluster_name> --resource-group <kubernetes_cluster_rg>
```

## <a name="components-introduction"></a>Inleiding tot onderdelen

Azure Synapse Analytics biedt een [helm-grafiek](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm) op basis van Prometheus-operator en Synapse Prometheus-connector. De helm-grafiek bevat Prometheus-server, Grafana-server en Grafana-Dash boards voor Apache Spark metrische gegevens op toepassings niveau. U kunt [Prometheus](https://prometheus.io/), een populair open-source-bewakings systeem, gebruiken om deze metrische gegevens bijna in realtime te verzamelen en [Grafana](https://github.com/grafana/grafana) te gebruiken voor visualisatie.

### <a name="synapse-prometheus-connector"></a>Synapse Prometheus-connector

Met Synapse Prometheus-connector kunt u verbinding maken met Azure Synapse Apache Spark pool en uw Prometheus-server. Het implementeert:

1.  Verificatie: het is authenticatie op basis van AAD en kan automatisch het AAD-token van de Service-Principal vernieuwen voor toepassings detectie, metrische opname en andere functies.
2.  Apache Spark toepassings detectie: wanneer u toepassingen in de doel werkruimte verzendt, kan de Synapse Prometheus-connector deze toepassingen automatisch detecteren.
3.  Meta gegevens van de toepassing Apache Spark: er worden elementaire toepassings gegevens verzameld en de gegevens geëxporteerd naar Prometheus.

Synapse Prometheus-connector wordt uitgebracht als een docker-installatie kopie die wordt gehost op [micro soft container Registry](https://github.com/microsoft/containerregistry). Het is open-source en bevindt zich in de [metrische gegevens van de Azure Synapse Spark-toepassing](https://github.com/microsoft/azure-synapse-spark-metrics).

### <a name="prometheus-server"></a>Prometheus-server

Prometheus is een open-source bewakings-Toolkit en waarschuwingen. Prometheus is afgeleid van de cloud computing Foundation (CNCF) en is de feitelijke standaard voor Cloud-systeem eigen bewaking geworden. Prometheus kan ons helpen om enorme hoeveel heden tijdreeks gegevens te verzamelen, op te vragen en op te slaan, en kan eenvoudig worden geïntegreerd met Grafana. In deze oplossing implementeren we het Prometheus-onderdeel op basis van het helm-diagram.

### <a name="grafana-and-dashboards"></a>Grafana en dash boards

Grafana is open-source visualisatie en analyse software. U kunt hiermee uw metrische gegevens opvragen, visualiseren, waarschuwen en verkennen. Azure Synapse Analytics bevat een aantal standaard Grafana-Dash boards voor het Apache Spark visualiseren van metrische gegevens op toepassings niveau.

Het dash board Synapse werk ruimte/werk ruimte biedt een weer gave op werkruimte niveau van alle Apache Spark groepen, toepassings aantallen, CPU-kernen enzovoort.

[![werk ruimte voor scherm afbeeldings dashboard](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png#lightbox)

Het dash board Synapse-werk ruimte/Spark-Pools bevat de metrische gegevens van Apache Spark toepassingen die in de geselecteerde Apache Spark groep worden uitgevoerd tijdens de periode.

[![scherm opname dashboard sparkpool](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png#lightbox)

Het dash board Synapse-werk ruimte/Spark-toepassing bevat de geselecteerde Apache Spark toepassing.

[![scherm opname dashboard toepassing](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png#lightbox)

De bovenstaande dashboard sjablonen zijn open source in de [metrische gegevens van de Azure Synapse Spark-toepassing](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm/synapse-prometheus-operator/grafana_dashboards).