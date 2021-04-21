---
title: Verifiëren met beheerde identiteit
description: Geef toegang tot installatie afbeeldingen in uw privécontainerregister met behulp van een door de gebruiker toegewezen of door het systeem toegewezen beheerde Azure-identiteit.
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 213f49356fdc2444f8bc2cb4635e96015aff0a61
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781537"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Een beheerde Azure-identiteit gebruiken om te verifiëren bij een Azure-containerregister 

Gebruik een [beheerde identiteit voor Azure-resources om](../active-directory/managed-identities-azure-resources/overview.md) vanuit een andere Azure-resource te verifiëren bij een Azure-containerregister, zonder dat u registerreferenties hoeft op te geven of te beheren. Stel bijvoorbeeld een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit in op een Linux-VM voor toegang tot containerinstallatie afbeeldingen uit uw containerregister, net zo eenvoudig als u een openbaar register gebruikt. U kunt ook een cluster Azure Kubernetes Service om de beheerde identiteit [te](../aks/use-managed-identity.md) gebruiken om containerafbeeldingen op te halen Azure Container Registry podimplementaties.

In dit artikel vindt u meer informatie over beheerde identiteiten en het volgende:

> [!div class="checklist"]
> * Een door de gebruiker toegewezen of door het systeem toegewezen identiteit inschakelen op een Azure-VM
> * De identiteit toegang verlenen tot een Azure-containerregister
> * De beheerde identiteit gebruiken om toegang te krijgen tot het register en een containerafbeelding op te halen 

Voor het maken van de Azure-resources moet u voor dit artikel versie 2.0.55 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Als u een containerregister wilt instellen en er een containerafbeelding naar wilt pushen, moet Docker ook lokaal zijn geïnstalleerd. Docker biedt pakketten die eenvoudig Docker configureren op elk [macOS][docker-mac]-, [Windows][docker-windows]- of [Linux][docker-linux]-systeem.

## <a name="why-use-a-managed-identity"></a>Waarom een beheerde identiteit gebruiken?

Als u niet bekend bent met de functie voor beheerde identiteiten voor Azure-resources, raadpleegt u dit [overzicht](../active-directory/managed-identities-azure-resources/overview.md).

Nadat u geselecteerde Azure-resources met een beheerde identiteit hebt ingesteld, geeft u de identiteit de toegankelijkheid tot een andere resource, net als elke beveiligingsprincipaal. Wijs bijvoorbeeld een beheerde identiteit een rol toe met pull-, push- en pull-machtigingen of andere machtigingen voor een privéregister in Azure. (Zie Azure Container Registry en machtigingen voor een volledige lijst [met registerrollen.)](container-registry-roles.md) U kunt een identiteit toegang geven tot een of meer resources.

Gebruik vervolgens de identiteit om te verifiëren bij een service die [Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)ondersteunt, zonder referenties in uw code. Kies hoe u wilt verifiëren met behulp van de beheerde identiteit, afhankelijk van uw scenario. Als u de identiteit wilt gebruiken voor toegang tot een Azure-containerregister vanaf een virtuele machine, verifieert u met Azure Resource Manager. 

> [!NOTE]
> Op dit moment kunnen services zoals Azure Web App for Containers of Azure Container Instances hun beheerde identiteit niet gebruiken voor verificatie met Azure Container Registry bij het binnenhalen van een containerafbeelding om de containerresource zelf te implementeren. De identiteit is alleen beschikbaar nadat de container wordt uitgevoerd. Als u deze resources wilt implementeren met behulp van Azure Container Registry, wordt een andere verificatiemethode, zoals [service-principal,](container-registry-auth-service-principal.md) aanbevolen.

## <a name="create-a-container-registry"></a>Een containerregister maken

Als u nog geen Azure-containerregister hebt, maakt u een register en pusht u er een voorbeeldcontainer-afbeelding naar. Zie [Quickstart: Een privécontainerregister](container-registry-get-started-azure-cli.md)maken met behulp van de Azure CLI voor stappen.

In dit artikel wordt ervan uitgenomen dat u de `aci-helloworld:v1` container-afbeelding hebt opgeslagen in uw register. In de voorbeelden wordt de registernaam *myContainerRegistry gebruikt.* Vervang in latere stappen door uw eigen register- en afbeeldingsnamen.

