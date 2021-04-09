---
title: Implementatie & HPA-metrische gegevens met container Insights | Microsoft Docs
description: In dit artikel wordt beschreven welke implementatie & HPA (POD) metrische gegevens worden verzameld met container Insights.
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: c8bb100b756ea92d73e1c3a698f119b4f8157930
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717703"
---
# <a name="deployment--hpa-metrics-with-container-insights"></a>Implementatie & HPA-metrische gegevens met container Insights

Met ingang van agent versie *ciprod08072020* verzamelt de agent voor container Insights nu metrische gegevens voor implementaties & HPAs.

## <a name="deployment-metrics"></a>Metrische implementaties

Container Insights start automatisch bewakings implementaties door de volgende metrische gegevens te verzamelen om 60 sec-intervallen en op te slaan in de tabel **InsightMetrics** :

|Naam van metrische gegevens |Metrische dimensie (Tags) |Description |
|------------|------------------------|------------|
|kube_deployment_status_replicas_ready |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, Deployment, deploymentStrategy, k8sNamespace, spec_replicas, status_replicas_available, status_replicas_updated (status. updatedReplicas) | Totaal aantal gereede peul dat is gericht op deze implementatie (status. readyReplicas). Hieronder ziet u de afmetingen van deze metrische gegevens. <ul> <li> implementatie-naam van de implementatie </li> <li> k8sNamespace-Kubernetes naam ruimte voor de implementatie </li> <li> deploymentStrategy-implementatie strategie die moet worden gebruikt voor het vervangen van peulen door nieuwe sjablonen (spec. strategie. type)</li><li> creationTime-tijds tempel voor het maken van implementaties </li> <li> spec_replicas-aantal gewenste peulen (spec. replicas) </li> <li>status_replicas_available: het totale aantal beschik bare peulen (gereed voor ten minste minReadySeconds) waarop deze implementatie is gericht (status. availableReplicas)</li><li>status_replicas_updated: het totale aantal niet-beëindigde peulen dat is gericht op deze implementatie en die de gewenste sjabloon specificatie (status. updatedReplicas) hebben. </li></ul>|

## <a name="hpa-metrics"></a>HPA-metrische gegevens

Met container Insights wordt de bewakings HPAs automatisch gestart door de volgende metrische gegevens te verzamelen om 60 sec-intervallen en op te slaan in de tabel **InsightMetrics** :

|Naam van metrische gegevens |Metrische dimensie (Tags) |Description |
|------------|------------------------|------------|
|kube_hpa_status_current_replicas |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, HPA, k8sNamespace, lastScaleTime, spec_max_replicas, spec_min_replicas, status_desired_replicas, targetKind, TargetName | Huidig aantal replica's van Peul dat wordt beheerd door deze automatische schaal functie (status. currentReplicas). Hieronder ziet u de afmetingen van deze metrische gegevens. <ul> <li> HPA-naam van de HPA </li> <li> k8sNamespace-Kubernetes naam ruimte voor de HPA </li> <li> lastScaleTime-laatste keer dat de HPA het aantal peulen (status. lastScaleTime) heeft geschaald</li><li> creationTime-HPA-tijds tempel maken </li> <li> spec_max_replicas-bovengrens voor het aantal peulen dat kan worden ingesteld door de automatische schaal functie (spec. maxReplicas) </li> <li> spec_min_replicas-ondergrens voor het aantal replica's waarmee de automatisch schalen kan worden geschaald (spec. minReplicas) </li><li>status_desired_replicas gewenst aantal replica's van peulen die worden beheerd door deze automatisch schalen (status. desiredReplicas)</li><li>targetKind-type van het doel van HPA (spec. scaleTargetRef. soort) </li><li>TargetName-naam van het doel van HPA (spec.scaleTargetRef.name) </li></ul>|

## <a name="deployment--hpa-charts"></a>Implementatie & HPA-grafieken 

Container Insights bevat vooraf geconfigureerde grafieken voor de metrische gegevens die eerder in de tabel zijn opgenomen als een werkmap voor elk cluster. U kunt de implementaties & HPA werkmap **implementaties & hpa** rechtstreeks vanuit een AKS-cluster vinden door **werkmappen** te selecteren in het linkerdeel venster en in de vervolg keuzelijst **werkmappen weer geven** in het inzicht.

## <a name="next-steps"></a>Volgende stappen

- Bekijk [metrische gegevens over de uitvoeren in Kubernetes](https://github.com/kubernetes/kube-state-metrics/tree/master/docs) voor meer informatie over metrische gegevens over de uitvoeren-status.