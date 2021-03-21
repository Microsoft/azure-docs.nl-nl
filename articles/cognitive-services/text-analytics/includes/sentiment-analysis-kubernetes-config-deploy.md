---
title: Sentimentanalyse Kubernetes-configuratie en implementatie stappen
titleSuffix: Azure Cognitive Services
description: Sentimentanalyse Kubernetes-configuratie en implementatie stappen
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: ca8d4d725ff25687d1005ddab1964316a147c730
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017847"
---
### <a name="deploy-the-sentiment-analysis-container-to-an-aks-cluster"></a>De Sentimentanalyse-container implementeren in een AKS-cluster

1. Open de Azure CLI en meld u aan bij Azure.

    ```azurecli
    az login
    ```

1. Meld u aan bij het AKS-cluster. Vervang `your-cluster-name` en `your-resource-group` door de juiste waarden.

    ```azurecli
    az aks get-credentials -n your-cluster-name -g -your-resource-group
    ```

    Wanneer deze opdracht wordt uitgevoerd, wordt een bericht met de volgende strekking gerapporteerd:

    ```console
    Merged "your-cluster-name" as current context in /home/username/.kube/config
    ```

    > [!WARNING]
    > Als er meerdere abonnementen voor u beschikbaar zijn in uw Azure-account en de `az aks get-credentials` opdracht wordt geretourneerd met een fout, is een veelvoorkomend probleem dat u het verkeerde abonnement gebruikt. Stel de context van uw Azure CLI-sessie in op het gebruik van hetzelfde abonnement dat u hebt gemaakt voor de resources en probeer het opnieuw.
    > ```azurecli
    >  az account set -s subscription-id
    > ```

1. Open de gewenste tekst editor. In dit voor beeld wordt Visual Studio code gebruikt.

    ```console
    code .
    ```

1. Maak in de tekst editor een nieuw bestand met de naam *sentiment. yaml* en plak de volgende YAML hierin. Vervang `billing/value` en door `apikey/value` uw eigen gegevens.

    ```yaml
    apiVersion: apps/v1beta1
    kind: Deployment
    metadata:
      name: sentiment
    spec:
      template:
        metadata:
          labels:
            app: sentiment-app
        spec:
          containers:
          - name: sentiment
            image: mcr.microsoft.com/azure-cognitive-services/sentiment
            ports:
            - containerPort: 5000
            resources:
              requests:
                memory: 2Gi
                cpu: 1
              limits:
                memory: 4Gi
                cpu: 1
            env:
            - name: EULA
              value: "accept"
            - name: billing
              value: # {ENDPOINT_URI}
            - name: apikey
              value: # {API_KEY}
     
    --- 
    apiVersion: v1
    kind: Service
    metadata:
      name: sentiment
    spec:
      type: LoadBalancer
      ports:
      - port: 5000
      selector:
        app: sentiment-app
    ```

1. Sla het bestand op en sluit de tekst editor.
1. Voer de opdracht Kubernetes uit `apply` met het bestand *sentiment. yaml* als doel:

    ```console
    kubectl apply -f sentiment.yaml
    ```

    Nadat de opdracht de implementatie configuratie heeft toegepast, wordt een bericht weer gegeven zoals in de volgende uitvoer:

    ```output
    deployment.apps "sentiment" created
    service "sentiment" created
    ```
1. Controleer of de Pod is geïmplementeerd:

    ```console
    kubectl get pods
    ```

    De uitvoer voor de uitvoerings status van de Pod:

    ```output
    NAME                         READY     STATUS    RESTARTS   AGE
    sentiment-5c9ccdf575-mf6k5   1/1       Running   0          1m
    ```

1. Controleer of de service beschikbaar is en haal het IP-adres op.

    ```console
    kubectl get services
    ```

    De uitvoer voor de uitvoerings status van de *sentiment* -service in de Pod:

    ```output
    NAME         TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)          AGE
    kubernetes   ClusterIP      10.0.0.1      <none>           443/TCP          2m
    sentiment    LoadBalancer   10.0.100.64   168.61.156.180   5000:31234/TCP   2m
    ```
