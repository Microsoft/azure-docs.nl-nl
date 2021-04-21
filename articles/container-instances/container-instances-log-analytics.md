---
title: Resourcelogboeken & analyseren
description: Meer informatie over het verzenden van resourcelogboeken en gebeurtenisgegevens van containergroepen in Azure Container Instances naar Azure Monitor logboeken
ms.topic: article
ms.date: 07/13/2020
ms.openlocfilehash: e46a1df65a4cfe5d10a58704aff485aa2834b55f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763915"
---
# <a name="container-group-and-instance-logging-with-azure-monitor-logs"></a>Logboekregistratie van containergroep en exemplaar met Azure Monitor logboeken

Log Analytics-werkruimten bieden een centrale locatie voor het opslaan en opvragen van logboekgegevens, niet alleen vanuit Azure-resources, maar ook on-premises resources en resources in andere clouds. Azure Container Instances bevat ingebouwde ondersteuning voor het verzenden van logboeken en gebeurtenisgegevens naar Azure Monitor logboeken.

Als u containergroeplogboek- en gebeurtenisgegevens wilt verzenden naar Azure Monitor logboeken, geeft u een bestaande Log Analytics-werkruimte-id en werkruimtesleutel op bij het configureren van een containergroep. 

In de volgende secties wordt beschreven hoe u een containergroep met logboekregistratie maakt en hoe u een query uitvoert op logboeken. U kunt ook een [containergroep bijwerken met een](container-instances-update.md) werkruimte-id en werkruimtesleutel om logboekregistratie mogelijk te maken.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> Op dit moment kunt u alleen gebeurtenisgegevens verzenden van Linux-container-exemplaren naar Log Analytics.

## <a name="prerequisites"></a>Vereisten

Voor het inschakelen van logboekregistratie voor uw containerexemplaren hebt u het volgende nodig:

* [Log Analytics-werkruimte](../azure-monitor/logs/quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli) (of [Cloud Shell](../cloud-shell/overview.md))

## <a name="get-log-analytics-credentials"></a>Log Analytics-referenties verkrijgen

Azure Container Instances heeft toestemming nodig om gegevens te verzenden naar uw Log Analytics-werkruimte. U kunt deze machtiging verlenen en de mogelijkheid tot logboekregistratie inschakelen als u de Log Analytics-werkruimte-id en een van de sleutels ervan opgeeft (primaire of secundaire) op het moment dat u de containergroep maakt.

De Log Analytics-werkruimte-id en de primaire sleutel verkrijgt u als volgt:

1. Navigeer in Azure Portal naar uw Log Analytics-werkruimte.
1. Selecteer **onder Instellingen** de optie **Agentbeheer**
1. Noteer het volgende:
   * **Werkruimte-id**
   * **Primaire sleutel**

## <a name="create-container-group"></a>Containergroep maken

Nu dat u de Log Analytics-werkruimte-id en de primaire sleutel hebt, kunt u een containergroep maken die geschikt is voor logboekregistratie.

In de volgende voorbeelden worden twee manieren getoond om een containergroep te maken die bestaat uit één [Fluentd-container:][fluentd] Azure CLI en Azure CLI met een YAML-sjabloon. De Fluentd-container produceert verschillende regels uitvoer in de standaardconfiguratie. Omdat deze uitvoer naar de Log Analytics-werkruimte wordt verzonden, is deze heel geschikt is om te illustreren hoe logboeken kunnen worden weergegeven en hoe er query's op kunnen worden uitgevoerd.

### <a name="deploy-with-azure-cli"></a>Implementeren met Azure CLI

Als u Azure CLI wilt gebruiken om te implementeren, moet u de parameters `--log-analytics-workspace` en `--log-analytics-workspace-key` opgeven in de opdracht [az container create][az-container-create]. Vervang de twee werkruimtewaarden door de waarden die u hebt verkregen in de vorige stap (en werk de naam van de resourcegroep bij) voordat u de volgende opdracht gaat uitvoeren.

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>Implementeren met YAML

Gebruik deze methode als u liever containergroepen met YAML wilt implementeren. Met de volgende YAML wordt een containergroep met een enkele container gedefinieerd. Kopieer de YAML naar een nieuw bestand en vervang `LOG_ANALYTICS_WORKSPACE_ID` en `LOG_ANALYTICS_WORKSPACE_KEY` door de waarden die u hebt verkregen in de vorige stap. Sla het bestand op als **deploy-aci.yaml**.

