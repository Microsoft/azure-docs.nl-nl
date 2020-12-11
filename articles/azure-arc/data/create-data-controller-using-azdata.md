---
title: Een gegevens controller maken met behulp van Azure data CLI (azdata)
description: Maak een Azure-Arc-gegevens controller op een typisch Kubernetes-cluster met meerdere knoop punten dat u al hebt gemaakt met behulp van de Azure data CLI (azdata).
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: f00cd1ec9c2900998596df3baded562059012658
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107295"
---
# <a name="create-azure-arc-data-controller-using-the-azure-data-cli-azdata"></a>Een Azure-Arc-gegevens controller maken met behulp van de [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Vereisten

Raadpleeg het onderwerp [de Azure Arc-gegevens controller maken](create-data-controller.md) voor overzichts informatie.

Als u de Azure-Arc-gegevens controller wilt maken met behulp van [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] , moet u beschikken over de [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] installatie.

   [Installeer de [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]](install-client-tools.md)

Ongeacht het doel platform dat u kiest, moet u de volgende omgevings variabelen instellen voordat u de gebruiker van de gegevens controller beheerder maakt. U kunt deze referenties opgeven voor andere personen die beheerders toegang nodig hebben tot de gegevens controller.

**AZDATA_USERNAME** : een gebruikers naam van uw keuze voor de gebruiker van de gegevens controller beheerder. Voorbeeld: `arcadmin`

**AZDATA_PASSWORD** : een wacht woord van uw keuze voor de gebruiker van de gegevens controller beheerder. Het wacht woord moet ten minste acht tekens lang zijn en tekens bevatten uit drie van de volgende vier sets: hoofd letters, kleine letters, cijfers en symbolen.

### <a name="linux-or-macos"></a>Linux of macOS

```console
export AZDATA_USERNAME="<your username of choice>"
export AZDATA_PASSWORD="<your password of choice>"
```

### <a name="windows-powershell"></a>Windows PowerShell

```console
$ENV:AZDATA_USERNAME="<your username of choice>"
$ENV:AZDATA_PASSWORD="<your password of choice>"
```

U moet verbinding maken met en verifiëren met een Kubernetes-cluster en een bestaande Kubernetes-context selecteren voordat u begint met het maken van de Azure Arc-gegevens controller. Hoe u verbinding maakt met een Kubernetes-cluster of-service, varieert. Raadpleeg de documentatie voor de Kubernetes-distributie of-service die u gebruikt om verbinding te maken met de Kubernetes-API-server.

U kunt controleren of u een huidige Kubernetes-verbinding hebt en uw huidige context bevestigen met de volgende opdrachten.

```console
kubectl get namespace
kubectl config current-context
```

### <a name="connectivity-modes"></a>Connectiviteitsmodi

