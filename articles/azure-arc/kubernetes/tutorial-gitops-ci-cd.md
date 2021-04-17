---
title: 'Zelfstudie: CI/CD implementeren met GitOps met behulp Azure Arc Kubernetes-clusters'
description: Deze zelfstudie bespreekt het instellen van een CI/CD-oplossing met behulp van GitOps met Azure Arc Kubernetes-clusters. Zie het artikel CI/CD Workflow using GitOps - Azure Arc enabled Kubernetes (CI/CD-werkstroom met GitOps - kubernetes met ingeschakelde Kubernetes) voor een conceptuele versie van deze werkstroom.
author: tcare
ms.author: tcare
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/03/2021
ms.custom: template-tutorial, devx-track-azurecli
ms.openlocfilehash: 9a228ce6f8b18afb77b656765abbad0bb4ae877f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589139"
---
# <a name="tutorial-implement-cicd-with-gitops-using-azure-arc-enabled-kubernetes-clusters"></a>Zelfstudie: CI/CD implementeren met GitOps met behulp Azure Arc Kubernetes-clusters


In deze zelfstudie stelt u een CI/CD-oplossing in met behulp van GitOps met Azure Arc Kubernetes-clusters. Met behulp van de Azure Vote-voorbeeld-app gaat u het volgende doen:

> [!div class="checklist"]
> * Maak een Azure Arc Kubernetes-cluster.
> * Verbind uw toepassing en GitOps-repos met Azure-repos.
> * IMPORTEER CI/CD-pijplijnen.
> * Verbind uw Azure Container Registry (ACR) met Azure DevOps en Kubernetes.
> * Maak omgevingsvariabelegroepen.
> * Implementeer de `dev` omgevingen `stage` en .
> * Test de toepassingsomgevingen.

Als u geen azure™ hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="before-you-begin"></a>Voordat u begint

In deze zelfstudie wordt ervan uitgenomen dat u bekend bent met Azure DevOps, Azure Repos en Pipelines en Azure CLI.

