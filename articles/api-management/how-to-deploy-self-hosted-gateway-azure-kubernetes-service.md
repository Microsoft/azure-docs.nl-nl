---
title: Een zelf-hostende Gateway implementeren in azure Kubernetes service | Microsoft Docs
description: Meer informatie over het implementeren van een zelf-hostend gateway onderdeel van Azure API Management naar Azure Kubernetes service
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 04/26/2020
ms.author: apimpm
ms.openlocfilehash: 02962e9c5be2c4b73d121a53a7b595c573ad6cd0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87015218"
---
# <a name="deploy-to-azure-kubernetes-service"></a>Implementeren naar Azure Kubernetes Service

Dit artikel bevat de stappen voor het implementeren van een zelf-hostend gateway onderdeel van Azure API Management naar de [Azure Kubernetes-service](https://azure.microsoft.com/services/kubernetes-service/). Zie dit[document](how-to-deploy-self-hosted-gateway-kubernetes.md)voor het implementeren van een zelf-hostende gateway naar een Kubernetes-cluster.

## <a name="prerequisites"></a>Vereisten

- [Een Azure API Management-exemplaar maken](get-started-create-service-instance.md)
- [Een Azure Kubernetes-cluster maken](../aks/kubernetes-walkthrough-portal.md)
- [Richt een gateway bron in uw API Management-exemplaar in](api-management-howto-provision-self-hosted-gateway.md).

## <a name="deploy-the-self-hosted-gateway-to-aks"></a>De zelf-hostende Gateway implementeren op AKS

1. Selecteer **gateways** onder **implementatie en infra structuur**.
2. Selecteer de zelf-hostende gateway resource die u wilt implementeren.
3. Selecteer **implementatie**.
4. Houd er rekening mee dat een nieuw token in het tekstvak **token** voor u automatisch wordt gegenereerd met de standaard waarden voor de **verlopende** en **geheime sleutel** . Pas een van beide of beide indien gewenst aan en selecteer **genereren** om een nieuw token te maken.
5. Zorg ervoor dat **Kubernetes** is geselecteerd onder **implementatie scripts**.
6. Selecteer **<gateway naam>. yml-** bestands koppeling naast **implementatie** om het bestand te downloaden.
7. Pas de poort toewijzingen en de container naam in het yml-bestand naar wens aan.
8. Afhankelijk van uw scenario moet u het [Service type](../aks/concepts-network.md#services)mogelijk wijzigen. De standaardwaarde is `NodePort`.
9. Selecteer het **Kopieer** pictogram dat zich aan de rechter kant van het tekstvak **implementeren** bevindt om de `kubectl` opdracht op het klem bord op te slaan.
10. Plak de opdracht in het venster Terminal (of Command). Houd er rekening mee dat de opdracht verwacht dat het gedownloade omgevings bestand aanwezig is in de huidige map.
```console
    kubectl apply -f <gateway-name>.yaml
```
11. Voer de opdracht uit. De opdracht geeft uw AKS-cluster de instructie om de container uit te voeren, waarbij de zelf-hostende gateway afbeelding wordt gedownload van de micro soft-Container Registry en om de container zo te configureren dat deze HTTP-(8080) en HTTPS-poorten (443) beschikbaar stelt.
12. Voer de onderstaande opdracht uit om te controleren of de pod van de gateway actief is. Houd er rekening mee dat de naam van uw Pod afwijkt.
```console
kubectl get pods
NAME                                   READY     STATUS    RESTARTS   AGE
contoso-apim-gateway-59f5fb94c-s9stz   1/1       Running   0          1m
```
13. Voer de onderstaande opdracht uit om te controleren of de Gateway Service wordt uitgevoerd. Houd er rekening mee dat uw service naam en IP-adres verschillend zijn.
```console
kubectl get services
NAME             TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
contosogateway   NodePort    10.110.230.87   <none>        80:32504/TCP,443:30043/TCP   1m
```
14. Ga terug naar de Azure Portal en bevestig dat het gateway-knoop punt dat u zojuist hebt geïmplementeerd, een goede status rapporteert.

> [!TIP]
> Gebruik <code>kubectl logs <gateway-pod-name></code> de opdracht om een moment opname van het zelf-hostende gateway logboek weer te geven.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de zelf-hostende Gateway vindt u in [Azure API Management zelf-hostende gateway-overzicht](self-hosted-gateway-overview.md)
* Meer informatie over de [Azure Kubernetes-service](../aks/intro-kubernetes.md)
* Meer informatie [over het configureren en persistent maken van Logboeken in de Cloud](how-to-configure-cloud-metrics-logs.md)
* * Meer informatie [over het lokaal configureren en persistent maken van Logboeken](how-to-configure-local-metrics-logs.md)
