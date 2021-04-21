---
title: Een container-exemplaar implementeren met de GitHub-actie
description: Een GitHub-actie configureren die stappen automatiseert voor het bouwen, pushen en implementeren van een container-Azure Container Instances
ms.topic: article
ms.date: 08/20/2020
ms.custom: github-actions-azure, devx-track-azurecli
ms.openlocfilehash: e6a4d9ecff292d79f132f933c36b0030e04f4efa
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771295"
---
# <a name="configure-a-github-action-to-create-a-container-instance"></a>Een GitHub-actie configureren voor het maken van een containerinstantie

[GitHub Actions](https://docs.github.com/en/actions) is een suite met functies in GitHub waarmee u uw werkstromen voor softwareontwikkeling kunt automatiseren op dezelfde plaats waar u code opgeslagen en samenwerkt aan pull-aanvragen en -problemen.

Gebruik de [GitHub Azure Container Instances actie](https://github.com/azure/aci-deploy) Implementeren om de implementatie van één container naar een Azure Container Instances. Met de actie kunt u eigenschappen instellen voor een container-instantie die vergelijkbaar is met die in de [opdracht az container create.][az-container-create]

In dit artikel wordt beschreven hoe u een werkstroom in een GitHub-opslagplaats in kunt stellen die de volgende acties uitvoert:

* Een installatiekopie bouwen op basis van een Dockerfile
* De afbeelding naar een Azure-containerregister pushen
* De containerafbeelding implementeren in een Azure-container-instantie

In dit artikel worden twee manieren beschreven om de werkstroom in te stellen:

* [GitHub-werkstroom configureren:](#configure-github-workflow) maak een werkstroom in een GitHub-opslagplaats met behulp van de actie Implementeren Azure Container Instances acties en andere acties.  
* [CLI-extensie gebruiken:](#use-deploy-to-azure-extension) gebruik de `az container app up` opdracht in de extensie Implementeren in [Azure](https://github.com/Azure/deploy-to-azure-cli-extension) in de Azure CLI. Met deze opdracht stroomlijnt u het maken van de GitHub-werkstroom en implementatiestappen.

> [!IMPORTANT]
> De GitHub-actie voor Azure Container Instances is momenteel in preview. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

## <a name="prerequisites"></a>Vereisten

* **GitHub-account:** maak een account https://github.com op als u er nog geen hebt.
* **Azure CLI:** u kunt de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om de Azure CLI-stappen uit te voeren. Zie [Azure CLI installeren][azure-cli-install] als u de CLI wilt installeren of een upgrade wilt uitvoeren.
* **Azure Container Registry:** als u er nog geen hebt, maakt u een Azure-containerregister in de basic-laag met behulp van [de Azure CLI,](../container-registry/container-registry-get-started-azure-cli.md) [Azure Portal](../container-registry/container-registry-get-started-portal.md)of andere methoden. Noteer de resourcegroep die wordt gebruikt voor de implementatie, die wordt gebruikt voor de GitHub-werkstroom.

## <a name="set-up-repo"></a>Een repo instellen

* Voor de voorbeelden in dit artikel gebruikt u GitHub om de volgende opslagplaats te forken: https://github.com/Azure-Samples/acr-build-helloworld-node

  Deze repo bevat een Dockerfile en bronbestanden om een containerafbeelding van een kleine web-app te maken.

  ![Schermafbeelding van de knop Fork (gemarkeerd) in GitHub](../container-registry/media/container-registry-tutorial-quick-build/quick-build-01-fork.png)

* Zorg ervoor dat Acties is ingeschakeld voor uw opslagplaats. Navigeer naar de gevorkte opslagplaats en selecteer   >  **InstellingenActies**. Zorg **ervoor dat in Machtigingen** voor acties de optie Lokale en externe acties **inschakelen** voor deze opslagplaats is geselecteerd.

## <a name="configure-github-workflow"></a>GitHub-werkstroom configureren

### <a name="create-service-principal-for-azure-authentication"></a>Service-principal maken voor Azure-verificatie

In de GitHub-werkstroom moet u Azure-referenties voor verificatie bij de Azure CLI leveren. In het volgende voorbeeld wordt een service-principal gemaakt met de rol Inzender die is beperkt tot de resourcegroep voor uw containerregister.

Haal eerst de resource-id van uw resourcegroep op. Vervang de naam van uw groep in de volgende [opdracht az group show:][az-group-show]

```azurecli
groupId=$(az group show \
  --name <resource-group-name> \
  --query id --output tsv)
```

Gebruik [az ad sp create-for-rbac om][az-ad-sp-create-for-rbac] de service-principal te maken:

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

Sla de JSON-uitvoer op omdat deze in een latere stap wordt gebruikt. Noteer ook de `clientId` , die u nodig hebt om de service-principal in de volgende sectie bij te werken.

### <a name="update-service-principal-for-registry-authentication"></a>Service-principal bijwerken voor registerverificatie

Werk de referenties van de Azure-service-principal bij om push- en pull-toegang tot uw containerregister toe te staan. Met deze stap kan de GitHub-werkstroom de service-principal gebruiken om te verifiëren bij uw [containerregister](../container-registry/container-registry-auth-service-principal.md) en om een Docker-afbeelding te pushen en op te halen. 

Haal de resource-id van het containerregister op. Vervang de naam van het register in de volgende [opdracht az acr show:][az-acr-show]

```azurecli
registryId=$(az acr show \
  --name <registry-name> \
  --query id --output tsv)
```

Gebruik [az role assignment create om][az-role-assignment-create] de rol AcrPush toe te wijzen, waardoor push- en pull-toegang tot het register wordt gegeven. Vervang de client-id van uw service-principal:

```azurecli
az role assignment create \
  --assignee <ClientId> \
  --scope $registryId \
  --role AcrPush
```

### <a name="save-credentials-to-github-repo"></a>Referenties opslaan in GitHub-opslagplaats

1. Navigeer in de GitHub-gebruikersinterface naar uw gevorkte opslagplaats en selecteer  >  **InstellingenGeheimen.** 

1. Selecteer **Een nieuw geheim toevoegen om** de volgende geheimen toe te voegen:

|Geheim  |Waarde  |
|---------|---------|
|`AZURE_CREDENTIALS`     | De volledige JSON-uitvoer van de stap voor het maken van de service-principal |
|`REGISTRY_LOGIN_SERVER`   | De naam van de aanmeldingsserver van uw register (in kleine letters). Voorbeeld: *myregistry.azurecr.io*        |
|`REGISTRY_USERNAME`     |  De `clientId` van de JSON-uitvoer van het maken van de service-principal       |
|`REGISTRY_PASSWORD`     |  De `clientSecret` van de JSON-uitvoer van het maken van de service-principal |
| `RESOURCE_GROUP` | De naam van de resourcegroep die u hebt gebruikt voor het bereik van de service-principal |

### <a name="create-workflow-file"></a>Werkstroombestand maken

1. Selecteer in de GitHub-gebruikersinterface **Acties**  >  **Nieuwe werkstroom.**
1. Selecteer **Zelf een werkstroom instellen.**
1. Plak **in Nieuw bestand bewerken** de volgende YAML-inhoud om de voorbeeldcode te overschrijven. Accepteer de standaardbestandsnaam `main.yml` of geef een bestandsnaam op die u kiest.
1. Selecteer **Commit starten,** geef desgewenst korte en uitgebreide beschrijvingen van uw door te geven en selecteer **Nieuw bestand commit.**

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

### <a name="validate-workflow"></a>Werkstroom valideren

Nadat u het werkstroombestand hebt door gegeven, wordt de werkstroom geactiveerd. Als u de voortgang van de werkstroom wilt bekijken, gaat **u naar**  >  **ActiesWerkstromen.** 

![Voortgang van werkstroom weergeven](./media/container-instances-github-action/github-action-progress.png)

Zie [Werkstroomuitloopgeschiedenis weergeven](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history) voor informatie over het weergeven van de status en resultaten van elke stap in uw werkstroom. Zie Logboeken weergeven om fouten vast te stellen als de [werkstroom niet is voltooid.](https://docs.github.com/en/actions/managing-workflow-runs/using-workflow-run-logs#viewing-logs-to-diagnose-failures)

Wanneer de werkstroom is voltooid, kunt u informatie krijgen over de container-instantie met de naam *aci-sampleapp* door de opdracht [az container show uit te][az-container-show] voeren. Vervang de naam van uw resourcegroep: 

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

Nadat het exemplaar is ingericht, gaat u naar de FQDN van de container in uw browser om de web-app te bekijken die wordt uitgevoerd.

![Web-app uitvoeren in browser](./media/container-instances-github-action/github-action-container.png)

## <a name="use-deploy-to-azure-extension"></a>Implementeren in Azure-extensie gebruiken

U kunt ook de extensie [Implementeren naar Azure](https://github.com/Azure/deploy-to-azure-cli-extension) in de Azure CLI gebruiken om de werkstroom te configureren. Met `az container app up` de opdracht in de extensie worden invoerparameters van u gebruikt om een werkstroom in te stellen voor implementatie Azure Container Instances. 

De werkstroom die door de Azure CLI is gemaakt, is vergelijkbaar met de werkstroom die u [handmatig kunt maken met behulp van GitHub](#configure-github-workflow).

### <a name="additional-prerequisite"></a>Aanvullende vereiste

Naast de vereisten [en](#prerequisites) de installatie [van de repo](#set-up-repo) voor dit scenario, moet u de extensie Implementeren in  **Azure** voor de Azure CLI installeren.

Voer de [opdracht az extension add][az-extension-add] uit om de extensie te installeren:

```azurecli
az extension add \
  --name deploy-to-azure
```

Zie Extensies gebruiken met Azure CLI voor meer informatie over het zoeken, installeren en beheren [van extensies.](/cli/azure/azure-cli-extensions-overview)

### <a name="run-az-container-app-up"></a>`az container app up` uitvoeren

Als u de opdracht [az container app up wilt][az-container-app-up] uitvoeren, geeft u ten minste het volgende op:

* De naam van uw Azure-containerregister, bijvoorbeeld *myregistry*
* De URL naar uw GitHub-opslagplaats, bijvoorbeeld `https://github.com/<your-GitHub-Id>/acr-build-helloworld-node`

Voorbeeldopdracht:

```azurecli
az container app up \
  --acr myregistry \
  --repository https://github.com/myID/acr-build-helloworld-node
```

### <a name="command-progress"></a>Voortgang van de opdracht

* Geef des gevraagd uw GitHub-referenties op of geef een persoonlijk  [GitHub-toegang token](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token) (PAT) op met opslagplaats- en gebruikersbereiken voor verificatie met uw GitHub-account.  Als u GitHub-referenties op geeft, maakt de opdracht een PAT voor u. Volg aanvullende aanwijzingen om de werkstroom te configureren.

* Met de opdracht maakt u de geheimen van de repo voor de werkstroom:

  * Referenties voor de service-principal voor de Azure CLI
  * Referenties voor toegang tot het Azure-containerregister

* Nadat de opdracht het werkstroombestand naar uw repo heeft door gegeven, wordt de werkstroom geactiveerd. 

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

Als u de werkstroomstatus en resultaten van elke stap in de GitHub-gebruikersinterface wilt bekijken, gaat u [naar Weergave van de werkstroomuitloopgeschiedenis.](https://docs.github.com/en/actions/managing-workflow-runs/viewing-workflow-run-history)

### <a name="validate-workflow"></a>Werkstroom valideren

De werkstroom implementeert een Azure-container-instantie met de basisnaam van uw GitHub-opslagplaats, in dit geval *acr-build-helloworld-node*. Wanneer de werkstroom is voltooid, kunt u informatie krijgen over de container-instantie met de naam *acr-build-helloworld-node* door de [opdracht az container show uit te][az-container-show] voeren. Vervang de naam van uw resourcegroep: 

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

Nadat het exemplaar is ingericht, gaat u naar de FQDN van de container in uw browser om de web-app te bekijken die wordt uitgevoerd.

## <a name="clean-up-resources"></a>Resources opschonen

Stop de containerinstantie met de opdracht [az container delete][az-container-delete]:

```azurecli
az container delete \
  --name <instance-name>
  --resource-group <resource-group-name>
```

Als u de resourcegroep en alle resources in de groep wilt verwijderen, moet u de [opdracht az group delete][az-group-delete] uitvoeren:

```azurecli
az group delete \
  --name <resource-group-name>
```

## <a name="next-steps"></a>Volgende stappen

Blader door [de GitHub Marketplace](https://github.com/marketplace?type=actions) voor meer acties om uw ontwikkelingswerkstroom te automatiseren


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az_group_show
[az-group-delete]: /cli/azure/group#az_group_delete
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-container-create]: /cli/azure/container#az_container_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-container-show]: /cli/azure/container#az_container_show
[az-container-delete]: /cli/azure/container#az_container_delete
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-container-app-up]: /cli/azure/ext/deploy-to-azure/container/app#ext-deploy-to-azure-az-container-app-up