## <a name="create-a-docker-enabled-vm"></a>Een voor Docker ingeschakelde VM maken

Maak een virtuele Ubuntu-machine met Docker-functie. U moet ook de [Azure CLI installeren](/cli/azure/install-azure-cli) op de virtuele machine. Als u al een virtuele Azure-machine hebt, kunt u deze stap overslaan om de virtuele machine te maken.

Implementeer een standaard virtuele Ubuntu Azure-machine [met az vm create][az-vm-create]. In het volgende voorbeeld wordt een VM met de *naam myDockerVM gemaakt* in een bestaande resourcegroep met de *naam myResourceGroup:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Het duurt enkele minuten voordat de virtuele machine wordt gemaakt. Wanneer de opdracht is voltooid, noteert u de `publicIpAddress` die wordt weergegeven door de Azure CLI. Gebruik dit adres om SSH-verbindingen met de VM te maken.

### <a name="install-docker-on-the-vm"></a>Docker installeren op de VM

Nadat de VM wordt uitgevoerd, maakt u een SSH-verbinding met de VM. Vervang *publicIpAddress door* het openbare IP-adres van uw VM.

```bash
ssh azureuser@publicIpAddress
```

Voer de volgende opdracht uit om Docker op de VM te installeren:

```bash
sudo apt update
sudo apt install docker.io -y
```

Voer na de installatie de volgende opdracht uit om te controleren of Docker correct wordt uitgevoerd op de VM:

```bash
sudo docker run -it mcr.microsoft.com/hello-world
```

Uitvoer:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Azure-CLI installeren

Volg de stappen in [Azure CLI installeren met apt om](/cli/azure/install-azure-cli-apt) de Azure CLI te installeren op uw virtuele Ubuntu-machine. Voor dit artikel moet u versie 2.0.55 of hoger installeren.

Sluit de SSH-sessie af.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Voorbeeld 1: Toegang met een door de gebruiker toegewezen identiteit

### <a name="create-an-identity"></a>Een identiteit maken

