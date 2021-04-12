---
title: Verifiëren met beheerde identiteit
description: Toegang bieden tot installatie kopieën in uw persoonlijke container register met behulp van een door de gebruiker toegewezen of door het systeem toegewezen beheerde Azure-identiteit.
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 2ab27e8548882b5bd296dc45e4bb74d3d6ba357b
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106285481"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Een door Azure beheerde identiteit gebruiken om te verifiëren bij een Azure container Registry 

Gebruik een [beheerde identiteit voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) om te verifiëren bij een Azure container Registry vanuit een andere Azure-resource, zonder dat u register referenties hoeft op te geven of te beheren. Stel bijvoorbeeld een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit op een Linux-VM in om toegang te krijgen tot container installatie kopieën vanuit het container register, net als bij een openbaar REGI ster. U kunt ook een Azure Kubernetes service-cluster instellen om de [beheerde identiteit](../aks/use-managed-identity.md) te gebruiken voor het ophalen van container installatie kopieën van Azure container Registry voor pod-implementaties.

Voor dit artikel vindt u meer informatie over beheerde identiteiten en over het volgende:

> [!div class="checklist"]
> * Een door de gebruiker toegewezen of door het systeem toegewezen identiteit inschakelen op een virtuele Azure-machine
> * De identiteit toegang verlenen tot een Azure container Registry
> * De beheerde identiteit gebruiken om toegang te krijgen tot het REGI ster en een container installatie kopie te halen 

Voor het maken van de Azure-resources moet u voor dit artikel de Azure CLI-versie 2.0.55 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Als u een container register wilt instellen en een container installatie kopie ernaar wilt pushen, moet u ook docker lokaal hebben geïnstalleerd. Docker biedt pakketten die eenvoudig Docker configureren op elk [macOS][docker-mac]-, [Windows][docker-windows]- of [Linux][docker-linux]-systeem.

## <a name="why-use-a-managed-identity"></a>Waarom een beheerde identiteit gebruiken?

Als u niet bekend bent met de functie voor beheerde identiteiten voor Azure-resources, raadpleegt u dit [overzicht](../active-directory/managed-identities-azure-resources/overview.md).

Nadat u geselecteerde Azure-resources met een beheerde identiteit hebt ingesteld, geeft u de identiteit van de gewenste toegang tot een andere bron, net als elke beveiligingsprincipal. Wijs bijvoorbeeld een beheerde identiteit toe met pull, push en pull of andere machtigingen voor een persoonlijk REGI ster in Azure. (Zie [Azure container Registry rollen en machtigingen](container-registry-roles.md)voor een volledige lijst met register rollen.) U kunt een identiteit toegang geven tot een of meer resources.

Gebruik vervolgens de identiteit voor verificatie bij elke [service die ondersteuning biedt voor Azure AD-verificatie](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), zonder dat er referenties in uw code zijn. Kies hoe u wilt verifiëren met behulp van de beheerde identiteit, afhankelijk van uw scenario. Als u de identiteit wilt gebruiken voor toegang tot een Azure container Registry vanaf een virtuele machine, kunt u zich verifiëren met Azure Resource Manager. 

> [!NOTE]
> Op dit moment kunnen services zoals Azure Web App for Containers of Azure Container Instances hun beheerde identiteit niet gebruiken om te verifiëren met Azure Container Registry bij het ophalen van een container installatie kopie voor het implementeren van de container resource zelf. De identiteit is alleen beschikbaar nadat de container actief is. Als u deze resources wilt implementeren met behulp van installatie kopieën van Azure Container Registry, wordt een andere verificatie methode als [Service-Principal](container-registry-auth-service-principal.md) aanbevolen.

## <a name="create-a-container-registry"></a>Een containerregister maken

Als u nog geen Azure container Registry hebt, maakt u een REGI ster en pusht u een voor beeld van een container installatie kopie. Zie [Quick Start: een persoonlijk container register maken met behulp van de Azure cli](container-registry-get-started-azure-cli.md)voor stappen.

