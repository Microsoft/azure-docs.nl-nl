---
title: Azure Arc data controller implementeren | Modus directe verbinding
description: Hierin wordt uitgelegd hoe u de gegevens controller implementeert in de modus directe verbinding.
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 04/06/2021
ms.topic: overview
ms.openlocfilehash: 220618f167237d5937766eb5e28b9b6569cef76a
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031488"
---
#  <a name="deploy-azure-arc-data-controller--direct-connect-mode"></a>Azure Arc data controller implementeren | Modus directe verbinding

In dit artikel wordt beschreven hoe u de Azure Arc-gegevens controller implementeert in de modus Direct Connect tijdens de huidige preview van deze functie. 

Op dit moment kunt u de Azure Arc-gegevens controller maken op basis van Azure Portal. Andere hulpprogram ma's voor Azure Arc enabled Data Services bieden geen ondersteuning voor het maken van de gegevens controller in de modus Direct Connect. Zie [bekende problemen-Azure Arc enabled Data Services (preview)](known-issues.md)voor meer informatie.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="complete-prerequisites"></a>Volledige vereisten

Voordat u begint, controleert u of u de vereisten hebt voltooid in [data controller implementeren-Direct Connect-modus-vereisten](deploy-data-controller-direct-mode-prerequisites.md).

Op hoog niveau wordt in dit artikel uitgelegd hoe u deze taken uitvoert:

1. Maak een Azure Arc enabled Data Services-extensie. 
1. Maak een aangepaste locatie.
1. Implementeer de gegevens controller vanuit de portal.

## <a name="create-an-azure-arc-enabled-data-services-extension"></a>Een extensie voor Azure Arc enabled Data Services maken

Gebruik de K8S-extensie CLI om een Data Services-uitbrei ding te maken.

### <a name="set-environment-variables"></a>Omgevingsvariabelen instellen

Stel de volgende omgevings variabelen in die vervolgens in de volgende stap worden gebruikt.

#### <a name="linux"></a>Linux

``` terminal
# where you want the connected cluster resource to be created in Azure 
export subscription=<Your subscription ID>
export resourceGroup=<Your resource group>
export resourceName=<name of your connected kubernetes cluster>
export location=<Azure location>
```

#### <a name="windows-powershell"></a>Windows PowerShell
``` PowerShell
# where you want the connected cluster resource to be created in Azure 
$ENV:subscription="<Your subscription ID>"
$ENV:resourceGroup="<Your resource group>"
$ENV:resourceName="<name of your connected kubernetes cluster>"
$ENV:location="<Azure location>"
```

### <a name="create-the-arc-data-services-extension"></a>De Arc Data Services-extensie maken

#### <a name="linux"></a>Linux

```bash
az k8s-extension create -c ${resourceName} -g ${resourceGroup} --name ${ADSExtensionName} --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g ${resourceGroup} -c ${resourceName} --name ${ADSExtensionName} --cluster-type connectedclusters
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:ADSExtensionName="ads-extension"

az k8s-extension create -c "$ENV:resourceName" -g "$ENV:resourceGroup" --name "$ENV:ADSExtensionName" --cluster-type connectedClusters --extension-type microsoft.arcdataservices --version "1.0.015564" --auto-upgrade false --scope cluster --release-namespace arc --config Microsoft.CustomLocation.ServiceAccount=sa-bootstrapper

az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --name "$ENV:ADSExtensionName" --cluster-type connectedclusters
```

> [!NOTE]
> Het volt ooien van de installatie van de Arc Data Services-extensie kan enkele minuten duren.

### <a name="verify-the-arc-data-services-extension-is-created"></a>Controleren of de Arc Data Services-extensie is gemaakt

U kunt controleren of de Arc enabled Data Services-extensie is gemaakt vanuit de portal of door rechtstreeks verbinding te maken met het Kubernetes-cluster met Arc-functionaliteit. 

#### <a name="azure-portal"></a>Azure Portal
1. Meld u aan bij de Azure Portal en blader naar de resource groep waar de Kubernetes Connected cluster resource zich bevindt.
1. Selecteer de Arc enabled kubernetes-cluster (type = "Kubernetes-Azure Arc") waar de uitbrei ding is geïmplementeerd.
1. Selecteer in de navigatie aan de linkerkant onder **instellingen** de optie uitbrei dingen (preview).
1. De uitbrei ding die zojuist eerder is gemaakt in de status ' geïnstalleerd ' moet worden weer geven.

:::image type="content" source="media/deploy-data-controller-direct-mode-prerequisites/dc-extensions-dashboard.png" alt-text="Dash board extensies":::

#### <a name="kubectl-cli"></a>kubectl CLI