```yaml
apiVersion: 2019-12-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Voer vervolgens de volgende opdracht uit om de containergroep te implementeren. Vervang `myResourceGroup` door een resourcegroep in uw abonnement (of maak eerst een resourcegroep met de naam myResourceGroup):

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

Kort nadat u de opdracht hebt opgegeven, zou u een reactie van Azure met implementatiedetails moeten ontvangen.

## <a name="view-logs"></a>Logboeken weergeven

Nadat u de containergroep hebt geïmplementeerd, kan het enkele minuten (wel tien minuten) duren voor de eerste logboekvermeldingen worden weergegeven in Azure Portal. 

De logboeken van de containergroep in de tabel `ContainerInstanceLog_CL` weergeven:

1. Navigeer in Azure Portal naar uw Log Analytics-werkruimte.
1. Selecteer **onder Algemeen** de optie **Logboeken**  
1. Typ de volgende query: `ContainerInstanceLog_CL | limit 50`
1. Selecteer **Uitvoeren**

Er worden verschillende resultaten weergegeven door de query. Als u in eerste instantie geen resultaten ziet, wacht u een paar minuten en selecteert u vervolgens de knop **Uitvoeren** om de query opnieuw uit te voeren. Standaard worden logboekgegevens weergegeven **in** tabelindeling. U kunt vervolgens een rij uitbreiden om de inhoud van een afzonderlijke logboekvermelding te zien.

![Resultaten van zoekopdrachten in logboeken in Azure Portal][log-search-01]

## <a name="view-events"></a>Gebeurtenissen weergeven

U kunt ook gebeurtenissen voor container instances in de Azure Portal. Gebeurtenissen omvatten het tijdstip waarop het exemplaar wordt gemaakt en wanneer het wordt gestart. De gebeurtenisgegevens in de tabel `ContainerEvent_CL` weergeven:

1. Navigeer in Azure Portal naar uw Log Analytics-werkruimte.
1. Selecteer **onder Algemeen** de optie **Logboeken**  
1. Typ de volgende query: `ContainerEvent_CL | limit 50`
1. Selecteer **Uitvoeren**

Er worden verschillende resultaten weergegeven door de query. Als u in eerste instantie geen resultaten ziet, wacht u een paar minuten en selecteert u vervolgens de knop **Uitvoeren** om de query opnieuw uit te voeren. Standaard worden vermeldingen weergegeven **in** tabelindeling. Vervolgens kunt u een rij uitv vouwen om de inhoud van een afzonderlijke vermelding weer te geven.

![Resultaten van Event Search in de Azure Portal][log-search-02]

## <a name="query-container-logs"></a>Query's uitvoeren op containerlogboeken

Azure Monitor-logboeken bevat een uitgebreide [querytaal][query_lang] voor het ophalen van gegevens uit mogelijk duizenden regels aan logboekuitvoer.

De basisstructuur van een query is de brontabel (in dit artikel of ), gevolgd door een reeks operators, gescheiden door `ContainerInstanceLog_CL` `ContainerEvent_CL` het s pipe-teken ( `|` ). U kunt verschillende operatoren aan elkaar koppelen om de resultaten te verfijnen en geavanceerde functies uit te voeren.

Als u voorbeeldqueryresultaten wilt zien, plakt u de volgende query in het querytekstvak en selecteert u de **knop Uitvoeren** om de query uit te voeren. Deze query geeft alle logboekvermeldingen weer waarvan het veld 'Bericht' het woord 'waarschuwen' bevat:

```query
ContainerInstanceLog_CL
| where Message contains "warn"
```

Complexere query's worden ook ondersteund. Met deze query worden bijvoorbeeld de logboekvermeldingen voor de containergroep 'mycontainergroup001' weergegeven die het afgelopen uur zijn gegenereerd:

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>Volgende stappen

### <a name="azure-monitor-logs"></a>Azure Monitor-logboeken

Voor meer informatie over het uitvoeren van query's op logboeken en het configureren van waarschuwingen in Azure Monitor-logboeken, zie:

* [Inzicht in zoekopdrachten in logboeken Azure Monitor logboeken](../azure-monitor/logs/log-query-overview.md)
* [Consistente waarschuwingen in Azure Monitor](../azure-monitor/alerts/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>CPU en geheugen bewaken

Voor informatie over het bewaken van CPU- en geheugenresources van containerinstanties, zie:

* [Containerresources in Azure Container Instances bewaken](container-instances-monitor.md).

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png
[log-search-02]: ./media/container-instances-log-analytics/portal-query-02.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: /azure/data-explorer/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create