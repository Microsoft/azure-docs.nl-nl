---
title: Bedrijfscontinuïteit en herstel na noodgevallen
services: azure-dev-spaces
author: lisaguthrie
ms.author: lcozzens
ms.date: 01/28/2019
ms.topic: conceptual
description: Meer informatie over het gebruik van Azure dev Spaces en Azure Kubernetes Services voor bedrijfs continuïteit en voor bereiding op nood herstel
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, servicemesh, servicemeshroutering, kubectl, k8s '
manager: gwallace
ms.openlocfilehash: 824eb1e65e7da5736080e47509aa163a868d8ec9
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023326"
---
# <a name="business-continuity-and-disaster-recovery-in-azure-dev-spaces"></a>Bedrijfs continuïteit en herstel na nood gevallen in azure dev Spaces

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

## <a name="review-disaster-recovery-guidance-for-azure-kubernetes-service-aks"></a>Raadpleeg de richt lijnen voor herstel na nood gevallen voor Azure Kubernetes service (AKS)

Azure dev Spaces is een functie van Azure Kubernetes service (AKS). U moet rekening houden met richt lijnen voor herstel na nood gevallen in AKS en u kunt overwegen of ze van toepassing zijn op de AKS-clusters die u gebruikt voor dev-ruimten. Raadpleeg [Aanbevolen procedures voor bedrijfs continuïteit en herstel na nood gevallen in azure Kubernetes service (AKS)](../../aks/operator-best-practices-multi-region.md) voor meer informatie.

## <a name="enable-dev-spaces-on-aks-clusters-in-different-regions"></a>Ontwikkel ruimten in AKS-clusters in verschillende regio's inschakelen

Door ontwikkel ruimten in AKS-clusters in verschillende regio's in te scha kelen, kunt u direct na een storing in de Azure-regio het gebruik van dev Spaces hervatten.

Voor algemene informatie over implementaties met meerdere regio's van AKS raadpleegt u [implementatie van meerdere regio's plannen](../../aks/operator-best-practices-multi-region.md#plan-for-multiregion-deployment)

### <a name="enable-dev-spaces-via-the-azure-cli"></a>Ontwikkel ruimten inschakelen via de Azure CLI

U kunt ook dev Spaces inschakelen op de opdracht regel:

```azurecli
az aks use-dev-spaces -g <resource group name> -n <cluster name>
```

## <a name="deploy-your-teams-baseline-to-each-cluster"></a>De basis lijn van uw team implementeren voor elk cluster

Wanneer u met dev Spaces werkt, implementeert u doorgaans de volledige toepassing naar een bovenliggende ontwikkel ruimte op uw Kubernetes-cluster. Standaard `default` wordt de ruimte gebruikt. De eerste implementatie omvat alle services en de externe resources waarvan deze services afhankelijk zijn, zoals data bases of wacht rijen. Dit staat bekend als de *basis lijn*. Zodra u een basis lijn in de bovenliggende ontwikkel ruimte hebt ingesteld, kunt u de afzonderlijke services in onderliggende ontwikkel ruimten opsporen en fout opsporing uitvoeren.

U moet de meest recente versies van uw Baseline set services implementeren voor clusters in meerdere regio's. Als u de basislijn Services op deze manier bijwerkt, kunt u ontwikkel ruimten blijven gebruiken als er sprake is van een Azure-regio fout. Als u bijvoorbeeld de basis lijn via een CI/CD-pijp lijn implementeert, wijzigt u de pijp lijn zodanig dat deze wordt geïmplementeerd in meerdere clusters in verschillende regio's.

## <a name="select-the-correct-aks-cluster-to-use-for-dev-spaces"></a>Selecteer het juiste AKS-cluster om te gebruiken voor dev-ruimten

Zodra u een back-upcluster hebt geconfigureerd waarop de basis lijn van uw team wordt uitgevoerd, kunt u op elk gewenst moment snel overschakelen naar het back-upcluster. Vervolgens kunt u de afzonderlijke services die u aan het werk bent, opnieuw uitvoeren in onderliggende ontwikkel ruimten.

Selecteer een ander cluster met de volgende CLI-opdracht:

```azurecli
az aks use-dev-spaces -g <new resource group name> -n <new cluster name>
```

U kunt de beschik bare ontwikkel ruimten op het nieuwe cluster weer geven met de volgende opdracht:

```cmd
azds space list
```

U kunt een nieuwe ontwikkel ruimte maken om in te werken of een bestaande dev-ruimte selecteren met de volgende opdracht:

```cmd
azds space select -n <space name>
```

Na het uitvoeren van deze opdrachten worden de geselecteerde cluster-en dev-ruimte gebruikt voor volgende CLI-bewerkingen en voor het opsporen van fouten in projecten met de Visual Studio code Extension voor Azure dev Spaces.

Als u Visual Studio gebruikt, kunt u via de volgende stappen overschakelen naar het cluster dat door een bestaand project wordt gebruikt:

1. Open uw project in Visual Studio.
1. Klik met de rechter muisknop op de project naam in Solution Explorer en klik op **Eigenschappen**
1. Klik in het linkerdeel venster op **fouten opsporen**
1. Klik op de pagina eigenschappen van fout opsporing op de vervolg keuzelijst **profiel** en kies **Azure dev Spaces**.
1. Klik op de knop **wijzigen** .
1. Selecteer in het dialoog venster dat wordt weer gegeven het AKS-cluster dat u wilt gebruiken. Kies indien gewenst een andere ontwikkel ruimte om in te werken of maak een nieuwe ontwikkel ruimte door de gewenste optie te selecteren in de vervolg keuzelijst **ruimte** .

Wanneer u het juiste cluster en de benodigde ruimte hebt geselecteerd, kunt u op F5 drukken om de service uit te voeren in dev Spaces.

Herhaal deze stappen voor andere projecten die zijn geconfigureerd voor gebruik van het oorspronkelijke cluster.

## <a name="access-a-service-on-a-backup-cluster"></a>Toegang tot een service op een back-upcluster

Als u uw service hebt geconfigureerd voor het gebruik van een open bare DNS-naam, heeft de service een andere URL als u deze uitvoert op een back-upcluster. Open bare DNS-namen zijn altijd in de indeling `<space name>.s.<root space name>.<service name>.<cluster GUID>.<region>.azds.io` . Als u overschakelt naar een ander cluster, worden de cluster-GUID en de regio mogelijk gewijzigd.

In dev ruimten wordt altijd de juiste URL voor de service weer gegeven wanneer deze wordt uitgevoerd `azds up` , of in het uitvoer venster in Visual Studio onder **Azure dev Spaces**.

U kunt de URL ook vinden door de opdracht uit te voeren `azds list-uris` :
```
$ azds list-uris
Uri                                                     Status
------------------------------------------------------  ---------
http://default.mywebapi.d05afe7e006a4fddb73c.eus.azds.io/  Available
```

Gebruik deze URL voor toegang tot de service.
