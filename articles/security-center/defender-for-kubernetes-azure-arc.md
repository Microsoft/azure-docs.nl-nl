---
title: Hybride en implementaties met meerdere Cloud Kubernetes beveiligen met Azure Defender voor Kubernetes
description: Azure Defender gebruiken voor Kubernetes met uw on-premises en multi-Cloud Kubernetes-clusters
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 04/06/2021
ms.author: memildin
ms.openlocfilehash: 940cae8829a99ee7ffacdb41844237acc85b7761
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029196"
---
# <a name="defend-azure-arc-enabled-kubernetes-clusters-running-in-on-premises-and-multi-cloud-environments"></a>Azure Arc enabled Kubernetes-clusters die worden uitgevoerd in on-premises en omgevingen met meerdere clouds beschermen

Met de **uitbrei ding Azure Defender for Kubernetes-clusters** kunt u uw on-premises clusters beschermen met dezelfde bedreigingen detectie mogelijkheden als Azure Kubernetes-Service clusters. Schakel [Azure Arc enabled Kubernetes](../azure-arc/kubernetes/overview.md) in op uw clusters en implementeer de extensie zoals beschreven op deze pagina. 

De uitbrei ding kan ook Kubernetes-clusters op andere cloud providers beveiligen, maar niet op hun beheerde Kubernetes-Services.