* Meld u aan [bij Azure DevOps Services.](https://dev.azure.com/)
* Voltooi de [vorige zelfstudie](./tutorial-use-gitops-connected-cluster.md) voor meer informatie over het implementeren van GitOps voor uw CI/CD-omgeving.
* Inzicht in [de voordelen en architectuur](./conceptual-configurations.md) van deze functie.
* Controleer of u het volgende hebt:
  * Een [verbonden Azure Arc Kubernetes-cluster](./quickstart-connect-cluster.md#connect-an-existing-kubernetes-cluster) met de **naam arc-cicd-cluster.**
  * Een verbonden Azure Container Registry (ACR) met [AKS-integratie](../../aks/cluster-container-registry-integration.md) of [niet-AKS-clusterverificatie.](../../container-registry/container-registry-auth-kubernetes.md)
  * Machtigingen voor 'Build Admin' en 'Project Admin' voor [Azure-repos](/azure/devops/repos/get-started/what-is-repos) en [Azure Pipelines.](/azure/devops/pipelines/get-started/pipelines-get-started)
* Installeer de volgende Azure Arc Kubernetes CLI-extensies van versies >= 1.0.0:

  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8sconfiguration
  ```
  * Voer de volgende opdrachten uit om deze extensies bij te werken naar de nieuwste versie:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8sconfiguration
    ```

## <a name="import-application-and-gitops-repos-into-azure-repos"></a>Toepassings- en GitOps-repos importeren in Azure-repos

Importeer [een toepassings-repo](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#application-repo) en [een GitOps-repo](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd#gitops-repo) in Azure-repos. Gebruik voor deze zelfstudie de volgende voorbeeld-repatriëringen:

* **arc-cicd-demo-src application** repo
   * Url: https://github.com/Azure/arc-cicd-demo-src
   * Bevat het voorbeeld van de Azure Vote-app die u gaat implementeren met behulp van GitOps.
* **arc-cicd-demo-gitops** GitOps-repo
   * Url: https://github.com/Azure/arc-cicd-demo-gitops
   * Werkt als basis voor uw clusterbronnen met de Azure Vote-app.

Meer informatie over [het importeren van Git-repo's.](/azure/devops/repos/git/import-git-repository)

>[!NOTE]
> Het importeren en gebruiken van twee afzonderlijke opslagplaatsen voor toepassings- en GitOps-opslagplaatsen kan de beveiliging en eenvoud verbeteren. De machtigingen en zichtbaarheid van de toepassing en de GitOps-opslagplaatsen kunnen afzonderlijk worden afgestemd.
> De clusterbeheerder kan bijvoorbeeld de wijzigingen in de toepassingscode niet vinden die relevant zijn voor de gewenste status van het cluster. Een toepassingsontwikkelaar hoeft daarentegen niet de specifieke parameters voor elke omgeving te kennen. Een set testwaarden die dekking bieden voor de parameters kan voldoende zijn.

## <a name="connect-the-gitops-repo"></a>Verbinding maken met de GitOps-repo

Als u uw app continu wilt implementeren, verbindt u de toepassings-repo met uw cluster met behulp van GitOps. Uw **GitOps-repo arc-cicd-demo-gitops** bevat de basisbronnen om uw app actief te maken op uw **cluster arc-cicd-cluster.**

De eerste GitOps-repo bevat alleen een [manifest](https://github.com/Azure/arc-cicd-demo-gitops/blob/master/arc-cicd-cluster/manifests/namespaces.yml) dat de **naamruimten dev** en **fase** maakt die overeenkomen met de implementatieomgevingen.

De GitOps-verbinding die u maakt, wordt automatisch:
* Synchroniseer de manifesten in de manifestmap.
* Werk de clustertoestand bij.

De CI/CD-werkstroom vult de manifestmap met extra manifesten om de app te implementeren.


1. [Maak een nieuwe GitOps-verbinding](./tutorial-use-gitops-connected-cluster.md) met uw zojuist geïmporteerde **arc-cicd-demo-gitops-repo** in Azure Repos.

   ```azurecli
   az k8sconfiguration create \
      --name cluster-config \
      --cluster-name arc-cicd-cluster \
      --resource-group myResourceGroup \
      --operator-instance-name cluster-config \
      --operator-namespace cluster-config \
      --repository-url https://dev.azure.com/<Your organization>/arc-cicd-demo-gitops \
      --https-user <Azure Repos username> \
      --https-key <Azure Repos PAT token> \
      --scope cluster \
      --cluster-type connectedClusters \
      --operator-params='--git-readonly --git-path=arc-cicd-cluster/manifests'
   ```

1. Zorg ervoor dat Flux *alleen* de `arc-cicd-cluster/manifests` map als basispad gebruikt. Definieer het pad met behulp van de volgende operatorparameter:

   `--git-path=arc-cicd-cluster/manifests`

   > [!NOTE]
   > Als u een HTTPS-connection string en verbindingsproblemen hebt, moet u ervoor zorgen dat u het voorvoegsel gebruikersnaam weglaten in de URL. Moet bijvoorbeeld `https://alice@dev.azure.com/contoso/arc-cicd-demo-gitops` zijn `alice@` verwijderd. De `--https-user` geeft in plaats daarvan de gebruiker op, bijvoorbeeld `--https-user alice` .

1. Controleer de status van de implementatie in Azure Portal.
   * Als dit lukt, ziet u de `dev` naamruimten en `stage` die in uw cluster zijn gemaakt.

## <a name="import-the-cicd-pipelines"></a>De CI/CD-pijplijnen importeren

Nu u een GitOps-verbinding hebt gesynchroniseerd, moet u de CI/CD-pijplijnen importeren die de manifesten maken.

De toepassings-repo bevat een map met de pijplijnen die `.pipeline` u gaat gebruiken voor PR's, CI en CD. Importeer en wijzig de naam van de drie pijplijnen in de voorbeeld-repo:

| Naam van pijplijnbestand | Description |
| ------------- | ------------- |
| [`.pipelines/az-vote-pr-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-pr-pipeline.yaml)  | De toepassings-PR-pijplijn, met de **naam arc-cicd-demo-src PR** |
| [`.pipelines/az-vote-ci-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-ci-pipeline.yaml) | De toepassings-CI-pijplijn met **de naam arc-cicd-demo-src CI** |
| [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) | De cd-pijplijn van de toepassing met **de naam arc-cicd-demo-src CD** |



## <a name="connect-your-acr"></a>Verbinding maken met uw ACR
Zowel uw pijplijnen als het cluster gebruiken ACR om Docker-afbeeldingen op te slaan en op te halen.

### <a name="connect-acr-to-azure-devops"></a>ACR verbinden met Azure DevOps
Tijdens het CI-proces implementeert u uw toepassingscontainers in een register. Maak eerst een Azure-serviceverbinding:

1. Open in Azure DevOps de **pagina Serviceverbindingen** op de pagina projectinstellingen. Open in TFS de **pagina Services** via het **instellingenpictogram** in de bovenste menubalk.
2. Kies **+ Nieuwe serviceverbinding** en selecteer het type serviceverbinding dat u nodig hebt.
3. Vul de parameters voor de serviceverbinding in. Voor deze zelfstudie:
   * Noem de serviceverbinding **arc-demo-acr**. 
   * Selecteer **myResourceGroup** als de resourcegroep.
4. Selecteer Toegang **verlenen aan alle pijplijnen.** 
   * Met deze optie worden YAML-pijplijnbestanden geautoriseerd voor serviceverbindingen. 
5. Kies **OK om** de verbinding te maken.

### <a name="connect-acr-to-kubernetes"></a>ACR verbinden met Kubernetes
Schakel uw Kubernetes-cluster in om afbeeldingen op te halen uit uw ACR. Als het privé is, is verificatie vereist.

#### <a name="connect-acr-to-existing-aks-clusters"></a>ACR verbinden met bestaande AKS-clusters

Integreer een bestaande ACR met bestaande AKS-clusters met behulp van de volgende opdracht:

```azurecli
az aks update -n arc-cicd-cluster -g myResourceGroup --attach-acr arc-demo-acr
```

#### <a name="create-an-image-pull-secret"></a>Een pull-geheim voor een afbeelding maken

Als u niet-AKS- en lokale clusters wilt verbinden met uw ACR, maakt u een pull-geheim voor de afbeelding. Kubernetes gebruikt pull-geheimen voor afbeeldingen om informatie op te slaan die nodig is om uw register te verifiëren.

Maak een pull-geheim voor de afbeelding met de volgende `kubectl` opdracht. Herhaal dit voor de `dev` `stage` naamruimten en .
```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

Om te voorkomen dat u voor elke pod een imagePullSecret moet instellen, kunt u overwegen om imagePullSecret toe te voegen aan het Service-account in de `dev` `stage` naamruimten en . Zie de [Kubernetes-zelfstudie](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account) voor meer informatie.

## <a name="create-environment-variable-groups"></a>Omgevingsvariabelegroepen maken

### <a name="app-repo-variable-group"></a>Groep met app-repo-variabelen
[Maak een variabelegroep met](/azure/devops/pipelines/library/variable-groups) de **naam az-vote-app-dev**. Stel de volgende waarden in:

| Variabele | Waarde |
| -------- | ----- |
| AZ_ACR_NAME | (uw ACR-exemplaar, bijvoorbeeld. azurearctest.azurecr.io) |
| AZURE_SUBSCRIPTION | (uw Azure-serviceverbinding, die **arc-demo-acr** moet zijn van eerder in de zelfstudie) |
| AZURE_VOTE_IMAGE_REPO | Het volledige pad naar de Azure Vote-app-repo, bijvoorbeeld azurearctest.azurecr.io/azvote |
| ENVIRONMENT_NAME | Dev |
| MANIFESTS_BRANCH | `master` |
| MANIFESTS_REPO | De Git connection string voor uw GitOps-repo |
| Pat | Een [gemaakt PAT-token](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate#create-a-pat) met bronmachtigingen voor lezen/schrijven. Sla deze op voor later gebruik bij het maken van `stage` de variabele groep. |
| SRC_FOLDER | `azure-vote` | 
| TARGET_CLUSTER | `arc-cicd-cluster` |
| TARGET_NAMESPACE | `dev` |

> [!IMPORTANT]
> Markeer uw PAT als geheimtype. In uw toepassingen kunt u geheimen uit een [Azure KeyVault koppelen.](/azure/devops/pipelines/library/variable-groups#link-secrets-from-an-azure-key-vault)
>
### <a name="stage-environment-variable-group"></a>Omgevingsvariabelegroep faseer

1. Kloon **de variabelegroep az-vote-app-dev.**
1. Wijzig de naam in **az-vote-app-stage**.
1. Controleer de volgende waarden voor de bijbehorende variabelen:

| Variabele | Waarde |
| -------- | ----- |
| ENVIRONMENT_NAME | Fase |
| TARGET_NAMESPACE | `stage` |

U bent nu klaar om te implementeren in de `dev` omgevingen en `stage` .

## <a name="deploy-the-dev-environment-for-the-first-time"></a>De dev-omgeving voor het eerst implementeren
Nu de CI- en CD-pijplijnen zijn gemaakt, moet u de CI-pijplijn uitvoeren om de app voor de eerste keer te implementeren.

### <a name="ci-pipeline"></a>CI-pijplijn

Tijdens de eerste ci-pijplijn wordt er mogelijk een resourceautorisatiefout weergegeven bij het lezen van de naam van de serviceverbinding.
1. Controleer of de variabele die wordt gebruikt, AZURE_SUBSCRIPTION.
1. Autor het gebruik.
1. De pijplijn opnieuw uit te proberen.

De CI-pijplijn:
* Zorgt ervoor dat de toepassingswijziging alle geautomatiseerde kwaliteitscontroles voor implementatie doorstaat.
* Wordt er een extra validatie uitgevoerd die niet kan worden voltooid in de pr-pijplijn.
    * Specifiek voor GitOps publiceert de pijplijn ook de artefacten voor de door commit die door de CD-pijplijn wordt geïmplementeerd.
* Controleert of de Docker-afbeelding is gewijzigd en of de nieuwe afbeelding wordt pusht.

### <a name="cd-pipeline"></a>CD-pijplijn
De geslaagde CI-pijplijn-run activeert de CD-pijplijn om het implementatieproces te voltooien. U implementeert incrementeel in elke omgeving.

> [!TIP]
> Als de CD-pijplijn niet automatisch wordt getriggerd:
> 1. Controleer of de naam overeenkomt met de vertakkingstrigger in [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml)
>    * Dit moet `arc-cicd-demo-src CI` zijn.
> 1. De CI-pijplijn opnieuw uit te proberen.

Zodra de sjabloon en het manifest zijn gewijzigd in de GitOps-repo, maakt de CD-pijplijn een door commit, pusht u deze en maakt u een pr ter goedkeuring.
1. Open de pr-koppeling in de `Create PR` taakuitvoer.
1. Controleer de wijzigingen in de GitOps-repo. U ziet het volgende:
   * Helm-sjabloonwijzigingen op hoog niveau.
   * Kubernetes-manifesten op laag niveau die de onderliggende wijzigingen in de gewenste status tonen. Flux implementeert deze manifesten.
1. Als alles er goed uitziet, keurt u de aanvraag goed en voltooit u deze.

1. Na enkele minuten wordt de wijziging door Flux opgehaald en wordt de implementatie gestart.
1. Doorsturen van de poort lokaal `kubectl` met behulp van en controleren of de app correct werkt met behulp van:

   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`

1. Bekijk de Azure Vote-app in uw browser op `http://localhost:8080/` .

1. Stem op uw favorieten en bereid u voor op het aanbrengen van enkele wijzigingen in de app.

## <a name="set-up-environment-approvals"></a>Omgevingsgoedkeuringen instellen
Bij de implementatie van de app kunt u niet alleen wijzigingen aanbrengen in de code of sjablonen, maar kunt u het cluster ook per ongeluk in een slechte staat brengen.

Als in de dev-omgeving een onderbreking na de implementatie wordt gedetecteerd, kunt u voorkomen dat deze naar latere omgevingen gaat met behulp van omgevingsgoedkeuringen.

1. Ga in uw Azure DevOps-project naar de omgeving die moet worden beveiligd.
1. **Navigeer naar Goedkeuringen en controles** voor de resource.
1. Selecteer **Maken**.
1. Geef de goedkeurders en een optioneel bericht op.
1. Selecteer **opnieuw Maken** om de handmatige goedkeuringscontrole te voltooien.

Zie de zelfstudie Goedkeuring en controles [definiëren voor meer](/azure/devops/pipelines/process/approvals) informatie.

De volgende keer dat de CD-pijplijn wordt uitgevoerd, wordt de pijplijn onderbroken nadat de GitOps-pr is gemaakt. Controleer of de wijziging juist is gesynchroniseerd en de basisfunctionaliteit doorstaat. Keur de controle van de pijplijn goed om de wijziging naar de volgende omgeving te laten stromen.

## <a name="make-an-application-change"></a>Een toepassingswijziging aanbrengen

Met deze basislijnset met sjablonen en manifesten die de status in het cluster vertegenwoordigen, maakt u een kleine wijziging in de app.

1. Bewerk het bestand in de **arc-cicd-demo-src-repo.** [`azure-vote/src/azure-vote-front/config_file.cfg`](https://github.com/Azure/arc-cicd-demo-src/blob/master/azure-vote/src/azure-vote-front/config_file.cfg)

2. Omdat 'Katten versus honden' onvoldoende stemmen krijgt, wijzigt u deze in Tabs vs Spaces om het aantal stemmen te laten optellen.

3. De wijziging door te geven in een nieuwe vertakking, deze te pushen en een pull-aanvraag te maken.
   * Dit is de typische ontwikkelaarsstroom die de CI/CD-levenscyclus start.

## <a name="pr-validation-pipeline"></a>Pijplijn voor PR-validatie

De pr-pijplijn is de eerste verdedigingslinie tegen een foute wijziging. Gebruikelijke kwaliteitscontroles van toepassingscodes omvatten linting en statische analyse. Vanuit het oogpunt van GitOps moet u ook dezelfde kwaliteit garanderen voor de resulterende infrastructuur die moet worden geïmplementeerd.

De Dockerfile- en Helm-grafieken van de toepassing kunnen linting gebruiken op een vergelijkbare manier als de toepassing.

Fouten die zijn gevonden tijdens linting, variëren van:
* ONJUIST opgemaakte YAML-bestanden, naar
* Suggesties voor best practice, zoals het instellen van CPU- en geheugenlimieten voor uw toepassing.

> [!NOTE]
> Als u de beste dekking wilt krijgen van Helm-linting in een echte toepassing, moet u waarden vervangen die redelijk vergelijkbaar zijn met die in een echte omgeving.

Fouten die zijn gevonden tijdens het uitvoeren van de pijplijn, worden weergegeven in de sectie met testresultaten van de uitvoering. Hier kunt u het volgende doen:
* Houd de nuttige statistieken voor de fouttypen bij.
* Zoek de eerste door commit waarop ze zijn gedetecteerd.
* Stack-traceerstijlkoppelingen naar de codesecties die de fout hebben veroorzaakt.

Zodra de pijplijn is uitgevoerd, hebt u de kwaliteit van de toepassingscode en de sjabloon die deze gaat implementeren gegarandeerd. U kunt nu de pr goedkeuren en voltooien. De CI wordt opnieuw uitgevoerd en de sjablonen en manifesten worden opnieuw gemaakt voordat de CD-pijplijn wordt activeert.

> [!TIP]
> Vergeet in een echte omgeving niet om vertakkingsbeleid in te stellen om ervoor te zorgen dat de pr uw kwaliteitscontroles doorstaat. Zie het artikel Vertakkingsbeleid instellen [voor meer](/azure/devops/repos/git/branch-policies) informatie.

## <a name="cd-process-approvals"></a>Goedkeuringen voor CD-processen

Een geslaagde CI-pijplijn-run activeert de CD-pijplijn om het implementatieproces te voltooien. Net als bij de eerste keer dat u de CD-pijplijn kunt gebruiken, implementeert u incrementeel in elke omgeving. Deze keer moet u voor de pijplijn elke implementatieomgeving goedkeuren.

1. Keur de implementatie in de omgeving `dev` goed.
1. Zodra de sjabloon en het manifest zijn gewijzigd in de GitOps-repo, maakt de CD-pijplijn een door commit, pusht deze en maakt een pr ter goedkeuring.
1. Open de pr-koppeling in de taak.
1. Controleer de wijzigingen in de GitOps-repo. U ziet het volgende:
   * Wijzigingen in helm-sjablonen op hoog niveau.
   * Kubernetes-manifesten op laag niveau die de onderliggende wijzigingen in de gewenste status tonen.
1. Als alles er goed uitziet, keurt u de aanvraag goed en voltooit u deze.
1. Wacht totdat de installatie is voltooid.
1. Als basistest gaat u naar de toepassingspagina en controleert u of in de stem-app nu Tabs versus Spaties worden weergegeven.
   * Doorsturen van de poort lokaal `kubectl` met behulp van en controleren of de app correct werkt met behulp van: `kubectl port-forward -n dev svc/azure-vote-front 8080:80`
   * Bekijk de Azure Vote-app in uw browser op en controleer of de stemkeuzes zijn http://localhost:8080/ gewijzigd in Tabs vs Spaces. 
1. Herhaal stap 1-7 voor de `stage` omgeving.

Uw implementatie is nu voltooid. Hiermee wordt de CI/CD-werkstroom beëindigd.

## <a name="clean-up-resources"></a>Resources opschonen

Als u deze toepassing verder niet gaat gebruiken, verwijdert u alle resources met de volgende stappen:

1. Verwijder de Azure Arc GitOps-configuratieverbinding:
   ```azurecli
   az k8sconfiguration delete \
   --name cluster-config \
   --cluster-name arc-cicd-cluster \
   --resource-group myResourceGroup \
   --cluster-type connectedClusters
   ```

2. Verwijder de `dev` naamruimte:
   * `kubectl delete namespace dev`

3. Verwijder de `stage` naamruimte:
   * `kubectl delete namespace stage`

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u een volledige CI/CD-werkstroom ingesteld die DevOps implementeert van toepassingsontwikkeling tot implementatie. Wijzigingen in de app activeren automatisch validatie en implementatie, door handmatige goedkeuringen.

Ga naar ons conceptuele artikel voor meer informatie over GitOps en configuraties met kubernetes Azure Arc kubernetes.

> [!div class="nextstepaction"]
> [CI/CD-werkstroom met GitOps - kubernetes Azure Arc ingeschakeld](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-ci-cd)
