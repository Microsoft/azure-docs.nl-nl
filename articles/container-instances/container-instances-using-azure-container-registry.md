---
title: Container-afbeelding implementeren vanuit Azure Container Registry
description: Meer informatie over het implementeren van containers in Azure Container Instances door containerafbeeldingen op te halen uit een Azure-containerregister.
services: container-instances
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7aba107ac58538245c16f581afa2c493f546ca01
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786941"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Azure Container Instances implementeren vanuit Azure Container Registry

[Azure Container Registry](../container-registry/container-registry-intro.md) is een op Azure gebaseerde, beheerde containerregisterservice die wordt gebruikt voor het opslaan van persoonlijke Docker-containerafbeeldingen. In dit artikel wordt beschreven hoe u containerafbeeldingen op pullt die zijn opgeslagen in een Azure-containerregister bij het implementeren naar Azure Container Instances. Een aanbevolen manier om registertoegang te configureren, is door een Azure Active Directory service-principal en wachtwoord te maken en de aanmeldingsreferenties op te slaan in een Azure-sleutelkluis.

## <a name="prerequisites"></a>Vereisten

**Azure Container Registry:** u hebt een Azure-containerregister en ten minste één containerafbeelding in het register nodig om de stappen in dit artikel uit te voeren. Zie Een containerregister maken met behulp van de Azure CLI als u een [register nodig hebt.](../container-registry/container-registry-get-started-azure-cli.md)

**Azure CLI:** In de opdrachtregelvoorbeelden in dit artikel wordt [de Azure CLI](/cli/azure/) gebruikt en deze zijn opgemaakt voor de Bash-shell. U kunt [de Azure CLI lokaal](/cli/azure/install-azure-cli) installeren of de Azure Cloud Shell. [][cloud-shell-bash]

## <a name="limitations"></a>Beperkingen

* U kunt zich niet verifiëren bij Azure Container Registry om installatie afbeeldingen [](container-instances-managed-identity.md) op te halen tijdens de implementatie van een containergroep met behulp van een beheerde identiteit die is geconfigureerd in dezelfde containergroep.
* U kunt op dit moment geen Azure Container Registry [uit](../container-registry/container-registry-vnet.md) een Azure-Virtual Network.

## <a name="configure-registry-authentication"></a>Registerverificatie configureren

In een productiescenario waarin u toegang biedt tot headless services en toepassingen, is het raadzaam om registertoegang te configureren met behulp van een [service-principal.](../container-registry/container-registry-auth-service-principal.md) Met een service-principal kunt u op rollen gebaseerd toegangsbeheer [van Azure (Azure RBAC) bieden](../container-registry/container-registry-roles.md) voor uw containerafbeeldingen. U kunt bijvoorbeeld een service-principal configureren met alleen pull-toegang tot een register.

Azure Container Registry biedt aanvullende [verificatieopties.](../container-registry/container-registry-authentication.md)

In de volgende sectie maakt u een Azure-sleutelkluis en een service-principal en slaan u de referenties van de service-principal op in de kluis.

### <a name="create-key-vault"></a>Sleutelkluis maken

Als u nog geen kluis hebt in [Azure Key Vault](../key-vault/general/overview.md), kunt u met de volgende opdrachten één maken met de Azure CLI.

Werk de variabele bij met de naam van een bestaande resourcegroep waarin u de sleutelkluis wilt maken `RES_GROUP` en met de naam van uw `ACR_NAME` containerregister. Voor de beknoptheid gaan opdrachten in dit artikel ervan uit dat uw register, sleutelkluis en container-exemplaren allemaal in dezelfde resourcegroep zijn gemaakt.

 Geef een naam op voor de nieuwe sleutelkluis in `AKV_NAME` . De kluisnaam moet uniek zijn binnen Azure en moet 3 tot 24 alfanumerieke tekens lang zijn, beginnen met een letter, eindigen met een letter of cijfer en mogen geen opeenvolgende afbreekstreeepten bevatten.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Service-principal maken en referenties opslaan

Maak nu een service-principal en sla de referenties op in uw sleutelkluis.

De volgende opdracht maakt gebruik [van az ad sp create-for-rbac om de service-principal][az-ad-sp-create-for-rbac] te maken en az [keyvault secret set][az-keyvault-secret-set] om het wachtwoord van de service-principal op te slaan in de kluis. 

```azurecli
# Create service principal, store its password in vault (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name http://$ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

Het argument `--role` in de voorgaande opdracht configureert de service-principal met de rol *acrpull*, die de principal alleen-push-toegang tot het register geeft. Als u zowel push-als pull-toegang wilt geven, wijzigt u het argument `--role` in *acrpush*.

Sla vervolgens de *appId* van de service-principal  op in de kluis. Dit is de gebruikersnaam die u door geeft aan Azure Container Registry voor verificatie.

```azurecli
# Store service principal ID in vault (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

