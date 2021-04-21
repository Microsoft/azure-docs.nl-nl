---
title: Containers bouwen, testen en implementeren in Azure Kubernetes Service met GitHub Actions
description: Meer informatie over het gebruik GitHub Actions om uw container te implementeren in Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: 3a8e91f74fe3c862a814d7660e64748df9553f1d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779755"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>GitHub Actions voor implementatie in Kubernetes-service

[GitHub Actions](https://docs.github.com/en/actions) biedt u de flexibiliteit om een geautomatiseerde werkstroom voor de levenscyclus van softwareontwikkeling te bouwen. U kunt meerdere Kubernetes-acties gebruiken om containers te implementeren van Azure Container Registry naar Azure Kubernetes Service met GitHub Actions. 

## <a name="prerequisites"></a>Vereisten 

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een GitHub-account. Als u geen account hebt, kunt u zich registreren voor een [gratis](https://github.com/join) account.  
- Een werkend Kubernetes-cluster
    - [Zelfstudie: Een toepassing voorbereiden voor Azure Kubernetes Service](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>Overzicht van werkstroom bestand

Een werkstroom wordt gedefinieerd door een YAML-bestand (.yml) in het pad `/.github/workflows/` in uw opslagplaats. Deze definitie bevat de verschillende stappen en parameters die deel uitmaken van de werkstroom.

Voor een werkstroom die is gericht op AKS, heeft het bestand drie secties:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | Aanmelden bij een privécontainerregister (ACR) |
|**Build** | De container & pushen  |
|**Implementeren** | 1. Het AKS-doelcluster instellen |
| |2. Een algemeen/docker-registergeheim maken in een Kubernetes-cluster  |
||3. Implementeren in het Kubernetes-cluster|

## <a name="create-a-service-principal"></a>Een service-principal maken

U kunt een [service-principal maken](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) met behulp van [de opdracht az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) in de Azure [CLI.](/cli/azure/) U kunt deze opdracht uitvoeren met behulp [Azure Cloud Shell](https://shell.azure.com/) in de Azure Portal of door de knop **Proberen te** selecteren.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Vervang in de bovenstaande opdracht de tijdelijke aanduidingen door uw abonnements-id en resourcegroep. De uitvoer bestaat uit de referenties voor roltoewijzing die toegang bieden tot uw resource. Met de opdracht moet een JSON-object worden uitgevoerd dat vergelijkbaar is met dit.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Kopieer dit JSON-object, dat u kunt gebruiken om te verifiëren vanuit GitHub.

## <a name="configure-the-github-secrets"></a>De GitHub-geheimen configureren

Volg de stappen om de geheimen te configureren:

1. Blader [in GitHub](https://github.com/)naar uw opslagplaats, selecteer **Instellingen > Geheimen > Een nieuw geheim toevoegen.**

    ![Schermopname van de koppeling Een nieuw geheim toevoegen voor een opslagplaats.](media/kubernetes-action/secrets.png)

2. Plak de inhoud van de bovenstaande `az cli` opdracht als de waarde van de geheime variabele. Bijvoorbeeld `AZURE_CREDENTIALS`.

3. Definieer op dezelfde manier de volgende aanvullende geheimen voor de containerregisterreferenties en stel deze in bij de docker-aanmeldingsactie. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. U ziet de geheimen zoals hieronder wordt weergegeven zodra deze zijn gedefinieerd.

    ![Schermopname van bestaande geheimen voor een opslagplaats.](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Een containerafbeelding bouwen en implementeren in Azure Kubernetes Service cluster

Het bouwen en pushen van de containerafbeeldingen wordt uitgevoerd met behulp van `Azure/docker-login@v1` de actie . 


```yml
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}

```

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Implementeren naar Azure Kubernetes Service cluster

Als u een containerafbeelding wilt implementeren in AKS, moet u de actie `Azure/k8s-deploy@v1` gebruiken. Deze actie heeft vijf parameters:

| **Parameter**  | **Uitleg**  |
|---------|---------|
| **Naamruimte** | (Optioneel) Kies de kubernetes-doelnaamruimte. Als de naamruimte niet is opgegeven, worden de opdrachten uitgevoerd in de standaardnaamruimte | 
| **Manifesteert** |  (Vereist) Pad naar de manifestbestanden, die worden gebruikt voor implementatie |
| **Afbeeldingen** | (Optioneel) Volledig gekwalificeerde resource-URL van de afbeelding(en) die moeten worden gebruikt voor vervangingen in de manifestbestanden |
| **imagepullsecrets** | (Optioneel) Naam van een docker-registergeheim dat al in het cluster is ingesteld. Elk van deze geheime namen wordt toegevoegd onder het veld imagePullSecrets voor de werkbelastingen in de invoermanifestbestanden |
| **kubectl-version** | (Optioneel) Installeert een specifieke versie van kubectl binary |


Voordat u kunt implementeren in AKS, moet u de Kubernetes-doelnaamruimte instellen en een pull-geheim voor de afbeelding maken. Zie [Pull images from an Azure container registry to a Kubernetes cluster (Afbeeldingen van een Azure-containerregister naar een Kubernetes-cluster pullen)](../container-registry/container-registry-auth-kubernetes.md)voor meer informatie over hoe het pullen van afbeeldingen werkt. 

```yaml
  # Create namespace if doesn't exist
  - run: |
      kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
  
  # Create image pull secret for ACR
  - uses: azure/k8s-create-secret@v1
    with:
      container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
      container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
      container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
      secret-name: ${{ env.SECRET }}
      namespace: ${{ env.NAMESPACE }}
      force: true
```


Voltooi uw implementatie met de `k8s-deploy` actie . Vervang de omgevingsvariabelen door waarden voor uw toepassing. 

```yaml

on: [push]

# Environment variables available to all jobs and steps in this workflow
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  SECRET: {secret-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
    
    # Set the target Azure Kubernetes Service (AKS) cluster. 
    - uses: azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: ${{ env.CLUSTER_NAME }}
        resource-group: ${{ env.CLUSTER_RESOURCE_GROUP }}
    
    # Create namespace if doesn't exist
    - run: |
        kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
    
    # Create image pull secret for ACR
    - uses: azure/k8s-create-secret@v1
      with:
        container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
        force: true
    
    # Deploy app to AKS
    - uses: azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        imagepullsecrets: |
          ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
```

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer uw Kubernetes-cluster, containerregister en opslagplaats niet meer nodig zijn, schoont u de resources op die u hebt geïmplementeerd door de resourcegroep en uw GitHub-opslagplaats te verwijderen. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over Azure Kubernetes Service](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>Meer Kubernetes-GitHub Actions

* [Installatieprogramma voor Kubectl](https://github.com/Azure/setup-kubectl) -hulpprogramma ( `azure/setup-kubectl` ): installeert een specifieke versie van kubectl op de runner.
* [Kubernetes-setcontext](https://github.com/Azure/k8s-set-context) ( ): stel de doelcontext van het Kubernetes-cluster in die wordt gebruikt door andere acties of `azure/k8s-set-context` voer kubectl-opdrachten uit.
* [AKS-setcontext](https://github.com/Azure/aks-set-context) ( `azure/aks-set-context` ): stel de doel-Azure Kubernetes Service clustercontext in.
* [Kubernetes-geheim maken](https://github.com/Azure/k8s-create-secret) ( ): maak een algemeen geheim of `azure/k8s-create-secret` docker-registry-geheim in het Kubernetes-cluster.
* [Kubernetes deploy](https://github.com/Azure/k8s-deploy) ( `azure/k8s-deploy` ): maak manifesten en implementeer ze in Kubernetes-clusters.
* [Helm instellen](https://github.com/Azure/setup-helm) ( `azure/setup-helm` ): installeer een specifieke versie van het binaire Helm-bestand op de runner.
* [Kubernetes maken](https://github.com/Azure/k8s-bake) ( ): maak een manifestbestand dat moet worden gebruikt voor implementaties met `azure/k8s-bake` helm2, kustomize of kompose.
* [Kubernetes-lint](https://github.com/azure/k8s-lint) ( `azure/k8s-lint` ): valideer/lint uw manifestbestanden.
