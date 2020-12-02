---
title: Gegevens controller maken met [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]
description: Maak een Azure-Arc-gegevens controller op een typisch Kubernetes-cluster met meerdere knoop punten dat u al hebt gemaakt met behulp van de [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] .
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 94f347cc24c675c69c69dad6a7d7a796b395c1a6
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493608"
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

## <a name="create-the-azure-arc-data-controller"></a>De Azure Arc-gegevens controller maken

> [!NOTE]
> U kunt een andere waarde voor de `--namespace` para meter van de opdracht azdata Arc DC Create gebruiken in de onderstaande voor beelden, maar zorg ervoor dat u deze naam ruimte gebruikt voor de `--namespace parameter` in alle andere opdrachten hieronder.

Volg de toepasselijke sectie hieronder, afhankelijk van uw doel platform, om uw aanmaak te configureren.

[Maken in azure Kubernetes service (AKS)](#create-on-azure-kubernetes-service-aks)

[Op de AKS-Engine maken op Azure Stack hub](#create-on-aks-engine-on-azure-stack-hub)

[Maken op AKS in Azure Stack HCI](#create-on-aks-on-azure-stack-hci)

[Maken op Azure Red Hat open Shift (ARO)](#create-on-azure-red-hat-openshift-aro)

[Maken op Red Hat open Shift container platform (OCP)](#create-on-red-hat-openshift-container-platform-ocp)

[Maken op open source, upstream-Kubernetes (kubeadm)](#create-on-open-source-upstream-kubernetes-kubeadm)

[Maken op AWS Elastic Kubernetes service (EKS)](#create-on-aws-elastic-kubernetes-service-eks)

[Maken op Google Cloud Kubernetes Engine-service (GKE)](#create-on-google-cloud-kubernetes-engine-service-gke)

### <a name="create-on-azure-kubernetes-service-aks"></a>Maken in azure Kubernetes service (AKS)

Het AKS-implementatie profiel maakt standaard gebruik van de `managed-premium` klasse Storage. De `managed-premium` opslag klasse werkt alleen als u virtuele machines hebt die zijn geïmplementeerd met behulp van VM-installatie kopieën die Premium-schijven hebben.

Als u `managed-premium` als uw opslag klasse gaat gebruiken, kunt u de volgende opdracht uitvoeren om de gegevens controller te maken. Vervang de tijdelijke aanduidingen in de opdracht door de naam van uw resource groep, abonnements-ID en Azure-locatie.

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Als u niet zeker weet welke opslag klasse u moet gebruiken, dient u de `default` opslag klasse te gebruiken die wordt ondersteund, ongeacht welk VM-type u gebruikt. Dit biedt alleen de snelste prestaties.

Als u de `default` opslag klasse wilt gebruiken, kunt u deze opdracht uitvoeren:

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Wanneer u de opdracht hebt uitgevoerd, gaat u door met om [de aanmaak status te bewaken](#monitoring-the-creation-status).

### <a name="create-on-aks-engine-on-azure-stack-hub"></a>Op de AKS-Engine maken op Azure Stack hub

Het implementatie profiel gebruikt standaard de `managed-premium` klasse Storage. De `managed-premium` opslag klasse werkt alleen als u werk-vm's hebt die zijn geïmplementeerd met behulp van VM-installatie kopieën die Premium-schijven hebben op Azure stack hub.

U kunt de volgende opdracht uitvoeren om de gegevens controller te maken met behulp van de beheerde-Premium-opslag klasse:

```console
azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Als u niet zeker weet welke opslag klasse u moet gebruiken, dient u de `default` opslag klasse te gebruiken die wordt ondersteund, ongeacht welk VM-type u gebruikt. In Azure Stack hub worden Premium-schijven en standaard schijven ondersteund door dezelfde opslag infrastructuur. Daarom worden ze naar verwachting dezelfde algemene prestaties leveren, maar met verschillende IOPS-limieten.

Als u de `default` opslag klasse wilt gebruiken, kunt u deze opdracht uitvoeren.

```console
azdata arc dc create --profile-name azure-arc-aks-default-storage --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-premium-storage --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Wanneer u de opdracht hebt uitgevoerd, gaat u door met om [de aanmaak status te bewaken](#monitoring-the-creation-status).

### <a name="create-on-aks-on-azure-stack-hci"></a>Maken op AKS in Azure Stack HCI

Het implementatie profiel gebruikt standaard een opslag klasse met de naam `default` en het Service type `LoadBalancer` .

U kunt de volgende opdracht uitvoeren om de gegevens controller te maken met behulp van de `default` opslag klasse en het Service type `LoadBalancer` .

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Wanneer u de opdracht hebt uitgevoerd, gaat u door met om [de aanmaak status te bewaken](#monitoring-the-creation-status).


### <a name="create-on-azure-red-hat-openshift-aro"></a>Maken op Azure Red Hat open Shift (ARO)

Als u een gegevens controller wilt maken in azure Red Hat open Shift, moet u de volgende opdrachten uitvoeren op uw cluster om de beveiligings beperkingen te versoepelen. Dit is een tijdelijke vereiste die in de toekomst zal worden verwijderd.
> [!NOTE]
>   Gebruik hier dezelfde naam ruimte en in de `azdata arc dc create` onderstaande opdracht. Voor beeld is `arc` .

Down load eerst de aangepaste beveiligings context constraint (SCC) van [github](https://github.com/microsoft/azure_arc/tree/master/arc_data_services/deploy/yaml) en pas deze toe op uw cluster.

U kunt de volgende opdracht uitvoeren om de gegevens controller te maken:
> [!NOTE]
>   Gebruik hier dezelfde naam ruimte en in de `oc adm policy add-scc-to-user` bovenstaande opdrachten. Voor beeld is `arc` .

```console
azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example
#azdata arc dc create --profile-name azure-arc-azure-openshift --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Wanneer u de opdracht hebt uitgevoerd, gaat u door met om [de aanmaak status te bewaken](#monitoring-the-creation-status).

### <a name="create-on-red-hat-openshift-container-platform-ocp"></a>Maken op Red Hat open Shift container platform (OCP)


> [!NOTE]
> Als u het Red Hat open Shift container platform gebruikt in azure, is het raadzaam om de meest recente beschik bare versie te gebruiken.

Als u een gegevens controller wilt maken op een Red Hat open Shift container platform, moet u de volgende opdrachten uitvoeren op uw cluster om de beveiligings beperkingen te versoepelen. Dit is een tijdelijke vereiste die in de toekomst zal worden verwijderd.
> [!NOTE]
>   Gebruik hier dezelfde naam ruimte en in de `azdata arc dc create` onderstaande opdracht. Voor beeld is `arc` .

```console
oc adm policy add-scc-to-user arc-data-scc --serviceaccount default --namespace arc
```

U moet ook bepalen welke opslag klasse moet worden gebruikt door de volgende opdracht uit te voeren.

```console
kubectl get storageclass
```

Eerst moet u beginnen met het maken van een nieuw aangepast implementatie profiel bestand op basis van het Azure-Arc-open Shift-implementatie profiel door de volgende opdracht uit te voeren. Met deze opdracht maakt u een map `custom` in uw huidige werkmap en een aangepast implementatie profiel bestand `control.json` in die map.

Gebruik het profiel `azure-arc-openshift` voor open Shift container platform.

```console
azdata arc dc config init --source azure-arc-openshift --path ./custom
```
Gebruik het profiel `azure-arc-azure-openshift` voor Azure Red Hat open SHIFT.

```console
azdata arc dc config init --source azure-arc-azure-openshift --path ./custom
```

Stel nu de gewenste opslag klasse in door te vervangen `<storageclassname>` in de onderstaande opdracht met de naam van de opslag klasse die u wilt gebruiken die is bepaald door de bovenstaande opdracht uit te voeren `kubectl get storageclass` .

```console
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=<storageclassname>"
azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=<storageclassname>"

#Example:
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.data.className=mystorageclass"
#azdata arc dc config replace --path ./custom/control.json --json-values "spec.storage.logs.className=mystorageclass"
```

Het implementatie profiel Azure-Arc-open Shift wordt standaard gebruikt `NodePort` als service type. Als u een open Shift-cluster gebruikt dat is geïntegreerd met een load balancer, kunt u de configuratie wijzigen om het Load Balancer-Service type te gebruiken met behulp van de volgende opdracht:

```console
azdata arc dc config replace --path ./custom/control.json --json-values "$.spec.services[*].serviceType=LoadBalancer"
```

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

U kunt nu de gegevens controller maken met behulp van de volgende opdracht.
> [!NOTE]
>   Gebruik hier dezelfde naam ruimte en in de `oc adm policy add-scc-to-user` bovenstaande opdrachten. Voor beeld is `arc` .

> [!NOTE]
>   De `--path` para meter moet verwijzen naar de _map_ met de control.jsop bestand, niet naar de control.jsin het bestand zelf.


```console
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
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
azdata arc dc create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --path ./custom --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Wanneer u de opdracht hebt uitgevoerd, gaat u door met om [de aanmaak status te bewaken](#monitoring-the-creation-status).

### <a name="create-on-aws-elastic-kubernetes-service-eks"></a>Maken op AWS Elastic Kubernetes service (EKS)

De opslag klasse EKS is standaard `gp2` en het Service type is `LoadBalancer` .

Voer de volgende opdracht uit om de gegevens controller te maken met behulp van het meegeleverde EKS-implementatie profiel.

```console
azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-eks --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
```

Wanneer u de opdracht hebt uitgevoerd, gaat u door met om [de aanmaak status te bewaken](#monitoring-the-creation-status).

### <a name="create-on-google-cloud-kubernetes-engine-service-gke"></a>Maken op Google Cloud Kubernetes Engine-service (GKE)

De opslag klasse GKE is standaard `standard` en het Service type is `LoadBalancer` .

Voer de volgende opdracht uit om de gegevens controller te maken met behulp van het meegeleverde GKE-implementatie profiel.

```console
azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect

#Example:
#azdata arc dc create --profile-name azure-arc-gke --namespace arc --name arc --subscription 1e5ff510-76cf-44cc-9820-82f2d9b51951 --resource-group my-resource-group --location eastus --connectivity-mode indirect
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
