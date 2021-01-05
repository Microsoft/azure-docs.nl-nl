---
title: Container exemplaar implementeren op GitHub-actie
description: Configureer een GitHub-actie waarmee de stappen voor het maken, pushen en implementeren van een container installatie kopie naar Azure Container Instances worden geautomatiseerd.
ms.topic: article
ms.date: 08/20/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: c6c030e05953dc98c82c573704018c3b482d2fea
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97814275"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Een GitHub-actie configureren voor het maken van een containerinstantie

[Github-acties](https://docs.github.com/en/free-pro-team@latest/actions) is een reeks functies in github voor het automatiseren van uw werk stromen voor software ontwikkeling op dezelfde locatie waar u code opslaat en samen werken aan pull-aanvragen en-problemen.

Gebruik de actie [implementeren naar Azure container instances](https://github.com/azure/aci-deploy) github voor het automatiseren van de implementatie van één Container naar Azure container instances. Met deze actie kunt u eigenschappen instellen voor een container exemplaar dat vergelijkbaar is met die in de opdracht [AZ container Create][az-container-create] .

In dit artikel wordt beschreven hoe u een werk stroom instelt in een GitHub-opslag plaats die de volgende acties uitvoert:

* Een installatiekopie bouwen op basis van een Dockerfile
* De installatie kopie naar een Azure container Registry pushen
* De container installatie kopie implementeren in een Azure-container exemplaar

In dit artikel ziet u twee manieren om de werk stroom in te stellen:

* [Github-werk stroom configureren](#configure-github-workflow) : Maak een werk stroom in een github-opslag plaats met de actie implementeren naar Azure container instances en andere acties.  
* [Cli-extensie gebruiken](#use-deploy-to-azure-extension) : gebruik de `az container app up` opdracht in de uitbrei ding [implementeren naar Azure](https://github.com/Azure/deploy-to-azure-cli-extension) in de Azure cli. Met deze opdracht stroomlijnt u het maken van de GitHub-werk stroom en implementaties tappen.

> [!IMPORTANT]
> De GitHub-actie voor Azure Container Instances is momenteel beschikbaar als preview-versie. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="prerequisites"></a>Vereisten

* **Github-account** : Maak een account op https://github.com Als u er nog geen hebt.
* **Azure cli** : u kunt de Azure Cloud shell of een lokale installatie van de Azure CLI gebruiken om de stappen van Azure CLI uit te voeren. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.
* **Azure container Registry** : als u er nog geen hebt, maakt u een Azure-container register in de Basic-laag met behulp van de [Azure cli](../container-registry/container-registry-get-started-azure-cli.md), [Azure Portal](../container-registry/container-registry-get-started-portal.md)of een andere methode. Noteer de resource groep die wordt gebruikt voor de implementatie, die wordt gebruikt voor de GitHub-werk stroom.

## <a name="set-up-repo"></a>Opslag plaats instellen

* Voor de voor beelden in dit artikel gebruikt u GitHub om de volgende opslag plaats te splitsen: https://github.com/Azure-Samples/acr-build-helloworld-node

  Deze opslag plaats bevat een Dockerfile en bron bestanden voor het maken van een container installatie kopie van een kleine web-app.

  ![Schermafbeelding van de knop Fork (gemarkeerd) in GitHub](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Zorg ervoor dat acties zijn ingeschakeld voor uw opslag plaats. Navigeer naar uw gevorkte opslag plaats en selecteer **instellingen**  >  **acties**. Zorg ervoor dat in **acties machtigingen** **lokale acties en derden inschakelen voor deze opslag plaats** is geselecteerd.

## <a name="configure-github-workflow"></a>GitHub-werk stroom configureren

### <a name="create-service-principal-for-azure-authentication"></a>Een service-principal maken voor Azure-verificatie

In de GitHub-werk stroom moet u Azure-referenties opgeven om te verifiëren bij de Azure CLI. In het volgende voor beeld wordt een service-principal gemaakt met de rol Inzender in het bereik van de resource groep voor uw container register.

Haal eerst de resource-ID van de resource groep op. Vervang de naam van uw groep in de volgende opdracht [AZ Group show][az-group-show] :

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Gebruik [AZ AD SP create-for-RBAC][az-ad-sp-create-for-rbac] om de service-principal te maken:

```azurecli
az ad sp create-for-rbac \
  --scope $groupId \
  --role Contributor \
  --sdk-auth
```

De uitvoer ziet er ongeveer zo uit:

```json
{
  "clientId": "xxxx6ddc-xxxx-xxxx-xxx-ef78a99dxxxx",
  "clientSecret": "xxxx79dc-xxxx-xxxx-xxxx-aaaaaec5xxxx",
  "subscriptionId": "xxxx251c-xxxx-xxxx-xxxx-bf99a306xxxx",
  "tenantId": "xxxx88bf-xxxx-xxxx-xxxx-2d7cd011xxxx",
  "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
  "resourceManagerEndpointUrl": "https://management.azure.com/",
  "activeDirectoryGraphResourceId": "https://graph.windows.net/",
  "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
  "galleryEndpointUrl": "https://gallery.azure.com/",
  "managementEndpointUrl": "https://management.core.windows.net/"
}
```

Sla de JSON-uitvoer op omdat deze wordt gebruikt in een latere stap. Let ook `clientId` op de, die u moet uitvoeren om de service-principal bij te werken in de volgende sectie.

### <a name="update-service-principal-for-registry-authentication"></a>Service-Principal bijwerken voor register verificatie

Werk de referenties van de Azure-Service-Principal bij om push-en pull-toegang tot uw container register mogelijk te maken. Met deze stap wordt de GitHub-werk stroom ingeschakeld om de service-principal te [verifiëren met het container register](../container-registry/container-registry-auth-service-principal.md) en om een docker-installatie kopie te pushen en te verzamelen. 

De resource-ID van het container register ophalen. Vervang de naam van het REGI ster in de volgende [AZ ACR show][az-acr-show] opdracht:

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Gebruik [AZ Role Assignment Create][az-role-assignment-create] om de AcrPush-rol toe te wijzen. Dit biedt push-en pull-toegang tot het REGI ster. Vervang de client-ID van de Service-Principal door:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Referenties opslaan in GitHub opslag plaats

1. Ga in de GitHub-gebruikers interface naar uw gevorkte opslag plaats en selecteer **instellingen**  >  **geheimen**. 

1. Selecteer **een nieuw geheim toevoegen** om de volgende geheimen toe te voegen:

|Geheim  |Waarde  |
|---------|---------|
|`AZURE_CREDENTIALS`     | De volledige JSON-uitvoer van de stap voor het maken van de Service-Principal |
|`REGISTRY_LOGIN_SERVER`   | De naam van de aanmeldings server van het REGI ster (alle kleine letters). Voor beeld: *myregistry.azurecr.io*        |
|`REGISTRY_USERNAME`     |  De `clientId` van de JSON-uitvoer van de service-principal maken       |
|`REGISTRY_PASSWORD`     |  De `clientSecret` van de JSON-uitvoer van de service-principal maken |
| `RESOURCE_GROUP` | De naam van de resource groep die u hebt gebruikt om de service-principal te bereiken |

### <a name="create-workflow-file"></a>Werk stroom bestand maken

1. Selecteer in de gebruikers interface van github de optie **acties**  >  **nieuwe werk stroom**.
1. Selecteer **zelf een werk stroom instellen**.
1. Plak in **nieuw bestand bewerken** de volgende YAML-inhoud om de voorbeeld code te overschrijven. Accepteer de standaard bestandsnaam `main.yml` of geef een bestands naam op die u kiest.
1. Selecteer **door voeren starten**, geef eventueel korte en uitgebreide beschrijvingen van uw door Voer op en selecteer **nieuw bestand door voeren**.

```yml
on: [push]
name: Linux_Container_Workflow

jobs:
    build-and-deploy:
        runs-on: ubuntu-latest
        steps:
        # checkout the repo
        - name: 'Checkout GitHub Action'
          uses: actions/checkout@main
          
        - name: 'Login via Azure CLI'
          uses: azure/login@v1
          with:
            creds: ${{ secrets.AZURE_CREDENTIALS }}
        
        - name: 'Build and push image'
          uses: azure/docker-login@v1
          with:
            login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            username: ${{ secrets.REGISTRY_USERNAME }}
            password: ${{ secrets.REGISTRY_PASSWORD }}
        - run: |
            docker build . -t ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            docker push ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}

        - name: 'Deploy to Azure Container Instances'
          uses: 'azure/aci-deploy@v1'
          with:
            resource-group: ${{ secrets.RESOURCE_GROUP }}
            dns-name-label: ${{ secrets.RESOURCE_GROUP }}${{ github.run_number }}
            image: ${{ secrets.REGISTRY_LOGIN_SERVER }}/sampleapp:${{ github.sha }}
            registry-login-server: ${{ secrets.REGISTRY_LOGIN_SERVER }}
            registry-username: ${{ secrets.REGISTRY_USERNAME }}
            registry-password: ${{ secrets.REGISTRY_PASSWORD }}
            name: aci-sampleapp
            location: 'west us'
```

### <a name="validate-workflow"></a>Werk stroom valideren

Nadat u het werk stroom bestand hebt doorgevoerd, wordt de werk stroom geactiveerd. Als u de voortgang van de werk stroom wilt bekijken, gaat u naar **Action**  >  **workflows**. 

![Werk stroom voortgang weer geven](./media/container-instances-github-action/github-action-progress.png)

Zie de [uitvoerings geschiedenis van de werk stroom](https://docs.github.com/en/free-pro-team@latest/actions/managing-workflow-runs/viewing-workflow-run-history) bekijken voor informatie over het weer geven van de status en de resultaten van elke stap in de werk stroom. Als de werk stroom niet is voltooid, raadpleegt [u logboeken weer geven om fouten te onderzoeken](https://docs.github.com/en/free-pro-team@latest/actions/managing-workflow-runs/using-workflow-run-logs#viewing-logs-to-diagnose-failures).

Wanneer de werk stroom is voltooid, haalt u informatie op over het container exemplaar met de naam *ACI-SampleApp* door de opdracht [AZ container show][az-container-show] uit te voeren. Vervang de naam van uw resource groep: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name aci-sampleapp \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

De uitvoer ziet er ongeveer zo uit:

```console
FQDN                                   ProvisioningState
---------------------------------      -------------------
aci-action01.westus.azurecontainer.io  Succeeded
```

Nadat het exemplaar is ingericht, gaat u naar de FQDN van de container in uw browser om de actieve web-app weer te geven.

![De web-app wordt uitgevoerd in de browser](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>De extensie implementeren naar Azure gebruiken

U kunt ook de [extensie implementeren naar Azure](https://github.com/Azure/deploy-to-azure-cli-extension) gebruiken in de Azure CLI om de werk stroom te configureren. De `az container app up` opdracht in de uitbrei ding krijgt invoer parameters van u om een werk stroom in te stellen om te implementeren in azure container instances. 

De werk stroom die door de Azure CLI is gemaakt, is vergelijkbaar met de werk stroom die u [hand matig kunt maken met behulp van github](#configure-github-workflow).

### <a name="additional-prerequisite"></a>Aanvullende vereiste

Naast de [vereisten](#prerequisites) en [opslag plaats-instellingen](#set-up-repo) voor dit scenario, moet u de  **uitbrei ding implementeren naar Azure** voor de Azure cli installeren.

Voer de opdracht [AZ extension add][az-extension-add] uit om de extensie te installeren:

```azurecli
az extension add \
  --name deploy-to-azure
```

Zie [extensies gebruiken met Azure cli](/cli/azure/azure-cli-extensions-overview)voor meer informatie over het zoeken, installeren en beheren van uitbrei dingen.

### <a name="run-az-container-app-up"></a>`az container app up` uitvoeren

Als u de opdracht [AZ container app up][az-container-app-up] wilt uitvoeren, geeft u Mini maal:

* De naam van uw Azure container Registry, bijvoorbeeld *myregistry*
* De URL naar uw GitHub-opslag plaats, bijvoorbeeld `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Voorbeeldopdracht:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Opdracht voortgang

* Geef desgevraagd uw GitHub-referenties op of geef een [github Personal Access token](https://docs.github.com/en/free-pro-team@latest/github/authenticating-to-github/creating-a-personal-access-token) (Pat) op die *opslag plaats* en *beveiligingsbereiken heeft om te verifiëren* met uw github-account. Als u GitHub-referenties opgeeft, maakt de opdracht een PAT voor u. Volg de extra prompts om de werk stroom te configureren.

* Met deze opdracht maakt u opslag plaats geheimen voor de werk stroom:

  * Referenties van de service-principal voor de Azure CLI
  * Referenties voor toegang tot het Azure container Registry

* Nadat de opdracht het werk stroom bestand heeft doorgevoerd in uw opslag plaats, wordt de werk stroom geactiveerd. 

De uitvoer ziet er ongeveer zo uit:

```console
[...]
Checking in file github/workflows/main.yml in the Github repository myid/acr-build-helloworld-node
Creating workflow...
GitHub Action Workflow has been created - https://github.com/myid/acr-build-helloworld-node/runs/515192398
GitHub workflow completed.
Workflow succeeded
Your app is deployed at:  http://acr-build-helloworld-node.eastus.azurecontainer.io:8080/
```

Als u de werk stroom status en resultaten van elke stap in de GitHub-gebruikers interface wilt weer geven, raadpleegt u de [werk stroom uitvoerings geschiedenis weer geven](https://docs.github.com/en/free-pro-team@latest/actions/managing-workflow-runs/viewing-workflow-run-history).

### <a name="validate-workflow"></a>Werk stroom valideren

De werk stroom implementeert een Azure-container instantie met de basis naam van uw GitHub-opslag plaats, in dit geval *ACR-build-HelloWorld-node*. Wanneer de werk stroom is voltooid, haalt u informatie op over de container instantie met de naam *ACR-build-HelloWorld-node* door de opdracht [AZ container show][az-container-show] uit te voeren. Vervang de naam van uw resource groep: 

```azurecli
az container show \
  --resource-group <resource-group-name> \
  --name acr-build-helloworld-node \
  --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" \
  --output table
```

De uitvoer ziet er ongeveer zo uit:

```console
FQDN                                                   ProvisioningState
---------------------------------                      -------------------
acr-build-helloworld-node.westus.azurecontainer.io     Succeeded
```

Nadat het exemplaar is ingericht, gaat u naar de FQDN van de container in uw browser om de actieve web-app weer te geven.

## <a name="clean-up-resources"></a>Resources opschonen

Stop de containerinstantie met de opdracht [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Als u de resource groep en alle resources erin wilt verwijderen, voert u de opdracht [AZ Group delete][az-group-delete] uit:

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Volgende stappen

Blader door de [github Marketplace](https://github.com/marketplace?type=actions) voor meer acties voor het automatiseren van uw werk stroom voor ontwikkel aars


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-container-create]: /cli/azure/container#az-container-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-container-show]: /cli/azure/container#az-container-show
[az-container-delete]: /cli/azure/container#az-container-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