U hebt een Azure-sleutelkluis gemaakt en er twee geheimen in opgeslagen:

* `$ACR_NAME-pull-usr`: De service-principal-id, voor gebruik als de **gebruikersnaam** van het containerregister.
* `$ACR_NAME-pull-pwd`: Het service-principal-wachtwoord, voor gebruik als het **wachtwoord** van het containerregister.

U kunt nu op naam naar deze geheime gegevens verwijzen wanneer u of uw toepassingen en services installatiekopieën uit het register halen.

## <a name="deploy-container-with-azure-cli"></a>Container implementeren met Azure CLI

Nu de referenties van de service-principal zijn opgeslagen in Azure Key Vault geheimen, kunnen uw toepassingen en services deze gebruiken voor toegang tot uw privéregister.

Haal eerst de naam van de aanmeldingsserver van het register op met behulp van [de opdracht az acr show.][az-acr-show] De naam van de aanmeldingsserver is in kleine letters en vergelijkbaar met `myregistry.azurecr.io` .

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Voer de volgende op [az container create][az-container-create] uit om een containerinstantie te implementeren. De opdracht gebruikt de referenties van de service-principal die zijn opgeslagen in Azure Key Vault om te verifiëren bij uw containerregister en gaat ervan uit dat u de [aci-helloworld-afbeelding](container-instances-quickstart.md) eerder naar het register hebt ge pusht. Werk de waarde bij als u een andere afbeelding van het `--image` register wilt gebruiken.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_LOGIN_SERVER/aci-helloworld:v1 \
    --registry-login-server $ACR_LOGIN_SERVER \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

De waarde moet uniek zijn binnen Azure, dus met de voorgaande opdracht wordt een willekeurig getal aan het `--dns-name-label` DNS-naamlabel van de container toevoegen. De uitvoer van de opdracht geeft bijvoorbeeld de Fully Qualified Domain Name (FQDN) van de container weer:

```output
"aci-demo-25007.eastus.azurecontainer.io"
```

Zodra de container is gestart, kunt u in uw browser naar de FQDN navigeren om te controleren of de toepassing wordt uitgevoerd.

## <a name="deploy-with-azure-resource-manager-template"></a>Implementeren met Azure Resource Manager sjabloon

U kunt de eigenschappen van uw Azure-containerregister opgeven in een Azure Resource Manager door de eigenschap op te geven in de `imageRegistryCredentials` definitie van de containergroep. U kunt bijvoorbeeld de registerreferenties rechtstreeks opgeven:

```JSON
[...]
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
[...]
```

Zie de sjabloonverwijzing Resource Manager volledige instellingen [voor de containergroep.](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups)    

Zie Use Azure Key Vault to pass secure parameter value during deployment (Beveiligde [parameterwaarde](../azure-resource-manager/templates/key-vault-parameter.md)doorgeven tijdens de implementatie) voor meer informatie over het verwijzen naar Azure Key Vault geheimen in een Resource Manager sjabloon.

## <a name="deploy-with-azure-portal"></a>Implementeren met Azure Portal

Als u containerafbeeldingen in een Azure-containerregister onderhoudt, kunt u eenvoudig een container in Azure Container Instances maken met behulp van Azure Portal. Wanneer u de portal gebruikt om een container-exemplaar te implementeren vanuit een containerregister, moet u het beheerdersaccount van het [register inschakelen.](../container-registry/container-registry-authentication.md#admin-account) Het beheerdersaccount is ontworpen voor één gebruiker om toegang te krijgen tot het register, voornamelijk voor testdoeleinden. 

1. Ga in de Azure-portal naar uw containerregister.

1. Als u wilt controleren of het beheerdersaccount is ingeschakeld, selecteert u **Toegangssleutels** en selecteert u onder Beheerder **de** optie **Inschakelen.**

1. Selecteer **Opslagplaatsen,** selecteer vervolgens de opslagplaats waar u wilt implementeren, klik met de rechtermuisknop op de tag voor de containerafbeelding die u wilt implementeren en selecteer **Exemplaar uitvoeren.**

    !['Exemplaar uitvoeren' in Azure Container Registry in de Azure Portal][acr-runinstance-contextmenu]

1. Voer een naam in voor de container en een naam voor de resourcegroep. U kunt ook de standaardwaarden wijzigen als u wilt.

    ![Menu maken voor Azure Container Instances][acr-create-deeplink]

1. Zodra de implementatie is voltooid, kunt u naar de containergroep navigeren vanuit het deelvenster Meldingen om het IP-adres en andere eigenschappen te vinden.

    ![Detailweergave voor Azure Container Instances containergroep][aci-detailsview]

## <a name="next-steps"></a>Volgende stappen

Zie Verifiëren met een [Azure-containerregister](../container-registry/container-registry-authentication.md)Azure Container Registry meer informatie over verificatie.

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-try-it]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-container-create]: /cli/azure/container#az_container_create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az_keyvault_secret_set
