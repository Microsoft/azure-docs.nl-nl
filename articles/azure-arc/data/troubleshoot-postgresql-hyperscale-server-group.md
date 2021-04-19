---
title: Problemen met PostgreSQL Hyperscale-servergroepen oplossen
description: Problemen met PostgreSQL Hyperscale-servergroepen oplossen met een Jupyter Notebook
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: caaab07200a8631935a2b5d5368a0c16ea9a60c5
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "92320220"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>Problemen met PostgreSQL Hyperscale-servergroepen oplossen
In dit artikel worden enkele technieken beschreven die u kunt gebruiken om problemen met uw servergroep op te lossen. Naast dit artikel kunt u lezen hoe u [Kibana](monitor-grafana-kibana.md) kunt gebruiken om de logboeken te doorzoeken of [Grafana](monitor-grafana-kibana.md) kunt gebruiken om metrische gegevens over uw servergroep te visualiseren. 

## <a name="getting-more-details-about-the-execution-of-an-azdata-command"></a>Meer informatie over de uitvoering van een azdata-opdracht
U kunt de parameter **--debug toevoegen aan** elke azdata-opdracht die u uitvoert. Als u dit doet, wordt in de console aanvullende informatie weergegeven over de uitvoering van die opdracht. Het is handig om meer informatie te krijgen om inzicht te krijgen in het gedrag van die opdracht.
U kunt bijvoorbeeld uitvoeren
```console
azdata arc postgres server create -n postgres01 -w 2 --debug
```

of
```console
azdata arc postgres server edit -n postgres01 --extension SomeExtensionName --debug
```

Bovendien kunt u de parameter --help gebruiken op elke azdata-opdracht om enkele Help-opdrachten weer te geven, een lijst met parameters voor een specifieke opdracht. Bijvoorbeeld:
```console
azdata arc postgres server create --help
```


## <a name="collecting-logs-of-the-data-controller-and-your-server-groups"></a>Logboeken van de gegevenscontroller en uw servergroepen verzamelen
Lees het artikel over het [verkrijgen van logboeken voor Azure Arc-gegevensservices](troubleshooting-get-logs.md)



## <a name="interactive-troubleshooting-with-jupyter-notebooks-in-azure-data-studio"></a>Interactieve probleemoplossing met Jupyter-notebooks in Azure Data Studio
Met notebooks kunt u procedures documenteren door Markdown-inhoud op te nemen om te beschrijven wat er moet gebeuren en hoe. Ze kunnen ook uitvoerbare code bevatten voor het automatiseren van een procedure.  Dit patroon is handig voor alles van standaardprocedures tot handleidingen voor het oplossen van problemen.

Laten we bijvoorbeeld een PostgreSQL Hyperscale-servergroep oplossen die mogelijk problemen heeft met het gebruik van Azure Data Studio.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

### <a name="install-tools"></a>Hulpprogramma's installeren

Installeer Azure Data Studio en `kubectl` op de clientcomputer die u gebruikt om het notebook uit te voeren [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] in Azure Data Studio. Volg hiervoor de instructies in [Clienthulpprogramma's installeren](install-client-tools.md)

### <a name="update-the-path-environment-variable"></a>De omgevingsvariabele PATH bijwerken

Zorg ervoor dat deze hulpprogramma's vanaf elke locatie op deze clientmachine kunnen worden aangeroepen. Werk bijvoorbeeld op een Windows-clientmachine de omgevingsvariabele PATH bij en voeg de map toe waarin u kubectl hebt geïnstalleerd.

### <a name="sign-in-with-azure-data-cli-azdata"></a>Aanmelden met [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

Meld u aan bij uw Arc-gegevenscontroller vanaf deze clientmachine en voordat u de Azure Data Studio. Voer hiervoor een opdracht uit zoals:

```console
azdata login --endpoint https://<IP address>:<port>
```

Vervang `<IP address>` door het IP-adres van uw Kubernetes-cluster en de `<port>` poort waarop Kubernetes luistert. U wordt gevraagd om de gebruikersnaam en het wachtwoord. Voer voor meer informatie uit:

```console
azdata login --help
```

### <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>Meld u aan bij uw Kubernetes-cluster met kubectl

Als u dit wilt doen, kunt u de voorbeeldopdrachten in [dit](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) blogbericht gebruiken.
U voert opdrachten uit zoals:

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### <a name="the-troubleshooting-notebook"></a>Het notebook voor probleemoplossing

Start Azure Data Studio en open het notebook voor probleemoplossing. 

Implementeert de stappen die in de volgende  [033-manage-Postgres-with-AzureDataStudio.md](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) beschreven:

1. Verbinding maken met uw Arc-gegevenscontroller
2. Selecteer met de rechterkant uw Postgres-exemplaar en kies **[Beheren]**
3. Selecteer het **dashboard [Problemen vaststellen en oplossen]**
4. Selecteer de **koppeling [Problemen oplossen]**

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio- PostgreSQL-notebook voor probleemoplossing openen":::

Het **notebook TSG100 - De** Azure PostgreSQL Hyperscale met Azure Arc-probleemoplosser wordt geopend: :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio - PostgreSQL-notebook"::: voor probleemoplossing gebruiken

#### <a name="run-the-scripts"></a>De scripts uitvoeren
Selecteer de knop Alles uitvoeren bovenaan om het notebook in één keer uit te voeren, of u kunt elke codecel een voor een stapsgewijs uitvoeren.

Bekijk de uitvoer van de uitvoering van de codecellen voor mogelijke problemen.

In de tijd zullen we meer details toevoegen aan het notebook over het herkennen van veelvoorkomende problemen en het oplossen ervan.

## <a name="next-step"></a>Volgende stap
- Meer informatie over [het verkrijgen van logboeken voor Azure Arc-gegevensservices](troubleshooting-get-logs.md)
- Meer informatie over [het doorzoeken van logboeken met Kibana](monitor-grafana-kibana.md)
- Meer informatie over [bewaking met Grafana](monitor-grafana-kibana.md)
- Uw eigen notebooks maken