In dit artikel wordt ervan uitgegaan dat u de `aci-helloworld:v1` container installatie kopie hebt opgeslagen in het REGI ster. In de voor beelden wordt de register naam *myContainerRegistry* gebruikt. Vervang door uw eigen register-en afbeeldings namen in latere stappen.

## <a name="create-a-docker-enabled-vm"></a>Een VM maken die is ingeschakeld voor docker

Maak een Ubuntu-virtuele machine met docker-functionaliteit. U moet ook de [Azure cli](/cli/azure/install-azure-cli) installeren op de virtuele machine. Als u al een virtuele machine van Azure hebt, kunt u deze stap overs Laan om de virtuele machine te maken.

Implementeer een standaard-Ubuntu Azure-virtuele machine met [AZ VM Create][az-vm-create]. In het volgende voor beeld wordt een VM gemaakt met de naam *myDockerVM* in een bestaande resource groep met de naam *myResourceGroup*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Het duurt enkele minuten voordat de virtuele machine wordt gemaakt. Wanneer de opdracht is voltooid, noteert u de `publicIpAddress` weer gegeven door de Azure cli. Gebruik dit adres om SSH-verbindingen met de virtuele machine te maken.

### <a name="install-docker-on-the-vm"></a>Docker installeren op de VM

Nadat de virtuele machine is uitgevoerd, maakt u een SSH-verbinding met de VM. Vervang *publicIpAddress* door het open bare IP-adres van uw virtuele machine.

```bash
ssh azureuser@publicIpAddress
```

Voer de volgende opdracht uit om docker te installeren op de VM:

```bash
sudo apt update
sudo apt install docker.io -y
```

Na de installatie voert u de volgende opdracht uit om te controleren of docker correct wordt uitgevoerd op de VM:

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

Volg de stappen in [Azure cli installeren met apt](/cli/azure/install-azure-cli-apt) om de Azure CLI op uw virtuele Ubuntu-machine te installeren. Voor dit artikel moet u versie 2.0.55 of hoger installeren.

Sluit de SSH-sessie af.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Voor beeld 1: toegang met een door de gebruiker toegewezen identiteit

### <a name="create-an-identity"></a>Een identiteit maken