Zoals beschreven in [connectiviteits modi en vereisten](https://docs.microsoft.com/azure/azure-arc/data/connectivity), kan de Azure Arc-gegevens controller worden geïmplementeerd met een `direct` of meer `indirect` connectiviteits modus. Met de `direct` connectiviteits modus worden gebruiks gegevens automatisch en continu naar Azure verzonden. In deze artikelen geeft de voor beelden de `direct` connectiviteits modus als volgt aan:

   ```console
   --connectivity-mode direct
   ```

   Als u de controller met `indirect` de connectiviteits modus wilt maken, werkt u de scripts in het voor beeld zoals hieronder opgegeven:

   ```console
   --connectivity-mode indirect
   ```

#### <a name="create-service-principal"></a>Een service-principal maken

Als u de Azure-Arc-gegevens controller implementeert met `direct` de connectiviteits modus, zijn de referenties van de Service-Principal vereist voor de Azure-verbinding. De service-principal wordt gebruikt voor het uploaden van gebruiks-en metrische gegevens. 

Voer deze opdrachten uit om de service-principal voor metrische gegevens te maken:

> [!NOTE]
> Voor het maken van een Service-Principal zijn [bepaalde machtigingen vereist in azure](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Als u een Service-Principal wilt maken, moet u het volgende voor beeld bijwerken. Vervang door `<ServicePrincipalName>` de naam van de Service-Principal en voer de volgende opdracht uit:

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName>
``` 

Als u de Service-Principal eerder hebt gemaakt en u de huidige referenties alleen wilt ophalen, voert u de volgende opdracht uit om de referentie opnieuw in te stellen.

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

Als u bijvoorbeeld een service-principal met de naam wilt maken `azure-arc-metrics` , voert u de volgende opdracht uit:

```console
az ad sp create-for-rbac --name azure-arc-metrics
```

Voorbeelduitvoer:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Sla de `appId` `password` waarden, en `tenant` op in een omgevings variabele voor later gebruik. 

#### <a name="save-environment-variables-in-windows"></a>Omgevings variabelen opslaan in Windows

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
```

#### <a name="save-environment-variables-in-linux-or-macos"></a>Omgevings variabelen opslaan in Linux of macOS

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
```

#### <a name="save-environment-variables-in-powershell"></a>Omgevings variabelen opslaan in Power shell

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
```

Nadat u de Service-Principal hebt gemaakt, wijst u de Service-Principal toe aan de juiste rol. 

### <a name="assign-roles-to-the-service-principal"></a>Rollen toewijzen aan de Service-Principal

Voer deze opdracht uit om de Service-Principal toe te wijzen aan de `Monitoring Metrics Publisher` rol van het abonnement waarin de resources van het data base-exemplaar zich bevinden:

#### <a name="run-the-command-on-windows"></a>Voer de opdracht uit in Windows

> [!NOTE]
> U moet dubbele aanhalings tekens voor rolnaams gebruiken bij het uitvoeren vanuit een Windows-omgeving.

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role "Contributor" --scope subscriptions/<Subscription ID>
```

#### <a name="run-the-command-on-linux-or-macos"></a>De opdracht uitvoeren op Linux of macOS

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

#### <a name="run-the-command-in-powershell"></a>Voer de opdracht uit in Power shell

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<Subscription ID>
az role assignment create --assignee <appId> --role 'Contributor' --scope subscriptions/<Subscription ID>
```

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

Wanneer de Service-Principal is toegewezen aan de juiste rol en de omgevings variabelen die zijn ingesteld, kunt u door gaan met het maken van de gegevens controller 

## <a name="create-the-azure-arc-data-controller"></a>De Azure Arc-gegevens controller maken

> [!NOTE]
> U kunt een andere waarde voor de `--namespace` para meter van de opdracht azdata Arc DC Create gebruiken in de onderstaande voor beelden, maar zorg ervoor dat u deze naam ruimte gebruikt voor de `--namespace parameter` in alle andere opdrachten hieronder.

- [Maken in azure Kubernetes service (AKS)](#create-on-azure-kubernetes-service-aks)
- [Op de AKS-Engine maken op Azure Stack hub](#create-on-aks-engine-on-azure-stack-hub)
- [Maken op AKS in Azure Stack HCI](#create-on-aks-on-azure-stack-hci)
- [Maken op Azure Red Hat open Shift (ARO)](#create-on-azure-red-hat-openshift-aro)
- [Maken op Red Hat open Shift container platform (OCP)](#create-on-red-hat-openshift-container-platform-ocp)
- [Maken op open source, upstream-Kubernetes (kubeadm)](#create-on-open-source-upstream-kubernetes-kubeadm)
- [Maken op AWS Elastic Kubernetes service (EKS)](#create-on-aws-elastic-kubernetes-service-eks)
- [Maken op Google Cloud Kubernetes Engine-service (GKE)](#create-on-google-cloud-kubernetes-engine-service-gke)

### <a name="create-on-azure-kubernetes-service-aks"></a>Maken in azure Kubernetes service (AKS)

Het AKS-implementatie profiel maakt standaard gebruik van de `managed-premium` klasse Storage. De `managed-premium` opslag klasse werkt alleen als u virtuele machines hebt die zijn geïmplementeerd met behulp van VM-installatie kopieën die Premium-schijven hebben.

Als u `managed-premium` als uw opslag klasse gaat gebruiken, kunt u de volgende opdracht uitvoeren om de gegevens controller te maken. Vervang de tijdelijke aanduidingen in de opdracht door de naam van uw resource groep, abonnements-ID en Azure-locatie.

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Als u niet zeker weet welke opslag klasse u moet gebruiken, dient u de `default` opslag klasse te gebruiken die wordt ondersteund, ongeacht welk VM-type u gebruikt. Dit biedt alleen de snelste prestaties.

Als u de `default` opslag klasse wilt gebruiken, kunt u deze opdracht uitvoeren:

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Wanneer u de opdracht hebt uitgevoerd, gaat u door met om [de aanmaak status te bewaken](#monitoring-the-creation-status).

### <a name="create-on-aks-engine-on-azure-stack-hub"></a>Op de AKS-Engine maken op Azure Stack hub

Het implementatie profiel gebruikt standaard de `managed-premium` klasse Storage. De `managed-premium` opslag klasse werkt alleen als u werk-vm's hebt die zijn geïmplementeerd met behulp van VM-installatie kopieën die Premium-schijven hebben op Azure stack hub.

U kunt de volgende opdracht uitvoeren om de gegevens controller te maken met behulp van de beheerde-Premium-opslag klasse:

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Als u niet zeker weet welke opslag klasse u moet gebruiken, dient u de `default` opslag klasse te gebruiken die wordt ondersteund, ongeacht welk VM-type u gebruikt. In Azure Stack hub worden Premium-schijven en standaard schijven ondersteund door dezelfde opslag infrastructuur. Daarom worden ze naar verwachting dezelfde algemene prestaties leveren, maar met verschillende IOPS-limieten.

Als u de `default` opslag klasse wilt gebruiken, kunt u deze opdracht uitvoeren.

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Wanneer u de opdracht hebt uitgevoerd, gaat u door met om [de aanmaak status te bewaken](#monitoring-the-creation-status).

### <a name="create-on-aks-on-azure-stack-hci"></a>Maken op AKS in Azure Stack HCI

Het implementatie profiel gebruikt standaard een opslag klasse met de naam `default` en het Service type `LoadBalancer` .

U kunt de volgende opdracht uitvoeren om de gegevens controller te maken met behulp van de `default` opslag klasse en het Service type `LoadBalancer` .

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Wanneer u de opdracht hebt uitgevoerd, gaat u door met om [de aanmaak status te bewaken](#monitoring-the-creation-status).


### <a name="create-on-azure-red-hat-openshift-aro"></a>Maken op Azure Red Hat open Shift (ARO)

#### <a name="apply-the-scc"></a>Het SCC Toep assen

Voordat u de gegevens controller op Azure Red Hat open SHIFT maakt, moet u specifieke beveiligings context beperkingen (SCC) Toep assen. Voor de preview-versie versoepelt deze de beveiligings beperkingen. Toekomstige releases bieden bijgewerkte SCC.

1. Down load de aangepaste beveiligings context beperking (SCC). Gebruik een van de volgende opties: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/master/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - ([Onbewerkt](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/arc-data-scc.yaml))
   - `curl` Met de volgende opdracht worden Arc-data-SCC. yaml gedownload:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. Maak SCC.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Pas het SCC toe op het service account.

   > [!NOTE]
   > Gebruik hier dezelfde naam ruimte en in de `azdata arc dc create` onderstaande opdracht. Voor beeld is `arc` .

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```


#### <a name="create-custom-deployment-profile"></a>Aangepast implementatie profiel maken

Gebruik het profiel `azure-arc-azure-openshift` voor Azure Red Hat open SHIFT.

```console
azdata arc dc config init --source azure-arc-azure-openshift --path ./custom
```

#### <a name="create-data-controller"></a>Een gegevenscontroller maken

U kunt de volgende opdracht uitvoeren om de gegevens controller te maken:

> [!NOTE]
> Gebruik hier dezelfde naam ruimte en in de `oc adm policy add-scc-to-user` bovenstaande opdrachten. Voor beeld is `arc` .

```console
azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example
#azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Wanneer u de opdracht hebt uitgevoerd, gaat u door met om [de aanmaak status te bewaken](#monitoring-the-creation-status).

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Maken op Red Hat open Shift container platform (OCP)

> [!NOTE]
> Als u het Red Hat open Shift container platform gebruikt in azure, is het raadzaam om de meest recente beschik bare versie te gebruiken.

#### <a name="apply-the-scc"></a>Het SCC Toep assen

Voordat u de gegevens controller op Red Hat OCP maakt, moet u specifieke beveiligings context beperkingen (SCC) Toep assen. Voor de preview-versie versoepelt deze de beveiligings beperkingen. Toekomstige releases bieden bijgewerkte SCC.

1. Down load de aangepaste beveiligings context beperking (SCC). Gebruik een van de volgende opties: 
   - [GitHub](https://github.com/microsoft/azure_arc/tree/master/arc_data_services/deploy/yaml/arc-data-scc.yaml) 
   - ([Onbewerkt](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/arc-data-scc.yaml))
   - `curl` Met de volgende opdracht worden Arc-data-SCC. yaml gedownload:

      ```console
      curl https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/arc-data-scc.yaml -o arc-data-scc.yaml
      ```

1. Maak SCC.

   ```console
   oc create -f arc-data-scc.yaml
   ```

1. Pas het SCC toe op het service account.

   > [!NOTE]
   > Gebruik hier dezelfde naam ruimte en in de `azdata arc dc create` onderstaande opdracht. Voor beeld is `arc` .

   ```console
   oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
   ```

#### <a name="determine-storage-class"></a>Opslag klasse bepalen

U moet ook bepalen welke opslag klasse moet worden gebruikt door de volgende opdracht uit te voeren.

```console
kubectl get storageclass
```

#### <a name="create-custom-deployment-profile"></a>Aangepast implementatie profiel maken

Maak een nieuw bestand met een aangepast implementatie profiel op basis van het `azure-arc-openshift` implementatie profiel door de volgende opdracht uit te voeren. Met deze opdracht maakt u een map `custom` in uw huidige werkmap en een aangepast implementatie profiel bestand `control.json` in die map.

Gebruik het profiel `azure-arc-openshift` voor open Shift container platform.

```console
azdata arc dc config init --source azure-arc-openshift --path ./custom
```

#### <a name="set-storage-class"></a>Opslag klasse instellen 

Stel nu de gewenste opslag klasse in door te vervangen `<storageclassname>` in de onderstaande opdracht met de naam van de opslag klasse die u wilt gebruiken die is bepaald door de bovenstaande opdracht uit te voeren `kubectl get storageclass` .

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

#### <a name="set-loadbalancer-optional"></a>LoadBalancer instellen (optioneel)

Het `azure-arc-openshift` implementatie profiel wordt standaard gebruikt `NodePort` als het Service type. Als u een open Shift-cluster gebruikt dat is geïntegreerd met een load balancer, kunt u de configuratie wijzigen in het gebruik `LoadBalancer` van het Service type met behulp van de volgende opdracht:

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

#### <a name="verify-security-policies"></a>Beveiligings beleid verifiëren

Wanneer u open SHIFT gebruikt, wilt u mogelijk uitvoeren met het standaard beveiligings beleid in open Shift of wilt u de omgeving doorgaans meer dan normaal vergren delen. U kunt ervoor kiezen om sommige functies uit te scha kelen om de vereiste machtigingen voor implementatie tijd en tijdens de uitvoering te minimaliseren door de volgende opdrachten uit te voeren.

Met deze opdracht worden metrische gegevens verzamelingen over peul uitgeschakeld. Als u deze functie uitschakelt, kunt u de metrische gegevens over het Grafana-dash board niet meer zien. De standaardwaarde is true.

```console
azdata arc dc config replace -p ./custom/control.json --json-values spec.security.allowPodMetricsCollection=false
```

Met deze opdracht worden metrische verzamelingen van knoop punten uitgeschakeld. Als u deze functie uitschakelt, kunt u geen metrische gegevens weer geven over de knoop punten in de Grafana-Dash boards. De standaardwaarde is true.

```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowNodeMetricsCollection=false
```

Met deze opdracht wordt de mogelijkheid om geheugen dumps te nemen voor het oplossen van problemen, uitgeschakeld.
```console
azdata arc dc config replace --path ./custom/control.json --json-values spec.security.allowDumps=false
```

#### <a name="create-data-controller"></a>Een gegevenscontroller maken

U kunt nu de gegevens controller maken met behulp van de volgende opdracht.

> [!NOTE]
>   Gebruik hier dezelfde naam ruimte en in de `oc adm policy add-scc-to-user` bovenstaande opdrachten. Voor beeld is `arc` .

> [!NOTE]
>   De `--path` para meter moet verwijzen naar de _map_ met de control.jsop bestand, niet naar de control.jsin het bestand zelf.


```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Wanneer u de opdracht hebt uitgevoerd, gaat u door met om [de aanmaak status te bewaken](#monitoring-the-creation-status).

### <a name="create-on-open-source-upstream-kubernetes-kubeadm"></a>Maken op open source, upstream-Kubernetes (kubeadm)

Het kubeadm-implementatie profiel maakt standaard gebruik van een opslag klasse met de naam `local-storage` en het Service type `NodePort` . Als dit acceptabel is, kunt u de onderstaande instructies voor het instellen van de gewenste opslag klasse en het betreffende service type overs Laan en onmiddellijk de `azdata arc dc create` onderstaande opdracht uitvoeren.

Als u uw implementatie profiel wilt aanpassen om een specifieke opslag klasse en/of een specifiek Service type op te geven, begint u met het maken van een nieuw profiel bestand voor aangepaste implementatie op basis van het kubeadm-implementatie profiel door de volgende opdracht uit te voeren. Met deze opdracht maakt u een map `custom` in uw huidige werkmap en een aangepast implementatie profiel bestand `control.json` in die map.

```console
azdata arc dc config init --source azure-arc-kubeadm --path ./custom
```

U kunt de beschik bare opslag klassen opzoeken door de volgende opdracht uit te voeren.

```console
kubectl get storageclass
```

Stel nu de gewenste opslag klasse in door te vervangen `<storageclassname>` in de onderstaande opdracht met de naam van de opslag klasse die u wilt gebruiken die is bepaald door de bovenstaande opdracht uit te voeren `kubectl get storageclass` .

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

Het kubeadm-implementatie profiel wordt standaard gebruikt `NodePort` als het Service type. Als u een Kubernetes-cluster gebruikt dat is geïntegreerd met een load balancer, kunt u de configuratie wijzigen met behulp van de volgende opdracht.

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

U kunt nu de gegevens controller maken met behulp van de volgende opdracht.

```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Wanneer u de opdracht hebt uitgevoerd, gaat u door met om [de aanmaak status te bewaken](#monitoring-the-creation-status).

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>Maken op AWS Elastic Kubernetes service (EKS)

De opslag klasse EKS is standaard `gp2` en het Service type is `LoadBalancer` .

Voer de volgende opdracht uit om de gegevens controller te maken met behulp van het meegeleverde EKS-implementatie profiel.

```console
azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Wanneer u de opdracht hebt uitgevoerd, gaat u door met om [de aanmaak status te bewaken](#monitoring-the-creation-status).

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>Maken op Google Cloud Kubernetes Engine-service (GKE)

De opslag klasse GKE is standaard `standard` en het Service type is `LoadBalancer` .

Voer de volgende opdracht uit om de gegevens controller te maken met behulp van het meegeleverde GKE-implementatie profiel.

```console
azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode direct

#Example:
#azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode direct
```

Wanneer u de opdracht hebt uitgevoerd, gaat u door met om [de aanmaak status te bewaken](#monitoring-the-creation-status).

## <a name="monitoring-the-creation-status"></a>De aanmaak status bewaken

Het duurt enkele minuten voordat de controller is gemaakt. U kunt de voortgang in een ander Terminal venster bewaken met de volgende opdrachten:

> [!NOTE]
>  In de onderstaande voor beelden wordt ervan uitgegaan dat u een gegevens controller en Kubernetes-naam ruimte hebt gemaakt met de naam `arc` . Als u de naam van een andere naam ruimte/gegevens controller hebt gebruikt, kunt u `arc` deze vervangen door uw naam.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

U kunt ook de status van de aanmaak van een bepaalde pod controleren door een opdracht als hieronder uit te voeren. Dit is met name nuttig voor het oplossen van problemen.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Problemen bij het maken oplossen

Als u problemen ondervindt bij het maken, raadpleegt u de [hand leiding](troubleshoot-guide.md)voor het oplossen van problemen.