Maak een identiteit in uw abonnement met behulp van [de opdracht az identity create.](/cli/azure/identity#az_identity_create) U kunt dezelfde resourcegroep gebruiken die u eerder hebt gebruikt om het containerregister, de virtuele machine of een andere te maken.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Als u de identiteit in de volgende stappen wilt configureren, gebruikt u de opdracht [az identity show][az_identity_show] om de resource-id en service-principal-id van de identiteit op te slaan in variabelen.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Omdat u de id van de identiteit in een latere stap nodig hebt bij het aanmelden bij de CLI vanaf uw virtuele machine, moet u de volgende waarde tonen:

```bash
echo $userID
```

De id heeft de volgende vorm:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>De VM configureren met de identiteit

Met de [volgende opdracht az vm identity assign][az-vm-identity-assign] wordt uw Docker-VM geconfigureerd met de door de gebruiker toegewezen identiteit:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Identiteit toegang verlenen tot het containerregister

Configureer nu de identiteit voor toegang tot uw containerregister. Gebruik eerst de [opdracht az acr show om][az-acr-show] de resource-id van het register op te halen:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Gebruik de [opdracht az role assignment create om][az-role-assignment-create] de rol AcrPull toe te wijzen aan het register. Deze rol biedt [pull-machtigingen](container-registry-roles.md) voor het register. Wijs de rol ACRPush toe om zowel pull- als push-machtigingen te bieden.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>De identiteit gebruiken voor toegang tot het register

Ga via SSH naar de virtuele Docker-machine die is geconfigureerd met de identiteit. Voer de volgende Azure CLI-opdrachten uit met behulp van de Azure CLI die op de VM is geïnstalleerd.

Verifieert u eerst bij de Azure CLI [met az login][az-login], met behulp van de identiteit die u hebt geconfigureerd op de VM. Vervang `<userID>` voor de id van de identiteit die u in een vorige stap hebt opgehaald. 

```azurecli
az login --identity --username <userID>
```

Verifieert u vervolgens bij het register met [az acr login][az-acr-login]. Wanneer u deze opdracht gebruikt, gebruikt de CLI het Active Directory-token dat u hebt gemaakt toen u de sessie met het `az login` containerregister naadloos verifieert. (Afhankelijk van de installatie van uw VM moet u mogelijk deze opdracht en docker-opdrachten uitvoeren met `sudo` .)

```azurecli
az acr login --name myContainerRegistry
```

Als het goed is, ziet u `Login succeeded` een bericht. Vervolgens kunt u opdrachten `docker` uitvoeren zonder referenties op te geven. Voer bijvoorbeeld [docker pull uit om de][docker-pull] -afbeelding op te halen en de naam van de aanmeldingsserver van het register op te `aci-helloworld:v1` geven. De naam van de aanmeldingsserver bestaat uit de naam van het containerregister (in kleine letters), gevolgd door `.azurecr.io` , bijvoorbeeld `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Voorbeeld 2: Toegang met een door het systeem toegewezen identiteit

### <a name="configure-the-vm-with-a-system-managed-identity"></a>De VM configureren met een door het systeem beheerde identiteit

Met de [volgende opdracht az vm identity assign][az-vm-identity-assign] configureert u uw Docker-VM met een door het systeem toegewezen identiteit:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Gebruik de [opdracht az vm show][az-vm-show] om een variabele in te stellen op de waarde van (de service-principal-id) van de identiteit van de `principalId` VM, voor gebruik in latere stappen.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Identiteit toegang verlenen tot het containerregister

Configureer nu de identiteit voor toegang tot uw containerregister. Gebruik eerst de [opdracht az acr show][az-acr-show] om de resource-id van het register op te halen:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Gebruik de [opdracht az role assignment create][az-role-assignment-create] om de rol AcrPull toe te wijzen aan de identiteit. Deze rol biedt [pull-machtigingen](container-registry-roles.md) voor het register. Wijs de rol ACRPush toe om zowel pull- als push-machtigingen te bieden.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>De identiteit gebruiken voor toegang tot het register

Gebruik SSH in de virtuele Docker-machine die is geconfigureerd met de identiteit. Voer de volgende Azure CLI-opdrachten uit met behulp van de Azure CLI die op de VM is geïnstalleerd.

Verifieert eerst de Azure CLI met [az login][az-login], met behulp van de door het systeem toegewezen identiteit op de VM.

```azurecli
az login --identity
```

Verifieert u vervolgens bij het register met [az acr login][az-acr-login]. Wanneer u deze opdracht gebruikt, gebruikt de CLI het Active Directory-token dat is gemaakt tijdens het uitvoeren om uw sessie met het `az login` containerregister naadloos te verifiëren. (Afhankelijk van de installatie van uw VM moet u deze opdracht mogelijk uitvoeren en docker-opdrachten met `sudo` .)

```azurecli
az acr login --name myContainerRegistry
```

Als het goed is, wordt er een `Login succeeded` bericht weergegeven. Vervolgens kunt u opdrachten `docker` uitvoeren zonder referenties op te geven. Voer bijvoorbeeld [docker pull uit om de][docker-pull] -afbeelding op te `aci-helloworld:v1` halen, en geef de naam van de aanmeldingsserver van het register op. De naam van de aanmeldingsserver bestaat uit de naam van het containerregister (in kleine letters), gevolgd door `.azurecr.io` , bijvoorbeeld `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over het gebruik van beheerde identiteiten met Azure Container Registry en hoe u het volgende kunt doen:

> [!div class="checklist"]
> * Een door de gebruiker toegewezen of door het systeem toegewezen identiteit inschakelen in een Azure-VM
> * De identiteit toegang verlenen tot een Azure-containerregister
> * De beheerde identiteit gebruiken om toegang te krijgen tot het register en een containerafbeelding op te halen

* Meer informatie over [beheerde identiteiten voor Azure-resources.](../active-directory/managed-identities-azure-resources/index.yml)


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-vm-show]: /cli/azure/vm#az_vm_show
[az-vm-identity-assign]: /cli/azure/vm/identity#az_vm_identity_assign
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-identity-show]: /cli/azure/identity#az_identity_show
[azure-cli]: /cli/azure/install-azure-cli