Maak een identiteit in uw abonnement met behulp van de opdracht [AZ Identity Create](/cli/azure/identity#az_identity_create) . U kunt dezelfde resource groep gebruiken die u eerder hebt gebruikt voor het maken van het container register of de virtuele machine of een andere.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Als u de identiteit in de volgende stappen wilt configureren, gebruikt u de opdracht [AZ Identity Show] [az_identity_show] om de resource-ID van de identiteit en de Service-Principal-ID op te slaan in variabelen.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Omdat u de ID van de identiteit in een latere stap nodig hebt wanneer u zich aanmeldt bij de CLI vanaf uw virtuele machine, geeft u de waarde:

```bash
echo $userID
```

De ID heeft de volgende notatie:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>De virtuele machine configureren met de identiteit

Met de volgende opdracht [AZ VM Identity Assign][az-vm-identity-assign] configureert u de docker-VM met de door de gebruiker toegewezen identiteit:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Identiteits toegang verlenen tot het container register

Configureer nu de identiteit voor toegang tot uw container register. Gebruik eerst de opdracht [AZ ACR show][az-acr-show] om de bron-id van het REGI ster op te halen:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Gebruik de opdracht [AZ Role Assignment Create][az-role-assignment-create] om de rol AcrPull toe te wijzen aan het REGI ster. Deze rol biedt [pull-machtigingen](container-registry-roles.md) voor het REGI ster. Wijs de ACRPush-rol toe om zowel pull-als push machtigingen te bieden.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>De identiteit gebruiken om toegang te krijgen tot het REGI ster

SSH naar de virtuele docker-machine die is geconfigureerd met de identiteit. Voer de volgende Azure CLI-opdrachten uit met behulp van de Azure CLI die is geïnstalleerd op de VM.

Verifieer eerst bij de Azure CLI met [AZ login][az-login], met behulp van de identiteit die u hebt geconfigureerd op de virtuele machine. `<userID>`Vervang door de id van de identiteit die u in een vorige stap hebt opgehaald. 

```azurecli
az login --identity --username <userID>
```

Verifieer vervolgens bij het REGI ster met [AZ ACR login][az-acr-login]. Wanneer u deze opdracht gebruikt, gebruikt de CLI het Active Directory-token dat u hebt gemaakt `az login` om uw sessie naadloos te verifiëren met het container register. (Afhankelijk van de installatie van uw virtuele machine moet u deze opdracht en docker-opdrachten mogelijk uitvoeren met `sudo` .)

```azurecli
az acr login --name myContainerRegistry
```

Er wordt een `Login succeeded` bericht weer gegeven. U kunt vervolgens `docker` opdrachten uitvoeren zonder referenties op te geven. Voer bijvoorbeeld [docker pull][docker-pull] uit om de installatie kopie op te halen `aci-helloworld:v1` , waarbij u de naam van de aanmeldings server van het REGI ster opgeeft. De naam van de aanmeldings server bestaat uit de register naam van de container (alle kleine letters), gevolgd door `.azurecr.io` , bijvoorbeeld `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Voor beeld 2: toegang met een door het systeem toegewezen identiteit

### <a name="configure-the-vm-with-a-system-managed-identity"></a>De virtuele machine configureren met een door het systeem beheerde identiteit

Met de volgende opdracht [AZ VM Identity Assign][az-vm-identity-assign] configureert u de docker-VM met een door het systeem toegewezen identiteit:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Gebruik de opdracht [AZ VM show][az-vm-show] om een variabele in te stellen op de waarde van `principalId` (de Service-Principal-id) van de identiteit van de virtuele machine die u in latere stappen moet gebruiken.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Identiteits toegang verlenen tot het container register

Configureer nu de identiteit voor toegang tot uw container register. Gebruik eerst de opdracht [AZ ACR show][az-acr-show] om de bron-id van het REGI ster op te halen:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Gebruik de opdracht [AZ Role Assignment Create][az-role-assignment-create] om de rol AcrPull toe te wijzen aan de identiteit. Deze rol biedt [pull-machtigingen](container-registry-roles.md) voor het REGI ster. Wijs de ACRPush-rol toe om zowel pull-als push machtigingen te bieden.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>De identiteit gebruiken om toegang te krijgen tot het REGI ster

SSH naar de virtuele docker-machine die is geconfigureerd met de identiteit. Voer de volgende Azure CLI-opdrachten uit met behulp van de Azure CLI die is geïnstalleerd op de VM.

Verifieer eerst de Azure CLI met [AZ login][az-login], waarbij de door het systeem toegewezen identiteit op de virtuele machine wordt gebruikt.

```azurecli
az login --identity
```

Verifieer vervolgens bij het REGI ster met [AZ ACR login][az-acr-login]. Wanneer u deze opdracht gebruikt, gebruikt de CLI het Active Directory-token dat u hebt gemaakt `az login` om uw sessie naadloos te verifiëren met het container register. (Afhankelijk van de installatie van uw virtuele machine moet u deze opdracht en docker-opdrachten mogelijk uitvoeren met `sudo` .)

```azurecli
az acr login --name myContainerRegistry
```

Er wordt een `Login succeeded` bericht weer gegeven. U kunt vervolgens `docker` opdrachten uitvoeren zonder referenties op te geven. Voer bijvoorbeeld [docker pull][docker-pull] uit om de installatie kopie op te halen `aci-helloworld:v1` , waarbij u de naam van de aanmeldings server van het REGI ster opgeeft. De naam van de aanmeldings server bestaat uit de register naam van de container (alle kleine letters), gevolgd door `.azurecr.io` , bijvoorbeeld `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u beheerde identiteiten gebruikt met Azure Container Registry en hoe u:

> [!div class="checklist"]
> * Een door de gebruiker toegewezen of door het systeem toegewezen identiteit inschakelen in een Azure VM
> * De identiteit toegang verlenen tot een Azure container Registry
> * De beheerde identiteit gebruiken om toegang te krijgen tot het REGI ster en een container installatie kopie te halen

* Meer informatie over [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/index.yml).


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-show]: /cli/azure/vm#az-vm-show
[az-vm-identity-assign]: /cli/azure/vm/identity#az-vm-identity-assign
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