> [!TIP]
> We hebben een aantal voorbeeld bestanden geplaatst om te helpen bij het installatie proces in [installatie voorbeelden op github](https://aka.ms/kubernetes-extension-installation-examples).

## <a name="availability"></a>Beschikbaarheid

| Aspect | Details |
|--------|---------|
| Release status | **Preview**<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]|
| Vereiste rollen en machtigingen | [Beveiligings beheerder](../role-based-access-control/built-in-roles.md#security-admin) kan waarschuwingen negeren<br>[Beveiligings lezer](../role-based-access-control/built-in-roles.md#security-reader) kan bevindingen weer geven |
| Prijzen | [Azure Defender voor Kubernetes](defender-for-kubernetes-introduction.md) vereist |
| Ondersteunde Kubernetes-distributies | [Azure Kubernetes-service op Azure Stack HCI](/azure-stack/aks-hci/overview)<br>[Kubernetes](https://kubernetes.io/docs/home/)<br> [AKS-engine](https://github.com/Azure/aks-engine)<br> [Red Hat open Shift](https://www.openshift.com/learn/topics/kubernetes/) (versie 4,6 of nieuwer) |
| Beperkingen | Azure Arc enabled Kubernetes en de Azure Defender-extensie **bieden geen ondersteuning** voor beheerde Kubernetes-aanbiedingen zoals Google Kubernetes engine en Elastic Kubernetes service. [Azure Defender is standaard beschikbaar voor Azure Kubernetes service (AKS)](defender-for-kubernetes-introduction.md) en vereist geen verbinding met het cluster met Azure Arc. |
| Omgevingen en regio's | De beschik baarheid voor deze uitbrei ding is hetzelfde als [Azure Arc enabled Kubernetes](../azure-arc/kubernetes/overview.md)|

## <a name="architecture-overview"></a>Overzicht van de architectuur

Voor alle andere Kubernetes-clusters dan AKS moet u uw cluster verbinden met Azure Arc. Nadat de verbinding tot stand is gebracht, kunnen Azure Defender voor Kubernetes worden geïmplementeerd op [Azure-Kubernetes](../azure-arc/kubernetes/overview.md) -resources als [cluster uitbreiding](../azure-arc/kubernetes/extensions.md).

De extensie onderdelen verzamelen Kubernetes audit logboek gegevens van alle knoop punten van het besturings element in het cluster en verzenden deze naar de back-end van Azure Defender voor Kubernetes in de Cloud voor verdere analyse. De extensie wordt geregistreerd bij een Log Analytics werk ruimte die wordt gebruikt als gegevens pijplijn, maar de controle logboek gegevens worden niet opgeslagen in de werk ruimte Log Analytics.

Dit diagram toont de interactie tussen Azure Defender voor Kubernetes en het Azure Arc enabled Kubernetes-cluster:

:::image type="content" source="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png" alt-text="Een architectuur diagram op hoog niveau met een overzicht van de interactie tussen Azure Defender voor Kubernetes en een Azure Arc ingeschakelde Kubernetes-clusters." lightbox="media/defender-for-kubernetes-azure-arc/defender-for-kubernetes-architecture-overview.png":::

## <a name="prerequisites"></a>Vereisten

- Azure Defender voor Kubernetes is [ingeschakeld voor uw abonnement](enable-azure-defender.md)
- Uw Kubernetes-cluster is [verbonden met Azure Arc](../azure-arc/kubernetes/quickstart-connect-cluster.md)
- U hebt voldaan aan de vereisten die worden vermeld in de [documentatie over algemene cluster uitbreidingen](../azure-arc/kubernetes/extensions.md#prerequisites).

## <a name="deploy-the-azure-defender-extension"></a>De Azure Defender-extensie implementeren

U kunt de Azure Defender-extensie implementeren met behulp van verschillende methoden. Selecteer het relevante tabblad voor gedetailleerde stappen.

### <a name="azure-portal"></a>[**Azure Portal**](#tab/k8s-deploy-asc)

### <a name="use-the-quick-fix-option-from-the-security-center-recommendation"></a>Gebruik de optie ' snel oplossen ' uit de Security Center aanbeveling

Een speciale aanbeveling in Azure Security Center biedt het volgende:

- **Zicht baarheid** van de uitbrei ding Defender for Kubernetes voor uw clusters
- **Een ' snelle oplossing ' optie** om deze te implementeren op die clusters zonder de extensie

1. Open op de pagina aanbevelingen van Azure Security Center het **Azure Defender** -beveiligings beheer inschakelen.

1. Gebruik het filter om de aanbeveling te vinden met de naam Azure **Kubernetes-clusters**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="De aanbeveling van Azure Security Center voor het implementeren van de Azure Defender-extensie voor Azure Arc enabled Kubernetes-clusters." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

    > [!TIP]
    > Let op het pictogram snel herstellen in de kolom acties

1. Selecteer de uitbrei ding om de details van de gezonde en beschadigde resources te bekijken: clusters met en zonder de extensie.

1. Selecteer een cluster in de lijst met beschadigde resources en selecteer **herstellen** om het deel venster met de opties voor herstel te openen.

1. Selecteer de relevante Log Analytics-werk ruimte en selecteer **x-resource herstellen**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/security-center-deploy-extension.gif" alt-text="Implementeer de Azure Defender-extensie voor Azure Arc met de optie voor snel oplossen van Security Center.":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-deploy-cli)

### <a name="use-azure-cli-to-deploy-the-azure-defender-extension"></a>Azure CLI gebruiken voor het implementeren van de Azure Defender-extensie

1. Meld u aan bij Azure:

    ```azurecli
    az login
    az account set --subscription <your-subscription-id>
    ```

    > [!IMPORTANT]
    > Zorg ervoor dat u dezelfde abonnements-ID gebruikt ``<your-subscription-id>`` als voor de toepassing die is gebruikt bij het verbinden van het cluster met Azure Arc.

1. Voer de volgende opdracht uit om de extensie boven op uw Azure Arc-Kubernetes-cluster te implementeren:

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <cluster-name> --resource-group <resource-group> --extension-type microsoft.azuredefender.kubernetes
    ```

    Hieronder vindt u een beschrijving van alle ondersteunde configuratie-instellingen voor het extensie type van Azure Defender:

    | Eigenschap | Beschrijving |
    |----------|-------------|
    | logAnalyticsWorkspaceResourceID | **Optioneel**. Volledige Resource-ID van uw eigen Log Analytics-werk ruimte.<br>Als u dit niet opgeeft, wordt de standaardwerk ruimte van de regio gebruikt.<br><br>Als u de volledige Resource-ID wilt ophalen, voert u de volgende opdracht uit om de lijst met werk ruimten in uw abonnementen weer te geven in de standaard JSON-indeling:<br>```az resource list --resource-type Microsoft.OperationalInsights/workspaces -o json```<br><br>De resource-ID van de Log Analytics werkruimte heeft de volgende syntaxis:<br>/subscriptions/{your-subscription-id}/resourceGroups/{your-resource-group}/providers/Microsoft.OperationalInsights/workspaces/{your-workspace-name}. <br>Meer informatie in [log Analytics-werk ruimten](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces) |
    | auditLogPath |**Optioneel**. Het volledige pad naar de controle logboek bestanden.<br>Als u niets opgeeft, wordt het standaardpad ``/var/log/kube-apiserver/audit.log`` gebruikt.<br>Voor de AKS-engine is het standaardpad ``/var/log/kubeaudit/audit.log`` |

    De onderstaande opdracht toont een voor beeld van het gebruik van alle optionele velden:

    ```azurecli
    az k8s-extension create --name microsoft.azuredefender.kubernetes --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --extension-type microsoft.azuredefender.kubernetes --configuration-settings logAnalyticsWorkspaceResourceID=<log-analytics-workspace-resource-id> auditLogPath=<your-auditlog-path>
    ```

### <a name="resource-manager"></a>[**Resource Manager**](#tab/k8s-deploy-resource-manager)

### <a name="use-azure-resource-manager-to-deploy-the-azure-defender-extension"></a>Azure Resource Manager gebruiken om de Azure Defender-extensie te implementeren

Als u Azure Resource Manager wilt gebruiken voor het implementeren van de Azure Defender-extensie, hebt u een Log Analytics-werk ruimte nodig voor uw abonnement. Meer informatie vindt u in [log Analytics-werk ruimten](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces).

U kunt de **azure-defender-extension-arm-template.jsvoor** de Resource Manager-sjabloon gebruiken vanuit de [installatie voorbeelden](https://aka.ms/kubernetes-extension-installation-examples)van Security Center.

> [!TIP]
> Als u geen ervaring hebt met Resource Manager-sjablonen, kunt u hier beginnen: [Wat zijn Azure Resource Manager sjablonen?](../azure-resource-manager/templates/overview.md)

### <a name="rest-api"></a>[**REST API**](#tab/k8s-deploy-api)

### <a name="use-rest-api-to-deploy-the-azure-defender-extension"></a>REST API gebruiken om de Azure Defender-extensie te implementeren 

Als u de REST API wilt gebruiken voor het implementeren van de Azure Defender-extensie, hebt u een Log Analytics-werk ruimte nodig voor uw abonnement. Meer informatie vindt u in [log Analytics-werk ruimten](../azure-monitor/logs/data-platform-logs.md#log-analytics-workspaces).

> [!TIP]
> De eenvoudigste manier om de API te gebruiken voor het implementeren van de Azure Defender-extensie is met het opgegeven JSON-voor beeld van de **postman-verzameling** in de [voor beelden](https://aka.ms/kubernetes-extension-installation-examples)van de installatie van Security Center.
- Als u de JSON van de Postman-verzameling wilt wijzigen of de extensie hand matig wilt implementeren met de REST API, voert u de volgende PUT-opdracht uit:

    ```rest
    PUT https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

    Waar:

    | Name            | In   | Vereist | Type   | Description                                  |
    |-----------------|------|----------|--------|----------------------------------------------|
    | Abonnements-id | leertraject | True     | tekenreeks | De abonnements-ID van uw Azure Arc ingeschakelde Kubernetes-resource |
    | Resourcegroep  | leertraject | True     | tekenreeks | De naam van de resource groep met uw Azure-Kubernetes-resource |
    | Clusternaam    | leertraject | True     | tekenreeks | Naam van uw Kubernetes-resource voor Azure-Arc  |


    Voor **verificatie** moet uw header een Bearer-token hebben (net als bij andere Azure-api's). Voer de volgende opdracht uit om een Bearer-token te verkrijgen:

    ```az account get-access-token --subscription <your-subscription-id>``` Gebruik de volgende structuur voor de hoofd tekst van het bericht:
    ```json
    { 
    "properties": { 
        "extensionType": "microsoft.azuredefender.kubernetes",
    "con    figurationSettings": { 
            "logAnalytics.workspaceId":"YOUR-WORKSPACE-ID"
        // ,    "auditLogPath":"PATH/TO/AUDITLOG"
        },
        "configurationProtectedSettings": {
            "logAnalytics.key":"YOUR-WORKSPACE-KEY"
        }
        }
    } 
    ```

    Hieronder vindt u een beschrijving van de eigenschappen:

    | Eigenschap | Beschrijving | 
    | -------- | ----------- |
    | logAnalytics. workspaceId | Werk ruimte-ID van de Log Analytics resource |
    | logAnalytics. key         | Sleutel van de Log Analytics resource | 
    | auditLogPath             | **Optioneel**. Het volledige pad naar de controle logboek bestanden. De standaardwaarde is ``/var/log/kube-apiserver/audit.log`` |

---

## <a name="verify-the-deployment"></a>De implementatie controleren

Als u wilt controleren of de Azure Defender-extensie op uw cluster is geïnstalleerd, volgt u de stappen in een van de onderstaande tabbladen:

### <a name="azure-portal---security-center"></a>[**Azure Portal-Security Center**](#tab/k8s-verify-asc)

### <a name="use-security-center-recommendation-to-verify-the-status-of-your-extension"></a>Gebruik Security Center aanbeveling om de status van uw uitbrei ding te controleren

1. Open op de pagina aanbevelingen van Azure Security Center het  **Azure Defender** -beveiligings beheer inschakelen.

1. Selecteer de aanbeveling met de naam Azure **Arc enabled Kubernetes-clusters moet de uitbrei ding van Azure Defender hebben geïnstalleerd**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-recommendation.png" alt-text="De aanbeveling van Azure Security Center voor het implementeren van de Azure Defender-extensie voor Azure Arc enabled Kubernetes-clusters." lightbox="media/defender-for-kubernetes-azure-arc/extension-recommendation.png":::

1. Controleer of het cluster waarop u de extensie hebt geïmplementeerd, wordt weer gegeven als **in orde**.


### <a name="azure-portal---azure-arc"></a>[**Azure Portal-Azure-boog**](#tab/k8s-verify-arc)

### <a name="use-the-azure-arc-pages-to-verify-the-status-of-your-extension"></a>Gebruik de pagina's van Azure Arc om de status van uw uitbrei ding te controleren

1. Open **Azure Arc** vanuit het Azure Portal.
1. Selecteer in de lijst met infra structuur **Kubernetes-clusters** en selecteer vervolgens het specifieke cluster.
1. Open de pagina extensies. De uitbrei dingen op het cluster worden weer gegeven. Controleer de kolom **installatie status** om te controleren of de Azure Defender-extensie juist is geïnstalleerd.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png" alt-text="De pagina van Azure Arc voor het controleren van de status van alle geïnstalleerde uitbrei dingen in een Kubernetes-cluster." lightbox="media/defender-for-kubernetes-azure-arc/extension-installed-clusters-page.png":::

1. Selecteer de uitbrei ding voor meer informatie.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-details-page.png" alt-text="Volledige details van een Azure Arc-extensie op een Kubernetes-cluster.":::


### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-verify-cli)

### <a name="use-azure-cli-to-verify-that-the-extension-is-deployed"></a>Azure CLI gebruiken om te controleren of de extensie is geïmplementeerd

1. Voer de volgende opdracht uit op Azure CLI:

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

1. Zoek in het antwoord naar "extension type": "micro soft. azuredefender. kubernetes" en "installState": "Installed".

    > [!NOTE]
    > Mogelijk wordt ' installState ': ' in behandeling ' weer gegeven gedurende de eerste paar minuten.
    
1. Als de status wordt **weer gegeven**, voert u de volgende opdracht op uw computer uit met het `kubeconfig` bestand dat naar uw cluster is gewijsd om te controleren of een pod met de naam ' azuredefender-xxxxx ' de status actief heeft:
    
    ```console
    kubectl get pods -n azuredefender
    ```

### <a name="rest-api"></a>[**REST API**](#tab/k8s-verify-api)

### <a name="use-the-rest-api-to-verify-that-the-extension-is-deployed"></a>De REST API gebruiken om te controleren of de extensie is geïmplementeerd

Om een geslaagde implementatie te bevestigen of om de status van uw uitbrei ding op elk gewenst moment te valideren:

1. Voer de volgende GET-opdracht uit:

    ```rest
    GET https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
    ```

1. Zoek in het antwoord naar "extension type": "micro soft. azuredefender. kubernetes" voor "installState": "Installed".

    > [!TIP]
    > Mogelijk wordt ' installState ': ' in behandeling ' weer gegeven gedurende de eerste paar minuten.
    
1. Als de status wordt **weer gegeven**, voert u de volgende opdracht op uw computer uit met het `kubeconfig` bestand dat naar uw cluster is gewijsd om te controleren of een pod met de naam ' azuredefender-xxxxx ' de status actief heeft:
    
    ```console
    kubectl get pods -n azuredefender
    ```
---

## <a name="simulate-security-alerts-from-azure-defender-for-kubernetes"></a>Beveiligings waarschuwingen van Azure Defender simuleren voor Kubernetes

Een volledige lijst met ondersteunde waarschuwingen is beschikbaar in de [referentie tabel van alle beveiligings waarschuwingen in azure Security Center](alerts-reference.md#alerts-akscluster).

1. Als u een Azure Defender-waarschuwing wilt simuleren, voert u de volgende opdracht uit:

    ```console
    kubectl get pods --namespace=asc-alerttest-662jfi039n
    ```

    De verwachte reactie is geen bron gevonden.

    Binnen 30 minuten detecteert Azure Defender deze activiteit en wordt een beveiligings waarschuwing geactiveerd.

1. Open in de Azure Portal de pagina beveiligings waarschuwingen van Azure Security Center en zoek de waarschuwing op de relevante resource:

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png" alt-text="Voorbeeld waarschuwing van Azure Defender voor Kubernetes." lightbox="media/defender-for-kubernetes-azure-arc/sample-kubernetes-security-alert.png":::

## <a name="removing-the-azure-defender-extension"></a>De Azure Defender-extensie verwijderen

U kunt de extensie verwijderen met Azure Portal, Azure CLI of REST API, zoals wordt uitgelegd in de onderstaande tabbladen.

### <a name="azure-portal---arc"></a>[**Azure Portal boog**](#tab/k8s-remove-arc)

### <a name="use-azure-portal-to-remove-the-extension"></a>Azure Portal gebruiken om de extensie te verwijderen

1. Open Azure Arc vanuit het Azure Portal.
1. Selecteer in de lijst met infra structuur **Kubernetes-clusters** en selecteer vervolgens het specifieke cluster.
1. Open de pagina extensies. De uitbrei dingen op het cluster worden weer gegeven.
1. Selecteer het cluster en selecteer **verwijderen**.

    :::image type="content" source="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png" alt-text="Een uitbrei ding verwijderen uit het Kubernetes-cluster met Arc-functionaliteit." lightbox="media/defender-for-kubernetes-azure-arc/extension-uninstall-clusters-page.png":::

### <a name="azure-cli"></a>[**Azure CLI**](#tab/k8s-remove-cli)

### <a name="use-azure-cli-to-remove-the-azure-defender-extension"></a>Azure CLI gebruiken om de Azure Defender-extensie te verwijderen

1. Verwijder de uitbrei ding Azure Defender voor Kubernetes Arc met de volgende opdrachten:

    ```azurecli
    az login
    az account set --subscription <subscription-id>
    az k8s-extension delete --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes --yes
    ```

    Het kan enkele minuten duren om de extensie te verwijderen. U wordt aangeraden te wachten voordat u probeert te controleren of het is gelukt.

1. Voer de volgende opdrachten uit om te controleren of de extensie is verwijderd:

    ```azurecli
    az k8s-extension show --cluster-type connectedClusters --cluster-name <your-connected-cluster-name> --resource-group <your-rg> --name microsoft.azuredefender.kubernetes
    ```

    Er mag geen vertraging optreden in de extensie resource die wordt verwijderd uit Azure Resource Manager. Als dat het geval is, controleert u of er op het cluster geen peul wordt genoemd met de naam ' azuredefender-XXXXX ' door de volgende opdracht uit te voeren met het `kubeconfig` bestand dat naar uw cluster is gewijsd: 

    ```console
    kubectl get pods -n azuredefender
    ```

    Het kan enkele minuten duren voordat het peul is verwijderd.

### <a name="rest-api"></a>[**REST API**](#tab/k8s-remove-api)

### <a name="use-rest-api-to-remove-the-azure-defender-extension"></a>REST API gebruiken om de extensie van Azure Defender te verwijderen 

Als u de extensie wilt verwijderen met de REST API, voert u de volgende opdracht verwijderen uit:

```rest
DELETE https://management.azure.com/subscriptions/{{Subscription Id}}/resourcegroups/{{Resource Group}}/providers/Microsoft.Kubernetes/connectedClusters/{{Cluster Name}}/providers/Microsoft.KubernetesConfiguration/extensions/microsoft.azuredefender.kubernetes?api-version=2020-07-01-preview
```

| Name            | In   | Vereist | Type   | Description                                           |
|-----------------|------|----------|--------|-------------------------------------------------------|
| Abonnements-id | leertraject | True     | tekenreeks | De abonnements-ID van uw Arc ingeschakelde Kubernetes-cluster |
| Resourcegroep  | leertraject | True     | tekenreeks | De resource groep van uw Arc-Kubernetes-cluster  |
| Clusternaam    | leertraject | True     | tekenreeks | De naam van uw Arc Kubernetes-cluster            |

Voor **verificatie** moet uw header een Bearer-token hebben (net als bij andere Azure-api's). Voer de volgende opdracht uit om een Bearer-token te verkrijgen:

```azurecli
az account get-access-token --subscription <your-subscription-id>
```

Het kan enkele minuten duren voordat de aanvraag is voltooid.

---

## <a name="next-steps"></a>Volgende stappen

Op deze pagina wordt uitgelegd hoe u de Azure Defender-extensie voor Azure Arc enabled Kubernetes-clusters implementeert. Meer informatie over de beveiligings functies van de container Azure Defender en Azure Security Center op de volgende pagina's:

- [Containerbeveiliging in Security Center](container-security.md)
- [Inleiding tot Azure Defender for Kubernetes](defender-for-kubernetes-introduction.md)
- [Kubernetes-workloads beveiligen](kubernetes-workload-protections.md)
