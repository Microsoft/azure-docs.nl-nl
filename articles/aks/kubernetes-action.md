---
title: Containers bouwen, testen en implementeren in azure Kubernetes service met GitHub-acties
description: Meer informatie over het gebruik van GitHub-acties voor het implementeren van uw container in Kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: 716cf4f4bfaed31dcbd756ae9494e1ddc8e475ad
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929877"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>GitHub acties voor het implementeren van de Kubernetes-service

[Github-acties](https://help.github.com/en/articles/about-github-actions) bieden u de flexibiliteit om een geautomatiseerde werk stroom voor de levens cyclus van software ontwikkeling te bouwen. U kunt meerdere Kubernetes-acties gebruiken om te implementeren in containers van Azure Container Registry naar Azure Kubernetes service met GitHub-acties. 

## <a name="prerequisites"></a>Vereisten 

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een GitHub-account. Als u geen account hebt, kunt u zich registreren voor een [gratis](https://github.com/join) account.  
- Een werkende Kubernetes-cluster
    - [Zelf studie: een toepassing voorbereiden voor de Azure Kubernetes-service](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>Overzicht van werkstroom bestand

Een werkstroom wordt gedefinieerd door een YAML-bestand (.yml) in het pad `/.github/workflows/` in uw opslagplaats. Deze definitie bevat de verschillende stappen en parameters die deel uitmaken van de werkstroom.

Voor een werk stroom gericht AKS heeft het bestand drie secties:

|Sectie  |Taken  |
|---------|---------|
|**Verificatie** | Aanmelden bij een persoonlijk container register (ACR) |
|**Ontwikkelen** | De container installatie kopie bouwen & pushen  |
|**Implementeren** | 1. Stel het doel-AKS-cluster in |
| |2. Maak een algemeen/docker-register geheim in het Kubernetes-cluster  |
||3. implementeren naar het Kubernetes-cluster|

## <a name="create-a-service-principal"></a>Een service-principal maken

U kunt een [Service-Principal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) maken met behulp van de opdracht [AZ AD SP create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) in de [Azure cli](/cli/azure/). U kunt deze opdracht uitvoeren met behulp van [Azure Cloud shell](https://shell.azure.com/) in het Azure portal of door de knop **try it** te selecteren.

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Vervang in de bovenstaande opdracht de tijdelijke aanduidingen door de abonnements-ID en de resource groep. De uitvoer is de roltoewijzings referenties die toegang bieden tot uw resource. De opdracht moet een JSON-object uitvoeren dat er ongeveer als volgt uitziet.

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

Volg de stappen voor het configureren van de geheimen:

1. Blader in [github](https://github.com/)naar uw opslag plaats, selecteer **instellingen > geheimen > een nieuw geheim toe te voegen**.

    ![Scherm afbeelding toont de koppeling een nieuwe geheime verbinding toevoegen voor een opslag plaats.](media/kubernetes-action/secrets.png)

2. Plak de inhoud van de bovenstaande `az cli` opdracht als de waarde van de geheime variabele. Bijvoorbeeld `AZURE_CREDENTIALS`.

3. U kunt ook de volgende extra geheimen voor de container register referenties definiëren en instellen in de aanmeldings actie voor docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. De geheimen worden weer gegeven, zoals hieronder is gedefinieerd.

    ![Scherm opname toont de bestaande geheimen voor een opslag plaats.](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Een container installatie kopie bouwen en implementeren in azure Kubernetes service-cluster

Het bouwen en pushen van de container installatie kopieën wordt uitgevoerd met behulp van `Azure/docker-login@v1` actie. 


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
    - uses: actions/checkout@master
    
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

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Implementeren naar Azure Kubernetes service-cluster

Als u een container installatie kopie wilt implementeren in AKS, moet u de `Azure/k8s-deploy@v1` actie gebruiken. Deze actie heeft vijf para meters:

| **Parameter**  | **Uitleg**  |
|---------|---------|
| **naam ruimte** | Beschrijving Kies de doel-Kubernetes naam ruimte. Als de naam ruimte niet wordt gegeven, worden de opdrachten uitgevoerd in de standaard naam ruimte | 
| **manifesten** |  Lang Pad naar de manifest bestanden die worden gebruikt voor implementatie |
| **installatie kopieën** | Beschrijving Volledig gekwalificeerde resource-URL van de afbeelding (en) die moet worden gebruikt voor vervangingen in de manifest bestanden |
| **imagepullsecrets** | Beschrijving Naam van een docker-REGI ster dat al in het cluster is ingesteld. Elk van deze geheime namen wordt toegevoegd onder het veld imagePullSecrets voor de werk belastingen die zijn gevonden in de invoer manifest bestanden |
| **kubectl-versie** | Beschrijving Hiermee wordt een specifieke versie van kubectl binary geïnstalleerd |


Voordat u kunt implementeren naar AKS, moet u de doel-Kubernetes-naam ruimte instellen en een installatie kopie pull Secret maken. Zie [pull-installatie kopieën van een Azure container Registry naar een Kubernetes-cluster](../container-registry/container-registry-auth-kubernetes.md)voor meer informatie over hoe pull-installatie kopieën werken. 

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


Voltooi uw implementatie met de `k8s-deploy` actie. Vervang de omgevings variabelen door de waarden voor uw toepassing. 

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
    - uses: actions/checkout@master
    
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

Wanneer uw Kubernetes-cluster, container register en opslag plaats niet meer nodig zijn, moet u de resources opschonen die u hebt geïmplementeerd door de resource groep en uw GitHub-opslag plaats te verwijderen. 

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over de Azure Kubernetes-service](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>Meer Kubernetes GitHub-acties

* Installatie programma voor het [Kubectl-hulp programma](https://github.com/Azure/setup-kubectl) ( `azure/setup-kubectl` ): installeert een specifieke versie van Kubectl op de loper.
* [Kubernetes set context](https://github.com/Azure/k8s-set-context) ( `azure/k8s-set-context` ): Stel de cluster context van het doel Kubernetes in dat door andere acties wordt gebruikt, of voer een kubectl-opdracht uit.
* [AKS set context](https://github.com/Azure/aks-set-context) ( `azure/aks-set-context` ): Stel de doel context van de Azure Kubernetes-service cluster in.
* [Kubernetes maken geheim](https://github.com/Azure/k8s-create-secret) ( `azure/k8s-create-secret` ): Maak een algemeen geheim of docker-REGI ster in het Kubernetes-cluster.
* [Kubernetes Deploy](https://github.com/Azure/k8s-deploy) ( `azure/k8s-deploy` ): maken en implementeer manifesten op Kubernetes-clusters.
* [Setup-helm](https://github.com/Azure/setup-helm) ( `azure/setup-helm` ): Installeer een specifieke versie van helm binary op de loper.
* [Kubernetes maken](https://github.com/Azure/k8s-bake) ( `azure/k8s-bake` ): maken-manifest bestand dat moet worden gebruikt voor implementaties met behulp van helm2, kustomize of kompose.
* [Kubernetes pluis](https://github.com/azure/k8s-lint) ( `azure/k8s-lint` ): Valideer/pluis uw manifest bestanden.