1. Maak verbinding met uw Kubernetes-cluster via een Terminal venster.
1. Voer de onderstaande opdracht uit en controleer of de (1) bovengenoemde naam ruimte is gemaakt en (2) de `bootstrapper` pod de status actief heeft voordat u verdergaat met de volgende stap.

``` console
kubectl get pods -n <name of namespace used in the json template file above>
```

In het volgende voor beeld wordt het Peul opgehaald uit de `arc` naam ruimte.

```console
#Example:
kubectl get pods -n arc
```

## <a name="create-a-custom-location-using-custom-location-cli-extension"></a>Een aangepaste locatie maken met de extensie aangepaste locatie CLI

Een aangepaste locatie is een Azure-resource die gelijk is aan een naam ruimte in een Kubernetes-cluster.  Aangepaste locaties worden gebruikt als doel voor het implementeren van resources van of naar Azure. Meer informatie over aangepaste locaties op de [aangepaste locaties boven op de Azure Arc enabled Kubernetes-documentatie](../kubernetes/conceptual-custom-locations.md).

### <a name="set-environment-variables"></a>Omgevingsvariabelen instellen

#### <a name="linux"></a>Linux

```bash
export clName=mycustomlocation
export clNamespace=arc
export hostClusterId=$(az connectedk8s show -g ${resourceGroup} -n ${resourceName} --query id -o tsv)
export extensionId=$(az k8s-extension show -g ${resourceGroup} -c ${resourceName} --cluster-type connectedClusters --name ${ADSExtensionName} --query id -o tsv)

az customlocation create -g ${resourceGroup} -n ${clName} --namespace ${clNamespace} \
  --host-resource-id ${hostClusterId} \
  --cluster-extension-ids ${extensionId} --location eastus2euap
```

#### <a name="windows-powershell"></a>Windows PowerShell
```PowerShell
$ENV:clName="mycustomlocation"
$ENV:clNamespace="arc"
$ENV:hostClusterId = az connectedk8s show -g "$ENV:resourceGroup" -n "$ENV:resourceName" --query id -o tsv
$ENV:extensionId = az k8s-extension show -g "$ENV:resourceGroup" -c "$ENV:resourceName" --cluster-type connectedClusters --name "$ENV:ADSExtensionName" --query id -o tsv

az customlocation create -g "$ENV:resourceGroup" -n "$ENV:clName" --namespace "$ENV:clNamespace" --host-resource-id "$ENV:hostClusterId" --cluster-extension-ids "$ENV:extensionId"
```

## <a name="validate--the-custom-location-is-created"></a>Valideren of de aangepaste locatie is gemaakt

Voer vanaf de Terminal de onderstaande opdracht uit om de aangepaste locaties weer te geven en te valideren dat de **inrichtings status** geslaagd is:

```azurecli
az customlocation list -o table
```

## <a name="create-the-azure-arc-data-controller"></a>De Azure Arc-gegevens controller maken

Nadat de uitbrei ding en de aangepaste locatie zijn gemaakt, gaat u verder met Azure Portal voor het implementeren van de Azure Arc-gegevens controller.

1. Meld u aan bij de Azure-portal.
1. Zoek naar ' Azure Arc data controller ' in de Azure Marketplace en start de stroom maken.
1. Zorg ervoor dat in de sectie **vereisten** de Azure Arc enabled Kubernetes-cluster (directe modus) is geselecteerd en ga verder met de volgende stap.
1. Kies in de sectie **Details van gegevens controller** een abonnement en resource groep.
1. Voer een naam in voor de gegevens controller.
1. Kies een configuratie profiel op basis van de Kubernetes die u implementeert.
1. Kies de aangepaste locatie die u in de vorige stap hebt gemaakt.
1. Geef details op voor de aanmelding en het wacht woord van de beheerder van de gegevens controller.
1. Geef details op voor ClientId, TenantId en client geheim voor de service-principal die wordt gebruikt voor het maken van de Azure-objecten. Zie [metrische gegevens uploaden](upload-metrics-and-logs-to-azure-monitor.md) voor gedetailleerde instructies voor het maken van een Service-Principal-account en de rollen die moeten worden verleend voor het account.
1. Klik op **volgende**, Controleer de overzichts pagina voor alle details en klik op **maken**.

## <a name="monitor-the-creation"></a>Het maken bewaken

Als de implementatie status van Azure Portal laat zien dat de implementatie is geslaagd, kunt u als volgt de status van de implementatie van de Arc-gegevens controller op het cluster controleren:

```console
kubectl get datacontrollers -n arc
```

## <a name="next-steps"></a>Volgende stappen

[Een PostgreSQL Hyperscale-servergroep met Azure Arc maken](create-postgresql-hyperscale-server-group.md)

[Een door Azure SQL beheerd exemplaar maken op Azure Arc](create-sql-managed-instance.md)
